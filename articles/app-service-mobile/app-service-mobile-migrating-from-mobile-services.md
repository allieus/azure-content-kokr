<properties
	pageTitle="모바일 서비스에서 앱 서비스 모바일 앱으로 마이그레이션"
	description="모바일 서비스 응용 프로그램을 앱 서비스 모바일 앱으로 쉽게 마이그레이션하는 방법을 알아봅니다."
	services="app-service\mobile"
	documentationCenter=""
	authors="adrianhall"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/26/2016"
	ms.author="adrianhall"/>

# <a name="article-top"></a>기존 Azure 모바일 서비스를 Azure 앱 서비스로 마이그레이션

[Azure 앱 서비스의 일반적인 가용성]을 사용하여 Azure 모바일 서비스 사이트를 쉽게 원래 위치로 마이그레이션하여 Azure 앱 서비스의 모든 기능을 활용할 수 있습니다. 이 문서에서는 Azure 모바일 서비스에서 Azure 앱 서비스에 사이트를 마이그레이션하는 경우의 결과를 설명합니다.

## <a name="what-does-migration-do"></a>사이트에 대한 마이그레이션의 기능

Azure 모바일 서비스의 마이그레이션은 코드에 전혀 영향을 주지 않고 모바일 서비스를 [Azure 앱 서비스] 앱으로 변환합니다. 알림 허브, SQL 데이터 연결, 인증 설정, 예약된 작업 및 도메인 이름은 변경되지 않습니다. Azure 모바일 서비스를 사용하는 모바일 클라이언트는 정상적으로 계속 작동합니다. 마이그레이션이 Azure 앱 서비스에 전송되면 서비스를 다시 시작합니다.

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

## <a name="why-migrate"></a>사이트를 마이그레이션해야 하는 이유

Microsoft는 다음을 비롯한 Azure 앱 서비스의 기능을 활용하기 위해 Azure 모바일 서비스를 마이그레이션하도록 권장합니다.

  *  [WebJobs] 및 [사용자 지정 도메인 이름]을 포함하는 새로운 호스트 기능.
  *  [하이브리드 연결] 외에 [VNet]을 사용하여 온-프레미스 리소스에 연결.
  *  New Relic 또는 [Application Insights]를 통한 모니터링 및 문제 해결.
  *  [스테이징 슬롯], 롤백 및 프로덕션 내 테스트를 포함하는 기본 제공 DevOps 도구.
  *  [자동 크기 조정], 부하 분산 및 [성능 모니터링].

Azure 앱 서비스의 이점에 대한 자세한 내용은 [모바일 서비스 vs. 앱 서비스] 항목을 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에

사이트에서 주요 작업을 시작하기 전에 [모바일 서비스] 스크립트와 SQL 데이터베이스를 백업해야 합니다.

프로덕션 사이트를 마이그레이션하기 전에 마이그레이션 프로세스를 테스트하려는 경우 새 [Azure 지역] 내에 프로덕션 Azure 모바일 서비스(데이터 원본의 복사본으로 완성)를 복제하고 새 URL에 대한 마이그레이션을 테스트합니다. 또한 올바르게 마이그레이션되는 사이트를 테스트하려면 테스트 사이트를 가리키는 테스트 클라이언트 구현이 필요합니다.

## <a name="migrating-site"></a>사이트 마이그레이션

마이그레이션 프로세스는 단일 Azure 지역 내의 모든 사이트를 마이그레이션합니다.

사이트를 마이그레이션하려면:

  1.  [Azure 클래식 포털]에 로그인합니다.
  2.  마이그레이션할 지역의 모바일 서비스를 선택합니다.
  3.  **앱 서비스 마이그레이션** 단추를 클릭합니다.

    ![마이그레이션 단추][0]

  4.  앱 서비스 대화 상자에 마이그레이션을 읽습니다.
  5.  제공된 상자에 모바일 서비스의 이름을 입력합니다. 예를 들어 도메인 이름이 contoso.azure mobile.net이면 제공된 상자에 _contoso_를 입력합니다.
  6.  눈금 단추를 클릭합니다.

작업 모니터의 마이그레이션 상태를 모니터링할 수 있고 사이트가 Azure 클래식 포털에서 *마이그레이션* 으로 나열됩니다.

  ![마이그레이션 작업 모니터링][1]

