<properties
	pageTitle="배치에서 작업 준비 및 정리 | Microsoft Azure"
	description="작업 수준 준비 태스크를 사용하여 Azure 배치 계산 노드에 데이터 전송을 최소화하고 작업 완료 시 노드 정리에 대한 태스크를 릴리스합니다."
	services="batch"
	documentationCenter=".net"
	authors="mmacy"
	manager="timlt"
	editor="" />

<tags
	ms.service="batch"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="big-compute"
	ms.date="06/22/2016"
	ms.author="marsma" />

# Azure 배치 계산 노드에서 작업 준비와 완료 작업 실행

Azure 배치 작업은 실행에 앞서 일정한 형태의 설치는 물론 작업 태스크가 완료된 후에는 일종의 작업 후 유지 관리를 필요로 하는 경우가 있습니다. 배치는 선택적인 작업 준비 및 작업 릴리스 태스크의 형태로 이 준비 및 유지 관리에 대한 메커니즘을 제공합니다.

**작업 준비 태스크**는 작업 태스크가 실행되기 전에 태스크를 실행하도록 예약된 모든 계산 노드에서 실행됩니다. 작업이 완료되면 하나 이상의 태스크를 실행한 풀의 각 노드에서 **작업 릴리스 태스크**가 실행됩니다. 일반적인 배치 태스크의 경우, 해당 태스크를 실행할 때 호출될 작업 준비 또는 릴리스 태스크의 명령줄을 지정할 수 있습니다. 이러한 특수 태스크는 파일 다운로드, 관리자 권한 실행, 사용자 지정 환경 변수, 최대 실행 기간, 재시도 횟수 및 파일 보존 시간 등의 기타 익숙한 태스크 기능을 제공합니다.

다음 섹션에서 [배치 .NET][api_net] API에서 [JobPreparationTask][net_job_prep] 클래스 및 [JobReleaseTask][net_job_release] 클래스를 사용하여 이러한 두 가지 특별한 태스크 형식을 사용하는 방법을 알아봅니다.

> [AZURE.TIP] 작업 준비 및 릴리스 태스크는 계산 노드의 풀이 작업 실행 사이에 지속되고 다른 여러 작업 간에 공유되는 "공유 풀" 환경에서 특히 유용합니다.

## 작업 준비 및 해제 태스크를 사용하는 시점

언제든지 작업 관련 구성 또는 데이터를 사용하여 노드를 준비(하고 태스크 결과 데이터를 정리하거나 유지)해야 하는 경우 작업 준비 및 릴리스 태스크를 사용하는 것이 좋습니다. 이러한 상황의 예는 다음과 같습니다.

**공통 태스크 데이터의 전송**

배치 작업은 종종 작업의 태스크에 대한 입력으로 데이터의 공통 집합이 필요합니다. 예를 들어 매일 위험 분석 계산, 시장 데이터는 작업 특정적이지만 작업의 모든 태스크에 공통적입니다. 기가바이트 크기인 이 시장 데이터는 각 계산 노드로 한 번만 다운로드해야 하므로 노드에서 실행되는 모든 태스크가 사용할 수 있습니다. **작업 준비 태스크**를 사용하여 작업의 다른 태스크를 실행하기 전에 각 노드에 데이터를 다운로드합니다.

**작업 데이터 삭제**

작업 사이에 풀의 계산 노드의 서비스가 해지되지 않은 공유 풀 환경 내에서 실행 간의 작업 데이터를 삭제하여 노드에서 디스크 공간을 절약하거나 조직의 보안 정책을 충족해야 합니다. **작업 릴리스 태스크**를 사용하여 작업 준비 태스크에서 다운로드되거나 태스크를 실행하는 동안 생성된 데이터를 삭제합니다.

**로그 보존**

태스크가 생성한 로그 파일의 복사본을 유지하거나 실패한 응용 프로그램에서 생성될 수 있는 덤프 파일의 작동을 중단할 수 있습니다. 이런 경우 **작업 릴리스 태스크**를 사용하여 [Azure 저장소][azure_storage] 계정에 이 데이터를 압축하고 업로드합니다.

## 작업 준비 태스크

작업의 태스크를 실행하기 전에 준비 작업 태스크는 작업을 실행하도록 예약된 각 계산 노드에서 실행됩니다. 기본적으로 배치 서비스는 노드에서 실행되도록 예약된 태스크를 실행하기 전에 작업 준비 태스크가 완료되기를 대기합니다. 그러나 서비스를 대기하지 않도록 구성할 수 있습니다. 작업 준비 태스크는 노드가 다시 시작되면 계산 노드에서 다시 실행되지만 이 동작을 사용하지 않도록 설정할 수 있습니다.

