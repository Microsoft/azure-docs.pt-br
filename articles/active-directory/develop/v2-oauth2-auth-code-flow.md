---
title: Plataforma de identidade da Microsoft e o fluxo de código de autorização do OAuth | Azure
description: Criação de aplicativos web usando a implementação de plataforma de identidade da Microsoft do protocolo de autenticação OAuth 2.0.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: ae1d7d86-7098-468c-aa32-20df0a10ee3d
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b8244707ec44b9afc51d5f2c21c3e95ff0692cfb
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67482352"
---
# <a name="microsoft-identity-platform-and-oauth-20-authorization-code-flow"></a>Plataforma de identidade da Microsoft e de fluxo de código de autorização do OAuth 2.0

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

A concessão de código de autorização OAuth 2.0 pode ser usada em aplicativos instalados em um dispositivo para obter acesso a recursos protegidos, como APIs Web. Usando a implementação de plataforma de identidade da Microsoft do OAuth 2.0, você pode adicionar entrar e acesso à API aos seus aplicativos móveis e da área de trabalho. Este guia é independente de idioma e descreve como enviar e receber mensagens HTTP sem usar nenhuma das [bibliotecas de autenticação de autenticação de software livre do Azure](reference-v2-libraries.md).

> [!NOTE]
> Nem todos os recursos e cenários do Azure Active Directory têm suporte no ponto de extremidade de plataforma do Microsoft identity. Para determinar se deve usar o ponto de extremidade de plataforma do Microsoft identity, leia sobre [limitações da plataforma Microsoft identity](active-directory-v2-limitations.md).

