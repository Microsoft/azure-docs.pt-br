---
title: Integrar o Azure ExpressRoute à recuperação de desastre para VMs do Azure usando o Azure com o Azure Site Recovery | Microsoft Docs
description: Descreve como usar o Azure ExpressRoute com o Azure Site Recovery para recuperação e migração de desastres
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: 90388d570d027aea3c897f7306a1714fd7e847b3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60772345"
---
# <a name="integrate-azure-expressroute-with-disaster-recovery-for-azure-vms"></a>Integrar o ExpressRoute do Azure à recuperação de desastres para VMs do Azure


Este artigo descreve como integrar o Azure ExpressRoute ao [Azure Site Recovery](site-recovery-overview.md), quando você configura a recuperação de desastres de VMs do Azure para uma região secundária do Azure.

O Site Recovery permite a recuperação de desastres das VMs do Azure, replicando os dados da VM do Azure para o Azure.

- Se as VMs do Azure usarem [discos gerenciados do Azure](../virtual-machines/windows/managed-disks-overview.md), os dados da VM serão replicados para um disco gerenciado replicado na região secundária.
- Se as VMs do Azure não usarem discos gerenciados, os dados da VM serão replicados para uma conta de armazenamento do Azure.
- Os pontos de extremidade de replicação são públicos, mas o tráfego de replicação das VMs do Azure não cruza a Internet.

A ExpressRoute permite que você estenda as redes locais para a nuvem do Microsoft Azure por meio de uma conexão privada, facilitada por um provedor de conectividade. Se você tiver configurado o ExpressRoute, ele se integra ao Site Recovery da seguinte maneira:

- **Habilitar a replicação entre as regiões do Azure**: O tráfego de replicação para a recuperação de desastres da VM do Azure está somente no Azure, e a Rota Expressa não é necessária ou usada para replicação. No entanto, se você estiver se conectando de um site local às VMs do Azure no site principal do Azure, há vários problemas a serem observados ao configurar a recuperação de desastres para essas VMs do Azure.
- **Failover entre as regiões do Azure**: Quando ocorrem interrupções, você faz failover das VMs do Azure da região primária para secundária do Azure. Após o failover para uma região secundária, há várias etapas a serem seguidas para acessar as VMs do Azure na região secundária usando o ExpressRoute.


## <a name="before-you-begin"></a>Antes de começar

Antes de começar, certifique-se de que entender os seguintes conceitos:

