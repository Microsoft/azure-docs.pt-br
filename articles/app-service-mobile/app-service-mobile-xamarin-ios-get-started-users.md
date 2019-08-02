---
title: Introdução à autenticação para aplicativos móveis no Xamarin iOS
description: Aprenda a usar os aplicativos móveis para autenticar usuários de seu aplicativo Xamarin iOS por meio de uma variedade de provedores de identidade, incluindo AAD, Google, Facebook, Twitter e Microsoft.
services: app-service\mobile
documentationcenter: xamarin
author: elamalani
manager: crdun
editor: ''
ms.assetid: 180cc61b-19c5-48bf-a16c-7181aef3eacc
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: fa1f4bae314025a71568e1e04cbf950ebbe26dbe
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446234"
---
# <a name="add-authentication-to-your-xamarinios-app"></a>Adicionar autenticação ao aplicativo Xamarin.iOS
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> Visual Studio App Center está investindo em novos e integrados serviços essenciais para o desenvolvimento de aplicativos móveis. Os desenvolvedores podem usar **construir**, **teste** e **distribuir** services para configurar o pipeline de integração contínua e entrega. Depois que o aplicativo é implantado, os desenvolvedores podem monitorar o status e o uso do seu aplicativo usando o **Analytics** e **diagnóstico** serviços e entre em contato com usuários usando o **enviar por Push** serviço. Os desenvolvedores também podem aproveitar **Auth** autenticar seus usuários e **dados** serviço para manter e sincronizar dados do aplicativo na nuvem. Fazer check-out [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-xamarin-ios-get-started-users) hoje mesmo.
>

## <a name="overview"></a>Visão geral

Este tópico mostra como autenticar usuários de um aplicativo móvel do Serviço de Aplicativo em seu aplicativo cliente. Neste tutorial, você adiciona a autenticação ao projeto de início rápido do Xamarin.iOS usando um provedor de identidade com suporte do Serviço de Aplicativo. Depois de ser autenticado e autorizado com êxito pelo Aplicativo Móvel, o valor da ID de usuário é exibido e você poderá acessar dados da tabela restrita.

