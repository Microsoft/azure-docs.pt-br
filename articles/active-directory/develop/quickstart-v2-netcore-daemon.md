---
title: 'Início Rápido: Obter token e chamar o Microsoft Graph em um aplicativo de console | Azure'
titleSuffix: Microsoft identity platform
description: Neste início rápido, você verá como um aplicativo de exemplo do .NET Core pode usar o fluxo de credenciais do cliente para obter um token e chamar o Microsoft Graph.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/05/2020
ms.author: jmprieur
ms.reviewer: marsma
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: 99dcd81cd24f762a5c2b55f5f2977aaf61bc26e8
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100103529"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-using-console-apps-identity"></a>Início Rápido: Adquirir um token e chamar a API do Microsoft Graph usando a identidade de aplicativo do console

Neste guia de início rápido, você baixará e executará um exemplo de código que demonstra como um aplicativo de console .NET Core pode obter um token de acesso para chamar a API do Microsoft Graph e exibir uma [lista de usuários](/graph/api/user-list) no diretório. O exemplo de código também demonstra como um trabalho ou um serviço Windows pode ser executado com uma identidade de aplicativo, em vez de uma identidade do usuário. 

Confira [Como o exemplo funciona](#how-the-sample-works) para ver uma ilustração.

## <a name="prerequisites"></a>Pré-requisitos

Este guia de início rápido exige o [.NET Core 3.1](https://www.microsoft.com/net/download/dotnet-core).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrar e baixar o aplicativo de início rápido

> [!div renderon="docs" class="sxs-lookup"]
>
> Você tem duas opções para iniciar o aplicativo de início rápido: Expressa (Opção 1 abaixo) e Manual (Opção 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opção 1: Registrar e configurar o aplicativo automaticamente e, em seguida, baixar seu exemplo de código
>
> 1. Acesse a experiência de início rápido do <a href="https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/DotNetCoreDaemonQuickstartPage/sourceType/docs" target="_blank">portal do Azure – Registros de aplicativo</a>.
> 1. Insira um nome para seu aplicativo e selecione **Registrar**.
> 1. Siga as instruções para baixar e configurar automaticamente o novo aplicativo com apenas um clique.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opção 2: Registrar e configurar manualmente o aplicativo e o exemplo de código

> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Etapa 1: Registre seu aplicativo
> Para registrar seu aplicativo e adicionar as informações de registro do aplicativo à solução manualmente, siga estas etapas:
>
> 1. Entre no <a href="https://portal.azure.com/" target="_blank">Portal do Azure</span></a>.
> 1. Se você tem acesso a vários locatários, use o filtro **Diretório + assinatura** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o locatário no qual você deseja registrar um aplicativo.
> 1. Pesquise **Azure Active Directory** e selecione-o.
> 1. Em **Gerenciar**, selecione **Registros de aplicativo** > **Novo registro**.
> 1. Insira um **Nome** para seu aplicativo, por exemplo, `Daemon-console`. Os usuários do seu aplicativo podem ver esse nome e você pode alterá-lo mais tarde.
> 1. Selecione **Registrar** para criar o aplicativo.
> 1. Em **Gerenciar**, selecione **Certificados e Segredos**.
> 1. Em **Segredos do cliente**, selecione **Novo segredo do cliente**, insira um nome e selecione **Adicionar**. Registre o valor secreto em uma localização segura para uso em uma etapa posterior.
> 1. Em **Gerenciar**, selecione **Permissões de API** > **Adicionar uma permissão**. Selecione **Microsoft Graph**.
> 1. Selecione **Permissões de aplicativo**.
> 1. No nó **Usuário**, selecione **User.Read.All** e selecione **Adicionar permissões**.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-your-quickstart-app"></a>Baixar e configurar seu aplicativo de início rápido
>
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Etapa 1: Configurar seu aplicativo no portal do Azure
> Para que o exemplo de código deste guia de início rápido funcione, crie um segredo do cliente e adicione a permissão de aplicativo **User.Read.All** da API do Graph.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Fazer essas alterações para mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurado](media/quickstart-v2-netcore-daemon/green-check.png) Seu aplicativo já está configurado com esses atributos.

#### <a name="step-2-download-your-visual-studio-project"></a>Etapa 2: Baixar seu projeto do Visual Studio

> [!div renderon="docs"]
> [Baixar o projeto do Visual Studio](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/archive/master.zip)
>
> Execute o projeto fornecido no Visual Studio ou no Visual Studio para Mac.


> [!div class="sxs-lookup" renderon="portal"]
> Execute o projeto usando o Visual Studio 2019.
> [!div class="sxs-lookup" renderon="portal" id="autoupdate" class="nextstepaction"]
> [Baixe o exemplo de código](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-visual-studio-project"></a>Etapa 3: Configurar o projeto do Visual Studio
>
> 1. Extraia o arquivo zip para uma pasta local mais próxima da raiz do disco, por exemplo, **C:\Azure-Samples**.
> 1. Abra a solução no Visual Studio – **1-Call-MSGraph\daemon-console.sln** (opcional).
> 1. Edite **appsettings.json** e substitua os valores dos campos `ClientId`, `Tenant` e `ClientSecret` pelo seguinte:
>
>    ```json
>    "Tenant": "Enter_the_Tenant_Id_Here",
>    "ClientId": "Enter_the_Application_Id_Here",
>    "ClientSecret": "Enter_the_Client_Secret_Here"
>    ```
>   Em que:
>   - `Enter_the_Application_Id_Here` - é a **ID do aplicativo (cliente)** que você registrou.
>   - `Enter_the_Tenant_Id_Here` – substitua esse valor pela **ID do locatário** ou pelo **Nome do locatário** (por exemplo, contoso.microsoft.com)
>   - `Enter_the_Client_Secret_Here` – substitua esse valor pelo segredo do cliente criado na etapa 1.

> [!div renderon="docs"]
> > [!TIP]
> > Para encontrar os valores de **ID do aplicativo (cliente)** , **ID de diretório (locatário)** , acesse a página **Visão Geral** do aplicativo no portal do Azure. Para gerar uma nova chave, acesse a página **Certificados e segredos**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-admin-consent"></a>Etapa 3: Consentimento do administrador

> [!div renderon="docs"]
> #### <a name="step-4-admin-consent"></a>Etapa 4: Consentimento do administrador

Se você tentar executar o aplicativo neste ponto, receberá o erro *HTTP 403 – Proibido*: `Insufficient privileges to complete the operation`. Isso acontece porque qualquer *permissão somente do aplicativo* exige o consentimento do administrador, ou seja, um administrador global do seu diretório precisa dar consentimento ao seu aplicativo. Selecione uma das opções abaixo, dependendo de sua função:

##### <a name="global-tenant-administrator"></a>Administrator de locatário global

> [!div renderon="docs"]
> Se você for um administrador de locatários global, no portal do Azure, navegue até **Aplicativos empresariais** > Selecione seu registro de aplicativo > escolha **"Permissões"** na seção Segurança do painel de navegação esquerdo. Selecione o botão grande rotulado **Conceder consentimento do administrador para {Nome do Locatário}** (em que {Nome do Locatário} é o nome do seu diretório).

> [!div renderon="portal" class="sxs-lookup"]
> Se você for um administrador global, acesse a página **Permissões de API**, selecione **Dar consentimento de administrador para Insira_o_nome_do_locatário_aqui**
> > [!div id="apipermissionspage"]
> > [Acesse a página Permissões de API]()

##### <a name="standard-user"></a>Usuário padrão

Se você é um usuário padrão do seu locatário, solicite ao Administrador global que forneça consentimento do administrador para seu aplicativo. Para fazer isso, dê a seguinte URL ao administrador:

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
>> Em que:
>> * `Enter_the_Tenant_Id_Here` – substitua esse valor pela **ID do locatário** ou pelo **Nome do locatário** (por exemplo, contoso.microsoft.com)
>> * `Enter_the_Application_Id_Here` - é a **ID do aplicativo (cliente)** que você registrou.

> [!NOTE]
> Você pode ver o erro *'AADSTS50011: nenhum endereço de resposta está registrado para o aplicativo'* após dar o consentimento ao aplicativo usando a URL anterior. Isso ocorre porque esse aplicativo e a URL não têm um URI de redirecionamento; ignore o erro.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>Etapa 4: Executar o aplicativo

> [!div renderon="docs"]
> #### <a name="step-5-run-the-application"></a>Etapa 5: Executar o aplicativo

Se estiver usando o Visual Studio ou o Visual Studio para Mac, clique em **F5** para executar o aplicativo. Caso contrário, execute o aplicativo por meio do prompt de comando, do console ou do terminal:

```dotnetcli
cd {ProjectFolder}\1-Call-MSGraph\daemon-console
dotnet run
```

> Em que:
> * *{ProjectFolder}* é a pasta onde você extraiu o arquivo zip. Exemplo **C:\Azure-Samples\active-directory-dotnetcore-daemon-v2**

Você deverá ver uma lista de usuários em seu diretório do Azure AD como resultado.

> [!IMPORTANT]
> Este aplicativo de início rápido usa um segredo do cliente para se identificar como cliente confidencial. Como o segredo do cliente é adicionado como texto sem formatação a seus arquivos de projeto, por motivos de segurança, é recomendável que você use um certificado, em vez de um segredo do cliente, antes de considerar o aplicativo como aplicativo de produção. Para obter mais informações sobre como usar um certificado, confira [estas instruções](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/#variation-daemon-application-using-client-credentials-with-certificates) no repositório do GitHub para este exemplo.

## <a name="more-information"></a>Mais informações

### <a name="how-the-sample-works"></a>Como o exemplo funciona
![Mostra como o aplicativo de exemplo gerado por este início rápido funciona](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)

### <a name="msalnet"></a>MSAL.NET

MSAL ([Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)) é a biblioteca usada para conectar usuários e solicitar tokens usados para acessar uma API protegida pela plataforma de identidade da Microsoft. Conforme descrito, este início rápido solicita tokens usando a identidade do próprio aplicativo, em vez de permissões delegadas. O fluxo de autenticação usado nesse caso é conhecido como *[fluxo OAuth de credenciais do cliente](v2-oauth2-client-creds-grant-flow.md)* . Para saber mais sobre como usar MSAL.NET com fluxo de credenciais de cliente, confira [este artigo](https://aka.ms/msal-net-client-credentials).

 Você pode instalar o MSAL.NET executando o comando abaixo no **Console do Gerenciador de Pacotes** do Visual Studio:

```dotnetcli
dotnet add package Microsoft.Identity.Client
```

### <a name="msal-initialization"></a>Inicialização da MSAL

Você pode adicionar a referência da MSAL adicionando o seguinte código:

```csharp
using Microsoft.Identity.Client;
```

Em seguida, inicialize a MSAL usando o seguinte código:

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientSecret(config.ClientSecret)
                                          .WithAuthority(new Uri(config.Authority))
                                          .Build();
```

> | Em que: | Descrição |
> |---------|---------|
> | `config.ClientSecret` | É o segredo do cliente criado para o aplicativo no portal do Azure. |
> | `config.ClientId` | É a **ID do aplicativo (cliente)** relativa ao aplicativo registrado no portal do Azure. Você pode encontrar esse valor na página **Visão Geral** do aplicativo no portal do Azure. |
> | `config.Authority`    | (Opcional) O ponto de extremidade do STS para o usuário autenticar. Normalmente, `https://login.microsoftonline.com/{tenant}` para a nuvem pública, em que {tenant} é o nome do seu locatário ou ID do seu locatário.|

Para saber mais, confira a [documentação de referência do `ConfidentialClientApplication`](/dotnet/api/microsoft.identity.client.iconfidentialclientapplication)

### <a name="requesting-tokens"></a>Solicitando tokens

Para solicitar um token usando a identidade do aplicativo, use o método `AcquireTokenForClient`:

```csharp
result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
```

> |Em que:| Descrição |
> |---------|---------|
> | `scopes` | Contém os escopos solicitados. Para clientes confidenciais, ele deve usar um formato semelhante a `{Application ID URI}/.default` para indicar que os escopos solicitados são os estaticamente definidos no objeto de aplicativo definido no portal do Azure (no caso do Microsoft Graph, `{Application ID URI}` aponta para `https://graph.microsoft.com`). Para APIs Web personalizadas, o `{Application ID URI}` é definido na seção **Expor uma API** no Registro de Aplicativo do portal do Azure (versão prévia). |

Para saber mais, confira a [documentação de referência do `AcquireTokenForClient`](/dotnet/api/microsoft.identity.client.confidentialclientapplication.acquiretokenforclient)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre aplicativos daemon, confira a visão geral do cenário:

> [!div class="nextstepaction"]
> [Aplicativo daemon que chama APIs Web](scenario-daemon-overview.md)
