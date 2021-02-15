---
title: Como marcar uma VM usando um modelo
description: Saiba mais sobre como marcar uma máquina virtual usando um modelo.
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.author: cynthn
author: cynthn
ms.date: 10/26/2018
ms.openlocfilehash: e7dd75a025b76773a0bf1e3b4f752b5a77db6786
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897367"
---
# <a name="tagging-a-vm-using-a-template"></a>Marcando uma VM usando um modelo

Este artigo descreve como marcar uma VM no Azure usando um modelo do Resource Manager. As marcas são pares de chave/valor definidos pelo usuário que podem ser colocados diretamente em um recurso ou grupo de recursos. Atualmente, o Azure dá suporte a até 50 marcas por recurso e grupo de recursos. As marcas podem ser colocadas em um recurso no momento da criação ou adicionadas a um recurso existente.

[Este modelo](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags) coloca marcas nos seguintes recursos: Computação (Máquina Virtual), Armazenamento (Conta de Armazenamento) e Rede (Endereço IP Público, Rede Virtual e Interface de Rede). Esse modelo destina-se a uma VM do Windows, mas pode ser adaptada para VMs do Linux.

Clique no botão **Implantar no Azure** no [link do modelo](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags). Você será direcionado para o [Portal do Azure](https://portal.azure.com/) , onde poderá implantar esse modelo.

![Implantação simples com marcas](./media/tag/deploy-to-azure-tags.png)

Esse modelo inclui as seguintes marcas: *Departamento*, *Aplicativo* e *Criado por*. Você pode adicionar/editar essas marcas diretamente no modelo se desejar diferentes nomes de marca.

![Marcas do Azure em um modelo](./media/tag/azure-tags-in-a-template.png)

Como pode ser visto, as marcas são pares de chave/valor definidos, separados por dois-pontos (:). As marcas devem ser definidas neste formato:

```config
"tags": {
    "Key1" : "Value1",
    "Key2" : "Value2"
}
```

Salve o arquivo de modelo quando terminar de editá-lo com as marcas de sua escolha.

Em seguida, na seção **Editar Parâmetros** , você pode preencher os valores de suas marcas.

![Editar marcas no Portal do Azure](./media/tag/edit-tags-in-azure-portal.png)

Clique em **Criar** para implantar esse modelo com seus valores de marca.

### <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre como marcar seus recursos do Azure, consulte [Visão geral do Azure Resource Manager](../azure-resource-manager/management/overview.md) e [Usando marcas para organizar os Recursos do Azure](../azure-resource-manager/management/tag-resources.md).
- Para ver como as marcas podem ajudá-lo a gerenciar seu uso de recursos do Azure, consulte [noções básicas sobre sua fatura do Azure](../cost-management-billing/understand/review-individual-bill.md).
