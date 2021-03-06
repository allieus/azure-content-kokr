<properties
	pageTitle="Ruby에서 Azure 테이블 저장소를 사용하는 방법 | Microsoft Azure"
	description="Azure 테이블 저장소, NoSQL 데이터 저장소를 사용하여 클라우드에 구조화된 데이터를 저장합니다."
	services="storage"
	documentationCenter="ruby"
	authors="rmcmurray"
	manager="wpickett"
	editor=""/>
<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="ruby"
	ms.topic="article"
	ms.date="08/11/2016"
	ms.author="robmcm"/>


# Ruby에서 Azure 테이블 저장소를 사용하는 방법

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)] <br/> [AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-tables.md)]

## 개요

이 가이드에서는 Azure 테이블 서비스를 사용하여 일반 시나리오를 수행하는 방법을 설명합니다. 샘플은 Ruby API를 사용하여 작성되었습니다. **테이블 만들기 및 삭제, 테이블에서 엔터티 삽입 및 쿼리** 등의 시나리오를 다룹니다.

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Ruby 응용 프로그램 만들기

Ruby 응용 프로그램을 만드는 방법에 대한 지침은 [Azure VM의 Ruby on Rails 웹 응용 프로그램](../virtual-machines/virtual-machines-linux-classic-ruby-rails-web-app.md)을 참조하세요.


## 저장소에 액세스하도록 응용 프로그램 구성

Azure 저장소를 사용하려면 저장소 REST 서비스와 통신하는 편리한 라이브러리 집합이 포함된 Ruby Azure 패키지를 다운로드하여 사용해야 합니다.

### RubyGems를 사용하여 패키지 가져오기

1. **PowerShell**(Windows), **Terminal**(Mac) 또는 **Bash**(Unix)와 같은 명령줄 인터페이스를 사용합니다.

2. 명령 창에 **gem install azure**를 입력하여 gem 및 종속성을 설치합니다.

### 패키지 가져오기

주로 사용하는 텍스트 편집기에서 저장소를 사용할 Ruby 파일의 맨 위에 다음을 추가합니다.

	require "azure"

## Azure 저장소 연결 설정

Azure 모듈은 **AZURE\_STORAGE\_ACCOUNT** 및 **AZURE\_STORAGE\_ACCESS\_KEY** 환경 변수를 읽고 Azure 저장소 계정에 연결하는 데 필요한 정보를 확인합니다. 이러한 환경 변수가 설정되지 않으면 **Azure::TableService**를 사용하기 전에 다음 코드로 계정 정보를 지정해야 합니다.

	Azure.config.storage_account_name = "<your azure storage account>"
	Azure.config.storage_access_key = "<your azure storage access key>"

Azure 포털의 클래식 또는 Resource Manager 저장소 계정에서 이러한 값을 가져오려면

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. 사용하려는 저장소 계정으로 이동합니다.
3. 오른쪽의 설정 블레이드에서 **액세스 키**를 클릭합니다.
4. 나타나는 액세스 키 블레이드에 액세스 키 1 및 액세스 키 2가 표시되어 있습니다. 이 둘 중 하나를 사용할 수 있습니다.
5. 복사 아이콘을 클릭하여 키를 클립보드에 복사합니다.

클래식 Azure 포털의 클래식 저장소 계정에서 이러한 값을 가져오려면

