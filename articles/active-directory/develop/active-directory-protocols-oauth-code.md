---
title: "Noções básicas sobre o fluxo do código de autorização do OAuth 2.0 no Azure AD | Microsoft Docs"
description: "Este artigo descreve como usar mensagens HTTP para autorizar o acesso a aplicativos Web e a APIs da Web em seu locatário usando o Azure Active Directory e o OAuth 2.0."
services: active-directory
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: de3412cb-5fde-4eca-903a-4e9c74db68f2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 916652f2d6336da625be91431c3771a730204a73
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# Autorizar o acesso aos aplicativos Web usando o OAuth 2.0 e o Azure Active Directory
O Azure AD (Azure Active Directory) usa o OAuth 2.0 para permitir que você autorize o acesso a aplicativos Web e APIs da Web em seu locatário do Azure AD. Este guia independe do idioma e descreve como enviar e receber mensagens HTTP sem usar qualquer uma das nossas bibliotecas de software livre.

O fluxo do código de autorização do OAuth 2.0 é descrito na [seção 4.1 da especificação do OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.1). Ele é usado para realizar a autenticação e a autorização na maioria dos tipos de aplicativo, incluindo aplicativos Web e aplicativos originalmente instalados.

[!INCLUDE [active-directory-protocols-getting-started](../../../includes/active-directory-protocols-getting-started.md)]

## Fluxo de autorização do OAuth 2.0
Em um alto nível, todo o fluxo de autorização de um aplicativo é semelhante a:

![Fluxo do código de autenticação do OAuth](media/active-directory-protocols-oauth-code/active-directory-oauth-code-flow-native-app.png)

