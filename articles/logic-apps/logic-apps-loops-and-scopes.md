---
title: "Criar loops e escopos ou retirar dados de lote em fluxos de trabalho - Aplicativos Lógicos do Azure | Microsoft Docs"
description: "Criar loops para iterar por meio de dados, as ações de grupo em escopos, ou retirar dados de lote para iniciar mais fluxos de trabalho em Aplicativos Lógicos do Azure."
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 75b52eeb-23a7-47dd-a42f-1351c6dfebdc
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: 413a2ba9107ca259ed577825bf0a17ff5622f1ac
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="logic-apps-loops-scopes-and-debatching"></a>Loops, Escopos e Debatch dos Aplicativos Lógicos
  
Os Aplicativos Lógicos fornecem várias maneiras de trabalhar com matrizes, coleções, lotes e loops em um fluxo de trabalho.
  
## <a name="foreach-loop-and-arrays"></a>Matrizes e loop ForEach
  
Os Aplicativos Lógicos permitem que você faça um loop em um conjunto de dados e execute uma ação para cada item.  Isso é possível por meio da ação `foreach` .  No designer, você pode especificar a adição de um loop for each.  Depois de selecionar a matriz em que você deseja iterar, você poderá começar a adicionar ações.  No momento, você está limitado a apenas uma ação por loop foreach, mas essa restrição será eliminada nas próximas semanas.  Uma vez no loop, você poderá começar a especificar o que deve ocorrer em cada valor da matriz.

Se você estiver usando o modo de exibição de código, poderá especificar um loop for each como o abaixo.  Este é um exemplo de um loop for each que envia um email para cada endereço de email com 'microsoft.com':

``` json
{
    "email_filter": {
        "type": "query",
        "inputs": {
            "from": "@triggerBody()['emails']",
            "where": "@contains(item()['email'], 'microsoft.com')"
        }
    },
    "forEach_email": {
        "type": "foreach",
        "foreach": "@body('email_filter')",
        "actions": {
            "send_email": {
                "type": "ApiConnection",
                "inputs": {
                "body": {
                    "to": "@item()",
                    "from": "me@contoso.com",
                    "message": "Hello, thank you for ordering"
                },
                "host": {
                    "connection": {
                        "id": "@parameters('$connections')['office365']['connection']['id']"
                    }
                },
                }
            }
        },
        "runAfter":{
            "email_filter": [ "Succeeded" ]
        }
    }
}
```
  
  Uma ação `foreach` pode iterar em matrizes até 5.000 linhas.  Por padrão, cada iteração será executada paralelamente.  

### <a name="sequential-foreach-loops"></a>Loops ForEach sequenciais

Para habilitar um loop foreach para ser executado sequencialmente, a opção de operação `Sequential` deve ser adicionada.

``` json
"forEach_email": {
        "type": "foreach",
        "foreach": "@body('email_filter')",
        "operationOptions": "Sequential",
        "..."
}
```
  
## <a name="until-loop"></a>Loop Until
  
  Você pode executar uma ação ou uma série de ações até que uma condição seja atendida.  O cenário mais comum para isso é chamar um ponto de extremidade até você chegar à resposta que está procurando.  No designer, você pode especificar a adição de um loop until.  Depois de adicionar ações dentro do loop, você poderá definir a condição de saída, bem como os limites do loop.  Há um atraso de um minuto entre os ciclos de loop.
  
  Se você estiver usando o modo de exibição de código, poderá especificar um loop unitl como o abaixo.  Este é um exemplo de chamada a um ponto de extremidade HTTP até que o corpo da resposta tenha o valor 'Concluído'.  Ele será concluído quando a 
  
  * Resposta HTTP tiver o status 'Concluído'
  * Ele tentou por uma hora
  * Ele entrou em loop 100 vezes
  
  ``` json
  {
      "until_successful":{
        "type": "until",
        "expression": "@equals(actions('http')['status'], 'Completed')",
        "limit": {
            "count": 100,
            "timeout": "PT1H"
        },
        "actions": {
            "create_resource": {
                "type": "http",
                "inputs": {
                    "url": "http://provisionRseource.com",
                    "body": {
                        "resourceId": "@triggerBody()"
                    }
                }
            }
        }
      }
  }
  ```
  
## <a name="spliton-and-debatching"></a>SplitOn e debatching

Às vezes, um gatilho pode receber uma matriz de itens nos quais deseja fazer debatch e iniciar um fluxo de trabalho por item.  Isso pode ser feito por meio do comando `spliton` .  Por padrão, se o gatilho swagger especificar uma carga que seja uma matriz, um `spliton` será adicionado e iniciará uma execução por item.  SplitOn só pode ser adicionado a um gatilho.  Isso pode ser manualmente configurado ou substituído na exibição de código de definição.  Atualmente, o SplitOn pode fazer debatch em matrizes de até 5.000 itens.  Você não pode ter um `spliton` e também implementar o padrão de resposta síncrona.  Qualquer fluxo de trabalho chamado com uma ação `response` além de `spliton` será executado de forma assíncrona e enviará uma resposta `202 Accepted` imediata.  

SplitOn pode ser especificado no modo de exibição de código como o exemplo a seguir.  Isso recebe uma matriz de itens e faz debatch em cada linha.

```
{
    "myDebatchTrigger": {
        "type": "Http",
        "inputs": {
            "url": "http://getNewCustomers",
        },
        "recurrence": {
            "frequencey": "Second",
            "interval": 15
        },
        "spliton": "@triggerBody()['rows']"
    }
}
```

## <a name="scopes"></a>Escopos

É possível agrupar uma série de ações usando um escopo.  Isso é particularmente útil para implementar o tratamento de exceções.  No designer, você pode adicionar um novo escopo e começar a adicionar ações dentro dele.  É possível definir escopos no modo de exibição de código semelhante ao seguinte:


```
{
    "myScope": {
        "type": "scope",
        "actions": {
            "call_bing": {
                "type": "http",
                "inputs": {
                    "url": "http://www.bing.com"
                }
            }
        }
    }
}
```