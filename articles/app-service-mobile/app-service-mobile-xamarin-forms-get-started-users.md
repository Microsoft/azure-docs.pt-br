---
title: Introdução à autenticação para os Aplicativos Móveis no aplicativo Xamarin.Forms | Microsoft Docs
description: Saiba como usar os Aplicativos Móveis para autenticar usuários de seu aplicativo Xamarin Forms por meio de uma variedade de provedores de identidade, incluindo o AAD, o Google, o Facebook, o Twitter e o Microsoft.
services: app-service\mobile
documentationcenter: xamarin
author: elamalani
manager: crdun
editor: ''
ms.assetid: 9c55e192-c761-4ff2-8d88-72260e9f6179
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: f1777fcb5a4e7899da982bd9d1d35905cb408ad2
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446297"
---
# <a name="add-authentication-to-your-xamarin-forms-app"></a>Adicionar autenticação ao seu aplicativo Xamarin Forms
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> Visual Studio App Center está investindo em novos e integrados serviços essenciais para o desenvolvimento de aplicativos móveis. Os desenvolvedores podem usar **construir**, **teste** e **distribuir** services para configurar o pipeline de integração contínua e entrega. Depois que o aplicativo é implantado, os desenvolvedores podem monitorar o status e o uso do seu aplicativo usando o **Analytics** e **diagnóstico** serviços e entre em contato com usuários usando o **enviar por Push** serviço. Os desenvolvedores também podem aproveitar **Auth** autenticar seus usuários e **dados** serviço para manter e sincronizar dados do aplicativo na nuvem. Fazer check-out [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-xamarin-forms-get-started-users) hoje mesmo.
>

## <a name="overview"></a>Visão Geral
Este tópico mostra como autenticar usuários de um aplicativo móvel do Serviço de Aplicativo em seu aplicativo cliente. Neste tutorial, você adiciona a autenticação ao projeto de início rápido do Xamarin.Forms usando um provedor de identidade com suporte do Serviço de Aplicativo. Depois de ser autenticado e autorizado com êxito pelo Aplicativo Móvel, o valor da ID de usuário é exibido e você poderá acessar dados da tabela restrita.

## <a name="prerequisites"></a>Pré-requisitos
Para obter o melhor resultado com este tutorial, é recomendável que você conclua primeiro a [criar um aplicativo Xamarin. Forms][1] tutorial. Depois de concluir este tutorial, você terá um projeto Xamarin.Forms que é um aplicativo de lista de tarefas para várias plataformas.

Se você não usar o projeto baixado de início rápido do servidor, deve adicionar o pacote de extensão de autenticação ao seu projeto. Para obter mais informações sobre pacotes de extensão do servidor, confira [Trabalhar com o servidor .NET back-end do SDK para Aplicativos Móveis do Azure][2].

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>Registrar seu aplicativo para a autenticação e configurar os Serviços de Aplicativos
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Adicionar seu aplicativo às URLs de redirecionamento externo permitidas

A autenticação segura exige que você defina um novo esquema de URL para seu aplicativo. Isso permite que o sistema de autenticação redirecione para seu aplicativo após a conclusão do processo de autenticação. Neste tutorial, usamos sempre o esquema de URL _appname_. No entanto, você pode usar o esquema de URL que quiser. Ele deve ser exclusivo para seu aplicativo móvel. Para habilitar o redirecionamento no lado do servidor:

1. No [portal do Azure][8], selecione sua conta.

2. Clique na opção de menu **Autenticação/Autorização**.

3. Em **URLs de Redirecionamento Externo Permitidas**, insira `url_scheme_of_your_app://easyauth.callback`.  O **esquema_de_URL_do_seu_aplicativo** nessa cadeia de caracteres é o esquema de URL do seu aplicativo móvel.  Ele deve seguir as especificações de URL normal para um protocolo (use somente letras e números e inicie com uma letra).  Você deve anotar a cadeia de caracteres escolhida, já que precisará ajustar o código do aplicativo móvel com o esquema de URL em vários lugares.

4. Clique em **OK**.

5. Clique em **Salvar**.

## <a name="restrict-permissions-to-authenticated-users"></a>Restringir permissões a usuários autenticados
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

## <a name="add-authentication-to-the-portable-class-library"></a>Adicionar autenticação à biblioteca de classes portátil
Os aplicativos móveis usam o [LoginAsync][3] extension method on the [MobileServiceClient][4] to sign in a user with App Service authentication. This sample
uses a server-managed authentication flow that displays the provider's sign-in interface in the app. For more information, see [Server-managed authentication][5]. Para proporcionar uma melhor experiência ao usuário em seu aplicativo de produção, você deve considerar o uso da [Autenticação gerenciada pelo cliente][6].

