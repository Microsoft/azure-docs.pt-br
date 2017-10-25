---
title: "VM com vários endereços IP usando a CLI do Azure 1.0 | Microsoft Docs"
description: "Saiba como atribuir diversos endereços IP a uma máquina virtual usando a CLI do Azure 1.0 | Resource Manager."
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/17/2016
ms.author: annahar
ms.openlocfilehash: 9f085dfa1fe4db36d58cb976bb550a46bf241ac7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-azure-cli-10"></a>Atribuir vários endereços IP a máquinas virtuais usando a CLI do Azure 1.0

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

Este artigo explica como criar uma máquina virtual (VM) por meio do Modelo de implantação do Azure Resource Manager usando a CLI do Azure 1.0. Múltiplos endereços IP não podem ser atribuídos a recursos criados por meio do modelo de implantação clássico. Para saber mais sobre modelos de implantação do Azure, leia o artigo [Compreender os modelos de implantação](../resource-manager-deployment-model.md).

[!INCLUDE [virtual-network-multiple-ip-addresses-template-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name = "create"></a>Criar uma VM com vários endereços IP

Você pode concluir esta tarefa usando a CLI do Azure 1.0 (este artigo) ou a [CLI do Azure 2.0](virtual-network-multiple-ip-addresses-cli.md). As etapas a seguir explicam como criar uma VM de exemplo com vários endereços IP, como descrito no cenário. Altere os nomes da variável e os tipos de endereço IP como exigido por sua implementação.

1. Instale e configure a CLI do Azure 1.0 seguindo as etapas do artigo [Instalar e configurar a CLI do Azure](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e faça logon em sua conta do Azure com o comando `azure-login`.

2. Crie um grupo de recursos:
    
    ```azurecli
    RgName=myResourceGroup
    Location=westcentralus
    azure group create --name $RgName --location $Location
    ```
3. Criar uma rede virtual:

    ```azurecli
    azure network vnet create --resource-group $RgName --location $Location --name myVNet \
    --address-prefixes 10.0.0.0/16
    ```
4. Crie uma sub-rede na rede virtual:

    ```azurecli
    azure network vnet subnet create --name mySubnet --resource-group $RgName --vnet-name myVNet \
    --address-prefix 10.0.0.0/24
    ```
    
5. Crie uma conta de armazenamento para a VM. Antes de executar o comando a seguir, substitua *mystorageaccount* por um nome exclusivo. O nome deve ser exclusivo no Azure:

    ```azurecli
    az storage account create --resource-group $RgName --location $Location --name mystorageaccount \
    --kind Storage --sku Standard_LRS
    ```

6. Crie as configurações de IP, uma NIC e atribua as configurações de IP à NIC. Você pode adicionar, remover ou alterar as configurações conforme necessário. As configurações a seguir são descritas no cenário:

    **IPConfig-1**

    Insira os comandos a seguir para criar:

    - Um recurso de endereço IP público com um endereço IP público estático
    - Uma NIC, atribuindo o endereço IP público e um endereço IP privado estático a ela.
    
    Substitua *mypublicdns* por um nome que seja exclusivo no local do Azure.

      ```azurecli
      azure network public-ip create --resource-group $RgName --location $Location --name myPublicIP1 \
      --domain-name-label mypublicdns --allocation-method Static
        
      azure network nic create --resource-group $RgName --location $Location --name myNic1 \
      --private-ip-address 10.0.0.4 --subnet-name mySubnet --subnet-vnet-name myVNet \
      --subnet-name mySubnet --public-ip-name myPublicIP1
      ```

      > [!NOTE]
      > Endereços IP públicos têm um valor nominal. Para saber mais sobre preços de endereço IP, leia a página [Preços de endereço IP](https://azure.microsoft.com/pricing/details/ip-addresses) . Há um limite para o número de endereços IP públicos que podem ser usados em uma assinatura. Para saber mais sobre os limites, leia o artigo [Limites do Azure](../azure-subscription-service-limits.md#networking-limits).

    **IPConfig-2**

     Insira os seguintes comandos para criar um novo recurso de endereço IP público e uma nova configuração de IP com um endereço IP público estático e um endereço IP privado estático:
    
      ```azurecli
      azure network public-ip create --resource-group $RgName --location $Location --name myPublicIP2
      --domain-name-label mypublicdns2 --allocation-method Static

      azure network nic ip-config create --resource-group $RgName --nic-name myNic1 --name IPConfig-2
      --private-ip-address 10.0.0.5 --public-ip-name myPublicIP2
      ```

    **IPConfig-3**

    Insira os seguintes comandos para criar uma configuração de IP com um endereço IP privado estático e nenhum endereço IP público:

      ```azurecli
      azure network nic ip-config create --resource-group $RgName --nic-name myNic1 --private-ip-address 10.0.0.6 \
      --name IPConfig-3
      ```

    >[!NOTE] 
    >Embora este artigo atribua todas as configurações de IP a uma única NIC, você também pode atribuir várias configurações de IP para qualquer NIC em uma VM. Para saber como criar uma VM com várias NICs, leia o artigo Criar uma VM com várias NICs.

7. Criar uma VM do Linux 

    ```azurecli
    az vm create --resource-group $RgName --name myVM1 --location $Location --nics myNic1 \
    --image UbuntuLTS --ssh-key-value ~/.ssh/id_rsa.pub --admin-username azureuser
    ```

    Para alterar o tamanho da VM para Standard DS2 v2, por exemplo, basta adicionar a seguinte propriedade `--vm-size Standard_DS3_v2` ao comando `azure vm create` na etapa 6.

8. Insira o comando a seguir para exibir a NIC e as configurações de IP associadas:

    ```azurecli
    azure network nic show --resource-group $RgName --name myNic1
    ```
9. Adicione os endereços IP privados ao sistema operacional da VM executando as etapas para seu sistema operacional na seção [Adicionar endereços IP ao sistema operacional de uma VM](#os-config) deste artigo.

## <a name="add"></a>Adicionar endereços IP a uma VM

Você pode adicionar endereços IP públicos e privados adicionais para um NIC existente ao concluir as etapas a seguir. Os exemplos baseiam-se no [cenário](#Scenario) descrito neste artigo.

1. Abra a CLI do Azure e conclua as etapas restantes nesta seção dentro de uma única sessão da CLI. Se você ainda não tiver a CLI do Azure instalada e configurada, conclua as etapas do artigo [Instalar e configurar a CLI do Azure](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e faça logon em sua conta do Azure.

2. Conclua as etapas em uma das seções a seguir, com base em seus requisitos:

    - **Adicionar um endereço IP privado**
    
        Para adicionar um endereço IP privado a uma NIC, você deve criar uma configuração de IP usando o comando a seguir. O endereço estático deve ser um endereço não usado para a sub-rede.

        ```azurecli
        azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic1 \
        --private-ip-address 10.0.0.7 --name IPConfig-4
        ```
        Crie quantas configurações forem necessárias, usando nomes de configuração exclusivos e endereços IP privados (para configurações com endereços IP estáticos).

    - **Adicionar um endereço IP público**
    
        Um endereço IP público é adicionado por meio de sua associação a uma nova configuração de IP ou de uma configuração de IP existente. Conclua as etapas em uma das seções a seguir, quando você precisar.

        > [!NOTE]
        > Endereços IP públicos têm um valor nominal. Para saber mais sobre preços de endereço IP, leia a página [Preços de endereço IP](https://azure.microsoft.com/pricing/details/ip-addresses) . Há um limite para o número de endereços IP públicos que podem ser usados em uma assinatura. Para saber mais sobre os limites, leia o artigo [Limites do Azure](../azure-subscription-service-limits.md#networking-limits).
        >

        **Associar o recurso a uma nova configuração de IP**
    
        Sempre que você adicionar um endereço IP público em uma nova configuração de IP, também deverá adicionar um endereço IP privado, pois todas as configurações de IP devem ter um endereço IP privado. Você pode adicionar um recurso de endereço IP público existente ou criar um novo. Para criar um novo, insira o seguinte comando:

        ```azurecli
        azure network public-ip create --resource-group myResourceGroup --location westcentralus --name myPublicIP3 \
        --domain-name-label mypublicdns3
        ```

        Para criar uma nova configuração de IP com um endereço IP privado estático e o recurso de endereço IP público *myPublicIP3* associado, insira o seguinte comando:

        ```azurecli
        azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic --name IPConfig-4 \
        --private-ip-address 10.0.0.8 --public-ip-name myPublicIP3
        ```

        **Associar o recurso a uma configuração de IP existente**

        Um recurso de endereço IP público só pode ser associado a uma configuração de IP que ainda não tenha um associado. Você pode determinar se uma configuração de IP tem um endereço IP público associado inserindo o seguinte comando:

        ```azurecli
        azure network nic ip-config list --resource-group myResourceGroup --nic-name myNic1
        ```

        Procure uma linha semelhante à linha que segue IPConfig-3 na saída retornada:

        ```         
        Name               Provisioning state  Primary  Private IP allocation Private IP version  Private IP address  Subnet    Public IP
        default-ip-config  Succeeded           true     Static                IPv4                10.0.0.4            mySubnet  myPublicIP
        IPConfig-2         Succeeded           false    Static                IPv4                10.0.0.5            mySubnet  myPublicIP2
        IPConfig-3         Succeeded           false    Static                IPv4                10.0.0.6            mySubnet
        ```
          
        Como a coluna **IP Público** para *IpConfig 3* está em branco, nenhum recurso de endereço IP público está associado a ele no momento. Você pode adicionar um recurso de endereço IP público existente como IpConfig-3 ou inserir o seguinte comando para criar um:

        ```azurecli
        azure network public-ip create --resource-group  myResourceGroup --location westcentralus \
        --name myPublicIP3 --domain-name-label mypublicdns3 --allocation-method Static
        ```

        Insira o comando a seguir para associar o recurso de endereço IP público à configuração de IP existente chamada *IPConfig-3*:
        ```azurecli
        azure network nic ip-config set --resource-group myResourceGroup --nic-name myNic1 --name IPConfig-3 \
        --public-ip-name myPublicIP3
        ```

3. Exiba os endereços IP privados e o recurso de endereço IP público atribuído à NIC, digitando o seguinte comando:

    ```azurecli
    azure network nic ip-config list --resource-group myResourceGroup --nic-name myNic1
    ```

      A saída retornada é semelhante a esta:
      ```
      Name               Provisioning state  Primary  Private IP allocation Private IP version  Private IP address  Subnet    Public IP
        
      default-ip-config  Succeeded           true     Static                IPv4                10.0.0.4            mySubnet  myPublicIP
      IPConfig-2         Succeeded           false    Static                IPv4                10.0.0.5            mySubnet  myPublicIP2
      IPConfig-3         Succeeded           false    Static                IPv4                10.0.0.6            mySubnet  myPublicIP3
      ```
4. Adicione os endereços IP privados que você adicionou à NIC para o sistema operacional da VM seguindo as instruções da seção [Adicionar endereços IP a um sistema operacional de VM](#os-config) deste artigo. Não adicione os endereços IP públicos ao sistema operacional.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
