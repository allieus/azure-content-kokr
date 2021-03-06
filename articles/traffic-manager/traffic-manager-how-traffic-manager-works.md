<properties
   pageTitle="트래픽 관리자 작동 방식 | Microsoft Azure"
   description="이 문서는 Azure 트래픽 관리자 작동 방식을 이해하는 데 도움이 됩니다."
   services="traffic-manager"
   documentationCenter=""
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn"/>

<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/07/2016"
   ms.author="sewhee"/>

# 트래픽 관리자 작동 방식

Azure 트래픽 관리자를 사용하면 응용 프로그램 끝점에서 트래픽을 분산하는 방법을 제어할 수 있습니다. 끝점은 Azure에 호스트되거나 또는 Azure 외부에 있는 모든 인터넷 연결 끝점이 될 수 있습니다.

트래픽 관리자는 다음과 같은 두 가지 주요 이점을 제공합니다.

1. 여러 가지 [트래픽 라우팅 메서드](traffic-manager-routing-methods.md) 중 하나에 따라 트래픽 분산
2. [끝점 상태 연속 모니터링](traffic-manager-monitoring.md) 및 끝점이 실패할 경우 자동 장애 조치(failover)

최종 사용자가 서비스 끝점에 연결하려고 하는 경우 먼저 해당 클라이언트(PC, 휴대폰 등)가 해당 끝점의 DNS 이름을 IP 주소로 확인해야 합니다. 그런 다음 클라이언트는 해당 IP 주소에 연결하여 서비스에 액세스합니다.

**가장 중요한 사항은 트래픽 관리자가 DNS 수준에서 작동한다는 점입니다.** 트래픽 관리자는 DNS를 사용하여 최종 사용자를 선택된 트래픽 라우팅 메서드 및 현재 끝점 상태에 따라 특정 서비스 끝점으로 보냅니다. 그러면 클라이언트는 선택한 끝점에 **직접** 연결됩니다. 트래픽 관리자는 프록시가 아니며 클라이언트와 서비스 간에 전달되는 트래픽을 표시하지 않습니다.

## 트래픽 관리자 예제

Contoso Corp에서 새 파트너 포털을 개발했습니다. 이 포털에 대한 URL은 https://partners.contoso.com/login.aspx입니다. 응용 프로그램은 Azure에 호스트되며 가용성을 개선하고 전역 성능을 최대화하기 위해 전 세계 3개 지역에 응용 프로그램을 배포하고 트래픽 관리자를 사용하여 최종 사용자를 사용 가능한 가장 가까운 끝점에 분산하고자 합니다.

이 구성을 설정하려면

- 3개의 서비스 인스턴스를 배포합니다. 이러한 배포의 DNS 이름은 'contoso us.cloudapp.net', 'contoso eu.cloudapp.net' 및 'contoso asia.cloudapp.net'입니다.
- 그런 다음 위에 명시된 3개의 끝점에서 '성능' 트래픽 라우팅 메서드를 사용하도록 구성된 'contoso.trafficmanager.net'이라는 이름의 트래픽 관리자 프로필을 만듭니다.
- 마지막으로, DNS CNAME 레코드를 사용하여 'contoso.trafficmanager.net'을 가리키는 베니티 도메인 'partners.contoso.com'을 구성합니다.

![트래픽 관리자 DNS 구성][1]

> [AZURE.NOTE] Azure 트래픽 관리자에서 베니티 도메인을 사용할 때는 베니티 도메인 이름을 트래픽 관리자 도메인 이름으로 가리키는 CNAME을 사용해야 합니다.

> DNS 표준의 제한으로 인해 도메인의 '루트'에서 CNAME을 만들 수 없습니다. 따라서 'contoso.com'('naked' 도메인이라고도 함)에 대한 CNAME을 만들 수 없습니다. 'contoso.com'의 도메인(예: 'www.contoso.com')에 대해서만 CNAME을 만들 수 있습니다.

> 따라서 naked 도메인과 직접 트래픽 관리자를 사용할 수 없습니다. 이 문제를 해결하려면 간단한 HTTP 리디렉션을 사용하여 대체 이름(예: 'www.contoso.com')으로 'contoso.com'에 대한 요청을 보내는 것이 좋습니다.

## 트래픽 관리자를 사용하여 클라이언트를 연결하는 방법

위 예제에 설명된 대로 최종 사용자가 https://partners.contoso.com/login.aspx 페이지를 요청하는 경우 해당 클라이언트는 다음 단계를 수행하여 DNS 이름을 확인하고 연결합니다.