## Solicitar um código de autorização
O fluxo do código de autorização começa com o cliente direcionando o usuário para o ponto de extremidade `/authorize` . Nessa solicitação, o cliente indica as permissões que precisa adquirir do usuário: Você pode obter os pontos de extremidade do OAuth 2.0 da página do seu aplicativo no Portal Clássico do Azure, no botão **Exibir Pontos de Extremidade** na gaveta inferior.

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&resource=https%3A%2F%2Fservice.contoso.com%2F
&state=12345
```

| Parâmetro |  | Descrição |
| --- | --- | --- |
| locatário |obrigatório |O valor `{tenant}` no caminho da solicitação pode ser usado para controlar quem pode entrar no aplicativo.  Os valores permitidos são identificadores de locatário, por exemplo, `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` ou `contoso.onmicrosoft.com` ou `common` para tokens independentes de locatário |
| client_id |obrigatório |A ID de aplicativo atribuída ao seu aplicativo quando você o registra no Azure AD. Você pode encontrá-la no Portal do Azure. Clique em **Active Directory**, clique no diretório, escolha o aplicativo e clique em **Configurar** |
| response_type |obrigatório |Deve incluir `code` para o fluxo do código de autorização. |
| redirect_uri |recomendável |O redirect_uri do seu aplicativo, onde as respostas de autenticação podem ser enviadas e recebidas pelo aplicativo.  Ele deve corresponder exatamente a um dos redirect_uris que você registrou no portal, com exceção de que ele deve ser codificado por url.  Para aplicativos nativos e móveis, você deve usar o valor padrão de `urn:ietf:wg:oauth:2.0:oob`. |
| response_mode |recomendável |Especifica o método que deve ser usado para enviar o token resultante de volta ao aplicativo.  Pode ser `query` ou `form_post`. |
| state |recomendável |Um valor incluído na solicitação que também retorna na resposta do token. Um valor exclusivo gerado aleatoriamente que normalmente é usado para [impedir ataques de solicitação intersite forjada](http://tools.ietf.org/html/rfc6749#section-10.12).  O estado também é usado para codificar as informações sobre o estado do usuário no aplicativo antes da solicitação de autenticação ocorrida, como a página ou exibição em que ele estava. |
| recurso |opcional |O URI de ID do Aplicativo da API Web (recurso seguro). Para localizar o URI da ID do Aplicativo de API Web, no Portal do Azure, clique em **Active Directory**, clique no diretório, clique no aplicativo e em **Configurar**. |
| prompt |opcional |Indique o tipo de interação do usuário necessária.<p> Os valores válidos são: <p> *logon*: o usuário deve ser solicitado a autenticar novamente. <p> *consentimento*: consentimento do usuário foi concedido, mas precisa ser atualizado. O usuário deve ser solicitado a consentir. <p> *admin_consent*: o administrador deve ser solicitado a consentir em nome de todos os usuários em sua organização |
| login_hint |opcional |Pode ser usado para preencher previamente o campo de nome de usuário/endereço de email da página de entrada do usuário, se você souber o nome de usuário com antecedência.  Geralmente, os aplicativos usam esse parâmetro durante a reautenticação, após já terem extraído o nome de usuário de uma entrada anterior usando a declaração `preferred_username`. |
| domain_hint |opcional |Fornece uma dica sobre o locatário ou domínio que o usuário deve usar para entrar. O valor de domain_hint é um domínio registrado para o locatário. Se o locatário for federado para um diretório local, o AAD redirecionará para o servidor de federação do locatário especificado. |

> [!NOTE]
> Se o usuário fizer parte de uma organização, um administrador da organização poderá consentir ou recusar em nome do usuário ou permitir que o usuário consinta. O usuário terá a opção de consentir apenas quando o administrador permitir.
>
>

Neste ponto, o usuário é solicitado a inserir suas credenciais e consentir as permissões indicadas no parâmetro de consulta `scope`. Depois que o usuário é autenticado e recebe a permissão de consentimento, o Azure AD envia uma resposta ao seu aplicativo no endereço `redirect_uri` em sua solicitação.

### Resposta bem-sucedida
Uma resposta bem-sucedida se parece com esta:

```
GET  HTTP/1.1 302 Found
Location: http://localhost/myapp/?code= AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF&state=D79E5777-702E-4260-9A62-37F75FF22CCE
```

| Parâmetro | Descrição |
| --- | --- |
| admin_consent |O valor será True se um administrador tiver consentido um prompt de solicitação de consentimento. |
| código |O código de autorização solicitado pelo aplicativo. O aplicativo pode usar o código de autorização para solicitar um token de acesso para o recurso de destino. |
| session_state |Um valor exclusivo que identifica a sessão de usuário atual. Esse valor é um GUID, mas deve ser tratado como um valor opaco que é transmitido sem verificação. |
| state |Se um parâmetro de estado estiver incluído na solicitação, o mesmo valor deverá aparecer na resposta. É uma boa prática fazer o aplicativo verificar se os valores de estado na solicitação e na resposta são idênticos antes de usar a resposta. Isso ajuda a detectar [ataques de CSRF (Solicitação Intersite Forjada)](https://tools.ietf.org/html/rfc6749#section-10.12) contra o cliente. |

### Resposta de erro
As respostas de erro também podem ser enviadas ao `redirect_uri` para que o aplicativo possa tratá-las adequadamente.

```
GET http://localhost:12345/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parâmetro | Descrição |
| --- | --- |
| error |Um valor de código de erro definido na Seção 5.2 da [Estrutura de Autorização OAuth 2.0](http://tools.ietf.org/html/rfc6749). A tabela a seguir descreve os códigos de erro retornados pelo Azure AD. |
| error_description |Uma descrição mais detalhada do erro. Esta mensagem não se destina a ser amigável para o usuário final. |
| state |O valor de estado é um valor não reutilizado gerado aleatoriamente que é enviado na solicitação e retornado na resposta para evitar ataques de CSRF (solicitação intersite forjada). |

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

## Usar o código de autorização para solicitar um token de acesso
Agora que você já adquiriu um código de autorização e recebeu permissão do usuário, poderá resgatar o código de um token de acesso para o recurso desejado ao enviar uma solicitação POST para o ponto de extremidade `/token` :

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded
grant_type=authorization_code
&client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA
&redirect_uri=https%3A%2F%2Flocalhost%2Fmyapp%2F
&resource=https%3A%2F%2Fservice.contoso.com%2F
&client_secret=p@ssw0rd

//NOTE: client_secret only required for web apps
```

| Parâmetro |  | Descrição |
| --- | --- | --- |
| locatário |obrigatório |O valor `{tenant}` no caminho da solicitação pode ser usado para controlar quem pode entrar no aplicativo.  Os valores permitidos são identificadores de locatário, por exemplo, `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` ou `contoso.onmicrosoft.com` ou `common` para tokens independentes de locatário |
| client_id |obrigatório |A ID de aplicativo atribuída ao seu aplicativo quando você o registra no Azure AD. Você poderá encontrá-la no Portal Clássico do Azure. Clique em **Active Directory**, clique no diretório, escolha o aplicativo e clique em **Configurar** |
| grant_type |obrigatório |Deve ser `authorization_code` para o fluxo do código de autorização. |
| código |obrigatório |O `authorization_code` que você adquiriu na seção anterior |
| redirect_uri |obrigatório |O mesmo valor `redirect_uri` usado para adquirir o `authorization_code`. |
| client_secret |obrigatório para aplicativos Web |O segredo do aplicativo que você criou no portal de registro do aplicativo para seu aplicativo.  Ele não deve ser usado em um aplicativo nativo, pois client_secrets não podem ser armazenados de modo confiável em dispositivos.  Ele é obrigatório para aplicativos Web e APIs Web, que têm a capacidade de armazenar o `client_secret` com segurança no servidor. |
| recurso |necessário se especificado na solicitação de código de autorização, caso contrário, é opcional |O URI de ID do Aplicativo da API Web (recurso seguro). |

Para localizar o URI de ID do Aplicativo, no Portal de Gerenciamento do Azure, clique em **Active Directory**, clique no diretório, clique no aplicativo e em **Configurar**.

### Resposta bem-sucedida
O Azure AD retorna um token de acesso após uma resposta bem-sucedida. Para minimizar as chamadas de rede do aplicativo cliente e sua latência associada, o aplicativo cliente deve armazenar em cache os tokens de acesso durante o tempo de vida do token especificado na resposta do OAuth 2.0. Para determinar o tempo de vida do token, use os valores de parâmetro `expires_in` ou `expires_on`.

Se um recurso da API Web retornar um código de erro `invalid_token` , isso poderá indicar que o recurso determinou que o token expirou. Se as horas de relógio do cliente e do recurso forem diferentes (conhecido como "diferença de horário"), o recurso poderá considerar o token expirado antes que o token seja removido do cache do cliente. Se isso ocorrer, remova o token do cache, mesmo se ele ainda estiver dentro de seu tempo de vida calculado.

Uma resposta bem-sucedida se parece com esta:

```
{
  "access_token": " eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1388444763",
  "resource": "https://service.contoso.com/",
  "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA",
  "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
  "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83ZmU4MTQ0Ny1kYTU3LTQzODUtYmVjYi02ZGU1N2YyMTQ3N2UvIiwiaWF0IjoxMzg4NDQwODYzLCJuYmYiOjEzODg0NDA4NjMsImV4cCI6MTM4ODQ0NDc2MywidmVyIjoiMS4wIiwidGlkIjoiN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlIiwib2lkIjoiNjgzODlhZTItNjJmYS00YjE4LTkxZmUtNTNkZDEwOWQ3NGY1IiwidXBuIjoiZnJhbmttQGNvbnRvc28uY29tIiwidW5pcXVlX25hbWUiOiJmcmFua21AY29udG9zby5jb20iLCJzdWIiOiJKV3ZZZENXUGhobHBTMVpzZjd5WVV4U2hVd3RVbTV5elBtd18talgzZkhZIiwiZmFtaWx5X25hbWUiOiJNaWxsZXIiLCJnaXZlbl9uYW1lIjoiRnJhbmsifQ."
}

```

| Parâmetro | Descrição |
| --- | --- |
| access_token |O token de acesso solicitado. O aplicativo pode usar esse token para se autenticar no recurso protegido, como uma API Web. |
| token_type |Indica o valor do tipo de token. O único tipo com suporte do Azure AD é Portador Para saber mais sobre os tokens de portador, confira [Estrutura de autorização do OAuth 2.0: uso do token de portador (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt) |
| expires_in |Por quanto tempo o token de acesso é válido (em segundos). |
| expires_on |A hora de expiração do token de acesso. A data é representada como o número de segundos de 1970-01-01T0:0:0Z UTC até a hora de expiração. Esse valor é usado para determinar o tempo de vida de tokens em cache. |
| recurso |O URI de ID do Aplicativo da API Web (recurso seguro). |
| scope |As permissões de representação concedidas ao aplicativo cliente. A permissão padrão é `user_impersonation`. O proprietário do recurso protegido pode registrar valores adicionais no AD do Azure. |
| refresh_token |Um token de atualização do OAuth 2.0. O aplicativo pode usar esse token para adquirir tokens de acesso adicionais depois que o token de acesso atual expira.  Os tokens de atualização têm longa duração e podem ser usados para reter acesso a recursos por períodos estendidos. |
| id_token |Um JWT (Token Web JSON) não assinado. O aplicativo pode decodificar com base64Url os segmentos desse token para solicitar informações sobre o usuário que se conectou. O aplicativo pode armazenar em cache os valores e exibi-los, mas não deve depender deles para qualquer autorização ou limites de segurança. |

### Declarações de token JWT
O token JWT no valor do parâmetro `id_token` pode ser decodificado para as seguintes declarações:

```
{
 "typ": "JWT",
 "alg": "none"
}.
{
 "aud": "2d4d11a2-f814-46a7-890a-274a72a7309e",
 "iss": "https://sts.windows.net/7fe81447-da57-4385-becb-6de57f21477e/",
 "iat": 1388440863,
 "nbf": 1388440863,
 "exp": 1388444763,
 "ver": "1.0",
 "tid": "7fe81447-da57-4385-becb-6de57f21477e",
 "oid": "68389ae2-62fa-4b18-91fe-53dd109d74f5",
 "upn": "frank@contoso.com",
 "unique_name": "frank@contoso.com",
 "sub": "JWvYdCWPhhlpS1Zsf7yYUxShUwtUm5yzPmw_-jX3fHY",
 "family_name": "Miller",
 "given_name": "Frank"
}.
```

Para saber mais sobre os tokens Web JSON, confira a [especificação de rascunho IETF JWT](http://go.microsoft.com/fwlink/?LinkId=392344). Para saber mais sobre os tipos de token e declaração, leia [Tipos de token e de declaração com suporte](active-directory-token-and-claims.md)

O parâmetro `id_token` inclui os seguintes tipos de declaração:

| Tipo de declaração | Descrição |
| --- | --- |
| aud |Público-alvo do token. Quando o token é emitido para um aplicativo cliente, o público-alvo será o `client_id` do cliente. |
| exp |Hora de expiração. A hora em que o token expira. Para que o token seja válido, a data/hora atual deve ser menor ou igual ao valor `exp` . A hora é representada como o número de segundos de 1º de janeiro de 1970 (1970-01-01T0:0:0Z) UTC até a hora em que a validade do token expira.|
| family_name |Sobrenome do usuário. O aplicativo pode exibir esse valor. |
| given_name |Nome do usuário. O aplicativo pode exibir esse valor. |
| iat |Hora da emissão. A hora em que o JWT foi emitido. A hora é representada como o número de segundos de 1º de janeiro de 1970 (1970-01-01T0:0:0Z) UTC até a hora em que o token foi emitido. |
| iss |Identifica o emissor do token |
| nbf |Não antes de. A hora em que o token entra em vigor. Para que o token seja válido, a data/hora atual deve ser maior ou igual ao valor de Nbf. A hora é representada como o número de segundos de 1º de janeiro de 1970 (1970-01-01T0:0:0Z) UTC até a hora em que o token foi emitido. |
| oid |O identificador (ID) do objeto do usuário no Azure AD. |
| sub |Identificador do assunto do token. Este é um identificador persistente e imutável para o usuário descrito pelo token. Use esse valor na lógica de cache. |
| tid |O identificador (ID) do locatário do Azure AD que emitiu o token. |
| unique_name |Um identificador exclusivo que pode ser exibido para o usuário. Geralmente é um nome de usuário principal (UPN). |
| upn |Nome UPN do usuário. |
| ver |Versão. A versão do token JWT, normalmente 1.0. |

### Resposta de erro
Os erros de ponto de extremidade de emissão de token são códigos de erro HTTP, pois o cliente chama o ponto de extremidade de emissão de token diretamente. Além do código de status HTTP, o ponto de extremidade de emissão de token do Azure AD também retorna um documento JSON com objetos que descrevem o erro.

Uma resposta de erro de exemplo se parece com esta:

```
{
  "error": "invalid_grant",
  "error_description": "AADSTS70002: Error validating credentials. AADSTS70008: The provided authorization code or refresh token is expired. Send a new interactive authorization request for this user and resource.\r\nTrace ID: 3939d04c-d7ba-42bf-9cb7-1e5854cdce9e\r\nCorrelation ID: a8125194-2dc8-4078-90ba-7b6592a7f231\r\nTimestamp: 2016-04-11 18:00:12Z",
  "error_codes": [
    70002,
    70008
  ],
  "timestamp": "2016-04-11 18:00:12Z",
  "trace_id": "3939d04c-d7ba-42bf-9cb7-1e5854cdce9e",
  "correlation_id": "a8125194-2dc8-4078-90ba-7b6592a7f231"
}
```
| Parâmetro | Descrição |
| --- | --- |
| error |Uma cadeia de caracteres de códigos de erro que pode ser usada para classificar tipos de erro que ocorrem e pode ser usada para responder aos erros. |
| error_description |Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro de autenticação. |
| error_codes |Uma lista de códigos de erro específicos do STS que pode ajudar no diagnóstico. |
| timestamp |A hora na qual o erro ocorreu. |
| trace_id |Um identificador exclusivo para a solicitação que pode ajudar no diagnóstico. |
| correlation_id |Um identificador exclusivo para a solicitação que pode ajudar no diagnóstico entre os componentes. |

#### Códigos de status HTTP
A tabela a seguir lista os códigos de status HTTP que o ponto de extremidade de emissão de token retorna. Em alguns casos, o código de erro é suficiente para descrever a resposta, mas se houver erros, você precisará analisar o documento JSON complementar e examinar seu código de erro.

| Código HTTP | Descrição |
| --- | --- |
| 400 |Código HTTP padrão. Usado na maioria dos casos e, normalmente, é devido a uma solicitação malformada. Corrija e reenvie a solicitação. |
| 401 |Falha na autenticação. Por exemplo, a solicitação não tem o parâmetro client_secret. |
| 403 |Falha na autorização. Por exemplo, o usuário não tem permissão para acessar o recurso. |
| 500 |Erro interno no serviço. Tente novamente a solicitação. |

#### Códigos de erro para erros de ponto de extremidade de token
| Código do Erro | Descrição | Ação do Cliente |
| --- | --- | --- |
| invalid_request |Erro de protocolo, como um parâmetro obrigatório ausente. |Corrija e reenvie a solicitação |
| invalid_grant |O código de autorização é inválido ou expirou. |Tente uma nova solicitação para o `/authorize` ponto de extremidade |
| unauthorized_client |O cliente autenticado não está autorizado a usar esse tipo de concessão de autorização. |Isso geralmente ocorre quando o aplicativo cliente não está registrado no Azure AD ou não é adicionado ao locatário do Azure AD do usuário. O aplicativo pode solicitar que o usuário instale o aplicativo e o adicione ao Azure AD. |
| invalid_client |Falha na autenticação de cliente. |As credenciais do cliente não são válidas. Para corrigi-las, o administrador do aplicativo atualiza as credenciais. |
| unsupported_grant_type |O servidor de autorização não dá suporte ao tipo de concessão de autorização. |Altere o tipo de concessão na solicitação. Esse tipo de erro deve ocorrer somente durante o desenvolvimento e ser detectado durante os testes iniciais. |
| invalid_resource |O recurso de destino é inválido porque não existe, o Azure AD não consegue encontrá-lo ou ele não está configurado corretamente. |Isso indica que o recurso, se ele existe, não foi configurado no locatário. O aplicativo pode solicitar que o usuário instale o aplicativo e o adicione ao Azure AD. |
| interaction_required |A solicitação requer interação do usuário. Por exemplo, é necessária uma etapa de autenticação adicional. | Em vez de uma solicitação não interativa, tente novamente com uma solicitação de autorização interativa para o mesmo recurso. |
| temporarily_unavailable |O servidor está temporariamente muito ocupado para tratar da solicitação. |Tente novamente a solicitação. O aplicativo cliente pode explicar para o usuário que sua resposta está atrasada devido a uma condição temporária. |

## Usar o token de acesso para acessar o recurso
Agora que você já adquiriu com êxito um `access_token`, você pode usar o token em solicitações para APIs Web incluindo-o no cabeçalho `Authorization`. A especificação [RFC 6750](http://www.rfc-editor.org/rfc/rfc6750.txt) explica como usar os tokens de portador em solicitações HTTP para acessar recursos protegidos.

### Solicitação de exemplo
```
GET /data HTTP/1.1
Host: service.contoso.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ
```

### Resposta de erro
Recursos protegidos que implementam RFC 6750 emitem códigos de status HTTP. Se a solicitação não tem credenciais de autenticação ou o token está ausente, a resposta inclui um cabeçalho `WWW-Authenticate` . Quando uma solicitação falha, o servidor de recursos responde com o código de status HTTP e um código de erro.

Este é um exemplo de uma resposta malsucedida quando a solicitação do cliente não inclui o token de portador:

```
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Bearer authorization_uri="https://login.microsoftonline.com/contoso.com/oauth2/authorize",  error="invalid_token",  error_description="The access token is missing.",
```

#### Parâmetros de erro
| Parâmetro | Descrição |
| --- | --- |
| authorization_uri |O URI (ponto de extremidade físico) do servidor de autorização. Esse valor também é usado como uma chave de pesquisa para obter mais informações sobre o servidor de um ponto de extremidade de descoberta. <p><p> O cliente deve validar que o servidor de autorização é confiável. Quando o recurso é protegido pelo Azure AD, é suficiente confirmar que a URL começa com https://login.microsoftonline.com ou com outro nome do host a que o Azure AD dá suporte. Um recurso específico de locatário sempre deve retornar um URI de autorização específico de locatário. |
| error |Um valor de código de erro definido na Seção 5.2 da [Estrutura de Autorização OAuth 2.0](http://tools.ietf.org/html/rfc6749). |
| error_description |Uma descrição mais detalhada do erro. Esta mensagem não se destina a ser amigável para o usuário final. |
| resource_id |Retorna o identificador exclusivo do recurso. O aplicativo cliente pode usar esse identificador como o valor do parâmetro `resource` ao solicitar um token para o recurso. <p><p> É importante para o aplicativo cliente verificar esse valor, caso contrário, um serviço mal-intencionado poderá induzir um ataque de **elevação de privilégios** <p><p> A estratégia recomendada para evitar um ataque é verificar se `resource_id` corresponde à base da URL da API Web que está sendo acessada. Por exemplo, se https://service.contoso.com/data estiver sendo acessado, `resource_id` poderá ser htttps://service.contoso.com/. O aplicativo cliente deverá rejeitar um `resource_id` que não comece com a URL base, a menos que haja uma maneira alternativa confiável para verificar a ID. |

#### Códigos de erro de esquema de portador
A especificação RFC 6750 define os erros a seguir para recursos que usam o cabeçalho WWW-Authenticate e o esquema de Portador na resposta.

| Código de status HTTP | Código do Erro | Descrição | Ação do Cliente |
| --- | --- | --- | --- |
| 400 |invalid_request |A solicitação não está bem formada. Por exemplo, pode estar sem um parâmetro ou usando o mesmo parâmetro duas vezes. |Corrija o erro e repita a solicitação. Esse tipo de erro deve ocorrer somente durante o desenvolvimento e deve ser detectado nos testes iniciais. |
| 401 |invalid_token |O token de acesso está ausente, é inválido ou foi revogado. O valor do parâmetro error_description fornece detalhes adicionais. |Solicite um novo token do servidor de autorização. Se o novo token falhar, isso indicará que ocorreu um erro inesperado. Envie uma mensagem de erro ao usuário e tente novamente após intervalos aleatórios. |
| 403 |insufficient_scope |O token de acesso não tem as permissões de representação necessárias para acessar o recurso. |Envie uma nova solicitação de autorização ao ponto de extremidade de autorização. Se a resposta contiver o parâmetro de escopo, use o valor de escopo na solicitação para o recurso. |
| 403 |insufficient_access |A entidade do token não tem as permissões necessárias para acessar o recurso. |Solicite que o usuário use uma conta diferente ou solicite permissões para o recurso especificado. |

## Atualização dos tokens de acesso
Os Tokens de Acesso têm curta duração e deverão ser atualizados depois de expirados para continuarem acessando recursos. Você pode atualizar o `access_token` ao enviar outra solicitação `POST` ao ponto de extremidade `/token`, mas dessa vez fornecendo o `refresh_token` em vez do `code`.

Os tokens de atualização não têm um tempo de vida especificado. Normalmente, os tempos de vida de tokens de atualização são relativamente longos. No entanto, em alguns casos, os tokens de atualização expiram, são revogados ou não têm privilégios suficientes para a ação desejada. Seu aplicativo precisa esperar e tratar os erros retornados pelo ponto de extremidade de emissão de token corretamente.

Quando você receber uma resposta com um erro de token de atualização, descarte o token de atualização atual e solicite um novo código de autorização ou um token de acesso. Em particular, quando usar um token de atualização no fluxo de Concessão de Código de Autorização, se você receber uma resposta com os códigos de erro `interaction_required` ou `invalid_grant`, descarte o token de atualização e solicite um novo código de autorização.

Uma solicitação de exemplo para o ponto de extremidade **específico do locatário** (você também pode usar o ponto de extremidade **comum**) para obter um novo token de acesso usando um token de atualização que tem esta aparência:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&grant_type=refresh_token
&resource=https%3A%2F%2Fservice.contoso.com%2F
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```

### Resposta bem-sucedida
Uma resposta de token bem-sucedida se parecerá com esta:

```
{
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1460404526",
  "resource": "https://service.contoso.com/",
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "refresh_token": "AwABAAAAv YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl PM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfmVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA"
}
```
| Parâmetro | Descrição |
| --- | --- |
| token_type |O tipo de token. O único valor com suporte é **portador**. |
| expires_in |O tempo de vida restante do token em segundos. Um valor típico é 3600 (uma hora). |
| expires_on |A data e a hora em que o token expira. A data é representada como o número de segundos de 1970-01-01T0:0:0Z UTC até a hora de expiração. |
| recurso |Identifica o recurso protegido que pode ser acessado pelo token de acesso. |
| scope |As permissões de representação concedidas ao aplicativo cliente nativo. A permissão padrão é **user_impersonation**. O proprietário do recurso de destino pode registrar valores alternativos no Azure AD. |
| access_token |O novo token de acesso solicitado. |
| refresh_token |Um novo refresh_token do OAuth 2.0 que pode ser usado para solicitar novos tokens de acesso quando um expirar nesta resposta. |

### Resposta de erro
Uma resposta de erro de exemplo se parece com esta:

```
{
  "error": "invalid_resource",
  "error_description": "AADSTS50001: The application named https://foo.microsoft.com/mail.read was not found in the tenant named 295e01fc-0c56-4ac3-ac57-5d0ed568f872.  This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant.  You might have sent your authentication request to the wrong tenant.\r\nTrace ID: ef1f89f6-a14f-49de-9868-61bd4072f0a9\r\nCorrelation ID: b6908274-2c58-4e91-aea9-1f6b9c99347c\r\nTimestamp: 2016-04-11 18:59:01Z",
  "error_codes": [
    50001
  ],
  "timestamp": "2016-04-11 18:59:01Z",
  "trace_id": "ef1f89f6-a14f-49de-9868-61bd4072f0a9",
  "correlation_id": "b6908274-2c58-4e91-aea9-1f6b9c99347c"
}
```

| Parâmetro | Descrição |
| --- | --- |
| error |Uma cadeia de caracteres de códigos de erro que pode ser usada para classificar tipos de erro que ocorrem e pode ser usada para responder aos erros. |
| error_description |Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro de autenticação. |
| error_codes |Uma lista de códigos de erro específicos do STS que pode ajudar no diagnóstico. |
| timestamp |A hora na qual o erro ocorreu. |
| trace_id |Um identificador exclusivo para a solicitação que pode ajudar no diagnóstico. |
| correlation_id |Um identificador exclusivo para a solicitação que pode ajudar no diagnóstico entre os componentes. |

Para obter uma descrição dos códigos de erro e a ação recomendada do cliente, veja [Códigos de erro para erros de ponto de extremidade de token](#error-codes-for-token-endpoint-errors).
