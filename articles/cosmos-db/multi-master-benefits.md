---
title: Benefícios de vários mestres do Azure Cosmos DB
description: Compreenda os benefícios de vários mestres no Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: mjbrown
ms.openlocfilehash: c78e5e4f8d396d777738bddfd6baf086c0b2ecf4
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789287"
---
# <a name="understand-multi-master-benefits-in-azure-cosmos-db"></a>Entender os benefícios de vários mestres no Azure Cosmos DB

Operadores de conta do cosmos DB devem escolher a configuração global de distribuição apropriados para garantir que a latência, disponibilidade e requisitos de RTO para seus aplicativos. Contas do Cosmos do Azure configuradas com vários locais de gravação oferecem benefícios significativos em contas com o local de gravação única incluindo, 99,999% de gravação do SLA de disponibilidade, < SLA de latência no 99 º percentil e RTO de gravação de 10 ms = 0 em caso de desastre regional.

## <a name="comparison-of-features"></a>Comparação de recursos

|Requisito de aplicativo|Vários locais de gravação|Local de gravação única|Observação|
|---|---|---|---|
|Gravar o SLA de latência de < 10 ms em P99|**Sim**|Não|Contas com um único local de gravação incorrerá em latência de rede de região cruzada adicionais para cada gravação.|
|Leia o SLA de latência de < 10 ms em P99|**Sim**|Sim| |
|SLA de 99,999% de gravação|**Sim**|Não|Contas com um único local de gravação garantem SLA de 99,99%|
|RTO = 0|**Sim**|Não|Zero tempo de inatividade para gravações em caso de desastres regionais. Contas com o local de gravação única têm RTO é de 15 minutos.|

## <a name="next-steps"></a>Próximas etapas

Se você ainda desejar desabilitar EnableMultipleWriteLocations em sua conta do Cosmos do Azure, por favor [abrir um tíquete de suporte](https://azure.microsoft.com/support/create-ticket/).
