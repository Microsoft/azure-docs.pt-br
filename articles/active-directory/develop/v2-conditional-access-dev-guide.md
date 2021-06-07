---
title: Diretrizes para desenvolvedores de Acesso Condicional do Azure Active Directory
titleSuffix: Microsoft identity platform
description: Diretrizes para desenvolvedores e cenários de Acesso Condicional do Azure AD e à plataforma de identidade da Microsoft.
services: active-directory
keywords: ''
author: rwike77
manager: CelesteDG
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda
ms.date: 05/18/2020
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.topic: conceptual
ms.workload: identity
ms.openlocfilehash: e4021f0ca2c1c9ca9434744a4aebb4b7938315f0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99584223"
---
# <a name="developer-guidance-for-azure-active-directory-conditional-access"></a>Diretrizes para desenvolvedores de Acesso Condicional do Azure Active Directory

O recurso de Acesso Condicional no Azure AD (Azure Active Directory) é uma das várias maneiras que você pode usar para proteger seu aplicativo e um serviço. O Acesso Condicional permite que desenvolvedores e clientes empresariais protejam serviços de diversas maneiras, incluindo:

* [Autenticação multifator](../authentication/concept-mfa-howitworks.md)
* Permissão para que somente dispositivos inscritos no Intune acessem serviços específicos
* Restrição de locais de usuário e intervalos de IP

Para saber mais sobre os recursos completos do Acesso Condicional, confira o artigo [O que é o Acesso Condicional](../conditional-access/overview.md).

Este artigo mostra como usar o Acesso Condicional para os desenvolvedores que criam aplicativos para o Azure AD. Além disso, você aprenderá sobre o impacto de acessar recursos sobre os quais você não tem controle, que podem ter políticas de Acesso Condicional aplicadas. Este artigo também explora as implicações do Acesso Condicional no fluxo On-Behalf-Of, nos aplicativos Web, no acesso ao Microsoft Graph e nas chamadas a APIs.

Conhecimento de aplicativos de [único locatário](quickstart-register-app.md) e [multilocatário](howto-convert-app-to-be-multi-tenant.md), além de [padrões comuns de autenticação](./authentication-vs-authorization.md) é assumido.

