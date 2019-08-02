---
title: Migrando para a MSAL.NET | Azure
description: Saiba mais sobre as diferenças entre a Biblioteca de Autenticação da Microsoft para .NET (MSAL.NET) e a Biblioteca de autenticação do Microsoft Azure AD para .NET (ADAL.NET) e como migrar para a MSAL.NET.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3ea45056b0112769105ddd997ce1abc79f59679f
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663337"
---
# <a name="migrating-applications-to-msalnet"></a>Migrando aplicativos para a MSAL.NET

Tanto a Biblioteca de Autenticação da Microsoft para .NET (MSAL.NET) e a Biblioteca de Autenticação do Active Directory para .NET (ADAL.NET) são usadas para autenticar as entidades do Azure AD e solicitar tokens a partir do Azure AD. Até agora, a maioria dos desenvolvedores trabalharam com o Azure AD para a plataforma de desenvolvedores (v1.0) para autenticar identidades do Azure AD (contas corporativas e de estudante), solicitando tokens usando a biblioteca de autenticação do Azure AD (ADAL). Agora, usando o MSAL.NET, você pode autenticar um conjunto mais amplo de identidades da Microsoft (identidades do Azure AD e contas da Microsoft e contas sociais e locais por meio do Azure AD B2C) por meio do ponto de extremidade da plataforma de identidade da Microsoft. 

Este artigo descreve como escolher entre a Biblioteca de Autenticação da Microsoft para .NET (MSAL.NET) e a Biblioteca de Autenticação do Microsoft Azure AD para .NET (ADAL.NET) e comparar as duas bibliotecas.  

## <a name="differences-between-adal-and-msal-apps"></a>Diferenças entre aplicativos ADAL e MSAL
Na maioria dos casos, convém usar o ponto de extremidade da plataforma MSAL.NET e da Microsoft, que é a geração mais recente das bibliotecas de autenticação da Microsoft. Ao usar a MSAL.NET, você adquire tokens para que os usuários entrem em seu aplicativo com o Azure AD (contas corporativas e de estudante), contas da Microsoft (pessoais) (MSA) ou Azure AD B2C. 

Se já estiver familiarizado com o Azure AD para o ponto de extremidade de desenvolvedores (v1.0) (e ADAL.NET), você talvez queira ler [Qual é a diferença sobre o ponto de extremidade da plataforma (v2.0) da identidade da Microsoft?](active-directory-v2-compare.md).

