---
title: Uso avançado de autenticação e autorização – Serviço de Aplicativo do Azure | Microsoft Docs
description: Mostra como personalizar a autenticação e a autorização no Serviço de Aplicativo e obter declarações de usuário e tokens diferentes.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/08/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: b4b70a45758f697c469895bcef6ea8d203065e26
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67853970"
---
# <a name="advanced-usage-of-authentication-and-authorization-in-azure-app-service"></a>Uso avançado de autenticação e autorização no Serviço de Aplicativo do Azure

Este artigo mostra como personalizar a [autenticação e a autorização integradas no Serviço de Aplicativo](overview-authentication-authorization.md) e gerenciar a identidade do seu aplicativo. 

Para começar rapidamente, veja um dos seguintes tutoriais:

* [Tutorial: Autenticar e autorizar usuários de ponta a ponta no Serviço de Aplicativo do Azure (Windows)](app-service-web-tutorial-auth-aad.md)
* [Tutorial: Autenticar e autorizar usuários de ponta a ponta no Serviço de Aplicativo do Azure para Linux](containers/tutorial-auth-aad.md)
* [Como configurar seu aplicativo para usar o logon do Active Directory do Azure](configure-authentication-provider-aad.md)
* [Como configurar seu aplicativo para usar o logon do Facebook](configure-authentication-provider-facebook.md)
* [Como configurar seu aplicativo para usar o logon do Google](configure-authentication-provider-google.md)
* [Como configurar seu aplicativo para usar o logon da Conta da Microsoft](configure-authentication-provider-microsoft.md)
* [Como configurar seu aplicativo para usar o logon do Twitter](configure-authentication-provider-twitter.md)

## <a name="use-multiple-sign-in-providers"></a>Usar vários provedores de entrada

A configuração do portal não oferece uma maneira prática turnkey para apresentar vários provedores de entrada aos usuários (como o Facebook e o Twitter). No entanto, não é difícil adicionar a funcionalidade ao aplicativo. As etapas são destacadas como a seguir:

Primeiro, na página **Autenticação/Autorização** no Portal do Azure, configure cada provedor de identidade que você deseja habilitar.

Em **Ação a tomar quando a solicitação não está autenticada**, selecione **Permitir solicitações anônimas (nenhuma ação)** .

Na página de entrada, na barra de navegação, ou em qualquer outro local do aplicativo, adicione um link de entrada a cada um dos provedores que você habilitou (`/.auth/login/<provider>`). Por exemplo:

```HTML
<a href="/.auth/login/aad">Log in with Azure AD</a>
<a href="/.auth/login/microsoftaccount">Log in with Microsoft Account</a>
<a href="/.auth/login/facebook">Log in with Facebook</a>
<a href="/.auth/login/google">Log in with Google</a>
<a href="/.auth/login/twitter">Log in with Twitter</a>
```

Quando o usuário clica em um dos links, a respectiva página de entrada é aberta para que ele entre.

Para redirecionar o usuário pós-entada para uma URL personalizada, use o parâmetro de cadeia de caracteres de consulta `post_login_redirect_url` (não deve ser confundido com o URI de redirecionamento na configuração do provedor de identidade). Por exemplo, para orientar o usuário para `/Home/Index` após entrar, use o seguinte código HTML:

```HTML
<a href="/.auth/login/<provider>?post_login_redirect_url=/Home/Index">Log in</a>
```

## <a name="validate-tokens-from-providers"></a>Validar os tokens de provedores

