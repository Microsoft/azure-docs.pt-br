---
title: "Introdução ao AngularJS do AD do Azure | Microsoft Docs"
description: "Como criar um aplicativo de página única AngularJS que se integre ao Azure AD para entrar e que chame as APIs protegidas do Azure AD usando OAuth."
services: active-directory
documentationcenter: 
author: jmprieur
manager: mbaldwin
editor: 
ms.assetid: f2991054-8146-4718-a5f7-59b892230ad7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 01/07/2017
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 4153910bc03f112f84c26cda6f9c78f11028b934
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="help-secure-angularjs-single-page-apps-by-using-azure-ad"></a>Ajudar a proteger aplicativos de página única AngularJS usando o AD do Azure

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

O Azure AD (Azure Active Directory) faz com que seja simples e direto adicionar entrada e saída e proteger chamadas de API do OAuth para seus aplicativos de página única.  Ele habilita seu aplicativo a autenticar usuários com suas contas do Active Directory do Windows Server e consuma qualquer API Web que o Azure AD ajuda a proteger, como as APIs do Office 365 ou a API do Azure.

Para aplicativos JavaScript em execução em um navegador, o Azure AD fornece a Biblioteca de Autenticação do Active Directory (ADAL) ou adal.js. A única finalidade da adal.js é facilitar para seu aplicativo a obtenção de tokens de acesso. Para demonstrar como é fácil, vamos compilar aqui um aplicativo de lista de tarefas AngularJS que:

* Conecta o usuário ao aplicativo usando o Azure AD como o provedor de identidade.

* Exibe informações sobre o usuário.
* Chama com segurança a API de lista de tarefas do aplicativo usando tokens de portador do Azure AD.
* Desconecta o usuário do aplicativo.

Para criar o aplicativo em funcionamento completo, será necessário:

1. Registrar um aplicativo no Azure AD.
2. Instalar a ADAL e configurar o aplicativo de página única.
3. Use o ADAL para páginas seguras no aplicativo de página única.

Para começar, [baixe o esqueleto do aplicativo](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/skeleton.zip) ou [baixe o exemplo concluído](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip). Você também precisará de um locatário do Azure AD no qual possa criar usuários e registrar um aplicativo. Se você ainda não tiver um locatário [saiba como obter um](active-directory-howto-tenant.md).

## <a name="step-1-register-the-directorysearcher-application"></a>Etapa 1: Registrar o aplicativo DirectorySearcher
Para permitir que seu aplicativo autentique usuários e obtenha tokens, primeiro você precisará registrá-lo em seu locatário do Azure AD:

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Se você entrar em vários diretórios, precisará garantir que está exibindo o diretório correto. Para fazer isso, na barra superior, clique em sua conta. Sob o **diretório** lista, escolha o locatário do AD do Azure onde você deseja registrar seu aplicativo.
3. Clique em **Mais Serviços** no painel esquerdo e selecione **Azure Active Directory**.
4. Clique em **Registros do aplicativo**e, em seguida, selecione **Adicionar**.
5. Siga os prompts e crie um novo aplicativo Web e/ou API Web:
  * **Nome** descreve seu aplicativo para os usuários.
  * O **URI de redirecionamento** é o local no qual o AAD retornará tokens. O local padrão deste exemplo é `https://localhost:44326/`.
6. Depois de concluir o registro, o Azure AD atribui uma identificação exclusiva do aplicativo ao seu aplicativo.  Você precisará desse valor nas próximas seções, portanto, copie-o da guia do aplicativo.
7. A adal.js usa o fluxo de OAuth implícito para se comunicar com o AD do Azure. Você deve habilitar o fluxo implícito para seu aplicativo:
  1. Clique no aplicativo e escolha **Manifesto** para abrir o editor de manifesto embutido.
  2. Localize a propriedade `oauth2AllowImplicitFlow`. Defina seu valor como `true`.
  3. Clique em **Salvar** para salvar o manifesto.
8. Conceda permissões em seu locatário para seu aplicativo. Acesse **Configurações** > **Propriedades** > **Permissões Necessárias** e clique no botão **Conceder Permissões** na barra superior. Clique em **Sim** para confirmar.

## <a name="step-2-install-adal-and-configure-the-single-page-app"></a>Etapa 2: Instalar o ADAL e configurar o aplicativo de página única
Agora que você tem um aplicativo no AD do Azure, você pode instalar a adal.js e escrever seu código relacionado à identidade.

### <a name="configure-the-javascript-client"></a>Configurar o cliente JavaScript
Comece adicionando a adal.js ao projeto TodoSPA usando o Console do Gerenciador de Pacotes:
  1. Baixe [adal.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal.js) e adicione-a ao diretório do projeto `App/Scripts/`.
  2. Baixe [adal-angular.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal-angular.js) e adicione-a ao diretório do projeto `App/Scripts/`.
  3. Carregue cada script antes do final do `</body>` in `index.html`:

    ```js
    ...
    <script src="App/Scripts/adal.js"></script>
    <script src="App/Scripts/adal-angular.js"></script>
    ...
    ```

