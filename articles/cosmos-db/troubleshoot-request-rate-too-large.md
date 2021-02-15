---
title: Solucionar problemas de Azure Cosmos DB taxa de solicitação de exceções muito grandes
description: Saiba como diagnosticar e corrigir exceções de taxa de solicitação muito grandes.
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: c3a692a8df93931bd8fd7982e69fe2f9118444c2
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94411330"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-rate-too-large-exceptions"></a>Diagnosticar e solucionar problemas de Azure Cosmos DB taxa de solicitação de exceções muito grandes
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Uma mensagem de "taxa de solicitação muito grande" ou código de erro 429 indica que suas solicitações estão sendo limitadas.

## <a name="troubleshooting-steps"></a>Etapas para solucionar problemas
A seção a seguir contém causas conhecidas e soluções para muitas solicitações.

### <a name="check-the-metrics"></a>Verificar as métricas
Verifique [Azure Cosmos DB monitoramento](monitor-cosmos-db.md) para ver o número de exceções de 429.

#### <a name="cause"></a>Causa:
A taxa de transferência consumida (unidades de solicitação por segundo) excedeu a [taxa de transferência provisionada](set-throughput.md). O SDK repete solicitações automaticamente com base na política de repetição especificada. Se você receber essa falha com frequência, considere aumentar a taxa de transferência na coleção. Verifique as métricas do portal para ver se você está recebendo erros 429. Examine sua chave de partição para garantir que ela resulte em uma [distribuição uniforme do armazenamento e do volume de solicitação](partitioning-overview.md).

#### <a name="solution"></a>Solução:
1. Use o [portal ou o SDK](set-throughput.md) para aumentar a taxa de transferência provisionada.
1. Alterne o banco de dados ou o contêiner para [dimensionamento automático](provision-throughput-autoscale.md).

## <a name="next-steps"></a>Próximas etapas
* [Diagnostique e solucione](troubleshoot-dot-net-sdk.md) problemas ao usar o SDK do .net Azure Cosmos DB.
* Saiba mais sobre as diretrizes de desempenho para o [.net v3](performance-tips-dotnet-sdk-v3-sql.md) e o [.net v2](performance-tips.md).
* [Diagnostique e solucione](troubleshoot-java-sdk-v4-sql.md) problemas ao usar o SDK do Azure Cosmos DB Java v4.
* Saiba mais sobre as diretrizes de desempenho para o [SDK do Java v4](performance-tips-java-sdk-v4-sql.md).