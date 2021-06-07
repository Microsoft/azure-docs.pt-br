---
title: 'Tutorial: Permitir acesso a uma API Web do Node.js em um aplicativo da área de trabalho'
description: Tutorial sobre como usar o Active Directory B2C para proteger uma API Web do Node.js e chamá-la de um aplicativo da área de trabalho do .NET.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/12/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 12951f25feb6f3710b8d35fbc682caeeb480e788
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100555609"
---
# <a name="tutorial-grant-access-to-a-nodejs-web-api-from-a-desktop-app-using-azure-active-directory-b2c"></a>Tutorial: Permitir acesso a uma API Web do Node.js de um aplicativo da área de trabalho usando o Azure Active Directory B2C

Este tutorial mostra como chamar uma API Web do Node.js protegida pelo Azure AD B2C (Azure Active Directory B2C) em um aplicativo da área de trabalho do WPF (Windows Presentation Foundation), também protegido pelo Azure AD B2C.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Adicionar um aplicativo API Web
> * Configurar escopos para uma API Web
> * Conceder permissões à API Web
> * Atualizar o exemplo para usar o aplicativo

## <a name="prerequisites"></a>Pré-requisitos

Conclua as etapas e os pré-requisitos no [Tutorial: Autentique os usuários em um cliente de área de trabalho nativo](tutorial-desktop-app.md).

## <a name="add-a-web-api-application"></a>Adicionar um aplicativo API Web

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>Configurar escopos

Os escopos fornecem uma maneira de controlar o acesso a recursos protegidos. Escopos são usados pela API Web para implementar o controle de acesso com base em escopo. Por exemplo, alguns usuários podem ter acesso de leitura e gravação, enquanto outros usuários podem ter permissões somente leitura. Neste tutorial, você define as permissões de leitura e gravação para a API Web.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

Registre o valor em **ESCOPOS** do escopo `demo.read` a ser usado em uma etapa posterior ao configurar o aplicativo de área de trabalho. O valor de escopo completo é semelhante a `https://contosob2c.onmicrosoft.com/api/demo.read`.

## <a name="grant-permissions"></a>Conceder permissões

Para chamar uma API Web de um aplicativo cliente nativo, você precisa conceder ao aplicativo cliente nativo registrado permissões para a API Web que você registrou no Azure AD B2C.

No tutorial de pré-requisito, você registrou um aplicativo cliente nativo chamado *nativeapp1*. As etapas a seguir configuram esse registro de aplicativo nativo com os escopos de API expostos para *webapi1* na seção anterior. Isso permite que o aplicativo de área de trabalho obtenha um token de acesso do Azure AD B2C que a API Web pode usar para verificar e fornecer acesso com escopo para seus recursos. Você configurará e executará os exemplos de código do aplicativo de área de trabalho e da API Web posteriormente neste tutorial.

Para registrar um aplicativo no locatário do Azure AD B2C, você pode usar a nossa nova experiência unificada de **Registros de aplicativos** ou a nossa experiência herdada **Aplicativos (Herdado)** . [Saiba mais sobre a nova experiência](./app-registrations-training-guide.md).

#### <a name="app-registrations"></a>[Registros de aplicativo](#tab/app-reg-ga/)

1. Selecione **Registros de aplicativo** e, em seguida, selecione o aplicativo cliente nativo que deve ter acesso à API. Por exemplo, *nativeapp1*.
1. Em **Gerenciar**, selecione **Permissões de API**.
1. Em **Permissões Configuradas**, selecione **Adicionar uma permissão**.
1. Selecione a guia **Minhas APIs**.
1. Selecione a API à qual o aplicativo cliente nativo deve receber acesso. Por exemplo, *webapi1*.
1. Em **Permissão**, expanda **demonstração** e, em seguida, selecione os escopos definidos anteriormente. Por exemplo, *demo.read* e *demo.write*.
1. Selecione **Adicionar Permissões**. Conforme as instruções, aguarde alguns minutos antes de seguir para a próxima etapa.
1. Selecione **Fornecer o consentimento do administrador para (nome do seu locatário)** .
1. Selecione a conta de administrador conectada no momento ou entre com uma conta no seu locatário do Azure AD B2C que tenha recebido, pelo menos, a função *Administrador de aplicativos de nuvem*.
1. Selecione **Aceitar**.
1. Selecione **Atualizar** e, em seguida, verifique se "Concedido para..." aparece em **Status** para ambos os escopos. Pode levar alguns minutos para que as permissões sejam propagadas.

#### <a name="applications-legacy"></a>[Aplicativos (Herdado)](#tab/applications-legacy/)

1. Selecione **Aplicativos (Herdado)** e, em seguida, selecione *nativeapp1*.
1. Selecione **Acesso à API** e, em seguida, selecione **Adicionar**.
1. No menu suspenso **Selecionar API**, selecione *webapi1*.
1. Na lista suspensa **Selecionar Escopos**, selecione os escopos que você definiu anteriormente. Por exemplo, *demo.read* e *demo.write*.
1. Selecione **OK**.

