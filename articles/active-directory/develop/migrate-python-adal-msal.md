---
title: Guia de migração do Python ADAL para MSAL | Azure
titleSuffix: Microsoft identity platform
description: Saiba como migrar seu aplicativo Python da ADAL (biblioteca de autenticação do Azure Active Directory) para a MSAL (biblioteca de autenticação da Microsoft) para Python.
services: active-directory
author: rayluo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Python
ms.workload: identity
ms.date: 11/11/2019
ms.author: rayluo
ms.reviewer: marsma, rayluo, nacanuma
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: 60d6e40b568c1189cdc01ef4239612d3717063b7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104578831"
---
# <a name="adal-to-msal-migration-guide-for-python"></a>Guia de migração do ADAL para MSAL para Python

Este artigo realça as alterações que você precisa fazer para migrar um aplicativo que usa a ADAL (biblioteca de autenticação Azure Active Directory) para usar a MSAL (biblioteca de autenticação da Microsoft).

## <a name="difference-highlights"></a>Destaques da diferença

A ADAL funciona com o ponto de extremidade do Azure Active Directory (Azure AD) v 1.0. A MSAL (biblioteca de autenticação da Microsoft) funciona com a plataforma de identidade da Microsoft, conhecida anteriormente como ponto de extremidade do Azure Active Directory v 2.0. A plataforma Microsoft Identity é diferente do Azure AD v 1.0, pois:

Oferece suporte a:
  - contas corporativas e de estudante (contas do Azure AD provisionadas)
  - contas pessoais (como Outlook.com ou Hotmail.com)
  - clientes que trazem seus próprios emails ou identidades sociais (como LinkedIn, Facebook e Google) por meio da oferta do Azure AD B2C

- Os padrões são compatíveis com:
  - OAuth v 2.0
  - OpenID Connect (OIDC)

Veja [o que há de diferente na plataforma de identidade da Microsoft?](../azuread-dev/azure-ad-endpoint-comparison.md) para obter mais detalhes.

### <a name="scopes-not-resources"></a>Escopos não recursos

O Python ADAL adquire tokens para recursos, mas o Python MSAL adquire tokens para escopos. A superfície de API no MSAL Python não tem mais o parâmetro de recurso. Você precisaria fornecer escopos como uma lista de cadeias de caracteres que declaram as permissões e os recursos desejados que são solicitados. Para ver alguns exemplos de escopos, consulte [escopos de Microsoft Graph](/graph/permissions-reference).

Você pode adicionar o `/.default` sufixo de escopo ao recurso para ajudar a migrar seus aplicativos do ponto de extremidade v 1.0 (Adal) para a plataforma de identidade da Microsoft (MSAL). Por exemplo, para o valor do recurso de `https://graph.microsoft.com` , o valor de escopo equivalente é `https://graph.microsoft.com/.default` .  Se o recurso não estiver no formato de URL, mas uma ID de recurso do formulário `XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXX` , você ainda poderá usar o valor de escopo como `XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXX/.default` .

Para obter mais detalhes sobre os diferentes tipos de escopos, consulte [permissões e consentimento na plataforma de identidade da Microsoft](./v2-permissions-and-consent.md) e os [escopos para uma API Web aceitando os artigos de tokens v 1.0](./msal-v1-app-scopes.md) .

### <a name="error-handling"></a>Tratamento de erros

A ADAL (biblioteca de autenticação Azure Active Directory) para Python usa a exceção `AdalError` para indicar que há um problema. O MSAL para o Python normalmente usa códigos de erro, em vez disso. Para obter mais informações, consulte [MSAL para tratamento de erros do Python](msal-error-handling-python.md).

### <a name="api-changes"></a>Alterações de API

A tabela a seguir lista uma API no ADAL para Python e aquela a ser usada em seu lugar no MSAL para Python:

