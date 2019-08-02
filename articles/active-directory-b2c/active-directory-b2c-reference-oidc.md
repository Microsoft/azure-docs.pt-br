---
title: Entrada na Web com OpenID Connect - Azure Active Directory B2C | Microsoft Docs
description: Crie aplicativos Web usando o protocolo de Autenticação OpenID Connect no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: c5626e2ddfc24eeaeed562f3eaf73d16626eb458
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68278028"
---
# <a name="web-sign-in-with-openid-connect-in-azure-active-directory-b2c"></a>Entrada na Web com o OpenID Connect no Azure Active Directory B2C

O OpenID Connect é um protocolo de autenticação criado com base em OAuth 2.0 que pode ser usado para conectar com segurança os usuários em aplicativos Web. Ao usar a implementação do Azure AD B2C (Azure Active Directory B2C) do OpenID Connect, você pode terceirizar a inscrição, a entrada e outras experiências de gerenciamento de identidade em seus aplicativos Web para o Azure AD (Azure Active Directory). Este guia mostra como fazer isso de maneira independente da linguagem. Ele descreve como enviar e receber mensagens HTTP sem usar qualquer uma das nossas bibliotecas de software livre.

O [OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) estende o protocolo de *autorização* do OAuth 2.0 a ser usado como um protocolo de *autenticação*. Esse protocolo de autenticação permite que você execute o logon único. Ele apresenta o conceito de *token de ID*, que permite ao cliente verificar a identidade do usuário e obter informações básicas de perfil sobre o usuário.

Como ele estende o OAuth 2,0, ele também permite que os aplicativos adquiram tokens de *acesso*com segurança. Você pode usar tokens de acesso para acessar os recursos protegidos por um [servidor de autorização](active-directory-b2c-reference-protocols.md). O OpenID Connect é recomendado se você estiver criando um aplicativo Web hospedado em um servidor e acessado por meio de um navegador. Se você quiser adicionar o gerenciamento de identidades a seus aplicativos móveis ou de área de trabalho usando Azure AD B2C, deverá usar o [OAuth 2,0](active-directory-b2c-reference-oauth-code.md) em vez do OpenID Connect. Para obter mais informações sobre tokens, consulte a [visão geral dos tokens no Azure Active Directory B2C](active-directory-b2c-reference-tokens.md)

O Azure AD B2C estende o protocolo padrão OpenID Connect para fazer mais do que uma simples ação de autenticação e autorização. Ele apresenta o [parâmetro de fluxo de usuário](active-directory-b2c-reference-policies.md), que permite que você use o OpenID Connect para adicionar experiências de usuário ao seu aplicativo, como inscrição, entrada e gerenciamento de perfil.

## <a name="send-authentication-requests"></a>Enviar solicitações de autenticação

Quando seu aplicativo Web precisa autenticar o usuário e executar um fluxo de usuário, ele pode direcionar o usuário `/authorize` para o ponto de extremidade. O usuário executa a ação dependendo do fluxo do usuário.

Nessa solicitação, o cliente indica as permissões que precisa adquirir do usuário no `scope` parâmetro e o fluxo do usuário para ser executado `p` no parâmetro. Três exemplos são fornecidos nas seções a seguir (com quebras de linha para legibilidade), cada um usando um fluxo de usuário diferente. Para ter uma ideia de como funciona cada solicitação, tente colar a solicitação em um navegador e executá-lo. Você pode substituir `fabrikamb2c` pelo nome do seu locatário se tiver um e tiver criado um fluxo de usuário.

#### <a name="use-a-sign-in-user-flow"></a>Usar um fluxo de usuário de entrada
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_in
```

#### <a name="use-a-sign-up-user-flow"></a>Usar um fluxo de usuário de inscrição
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_up
```

