<properties 
	pageTitle="Azure 데이터 팩터리 문제 해결" 
	description="Azure 데이터 팩터리 사용과 관련된 문제를 해결하는 방법에 대해 알아봅니다." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/15/2016" 
	ms.author="spelluru"/>

# 데이터 팩터리 문제 해결
이 문서에서는 Azure Data Factory 사용 시 발생하는 문제에 대한 문제 해결 팁을 제공합니다. 서비스 사용 중 발생할 수 있는 모든 문제를 다루지는 않지만 몇 가지 문제와 일반적인 문제 해결 팁을 설명합니다.

## 문제 해결 팁

### 오류: 구독이 'Microsoft.DataFactory' 네임스페이스를 사용하도록 등록되어 있지 않습니다.
이 오류가 발생하는 경우 Azure Data Factory 리소스 공급자가 컴퓨터에 등록되지 않은 것입니다. 다음 작업을 수행하세요.

1. Azure PowerShell을 시작합니다.
2. 다음 명령을 사용하여 Azure 계정에 로그인합니다. Login-AzureRmAccount
3. 다음 명령을 실행하여 Azure Data Factory 공급자를 등록합니다. Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory

### 문제: 데이터 팩터리 cmdlet을 실행할 때 권한 없음 오류 발생
Azure PowerShell에서 올바른 Azure 계정 또는 구독을 사용하고 있지 않습니다. 다음 cmdlet을 사용하여 Azure PowerShell에서 사용할 올바른 Azure 계정 및 구독을 선택합니다.

1. Login-AzureRmAccount - 올바른 사용자 ID 및 암호를 사용합니다.
2. Get-AzureRmSubscription - 계정의 모든 구독을 확인합니다.
3. Select-AzureRmSubscription <구독 이름> - 올바른 구독을 선택합니다. Azure 포털에서 데이터 팩터리를 만드는 데 사용한 것과 동일한 구독을 사용합니다.

### 문제: Azure 포털에서 데이터 관리 게이트웨이 빠른 설치를 시작하지 못함
데이터 관리 게이트웨이 빠른 설치를 수행하려면 Internet Explorer 또는 Microsoft ClickOnce 호환 웹 브라우저가 필요합니다. 빠른 설치를 시작할 수 없는 경우 다음 중 하나를 수행합니다.

- Internet Explorer 또는 Microsoft ClickOnce 호환 웹 브라우저를 사용하세요.

	Chrome을 사용하는 경우 [Chrome 웹 스토어](https://chrome.google.com/webstore/)로 이동하여 "ClickOnce" 키워드로 검색하고 ClickOnce 확장 중 하나를 선택해 설치합니다.
	
	Firefox의 경우 동일한 작업을 수행해야 합니다(추가 기능 설치). 도구 모음(상단 오른쪽 모서리의 가로 줄 세 개)의 열기 메뉴 단추를 클릭하고 추가 기능을 클릭하고 "ClickOnce" 키워드로 검색하고 ClickOnce 확장 중 하나를 선택하고 설치합니다.

- 포털의 동일한 블레이드에 표시된 **수동 설치** 링크를 사용하여 설치 파일을 다운로드하고 수동으로 실행합니다. 설치에 성공하면 데이터 관리 게이트웨이 구성 대화 상자가 표시됩니다. 포털 화면에서 **키**를 복사하고 구성 관리자에서 이 키를 사용하여 게이트웨이를 서비스에 등록합니다.

### 문제: 온-프레미스 SQL Server에 연결하지 못함 
게이트웨이 컴퓨터에서 **데이터 관리 게이트웨이 구성 관리자**를 시작하고 **문제 해결** 탭을 사용하여 게이트웨이 컴퓨터에서 SQL Server에 대한 연결을 테스트합니다. 연결/게이트웨이 관련 문제 해결에 대한 팁은 [게이트웨이 문제 해결](data-factory-data-management-gateway.md#troubleshoot-gateway-issues)을 참조하세요.
 

### 문제: 입력 조각이 무기한 대기 상태임

몇 가지 이유로 인해 조각이 **Waiting** 상태일 수 있으며 일반적인 이유 중 하나는 **external** 속성이 **true**로 설정되지 않은 것입니다. Azure 데이터 팩터리의 외부에서 생성된 데이터 집합은 **external** 속성으로 표시되어야 합니다. 이 속성은 데이터가 외부이며 데이터 팩터리 내의 파이프라인에서 지원되지 않음을 나타냅니다. 해당 저장소에서 데이터를 사용할 수 있으면 데이터 조각이 **Ready**로 표시됩니다.

**external** 속성의 사용 방법은 다음 예제를 참조하세요. external을 true로 설정할 경우 선택적으로 **externalData***를 지정할 수 있습니다.

이 속성에 대한 자세한 내용은 [데이터 집합](data-factory-create-datasets.md) 문서를 참조하세요.
	
	{
	  "name": "CustomerTable",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "MyLinkedService",
	    "typeProperties": {
	      "folderPath": "MyContainer/MySubFolder/",
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": ",",
	        "rowDelimiter": ";"
	      }
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    },
	    "policy": {
	      }
	    }
	  }
	}

