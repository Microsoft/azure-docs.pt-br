---
title: Solucionar problemas de exceções do serviço Azure Cosmos DB indisponíveis com o SDK do Java v4
description: Saiba como diagnosticar e corrigir Azure Cosmos DB exceções de serviço indisponíveis com o SDK do Java v4.
author: kushagrathapar
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 10/28/2020
ms.author: kuthapar
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 03c899307b00010ca87b279ed720b92946d3673c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93340049"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-java-v4-sdk-service-unavailable-exceptions"></a>Diagnosticar e solucionar problemas Azure Cosmos DB exceções não disponíveis do serviço SDK do Java v4
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]
O SDK do Java v4 não foi capaz de se conectar ao Azure Cosmos DB.

## <a name="troubleshooting-steps"></a>Etapas para solucionar problemas
A lista a seguir contém causas conhecidas e soluções para exceções de serviço indisponíveis.

### <a name="the-required-ports-are-being-blocked"></a>As portas necessárias estão sendo bloqueadas
Verifique se todas as [portas necessárias](sql-sdk-connection-modes.md#service-port-ranges) estão habilitadas.

### <a name="client-side-transient-connectivity-issues"></a>Problemas de conectividade transitório do lado do cliente
As exceções de serviço indisponíveis podem surgir quando há problemas de conectividade transitórios que estão causando tempos limite. Normalmente, o rastreamento de pilha relacionado a esse cenário conterá um `ServiceUnavailableException` erro com os detalhes de diagnóstico. Por exemplo:

```java
Exception in thread "main" ServiceUnavailableException{userAgent=azsdk-java-cosmos/4.6.0 Linux/4.15.0-1096-azure JRE/11.0.8, error=null, resourceAddress='null', requestUri='null', statusCode=503, message=Service is currently unavailable, please retry after a while. If this problem persists please contact support.: Message: "" {"diagnostics"}
```

Siga as [etapas de solução de problemas de tempo limite da solicitação](troubleshoot-request-timeout-java-sdk-v4-sql.md#troubleshooting-steps) para resolvê-lo.

### <a name="service-outage"></a>Interrupção do serviço
Verifique o [status do Azure](https://status.azure.com/status) para ver se há um problema em andamento.


## <a name="next-steps"></a>Próximas etapas
* [Diagnostique e solucione](troubleshoot-java-sdk-v4-sql.md) problemas ao usar o SDK do Azure Cosmos DB Java v4.
* Saiba mais sobre as diretrizes de desempenho para o [SDK do Java v4](performance-tips-java-sdk-v4-sql.md).