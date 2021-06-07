---
title: Evitar erros de limitação de taxa para as operações Azure Cosmos DB API para MongoDB.
description: Saiba como impedir que seu Azure Cosmos DB API para operações do MongoDB atinjam erros de limitação de taxa com o recurso SSR (repetição do servidor).
author: gahl-levy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 01/13/2021
ms.author: gahllevy
ms.openlocfilehash: 1e9062b111c30efa90b98c4ebcee710b1d975a1d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99507923"
---
# <a name="prevent-rate-limiting-errors-for-azure-cosmos-db-api-for-mongodb-operations"></a>Evitar erros de limitação de taxa para as operações Azure Cosmos DB API para MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

As operações Azure Cosmos DB API para MongoDB podem falhar com erros de limitação de taxa (16500/429) se excederem o limite de taxa de transferência (RUs) da coleção. 

Você pode habilitar o recurso de repetição do lado do servidor (SSR) e permitir que o servidor Repita essas operações automaticamente. As solicitações são repetidas após um pequeno atraso para todas as coleções em sua conta. Esse recurso é uma alternativa conveniente ao tratamento de erros de limitação de taxa no aplicativo cliente.

## <a name="use-the-azure-portal"></a>Use o Portal do Azure

1. Entre no [portal do Azure](https://portal.azure.com/).

1. Navegue até sua conta da API do Azure Cosmos DB para MongoDB.

1. Vá para o painel **recursos** abaixo da seção **configurações** .

1. Selecione **nova tentativa do servidor**.

1. Clique em **habilitar** para habilitar esse recurso para todas as coleções em sua conta.

:::image type="content" source="./media/prevent-rate-limiting-errors/portal-features-server-side-retry.png" alt-text="Captura de tela do recurso de repetição do lado do servidor para Azure Cosmos DB API para MongoDB":::

## <a name="use-the-azure-cli"></a>Usar a CLI do Azure

1. Verifique se o SSR já está habilitado para sua conta:
```bash
az cosmosdb show --name accountname --resource-group resourcegroupname
```
2. **Habilitar** SSR para todas as coleções em sua conta de banco de dados. Pode levar até 15 min para que essa alteração entre em vigor.
```bash
az cosmosdb update --name accountname --resource-group resourcegroupname --capabilities EnableMongo DisableRateLimitingResponses
```
O comando a seguir **desabilitará** SSR para todas as coleções em sua conta de banco de dados removendo "DisableRateLimitingResponses" da lista de recursos. Pode levar até 15 min para que essa alteração entre em vigor.
```bash
az cosmosdb update --name accountname --resource-group resourcegroupname --capabilities EnableMongo
```

## <a name="frequently-asked-questions"></a>Perguntas frequentes
* Como as solicitações são repetidas?
    * As solicitações são repetidas continuamente (repetidamente) até que um tempo limite de 60 segundos seja atingido. Se o tempo limite for atingido, o cliente receberá uma [exceção ExceededTimeLimit (50)](mongodb-troubleshoot.md).
*  Como posso monitorar os efeitos de SSR?
    *  Você pode exibir os erros de limitação de taxa (429s) que são repetidos no lado do servidor no painel Cosmos DB métricas. Lembre-se de que esses erros não vão para o cliente quando o SSR está habilitado, uma vez que eles são manipulados e repetidos no lado do servidor. 
    *  Você pode pesquisar por entradas de log que contenham "estimatedDelayFromRateLimitingInMilliseconds" em seus [logs de recursos Cosmos DB](cosmosdb-monitor-resource-logs.md).
*  O SSR afeta meu nível de consistência?
    *  SSR não afeta a consistência de uma solicitação. As solicitações serão repetidas no lado do servidor se forem limitadas por taxa (com um erro 429). 
*  O SSR afeta qualquer tipo de erro que meu cliente possa receber?
    *  Não, o SSR afeta apenas os erros de limitação de taxa (429s), repetindo-os no lado do servidor. Esse recurso impede que você precise manipular erros de limitação de taxa no aplicativo cliente. Todos os [outros erros](mongodb-troubleshoot.md) vão para o cliente. 

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como solucionar erros comuns, consulte este artigo:

* [Solucionar problemas comuns na API do Azure Cosmos DB para MongoDB](mongodb-troubleshoot.md)
