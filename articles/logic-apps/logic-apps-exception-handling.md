---
title: "Tratamento de erros e exceções - Aplicativos Lógicos do Azure | Microsoft Docs"
description: "Padrões para tratamento de erros e exceções em Aplicativos Lógicos do Azure"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: e50ab2f2-1fdc-4d2a-be40-995a6cc5a0d4
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: 9af2f71b3d288cc6f4e271d0915545d43a1249bc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="handle-errors-and-exceptions-in-azure-logic-apps"></a>Tratar erros e exceções em Aplicativos Lógicos do Azure

Os Aplicativos Lógicos do Azure fornecem ferramentas avançadas e padrões para ajudá-lo a garantir que suas integrações sejam robustas e resistentes a falhas. Qualquer arquitetura de integração apresenta o desafio de lidar adequadamente com o tempo de inatividade ou problemas de sistemas dependentes. Os Aplicativos lógicos transformam o tratamento de erros em uma experiência de qualidade, dando a você as ferramentas necessárias para agir em relação às exceções e aos erros em seus fluxos de trabalho.

## <a name="retry-policies"></a>Políticas de repetição

Uma política de repetição é o tipo mais básico de exceção e de tratamento de erros. Se uma solicitação inicial tiver atingido o tempo limite ou falhado (qualquer solicitação que resulte em uma resposta 429 ou 5xx), essa política definirá se a ação deverá ser repetida. Por padrão, todas as ações tentam novamente por mais quatro vezes em intervalos de 20 segundos. Portanto, se a primeira solicitação recebeu uma resposta `500 Internal Server Error`, o mecanismo de fluxo de trabalho pausa por 20 segundos e tenta a solicitação novamente. Se, depois de todas as novas tentativas, a resposta ainda for uma exceção ou falha, o fluxo de trabalho prosseguirá e marcará o status da ação como `Failed`.

Você pode configurar políticas de repetição nas **entradas** para uma ação específica. Por exemplo, você pode configurar uma política de repetição para tentar novamente até quatro vezes em intervalos de uma hora. Para obter detalhes completos sobre as propriedades de entrada, confira [Ações de fluxo de trabalho e gatilhos][retryPolicyMSDN].

```json
"retryPolicy" : {
      "type": "<type-of-retry-policy>",
      "interval": <retry-interval>,
      "count": <number-of-retry-attempts>
    }
```

Se você quisesse que a ação de HTTP repetisse quatro vezes e aguardasse dez minutos entre cada tentativa, usaria a seguinte definição:

```json
"HTTP": 
{
    "inputs": {
        "method": "GET",
        "uri": "http://myAPIendpoint/api/action",
        "retryPolicy" : {
            "type": "fixed",
            "interval": "PT10M",
            "count": 4
        }
    },
    "runAfter": {},
    "type": "Http"
}
```

Para obter mais informações sobre a sintaxe com suporte, confira a [seção de política de repetição em Ações de fluxo de trabalho e gatilhos][retryPolicyMSDN].

## <a name="catch-failures-with-the-runafter-property"></a>Detectar falhas com a propriedade RunAfter

Cada ação de aplicativo lógico declara quais ações devem ser concluídas antes do início da ação, como ordenação de etapas no fluxo de trabalho. Na definição de ação, essa ordem é conhecido como a propriedade `runAfter`. Essa propriedade é um objeto que descreve quais ações e status de ação executam a ação. Por padrão, todas as ações adicionadas por meio do Designer de Aplicativos Lógicos serão definidas como `runAfter` na etapa anterior se a etapa anterior for `Succeeded`. No entanto, você pode personalizar esse valor para disparar ações quando as ações anteriores são `Failed`, `Skipped` ou um conjunto possível desses valores. Se você quisesse adicionar um item a um tópico do Barramento de Serviço designado após uma ação específica `Insert_Row` falhar, usaria a seguinte configuração `runAfter`:

```json
"Send_message": {
    "inputs": {
        "body": {
            "ContentData": "@{encodeBase64(body('Insert_Row'))}",
            "ContentType": "{ \"content-type\" : \"application/json\" }"
        },
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/servicebus"
            },
            "connection": {
                "name": "@parameters('$connections')['servicebus']['connectionId']"
            }
        },
        "method": "post",
        "path": "/@{encodeURIComponent('failures')}/messages"
    },
    "runAfter": {
        "Insert_Row": [
            "Failed"
        ]
    }
}
```

