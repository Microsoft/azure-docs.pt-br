---
title: Plataforma de identidade da Microsoft e protocolo OpenID Connect | Azure
titleSuffix: Microsoft identity platform
description: Crie aplicativos Web usando a implementação da plataforma de identidade da Microsoft do protocolo de autenticação OpenID Connect.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 05/22/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: de1fcdc259de3f72e35feb411bcc836354352eb4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98752585"
---
# <a name="microsoft-identity-platform-and-openid-connect-protocol"></a>Plataforma de identidade da Microsoft e protocolo OpenID Connect

O OpenID Connect (OIDC) é um protocolo de autenticação criado no OAuth 2,0 que você pode usar para conectar com segurança um usuário a um aplicativo. Quando você usa a implementação da plataforma de identidade da Microsoft do OpenID Connect, você pode adicionar entrada e acesso à API para seus aplicativos. Este artigo mostra como fazer isso independentemente do idioma e descreve como enviar e receber mensagens HTTP sem usar nenhuma [biblioteca de código-fonte aberto da Microsoft](reference-v2-libraries.md).

O [OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) estende o protocolo de *autorização* OAuth 2,0 para uso como um protocolo de *autenticação* , para que você possa fazer logon único usando OAuth. O OpenID Connect apresenta o conceito de um *token de ID*, que é um token de segurança que permite ao cliente verificar a identidade do usuário. O token de ID também obtém informações de perfil básico sobre o usuário. Ele também apresenta o [ponto de extremidade de UserInfo](userinfo.md), uma API que retorna informações sobre o usuário. 


## <a name="protocol-diagram-sign-in"></a>Diagrama de protocolo: Conexão

O fluxo de entrada mais básico tem as etapas mostradas no diagrama seguinte. Cada etapa é descrita detalhadamente neste artigo.

![Protocolo OpenID Connect: Conexão](./media/v2-protocols-oidc/convergence-scenarios-webapp.svg)

## <a name="fetch-the-openid-connect-metadata-document"></a>Obter o documento de metadados do OpenID Connect

