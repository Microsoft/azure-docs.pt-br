---
title: "Manipulação de tipos de conteúdo - Aplicativos lógicos do Azure | Microsoft Docs"
description: "Como os aplicativos lógicos do Azure lidam com tipos de conteúdo em tempo de execução e de design"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: cd1f08fd-8cde-4afc-86ff-2e5738cc8288
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: ac67838344bbd10384299c086ff096fbe5dec6a9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="handle-content-types-in-logic-apps"></a>Gerenciamento de tipos de conteúdo em aplicativos lógicos

JSON, XML, arquivos simples e dados binários são alguns dos diversos tipos de conteúdo que podem fluir em um aplicativo lógico. O mecanismo de aplicativos lógicos oferece suporte a todos os tipos de conteúdo e também é capaz de compreender nativamente o mecanismo de aplicativos lógicos. Outros podem exigir transmissões ou conversões conforme necessário. Este artigo descreve como o mecanismo gerencia diferentes tipos de conteúdo e como lidar corretamente com esses tipos quando necessário.

## <a name="content-type-header"></a>Cabeçalho Content-Type

Para iniciar, vamos examinar os dois `Content-Types` que não requerem transmissão nem conversão que podem ser usados em um aplicativo lógico: `application/json` e `text/plain`.

## <a name="applicationjson"></a>Aplicativo/JSON

O mecanismo de fluxo de trabalho depende do cabeçalho `Content-Type` de chamadas HTTP para determinar o tratamento apropriado. Qualquer solicitação com o tipo de conteúdo `application/json` será armazenada e tratada como um objeto JSON. Além disso, o conteúdo JSON pode ser analisado por padrão sem a necessidade de conversão. 

Por exemplo, para analisar uma solicitação com o cabeçalho de tipo de conteúdo `application/json ` em um fluxo de trabalho, use uma expressão como `@body('myAction')['foo'][0]` para obter o valor `bar`, nesse caso:

```
{
    "data": "a",
    "foo": [
        "bar"
    ]
}
```

Nenhuma conversão adicional é necessária. Se estiver trabalhando com dados JSON, mas não tiver um cabeçalho especificado, você poderá convertê-lo manualmente para JSON usando a função `@json()`, por exemplo: `@json(triggerBody())['foo']`.

### <a name="schema-and-schema-generator"></a>Esquema e gerador de esquema

O gatilho da solicitação permite que você insira um esquema JSON para o conteúdo que espera receber. Esse esquema permite que o designer gere tokens para que você possa consumir o conteúdo da solicitação. Se não tiver um esquema pronto, escolha **Usar o conteúdo de exemplo para gerar esquema** para poder gerar um esquema JSON de um conteúdo de exemplo.

![Esquema](./media/logic-apps-http-endpoint/manualtrigger.png)

### <a name="parse-json-action"></a>Ação "Parse JSON"

A ação `Parse JSON` permite que você analise o conteúdo JSON em tokens amigáveis para o consumo do aplicativo lógico. Assim como com o gatilho da solicitação, essa ação permite que você insira ou gere um esquema JSON para o conteúdo que você deseja analisar. Essa ferramenta facilita grande parte do consumo de dados do Barramento de Serviço, do Azure Cosmos DB e assim por diante.

![Analisar o JSON](./media/logic-apps-content-type/ParseJSON.png)

## <a name="textplain"></a>Text/plain

Assim como `application/json`, as mensagens HTTP recebidas com o cabeçalho `Content-Type` de `text/plain` são armazenadas em sua forma bruta. Além disso, se essas mensagens são incluídas em ações subsequentes sem conversão, essas solicitações saem com o cabeçalho `Content-Type`: `text/plain`. Por exemplo, ao trabalhar com um arquivo simples, você pode obter esse conteúdo HTTP como `text/plain`:

```
Date,Name,Address
Oct-1,Frank,123 Ave.
```

Se na próxima ação, você a enviar como o corpo de outra solicitação (`@body('flatfile')`), a solicitação terá um cabeçalho Content-Type `text/plain`. Se estiver trabalhando com dados de texto sem formatação, mas não tiver um cabeçalho especificado, você poderá converter os dados manualmente para texto usando a função `@string()`, por exemplo: `@string(triggerBody())`.

## <a name="applicationxml-and-applicationoctet-stream-and-converter-functions"></a>Application/xml e Application/octet-stream e funções de conversor

O mecanismo de aplicativos lógicos sempre mantém o `Content-Type` recebido na resposta ou solicitação HTTP. Portanto, se o mecanismo recebe o conteúdo com o `Content-Type` de `application/octet-stream`, e você inclui o conteúdo em uma ação subsequente sem conversão, a solicitação de saída tem `Content-Type`: `application/octet-stream`. Dessa forma, o mecanismo garante que dados não sejam perdidos ao movê-los pelo fluxo de trabalho. No entanto, o estado de ação (entradas e saídas) é armazenado em um objeto JSON conforme o estado se move no fluxo de trabalho. Ou seja, para preservar alguns tipos de dados, o mecanismo converte o conteúdo em uma cadeia de caracteres codificada em base64 binária com os metadados apropriados que preservam `$content` e `$content-type`, que são convertidos automaticamente. 

* `@json()` - converte dados em `application/json`
* `@xml()` - converte dados em `application/xml`
* `@binary()` - converte dados em `application/octet-stream`
* `@string()` - converte dados em `text/plain`
* `@base64()` - converte conteúdo em uma cadeia de caracteres de base64
* `@base64toString()` - converte uma cadeia de caracteres codificada em base64 em `text/plain`
* `@base64toBinary()` - converte uma cadeia de caracteres codificada em base64 em `application/octet-stream`
* `@encodeDataUri()` - codifica uma cadeia de caracteres como uma matriz de bytes dataUri
* `@decodeDataUri()` - decodifica uma dataUri em uma matriz de bytes

Por exemplo, caso tenha recebido uma solicitação HTTP com `Content-Type`: `application/xml`:

```
<?xml version="1.0" encoding="UTF-8" ?>
<CustomerName>Frank</CustomerName>
```

Você pode converter e usar mais tarde com algo como `@xml(triggerBody())`, ou em uma função como `@xpath(xml(triggerBody()), '/CustomerName')`.

## <a name="other-content-types"></a>Outros tipos de conteúdo

Outros tipos de conteúdo têm suporte e funcionam com aplicativos lógicos, mas podem exigir a recuperação manual do corpo da mensagem por meio da decodificação do `$content`. Por exemplo, suponha que você dispare uma solicitação `application/x-www-url-formencoded` onde `$content` é o conteúdo codificado como uma cadeia de caracteres base64 para preservar todos os dados:

```
CustomerName=Frank&Address=123+Avenue
```

Como a solicitação não é texto sem formatação, nem JSON, ela é armazenada na ação da seguinte maneira:

```
...
"body": {
    "$content-type": "application/x-www-url-formencoded",
    "$content": "AAB1241BACDFA=="
}
```

Quando não houver uma função nativa para dados de formulário, você ainda pode usar esses dados em um fluxo de trabalho ao acessar manualmente os dados com uma função como `@string(body('formdataAction'))`. Se desejar que a solicitação de saída também tenha o cabeçalho de tipo de conteúdo `application/x-www-url-formencoded` , você pode adicionar a solicitação ao corpo de ação sem conversões, como `@body('formdataAction')`. No entanto, esse método só funciona se o corpo é o único parâmetro na entrada `body`. Se você tentar usar `@body('formdataAction')` em uma solicitação `application/json`, o corpo codificado será enviado causando erro de tempo de execução.

