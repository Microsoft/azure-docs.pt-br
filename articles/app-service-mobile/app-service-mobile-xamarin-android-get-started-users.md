---
title: Introdução à autenticação para aplicativos móveis no Xamarin Android
description: Aprenda a usar os aplicativos móveis para autenticar usuários de seu aplicativo Xamarin Android por meio de uma variedade de provedores de identidade, incluindo AAD, Google, Facebook, Twitter e Microsoft.
services: app-service\mobile
documentationcenter: xamarin
author: elamalani
manager: panarasi
editor: ''
ms.assetid: 570fc12b-46a9-4722-b2e0-0d1c45fb2152
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 7a0b2c54c2d2a9daba56ea1d05c18e72a2d7a7a0
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447064"
---
# <a name="add-authentication-to-your-xamarinandroid-app"></a>Adicione autenticação ao aplicativo Xamarin.Android
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> Visual Studio App Center está investindo em novos e integrados serviços essenciais para o desenvolvimento de aplicativos móveis. Os desenvolvedores podem usar **construir**, **teste** e **distribuir** services para configurar o pipeline de integração contínua e entrega. Depois que o aplicativo é implantado, os desenvolvedores podem monitorar o status e o uso do seu aplicativo usando o **Analytics** e **diagnóstico** serviços e entre em contato com usuários usando o **enviar por Push** serviço. Os desenvolvedores também podem aproveitar **Auth** autenticar seus usuários e **dados** serviço para manter e sincronizar dados do aplicativo na nuvem. Fazer check-out [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-xamarin-android-get-started-users) hoje mesmo.
>

## <a name="overview"></a>Visão geral
Este tópico mostra como autenticar usuários de um aplicativo móvel em seu aplicativo cliente. Neste tutorial, você pode adicionar autenticação ao projeto de início rápido usando um provedor de identidade suportado pelos Aplicativos Móveis do Azure. Após ser autenticado e autorizado com sucesso no aplicativo móvel, o valor da ID de usuário é exibido.

