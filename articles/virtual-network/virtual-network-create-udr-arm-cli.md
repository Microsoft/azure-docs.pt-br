---
title: Controlar dispositivos virtuais e de roteamento usando a CLI do Azure 2.0 | Microsoft Docs
description: Aprenda a controlar dispositivos virtuais e de roteamento usando a CLI do Azure 2.0.
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 5452a0b8-21a6-4699-8d6a-e2d8faf32c25
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/12/2017
ms.author: jdial
ms.openlocfilehash: e5d9519998346619093f443b740c8904283f76e8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-user-defined-routes-udr-using-the-azure-cli-20"></a>Criar UDRs (Rotas Definidas pelo Usuário) usando a CLI do Azure 2.0

> [!div class="op_single_selector"]
> * [PowerShell](virtual-network-create-udr-arm-ps.md)
> * [CLI do Azure](virtual-network-create-udr-arm-cli.md)
> * [Modelo](virtual-network-create-udr-arm-template.md)
> * [PowerShell (Implantação clássica)](virtual-network-create-udr-classic-ps.md)
> * [CLI (Implantação clássica)](virtual-network-create-udr-classic-cli.md)

## <a name="cli-versions-to-complete-the-task"></a>Versões da CLI para concluir a tarefa 

Você pode concluir a tarefa usando uma das seguintes versões da CLI: 

