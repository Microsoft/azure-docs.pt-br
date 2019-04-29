---
title: Reter endereços IP durante o failover de VMs do Azure com o Azure Site Recovery | Microsoft Docs
description: Descreve como reter endereços IP ao falhar nas VMs do Azure para recuperação de desastres em uma região secundária com o Azure Site Recovery
ms.service: site-recovery
ms.date: 4/9/2019
author: mayurigupta13
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 618d60417aa6b582eaef94bf75dcf16c74750f83
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61277170"
---
# <a name="retain-ip-addresses-during-failover"></a>Reter endereços IP durante o failover

O [Azure Site Recovery](site-recovery-overview.md) habilita a recuperação de desastre para VMs do Azure replicando as VMs em outra região do Azure, efetuando failover se ocorrer uma interrupção e realizando o failback para a região primária quando as coisas voltam ao normal.

Durante o failover, talvez você queira manter os endereços IP da região de destino idênticos aos da região de origem:

- Por padrão, quando você habilita a recuperação de desastre para VMs do Azure, o Site Recovery cria recursos de destino com base nas configurações do recurso de origem. Para VMs do Azure configuradas com endereços IP estáticos, o Site Recovery tentará provisionar o mesmo endereço IP para a VM de destino se ele não estiver em uso. Para ver uma explicação completa de como o Site Recovery trata o endereçamento, [examine este artigo](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms).
- Para aplicativos simples, a configuração padrão é suficiente. Para aplicativos mais complexos, talvez você precise provisionar recursos adicionais para certificar-se de que a conectividade funcione conforme o esperado após o failover.


Este artigo fornece alguns exemplos de como reter os endereços IP em cenários de exemplo mais complexos. Os exemplos incluem:

- Failover para uma empresa com todos os recursos em execução no Azure
- Failover para uma empresa com implantação híbrida e com recursos em execução local e no Azure

## <a name="resources-in-azure-full-failover"></a>Recursos no Azure: failover completo

A Empresa A tem todos os aplicativos em execução no Azure.

### <a name="before-failover"></a>Antes do failover

Esta é a arquitetura antes do failover.

- A Empresa A tem redes e sub-redes idênticas nas regiões de origem e de destino do Azure.
- Para reduzir o RTO (objetivo de tempo de recuperação), a empresa usa nós de réplica para o SQL Server Always On, para os controladores de domínio etc. Esses nós de réplica ficam em uma rede virtual diferente na região de destino, para que possam estabelecer a conectividade site a site por VPN entre as regiões de origem e de destino. Isso não é possível quando o mesmo espaço de endereços IP é usado na origem e no destino.  
- Antes do failover, a arquitetura de rede é a seguinte:
    - A região primária é o Azure na Ásia Oriental
        - A Ásia Oriental tem uma VNet (**VNet de Origem**) com o espaço de endereço 10.1.0.0/16.
        - A Ásia Oriental tem cargas de trabalho divididas em três sub-redes na rede virtual:
            - **Sub-rede 1**: 10.1.1.0/24
            - **Sub-rede 2**: 10.1.2.0/24,
            - **Sub-rede 3**: 10.1.3.0/24
    - A região secundária (de destino) é o Sudeste Asiático do Azure
        - O Sudeste Asiático tem uma VNet de recuperação (**VNet de Recuperação**) idêntica à **VNet de Origem**.
        - O Sudeste Asiático tem uma VNet adicional (**VNet do Azure**) com o espaço de endereço 10.2.0.0/16.
        - A **VNet do Azure** contém uma sub-rede (**Sub-rede 4**) com o espaço de endereço 10.2.4.0/24.
        - Nós de réplica para o SQL Server Always On, controlador de domínio etc. estão localizados em **subrede 4**.
    - A **VNet de origem** e a **VNet do Azure** estão conectadas por meio de uma conexão site a site via VPN.
    - A **VNet de recuperação** não é conectada a nenhuma outra rede virtual.
    - A **Empresa A** atribui/verifica os endereços IP de destino para itens replicados. O IP de destino é o mesmo que o IP de origem para cada VM.

![Recursos no Azure antes do failover completo](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-before-failover2.png)

### <a name="after-failover"></a>Após o failover

Se ocorrer uma interrupção regional na origem, a Empresa A poderá fazer failover de todos os seus recursos para a região de destino.

