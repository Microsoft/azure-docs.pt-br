---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 09/01/2020
ms.author: tomfitz
ms.openlocfilehash: 543aa50d72de5a06a9a1c7ac88ac5ecae993bc9d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98698132"
---
| Recurso | Limite |
| --- | --- |
| Recursos por [grupo de recursos](../articles/azure-resource-manager/management/overview.md#resource-groups) | Os recursos não são limitados pelo grupo de recursos. Em vez disso, eles são limitados pelo tipo de recurso em um grupo de recursos. Veja a próxima linha. |
| Recursos por grupo de recursos, por tipo de recurso |800 – Alguns tipos de recurso podem exceder o limite de 800. Confira [Recursos não limitados a 800 instâncias por grupo de recursos](../articles/azure-resource-manager/management/resources-without-resource-group-limit.md). |
| Implantações por grupo de recursos no histórico de implantações |800<sup>1</sup> |
| Recursos por implantação |800 |
| Bloqueios de gerenciamento por [escopo](../articles/azure-resource-manager/management/overview.md#understand-scope) exclusivo  |20 |
| Número de marcas por recurso ou grupo de recursos |50 |
| Comprimento da chave de marca |512 |
| Comprimento do valor da marca |256 |

<sup>1</sup>As implantações são automaticamente excluídas do histórico à medida que você se aproxima do limite. A exclusão de uma entrada do histórico de implantações não afeta os recursos implantados. Para obter mais informações, confira [Exclusões automáticas do histórico de implantações](../articles/azure-resource-manager/templates/deployment-history-deletions.md).

#### <a name="template-limits"></a>Limites de modelo

| Valor | Limite |
| --- | --- |
| Parâmetros |256 |
| Variáveis |256 |
| Recursos (incluindo a contagem de cópias) |800 |
| Saídas |64 |
| Expressão de modelo |24.576 caracteres |
| Recursos em modelos exportados |200 |
| Tamanho do modelo |4 MB |
| Tamanho do arquivo de parâmetro |64 KB |

Você pode exceder alguns limites de modelo usando um modelo aninhado. Para obter mais informações, confira [Usar modelos vinculados quando você implanta recursos do Azure](../articles/azure-resource-manager/templates/linked-templates.md). Para reduzir o número de parâmetros, variáveis ou saídas, você pode combinar vários valores em um objeto. Para saber mais, veja [Objetos como parâmetros](/azure/architecture/guide/azure-resource-manager/advanced-templates/objects-as-parameters).