---
title: Processadores de telemetria (visualização)-Azure Monitor Application Insights para Java
description: Como configurar processadores de telemetria no Azure Monitor Application Insights para Java
ms.topic: conceptual
ms.date: 10/29/2020
author: kryalama
ms.custom: devx-track-java
ms.author: kryalama
ms.openlocfilehash: c0745dd4069c64292fbcaef666d843ae2d25f7b3
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632573"
---
# <a name="telemetry-processors-preview---azure-monitor-application-insights-for-java"></a>Processadores de telemetria (visualização)-Azure Monitor Application Insights para Java

> [!NOTE]
> Este recurso ainda está em versão prévia.

O agente do Java 3,0 para Application Insights agora tem os recursos para processar dados de telemetria antes de os dados serem exportados.

A seguir estão alguns casos de uso dos processadores de telemetria:
 * Mascarar dados confidenciais
 * Adicionar dimensões personalizadas condicionalmente
 * Atualize o nome que é usado para agregação e exibição no portal do Azure
 * Descartar atributos de span para controlar o custo de ingestão

## <a name="terminology"></a>Terminologia

Antes de passarmos para processadores de telemetria, é importante entender o que o termo span refere.

Uma extensão é um termo geral para qualquer uma destas três coisas:

* Uma solicitação de entrada
* Uma dependência de saída (por exemplo, uma chamada remota para outro serviço)
* Uma dependência em processo (por exemplo, trabalho sendo feito por subcomponentes do serviço)

Para a finalidade dos processadores de telemetria, os componentes importantes de um Span são:

* Name
* Atributos

O nome do span é a exibição primária usada para solicitações e dependências no portal do Azure.

Os atributos de span representam as propriedades padrão e personalizadas de uma determinada solicitação ou dependência.

## <a name="telemetry-processor-types"></a>Tipos de processador de telemetria

Atualmente, há dois tipos de processadores de telemetria.

#### <a name="attribute-processor"></a>Processador de atributos

Um processador de atributos tem a capacidade de inserir, atualizar, excluir ou hash de atributos.
Ele também pode extrair (por meio de uma expressão regular) um ou mais atributos novos de um atributo existente.

#### <a name="span-processor"></a>Processador span

Um processador de span tem a capacidade de atualizar o nome da telemetria.
Ele também pode extrair (por meio de uma expressão regular) um ou mais atributos novos do nome do span.

> [!NOTE]
> Observe que os processadores de telemetria atualmente processam apenas atributos do tipo cadeia de caracteres e não processam atributos do tipo booliano ou número.

## <a name="getting-started"></a>Introdução

Crie um arquivo de configuração chamado `applicationinsights.json` e coloque-o no mesmo diretório do `applicationinsights-agent-*.jar` , com o modelo a seguir.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        ...
      },
      {
        "type": "attribute",
        ...
      },
      {
        "type": "span",
        ...
      }
    ]
  }
}
```

## <a name="includeexclude-criteria"></a>Incluir/excluir critérios

Os processadores de atributo e os processadores de span dão suporte a `include` critérios e opcionais `exclude` .
Um processador só será aplicado a esses spans que correspondam aos seus `include` critérios (se fornecido) _e_ não correspondam aos seus `exclude` critérios (se fornecido).

Para configurar essa opção, em `include` e/ou `exclude` pelo menos um `matchType` e um `spanNames` ou `attributes` é necessário.
A configuração de inclusão/exclusão tem suporte para ter mais de uma condição especificada.
Todas as condições especificadas devem ser avaliadas como true para que uma correspondência ocorra. 

**Campo obrigatório**: 
* `matchType` controla como os itens `spanNames` nas `attributes` matrizes e são interpretados. Os possíveis valores são `regexp` ou `strict`. 

**Campos opcionais**: 
* `spanNames` deve corresponder a pelo menos um dos itens. 
* `attributes` Especifica a lista de atributos a serem correspondidos. Todos esses atributos devem corresponder exatamente para que ocorra uma correspondência.

> [!NOTE]
> Se ambos `include` e `exclude` forem especificados, as `include` Propriedades serão verificadas antes das `exclude` Propriedades.

#### <a name="sample-usage"></a>Exemplo de uso

```json

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
```
Para obter mais noções básicas, confira a documentação [exemplos do processador de telemetria](./java-standalone-telemetry-processors-examples.md) .

## <a name="attribute-processor"></a>Processador de atributos

O processador de atributos modifica os atributos de um Span. Opcionalmente, ele dá suporte à capacidade de incluir/excluir spans. Ele usa uma lista de ações que são executadas na ordem especificada no arquivo de configuração. As ações com suporte são:

### `insert`

Insere um novo atributo em spans onde a chave ainda não existe.   

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "value": "value1",
        "action": "insert"
      }
    ]
  }
]
```
Para a `insert` ação, os itens a seguir são necessários
  * `key`
  * um `value` ou `fromAttribute`
  * `action`:`insert`

### `update`

Atualiza um atributo em spans onde a chave existe

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "value": "newValue",
        "action": "update"
      }
    ]
  }
]
```
Para a `update` ação, os itens a seguir são necessários
  * `key`
  * um `value` ou `fromAttribute`
  * `action`:`update`


### `delete` 

Exclui um atributo de um span

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "action": "delete"
      }
    ]
  }
]
```
Para a `delete` ação, os itens a seguir são necessários
  * `key`
  * `action`: `delete`

