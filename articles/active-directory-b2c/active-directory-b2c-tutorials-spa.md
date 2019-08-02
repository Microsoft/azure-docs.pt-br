---
title: Tutorial – Habilitar a autenticação em um aplicativo de página única – Azure Active Directory B2C
description: Saiba como usar o Azure Active Directory B2C para fornecer o logon do usuário para um aplicativo de página única (JavaScript).
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 07/24/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 6884cb7b10da3996977f2aea7693625bc45c3139
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68369566"
---
# <a name="tutorial-enable-authentication-in-a-single-page-application-using-azure-active-directory-b2c"></a>Tutorial: Habilitar autenticação em um aplicativo de página única usando o Azure Active Directory B2C

Este tutorial mostra como usar o Azure AD (Azure Active Directory) B2C para inscrever e conectar usuários em um SPA (aplicativo de página única). O Azure AD B2C permite que seus aplicativos se autentiquem com contas sociais, corporativas e do Azure Active Directory usando protocolos padrão abertos.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Atualizar o aplicativo no Azure AD B2C
> * Configurar o exemplo para usar o aplicativo
> * Inscrever-se usando o fluxo de usuário

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Você precisará dos seguintes recursos do Azure AD B2C em vigor antes de continuar com as etapas deste tutorial:

* [Locatário do Azure AD B2C](tutorial-create-tenant.md)
* [Aplicativo registrado](tutorial-register-applications.md) em seu locatário
* [Fluxos dos usuários criados](tutorial-create-user-flows.md) em seu locatário

Além disso, você precisará do seguinte no ambiente de desenvolvimento local:

* Editor de códigos, por exemplo, [Visual Studio Code](https://code.visualstudio.com/) ou [Visual Studio 2019](https://www.visualstudio.com/downloads/)
* [SDK do .NET Core 2.2](https://dotnet.microsoft.com/download) ou posterior
* [Node.js](https://nodejs.org/en/download/)

## <a name="update-the-application"></a>Atualizar o aplicativo

No segundo tutorial concluído como parte dos pré-requisitos, você registrou um aplicativo Web no Azure AD B2C. Para habilitar a comunicação com o exemplo no tutorial, é necessário adicionar um URI de redirecionamento ao aplicativo no Azure AD B2C.

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C clicando no **filtro Diretório e assinatura** no menu superior e escolhendo o diretório que contém seu locatário.
1. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
1. Selecione **Aplicativos** e, em seguida, selecione o aplicativo *webapp1*.
1. Em **URL de resposta**, adicione `http://localhost:6420`.
1. Clique em **Salvar**.
1. Na página de propriedades, registre a **ID do Aplicativo**. Você pode usar a ID do aplicativo em uma etapa posterior ao atualizar o código no aplicativo Web de página única.

## <a name="get-the-sample-code"></a>Obter o código de amostra

Neste tutorial, você configurará um exemplo de código baixado do GitHub. A amostra descreve como um aplicativo de página única pode usar o Azure AD B2C para a inscrição e a entrada de usuário e para chamar uma API Web protegida.

[Baixe um arquivo zip](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) ou clone o exemplo do GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

## <a name="update-the-sample"></a>Atualizar a amostra

Agora que você já obteve a amostra, atualize o código com o nome do locatário do Azure AD B2C e a ID do aplicativo registrada em uma etapa anterior.

1. Abra o arquivo `index.html` na raiz do diretório de exemplo.
1. Na definição `msalConfig`, modifique o valor de **clientId** com a ID do aplicativo registrada em uma etapa anterior. Em seguida, atualize o valor de URI **authority** com o nome do locatário do Azure AD B2C. Atualize também o URI com o nome do fluxo de usuário de inscrição/entrada criado em um dos pré-requisitos (por exemplo, *B2C_1_signupsignin1*).

    ```javascript
    var msalConfig = {
        auth: {
            clientId: "00000000-0000-0000-0000-000000000000", //This is your client ID
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_susi", //This is your tenant info
            validateAuthority: false
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    ```

    O nome do fluxo de usuário usado neste tutorial é **B2C_1_signupsignin1**. Se estiver usando outro nome de fluxo, especifique esse nome no valor `authority`.

## <a name="run-the-sample"></a>Execute o exemplo

1. Abra uma janela do console e altere para o diretório que contém a amostra. Por exemplo:

    ```console
    cd active-directory-b2c-javascript-msal-singlepageapp
    ```
1. Execute os seguintes comandos:

    ```
    npm install && npm update
    node server.js
    ```

    A janela do console exibe o número da porta do servidor do Node.js em execução localmente:

    ```
    Listening on port 6420...
    ```

1. Navegue até `http://localhost:6420` no navegador para exibir o aplicativo.

O exemplo dá suporte à inscrição, conexão, edição de perfil e redefinição de senha. Este tutorial destaca como um usuário se inscreve usando um endereço de email.

### <a name="sign-up-using-an-email-address"></a>Criar conta usando um endereço de email

1. Clique em **Logon** para iniciar o fluxo de usuário *B2C_1_signupsignin1* especificado em uma etapa anterior.
1. O Azure AD B2C apresenta uma página de entrada com um link de inscrição. Como você ainda não tem uma conta, clique no link **Inscrever-se agora**.
1. O fluxo de trabalho de inscrição apresenta uma página para coletar e verificar a identidade do usuário usando um endereço de email. O fluxo de trabalho de inscrição também coleta a senha do usuário e os atributos solicitados definidos no fluxo de usuário.

    Use um endereço de email válido e valide-o usando o código de verificação. Defina uma senha. Insira valores para os atributos necessários.

    ![Página de inscrição apresentada pelo fluxo de usuário para entrada/inscrição](./media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.PNG)

1. Clique em **Criar** para criar uma conta local no diretório do Azure AD B2C.

Quando você clica em **Criar**, a página de inscrição é fechada e a página de entrada é exibida novamente.

Agora você pode usar seu endereço de email e sua senha para entrar no aplicativo.

### <a name="error-insufficient-permissions"></a>Erro: permissões insuficientes

Depois que você entrar, o aplicativo exibirá um erro de permissões insuficientes – isso é **esperado**:

```Output
ServerError: AADB2C90205: This application does not have sufficient permissions against this web resource to perform the operation.
Correlation ID: ce15bbcc-0000-0000-0000-494a52e95cd7
Timestamp: 2019-07-20 22:17:27Z
```

Você recebe esse erro porque o aplicativo Web está tentando acessar uma API Web protegida pelo diretório de demonstração, *fabrikamb2c*. Como seu token de acesso só é válido para o diretório do Azure AD, a chamada à API não é autorizada.

Para corrigir esse erro, prossiga para o próximo tutorial da série (confira as [Próximas etapas](#next-steps)) a fim de criar uma API Web protegida para o diretório.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a:

> [!div class="checklist"]
> * Atualizar o aplicativo no Azure AD B2C
> * Configurar o exemplo para usar o aplicativo
> * Inscrever-se usando o fluxo de usuário

Agora passe para o próximo tutorial da série para permitir acesso a uma API Web protegida por meio do SPA:

> [!div class="nextstepaction"]
> [Tutorial: Permitir acesso a uma API Web do ASP.NET Core de um SPA usando o Azure AD B2C >](active-directory-b2c-tutorials-spa-webapi.md)
