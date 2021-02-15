---
title: API REST de configuração de Azure App-bloqueios
description: Páginas de referência para trabalhar com bloqueios de chave-valor usando a API REST de configuração de Azure App
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: e99ce5595bae8ed64285317d9249da60e0fc1b83
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932515"
---
# <a name="locks"></a>Locks

Essa API (versão 1,0) fornece semântica de bloqueio e desbloqueio para o recurso chave-valor. Ele dá suporte às seguintes operações:

- Local de bloqueio
- Remover bloqueio

Se presente, `label` deve ser um valor de rótulo explícito (não um curinga). Para todas as operações, é um parâmetro opcional. Se omitido, ele não implica nenhum rótulo.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="lock-key-value"></a>Chave de bloqueio-valor

- Necessário: ``{key}`` , ``{api-version}``  
- Opcional: ``label``

```http
PUT /locks/{key}?label={label}&api-version={api-version} HTTP/1.1
```

**Response**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8"
```

```json
{
  "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
  "key": "{key}",
  "label": "{label}",
  "content_type": null,
  "value": "example value",
  "created": "2017-12-05T02:41:26.4874615+00:00",
  "locked": true,
  "tags": []
}
```

Se o valor de chave não existir, a seguinte resposta será retornada:

```http
HTTP/1.1 404 Not Found
```

## <a name="unlock-key-value"></a>Desbloquear chave-valor

- Necessário: ``{key}`` , ``{api-version}``  
- Opcional: ``label``

```http
DELETE /locks/{key}?label={label}?api-version={api-version} HTTP/1.1
```

**Response**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8"
```

```json
{
  "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
  "key": "{key}",
  "label": "{label}",
  "content_type": null,
  "value": "example value",
  "created": "2017-12-05T02:41:26.4874615+00:00",
  "locked": true,
  "tags": []
}
```

Se o valor de chave não existir, a seguinte resposta será retornada:

```http
HTTP/1.1 404 Not Found
```

## <a name="conditional-lock-and-unlock"></a>Bloqueio condicional e desbloquear

Para evitar condições de corrida, use `If-Match` ou `If-None-Match` cabeçalhos de solicitação. O `etag` argumento faz parte da representação de chave. Se `If-Match` ou `If-None-Match` forem omitidos, a operação será incondicional.

A solicitação a seguir aplica a operação somente se a representação de chave-valor atual corresponder à especificada `etag` :

```http
PUT|DELETE /locks/{key}?label={label}&api-version={api-version} HTTP/1.1
If-Match: "4f6dd610dd5e4deebc7fbaef685fb903"
```

A solicitação a seguir aplica a operação somente se a representação de chave-valor atual existir, mas não corresponder ao especificado `etag` :

```http
PUT|DELETE /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
If-None-Match: "4f6dd610dd5e4deebc7fbaef685fb903"
```
