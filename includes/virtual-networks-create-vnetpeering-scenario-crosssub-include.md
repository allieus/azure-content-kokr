## 구독간 피어링

이 시나리오에서는 다른 구독에 속한 두 개의 Vnet 간에 피어링을 만듭니다.

![하위 시나리오간](./media/virtual-networks-create-vnetpeering-scenario-crosssub-include/figure01.PNG)

VNet 피어링은 권한 부여를 위해 RBAC\(역할 기반 액세스 제어\)가 필요합니다. 구독 간 시나리오에서는 먼저 피어링 링크를 만들 사용자에게 충분한 권한을 부여해야 합니다. 참고: 동일한 사용자가 두 구독에 대해 권한을 가진 경우 아래의 1-4 단계를 건너뛸 수 있습니다.

<!---HONumber=AcomDC_0803_2016-->