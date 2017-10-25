---
title: "Definir fluxos de trabalho com JSON - Aplicativos Lógicos do Azure | Microsoft Docs"
description: "Como escrever definições de fluxo de trabalho em JSON para aplicativos lógicos"
author: jeffhollan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 03/29/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: 7f9e5a10066df8a464c285273e77a85c0d562ebb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-workflow-definitions-for-logic-apps-using-json"></a>Criar definições de fluxo de trabalho para aplicativos lógicos usando JSON

Você pode criar definições de fluxo de trabalho para [Aplicativos Lógicos do Azure](logic-apps-what-are-logic-apps.md) com linguagem JSON simples e declarativa. Se ainda não fez isso, primeiro examine [como criar seu primeiro aplicativo lógico com o Designer de Aplicativos Lógicos](logic-apps-create-a-logic-app.md). Confira também a [referência completa da Linguagem de Definição de Fluxo de Trabalho](http://aka.ms/logicappsdocs).

## <a name="repeat-steps-over-a-list"></a>Repetir etapas em uma lista

Para percorrer uma matriz com até 10.000 itens e executar uma ação para cada item, use o [tipo foreach](logic-apps-loops-and-scopes.md).

## <a name="handle-failures-if-something-goes-wrong"></a>Lidar com falhas se algo der errado

Normalmente, convém incluir uma *etapa de correção*: uma lógica que será executada *somente se* uma ou mais de suas chamadas falharem. Este exemplo obtém dados de vários locais, mas, se a chamada falhar, queremos POSTAR uma mensagem em algum lugar para podermos acompanhar a falha posteriormente:  

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "triggers": {
    "Request": {
      "type": "request",
      "kind": "http"
    }
  },
  "actions": {
    "readData": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      }
    },
    "postToErrorMessageQueue": {
      "type": "ApiConnection",
      "inputs": "...",
      "runAfter": {
        "readData": [
          "Failed"
        ]
      }
    }
  },
  "outputs": {}
}
```

Para especificar que `postToErrorMessageQueue` só será executado depois que `readData` `Failed`, use a propriedade `runAfter`, por exemplo, para especificar uma lista de valores possíveis, para que `runAfter` possa ser `["Succeeded", "Failed"]`.

Por fim, como agora este exemplo trata do erro, não marcamos mais a execução como `Failed`. Como adicionamos a etapa para tratar dessa falha neste exemplo, a execução foi `Succeeded`, embora uma etapa `Failed`.

## <a name="execute-two-or-more-steps-in-parallel"></a>Executar duas ou mais etapas em paralelo

Para executar várias ações em paralelo, a propriedade `runAfter` deve ser equivalente em tempo de execução. 

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "triggers": {
    "Request": {
      "kind": "http",
      "type": "Request"
    }
  },
  "actions": {
    "readData": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      }
    },
    "branch1": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {
        "readData": [
          "Succeeded"
        ]
      }
    },
    "branch2": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {
        "readData": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

Neste exemplo, `branch1` e `branch2` são definidos para execução após `readData`. Como resultado, ambas as ramificações são executadas em paralelo. O carimbo de data/hora de ambas as ramificações é idêntico.

![Paralelo](media/logic-apps-author-definitions/parallel.png)

## <a name="join-two-parallel-branches"></a>Unir duas ramificações paralelas

Você pode unir duas ações que são definidas para execução em paralelo com a adição de itens para a propriedade `runAfter` do exemplo anterior.

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-04-01-preview/workflowdefinition.json#",
  "actions": {
    "readData": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {}
    },
    "branch1": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {
        "readData": [
          "Succeeded"
        ]
      }
    },
    "branch2": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {
        "readData": [
          "Succeeded"
        ]
      }
    },
    "join": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {
        "branch1": [
          "Succeeded"
        ],
        "branch2": [
          "Succeeded"
        ]
      }
    }
  },
  "parameters": {},
  "triggers": {
    "Request": {
      "type": "Request",
      "kind": "Http",
      "inputs": {
        "schema": {}
      }
    }
  },
  "contentVersion": "1.0.0.0",
  "outputs": {}
}
```

![Paralelo](media/logic-apps-author-definitions/join.png)

## <a name="map-list-items-to-a-different-configuration"></a>Mapear itens de lista para uma configuração diferente

Em seguida, digamos que desejemos obter conteúdo diferente com base no valor de uma propriedade. Podemos criar um mapa de valores para destinos, como um parâmetro:  

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "specialCategories": {
      "defaultValue": [
        "science",
        "google",
        "microsoft",
        "robots",
        "NSA"
      ],
      "type": "Array"
    },
    "destinationMap": {
      "defaultValue": {
        "science": "http://www.nasa.gov",
        "microsoft": "https://www.microsoft.com/en-us/default.aspx",
        "google": "https://www.google.com",
        "robots": "https://en.wikipedia.org/wiki/Robot",
        "NSA": "https://www.nsa.gov/"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "Request": {
      "type": "Request",
      "kind": "http"
    }
  },
  "actions": {
    "getArticles": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=http://feeds.wired.com/wired/index"
      }
    },
    "forEachArticle": {
      "type": "foreach",
      "foreach": "@body('getArticles').responseData.feed.entries",
      "actions": {
        "ifGreater": {
          "type": "if",
          "expression": "@greater(length(intersection(item().categories, parameters('specialCategories'))), 0)",
          "actions": {
            "getSpecialPage": {
              "type": "Http",
              "inputs": {
                "method": "GET",
                "uri": "@parameters('destinationMap')[first(intersection(item().categories, parameters('specialCategories')))]"
              }
            }
          }
        }
      },
      "runAfter": {
        "getArticles": [
          "Succeeded"
        ]
      }
    }
  }
}
```

Nesse caso, primeiro obtemos uma lista de artigos. Com base na categoria que foi definida como um parâmetro, a segunda etapa usa um mapa para pesquisar a URL e obter o conteúdo.

Alguns itens a serem observados: 

*   A função [`intersection()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#intersection) verifica se a categoria corresponde a uma das categorias definidas conhecidas.