* * *

Um usuário autentica-se com o Azure AD B2C para usar o aplicativo da área de trabalho do WPF. O aplicativo da área de trabalho obtém uma concessão de autorização do Azure AD B2C para acessar a API Web protegida.

## <a name="configure-the-samples"></a>Configurar os exemplos

Agora que a API Web está registrada e os escopos e permissões estão configurados, é preciso configurar os exemplos do aplicativo de área de trabalho e da API Web para utilizar seu locatário do Azure AD B2C.

### <a name="update-the-desktop-application"></a>Atualizar o aplicativo de área de trabalho

Em um pré-requisito para este artigo, você modificou um [aplicativo de área de trabalho WPF](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) para habilitar o login com um fluxo de usuário em seu locatário do Azure AD B2C. Nesta seção, você atualizará esse mesmo aplicativo para fazer referência à API Web registrada anteriormente: *webapi1*.

1. Abra a solução **active-directory-b2c-wpf** (`active-directory-b2c-wpf.sln`) no Visual Studio.
1. No projeto **active-directory-b2c-wpf**, abra o arquivo *App.xaml.cs* e localize as seguintes definições de variáveis.
    1. Substitua o valor da variável `ApiScopes` pelo valor registrado anteriormente quando você definiu o escopo **demo.read**.
    1. Substitua o valor da variável `ApiEndpoint` pelo **URI de redirecionamento** registrado anteriormente quando você registrou a API Web (por exemplo, *webapi1*) em seu locatário.

    Aqui está um exemplo:

    ```csharp
    public static string[] ApiScopes = { "https://contosob2c.onmicrosoft.com/api/demo.read" };
    public static string ApiEndpoint = "http://localhost:5000";
    ```

### <a name="get-and-update-the-nodejs-api-sample"></a>Obter e atualizar o exemplo da API Node.js

Em seguida, obtenha o exemplo de código da API Web Node.js do GitHub e configure-o para usar a API Web registrada no seu locatário do Azure Active Directory B2C.

[Baixe um arquivo zip](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip) ou clone o aplicativo Web de exemplo do GitHub.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

O exemplo de API Web Node.js usa a biblioteca de Passport.js para habilitar o Azure AD B2C a proteger chamadas à API.

1. Abra o arquivo `index.js` .
1. Atualize as definições dessas variáveis com os valores a seguir. Altere `<web-API-application-ID>` para o **ID (cliente) do aplicativo** da API Web registrada anteriormente (*webapi1*). Altere `<your-b2c-tenant>` para o nome de seu locatário do Azure AD B2C.

    ```nodejs
    var clientID = "<web-API-application-ID>";
    var b2cDomainHost = "<your-b2c-tenant>.b2clogin.com";
    var tenantIdGuid = "<your-b2c-tenant>.onmicrosoft.com";
    var policyName = "B2C_1_signupsignin1";
    ```
1. Como você está executando a API localmente, atualize o caminho na rota para o método GET para `/` em vez de a localização do aplicativo demo de `/hello`:

    ```nodejs
    app.get("/",
    ```

## <a name="run-the-samples"></a>Executar as amostras

### <a name="run-the-nodejs-web-api"></a>Executar a API Web Node.Js

1. Inicie um prompt de comando de Node.js.
2. Altere o diretório que contém o exemplo de Node.js. Por exemplo `cd c:\active-directory-b2c-javascript-nodejs-webapi`
3. Execute os seguintes comandos:
    ```console
    npm install && npm update
    ```
    ```console
    node index.js
    ```

### <a name="run-the-desktop-application"></a>Executar o aplicativo da área de trabalho

1. Abra a solução **active-directory-b2c-wpf** no Visual Studio.
2. Pressione **F5** para executar o aplicativo de desktop.
3. Entre usando o endereço de email e a senha usada no [tutorial Autenticar usuários com o Azure Active Directory B2C em um aplicativo de desktop](tutorial-desktop-app.md).
4. Selecione o botão **Chamar API**.

O aplicativo de área de trabalho faz uma solicitação para a API Web executada localmente e, após a verificação de um token de acesso válido, mostra o nome de exibição do usuário conectado.

![Nome de exibição mostrado no painel superior do aplicativo de área de trabalho WPF](./media/tutorial-desktop-app-webapi/desktop-app-01-post-api-call.png)

Seu aplicativo de área de trabalho, protegido pelo Azure AD B2C, está chamando a API Web executada localmente, que também é protegida pelo Azure AD B2C.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Adicionar um aplicativo API Web
> * Configurar escopos para uma API Web
> * Conceder permissões à API Web
> * Atualizar o exemplo para usar o aplicativo

> [!div class="nextstepaction"]
> [Adicionar provedores de identidade a seus aplicativos no Azure Active Directory B2C](add-identity-provider.md)