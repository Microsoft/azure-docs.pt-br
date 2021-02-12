---
title: Atualizando de 2. x Azure Monitor Application Insights Java
description: Atualizando do Azure Monitor Application Insights Java 2. x
ms.topic: conceptual
ms.date: 11/25/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: d815c919c2b2d63b093c4290a661cbf508c56012
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2020
ms.locfileid: "96601060"
---
# <a name="upgrading-from-application-insights-java-2x-sdk"></a>Atualizando do SDK do Application Insights Java 2. x

Se você já estiver usando Application Insights SDK Java 2. x em seu aplicativo, não será necessário removê-lo.
O agente do Java 3,0 o detectará e irá capturar e correlacionar qualquer telemetria personalizada que você estiver enviando por meio do SDK 2. x, ao mesmo tempo em que suprimirá qualquer coleção automática executada pelo SDK 2. x para evitar a telemetria duplicada.

Se você estiver usando o agente Application Insights 2. x, será necessário remover o `-javaagent:` ARG JVM que estava apontando para o agente 2. x.

O restante deste documento descreve as limitações e as alterações que você pode encontrar ao atualizar de 2. x para 3,0, bem como algumas soluções alternativas que podem ser úteis.

## <a name="telemetryinitializers-and-telemetryprocessors"></a>TelemetryInitializers e TelemetryProcessors

