---
title: Guia de migração do ADAL para MSAL (MSAL4j) | Azure
titleSuffix: Microsoft identity platform
description: Saiba como migrar seu aplicativo Java ADAL (biblioteca de autenticação do Azure Active Directory) para a biblioteca de autenticação da Microsoft (MSAL).
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Java
ms.workload: identity
ms.date: 11/04/2019
ms.author: sagonzal
ms.reviewer: nacanuma, twhitney
ms.custom: aaddev, devx-track-java
ms.openlocfilehash: 620f77655f8281919ba0831a7e53af8cd28bc5f2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99583986"
---
# <a name="adal-to-msal-migration-guide-for-java"></a>Guia de migração do ADAL para o MSAL para Java

Este artigo realça as alterações que você precisa fazer para migrar um aplicativo que usa a ADAL (biblioteca de autenticação Azure Active Directory) para usar a MSAL (biblioteca de autenticação da Microsoft).

Tanto a biblioteca de autenticação da Microsoft para Java (MSAL4J) quanto a biblioteca de autenticação do AD do Azure para Java (ADAL4J) são usadas para autenticar entidades do Azure AD e solicitar tokens do Azure AD. Até agora, a maioria dos desenvolvedores trabalhou com o Azure AD para a plataforma de desenvolvedores (v 1.0) para autenticar identidades do Azure AD (contas corporativas e de estudante) solicitando tokens usando a ADAL (biblioteca de autenticação do Azure AD).

O MSAL oferece os seguintes benefícios:

- Como ele usa a plataforma de identidade mais recente da Microsoft, você pode autenticar um conjunto mais amplo de identidades da Microsoft, como identidades do Azure AD, contas da Microsoft e contas sociais e locais por meio do Azure AD Business to Consumer (B2C).
- Os usuários terão a melhor experiência de logon único.
- Seu aplicativo pode habilitar o consentimento incremental e o suporte ao acesso condicional é mais fácil.

MSAL para Java é a biblioteca de autenticação que recomendamos que você use com a plataforma de identidade da Microsoft. Nenhum recurso novo será implementado em ADAL4J. Todos os esforços que avançam estão concentrados em melhorar o MSAL.

## <a name="differences"></a>Diferenças

Se você esteve trabalhando com o ponto de extremidade do Azure AD para desenvolvedores (v 1.0) (e ADAL4J), talvez queira ler [o que há de diferente na plataforma de identidade da Microsoft?](../azuread-dev/azure-ad-endpoint-comparison.md).

## <a name="scopes-not-resources"></a>Escopos não recursos

O ADAL4J adquire tokens para recursos, enquanto MSAL para Java adquire tokens para escopos. Um número de MSAL para classes Java requer um parâmetro de escopos. Esse parâmetro é uma lista de cadeias de caracteres que declaram as permissões e os recursos desejados que são solicitados. Consulte [escopos de Microsoft Graph](/graph/permissions-reference) para ver os escopos de exemplo.

Você pode adicionar o `/.default` sufixo de escopo ao recurso para ajudar a migrar seus aplicativos do Adal para o MSAL. Por exemplo, para o valor do recurso de `https://graph.microsoft.com` , o valor de escopo equivalente é `https://graph.microsoft.com/.default` .  Se o recurso não estiver no formato de URL, mas uma ID de recurso do formulário `XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXX` , você ainda poderá usar o valor de escopo como `XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXX/.default` .

Para obter mais detalhes sobre os diferentes tipos de escopos, consulte [permissões e consentimento na plataforma de identidade da Microsoft](./v2-permissions-and-consent.md) e os [escopos para uma API Web aceitando os artigos de tokens v 1.0](./msal-v1-app-scopes.md) .

## <a name="core-classes"></a>Classes de núcleo

No ADAL4J, a `AuthenticationContext` classe representa sua conexão com o serviço de token de segurança (STS) ou servidor de autorização por meio de uma autoridade. No entanto, o MSAL para Java foi projetado em relação aos aplicativos cliente. Ele fornece duas classes separadas: `PublicClientApplication` e `ConfidentialClientApplication` para representar aplicativos cliente.  O último, `ConfidentialClientApplication` , representa um aplicativo projetado para manter um segredo com segurança, como um identificador de aplicativo para um aplicativo daemon.

A tabela a seguir mostra como o ADAL4J Functions é mapeado para o novo MSAL para funções Java:

| Método ADAL4J| Método MSAL4J|
|------|-------|
|acquireToken (recurso de cadeia de caracteres, credencial ClientCredential, retorno de chamada AuthenticationCallback) | acquireToken (ClientCredentialparameters)|
|acquireToken (recurso de cadeia de caracteres, declaração de ClientAssertion, retorno de chamada AuthenticationCallback)|acquireToken (ClientCredentialparameters)|
|acquireToken (recurso de cadeia de caracteres, credencial de AsymmetricKeyCredential, AuthenticationCallback de retorno de chamada)|acquireToken (ClientCredentialparameters)|
|acquireToken (recurso de cadeia de caracteres, Cadeia de caracteres clientId, Cadeia de caracteres de nome de usuário, senha de cadeia de caracteres AuthenticationCallback| acquireToken (UsernamePasswordParameters)|
|acquireToken (String Resource, String clientId, String username, String password = NULL, AuthenticationCallback|acquireToken (IntegratedWindowsAuthenticationParameters)|
|acquireToken (recurso de cadeia de caracteres, userasserção userassertionion, credenciais ClientCredential, retorno de chamada AuthenticationCallback)| acquireToken (OnBehalfOfParameters)|
|acquireTokenByAuthorizationCode () | acquireToken (AuthorizationCodeParameters) |
| acquireDeviceCode () e acquireTokenByDeviceCode ()| acquireToken (DeviceCodeParameters)|
|acquireTokenByRefreshToken()| acquireTokenSilently (Silenciosaparameters)|

## <a name="iaccount-instead-of-iuser"></a>IAccount em vez de IUser

ADAL4J os usuários manipulados. Embora um usuário represente um único agente humano ou de software, ele pode ter uma ou mais contas no sistema de identidade da Microsoft. Por exemplo, um usuário pode ter várias contas pessoais do Azure AD, Azure AD B2C ou Microsoft.

MSAL para Java define o conceito de conta por meio da `IAccount` interface. Essa é uma alteração significativa do ADAL4J, mas é uma boa porque captura o fato de que o mesmo usuário pode ter várias contas, e talvez até mesmo em diretórios diferentes do Azure AD. O MSAL para Java fornece mais informações em cenários de convidado, pois as informações da conta residencial são fornecidas.

## <a name="cache-persistence"></a>Persistência de cache

ADAL4J não tinha suporte para cache de token.
O MSAL para Java adiciona um [cache de token](msal-acquire-cache-tokens.md) para simplificar o gerenciamento de tempos de vida de token atualizando automaticamente os tokens expirados quando possível e impedindo solicitações desnecessárias para que o usuário forneça credenciais quando possível.

## <a name="common-authority"></a>Autoridade comum

Em v 1.0, se você usar a `https://login.microsoftonline.com/common` autoridade, os usuários poderão entrar com qualquer conta do Azure Active Directory (AAD) (para qualquer organização).

Se você usar a `https://login.microsoftonline.com/common` autoridade na v 2.0, os usuários poderão entrar com qualquer organização do AAD ou até mesmo uma MSA (conta pessoal da Microsoft). No MSAL para Java, se você quiser restringir o logon a qualquer conta do AAD, use a `https://login.microsoftonline.com/organizations` autoridade (que é o mesmo comportamento do ADAL4J). Para especificar uma autoridade, defina o `authority` parâmetro no método [PublicClientApplication. Builder](https://javadoc.io/doc/com.microsoft.azure/msal4j/1.0.0/com/microsoft/aad/msal4j/PublicClientApplication.Builder.html) ao criar sua `PublicClientApplication` classe.

## <a name="v10-and-v20-tokens"></a>Tokens v1.0 e v2.0

O ponto de extremidade v1.0 (usado pela ADAL) só emite tokens v1.0.

O ponto de extremidade v 2.0 (usado por MSAL) pode emitir tokens v 1.0 e v 2.0. Uma propriedade do manifesto do aplicativo da API Web permite que os desenvolvedores escolham qual versão do token é aceita. Consulte `accessTokenAcceptedVersion` na documentação de referência do [manifesto do aplicativo](./reference-app-manifest.md) .

Para obter mais informações sobre tokens v 1.0 e v 2.0, consulte [Azure Active Directory tokens de acesso](./access-tokens.md).

## <a name="adal-to-msal-migration"></a>Migração de ADAL para MSAL

No ADAL4J, os tokens de atualização foram expostos, que permitiam aos desenvolvedores armazená-los em cache. Em seguida, eles seriam usados `AcquireTokenByRefreshToken()` para habilitar soluções como a implementação de serviços de longa execução que atualizam painéis em nome do usuário quando o usuário não está mais conectado.

O MSAL para Java não expõe tokens de atualização por motivos de segurança. Em vez disso, o MSAL lida com a atualização de tokens para você.

O MSAL para Java tem uma API que permite migrar tokens de atualização adquiridos com ADAL4j para ClientApplication: [acquireToken (RefreshTokenParameters)](https://javadoc.io/static/com.microsoft.azure/msal4j/1.0.0/com/microsoft/aad/msal4j/PublicClientApplication.html#acquireToken-com.microsoft.aad.msal4j.RefreshTokenParameters-). Com esse método, é possível fornecer o token de atualização usado anteriormente junto com todos os escopos (recursos) desejados. O token de atualização será trocado por um novo e armazenado em cache para ser usado pelo seu aplicativo.

O trecho de código a seguir mostra um código de migração em um aplicativo cliente confidencial:

```java
String rt = GetCachedRefreshTokenForSignedInUser(); // Get refresh token from where you have them stored
Set<String> scopes = Collections.singleton("SCOPE_FOR_REFRESH_TOKEN");

RefreshTokenParameters parameters = RefreshTokenParameters.builder(scopes, rt).build();

PublicClientApplication app = PublicClientApplication.builder(CLIENT_ID) // ClientId for your application
                .authority(AUTHORITY)  //plug in your authority
                .build();

IAuthenticationResult result = app.acquireToken(parameters);
```

O `IAuthenticationResult` retorna um token de acesso e um token de ID, enquanto o novo token de atualização é armazenado no cache.
O aplicativo agora também conterá um IAccount:

```java
Set<IAccount> accounts =  app.getAccounts().join();
```

Para usar os tokens que estão agora no cache, chame:

```java
SilentParameters parameters = SilentParameters.builder(scope, accounts.iterator().next()).build();
IAuthenticationResult result = app.acquireToken(parameters);
```
