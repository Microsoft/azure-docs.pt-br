---
title: 'Início Rápido: Adicionar a opção Entrar com a conta da Microsoft a um aplicativo Android | Azure'
titleSuffix: Microsoft identity platform
description: Neste guia de início rápido, saiba como aplicativos Android podem chamar uma API que exige tokens de acesso emitidos pela plataforma de identidade da Microsoft.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/15/2019
ms.author: marsma
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:Android
ms.openlocfilehash: 49c69a79b4841f13623c9e6ab3daea72dd7fde26
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99583647"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>Início Rápido: Entrada de usuários e chamada à API do Microsoft Graph em um aplicativo Android

Neste guia de início rápido, você baixará e executará um exemplo de código que demonstra como um aplicativo Android pode conectar usuários e obter um token de acesso para chamar a API do Microsoft Graph. 

Confira [Como o exemplo funciona](#how-the-sample-works) para ver uma ilustração.

Os aplicativos precisam ser representados por um objeto de aplicativo no Azure Active Directory, de modo que a plataforma de identidade da Microsoft possa fornecer tokens ao aplicativo.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Android Studio
* Android 16 ou superior

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Etapa 1: Configurar seu aplicativo no portal do Azure
> Para que o exemplo de código deste guia de início rápido funcione, é necessário adicionar um **URI de Redirecionamento** compatível com o agente de autenticação.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Fazer essas alterações para mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurado](media/quickstart-v2-android/green-check.png) Seu aplicativo já está configurado com esses atributos
>
> ### <a name="step-2-download-the-project"></a>Etapa 2: Baixe o projeto
> [!div class="sxs-lookup" renderon="portal"]
> Execute o projeto usando o Android Studio.
> [!div class="sxs-lookup" renderon="portal" id="autoupdate" class="nextstepaction"]
> [Baixe o exemplo de código](https://github.com/Azure-Samples/ms-identity-android-java/archive/master.zip)
>
> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Etapa 3: seu aplicativo está configurado e pronto para ser executado
> Configuramos seu projeto com os valores das propriedades do aplicativo e ele está pronto para ser executado.
> O aplicativo de exemplo é iniciado na tela **Modo de Conta Único**. Um escopo padrão, **user.read**, é fornecido por padrão, usado ao ler seus próprios dados de perfil durante a chamada à API do Microsoft Graph. A URL da chamada à API do Microsoft Graph é fornecida por padrão. Se desejar, você poderá alterar as duas.
>
> ![Aplicativo de exemplo MSAL mostrando o uso de conta única e múltipla](./media/quickstart-v2-android/quickstart-sample-app.png)
>
> Use o menu do aplicativo para alterar entre modos de conta única e múltipla.
>
> No modo de conta única, entre usando uma conta corporativa ou doméstica:
>
> 1. Selecione **Obter dados de grafo interativamente** para solicitar que o usuário insira suas credenciais. Você verá a saída da chamada à API do Microsoft Graph na parte inferior da tela.
> 2. Após entrar, selecione **Obter dados de grafo silenciosamente** para fazer uma chamada à API do Microsoft Graph sem solicitar as credenciais do usuário novamente. Você verá a saída da chamada à API do Microsoft Graph na parte inferior da tela.
>
> No modo de conta múltipla, você pode repetir as mesmas etapas.  Além disso, você pode remover a conta conectada, que também remove os tokens armazenados em cache dessa conta.

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> ## <a name="step-1-get-the-sample-app"></a>Etapa 1: Obter o aplicativo de exemplo
>
> [Baixe o código](https://github.com/Azure-Samples/ms-identity-android-java/archive/master.zip).
>
> ## <a name="step-2-run-the-sample-app"></a>Etapa 2: Executar o aplicativo de exemplo
>
> Selecione seu emulador ou dispositivo físico da lista suspensa de **dispositivos disponíveis** do Android Studio e execute o aplicativo.
>
> O aplicativo de exemplo é iniciado na tela **Modo de Conta Único**. Um escopo padrão, **user.read**, é fornecido por padrão, usado ao ler seus próprios dados de perfil durante a chamada à API do Microsoft Graph. A URL da chamada à API do Microsoft Graph é fornecida por padrão. Se desejar, você poderá alterar as duas.
>
> ![Aplicativo de exemplo MSAL mostrando o uso de conta única e múltipla](./media/quickstart-v2-android/quickstart-sample-app.png)
>
> Use o menu do aplicativo para alterar entre modos de conta única e múltipla.
>
> No modo de conta única, entre usando uma conta corporativa ou doméstica:
>
> 1. Selecione **Obter dados de grafo interativamente** para solicitar que o usuário insira suas credenciais. Você verá a saída da chamada à API do Microsoft Graph na parte inferior da tela.
> 2. Após entrar, selecione **Obter dados de grafo silenciosamente** para fazer uma chamada à API do Microsoft Graph sem solicitar as credenciais do usuário novamente. Você verá a saída da chamada à API do Microsoft Graph na parte inferior da tela.
>
> No modo de conta múltipla, você pode repetir as mesmas etapas.  Além disso, você pode remover a conta conectada, que também remove os tokens armazenados em cache dessa conta.

## <a name="how-the-sample-works"></a>Como o exemplo funciona
![Captura de tela do aplicativo de exemplo](media/quickstart-v2-android/android-intro.svg)


O código é organizado em fragmentos que mostram como escrever um aplicativo MSAL de contas únicas e múltiplas. Os arquivos de código são organizados da seguinte maneira:

| Arquivo  | Demonstra  |
|---------|---------|
| MainActivity | Gerencia a interface do usuário |
| MSGraphRequestWrapper  | Chama a API do Microsoft Graph usando o token fornecido pela MSAL |
| MultipleAccountModeFragment  | Inicializa um aplicativo de conta múltipla, carrega uma conta de usuário e obtém um token para chamar a API do Microsoft Graph |
| SingleAccountModeFragment | Inicializa um aplicativo de conta única, carrega uma conta de usuário e obtém um token para chamar a API do Microsoft Graph |
| res/auth_config_multiple_account.json  | O arquivo de configuração de conta múltipla |
| res/auth_config_single_account.json  | O arquivo de configuração de conta única |
| Gradle Scripts/build.grade (Module:app) | As dependências da biblioteca MSAL são adicionadas aqui |

Agora examinaremos esses arquivos mais detalhadamente e chamaremos o código específico da MSAL em cada um.

### <a name="adding-msal-to-the-app"></a>Adicionar MSAL ao aplicativo

MSAL ([com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal)) é a biblioteca usada para conectar usuários e solicitar tokens usados para acessar uma API protegida pela plataforma de identidade da Microsoft. O Gradle 3.0+ instala a biblioteca quando você adiciona o seguinte aos **Scripts do Gradle** > **build.gradle (Módulo: aplicativo)** em **Dependências**:

```gradle
implementation 'com.microsoft.identity.client:msal:2.+'
```

É possível ver isso no projeto de exemplo em build.gradle (Módulo: aplicativo):

```java
dependencies {
    ...
    implementation 'com.microsoft.identity.client:msal:2.+'
    ...
}
```

Isso instrui o Gradle a baixar e compilar o MSAL da central do maven.

### <a name="msal-imports"></a>Importações da MSAL

As importações relevantes para a biblioteca MSAL são `com.microsoft.identity.client.*`.  Por exemplo, você verá `import com.microsoft.identity.client.PublicClientApplication;` que é o namespace da classe `PublicClientApplication`, que representa seu aplicativo cliente público.

### <a name="singleaccountmodefragmentjava"></a>SingleAccountModeFragment.java

Este arquivo demonstra como criar um aplicativo MSAL de conta única e chamar uma API do Microsoft Graph.

Os aplicativos de conta única são usados por um usuário único.  Por exemplo, talvez você só tenha uma conta com a qual você entra em seu aplicativo de mapeamento.

#### <a name="single-account-msal-initialization"></a>Inicialização da MSAL de conta única

No `auth_config_single_account.json`, no `onCreateView()`, uma conta única`PublicClientApplication` é criada usando as informações de configuração armazenadas no arquivo `auth_config_single_account.json`.  É assim como você inicializa a biblioteca MSAL para que ela seja usada em um aplicativo MSAL de conta única:

```java
...
// Creates a PublicClientApplication object with res/raw/auth_config_single_account.json
PublicClientApplication.createSingleAccountPublicClientApplication(getContext(),
        R.raw.auth_config_single_account,
        new IPublicClientApplication.ISingleAccountApplicationCreatedListener() {
            @Override
            public void onCreated(ISingleAccountPublicClientApplication application) {
                /**
                 * This test app assumes that the app is only going to support one account.
                 * This requires "account_mode" : "SINGLE" in the config json file.
                 **/
                mSingleAccountApp = application;
                loadAccount();
            }

            @Override
            public void onError(MsalException exception) {
                displayError(exception);
            }
        });
```

#### <a name="sign-in-a-user"></a>Conectar um usuário

No `SingleAccountModeFragment.java`, o código para conectar um usuário está no `initializeUI()`, no manipulador de cliques `signInButton`.

Chame `signIn()` antes de tentar adquirir tokens. `signIn()` se comporta como se `acquireToken()` foi chamado, resultando em um prompt interativo para o usuário entrar.

Conectar um usuário é uma operação assíncrona. Um retorno de chamada é passado que chama a API do Microsoft Graph e atualiza a interface do usuário depois que o usuário se conecta:

```java
mSingleAccountApp.signIn(getActivity(), null, getScopes(), getAuthInteractiveCallback());
```

#### <a name="sign-out-a-user"></a>Desconectar um usuário

No `SingleAccountModeFragment.java`, o código para desconectar um usuário está no `initializeUI()`, no manipulador de cliques `signOutButton`.  Desconectar um usuário é uma operação assíncrona. Desconectar o usuário também apaga o cache de token dessa conta. Um retorno de chamada será criado para atualizar a interface do usuário depois que a conta de usuário for desconectada:

```java
mSingleAccountApp.signOut(new ISingleAccountPublicClientApplication.SignOutCallback() {
    @Override
    public void onSignOut() {
        updateUI(null);
        performOperationOnSignOut();
    }

    @Override
    public void onError(@NonNull MsalException exception) {
        displayError(exception);
    }
});
```

#### <a name="get-a-token-interactively-or-silently"></a>Obter um token de maneira interativa ou silenciosa

Para representar o menor número de solicitações para o usuário, normalmente você obterá um token silenciosamente. Em seguida, se houver um erro, tente obter o token interativamente. Na primeira vez em que o aplicativo chama `signIn()`, ele funciona efetivamente como uma chamada a `acquireToken()`, que solicitará as credenciais do usuário.

Algumas situações em que o usuário pode ser solicitado a selecionar sua conta, inserir credenciais ou dar consentimento às permissões que seu aplicativo solicitou são:

* A primeira vez que o usuário entra no aplicativo
* Se um usuário redefinir a senha, ele precisará inserir as credenciais
* Se o consentimento for revogado
* Se o aplicativo exigir consentimento explicitamente
* Quando seu aplicativo estiver solicitando acesso a um recurso pela primeira vez
* Quando a MFA ou outras políticas de Acesso Condicional forem necessárias

O código para obter um token interativamente, que está com a interface do usuário que envolverá o usuário, está em `SingleAccountModeFragment.java`, no `initializeUI()`, no manipulador de cliques `callGraphApiInteractiveButton`:

```java
/**
 * If acquireTokenSilent() returns an error that requires an interaction (MsalUiRequiredException),
 * invoke acquireToken() to have the user resolve the interrupt interactively.
 *
 * Some example scenarios are
 *  - password change
 *  - the resource you're acquiring a token for has a stricter set of requirement than your Single Sign-On refresh token.
 *  - you're introducing a new scope which the user has never consented for.
 **/
mSingleAccountApp.acquireToken(getActivity(), getScopes(), getAuthInteractiveCallback());
```

Se o usuário já tiver entrado, `acquireTokenSilentAsync()` permitirá que aplicativos solicitem tokens silenciosamente, conforme mostrado no `initializeUI()`, no manipulador de cliques `callGraphApiSilentButton`:

```java
/**
 * Once you've signed the user in,
 * you can perform acquireTokenSilent to obtain resources without interrupting the user.
 **/
  mSingleAccountApp.acquireTokenSilentAsync(getScopes(), AUTHORITY, getAuthSilentCallback());
```

#### <a name="load-an-account"></a>Carregar uma conta

O código para carregar uma conta está em `SingleAccountModeFragment.java` em `loadAccount()`.  Carregar a conta do usuário é uma operação assíncrona. Portanto, retornos de chamada para manipular quando a conta é carregada ou alterada ou quando um erro ocorre são passados para a MSAL.  O código a seguir também manipula `onAccountChanged()`, que ocorre quando uma conta é removida, o usuário é muda para outra conta e assim por diante.

```java
private void loadAccount() {
    ...

    mSingleAccountApp.getCurrentAccountAsync(new ISingleAccountPublicClientApplication.CurrentAccountCallback() {
        @Override
        public void onAccountLoaded(@Nullable IAccount activeAccount) {
            // You can use the account data to update your UI or your app database.
            updateUI(activeAccount);
        }

        @Override
        public void onAccountChanged(@Nullable IAccount priorAccount, @Nullable IAccount currentAccount) {
            if (currentAccount == null) {
                // Perform a cleanup task as the signed-in account changed.
                performOperationOnSignOut();
            }
        }

        @Override
        public void onError(@NonNull MsalException exception) {
            displayError(exception);
        }
    });
```

#### <a name="call-microsoft-graph"></a>Chamar o Microsoft Graph

Quando um usuário é conectado, a chamada ao Microsoft Graph é feita por meio de uma solicitação HTTP pelo `callGraphAPI()`, que é definido em `SingleAccountModeFragment.java`. Essa função é um wrapper que simplifica o exemplo realizando algumas tarefas como obter o token de acesso do `authenticationResult` e empacotar a chamada para o MSGraphRequestWrapper e exibir os resultados da chamada.

```java
private void callGraphAPI(final IAuthenticationResult authenticationResult) {
    MSGraphRequestWrapper.callGraphAPIUsingVolley(
            getContext(),
            graphResourceTextView.getText().toString(),
            authenticationResult.getAccessToken(),
            new Response.Listener<JSONObject>() {
                @Override
                public void onResponse(JSONObject response) {
                    /* Successfully called graph, process data and send to UI */
                    ...
                }
            },
            new Response.ErrorListener() {
                @Override
                public void onErrorResponse(VolleyError error) {
                    ...
                }
            });
}
```

### <a name="auth_config_single_accountjson"></a>auth_config_single_account.json

Este é o arquivo de configuração de um aplicativo MSAL que usa uma conta única.

Confira [Noções básicas sobre o arquivo de configuração da MSAL do Android](msal-configuration.md) para obter uma explicação desses campos.

Observe a presença de `"account_mode" : "SINGLE"`, que configura esse aplicativo para usar uma conta única.

O `"client_id"` é pré-configurado para usar um registro de objeto de aplicativo que a Microsoft mantém.
O `"redirect_uri"` é pré-configurado para usar a chave de assinatura fornecida com a amostra de código.

```json
{
  "client_id" : "0984a7b6-bc13-4141-8b0d-8f767e136bb7",
  "authorization_user_agent" : "DEFAULT",
  "redirect_uri" : "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D",
  "account_mode" : "SINGLE",
  "broker_redirect_uri_registered": true,
  "authorities" : [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount",
        "tenant_id": "common"
      }
    }
  ]
}
```

### <a name="multipleaccountmodefragmentjava"></a>MultipleAccountModeFragment.java

Este arquivo demonstra como criar um aplicativo MSAL de conta múltipla e chamar uma API do Microsoft Graph.

Um exemplo de um aplicativo de conta múltipla é um aplicativo pequeno que permite que você trabalhe com várias contas de usuário, como uma conta corporativa e uma conta pessoal.

#### <a name="multiple-account-msal-initialization"></a>Inicialização da MSAL de conta múltipla

No arquivo `MultipleAccountModeFragment.java`, no `onCreateView()`, um objeto de aplicativo de conta múltipla (`IMultipleAccountPublicClientApplication`) é criado usando as informações de configuração armazenadas no `auth_config_multiple_account.json file`:

```java
// Creates a PublicClientApplication object with res/raw/auth_config_multiple_account.json
PublicClientApplication.createMultipleAccountPublicClientApplication(getContext(),
        R.raw.auth_config_multiple_account,
        new IPublicClientApplication.IMultipleAccountApplicationCreatedListener() {
            @Override
            public void onCreated(IMultipleAccountPublicClientApplication application) {
                mMultipleAccountApp = application;
                loadAccounts();
            }

            @Override
            public void onError(MsalException exception) {
                ...
            }
        });
```

O objeto `MultipleAccountPublicClientApplication` criado é armazenado em uma variável de membro de classe para que possa ser usado para interagir com a biblioteca MSAL para adquirir tokens e carregar e remover a conta de usuário.

#### <a name="load-an-account"></a>Carregar uma conta

Aplicativos de conta múltipla geralmente chamam `getAccounts()` para selecionar a conta a ser usada para operações de MSAL. O código para carregar uma conta está no arquivo `MultipleAccountModeFragment.java` em `loadAccounts()`.  Carregar a conta de usuário é uma operação assíncrona. Portanto, um retorno de chamada lida com as situações em que a conta é carregada ou alterada ou quando ocorre um erro.

```java
/**
 * Load currently signed-in accounts, if there's any.
 **/
private void loadAccounts() {
    if (mMultipleAccountApp == null) {
        return;
    }

    mMultipleAccountApp.getAccounts(new IPublicClientApplication.LoadAccountsCallback() {
        @Override
        public void onTaskCompleted(final List<IAccount> result) {
            // You can use the account data to update your UI or your app database.
            accountList = result;
            updateUI(accountList);
        }

        @Override
        public void onError(MsalException exception) {
            displayError(exception);
        }
    });
}
```

#### <a name="get-a-token-interactively-or-silently"></a>Obter um token de maneira interativa ou silenciosa

Algumas situações em que o usuário pode ser solicitado a selecionar sua conta, inserir credenciais ou dar consentimento às permissões que seu aplicativo solicitou são:

* A primeira vez que os usuários entram no aplicativo
* Se um usuário redefinir a senha, ele precisará inserir as credenciais
* Se o consentimento for revogado
* Se o aplicativo exigir consentimento explicitamente
* Quando seu aplicativo estiver solicitando acesso a um recurso pela primeira vez
* Quando a MFA ou outras políticas de Acesso Condicional forem necessárias

Os aplicativos de conta múltipla normalmente devem adquirir tokens interativamente, ou seja, com uma interface do usuário que envolva o usuário, com uma chamada para `acquireToken()`.  O código para obter um token interativamente está no arquivo `MultipleAccountModeFragment.java` no `initializeUI()`, no manipulador de cliques `callGraphApiInteractiveButton`:

```java
/**
 * Acquire token interactively. It will also create an account object for the silent call as a result (to be obtained by getAccount()).
 *
 * If acquireTokenSilent() returns an error that requires an interaction,
 * invoke acquireToken() to have the user resolve the interrupt interactively.
 *
 * Some example scenarios are
 *  - password change
 *  - the resource you're acquiring a token for has a stricter set of requirement than your SSO refresh token.
 *  - you're introducing a new scope which the user has never consented for.
 **/
mMultipleAccountApp.acquireToken(getActivity(), getScopes(), getAuthInteractiveCallback());
```

Os aplicativos não devem exigir que o usuário entre sempre que solicitar um token. Se o usuário já tiver entrado, `acquireTokenSilentAsync()` permitirá que aplicativos solicitem tokens sem solicitar o usuário, conforme mostrado no arquivo `MultipleAccountModeFragment.java`, no `initializeUI()` no manipulador de cliques `callGraphApiSilentButton`:

```java
/**
 * Performs acquireToken without interrupting the user.
 *
 * This requires an account object of the account you're obtaining a token for.
 * (can be obtained via getAccount()).
 */
mMultipleAccountApp.acquireTokenSilentAsync(getScopes(),
    accountList.get(accountListSpinner.getSelectedItemPosition()),
    AUTHORITY,
    getAuthSilentCallback());
```

#### <a name="remove-an-account"></a>Remover uma conta

O código para remover uma conta e todos os tokens em cache da conta está no arquivo `MultipleAccountModeFragment.java` no `initializeUI()` no manipulador para o botão Remover conta. Antes de remover uma conta, você precisa de um objeto de conta, que você obtém de métodos MSAL como `getAccounts()` e `acquireToken()`. Como a remoção de uma conta é uma operação assíncrona, o retorno de chamada `onRemoved` é fornecido para atualizar a interface do usuário.

```java
/**
 * Removes the selected account and cached tokens from this app (or device, if the device is in shared mode).
 **/
mMultipleAccountApp.removeAccount(accountList.get(accountListSpinner.getSelectedItemPosition()),
        new IMultipleAccountPublicClientApplication.RemoveAccountCallback() {
            @Override
            public void onRemoved() {
                ...
                /* Reload account asynchronously to get the up-to-date list. */
                loadAccounts();
            }

            @Override
            public void onError(@NonNull MsalException exception) {
                displayError(exception);
            }
        });
```

### <a name="auth_config_multiple_accountjson"></a>auth_config_multiple_account.json

Este é o arquivo de configuração para um aplicativo MSAL que usa várias contas.

Confira [Noções básicas sobre o arquivo de configuração da MSAL do Android](msal-configuration.md) para obter uma explicação dos vários campos.

Diferentemente do arquivo de configuração [auth_config_single_account.json](#auth_config_single_accountjson), esse arquivo de configuração tem `"account_mode" : "MULTIPLE"`, em vez de `"account_mode" : "SINGLE"`, porque este é um aplicativo de conta múltipla.

O `"client_id"` é pré-configurado para usar um registro de objeto de aplicativo que a Microsoft mantém.
O `"redirect_uri"` é pré-configurado para usar a chave de assinatura fornecida com a amostra de código.

```json
{
  "client_id" : "0984a7b6-bc13-4141-8b0d-8f767e136bb7",
  "authorization_user_agent" : "DEFAULT",
  "redirect_uri" : "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D",
  "account_mode" : "MULTIPLE",
  "broker_redirect_uri_registered": true,
  "authorities" : [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount",
        "tenant_id": "common"
      }
    }
  ]
}
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Próximas etapas

Prossiga para o tutorial do Android, em que você criará um aplicativo Android que obtém um token de acesso da plataforma de identidade da Microsoft e o utilizará para chamar a API do Microsoft Graph.

> [!div class="nextstepaction"]
> [Tutorial: Conectar usuários e chamar o Microsoft Graph em um aplicativo Android](tutorial-v2-android.md)