#### <a name="use-an-edit-profile-user-flow"></a>Usar um fluxo de usuário do perfil de edição
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_edit_profile
```

| Parâmetro | Obrigatório | DESCRIÇÃO |
| --------- | -------- | ----------- |
| client_id | Sim | A ID do aplicativo que o [portal do Azure](https://portal.azure.com/) atribuído ao seu aplicativo. |
| response_type | Sim | Deve incluir um token de ID para o OpenID Connect. Se o aplicativo Web também precisar de tokens para chamar uma API da Web, `code+id_token`você poderá usar o. |
| redirect_uri | Não | O `redirect_uri` parâmetro do seu aplicativo, em que as respostas de autenticação podem ser enviadas e recebidas pelo seu aplicativo. Ele deve corresponder exatamente a `redirect_uri` um dos parâmetros que você registrou na portal do Azure, exceto que ele deve ser codificado por URL. |
| scope | Sim | Uma lista de escopos separados por espaços. O escopo `openid` indica uma permissão para entrar no usuário e obter dados sobre ele na forma de tokens de ID. O `offline_access` escopo é opcional para aplicativos Web. Isso indica que seu aplicativo precisará de um *token de atualização* para acesso estendido aos recursos. |
| response_mode | Não | O método usado para enviar o código de autorização resultante de volta para seu aplicativo. Ele pode ser `query`, `form_post` ou `fragment`.  O modo de resposta `form_post` é recomendado para maior segurança. |
| state | Não | Um valor incluído na solicitação que também é retornado na resposta do token. Pode ser uma cadeia de caracteres de qualquer conteúdo desejado. Um valor exclusivo gerado aleatoriamente que normalmente é usado para impedir ataques de solicitação intersite forjada. O estado também é usado para codificar informações sobre o estado do usuário no aplicativo antes que a solicitação de autenticação ocorra, como a página em que eles estavam. |
| nonce | Sim | Um valor incluído na solicitação (gerado pelo aplicativo) que é incluído no token de ID resultante como uma declaração. O aplicativo pode, então, verificar esse valor para atenuar os ataques de reprodução de token. Normalmente, o valor é uma cadeia de caracteres aleatória e exclusiva que pode ser usada para identificar a origem da solicitação. |
| p | Sim | O fluxo do usuário que é executado. É o nome de um fluxo de usuário que é criado em seu locatário Azure AD B2C. O nome do fluxo do usuário deve começar com `b2c\_1\_`. |
| prompt | Não | O tipo de interação do usuário que é necessária. O único valor válido no momento é `login`, que força o usuário a inserir suas credenciais nessa solicitação. |

Neste ponto, o usuário é solicitado a concluir o fluxo de trabalho. O usuário pode precisar inserir seu nome de usuário e senha, entrar com uma identidade social ou inscrever-se no diretório. Pode haver qualquer outro número de etapas, dependendo de como o fluxo do usuário é definido.

Depois que o usuário conclui o fluxo do usuário, uma resposta é retornada ao seu aplicativo no parâmetro `redirect_uri` indicado, usando o método especificado `response_mode` no parâmetro. A resposta é a mesma para cada um dos casos anteriores, independentemente do fluxo do usuário.

Uma resposta bem-sucedida usando `response_mode=fragment` se parece com esta:

```
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parâmetro | DESCRIÇÃO |
| --------- | ----------- |
| id_token | O token de ID que o aplicativo solicitou. Você pode usar o token de ID para verificar a identidade do usuário e iniciar uma sessão com o usuário. |
| code | O código de autorização que o aplicativo solicitou, se `response_type=code+id_token`você usou. O aplicativo pode usar o código de autorização para solicitar um token de acesso para um recurso de destino. Os códigos de autorização normalmente expiram após cerca de 10 minutos. |
| state | Se um parâmetro `state` estiver incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os `state` valores na solicitação e na resposta são idênticos. |

As respostas de erro também podem ser enviadas `redirect_uri` ao parâmetro para que o aplicativo possa tratá-las adequadamente:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parâmetro | DESCRIÇÃO |
| --------- | ----------- |
| error | Um código que pode ser usado para classificar os tipos de erros que ocorrem. |
| error_description | Uma mensagem de erro específica que pode ajudar a identificar a causa raiz de um erro de autenticação. |
| state | Se um parâmetro `state` estiver incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os `state` valores na solicitação e na resposta são idênticos. |

