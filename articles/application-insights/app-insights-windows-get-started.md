<properties
	pageTitle="Windows Phone 및 스토어 앱에 대한 분석 | Microsoft Azure"
	description="Windows 장치 앱의 사용량 및 작동 중단을 분석합니다."
	services="application-insights"
    documentationCenter="windows"
	authors="alancameronwills"
	manager="douge"/>

<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="08/26/2016"
	ms.author="awills"/>

# Windows Phone 및 스토어 앱에 대한 분석

Microsoft는 장치 devOps에 대해 클라이언트측 분석을 위한 [HockeyApp](http://hockeyapp.net/)과 서버측을 위한 [Application Insights](app-insights-overview.md)라는 두 가지 솔루션을 제공합니다.

[HockeyApp](http://hockeyapp.net/)은 iOS, OS X, Android 또는 Windows 장치 앱 뿐만 아니라 Xamarin, Cordova, Unity에 기반하는 플랫폼 간 앱에 대한 Mobile DevOps 솔루션일 입니다. 이를 통해 빌드를 베타 테스터에게 배포하고, 충돌 데이터를 수집하고, 사용자 메트릭 및 피드백을 얻을 수 있습니다. Visual Studio Team Services와 통합되어 있기 때문에 손쉬운 빌드 배포 및 작업 항목 통합이 가능합니다.

## HockeyApp 시작

다음으로 이동합니다.

* [HockeyApp](http://support.hockeyapp.net/kb)
* [Windows용 HockeyApp](http://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone)
* [HockeyApp 블로그](http://hockeyapp.net/blog/)
* [Hockeyapp Preseason](http://hockeyapp.net/preseason/)을 조인하여 초기 릴리스를 얻습니다.

앱에 서버측이 있으면, [Application Insights](app-insights-overview.md)를 사용하여 [ASP.NET](app-insights-asp-net.md) 또는 [J2EE](app-insights-java-get-started.md)에서 앱의 웹 서버측을 모니터링합니다.


[Windows 데스크톱 앱용 Application Insights](app-insights-windows-desktop.md)를 사용할 수도 있습니다.

## HockeyApp 데이터에 대한 분석, 내보내기 및 API 액세스 

Application Insights에서 [HockeyApp 브리지 설정](app-insights-hockeyapp-bridge-app.md). 다음을 수행할 수 있습니다.

* 원격 분석에 강력한 [분석](app-insights-analytics.md) 쿼리 언어 사용.
* Azure Blob 저장소에 [원격 분석 내보내기](app-insights-export-telemetry.md).

## 다음 단계

* [Windows용 HockeyApp 시작](http://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone)

<!---HONumber=AcomDC_0831_2016-->