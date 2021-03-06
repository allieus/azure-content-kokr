<properties
	pageTitle="Azure AD Connect 동기화: 기본 구성을 변경하는 방법 | Microsoft Azure"
	description="Azure AD Connect 동기화의 구성을 변경하는 방법을 안내합니다."
	services="active-directory"
	documentationCenter=""
	authors="andkjell"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/23/2016"
	ms.author="andkjell"/>


# Azure AD Connect 동기화: 기본 구성을 변경하는 방법
이 항목의 목적은 Azure AD Connect 동기화에서 기본 구성을 변경하는 방법을 안내하는 것입니다. 몇 가지 일반적인 시나리오를 위한 단계를 제공합니다. 이러한 지식을 바탕으로 사용자의 고유한 비즈니스 규칙에 따라 자체 구성에 대해 몇 가지 간단한 내용을 변경할 수 있습니다.

## 동기화 규칙 편집기
동기화 규칙 편집기는 기본 구성을 확인하고 변경하는 데 사용됩니다. **Azure AD Connect** 그룹 아래에 있는 시작 메뉴에서 찾을 수 있습니다. ![동기화 규칙 편집기를 사용 하여 메뉴 시작](./media/active-directory-aadconnectsync-change-the-configuration/startmenu2.png)

이를 열면 기본 규칙이 나타납니다.

![동기화 규칙 편집기](./media/active-directory-aadconnectsync-change-the-configuration/sre2.png)

### 편집기에서 탐색
편집기 맨 위에 있는 드롭다운 메뉴를 사용하여 특정 규칙을 빠르게 찾을 수 있습니다. 예를 들어 proxyAddresses 특성을 포함하는 규칙을 보려는 경우 드롭다운을 다음과 같이 변경합니다. ![SRE 필터링](./media/active-directory-aadconnectsync-change-the-configuration/filtering.png) 필터링을 다시 설정하고 새로운 구성을 로드하려면 키보드의 **F5**를 누릅니다.

오른쪽 맨 위에는 **새 규칙 추가** 단추가 있습니다. 이 단추를 사용하여 사용자 지정 규칙을 만듭니다.

아래에는 선택된 동기화 규칙에 대한 작업을 수행하는 단추가 있습니다. **편집** 및 **삭제** 단추를 누르면 해당 작업을 수행합니다. **내보내기**는 동기화 규칙을 다시 만들기 위해 PowerShell 스크립트를 생성합니다. 이 절차를 통해 동기화 규칙을 한 서버에서 다른 서버로 이동할 수 있습니다.

## 첫 번째 사용자 지정 규칙 만들기
가장 일반적인 변경은 특성 흐름을 변경하는 것입니다. 원본 디렉터리의 데이터는 사용자가 원하는 방식으로 Azure AD에 저장되지 않을 수도 있습니다. 이 섹션의 예제에서는 지정된 사용자의 이름이 항상 **적절한 대/소문자**로 표시되는지 확인해야 합니다.

### 스케줄러 사용 안 함
[스케줄러](active-directory-aadconnectsync-feature-scheduler.md)는 기본적으로 30분마다 실행됩니다. 변경을 수행하고 새 규칙의 문제를 해결하는 동안 스케줄러가 시작되지 않도록 해야 합니다. 스케줄러를 일시적으로 사용하지 않으려면 PowerShell을 시작하고 `Set-ADSyncScheduler -SyncCycleEnabled $false`을(를) 실행합니다.

![스케줄러 사용 안 함](./media/active-directory-aadconnectsync-change-the-configuration/schedulerdisable.png)

### 규칙 만들기

1. **새 규칙 추가**를 클릭합니다.
2. **설명** 페이지에서 다음을 입력합니다. ![인바운드 규칙 필터링](./media/active-directory-aadconnectsync-change-the-configuration/description2.png)
	- 이름: 규칙에 설명이 포함된 이름을 지정합니다.
	- 설명: 다른 사용자가 어떤 규칙인지 이해할 수 있도록 하기 위한 간단한 설명입니다.
	- 연결된 시스템: 개체를 찾을 수 있는 시스템입니다. 이 경우 Active Directory Connector를 선택합니다.
	- 연결된 시스템/메타 버스 개체 유형: **사용자**와 **사람**을 각각 선택합니다.
	- 링크 형식:이 값을 **조인**으로 변경합니다.
	- 우선 순위: 시스템에서 고유한 값을 제공합니다. 숫자 값이 낮을수록 높은 우선 순위를 나타냅니다.
	- 태그: 비워 둡니다. Microsoft의 기본 규칙으로만 이 상자에 값을 채워야 합니다.
