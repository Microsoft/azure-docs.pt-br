---
title: 'Tutorial: Criar um aplicativo iOS ou macOS que usa a plataforma de identidade da Microsoft para autenticação | Azure'
titleSuffix: Microsoft identity platform
description: Neste tutorial, você criará um aplicativo iOS ou macOS que usa a plataforma de identidade da Microsoft para conectar usuários e receberá um token de acesso para chamar a API do Microsoft Graph em nome deles.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 09/18/2020
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: e273b1c41a9c418bf0121e87e73ec59e65f2242e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100102947"
---
# <a name="tutorial-sign-in-users-and-call-microsoft-graph-from-an-ios-or-macos-app"></a>Tutorial: Conectar usuários e chamar o Microsoft Graph de um aplicativo iOS ou macOS

Neste tutorial, você criará um aplicativo iOS ou macOS que se integra à plataforma de identidade da Microsoft para conectar usuários e obter um token de acesso para chamar a API do Microsoft Graph.

Após concluir este guia, seu aplicativo aceitará conexões de contas Microsoft pessoais (incluindo outlook.com, live.com e outras) e contas corporativas ou de estudante de qualquer empresa ou organização que utilize o Azure Active Directory. Este tutorial é aplicável a aplicativos iOS e macOS. Algumas etapas são diferentes entre as duas plataformas.

Neste tutorial:

> [!div class="checklist"]
> * Criar um projeto de aplicativo iOS ou macOS no *Xcode*
> * Registrar o aplicativo no portal do Azure
> * Adicionar código para entrada e saída do usuário
> * Adicionar código para chamar a API do Microsoft Graph
> * Testar o aplicativo

## <a name="prerequisites"></a>Pré-requisitos

