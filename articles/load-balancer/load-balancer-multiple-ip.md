---
title: "Balanceamento de carga em várias configurações de IP no Azure | Microsoft Docs"
description: "Balanceamento de carga entre as configurações de IP primárias e secundárias."
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: na
ms.assetid: 244907cd-b275-4494-aaf7-dcfc4d93edfe
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 8c0fc8d11a872b99fee2efa3a32a9e1ccce67f3c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="load-balancing-on-multiple-ip-configurations-using-the-azure-portal"></a>Balanceamento de carga em várias configurações de IP usando o portal do Azure

> [!div class="op_single_selector"]
> * [Portal](load-balancer-multiple-ip.md)
> * [PowerShell](load-balancer-multiple-ip-powershell.md)
> * [CLI](load-balancer-multiple-ip-cli.md)

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Este artigo descreve como usar o Azure Load Balancer com vários endereços IP em um NIC (adaptador de rede) secundário. Para esse cenário, temos duas VMs executando o Windows, cada uma com um NIC primário e um secundário. Cada uma das NICs secundárias tem duas configurações de IP. Cada VM hospeda os sites contoso.com e fabrikam.com. Cada site está associado a uma das configurações de IP na NIC secundária. Usamos o Azure Load Balancer para expor dois endereços IP front-end, um para cada site, a fim de distribuir o tráfego para a respectiva configuração de IP do site. Esse cenário usa o mesmo número de porta entre os front-ends, bem como os dois endereços IP do pool de back-end.

