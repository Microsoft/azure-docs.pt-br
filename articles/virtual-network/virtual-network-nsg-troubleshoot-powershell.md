---
title: "Solucionar problemas de grupos de segurança de rede – PowerShell | Microsoft Docs"
description: "Saiba como solucionar problemas de Grupos de Segurança de Rede no modelo de implantação do Azure Resource Manager usando o Azure PowerShell."
services: virtual-network
documentationcenter: na
author: AnithaAdusumilli
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: 4c732bb7-5cb1-40af-9e6d-a2a307c2a9c4
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2016
ms.author: anithaa
ms.openlocfilehash: 5edaf7197576ac1c0bd1fc6bed21fd65ed135106
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-network-security-groups-using-azure-powershell"></a>Solucionar problemas de grupos de segurança de rede usando o Azure PowerShell
> [!div class="op_single_selector"]
> * [Portal do Azure](virtual-network-nsg-troubleshoot-portal.md)
> * [PowerShell](virtual-network-nsg-troubleshoot-powershell.md)
> 
> 

Se você configurou NSGs (grupos de segurança de rede) em sua VM (máquina virtual) e está tendo problemas de conectividade com a VM, este artigo fornece uma visão geral dos recursos de diagnóstico para NSGs a fim de ajudar a solucionar outros problemas.

Os NSGs permitem que você controle os tipos de tráfego que fluem para dentro e fora de suas VMs (máquinas virtuais). Os NSGs podem ser aplicados a sub-redes em uma VNet (rede virtual) do Azure, à NIC (adaptadores de rede) ou ambas. As regras em vigor aplicadas a uma NIC são uma agregação das regras que existem nos NSGs aplicados a uma NIC e à sub-rede à qual ela está conectada. As regras nesses NSGs podem, às vezes, entrar em conflito umas com as outras e afetar a conectividade de rede de uma VM.  

Você pode ver todas as regras de segurança em vigor em seus NSGs, conforme aplicado nas NICs da sua VM. Este artigo mostra como solucionar problemas de conectividade da VM usando essas regras no modelo de implantação do Azure Resource Manager. Se você não estiver familiarizado com conceitos de VNet e NSG, leia os artigos de visão geral de [Rede virtual](virtual-networks-overview.md) e [Grupos de segurança de rede](virtual-networks-nsg.md).

## <a name="using-effective-security-rules-to-troubleshoot-vm-traffic-flow"></a>Usando regras de segurança em vigor para solucionar problemas de fluxo de tráfego da VM
O cenário a seguir é um exemplo de um problema de conexão comum:

Uma VM denominada *VM1* faz parte de uma sub-rede denominada *Subnet1* em uma VNet denominada *WestUS-VNet1*. Falha em uma tentativa de conectar-se à VM usando o RDP por meio da porta TCP 3389. Os NSGs são aplicados tanto na NIC *VM1-NIC1* quanto na sub-rede *Subnet1*. O tráfego para a porta TCP 3389 é permitido no NSG associado ao adaptador de rede *VM1-NIC1*. No entanto, ocorre uma falha no ping do TCP para a porta 3389 da VM1.

Embora este exemplo use a porta TCP 3389, as etapas a seguir podem ser seguidas para determinar falhas de conexão de entrada e saída em qualquer porta.

## <a name="detailed-troubleshooting-steps"></a>Etapas de solução de problemas detalhadas
Siga as etapas a seguir para solucionar problemas de NSGs em uma VM:

1. Inicie uma sessão do Azure PowerShell e faça logon no Azure. Se você não estiver familiarizado com o uso do Azure PowerShell, leia o artigo [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview) .
2. Digite o seguinte comando para retornar todas as regras do NSG aplicadas a uma NIC denominada *VM1-NIC1* no grupo de recursos *RG1*:
   
        Get-AzureRmEffectiveNetworkSecurityGroup -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1
   
   > [!TIP]
   > Se você não souber o nome de uma NIC, digite o seguinte comando para recuperar os nomes de todas as NICs em um grupo de recursos: 
   > 
   > `Get-AzureRmNetworkInterface -ResourceGroupName RG1 | Format-Table Name`
   > 
   > 
   
    O texto a seguir é um exemplo da saída de regras em vigor retornadas para a NIC *VM1-NIC1* :
   
        NetworkSecurityGroup   : {
                                   "Id": "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/networkSecurityGroups/VM1-NIC1-NSG"
                                 }
        Association            : {
                                   "NetworkInterface": {
                                     "Id": "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/networkInterfaces/VM1-NIC1"
                                   }
                                 }
        EffectiveSecurityRules : [
                                 {
                                 "Name": "securityRules/allowRDP",
                                 "Protocol": "Tcp",
                                 "SourcePortRange": "0-65535",
                                 "DestinationPortRange": "3389-3389",
                                 "SourceAddressPrefix": "Internet",
                                 "DestinationAddressPrefix": "0.0.0.0/0",
                                 "ExpandedSourceAddressPrefix": [… ],
                                 "ExpandedDestinationAddressPrefix": [],
                                 "Access": "Allow",
                                 "Priority": 1000,
                                 "Direction": "Inbound"
                                 },
                                 {
                                 "Name": "defaultSecurityRules/AllowVnetInBound",
                                 "Protocol": "All",
                                 "SourcePortRange": "0-65535",
                                 "DestinationPortRange": "0-65535",
                                 "SourceAddressPrefix": "VirtualNetwork",
                                 "DestinationAddressPrefix": "VirtualNetwork",
                                 "ExpandedSourceAddressPrefix": [
                                  "10.9.0.0/16",
                                  "168.63.129.16/32",
                                  "10.0.0.0/16",
                                  "10.1.0.0/16"
                                  ],
                                 "ExpandedDestinationAddressPrefix": [
                                  "10.9.0.0/16",
                                  "168.63.129.16/32",
                                  "10.0.0.0/16",
                                  "10.1.0.0/16"
                                  ],
                                  "Access": "Allow",
                                  "Priority": 65000,
                                  "Direction": "Inbound"
                                  },…
                         ]
   
        NetworkSecurityGroup   : {
                                   "Id": 
                                 "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/networkSecurityGroups/Subnet1-NSG"
                                 }
        Association            : {
                                   "Subnet": {
                                     "Id": 
                                 "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/virtualNetworks/WestUS-VNet1/subnets/Subnet1"
                                 }
                                 }
        EffectiveSecurityRules : [
                                 {
                                "Name": "securityRules/denyRDP",
                                "Protocol": "Tcp",
                                "SourcePortRange": "0-65535",
                                "DestinationPortRange": "3389-3389",
                                "SourceAddressPrefix": "Internet",
                                "DestinationAddressPrefix": "0.0.0.0/0",
                                "ExpandedSourceAddressPrefix": [
                                   ... ],
                                "ExpandedDestinationAddressPrefix": [],
                                "Access": "Deny",
                                "Priority": 1000,
                                "Direction": "Inbound"
                                },
                                {
                                "Name": "defaultSecurityRules/AllowVnetInBound",
                                "Protocol": "All",
                                "SourcePortRange": "0-65535",
                                "DestinationPortRange": "0-65535",
                                "SourceAddressPrefix": "VirtualNetwork",
                                "DestinationAddressPrefix": "VirtualNetwork",
                                "ExpandedSourceAddressPrefix": [
                                "10.9.0.0/16",
                                "168.63.129.16/32",
                                "10.0.0.0/16",
                                "10.1.0.0/16"
                                ],
                                "ExpandedDestinationAddressPrefix": [
                                "10.9.0.0/16",
                                "168.63.129.16/32",
                                "10.0.0.0/16",
                                "10.1.0.0/16"
                                ],
                                "Access": "Allow",
                                "Priority": 65000,
                                "Direction": "Inbound"
                                },...
                                ]
   
    Observe as seguintes informações na saída:
   
   * Há duas seções **NetworkSecurityGroup**: uma é associada a uma sub-rede (*Subnet1*) e a outra é associada a uma NIC (*VM1-NIC1*). Neste exemplo, um NSG foi aplicado a cada uma delas.
   * **Association** mostra os recursos (sub-rede ou NIC) aos quais um determinado NSG foi associado. Se o recurso NSG for desassociado/movido imediatamente antes de executar esse comando, você precisará aguardar alguns segundos para que a alteração reflita na saída do comando. 
   * Os nomes de regra precedidos por *defaultSecurityRules*: quando um NSG é criado, várias regras de segurança padrão são criadas com ele. Regras padrão não podem ser removidas, mas podem ser substituídas por regras com prioridade mais alta.
     Leia o artigo [Visão geral do NSG](virtual-networks-nsg.md#default-rules) para saber mais sobre regras de segurança padrão do NSG.
   * **ExpandedAddressPrefix** expande os prefixos de endereço para as marcações padrão do NSG. As marcações representam vários prefixos de endereço. A expansão das marcações pode ser útil ao solucionar problemas de conectividade da VM para/de prefixos de endereço específico. Por exemplo, com o emparelhamento da VNET, a marcação VIRTUAL_NETWORK se expande para mostrar prefixos da VNet emparelhados na saída anterior.
     
     > [!NOTE]
     > O comando mostrará somente regras em vigor se um NSG estiver associado a uma sub-rede, a uma NIC ou a ambas. Uma VM pode ter várias NICs com diferentes NSGs aplicados. Ao solucionar problemas, execute o comando para cada NIC.
     > 
     > 
3. Para facilitar a filtragem em um número maior de regras do NSG, digite os comandos a seguir para solucionar outros problemas: 
   
        $NSGs = Get-AzureRmEffectiveNetworkSecurityGroup -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1
        $NSGs.EffectiveSecurityRules | Sort-Object Direction, Access, Priority | Out-GridView
   
    Um filtro para o tráfego RDP (porta TCP 3389) é aplicado à exibição de grade, conforme mostrado na imagem a seguir:
   
    ![Lista de regras](./media/virtual-network-nsg-troubleshoot-powershell/rules.png)
4. Como você pode ver no modo de exibição de grade, há duas regras permitir e negar para RDP. A saída da etapa 2 mostra que a regra *DenyRDP* está no NSG aplicado à sub-rede. Para regras de entrada, os NSGs aplicados à sub-rede são processados primeiro. Se uma correspondência for encontrada, o NSG aplicado ao adaptador de rede não será processado. Nesse caso, a regra *DenyRDP* da sub-rede bloqueia o RDP para a VM (**VM1**).
   
   > [!NOTE]
   > Uma VM pode ter várias NICs conectadas a ele. Cada uma pode estar conectada a uma sub-rede diferente. Como os comandos nas etapas anteriores são executados em relação a uma NIC, é importante garantir que você especifique a NIC que está apresentando falha na conectividade. Se você não tiver certeza, você sempre poderá executar os comandos com relação a cada NIC conectada à VM.
   > 
   > 
5. Para o RDP na VM1, altere a regra *Negar RDP (3389)* para *Permitir RDP(3389)* no NSG **Subnet1-NSG**. Confirme que a porta TCP 3389 está aberta abrindo uma conexão RDP com a VM ou usando a ferramenta PsPing. Você pode saber mais sobre o PsPing lendo a [página de download do PsPing](https://technet.microsoft.com/sysinternals/psping.aspx)
   
    Você pode adicionar ou remove regras de um NSG usando as informações na saída com o seguinte comando:
   
        Get-Help *-AzureRmNetworkSecurityRuleConfig

## <a name="considerations"></a>Considerações
Considere os seguintes pontos ao solucionar problemas de conectividade:

* As regras NSG padrão bloquearão o acesso de entrada da Internet e permitirão apenas o tráfego de entrada da VNet. As regras devem ser adicionadas explicitamente para permitir acesso de entrada da Internet, conforme necessário.
* Se não houver nenhuma regra de segurança NSG que faça a conectividade de rede da VM falhar, o problema poderá ser devido a:
  * Software de firewall em execução no sistema operacional da VM
  * Rotas configuradas para soluções de virtualização ou tráfego local. O tráfego da Internet pode ser redirecionado para o local por meio do túnel forçado. Uma conexão RDP/SSH da Internet para sua VM pode não funcionar com essa configuração, dependendo de como o hardware de rede local trata esse tráfego. Leia o artigo [Solucionando problemas de rotas](virtual-network-routes-troubleshoot-powershell.md) para saber como diagnosticar problemas de rotas que podem estar impedindo o fluxo de tráfego de entrada e saída da VM. 
* Se você tiver VNets emparelhadas, por padrão, a marcação VIRTUAL_NETWORK expandirá automaticamente para incluir prefixos para VNets emparelhadas. Você pode exibir esses prefixos na lista **ExpandedAddressPrefix** , para solucionar os problemas relacionados à conectividade de emparelhamento da VNet. 
* As regras de segurança em vigor serão mostradas apenas se houver um NSG associado à NIC e/ou sub-rede da VM. 
* Se não houver nenhum NSG associado à NIC ou à sub-rede e você tiver um endereço IP público atribuído à sua VM, todas as portas estarão abertas para acesso de entrada e saída. Se a VM tem um endereço IP público, recomenda-se aplicar NSGs à NIC ou à sub-rede.  

