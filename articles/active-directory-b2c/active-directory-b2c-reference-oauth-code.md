---
title: "Fluxo de código de autorização – Azure AD B2C | Microsoft Docs"
description: "Saiba como compilar aplicativos Web usando o Azure AD B2C e o protocolo de autenticação OpenID Connect."
services: active-directory-b2c
documentationcenter: 
author: saeedakhter-msft
manager: krassk
editor: parakhj
ms.assetid: c371aaab-813a-4317-97df-b62e2f53d865
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: saeedakhter-msft
ms.openlocfilehash: dfc4f2e84704307ccbea6141c0dbc8d089733b22
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-oauth-20-authorization-code-flow"></a>Azure Active Directory B2C: fluxo de código de autorização OAuth 2.0
Você pode usar a concessão de código de autorização OAuth 2.0 em aplicativos instalados em um dispositivo para obter acesso a recursos protegidos, como APIs Web. Usando a implementação do Azure AD B2C (Azure Active Directory B2C) do OAuth 2.0, você pode adicionar tarefas de inscrição, entrada e outras de gerenciamento de identidade aos seus aplicativos móveis e da área de trabalho. Este artigo é independente de linguagem. No artigo, descreveremos como enviar e receber mensagens HTTP sem usar nenhuma biblioteca de software livre.

<!-- TODO: Need link to libraries -->

