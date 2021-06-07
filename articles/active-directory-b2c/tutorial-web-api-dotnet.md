---
title: 'Tutorial: Permitir acesso a uma ASP.NET Web API'
titleSuffix: Azure AD B2C
description: Tutorial sobre como usar o Active Directory B2C para proteger a ASP.NET Web API e chamá-la de um aplicativo Web ASP .NET.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/14/2019
ms.custom: devx-track-csharp, mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 84bf0ddd194cd59a7e728c0c50dbe3cbad2afa4a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100555651"
---
# <a name="tutorial-grant-access-to-an-aspnet-web-api-using-azure-active-directory-b2c"></a>Tutorial: Permitir acesso a uma ASP.NET Web API usando o Azure Active Directory B2C

Este tutorial mostra como chamar um recurso de API Web protegida no Azure AD B2C (Azure Active Directory B2C) de um aplicativo Web ASP .NET.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Adicionar um aplicativo API Web
> * Configurar escopos para uma API Web
> * Conceder permissões à API Web
> * Configurar o exemplo para usar o aplicativo

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Conclua as etapas e os pré-requisitos no [Tutorial: Habilitar autenticação em um aplicativo Web usando o Azure Active Directory B2C](tutorial-web-app-dotnet.md).

## <a name="add-a-web-api-application"></a>Adicionar um aplicativo API Web

Os recursos da API Web precisam ser registrados no seu locatário antes de poderem aceitar e responder a solicitações de recurso protegido de aplicativos clientes que apresentem um token de acesso.

Para registrar um aplicativo no locatário do Azure AD B2C, você pode usar a nossa nova experiência unificada de **Registros de aplicativos** ou a nossa experiência herdada **Aplicativos (Herdado)** . [Saiba mais sobre a nova experiência](./app-registrations-training-guide.md).

#### <a name="app-registrations"></a>[Registros de aplicativo](#tab/app-reg-ga/)

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro **Diretório + assinatura** no menu superior e, em seguida, selecione o diretório que contém o locatário do Azure AD B2C.
1. No menu à esquerda, selecione **Azure AD B2C**. Ou selecione **Todos os serviços** e pesquise e selecione **Azure AD B2C**.
1. Escolha **Registros de aplicativo** e **Novo registro**.
1. Insira um **Nome** para o aplicativo. Por exemplo, *webapi1*.
1. Em **URI de Redirecionamento**, selecione **Web** e depois insira um ponto de extremidade em que o Azure AD B2C deve retornar os tokens solicitados pelo seu aplicativo. Neste tutorial, o exemplo é executado localmente e escuta em `https://localhost:44332`.
1. Selecione **Registrar**.
1. Registre a **ID do aplicativo (cliente)** para uso em uma etapa posterior.

#### <a name="applications-legacy"></a>[Aplicativos (Herdado)](#tab/applications-legacy/)

