---
title: Tutorial – Permitir acesso a uma ASP.NET Web API – Azure Active Directory B2C | Microsoft Docs
description: Tutorial sobre como usar o Active Directory B2C para proteger a ASP.NET Web API e chamá-la de um aplicativo Web ASP .NET.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 339b118e48a01469312a40e6b0652a4ffb90291a
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68347135"
---
# <a name="tutorial-grant-access-to-an-aspnet-web-api-using-azure-active-directory-b2c"></a>Tutorial: Permitir acesso a uma ASP.NET Web API usando o Azure Active Directory B2C

Este tutorial mostra como chamar um recurso de API Web protegida no Azure AD (Azure Active Directory) B2C de um aplicativo Web ASP .NET.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Adicionar um aplicativo API Web
> * Configurar escopos para uma API Web
> * Conceder permissões à API Web
> * Configurar o exemplo para usar o aplicativo

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Conclua as etapas e os pré-requisitos no [Tutorial: Habilitar autenticação em um aplicativo Web usando o Azure Active Directory B2C](active-directory-b2c-tutorials-web-app.md).

## <a name="add-a-web-api-application"></a>Adicionar um aplicativo API Web

Os recursos da API Web precisam ser registrados no seu locatário antes de poderem aceitar e responder a solicitações de recurso protegido de aplicativos clientes que apresentem um token de acesso.

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C clicando no **filtro Diretório e assinatura** no menu superior e escolhendo o diretório que contém seu locatário.
3. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
4. Selecione **Aplicativos** e, em seguida, selecione **Adicionar**.
5. Insira um nome para o aplicativo. Por exemplo, *webapi1*.
6. Para **Incluir aplicativo da Web / API da Web** e **Permitir fluxo implícito**, selecione **Sim**.
7. Para a **URL de resposta**, insira um ponto de extremidade em que o Azure AD B2C deve retornar os tokens solicitados pelo seu aplicativo. Neste tutorial, o exemplo é executado localmente e escuta em `https://localhost:44332`.
8. Para o **ID do aplicativo URI**, insira o identificador usado para sua API da web. O URI do identificador completo, incluindo o domínio, é gerado para você. Por exemplo, `https://contosotenant.onmicrosoft.com/api`.
9. Clique em **Criar**.
10. Na página de propriedades, registre a ID do aplicativo que você usará ao configurar o aplicativo Web.

## <a name="configure-scopes"></a>Configurar escopos

Os escopos fornecem uma maneira de controlar o acesso a recursos protegidos. Escopos são usados pela API Web para implementar o controle de acesso com base em escopo. Por exemplo, os usuários da API Web podem ter tanto acesso de leitura quanto de gravação ou somente acesso de leitura. Neste tutorial, você usa escopos para definir as permissões de leitura e gravação da API Web.

1. Selecione **Aplicativos** e selecione *webapi1*.
2. Selecione **Escopos publicados**.
3. Em **escopo**, insira `Hello.Read` e, na descrição, insira `Read access to hello`.
4. Em **escopo**, insira `Hello.Write` e, na descrição, insira `Write access to hello`.
5. Clique em **Save** (Salvar).

Os escopos publicados podem ser usados para conceder uma permissão de aplicativo cliente à API Web.

## <a name="grant-permissions"></a>Conceder permissões

Para chamar uma API Web protegida de um aplicativo, é necessário conceder permissões de aplicativo à API. No tutorial de pré-requisito, você criou um aplicativo Web no Azure AD B2C chamado *webapp1*. Use este aplicativo para chamar a API Web.

1. Selecione **Aplicativos** e, em seguida, selecione *webapp1*.
2. Selecione **Acesso à API** e, em seguida, selecione **Adicionar**.
3. No menu suspenso **Selecionar API**, selecione *webapi1*.
4. No menu suspenso **Selecionar Escopos**, selecione os escopos **Hello.Read** e **Hello.Write** definidos anteriormente.
5. Clique em **OK**.

Seu aplicativo é registrado para chamar a API Web protegida. Um usuário autentica-se com o Azure AD B2C para usar o aplicativo. O aplicativo obtém uma concessão de autorização do Azure AD B2C para acessar a API Web protegida.

