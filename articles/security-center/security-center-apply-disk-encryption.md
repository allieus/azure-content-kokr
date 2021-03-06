<properties
   pageTitle="Azure 보안 센터에서 디스크 암호화 적용 | Microsoft Azure"
   description="이 문서에서는 보안 센터 권장 사항 **디스크 암호화 적용**을 구현하는 방법을 보여줍니다."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/29/2016"
   ms.author="terrylan"/>

# Azure 보안 센터에서 디스크 암호화 적용

Azure 보안 센터는 암호화되지 않은 Windows 또는 Linux VM 디스크가 있는 경우 Azure 디스크 암호화를 사용하여 디스크 암호화를 적용하도록 권장합니다. 디스크 암호화를 사용하면 Windows 및 Linux IaaS VM 디스크를 암호화할 수 있습니다. VM에서 OS 및 데이터 볼륨에 암호화를 사용하는 것이 좋습니다.


디스크 암호화는 OS 및 데이터 암호화를 제공하는 Windows의 업계 표준 [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) 기능 및 Linux의 [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) 기능을 활용하여 데이터를 보호하고 조직 보안 및 규정 준수 약정을 충족하는 데 도움이 됩니다. 디스크 암호화는 [Azure 주요 자격 증명 모음](https://azure.microsoft.com/documentation/services/key-vault/)과 함께 통합되어 주요 자격 증명 모음 구독에서 디스크 암호화 키 및 암호를 제어하고 관리할 수 있도록 하며 VM 디스크의 모든 휴지 상태 데이터가 [Azure 저장소](https://azure.microsoft.com/documentation/services/storage/)에서 암호화되도록 보장합니다.

> [AZURE.NOTE] Azure 디스크 암호화는 Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2와 같은 Windows Server 운영 체제에서 지원됩니다. 디스크 암호화는 Ubuntu, CentOS, SUSE 및 SLES(SUSE Linux Enterprise Server)와 같은 Linux Server 운영 체제에서 지원됩니다.

## 권장 사항 구현

1. **권장 사항** 블레이드에서 **디스크 암호화 적용**을 선택합니다.
2. **디스크 암호화 적용** 블레이드에서 디스크 암호화가 추천되는 VM의 목록이 표시됩니다.
3. 지시를 따라서 이러한 VM에 암호화를 적용합니다.

![][1]

보안 센터에 의해 암호화가 필요하다고 식별된 Azure 가상 컴퓨터를 암호화하려면 다음 단계를 권장합니다.

- Azure PowerShell을 설치하고 구성합니다. 이렇게 하면 Azure 가상 컴퓨터를 암호화하는 데 필요한 필수 구성 요소를 설정하는 데 필요한 PowerShell 명령을 실행할 수 있습니다.
- Azure 디스크 암호화 필수 구성 요소 Azure PowerShell 스크립트를 가져오고 실행합니다.
- 가상 컴퓨터 암호화

[Azure 가상 컴퓨터 암호화](security-center-disk-encryption.md)에서 이러한 단계를 안내합니다. 이 항목에서는 디스크 암호화를 구성할 클라이언트 컴퓨터로 Windows 10을 사용하고 있다고 가정합니다.

필수 구성 요소를 설치하고 Azure 가상 컴퓨터에 대한 암호화를 구성하는 데 사용될 수 있는 여러 가지 방법이 있습니다. 이미 Azure PowerShell 또는 Azure CLI에 대해 잘 알고 있다면 대체 방법을 사용하는 것을 선호할 수 있습니다. 이러한 대체 방법에 대한 자세한 내용은 [Azure 디스크 암호화](../security/azure-security-disk-encryption.md)를 참조하세요.



## 참고 항목

이 문서에서는 보안 센터 권장 사항 "디스크 암호화 적용"을 구현하는 방법을 보여주었습니다. 디스크 암호화에 대해 자세히 알아보려면 다음을 참조하세요.

- [Azure 주요 자격 증명으로 암호화 및 키 관리](https://azure.microsoft.com/documentation/videos/azurecon-2015-encryption-and-key-management-with-azure-key-vault/)(비디오, 36분 39초) -- IaaS VM 및 Azure 주요 자격 증명 모음에 디스크 암호화 관리를 사용하여 데이터를 세이프가드하는 방법 알아보기
- [Azure 가상 컴퓨터 암호화](security-center-disk-encryption.md)(문서) -- Azure 가상 컴퓨터를 암호화하는 방법 알아보기
- [Azure 디스크 암호화](../security/azure-security-disk-encryption.md)(문서) -- Windows 및 Linux VM에 디스크 암호화를 사용하도록 설정하는 방법 알아보기

보안 센터에 대한 자세한 내용은 다음을 참조하세요.

- [Azure 보안 센터에서 보안 정책 설정](security-center-policies.md) -- 보안 정책을 구성하는 방법 알아보기
- [Azure 보안 센터에서 보안 상태 모니터링](security-center-monitoring.md) -- Azure 리소스의 상태를 모니터링하는 방법 알아보기
- [Azure 보안 센터에서 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md) -- 보안 경고를 관리하고 대응하는 방법 알아보기
- [Azure 보안 센터에서 보안 권장 사항 관리](security-center-recommendations.md) -- 권장 사항이 Azure 리소스 보호에 어떤 도움이 되는지 알아보기
- [Azure 보안 센터 FAQ](security-center-faq.md) -- 서비스 사용에 관한 질문과 대답 찾기
- [Azure 보안 블로그](http://blogs.msdn.com/b/azuresecurity/) -- Azure 보안 및 규정 준수에 관한 블로그 게시물 찾기



<!--Image references-->
[1]: ./media/security-center-apply-disk-encryption/apply-disk-encryption.png

<!---HONumber=AcomDC_0803_2016-->