Para se autenticar em um projeto Xamarin.Forms, defina uma interface **IAuthenticate** na Biblioteca de Classes Portátil para o aplicativo. Em seguida, adicione um botão **Entrar** à interface de usuário definida na Biblioteca de Classes Portátil, em que você possa clicar para iniciar a autenticação. Os dados são carregados do back-end do aplicativo móvel depois da autenticação bem-sucedida.

Implemente a interface **IAuthenticate** para cada plataforma compatível com seu aplicativo.

1. No Visual Studio ou no Xamarin Studio, abra App.cs do projeto com **Portable no nome**, que é o projeto da Biblioteca de Classes Portátil, e adicione a seguinte instrução `using`:

        using System.Threading.Tasks;
2. Em App.cs, adicione a definição de interface `IAuthenticate` a seguir imediatamente antes da definição de classe `App`.

        public interface IAuthenticate
        {
            Task<bool> Authenticate();
        }
3. Para inicializar a interface com uma implementação específica de plataforma, adicione os membros estáticos a seguir à classe **App**.

        public static IAuthenticate Authenticator { get; private set; }

        public static void Init(IAuthenticate authenticator)
        {
            Authenticator = authenticator;
        }
4. Abra TodoList.xaml do projeto da Biblioteca de Classes Portátil, adicione o elemento **Button** a seguir ao elemento de layout *buttonsPanel* após o botão existente:

          <Button x:Name="loginButton" Text="Sign-in" MinimumHeightRequest="30"
            Clicked="loginButton_Clicked"/>

    Esse botão dispara a autenticação gerenciada por servidor com seu back-end de aplicativo móvel.
5. Abra TodoList.xaml.cs do projeto da Biblioteca de Classes Portátil e adicione o seguinte campo à classe `TodoList` :

        // Track whether the user has authenticated.
        bool authenticated = false;
6. Substitua o método **OnAppearing** pelo seguinte código:

        protected override async void OnAppearing()
        {
            base.OnAppearing();

            // Refresh items only when authenticated.
            if (authenticated == true)
            {
                // Set syncItems to true in order to synchronize the data
                // on startup when running in offline mode.
                await RefreshItems(true, syncItems: false);

                // Hide the Sign-in button.
                this.loginButton.IsVisible = false;
            }
        }

    Esse código garante que os dados só serão atualizados do serviço depois que você tiver sido autenticado.
7. Adicione o seguinte manipulador para o evento **Clicked** à classe **TodoList**:

        async void loginButton_Clicked(object sender, EventArgs e)
        {
            if (App.Authenticator != null)
                authenticated = await App.Authenticator.Authenticate();

            // Set syncItems to true to synchronize the data on startup when offline is enabled.
            if (authenticated == true)
                await RefreshItems(true, syncItems: false);
        }
8. Salve suas alterações e compile o projeto de Biblioteca de Classes Portátil verificando se não há erros.

## <a name="add-authentication-to-the-android-app"></a>Adicionar autenticação ao aplicativo Android
Esta seção mostra como implementar a interface **IAuthenticate** no projeto do aplicativo Android. Ignore esta seção se não estiver dando suporte a dispositivos Android.

1. No Visual Studio ou no Xamarin Studio, clique com botão direito do mouse no projeto **droid** e em **Definir como Projeto de Inicialização**.
2. Pressione F5 para iniciar o projeto no depurador e verifique se uma exceção sem tratamento com um código de status de 401 (Não autorizado) foi gerada depois que o aplicativo foi iniciado. O código 401 é gerado porque o acesso no back-end é restrito apenas aos usuários autorizados.
3. Abra MainActivity.cs no projeto Android e adicione estas instruções `using` :

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
4. Atualize a classe **MainActivity** para implementar a interface **IAuthenticate** da seguinte maneira:

        public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity, IAuthenticate
5. Atualize a classe **MainActivity** adicionando um campo **MobileServiceUser** e um método **Authenticate**, que é necessário para a interface **IAuthenticate**, da seguinte maneira:

        // Define an authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            var message = string.Empty;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(this, 
                    MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                if (user != null)
                {
                    message = string.Format("you are now signed-in as {0}.",
                        user.UserId);
                    success = true;
                }
            }
            catch (Exception ex)
            {
                message = ex.Message;
            }

            // Display the success or failure message.
            AlertDialog.Builder builder = new AlertDialog.Builder(this);
            builder.SetMessage(message);
            builder.SetTitle("Sign-in result");
            builder.Create().Show();

            return success;
        }

    Se você estiver usando um provedor de identidade diferente do Facebook, escolha um valor diferente para [MobileServiceAuthenticationProvider][7].

