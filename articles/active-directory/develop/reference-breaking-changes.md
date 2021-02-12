---
title: Referência de alterações recentes de Azure Active Directory
description: Saiba mais sobre as alterações realizadas nos protocolos do Azure AD que podem afetar o aplicativo.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: reference
ms.date: 5/4/2020
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 94c34e6f7cb24ff749e5de95f1c28a496700af80
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348714"
---
# <a name="whats-new-for-authentication"></a>Quais são as novidades para autenticação?

> Seja notificado sobre atualizações desta página colando esta URL em seu leitor de RSS feed:<br/>`https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20for%20authentication%22&locale=en-us`

O sistema de autenticação altera e adiciona recursos em uma base contínua para melhorar a segurança e a conformidade com os padrões. Para manter-se atualizado com os desenvolvimentos mais recentes, este artigo fornece informações sobre os seguintes detalhes:

- Os recursos mais recentes
- Problemas conhecidos
- Alterações do protocolo
- Funcionalidades preteridas

> [!TIP]
> Esta página é atualizada regularmente, então, visite-a com frequência. Salvo indicação contrária, essas alterações só serão aplicadas para aplicativos registrados recentemente.

## <a name="upcoming-changes"></a>Alterações futuras

Nenhum agendado neste momento.  Veja abaixo as alterações que estão no ou que estão chegando à produção.

## <a name="may-2020"></a>Maio de 2020

### <a name="azure-government-endpoints-are-changing"></a>Os pontos de extremidade do Azure governamental estão mudando

**Data de efetivação**: maio de 5 (concluindo em junho de 2020) 

**Pontos de extremidade afetados**: todos

**Protocolo afetado**: todos os fluxos

Em 1 de junho de 2018, a autoridade de Azure Active Directory oficial (AAD) para o Azure governamental mudou de `https://login-us.microsoftonline.com` para `https://login.microsoftonline.us` . Essa alteração também é aplicada a Microsoft 365 GCC High e DoD, que o AAD do Azure governamental também Services. Se você possui um aplicativo dentro de um locatário do governo dos EUA, você deve atualizar seu aplicativo para conectar usuários no `.us` ponto de extremidade.  

A partir de 1º de maio, o Azure AD começará a impor a alteração do ponto de extremidade, bloqueando os usuários do governo de entrar em aplicativos hospedados em locatários do governo dos EUA usando o ponto de extremidade público ( `microsoftonline.com` ).  Os aplicativos impactados começarão a ver um erro `AADSTS900439`  -  `USGClientNotSupportedOnPublicEndpoint` . Esse erro indica que o aplicativo está tentando entrar em um usuário do governo dos EUA no ponto de extremidade de nuvem pública. Se seu aplicativo estiver em um locatário de nuvem pública e pretende oferecer suporte a usuários do governo dos EUA, você precisará [atualizar seu aplicativo para dar suporte a eles explicitamente](./authentication-national-cloud.md). Isso pode exigir a criação de um novo registro de aplicativo na nuvem do governo dos EUA. 

A imposição dessa alteração será feita usando uma distribuição gradual com base na frequência com que os usuários da nuvem do governo dos EUA entram na assinatura dos aplicativos de aplicativo em usuários do governo dos EUA que raramente verão a aplicação primeiro, e os aplicativos frequentemente usados pelos usuários do governo dos EUA terão a aplicação aplicada. Esperamos que a imposição seja concluída em todos os aplicativos em junho de 2020. 

Para obter mais detalhes, consulte a [postagem no blog do Azure governamental nesta migração](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/). 

## <a name="march-2020"></a>Março de 2020

### <a name="user-passwords-will-be-restricted-to-256-characters"></a>As senhas de usuário serão restritas a 256 caracteres.

**Data de efetivação**: 13 de março de 2020

**Pontos de extremidade afetados**: todos

**Protocolo afetado**: todos os fluxos de usuário.

Os usuários com senhas com mais de 256 caracteres que entram diretamente no Azure AD (em oposição a um IDP federado como ADFS) não poderão entrar a partir de 13 de março de 2020 e serão solicitados a redefinir sua senha em vez disso.  Os administradores podem receber solicitações para ajudar a redefinir a senha dos usuários.

O erro nos logs de entrada será AADSTS 50052: InvalidPasswordExceedsMaxLength

