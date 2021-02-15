---
title: Migrar APIs Web baseadas em OWIN para b2clogin.com
titleSuffix: Azure AD B2C
description: Saiba como habilitar uma API Web .NET para dar suporte a tokens emitidos por vários emissores de token enquanto você migra seus aplicativos para o b2clogin.com.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/31/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c362ce256259606c85af0a7e13ccde1715bb012b
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94953926"
---
# <a name="migrate-an-owin-based-web-api-to-b2clogincom"></a>Migrar uma API Web baseada em OWIN para b2clogin.com

Este artigo descreve uma técnica para habilitar o suporte a vários emissores de token em APIs Web que implementam a [interface da Web aberta para .net (OWIN)](http://owin.org/). O suporte a vários pontos de extremidade de token é útil quando você está migrando APIs de Azure Active Directory B2C (Azure AD B2C) e seus aplicativos de *login.microsoftonline.com* para *b2clogin.com*.

Ao adicionar suporte em sua API para aceitar tokens emitidos por b2clogin.com e login.microsoftonline.com, você pode migrar seus aplicativos Web de uma maneira preparada antes de remover o suporte para tokens emitidos por login.microsoftonline.com da API.

As seções a seguir apresentam um exemplo de como habilitar vários emissores em uma API Web que usa os componentes do [Microsoft OWIN][katana] middleware (Katana). Embora os exemplos de código sejam específicos para o middleware Microsoft OWIN, a técnica geral deve ser aplicável a outras bibliotecas OWIN.

> [!NOTE]
> Este artigo destina-se a Azure AD B2C clientes com APIs e aplicativos atualmente implantados que fazem referência `login.microsoftonline.com` e que desejam migrar para o `b2clogin.com` ponto de extremidade recomendado. Se você estiver configurando um novo aplicativo, use [b2clogin.com](b2clogin.md) como indicado.

## <a name="prerequisites"></a>Pré-requisitos

Você precisa dos seguintes recursos de Azure AD B2C em vigor antes de continuar com as etapas neste artigo:

* [Fluxos de usuário](tutorial-create-user-flows.md) ou [políticas personalizadas](custom-policy-get-started.md) criadas em seu locatário

## <a name="get-token-issuer-endpoints"></a>Obter pontos de extremidade do emissor do token

Primeiro, você precisa obter os URIs do ponto de extremidade do emissor do token para cada emissor para o qual você deseja dar suporte em sua API. Para obter os pontos de extremidade *b2clogin.com* e *login.microsoftonline.com* com suporte pelo seu locatário Azure ad B2C, use o procedimento a seguir no portal do Azure.

Comece selecionando um dos fluxos de usuário existentes:

1. Navegue até seu locatário do Azure AD B2C no [portal do Azure](https://portal.azure.com)
1. Em **políticas**, selecione **fluxos de usuário (políticas)**
1. Selecione uma política existente, por exemplo *B2C_1_signupsignin1*, em seguida, selecione **executar fluxo de usuário**
1. No cabeçalho **executar fluxo de usuário** próximo à parte superior da página, selecione o hiperlink para navegar até o ponto de extremidade de descoberta do OpenID Connect para esse fluxo de usuário.

    ![Hiperlink de URI conhecido na página Executar agora do portal do Azure](media/multi-token-endpoints/portal-01-policy-link.png)

1. Na página que é aberta no navegador, registre o valor `issuer`, por exemplo:

    `https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

1. Use a lista suspensa **selecionar domínio** para selecionar o outro domínio e, em seguida, execute as duas etapas anteriores novamente e Registre seu `issuer` valor.

Agora você deve ter dois URIs registrados que são semelhantes a:

```
https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
```

### <a name="custom-policies"></a>Políticas personalizadas

Se você tiver políticas personalizadas em vez de fluxos de usuário, poderá usar um processo semelhante para obter os URIs do emissor.

1. Navegue até seu locatário do Azure AD B2C
1. Selecionar **estrutura de experiência de identidade**
1. Selecione uma das políticas de terceira parte confiável, por exemplo, *B2C_1A_signup_signin*
1. Use a lista suspensa **selecionar domínio** para selecionar um domínio, por exemplo, *yourtenant.b2clogin.com*
1. Selecione o hiperlink exibido em **ponto de extremidade de descoberta do OpenID Connect**
1. Registrar o `issuer` valor
1. Execute as etapas 4-6 para o outro domínio, por exemplo, *login.microsoftonline.com*

## <a name="get-the-sample-code"></a>Obter o código de amostra

Agora que você tem os dois URIs de ponto de extremidade de token, você precisa atualizar seu código para especificar que ambos os pontos de extremidades sejam emissores válidos. Para percorrer um exemplo, baixe ou clone o aplicativo de exemplo e, em seguida, atualize o exemplo para dar suporte a ambos os pontos de extremidade como emissores válidos.

Baixe o arquivo morto: [active-directory-b2c-dotnet-webapp-and-webapi-master.zip][sample-archive]

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

## <a name="enable-multiple-issuers-in-web-api"></a>Habilitar vários emissores na API Web

Nesta seção, você atualiza o código para especificar que ambos os pontos de extremidade do emissor do token sejam válidos.

1. Abra a solução **B2C-WebAPI-dotnet. sln** no Visual Studio
1. No projeto **TaskService** , abra o arquivo * TaskService \\ App_Start \\ **Startup.auth.cs** _ em seu editor
1. Adicione a seguinte `using` diretiva à parte superior do arquivo:

    `using System.Collections.Generic;`
1. Adicione a [`ValidIssuers`][validissuers] Propriedade à [`TokenValidationParameters`][tokenvalidationparameters] definição e especifique os URIs que você registrou na seção anterior:

    ```csharp
    TokenValidationParameters tvps = new TokenValidationParameters
    {
        // Accept only those tokens where the audience of the token is equal to the client ID of this app
        ValidAudience = ClientId,
        AuthenticationType = Startup.DefaultPolicy,
        ValidIssuers = new List<string> {
            "https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/",
            "https://{your-b2c-tenant}.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/"
        }
    };
    ```

`TokenValidationParameters` é fornecido pelo MSAL.NET e é consumido pelo middleware OWIN na próxima seção do código em _Startup. auth. cs *. Com vários emissores válidos especificados, o pipeline de aplicativo do OWIN é informado de que os dois pontos de extremidade do token são emissores válidos.

```csharp
app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
{
    // This SecurityTokenProvider fetches the Azure AD B2C metadata &  from the OpenID Connect metadata endpoint
    AccessTokenFormat = new JwtFormat(tvps, new tCachingSecurityTokenProvider(String.Format(AadInstance, ultPolicy)))
});
```

Como mencionado anteriormente, outras bibliotecas OWIN geralmente fornecem um recurso semelhante para dar suporte a vários emissores. Embora fornecer exemplos para cada biblioteca esteja fora do escopo deste artigo, você pode usar uma técnica semelhante para a maioria das bibliotecas.

## <a name="switch-endpoints-in-web-app"></a>Alternar pontos de extremidade no aplicativo Web

Agora, com os dois URIs compatíveis com sua API Web, você precisa atualizar seu aplicativo Web para que ele recupere tokens do ponto de extremidade b2clogin.com.

Por exemplo, você pode configurar o aplicativo Web de exemplo para usar o novo ponto de extremidade modificando o `ida:AadInstance` valor no *arquivo TaskWebApp \\ **Web.config** _ do projeto _* TaskWebApp * *.

Altere o `ida:AadInstance` valor na *Web.config* de TaskWebApp para que ele faça referência `{your-b2c-tenant-name}.b2clogin.com` em vez de `login.microsoftonline.com` .

Antes:

```xml
<!-- Old value -->
<add key="ida:AadInstance" value="https://login.microsoftonline.com/tfp/{0}/{1}" />
```

Depois (substitua `{your-b2c-tenant}` pelo nome do seu locatário B2C):

```xml
<!-- New value -->
<add key="ida:AadInstance" value="https://{your-b2c-tenant}.b2clogin.com/tfp/{0}/{1}" />
```

Quando as cadeias de caracteres de ponto de extremidade são construídas durante a execução do aplicativo Web, os pontos de extremidades baseados em b2clogin.com são usados ao solicitar tokens.

## <a name="next-steps"></a>Próximas etapas

Este artigo apresentou um método de configuração de uma API Web que implementa o Katana (Microsoft OWIN middleware) para aceitar tokens de vários pontos de extremidade do emissor. Como você pode observar, há várias outras cadeias de caracteres no *Web.Config* arquivos dos projetos TaskService e TaskWebApp que precisariam ser alterados se você quiser compilar e executar esses projetos em seu próprio locatário. Você é bem-vindo a modificar os projetos adequadamente se quiser vê-los em ação. no entanto, um passo a passo completo de fazer isso está fora do escopo deste artigo.

Para obter mais informações sobre os diferentes tipos de tokens de segurança emitidos por Azure AD B2C, consulte [visão geral de tokens no Azure Active Directory B2C](tokens-overview.md).

<!-- LINKS - External -->
[sample-archive]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip
[sample-repo]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi

<!-- LINKS - Internal -->
[katana]: /aspnet/aspnet/overview/owin-and-katana/
[validissuers]: /dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters.validissuers
[tokenvalidationparameters]: /dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters