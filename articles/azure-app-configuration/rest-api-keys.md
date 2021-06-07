---
title: API REST de configuração de Azure App-chaves
description: Páginas de referência para trabalhar com chaves usando a API REST de configuração de Azure App
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 65ca190d7fbd6d8d4df473fbe2112eafbd031fde
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96932533"
---
# <a name="keys"></a>simétricas

versão da API: 1,0

A sintaxe a seguir representa um recurso-chave:

```http
{
    "name": [string]             // Name of the key
}
```

## <a name="operations"></a>Operations

Os recursos principais dão suporte à seguinte operação:

- Lista

Para todas as operações `name` é um parâmetro de filtro opcional. Se omitido, ele implica *qualquer* chave.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="list-keys"></a>Listar chaves

```http
GET /keys?api-version={api-version} HTTP/1.1
```

**Response**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.keyset+json; charset=utf-8"
```

```json
{
    "items": [
        {
          "name": "{key-name}"
        },
        ...
    ],
    "@nextLink": "{relative uri}"
}
```

## <a name="pagination"></a>Paginação

O resultado será paginado se o número de itens retornados exceder o limite de resposta. Siga os cabeçalhos de resposta opcionais `Link` e use `rel="next"` para navegação. Como alternativa, o conteúdo fornece um próximo link na forma da `@nextLink` propriedade. O próximo link contém o `api-version` parâmetro.

```http
GET /keys?api-version={api-version} HTTP/1.1
```

**Resposta:**

```http
HTTP/1.1 OK
Content-Type: application/vnd.microsoft.appconfig.keyset+json; charset=utf-8
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

```name```Há suporte para a filtragem por.

```http
GET /keys?name={key-name}&api-version={api-version}
```

Há suporte para os seguintes filtros:

|Filtro de chave|Efeito|
|--|--|
|`name` é omitido ou `name=*`|Corresponde a **qualquer** chave|
|`name=abc`|Corresponde a uma chave chamada  **ABC**|
|`name=abc*`|Corresponde aos nomes de chave que começam com **abc**|
|`name=abc,xyz`|Corresponde aos nomes de chave **ABC** ou **XYZ** (limitado a 5 CSV)|

Os seguintes caracteres são reservados: `*` , `\` , `,`

Se um caractere reservado fizer parte do valor, ele deverá ter um escape usando `\{Reserved Character}` . Caracteres não reservados também podem ser ignorados.

## <a name="filter-validation"></a>Validação de filtro

No caso de um erro de validação de filtro, a resposta é HTTP `400` com os detalhes do erro:

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
```

```json
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Invalid request parameter 'name'",
  "name": "name",
  "detail": "name(2): Invalid character",
  "status": 400
}
```

## <a name="examples"></a>Exemplos

- Tudo

    ```http
    GET /keys?api-version={api-version}
    ```

- O nome da chave começa com **ABC**

    ```http
    GET  /keys?name=abc*&api-version={api-version}
    ```

- O nome da chave é **ABC** ou **XYZ**

    ```http
    GET /keys?name=abc,xyz&api-version={api-version}
    ```

## <a name="request-specific-fields"></a>Campos específicos da solicitação

Use o parâmetro opcional de `$select` cadeia de caracteres de consulta e forneça a lista separada por vírgulas dos campos solicitados. Se o `$select` parâmetro for omitido, a resposta conterá o conjunto padrão.

```http
GET /keys?$select=name&api-version={api-version} HTTP/1.1
```

## <a name="time-based-access"></a>Acesso Time-Based

Obtenha uma representação do resultado como estava em um momento anterior. Consulte a seção [2.1.1](https://tools.ietf.org/html/rfc7089#section-2.1)

```http
GET /keys&api-version={api-version} HTTP/1.1
Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT
```

**Resposta:**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.keyset+json"
Memento-Datetime: Sat, 12 May 2018 02:10:00 GMT
Link: <relative uri>; rel="original"
```

```json
{
    "items": [
        ....
    ]
}
```