Mensagem: `The password entered exceeds the maximum length of 256. Please reach out to your admin to reset the password.`

Correção:

O usuário não pode fazer logon porque sua senha excede o comprimento máximo permitido. Eles devem contatar seu administrador para redefinir a senha. Se o SSPR estiver habilitado para seu locatário, ele poderá redefinir sua senha seguindo o link "esqueceu sua senha".



## <a name="february-2020"></a>Fevereiro de 2020

### <a name="empty-fragments-will-be-appended-to-every-http-redirect-from-the-login-endpoint"></a>Fragmentos vazios serão anexados a cada redirecionamento HTTP do ponto de extremidade de logon.

**Data de efetivação**: 8 de fevereiro de 2020

**Pontos de extremidade afetados**: v1.0 e v2.0

**Protocolo afetado**: fluxos OAuth e OIDC que usam response_type = Query – isso abrange o [fluxo do código de autorização](v2-oauth2-auth-code-flow.md) em alguns casos e o [fluxo implícito](v2-oauth2-implicit-grant-flow.md).

Quando uma resposta de autenticação é enviada do login.microsoftonline.com para um aplicativo via redirecionamento HTTP, o serviço acrescentará um fragmento vazio à URL de resposta.  Isso impede uma classe de ataques de redirecionamento, garantindo que o navegador apague qualquer fragmento existente na solicitação de autenticação.  Nenhum aplicativo deve ter uma dependência desse comportamento.


## <a name="august-2019"></a>Agosto de 2019

### <a name="post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored"></a>A semântica do formulário de POSTAgem será imposta mais estritamente espaços e aspas será ignorada

**Data de efetivação**: 2 de setembro de 2019

**Pontos de extremidade afetados**: v1.0 e v2.0

