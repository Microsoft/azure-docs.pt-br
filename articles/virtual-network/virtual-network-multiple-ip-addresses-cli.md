---
title: VM com vários endereços IP usando a CLI do Azure
titlesuffix: Azure Virtual Network
description: Saiba como atribuir diversos endereços IP a uma máquina virtual usando a interface de linha de comando (CLI) do Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/17/2016
ms.author: kumud
ms.openlocfilehash: b693500e785d41b2ad3339e26dd9fd3505891bc0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60742218"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-cli"></a>Como atribuir vários endereços IP a máquinas virtuais usando a CLI do Azure

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

Este artigo explica como criar uma máquina virtual (VM) por meio do Modelo de implantação do Azure Resource Manager usando a CLI do Azure. Múltiplos endereços IP não podem ser atribuídos a recursos criados por meio do modelo de implantação clássico. Para saber mais sobre modelos de implantação do Azure, leia o artigo [Compreender os modelos de implantação](../resource-manager-deployment-model.md).

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name = "create"></a>Criar uma VM com vários endereços IP

As etapas a seguir explicam como criar uma máquina virtual de exemplo com vários endereços IP, como descrito no cenário. Altere os valores da variável em "" e os tipos de endereço IP conforme exigido por sua implementação. 

1. Instale a [CLI do Azure](/cli/azure/install-azure-cli) se você ainda não tiver instalado.
2. Siga as etapas em [Como criar um par de chaves público e privado SSH para VMs do Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para criar um par de chaves público e privado SSH para VMs do Linux.
3. Faça logon com o comando `az login` de um shell de comando e selecione a assinatura que você está usando.
4. Execute o script a seguir em um computador Linux ou Mac para criar a VM. O script cria um grupo de recursos, uma rede virtual (VNet), uma NIC com três configurações de IP e uma VM com dois NICs anexados a ela. A NIC, o endereço IP público, a rede virtual e os recursos da VM devem existir no mesmo local e assinatura. Embora não seja obrigatório que todos os recursos existam no mesmo grupo de recursos, no script a seguir é obrigatório.

```bash
    
#!/bin/sh
    
RgName="myResourceGroup"
Location="westcentralus"
az group create --name $RgName --location $Location
    
# Create a public IP address resource with a static IP address using the `--allocation-method Static` option. If you
# do not specify this option, the address is allocated dynamically. The address is assigned to the resource from a pool
# of IP addresses unique to each Azure region. Download and view the file from
# https://www.microsoft.com/en-us/download/details.aspx?id=41653 that lists the ranges for each region.

PipName="myPublicIP"

# This name must be unique within an Azure location.
DnsName="myDNSName"

az network public-ip create \
--name $PipName \
--resource-group $RgName \
--location $Location \
--dns-name $DnsName\
--allocation-method Static

# Create a virtual network with one subnet

VnetName="myVnet"
VnetPrefix="10.0.0.0/16"
VnetSubnetName="mySubnet"
VnetSubnetPrefix="10.0.0.0/24"

az network vnet create \
--name $VnetName \
--resource-group $RgName \
--location $Location \
--address-prefix $VnetPrefix \
--subnet-name $VnetSubnetName \
--subnet-prefix $VnetSubnetPrefix

# Create a network interface connected to the subnet and associate the public IP address to it. Azure will create the
# first IP configuration with a static private IP address and will associate the public IP address resource to it.

NicName="MyNic1"
az network nic create \
--name $NicName \
--resource-group $RgName \
--location $Location \
--subnet $VnetSubnet1Name \
--private-ip-address 10.0.0.4
--vnet-name $VnetName \
--public-ip-address $PipName
    
# Create a second public IP address, a second IP configuration, and associate it to the NIC. This configuration has a
# static public IP address and a static private IP address.

az network public-ip create \
--resource-group $RgName \
--location $Location \
--name myPublicIP2 \
--dns-name mypublicdns2 \
--allocation-method Static

az network nic ip-config create \
--resource-group $RgName \
--nic-name $NicName \
--name IPConfig-2 \
--private-ip-address 10.0.0.5 \
--public-ip-name myPublicIP2

# Create a third IP configuration, and associate it to the NIC. This configuration has  static private IP address and   # no public IP address.

az network nic ip-config create \
--resource-group $RgName \
--nic-name $NicName \
--private-ip-address 10.0.0.6 \
--name IPConfig-3

# Note: Though this article assigns all IP configurations to a single NIC, you can also assign multiple IP configurations
# to any NIC in a VM. To learn how to create a VM with multiple NICs, read the Create a VM with multiple NICs 
# article: https://docs.microsoft.com/azure/virtual-network/virtual-network-deploy-multinic-arm-cli.

# Create a VM and attach the NIC.

VmName="myVm"

# Replace the value for the following **VmSize** variable with a value from the
# https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-sizes article. The script fails if the VM size
# is not supported in the location you select. Run the `azure vm sizes --location eastcentralus` command to get a full list
# of VMs in US West Central, for example.

VmSize="Standard_DS1"

# Replace the value for the OsImage variable value with a value for *urn* from the output returned by entering the
# `az vm image list` command.

OsImage="credativ:Debian:8:latest"

Username="adminuser"

# Replace the following value with the path to your public key file. If you're creating a Windows VM, remove the following
# line and you'll be prompted for the password you want to configure for the VM.

SshKeyValue="~/.ssh/id_rsa.pub"

az vm create \
--name $VmName \
--resource-group $RgName \
--image $OsImage \
--location $Location \
--size $VmSize \
--nics $NicName \
--admin-username $Username \
--ssh-key-value $SshKeyValue
```

Além de criar uma VM com uma NIC com três configurações de IP, o script cria:

- Um único disco gerenciado premium por padrão, mas há outras opções para você criar outros tipos de disco. Leia o artigo [Como criar uma VM do Linux usando a CLI do Azure](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para obter detalhes.
- Uma rede virtual com uma sub-rede e dois endereços IP públicos. Como alternativa, você pode usar uma rede virtual, uma sub-rede, uma NIC ou recursos de endereço IP público *existentes*. Para saber como usar os recursos de rede existente em vez de criar recursos adicionais, digite `az vm create -h`.

Endereços IP públicos têm um valor nominal. Para saber mais sobre preços de endereço IP, leia a página [Preços de endereço IP](https://azure.microsoft.com/pricing/details/ip-addresses) . Há um limite para o número de endereços IP públicos que podem ser usados em uma assinatura. Para saber mais sobre os limites, leia o artigo [Limites do Azure](../azure-subscription-service-limits.md#networking-limits).

Depois que a VM é criada, insira o `az network nic show --name MyNic1 --resource-group myResourceGroup` comando para exibir a configuração da NIC. Insira o `az network nic ip-config list --nic-name MyNic1 --resource-group myResourceGroup --output table` para exibir uma lista das configurações de IP associados à NIC.

Adicione os endereços IP privados ao sistema operacional da VM executando as etapas para seu sistema operacional na seção [Adicionar endereços IP ao sistema operacional de uma VM](#os-config) deste artigo.

## <a name="add"></a>Adicionar endereços IP a uma VM

Você pode adicionar endereços IP públicos e privados adicionais para uma interface de rede do Azure existente ao concluir as etapas a seguir. Os exemplos baseiam-se no [cenário](#scenario) descrito neste artigo.

1. Abra um shell de comando e conclua as etapas restantes nesta seção dentro de uma única sessão. Se a CLI do Azure ainda não estiver instalada e configurada, conclua as etapas do artigo [Instalação da CLI do Azure](/cli/azure/install-az-cli2?toc=%2fazure%2fvirtual-network%2ftoc.json) e faça logon em sua conta do Azure com o comando `az-login`.

2. Conclua as etapas em uma das seções a seguir, com base em seus requisitos:

    **Adicionar um endereço IP privado**
    
    Para adicionar um endereço IP privado a uma NIC, você deve criar uma configuração de IP usando o comando a seguir. O endereço IP estático deve ser um endereço não usado para a sub-rede.

    ```bash
    az network nic ip-config create \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --private-ip-address 10.0.0.7 \
    --name IPConfig-4
    ```
    
    Crie quantas configurações forem necessárias, usando nomes de configuração exclusivos e endereços IP privados (para configurações com endereços IP estáticos).

    **Adicionar um endereço IP público**
    
    Um endereço IP público é adicionado por meio de sua associação a uma nova configuração de IP ou de uma configuração de IP existente. Conclua as etapas em uma das seções a seguir, quando você precisar.

    Endereços IP públicos têm um valor nominal. Para saber mais sobre preços de endereço IP, leia a página [Preços de endereço IP](https://azure.microsoft.com/pricing/details/ip-addresses) . Há um limite para o número de endereços IP públicos que podem ser usados em uma assinatura. Para saber mais sobre os limites, leia o artigo [Limites do Azure](../azure-subscription-service-limits.md#networking-limits).

    - **Associar o recurso a uma nova configuração de IP**
    
        Sempre que você adicionar um endereço IP público em uma nova configuração de IP, também deverá adicionar um endereço IP privado, pois todas as configurações de IP devem ter um endereço IP privado. Você pode adicionar um recurso de endereço IP público existente ou criar um novo. Para criar um novo, insira o seguinte comando:
    
        ```bash
        az network public-ip create \
        --resource-group myResourceGroup \
        --location westcentralus \
        --name myPublicIP3 \
        --dns-name mypublicdns3
        ```

        Para criar uma nova configuração de IP com um endereço IP privado estático e o recurso de endereço IP público *myPublicIP3* associado, insira o seguinte comando:

        ```bash
        az network nic ip-config create \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --name IPConfig-5 \
        --private-ip-address 10.0.0.8
        --public-ip-address myPublicIP3
        ```

    - **Associar o recurso a uma configuração de IP existente** Um recurso de endereço IP público só pode ser associado a uma configuração de IP que ainda não tenha um associado. Você pode determinar se uma configuração de IP tem um endereço IP público associado inserindo o seguinte comando:

        ```bash
        az network nic ip-config list \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --query "[?provisioningState=='Succeeded'].{ Name: name, PublicIpAddressId: publicIpAddress.id }" --output table
        ```

        Saída retornada:
    
            Name        PublicIpAddressId
            
            ipconfig1   /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP1
            IPConfig-2  /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP2
            IPConfig-3

        Como a coluna **PublicIpAddress** para *IpConfig-3* está em branco na saída, nenhum recurso de endereço IP público está associado a ela no momento. Você pode adicionar um recurso de endereço IP público existente como IpConfig-3 ou inserir o seguinte comando para criar um:

        ```bash
        az network public-ip create \
        --resource-group  myResourceGroup
        --location westcentralus \
        --name myPublicIP3 \
        --dns-name mypublicdns3 \
        --allocation-method Static
        ```
    
        Insira o comando a seguir para associar o recurso de endereço IP público à configuração de IP existente chamada *IPConfig-3*:
    
        ```bash
        az network nic ip-config update \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --name IPConfig-3 \
        --public-ip myPublicIP3
        ```

3. Digite o seguinte comando para ver os endereços IP privados e os IDs de recurso de endereço IP público atribuídos à NIC:

    ```bash
    az network nic ip-config list \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --query "[?provisioningState=='Succeeded'].{ Name: name, PrivateIpAddress: privateIpAddress, PrivateIpAllocationMethod: privateIpAllocationMethod, PublicIpAddressId: publicIpAddress.id }" --output table
    ```

    Saída retornada: <br>
    
        Name        PrivateIpAddress    PrivateIpAllocationMethod   PublicIpAddressId
        
        ipconfig1   10.0.0.4            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP1
        IPConfig-2  10.0.0.5            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP2
        IPConfig-3  10.0.0.6            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP3
    

4. Adicione os endereços IP privados que você adicionou à NIC para o sistema operacional da VM seguindo as instruções da seção [Adicionar endereços IP a um sistema operacional de VM](#os-config) deste artigo. Não adicione os endereços IP públicos ao sistema operacional.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