각 마이그레이션은 마이그레이션되는 모바일 서비스 당 3-15분이 걸릴 수 있습니다. 사이트는 마이그레이션 중 사용할 수 있는 상태로 유지되지만 마이그레이션 프로세스가 끝날 때 다시 시작됩니다. 다시 시작 프로세스 동안 사이트는 사용할 수 없게 되며 몇 초 동안 지속될 수 있습니다.

## <a name="finalizing-migration"></a>마이그레이션 완료

마이그레이션 프로세스가 끝날 때 모바일 클라이언트에서 사이트를 테스트하도록 계획해야 합니다. 모바일 클라이언트를 변경하지 않고 일반적인 모든 클라이언트 작업을 수행할 수 있도록 합니다. 또한 필요한 경우 마이그레이션에 영향을 주도록 변경한 내용(예: 가격 책정 계층 변경)을 되돌렸는지 확인해야 합니다.

### <a name="update-app-service-tier"></a>적절한 앱 서비스 가격 책정 계층 선택

Azure 앱 서비스를 마이그레이션한 후에 가격 책정에 유연성이 증가합니다.

  1.  [Azure 포털]에 로그인합니다.
  2.  **모든 리소스** 또는 **앱 서비스**를 선택한 후 마이그레이션된 모바일 서비스의 이름을 클릭합니다.
  3.  기본적으로 설정 블레이드가 열립니다. 열리지 않으면 **설정**을 클릭합니다.
  4.  설정 메뉴에서 **앱 서비스 계획**을 클릭합니다.
  5.  **가격 책정 계층** 타일을 클릭합니다.
  6.  요구 사항에 적합한 타일을 클릭한 다음 **선택**을 클릭합니다. **모두 보기**를 클릭하여 사용 가능한 가격 책정 계층을 볼 수 있습니다.

시작 지점으로 다음을 권장합니다.

| 모바일 서비스 가격 책정 계층 | 앱 서비스 가격 책정 계층 |
| :-------------------------- | :----------------------- |
| 무료 | F1 무료 |
| Basic | B1 기본 |
| 표준 | S1 표준 |

응용 프로그램에 대한 올바른 가격 책정 계층을 선택하는 데 유연성을 고려합니다. 새 앱 서비스의 가격 책정에 대한 자세한 내용은 [앱 서비스 가격]을 참조합니다.

> [AZURE.TIP] 앱 서비스 표준 계층은 [스테이징 슬롯], 자동 백업, 자동 크기 조정을 비롯한 사용하려는 다양한 기능에 대한 액세스를 포함합니다. 이 때 새로운 기능을 확인해 보십시오.

### <a name="review-migration-scheduler-jobs"></a>마이그레이션된 스케줄러 작업 검토

스케줄러 작업은 마이그레이션 후에 약 30분까지 표시되지 않습니다. 예약된 작업은 백그라운드에서 계속 실행됩니다. 예약된 작업을 보려면:

  1.  [Azure 포털]에 로그인합니다.
  2.  **찾아보기>**를 선택하고 _필터_ 상자에 **일정**을 입력한 다음 **스케줄러 컬렉션**을 선택합니다.

마이그레이션 후에 사용 가능한 무료 스케줄러 작업의 수가 제한됩니다. 사용량 및 [Azure 스케줄러 계획]을 검토해야 합니다.

### <a name="configure-cors"></a>필요한 경우 CORS 구성

원본 간 리소스 공유는 웹 사이트가 다른 도메인의 Web API에 액세스하도록 허용하는 기술입니다. 관련된 웹 사이트를 통해 Azure 모바일 서비스를 사용하면 마이그레이션의 일부로 CORS를 구성해야 합니다. Azure 모바일 서비스를 모바일 장치에서 단독으로 액세스하는 경우 드문 경우를 제외하고 CORS를 구성할 필요는 없습니다.

