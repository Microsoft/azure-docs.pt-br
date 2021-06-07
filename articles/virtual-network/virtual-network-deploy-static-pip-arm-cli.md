---
title: Criação de uma VM com um endereço IP público estático - CLI do Azure | Microsoft Docs
description: Aprenda a criar uma VM com um endereço IP público estático usando a interface de linha de comando (CLI) do Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: 55bc21b0-2a45-4943-a5e7-8d785d0d015c
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: kumud
ms.openlocfilehash: f0f61cc4ef02033a2c21ce5acde68caea483e743
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790123"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-cli"></a>Crie uma máquina virtual com um endereço IP público estático usando a CLI do Azure

Você pode criar uma máquina virtual com um endereço IP público estático. Um endereço IP público permite que você se comunique com uma máquina virtual pela Internet. Atribua um endereço IP público estático, em vez de um endereço dinâmico, para garantir que o endereço nunca seja alterado. Saiba mais sobre os [endereços IP públicos estáticos](./public-ip-addresses.md#allocation-method). Para alterar um endereço IP público atribuído a uma máquina virtual existente de dinâmico para estático, ou para trabalhar com endereços IP, consulte [adicionar, alterar ou remover endereços IP](virtual-network-network-interface-addresses.md). Endereços IP públicos têm [carga nominal](https://azure.microsoft.com/pricing/details/ip-addresses), e há um [limite](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) para o número de endereços IP públicos que você pode usar por assinatura.

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Você pode concluir as etapas a seguir no seu computador local ou usando o Shell de Nuvem do Azure. Para usar seu computador local, verifique se você tem o [CLI do Azure instalado](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Para usar o Azure Cloud Shell, selecione **Experimente** no canto superior direito de qualquer caixa de comando a seguir. O Cloud Shell entrará no Azure.

1. Se usar o Cloud Shell, vá para a etapa 2. Abra uma sessão de comando e entre no Azure com `az login`.
2. Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az_group_create). O exemplo a seguir cria um grupo de recursos na região Leste do Azure dos EUA:

   ```azurecli-interactive
   az group create --name myResourceGroup --location eastus
   ```

3. Crie uma máquina virtual com o comando [az vm create](/cli/azure/vm#az_vm_create). O `--public-ip-address-allocation=static` opção atribui um endereço IP público estático para a máquina virtual. O exemplo a seguir cria uma máquina virtual Ubuntu com um endereço IP público SKU estático e básico chamado *myPublicIpAddress*:

   ```azurecli-interactive
   az vm create \
     --resource-group myResourceGroup \
     --name myVM \
     --image UbuntuLTS \
     --admin-username azureuser \
     --generate-ssh-keys \
     --public-ip-address myPublicIpAddress \
     --public-ip-address-allocation static
   ```

   Se o endereço IP público precisar ser um SKU padrão, adicione `--public-ip-sku Standard` ao comando anterior. Saiba mais sobre [SKUs de endereço IP público](./public-ip-addresses.md#sku). Se a máquina virtual for adicionada ao pool de back-end de um Azure Load Balancer público, o SKU do endereço IP público da máquina virtual deverá corresponder ao SKU do endereço IP público do balanceador de carga. Para obter detalhes, consulte [balanceador de carga do Azure](../load-balancer/skus.md).

4. Visualize o endereço IP público atribuído e confirme se ele foi criado como um endereço SKU estático básico, com [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show):

   ```azurecli-interactive
   az network public-ip show \
     --resource-group myResourceGroup \
     --name myPublicIpAddress \
     --query [ipAddress,publicIpAllocationMethod,sku] \
     --output table
   ```

   O Azure atribuiu um endereço IP público dos endereços usados na região na qual você criou a máquina virtual. Você pode baixar a lista de intervalos (prefixos) para as nuvens [pública](https://www.microsoft.com/download/details.aspx?id=56519), do [governo dos EUA](https://www.microsoft.com/download/details.aspx?id=57063), da [China](https://www.microsoft.com/download/details.aspx?id=57062) e da [Alemanha](https://www.microsoft.com/download/details.aspx?id=57064) do Azure.

> [!WARNING]
> Não modifique as configurações do endereço IP no sistema operacional da máquina virtual. O sistema operacional fica ciente dos endereços IP públicos do Azure. Embora você possa adicionar configurações de endereço IP privado ao sistema operacional, recomendamos não fazê-lo, a menos que seja necessário, e somente depois de ler [Adicionar um endereço IP privado a um sistema operacional](virtual-network-network-interface-addresses.md#private).

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, você poderá usar [az group delete](/cli/azure/group#az_group_delete) para remover o grupo de recursos e todos os recursos que ele contém:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [endereços IP públicos](./public-ip-addresses.md#public-ip-addresses) no Azure
- Saiba mais sobre todos os [configurações de endereço IP público](virtual-network-public-ip-address.md#create-a-public-ip-address)
- Saiba mais sobre [endereços IP privados](./private-ip-addresses.md) e atribuir uma [endereço IP privado estático](virtual-network-network-interface-addresses.md#add-ip-addresses) para uma máquina virtual do Azure
- Saiba mais sobre como criar [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) máquinas virtuais
