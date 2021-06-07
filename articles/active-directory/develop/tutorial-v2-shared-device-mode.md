---
title: 'Tutorial: Usar o modo de dispositivo compartilhado com a MSAL (Biblioteca de Autenticação da Microsoft) para Android | Azure'
titleSuffix: Microsoft identity platform
description: Neste tutorial, você aprende a preparar um dispositivo Android para ser executado no modo compartilhado e a executar um aplicativo de trabalhador de contato direto.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 1/15/2020
ms.author: hahamil
ms.reviewer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 6a173ed4dae9237d8aae991c943817ed70246eea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101649048"
---
# <a name="tutorial-use-shared-device-mode-in-your-android-application"></a>Tutorial: Usar o modo de dispositivo compartilhado em seu aplicativo Android

Neste tutorial, os desenvolvedores do Android e os administradores de locatários do Azure AD (Azure Active Directory) aprenderão mais sobre o código, o aplicativo Authenticator e as configurações de locatário necessárias para habilitar o modo de dispositivo compartilhado em um aplicativo Android.

Neste tutorial:

> [!div class="checklist"]
> * Baixe um exemplo de código
> * Habilite e detecte o modo de dispositivo compartilhado
> * Detecte o modo único ou o modo de várias contas
> * Detecte uma opção do usuário e habilite a entrada e a saída globais
> * Configure um locatário e registre o aplicativo no portal do Azure
> * Configure um dispositivo Android para o modo de dispositivo compartilhado
> * Executar o aplicativo de exemplo

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="developer-guide"></a>Guia do desenvolvedor

Esta seção do tutorial fornece aos desenvolvedores diretrizes de como implementar o modo de dispositivo compartilhado em um aplicativo Android usando o MSAL (Biblioteca de Autenticação da Microsoft). Confira o [tutorial do MSAL para Android](./tutorial-v2-android.md) para saber como integrar o MSAL ao seu aplicativo Android, conectar um usuário, chamar o Microsoft Graph e sair de um usuário.

### <a name="download-the-sample"></a>Baixar o exemplo