Você deve primeiro concluir o tutorial [Criar um aplicativo Xamarin.iOS]. Se você não usar o projeto baixado de início rápido do servidor, deve adicionar o pacote de extensão de autenticação ao seu projeto. Para obter mais informações sobre pacotes de extensão do servidor, confira [Trabalhar com o servidor .NET back-end do SDK para Aplicativos Móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>Registrar seu aplicativo para a autenticação e configurar os Serviços de Aplicativos
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="add-your-app-to-the-allowed-external-redirect-urls"></a>Adicionar seu aplicativo às URLs de redirecionamento externo permitidas

A autenticação segura exige que você defina um novo esquema de URL para seu aplicativo. Isso permite que o sistema de autenticação redirecione para seu aplicativo após a conclusão do processo de autenticação. Neste tutorial, usamos sempre o esquema de URL _appname_. No entanto, você pode usar o esquema de URL que quiser. Ele deve ser exclusivo para seu aplicativo móvel. Para habilitar o redirecionamento no lado do servidor:

1. No [portal do Azure](https://portal.azure.com/), selecione sua conta.

2. Clique na opção de menu **Autenticação/Autorização**.

3. Em **URLs de Redirecionamento Externo Permitidas**, insira `url_scheme_of_your_app://easyauth.callback`.  O **esquema_de_URL_do_seu_aplicativo** nessa cadeia de caracteres é o esquema de URL do seu aplicativo móvel.  Ele deve seguir as especificações de URL normal para um protocolo (use somente letras e números e inicie com uma letra).  Você deve anotar a cadeia de caracteres escolhida, já que precisará ajustar o código do aplicativo móvel com o esquema de URL em vários lugares.

4. Clique em **OK**.

5. Clique em **Salvar**.

## <a name="restrict-permissions-to-authenticated-users"></a>Restringir permissões a usuários autenticados
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* No Visual Studio ou Xamarin Studio, execute o projeto cliente em um dispositivo ou emulador. Verifique se uma exceção não tratada com um código de status 401 (Não autorizado) é gerada após o aplicativo ser iniciado. A falha será registrada no console do depurador. Então no Visual Studio, você deve ver a falha na janela de saída.

    Essa falha não autorizada acontece porque o aplicativo tenta acessar o back-end do aplicativo móvel como um usuário não autenticado. A tabela *TodoItem* agora exige autenticação.

Em seguida, você atualizará o aplicativo do cliente para solicitar recursos do back-end do aplicativo móvel com um usuário autenticado.

## <a name="add-authentication-to-the-app"></a>Adicionar autenticação ao aplicativo
Nesta seção, você modificará o aplicativo para exibir uma tela de logon antes de exibir os dados. Quando o aplicativo for iniciado, ele não será conectado ao Serviço de Aplicativo e não exibirá dados. Depois que o usuário executar pela primeira vez um gesto de atualização, a tela de logon aparecerá e, após o êxito no logon, a lista de itens de tarefas pendentes será exibida.

1. No projeto do cliente, abra o arquivo **QSTodoService.cs** e adicione a seguinte instrução using e `MobileServiceUser` com acessador à classe QSTodoService:

    ```csharp
    using UIKit;

    // Logged in user
    private MobileServiceUser user;
    public MobileServiceUser User { get { return user; } }
    ```

2. Adicione um novo método chamado **Authenticate** ao **QSTodoService** com a seguinte definição:

    ```csharp
    public async Task Authenticate(UIViewController view)
    {
        try
        {
            AppDelegate.ResumeWithURL = url => url.Scheme == "{url_scheme_of_your_app}" && client.ResumeWithURL(url);
            user = await client.LoginAsync(view, MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
        }
        catch (Exception ex)
        {
            Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
        }
    }
    ```

    > [!NOTE]
    > Se você estiver usando um provedor de identidade que não seja o Facebook, altere o valor passado para **LoginAsync** acima para um dos seguintes: _MicrosoftAccount_, _Twitter_, _Google_ ou _WindowsAzureActiveDirectory_.

3. Abra o **QSTodoListViewController.cs**. Modifique a definição do método de **ViewDidLoad** removendo a chamada para **RefreshAsync()** perto do final:

    ```csharp
    public override async void ViewDidLoad ()
    {
        base.ViewDidLoad ();

        todoService = QSTodoService.DefaultService;
        await todoService.InitializeStoreAsync();

        RefreshControl.ValueChanged += async (sender, e) => {
            await RefreshAsync();
        }

        // Comment out the call to RefreshAsync
        // await RefreshAsync();
    }
    ```

4. Modifique o método **RefreshAsync** se a propriedade **User** for null. Adicione o seguinte código à parte superior da definição do método:

    ```csharp
    // start of RefreshAsync method
    if (todoService.User == null) {
        await QSTodoService.DefaultService.Authenticate(this);
        if (todoService.User == null) {
            Console.WriteLine("couldn't login!!");
            return;
        }
    }
    // rest of RefreshAsync method
    ```

5. Abra **AppDelegate.cs** e adicione o seguinte método:

    ```csharp
    public static Func<NSUrl, bool> ResumeWithURL;

    public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
    {
        return ResumeWithURL != null && ResumeWithURL(url);
    }
    ```

6. Abra o arquivo **Info.plist**, navegue até **Tipos de URL** na seção **Avançado**. Agora configure o **Identificador** e os **Esquemas de URL** do Tipo de URL e clique em **Adicionar Tipo de URL**. Os **Esquemas de URL** devem ser o mesmo que o {esquema_de_URL_do_seu_aplicativo}.
7. No Visual Studio, conectado ao Host do Mac ou Visual Studio para Mac, execute o projeto do cliente direcionando um dispositivo ou emulador. Verifique se o aplicativo não exibe dados.

    Faça um gesto de atualização pressionando a lista de itens, o que fará com que a tela de logon apareça. Depois de fornecer credenciais válidas, o aplicativo exibirá a lista de itens de tarefas e você poderá atualizar os dados.

<!-- URLs. -->
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Criar um aplicativo Xamarin.iOS]: app-service-mobile-xamarin-ios-get-started.md
