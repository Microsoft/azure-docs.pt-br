---
title: Solucionar problemas Azure Cosmos DB exceções de tempo limite de solicitação de serviço
description: Saiba como diagnosticar e corrigir Azure Cosmos DB exceções de tempo limite de solicitação de serviço.
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 5b188021de30561222f098e2b5782bada25d7ce0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94411236"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-timeout-exceptions"></a>Diagnosticar e solucionar problemas Azure Cosmos DB exceções de tempo limite de solicitação
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB retornou um tempo limite de solicitação HTTP 408.

## <a name="troubleshooting-steps"></a>Etapas para solucionar problemas
A lista a seguir contém causas conhecidas e soluções para exceções de tempo limite de solicitação.

### <a name="check-the-sla"></a>Verificar o SLA
Verifique [Azure Cosmos DB monitoramento](monitor-cosmos-db.md) para ver se o número de exceções 408 viola o SLA de Azure Cosmos DB.

#### <a name="solution-1-it-didnt-violate-the-azure-cosmos-db-sla"></a>Solução 1: ela não violou o SLA de Azure Cosmos DB
O aplicativo deve lidar com esse cenário e tentar novamente nessas falhas transitórias.

#### <a name="solution-2-it-did-violate-the-azure-cosmos-db-sla"></a>Solução 2: ela violou a Azure Cosmos DB SLA
Contate o [suporte do Azure](https://aka.ms/azure-support).
 
### <a name="hot-partition-key"></a>Chave de partição ativa
Azure Cosmos DB distribui a taxa de transferência provisionada de maneira uniforme entre as partições físicas. Quando há uma partição ativa, uma ou mais chaves de partição lógica em uma partição física estão consumindo todas as unidades de solicitação por segundo da partição física (RU/s). Ao mesmo tempo, as RU/s em outras partições físicas ficarão não utilizadas. Como sintoma, o total de RU/s consumido será menor do que o RU/s provisionado geral no banco de dados ou contêiner. Você ainda verá limitação (429s) nas solicitações em relação à chave de partição lógica ativa. Use a [métrica de consumo de ru normalizada](monitor-normalized-request-units.md) para ver se a carga de trabalho está encontrando uma partição ativa. 

#### <a name="solution"></a>Solução:
Escolha uma boa chave de partição que distribua uniformemente o volume de solicitação e o armazenamento. Saiba como [alterar sua chave de partição](https://devblogs.microsoft.com/cosmosdb/how-to-change-your-partition-key/).

## <a name="next-steps"></a>Próximas etapas
* [Diagnostique e solucione](troubleshoot-dot-net-sdk.md) problemas ao usar o SDK do .net Azure Cosmos DB.
* Saiba mais sobre as diretrizes de desempenho para o [.net v3](performance-tips-dotnet-sdk-v3-sql.md) e o [.net v2](performance-tips.md).
* [Diagnostique e solucione](troubleshoot-java-sdk-v4-sql.md) problemas ao usar o SDK do Azure Cosmos DB Java v4.
* Saiba mais sobre as diretrizes de desempenho para o [SDK do Java v4](performance-tips-java-sdk-v4-sql.md).