Clone o [aplicativo de exemplo](https://github.com/Azure-Samples/ms-identity-android-java/) do GitHub. O exemplo tem a capacidade de trabalhar em [modo de conta única ou modo de contas múltiplas](./single-multi-account.md).

### <a name="add-the-msal-sdk-to-your-local-maven-repository"></a>Adicionar o SDK do MSAL ao seu repositório Maven local

Se você não estiver usando o aplicativo de exemplo, adicione a biblioteca do MSAL como uma dependência no arquivo build.gradle, desta forma:

```gradle
dependencies{
  implementation 'com.microsoft.identity.client.msal:1.0.+'
}
```

### <a name="configure-your-app-to-use-shared-device-mode"></a>Configurar seu aplicativo para usar o modo de dispositivo compartilhado

Veja a [documentação de configuração](./msal-configuration.md) para obter mais informações sobre como configurar o arquivo de configuração.

Defina `"shared_device_mode_supported"` como `true` no arquivo de configuração do MSAL.

Talvez você não esteja planejando dar suporte ao modo de várias contas. Isso poderá ocorrer se você não estiver usando um dispositivo compartilhado e o usuário puder entrar no aplicativo com mais de uma conta ao mesmo tempo. Nesse caso, defina `"account_mode"` como `"SINGLE"`. Isso garante que seu aplicativo sempre obterá `ISingleAccountPublicClientApplication` e simplifica significativamente a integração do MSAL. O valor padrão de `"account_mode"` é `"MULTIPLE"`, portanto é importante alterar esse valor no arquivo de configuração se você está usando o modo `"single account"`.

Aqui está um exemplo do arquivo auth_config.json incluído no diretório **aplicativo**>**principal**>**res**>**bruto** do aplicativo de exemplo:

```json
{
 "client_id":"Client ID after app registration at https://aka.ms/MobileAppReg",
 "authorization_user_agent":"DEFAULT",
 "redirect_uri":"Redirect URI after app registration at https://aka.ms/MobileAppReg",
 "account_mode":"SINGLE",
 "broker_redirect_uri_registered": true,
 "shared_device_mode_supported": true,
 "authorities":[
  {
   "type":"AAD",
   "audience":{
     "type": "AzureADandPersonalMicrosoftAccount",
     "tenant_id":"common"
   }
  }
 ]
}
```

### <a name="detect-shared-device-mode"></a>Detectar modo de dispositivo compartilhado

O modo de dispositivo compartilhado permite configurar dispositivos Android para serem compartilhados por vários funcionários enquanto fornece o gerenciamento de Microsoft Identity do dispositivo. Os funcionários podem conectar em seus dispositivos e acessar as informações do cliente rapidamente. Quando eles concluírem seus turnos ou tarefas, eles poderão sair de todos os aplicativos no dispositivo compartilhado com um clique e o dispositivo estará imediatamente pronto para o uso do próximo funcionário.

Use `isSharedDevice()` para determinar se um aplicativo está em execução em um dispositivo que esteja no modo de dispositivo compartilhado. Seu aplicativo poderia usar esse sinalizador para determinar se ele deve modificar o UX adequadamente.

Aqui está um snippet de código que mostra como você poderia usar `isSharedDevice()`.  Está na classe `SingleAccountModeFragment` no aplicativo de exemplo:

```Java
deviceModeTextView.setText(mSingleAccountApp.isSharedDevice() ? "Shared" : "Non-Shared");
```

### <a name="initialize-the-publicclientapplication-object"></a>Inicializar o objeto PublicClientApplication

Se você definir `"account_mode":"SINGLE"` no arquivo de configuração do MSAL, você poderá converter com segurança o objeto de aplicativo retornado como um `ISingleAccountPublicCLientApplication`.

```java
private ISingleAccountPublicClientApplication mSingleAccountApp;

/*Configure your sample app and save state for this activity*/
PublicClientApplication.create(this.getApplicationCOntext(),
  R.raw.auth_config,
  new PublicClientApplication.ApplicationCreatedListener(){
  @Override
  public void onCreated(IPublicClientApplication application){
  mSingleAccountApp = (ISingleAccountPublicClientApplication)application;
  loadAccount();
  }
  @Override
  public void onError(MsalException exception{
  /*Fail to initialize PublicClientApplication */
  }
});
```

### <a name="detect-single-vs-multiple-account-mode"></a>Detectar modo único versus modo de várias contas

Se você estiver escrevendo um aplicativo que será usado apenas por trabalhadores de contato direto em um dispositivo compartilhado, recomendamos que você codifique o aplicativo para dar suporte apenas ao modo de conta única. Isso inclui a maioria dos aplicativos focados em tarefas, como os aplicativos de registros médicos, aplicativos de fatura e a maioria dos aplicativos de linha de negócios. Isso simplificará o desenvolvimento, pois muitos recursos do SDK não precisarão ser acomodados.

Se o aplicativo dá suporte a várias contas, bem como ao modo de dispositivo compartilhado, você deve executar uma verificação de tipo e converter para a interface apropriada, conforme mostrado abaixo.

```java
private IPublicClientApplication mApplication;

        if (mApplication instanceOf IMultipleAccountPublicClientApplication) {
          IMultipleAccountPublicClientApplication multipleAccountApplication = (IMultipleAccountPublicClientApplication) mApplication;
          ...
        } else if (mApplication instanceOf    ISingleAccountPublicClientApplication) {
           ISingleAccountPublicClientApplication singleAccountApplication = (ISingleAccountPublicClientApplication) mApplication;
            ...
        }
```

### <a name="get-the-signed-in-user-and-determine-if-a-user-has-changed-on-the-device"></a>Obter o usuário conectado e determinar se um usuário foi alterado no dispositivo

O método `loadAccount` recupera a conta do usuário conectado. O método `onAccountChanged` determina se o usuário conectado mudou e, nesse caso, limpa:

```java
private void loadAccount()
{
  mSingleAccountApp.getCurrentAccountAsync(new ISingleAccountPublicClientApplication.CurrentAccountCallback()
  {
    @Overide
    public void onAccountLoaded(@Nullable IAccount activeAccount)
    {
      if (activeAccount != null)
      {
        signedInUser = activeAccount;
        mSingleAccountApp.acquireTokenSilentAsync(SCOPES,"http://login.microsoftonline.com/common",getAuthSilentCallback());
      }
    }
    @Override
    public void on AccountChanged(@Nullable IAccount priorAccount, @Nullable Iaccount currentAccount)
    {
      if (currentAccount == null)
      {
        //Perform a cleanup task as the signed-in account changed.
        updateSingedOutUI();
      }
    }
    @Override
    public void onError(@NonNull Exception exception)
    {
    }
  }
}
```

### <a name="globally-sign-in-a-user"></a>Conectar um usuário globalmente

O seguinte conecta um usuário em todo o dispositivo para outros aplicativos que usam o MSAL com o Aplicativo Authenticator:

```java
private void onSignInClicked()
{
  mSingleAccountApp.signIn(getActivity(), SCOPES, null, getAuthInteractiveCallback());
}
```

### <a name="globally-sign-out-a-user"></a>Desconectar um usuário globalmente

O seguinte remove a conta conectada e limpa os tokens em cache não apenas do aplicativo, mas também do dispositivo que está no modo de dispositivo compartilhado:

```java
private void onSignOutClicked()
{
  mSingleAccountApp.signOut(new ISingleAccountPublicClientApplication.SignOutCallback()
  {
    @Override
    public void onSignOut()
    {
      updateSignedOutUI();
    }
    @Override
    public void onError(@NonNull MsalException exception)
    {
      /*failed to remove account with an exception*/
    }
  });
}
```

## <a name="administrator-guide"></a>Guia do administrador

As etapas a seguir descrevem como configurar seu aplicativo no portal do Azure e como colocar seu dispositivo no modo de dispositivo compartilhado.

### <a name="register-your-application-in-azure-active-directory"></a>Registrar seu aplicativo no Azure Active Directory

Primeiro, registre seu aplicativo em seu locatário organizacional. Em seguida, forneça esses valores abaixo em auth_config.json para que seu aplicativo seja executado corretamente.

Para obter informações sobre como fazer isso, confira [Registrar seu aplicativo](./tutorial-v2-android.md#register-your-application).

> [!NOTE]
> Ao registrar seu aplicativo, use o guia de início rápido no lado esquerdo e, em seguida, selecione **Android**. Isso levará a uma página em que você deverá fornecer o **Nome do Pacote** e o **Hash de Assinatura** para seu aplicativo. Isso é muito importante para garantir que a configuração do aplicativo funcione. Em seguida, você receberá um objeto de configuração que pode ser usado para seu aplicativo e que será recortado e colado em seu arquivo auth_config.json.

:::image type="content" source="media/tutorial-v2-shared-device-mode/register-app.png" alt-text="Configurar a página do aplicativo Android no início rápido do portal do Azure":::

Selecione **Fazer essa alteração para mim** e forneça os valores solicitados pelo início rápido no portal do Azure. Feito isso, geraremos todos os arquivos de configuração necessários.

:::image type="content" source="media/tutorial-v2-shared-device-mode/config-info.png" alt-text="Configurar a página do projeto no início rápido do portal do Azure":::

## <a name="set-up-a-tenant"></a>Configurar um locatário

Para fins de teste, configure o seguinte em seu locatário: pelo menos dois funcionários, um Administrador de dispositivo de nuvem e um Administrador global. No portal do Azure, defina o Administrador de Dispositivo de Nuvem modificando Funções Organizacionais. No portal do Azure, acesse suas Funções Organizacionais selecionando **Azure Active Directory** > **Funções e Administradores** > **Administrador de Dispositivo de Nuvem**. Adicione os usuários que poderão colocar um dispositivo no modo compartilhado.

## <a name="set-up-an-android-device-in-shared-mode"></a>Configurar um dispositivo Android para modo compartilhado

### <a name="download-the-authenticator-app"></a>Baixar o Aplicativo Authenticator

Baixe o aplicativo Microsoft Authenticator na Google Play Store. Se o aplicativo já estiver baixado, verifique se é a versão mais recente.

### <a name="authenticator-app-settings--registering-the-device-in-the-cloud"></a>Configurações do aplicativo Authenticator & registrar o dispositivo na nuvem

Inicie o aplicativo Authenticator e navegue até a página da conta principal. Depois de ver a página **Adicionar Conta**, você estará pronto para tornar o dispositivo compartilhado.

:::image type="content" source="media/tutorial-v2-shared-device-mode/authenticator-add-account.png" alt-text="Tela de adição de conta do Authenticator":::

Vá para o painel de **Configurações** usando a barra de menus à direita. Selecione **Registro de Dispositivo** em **Contas de Trabalho & Estudo**.

:::image type="content" source="media/tutorial-v2-shared-device-mode/authenticator-settings.png" alt-text="Tela de configurações do Authenticator":::

Ao clicar nesse botão, será solicitado que você autorize o acesso aos contatos do dispositivo. Isso ocorre devido à integração de conta do Android no dispositivo. Escolha **permitir**.

:::image type="content" source="media/tutorial-v2-shared-device-mode/authenticator-allow-screen.png" alt-text="Tela de confirmação de permissão de acesso do Authenticator":::

O Administrador de Dispositivo de Nuvem deve inserir seu email organizacional em **Ou registre-se como um dispositivo compartilhado**. Em seguida, clique no botão **registrar-se como dispositivo compartilhado** e insira suas credenciais.

:::image type="content" source="media/tutorial-v2-shared-device-mode/register-device.png" alt-text="Tela de registro do dispositivo no aplicativo":::

:::image type="content" source="media/tutorial-v2-shared-device-mode/sign-in.png" alt-text="Captura de tela do aplicativo mostrando a página de entrada da Microsoft":::

Agora o dispositivo está no modo compartilhado.

:::image type="content" source="media/tutorial-v2-shared-device-mode/shared-device-mode-screen.png" alt-text="Tela do aplicativo mostrando o modo de dispositivo compartilhado habilitado":::

 As entradas e saídas no dispositivo serão globais, o que significa que se aplicam a todos os aplicativos integrados ao MSAL e ao Microsoft Authenticator no dispositivo. Agora você pode implantar aplicativos que usam recursos de modo de dispositivo compartilhado.

## <a name="view-the-shared-device-in-the-azure-portal"></a>Exibir dispositivos compartilhados no portal do Azure

Depois de colocar um dispositivo no modo compartilhado, ele se torna conhecido em sua organização e é acompanhado em seu locatário organizacional. Você pode exibir dispositivos compartilhados examinando o **Tipo de Junção** na folha do Azure Active Directory do portal do Azure.

:::image type="content" source="media/tutorial-v2-shared-device-mode/registered-device-screen.png" alt-text="Painel Todos os dispositivos mostrado no portal do Azure":::

## <a name="running-the-sample-app"></a>Executando o aplicativo de exemplo

O Aplicativo de Exemplo é um aplicativo simples que chamará a API do Graph de sua organização. Na primeira execução você será solicitado a consentir, pois o aplicativo é novo na sua conta de funcionário.

:::image type="content" source="media/tutorial-v2-shared-device-mode/run-app-permissions-requested.png" alt-text="Tela de informações de configuração do aplicativo":::

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como trabalhar com a Biblioteca de Autenticação da Microsoft e o modo de dispositivo compartilhado em dispositivos Android:

> [!div class="nextstepaction"]
> [Modo de dispositivo compartilhado para dispositivos Android](msal-android-shared-devices.md)
