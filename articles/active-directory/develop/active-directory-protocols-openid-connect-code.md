---
title: "Entender o fluxo de código de autorização do OpenID Connect no Azure AD | Microsoft Docs"
description: "Este artigo descreve como usar mensagens HTTP para autorizar o acesso a aplicativos Web e a APIs da Web em seu locatário usando o Azure Active Directory e o OpenID Connect."
services: active-directory
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 29142f7e-d862-4076-9a1a-ecae5bcd9d9b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 1bb944997caa0c43354e82bf9b1a70e3e104a476
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# Autorizar o acesso aos aplicativos Web usando o OpenID Connect e o Azure Active Directory
[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) é uma camada de identidade simples criada sobre o protocolo OAuth 2.0. OAuth 2.0 define os mecanismos para obter e usar **tokens de acesso** para acessar recursos protegidos, mas eles não definem os métodos padrão para fornecer informações de identidade. O OpenID Connect implementa a autenticação como uma extensão para o processo de autorização do OAuth 2.0. Ele fornece informações sobre o usuário final na forma de um `id_token` que verifica a identidade do usuário e fornece informações básicas de perfil sobre o usuário.

O OpenID Connect é a nossa recomendação se você estiver criando um aplicativo Web hospedado em um servidor e acessado por meio de um navegador.


[!INCLUDE [active-directory-protocols-getting-started](../../../includes/active-directory-protocols-getting-started.md)] 

## Fluxo de autenticação usando o OpenID Connect
O fluxo de credenciais mais básico contém as seguintes etapas: cada um delas é descrita em detalhes abaixo.

![Fluxo de autenticação usando o OpenId Connect](media/active-directory-protocols-openid-connect-code/active-directory-oauth-code-flow-web-app.png)

## Documento de metadados do OpenID Connect

O OpenID Connect descreve um documento de metadados que contém a maioria das informações necessárias para que um aplicativo inicie uma sessão. Isso inclui informações como as URLs a serem usadas e o local das chaves de assinatura públicas do serviço. O documento de metadados do OpenID Connect pode ser localizado em:

