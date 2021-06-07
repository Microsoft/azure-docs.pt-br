---
title: Diretrizes do desenvolvedor para acesso condicional do Azure Active Directory
description: Diretrizes do desenvolvedor e cenários para acesso condicional do Azure Active Directory
services: active-directory
author: rwike77
manager: CelesteDG
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda
ms.date: 02/28/2019
ms.service: active-directory
ms.subservice: azuread-dev
ms.custom: aaddev
ms.topic: conceptual
ms.workload: identity
ROBOTS: NOINDEX
ms.openlocfilehash: b08b07ebcd6e45e95e23dff8686148889e9d4b39
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96173912"
---
# <a name="developer-guidance-for-the-azure-active-directory-conditional-access-feature"></a>Diretrizes para desenvolvedores para o Azure Active Directory recurso de acesso condicional

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

> [!NOTE]
> Para obter a versão da plataforma Microsoft Identity deste artigo, consulte [diretrizes para desenvolvedores para Azure Active Directory acesso condicional](../develop/v2-conditional-access-dev-guide.md).

O recurso de Acesso Condicional no Azure AD (Azure Active Directory) é uma das várias maneiras que você pode usar para proteger seu aplicativo e um serviço. O acesso condicional permite que desenvolvedores e clientes corporativos protejam serviços de diversas maneiras, incluindo:

* Autenticação multifator
* Permissão para que somente dispositivos inscritos no Intune acessem serviços específicos
* Restrição de locais de usuário e intervalos de IP

Para saber mais sobre os recursos completos do acesso condicional, consulte [O que é o acesso condicional](../conditional-access/overview.md).

Para os desenvolvedores que criam aplicativos para Azure Active Directory, este artigo mostra como eles podem usar o acesso condicional. Você também aprenderá sobre o impacto de acessar os recursos que você não controla e que podem ter políticas de acesso condicional aplicadas. Este artigo também explora as implicações do acesso condicional no fluxo em nome de, aplicativos Web, acesso ao Microsoft Graph e chamada de APIs.

Pressupõe-se o conhecimento de aplicativos de [único locatário e multilocatário](../develop/howto-convert-app-to-be-multi-tenant.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json), bem como [padrões comuns de autenticação](v1-authentication-scenarios.md).

## <a name="how-does-conditional-access-impact-an-app"></a>Como o acesso condicional afeta um aplicativo?

### <a name="app-types-impacted"></a>Tipos de aplicativo afetados

Na maioria dos casos, o Acesso Condicional não altera o comportamento de um aplicativo nem requer alterações por parte do desenvolvedor.  Somente em determinados casos em que um aplicativo solicita indireta ou silenciosamente um token para um serviço, um aplicativo exigirá alterações de código para tratar dos "desafios" do Acesso Condicional.  O que pode ser tão simples quanto executar uma solicitação de entrada interativa.

Especificamente, os seguintes cenários exigem código para tratar dos "desafios" do Acesso Condicional:

* Aplicativos executando o fluxo em nome de
* Aplicativos acessando vários serviços/recursos
* Aplicativos de página única usando ADAL.js
* Aplicativos Web chamando um recurso

As políticas de acesso condicional podem ser aplicadas ao aplicativo, mas também a uma API Web acessada pelo seu aplicativo. Para saber mais sobre como configurar uma política de acesso condicional, consulte [Políticas de acesso condicional comum](../conditional-access/concept-conditional-access-policy-common.md).

Dependendo do cenário, um cliente empresarial pode aplicar e remover políticas de acesso condicional a qualquer momento. Para que o aplicativo continue funcionando quando uma nova política é aplicada, você precisa implementar o tratamento de "desafio". Os exemplos a seguir ilustram o tratamento de desafio.

### <a name="conditional-access-examples"></a>Exemplos de Acesso Condicional

Alguns cenários exigem alterações de código para tratar do Acesso Condicional, enquanto outros funcionam como estão. Veja a seguir alguns cenários que usam Acesso Condicional na autenticação multifator, dando uma ideia da diferença.

