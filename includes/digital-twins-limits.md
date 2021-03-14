---
author: baanders
description: incluir arquivo para limites de gêmeos digitais do Azure
ms.service: digital-twins
ms.topic: include
ms.date: 6/9/2020
ms.author: baanders
ms.openlocfilehash: 4a69b2ff15fc4857e9fb292d2f753aa68ed875d4
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100370080"
---
### <a name="functional-limits"></a>Limites funcionais

A tabela a seguir lista os limites funcionais do Azure digital gêmeos. 

> [!TIP]
> Para que as recomendações de modelagem operem dentro desses limites funcionais, consulte [práticas recomendadas para a criação de modelos](../articles/digital-twins/concepts-models.md#best-practices-for-designing-models).

| Área | Funcionalidade | Limite padrão | Ajustá? |
| --- | --- | --- | --- |
| Recursos do Azure | Número de instâncias de gêmeos digitais do Azure em uma região, por assinatura | 10 | Sim |
| Gêmeos Digitais | Número de gêmeos em uma instância de gêmeos digital do Azure | 200.000 | Sim |
| Gêmeos Digitais | Número de relações de entrada para um único "n" | 5\.000 | Não |
| Gêmeos Digitais | Número de relações de saída de um único n º | 5\.000 | Não |
| Gêmeos Digitais | Tamanho máximo (do corpo JSON em uma solicitação PUT ou PATCH) de um único número de texto | 32 KB | Não |
| Gêmeos Digitais | Tamanho máximo da carga de solicitação | 32 KB | Não | 
| Roteamento | Número de pontos de extremidade para uma única instância de gêmeos digital do Azure | 6 | Não |
| Roteamento | Número de rotas para uma única instância de gêmeos digital do Azure | 6 | Sim |
| Modelos | Número de modelos em uma única instância de gêmeos digital do Azure | 10.000 | Sim |
| Modelos | Número de modelos que podem ser carregados em uma única chamada à API | 250 | Não |
| Modelos | Tamanho máximo (do corpo JSON em uma solicitação PUT ou PATCH) de um único modelo | 1 MB | Não |
| Modelos | Número de itens retornados em uma única página | 100 | Não |
| Consulta | Número de itens retornados em uma única página | 100 | Sim |
| Consulta | Número de `AND`  /  `OR` expressões em uma consulta | 50 | Sim |
| Consulta | Número de itens de matriz em uma `IN`  /  `NOT IN` cláusula | 50 | Sim |
| Consulta | Número de caracteres em uma consulta | 8,000 | Sim |
| Consulta | Número de `JOINS` em uma consulta | 5 | Sim |

### <a name="rate-limits"></a>Limites de taxa

A tabela a seguir reflete os limites de taxa de APIs diferentes.

| API | Funcionalidade | Limite padrão | Ajustá? |
| --- | --- | --- | --- |
| API de modelos | Número de solicitações por segundo | 100 | Sim |
| API gêmeos digital | Número de solicitações por segundo | 2\.000 | Sim |
| API gêmeos digital | Número de operações de criação/exclusão por segundo em **todas as gêmeos e relações** | 50 | Sim |
| API gêmeos digital | Número de operações de criação/atualização/exclusão por segundo em uma **única** ou suas relações | 10 | Não |
| API de Consulta | Número de solicitações por segundo | 500 | Sim |
| API de Consulta | [Unidades de consulta](../articles/digital-twins/concepts-query-units.md) por segundo | 4.000 | Sim |
| API de rotas de eventos | Número de solicitações por segundo | 100 | Sim |

### <a name="other-limits"></a>Outros limites

Os limites em tipos de dados e campos em documentos de DTDL para modelos de gêmeos digitais do Azure podem ser encontrados em sua documentação de especificações no GitHub: [*DTDL (digital gêmeos Definition Language)-versão 2*](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md).
 
Detalhes de latência de consulta e outras limitações de consulta podem ser encontrados em [*instruções: consultar o grafo de entrelaçamento*](../articles/digital-twins/how-to-query-graph.md).
