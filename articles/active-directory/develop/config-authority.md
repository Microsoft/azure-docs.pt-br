---
title: Configurar provedores de identidade (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Saiba como usar autoridades diferentes, como B2C, nuvens soberanas e usuários convidados, com MSAL para iOS e macOS.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: d8a176fff0da932d0fafd40b9ab895b635acc5f6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96169436"
---
# <a name="how-to-configure-msal-for-ios-and-macos-to-use-different-identity-providers"></a>Como configurar o MSAL para iOS e macOS para usar provedores de identidade diferentes

Este artigo mostrará como configurar seu aplicativo de biblioteca de autenticação da Microsoft para iOS e macOS (MSAL) para autoridades diferentes, como Azure Active Directory (Azure AD), B2C (Business-to-consumidor), nuvens soberanas e usuários convidados.  Ao longo deste artigo, você geralmente pode considerar uma autoridade como um provedor de identidade.

## <a name="default-authority-configuration"></a>Configuração de autoridade padrão

`MSALPublicClientApplication` é configurado com uma URL de autoridade padrão do `https://login.microsoftonline.com/common` , que é adequada para a maioria dos cenários de Azure Active Directory (AAD). A menos que você esteja implementando cenários avançados como nuvens nacionais ou trabalhando com B2C, você não precisará alterá-lo.

> [!NOTE]
> Não há suporte para a autenticação moderna com Serviços de Federação do Active Directory (AD FS) como provedor de identidade (ADFS) (consulte [ADFS para desenvolvedores](/windows-server/identity/ad-fs/overview/ad-fs-openid-connect-oauth-flows-scenarios) para obter detalhes). O ADFS tem suporte por meio da Federação.

## <a name="change-the-default-authority"></a>Alterar a autoridade padrão

Em alguns cenários, como B2C (Business-to-Consumer), talvez seja necessário alterar a autoridade padrão.

### <a name="b2c"></a>B2C

Para trabalhar com o B2C, a [MSAL (biblioteca de autenticação da Microsoft)](reference-v2-libraries.md) requer uma configuração de autoridade diferente. O MSAL reconhece o formato de URL de uma autoridade como B2C por si só. O formato de autoridade B2C reconhecido é `https://<host>/tfp/<tenant>/<policy>` , por exemplo `https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy` . No entanto, você também pode usar qualquer outra URL de autoridade B2C com suporte, declarando autoridade como autoridade B2C explicitamente.

Para dar suporte a um formato de URL arbitrário para B2C, o `MSALB2CAuthority` pode ser definido com uma URL arbitrária, como esta:

Objective-C
```objc
NSURL *authorityURL = [NSURL URLWithString:@"arbitrary URL"];
MSALB2CAuthority *b2cAuthority = [[MSALB2CAuthority alloc] initWithURL:authorityURL
                                                                     error:&b2cAuthorityError];
```
Swift
```swift
guard let authorityURL = URL(string: "arbitrary URL") else {
    // Handle error
    return
}
let b2cAuthority = try MSALB2CAuthority(url: authorityURL)
```

Todas as autoridades B2C que não usam o formato de autoridade B2C padrão devem ser declaradas como autoridades conhecidas.

Adicione cada autoridade B2C diferente à lista de autoridades conhecidas, mesmo se as autoridades só diferirem na política.

Objective-C
```objc
MSALPublicClientApplicationConfig *b2cApplicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:b2cAuthority];
b2cApplicationConfig.knownAuthorities = @[b2cAuthority];
```
Swift
```swift
let b2cApplicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: b2cAuthority)
b2cApplicationConfig.knownAuthorities = [b2cAuthority]
```

Quando seu aplicativo solicita uma nova política, a URL da autoridade precisa ser alterada porque a URL da autoridade é diferente para cada política. 

Para configurar um aplicativo B2C, defina `@property MSALAuthority *authority` com uma instância do `MSALB2CAuthority` em `MSALPublicClientApplicationConfig` antes de criar `MSALPublicClientApplication` , desta forma:

Objective-C
```ObjC
    // Create B2C authority URL
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy"];
    
    MSALB2CAuthority *b2cAuthority = [[MSALB2CAuthority alloc] initWithURL:authorityURL
                                                                     error:&b2cAuthorityError];
    if (!b2cAuthority)
    {
        // Handle error
        return;
    }
    
    // Create MSALPublicClientApplication configuration
    MSALPublicClientApplicationConfig *b2cApplicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                                   initWithClientId:@"your-client-id"
                                                                   redirectUri:@"your-redirect-uri"
                                                                   authority:b2cAuthority];

    // Initialize MSALPublicClientApplication
    MSALPublicClientApplication *b2cApplication =
    [[MSALPublicClientApplication alloc] initWithConfiguration:b2cApplicationConfig error:&error];
    
    if (!b2cApplication)
    {
        // Handle error
        return;
    }
```
Swift
```swift
do{
    // Create B2C authority URL
    guard let authorityURL = URL(string: "https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy") else {
        // Handle error
        return
    }
    let b2cAuthority = try MSALB2CAuthority(url: authorityURL)

    // Create MSALPublicClientApplication configuration
    let b2cApplicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: b2cAuthority)

    // Initialize MSALPublicClientApplication
    let b2cApplication = try MSALPublicClientApplication(configuration: b2cApplicationConfig)
} catch {
    // Handle error
}
```

### <a name="sovereign-clouds"></a>Nuvens soberanas

Se seu aplicativo for executado em uma nuvem do soberanas, talvez seja necessário alterar a URL da autoridade no `MSALPublicClientApplication` . O exemplo a seguir define a URL da autoridade para trabalhar com a nuvem do AAD em alemão:

Objective-C
```objc
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.de/common"];
    MSALAuthority *sovereignAuthority = [MSALAuthority authorityWithURL:authorityURL error:&authorityError];
    
    if (!sovereignAuthority)
    {
        // Handle error
        return;
    }
    
    MSALPublicClientApplicationConfig *applicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:sovereignAuthority];
    
    
    MSALPublicClientApplication *sovereignApplication = [[MSALPublicClientApplication alloc] initWithConfiguration:applicationConfig error:&error];
    
    
    if (!sovereignApplication)
    {
        // Handle error
        return;
    }
```
Swift
```swift
do{
    guard let authorityURL = URL(string: "https://login.microsoftonline.de/common") else {
        //Handle error
        return
    }
    let sovereignAuthority = try MSALAuthority(url: authorityURL)
            
    let applicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: sovereignAuthority)
            
    let sovereignApplication = try MSALPublicClientApplication(configuration: applicationConfig)
} catch {
    // Handle error
}
```

Talvez seja necessário passar escopos diferentes para cada nuvem soberanas. Quais escopos enviar depende do recurso que você está usando. Por exemplo, você pode usar `"https://graph.microsoft.com/user.read"` na nuvem mundial e `"https://graph.microsoft.de/user.read"` na nuvem em alemão.

### <a name="signing-a-user-into-a-specific-tenant"></a>Assinando um usuário em um locatário específico

Quando a URL da autoridade for definida como `"login.microsoftonline.com/common"` , o usuário será conectado ao seu locatário inicial. No entanto, alguns aplicativos podem precisar conectar o usuário em um locatário diferente e alguns aplicativos só funcionam com um único locatário.

Para conectar o usuário a um locatário específico, configure `MSALPublicClientApplication` com uma autoridade específica. Por exemplo:

`https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4`

O seguinte mostra como assinar um usuário em um locatário específico:

Objective-C
```objc
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4"];
    MSALAADAuthority *tenantedAuthority = [[MSALAADAuthority alloc] initWithURL:authorityURL error:&authorityError];
    
    if (!tenantedAuthority)
    {
        // Handle error
        return;
    }
    
    MSALPublicClientApplicationConfig *applicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:tenantedAuthority];
    
    MSALPublicClientApplication *application =
    [[MSALPublicClientApplication alloc] initWithConfiguration:applicationConfig error:&error];
    
    if (!application)
    {
        // Handle error
        return;
    }
```
Swift
```swift
do{
    guard let authorityURL = URL(string: "https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4") else {
        //Handle error
        return
    }    
    let tenantedAuthority = try MSALAADAuthority(url: authorityURL)
            
    let applicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: tenantedAuthority)
            
    let application = try MSALPublicClientApplication(configuration: applicationConfig)
} catch {
    // Handle error
}
```

## <a name="supported-authorities"></a>Autoridades com suporte

### <a name="msalauthority"></a>MSALAuthority

A `MSALAuthority` classe é a classe abstrata base para as classes de autoridade MSAL. Não tente criar uma instância dela usando `alloc` ou `new` . Em vez disso, crie uma de suas subclasses diretamente ( `MSALAADAuthority` , `MSALB2CAuthority` ) ou use o método de fábrica `authorityWithURL:error:` para criar subclasses usando uma URL de autoridade.

Use a `url` propriedade para obter uma URL de autoridade normalizada. Os parâmetros extras e componentes de caminho ou fragmentos que não fazem parte da autoridade não estarão na URL de autoridade normalizada retornada.

Veja a seguir as subclasses de `MSALAuthority` que você pode criar uma instância dependendo da autoridade que deseja usar.

### <a name="msalaadauthority"></a>MSALAADAuthority

`MSALAADAuthority` representa uma autoridade do AAD. A URL da autoridade deve estar no seguinte formato, em que `<port>` é opcional: `https://<host>:<port>/<tenant>`

### <a name="msalb2cauthority"></a>MSALB2CAuthority

`MSALB2CAuthority` representa uma autoridade B2C. Por padrão, a URL da autoridade B2C deve estar no seguinte formato, em que `<port>` é opcional: `https://<host>:<port>/tfp/<tenant>/<policy>` . No entanto, o MSAL também dá suporte a outros formatos de autoridade B2C arbitrárias.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os [Fluxos de autenticação e cenários de aplicativos](authentication-flows-app-scenarios.md)