> [!NOTE]
> O uso desse recurso requer uma licença do Azure AD Premium P1. Para localizar a licença correta para os requisitos, consulte [Comparar recursos geralmente disponíveis nas edições Gratuita, Básica e Premium](https://azure.microsoft.com/pricing/details/active-directory/).
> Os clientes com [licenças do Microsoft 365 Business](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description) também têm acesso a recursos de Acesso Condicional.

## <a name="how-does-conditional-access-impact-an-app"></a>Como o Acesso Condicional afeta um aplicativo?

### <a name="app-types-impacted"></a>Tipos de aplicativo afetados

Na maioria dos casos, o Acesso Condicional não altera o comportamento de um aplicativo nem requer alterações por parte do desenvolvedor. Somente em determinados casos em que um aplicativo solicita indiretamente ou silenciosamente um token para um serviço, um aplicativo requer alterações de código para lidar com os desafios de acesso condicional.  O que pode ser tão simples quanto executar uma solicitação de entrada interativa.

Especificamente, os cenários a seguir exigem código para lidar com os desafios de acesso condicional:

* Aplicativos executando o fluxo em nome de
* Aplicativos acessando vários serviços/recursos
* Aplicativos de página única que usam MSAL.js
* Aplicativos Web chamando um recurso

As políticas de Acesso Condicional podem ser aplicadas ao aplicativo, mas também podem ser aplicadas a uma API Web acessada pelo seu aplicativo. Para saber mais sobre como configurar uma política de acesso condicional, confira [Início Rápido: Exigir MFA para aplicativos específicos com Acesso Condicional ao Azure Active Directory](../authentication/tutorial-enable-azure-mfa.md).

Dependendo do cenário, um cliente empresarial pode aplicar e remover políticas de Acesso Condicional a qualquer momento. Para que seu aplicativo continue funcionando quando uma nova política for aplicada, implemente o tratamento de desafios. Os exemplos a seguir ilustram o tratamento de desafio.

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

O Acesso Condicional do Azure AD é um recurso incluído no [Azure AD Premium](../fundamentals/active-directory-whatis.md). Os clientes com [licenças do Microsoft 365 Business](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description) também têm acesso a recursos de Acesso Condicional.

### <a name="considerations-for-specific-scenarios"></a>Considerações para cenários específicos

As seguintes informações se aplicam apenas nestes cenários de Acesso Condicional:

* Aplicativos executando o fluxo em nome de
* Aplicativos acessando vários serviços/recursos
* Aplicativos de página única que usam MSAL.js

As seções a seguir discutem cenários comuns que são mais complexos. O princípio operacional básico é que as políticas de acesso condicional são avaliadas no momento em que o token é solicitado para o serviço que tem uma política de acesso condicional aplicada.

## <a name="scenario-app-performing-the-on-behalf-of-flow"></a>Cenário: aplicativo executando o fluxo "em nome de"

Nesse cenário, vamos acompanhar o caso em que um aplicativo nativo chama um serviço/API Web. Por sua vez, esse serviço faz o fluxo On-Behalf-Of chamar um serviço downstream. Em nosso caso, aplicamos a política de acesso condicional ao serviço downstream (API Web 2) e estamos usando um aplicativo nativo em vez um aplicativo de servidor/daemon.

![Diagrama de aplicativo executando o fluxo em nome de](./media/v2-conditional-access-dev-guide/app-performing-on-behalf-of-scenario.png)

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

Para testar esse cenário, veja nosso [exemplo de código .NET](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph#handling-required-interactions-with-the-user-dynamic-consent-mfa-etc-). Ele demonstra como passar o desafio claims de volta da API Web 1 para o aplicativo nativo e construir uma nova solicitação dentro do aplicativo cliente.

## <a name="scenario-app-accessing-multiple-services"></a>Cenário: aplicativo acessando vários serviços

Nesse cenário, vamos acompanhar o caso em que um aplicativo Web acessa dois serviços e um deles tem uma política de acesso condicional atribuída. Dependendo da lógica do seu aplicativo, pode existir um caminho no qual seu aplicativo não exige acesso a ambos os serviços Web. Nesse cenário, a ordem na qual você solicita um token tem um papel importante na experiência do usuário final.

Imagine que você tem o serviço Web A e B. O serviço Web B tem a política de acesso condicional aplicada. Enquanto a solicitação de autenticação interativa inicial exige consentimento para ambos os serviços, a política de acesso condicional não é exigida em todos os casos. Se o aplicativo solicitar um token para o serviço Web B, a política será invocada e as solicitações subsequentes para o serviço Web A também serão bem-sucedidas, como se segue.

![Diagrama do fluxo de aplicativo acessando vários serviços](./media/v2-conditional-access-dev-guide/app-accessing-multiple-services-scenario.png)

Como alternativa, se o aplicativo inicialmente solicitar um token para o serviço Web A, o usuário final não invocará a política de acesso condicional. Isso permite que o desenvolvedor do aplicativo controle a experiência do usuário final e não force a política de acesso condicional a ser invocada em todos os casos. O caso difícil será se o aplicativo solicitar depois um token para o serviço Web B. Nesse momento, o usuário final precisará cumprir a política de acesso condicional. Quando o aplicativo tenta `acquireToken`, ele pode gerar o seguinte erro (ilustrado no diagrama a seguir):

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

![Aplicativo acessando vários serviços que solicitam um novo token](./media/v2-conditional-access-dev-guide/app-accessing-multiple-services-new-token.png)

Se o aplicativo estiver usando a biblioteca MSAL, uma falha ao adquirir o token será sempre repetida interativamente. Quando essa solicitação interativa ocorre, o usuário final tem a oportunidade de cumprir o Acesso Condicional. Isso não se aplica quando a solicitação é `AcquireTokenSilentAsync` ou `PromptBehavior.Never`, caso em que o aplicativo precisa executar uma solicitação ```AcquireToken``` interativa para dar ao usuário final a oportunidade de cumprir a política.

## <a name="scenario-single-page-app-spa-using-msaljs"></a>Cenário: SPA (aplicativo de página única) que usa MSAL.js

Neste cenário, vamos acompanhar o caso em que temos um SPA (aplicativo de página única) que usa o MSAL.js para chamar uma API Web protegida pelo Acesso Condicional. É uma arquitetura simples, mas tem alguns detalhes que precisam ser considerados no desenvolvimento do Acesso Condicional.

No MSAL.js, há algumas funções que obtêm tokens: `loginPopup()`, `acquireTokenSilent(...)`, `acquireTokenPopup(…)` e `acquireTokenRedirect(…)`.

* `loginPopup()` obtém um token de ID por meio de uma solicitação de conexão interativa, mas não obtém tokens de acesso para qualquer serviço (incluindo uma API Web protegida por Acesso Condicional).
* `acquireTokenSilent(…)` pode ser usada para obter silenciosamente um token de acesso, o que significa que ela não mostra a interface do usuário em nenhuma circunstância.
* `acquireTokenPopup(…)`e `acquireTokenRedirect(…)` são usadas para solicitar interativamente um token para um recurso, o que significa que elas sempre mostram a interface do usuário de entrada.

Quando um aplicativo precisa de um token de acesso para chamar uma API Web, ele tenta um `acquireTokenSilent(…)`. Se a sessão do token estiver expirada ou você precisar cumprir uma política de acesso condicional, a função *acquireToken* falhará e o aplicativo usará `acquireTokenPopup()` ou `acquireTokenRedirect()`.

![Diagrama do fluxo de aplicativo de página única que usa MSAL](./media/v2-conditional-access-dev-guide/spa-using-msal-scenario.png)

Veja um exemplo com nosso cenário de Acesso Condicional. O usuário final apenas aterrissou no site e não tem uma sessão. Executamos uma chamada `loginPopup()`, obtemos um token de ID sem autenticação multifator. Em seguida, o usuário pressiona um botão que requer que o aplicativo solicite dados de uma API Web. O aplicativo tenta fazer uma chamada `acquireTokenSilent()`, mas falha porque o usuário ainda não executou a autenticação multifator e precisa cumprir a política de acesso condicional.

O Azure AD envia a seguinte resposta HTTP:

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
```

Nosso aplicativo precisa capturar `error=interaction_required`. O aplicativo pode usar `acquireTokenPopup()` ou `acquireTokenRedirect()` no mesmo recurso. O usuário é forçado a fazer uma autenticação multifator. Depois que o usuário conclui a autenticação multifator, o aplicativo recebe um novo token de acesso para o recurso solicitado.

Para testar esse cenário, veja nosso [exemplo de código Em nome de SPA JS](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/a2b257381b410c765ee01ecb611aa6f98c099eb1/2.%20Web%20API%20now%20calls%20Microsoft%20Graph/README.md). Esse exemplo de código usa a política de acesso condicional e a API Web que você registrou anteriormente com um SPA JS para demonstrar esse cenário. Ele mostra como tratar corretamente o desafio de declaração e obter um token de acesso que pode ser usado na API Web. Como alternativa, verifique o [exemplo de código Angular.js](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2) geral para obter orientação sobre um SPA Angular

## <a name="see-also"></a>Confira também

* Para saber mais sobre os recursos, confira [Acesso condicional no Azure Active Directory](../conditional-access/overview.md).
* Para ver mais exemplos de código do Azure AD, confira os [exemplos](sample-v2-code.md).
* Para obter mais informações sobre o SDK do MSAL e acessar a documentação de referência, consulte a [visão geral da biblioteca de autenticação da Microsoft](msal-overview.md).
* Para saber mais sobre cenários de multilocatários, confira [Como conectar usuários usando o padrão de multilocatário](howto-convert-app-to-be-multi-tenant.md).
* Saiba mais sobre o [Acesso condicional e como proteger o acesso a aplicativos IoT](/azure/architecture/example-scenario/iot-aad/iot-aad).