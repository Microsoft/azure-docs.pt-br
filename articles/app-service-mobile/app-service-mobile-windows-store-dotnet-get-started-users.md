---
title: Adicionar autenticação ao seu aplicativo da UWP (Plataforma Universal do Windows) | Microsoft Docs
description: 'Saiba como usar aplicativos de celular do serviço de aplicativo do Azure para autenticar usuários de seu aplicativo da plataforma Universal do Windows (UWP) usando uma variedade de provedores de identidade, incluindo: AAD, Google, Facebook, Twitter e Microsoft.'
services: app-service\mobile
documentationcenter: windows
author: elamalani
manager: panarasi
editor: ''
ms.assetid: 6cffd951-893e-4ce5-97ac-86e3f5ad9466
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 9a3a6a8d063836094c1285a5ab4c17ee403fc347
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67440227"
---
# <a name="add-authentication-to-your-windows-app"></a>Adicionar autenticação ao seu aplicativo do Windows
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> Visual Studio App Center está investindo em novos e integrados serviços essenciais para o desenvolvimento de aplicativos móveis. Os desenvolvedores podem usar **construir**, **teste** e **distribuir** services para configurar o pipeline de integração contínua e entrega. Depois que o aplicativo é implantado, os desenvolvedores podem monitorar o status e o uso do seu aplicativo usando o **Analytics** e **diagnóstico** serviços e entre em contato com usuários usando o **enviar por Push** serviço. Os desenvolvedores também podem aproveitar **Auth** autenticar seus usuários e **dados** serviço para manter e sincronizar dados do aplicativo na nuvem. Fazer check-out [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-windows-store-dotnet-get-started-users) hoje mesmo.
>

## <a name="overview"></a>Visão geral
Este tópico mostra como adicionar autenticação baseada em nuvem ao seu aplicativo móvel. Neste tutorial, você aprenderá a adicionar autenticação ao projeto de início rápido da UWP (Plataforma Universal do Windows) para aplicativos móveis usando um provedor de identidade com suporte no Serviço de Aplicativo do Azure. Após ser autenticado e autorizado com sucesso pelo back-end do Aplicativo Móvel, o valor da ID de usuário é exibido.

Este tutorial baseia-se no início rápido dos Aplicativos Móveis. Você deve primeiro concluir o tutorial [Introdução aos Aplicativos Móveis](app-service-mobile-windows-store-dotnet-get-started.md).

## <a name="register"></a>Registrar seu aplicativo para autenticação e configurar o Serviço de Aplicativo
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Adicionar seu aplicativo às URLs de redirecionamento externo permitidas

A autenticação segura exige que você defina um novo esquema de URL para seu aplicativo. Isso permite que o sistema de autenticação redirecione para seu aplicativo após a conclusão do processo de autenticação. Neste tutorial, usamos sempre o esquema de URL _appname_. No entanto, você pode usar o esquema de URL que quiser. Ele deve ser exclusivo para seu aplicativo móvel. Para habilitar o redirecionamento no lado do servidor:

1. No [portal do Azure](https://ms.portal.azure.com), selecione sua conta.

2. Clique na opção de menu **Autenticação/Autorização**.

3. Em **URLs de Redirecionamento Externo Permitidas**, insira `url_scheme_of_your_app://easyauth.callback`.  O **esquema_de_URL_do_seu_aplicativo** nessa cadeia de caracteres é o esquema de URL do seu aplicativo móvel.  Ele deve seguir as especificações de URL normal para um protocolo (use somente letras e números e inicie com uma letra).  Você deve anotar a cadeia de caracteres escolhida, já que precisará ajustar o código do aplicativo móvel com o esquema de URL em vários lugares.

4. Clique em **Salvar**.

## <a name="permissions"></a>Restringir permissões a usuários autenticados
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Agora, é possível verificar se o acesso anônimo para o back-end foi desabilitado. Com o projeto de aplicativo da UWP configurado como projeto de inicialização, implante e execute o aplicativo; verifique se uma exceção sem tratamento com um código de status 401 (não autorizado) é gerada depois que o aplicativo é iniciado. Isso acontece porque o aplicativo tenta acessar o código do aplicativo móvel como um usuário não autenticado, mas a tabela *TodoItem* agora exige autenticação.

Em seguida, você atualizará o aplicativo para autenticar usuários antes de solicitar recursos do seu Serviço de Aplicativo.

## <a name="add-authentication"></a>Adicionar autenticação ao aplicativo
1. No arquivo de projeto de aplicativo da UWP MainPage.xaml.cs, adicione o seguinte snippet de código:
   
        // Define a member variable for storing the signed-in user. 
        private MobileServiceUser user;
   
        // Define a method that performs the authentication process
        // using a Facebook sign-in. 
        private async System.Threading.Tasks.Task<bool> AuthenticateAsync()
        {
            string message;
            bool success = false;
            try
            {
                // Change 'MobileService' to the name of your MobileServiceClient instance.
                // Sign-in using Facebook authentication.
                user = await App.MobileService
                    .LoginAsync(MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                message =
                    string.Format("You are now signed in - {0}", user.UserId);
   
                success = true;
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }
   
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
            return success;
        }
   
    Esse código autentica o usuário com um logon do Facebook. Se você estiver usando um provedor de identidade além do Facebook, altere o valor **MobileServiceAuthenticationProvider** acima para o valor de seu provedor.
2. Substitua o método **OnNavigatedTo** em MainPage.xaml.cs. Em seguida, você adicionará um botão **Entrar** ao aplicativo que dispara a autenticação.

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            if (e.Parameter is Uri)
            {
                App.MobileService.ResumeWithURL(e.Parameter as Uri);
            }
        }

3. Adicione o seguinte snippet de código à classe MainPage.xaml.cs:
   
        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile app.
            if (await AuthenticateAsync())
            {
                // Switch the buttons and load items from the mobile app.
                ButtonLogin.Visibility = Visibility.Collapsed;
                ButtonSave.Visibility = Visibility.Visible;
                //await InitLocalStoreAsync(); //offline sync support.
                await RefreshTodoItems();
            }
        }
