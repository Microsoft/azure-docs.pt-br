---
title: 'Tutorial: Autenticar usuários em um aplicativo cliente nativo'
titleSuffix: Azure AD B2C
description: Tutorial sobre como usar o Azure Active Directory B2C para fornecer o logon do usuário para um aplicativo da área de trabalho .NET.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/12/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 06d27c3a3daa4702653a2063d0ac70fd094e2d74
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "78186192"
---
# <a name="tutorial-authenticate-users-in-a-native-desktop-client-using-azure-active-directory-b2c"></a>Tutorial: Autenticar usuários em um cliente de área de trabalho nativo usando o Azure Active Directory B2C

Este tutorial mostra como usar o Azure AD B2C (Azure Active Directory B2C) para inscrever e conectar usuários em um aplicativo da área de trabalho do WPF (Windows Presentation Foundation). O Azure AD B2C permite que seus aplicativos se autentiquem com contas sociais, corporativas e do Azure Active Directory usando protocolos padrão abertos.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Adicionar um aplicativo cliente nativo
> * Configurar o exemplo para usar o aplicativo
> * Inscrever-se usando o fluxo de usuário

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- [Crie fluxos de usuário](tutorial-create-user-flows.md) para habilitar experiências de usuário em seu aplicativo.
- Instale o [Visual Studio 2019](https://www.visualstudio.com/downloads/) com cargas de trabalho de **desenvolvimento de área de trabalho do .NET** e **desenvolvimento Web e do ASP.NET**.

## <a name="add-the-native-client-application"></a>Adicionar um aplicativo cliente nativo

[!INCLUDE [active-directory-b2c-appreg-native](../../includes/active-directory-b2c-appreg-native.md)]

Registre a **ID do aplicativo (cliente)** para uso em uma etapa posterior.

## <a name="configure-the-sample"></a>Configurar o exemplo

Neste tutorial, você deve configurar um exemplo que pode ser baixado do GitHub. O aplicativo da área de trabalho WPF de exemplo demonstra a inscrição e pode chamar uma API Web protegida no Azure AD B2C. [Baixe um arquivo zip](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/msalv3.zip), [procure no repositório](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) ou clone o exemplo do GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
```

Para atualizar o aplicativo para trabalhar com seu locatário do Azure AD B2C e invocar seus fluxos de usuário em vez daqueles no locatário de demonstração padrão:

1. Abra a solução **active-directory-b2c-wpf** (`active-directory-b2c-wpf.sln`) no Visual Studio.
2. No projeto **active-directory-b2c-wpf**, abra o arquivo *App.xaml.cs* e localize as seguintes definições de variáveis. Substitua `{your-tenant-name}` pelo nome do locatário do Azure AD B2C e `{application-ID}` pela ID do aplicativo que você registrou anteriormente.

    ```csharp
    private static readonly string Tenant = "{your-tenant-name}.onmicrosoft.com";
    private static readonly string AzureAdB2CHostname = "{your-tenant-name}.b2clogin.com";
    private static readonly string ClientId = "{application-ID}";
    ```

3. Atualize as variáveis de nome de política com os nomes dos fluxos de usuário que você criou como parte dos pré-requisitos. Por exemplo:

    ```csharp
    public static string PolicySignUpSignIn = "B2C_1_signupsignin1";
    public static string PolicyEditProfile = "B2C_1_profileediting1";
    public static string PolicyResetPassword = "B2C_1_passwordreset1";
    ```

## <a name="run-the-sample"></a>Execute o exemplo

Pressione **F5** para criar e executar o exemplo.

### <a name="sign-up-using-an-email-address"></a>Criar conta usando um endereço de email

1. Selecione **Entrar** para inscrever-se como um usuário. Isso usa o fluxo de usuário **B2C_1_signupsignin1**.
2. O Azure AD B2C apresenta uma página de entrada com um link **Inscrever-se agora**. Como você ainda não tem uma conta, selecione o link **Inscrever-se agora**.
3. O fluxo de trabalho de inscrição apresenta uma página para coletar e verificar a identidade do usuário usando um endereço de email. O fluxo de trabalho de inscrição também coleta a senha do usuário e os atributos solicitados definidos no fluxo de usuário.

    Use um endereço de email válido e valide-o usando o código de verificação. Defina uma senha. Insira valores para os atributos necessários.

    ![Página de inscrição mostrada como parte do fluxo de trabalho de entrada/inscrição](./media/tutorial-desktop-app/azure-ad-b2c-sign-up-workflow.png)

4. Selecione **Criar** para criar uma conta local no locatário do Azure AD B2C.

O usuário pode agora usar seu endereço de email para entrar e usar o aplicativo da área de trabalho. Após uma inscrição ou entrada bem-sucedida, os detalhes do token são exibidos no painel inferior do aplicativo do WPF.

![Detalhes do token mostrados no painel inferior do aplicativo da área de trabalho do WPF](./media/tutorial-desktop-app/desktop-app-01-post-signin.png)

Se você selecionar o botão **Chamar API**, uma **mensagem de erro** será exibida. Você encontra o erro porque, em seu estado atual, o aplicativo está tentando acessar uma API protegida pelo locatário de demonstração, `fabrikamb2c.onmicrosoft.com`. Como seu token de acesso só é válido para o locatário do Azure AD B2C, a chamada à API não é autorizada.

Continue para o próximo tutorial a fim de registrar uma API Web protegida em seu próprio locatário e habilitar a funcionalidade **Chamar API**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Adicionar um aplicativo cliente nativo
> * Configurar o exemplo para usar o aplicativo
> * Inscrever-se usando o fluxo de usuário

Em seguida, para habilitar a funcionalidade de botão de **Chamar API**, conceda ao aplicativo da área de trabalho do WPF acesso a uma API Web registrada em seu próprio locatário do Azure AD B2C:

> [!div class="nextstepaction"]
> [Tutorial: Permitir acesso a uma API Web do Node.js em um aplicativo da área de trabalho >](tutorial-desktop-app-webapi.md)
