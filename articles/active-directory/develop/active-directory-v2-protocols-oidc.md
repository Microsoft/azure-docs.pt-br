---
title: Azure Active Directory v2.0 e o protocolo OpenID Connect | Microsoft Docs
description: "Compile aplicativos Web usando a implementação v.2.0 do Azure AD do protocolo de autenticação OpenID Connect."
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: a4875997-3aac-4e4c-b7fe-2b4b829151ce
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: d63692f02b3dec50a1e7df034b8915bb450b4cfd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# Azure Active Directory v2.0 e o protocolo OpenID Connect
O OpenID Connect é um protocolo de autenticação baseado no OAuth 2.0 que você pode usar para assinar com segurança em um usuário a um aplicativo Web. Quando você usa a implementação do ponto de extremidade v2.0 do OpenID Connect, você pode adicionar entrada e acesso à API aos seus aplicativos baseados na Web. Neste artigo, mostraremos como fazer isso independentemente do idioma. Descreveremos como enviar e receber mensagens HTTP sem usar qualquer uma das bibliotecas de software livre da Microsoft.

> [!NOTE]
> O ponto de extremidade v2.0 não dá suporte a todos os cenários e recursos do Azure Active Directory. Para determinar se você deve usar o ponto de extremidade v2.0, leia sobre as [limitações da v2.0](active-directory-v2-limitations.md).
> 
> 

