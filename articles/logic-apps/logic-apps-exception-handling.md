---
title: Tratamento de erros e exceções - Aplicativos Lógicos do Azure | Microsoft Docs
description: Saiba mais sobre os padrões de manipulação de erros e exceções nos Aplicativos Lógicos do Azure
services: logic-apps
ms.service: logic-apps
author: dereklee
ms.author: deli
manager: jeconnoc
ms.date: 01/31/2018
ms.topic: article
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 3f812c1142b5cd40169f7340163295b0f7ea6a4d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60996557"
---
# <a name="handle-errors-and-exceptions-in-azure-logic-apps"></a>Tratar erros e exceções em Aplicativos Lógicos do Azure

A maneira como qualquer arquitetura de integração lida adequadamente com o tempo de inatividade ou problemas causados por sistemas dependentes pode representar um desafio. Para ajudá-lo a criar integrações robustas e resilientes que lidam com problemas e falhas, o Logic Apps oferece uma experiência de primeira classe para lidar com erros e exceções. 

<a name="retry-policies"></a>

## <a name="retry-policies"></a>Políticas de repetição

Para a exceção mais básica e o tratamento de erros, você pode usar uma *política de repetição* em qualquer ação ou acionador onde houver suporte. Uma política de repetição especifica se e como a ação ou o acionador tenta novamente uma solicitação quando a solicitação original expira ou falha, que é qualquer solicitação que resulte em uma resposta 408, 429 ou 5xx. Se nenhuma outra política de repetição for usada, a política padrão será usada. 

Aqui estão os tipos de política de repetição: 

| Type | DESCRIÇÃO | 
|------|-------------| 
| **Padrão** | Essa política envia até quatro novas tentativas em intervalos *exponencialmente crescentes*, que são dimensionados em 7,5 segundos, mas são limitados entre 5 e 45 segundos. | 
| **Intervalo exponencial**  | Essa política aguarda um intervalo aleatório selecionado de um intervalo em crescimento exponencial antes de enviar a próxima solicitação. | 
| **Intervalo fixo**  | Essa política aguarda o intervalo especificado antes de enviar a próxima solicitação. | 
| **Nenhum**  | Não reenvie o pedido. | 
||| 