Este tutorial baseia-se no início rápido do aplicativo móvel. Você também deve primeiro concluir o tutorial [Criar um aplicativo Xamarin.Android]. Se você não usar o projeto baixado de início rápido do servidor, deve adicionar o pacote de extensão de autenticação ao seu projeto. Para obter mais informações sobre pacotes de extensão do servidor, confira [Trabalhar com o servidor .NET back-end do SDK para Aplicativos Móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="register"></a>Registrar seu aplicativo para autenticação e configurar os Serviços de Aplicativos
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Adicionar seu aplicativo às URLs de redirecionamento externo permitidas

A autenticação segura exige que você defina um novo esquema de URL para seu aplicativo. Isso permite que o sistema de autenticação redirecione para seu aplicativo após a conclusão do processo de autenticação. Neste tutorial, usamos sempre o esquema de URL _appname_. No entanto, você pode usar o esquema de URL que quiser. Ele deve ser exclusivo para seu aplicativo móvel. Para habilitar o redirecionamento no lado do servidor:

1. No [Portal do Azure], selecione seu Serviço de Aplicativo.

2. Clique na opção de menu **Autenticação/Autorização**.

3. Em **URLs de Redirecionamento Externo Permitidas**, insira `url_scheme_of_your_app://easyauth.callback`.  O **esquema_de_URL_do_seu_aplicativo** nessa cadeia de caracteres é o esquema de URL do seu aplicativo móvel.  Ele deve seguir as especificações de URL normal para um protocolo (use somente letras e números e inicie com uma letra).  Você deve anotar a cadeia de caracteres escolhida, já que precisará ajustar o código do aplicativo móvel com o esquema de URL em vários lugares.

4. Clique em **OK**.

5. Clique em **Salvar**.

## <a name="permissions"></a>Restringir permissões a usuários autenticados
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

No Visual Studio ou Xamarin Studio, execute o projeto cliente em um dispositivo ou emulador. Verifique se uma exceção não tratada com um código de status 401 (Não autorizado) é gerada após o aplicativo ser iniciado. Isso acontece porque o aplicativo tenta acessar o back-end do aplicativo móvel como um usuário não autenticado. A tabela *TodoItem* agora exige autenticação.

Em seguida, você atualizará o aplicativo do cliente para solicitar recursos do back-end do aplicativo móvel com um usuário autenticado.

## <a name="add-authentication"></a>Adicionar autenticação ao aplicativo
O aplicativo é atualizado para exigir que os usuários toquem no botão **Entrar** e se autentiquem para que os dados sejam exibidos.

1. Adicione o seguinte código à classe **TodoActivity** :
   
        // Define an authenticated user.
        private MobileServiceUser user;
        private async Task<bool> Authenticate()
        {
                var success = false;
                try
                {
                    // Sign in with Facebook login using a server-managed flow.
                    user = await client.LoginAsync(this,
                        MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                    CreateAndShowDialog(string.Format("you are now logged in - {0}",
                        user.UserId), "Logged in!");
   
                    success = true;
                }
                catch (Exception ex)
                {
                    CreateAndShowDialog(ex, "Authentication failed");
                }
                return success;
        }
   
        [Java.Interop.Export()]
        public async void LoginUser(View view)
        {
            // Load data only after authentication succeeds.
            if (await Authenticate())
            {
                //Hide the button after authentication succeeds.
                FindViewById<Button>(Resource.Id.buttonLoginUser).Visibility = ViewStates.Gone;
   
                // Load the data.
                OnRefreshItemsSelected();
            }
        }
   
    Isso cria um novo método para autenticar um usuário e um manipulador de método para um novo botão **Entrar** . O usuário no código de exemplo acima é autenticado usando um logon do Facebook. Uma caixa de diálogo é usada para exibir a ID de usuário após a autenticação.
   
   > [!NOTE]
   > Se você estiver usando um provedor de identidade diferente do Facebook, altere o valor passado para **LoginAsync** acima para um dos seguintes: *MicrosoftAccount*, *Twitter*, *Google* ou *WindowsAzureActiveDirectory*.
   > 
   > 
2. No método **OnCreate** , exclua ou comente a linha de código a seguir:
   
        OnRefreshItemsSelected ();
3. No arquivo Activity_To_Do.axml, adicione a definição do botão *LoginUser* a seguir antes do botão *AddItem* existente:
   
          <Button
            android:id="@+id/buttonLoginUser"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:onClick="LoginUser"
            android:text="@string/login_button_text" />
4. Adicione o elemento a seguir no arquivo de recursos de Strings.xml:
   
        <string name="login_button_text">Sign in</string>
5. Abra o arquivo AndroidManifest.xml e adicione o seguinte código dentro do elemento XML `<application>`:

        <activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity" android:launchMode="singleTop" android:noHistory="true">
          <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="{url_scheme_of_your_app}" android:host="easyauth.callback" />
          </intent-filter>
        </activity>

6. No Visual Studio ou Xamarin Studio, execute o projeto cliente em um dispositivo ou emulador e entre com o seu provedor de identidade preferido. Quando você entrar com êxito, o aplicativo exibirá o sua ID de logon e a lista de itens de tarefas e você poderá fazer atualizações aos dados.

## <a name="troubleshooting"></a>solução de problemas

**O aplicativo falhou com `Java.Lang.NoSuchMethodError: No static method startActivity`**

Em alguns casos, conflitos nos pacotes de suporte são exibidos como apenas um aviso no Visual Studio, o aplicativo falha com esta exceção em tempo de execução. Neste caso, você precisa verificar se todos os pacotes de suporte referenciados em seu projeto têm a mesma versão. O [pacote NuGet de Aplicativos Móveis do Azure](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) depende de `Xamarin.Android.Support.CustomTabs` para a plataforma Android e, portanto, se o projeto usar pacotes de suporte mais recentes, será necessário instalar diretamente esse pacote com a versão necessária para evitar conflitos.

<!-- URLs. -->
[Criar um aplicativo Xamarin.Android]: app-service-mobile-xamarin-android-get-started.md