![Imagem de cenário do LB](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

##<a name="prerequisites"></a>Pré-requisitos
Este exemplo pressupõe que você tenha um Grupo de recursos denominado *contosofabrikam* com a seguinte configuração:
 -  inclui uma rede virtual chamada *myVNet*, duas VMs chamadas *VM1* e *VM2* respectivamente, no mesmo conjunto de disponibilidade chamado *myAvailset*. 
 - cada VM tem um NIC primário e um NIC secundário. Os NICs primários são chamados *VM1NIC1* e *VM2NIC1* e os NICs secundários são chamados *VM1NIC2* e *VM2NIC2*. Para saber mais sobre como criar VMs com vários NICs, confira [Criar uma VM com vários NICs usando o PowerShell](../virtual-network/virtual-network-deploy-multinic-arm-ps.md).

## <a name="steps-to-load-balance-on-multiple-ip-configurations"></a>Etapas para o balanceamento de carga em várias configurações de IP

Conclua as etapas a seguir para obter o cenário descrito neste artigo:

### <a name="step-1-configure-the-secondary-nics-for-each-vm"></a>Etapa 1: Configurar os NICs secundários para cada VM

Para cada VM em sua rede virtual, adicione a configuração de IP para o NIC secundário da seguinte maneira:  

1. Em um navegador, acesse o Portal do Azure: http://portal.azure.com e faça logon com sua conta do Azure.
2. No canto superior esquerdo da tela, clique no ícone do Grupo de Recursos e depois clique no grupo de recursos no qual as VMs estão localizadas (por exemplo, *contosofabrikam*). A folha **Grupos de recursos**, que lista todos os recursos junto com os adaptadores de rede para as VMs, agora é exibida.
3. Para o NIC secundário de cada VM, adicione uma configuração de IP da seguinte maneira:
    1. Selecione o adaptador de rede ao qual você deseja adicionar a configuração de IP.
    2. Na folha que aparece para o NIC selecionado, clique em **Configurações de IP**. Em seguida, clique em **Adicionar** na parte superior da folha que aparece.
    3. Na folha **Adicionar configurações de IP**, adicione uma segunda configuração de IP ao NIC da seguinte maneira: 
        1. Digite um nome para sua configuração de IP secundário (por exemplo, para VM1 e VM2 dê o nome das configurações de IP como *VM1NIC2 ipconfig2* e *VM2NIC2 ipconfig2* respectivamente).
        2. Para **Endereço IP privado**, para **Alocação**, selecione **Estático**.
        3. Clique em **OK**.
        4. Após a conclusão da segunda configuração de IP para o NIC secundário, ela será exibida na folha de configurações **Configurações de IP** do NIC especificado.

### <a name="step-2-create-a-load-balancer"></a>ETAPA 2: Criar um balanceador de carga

Crie um balanceador de carga da seguinte maneira:

1. Em um navegador, acesse o Portal do Azure: http://portal.azure.com e faça logon com sua conta do Azure.
2. No lado superior esquerdo da tela, clique em **Novo** > **Rede** > **Balanceador de Carga**. Em seguida, clique em **Criar**.
3. Na folha **Criar balanceador de carga** , digite um nome para o balanceador de carga. Aqui, ele é chamado de *mylb*.
4. Em Endereço IP público, crie um novo IP público chamado **PublicIP1**.
5. Em Grupo de Recursos, selecione o Grupo de recursos existente de suas VMs (por exemplo, *contosofabrikam*). Em seguida, selecione uma Localização apropriada e clique em **OK**. Em seguida, o balanceador de carga começa a ser implantado e pode levar alguns minutos para que a implantação seja concluída com êxito.
6. Após a implantação, o balanceador de carga é exibido como um recurso em seu Grupo de Recursos.

### <a name="step-3-configure-the-frontend-ip-pool"></a>ETAPA 3: Configurar o pool de IPs de front-end

Configure seu pool de IPs de front-end para cada site (Contoso e Fabrikam) da seguinte maneira:

1. No portal, clique em **Mais serviços** > digite **Endereço IP público** na caixa de filtro e clique em **Endereços IP públicos**. Clique em **Adicionar** na parte superior da folha que aparece.
2. Configurar dois endereços IP públicos (*PublicIP1* e *PublicIP2*) para os dois sites (contoso e fabrikam) da seguinte maneira:
    1. Digite um nome para seu endereço IP de front-end.
    2. Para **Grupo de Recursos**, selecione o Grupo de Recursos existente das VMs (por exemplo, *contosofabrikam*).
    3. Para **Local**, selecione o mesmo local das VMs.
    4. Clique em **OK**.
    5. Após a criação dos dois endereços IP públicos, eles serão exibidos na folha de **Endereços IP públicos**.
3. No portal, clique em **Mais serviços** > digite **balanceador de carga** na caixa de filtro e clique em **Balanceador de Carga**.  
4. Selecione o balanceador de carga (*mylb*) ao qual você deseja adicionar o pool de IPs de front-end.
5. Em **Configurações**, selecione **Pools de Front-end**. Em seguida, clique em **Adicionar** na parte superior da folha que aparece.
6. Digite um nome para o endereço IP de front-end (*farbikamfe* ou *contosofe*).
7. Clique em **Endereço IP** e na folha **Escolher Endereço IP público**, selecione os endereços IP de seu front-end (*PublicIP1* ou *PublicIP2*).
8. Para criar o segundo endereço IP de front-end, repita as etapas 3 a 7 desta seção.
9. Quando a configuração de pool de IPs de front-end estiver concluída, os dois endereços IP de front-end serão exibidos na folha **Pool de IPs de Front-end** de seu balanceador de carga. 
    
### <a name="step-4-configure-the-backend-pool"></a>ETAPA 4: Configurar o pool de back-ends   
Configure os pools de endereços back-end em eu balanceador de carga para cada site (Contoso e Fabrikam) da seguinte maneira:
        
1. No portal, clique em **Mais serviços** > digite balanceador de carga na caixa de filtro e clique em **Balanceador de Carga**.  
2. Selecione o balanceador de carga (*mylb*) ao qual você deseja adicionar os pools de back-end.
3. Em **Configurações**, selecione **Pools de Back-end**. Digite um nome para o pool de back-ends (por exemplo, *contosopool* ou *fabrikampool*). Em seguida, clique no botão **Adicionar** na parte superior da folha que aparece. 
4. Para **Associado a**, selecione **Conjunto de disponibilidade**.
5. Para **Conjunto de Disponibilidade**, selecione **myAvailset**.
6. Adicione as Configurações de IP da rede de destino, para as duas VMs, da seguinte maneira (veja a Figura 2):  
    1. Para **Máquina Virtual de destino**, selecione a VM que você deseja adicionar ao pool de back-ends (por exemplo, VM1 ou VM2).
    2. Para **Configuração de rede IP**, selecione a configuração de IP de NICs secundários dessa VM (por exemplo, VM1NIC2-ipconfig2 ou VM2NIC2-ipconfig2).
    ![Imagem do cenário de BC](./media/load-balancer-multiple-ip/lb-backendpool.PNG)
            
        **Figura 2**: configuração do balanceador de carga com pools de back-end  
7. Clique em **OK**.
8. Quando a configuração de pool de back-ends estiver concluída, os dois pools de endereços back-end serão exibidos na folha **Pool de back-ends** de seu balanceador de carga.

### <a name="step-5-configure-a-health-probe-for-your-load-balancer"></a>ETAPA 5: Configurar um teste de integridade para seu balanceador de carga
Configure uma investigação de integridade para o balanceador de carga da seguinte maneira:
    1. No portal, clique em Mais serviços > digite balanceador de carga na caixa de filtro e clique em **Balanceador de Carga**.  
    2. Selecione o balanceador de carga ao qual você deseja adicionar os pools de back-end.
    3. Em **Configurações**, selecione **Investigação de integridade**. Em seguida, clique em **Adicionar** na parte superior da folha que aparece.
    4. Digite um nome para a investigação de integridade (por exemplo, HTTP) e clique em **OK**.

### <a name="step-6-configure-load-balancing-rules"></a>ETAPA 6: Configurar regras de balanceamento de carga
Configure as regras de balanceamento de carga (*HTTPc* e *HTTPf*) para cada site da seguinte maneira:
    
1. Em **Configurações**, selecione **Investigação de integridade**. Em seguida, clique em **Adicionar** na parte superior da folha que aparece.
2. Para **Nome**, digite um nome para a regra de balanceamento de carga (por exemplo, *HTTPc* para Contoso, ou *HTTPf* para Fabrikam)
3. Para Endereço IP de front-end, selecione o endereço IP de front-end (por exemplo *Contosofe* ou *Fabrikamfe*)
4. Para **Porta** e **Porta de back-end**, mantenha o valor padrão de **80**.
5. Para **IP flutuante (retorno de servidor direto)** clique em **Habilitado**.
6. Clique em **OK**.
7. Para criar a segunda regra do Load Balancer, repita as etapas 1 a 6 nesta seção.
8. Após a conclusão da configuração da regra de balanceamento de carga, as duas regras ((*HTTPc* e *HTTPf*) serão exibidas na folha **Regras de balanceamento de carga** de seu balanceador de carga.

### <a name="step-7-configure-dns-records"></a>ETAPA 7: Configurar registros DNS
Por fim, você deve configurar os registros de recurso DNS para apontar para o endereço IP do endereço IP front-end do balanceador de carga. Você pode hospedar seus domínios no DNS do Azure. Para saber mais sobre como usar o DNS do Azure com o Load Balancer, confira [Usar o DNS do Azure com outros serviços do Azure](../dns/dns-for-azure-services.md).

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre como combinar os serviços de balanceamento de carga no Azure em [Usando os serviços de balanceamento de carga no Azure](../traffic-manager/traffic-manager-load-balancing-azure.md).
- Saiba como é possível usar diferentes tipos de logs para gerenciar e solucionar problemas do balanceador de carga em [Log Analytics para o Azure Load Balancer](../load-balancer/load-balancer-monitor-log.md).
