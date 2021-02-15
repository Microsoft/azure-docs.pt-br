---
title: SSO entre o ADAL & aplicativos MSAL (iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Saiba como compartilhar SSO entre aplicativos ADAL e MSAL
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: 396e9cfeace8791a59dec4a9c9c7203212f57304
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584240"
---
# <a name="how-to-sso-between-adal-and-msal-apps-on-macos-and-ios"></a>Como: SSO entre aplicativos ADAL e MSAL no macOS e iOS

A MSAL (biblioteca de autenticação da Microsoft) para iOS pode compartilhar o estado do SSO com o [Objective-C de Adal](https://github.com/AzureAD/azure-activedirectory-library-for-objc) entre aplicativos. Você pode migrar seus aplicativos para o MSAL em seu próprio ritmo, garantindo que os usuários ainda se beneficiem do SSO entre aplicativos, mesmo com uma combinação de ADAL e aplicativos baseados em MSAL.

Se você estiver procurando orientação para configurar o SSO entre aplicativos usando o SDK do MSAL, consulte [SSO silencioso entre vários aplicativos](single-sign-on-macos-ios.md#silent-sso-between-apps). Este artigo se concentra no SSO entre a ADAL e a MSAL.

As especificidades que implementam o SSO dependem da versão da ADAL que você está usando.

## <a name="adal-27x"></a>ADAL 2.7. x

Esta seção aborda as diferenças de SSO entre MSAL e ADAL 2.7. x

### <a name="cache-format"></a>Formato de cache

O ADAL 2.7. x pode ler o formato de cache MSAL. Você não precisa fazer nada de especial para o SSO entre aplicativos com a versão ADAL 2.7. x. No entanto, lembre-se das diferenças nos identificadores de conta que essas duas bibliotecas oferecem suporte.

### <a name="account-identifier-differences"></a>Diferenças de identificador de conta

MSAL e ADAL usam identificadores de conta diferentes. A ADAL usa o UPN como seu identificador de conta principal. O MSAL usa um identificador de conta não-reproduzido que é baseado em uma ID de objeto e uma ID de locatário para contas do AAD e uma `sub` declaração para outros tipos de contas.

Quando você recebe um `MSALAccount` objeto no resultado MSAL, ele contém um identificador de conta na `identifier` propriedade. O aplicativo deve usar esse identificador para solicitações silenciosas subsequentes.

Além disso `identifier` , `MSALAccount` o objeto contém um identificador de exibição chamado `username` . Isso se traduz `userId` em Adal. `username` Não é considerado um identificador exclusivo e pode ser alterado a qualquer momento, portanto, ele só deve ser usado para cenários de compatibilidade com versões anteriores com a ADAL. O MSAL dá suporte a consultas de cache usando `username` ou `identifier` , em que `identifier` é recomendado consultar por.

A tabela a seguir resume as diferenças de identificador de conta entre ADAL e MSAL:

| Identificador de conta                | MSAL                                                         | ADAL 2.7. x      | ADAL mais antiga (antes da ADAL 2.7. x) |
| --------------------------------- | ------------------------------------------------------------ | --------------- | ------------------------------ |
| identificador de exibição            | `username`                                                   | `userId`        | `userId`                       |
| identificador exclusivo não-reproduzido | `identifier`                                                 | `homeAccountId` | N/D                            |
| Nenhuma ID de conta conhecida               | Consultar todas as contas por meio `allAccounts:` da API `MSALPublicClientApplication` | N/D             | N/D                            |

Essa é a `MSALAccount` interface que fornece esses identificadores:

```objc
@protocol MSALAccount <NSObject>

/*!
 Displayable user identifier. Can be used for UI and backward compatibility with ADAL.
 */
@property (readonly, nullable) NSString *username;

/*!
 Unique identifier for the account.
 Save this for account lookups from cache at a later point.
 */
@property (readonly, nullable) NSString *identifier;

/*!
 Host part of the authority string used for authentication based on the issuer identifier.
 */
@property (readonly, nonnull) NSString *environment;

/*!
 ID token claims for the account.
 Can be used to read additional information about the account, e.g. name
 Will only be returned if there has been an id token issued for the client Id for the account's source tenant.
 */
@property (readonly, nullable) NSDictionary<NSString *, NSString *> *accountClaims;

@end
```

### <a name="sso-from-msal-to-adal"></a>SSO de MSAL para ADAL

Se você tiver um aplicativo MSAL e um aplicativo ADAL, e o usuário entrar primeiro no aplicativo baseado em MSAL, você poderá obter o SSO no aplicativo ADAL salvando o `username` do `MSALAccount` objeto e passando-o para seu aplicativo baseado em Adal como `userId` . A ADAL pode encontrar as informações da conta silenciosamente com a `acquireTokenSilentWithResource:clientId:redirectUri:userId:completionBlock:` API.

### <a name="sso-from-adal-to-msal"></a>SSO da ADAL para MSAL

Se você tiver um aplicativo MSAL e um aplicativo ADAL, e o usuário entrar primeiro no aplicativo baseado em ADAL, você poderá usar identificadores de usuário ADAL para pesquisas de conta no MSAL. Isso também se aplica ao migrar do ADAL para o MSAL.

#### <a name="adals-homeaccountid"></a>HomeAccountId da ADAL

A ADAL 2.7. x retorna o `homeAccountId` no `ADUserInformation` objeto no resultado por essa propriedade:

```objc
/*! Unique AAD account identifier across tenants based on user's home OID/home tenantId. */
@property (readonly) NSString *homeAccountId;
```

`homeAccountId` no ADAL, é equivalente a `identifier` em MSAL. Você pode salvar esse identificador para usar no MSAL para pesquisas de conta com a `accountForIdentifier:error:` API.

#### <a name="adals-userid"></a>Da ADAL `userId`

Se `homeAccountId` o não estiver disponível ou se você tiver apenas o identificador de exibição, poderá usar a Adal `userId` para pesquisar a conta em MSAL.

No MSAL, primeiro procure uma conta por `username` ou `identifier` . Sempre use `identifier` para consultar, se você tiver, e usar apenas `username` como um fallback. Se a conta for encontrada, use a conta nas `acquireTokenSilent` chamadas.

Objective-C:

```objc
NSString *msalIdentifier = @"previously.saved.msal.account.id";
MSALAccount *account = nil;
    
if (msalIdentifier)
{
    // If you have MSAL account id returned either from MSAL as identifier or ADAL as homeAccountId, use it
    account = [application accountForIdentifier:@"my.account.id.here" error:nil];
}
else
{
    // Fallback to ADAL userId for migration
    account = [application accountForUsername:@"adal.user.id" error:nil];
}
    
if (!account)
{
  // Account not found.
  return;
}

MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:account];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

Swift:

```swift
        
let msalIdentifier: String?
var account: MSALAccount
        
do {
  if let msalIdentifier = msalIdentifier {
    account = try application.account(forIdentifier: msalIdentifier)
  }
  else {
    account = try application.account(forUsername: "adal.user.id") 
  }
             
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: account)          
  application.acquireTokenSilent(with: silentParameters) {
    (result: MSALResult?, error: Error?) in
    // handle result
  }  
} catch let error as NSError {
  // handle error or account not found
}
```



APIs de pesquisa de conta com suporte do MSAL:

```objc
/*!
 Returns account for the given account identifier (received from an account object returned in a previous acquireToken call)
 
 @param  error      The error that occurred trying to get the accounts, if any, if you're
                    not interested in the specific error pass in nil.
 */
- (nullable MSALAccount *)accountForIdentifier:(nonnull NSString *)identifier
                                         error:(NSError * _Nullable __autoreleasing * _Nullable)error;
    
/*!
Returns account for for the given username (received from an account object returned in a previous acquireToken call or ADAL)
    
@param  username    The displayable value in UserPrincipleName(UPN) format
@param  error       The error that occurred trying to get the accounts, if any, if you're
                    not interested in the specific error pass in nil.
*/
- (MSALAccount *)accountForUsername:(NSString *)username
                              error:(NSError * __autoreleasing *)error;
```

## <a name="adal-2x-266"></a>ADAL 2. x-2.6.6

Esta seção aborda as diferenças de SSO entre MSAL e ADAL 2. x-2.6.6.

Versões mais antigas do ADAL não oferecem suporte nativo ao formato de cache MSAL. No entanto, para garantir a migração tranqüila do ADAL para o MSAL, o MSAL pode ler o formato de cache ADAL mais antigo sem solicitar as credenciais do usuário novamente.

Como `homeAccountId` o não está disponível nas versões mais antigas do Adal, você precisará pesquisar contas usando `username` :

```objc
/*!
 Returns account for for the given username (received from an account object returned in a previous acquireToken call or ADAL)

 @param  username    The displayable value in UserPrincipleName(UPN) format
 @param  error       The error that occurred trying to get the accounts, if any.  If you're not interested in the specific error pass in nil.
 */
- (MSALAccount *)accountForUsername:(NSString *)username
                              error:(NSError * __autoreleasing *)error;
```

Por exemplo:

Objective-C:


```objc
MSALAccount *account = [application accountForUsername:@"adal.user.id" error:nil];;
MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:account];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

Swift:

```swift
do {
  let account = try application.account(forUsername: "adal.user.id")          
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: account)
  application.acquireTokenSilent(with: silentParameters) { 
    (result: MSALResult?, error: Error?) in
    // handle result
  }   
} catch let error as NSError { 
  // handle error or account not found
}
```



Como alternativa, você pode ler todas as contas, que também lerá as informações da conta da ADAL:

Objective-C:

```objc
NSArray *accounts = [application allAccounts:nil];
    
if ([accounts count] == 0)
{
  // No account found.
  return; 
}
if ([accounts count] > 1)
{
  // You might want to display an account picker to user in actual application
  // For this sample we assume there's only ever one account in cache
  return;
}
    ``
MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:accounts[0]];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

Swift:

```swift
      
do {
  let accounts = try application.allAccounts()
  if accounts.count == 0 {
    // No account found.
    return
  }
  if accounts.count > 1 {
    // You might want to display an account picker to user in actual application
    // For this sample we assume there's only ever one account in cache
    return
  }
  
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: accounts[0])
  application.acquireTokenSilent(with: silentParameters) {
    (result: MSALResult?, error: Error?) in
    // handle result or error  
  }  
} catch let error as NSError { 
  // handle error
}
```



## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os [Fluxos de autenticação e cenários de aplicativos](authentication-flows-app-scenarios.md)