![트래픽 관리자를 사용하여 연결 설정][2]

1.	클라이언트(PC, 휴대폰 등)는 'partners.contoso.com'에 대한 DNS 쿼리를 구성된 재귀 DNS 서비스로 만듭니다. ('로컬 DNS' 서비스라고도 하는 재귀 DNS 서비스는 DNS 도메인을 직접 호스트하지 않습니다. 대신 DNS 이름을 확인하는 데 필요한 인터넷에서 권한 있는 다양한 DNS 서비스를 연결하는 작업을 오프로드하기 위해 클라이언트에서 사용합니다.
2.	이제 재귀 DNS 서비스가 'partners.contoso.com' DNS 이름을 확인합니다. 먼저 재귀 DNS 서비스는 'contoso.com' 도메인에 대한 이름 서버를 찾습니다. 그런 다음 이러한 이름 서버에 연결하여 'partners.contoso.com' DNS 레코드를 요청합니다. contoso.trafficmanager.net에 대한 CNAME이 반환됩니다.
3.	이제 재귀 DNS 서비스는 Azure 트래픽 관리자 서비스에서 제공하는 'trafficmanager.net' 도메인에 대한 이름 서버를 찾습니다. 이러한 이름 서버에 연결하여 'contoso.trafficmanager.net' DNS 레코드를 요청합니다.
4.	트래픽 관리자 이름 서버가 요청을 받습니다. 그런 다음, 다음 사항에 따라 어떤 끝점이 반환되어야 하는지 선택합니다. a. 각 끝점의 사용/사용 안 함 상태(사용하지 않는 끝점은 반환되지 않음) b. 트래픽 관리자 상태 검사에서 확인된 각 끝점의 현재 상태. 자세한 내용은 트래픽 관리자 끝점 모니터링을 참조하세요. c. 선택된 트래픽 라우팅 메서드. 자세한 내용은 트래픽 관리자 트래픽 라우팅 메서드를 참조하세요.
5.	선택한 끝점이 다른 DNS CNAME 레코드로 반환되며 이 경우 contoso-us.cloudapp.net이 반환된다고 가정합니다.
6.	이제 재귀 DNS 서비스가 'cloudapp.net' 도메인에 대한 이름 서버를 찾습니다. 이러한 이름 서버에 연결하여 'contoso-us.cloudapp.net' DNS 레코드를 요청합니다. 미국 기반 서비스 끝점의 IP 주소를 포함하는 DNS 'A' 레코드가 반환됩니다.
7.	재귀 DNS 서비스는 위 시퀀스의 클라이언트에 대한 이름 확인의 통합된 결과를 반환합니다.
8.	클라이언트는 재귀 DNS 서비스에서 DNS 결과를 받고 지정 IP 주소에 연결합니다. 트래픽 관리자를 통해서가 아니라 직접 응용 프로그램 서비스 끝점에 연결합니다. 해당 끝점은 HTTPS 끝점이므로 필요한 SSL/TLS 핸드셰이크를 수행한 다음 ‘/login.aspx’ 페이지에 대해 HTTP GET 요청을 합니다.

재귀 DNS 서비스는 최종 사용자의 장치에서 DNS 클라이언트처럼 수신하는 DNS 응답을 캐시합니다. 그러면 후속 DNS 쿼리는 다른 이름 서버를 쿼리하는 대신 캐시의 데이터를 사용하여 더 신속하게 답변을 받을 수 있습니다. 캐시의 기간은 각 DNS 레코드의 ‘TTL’(time-to-live) 속성에 의해 결정됩니다. 값이 짧을수록 캐시 만료가 더 빠르고 이에 따라 트래픽 관리자 이름 서버로의 왕복이 많아집니다. 값이 길수록 실패한 끝점에서 트래픽을 보내는 데 더 오래 걸릴 수 있음을 의미합니다. 트래픽 관리자를 사용하면 응용 프로그램의 요구를 가장 잘 분산하는 값을 선택할 수 있도록 트래픽 관리자 DNS 응답에 사용되는 TTL을 구성할 수 있습니다.

## FAQ

### 트래픽 관리자가 사용하는 IP 주소는 어떻게 되나요?

트래픽 관리자 작동 방식에서 설명했듯이 트래픽 관리자는 DNS 수준에서 작동합니다. 클라이언트를 적절한 서비스 끝점으로 보내기 위해 DNS 응답을 사용합니다. 그러면 클라이언트는 트래픽 관리자를 통해서가 아니라 직접 서비스 끝점에 연결합니다.

따라서 트래픽 관리자는 클라이언트가 연결할 끝점 또는 IP 주소를 제공하지 않습니다. 따라서 예를 들어 고정 IP 주소가 필요한 경우 트래픽 관리자에서가 아니라 서비스에서 구성해야 합니다.

### 트래픽 관리자는 '고정' 세션을 지원하나요?

[위](#how-clients-connect-using-traffic-manager)에 설명된 것처럼 트래픽 관리자는 DNS 수준에서 작동합니다. 클라이언트를 적절한 서비스 끝점으로 보내기 위해 DNS 응답을 사용합니다. 그러면 클라이언트는 트래픽 관리자를 통해서가 아니라 직접 서비스 끝점에 연결합니다. 따라서 트래픽 관리자는 쿠키를 비롯하여 클라이언트 및 서버 간 HTTP 트래픽을 표시하지 않습니다.

또한 트래픽 관리자에서 수신하는 DNS 쿼리의 원본 IP 주소는 클라이언트의 IP 주소가 아니라 재귀 DNS 서비스의 IP 주소입니다.

따라서 트래픽 관리자에서 개별 클라이언트를 식별 또는 추적할 방법이 없으므로 '고정' 세션을 구현할 수 없습니다. 이것은 모든 DNS 기반 트래픽 관리 시스템에 공통적으로 적용되며, 트래픽 관리자를 사용할 때로 국한되지 않습니다.

### 트래픽 관리자를 사용할 때 HTTP 오류가 나타나는 이유는 무엇인가요?

[위](#how-clients-connect-using-traffic-manager)에 설명된 것처럼 트래픽 관리자는 DNS 수준에서 작동합니다. 클라이언트를 적절한 서비스 끝점으로 보내기 위해 DNS 응답을 사용합니다. 그러면 클라이언트는 트래픽 관리자를 통해서가 아니라 직접 서비스 끝점에 연결합니다.

따라서 트래픽 관리자는 클라이언트 및 서버 간 HTTP 트래픽을 표시하지 않으며 HTTP 수준 오류를 생성할 수 없습니다. 표시된 모든 HTTP 오류는 응용 프로그램에서 가져온 것이어야 합니다. 클라이언트가 응용 프로그램에 연결되는 중이므로 트래픽 관리자의 역할을 포함한 DNS 확인이 완료되어야 합니다.

따라서 추가 조사는 응용 프로그램에 중점을 두어야 합니다.

트래픽 관리자를 사용할 때 일반적인 문제는 브라우저에서 응용 프로그램으로 전달된 'host' HTTP 헤더에 브라우저에 사용된 도메인 이름이 표시되는 것입니다. 테스트 중에 도메인 이름을 사용하는 경우 트래픽 관리자 도메인 이름(예: myprofile.trafficmanager.net)이거나 트래픽 관리자 도메인 이름을 가리키도록 구성된 베니티 도메인 CNAME일 수 있습니다. 어떤 경우든 이 host 헤더를 수락하도록 응용 프로그램이 구성되어 있는지 확인합니다..

응용 프로그램이 Azure 앱 서비스에서 호스팅되는 경우 [트래픽 관리자를 사용하는 Azure 앱 서비스의 웹앱에 대한 사용자 지정 도메인 이름 구성](../app-service-web/web-sites-traffic-manager-custom-domain-name.md)을 참조하세요.

### 트래픽 관리자를 사용할 때 성능 영향은 무엇인가요?

[위](#how-clients-connect-using-traffic-manager)에 설명된 것처럼 트래픽 관리자는 DNS 수준에서 작동합니다. 클라이언트를 적절한 서비스 끝점으로 보내기 위해 DNS 응답을 사용합니다. 그러면 클라이언트는 트래픽 관리자를 통해서가 아니라 직접 서비스 끝점에 연결합니다.

클라이언트가 서비스 끝점에 직접 연결되므로 연결이 설정된 후 트래픽 관리자를 사용하면 성능 영향이 발생하지 않습니다.

트래픽 관리자는 DNS 수준에서 응용 프로그램과 통합되므로 추가 DNS 조회를 을 DNS 확인 체인에 삽입해야 합니다([트래픽 관리자 예제](#traffic-manager-example) 참조). DNS 확인 시간에 대한 트래픽 관리자의 영향은 최소입니다. 트래픽 관리자는 이름 서버의 글로벌 네트워크를 사용하며 DNS 쿼리가 사용 가능한 가장 가까운 이름 서버로 라우팅되도록 애니캐스트 네트워킹을 사용합니다. 또한 DNS 응답의 캐싱은 트래픽 관리자를 사용하여 발생한 추가 DNS 대기 시간이 세션 중 일부에만 적용되는 것을 의미합니다.

최종적인 결론은 트래픽 관리자를 응용 프로그램에 통합하는 것과 관련한 전체 성능 영향이 최소여야 합니다.

또한 트래픽 관리자의 ['성능' 트래픽 라우팅 메서드](traffic-manager-routing-methods.md#performance-traffic-routing-method)가 사용되는 경우 DNS 대시 시간의 증가는 최종 사용자를 사용 가능한 가장 가까운 끝점에 라우팅하여 달성할 수 있는 성능 향상 오프셋보다 훨씬 많습니다.

### 트래픽 관리자에는 어떤 응용 프로그램 프로토콜을 사용할 수 있나요?
[위](#how-clients-connect-using-traffic-manager)에 설명된 것처럼 트래픽 관리자는 DNS 수준에서 작동합니다. DNS 조회가 완료되면 클라이언트는 트래픽 관리자를 통해서가 아닌 응용 프로그램 끝점에 직접 연결됩니다. 따라서 이 연결에서 모든 응용 프로그램 프로토콜을 사용할 수 있습니다.

그러나 트래픽 관리자의 끝점 상태 검사에는 HTTP 또는 HTTPS 끝점이 필요합니다. 이 끝점은 트래픽 관리자 프로필 상태 검사 설정에 다른 TCP 포트 또는 URI 경로를 지정하여 클라이언트에서 연결되는 응용 프로그램 끝점과 구분될 수 있습니다.

### 'Naked'(www 없는) 도메인 이름으로 트래픽 관리자를 사용할 수 있나요?

현재는 아닙니다.

한 DNS 이름에서 다른 이름으로 매핑을 만드는 데 DNS CNAME 레코드 유형을 사용합니다. [트래픽 관리자 예제](#traffic-manager-example)에 설명된 대로 트래픽 관리자에는 베니티 DNS 이름(예: www.contoso.com)을 트래픽 관리자 프로필 DNS 이름(예: contoso.trafficmanager.net)에 매핑할 DNS CNAME 레코드가 필요합니다. 또한 트래픽 관리자 프로필 자체에서 클라이언트가 연결할 끝점을 나타내는 보조 DNS CNAME을 반환합니다.

DNS 표준은 CNAME이 동일한 형식의 다른 DNS 레코드와 함께 존재하는 것을 허용하지 않습니다. DNS 영역의 루트에는 항상 두 개의 기존 DNS 레코드(SOA 및 권한 있는 NS 레코드)가 있으므로 DNS 표준을 위반하지 않고 루트에 CNAME 레코드를 만들 수 없습니다.

이 문제를 해결하려면 트래픽 관리자를 사용하려는 naked(www 없는) 도메인을 사용하는 서비스가 HTTP 리디렉션을 사용하여 naked 도메인에서 다른 URL로 트래픽을 보내야 하며 그런 다음 트래픽 관리자를 사용할 수 있습니다. 예를 들어 naked 도메인 'contoso.com'에서 사용자를 'www.contoso.com'으로 리디렉션한 후 트래픽 관리자를 사용할 수 있습니다.

트래픽 관리자에서 naked 도메인에 대한 전체 지원은 기능 백로그에서 추적됩니다. 이 기능에 관심이 있는 경우 [커뮤니티 피드백 사이트에서 투표](https://feedback.azure.com/forums/217313-networking/suggestions/5485350-support-apex-naked-domains-more-seamlessly)하여 지원에 등록해 주세요.

## 다음 단계

트래픽 관리자 [끝점 모니터링 및 자동 장애 조치(failover)](traffic-manager-monitoring.md)에 대해 자세히 알아봅니다.

트래픽 관리자 [트래픽 라우팅 방법](traffic-manager-routing-methods.md)에 대해 자세히 알아봅니다.

<!--Image references-->
[1]: ./media/traffic-manager-how-traffic-manager-works/dns-configuration.png
[2]: ./media/traffic-manager-how-traffic-manager-works/flow.png

<!---HONumber=AcomDC_0824_2016-->