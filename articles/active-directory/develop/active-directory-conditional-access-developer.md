---
title: Diretrizes do desenvolvedor para acesso condicional do Azure Active Directory | Microsoft Docs
description: "Diretrizes do desenvolvedor e cenários para acesso condicional do Azure AD"
services: active-directory
keywords: 
author: danieldobalian
manager: mbaldwin
editor: PatAltimore
ms.author: dadobali
ms.date: 07/19/2017
ms.assetid: 115bdab2-e1fd-4403-ac15-d4195e24ac95
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.openlocfilehash: ed42af90a9c467042ff2537a38646f59147fb5ad
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="developer-guidance-for-azure-active-directory-conditional-access"></a>Diretrizes do desenvolvedor para acesso condicional do Azure Active Directory

O Azure AD (Azure Active Directory) oferece várias maneiras de proteger seu aplicativo e um serviço.  Um desses recursos exclusivos é o acesso condicional.  O acesso condicional permite que desenvolvedores e clientes corporativos protejam serviços de diversas maneiras, incluindo:

* Autenticação multifator
* Permissão para que somente dispositivos inscritos no Intune acessem serviços específicos
* Restrição de locais de usuário e intervalos de IP

Para saber mais sobre os recursos completos do acesso condicional, confira [Acesso condicional no portal clássico do Azure](../active-directory-conditional-access.md). 

Neste artigo, vamos nos concentrar no significado do acesso condicional para os desenvolvedores que criam aplicativos para o Azure AD.  O artigo pressupõe o conhecimento de aplicativos de [único locatário](active-directory-integrating-applications.md) e [multilocatário](active-directory-devhowto-multi-tenant-overview.md), além de [padrões comuns de autenticação](active-directory-authentication-scenarios.md).

Vamos nos aprofundar no impacto de acessar recursos sobre os quais você não tem controle que podem ter políticas de acesso condicional aplicadas.  Além disso, exploraremos as implicações do acesso condicional no fluxo em nome de, aplicativos Web, acessando o Microsoft Graph e chamando APIs.

## <a name="how-does-conditional-access-impact-an-app"></a>Como o acesso condicional afeta um aplicativo?

### <a name="app-types-impacted"></a>Tipos de aplicativo afetados

Na maioria dos casos, o acesso condicional não altera o comportamento de um aplicativo nem requer alterações por parte do desenvolvedor.  Somente em determinados casos em que um aplicativo solicita indireta ou silenciosamente um token para um serviço, um aplicativo exigirá alterações de código para tratar dos "desafios" do acesso condicional.  O que pode ser tão simples quanto executar uma solicitação de entrada interativa. 

Especificamente, os seguintes cenários exigem código para tratar dos "desafios" do acesso condicional: 

* Aplicativos acessando o Microsoft Graph
* Aplicativos executando o fluxo em nome de
* Aplicativos acessando vários serviços/recursos
* Aplicativos de única página usando ADAL.js
* Aplicativos Web chamando um recurso

