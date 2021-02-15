---
title: Autorizar o acesso do aplicativo Web com o OpenID Connect & Azure AD | Microsoft Docs
description: Este artigo descreve como usar mensagens HTTP para autorizar o acesso a aplicativos Web e a APIs da Web em seu locatário usando o Azure Active Directory e o OpenID Connect.
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: b719e866852d2e865c16c62fddd8c549ae505b7d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85551558"
---
# <a name="authorize-access-to-web-applications-using-openid-connect-and-azure-active-directory"></a>Autorizar o acesso aos aplicativos Web usando o OpenID Connect e o Azure Active Directory

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) é uma camada de identidade simples criada sobre o protocolo OAuth 2.0. OAuth 2.0 define os mecanismos para obter e usar [**tokens de acesso**](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) para acessar recursos protegidos, mas eles não definem os métodos padrão para fornecer informações de identidade. O OpenID Connect implementa a autenticação como uma extensão para o processo de autorização do OAuth 2.0. Ele fornece informações sobre o usuário final na forma de um [`id_token`](../develop/id-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) que verifica a identidade do usuário e fornece informações básicas de perfil sobre o usuário.

O OpenID Connect é a nossa recomendação se você estiver criando um aplicativo Web hospedado em um servidor e acessado por meio de um navegador.

## <a name="register-your-application-with-your-ad-tenant"></a>Registrar seu aplicativo no seu locatário do AD
Primeiro, Registre seu aplicativo com seu locatário do Azure Active Directory (Azure AD). Isso dará a você uma ID do Aplicativo para seu aplicativo e permitirá que ele receba tokens.