Para obter informações sobre novos limites de política, consulte [Limites e configuração de aplicativos lógicos](../logic-apps/logic-apps-limits-and-config.md#request-limits). 

### <a name="change-retry-policy"></a>Política de repetição de alteração

Para selecionar uma política de repetição diferente, siga estas etapas: 

1. Abra seu aplicativo lógico no Logic App Designer. 

2. Abra o **configurações** para um gatilho ou ação.

3. Se a ação ou o gatilho suportar novas políticas, em **Repetir política**, selecione o tipo desejado. 

Ou você pode especificar manualmente a política de repetição na seção `inputs` para uma ação ou um gatilho que suporte políticas de repetição. Se você não especificar uma política de repetição, a ação usa a política padrão.

```json
"<action-name>": {
   "type": "<action-type>", 
   "inputs": {
      "<action-specific-inputs>",
      "retryPolicy": {
         "type": "<retry-policy-type>",
         "interval": "<retry-interval>",
         "count": <retry-attempts>,
         "minimumInterval": "<minimum-interval>",
         "maximumInterval": "<maximun-interval>"
      },
      "<other-action-specific-inputs>"
   },
   "runAfter": {}
}
```

*Obrigatório*

| Value | Type | DESCRIÇÃO |
|-------|------|-------------|
| <*tipo de política de repetição*> | Cadeia de caracteres | O tipo de política de repetição que você deseja usar: `default`, `none`, `fixed`, ou `exponential` | 
| <*intervalo de repetição*> | Cadeia de caracteres | O intervalo de repetição em que o valor deve usar [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). O intervalo mínimo de padrão é `PT5S` e o intervalo máximo é `PT1D`. Ao usar a política de intervalo exponencial, você pode especificar valores mínimos e máximos diferentes. | 
| <*tentativas de repetição*> | Número inteiro | O número de tentativas de repetição, que deve estar entre 1 e 90 | 
||||

*Opcional*

| Value | Type | DESCRIÇÃO |
|-------|------|-------------|
| <*intervalo mínimo*> | Cadeia de caracteres | Para a política de intervalo exponencial, o menor intervalo para o intervalo selecionado aleatoriamente no formato [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) | 
| <*intervalo máximo*> | Cadeia de caracteres | Para a política de intervalo exponencial, o maior intervalo para o intervalo selecionado aleatoriamente no formato [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) | 
|||| 

Aqui estão mais informações sobre os diferentes tipos de políticas.

<a name="default-retry"></a>

### <a name="default"></a>Padrão

Se você não especificar uma política de repetição, a ação usará a política padrão, que é na verdade uma política de [intervalo exponencial](#exponential-interval) que envia até quatro novas tentativas em intervalos de aumento exponencial que são dimensionados por 7,5 segundos. O intervalo é limitado entre 5 e 45 segundos. 

Embora não seja explicitamente definido em sua ação ou acionador, aqui está como a política padrão se comporta em uma ação HTTP de exemplo:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "http://myAPIendpoint/api/action",
      "retryPolicy" : {
         "type": "exponential",
         "interval": "PT7S",
         "count": 4,
         "minimumInterval": "PT5S",
         "maximumInterval": "PT1H"
      }
   },
   "runAfter": {}
}
```

### <a name="none"></a>Nenhum

Para especificar que a ação ou gatilho não tentará repetir solicitações com falha, defina o <*tipo de política de repetição*> para `none`.

### <a name="fixed-interval"></a>Intervalo fixo

Para especificar que a ação ou o acionador aguarda o intervalo especificado antes de enviar a próxima solicitação, defina <*retry-policy-type*> como `fixed`.

*Exemplo*

Essa política de repetição tenta receber as últimas notícias mais duas vezes após a primeira solicitação com falha, com um atraso de 30 segundos entre cada tentativa:

```json
"Get_latest_news": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "https://mynews.example.com/latest",
      "retryPolicy": {
         "type": "fixed",
         "interval": "PT30S",
         "count": 2
      }
   }
}
```

<a name="exponential-interval"></a>

### <a name="exponential-interval"></a>Intervalo exponencial

Para especificar que a ação ou o acionador aguarda um intervalo aleatório antes de enviar a próxima solicitação, defina o <*retry-policy-type*> como `exponential`. O intervalo aleatório é selecionado de um intervalo crescente exponencial. Opcionalmente, você também pode substituir os intervalos mínimo e máximo padrão, especificando seus próprios intervalos mínimo e máximo.

**Intervalos de variável aleatória**

Esta tabela mostra como o Logic Apps gera uma variável aleatória uniforme no intervalo especificado para cada nova tentativa até e incluindo o número de novas tentativas:

| Número de Repetições | Intervalo mínimo | Intervalo máximo |
|--------------|------------------|------------------|
| 1 | Max (0, <*intervalo mínimo*>) | min (intervalo, <*máximo intervalo*>) |
| 2 | Max (intervalo, <*intervalo mínimo*>) | min (2 * intervalo, <*máximo intervalo*>) |
| 3 | Max (2 * intervalo, <*intervalo mínimo*>) | min (4 * intervalo, <*máximo intervalo*>) |
| 4 | Max (4 * intervalo, <*intervalo mínimo*>) | min (8 * intervalo, <*máximo intervalo*>) |
| .... | .... | .... | 
|||| 

## <a name="catch-and-handle-failures-with-the-runafter-property"></a>Detectar e lidar com falhas com a propriedade RunAfter

Cada ação de aplicativo lógico declara as ações que devem terminar antes do início da ação, semelhante a como você especifica a ordem das etapas no fluxo de trabalho. Em uma definição de ação, a propriedade **runAfter** define essa ordenação e é um objeto que descreve quais status de ação e ações executam a ação.

Por padrão, todas as ações adicionadas no Designer do Aplicativo Lógico são definidas para serem executadas após a etapa anterior quando o resultado da etapa anterior for com **Êxito**. No entanto, você pode personalizar o valor **runAfter** para que as ações sejam acionadas quando as ações anteriores resultarem em **Falha**, **Ignoradas**, ou uma combinação desses valores. Por exemplo, para adicionar um item a um tópico específico do Barramento de Serviço após uma determinada ação **Insert_Row** falhar, você pode usar este exemplo de definição de **runAfter**:

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

A propriedade **runAfter** é definida para ser executada quando o status de ação **Insert_Row** for **Falha**. Para executar a ação se o status da ação for **Com Êxito**, **Com Falha** ou **Ignorado**, use essa sintaxe:

```json
"runAfter": {
        "Insert_Row": [
            "Failed", "Succeeded", "Skipped"
        ]
    }
