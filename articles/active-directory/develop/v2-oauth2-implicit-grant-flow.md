---
title: Fluxo de concessão implícita do OAuth 2,0 – a plataforma Microsoft Identity | Azure
description: Proteger aplicativos de página única usando o fluxo implícito da plataforma de identidade da Microsoft.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: f3598c6f072d09d7e427db66dcfbf8721b92a3a1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99226481"
---
# <a name="microsoft-identity-platform-and-implicit-grant-flow"></a>Plataforma de identidade da Microsoft e fluxo de concessão implícita

A plataforma de identidade da Microsoft dá suporte ao fluxo de concessão implícita do OAuth 2,0, conforme descrito na [especificação do OAuth 2,0](https://tools.ietf.org/html/rfc6749#section-4.2). A definição da característica da concessão implícita é que os tokens (tokens de ID ou tokens de acesso) são retornados diretamente do ponto de extremidade/Authorize em vez do ponto de extremidade/token. Isso geralmente é usado como parte do [fluxo do código de autorização](v2-oauth2-auth-code-flow.md), em que é chamado de "fluxo híbrido" – recuperando o token de ID na solicitação/Authorize junto com um código de autorização.

[!INCLUDE [suggest-msal-from-protocols](includes/suggest-msal-from-protocols.md)]

## <a name="prefer-the-auth-code-flow"></a>Prefira o fluxo do código de autenticação

Com os planos para [cookies de terceiros a serem removidos dos navegadores](reference-third-party-cookies-spas.md), o **fluxo de concessão implícita não é mais um método de autenticação adequado**.  Os [recursos de SSO silencioso](#getting-access-tokens-silently-in-the-background) do fluxo implícito não funcionam sem cookies de terceiros, fazendo com que os aplicativos sejam interrompidos quando tentam obter um novo token. É altamente recomendável que todos os novos aplicativos usem o [fluxo de código de autorização](v2-oauth2-auth-code-flow.md) que agora dá suporte a aplicativos de página única no lugar do fluxo implícito, e que os aplicativos de [página única existentes também comecem a migrar para o fluxo do código de autorização](migrate-spa-implicit-to-auth-code.md) .

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>Cenários adequados para a concessão implícita OAuth2

A concessão implícita é apenas confiável para a parte inicial e interativa do seu fluxo de entrada, em que a falta de [cookies](reference-third-party-cookies-spas.md) de terceiros não pode afetar seu aplicativo. Essa limitação significa que você deve usá-la exclusivamente como parte do fluxo híbrido, em que seu aplicativo solicita um código, bem como um token do ponto de extremidade de autorização. Isso garante que seu aplicativo receba um código que possa ser resgatado para um token de atualização, garantindo assim que a sessão de logon do aplicativo permaneça válida ao longo do tempo.

## <a name="protocol-diagram"></a>Diagrama do protocolo

O diagrama a seguir mostra a aparência de todo o fluxo de entrada implícito e as seções a seguir descrevem cada etapa em mais detalhes.

![Diagrama mostrando o fluxo de entrada implícito](./media/v2-oauth2-implicit-grant-flow/convergence-scenarios-implicit.svg)

## <a name="send-the-sign-in-request"></a>Enviar a solicitação de conexão

Para conectar inicialmente o usuário em seu aplicativo, você pode enviar uma solicitação de autenticação do [OpenID Connect](v2-protocols-oidc.md) e obter uma `id_token` da plataforma de identidade da Microsoft.

> [!IMPORTANT]
> Para solicitar um token de ID e/ou um token de acesso com êxito, o registro do aplicativo na página de [portal do Azure registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) deve ter o fluxo de concessão implícito correspondente habilitado, selecionando **tokens de ID** e **tokens de acesso** na seção **concessão implícita e fluxos híbridos** . Se não estiver habilitado, um `unsupported_response` erro será retornado: `The provided value for the input parameter 'response_type' is not allowed for this client. Expected value is 'code'`

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=openid
&response_mode=fragment
&state=12345
&nonce=678910
```

> [!TIP]
> Para testar a entrada usando o fluxo implícito, clique em <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank"> https://login.microsoftonline.com/common/oauth2/v2.0/authorize.. .</a> Depois de entrar, seu navegador deve ser redirecionado para `https://localhost/myapp/` com um `id_token` na barra de endereços.
>

| Parâmetro | Type | Descrição |
| --- | --- | --- |
| `tenant` | obrigatório |O valor `{tenant}` no caminho da solicitação pode ser usado para controlar quem pode entrar no aplicativo. Os valores permitidos são `common`, `organizations`, `consumers` e identificadores de locatário. Para obter mais detalhes, consulte [noções básicas de protocolo](active-directory-v2-protocols.md#endpoints). |
| `client_id` | obrigatório | A ID do aplicativo (cliente) que a página [Portal do Azure - Registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) atribuiu ao seu aplicativo. |
| `response_type` | exigido |Deve incluir `id_token` para conexão do OpenID Connect. Também pode incluir o response_type `token`. O uso de `token` aqui permitirá que seu aplicativo receba imediatamente um token de acesso do ponto de extremidade de autorização sem precisar fazer uma segunda solicitação ao ponto de extremidade de autorização. Se você usar o `token` response_type, o `scope` parâmetro deverá conter um escopo que indica para qual recurso emitir o token (por exemplo, User. read on Microsoft Graph). Ele também pode conter `code` no lugar de `token` fornecer um código de autorização para uso no fluxo do [código de autorização](v2-oauth2-auth-code-flow.md). Essa resposta id_token + Code às vezes é chamada de fluxo híbrido.  |
| `redirect_uri` | recomendável |O redirect_uri do seu aplicativo, onde as respostas de autenticação podem ser enviadas e recebidas pelo aplicativo. Ele deve corresponder exatamente a um dos redirect_uris que você registrou no portal, com exceção de que ele deve ser codificado por url. |
| `scope` | exigido |Uma lista de [escopos](v2-permissions-and-consent.md)separados por espaços. Para o OpenID Connect (id_tokens), ele deve incluir o escopo `openid` , que se traduz na permissão "entrar" na interface do usuário de consentimento. Opcionalmente, talvez você também queira incluir os `email` `profile` escopos e para obter acesso a dados de usuário adicionais. Você também pode incluir outros escopos nesta solicitação para solicitar o consentimento para vários recursos, se um token de acesso for solicitado. |
| `response_mode` | opcionais |Especifica o método que deve ser usado para enviar o token resultante de volta ao seu aplicativo. O padrão é consultar apenas um token de acesso, mas fragmentar se a solicitação incluir uma id_token. |
| `state` | recomendável |Um valor incluído na solicitação também será retornado na resposta do token. Pode ser uma cadeia de caracteres de qualquer conteúdo desejado. Um valor exclusivo gerado aleatoriamente normalmente é usado para [impedir ataques de solicitação intersite forjada](https://tools.ietf.org/html/rfc6749#section-10.12). O estado também é usado para codificar informações sobre o estado do usuário no aplicativo antes que a solicitação de autenticação ocorra, como a página ou a exibição em que ele estava. |
| `nonce` | necessárias |Um valor incluído na solicitação, gerado pelo aplicativo, que será incluído no id_token resultante como uma declaração. O aplicativo pode, então, verificar esse valor para atenuar os ataques de reprodução de token. O valor normalmente é uma cadeia de caracteres aleatória e exclusiva que pode ser usada para identificar a origem da solicitação. Obrigatório somente quando um id_token é solicitado. |
| `prompt` | opcionais |Indica o tipo de interação do usuário que é necessário. Os únicos valores válidos no momento são 'login', 'none', 'select_account' e 'consent'. `prompt=login` forçará o usuário a inserir suas credenciais na solicitação, negando o logon único. `prompt=none` é o oposto: ele garantirá que o usuário não seja apresentado a nenhum prompt interativo. Se a solicitação não puder ser concluída silenciosamente por meio de logon único, a plataforma de identidade da Microsoft retornará um erro. `prompt=select_account` envia o usuário para um seletor de conta em que todas as contas lembradas na sessão serão exibidas. `prompt=consent` irá disparar a caixa de diálogo de consentimento do OAuth depois que o usuário iniciar a sessão, solicitando que ele conceda permissões ao aplicativo. |
| `login_hint`  |opcionais |Pode ser usado para preencher previamente o campo de nome de usuário/endereço de email da página de entrada do usuário, caso você saiba o nome de usuário com antecedência. Com frequência, os aplicativos usarão esse parâmetro durante a reautenticação, já tendo extraído o nome de usuário de uma entrada anterior usando a `preferred_username` declaração.|
| `domain_hint` | opcionais |Se for incluído, ele ignorará o processo de descoberta baseado em email que o usuário passa na página de entrada, levando a uma experiência de usuário um pouco mais simplificada. Esse parâmetro é usado normalmente para aplicativos de linha de negócios que operam em um único locatário, no qual eles fornecerão um nome de domínio dentro de um determinado locatário, encaminhando o usuário para o provedor de Federação para esse locatário.  Observe que essa dica impede que os convidados entrem neste aplicativo e limita o uso de credenciais de nuvem como FIDO.  |

Neste ponto, o usuário será solicitado a inserir suas credenciais e concluir a autenticação. A plataforma Microsoft Identity também garantirá que o usuário tenha consentido as permissões indicadas no parâmetro de `scope` consulta. Se o usuário não consentir a **nenhuma** dessas permissões, ele será solicitado a consentir às permissões necessárias. Para obter mais informações, consulte [permissões, consentimento e aplicativos multilocatários](v2-permissions-and-consent.md).

Depois que o usuário autenticar e conceder consentimento, a plataforma de identidade da Microsoft retornará uma resposta ao seu aplicativo no indicado `redirect_uri` , usando o método especificado no `response_mode` parâmetro.

#### <a name="successful-response"></a>Resposta bem-sucedida

Uma resposta bem-sucedida usando `response_mode=fragment` e `response_type=id_token+code` é semelhante ao seguinte (com quebras de linha para legibilidade):

```HTTP
GET https://localhost/myapp/#
code=0.AgAAktYV-sfpYESnQynylW_UKZmH-C9y_G1A
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| Parâmetro | Descrição |
| --- | --- |
| `code` | Incluído se `response_type` incluir `code`. Este é um código de autorização adequado para uso no [fluxo de código de autorização](v2-oauth2-auth-code-flow.md).  |
| `access_token` |Incluído se `response_type` incluir `token`. O token de acesso que o aplicativo solicitou. O token de acesso não deve ser decodificado ou inspecionado de outra forma, ele deve ser tratado como uma cadeia de caracteres opaca. |
| `token_type` |Incluído se `response_type` incluir `token`. Sempre será `Bearer`. |
| `expires_in`|Incluído se `response_type` incluir `token`. Indica o número de segundos pelos quais o token é válido para fins de cache. |
| `scope` |Incluído se `response_type` incluir `token`. Indica os escopos para os quais o access_token será válido. Pode não incluir todos os escopos solicitados, se eles não forem aplicáveis ao usuário (no caso de escopos somente AD do Azure serem solicitados quando uma conta pessoal for usada para fazer logon). |
| `id_token` | Um JWT (JSON Web Token) assinado. O aplicativo pode decodificar os segmentos desse token para solicitar informações sobre o usuário que fez login. O aplicativo pode armazenar em cache os valores e exibi-los, mas não deve depender deles para qualquer autorização ou limites de segurança. Para obter mais informações sobre id_tokens, veja a [`id_token reference`](id-tokens.md). <br> **Observação:** Fornecido somente se o `openid` escopo foi solicitado e `response_type` incluído `id_tokens` . |
| `state` |Se um parâmetro de estado for incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores de estado na solicitação e na resposta são idênticos. |

#### <a name="error-response"></a>Resposta de erro

As respostas de erro também podem ser enviadas ao `redirect_uri` para que o aplicativo possa tratá-las adequadamente:

```HTTP
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parâmetro | Descrição |
| --- | --- |
| `error` |Uma cadeia de caracteres de códigos de erro que pode ser usada para classificar tipos de erro que ocorrem e pode ser usada para responder aos erros. |
| `error_description` |Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro de autenticação. |

## <a name="getting-access-tokens-silently-in-the-background"></a>Obtendo tokens de acesso silenciosamente em segundo plano

> [!Important]
> Essa parte do fluxo implícito provavelmente não funcionará para seu aplicativo, pois é usada em diferentes navegadores devido à [remoção de cookies de terceiros por padrão](reference-third-party-cookies-spas.md).  Embora isso ainda funcione atualmente em navegadores baseados em Chromium que não estão em Incognito, os desenvolvedores devem reconsiderar o uso dessa parte do fluxo. Em navegadores que não dão suporte a cookies de terceiros, você receberá um erro indicando que nenhum usuário está conectado, pois os cookies de sessão da página de logon foram removidos pelo navegador. 

Agora que você assinou o usuário em seu aplicativo de página única, você pode obter silenciosamente tokens de acesso para chamar APIs da Web protegidas pela plataforma de identidade da Microsoft, como o [Microsoft Graph](https://developer.microsoft.com/graph). Mesmo se já tiver recebido um token usando o response_type `token`, você poderá usar esse método para adquirir tokens para recursos adicionais sem precisar redirecionar o usuário para entrar novamente.

No fluxo normal do OpenID Connect/OAuth, você faria isso fazendo uma solicitação para o ponto de extremidade da plataforma de identidade da Microsoft `/token` . Você pode fazer a solicitação em um iframe oculto para obter novos tokens para outras APIs da Web:

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&response_mode=fragment
&state=12345
&nonce=678910
&prompt=none
&login_hint=myuser@mycompany.com
```

Para obter detalhes sobre os parâmetros de consulta na URL, consulte [enviar a solicitação de entrada](#send-the-sign-in-request).

> [!TIP]
> Tente copiar e colar a solicitação abaixo em uma guia do navegador! (Não se esqueça de substituir os valores `login_hint` pelos valores corretos para o seu usuário)
>
>`https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&login_hint={your-username}`
>
> Observe que isso funcionará mesmo em navegadores sem suporte a cookies de terceiros, já que você está inserindo isso diretamente em uma barra de navegador, em vez de abri-lo em um iframe. 

Graças ao parâmetro `prompt=none` , essa solicitação terá êxito ou falhará imediatamente e retornará ao seu aplicativo. A resposta será enviada para seu aplicativo no indicado `redirect_uri` , usando o método especificado no `response_mode` parâmetro.

#### <a name="successful-response"></a>Resposta bem-sucedida

Uma resposta bem-sucedida usando `response_mode=fragment` tem a seguinte aparência:

```HTTP
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fdirectory.read
```

| Parâmetro | Descrição |
| --- | --- |
| `access_token` |Incluído se `response_type` incluir `token`. O token de acesso solicitado pelo aplicativo, nesse caso para o Microsoft Graph. O token de acesso não deve ser decodificado ou inspecionado de outra forma, ele deve ser tratado como uma cadeia de caracteres opaca. |
| `token_type` | Sempre será `Bearer`. |
| `expires_in` | Indica o número de segundos pelos quais o token é válido para fins de cache. |
| `scope` | Indica os escopos para os quais o access_token será válido. Pode não incluir todos os escopos solicitados, se eles não forem aplicáveis ao usuário (no caso de escopos somente AD do Azure serem solicitados quando uma conta pessoal for usada para fazer logon). |
| `id_token` | Um JWT (JSON Web Token) assinado. Incluído se `response_type` incluir `id_token`. O aplicativo pode decodificar os segmentos desse token para solicitar informações sobre o usuário que fez login. O aplicativo pode armazenar em cache os valores e exibi-los, mas não deve depender deles para qualquer autorização ou limites de segurança. Para obter mais informações sobre id_tokens, consulte a [ `id_token` referência](id-tokens.md). <br> **Observação:** Somente fornecido se o escopo `openid` for solicitado. |
| `state` |Se um parâmetro de estado for incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores de estado na solicitação e na resposta são idênticos. |

#### <a name="error-response"></a>Resposta de erro

As respostas de erro também podem ser enviadas ao `redirect_uri` para que o aplicativo possa tratá-las adequadamente. No caso de `prompt=none`, um erro esperado será:

```HTTP
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parâmetro | Descrição |
| --- | --- |
| `error` |Uma cadeia de caracteres de códigos de erro que pode ser usada para classificar tipos de erro que ocorrem e pode ser usada para responder aos erros. |
| `error_description` |Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro de autenticação. |

Se você receber esse erro na solicitação do iframe, o usuário deverá entrar novamente de forma interativa para recuperar um novo token. Você pode escolher lidar com isso da maneira que fizer mais sentido para seu aplicativo.

## <a name="refreshing-tokens"></a>Atualizando tokens

A concessão implícita não fornece tokens de atualização. `id_token`s e `access_token`s expirarão após um curto período. Portanto, seu aplicativo deve estar preparado para atualizar esses tokens periodicamente. Para atualizar qualquer tipo de token, você pode executar a mesma solicitação de iframe oculto acima usando o `prompt=none` parâmetro para controlar o comportamento da plataforma de identidade. Se você quiser receber um novo, certifique-se de `id_token` usar `id_token` o no `response_type` e o `scope=openid` , bem como um `nonce` parâmetro.

Em navegadores que não dão suporte a cookies de terceiros, isso resultará em um erro indicando que nenhum usuário está conectado. 

## <a name="send-a-sign-out-request"></a>Enviar uma solicitação de desconexão

O OpenID Connect `end_session_endpoint` permite que seu aplicativo envie uma solicitação para a plataforma de identidade da Microsoft para encerrar a sessão de um usuário e limpar os cookies definidos pela plataforma de identidade da Microsoft. Para desconectar por completo um usuário de um aplicativo Web, seu aplicativo deve encerrar sua própria sessão com o usuário (normalmente, limpando o cache de token ou removendo cookies) e, depois, redirecionar o navegador para:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| Parâmetro | Type | Descrição |
| --- | --- | --- |
| `tenant` |obrigatório |O valor `{tenant}` no caminho da solicitação pode ser usado para controlar quem pode entrar no aplicativo. Os valores permitidos são `common`, `organizations`, `consumers` e identificadores de locatário. Para obter mais detalhes, consulte [noções básicas de protocolo](active-directory-v2-protocols.md#endpoints). |
| `post_logout_redirect_uri` | recomendável | A URL para a qual o usuário deve retornar após a conclusão do logoff. Esse valor deve corresponder a um dos URIs de redirecionamento registrados no aplicativo. Se não estiver incluído, o usuário receberá uma mensagem genérica da plataforma Microsoft Identity. |

## <a name="next-steps"></a>Próximas etapas

* Percorra os [exemplos de MSAL JS](sample-v2-code.md) para começar a codificação.
* Examine o [fluxo do código de autorização](v2-oauth2-auth-code-flow.md) como uma alternativa mais nova e melhor para a concessão implícita. 