Em um login direcionado ao cliente, o aplicativo faz login manual do usuário no provedor e, em seguida, envia o token de autenticação para o Serviço de Aplicativo para validação (consulte [Fluxo de Autenticação](overview-authentication-authorization.md#authentication-flow)). Essa validação em si não concede a você acesso aos recursos desejados do aplicativo, mas uma validação bem-sucedida fornecerá um token de sessão que você pode usar para acessar os recursos do aplicativo. 

Para validar o token do provedor, o aplicativo Serviço de Aplicativo deve ser configurado primeiro com o provedor desejado. Em tempo de execução, depois de recuperar o token de autenticação do seu provedor, poste o token em `/.auth/login/<provider>` para validação. Por exemplo: 

```
POST https://<appname>.azurewebsites.net/.auth/login/aad HTTP/1.1
Content-Type: application/json

{"id_token":"<token>","access_token":"<token>"}
```

O formato do token varia ligeiramente de acordo com o provedor. Consulte a tabela a seguir para obter detalhes:

| Valor do provedor | Necessário no corpo da solicitação | Comentários |
|-|-|-|
| `aad` | `{"access_token":"<access_token>"}` | |
| `microsoftaccount` | `{"access_token":"<token>"}` | A propriedade `expires_in` é opcional. <br/>Ao solicitar o token de serviços em tempo real, sempre solicitar o `wl.basic` escopo. |
| `google` | `{"id_token":"<id_token>"}` | A propriedade `authorization_code` é opcional. Quando especificado, ele também pode ser acompanhado pelo `redirect_uri` propriedade. |
| `facebook`| `{"access_token":"<user_access_token>"}` | Use um válido [token de acesso do usuário](https://developers.facebook.com/docs/facebook-login/access-tokens) do Facebook. |
| `twitter` | `{"access_token":"<access_token>", "access_token_secret":"<acces_token_secret>"}` | |
| | | |

Se o token do provedor for validado com êxito, a API retorna com um `authenticationToken` no corpo da resposta, que é seu token de sessão. 

```json
{
    "authenticationToken": "...",
    "user": {
        "userId": "sid:..."
    }
}
```

Uma vez que esse token de sessão, você pode acessar os recursos de aplicativo protegido, adicionando o `X-ZUMO-AUTH` cabeçalho às solicitações HTTP. Por exemplo: 

```
GET https://<appname>.azurewebsites.net/api/products/1
X-ZUMO-AUTH: <authenticationToken_value>
```

## <a name="sign-out-of-a-session"></a>Sair de uma sessão

Os usuários podem iniciar uma saída, enviando uma `GET` solicitação ao ponto de extremidade `/.auth/logout` do aplicativo. A solicitação `GET` faz o seguinte:

- Limpa os cookies de autenticação da sessão atual.
- Exclui os tokens do usuário atual do Token Store.
- Para o Azure Active Directory e o Google, executa uma saída do servidor no provedor de identidade.

Aqui está um link de saída simples em uma página da Web:

```HTML
<a href="/.auth/logout">Sign out</a>
```

Por padrão, uma saída com êxito redireciona o cliente para a URL `/.auth/logout/done`. É possível alterar a página de redirecionamento pós-saída, adicionando o parâmetro de consulta `post_logout_redirect_uri`. Por exemplo:

```
GET /.auth/logout?post_logout_redirect_uri=/index.html
```

É recomendável [codificar](https://wikipedia.org/wiki/Percent-encoding) o valor de `post_logout_redirect_uri`.

Ao usar URLs totalmente qualificadas, a URL deve ser hospedada no mesmo domínio ou configurada como uma URL de redirecionamento externo permitido para o aplicativo. No exemplo a seguir, para redirecionar para `https://myexternalurl.com` que não está hospedado no mesmo domínio:

```
GET /.auth/logout?post_logout_redirect_uri=https%3A%2F%2Fmyexternalurl.com
```

É necessário executar o comando a seguir no [Azure Cloud Shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp auth update --name <app_name> --resource-group <group_name> --allowed-external-redirect-urls "https://myexternalurl.com"
```

## <a name="preserve-url-fragments"></a>Preservar fragmentos de URL

Depois que os usuários entram no aplicativo, geralmente querem ser redirecionados para a mesma seção da mesma página, como `/wiki/Main_Page#SectionZ`. No entanto, como os [fragmentos de URL](https://wikipedia.org/wiki/Fragment_identifier) (por exemplo, `#SectionZ`) nunca são enviados ao servidor, consequentemente não são preservados por padrão depois que a assinatura OAuth é concluída e redirecionada de volta ao aplicativo. Portanto, os usuários obtêm uma experiência abaixo do ideal quando precisam navegar novamente para a âncora desejada. Essa limitação aplica-se a todas as soluções de autenticação do lado do servidor.

Na autenticação do Serviço de Aplicativo, é possível preservar os fragmentos de URL na assinatura OAuth. Para fazer isso, defina uma configuração de aplicativo chamada `WEBSITE_AUTH_PRESERVE_URL_FRAGMENT` para `true`. Você pode fazer isso no [portal do Azure](https://portal.azure.com) ou simplesmente executar o comando a seguir no [Azure Cloud Shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group <group_name> --settings WEBSITE_AUTH_PRESERVE_URL_FRAGMENT="true"
```

## <a name="access-user-claims"></a>Acessar declarações de usuários

O Serviço de Aplicativo transmite declarações do usuário para seu aplicativo usando cabeçalhos especiais. Solicitações externas não são permitidas para definir esses cabeçalhos, portanto são apresentadas somente se definido pelo Serviço de Aplicativo. Alguns cabeçalhos de exemplo incluem:

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID

Um código escrito em qualquer linguagem ou estrutura pode obter as informações necessárias desses cabeçalhos. Para aplicativos ASP.NET 4.6, **ClaimsPrincipal** é definido automaticamente com os valores apropriados.

Seu aplicativo também pode obter detalhes adicionais sobre o usuário autenticado chamando `/.auth/me`. Os SDKs do servidor dos Aplicativos Móveis fornecem métodos auxiliares para trabalhar com esses dados. Para saber mais, confira [Como usar o SDK do Node.js dos Aplicativos Móveis do Azure](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-getidentity) e [Trabalhar com o SDK do servidor de back-end .NET para Aplicativos Móveis do Azure](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#user-info).

## <a name="retrieve-tokens-in-app-code"></a>Recuperar tokens no código do aplicativo

No seu código de servidor, os tokens específicos do provedor são injetados no cabeçalho da solicitação, para que você possa acessá-los facilmente. A tabela a seguir mostra os possíveis nomes de cabeçalho do token:

| Provedor | Nomes do Cabeçalho |
|-|-|
| Azure Active Directory | `X-MS-TOKEN-AAD-ID-TOKEN` <br/> `X-MS-TOKEN-AAD-ACCESS-TOKEN` <br/> `X-MS-TOKEN-AAD-EXPIRES-ON`  <br/> `X-MS-TOKEN-AAD-REFRESH-TOKEN` |
| Token do Facebook | `X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN` <br/> `X-MS-TOKEN-FACEBOOK-EXPIRES-ON` |
| Google | `X-MS-TOKEN-GOOGLE-ID-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-ACCESS-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-EXPIRES-ON` <br/> `X-MS-TOKEN-GOOGLE-REFRESH-TOKEN` |
| Conta da Microsoft | `X-MS-TOKEN-MICROSOFTACCOUNT-ACCESS-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-EXPIRES-ON` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-AUTHENTICATION-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-REFRESH-TOKEN` |
| Twitter | `X-MS-TOKEN-TWITTER-ACCESS-TOKEN` <br/> `X-MS-TOKEN-TWITTER-ACCESS-TOKEN-SECRET` |
|||

Do seu código do cliente (por exemplo, um aplicativo móvel ou JavaScript no navegador), envie uma solicitação HTTP `GET` para `/.auth/me`. O JSON retornado tem os tokens específicos do provedor.

> [!NOTE]
> Tokens de acesso são para acessar recursos do provedor, para que eles fiquem presentes somente se você configurar o provedor com um segredo do cliente. Para saber como obter tokens de atualização, confira Tokens de acesso de atualização.

## <a name="refresh-identity-provider-tokens"></a>Atualizar tokens do provedor de identidade

Quando o token de acesso do seu provedor (não o [token de sessão](#extend-session-token-expiration-grace-period)) expirar, você precisará autenticar novamente o usuário antes de usar esse token novamente. Você pode evitar a expiração do token fazendo uma `GET` chamada para o `/.auth/refresh` ponto de extremidade de seu aplicativo. Quando chamado, o Serviço de Aplicativo atualiza automaticamente tokens de acesso no repositório de token para o usuário autenticado. As solicitações subsequentes de tokens do seu código do aplicativo obtêm tokens atualizados. No entanto, para que a atualização do token funcione, o repositório de token deve conter [tokens de atualização](https://auth0.com/learn/refresh-tokens/) para o seu provedor. A forma de obter tokens de atualização é documentada por cada provedor, mas a lista a seguir traz um breve resumo:

- **Google**: Acrescente um parâmetro de cadeia de caracteres de consulta `access_type=offline` à chamada à API `/.auth/login/google`. Se usar o SDK de Aplicativos Móveis, você pode adicionar o parâmetro a uma das `LogicAsync` sobrecargas (consulte [Tokens de atualização do Google](https://developers.google.com/identity/protocols/OpenIDConnect#refresh-tokens)).
- **Facebook**: Não fornece tokens de atualização. Tokens de vida útil longa expiram em 60 dias (consulte [Expiração e extensão de tokens de acesso do Facebook](https://developers.facebook.com/docs/facebook-login/access-tokens/expiration-and-extension)).
- **Twitter**: Tokens de acesso não expiram (confira [Perguntas frequentes sobre o OAuth do Twitter](https://developer.twitter.com/en/docs/basics/authentication/FAQ)).
- **Conta Microsoft**: Ao [definir configurações de autenticação de conta Microsoft](configure-authentication-provider-microsoft.md), selecione o escopo `wl.offline_access`.
- **Azure Active Directory**: No [https://resources.azure.com](https://resources.azure.com), execute as seguintes etapas:
    1. Na parte superior da página, selecione **Ler/Gravar**.
    2. No navegador à esquerda, navegue até **assinaturas** >  **_\<assinatura\_nome_**  > **resourceGroups** >  **_\<recurso\_grupo\_nome >_**  > **provedores** > **Microsoft.web** > **sites** >  **_\<aplicativo \_nome >_**  > **config** > **authsettings**. 
    3. Clique em **Editar**.
    4. Modifique a propriedade a seguir. Substitua _\<aplicativo\_id>_ pela ID do aplicativo do Azure Active Directory do serviço que você deseja acessar.

        ```json
        "additionalLoginParams": ["response_type=code id_token", "resource=<app_id>"]
        ```

    5. Clique em **Put**. 

Depois que seu provedor estiver configurado, você poderá [ encontrar o token de atualização e o tempo de expiração do token de acesso ](#retrieve-tokens-in-app-code) na loja do token. 

Para atualizar o token de acesso a qualquer momento, basta chamar `/.auth/refresh` em qualquer idioma. O snippet a seguir usa o jQuery para atualizar seus tokens de acesso de um cliente JavaScript.

```JavaScript
function refreshTokens() {
  let refreshUrl = "/.auth/refresh";
  $.ajax(refreshUrl) .done(function() {
    console.log("Token refresh completed successfully.");
  }) .fail(function() {
    console.log("Token refresh failed. See application logs for details.");
  });
}
```

Se um usuário revoga as permissões concedidas ao seu aplicativo, a chamada para `/.auth/me` pode falhar com um resposta `403 Forbidden`. Para diagnosticar erros, verifique os logs de aplicativo para obter detalhes.

## <a name="extend-session-token-expiration-grace-period"></a>Estender o período de cortesia de término do token da sessão

A sessão autenticada expira após 8 horas. Depois que uma sessão autenticada expira, há um período de cortesia de 72 horas por padrão. Dentro desse período, você pode atualizar o token de sessão com o Serviço de Aplicativo sem reautenticar o usuário. Você pode simplesmente chamar `/.auth/refresh` quando o token de sessão se torna inválido e não é necessário rastrear o término do token por conta própria. Após o período de cortesia de 72 horas, o usuário deverá entrar novamente para obter um token de sessão válido.

Se 72 horas não for tempo suficiente, você pode estender essa janela de expiração. Estender a expiração por um longo período pode ter implicações significativas de segurança (por exemplo, quando um token de autenticação é perdido ou roubado). Portanto, você deve deixar o padrão de 72 horas ou definir o período de extensão para o menor valor.

Para estender a janela de validade padrão, execute o seguinte comando no [Cloud Shell](../cloud-shell/overview.md).

```azurecli-interactive
az webapp auth update --resource-group <group_name> --name <app_name> --token-refresh-extension-hours <hours>
```

> [!NOTE]
> O período de cortesia se aplica somente à sessão autenticada do Serviço de Aplicativo, não aos tokens de provedores de identidade. Não há um período de cortesia para os tokens de provedor expirados. 
>

## <a name="limit-the-domain-of-sign-in-accounts"></a>Limite do domínio de contas de entrada

A Conta da Microsoft e o Microsoft Azure Active Directory permitem a entrada de vários domínios. Por exemplo, a Conta da Microsoft permite contas de _outlook.com_, _live.com_ e _hotmail.com_. O Microsoft Azure Active Directory permite qualquer número de domínios personalizados para as contas de entrada. Esse comportamento pode ser indesejável para um aplicativo interno, que você não deseja que qualquer pessoa com uma conta de _outlook.com_ acesse. Para limitar o nome de domínio das contas de entrada, siga estas etapas.

Em [https://resources.azure.com](https://resources.azure.com), navegue até **assinaturas** >  **_\< assinatura\_ nome_**  > **resourceGroups** >  **_\< recurso\_ grupo\_ nome>_**  > **provedores** > **Microsoft.Web** > **sites** >  **_\< aplicativo\_ nome>_**  > **config** > **authsettings**. 

Clique em **Editar**, modifique a propriedade a seguir e, em seguida, clique em **Put**. Certifique-se de substituir _\<domínio\_nome >_ pelo domínio que você deseja.

```json
"additionalLoginParams": ["domain_hint=<domain_name>"]
```
## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial: Autenticar e autorizar usuários de ponta a ponta (Windows)](app-service-web-tutorial-auth-aad.md)
> [Tutorial: Autenticar e autorizar usuários de ponta a ponta (Linux)](containers/tutorial-auth-aad.md)