O fluxo do código de autorização do OAuth 2.0 é descrito na [seção 4.1 da especificação do OAuth 2.0](http://tools.ietf.org/html/rfc6749). Você pode usá-lo para autenticação e autorização na maioria dos tipos de aplicativo, incluindo [aplicativos Web](active-directory-b2c-apps.md#web-apps) e [aplicativos instalados nativamente](active-directory-b2c-apps.md#mobile-and-native-apps). Você pode usar o fluxo de código de autorização OAuth 2.0 para adquirir *tokens de acesso* com segurança para seus aplicativos, que podem ser usados para acessar recursos protegidos por um [servidor de autorização](active-directory-b2c-reference-protocols.md#the-basics).

Este artigo concentra-se no fluxo de código de autorização do OAuth 2.0 de **clientes públicos**. Um cliente público é qualquer aplicativo cliente que não é confiável para manter a integridade de uma senha secreta com segurança. Isso inclui aplicativos móveis, aplicativos da área de trabalho e, basicamente, qualquer aplicativo que seja executado em um dispositivo e precise obter tokens de acesso. 

> [!NOTE]
> Para adicionar o gerenciamento de identidade em um aplicativo Web usando o Azure AD B2C, use o [OpenID Connect](active-directory-b2c-reference-oidc.md) em vez do OAuth 2.0.

O Azure AD B2C estende os fluxos padrão do OAuth 2.0 para fazer mais do que simples ações de autenticação e autorização. Ele apresenta o [parâmetro de política](active-directory-b2c-reference-policies.md). Com políticas internas, você pode usar o OAuth 2.0 para adicionar experiências do usuário ao seu aplicativo, como inscrição, entrada e gerenciamento de perfil. Neste artigo, mostramos como usar políticas e o OAuth 2.0 para implementar cada uma dessas experiências em seus aplicativos nativos. Também mostramos como obter tokens de acesso para acessar APIs Web.

Nas solicitações HTTP de exemplo neste artigo, usamos o diretório do Azure AD B2C de exemplo, **fabrikamb2c.onmicrosoft.com**. Também usamos nosso aplicativo de exemplo e políticas. Você pode tentar as solicitações sozinho usando esses valores ou substituindo-os pelos seus próprios valores.
Saiba como [obter seus próprios diretório, aplicativo e políticas do Azure AD B2C](#use-your-own-azure-ad-b2c-directory).

## <a name="1-get-an-authorization-code"></a>1. Obter um código de autorização
O fluxo do código de autorização começa com o cliente direcionando o usuário para o ponto de extremidade `/authorize` . Essa é a parte interativa do fluxo, na qual o usuário entra em ação. Nessa solicitação, o cliente indica no parâmetro `scope` as permissões que ele precisa adquirir do usuário. No parâmetro `p`, ele indica que a política a ser executada. Cada um dos três exemplos a seguir (com quebras de linha para facilitar a leitura) usam uma política diferente.

### <a name="use-a-sign-in-policy"></a>Usar uma política de entrada
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_in
```

### <a name="use-a-sign-up-policy"></a>Usar uma política de inscrição
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_up
```

### <a name="use-an-edit-profile-policy"></a>Usar uma política de edição de perfil
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_edit_profile
```

| Parâmetro | Obrigatório? | Descrição |
| --- | --- | --- |
| client_id |Obrigatório |A ID do aplicativo atribuída ao seu aplicativo no [portal do Azure](https://portal.azure.com). |
| response_type |Obrigatório |O tipo de resposta, que deve incluir `code` para o fluxo do código de autorização. |
| redirect_uri |Obrigatório |O URI de redirecionamento do seu aplicativo, no qual as respostas de autenticação são enviadas e recebidas pelo seu aplicativo. Ele deve corresponder exatamente a um dos URIs de redirecionamento registrados no portal, exceto que ele deve ser codificado como URL. |
| scope |Obrigatório |Uma lista de escopos separados por espaços. Um valor de escopo único indica ao Azure AD (Azure Active Directory) que ambas as permissões estão sendo solicitadas. O uso da ID do cliente como o escopo indica que seu aplicativo precisa de um token de acesso que possa ser usado em relação a seu próprio serviço ou API Web, representado pela mesma ID de cliente.  O escopo `offline_access` indica que seu aplicativo precisa de um token de atualização para acesso de longa duração a recursos. Você também pode usar o escopo `openid` para solicitar um token de ID do Azure AD B2C. |
| response_mode |Recomendadas |O método que você usa para enviar o código de autorização resultante para seu aplicativo. Pode ser `query`, `form_post` ou `fragment`. |
| state |Recomendadas |Um valor incluído na solicitação que retorna na resposta do token. Pode ser uma cadeia de caracteres de qualquer conteúdo que você deseje usar. Geralmente, um valor exclusivo gerado aleatoriamente é usado para evitar ataques de solicitação intersite forjada. O estado também é usado para codificar informações sobre o estado do usuário no aplicativo, antes que a solicitação de autenticação tenha ocorrido. Por exemplo, a página em que o usuário estava em ou a política que estava sendo executada. |
| p |Obrigatório |A política que é executada. É o nome de uma política que é criada no diretório do Azure AD B2C. O valor de nome da política deve começar com **b2c\_1\_**. Para saber mais sobre políticas, consulte [Políticas internas do Azure AD B2C](active-directory-b2c-reference-policies.md). |
| prompt |Opcional |O tipo de interação do usuário que é necessário. Atualmente, o único valor válido é `login`, que força o usuário a inserir suas credenciais nessa solicitação. O logon único não terá efeito. |

Nesse momento, é solicitado que o usuário conclua o fluxo de trabalho da política. Isso pode exigir que o usuário insira seu nome de usuário e senha, entre com uma identidade social, inscreva-se no diretório ou realize outras etapas. As ações do usuário dependem de como a política é definida.

Depois que o usuário completar a política, o Azure AD retornará uma resposta ao seu aplicativo no valor usado para `redirect_uri`. Ele usa o método especificado no parâmetro `response_mode`. A resposta é exatamente a mesma para cada um dos cenários de ação do usuário, independentemente de qual política foi executada.

Uma resposta bem-sucedida que usa `response_mode=query` tem esta aparência:

```
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...        // the authorization_code, truncated
&state=arbitrary_data_you_can_receive_in_the_response                // the value provided in the request
```

| Parâmetro | Descrição |
| --- | --- |
| código |O código de autorização que o aplicativo solicitou. O aplicativo pode usar o código de autorização para solicitar um token de acesso para um recurso de destino. Os códigos de autorização têm uma duração muito curta. Normalmente, eles expiram depois de cerca de 10 minutos. |
| state |Consulte a descrição completa na tabela na seção anterior. Se um parâmetro `state` estiver incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores `state` na solicitação e na resposta são idênticos. |

As respostas de erro também podem ser enviadas ao URI de redirecionamento, de modo que o aplicativo possa tratá-las adequadamente:

```
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=The+user+has+cancelled+entering+self-asserted+information
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parâmetro | Descrição |
| --- | --- |
| error |Uma cadeia de caracteres de código de erro que você pode usar para classificar os tipos de erros que ocorrem. Você também pode usar a cadeia de caracteres para reagir a erros. |
| error_description |Uma mensagem de erro específica que pode ajudar você a identificar a causa raiz de um erro de autenticação. |
| state |Consulte a descrição completa na tabela anterior. Se um parâmetro `state` estiver incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores `state` na solicitação e na resposta são idênticos. |

## <a name="2-get-a-token"></a>2. Obter um token
Agora que você adquiriu um código de autorização, será possível resgatar o `code` de um token para o recurso desejado enviando uma solicitação POST para o ponto de extremidade `/token`. No Azure AD B2C, o único recurso para o qual você pode solicitar um token é a própria API Web de back-end do seu aplicativo. A convenção usada para solicitar um token para si mesmo é usar a ID do cliente do aplicativo como o escopo:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob

```

| Parâmetro | Obrigatório? | Descrição |
| --- | --- | --- |
| p |Obrigatório |A política que foi usada para adquirir o código de autorização. Você não poderá usar uma política diferente nessa solicitação. Observe que esse parâmetro é adicionado à *cadeia de caracteres de consulta*e não ao corpo do POST. |
| client_id |Obrigatório |A ID do aplicativo atribuída ao seu aplicativo no [portal do Azure](https://portal.azure.com). |
| grant_type |Obrigatório |O tipo de concessão. Para o fluxo de código de autorização, o tipo de concessão deve ser `authorization_code`. |
| scope |Recomendadas |Uma lista de escopos separados por espaços. Um valor de escopo único indica ao Azure AD que ambas as permissões estão sendo solicitadas. O uso da ID do cliente como o escopo indica que seu aplicativo precisa de um token de acesso que possa ser usado em relação a seu próprio serviço ou API Web, representado pela mesma ID de cliente.  O escopo `offline_access` indica que seu aplicativo precisa de um token de atualização para acesso de longa duração a recursos.  Você também pode usar o escopo `openid` para solicitar um token de ID do Azure AD B2C. |
| código |Obrigatório |O código de autorização que você adquiriu no primeiro segmento do fluxo. |
| redirect_uri |Obrigatório |O URI de redirecionamento do aplicativo em que você recebeu o código de autorização. |

Uma resposta de token bem-sucedido tem esta aparência:

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
| token_type |O valor do tipo de token. O único tipo com suporte do Azure AD é Portador |
| access_token |O JWT (Token Web JSON) assinado que você solicitou. |
| scope |Os escopos para os quais o token é válido. Você também pode usar os escopos para armazenar tokens em cache para uso posterior. |
| expires_in |O período de tempo pelo qual o token é válido (em segundos). |
| refresh_token |Um token de atualização do OAuth 2.0. O aplicativo pode usar esse token para adquirir tokens de acesso adicionais depois que o atual expirar. Os tokens de atualização têm uma vida longa. Você pode usá-los para manter o acesso aos recursos por longos períodos de tempo. Para obter mais informações, consulte a [Referência de token do Azure AD B2C](active-directory-b2c-reference-tokens.md). |

As respostas de erro tem esta aparência:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parâmetro | Descrição |
| --- | --- |
| error |Uma cadeia de caracteres de código de erro que você pode usar para classificar os tipos de erros que ocorrem. Você também pode usar a cadeia de caracteres para reagir a erros. |
| error_description |Uma mensagem de erro específica que pode ajudar você a identificar a causa raiz de um erro de autenticação. |

## <a name="3-use-the-token"></a>3. Usar o token
Agora que adquiriu um token de acesso com êxito, você poderá usá-lo em solicitações às suas APIs Web de back-end incluindo-o no cabeçalho `Authorization`:

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="4-refresh-the-token"></a>4. Atualizar o token
Os tokens de acesso e os tokens de ID tem curta duração. Depois que eles expirarem, você deverá atualizá-los para continuar a acessar recursos. Para fazer isso, envie outra solicitação POST para o ponto de extremidade `/token`. Dessa vez, forneça o `refresh_token` em vez do `code`:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parâmetro | Obrigatório? | Descrição |
| --- | --- | --- |
| p |Obrigatório |A política que foi usada para adquirir o token de atualização original. Você não poderá usar uma política diferente nessa solicitação. Observe que esse parâmetro é adicionado à *cadeia de caracteres de consulta*e não ao corpo do POST. |
| client_id |Recomendadas |A ID do aplicativo atribuída ao seu aplicativo no [portal do Azure](https://portal.azure.com). |
| grant_type |Obrigatório |O tipo de concessão. Para este segmento do fluxo de código de autorização, o tipo de concessão deve ser `refresh_token`. |
| scope |Recomendadas |Uma lista de escopos separados por espaços. Um valor de escopo único indica ao Azure AD que ambas as permissões estão sendo solicitadas. O uso da ID do cliente como o escopo indica que seu aplicativo precisa de um token de acesso que possa ser usado em relação a seu próprio serviço ou API Web, representado pela mesma ID de cliente.  O escopo `offline_access` indica que o aplicativo precisará de um token de atualização para acessar os recursos de longa duração.  Você também pode usar o escopo `openid` para solicitar um token de ID do Azure AD B2C. |
| redirect_uri |Opcional |O URI de redirecionamento do aplicativo em que você recebeu o código de autorização. |
| refresh_token |Obrigatório |O token de atualização original que você adquiriu na segunda ramificação do fluxo. |

Uma resposta de token bem-sucedido tem esta aparência:

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
| token_type |O valor do tipo de token. O único tipo com suporte do Azure AD é Portador |
| access_token |O JWT assinado que você solicitou. |
| scope |Os escopos para os quais o token é válido. Você também pode usar os escopos para armazenar tokens em cache para uso posterior. |
| expires_in |O período de tempo pelo qual o token é válido (em segundos). |
| refresh_token |Um token de atualização do OAuth 2.0. O aplicativo pode usar esse token para adquirir tokens de acesso adicionais depois que o atual expirar. Os tokens de atualização têm longa duração e podem ser usados para reter acesso a recursos por períodos estendidos. Para obter mais informações, consulte a [Referência de token do Azure AD B2C](active-directory-b2c-reference-tokens.md). |

As respostas de erro tem esta aparência:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parâmetro | Descrição |
| --- | --- |
| error |Uma cadeia de caracteres de código de erro que você pode usar para classificar os tipos de erros que ocorrem. Você também pode usar a cadeia de caracteres para reagir a erros. |
| error_description |Uma mensagem de erro específica que pode ajudar você a identificar a causa raiz de um erro de autenticação. |

## <a name="use-your-own-azure-ad-b2c-directory"></a>Use seu próprio diretório do Azure AD B2C
Para testar essas solicitações, conclua as etapas a seguir. Substitua os valores de exemplo que usamos neste artigo pelos seus próprios valores.

1. [Criar um diretório do Azure AD B2C](active-directory-b2c-get-started.md). Use o nome do seu diretório nas solicitações.
2. [Criar um aplicativo](active-directory-b2c-app-registration.md) para obter uma ID de aplicativo e um URI de redirecionamento. Inclua um cliente nativo em seu aplicativo.
3. [Criar suas regras](active-directory-b2c-reference-policies.md) para obter os nomes de política.

