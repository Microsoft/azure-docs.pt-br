---
title: Configurar a entrada com a Apple (versão prévia)
description: Saiba como configurar a entrada com a Apple como um provedor de identidade para seu serviço de aplicativo ou Azure Functions aplicativo.
ms.topic: article
ms.date: 11/19/2020
ms.reviewer: mikarmar
ms.openlocfilehash: b77e0613f502d003b5e4651e34be4cadbd4209a9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96603096"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-sign-in-using-a-sign-in-with-apple-provider-preview"></a>Configure seu serviço de aplicativo ou Azure Functions aplicativo para entrar usando uma entrada com o provedor Apple (versão prévia)

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este artigo mostra como configurar Azure App serviço ou Azure Functions para usar a entrada com a Apple como um provedor de autenticação. 

Para concluir o procedimento neste artigo, você deve estar registrado no programa de desenvolvedor da Apple. Para se registrar no programa de desenvolvedor da Apple, acesse [Developer.Apple.com/programs/Enroll](https://developer.apple.com/programs/enroll/).

> [!CAUTION]
> Habilitar a entrada com a Apple desabilitará o gerenciamento do recurso de autenticação/autorização do serviço de aplicativo para seu aplicativo por meio de alguns clientes, como o portal do Azure, CLI do Azure e Azure PowerShell. O recurso depende de uma nova superfície de API que, durante a versão prévia, ainda não foi contabilizada em todas as experiências de gerenciamento.

[comment]: <Remove this caution block when V2 becomes available for use.> 

## <a name="create-an-application-in-the-apple-developer-portal"></a><a name="createApplication"> </a>Criar um aplicativo no portal do desenvolvedor da Apple
Você precisará criar uma ID do aplicativo e uma ID de serviço no portal do desenvolvedor da Apple.

1. No portal do desenvolvedor da Apple, acesse **certificados, identificadores, & perfis**.
2. Na guia **identificadores** , selecione o botão **(+)** .
3. Na página **registrar um novo identificador** , escolha **IDs de aplicativo** e selecione **continuar**. (As IDs de aplicativo incluem uma ou mais IDs de serviço.) ![Registrando um novo identificador de aplicativo no portal do desenvolvedor da Apple](media/configure-authentication-provider-apple/apple-register-app.jpg)
4. Na página **registrar uma ID do aplicativo** , forneça uma descrição e uma ID de pacote e selecione **entrar com a Apple** na lista de recursos. Depois selecione **Continuar**. Anote o prefixo da **ID do aplicativo (ID da equipe)** nesta etapa, você precisará dele mais tarde.
![Configurando um novo identificador de aplicativo no portal do desenvolvedor da Apple](media/configure-authentication-provider-apple/apple-configure-app.jpg)
5. Examine as informações de registro do aplicativo e selecione **registrar**.
6. Novamente, na guia **identificadores** , selecione o botão **(+)** .
![Criando um novo identificador de serviço no portal do desenvolvedor da Apple](media/configure-authentication-provider-apple/apple-new-app.jpg) 
7. Na página **registrar um novo identificador** , escolha **Serviços IDs** e selecione **continuar**.
![Registrando um novo identificador de serviço no portal do desenvolvedor da Apple](media/configure-authentication-provider-apple/apple-register-service.jpg)
8. Na página **registrar uma ID de serviços** , forneça uma descrição e um identificador. A descrição é o que será exibido para o usuário na tela de consentimento. O identificador será a sua ID de cliente usada na configuração do provedor da Apple com seu serviço de aplicativo. Em seguida, selecione **Configurar**.
![Fornecendo uma descrição e um identificador](media/configure-authentication-provider-apple/apple-configure-service-1.jpg)
9. Na janela pop-up, defina a ID do aplicativo primário para a ID do aplicativo que você criou anteriormente. Especifique o domínio do aplicativo na seção domínio. Para a URL de retorno, use a URL `<app-url>/.auth/login/apple/callback` . Por exemplo, `https://contoso.azurewebsites.net/.auth/login/apple/callback`. Em seguida, selecione **Adicionar** e **salvar**.
![Especificando o domínio e a URL de retorno para o registro](media/configure-authentication-provider-apple/apple-configure-service-2.jpg)
10. Examine as informações de registro do serviço e selecione **salvar**.

## <a name="generate-the-client-secret"></a><a name="generateClientSecret"> </a>Gerar o segredo do cliente
A Apple exige que os desenvolvedores de aplicativos criem e assinem um token JWT como o valor de segredo do cliente. Para gerar esse segredo, primeiro gere e Baixe uma chave privada de curva elíptica do portal do desenvolvedor da Apple. Em seguida, use essa chave para [assinar um JWT](#sign-the-client-secret-jwt) com uma [carga específica](#structure-the-client-secret-jwt).

### <a name="create-and-download-the-private-key"></a>Criar e baixar a chave privada
1. Na guia **chaves** no portal do desenvolvedor da Apple, escolha **criar uma chave** ou selecione o botão **(+)** .
2. Na página **registrar uma nova chave** , dê um nome à chave, marque a caixa ao lado de **entrar com a Apple** e selecione **Configurar**.
3. Na página **Configurar chave** , vincule a chave à ID do aplicativo primário que você criou anteriormente e selecione **salvar**.
4. Conclua a criação da chave confirmando as informações e selecionando **continuar** e, em seguida, revisar as informações e selecionar **registrar**.
5. Na página **baixar sua chave** , baixe a chave. Ele será baixado como um `.p8` arquivo (PKCS # 8)-você usará o conteúdo do arquivo para assinar o JWT secreto do cliente.

### <a name="structure-the-client-secret-jwt"></a>Estruturar o JWT secreto do cliente
A Apple exige que o segredo do cliente seja a codificação Base64 de um token JWT. O token JWT decodificado deve ter uma carga estruturada como este exemplo:
```json
{
  "alg": "ES256",
  "kid": "URKEYID001",
}.{
  "sub": "com.yourcompany.app1",
  "nbf": 1560203207,
  "exp": 1560289607,
  "iss": "ABC123DEFG",
  "aud": "https://appleid.apple.com"
}.[Signature]
```
- **sub**: a ID do cliente da Apple (também a ID do serviço)
- **ISS**: sua ID da equipe de desenvolvedores da Apple
- **AUD**: a Apple está recebendo o token, portanto, é o público-alvo
- **exp**: não mais do que seis meses após **NBF**

A versão codificada em base64 do conteúdo acima tem esta aparência: ```eyJhbGciOiJFUzI1NiIsImtpZCI6IlVSS0VZSUQwMDEifQ.eyJzdWIiOiJjb20ueW91cmNvbXBhbnkuYXBwMSIsIm5iZiI6MTU2MDIwMzIwNywiZXhwIjoxNTYwMjg5NjA3LCJpc3MiOiJBQkMxMjNERUZHIiwiYXVkIjoiaHR0cHM6Ly9hcHBsZWlkLmFwcGxlLmNvbSJ9.ABSXELWuTbgqfrIUz7bLi6nXvkXAz5O8vt0jB2dSHTQTib1x1DSP4__4UrlKI-pdzNg1sgeocolPNTmDKazO8-BHAZCsdeeTNlgFEzBytIpMKFfVEQbEtGRkam5IeclUK7S9oOva4EK4jV4VmgDrr-LGWWO3TaAxAvy3_ZoKohvFFkVG```

_Observação: a Apple não aceita o JWTs do segredo do cliente com uma data de expiração superior a seis meses após a data de criação (ou NBF). Isso significa que você precisará girar o segredo do cliente, no mínimo, a cada seis meses._

Mais informações sobre como gerar e validar tokens podem ser encontradas na [documentação do desenvolvedor da Apple](https://developer.apple.com/documentation/sign_in_with_apple/generate_and_validate_tokens). 

### <a name="sign-the-client-secret-jwt"></a>Assinar o JWT secreto do cliente
Você usará o `.p8` arquivo baixado anteriormente para assinar o JWT secreto do cliente. Esse arquivo é um [arquivo PCKS n º 8](https://en.wikipedia.org/wiki/PKCS_8) que contém a chave de assinatura privada no formato PEM. Há muitas bibliotecas que podem criar e assinar o JWT para você. 

Há diferentes tipos de bibliotecas de software livre disponíveis online para criar e assinar tokens JWT. Para obter mais informações sobre como gerar tokens JWT, consulte jwt.io. Por exemplo, uma maneira de gerar o segredo do cliente é importando o [pacote NuGet Microsoft. IdentityModel. Tokens](https://www.nuget.org/packages/Microsoft.IdentityModel.Tokens/) e executando uma pequena quantidade de código C# mostrado abaixo.

```csharp
using Microsoft.IdentityModel.Tokens;

public static string GetAppleClientSecret(string teamId, string clientId, string keyId, string p8key)
{
    string audience = "https://appleid.apple.com";

    string issuer = teamId;
    string subject = clientId;
    string kid = keyId;

    IList<Claim> claims = new List<Claim> {
        new Claim ("sub", subject)
    };

    CngKey cngKey = CngKey.Import(Convert.FromBase64String(p8key), CngKeyBlobFormat.Pkcs8PrivateBlob);

    SigningCredentials signingCred = new SigningCredentials(
        new ECDsaSecurityKey(new ECDsaCng(cngKey)),
        SecurityAlgorithms.EcdsaSha256
    );

    JwtSecurityToken token = new JwtSecurityToken(
        issuer,
        audience,
        claims,
        DateTime.Now,
        DateTime.Now.AddDays(180),
        signingCred
    );
    token.Header.Add("kid", kid);
    token.Header.Remove("typ");

    JwtSecurityTokenHandler tokenHandler = new JwtSecurityTokenHandler();

    return tokenHandler.WriteToken(token);
}
```
- **TeamID**: sua ID da equipe de desenvolvedores da Apple
- **clientId**: a ID do cliente da Apple (também a ID do serviço)
- **p8key**: a chave de formato PEM – você pode obter a chave abrindo o `.p8` arquivo em um editor de texto e copiando tudo entre `-----BEGIN PRIVATE KEY-----` e `-----END PRIVATE KEY-----` sem quebras de linha
- **keyId**: a ID da chave baixada

Esse token retornado é o valor de segredo do cliente que você usará para configurar o provedor da Apple.

> [!IMPORTANT]
> O segredo do cliente é uma credencial de segurança importante. Não compartilhe essa senha com ninguém nem distribua-a em um aplicativo cliente.
>

Adicione o segredo do cliente como uma [configuração de aplicativo](./configure-common.md#configure-app-settings) para o aplicativo, usando um nome de configuração de sua escolha. Anote esse nome para mais tarde.

## <a name="add-provider-information-to-your-application"></a><a name="configure"> </a>Adicionar informações de provedor ao seu aplicativo

> [!NOTE]
> A configuração necessária está em um novo formato de API, atualmente só tem suporte da [Configuração baseada em arquivo (versão prévia)](.\app-service-authentication-how-to.md#config-file). Você precisará seguir as etapas a seguir usando esse arquivo.

Esta seção explicará como atualizar a configuração para incluir o novo IDP. Segue um exemplo de configuração.

1. No `identityProviders` objeto, adicione um `apple` objeto se ele ainda não existir.
2. Atribua um objeto a essa chave com um `registration` objeto dentro dele e, opcionalmente, um `login` objeto:
    
    ```json
    "apple" : {
       "registration" : {
            "clientId": "<client id>",
            "clientSecretSettingName": "APP_SETTING_CONTAINING_APPLE_CLIENT_SECRET" 
        },
       "login": {
             "scopes": []
       }
    }
    ```
    a. No `registration` objeto, defina como a `clientId` ID do cliente que você coletou.
    
    b. Dentro do `registration` objeto, defina `clientSecretSettingName` como o nome da configuração do aplicativo em que você armazenou o segredo do cliente.
    
    c. Dentro do `login` objeto, você pode optar por definir a `scopes` matriz para incluir uma lista de escopos usados ao autenticar com a Apple, como "nome" e "email". Se os escopos estiverem configurados, eles serão explicitamente solicitados na tela de consentimento quando os usuários entrarem pela primeira vez.

Depois que essa configuração tiver sido definida, você estará pronto para usar seu provedor Apple para autenticação em seu aplicativo.

Uma configuração completa pode ser semelhante ao exemplo a seguir (em que a configuração APPLE_GENERATED_CLIENT_SECRET aponta para uma configuração de aplicativo que contém um JWT gerado):

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
        "apple": {
            "registration": {
                "clientId": "com.contoso.example.client",
                "clientSecretSettingName": "APPLE_GENERATED_CLIENT_SECRET"
            },
            "login": {
                "scopes": []
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