작업 준비 태스크는 태스크를 실행하도록 예약된 노드에서 실행됩니다. 노드에 태스크를 할당하지 않은 경우 준비 태스크가 불필요하게 실행되지 않도록 방지합니다. 이는 작업에 대한 태스크 수가 풀의 노드 수보다 작은 경우에 발생할 수 있습니다. 또한 [동시 태스크 실행](batch-parallel-node-tasks.md)을 활성화할 때 적용되며 이는 태스크 개수가 가능한 총 동시 태스크 보다 작을 때 일부 노드를 유휴 상태로 남겨둡니다. 유휴 노드에서 작업 준비 태스크를 실행하지 않으면 데이터 전송 요금에 적은 비용을 투자할 수 있습니다.

> [AZURE.NOTE] [JobPreparationTask]계산 노드가 먼저 풀에 조인하거나 다시 시작할 때 StartTask가 실행하는 반면 [net\_job\_prep\_cloudjob]은 JobPreparationTask가 각 작업의 시작 부분에서 실행된다는 점에서 [CloudPool.StartTask][pool_starttask]가 다릅니다.

## 작업 릴리스 태스크

작업이 완료로 표시되면 하나 이상의 태스크를 실행한 풀의 각 노드에서 작업 릴리스 태스크가 실행됩니다. 종료 요청을 발행하여 작업을 완료로 표시합니다. 그런 다음 배치 서비스는 작업 상태를 *종료*로 설정하며 작업과 연관된 모든 활성 또는 실행 중인 태스크를 종료하고 작업 릴리스 태스크를 실행합니다. 작업은 *완료* 상태로 이동합니다.

> [AZURE.NOTE] 또한 작업 삭제는 작업 릴리스 태스크를 실행합니다. 그러나 작업이 이미 종료된 경우 이후에 작업이 삭제될 때 릴리스 태스크는 두 번째로 실행되지 않습니다.

## 배치 .NET을 사용한 작업 준비 및 릴리스 태스크

작업 준비 태스크를 사용하려면 [JobPreparationTask][net_job_prep]를 만들고 구성하며 작업의 [CloudJob.JobPreparationTask][net_job_prep_cloudjob] 속성에 할당합니다. 마찬가지로 [JobReleaseTask][net_job_release]를 초기화하고 작업의 [CloudJob.JobReleaseTask][net_job_prep_cloudjob] 속성을 할당하여 작업의 릴리스 태스크를 설정합니다.

이 코드 조각에서 `myBatchClient`는 완전히 [BatchClient][net_batch_client]의 초기화된 인스턴스이고 `myPool`은 배치 계정 내에 있는 기존 풀입니다.

		// Create the CloudJob for CloudPool "myPool"
		CloudJob myJob = myBatchClient.JobOperations.CreateJob("JobPrepReleaseSampleJob",
															   new PoolInformation() { PoolId = "myPool" });

		// Specify the command lines for the job preparation and release tasks
		string jobPrepCmdLine = "cmd /c echo %AZ_BATCH_NODE_ID% > %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";
		string jobReleaseCmdLine = "cmd /c del %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";

		// Assign the job preparation task to the job
		myJob.JobPreparationTask = new JobPreparationTask { CommandLine = jobPrepCmdLine };

		// Assign the job release task to the job
		myJob.JobReleaseTask = new JobPreparationTask { CommandLine = jobReleaseCmdLine };

		await myJob.CommitAsync();

위에서 설명했듯이 릴리스 태스크는 작업이 종료되거나 삭제될 때 실행됩니다. 배치 .NET API로 작업을 종료하려면 [JobOperations.TerminateJobAsync][net_job_terminate]를 호출하여 수행됩니다. 작업 삭제는 [JobOperations.DeleteJobAsync][net_job_delete]를 사용하여 수행됩니다. 이러한 작업은 모두 작업의 태스크를 완료할 때 또는 사용자가 정의한 시간 제한에 도달한 경우 일반적으로 수행됩니다.

		// Terminate the job to mark it as Completed; this will initiate the Job Release Task on any node
		// that executed job tasks. Note that the Job Release Task is also executed when a job is deleted,
		// thus you need not call Terminate if you typically delete your jobs upon task completion.
		await myBatchClient.JobOperations.TerminateJobAsync("JobPrepReleaseSampleJob");

## GitHub의 코드 샘플

GitHub에서 [JobPrepRelease][job_prep_release_sample] 샘플 프로젝트를 확인하여 작동 중인 작업 준비 및 릴리스 태스크를 봅니다. 이 콘솔 응용 프로그램은 다음을 수행합니다.

1. "small" 노드가 두 개인 풀을 만듭니다.
2. 작업 준비, 릴리스 및 표준 태스크를 사용하여 작업을 만듭니다.
3. 먼저 노드 "공유" 디렉터리의 텍스트 파일에 노드 ID를 기록하는 작업 준비 태스크를 실행합니다.
4. 동일한 텍스트 파일에 해당 태스크 ID를 기록하는 각 노드에서 태스크를 실행합니다.
5. 모든 태스크가 완료되면(또는 시간 제한에 도달하면) 콘솔에 각 노드의 텍스트 파일에 있는 내용을 인쇄합니다.
6. 작업이 완료되면 작업 릴리스 태스크를 실행하여 노드에서 파일을 삭제합니다.
6. 실행된 각 노드에 대한 작업 준비 및 릴리스 태스크의 종료 코드를 인쇄합니다.
7. 실행을 일시 중지하여 작업 및/또는 풀 삭제를 확인합니다.

