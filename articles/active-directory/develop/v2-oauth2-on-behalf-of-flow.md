---
title: Plataforma de identidade da Microsoft e o fluxo On-Behalf-Of do OAuth 2.0 | Azure
titleSuffix: Microsoft identity platform
description: Este artigo descreve como usar mensagens HTTP para implementar a autenticação de serviço para serviço usando o fluxo em nome de do OAuth2.0.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/7/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: ff8e03b813e2cb890192667e3466d920eaabc72c
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98756095"
---
# <a name="microsoft-identity-platform-and-oauth-20-on-behalf-of-flow"></a>Plataforma de identidade da Microsoft e o fluxo On-Behalf-Of de OAuth 2.0


O fluxo On-Behalf-Of (OBO) do OAuth 2.0 serve para o caso em que um aplicativo chama um serviço/uma API Web que, por sua vez, precisa chamar outro serviço/outra API Web. A ideia é propagar as permissões e identidade de usuário delegado por meio da cadeia de solicitações. Para o serviço de camada intermediária fazer solicitações autenticadas para o serviço downstream, ele precisa proteger um token de acesso da plataforma de identidade da Microsoft em nome do usuário.

Este artigo descreve como programar diretamente no protocolo do seu aplicativo.  Quando possível, recomendamos que você use as MSAL (bibliotecas de autenticação da Microsoft) com suporte para [adquirir tokens e chamar APIs Web seguras](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Confira também os [aplicativos de exemplo que usam MSAL](sample-v2-code.md).


Desde maio de 2018, alguns `id_token` derivados de fluxo implícito não podem ser usados para o fluxo OBO. Os aplicativos de página única (SPAs) devem aprovar um token de **acesso** para um cliente confidencial de camada intermediária a fim de executar fluxos OBO. Para saber mais sobre quais clientes podem fazer chamadas OBO, confira as [limitações](#client-limitations).

## <a name="protocol-diagram"></a>Diagrama de protocolo

Suponha que o usuário tenha sido autenticado em um aplicativo usando o [fluxo de concessão de código de autorização do OAuth 2.0](v2-oauth2-auth-code-flow.md) ou outro fluxo de login. Nesse ponto, o aplicativo tem um token de acesso *para a API A* (token A) com as declarações do usuário e o consentimento para acessar a API Web de camada intermediária (API A). Agora, a API A precisa fazer uma solicitação autenticada para a API Web downstream (API B).

As etapas abaixo constituem o fluxo OBO e são explicadas com a ajuda do diagrama a seguir.

![Mostra o fluxo On-Behalf-Of do OAuth 2.0](./media/v2-oauth2-on-behalf-of-flow/protocols-oauth-on-behalf-of-flow.png)

1. O aplicativo cliente faz uma solicitação para API A com o token A (com uma declaração `aud` da API A).
1. A API A se autentica no ponto de extremidade de emissão de token da plataforma de identidade da Microsoft e solicita um token para acessar a API B.
1. O ponto de extremidade de emissão de token da plataforma de identidade da Microsoft valida as credenciais da API A com o token A e emite o token de acesso da API B (token B) para a API A.
1. O token B é definido pela API A no cabeçalho de autorização da solicitação para a API B.
1. Os dados do recurso protegido são retornados pela API B para a API A e, em seguida, para o cliente.

Nesse cenário, o serviço de camada intermediária não tem interação com o usuário para obter o consentimento do usuário para acessar a API downstream. Portanto, a opção de conceder acesso à API downstream é apresentada antecipadamente como parte da etapa de consentimento durante a autenticação. Para saber como realizar essa configuração em seu aplicativo, confira o artigo sobre como [obter consentimento para o aplicativo de camada intermediária](#gaining-consent-for-the-middle-tier-application).

## <a name="middle-tier-access-token-request"></a>Solicitação de token de acesso de camada intermediária

Para solicitar um token de acesso, use um HTTP POST para o ponto de extremidade do token da plataforma de identidade da Microsoft específico ao locatário com os parâmetros a seguir.

```
https://login.microsoftonline.com/<tenant>/oauth2/v2.0/token
```

Há dois casos que dependem se o aplicativo cliente escolhe ser protegido por um segredo compartilhado ou por um certificado.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Primeiro caso: solicitação de token de acesso com um segredo compartilhado

Ao usar um segredo compartilhado, uma solicitação de token de acesso de serviço para serviço contém estes parâmetros:

| Parâmetro | Type | Descrição |
| --- | --- | --- |
| `grant_type` | Obrigatório | O tipo da solicitação do token. Para uma solicitação usando um JWT, o valor deve ser `urn:ietf:params:oauth:grant-type:jwt-bearer`. |
| `client_id` | Obrigatório | A ID do aplicativo (cliente) que a página [Portal do Azure - Registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) atribuiu ao seu aplicativo. |
| `client_secret` | Obrigatório | O segredo do cliente que você gerou para seu aplicativo no portal do Azure, página Registros de aplicativo. |
| `assertion` | Obrigatório | O token de acesso que foi enviado para a API de camada intermediária.  Esse token deve ter uma `aud` declaração de Audience () do aplicativo que faz essa solicitação obo (o aplicativo indicado pelo `client-id` campo). Os aplicativos não podem resgatar um token para um aplicativo diferente (por exemplo, se um cliente enviar uma API um token destinado ao MS Graph, a API não poderá resgatar usando OBO.  Em vez disso, ele deve rejeitar o token).  |
| `scope` | Obrigatório | Lista de escopos separados por espaço para a solicitação de token. Para obter mais informações, consulte [escopos](v2-permissions-and-consent.md). |
| `requested_token_use` | Obrigatório | Especifica como a solicitação deve ser processada. No fluxo OBO, o valor precisa ser definido como `on_behalf_of`. |

#### <a name="example"></a>Exemplo

O seguinte HTTP POST solicita um token de acesso e um token de atualização com o escopo `user.read` para a API da Web https://graph.microsoft.com.

```HTTP
//line breaks for legibility only

POST /oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com/<tenant>
Content-Type: application/x-www-form-urlencoded

grant_type=urn:ietf:params:oauth:grant-type:jwt-bearer
&client_id=2846f71b-a7a4-4987-bab3-760035b2f389
&client_secret=BYyVnAt56JpLwUcyo47XODd
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiIyO{a lot of characters here}
&scope=https://graph.microsoft.com/user.read+offline_access
&requested_token_use=on_behalf_of
```

### <a name="second-case-access-token-request-with-a-certificate"></a>Segundo caso: solicitação de token de acesso com um certificado

Uma solicitação de token de acesso de serviço a serviço com um certificado contém os seguintes parâmetros:

| Parâmetro | Type | Descrição |
| --- | --- | --- |
| `grant_type` | Obrigatório | O tipo da solicitação de token. Para uma solicitação usando um JWT, o valor deve ser `urn:ietf:params:oauth:grant-type:jwt-bearer`. |
| `client_id` | Obrigatório |  A ID do aplicativo (cliente) que a página [Portal do Azure - Registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) atribuiu ao seu aplicativo. |
| `client_assertion_type` | Obrigatório | O valor deve ser `urn:ietf:params:oauth:client-assertion-type:jwt-bearer`. |
| `client_assertion` | Obrigatório | Uma asserção (um token Web JSON) que você precisa para criar e assinar com o certificado registrado como credenciais do seu aplicativo. Para saber mais sobre como registrar seu certificado e o formato da asserção, confira [credenciais de certificado](active-directory-certificate-credentials.md). |
| `assertion` | Obrigatório |  O token de acesso que foi enviado para a API de camada intermediária.  Esse token deve ter uma `aud` declaração de Audience () do aplicativo que faz essa solicitação obo (o aplicativo indicado pelo `client-id` campo). Os aplicativos não podem resgatar um token para um aplicativo diferente (por exemplo, se um cliente enviar uma API um token destinado ao MS Graph, a API não poderá resgatar usando OBO.  Em vez disso, ele deve rejeitar o token).  |
| `requested_token_use` | Obrigatório | Especifica como a solicitação deve ser processada. No fluxo OBO, o valor precisa ser definido como `on_behalf_of`. |
| `scope` | Obrigatório | Lista de escopos separados por espaço para a solicitação de token. Para obter mais informações, consulte [escopos](v2-permissions-and-consent.md).|

Observe que os parâmetros são praticamente os mesmos como no caso da solicitação pelo segredo compartilhado, exceto pelo fato de o parâmetro `client_secret` ser substituído por dois parâmetros: `client_assertion_type` e `client_assertion`.

#### <a name="example"></a>Exemplo

O seguinte HTTP POST solicita um token de acesso com o escopo `user.read` para a API da Web https://graph.microsoft.com com um certificado.

```HTTP
// line breaks for legibility only

POST /oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com/<tenant>
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiO{a lot of characters here}
&requested_token_use=on_behalf_of
&scope=https://graph.microsoft.com/user.read+offline_access
```

## <a name="middle-tier-access-token-response"></a>Resposta de token de acesso de camada intermediária

Uma resposta de êxito é uma resposta JSON OAuth 2.0 com os parâmetros a seguir.

| Parâmetro | Descrição |
| --- | --- |
| `token_type` | Indica o valor do tipo de token. O único tipo ao qual a plataforma Microsoft Identity dá suporte é `Bearer` . Para saber mais sobre os tokens de portador, confira o artigo [Estrutura de Autorização do OAuth 2.0: Uso do Token de Portador (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| `scope` | O escopo do acesso concedido no token. |
| `expires_in` | O tempo, em segundos, pelo qual o token de acesso é válido. |
| `access_token` | O token de acesso solicitado. O serviço de chamada pode usar esse token para se autenticar no serviço de recebimento. |
| `refresh_token` | O token de atualização para o token de acesso solicitado. O serviço de chamada poderá usar esse token para solicitar outro token de acesso depois que o token de acesso atual expirar. O token de atualização é fornecido apenas se o `offline_access` escopo foi solicitado. |

### <a name="success-response-example"></a>Exemplo de resposta de êxito

O exemplo a seguir mostra uma resposta bem-sucedida a uma solicitação para um token de acesso para a API Web https://graph.microsoft.com.

```json
{
  "token_type": "Bearer",
  "scope": "https://graph.microsoft.com/user.read",
  "expires_in": 3269,
  "ext_expires_in": 0,
  "access_token": "eyJ0eXAiOiJKV1QiLCJub25jZSI6IkFRQUJBQUFBQUFCbmZpRy1tQTZOVGFlN0NkV1c3UWZkQ0NDYy0tY0hGa18wZE50MVEtc2loVzRMd2RwQVZISGpnTVdQZ0tQeVJIaGlDbUN2NkdyMEpmYmRfY1RmMUFxU21TcFJkVXVydVJqX3Nqd0JoN211eHlBQSIsImFsZyI6IlJTMjU2IiwieDV0IjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIiwia2lkIjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIn0.eyJhdWQiOiJodHRwczovL2dyYXBoLm1pY3Jvc29mdC5jb20iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaWF0IjoxNDkzOTMwMzA1LCJuYmYiOjE0OTM5MzAzMDUsImV4cCI6MTQ5MzkzMzg3NSwiYWNyIjoiMCIsImFpbyI6IkFTUUEyLzhEQUFBQU9KYnFFWlRNTnEyZFcxYXpKN1RZMDlYeDdOT29EMkJEUlRWMXJ3b2ZRc1k9IiwiYW1yIjpbInB3ZCJdLCJhcHBfZGlzcGxheW5hbWUiOiJUb2RvRG90bmV0T2JvIiwiYXBwaWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJhcHBpZGFjciI6IjEiLCJmYW1pbHlfbmFtZSI6IkNhbnVtYWxsYSIsImdpdmVuX25hbWUiOiJOYXZ5YSIsImlwYWRkciI6IjE2Ny4yMjAuMC4xOTkiLCJuYW1lIjoiTmF2eWEgQ2FudW1hbGxhIiwib2lkIjoiZDVlOTc5YzctM2QyZC00MmFmLThmMzAtNzI3ZGQ0YzJkMzgzIiwib25wcmVtX3NpZCI6IlMtMS01LTIxLTIxMjc1MjExODQtMTYwNDAxMjkyMC0xODg3OTI3NTI3LTI2MTE4NDg0IiwicGxhdGYiOiIxNCIsInB1aWQiOiIxMDAzM0ZGRkEwNkQxN0M5Iiwic2NwIjoiVXNlci5SZWFkIiwic3ViIjoibWtMMHBiLXlpMXQ1ckRGd2JTZ1JvTWxrZE52b3UzSjNWNm84UFE3alVCRSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInVuaXF1ZV9uYW1lIjoibmFjYW51bWFAbWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hY2FudW1hQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJWR1ItdmtEZlBFQ2M1dWFDaENRSkFBIiwidmVyIjoiMS4wIn0.cubh1L2VtruiiwF8ut1m9uNBmnUJeYx4x0G30F7CqSpzHj1Sv5DCgNZXyUz3pEiz77G8IfOF0_U5A_02k-xzwdYvtJUYGH3bFISzdqymiEGmdfCIRKl9KMeoo2llGv0ScCniIhr2U1yxTIkIpp092xcdaDt-2_2q_ql1Ha_HtjvTV1f9XR3t7_Id9bR5BqwVX5zPO7JMYDVhUZRx08eqZcC-F3wi0xd_5ND_mavMuxe2wrpF-EZviO3yg0QVRr59tE3AoWl8lSGpVc97vvRCnp4WVRk26jJhYXFPsdk4yWqOKZqzr3IFGyD08WizD_vPSrXcCPbZP3XWaoTUKZSNJg",
  "refresh_token": "OAQABAAAAAABnfiG-mA6NTae7CdWW7QfdAALzDWjw6qSn4GUDfxWzJDZ6lk9qRw4An{a lot of characters here}"
}
```

O token de acesso acima é um token formatado v 1.0 para Microsoft Graph. Isso ocorre porque o formato do token é baseado no **recurso** que está sendo acessado e não está relacionado aos pontos de extremidade usados para solicitá-lo. A Microsoft Graph está configurada para aceitar tokens v 1.0, portanto, a plataforma de identidade da Microsoft produz tokens de acesso v 1.0 quando um cliente solicita tokens para Microsoft Graph. Outros aplicativos podem indicar que desejam v 2.0-Format tokens, v 1.0-Format tokens ou até mesmo formatos de token de propriedade ou de proprietário.  Os pontos de extremidade v 1.0 e v 2.0 podem emitir um formato de token, dessa forma, o recurso sempre pode obter o formato correto do token, independentemente de como ou onde o token foi solicitado pelo cliente. 

Somente os aplicativos devem examinar os tokens de acesso. Os clientes **não devem** inspecioná-los. Inspecionar tokens de acesso para outros aplicativos em seu código resultará na interrupção inesperada do aplicativo quando o aplicativo alterar o formato de seus tokens ou começar a criptografá-los. 

### <a name="error-response-example"></a>Exemplo de resposta de erro

Uma resposta de erro retornará pelo ponto de extremidade do token durante a tentativa de adquirir um token de acesso para a API downstream, se esta tiver definida nela uma política de acesso condicional (como a [autenticação multifator](../authentication/concept-mfa-howitworks.md)). O serviço de camada intermediária deve revelar esse erro para o aplicativo cliente de modo que este possa fornecer a interação do usuário para satisfazer a política de acesso condicional.

```json
{
    "error":"interaction_required",
    "error_description":"AADSTS50079: Due to a configuration change made by your administrator, or because you moved to a new location, you must enroll in multi-factor authentication to access 'bf8d80f9-9098-4972-b203-500f535113b1'.\r\nTrace ID: b72a68c3-0926-4b8e-bc35-3150069c2800\r\nCorrelation ID: 73d656cf-54b1-4eb2-b429-26d8165a52d7\r\nTimestamp: 2017-05-01 22:43:20Z",
    "error_codes":[50079],
    "timestamp":"2017-05-01 22:43:20Z",
    "trace_id":"b72a68c3-0926-4b8e-bc35-3150069c2800",
    "correlation_id":"73d656cf-54b1-4eb2-b429-26d8165a52d7",
    "claims":"{\"access_token\":{\"polids\":{\"essential\":true,\"values\":[\"9ab03e19-ed42-4168-b6b7-7001fb3e933a\"]}}}"
}
```

## <a name="use-the-access-token-to-access-the-secured-resource"></a>Usar o token de acesso para acessar o recurso protegido

Agora, o serviço de camada intermediária pode usar o token adquirido acima para fazer solicitações autenticadas para a API Web downstream, definindo o token no cabeçalho `Authorization`.

### <a name="example"></a>Exemplo

```HTTP
GET /v1.0/me HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiO ... 0X2tnSQLEANnSPHY0gKcgw
```

## <a name="saml-assertions-obtained-with-an-oauth20-obo-flow"></a>Declarações SAML obtidas com um fluxo OBO do OAuth 2.0

Alguns serviços Web baseados em OAuth precisam acessar outras APIs de serviços Web que aceitam declarações SAML em fluxos não interativos. O Azure Active Directory pode fornecer uma declaração SAML em resposta a um fluxo On-Behalf-Of que usa um serviço Web baseado em SAML como recurso de destino.

Essa é uma extensão não padrão para o fluxo On-Behalf-Of do OAuth 2.0 que permite que um aplicativo baseado em OAuth2 acesse pontos de extremidade da API do serviço Web que consomem tokens SAML.

> [!TIP]
> Quando chama um serviço Web protegido por SAML de um aplicativo Web de front-end, você pode simplesmente chamar a API e iniciar um fluxo de autenticação interativa normal, com a sessão existente do usuário. Você só precisa usar um fluxo OBO quando uma chamada de serviço a serviço exigir um token SAML para fornecer o contexto de usuário.

## <a name="gaining-consent-for-the-middle-tier-application"></a>Obter consentimento para o aplicativo de camada intermediária

Dependendo da arquitetura ou do uso de seu aplicativo, você pode considerar estratégias diferentes para garantir que o fluxo OBO seja bem-sucedido. Em todos os casos, o objetivo final é garantir que o consentimento apropriado seja fornecido para que o aplicativo cliente possa chamar o aplicativo de camada intermediária, e o aplicativo de camada intermediária tenha permissão para chamar o recurso de back-end.

> [!NOTE]
> Anteriormente, o sistema de conta Microsoft (contas pessoais) não dava suporte ao campo "Aplicativo cliente conhecido", nem poderia mostrar o consentimento combinado.  Isso foi adicionado, e todos os aplicativos na plataforma de identidade da Microsoft podem usar a conhecida abordagem de aplicativo cliente para obter consentimento para chamadas OBO.

### <a name="default-and-combined-consent"></a>/.default e consentimento combinado

O aplicativo de camada intermediária adiciona o cliente à lista de aplicativos cliente conhecidos em seu manifesto e, em seguida, o cliente pode disparar um fluxo de consentimento combinado para ele mesmo e para o aplicativo de camada intermediária. Na plataforma de identidade da Microsoft, isso é feito usando o [ `/.default` escopo](v2-permissions-and-consent.md#the-default-scope). Ao disparar uma tela de consentimento usando aplicativos clientes conhecidos e `/.default`, a tela de consentimento mostrará as permissões **tanto** para o cliente da API de camada intermediária e também solicitará todas as permissões necessárias para a API de camada intermediária. O usuário fornece o consentimento para ambos os aplicativos e, em seguida, o fluxo OBO funciona.

### <a name="pre-authorized-applications"></a>Aplicativos pré-autorizados

Os recursos podem indicar que um determinado aplicativo sempre terá permissão para receber determinados escopos. Isso é útil principalmente para estabelecer conexões entre um cliente de front-end e um recurso de back-end de forma mais contínua. Um recurso pode declarar vários aplicativos pré-autorizados: qualquer aplicativo desse tipo pode solicitar essas permissões em um fluxo OBO e recebê-las sem que o usuário forneça o consentimento.

### <a name="admin-consent"></a>Consentimento do administrador

Um administrador de locatários pode garantir que os aplicativos tenham permissão para chamar as APIs necessárias, fornecendo consentimento do administrador para o aplicativo de camada intermediária. Para fazer isso, o administrador pode localizar o aplicativo de camada intermediária em seu locatário, abrir a página de permissões necessárias e optar por dar permissão ao aplicativo. Para saber mais sobre o consentimento do administrador, confira a [documentação de permissões e consentimento](v2-permissions-and-consent.md).

### <a name="use-of-a-single-application"></a>Uso de um único aplicativo

Em alguns cenários, você pode ter apenas um emparelhamento único de cliente de camada intermediária e front-end. Nesse caso, talvez seja mais fácil fazer isso em um único aplicativo, eliminando a necessidade de um aplicativo de camada intermediária completamente. Para fazer a autenticação entre o front-end e a API web, você pode usar cookies, um id_token ou um token de acesso solicitado para o aplicativo em si. Em seguida, solicite o consentimento desse aplicativo único para o recurso de back-end.

## <a name="client-limitations"></a>Limitações do cliente

Se um cliente usa o fluxo implícito para obter um id_token e esse cliente também tem caracteres curinga em uma URL de resposta, o id_token não pode ser usado para um fluxo OBO.  No entanto, os tokens de acesso adquiridos por meio de fluxo de concessão implícito ainda podem ser resgatados por um cliente confidencial mesmo se o cliente que inicia o processo tiver uma URL de resposta curinga registrada.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o protocolo OAuth 2.0 e outra maneira de executar autenticação de serviço para serviço usando as credenciais do cliente.

* [Concessão de credenciais de cliente OAuth 2.0 na plataforma de identidade da Microsoft](v2-oauth2-client-creds-grant-flow.md)
* [Fluxo de código do OAuth 2.0 na plataforma de identidade da Microsoft](v2-oauth2-auth-code-flow.md)
* [Como usar o escopo `/.default`](v2-permissions-and-consent.md#the-default-scope)
