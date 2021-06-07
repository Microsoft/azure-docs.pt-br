---
title: Processadores de telemetria (versão prévia) – Application Insights do Azure Monitor para Java
description: Saiba como configurar os processadores de telemetria no Azure Monitor Application Insights para Java.
ms.topic: conceptual
ms.date: 10/29/2020
author: kryalama
ms.custom: devx-track-java
ms.author: kryalama
ms.openlocfilehash: 991e52c13a5730b83552abb6b922d4d7a57c5429
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105024108"
---
# <a name="telemetry-processors-preview---azure-monitor-application-insights-for-java"></a>Processadores de telemetria (versão prévia) – Application Insights do Azure Monitor para Java

> [!NOTE]
> O recurso de processadores de telemetria está em versão prévia.

O agente do Java 3,0 para Application Insights pode processar dados de telemetria antes de os dados serem exportados.

Aqui estão alguns casos de uso para processadores de telemetria:
 * Mascarar dados confidenciais.
 * Adicionar dimensões personalizadas condicionalmente.
 * Atualize o nome do span, que é usado para agregar telemetria semelhante no portal do Azure.
 * Descartar atributos de span específicos para controlar os custos de ingestão.

> [!NOTE]
> Se você pretende descartar spans (inteiros) específicos para controlar o custo de ingestão, consulte [substituições de amostragem](./java-standalone-sampling-overrides.md).

## <a name="terminology"></a>Terminologia

Antes de aprender sobre os processadores de telemetria, você deve entender o termo *span*. Uma extensão é um termo geral para:

* Uma solicitação de entrada.
* Uma dependência de saída (por exemplo, uma chamada remota para outro serviço).
* Uma dependência em processo (por exemplo, trabalho sendo feito por subcomponentes do serviço).

Para processadores de telemetria, esses componentes de span são importantes:

* Name
* Atributos

O nome do span é a exibição primária para solicitações e dependências no portal do Azure. Atributos de span representam propriedades padrão e personalizadas de uma determinada solicitação ou dependência.

## <a name="telemetry-processor-types"></a>Tipos de processador de telemetria

Atualmente, os dois tipos de processadores de telemetria são processadores de atributo e processadores de span.

Um processador de atributos pode inserir, atualizar, excluir ou hash de atributos.
Ele também pode usar uma expressão regular para extrair um ou mais atributos novos de um atributo existente.

Um processador de span pode atualizar o nome da telemetria.
Ele também pode usar uma expressão regular para extrair um ou mais atributos novos do nome do span.

> [!NOTE]
> Atualmente, os processadores de telemetria processam apenas atributos do tipo cadeia de caracteres. Eles não processam atributos do tipo booliano ou número.

## <a name="getting-started"></a>Introdução

Para começar, crie um arquivo de configuração chamado *applicationinsights.jsem*. Salve-o no mesmo diretório que *applicationinsights-Agent- \* . jar*. Use o modelo a seguir.

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

## <a name="include-criteria-and-exclude-criteria"></a>Incluir critérios e critérios de exclusão

Os processadores de atributo e os processadores de span dão suporte a `include` critérios e opcionais `exclude` .
Um processador é aplicado somente a spans que correspondem aos seus `include` critérios (se for fornecido) _e_ não correspondem aos seus `exclude` critérios (se for fornecido).

Para configurar essa opção, em `include` ou `exclude` (ou em ambos), especifique pelo menos um `matchType` e um `spanNames` ou `attributes` .
A configuração include-Exclude permite mais de uma condição especificada.
Todas as condições especificadas devem ser avaliadas como true para resultar em uma correspondência. 

* **Campo obrigatório**: `matchType` controla como os itens em `spanNames` matrizes e `attributes` matrizes são interpretados. Os valores possíveis são `regexp` e `strict`. 

* **Campos opcionais**: 
    * `spanNames` deve corresponder a pelo menos um dos itens. 
    * `attributes` Especifica a lista de atributos a serem correspondentes. Todos esses atributos devem corresponder exatamente para resultar em uma correspondência.
    
> [!NOTE]
> Se ambos `include` e `exclude` forem especificados, as `include` Propriedades serão verificadas antes da `exclude` verificação das propriedades.

### <a name="sample-usage"></a>Amostra de uso

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
Para obter mais informações, consulte [exemplos do processador de telemetria](./java-standalone-telemetry-processors-examples.md).

## <a name="attribute-processor"></a>Processador de atributos

O processador de atributo modifica os atributos de um Span. Ele pode dar suporte à capacidade de incluir ou excluir spans. Ele usa uma lista de ações que são executadas na ordem em que o arquivo de configuração especifica. O processador dá suporte a estas ações:

- `insert`
- `update`
- `delete`
- `hash`
- `extract`
### `insert`

A `insert` ação insere um novo atributo em spans onde a chave ainda não existe.   

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
A `insert` ação requer as seguintes configurações:
* `key`
* Um `value` ou `fromAttribute`
* `action`: `insert`

