[.NET용 Microsoft Azure 구성 관리자 라이브러리](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)는 구성 파일에서 연결 문자열을 구문 분석하기 위해 클래스를 제공합니다. [CloudConfigurationManager 클래스](https://msdn.microsoft.com/library/azure/mt634650.aspx)는 클라이언트 응용 프로그램이 데스크톱, 모바일 장치, Azure 가상 컴퓨터 또는 Azure 클라우드 서비스 중 어디서 실행되는지에 관계없이 구성 설정을 구문 분석합니다.

CloudConfigurationManager 패키지를 참조하려면 다음 `using` 문을 클래스에 추가합니다.

	using Microsoft.Azure;	//Namespace for CloudConfigurationManager

구성 파일에서 연결 문자열 검색을 사용하는 방법을 보여 주는 예제는 다음과 같습니다.

    // Parse the connection string and return a reference to the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		CloudConfigurationManager.GetSetting("StorageConnectionString"));

Azure 구성 관리자 사용은 선택 사항입니다. 또한 .NET Framework의 [ConfigurationManager 클래스](https://msdn.microsoft.com/library/system.configuration.configurationmanager.aspx)와 같은 API를 사용할 수 있습니다.

<!---HONumber=AcomDC_0601_2016-->