---
title: "Solucionar problemas de rotas – PowerShell | Microsoft Docs"
description: "Saiba como solucionar problemas de rotas no modelo de implantação do Azure Resource Manager usando o Azure PowerShell."
services: virtual-network
documentationcenter: na
author: AnithaAdusumilli
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: bf7dc5e7-9399-460e-8e0d-8992dbed98a6
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2016
ms.author: anithaa
ms.openlocfilehash: 141e3c571d744470fd07e99538b6e38d4144e8d7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-routes-using-azure-powershell"></a>Solucionar problemas de rotas usando o Azure PowerShell
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

### <a name="view-effective-routes-for-a-network-interface"></a>Exibir rotas em vigor para um adaptador de rede
Para ver as rotas agregadas aplicadas a um adaptador de rede, siga as etapas a seguir:

1. Inicie uma sessão do Azure PowerShell e faça logon no Azure. Se você não estiver familiarizado com o Azure PowerShell, leia o artigo [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview) .
2. O comando a seguir retorna todas as rotas aplicadas a um adaptador de rede denominado *VM1-NIC1* no grupo de recursos *RG1*.
   
       Get-AzureRmEffectiveRouteTable -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1
   
   > [!TIP]
   > Se você não souber o nome de um adaptador de rede, digite o seguinte comando para recuperar os nomes de todos os adaptadores de rede em um grupo de recursos.*
   > 
   > 
   
       Get-AzureRmNetworkInterface -ResourceGroupName RG1 | Format-Table Name
   
   A seguinte saída é semelhante à saída de cada rota aplicada à sub-rede à qual a NIC está conectada:
   
       Name :
       State : Active
       AddressPrefix : {10.9.0.0/16}
       NextHopType : VNetLocal
       NextHopIpAddress : {}
   
       Name :
       State : Active
       AddressPrefix : {0.0.0.0/16}
       NextHopType : Internet
       NextHopIpAddress : {}
   
   Observe o seguinte na saída:
   
   * **Name**: o nome da rota em vigor pode estar vazio, a menos que explicitamente especificado, para rotas definidas pelo usuário. 
   * **State**: indica o estado da rota em vigor. Os valores possíveis são “Active” ou “Invalid”
   * **AddressPrefixes**: especifica o prefixo do endereço da rota em vigor na notação CIDR. 
   * **nextHopType**: indica o próximo salto para a rota determinada. Os valores possíveis são *VirtualAppliance*, *Internet*, *VNetLocal*, *VNetPeering* ou *Null*. Um valor *Null* para **nextHopType** em uma UDR pode indicar uma rota inválida. Por exemplo, se **nextHopType** for *VirtualAppliance* e a VM da solução de virtualização de rede não estiver em um estado provisionado/de execução. Se **nextHopType** for *VPNGateway* e não houver nenhum gateway provisionado/em execução na VNet fornecida, a rota poderá se tornar inválida.
   * **NextHopIpAddress**: especifica o endereço IP do próximo salto da rota em vigor.
   
   O comando a seguir retorna as rotas de uma maneira mais fácil para ver a tabela:
   
       Get-AzureRmEffectiveRouteTable -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1 | Format-Table
   
   A seguinte saída é parte da saída recebida para o cenário descrito anteriormente:
   
       Name State AddressPrefix NextHopType NextHopIpAddress
       ---- ----- ------------- ----------- ----------------
       Active {10.9.0.0/16} VnetLocal {}
       Active {0.0.0.0/0} Internet {}
3. Não há rota listada para a VNet *WestUS-VNet3* (prefixo 10.10.0.0/16)** da *WestUS-VNet1* (prefixo 10.9.0.0/16) na saída da etapa anterior. Conforme mostrado na imagem a seguir, o link de emparelhamento da VNet com a VNet *WestUS-VNet3* está no estado *Desconectado*.
   
    ![](./media/virtual-network-routes-troubleshoot-portal/image4.png)
   
    O link de bi-direcional para o emparelhamento está dividido, o que explica por que a VM1 não pôde se conectar à VM3 na VNet *WestUS-VNet3* . Configure um link de emparelhamento de VNet bidirecional novamente para as VNets *WestUS-VNet1* e *WestUS-VNet3*. A saída retornada após o link de emparelhamento da VNet é estabelecida corretamente a seguir:
   
        Name State AddressPrefix NextHopType NextHopIpAddress
        ---- ----- ------------- ----------- ----------------
        Active {10.9.0.0/16} VnetLocal {}
        Active {10.10.0.0/16} VNetPeering {}
        Active {0.0.0.0/0} Internet {}
   
    Depois de determinar o problema, você pode adicionar, remover ou alterar as rotas e tabelas de rotas. Digite o comando a seguir para ver uma lista dos comandos usados para fazer isso:
   
        Get-Help *-AzureRmRouteConfig

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
  * As regras do NSG (grupo de segurança de rede) podem afetar os fluxos de tráfego. Para obter mais informações, consulte o artigo [Solucionar problemas dos grupos de segurança de rede](virtual-network-nsg-troubleshoot-powershell.md) .

