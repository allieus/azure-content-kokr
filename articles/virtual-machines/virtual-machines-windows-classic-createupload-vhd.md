<properties
	pageTitle="Powershell을 사용하여 VM 이미지 만들기 및 업로드 | Microsoft Azure"
	description="클래식 배포 모델 및 Azure Powershell을 사용하여 일반화된 Windows Server 이미지(VHD)를 만들고 업로드하는 방법에 대해 알아봅니다."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/21/2016"
	ms.author="cynthn"/>

# Windows Server VHD를 만들어서 Azure에 업로드

이 문서에서는 일반화된 자체 VM 이미지를 VHD(가상 하드 디스크)로 업로드하고 이를 가상 컴퓨터를 만드는 데 사용하는 방법을 소개합니다. Microsoft Azure의 디스크 및 VHD에 대한 자세한 내용은 [가상 컴퓨터용 디스크 및 VHD에 대하여](virtual-machines-linux-about-disks-vhds.md)를 참조하세요.


[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]. 리소스 관리자 모델을 사용하여 가상 컴퓨터를 [캡처](virtual-machines-windows-capture-image.md)하고 [업로드](virtual-machines-windows-upload-image.md)할 수도 있습니다.

## 필수 조건

이 문서에서는 사용자가 다음 작업을 수행한 것으로 가정합니다.

- **Azure 구독** - 계정이 없는 경우 [무료로 Azure 계정을 개설할 수 있습니다](/pricing/free-trial/?WT.mc_id=A261C142F).

- **[Microsoft Azure PowerShell](../powershell-install-configure.md)** - Microsoft Azure PowerShell 모듈이 설치되고 구독을 사용하도록 구성되어 있어야 합니다.

- **.VHD 파일** - .vhd 파일에 저장되고 가상 컴퓨터에 연결된 지원되는 Windows 운영 체제 VHD에서 실행 중인 서버 역할이 sysprep에서 지원되는지도 확인해야 합니다. 자세한 내용은 [서버 역할에 대한 Sysprep 지원](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)을 참조하세요.

> [AZURE.IMPORTANT] VHDX 형식은 Microsoft Azure에서 지원되지 않습니다. Hyper-V 관리자 또는 [Convert-VHD cmdlet](http://technet.microsoft.com/library/hh848454.aspx)을 사용하여 디스크를 VHD 형식으로 변환할 수 있습니다. 자세한 내용은 이 [블로그 게시물](http://blogs.msdn.com/b/virtual_pc_guy/archive/2012/10/03/using-powershell-to-convert-a-vhd-to-a-vhdx.aspx)을 참조하세요.

## 1 단계: VHD 준비 

Azure에 VHD를 업로드하기 전에 Sysprep 도구를 사용하여 일반화해야 합니다. 그러면 VHD가 이미지로 사용하도록 준비됩니다. Sysprep에 대한 자세한 내용은 [Sysprep 사용 방법: 소개](http://technet.microsoft.com/library/bb457073.aspx)를 참조하세요.

운영 체제를 설치한 가상 컴퓨터에서 다음 절차를 완료합니다.

1. 운영 체제에 로그인합니다.

2. 관리자로 명령 프롬프트 창을 엽니다. 디렉터리를 **%windir%\\system32\\sysprep**로 변경한 후 `sysprep.exe`를 실행합니다.

	![명령 프롬프트 창 열기](./media/virtual-machines-windows-classic-createupload-vhd/sysprep_commandprompt.png)

3.	**시스템 준비 도구** 대화 상자가 나타납니다.

	![Sysprep 시작](./media/virtual-machines-windows-classic-createupload-vhd/sysprepgeneral.png)

4.  **시스템 준비 도구**에서 **시스템 OOBE(첫 실행 경험) 입력**을 선택하고 **일반화**를 선택했는지 확인합니다.

5.  **종료 옵션**에서 **종료**를 선택합니다.

6.  **확인**을 클릭합니다.

## 2단계: 저장소 계정 및 컨테이너 만들기

.vhd 파일을 업로드할 장소가 있도록 Azure에 저장소 계정이 필요합니다. 이 단계는 계정을 만들거나 기존 계정에서 필요한 정보를 가져오는 방법을 보여줍니다. &lsaquo; brackets &rsaquo;의 변수를 자체 정보로 바꿉니다.

1. 로그인

		Add-AzureAccount

1. Azure 구독을 설정합니다.

    	Select-AzureSubscription -SubscriptionName <SubscriptionName> 

2. 새 저장소 계정을 만듭니다. 저장소 계정의 이름은 고유한 3-24자여야 합니다. 이름은 문자와 숫자의 조합으로 구성될 수 있습니다. "미국 동부"와 같은 위치도 지정해야 합니다.
    	
		New-AzureStorageAccount –StorageAccountName <StorageAccountName> -Location <Location>

3. 새 저장소 계정을 기본값으로 설정합니다.
    	
		Set-AzureSubscription -CurrentStorageAccountName <StorageAccountName> -SubscriptionName <SubscriptionName>

4. 새 컨테이너를 만듭니다.

    	New-AzureStorageContainer -Name <ContainerName> -Permission Off

 

## 3단계: .vhd 파일 업로드

[Add-AzureVhd](http://msdn.microsoft.com/library/dn495173.aspx)를 사용하여 VHD를 업로드합니다.

이전 단계에서 사용한 Azure PowerShell 창에서 다음 명령을 입력하고 &lsaquo; brackets &rsaquo;의 변수를 자체 정보로 바꿉니다.

		Add-AzureVhd -Destination "https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/<vhdName>.vhd" -LocalFilePath <LocalPathtoVHDFile>


## 4단계: 사용자 지정 이미지 목록에 이미지 추가

[Add-AzureVMImage])(https://msdn.microsoft.com/library/mt589167.aspx) cmdlet을 사용하여 사용자 지정 이미지 목록에 이미지를 추가합니다.

		Add-AzureVMImage -ImageName <ImageName> -MediaLocation "https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/<vhdName>.vhd" -OS "Windows"


## 다음 단계

이제 업로드한 이미지를 사용하여 [사용자 지정 VM을 만들 수 있습니다](virtual-machines-windows-classic-createportal.md).

<!---HONumber=AcomDC_0817_2016-->