오류를 해결하려면 입력 테이블의 JSON 정의에 **external** 속성과 **externalData** 섹션을 추가하고 테이블을 다시 만듭니다.

### 문제: 하이브리드 복사 작업 실패
데이터 관리 게이트웨이를 사용하여 온-프레미스 데이터 저장소 간 복사 작업에서 발생하는 문제를 해결하는 단계는 [게이트웨이 문제 해결](data-factory-data-management-gateway.md#troubleshoot-gateway-issues)을 참조하세요.

### 문제: 주문형 HDInsight 프로비전 실패
HDInsightOnDemand 형식의 연결된 서비스를 사용하는 경우 Azure Blob 저장소를 가리키는 linkedServiceName을 지정해야 합니다. Data Factory 서비스는 이 저장소를 사용하여 주문형 HDInsight 클러스터에 대한 모든 로그 및 지원 파일을 저장합니다. 경우에 따라 다음 오류와 함께 주문형 HDInsight 클러스터 프로비전이 실패할 수 있습니다.

		Failed to create cluster. Exception: Unable to complete the cluster create operation. Operation failed with code '400'. Cluster left behind state: 'Error'. Message: 'StorageAccountNotColocated'.

이 오류는 일반적으로 linkedServiceName에 지정된 저장소 계정의 위치가 HDInsight 프로비전이 발생하는 위치와 동일한 데이터 센터 위치에 있지 않음을 나타냅니다. 예를 들어 Azure 데이터 팩터리 위치가 미국 서부이고 주문형 HDInsight 프로비저닝이 미국 서부에서 발생하지만 Azure Blob 저장소 계정 위치가 미국 동부로 설정된 경우 주문형 프로비저닝이 실패합니다.

또한 주문형 HDInsight에서 추가 저장소 계정을 지정할 수 있는 두 번째 JSON 속성 additionalLinkedServiceNames가 있습니다. 이러한 추가 연결된 저장소 계정은 HDInsight 클러스터와 동일한 위치에 있어야 합니다. 그렇지 않으면 동일한 오류로 인해 실패합니다.

### 문제: 사용자 지정 .NET 작업 실패
자세한 단계는 [사용자 지정 작업을 사용하여 파이프라인 디버그](data-factory-use-custom-activities.md#debug-the-pipeline)를 참조하세요.

## Azure 포털을 사용하여 문제 해결 

### 포털 블레이드 사용
단계는 [파이프라인 모니터링](data-factory-build-your-first-pipeline-using-editor.md#monitor-pipeline)을 참조하세요.

### 모니터링 및 관리 앱 사용
자세한 내용은 [모니터링 및 관리 앱을 사용하여 데이터 팩터리 파이프라인 모니터링 및 관리](data-factory-monitor-manage-app.md)를 참조하세요.

## Azure PowerShell을 사용하여 문제 해결

### Azure PowerShell을 사용하여 오류 해결  
자세한 내용은 [Azure PowerShell을 사용하여 Data Factory 파이프라인 모니터링](data-factory-build-your-first-pipeline-using-powershell.md#monitor-pipeline)을 참조하세요.


[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456
[json-scripting-reference]: http://go.microsoft.com/fwlink/?LinkId=516971

[azure-portal]: https://portal.azure.com/

[image-data-factory-troubleshoot-with-error-link]: ./media/data-factory-troubleshoot/DataFactoryWithErrorLink.png

[image-data-factory-troubleshoot-datasets-with-errors-blade]: ./media/data-factory-troubleshoot/DatasetsWithErrorsBlade.png

[image-data-factory-troubleshoot-table-blade-with-problem-slices]: ./media/data-factory-troubleshoot/TableBladeWithProblemSlices.png

[image-data-factory-troubleshoot-activity-run-with-error]: ./media/data-factory-troubleshoot/ActivityRunDetailsWithError.png

[image-data-factory-troubleshoot-dataslice-blade-with-active-runs]: ./media/data-factory-troubleshoot/DataSliceBladeWithActivityRuns.png

[image-data-factory-troubleshoot-walkthrough2-with-errors-link]: ./media/data-factory-troubleshoot/Walkthrough2WithErrorsLink.png

[image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]: ./media/data-factory-troubleshoot/Walkthrough2DataSetsWithErrors.png

[image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]: ./media/data-factory-troubleshoot/Walkthrough2TableProblemSlices.png

[image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]: ./media/data-factory-troubleshoot/Walkthrough2DataSliceActivityRuns.png

[image-data-factory-troubleshoot-activity-run-details]: ./media/data-factory-troubleshoot/Walkthrough2ActivityRunDetails.png
 

<!---HONumber=AcomDC_0803_2016-->