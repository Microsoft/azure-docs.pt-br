---
title: Gatilho de Azure Functions para Cosmos DB configuração avançada
description: Saiba como configurar o log e a política de conexão usada pelo gatilho Azure Functions para Cosmos DB
author: ealsur
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/19/2020
ms.author: maquaran
ms.openlocfilehash: f753010eb994c9f3c286ad6eca6392ca7b643075
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97932904"
---
# <a name="how-to-configure-logging-and-connectivity-with-the-azure-functions-trigger-for-cosmos-db"></a>Como configurar o log e a conectividade com o gatilho Azure Functions para Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Este artigo descreve as opções de configuração avançadas que você pode definir ao usar o gatilho de Azure Functions para Cosmos DB.

## <a name="enabling-trigger-specific-logs"></a>Habilitando logs específicos do gatilho

O gatilho do Azure Functions para Cosmos DB usa a [Biblioteca do Processador do Feed de Alterações](./change-feed-processor.md) internamente e a biblioteca gera um conjunto de logs de integridade que podem ser usados para monitorar operações internas para [fins de solução de problemas](./troubleshoot-changefeed-functions.md).

Os logs de integridade descrevem como o gatilho do Azure Functions para Cosmos DB se comporta ao tentar executar operações durante cenários de balanceamento de carga ou de inicialização.

### <a name="enabling-logging"></a>Como habilitar o registro em log

Para habilitar o registro em log ao usar o gatilho do Azure Functions para Cosmos DB, localize o arquivo `host.json` no projeto do Azure Functions ou no aplicativo do Azure Functions e [configure o nível de registro em log necessário](../azure-functions/functions-monitoring.md#log-levels-and-categories). Habilite os rastreamentos para  `Host.Triggers.CosmosDB` , conforme mostrado no exemplo a seguir:

```js
{
  "version": "2.0",
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "Host.Triggers.CosmosDB": "Trace"
    }
  }
}
```

Após a implantação da Função do Azure com a configuração atualizada, você verá os logs do gatilho do Azure Functions para Cosmos DB como parte dos seus rastreamentos. É possível exibir os logs em seu provedor de registro em log configurado na *Categoria* `Host.Triggers.CosmosDB`.

### <a name="query-the-logs"></a>Consultar os logs

Execute a seguinte consulta para consultar os logs gerados pelo gatilho do Azure Functions para Cosmos DB na [Análise do Azure Application Insights](../azure-monitor/log-query/log-query-overview.md):

```sql
traces
| where customDimensions.Category == "Host.Triggers.CosmosDB"
```

## <a name="configuring-the-connection-policy"></a>Configurando a política de conexão

Há dois modos de conexão – modo Direto e o modo de Gateway. Para saber mais sobre esses modos de conexão, confira o artigo [modos de conexão](sql-sdk-connection-modes.md) . Por padrão, o **Gateway** é usado para estabelecer todas as conexões no gatilho do Azure Functions para o Azure Cosmos DB. No entanto, talvez ele não seja a melhor opção para cenários orientados ao desempenho.

### <a name="changing-the-connection-mode-and-protocol"></a>Como alterar o modo de conexão e o protocolo

Há duas definições de configuração principais disponíveis para configurar a política de conexão do cliente – o **modo de conexão** e o **protocolo de conexão**. Você pode alterar o modo e o protocolo de conexão padrões usados pelo gatilho do Azure Functions para o Cosmos DB e todas as [associações do Azure Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2-output.md). Para alterar as configurações padrão, você precisará localizar o arquivo `host.json` no projeto do Azure Functions ou no Aplicativo do Azure Functions e adicionar a seguinte [configuração extra](../azure-functions/functions-bindings-cosmosdb-v2-output.md#hostjson-settings):

```js
{
  "cosmosDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

Em que `connectionMode` precisa ter o modo de conexão desejado (Direto ou Gateway) e `protocol`, o protocolo de conexão desejado (TCP ou HTTPS). 

Se o projeto do Azure Functions estiver funcionando com o Azure Functions V1 Runtime, a configuração terá uma pequena diferença de nome e você deverá usar `documentDB` em vez de `cosmosDB`:

```js
{
  "documentDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

> [!NOTE]
> Ao hospedar seu aplicativo de funções em um plano de consumo, cada instância tem um limite na quantidade de conexões de soquete que ele pode manter. Ao trabalhar com o modo direto/TCP, o design de mais conexões é criado e pode atingir o [limite do plano de consumo](../azure-functions/manage-connections.md#connection-limit). nesse caso, você pode usar o modo de gateway ou hospedar seu aplicativo de funções em um [plano Premium](../azure-functions/functions-premium-plan.md) ou em um [plano dedicado (serviço de aplicativo)](../azure-functions/dedicated-plan.md).

## <a name="next-steps"></a>Próximas etapas

* [Limites de conexão no Azure Functions](../azure-functions/manage-connections.md#connection-limit)
* [Habilitar o monitoramento](../azure-functions/functions-monitoring.md) em seus aplicativos do Azure Functions.
* Saiba como [Diagnosticar e solucionar problemas comuns](./troubleshoot-changefeed-functions.md) ao usar o gatilho do Azure Functions para Cosmos DB.