---
title: Balanceamento de carga em várias configurações de IP – Portal do Azure
titlesuffix: Azure Load Balancer
description: Balanceamento de carga entre as configurações de IP primárias e secundárias.
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: se0dec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 0cf5aa45e1e8a28dfcdadac0ea32658e5993d06c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60591707"
---
# <a name="load-balancing-on-multiple-ip-configurations-by-using-the-azure-portal"></a>Balanceamento de carga em várias configurações de IP usando o Portal do Azure

> [!div class="op_single_selector"]
> * [Portal](load-balancer-multiple-ip.md)
> * [PowerShell](load-balancer-multiple-ip-powershell.md)
> * [CLI](load-balancer-multiple-ip-cli.md)


Neste artigo, mostraremos como usar o Azure Load Balancer com vários endereços IP em um NIC (controlador de adaptador de rede). O diagrama a seguir ilustra nosso cenário:

![Cenário com o balanceador de carga](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

Em nosso cenário, estamos usando a seguinte configuração:

- Duas VMs (máquinas virtuais) que executam Windows.
- Cada VM tem um NIC primário e um secundário.
- Cada NIC secundário tem duas configurações de IP.
- Cada VM hospeda dois sites: contoso.com e fabrikam.com.
- Cada site está associado a uma das configurações de IP no NIC secundário.
- O Azure Load Balancer é usado para expor os dois endereços IP de front-end, um para cada site. Os endereços de front-end são usados para distribuir o tráfego para a respectiva configuração de IP de cada site.
- O mesmo número da porta é usado para endereços IP de front-end e endereços IP do pool de back-end.

## <a name="prerequisites"></a>Pré-requisitos

Nosso cenário pressupõe que você tem um grupo de recursos denominado **contosofabrikam** com a seguinte configuração:

- O grupo de recursos inclui uma rede virtual denominada **myVNet**.
- A rede **myVNet** inclui duas VMs denominadas **VM1** e **VM2**.
- VM1 e VM2 estão no mesmo conjunto de disponibilidade chamado **myAvailset**. 
- VM1 e VM2 têm cada uma um NIC primário chamado **VM1NIC1** e **VM2NIC1**, respectivamente. 
- VM1 e VM2 têm cada uma um NIC secundário chamado **VM1NIC2** e **VM2NIC2**, respectivamente.

Para saber mais sobre como criar VMs com vários NICs, veja [Criar uma VM com vários NICs usando o PowerShell](../virtual-machines/windows/multiple-nics.md).

## <a name="perform-load-balancing-on-multiple-ip-configurations"></a>Realizar balanceamento de carga em várias configurações de IP

Execute as etapas a seguir para obter o cenário descrito neste artigo.

### <a name="step-1-configure-the-secondary-nics"></a>Etapa 1: Configurar os NICs secundários

Adicione a configuração de IP para o NIC secundário para cada VM na sua rede virtual:  

1. Navegue até o Portal do Azure: https://portal.azure.com. Faça logon usando sua conta do Azure.

2. No canto superior esquerdo da tela, selecione o ícone **Grupo de Recursos**. Em seguida, selecione o grupo de recursos em que suas VMs se encontram (por exemplo, **contosofabrikam**). O painel **Grupos de recursos** exibe todos os recursos e as NICs para as VMs.

3. Adicione uma configuração de IP para o NIC secundário de cada VM:

    1. Selecione o NIC secundário que você deseja configurar.
    
    2. Selecione **Configurações de IP**. No próximo painel, selecione **Adicionar** na parte superior.

    3. Em **Adicionar configurações de IP**, adicione uma segunda configuração de IP ao NIC: 

        1. Digite um nome para a configuração de IP secundário. (Por exemplo, para VM1 e VM2, nomeie a configuração de IP como **VM1NIC2-ipconfig2** e **VM2NIC2-ipconfig2**, respectivamente.)

        2. Para **Endereço IP privado**, na configuração **Alocação**, selecione **Estático**.

        3. Selecione **OK**.

Após concluir a segunda configuração de IP para o NIC secundário, ela será exibida nas definições **Configurações de IP** do NIC especificado.

### <a name="step-2-create-the-load-balancer"></a>Etapa 2: Criar o balanceador de carga

Crie seu balanceador de carga para a configuração:

1. Navegue até o Portal do Azure: https://portal.azure.com. Faça logon usando sua conta do Azure.

2. No canto superior esquerdo da tela, selecione **Criar um recurso** > **Rede** > **Balanceador de carga**. Em seguida, selecione **Criar**.

3. Em **Criar balanceador de carga**, digite um nome para o balanceador de carga. Neste cenário, estamos usando o nome **mylb**.

4. Em **Endereço IP público**, crie um novo IP público chamado **PublicIP1**.

5. Em **Grupo de Recursos**, selecione o grupo de recursos existente das suas VMs (por exemplo, **contosofabrikam**). Selecione o local em que o balanceador de carga será implantado e selecione **OK**.

O balanceador de carga começa a ser implantado. A implantação pode levar alguns minutos para ser concluída. Após a implantação ser concluída, o balanceador de carga é exibido como um recurso em seu grupo de Recursos.

### <a name="step-3-configure-the-front-end-ip-pool"></a>Etapa 3: Configurar o pool de IPs de front-end

Configure o pool de IPs de front-end no balanceador de carga para cada site (contoso.com e fabrikam.com):

1. No portal, clique em **Mais serviços**. Na caixa de filtro, digite **Endereço IP público** e selecione **Endereços IP públicos**. No próximo painel, selecione **Adicionar** na parte superior.

2. Configure dois endereços IP públicos (**PublicIP1** e **PublicIP2**) para os dois sites (contoso.com e fabrikam.com):

   1. Digite um nome para seu endereço IP de front-end.

   2. Em **Grupo de Recursos**, selecione o grupo de recursos existente para suas VMs (por exemplo, **contosofabrikam**).

   3. Para **Local**, selecione o mesmo local das VMs.

   4. Selecione **OK**.

      Após a criação dos dois endereços IP públicos, eles serão exibidos nos endereços **IP público**.

3. <a name="step3-3"></a>No portal, clique em **Mais serviços**. Na caixa de filtro, digite **balanceador de carga** e selecione **Load Balancer**. 

4. Selecione o balanceador de carga (**mylb**) ao qual você deseja adicionar o pool de IPs de front-end.

5. Em **Configurações**, selecione **configuração IP de Front-end**. No próximo painel, selecione **Adicionar** na parte superior.

6. Digite um nome para o endereço IP de front-end (por exemplo, **contosofe** ou **fabrikamfe**).

7. <a name="step3-7"></a>Selecione **Endereço IP**. Em **Escolher Endereço IP público**, selecione os endereços IP do seu front-end (**PublicIP1** ou **PublicIP2**).

8. Crie o segundo endereço IP de front-end repetindo a <a href="#step3-3">etapa 3</a> até a <a href="#step3-7">etapa 7</a> nesta seção.

Depois que o pool de front-end estiver configurado, os endereços IP serão exibidos nas configurações de **configuração de IP de front-end** do balanceador de carga. 
    
### <a name="step-4-configure-the-back-end-pool"></a>Etapa 4: Configure o pool de back-end

Configure o pool de endereços de back-end no balanceador de carga para cada site (contoso.com e fabrikam.com):
        
1. No portal, clique em **Mais serviços**. Na caixa de filtro, digite **balanceador de carga** e selecione **Load Balancer**.

2. Selecione o balanceador de carga (**mylb**) ao qual você deseja adicionar o pool de back-end.

3. Em **Configurações**, selecione **Pools de Back-end**. Digite um nome para o pool de back-end (por exemplo, **contosopool** ou **fabrikampool**). No próximo painel, selecione **Adicionar** na parte superior. 

4. Para **Associado a**, selecione **Conjunto de disponibilidade**.

5. Para **Conjunto de Disponibilidade**, selecione **myAvailset**.

6. Adicione as configurações de IP da rede de destino para as duas VMs: 

    ![Configurar pools de back-end para o balanceador de carga](./media/load-balancer-multiple-ip/lb-backendpool.PNG)
    
    1. Para **Máquina Virtual de destino**, selecione a VM que você deseja adicionar ao pool de back-end (por exemplo, **VM1** ou **VM2**).

    2. Para **Configuração de IP de rede**, selecione a configuração de IP do NIC secundário da VM selecionada na etapa anterior (por exemplo, **VM1NIC2-ipconfig2** ou **VM2NIC2-ipconfig2**).

7. Selecione **OK**.

Depois que o pool de back-end estiver configurado, os endereços serão exibidos nas configurações de **Pool de IP de front-end** do balanceador de carga.

### <a name="step-5-configure-the-health-probe"></a>Etapa 5: Configurar a investigação de integridade

Configure uma investigação de integridade para o balanceador de carga:

1. No portal, clique em **Mais serviços**. Na caixa de filtro, digite **balanceador de carga** e selecione **Load Balancer**.

2. Selecione o balanceador de carga (**mylb**) ao qual você deseja adicionar a investigação de integridade.

3. Em **Configurações**, selecione **Investigação de integridade**. No próximo painel, selecione **Adicionar** na parte superior. 

4. Digite um nome para a investigação de integridade (por exemplo, **HTTP**). Selecione **OK**.

### <a name="step-6-configure-load-balancing-rules"></a>Etapa 6: Configuração de regras de balanceamento de carga

Configure as regras de balanceamento de carga para cada site (contoso.com e fabrikam.com):
    
1. <a name="step6-1"></a>Em **Configurações**, selecione **Carregar regras de balanceamento de carga**. No próximo painel, selecione **Adicionar** na parte superior. 

2. Para **Nome**, digite um nome para a regra de balanceamento de carga (por exemplo, **HTTPc** para Contoso ou **HTTPf** para Fabrikam).

3. Para **Endereço IP de front-end**, selecione o endereço IP de front-end criado anteriormente (por exemplo **contosofe** ou **fabrikamfe**).

4. Para **Porta** e **Porta de back-end**, mantenha o valor padrão de **80**.

5. Para **IP Flutuante (retorno de servidor direto)**, selecione **Desabilitado**.

6. <a name="step6-6"></a>Selecione **OK**.

7. Crie a regra do segundo balanceador de carga repetindo a <a href="#step6-1">etapa 1</a> até a <a href="#step6-6">etapa 6</a> nesta seção.

Depois que as regras forem configuradas, elas serão exibidas nas configurações **Regras de balanceamento de carga** do balanceador de carga.

### <a name="step-7-configure-dns-records"></a>Etapa 7: Configurar registros de DNS

Como a última etapa, configure os registros de recurso DNS para apontar para os respectivos endereços IP de front-end do balanceador de carga. É possível hospedar seus domínios no DNS do Azure. Para saber mais sobre como usar o DNS do Azure com o Load Balancer, confira [Usar o DNS do Azure com outros serviços do Azure](../dns/dns-for-azure-services.md).

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre como combinar os serviços de balanceamento de carga no Azure em [Usando os serviços de balanceamento de carga no Azure](../traffic-manager/traffic-manager-load-balancing-azure.md).
- Saiba como você pode usar diferentes tipos de logs para gerenciar e solucionar problemas do balanceador de carga no [logs do Azure Monitor para o Azure Load Balancer](../load-balancer/load-balancer-monitor-log.md).