### `hash`

Hashes (SHA1) um valor de atributo existente

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "action": "hash"
      }
    ]
  }
]
```
Para a `hash` ação, os itens a seguir são necessários
* `key`
* `action` : `hash`

### `extract`

> [!NOTE]
> Esse recurso só está no 3.0.2 e posterior

Extrai valores usando uma regra de expressão regular da chave de entrada para chaves de destino especificadas na regra. Se uma chave de destino já existir, ela será substituída. Ele se comporta de forma semelhante à configuração do [processador span](#extract-attributes-from-span-name) `toAttributes` com o atributo existente como a origem.

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "pattern": "<regular pattern with named matchers>",
        "action": "extract"
      }
    ]
  }
]
```
Para a `extract` ação, os itens a seguir são necessários
* `key`
* `pattern`
* `action` : `extract`

Para obter mais noções básicas, confira a documentação [exemplos do processador de telemetria](./java-standalone-telemetry-processors-examples.md) .

## <a name="span-processor"></a>Processador span

O processador span modifica o nome do SPAN ou os atributos de um Span com base no nome do span. Opcionalmente, ele dá suporte à capacidade de incluir/excluir spans.

### <a name="name-a-span"></a>Nomear um intervalo

A configuração a seguir é necessária como parte da seção de nome:

* `fromAttributes`: O valor do atributo para as chaves é usado para criar um novo nome na ordem especificada na configuração. Todas as chaves de atributo precisam ser especificadas no intervalo para o processador renomeá-lo.

A configuração a seguir pode ser configurada opcionalmente:

* `separator`: Uma cadeia de caracteres especificada será usada para dividir valores
> [!NOTE]
> Se a renomeação for dependente dos atributos que estão sendo modificados pelo processador de atributos, verifique se o processador de span está especificado após o processador de atributos na especificação do pipeline.

```json
"processors": [
  {
    "type": "span",
    "name": {
      "fromAttributes": [
        "attributeKey1",
        "attributeKey2",
      ],
      "separator": "::"
    }
  }
] 
```

### <a name="extract-attributes-from-span-name"></a>Extrair atributos do nome do span

Usa uma lista de expressões regulares para corresponder ao nome do span e extrair atributos dele com base em subexpressões. Deve ser especificado na `toAttributes` seção.

As seguintes configurações são necessárias:

`rules` : Uma lista de regras para extrair valores de atributo do nome de extensão. Os valores no nome do span são substituídos por nomes de atributo extraídos. Cada regra na lista é a cadeia de caracteres de padrão Regex. O nome do span é verificado em relação ao Regex. Se o Regex corresponder, todas as subexpressões nomeadas do Regex serão extraídas como atributos e adicionadas à extensão. Cada nome de subexpressão se torna um nome de atributo e a subexpressão correspondente de parte se torna o valor do atributo. A parte correspondente no nome do span é substituída pelo nome do atributo extraído. Se os atributos já existirem no span, eles serão substituídos. O processo é repetido para todas as regras na ordem em que são especificadas. Cada regra subsequente funciona no nome do span que é a saída após o processamento da regra anterior.

```json

"processors": [
  {
    "type": "span",
    "name": {
      "toAttributes": {
        "rules": [
          "rule1",
          "rule2",
          "rule3"
        ]
      }
    }
  }
]

```

## <a name="list-of-attributes"></a>Lista de atributos

A seguir, há uma lista de alguns atributos de span comuns que podem ser usados nos processadores de telemetria.

### <a name="http-spans"></a>Spans HTTP

| Atributo  | Type | Descrição | 
|---|---|---|
| `http.method` | string | Método de solicitação HTTP.|
| `http.url` | string | URL de solicitação HTTP completa no formulário `scheme://host[:port]/path?query[#fragment]` . Normalmente, o fragmento não é transmitido por HTTP, mas, se for conhecido, ele deve ser incluído no entanto.|
| `http.status_code` | número | [Código de status de resposta http](https://tools.ietf.org/html/rfc7231#section-6).|
| `http.flavor` | string | Tipo de protocolo HTTP usado |
| `http.user_agent` | string | Valor do cabeçalho de [agente de usuário http](https://tools.ietf.org/html/rfc7231#section-5.5.3) enviado pelo cliente. |

### <a name="jdbc-spans"></a>Spans JDBC

| Atributo  | Type | Descrição  |
|---|---|---|
| `db.system` | string | Um identificador para o produto do sistema de gerenciamento de banco de dados (DBMS) que está sendo usado. |
| `db.connection_string` | string | A cadeia de conexão usada para se conectar ao banco de dados. É recomendável remover as credenciais inseridas.|
| `db.user` | string | Nome de usuário para acessar o banco de dados. |
| `db.name` | string | Esse atributo é usado para relatar o nome do banco de dados que está sendo acessado. Para comandos que alternam o banco de dados, isso deve ser definido como o banco de dados de destino (mesmo que o comando falhe).|
| `db.statement` | string | A instrução do banco de dados que está sendo executada.|
