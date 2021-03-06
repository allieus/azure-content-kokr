<properties
	pageTitle="Azure Active Directory B2C: 개요 | Microsoft Azure"
	description="Azure Active Directory B2C를 사용하여 소비자 지향 응용 프로그램 개발"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="bryanla"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="07/24/2016"
	ms.author="swkrish"/>

# Azure Active Directory B2C: 응용 프로그램에 소비자 등록 및 로그인

Azure Active Directory B2C는 소비자 지향 웹 및 모바일 응용 프로그램을 위한 포괄적인 클라우드 ID 관리 솔루션입니다. 수억 개의 소비자 ID로 확장되는 고가용성 글로벌 서비스입니다. 엔터프라이즈급 보안 플랫폼에 기반하여 Azure Active Directory B2C는 응용 프로그램, 비즈니스 및 소비자를 보호합니다.

이전에는 고객을 응용 프로그램에 가입 및 로그인하게 하고자 했던 응용 프로그램 개발자들이 자체 코드를 작성했습니다. 또한 온-프레미스 데이터베이스나 시스템을 사용하여 사용자 이름과 암호를 저장했습니다. Azure Active Directory B2C는 개발자에게 안전한 표준 기반 플랫폼 및 확장할 수 있는 정책의 다양한 집합으로 소비자 ID 관리를 응용 프로그램에 통합하는 더 나은 방법을 제공합니다. Azure Active Directory B2C를 사용하면 소비자는 기존 소셜 계정(Facebook, Google, Amazon, LinkedIn)을 사용하거나 새 자격 증명(전자 메일 주소 및 암호 또는 사용자 이름 및 암호)을 만들어서 응용 프로그램을 등록할 수 있습니다. 후자를 "로컬 계정"이라고 합니다.

## 시작

소비자 등록 및 로그인을 수락하는 응용 프로그램을 만들려면 먼저 Azure Active Directory B2C 테넌트를 사용하여 해당 응용 프로그램을 등록해야 합니다. [Azure AD B2C 테넌트 만들기](active-directory-b2c-get-started.md)에 요약한 단계를 사용하여 자신의 테넌트를 가져옵니다.

프로토콜 메시지를 직접 보내도록 선택하거나 [OAuth 2.0](active-directory-b2c-reference-protocols.md#oauth2-authorization-code-flow) 또는 [Open ID 연결](active-directory-b2c-reference-protocols.md#openid-connect-sign-in-flow)을 사용하여 또는 작업을 수행하는 라이브러리를 사용하여 Azure Active Directory B2C 서비스에 대해 응용 프로그램을 작성할 수 있습니다. 다음 테이블에서 원하는 플랫폼을 선택하고 시작합니다.

[AZURE.INCLUDE [active-directory-b2c-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]

## 새로운 기능

여기를 다시 종종 확인하여 Azure Active Directory B2C에 대한 이후 변경 내용을 알아봅니다. 또한 @AzureAD를 사용하여 업데이트에 대해 트윗합니다.

- [확장할 수 있는 정책 프레임워크](active-directory-b2c-reference-policies.md) 및 응용 프로그램에서 만들고 사용할 수 있는 정책 형식에 대해 알아봅니다.
- 사소한 서비스 문제, 업데이트, 상태 및 완화에서 알림에 대한 [서비스 블로그](https://blogs.msdn.microsoft.com/azureadb2c/)에 책갈피를 설정합니다. [Azure 상태 대시보드](https://azure.microsoft.com/status/)도 계속 모니터링합니다.
- 현재 [서비스 제한, 제한 사항 및 제약 조건](active-directory-b2c-limitations.md)입니다.
- 마지막으로 Azure AD B2C 및 ASP.NET Core를 사용하는 [코드 샘플](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-b2c)입니다.

## 방법 문서

특정 Azure Active Directory B2C 기능을 사용하는 방법을 알아봅니다.

- 소비자 지향 응용 프로그램에서 사용하기 위해 [Facebook](active-directory-b2c-setup-fb-app.md), [Google +](active-directory-b2c-setup-goog-app.md), [Microsoft 계정](active-directory-b2c-setup-msa-app.md), [Amazon](active-directory-b2c-setup-amzn-app.md) 및 [LinkedIn](active-directory-b2c-setup-li-app.md) 계정을 구성합니다.
- [사용자 지정 특성을 사용하여 소비자에 대한 정보를 수집합니다.](active-directory-b2c-reference-custom-attr.md)
- [소비자 지향 응용 프로그램에서 Azure Multi-Factor Authentication을 사용합니다](active-directory-b2c-reference-mfa.md).
- [소비자를 위해 셀프 서비스 암호 재설정을 설정합니다](active-directory-b2c-reference-sspr.md).
- Azure Active Directory B2C에서 제공하는 [등록, 로그인 및 다른 소비자 지향 페이지의 모양과 느낌을 사용자 지정](active-directory-b2c-reference-ui-customization.md)합니다.
- Azure Active Directory B2C 테넌트에서 [Azure Active Directory Graph API를 사용하여 프로그래밍 방식으로 소비자를 만들고 읽고 업데이트 및 삭제](active-directory-b2c-devquickstarts-graph-dotnet.md)합니다.

## 다음 단계

이러한 링크는 서비스를 자세히 탐색하는 데 유용합니다.

- [Azure Active Directory B2C 가격 책정 정보](https://azure.microsoft.com/pricing/details/active-directory-b2c/)를 참조하세요.
- [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) 또는 [adal](http://stackoverflow.com/questions/tagged/adal) 태그를 사용하여 스택 오버플로에 대한 도움말을 봅니다.
- [사용자 의견](https://feedback.azure.com/forums/169401-azure-active-directory/)을 사용하여 의견을 보내주세요. 사용자의 의견을 듣고 싶습니다! 게시물의 제목에 "AzureADB2C:" 구문을 사용해야 찾을 수 있습니다.
- [Azure AD B2C 프로토콜 참조](active-directory-b2c-reference-protocols.md)를 검토합니다.
- [Azure AD B2C 토큰 참조](active-directory-b2c-reference-tokens.md)를 검토합니다.
- [Azure Active Directory B2C FAQ](active-directory-b2c-faqs.md)를 참고하세요.
- [Azure Active Directory B2C에 대한 파일 지원 요청](active-directory-b2c-support.md)

## 당사 제품에 대한 보안 업데이트 가져오기

[이 페이지](https://technet.microsoft.com/security/dd252948)를 방문해서 보안 공지 경고를 구독하여 보안 인시던트가 발생할 때 알림을 받는 것이 좋습니다.

<!---HONumber=AcomDC_0727_2016-->