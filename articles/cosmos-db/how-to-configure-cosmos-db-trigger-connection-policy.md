---
title: Gatilho do Azure Functions para a política de conexão do Cosmos DB
description: Saiba como configurar a política de conexão usada pelo gatilho do Azure Functions para o Cosmos DB
author: ealsur
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/17/2019
ms.author: maquaran
ms.openlocfilehash: 359b6a905e64046aad62b70ae53b993c86884ad2
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335633"
---
# <a name="how-to-configure-the-connection-policy-used-by-azure-functions-trigger-for-cosmos-db"></a>Como configurar a política de conexão usada pelo gatilho do Azure Functions para o Cosmos DB

Este artigo descreve como você pode configurar a política de conexão ao usar o gatilho do Azure Functions para o Cosmos DB a fim de se conectar à sua conta do Azure Cosmos.

## <a name="why-is-the-connection-policy-important"></a>Por que a política de conexão é importante?

Há dois modos de conexão – modo Direto e o modo de Gateway. Para saber mais sobre esses modos de conexão, confira o artigo [Dicas de desempenho](./performance-tips.md#networking). Por padrão, o **Gateway** é usado para estabelecer todas as conexões no gatilho do Azure Functions para o Azure Cosmos DB. No entanto, talvez ele não seja a melhor opção para cenários orientados ao desempenho.

## <a name="changing-the-connection-mode-and-protocol"></a>Como alterar o modo de conexão e o protocolo

Há duas definições de configuração principais disponíveis para configurar a política de conexão do cliente – o **modo de conexão** e o **protocolo de conexão**. Você pode alterar o modo e o protocolo de conexão padrões usados pelo gatilho do Azure Functions para o Cosmos DB e todas as [associações do Azure Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2.md#output). Para alterar as configurações padrão, você precisará localizar o arquivo `host.json` no projeto do Azure Functions ou no Aplicativo do Azure Functions e adicionar a seguinte [configuração extra](../azure-functions/functions-bindings-cosmosdb-v2.md#hostjson-settings):

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
> Ao trabalhar com o plano de Hospedagem do Plano de Consumo do Azure Functions, cada instância terá um limite na quantidade de Conexões de Soquete que ela pode manter. Ao trabalhar com o modo Direto/TCP, por design, mais conexões são criadas e podem atingir o [limite do Plano de Consumo](../azure-functions/manage-connections.md#connection-limit); nesse caso, você pode usar o modo de Gateway ou executar o Azure Functions no [Modo de Serviço de Aplicativo](../azure-functions/functions-scale.md#app-service-plan).

## <a name="next-steps"></a>Próximas etapas

* [Limites de conexão no Azure Functions](../azure-functions/manage-connections.md#connection-limit)
* [Dicas de desempenho do Azure Cosmos DB](./performance-tips.md)
* [Exemplos de código](https://github.com/ealsur/serverless-recipes/tree/master/connectionmode)
