<properties
    pageTitle="Azure 검색 인덱스 쿼리 | Microsoft Azure | 호스트된 클라우드 검색 서비스"
    description="Azure 검색에서 검색 쿼리를 작성하고 검색 매개 변수를 사용하여 검색 결과를 필터링하고 정렬합니다."
    services="search"
    documentationCenter=""
	authors="ashmaka"
/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# Azure 검색 인덱스 쿼리
> [AZURE.SELECTOR]
- [개요](search-query-overview.md)
- [포털](search-explorer.md)
- [.NET](search-query-dotnet.md)
- [REST (영문)](search-query-rest-api.md)

Azure 검색에 검색 요청을 제출할 때 응용 프로그램의 검색 상자에 입력하는 실제 단어와 함께 지정할 수 있는 매개 변수는 여러 가지가 있습니다. 이러한 쿼리 매개 변수를 사용하여 전체 텍스트 검색 환경을 보다 자세히 제어할 수 있습니다.

다음은 Azure 검색에서 쿼리 매개 변수의 일반적인 사용을 간략히 설명하는 목록입니다. 쿼리 매개 변수 및 해당 동작에 대한 전체 설명은 [REST API](https://msdn.microsoft.com/library/azure/dn798927.aspx) 및 [.NET SDK](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.searchparameters_properties.aspx)에 대한 상세 페이지를 참조하세요.

## 쿼리 유형

Azure 검색은 매우 강력한 쿼리를 만드는 다양한 옵션을 제공합니다. 사용할 두 가지 기본 유형 쿼리는 `search` 및 `filter`입니다. `search` 쿼리는 인덱스의 _검색 가능한_ 모든 필드에서 하나 이상의 단어를 검색하고 Google 또는 Bing과 같은 검색 엔진이 작동하리라고 예상되는 방법으로 작동합니다. `filter` 쿼리는 인덱스의 _필터링 가능한_ 모든 필드에 걸쳐 부울 식을 계산합니다. `search` 쿼리와 달리 `filter` 쿼리는 필드의 정확한 내용과 일치하며 이는 문자열 필드에서 대/소문자를 구분한다는 것을 의미합니다.

검색 및 필터를 함께 또는 별도로 사용할 수 있습니다. 이러한 작업을 함께 사용하는 경우 필터가 전체 인덱스에 먼저 적용된 다음 필터의 결과에 검색이 수행됩니다. 따라서 필터는 검색 쿼리가 처리해야 하는 일련의 문서를 감소시키기 때문에 쿼리 성능을 개선하는 데 유용한 기술일 수 있습니다.

필터 식에 대한 구문은 [OData 필터 언어](https://msdn.microsoft.com/library/azure/dn798921.aspx)의 하위 집합입니다. 검색 쿼리의 경우 [간단한 구문](https://msdn.microsoft.com/library/azure/dn798920.aspx) 또는 [Lucene 쿼리 구문](https://msdn.microsoft.com/library/azure/mt589323.aspx)을 사용할 수 있으며 아래에서 설명합니다.

### 단순 쿼리 구문
[단순 쿼리 구문](https://msdn.microsoft.com/library/azure/dn798920.aspx)은 Azure 검색에 사용되는 기본 쿼리 언어입니다. 단순 쿼리 구문은 AND, OR, NOT, 구, 접미사 및 우선 순위 연산자를 포함한 여러 일반 검색 연산자를 지원합니다.

### Lucene 쿼리 구문
[Lucene 쿼리 구문](https://msdn.microsoft.com/library/azure/mt589323.aspx)을 통해 [Apache Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)의 일부로 개발되어 널리 채택되고 표현적인 쿼리 언어를 사용할 수 있습니다.

이 쿼리 구문을 통해 [필드 범위 쿼리](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_fields), [유사 항목 검색](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_fuzzy), [근접 검색](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_proximity), [용어 승격](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_termboost), [정규식 검색](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_regex), [와일드 카드 검색](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_wildcard), [구문 기초](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_syntax) 및 [부울 연산자를 사용하여 쿼리](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_boolean)와 같은 기능을 쉽게 얻을 수 있습니다.



## 결과 정렬
검색 쿼리에 대한 결과를 받을 때 Azure 검색에서 특정 필드의 값을 기준으로 결과를 제공하도록 요청할 수 있습니다. 기본적으로 Azure 검색은 [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)에서 파생되는 각 문서의 검색 점수 순위에 따라 검색 결과를 정렬합니다.

Azure 검색에서 검색 점수 이외의 값으로 결과를 정렬하여 반환하려면 `orderby` 검색 매개 변수를 사용할 수 있습니다. 지리 공간 값에 대한 필드 이름 및 [`geo.distance()` 함수](https://msdn.microsoft.com/library/azure/dn798921.aspx)에 대한 호출을 포함하도록 `orderby` 매개 변수 값을 지정할 수 있습니다. 각 식 뒤에는 결과가 오름차순으로 요청됨을 나타내는 `asc`와 결과가 내림차순으로 요청되는 `desc`가 나타날 수 있습니다. 기본 순위는 오름차순입니다.

## 페이징
Azure 검색을 사용하면 검색 결과의 페이징을 구현하기가 쉽습니다. `top` 및 `skip` 매개 변수를 사용하면 적절한 검색 UI 사례를 쉽게 사용할 수 있도록 모든 검색 결과 집합을 관리 가능하며 정렬된 하위 집합으로 수신할 수 있는 검색 요청을 원활하게 실행할 수 있습니다. 이러한 작은 하위 집합 결과를 받을 때 총 검색 결과 집합에서 문서 수도 수신할 수 있습니다.

[Azure 검색에서 검색 결과를 페이징하는 방법](search-pagination-page-layout.md) 문서에서 페이징 검색 결과에 대해 자세히 알아볼 수 있습니다.


## 적중 항목 강조 표시
Azure 검색에서는 `highlight`, `highlightPreTag` 및 `highlightPostTag` 매개 변수를 사용하여 검색 쿼리와 일치하는 검색 결과의 정확한 부분을 쉽게 강조할 수 있습니다. 어떤 _검색 가능한_ 필드의 일치 텍스트를 강조할지를 지정할 수 있으며 Azure 검색에서 반환하는 일치 텍스트의 시작 및 끝부분에 추가할 정확한 문자열 태그를 지정할 수 있습니다.

<!---HONumber=AcomDC_0831_2016-->