6. Atualize o arquivo **AndroidManifest.xml** adicionando o seguinte XML dentro de `<application>` elemento:

    ```xml
    <activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity" android:launchMode="singleTop" android:noHistory="true">
      <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="{url_scheme_of_your_app}" android:host="easyauth.callback" />
      </intent-filter>
    </activity>
    ```
    Substitua `{url_scheme_of_your_app}` pelo seu esquema de URL.
7. Adicione o seguinte código ao método **onCreate** da classe **MainActivity** antes de chamar `LoadApplication()`:

        // Initialize the authenticator before loading the app.
        App.Init((IAuthenticate)this);

    Esse código garante que o autenticador seja inicializado antes que o aplicativo seja carregado.
8. Recompile o aplicativo, execute-o, entre com o provedor de autenticação escolhido e verifique se você consegue acessar os dados como um usuário autenticado.

### <a name="troubleshooting"></a>solução de problemas

**O aplicativo falhou com `Java.Lang.NoSuchMethodError: No static method startActivity`**

Em alguns casos, conflitos nos pacotes de suporte são exibidos como apenas um aviso no Visual Studio, o aplicativo falha com esta exceção em tempo de execução. Neste caso, você precisa verificar se todos os pacotes de suporte referenciados em seu projeto têm a mesma versão. O [pacote NuGet de Aplicativos Móveis do Azure](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) depende de `Xamarin.Android.Support.CustomTabs` para a plataforma Android e, portanto, se o projeto usar pacotes de suporte mais recentes, será necessário instalar diretamente esse pacote com a versão necessária para evitar conflitos.

## <a name="add-authentication-to-the-ios-app"></a>Adicionar autenticação ao aplicativo do iOS
Esta seção mostra como implementar a interface **IAuthenticate** no projeto do aplicativo iOS. Ignore esta seção se não estiver dando suporte a dispositivos iOS.

1. No Visual Studio ou no Xamarin Studio, clique com o botão direito do mouse no projeto **iOS** e em **Definir como Projeto de Inicialização**.
2. Pressione F5 para iniciar o projeto no depurador e verifique se uma exceção sem tratamento com um código de status de 401 (Não autorizado) foi gerada depois que o aplicativo foi iniciado. A resposta 401 é gerada porque o acesso no back-end é restrito apenas aos usuários autorizados.
3. Abra AppDelegate.cs no projeto do iOS e adicione as seguintes instruções `using` :

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
4. Atualize a classe **AppDelegate** para implementar a interface **IAuthenticate** da seguinte maneira:

        public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate, IAuthenticate
5. Atualize a classe **AppDelegate** adicionando um campo **MobileServiceUser** e um método **Authenticate**, que é necessário para a interface **IAuthenticate**, da seguinte maneira:

        // Define an authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            var message = string.Empty;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient
                        .LoginAsync(UIApplication.SharedApplication.KeyWindow.RootViewController,
                        MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                    if (user != null)
                    {
                        message = string.Format("You are now signed-in as {0}.", user.UserId);
                        success = true;
                    }
                }
            }
            catch (Exception ex)
            {
               message = ex.Message;
            }

            // Display the success or failure message.
            UIAlertController avAlert = UIAlertController.Create("Sign-in result", message, UIAlertControllerStyle.Alert);
            avAlert.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));
            UIApplication.SharedApplication.KeyWindow.RootViewController.PresentViewController(avAlert, true, null);

            return success;
        }

    Se você estiver usando um provedor de identidade diferente do Facebook, escolha outro valor para [MobileServiceAuthenticationProvider].
    
6. Atualize a classe **AppDelegate** adicionando a sobrecarga do método **OpenUrl**, da seguinte maneira:

        public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
        {
            return TodoItemManager.DefaultManager.CurrentClient.ResumeWithURL(url);
        }
   
7. Adicione a seguinte linha de código ao método **FinishedLaunching** antes de chamar `LoadApplication()`:

        App.Init(this);

    Esse código garante que o autenticador seja inicializado antes que o aplicativo seja carregado.

8. Abra Info.plist e adicione um **Tipo de URL**. Defina o **Identificador** como um nome de sua escolha, os **esquemas de URL** para o esquema de URL para o seu aplicativo e a **Função** como Nenhuma.

9. Recompile o aplicativo, execute-o, entre com o provedor de autenticação escolhido e verifique se você consegue acessar os dados como um usuário autenticado.

