<properties
   pageTitle="Azure SQL 데이터 웨어하우스 복원(REST API) | Microsoft Azure"
   description="Azure SQL 데이터 웨어하우스 복원을 위한 REST API 작업."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="Lakshmi1812"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/18/2016"
   ms.author="lakshmir;barbkess;sonyama"/>

# Azure SQL 데이터 웨어하우스 복원(REST API)

> [AZURE.SELECTOR]
- [개요][]
- [포털][]
- [PowerShell][]
- [REST (영문)][]

이 문서에서는 REST API를 사용하여 Azure SQL 데이터 웨어하우스를 복원하는 방법을 배웁니다.

## 시작하기 전에

**DTU 용량을 확인합니다.** 각 SQL 데이터 웨어하우스는 기본 DTU 할당량이 있는 SQL server (예: myserver.database.windows.net)에 의해 호스팅됩니다. SQL 데이터 웨어하우스를 복원하기 전에 SQL 서버에 복원 중인 데이터베이스에 대해 충분한 DTU 할당량이 남아 있는지 확인합니다. 필요한 DTU를 계산하거나 더 많은 DTU를 요청하는 방법을 알아보려면 [DTU 할당량 변경 요청][]을 참조합니다.

## 활성 또는 일시 중지된 데이터베이스 복원

데이터베이스를 복원하려면

1. 데이터베이스 복원 지점 가져오기 작업을 사용하여 데이터베이스 복원 지점 목록을 가져옵니다.
2. [데이터베이스 복원 요청 만들기][] 작업을 사용하여 복원을 시작합니다.
3. [데이터베이스 작업 상태][] 작업을 사용하여 복원 상태를 추적합니다.

>[AZURE.NOTE] 복원이 완료된 후 [복구된 데이터베이스 마무리][] 가이드에 따라 복구된 데이터베이스를 구성할 수 있습니다.

## 삭제된 데이터베이스 복원

삭제된 데이터베이스를 복원하려면:

1.	[복원 가능한 삭제된 데이터베이스 나열][] 작업을 사용하여 복원 가능한 모든 삭제된 데이터베이스를 나열합니다.
2.	[복원 가능한 삭제된 데이터베이스 가져오기][] 작업을 사용하여 복원하려는 삭제된 데이터베이스에 대한 세부 정보를 가져옵니다.
3.	[데이터베이스 복원 요청 만들기][] 작업을 사용하여 복원을 시작합니다.
4.	[데이터베이스 작업 상태][] 작업을 사용하여 복원 상태를 추적합니다.

>[AZURE.NOTE] 복원이 완료된 후 [복구된 데이터베이스 마무리][] 가이드에 따라 복구된 데이터베이스를 구성할 수 있습니다.


## 다음 단계
Azure SQL 데이터베이스 버전의 무중단 업무 방식 기능에 대해 알아보려면 [Azure SQL 데이터베이스 무중단 업무 방식 개요][]를 읽으세요.

<!--Image references-->

<!--Article references-->
[Azure SQL 데이터베이스 무중단 업무 방식 개요]: ./sql-database-business-continuity.md
[DTU 할당량 변경 요청]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change
[복구된 데이터베이스 마무리]: ./sql-database-recovered-finalize.md
[How to install and configure Azure PowerShell]: ./powershell-install-configure.md
[개요]: ./sql-data-warehouse-restore-database-overview.md
[포털]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST (영문)]: ./sql-data-warehouse-restore-database-rest-api.md

<!--MSDN references-->
[데이터베이스 복원 요청 만들기]: https://msdn.microsoft.com/library/azure/dn509571.aspx
[데이터베이스 작업 상태]: https://msdn.microsoft.com/library/azure/dn720371.aspx
[복원 가능한 삭제된 데이터베이스 가져오기]: https://msdn.microsoft.com/library/azure/dn509574.aspx
[복원 가능한 삭제된 데이터베이스 나열]: https://msdn.microsoft.com/library/azure/dn509562.aspx
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps

<!---HONumber=AcomDC_0824_2016-->