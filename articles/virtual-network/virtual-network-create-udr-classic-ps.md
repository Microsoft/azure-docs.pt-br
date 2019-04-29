---
title: Controlar o roteamento em uma rede virtual do Azure - PowerShell - Clássico | Microsoft Docs
description: Aprenda a controlar o roteamento em VNets usando o PowerShell | Classico
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: d8d07c16-cbe5-4536-acd6-870269346fe3
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: genli
ms.openlocfilehash: 1441ee9a3d4a563ab35cd9b01e8347d8f51b827a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60743339"
---
# <a name="control-routing-and-use-virtual-appliances-classic-using-powershell"></a>Controlar o roteamento e usar dispositivos virtuais (clássico) usando o PowerShell

> [!div class="op_single_selector"]
> * [PowerShell](tutorial-create-route-table-powershell.md)
> * [CLI do Azure](tutorial-create-route-table-cli.md)
> * [PowerShell (Clássico)](virtual-network-create-udr-classic-ps.md)
> * [CLI (Clássica)](virtual-network-create-udr-classic-cli.md)

[!INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

> [!IMPORTANT]
> Antes de trabalhar com recursos do Azure, é importante entender que, no momento, o Azure tem dois modelos de implantação: Azure Resource Manager e clássico. Verifique se você entendeu [os modelos e as ferramentas de implantação](../azure-resource-manager/resource-manager-deployment-model.md) antes de trabalhar com qualquer recurso do Azure. Você pode exibir a documentação para ferramentas diferentes selecionando uma opção na parte superior deste artigo. Este artigo aborda o modelo de implantação clássico.
> 

[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

O exemplo de comando do Azure PowerShell abaixo espera um ambiente simples já criado com base no cenário acima. Se você quiser executar os comandos da forma como eles aparecem neste documento, crie o ambiente descrito em [criar uma VNet (clássica) usando o PowerShell](virtual-networks-create-vnet-classic-netcfg-ps.md).

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>Criar o UDR para a sub-rede de front-end
Para criar a tabela de rotas e a rota necessária para a sub-rede de front-end com base no cenário acima, siga as etapas abaixo.

1. Execute o comando a seguir para criar uma tabela de rota para a sub-rede de front-end:

    ```powershell
    New-AzureRouteTable -Name UDR-FrontEnd -Location uswest `
    -Label "Route table for front end subnet"
    ```

2. Execute o comando a seguir para criar uma rota na tabela de rotas para enviar todo o tráfego destinado à sub-rede de back-end (192.168.2.0/24) para a VM **FW1** (192.168.0.4):

    ```powershell
    Get-AzureRouteTable UDR-FrontEnd `
    |Set-AzureRoute -RouteName RouteToBackEnd -AddressPrefix 192.168.2.0/24 `
    -NextHopType VirtualAppliance `
    -NextHopIpAddress 192.168.0.4
    ```

3. Execute o comando a seguir para associar a tabela de rotas à sub-rede de **FrontEnd**:

    ```powershell
    Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
    -SubnetName FrontEnd `
    -RouteTableName UDR-FrontEnd
    ```

## <a name="create-the-udr-for-the-back-end-subnet"></a>Criar o UDR para a sub-rede de back-end
Para criar a tabela de rotas e a rota necessária para a sub-rede de back-end com base no cenário, siga as seguintes etapas:

1. Execute o comando a seguir para criar uma tabela de rota para a sub-rede de back-end:

    ```powershell
    New-AzureRouteTable -Name UDR-BackEnd `
    -Location uswest `
    -Label "Route table for back end subnet"
    ```

2. Execute o comando a seguir para criar uma rota na tabela de rotas para enviar todo o tráfego destinado à sub-rede de front-end (192.168.1.0/24) para a VM **FW1** (192.168.0.4):

    ```powershell
    Get-AzureRouteTable UDR-BackEnd
    | Set-AzureRoute `
    -RouteName RouteToFrontEnd `
    -AddressPrefix 192.168.1.0/24 `
    -NextHopType VirtualAppliance `
    -NextHopIpAddress 192.168.0.4
    ```

3. Execute o comando a seguir para associar a tabela de rotas à sub-rede de **BackEnd**:

    ```powershell
    Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
    -SubnetName BackEnd `
    -RouteTableName UDR-BackEnd
    ```

## <a name="enable-ip-forwarding-on-the-fw1-vm"></a>Habilitar o encaminhamento IP na VM FW1

Para habilitar o encaminhamento de IP na VM FW1, siga as seguintes etapas:

1. Execute o seguinte comando para verificar o status do encaminhamento de IP:

    ```powershell
    Get-AzureVM -Name FW1 -ServiceName TestRGFW `
    | Get-AzureIPForwarding
    ```

2. Execute o comando a seguir para habilitar o encaminhamento de IP para a VM *FW1*:

    ```powershell
    Get-AzureVM -Name FW1 -ServiceName TestRGFW `
    | Set-AzureIPForwarding -Enable
    ```