## <a name="add-authentication-to-windows-10-including-phone-app-projects"></a>Adicionar autenticação a projetos de aplicativo do Windows 10 (incluindo o Phone)
Esta seção mostra como implementar a interface **IAuthenticate** nos projetos de aplicativo do Windows 10. As mesmas etapas se aplicam aos projetos UWP (Plataforma Universal do Windows), mas usando o projeto **UWP** (com alterações indicadas). Ignore esta seção se não estiver dando suporte a dispositivos Windows.

1. Em Visual Studio, clique com o botão direito do mouse no projeto **UWP** e depois em **Definir como Projeto de Inicialização**.
2. Pressione F5 para iniciar o projeto no depurador e verifique se uma exceção sem tratamento com um código de status de 401 (Não autorizado) foi gerada depois que o aplicativo foi iniciado. Essa resposta 401 é gerada porque o acesso no back-end é restrito apenas aos usuários autorizados.
3. Abra MainPage.xaml.cs para o projeto do aplicativo do Windows e adicione as seguintes instruções `using` :

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
        using Windows.UI.Popups;
        using <your_Portable_Class_Library_namespace>;

    Substitua `<your_Portable_Class_Library_namespace>` pelo namespace para sua biblioteca de classes portátil.
4. Atualize a classe **MainPage** para implementar a interface **IAuthenticate** da seguinte maneira:

        public sealed partial class MainPage : IAuthenticate
5. Atualize a classe **MainPage** adicionando um campo **MobileServiceUser** e um método **Authenticate**, que é necessário para a interface **IAuthenticate**, da seguinte maneira:

        // Define an authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            string message = string.Empty;
            var success = false;

            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient
                        .LoginAsync(MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                    if (user != null)
                    {
                        success = true;
                        message = string.Format("You are now signed-in as {0}.", user.UserId);
                    }
                }

            }
            catch (Exception ex)
            {
                message = string.Format("Authentication Failed: {0}", ex.Message);
            }

            // Display the success or failure message.
            await new MessageDialog(message, "Sign-in result").ShowAsync();

            return success;
        }

    Se você estiver usando um provedor de identidade diferente do Facebook, escolha um valor diferente para [MobileServiceAuthenticationProvider][7].

1. Adicione a seguinte linha de código no construtor para a classe **MainPage** antes de chamar `LoadApplication()`:

        // Initialize the authenticator before loading the app.
        <your_Portable_Class_Library_namespace>.App.Init(this);

    Substitua `<your_Portable_Class_Library_namespace>` pelo namespace para sua biblioteca de classes portátil.

3. Se estiver usando a **UWP**, adicione a seguinte substituição do método **OnActivated** à classe **App**:

       protected override void OnActivated(IActivatedEventArgs args)
       {
           base.OnActivated(args);

            if (args.Kind == ActivationKind.Protocol)
            {
                ProtocolActivatedEventArgs protocolArgs = args as ProtocolActivatedEventArgs;
                MobileServiceClientExtensions.ResumeWithURL(TodoItemManager.DefaultManager.CurrentClient,protocolArgs.Uri);
            }
       }

3. Abra Package.appxmanifest e adicione uma declaração de **Protocolo**. Defina o **Nome de exibição** como um nome de sua escolha e o **Nome** para o esquema de URL do seu aplicativo.

4. Recompile o aplicativo, execute-o, entre com o provedor de autenticação escolhido e verifique se você consegue acessar os dados como um usuário autenticado.

## <a name="next-steps"></a>Próximas etapas
Agora que você concluiu este tutorial de autenticação básica, considere continuar com um dos seguintes tutoriais:

* [Adicionar notificações por push ao aplicativo](app-service-mobile-xamarin-forms-get-started-push.md)

  Saiba como adicionar suporte a notificações por push ao aplicativo e configurar o back-end do Aplicativo Móvel para usar os Hubs de Notificação do Azure para enviar notificações por push.
* [Habilitar sincronização offline para seu aplicativo](app-service-mobile-xamarin-forms-get-started-offline-data.md)

  Saiba como adicionar suporte offline ao seu aplicativo usando um back-end de Aplicativo Móvel. A sincronização offline permite que os usuários finais interajam com um aplicativo móvel, exibindo, adicionando ou modificando dados, mesmo quando não há conexão de rede.

<!-- Images. -->

<!-- URLs. -->
[1]: app-service-mobile-xamarin-forms-get-started.md
[2]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[3]: https://msdn.microsoft.com/library/azure/dn268341(v=azure.10).aspx
[4]: https://msdn.microsoft.com/library/azure/JJ553674(v=azure.10).aspx
[5]: app-service-mobile-dotnet-how-to-use-client-library.md#serverflow
[6]: app-service-mobile-dotnet-how-to-use-client-library.md#clientflow
[7]: https://msdn.microsoft.com/library/azure/jj730936(v=azure.10).aspx
[8]: https://portal.azure.com
