---
title: API REST de configuração de Azure App-rótulos
description: Páginas de referência para trabalhar com rótulos usando a API REST de configuração de Azure App
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 5a59f5910d44f2a2b4cd75e7a1d51c2ed5dd51a4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96932499"
---
# <a name="labels"></a>Rótulos

versão da API: 1,0

O recurso de **rótulo** é definido da seguinte maneira:

```json
{
      "name": [string]             // Name of the label
}
```

O oferece suporte às seguintes operações:

- Lista

Para todas as operações ``name`` é um parâmetro de filtro opcional. Se omitido, ele implica **qualquer** rótulo.

## <a name="prerequisites"></a>Pré-requisitos

- Todas as solicitações HTTP devem ser autenticadas. Consulte a seção [autenticação](./rest-api-authentication-index.md) .
- Todas as solicitações HTTP devem fornecer Explicit `api-version` . Consulte a seção [controle de versão](./rest-api-versioning.md) .

## <a name="list-labels"></a>Listar rótulos

```http
GET /labels?api-version={api-version} HTTP/1.1
```

**Response**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.labelset+json; charset=utf-8"
```

```json
{
    "items": [
        {
          "name": "{label-name}"
        },
        ...
    ],
    "@nextLink": "{relative uri}"
}
```

## <a name="pagination"></a>Paginação

O resultado será paginado se o número de itens retornados exceder o limite de resposta. Siga os cabeçalhos de resposta opcionais `Link` e use `rel="next"` para navegação. Como alternativa, o conteúdo fornece um link próximo na forma da `@nextLink` propriedade. O próximo link contém o `api-version` parâmetro.

```http
GET /labels?api-version={api-version} HTTP/1.1
```

**Resposta:**

```http
HTTP/1.1 OK
Content-Type: application/vnd.microsoft.appconfig.labelset+json; charset=utf-8
Accept-Ranges: items
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

`name`Há suporte para a filtragem por.

```http
GET /labels?name={label-name}&api-version={api-version}
```

### <a name="supported-filters"></a>Filtros com suporte

|Filtro de chave|Efeito|
|--|--|
|`name` é omitido ou `name=*`|Corresponde a **qualquer** rótulo|
|`name=abc`|Corresponde a um rótulo chamado  **ABC**|
|`name=abc*`|Faz a correspondência de nomes de rótulo que começam com **ABC**|
|`name=abc,xyz`|Corresponde aos nomes de rótulo **ABC** ou **XYZ** (limitado a 5 CSV)|

### <a name="reserved-characters"></a>Caracteres reservados

`*`, `\`, `,`

Se um caractere reservado fizer parte do valor, ele deverá ter um escape usando `\{Reserved Character}` . Caracteres não reservados também podem ser ignorados.

### <a name="filter-validation"></a>Validação de filtro

Se ocorrer um erro de validação de filtro, a resposta será HTTP `400` com os detalhes do erro:

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

### <a name="examples"></a>Exemplos

- Tudo

    ```http
    GET /labels?api-version={api-version}
    ```

- O nome do rótulo começa com **ABC**

    ```http
    GET  /labels?name=abc*&api-version={api-version}
    ```

- O nome do rótulo é **ABC** ou **XYZ**

    ```http
    GET /labels?name=abc,xyz&api-version={api-version}
    ```

## <a name="request-specific-fields"></a>Campos específicos da solicitação

Use o parâmetro opcional de `$select` cadeia de caracteres de consulta e forneça a lista separada por vírgulas dos campos solicitados. Se o `$select` parâmetro for omitido, a resposta conterá o conjunto padrão.

```http
GET /labels?$select=name&api-version={api-version} HTTP/1.1
```

## <a name="time-based-access"></a>Acesso Time-Based

Obtenha uma representação do resultado como estava em um momento anterior. Consulte a seção [2.1.1](https://tools.ietf.org/html/rfc7089#section-2.1)

```http
GET /labels&api-version={api-version} HTTP/1.1
Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT
```

**Resposta:**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.labelset+json"
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
