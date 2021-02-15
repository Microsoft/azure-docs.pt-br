---
title: API REST do Azure Active Directory-autenticação
description: Usar Azure Active Directory para autenticar na configuração do Azure App usando a API REST
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: cbf05245768a663e324e9bb6e1ad422eeee3ab1a
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96930510"
---
# <a name="azure-active-directory-authentication"></a>Autenticação do Azure Active Directory

Você pode autenticar solicitações HTTP usando o `Bearer` esquema de autenticação com um token adquirido do Azure Active Directory (AD do Azure). Você deve transmitir essas solicitações por TLS (Transport Layer Security).

## <a name="prerequisites"></a>Pré-requisitos

Você deve atribuir a entidade de segurança usada para solicitar um token do Azure AD a uma das [funções de configuração Azure app](./rest-api-authorization-azure-ad.md)aplicáveis.

Forneça cada solicitação com todos os cabeçalhos HTTP necessários para autenticação. Aqui está o requisito mínimo:

|  Cabeçalho da solicitação | Descrição  |
| --------------- | ------------ |
| `Authorization` | Informações de autenticação exigidas pelo `Bearer` esquema. |

**Exemplo:**

```http
Host: {myconfig}.azconfig.io
Authorization: Bearer {{AadToken}}
```

## <a name="azure-ad-token-acquisition"></a>Aquisição de token do Azure AD

Antes de adquirir um token do Azure AD, você deve identificar o usuário que deseja autenticar como o público-alvo para o qual você está solicitando o token e qual ponto de extremidade do AD do Azure (autoridade) usar.

### <a name="audience"></a>Público

Solicite o token do Azure AD com um público-alvo adequado. Para Azure App configuração, use um dos seguintes públicos. O público-alvo também pode ser referido como o *recurso* para o qual o token está sendo solicitado.

- {configurationStoreName}. azconfig. Io
- *. azconfig.io

> [!IMPORTANT]
> Quando o público-alvo solicitado é `{configurationStoreName}.azconfig.io` , ele deve corresponder exatamente ao `Host` cabeçalho da solicitação (diferencia maiúsculas de minúsculas) usado para enviar a solicitação.

### <a name="azure-ad-authority"></a>Autoridade do Azure AD

A autoridade do Azure AD é o ponto de extremidade usado para adquirir um token do Azure AD. Ele está na forma de `https://login.microsoftonline.com/{tenantId}` . O `{tenantId}` segmento refere-se à ID de locatário do Azure ad à qual o usuário ou aplicativo que está tentando autenticar pertence.

### <a name="authentication-libraries"></a>Bibliotecas de autenticação

O Azure fornece um conjunto de bibliotecas, chamadas Azure Active Directory bibliotecas de autenticação, para simplificar o processo de aquisição de um token do Azure AD. O Azure cria essas bibliotecas para vários idiomas. Para obter mais informações, confira a [documentação](../active-directory/azuread-dev/active-directory-authentication-libraries.md).

## <a name="errors"></a>Errors

Você pode encontrar os erros a seguir.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer
```

**Motivo:** Você não forneceu o cabeçalho de solicitação de autorização com o `Bearer` esquema.

**Solução:** Forneça um `Authorization` cabeçalho de solicitação HTTP válido.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer error="invalid_token", error_description="Authorization token failed validation"
```

**Motivo:** O token do Azure AD não é válido.

**Solução:** Adquira um token do Azure AD da autoridade do Azure AD e verifique se você usou o público-alvo adequado.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer error="invalid_token", error_description="The access token is from the wrong issuer. It must match the AD tenant associated with the subscription to which the configuration store belongs. If you just transferred your subscription and see this error message, please try back later."
```

**Motivo:** O token do Azure AD não é válido.

**Solução:** Adquira um token do Azure AD da autoridade do Azure AD. Verifique se o locatário do Azure AD é aquele associado à assinatura à qual o repositório de configuração pertence. Esse erro pode aparecer se a entidade pertencer a mais de um locatário do Azure AD.