```

> [!TIP]
> As ações executadas e concluídas com êxito depois que uma ação anterior falhar serão marcadas como com **Êxito**. Esse comportamento significa que se você detectar todas as falhas em um fluxo de trabalho com êxito, a execução será marcada como com **Êxito**.

<a name="scopes"></a>

## <a name="evaluate-actions-with-scopes-and-their-results"></a>Avaliar ações com escopos e seus resultados

Semelhante à execução de etapas depois de ações individuais com a propriedade **runAfter**, você pode agrupar ações dentro de um [escopo](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md). Você pode usar escopos quando quiser agrupar ações logicamente, avaliar o status agregado do escopo e executar ações com base no status. Depois que todas as ações em um escopo concluem a execução, o próprio escopo também obtém seu próprio status. 

Para verificar o status de um escopo, é possível usar os mesmos critérios utilizados para verificar o status de execução de um aplicativo lógico, como com **Êxito**, com **Falha** e assim por diante. 

Por padrão, quando todas as ações do escopo são bem-sucedidas, o status do escopo é marcado como com **Êxito**. Se a ação final em um escopo resulta em **Falha** ou **Abortado**, o status do escopo fica marcado como **Falha**. 

Para capturar exceções em um escopo com **Falha** e executar ações que lidam com esses erros, você pode usar a propriedade **runAfter** nesse escopo com **Falha**. Dessa maneira, se *quaisquer* ações no escopo falharem e você usar a propriedade **runAfter** para esse escopo, será possível criar uma ação única para capturar falhas.

Para limites nos escopos, consulte [Limites e configurações](../logic-apps/logic-apps-limits-and-config.md).

### <a name="get-context-and-results-for-failures"></a>Obter o contexto e os resultados de falhas

Embora seja útil detectar falhas de um escopo, convém ter o contexto para ajudá-lo a entender exatamente quais ações falharam, além de quais erros ou códigos de status foram retornados. A expressão `@result()` fornece contexto sobre o resultado de todas as ações em um escopo.

A expressão `@result()` aceita um único parâmetro (o nome do escopo) e retorna uma matriz de todos os resultados da ação dentro desse escopo. Esses objetos de ação incluem os mesmos atributos como o  **\@actions()** objeto, como a hora de início, hora de término, status, entradas, as IDs de correlação e saídas da ação. Para enviar o contexto para qualquer ação que falhou dentro de um escopo, você pode facilmente emparelhar uma  **\@result()** funcionar com um **runAfter** propriedade.

Para executar uma ação para cada ação em um escopo que tenha uma **Failed** resultados, e para filtrar a matriz de resultados para as ações que falharam, é possível emparelhar  **\@result()** com um **[ Filtrar matriz](../connectors/connectors-native-query.md)** ação e um [ **para cada** ](../logic-apps/logic-apps-control-flow-loops.md) loop. Você pode pegar o array de resultados filtrados e executar uma ação para cada falha usando o **For each**  loop. 

Aqui está um exemplo, seguido por uma explicação detalhada, que envia uma solicitação HTTP POST com o corpo da resposta para quaisquer ações que falharam no escopo "My_Scope":

```json
"Filter_array": {
   "type": "Query",
   "inputs": {
      "from": "@result('My_Scope')",
      "where": "@equals(item()['status'], 'Failed')"
   },
   "runAfter": {
      "My_Scope": [
         "Failed"
      ]
    }
},
"For_each": {
   "type": "foreach",
   "actions": {
      "Log_exception": {
         "type": "Http",
         "inputs": {
            "method": "POST",
            "body": "@item()['outputs']['body']",
            "headers": {
               "x-failed-action-name": "@item()['name']",
               "x-failed-tracking-id": "@item()['clientTrackingId']"
            },
            "uri": "http://requestb.in/"
         },
         "runAfter": {}
      }
   },
   "foreach": "@body('Filter_array')",
   "runAfter": {
      "Filter_array": [
         "Succeeded"
      ]
   }
}
```

Aqui, está um passo a passo detalhado que descreve o que acontece nesse exemplo:

1. Para obter o resultado de todas as ações dentro de "My_Scope", a ação **Filter Array** usa essa expressão de filtro: `@result('My_Scope')`

2. A condição para **Filter Array** é qualquer item `@result()` que tenha um status igual a **Failed**. Essa condição filtra a matriz que tem todos os resultados de ação "My_Scope" para uma matriz com apenas os resultados de ação com falha.

3. Executar uma **para cada** ação de loop a *matriz filtrada* saídas. Esta etapa executa uma ação para cada resultado de ação com falha que foi filtrado anteriormente.

   Se uma única ação no escopo falhar, as ações na **para cada** loop executado apenas uma vez. 
   Várias ações com falha causam uma ação por falha.

4. Envie um POST HTTP no **Para cada** corpo de resposta do item, que é a expressão "`@item()['outputs']['body']` () ['outputs'] ['body']". 

   A forma do item `@result()` é a mesma que a forma `@actions()` e pode ser analisada da mesma maneira.

5. Inclua dois cabeçalhos personalizados com o nome da ação com falha (`@item()['name']`) e o ID de acompanhamento do cliente de execução com falha (`@item()['clientTrackingId']`).

Para referência, veja um exemplo de um único item `@result()`, mostrando as propriedades **nome**, **corpo** e **clientTrackingId** que são analisadas no exemplo anterior. Outside uma **para cada** ação, `@result()` retorna uma matriz desses objetos.

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

Para executar diferentes padrões de tratamento de exceções, você pode usar as expressões descritas anteriormente neste artigo. Você pode optar por executar uma única ação de tratamento de exceção fora do escopo que aceita toda a matriz filtrada de falhas e remover a ação **For each**. Você também pode incluir outras propriedades úteis dos  **\@result()** resposta conforme descrita anteriormente.

## <a name="azure-diagnostics-and-metrics"></a>Métricas e diagnóstico do Azure

Os padrões anteriores são uma ótima maneira de identificar erros e exceções dentro de uma execução, mas você também pode identificar e responder a erros independentemente da execução em si. 
O [Diagnóstico do Microsoft Azure](../logic-apps/logic-apps-monitor-your-logic-apps.md) fornece uma maneira simples de enviar todos os eventos do fluxo de trabalho, incluindo todos os status de ação e execução, para uma conta de Armazenamento do Azure ou para um hub de eventos criado com Hubs de Eventos do Azure. 

Para avaliar o status de execução, você pode monitorar os logs e as métricas ou publicá-los em qualquer ferramenta de monitoramento que preferir. Uma opção possível é transmitir todos os eventos através dos Hubs de Eventos para o [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/). No Stream Analytics é possível gravar consultas dinâmicas com base em quaisquer anomalias, médias ou falhas dos logs de diagnóstico. Você pode usar o Stream Analytics para enviar informações a outras fontes de dados, como filas, tópicos, SQL, Azure Cosmos DB ou Power BI.

## <a name="next-steps"></a>Próximas etapas

* [Veja como um cliente compila o tratamento de erros com Aplicativos Lógicos do Azure](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [Encontrar mais exemplos e cenários dos Aplicativos Lógicos](../logic-apps/logic-apps-examples-and-scenarios.md)

<!-- References -->
[retryPolicyMSDN]: https://docs.microsoft.com/rest/api/logic/actions-and-triggers#Anchor_9
