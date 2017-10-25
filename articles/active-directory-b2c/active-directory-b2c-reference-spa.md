---
title: "Azure Active Directory B2C: Aplicativos de página única utilizando fluxo implícito | Microsoft Docs"
description: "Saiba como criar aplicativos de página única diretamente utilizando fluxo implícito do OAuth 2.0 com o Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: a45cc74c-a37e-453f-b08b-af75855e0792
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: parakhj
ms.openlocfilehash: 44ff168599e9078506e1afdd0f1dc4657ef0964d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-b2c-single-page-app-sign-in-by-using-oauth-20-implicit-flow"></a>Azure AD B2C: Aplicativo de página única utilizando fluxo implícito do OAuth 2.0

> [!NOTE]
> Essa funcionalidade está em visualização.
> 

Muitos aplicativos modernos têm um aplicativo de página única front-end que é escrito principalmente em JavaScript. Frequentemente, o aplicativo é escrito utilizando uma estrutura como AngularJS, Ember.js ou Durandal. Os aplicativos de página única e outros aplicativos JavaScript que são executados principalmente em um navegador possuem alguns desafios adicionais para autenticação:

* As características de segurança desses aplicativos são consideravelmente diferentes dos aplicativos Web tradicionais baseados em servidor.
* Muitos servidores de autorização e provedores de identidade não dão suporte para solicitações CORS (compartilhamento de recursos entre origens).
* Navegador de página inteira redirecionando para longe do aplicativo pode ser significativamente invasivo para a experiência do usuário.