As políticas de acesso condicional podem ser aplicadas ao aplicativo, mas também podem ser aplicadas a uma API Web acessada pelo seu aplicativo. Para saber mais sobre como configurar uma política de acesso condicional, confira [Introdução ao acesso condicional ao Azure Active Directory](../active-directory-conditional-access-azuread-connected-apps.md#configure-per-application-access-rules).

Dependendo do cenário, um cliente empresarial pode aplicar e remover políticas de acesso condicional a qualquer momento.  Para que o aplicativo continue funcionando quando uma nova política é aplicada, você precisa implementar o tratamento de "desafio". Os exemplos a seguir ilustram o tratamento de desafio. 

### <a name="conditional-access-examples"></a>Exemplos de acesso condicional

Alguns cenários exigem alterações de código para tratar do acesso condicional, enquanto outros funcionam como estão.  Veja a seguir alguns cenários que usam acesso condicional na autenticação multifator, dando uma ideia da diferença.

* Você está criando um aplicativo iOS de único locatário e aplica uma política de acesso condicional.  O aplicativo conecta um usuário e não solicita acesso a uma API.  Quando o usuário entra, a política é invocada automaticamente e o usuário precisa realizar a MFA (autenticação multifator). 
* Você está criando um aplicativo Web multilocatário que usa o Microsoft Graph para acessar o Exchange, entre outros serviços.  Um cliente empresarial que adota esse aplicativo define uma política no Exchange.  Quando o aplicativo Web solicita um token do MS Graph, o aplicativo não é desafiado a cumprir a política.  O usuário final é conectado com tokens válidos. Quando o aplicativo tenta usar esse token com o Microsoft Graph para acessar dados do Exchange, um “desafio” de declarações é retornado ao aplicativo Web por meio do cabeçalho ```WWW-Authenticate```.  O aplicativo então pode usar o ```claims``` em uma nova solicitação e o usuário final será solicitado a cumprir as condições. 
* Você está criando um aplicativo nativo que usa um serviço de camada intermediária para acessar a API downstream.  Um cliente empresarial na empresa usando esse aplicativo aplica uma política à API downstream.  Quando um usuário final se conecta, o aplicativo nativo solicita acesso à camada intermediária e envia o token.  A camada intermediária executa o fluxo “em nome de” para solicitar acesso à API downstream.  Nesse momento, um "desafio" claims é apresentado à camada intermediária. A camada intermediária envia o desafio de volta ao aplicativo nativo, que precisa estar em conformidade com a política de acesso condicional.

### <a name="complying-with-a-conditional-access-policy"></a>Estabelecendo conformidade com uma política de acesso condicional

Para várias topologias diferentes de aplicativo, uma política de acesso condicional é avaliada quando a sessão é estabelecida.  Como uma política de acesso condicional funciona na granularidade de aplicativos e serviços, o ponto em que ela é invocada depende totalmente do cenário que você está tentando executar.

Quando o aplicativo tenta acessar um serviço com uma política de acesso condicional, ele pode encontrar um desafio de acesso condicional.  Esse desafio é codificado no parâmetro `claims` que é apresentado em uma resposta do Azure AD ou do Microsoft Graph.  Veja um exemplo desse parâmetro de desafio: 

```
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

Os desenvolvedores podem aceitar esse desafio e acrescentá-lo em uma nova solicitação no Azure AD.  Passar por esse estado solicita que o usuário final execute qualquer ação necessária para cumprir a política de acesso condicional. Nos cenários a seguir, são explicadas as especificações do erro e como extrair o parâmetro. 

## <a name="scenarios"></a>Cenários

### <a name="prerequisites"></a>Pré-requisitos

O acesso condicional do Azure AD é um recurso incluído no [Azure AD Premium](../active-directory-whatis.md#choose-an-edition).  Você pode saber mais sobre os requisitos de licenciamento no [relatório de uso não licenciado](../active-directory-conditional-access-unlicensed-usage-report.md).  Os desenvolvedores podem ingressar no [Microsoft Developer Network](https://msdn.microsoft.com/dn308572.aspx), que inclui uma assinatura gratuita para o Enterprise Mobility Suite que, por sua vez, inclui o Azure AD Premium.

### <a name="considerations-for-specific-scenarios"></a>Considerações para cenários específicos

As seguintes informações se aplicam apenas nestes cenários de acesso condicional:

* Aplicativos acessando o Microsoft Graph
* Aplicativos executando o fluxo em nome de
* Aplicativos acessando vários serviços/recursos
* Aplicativos de única página usando ADAL.js

Nas seções a seguir, vamos explorar cenários comuns mais complexos.  O princípio operacional básico é que as políticas de acesso condicional são avaliadas no momento em que o token é solicitado para o serviço que tem uma política de acesso condicional aplicada, a menos que ele esteja sendo acessado por meio do Microsoft Graph.

## <a name="scenario-app-accessing-microsoft-graph"></a>Cenário: aplicativo acessando o Microsoft Graph

Nesse cenário, acompanharemos o caso em que um aplicativo Web solicita acesso ao Microsoft Graph. A política de acesso condicional, nesse caso, pode ser atribuída ao SharePoint, Exchange ou a algum outro serviço que seja acessado como uma carga de trabalho por meio do Microsoft Graph.  Neste exemplo, vamos supor que haja uma política de acesso condicional no Sharepoint Online.

![Diagrama do fluxo de aplicativo acessando o Microsoft Graph](media/active-directory-conditional-access-developer/app-accessing-microsoft-graph-scenario.png)

Primeiramente, o aplicativo solicita autorização ao Microsoft Graph, que exige acesso a uma carga de trabalho downstream sem acesso condicional.  A solicitação é bem-sucedida sem invocar qualquer política e o aplicativo recebe tokens para o Microsoft Graph.  Nesse ponto, o aplicativo pode usar o token de acesso em uma solicitação de portador para o ponto de extremidade solicitado. Agora, o aplicativo precisa acessar um ponto de extremidade do Sharepoint Online do Microsoft Graph, por exemplo: `https://graph.microsoft.com/v1.0/me/mySite`

O aplicativo já tem um token válido para o Microsoft Graph, de modo que ele pode executar a nova solicitação sem emitir um novo token. Essa solicitação falha e um desafio de declarações é emitido pelo Microsoft Graph na forma de um HTTP 403 Proibido com um desafio ```WWW-Authenticate```.
Veja um exemplo de resposta: 

```
HTTP 403; Forbidden 
error=insufficient_claims
www-authenticate="Bearer realm="", authorization_uri="https://login.windows.net/common/oauth2/authorize", client_id="<GUID>", error=insufficient_claims, claims={"access_token":{"polids":{"essential":true,"values":["<GUID>"]}}}"
```

O desafio de declarações está dentro do cabeçalho ```WWW-Authenticate```, que pode ser analisado de modo a extrair o parâmetro claims para a próxima solicitação.  Depois que ele é acrescentado à nova solicitação, o Azure AD sabe que deve avaliar a política de acesso condicional ao conectar o usuário, e o aplicativo agora está em conformidade com a política de acesso condicional.  A repetição da solicitação para o ponto de extremidade do Sharepoint Online é bem-sucedida.

O cabeçalho ```WWW-Authenticate``` têm uma estrutura única e não é tão simples de analisar para extrair valores.  Aqui está um método breve para ajudar.

    ```C#
        /// <summary>
        /// This method extracts the claims value from the 403 error response from MS Graph. 
        /// </summary>
        /// <param name="wwwAuthHeader"></param>
        /// <returns>Value of the claims entry. This should be considered an opaque string. 
        /// Returns null if the wwwAuthheader does not contain the claims value. </returns>
        private String extractClaims(String wwwAuthHeader)
        {
            String ClaimsKey = "claims=";
            String ClaimsSubstring = "";
            if (wwwAuthHeader.Contains(ClaimsKey))
            {
                int Index = wwwAuthHeader.IndexOf(ClaimsKey);
                ClaimsSubstring = wwwAuthHeader.Substring(Index, wwwAuthHeader.Length - Index);
                string ClaimsChallenge;
                if (Regex.Match(ClaimsSubstring, @"}$").Success)
                {
                    ClaimsChallenge = ClaimsSubstring.Split('=')[1];
                }
                else
                {
                    ClaimsChallenge = ClaimsSubstring.Substring(0, ClaimsSubstring.IndexOf("},") + 1);
                }
                return ClaimsChallenge;
            }
            return null; 
        }
    ```

Para obter exemplos de código que demonstrem como lidar com o desafio de declarações, consulte o [exemplo de código “em nome de”](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca) para .NET ADAL.

## <a name="scenario-app-performing-the-on-behalf-of-flow"></a>Cenário: aplicativo executando o fluxo em nome de

Nesse cenário, vamos acompanhar o caso em que um aplicativo nativo chama um serviço/API Web.  Por sua vez, esse serviço faz [o fluxo "em nome de"](active-directory-authentication-scenarios.md#application-types-and-scenarios) chamar um serviço downstream.  Em nosso caso, aplicamos nossa política de acesso condicional ao serviço downstream (API Web 2) e estamos usando um aplicativo nativo em vez um aplicativo de servidor/daemon. 

![Diagrama de aplicativo executando o fluxo em nome de](media/active-directory-conditional-access-developer/app-performing-on-behalf-of-scenario.png)

A solicitação de token inicial para a API Web 1 não solicita ao usuário final a autenticação multifator, pois a API Web 1 nem sempre pode acessar a API downstream.  Depois que a API Web 1 tenta solicitar um token em nome do usuário para a API Web 2, a solicitação falha, uma vez que o usuário não se conectou com a autenticação multifator.

O Azure AD retorna uma resposta HTTP com alguns dados interessantes: 

> [!NOTE]
> Nessa instância, trata-se de uma descrição do erro da autenticação multifator, mas há uma ampla gama de `interaction_required` possíveis relacionados ao acesso condicional.  

```
HTTP 400; Bad Request 
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API 2 App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

Em nossa API Web 1, capturamos o erro `error=interaction_required` e enviamos de volta o desafio `claims` ao aplicativo da área de trabalho.  Nesse ponto, o aplicativo da área de trabalho pode fazer uma nova chamada `acquireToken()` e acrescentar o desafio `claims` como um parâmetro extra de cadeia de consulta.  Essa nova solicitação requer que o usuário faça a autenticação multifator e envie esse novo token de volta à API Web 1 e complete o fluxo em nome de.

Para testar esse cenário, veja nosso [exemplo de código .NET](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca).  Ele demonstra como passar o desafio claims de volta da API Web 1 para o aplicativo nativo e construir uma nova solicitação dentro do aplicativo cliente. 

## <a name="scenario-app-accessing-multiple-services"></a>Cenário: aplicativo acessando vários serviços

Nesse cenário, vamos acompanhar o caso em que um aplicativo Web acessa dois serviços, e um deles tem uma política de acesso condicional atribuída.  Dependendo da lógica do seu aplicativo, pode existir um caminho no qual seu aplicativo não exige acesso a ambos os serviços Web.  Nesse cenário, a ordem na qual você solicita um token tem um papel importante na experiência do usuário final.

Vamos supor que temos o serviço Web A e B, e o serviço Web B tem nossa política de acesso condicional aplicada.  Enquanto a solicitação de autenticação interativa inicial exige consentimento para ambos os serviços, a política de acesso condicional não é exigida em todos os casos.  Se o aplicativo solicitar um token para o serviço Web B, a política será invocada e as solicitações subsequentes para o serviço Web A também serão bem-sucedidas, como se segue.

![Diagrama do fluxo de aplicativo acessando vários serviços](media/active-directory-conditional-access-developer/app-accessing-multiple-services-scenario.png)

Como alternativa, se o aplicativo inicialmente solicitar um token para o serviço Web A, o usuário final não invocará a política de acesso condicional.  Isso permite que o desenvolvedor do aplicativo controle a experiência do usuário final e não force a política de acesso condicional a ser invocada em todos os casos. O caso difícil será se o aplicativo solicitar subsequentemente um token para o serviço Web B. Nesse ponto, o usuário final precisa cumprir a política de acesso condicional.  Quando o aplicativo tenta `acquireToken`, ele pode gerar o seguinte erro (ilustrado no diagrama a seguir): 

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
``` 

![Aplicativo acessando vários serviços que solicitam um novo token](media/active-directory-conditional-access-developer/app-accessing-multiple-services-new-token.png)

Se o aplicativo estiver usando a biblioteca ADAL, uma falha ao adquirir o token será sempre repetida interativamente.  Quando essa solicitação interativa ocorre, o usuário final tem a oportunidade de cumprir o acesso condicional.  Isso não se aplica quando a solicitação é `AcquireTokenSilentAsync` ou `PromptBehavior.Never`, caso em que o aplicativo precisa executar uma solicitação ```AcquireToken``` interativa para dar ao usuário final a oportunidade de cumprir a política. 

## <a name="scenario-single-page-app-spa-using-adaljs"></a>Cenário: SPA (Aplicativo de Única Página) usando ADAL.js

Nesse cenário, vamos acompanhar o caso em que temos um SPA (aplicativo de única página) usando ADAL.js para chamar uma API Web protegida pelo acesso condicional.  Essa é uma arquitetura simples, mas tem algumas nuanças que precisam ser levadas em consideração no desenvolvimento do acesso condicional.

No ADAL.js, há algumas funções que obtêm tokens: `login()`, `acquireToken(...)`, `acquireTokenPopup(…)` e `acquireTokenRedirect(…)`. 

* `login()` obtém um token de ID por meio de uma solicitação de entrada interativa, mas não obtém tokens de acesso para qualquer serviço (incluindo uma API Web protegida por acesso condicional).  
* `acquireToken(…)` pode ser usada para obter silenciosamente um token de acesso, o que significa que ela não mostra a interface do usuário em nenhuma circunstância.  
* `acquireTokenPopup(…)`e `acquireTokenRedirect(…)` são usadas para solicitar interativamente um token para um recurso, o que significa que elas sempre mostram a interface do usuário de entrada.

Quando um aplicativo precisa de um token de acesso para chamar uma API Web, ele tenta uma função `acquireToken(…)`.  Se a sessão do token estiver expirada ou precisarmos cumprir uma política de acesso condicional, a função *acquireToken* falhará e o aplicativo usará `acquireTokenPopup()` ou `acquireTokenRedirect()`.

![Diagrama do fluxo de aplicativo de única página usando ADAL](media/active-directory-conditional-access-developer/spa-using-adal-scenario.png)

Vamos examinar um exemplo com nosso cenário de acesso condicional.  O usuário final apenas aterrissou no site e não tem uma sessão.  Executamos uma chamada `login()`, obtemos um token de ID sem autenticação multifator.  Em seguida, o usuário pressiona um botão que requer que o aplicativo solicite dados de uma API Web.  O aplicativo tenta fazer uma chamada `acquireToken()`, mas falha porque o usuário ainda não executou a autenticação multifator e precisa cumprir a política de acesso condicional.

O Azure AD envia a seguinte resposta HTTP: 

```
HTTP 400; Bad Request 
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
```

Nosso aplicativo precisa capturar `error=interaction_required`.  O aplicativo pode usar `acquireTokenPopup()` ou `acquireTokenRedirect()` no mesmo recurso.  O usuário é forçado a fazer uma autenticação multifator. Depois que o usuário conclui a autenticação multifator, o aplicativo recebe um novo token de acesso para o recurso solicitado.

Para testar esse cenário, veja nosso [exemplo de código Em nome de SPA JS](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca).  Esse exemplo de código usa a política de acesso condicional e a API Web que você registrou anteriormente com um SPA JS para demonstrar esse cenário. Ele mostra como tratar corretamente o desafio claims e obter um token de acesso que pode ser usado para sua API Web. Como alternativa, verifique o [exemplo de código Angular.js](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) geral para obter orientação sobre um SPA Angular


## <a name="see-also"></a>Consulte também

* Para saber mais sobre os recursos, confira [Acesso condicional no Azure AD](../active-directory-conditional-access.md).
* Para obter mais exemplos de código do Azure AD, confira o [repositório Github de exemplos de código](https://github.com/azure-samples?utf8=%E2%9C%93&q=active-directory). 
* Para saber mais sobre SDKs da ADAL e como acessar a documentação de referência, confira o [guia da biblioteca](active-directory-authentication-libraries.md).
* Para saber mais sobre cenários de multilocatários, confira [Como conectar usuários usando o padrão de multilocatário](active-directory-devhowto-multi-tenant-overview.md).
