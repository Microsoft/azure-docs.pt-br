---
title: API do Azure para validação de token de acesso FHIR
description: Percorre a validação de token e fornece dicas sobre como solucionar problemas de acesso
services: healthcare-apis
author: caitlinv39
ms.reviewer: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: cavoeg
ms.openlocfilehash: 426ec0c2d6cc274aa0b6829eb4a30fd29b9ba8e2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87844653"
---
# <a name="azure-api-for-fhir-access-token-validation"></a>API do Azure para validação de token de acesso FHIR

Como a API do Azure para FHIR valida o token de acesso dependerá da implementação e da configuração. Neste artigo, percorreremos as etapas de validação, que podem ser úteis ao solucionar problemas de acesso.

## <a name="validate-token-has-no-issues-with-identity-provider"></a>O token de validação não tem problemas com o provedor de identidade

A primeira etapa na validação do token é verificar se o token foi emitido pelo provedor de identidade correto e se ele não foi modificado. O servidor FHIR será configurado para usar um provedor de identidade específico conhecido como autoridade `Authority` . O servidor FHIR recuperará informações sobre o provedor de identidade do `/.well-known/openid-configuration` ponto de extremidade. Ao usar o Azure AD, a URL completa seria:

```
GET https://login.microsoftonline.com/<TENANT-ID>/.well-known/openid-configuration
```

em que `<TENANT-ID>` é o locatário específico do Azure AD (uma ID de locatário ou um nome de domínio).

O Azure AD retornará um documento como o mostrado abaixo para o servidor FHIR.

```json
{
    "authorization_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize",
    "token_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/token",
    "token_endpoint_auth_methods_supported": [
        "client_secret_post",
        "private_key_jwt",
        "client_secret_basic"
    ],
    "jwks_uri": "https://login.microsoftonline.com/common/discovery/keys",
    "response_modes_supported": [
        "query",
        "fragment",
        "form_post"
    ],
    "subject_types_supported": [
        "pairwise"
    ],
    "id_token_signing_alg_values_supported": [
        "RS256"
    ],
    "http_logout_supported": true,
    "frontchannel_logout_supported": true,
    "end_session_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/logout",
    "response_types_supported": [
        "code",
        "id_token",
        "code id_token",
        "token id_token",
        "token"
    ],
    "scopes_supported": [
        "openid"
    ],
    "issuer": "https://sts.windows.net/<TENANT-ID>/",
    "claims_supported": [
        "sub",
        "iss",
        "cloud_instance_name",
        "cloud_instance_host_name",
        "cloud_graph_host_name",
        "msgraph_host",
        "aud",
        "exp",
        "iat",
        "auth_time",
        "acr",
        "amr",
        "nonce",
        "email",
        "given_name",
        "family_name",
        "nickname"
    ],
    "microsoft_multi_refresh_token": true,
    "check_session_iframe": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/checksession",
    "userinfo_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/openid/userinfo",
    "tenant_region_scope": "WW",
    "cloud_instance_name": "microsoftonline.com",
    "cloud_graph_host_name": "graph.windows.net",
    "msgraph_host": "graph.microsoft.com",
    "rbac_url": "https://pas.windows.net"
}
``` 
As propriedades importantes para o servidor FHIR são `jwks_uri` , que informa ao servidor onde buscar as chaves de criptografia necessárias para validar a assinatura do token e `issuer` , que diz ao servidor o que estará na declaração do emissor ( `iss` ) de tokens emitidos por esse servidor. O servidor FHIR pode usar isso para validar que está recebendo um token autêntico.

## <a name="validate-claims-of-the-token"></a>Validar declarações do token

Depois que o servidor tiver verificado a autenticidade do token, o servidor FHIR continuará a validar que o cliente tem as declarações necessárias para acessar o token.

Ao usar a API do Azure para FHIR, o servidor irá validar:

1. O token tem a direita `Audience` ( `aud` declaração).
1. O usuário ou entidade de segurança para o qual o token foi emitido tem permissão para acessar o plano de dados do servidor FHIR. A `oid` declaração do token contém uma ID de objeto de identidade que identifica exclusivamente o usuário ou a entidade de segurança.

Recomendamos que o serviço FHIR seja [configurado para usar o RBAC do Azure](configure-azure-rbac.md) para gerenciar as atribuições de função do plano de dados. Mas você também pode [Configurar o RBAC local](configure-local-rbac.md) se o serviço FHIR usar um locatário de Azure Active Directory externo ou secundário. 

Ao usar o servidor do Microsoft FHIR em OSS para o Azure, o servidor irá validar:

1. O token tem a direita `Audience` ( `aud` declaração).
1. O token tem uma função na `roles` declaração, que tem permissão de acesso ao servidor FHIR.

Consulte os detalhes sobre como [definir funções no servidor FHIR](https://github.com/microsoft/fhir-server/blob/master/docs/Roles.md).

Um servidor FHIR também pode validar que um token de acesso tem os escopos (em declaração de token `scp` ) para acessar a parte da API FHIR que um cliente está tentando acessar. Atualmente, a API do Azure para FHIR e o servidor FHIR para Azure não validam escopos de token.

## <a name="next-steps"></a>Próximas etapas
Agora que você sabe como percorrer a validação de token, você pode concluir o tutorial para criar um aplicativo JavaScript e ler dados do FHIR.

>[!div class="nextstepaction"]
>[Tutorial de aplicativo Web](tutorial-web-app-fhir-server.md)