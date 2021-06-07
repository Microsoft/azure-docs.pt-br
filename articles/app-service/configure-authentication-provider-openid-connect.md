---
title: Configurar um provedor do OpenID Connect (versão prévia)
description: Saiba como configurar um provedor do OpenID Connect como um provedor de identidade para seu serviço de aplicativo ou Azure Functions aplicativo.
ms.topic: article
ms.date: 07/08/2020
ms.reviewer: mahender
ms.openlocfilehash: e8112f2dc20175e81cfa8388440b2d9aef6a419c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "90983869"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-login-using-an-openid-connect-provider-preview"></a>Configure o serviço de aplicativo ou o aplicativo do Azure Functions para fazer logon usando um provedor do OpenID Connect (versão prévia)

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este artigo mostra como configurar Azure App serviço ou Azure Functions para usar um provedor de autenticação personalizado que adere à [especificação do OpenID Connect](https://openid.net/connect/). O OpenID Connect (OIDC) é um padrão do setor usado por muitos provedores de identidade (IDPs). Você não precisa entender os detalhes da especificação para configurar seu aplicativo para usar um IDP adherent.

Você pode configurar seu aplicativo para usar um ou mais provedores de OIDC. Cada um deve receber um nome exclusivo na configuração e apenas um pode servir como o destino de redirecionamento padrão.

> [!CAUTION]
> Habilitar um provedor do OpenID Connect desabilitará o gerenciamento do recurso de autenticação/autorização do serviço de aplicativo para seu aplicativo por meio de alguns clientes, como o portal do Azure, CLI do Azure e Azure PowerShell. O recurso depende de uma nova superfície de API que, durante a versão prévia, ainda não foi contabilizada em todas as experiências de gerenciamento.

## <a name="register-your-application-with-the-identity-provider"></a><a name="register"> </a>Registrar seu aplicativo com o provedor de identidade

Seu provedor exigirá que você registre os detalhes do seu aplicativo com ele. Consulte as instruções relevantes para esse provedor. Você precisará coletar uma **ID do cliente** e um **segredo do cliente** para seu aplicativo.

> [!IMPORTANT]
> O segredo do aplicativo é uma credencial de segurança importante. Não compartilhe essa senha com ninguém nem distribua-a em um aplicativo cliente.
>

> [!NOTE]
> Alguns provedores podem exigir etapas adicionais para sua configuração e como usar os valores que eles fornecem. Por exemplo, a Apple fornece uma chave privada que não é usada por si só como o segredo do cliente OIDC e, em vez disso, você deve usá-la para criar um JWT que é tratado como o segredo que você fornece em sua configuração de aplicativo (consulte a seção "criando o segredo do cliente" de [entrar com a documentação da Apple](https://developer.apple.com/documentation/sign_in_with_apple/generate_and_validate_tokens))
>

Adicione o segredo do cliente como uma [configuração de aplicativo](./configure-common.md#configure-app-settings) para o aplicativo, usando um nome de configuração de sua escolha. Anote esse nome para mais tarde.

Além disso, você precisará dos metadados do OpenID Connect para o provedor. Isso geralmente é exposto por meio de um [documento de metadados de configuração](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderConfig), que é o sufixo da URL do emissor do provedor com `/.well-known/openid-configuration` . Reúna esta URL de configuração.

Se você não conseguir usar um documento de metadados de configuração, será necessário reunir os seguintes valores separadamente:

- A URL do emissor (às vezes mostrada como `issuer` )
- O [ponto de extremidade de autorização do OAuth 2,0](https://tools.ietf.org/html/rfc6749#section-3.1) (às vezes mostrado como `authorization_endpoint` )
- O [ponto de extremidade do Token 2,0 do OAuth](https://tools.ietf.org/html/rfc6749#section-3.2) (às vezes mostrado como `token_endpoint` )
- A URL do documento do [conjunto de chaves da Web JSON do OAuth 2,0](https://tools.ietf.org/html/rfc8414#section-2) (às vezes mostrado como `jwks_uri` )

## <a name="add-provider-information-to-your-application"></a><a name="configure"> </a>Adicionar informações de provedor ao seu aplicativo

> [!NOTE]
> A configuração necessária está em um novo formato de API, atualmente só tem suporte da [Configuração baseada em arquivo (versão prévia)](.\app-service-authentication-how-to.md#config-file). Você precisará seguir as etapas a seguir usando esse arquivo.

Esta seção explicará como atualizar a configuração para incluir o novo IDP. Segue um exemplo de configuração.

1. No `identityProviders` objeto, adicione um `openIdConnectProviders` objeto se ele ainda não existir.
1. No `openIdConnectProviders` objeto, adicione uma chave para seu novo provedor. Esse é um nome amigável usado para referenciar o provedor no restante da configuração. Por exemplo, se você quisesse exigir que todas as solicitações fossem autenticadas com esse provedor, você definiria `globalValidation.unauthenticatedClientAction` como "RedirectToLoginPage" e configuraria `redirectToProvider` esse mesmo nome amigável.
1. Atribua um objeto a essa chave com um `registration` objeto dentro dele e, opcionalmente, um `login` objeto:
    
    ```json
    "myCustomIDP" : {
       "registration" : {},
       "login": {
             "nameClaimType": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name",
             "scope": [],
             "loginParameterNames": [],
       }
    }
    ```

1. No objeto de registro, defina `clientId` como a ID do cliente coletada, defina `clientCredential.secretSettingName` como o nome da configuração do aplicativo em que você armazenou o segredo do cliente e crie um `openIdConnectConfiguration` objeto:

    ```json
    "registration": {
      "clientId": "bd96cf8a-3f2b-4806-b180-d3c5fd11a2be",
      "clientCredential": {
         "secretSettingName": "IDP_CLIENT_SECRET"
      },
      "openIdConnectConfiguration" : {}
    }
    ```

1. Dentro do `openIdConnectConfiguration` objeto, forneça os metadados do OpenID Connect que você coletou anteriormente. Há duas opções para isso, com base nas informações coletadas:

    - Defina a `wellKnownOpenIdConfiguration` propriedade para a URL de metadados de configuração que você coletou anteriormente.
    - Como alternativa, defina os quatro valores individuais coletados da seguinte maneira:
        - Definir `issuer` para a URL do emissor
        - Definir `authorizationEndpoint` como o ponto de extremidade de autorização
        - Definir `tokenEndpoint` como o ponto de extremidade do token
        - Definir `certificationUri` para a URL do documento do conjunto de chaves da Web JSON

    Essas duas opções são mutuamente exclusivas.

Depois que essa configuração tiver sido definida, você estará pronto para usar seu provedor do OpenID Connect para autenticação em seu aplicativo.

Uma configuração de exemplo pode ser parecida com a seguinte (usando entrar com a Apple como um exemplo, em que a configuração de APPLE_GENERATED_CLIENT_SECRET aponta para um JWT gerado de acordo com a [documentação da Apple](https://developer.apple.com/documentation/sign_in_with_apple/generate_and_validate_tokens)):

```json
{
    "platform": {
        "enabled": true
    },
    "globalValidation": {
        "redirectToProvider": "apple",
        "unauthenticatedClientAction": "RedirectToLoginPage"
    },
    "identityProviders": {
        "openIdConnectProviders": {
            "apple": {
                "registration": {
                    "clientId": "com.contoso.example.client",
                    "clientCredential": {
                        "secretSettingName": "APPLE_GENERATED_CLIENT_SECRET"
                    },
                    "openIdConnectConfiguration": {
                        "wellKnownOpenIdConfiguration": "https://appleid.apple.com/.well-known/openid-configuration"
                    }
                },
                "login": {
                    "nameClaimType": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name",
                    "scope": [],
                    "loginParameterNames": []
                }
            }
        }
    },
    "login": {
        "tokenStore": {
            "enabled": true
        }
    }     
}
```

## <a name="next-steps"></a><a name="related-content"> </a>Próximas etapas

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]
