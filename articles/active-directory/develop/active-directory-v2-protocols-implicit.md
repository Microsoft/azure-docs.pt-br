---
title: "Proteger aplicativos de única página usando o fluxo implícito de v 2.0 do Azure AD | Microsoft Docs"
description: "Compilando aplicativos Web usando a implementação v2.0 do Azure AD do fluxo implícito para aplicativos de página única."
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 3605931f-dc24-4910-bb50-5375defec6a8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 3bd8256814036a357b30b69286da6bb7c974162f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# Protocolos v2.0 - SPAs que usam o fluxo implícito
Com o ponto de extremidade v2.0, você pode autenticar usuários em seus aplicativos de página única com contas pessoais e corporativas/de estudante da Microsoft.  Aplicativos de página única e outros aplicativos JavaScript executados principalmente em um navegador enfrentam desafios interessantes quando o assunto é autenticação:

* As características de segurança desses aplicativos são consideravelmente diferentes dos aplicativos Web tradicionais baseados em servidor.
* Muitos servidores de autorização e provedores de identidade não dão suporte a solicitações CORS.
* Redirecionamentos para fora do aplicativo em páginas inteiras do navegador se tornam algo particularmente invasivo para a experiência do usuário.

Para esses aplicativos (por exemplo: AngularJS, Ember.js, React.js etc), o Azure AD oferece suporte ao Fluxo de concessão implícita do OAuth 2.0.  O fluxo está descrito na [Especificação do OAuth 2.0](http://tools.ietf.org/html/rfc6749#section-4.2).  O principal benefício é que ele permite que o aplicativo obtenha tokens do Azure AD sem executar uma troca de credenciais de servidor back-end.  Isso permite que o aplicativo autentique o usuário, mantenha a sessão e obtenha tokens para outras APIs da Web, tudo dentro do código JavaScript do cliente.  Há algumas considerações de segurança importantes que você deve levar em conta ao usar o fluxo implícito, especificamente sobre [cliente](http://tools.ietf.org/html/rfc6749#section-10.3) e [representação de usuário](http://tools.ietf.org/html/rfc6749#section-10.3).

Se você quiser usar o fluxo implícito e o Azure AD para adicionar autenticação ao seu aplicativo JavaScript, recomendamos nossa biblioteca JavaScript de código aberto, a [adal.js](https://github.com/AzureAD/azure-activedirectory-library-for-js).  Há alguns tutoriais sobre AngularJS [aqui](active-directory-appmodel-v2-overview.md#getting-started) para ajudá-lo a começar.  

No entanto, se você preferir não usar uma biblioteca em seu aplicativo de página única e enviar mensagens de protocolo por conta própria, siga as etapas gerais a seguir.

> [!NOTE]
> Nem todos os recursos e cenários do Azure Active Directory têm suporte no ponto de extremidade v2.0.  Para determinar se você deve usar o ponto de extremidade v2.0, leia sobre as [limitações da v2.0](active-directory-v2-limitations.md).
> 
> 

## Diagrama de protocolo
O fluxo completo de entrada implícita é semelhante a este. Cada uma das etapas são descritas em detalhes abaixo.

![Raias do OpenId Connect](../../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

## Enviar a solicitação de conexão
Para autenticar inicialmente o usuário em seu aplicativo, você pode enviar uma solicitação de autorização [OpenID Connect](active-directory-v2-protocols-oidc.md) e obter um `id_token` do ponto de extremidade v 2.0:

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token+token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=openid%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&response_mode=fragment
&state=12345
&nonce=678910
```

> [!TIP]
> Clique no link a seguir para executar essa solicitação! Depois de entrar, seu navegador deverá ser redirecionado para `https://localhost/myapp/` com um `id_token` na barra de endereços.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token+token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>
> 
> 

| Parâmetro |  | Descrição |
| --- | --- | --- |
| locatário |obrigatório |O valor `{tenant}` no caminho da solicitação pode ser usado para controlar quem pode entrar no aplicativo.  Os valores permitidos são `common`, `organizations`, `consumers` e identificadores de locatário.  Para obter mais detalhes, consulte [noções básicas de protocolo](active-directory-v2-protocols.md#endpoints). |
| client_id |obrigatório |A Id de Aplicativo que o portal de registro ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) atribuiu ao aplicativo. |
| response_type |obrigatório |Deve incluir `id_token` para conexão do OpenID Connect.  Também pode incluir o response_type `token`. O uso de `token` aqui permitirá que seu aplicativo receba imediatamente um token de acesso do ponto de extremidade de autorização sem precisar fazer uma segunda solicitação ao ponto de extremidade de autorização.  Se você usar o response_type de `token`, o parâmetro `scope` deverá conter um escopo indicando para quais recursos o token será emitido. |
| redirect_uri |recomendável |O redirect_uri do seu aplicativo, onde as respostas de autenticação podem ser enviadas e recebidas pelo aplicativo.  Ele deve corresponder exatamente a um dos redirect_uris que você registrou no portal, com exceção de que ele deve ser codificado por url. |
| scope |obrigatório |Uma lista de escopos separados por espaços.  Para o OpenID Connect, é necessário incluir o escopo `openid`, que é traduzido para a permissão "Fazer seu logon" na interface do usuário de consentimento.  Como opção, convém incluir[ escopos](active-directory-v2-scopes.md) `email`ou `profile` para obter acesso a dados adicionais do usuário.  Você também pode incluir outros escopos nessa solicitação a fim de solicitar o consentimento a vários recursos. |
| response_mode |recomendável |Especifica o método que deve ser usado para enviar o token resultante de volta ao aplicativo.  Deve ser `fragment` para o fluxo implícito. |
| state |recomendável |Um valor incluído na solicitação também será retornado na resposta do token.  Pode ser uma cadeia de caracteres de qualquer conteúdo desejado.  Um valor exclusivo gerado aleatoriamente normalmente é usado para [impedir ataques de solicitação intersite forjada](http://tools.ietf.org/html/rfc6749#section-10.12).  O estado também é usado para codificar as informações sobre o estado do usuário no aplicativo antes da solicitação de autenticação ocorrida, como a página ou exibição em que ele estava. |
| nonce |obrigatório |Um valor incluído na solicitação, gerado pelo aplicativo, que será incluído no id_token resultante como uma declaração.  O aplicativo pode verificar esse valor para reduzir os ataques de reprodução de token.  Normalmente, o valor é uma cadeia de caracteres aleatória e exclusiva que pode ser usada para identificar a origem da solicitação. |
| prompt |opcional |Indica o tipo de interação do usuário que é necessário.  Os únicos valores válidos no momento são 'login', 'none' e 'consent'.  `prompt=login` forçará o usuário a inserir suas credenciais na solicitação, negando o logon único.  `prompt=none` é o oposto - ele garantirá que o usuário não seja apresentado a nenhum prompt interativo.  Se a solicitação não puder ser concluída silenciosamente por meio de logon único, o ponto de extremidade v2.0 retornará um erro.  `prompt=consent` irá disparar a caixa de diálogo de consentimento do OAuth depois que o usuário iniciar a sessão, solicitando que ele conceda permissões ao aplicativo. |
| login_hint |opcional |Pode ser usado para preencher previamente o campo de nome de usuário/endereço de email da página de entrada do usuário, se você souber o nome de usuário com antecedência.  Geralmente, os aplicativos usarão esse parâmetro durante a reautenticação, após já terem extraído o nome de usuário de uma entrada anterior usando a declaração `preferred_username`. |
| domain_hint |opcional |Pode ser `consumers` ou `organizations`.  Se for incluído, ele ignorará o processo de descoberta baseada em email que o usuário passa na página de entrada v 2.0, resultando em uma experiência de usuário um pouco mais simples.  Geralmente, os aplicativos usarão esse parâmetro durante a reautenticação, extraindo a declaração `tid` do id_token.  Se o valor da declaração `tid` for `9188040d-6c67-4c5b-b112-36a304b66dad`, você deverá usar `domain_hint=consumers`.  Caso contrário, use `domain_hint=organizations`. |

Nesse ponto, será solicitado que o usuário insira suas credenciais e conclua a autenticação.  O ponto de extremidade v2.0 também garantirá que o usuário tenha consentido as permissões indicadas no parâmetro de consulta `scope` .  Se o usuário não tiver consentido nenhuma dessas permissões, ele será solicitado a consentir as permissões necessárias.  Os detalhes dos aplicativos quanto a [permissões, consentimento e multilocatário são fornecidos aqui](active-directory-v2-scopes.md).

Depois que o usuário se autentica e dá consentimento, o ponto de extremidade v2.0 retorna uma resposta ao aplicativo no `redirect_uri` indicado usando o método especificado no parâmetro `response_mode`.

#### Resposta bem-sucedida
Uma resposta bem-sucedida usando `response_mode=fragment` e `response_type=id_token+token` é semelhante ao seguinte, com quebras de linha para legibilidade:

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope=https%3a%2f%2fgraph.microsoft.com%2fmail.read 
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| Parâmetro | Descrição |
| --- | --- |
| access_token |Incluído se `response_type` incluir `token`. O token de acesso solicitado pelo aplicativo, nesse caso para o Microsoft Graph.  O token de acesso não deve ser decodificado ou inspecionado de outra forma, ele pode ser tratado como uma cadeia de caracteres opaca. |
| token_type |Incluído se `response_type` incluir `token`.  Sempre será `Bearer`. |
| expires_in |Incluído se `response_type` incluir `token`.  Indica o número de segundos durante os quais o token é válido, para fins de caching. |
| scope |Incluído se `response_type` incluir `token`.  Indica os escopos para os quais access_token será válido. |
| id_token |O id_token que o aplicativo solicitou. Você pode usar o id_token para verificar a identidade do usuário e iniciar uma sessão com o usuário.  Mais detalhes sobre id_tokens e o respectivo conteúdo estão incluídos na [referência ao token do ponto de extremidade v2.0](active-directory-v2-tokens.md). |
| state |Se um parâmetro de estado estiver incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores de estado na solicitação e na resposta são idênticos. |

#### Resposta de erro
As respostas de erro também podem ser enviadas ao `redirect_uri` para que o aplicativo possa tratá-las adequadamente:

```
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| . | Descrição |
| --- | --- |
| error |Uma cadeia de caracteres de códigos de erro que pode ser usada para classificar tipos de erro que ocorrem e pode ser usada para responder aos erros. |
| error_description |Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro de autenticação. |

## Validar o id_token
Apenas receber o id_token não é suficiente para autenticar o usuário; você deve validar a assinatura do id_token e verificar as declarações no token de acordo com os requisitos do aplicativo.  O ponto de extremidade v2.0 usa [JWTs (Tokens Web JSON)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) e criptografia de chave pública para assinar tokens e verificar se eles são válidos.

Você pode escolher validar o `id_token` no código do cliente, mas uma prática comum é enviar o `id_token` para um servidor de back-end e executar a validação nele.  Após a validação da assinatura do id_token, será necessário verificar algumas declarações:  Consulte a [referência do token v2.0](active-directory-v2-tokens.md) para obter mais informações, incluindo [Validando tokens](active-directory-v2-tokens.md#validating-tokens) e [Informações importantes sobre substituição de chave de assinatura](active-directory-v2-tokens.md#validating-tokens).  Há, pelo menos, uma disponível para a maioria das linguagens e plataformas.
<!--TODO: Improve the information on this-->

Talvez você também queira validar declarações adicionais, dependendo do cenário.  Algumas validações comuns incluem:

* Garantir que o usuário/organização tenha assinado para usar o aplicativo.
* Garantir que o usuário tenha autorização/privilégios adequados.
* Garantir que uma determinada intensidade de autenticação tenha ocorrido, como autenticação multifator.

Para saber mais sobre declarações em um id_token, consulte a [referência do token do ponto de extremidade v2.0](active-directory-v2-tokens.md).

Depois de ter validado completamente o id_token, você poderá iniciar uma sessão com o usuário e usar declarações no id_token para obter informações sobre o usuário no seu aplicativo.  Essas informações podem ser usadas para exibição, registros, autorizações, etc.

## Obter tokens de acesso
Agora que você autenticou o usuário em seu aplicativo de página única, pode obter tokens de acesso para chamar APIs da Web protegidas pelo Azure AD, como o [Microsoft Graph](https://graph.microsoft.io).  Mesmo se já tiver recebido um token usando o response_type `token`, você poderá usar esse método para adquirir tokens para recursos adicionais sem precisar redirecionar o usuário para entrar novamente.

No fluxo normal de OpenID Connect/OAuth, você faria isso por meio de uma solicitação para o ponto de extremidade `/token` do v2.0.  No entanto, o ponto de extremidade v 2.0 não suporta solicitações CORS, portanto, fazer chamadas AJAX para obter e atualizar tokens está fora de cogitação.  Em vez disso, você pode usar o fluxo implícito em um iframe oculto para obter novos tokens para outras APIs da Web: 

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&response_mode=fragment
&state=12345&nonce=678910
&prompt=none
&domain_hint=organizations
&login_hint=myuser@mycompany.com
```

> [!TIP]
> Tente copiar e colar a solicitação abaixo em uma guia do navegador! (Não se esqueça de substituir os valores `domain_hint` e `login_hint` pelos valores corretos para o seu usuário)
> 
> 

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&domain_hint={{consumers-or-organizations}}&login_hint={{your-username}}
```

| Parâmetro |  | Descrição |
| --- | --- | --- |
| locatário |obrigatório |O valor `{tenant}` no caminho da solicitação pode ser usado para controlar quem pode entrar no aplicativo.  Os valores permitidos são `common`, `organizations`, `consumers` e identificadores de locatário.  Para obter mais detalhes, consulte [noções básicas de protocolo](active-directory-v2-protocols.md#endpoints). |
| client_id |obrigatório |A Id de Aplicativo que o portal de registro ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) atribuiu ao aplicativo. |
| response_type |obrigatório |Deve incluir `id_token` para conexão do OpenID Connect.  Também pode incluir outros response_types, como `code`. |
| redirect_uri |recomendável |O redirect_uri do seu aplicativo, onde as respostas de autenticação podem ser enviadas e recebidas pelo aplicativo.  Ele deve corresponder exatamente a um dos redirect_uris que você registrou no portal, com exceção de que ele deve ser codificado por url. |
| scope |obrigatório |Uma lista de escopos separados por espaços.  Para obter tokens, inclua todos os [escopos](active-directory-v2-scopes.md) necessários para o recurso de seu interesse. |
| response_mode |recomendável |Especifica o método que deve ser usado para enviar o token resultante de volta ao aplicativo.  Pode ser `query`, `form_post` ou `fragment`. |
| state |recomendável |Um valor incluído na solicitação também será retornado na resposta do token.  Pode ser uma cadeia de caracteres de qualquer conteúdo desejado.  Um valor exclusivo gerado aleatoriamente que normalmente é usado para impedir ataques de solicitação intersite forjada.  O estado também é usado para codificar as informações sobre o estado do usuário no aplicativo antes da solicitação de autenticação ocorrida, como a página ou exibição em que ele estava. |
| nonce |obrigatório |Um valor incluído na solicitação, gerado pelo aplicativo, que será incluído no id_token resultante como uma declaração.  O aplicativo pode verificar esse valor para reduzir os ataques de reprodução de token.  Normalmente, o valor é uma cadeia de caracteres aleatória e exclusiva que pode ser usada para identificar a origem da solicitação. |
| prompt |obrigatório |Para atualizar e obter tokens em um iframe oculto, use o `prompt=none` para garantir que o iframe não pare de responder na página de entrada v2.0 e retorne imediatamente. |
| login_hint |obrigatório |Para atualizar e obter tokens em um iframe oculto, você deve incluir o nome de usuário nessa dica a fim de distinguir entre várias sessões que o usuário pode ter em um determinado momento. Você pode extrair o nome de usuário de uma entrada anterior usando a declaração `preferred_username`. |
| domain_hint |obrigatório |Pode ser `consumers` ou `organizations`.  Para atualizar e obter tokens em um iframe oculto, você deve incluir o domain_hint na solicitação.  Você deve extrair a declaração `tid` do id_token de uma entrada anterior para determinar qual valor usar.  Se o valor da declaração `tid` for `9188040d-6c67-4c5b-b112-36a304b66dad`, você deverá usar `domain_hint=consumers`.  Caso contrário, use `domain_hint=organizations`. |

Graças ao parâmetro `prompt=none` , essa solicitação terá êxito ou falhará imediatamente e retornará ao seu aplicativo.  Uma resposta bem-sucedida será enviada ao seu aplicativo no `redirect_uri` indicado, usando o método especificado no parâmetro `response_mode`.

#### Resposta bem-sucedida
Uma resposta bem-sucedida usando `response_mode=fragment` tem a seguinte aparência:

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read
```

| . | Descrição |
| --- | --- |
| access_token |O token solicitado pelo aplicativo. |
| token_type |Sempre será `Bearer`. |
| state |Se um parâmetro de estado estiver incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores de estado na solicitação e na resposta são idênticos. |
| expires_in |Por quanto tempo o token de acesso é válido (em segundos). |
| scope |Os escopos para os quais o access_token é válido. |

#### Resposta de erro
As respostas de erro também podem ser enviadas ao `redirect_uri` para que o aplicativo possa tratá-las adequadamente.  No caso de `prompt=none`, um erro esperado será:

```
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parâmetro | Descrição |
| --- | --- |
| error |Uma cadeia de caracteres de códigos de erro que pode ser usada para classificar tipos de erro que ocorrem e pode ser usada para responder aos erros. |
| error_description |Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro de autenticação. |

Se você receber esse erro na solicitação do iframe, o usuário deverá entrar novamente de forma interativa para recuperar um novo token.  Você pode escolher lidar com isso da maneira que fizer mais sentido para seu aplicativo.

## Atualizando tokens
`id_token`s e `access_token`s expirarão após um curto período. Portanto, seu aplicativo deve estar preparado para atualizar esses tokens periodicamente.  Para atualizar qualquer um desses tipos de tokens, você pode executar a mesma solicitação de iframe oculto mencionada acima usando o parâmetro `prompt=none` para controlar o comportamento do Azure AD.  Se você quiser receber um novo `id_token`, use `response_type=id_token` e `scope=openid`, bem como um parâmetro `nonce`.

## Enviar uma solicitação de desconexão
O `end_session_endpoint` do OpenIdConnect permite que o aplicativo envie uma solicitação ao ponto de extremidade v2.0 para encerrar uma sessão do usuário e limpar os cookies definidos pelo ponto de extremidade v2.0.  Para desconectar por completo um usuário de um aplicativo Web, seu aplicativo deve encerrar sua própria sessão com o usuário (normalmente, limpando o cache de token ou removendo cookies) e, depois, redirecionar o navegador para:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| Parâmetro |  | Descrição |
| --- | --- | --- |
| locatário |obrigatório |O valor `{tenant}` no caminho da solicitação pode ser usado para controlar quem pode entrar no aplicativo.  Os valores permitidos são `common`, `organizations`, `consumers` e identificadores de locatário.  Para obter mais detalhes, consulte [noções básicas de protocolo](active-directory-v2-protocols.md#endpoints). |
| post_logout_redirect_uri | recomendável | A URL para a qual o usuário deve retornar após a conclusão do logoff. Esse valor deve corresponder a um dos URIs de redirecionamento registrados no aplicativo. Se ele não estiver incluído, o usuário verá uma mensagem genérica do ponto de extremidade v2.0. |
