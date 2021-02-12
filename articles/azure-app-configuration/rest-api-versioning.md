---
title: API REST de configuração de Azure App-controle de versão
description: Páginas de referência para controle de versão usando a API REST de configuração de Azure App
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: a869531860942e5a8b2b05212e778aca2170c89b
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932414"
---
# <a name="versioning"></a>Controle de versão

Cada solicitação de cliente deve fornecer uma versão de API explícita como um parâmetro de cadeia de caracteres de consulta. Por exemplo: `https://{myconfig}.azconfig.io/kv?api-version=1.0`.

`api-version` é expresso no formato SemVer (Major. minor). Não há suporte para a negociação de intervalo ou versão.

Este artigo se aplica à versão de API 1,0.

O seguinte descreve um resumo das possíveis respostas de erro retornadas pelo servidor quando a versão de API solicitada não pode ser correspondida.

## <a name="api-version-unspecified"></a>Versão da API não especificada

Esse erro ocorre quando um cliente faz uma solicitação sem fornecer uma versão de API.

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "API version is not specified",
  "name": "api-version",
  "detail": "An API version is required, but was not specified.",
  "status": 400
}
```

## <a name="unsupported-api-version"></a>Versão de API sem suporte

Esse erro ocorre quando uma versão de API solicitada pelo cliente não corresponde a nenhuma das versões de API com suporte pelo servidor.

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Unsupported API version",
  "name": "api-version",
  "detail": "The HTTP resource that matches the request URI '{request uri}' does not support the API version '{api-version}'.",
  "status": 400
}
```

## <a name="invalid-api-version"></a>Versão de API inválida

Esse erro ocorre quando um cliente faz uma solicitação com uma versão de API, mas o valor está malformado ou não pode ser analisado pelo servidor.

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8  
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Invalid API version",
  "name": "api-version",
  "detail": "The HTTP resource that matches the request URI '{request uri}' does not support the API version '{api-version}'.",
  "status": 400
}
```

## <a name="ambiguous-api-version"></a>Versão de API ambígua

Esse erro ocorre quando um cliente solicita uma versão de API que é ambígua para o servidor (por exemplo, vários valores diferentes).

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Ambiguous API version",
  "name": "api-version",
  "detail": "The following API versions were requested: {comma separated api versions}. At most, only a single API version may be specified. Please update the intended API version and retry the request.",
  "status": 400
}
```