* Você está criando um aplicativo iOS de único locatário e aplica uma política de acesso condicional. O aplicativo conecta um usuário e não solicita acesso a uma API. Quando o usuário entra, a política é invocada automaticamente e o usuário precisa realizar a MFA (autenticação multifator). 
* Você está criando um aplicativo nativo que usa um serviço de camada intermediária para acessar a API downstream. Um cliente empresarial na empresa usando esse aplicativo aplica uma política à API downstream. Quando um usuário final se conecta, o aplicativo nativo solicita acesso à camada intermediária e envia o token. A camada intermediária executa o fluxo “em nome de” para solicitar acesso à API downstream. Nesse momento, um "desafio" claims é apresentado à camada intermediária. A camada intermediária envia o desafio de volta ao aplicativo nativo, que precisa estar em conformidade com a política de acesso condicional.

#### <a name="microsoft-graph"></a>Microsoft Graph

O Microsoft Graph tem considerações especiais ao criar aplicativos em ambientes de Acesso Condicional. Em geral, a mecânica do Acesso Condicional se comporta da mesma forma, mas as políticas que os usuários veem serão baseadas nos dados subjacentes que seu aplicativo está solicitando do grafo. 

Especificamente, todos os escopos do Microsoft Graph representam algum conjunto de dados que pode ter políticas aplicadas somente a ele. Como as políticas de Acesso Condicional são atribuídas a conjuntos de dados específicos, o Azure AD imporá políticas de Acesso Condicional com base nos dados por trás do Graph, em vez de no próprio Graph.

Por exemplo, se um aplicativo solicitar os seguintes escopos do Microsoft Graph,

```
scopes="Bookings.Read.All Mail.Read"
```

Um aplicativo pode esperar que os usuários atendam a todas as políticas definidas no Bookings e Exchange. Alguns escopos poderão ser mapeados para vários conjuntos de dados se concederem acesso. 

### <a name="complying-with-a-conditional-access-policy"></a>Estabelecendo conformidade com uma política de acesso condicional

Para várias topologias diferentes de aplicativo, uma política de acesso condicional é avaliada quando a sessão é estabelecida. Como uma política de acesso condicional funciona na granularidade de aplicativos e serviços, o ponto em que ela é invocada depende totalmente do cenário que você está tentando executar.

Quando o aplicativo tenta acessar um serviço com uma política de acesso condicional, ele pode encontrar um desafio de Acesso Condicional. Esse desafio é codificado no parâmetro `claims` que é apresentado em uma resposta do Azure AD. Veja um exemplo desse parâmetro de desafio: 

