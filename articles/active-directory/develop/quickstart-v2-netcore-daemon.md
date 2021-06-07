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
ms.openlocfilehash: 1b539c168deab7c1893f071a2453be28310fc132
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105022918"
---
# <a name="quickstart-get-a-token-and-call-the-microsoft-graph-api-by-using-a-console-apps-identity"></a>Guia de início rápido: Obter um token e chamar a API do Microsoft Graph usando a identidade do aplicativo do console

Neste guia de início rápido, você baixará e executará um exemplo de código que demonstra como um aplicativo de console .NET Core pode obter um token de acesso para chamar a API do Microsoft Graph e exibir uma [lista de usuários](/graph/api/user-list) no diretório. O exemplo de código também demonstra como um trabalho ou um serviço Windows pode ser executado com uma identidade de aplicativo, em vez de uma identidade do usuário. O exemplo de aplicativo de console deste guia de início rápido também é um aplicativo daemon e, portanto, um aplicativo cliente confidencial.

> [!div renderon="docs"]
> O seguinte diagrama mostra como o aplicativo de exemplo funciona:
>
> ![Diagrama que mostra como o aplicativo de exemplo gerado por este início rápido funciona.](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)
>

## <a name="prerequisites"></a>Pré-requisitos

Este guia de início rápido exige o [.NET Core 3.1](https://www.microsoft.com/net/download/dotnet-core), mas também funcionará com o .NET Core 5.0.

> [!div renderon="docs"]
> ## <a name="register-and-download-the-app"></a>Registrar e baixar o aplicativo

> [!div renderon="docs" class="sxs-lookup"]
>
> Você tem duas opções para começar a criar o seu aplicativo: configuração automática ou manual.
>
> ### <a name="automatic-configuration"></a>Configuração automática
>
> Caso deseje registrar e configurar automaticamente seu aplicativo e baixar o exemplo de código, siga estas etapas:
>
> 1. Acesse a <a href="https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/DotNetCoreDaemonQuickstartPage/sourceType/docs" target="_blank">página do portal do Azure para o registro do aplicativo</a>.
> 1. Insira um nome para seu aplicativo e selecione **Registrar**.
> 1. Siga as instruções para baixar e configurar automaticamente o novo aplicativo com apenas um clique.
>
> ### <a name="manual-configuration"></a>Configuração manual
>
> Se você quiser configurar manualmente o seu aplicativo e o exemplo de código, use os procedimentos a seguir.
>
> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Etapa 1: Registre seu aplicativo
> Para registrar seu aplicativo e adicionar as informações de registro do aplicativo à solução manualmente, siga estas etapas:
>
> 1. Entre no <a href="https://portal.azure.com/" target="_blank">Portal do Azure</span></a>.
> 1. Se você tem acesso a vários locatários, use o filtro **Diretório + assinatura** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o locatário no qual você deseja registrar o aplicativo.
> 1. Pesquise **Azure Active Directory** e selecione-o.
> 1. Em **Gerenciar**, selecione **Registros de aplicativo** > **Novo registro**.
> 1. Em **Nome**, insira um nome para o seu aplicativo. Por exemplo, insira **Daemon-console**. Os usuários do seu aplicativo verão esse nome e você pode alterá-lo mais tarde.
> 1. Selecione **Registrar** para criar o aplicativo.
> 1. Em **Gerenciar**, selecione **Certificados e Segredos**.
> 1. Em **Segredos do cliente**, selecione **Novo segredo do cliente**, insira um nome e selecione **Adicionar**. Registre o valor secreto em uma localização segura para uso em uma etapa posterior.
> 1. Em **Gerenciar**, selecione **Permissões de API** > **Adicionar uma permissão**. Selecione **Microsoft Graph**.
> 1. Selecione **Permissões de aplicativo**.
> 1. No nó **Usuário**, selecione **User.Read.All** e **Adicionar permissões**.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-your-quickstart-app"></a>Baixar e configurar seu aplicativo de início rápido
>
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Etapa 1: Configurar seu aplicativo no portal do Azure
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

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-visual-studio-project"></a>Etapa 3: Configurar o projeto do Visual Studio
>
> 1. Extraia o arquivo. zip em uma pasta local próxima à raiz do disco. Por exemplo, extraia em *C:\Azure-Samples*.
>
>    É recomendável extrair os arquivos em um diretório próximo à raiz da unidade para evitar erros causados por limitações de comprimento do caminho no Windows.
>
> 1. Abra a solução no Visual Studio: *1-Call-MSGraph\daemon-console.sln* (opcional).
> 1. Em *appsettings.json*, substitua os valores de `Tenant`, `ClientId` e `ClientSecret`:
>
>    ```json
>    "Tenant": "Enter_the_Tenant_Id_Here",
>    "ClientId": "Enter_the_Application_Id_Here",
>    "ClientSecret": "Enter_the_Client_Secret_Here"
>    ```
>    Neste código:
>    - `Enter_the_Application_Id_Here` é a ID do aplicativo (cliente) que você registrou.
        Para encontrar os valores da ID do aplicativo (cliente) e da ID de diretório (locatário), acesse a página **Visão Geral** do aplicativo no portal do Azure.
>    - Substitua `Enter_the_Tenant_Id_Here` pela ID ou pelo nome do locatário (por exemplo, `contoso.microsoft.com`).
>    - Substitua `Enter_the_Client_Secret_Here` pelo segredo do cliente que você criou na etapa 1.
    Para gerar uma chave, acesse a página **Certificados e segredos**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-admin-consent"></a>Etapa 3: Consentimento do administrador

> [!div renderon="docs"]
> #### <a name="step-4-admin-consent"></a>Etapa 4: Consentimento do administrador

Se você tentar executar o aplicativo neste ponto, receberá um erro *HTTP 403 – Proibido*: "privilégios insuficientes para concluir a operação". Esse erro acontece porque qualquer permissão somente do aplicativo exige o consentimento de um Administrador global do seu diretório ao aplicativo. Selecione uma das opções a seguir, dependendo da sua função.

##### <a name="global-tenant-administrator"></a>Administrator de locatário global

> [!div renderon="docs"]
> Se você é um administrador de locatários global, acesse **Aplicativos empresariais** no portal do Azure. Selecione o registro do aplicativo e **Permissões** na seção **Segurança** do painel esquerdo. Depois, escolha o botão grande rotulado **Conceder consentimento do administrador para {Nome do Locatário}** (em que **{Nome do Locatário}** é o nome do seu diretório).

> [!div renderon="portal" class="sxs-lookup"]
> Se você é um Administrador global, acesse a página **Permissões de API** e selecione **Conceder consentimento do administrador para Insira_o_Nome_do_Locatário_Aqui**.
> > [!div id="apipermissionspage"]
> > [Acesse a página Permissões de API]()

##### <a name="standard-user"></a>Usuário padrão

Se você é um usuário padrão do seu locatário, solicite ao Administrador global que forneça consentimento do administrador para seu aplicativo. Para fazer isso, dê a seguinte URL ao administrador:

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
> Nessa URL:
> * Substitua `Enter_the_Tenant_Id_Here` pela ID ou pelo nome do locatário (por exemplo, `contoso.microsoft.com`).
> * `Enter_the_Application_Id_Here` é a ID do aplicativo (cliente) que você registrou.

Talvez você veja o erro "AADSTS50011: nenhum endereço de resposta está registrado para o aplicativo" depois de conceder seu consentimento ao aplicativo usando a URL anterior. Esse erro ocorre porque esse aplicativo e a URL não têm um URI de redirecionamento. Você pode ignorá-la.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>Etapa 4: Executar o aplicativo

> [!div renderon="docs"]
> #### <a name="step-5-run-the-application"></a>Etapa 5: Executar o aplicativo

Se estiver usando o Visual Studio ou o Visual Studio para Mac, clique em **F5** para executar o aplicativo. Caso contrário, execute o aplicativo por meio do prompt de comando, do console ou do terminal:

```dotnetcli
cd {ProjectFolder}\1-Call-MSGraph\daemon-console
dotnet run
```
Neste código:
* `{ProjectFolder}` é a pasta em que você extraiu o arquivo .zip. Um exemplo é `C:\Azure-Samples\active-directory-dotnetcore-daemon-v2`.

Você verá uma lista de usuários no Azure Active Directory como resultado.

Este aplicativo de início rápido usa um segredo do cliente para se identificar como um cliente confidencial. O segredo do cliente é adicionado como um arquivo de texto sem formatação aos seus arquivos de projeto. Por motivos de segurança, recomendamos que você use um certificado em vez de um segredo do cliente antes de considerar o aplicativo como um aplicativo de produção. Para obter mais informações sobre como usar um certificado, confira [estas instruções](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/#variation-daemon-application-using-client-credentials-with-certificates) no repositório do GitHub para este exemplo.

## <a name="more-information"></a>Mais informações
Esta seção apresenta uma visão geral do código necessário para a entrada de usuários. Essa visão geral pode ser útil para entender como o código funciona, quais são os argumentos principais e como adicionar credenciais a um aplicativo de console .NET Core existente.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="how-the-sample-works"></a>Como o exemplo funciona
>
> ![Diagrama que mostra como o aplicativo de exemplo gerado por este início rápido funciona.](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)

### <a name="msalnet"></a>MSAL.NET

A Biblioteca de Autenticação da Microsoft (MSAL, no pacote [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)) é a biblioteca usada para conectar usuários e solicitar tokens para acessar uma API protegida pela plataforma de identidade da Microsoft. Este guia de início rápido solicita tokens usando a identidade do próprio aplicativo, em vez de permissões delegadas. O fluxo de autenticação, nesse caso, é conhecido como [fluxo OAuth de credenciais do cliente](v2-oauth2-client-creds-grant-flow.md). Para obter mais informações sobre como usar o MSAL.NET com um fluxo de credenciais de cliente, confira [este artigo](https://aka.ms/msal-net-client-credentials).

 Instale o MSAL.NET executando o seguinte comando no Console do Gerenciador de Pacotes do Visual Studio:

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

 | Elemento | Descrição |
 |---------|---------|
 | `config.ClientSecret` | O segredo do cliente criado para o aplicativo no portal do Azure. |
 | `config.ClientId` | A ID do aplicativo (cliente) referente ao aplicativo registrado no portal do Azure. Encontre esse valor na página **Visão geral** do aplicativo no portal do Azure. |
 | `config.Authority`    | (Opcional) O ponto de extremidade do STS (serviço de token de segurança) usado para o usuário se autenticar. Normalmente, é `https://login.microsoftonline.com/{tenant}` para a nuvem pública, em que `{tenant}` é o nome ou a ID do seu locatário.|

Para obter mais informações, confira a [documentação de referência do `ConfidentialClientApplication`](/dotnet/api/microsoft.identity.client.iconfidentialclientapplication).

### <a name="requesting-tokens"></a>Solicitando tokens

Para solicitar um token usando a identidade do aplicativo, use o método `AcquireTokenForClient`:

```csharp
result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
```

|Elemento| Descrição |
|---------|---------|
| `scopes` | Contém os escopos solicitados. Para clientes confidenciais, esse valor deve usar um formato semelhante a `{Application ID URI}/.default`. Esse formato indica que os escopos solicitados são aqueles definidos estaticamente no conjunto de objetos de aplicativo no portal do Azure. Para o Microsoft Graph, `{Application ID URI}` aponta para `https://graph.microsoft.com`. Para as APIs Web personalizadas, `{Application ID URI}` é definido no portal do Azure em **Registro de Aplicativo (Versão Prévia)**  > **Expor uma API**. |

Para obter mais informações, confira a [documentação de referência do `AcquireTokenForClient`](/dotnet/api/microsoft.identity.client.confidentialclientapplication.acquiretokenforclient).

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre aplicativos daemon, confira a visão geral do cenário:

> [!div class="nextstepaction"]
> [Aplicativo daemon que chama APIs Web](scenario-daemon-overview.md)
