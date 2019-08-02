---
title: Adicionar autenticação no iOS com aplicativos móveis do Azure
description: Aprenda a usar os aplicativos móveis do Azure para autenticar usuários de seu aplicativo iOS por meio de uma variedade de provedores de identidade, incluindo AAD, Google, Facebook, Twitter e Microsoft.
services: app-service\mobile
documentationcenter: ios
author: elamalani
manager: crdun
editor: ''
ms.assetid: ef3d3cbe-e7ca-45f9-987f-80c44209dc06
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 88e278ced5cbddb132cdc2f760864df119762088
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449129"
---
# <a name="add-authentication-to-your-ios-app"></a>Adicione autenticação ao seu aplicativo do iOS
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> Visual Studio App Center está investindo em novos e integrados serviços essenciais para o desenvolvimento de aplicativos móveis. Os desenvolvedores podem usar **construir**, **teste** e **distribuir** services para configurar o pipeline de integração contínua e entrega. Depois que o aplicativo é implantado, os desenvolvedores podem monitorar o status e o uso do seu aplicativo usando o **Analytics** e **diagnóstico** serviços e entre em contato com usuários usando o **enviar por Push** serviço. Os desenvolvedores também podem aproveitar **Auth** autenticar seus usuários e **dados** serviço para manter e sincronizar dados do aplicativo na nuvem. Fazer check-out [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-ios-get-started-users) hoje mesmo.
>

Neste tutorial, você adicionará a autenticação ao projeto de [início rápido do iOS] usando um provedor de identidade com suporte. Este tutorial baseia-se no tutorial de [início rápido do iOS] , que você deve concluir primeiro.

## <a name="register"></a>Registrar seu aplicativo para autenticação e configurar o Serviço de Aplicativo
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Adicionar seu aplicativo às URLs de redirecionamento externo permitidas

A autenticação segura exige que você defina um novo esquema de URL para seu aplicativo.  Isso permite que o sistema de autenticação redirecione para seu aplicativo após a conclusão do processo de autenticação.  Neste tutorial, usamos sempre o esquema de URL _appname_.  No entanto, você pode usar o esquema de URL que quiser.  Ele deve ser exclusivo para seu aplicativo móvel.  Para habilitar o redirecionamento no lado do servidor:

1. No [portal do Azure], selecione sua conta.

2. Clique na opção de menu **Autenticação/Autorização**.

3. Clique em **Azure Active Directory** na seção **Provedores de Autenticação**.

4. Defina o **Modo de gerenciamento** como **Avançado**.

5. Em **URLs de Redirecionamento Externo Permitidas**, insira `appname://easyauth.callback`.  O _appname_ na cadeia de caracteres é o esquema de URL para seu aplicativo móvel.  Ele deve seguir as especificações de URL normal para um protocolo (use somente letras e números e inicie com uma letra).  Você deve anotar a cadeia de caracteres escolhida, já que precisará ajustar o código do aplicativo móvel com o esquema de URL em vários lugares.

6. Clique em **OK**.

7. Clique em **Salvar**.

## <a name="permissions"></a>Restringir permissões a usuários autenticados
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

No Xcode, pressione **Executar** para iniciar o aplicativo. Uma exceção é criada porque o aplicativo tenta acessar o back-end como um usuário não autenticado, mas a tabela *TodoItem* agora exige autenticação.

## <a name="add-authentication"></a>Adicionar autenticação ao aplicativo
**Objective-C**:

1. Em seu Mac, abra *QSTodoListViewController.m* em Xcode e adicione o seguinte método:

    ```Objective-C
    - (void)loginAndGetData
    {
        QSAppDelegate *appDelegate = (QSAppDelegate *)[UIApplication sharedApplication].delegate;
        appDelegate.qsTodoService = self.todoService;

        [self.todoService.client loginWithProvider:@"google" urlScheme:@"appname" controller:self animated:YES completion:^(MSUser * _Nullable user, NSError * _Nullable error) {
            if (error) {
                NSLog(@"Login failed with error: %@, %@", error, [error userInfo]);
            }
            else {
                self.todoService.client.currentUser = user;
                NSLog(@"User logged in: %@", user.userId);

                [self refresh];
            }
        }];
    }
    ```

    Altere *google* para *microsoftaccount*, *twitter*, *facebook* ou *windowsazureactivedirectory* se não estiver usando o Google como seu provedor de identidade. Se você usar o Facebook, você deve [colocar os domínios do Facebook][1] em seu aplicativo.

    Substitua o **urlScheme** por um nome exclusivo para seu aplicativo.  O urlScheme deve ser o mesmo que o protocolo de esquema de URL especificado no campo **URLs de redirecionamento externo permitidas** no portal do Azure. O urlScheme é usado pelo retorno de chamada de autenticação a fim de voltar para seu aplicativo depois que a solicitação de autenticação é concluída.

2. Substitua `[self refresh]` em `viewDidLoad` em *QSTodoListViewController.m* pelo seguinte código:

    ```Objective-C
    [self loginAndGetData];
    ```

3. Abra o arquivo `QSAppDelegate.h`e adicione o seguinte código:

    ```Objective-C
    #import "QSTodoService.h"

    @property (strong, nonatomic) QSTodoService *qsTodoService;
    ```

