---
title: "Solucionar problemas de rotas – Portal | Microsoft Docs"
description: "Saiba como solucionar problemas de rotas no modelo de implantação do Azure Resource Manager usando o Portal do Azure."
services: virtual-network
documentationcenter: na
author: AnithaAdusumilli
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: bdd8b6dc-02fb-4057-bb23-8289caa9de89
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2016
ms.author: anithaa
ms.openlocfilehash: dad415936280b4af916b8c46df46f6c51ac0bca4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-routes-using-the-azure-portal"></a>Solucionar problemas de rotas usando o Portal do Azure
> [!div class="op_single_selector"]
> * [Portal do Azure](virtual-network-routes-troubleshoot-portal.md)
> * [PowerShell](virtual-network-routes-troubleshoot-powershell.md)
>
>

Se você está tendo problemas de conectividade de rede para ou da sua VM (máquina virtual) do Azure, as rotas podem estar afetando os fluxos de tráfego da sua VM. Este artigo oferece uma visão geral dos recursos de diagnóstico para rotas a fim de ajudar a solucionar outros problemas.

As tabelas de rotas são associadas a sub-redes e estão em vigor em todas as NICs (interfaces de rede) nessa sub-rede. Os seguintes tipos de rotas podem ser aplicados a cada adaptador de rede:

