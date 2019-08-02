---
title: Fluxos de autenticação (biblioteca de autenticação da Microsoft) | Azure
description: Saiba mais sobre os fluxos de autenticação e concede usado pela biblioteca de autenticação (MSAL) da Microsoft.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/25/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7ba6ae188c098e85573503a1518ba65480d713a
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807204"
---
# <a name="authentication-flows"></a>Fluxos de autenticação

Este artigo descreve os fluxos de autenticação diferentes fornecidos pela biblioteca de autenticação da Microsoft (MSAL).  Esses fluxos podem ser usados em uma variedade de cenários de aplicativo diferente.

| Flow | DESCRIÇÃO | Usado em|  
| ---- | ----------- | ------- | 
| [Interativo](#interactive) | Obtém o token por meio de um processo interativo que solicita ao usuário credenciais por meio de um navegador ou a janela pop-up. | [Aplicativos da área de trabalho](scenario-desktop-overview.md), [aplicativos móveis](scenario-mobile-overview.md) |
| [Concessão implícita](#implicit-grant) | Permite que o aplicativo obter tokens sem executar uma troca de credenciais do servidor back-end. Isso permite que o aplicativo para a entrada do usuário, mantenha a sessão e obter tokens para outra APIs web, tudo dentro do cliente código JavaScript.| [Aplicativos de página única (SPA)](scenario-spa-overview.md) |
| [Código de autorização](#authorization-code) | Usado em aplicativos que são instalados em um dispositivo para obter acesso a recursos protegidos, como APIs da web. Isso permite que você adicione a entrada e acesso à API aos seus aplicativos móveis e de desktop. | [Aplicativos da área de trabalho](scenario-desktop-overview.md), [aplicativos móveis](scenario-mobile-overview.md), [aplicativos web](scenario-web-app-call-api-overview.md) | 
| [On-behalf-of](#on-behalf-of) | Um aplicativo chama um serviço ou API da web, que por sua vez precisa chamar outro serviço ou API da web. A ideia é propagar as permissões e identidade de usuário delegado por meio da cadeia de solicitações. | [APIs da Web](scenario-web-api-call-api-overview.md) |
| [Credenciais do cliente](#client-credentials) | Permite que você acesse recursos hospedados na web usando a identidade de um aplicativo. Normalmente usado para interações de servidor para servidor que devem ser executado em segundo plano, sem interação imediata com um usuário. | [Aplicativos daemon](scenario-daemon-overview.md) |
| [Código de dispositivo](#device-code) | Permite que os usuários entrarem dispositivos restritos a entrada como uma smart TV, dispositivo IoT ou impressora. | [Aplicativos de área de trabalho/móvel](scenario-desktop-acquire-token.md#command-line-tool-without-web-browser) |
| [Autenticação Integrada do Windows](scenario-desktop-acquire-token.md#integrated-windows-authentication) | Permite que os computadores de aplicativos no Azure Active Directory (Azure AD) ou de domínio associados adquirir um token silenciosamente (sem nenhuma interação da interface do usuário do usuário).| [Aplicativos de área de trabalho/móvel](scenario-desktop-acquire-token.md#integrated-windows-authentication) |
| [Nome de usuário/senha](scenario-desktop-acquire-token.md#username--password) | Permite que um aplicativo para a entrada do usuário manipulando diretamente sua senha. Este fluxo não é recomendado. | [Aplicativos de área de trabalho/móvel](scenario-desktop-acquire-token.md#username--password) | 

## <a name="interactive"></a>Interativo
A MSAL oferece suporte a capacidade de solicitar interativamente ao usuário suas credenciais para entrar e obter um token usando essas credenciais.

![Diagrama de fluxo interativo](media/msal-authentication-flows/interactive.png)

Para obter mais informações sobre como usar MSAL.NET interativamente adquirir tokens em plataformas específicas, consulte:
- [Xamarin Android](msal-net-xamarin-android-considerations.md)
- [Xamarin iOS](msal-net-xamarin-ios-considerations.md)
- [Plataforma Universal do Windows](msal-net-uwp-considerations.md)

Para obter mais informações sobre chamadas interativas no msal, consulte [solicitar o comportamento em solicitações interativas de msal](msal-js-prompt-behavior.md).

## <a name="implicit-grant"></a>Concessão implícita

A MSAL dá suporte a [fluxo de concessão implícita do OAuth 2](v2-oauth2-implicit-grant-flow.md), que permite que o aplicativo obter tokens de plataforma de identidade da Microsoft sem executar um servidor de back-end exchange de credencial. Isso permite que o aplicativo para a entrada do usuário, mantenha a sessão e obter tokens para outra APIs web, tudo dentro do cliente código JavaScript.

![Diagrama de fluxo de concessão implícita](media/msal-authentication-flows/implicit-grant.svg)

Muitos aplicativos web modernos são criados como aplicativos do lado do cliente, uma única página, escritos usando JavaScript ou uma estrutura SPA como Angular, VUE e React. js. Esses aplicativos executados em um navegador da web e têm características de autenticação diferente de aplicativos web tradicionais do lado do servidor. A plataforma de identidade da Microsoft permite que os aplicativos de página única conectar usuários e obter tokens para acessar serviços de back-end ou APIs web, usando o fluxo de concessão implícita. O fluxo implícito permite que o aplicativo obter tokens de ID para representar o usuário autenticado e também o necessário para chamar as APIs protegidas de tokens de acesso.

Esse fluxo de autenticação não inclui os cenários de aplicativos que usam estruturas de JavaScript de plataforma cruzada como Electron e React-Native, porque eles exigem mais recursos para interação com plataformas nativas.

## <a name="authorization-code"></a>Código de autorização
A MSAL dá suporte a [concessão de código de autorização do OAuth 2](v2-oauth2-auth-code-flow.md). Essa concessão pode ser usado em aplicativos que são instalados em um dispositivo para obter acesso a recursos protegidos, como APIs da web. Isso permite que você adicione a entrada e acesso à API aos seus aplicativos móveis e de desktop. 

Quando os usuários entram aplicativos da web (sites), o aplicativo web recebe um código de autorização.  O código de autorização é resgatado para adquirir um token para chamar APIs da web. Em aplicativos de web ASP.NET e ASP.NET Core, o único objetivo de `AcquireTokenByAuthorizationCode` é adicionar um token ao cache de token. O token, em seguida, pode ser usado pelo aplicativo (normalmente em controladores, que apenas obter um token para uma API usando `AcquireTokenSilent`).

![Diagrama de fluxo de código de autorização](media/msal-authentication-flows/authorization-code.png)

No diagrama anterior, o aplicativo:

1. Solicita um código de autorização, que é resgatado para um token de acesso.
2. Usa o token de acesso para chamar uma API da web.

### <a name="considerations"></a>Considerações
- Você pode usar o código de autorização apenas uma vez para resgatar um token. Não tente adquirir um token várias vezes com o mesmo código de autorização (ele é explicitamente proibido pela especificação do padrão de protocolo). Se você resgatar o código várias vezes intencionalmente ou porque você não estiver ciente de que uma estrutura também faz isso para você, você obterá o seguinte erro: `AADSTS70002: Error validating credentials. AADSTS54005: OAuth2 Authorization code was already redeemed, please retry with a new valid code or use an existing refresh token.`

- Se você estiver escrevendo um aplicativo ASP.NET ou ASP.NET Core, isso pode acontecer que se você não informam à estrutura que você já resgatou o código de autorização. Para isso, você precisará chamar o `context.HandleCodeRedemption()` método da `AuthorizationCodeReceived` manipulador de eventos.

- Evite compartilhar o token de acesso com o ASP.NET, que pode impedir que o consentimento incremental ocorrendo corretamente. Para obter mais informações, consulte [emitir 693 #](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/693).

## <a name="on-behalf-of"></a>On-behalf-of

A MSAL dá suporte a [fluxo de autenticação em nome do OAuth 2](v2-oauth2-on-behalf-of-flow.md).  Esse fluxo é usado quando um aplicativo chama um serviço ou API da web, que por sua vez precisa chamar outro serviço ou API da web. A ideia é propagar as permissões e identidade de usuário delegado por meio da cadeia de solicitações. Para o serviço de camada intermediária fazer solicitações autenticadas para o serviço downstream, ele precisa proteger um token de acesso da plataforma Microsoft identity, em nome do usuário.

![Diagrama de fluxo on-behalf-of](media/msal-authentication-flows/on-behalf-of.png)

No diagrama anterior:

1. O aplicativo adquire um token de acesso para a API da web.
2. Um cliente (web, aplicativos de área de trabalho, móveis ou de página única) chama uma API, adicionando o token de acesso como um token de portador no cabeçalho de autenticação da solicitação HTTP da web protegida. A API da web autentica o usuário.
3. Quando o cliente chama a API da web, a API da web solicita outro token em nome de usuário.  
4. A API web protegida usa esse token para chamar uma API da web downstream em nome de usuário.  A API da web também pode solicitar tokens posteriormente para outras APIs de downstream (mas ainda em nome do usuário mesmo).

## <a name="client-credentials"></a>Credenciais do cliente

A MSAL dá suporte a [fluxo de credenciais de cliente OAuth 2](v2-oauth2-client-creds-grant-flow.md). Esse fluxo permite que você acesse recursos hospedados na web usando a identidade de um aplicativo. Esse tipo de concessão normalmente é usado para interações de servidor para servidor que devem ser executadas em segundo plano, sem interação imediata com um usuário. Esses tipos de aplicativos são geralmente denominados daemons ou contas de serviço. 

As credenciais do cliente conceder permite que o fluxo de um serviço web (um cliente confidencial) para usar suas próprias credenciais, em vez de representar um usuário para autenticação quando chama outro serviço web. Nesse cenário, o cliente é geralmente um serviço web de camada intermediária, um serviço daemon ou um site. Para um nível mais alto de garantia, a plataforma de identidade da Microsoft também permite que o serviço de chamada use um certificado (em vez de um segredo compartilhado) como uma credencial.

> [!NOTE]
> O fluxo de cliente confidencial não está disponível nas plataformas móveis (UWP, xamarin. IOS e xamarin. Android), porque elas só oferecem suporte a aplicativos de cliente público. Aplicativos de cliente público não sabem como provar a identidade do aplicativo para o provedor de identidade. Uma conexão segura pode ser obtido no aplicativo web ou termina de volta a API web ao implantar um certificado.

MSAL.NET dá suporte a dois tipos de credenciais do cliente. Essas credenciais de cliente precisam ser registrados com o Azure AD. As credenciais são passadas em para os construtores do aplicativo cliente confidencial em seu código.

### <a name="application-secrets"></a>Segredos do aplicativo 

![Diagrama de cliente confidencial com senha](media/msal-authentication-flows/confidential-client-password.png)

No diagrama anterior, o aplicativo:

1. Adquire um token usando credenciais de senha ou segredo do aplicativo.
2. Usa o token para fazer solicitações de recurso.

### <a name="certificates"></a>Certificados 

![Diagrama de cliente confidencial com certificado](media/msal-authentication-flows/confidential-client-certificate.png)

No diagrama anterior, o aplicativo:

1. Adquire um token usando as credenciais de certificado.
2. Usa o token para fazer solicitações de recurso.

Essas credenciais de cliente precisam ser:
- Registrado com o Azure AD.
- Passado na construção do aplicativo cliente confidencial em seu código.


## <a name="device-code"></a>Código de dispositivo
A MSAL dá suporte a [OAuth 2 fluxo de código de dispositivo](v2-oauth2-device-code.md), que permite aos usuários entrar em dispositivos restritos de entrada, como uma smart TV, dispositivo IoT ou impressora. A autenticação interativa com o Azure AD requer um navegador da web. O fluxo de código de dispositivo permite que o usuário use outro dispositivo (por exemplo, outro computador ou um telefone celular) para entrar no modo interativo, em que o dispositivo ou sistema operacional não fornece um navegador da web.

Usando o fluxo de código do dispositivo, o aplicativo obtém tokens por meio de um processo de duas etapas, projetado especialmente para esses sistemas operacionais ou dispositivos. Exemplos de tais aplicativos incluem os que são executados em dispositivos de IoT ou ferramentas de linha de comando (CLI). 

![Diagrama de fluxo de código de dispositivo](media/msal-authentication-flows/device-code.png)

No diagrama anterior:

1. Sempre que a autenticação do usuário é necessária, o aplicativo fornece um código e pede ao usuário para usar outro dispositivo (como um smartphone conectado à internet) para ir para uma URL (por exemplo, https://microsoft.com/devicelogin). O usuário é solicitado a inserir o código e continua por meio de uma experiência de autenticação normal, incluindo solicitações de consentimento e a autenticação multifator se necessário.

2. Após a autenticação bem-sucedida, o aplicativo de linha de comando recebe os símbolos necessários por meio de um canal de retorno e os utiliza para executar as chamadas à API da web que precisa.

### <a name="constraints"></a>Restrições

- Fluxo de código do dispositivo só está disponível em aplicativos de cliente público.
- A autoridade passada ao construir o aplicativo cliente público deve ser um dos seguintes:
  - Com locatários (no formato `https://login.microsoftonline.com/{tenant}/` onde `{tenant}` é o GUID que representa a ID de locatário ou em um domínio associado ao Locatário).
  - para qualquer trabalho e contas corporativas (`https://login.microsoftonline.com/organizations/`).
- Contas pessoais da Microsoft ainda não são suportadas pelo ponto de extremidade v2.0 do Azure AD (não é possível usar o `/common` ou `/consumers` locatários).

## <a name="integrated-windows-authentication"></a>Autenticação Integrada do Windows
A MSAL dá suporte à autenticação integrada do Windows (IWA) para área de trabalho ou aplicativos móveis que são executados em um Azure AD ou ingressado no domínio ingressado no computador do Windows. Usando a IWA, esses aplicativos podem adquirir um token silenciosamente (sem nenhuma interação da interface do usuário do usuário). 

![Diagrama de autenticação integrada do Windows](media/msal-authentication-flows/integrated-windows-authentication.png)

No diagrama anterior, o aplicativo:

1. Adquire um token usando a autenticação integrada do Windows.
2. Usa o token para fazer solicitações de recurso.

### <a name="constraints"></a>Restrições

IWA dá suporte a usuários federados, que significa que os usuários criados no Active Directory e tem o suporte do Azure AD. Usuários criados diretamente no Azure AD, sem o Active Directory fazendo (gerenciado) não é possível usar esse fluxo de autenticação. Essa limitação não afeta a [fluxo do nome de usuário/senha](#usernamepassword).

IWA é para aplicativos escritos para plataformas do .NET Framework, .NET Core e a plataforma Universal do Windows.

IWA não ignora a autenticação multifator. Se a autenticação multifator é configurada, IWA poderá falhar se um desafio de autenticação multifator é necessário. A autenticação multifator requer interação do usuário.

Você não controla quando o provedor de identidade solicita a autenticação de dois fatores a serem executadas. O administrador do locatário faz. Normalmente, a autenticação de dois fatores é necessária quando você entrar em um país diferente, quando você não estiver conectado por meio de VPN a uma rede corporativa e, às vezes, até mesmo quando estão conectados por meio de VPN. Azure AD usa inteligência Artificial para aprender continuamente se a autenticação de dois fatores é necessária. Se IWA falhar, você deve voltar a [prompt interativo do usuário] (#interactive).

A autoridade passada ao construir o aplicativo cliente público deve ser um dos seguintes:
- Com locatários (no formato `https://login.microsoftonline.com/{tenant}/` onde `tenant` é o guid que representa a ID de locatário ou em um domínio associado ao Locatário).
- para qualquer trabalho e contas corporativas (`https://login.microsoftonline.com/organizations/`). Não há suporte para contas pessoais da Microsoft (não é possível usar `/common` ou `/consumers` locatários).

Como um fluxo silencioso IWA, um dos procedimentos a seguir deve ser verdadeiro:
- o usuário do seu aplicativo deve ter consentido anteriormente para usar o aplicativo. 
- O administrador de locatário deve ter consentimento anteriormente a todos os usuários no locatário para usar o aplicativo.

Isso significa que uma das seguintes opções for verdadeira:
- Como desenvolvedor selecionado **Grant** no portal do Azure por conta própria.
- Um administrador de locatários tiver selecionado **conceder/revogar o consentimento do administrador de domínio do locatário {}** na **permissões de API** guia do registro para o aplicativo (consulte [adicionar permissões para acessar APIs web ](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)).
- Você forneceu uma maneira para os usuários dar consentimento ao aplicativo (consulte [solicitar o consentimento do usuário individual](v2-permissions-and-consent.md#requesting-individual-user-consent)).
- Você forneceu uma maneira para que o administrador de locatários dar consentimento para o aplicativo (consulte [consentimento do administrador](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)).

O fluxo IWA está habilitado para a área de trabalho do .NET, .NET Core e aplicativos da plataforma Universal do Windows. No .NET Core, somente a sobrecarga de colocar o nome de usuário está disponível. A plataforma .NET Core não pode solicitar o nome de usuário para o sistema operacional.
  
Para obter mais informações sobre o consentimento, consulte [v 2.0 permissões e consentimento](v2-permissions-and-consent.md).

## <a name="usernamepassword"></a>Nome de usuário/senha 
A MSAL dá suporte a [concessão de credenciais de senha de proprietário de recurso OAuth 2](v2-oauth-ropc.md), que permite que um aplicativo para a entrada do usuário manipulando diretamente sua senha. Em seu aplicativo da área de trabalho, você pode usar o fluxo de nome de usuário e senha para adquirir um token silenciosamente. Nenhuma interface do usuário é necessária ao usar o aplicativo.

![Diagrama do fluxo de nome de usuário/senha](media/msal-authentication-flows/username-password.png)

No diagrama anterior, o aplicativo:

1. Adquire um token, enviando o nome de usuário e a senha para o provedor de identidade.
2. Chama uma API da web usando o token.

> [!WARNING]
> Este fluxo não é recomendado. Ele requer um alto grau de exposição de confiança e o usuário.  Você deve usar apenas esse fluxo quando fluxos de outros, mais seguros, não podem ser usados. Para obter mais informações, consulte [qual é a solução ao problema crescente de senhas?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). 

O fluxo preferencial para adquirir um token silenciosamente nos computadores que ingressaram no domínio Windows é [autenticação integrada do Windows](#integrated-windows-authentication). Caso contrário, você também pode usar [fluxo de código de dispositivo](#device-code).

Embora isso seja útil em alguns casos (cenários de DevOps), se você quiser usar o nome de usuário e senha em cenários interativos em que você fornece sua própria interface do usuário, tente evitar isso. Usando o nome de usuário e senha:
- Os usuários que precisam fazer a autenticação multifator não será capazes de entrar (pois não há nenhuma interação).
- Os usuários não será capazes de logon único.

### <a name="constraints"></a>Restrições

Além do [restrições de autenticação integrada do Windows](#integrated-windows-authentication), as seguintes restrições também se aplicam:

- O fluxo de nome de usuário e senha não é compatível com acesso condicional e autenticação multifator. Como consequência, se seu aplicativo for executado em um locatário do Azure AD em que o administrador do locatário requer a autenticação multifator, é possível usar esse fluxo. Muitas organizações fazem isso.
- Ele funciona somente para contas corporativas e de Estudante (não contas da Microsoft).
- O fluxo está disponível na área de trabalho do .NET e .NET Core, mas não em plataforma Universal do Windows.

### <a name="azure-ad-b2c-specifics"></a>Especificações do Azure AD B2C

Para obter mais informações sobre como usar MSAL.NET e o Azure AD B2C, consulte [ROPC usando com o Azure AD B2C (MSAL.NET)](msal-net-aad-b2c-considerations.md#resource-owner-password-credentials-ropc-with-azure-ad-b2c).
