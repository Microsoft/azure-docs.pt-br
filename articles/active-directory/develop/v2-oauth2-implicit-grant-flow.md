---
title: Proteger aplicativos de página única usando o fluxo implícito do Microsoft identity platform | Azure
description: Criação de aplicativos web usando a implementação de plataforma de identidade Microsoft o fluxo implícito para aplicativos de página única.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 3605931f-dc24-4910-bb50-5375defec6a8
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2e6ac72a91ae14b6f9c513c84da6f1f06508caef
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67482215"
---
# <a name="microsoft-identity-platform-and-implicit-grant-flow"></a>Fluxo de concessão de plataforma de identidade da Microsoft e implícitas

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Com o ponto de extremidade de plataforma identidade Microsoft, você pode autenticar usuários em seus aplicativos de página única com contas tanto pessoais e corporativas ou de estudante da Microsoft. Aplicativos de página única e outros aplicativos JavaScript executados principalmente em um navegador enfrentam desafios interessantes quando o assunto é autenticação:

* As características de segurança desses aplicativos são consideravelmente diferentes dos aplicativos Web tradicionais baseados em servidor.
* Muitos servidores de autorização e provedores de identidade não são compatíveis com solicitações CORS.
* Redirecionamentos para fora do aplicativo em páginas inteiras do navegador se tornam algo particularmente invasivo para a experiência do usuário.

