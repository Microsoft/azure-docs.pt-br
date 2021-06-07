---
title: Fluxos de autenticação da MSAL | Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre os fluxos de autenticação e as concessões usadas pela MSAL (Biblioteca de Autenticação da Microsoft).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/25/2021
ms.author: marsma
ms.reviewer: saeeda
ms.openlocfilehash: 78932e5852453fe996e26a278f8a1859a8ecf546
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98755025"
---
# <a name="authentication-flows"></a>Fluxos de autenticação

A MSAL (biblioteca de autenticação da Microsoft) dá suporte a vários fluxos de autenticação para uso em diferentes cenários de aplicativo.

| Fluxo | Descrição | Usado em |
|--|--|--|
| [Código de autorização](#authorization-code) | Usado em aplicativos instalados em um dispositivo para obter acesso a recursos protegidos, como APIs Web. Permite que você adicione entrada e acesso à API aos seus aplicativos móveis e de área de trabalho. | [Aplicativos da área de trabalho](scenario-desktop-overview.md), [Aplicativos móveis](scenario-mobile-overview.md), [Aplicativos Web](scenario-web-app-call-api-overview.md) |
| [Credenciais do cliente](#client-credentials) | Permite acessar recursos hospedados na Web com a identidade de um aplicativo. Normalmente é usado para interações de servidor para servidor que devem ser executadas em segundo plano, sem interação imediata com um usuário. | [Aplicativos daemon](scenario-daemon-overview.md) |
| [Código do dispositivo](#device-code) | Permite que os usuários se conectem a dispositivos com restrição de entrada, como uma smart TV, um dispositivo IoT ou uma impressora. | [Aplicativos de área de trabalho/móveis](scenario-desktop-acquire-token.md#command-line-tool-without-a-web-browser) |
| [Concessão implícita](#implicit-grant) | Permite que o aplicativo obtenha tokens sem executar uma troca de credenciais de servidor back-end. Permite que o aplicativo Conecte o usuário, mantenha a sessão e obtenha tokens para outras APIs da Web, tudo no código JavaScript do cliente. | [SPA (aplicativos de página única)](scenario-spa-overview.md) |
| [On-behalf-of](#on-behalf-of) | Um aplicativo invoca um serviço ou uma API Web que, por sua vez, precisa chamar outro serviço ou API Web. A ideia é propagar as permissões e identidade de usuário delegado por meio da cadeia de solicitações. | [APIs da Web](scenario-web-api-call-api-overview.md) |
| [Nome de usuário/senha](#usernamepassword) | Permite que um aplicativo conecte o usuário, processando diretamente a senha dele. Esse fluxo não é recomendado. | [Aplicativos de área de trabalho/móveis](scenario-desktop-acquire-token.md#username-and-password) |
| [Autenticação Integrada do Windows](#integrated-windows-authentication) | Permite que os aplicativos em computadores ingressados no domínio ou no Azure AD (Azure Active Directory) adquiram um token silenciosamente (sem qualquer interação com a interface do usuário). | [Aplicativos de área de trabalho/móveis](scenario-desktop-acquire-token.md#integrated-windows-authentication) |

## <a name="how-each-flow-emits-tokens-and-codes"></a>Como cada fluxo emite tokens e códigos

Dependendo de como o aplicativo cliente é criado, ele pode usar um ou mais dos fluxos de autenticação com suporte na plataforma Microsoft Identity. Esses fluxos podem produzir vários tipos de tokens, bem como códigos de autorização, e exigem tokens diferentes para fazê-los funcionar.

| Flow                                                                               | Exige            | id_token | o token de acesso | token de atualização | código de autorização |
|------------------------------------------------------------------------------------|:-------------------:|:--------:|:------------:|:-------------:|:------------------:|
| [Fluxo do código de autorização](v2-oauth2-auth-code-flow.md)                             |                     | x        | x            | x             | x                  |
| [Credenciais do cliente](v2-oauth2-client-creds-grant-flow.md)                         |                     |          | x (somente de aplicativo) |               |                    |
| [Fluxo de código do dispositivo](v2-oauth2-device-code.md)                                       |                     | x        | x            | x             |                    |
| [Fluxo implícito](v2-oauth2-implicit-grant-flow.md)                                  |                     | x        | x            |               |                    |
| [Fluxo em-nome-de](v2-oauth2-on-behalf-of-flow.md)                                | o token de acesso        | x        | x            | x             |                    |
| [Nome de usuário/senha](v2-oauth-ropc.md) (ROPC)                                       | nome de usuário & senha | x        | x            | x             |                    |
| [Fluxo de OIDC híbrido](v2-protocols-oidc.md#protocol-diagram-access-token-acquisition) |                     | x        |              |               | x                  |
| [Resgate de token de atualização](v2-oauth2-auth-code-flow.md#refresh-the-access-token)   | token de atualização       | x        | x            | x             |                    |

### <a name="interactive-and-non-interactive-authentication"></a>Autenticação interativa e não interativa

Vários desses fluxos dão suporte à aquisição de token interativa e não interativo.

  - **Interativo** significa que o usuário pode ser solicitado a inserir a entrada. Por exemplo, solicitando que o usuário faça logon, execute a autenticação multifator (MFA) ou conceda consentimento adicional aos recursos.
  - A autenticação **não interativa**, ou *silenciosa*, tenta adquirir um token de uma maneira na qual o servidor de logon *não pode* solicitar informações adicionais ao usuário.

Seu aplicativo baseado em MSAL deve primeiro tentar adquirir um token *silenciosamente* e, em seguida, interativamente somente se o método não interativo falhar. Para obter mais informações sobre esse padrão, consulte [adquirir e armazenar tokens em cache usando a MSAL (biblioteca de autenticação da Microsoft)](msal-acquire-cache-tokens.md).

## <a name="authorization-code"></a>Código de Autorização

A [concessão de código de autorização OAuth 2](v2-oauth2-auth-code-flow.md) pode ser usada em aplicativos que estão instalados em um dispositivo para obter acesso a recursos protegidos, como APIs Web. Isso permite adicionar credenciais e acesso à API a aplicativos móveis e de área de trabalho.

Quando os usuários entram em aplicativos Web (sites), este recebe um código de autorização. O código de autorização é resgatado para adquirir um token para chamar APIs Web.

![Diagrama de fluxo de código de autorização](media/msal-authentication-flows/authorization-code.png)

No diagrama anterior, o aplicativo:

1. Solicita um código de autorização, que é resgatado para um token de acesso.
2. Usa o token de acesso para chamar a API Web.

### <a name="considerations"></a>Considerações

- É possível usar o código de autorização apenas uma vez para resgatar um token. Não tente adquirir um token várias vezes com o mesmo código de autorização porque ele é explicitamente proibido pela especificação de protocolo padrão. Se você resgatar o código várias vezes, intencionalmente ou porque não está ciente de que uma estrutura também faz isso para você, você receberá o seguinte erro:

    `AADSTS70002: Error validating credentials. AADSTS54005: OAuth2 Authorization code was already redeemed, please retry with a new valid code or use an existing refresh token.`

## <a name="client-credentials"></a>Credenciais do cliente

O [fluxo de credenciais do cliente OAuth 2](v2-oauth2-client-creds-grant-flow.md) permite que você acesse recursos hospedados na Web usando a identidade de um aplicativo. Esse tipo de concessão normalmente é usado para interações de servidor para servidor que devem ser executadas em segundo plano, sem interação imediata com um usuário. Esses tipos de aplicativo normalmente são chamados de daemons ou contas de serviço.

O fluxo de concessão de credenciais do cliente permite que um serviço Web (um cliente confidencial) use suas próprias credenciais, em vez de representar um usuário, para autenticar ao chamar outro serviço Web. Nesse cenário, o cliente é geralmente um serviço Web de camada intermediária, um serviço daemon ou um site. Para um nível mais alto de garantia, a plataforma de identidade da Microsoft também permite que o serviço de chamada use um certificado (em vez de um segredo compartilhado) como uma credencial.

> [!NOTE]
> O fluxo de cliente confidencial não está disponível em plataformas móveis como UWP, Xamarin. iOS e Xamarin. Android porque eles dão suporte apenas a aplicativos cliente públicos. Aplicativos cliente públicos não sabem como provar a identidade do aplicativo para o provedor de identidade. Uma conexão segura pode ser obtida em back-ends de aplicativo Web ou de API Web Implantando um certificado.

### <a name="application-secrets"></a>Segredos do aplicativo

![Diagrama de cliente confidencial com senha](media/msal-authentication-flows/confidential-client-password.png)

No diagrama anterior, o aplicativo:

1. Adquire um token usando credenciais de senha ou de segredo do aplicativo.
2. Usa o token para fazer solicitações do recurso.

### <a name="certificates"></a>Certificados

![Diagrama de cliente confidencial com certificado](media/msal-authentication-flows/confidential-client-certificate.png)

No diagrama anterior, o aplicativo:

1. Adquire um token usando credenciais de certificado.
2. Usa o token para fazer solicitações do recurso.

Essas credenciais de cliente precisam ser:

- Registradas no Azure AD.
- Passado ao construir o objeto de aplicativo cliente confidencial em seu código.

## <a name="device-code"></a>Código do dispositivo

O [fluxo de código do dispositivo OAuth 2](v2-oauth2-device-code.md) permite que os usuários entrem em dispositivos com restrições de entrada, como Smart TVs, dispositivos IOT e impressoras. A autenticação interativa com o Azure AD requer um navegador da Web. Quando o dispositivo ou o sistema operacional não fornece um navegador da Web, o fluxo de código do dispositivo permite que o usuário use outro dispositivo como um computador ou um celular para entrar interativamente.

Usando o fluxo de código do dispositivo, o aplicativo obtém tokens por meio de um processo de duas etapas projetado para esses dispositivos e sistemas operacionais. Exemplos desses aplicativos incluem aqueles executados em dispositivos IoT e ferramentas de CLI (interface de linha de comando).

![Diagrama do fluxo de código do dispositivo](media/msal-authentication-flows/device-code.png)

No diagrama anterior:

1. Sempre que a autenticação do usuário é necessária, o aplicativo fornece um código e solicita que o usuário use outro dispositivo como um smartphone conectado à Internet para visitar uma URL (por exemplo, `https://microsoft.com/devicelogin` ). Em seguida, o usuário é solicitado a inserir o código e prosseguir com uma experiência de autenticação normal, incluindo prompts de consentimento e [autenticação multifator](../authentication/concept-mfa-howitworks.md), se necessário.
1. Após a autenticação bem-sucedida, o aplicativo de linha de comando recebe os tokens necessários por meio de um canal de apoio e os usa para executar as chamadas à API Web necessárias.

### <a name="constraints"></a>Restrições

- O fluxo de código do dispositivo está disponível somente em aplicativos cliente públicos.
- A autoridade passada na construção do aplicativo cliente público deve ser uma das seguintes:
  - Com o locatário, no formato `https://login.microsoftonline.com/{tenant}/,` , `{tenant}` o GUID que representa a ID do locatário ou um nome de domínio associado ao locatário.
  - Para contas corporativas e de estudante no formulário `https://login.microsoftonline.com/organizations/` .

## <a name="implicit-grant"></a>Concessão implícita

O fluxo de [concessão implícita do OAuth 2](v2-oauth2-implicit-grant-flow.md) permite que o aplicativo obtenha tokens da plataforma Microsoft Identity sem executar uma troca de credenciais de servidor back-end. Esse fluxo permite que o aplicativo Conecte o usuário, mantenha uma sessão e obtenha tokens para outras APIs da Web, tudo no código JavaScript do cliente.

![Diagrama de fluxo de concessão implícita](media/msal-authentication-flows/implicit-grant.svg)

Muitos aplicativos Web modernos são criados como cliente, aplicativos de página única (SPA) escritos em JavaScript ou em uma estrutura SPA, como angular, Vue.js e React.js. Esses aplicativos são executados em um navegador da Web e têm características de autenticação diferentes dos aplicativos Web do lado do servidor tradicionais. A plataforma de identidade da Microsoft permite que aplicativos de página única conectem usuários e obtenham tokens para acessar serviços de back-end ou APIs Web usando o fluxo de concessão implícita. O fluxo implícito permite que o aplicativo obtenha tokens de ID para representar o usuário autenticado e também os tokens de acesso necessários para chamar APIs protegidas.

Esse fluxo de autenticação não inclui cenários de aplicativo que usam estruturas JavaScript de plataforma cruzada, como o uso de aplicativos de informações de irestas ou React-Native, porque exigem mais recursos para interação com as plataformas nativas.

Tokens emitidos por meio do modo de fluxo implícito têm uma **limitação de comprimento** porque são retornados ao navegador pela URL (onde `response_mode` é `query` ou `fragment` ). Alguns navegadores limitam o comprimento da URL na barra do navegador e falham quando é muito longo. Portanto, esses tokens de fluxo implícitos não contêm `groups` ou `wids` declarações.

## <a name="on-behalf-of"></a>On-behalf-of

O fluxo de [fluxo de autenticação em nome de OAuth 2](v2-oauth2-on-behalf-of-flow.md) é usado quando um aplicativo chama um serviço ou uma API Web que, por sua vez, precisa chamar outro serviço ou API Web. A ideia é propagar a identidade do usuário delegado e as permissões pela cadeia de solicitação. Para que o serviço de camada intermediária faça solicitações autenticadas para o serviço downstream, ele precisa proteger um token de acesso da plataforma Microsoft Identity *em nome do* usuário.

![Diagrama do fluxo On-behalf-of](media/msal-authentication-flows/on-behalf-of.png)

No diagrama anterior:

1. O aplicativo adquire um token de acesso para a API Web.
2. Um cliente (Web, área de trabalho, móvel ou aplicativo de página única) chama uma API Web protegida, adicionando o token de acesso como um token de portador ao cabeçalho de autenticação da solicitação HTTP. A API Web autentica o usuário.
3. Quando o cliente chama a API Web, ela solicita outro token em nome do usuário.
4. A API Web protegida usa esse token para chamar uma API Web downstream em nome do usuário. A API Web também pode solicitar tokens para outras APIs downstream (mas ainda em nome do mesmo usuário).

## <a name="usernamepassword"></a>Nome de usuário/senha

A concessão de credenciais de senha (ROPC) do [proprietário do recurso OAuth 2](v2-oauth-ropc.md) permite que um aplicativo Conecte o usuário manipulando sua senha diretamente. Em seu aplicativo de área de trabalho, é possível usar o fluxo de nome de usuário/senha para adquirir um token silenciosamente. Não é necessária uma interface do usuário ao usar o aplicativo.

![Diagrama do fluxo de nome de usuário/senha](media/msal-authentication-flows/username-password.png)

No diagrama anterior, o aplicativo:

1. Adquire um token ao enviar o nome de usuário e a senha ao provedor de identidade.
2. Chama a API Web usando o token.

> [!WARNING]
> Esse fluxo não é recomendado. Ele requer um alto grau de confiança e exposição de credenciais. Você deve usar esse fluxo *somente* quando fluxos mais seguros não puderem ser usados. Para saber mais, confira [Qual é a solução para o problema crescente das senhas?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/).

O fluxo preferencial para adquirir um token silenciosamente em computadores Windows ingressados no domínio é a [Autenticação Integrada do Windows](#integrated-windows-authentication). Em outros casos, use o [fluxo de código do dispositivo](#device-code).

Embora o fluxo de nome de usuário/senha possa ser útil em alguns cenários como DevOps, evite-o se você quiser usar o nome de usuário/senha em cenários interativos em que você fornece sua própria interface do usuário.

Ao usar o nome de usuário/senha:

- Os usuários que precisam executar a autenticação multifator não poderão entrar porque não há nenhuma interação.
- Os usuários não poderão fazer logon único.

### <a name="constraints"></a>Restrições

Além das [restrições da Autenticação Integrada do Windows](#integrated-windows-authentication), as restrições a seguir também se aplicam:

- O fluxo de nome de usuário/senha não é compatível com o Acesso Condicional e a autenticação multifator. Como consequência, se o aplicativo for executado em um locatário do Azure AD em que o administrador de locatários exige autenticação multifator, você não poderá usar esse fluxo. Muitas organizações fazem isso.
- ROPC funciona apenas para contas corporativas e de estudante. Você não pode usar o ROPC para contas da Microsoft (MSA).
- O fluxo está disponível no desktop do .NET e no .NET Core, mas não na Plataforma Universal do Windows.
- No Azure AD B2C, o fluxo ROPC funciona apenas para contas locais. Para obter informações sobre ROPC em MSAL.NET e Azure AD B2C, consulte [usando o ROPC com Azure ad B2C](msal-net-aad-b2c-considerations.md#resource-owner-password-credentials-ropc).

## <a name="integrated-windows-authentication"></a>Autenticação Integrada do Windows

O MSAL dá suporte à autenticação integrada do Windows (IWA) para desktops e aplicativos móveis que são executados em um computador Windows ingressado no domínio ou no Azure AD. Usando o IWA, esses aplicativos podem adquirir um token silenciosamente sem exigir interação com a interface do usuário.

![Diagrama da Autenticação Integrada do Windows](media/msal-authentication-flows/integrated-windows-authentication.png)

No diagrama anterior, o aplicativo:

1. Adquire um token usando a Autenticação Integrada do Windows.
2. Usa o token para fazer solicitações do recurso.

### <a name="constraints"></a>Restrições

A autenticação integrada do Windows (IWA) dá suporte *apenas* a usuários federados-usuários criados no Active Directory e apoiados pelo Azure AD. Os usuários criados diretamente no Azure AD sem Active Directory backup (usuários gerenciados) não podem usar esse fluxo de autenticação. Essa limitação não afeta o [fluxo de nome de usuário/senha](#usernamepassword).

O IWA é para aplicativos .NET Framework, .NET Core e Plataforma Universal do Windows.

A IWA não ignora a autenticação multifator. Se a autenticação multifator estiver configurada, a IWA poderá falhar se um desafio de autenticação multifator for exigido. A autenticação multifator exige interação do usuário.

Você não controla o momento em que o provedor de identidade exige que a autenticação de dois fatores seja realizada. Mas o administrador de locatário controla. Normalmente, a autenticação de dois fatores é necessária quando você entra em um país/região diferente, quando você não está conectado via VPN a uma rede corporativa e, às vezes, até quando você está conectado via VPN. O Azure AD usa IA para aprender continuamente se a autenticação de dois fatores é necessária. Se IWA falhar, você deverá retornar a um [prompt de usuário interativo](#interactive-and-non-interactive-authentication).

A autoridade passada ao construir o aplicativo cliente público deve ser uma das:

- Com o locatário, no formato `https://login.microsoftonline.com/{tenant}/,` , `{tenant}` o GUID que representa a ID do locatário ou um nome de domínio associado ao locatário.
- Para contas corporativas ou de estudante (`https://login.microsoftonline.com/organizations/`). Não há suporte para o MSA (contas pessoais da Microsoft). Você não pode usar `/common` ou `/consumers` locatários.

Como a IWA é um fluxo silencioso, uma das seguintes afirmações deve ser verdadeira:

- O usuário do seu aplicativo deve ter consentido o uso do aplicativo anteriormente.
- O administrador de locatários deve ter consentido o uso do aplicativo anteriormente para todos os usuários no locatário.

Isso significa que uma das afirmações a seguir é verdadeira:

- Você, como desenvolvedor, selecionou **Grant** no portal do Azure por conta própria.
- Um administrador de locatários selecionou **conceder/revogar consentimento de administrador para {domínio de locatário}** na guia **permissões de API** do registro de aplicativo no portal do Azure (consulte [adicionar permissões para acessar sua API Web](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api)).
- Você forneceu uma maneira para os usuários consentirem com o aplicativo; consulte [solicitando consentimento de usuário individual](v2-permissions-and-consent.md#requesting-individual-user-consent).
- Você forneceu uma maneira para o administrador do locatário consentir para o aplicativo; consulte [consentimento do administrador](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

O fluxo da IWA está habilitado para aplicativos de área de trabalho do .NET, o .NET Core e a Plataforma Universal do Windows.

Para saber mais sobre o consentimento, confira [Permissões e consentimento v2.0](v2-permissions-and-consent.md).

## <a name="next-steps"></a>Próximas etapas

Agora que você examinou os fluxos de autenticação com suporte na MSAL (biblioteca de autenticação da Microsoft), saiba mais sobre como adquirir e armazenar em cache os tokens usados nesses fluxos:

[Adquirir e armazenar tokens em cache usando a MSAL (biblioteca de autenticação da Microsoft)](msal-acquire-cache-tokens.md)