마이그레이션된 CORS 설정은 **MS\_CrossDomainWhitelist** 앱 설정으로 사용할 수 있습니다. 앱 서비스의 CORS 기능에 사이트를 마이그레이션하려면:

  1.  [Azure 포털]에 로그인합니다.
  2.  **모든 리소스** 또는 **앱 서비스**를 선택한 후 마이그레이션된 모바일 서비스의 이름을 클릭합니다.
  3.  기본적으로 설정 블레이드가 열립니다. 열리지 않으면 **설정**을 클릭합니다.
  4.  API 메뉴에서 **CORS**를 클릭합니다.
  5.  제공된 상자에 허용된 원본을 입력하고 각각 Enter 키를 누릅니다.
  6.  허용된 원본 목록이 올바르면 저장 단추를 클릭합니다.

선택적 작업이지만 이후에 더 나은 관리 환경을 제공합니다.

> [AZURE.TIP]  Azure 앱 서비스의 장점 중 하나는 동일한 사이트에서 웹 사이트 및 모바일 서비스를 실행할 수 있다는 점입니다. 자세한 내용은 [다음 단계](#next-steps) 섹션을 참조하세요.

### <a name="download-publish-profile"></a>새 게시 프로필 다운로드

Azure 앱 서비스로 마이그레이션할 때 사이트의 게시 프로필이 변경됩니다. Visual Studio 내에서 사이트를 게시하려면 새 게시 프로필이 필요합니다. 새 게시 프로필을 다운로드하려면

  1.  [Azure 포털]에 로그인합니다.
  2.  **모든 리소스** 또는 **앱 서비스**를 선택한 후 마이그레이션된 모바일 서비스의 이름을 클릭합니다.
  3.  **게시 프로필 가져오기**를 클릭합니다.

PublishSettings 파일이 컴퓨터에 다운로드됩니다. 일반적으로 _sitename_.PublishSettings가 호출됩니다. 그런 다음 기존 프로젝트에 게시 설정을 가져올 수 있습니다.

  1.  Visual Studio 및 Azure 모바일 서비스 프로젝트를 엽니다.
  2.  **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시...**를 선택합니다.
  3.  **가져오기**를 클릭합니다.
  4.  **찾아보기**를 클릭하고 다운로드한 게시 설정 파일을 선택합니다. **확인**을 클릭합니다.
  5.  **연결 유효성 검사**를 클릭하여 게시 설정이 작동하는지 확인합니다.
  6.  **게시**를 클릭하여 사이트를 게시합니다.


## <a name="working-with-your-site"></a>마이그레이션 후에 사이트로 작업

마이그레이션 후에 [Azure 포털]에서 새 앱 서비스로 작업을 시작합니다. 다음은 해당하는 앱 서비스와 함께 [Azure 클래식 포털]에서 수행하는 데 사용되는 몇 가지 주의 사항입니다.

### <a name="publishing-your-site"></a>마이그레이션된 사이트 다운로드 및 게시

사이트는 git 또는 ftp를 통해 사용될 수 있고 WebDeploy, TFS, Mercurial, GitHub, FTP 등 다양한 메커니즘을 사용하여 다시 게시될 수 있습니다. 배포 자격 증명은 사이트의 나머지 부분을 통해 마이그레이션됩니다. 배포 자격 증명을 설정하지 않거나 기억하지 못하는 경우 다시 설정할 수 있습니다.

  1. [Azure 포털]에 로그인합니다.
  2. **모든 리소스** 또는 **앱 서비스**를 선택한 후 마이그레이션된 모바일 서비스의 이름을 클릭합니다.
  3. 기본적으로 설정 블레이드가 열립니다. 열리지 않으면 **설정**을 클릭합니다.
  4. 게시 메뉴에서 **배포 자격 증명**을 클릭합니다.
  5. 제공한 상자에 새 배포 자격 증명을 입력한 다음 저장 단추를 클릭합니다.

이러한 자격 증명을 사용하여 git를 통해 사이트를 복제하거나 GitHub, TFS 또는 Mercurial에서 자동화된 배포를 설치할 수 있습니다. 자세한 내용은 [Azure 앱 서비스 배포 설명서]를 참조하세요.

### <a name="appsettings"></a>응용 프로그램 설정

마이그레이션된 모바일 서비스에 대한 설정은 대부분 앱 설정을 통해 사용할 수 있습니다. [Azure 포털]에서 앱 설정의 목록을 가져올 수 있습니다. 앱 설정을 보거나 변경하려면:

  1. [Azure 포털]에 로그인합니다.
  2. **모든 리소스** 또는 **앱 서비스**를 선택한 후 마이그레이션된 모바일 서비스의 이름을 클릭합니다.
  3. 기본적으로 설정 블레이드가 열립니다. 열리지 않으면 **설정**을 클릭합니다.
  4. 일반 메뉴에서 **응용 프로그램 설정**을 클릭합니다.
  5. 앱 설정 섹션으로 스크롤하고 앱 설정을 찾습니다.
  6. 앱 설정 값을 클릭하여 값을 편집합니다. **저장**을 클릭하여 값을 저장합니다.

동시에 여러 앱 설정을 업데이트할 수 있습니다.

> [AZURE.TIP]  동일한 값을 가진 두 응용 프로그램 설정이 있는지 알 수 있습니다. 예를 들어 _ApplicationKey_ 및 _MS\_ApplicationKey_를 확인할 수 있습니다. **MS\_**라는 접두사가 붙은 앱 설정을 변경해야 합니다. 그러나 두 가지 앱 설정을 동시에 업데이트하는 것이 좋습니다.

### <a name="authentication"></a>인증

모든 인증 설정은 마이그레이션된 사이트에서 앱 설정으로 사용할 수 있습니다. 인증 설정을 업데이트하려면 적절한 앱 설정을 변경해야 합니다. 다음 테이블에서는 인증 공급자에 대한 적절한 앱 설정을 보여줍니다.

| 공급자 | 클라이언트 ID | 클라이언트 암호 | 기타 설정 |
| :---------------- | :------------------------ | :--------------------------- | :------------------------- |
| Microsoft 계정 | **MS\_MicrosoftClientID** | **MS\_MicrosoftClientSecret** | **MS\_MicrosoftPackageSID** |
| Facebook | **MS\_FacebookAppID** | **MS\_FacebookAppSecret** | |
| Twitter | **MS\_TwitterConsumerKey** | **MS\_TwitterConsumerSecret** | |
| Google | **MS\_GoogleClientID** | **MS\_GoogleClientSecret** | |
| Azure AD | **MS\_AadClientID** | | **MS\_AadTenants** |

참고: **MS\_AadTenants**는 테넌트 도메인의 쉼표로 구분된 목록으로 저장됩니다(모바일 서비스 포털에서 "허용된 테넌트" 필드).

> [AZURE.WARNING] **설정 메뉴에 인증 메커니즘을 사용하지 마십시오**
>
> Azure 앱 서비스는 _인증/권한 부여_ 설정 메뉴에서 별도의 "코드 없는" 인증 및 권한 부여 시스템 및 설정 메뉴에서 (사용되지 않는) _모바일 인증_ 옵션을 제공합니다. 이러한 옵션은 마이그레이션된 Azure 모바일 서비스와 호환되지 않습니다. [사이트를 업그레이드](app-service-mobile-net-upgrading-from-mobile-services.md)하여 Azure 앱 서비스 인증을 활용할 수 있습니다.

### <a name="easytables"></a>데이터

모바일 서비스에서 _데이터_ 탭은 Azure 포털 내에서 _쉬운 테이블_로 대체되었습니다. 쉬운 테이블에 액세스하려면:

  1. [Azure 포털]에 로그인합니다.
  2. **모든 리소스** 또는 **앱 서비스**를 선택한 후 마이그레이션된 모바일 서비스의 이름을 클릭합니다.
  3. 기본적으로 설정 블레이드가 열립니다. 열리지 않으면 **설정**을 클릭합니다.
  4. 모바일 메뉴에서 **쉬운 테이블**을 클릭합니다.

**추가** 단추를 클릭하여 새 테이블을 추가하거나 테이블 이름을 클릭하여 기존 테이블에 액세스할 수 있습니다. 다음을 비롯하여 이 블레이드에서 할 수 있는 다양한 작업이 있습니다.

  * 테이블 사용 권한 변경
  * 작업 스크립트 편집
  * 테이블 스키마 관리
  * 테이블 삭제
  * 테이블 내용 지우기
  * 테이블의 특정 행 삭제

### <a name="easyapis"></a>API

모바일 서비스에서 _API_ 탭은 Azure 포털 내에서 _쉬운 API_로 대체되었습니다. 쉬운 API에 액세스하려면:

  1. [Azure 포털]에 로그인합니다.
  2. **모든 리소스** 또는 **앱 서비스**를 선택한 후 마이그레이션된 모바일 서비스의 이름을 클릭합니다.
  3. 기본적으로 설정 블레이드가 열립니다. 열리지 않으면 **설정**을 클릭합니다.
  4. 모바일 메뉴에서 **쉬운 API**를 클릭합니다.

마이그레이션된 API는 블레이드에 이미 나열됩니다. 또한 이 블레이드에서 새로운 API를 추가할 수 있습니다. 특정 API를 관리하려면 API를 클릭합니다. 새 블레이드에서 사용 권한을 조정하고 API에 대한 스크립트를 편집할 수 있습니다.

### <a name="on-demand-jobs"></a>스케줄러 작업

모든 스케줄러 작업은 스케줄러 작업 컬렉션 섹션을 통해 사용할 수 있습니다. 스케줄러 작업에 액세스하려면

  1. [Azure 포털]에 로그인합니다.
  2. **찾아보기>**를 선택하고 _필터_ 상자에 **일정**을 입력한 다음 **스케줄러 컬렉션**을 선택합니다.
  3. 사이트에 대한 작업 컬렉션을 선택합니다. _sitename_-Jobs로 이름이 지정됩니다.
  4. **설정**을 클릭합니다.
  5. 관리 아래 **스케줄러 작업**을 클릭합니다.

예약된 작업이 마이그레이션 전에 지정한 빈도와 함께 나열됩니다. 주문형 작업을 사용할 수 없게 됩니다. 주문형 작업을 실행하려면

  1. 실행하려는 작업을 선택합니다.
  2. 필요한 경우 **사용**을 클릭하여 작업을 사용하도록 설정합니다.
  3. **설정**을 클릭한 다음 **일정**을 클릭합니다.
  4. **한 번** 되풀이를 선택한 다음 **저장**을 클릭합니다.

요청 시 작업은 `App_Data/config/scripts/scheduler post-migration`에 위치합니다. 모든 요청 시 작업을 [WebJobs]으로 변환하는 것이 좋습니다. [WebJobs]로 새 스케줄러 작업을 작성해야 합니다.

### <a name="notification-hubs"></a>알림 허브

모바일 서비스는 푸시 알림에 알림 허브를 사용합니다. 마이그레이션 후에 알림 허브를 모바일 서비스에 연결하는 데 다음 앱 설정을 사용합니다.

| 응용 프로그램 설정 | 설명 |
| :------------------------------------- | :--------------------------------------- |
| **MS\_PushEntityNamespace** | 알림 허브 네임스페이스 |
| **MS\_NotificationHubName** | 알림 허브 이름 |
| **MS\_NotificationHubConnectionString** | 알림 허브 연결 문자열 |
| **MS\_NamespaceName** | MS\_PushEntityNamespace에 대한 별칭 |

알림 허브는 [Azure 포털]을 통해 관리됩니다. 알림 허브 이름을 적어둡니다.(앱 설정을 사용하여 찾을 수 있습니다)

  1. [Azure 포털]에 로그인합니다.
  2. **찾아보기**>를 선택한 다음 **알림 허브**를 선택합니다.
  3. 모바일 서비스와 연결된 알림 허브 이름을 클릭합니다.

> [AZURE.NOTE] 알림 허브가 "혼합" 형식인 경우 표시되지 않습니다. "혼합" 형식 알림 허브는 알림 허브 및 레거시 서비스 버스 기능을 모두 활용합니다. [혼합 네임스페이스를 변환]해야 합니다. 변환이 완료되면 알림 허브가 [Azure 포털]에 표시됩니다.

자세한 내용은 [알림 허브] 설명서를 검토합니다.

> [AZURE.TIP] [Azure 포털]의 알림 허브 관리 기능은 아직 미리 보기 상태입니다. [Azure 클래식 포털]은 모든 알림 허브를 관리하기 위해 사용 가능합니다.

### <a name="legacy-push"></a>레거시 푸시 설정

알림 허브에서 소개하기 전에 모바일 서비스에서 푸시를 구성한 경우 _레거시 푸시_가 사용됩니다. 사용 중인 푸시 구성에 알림 허브가 표시되지 않으면 _레거시 푸시_가 사용되는 것입니다. 이 기능은 다른 모든 기능과 함께 마이그레이션되어 사용할 수 있습니다. 그러나 마이그레이션이 완료된 직후 알림 허브로 업그레이드하는 것이 좋습니다.

그 동안 모든 레거시 푸시 설정을 앱 설정에서 사용할 수 있습니다(단, APNS 인증서 예외). 사이트에서 적절한 파일을 대체하여 APNS 인증서를 바꿀 수 있습니다. 이 작업은 Azure 앱 서비스에 사용할 수 있는 배포 옵션 중 하나를 통해 수행할 수 있습니다.

### <a name="app-settings"></a>기타 앱 설정

다음 추가 앱 설정은 모바일 서비스에서 마이그레이션되고 *설정* > *앱 설정*에서 사용할 수 있습니다.

| 응용 프로그램 설정 | 설명 |
| :------------------------------- | :-------------------------------------- |
| **MS\_MobileServiceName** | 앱의 이름 |
| **MS\_MobileServiceDomainSuffix** | 도메인 접두사 즉, azure-mobile.net입니다. |
| **MS\_ApplicationKey** | 응용 프로그램 키 |
| **MS\_MasterKey** | 앱 마스터 키 |

응용 프로그램 키 및 마스터 키는 원본 모바일 서비스에서 응용 프로그램 키와 동일해야 합니다. 특히 응용 프로그램 키는 모바일 클라이언트에서 전송되어 모바일 API를 사용하는 유효성을 검사합니다.

### <a name="cliequivalents"></a>해당하는 명령줄

더 이상 _Azure 모바일_ 명령을 활용하여 Azure 모바일 서비스 사이트를 관리할 수 없습니다. 대신 많은 기능이 _Azure 사이트_ 명령과 대체됩니다. 아래 테이블을 사용하여 일반적인 명령에 대한 해당 항목을 찾습니다.

| _Azure 모바일_ 명령 | 해당하는 _Azure 사이트_ 명령 |
| :----------------------------------------- | :----------------------------------------- |
| 모바일 위치 | 사이트 위치 목록 |
| 모바일 목록 | 사이트 목록 |
| 모바일 쇼 _이름_ | 사이트 쇼 _이름_ |
| 모바일 재시작 _이름_ | 사이트 재시작 _이름_ |
| 모바일 재배포 _이름_ | 사이트 배포 재배포 _commitId_ _이름_ |
| 모바일 키 집합 _이름_ _형식_ _값_ | 사이트 appsetting 삭제 _키_ _이름_ <br/> 사이트 appsetting 추가 _키_=_값_ _이름_ |
| 모바일 구성 목록 _이름_ | 사이트 appsetting 목록 _이름_ |
| 모바일 구성 가져오기 _이름_ _키_ | 사이트 appsetting 쇼 _키_ _이름_ |
| 모바일 구성 설정 _이름_ _키_ | 사이트 appsetting 삭제 _키_ _이름_ <br/> 사이트 appsetting 추가 _키_=_값_ _이름_ |
| 모바일 도메인 목록 _이름_ | 사이트 도메인 목록 _이름_ |
| 모바일 도메인 추가 _이름_ _도메인_ | 사이트 도메인 추가 _도메인_ _이름_ |
| 모바일 도메인 삭제 _이름_ | 사이트 도메인 삭제 _도메인_ _이름_ |
| 모바일 크기 조정 쇼 _이름_ | 사이트 쇼 _이름_ |
| 모바일 크기 조정 변경 _이름_ | 사이트 크기 조정 모드 _모드_ _이름_ <br /> 사이트 크기 조정 인스턴스 _인스턴스_ _이름_ |
| 모바일 appsetting 목록 _이름_ | 사이트 appsetting 목록 _이름_ |
| 모바일 appsetting 추가 _이름_ _키_ _값_ | 사이트 appsetting 추가 _키_=_값_ _이름_ |
| 모바일 appsetting 삭제 _이름_ _키_ | 사이트 appsetting 삭제 _키_ _이름_ |
| 모바일 appsetting 쇼 _이름_ _키_ | 사이트 appsetting 삭제 _키_ _이름_ |

적절한 응용 프로그램 설정을 업데이트하여 인증 또는 푸시 알림 설정을 업데이트합니다. ftp 또는 git를 통해 파일을 편집하고 사이트를 게시합니다.

### <a name="diagnostics"></a>진단 및 로깅

Azure 앱 서비스에서 정상적으로 진단 로깅을 사용합니다. 진단 로깅을 사용하려면:

  1. [Azure 포털]에 로그인합니다.
  2. **모든 리소스** 또는 **앱 서비스**를 선택한 후 마이그레이션된 모바일 서비스의 이름을 클릭합니다.
  3. 기본적으로 설정 블레이드가 열립니다. 열리지 않으면 **설정**을 클릭합니다.
  4. 기능 메뉴에서 **진단 로그**를 선택합니다.
  5. 다음 로그에서 **켜기**를 클릭합니다. **응용 프로그램 로깅(파일 시스템)**, **자세한 오류 메시지** 및 **실패한 요청 추적**
  6. 웹 서버 로깅에서 **파일 시스템**을 클릭합니다.
  7. **저장**을 클릭합니다.

로그를 보려면:

  1. [Azure 포털]에 로그인합니다.
  2. **모든 리소스** 또는 **앱 서비스**를 선택한 후 마이그레이션된 모바일 서비스의 이름을 클릭합니다.
  3. **도구** 단추를 클릭합니다.
  4. 관찰 메뉴에서 **로그 스트림**을 선택합니다.

로그는 생성될 때 제공된 창에 스트리밍합니다. 배포 자격 증명을 사용하여 나중에 분석에 대한 로그를 다운로드할 수 있습니다. 자세한 내용은 [로깅] 설명서를 참조하세요.

## <a name="known-issues"></a>알려진 문제

### 마이그레이션된 모바일 앱 복제를 삭제하면 사이트 중단이 발생함

Azure PowerShell을 사용하여 마이그레이션된 모바일 서비스를 복제하고 복제본을 삭제하면 프로덕션 서비스에 대한 DNS 항목이 제거됩니다. 결과적으로 사이트는 인터넷에서 더 이상 액세스할 수 없습니다.

해결 방법: 당사에서 이 문제에 대한 작업을 하고 있습니다. 사이트를 복제하려는 경우 포털을 통해 수행하세요.

### web.config을 변경할 수 없습니다.

ASP.NET 사이트가 있는 경우 `Web.config` 파일을 변경할 수 없습니다. Azure 앱 서비스는 시작하는 동안 적절한 `Web.config` 파일을 빌드하여 모바일 서비스 런타임을 지원합니다. XML 변환 파일을 사용하여 특정 설정(예: 사용자 지정 헤더)를 재정의할 수 있습니다. 호출된 `applicationHost.xdt`에 파일을 만듭니다. 이 파일은 Azure 서비스의 `D:\home\site` 디렉터리에서 종료되어야 합니다. 사용자 지정 배포 스크립트를 통하거나 Kudu를 사용하여 직접 수행할 수 있습니다. 예제 문서는 다음과 같습니다.

```
<?xml version="1.0" encoding="utf-8"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <system.webServer>
    <httpProtocol>
      <customHeaders>
        <add name="X-Frame-Options" value="DENY" xdt:Transform="Replace" />
        <remove name="X-Powered-By" xdt:Transform="Insert" />
      </customHeaders>
    </httpProtocol>
    <security>
      <requestFiltering removeServerHeader="true" xdt:Transform="SetAttributes(removeServerHeader)" />
    </security>
  </system.webServer>
</configuration>
```

자세한 내용은 GitHub에 대한 [XDT 변환 샘플] 설명서를 참조하세요.

### 마이그레이션된 모바일 서비스를 트래픽 관리자에 추가할 수 없습니다.

트래픽 관리자 프로필을 만들면 프로필에 마이그레이션된 모바일 서비스를 직접 선택할 수 없습니다. "외부 끝점"을 사용해야 합니다. PowerShell을 통해서만 외부 끝점을 추가할 수 있습니다. 자세한 내용은 [트래픽 관리자 자습서](https://azure.microsoft.com/blog/azure-traffic-manager-external-endpoints-and-weighted-round-robin-via-powershell/)를 참조합니다.

## <a name="next-steps"></a>다음 단계

응용 프로그램이 앱 서비스에 마이그레이션하지 않지만 더 많은 기능을 활용할 수 있습니다.

  * [스테이징 슬롯]을 배포하면 사이트에 대한 변경 내용을 준비하고 A/B 테스트를 수행할 수 있습니다.
  * [WebJobs]은 요청 시 예약된 작업을 대체합니다.
  * TFS, GitHub 또는 Mercurial에 사이트를 연결하여 사이트를 [지속적으로 배포]할 수 있습니다.
  * [Application Insights]를 사용하여 사이트를 모니터링할 수 있습니다.
  * 동일한 코드에서 웹 사이트 및 모바일 API를 제공합니다.

### <a name="upgrading-your-site"></a>Azure 모바일 앱 SDK에 모바일 서비스 사이트 업그레이드

  * Node.js 기반 서버 프로젝트의 경우 새로운 [모바일 앱 Node.js SDK]에서 다양한 새로운 기능을 제공합니다. 예를 들어 이제 로컬 개발 및 디버깅을 수행하고 0.10 이후의 모든 Node.js 버전을 사용할 수 있으며 원하는 Express.js 미들웨어로 사용자 지정할 수 있습니다.

  * .NET 기반 서버 프로젝트의 경우 새로운 [모바일 앱 SDK NuGet 패키지](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/)는 NuGet 종속성에 대한 유연성을 더 많이 제공하고, 새로운 앱 서비스 인증 기능을 지원하며, MVC를 비롯한 모든 ASP.NET 프로젝트를 사용하여 작성합니다. 업그레이드에 대한 자세한 내용은 [기존 .NET 모바일 서비스를 앱 서비스로 업그레이드](app-service-mobile-net-upgrading-from-mobile-services.md)를 참조하세요.

<!-- Images -->
[0]: ./media/app-service-mobile-migrating-from-mobile-services/migrate-to-app-service-button.PNG
[1]: ./media/app-service-mobile-migrating-from-mobile-services/migration-activity-monitor.png
[2]: ./media/app-service-mobile-migrating-from-mobile-services/triggering-job-with-postman.png

<!-- Links -->
[앱 서비스 가격]: https://azure.microsoft.com/pricing/details/app-service/
[Application Insights]: ../application-insights/app-insights-overview.md
[자동 크기 조정]: ../app-service-web/web-sites-scale.md
[Azure 앱 서비스]: ../app-service/app-service-value-prop-what-is.md
[Azure 앱 서비스 배포 설명서]: ../app-service-web/web-sites-deploy.md
[Azure 클래식 포털]: https://manage.windowsazure.com
[Azure 포털]: https://portal.azure.com
[Azure 지역]: https://azure.microsoft.com/regions/
[Azure 스케줄러 계획]: ../scheduler/scheduler-plans-billing.md
[지속적으로 배포]: ../app-service-web/app-service-continuous-deployment.md
[혼합 네임스페이스를 변환]: https://azure.microsoft.com/blog/updates-from-notification-hubs-independent-nuget-installation-model-pmt-and-more/
[curl]: http://curl.haxx.se/
[사용자 지정 도메인 이름]: ../app-service-web/web-sites-custom-domain-name.md
[Fiddler]: http://www.telerik.com/fiddler
[Azure 앱 서비스의 일반적인 가용성]: /blog/announcing-general-availability-of-app-service-mobile-apps/
[하이브리드 연결]: ../app-service-web/web-sites-hybrid-connection-get-started.md
[로깅]: ../app-service-web/web-sites-enable-diagnostic-log.md
[모바일 앱 Node.js SDK]: https://github.com/azure/azure-mobile-apps-node
[모바일 서비스 vs. 앱 서비스]: app-service-mobile-value-prop-migration-from-mobile-services.md
[알림 허브]: ../notification-hubs/notification-hubs-overview.md
[성능 모니터링]: ../app-service-web/web-sites-monitor.md
[Postman]: http://www.getpostman.com/
[모바일 서비스]: ../mobile-services/mobile-services-disaster-recovery.md
[스테이징 슬롯]: ../app-service-web/web-sites-staged-publishing.md
[VNet]: ../app-service-web/web-sites-integrate-with-vnet.md
[WebJobs]: ../app-service-web/websites-webjobs-resources.md
[XDT 변환 샘플]: https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples

<!---HONumber=AcomDC_0803_2016-->