O [OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) estende o protocolo de *autorização* do OAuth 2.0 para uso como um protocolo de *autenticação*, o que permite executar o logon único usando o OAuth. O OpenID Connect apresenta o conceito de um *token de ID*, que é um token de segurança que permite ao cliente verificar a identidade do usuário. O token de ID também obtém informações de perfil básico sobre o usuário. Como o OpenID Connect estende o OAuth 2.0, os aplicativos podem adquirir *access_tokens* com segurança, os quais podem ser usados para acessar os recursos protegidos por um [servidor de autorização](active-directory-v2-protocols.md#the-basics). É recomendável que você use o OpenID Connect se estiver criando um [aplicativo Web](active-directory-v2-flows.md#web-apps) que fica hospedado em um servidor e é acessado por meio de um navegador.

## Diagrama de protocolo: Entrar
O fluxo de entrada mais básico tem as etapas mostradas no diagrama seguinte. Descrevemos cada etapa detalhadamente neste artigo.

![Protocolo OpenID Connect: entrar](../../media/active-directory-v2-flows/convergence_scenarios_webapp.png)

## Obter o documento de metadados do OpenID Connect
O OpenID Connect descreve um documento de metadados que contém a maioria das informações necessárias para que um aplicativo inicie uma sessão. Isso inclui informações como as URLs a serem usadas e o local das chaves de assinatura públicas do serviço. Para o ponto de extremidade v2.0, este é o documento de metadados do OpenID Connect que você deve usar:

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
```

O `{tenant}` pode ter um de quatro valores:

| Valor | Descrição |
| --- | --- |
| `common` |Os usuários com uma conta pessoal da Microsoft e uma conta corporativa ou de estudante do Azure Active Directory (Azure AD) podem entrar aplicativo. |
| `organizations` |Somente os usuários com contas corporativas ou de estudante do Azure AD podem se conectar ao aplicativo. |
| `consumers` |Somente os usuários com uma conta pessoal da Microsoft podem entrar no aplicativo. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` ou `contoso.onmicrosoft.com` |Somente os usuários com uma conta corporativa ou de estudante de um locatário específico do Azure AD podem entrar no aplicativo. É possível usar o nome de domínio amigável do locatário do Azure AD ou o identificador GUID de locatário. |

Os metadados são um documento JSON (JavaScript Object Notation) simples. Veja o trecho a seguir para obter um exemplo. O conteúdo do trecho é totalmente descrito na [especificação do OpenID Connect](https://openid.net).

```
{
  "authorization_endpoint": "https:\/\/login.microsoftonline.com\/common\/oauth2\/v2.0\/authorize",
  "token_endpoint": "https:\/\/login.microsoftonline.com\/common\/oauth2\/v2.0\/token",
  "token_endpoint_auth_methods_supported": [
    "client_secret_post",
    "private_key_jwt"
  ],
  "jwks_uri": "https:\/\/login.microsoftonline.com\/common\/discovery\/v2.0\/keys",

  ...

}
```

Normalmente, você usaria este documento de metadados para configurar uma biblioteca do OpenID Connect ou o SDK. A biblioteca usaria os metadados para fazer seu trabalho. No entanto, se você não estiver usando uma biblioteca de pré-compilação do OpenID Connect, é possível seguir as etapas no restante deste artigo para iniciar uma sessão em um aplicativo web usando o ponto de extremidade v2.0.

## Enviar a solicitação de conexão
Quando o aplicativo Web precisa autenticar o usuário, ele pode direcionar o usuário para o ponto de extremidade `/authorize` . Essa solicitação é semelhante ao primeiro segmento do [Fluxo de código de autorização do OAuth 2.0](active-directory-v2-protocols-oauth-code.md), com estas diferenças importantes:

* A solicitação deve incluir o escopo `openid` no parâmetro `scope`.
* O parâmetro `response_type` deve incluir `id_token`.
* A solicitação deve incluir o parâmetro `nonce` .

Por exemplo:

```
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

> [!TIP]
> Clique no link a seguir para executar essa solicitação. Depois de entrar, seu navegador será redirecionado para https://localhost/myapp/, com um token de ID na barra de endereços. Observe que esta solicitação usa `response_mode=query` (somente para fins de demonstração). É recomendável usar o `response_mode=form_post`.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=query&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>
> 
> 

| Parâmetro | Condição | Descrição |
| --- | --- | --- |
| locatário |Obrigatório |Você pode usar o valor `{tenant}` no caminho da solicitação para controlar quem pode entrar no aplicativo. Os valores permitidos são `common`, `organizations`, `consumers` e identificadores de locatário. Para saber mais, veja [noções básicas de protocolo](active-directory-v2-protocols.md#endpoints). |
| client_id |Obrigatório |A ID de aplicativo do [Portal de Registro de Aplicativo](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) atribuída ao seu aplicativo. |
| response_type |Obrigatório |Deve incluir `id_token` para conexão do OpenID Connect. Ele também pode incluir outros `response_types` valores, como `code`. |
| redirect_uri |Recomendadas |O URI de redirecionamento do seu aplicativo, onde as respostas de autenticação podem ser enviadas e recebidas pelo aplicativo. Ele deve corresponder exatamente a um dos URIs de redirecionamento que você registrou no portal, com exceção de que ele deve ser codificado por URL. |
| scope |Obrigatório |Uma lista de escopos separados por espaços. Para o OpenID Connect, é necessário incluir o escopo `openid`, que é traduzido para a permissão "Fazer seu logon" na interface do usuário de consentimento. Você também pode incluir outros escopos nesta solicitação para solicitar o consentimento. |
| nonce |Obrigatório |Um valor incluído na solicitação, gerado pelo aplicativo, que será incluído no valor do id_token resultante como uma declaração. O aplicativo pode verificar esse valor para reduzir os ataques de reprodução de token. Normalmente, o valor é uma cadeia de caracteres aleatória e exclusiva que pode ser usada para identificar a origem da solicitação. |
| response_mode |Recomendadas |Especifica o método que deve ser usado para enviar o authorization_code resultante de volta ao aplicativo. Pode ser `query`, `form_post` ou `fragment`. Para aplicativos Web, é recomendável usar `response_mode=form_post` para garantir a transferência mais segura de tokens para seu aplicativo. |
| state |Recomendadas |Um valor incluído na solicitação também será retornado na resposta do token. Pode ser uma cadeia de caracteres de qualquer conteúdo desejado. Um valor exclusivo gerado aleatoriamente que normalmente é usado para [impedir ataques de solicitação entre sites forjada](http://tools.ietf.org/html/rfc6749#section-10.12). O estado também é usado para codificar as informações sobre o estado do usuário no aplicativo antes da solicitação de autenticação ocorrida, como a página ou exibição em que ele estava. |
| prompt |Opcional |Indica o tipo de interação do usuário que é necessário. Os únicos valores válidos no momento são `login`, `none`, e `consent`. A declaração `prompt=login` força o usuário a digitar suas credenciais na solicitação, o que nega o logon único. A declaração `prompt=none` é o oposto. Essa declaração garante que o usuário não seja apresentado a nenhum prompt interativo. Se a solicitação não puder ser concluída silenciosamente por meio de logon único, o ponto de extremidade v2.0 retornará um erro. A declaração `prompt=consent` aciona a caixa de diálogo de consentimento de OAuth depois que o usuário faz logon. A caixa de diálogo pede ao usuário para conceder permissões para o aplicativo. |
| login_hint |Opcional |Você pode usar esse parâmetro para preencher previamente o campo de nome de usuário/endereço de email da página de entrada do usuário, se você souber o nome de usuário com antecedência. Muitas vezes, os aplicativos usam esse parâmetro durante a reautenticação, depois de já terem extraído o nome de usuário de uma entrada anterior usando a declaração `preferred_username`. |
| domain_hint |Opcional |Esse valor pode ser `consumers` ou `organizations`. Se for incluído, ele ignorará o processo de descoberta baseada em email que o usuário passa na página de entrada da v2.0 para uma experiência de usuário um pouco mais simples. Geralmente, os aplicativos usarão esse parâmetro durante a reautenticação, extraindo a declaração `tid` do token de ID. Se o valor da declaração `tid` for `9188040d-6c67-4c5b-b112-36a304b66dad`, use `domain_hint=consumers`. Caso contrário, use `domain_hint=organizations`. |

Nesse ponto, será solicitado que o usuário insira suas credenciais e conclua a autenticação. O ponto de extremidade v2.0 verifique se o usuário consentiu as permissões indicadas no parâmetro de consulta `scope` . Se o usuário não tiver consentido nenhuma dessas permissões, o ponto de extremidade v2.0 solicitará que o usuário consinta as permissões necessárias. Você pode ler mais sobre [aplicativos multilocatários, autorização e permissões](active-directory-v2-scopes.md).

Depois que o usuário se autentica e dá consentimento, o ponto de extremidade v2.0 retorna uma resposta ao aplicativo no URI de redirecionamento indicado usando o método especificado no parâmetro `response_mode`.

### Resposta bem-sucedida
Uma resposta bem-sucedida ao usar `response_mode=form_post` tem esta aparência:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Parâmetro | Descrição |
| --- | --- |
| id_token |O token de ID que o aplicativo solicitou. Você pode usar o parâmetro `id_token` para verificar a identidade do usuário e iniciar uma sessão com o usuário. Para obter mais detalhes sobre tokens de ID e seu conteúdo, veja a [referência os tokens de ponto de extremidade v2.0](active-directory-v2-tokens.md). |
| state |Se um parâmetro `state` estiver incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores de estado na solicitação e na resposta são idênticos. |

### Resposta de erro
As respostas de erros também podem ser enviadas para o URI de redirecionamento para que o aplicativo possa lidar com elas. Uma resposta de erro tem esta aparência:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parâmetro | Descrição |
| --- | --- |
| error |Uma cadeia de caracteres de códigos de erro que você pode usar para classificar tipos de erro que ocorrem e para responder aos erros. |
| error_description |Uma mensagem de erro específica que pode ajudar você a identificar a causa raiz de um erro de autenticação. |

### Códigos de erro para erros de ponto de extremidade de autorização
A tabela a seguir descreve os códigos de erro que podem ser retornados no parâmetro `error` da resposta de erro:

| Código do erro | Descrição | Ação do cliente |
| --- | --- | --- |
| invalid_request |Erro de protocolo, como um parâmetro obrigatório ausente. |Corrija e reenvie a solicitação. Esse é um erro de desenvolvimento normalmente identificado durante os testes iniciais. |
| unauthorized_client |O aplicativo cliente não pode solicitar um código de autorização. |Isso geralmente ocorre quando o aplicativo cliente não está registrado no Azure AD ou não é adicionado ao locatário do Azure AD do usuário. O aplicativo pode solicitar que o usuário instale o aplicativo e o adicione ao Azure AD. |
| access_denied |O consentimento negado pelo proprietário do recurso. |O aplicativo cliente pode notificar o usuário de que não pode continuar, a menos que o usuário consinta. |
| unsupported_response_type |O servidor de autorização não dá suporta ao tipo de resposta na solicitação. |Corrija e reenvie a solicitação. Esse é um erro de desenvolvimento normalmente identificado durante os testes iniciais. |
| server_error |O servidor encontrou um erro inesperado. |Tente novamente a solicitação. Esses erros podem resultar de condições temporárias. O aplicativo cliente pode explicar ao usuário que sua resposta está atrasada devido a um erro temporário. |
| temporarily_unavailable |O servidor está temporariamente muito ocupado para tratar da solicitação. |Tente novamente a solicitação. O aplicativo cliente pode explicar para o usuário que sua resposta está atrasada devido a uma condição temporária. |
| invalid_resource |O recurso de destino é inválido porque não existe, o Azure AD não consegue encontrá-lo ou ele não está configurado corretamente. |Isso indica que o recurso, se ele existe, não foi configurado no locatário. O aplicativo pode solicitar que o usuário instale o aplicativo e o adicione ao Azure AD. |

## Validar o token de ID
Receber um tokend e ID não é suficiente para autenticar o usuário. Você também deve validar a assinatura do token de ID e verificar as declarações no token de acordo com os requisitos do seu aplicativo. O ponto de extremidade v2.0 usa [JWTs (Tokens Web JSON)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) e criptografia de chave pública para assinar tokens e verificar se eles são válidos.

Você pode escolher validar o token de ID no código do cliente, mas uma prática comum é enviar o token de ID para um servidor de back-end e executar a validação nele. Depois que tiver validado a assinatura do token de ID, você precisará verificar algumas declarações. Para saber mais, incluindo mais sobre [tokens de validação](active-directory-v2-tokens.md#validating-tokens) e [informações importantes sobre a substituição de chave de assinatura](active-directory-v2-tokens.md#validating-tokens), veja a [referência de tokens v2.0](active-directory-v2-tokens.md). É recomendável usar uma biblioteca para analisar e validar os tokens. Há pelo menos uma dessas bibliotecas disponível para a maioria das linguagens e plataformas.
<!--TODO: Improve the information on this-->

Talvez você também queira validar declarações adicionais, dependendo do cenário. Algumas validações comuns incluem:

* Garanta que o usuário/organização tenha feito inscrição para usar o aplicativo.
* Garanta que o usuário tenha privilégios ou autorização.
* Garanta que uma determinada intensidade de autenticação tenha ocorrido, como autenticação multifator.

Para saber mais sobre as declarações em um token de ID, veja a [referência de tokens do ponto de extremidade v2.0](active-directory-v2-tokens.md).

Após validar completamente o token de ID, você poderá iniciar uma sessão com o usuário. Use as declarações no token de ID para obter informações sobre o usuário em seu aplicativo. Você pode usar essas informações para exibição, registros, autorizações e assim por diante.

## Enviar uma solicitação de saída
Quando você deseja desconectar o usuário do aplicativo, não é suficiente limpar os cookies do aplicativo ou encerrar a sessão do usuário. Você também deve redirecionar o usuário para o ponto de extremidade v2.0 para desconexão. Se você não fizer isso, o usuário poderá se autenticar novamente no seu aplicativo sem inserir as credenciais novamente, pois continuará em uma sessão de logon único válida com o ponto de extremidade v2.0.

Você pode redirecionar o usuário para o `end_session_endpoint` listado no documento de metadados do OpenID Connect:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Parâmetro | Condição | Descrição |
| ----------------------- | ------------------------------- | ------------ |
| post_logout_redirect_uri | Recomendadas | A URL para a qual o usuário é redirecionado após o logout bem-sucedido. Se o parâmetro não for incluído, o usuário receberá uma mensagem genérica gerada pelo ponto de extremidade v2.0. Esta URL deve corresponder exatamente a um redirecionamento de URIs registrado para seu aplicativo no portal de registro de aplicativo.  |

## Logout único
Quando você redireciona o usuário para o `end_session_endpoint`, o ponto de extremidade v2.0 limpa a sessão do usuário do navegador. No entanto, o usuário pode ainda entrar em outros aplicativos que usam contas da Microsoft para autenticação. Para permitir que esses aplicativos desconectem o usuário simultaneamente, o ponto de extremidade v2.0 envia uma solicitação HTTP GET para o `LogoutUrl` de todos os aplicativos aos quais o usuário está atualmente conectado. Os aplicativos devem responder a essa solicitação, limpando as sessões que identificam o usuário e retornando uma resposta `200`.  Se você deseja dar suporte um logout único em seu aplicativo, você deve implementar um `LogoutUrl` no código do aplicativo.  Você pode configurar a `LogoutUrl` no portal de registro do aplicativo.

## Diagrama de Protocolos: aquisição de Token
Muitos aplicativos Web precisam não apenas conectar o usuário, mas acessar um serviço Web em nome desse usuário usando o OAuth. Esse cenário combina o OpenID Connect para autenticação de usuário enquanto obtém simultaneamente um código de autorização que pode ser usado para obter tokens de acesso se você estiver usando o fluxo do código de autorização do OAuth.

O fluxo total de entrada e de aquisição de token do OpenID Connect é similar ao próximo diagrama. Descrevemos cada etapa detalhadamente nas próximas seções do artigo.

![Protocolo OpenID Connect: aquisição de token](../../media/active-directory-v2-flows/convergence_scenarios_webapp_webapi.png)

## Obter tokens de acesso
Para obter tokens de acesso, modifique a solicitação de entrada:

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e        // Your registered Application ID
&response_type=id_token%20code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F       // Your registered redirect URI, URL encoded
&response_mode=form_post                              // 'query', 'form_post', or 'fragment'
&scope=openid%20                                      // Include both 'openid' and scopes that your app needs  
offline_access%20                                         
https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&state=12345                                          // Any value, provided by your app
&nonce=678910                                         // Any value, provided by your app
```

> [!TIP]
> Clique no link a seguir para executar essa solicitação. Depois de entrar, o navegador é redirecionado para https://localhost/myapp/, com um token de ID e um código na barra de endereços. Observe que esta solicitação usa `response_mode=query` (somente para fins de demonstração). É recomendável usar o `response_mode=form_post`.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token%20code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>
> 
> 

Com a inclusão de escopos de permissão na solicitação e usando `response_type=id_token code`, o ponto de extremidade v2.0 garante que o usuário consentiu as permissões indicadas no parâmetro de consulta `scope`. Ele retorna um código de autorização para seu aplicativo para o exchange para um token de acesso.

### Resposta bem-sucedida
Uma resposta bem-sucedida do uso do `response_mode=form_post` tem a seguinte aparência:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Parâmetro | Descrição |
| --- | --- |
| id_token |O token de ID que o aplicativo solicitou. Você pode usar o token de ID para verificar a identidade do usuário e iniciar uma sessão com o usuário. Você encontrará mais detalhes sobre tokens de ID e seu conteúdo na [referência os tokens de ponto de extremidade v2.0](active-directory-v2-tokens.md). |
| código |O código de autorização que o aplicativo solicitou. O aplicativo pode usar o código de autorização para solicitar um token de acesso para o recurso de destino. Um código de autorização tem uma duração muito curta. Normalmente, um código de autorização expira em cerca de 10 minutos. |
| state |Se um parâmetro de estado estiver incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores de estado na solicitação e na resposta são idênticos. |

### Resposta de erro
As respostas de erro também podem ser enviadas ao URI de redirecionamento para que o aplicativo possa tratá-las adequadamente. Uma resposta de erro tem esta aparência:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parâmetro | Descrição |
| --- | --- |
| error |Uma cadeia de caracteres de códigos de erro que você pode usar para classificar tipos de erro que ocorrem e para responder aos erros. |
| error_description |Uma mensagem de erro específica que pode ajudar você a identificar a causa raiz de um erro de autenticação. |

Para obter uma descrição dos possíveis códigos de erro e as respostas recomendadas do cliente, veja [Códigos de erro para erros de ponto de extremidade de autorização](#error-codes-for-authorization-endpoint-errors).

Quando você tiver um código de autorização e um token de ID, poderá conectar o usuário e obter tokens de acesso em seu nome. Para conectar o usuário, você deve validar o token de ID [exatamente como descrito ](#validate-the-id-token). Para obter tokens de acesso, siga as etapas descritas em nossa [documentação do protocolo OAuth](active-directory-v2-protocols-oauth-code.md#request-an-access-token).
