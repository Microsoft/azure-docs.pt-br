---
title: Dados do Assistente no Azure Resource Graph
description: Fazer consultas para dados do Advisor no grafo de recursos do Azure
ms.topic: article
ms.date: 03/12/2020
ms.author: sagupt
ms.openlocfilehash: 66bb500d419d1f5537afafd7a2df543ded8cc7ce
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87057781"
---
# <a name="query-for-advisor-data-in-resource-graph-explorer-azure-resource-graph"></a>Consultar dados do Advisor no Gerenciador de grafo de recursos (grafo de recursos do Azure)

Os recursos do Advisor agora são integrados ao [grafo de recursos do Azure](https://azure.microsoft.com/features/resource-graph/). Isso estabelece a base para muitos cenários de clientes em escala para as recomendações do Advisor. Poucos cenários que não eram possíveis antes de fazer em escala e agora podem ser obtidos usando o grafo de recursos são:
* Fornece a capacidade de executar consultas complexas para todas as suas assinaturas no portal do Azure
* Recomendações resumidas por tipos de categoria (como confiabilidade, desempenho) e tipos de impacto (alta, média, baixa)
* Todas as recomendações para um tipo de recomendação específico
* Contagem de recursos impactados por categoria de recomendação

![Assistente no Gerenciador de grafo de recursos do Azure](./media/azure-resource-graph-1.png)  


## <a name="advisor-resource-types-in-azure-graph"></a>Tipos de recursos do Advisor no grafo do Azure

Tipos de recursos do Advisor disponíveis no [grafo de recursos](../governance/resource-graph/index.yml): há três tipos de recursos disponíveis para consulta de recursos do Advisor. Aqui está a lista dos recursos que agora estão disponíveis para consulta no grafo de recursos.
* Microsoft. Advisor/Configurations
* Microsoft. Advisor/Recommendations
* Microsoft. Advisor/supressões

Esses tipos de recursos são listados em uma nova tabela chamada como AdvisorResources, que você também pode consultar no Gerenciador de grafo de recursos em portal do Azure.


## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre as recomendações do Assistente, consulte:
* [Introdução ao Assistente do Azure](advisor-overview.md)
* [Introdução ao Assistente](advisor-get-started.md)
* [Recomendações de custo do Advisor](advisor-cost-recommendations.md)
* [Recomendações de confiabilidade do Advisor](advisor-high-availability-recommendations.md)
* [Recomendações de desempenho do Advisor](advisor-performance-recommendations.md)
* [Recomendações de segurança do Advisor](advisor-security-recommendations.md)
* [Recomendações de excelência operacional do Advisor](advisor-operational-excellence-recommendations.md)
* [API REST do Advisor](/rest/api/advisor/)
