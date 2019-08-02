---
title: Início Rápido – Configurar a entrada para um aplicativo ASP.NET usando o Azure Active Directory B2C | Microsoft Docs
description: Execute um aplicativo Web ASP.NET de exemplo que usa o Azure Active Directory B2C para fornecer a entrada na conta.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 647ea3bdeb914b97fe131d32078ddb610d4d163e
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67835469"
---
# <a name="quickstart-set-up-sign-in-for-an-aspnet-application-using-azure-active-directory-b2c"></a>Início Rápido: Configurar a entrada para um aplicativo ASP.NET usando o Azure Active Directory B2C

O Azure Active Directory (Azure AD) B2C fornece gerenciamento de identidades de nuvem para manter seu aplicativo, negócios e clientes protegidos. O Azure AD B2C permite que seus aplicativos se autentiquem com contas sociais e corporativas usando protocolos padrão. Neste início rápido, você usa um aplicativo ASP.NET para se conectar usando um provedor de identidade de redes sociais e chama uma API Web do Azure AD B2C protegida.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) com a carga de trabalho de **desenvolvimento Web e do ASP.NET**.
- Uma conta social do Facebook, Google, Microsoft ou Twitter.
- [Baixe um arquivo zip](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip) ou clone o aplicativo Web de exemplo do GitHub.

    ```
    git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
    ```

    Estes dois projetos estão na solução de exemplo:

    - **TaskWebApp**: um aplicativo Web que cria e edita uma lista de tarefas. O aplicativo Web usa o fluxo de usuário de **inscrição ou conexão** para inscrever ou conectar usuários.
    - **TaskService**: a API Web que dá suporte às funções criar, ler, atualizar e excluir para a lista de tarefas. A API Web é protegida pelo Azure AD B2C e chamada pelo aplicativo Web.

## <a name="run-the-application-in-visual-studio"></a>Executar o aplicativo no Visual Studio

1. Na pasta de projeto do aplicativo de exemplo, abra a solução **B2C-WebAPI-DotNet.sln** no Visual Studio.
2. Para este início rápido, você executa os projetos **TaskWebApp** e **TaskService** ao mesmo tempo. Clique com botão direito do mouse na solução **B2C-WebAPI-DotNet** no Gerenciador de Soluções e selecione **Definir Projetos de Inicialização**.
3. Selecione **Vários projetos de inicialização** e altere a **Ação** de ambos os projetos para **Iniciar**.
4. Clique em **OK**.
5. Pressione **F5** para executar os dois aplicativos. Cada aplicativo é aberto em sua própria guia do navegador:

    - `https://localhost:44316/`: o aplicativo Web ASP .NET. Você interage diretamente com este aplicativo no início rápido.
    - `https://localhost:44332/`: a API Web que é chamada pelo aplicativo Web ASP.NET.

## <a name="sign-in-using-your-account"></a>Conectar-se usando sua conta

1. Clique em **Inscrever-se/Entrar** no aplicativo Web ASP.NET para iniciar o fluxo de trabalho.

    ![Aplicativo Web ASP.NET de exemplo no navegador com o link de inscrição/entrada realçado](media/active-directory-b2c-quickstarts-web-app/web-app-sign-in.png)

    O exemplo dá suporte a várias opções de inscrição, incluindo o uso de um provedor de identidade social ou a criação de uma conta local usando um endereço de email. Para este guia de início rápido, use uma conta de provedor de identidade social do Facebook, do Google, da Microsoft ou do Twitter.

2. O Azure AD B2C apresenta uma página de entrada personalizada para uma marca fictícia chamada Wingtip Toys para o aplicativo Web de exemplo. Para inscrever-se usando um provedor de identidade social, clique no botão do provedor de identidade que você deseja usar.

    ![Página de entrada ou inscrição mostrando os botões do provedor de identidade](media/active-directory-b2c-quickstarts-web-app/sign-in-or-sign-up-web.png)

    Você se autentica (entra) usando as credenciais de sua conta social e autoriza o aplicativo a ler as informações da sua conta social. Ao conceder o acesso, o aplicativo poderá recuperar informações de perfil da conta social, tais como seu nome e cidade.

3. Conclua o processo de entrada para o provedor de identidade.

## <a name="edit-your-profile"></a>Editar o perfil

O Azure Active Directory B2C fornece funcionalidade para permitir que usuários atualizem seus perfis. O aplicativo Web de exemplo usa um fluxo de usuário de perfil de edição do Azure AD B2C no fluxo de trabalho.

1. Na barra de menus do aplicativo, clique no nome do perfil e selecione **Editar perfil** para editar o perfil criado.

    ![Aplicativo Web de exemplo no navegador com o link Editar perfil realçado](media/active-directory-b2c-quickstarts-web-app/edit-profile-web.png)

2. Altere o **Nome de exibição** ou **Cidade**e clique em **Continuar** para atualizar seu perfil.

    A alteração é exibida na parte superior direita da home page do aplicativo Web.

## <a name="access-a-protected-api-resource"></a>Acessar um recurso de API protegido

1. Clique em **Lista de tarefas pendentes** para inserir e modificar os itens da lista de tarefas pendentes.

2. Insira texto na caixa de texto **Novo Item**. Clique em **Adicionar** para chamar a API Web protegida do Azure AD B2C que adiciona um item de lista de tarefas pendentes.

    ![Aplicativo Web de exemplo no navegador com Adicionar um item de lista de tarefas pendentes](media/active-directory-b2c-quickstarts-web-app/add-todo-item-web.png)

    O aplicativo Web ASP.NET inclui um token de acesso do Azure AD na solicitação para a recurso da API Web protegido para executar operações em itens de lista de tarefas pendentes do usuário.

Você usou com êxito sua conta de usuário do Azure AD B2C para fazer uma chamada autorizada a uma API Web protegida pelo Azure AD B2C.

## <a name="clean-up-resources"></a>Limpar recursos

Você pode usar o locatário do Azure AD B2C se planeja experimentar outros tutoriais ou inícios rápidos do Azure AD B2C. Quando não for mais necessário, você poderá [excluir o locatário do Azure AD B2C](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você usou um aplicativo ASP.NET de exemplo para:

* Entrar com uma página de logon personalizada
* Entrar com um provedor de identidade social
* Criar uma conta do Azure AD B2C
* Chamar uma API Web protegida pelo Azure AD B2C

Introdução à criação de seu próprio locatário do Azure AD B2C.

> [!div class="nextstepaction"]
> [Criar um locatário do Azure Active Directory B2C no Portal do Azure](tutorial-create-tenant.md)