4. Abra o arquivo de projeto MainPage.xaml, localize o elemento que define o botão **Salvar** e substitua-o pelo código a seguir:
   
        <Button Name="ButtonSave" Visibility="Collapsed" Margin="0,8,8,0" 
                Click="ButtonSave_Click">
            <StackPanel Orientation="Horizontal">
                <SymbolIcon Symbol="Add"/>
                <TextBlock Margin="5">Save</TextBlock>
            </StackPanel>
        </Button>
        <Button Name="ButtonLogin" Visibility="Visible" Margin="0,8,8,0" 
                Click="ButtonLogin_Click" TabIndex="0">
            <StackPanel Orientation="Horizontal">
                <SymbolIcon Symbol="Permissions"/>
                <TextBlock Margin="5">Sign in</TextBlock> 
            </StackPanel>
        </Button>
5. Adicione o seguinte snippet de código ao App.xaml.cs:

        protected override void OnActivated(IActivatedEventArgs args)
        {
            if (args.Kind == ActivationKind.Protocol)
            {
                ProtocolActivatedEventArgs protocolArgs = args as ProtocolActivatedEventArgs;
                Frame content = Window.Current.Content as Frame;
                if (content.Content.GetType() == typeof(MainPage))
                {
                    content.Navigate(typeof(MainPage), protocolArgs.Uri);
                }
            }
            Window.Current.Activate();
            base.OnActivated(args);
        }
6. Abra o arquivo Package.appxmanifest, navegue até **Declarações**, na lista suspensa **Declarações Disponíveis**, selecione **Protocolo** e clique no botão **Adicionar**. Agora, configure as **Propriedades** da declaração do **Protocolo**. Em **Nome de exibição**, adicione o nome que você deseja exibir aos usuários do aplicativo. Em **Nome**, adicione o {esquema_de_URL_do_seu_aplicativo}.
7. Pressione a tecla F5 para executar o aplicativo, clique no botão **Entrar** e entre no aplicativo com o provedor de identidade escolhido. Depois que o seu logon for bem-sucedido, o aplicativo será executado sem erros, e você poderá consultar o seu back-end e fazer atualizações nos dados.

## <a name="tokens"></a>Armazenar o token de autenticação no cliente
O exemplo anterior mostrou uma entrada padrão, que requer que o cliente contate o provedor de identidade e o Serviço de Aplicativo sempre que o aplicativo for iniciado. Além de esse método ser ineficiente, você pode se deparar com problemas relacionados ao uso caso muitos consumidores tentem iniciar o aplicativo ao mesmo tempo. Uma abordagem melhor é armazenar em cache o token de autorização retornado pelo Serviço de Aplicativo e tentar usá-lo antes de usar um logon baseado em provedor.

> [!NOTE]
> Você pode armazenar em cache o token emitido pelos Serviços de Aplicativos usando tanto a autenticação gerenciada pelo cliente quanto a autenticação gerenciada pelo serviço. Este tutorial usa a autenticação gerenciada pelo serviço.
> 
> 

[!INCLUDE [mobile-windows-universal-dotnet-authenticate-app-with-token](../../includes/mobile-windows-universal-dotnet-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Próximas etapas
Agora que você concluiu este tutorial de autenticação básica, considere continuar com um dos seguintes tutoriais:

* [Adicionar notificações por push ao aplicativo](app-service-mobile-windows-store-dotnet-get-started-push.md)  
  Saiba como adicionar suporte a notificações por push ao aplicativo e configurar o back-end do Aplicativo Móvel para usar os Hubs de Notificação do Azure para enviar notificações por push.
* [Habilitar sincronização offline para seu aplicativo](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  Saiba como adicionar suporte offline em seu aplicativo usando um back-end do aplicativo móvel. Sincronização offline permite que os usuários finais interajam com um aplicativo móvel, &mdash;exibindo, adicionando ou modificando dados&mdash;, mesmo quando não há conexão de rede.

<!-- URLs. -->
[Get started with your mobile app]: app-service-mobile-windows-store-dotnet-get-started.md
