---
title: Plataforma de identidade da Microsoft e fluxo de código de autorização do OAuth 2.0 | Azure
titleSuffix: Microsoft identity platform
description: Crie aplicativos Web usando a implementação da plataforma de identidade da Microsoft do protocolo de autenticação OAuth 2.0.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: aeed031025b9c494b35886861c273e2a7f9d2ac4
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101653721"
---
# <a name="microsoft-identity-platform-and-oauth-20-authorization-code-flow"></a>Plataforma de identidade da Microsoft e fluxo de código de autorização do OAuth 2.0

A concessão de código de autorização OAuth 2.0 pode ser usada em aplicativos instalados em um dispositivo para obter acesso a recursos protegidos, como APIs Web. Com a implementação da plataforma de identidade da Microsoft do OAuth 2.0, você pode adicionar entrada e acesso à API a seus aplicativos móveis e de área de trabalho.

Este artigo descreve como programar diretamente no protocolo do seu aplicativo usando qualquer linguagem de programação.  Quando possível, recomendamos usar as MSAL (bibliotecas de autenticação da Microsoft) com suporte para [adquirir tokens e chamar APIs Web seguras](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Confira também os [aplicativos de exemplo que usam MSAL](sample-v2-code.md).

O fluxo do código de autorização do OAuth 2.0 é descrito na [seção 4.1 da especificação do OAuth 2.0](https://tools.ietf.org/html/rfc6749). Ele é usado para realizar a autenticação e a autorização na maioria dos tipos de aplicativo, incluindo [aplicativos de página única](v2-app-types.md#single-page-apps-javascript), [aplicativos web](v2-app-types.md#web-apps) e [aplicativos instalados nativamente](v2-app-types.md#mobile-and-native-apps). O fluxo permite que os aplicativos adquiram access_tokens com segurança que podem ser usados para acessar recursos protegidos pela plataforma de identidade da Microsoft, bem como os tokens de atualização para obter access_tokens adicionais e tokens de ID para o usuário conectado.

## <a name="protocol-diagram"></a>Diagrama de protocolo

Em um alto nível, todo o fluxo de autenticação de um aplicativo é semelhante a:

![Fluxo do código de autenticação do OAuth](./media/v2-oauth2-auth-code-flow/convergence-scenarios-native.svg)

## <a name="redirect-uri-setup-required-for-single-page-apps"></a>Redirecionar a instalação do URI necessária para aplicativos de página única

O fluxo de código de autorização para aplicativos de página única requer umas configurações adicionais.  Siga as instruções para [criar seu aplicativo de página única](scenario-spa-app-registration.md#redirect-uri-msaljs-20-with-auth-code-flow) para marcar corretamente o URI de redirecionamento como habilitado para CORS. Para atualizar um URI de redirecionamento existente para habilitar o CORS, abra o editor de manifesto e defina o `type` campo para o URI de redirecionamento para `spa` na `replyUrlsWithType` seção. Você também pode clicar no URI de redirecionamento na seção "Web" da guia Autenticação e selecionar os URIs que você deseja migrar usando o fluxo de código de autorização.

O `spa` tipo de redirecionamento é compatível com versões anteriores com o fluxo implícito. Os aplicativos que atualmente usam o fluxo implícito para obter tokens podem mudar para o `spa` tipo de URI de redirecionamento sem problemas e continuar usando o fluxo implícito.

Se você tentar usar o fluxo de código de autorização e vir este erro:

`access to XMLHttpRequest at 'https://login.microsoftonline.com/common/v2.0/oauth2/token' from origin 'yourApp.com' has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.`

Em seguida, visite o registro do aplicativo e atualize o URI de redirecionamento para que seu aplicativo digite `spa` .

## <a name="request-an-authorization-code"></a>Solicitar um código de autorização

O fluxo do código de autorização começa com o cliente direcionando o usuário para o ponto de extremidade `/authorize` . Nessa solicitação, o cliente solicita as permissões `openid`, `offline_access` e `https://graph.microsoft.com/mail.read ` do usuário.  Algumas permissões são restritas ao administrador, por exemplo, gravar dados no diretório de uma organização usando `Directory.ReadWrite.All`. Se seu aplicativo solicitar acesso a uma dessas permissões de um usuário organizacional, o usuário receberá uma mensagem de erro que informa que não está autorizado a consentir com as permissões de seu aplicativo. Para solicitar acesso a escopos restritos ao administrador, você deve solicitá-los diretamente de um administrador global.  Para obter mais informações, leia [Permissões restritas ao administrador](v2-permissions-and-consent.md#admin-restricted-permissions).

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read%20api%3A%2F%2F
&state=12345
&code_challenge=YTFjNjI1OWYzMzA3MTI4ZDY2Njg5M2RkNmVjNDE5YmEyZGRhOGYyM2IzNjdmZWFhMTQ1ODg3NDcxY2Nl
&code_challenge_method=S256
```

> [!TIP]
> Clique no link a seguir para executar essa solicitação! Depois de entrar, seu navegador deverá ser redirecionado para `https://localhost/myapp/` com um `code` na barra de endereços.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

| Parâmetro    | Obrigatório/opcional | Descrição |
|--------------|-------------|--------------|
| `tenant`    | obrigatório    | O valor `{tenant}` no caminho da solicitação pode ser usado para controlar quem pode entrar no aplicativo. Os valores permitidos são `common`, `organizations`, `consumers` e identificadores de locatário. Para obter mais detalhes, consulte [noções básicas de protocolo](active-directory-v2-protocols.md#endpoints).  |
| `client_id`   | obrigatório    | A **ID do Aplicativo (cliente)** que a experiência [Portal do Azure - Registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) atribui ao seu aplicativo.  |
| `response_type` | obrigatório    | Deve incluir `code` para o fluxo do código de autorização. Também pode incluir `id_token` ou `token` se estiver usando o [fluxo híbrido](#request-an-id-token-as-well-hybrid-flow). |
| `redirect_uri`  | obrigatório | O redirect_uri do seu aplicativo, onde as respostas de autenticação podem ser enviadas e recebidas pelo aplicativo. Ele deve corresponder exatamente a um dos redirect_uris que você registrou no portal, com exceção de que ele deve ser codificado por url. Para aplicativos móveis nativos &, você deve usar um dos valores recomendados –  `https://login.microsoftonline.com/common/oauth2/nativeclient` (para aplicativos que usam navegadores inseridos) ou `http://localhost` (para aplicativos que usam navegadores do sistema). |
| `scope`  | obrigatório    | Uma lista separada por espaços de [escopos](v2-permissions-and-consent.md) para os quais você deseja o consentimento do usuário.  Para o trecho `/authorize` da solicitação, vários recursos podem ser envolvidos, permitindo que seu aplicativo receba consentimento para várias APIs Web que você deseja chamar. |
| `response_mode`   | recomendável | Especifica o método que deve ser usado para enviar o token resultante de volta ao aplicativo. Um dos seguintes pode ser feito:<br/><br/>- `query`<br/>- `fragment`<br/>- `form_post`<br/><br/>`query` fornece o código como um parâmetro da cadeia de caracteres de consulta no URI de redirecionamento. Se você estiver solicitando um token de ID usando o fluxo implícito, não será possível usar `query` como especificado na [especificação OpenID](https://openid.net/specs/oauth-v2-multiple-response-types-1_0.html#Combinations). Se você estiver solicitando apenas o código, será possível usar `query`, `fragment` ou `form_post`. `form_post` executa um POST contendo o código para o URI de redirecionamento. |
| `state`                 | recomendável | Um valor incluído na solicitação também será retornado na resposta do token. Pode ser uma cadeia de caracteres de qualquer conteúdo desejado. Um valor exclusivo gerado aleatoriamente normalmente é usado para [impedir ataques de solicitação intersite forjada](https://tools.ietf.org/html/rfc6749#section-10.12). O valor também pode codificar informações sobre o estado do usuário no aplicativo antes da solicitação de autenticação, como a página ou exibição em que estavam. |
| `prompt`  | opcional    | Indica o tipo de interação do usuário que é necessário. Os únicos valores válidos no momento são `login`, `none`, e `consent`.<br/><br/>- `prompt=login`forçará o usuário a inserir suas credenciais na solicitação, negando o logon único.<br/>- `prompt=none` é o oposto: ele garantirá que o usuário não seja apresentado a nenhum prompt interativo. Se a solicitação não puder ser concluída silenciosamente por meio de logon único, a plataforma de identidade da Microsoft retornará um `interaction_required` erro.<br/>- `prompt=consent` irá disparar a caixa de diálogo de consentimento do OAuth depois que o usuário iniciar a sessão, solicitando que ele conceda permissões ao aplicativo.<br/>- `prompt=select_account` interromperá o logon único fornecendo experiência de seleção de conta que lista todas as contas na sessão, ou em qualquer conta memorizada, ou uma opção para optar por usar uma conta completamente diferente.<br/> |
| `login_hint`  | opcional    | Pode ser usado para preencher previamente o campo de nome de usuário/endereço de email da página de entrada do usuário, se você souber o nome de usuário com antecedência. Geralmente, os aplicativos usarão esse parâmetro durante a reautenticação, após já terem extraído o nome de usuário de uma entrada anterior usando a declaração `preferred_username`.   |
| `domain_hint`  | opcional    | Se for incluído, ele ignorará o processo de descoberta baseada em email que o usuário passa na página de entrada, resultando em uma experiência de usuário um pouco mais simples, por exemplo, enviando-os ao provedor de identidade federada. Geralmente, os aplicativos usam esse parâmetro durante a reautenticação, extraindo `tid` de uma entrada anterior. Se o valor da declaração `tid` for `9188040d-6c67-4c5b-b112-36a304b66dad`, você deverá usar `domain_hint=consumers`. Caso contrário, use `domain_hint=organizations`.  |
| `code_challenge`  | recomendado/obrigatório | Usado para proteger as concessões de código de autorização por meio da Chave de Prova para Troca de Código (PKCE). Necessário se `code_challenge_method` estiver incluído. Para mais informações, consulte [PKCE RFC](https://tools.ietf.org/html/rfc7636). Isso agora é recomendado para todos os tipos de aplicativos – clientes públicos e confidenciais – e exigidos pela plataforma de identidade da Microsoft para [aplicativos de página única usando o fluxo de código de autorização](reference-third-party-cookies-spas.md). |
| `code_challenge_method` | recomendado/obrigatório | O método utilizado para codificar o `code_verifier` para o parâmetro `code_challenge`. Isso *deve* ser `S256` , mas a especificação permite o uso de `plain` If, por algum motivo, o cliente não dá suporte a SHA256. <br/><br/>Se excluído, `code_challenge` será considerado texto não criptografado se `code_challenge` estiver incluído. A plataforma Microsoft Identity dá suporte ao `plain` e ao `S256` . Para mais informações, consulte [PKCE RFC](https://tools.ietf.org/html/rfc7636). Isso é necessário para [aplicativos de página única que usam o fluxo de código de autorização](reference-third-party-cookies-spas.md).|


Nesse ponto, será solicitado que o usuário insira suas credenciais e conclua a autenticação. A plataforma Microsoft Identity também garantirá que o usuário tenha consentido as permissões indicadas no parâmetro de `scope` consulta. Se o usuário não tiver consentido nenhuma dessas permissões, ele será solicitado a consentir as permissões necessárias. Os detalhes dos aplicativos quanto a [permissões, consentimento e multilocatário são fornecidos aqui](v2-permissions-and-consent.md).

Depois que o usuário autenticar e conceder consentimento, a plataforma de identidade da Microsoft retornará uma resposta ao seu aplicativo no indicado `redirect_uri` , usando o método especificado no `response_mode` parâmetro.

#### <a name="successful-response"></a>Resposta bem-sucedida

Uma resposta bem-sucedida usando `response_mode=query` tem a seguinte aparência:

```HTTP
GET http://localhost?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=12345
```

| Parâmetro | Descrição  |
|-----------|--------------|
| `code` | O authorization_code que o aplicativo solicitou. O aplicativo pode usar o código de autorização para solicitar um token de acesso para o recurso de destino. Authorization_codes têm duração curta, geralmente, expiram depois de aproximadamente 10 minutos. |
| `state` | Se um parâmetro de estado estiver incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores de estado da solicitação e da resposta são idênticos. |

Você também poderá receber um token de ID se solicitar um e tiver a concessão implícita habilitada no registro do aplicativo.  Isso às vezes é chamado de ["fluxo híbrido"](#request-an-id-token-as-well-hybrid-flow)e é usado por estruturas como ASP.net.

#### <a name="error-response"></a>Resposta de erro

As respostas de erro também podem ser enviadas ao `redirect_uri` para que o aplicativo possa tratá-las adequadamente:

```HTTP
GET http://localhost?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parâmetro | Descrição  |
|----------|------------------|
| `error`  | Uma cadeia de caracteres de códigos de erro que pode ser usada para classificar tipos de erro que ocorrem e pode ser usada para responder aos erros. |
| `error_description` | Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro de autenticação. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Códigos de erro para erros de ponto de extremidade de autorização

A tabela a seguir descreve os vários códigos de erro que podem ser retornados no parâmetro `error` da resposta de erro.

| Código do Erro  | Descrição    | Ação do Cliente   |
|-------------|----------------|-----------------|
| `invalid_request` | Erro de protocolo, como um parâmetro obrigatório ausente. | Corrija e reenvie a solicitação. Este é um erro de desenvolvimento normalmente detectado durante o teste inicial. |
| `unauthorized_client` | O aplicativo cliente não tem permissão para solicitar um código de autorização. | Esse erro geralmente ocorre quando o aplicativo cliente não está registrado no Azure Active Directory ou não é adicionado ao locatário do Azure Active Directory do usuário. O aplicativo pode solicitar que o usuário instale o aplicativo e o adicione ao Azure AD. |
| `access_denied`  | Consentimento negado pelo proprietário do recurso  | O aplicativo cliente pode notificar o usuário de que não pode continuar, a menos que o usuário consinta. |
| `unsupported_response_type` | O servidor de autorização não dá suporta ao tipo de resposta na solicitação. | Corrija e reenvie a solicitação. Este é um erro de desenvolvimento normalmente detectado durante o teste inicial. Quando visto no [fluxo híbrido](#request-an-id-token-as-well-hybrid-flow), o sinaliza que você deve habilitar a configuração de concessão implícita de token de ID no registro do aplicativo cliente. |
| `server_error`  | O servidor encontrou um erro inesperado.| Tente novamente a solicitação. Esses erros podem resultar de condições temporárias. O aplicativo cliente pode explicar ao usuário que a resposta está atrasada para um erro temporário. |
| `temporarily_unavailable`   | O servidor está temporariamente muito ocupado para tratar da solicitação. | Tente novamente a solicitação. O aplicativo cliente pode explicar para o usuário que sua resposta está atrasada devido a uma condição temporária. |
| `invalid_resource`  | O recurso de destino é inválido porque não existe, o Azure Active Directory não consegue encontrá-lo ou ele não está configurado corretamente. | Esse erro indica que o recurso, se existir, não foi configurado no locatário. O aplicativo pode solicitar que o usuário instale o aplicativo e o adicione ao Azure AD. |
| `login_required` | Muitos ou nenhum usuário encontrado | O cliente solicitou autenticação silenciosa (`prompt=none`), mas não foi possível encontrar um usuário único. Isso pode significar que há vários usuários ativos na sessão ou nenhum usuário. Isso leva em conta o locatário escolhido (por exemplo, se houver duas contas do Azure AD ativas e uma conta Microsoft e `consumers` for escolhido, a autenticação silenciosa funcionará). |
| `interaction_required` | A solicitação requer interação do usuário. | É necessário uma etapa de autenticação adicional ou consentimento. Repita a solicitação sem `prompt=none`. |

### <a name="request-an-id-token-as-well-hybrid-flow"></a>Solicitar um token de ID também (fluxo híbrido)

Para saber quem é o usuário antes de resgatar um código de autorização, é comum que os aplicativos também solicitem um token de ID ao solicitarem o código de autorização. Isso é chamado de *fluxo híbrido* porque combina a concessão implícita com o fluxo do código de autorização. O fluxo híbrido é comumente usado em aplicativos Web que desejam renderizar uma página para um usuário sem bloqueio no resgate de código, especialmente [ASP.net](quickstart-v2-aspnet-core-webapp.md). Os aplicativos de página única e os aplicativos Web tradicionais se beneficiam da latência reduzida nesse modelo.

O fluxo híbrido é o mesmo que o fluxo de código de autorização descrito anteriormente, mas com três adições, todas necessárias para solicitar um token de ID: novos escopos, um novo response_type e um novo `nonce` parâmetro de consulta.

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code%20id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=fragment
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&state=12345
&nonce=abcde
&code_challenge=YTFjNjI1OWYzMzA3MTI4ZDY2Njg5M2RkNmVjNDE5YmEyZGRhOGYyM2IzNjdmZWFhMTQ1ODg3NDcxY2Nl
&code_challenge_method=S256
```

| Parâmetro atualizado | Obrigatório/opcional | Descrição |
|---------------|-------------|--------------|
|`response_type`| Obrigatório | A adição de `id_token` indica ao servidor que o aplicativo gostaria de um token de ID na resposta do ponto de `/authorize` extremidade.  |
|`scope`| Obrigatório | Para tokens de ID, deve ser atualizado para incluir os escopos de token de ID- `openid` e, opcionalmente, `profile` e `email` . |
|`nonce`| Obrigatório|     Um valor incluído na solicitação, gerado pelo aplicativo, que será incluído no id_token resultante como uma declaração. O aplicativo pode, então, verificar esse valor para atenuar os ataques de reprodução de token. O valor normalmente é uma cadeia de caracteres aleatória e exclusiva que pode ser usada para identificar a origem da solicitação. |
|`response_mode`| Recomendadas | Especifica o método que deve ser usado para enviar o token resultante de volta ao seu aplicativo. O padrão é para `query` apenas um código de autorização, mas `fragment` se a solicitação incluir uma id_token `response_type` .  No entanto, os aplicativos são recomendados para uso `form_post` , especialmente ao usar `http:/localhost` como um URI de redirecionamento. |

O uso do `fragment` como modo de resposta causa problemas para aplicativos Web que lêem o código do redirecionamento, uma vez que os navegadores não passam o fragmento para o servidor Web.  Nessas situações, os aplicativos devem usar o `form_post` modo de resposta para garantir que todos os dados sejam enviados ao servidor. 

#### <a name="successful-response"></a>Resposta bem-sucedida

Uma resposta bem-sucedida usando `response_mode=fragment` tem a seguinte aparência:

```HTTP
GET https://login.microsoftonline.com/common/oauth2/nativeclient#
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&id_token=eYj...
&state=12345
```

| Parâmetro | Descrição  |
|-----------|--------------|
| `code` | O código de autorização que o aplicativo solicitou. O aplicativo pode usar o código de autorização para solicitar um token de acesso para o recurso de destino. Os códigos de autorização são de curta duração, normalmente expirando após cerca de 10 minutos. |
| `id_token` | Um token de ID para o usuário, emitido por meio de *concessão implícita*. Contém uma `c_hash` declaração especial que é o hash do `code` na mesma solicitação. |
| `state` | Se um parâmetro de estado for incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores de estado da solicitação e da resposta são idênticos. |

## <a name="request-an-access-token"></a>Solicitar um token de acesso

Agora que você adquiriu um authorization_code e teve permissão concedida pelo usuário, é possível resgatar `code` para `access_token` no recurso desejado. Faça isso enviando uma `POST` solicitação para o ponto de extremidade `/token`:

```HTTP
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&code_verifier=ThisIsntRandomButItNeedsToBe43CharactersLong 
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps. This secret needs to be URL-Encoded.
```

> [!TIP]
> Tente executar a solicitação no Postman! (Não se esqueça de substituir o `code`) [![Tente executar esta solicitação no Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

| Parâmetro  | Obrigatório/opcional | Descrição     |
|------------|-------------------|----------------|
| `tenant`   | obrigatório   | O valor `{tenant}` no caminho da solicitação pode ser usado para controlar quem pode entrar no aplicativo. Os valores permitidos são `common`, `organizations`, `consumers` e identificadores de locatário. Para obter mais detalhes, consulte [noções básicas de protocolo](active-directory-v2-protocols.md#endpoints).  |
| `client_id` | obrigatório  | A ID do Aplicativo (cliente) que a página [Portal do Azure - Registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) atribuiu ao seu aplicativo. |
| `grant_type` | obrigatório   | Deve ser `authorization_code` para o fluxo do código de autorização.   |
| `scope`      | opcionais   | Uma lista de escopos separados por espaços. Os escopos devem ser todos de um único recurso, juntamente com escopos OIDC (`profile`, `openid`, `email`). Para obter uma explicação mais detalhada de escopos, confira [permissões, consentimento e escopos](v2-permissions-and-consent.md). Essa é uma extensão da Microsoft para o fluxo de código de autorização, destinado a permitir que os aplicativos declarem o recurso para o qual desejam o token durante o resgate do token.|
| `code`          | obrigatório  | O authorization_code que você adquiriu na primeira ramificação do fluxo. |
| `redirect_uri`  | obrigatório  | O mesmo valor de redirect_uri que foi usado para adquirir o authorization_code. |
| `client_secret` | necessário para aplicativos Web confidenciais | O segredo do aplicativo que você criou no portal de registro do aplicativo para seu aplicativo. Você não deve usar o segredo do aplicativo em um aplicativo nativo ou de página única porque client_secrets não pode ser armazenado de forma confiável em dispositivos ou páginas da Web. Ele é obrigatório para aplicativos Web e APIs Web, que têm a capacidade de armazenar o client_secret com segurança no servidor.  Como todos os parâmetros discutidos aqui, o segredo do cliente deve ser codificado por URL antes de ser enviado, uma etapa geralmente executada pelo SDK. Para obter mais informações sobre a codificação de URI, consulte a [especificação de Sintaxe Genérica de URI](https://tools.ietf.org/html/rfc3986#page-12). |
| `code_verifier` | recomendável  | O mesmo code_verifier que foi usado para obter o authorization_code. Obrigatório se o PKCE foi usado na solicitação de concessão de código de autorização. Para mais informações, consulte [PKCE RFC](https://tools.ietf.org/html/rfc7636). |

### <a name="successful-response"></a>Resposta bem-sucedida

Uma resposta de token bem-sucedida será assim:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```

| Parâmetro     | Descrição   |
|---------------|------------------------------|
| `access_token`  | O token de acesso solicitado. O aplicativo pode usar esse token para se autenticar no recurso protegido, como uma API Web.  |
| `token_type`    | Indica o valor do tipo de token. O único tipo que oferece suporte ao AD do Azure é Portador |
| `expires_in`    | Por quanto tempo o token de acesso é válido (em segundos). |
| `scope`         | Os escopos para os quais o access_token é válido. Opcional-isso não é padrão e, se omitido, o token será para os escopos solicitados no segmento inicial do fluxo. |
| `refresh_token` | Um token de atualização do OAuth 2.0. O aplicativo pode usar esse token para adquirir tokens de acesso adicionais depois que o token de acesso atual expira. Os Refresh_tokens têm longa duração e podem ser usados para reter acesso a recursos por períodos estendidos. Para saber mais sobre como atualizar um token de acesso, consulte a [seção abaixo](#refresh-the-access-token). <br> **Observação:** Somente fornecido se o escopo `offline_access` for solicitado. |
| `id_token`      | Um JWT (Token Web JSON). O aplicativo pode decodificar os segmentos desse token para solicitar informações sobre o usuário que fez login. O aplicativo pode armazenar em cache os valores e exibi-los, e clientes confidenciais podem usar isso para autorização. Para obter mais informações sobre id_tokens, veja a [`id_token reference`](id-tokens.md). <br> **Observação:** Somente fornecido se o escopo `openid` for solicitado. |

### <a name="error-response"></a>Resposta de erro

As respostas de erro serão parecidas com esta:

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Parâmetro         | Descrição    |
|-------------------|----------------|
| `error`       | Uma cadeia de caracteres de códigos de erro que pode ser usada para classificar tipos de erro que ocorrem e pode ser usada para responder aos erros. |
| `error_description` | Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro de autenticação. |
| `error_codes` | Uma lista de códigos de erro específicos do STS que pode ajudar no diagnóstico.  |
| `timestamp`   | A hora na qual o erro ocorreu. |
| `trace_id`    | Um identificador exclusivo para a solicitação que pode ajudar no diagnóstico. |
| `correlation_id` | Um identificador exclusivo para a solicitação que pode ajudar no diagnóstico entre os componentes. |

### <a name="error-codes-for-token-endpoint-errors"></a>Códigos de erro para erros de ponto de extremidade de token

| Código do Erro         | Descrição        | Ação do Cliente    |
|--------------------|--------------------|------------------|
| `invalid_request`  | Erro de protocolo, como um parâmetro obrigatório ausente. | Corrigir a solicitação ou o registro do aplicativo e reenviar a solicitação   |
| `invalid_grant`    | O código de autorização ou o verificador de código PKCE é inválido ou expirou. | Tente uma nova solicitação para o ponto de extremidade `/authorize` e verifique se o parâmetro code_verifier estava correto.  |
| `unauthorized_client` | O cliente autenticado não está autorizado a usar esse tipo de concessão de autorização. | Isso geralmente ocorre quando o aplicativo cliente não está registrado no Azure Active Directory ou não é adicionado ao locatário do Azure Active Directory do usuário. O aplicativo pode solicitar que o usuário instale o aplicativo e o adicione ao Azure AD. |
| `invalid_client` | Falha na autenticação de cliente.  | As credenciais do cliente não são válidas. Para corrigi-las, o administrador do aplicativo atualiza as credenciais.   |
| `unsupported_grant_type` | O servidor de autorização não dá suporte ao tipo de concessão de autorização. | Altere o tipo de concessão na solicitação. Esse tipo de erro deve ocorrer somente durante o desenvolvimento e ser detectado durante os testes iniciais. |
| `invalid_resource` | O recurso de destino é inválido porque não existe, o Azure Active Directory não consegue encontrá-lo ou ele não está configurado corretamente. | Isso indica que o recurso, se ele existe, não foi configurado no locatário. O aplicativo pode solicitar que o usuário instale o aplicativo e o adicione ao Azure AD.  |
| `interaction_required` | Não padrão, pois a especificação OIDC chama isso somente no `/authorize` ponto de extremidade. A solicitação requer interação do usuário. Por exemplo, é necessária uma etapa de autenticação adicional. | Repita a `/authorize` solicitação com os mesmos escopos. |
| `temporarily_unavailable` | O servidor está temporariamente muito ocupado para tratar da solicitação. | Repita a solicitação após um pequeno atraso. O aplicativo cliente pode explicar para o usuário que sua resposta está atrasada devido a uma condição temporária. |
|`consent_required` | A solicitação requer consentimento do usuário. Esse erro não é padrão, pois geralmente é retornado apenas no `/authorize` ponto de extremidade por especificações de OIDC. Retornado quando um `scope` parâmetro foi usado no fluxo de resgate de código que o aplicativo cliente não tem permissão para solicitar.  | O cliente deve enviar o usuário de volta para o `/authorize` ponto de extremidade com o escopo correto para disparar o consentimento. |
|`invalid_scope` | O escopo solicitado pelo aplicativo é inválido.  | Atualize o valor do parâmetro de escopo na solicitação de autenticação para um valor válido. |

> [!NOTE]
> Aplicativos de página única podem receber um erro `invalid_request` indicando que o resgate de token entre origens é permitido somente para o tipo de cliente “Aplicativo de Página Única”.  Isso indica que o URI de redirecionamento usado para solicitar o token não foi marcado como um URI de redirecionamento `spa`.  Revise as [etapas de registro do aplicativo](#redirect-uri-setup-required-for-single-page-apps) para saber como habilitar esse fluxo.

## <a name="use-the-access-token"></a>Usar o token de acesso

Agora que já adquiriu com êxito um `access_token`, você pode usar o token em solicitações para APIs Web incluindo-o no cabeçalho `Authorization`:

> [!TIP]
> Execute essa solicitação no Postman! (Substitua o header `Authorization` primeiro) [![Tente executar esta solicitação no Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```HTTP
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-access-token"></a>Atualizar o token de acesso

Os access_tokens têm curta duração e você deve atualizá-los depois que eles expiram para continuar acessando recursos. Faça isso enviando outra solicitação `POST` ao ponto de extremidade `/token`, dessa vez fornecendo `refresh_token` em vez de `code`.  Os tokens de atualização são válidos para todas as permissões já autorizadas para seu cliente. Assim, um token de atualização emitido em uma solicitação para `scope=mail.read` poderá ser usado para solicitar um novo token de acesso para `scope=api://contoso.com/api/UseResource`.

Os tokens de atualização para aplicativos Web e aplicativos nativos não têm tempos de vida especificados. Normalmente, os tempos de vida de tokens de atualização são relativamente longos. No entanto, em alguns casos, os tokens de atualização expiram, são revogados ou não têm privilégios suficientes para a ação desejada. Seu aplicativo precisa esperar e tratar os [erros retornados pelo ponto de extremidade](#error-codes-for-token-endpoint-errors) de emissão de token corretamente. No entanto, os aplicativos de página única obtêm um token com tempo de vida de 24 horas, exigindo uma nova autenticação todos os dias.  Isso pode ser feito silenciosamente em um iframe quando os cookies de terceiros estão habilitados, mas precisam usar um quadro de nível superior (seja navegação de página inteira ou um pop-up) em navegadores sem cookies de terceiros, como o Safari.

Embora tokens de atualização não sejam revogados quando usados para adquirir novos tokens de acesso, você deve descartar o token de atualização antigo. De acordo com a especificação [OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-6): “O servidor de autorização PODE emitir um novo token de atualização. Nesse caso, o cliente DEVE descartar o token de atualização antigo e substituí-lo pelo novo token de atualização. O servidor de autorização PODE revogar o token de atualização antigo depois de emitir um novo token de atualização ao cliente.”

>[!IMPORTANT]
> Para tokens de atualização enviados a um URI de redirecionamento registrado como `spa`, o token de atualização expirará após 24 horas. Tokens de atualização adicionais adquiridos usando o token de atualização inicial transportam esse tempo de expiração, portanto, os aplicativos devem estar preparados para executar novamente o fluxo do código de autorização usando uma autenticação interativa para obter um novo token de atualização a cada 24 horas. Os usuários não precisam inserir suas credenciais e geralmente nem mesmo veem qualquer UX, apenas um recarregamento do seu aplicativo, mas o navegador deve visitar a página de logon em um quadro de nível superior para ver a sessão de logon.  Isso ocorre devido a [recursos de privacidade em navegadores que bloqueiam cookies de terceiros](reference-third-party-cookies-spas.md).

```HTTP

// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&grant_type=refresh_token
&client_secret=JqQX2PNo9bpM0uEihUPzyrh      // NOTE: Only required for web apps. This secret needs to be URL-Encoded
```

> [!TIP]
> Tente executar a solicitação no Postman! (Não se esqueça de substituir o `refresh_token`) [![Tente executar esta solicitação no Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)
>

| Parâmetro     | Tipo           | Descrição        |
|---------------|----------------|--------------------|
| `tenant`        | obrigatório     | O valor `{tenant}` no caminho da solicitação pode ser usado para controlar quem pode entrar no aplicativo. Os valores permitidos são `common`, `organizations`, `consumers` e identificadores de locatário. Para obter mais detalhes, consulte [noções básicas de protocolo](active-directory-v2-protocols.md#endpoints).   |
| `client_id`     | obrigatório    | A **ID do Aplicativo (cliente)** que a experiência [Portal do Azure - Registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) atribui ao seu aplicativo. |
| `grant_type`    | obrigatório    | Deve ser `refresh_token` para essa ramificação do código de autorização. |
| `scope`         | obrigatório    | Uma lista de escopos separados por espaços. Os escopos solicitados nessa ramificação devem ser equivalentes aos escopos solicitados na ramificação de solicitação authorization_code original, ou um subconjunto desses escopos. Se os escopos especificados nessa solicitação abrangerem vários servidores de recursos, a plataforma de identidade da Microsoft retornará um token para o recurso especificado no primeiro escopo. Para obter uma explicação mais detalhada de escopos, confira [permissões, consentimento e escopos](v2-permissions-and-consent.md). |
| `refresh_token` | obrigatório    | O refresh_token que você adquiriu na segunda ramificação do fluxo. |
| `client_secret` | obrigatório para aplicativos Web | O segredo do aplicativo que você criou no portal de registro do aplicativo para seu aplicativo. Ele não deve ser usado em um aplicativo nativo, pois client_secrets não podem ser armazenados de modo confiável em dispositivos. Ele é obrigatório para aplicativos Web e APIs Web, que têm a capacidade de armazenar o client_secret com segurança no servidor. Esse segredo precisa ser codificado por URL. Para obter mais informações, consulte a [especificação de sintaxe genérica do URI](https://tools.ietf.org/html/rfc3986#page-12). |

#### <a name="successful-response"></a>Resposta bem-sucedida

Uma resposta de token bem-sucedida será assim:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```

| Parâmetro     | Descrição         |
|---------------|-------------------------------------------------------------|
| `access_token`  | O token de acesso solicitado. O aplicativo pode usar esse token para se autenticar no recurso protegido, como uma API Web. |
| `token_type`    | Indica o valor do tipo de token. O único tipo que oferece suporte ao AD do Azure é Portador |
| `expires_in`    | Por quanto tempo o token de acesso é válido (em segundos).   |
| `scope`         | Os escopos para os quais o access_token é válido.    |
| `refresh_token` | Um novo token de atualização OAuth 2.0. Você deve substituir o token de atualização antigo por esse token de atualização recém-adquirido para garantir que seus tokens de atualização permaneçam válidos pelo máximo tempo possível. <br> **Observação:** Somente fornecido se o escopo `offline_access` for solicitado.|
| `id_token`      | Um JWT (Token Web JSON) não assinado. O aplicativo pode decodificar os segmentos desse token para solicitar informações sobre o usuário que fez login. O aplicativo pode armazenar em cache os valores e exibi-los, mas não deve depender deles para qualquer autorização ou limites de segurança. Para obter mais informações sobre id_tokens, veja a [`id_token reference`](id-tokens.md). <br> **Observação:** Somente fornecido se o escopo `openid` for solicitado. |

#### <a name="error-response"></a>Resposta de erro

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Parâmetro         | Descrição                                                                                        |
|-------------------|----------------------------------------------------------------------------------------------------|
| `error`           | Uma cadeia de caracteres de códigos de erro que pode ser usada para classificar tipos de erro que ocorrem e pode ser usada para responder aos erros. |
| `error_description` | Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro de autenticação.           |
| `error_codes` |Uma lista de códigos de erro específicos do STS que pode ajudar no diagnóstico. |
| `timestamp` | A hora na qual o erro ocorreu. |
| `trace_id` | Um identificador exclusivo para a solicitação que pode ajudar no diagnóstico. |
| `correlation_id` | Um identificador exclusivo para a solicitação que pode ajudar no diagnóstico entre os componentes. |

Para obter uma descrição dos códigos de erro e a ação recomendada do cliente, veja [Códigos de erro para erros de ponto de extremidade de token](#error-codes-for-token-endpoint-errors).
