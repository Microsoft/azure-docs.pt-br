---
title: Usar o MSAL em um aplicativo de nuvem nacional | Azure
titleSuffix: Microsoft identity platform
description: A MSAL (biblioteca de autenticação da Microsoft) permite que os desenvolvedores de aplicativos adquiram tokens para chamar APIs da Web protegidas. Essas APIs da Web podem ser Microsoft Graph, outras APIs da Microsoft, APIs Web de parceiros ou sua própria API Web. A MSAL dá suporte a várias arquiteturas de aplicativos e plataformas.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/22/2019
ms.author: negoe
ms.reviewer: marsma, nacanuma
ms.custom: aaddev
ms.openlocfilehash: 09c4dadd7a6560bd5163d623dd8a7f247b57860e
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100102488"
---
# <a name="use-msal-in-a-national-cloud-environment"></a>Usar o MSAL em um ambiente de nuvem nacional

As [nuvens nacionais](authentication-national-cloud.md), também conhecidas como nuvens soberanas, são instâncias fisicamente isoladas do Azure. Essas regiões do Azure ajudam a garantir que os requisitos de residência de dados, soberania e conformidade sejam respeitados dentro dos limites geográficos.

Além da nuvem Mundial da Microsoft, a MSAL (biblioteca de autenticação da Microsoft) permite que os desenvolvedores de aplicativos em nuvens nacionais adquiram tokens para autenticar e chamar APIs da Web protegidas. Essas APIs da Web podem ser Microsoft Graph ou outras APIs da Microsoft.

Incluindo a nuvem global, o Azure Active Directory (Azure AD) é implantado nas seguintes nuvens nacionais:  

- Azure Government
- Azure China 21Vianet
- Azure Alemanha