1. [클래식 Azure 포털](https://manage.windowsazure.com)에 로그인합니다.
2. 사용하려는 저장소 계정으로 이동합니다.
3. 탐색 창 아래쪽에서 **액세스 키 관리**를 클릭합니다.
4. 팝업 대화 상자에 저장소 계정 이름, 기본 액세스 키 및 보조 액세스 키가 표시됩니다. 액세스 키의 경우 기본 액세스 키 또는 보조 액세스 키를 사용할 수 있습니다.
5. 복사 아이콘을 클릭하여 키를 클립보드에 복사합니다.

## 테이블 만들기

**Azure::TableService** 개체를 통해 테이블 및 엔터티에 대한 작업을 할 수 있습니다. 테이블을 만들려면 **create\_table()** 메서드를 사용합니다. 다음 예제는 테이블을 만들거나 테이블이 있으면 오류를 출력합니다.

	azure_table_service = Azure::TableService.new
	begin
	  azure_table_service.create_table("testtable")
	rescue
	  puts $!
	end

## 테이블에 엔터티 추가

엔터티를 추가하려면 먼저 엔터티 속성을 정의하는 해시 개체를 만듭니다. 모든 엔터티에 대해 **PartitionKey** 및 **RowKey**를 지정해야 합니다. 이 두 키는 엔터티의 고유한 식별자이며, 다른 속성보다 훨씬 더 빠르게 쿼리할 수 있는 값입니다. Azure 저장소는 **PartitionKey**를 사용하여 여러 저장소 노드를 통해 테이블의 엔터티를 자동으로 분산합니다. **PartitionKey**가 동일한 엔터티는 동일한 노드에 저장됩니다. **RowKey**는 엔터티가 속하는 파티션 내에서 엔터티의 고유한 ID입니다.

	entity = { "content" => "test entity",
	  :PartitionKey => "test-partition-key", :RowKey => "1" }
	azure_table_service.insert_entity("testtable", entity)

## 엔터티 업데이트

다음과 같은 여러 메서드를 사용하여 기존 엔터티를 업데이트할 수 있습니다.

* **update\_entity():** 기존 엔터티를 바꿔서 업데이트합니다.
* **merge\_entity():** 새 속성 값을 기존 엔터티에 병합하여 기존 엔터티를 업데이트합니다.
* **insert\_or\_merge\_entity():** 기존 엔터티를 바꿔서 업데이트합니다. 엔터티가 없는 경우 새 엔터티를 삽입합니다.
* **insert\_or\_replace\_entity():** 새 속성 값을 기존 엔터티에 병합하여 기존 엔터티를 업데이트합니다. 엔터티가 없는 경우 새 엔터티를 삽입합니다.

다음 예제에서는 **update\_entity()**를 사용하여 엔터티를 업데이트하는 방법을 보여 줍니다.

	entity = { "content" => "test entity with updated content",
	  :PartitionKey => "test-partition-key", :RowKey => "1" }
	azure_table_service.update_entity("testtable", entity)

**update\_entity()** 및 **merge\_entity()**를 사용할 때 업데이트 중인 엔터티가 없는 경우 업데이트 작업이 실패합니다. 따라서 엔터티의 존재 여부와 상관없이 엔터티를 저장하려면 **insert\_or\_replace\_entity()** 또는 **insert\_or\_merge\_entity()**를 대신 사용해야 합니다.

## 엔터티 그룹 작업

서버에서 원자성 처리를 수행하도록 여러 작업을 일괄적으로 제출하는 것이 좋은 경우도 있습니다. 이렇게 하려면 먼저 **Batch** 개체를 만든 다음 **TableService**에서 **execute\_batch()** 메서드를 사용합니다. 다음 예제에서는 RowKey 2와 3을 가진 두 엔터티를 일괄 제출하는 방법을 보여 줍니다. 동일한 PartitionKey를 가진 엔터티에 대해서만 작동합니다.

	azure_table_service = Azure::TableService.new
	batch = Azure::Storage::Table::Batch.new("testtable",
	  "test-partition-key") do
	  insert "2", { "content" => "new content 2" }
	  insert "3", { "content" => "new content 3" }
	end
	results = azure_table_service.execute_batch(batch)

## 엔터티 쿼리

테이블에서 엔터티를 쿼리하려면 테이블 이름인 **PartitionKey** 및 **RowKey**를 전달하여 **get\_entity()** 메서드를 사용합니다.

	result = azure_table_service.get_entity("testtable", "test-partition-key",
	  "1")

## 엔터티 집합 쿼리

테이블에서 엔터티 집합을 쿼리하려면 쿼리 해시 개체를 만들고 **query\_entities()** 메서드를 사용합니다. 다음 예제에서는 동일한 **PartitionKey**를 가진 엔터티를 모두 가져오는 방법을 보여 줍니다.

	query = { :filter => "PartitionKey eq 'test-partition-key'" }
	result, token = azure_table_service.query_entities("testtable", query)

> [AZURE.NOTE] 단일 쿼리에서 반환할 결과 집합이 너무 크면 후속 페이지를 가져오는 데 사용할 수 있는 연속 토큰이 반환됩니다.

## 엔터티 속성 하위 집합 쿼리

테이블 쿼리에서는 엔터티에서 일부 속성만 검색할 수 있습니다. "프로젝션"이라고 하는 이 기술은 대역폭을 줄이며 특히 큰 엔터티에 대한 쿼리 성능을 향상시킬 수 있습니다. select 절을 사용하고 가져올 속성의 이름을 클라이언트에 전달합니다.

	query = { :filter => "PartitionKey eq 'test-partition-key'",
	  :select => ["content"] }
	result, token = azure_table_service.query_entities("testtable", query)

## 엔터티 삭제

엔터티를 삭제하려면 **delete\_entity()** 메서드를 사용합니다. 엔터티, 엔터티의 PartitionKey 및 RowKey가 포함된 테이블의 이름을 전달해야 합니다.

		azure_table_service.delete_entity("testtable", "test-partition-key", "1")

## 테이블 삭제

테이블을 삭제하려면 **delete\_table()** 메서드를 사용하고 삭제하려는 테이블의 이름을 전달합니다.

		azure_table_service.delete_table("testtable")

## 다음 단계

더 복잡한 저장소 작업에 대해 알아보려면 다음 링크를 따라가세요.

- [Azure 저장소 팀 블로그](http://blogs.msdn.com/b/windowsazurestorage/)
- GitHub의 [Azure SDK for Ruby](http://github.com/WindowsAzure/azure-sdk-for-ruby)(영문) 리포지토리

<!---HONumber=AcomDC_0817_2016-->