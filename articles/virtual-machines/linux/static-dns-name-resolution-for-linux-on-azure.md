---
title: Usar DNS interno para resolução de nomes de VM com CLI do Azure | Microsoft Docs
description: Como criar adaptadores de rede de rede virtual e usar DNS interno para resolução de nomes de VM no Azure com a CLI do Azure
services: virtual-machines-linux
documentationcenter: ''
author: vlivech
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 02/16/2017
ms.author: gwallace
ms.openlocfilehash: d53c4c2120701ca99d0865e2c074c85e629ae81c
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875169"
---
# <a name="create-virtual-network-interface-cards-and-use-internal-dns-for-vm-name-resolution-on-azure"></a>Criar placas de adaptador de rede virtual e usar DNS interno para resolução de nome da VM no Azure

Este artigo mostra como definir nomes DNS internos estáticos para VMs do Linux usando vNics (adaptadores de rede de rede virtual) e nomes de rótulo DNS com a CLI do Azure. Nomes DNS estáticos são usados para serviços de infraestrutura permanentes como um servidor de build Jenkins, que é usado para este documento ou um servidor Git.

Esses requisitos são:

* [uma conta do Azure](https://azure.microsoft.com/pricing/free-trial/)
* [arquivos de chave SSH pública e privada](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="quick-commands"></a>Comandos rápidos
Se você precisar executar a tarefa rapidamente, a seção a seguir fornecerá detalhes dos comandos necessários. Mais informações detalhadas e contexto para cada etapa podem ser encontrados no restante do documento, começando [aqui](#detailed-walkthrough). Para realizar essas etapas, é preciso ter a [CLI do Azure](/cli/azure/install-az-cli2) mais recente instalada e conectada a uma conta do Azure usando [az login](/cli/azure/reference-index).

Pré-requisitos: grupo de recursos, rede virtual e sub-rede, Grupo de Segurança de Rede com SSH de entrada.

### <a name="create-a-virtual-network-interface-card-with-a-static-internal-dns-name"></a>Criar uma placa de interface de rede virtual com um nome DNS interno estático
Crie a vNic com [az network nic create](/cli/azure/network/nic). O sinalizador `--internal-dns-name` da CLI serve para configurar o rótulo DNS, que fornece o nome DNS estático para a vNic (placa de interface de rede virtual). O exemplo a seguir cria uma vNic chamada `myNic`, conecta-a à rede virtual `myVnet` e cria um registro de nome DNS interno chamado `jenkins`:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --internal-dns-name jenkins
```

### <a name="deploy-a-vm-and-connect-the-vnic"></a>Implantar uma VM e conectar a vNic
Crie uma VM com [az vm create](/cli/azure/vm). O sinalizador `--nics` conecta a vNic à VM durante a implantação no Azure. O exemplo a seguir cria uma VM denominada `myVM` com Azure Managed Disks e anexa a vNic chamada `myNic` da etapa anterior:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="detailed-walkthrough"></a>Passo a passo detalhado

Uma infraestrutura completa de CiCd (implantação e integração contínuas) no Azure exige que alguns servidores sejam estáticos ou de longa duração. Recomendamos que os ativos do Azure, por exemplo as redes virtuais e os grupos de segurança de rede, sejam recursos estáticos e de longa duração que raramente sejam implantados. Após a implantação de uma rede virtual, ela pode ser reutilizada por novas implantações sem nenhum efeito negativo sobre a infraestrutura. Você pode adicionar posteriormente um servidor de repositório Git ou então um servidor de automação Jenkins fornece CiCd para essa rede virtual para os ambientes de desenvolvimento ou teste.  

Nomes DNS internos podem ser resolvidos apenas em uma rede virtual do Azure. Como os nomes DNS são internos, eles não podem ser resolvidos para a Internet externa, fornecendo segurança adicional para a infraestrutura.

Nos exemplos a seguir, substitua os nomes de parâmetro de exemplo com seus próprios valores. Os nomes de parâmetro de exemplo incluem `myResourceGroup`, `myNic` e `myVM`.

## <a name="create-the-resource-group"></a>Criar o grupo de recursos
Primeiro, crie o grupo de recursos com [az group create](/cli/azure/group). O exemplo a seguir cria um grupo de recursos denominado `myResourceGroup` no local `westus`:

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-the-virtual-network"></a>Criar a rede virtual

A próxima etapa é criar uma rede virtual na qual iniciar as VMs. A rede virtual contém uma sub-rede para este passo a passo. Para obter mais informações sobre as redes virtuais do Azure, consulte [Criar uma rede virtual](../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network). 

Crie a rede virtual com [az network vnet create](/cli/azure/network/vnet). O exemplo a seguir cria uma rede virtual chamada `myVnet` e uma sub-rede chamada `mySubnet`:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-the-network-security-group"></a>Criar o grupo de segurança de rede
Os grupos de segurança de rede do Azure são equivalentes a um firewall na camada de rede. Para obter mais informações sobre os grupos de segurança de rede, confira [Como criar NSGs na CLI do Azure](../../virtual-network/tutorial-filter-network-traffic-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Crie o grupo de segurança de rede com [az network nsg create](/cli/azure/network/nsg). O seguinte exemplo cria um grupo de segurança de rede chamado `myNetworkSecurityGroup`:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="add-an-inbound-rule-to-allow-ssh"></a>Adicionar uma regra de entrada para permitir SSH
Adicione uma regra de entrada para o grupo de segurança de rede com [az network nsg rule create](/cli/azure/network/nsg/rule). O exemplo a seguir cria uma regra chamada `myRuleAllowSSH`:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myRuleAllowSSH \
    --protocol tcp \
    --direction inbound \
    --priority 1000 \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 22 \
    --access allow
```

## <a name="associate-the-subnet-with-the-network-security-group"></a>Associar uma sub-rede ao Grupo de Segurança de Rede
Para associar a sub-rede ao grupo de segurança de rede, use [az network vnet subnet update](/cli/azure/network/vnet/subnet). O exemplo a seguir associa a sub-rede denominada `mySubnet` ao Grupo de Segurança de Rede denominado `myNetworkSecurityGroup`:

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```


## <a name="create-the-virtual-network-interface-card-and-static-dns-names"></a>Criar a placa de interface de rede virtual e nomes DNS estáticos
O Azure é muito flexível, mas para usar nomes DNS para a resolução de nomes de VM, você precisa criá-los como vNics (placas de adaptador de rede virtual) que incluam um rótulo DNS. Placas de vNics são importantes, uma vez que você pode reutilizá-las conectando-as a diferentes VMs. Essa abordagem mantém a vNic como um recurso estático, enquanto as VMs podem ser temporárias. Ao usar a rotulagem DNS na vNic, podemos habilitar a resolução de nomes simples em outras VMs na VNet. Usar nomes que podem ser resolvidos permite que outras VMs acessem o servidor de automação com o nome DNS `Jenkins` ou o servidor Git como `gitrepo`.  

Crie a vNic com [az network nic create](/cli/azure/network/nic). O exemplo a seguir cria uma vNic chamada `myNic`, conecta-a à rede virtual `myVnet` chamada `myVnet` e cria um registro de nome DNS interno chamado `jenkins`:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --internal-dns-name jenkins
```

## <a name="deploy-the-vm-into-the-virtual-network-infrastructure"></a>Implantar a VM na infra-estrutura de rede virtual
Agora temos uma rede virtual, uma sub-rede, uma vNic e um grupo de segurança de rede atuando como um firewall, para proteger nossa sub-rede bloqueando todo o tráfego de entrada, exceto pela porta 22 para o SSH. Agora a VM pode ser implantada nessa infraestrutura de rede existente.

Crie uma VM com [az vm create](/cli/azure/vm). O exemplo a seguir cria uma VM denominada `myVM` com Azure Managed Disks e anexa a vNic chamada `myNic` da etapa anterior:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

Ao usar sinalizadores da CLI para chamar os recursos existentes, instruímos o Azure a implantar a VM na rede existente. Em outras palavras, depois que uma VNET e uma sub-rede forem implantadas, elas poderão ser mantidas como recursos estáticos ou permanentes na região do Azure.  

## <a name="next-steps"></a>Próximas etapas
* [Criar seu próprio ambiente personalizado para uma VM do Linux usando os comandos da CLI do Azure diretamente](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Criar uma VM do Linux no Azure usando modelos](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
