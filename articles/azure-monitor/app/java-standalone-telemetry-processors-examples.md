---
title: Exemplos de processadores de telemetria-Azure Monitor Application Insights para Java
description: Exemplos que ilustram processadores de telemetria no Azure Monitor Application Insights para Java
ms.topic: conceptual
ms.date: 12/29/2020
author: kryalama
ms.custom: devx-track-java
ms.author: kryalama
ms.openlocfilehash: 9b29c9611359c97c4097ad0b90ee2673bb28f37c
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98696305"
---
# <a name="telemetry-processors-examples---azure-monitor-application-insights-for-java"></a>Exemplos de processadores de telemetria-Azure Monitor Application Insights para Java

## <a name="includeexclude-samples"></a>Incluir/excluir amostras

### <a name="1-include-spans"></a>1. incluir spans

O a seguir demonstra como incluir spans para esse processador de atributos. Todas as outras extensões que não correspondem às propriedades não são processadas por esse processador.

Estas são as condições a serem atendidas para uma correspondência:
* O nome do span deve ser igual a "spana" ou "spanB" 

Veja a seguir as extensões que correspondem às propriedades include e as ações do processador são aplicadas.
* Nome do Span1: atributos ' spana ': {env: dev, test_request: 123, credit_card: 1234}
* Nome do Span2: atributos ' spanB ': {env: dev, test_request: false}
* Nome do Span3: atributos ' spana ': {env: 1, test_request: dev, credit_card: 1234}

O trecho a seguir não corresponde às propriedades include e as ações do processador não são aplicadas.
* Nome do Span4: atributos ' spanC ': {env: dev, test_request: false}

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "spanNames": [
            "spanA",
            "spanB"
          ]
        },
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

### <a name="2-exclude-spans"></a>2. excluir spans

O seguinte demonstra a exclusão de Spans para esse processador de atributos. Todas as extensões que correspondem às propriedades não são processadas por esse processador.

Estas são as condições a serem atendidas para uma correspondência:
* O nome do span deve ser igual a "spana" ou "spanB" 

A seguir estão as extensões que correspondem às propriedades de exclusão e as ações do processador não são aplicadas.
* Nome do Span1: atributos ' spana ': {env: dev, test_request: 123, credit_card: 1234}
* Nome do Span2: atributos ' spanB ': {env: dev, test_request: false}
* Nome do Span3: atributos ' spana ': {env: 1, test_request: dev, credit_card: 1234}

O trecho a seguir não corresponde às propriedades Exclude e as ações do processador são aplicadas.
* Nome do Span4: atributos ' spanC ': {env: dev, test_request: false}

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "exclude": {
          "matchType": "strict",
          "spanNames": [
            "spanA",
            "spanB"
          ]
        },
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

### <a name="3-excludemulti-spans"></a>3. ExcludeMulti spans

O seguinte demonstra a exclusão de Spans para esse processador de atributos. Todas as extensões que correspondem às propriedades não são processadas por esse processador.

Estas são as condições a serem atendidas para uma correspondência:
* Um atributo (' env ', ' dev ') deve existir no span para uma correspondência.
* Desde que haja um atributo com a chave ' test_request ' no span, há uma correspondência.

A seguir estão as extensões que correspondem às propriedades de exclusão e as ações do processador não são aplicadas.
* Nome do Span1: atributos ' spanB ': {env: dev, test_request: 123, credit_card: 1234}
* Nome do Span2: atributos ' spana ': {env: dev, test_request: false}

O trecho a seguir não corresponde às propriedades Exclude e as ações do processador são aplicadas.
* Nome do Span3: atributos ' spanB ': {env: 1, test_request: dev, credit_card: 1234}
* Nome do Span4: atributos ' spanC ': {env: dev, dev_request: false}


```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "exclude": {
          "matchType": "strict",
          "spanNames": [
            "spanA",
            "spanB"
          ],
          "attributes": [
            {
              "key": "env",
              "value": "dev"
            },
            {
              "key": "test_request"
            }
          ]
        },
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

### <a name="4-selective-processing"></a>4. processamento seletivo

O seguinte demonstra como especificar o conjunto de propriedades de span para indicar a qual extensão esse processador deve ser aplicado. As `include` Propriedades de indicam quais devem ser incluídas e as `exclude` Propriedades filtram ainda mais os intervalos que não devem ser processados.

Com a configuração abaixo, as seguintes extensões correspondem às propriedades e as ações do processador são aplicadas:

* Nome do Span1: atributos ' spanB ': {env: Production, test_request: 123, credit_card: 1234, redact_trace: "false"}
* Nome do Span2: atributos ' spana ': {env: preparo, test_request: false, redact_trace: true}

As seguintes extensões não correspondem às propriedades include e as ações do processador não são aplicadas:
* Nome do Span3: atributos ' spanB ': {env: Production, test_request: true, credit_card: 1234, redact_trace: false}
* Nome do Span4: atributos ' spanC ': {env: dev, test_request: false}

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "spanNames": [
            "spanA",
            "spanB"
          ]
        },
        "exclude": {
          "matchType": "strict",
          "attributes": [
            {
              "key": "redact_trace",
              "value": "false"
            }
          ]
        },
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          },
          {
            "key": "duplicate_key",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```