## <a name="configure-the-sample"></a>Configurar o exemplo

Agora que a API Web está registrada e você tem escopos definidos, configure a API Web para usar o locatário do Azure AD B2C. Neste tutorial, você configura uma API Web de exemplo. A API Web de exemplo está inclusa no projeto que você baixou no tutorial de pré-requisito.

Há dois projetos na solução de exemplo:

A seguir, há dois projetos estão na solução de exemplo:

- **TaskWebApp** – Crie e edite uma lista de tarefas. O exemplo usa o fluxo de usuário de **inscrição ou conexão** para inscrever ou conectar usuários.
- **TaskService** – Dá suporte à funcionalidade de criar, ler, atualizar e excluir a lista de tarefas. A API é protegida pelo Azure AD B2C e chamada pelo TaskWebApp.

### <a name="configure-the-web-application"></a>Configurar o aplicativo Web

1. Abra a solução **B2C-WebAPI-DotNet** no Visual Studio.
2. Abra **Web.config** no projeto **TaskWebApp**.
3. Para executar a API localmente, use a configuração de localhost para **api:TaskServiceUrl**. Altere o Web.config da seguinte maneira: 

    ```csharp
    <add key="api:TaskServiceUrl" value="https://localhost:44332/"/>
    ```

3. Configure o URI da API. Este é o URI usado pelo aplicativo Web para fazer a solicitação à API. Além disso, configure as permissões solicitadas.

    ```csharp
    <add key="api:ApiIdentifier" value="https://<Your tenant name>.onmicrosoft.com/api/" />
    <add key="api:ReadScope" value="Hello.Read" />
    <add key="api:WriteScope" value="Hello.Write" />
    ```

### <a name="configure-the-web-api"></a>Configurar a API Web

1. Abra **Web.config** no projeto **TaskService**.
2. Configure a API para usar seu locatário.

    ```csharp
    <add key="ida:Tenant" value="<Your tenant name>.onmicrosoft.com" />
    ```

3. Defina a ID do cliente como a ID do aplicativo registrado para sua API.

    ```csharp
    <add key="ida:ClientId" value="<application-ID>"/>
    ```

4. Atualize a configuração de fluxo de usuário com o nome do fluxo de usuário de inscrição e conexão.

    ```csharp
    <add key="ida:SignUpSignInUserFlowId" value="B2C_1_signupsignin1" />
    ```

5. Configure os escopos de configuração para corresponder ao que você criou no portal.

    ```csharp
    <add key="api:ReadScope" value="Hello.Read" />
    <add key="api:WriteScope" value="Hello.Write" />
    ```

## <a name="run-the-sample"></a>Execute o exemplo

Você precisa executar os projetos **TaskWebApp** e **TaskService**. 

1. No Gerenciador de Soluções, clique com o botão direito do mouse na solução e selecione **Definir projetos de inicialização...** . 
2. Selecione **Vários projetos de inicialização**.
3. Altere a **Ação** para ambos os projetos para **Iniciar**.
4. Clique em **OK** para salvar a configuração.
5. Pressione **F5** para executar os dois aplicativos. Cada aplicativo é aberto em sua própria guia do navegador. `https://localhost:44316/` é o aplicativo Web.
    `https://localhost:44332/` é a API Web.

6. No aplicativo Web, clique em **inscrição/conexão** para entrar no aplicativo Web. Use a conta que você criou anteriormente. 
7. Depois de entrar, clique na **Lista de Tarefas Pendentes** e crie um item de lista de tarefas pendentes.

Quando você cria um item de lista de tarefas pendentes, o aplicativo Web faz uma solicitação à API Web para gerar o item de lista de tarefas pendentes da Web. Seu aplicativo Web protegido está chamando a API Web protegida no seu locatário do Azure AD B2C.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Adicionar um aplicativo API Web
> * Configurar escopos para uma API Web
> * Conceder permissões à API Web
> * Configurar o exemplo para usar o aplicativo

> [!div class="nextstepaction"]
> [Tutorial: Adicionar provedores de identidade aos aplicativos no Azure Active Directory B2C](tutorial-add-identity-providers.md)
