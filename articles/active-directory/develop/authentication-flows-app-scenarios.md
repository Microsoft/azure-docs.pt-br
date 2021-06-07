---
title: Cenários de aplicativo e fluxos de autenticação da plataforma de identidade da Microsoft | Azure
description: Saiba mais sobre cenários de aplicativos para a plataforma de identidade da Microsoft, incluindo autenticação de identidades, aquisição de tokens e chamada de APIs protegidas.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/03/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: ffa52805a5e2680d534b2b24a210465cb3fc7cac
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100557858"
---
# <a name="authentication-flows-and-application-scenarios"></a>Fluxos de autenticação e cenários de aplicativos

A plataforma de identidade da Microsoft oferece suporte à autenticação para diferentes tipos de arquiteturas de aplicativo modernas. Todas elas são baseadas nos protocolos padrão da indústria [OAuth 2.0 e OpenID Connect](active-directory-v2-protocols.md). Usando as [bibliotecas de autenticação para a plataforma de identidade da Microsoft](reference-v2-libraries.md), os aplicativos autenticam identidades e adquirem tokens para acessar APIs protegidas.

Este artigo descreve os fluxos de autenticação e os cenários de aplicativo em que eles são usados.

## <a name="application-categories"></a>Categorias de aplicativo

Os tokens podem ser adquiridos de vários tipos de aplicativos, incluindo:

- Aplicativos Web
- Aplicativos móveis
- Aplicativos da área de trabalho
- APIs da Web

Os tokens também podem ser adquiridos por aplicativos em execução em dispositivos que não têm um navegador ou que estão em execução no Internet das Coisas (IoT).

As seções a seguir descrevem as categorias de aplicativos.

### <a name="protected-resources-vs-client-applications"></a>Recursos protegidos versus aplicativos cliente

Os cenários de autenticação envolvem duas atividades:

- **Adquirindo tokens de segurança para uma API Web protegida**: Recomendamos que você use a [MSAL (biblioteca de autenticação da Microsoft)](reference-v2-libraries.md), desenvolvida e suportada pela Microsoft.
- **Protegendo uma API da Web ou um aplicativo Web**: um desafio de proteger esses recursos é validar o token de segurança. Em algumas plataformas, a Microsoft oferece [bibliotecas de middleware](reference-v2-libraries.md).

### <a name="with-users-or-without-users"></a>Com usuários ou sem usuários

A maioria dos cenários de autenticação adquire tokens em nome de usuários conectados.

![Cenários com usuários](media/scenarios/scenarios-with-users.svg)

No entanto, também há aplicativos de daemon. Nesses cenários, os aplicativos adquirem tokens em nome de si mesmos sem usuário.

![Cenários com aplicativos daemon](media/scenarios/daemon-app.svg)

### <a name="single-page-public-client-and-confidential-client-applications"></a>Aplicativos de página única, aplicativos cliente públicos e aplicativos cliente confidenciais

Os tokens de segurança podem ser adquiridos por vários tipos de aplicativos. Esses aplicativos tendem a ser separados nas três categorias a seguir. Cada uma é usada com diferentes bibliotecas e objetos.

- **Aplicativos de página única**: também conhecidos como spas, são aplicativos Web nos quais os tokens são adquiridos por um aplicativo JavaScript ou TypeScript em execução no navegador. Muitos aplicativos modernos têm um aplicativo de página única no front-end que é escrito principalmente em JavaScript. O aplicativo geralmente usa uma estrutura como Angular, React ou Vue. MSAL.js é a única biblioteca de autenticação da Microsoft que dá suporte a aplicativos de página única.

- **Aplicativos cliente públicos**: aplicativos nesta categoria, como os tipos a seguir, sempre entram em usuários:
  - Aplicativos da área de trabalho que chamam APIs da Web em nome de usuários conectados
  - Aplicativos móveis
  - Aplicativos em execução em dispositivos que não têm um navegador, como aqueles em execução na IoT

- **Aplicativos cliente confidenciais: os** aplicativos nessa categoria incluem:
  - Aplicativos Web que chamam uma API da Web
  - APIs Web que chamam uma API da Web
  - Aplicativos daemon, mesmo quando implementados como um serviço de console, como um daemon no Linux ou um serviço Windows

### <a name="sign-in-audience"></a>Conectar o público