### `update`

A `update` ação atualiza um atributo em spans onde a chave já existe.

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
A `update` ação requer as seguintes configurações:
* `key`
* Um `value` ou `fromAttribute`
* `action`: `update`


### `delete` 

A `delete` ação exclui um atributo de um Span.

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
A `delete` ação requer as seguintes configurações:
* `key`
* `action`: `delete`

### `hash`

A `hash` ação hashes (SHA1) de um valor de atributo existente.

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
A `hash` ação requer as seguintes configurações:
* `key`
* `action`: `hash`

### `extract`

> [!NOTE]
> O `extract` recurso está disponível somente na versão 3.0.2 e posterior.

A `extract` ação extrai valores usando uma regra de expressão regular da chave de entrada para chaves de destino que a regra especifica. Se uma chave de destino já existir, ela será substituída. Essa ação se comporta como a configuração do [processador span](#extract-attributes-from-the-span-name) `toAttributes` , em que o atributo existente é a origem.

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
A `extract` ação requer as seguintes configurações:
* `key`
* `pattern`
* `action`: `extract`

Para obter mais informações, consulte [exemplos do processador de telemetria](./java-standalone-telemetry-processors-examples.md).

## <a name="span-processor"></a>Processador span

O processador span modifica o nome do SPAN ou os atributos de um Span com base no nome do span. Ele pode dar suporte à capacidade de incluir ou excluir spans.

### <a name="name-a-span"></a>Nomear um intervalo

A `name` seção requer a `fromAttributes` configuração. Os valores desses atributos são usados para criar um novo nome, concatenado na ordem em que a configuração especifica. O processador irá alterar o nome do span somente se todos esses atributos estiverem presentes no span.

A `separator` configuração é opcional. Essa configuração é uma cadeia de caracteres. Ele é especificado para dividir valores.
> [!NOTE]
> Se a renomeação depender do processador de atributos para modificar os atributos, verifique se o processador de span está especificado após o processador de atributos na especificação do pipeline.

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

### <a name="extract-attributes-from-the-span-name"></a>Extrair atributos do nome do span

A `toAttributes` seção lista as expressões regulares para correspondência com o nome do span. Ele extrai atributos com base em subexpressões.

A `rules` configuração é necessária. Essa configuração lista as regras que são usadas para extrair valores de atributo do nome de extensão. 

Os valores no nome do span são substituídos por nomes de atributo extraídos. Cada regra na lista é uma cadeia de caracteres de padrão de expressão regular (Regex). 

Veja como os valores são substituídos por nomes de atributo extraídos:

1. O nome do span é verificado em relação ao Regex. 
1. Se o Regex corresponder, todas as subexpressões nomeadas do Regex serão extraídas como atributos. 
1. Os atributos extraídos são adicionados ao SPAN. 
1. Cada nome de subexpressão se torna um nome de atributo. 
1. A parte correspondente da subexpressão se torna o valor do atributo. 
1. A parte correspondente no nome do span é substituída pelo nome do atributo extraído. Se os atributos já existirem no span, eles serão substituídos. 
 
Esse processo é repetido para todas as regras na ordem em que são especificadas. Cada regra subsequente funciona no nome do span que é a saída da regra anterior.

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

## <a name="common-span-attributes"></a>Atributos de span comuns

Esta seção lista alguns atributos de span comuns que os processadores de telemetria podem usar.

### <a name="http-spans"></a>Spans HTTP

| Atributo  | Type | Descrição | 
|---|---|---|
| `http.method` | string | Método de solicitação HTTP.|
| `http.url` | string | URL de solicitação HTTP completa no formulário `scheme://host[:port]/path?query[#fragment]` . O fragmento geralmente não é transmitido por HTTP. Mas se o fragmento for conhecido, ele deverá ser incluído.|
| `http.status_code` | número | [Código de status de resposta http](https://tools.ietf.org/html/rfc7231#section-6).|
| `http.flavor` | string | Tipo de protocolo HTTP. |
| `http.user_agent` | string | Valor do cabeçalho de [agente de usuário http](https://tools.ietf.org/html/rfc7231#section-5.5.3) enviado pelo cliente. |

### <a name="jdbc-spans"></a>Spans JDBC

| Atributo  | Type | Descrição  |
|---|---|---|
| `db.system` | string | Identificador do produto do sistema de gerenciamento de banco de dados (DBMS) que está sendo usado. |
| `db.connection_string` | string | A cadeia de conexão usada para se conectar ao banco de dados. É recomendável remover as credenciais inseridas.|
| `db.user` | string | Nome de usuário para acessar o banco de dados. |
| `db.name` | string | Cadeia de caracteres usada para relatar o nome do banco de dados que está sendo acessado. Para comandos que alternam o banco de dados, essa cadeia de caracteres deve ser definida para o banco de dados de destino, mesmo que o comando falhe.|
| `db.statement` | string | A instrução Database que está sendo executada.|