Observe que a propriedade `runAfter` está definida para ser acionada se a ação `Insert_Row` é `Failed`. Para executar a ação se o status da ação for `Succeeded`, `Failed` ou `Skipped`, use a seguinte sintaxe:

```json
"runAfter": {
        "Insert_Row": [
            "Failed", "Succeeded", "Skipped"
        ]
    }
```

> [!TIP]
> Ações que são executadas e concluídas com êxito depois que uma ação anterior falha são marcadas como `Succeeded`. Esse comportamento significa que se você detectar todas as falhas em um fluxo de trabalho com êxito, a execução será marcada como `Succeeded`.

## <a name="scopes-and-results-to-evaluate-actions"></a>Escopos e resultados para avaliar ações

Assim como você pode executar depois de ações individuais, também pode agrupar ações em um mesmo [escopo](../logic-apps/logic-apps-loops-and-scopes.md), o que funciona como um agrupamento lógico de ações. Escopos são úteis para organizar suas ações de aplicativo lógico e para realizar avaliações de agregação do status de um escopo. O escopo em si recebe um status depois que todas as ações em um escopo são concluídas. O status do escopo é determinado com os mesmos critérios que uma execução. Se a ação final em uma ramificação de execução for `Failed` ou `Aborted`, o status será `Failed`.

Para acionar ações específicas para as falhas que ocorreram no escopo, você pode usar `runAfter` com um escopo que está marcado como `Failed`. Se *quaisquer* ações no escopo falharem, a execução após a falha de um escopo permitirá que você crie uma única ação para detectar falhas.

### <a name="getting-the-context-of-failures-with-results"></a>Obtendo o contexto de falhas com resultados

Embora seja útil detectar falhas de um escopo, convém ter o contexto para ajudá-lo a entender exatamente quais ações falharam e quais erros ou códigos de status foram retornados. A função de fluxo de trabalho `@result()` fornece o contexto sobre o resultado de todas as ações em um escopo.

`@result()` usa um único parâmetro, o nome do escopo, e retorna uma matriz de todos os resultados de ação nesse escopo. Esses objetos de ação incluem os mesmos atributos do objeto `@actions()` , incluindo a hora de início, a hora de término, o status, as entradas, as IDs de correlação e as saídas da ação. Para enviar o contexto de qualquer ação que falhou em um escopo, você pode facilmente combinar uma função `@result()` a um `runAfter`.

Para executar uma ação *para cada* ação em um escopo que `Failed`, filtre a matriz de resultados para ações que falharam. Você pode combinar `@result()` a uma ação de **[Matriz de Filtro](../connectors/connectors-native-query.md)** e um loop **[ForEach](../logic-apps/logic-apps-loops-and-scopes.md)**. Você pode usar a matriz de resultados filtrados e executar uma ação para cada falha usando o loop **ForEach** . Aqui está um exemplo, seguido de uma explicação detalhada, que envia uma solicitação HTTP POST com o corpo da resposta de qualquer ação que falhou no escopo `My_Scope`.

```json
"Filter_array": {
    "inputs": {
        "from": "@result('My_Scope')",
        "where": "@equals(item()['status'], 'Failed')"
    },
    "runAfter": {
        "My_Scope": [
            "Failed"
        ]
    },
    "type": "Query"
},
"For_each": {
    "actions": {
        "Log_Exception": {
            "inputs": {
                "body": "@item()['outputs']['body']",
                "method": "POST",
                "headers": {
                    "x-failed-action-name": "@item()['name']",
                    "x-failed-tracking-id": "@item()['clientTrackingId']"
                },
                "uri": "http://requestb.in/"
            },
            "runAfter": {},
            "type": "Http"
        }
    },
    "foreach": "@body('Filter_array')",
    "runAfter": {
        "Filter_array": [
            "Succeeded"
        ]
    },
    "type": "Foreach"
}
```

Aqui está um passo a passo detalhado para descrever o que acontece:

1. Para obter o resultado de todas as ações em `My_Scope`, a ação **Matriz de Filtro** filtra `@result('My_Scope')`.