```
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

Os desenvolvedores podem aceitar esse desafio e acrescentá-lo em uma nova solicitação no Azure AD. Para passar por esse estado, o usuário final é solicitado a executar qualquer ação necessária para cumprir a política de acesso condicional. Nos cenários a seguir, são explicadas as especificações do erro e como extrair o parâmetro.

## <a name="scenarios"></a>Cenários

### <a name="prerequisites"></a>Pré-requisitos

O acesso condicional do Azure Active Directory é um recurso incluído no [Azure Active Directory Premium](../fundamentals/active-directory-whatis.md). Você pode saber mais sobre os requisitos de licenciamento no [relatório de uso não licenciado](../reports-monitoring/overview-reports.md). Os desenvolvedores podem ingressar no [Microsoft Developer Network](/), que inclui uma assinatura gratuita para o Enterprise Mobility Suite que, por sua vez, inclui o Azure AD Premium.

### <a name="considerations-for-specific-scenarios"></a>Considerações para cenários específicos

As seguintes informações se aplicam apenas nestes cenários de Acesso Condicional:

* Aplicativos executando o fluxo em nome de
* Aplicativos acessando vários serviços/recursos
* Aplicativos de página única usando ADAL.js

As seções a seguir discutem cenários comuns que são mais complexos. O princípio operacional básico é que as políticas de acesso condicional são avaliadas no momento em que o token é solicitado para o serviço que tem uma política de acesso condicional aplicada.

## <a name="scenario-app-performing-the-on-behalf-of-flow"></a>Cenário: aplicativo executando o fluxo "em nome de"

Nesse cenário, vamos acompanhar o caso em que um aplicativo nativo chama um serviço/API Web. Por sua vez, esse serviço faz o fluxo On-Behalf-Of chamar um serviço downstream. Em nosso caso, aplicamos a política de acesso condicional ao serviço downstream (API Web 2) e estamos usando um aplicativo nativo em vez um aplicativo de servidor/daemon. 

![Diagrama de aplicativo executando o fluxo em nome de](./media/conditional-access-dev-guide/app-performing-on-behalf-of-scenario.png)

A solicitação de token inicial para a API Web 1 não solicita ao usuário final a autenticação multifator, pois a API Web 1 nem sempre pode acessar a API downstream. Depois que a API Web 1 tenta solicitar um token em nome do usuário para a API Web 2, a solicitação falha, uma vez que o usuário não se conectou com a autenticação multifator.

O Azure AD retorna uma resposta HTTP com alguns dados interessantes:

> [!NOTE]
> Nessa instância, trata-se de uma descrição do erro da autenticação multifator, mas há uma ampla variedade de `interaction_required` possíveis relacionados ao Acesso Condicional.

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API 2 App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

Na API Web 1, capturamos o erro `error=interaction_required` e enviamos de volta o desafio `claims` ao aplicativo da área de trabalho. Nesse ponto, o aplicativo da área de trabalho pode fazer uma nova chamada `acquireToken()` e acrescentar o desafio `claims` como um parâmetro extra de cadeia de consulta. Essa nova solicitação requer que o usuário faça a autenticação multifator e envie esse novo token de volta à API Web 1 e complete o fluxo em nome de.

Para testar esse cenário, veja nosso [exemplo de código .NET](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca). Ele demonstra como passar o desafio claims de volta da API Web 1 para o aplicativo nativo e construir uma nova solicitação dentro do aplicativo cliente.

## <a name="scenario-app-accessing-multiple-services"></a>Cenário: aplicativo acessando vários serviços

Nesse cenário, vamos acompanhar o caso em que um aplicativo Web acessa dois serviços e um deles tem uma política de acesso condicional atribuída. Dependendo da lógica do seu aplicativo, pode existir um caminho no qual seu aplicativo não exige acesso a ambos os serviços Web. Nesse cenário, a ordem na qual você solicita um token tem um papel importante na experiência do usuário final.

Vamos supor que temos o serviço Web A e B, e o serviço Web B tem nossa política de acesso condicional aplicada. Enquanto a solicitação de autenticação interativa inicial exige consentimento para ambos os serviços, a política de acesso condicional não é exigida em todos os casos. Se o aplicativo solicitar um token para o serviço Web B, a política será invocada e as solicitações subsequentes para o serviço Web A também serão bem-sucedidas, como se segue.

![Diagrama do fluxo de aplicativo acessando vários serviços](./media/conditional-access-dev-guide/app-accessing-multiple-services-scenario.png)

Como alternativa, se o aplicativo inicialmente solicitar um token para o serviço Web A, o usuário final não invocará a política de acesso condicional. Isso permite que o desenvolvedor do aplicativo controle a experiência do usuário final e não force a política de acesso condicional a ser invocada em todos os casos. O caso difícil será se o aplicativo solicitar subsequentemente um token para o serviço Web B. Nesse ponto, o usuário final precisa cumprir a Política de acesso condicional. Quando o aplicativo tenta `acquireToken`, ele pode gerar o seguinte erro (ilustrado no diagrama a seguir):

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

![Aplicativo acessando vários serviços que solicitam um novo token](./media/conditional-access-dev-guide/app-accessing-multiple-services-new-token.png)

Se o aplicativo estiver usando a biblioteca ADAL, uma falha ao adquirir o token será sempre repetida interativamente. Quando essa solicitação interativa ocorre, o usuário final tem a oportunidade de cumprir o acesso condicional. Isso não se aplica quando a solicitação é `AcquireTokenSilentAsync` ou `PromptBehavior.Never`, caso em que o aplicativo precisa executar uma solicitação ```AcquireToken``` interativa para dar ao usuário final a oportunidade de cumprir a política.

## <a name="scenario-single-page-app-spa-using-adaljs"></a>Cenário: SPA (aplicativo de página única) usando ADAL.js

Nesse cenário, vamos acompanhar o caso em que temos um SPA (aplicativo de página única), usando ADAL.js, para chamar uma API Web protegida pelo acesso condicional. Essa é uma arquitetura simples, mas tem algumas nuanças que precisam ser levadas em consideração no desenvolvimento do acesso condicional.

No ADAL.js, há algumas funções que obtêm tokens: `login()`, `acquireToken(...)`, `acquireTokenPopup(…)` e `acquireTokenRedirect(…)`.

* `login()` obtém um token de ID por meio de uma solicitação de entrada interativa, mas não obtém tokens de acesso para qualquer serviço (incluindo uma API Web protegida por acesso condicional).
* `acquireToken(…)` pode ser usada para obter silenciosamente um token de acesso, o que significa que ela não mostra a interface do usuário em nenhuma circunstância.
* `acquireTokenPopup(…)`e `acquireTokenRedirect(…)` são usadas para solicitar interativamente um token para um recurso, o que significa que elas sempre mostram a interface do usuário de entrada.

Quando um aplicativo precisa de um token de acesso para chamar uma API Web, ele tenta uma função `acquireToken(…)`. Se a sessão do token estiver expirada ou precisarmos cumprir uma política de acesso condicional, a função *acquireToken* falhará e o aplicativo usará `acquireTokenPopup()` ou `acquireTokenRedirect()`.

![Diagrama do fluxo de aplicativo de página única usando ADAL](./media/conditional-access-dev-guide/spa-using-adal-scenario.png)

Vamos examinar um exemplo com nosso cenário de acesso condicional. O usuário final apenas aterrissou no site e não tem uma sessão. Executamos uma chamada `login()`, obtemos um token de ID sem autenticação multifator. Em seguida, o usuário pressiona um botão que requer que o aplicativo solicite dados de uma API Web. O aplicativo tenta fazer uma chamada `acquireToken()`, mas falha porque o usuário ainda não executou a autenticação multifator e precisa cumprir a política de acesso condicional.

O Azure AD envia a seguinte resposta HTTP:

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
```