**Protocolo afetado**: a postagem em qualquer lugar é usada ([credenciais do cliente](./v2-oauth2-client-creds-grant-flow.md), resgate de [código de autorização](./v2-oauth2-auth-code-flow.md), [ROPC](./v2-oauth-ropc.md), [obo](./v2-oauth2-on-behalf-of-flow.md)e resgate de [token de atualização](./v2-oauth2-auth-code-flow.md#refresh-the-access-token))

A partir da semana de 9/2, as solicitações de autenticação que usam o método POST serão validadas usando padrões de HTTP mais estritos.  Especificamente, espaços e aspas duplas (") não serão mais removidos dos valores do formulário de solicitação. Não se espera que essas alterações interrompam os clientes existentes e garantirão que as solicitações enviadas ao Azure AD sejam manipuladas de forma confiável a cada vez. No futuro (veja acima), planejamos rejeitar ainda mais parâmetros duplicados e ignorar a BOM em solicitações.

Exemplo:

Hoje, `?e=    "f"&g=h` o é analisado de `?e=f&g=h` forma idêntica `e`  ==  `f` .  Com essa alteração, agora ela seria analisada para que, `e`  ==  `    "f"` provavelmente, isso não seja um argumento válido, e a solicitação agora falharia.


## <a name="july-2019"></a>Julho de 2019

### <a name="app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant"></a>Tokens somente de aplicativo para aplicativos de locatário único só serão emitidos se o aplicativo cliente existir no locatário de recursos

**Data de efetivação**: 26 de julho de 2019

**Pontos de extremidade afetados**: [v 1.0](../azuread-dev/v1-oauth2-client-creds-grant-flow.md) e [v 2.0](./v2-oauth2-client-creds-grant-flow.md)

**Protocolo afetado**: [credenciais de cliente (tokens somente de aplicativo)](../azuread-dev/v1-oauth2-client-creds-grant-flow.md)

Uma alteração de segurança entrou em 26 de julho que altera a maneira como os tokens somente de aplicativo (por meio da concessão de credenciais de cliente) são emitidos. Anteriormente, os aplicativos eram autorizados a obter tokens para chamar qualquer outro aplicativo, independentemente da presença no locatário ou das funções consentidas para esse aplicativo.  Esse comportamento foi atualizado para que, para recursos (às vezes chamados de APIs da Web) definido como um único locatário (o padrão), o aplicativo cliente deve existir dentro do locatário do recurso.  Observe que o consentimento existente entre o cliente e a API ainda não é necessário, e os aplicativos ainda devem estar fazendo suas próprias verificações de autorização para garantir que uma `roles` declaração esteja presente e contenha o valor esperado para a API.

A mensagem de erro para este cenário declara atualmente:

`The service principal named <appName> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.`

Para corrigir esse problema, use a experiência de consentimento do administrador para criar a entidade de serviço do aplicativo cliente em seu locatário ou crie-a manualmente.  Esse requisito garante que o locatário tenha concedido a permissão do aplicativo para operar dentro do locatário.

#### <a name="example-request"></a>Solicitação de exemplo

`https://login.microsoftonline.com/contoso.com/oauth2/authorize?resource=https://gateway.contoso.com/api&response_type=token&client_id=14c88eee-b3e2-4bb0-9233-f5e3053b3a28&...` Neste exemplo, o locatário (autoridade) do recurso é contoso.com, o aplicativo de recurso é um aplicativo de locatário único chamado `gateway.contoso.com/api` para o locatário da Contoso e o aplicativo cliente é `14c88eee-b3e2-4bb0-9233-f5e3053b3a28` .  Se o aplicativo cliente tiver uma entidade de serviço dentro de Contoso.com, essa solicitação poderá continuar.  No entanto, se isso não acontecer, a solicitação falhará com o erro acima.

No entanto, se o aplicativo de gateway da Contoso fosse de vários locatários, a solicitação continuaria independentemente do aplicativo cliente ter uma entidade de serviço em Contoso.com.

### <a name="redirect-uris-can-now-contain-query-string-parameters"></a>URIs de redirecionamento agora podem conter parâmetros de cadeia de caracteres de consulta

**Data de efetivação**: 22 de julho de 2019

**Pontos de extremidade afetados**: v1.0 e v2.0

**Protocolo afetado**: todos os fluxos

Por [RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2), os aplicativos do Azure ad agora podem registrar e usar URIs de redirecionamento (resposta) com parâmetros de consulta estáticos (como `https://contoso.com/oauth2?idp=microsoft` ) para solicitações do OAuth 2,0.  URIs de redirecionamento dinâmico ainda são proibidos, pois representam um risco de segurança, e isso não pode ser usado para reter informações de estado em uma solicitação de autenticação-para isso, use o `state` parâmetro.

O parâmetro de consulta estática está sujeito à correspondência de cadeia de caracteres para URIs de redirecionamento como qualquer outra parte do URI de redirecionamento-se nenhuma cadeia de caracteres estiver registrada que corresponda à redirect_uri decodificada por URI, a solicitação será rejeitada.  Se o URI for encontrado no registro do aplicativo, a cadeia de caracteres inteira será usada para redirecionar o usuário, incluindo o parâmetro de consulta estática.

Observe que, neste momento (fim de julho de 2019), o UX de registro de aplicativo no portal do Azure ainda bloqueia parâmetros de consulta.  No entanto, você pode editar o manifesto do aplicativo manualmente para adicionar parâmetros de consulta e testá-lo em seu aplicativo.


## <a name="march-2019"></a>Março de 2019

### <a name="looping-clients-will-be-interrupted"></a>Os clientes de loop serão interrompidos

**Data de efetivação**: 25 de março de 2019

**Pontos de extremidade afetados**: v1.0 e v2.0

**Protocolo afetado**: todos os fluxos

Às vezes, os aplicativos cliente podem se comportar, emitindo centenas da mesma solicitação de logon em um curto período de tempo.  Essas solicitações podem ou não ser bem-sucedidas, mas todas contribuem para a má experiência do usuário e cargas de trabalho aumentadas para o IDP, aumentando a latência para todos os usuários e reduzindo a disponibilidade do IDP.  Esses aplicativos estão operando fora dos limites de uso normal e devem ser atualizados para se comportarem corretamente.

Os clientes que emitirem solicitações duplicadas várias vezes receberão um `invalid_grant` erro: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` .

A maioria dos clientes não precisará alterar o comportamento para evitar esse erro.  Somente clientes mal configurados (aqueles sem cache de token ou aqueles que exibem loops de prompt já) serão afetados por esse erro.  Os clientes são acompanhados em uma base por instância localmente (via cookie) nos seguintes fatores:

* Dica de usuário, se houver

* Escopos ou recursos sendo solicitados

* ID do Cliente

* URI de redirecionamento

* Tipo e modo de resposta

Os aplicativos que fazem várias solicitações (15 +) em um curto período de tempo (5 minutos) receberão um `invalid_grant` erro explicando que eles estão em loop.  Os tokens que estão sendo solicitados têm tempos de vida de vida longa suficiente (10 minutos no mínimo, 60 minutos por padrão), portanto, solicitações repetidas nesse período de tempo são desnecessárias.

Todos os aplicativos devem lidar `invalid_grant` com a exibição de um prompt interativo, em vez de solicitar silenciosamente um token.  Para evitar esse erro, os clientes devem garantir que eles estejam armazenando em cache corretamente os tokens recebidos.


## <a name="october-2018"></a>Outubro de 2018

### <a name="authorization-codes-can-no-longer-be-reused"></a>Não é mais possível reutilizar códigos de autorização

**Data de efetivação**: 15 de novembro de 2018

**Pontos de extremidade afetados**: v1.0 e v2.0

**Protocolo afetado**: [Fluxo de código](v2-oauth2-auth-code-flow.md)

A partir de 15 de novembro de 2018, o Azure AD deixará de aceitar códigos de autenticação usados anteriormente para aplicativos. Essa alteração de segurança ajuda a deixar o Azure AD em conformidade com a especificação do OAuth e será aplicada aos pontos de extremidade v1 e v2.

Se seu aplicativo reutiliza códigos de autorização para obter tokens para vários recursos, recomendamos que você use o código para obter um token de atualização e, em seguida, utilize esse token de atualização para adquirir tokens adicionais para outros recursos. Os códigos de autorização só podem ser usados uma vez; porém, os códigos de atualização podem ser usados várias vezes em diferentes recursos. Qualquer aplicativo novo que tente reutilizar um código de autenticação durante o fluxo de código OAuth receberá um erro invalid_grant.

Para obter mais informações sobre tokens de atualização, consulte [Atualização de tokens de acesso](v2-oauth2-auth-code-flow.md#refresh-the-access-token).  Se usar a ADAL ou MSAL, isso é feito para você pela biblioteca – substituir a segunda instância de 'AcquireTokenByAuthorizationCodeAsync' por 'AcquireTokenSilentAsync'.

## <a name="may-2018"></a>Maio de 2018

### <a name="id-tokens-cannot-be-used-for-the-obo-flow"></a>Os tokens de ID não podem ser usados para o fluxo OBO

**Data**: 1º de maio de 2018

**Pontos de extremidade afetados**: v1.0 e v2.0

**Protocolos afetados**: fluxo implícito e [fluxo em nome de](v2-oauth2-on-behalf-of-flow.md)

Após 1 de maio de 2018, os id_tokens não podem ser usados como a declaração em um Fluxo OBO para novos aplicativos. Em vez disso, é necessário usar tokens de acesso para proteger as APIs, até mesmo entre um cliente e a camada intermediária do mesmo aplicativo. Os aplicativos registrados antes de 1º de maio de 2018 continuarão funcionando e poderão trocar id_tokens por um token de acesso, no entanto, esse padrão não é considerado uma melhor prática.

Para contornar essa alteração, é possível fazer o seguinte:

1. Crie uma API da Web para seu aplicativo, com um ou mais escopos. Esse ponto de entrada explícito permitirá que segurança e controle mais precisos.
1. No manifesto do aplicativo, no [portal do Azure](https://portal.azure.com) ou no [portal de registro do aplicativo](https://apps.dev.microsoft.com), verifique se o aplicativo tem permissão para emitir tokens de acesso por meio do fluxo implícito. Isso é controlado pela chave `oauth2AllowImplicitFlow`.
1. Quando o aplicativo cliente solicita uma id_token via `response_type=id_token` , solicite também um token de acesso ( `response_type=token` ) para a API Web criada acima. Portanto, ao usar o ponto de extremidade v 2.0, o parâmetro `scope` deverá ser semelhante ao `api://GUID/SCOPE`. No ponto de extremidade v1.0, o parâmetro `resource` deve ser o URI da API do aplicativo Web.
1. Passe esse token de acesso para a camada intermediária no lugar de id_token.