2. A condição de **Matriz de Filtro** é qualquer item `@result()` que tenha um status igual a `Failed`. Essa condição filtra a matriz com todos os resultados de ação de `My_Scope` para uma matriz somente com resultados de ação com falha.

3. Execute uma ação **For Each** nas saídas **Matriz Filtrada**. Esta etapa executa uma ação *para cada* resultado de ação com falha que foi filtrado anteriormente.

    Se uma única ação no escopo tiver falhado, as ações em `foreach` serão executadas apenas uma vez. 
    Muitas ações com falha causam uma ação por falha.

4. Envie um HTTP POST no corpo da resposta do item `foreach` ou `@item()['outputs']['body']`. A forma do item `@result()` é igual à forma `@actions()` e pode ser analisada da mesma maneira.

5. Inclua dois cabeçalhos personalizados com o nome da ação com falha `@item()['name']` e a ID de rastreamento do cliente executado com falha `@item()['clientTrackingId']`.

Para referência, aqui está um exemplo de um único item `@result()`, mostrando as propriedades `name`, `body` e `clientTrackingId` que são analisadas no exemplo anterior. Fora de um `foreach`, `@result()` retorna uma matriz desses objetos.

```json
{
    "name": "Example_Action_That_Failed",
    "inputs": {
        "uri": "https://myfailedaction.azurewebsites.net",
        "method": "POST"
    },
    "outputs": {
        "statusCode": 404,
        "headers": {
            "Date": "Thu, 11 Aug 2016 03:18:18 GMT",
            "Server": "Microsoft-IIS/8.0",
            "X-Powered-By": "ASP.NET",
            "Content-Length": "68",
            "Content-Type": "application/json"
        },
        "body": {
            "code": "ResourceNotFound",
            "message": "/docs/folder-name/resource-name does not exist"
        }
    },
    "startTime": "2016-08-11T03:18:19.7755341Z",
    "endTime": "2016-08-11T03:18:20.2598835Z",
    "trackingId": "bdd82e28-ba2c-4160-a700-e3a8f1a38e22",
    "clientTrackingId": "08587307213861835591296330354",
    "code": "NotFound",
    "status": "Failed"
}
```

Para executar diferentes padrões de tratamento de exceções, você pode usar as expressões mostradas anteriormente. Você pode optar por executar uma única ação de tratamento de exceção fora do escopo que aceita toda a matriz filtrada de falhas e remover a `foreach`. Você também pode incluir outras propriedades úteis da resposta `@result()` mostrada anteriormente.

## <a name="azure-diagnostics-and-telemetry"></a>Diagnósticos do Azure e telemetria

Os padrões anteriores são uma ótima maneira de identificar erros e exceções dentro de uma execução, mas você também pode identificar e responder a erros independentemente da execução em si. 
[Diagnóstico do Azure](../logic-apps/logic-apps-monitor-your-logic-apps.md) fornece uma maneira simples de enviar todos os eventos de fluxo de trabalho (incluindo todos os status de execução e de ação) a uma conta de Armazenamento do Azure ou a um Hub de Eventos do Azure. Para avaliar o status de execução, você pode monitorar os logs e as métricas ou publicá-los em qualquer ferramenta de monitoramento que preferir. Uma opção possível é transmitir todos os eventos usando o Hub de Eventos do Azure no [Stream Analytics](https://azure.microsoft.com/services/stream-analytics/). No Stream Analytics, você pode dar baixa em consultas nas anomalias, médias ou falhas dos logs de diagnóstico. O Stream Analytics pode enviar a saída facilmente para outras fontes de dados, como filas, tópicos, o SQL, o Azure Cosmos DB e o Power BI.

## <a name="next-steps"></a>Próximas etapas

* [Veja como um cliente compila o tratamento de erros com Aplicativos Lógicos do Azure](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [Encontrar mais exemplos e cenários dos Aplicativos Lógicos](../logic-apps/logic-apps-examples-and-scenarios.md)
* [Saiba como criar implantações automatizadas para aplicativos lógicos](../logic-apps/logic-apps-create-deploy-template.md)
* [Compilar e implantar aplicativos lógicos no Visual Studio](logic-apps-deploy-from-vs.md)

<!-- References -->
[retryPolicyMSDN]: https://docs.microsoft.com/rest/api/logic/actions-and-triggers#Anchor_9