O SDK 2. x TelemetryInitializers e TelemetryProcessors não será executado ao usar o agente 3,0.
Muitos dos casos de uso exigidos anteriormente podem ser resolvidos em 3,0 Configurando [dimensões personalizadas](./java-standalone-config.md#custom-dimensions) ou configurando [processadores de telemetria](./java-standalone-telemetry-processors.md).

## <a name="multiple-applications-in-a-single-jvm"></a>Vários aplicativos em uma única JVM

Atualmente, o 3,0 dá suporte apenas a uma única [cadeia de conexão e nome de função](./java-standalone-config.md#connection-string-and-role-name) por processo em execução. Em particular, você não pode ter vários aplicativos Web Tomcat na mesma implantação do Tomcat usando cadeias de conexão diferentes ou nomes de função diferentes ainda.

## <a name="operation-names"></a>Nomes de operação

Os nomes de operação em 3,0 foram alterados para geralmente fornecer uma exibição agregada melhor no portal de Application Insights U/X.

:::image type="content" source="media/java-ipa/upgrade-from-2x/operation-names-3-0.png" alt-text="Nomes de operação em 3,0":::

No entanto, para alguns aplicativos, você ainda pode preferir a exibição agregada no U/X que foi fornecido pelos nomes de operação anteriores. nesse caso, você pode usar o recurso [processadores de telemetria](./java-standalone-telemetry-processors.md) (versão prévia) no 3,0 para replicar o comportamento anterior.

### <a name="prefix-the-operation-name-with-the-http-method-get-post-etc"></a>Prefixe o nome da operação com o método http ( `GET` , `POST` , etc.)

No SDK 2. x, os nomes de operação foram prefixados pelo método http ( `GET` , `POST` etc.), por exemplo,

:::image type="content" source="media/java-ipa/upgrade-from-2x/operation-names-prefixed-by-http-method.png" alt-text="Nomes de operação prefixados pelo método http":::

O trecho de código a seguir configura três processadores de telemetria que se combinam para replicar o comportamento anterior.
Os processadores de telemetria executam as seguintes ações (em ordem):

1. O primeiro processador de telemetria é um processador de span (tem tipo `span` ), o que significa que ele se aplica a `requests` e `dependencies` .

   Ele corresponderá a qualquer span que tenha um atributo chamado `http.method` e tem um nome de extensão que começa com `/` .

   Em seguida, ele extrairá esse nome de span em um atributo chamado `tempName` .

2. O segundo processador de telemetria também é um processador de span.

   Ele corresponderá a qualquer span que tenha um atributo chamado `tempName` .

   Em seguida, ele atualizará o nome do span concatenando os dois atributos `http.method` e `tempName` separados por um espaço.

3. O último processador de telemetria é um processador de atributo (tem tipo `attribute` ), o que significa que ele se aplica a toda a telemetria que tem atributos (atualmente `requests` , `dependencies` e `traces` ).

   Ele fará a correspondência de qualquer telemetria que tenha um atributo chamado `tempName` .

   Em seguida, ele excluirá o atributo chamado `tempName` , para que ele não seja relatado como uma dimensão personalizada.

```
{
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "regexp",
          "attributes": [
            { "key": "http.method", "value": "" }
          ],
          "spanNames": [ "^/" ]
        },
        "name": {
          "toAttributes": {
            "rules": [ "^(?<tempName>.*)$" ]
          }
        }
      },
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempName" }
          ]
        },
        "name": {
          "fromAttributes": [ "http.method", "tempName" ],
          "separator": " "
        }
      },
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempName" }
          ]
        },
        "actions": [
          { "key": "tempName", "action": "delete" }
        ]
      }
    ]
  }
}
```

### <a name="set-the-operation-name-to-the-full-path"></a>Definir o nome da operação para o caminho completo

Além disso, no SDK 2. x, em alguns casos, os nomes de operação continham o caminho completo, por exemplo,

:::image type="content" source="media/java-ipa/upgrade-from-2x/operation-names-with-full-path.png" alt-text="Nomes de operação com caminho completo":::

O trecho de código a seguir configura quatro processadores de telemetria que se combinam para replicar o comportamento anterior.
Os processadores de telemetria executam as seguintes ações (em ordem):

1. O primeiro processador de telemetria é um processador de span (tem tipo `span` ), o que significa que ele se aplica a `requests` e `dependencies` .

   Ele corresponderá a qualquer span que tenha um atributo chamado `http.url` .

   Em seguida, ele atualizará o nome do span com o `http.url` valor do atributo.

   Isso seria o fim, exceto que `http.url` parece algo parecido `http://host:port/path` , e é provável que você queira apenas a `/path` parte.

2. O segundo processador de telemetria também é um processador de span.

   Ele corresponderá a qualquer span que tenha um atributo chamado `http.url` (em outras palavras, qualquer span que o primeiro processador corresponda).

   Em seguida, ele extrairá a parte do caminho do nome do SPAN em um atributo chamado `tempName` .

3. O terceiro processador de telemetria também é um processador de span.

   Ele corresponderá a qualquer span que tenha um atributo chamado `tempPath` .

   Em seguida, ele atualizará o nome do span do atributo `tempPath` .

4. O último processador de telemetria é um processador de atributo (tem tipo `attribute` ), o que significa que ele se aplica a toda a telemetria que tem atributos (atualmente `requests` , `dependencies` e `traces` ).

   Ele fará a correspondência de qualquer telemetria que tenha um atributo chamado `tempPath` .

   Em seguida, ele excluirá o atributo chamado `tempPath` , para que ele não seja relatado como uma dimensão personalizada.

```
{
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "http.url" }
          ]
        },
        "name": {
          "fromAttributes": [ "http.url" ]
        }
      },
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "http.url" }
          ]
        },
        "name": {
          "toAttributes": {
            "rules": [ "https?://[^/]+(?<tempPath>/[^?]*)" ]
          }
        }
      },
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempPath" }
          ]
        },
        "name": {
          "fromAttributes": [ "tempPath" ]
        }
      },
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempPath" }
          ]
        },
        "actions": [
          { "key": "tempPath", "action": "delete" }
        ]
      }
    ]
  }
}
```

## <a name="dependency-names"></a>Nomes de dependência

Os nomes de dependência em 3,0 também foram alterados, para geralmente fornecer uma exibição agregada melhor no portal de Application Insights U/X.

Novamente, para alguns aplicativos, você ainda pode preferir a exibição agregada no U/X que foi fornecido pelos nomes de dependência anteriores. nesse caso, você pode usar técnicas semelhantes como acima para replicar o comportamento anterior.

## <a name="operation-name-on-dependencies"></a>Nome da operação em dependências

Anteriormente no SDK 2. x, o nome da operação da telemetria de solicitação também foi definido na telemetria de dependência.
Application Insights Java 3,0 não popula mais o nome da operação na telemetria de dependência.
Se você quiser ver o nome da operação para a solicitação que é o pai da telemetria de dependência, você pode gravar uma consulta de logs (Kusto) para unir da tabela de dependência à tabela de solicitação.
