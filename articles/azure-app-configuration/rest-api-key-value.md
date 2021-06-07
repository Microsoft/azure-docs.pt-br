---
title: API REST de configuração de Azure App-chave-valor
description: Páginas de referência para trabalhar com valores de chave usando a API REST de configuração de Azure App
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 95081d6f8c2770d01f7836e08b6851860bf47ba8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96932550"
---
# <a name="key-values"></a>Valores de chave

Um valor-chave é um recurso identificado por uma combinação exclusiva de `key`  +  `label` . `label` é opcional. Para referenciar explicitamente um valor de chave sem um rótulo, use "\ 0" (URL codificado como ``%00`` ). Veja os detalhes de cada operação.

Este artigo se aplica à versão de API 1,0.

## <a name="operations"></a>Operations

- Obter
- Listar vários
- Definir
- Excluir

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="syntax"></a>Sintaxe

```json
{
  "etag": [string],
  "key": [string],
  "label": [string, optional],
  "content_type": [string, optional],
  "value": [string],
  "last_modified": [datetime ISO 8601],
  "locked": [boolean],
  "tags": [object with string properties, optional]
}
```

## <a name="get-key-value"></a>Obter chave-valor

Necessário: ``{key}`` , ``{api-version}``  
Opcional: ``label`` (se omitido, ele implica um valor de chave sem um rótulo.)

```http
GET /kv/{key}?label={label}&api-version={api-version}
```

**Response**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8;
Last-Modified: Tue, 05 Dec 2017 02:41:26 GMT
ETag: "4f6dd610dd5e4deebc7fbaef685fb903"
```

```json
{
  "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
  "key": "{key}",
  "label": "{label}",
  "content_type": null,
  "value": "example value",
  "last_modified": "2017-12-05T02:41:26+00:00",
  "locked": "false",
  "tags": {
    "t1": "value1",
    "t2": "value2"
  }
}
```

Se a chave não existir, a seguinte resposta será retornada:

```http
HTTP/1.1 404 Not Found
```

## <a name="get-conditionally"></a>Get (condicionalmente)

Para melhorar o cache de cliente, use `If-Match` ou `If-None-Match` cabeçalhos de solicitação. O `etag` argumento faz parte da representação de chave. Para obter mais informações, consulte as [seções 14,24 e 14,26](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).

A solicitação a seguir recuperará o valor de chave somente se a representação atual não corresponder ao especificado `etag` :

```http
GET /kv/{key}?api-version={api-version} HTTP/1.1
Accept: application/vnd.microsoft.appconfig.kv+json;
If-None-Match: "{etag}"
```

**Response**

```http
HTTP/1.1 304 NotModified
```

ou

```http
HTTP/1.1 200 OK
```

## <a name="list-key-values"></a>Listar valores de chave

Opcional: ``key`` (se não for especificado, ele implica qualquer chave.) Opcional: ``label`` (se não for especificado, ele implica qualquer rótulo.)

```http
GET /kv?label=*&api-version={api-version} HTTP/1.1
```

**Resposta:**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kvset+json; charset=utf-8
```

Para obter opções adicionais, consulte a seção "filtragem" mais adiante neste artigo.

## <a name="pagination"></a>Paginação

O resultado será paginado se o número de itens retornados exceder o limite de resposta. Siga os cabeçalhos de resposta opcionais `Link` e use `rel="next"` para navegação.
Como alternativa, o conteúdo fornece um link seguinte na forma da `@nextLink` propriedade. O URI vinculado inclui o `api-version` argumento.

```http
GET /kv?api-version={api-version} HTTP/1.1
```

**Resposta:**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kvs+json; charset=utf-8
Link: <{relative uri}>; rel="next"
```

```json
{
    "items": [
        ...
    ],
    "@nextLink": "{relative uri}"
}
```

## <a name="filtering"></a>Filtragem

`key`Há suporte para uma combinação de e `label` filtragem.
Use os `key` parâmetros de cadeia de caracteres opcional e de `label` consulta.

```http
GET /kv?key={key}&label={label}&api-version={api-version}
```

### <a name="supported-filters"></a>Filtros com suporte

|Filtro de chave|Efeito|
|--|--|
|`key` é omitido ou `key=*`|Corresponde a **qualquer** chave|
|`key=abc`|Corresponde a uma chave chamada **ABC**|
|`key=abc*`|Corresponde aos nomes de chaves que começam com **ABC**|
|`key=abc,xyz`|Corresponde aos nomes de chaves **ABC** ou **XYZ** (limitado a 5 CSV)|

|Filtro de rótulo|Efeito|
|--|--|
|`label` é omitido ou `label=*`|Corresponde a **qualquer** rótulo|
|`label=%00`|Corresponde a KV sem rótulo|
|`label=prod`|Corresponde ao rótulo **prod**|
|`label=prod*`|Corresponde a rótulos que começam com **prod**|
|`label=prod,test`|Faz a correspondência dos rótulos **prod** ou **Test** (limitado a 5 CSV)|

***Caracteres reservados***

`*`, `\`, `,`

Se um caractere reservado fizer parte do valor, ele deverá ser ignorado com o uso de `\{Reserved Character}` . Caracteres não reservados também podem ser ignorados.

***Validação de filtro***

No caso de um erro de validação de filtro, a resposta é HTTP `400` com os detalhes do erro:

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
```

