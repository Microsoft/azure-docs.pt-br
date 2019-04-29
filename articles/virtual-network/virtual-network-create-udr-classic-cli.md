---
title: Controlar o roteamento em uma rede virtual do Azure - CLI - Clássico | Microsoft Docs
description: Aprenda a controlar o roteamento em VNets usando o Azure CLI no modelo de implantação clássico
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: ca2b4638-8777-4d30-b972-eb790a7c804f
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: genli
ms.openlocfilehash: e1b8bb3544a08b60564ceb5bd7e1666214059e09
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60743914"
---
# <a name="control-routing-and-use-virtual-appliances-classic-using-the-azure-cli"></a>Controlar o roteamento e usar dispositivos virtuais (clássico) usando o Azure CLI

> [!div class="op_single_selector"]
> * [PowerShell](tutorial-create-route-table-powershell.md)
> * [CLI do Azure](tutorial-create-route-table-cli.md)
> * [PowerShell (Clássico)](virtual-network-create-udr-classic-ps.md)
> * [CLI (Clássica)](virtual-network-create-udr-classic-cli.md)

[!INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Este artigo aborda o modelo de implantação clássico. Você também pode [controlar o roteamento e usar dispositivos virtuais no modelo de implantação do Gerenciador de Recursos](tutorial-create-route-table-cli.md).

[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

Os comandos da CLI do Azure de exemplo abaixo esperam um ambiente simples já criado com base no cenário acima. Se você quiser executar os comandos da forma como eles aparecem neste documento, crie o ambiente descrito em [criar uma VNet (clássica) usando o Azure CLI](virtual-networks-create-vnet-classic-cli.md).

[!INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>Criar o UDR para a sub-rede de front-end
Para criar a tabela de rotas e a rota necessária para a sub-rede de front-end com base no cenário acima, siga as etapas abaixo.

1. Execute o seguinte comando para alternar para o modo clássico:

    ```azurecli
    azure config mode asm
    ```

    Saída:

        info:    New mode is asm

2. Execute o comando a seguir para criar uma tabela de rota para a sub-rede de front-end:

    ```azurecli
    azure network route-table create -n UDR-FrontEnd -l uswest
    ```
   
    Saída:
   
        info:    Executing command network route-table create
        info:    Creating route table "UDR-FrontEnd"
        info:    Getting route table "UDR-FrontEnd"
        data:    Name                            : UDR-FrontEnd
        data:    Location                        : West US
        info:    network route-table create command OK
   
    Parâmetros:
   
   * **-l (or --location)**. A região do Azure em que o novo NSG será criado. Para o nosso cenário, *westus*.
   * **-n (or --name)**. Nome para o novo NGS. Para o nosso cenário, *NSG-FrontEnd*.
3. Execute o comando a seguir para criar uma rota na tabela de rotas para enviar todo o tráfego destinado à sub-rede de back-end (192.168.2.0/24) para a VM **FW1** (192.168.0.4):

    ```azurecli
    azure network route-table route set -r UDR-FrontEnd -n RouteToBackEnd -a 192.168.2.0/24 -t VirtualAppliance -p 192.168.0.4
    ```

    Saída:
   
        info:    Executing command network route-table route set
        info:    Getting route table "UDR-FrontEnd"
        info:    Setting route "RouteToBackEnd" in a route table "UDR-FrontEnd"
        info:    network route-table route set command OK
   
    Parâmetros:
   
   * **-r (ou --route-table-name)**. Nome da tabela de rotas à qual a rota será adicionada. Para nosso cenário, *UDR-FrontEnd*.
   * **-a (ou --address-prefix)**. Prefixo de endereço para a sub-rede à qual os pacotes são destinados. Para nosso cenário, *192.168.2.0/24*.
   * **-t (ou --next-hop-type)**. Tipo de objeto ao qual o tráfego será enviado. Os valores possíveis são *VirtualAppliance*, *VirtualNetworkGateway*, *VNETLocal*, *Internet* ou *None*.
   * **-p (ou --next-hop-ip-address**). Endereço IP do próximo salto. Para o nosso cenário, *192.168.0.4*.
4. Execute o comando a seguir para associar a tabela de rotas criada à sub-rede de **FrontEnd**:

    ```azurecli
    azure network vnet subnet route-table add -t TestVNet -n FrontEnd -r UDR-FrontEnd
    ```
   
    Saída:
   
        info:    Executing command network vnet subnet route-table add
        info:    Looking up the subnet "FrontEnd"
        info:    Looking up network configuration
        info:    Looking up network gateway route tables in virtual network "TestVNet" subnet "FrontEnd"
        info:    Associating route table "UDR-FrontEnd" and subnet "FrontEnd"
        info:    Looking up network gateway route tables in virtual network "TestVNet" subnet "FrontEnd"
        data:    Route table name                : UDR-FrontEnd
        data:      Location                      : West US
        data:      Routes:
        info:    network vnet subnet route-table add command OK    
   
    Parâmetros:
   
   * **-t (ou --vnet-name)**. Nome da VNet na qual a sub-rede está localizada. Para nosso cenário, *TestVNet*.
   * **-n (ou --subnet-name**. Nome da sub-rede à qual a tabela de rotas será adicionada. Para o nosso cenário, *FrontEnd*.

## <a name="create-the-udr-for-the-back-end-subnet"></a>Criar UDR para a sub-rede de back-end
Para criar a tabela de rotas e a rota necessária para a sub-rede de back-end com base no cenário, siga as seguintes etapas:

1. Execute o comando a seguir para criar uma tabela de rota para a sub-rede de back-end:

    ```azurecli
    azure network route-table create -n UDR-BackEnd -l uswest
    ```

2. Execute o comando a seguir para criar uma rota na tabela de rotas para enviar todo o tráfego destinado à sub-rede de front-end (192.168.1.0/24) para a VM **FW1** (192.168.0.4):

    ```azurecli
    azure network route-table route set -r UDR-BackEnd -n RouteToFrontEnd -a 192.168.1.0/24 -t VirtualAppliance -p 192.168.0.4
    ```

3. Execute o comando a seguir para associar a tabela de rotas à sub-rede de **BackEnd**:

    ```azurecli
    azure network vnet subnet route-table add -t TestVNet -n BackEnd -r UDR-BackEnd
    ```

