---
title: Entrada na Web com o OpenID Connect - Azure AD B2C | Microsoft Docs
description: "Criação de aplicativos Web usando a implementação do Azure Active Directory do protocolo de autenticação OpenID Connect"
services: active-directory-b2c
documentationcenter: 
author: saeedakhter-msft
manager: krassk
editor: parakhj
ms.assetid: 21d420c8-3c10-4319-b681-adf2e89e7ede
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: saeedakhter-msft
ms.openlocfilehash: b0c33a47dd0cae79eab32ac578448fae8bf59be5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-web-sign-in-with-openid-connect"></a>Azure Active Directory B2C: entrada na Web com o OpenID Connect
O OpenID Connect é um protocolo de autenticação criado com base em OAuth 2.0 que pode ser usado para conectar com segurança os usuários em aplicativos Web. Ao usar a implementação do Azure AD B2C (Azure Active Directory B2C) do OpenID Connect, você pode terceirizar a inscrição, a entrada e outras experiências de gerenciamento de identidade em seus aplicativos Web para o Azure AD (Azure Active Directory). Este guia mostra como fazer isso de maneira independente da linguagem. Ele descreve como enviar e receber mensagens HTTP sem usar qualquer uma das nossas bibliotecas de software livre.

O [OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) estende o protocolo de *autorização* do OAuth 2.0 a ser usado como um protocolo de *autenticação*. Isso permite que você execute o logon único usando OAuth. Ele apresenta o conceito de um *token de ID*, que é um token de segurança que permite ao cliente verificar a identidade do usuário e obter informações básicas de perfil sobre o usuário.