## <a name="attribute-processor-samples"></a>Exemplos de processador de atributo

### <a name="insert"></a>Inserir

O seguinte insere um novo atributo {"attribute1": "attributeValue1"} para abranger onde a chave "attribute1" não existe.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "attribute1",
            "value": "attributeValue1",
            "action": "insert"
          }
        ]
      }
    ]
  }
}
```

### <a name="insert-from-another-key"></a>Inserir de outra chave

O seguinte usa o valor do atributo "anotherkey" para inserir um novo atributo {"newKey": "valor do atributo" anotherkey "} para se estender onde a chave" newKey "não existe. Se o atributo ' anotherkey ' não existir, nenhum novo atributo será inserido para se estender.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "newKey",
            "fromAttribute": "anotherKey",
            "action": "insert"
          }
        ]
      }
    ]
  }
}
```

### <a name="update"></a>Atualizar

O seguinte atualiza o atributo para {"DB. Secret": "redaçãod"} e atualiza o atributo ' Boo ' usando o valor do atributo ' foo '. As extensões sem o atributo ' Boo ' não serão alteradas.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "db.secret",
            "value": "redacted",
            "action": "update"
          },
          {
            "key": "boo",
            "fromAttribute": "foo",
            "action": "update" 
          }
        ]
      }
    ]
  }
}
```

### <a name="delete"></a>Excluir

O seguinte demonstra como excluir o atributo com a chave ' credit_card '.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

### <a name="hash"></a>Hash

O seguinte demonstra os valores de atributo existentes de hash.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "user.email",
            "action": "hash"
          }
        ]
      }
    ]
  }
}
```

### <a name="extract"></a>Extract

O exemplo a seguir demonstra o uso de Regex para criar novos atributos com base no valor de outro atributo.
Por exemplo, dado http. URL = ' http://example.com/path?queryParam1=value1 , queryParam2 = value2 ', os seguintes atributos serão inseridos:
* httpProtocol: http
* httpDomain: example.com
* httpPath: caminho
* httpQueryParams: queryParam1 = value1, queryParam2 = value2
* o valor de http. URL não é alterado.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "http.url",
            "pattern": "^(?<httpProtocol>.*):\\/\\/(?<httpDomain>.*)\\/(?<httpPath>.*)(\\?|\\&)(?<httpQueryParams>.*)",
            "action": "extract"
          }
        ]
      }
    ]
  }
}
```

O exemplo a seguir demonstra como processar intervalos que têm um nome de extensão que corresponde aos padrões de RegExp.
Esse processador removerá o atributo "token" e ofuscará o atributo "password" em Spans, onde o nome do span corresponde a "auth \* ". e onde o nome do span não corresponde a "login. \* ".

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "include": {
          "matchType": "regexp",
          "spanNames": [
            "auth.*"
          ]
        },
        "exclude": {
          "matchType": "regexp",
          "spanNames": [
            "login.*"
          ]
        },
        "actions": [
          {
            "key": "password",
            "value": "obfuscated",
            "action": "update"
          },
          {
            "key": "token",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```


## <a name="span-processor-samples"></a>Amostras de processador de span

### <a name="name-a-span"></a>Nomear um intervalo

O exemplo a seguir especifica os valores do atributo "DB. svc", "Operation" e "ID" formará o novo nome do span, nessa ordem, separados pelo valor "::".
```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "span",
        "name": {
          "fromAttributes": [
            "db.svc",
            "operation",
            "id"
          ],
          "separator": "::"
        }
      }
    ]
  }
}
```

### <a name="extract-attributes-from-span-name"></a>Extrair atributos do nome do span

Vamos supor que o nome da extensão de entrada seja/API/v1/Document/12345678/Update. Aplicar os seguintes resultados no nome do span de saída/api/v1/document/{documentId}/update adicionará um novo atributo "DocumentID" = "12345678" à extensão.
```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "span",
        "name": {
          "toAttributes": {
            "rules": [
              "^/api/v1/document/(?<documentId>.*)/update$"
            ]
          }
        }
      }
    ]
  }
}
```

### <a name="extract-attributes-from-span-name-with-include-and-exclude"></a>Extrair atributos do nome do span com include e Exclude

O a seguir demonstra como renomear o nome do span como "{operation_website}" e adicionar o atributo {Key: operation_website, value: oldSpanName} quando o span tem as seguintes propriedades:
- O nome de extensão contém '/' em qualquer lugar na cadeia de caracteres.
- O nome do span não é ' não cercamento/Change '.
```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "regexp",
          "spanNames": [
            "^(.*?)/(.*?)$"
          ]
        },
        "exclude": {
          "matchType": "strict",
          "spanNames": [
            "donot/change"
          ]
        },
        "name": {
          "toAttributes": {
            "rules": [
              "(?<operation_website>.*?)$"
            ]
          }
        }
      }
    ]
  }
}
```