1. Entre no [portal do Azure](https://portal.azure.com).
2. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C selecionando o filtro **Diretório + assinatura** no menu superior e escolhendo o diretório que contém o locatário.
3. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
4. Selecione **Aplicativos (Herdado)** e, em seguida, selecione **Adicionar**.
5. Insira um nome para o aplicativo. Por exemplo, *webapi1*.
6. Para **incluir o aplicativo web/API web**, selecione **Sim**.
7. Para a **URL de resposta**, insira um ponto de extremidade em que o Azure AD B2C deve retornar os tokens solicitados pelo seu aplicativo. Neste tutorial, o exemplo é executado localmente e escuta em `https://localhost:44332`.
8. Para o **ID do aplicativo URI**, insira o identificador usado para sua API da web. O URI do identificador completo, incluindo o domínio, é gerado para você. Por exemplo, `https://contosotenant.onmicrosoft.com/api`.
9. Clique em **Criar**.
10. Na página de propriedades, registre a ID do aplicativo que você usará ao configurar o aplicativo Web.

* * *

## <a name="configure-scopes"></a>Configurar escopos

Os escopos fornecem uma maneira de controlar o acesso a recursos protegidos. Escopos são usados pela API Web para implementar o controle de acesso com base em escopo. Por exemplo, os usuários da API Web podem ter tanto acesso de leitura quanto de gravação ou somente acesso de leitura. Neste tutorial, você usa escopos para definir as permissões de leitura e gravação da API Web.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

## <a name="grant-permissions"></a>Conceder permissões

Para chamar uma API Web protegida de um aplicativo, é necessário conceder permissões de aplicativo à API. No tutorial de pré-requisito, você criou um aplicativo Web no Azure AD B2C chamado *webapp1*. Use este aplicativo para chamar a API Web.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

Seu aplicativo é registrado para chamar a API Web protegida. Um usuário autentica-se com o Azure AD B2C para usar o aplicativo. O aplicativo obtém uma concessão de autorização do Azure AD B2C para acessar a API Web protegida.

## <a name="configure-the-sample"></a>Configurar o exemplo

Agora que a API Web está registrada e você tem escopos definidos, configure a API Web para usar o locatário do Azure AD B2C. Neste tutorial, você configura uma API Web de exemplo. A API Web de exemplo está inclusa no projeto que você baixou no tutorial de pré-requisito.

Há dois projetos na solução de exemplo:

* **TaskWebApp** – Crie e edite uma lista de tarefas. O exemplo usa o fluxo de usuário de **inscrição ou conexão** para inscrever ou conectar usuários.
* **TaskService** – Dá suporte à funcionalidade de criar, ler, atualizar e excluir a lista de tarefas. A API é protegida pelo Azure AD B2C e chamada pelo TaskWebApp.

### <a name="configure-the-web-application"></a>Configurar o aplicativo Web

1. Abra a solução **B2C-WebAPI-DotNet** no Visual Studio.
1. No projeto **TaskWebApp**, abra **Web.config**.
1. Para executar a API localmente, use a configuração de localhost para **api:TaskServiceUrl**. Altere o Web.config da seguinte maneira:

    ```csharp
    <add key="api:TaskServiceUrl" value="https://localhost:44332/"/>
    ```

1. Configure o URI da API. Este é o URI usado pelo aplicativo Web para fazer a solicitação à API. Além disso, configure as permissões solicitadas.

    ```csharp
    <add key="api:ApiIdentifier" value="https://<Your tenant name>.onmicrosoft.com/api/" />
    <add key="api:ReadScope" value="demo.read" />
    <add key="api:WriteScope" value="demo.write" />
    ```

### <a name="configure-the-web-api"></a>Configurar a API Web

1. No projeto **TaskService**, abra **Web.config**.
1. Configure a API para usar seu locatário.

    ```csharp
    <add key="ida:AadInstance" value="https://<Your tenant name>.b2clogin.com/{0}/{1}/v2.0/.well-known/openid-configuration" />
    <add key="ida:Tenant" value="<Your tenant name>.onmicrosoft.com" />
    ```

1. Defina a ID do cliente para a ID do Aplicativo do seu aplicativo de API Web registrado, *webapi1*.

    ```csharp
    <add key="ida:ClientId" value="<application-ID>"/>
    ```

1. Atualize a configuração de fluxo de usuário com o nome do fluxo de usuário de inscrição e conexão, *B2C_1_signupsignin1*.

    ```csharp
    <add key="ida:SignUpSignInPolicyId" value="B2C_1_signupsignin1" />
    ```

1. Configure os escopos de configuração para corresponder aos criados no portal.

    ```csharp
    <add key="api:ReadScope" value="demo.read" />
    <add key="api:WriteScope" value="demo.write" />
    ```

## <a name="run-the-sample"></a>Execute o exemplo

Você precisa executar os projetos **TaskWebApp** e **TaskService**.

1. No Gerenciador de Soluções, clique com o botão direito do mouse na solução e selecione **Definir projetos de inicialização...** .
1. Selecione **Vários projetos de inicialização**.
1. Altere a **Ação** para ambos os projetos para **Iniciar**.
1. Clique em **OK** para salvar a configuração.
1. Pressione **F5** para executar os dois aplicativos. Cada aplicativo é aberto em sua própria janela do navegador.
    * `https://localhost:44316/` é o aplicativo Web.
    * `https://localhost:44332/` é a API Web.

1. No aplicativo Web, selecione **inscrição/conexão** para entrar no aplicativo Web. Use a conta que você criou anteriormente.
1. Depois de entrar, selecione **Lista de Tarefas Pendentes** e crie um item de lista de tarefas pendentes.

Quando você cria um item de lista de tarefas pendentes, o aplicativo Web faz uma solicitação à API Web para gerar o item de lista de tarefas pendentes da Web. Seu aplicativo Web protegido está chamando a API Web protegida no seu locatário do Azure AD B2C.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Adicionar um aplicativo API Web
> * Configurar escopos para uma API Web
> * Conceder permissões à API Web
> * Configurar o exemplo para usar o aplicativo

> [!div class="nextstepaction"]
> [Adicionar provedores de identidade a seus aplicativos no Azure Active Directory B2C](add-identity-provider.md)