No entanto, você ainda precisará usar a ADAL.NET se seu aplicativo precisar conectar os usuários com versões anteriores dos [Serviços de Federação do Active Directory (AD FS)](/windows-server/identity/active-directory-federation-services). Para obter mais detalhes, consulte o [suporte ADFS](https://aka.ms/msal-net-adfs-support).

A figura a seguir resume algumas das diferenças entre ADAL.NET e MSAL.NET ![Código lado a lado](./media/msal-compare-msaldotnet-and-adaldotnet/differences.png)

### <a name="nuget-packages-and-namespaces"></a>Pacotes NuGet e Namespaces

A ADAL.NET é consumida a partir do [pacote NuGet Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory). o namespace a ser usado é `Microsoft.IdentityModel.Clients.ActiveDirectory`.

Para usar a MSAL.NET, você precisará adicionar o pacote NuGet do [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) e usar o `Microsoft.Identity.Client` namespace

### <a name="scopes-not-resources"></a>Escopos, não recursos

A ADAL.NET adquire tokens para *recursos*, mas MSAL.NET adquire tokens para *escopos*. Um número de substituições MSAL.NET AcquireToken requer um parâmetro chamado escopos (`IEnumerable<string> scopes`). Esse parâmetro é uma lista simples de cadeias de caracteres que declara as permissões desejadas e os recursos que são solicitados. Os [escopos do Microsoft Graph](/graph/permissions-reference) são escopos conhecidos.

Na MSAL.NET, também é possível acessar recursos da versão 1.0. Confira os detalhes em [Escopos para um aplicativo v1.0](#scopes-for-a-web-api-accepting-v10-tokens). 

### <a name="core-classes"></a>Classes de núcleo

- A ADAL.NET usa [AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AuthenticationContext:-the-connection-to-Azure-AD) como representação de sua conexão para o Serviço de Token de Segurança (STS) ou servidor de autorização, por meio de uma Autoridade. Por outro lado, a MSAL.NET é desenvolvida em torno dos [aplicativos cliente](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications). Ele fornece duas classes separadas: `PublicClientApplication` e `ConfidentialClientApplication`

- Adquirir Tokens: ADAL.NET e MSAL.NET têm as mesmas chamadas de autenticação (`AcquireTokenAsync` e `AcquireTokenSilentAsync` para ADAL.NET, e `AcquireTokenInteractive` e `AcquireTokenSilent` na MSAL.NET), mas com diferentes parâmetros necessários. Uma diferença é o fato de que, na MSAL.NET, você não precisa passar no `ClientID` do seu aplicativo em todas as chamadas AcquireTokenXX. Na verdade, o `ClientID` é definido apenas uma vez ao criar (`IPublicClientApplication` ou `IConfidentialClientApplication`).

### <a name="iaccount-not-iuser"></a>IAccount não IUser

Usuários manipulados da ADAL.NET. No entanto, um usuário é um ser humano ou um agente de software, mas pode ter/possuir/ser responsável por uma ou mais contas do sistema de identidade da Microsoft (várias contas do Azure AD B2C, Azure AD B2C, contas pessoais da Microsoft). 

A MSAL.NET 2.x agora define o conceito de Conta (por meio da interface da IAccount). Essa alteração interruptiva fornece a semântica de direito: o fato de que o mesmo usuário pode ter várias contas em diferentes diretórios do Azure AD. Além disso, a MSAL.NET fornece melhores informações em cenários de convidado, conforme as informações de conta iniciais são fornecidas.

Para obter mais informações sobre as diferenças entre IUser e IAccount, confira [MSAL.NET 2.x](https://aka.ms/msal-net-2-released).

### <a name="exceptions"></a>Exceções

#### <a name="interaction-required-exceptions"></a>Exceções de interação necessária

A MSAL.NET tem exceções mais explícitas. Por exemplo, quando a autenticação sem confirmação falha na ADAL, o procedimento é capturar a exceção e procurar o `user_interaction_required` código de erro:

```csharp
catch(AdalException exception)
{
 if (exception.ErrorCode == “user_interaction_required”)
 {
  try
  {“try to authenticate interactively”}}
 }
}
```

Consulte os detalhes em [Padrão recomendado para adquirir um token](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-a-cached-token#recommended-pattern-to-acquire-a-token) com a ADAL.NET

Usando a MSAL.NET, você captura `MsalUiRequiredException` conforme descrito em [AcquireTokenSilent](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-a-cached-token).

```csharp
catch(MsalUiRequiredException exception)
{
 try {“try to authenticate interactively”}
}
```

#### <a name="handling-claim-challenge-exceptions"></a>Tratamento de exceções do desafio de declaração

Na ADAL.NET, as exceções do desafio de declaração são tratadas da seguinte forma:

- `AdalClaimChallengeException` é uma exceção (que deriva de `AdalServiceException`) gerada pelo serviço no caso de um recurso precisar de mais declarações do usuário (por exemplo autenticação de dois fatores). O `Claims` membro contém alguns fragmento JSON com as declarações que são esperadas.
- Ainda na ADAL.NET, o aplicativo cliente público que recebe essa exceção, precisa chamar a `AcquireTokenInteractive` substituição que tem um parâmetro de declaração. Essa substituição de `AcquireTokenInteractive` nem mesmo tenta atingir o cache conforme não é necessário. O motivo é que o token no cache não tem as declarações corretas (caso contrário, um `AdalClaimChallengeException` não teria sido lançado). Portanto, não é necessário examinar o cache. Observe que o `ClaimChallengeException` pode ser recebido de uma API Web fazendo OBO, enquanto o `AcquireTokenInteractive` precisa ser chamado em um aplicativo cliente público que chama essa API Web.
- Para obter detalhes, incluindo exemplos, consulte Tratar [AdalClaimChallengeException](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Exceptions-in-ADAL.NET#handling-adalclaimchallengeexception)

Na MSAL.NET, as exceções do desafio de declaração são tratadas da seguinte forma:

- O `Claims` são apresentados no `MsalServiceException`.
- Há um `.WithClaim(claims)` método que pode se aplicar ao `AcquireTokenInteractive` construtor. 

### <a name="supported-grants"></a>Concessões com suporte

Nem todas as concessões ainda têm suporte na MSAL.NET e o ponto de extremidade v 2.0. A seguir está um resumo de comparação com suporte do concessões da ADAL.NET e MSAL. NET.

#### <a name="public-client-applications"></a>Aplicativos cliente públicos

Veja as concessões suportadas na ADAL.NET e MSAL.NET para aplicativos desktop e móveis

Concessão | ADAL.NET | MSAL.NET
----- |----- | -----
Interativo | [Autenticação Interativa](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-interactively---Public-client-application-flows) | [Aquisição de tokens interativamente na MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Acquiring-tokens-interactively)
Autenticação Integrada do Windows | [Autenticação integrada no Windows (Kerberos)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-Integrated-authentication-on-Windows-(Kerberos)) | [Autenticação Integrada do Windows](msal-authentication-flows.md#integrated-windows-authentication)
Nome de usuário + senha | [Adquirir tokens com nome de usuário e senha](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-with-username-and-password)| [Autenticação de Senha do Nome de Usuário](msal-authentication-flows.md#usernamepassword)
Fluxo de código do dispositivo | [Perfil de dispositivo para dispositivos sem navegadores web](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Device-profile-for-devices-without-web-browsers) | [Fluxo de código do dispositivo](msal-authentication-flows.md#device-code)

#### <a name="confidential-client-applications"></a>Aplicativos cliente confidenciais

Aqui estão as concessões com suporte no ADAL.NET e MSAL.NET para aplicativos Web, APIs Web e aplicativos daemon:

Tipo de aplicativo | Concessão | ADAL.NET | MSAL.NET
----- | ----- | ----- | -----
Aplicativo Web, API Web, daemon | Credenciais do cliente | [Fluxos de credencial de cliente na ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Client-credential-flows) | [Fluxos de credencial de cliente na MSAL.NET](msal-authentication-flows.md#client-credentials))
API Web | Em nome de | [Serviço para chamadas de serviço em nome do usuário com a ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Service-to-service-calls-on-behalf-of-the-user) | [Em nome da MSAL.NET](msal-authentication-flows.md#on-behalf-of)
Aplicativo Web | Código de Autenticação | [Aquisição de tokens com códigos de autorização em aplicativos web com a ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-with-authorization-codes-on-web-apps) | [Aquisição de tokens com códigos de autorização em aplicativos web com a MSAL.NET](msal-authentication-flows.md#authorization-code)

### <a name="cache-persistence"></a>Persistência de cache

A ADAL.NET permite que você estenda a classe `TokenCache` para implementar a funcionalidade de persistência desejada nas plataformas sem um armazenamento seguro (.NET Framework e .NET core) usando os métodos `BeforeAccess` e `BeforeWrite`. Para obter detalhes, consulte [Serialização de Cache do Token na ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization).

A MSAL.NET torna o cache de token uma classe selada, removendo a capacidade de estendê-la. Portanto, sua implementação de persistência de cache de token deve estar na forma de uma classe auxiliar que interage com o cache de token selado. Essa interação é descrita em [Serialização do Cache de Token em MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization).

## <a name="signification-of-the-common-authority"></a>Significado da autoridade comum

No v1.0, ao usar a autoridade https://login.microsoftonline.com/common , você permitirá que os usuários entrem com qualquer conta do Microsoft Azure Active Directory (para qualquer organização). Consulte [Validação de Autoridade na ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AuthenticationContext:-the-connection-to-Azure-AD#authority-validation)

Ao usar a https://login.microsoftonline.com/common autoridade no v2.0, você permitirá que os usuários entrem com uma organização do Microsoft Azure Active Directory ou com uma conta pessoal da Microsoft. Na MSAL.NET, se você quiser restringir o logon a qualquer conta do Microsoft Azure Active Directory (mesmo comportamento que com ADAL.NET), será necessário usar https://login.microsoftonline.com/organizations. Para obter detalhes, consulte o `authority` parâmetro no [aplicativo cliente público](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications#publicclientapplication).

## <a name="v10-and-v20-tokens"></a>Tokens v1.0 e v2.0

No momento, há duas versões dos tokens:
- Tokens v1.0
- Tokens v2.0 

O ponto de extremidade v1.0 (usado pela ADAL) só emite tokens v1.0.

No entanto, o ponto de extremidade v2.0 (usado pela MSAL) emite a versão do token que aceita a API Web. Uma propriedade de manifesto do aplicativo de API Web permite que os desenvolvedores escolham qual versão do token é aceita. Consulte `accessTokenAcceptedVersion` na [documentação de referência](reference-app-manifest.md) do Manifesto do aplicativo.

Para obter mais informações sobre tokens v1.0 e v2.0, veja [Tokens de acesso do Azure Active Directory](access-tokens.md)

## <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>Escopos para uma API Web que aceita os tokens v1.0

As permissões OAuth2 são escopos de permissão que um aplicativo API Web v1.0 (recurso) expões para os aplicativos cliente. Os escopos de permissões podem ser concedidos a aplicativos cliente durante o consentimento. Confira a seção sobre oauth2Permissions no manifesto do aplicativo [do Azure Active Directory](active-directory-application-manifest.md).

### <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>Os escopos solicitam acesso a permissões específicas de OAuth2 de um aplicativo v1.0

Se você deseja adquirir tokens para escopos específicos de um aplicativo de v1.0 (por exemplo, o grafo do Azure Active Directory que é https://graph.windows.net) , você precisaria criar `scopes` concatenando um identificador de recurso desejado com uma permissão OAuth2 desejado para esse recurso.

Por exemplo, para acessar no nome de usuário uma API Web v1.0 cujo URI de ID do aplicativo é `ResourceId`, você iria querer usar:

```csharp
var scopes = new [] {  ResourceId+"/user_impersonation"};
```

Se você quiser ler e escrever com o Azure Active Directory da MSAL.NET usando a API do grafo do Azure Active Directory (https://graph.windows.net/) , você criaria uma lista de escopos, como no trecho a seguir:

```csharp
ResourceId = "https://graph.windows.net/";
var scopes = new [] { ResourceId + “Directory.Read”, ResourceID + “Directory.Write”}
```

#### <a name="warning-should-you-have-one-or-two-slashes-in-the-scope-corresponding-to-a-v10-web-api"></a>Aviso: Você deve ter uma ou duas barras no escopo correspondente a uma API Web v1.0

Se você quiser escrever o escopo correspondente à API do Azure Resource Manager (https://management.core.windows.net/) , você precisará solicitar o escopo a seguir (observe as duas barras) 

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

Isso ocorre porque a API do Gerenciador de Recursos espera uma barra em sua declaração de público-alvo (`aud`), e, em seguida, há uma barra para separar o nome da API do escopo.

A lógica usada pelo Azure AD é a seguinte:
- Ponto de extremidade da ADAL (v1.0) com um token de acesso v 1.0 (o único possível), aud = recurso
- Para a MSAL (ponto de extremidade v2.0) solicitando um token de acesso para um recurso que aceita tokens v2.0, aud=resource.AppId
- Para MSAL (ponto de extremidade v2.0) solicitando um token de acesso para um recurso que aceita um token de acesso v1.0 (que é o caso acima), o Azure AD analisa a audiência desejada do escopo solicitado, assumindo tudo antes da última barra e usando como identificador de recursos. Portanto, se https:\//database.windows.net espera um público de "https://database.windows.net/ ", você precisará solicitar um escopo de https:\/ /database.windows.net//.default. Consulte também o problema #[747](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747): Uma barra à direita do recurso da url foi omitida, o que causou a falha de autenticação do sql número 747


### <a name="scopes-to-request-access-to-all-the-permissions-of-a-v10-application"></a>Os escopos solicitam acesso a permissões específicas de v1.0

Por exemplo, se você quiser adquirir um token para todos os escopos estáticos de um aplicativo de v1.0, um precisaria usar

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

### <a name="scopes-to-request-in-the-case-of-client-credential-flow--daemon-app"></a>Escopos para solicitar no caso de um fluxo de credencial de cliente / aplicativo daemon

No caso do fluxo de credenciais do cliente, o escopo para passar também seria `/.default`. Isso informa ao Azure Active Directory: "todas as permissões de nível de aplicativo que o administrador consentiu no registro de aplicativo.

## <a name="adal-to-msal-migration"></a>ADAL para a migração MSAL

Na ADAL.NET v2. X, os tokens de atualização foram expostos, permitindo que você desenvolva soluções em torno do uso desses tokens, armazenando-os em cache e usando os `AcquireTokenByRefreshToken` métodos fornecidos pela ADAL 2. x. Algumas dessas soluções foram usadas em cenários como:
* Serviços de execução prolongada que realizam ações, inclusive a atualização de painéis em nome dos usuários, enquanto os usuários não estiverem mais conectados. 
* Cenários de WebFarm para habilitar o cliente para trazer o RT para o serviço web (o cache é feito ao lado do cliente, cookie criptografado e não o do servidor)

Isso não é o caso com a MSAL.NET, no entanto, por motivos de segurança, nós não recomendamos usar tokens de atualização dessa maneira. Isso dificultaria migrar para a MSAL 3.x uma vez que a API não oferece uma maneira de passar tokens de atualização adquiridos anteriormente. 

Felizmente, a MSAL.NET agora tem uma API que permite que você migre seus tokens de atualização anteriores para o `IConfidentialClientApplication` 

```CSharp
/// <summary>
/// Acquires an access token from an existing refresh token and stores it and the refresh token into 
/// the application user token cache, where it will be available for further AcquireTokenSilent calls.
/// This method can be used in migration to MSAL from ADAL v2 and in various integration 
/// scenarios where you have a RefreshToken available. 
/// (see https://aka.ms/msal-net-migration-adal2-msal2)
/// </summary>
/// <param name="scopes">Scope to request from the token endpoint. 
/// Setting this to null or empty will request an access token, refresh token and ID token with default scopes</param>
/// <param name="refreshToken">The refresh token from ADAL 2.x</param>
IByRefreshToken.AcquireTokenByRefreshToken(IEnumerable<string> scopes, string refreshToken);
```
 
Com esse método, você pode fornecer o token de atualização usado anteriormente, juntamente a qualquer escopo (recurso) que você deseja. O token de atualização será trocado por um novo e armazenado em cache em seu aplicativo.  

Uma vez que esse método é destinado para cenários que não são típicos, não estará prontamente acessível com `IConfidentialClientApplication` sem primeiro convertê-lo para `IByRefreshToken`.

Este snippet de código mostra um código de migração em um aplicativo cliente confidencial. `GetCachedRefreshTokenForSignedInUser` recupere o token de atualização que foi armazenado em algum armazenamento por uma versão anterior do aplicativo que é usado para aproveitar a ADAL 2.x. `GetTokenCacheForSignedInUser` desserializa um cache para o usuário conectado (como aplicativos de cliente confidenciais devem ter um cache por usuário).

```csharp
TokenCache userCache = GetTokenCacheForSignedInUser();
string rt = GetCachedRefreshTokenForSignedInUser();

IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.Create(clientId)
 .WithAuthority(Authority)
 .WithRedirectUri(RedirectUri)
 .WithClientSecret(ClientSecret)
 .Build();
IByRefreshToken appRt = app as IByRefreshToken;
         
AuthenticationResult result = await appRt.AcquireTokenByRefreshToken(null, rt)
                                         .ExecuteAsync()
                                         .ConfigureAwait(false);
```

Você verá um token de acesso e token de ID retornado no seu AuthenticationResult enquanto o novo token de atualização é armazenado em cache.

Também é possível usar esse método para vários cenários de integração onde você tem um token de atualização disponível.

## <a name="next-steps"></a>Próximas etapas

Você pode encontrar mais informações sobre os escopos em [Escopos, permissões e consentimento no ponto de extremidade da plataforma de identidade da Microsoft](v2-permissions-and-consent.md)