```
https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration
```
Os metadados são um documento JSON (JavaScript Object Notation) simples. Veja o trecho a seguir para obter um exemplo. O conteúdo do trecho é totalmente descrito na [especificação do OpenID Connect](https://openid.net).

```
{
    "authorization_endpoint": "https://login.microsoftonline.com/common/oauth2/authorize",
    "token_endpoint": "https://login.microsoftonline.com/common/oauth2/token",
    "token_endpoint_auth_methods_supported":
    [
        "client_secret_post",
        "private_key_jwt"
    ],
    "jwks_uri": "https://login.microsoftonline.com/common/discovery/keys"
    
    ...
}
```

## Enviar a solicitação de conexão
Quando o aplicativo Web precisa autenticar o usuário, ele deve direcionar o usuário para o ponto de extremidade `/authorize` . Essa solicitação é semelhante ao primeiro segmento do [Fluxo de código de autorização do OAuth 2.0](active-directory-protocols-oauth-code.md), com algumas diferenças importantes:

* A solicitação deve incluir o escopo `openid` no parâmetro `scope`.
* O parâmetro `response_type` deve incluir `id_token`.
* A solicitação deve incluir o parâmetro `nonce` .

Dessa forma, uma solicitação de exemplo teria esta aparência:

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=form_post
&scope=openid
&state=12345
&nonce=7362CAEA-9CA5-4B43-9BA3-34D7C303EBA7
```

| Parâmetro |  | Descrição |
| --- | --- | --- |
| locatário |obrigatório |O valor `{tenant}` no caminho da solicitação pode ser usado para controlar quem pode entrar no aplicativo.  Os valores permitidos são identificadores de locatário, por exemplo, `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` ou `contoso.onmicrosoft.com` ou `common` para tokens independentes de locatário |
| client_id |obrigatório |A ID de aplicativo atribuída ao seu aplicativo quando você o registra no Azure AD. Você pode encontrá-la no Portal do Azure. Clique em **Azure Active Directory**, clique em **Registros do Aplicativo**, escolha o aplicativo e localize a ID do Aplicativo na página do aplicativo. |
| response_type |obrigatório |Deve incluir `id_token` para conexão do OpenID Connect.  Também pode incluir outros response_types, como `code`. |
| scope |obrigatório |Uma lista de escopos separados por espaços.  Para o OpenID Connect, é necessário incluir o escopo `openid`, que é traduzido para a permissão "Fazer seu logon" na interface do usuário de consentimento.  Você também pode incluir outros escopos nesta solicitação para solicitar o consentimento. |
| nonce |obrigatório |Um valor incluído na solicitação, gerado pelo aplicativo, que é incluído no `id_token` resultante como uma declaração.  O aplicativo pode verificar esse valor para reduzir os ataques de reprodução de token.  Normalmente, o valor é uma cadeia de caracteres aleatória e exclusiva ou um GUID que pode ser usado para identificar a origem da solicitação. |
| redirect_uri |recomendável |O redirect_uri do seu aplicativo, onde as respostas de autenticação podem ser enviadas e recebidas pelo aplicativo.  Ele deve corresponder exatamente a um dos redirect_uris que você registrou no portal, com exceção de que ele deve ser codificado por url. |
| response_mode |recomendável |Especifica o método que deve ser usado para enviar o authorization_code resultante de volta ao aplicativo.  Os valores com suporte são `form_post` para *postagem no formato HTTP* ou `fragment` para *fragmento de URL*.  Em aplicativos Web, é recomendável usar `response_mode=form_post` a fim de garantir a transferência mais segura de tokens para seu aplicativo. |
| state |recomendável |Um valor incluído na solicitação que retorna na resposta do token.  Pode ser uma cadeia de caracteres de qualquer conteúdo desejado.  Um valor exclusivo gerado aleatoriamente normalmente é usado para [impedir ataques de solicitação intersite forjada](http://tools.ietf.org/html/rfc6749#section-10.12).  O estado também é usado para codificar as informações sobre o estado do usuário no aplicativo antes da solicitação de autenticação ocorrida, como a página ou exibição em que ele estava. |
| prompt |opcional |Indica o tipo de interação do usuário que é necessário.  Atualmente, os únicos valores válidos são "login", "none" e "consent".  `prompt=login` força o usuário a inserir suas credenciais na solicitação, negando o logon único.  `prompt=none` é o oposto — ele garante que nenhum prompt interativo seja apresentado ao usuário.  Se a solicitação não puder ser concluída silenciosamente por meio de logon único, o ponto de extremidade retornará um erro.  `prompt=consent` dispara a caixa de diálogo de consentimento do OAuth depois que o usuário se conecta, solicitando que ele conceda permissões ao aplicativo. |
| login_hint |opcional |Pode ser usado para preencher previamente o campo de nome de usuário/endereço de email da página de entrada do usuário, se você souber o nome de usuário com antecedência.  Geralmente, os aplicativos usam esse parâmetro durante a reautenticação, após já terem extraído o nome de usuário de uma entrada anterior usando a declaração `preferred_username`. |

Nesse ponto, é solicitado que o usuário insira suas credenciais e conclua a autenticação.

### Resposta de exemplo
Uma resposta de exemplo, após a autenticação do usuário, poderia ser assim:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Parâmetro | Descrição |
| --- | --- |
| id_token |O `id_token` solicitado pelo aplicativo. Você pode usar o `id_token` para verificar a identidade do usuário e iniciar uma sessão com o usuário. |
| state |Um valor incluído na solicitação que também retorna na resposta do token. Um valor exclusivo gerado aleatoriamente que normalmente é usado para [impedir ataques de solicitação intersite forjada](http://tools.ietf.org/html/rfc6749#section-10.12).  O estado também é usado para codificar as informações sobre o estado do usuário no aplicativo antes da solicitação de autenticação ocorrida, como a página ou exibição em que ele estava. |

### Resposta de erro
As respostas de erro também podem ser enviadas ao `redirect_uri` para que o aplicativo possa tratá-las adequadamente:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| . | Descrição |
| --- | --- |
| error |Uma cadeia de caracteres de códigos de erro que pode ser usada para classificar tipos de erro que ocorrem e pode ser usada para responder aos erros. |
| error_description |Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro de autenticação. |

#### Códigos de erro para erros de ponto de extremidade de autorização
A tabela a seguir descreve os vários códigos de erro que podem ser retornados no parâmetro `error` da resposta de erro.

| Código do Erro | Descrição | Ação do Cliente |
| --- | --- | --- |
| invalid_request |Erro de protocolo, como um parâmetro obrigatório ausente. |Corrija e reenvie a solicitação. Esse é um erro de desenvolvimento que, normalmente, é capturado durante os testes iniciais. |
| unauthorized_client |O aplicativo cliente não tem permissão para solicitar um código de autorização. |Isso geralmente ocorre quando o aplicativo cliente não está registrado no Azure AD ou não é adicionado ao locatário do Azure AD do usuário. O aplicativo pode solicitar que o usuário instale o aplicativo e o adicione ao Azure AD. |
| access_denied |Consentimento negado pelo proprietário do recurso |O aplicativo cliente pode notificar o usuário de que não pode continuar, a menos que o usuário consinta. |
| unsupported_response_type |O servidor de autorização não dá suporta ao tipo de resposta na solicitação. |Corrija e reenvie a solicitação. Esse é um erro de desenvolvimento que, normalmente, é capturado durante os testes iniciais. |
| server_error |O servidor encontrou um erro inesperado. |Tente novamente a solicitação. Esses erros podem resultar de condições temporárias. O aplicativo cliente pode explicar ao usuário que sua resposta está atrasada devido a um erro temporário. |
| temporarily_unavailable |O servidor está temporariamente muito ocupado para tratar da solicitação. |Tente novamente a solicitação. O aplicativo cliente pode explicar para o usuário que sua resposta está atrasada devido a uma condição temporária. |
| invalid_resource |O recurso de destino é inválido porque não existe, o Azure AD não consegue encontrá-lo ou ele não está configurado corretamente. |Isso indica que o recurso, se ele existe, não foi configurado no locatário. O aplicativo pode solicitar que o usuário instale o aplicativo e o adicione ao Azure AD. |

## Validar o id_token
Apenas receber um `id_token` não é suficiente para autenticar o usuário. Você deve validar a assinatura e verificar as declarações no `id_token`, de acordo com os requisitos do aplicativo. O ponto de extremidade do Azure AD usa JWTs (Tokens Web JSON) e criptografia de chave pública para assinar tokens e verificar se eles são válidos.

Você pode escolher validar o `id_token` no código do cliente, mas uma prática comum é enviar o `id_token` para um servidor de back-end e executar a validação nele. Após a validação da assinatura do `id_token`, haverá algumas declarações que você precisará verificar.

Talvez você também queira validar declarações adicionais, dependendo do cenário. Algumas validações comuns incluem:

* Garantir que o usuário/organização tenha assinado para usar o aplicativo.
* Garantir que o usuário tenha autorização/privilégios adequados.
* Garantir que uma determinada intensidade de autenticação tenha ocorrido, como autenticação multifator.

Depois de ter validado o `id_token`, você poderá iniciar uma sessão com o usuário e usar declarações no `id_token` para obter informações sobre o usuário no seu aplicativo. Essas informações podem ser usadas para exibição, registros, autorizações, etc. Para saber mais sobre os tipos de token e declaração, leia [Tipos de token e de declaração com suporte](active-directory-token-and-claims.md).

## Enviar uma solicitação de saída
Quando você deseja conectar o usuário do aplicativo, não é suficiente limpar os cookies do aplicativo e encerrar a sessão do usuário.  Você também deve redirecionar o usuário para `end_session_endpoint`, para desconexão.  Se você não fizer isso, o usuário poderá fazer nova autenticação no seu aplicativo sem inserir as credenciais novamente, pois ele terá uma sessão de logon único válida com o ponto de extremidade do Azure AD.

Você pode simplesmente redirecionar o usuário para o `end_session_endpoint` listado no documento de metadados do OpenID Connect:

```
GET https://login.microsoftonline.com/common/oauth2/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F

```

| Parâmetro |  | Descrição |
| --- | --- | --- |
| post_logout_redirect_uri |recomendável |A URL para a qual o usuário deve ser redirecionado após logoff bem-sucedido.  Se não estiver incluído, o usuário verá uma mensagem genérica. |

## Logout único
Quando você redireciona o usuário para o `end_session_endpoint`, o Azure AD limpa a sessão do usuário do navegador. No entanto, o usuário pode ainda entrar em outros aplicativos que usam o Azure AD para autenticação. Para permitir que esses aplicativos desconectem o usuário simultaneamente, o Azure AD envia uma solicitação HTTP GET para o `LogoutUrl` de todos os aplicativos aos quais o usuário está atualmente conectado. Os aplicativos devem responder a essa solicitação, limpando as sessões que identificam o usuário e retornando uma resposta `200`.  Se você deseja dar suporte um logout único em seu aplicativo, você deve implementar um `LogoutUrl` no código do aplicativo.  Você pode definir o `LogoutUrl` do Portal do Azure:

1. Navegue para o [Portal do Azure](https://portal.azure.com).
2. Escolha seu Active Directory clicando em sua conta no canto superior direito da página.
3. No painel de navegação esquerdo, escolha **Azure Active Directory** e, em seguida, escolha **Registros de aplicativo** e selecione seu aplicativo.
4. Clique em **Propriedades** e localize a caixa de texto **URL de Logoff**. 

## Aquisição de token
Muitos aplicativos Web precisam não apenas conectar o usuário, mas acessar um serviço Web em nome desse usuário usando o OAuth. Esse cenário combina o OpenID Connect para autenticação de usuário enquanto adquire simultaneamente um `authorization_code` que pode ser usado para obter `access_tokens` usando o Fluxo do Código de Autorização do OAuth.

## Obter tokens de acesso
Para obter tokens de acesso, você precisará modificar a solicitação de entrada acima:

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e        // Your registered Application Id
&response_type=id_token+code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F       // Your registered Redirect Uri, url encoded
&response_mode=form_post                              // form_post', or 'fragment'
&scope=openid
&resource=https%3A%2F%2Fservice.contoso.com%2F                                     
&state=12345                                          // Any value, provided by your app
&nonce=678910                                         // Any value, provided by your app
```

Ao incluir escopos de permissão na solicitação e usar `response_type=code+id_token`, o ponto de extremidade `authorize` terá certeza de que o usuário recebeu as permissões indicadas no parâmetro de consulta `scope` e retornará ao seu aplicativo um código de autorização para trocar por um token de acesso.

### Resposta bem-sucedida
Uma resposta bem-sucedida usando `response_mode=form_post` tem a seguinte aparência:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| . | Descrição |
| --- | --- |
| id_token |O `id_token` solicitado pelo aplicativo. Você pode usar o `id_token` para verificar a identidade do usuário e iniciar uma sessão com o usuário. |
| código |O authorization_code que o aplicativo solicitou. O aplicativo pode usar o código de autorização para solicitar um token de acesso para o recurso de destino. Authorization_codes têm uma duração curta e, geralmente, expiram depois de aproximadamente 10 minutos. |
| state |Se um parâmetro de estado estiver incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores de estado na solicitação e na resposta são idênticos. |

### Resposta de erro
As respostas de erro também podem ser enviadas ao `redirect_uri` para que o aplicativo possa tratá-las adequadamente:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| . | Descrição |
| --- | --- |
| error |Uma cadeia de caracteres de códigos de erro que pode ser usada para classificar tipos de erro que ocorrem e pode ser usada para responder aos erros. |
| error_description |Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro de autenticação. |

Para obter uma descrição dos possíveis códigos de erro e sua ação recomendada do cliente, veja [Códigos de erro para erros de ponto de extremidade de autorização](#error-codes-for-authorization-endpoint-errors).

Após você conseguir uma autorização `code` e um `id_token`, poderá conectar o usuário e obter tokens de acesso em seu nome.  Para conectar o usuário, você deve validar o `id_token` exatamente como descrito acima. Para obter tokens de acesso, você pode seguir as etapas descritas na seção "Usar o código de autorização para solicitar um token de acesso" da nossa [documentação do protocolo OAuth](active-directory-protocols-oauth-code.md).