* **Rotas de sistema:** por padrão, cada sub-rede criada em uma VNet (rede virtual) do Azure tem tabelas de rotas de sistema que possibilitam o tráfego da VNet, o tráfego local por meio de gateways de VPN e o tráfego da Internet. Também existem rotas de sistema para VNets emparelhadas.
* **Rotas BGP:** propagadas para adaptadores de rede por meio do ExpressRoute ou conexões VPN site a site. Saiba mais sobre o roteamento BGP lendo os artigos [BGP com gateways de VPN](../vpn-gateway/vpn-gateway-bgp-overview.md) e de [visão geral do ExpressRoute](../expressroute/expressroute-introduction.md).
* **UDR (Rotas definidas pelo usuário):** se você estiver usando soluções de virtualização de rede ou fazendo o túnel forçado do tráfego para uma rede local por meio de uma VPN site a site, você poderá ter UDRs (rotas definidas pelo usuário) associadas à sua tabela de rotas de sub-rede. Se você não estiver familiarizado com UDRs, leia o artigo [rotas definidas pelo usuário](virtual-networks-udr-overview.md#user-defined-routes) .

Com as várias rotas que podem ser aplicadas a um adaptador de rede, pode ser difícil determinar quais rotas agregadas estão em vigor. Para ajudar a solucionar problemas de conectividade de rede da VM, você pode exibir todas as rotas em vigor para um adaptador de rede no modelo de implantação do Azure Resource Manager.

## <a name="using-effective-routes-to-troubleshoot-vm-traffic-flow"></a>Usando rotas em vigor para solucionar problemas de fluxo de tráfego da VM
Este artigo usa o cenário a seguir como um exemplo para ilustrar como solucionar problemas das rotas em vigor de um adaptador de rede:

Uma VM (*VM1*) conectada à VNet (*VNet1*, prefixo: 10.9.0.0/16) falha ao se conectar a uma VM(VM3) em uma VNet recém-emparelhada (*VNet3*, prefixo 10.10.0.0/16). Não há nenhuma UDR ou rotas BGP aplicadas ao adaptador de rede VM1-NIC1 conectado à VM. Apenas rotas de sistema foram aplicadas.

Este artigo explica como determinar a causa da falha de conexão, usando a funcionalidade de rotas em vigor no modelo de implantação do Gerenciamento de recursos do Azure.
Embora o exemplo use somente rotas de sistema, as mesmas etapas podem ser usadas para determinar falhas de conexão de entrada e saída em qualquer tipo de porta.

> [!NOTE]
> Se sua VM tiver mais de uma NIC conectada, verifique as rotas em vigor para cada uma das NICs a fim de diagnosticar problemas de conectividade de rede para e de uma VM.
>
>

### <a name="view-effective-routes-for-a-virtual-machine"></a>Exibir rotas em vigor para uma máquina virtual
Para ver as rotas agregadas aplicadas a uma VM, siga as etapas a seguir:

1. Entre no portal do Azure em https://portal.azure.com.
2. Clique em **Mais serviços** e em **Máquinas virtuais** na lista exibida.
3. Selecione uma VM para solucionar problemas na lista, e uma folha da VM com opções é exibida.
4. Clique em **Diagnosticar e resolver problemas** e selecione um problema comum. Neste exemplo, **Não é possível me conectar à minha VM do Windows** está selecionado.

    ![](./media/virtual-network-routes-troubleshoot-portal/image1.png)
5. As etapas são exibidas embaixo do problema, conforme mostrado na imagem a seguir:

    ![](./media/virtual-network-routes-troubleshoot-portal/image2.png)

    Clique em *rotas efetivas* na lista de etapas recomendadas.
6. A folha **Rotas em vigor** é exibida, conforme mostrado na imagem a seguir:

    ![](./media/virtual-network-routes-troubleshoot-portal/image3.png)

    Se sua VM tiver apenas um NIC, ele será selecionado por padrão. Se você tiver mais de um NIC, selecione a NIC para a qual você deseja exibir as rotas em vigor.

   > [!NOTE]
   > Se a VM associada à NIC não estiver em um estado de execução, as rotas em vigor não serão exibidas. Somente as primeiras 200 rotas em vigor são mostradas no portal. Para obter a lista completa, clique em **Baixar**. Você pode filtrar ainda mais os resultados do arquivo. csv baixado.
   >
   >

    Observe o seguinte na saída:

   * **Source**: indica o tipo de rota. Rotas de sistema são mostradas como *Default*, UDRs são mostradas como *User* e as rotas de gateway (estáticas ou BGP) são mostradas como *VPNGateway*.
   * **State**: indica o estado da rota em vigor. Os valores possíveis são *Active* ou *Invalid*.
   * **AddressPrefixes**: especifica o prefixo do endereço da rota em vigor na notação CIDR.
   * **nextHopType**: indica o próximo salto para a rota determinada. Os valores possíveis são *VirtualAppliance*, *Internet*, *VNetLocal*, *VNetPeering* ou *Null*. Um valor *Null* para **nextHopType** em uma UDR pode indicar uma rota inválida. Por exemplo, se **nextHopType** for *VirtualAppliance* e a VM da solução de virtualização de rede não estiver em um estado provisionado/de execução. Se **nextHopType** for *VPNGateway* e não houver nenhum gateway provisionado/em execução na VNet fornecida, a rota poderá se tornar inválida.
7. Não há nenhuma rota listada para a *VNet WestUS-VNET3* (prefixo 10.10.0.0/16) da *WestUS-VNet1* (prefixo 10.9.0.0/16) na imagem da etapa anterior. Na imagem a seguir, o vínculo de emparelhamento está no estado *Desconectado* :

    ![](./media/virtual-network-routes-troubleshoot-portal/image4.png)

    O link de bi-direcional para o emparelhamento está dividido, o que explica por que a VM1 não pôde se conectar à VM3 na VNet *WestUS-VNet3* .
8. A imagem a seguir mostra as rotas depois de estabelecer o link de emparelhamento bidirecional:

    ![](./media/virtual-network-routes-troubleshoot-portal/image5.png)

Para ver mais cenários de solução de problemas para avaliação de rotas e túnel forçado, leia a seção [Considerações](virtual-network-routes-troubleshoot-portal.md#considerations) deste artigo.

### <a name="view-effective-routes-for-a-network-interface"></a>Exibir rotas em vigor para um adaptador de rede
Se o fluxo do tráfego de rede for afetado para um NIC (adaptador de rede) específico, você poderá ver uma lista completa de rotas em vigor em um NIC diretamente. Para ver as rotas agregadas aplicadas a um NIC, siga as etapas a seguir:

1. Entre no portal do Azure em https://portal.azure.com.
2. Clique em **Mais serviços** e, em seguida, clique em **Interfaces de rede**
3. Procure o nome de um NIC na lista ou selecione-o na lista exibida. Neste exemplo, **VM1-NIC1** foi selecionada.
4. Selecione **Rotas em vigor** na folha **Interface de rede**, conforme mostrado na imagem a seguir:

       ![](./media/virtual-network-routes-troubleshoot-portal/image6.png)

    O valor padrão para o adaptador de rede selecionado é **Escopo** .

      ![](./media/virtual-network-routes-troubleshoot-portal/image7.png)

### <a name="view-effective-routes-for-a-route-table"></a>Exibir rotas em vigor para uma tabela de rotas
Ao modificar as UDRs (rotas definidas pelo usuário) em uma tabela de rotas, convém analisar o impacto das rotas sendo adicionadas em uma VM específica. Uma tabela de rotas pode ser associada a qualquer número de sub-redes. Agora você pode exibir todas as rotas em vigor para todas as NICs aos quais uma tabela de rotas determinada é aplicada, sem precisar mudar o contexto da folha de tabelas de rotas determinada.

Neste exemplo, uma UDR (*UDRoute*) é especificada em uma tabela de rotas (*UDRouteTable*). Essa rota envia todo o tráfego de Internet da *Subnet1* na VNet *WestUS-VNet1*, por meio de uma NVA (solução de virtualização de rede), na *Sub-rede 2* da mesma VNet. A saída é mostrada na imagem a seguir:

![](./media/virtual-network-routes-troubleshoot-portal/image8.png)

Para ver as rotas agregadas para a uma tabela de rotas, conclua as etapas a seguir:

1. Entre no portal do Azure em https://portal.azure.com.
2. Clique em **Mais serviços** e, em seguida, clique em **Tabelas de rotas**
3. Pesquise na lista a tabela de rotas para a qual você deseja ver rotas agregadas e selecione-a. Neste exemplo, **UDRouteTable** foi selecionado. Uma folha da tabela de rotas selecionada é exibida, conforme mostrado na imagem a seguir:

    ![](./media/virtual-network-routes-troubleshoot-portal/image9.png)
4. Selecione **Rotas em vigor** na folha **Tabela de rotas**. O **Escopo** é definido como a tabela de rotas selecionada.
5. Uma tabela de rotas pode ser aplicada a várias sub-redes. Selecione a **Sub-rede** que você deseja examinar na lista. Neste exemplo, a **Subnet1** foi selecionada.
6. Selecione um **Adaptador de rede**. Todas as NICs conectadas à sub-rede selecionada são listadas. Neste exemplo, **VM1-NIC1** foi selecionada.

    ![](./media/virtual-network-routes-troubleshoot-portal/image10.png)

   > [!NOTE]
   > Se a NIC não está associada uma VM em execução, nenhuma rota em vigor é mostrada.
   >
   >

## <a name="considerations"></a>Considerações
Algumas coisas para ter em mente ao examinar a lista de rotas retornadas:

* O roteamento é baseado no LPM (Correspondência de Prefixo Mais Longo) entre UDRs, rotas BGP e do sistema. Se houver mais de uma rota com a mesma correspondência LPM, então uma rota será selecionada com base em sua origem na seguinte ordem:

  * Rota definida pelo usuário
  * Rota BGP
  * Rota do sistema (padrão)

    Com rotas em vigor, você só pode ver as rotas em vigor com correspondência LPM baseada em todas as rotas disponíveis. Ao mostrar como as rotas são realmente avaliadas para uma determinada NIC, fica muito mais fácil solucionar problemas de rotas específicas que podem afetar a conectividade para/de sua VM.
* Se você tiver UDRs e estiver enviando o tráfego para uma NVA (solução de virtualização de rede) com *VirtualAppliance* como **nextHopType**, verifique se o encaminhamento de IP está habilitado na NVA que está recebendo o tráfego ou se os pacotes foram removidos.
* Se túnel forçado estiver habilitado, todo o tráfego de Internet de saída será roteado para o local. O RDP/SSH da Internet para sua VM pode não funcionar com essa configuração, dependendo de como o local trata esse tráfego.
  O túnel forçado pode ser habilitado:
  * Se você estiver usando a VPN site a site, definindo uma UDR (rota definida pelo usuário) com nextHopType como Gateway de VPN
  * Se uma rota padrão é anunciada por meio do BGP
* Para que o tráfego do emparelhamento de VNet funcione corretamente, deve haver uma rota do sistema com **nextHopType** *VNetPeering* para o intervalo de prefixo da VNet emparelhada. Se tal rota não existir e o link de emparelhamento da VNet parecer correto:
  * Aguarde alguns segundos e tente novamente se for um link de emparelhamento recém-estabelecido. Ela ocasionalmente demora mais para propagar rotas para todos os adaptadores de rede em uma sub-rede.
  * As regras do NSG (grupo de segurança de rede) podem afetar os fluxos de tráfego. Para obter mais informações, consulte o artigo [Solucionar problemas dos grupos de segurança de rede](virtual-network-nsg-troubleshoot-portal.md) .
