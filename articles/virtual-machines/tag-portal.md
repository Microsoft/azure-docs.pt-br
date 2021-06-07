---
title: Como marcar uma VM usando o portal do Azure
description: Saiba mais sobre como marcar uma máquina virtual usando o portal do Azure.
ms.topic: how-to
ms.workload: infrastructure-services
author: cynthn
ms.service: virtual-machines
ms.date: 11/11/2020
ms.author: cynthn
ms.openlocfilehash: 9c220814ae1f714e9eac0c0c11a50d9cf68a621d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98897401"
---
# <a name="tagging-a-vm-using-the-portal"></a>Marcando uma VM usando o portal

Este artigo descreve como adicionar marcas a uma VM usando o Portal. As marcas são pares de chave/valor definidos pelo usuário que podem ser colocados diretamente em um recurso ou grupo de recursos. Atualmente, o Azure dá suporte a até 50 marcas por recurso e grupo de recursos. As marcas podem ser colocadas em um recurso no momento da criação ou adicionadas a um recurso existente.


1. Navegue até sua VM no Portal.
1. Em **Essentials**, selecione **clique aqui para adicionar marcas**.

    :::image type="content" source="media/tag/azure-portal-tag.png" alt-text="Captura de tela da seção Essentials da página da VM.":::

1. Adicione um valor para **nome** e **valor** e, em seguida, selecione **salvar**.

    :::image type="content" source="media/tag/key-value.png" alt-text="Captura de tela da página para adicionar um par chave-valor como uma marca.":::

### <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre como marcar seus recursos do Azure, consulte [Visão geral do Azure Resource Manager](../azure-resource-manager/management/overview.md) e [Usando marcas para organizar os Recursos do Azure](../azure-resource-manager/management/tag-resources.md).
- Para ver como as marcas podem ajudá-lo a gerenciar seu uso de recursos do Azure, consulte [noções básicas sobre sua fatura do Azure](../cost-management-billing/understand/review-individual-bill.md).