Para oferecer suporte a esses aplicativos, o Azure AD B2C (Azure Active Directory B2C) utiliza o fluxo implícito do OAuth 2.0. O fluxo de concessão implícita de autorização do OAuth 2.0 é descrito na [seção 4.2 da especificação do OAuth 2.0](http://tools.ietf.org/html/rfc6749). No fluxo implícito, o aplicativo recebe tokens diretamente do ponto de extremidade autorizado do Azure AD (Azure Active Directory) sem qualquer troca de servidor para servidor. Toda lógica de autenticação e gerenciamento de sessão ocorre inteiramente no cliente JavaScript, sem redirecionamentos de página adicionais.

O Azure AD B2C estende o fluxo implícito do OAuth 2.0 padrão para mais que autenticação e autorização simples. O Azure AD B2C introduz o [parâmetro de política](active-directory-b2c-reference-policies.md). Com o parâmetro de política, é possível utiliza o OAuth 2.0 para adicionar experiências do usuário ao seu aplicativo, como inscrição, conexão e gerenciamento de perfil. Neste artigo, mostraremos como utilizar o fluxo implícito e o Azure AD para implementar cada uma dessas experiências em seus aplicativos de uma página única. Para ajudá-lo a começar, examine os nossos exemplos[Node.js](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-nodejs-webapi) e [Microsoft .NET](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-dotnet-webapi).

No exemplo de solicitações HTTP neste artigo, utilizamos nosso diretório Azure AD B2C, **fabrikamb2c.onmicrosoft.com**. Além disso, utilizamos nosso próprio aplicativo de exemplo e políticas. É possível tentar as solicitações utilizando esses valores ou, substituí-los com seus próprios valores.
Saiba como [obter seu próprio diretório Azure AD B2C, aplicativo e políticas](#use-your-own-b2c-tenant).


## <a name="protocol-diagram"></a>Diagrama de protocolo

O fluxo de entrada implícito parece ser semelhante à seguinte figura. Cada etapa é descrita detalhadamente mais adiante no artigo.

![Raias do OpenID Connect](../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

## <a name="send-authentication-requests"></a>Enviar solicitações de autenticação
Quando o aplicativo Web precisa autenticar o usuário e executar uma política, ele direciona o usuário para o ponto de extremidade `/authorize`. Essa é a parte interativa do fluxo, onde o usuário toma ação, dependendo da política. O usuário obtém um token de identificação do ponto de extremidade do Azure AD.

Nessa solicitação, o cliente indica no parâmetro `scope` as permissões que ele precisa adquirir do usuário. No parâmetro `p`, ele indica que a política a ser executada. Os três exemplos a seguir (com quebras de linha para legibilidade) utilizam uma política diferente. Para ter uma ideia de como funciona cada solicitação, tente colar a solicitação em um navegador e executá-lo.

### <a name="use-a-sign-in-policy"></a>Usar uma política de entrada
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_in
```

### <a name="use-a-sign-up-policy"></a>Usar uma política de inscrição
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_up
```

### <a name="use-an-edit-profile-policy"></a>Usar uma política de edição de perfil
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_edit_profile
```

| Parâmetro | Obrigatório? | Descrição |
| --- | --- | --- |
| client_id |Obrigatório |A ID do aplicativo atribuída ao seu aplicativo no [portal do Azure](https://portal.azure.com). |
| response_type |Obrigatório |Deve incluir `id_token` para conexão do OpenID Connect. É possível também incluir o tipo de resposta `token`. Se utilizar `token`, seu aplicativo poderá receber imediatamente um token de acesso do ponto de extremidade autorizado, sem fazer uma segunda solicitação para o ponto de extremidade autorizado.  Se utilizar o tipo de resposta `token`, o `scope` parâmetro deverá conter um escopo indicando para quais recursos o token será emitido. |
| redirect_uri |Recomendadas |O URI de redirecionamento do seu aplicativo, onde as respostas de autenticação podem ser enviadas e recebidas pelo aplicativo. Ele deve corresponder exatamente a um dos URIs que você registrou no portal, com exceção de que ele deve ser codificado por URL. |
| response_mode |Recomendadas |Especifica o método que deve ser usado para enviar o token resultante de volta ao aplicativo.  Para fluxos implícitos, utilize `fragment`. |
| scope |Obrigatório |Uma lista de escopos separados por espaços. Um valor de escopo único indica ao Azure AD que ambas as permissões estão sendo solicitadas. O escopo `openid` indica uma permissão para entrar no usuário e obter dados sobre ele na forma de tokens de ID. (Isso será abordado mais adiantes no artigo.) O escopo `offline_access` é opcional para aplicativos Web. Isso indica que seu aplicativo precisa de um token de atualização para acesso de longa vida para recursos. |
| state |Recomendadas |Um valor incluído na solicitação que também é retornado na resposta de token. Pode ser uma cadeia de caracteres de qualquer conteúdo que você deseja utilizar. Geralmente, um valor exclusivo gerado aleatoriamente é utilizado para evitar ataques de solicitação intersite forjada. O estado também é utilizado para codificar informações sobre o estado do usuário no aplicativo antes da solicitação de autenticação ocorrida, como a página em que estava. |
| nonce |Obrigatório |Um valor incluído na solicitação, gerado pelo aplicativo, incluído no token de ID resultante como uma declaração. O aplicativo pode verificar esse valor para reduzir os ataques de reprodução de token. Normalmente, o valor é uma cadeia de caracteres aleatória e exclusiva que pode ser usada para identificar a origem da solicitação. |
| p |Obrigatório |A política para executar. É o nome de uma política criada no seu locatário do Azure AD B2C. O valor do nome da política deve começar com **b2c\_1\_**. Para obter mais informações, consulte [Políticas internas do Azure AD B2C](active-directory-b2c-reference-policies.md). |
| prompt |Opcional |O tipo de interação do usuário que é necessária. Atualmente, o único valor válido é `login`. Isso força o usuário a inserir suas credenciais nessa solicitação. O logon único não terá efeito. |

Nesse momento, é solicitado que o usuário conclua o fluxo de trabalho da política. Isso pode exigir que o usuário insira seu nome de usuário e senha, entre com uma identidade social, inscreva-se no diretório ou realize outras etapas. As ações do usuário dependem de como a política é definida.

Depois que o usuário completar a política, o Azure AD retornará uma resposta ao seu aplicativo no valor usado para `redirect_uri`. Ele usa o método especificado no parâmetro `response_mode`. A resposta é exatamente a mesma para cada um dos cenários de ação do usuário, independentemente de qual política foi executada.

### <a name="successful-response"></a>Resposta bem-sucedida
Uma resposta bem sucedida que utiliza `response_mode=fragment` e `response_type=id_token+token` é semelhante à seguinte, com quebras de linha para legibilidade:

```
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
```

| Parâmetro | Descrição |
| --- | --- |
| access_token |O token de acesso que o aplicativo solicitou.  O token de acesso não deve ser decodificado nem inspecionado de outra forma. É possível tratá-lo como uma cadeia de caracteres opaca. |
| token_type |O valor do tipo de token. O único tipo com suporte do Azure AD é Portador |
| expires_in |O período de tempo que o token de acesso é válido (em segundos). |
| scope |Os escopos para os quais o token é válido. Além disso, também é possível utilizar escopos para tokens de cache para uso posterior. |
| id_token |O token de ID que o aplicativo solicitou. Você pode usar o token de ID para verificar a identidade do usuário e iniciar uma sessão com o usuário. Para obter mais informações sobre tokens de identificação e seus conteúdos, consulte a [referência de token do Azure AD B2C](active-directory-b2c-reference-tokens.md). |
| state |Se um parâmetro `state` estiver incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores `state` na solicitação e resposta são idênticos. |

### <a name="error-response"></a>Resposta de erro
As respostas de erro também podem ser enviadas para URI de redirecionamento, de modo que o aplicativo possa tratá-los adequadamente:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parâmetro | Descrição |
| --- | --- |
| error |Uma cadeia de caracteres de código de erro usada para classificar os tipos de erros que ocorrem. O código de erro também pode ser utilizado para tratamento de erro. |
| error_description |Uma mensagem de erro específica que pode ajudar você a identificar a causa raiz de um erro de autenticação. |
| state |Consulte a descrição completa na tabela anterior. Se um parâmetro `state` estiver incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores `state` na solicitação e resposta são idênticos.|

## <a name="validate-the-id-token"></a>Validar o token de ID
Receber um token de ID não é suficiente para autenticar o usuário. Você deverá validar a assinatura do token de ID e verificar as reivindicações no token pelos requisitos de seu aplicativo. O Azure AD B2C usa [JWTs (Tokens Web JSON)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) e criptografia de chave pública para assinar tokens e verificar se eles são válidos.

Muitas bibliotecas de software livre estão disponíveis para validar JWTs, dependendo do idioma de sua preferência. Considere explorar bibliotecas de software livre disponíveis em vez de implementar sua própria lógica de validação. As informações contidas neste artigo podem ser utilizadas para ajudá-lo a aprender como utilizar essas bibliotecas corretamente.

O Azure AD B2C tem um ponto de extremidade de metadados OpenID Connect. Um aplicativo pode usar o ponto de extremidade para buscar informações sobre o Azure AD B2C em tempo de execução. Essas informações incluem pontos de extremidade, conteúdos de token e chaves de assinatura de token. Há um documento de metadados JSON para cada política no locatário do Azure AD B2C. Por exemplo, o documento de metadados para a política b2c_1_sign_in no locatário fabrikamb2c.onmicrosoft.com está localizado em:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

Uma das propriedades deste documento de configuração é `jwks_uri`. O valor para a mesma política seria:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`

Para determinar qual política foi utilizada para assinar um token de ID (e onde buscar os metadados), há duas opções. Primeiro, o nome da política está incluído na declaração `acr` em `id_token`. Para obter informações sobre como analisar as declarações de um token de ID, consulte a [referência de token do Azure AD B2C](active-directory-b2c-reference-tokens.md). Sua outra opção é codificar a política no valor do parâmetro `state` quando emitir a solicitação. Em seguida, decodifique o parâmetro `state` para determinar qual política foi utilizada. Ambos os métodos são válidos.

Após ter adquirido o documento de metadados a partir do ponto de extremidade de metadados OpenID Connect, você poderá utilizar as chaves públicas RSA-256 (localizadas nesse ponto de extremidade) para validar a assinatura do token de ID. Poderá haver várias chaves listadas nesse ponto de extremidade em qualquer momento, cada uma identificada por um `kid`. O cabeçalho de `id_token` também contém uma declaração `kid`. Ele indica qual dessas chaves foi utilizada para assinar o token de ID. Para obter mais informações, incluindo aprender sobre [tokens de validação](active-directory-b2c-reference-tokens.md#token-validation), consulte a [referência de token do Azure AD B2C](active-directory-b2c-reference-tokens.md).
<!--TODO: Improve the information on this-->

Após validar a assinatura do token de ID, várias declarações exigirão verificação. Por exemplo:

* Valide a declaração `nonce` para evitar ataques de reprodução de token. Seu valor deve ser o que você especificou na solicitação de conexão.
* Valide `aud` para garantir que o token de ID foi emitido para seu aplicativo. Seu valor deve ser a ID do aplicativo do seu aplicativo.
* Valide as declarações `iat` e `exp` para garantir que o token de ID não expirou.

Várias outras validações que deverão ser executadas estão descritas detalhadamente em [Especificação de OpenID Connect Core](http://openid.net/specs/openid-connect-core-1_0.html). Talvez você também queira validar declarações adicionais, dependendo do cenário. Algumas validações comuns incluem:

* Garanta que o usuário ou a organização tenha se inscrito no aplicativo.
* Garanta que o usuário tenha autorização e privilégios adequados.
* Garanta que uma determina força de autenticação tenha ocorrido como, por exemplo, utilizando a autenticação multifator do Azure.

Para obter mais informações sobre as reivindicações em um token de ID, consulte a [referência de token do Azure AD B2C](active-directory-b2c-reference-tokens.md).

Após validar completamente o token de ID, você poderá iniciar uma sessão com o usuário. No seu aplicativo, use as declarações no token de ID para obter informações sobre o usuário. Essas informações podem ser usadas para exibição, registros, autorizações e outros.

## <a name="get-access-tokens"></a>Obter tokens de acesso
Se os seus aplicativos Web precisam somente executar políticas, você poderá ignorar as próximas seções. As informações contidas nas seções a seguir são aplicáveis apenas para aplicativos Web que precisam fazer chamadas autenticadas para uma API Web e que são protegidas pelo Azure AD B2C.

Agora que você autenticou o usuário no seu aplicativo de página única, você pode obter tokens de acesso para chamar APIs Web que são protegidas pelo Azure AD. Mesmo que já tenha recebido um token utilizando o tipo de resposta `token`, você poderá utilizar esse método para adquirir tokens para recursos adicionais sem redirecionar o usuário para conectar novamente.

Em um fluxo típico de aplicativo Web, você faria isso fazendo uma solicitação para o ponto de extremidade `/token`.  No entanto, o ponto de extremidade não dá suporte para solicitações CORS, portanto, fazer chamadas AJAX para obter e atualizar tokens não é uma opção. Em vez disso, você poderá utiliza o fluxo implícito em um elemento iframe HTML oculto para obter novos tokens para outras APIs Web. A seguir está um exemplo, com quebras de linha para legibilidade:

```

https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
&response_mode=fragment
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&prompt=none
&domain_hint=organizations
&login_hint=myuser@mycompany.com
&p=b2c_1_sign_in
```

| Parâmetro | Obrigatório? | Descrição |
| --- | --- | --- |
| client_id |Obrigatório |A ID do aplicativo atribuída ao seu aplicativo no [portal do Azure](https://portal.azure.com). |
| response_type |Obrigatório |Deve incluir `id_token` para conexão do OpenID Connect.  Também é possível incluir o tipo de resposta `token`. Se utilizar `token` aqui, seu aplicativo poderá receber imediatamente um token de acesso do ponto de extremidade autorizado, sem fazer uma segunda solicitação para o ponto de extremidade autorizado. Se utilizar o tipo de resposta `token`, o `scope` parâmetro deverá conter um escopo indicando para quais recursos o token será emitido. |
| redirect_uri |Recomendadas |O URI de redirecionamento do seu aplicativo, onde as respostas de autenticação podem ser enviadas e recebidas pelo aplicativo. Ele deve coincidir exatamente com um dos URIs de redirecionamento registrados no portal, exceto que deve ser codificado em URL. |
| scope |Obrigatório |Uma lista de escopos separados por espaços.  Para obter tokens, inclua todos os escopos que necessários para o recurso pretendido. |
| response_mode |Recomendadas |Especifica o método que deve ser usado para enviar o token resultante de volta ao aplicativo.  Pode ser `query`, `form_post` ou `fragment`. |
| state |Recomendadas |Um valor incluído na solicitação que retorna na resposta do token.  Pode ser uma cadeia de caracteres de qualquer conteúdo que você deseja utilizar.  Geralmente, um valor exclusivo gerado aleatoriamente é utilizado para evitar ataques de solicitação intersite forjada.  O estado também é utilizado para codificar informações sobre o estado do usuário no aplicativo, antes que a solicitação de autenticação tenha ocorrido. Por exemplo, a página ou a exibição do usuário estava ativada. |
| nonce |Obrigatório |Um valor incluído na solicitação, gerado pelo aplicativo, que está incluído no token de ID resultante como uma reivindicação.  O aplicativo pode verificar esse valor para reduzir os ataques de reprodução de token. Normalmente, o valor é uma cadeia de caracteres aleatória e exclusiva que identifica a origem da solicitação. |
| prompt |Obrigatório |Para atualizar e obter tokens em um iframe oculto, utilize `prompt=none` para garantir que o iframe não fique preso na página de entrada e retorna imediatamente. |
| login_hint |Obrigatório |Para atualizar e obter tokens em um iframe oculto, inclua o nome de usuário de usuário nesta dica para distinguir entre várias sessões que o usuário pode ter em um determinado momento. Você pode extrair o nome de usuário de um início de sessão anterior usando a declaração `preferred_username`. |
| domain_hint |Obrigatório |Pode ser `consumers` ou `organizations`.  Para atualizar e obter tokens em um iframe oculto, é necessário incluir o valor `domain_hint` na solicitação.  Extraia a declaração `tid` do token de ID de uma entrada anterior para determinar qual valor usar.  Se o valor da declaração `tid` for `9188040d-6c67-4c5b-b112-36a304b66dad`,  utilize `domain_hint=consumers`.  Caso contrário, use `domain_hint=organizations`. |

Ao configurar o parâmetro `prompt=none`, essa solicitação terá êxito ou falhará imediatamente e retornará ao seu aplicativo.  Uma resposta bem-sucedida será enviada para seu aplicativo no URI de redirecionamento indicado, utilizando o método especificado no parâmetro `response_mode`.

### <a name="successful-response"></a>Resposta bem-sucedida
Uma resposta bem-sucedida utilizando `response_mode=fragment` é semelhante à seguinte:

```
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
```

| Parâmetro | Descrição |
| --- | --- |
| access_token |O token solicitado pelo aplicativo. |
| token_type |O tipo de token sempre será Portador. |
| state |Se um parâmetro `state` estiver incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores `state` na solicitação e resposta são idênticos. |
| expires_in |Por quanto tempo o token de acesso é válido (em segundos). |
| scope |Os escopos para os quais o access_token é válido. |

### <a name="error-response"></a>Resposta de erro
As respostas de erro também podem ser enviadas para URI de redirecionamento, de modo que o aplicativo possa tratá-los adequadamente.  Para `prompt=none`, um erro esperado é semelhante ao seguinte:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parâmetro | Descrição |
| --- | --- |
| error |Uma cadeia de caracteres de código de erro que pode ser utilizada para classificar os tipos de erros que ocorrem. A cadeia de caracteres também pode ser utilizada para reagir a erros. |
| error_description |Uma mensagem de erro específica que pode ajudar você a identificar a causa raiz de um erro de autenticação. |

Se você receber esse erro na solicitação do iframe, o usuário deverá entrar novamente de forma interativa para recuperar um novo token. Você pode fazer isso de maneira que faça sentido para seu aplicativo.

## <a name="refresh-tokens"></a>Tokens de atualização
Os tokens de ID e tokens de acesso expiram após um curto período de tempo. Seu aplicativo deverá estar preparado para atualizar esses tokens periodicamente.  Para atualizar qualquer tipo de token, execute a mesma solicitação de iframe oculto utilizada no exemplo anterior, utilizando o parâmetro `prompt=none` para controlar as etapas do AD Azure.  Para receber um novo valor `id_token`, certifique-se de usar `response_type=id_token` e `scope=openid` e um parâmetro `nonce`.

## <a name="send-a-sign-out-request"></a>Enviar uma solicitação de saída
Quando você quiser desconectar o usuário do aplicativo, redirecione o usuário ao Azure AD para sair. Se não fizer isso, o usuário poderá reautenticar no seu aplicativo sem entrar novamente nas credenciais. Isso ocorre porque eles terão uma sessão de logon único válida com o Azure AD.

Você pode simplesmente redirecionar o usuário para `end_session_endpoint` que está listado no mesmo documento de metadados do OpenID Connect descrito em [Validar o ID de token](#validate-the-id-token). Por exemplo:

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Parâmetro | Obrigatório? | Descrição |
| --- | --- | --- |
| p |Obrigatório |A política usada para desconectar o usuário de seu aplicativo. |
| post_logout_redirect_uri |Recomendadas |A URL para a qual o usuário deve ser redirecionado após a saída com êxito. Se não estiver incluído, o Azure AD B2C exibirá uma mensagem genérica para o usuário. |

> [!NOTE]
> Direcionar o usuário para `end_session_endpoint` limpa alguns dos estados de logon único do usuário com o Azure AD B2C. No entanto, ele não desconecta o usuário da sessão do provedor de identidade social do usuário. Se o usuário selecionar o mesmo provedor de identificação durante um conexão subsequente, o usuário será reautenticado, sem entrar em suas credenciais. Se um usuário quiser sair do serviço de seu aplicativo do Azure AD B2C, isso não significa necessariamente que ele deseja se desconectar completamente de sua conta do Facebook, por exemplo. No entanto, para contas locais, a sessão do usuário será encerrada corretamente.
> 
> 

## <a name="use-your-own-azure-ad-b2c-tenant"></a>Utilize seu próprio locatário do Azure AD B2C
Para tentar essas solicitações, complete as três etapas a seguir. Substitua os valores de exemplo que utilizamos neste artigo com seus próprios valores:

1. [Criar um locatário do Azure AD B2C](active-directory-b2c-get-started.md). Utilize o nome do seu locatário nas solicitações.
2. [Criar um aplicativo](active-directory-b2c-app-registration.md) para obter um ID de aplicativo e um valor `redirect_uri`. Inclua um aplicativo Web ou uma API Web em seu aplicativo. Opcionalmente, é possível criar um segredo de aplicativo.
3. [Criar suas regras](active-directory-b2c-reference-policies.md) para obter os nomes de política.

## <a name="samples"></a>Exemplos

* [Criar um aplicativo de página única usando Node.js](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-nodejs-webapi)
* [Criar um aplicativo de página única usando .NET](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-dotnet-webapi)

