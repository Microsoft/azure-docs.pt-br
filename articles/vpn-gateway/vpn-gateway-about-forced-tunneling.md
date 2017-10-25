---
title: "Configurar o túnel forçado para conexões Site a Site: clássico | Microsoft Docs"
description: "Como redirecionar ou 'forçar' todo o tráfego direcionado à Internet para sua localização local."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 5c0177f1-540c-4474-9b80-f541fa44240b
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/01/2017
ms.author: cherylmc
ms.openlocfilehash: 79bf6892c823da282c3e763921e830f986419854
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configure-forced-tunneling-using-the-classic-deployment-model"></a>Configurar o túnel forçado usando o modelo de implantação clássico

O túnel forçado permite redirecionar ou "forçar" todo o tráfego direcionado para a Internet de volta para seu local por meio de um túnel VPN de Site a Site para inspeção e auditoria. Esse é um requisito crítico de segurança para a maioria das políticas de TI empresariais. Sem o túnel forçado, o tráfego direcionado para Internet de suas VMs no Azure sempre percorrerão da infraestrutura de rede do Azure diretamente para a Internet, sem a opção para permitir que você inspecione ou audite o tráfego. O acesso não autorizado à Internet pode levar à divulgação de informações ou outros tipos de violações de segurança.

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Este artigo o guia pela configuração de túnel forçado para redes virtuais criadas usando o modelo de implantação clássico. O túnel forçado pode ser configurado usando o PowerShell, não por meio do portal. Se você quiser configurar o túnel forçado para o modelo de implantação do Resource Manager, selecione o artigo clássico na lista suspensa abaixo:

> [!div class="op_single_selector"]
> * [PowerShell - clássico](vpn-gateway-about-forced-tunneling.md)
> * [PowerShell – Resource Manager](vpn-gateway-forced-tunneling-rm.md)
> 
> 

## <a name="requirements-and-considerations"></a>Requisitos e considerações
O túnel forçado no Azure é configurado por meio de UDR (rotas de definidas pelo usuário) de rede virtual. Redirecionar o tráfego para um site local é expressado como uma Rota Padrão para o gateway de VPN do Azure. A seção a seguir lista a limitação atual da tabela de roteamento e as rotas para uma Rede Virtual do Azure:

* Cada sub-rede de rede virtual tem uma tabela de roteamento interna do sistema. A tabela de roteamento do sistema tem estes três grupos de rotas:

  * **Rotas locais de Rede Virtual:** diretamente para as VMs de destino na mesma rede virtual.
  * **Rotas locais:** para o gateway de VPN do Azure.
  * **Rota padrão:** diretamente para a Internet. Os pacotes destinados para os endereços IP privados não cobertos pelas duas rotas anteriores serão removidos.
* Com a liberação de rotas definidas pelo usuário, você poderá criar uma tabela de roteamento para adicionar uma rota padrão e, em seguida, associar a tabela de roteamento às suas sub-redes de VNet para habilitar o túnel forçado nessas sub-redes.
* Você precisa definir um "site padrão" entre sites locais entre locais conectado à rede virtual.
* O túnel forçado deve ser associado a uma Rede Virtual que tem um gateway de VPN de roteamento dinâmico (e não um gateway estático).
* O túnel forçado do ExpressRoute não é configurado por meio deste mecanismo, mas é habilitado por meio do anúncio de uma rota padrão por meio de sessões de emparelhamento via protocolo BGP do ExpressRoute. Confira a [Documentação do ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) para saber mais.

## <a name="configuration-overview"></a>Visão geral de configuração
No exemplo a seguir, a sub-rede Frontend não é um túnel forçado. As cargas de trabalho na sub-rede do front-end podem continuar a aceitar e a responder diretamente às solicitações de clientes da Internet. As sub-redes de Camada intermediária e Back-end são túneis forçados. As conexões de saída dessas duas sub-redes com a Internet serão forçadas ou redirecionadas de volta ao site local por meio de túneis de VPN S2S.

Isso permite que você restrinja e inspecione o acesso à Internet de suas máquinas virtuais ou serviços de nuvem no Azure, continuando a habilitar a arquitetura de serviço de várias camadas necessária. Você também pode aplicar o túnel forçado às redes virtuais inteiras se não houver cargas de trabalho voltadas para a Internet em suas redes virtuais.

![Túnel forçado](./media/vpn-gateway-about-forced-tunneling/forced-tunnel.png)

## <a name="before-you-begin"></a>Antes de começar
Verifique se você tem os itens a seguir antes de iniciar a configuração.