### <a name="configure-the-back-end-server"></a>Configurar o servidor de back-end
Para que a API de lista de tarefas do back-end do aplicativo de página única aceite tokens do navegador, o back-end precisa de informações de configuração sobre o registro do aplicativo. No projeto TodoSPA, abra `web.config`. Substitua os valores dos elementos na seção `<appSettings>` para refletir os valores que você usou no portal do Azure. Seu código fará referência a esses valores sempre que ele usar a ADAL.
  * O `ida:Tenant` é o domínio do seu locatário do Azure AD, por exemplo, contoso.onmicrosoft.com.
  * O `ida:Audience` é a ID do cliente do seu aplicativo que você copiou do portal.

## <a name="step-3-use-adal-to-help-secure-pages-in-the-single-page-app"></a>Etapa 3: Usar o ADAL para ajudar a páginas seguras no aplicativo de página única
O Adal.js integra-se ao roteiro AngularJS e aos provedores HTTP e, portanto, você pode ajudar a proteger exibições individuais em seu aplicativo de página única.

1. Em `App/Scripts/app.js`, adicione o módulo adal.js:

    ```js
    angular.module('todoApp', ['ngRoute','AdalAngular'])
    .config(['$routeProvider','$httpProvider', 'adalAuthenticationServiceProvider',
     function ($routeProvider, $httpProvider, adalProvider) {
    ...
    ```
2. Inicialize o `adalProvider` usando os valores de configuração de seu registro de aplicativo, também no `App/Scripts/app.js`:

    ```js
    adalProvider.init(
      {
          instance: 'https://login.microsoftonline.com/',
          tenant: 'Enter your tenant name here e.g. contoso.onmicrosoft.com',
          clientId: 'Enter your client ID here e.g. e9a5a8b6-8af7-4719-9821-0deef255f68e',
          extraQueryParameter: 'nux=1',
          //cacheLocation: 'localStorage', // enable this for IE, as sessionStorage does not work for localhost.
      },
      $httpProvider
    );
    ```
3. Ajudar a proteger o `TodoList` exibição no aplicativo, usando apenas uma linha de código: `requireADLogin`.

    ```js
    ...
    }).when("/TodoList", {
            controller: "todoListCtrl",
            templateUrl: "/App/Views/TodoList.html",
            requireADLogin: true,
    ...
    ```

## <a name="summary"></a>Resumo
Agora você tem um aplicativo de página única seguro que pode conectar usuários e emitir solicitações protegidas de token de portador para sua API de back-end. Quando um usuário clica no link **TodoList**, o adal.js o redireciona automaticamente ao Azure AD para conexão, se for necessário. Além disso, o adal.js anexa automaticamente um access_token para todas as solicitações Ajax que são enviadas ao back-end do aplicativo.  

As etapas anteriores são o mínimo necessário para criar um aplicativo de página única usando adal.js. Mas alguns outros recursos são úteis no aplicativo de página única:

* Para emitir explicitamente solicitações de entrada e de saída, você pode definir funções em seus controladores que invocam adal.js.  Em `App/Scripts/homeCtrl.js`:

    ```js
    ...
    $scope.login = function () {
        adalService.login();
    };
    $scope.logout = function () {
        adalService.logOut();
    };
    ...
    ```
* Você poderá querer apresentar informações do usuário na interface do usuário do aplicativo. O serviço ADAL já foi adicionado ao controlador `userDataCtrl` e, portanto, você pode acessar o objeto `userInfo` na exibição associada, `App/Views/UserData.html`:

    ```js
    <p>{{userInfo.userName}}</p>
    <p>aud:{{userInfo.profile.aud}}</p>
    <p>iss:{{userInfo.profile.iss}}</p>
    ...
    ```

* há muitos cenários nos quais você pode querer saber se o usuário está conectado ou não. Você também pode usar o objeto `userInfo` para coletar essas informações.  Por exemplo, em `index.html`, você pode mostrar o botão **Logon** ou **Logout** com base no status de autenticação:

    ```js
    <li><a class="btn btn-link" ng-show="userInfo.isAuthenticated" ng-click="logout()">Logout</a></li>
    <li><a class="btn btn-link" ng-hide=" userInfo.isAuthenticated" ng-click="login()">Login</a></li>
    ```

O aplicativo de página única integrado ao AD do Azure pode autenticar os usuários, com segurança chamar seu back-end usando OAuth 2.0 e obter informações básicas sobre o usuário. Se você ainda não fez isso, agora é o momento de preencher seu locatário com alguns usuários. Execute o aplicativo de página única da lista de tarefas e entre com um desses usuários. Adicione tarefas à lista do usuário, saia e entre novamente.

Adal.js facilita a incorporar recursos comuns de identidade em seu aplicativo. Ele se encarrega de todo o trabalho difícil para você - gerenciamento de cache, suporte a protocolo OAuth, apresentação de uma IU de entrada ao usuário, atualização de tokens expirados e mais.

Para referência, o exemplo concluído (sem seus valores de configuração) está disponível no [GitHub](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip).

## <a name="next-steps"></a>Próximas etapas
Agora você pode passar para cenários de adicionais. Você talvez queira: [chamar uma API da web CORS de um aplicativo de página única](https://github.com/AzureAdSamples/SinglePageApp-WebAPI-AngularJS-DotNet).

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
