---
title: Como marcar uma máquina virtual do Azure usando a CLI
description: Saiba mais sobre como marcar uma máquina virtual usando o CLI do Azure.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/11/2020
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: 32d15730557c96362602b5e324254c76637ecb55
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897435"
---
# <a name="how-to-tag-a-vm-using-the-cli"></a>Como marcar uma VM usando a CLI

Este artigo descreve como marcar uma VM usando o CLI do Azure. As marcas são pares de chave/valor definidos pelo usuário que podem ser colocados diretamente em um recurso ou grupo de recursos. Atualmente, o Azure dá suporte a até 50 marcas por recurso e grupo de recursos. As marcas podem ser colocadas em um recurso no momento da criação ou adicionadas a um recurso existente. Você também pode marcar uma máquina virtual usando o Azure [PowerShell](tag-powershell.md).


Você pode exibir todas as propriedades de uma determinada VM, incluindo as marcas, usando `az vm show` .

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM --query tags
```

Para adicionar uma nova marca de VM por meio do CLI do Azure, você pode usar o `azure vm update` comando junto com o parâmetro de marca `--set` :

```azurecli-interactive
az vm update \
    --resource-group myResourceGroup \
    --name myVM \
    --set tags.myNewTagName1=myNewTagValue1 tags.myNewTagName2=myNewTagValue2
```

Para remover marcas, você pode usar o `--remove` parâmetro no `azure vm update` comando.

```azurecli-interactive
az vm update \
   --resource-group myResourceGroup \
   --name myVM \
   --remove tags.myNewTagName1
```

Agora que aplicamos marcas aos nossos recursos CLI do Azure e ao portal, vamos dar uma olhada nos detalhes de uso para ver as marcas no portal de cobrança.

### <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre como marcar seus recursos do Azure, consulte [Visão geral do Azure Resource Manager](../azure-resource-manager/management/overview.md) e [Usando marcas para organizar os Recursos do Azure](../azure-resource-manager/management/tag-resources.md).
- Para ver como as marcas podem ajudá-lo a gerenciar seu uso de recursos do Azure, consulte [noções básicas sobre sua fatura do Azure](../cost-management-billing/understand/review-individual-bill.md).