Como ele estende o OAuth 2.0, também permite que os aplicativos adquiram *tokens de acesso* com segurança. Você pode usar access_tokens para acessar os recursos protegidos por um [servidor de autorização](active-directory-b2c-reference-protocols.md#the-basics). Nós recomendamos o OpenID Connect se você estiver criando um aplicativo Web que fica hospedado em um servidor e é acessado por meio de um navegador. Se você deseja adicionar o gerenciamento de identidades para seus aplicativos móveis ou para área de trabalho usando o Azure AD B2C, deve usar o [OAuth 2.0](active-directory-b2c-reference-oauth-code.md) em vez do OpenID Connect.

O Azure AD B2C estende o protocolo padrão OpenID Connect para fazer mais do que uma simples ação de autenticação e autorização. Ele apresenta o [parâmetro de política](active-directory-b2c-reference-policies.md), que permite usar o OpenID Connect para adicionar experiências de usuário, como o gerenciamento de inscrição, de entrada e de perfis, ao seu aplicativo. Aqui, mostramos como usar o OpenID Connect e as políticas para implementar cada uma dessas experiências em seus aplicativos Web. Também mostraremos como obter tokens de acesso para acessar APIs Web.

As solicitações HTTP de exemplo na próxima sessão usam nosso diretório B2C de exemplo, fabrikamb2c.onmicrosoft.com, bem como nossas políticas e nosso aplicativo de exemplo https://aadb2cplayground.azurewebsites.net. Fique à vontade para experimentar as solicitações usando esses valores ou os substituindo pelos seus próprios.
Saiba como [obter seus próprios locatário, aplicativo e políticas B2C](#use-your-own-b2c-directory).

## <a name="send-authentication-requests"></a>Enviar solicitações de autenticação
Quando o aplicativo Web precisa autenticar o usuário e executar uma política, ele pode direcionar o usuário para o ponto de extremidade `/authorize` . Essa é a parte interativa do fluxo, em que o usuário realiza uma ação, dependendo da política.

Nessa solicitação, o cliente indica as permissões que precisa adquirir do usuário no parâmetro `scope` e a política a ser executada no parâmetro `p`. Três exemplos são fornecidos nas seções a seguir (com quebras de linha para facilitar a leitura), cada um com uma política diferente. Para ter uma ideia de como funciona cada solicitação, tente colar a solicitação em um navegador e executá-lo.

#### <a name="use-a-sign-in-policy"></a>Usar uma política de entrada
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_in
```

#### <a name="use-a-sign-up-policy"></a>Usar uma política de inscrição
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_up
```

#### <a name="use-an-edit-profile-policy"></a>Usar uma política de edição de perfil
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_edit_profile
```

| Parâmetro | Obrigatório? | Descrição |
| --- | --- | --- |
| client_id |Obrigatório |A ID do aplicativo que o [portal do Azure](https://portal.azure.com/) atribuiu a seu aplicativo. |
| response_type |Obrigatório |O tipo de resposta, que deve incluir token de ID para o OpenID Connect. Se seu aplicativo Web também precisa de tokens para chamar uma API Web, você pode usar `code+id_token`, como fizemos aqui. |
| redirect_uri |Recomendadas |O parâmetro `redirect_uri` do seu aplicativo, em que as respostas de autenticação podem ser enviadas e recebidas pelo aplicativo. Ele deve corresponder exatamente a um dos parâmetros `redirect_uri` que você registrou no portal, com exceção de que ele deve ser codificado por URL. |
| scope |Obrigatório |Uma lista de escopos separados por espaços. Um único valor de escopo indica ao Azure AD ambas as permissões que estão sendo solicitadas. O escopo `openid` indica uma permissão para conectar o usuário e obter dados sobre ele na forma de tokens de ID (falaremos mais sobre isso posteriormente neste artigo). O escopo `offline_access` é opcional para aplicativos Web. Ele indica que seu aplicativo precisará de um *token de atualização* para obter acesso de longa duração aos recursos. |
| response_mode |Recomendadas |O método que deve ser usado para enviar o código de autorização resultante de volta ao aplicativo. Ele pode ser `query`, `form_post` ou `fragment`.  O modo de resposta `form_post` é recomendado para maior segurança. |
| state |Recomendadas |Um valor incluído na solicitação que também retorna na resposta do token. Pode ser uma cadeia de caracteres de qualquer conteúdo desejado. Um valor exclusivo gerado aleatoriamente que normalmente é usado para impedir ataques de solicitação intersite forjada. O estado também é usado para codificar as informações sobre o estado do usuário no aplicativo antes da solicitação de autenticação ocorrida, como a página em que ele estava. |
| nonce |Obrigatório |Um valor incluído na solicitação(gerado pelo aplicativo) que será incluído no token de ID resultante como uma declaração. O aplicativo pode verificar esse valor para reduzir os ataques de reprodução de token. Normalmente, o valor é uma cadeia de caracteres aleatória e exclusiva que pode ser usada para identificar a origem da solicitação. |
| p |Obrigatório |A política que será executada. É o nome de uma política criada no locatário B2C. O valor do nome da política deve começar com `b2c\_1\_`. Saiba mais sobre as políticas e a [estrutura de política extensível](active-directory-b2c-reference-policies.md). |
| prompt |Opcional |O tipo de interação do usuário que é necessário. O único valor válido no momento é `login`, que força o usuário a inserir suas credenciais nessa solicitação. O logon único não terá efeito. |

Nesse momento, é solicitado que o usuário conclua o fluxo de trabalho da política. Isso pode exigir que o usuário insira seu nome de usuário e senha, entre com uma identidade social, inscreva-se no diretório ou outras etapas, dependendo de como a política está definida.

Depois que o usuário conclui a política, o Azure AD retorna uma resposta ao seu aplicativo no parâmetro `redirect_uri` indicado, usando o método especificado no parâmetro `response_mode`. A resposta é a mesma para cada um dos casos anteriores, independentemente da política que é executada.

Uma resposta bem-sucedida usando `response_mode=fragment` se parece com esta:

```
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parâmetro | Descrição |
| --- | --- |
| id_token |O token de ID que o aplicativo solicitou. Você pode usar o token de ID para verificar a identidade do usuário e iniciar uma sessão com o usuário. Mais detalhes sobre tokens de ID e respectivo conteúdo estão incluídos na [referência de token do Azure AD B2C](active-directory-b2c-reference-tokens.md). |
| código |O código de autorização que o aplicativo solicitou, se você usou `response_type=code+id_token`. O aplicativo pode usar o código de autorização para solicitar um token de acesso para um recurso de destino. Os códigos de autorização têm uma duração muito curta. Normalmente, eles expiram depois de cerca de 10 minutos. |
| state |Se um parâmetro `state` estiver incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores `state` na solicitação e na resposta são idênticos. |

As respostas de erro também podem ser enviadas ao parâmetro `redirect_uri` para que o aplicativo possa tratá-las adequadamente:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parâmetro | Descrição |
| --- | --- |
| error |Uma cadeia de caracteres de códigos de erro que pode ser usada para classificar tipos de erro que ocorrem e pode ser usada para responder aos erros. |
| error_description |Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro de autenticação. |
| state |Confira a descrição completa da primeira tabela nesta seção. Se um parâmetro `state` estiver incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores `state` na solicitação e resposta são idênticos. |

## <a name="validate-the-id-token"></a>Validar o token de ID
Apenas o recebimento de um tokend de ID não é suficiente para autenticar o usuário. Você deve validar a assinatura do token de ID e verificar as declarações no token, de acordo com os requisitos do seu aplicativo. O Azure AD B2C usa [JWTs (Tokens Web JSON)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) e criptografia de chave pública para assinar tokens e verificar se eles são válidos.

Há muitas bibliotecas de software livre para validar JWTs dependendo do idioma de preferência. Recomendamos que você explore essas opções em vez de implementar a sua própria lógica de validação. As informações aqui serão úteis para descobrir como usar essas bibliotecas adequadamente.

O Azure AD B2C tem um ponto de extremidade de metadados do OpenID Connect, que permite a um aplicativo buscar informações sobre o Azure AD B2C no tempo de execução. Essas informações incluem pontos de extremidade, conteúdos de token e chaves de assinatura de token. Há um documento de metadados JSON para cada política no seu locatário de B2C. Por exemplo, o documento de metadados para a política `b2c_1_sign_in` em `fabrikamb2c.onmicrosoft.com` está localizado em:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

Uma das propriedades desse documento de configuração é a `jwks_uri`, cujo valor para a mesma política seria:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`.

Para determinar qual política foi usada na assinatura de um token de ID (e onde buscar os metadados), você tem duas opções. Primeiro, o nome da política é incluído na declaração `acr` no token de ID. Para obter informações sobre como analisar as declarações de um token de ID, consulte a [Referência de token do Azure AD B2C](active-directory-b2c-reference-tokens.md). A outra opção é codificar a política no valor do parâmetro `state` quando você emitir a solicitação e, em seguida, decodificá-lo para determinar qual política foi usada. Ambos os métodos são válidos.

Depois que tiver adquirido o documento de metadados do ponto de extremidade de metadados do OpenID Connect, você poderá usar as chaves públicas RSA 256 (localizadas nesse ponto de extremidade) para validar a assinatura do token de ID. Poderá haver várias chaves listadas nesse ponto de extremidade em qualquer ponto no tempo, cada uma identificada por uma declaração `kid`. O cabeçalho do token de ID também contém uma declaração `kid`, que indica quais dessas chaves foi usada para assinar o token de ID. Para obter mais informações, consulte a [referência de token do Azure AD B2C](active-directory-b2c-reference-tokens.md) (especialmente a seção [validando tokens](active-directory-b2c-reference-tokens.md#token-validation)).
<!--TODO: Improve the information on this-->

Depois de validar a assinatura do token de ID, há várias declarações que você precisa verificar. Por exemplo:

* Você deve validar a declaração `nonce` para evitar ataques de reprodução de token. Seu valor deve ser o que você especificou na solicitação de conexão.
* Você deve validar a declaração `aud` para garantir que o token de ID foi emitido para o seu aplicativo. Seu valor deve ser a ID do aplicativo do seu aplicativo.
* Você deve validar as declarações `iat` e `exp` para garantir que o token de ID não tenha se expirado.

Também há várias outras validações que devem ser realizadas. Elas estão descritas detalhadamente nas [Especificações básicas do OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html).  Talvez você também queira validar declarações adicionais, dependendo do cenário. Algumas validações comuns incluem:

* A garantia de que o usuário/organização tenha feito inscrição para usar o aplicativo.
* A garantia de que o usuário tenha a autorização/os privilégios adequados.
* A garantia de que uma determinada intensidade de autenticação tenha ocorrido, como o Azure Multi-Factor Authentication.

Para obter mais informações sobre as declarações em um token de ID, consulte a [referência de token do Azure AD B2C](active-directory-b2c-reference-tokens.md).

Após validar o token de ID, você poderá iniciar uma sessão com o usuário. Você pode usar as declarações no token de ID para obter informações sobre o usuário em seu aplicativo. Os usos para essas informações incluem exibição, registros e autorização.

## <a name="get-a-token"></a>Obter um token
Se você precisar de seu aplicativo Web somente para executar as políticas, você poderá ignorar as próximas seções. Essas seções serão aplicáveis somente aos aplicativos Web que precisarem fazer chamadas autenticadas a uma API Web e que também sejam protegidas pelo Azure AD B2C.

Você pode resgatar o código de autorização adquirido (usando `response_type=code+id_token`) para um token do recurso desejado enviando uma solicitação `POST` ao ponto de extremidade `/token`. Atualmente, o único recurso para o qual você pode solicitar um token é a própria API Web de back-end do aplicativo. A convenção para solicitar um token para si mesmo é usar a ID do cliente do aplicativo como o escopo:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>

```

| Parâmetro | Obrigatório? | Descrição |
| --- | --- | --- |
| p |Obrigatório |A política que foi usada para adquirir o código de autorização. Você não poderá usar uma política diferente nessa solicitação. Observe que esse parâmetro é adicionado à cadeia de caracteres de consulta e não ao corpo do `POST`. |
| client_id |Obrigatório |A ID do aplicativo que o [portal do Azure](https://portal.azure.com/) atribuiu a seu aplicativo. |
| grant_type |Obrigatório |O tipo de concessão, que deve ser `authorization_code` para o fluxo do código de autorização. |
| scope |Recomendadas |Uma lista de escopos separados por espaços. Um único valor de escopo indica ao Azure AD ambas as permissões que estão sendo solicitadas. O escopo `openid` indica uma permissão para conectar o usuário e obter dados sobre ele na forma de parâmetros de id_token. Ele pode ser usado para obter tokens para a própria API Web de back-end do seu aplicativo, representado pela mesma ID do aplicativo que o cliente. O escopo `offline_access` indica que o aplicativo precisará de um token de atualização para acessar os recursos de longa duração. |
| código |Obrigatório |O código de autorização que você adquiriu no primeiro segmento do fluxo. |
| redirect_uri |Obrigatório |O parâmetro `redirect_uri` do aplicativo em que você recebeu o código de autorização. |
| client_secret |Obrigatório |O segredo do aplicativo gerado no [portal do Azure](https://portal.azure.com/). O segredo do aplicativo é um artefato de segurança importante. Você deve armazená-lo com segurança no servidor. Você também deve circular esse segredo do cliente em intervalos periódicos. |

Uma resposta de token bem-sucedida tem a seguinte aparência:

```
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| Parâmetro | Descrição |
| --- | --- |
| not_before |A hora em que o token é considerado válido, nos valores de hora da época. |
| token_type |O valor do tipo de token. O único tipo com suporte do Azure AD é o `Bearer`. |
| access_token |O token JWT assinado que você solicitou. |
| scope |Os escopos para os quais o token é válido. Eles podem ser usados para o cache de tokens para uso posterior. |
| expires_in |O período de tempo pelo qual o token de acesso é válido (em segundos). |
| refresh_token |Um token de atualização do OAuth 2.0. O aplicativo pode usar esse token para adquirir tokens de acesso adicionais depois que o atual expirar. Os tokens de atualização têm longa duração e podem ser usados para reter acesso a recursos por períodos estendidos. Para obter mais detalhes, consulte a [referência de token B2C](active-directory-b2c-reference-tokens.md). Observe que você deve ter usado o escopo `offline_access` tanto na autorização quanto nas solicitações de token para receber um token de atualização. |

As respostas de erro se parecem com:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parâmetro | Descrição |
| --- | --- |
| error |Uma cadeia de caracteres de códigos de erro que pode ser usada para classificar tipos de erro que ocorrem e pode ser usada para responder aos erros. |
| error_description |Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro de autenticação. |

## <a name="use-the-token"></a>Usar o token
Agora que adquiriu um token de acesso com êxito, você poderá usá-lo em solicitações às suas APIs Web de back-end incluindo-o no cabeçalho `Authorization`:

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>Atualizar o token
Os tokens de ID têm vida curta. Você deve atualizá-los depois que eles expirarem para poder continuar a acessar os recursos. Faça isso enviando outra solicitação `POST` ao ponto de extremidade `/token`. Desta vez, forneça o parâmetro `refresh_token` em vez do parâmetro `code`:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>
```

| Parâmetro | Obrigatório | Descrição |
| --- | --- | --- |
| p |Obrigatório |A política que foi usada para adquirir o token de atualização original. Você não poderá usar uma política diferente nessa solicitação. Observe que esse parâmetro é adicionado à cadeia de caracteres de consulta e não ao corpo do POST. |
| client_id |Obrigatório |A ID do aplicativo que o [portal do Azure](https://portal.azure.com/) atribuiu a seu aplicativo. |
| grant_type |Obrigatório |O tipo de concessão, que deve ser um token de atualização para esse segmento do fluxo do código de autorização. |
| scope |Recomendadas |Uma lista de escopos separados por espaços. Um único valor de escopo indica ao Azure AD ambas as permissões que estão sendo solicitadas. O escopo `openid` indica uma permissão para conectar o usuário e obter dados sobre ele na forma de tokens de ID. Ele pode ser usado para obter tokens para a própria API Web de back-end do seu aplicativo, representado pela mesma ID do aplicativo que o cliente. O escopo `offline_access` indica que o aplicativo precisará de um token de atualização para acessar os recursos de longa duração. |
| redirect_uri |Recomendadas |O parâmetro `redirect_uri` do aplicativo em que você recebeu o código de autorização. |
| refresh_token |Obrigatório |O token de atualização original que você adquiriu na segunda ramificação do fluxo. Observe que você deve ter usado o escopo `offline_access` tanto na autorização quanto nas solicitações de token para receber um token de atualização. |
| client_secret |Obrigatório |O segredo do aplicativo gerado no [portal do Azure](https://portal.azure.com/). O segredo do aplicativo é um artefato de segurança importante. Você deve armazená-lo com segurança no servidor. Você também deve circular esse segredo do cliente em intervalos periódicos. |

Uma resposta de token bem-sucedida tem a seguinte aparência:

```
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| Parâmetro | Descrição |
| --- | --- |
| not_before |A hora em que o token é considerado válido, nos valores de hora da época. |
| token_type |O valor do tipo de token. O único tipo com suporte do Azure AD é o `Bearer`. |
| access_token |O token JWT assinado que você solicitou. |
| scope |O escopo para o qual o token é válido, que pode ser usado no cache de tokens para uso posterior. |
| expires_in |O período de tempo pelo qual o token de acesso é válido (em segundos). |
| refresh_token |Um token de atualização do OAuth 2.0. O aplicativo pode usar esse token para adquirir tokens de acesso adicionais depois que o atual expirar.  Os tokens de atualização têm longa duração e podem ser usados para reter acesso a recursos por períodos estendidos. Para obter mais detalhes, consulte a [referência ao token B2C](active-directory-b2c-reference-tokens.md). |

As respostas de erro se parecem com:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parâmetro | Descrição |
| --- | --- |
| error |Uma cadeia de caracteres de códigos de erro que pode ser usada para classificar tipos de erro que ocorrem e pode ser usada para responder aos erros. |
| error_description |Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro de autenticação. |

## <a name="send-a-sign-out-request"></a>Enviar uma solicitação de saída
Quando você deseja conectar o usuário do aplicativo, não é suficiente limpar os cookies do aplicativo e encerrar a sessão do usuário. Você também deve redirecionar o usuário ao Azure AD para sair. Se você não conseguir fazer isso, é provável que o usuário consiga se autenticar novamente no aplicativo sem ter que reinserir as credenciais. Isso ocorre porque eles terão uma sessão de logon único válida com o Azure AD.

Você pode simplesmente redirecionar o usuário para o ponto de extremidade `end_session` listado no documento de metadados do OpenID Connect descrito anteriormente na seção “Validar o token de ID”:

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Parâmetro | Obrigatório? | Descrição |
| --- | --- | --- |
| p |Obrigatório |A política que você deseja usar para desconectar o usuário de seu aplicativo. |
| post_logout_redirect_uri |Recomendadas |A URL para a qual o usuário deve ser redirecionado após a saída com êxito. Se não for incluída, o Azure AD B2C mostrará uma mensagem genérica ao usuário. |

> [!NOTE]
> Embora o direcionamento do usuário para o ponto de extremidade `end_session` remova algum dos estados de logon único do usuário com o Azure AD B2C, ele não o desconectará da sessão de IDP (provedor de identidade) social. Se o usuário selecionar o mesmo IDP durante uma conexão posterior, ele será autenticados novamente sem precisar inserir suas credenciais. Se um usuário deseja sair do seu aplicativo B2C, isso não significa, necessariamente, que ele deseja desconectar-se de sua conta do Facebook. No entanto, no caso de contas locais, a sessão do usuário será encerrada corretamente.
> 
> 

## <a name="use-your-own-b2c-tenant"></a>Usar seu próprio locatário B2C
Se você quiser experimentar essas solicitações por conta própria, primeiro deverá seguir estas três etapas e, em seguida, substituir os valores descritos anteriormente pelos seus próprios valores:

1. [Criar um locatário de B2C](active-directory-b2c-get-started.md)e usar o nome do seu locatário nas solicitações.
2. [Criar um aplicativo](active-directory-b2c-app-registration.md) para obter uma ID de aplicativo. Inclui um aplicativo Web/uma API Web em seu aplicativo. Opcionalmente, criar um segredo do aplicativo.
3. [Criar suas regras](active-directory-b2c-reference-policies.md) para obter os nomes de política.

