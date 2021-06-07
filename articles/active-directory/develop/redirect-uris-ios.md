---
title: Usar URIs de redirecionamento com MSAL (iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre as diferenças entre a biblioteca de autenticação da Microsoft para ObjectiveC (MSAL para iOS e macOS) e a biblioteca de autenticação do Azure AD para ObjectiveC (ADAL. ObjC) e como migrar entre eles.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: jak
ms.custom: aaddev
ms.openlocfilehash: 2c728854846a3add1f5b41cb318d7dc5fd86e742
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98064191"
---
# <a name="using-redirect-uris-with-the-microsoft-authentication-library-for-ios-and-macos"></a>Usar URIs de redirecionamento com a biblioteca de autenticação da Microsoft para iOS e macOS

Quando um usuário é autenticado, o Azure AD (Azure Active Directory) envia o token para o aplicativo usando o URI de redirecionamento registrado no aplicativo do Azure AD.

A MSAL (Biblioteca de Autenticação da Microsoft) requer que o URI de redirecionamento seja registrado no aplicativo Azure AD em um formato específico. A MSAL usará o URI de redirecionamento padrão se você não especificar um. O formato é `msauth.[Your_Bundle_Id]://auth`.

O formato do URI de redirecionamento padrão funciona para a maioria dos aplicativos e cenários, incluindo autenticação agenciada e a exibição da Web do sistema. Use o formato padrão sempre que possível.

No entanto, talvez seja necessário alterar o URI de redirecionamento para cenários avançados, conforme descrito abaixo.

## <a name="scenarios-that-require-a-different-redirect-uri"></a>Cenários que exigem um URI de redirecionamento diferente

### <a name="cross-app-single-sign-on-sso"></a>SSO (logon único) entre aplicativos

Para que a Plataforma de Identidade da Microsoft compartilhe tokens entre aplicativos, cada aplicativo precisa ter a mesma ID do cliente ou do aplicativo. Esse é o identificador exclusivo fornecido quando você registrou seu aplicativo no portal (não a ID do pacote de aplicativos que você registra por aplicativo na Apple).

Os URIs de redirecionamento precisam ser diferentes para cada aplicativo iOS. Isso permite que o serviço de identidade da Microsoft identifique exclusivamente aplicativos diferentes que compartilham uma ID de aplicativo. Cada aplicativo pode ter vários URIs de redirecionamento registrados no portal do Azure. Cada aplicativo em seu pacote terá um URI de redirecionamento diferente. Por exemplo:

Considerando o registro de aplicativo a seguir no portal do Azure:

* ID do cliente: `ABCDE-12345` (essa é uma ID de cliente único)
* RedirectUris: `msauth.com.contoso.app1://auth` , `msauth.com.contoso.app2://auth` , `msauth.com.contoso.app3://auth`

App1 usa Redirect `msauth.com.contoso.app1://auth` . \
App2 usa `msauth.com.contoso.app2://auth` . \
App3 usa `msauth.com.contoso.app1://auth` .

### <a name="migrating-from-adal-to-msal"></a>Migrar da ADAL para a MSAL

Ao migrar o código que usou a ADAL (Biblioteca de Autenticação do Azure AD) para a MSAL, você já pode ter um URI de redirecionamento configurado para seu aplicativo. É possível continuar usando o mesmo URI de redirecionamento, desde que seu aplicativo ADAL tenha sido configurado para dar suporte a cenários agenciados e seu URI de redirecionamento atenda aos requisitos de formato de URI de redirecionamento da MSAL.

## <a name="msal-redirect-uri-format-requirements"></a>Requisitos do formato do URI de redirecionamento da MSAL

* O URI de redirecionamento da MSAL deve estar no formato `<scheme>://host`

    Em que `<scheme>` é uma cadeia de caracteres exclusiva que identifica seu aplicativo. Ele se baseia principalmente no Identificador de Pacote do seu aplicativo para garantir a exclusividade. Por exemplo, se a ID de Pacote do seu aplicativo fosse `com.contoso.myapp`, seu URI de redirecionamento estaria no formato: `msauth.com.contoso.myapp://auth`.

    Se estiver migrando da ADAL, seu URI de redirecionamento provavelmente terá este formato: `<scheme>://[Your_Bundle_Id]`, em que `scheme` é uma cadeia de caracteres exclusiva. Esse formato continuará funcionando quando você usar a MSAL.

* `<scheme>` deve estar registrado no Info.plist do seu aplicativo em `CFBundleURLTypes > CFBundleURLSchemes`.  Neste exemplo, o Info.plist foi aberto como código-fonte:

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
    ```

A MSAL verificará se o URI de redirecionamento pode registrar corretamente; caso contrário, retornará um erro.
    
* Se desejar usar links universais como um URI de redirecionamento, o `<scheme>` deverá ser `https` e não precisará ser declarado em `CFBundleURLSchemes`. Em vez disso, configure o aplicativo e o domínio de acordo com as instruções da Apple em [Links Universais para Desenvolvedores](https://developer.apple.com/ios/universal-links/) e chame o método `handleMSALResponse:sourceApplication:` de `MSALPublicClientApplication` quando seu aplicativo for aberto por um link universal.

## <a name="use-a-custom-redirect-uri"></a>Usar um URI de redirecionamento personalizado

Para usar um URI de redirecionamento personalizado, passe o parâmetro `redirectUri` para `MSALPublicClientApplicationConfig` e passe esse objeto para `MSALPublicClientApplication` quando inicializá-lo. Se o URI de redirecionamento for inválido, o inicializador retornará `nil` e definirá o `redirectURIError`com informações adicionais.  Por exemplo:

Objective-C:

```objc
MSALPublicClientApplicationConfig *config =
        [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"your-client-id"
                                                        redirectUri:@"your-redirect-uri"
                                                        authority:authority];
NSError *redirectURIError;
MSALPublicClientApplication *application =
        [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&redirectURIError];
```

Swift:

```swift
let config = MSALPublicClientApplicationConfig(clientId: "your-client-id",
                                            redirectUri: "your-redirect-uri",
                                              authority: authority)
do {
  let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application          
} catch let error as NSError {
  // handle error here
}       
```



## <a name="handle-the-url-opened-event"></a>Manipular o evento aberto da URL

Seu aplicativo deverá chamar a MSAL quando receber qualquer resposta por meio de esquemas de URL ou links universais. Chame o método `handleMSALResponse:sourceApplication:` de `MSALPublicClientApplication` quando seu aplicativo for aberto. Veja um exemplo para esquemas personalizados:

Objective-C:

```objc
- (BOOL)application:(UIApplication *)app
            openURL:(NSURL *)url
            options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
{
    return [MSALPublicClientApplication handleMSALResponse:url 
                                         sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

Swift:

```swift
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
    return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}
```



## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os [Fluxos de autenticação e cenários de aplicativos](authentication-flows-app-scenarios.md)
