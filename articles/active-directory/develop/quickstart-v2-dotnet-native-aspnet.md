---
title: 'Início rápido: Chamar um ASP.NET Web API protegido pela plataforma de identidade da Microsoft | Azure'
titleSuffix: Microsoft identity platform
description: Neste início rápido, saiba como chamar uma ASP.NET Web API protegida pela plataforma de identidade da Microsoft de um aplicativo da Área de Trabalho do Windows (WPF).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/05/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.openlocfilehash: 7d802aa83e2d48e7b66980b5c06aece10a93f37f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104658519"
---
# <a name="quickstart-call-an-aspnet-web-api-thats-protected-by-microsoft-identity-platform"></a>Início Rápido: chamar uma ASP.NET Web API protegida pela plataforma de identidade da Microsoft

Neste guia de início rápido, você baixará e executará um exemplo de código que demonstra como proteger uma ASP.NET Web API restringindo o acesso aos recursos dela somente às contas autorizadas. O exemplo dá suporte à autorização de contas Microsoft pessoais e contas em qualquer organização do Azure AD (Azure Active Directory).

O artigo também usa um aplicativo do WPF (Windows Presentation Foundation) para demonstrar como você pode solicitar um token de acesso para acessar uma API Web.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Visual Studio 2017 ou 2019. Baixe o [Visual Studio gratuitamente](https://www.visualstudio.com/downloads/).

## <a name="clone-or-download-the-sample"></a>Clonar ou baixar o exemplo

Você pode obter o exemplo de duas maneiras:

* Clone-o no Shell ou na linha de comando:
   ```console
   git clone https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git
   ```
* [Baixe-o como um arquivo ZIP](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip).

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

## <a name="register-your-web-api"></a>Registrar sua API Web

Nesta seção, você registrará sua API Web em **Registros de aplicativo** no portal do Azure.

### <a name="choose-your-azure-ad-tenant"></a>Escolher o locatário do Azure AD

Para registrar seus aplicativos manualmente, escolha o locatário do Azure AD (Azure Active Directory) no qual deseja criar os aplicativos.

1. Entre no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>.
1. Se você tem acesso a vários locatários, use o filtro **Diretório + assinatura** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o locatário que deseja usar.

### <a name="register-the-todolistservice-app"></a>Registrar o aplicativo TodoListService

1. Entre no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>.
1. Se você tem acesso a vários locatários, use o filtro **Diretório + assinatura** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o locatário no qual você deseja registrar um aplicativo.
1. Pesquise **Azure Active Directory** e selecione-o.
1. Em **Gerenciar**, selecione **Registros de aplicativo** > **Novo registro**.
1. Insira um **Nome** para seu aplicativo, por exemplo, `AppModelv2-NativeClient-DotNet-TodoListService`. Os usuários do seu aplicativo podem ver esse nome e você pode alterá-lo mais tarde.
1. Em **Tipos de conta compatíveis**, selecione **Contas em qualquer diretório da organização**.
1. Selecione **Registrar** para criar o aplicativo.
1. Na página **Visão Geral** do aplicativo, localize o valor da **ID do aplicativo (cliente)** e registre-o para uso posterior. Você precisará dele para definir o arquivo de configuração do Visual Studio para este projeto (ou seja, `ClientId` no arquivo *TodoListService\Web.config*).
1. Em **Gerenciar**, selecione **Expor uma API** > **Adicionar um escopo**. Aceite o URI da ID do Aplicativo proposto (`api://{clientId}`) selecionando **Salvar e continuar** e insira as seguintes informações:

    1. Para **Nome do escopo**, insira `access_as_user`.
    1. Para **Quem pode consentir**, verifique se a opção **Administradores e usuários** está selecionada.
    1. Na caixa **Nome de exibição do consentimento do administrador**, insira `Access TodoListService as a user`.
    1. Na caixa **Descrição do consentimento do administrador**, insira `Accesses the TodoListService web API as a user`.
    1. Na caixa **Nome de exibição do consentimento do usuário**, insira `Access TodoListService as a user`.
    1. Na caixa **Descrição do consentimento do usuário**, insira `Accesses the TodoListService web API as a user`.
    1. Para **Estado**, mantenha **Habilitado**.
1. Selecione **Adicionar escopo**.

### <a name="configure-the-service-project"></a>Configurar o projeto de serviço

Configure o projeto de serviço para que corresponda à API Web registrada fazendo o seguinte:

1. Abra a solução no Visual Studio e abra o arquivo *Web.config* na raiz do projeto TodoListService.

1. Substitua o valor do parâmetro `ida:ClientId` pelo valor da ID do Cliente (ID do Aplicativo) do aplicativo que você acabou de registrar no portal de **Registros de aplicativo**.

### <a name="add-the-new-scope-to-the-appconfig-file"></a>Adicionar o novo escopo ao arquivo app.config

Para adicionar o novo escopo ao arquivo *app.config* do TodoListClient, faça o seguinte:

1. Na pasta raiz do projeto TodoListClient, abra o arquivo *app.config*.

1. Cole a ID do Aplicativo que você acabou de registrar para o projeto TodoListService no parâmetro `TodoListServiceScope`, substituindo a cadeia de caracteres `{Enter the Application ID of your TodoListService from the app registration portal}`.

  > [!NOTE]
  > Verifique se a ID do Aplicativo usa o seguinte formato: `api://{TodoListService-Application-ID}/access_as_user` (em que `{TodoListService-Application-ID}` é o GUID que representa a ID do Aplicativo para o aplicativo do TodoListService).

## <a name="register-the-todolistclient-client-app"></a>Registrar o aplicativo cliente do TodoListClient

Nesta seção, você registra o aplicativo do TodoListClient nos **Registros de aplicativo** do portal do Azure e configura o código no projeto TodoListClient. Se o cliente e o servidor forem considerados *o mesmo aplicativo*, você poderá reutilizar o aplicativo registrado na etapa 2. Use o mesmo aplicativo se desejar que os usuários entrem com uma conta pessoal Microsoft.

### <a name="register-the-app"></a>Registre o aplicativo

Para registrar o aplicativo TodoListClient, faça o seguinte:

1. Vá para o portal [Registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) da plataforma de identidade da Microsoft para desenvolvedores.
1. Selecione **Novo registro**.
1. Quando a **página Registrar um aplicativo** for exibida, insira as informações de registro do aplicativo:

    1. Na seção **Nome**, insira um nome de aplicativo relevante que será exibido aos usuários do aplicativo (por exemplo, **NativeClient-DotNet-TodoListClient**).
    1. Em **Tipos de conta compatíveis**, selecione **Contas em qualquer diretório da organização**.
    1. Selecione **Registrar** para criar o aplicativo.

   > [!NOTE]
   > No arquivo *app.config* do projeto TodoListClient, o valor padrão de `ida:Tenant` é definido como `common`. Os valores possíveis são:
   > - `common`: você pode entrar usando uma conta corporativa ou de estudante ou uma conta pessoal Microsoft (porque você selecionou **Contas em qualquer diretório da organização** na etapa 3b).
   > - `organizations`: você pode entrar usando uma conta corporativa ou de estudante.
   > - `consumers`: você pode entrar somente usando uma conta pessoal Microsoft.

1. Na página de **Visão Geral** do aplicativo, selecione **Autenticação** e faça o seguinte:

    1. Em **Configurações da plataforma**, selecione o botão **Adicionar uma plataforma**.
    1. Para **Aplicativos móveis e da área de trabalho**, selecione **Aplicativos móveis e da área de trabalho**.
    1. Em **URIs de Redirecionamento**, marque a caixa de seleção **https://login.microsoftonline.com/common/oauth2/nativeclient** .
    1. Selecione **Configurar**.

1. Selecione **Permissões de API** e faça o seguinte:

    1. Selecione o botão **Adicionar uma permissão**.
    1. Selecione a guia **Minhas APIs**.
    1. Na lista de APIs, selecione a **API AppModelv2-NativeClient-DotNet-TodoListService** ou o nome que você inseriu para a API Web.
    1. Marque a caixa de seleção de permissão **access_as_user** caso ela ainda não esteja marcada. Use a caixa de Pesquisa, se necessário.
    1. Selecione o botão **Adicionar permissões**.

### <a name="configure-your-project"></a>Configurar seu projeto

Para configurar o projeto TodoListClient, faça o seguinte:

1. No portal de **Registros de aplicativo**, na página **Visão Geral**, copie o valor da **ID do Aplicativo (Cliente)** .

1. Da pasta raiz do projeto TodoListClient, abra o arquivo *app.config* e cole o valor da ID do Aplicativo no parâmetro `ida:ClientId`.

## <a name="run-your-todolistclient-project"></a>Executar o projeto TodoListClient

Para executar o projeto TodoListClient, faça o seguinte:

1. Pressione F5 para abrir o projeto TodoListClient. A página do projeto deve ser exibida.

1. No canto superior direito, selecione **Entrar** e conecte-se com as mesmas credenciais usadas para registrar o aplicativo ou entre como um usuário no mesmo diretório.

   Se você estiver entrando pela primeira vez, talvez seja solicitado a consentir com a API Web do TodoListService.

   Para ajudá você a acessar a API Web do TodoListService e manipular a lista de *tarefas pendentes*, a entrada também solicita um token de acesso para o escopo *access_as_user*.

## <a name="pre-authorize-your-client-application"></a>Pré-autorizar seu aplicativo cliente

Uma forma de permitir que os usuários de outros diretórios acessem a API Web é autorizar previamente o aplicativo cliente a acessar a API Web. Você faz isso adicionando a ID do Aplicativo Cliente à lista de aplicativos previamente autorizados para sua API Web. Ao adicionar um cliente previamente autorizado, você está permitindo que usuários acessem a API Web sem a necessidade de fornecer consentimento. Para autorizar previamente seu aplicativo cliente, faça o seguinte:

1. No portal **Registros de aplicativo**, abra as propriedades do aplicativo do TodoListService.
1. Na seção **Expor uma API**, em **Aplicativos cliente autorizados**, selecione **Adicionar um aplicativo cliente**.
1. No campo **ID do Cliente**, cole a ID do Aplicativo do TodoListClient.
1. Na seção **Escopos autorizados**, selecione o escopo da API Web `api://<Application ID>/access_as_user`.
1. Escolha **Adicionar aplicativo**.

### <a name="run-your-project"></a>Execute seu projeto

1. Pressione F5 para executar o projeto. O aplicativo do TodoListClient deve ser aberto.
1. No canto superior direito, selecione **Entrar** e conecte-se usando uma conta pessoal Microsoft, como live.com ou hotmail.com, ou uma conta corporativa ou de estudante.

## <a name="optional-limit-sign-in-access-to-certain-users"></a>Opcional: limitar o acesso de entrada a determinados usuários

Por padrão, quando você seguiu as etapas anteriores, todas as contas pessoais, como outlook.com ou live.com, ou as contas corporativas ou de estudante de organizações integradas ao Azure AD podem solicitar tokens e acessar a API Web.

Para especificar quem pode entrar no aplicativo, use uma das seguintes opções:

### <a name="option-1-limit-access-to-a-single-organization-single-tenant"></a>Opção 1: limitar o acesso a uma organização (locatário único)

Você pode restringir o acesso de entrada ao aplicativo às contas de usuário que estejam em um locatário do Azure AD, incluindo as *contas de convidado* daquele locatário. Esse cenário é comum para *aplicativos de linha de negócios*.

1. Abra o arquivo *App_Start\Startup.Auth* e altere o valor do ponto de extremidade de metadados que é passado em `OpenIdConnectSecurityTokenProvider` para `"https://login.microsoftonline.com/{Tenant ID}/v2.0/.well-known/openid-configuration"`. Você também pode usar o nome do locatário, como `contoso.onmicrosoft.com`.
2. No mesmo arquivo, defina a propriedade `ValidIssuer` em `TokenValidationParameters` como `"https://sts.windows.net/{Tenant ID}/"` e o argumento `ValidateIssuer` como `true`.

### <a name="option-2-use-a-custom-method-to-validate-issuers"></a>Opção 2: Usar um método personalizado para validar emissores

É possível implementar um método personalizado para validar emissores usando o parâmetro `IssuerValidator`. Para obter mais informações sobre como usar esse parâmetro, confira a [classe TokenValidationParameters](/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters).

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre o cenário de API Web compatível com a plataforma de identidade da Microsoft:
> [!div class="nextstepaction"]
> [Cenário da API Web protegida](scenario-protected-web-api-overview.md)
