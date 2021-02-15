---
title: Configurar endereços IP privados para VMs-CLI do Azure
description: Aprenda a configurar endereços IP privados para máquinas virtuais usando a interface de linha de comando (CLI) do Azure.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
editor: ''
tags: azure-resource-manager
ms.assetid: 40b03a1a-ea00-454c-b716-7574cea49ac0
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2017
ms.author: allensu
ms.openlocfilehash: c34ab73422d8dd41feb9da542ed63fdba060fe3f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "84708154"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-cli"></a>Configurar endereços IP privados para uma máquina virtual usando a CLI do Azure


[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

> [!NOTE]
> Os seguintes exemplos de comandos de CLI do Azure esperam um ambiente simples existente. Se você quiser executar os comandos conforme eles são exibidos neste documento, primeiro crie o ambiente de teste descrito em [criar uma vnet](quick-create-cli.md).

## <a name="specify-a-static-private-ip-address-when-creating-a-vm"></a>Especificar um endereço IP estático e privado ao criar uma VM

Para criar uma VM denominada *DNS01* na sub-rede *FrontEnd* de uma VNet chamada *TestVNet* com o endereço IP privado estático *192.168.1.101*, conclua as seguintes etapas:

1. Caso ainda não tenha feito isso, instale e configure a versão mais recente da [CLI do Azure](/cli/azure/install-azure-cli) e faça logon na conta do Azure usando [az login](/cli/azure/reference-index).

2. Execute o comando [az network nic create](/cli/azure/network/nic) para criar uma NIC com um endereço IP privado estático. A lista exibida após a saída explicar os parâmetros usados. 
   
    ```azurecli
    az network nic create \
    --resource-group TestRG \
    --name TestNIC \
    --location centralus \
    --subnet FrontEnd \
    --private-ip-address 192.168.1.101 \
    --vnet-name TestVNet
    ```

    Saída esperada:
   
    ```json
    {
        "newNIC": {
            "dnsSettings": {
            "appliedDnsServers": [],
            "dnsServers": []
            },
            "enableIPForwarding": false,
            "ipConfigurations": [
            {
                "etag": "W/\"<guid>\"",
                "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC/ipConfigurations/ipconfig1",
                "name": "ipconfig1",
                "properties": {
                "primary": true,
                "privateIPAddress": "192.168.1.101",
                "privateIPAllocationMethod": "Static",
                "provisioningState": "Succeeded",
                "subnet": {
                    "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                    "resourceGroup": "TestRG"
                }
                },
                "resourceGroup": "TestRG"
            }
            ],
            "provisioningState": "Succeeded",
            "resourceGuid": "<guid>"
        }
    }
    ```
    
    Parâmetros:

    * `--private-ip-address`: endereço IP privado estático para a NIC.
    * `--vnet-name`: o nome da VNet na qual criar a NIC.
    * `--subnet`: o nome da subrede na qual criar a NIC.

3. Execute o comando [azure vm create](/cli/azure/vm/nic) para criar a VM usando o IP público e a NIC criada anteriormente. A lista exibida após a saída explicar os parâmetros usados.
   
    ```azurecli
    az vm create \
    --resource-group TestRG \
    --name DNS01 \
    --location centralus \
    --image Debian \
    --admin-username adminuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --nics TestNIC
    ```

    Saída esperada:
   
    ```json
    {
        "fqdns": "",
        "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/DNS01",
        "location": "centralus",
        "macAddress": "00-0D-3A-92-C1-66",
        "powerState": "VM running",
        "privateIpAddress": "192.168.1.101",
        "publicIpAddress": "",
        "resourceGroup": "TestRG"
    }
    ```
   
   Parâmetros diferentes dos parâmetros básicos [az vm create](/cli/azure/vm).

   * `--nics`: nome da NIC à qual a VM está conectada.
   
É recomendável que você não atribua estaticamente o IP privado atribuído à máquina virtual do Azure no sistema operacional de uma VM, a menos que seja necessário, como quando [atribuímos vários endereços IP para uma VM do Windows](virtual-network-multiple-ip-addresses-cli.md). Se você definir manualmente o endereço IP privado no sistema operacional, verifique se é o mesmo endereço que o endereço IP privado atribuído ao [adaptador de rede](virtual-network-network-interface-addresses.md#change-ip-address-settings) do Azure ou se é possível perder a conectividade com a máquina virtual. Saiba mais sobre as configurações de [endereço IP privado](virtual-network-network-interface-addresses.md#private).

## <a name="retrieve-static-private-ip-address-information-for-a-vm"></a>Recuperar informações do endereço IP privado estático de uma VM

Execute o seguinte comando de CLI do Azure para observar os valores de *método de alocação de IP privado* e *endereço IP privado*:

```azurecli
az vm show -g TestRG -n DNS01 --show-details --query 'privateIps'
```

Saída esperada:

```json
"192.168.1.101"
```

Para exibir as informações específicas de IP da NIC da VM em questão, consulte a NIC:

```azurecli
az network nic show \
-g testrg \
-n testnic \
--query 'ipConfigurations[0].{PrivateAddress:privateIpAddress,IPVer:privateIpAddressVersion,IpAllocMethod:p
rivateIpAllocationMethod,PublicAddress:publicIpAddress}'
```

A saída será algo semelhante a:

```json
{
    "IPVer": "IPv4",
    "IpAllocMethod": "Static",
    "PrivateAddress": "192.168.1.101",
    "PublicAddress": null
}
```

## <a name="remove-a-static-private-ip-address-from-a-vm"></a>Remover o endereço IP privado estático de uma VM

Você não pode remover um endereço IP privado estático de uma NIC na CLI do Azure para implantações do Azure Resource Manager. Você deve:
- Criar uma nova NIC que usa um IP dinâmico
- Definir a NIC na VM à NIC recém-criada. 

Para alterar a NIC da VM usada em comandos anteriores, conclua as seguintes etapas:

1. Execute o comando **azure network nic create** para criar uma nova NIC usando alocação de IP dinâmico com um endereço IP novo. Como nenhum endereço IP é especificado, o método de alocação será **dinâmico**.

    ```azurecli
    az network nic create     \
    --resource-group TestRG     \
    --name TestNIC2     \
    --location centralus     \
    --subnet FrontEnd    \
    --vnet-name TestVNet
    ```        
   
    Saída esperada:

    ```json
    {
        "newNIC": {
            "dnsSettings": {
            "appliedDnsServers": [],
            "dnsServers": []
            },
            "enableIPForwarding": false,
            "ipConfigurations": [
            {
                "etag": "W/\"<guid>\"",
                "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC2/ipConfigurations/ipconfig1",
                "name": "ipconfig1",
                "properties": {
                "primary": true,
                "privateIPAddress": "192.168.1.4",
                "privateIPAllocationMethod": "Dynamic",
                "provisioningState": "Succeeded",
                "subnet": {
                    "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                    "resourceGroup": "TestRG"
                }
                },
                "resourceGroup": "TestRG"
            }
            ],
            "provisioningState": "Succeeded",
            "resourceGuid": "0808a61c-476f-4d08-98ee-0fa83671b010"
        }
    }
    ```

2. Execute o comando **azure vm set** para alterar a NIC usada pela VM.
   
    ```azurecli
   az vm nic set --resource-group TestRG --vm-name DNS01 --nics TestNIC2
    ```

    Saída esperada:
   
    ```json
    [
        {
            "id": "/subscriptions/0e220bf6-5caa-4e9f-8383-51f16b6c109f/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC3",
            "primary": true,
            "resourceGroup": "TestRG"
        }
    ]
    ```

    > [!NOTE]
    > Se a VM for grande o suficiente para ter mais de uma NIC, execute o comando **azure network nic delete** para excluir a NIC antiga.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as configurações de [endereço IP privado](virtual-network-network-interface-addresses.md).