- Os [circuitos](../expressroute/expressroute-circuit-peerings.md) do ExpressRoute
- Os [domínios de roteamento](../expressroute/expressroute-circuit-peerings.md#routingdomains) do ExpressRoute
- ExpressRoute [locais](../expressroute/expressroute-locations.md).
- VM do Azure [arquitetura de replicação](azure-to-azure-architecture.md)
- Como [configurar a replicação](azure-to-azure-tutorial-enable-replication.md) para VMs do Azure.
- Como [failover](azure-to-azure-tutorial-failover-failback.md) VMs do Azure.


## <a name="general-recommendations"></a>Recomendações gerais

Para práticas recomendadas e para garantir RTOs (Recovery Time Objectives) eficientes para recuperação de desastres, recomendamos que você faça o seguinte ao configurar o Site Recovery para integrar-se ao ExpressRoute:

- Provisione os componentes de rede antes do failover para uma região secundária:
    - Quando você ativa a replicação para VMs do Azure, o Site Recovery pode implantar automaticamente recursos de rede, como redes, sub-redes e gateways na região do Azure de destino, com base nas configurações de rede de origem.
    - Recuperação de site não pode configurar automaticamente os recursos de rede, como gateways de rede virtual.
    - Recomendamos que você provisione esses recursos de rede adicionais antes do failover. Um pequeno tempo de inatividade é associado a essa implantação e pode afetar o tempo de recuperação geral, se você não tiver contabilizado durante o planejamento da implantação.
- Execute o desastre regular exercícios de recuperação:
    - Uma simulação valida sua estratégia de replicação sem perda de dados ou tempo de inatividade e não afeta seu ambiente de produção. Isso ajuda a evitar problemas de configuração de última hora que podem afetar negativamente o RTO.
    - Quando você executa um failover de teste para a simulação, recomendamos usar uma rede separada da VM do Azure, em vez da rede padrão que é configurada quando você habilita a replicação.
- Use espaços de endereço IP diferentes se você tiver um único circuito de Rota Expressa.
    - Recomendamos que você use um espaço de endereço IP diferente para a rede virtual de destino. Isso evita problemas ao estabelecer conexões durante paralisações regionais.
    - Se você não puder usar um espaço de endereço separado, execute o failover de teste de simulação de recuperação de desastre em uma rede de teste separada com endereços IP diferentes. Você não pode conectar duas VNets com espaço de endereço IP sobreposto ao mesmo circuito da Rota Expressa.

## <a name="replicate-azure-vms-when-using-expressroute"></a>Replicar VMs do Azure ao usar o ExpressRoute


Se você quiser configurar a replicação para VMs do Azure em um site primário e estiver se conectando a essas VMs em seu site local através da ExpressRoute, veja o que você precisa fazer:

1. [Ative a replicação](azure-to-azure-tutorial-enable-replication.md) para cada VM do Azure.
2. Opcionalmente, deixe o Site Recovery configurar a rede:
    - Quando você configura e habilita a replicação, o Site Recovery configura redes, sub-redes e sub-redes de gateway na região do Azure de destino, para corresponder àquelas na região de origem. O Site Recovery também mapeia entre as redes virtuais de origem e de destino.
    - Se você não quiser que o Site Recovery faça isso automaticamente, crie os recursos de rede no lado do alvo antes de habilitar a replicação.
3. Crie outros elementos de rede:
    - O Site Recovery não cria tabelas de rotas, gateways VNet, conexões de gateway VNet, peering VNet ou outros recursos e conexões de rede na região secundária.
    - Você precisa criar esses elementos de rede adicionais na região secundária, a qualquer momento antes de executar um failover da região primária.
    - Você pode usar [planos de recuperação](site-recovery-create-recovery-plans.md) e scripts de automação para configurar e conectar esses recursos de rede.
1. Se você tiver um dispositivo virtual de rede (NVA) implantado para controlar o fluxo do tráfego de rede, observe que:
    - Rota de sistema padrão do Azure para replicação de VM do Azure é 0.0.0.0/0.
    - Normalmente, as implantações de NVA também definem uma rota padrão (0.0.0.0/0) que força o tráfego de saída da Internet a fluir pelo NVA. A rota padrão é usada quando nenhuma outra configuração de rota específica pode ser encontrada.
    - Se esse for o caso, o NVA pode estar sobrecarregado se todo o tráfego de replicação passar pelo NVA.
    - A mesma limitação também se aplica ao usar rotas padrão para rotear todo o tráfego de VM do Azure para implantações no local.
    - Nesse cenário, é recomendável que você [crie um ponto de extremidade de serviço de rede](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) em sua rede virtual para o serviço de Microsoft. Storage, para que o tráfego de replicação não saia do limite do Azure.

## <a name="replication-example"></a>Exemplo de replicação

Geralmente, as implantações corporativas têm cargas de trabalho divididas em várias VNets do Azure, com um hub de conectividade central para conectividade externa à Internet e a sites locais. Uma topologia hub e spoke é normalmente usada junto com o ExpressRoute.

![Local-para-Azure com o ExpressRoute antes do failover](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-before-failover.png)

- **Região**. Os aplicativos são implantados na região do Azure East Asia.
- **Redes virtuais de spoke**. Aplicativos são implantados em duas redes virtuais de spoke:
    - **VNet1 de origem**: 10.1.0.0/24.
    - **VNet2 de origem**: 10.2.0.0/24.
    - Cada rede virtual spoke é conectado ao **vNet do Hub**.
- **VNet do hub**. Há uma vNet do hub **vNet do Hub de Origem**: 10.10.10.0/24.
  - Este hub vNet atua como o gatekeeper.
  - Todas as comunicações entre sub-redes passam por esse hub.
    - **As sub-redes de vNet do hub**. O hub vNet tem duas sub-redes:
    - **Subrede NVA**: 10.10.10.0/25. Essa sub-rede contém uma NVA (10.10.10.10).
    - **Gateway de sub-rede**: 10.10.10.128/25. Essa sub-rede contém um gateway da Rota Expressa conectado a uma conexão da Rota Expressa que direciona para o site local por meio de um domínio de roteamento de emparelhamento.
- O datacenter local tem uma conexão de circuito de Rota Expressa por meio de uma borda de parceiro em Hong Kong.
- Todo o roteamento é controlado por meio das tabelas de rota do Azure (UDR).
- Todo o tráfego de saída entre o vNets ou o datacenter no local é roteado pelo NVA.

### <a name="hub-and-spoke-peering-settings"></a>Hub e spoke, configurações de emparelhamento

#### <a name="spoke-to-hub"></a>Do spoke para o hub

**Direção** | **Configuração** | **State**
--- | --- | ---
Do spoke para o hub | Permitir que o endereço de rede virtual | Enabled
Do spoke para o hub | Permitir tráfego encaminhado | Enabled
Do spoke para o hub | Permitir trânsito de gateway | Desabilitado
Do spoke para o hub | Use remover gateways | Enabled

 ![Configuração do emparelhamento spoke-hub](./media/azure-vm-disaster-recovery-with-expressroute/spoke-to-hub-peering-configuration.png)

#### <a name="hub-to-spoke"></a>Do hub para o spoke

**Direção** | **Configuração** | **State**
--- | --- | ---
Do hub para o spoke | Permitir que o endereço de rede virtual | Enabled
Do hub para o spoke | Permitir tráfego encaminhado | Enabled
Do hub para o spoke | Permitir trânsito de gateway | Enabled
Do hub para o spoke | Use remover gateways | Desabilitado

 ![Configuração do emparelhamento hub-spoke](./media/azure-vm-disaster-recovery-with-expressroute/hub-to-spoke-peering-configuration.png)

### <a name="example-steps"></a>Etapas de exemplo

Em nosso exemplo, o seguinte deve acontecer ao habilitar a replicação de VMs do Azure na rede de origem:

1. Você [habilita a replicação](azure-to-azure-tutorial-enable-replication.md) para uma VM.
2. O Site Recovery criará vNets, sub-redes e sub-redes de gateway de réplica na região de destino.
3. O Site Recovery cria mapeamentos entre as redes de origem e as redes de destino da réplica que cria.
4. Você cria manualmente gateways de rede virtual, conexões de gateway de rede virtual, emparelhamento de rede virtual ou qualquer outro recurso ou conexão de rede.


## <a name="fail-over-azure-vms-when-using-expressroute"></a>Falha nas VMs do Azure ao usar o ExpressRoute

Após a falha das VMs do Azure na região do Azure de destino usando o Site Recovery, você pode acessá-las usando o ExpressRoute [emparelhamento privado](../expressroute/expressroute-circuit-peerings.md#privatepeering).

- Você precisa conectar o ExpressRoute ao alvo vNet com uma nova conexão. A conexão ExpressRoute existente não é transferida automaticamente.
- A maneira como você configura sua conexão do ExpressRoute com o vNet de destino depende da sua topologia do ExpressRoute.


### <a name="access-with-two-circuits"></a>Acesso com dois circuitos

#### <a name="two-circuits-with-two-peering-locations"></a>Dois circuitos com dois locais de emparelhamento

Essa configuração ajuda a proteger os circuitos da ExpressRoute contra desastres regionais. Se a localização primária de emparelhamento ficar inativa, as conexões poderão continuar da outra localização.

- O circuito conectado ao ambiente de produção é geralmente o principal. O circuito secundário geralmente tem uma largura de banda menor, que pode ser aumentada se ocorrer um desastre.
- Após o failover, você pode estabelecer conexões do circuito ExpressRoute secundário para a vNet de destino. Como alternativa, você pode configurar e preparar as conexões em caso de desastre para reduzir o tempo de recuperação geral.
- Com conexões simultâneas para os vNets principais e de destino, certifique-se de que o roteamento no local use apenas o circuito secundário e a conexão após o failover.
- As redes virtuais de origem e destino podem receber novos endereços IP ou manter os mesmos que, após o failover. Em ambos os casos, as conexões secundárias podem ser estabelecidas antes do failover.


#### <a name="two-circuits-with-single-peering-location"></a>Dois circuitos com localização de emparelhamento único

Essa configuração ajuda a proteger contra falhas do circuito ExpressRoute primário, mas não se o único local de emparelhamento ExpressRoute cair, afetando os dois circuitos.

- Você pode ter conexões simultâneas do datacenter no local para acessar o vNEt com o circuito principal e para o vNet de destino com o circuito secundário.
- Com conexões simultâneas ao principal e ao destino, certifique-se de que o roteamento no local use apenas o circuito secundário e a conexão após o failover.
-   Você não pode conectar os dois circuitos à mesma rede virtual quando os circuitos são criados no mesmo local de emparelhamento.

### <a name="access-with-a-single-circuit"></a>Acesso com um único circuito

Nessa configuração, há apenas um circuito do Expressroute. Embora o circuito tenha uma conexão redundante no caso de um deles ficar inativo, um único circuito de roteamento não fornecerá resiliência se sua região de emparelhamento for desativada. Observe que:

- Você pode replicar VMs do Azure para qualquer região do Azure na [mesma localização geográfica](azure-to-azure-support-matrix.md#region-support). Se a região do Azure de destino não estiver no mesmo local da origem, você precisará ativar o ExpressRoute Premium se estiver usando um único circuito de Rota Expressa. Saiba mais sobre [localizações de ExpressRoute](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region) e [preços de ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute/).
- Você não pode conectar redes virtuais de origem e de destino simultaneamente o circuito se o mesmo espaço de endereço IP é usado na região de destino. Neste cenário:    
    -  Desconectar a conexão do lado de origem e, em seguida, estabelecer a conexão do lado de destino. Essa alteração de conexão pode ser inserida como parte de um plano de recuperação de Site Recovery. Observe que:
        - Em uma falha regional, se a região primária estiver inacessível, a operação de desconexão poderá falhar. Isso pode afetar a criação da conexão para a região de destino.
        - Se você criou a conexão na região de destino e a região primária for recuperada posteriormente, poderá ocorrer queda de pacotes se duas conexões simultâneas tentarem se conectar ao mesmo espaço de endereço.
        - Para evitar isso, encerrar imediatamente a conexão primária.
        - Após o failback de VM para a região principal, a conexão principal pode ser estabelecida novamente, depois que você desconectar a conexão secundária.
-   Se um espaço de endereço diferente for usado na vNet de destino, você poderá se conectar simultaneamente às vNets de origem e de destino do mesmo circuito da Rota Expressa.


## <a name="failover-example"></a>Exemplo de failover

No nosso exemplo, estamos usando a topologia a seguir:

- Dois circuitos ExpressRoute diferentes em dois locais de emparelhamentos diferentes.
- Reter endereços IP privados para as VMs do Azure após o failover.
- Região de recuperação de destino é o Azure do Sudeste Asiático.
- Uma conexão de circuito do ExpressRoute secundário é estabelecida por meio de uma borda de parceiro em Singapura.

Para uma topologia simples que usa um único circuito da Rota Expressa, com o mesmo endereço IP após o failover, [analise este artigo](site-recovery-retain-ip-azure-vm-failover.md#hybrid-resources-full-failover).

### <a name="example-steps"></a>Etapas de exemplo
Para automatizar a recuperação neste exemplo, veja o que você precisa fazer:

1. Siga as etapas a seguir para configurar a replicação.
2. [Falha nas VMs do Azure](azure-to-azure-tutorial-failover-failback.md), com essas etapas adicionais durante ou após o failover.

    a. Crie o Gateway do ExpressRoute do Azure no hub da região de destino VNet. Isso é necessário para conectar o hub de destino vNet ao circuito da Rota Expressa.

    b. Crie a conexão da vNet do hub de destino para o circuito de ExpressRoute de destino.

    c. Configurar os emparelhamentos de VNet entre o hub da região de destino e as redes virtuais spoke. As propriedades de emparelhamento na região de destino serão as mesmas da região de origem.

    d. Configurar os UDRs na VNet do hub, e as duas VNets spoke.

    - As propriedades dos UDRs do lado do destino são as mesmas do lado da origem quando os mesmos endereços IP são usados.
    - Com endereços IP de destino diferentes, os UDRs devem ser modificados de acordo.


As etapas acima podem ser inseridas como parte de um [plano de recuperação](site-recovery-create-recovery-plans.md). De acordo com a conectividade do aplicativo e com os requisitos de tempo de recuperação, as etapas acima também podem ser concluídas antes de iniciar o failover.

#### <a name="after-recovery"></a>Após a recuperação

Depois de recuperar as máquinas virtuais e concluir a conectividade, o ambiente de recuperação é da seguinte maneira.

![Local-para-o Azure com o ExpressRoute após o Failover](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-after-failover.png)



## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o uso de [planos de recuperação](site-recovery-create-recovery-plans.md) para automatizar o failover de aplicativos.