Nosso aplicativo precisa capturar `error=interaction_required`. O aplicativo pode usar `acquireTokenPopup()` ou `acquireTokenRedirect()` no mesmo recurso. O usuário é forçado a fazer uma autenticação multifator. Depois que o usuário conclui a autenticação multifator, o aplicativo recebe um novo token de acesso para o recurso solicitado.

Para testar esse cenário, veja nosso [exemplo de código Em nome de SPA JS](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca). Esse exemplo de código usa a política de acesso condicional e a API Web que você registrou anteriormente com um SPA JS para demonstrar esse cenário. Ele mostra como tratar corretamente o desafio claims e obter um token de acesso que pode ser usado para sua API Web. Como alternativa, verifique o [exemplo de código Angular.js](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) geral para obter orientação sobre um SPA Angular

## <a name="see-also"></a>Confira também

* Para saber mais sobre os recursos, confira [Acesso condicional no Azure Active Directory](../conditional-access/overview.md).
* Para obter mais exemplos de código do Azure AD, confira o [Repositório de exemplos de código do GitHub](https://github.com/azure-samples?utf8=%E2%9C%93&q=active-directory).
* Para saber mais sobre SDKs da ADAL e como acessar a documentação de referência, confira o [guia da biblioteca](active-directory-authentication-libraries.md).
* Para saber mais sobre cenários de multilocatários, confira [Como conectar usuários usando o padrão de multilocatário](../develop/howto-convert-app-to-be-multi-tenant.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).