## <a name="validate-the-id-token"></a>Validar o token de ID

Apenas o recebimento de um tokend de ID não é suficiente para autenticar o usuário. Valide a assinatura do token de ID e verifique as declarações no token de acordo com os requisitos do seu aplicativo. O Azure AD B2C usa [JWTs (Tokens Web JSON)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) e criptografia de chave pública para assinar tokens e verificar se eles são válidos. Há muitas bibliotecas de software livre para validar JWTs dependendo do idioma de preferência. Recomendamos que você explore essas opções em vez de implementar a sua própria lógica de validação. 

Azure AD B2C tem um ponto de extremidade de metadados do OpenID Connect, que permite que um aplicativo Obtenha informações sobre Azure AD B2C em tempo de execução. Essas informações incluem pontos de extremidade, conteúdos de token e chaves de assinatura de token. Há um documento de metadados JSON para cada fluxo de usuário no locatário B2C. Por exemplo, o documento de metadados para o fluxo de usuário `b2c_1_sign_in` em `fabrikamb2c.onmicrosoft.com` está localizado em:

`https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

Uma das propriedades desse documento de configuração é a `jwks_uri`, cujo valor para o mesmo fluxo de usuário seria:

`https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`.

Para determinar qual fluxo de usuário foi usado na assinatura de um token de ID (e de onde obter os metadados), você tem duas opções. Primeiro, o nome do fluxo de usuário é incluído na declaração `acr` no token de ID. A outra opção é codificar o fluxo de usuário no valor do parâmetro `state` quando você emitir a solicitação e, em seguida, decodificá-lo para determinar qual fluxo de usuário foi usado. Ambos os métodos são válidos.

Depois de adquirir o documento de metadados do ponto de extremidade de metadados do OpenID Connect, você pode usar as chaves públicas do RSA 256 para validar a assinatura do token de ID. Pode haver várias chaves listadas nesse ponto de extremidade, cada uma identificada por uma `kid` declaração. O cabeçalho do token de ID também contém uma declaração `kid`, que indica quais dessas chaves foi usada para assinar o token de ID.

Para verificar os tokens de Azure AD B2C, você precisa gerar a chave pública usando o expoente (e) e o módulo (n). Você precisa determinar como fazer isso em sua respectiva linguagem de programação de acordo. A documentação oficial sobre a geração de chave pública com o protocolo RSA pode ser encontrada aqui: https://tools.ietf.org/html/rfc3447#section-3.1

Depois de validar a assinatura do token de ID, há várias declarações que você precisa verificar. Por exemplo:

- Valide a declaração `nonce` para evitar ataques de reprodução de token. Seu valor deve ser o que você especificou na solicitação de conexão.
- Valide a `aud` declaração para garantir que o token de ID foi emitido para seu aplicativo. Seu valor deve ser a ID do aplicativo do seu aplicativo.
- Valide as `iat` declarações `exp` e para garantir que o token de ID não tenha expirado.

Também há várias outras validações que devem ser realizadas. As validações são descritas em detalhes na [especificação de núcleo do OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html). Talvez você também queira validar declarações adicionais, dependendo do cenário. Algumas validações comuns incluem:

- Garantir que o usuário/organização tenha se inscrito no aplicativo.
- A garantia de que o usuário tenha a autorização/os privilégios adequados.
- A garantia de que uma determinada intensidade de autenticação tenha ocorrido, como a Autenticação Multifator do Azure.

Depois de validar o token de ID, você pode iniciar uma sessão com o usuário. Você pode usar as declarações no token de ID para obter informações sobre o usuário em seu aplicativo. Os usos para essas informações incluem exibição, registros e autorização.

## <a name="get-a-token"></a>Obter um token

Se precisar que seu aplicativo Web execute apenas fluxos de usuário, você poderá ignorar as próximas seções. Essas seções são aplicáveis somente a aplicativos Web que precisam fazer chamadas autenticadas para uma API da Web e também são protegidas por Azure AD B2C.

Você pode resgatar o código de autorização adquirido (usando `response_type=code+id_token`) para um token do recurso desejado enviando uma solicitação `POST` ao ponto de extremidade `/token`. No Azure AD B2C, você pode [solicitar tokens de acesso para outras APIs](active-directory-b2c-access-tokens.md#request-a-token) como de costume, especificando seus escopos na solicitação.

Você também pode solicitar um token de acesso para a própria API Web de back-end do seu aplicativo por convenção de usar a ID do cliente do aplicativo como o escopo solicitado (o que resultará em um token de acesso com essa ID de cliente como o "público"):

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://fabrikamb2c.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>
```

| Parâmetro | Obrigatório | DESCRIÇÃO |
| --------- | -------- | ----------- |
| p | Sim | O fluxo de usuário que foi usado para adquirir o código de autorização. Você não pode usar um fluxo de usuário diferente nesta solicitação. Adicione esse parâmetro à cadeia de caracteres de consulta, não ao corpo da POSTAgem. |
| client_id | Sim | A ID do aplicativo que o [portal do Azure](https://portal.azure.com/) atribuído ao seu aplicativo. |
| grant_type | Sim | O tipo de concessão, que deve ser `authorization_code` para o fluxo do código de autorização. |
| scope | Não | Uma lista de escopos separados por espaços. O escopo `openid` indica uma permissão para conectar o usuário e obter dados sobre ele na forma de parâmetros de id_token. Ele pode ser usado para obter tokens para a própria API Web de back-end do seu aplicativo, que é representada pela mesma ID do aplicativo que o cliente. O `offline_access` escopo indica que seu aplicativo precisa de um token de atualização para acesso estendido aos recursos. |
| code | Sim | O código de autorização que você adquiriu no início do fluxo do usuário. |
| redirect_uri | Sim | O parâmetro `redirect_uri` do aplicativo em que você recebeu o código de autorização. |
| client_secret | Sim | O segredo do aplicativo que foi gerado no [portal do Azure](https://portal.azure.com/). O segredo do aplicativo é um artefato de segurança importante. Você deve armazená-lo com segurança no servidor. Altere esse segredo do cliente periodicamente. |

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
| Parâmetro | DESCRIÇÃO |
| --------- | ----------- |
| not_before | A hora em que o token é considerado válido, nos valores de hora da época. |
| token_type | O valor do tipo de token. `Bearer`é o único tipo com suporte. |
| access_token | O token JWT assinado que você solicitou. |
| scope | Os escopos para os quais o token é válido. |
| expires_in | O período de tempo pelo qual o token de acesso é válido (em segundos). |
| refresh_token | Um token de atualização do OAuth 2.0. O aplicativo pode usar esse token para adquirir tokens adicionais depois que o token atual expirar. Os tokens de atualização podem ser usados para manter o acesso aos recursos por longos períodos de tempo. O escopo `offline_access` deve ter sido usado tanto na autorização quanto nas solicitações de token para receber um token de atualização. |

As respostas de erro se parecem com:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parâmetro | DESCRIÇÃO |
| --------- | ----------- |
| error | Um código que pode ser usado para classificar tipos de erros que ocorrem. |
| error_description | Uma mensagem que pode ajudar a identificar a causa raiz de um erro de autenticação. |

## <a name="use-the-token"></a>Usar o token

Agora que adquiriu um token de acesso com êxito, você poderá usá-lo em solicitações às suas APIs Web de back-end incluindo-o no cabeçalho `Authorization`:

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>Atualizar o token

Tokens de ID expiram em um curto período de tempo. Atualize os tokens depois que eles expirarem para continuar a ser capaz de acessar recursos. Você pode atualizar um token enviando outra `POST` solicitação ao ponto de `/token` extremidade. Desta vez, forneça o parâmetro `refresh_token` em vez do parâmetro `code`:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://fabrikamb2c.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>
```

| Parâmetro | Obrigatório | DESCRIÇÃO |
| --------- | -------- | ----------- |
| p | Sim | O fluxo de usuário que foi usado para adquirir o token de atualização original. Você não pode usar um fluxo de usuário diferente nesta solicitação. Adicione esse parâmetro à cadeia de caracteres de consulta, não ao corpo da POSTAgem. |
| client_id | Sim | A ID do aplicativo que o [portal do Azure](https://portal.azure.com/) atribuído ao seu aplicativo. |
| grant_type | Sim | O tipo de concessão, que deve ser um token de atualização para esta parte do fluxo do código de autorização. |
| scope | Não | Uma lista de escopos separados por espaços. O escopo `openid` indica uma permissão para entrar no usuário e obter dados sobre ele na forma de tokens de ID. Ele pode ser usado para enviar tokens para a própria API Web de back-end do seu aplicativo, que é representada pela mesma ID do aplicativo que o cliente. O `offline_access` escopo indica que seu aplicativo precisa de um token de atualização para acesso estendido aos recursos. |
| redirect_uri | Não | O parâmetro `redirect_uri` do aplicativo em que você recebeu o código de autorização. |
| refresh_token | Sim | O token de atualização original que foi adquirido na segunda parte do fluxo. O `offline_access` escopo deve ser usado tanto na autorização quanto nas solicitações de token para receber um token de atualização. |
| client_secret | Sim | O segredo do aplicativo que foi gerado no [portal do Azure](https://portal.azure.com/). O segredo do aplicativo é um artefato de segurança importante. Você deve armazená-lo com segurança no servidor. Altere esse segredo do cliente periodicamente. |

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
| Parâmetro | DESCRIÇÃO |
| --------- | ----------- |
| not_before | A hora em que o token é considerado válido, nos valores de hora da época. |
| token_type | O valor do tipo de token. `Bearer`é o único tipo com suporte. |
| access_token | O token JWT assinado que foi solicitado. |
| scope | O escopo para o qual o token é válido. |
| expires_in | O período de tempo pelo qual o token de acesso é válido (em segundos). |
| refresh_token | Um token de atualização do OAuth 2.0. O aplicativo pode usar esse token para adquirir tokens adicionais depois que o token atual expirar. Os tokens de atualização podem ser usados para manter o acesso aos recursos por longos períodos de tempo. |

As respostas de erro se parecem com:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parâmetro | DESCRIÇÃO |
| --------- | ----------- |
| error | Um código que pode ser usado para classificar tipos de erros que ocorrem. |
| error_description | Uma mensagem que pode ajudar a identificar a causa raiz de um erro de autenticação. |

## <a name="send-a-sign-out-request"></a>Enviar uma solicitação de saída

Quando você deseja desconectar o usuário do aplicativo, não é suficiente limpar os cookies do aplicativo ou encerrar a sessão com o usuário. Redirecione o usuário para Azure AD B2C para sair. Se você não conseguir fazer isso, o usuário poderá se autenticar novamente em seu aplicativo sem inserir suas credenciais novamente.

Você pode simplesmente redirecionar o usuário `end_session` para o ponto de extremidade listado no documento de metadados do OpenID Connect descrito anteriormente:

```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Parâmetro | Obrigatório | DESCRIÇÃO |
| --------- | -------- | ----------- |
| p | Sim | O fluxo de usuário que você quer usar para desconectar o usuário do aplicativo. |
| post_logout_redirect_uri | Não | A URL para a qual o usuário deve ser redirecionado após a saída bem-sucedida. Se não estiver incluído, Azure AD B2C mostrará ao usuário uma mensagem genérica. |

Direcionar o usuário para o `end_session` ponto de extremidade limpa parte do estado de logon único do usuário com o Azure ad B2C, mas ele não desconecta o usuário de sua sessão do IDP (provedor de identidade social). Se o usuário selecionar o mesmo IDP durante uma entrada subsequente, ele será reautenticado, sem inserir suas credenciais. Se um usuário quiser sair do aplicativo, isso não significa necessariamente que deseja sair de sua conta do Facebook. No entanto, se forem usadas contas locais, a sessão do usuário será encerrada corretamente.

