---
title: 'Início Rápido: Adicionar a entrada com a conta da Microsoft a um aplicativo iOS ou macOS | Azure'
titleSuffix: Microsoft identity platform
description: Neste guia de início rápido, saiba como um aplicativo iOS ou macOS pode conectar usuários, obter um token de acesso da plataforma de identidade da Microsoft e chamar a API do Microsoft Graph.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/24/2019
ms.author: marsma
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:iOS
ms.openlocfilehash: d2c5af6aeccfbae0851513ff575bde3c39e3ca5f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100103780"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-ios-or-macos-app"></a>Início Rápido: Conectar usuários e chamar a API do Microsoft Graph de um aplicativo iOS ou macOS

Neste guia de início rápido, você baixará e executará um exemplo de código que demonstra como um aplicativo iOS ou macOS nativo pode conectar usuários e obter um token de acesso para chamar a API do Microsoft Graph.

O guia de início rápido se aplica a aplicativos iOS e macOS. Algumas etapas são necessárias apenas para aplicativos iOS e serão indicadas como tal.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* XCode 10+
* iOS 10+
* macOS 10.12+

## <a name="how-the-sample-works"></a>Como o exemplo funciona

![Mostra como o aplicativo de exemplo gerado por este início rápido funciona](media/quickstart-v2-ios/ios-intro.svg)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrar e baixar o aplicativo de início rápido
> Você tem duas opções para iniciar o aplicativo de início rápido:
> * [Express] [Opção 1: Registrar e configurar o aplicativo automaticamente e, em seguida, baixar seu exemplo de código](#option-1-register-and-auto-configure-your-app-and-then-download-the-code-sample)
> * [Manual] [Opção 2: Registrar e configurar manualmente o aplicativo e o exemplo de código](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-the-code-sample"></a>Opção 1: Registrar e configurar automaticamente o aplicativo e, em seguida, baixar o exemplo de código
> #### <a name="step-1-register-your-application"></a>Etapa 1: Registre seu aplicativo
> Para registrar seu aplicativo,
> 1. Acesse a experiência de início rápido do <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/IosQuickstartPage/sourceType/docs" target="_blank">portal do Azure – Registros de aplicativo</a>.
> 1. Insira um nome para seu aplicativo e selecione **Registrar**.
> 1. Siga as instruções para baixar e configurar automaticamente o novo aplicativo com apenas um clique.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opção 2: Registrar e configurar manualmente o aplicativo e o exemplo de código
>
> #### <a name="step-1-register-your-application"></a>Etapa 1: Registre seu aplicativo
> Para registrar seu aplicativo e adicionar as informações de registro do aplicativo à solução manualmente, siga estas etapas:
>
> 1. Entre no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>.
> 1. Se você tem acesso a vários locatários, use o filtro **Diretório + assinatura** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o locatário no qual você deseja registrar um aplicativo.
> 1. Pesquise **Azure Active Directory** e selecione-o.    
> 1. Em **Gerenciar**, selecione **Registros de aplicativo** > **Novo registro**.
> 1. Insira um **Nome** para seu aplicativo. Os usuários do seu aplicativo podem ver esse nome e você pode alterá-lo mais tarde.
> 1. Selecione **Registrar**.
> 1. Em **Gerenciar**, selecione **Autenticação** > **Adicionar Plataforma** > **iOS**.
> 1. Insira o **Identificador de pacote** para seu aplicativo. O identificador de pacote é uma cadeia de caracteres exclusiva que identifica exclusivamente seu aplicativo, por exemplo, `com.<yourname>.identitysample.MSALMacOS`. Anote o valor que for usado. Observe que a configuração do iOS também é aplicável a aplicativos macOS.
> 1. Selecione **Configurar** e salve os detalhes da **Configuração da MSAL** para uso posterior neste guia de início rápido.
> 1. Selecione **Concluído**.

> [!div renderon="portal" class="sxs-lookup"]
>
> #### <a name="step-1-configure-your-application"></a>Etapa 1: Configurar seu aplicativo
> Para que o exemplo de código deste guia de início rápido funcione, adicione um **URI de Redirecionamento** compatível com o agente de autenticação.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Fazer essa alteração para mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurado](media/quickstart-v2-ios/green-check.png) Seu aplicativo já está configurado com esses atributos
>
> #### <a name="step-2-download-the-sample-project"></a>Etapa 2: Baixar o projeto de exemplo
> > [!div id="autoupdate_ios" class="nextstepaction"]
> > [Baixar o exemplo de código para iOS]()
>
> > [!div id="autoupdate_macos" class="nextstepaction"]
> > [Baixar o exemplo de código para macOS]()
> [!div renderon="docs"]
> #### <a name="step-2-download-the-sample-project"></a>Etapa 2: Baixar o projeto de exemplo
>
> - [Baixar o exemplo de código para iOS](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)
> - [Baixar o exemplo de código para macOS](https://github.com/Azure-Samples/active-directory-macOS-swift-native-v2/archive/master.zip)

#### <a name="step-3-install-dependencies"></a>Etapa 3: Instalar dependências

Em uma janela do terminal, navegue até a pasta que contém o exemplo de código baixado e execute `pod install` para instalar a biblioteca MSAL mais recente.

> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-4-your-app-is-configured-and-ready-to-run"></a>Etapa 4: seu aplicativo está configurado e pronto para ser executado
> Configuramos seu projeto com os valores das propriedades do aplicativo e ele está pronto para ser executado.
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`
>
> [!div renderon="docs"]
>#### <a name="step-4-configure-your-project"></a>Etapa 4: Configurar seu projeto
> Se você tiver selecionado a Opção 1 acima, poderá ignorar essas etapas.
> 1. Extraia o arquivo zip e abra o projeto no XCode.
> 1. Edite **ViewController.swift** e substitua a linha que começa com 'let kClientID' pelo seguinte snippet de código. Lembre-se de atualizar o valor de `kClientID` com a ID do cliente que você salvou quando registrou seu aplicativo no portal anteriormente neste início rápido:
>    ```swift
>    let kClientID = "Enter_the_Application_Id_Here"
>    ```
> 1. Se você estiver criando um aplicativo para [nuvens nacionais do Azure AD](/graph/deployments#app-registration-and-token-service-root-endpoints), substitua a linha que começa com 'let kGraphEndpoint' e 'let kAuthority' pelos pontos de extremidade corretos. Para acesso global, use valores padrão:
>     ```swift
>     let kGraphEndpoint = "https://graph.microsoft.com/"
>     let kAuthority = "https://login.microsoftonline.com/common"
>     ```
> 1. Outros pontos de extremidade estão documentados [aqui](/graph/deployments#app-registration-and-token-service-root-endpoints). Por exemplo, para executar o início rápido com o Azure AD Alemanha, use o seguinte:
>     ```swift
>     let kGraphEndpoint = "https://graph.microsoft.de/"
>     let kAuthority = "https://login.microsoftonline.de/common"
>     ```
> 1. Abra as configurações do projeto. Na seção **Identidade**, insira o **Identificador do Pacote** que você inseriu no portal.
> 1. Clique com o botão direito do mouse em **Info.plist** e selecione **Abrir Como** > **Código-Fonte**.
> 1. No nó raiz dict, substitua `Enter_the_bundle_Id_Here` pela ***ID do Pacote*** que você usou no portal.
>
>    ```xml
>    <key>CFBundleURLTypes</key>
>    <array>
>       <dict>
>          <key>CFBundleURLSchemes</key>
>          <array>
>             <string>msauth.Enter_the_Bundle_Id_Here</string>
>          </array>
>       </dict>
>    </array>
>
>    ```
> 1. Compile e execute o aplicativo.

## <a name="more-information"></a>Mais informações

Leia estas seções para saber mais sobre este guia de início rápido.

### <a name="get-msal"></a>Obter MSAL

MSAL ([MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)) é a biblioteca usada para conectar usuários e solicitar tokens usados para acessar uma API protegida pela plataforma de identidade da Microsoft. Você pode adicionar s MSAL ao seu aplicativo usando o seguinte processo:

```
$ vi Podfile

```
Adicione o seguinte a esse podfile (com o destino do seu projeto):

```
use_frameworks!

target 'MSALiOS' do
   pod 'MSAL'
end

```

Execute o comando de instalação do CocoaPods:

```pod install```

### <a name="initialize-msal"></a>Inicializar a MSAL

Você pode adicionar a referência da MSAL adicionando o seguinte código:

```swift
import MSAL
```

Em seguida, inicialize a MSAL usando o seguinte código:

```swift
let authority = try MSALAADAuthority(url: URL(string: kAuthority)!)

let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)
```

> |Em que: | Descrição |
> |---------|---------|
> | `clientId` | A ID do aplicativo registrado em *portal.azure.com* |
> | `authority` | A plataforma de identidade da Microsoft. Na maioria dos casos, ele estará em `https://login.microsoftonline.com/common` |
> | `redirectUri` | O URI de redirecionamento do aplicativo. Você pode passar “nulo” para usar o valor padrão ou o URI de redirecionamento personalizado. |

### <a name="for-ios-only-additional-app-requirements"></a>Somente para o iOS, requisitos adicionais do aplicativo

Seu aplicativo também deve ter o seguinte no `AppDelegate`. Isso permite que o SDK da MSAL lide com a resposta de token do aplicativo do agente de autenticação quando você efetuar a autenticação.

 ```swift
 func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {

        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
    }

 ```

> [!NOTE]
> No iOS 13 +, se você adotar `UISceneDelegate` em vez de `UIApplicationDelegate`, coloque esse código no retorno de chamada `scene:openURLContexts:` (confira a [Documentação da Apple](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc)).
> Se você der suporte a UISceneDelegate e UIApplicationDelegate para compatibilidade com o iOS mais antigo, o retorno de chamada da MSAL precisará ser colocado nos dois locais.

 ```swift
 func scene(_ scene: UIScene, openURLContexts URLContexts: Set<UIOpenURLContext>) {

        guard let urlContext = URLContexts.first else {
            return
        }

        let url = urlContext.url
        let sourceApp = urlContext.options.sourceApplication

        MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApp)
    }
 ```

Por fim, seu aplicativo deve ter uma entrada `LSApplicationQueriesSchemes` em ***Info.plist*** junto com `CFBundleURLTypes`. A amostra vem com isso incluído.

   ```xml
   <key>LSApplicationQueriesSchemes</key>
   <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
   </array>
   ```

### <a name="sign-in-users--request-tokens"></a>Conectar usuários e solicitar tokens

A MSAL tem dois métodos usados para adquirir tokens: `acquireToken` e `acquireTokenSilent`.

#### <a name="acquiretoken-get-a-token-interactively"></a>acquireToken: Obter um token interativamente

Algumas situações exigem que os usuários interajam com a plataforma de identidade da Microsoft. Nesses casos, o usuário final talvez precise selecionar sua conta, inserir suas credenciais ou dar consentimento às permissões do seu aplicativo. Por exemplo,

* A primeira vez que os usuários entram no aplicativo
* Se um usuário redefinir a senha, ele precisará inserir as credenciais
* Quando seu aplicativo estiver solicitando acesso a um recurso pela primeira vez
* Quando a MFA ou outras políticas de Acesso Condicional forem necessárias

```swift
let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: self.webViewParamaters!)
self.applicationContext!.acquireToken(with: parameters) { (result, error) in /* Add your handling logic */}
```

> |Em que:| Descrição |
> |---------|---------|
> | `scopes` | Contém os escopos que estão sendo solicitados (ou seja, `[ "user.read" ]` para o Microsoft Graph ou `[ "<Application ID URL>/scope" ]` para as APIs Web personalizadas (`api://<Application ID>/access_as_user`)) |

#### <a name="acquiretokensilent-get-an-access-token-silently"></a>acquireTokenSilent: Obter um token de acesso silenciosamente

Os aplicativos não deverão exigir que seus usuários entrem sempre que solicitarem um token. Se o usuário já está conectado, esse método permite que os aplicativos solicitem tokens silenciosamente.

```swift
self.applicationContext!.getCurrentAccount(with: nil) { (currentAccount, previousAccount, error) in

   guard let account = currentAccount else {
      return
   }

   let silentParams = MSALSilentTokenParameters(scopes: self.kScopes, account: account)
   self.applicationContext!.acquireTokenSilent(with: silentParams) { (result, error) in /* Add your handling logic */}
}
```

> |Em que: | Descrição |
> |---------|---------|
> | `scopes` | Contém os escopos que estão sendo solicitados (ou seja, `[ "user.read" ]` para o Microsoft Graph ou `[ "<Application ID URL>/scope" ]` para as APIs Web personalizadas (`api://<Application ID>/access_as_user`)) |
> | `account` | A conta para a qual um token está sendo solicitado. Este é um guia de início rápido sobre um aplicativo de conta única. Se quiser criar um aplicativo de várias contas, você precisará definir a lógica para identificar qual conta usar em solicitações de token usando `accountsFromDeviceForParameters:completionBlock:` e passando o `accountIdentifier` correto |

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Próximas etapas

Prossiga para o tutorial passo a passo, em que você criará um aplicativo iOS ou macOS que obtém um token de acesso da plataforma de identidade da Microsoft e o utilizará para chamar a API do Microsoft Graph.

> [!div class="nextstepaction"]
> [Tutorial: Conectar usuários e chamar o Microsoft Graph em um aplicativo iOS ou macOS](tutorial-v2-ios.md)