O OpenID Connect descreve um documento de metadados [(RFC)](https://openid.net/specs/openid-connect-discovery-1_0.html) que contém a maioria das informações necessárias para que um aplicativo faça logon. Isso inclui informações como as URLs a serem usadas e o local das chaves de assinatura públicas do serviço. Você pode encontrar este documento acrescentando o caminho do documento de descoberta à URL da autoridade:

Caminho do documento de descoberta: `/.well-known/openid-configuration`

Autoridades `https://login.microsoftonline.com/{tenant}/v2.0`

O `{tenant}` pode ter um de quatro valores:

| Valor | Descrição |
| --- | --- |
| `common` |Os usuários com uma conta pessoal da Microsoft e uma conta corporativa ou de estudante do Azure Active Directory podem entrar no aplicativo. |
| `organizations` |Somente os usuários com contas corporativas ou de estudante do Azure AD podem se conectar ao aplicativo. |
| `consumers` |Somente os usuários com uma conta pessoal da Microsoft podem entrar no aplicativo. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` ou `contoso.onmicrosoft.com` | Somente os usuários de um locatário específico do Azure Active Directory (sejam eles membros do diretório com uma conta corporativa ou de estudante ou convidados no diretório com um conta Microsoft pessoal) podem entrar no aplicativo. É possível usar o nome de domínio amigável do locatário do Azure AD ou o identificador GUID de locatário. Também é possível usar o locatário do consumidor, `9188040d-6c67-4c5b-b112-36a304b66dad`, no lugar do locatário de `consumers`.  |

A autoridade difere em nuvens nacionais, por exemplo, `https://login.microsoftonline.de` para a instância do Azure ad Alemanha. Se você não usar a nuvem pública, examine os pontos de [extremidade de nuvem nacional](authentication-national-cloud.md#azure-ad-authentication-endpoints) para encontrar o apropriado para você. Verifique se o locatário `/v2.0/` está presente em sua solicitação para que você possa usar a versão v 2.0 do ponto de extremidade.

> [!TIP]
> Experimente! Clique [https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration) para ver a `common` configuração.

### <a name="sample-request"></a>Solicitação de exemplo

Para chamar o ponto de extremidade de UserInfo para a autoridade comum na nuvem pública, use o seguinte:

```http
GET /common/v2.0/.well-known/openid-configuration
Host: login.microsoftonline.com
```

### <a name="sample-response"></a>Resposta de exemplo

Os metadados são um documento JSON (JavaScript Object Notation) simples. Veja o snippet a seguir para obter um exemplo. O conteúdo é totalmente descrito na [especificação do OpenID Connect](https://openid.net/specs/openid-connect-discovery-1_0.html#rfc.section.4.2).

```json
{
  "authorization_endpoint": "https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize",
  "token_endpoint": "https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token",
  "token_endpoint_auth_methods_supported": [
    "client_secret_post",
    "private_key_jwt"
  ],
  "jwks_uri": "https://login.microsoftonline.com/{tenant}/discovery/v2.0/keys",
  "userinfo_endpoint": "https://graph.microsoft.com/oidc/userinfo",
  "subject_types_supported": [
      "pairwise"
  ],
  ...

}
```

Se seu aplicativo tiver chaves de assinatura personalizadas como resultado do uso do recurso [claims-mapping](active-directory-claims-mapping.md), será necessário acrescentar um `appid`parâmetro de consulta contendo a ID do aplicativo para obter um `jwks_uri` apontando para as informações de chave de assinatura do aplicativo. Por exemplo: `https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` contém uma `jwks_uri` de `https://login.microsoftonline.com/{tenant}/discovery/v2.0/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e`.

Normalmente, você usaria este documento de metadados para configurar uma biblioteca do OpenID Connect ou o SDK. A biblioteca usaria os metadados para fazer seu trabalho. No entanto, se você não estiver usando uma biblioteca pré-criada do OpenID Connect, poderá seguir as etapas no restante deste artigo para fazer logon em um aplicativo Web usando a plataforma de identidade da Microsoft.

## <a name="send-the-sign-in-request"></a>Enviar a solicitação de conexão

Quando o aplicativo Web precisa autenticar o usuário, ele pode direcionar o usuário para o ponto de extremidade `/authorize` . Essa solicitação é semelhante ao primeiro segmento do [Fluxo de código de autorização do OAuth 2.0](v2-oauth2-auth-code-flow.md), com estas diferenças importantes:

* A solicitação deve incluir o escopo `openid` no parâmetro `scope`.
* O parâmetro `response_type` deve incluir `id_token`.
* A solicitação deve incluir o parâmetro `nonce` .

> [!IMPORTANT]
> Para solicitar com êxito um token de ID do ponto de extremidade/autorização, o registro do aplicativo no [ portal de registro](https://portal.azure.com) deve ter a concessão implícita de id_tokens habilitado na guia Autenticação (que define o sinalizador `oauth2AllowIdTokenImplicitFlow` no [manifesto do aplicativo](reference-app-manifest.md) para `true`). Se não estiver habilitado, um erro `unsupported_response` será retornado: "O valor fornecido para o parâmetro de entrada 'response_type' não é permitido para este cliente. O valor esperado é 'code' "

Por exemplo:

```HTTP
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=form_post
&scope=openid
&state=12345
&nonce=678910
```

| Parâmetro | Condição | Descrição |
| --- | --- | --- |
| `tenant` | Obrigatório | Você pode usar o valor `{tenant}` no caminho da solicitação para controlar quem pode entrar no aplicativo. Os valores permitidos são `common`, `organizations`, `consumers` e identificadores de locatário. Para saber mais, veja [noções básicas de protocolo](active-directory-v2-protocols.md#endpoints). |
| `client_id` | Obrigatório | A **ID do Aplicativo (cliente)** que a experiência [Portal do Microsoft Azure - Registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) atribui ao seu aplicativo. |
| `response_type` | Obrigatório | Deve incluir `id_token` para conexão do OpenID Connect. Ele também pode incluir outros `response_type` valores, como `code`. |
| `redirect_uri` | Recomendadas | O URI de redirecionamento do seu aplicativo, onde as respostas de autenticação podem ser enviadas e recebidas pelo aplicativo. Ele deve corresponder exatamente a um dos URIs de redirecionamento que você registrou no portal, com exceção de que ele deve ser codificado por URL. Se não estiver presente, o ponto de extremidade escolherá um redirect_uri registrado aleatoriamente para enviar o usuário de volta. |
| `scope` | Obrigatório | Uma lista de escopos separados por espaços. Para o OpenID Connect, é necessário incluir o escopo `openid`, que é traduzido para a permissão "Fazer seu logon" na interface do usuário de consentimento. Você também pode incluir outros escopos nesta solicitação para solicitar o consentimento. |
| `nonce` | Obrigatório | Um valor incluído na solicitação, gerado pelo aplicativo, que será incluído no valor do id_token resultante como uma declaração. O aplicativo pode verificar esse valor para reduzir os ataques de reprodução de token. Normalmente, o valor é uma cadeia de caracteres aleatória e exclusiva que pode ser usada para identificar a origem da solicitação. |
| `response_mode` | Recomendadas | Especifica o método que deve ser usado para enviar o authorization_code resultante de volta ao aplicativo. Pode ser `form_post` ou `fragment`. Para aplicativos Web, é recomendável usar `response_mode=form_post` para garantir a transferência mais segura de tokens para seu aplicativo. |
| `state` | Recomendadas | Um valor incluído na solicitação também será retornado na resposta do token. Pode ser uma cadeia de caracteres de qualquer conteúdo desejado. Um valor exclusivo gerado aleatoriamente que normalmente é usado para [impedir ataques de solicitação entre sites forjada](https://tools.ietf.org/html/rfc6749#section-10.12). O estado também é usado para codificar as informações sobre o estado do usuário no aplicativo antes da solicitação de autenticação ocorrida, como a página ou exibição em que ele estava. |
| `prompt` | Opcional | Indica o tipo de interação do usuário que é necessário. Os únicos valores válidos no momento são `login`, `none`, e `consent`. A declaração `prompt=login` força o usuário a digitar suas credenciais na solicitação, o que nega o logon único. A declaração `prompt=none` é o oposto. Essa declaração garante que o usuário não seja apresentado a nenhum prompt interativo. Se a solicitação não puder ser concluída silenciosamente por meio do logon único, a plataforma de identidade da Microsoft retornará um erro. A declaração `prompt=consent` aciona a caixa de diálogo de consentimento de OAuth depois que o usuário faz logon. A caixa de diálogo pede ao usuário para conceder permissões para o aplicativo. |
| `login_hint` | Opcional | Você pode usar esse parâmetro para preencher previamente o campo de nome de usuário/endereço de email da página de entrada do usuário, se você souber o nome de usuário com antecedência. Muitas vezes, os aplicativos usam esse parâmetro durante a reautenticação, depois de já terem extraído o nome de usuário de uma entrada anterior usando a declaração `preferred_username`. |
| `domain_hint` | Opcional | O realm do usuário em um diretório federado.  Ele ignorará o processo de descoberta baseada em email que o usuário passa na página de entrada para uma experiência de usuário um pouco mais simples. Para locatários federados por meio de um diretório local, como AD FS, isso geralmente resulta em uma entrada direta devido à sessão de logon existente. |

Nesse ponto, será solicitado que o usuário insira suas credenciais e conclua a autenticação. A plataforma de identidade da Microsoft verifica se o usuário consentiu as permissões indicadas no `scope` parâmetro de consulta. Se o usuário não consentiu em nenhuma dessas permissões, a plataforma de identidade da Microsoft solicitará que o usuário consentisse nas permissões necessárias. Leia mais sobre [aplicativos multilocatários, autorização e permissões](v2-permissions-and-consent.md).

Depois que o usuário é autenticado e concede consentimento, a plataforma de identidade da Microsoft retorna uma resposta ao seu aplicativo no URI de redirecionamento indicado usando o método especificado no `response_mode` parâmetro.

### <a name="successful-response"></a>Resposta bem-sucedida

Uma resposta bem-sucedida ao usar `response_mode=form_post` tem esta aparência:

```HTTP
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Parâmetro | Descrição |
| --- | --- |
| `id_token` | O token de ID que o aplicativo solicitou. Você pode usar o parâmetro `id_token` para verificar a identidade do usuário e iniciar uma sessão com o usuário. Para obter mais informações sobre tokens de identificação e seus conteúdos, consulte a [referência do `id_tokens`](id-tokens.md). |
| `state` | Se um parâmetro `state` estiver incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores de estado na solicitação e na resposta são idênticos. |

### <a name="error-response"></a>Resposta de erro

As respostas de erros também podem ser enviadas para o URI de redirecionamento para que o aplicativo possa lidar com elas. Uma resposta de erro tem esta aparência:

```HTTP
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parâmetro | Descrição |
| --- | --- |
| `error` | Uma cadeia de caracteres de códigos de erro que você pode usar para classificar tipos de erro que ocorrem e para responder aos erros. |
| `error_description` | Uma mensagem de erro específica que pode ajudar você a identificar a causa raiz de um erro de autenticação. |

### <a name="error-codes-for-authorization-endpoint-errors"></a>Códigos de erro para erros de ponto de extremidade de autorização

A tabela a seguir descreve os códigos de erro que podem ser retornados no parâmetro `error` da resposta de erro:

| Código do erro | Descrição | Ação do cliente |
| --- | --- | --- |
| `invalid_request` | Erro de protocolo, como um parâmetro obrigatório ausente. |Corrija e reenvie a solicitação. Esse é um erro de desenvolvimento normalmente identificado durante os testes iniciais. |
| `unauthorized_client` | O aplicativo cliente não pode solicitar um código de autorização. |Isso geralmente ocorre quando o aplicativo cliente não está registrado no Azure Active Directory ou não é adicionado ao locatário do Azure Active Directory do usuário. O aplicativo pode solicitar que o usuário instale o aplicativo e o adicione ao Azure AD. |
| `access_denied` | O consentimento negado pelo proprietário do recurso. |O aplicativo cliente pode notificar o usuário de que não pode continuar, a menos que o usuário consinta. |
| `unsupported_response_type` |O servidor de autorização não dá suporta ao tipo de resposta na solicitação. |Corrija e reenvie a solicitação. Esse é um erro de desenvolvimento normalmente identificado durante os testes iniciais. |
| `server_error` | O servidor encontrou um erro inesperado. |Tente novamente a solicitação. Esses erros podem resultar de condições temporárias. O aplicativo cliente pode explicar para o usuário que sua resposta está atrasada devido a um erro temporário. |
| `temporarily_unavailable` | O servidor está temporariamente muito ocupado para tratar da solicitação. |Tente novamente a solicitação. O aplicativo cliente pode explicar para o usuário que sua resposta está atrasada devido a uma condição temporária. |
| `invalid_resource` | O recurso de destino é inválido porque não existe, o Azure Active Directory não consegue encontrá-lo ou ele não está configurado corretamente. |Isso indica que o recurso, se ele existe, não foi configurado no locatário. O aplicativo pode solicitar que o usuário instale o aplicativo e o adicione ao Azure AD. |

## <a name="validate-the-id-token"></a>Validar o token de ID

Apenas receber um id_token nem sempre é suficiente para autenticar o usuário; Talvez você também precise validar a assinatura do id_token e verificar as declarações no token de acordo com os requisitos do aplicativo. Como todas as plataformas OIDC, a plataforma de identidade da Microsoft usa [JWTs (tokens Web JSON)](https://tools.ietf.org/html/rfc7519) e criptografia de chave pública para assinar tokens de ID e verificar se eles são válidos.

Nem todos os aplicativos se beneficiam da verificação do token de ID-aplicativos nativos e aplicativos de página única, por exemplo, raramente se beneficiam da validação do token de ID.  Alguém com acesso físico ao dispositivo (ou navegador) pode ignorar a validação de várias maneiras – desde editar o tráfego da Web para o dispositivo para fornecer tokens falsos e chaves para simplesmente depurar o aplicativo para ignorar a lógica de validação.  Por outro lado, os aplicativos Web e as APIs que usam um token de ID para autorização devem validar o token de ID com cuidado, pois eles recebem acesso aos dados.

Após a validação da assinatura do id_token, será necessário verificar algumas declarações. Consulte a [referência do `id_token`](id-tokens.md) para obter mais informações, incluindo [Validação de tokens](id-tokens.md#validating-an-id_token) e [Informações importantes sobre substituição de chave de assinatura](active-directory-signing-key-rollover.md). Há, pelo menos, uma disponível para a maioria das linguagens e plataformas.

Talvez você também queira validar declarações adicionais, dependendo do cenário. Algumas validações comuns incluem:

* Garantir que o usuário/organização tenha assinado para usar o aplicativo.
* Garantir que o usuário tenha autorização/privilégios adequados.
* Garantir que uma determinada intensidade de autenticação tenha ocorrido, como [autenticação multifator](../authentication/concept-mfa-howitworks.md).

Depois de ter validado o id_token, não será possível iniciar uma sessão com o usuário e usar declarações no id_token para obter informações sobre o usuário no seu aplicativo. Essas informações podem ser usadas para exibição, registros, personalização etc.

## <a name="protocol-diagram-access-token-acquisition"></a>Diagrama de protocolo: aquisição de token de acesso

Muitos aplicativos Web precisam não apenas conectar o usuário, mas acessar um serviço Web em nome desse usuário usando o OAuth. Esse cenário combina o OpenID Connect para autenticação de usuário enquanto obtém simultaneamente um código de autorização que pode ser usado para obter tokens de acesso se você estiver usando o fluxo do código de autorização do OAuth.

O fluxo total de entrada e de aquisição de token do OpenID Connect é similar ao próximo diagrama. Descrevemos cada etapa detalhadamente nas próximas seções do artigo.

![Protocolo OpenID Connect: aquisição de token](./media/v2-protocols-oidc/convergence-scenarios-webapp-webapi.svg)

## <a name="get-an-access-token-to-call-userinfo"></a>Obter um token de acesso para chamar UserInfo

Para adquirir um token para o ponto de extremidade OIDC UserInfo, modifique a solicitação de entrada:

```HTTP
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e        // Your registered Application ID
&response_type=id_token%20token                       // this will return both an id_token and an access token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F       // Your registered redirect URI, URL encoded
&response_mode=form_post                              // 'form_post' or 'fragment'
&scope=openid+profile+email                           // `openid` is required.  `profile` and `email` provide additional information in the UserInfo endpoint the same way they do in an ID token. 
&state=12345                                          // Any value, provided by your app
&nonce=678910                                         // Any value, provided by your app
```

Você também pode usar o [fluxo do código de autorização](v2-oauth2-auth-code-flow.md), o [fluxo de código do dispositivo](v2-oauth2-device-code.md)ou um token de [atualização](v2-oauth2-auth-code-flow.md#refresh-the-access-token) no lugar do `response_type=token` para obter um token para seu aplicativo.

> [!TIP]
> Clique no link a seguir para executar essa solicitação. Depois de entrar, seu navegador será redirecionado para `https://localhost/myapp/` , com um token de ID e um token na barra de endereços. Observe que essa solicitação usa `response_mode=fragment` apenas para fins de demonstração – para um webapp, recomendamos usar `form_post` para segurança adicional sempre que possível. 
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token%20token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=fragment&scope=openid+profile+email&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

### <a name="successful-token-response"></a>Resposta de token bem-sucedida

Uma resposta bem-sucedida do uso do `response_mode=form_post` tem a seguinte aparência:

```HTTP
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded
 access_token=eyJ0eXAiOiJKV1QiLCJub25jZSI6I....
 &token_type=Bearer
 &expires_in=3598
 &scope=email+openid+profile
 &id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI....
 &state=12345
```

Os parâmetros de resposta significam a mesma coisa, independentemente do fluxo usado para adquiri-los.

| Parâmetro | Descrição |
| --- | --- |
| `access_token` | O token que será usado para chamar o ponto de extremidade de UserInfo.|
| `token_type` | Sempre "portador" |
| `expires_in`| Quanto tempo até que o token de acesso expire, em segundos. |
| `scope` | As permissões concedidas no token de acesso.  Observe que, como o ponto de extremidade de UserInfo é hospedado no MS Graph, pode haver escopos de grafo adicionais listados aqui (por exemplo, User. Read) se eles foram concedidos anteriormente ao aplicativo.  Isso ocorre porque um token para um determinado recurso sempre inclui todas as permissões atualmente concedidas ao cliente.  |
| `id_token` | O token de ID que o aplicativo solicitou. Você pode usar o token de ID para verificar a identidade do usuário e iniciar uma sessão com o usuário. Você encontrará mais detalhes sobre tokens de ID e seu conteúdo na [referência do `id_tokens`](id-tokens.md). |
| `state` | Se um parâmetro de estado for incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores de estado na solicitação e na resposta são idênticos. |

### <a name="error-response"></a>Resposta de erro

As respostas de erro também podem ser enviadas ao URI de redirecionamento para que o aplicativo possa tratá-las adequadamente. Uma resposta de erro tem esta aparência:

```HTTP
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parâmetro | Descrição |
| --- | --- |
| `error` | Uma cadeia de caracteres de códigos de erro que você pode usar para classificar tipos de erro que ocorrem e para responder aos erros. |
| `error_description` | Uma mensagem de erro específica que pode ajudar você a identificar a causa raiz de um erro de autenticação. |

Para obter uma descrição dos possíveis códigos de erro e as respostas recomendadas do cliente, veja [Códigos de erro para erros de ponto de extremidade de autorização](#error-codes-for-authorization-endpoint-errors).

Quando você tiver um código de autorização e um token de ID, poderá conectar o usuário e obter tokens de acesso em seu nome. Para conectar o usuário, você deve validar o token de ID [exatamente como descrito ](id-tokens.md#validating-an-id_token). Para obter tokens de acesso, siga as etapas descritas na [documentação do fluxo de código OAuth](v2-oauth2-auth-code-flow.md#request-an-access-token).

### <a name="calling-the-userinfo-endpoint"></a>Chamando o ponto de extremidade de UserInfo

Examine a [documentação do UserInfo](userinfo.md#calling-the-api) para ver como a chamada do ponto de extremidade UserInfo com esse token.

## <a name="send-a-sign-out-request"></a>Enviar uma solicitação de saída

Quando você deseja desconectar o usuário do aplicativo, não é suficiente limpar os cookies do aplicativo ou encerrar a sessão do usuário. Você também deve redirecionar o usuário para a plataforma de identidade da Microsoft para sair. Se você não fizer isso, o usuário se reautenticará em seu aplicativo sem inserir suas credenciais novamente, pois ele terá uma sessão de logon único válida com a plataforma de identidade da Microsoft.

Você pode redirecionar o usuário para o `end_session_endpoint` listado no documento de metadados do OpenID Connect:

```HTTP
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Parâmetro | Condição | Descrição |
| ----------------------- | ------------------------------- | ------------ |
| `post_logout_redirect_uri` | Recomendadas | A URL para a qual o usuário é redirecionado depois de sair com êxito. Se o parâmetro não estiver incluído, o usuário será mostrado uma mensagem genérica gerada pela plataforma de identidade da Microsoft. Esta URL deve corresponder exatamente a um redirecionamento de URIs registrado para seu aplicativo no portal de registro de aplicativo. |

## <a name="single-sign-out"></a>Logout único

Quando você redireciona o usuário para o `end_session_endpoint` , a plataforma de identidade da Microsoft limpa a sessão do usuário no navegador. No entanto, o usuário pode ainda entrar em outros aplicativos que usam contas da Microsoft para autenticação. Para permitir que esses aplicativos desconectem o usuário simultaneamente, a plataforma de identidade da Microsoft envia uma solicitação HTTP GET para o registrado `LogoutUrl` de todos os aplicativos aos quais o usuário está conectado no momento. Os aplicativos devem responder a essa solicitação, limpando as sessões que identificam o usuário e retornando uma resposta `200`. Se você deseja dar suporte um logout único em seu aplicativo, deve implementar um `LogoutUrl` no código do aplicativo. Você pode configurar a `LogoutUrl` no portal de registro do aplicativo.

## <a name="next-steps"></a>Próximas etapas

* Examine a [documentação do UserInfo](userinfo.md)
* Saiba como [Personalizar os valores em um token](active-directory-claims-mapping.md) com dados de seus sistemas locais. 
* Saiba como [incluir declarações padrão adicionais em tokens](active-directory-optional-claims.md).  