*   Depois que obtemos a categoria, podemos extrair o item do mapa usando colchetes: `parameters[...]`

## <a name="process-strings"></a>Cadeias de caracteres de processo

Você pode usar várias funções para manipular cadeias de caracteres. Por exemplo, suponha que haja uma cadeia de caracteres que queremos passar para um sistema, mas não temos certeza sobre a manipulação adequada da codificação de caracteres. Uma opção é para codificar essa cadeia de caracteres em formato base64. No entanto, para evitar o uso de caracteres de escape em uma URL, vamos substituir alguns caracteres. 

Também queremos uma subcadeia de caracteres do nome do autor da ordem, porque os cinco primeiros caracteres não são usados.

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder1",
        "orderer": "NAME=Contoso"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "request": {
      "type": "request",
      "kind": "http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').orderer,5,sub(length(parameters('order').orderer), 5) )),'+','-') ,'/' ,'_' )}"
      }
    }
  },
  "outputs": {}
}
```

Trabalhando de dentro para fora:

1. Obtenha o [`length()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#length) para o nome do autor da ordem, para obtermos de volta o número total de caracteres.

2. Subtrair 5 porque desejamos uma cadeia de caracteres mais curta.

3. Na verdade, colete o [`substring()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#substring). Vamos começar no `5` do índice e seguir pelo restante da cadeia de caracteres.

4. Converter esta subcadeia de caracteres em uma cadeia de caracteres [`base64()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#base64).

5. [`replace()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#replace) todos os `+` caracteres com `-` caracteres.

6. [`replace()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#replace) todos os `/` caracteres com `_` caracteres.

## <a name="work-with-date-times"></a>Trabalhar com datas e horas

Valores de Data/Hora podem ser úteis, especialmente quando você estiver tentando extrair dados de uma fonte de dados na qual, naturalmente, não há suporte para *gatilhos*. Você também pode usar Datas e Horas para saber o tempo que as diversas etapas estão levando.

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder1"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "Request": {
      "type": "request",
      "kind": "http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://www.example.com/?id=@{parameters('order').id}"
      }
    },
    "ifTimingWarning": {
      "type": "If",
      "expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
      "actions": {
        "timingWarning": {
          "type": "Http",
          "inputs": {
            "method": "GET",
            "uri": "http://www.example.com/?recordLongOrderTime=@{parameters('order').id}&currentTime=@{utcNow('r')}"
          }
        }
      },
      "runAfter": {
        "order": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

Neste exemplo, extraímos `startTime` da etapa anterior. Em seguida, obtemos a hora atual e subtraímos um segundo:

[`addseconds(..., -1)`](https://msdn.microsoft.com/library/azure/mt643789.aspx#addseconds) 

Você pode usar outras unidades de tempo, como `minutes` ou `hours`. Por fim, podemos comparar esses dois valores. Se o primeiro valor for menor que o segundo valor, mais de um segundo terá decorrido desde o primeiro pedido.

Para formatar datas, podemos usar formatadores de cadeia de caracteres. Por exemplo, para obter RFC1123, usamos [`utcnow('r')`](https://msdn.microsoft.com/library/azure/mt643789.aspx#utcnow). Para saber mais sobre a formatação de datas, confira [Linguagem de Definição de Fluxo de Trabalho](https://msdn.microsoft.com/library/azure/mt643789.aspx#utcnow).

## <a name="deployment-parameters-for-different-environments"></a>Parâmetros de implantação para ambientes diferentes

Normalmente, os ciclos de vida de implantação têm um ambiente de desenvolvimento, um ambiente de preparo e um ambiente de produção. Por exemplo, você pode usar a mesma definição em todos esses ambientes, mas usar bancos de dados diferentes. Do mesmo modo, talvez você queira usar a mesma definição em regiões diferentes para alta disponibilidade, mas deseje que cada instância de Aplicativo lógico se comunique com o banco de dados dessa região.
Esse cenário é diferente de obter parâmetros em *tempo de execução* em que, em vez disso, você deve usar a função `trigger()` do exemplo anterior.

Você pode começar com uma definição básica, como neste exemplo:

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "uri": {
            "type": "string"
        }
    },
    "triggers": {
        "request": {
          "type": "request",
          "kind": "http"
        }
    },
    "actions": {
        "readData": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@parameters('uri')"
            }
        }
    },
    "outputs": {}
}
```

Na verdadeira solicitação `PUT` para Aplicativos Lógicos, você pode fornecer o parâmetro `uri`. Como não existe mais um valor padrão, a carga do aplicativo lógico requer este parâmetro:

```
{
    "properties": {},
        "definition": {
          // Use the definition from above here
        },
        "parameters": {
            "connection": {
                "value": "https://my.connection.that.is.per.enviornment"
            }
        }
    },
    "location": "westus"
}
``` 

Em cada ambiente, você pode fornecer um valor diferente para o parâmetro `connection` . 

Para ver todas as opções disponíveis para criar e gerenciar aplicativos lógicos, confira a [documentação da API REST](https://msdn.microsoft.com/library/azure/mt643787.aspx). 