Para esses aplicativos (AngularJS, ember, React. js e assim por diante), a plataforma de identidade da Microsoft suporta o fluxo de concessão implícita do OAuth 2.0. O fluxo está descrito na [Especificação do OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.2). O principal benefício é que ele permite que o aplicativo obter tokens de plataforma de identidade da Microsoft sem executar um servidor de back-end exchange de credencial. Isso permite que o aplicativo autentique o usuário, mantenha a sessão e obtenha tokens para outras APIs da Web, tudo dentro do código JavaScript do cliente. Há algumas considerações de segurança importantes que você deve levar em conta ao usar o fluxo implícito, especificamente sobre [cliente](https://tools.ietf.org/html/rfc6749#section-10.3) e [representação de usuário](https://tools.ietf.org/html/rfc6749#section-10.3).

Se você quiser usar o fluxo implícito e plataforma de identidade da Microsoft para adicionar autenticação ao seu aplicativo JavaScript, é recomendável que você use a biblioteca JavaScript de código-fonte aberto, [msal](https://github.com/AzureAD/microsoft-authentication-library-for-js).

No entanto, se você preferir não usar uma biblioteca em seu aplicativo de página única e enviar mensagens de protocolo por conta própria, siga as etapas gerais a seguir.

> [!NOTE]
> Nem todos os cenários do Azure Active Directory (Azure AD) e recursos são compatíveis com o ponto de extremidade de plataforma do Microsoft identity. Para determinar se deve usar o ponto de extremidade de plataforma do Microsoft identity, leia sobre [limitações da plataforma Microsoft identity](active-directory-v2-limitations.md).

## <a name="protocol-diagram"></a>Diagrama de protocolo

O diagrama a seguir mostra a aparência de todo fluxo de entrada implícita e as seções a seguir descrevem cada etapa em mais detalhes.

![Diagrama mostrando o fluxo de entrada implícito](./media/v2-oauth2-implicit-grant-flow/convergence-scenarios-implicit.svg)

## <a name="send-the-sign-in-request"></a>Enviar a solicitação de conexão

Para assinar inicialmente o usuário em seu aplicativo, você pode enviar uma [OpenID Connect](v2-protocols-oidc.md) solicitação de autenticação e obter um `id_token` do ponto de extremidade de plataforma de identidade Microsoft.

> [!IMPORTANT]
> Para solicitar com êxito um token de ID, o registro do aplicativo na [portal do Azure - registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) página deverá ter o fluxo de concessão implícita habilitado corretamente, selecionando **tokens de acesso** e **Tokens de ID** sob o **concessão implícita** seção. Se não estiver habilitado, um `unsupported_response` erro será retornado: **O valor fornecido para o parâmetro de entrada 'response_type' não é permitido para este cliente. O valor esperado é 'code'**

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
> Para testar a entrada usando o fluxo implícito, clique em <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a> Depois de entrar, seu navegador deverá ser redirecionado para `https://localhost/myapp/` com um `id_token` na barra de endereços.
>

| Parâmetro |  | DESCRIÇÃO |
| --- | --- | --- |
| `tenant` | obrigatório |O valor `{tenant}` no caminho da solicitação pode ser usado para controlar quem pode entrar no aplicativo. Os valores permitidos são `common`, `organizations`, `consumers` e identificadores de locatário. Para obter mais detalhes, consulte [noções básicas de protocolo](active-directory-v2-protocols.md#endpoints). |
| `client_id` | obrigatório | ID do aplicativo (cliente) que o [portal do Azure - registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) atribuída ao seu aplicativo de página. |
| `response_type` | obrigatório |Deve incluir `id_token` para conexão do OpenID Connect. Também pode incluir o response_type `token`. O uso de `token` aqui permitirá que seu aplicativo receba imediatamente um token de acesso do ponto de extremidade de autorização sem precisar fazer uma segunda solicitação ao ponto de extremidade de autorização. Se você usar o response_type de `token`, o parâmetro `scope` deverá conter um escopo indicando para quais recursos o token será emitido. |
| `redirect_uri` | recomendável |O redirect_uri do seu aplicativo, onde as respostas de autenticação podem ser enviadas e recebidas pelo aplicativo. Ele deve corresponder exatamente a um dos redirect_uris que você registrou no portal, com exceção de que ele deve ser codificado por url. |
| `scope` | obrigatório |Uma lista de [escopos](v2-permissions-and-consent.md) separados por espaços. Para o OpenID Connect, é necessário incluir o escopo `openid`, que é traduzido para a permissão "Fazer seu logon" na interface do usuário de consentimento. Como opção, convém incluir os escopos `email` ou `profile` para obter acesso a dados adicionais do usuário. Você também pode incluir outros escopos nessa solicitação a fim de solicitar o consentimento a vários recursos. |
| `response_mode` | opcional |Especifica o método que deve ser usado para enviar o token resultante de volta ao aplicativo. O padrão é consulta de um token de acesso, mas um fragmento caso a solicitação inclua um id_token. |
| `state` | recomendável |Um valor incluído na solicitação também será retornado na resposta do token. Pode ser uma cadeia de caracteres de qualquer conteúdo desejado. Um valor exclusivo gerado aleatoriamente normalmente é usado para [impedir ataques de solicitação intersite forjada](https://tools.ietf.org/html/rfc6749#section-10.12). O estado também é usado para codificar as informações sobre o estado do usuário no aplicativo antes da solicitação de autenticação ocorrida, como a página ou exibição em que ele estava. |
| `nonce` | obrigatório |Um valor incluído na solicitação, gerado pelo aplicativo, que será incluído no id_token resultante como uma declaração. O aplicativo pode verificar esse valor para reduzir os ataques de reprodução de token. Normalmente, o valor é uma cadeia de caracteres aleatória e exclusiva que pode ser usada para identificar a origem da solicitação. Necessário somente quando um id_token é solicitado. |
| `prompt` | opcional |Indica o tipo de interação do usuário que é necessário. Os únicos valores válidos no momento são 'login', 'none', 'select_account' e 'consent'. `prompt=login` forçará o usuário a inserir suas credenciais na solicitação, negando o logon único. `prompt=none` é o oposto - ele garantirá que o usuário não é apresentado a nenhum prompt interativo. Se a solicitação não pode ser concluída silenciosamente por meio de logon único, o ponto de extremidade de plataforma do Microsoft identity retornará um erro. `prompt=select_account` envia o usuário para um seletor de conta em que todas as contas lembradas na sessão serão exibidas. `prompt=consent` irá disparar a caixa de diálogo de consentimento do OAuth depois que o usuário iniciar a sessão, solicitando que ele conceda permissões ao aplicativo. |
| `login_hint`  |opcional |Pode ser usado para preencher previamente o campo de nome de usuário/endereço de email da página de entrada do usuário, se você souber o nome de usuário com antecedência. Geralmente, os aplicativos usarão esse parâmetro durante a reautenticação, após já terem extraído o nome de usuário de uma entrada anterior usando a declaração `preferred_username` .|
| `domain_hint` | opcional |Pode ser `consumers` ou `organizations`. Se for incluído, ele ignorará o processo de descoberta baseada em email que o usuário passa na página, a entrada resultando em uma experiência de usuário um pouco mais simples. Geralmente, os aplicativos usarão esse parâmetro durante a reautenticação, extraindo a declaração `tid` do id_token. Se o `tid` é de valor de declaração `9188040d-6c67-4c5b-b112-36a304b66dad` (o Microsoft Account locatário do consumidor), você deve usar `domain_hint=consumers`. Caso contrário, você pode usar `domain_hint=organizations` durante a reautenticação. |

Nesse ponto, será solicitado que o usuário insira suas credenciais e conclua a autenticação. O ponto de extremidade de plataforma do Microsoft identity também garantirá que o usuário consentiu as permissões indicadas no `scope` parâmetro de consulta. Se o usuário não consentir a **nenhuma** dessas permissões, ele será solicitado a consentir às permissões necessárias. Para obter mais informações, consulte [permissões, consentimento e aplicativos multilocatários](v2-permissions-and-consent.md).

Depois que o usuário se autentica e dá consentimento, o ponto de extremidade de plataforma do Microsoft identity retornará uma resposta ao seu aplicativo no indicado `redirect_uri`, usando o método especificado no `response_mode` parâmetro.

#### <a name="successful-response"></a>Resposta bem-sucedida

Uma resposta bem-sucedida usando `response_mode=fragment` e `response_type=id_token+token` é semelhante ao seguinte (com quebras de linha para legibilidade):

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope=https%3a%2f%2fgraph.microsoft.com%2fuser.read 
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| Parâmetro | DESCRIÇÃO |
| --- | --- |
| `access_token` |Incluído se `response_type` incluir `token`. O token de acesso solicitado pelo aplicativo, nesse caso para o Microsoft Graph. O token de acesso não deve ser decodificado ou inspecionado de outra forma, ele deve ser tratado como uma cadeia de caracteres opaca. |
| `token_type` |Incluído se `response_type` incluir `token`. Sempre será `Bearer`. |
| `expires_in`|Incluído se `response_type` incluir `token`. Indica o número de segundos durante os quais o token é válido, para fins de caching. |
| `scope` |Incluído se `response_type` incluir `token`. Indica os escopos para os quais access_token será válido. Pode não incluir todos os escopos solicitados, se eles não eram aplicáveis ao usuário (no caso de escopos do Azure de somente do AD que está sendo solicitado quando uma conta pessoal é usada para fazer logon). |
| `id_token` | Um JWT (Token Web JSON) assinado. O aplicativo pode decodificar os segmentos desse token para solicitar informações sobre o usuário que fez login. O aplicativo pode armazenar em cache os valores e exibi-los, mas ele não deve depender deles para qualquer autorização ou limites de segurança. Para obter mais informações sobre id_tokens, veja a [`id_token reference`](id-tokens.md). <br> **Observação:** Somente fornecido se o escopo `openid` for solicitado. |
| `state` |Se um parâmetro de estado estiver incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores de estado na solicitação e na resposta são idênticos. |

#### <a name="error-response"></a>Resposta de erro

As respostas de erro também podem ser enviadas ao `redirect_uri` para que o aplicativo possa tratá-las adequadamente:

```
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parâmetro | DESCRIÇÃO |
| --- | --- |
| `error` |Uma cadeia de caracteres de códigos de erro que pode ser usada para classificar tipos de erro que ocorrem e pode ser usada para responder aos erros. |
| `error_description` |Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro de autenticação. |

## <a name="validate-the-idtoken"></a>Validar o id_token

Apenas receber o id_token não é suficiente para autenticar o usuário. Você também deve validar a assinatura do id_token e verificar as declarações no token com base nos requisitos do seu aplicativo. O ponto de extremidade de plataforma do Microsoft identity usa [Tokens Web JSON (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) e criptografia de chave pública para assinar tokens e verificar que eles são válidos.

Você pode escolher validar o `id_token` no código do cliente, mas uma prática comum é enviar o `id_token` para um servidor de back-end e executar a validação nele. Depois que você tiver validado a assinatura do id_token, há algumas declarações que você será solicitado a verificar. Consulte a [referência do `id_token`](id-tokens.md) para obter mais informações, incluindo [validação de tokens](id-tokens.md#validating-an-id_token) e [informações importantes sobre substituição de chave de assinatura](active-directory-signing-key-rollover.md). Há, pelo menos, uma disponível para a maioria das linguagens e plataformas.

Talvez você também queira validar declarações adicionais, dependendo do cenário. Algumas validações comuns incluem:

* Garantir que o usuário/organização tenha assinado para usar o aplicativo.
* Garantir que o usuário tenha autorização/privilégios adequados.
* Garantir que uma determinada intensidade de autenticação tenha ocorrido, como autenticação multifator.

Depois de ter validado o id_token, você pode iniciar uma sessão com o usuário e usar as declarações no id_token para obter informações sobre o usuário em seu aplicativo. Essas informações podem ser usadas para exibição, registros, personalização e muito mais.

## <a name="get-access-tokens"></a>Obter tokens de acesso

Agora que você autenticou o usuário em seu aplicativo de página única, você pode obter tokens de acesso para chamar APIs web protegidas por plataforma de identidade da Microsoft, como o [Microsoft Graph](https://developer.microsoft.com/graph). Mesmo se já tiver recebido um token usando o response_type `token`, você poderá usar esse método para adquirir tokens para recursos adicionais sem precisar redirecionar o usuário para entrar novamente.

No fluxo normal de OpenID Connect/OAuth, você faria isso fazendo uma solicitação para a plataforma de identidade Microsoft `/token` ponto de extremidade. No entanto, o ponto de extremidade de plataforma do Microsoft identity não suporta solicitações CORS, portanto, fazer chamadas AJAX para obter e atualizar tokens está fora de cogitação. Em vez disso, você pode usar o fluxo implícito em um iframe oculto para obter novos tokens para outras APIs da Web: 

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read 
&response_mode=fragment
&state=12345&nonce=678910
&prompt=none
&domain_hint=organizations
&login_hint=myuser@mycompany.com
```

Para obter detalhes sobre os parâmetros de consulta na URL, consulte [enviar a solicitação de entrada](#send-the-sign-in-request).

> [!TIP]
> Tente copiar e colar a solicitação abaixo em uma guia do navegador! (Não se esqueça de substituir os valores `login_hint` pelos valores corretos para o seu usuário)
>
>`https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2user.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&login_hint=your-username`
>

Graças ao parâmetro `prompt=none` , essa solicitação terá êxito ou falhará imediatamente e retornará ao seu aplicativo. Uma resposta bem-sucedida será enviada ao seu aplicativo no `redirect_uri` indicado, usando o método especificado no parâmetro `response_mode`.

#### <a name="successful-response"></a>Resposta bem-sucedida

Uma resposta bem-sucedida usando `response_mode=fragment` tem a seguinte aparência:

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read
```

| Parâmetro | DESCRIÇÃO |
| --- | --- |
| `access_token` |Incluído se `response_type` incluir `token`. O token de acesso solicitado pelo aplicativo, nesse caso para o Microsoft Graph. O token de acesso não deve ser decodificado ou inspecionado de outra forma, ele deve ser tratado como uma cadeia de caracteres opaca. |
| `token_type` | Sempre será `Bearer`. |
| `expires_in` | Indica o número de segundos durante os quais o token é válido, para fins de caching. |
| `scope` | Indica os escopos para os quais access_token será válido. Pode não incluir todos os escopos solicitados, se eles não eram aplicáveis ao usuário (no caso de escopos do Azure de somente do AD que está sendo solicitado quando uma conta pessoal é usada para fazer logon). |
| `id_token` | Um JWT (Token Web JSON) assinado. Incluído se `response_type` incluir `id_token`. O aplicativo pode decodificar os segmentos desse token para solicitar informações sobre o usuário que fez login. O aplicativo pode armazenar em cache os valores e exibi-los, mas ele não deve depender deles para qualquer autorização ou limites de segurança. Para obter mais informações sobre id_tokens, veja a [referência `id_token`](id-tokens.md). <br> **Observação:** Somente fornecido se o escopo `openid` for solicitado. |
| `state` |Se um parâmetro de estado estiver incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores de estado na solicitação e na resposta são idênticos. |

#### <a name="error-response"></a>Resposta de erro

As respostas de erro também podem ser enviadas ao `redirect_uri` para que o aplicativo possa tratá-las adequadamente. No caso de `prompt=none`, um erro esperado será:

```
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parâmetro | DESCRIÇÃO |
| --- | --- |
| `error` |Uma cadeia de caracteres de códigos de erro que pode ser usada para classificar tipos de erro que ocorrem e pode ser usada para responder aos erros. |
| `error_description` |Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro de autenticação. |

Se você receber esse erro na solicitação do iframe, o usuário deverá entrar novamente de forma interativa para recuperar um novo token. Você pode escolher lidar com isso da maneira que fizer mais sentido para seu aplicativo.

## <a name="validating-access-tokens"></a>Validar tokens de acesso

Após receber um access_token, certifique-se de validar a assinatura do token e as declarações a seguir. Também é possível escolher validar declarações adicionais com base no seu cenário.

* Declaração de**audiência** para garantir que o token tenha sido destinado ao aplicativo
* **emissor** de declaração, para verificar se o token foi emitido para seu aplicativo no ponto de extremidade de plataforma de identidade do Microsoft
* Declarações de **tempo de expiração** e **não antes** para verificar se o token não expirou

Para obter mais informações sobre as declarações presentes no token de acesso, veja a [referência de token de acesso](access-tokens.md)

## <a name="refreshing-tokens"></a>Atualizando tokens

A concessão implícita não fornece tokens de atualização. `id_token`s e `access_token`s expirarão após um curto período. Portanto, seu aplicativo deve estar preparado para atualizar esses tokens periodicamente. Para atualizar qualquer tipo de token, você pode executar a mesma solicitação de iframe oculto mencionada acima usando o `prompt=none` parâmetro para controlar o comportamento da plataforma de identidade. Se você quiser receber um novo `id_token`, use `response_type=id_token` e `scope=openid`, bem como um parâmetro `nonce`.

## <a name="send-a-sign-out-request"></a>Enviar uma solicitação de desconexão

O OpenID Connect `end_session_endpoint` permite que seu aplicativo enviar uma solicitação para o ponto de extremidade de plataforma do Microsoft identity para encerrar uma sessão de usuário e limpar os cookies definidos pelo ponto de extremidade de plataforma de identidade Microsoft. Para desconectar por completo um usuário de um aplicativo Web, seu aplicativo deve encerrar sua própria sessão com o usuário (normalmente, limpando o cache de token ou removendo cookies) e, depois, redirecionar o navegador para:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| Parâmetro |  | DESCRIÇÃO |
| --- | --- | --- |
| `tenant` |obrigatório |O valor `{tenant}` no caminho da solicitação pode ser usado para controlar quem pode entrar no aplicativo. Os valores permitidos são `common`, `organizations`, `consumers` e identificadores de locatário. Para obter mais detalhes, consulte [noções básicas de protocolo](active-directory-v2-protocols.md#endpoints). |
| `post_logout_redirect_uri` | recomendável | A URL para a qual o usuário deve retornar após a conclusão do logoff. Esse valor deve corresponder a um dos URIs de redirecionamento registrados no aplicativo. Se não estiver incluído, o usuário verá uma mensagem genérica no ponto de extremidade de plataforma do Microsoft identity. |

## <a name="next-steps"></a>Próximas etapas

* Percorra os [exemplos de MSAL JS](sample-v2-code.md) para começar a codificação.