```json
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Invalid request parameter '{filter}'",
  "name": "{filter}",
  "detail": "{filter}(2): Invalid character",
  "status": 400
}
```

**Exemplos**

- Tudo

    ```http
    GET /kv?api-version={api-version}
    ```

- O nome da chave começa com **ABC** e inclui todos os rótulos

    ```http
    GET /kv?key=abc*&label=*&api-version={api-version}
    ```

- O nome da chave começa com **ABC** e o rótulo é igual a **v1** ou **v2**

    ```http
    GET /kv?key=abc*&label=v1,v2&api-version={api-version}
    ```

## <a name="request-specific-fields"></a>Campos específicos da solicitação

Use o parâmetro opcional de `$select` cadeia de caracteres de consulta e forneça uma lista separada por vírgulas dos campos solicitados. Se o `$select` parâmetro for omitido, a resposta conterá o conjunto padrão.

```http
GET /kv?$select=key,value&api-version={api-version} HTTP/1.1
```

## <a name="time-based-access"></a>Acesso baseado em tempo

Obtenha uma representação do resultado como estava em um momento anterior. Para obter mais informações, consulte a seção [2.1.1](https://tools.ietf.org/html/rfc7089#section-2.1). A paginação ainda tem suporte conforme definido anteriormente neste artigo.

```http
GET /kv?api-version={api-version} HTTP/1.1
Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT
```

**Resposta:**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kvset+json"
Memento-Datetime: Sat, 12 May 2018 02:10:00 GMT
Link: <{relative uri}>; rel="original"
```

```json
{
    "items": [
        ....
    ]
}
```

## <a name="set-key"></a>Definir chave

- Necessário: ``{key}``
- Opcional: ``label`` (se não for especificado, ou rótulo = %00, ele implica o valor da chave sem um rótulo.)

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json
```

```json
{
  "value": "example value",         // optional
  "content_type": "user defined",   // optional
  "tags": {                         // optional
    "tag1": "value1",
    "tag2": "value2",
  }
}
```

**Response**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8
Last-Modified: Tue, 05 Dec 2017 02:41:26 GMT
ETag: "4f6dd610dd5e4deebc7fbaef685fb903"
```

```json
{
  "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
  "key": "{key}",
  "label": "{label}",
  "content_type": "user defined",
  "value": "example value",
  "last_modified": "2017-12-05T02:41:26.4874615+00:00",
  "tags": {
    "tag1": "value1",
    "tag2": "value2",
  }
}
```

Se o item estiver bloqueado, você receberá a seguinte resposta:

```http
HTTP/1.1 409 Conflict
Content-Type: application/problem+json; charset="utf-8"
```

```json
{
    "type": "https://azconfig.io/errors/key-locked",
    "title": "Modifing key '{key}' is not allowed",
    "name": "{key}",
    "detail": "The key is read-only. To allow modification unlock it first.",
    "status": "409"
}
```

## <a name="set-key-conditionally"></a>Definir chave (condicionalmente)

Para evitar condições de corrida, use `If-Match` ou `If-None-Match` cabeçalhos de solicitação. O `etag` argumento faz parte da representação de chave.
Se `If-Match` ou `If-None-Match` forem omitidos, a operação será incondicional.

A resposta a seguir atualizará o valor somente se a representação atual corresponder ao especificado `etag` :

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json
If-Match: "4f6dd610dd5e4deebc7fbaef685fb903"
```

A resposta a seguir atualizará o valor somente se a representação atual não corresponder ao especificado `etag` :

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json;
If-None-Match: "4f6dd610dd5e4deebc7fbaef685fb903"
```

A solicitação a seguir adicionará o valor somente se uma representação já existir:

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json;
If-Match: "*"
```

A solicitação a seguir adicionará o valor somente se uma representação ainda não existir:

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json
If-None-Match: "*"
```

**Respostas**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8
...
```

ou

```http
HTTP/1.1 412 PreconditionFailed
```

## <a name="delete"></a>Excluir

- Necessário: `{key}` , `{api-version}`
- Opcional: `{label}` (se não for especificado, ou rótulo = %00, ele implica o valor da chave sem um rótulo.)

```http
DELETE /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
```

**Resposta:** Retorna o valor de chave excluído ou nenhum se o valor de chave não existir.

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8
...
```

ou

```http
HTTP/1.1 204 No Content
```

## <a name="delete-key-conditionally"></a>Excluir chave (condicionalmente)

Isso é semelhante à seção "definir chave (condicionalmente)" anteriormente neste artigo.