* Uma assinatura do Azure. Se ainda não tiver uma assinatura do Azure, você poderá ativar os [Benefícios do assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se para obter uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Uma rede virtual configurada. 
* A versão mais recente dos cmdlets do Azure PowerShell. Consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview) para saber mais sobre como instalar os cmdlets do PowerShell.

## <a name="configure-forced-tunneling"></a>Configurar o túnel forçado
O procedimento a seguir ajudará você a especificar um túnel forçado em uma rede virtual. As etapas de configuração correspondem ao arquivo de configuração de rede VNet.

```
<VirtualNetworkSite name="MultiTier-VNet" Location="North Europe">
     <AddressSpace>
      <AddressPrefix>10.1.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Frontend">
            <AddressPrefix>10.1.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Midtier">
            <AddressPrefix>10.1.1.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Backend">
            <AddressPrefix>10.1.2.0/23</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.1.200.0/28</AddressPrefix>
          </Subnet>
        </Subnets>
        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="DefaultSiteHQ">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch1">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch2">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch3">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
        </Gateway>
      </VirtualNetworkSite>
    </VirtualNetworkSite>
```

Nesse exemplo, a rede virtual "MultiTier-VNet" tem três sub-redes:'Frontend', 'Midtier' e 'Backend', com quatro conexões entre locais: ‘DefaultSiteHQ’ e três Branches. 

As etapas vão definir ‘DefaultSiteHQ’ como a conexão de site padrão para o túnel forçado e configurar as sub-redes Midtier e Backend para usarem túnel forçado.

1. Crie uma tabela de roteamento. Use o cmdlet a seguir para criar sua tabela de rotas.

  ```powershell
  New-AzureRouteTable –Name "MyRouteTable" –Label "Routing Table for Forced Tunneling" –Location "North Europe"
  ```
2. Adicione uma rota padrão à tabela de roteamento. 

  O exemplo a seguir adiciona uma rota padrão à tabela de roteamento criada na Etapa 1. Observe que a única rota com suporte é o prefixo de destino de "0.0.0.0/0" para o próximo salto "VPNGateway".

  ```powershell
  Get-AzureRouteTable -Name "MyRouteTable" | Set-AzureRoute –RouteTable "MyRouteTable" –RouteName "DefaultRoute" –AddressPrefix "0.0.0.0/0" –NextHopType VPNGateway
  ```
3. Associe a tabela de roteamento às sub-redes. 

  Depois que uma tabela de roteamento for criada e uma rota adicionada, use o exemplo a seguir para adicionar ou associar a tabela de rotas a uma sub-rede da VNet. O exemplo adiciona a tabela de rotas "MyRouteTable" às sub-redes Midtier e Backend da VNet MultiTier-VNet.

  ```powershell
  Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Midtier" -RouteTableName "MyRouteTable"
  Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Backend" -RouteTableName "MyRouteTable"
  ```
4. Atribua um site padrão ao túnel forçado. 

  Na etapa anterior, os scripts de cmdlet de exemplo criaram a tabela de roteamento e associou a tabela de rotas a duas sub-redes de rede virtual. A etapa restante é selecionar um site local entre as conexões de vários locais da rede virtual como o site padrão ou túnel.

  ```powershell
  $DefaultSite = @("DefaultSiteHQ")
  Set-AzureVNetGatewayDefaultSite –VNetName "MultiTier-VNet" –DefaultSite "DefaultSiteHQ"
  ```

## <a name="additional-powershell-cmdlets"></a>Cmdlets do PowerShell adicionais
### <a name="to-delete-a-route-table"></a>Para excluir uma tabela de rotas

```powershell
Remove-AzureRouteTable -Name <routeTableName>
```
  
### <a name="to-list-a-route-table"></a>Para listar uma tabela de rotas

```powershell
Get-AzureRouteTable [-Name <routeTableName> [-DetailLevel <detailLevel>]]
```

### <a name="to-delete-a-route-from-a-route-table"></a>Para excluir uma rota de uma tabela de rotas

```powershell
Remove-AzureRouteTable –Name <routeTableName>
```

### <a name="to-remove-a-route-from-a-subnet"></a>Para remover uma rota de uma sub-rede

```powershell
Remove-AzureSubnetRouteTable –VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>
```

### <a name="to-list-the-route-table-associated-with-a-subnet"></a>Para listar a tabela de rotas associadas a uma sub-rede

```powershell
Get-AzureSubnetRouteTable -VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>
```

### <a name="to-remove-a-default-site-from-a-vnet-vpn-gateway"></a>Para remover um site padrão de um gateway de VPN de VNet

```powershell
Remove-AzureVnetGatewayDefaultSite -VNetName <virtualNetworkName>
```