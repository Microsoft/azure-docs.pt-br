---
title: Exemplos de processador de telemetria – Application Insights do Azure Monitor para Java
description: Explore exemplos que mostram os processadores de telemetria no Azure Monitor Application Insights para Java.
ms.topic: conceptual
ms.date: 12/29/2020
author: kryalama
ms.custom: devx-track-java
ms.author: kryalama
ms.openlocfilehash: 0978bd669855d264ed6dfa5eeddc45ad499aa2a5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101734580"
---
# <a name="telemetry-processor-examples---azure-monitor-application-insights-for-java"></a>Exemplos de processador de telemetria – Application Insights do Azure Monitor para Java

Este artigo fornece exemplos de processadores de telemetria no Application Insights para Java. Você encontrará exemplos para incluir e excluir configurações. Você também encontrará exemplos de processadores de atributos e de span.
## <a name="include-and-exclude-samples"></a>Incluir e excluir amostras

Nesta seção, você verá como incluir e excluir intervalos. Você também verá como excluir vários spans e aplicar o processamento seletivo.
### <a name="include-spans"></a>Incluir spans

Esta seção mostra como incluir spans para um processador de atributo. Os spans que não correspondem às propriedades não são processados pelo processador.

Uma correspondência requer que o nome de extensão seja igual a `spanA` ou `spanB` . 

Essas extensões correspondem às propriedades include e as ações do processador são aplicadas:
* Nome do Span1: atributos ' spana ': {env: dev, test_request: 123, credit_card: 1234}
* Nome do Span2: atributos ' spanB ': {env: dev, test_request: false}
* Nome do Span3: atributos ' spana ': {env: 1, test_request: dev, credit_card: 1234}

Esse span não corresponde às propriedades include e as ações do processador não são aplicadas:
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

### <a name="exclude-spans"></a>Excluir spans

Esta seção demonstra como excluir spans para um processador de atributo. As extensões que correspondem às propriedades não são processadas por esse processador.

Uma correspondência requer que o nome de extensão seja igual a `spanA` ou `spanB` .

Os seguintes intervalos correspondem às propriedades de exclusão e as ações do processador não são aplicadas:
* Nome do Span1: atributos ' spana ': {env: dev, test_request: 123, credit_card: 1234}
* Nome do Span2: atributos ' spanB ': {env: dev, test_request: false}
* Nome do Span3: atributos ' spana ': {env: 1, test_request: dev, credit_card: 1234}

Esse intervalo não corresponde às propriedades de exclusão e as ações do processador são aplicadas:
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

### <a name="exclude-spans-by-using-multiple-criteria"></a>Excluir intervalos usando vários critérios

Esta seção demonstra como excluir spans para um processador de atributo. As extensões que correspondem às propriedades não são processadas por esse processador.

Uma correspondência requer que as seguintes condições sejam atendidas:
* Um atributo (por exemplo, `env` ou `dev` ) deve existir no span.
* O span deve ter um atributo que tenha a chave `test_request` .

Os intervalos a seguir correspondem às propriedades de exclusão e as ações do processador não são aplicadas.
* Nome do Span1: atributos ' spanB ': {env: dev, test_request: 123, credit_card: 1234}
* Nome do Span2: atributos ' spana ': {env: dev, test_request: false}

O trecho a seguir não corresponde às propriedades Exclude e as ações do processador são aplicadas:
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

### <a name="selective-processing"></a>Processamento seletivo

Esta seção mostra como especificar o conjunto de propriedades de span que indicam a quais extensões esse processador deve ser aplicado. As propriedades include indicam quais extensões devem ser processadas. As propriedades de exclusão filtram os intervalos que não devem ser processados.

Na configuração a seguir, essas extensões correspondem às propriedades e as ações do processador são aplicadas:

* Nome do Span1: atributos ' spanB ': {env: Production, test_request: 123, credit_card: 1234, redact_trace: "false"}
* Nome do Span2: atributos ' spana ': {env: preparo, test_request: false, redact_trace: true}

Essas extensões não correspondem às propriedades include e as ações do processador não são aplicadas:
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

O exemplo a seguir insere o novo atributo `{"attribute1": "attributeValue1"}` em spans onde a chave `attribute1` não existe.

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

O exemplo a seguir usa o atributo valor do `anotherkey` para inserir o novo atributo `{"newKey": "<value from attribute anotherkey>"}` em spans onde a chave `newKey` não existe. Se o atributo `anotherkey` não existir, nenhum atributo novo será inserido em spans.

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

O exemplo a seguir atualiza o atributo para `{"db.secret": "redacted"}` . Ele atualiza o atributo `boo` usando o valor do atributo `foo` . Os spans que não têm o atributo `boo` não são alterados.

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

O exemplo a seguir mostra como excluir um atributo que tem a chave `credit_card` .

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

O exemplo a seguir mostra como fazer hash de valores de atributo existentes.

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

### <a name="extract"></a>Extração

O exemplo a seguir mostra como usar uma expressão regular (Regex) para criar novos atributos com base no valor de outro atributo.
Por exemplo, dado `http.url = http://example.com/path?queryParam1=value1,queryParam2=value2` , os seguintes atributos são inseridos:
* httpProtocol: `http`
* httpDomain: `example.com`
* httpPath: `path`
* httpQueryParams: `queryParam1=value1,queryParam2=value2`
* http. URL: *nenhuma* alteração

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

O exemplo a seguir mostra como processar intervalos que têm um nome de extensão que corresponde aos padrões de Regex.
Esse processador remove o `token` atributo. Ele ofusca o `password` atributo em spans onde o nome do span corresponde `auth.*` e onde o nome do span não corresponde `login.*` .

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

O exemplo a seguir especifica os valores de atributos `db.svc` , `operation` e `id` . Ele forma o novo nome do span usando esses atributos, nessa ordem, separados pelo valor `::` .
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

### <a name="extract-attributes-from-a-span-name"></a>Extrair atributos de um nome de extensão

Vamos supor que o nome da extensão de entrada seja `/api/v1/document/12345678/update` . O exemplo a seguir resulta no nome da extensão de saída `/api/v1/document/{documentId}/update` . Ele adiciona o novo atributo `documentId=12345678` à extensão.
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

### <a name="extract-attributes-from-a-span-name-by-using-include-and-exclude"></a>Extrair atributos de um nome de extensão usando include e Exclude

O exemplo a seguir mostra como alterar o nome do span para `{operation_website}` . Ele adiciona um atributo com chave `operation_website` e valor `{oldSpanName}` quando a extensão tem as seguintes propriedades:
- O nome do span contém `/` qualquer lugar na cadeia de caracteres.
- O nome do span não é `donot/change` .
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