| ADAL para API do Python  | MSAL para API do Python |
| ------------------- | ---------------------------------- |
| [AuthenticationContext](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext)  | [PublicClientApplication ou ConfidentialClientApplication](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.__init__)  |
| N/D  | [get_authorization_request_url ()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.get_authorization_request_url)  |
| [acquire_token_with_authorization_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_authorization_code) | [acquire_token_by_authorization_code ()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_by_authorization_code) |
| [acquire_token ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token) | [acquire_token_silent ()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_silent) |
| [acquire_token_with_refresh_token ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_refresh_token) | N/D |
| [acquire_user_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_user_code) | [initiate_device_flow ()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.initiate_device_flow) |
| [acquire_token_with_device_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_device_code) e [cancel_request_to_get_token_with_device_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.cancel_request_to_get_token_with_device_code) | [acquire_token_by_device_flow ()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_device_flow) |
| [acquire_token_with_username_password ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_username_password) | [acquire_token_by_username_password ()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_username_password) |
| [acquire_token_with_client_credentials ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_credentials) e [acquire_token_with_client_certificate ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_certificate) | [acquire_token_for_client ()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_for_client) |
| N/D | [acquire_token_on_behalf_of ()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) |
| [TokenCache ()](https://adal-python.readthedocs.io/en/latest/#adal.TokenCache) | [SerializableTokenCache()](https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache) |
| N/D | Cache com persistência, disponível em [extensões MSAL](https://github.com/marstr/original-microsoft-authentication-extensions-for-python) |

## <a name="migrate-existing-refresh-tokens-for-msal-python"></a>Migrar tokens de atualização existentes para Python MSAL

A MSAL (biblioteca de autenticação da Microsoft) abstrai o conceito de tokens de atualização. O MSAL Python fornece um cache de token na memória por padrão para que você não precise armazenar, Pesquisar ou atualizar tokens de atualização. Os usuários também verão menos prompts de entrada, pois os tokens de atualização geralmente podem ser atualizados sem a intervenção do usuário. Para obter mais informações sobre o cache de token, consulte [serialização personalizada de cache de token em MSAL para Python](msal-python-token-cache-serialization.md).

O código a seguir o ajudará a migrar seus tokens de atualização gerenciados por outra biblioteca do OAuth2 (incluindo, mas não se limitando ao Python do ADAL) para ser gerenciado pelo MSAL para Python. Um motivo para migrar esses tokens de atualização é impedir que os usuários existentes precisem entrar novamente ao migrar seu aplicativo para o MSAL para Python.

O método para migrar um token de atualização é usar o MSAL para Python para adquirir um novo token de acesso usando o token de atualização anterior. Quando o novo token de atualização for retornado, o MSAL para Python irá armazená-lo no cache.
Desde o MSAL Python 1.3.0, fornecemos uma API dentro de MSAL para essa finalidade.
Consulte o trecho de código a seguir, entre aspas, de [uma amostra completa de migrar tokens de atualização com Python MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.3.0/sample/migrate_rt.py#L28-L67)

```python
import msal
def get_preexisting_rt_and_their_scopes_from_elsewhere():
    # Maybe you have an ADAL-powered app like this
    #   https://github.com/AzureAD/azure-activedirectory-library-for-python/blob/1.2.3/sample/device_code_sample.py#L72
    # which uses a resource rather than a scope,
    # you need to convert your v1 resource into v2 scopes
    # See https://docs.microsoft.com/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources
    # You may be able to append "/.default" to your v1 resource to form a scope
    # See https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#the-default-scope

    # Or maybe you have an app already talking to the Microsoft identity platform,
    # powered by some 3rd-party auth library, and persist its tokens somehow.

    # Either way, you need to extract RTs from there, and return them like this.
    return [
        ("old_rt_1", ["scope1", "scope2"]),
        ("old_rt_2", ["scope3", "scope4"]),
        ]


# We will migrate all the old RTs into a new app powered by MSAL
app = msal.PublicClientApplication(
    "client_id", authority="...",
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache
    )

# We choose a migration strategy of migrating all RTs in one loop
for old_rt, scopes in get_preexisting_rt_and_their_scopes_from_elsewhere():
    result = app.acquire_token_by_refresh_token(old_rt, scopes)
    if "error" in result:
        print("Discarding unsuccessful RT. Error: ", json.dumps(result, indent=2))

print("Migration completed")
```


## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, confira a [comparação entre a v1.0 e a v2.0](../azuread-dev/azure-ad-endpoint-comparison.md).
