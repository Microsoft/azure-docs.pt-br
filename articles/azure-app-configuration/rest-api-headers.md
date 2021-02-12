---
title: API REST de configuração de Azure App-cabeçalhos
description: Páginas de referência para cabeçalhos usados com a API REST de configuração de Azure App
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 80b20b0b55219766872166685c0b1257f3c39c55
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932567"
---
# <a name="headers"></a>Cabeçalhos

Este artigo fornece links para páginas de referência para cabeçalhos usados com a API REST de configuração do Azure App.

## <a name="request-headers"></a>Cabeçalhos de solicitação

A tabela a seguir descreve os cabeçalhos de solicitação comuns usados na configuração do Azure App.

| Cabeçalho | Descrição | Exemplo |
|--|--|--|
| **Autorização** | Usado para [autenticar](./rest-api-authentication-index.md) uma solicitação para o serviço. Consulte a [seção 14,8](https://tools.ietf.org/html/rfc2616#section-14.8) | `Authorization: HMAC-SHA256 Credential=<Credential>&SignedHeaders=Host;x-ms-date;x-ms-content-sha256&Signature=<Signature>` |
| **Aceitar** | Informa ao servidor qual tipo de mídia o cliente aceitará em uma resposta HTTP. Consulte a [seção 14,1](https://tools.ietf.org/html/rfc2616#section-14.1) | `Accept: application/vnd.microsoft.appconfig.kv+json;` |
| **Accept-DateTime** | Solicita que o servidor retorne seu conteúdo como uma representação de seu estado anterior. O valor desse cabeçalho é o DateTime solicitado desse Estado. Consulte [RFC 7089](https://tools.ietf.org/html/rfc7089#section-2.1.1) | `Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT` |
| **Content-Type** | Contém o tipo de mídia do conteúdo dentro do corpo da solicitação HTTP. Consulte a [seção 14,17](https://tools.ietf.org/html/rfc2616#section-14.17) | `Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8;` |
| **Data** | O DateTime em que a solicitação HTTP foi emitida. Esse cabeçalho é usado na [autenticação HMAC](./rest-api-authentication-hmac.md). Consulte a [seção 14,18](https://tools.ietf.org/html/rfc2616#section-14.18) | `Date: Fri, 11 May 2018 18:48:36 GMT` |
| **Host** | Especifica o locatário para o qual a solicitação foi emitida. Esse cabeçalho é usado na [autenticação HMAC](./rest-api-authentication-hmac.md). Consulte a [seção 14,23](https://tools.ietf.org/html/rfc2616#section-14.23) | `Host: contoso.azconfig.io` |
| **If-Match** | Usado para fazer uma solicitação HTTP condicional. Essa solicitação só deverá ser realizada se a ETag do recurso de destino corresponder ao valor desse cabeçalho. O valor ' * ' corresponde a qualquer ETag. Consulte a [seção 14,24](https://tools.ietf.org/html/rfc2616#section-14.24) | `If-Match: "4f6dd610dd5e4deebc7fbaef685fb903"` |
| **If-None-Match** | Usado para fazer uma solicitação HTTP condicional. Essa solicitação só deverá ser realizada se a ETag do recurso de destino não corresponder ao valor desse cabeçalho. O valor ' * ' corresponde a qualquer ETag. Consulte a [seção 14,26](https://tools.ietf.org/html/rfc2616#section-14.26) | `If-None-Match: "4f6dd610dd5e4deebc7fbaef685fb903"` |
| **Sincronizar token** | Usado para habilitar a consistência em tempo real durante uma sequência de solicitações. | `Sync-Token: jtqGc1I4=MDoyOA==;sn=28` |
| **x-ms-client-request-id** | Uma ID exclusiva fornecida pelo cliente usado para acompanhar a viagem de ida e volta de uma solicitação. | `x-ms-client-request-id: 00000000-0000-0000-0000-000000000000` |
| **x-MS-Content-SHA256** | Um resumo SHA256 do corpo da solicitação HTTP. Esse cabeçalho é usado na [autenticação HMAC](./rest-api-authentication-hmac.md). | `x-ms-content-sha256: 47DEQpj8HBSa+/TImW+5JCeuQeRkm5NMpJWZG3hSuFU=` |
| **x-ms-date** | Esse cabeçalho pode ser definido e usado no lugar do `Date` cabeçalho se o cabeçalho de data não puder ser acessado. Esse cabeçalho é usado na [autenticação HMAC](./rest-api-authentication-hmac.md). | `x-ms-date: Fri, 11 May 2018 18:48:36 GMT` |
| **x-ms-return-client-request-id** | Usado em conjunto com o `x-ms-client-request-id` cabeçalho. Se o valor desse cabeçalho for ' true ', o servidor será instruído a retornar o valor do `x-ms-client-request-id` cabeçalho da solicitação. | `x-ms-return-client-request-id: true` |

## <a name="response-headers"></a>Cabeçalhos de resposta

O servidor pode incluir os seguintes cabeçalhos HTTP em suas respostas.

| Cabeçalho | Descrição | Exemplo |
|--|--|--|
| **Content-Type** | Contém o tipo de mídia do conteúdo no corpo da resposta HTTP. Consulte a [seção 14,17](https://tools.ietf.org/html/rfc2616#section-14.17) | `Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8;` |
| **ETag** | Um token opaco que representa o estado de um determinado recurso. Pode ser usado em operações condicionais. Consulte a [seção 14,19](https://tools.ietf.org/html/rfc2616#section-14.19) | `ETag: "4f6dd610dd5e4deebc7fbaef685fb903"` |
| **Última modificação** | Descreve quando o recurso solicitado foi modificado pela última vez. Formatado como uma [data de http](https://tools.ietf.org/html/rfc2616#section-3.3.1). Consulte a [seção 14,29](https://tools.ietf.org/html/rfc2616#section-14.29) | `Last-Modified: Tue, 05 Dec 2017 02:41:26 GMT` |
| **Link** | Fornece links para recursos relacionados à resposta. Esse cabeçalho é usado para paginação usando o link _seguinte_ . Consulte [RFC 5988](https://tools.ietf.org/html/rfc5988) | `Link: </kv?after={token}>; rel="next"` |
| **Lembrança-DateTime** | Indica que o conteúdo contido em uma resposta representa um estado anterior. O valor desse cabeçalho é o DateTime desse Estado. Consulte [RFC 7089](https://tools.ietf.org/html/rfc7089#section-2.1.1) | `Memento-Datetime: Sat, 12 May 2018 02:10:00 GMT` |
| **repetir-após-MS** | Fornece um período sugerido (em milissegundos) para que o cliente aguarde antes de tentar novamente uma solicitação com falha. | `retry-after-ms: 10` |
| **x-ms-request-id** | Uma ID exclusiva gerada pelo servidor que é usada para rastrear a solicitação no serviço. | `x-ms-request-id: 00000000-0000-0000-0000-000000000000` |
| **WWW-Authenticate** | Usado para desafiar clientes para autenticação e fornecer um motivo para o motivo da falha de uma tentativa de autenticação. Consulte a [seção 14,47](https://tools.ietf.org/html/rfc2616#section-14.47) | `WWW-Authenticate: HMAC-SHA256 error="invalid_token" error_description="Invalid Signature"` |