O fluxo do código de autorização do OAuth 2.0 é descrito na [seção 4.1 da especificação do OAuth 2.0](https://tools.ietf.org/html/rfc6749). Ele é usado para realizar a autenticação e autorização na maioria dos tipos de aplicativos, incluindo [aplicativos web](v2-app-types.md#web-apps) e [aplicativos instalados nativamente](v2-app-types.md#mobile-and-native-apps). O fluxo permite que aplicativos adquiram com segurança access_tokens que podem ser usados para acessar recursos protegidos pelo ponto de extremidade de plataforma de identidade Microsoft.

## <a name="protocol-diagram"></a>Diagrama de protocolo

Em um alto nível, todo o fluxo de autenticação de um aplicativo nativo/móvel é um pouco semelhante a:

![Fluxo do código de autenticação do OAuth](./media/v2-oauth2-auth-code-flow/convergence-scenarios-native.svg)

## <a name="request-an-authorization-code"></a>Solicitar um código de autorização

O fluxo do código de autorização começa com o cliente direcionando o usuário para o ponto de extremidade `/authorize` . Nessa solicitação, o cliente indica as permissões que precisa adquirir do usuário:

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&state=12345
```

> [!TIP]
> Clique no link a seguir para executar essa solicitação! Depois de entrar, seu navegador deverá ser redirecionado para `https://localhost/myapp/` com um `code` na barra de endereços.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&state=12345" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

| Parâmetro    | Obrigatório/opcional | DESCRIÇÃO |
|--------------|-------------|--------------|
| `tenant`    | obrigatório    | O valor `{tenant}` no caminho da solicitação pode ser usado para controlar quem pode entrar no aplicativo. Os valores permitidos são `common`, `organizations`, `consumers` e identificadores de locatário. Para obter mais detalhes, consulte [noções básicas de protocolo](active-directory-v2-protocols.md#endpoints).  |
| `client_id`   | obrigatório    | O **ID do aplicativo (cliente)** que o [portal do Azure – registros do aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) experiência atribuída ao seu aplicativo.  |
| `response_type` | obrigatório    | Deve incluir `code` para o fluxo do código de autorização.       |
| `redirect_uri`  | obrigatório | O redirect_uri do seu aplicativo, onde as respostas de autenticação podem ser enviadas e recebidas pelo aplicativo. Ele deve corresponder exatamente a um dos redirect_uris que você registrou no portal, com exceção de que ele deve ser codificado por url. Para aplicativos nativos e móveis, você deve usar o valor padrão de `https://login.microsoftonline.com/common/oauth2/nativeclient`.   |
| `scope`  | obrigatório    | Uma lista separada por espaços de [escopos](v2-permissions-and-consent.md) para os quais você deseja o consentimento do usuário.  Para o `/authorize` trecho da solicitação, isso pode abranger vários recursos, permitindo que seu aplicativo obter o consentimento para várias APIs que você deseja chamar da web. |
| `response_mode`   | recomendável | Especifica o método que deve ser usado para enviar o token resultante de volta ao aplicativo. Um dos seguintes pode ser feito:<br/><br/>- `query`<br/>- `fragment`<br/>- `form_post`<br/><br/>`query` fornece o código como um parâmetro da cadeia de caracteres de consulta no URI de redirecionamento. Se você estiver solicitando um token de ID usando o fluxo implícito, você não pode usar `query` conforme especificado na [especificação de OpenID](https://openid.net/specs/oauth-v2-multiple-response-types-1_0.html#Combinations). Se você estiver solicitando apenas o código, será possível usar `query`, `fragment` ou `form_post`. `form_post` executa uma POSTAGEM contendo o código para o URI de redirecionamento. Para obter mais informações, consulte [protocolo OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code).  |
| `state`                 | recomendável | Um valor incluído na solicitação também será retornado na resposta do token. Pode ser uma cadeia de caracteres de qualquer conteúdo desejado. Um valor exclusivo gerado aleatoriamente normalmente é usado para [impedir ataques de solicitação intersite forjada](https://tools.ietf.org/html/rfc6749#section-10.12). O valor também pode codificar informações sobre o estado do usuário no aplicativo antes da solicitação de autenticação, como a página ou exibição em que estavam. |
| `prompt`  | opcional    | Indica o tipo de interação do usuário que é necessário. Os únicos valores válidos no momento são `login`, `none`, e `consent`.<br/><br/>- `prompt=login`forçará o usuário a inserir suas credenciais na solicitação, negando o logon único.<br/>- `prompt=none` é o oposto - ele garantirá que o usuário não é apresentado a nenhum prompt interativo. Se a solicitação não pode ser concluída silenciosamente por meio de logon único, o ponto de extremidade de plataforma do Microsoft identity retornará um `interaction_required` erro.<br/>- `prompt=consent` irá disparar a caixa de diálogo de consentimento do OAuth depois que o usuário iniciar a sessão, solicitando que ele conceda permissões ao aplicativo. |
| `login_hint`  | opcional    | Pode ser usado para preencher previamente o campo de nome de usuário/endereço de email da página de entrada do usuário, se você souber o nome de usuário com antecedência. Geralmente, os aplicativos usarão esse parâmetro durante a reautenticação, após já terem extraído o nome de usuário de uma entrada anterior usando a declaração `preferred_username`.   |
| `domain_hint`  | opcional    | Pode ser `consumers` ou `organizations`.<br/><br/>Se for incluído, ele ignorará o processo de descoberta baseada em email que o usuário passa na página de entrada, resultando em uma experiência de usuário um pouco mais simples. Geralmente, os aplicativos usam esse parâmetro durante a reautenticação, extraindo `tid` de uma entrada anterior. Se o valor da declaração `tid` for `9188040d-6c67-4c5b-b112-36a304b66dad`, você deverá usar `domain_hint=consumers`. Caso contrário, use `domain_hint=organizations`.  |
| `code_challenge_method` | opcional    | O método utilizado para codificar o `code_verifier` para o parâmetro `code_challenge`. Pode ser um dos seguintes valores:<br/><br/>- `plain` <br/>- `S256`<br/><br/>Se excluído, `code_challenge` será considerado texto não criptografado se `code_challenge` estiver incluído. Plataforma de identidade da Microsoft dá suporte a ambos `plain` e `S256`. Para mais informações, consulte [PKCE RFC](https://tools.ietf.org/html/rfc7636). |
| `code_challenge`  | opcional | Usado para proteger as concessões de código de autorização por meio da chave de prova para código de câmbio (PKCE) de um cliente nativo. Necessário se `code_challenge_method` estiver incluído. Para mais informações, consulte [PKCE RFC](https://tools.ietf.org/html/rfc7636). |

Nesse ponto, será solicitado que o usuário insira suas credenciais e conclua a autenticação. O ponto de extremidade de plataforma do Microsoft identity também garantirá que o usuário consentiu as permissões indicadas no `scope` parâmetro de consulta. Se o usuário não tiver consentido nenhuma dessas permissões, ele será solicitado a consentir as permissões necessárias. Os detalhes dos aplicativos quanto a [permissões, consentimento e multilocatário são fornecidos aqui](v2-permissions-and-consent.md).

Depois que o usuário se autentica e dá consentimento, o ponto de extremidade de plataforma do Microsoft identity retornará uma resposta ao seu aplicativo no indicado `redirect_uri`, usando o método especificado no `response_mode` parâmetro.

#### <a name="successful-response"></a>Resposta bem-sucedida

Uma resposta bem-sucedida usando `response_mode=query` tem a seguinte aparência:

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=12345
```

| Parâmetro | DESCRIÇÃO  |
|-----------|--------------|
| `code` | O authorization_code que o aplicativo solicitou. O aplicativo pode usar o código de autorização para solicitar um token de acesso para o recurso de destino. Authorization_codes têm vida curta, normalmente eles expiram depois de cerca de 10 minutos. |
| `state` | Se um parâmetro de estado estiver incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores de estado na solicitação e na resposta são idênticos. |

#### <a name="error-response"></a>Resposta de erro

As respostas de erro também podem ser enviadas ao `redirect_uri` para que o aplicativo possa tratá-las adequadamente:

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parâmetro | DESCRIÇÃO  |
|----------|------------------|
| `error`  | Uma cadeia de caracteres de códigos de erro que pode ser usada para classificar tipos de erro que ocorrem e pode ser usada para responder aos erros. |
| `error_description` | Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro de autenticação. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Códigos de erro para erros de ponto de extremidade de autorização

A tabela a seguir descreve os vários códigos de erro que podem ser retornados no parâmetro `error` da resposta de erro.

| Código de Erro  | DESCRIÇÃO    | Ação do Cliente   |
|-------------|----------------|-----------------|
| `invalid_request` | Erro de protocolo, como um parâmetro obrigatório ausente. | Corrija e reenvie a solicitação. Este é um erro de desenvolvimento normalmente detectado durante o teste inicial. |
| `unauthorized_client` | O aplicativo cliente não tem permissão para solicitar um código de autorização. | Esse erro geralmente ocorre quando o aplicativo cliente não estiver registrado no Azure AD ou não será adicionado ao locatário do Azure AD do usuário. O aplicativo pode solicitar que o usuário instale o aplicativo e o adicione ao Azure AD. |
| `access_denied`  | Consentimento negado pelo proprietário do recurso  | O aplicativo cliente pode notificar o usuário que ele não pode continuar, a menos que o consentimento do usuário. |
| `unsupported_response_type` | O servidor de autorização não dá suporta ao tipo de resposta na solicitação. | Corrija e reenvie a solicitação. Este é um erro de desenvolvimento normalmente detectado durante o teste inicial.  |
| `server_error`  | O servidor encontrou um erro inesperado.| Tente novamente a solicitação. Esses erros podem resultar de condições temporárias. O aplicativo cliente pode explicar ao usuário que a resposta está atrasada para um erro temporário. |
| `temporarily_unavailable`   | O servidor está temporariamente muito ocupado para tratar da solicitação. | Tente novamente a solicitação. O aplicativo cliente pode explicar ao usuário que sua resposta está atrasada devido a uma condição temporária. |
| `invalid_resource`  | O recurso de destino é inválido porque não existe, AD do Azure não é possível encontrá-lo ou ele é não está corretamente configurado. | Esse erro indica que o recurso, se existir, não foi configurado no locatário. O aplicativo pode solicitar que o usuário instale o aplicativo e o adicione ao Azure AD. |
| `login_required` | Muitos ou nenhum usuário encontrado | O cliente solicitou autenticação silenciosa (`prompt=none`), mas não foi possível encontrar um usuário único. Isso pode significar que há vários usuários ativos na sessão ou nenhum usuário. Isso leva em conta o locatário escolhido (por exemplo, se houver duas contas do AD do Azure Active Directory e uma conta da Microsoft, e `consumers` for escolhido, a autenticação silenciosa funcionará). |
| `interaction_required` | A solicitação requer interação do usuário. | É necessário uma etapa de autenticação adicional ou consentimento. Repita a solicitação sem `prompt=none`. |

## <a name="request-an-access-token"></a>Solicitar um token de acesso

Agora que você adquiriu um authorization_code e teve permissão concedida pelo usuário, é possível resgatar `code` para `access_token` no recurso desejado. Faça isso enviando uma `POST` solicitação para o ponto de extremidade `/token`:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```

> [!TIP]
> Tente executar a solicitação no Postman! (Não se esqueça de substituir os `code`) [ ![tente executar essa solicitação no Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

| Parâmetro  | Obrigatório/opcional | DESCRIÇÃO     |
|------------|-------------------|----------------|
| `tenant`   | obrigatório   | O valor `{tenant}` no caminho da solicitação pode ser usado para controlar quem pode entrar no aplicativo. Os valores permitidos são `common`, `organizations`, `consumers` e identificadores de locatário. Para obter mais detalhes, consulte [noções básicas de protocolo](active-directory-v2-protocols.md#endpoints).  |
| `client_id` | obrigatório  | ID do aplicativo (cliente) que o [portal do Azure – registros do aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) atribuída ao seu aplicativo de página. |
| `grant_type` | obrigatório   | Deve ser `authorization_code` para o fluxo do código de autorização.   |
| `scope`      | obrigatório   | Uma lista de escopos separados por espaços. Os escopos solicitados nessa ramificação devem ser equivalentes aos escopos solicitados na primeira ramificação, ou um subconjunto desses escopos. Os escopos devem ser de um único recurso, junto com os escopos OIDC (`profile`, `openid`, `email`). Para obter uma explicação mais detalhada de escopos, confira [permissões, consentimento e escopos](v2-permissions-and-consent.md). |
| `code`          | obrigatório  | O authorization_code que você adquiriu na primeira ramificação do fluxo. |
| `redirect_uri`  | obrigatório  | O mesmo valor de redirect_uri que foi usado para adquirir o authorization_code. |
| `client_secret` | obrigatório para aplicativos Web | O segredo do aplicativo que você criou no portal de registro do aplicativo para seu aplicativo. Você não deve usar o segredo do aplicativo em um aplicativo nativo, pois client_secrets não podem ser armazenados de forma confiável em dispositivos. É necessário para aplicativos web e APIs web, que têm a capacidade de armazenar o client_secret com segurança no lado do servidor.  O segredo do cliente deve ser codificada como URL antes de serem enviados.  |
| `code_verifier` | opcional  | O mesmo code_verifier que foi usado para obter o authorization_code. Obrigatório se o PKCE foi usado na solicitação de concessão de código de autorização. Para mais informações, consulte [PKCE RFC](https://tools.ietf.org/html/rfc7636). |

### <a name="successful-response"></a>Resposta bem-sucedida

Uma resposta de token bem-sucedida se parecerá com esta:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fuser.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```

| Parâmetro     | DESCRIÇÃO   |
|---------------|------------------------------|
| `access_token`  | O token de acesso solicitado. O aplicativo pode usar esse token para se autenticar no recurso protegido, como uma API Web.  |
| `token_type`    | Indica o valor do tipo de token. O único tipo que oferece suporte ao AD do Azure é Portador |
| `expires_in`    | Por quanto tempo o token de acesso é válido (em segundos). |
| `scope`         | Os escopos para os quais o access_token é válido. |
| `refresh_token` | Um token de atualização do OAuth 2.0. O aplicativo pode usar esse token para adquirir tokens de acesso adicionais depois que o token de acesso atual expira. Os Refresh_tokens têm longa duração e podem ser usados para reter acesso a recursos por períodos estendidos. Para saber mais sobre como atualizar um token de acesso, consulte a [seção abaixo](#refresh-the-access-token). <br> **Observação:** Somente fornecido se o escopo `offline_access` for solicitado. |
| `id_token`      | Um JWT (Token Web JSON). O aplicativo pode decodificar os segmentos desse token para solicitar informações sobre o usuário que fez login. O aplicativo pode armazenar em cache os valores e exibi-los, mas não deve depender deles para qualquer autorização ou limites de segurança. Para obter mais informações sobre id_tokens, veja a [`id_token reference`](id-tokens.md). <br> **Observação:** Somente fornecido se o escopo `openid` for solicitado. |

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

| Parâmetro         | DESCRIÇÃO    |
|-------------------|----------------|
| `error`       | Uma cadeia de caracteres de códigos de erro que pode ser usada para classificar tipos de erro que ocorrem e pode ser usada para responder aos erros. |
| `error_description` | Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro de autenticação. |
| `error_codes` | Uma lista de códigos de erro específicos do STS que pode ajudar no diagnóstico.  |
| `timestamp`   | A hora na qual o erro ocorreu. |
| `trace_id`    | Um identificador exclusivo para a solicitação que pode ajudar no diagnóstico. |
| `correlation_id` | Um identificador exclusivo para a solicitação que pode ajudar no diagnóstico entre os componentes. |

### <a name="error-codes-for-token-endpoint-errors"></a>Códigos de erro para erros de ponto de extremidade de token

| Código de Erro         | DESCRIÇÃO        | Ação do Cliente    |
|--------------------|--------------------|------------------|
| `invalid_request`  | Erro de protocolo, como um parâmetro obrigatório ausente. | Corrija e reenvie a solicitação   |
| `invalid_grant`    | O código de autorização ou o verificador de código PKCE é inválido ou expirou. | Tente uma nova solicitação para o ponto de extremidade `/authorize` e verifique se o parâmetro code_verifier estava correto.  |
| `unauthorized_client` | O cliente autenticado não está autorizado a usar esse tipo de concessão de autorização. | Isso geralmente ocorre quando o aplicativo cliente não estiver registrado no Azure AD ou não será adicionado ao locatário do Azure AD do usuário. O aplicativo pode solicitar que o usuário instale o aplicativo e o adicione ao Azure AD. |
| `invalid_client` | Falha na autenticação de cliente.  | As credenciais do cliente não são válidas. Para corrigi-las, o administrador do aplicativo atualiza as credenciais.   |
| `unsupported_grant_type` | O servidor de autorização não dá suporte ao tipo de concessão de autorização. | Altere o tipo de concessão na solicitação. Esse tipo de erro deve ocorrer somente durante o desenvolvimento e ser detectado durante os testes iniciais. |
| `invalid_resource` | O recurso de destino é inválido porque não existe, AD do Azure não é possível encontrá-lo ou ele é não está corretamente configurado. | Isso indica que o recurso, se ele existe, não foi configurado no locatário. O aplicativo pode solicitar que o usuário instale o aplicativo e o adicione ao Azure AD.  |
| `interaction_required` | A solicitação requer interação do usuário. Por exemplo, é necessária uma etapa de autenticação adicional. | Repita a solicitação com o mesmo recurso.  |
| `temporarily_unavailable` | O servidor está temporariamente muito ocupado para tratar da solicitação. | Tente novamente a solicitação. O aplicativo cliente pode explicar ao usuário que sua resposta está atrasada devido a uma condição temporária. |

## <a name="use-the-access-token"></a>Usar o token de acesso

Agora que você já adquiriu com êxito um `access_token`, você pode usar o token em solicitações para APIs Web incluindo-o no cabeçalho `Authorization`:

> [!TIP]
> Execute essa solicitação no Postman! (Substitua os `Authorization` cabeçalho primeiro) [ ![tente executar essa solicitação no Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-access-token"></a>Atualizar o token de acesso

Os access_tokens têm curta duração e você deve atualizá-los depois que eles expiram para continuar acessando recursos. Faça isso enviando outra solicitação `POST` ao ponto de extremidade `/token`, dessa vez fornecendo `refresh_token` em vez de `code`.  Os tokens de atualização são válidos para todas as permissões já autorizadas para seu cliente. Assim, um token de atualização emitido em uma solicitação para `scope=mail.read` poderá ser usado para solicitar um novo token de acesso para `scope=api://contoso.com/api/UseResource`.  

Os tokens de atualização não têm um tempo de vida especificado. Normalmente, os tempos de vida de tokens de atualização são relativamente longos. No entanto, em alguns casos, os tokens de atualização expiram, são revogados ou não têm privilégios suficientes para a ação desejada. Seu aplicativo precisa esperar e tratar os [erros retornados pelo ponto de extremidade](#error-codes-for-token-endpoint-errors) de emissão de token corretamente. 

Embora os tokens de atualização não são revogados quando usado para adquirir novos tokens de acesso, você deve descartar o token de atualização antigo. O [especificação do OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-6) diz: "O servidor de autorização pode emitir um novo token de atualização, nesse caso o cliente deve descartar o token de atualização antigo e substituí-lo com o novo token de atualização. O servidor de autorização pode revogar o token de atualização antigos depois de emitir um novo token de atualização para o cliente."  

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&grant_type=refresh_token
&client_secret=JqQX2PNo9bpM0uEihUPzyrh      // NOTE: Only required for web apps
```

> [!TIP]
> Tente executar a solicitação no Postman! (Não se esqueça de substituir os `refresh_token`) [ ![tente executar essa solicitação no Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)
> 

| Parâmetro     |                | DESCRIÇÃO        |
|---------------|----------------|--------------------|
| `tenant`        | obrigatório     | O valor `{tenant}` no caminho da solicitação pode ser usado para controlar quem pode entrar no aplicativo. Os valores permitidos são `common`, `organizations`, `consumers` e identificadores de locatário. Para obter mais detalhes, consulte [noções básicas de protocolo](active-directory-v2-protocols.md#endpoints).   |
| `client_id`     | obrigatório    | O **ID do aplicativo (cliente)** que o [portal do Azure – registros do aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) experiência atribuída ao seu aplicativo. |
| `grant_type`    | obrigatório    | Deve ser `refresh_token` para essa ramificação do código de autorização. |
| `scope`         | obrigatório    | Uma lista de escopos separados por espaços. Os escopos solicitados nessa ramificação devem ser equivalentes aos escopos solicitados na ramificação de solicitação authorization_code original, ou um subconjunto desses escopos. Se os escopos especificados nessa solicitação incluírem vários servidores de recursos, o ponto de extremidade de plataforma do Microsoft identity retornará um token para o recurso especificado no primeiro escopo. Para obter uma explicação mais detalhada de escopos, confira [permissões, consentimento e escopos](v2-permissions-and-consent.md). |
| `refresh_token` | obrigatório    | O refresh_token que você adquiriu na segunda ramificação do fluxo. |
| `client_secret` | obrigatório para aplicativos Web | O segredo do aplicativo que você criou no portal de registro do aplicativo para seu aplicativo. Ele não deve ser usado em um aplicativo nativo, pois client_secrets não podem ser armazenados de forma confiável em dispositivos. É necessário para aplicativos web e APIs web, que têm a capacidade de armazenar o client_secret com segurança no lado do servidor. |

#### <a name="successful-response"></a>Resposta bem-sucedida

Uma resposta de token bem-sucedida se parecerá com esta:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fuser.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| Parâmetro     | DESCRIÇÃO         |
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

| Parâmetro         | DESCRIÇÃO                                                                                        |
|-------------------|----------------------------------------------------------------------------------------------------|
| `error`           | Uma cadeia de caracteres de códigos de erro que pode ser usada para classificar tipos de erro que ocorrem e pode ser usada para responder aos erros. |
| `error_description` | Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro de autenticação.           |
| `error_codes` |Uma lista de códigos de erro específicos do STS que pode ajudar no diagnóstico. |
| `timestamp` | A hora na qual o erro ocorreu. |
| `trace_id` | Um identificador exclusivo para a solicitação que pode ajudar no diagnóstico. |
| `correlation_id` | Um identificador exclusivo para a solicitação que pode ajudar no diagnóstico entre os componentes. |

Para obter uma descrição dos códigos de erro e a ação recomendada do cliente, veja [Códigos de erro para erros de ponto de extremidade de token](#error-codes-for-token-endpoint-errors).
