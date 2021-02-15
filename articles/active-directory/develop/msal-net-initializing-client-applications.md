---
title: Inicializar aplicativos cliente do MSAL.NET | Azure
titleSuffix: Microsoft identity platform
description: Saiba como inicializar aplicativos cliente públicos e clientes confidenciais usando a MSAL.NET (biblioteca de autenticação da Microsoft para .NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/18/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 6616b0573019703f287d3be521569f5953aeb032
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98600288"
---
# <a name="initialize-client-applications-using-msalnet"></a>Inicializar aplicativos cliente usando o MSAL.NET
Este artigo descreve como inicializar aplicativos cliente públicos e clientes confidenciais usando a MSAL.NET (biblioteca de autenticação da Microsoft para .NET).  Para saber mais sobre os tipos de aplicativo cliente e as opções de configuração de aplicativo, leia a [visão geral](msal-client-applications.md).

Com o MSAL.NET 3. x, a maneira recomendada para instanciar um aplicativo é usando os construtores de aplicativo: `PublicClientApplicationBuilder` e `ConfidentialClientApplicationBuilder` . Eles oferecem um mecanismo poderoso para configurar o aplicativo a partir do código ou de um arquivo de configuração, ou até mesmo misturando as duas abordagens.

## <a name="prerequisites"></a>Pré-requisitos
Antes de inicializar um aplicativo, primeiro você precisa [registrá-lo](quickstart-register-app.md) para que seu aplicativo possa ser integrado à plataforma de identidade da Microsoft.  Após o registro, talvez você precise das seguintes informações (que podem ser encontradas no portal do Azure):

- A ID do cliente (uma cadeia de caracteres que representa um GUID)
- A URL do provedor de identidade (chamada de instância) e o público-alvo de entrada para seu aplicativo. Esses dois parâmetros são coletivamente conhecidos como autoridade.
- A ID do locatário se você estiver escrevendo um aplicativo de linha de negócios somente para sua organização (também chamado de aplicativo de locatário único).
- O segredo do aplicativo (cadeia de caracteres secreta do cliente) ou certificado (do tipo X509Certificate2) se for um aplicativo cliente confidencial.
- Para aplicativos Web e, às vezes, para aplicativos cliente públicos (em particular quando seu aplicativo precisa usar um agente), você também terá definido o redirectUri em que o provedor de identidade entrará em contato com o seu aplicativo com os tokens de segurança.

## <a name="ways-to-initialize-applications"></a>Maneiras de inicializar aplicativos
Há várias maneiras diferentes de instanciar aplicativos cliente.

### <a name="initializing-a-public-client-application-from-code"></a>Inicializando um aplicativo cliente público do código

O código a seguir instancia um aplicativo cliente público, usuários de entrada na nuvem pública Microsoft Azure, com suas contas corporativas e de estudante, ou suas contas pessoais da Microsoft.

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-code"></a>Inicializando um aplicativo cliente confidencial do código

Da mesma forma, o código a seguir instancia um aplicativo confidencial (um aplicativo Web localizado em `https://myapp.azurewebsites.net` ) manipulando tokens de usuários na nuvem pública Microsoft Azure, com suas contas corporativas e de estudante, ou suas contas pessoais da Microsoft. O aplicativo é identificado com o provedor de identidade compartilhando um segredo do cliente:

```csharp
string redirectUri = "https://myapp.azurewebsites.net";
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithClientSecret(clientSecret)
    .WithRedirectUri(redirectUri )
    .Build();
```

Como você deve saber, em produção, em vez de usar um segredo do cliente, talvez você queira compartilhar com o Azure AD um certificado. Em seguida, o código seria o seguinte:

```csharp
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithCertificate(certificate)
    .WithRedirectUri(redirectUri )
    .Build();
```

### <a name="initializing-a-public-client-application-from-configuration-options"></a>Inicializando um aplicativo cliente público por meio de opções de configuração

O código a seguir instancia um aplicativo cliente público a partir de um objeto de configuração, que poderia ser preenchido programaticamente ou lido em um arquivo de configuração:

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-configuration-options"></a>Inicializando um aplicativo cliente confidencial das opções de configuração

O mesmo tipo de padrão se aplica a aplicativos cliente confidenciais. Você também pode adicionar outros parâmetros usando `.WithXXX` modificadores (aqui um certificado).

```csharp
ConfidentialClientApplicationOptions options = GetOptions(); // your own method
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(options)
    .WithCertificate(certificate)
    .Build();
```

## <a name="builder-modifiers"></a>Modificadores de Construtor

Nos trechos de código que usam construtores de aplicativos, vários `.With` métodos podem ser aplicados como modificadores (por exemplo, `.WithCertificate` e `.WithRedirectUri` ). 

### <a name="modifiers-common-to-public-and-confidential-client-applications"></a>Modificadores comuns a aplicativos cliente públicos e confidenciais

Os modificadores que você pode definir em um cliente público ou o construtor de aplicativos cliente confidencial são:

|Modificador | Descrição|
|--------- | --------- |
|`.WithAuthority()` 7 substituições | Define a autoridade padrão do aplicativo como uma autoridade do Azure AD, com a possibilidade de escolher a nuvem do Azure, o público, o locatário (ID do locatário ou nome de domínio) ou fornecer diretamente o URI de autoridade.|
|`.WithAdfsAuthority(string)` | Define a autoridade padrão do aplicativo como uma autoridade do ADFS.|
|`.WithB2CAuthority(string)` | Define a autoridade padrão do aplicativo como uma autoridade de Azure AD B2C.|
|`.WithClientId(string)` | Substitui a ID do cliente.|
|`.WithComponent(string)` | Define o nome da biblioteca usando MSAL.NET (para motivos de telemetria). |
|`.WithDebugLoggingCallback()` | Se chamado, o aplicativo chamará `Debug.Write` simplesmente habilitar rastreamentos de depuração. Consulte [Log](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging) para obter mais informações.|
|`.WithExtraQueryParameters(IDictionary<string,string> eqp)` | Defina os parâmetros de consulta extra em nível de aplicativo que serão enviados em todas as solicitações de autenticação. Isso é substituível em cada nível de método de aquisição de token (com o mesmo `.WithExtraQueryParameters pattern` ).|
|`.WithHttpClientFactory(IMsalHttpClientFactory httpClientFactory)` | Habilita cenários avançados, como a configuração de um proxy HTTP, ou para forçar o MSAL a usar um HttpClient específico (por exemplo, em ASP.NET Core aplicativos Web/APIs).|
|`.WithLogging()` | Se chamado, o aplicativo chamará um retorno de chamada com rastreamentos de depuração. Consulte [Log](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging) para obter mais informações.|
|`.WithRedirectUri(string redirectUri)` | Substitui o URI de redirecionamento padrão. No caso de aplicativos cliente públicos, isso será útil para cenários que envolvem o agente.|
|`.WithTelemetry(TelemetryCallback telemetryCallback)` | Define o delegado usado para enviar telemetria.|
|`.WithTenantId(string tenantId)` | Substitui a ID do locatário ou a descrição do locatário.|

### <a name="modifiers-specific-to-xamarinios-applications"></a>Modificadores específicos para aplicativos Xamarin. iOS

Os modificadores que você pode definir em um construtor de aplicativo cliente público no Xamarin. iOS são:

|Modificador | Descrição|
|--------- | --------- |
|`.WithIosKeychainSecurityGroup()` | **Xamarin. Ios somente**: define o grupo de segurança da cadeia de chaves do IOS (para a persistência do cache).|

### <a name="modifiers-specific-to-confidential-client-applications"></a>Modificadores específicos para aplicativos cliente confidenciais

Os modificadores que você pode definir em um construtor de aplicativos cliente confidencial são:

|Modificador | Descrição|
|--------- | --------- |
|`.WithCertificate(X509Certificate2 certificate)` | Define o certificado que identifica o aplicativo com o Azure AD.|
|`.WithClientSecret(string clientSecret)` | Define o segredo do cliente (senha do aplicativo) que identifica o aplicativo com o Azure AD.|

Esses modificadores são mutuamente exclusivos. Se você fornecer ambos, o MSAL gerará uma exceção significativa.

### <a name="example-of-usage-of-modifiers"></a>Exemplo de uso de modificadores

Vamos supor que seu aplicativo é um aplicativo de linha de negócios, que é apenas para sua organização.  Em seguida, você pode escrever:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAuthority(AzureCloudInstance.AzurePublic, tenantId)
        .Build();
```

Onde se torna interessante que a programação para nuvens nacionais agora é simplificada. Se você quiser que seu aplicativo seja um aplicativo multilocatário em uma nuvem nacional, poderá escrever, por exemplo:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAuthority(AzureCloudInstance.AzureUsGovernment, AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

Há também uma substituição para ADFS (atualmente não há suporte para ADFS 2019):
```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Por fim, se você for um desenvolvedor Azure AD B2C, poderá especificar seu locatário como este:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```

## <a name="next-steps"></a>Próximas etapas

Depois de inicializar o aplicativo cliente, a próxima tarefa é adicionar suporte para entrada do usuário, acesso autorizado à API ou ambos.

A documentação do cenário do aplicativo fornece orientação para entrar em um usuário e adquirir um token de acesso para acessar uma API em nome desse usuário:

- [Aplicativo Web que conecta usuários: entrar e sair](scenario-web-app-sign-user-sign-in.md)
- [Aplicativo Web que chama APIs da Web: adquirir um token](scenario-web-app-call-api-acquire-token.md)
