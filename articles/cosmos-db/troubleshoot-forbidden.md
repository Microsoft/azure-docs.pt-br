---
title: Solucionar problemas Azure Cosmos DB exceções proibidas
description: Saiba como diagnosticar e corrigir exceções proibidas.
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 02/05/2021
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 0854e5d2c323df695460908c45714673756328c2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99971897"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-forbidden-exceptions"></a>Diagnosticar e solucionar problemas Azure Cosmos DB exceções proibidas
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

O código de status HTTP 403 representa que a solicitação está proibida de ser concluída.

## <a name="firewall-blocking-requests"></a>Solicitações de bloqueio de firewall
Nesse cenário, é comum ver erros como os abaixo:

```
Request originated from client IP {...} through public internet. This is blocked by your Cosmos DB account firewall settings.
```

```
Request is blocked. Please check your authorization token and Cosmos DB account firewall settings
```

### <a name="solution"></a>Solução
Verifique se as configurações atuais do [Firewall](how-to-configure-firewall.md) estão corretas e inclua os IPs ou redes das quais você está tentando se conectar.
Se você os atualizou recentemente, tenha em mente que as alterações podem levar **até 15 minutos para serem aplicadas**.

## <a name="next-steps"></a>Próximas etapas
* Configure o [Firewall de IP](how-to-configure-firewall.md).
* Configure o acesso de [redes virtuais](how-to-configure-vnet-service-endpoint.md).
* Configure o acesso de [pontos de extremidade privados](how-to-configure-private-endpoints.md).
