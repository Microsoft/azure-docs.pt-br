---
title: Criar uma rede virtual – início rápido – CLI do Azure
titlesuffix: Azure Virtual Network
description: Neste início rápido, aprenda a criar uma rede virtual usando a CLI do Azure. A rede virtual permite que os recursos do Azure se comuniquem entre si e com a Internet.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
Customer intent: I want to create a virtual network so that virtual machines can communicate with privately with each other and with the internet.
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 01/22/2019
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1feae201738a560c4cdb56f703c4af9a38af86d1
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "88056781"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-cli"></a>Início Rápido: Criar uma rede virtual usando a CLI do Azure

Uma rede virtual permite que recursos do Azure, como VMs (máquinas virtuais), comuniquem-se em modo privado e com a Internet. Neste início rápido, você aprende como criar uma rede virtual. Após criar uma rede virtual, você implantará duas VMs na rede virtual. Você fará a conexão com as VMs usando a Internet e se comunicará de modo privado na nova rede virtual.
## <a name="prerequisites"></a>Pré-requisitos
Caso não tenha uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) agora.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI do Azure localmente, este guia de início rápido exigirá a versão 2.0.28 ou posterior da CLI do Azure. Execute `az --version` para localizar a versão instalada. Para informações sobre como instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group-and-a-virtual-network"></a>Crie um grupo de recursos e uma rede virtual

Antes de poder criar uma rede virtual, você deverá criar um grupo de recursos para hospedá-la. Crie um grupo de recursos com [az group create](/cli/azure/group). Este exemplo cria um grupo de recursos chamado *myResourceGroup* na região *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Crie a rede virtual com [az network vnet create](/cli/azure/network/vnet). O exemplo cria uma rede virtual padrão nomeada *myVirtualNetwork* com uma sub-rede nomeada *padrão*:

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --subnet-name default
```

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie duas VMs na rede virtual.

### <a name="create-the-first-vm"></a>Criar a primeira VM

Crie uma VM com [az vm create](/cli/azure/vm). Se as chaves SSH ainda não existirem em uma localização de chave padrão, o comando as criará. Para usar um conjunto específico de chaves, use a opção `--ssh-key-value`. A opção `--no-wait` cria a VM em segundo plano para que você possa prosseguir para a próxima etapa. O exemplo a seguir cria uma VM nomeada *myVm1*:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --generate-ssh-keys \
  --no-wait
```

### <a name="create-the-second-vm"></a>Criar a segunda VM

Como você usou a opção `--no-wait` na etapa anterior, poderá seguir e criar a segunda VM chamada *myVm2*.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --generate-ssh-keys
```

### <a name="azure-cli-output-message"></a>Mensagem de saída da CLI do Azure

As VMs podem levar alguns minutos para serem criadas. Depois que o Azure cria as VMs, a CLI do Azure retorna uma saída como esta:

```output
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVm2",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.5",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
  "zones": ""
}
```

Anote o **publicIpAddress**. Você usará esse endereço para conectar-se à VM pela Internet na próxima etapa.

## <a name="connect-to-a-vm-from-the-internet"></a>Conecte uma VM a partir da Internet

Neste comando, substitua `<publicIpAddress>` pelo endereço IP público da VM *myVm2*:

```bash
ssh <publicIpAddress>
```

## <a name="communicate-between-vms"></a>Comunicação entre VMs

Para confirmar a comunicação privada entre as VMs *myVm2* e *myVm1*, insira este comando:

```bash
ping myVm1 -c 4
```

Quatro respostas serão recebidas de *10.0.0.4*.

Encerre a sessão SSH com a VM *myVm2*.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, você poderá usar [az group delete](/cli/azure/group) para remover o grupo de recursos e todos os recursos que ele contém:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou uma rede virtual padrão e duas VMs. Você se conectou a uma VM pela Internet e executou uma comunicação entre duas VMs em modo privado.
O Azure permite comunicação privada irrestrita entre VMs. Por padrão, ele só permite conexões de área de trabalho remota de entrada para VMs do Windows da Internet. Prossiga para o próximo artigo para aprender a configurar diferentes tipos de comunicações de rede de VMs:
> [!div class="nextstepaction"]
> [Filtrar tráfego de rede](tutorial-filter-network-traffic.md)