- [CLI do Azure 1.0](virtual-network-create-udr-arm-cli-nodejs.md) – nossa CLI para os modelos de implantação clássico e de gerenciamento de recursos 
- [CLI do Azure 2.0](#Create-the-UDR-for-the-front-end-subnet) – nossa CLI da próxima geração para o modelo de implantação do resource manager (este artigo)

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

[!INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

Os comandos da CLI do Azure de exemplo abaixo esperam um ambiente simples já criado com base no cenário acima. Se você quiser executar os comandos conforme eles são exibidos neste documento, primeiro crie o ambiente de teste ao implantar [esse modelo](http://github.com/telmosampaio/azure-templates/tree/master/IaaS-NSG-UDR-Before), clique em **Implantar no Azure**, substitua os valores de parâmetro padrão, se necessário, e siga as instruções no portal.


## <a name="create-the-udr-for-the-front-end-subnet"></a>Criar a UDR para a sub-rede de front-end
Para criar a tabela de rotas e a rota necessária para a sub-rede de front-end com base no cenário acima, siga as etapas abaixo.

1. Criar uma tabela de rota para a sub-rede front-end com o comando [az network route-table create](/cli/azure/network/route-table#create):

    ```azurecli
    az network route-table create \
    --resource-group testrg \
    --location centralus \
    --name UDR-FrontEnd
    ```

    Saída:

    ```json
    {
    "etag": "W/\"<guid>\"",
    "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/routeTables/UDR-FrontEnd",
    "location": "centralus",
    "name": "UDR-FrontEnd",
    "provisioningState": "Succeeded",
    "resourceGroup": "testrg",
    "routes": [],
    "subnets": null,
    "tags": null,
    "type": "Microsoft.Network/routeTables"
    }
    ```

2. Crie uma rota que envia todo o tráfego destinado à sub-rede de back-end (192.168.2.0/24) para a VM **FW1** (192.168.0.4) com o comando [az network route-table route create](/cli/azure/network/route-table/route#create):

    ```azurecli 
    az network route-table route create \
    --resource-group testrg \
    --name RouteToBackEnd \
    --route-table-name UDR-FrontEnd \
    --address-prefix 192.168.2.0/24 \
    --next-hop-type VirtualAppliance \
    --next-hop-ip-address 192.168.0.4
    ```

    Saída:

    ```json
    {
    "addressPrefix": "192.168.2.0/24",
    "etag": "W/\"<guid>\"",
    "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/routeTables/UDR-FrontEnd/routes/RouteToBackEnd",
    "name": "RouteToBackEnd",
    "nextHopIpAddress": "192.168.0.4",
    "nextHopType": "VirtualAppliance",
    "provisioningState": "Succeeded",
    "resourceGroup": "testrg"
    }
    ```
    Parâmetros:

    * **--route-table-name**. Nome da tabela de rotas à qual a rota será adicionada. Para nosso cenário, *UDR-FrontEnd*.
    * **--address-prefix**. Prefixo de endereço para a sub-rede à qual os pacotes são destinados. Para nosso cenário, *192.168.2.0/24*.
    * **--next-hop-type**. Tipo de objeto ao qual o tráfego será enviado. Os valores possíveis são *VirtualAppliance*, *VirtualNetworkGateway*, *VNETLocal*, *Internet* ou *None*.
    * **--next-hop-ip-address**. Endereço IP do próximo salto. Para o nosso cenário, *192.168.0.4*.

3. Execute o comando [az network vnet subnet update](/cli/azure/network/vnet/subnet#update) para associar a tabela de rotas criada acima à sub-rede de **FrontEnd**:

    ```azurecli
    az network vnet subnet update \
    --resource-group testrg \
    --vnet-name testvnet \
    --name FrontEnd \
    --route-table UDR-FrontEnd
    ```

    Saída:

    ```json
    {
    "addressPrefix": "192.168.1.0/24",
    "etag": "W/\"<guid>\"",
    "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/FrontEnd",
    "ipConfigurations": null,
    "name": "FrontEnd",
    "networkSecurityGroup": null,
    "provisioningState": "Succeeded",
    "resourceGroup": "testrg",
    "resourceNavigationLinks": null,
    "routeTable": {
        "etag": null,
        "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/routeTables/UDR-FrontEnd",
        "location": null,
        "name": null,
        "provisioningState": null,
        "resourceGroup": "testrg",
        "routes": null,
        "subnets": null,
        "tags": null,
        "type": null
        }
    }
    ```

    Parâmetros:
    
    * **--vnet-name**. Nome da VNet na qual a sub-rede está localizada. Para o nosso cenário, *TestVNet*.

## <a name="create-the-udr-for-the-back-end-subnet"></a>Criar o UDR para a sub-rede de back-end

Para criar a tabela de rotas e a rota necessária para a sub-rede de back-end com base no cenário acima, conclua as etapas a seguir:

1. Execute o comando a seguir para criar uma tabela de rota para a sub-rede de back-end:

    ```azurecli
    az network route-table create \
    --resource-group testrg \
    --name UDR-BackEnd \
    --location centralus
    ```

2. Execute o comando a seguir para criar uma rota na tabela de rotas para enviar todo o tráfego destinado à sub-rede de front-end (192.168.1.0/24) para a VM **FW1** (192.168.0.4):

    ```azurecli
    az network route-table route create \
    --resource-group testrg \
    --name RouteToFrontEnd \
    --route-table-name UDR-BackEnd \
    --address-prefix 192.168.1.0/24 \
    --next-hop-type VirtualAppliance \
    --next-hop-ip-address 192.168.0.4
    ```

3. Execute o comando a seguir para associar a tabela de rotas à sub-rede de **BackEnd**:

    ```azurecli
    az network vnet subnet update \
    --resource-group testrg \
    --vnet-name testvnet \
    --name BackEnd \
    --route-table UDR-BackEnd
    ```

## <a name="enable-ip-forwarding-on-fw1"></a>Habilite o encaminhamento de IP em FW1

Para habilitar o encaminhamento IP na NIC usada por **FW1**, conclua as seguintes etapas:

1. Execute o comando [az network nic show](/cli/azure/network/nic#show) com um filtro JMESPATH para exibir o valor atual de **enable-ip-forwarding** para **Habilitar encaminhamento de IP**. Ele deve ser definido como *falso*.

    ```azurecli
    az network nic show \
    --resource-group testrg \
    --nname nicfw1 \
    --query 'enableIpForwarding' -o tsv
    ```

    Saída:

        false

2. Execute o seguinte comando para habilitar o encaminhamento IP:

    ```azurecli
    az network nic update \
    --resource-group testrg \
    --name nicfw1 \
    --ip-forwarding true
    ```

    Você pode examinar a saída transmitida ao console ou apenas testar novamente para um valor de **enableIpForwarding** específico:

    ```azurecli
    az network nic show -g testrg -n nicfw1 --query 'enableIpForwarding' -o tsv
    ```

    Saída:

        true

    Parâmetros:

    **--ip-forwarding**: *true* ou *false*.