샘플 응용 프로그램에서 출력은 다음과 비슷합니다.

```
Attempting to create pool: JobPrepReleaseSamplePool
Created pool JobPrepReleaseSamplePool with 2 small nodes
Checking for existing job JobPrepReleaseSampleJob...
Job JobPrepReleaseSampleJob not found, creating...
Submitting tasks and awaiting completion...
All tasks completed.

Contents of shared\job_prep_and_release.txt on tvm-2434664350_1-20160623t173951z:
-------------------------------------------
tvm-2434664350_1-20160623t173951z tasks:
  task001
  task004
  task005
  task006

Contents of shared\job_prep_and_release.txt on tvm-2434664350_2-20160623t173951z:
-------------------------------------------
tvm-2434664350_2-20160623t173951z tasks:
  task008
  task002
  task003
  task007

Waiting for job JobPrepReleaseSampleJob to reach state Completed
...

tvm-2434664350_1-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

tvm-2434664350_2-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

Delete job? [yes] no
yes
Delete pool? [yes] no
yes

Sample complete, hit ENTER to exit...
```

>[AZURE.NOTE] 새 풀에 있는 노드의 변수 생성 및 시작 시간으로 인해(일부 노드는 다른 노드 전에 태스크에 대해 준비됨) 다른 출력이 표시될 수 있습니다. 특히, 태스크가 신속하게 완료되기 때문에 풀의 노드 중 하나에서 작업의 태스크를 모두 실행할 수 있습니다. 이러한 경우 작업을 실행하지 않은 노드에는 작업 준비 및 릴리스 태스크가 존재하지 않습니다.

### Azure 포털에서 작업 준비 및 릴리스 태스크 검사

위의 샘플 응용 프로그램을 실행하는 경우 [Azure 포털][portal]을 사용하여 작업 및 해당 태스크의 속성을 확인하거나 작업의 태스크에서 수정된 공유 텍스트 파일을 다운로드할 수 있습니다.

아래 스크린샷에서는 샘플 응용 프로그램의 실행 후에 Azure 포털의 **준비 태스크 블레이드**를 보여 줍니다. 작업이 완료된 후에(하지만 작업 및 풀을 삭제하기 전에) *JobPrepReleaseSampleJob* 속성으로 이동하고 **준비 태스크** 또는 **릴리스 태스크**를 클릭하여 속성을 확인합니다.

![Azure 포털에서 작업 준비 속성][1]

## 다음 단계

### 응용 프로그램 패키지

작업 준비 태스크 외에도 배치의 [응용 프로그램 패키지](batch-application-packages.md) 기능을 사용하여 태스크 실행을 위한 계산 노드를 준비할 수 있습니다. 이 기능은 설치 관리자를 실행하지 않아도 되는 응용 프로그램, 100개 이상의 파일을 포함하는 응용 프로그램 또는 엄격한 버전 제어를 필요로 하는 응용 프로그램을 배포하는 데 특히 유용합니다.

### 응용 프로그램 설치 및 데이터 준비

태스크를 실행하기 위해 노드를 준비하는 다양한 방법의 개요는 Azure 배치 포럼에서 [배치 계산 노드에서 응용 프로그램 설치 및 데이터 준비][forum_post] 게시물을 확인합니다. Azure 배치 팀 멤버 중 하나가 작성한 이 게시물은 계산 노드에 (응용 프로그램 및 태스크 입력 데이터를 모두 포함한) 파일을 가져오는 다른 방법에 대한 좋은 기초이며 각 메서드에 고려해야 할 특별한 고려 사항입니다.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[azure_storage]: https://azure.microsoft.com/services/storage/
[portal]: https://portal.azure.com
[job_prep_release_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/JobPrepRelease
[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[net_batch_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_job_prep]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.aspx
[net_job_prep_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
[net\_job\_prep\_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
[net_job_delete]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.deletejobasync.aspx
[net_job_terminate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.terminatejobasync.aspx
[net_job_release]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobreleasetask.aspx
[net_job_release_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobreleasetask.aspx
[pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx

[net_list_certs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.listcertificates.aspx
[net_list_compute_nodes]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listcomputenodes.aspx
[net_list_job_schedules]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobschedules.aspx
[net_list_jobprep_status]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobpreparationandreleasetaskstatus.aspx
[net_list_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[net_list_nodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listnodefiles.aspx
[net_list_pools]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listpools.aspx
[net_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobs.aspx
[net_list_task_files]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[net_list_tasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listtasks.aspx

[1]: ./media/batch-job-prep-release/portal-jobprep-01.png

<!---HONumber=AcomDC_0629_2016-->