Este guia demonstra como entrar em contas corporativas e de estudante, obter um token de acesso e chamar a API de Microsoft Graph no ambiente de [nuvem do Azure governamental](https://azure.microsoft.com/global-infrastructure/government/) .

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de atender a esses pré-requisitos.

### <a name="choose-the-appropriate-identities"></a>Escolha as identidades apropriadas

Os aplicativos [do Azure governamental](../../azure-government/index.yml) podem usar identidades do Azure ad governamental e identidades públicas do Azure ad para autenticar usuários. Como você pode usar qualquer uma dessas identidades, decida qual ponto de extremidade de autoridade você deve escolher para seu cenário:

- Público do Azure AD: normalmente usado se sua organização já tiver um locatário público do Azure AD para dar suporte a Microsoft 365 (pública ou GCC) ou a outro aplicativo.
- Azure AD governamental: normalmente usado se sua organização já tiver um locatário do Azure AD governamental para dar suporte ao Office 365 (GCC High ou DoD) ou estiver criando um novo locatário no Azure AD governamental.

Depois de decidir, uma consideração especial é onde você executa o registro do aplicativo. Se você escolher identidades públicas do Azure AD para seu aplicativo do Azure governamental, deverá registrar o aplicativo em seu locatário público do Azure AD.

### <a name="get-an-azure-government-subscription"></a>Obter uma assinatura do Azure governamental

Para obter uma assinatura do Azure governamental, consulte [Gerenciando e conectando-se à sua assinatura no Azure governamental](../../azure-government/compare-azure-government-global-azure.md).

Se você não tiver uma assinatura do Azure governamental, crie uma [conta gratuita](https://azure.microsoft.com/global-infrastructure/government/request/) antes de começar.

Para obter detalhes sobre como usar uma nuvem nacional com uma linguagem de programação específica, escolha a guia correspondente ao seu idioma:

## <a name="net"></a>[.NET](#tab/dotnet)

Você pode usar o MSAL.NET para conectar usuários, adquirir tokens e chamar a API Microsoft Graph em nuvens nacionais.

Os tutoriais a seguir demonstram como criar um aplicativo Web MVC do .NET Core 2,2. O aplicativo usa o OpenID Connect para conectar usuários com uma conta corporativa e de estudante em uma organização que pertence a uma nuvem nacional.

- Para conectar usuários e adquirir tokens, siga este tutorial: [criar um ASP.NET Core usuários de assinatura de aplicativo Web em nuvens soberanas com a plataforma Microsoft Identity](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-4-Sovereign#build-an-aspnet-core-web-app-signing-in-users-in-sovereign-clouds-with-the-microsoft-identity-platform).
- Para chamar a API de Microsoft Graph, siga este tutorial: [usando a plataforma de identidade da Microsoft para chamar a API de Microsoft Graph de um aplicativo web ASP.NET Core 2. x, em nome de uma entrada de usuário usando sua conta corporativa e de estudante na nuvem nacional da Microsoft](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-4-Sovereign-Call-MSGraph#using-the-microsoft-identity-platform-to-call-the-microsoft-graph-api-from-an-an-aspnet-core-2x-web-app-on-behalf-of-a-user-signing-in-using-their-work-and-school-account-in-microsoft-national-cloud).

## <a name="javascript"></a>[JavaScript](#tab/javascript)

Para habilitar seu aplicativo MSAL.js para nuvens soberanas:

### <a name="step-1-register-your-application"></a>Etapa 1: Registre seu aplicativo

1. Entre no <a href="https://portal.azure.us/" target="_blank">portal do Azure</a>.

   Para localizar portal do Azure pontos de extremidade para outras nuvens nacionais, consulte [pontos de extremidade de registro de aplicativo](authentication-national-cloud.md#app-registration-endpoints).

1. Se você tem acesso a vários locatários, use o filtro **Diretório + assinatura** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o locatário no qual você deseja registrar um aplicativo.
1. Pesquise **Azure Active Directory** e selecione-o.
1. Em **Gerenciar**, selecione **Registros de aplicativo** > **Novo registro**.
1. Insira um **Nome** para seu aplicativo. Os usuários do seu aplicativo podem ver esse nome e você pode alterá-lo mais tarde.
1. Em **Tipos de conta com suporte**, selecione **Contas em qualquer diretório organizacional**.
1. Na seção **URI de redirecionamento** , selecione a plataforma **da Web** e defina o valor como a URL do aplicativo com base no servidor Web. Consulte as próximas seções para obter instruções sobre como definir e obter a URL de redirecionamento no Visual Studio e no nó.
1. Selecione **Registrar**.
1. Na página **visão geral** , anote o valor da **ID do aplicativo (cliente)** para uso posterior.
    Este tutorial requer que você habilite o [fluxo de concessão implícito](v2-oauth2-implicit-grant-flow.md). 
1. Em **Gerenciar**, selecione **Autenticação**.
1. Na **concessão implícita e em fluxos híbridos**, selecione **tokens de ID** e **tokens de acesso**. Tokens de ID e tokens de acesso são necessários porque esse aplicativo precisa conectar usuários e chamar uma API.
1. Clique em **Salvar**.

### <a name="step-2--set-up-your-web-server-or-project"></a>Etapa 2: configurar seu servidor Web ou projeto

- [Baixe os arquivos de projeto](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip) para um servidor Web local, como node.

  ou

- [Baixe o projeto do Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip).

Em seguida, pule para [Configurar o seu JavaScript Spa](#step-4-configure-your-javascript-spa) para configurar o exemplo de código antes de executá-lo.

### <a name="step-3-use-the-microsoft-authentication-library-to-sign-in-the-user"></a>Etapa 3: usar a biblioteca de autenticação da Microsoft para conectar o usuário

Siga as etapas no [tutorial do JavaScript](tutorial-v2-javascript-spa.md#create-your-project) para criar seu projeto e integrá-lo ao MSAL para conectar o usuário.

### <a name="step-4-configure-your-javascript-spa"></a>Etapa 4: configurar seu JavaScript SPA

No arquivo `index.html` criado durante a configuração do projeto, inclua as informações de registro do aplicativo. Adicione o seguinte código na parte superior dentro das marcas `<script></script>` no corpo do arquivo `index.html`:

```javascript
const msalConfig = {
    auth:{
        clientId: "Enter_the_Application_Id_here",
        authority: "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here",
        }
}

const graphConfig = {
        graphEndpoint: "https://graph.microsoft.us",
        graphScopes: ["user.read"],
}

// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(msalConfig);
```

Nesse código:

- `Enter_the_Application_Id_here` é o valor da **ID do aplicativo (cliente)** para o aplicativo que você registrou.
- `Enter_the_Tenant_Info_Here` é definido como uma das seguintes opções:
    - Se seu aplicativo der suporte a **contas nesse diretório organizacional**, substitua esse valor pela ID do locatário ou pelo nome do locatário (por exemplo, contoso.Microsoft.com).
    - Se seu aplicativo oferecer suporte a **contas em qualquer diretório organizacional**, substitua esse valor por `organizations` .

    Para localizar pontos de extremidade de autenticação para todas as nuvens nacionais, consulte [pontos de extremidade de autenticação do Azure ad](./authentication-national-cloud.md#azure-ad-authentication-endpoints).

    > [!NOTE]
    > Não há suporte para contas pessoais da Microsoft em nuvens nacionais.

- `graphEndpoint` é o ponto de extremidade Microsoft Graph para a nuvem da Microsoft para o governo dos EUA.

   Para localizar Microsoft Graph pontos de extremidade para todas as nuvens nacionais, confira [Microsoft Graph pontos de extremidade em nuvens nacionais](/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints).

## <a name="python"></a>[Python](#tab/python)

Para habilitar seu aplicativo MSAL Python para nuvens soberanas:

- Registre seu aplicativo em um portal específico, dependendo da nuvem. Para obter mais informações sobre como escolher o portal, consulte [pontos de extremidade de registro de aplicativo](authentication-national-cloud.md#app-registration-endpoints)
- Use qualquer um dos [exemplos](https://github.com/AzureAD/microsoft-authentication-library-for-python/tree/dev/sample) do repositório com algumas alterações na configuração, dependendo da nuvem, que é mencionada a seguir.
- Use uma autoridade específica, dependendo da nuvem em que você registrou o aplicativo. Para obter mais informações sobre autoridades para nuvens diferentes, consulte [pontos de extremidade de autenticação do Azure ad](authentication-national-cloud.md#azure-ad-authentication-endpoints).

    Aqui está uma autoridade de exemplo:

    ```json
    "authority": "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here"
    ```

- Chamar a API de Microsoft Graph requer uma URL de ponto de extremidade específica para a nuvem que você está usando. Para localizar Microsoft Graph pontos de extremidade para todas as nuvens nacionais, consulte [Microsoft Graph e pontos de extremidade raiz de serviço do Gerenciador de gráficos](/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints).

    Aqui está um exemplo de um ponto de extremidade Microsoft Graph, com escopo:

    ```json
    "endpoint" : "https://graph.microsoft.us/v1.0/me"
    "scope": "User.Read"
    ```

## <a name="java"></a>[Java](#tab/java)

Para habilitar seu MSAL para aplicativo Java para nuvens soberanas:

- Registre seu aplicativo em um portal específico, dependendo da nuvem. Para obter mais informações sobre como escolher o portal, consulte [pontos de extremidade de registro de aplicativo](authentication-national-cloud.md#app-registration-endpoints)
- Use qualquer um dos [exemplos](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples) do repositório com algumas alterações na configuração, dependendo da nuvem, que são mencionadas a seguir.
- Use uma autoridade específica, dependendo da nuvem em que você registrou o aplicativo. Para obter mais informações sobre autoridades para nuvens diferentes, consulte [pontos de extremidade de autenticação do Azure ad](authentication-national-cloud.md#azure-ad-authentication-endpoints).

Aqui está uma autoridade de exemplo:

```json
"authority": "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here"
```

- Chamar a API de Microsoft Graph requer uma URL de ponto de extremidade específica para a nuvem que você está usando. Para localizar Microsoft Graph pontos de extremidade para todas as nuvens nacionais, consulte [Microsoft Graph e pontos de extremidade raiz de serviço do Gerenciador de gráficos](/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints).

Aqui está um exemplo de um ponto de extremidade de grafo, com escopo:

```json
"endpoint" : "https://graph.microsoft.us/v1.0/me"
"scope": "User.Read"
```

## <a name="objective-c"></a>[Objective-C](#tab/objc)

O MSAL para iOS e macOS pode ser usado para adquirir tokens em nuvens nacionais, mas requer configuração adicional ao criar `MSALPublicClientApplication` .

Por exemplo, se você quiser que seu aplicativo seja um aplicativo multilocatário em uma nuvem nacional (aqui, o governo dos EUA), você poderia escrever:

```objc
MSALAADAuthority *aadAuthority =
                [[MSALAADAuthority alloc] initWithCloudInstance:MSALAzureUsGovernmentCloudInstance
                                                   audienceType:MSALAzureADMultipleOrgsAudience
                                                      rawTenant:nil
                                                          error:nil];

MSALPublicClientApplicationConfig *config =
                [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"
                                                                redirectUri:@"<your-redirect-uri-here>"
                                                                  authority:aadAuthority];

NSError *applicationError = nil;
MSALPublicClientApplication *application =
                [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&applicationError];
```

## <a name="swift"></a>[Swift](#tab/swift)

O MSAL para iOS e macOS pode ser usado para adquirir tokens em nuvens nacionais, mas requer configuração adicional ao criar `MSALPublicClientApplication` .

Por exemplo, se você quiser que seu aplicativo seja um aplicativo multilocatário em uma nuvem nacional (aqui, o governo dos EUA), você poderia escrever:

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)

let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```

---

## <a name="next-steps"></a>Próximas etapas

Consulte [pontos de extremidade de autenticação de nuvem nacional](authentication-national-cloud.md) para obter uma lista de URLs de portal do Azure e pontos de extremidade de token para cada nuvem.

Documentação da nuvem nacional:

- [Azure Governamental](../../azure-government/index.yml)
- [Azure China 21Vianet](/azure/china/)
- [Azure Alemanha](../../germany/index.yml)