3. **범위 지정 필터** 페이지에서 **givenName ISNOTNULL**를 입력합니다. ![인바운드 규칙 범위 지정 필터](./media/active-directory-aadconnectsync-change-the-configuration/scopingfilter.png) 이 섹션은 규칙을 적용해야 개체를 정의하는 데 사용됩니다. 이를 비워 두면 규칙은 모든 사용자 개체에 적용됩니다. 하지만 여기에는 회의실, 서비스 계정 및 기타 사람이 아닌 사용자 개체가 포함됩니다.
4. **조인 규칙**에서는 이를 비워 둡니다.
5. **변환을** 페이지에서 FlowType을 **식**으로 변경합니다. 대상 특성 **giveName**을 선택하고, 소스에서 `PCase([givenName])`을(를) 입력합니다. ![인바운드 규칙 변환](./media/active-directory-aadconnectsync-change-the-configuration/transformations.png) 동기화 엔진은 함수 이름과 특성의 이름에서 대/소문자를 구분합니다. 잘못 입력한 경우 규칙을 추가할 때 경고가 표시됩니다. 규칙을 다시 열고 수정할 수 있도록 편집기를 사용하여 저장하고 계속합니다.
6. **추가**를 클릭하여 규칙을 저장합니다.

새 사용자 지정 규칙은 시스템의 다른 동기화 규칙과 함께 표시되어야 합니다.

### 변경 확인
이 새로운 변경으로 예상한 대로 작동하고 어떠한 오류도 발생하지 않는 확인해야 합니다. 개체 수에 따라 이 단계를 수행하는 두 가지 방식이 있습니다.

1. 모든 개체에서 전체 동기화 실행
2. 단일 개체에서 미리 보기 및 전체 동기화 실행

시작 메뉴에서 **동기화 서비스**를 시작합니다. 이 섹션의 단계는 이 도구에 모두 있습니다.

1. **모든 개체에서 전체 동기화** 위쪽에서 **커넥터**를 선택합니다. 이전 섹션에서 변경 사항을 적용할 커넥터를 식별합니다. 이 경우 Active Directory 도메인 서비스를 선택합니다. 작업에서 **실행**을 선택하고 **전체 동기화**와 **확인**을 선택합니다. ![전체 동기화](./media/active-directory-aadconnectsync-change-the-configuration/fullsync.png) 개체는 이제 메타 버스에 업데이트됩니다. 이제 메타 버스의 개체를 보려 합니다.

2. **단일 개체에서 미리 보기 및 전체 동기화** 위쪽에서 **커넥터**를 선택합니다. 이전 섹션에서 변경 사항을 적용할 커넥터를 식별합니다. 이 경우 Active Directory 도메인 서비스를 선택합니다. **커넥터 공간 검색**을 선택합니다. 범위를 사용하여 변경 사항을 테스트하려는 개체를 찾습니다. 개체를 선택하고 **미리 보기**를 클릭합니다. 새 화면에서 **커밋 미리 보기**를 선택합니다. ![커밋 미리 보기](./media/active-directory-aadconnectsync-change-the-configuration/commitpreview.png) 이제 변경 사항이 메타 버스에 커밋됩니다.

**메타 버스의 개체 보기** 이제 값을 예상하고 규칙을 적용하기 위해 몇 가지 샘플 개체를 선택해야 합니다. 위쪽에서 **메타 버스 검색**을 선택합니다. 관련 개체를 찾기 위해 필요한 모든 필터를 추가합니다. 검색 결과에서 개체를 엽니다. 특성 값을 살펴보고 **동기화 규칙** 열에서 규칙이 예상대로 적용되었는지 확인합니다. ![메타 버스 검색](./media/active-directory-aadconnectsync-change-the-configuration/mvsearch.png)
### 스케줄러 사용
모든 것이 예상대로 적용된 경우 스케줄러를 다시 사용할 수 있습니다. PowerShell에서 `Set-ADSyncScheduler -SyncCycleEnabled $true`을(를) 실행합니다.

## 기타 일반적인 특성 흐름 변경
이전 섹션에서는 특성 흐름을 변경하는 방법을 설명하였습니다. 이 섹션에서는 몇 가지 추가 예제가 제공됩니다. 동기화 규칙을 만드는 방법에 대한 단계가 간략하게 설명되어 있지만 이전 섹션에서 전체 단계를 찾아볼 수 있습니다.

### 기본값과 다른 특성 사용
Fabrikam에는 지정된 이름, 성 및 표시 이름에 로컬 알파벳이 사용된 포리스트가 있습니다. 이러한 특성의 라틴 문자 표현은 확장 특성에서 찾을 수 있습니다. Azure AD 및 Office 365에서 전체 주소 목록을 빌드할 때 조직이 이러한 특성을 대신 사용합니다.

기본 구성을 사용하면 로컬 포리스트의 개체는 다음과 같이 나타납니다.![특성 흐름 1](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp1.png)

다른 특성 흐름으로 규칙을 만들려면 다음을 수행합니다.

- 시작 메뉴에서 **동기화 규칙 편집기**를 시작합니다.
- 왼쪽에서 **인바운드**를 선택한 상태에서 **새 규칙 추가** 단추를 클릭합니다.
- 규칙에 이름 및 설명을 지정합니다. 온-프레미스 Active Directory 및 관련 개체 형식을 선택합니다. **링크 형식**에서 **조인**을 선택합니다. 우선 순위로 다른 규칙에서 사용하지 않은 숫자를 선택합니다. 기본 규칙이 100에서 시작하므로 값 50을 다음 예제에서 사용할 수 있습니다. ![특성 흐름 2](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp2.png)
- 범위를 비워 둡니다(즉, 포리스트의 모든 사용자 개체에 적용해야 합니다).
- 조인 규칙을 비워 둡니다(즉, 기본 규칙이 모든 연결을 처리할 수 있도록 합니다).
- 변환에서는 다음 흐름을 만듭니다. ![특성 흐름 3](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp3.png)
- **추가**를 클릭하여 규칙을 저장합니다.
- **Synchronization Service Manager**로 이동합니다. **커넥터**에서 규칙을 추가한 커넥터를 선택합니다. **실행**을 선택한 다음 **전체 동기화**를 선택합니다. 전체 동기화는 현재 규칙을 사용하여 모든 개체를 다시 계산합니다.

이 사용자 지정 규칙이 있는 동일한 개체에 대한 결과는 다음과 같습니다. ![특성 흐름 4](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp4.png)

### 특성의 길이
문자열 특성은 기본적으로 인덱싱 가능하게 설정되고 최대 길이는 448자입니다. 더 포함할 수 있는 문자열 특성을 사용한다면 특성 흐름에 반드시 다음을 포함하십시오. `attributeName` <- `Left([attributeName],448)`

### userPrincipalSuffix 변경
Active Directory의 userPrincipalName 특성을 항상 사용자가 알 수 있는 것은 아니며 로그인 ID로 적절하지 않을 수도 있습니다. Azure AD Connect Sync 설치 마법사를 통해 여러 특성을 선택할 수 있습니다(예: 메일). 하지만 일부 경우에는 특성을 계산해야 합니다. 예를 들어 회사 Contoso에는 2개의 Azure AD 디렉터리가 있습니다. 하나는 프로덕션용이고 하나는 테스트용입니다. 테스트 테넌트의 사용자가 로그인 ID의 접미사만 사용하도록 할 수 있습니다. `userPrincipalName` <- `Word([userPrincipalName],1,"@") & "@contosotest.com"`

이 식에서는 첫 번째 @ 기호(Word) 왼쪽에 있는 모든 것을 고정된 문자열과 연결합니다.

### 다중 값을 단일 값으로 변환
Active Directory 내의 일부 특성은 Active Directory 사용자 및 컴퓨터에서 단일 값으로 보이더라도 스키마에서는 다중 값입니다. 그 예는 설명 특성입니다. `description` <- `IIF(IsNullOrEmpty([description]),NULL,Left(Trim(Item([description],1)),448))`

이 식에서 특성에 값이 있는 경우 해당 특성 내 첫번째 항목(항목)에서 선행 및 후행 공백을 제거한 다음(트리밍), 문자열에 처음 448개의 문자(왼쪽)를 유지합니다.

