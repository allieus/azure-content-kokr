<properties
	pageTitle="404 상태를 반환하는 Azure CDN 끝점 문제 해결 | Microsoft Azure"
	description="Azure CDN 끝점에서 404 응답 코드 문제를 해결합니다."
	services="cdn"
	documentationCenter=""
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/28/2016"
	ms.author="casoper"/>
    
# 404 상태를 반환하는 CDN 끝점 문제 해결

이 문서는 404 오류를 반환하는 [CDN 끝점](cdn-create-new-endpoint.md) 관련 문제를 해결하는 데 도움이 됩니다.

이 문서의 어디에서든 도움이 필요한 경우 [MSDN Azure 및 스택 오버플로 포럼](https://azure.microsoft.com/support/forums/)에서 Azure 전문가에게 문의할 수 있습니다. 또는 Azure 기술 지원 인시던트를 제출할 수도 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/)로 이동한 다음 **지원 받기**를 클릭합니다.

## 증상

CDN 프로필 및 끝점을 만들었지만 콘텐츠를 CDN에서 사용할 수 없습니다. CDN URL을 통해 콘텐츠에 액세스하려는 사용자가 HTTP 404 상태 코드를 수신합니다.

## 원인

몇 가지 가능한 원인은 다음과 같습니다.

- 파일 원본이 CDN에 표시되지 않음
- 끝점이 잘못 구성되어 CDN이 엉뚱한 위치에 보임
- 호스트가 CDN의 호스트 헤더를 거부
- 끝점이 CDN 전체에 전파할 시간이 부족했음

## 문제 해결 단계

> [AZURE.IMPORTANT] CDN 끝점을 만든 후 등록이 CDN 네트워크 전체에 전파되는 데 시간이 걸리기 때문에 끝점을 즉시 사용할 수는 없습니다. <b>Akamai의 Azure CDN</b> 프로필의 경우, 일반적으로 1분 이내에 전파가 완료됩니다. <b>Verizon의 Azure CDN</b> 프로필의 경우 일반적으로 90분 이내에 전파가 완료되지만 더 오래 소요될 수도 있습니다. 이 문서의 단계를 완료한 후에도 여전히 404 응답이 반환되면 몇 시간 정보 기다렸다가 다시 확인한 후 지원 티켓을 열어 보세요.

### 원본 파일 확인

먼저, 원하는 캐시된 파일이 원본에서 사용할 수 있으며 공개적으로 액세스할 수 있는지 확인해야 합니다. 이 작업을 수행하는 가장 빠른 방법은 In-Private 또는 Incognito 세션에서 브라우저를 열고 해당 파일을 직접 찾아보는 것입니다. 주소 상자에 URL을 직접 입력하거나 붙여넣고 기대하는 파일이 나오는지 확인합니다. 이 예에서는 Azure 저장소 계정에 있는 파일을 사용하겠습니다. `https://cdndocdemo.blob.core.windows.net/publicblob/lorem.txt`에서 액세스할 수 있습니다. 보시다시피 성공적으로 테스트를 통과합니다.

![성공!](./media/cdn-troubleshoot-endpoint/cdn-origin-file.png)

> [AZURE.WARNING] 이 방법은 파일이 공개적으로 사용 가능한지 확인하는 가장 빠르고 쉬운 방법이지만 조직의 일부 네트워크 구성에 따라 사실은 이 파일이 네트워크 사용자에게만 보이는(심지어 Azure에 호스팅되는) 경우에도 공개적으로 사용 가능한 것으로 착각할 수 있습니다. 조직의 네트워크에 연결되지 않은 모바일 장치나 Azure의 가상 컴퓨터처럼 테스트 가능한 외부 브라우저를 사용하는 것이 가장 좋습니다.

### 원본 설정 확인

파일이 인터넷에서 공개적으로 사용할 수 있는 것을 확인했으니, 이제 원본 설정을 확인해야 합니다. [Azure 포털](https://portal.azure.com)에서 CDN 프로필을 찾아서 문제를 해결하려는 끝점을 클릭합니다. 그 결과로 표시되는 **끝점** 블레이드에서 원본을 클릭합니다.

![원본이 강조 표시된 끝점 블레이드](./media/cdn-troubleshoot-endpoint/cdn-endpoint.png)

**원본** 블레이드가 나타납니다.

![원본 블레이드](./media/cdn-troubleshoot-endpoint/cdn-origin-settings.png)

#### 원본 유형 및 호스트 이름

**원본 유형**이 올바른지 확인하고 **원본 호스트 이름**을 확인합니다. 이 예의 `https://cdndocdemo.blob.core.windows.net/publicblob/lorem.txt`에서 URL의 호스트 이름 부분은 `cdndocdemo.blob.core.windows.net`입니다. 스크린샷에서 보시는 것처럼 올바른 유형입니다. Azure 저장소, 웹앱 및 클라우드 서비스 원본의 경우 **원본 호스트 이름** 필드가 드롭다운이므로 철자를 신경 쓸 필요가 없습니다. 그러나 사용자 지정 원본을 사용하는 경우 *반드시* 호스트 이름 철자를 올바르게 입력해야 합니다.

#### HTTP 및 HTTPS 포트

여기서 확인할 또 다른 사항은 **HTTP** 및 **HTTPS 포트**입니다. 대부분의 경우 80 및 443이 올바르며, 포트를 변경할 필요가 없습니다. 그러나 원본 서버가 다른 포트에서 수신하는 경우 해당 사실을 여기에 나타내야 합니다. 확실하지 않은 경우 원본 파일의 URL을 살펴보기만 하세요. HTTP 및 HTTPS 사양은 포트 80 및 443을 기본값으로 지정합니다. 이 예의 URL `https://cdndocdemo.blob.core.windows.net/publicblob/lorem.txt`에서는 포트가 지정되지 않았기 때문에 기본값이 443인 것으로 가정하며 설정이 올바릅니다.

하지만 앞에서 테스트한 원본 파일의 URL이 `http://www.contoso.com:8080/file.txt`라고 가정해 봅시다. 호스트 이름 세그먼트의 끝부분에 있는 `:8080`에 주목하세요. 브라우저에 `8080` 포트를 사용하여 `www.contoso.com`의 웹 서버에 연결하라고 지시하는 것이므로 **HTTP 포트** 필드에 8080을 입력해야 합니다. 이러한 포트 설정은 끝점이 원본에서 정보를 검색할 때 사용하는 포트에만 영향을 줍니다.

> [AZURE.NOTE] **Akamai의 Azure CDN** 끝점에서는 원본에 대해 전체 TCP 포트 범위를 허용하지 않습니다. 허용되지 않는 원본 포트 목록을 보려면 [Akamai 허용된 원본 포트의 Azure CDN](https://msdn.microsoft.com/library/mt757337.aspx)을 참조하세요.
  
### 끝점 설정 검사

**끝점** 블레이드로 돌아가서 **구성** 단추를 클릭합니다.

![구성 단추가 강조 표시된 끝점 블레이드](./media/cdn-troubleshoot-endpoint/cdn-endpoint-configure-button.png)

끝점의 **구성** 블레이드가 나타납니다.

![구성 블레이드](./media/cdn-troubleshoot-endpoint/cdn-configure.png)

#### 프로토콜

**프로토콜**의 경우 클라이언트에서 사용하는 프로토콜이 선택되었는지 확인합니다. 클라이언트에서 사용한 것과 동일한 프로토콜이 원본 액세스에 사용됩니다. 따라서 이전 섹션에서 원본 포트를 올바르게 구성하는 것이 중요합니다. 끝점은 원본 포트에 관계없이 기본 HTTP 및 HTTPS 포트(80 및 443)에서만 수신합니다.

가상의 예 `http://www.contoso.com:8080/file.txt`로 돌아가 봅시다. 기억하시겠지만 Contoso는 HTTP 포트로 `8080`을 지정했습니다. 거기에 덧붙여 HTTPS 포트로 `44300`을 지정했다고 가정해 봅시다. `contoso`라는 끝점을 만들었다면 CDN 끝점 호스트 이름은 `contoso.azureedge.net`입니다. `http://contoso.azureedge.net/file.txt`에 대한 요청은 HTTP 요청이므로 끝점이 포트 8080에서 HTTP를 사용하여 원본에서 파일을 검색합니다. HTTPS 통한 보안 요청 `https://contoso.azureedge.net/file.txt`는 끝점이 포트 44300에서 HTTPS를 사용하여 원본에서 파일을 검색하게 만듭니다.

#### 원본 호스트 헤더

**원본 호스트 헤더**는 각 요청과 함께 원본으로 전송되는 호스트 헤더 값입니다. 대부분의 경우 앞에서 확인한 **원본 호스트 이름**과 같아야 합니다. 이 필드의 값이 잘못되어도 일반적으로 404 상태가 발생하지 않지만 원본이 예상하는 것이 무엇인지에 따라 다른 4xx 상태가 발생할 가능성이 있습니다.

#### 원본 경로

마지막으로 **원본 경로**를 확인해야 합니다. 기본적으로 원본 경로는 비어 있습니다. CDN에서 사용 가능하게 만들려는 원본 호스트 리소스의 범위를 좁히려는 경우에만 이 필드를 사용해야 합니다.

예를 들어 제가 예로 든 끝점에서는 저장소 계정의 모든 리소스를 사용 가능하게 만들기 위해 **원본 경로**를 비워 두었습니다. 다시 말해서 `https://cdndocdemo.azureedge.net/publicblob/lorem.txt`에 대한 요청은 끝점에서 `/publicblob/lorem.txt`를 요청하는 `cdndocdemo.core.windows.net`으로의 연결이 됩니다. 이와 마찬가지로, `https://cdndocdemo.azureedge.net/donotcache/status.png`에 대한 요청은 원본의 `/donotcache/status.png`를 요청하는 끝점이 됩니다.

그러나 원본의 모든 경로에 CDN을 사용하지 않으려면 어떻게 해야 할까요? `publicblob` 경로만 노출하려 한다고 가정해 봅시다. **원본 경로** 필드에 */publicblob*를 입력하면 끝점이 원본에 대해 만들어지는 모든 요청 앞에 */publicblob*를 삽입하게 됩니다. 다시 말해서 `https://cdndocdemo.azureedge.net/publicblob/lorem.txt`에 대한 요청이 이제 URL의 요청 부분인 `/publicblob/lorem.txt`를 가져와서 앞부분에 `/publicblob`를 추가합니다. 이것은 원본의 `/publicblob/publicblob/lorem.txt`에 대한 요청이 됩니다. 해당 경로가 실제 파일을 확인하지 못하면 원본이 404 상태를 반환합니다. 이 예에서 lorem.txt를 검색하는 올바른 URL은 `https://cdndocdemo.azureedge.net/lorem.txt`입니다. 이 예에서는 */publicblob* 경로를 전혀 포함하지 않았습니다. 왜냐하면 URL의 요청 부분이 `/lorem.txt`인데 끝점이 `/publicblob`를 추가하면 `/publicblob/lorem.txt`가 되어, 원본에 전달되는 요청이 되기 때문입니다.

<!---HONumber=AcomDC_0803_2016-->