1. Entre no [portal do Azure](https://portal.azure.com).
   
1. Escolha seu locatário do Azure AD selecionando sua conta no canto superior direito da página, seguido selecionando a navegação do **diretório switch** e, em seguida, selecionando o locatário apropriado. 
   - Ignore esta etapa se você tiver apenas um locatário do Azure AD em sua conta ou se você já tiver selecionado o locatário do Azure AD apropriado.
   
1. Na portal do Azure, procure e selecione **Azure Active Directory**.
   
1. No menu **Azure Active Directory** à esquerda, selecione **registros do aplicativo**e, em seguida, selecione **novo registro**.
   
1. Siga os prompts e crie um novo aplicativo. Não importa se é um aplicativo Web ou um cliente público (Mobile & Desktop) para este tutorial, mas se você quiser exemplos específicos para aplicativos Web ou aplicativos cliente públicos, Confira nossos [guias de início rápido](v1-overview.md).
   
   - **Nome** é o nome do aplicativo e descreve o seu aplicativo aos usuários finais.
   - Em **Tipos de conta com suporte**, selecione **Contas em qualquer diretório organizacional e contas pessoais da Microsoft**.
   - Forneça o **URI de redirecionamento**. Para aplicativos Web, essa é a URL base do seu aplicativo onde os usuários podem entrar.  Por exemplo, `http://localhost:12345`. Para o cliente público (Mobile & Desktop), o AD do Azure o usa para retornar respostas de token. Insira um valor específico para seu aplicativo.  Por exemplo, `http://MyFirstAADApp`.
   <!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->  
   
1. Depois de concluir o registro, o Azure AD atribuirá ao seu aplicativo um identificador de cliente exclusivo (a **ID do aplicativo**). Você precisará desse valor nas próximas seções, então copie-o da página do aplicativo.
   
1. Para localizar seu aplicativo no portal do Azure, selecione **registros de aplicativo**e, em seguida, selecione **Exibir todos os aplicativos**.

## <a name="authentication-flow-using-openid-connect"></a>Fluxo de autenticação usando o OpenID Connect

O fluxo de credenciais mais básico contém as seguintes etapas: cada um delas é descrita em detalhes abaixo.

![Fluxo de autenticação usando o OpenId Connect](./media/v1-protocols-openid-connect-code/active-directory-oauth-code-flow-web-app.png)

## <a name="openid-connect-metadata-document"></a>Documento de metadados do OpenID Connect

O OpenID Connect descreve um documento de metadados que contém a maioria das informações necessárias para que um aplicativo inicie uma sessão. Isso inclui informações como as URLs a serem usadas e o local das chaves de assinatura públicas do serviço. O documento de metadados do OpenID Connect pode ser localizado em:

```
https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration
```
Os metadados são um documento JSON (JavaScript Object Notation) simples. Veja o snippet a seguir para obter um exemplo. O conteúdo do snippet é totalmente descrito na [especificação do OpenID Connect](https://openid.net). Observe que fornecer uma ID de locatário em vez de `common` {Tenant} acima resultará em URIs específicos de locatário no objeto JSON retornado.

```
{
    "authorization_endpoint": "https://login.microsoftonline.com/{tenant}/oauth2/authorize",
    "token_endpoint": "https://login.microsoftonline.com/{tenant}/oauth2/token",
    "token_endpoint_auth_methods_supported":
    [
        "client_secret_post",
        "private_key_jwt",
        "client_secret_basic"
    ],
    "jwks_uri": "https://login.microsoftonline.com/common/discovery/keys"
    "userinfo_endpoint":"https://login.microsoftonline.com/{tenant}/openid/userinfo",
    ...
}
```

Se seu aplicativo tiver chaves de assinatura personalizadas como resultado do uso do recurso [claims-mapping](../develop/active-directory-claims-mapping.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json), será necessário acrescentar um `appid`parâmetro de consulta contendo a ID do aplicativo para obter um `jwks_uri` apontando para as informações de chave de assinatura do aplicativo. Por exemplo: `https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` contém uma `jwks_uri` de `https://login.microsoftonline.com/{tenant}/discovery/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e`.

## <a name="send-the-sign-in-request"></a>Enviar a solicitação de conexão

Quando o aplicativo Web precisa autenticar o usuário, ele deve direcionar o usuário para o ponto de extremidade `/authorize` . Essa solicitação é semelhante ao primeiro segmento do [Fluxo de código de autorização do OAuth 2.0](v1-protocols-oauth-code.md), com algumas diferenças importantes:

* A solicitação deve incluir o escopo `openid` no parâmetro `scope`.
* O parâmetro `response_type` deve incluir `id_token`.
* A solicitação deve incluir o parâmetro `nonce` .

Dessa forma, uma solicitação de exemplo teria esta aparência:

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%3a12345
&response_mode=form_post
&scope=openid
&state=12345
&nonce=7362CAEA-9CA5-4B43-9BA3-34D7C303EBA7
```

| Parâmetro | Type | Descrição |
| --- | --- | --- |
| locatário |obrigatório |O valor `{tenant}` no caminho da solicitação pode ser usado para controlar quem pode entrar no aplicativo. Os valores permitidos são identificadores de locatário, por exemplo, `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` ou `contoso.onmicrosoft.com` ou `common` para tokens independentes de locatário |
| client_id |necessárias |A ID do Aplicativo atribuída para o aplicativo ao registrá-lo no Azure Active Directory. Você pode encontrar isso no Portal do Azure. Clique em **Azure Active Directory**, clique em **registros do aplicativo**, escolha o aplicativo e localize a ID do aplicativo na página do aplicativo. |
| response_type |necessárias |Deve incluir `id_token` para conexão do OpenID Connect. Também pode incluir outros response_types, como `code` ou `token`. |
| escopo | recomendável | A especificação do OpenID Connect requer o escopo `openid` , que se traduz na permissão "entrar" na interface do usuário de consentimento. Esse e outros escopos OIDC são ignorados no ponto de extremidade v 1.0, mas ainda é uma prática recomendada para clientes em conformidade com os padrões. |
| nonce |necessárias |Um valor incluído na solicitação, gerado pelo aplicativo, que é incluído no `id_token` resultante como uma declaração. O aplicativo pode, então, verificar esse valor para atenuar os ataques de reprodução de token. Normalmente, o valor é uma cadeia de caracteres aleatória e exclusiva ou um GUID que pode ser usado para identificar a origem da solicitação. |
| redirect_uri | recomendável |O redirect_uri do seu aplicativo, onde as respostas de autenticação podem ser enviadas e recebidas pelo aplicativo. Ele deve corresponder exatamente a um dos redirect_uris que você registrou no portal, com exceção de que ele deve ser codificado por url. Se estiver ausente, o agente do usuário será enviado de volta para um dos URIs de redirecionamento registrados para o aplicativo, aleatoriamente. O comprimento máximo é de 255 bytes |
| response_mode |opcionais |Especifica o método que deve ser usado para enviar o authorization_code resultante de volta ao aplicativo. Os valores suportados são `form_post` para * post do formulário HTTP * e `fragment` para o * fragmento de URL *. Em aplicativos Web, é recomendável usar `response_mode=form_post` a fim de garantir a transferência mais segura de tokens para seu aplicativo. O padrão para qualquer fluxo, incluindo um id_token é `fragment`.|
| state |recomendável |Um valor incluído na solicitação que retorna na resposta do token. Pode ser uma cadeia de caracteres de qualquer conteúdo desejado. Um valor exclusivo gerado aleatoriamente normalmente é usado para [impedir ataques de solicitação intersite forjada](https://tools.ietf.org/html/rfc6749#section-10.12). O estado também é usado para codificar informações sobre o estado do usuário no aplicativo antes que a solicitação de autenticação ocorra, como a página ou a exibição em que ele estava. |
| prompt |opcionais |Indica o tipo de interação do usuário que é necessário. Atualmente, os únicos valores válidos são "login", "none" e "consent". `prompt=login` força o usuário a inserir suas credenciais na solicitação, negando o logon único. `prompt=none` é o oposto — ele garante que nenhum prompt interativo seja apresentado ao usuário. Se a solicitação não puder ser concluída silenciosamente por meio de logon único, o ponto de extremidade retornará um erro. `prompt=consent` dispara a caixa de diálogo de consentimento do OAuth depois que o usuário se conecta, solicitando que ele conceda permissões ao aplicativo. |
| login_hint |opcionais |Pode ser usado para preencher previamente o campo de nome de usuário/endereço de email da página de entrada do usuário, caso você saiba o nome de usuário com antecedência. Geralmente, os aplicativos usam esse parâmetro durante a reautenticação, após já terem extraído o nome de usuário de uma entrada anterior usando a declaração `preferred_username`. |

Nesse ponto, é solicitado que o usuário insira suas credenciais e conclua a autenticação.

### <a name="sample-response"></a>Resposta de exemplo

Uma resposta de exemplo, enviada para o `redirect_uri` especificado na solicitação de entrada após a autenticação do usuário, poderia ter esta aparência:

```
POST / HTTP/1.1
Host: localhost:12345
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Parâmetro | Descrição |
| --- | --- |
| id_token |O `id_token` que o aplicativo solicitou. Você pode usar o `id_token` para verificar a identidade do usuário e iniciar uma sessão com o usuário. |
| state |Um valor incluído na solicitação que também retorna na resposta do token. Um valor exclusivo gerado aleatoriamente normalmente é usado para [impedir ataques de solicitação intersite forjada](https://tools.ietf.org/html/rfc6749#section-10.12). O estado também é usado para codificar informações sobre o estado do usuário no aplicativo antes que a solicitação de autenticação ocorra, como a página ou a exibição em que ele estava. |

### <a name="error-response"></a>Resposta de erro

As respostas de erro também podem ser enviadas ao `redirect_uri` para que o aplicativo possa tratá-las adequadamente:

```
POST / HTTP/1.1
Host: localhost:12345
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parâmetro | Descrição |
| --- | --- |
| error |Uma cadeia de caracteres de códigos de erro que pode ser usada para classificar tipos de erro que ocorrem e pode ser usada para responder aos erros. |
| error_description |Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro de autenticação. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Códigos de erro para erros de ponto de extremidade de autorização

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

## <a name="validate-the-id_token"></a>Validar o id_token

Apenas receber um `id_token` não é suficiente para autenticar o usuário. Você deve validar a assinatura e verificar as declarações no `id_token`, de acordo com os requisitos do aplicativo. O ponto de extremidade do Azure AD usa JWTs (Tokens Web JSON) e criptografia de chave pública para assinar tokens e verificar se eles são válidos.

Você pode escolher validar o `id_token` no código do cliente, mas uma prática comum é enviar o `id_token` para um servidor de back-end e executar a validação nele. 

Talvez você também queira validar declarações adicionais, dependendo do cenário. Algumas validações comuns incluem:

* Garantir que o usuário/organização tenha assinado para usar o aplicativo.
* Garantir que o usuário tenha autorização/privilégios adequados usando `wids` as `roles` declarações ou. 
* Garantir que uma determinada intensidade de autenticação tenha ocorrido, como autenticação multifator.

Depois de ter validado o `id_token`, você poderá iniciar uma sessão com o usuário e usar declarações no `id_token` para obter informações sobre o usuário no seu aplicativo. Essas informações podem ser usadas para exibição, registros, personalização, etc. Para obter mais informações `id_tokens` e declarações, leia [AAD id_tokens](../develop/id-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

## <a name="send-a-sign-out-request"></a>Enviar uma solicitação de saída

Quando você deseja conectar o usuário do aplicativo, não é suficiente limpar os cookies do aplicativo e encerrar a sessão do usuário. Você também deve redirecionar o usuário para o `end_session_endpoint` para sair. Se você não conseguir fazer isso, o usuário poderá se autenticar novamente em seu aplicativo sem inserir suas credenciais novamente, pois eles terão uma sessão de logon único válida com o ponto de extremidade do Azure AD.

Você pode simplesmente redirecionar o usuário para o `end_session_endpoint` listado no documento de metadados do OpenID Connect:

```
GET https://login.microsoftonline.com/common/oauth2/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F

```

| Parâmetro | Type | Descrição |
| --- | --- | --- |
| post_logout_redirect_uri |recomendável |A URL para a qual o usuário deve ser redirecionado após a saída bem-sucedida.  Essa URL deve corresponder a um dos URIs de redirecionamento registrados para seu aplicativo no portal de registro de aplicativo.  Se *post_logout_redirect_uri* não for incluído, o usuário será exibido como uma mensagem genérica. |

## <a name="single-sign-out"></a>Logout único

Quando você redireciona o usuário para o `end_session_endpoint`, o Azure AD limpa a sessão do usuário do navegador. No entanto, o usuário pode ainda entrar em outros aplicativos que usam o Azure AD para autenticação. Para permitir que esses aplicativos desconectem o usuário simultaneamente, o Azure AD envia uma solicitação HTTP GET para o `LogoutUrl` de todos os aplicativos aos quais o usuário está atualmente conectado. Os aplicativos devem responder a essa solicitação, limpando as sessões que identificam o usuário e retornando uma resposta `200`. Se você deseja dar suporte um logout único em seu aplicativo, você deve implementar um `LogoutUrl` no código do aplicativo. Você pode definir o `LogoutUrl` do Portal do Azure:

1. Navegue até o [Portal do Azure](https://portal.azure.com).
2. Escolha seu Active Directory clicando em sua conta no canto superior direito da página.
3. No painel de navegação esquerdo, escolha **Azure Active Directory** e, em seguida, escolha **Registros de aplicativo** e selecione seu aplicativo.
4. Clique em ** Configurações **, em ** Propriedades ** e encontre a caixa de texto ** URL de saída **. 

## <a name="token-acquisition"></a>Aquisição de token
Muitos aplicativos Web precisam não apenas conectar o usuário, mas acessar um serviço Web em nome desse usuário usando o OAuth. Esse cenário combina o OpenID Connect para autenticação de usuário enquanto adquire simultaneamente um `authorization_code` que pode ser usado para obter `access_tokens` usando o [Fluxo do Código de Autorização do OAuth](v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token).

## <a name="get-access-tokens"></a>Obter tokens de acesso
Para obter tokens de acesso, você precisará modificar a solicitação de entrada acima:

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e        // Your registered Application ID
&response_type=id_token+code
&redirect_uri=http%3A%2F%2Flocalhost%3a12345          // Your registered Redirect Uri, url encoded
&response_mode=form_post                              // `form_post' or 'fragment'
&scope=openid
&resource=https%3A%2F%2Fservice.contoso.com%2F        // The identifier of the protected resource (web API) that your application needs access to
&state=12345                                          // Any value, provided by your app
&nonce=678910                                         // Any value, provided by your app
```

Ao incluir escopos de permissão na solicitação e usar `response_type=code+id_token`, o ponto de extremidade `authorize` terá certeza de que o usuário recebeu as permissões indicadas no parâmetro de consulta `scope` e retornará ao seu aplicativo um código de autorização para trocar por um token de acesso.

### <a name="successful-response"></a>Resposta bem-sucedida

Uma resposta bem-sucedida, enviada ao `redirect_uri` usando `response_mode=form_post` , é semelhante a:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Parâmetro | Descrição |
| --- | --- |
| id_token |O `id_token` que o aplicativo solicitou. Você pode usar o `id_token` para verificar a identidade do usuário e iniciar uma sessão com o usuário. |
| code |O authorization_code que o aplicativo solicitou. O aplicativo pode usar o código de autorização para solicitar um token de acesso para o recurso de destino. Authorization_codes têm uma duração curta e, geralmente, expiram depois de aproximadamente 10 minutos. |
| state |Se um parâmetro de estado for incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores de estado na solicitação e na resposta são idênticos. |

### <a name="error-response"></a>Resposta de erro

As respostas de erro também podem ser enviadas ao `redirect_uri` para que o aplicativo possa tratá-las adequadamente:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parâmetro | Descrição |
| --- | --- |
| error |Uma cadeia de caracteres de códigos de erro que pode ser usada para classificar tipos de erro que ocorrem e pode ser usada para responder aos erros. |
| error_description |Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro de autenticação. |

Para obter uma descrição dos possíveis códigos de erro e sua ação recomendada do cliente, veja [Códigos de erro para erros de ponto de extremidade de autorização](#error-codes-for-authorization-endpoint-errors).

Depois de obter uma autorização `code` e um `id_token` , você pode conectar o usuário e obter [tokens de acesso](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) em seu nome. Para conectar o usuário, você deve validar o `id_token` exatamente como descrito acima. Para obter tokens de acesso, você pode seguir as etapas descritas na seção "Usar o código de autorização para solicitar um token de acesso" da nossa [documentação do fluxo de código OAuth](v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre os [tokens de acesso](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).
* Saiba mais sobre as [ `id_token` declarações e](../develop/id-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).