### 특성을 전달하지 않습니다.
이 섹션에 대한 시나리오의 배경은 [특성 흐름 프로세스 제어](#control-the-attribute-flow-process)를 참조하세요.

특성을 전달하지 않는 방법에는 다음 두 가지가 있습니다. 첫 번째 방법은 설치 마법사를 사용하여 [선택한 특성을 제거합니다](active-directory-aadconnect-get-started-custom.md#azure-ad-app-and-attribute-filtering). 이 옵션은 전에 특성을 동기화하지 않은 경우에만 작동합니다. 하지만 이 특성을 동기화한 다음 이후에 이 기능을 사용하여 제거한 경우 동기화 엔진은 이 특성을 관리하는 것을 중지하고 Azure AD에 기존 값이 남아 있게 됩니다.

특성 값을 제거하고 나중에 이 값이 전달되지 않는지 확인하려면 대신 사용자 지정 규칙을 생성해야 합니다.

Fabrikam에서는 클라우드에 동기화된 특성 중 일부라도 클라우드에 남아 있지 않아야 합니다. Azure AD에서 이들 특성이 제거되었는지 확인하려 합니다. ![잘못된 확장 특성](./media/active-directory-aadconnectsync-change-the-configuration/badextensionattribute.png)

- 새 인바운드 동기화 규칙 만들기 및 설명 ![설명](./media/active-directory-aadconnectsync-change-the-configuration/syncruledescription.png) 채우기
- 원본 **AuthoritativeNull**을 포함하는 형식 **식**의 특성 흐름을 만듭니다. 리터럴 **AuthoritativeNull**은 심지어 우선 순위가 더 낮은 동기화 규칙이 그 값을 채우려고 해도 MV에서 그 값이 비어 있어야 함을 나타냅니다. ![확장 특성을 위한 변환](./media/active-directory-aadconnectsync-change-the-configuration/syncruletransformations.png)
- 해당 동기화 규칙을 저장합니다. **동기화 서비스**를 시작하여 커넥터를 찾고 **실행**을 선택한 다음 **전체 동기화**를 선택합니다. 이 단계는 모든 특성 흐름을 다시 계산합니다.
- 의도한 변경 내용을 커넥터 공간을 검색하여 내보낼 수 있는지 확인합니다. ![단계적 삭제](./media/active-directory-aadconnectsync-change-the-configuration/deletetobeexported.png)

## 고급 개념

### 특성 흐름 프로세스 제어
동일한 메타 버스 특성에 적용하도록 인바운드 동기화 규칙을 여러 개 구성한 경우, 우선 순위는 우선권을 결정하는 데 사용됩니다. 가장 높은 우선 순위(가장 낮은 숫자 값)의 동기화 규칙이 값이 적용됩니다. 이는 아웃바운드 규칙에 대해서도 동일하게 적용됩니다. 가장 높은 우선 순위의 동기화 규칙이 우선하고 연결된 된 디렉터리에 값을 적용합니다.

경우에 따라 동기화 규칙은 값을 적용하는 대신 다른 규칙의 적용 방식을 결정합니다. 그러한 경우에 사용되는 특수한 리터럴이 몇 가지 있습니다.

인바운드 동기화 규칙의 경우, **NULL** 리터럴은 흐름이 어떤 값도 적용하지 않음을 나타내는 데 사용할 수 있습니다. 우선 순위가 낮은 다른 규칙이 값을 적용할 수 있습니다. 값을 기여한 규칙이 없다면 메타 버스 특성이 제거됩니다. 아웃바운드 규칙에서 모든 동기화 규칙이 처리된 후 최종 값이 **NULL**인 경우 해당 값은 연결된 디렉터리에서 제거됩니다.

**AuthoritativeNull** 리터럴은 **NULL**과 비슷하지만, 차이점은 낮은 우선 순위 규칙이 값을 적용할 수 없다는 것입니다.

특성 흐름은 또한 **IgnoreThisFlow**를 사용할 수 있습니다. 이는 적용할 것이 없다는 것을 나타내는 면에서 NULL과 유사합니다. 차이점은 대상에서 이전의 기존 값을 제거하지 않는다는 것입니다. 이는 특성 흐름이 애초에 있지 않았다는 것과 같습니다.

다음은 예제입니다.

*AD로 나가기 - 사용자 Exchange 하이브리드*에서 다음 흐름을 찾을 수 있습니다. `IIF([cloudSOAExchMailbox] = True,[cloudMSExchSafeSendersHash],IgnoreThisFlow)` 이 식은 다음과 같이 읽어야 합니다. 사용자 사서함이 Azure AD에 있는 경우 특성을 Azure AD에서 AD로 전달합니다. 그렇지 않으면 Active Directory에 아무 것도 전달되지 않습니다. 이 경우 AD의 기존 값이 유지됩니다.

### ImportedValue
ImportedValue 함수는 특성 이름을 대괄호 대신 따옴표로 묶어야 하므로 다른 모든 함수와 다릅니다. `ImportedValue("proxyAddresses")`.

일반적으로 동기화 중에는 내보내는 동안("타워 최상위")에 오류가 발생하거나 아직 내보내지 않았어도 특성이 예상 값을 사용합니다. 인바운드 동기화는 연결된 디렉터리에 아직 도달하지 않은 특성도 결국 도달할 것으로 가정합니다. 일부 경우에는 연결된 디렉터리에서 확인한 값만 동기화한다는 것이 중요합니다("홀로그램 및 델타 가져오기 타워").

기본 동기화 규칙 *AD에서 들어오기 – Exchange에서 사용자 공통*에서 이 함수의 예제를 찾을 수 있습니다. 하이브리드 Exchange에서 값을 성공적으로 내보냈음이 확인된 경우 Exchange Online에 의해 추가되는 값만 동기화됩니다. `proxyAddresses` <- `RemoveDuplicates(Trim(ImportedValue("proxyAddresses")))`

## 다음 단계

특성 흐름에 사용되는 [선언적 프로비저닝 식](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md)에 대해 자세히 알아봅니다.

[Azure AD Connect 동기화](active-directory-aadconnectsync-whatis.md) 구성에 대해 자세히 알아봅니다.

[Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)에 대해 자세히 알아봅니다.

<!---HONumber=AcomDC_0824_2016-->