- Com os endereços IP de destino já em vigor antes do failover, a Empresa A pode orquestrar o failover e estabelecer automaticamente após o failover conexões entre a **VNet de Recuperação** e a **VNet do Azure**. Isso é ilustrado no diagrama a seguir.
- Dependendo dos requisitos do aplicativo, as conexões entre as duas VNets (**VNet de Recuperação** e **VNet do Azure**) na região de destino podem ser estabelecida antes, durante (como etapa intermediária) ou depois do failover.
  - A empresa pode usar [planos de recuperação](site-recovery-create-recovery-plans.md) para especificar quando as conexões serão estabelecidas.
  - Eles podem estabelecer uma conexão entre as VNets usando o Emparelhamento VNET ou um VPN site a site.
      - O Emparelhamento VNET não usa um gateway de VPN e tem restrições diferentes.
      - Os [preços do Emparelhamento VNET](https://azure.microsoft.com/pricing/details/virtual-network) são calculados de maneira diferente dos [preços](https://azure.microsoft.com/pricing/details/vpn-gateway) do Gateway de VPN de VNet a VNet. Para failovers, normalmente aconselhamos o uso do mesmo método de conectividade das redes de origem, incluindo o tipo de conexão, para minimizar incidentes de rede imprevisíveis.

    ![Failover completo de recursos no Azure](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-full-region-failover2.png)



## <a name="resources-in-azure-isolated-app-failover"></a>Recursos no Azure: failover de aplicativo isolado

Talvez você precise efetuar o failover no nível do aplicativo. Por exemplo, efetuar o failover de um aplicativo específico ou de uma camada de aplicativo localizada em uma sub-rede dedicada.

- Nesse cenário, embora você possa reter os endereços IP, isso não é aconselhável pois aumenta a chance de haver inconsistências de conectividade. Você também perderá a conectividade de sub-rede com outras sub-redes na mesma VNet do Azure.
- Uma maneira melhor de efetuar o failover do aplicativo no nível da sub-rede é usar endereços IP de destino diferentes para o failover (se você precisar de conectividade com outras sub-redes na VNet de origem) ou isolar cada aplicativo em sua própria VNet dedicada na região de origem. Com a segunda abordagem, você pode estabelecer a conectividade entre as redes na região de origem e emular o mesmo comportamento durante o failover para a região de destino.  

Neste exemplo, a Empresa A coloca aplicativos na região de origem em VNets dedicadas e estabelece a conectividade entre essas VNets. Com esse design, é possível efetuar o failover de aplicativos isolados e reter os endereços IP privados de origem na rede de destino.

### <a name="before-failover"></a>Antes do failover

Antes do failover, a arquitetura é a seguinte:

- VMs de aplicativo são hospedadas na região primária do Azure da Ásia Oriental:
    - VMs do **App1** ficam localizadas na **VNet de Origem 1**: 10.1.0.0/16.
    - VMs do **App2** ficam localizadas na **VNet de Origem 2**: 10.2.0.0/16.
    - A **VNet de Origem 1** tem duas sub-redes.
    - A **VNet de Origem 2** tem duas sub-redes.
- A região secundária (de destino) é o Azure no Sudeste Asiático, que conta com VNets de recuperação (**VNet de Recuperação 1** e **VNet de Recuperação 1**) idênticas à **VNet de Origem 1** e à **VNet2 de Origem 2**.
        A - **VNet de Recuperação 1** e a **VNet de Recuperação 2** têm duas sub-redes que correspondem às sub-redes na **VNet de Origem 1** e na **VNet de Origem 2** – no Sudeste Asiático, há uma VNet adicional (**VNet do Azure**) com o espaço de endereço 10.3.0.0/16.
        A - **VNet do Azure** contém uma sub-rede (**Sub-rede 4**) com o espaço de endereço 10.3.4.0/24.
        -Nós de réplica para o SQL Server Always On, controlador de domínio etc. estão localizados em **subrede 4**.
- Há um número de conexões de VPN site a site: 
    - **VNet de Origem 1** e **VNet do Azure**
    - **VNet de Origem 2** e **VNet do Azure**
    - A **VNet de Origem 1** e a **VNet de Origem 2** são conectadas site a site via VPN
- A **VNet de Recuperação 1** e a **VNet de Recuperação 2** não são conectadas a nenhuma outra VNet.
- A **Empresa A** configura gateways de VPN na **VNet de Recuperação 1** e na **VNet de Recuperação 2** para reduzir o RTO.  
- A **VNet1 de recuperação** e a **VNet2 de recuperação** não são conectadas a nenhuma outra rede virtual.
- Para reduzir o objetivo de tempo de recuperação (RTO), gateways de VPN são configurados na **VNet1 de recuperação** e na **VNet2 recuperação** antes do failover.

    ![Recursos no Azure antes do failover do aplicativo](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-before-failover2.png)

### <a name="after-failover"></a>Após o failover

No caso de uma interrupção ou de um problema que afete um único aplicativo (na **VNet de Origem 2 em nosso exemplo), a Empresa A pode recuperar o aplicativo afetado da seguinte maneira:


- Desfaça as conexões VPN entre a **VNet de Origem 1** e a **VNet de Origem 2** e entre a **VNet de Origem 2** e a **VNet do Azure**.
- Estabeleça conexões VPN entre a **VNet de Origem 1** e a **VNet de Recuperação 2** e entre a **VNet de Recuperação 2** e a **VNet do Azure**.
- Efetue o failover das VMs na **VNet de Origem 2** para a **VNet de Recuperação 2**.

![Recursos no failover de aplicativo do Azure](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-after-failover2.png)


- Este exemplo pode ser expandido para incluir mais aplicativos e conexões de rede. Recomenda-se seguir um modelo de conexão similar, sempre que possível, ao realizar failover da origem para o destino.
- Gateways de VPN usam endereços IP públicos e saltos de gateway para estabelecer conexões. Se não quiser usar endereços IP públicos ou se quiser evitar saltos extras, você poderá usar o [Emparelhamento VNET do Azure](../virtual-network/virtual-network-peering-overview.md) para emparelhar redes virtuais em [regiões do Azure com suporte](../virtual-network/virtual-network-manage-peering.md#cross-region).

## <a name="hybrid-resources-full-failover"></a>Recursos híbridos: failover completo

Neste cenário, a **Empresa B** tem um negócio híbrido, com parte da infraestrutura de aplicativos em execução no Azure e o restante em execução local. 

### <a name="before-failover"></a>Antes do failover

A arquitetura de rede tem a seguinte aparência antes do failover.

- VMs de aplicativo são hospedadas no Azure na Ásia Oriental.
- A Ásia Oriental tem uma VNet (**VNet de Origem**) com o espaço de endereço 10.1.0.0/16.
  - A Ásia Oriental tem cargas de trabalho divididas entre três sub-redes na **VNet de Origem**:
    - **Sub-rede 1**: 10.1.1.0/24
    - **Sub-rede 2**: 10.1.2.0/24,
    - **Sub-rede 3**: 10.1.3.0/24, usando uma rede virtual do Azure com o espaço de endereço 10.1.0.0/16. Essa rede virtual é chamada de **VNet de Origem**
      - A região secundária (de destino) é o Azure no Sudeste Asiático:
  - O Sudeste Asiático tem uma VNet de recuperação (**VNet de Recuperação**) idêntica à **VNet de Origem**.
- As VMs na Ásia Oriental são conectadas a um datacenter local por meio do Azure ExpressRoute ou do VPN site a site.
- Para reduzir o RTO, a Empresa B provisiona gateways na VNet de Recuperação no Azure do Sudeste Asiático antes do failover.
- A Empresa B atribui/verifica os endereços IP de destino para VMs replicadas. O endereço IP de destino é o mesmo que o endereço IP de origem para cada VM.


![Conectividade local para Azure antes do failover](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-before-failover2.png)

### <a name="after-failover"></a>Após o failover


Se ocorrer uma interrupção regional na origem, a Empresa B poderá fazer failover de todos os seus recursos para a região de destino.

- Com os endereços IP de destino já em vigor antes do failover, a Empresa B pode orquestrar o failover e estabelecer automaticamente após o failover conexões entre a **VNet de Recuperação** e a **VNet do Azure**.
- Dependendo dos requisitos do aplicativo, as conexões entre as duas VNets (**VNet de Recuperação** e **VNet do Azure**) na região de destino podem ser estabelecida antes, durante (como etapa intermediária) ou depois do failover. A empresa pode usar [planos de recuperação](site-recovery-create-recovery-plans.md) para especificar quando as conexões serão estabelecidas.
- A conexão original entre o Azure da Ásia Oriental e o datacenter local deve ser desconectada antes de estabelecer a conexão entre o Azure do Sudeste Asiático e o datacenter local.
- O roteamento local é reconfigurado para apontar para a região e os gateways de destino após o failover.

![Conectividade local para Azure depois do failover](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-after-failover2.png)

## <a name="hybrid-resources-isolated-app-failover"></a>Recursos híbridos: failover de aplicativo isolado

A Empresa B não pode efetuar o failover de aplicativos isolados no nível da sub-rede. Isso ocorre porque o espaço de endereço nas VNets de origem e de recuperação é o mesmo e conexão original da origem com o local está ativa.

 - Para ter resiliência de aplicativos, a Empresa B precisará colocar cada aplicativo em sua própria VNet do Azure dedicada.
 - Com cada aplicativo em uma VNet separada, a Empresa B pode efetuar failover de aplicativos isolados e encaminhar conexões da origem para a região de destino.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [planos de recuperação](site-recovery-create-recovery-plans.md).