- [Xcode 11.x+](https://developer.apple.com/xcode/)

## <a name="how-tutorial-app-works"></a>Como o aplicativo do tutorial funciona

![Mostra como funciona o aplicativo de exemplo gerado por este tutorial](../../../includes/media/active-directory-develop-guidedsetup-ios-introduction/iosintro.svg)

O aplicativo neste tutorial pode conectar usuários e obter dados do Microsoft Graph no nome deles. Esses dados serão acessados por meio de uma API protegida (API do Microsoft Graph, nesse caso) que exige autorização e é protegida pela plataforma de identidade da Microsoft.

Mais especificamente:

* Seu aplicativo conectará o usuário por meio de um navegador ou do Microsoft Authenticator.
* O usuário final aceitará as permissões solicitadas por seu aplicativo.
* Será emitido um token de acesso para seu aplicativo para a API do Microsoft Graph.
* O token de acesso será incluído na solicitação HTTP para API Web.
* Processe a resposta do Microsoft Graph.

Esta amostra usa a MSAL (Biblioteca de Autenticação da Microsoft) para implementar a Autenticação. A MSAL automaticamente renovará tokens, fornecerá o SSO (logon único) entre outros aplicativos no dispositivo e ajudará a gerenciar as contas.

Se quiser baixar uma versão completa do aplicativo criado neste tutorial, você encontrará ambas as versões no GitHub:

- [Exemplo de código para iOS](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/) (GitHub)
- [Exemplo de código para macOS](https://github.com/Azure-Samples/active-directory-macOS-swift-native-v2/) (GitHub)

## <a name="create-a-new-project"></a>Criar um novo projeto

1. Abra o Xcode e selecione **Crie um novo projeto Xcode**.
2. Para aplicativos iOS, selecione **iOS** > **Aplicativo de Exibição Única** e selecione **Avançar**.
3. Para aplicativos macOS, selecione **macOS** > **Cocoa App** e selecione **Avançar**.
4. Forneça um nome de produto.
5. Defina a **Linguagem** como **Swift** e selecione **Avançar**.
6. Selecione uma pasta na qual criar o aplicativo e selecione **Criar**.

## <a name="register-your-application"></a>Registre seu aplicativo

1. Entre no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>.
1. Se você tem acesso a vários locatários, use o filtro **Diretório + assinatura** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o locatário no qual você deseja registrar um aplicativo.
1. Pesquise **Azure Active Directory** e selecione-o.
1. Em **Gerenciar**, selecione **Registros de aplicativo** > **Novo registro**.
1. Insira um **Nome** para seu aplicativo. Os usuários do seu aplicativo podem ver esse nome e você pode alterá-lo mais tarde.
1. Selecione **Contas em qualquer diretório organizacional (Qualquer diretório do Azure AD – Multilocatário) e contas pessoais Microsoft (por exemplo, Skype, Xbox)** em **Tipos de contas com suporte**.
1. Selecione **Registrar**.
1. Em **Gerenciar**, selecione **Autenticação** > **Adicionar uma plataforma** > **iOS/macOS**.
1. Insira a ID do Pacote do seu projeto. Se você baixou o código, ele é `com.microsoft.identitysample.MSALiOS`. Se estiver criando seu próprio projeto, selecione seu projeto no Xcode e abra a guia **Geral**. O identificador do pacote será exibido na seção **Identidade**.
1. Selecione **Configurar** e salve a **Configuração da MSAL** exibida na página **Configuração da MSAL**, de modo que você possa inseri-la quando configurar o aplicativo mais tarde. 
1. Selecione **Concluído**.

## <a name="add-msal"></a>Adicionar MSAL

Escolha uma das seguintes maneiras de instalar a biblioteca MSAL em seu aplicativo:

### <a name="cocoapods"></a>CocoaPods

1. Se estiver usando o [CocoaPods](https://cocoapods.org/), instale `MSAL` primeiro criando um arquivo vazio chamado `podfile` na mesma pasta do arquivo `.xcodeproj` do projeto. Adicione o seguinte a `podfile`:

   ```
   use_frameworks!

   target '<your-target-here>' do
      pod 'MSAL'
   end
   ```

2. Substitua `<your-target-here>` pelo nome do projeto.
3. Em uma janela do terminal, navegue até a pasta que contém `podfile` que você criou e execute `pod install` para instalar a biblioteca MSAL.
4. Feche o Xcode e abra `<your project name>.xcworkspace` para recarregar o projeto no Xcode.

### <a name="carthage"></a>Carthage

Se estiver usando o [Carthage](https://github.com/Carthage/Carthage), instale `MSAL` adicionando-o ao `Cartfile`:

```
github "AzureAD/microsoft-authentication-library-for-objc" "master"
```

Em uma janela de terminal, no mesmo diretório que o `Cartfile` atualizado, execute o seguinte comando para que o Carthage atualize as dependências em seu projeto.

iOS:

```bash
carthage update --platform iOS
```

macOS:

```bash
carthage update --platform macOS
```

### <a name="manually"></a>Manualmente

Você também pode usar o Submódulo Git ou conferir a última versão e usá-la como uma estrutura em seu aplicativo.

## <a name="add-your-app-registration"></a>Adicionar o registro do seu aplicativo

Em seguida, adicionaremos o registro de aplicativo ao código.

Primeiro, adicione a seguinte instrução de importação à parte superior do `ViewController.swift`, bem como dos arquivos `AppDelegate.swift` e `SceneDelegate.swift`:

```swift
import MSAL
```

Em seguida, adicione o seguinte código a `ViewController.swift` antes de `viewDidLoad()`:

```swift
// Update the below to your client ID you received in the portal. The below is for running the demo only
let kClientID = "Your_Application_Id_Here"
let kGraphEndpoint = "https://graph.microsoft.com/" // the Microsoft Graph endpoint
let kAuthority = "https://login.microsoftonline.com/common" // this authority allows a personal Microsoft account and a work or school account in any organization's Azure AD tenant to sign in

let kScopes: [String] = ["user.read"] // request permission to read the profile of the signed-in user

var accessToken = String()
var applicationContext : MSALPublicClientApplication?
var webViewParameters : MSALWebviewParameters?
var currentAccount: MSALAccount?
```

O único valor que você precisa modificar acima é que o valor atribuído a `kClientID` seja a [ID do Aplicativo](./developer-glossary.md#application-id-client-id). Esse valor faz parte dos dados de Configuração da MSAL que você salvou durante a etapa no início deste tutorial para registrar o aplicativo no portal do Azure.

## <a name="configure-xcode-project-settings"></a>Definir as configurações de projeto do Xcode

Adicione um novo grupo de conjunto de chaves ao seu projeto **Autenticação e Funcionalidades**. O grupo de conjunto de chaves deve ser `com.microsoft.adalcache` no iOS e `com.microsoft.identity.universalstorage` no macOS.

![Interface do usuário do Xcode exibindo como o grupo de conjunto de chaves deve ser configurado](../../../includes/media/active-directory-develop-guidedsetup-ios-introduction/iosintro-keychainShare.png)

## <a name="for-ios-only-configure-url-schemes"></a>Somente para o iOS, Configure os esquemas de URL

Nesta etapa, você registrará `CFBundleURLSchemes` para que o usuário possa ser redirecionado novamente para o aplicativo após a entrada. A propósito, `LSApplicationQueriesSchemes` também permite que seu aplicativo faça uso do Microsoft Authenticator.

No Xcode, abra `Info.plist` como um arquivo de código-fonte e adicione o código a seguir dentro da seção `<dict>`. Substitua `[BUNDLE_ID]` pelo valor usado no portal do Azure. Se você baixou o código, o identificador do pacote é `com.microsoft.identitysample.MSALiOS`. Se estiver criando seu próprio projeto, selecione seu projeto no Xcode e abra a guia **Geral**. O identificador do pacote será exibido na seção **Identidade**.

```xml
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>msauth.[BUNDLE_ID]</string>
        </array>
    </dict>
</array>
<key>LSApplicationQueriesSchemes</key>
<array>
    <string>msauthv2</string>
    <string>msauthv3</string>
</array>
```

## <a name="for-macos-only-configure-app-sandbox"></a>Somente para macOS, configure a área restrita do aplicativo

1. Acesse as Configurações do Projeto do Xcode > **guia Funcionalidades** > **Área Restrita do Aplicativo**
2. Marque a caixa de seleção **Conexões de Saída (Cliente)** .

## <a name="create-your-apps-ui"></a>Criar a interface do usuário do aplicativo

Agora crie uma interface do usuário que inclui um botão para chamar a API do Microsoft Graph, outra para sair e uma exibição de texto para ver uma saída adicionando o seguinte código à classe `ViewController`:

### <a name="ios-ui"></a>Interface do usuário do iOS

```swift
var loggingText: UITextView!
var signOutButton: UIButton!
var callGraphButton: UIButton!
var usernameLabel: UILabel!

func initUI() {

    usernameLabel = UILabel()
    usernameLabel.translatesAutoresizingMaskIntoConstraints = false
    usernameLabel.text = ""
    usernameLabel.textColor = .darkGray
    usernameLabel.textAlignment = .right

    self.view.addSubview(usernameLabel)

    usernameLabel.topAnchor.constraint(equalTo: view.topAnchor, constant: 50.0).isActive = true
    usernameLabel.rightAnchor.constraint(equalTo: view.rightAnchor, constant: -10.0).isActive = true
    usernameLabel.widthAnchor.constraint(equalToConstant: 300.0).isActive = true
    usernameLabel.heightAnchor.constraint(equalToConstant: 50.0).isActive = true

    // Add call Graph button
    callGraphButton  = UIButton()
    callGraphButton.translatesAutoresizingMaskIntoConstraints = false
    callGraphButton.setTitle("Call Microsoft Graph API", for: .normal)
    callGraphButton.setTitleColor(.blue, for: .normal)
    callGraphButton.addTarget(self, action: #selector(callGraphAPI(_:)), for: .touchUpInside)
    self.view.addSubview(callGraphButton)

    callGraphButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    callGraphButton.topAnchor.constraint(equalTo: view.topAnchor, constant: 120.0).isActive = true
    callGraphButton.widthAnchor.constraint(equalToConstant: 300.0).isActive = true
    callGraphButton.heightAnchor.constraint(equalToConstant: 50.0).isActive = true

    // Add sign out button
    signOutButton = UIButton()
    signOutButton.translatesAutoresizingMaskIntoConstraints = false
    signOutButton.setTitle("Sign Out", for: .normal)
    signOutButton.setTitleColor(.blue, for: .normal)
    signOutButton.setTitleColor(.gray, for: .disabled)
    signOutButton.addTarget(self, action: #selector(signOut(_:)), for: .touchUpInside)
    self.view.addSubview(signOutButton)

    signOutButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    signOutButton.topAnchor.constraint(equalTo: callGraphButton.bottomAnchor, constant: 10.0).isActive = true
    signOutButton.widthAnchor.constraint(equalToConstant: 150.0).isActive = true
    signOutButton.heightAnchor.constraint(equalToConstant: 50.0).isActive = true

    let deviceModeButton = UIButton()
    deviceModeButton.translatesAutoresizingMaskIntoConstraints = false
    deviceModeButton.setTitle("Get device info", for: .normal);
    deviceModeButton.setTitleColor(.blue, for: .normal);
    deviceModeButton.addTarget(self, action: #selector(getDeviceMode(_:)), for: .touchUpInside)
    self.view.addSubview(deviceModeButton)

    deviceModeButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    deviceModeButton.topAnchor.constraint(equalTo: signOutButton.bottomAnchor, constant: 10.0).isActive = true
    deviceModeButton.widthAnchor.constraint(equalToConstant: 150.0).isActive = true
    deviceModeButton.heightAnchor.constraint(equalToConstant: 50.0).isActive = true

    // Add logging textfield
    loggingText = UITextView()
    loggingText.isUserInteractionEnabled = false
    loggingText.translatesAutoresizingMaskIntoConstraints = false

    self.view.addSubview(loggingText)

    loggingText.topAnchor.constraint(equalTo: deviceModeButton.bottomAnchor, constant: 10.0).isActive = true
    loggingText.leftAnchor.constraint(equalTo: self.view.leftAnchor, constant: 10.0).isActive = true
    loggingText.rightAnchor.constraint(equalTo: self.view.rightAnchor, constant: -10.0).isActive = true
    loggingText.bottomAnchor.constraint(equalTo: self.view.bottomAnchor, constant: 10.0).isActive = true
}

func platformViewDidLoadSetup() {

    NotificationCenter.default.addObserver(self,
                        selector: #selector(appCameToForeGround(notification:)),
                        name: UIApplication.willEnterForegroundNotification,
                        object: nil)

}

@objc func appCameToForeGround(notification: Notification) {
    self.loadCurrentAccount()
}

```

### <a name="macos-ui"></a>Interface do usuário do macOS

```swift

var callGraphButton: NSButton!
var loggingText: NSTextView!
var signOutButton: NSButton!

var usernameLabel: NSTextField!

func initUI() {

    usernameLabel = NSTextField()
    usernameLabel.translatesAutoresizingMaskIntoConstraints = false
    usernameLabel.stringValue = ""
    usernameLabel.isEditable = false
    usernameLabel.isBezeled = false
    self.view.addSubview(usernameLabel)

    usernameLabel.topAnchor.constraint(equalTo: view.topAnchor, constant: 30.0).isActive = true
    usernameLabel.rightAnchor.constraint(equalTo: view.rightAnchor, constant: -10.0).isActive = true

    // Add call Graph button
    callGraphButton  = NSButton()
    callGraphButton.translatesAutoresizingMaskIntoConstraints = false
    callGraphButton.title = "Call Microsoft Graph API"
    callGraphButton.target = self
    callGraphButton.action = #selector(callGraphAPI(_:))
    callGraphButton.bezelStyle = .rounded
    self.view.addSubview(callGraphButton)

    callGraphButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    callGraphButton.topAnchor.constraint(equalTo: view.topAnchor, constant: 50.0).isActive = true
    callGraphButton.heightAnchor.constraint(equalToConstant: 34.0).isActive = true

    // Add sign out button
    signOutButton = NSButton()
    signOutButton.translatesAutoresizingMaskIntoConstraints = false
    signOutButton.title = "Sign Out"
    signOutButton.target = self
    signOutButton.action = #selector(signOut(_:))
    signOutButton.bezelStyle = .texturedRounded
    self.view.addSubview(signOutButton)

    signOutButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    signOutButton.topAnchor.constraint(equalTo: callGraphButton.bottomAnchor, constant: 10.0).isActive = true
    signOutButton.heightAnchor.constraint(equalToConstant: 34.0).isActive = true
    signOutButton.isEnabled = false

    // Add logging textfield
    loggingText = NSTextView()
    loggingText.translatesAutoresizingMaskIntoConstraints = false

    self.view.addSubview(loggingText)

    loggingText.topAnchor.constraint(equalTo: signOutButton.bottomAnchor, constant: 10.0).isActive = true
    loggingText.leftAnchor.constraint(equalTo: self.view.leftAnchor, constant: 10.0).isActive = true
    loggingText.rightAnchor.constraint(equalTo: self.view.rightAnchor, constant: -10.0).isActive = true
    loggingText.bottomAnchor.constraint(equalTo: self.view.bottomAnchor, constant: -10.0).isActive = true
    loggingText.widthAnchor.constraint(equalToConstant: 500.0).isActive = true
    loggingText.heightAnchor.constraint(equalToConstant: 300.0).isActive = true
}

func platformViewDidLoadSetup() {}

```

Em seguida, também dentro da classe `ViewController`, substitua o método `viewDidLoad()` por:

```swift
    override func viewDidLoad() {

        super.viewDidLoad()

        initUI()

        do {
            try self.initMSAL()
        } catch let error {
            self.updateLogging(text: "Unable to create Application Context \(error)")
        }

        self.loadCurrentAccount()
        self.platformViewDidLoadSetup()
    }
```

## <a name="use-msal"></a>Usar a MSAL

### <a name="initialize-msal"></a>Inicializar a MSAL

Adicione o seguinte método `initMSAL` à classe `ViewController`:

```swift
    func initMSAL() throws {

        guard let authorityURL = URL(string: kAuthority) else {
            self.updateLogging(text: "Unable to create authority URL")
            return
        }

        let authority = try MSALAADAuthority(url: authorityURL)

        let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
        self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)
        self.initWebViewParams()
    }
```

Adicione o seguinte após o método `initMSAL` para a classe `ViewController`.

### <a name="ios-code"></a>Código do iOS:

```swift
func initWebViewParams() {
        self.webViewParameters = MSALWebviewParameters(authPresentationViewController: self)
    }
```

### <a name="macos-code"></a>Código do macOS:

```swift
func initWebViewParams() {
        self.webViewParameters = MSALWebviewParameters()
    }
```

### <a name="for-ios-only-handle-the-sign-in-callback"></a>Somente para o iOS, manipule o retorno de chamada de entrada

Abra o arquivo `AppDelegate.swift` . Para manipular o retorno de chamada após a entrada, adicione `MSALPublicClientApplication.handleMSALResponse` à classe `appDelegate` desta forma:

```swift
// Inside AppDelegate...
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {

        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}

```

**Se estiver usando o Xcode 11**, você deverá colocar o retorno de chamada da MSAL no `SceneDelegate.swift` em vez disso.
Se você der suporte a UISceneDelegate e UIApplicationDelegate para compatibilidade com o iOS mais antigo, o retorno de chamada da MSAL precisará ser colocado nos dois arquivos.

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

#### <a name="acquire-tokens"></a>Adquirir tokens

Agora, podemos implementar a lógica de processamento da interface do usuário do aplicativo e obter tokens de maneira interativa por meio da MSAL.

A MSAL expõe dois métodos principais para obter tokens: `acquireTokenSilently()` e `acquireTokenInteractively()`:

- `acquireTokenSilently()` tenta conectar um usuário e obter tokens sem nenhuma interação do usuário, desde que uma conta esteja presente. `acquireTokenSilently()` requer o fornecimento de um `MSALAccount` válido que pode ser recuperado usando uma das APIs de enumeração de conta MSAL. Este exemplo usa `applicationContext.getCurrentAccount(with: msalParameters, completionBlock: {})` para recuperar a conta atual.

- `acquireTokenInteractively()` sempre mostra a interface do usuário ao tentar conectar o usuário. Ele pode usar os cookies de sessão no navegador ou uma conta no Microsoft Authenticator para fornecer uma experiência de SSO interativo.

Adicione o código a seguir à classe `ViewController`:

```swift
    func getGraphEndpoint() -> String {
        return kGraphEndpoint.hasSuffix("/") ? (kGraphEndpoint + "v1.0/me/") : (kGraphEndpoint + "/v1.0/me/");
    }

    @objc func callGraphAPI(_ sender: AnyObject) {

        self.loadCurrentAccount { (account) in

            guard let currentAccount = account else {

                // We check to see if we have a current logged in account.
                // If we don't, then we need to sign someone in.
                self.acquireTokenInteractively()
                return
            }

            self.acquireTokenSilently(currentAccount)
        }
    }

    typealias AccountCompletion = (MSALAccount?) -> Void

    func loadCurrentAccount(completion: AccountCompletion? = nil) {

        guard let applicationContext = self.applicationContext else { return }

        let msalParameters = MSALParameters()
        msalParameters.completionBlockQueue = DispatchQueue.main

        applicationContext.getCurrentAccount(with: msalParameters, completionBlock: { (currentAccount, previousAccount, error) in

            if let error = error {
                self.updateLogging(text: "Couldn't query current account with error: \(error)")
                return
            }

            if let currentAccount = currentAccount {

                self.updateLogging(text: "Found a signed in account \(String(describing: currentAccount.username)). Updating data for that account...")

                self.updateCurrentAccount(account: currentAccount)

                if let completion = completion {
                    completion(self.currentAccount)
                }

                return
            }

            self.updateLogging(text: "Account signed out. Updating UX")
            self.accessToken = ""
            self.updateCurrentAccount(account: nil)

            if let completion = completion {
                completion(nil)
            }
        })
    }
```

#### <a name="get-a-token-interactively"></a>Obter um token interativamente

O snippet de código a seguir obtém um token pela primeira vez criando um objeto `MSALInteractiveTokenParameters` e chamando `acquireToken`. Em seguida, você adicionará o código que:

1. Cria `MSALInteractiveTokenParameters` com escopos.
2. Chama `acquireToken()` com os parâmetros criados.
3. Trata os erros. Para obter mais detalhes, confira o [Guia de tratamento de erro da MSAL para iOS e macOS](msal-error-handling-ios.md).
4. Trata o caso de sucesso.

Adicione o código a seguir à classe `ViewController` .

```swift
func acquireTokenInteractively() {

    guard let applicationContext = self.applicationContext else { return }
    guard let webViewParameters = self.webViewParameters else { return }

    // #1
    let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: webViewParameters)
    parameters.promptType = .selectAccount

    // #2
    applicationContext.acquireToken(with: parameters) { (result, error) in

        // #3
        if let error = error {

            self.updateLogging(text: "Could not acquire token: \(error)")
            return
        }

        guard let result = result else {

            self.updateLogging(text: "Could not acquire token: No result returned")
            return
        }

        // #4
        self.accessToken = result.accessToken
        self.updateLogging(text: "Access token is \(self.accessToken)")
        self.updateCurrentAccount(account: result.account)
        self.getContentWithToken()
    }
}
```

A propriedade `promptType` de `MSALInteractiveTokenParameters` configura o comportamento das solicitação de autenticação e de consentimento. Os seguintes valores têm suporte:

- `.promptIfNecessary` (padrão) – o usuário é solicitado somente se necessário. A experiência de SSO é determinada pela presença de cookies no modo de exibição da Web e pelo tipo de conta. Se vários usuários estiverem conectados, a experiência de seleção de conta será apresentada. *Esse é o comportamento padrão*.
- `.selectAccount` – se nenhum usuário for especificado, o modo de exibição da Web da autenticação apresentará uma lista de contas conectadas para o usuário selecionar.
- `.login` – requer que o usuário autentique no modo de exibição da Web. Somente uma conta poderá ser conectada por vez se você especificar esse valor.
- `.consent` – requer que o usuário dê consentimento ao conjunto atual de escopos da solicitação.

#### <a name="get-a-token-silently"></a>Obter um token no modo silencioso

Para adquirir um token atualizado silenciosamente, adicione o código a seguir à classe `ViewController`. Ele cria um objeto `MSALSilentTokenParameters` e chama `acquireTokenSilent()`:

```swift

    func acquireTokenSilently(_ account : MSALAccount!) {

        guard let applicationContext = self.applicationContext else { return }

        /**

         Acquire a token for an existing account silently

         - forScopes:           Permissions you want included in the access token received
         in the result in the completionBlock. Not all scopes are
         guaranteed to be included in the access token returned.
         - account:             An account object that we retrieved from the application object before that the
         authentication flow will be locked down to.
         - completionBlock:     The completion block that will be called when the authentication
         flow completes, or encounters an error.
         */

        let parameters = MSALSilentTokenParameters(scopes: kScopes, account: account)

        applicationContext.acquireTokenSilent(with: parameters) { (result, error) in

            if let error = error {

                let nsError = error as NSError

                // interactionRequired means we need to ask the user to sign-in. This usually happens
                // when the user's Refresh Token is expired or if the user has changed their password
                // among other possible reasons.

                if (nsError.domain == MSALErrorDomain) {

                    if (nsError.code == MSALError.interactionRequired.rawValue) {

                        DispatchQueue.main.async {
                            self.acquireTokenInteractively()
                        }
                        return
                    }
                }

                self.updateLogging(text: "Could not acquire token silently: \(error)")
                return
            }

            guard let result = result else {

                self.updateLogging(text: "Could not acquire token: No result returned")
                return
            }

            self.accessToken = result.accessToken
            self.updateLogging(text: "Refreshed Access token is \(self.accessToken)")
            self.updateSignOutButton(enabled: true)
            self.getContentWithToken()
        }
    }
```

### <a name="call-the-microsoft-graph-api"></a>Chamar a API do Microsoft Graph

Depois que você obtiver um token, seu aplicativo poderá usá-lo no cabeçalho HTTP para fazer uma solicitação autorizada ao Microsoft Graph:

| chave de cabeçalho    | value                 |
| ------------- | --------------------- |
| Autorização | Portador \<access-token> |

Adicione o código a seguir à classe `ViewController`:

```swift
    func getContentWithToken() {

        // Specify the Graph API endpoint
        let graphURI = getGraphEndpoint()
        let url = URL(string: graphURI)
        var request = URLRequest(url: url!)

        // Set the Authorization header for the request. We use Bearer tokens, so we specify Bearer + the token we got from the result
        request.setValue("Bearer \(self.accessToken)", forHTTPHeaderField: "Authorization")

        URLSession.shared.dataTask(with: request) { data, response, error in

            if let error = error {
                self.updateLogging(text: "Couldn't get graph result: \(error)")
                return
            }

            guard let result = try? JSONSerialization.jsonObject(with: data!, options: []) else {

                self.updateLogging(text: "Couldn't deserialize result JSON")
                return
            }

            self.updateLogging(text: "Result from Graph: \(result))")

            }.resume()
    }
```

Confira [API do Microsoft Graph](https://graph.microsoft.com) para saber mais sobre a API do Microsoft Graph.

### <a name="use-msal-for-sign-out"></a>Usar a MSAL para saída

Em seguida, adicione o suporte para saída.

> [!Important]
> Sair com a MSAL remove todas as informações conhecidas sobre um usuário do aplicativo, bem como remove uma sessão ativa no dispositivo do usuário quando permitido pela configuração do dispositivo. Você também pode opcionalmente desconectar o usuário do navegador.

Para adicionar a funcionalidade de saída, adicione o código a seguir dentro da classe `ViewController`.

```swift
@objc func signOut(_ sender: AnyObject) {

        guard let applicationContext = self.applicationContext else { return }

        guard let account = self.currentAccount else { return }

        do {

            /**
             Removes all tokens from the cache for this application for the provided account

             - account:    The account to remove from the cache
             */

            let signoutParameters = MSALSignoutParameters(webviewParameters: self.webViewParameters!)
            signoutParameters.signoutFromBrowser = false // set this to true if you also want to signout from browser or webview

            applicationContext.signout(with: account, signoutParameters: signoutParameters, completionBlock: {(success, error) in

                if let error = error {
                    self.updateLogging(text: "Couldn't sign out account with error: \(error)")
                    return
                }

                self.updateLogging(text: "Sign out completed successfully")
                self.accessToken = ""
                self.updateCurrentAccount(account: nil)
            })

        }
    }
```

### <a name="enable-token-caching"></a>Ativar cache de token

Por padrão, a MSAL armazena em cache os tokens do seu aplicativo nos conjuntos de chaves do iOS ou macOS.

Para habilitar o cache de token:

1. Verifique se seu aplicativo está assinado corretamente
1. Acesse as Configurações do Projeto do Xcode > **guia Funcionalidades** > **Habilitar o Compartilhamento do Conjunto de Chaves**
1. Selecione **+** e insira um dos **Grupos de conjunto de chaves**:
    - iOS: `com.microsoft.adalcache`
    - macOS - `com.microsoft.identity.universalstorage`

### <a name="add-helper-methods"></a>Adicionar métodos auxiliares
Adicione os seguintes métodos auxiliares à classe `ViewController` para concluir a amostra.

### <a name="ios-ui"></a>Interface do usuário do iOS:

``` swift

    func updateLogging(text : String) {

        if Thread.isMainThread {
            self.loggingText.text = text
        } else {
            DispatchQueue.main.async {
                self.loggingText.text = text
            }
        }
    }

    func updateSignOutButton(enabled : Bool) {
        if Thread.isMainThread {
            self.signOutButton.isEnabled = enabled
        } else {
            DispatchQueue.main.async {
                self.signOutButton.isEnabled = enabled
            }
        }
    }

    func updateAccountLabel() {

        guard let currentAccount = self.currentAccount else {
            self.usernameLabel.text = "Signed out"
            return
        }

        self.usernameLabel.text = currentAccount.username
    }

    func updateCurrentAccount(account: MSALAccount?) {
        self.currentAccount = account
        self.updateAccountLabel()
        self.updateSignOutButton(enabled: account != nil)
    }
```

### <a name="macos-ui"></a>Interface do usuário do macOS:

```swift
    func updateLogging(text : String) {

        if Thread.isMainThread {
            self.loggingText.string = text
        } else {
            DispatchQueue.main.async {
                self.loggingText.string = text
            }
        }
    }

    func updateSignOutButton(enabled : Bool) {
        if Thread.isMainThread {
            self.signOutButton.isEnabled = enabled
        } else {
            DispatchQueue.main.async {
                self.signOutButton.isEnabled = enabled
            }
        }
    }

     func updateAccountLabel() {

         guard let currentAccount = self.currentAccount else {
            self.usernameLabel.stringValue = "Signed out"
            return
        }

        self.usernameLabel.stringValue = currentAccount.username ?? ""
        self.usernameLabel.sizeToFit()
     }

     func updateCurrentAccount(account: MSALAccount?) {
        self.currentAccount = account
        self.updateAccountLabel()
        self.updateSignOutButton(enabled: account != nil)
    }
```

### <a name="for-ios-only-get-additional-device-information"></a>Somente para iOS, obtém informações adicionais do dispositivo

Use o seguinte código para ler a configuração atual do dispositivo, incluindo se o dispositivo está configurado como compartilhado:

```swift
    @objc func getDeviceMode(_ sender: AnyObject) {

        if #available(iOS 13.0, *) {
            self.applicationContext?.getDeviceInformation(with: nil, completionBlock: { (deviceInformation, error) in

                guard let deviceInfo = deviceInformation else {
                    self.updateLogging(text: "Device info not returned. Error: \(String(describing: error))")
                    return
                }

                let isSharedDevice = deviceInfo.deviceMode == .shared
                let modeString = isSharedDevice ? "shared" : "private"
                self.updateLogging(text: "Received device info. Device is in the \(modeString) mode.")
            })
        } else {
            self.updateLogging(text: "Running on older iOS. GetDeviceInformation API is unavailable.")
        }
    }
```

### <a name="multi-account-applications"></a>Aplicativos de várias contas

Este aplicativo foi criado para um cenário de conta única. A MSAL também dá suporte a cenários de várias contas, mas requer algum trabalho adicional dos aplicativos. Você precisará criar a interface do usuário para ajudar a selecionar qual conta deseja usar para cada ação que requer tokens do usuário. Como alternativa, seu aplicativo pode implementar uma heurística para selecionar qual conta usar consultando todas as contas da MSAL. Por exemplo, confira [API](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALPublicClientApplication.html#/c:objc(cs)MSALPublicClientApplication(im)accountsFromDeviceForParameters:completionBlock:) `accountsFromDeviceForParameters:completionBlock:`

## <a name="test-your-app"></a>Testar seu aplicativo

Compilar e implantar o aplicativo em um simulador ou dispositivo de teste. Você deve conseguir conectar e obter tokens para o Azure AD ou contas Microsoft pessoais.

Na primeira vez que um usuário entrar no aplicativo, ele será solicitado pela identidade da Microsoft a fornecer seu consentimento às permissões solicitadas. Embora a maioria dos usuários consiga fornecer seu consentimento, alguns locatários do Azure AD desabilitaram o consentimento do usuário, o que exige que os administradores forneçam consentimento em nome de todos os usuários. Para dar suporte a esse cenário, registre os escopos do seu aplicativo no portal do Azure.

Após entrar, este aplicativo exibirá os dados retornados do ponto de extremidade `/me` do Microsoft Graph.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como criar aplicativos móveis que chamam APIs Web protegidas em nossa série de cenários com várias partes.

> [!div class="nextstepaction"]
> [Cenário: Aplicativo móvel que chama APIs Web](scenario-mobile-overview.md)