Os fluxos de autenticação disponíveis diferem de acordo com o público-alvo para entrada. Alguns fluxos estão disponíveis apenas para contas corporativas ou de estudante. Outras estão disponíveis para contas corporativas ou de estudante e para contas pessoais da Microsoft.

Para obter mais informações, consulte [tipos de conta com suporte](v2-supported-account-types.md#account-type-support-in-authentication-flows).

## <a name="application-scenarios"></a>Cenários de aplicativos

A plataforma de identidade da Microsoft dá suporte à autenticação para essas arquiteturas de aplicativo:

- Aplicativos de página única
- Aplicativos Web
- APIs da Web
- Aplicativos móveis
- Aplicativos nativos
- Aplicativos Daemon
- Aplicativos do lado do servidor

Os aplicativos usam fluxos de autenticação diferentes para conectar usuários e obter tokens para chamar APIs protegidas.

### <a name="single-page-application"></a>Aplicativo de página única

Muitos aplicativos Web modernos são criados como aplicativos de página única do lado do cliente. Esses aplicativos usam JavaScript ou uma estrutura como angular, Vue e reagir. Esses aplicativos são executados em um navegador da Web.

Os aplicativos de página única diferem dos aplicativos Web tradicionais do lado do servidor em termos de características de autenticação. Usando a plataforma de identidade da Microsoft, aplicativos de página única podem conectar usuários e obter tokens para acessar serviços de back-end ou APIs Web. A plataforma de identidade da Microsoft oferece dois tipos de concessão para aplicativos JavaScript:

| MSAL.js (2. x) | MSAL.js (1. x) |
|---|---|
| ![Uma autenticação de aplicativo de página única](media/scenarios/spa-app-auth.svg) | ![Um aplicativo de página única implícito](media/scenarios/spa-app.svg) |

### <a name="web-app-that-signs-in-a-user"></a>Aplicativo Web que assina um usuário

![Um aplicativo Web que conecta um usuário](media/scenarios/scenario-webapp-signs-in-users.svg)

Para ajudar a proteger um aplicativo Web que assina um usuário:

- Se você desenvolver no .NET, use ASP.NET ou ASP.NET Core com o middleware ASP.NET OpenID Connect. A proteção de um recurso envolve validar o token de segurança, que é feito pelas bibliotecas [IdentityModel para .net](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) e não MSAL.

- Se você desenvolver o no Node.js, use o [nó MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) ou [Passport.js](https://github.com/AzureAD/passport-azure-ad).

Para obter mais informações, confira [Aplicativo Web que conecta os usuários](scenario-web-app-sign-user-overview.md).

### <a name="web-app-that-signs-in-a-user-and-calls-a-web-api-on-behalf-of-the-user"></a>Aplicativo Web que assina um usuário e chama uma API da Web em nome do usuário

![Um aplicativo Web chamando APIs Web](media/scenarios/web-app.svg)

Para chamar uma API da Web de um aplicativo Web em nome de um usuário, use o fluxo de código de autorização e armazene os tokens adquiridos no cache de token. Quando necessário, o MSAL atualiza os tokens e o controlador adquire silenciosamente os tokens do cache.

Para obter mais informações, consulte [aplicativo Web que chama APIs da Web](scenario-web-app-call-api-overview.md).

### <a name="desktop-app-that-calls-a-web-api-on-behalf-of-a-signed-in-user"></a>Aplicativo de área de trabalho que chama uma API da Web em nome de um usuário conectado

Para um aplicativo de desktop chamar uma API Web que conecta usuários, use os métodos de aquisição de token interativo do MSAL. Com esses métodos interativos, é possível controlar a experiência de interface do usuário de entrada. A MSAL usa um navegador da Web para essa interação.

![Um aplicativo da área de trabalho que chama uma API Web](media/scenarios/desktop-app.svg)

Há outra possibilidade para aplicativos hospedados no Windows em execução em computadores ingressados em um domínio do Windows ou ingressados no Azure AD (Azure Active Directory). Esses aplicativos podem adquirir um token silenciosamente usando a [Autenticação Integrada do Windows](https://aka.ms/msal-net-iwa).

Os aplicativos em execução em um dispositivo sem um navegador continuam podendo chamar uma API em nome de um usuário. Para autenticar, o usuário precisa entrar em outro dispositivo que tem um navegador da Web. Este cenário requer que você use o [fluxo de código do dispositivo](https://aka.ms/msal-net-device-code-flow).

![Fluxo de código do dispositivo](media/scenarios/device-code-flow-app.svg)

Embora não seja recomendável usá-lo, o [fluxo de nome de usuário/senha](scenario-desktop-acquire-token.md#username-and-password) está disponível em aplicativos cliente públicos. Esse fluxo ainda é necessário em alguns cenários, tais como o DevOps.

Usar o fluxo de nome de usuário/senha restringe seus aplicativos. Por exemplo, os aplicativos não podem entrar em um usuário que precisa usar a autenticação multifator ou a ferramenta de acesso condicional no Azure AD. Seus aplicativos também não se beneficiam do logon único. A autenticação com o fluxo de nome de usuário/senha vai contra os princípios de autenticação moderna e é fornecida somente por motivos herdados.

Em aplicativos de desktop, se você quiser que o cache de token persista, você poderá personalizar a [serialização de cache de token](scenario-desktop-acquire-token.md#file-based-token-cache). Ao implementar a [serialização de cache de token duplo](scenario-desktop-acquire-token.md#dual-token-cache-serialization-msal-unified-cache--adal-v3), você pode usar caches de token compatíveis com versões anteriores e de encaminhamento. Esses tokens dão suporte a gerações anteriores de bibliotecas de autenticação. Bibliotecas específicas incluem a ADAL.NET (Biblioteca de Autenticação do Azure AD para .NET) versão 3 e versão 4.

Para obter mais informações, confira [Aplicativo da área de trabalho que chama APIs Web](scenario-desktop-overview.md).

### <a name="mobile-app-that-calls-a-web-api-on-behalf-of-an-interactive-user"></a>Aplicativo móvel que chama uma API da Web em nome de um usuário interativo

Semelhante a um aplicativo de área de trabalho, um aplicativo móvel chama os métodos de aquisição de token interativos de MSAL para adquirir um token para chamar uma API da Web.

![Um aplicativo móvel que chama uma API Web](media/scenarios/mobile-app.svg)

A MSAL iOS e a MSAL Android usam, por padrão, o navegador da Web do sistema. No entanto, você pode direcioná-los para usar a exibição da Web inserida. Há especificidades que dependem da plataforma móvel: Plataforma Universal do Windows (UWP), iOS ou Android.

Alguns cenários, como os que envolvem o acesso condicional relacionado à ID do dispositivo ou um registro de dispositivo, exigem a instalação de um agente no dispositivo. Alguns exemplos de agentes incluem o Portal da Empresa Microsoft no Android e o Microsoft Authenticator no Android e no iOS. MSAL agora pode interagir com agentes. Para obter mais informações sobre agentes, consulte [aproveitando os agentes no Android e no Ios](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/leveraging-brokers-on-Android-and-iOS).

Para obter mais informações, confira [Aplicativo móvel que chama APIs Web](scenario-mobile-overview.md).

> [!NOTE]
> Um aplicativo móvel que usa MSAL. iOS, MSAL. O Android ou o MSAL.NET no Xamarin podem ter políticas de proteção de aplicativo aplicadas a ele. Por exemplo, as políticas podem impedir que um usuário copie texto protegido. O aplicativo móvel é gerenciado pelo Intune e é reconhecido pelo Intune como um aplicativo gerenciado. Para obter mais informações, consulte [visão geral do SDK do aplicativo Microsoft Intune](/intune/app-sdk).
>
> O [SDK do aplicativo do Intune](/intune/app-sdk-get-started) é separado das bibliotecas MSAL e interage com o Azure AD por conta própria.

### <a name="protected-web-api"></a>API Web protegida

Você pode usar o ponto de extremidade da plataforma de identidade da Microsoft para proteger serviços Web como a API Web RESTful do seu aplicativo. Uma API Web protegida é chamada por meio de um token de acesso. O token ajuda a proteger os dados da API e autenticar as solicitações de entrada. O chamador de uma API Web acrescenta um token de acesso ao cabeçalho de autorização de uma solicitação HTTP.

Se você quiser proteger sua API Web do ASP.NET ou ASP.NET Core, valide o token de acesso. Para essa validação, você usará o middleware JWT ASP.NET. A validação é feita pela biblioteca [Extensões IdentityModel para .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki), não por MSAL.NET.

Para obter mais informações, confira [API Web protegida](scenario-protected-web-api-overview.md).

### <a name="web-api-that-calls-another-web-api-on-behalf-of-a-user"></a>API Web que chama outra API da Web em nome de um usuário

Para que sua API Web protegida chame outra API da Web em nome de um usuário, seu aplicativo precisa adquirir um token para a API da Web downstream. Essas chamadas, às vezes, são referidas como chamadas de *serviço a serviço* . As APIs da Web que chamam outras APIs da Web precisam fornecer serialização de cache Personalizada.

![Uma API Web que chama outra API Web](media/scenarios/web-api.svg)

Para obter mais informações, confira [API Web que chama APIs Web](scenario-web-api-call-api-overview.md).

### <a name="daemon-app-that-calls-a-web-api-in-the-daemons-name"></a>Aplicativo daemon que chama uma API Web no nome do daemon

Os aplicativos com processos de longa duração ou que operam sem interação com um usuário também precisam de uma maneira de acessar APIs Web protegidas. Esse aplicativo pode autenticar e obter tokens usando a identidade do aplicativo. O aplicativo provar a própria identidade usando um certificado ou o segredo do cliente.

Você pode escrever esses aplicativos de daemon que adquirem um token para o aplicativo de chamada usando os métodos de aquisição de [credencial do cliente](scenario-daemon-acquire-token.md#acquiretokenforclient-api) no MSAL. Esses métodos exigem um segredo do cliente que você adiciona ao registro do aplicativo no Azure AD. Em seguida, o aplicativo compartilha o segredo com o daemon chamado. Exemplos desses segredos incluem senhas de aplicativo, declaração de certificado e asserção de cliente.

![Um aplicativo daemon chamado por outros aplicativos e APIs](media/scenarios/daemon-app.svg)

Para obter mais informações, confira [Aplicativo daemon que chama APIs Web](scenario-daemon-overview.md).

## <a name="scenarios-and-supported-authentication-flows"></a>Cenários e fluxos de autenticação com suporte

Você usa fluxos de autenticação para implementar os cenários de aplicativo que estão solicitando tokens. Não há um mapeamento individualizado entre cenários de aplicativos e fluxos de autenticação.

Os cenários que envolvem a aquisição de tokens também são mapeados para os fluxos de autenticação do OAuth 2,0. Para obter mais informações, consulte [protocolos OAuth 2,0 e OpenID Connect na plataforma de identidade da Microsoft](active-directory-v2-protocols.md).

<table>
 <thead>
  <tr><th>Cenário</th> <th>Passo a passo detalhado do cenário</th> <th>Fluxo e concessão do OAuth 2.0</th> <th>Público</th></tr>
 </thead>
 <tbody>
  <tr>
   <td><a href="scenario-spa-overview.md"><img alt="Single-Page App with Auth code" src="media/scenarios/spa-app-auth.svg"></a></td>
   <td><a href="scenario-spa-overview.md">Aplicativo de página única</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">Código de autorização</a> com PKCE</td>
   <td>Contas corporativas ou de estudante, contas pessoais e Azure Active Directory B2C (Azure AD B2C)</td>
 </tr>

  <tr>
   <td><a href="scenario-spa-overview.md"><img alt="Single-Page App with Implicit" src="media/scenarios/spa-app.svg"></a></td>
   <td><a href="scenario-spa-overview.md">Aplicativo de página única</a></td>
   <td><a href="v2-oauth2-implicit-grant-flow.md">Implícito</a></td>
   <td>Contas corporativas ou de estudante, contas pessoais e Azure Active Directory B2C (Azure AD B2C)</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-sign-user-overview.md"><img alt="Web app that signs in users" src="media/scenarios/scenario-webapp-signs-in-users.svg"></a></td>
   <td><a href="scenario-web-app-sign-user-overview.md">Aplicativo Web que conecta os usuários</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">Código de autorização</a></td>
   <td>Contas corporativas ou de estudante, contas pessoais e Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-call-api-overview.md"><img alt="Web app that calls web APIs" src="media/scenarios/web-app.svg"></a></td>
   <td><a href="scenario-web-app-call-api-overview.md">Aplicativo Web que chama as APIs Web</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">Código de autorização</a></td>
   <td>Contas corporativas ou de estudante, contas pessoais e Azure AD B2C</td>
 </tr>

  <tr>
   <td rowspan="3"><a href="scenario-desktop-overview.md"><img alt=Desktop app that calls web APIs" src="media/scenarios/desktop-app.svg"></a></td>
   <td rowspan="4"><a href="scenario-desktop-overview.md">Aplicativo da área de trabalho que chama APIs Web</a></td>
   <td>Interativo usando <a href="v2-oauth2-auth-code-flow.md">código de autorização</a> com PKCE</td>
   <td>Contas corporativas ou de estudante, contas pessoais e Azure AD B2C</td>
 </tr>

  <tr>
   <td>Autenticação Integrada do Windows</td>
   <td>Contas corporativas ou de estudante</td>
 </tr>

  <tr>
   <td><a href="v2-oauth-ropc.md">Senha do proprietário do recurso</a></td>
   <td>Contas corporativas ou de estudante e Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-desktop-acquire-token.md#command-line-tool-without-a-web-browser"><img alt="Browserless application" src="media/scenarios/device-code-flow-app.svg"></a></td>
   <td><a href="v2-oauth2-device-code.md">Código do dispositivo</a></td>
   <td>Contas corporativas ou de estudante, contas pessoais e Azure AD B2C</td>
 </tr>

 <tr>
   <td rowspan="2"><a href="scenario-mobile-overview.md"><img alt="Mobile app that calls web APIs" src="media/scenarios/mobile-app.svg"></a></td>
   <td rowspan="2"><a href="scenario-mobile-overview.md">Aplicativo móvel que chama as APIs Web</a></td>
   <td>Interativo usando <a href="v2-oauth2-auth-code-flow.md">código de autorização</a> com PKCE</td>
   <td>Contas corporativas ou de estudante, contas pessoais e Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="v2-oauth-ropc.md">Senha do proprietário do recurso</a></td>
   <td>Contas corporativas ou de estudante e Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-daemon-overview.md"><img alt="Daemon app that calls web APIs" src="media/scenarios/daemon-app.svg"></a></td>
   <td><a href="scenario-daemon-overview.md">Aplicativo daemon que chama APIs da Web</a></td>
   <td><a href="v2-oauth2-client-creds-grant-flow.md">Credenciais de cliente</a></td>
   <td>Permissões somente de aplicativo que não têm nenhum usuário e são usadas somente em organizações do Azure AD</td>
 </tr>

  <tr>
   <td><a href="scenario-web-api-call-api-overview.md"><img alt="Web API that calls web APIs" src="media/scenarios/web-api.svg"></a></td>
   <td><a href="scenario-web-api-call-api-overview.md">API Web que chama as APIs Web</a></td>
   <td><a href="v2-oauth2-on-behalf-of-flow.md">On-behalf-of</a></td>
   <td>Contas corporativas ou de estudante e contas pessoais</td>
 </tr>

 </tbody>
</table>

## <a name="scenarios-and-supported-platforms-and-languages"></a>Cenários e plataformas e idiomas com suporte

As bibliotecas de autenticação da Microsoft dão suporte a várias plataformas:

- .NET Core
- .NET Framework
- Java
- JavaScript
- macOS
- Android nativo
- iOS nativo
- Node.js
- Python
- Windows 10/UWP
- Xamarin.iOS
- Xamarin.Android

Você também pode usar várias linguagens para compilar seus aplicativos.

> [!NOTE]
> Alguns tipos de aplicativos não estão disponíveis em todas as plataformas.

Na coluna Windows da tabela a seguir, cada vez que o .NET Core é mencionado, o .NET Framework também é possível. Esse último é omitido para evitar aglomeração na tabela.

|Cenário  | Windows | Linux | Mac | iOS | Android
|--|--|--|--|--|--|--|
| [Aplicativo de página única](scenario-spa-overview.md) <br/>[![Autenticação de aplicativo de página única](media/scenarios/spa-app-auth.svg)](scenario-spa-overview.md) | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js
| [Aplicativo de página única](scenario-spa-overview.md) <br/>[![Aplicativo de página única implícito](media/scenarios/spa-app.svg)](scenario-spa-overview.md) | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js
| [Aplicativo Web que conecta os usuários](scenario-web-app-sign-user-overview.md) <br/>[![Aplicativo Web que faz logon de usuários](media/scenarios/scenario-webapp-signs-in-users.svg)](scenario-web-app-sign-user-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core ![Nó MSAL](media/sample-v2-code/small-logo-nodejs.png) <br/>Nó MSAL<br/>| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core ![Nó MSAL](media/sample-v2-code/small-logo-nodejs.png) <br/>Nó MSAL<br/>| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core ![Nó MSAL](media/sample-v2-code/small-logo-nodejs.png) <br/>Nó MSAL<br/>
| [Aplicativo Web que chama as APIs Web](scenario-web-app-call-api-overview.md) <br/> <br/>[![Aplicativo Web que chama as APIs Web](media/scenarios/web-app.svg)](scenario-web-app-call-api-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png) <br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Flask + MSAL Python ![Nó MSAL](media/sample-v2-code/small-logo-nodejs.png) <br/>Nó MSAL<br/>| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Flask + MSAL Python ![Nó MSAL](media/sample-v2-code/small-logo-nodejs.png) <br/>Nó MSAL<br/>| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/> ![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Flask + MSAL Python ![Nó MSAL](media/sample-v2-code/small-logo-nodejs.png) <br/>Nó MSAL<br/>
| [Aplicativo da área de trabalho que chama APIs Web](scenario-desktop-overview.md) <br/> <br/>[ ![ Aplicativo de desktop que chama APIs](media/scenarios/desktop-app.svg)](scenario-desktop-overview.md) ![ da Web Fluxo de código do dispositivo](media/scenarios/device-code-flow-app.svg) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/> ![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python ![Nó MSAL](media/sample-v2-code/small-logo-nodejs.png) <br/>Nó MSAL<br/>| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python ![Nó MSAL](media/sample-v2-code/small-logo-nodejs.png) <br/>Nó MSAL<br/>| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python <br/> ![Nó MSAL](media/sample-v2-code/small-logo-nodejs.png) <br/>Nó MSAL<br/> ![iOS / Objective C ou swift](media/sample-v2-code/small_logo_iOS.png) MSAL.objc |
| [Aplicativo móvel que chama as APIs Web](scenario-mobile-overview.md) <br/> [![Aplicativo móvel que chama as APIs Web](media/scenarios/mobile-app.svg)](scenario-mobile-overview.md) | ![UWP](media/sample-v2-code/small_logo_windows.png) MSAL.NET ![Xamarin](media/sample-v2-code/small_logo_xamarin.png) MSAL.NET | | | ![iOS / Objective C ou swift](media/sample-v2-code/small_logo_iOS.png) MSAL.objc | ![Android](media/sample-v2-code/small_logo_Android.png) MSAL.Android
| [Aplicativo daemon](scenario-daemon-overview.md) <br/> [![Aplicativo daemon](media/scenarios/daemon-app.svg)](scenario-daemon-overview.md) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python ![Nó MSAL](media/sample-v2-code/small-logo-nodejs.png) <br/>Nó MSAL<br/>| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png) MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python ![Nó MSAL](media/sample-v2-code/small-logo-nodejs.png) <br/>Nó MSAL<br/>| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python ![Nó MSAL](media/sample-v2-code/small-logo-nodejs.png) <br/>Nó MSAL<br/>
| [API Web que chama as APIs Web](scenario-web-api-call-api-overview.md) <br/><br/> [![API Web que chama as APIs Web](media/scenarios/web-api.svg)](scenario-web-api-call-api-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python ![Nó MSAL](media/sample-v2-code/small-logo-nodejs.png) <br/>Nó MSAL<br/>| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python ![Nó MSAL](media/sample-v2-code/small-logo-nodejs.png) <br/>Nó MSAL<br/>| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python ![Nó MSAL](media/sample-v2-code/small-logo-nodejs.png) <br/>Nó MSAL<br/>

Para obter mais informações, consulte [bibliotecas de autenticação da plataforma de identidade da Microsoft](reference-v2-libraries.md).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [noções básicas de autenticação](./authentication-vs-authorization.md) e [tokens de acesso na plataforma de identidade da Microsoft](access-tokens.md).
* Saiba mais sobre como [proteger o acesso a aplicativos de IOT](/azure/architecture/example-scenario/iot-aad/iot-aad).