4. Abra o arquivo `QSAppDelegate.m`e adicione o seguinte código:

    ```Objective-C
    - (BOOL)application:(UIApplication *)application openURL:(NSURL *)url options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
    {
        if ([[url.scheme lowercaseString] isEqualToString:@"appname"]) {
            // Resume login flow
            return [self.qsTodoService.client resumeWithURL:url];
        }
        else {
            return NO;
        }
    }
    ```

   Adicione o código diretamente antes da linha que tem `#pragma mark - Core Data stack`.  Substitua os _appname_ com o valor de urlScheme que você usou na etapa 1.

5. Abra o arquivo `AppName-Info.plist` (substituindo AppName pelo nome de seu aplicativo) e adicione o seguinte código:

    ```XML
    <key>CFBundleURLTypes</key>
    <array>
        <dict>
            <key>CFBundleURLName</key>
            <string>com.microsoft.azure.zumo</string>
            <key>CFBundleURLSchemes</key>
            <array>
                <string>appname</string>
            </array>
        </dict>
    </array>
    ```

    Esse código deve ser colocado dentro do elemento `<dict>`.  Substitua a cadeia de caracteres _appname_ (dentro da matriz de **CFBundleURLSchemes**) pelo nome do aplicativo que você escolheu na etapa 1.  Você também pode fazer essas alterações no editor de plist: clique no arquivo `AppName-Info.plist` no XCode para abrir o editor de plist.

    Substitua a cadeia de caracteres `com.microsoft.azure.zumo` para **CFBundleURLName** pelo identificador do pacote Apple.

6. Pressione *Executar* para iniciar o aplicativo e faça logon. Após ter feito o logon você poderá exibir a lista de Tarefas pendentes e fazer atualizações.

**Swift**:

1. Em seu Mac, abra *ToDoTableViewController.swift* em Xcode e adicione o seguinte método:

    ```swift
    func loginAndGetData() {

        guard let client = self.table?.client, client.currentUser == nil else {
            return
        }

        let appDelegate = UIApplication.shared.delegate as! AppDelegate
        appDelegate.todoTableViewController = self

        let loginBlock: MSClientLoginBlock = {(user, error) -> Void in
            if (error != nil) {
                print("Error: \(error?.localizedDescription)")
            }
            else {
                client.currentUser = user
                print("User logged in: \(user?.userId)")
            }
        }

        client.login(withProvider:"google", urlScheme: "appname", controller: self, animated: true, completion: loginBlock)

    }
    ```

    Altere *google* para *microsoftaccount*, *twitter*, *facebook* ou *windowsazureactivedirectory* se não estiver usando o Google como seu provedor de identidade. Se você usar o Facebook, você deve [colocar os domínios do Facebook][1] em seu aplicativo.

    Substitua o **urlScheme** por um nome exclusivo para seu aplicativo.  O urlScheme deve ser o mesmo que o protocolo de esquema de URL especificado no campo **URLs de redirecionamento externo permitidas** no portal do Azure. O urlScheme é usado pelo retorno de chamada de autenticação a fim de voltar para seu aplicativo depois que a solicitação de autenticação é concluída.

2. Remova as linhas `self.refreshControl?.beginRefreshing()` e `self.onRefresh(self.refreshControl)` ao final de `viewDidLoad()` em *ToDoTableViewController.swift*. Adicione uma chamada para `loginAndGetData()` em seu lugar:

    ```swift
    loginAndGetData()
    ```

3. Abra o arquivo `AppDelegate.swift` e adicione a seguinte linha à classe `AppDelegate`:

    ```swift
    var todoTableViewController: ToDoTableViewController?

    func application(_ application: UIApplication, openURL url: NSURL, options: [UIApplicationOpenURLOptionsKey : Any] = [:]) -> Bool {
        if url.scheme?.lowercased() == "appname" {
            return (todoTableViewController!.table?.client.resume(with: url as URL))!
        }
        else {
            return false
        }
    }
    ```

    Substitua os _appname_ com o valor de urlScheme que você usou na etapa 1.

4. Abra o arquivo `AppName-Info.plist` (substituindo AppName pelo nome de seu aplicativo) e adicione o seguinte código:

    ```xml
    <key>CFBundleURLTypes</key>
    <array>
        <dict>
            <key>CFBundleURLName</key>
            <string>com.microsoft.azure.zumo</string>
            <key>CFBundleURLSchemes</key>
            <array>
                <string>appname</string>
            </array>
        </dict>
    </array>
    ```

    Esse código deve ser colocado dentro do elemento `<dict>`.  Substitua a cadeia de caracteres _appname_ (dentro da matriz de **CFBundleURLSchemes**) pelo nome do aplicativo que você escolheu na etapa 1.  Você também pode fazer essas alterações no editor de plist: clique no arquivo `AppName-Info.plist` no XCode para abrir o editor de plist.

    Substitua a cadeia de caracteres `com.microsoft.azure.zumo` para **CFBundleURLName** pelo identificador do pacote Apple.

5. Pressione *Executar* para iniciar o aplicativo e faça logon. Após ter feito o logon você poderá exibir a lista de Tarefas pendentes e fazer atualizações.

A autenticação do Serviço de Aplicativo usa a comunicação interaplicativos da Apple.  Para obter mais detalhes sobre esse assunto, consulte o [documentação da Apple][2]
<!-- URLs. -->

[1]: https://developers.facebook.com/docs/ios/ios9#whitelist
[2]: https://developer.apple.com/library/content/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Inter-AppCommunication/Inter-AppCommunication.html
[Portal do Azure]: https://portal.azure.com

[início rápido do iOS]: app-service-mobile-ios-get-started.md

