---
title: 'WAN virtual: criar tabela de rotas de Hub virtual para NVA: Azure PowerShell'
description: Tabela de rotas do hub virtual da WAN Virtual para conduzir o tráfego para uma solução de virtualização de rede.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to work with routing tables for NVA.
ms.openlocfilehash: 40154a9c3cefed69bd3f1639153099b944da79b5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91267118"
---
# <a name="create-a-virtual-hub-route-table-to-steer-traffic-to-a-network-virtual-appliance"></a>Criar uma tabela de rotas do Hub Virtual para conduzir o tráfego para uma solução de virtualização de rede

Este artigo mostra como conduzir tráfego de um Hub Virtual para uma Solução de Virtualização de Rede. 

![Diagrama de WAN virtual](./media/virtual-wan-route-table-nva/vwanroute.png)

Neste artigo, você aprenderá a:

* Criar uma WAN
* Criar um hub
* Criar conexões de rede virtual do hub
* Criar uma rota do hub
* Criar uma tabela de rotas
* Aplicar a tabela de rotas

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Verifique se você atende aos seguintes critérios:

* Você tem uma NVA (Solução de Virtualização da Rede). Esse é um software de terceiros de sua escolha que normalmente é provisionado do Azure Marketplace em uma rede virtual.
* Você tem um IP privado atribuído ao adaptador de rede da NVA. 
* O NVA não pode ser implantado no Hub virtual. Ela deve ser implantada em uma VNet separada. Neste artigo, a VNet NVA é chamada de 'VNet DMZ'.
* A “VNet DMZ” pode ter uma ou várias redes virtuais conectadas a ela. Neste artigo, essa VNet é chamada de “VNet de spoke indireto”. Essas VNets podem ser conectadas à rede VNets DMZ usando o emparelhamento VNet.
* Verifique se você tem duas VNets já criadas. Elas serão usadas como VNets spoke. Neste artigo, os espaços de endereço da VNets spoke são 10.0.2.0/24 e 10.0.3.0/24. Se você precisar obter informações sobre como criar uma VNets, consulte [Criar uma rede virtual usando o PowerShell](../virtual-network/quick-create-powershell.md).
* Certifique-se de que não há nenhum gateway de rede virtual nas VNets.

## <a name="1-sign-in"></a><a name="signin"></a>1. Entrar

Certifique-se de instalar a versão mais recente dos cmdlets do PowerShell do Resource Manager. Para saber mais sobre como instalar cmdlets do PowerShell, confira [Como instalar e configurar o Azure PowerShell](/powershell/azure/install-az-ps). Isso é importante porque as versões anteriores dos cmdlets não contêm os valores atuais que são necessários para este exercício.

1. Abra o console do PowerShell com privilégios elevados e entre na sua conta do Azure. Esse cmdlet solicita as credenciais de entrada. Depois de entrar, ele baixa as configurações da conta para que elas estejam disponíveis para o Azure PowerShell.

   ```powershell
   Connect-AzAccount
   ```
2. Obtenha uma lista das assinaturas do Azure.

   ```powershell
   Get-AzSubscription
   ```
3. Especifique a assinatura que você deseja usar.

   ```powershell
   Select-AzSubscription -SubscriptionName "Name of subscription"
   ```

## <a name="2-create-resources"></a><a name="rg"></a>2. criar recursos

1. Crie um grupos de recursos.

   ```powershell
   New-AzResourceGroup -Location "West US" -Name "testRG"
   ```
2. Crie uma WAN virtual.

   ```powershell
   $virtualWan = New-AzVirtualWan -ResourceGroupName "testRG" -Name "myVirtualWAN" -Location "West US"
   ```
3. Crie um hub virtual.

   ```powershell
   New-AzVirtualHub -VirtualWan $virtualWan -ResourceGroupName "testRG" -Name "westushub" -AddressPrefix "10.0.1.0/24" -Location "West US"
   ```

## <a name="3-create-connections"></a><a name="connections"></a>3. criar conexões

Crie conexões de rede virtual de hub da VNet de spoke indireto e a VNet DMZ para o hub virtual.

  ```powershell
  $remoteVirtualNetwork1= Get-AzVirtualNetwork -Name "indirectspoke1" -ResourceGroupName "testRG"
  $remoteVirtualNetwork2= Get-AzVirtualNetwork -Name "indirectspoke2" -ResourceGroupName "testRG"
  $remoteVirtualNetwork3= Get-AzVirtualNetwork -Name "dmzvnet" -ResourceGroupName "testRG"

  New-AzVirtualHubVnetConnection -ResourceGroupName "testRG" -VirtualHubName "westushub" -Name  "testvnetconnection1" -RemoteVirtualNetwork $remoteVirtualNetwork1
  New-AzVirtualHubVnetConnection -ResourceGroupName "testRG" -VirtualHubName "westushub" -Name  "testvnetconnection2" -RemoteVirtualNetwork $remoteVirtualNetwork2
  New-AzVirtualHubVnetConnection -ResourceGroupName "testRG" -VirtualHubName "westushub" -Name  "testvnetconnection3" -RemoteVirtualNetwork $remoteVirtualNetwork3
  ```

## <a name="4-create-a-virtual-hub-route"></a><a name="route"></a>4. criar uma rota de Hub virtual

Neste artigo, os espaços de endereço da VNet de spoke indireto são 10.0.2.0/24 e 10.0.3.0/24 e o endereço IP privado do adaptador de rede da NVA DMZ é 10.0.4.5.

```powershell
$route1 = New-AzVirtualHubRoute -AddressPrefix @("10.0.2.0/24", "10.0.3.0/24") -NextHopIpAddress "10.0.4.5"
```

## <a name="5-create-a-virtual-hub-route-table"></a><a name="applyroute"></a>5. criar uma tabela de rotas do Hub virtual

Crie uma tabela de rota do hub virtual e, em seguida, aplique a rota criada.
 
```powershell
$routeTable = New-AzVirtualHubRouteTable -Route @($route1)
```

## <a name="6-commit-the-changes"></a><a name="commit"></a>6. confirmar as alterações

Confirme as alterações no hub virtual.

```powershell
Update-AzVirtualHub -ResourceGroupName "testRG" -Name "westushub" -RouteTable $routeTable
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a WAN Virtual, consulte a página [Visão geral de WAN Virtual](virtual-wan-about.md).
