---
title: Tutorial – Habilitar autenticação um aplicativo Web – Azure Active Directory B2C | Microsoft Docs
description: Tutorial sobre como usar o Azure Active Directory B2C para fornecer o logon do usuário para um aplicativo Web ASP.NET.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: bcfd1ef02c68de7709cb8642b94f23a6884ea156
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68464755"
---
# <a name="tutorial-enable-authentication-in-a-web-application-using-azure-active-directory-b2c"></a>Tutorial: Habilitar autenticação em um aplicativo Web usando o Azure Active Directory B2C

Este tutorial mostra como usar o Azure AD (Azure Active Directory) B2C para inscrever e conectar usuários em um aplicativo Web ASP .NET. O Azure AD B2C permite que seus aplicativos se autentiquem com contas sociais, corporativas e do Azure Active Directory usando protocolos padrão abertos.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Atualizar o aplicativo no Azure AD B2C
> * Configurar o exemplo para usar o aplicativo
> * Inscrever-se usando o fluxo de usuário

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- [Crie fluxos de usuário](tutorial-create-user-flows.md) para habilitar experiências de usuário em seu aplicativo.
- Instale o [Visual Studio 2019](https://www.visualstudio.com/downloads/) com a carga de trabalho de **desenvolvimento Web e do ASP.NET**.

## <a name="update-the-application"></a>Atualizar o aplicativo

No tutorial concluído como parte dos pré-requisitos, você adicionou um aplicativo Web no Azure AD B2C. Para habilitar a comunicação com o exemplo neste tutorial, é necessário adicionar um URI de redirecionamento ao aplicativo no Azure AD B2C.

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C clicando no **filtro Diretório e assinatura** no menu superior e escolhendo o diretório que contém seu locatário.
3. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
4. Selecione **Aplicativos** e, em seguida, selecione o aplicativo *webapp1*.
5. Em **URL de resposta**, adicione `https://localhost:44316`.
6. Clique em **Salvar**.
7. Na página de propriedades, registre a ID do aplicativo que você usará ao configurar o aplicativo Web.
8. Selecione **Chaves**, **Gerar chave** e selecione **Salvar**. Registre a chave que você usará ao configurar o aplicativo Web.

## <a name="configure-the-sample"></a>Configurar o exemplo

Neste tutorial, você deve configurar um exemplo que pode ser baixado do GitHub. O exemplo usa o ASP.NET para fornecer uma lista de tarefas pendentes simples. O exemplo usa os [componentes de middleware OWIN da Microsoft](https://docs.microsoft.com/aspnet/aspnet/overview/owin-and-katana/). [Baixe um arquivo zip](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip) ou clone o exemplo do GitHub. Extraia o arquivo de exemplo em uma pasta cujo tamanho total de caracteres do caminho seja menor que 260.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

A seguir, há dois projetos estão na solução de exemplo:

- **TaskWebApp** – Crie e edite uma lista de tarefas. O exemplo usa o fluxo de usuário de **inscrição ou conexão** para inscrever ou conectar usuários.
- **TaskService** – Dá suporte à funcionalidade de criar, ler, atualizar e excluir a lista de tarefas. A API é protegida pelo Azure AD B2C e chamada pelo TaskWebApp.

Altere o exemplo para usar o aplicativo registrado no locatário, que inclui a ID do aplicativo e a chave registrada anteriormente. Além disso, configure os fluxos de usuário que você criou. O exemplo define os valores de configuração como configurações no arquivo Web.config. Para alterar as configurações:

1. Abra a solução **B2C-WebAPI-DotNet** no Visual Studio.
2. No projeto **TaskWebApp**, abra o arquivo **Web.config**. Substitua o valor de `ida:Tenant` pelo nome do locatário que você criou. Substitua o valor de `ida:ClientId` pela ID do aplicativo que você registrou. Substitua o valor de `ida:ClientSecret` pela chave que você registrou. É necessário codificar o segredo do cliente em XML antes de adicioná-lo a Web.config.
3. No arquivo **Web.config**, substitua o valor de `ida:SignUpSignInPolicyId` por `b2c_1_signupsignin1`. Substitua o valor de `ida:EditProfilePolicyId` por `b2c_1_profileediting1`. Substitua o valor de `ida:ResetPasswordPolicyId` por `b2c_1_passwordreset1`.


## <a name="run-the-sample"></a>Execute o exemplo

1. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **TaskWebApp** e, em seguida, clique em **Definir como Projeto de Inicialização**.
2. Pressione **F5**. O navegador padrão inicia no endereço do site local `https://localhost:44316/`.

### <a name="sign-up-using-an-email-address"></a>Criar conta usando um endereço de email

1. Clique em **Inscrever-se/Entrar** para inscrever-se como um usuário do aplicativo. O fluxo de usuário **b2c_1_signupsignin1** é usado.
2. O Azure AD B2C apresenta uma página de entrada com um link de inscrição. Como você ainda não tem uma conta, selecione **Inscrever-se agora**. O fluxo de trabalho de inscrição apresenta uma página para coletar e verificar a identidade do usuário usando um endereço de email. O fluxo de trabalho de inscrição também coleta a senha do usuário e os atributos solicitados definidos no fluxo de usuário.
3. Use um endereço de email válido e valide-o usando o código de verificação. Defina uma senha. Insira valores para os atributos necessários.

    ![Página de inscrição mostrada como parte do fluxo de trabalho de entrada/inscrição](media/active-directory-b2c-tutorials-web-app/sign-up-workflow.PNG)

4. Clique em **Criar** para criar uma conta local no locatário do Azure AD B2C.

Agora o usuário pode usar seu endereço de email para entrar e usar o aplicativo Web.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Atualizar o aplicativo no Azure AD B2C
> * Configurar o exemplo para usar o aplicativo
> * Inscrever-se usando o fluxo de usuário

> [!div class="nextstepaction"]
> [Tutorial: usar o Azure Active Directory B2C para proteger uma ASP.NET Web API](active-directory-b2c-tutorials-web-api.md)
