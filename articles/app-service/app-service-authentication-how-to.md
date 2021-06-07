---
title: Uso avançado de Authn/AuthZ
description: Saiba como personalizar o recurso de autenticação e autorização no serviço de aplicativo para diferentes cenários e obter declarações de usuário e tokens diferentes.
ms.topic: article
ms.date: 07/08/2020
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: fc2916cbccc21262467533b0b497b14f4f4b941c
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105034870"
---
# <a name="advanced-usage-of-authentication-and-authorization-in-azure-app-service"></a>Uso avançado de autenticação e autorização no Serviço de Aplicativo do Azure

Este artigo mostra como personalizar a [autenticação e a autorização integradas no Serviço de Aplicativo](overview-authentication-authorization.md) e gerenciar a identidade do seu aplicativo. 

Para começar rapidamente, veja um dos seguintes tutoriais:

* [Tutorial: Autenticar e autorizar usuários de ponta a ponta no Serviço de Aplicativo do Azure](tutorial-auth-aad.md)
* [Como configurar seu aplicativo para usar o logon do Azure Active Directory](configure-authentication-provider-aad.md)
* [Como configurar seu aplicativo para usar o logon do Facebook](configure-authentication-provider-facebook.md)
* [Como configurar seu aplicativo para usar o logon do Google](configure-authentication-provider-google.md)
* [Como configurar seu aplicativo para usar o logon da Conta da Microsoft](configure-authentication-provider-microsoft.md)
* [Como configurar seu aplicativo para usar o logon do Twitter](configure-authentication-provider-twitter.md)
* [Como configurar seu aplicativo para fazer logon usando um provedor do OpenID Connect (versão prévia)](configure-authentication-provider-openid-connect.md)
* [Como configurar seu aplicativo para fazer logon usando uma entrada com a Apple (versão prévia)](configure-authentication-provider-apple.md)

## <a name="use-multiple-sign-in-providers"></a>Usar vários provedores de entrada

A configuração do portal não oferece uma maneira prática turnkey para apresentar vários provedores de entrada aos usuários (como o Facebook e o Twitter). No entanto, não é difícil adicionar a funcionalidade ao aplicativo. As etapas são destacadas como a seguir:

Primeiro, na página **Autenticação/Autorização** no Portal do Azure, configure cada provedor de identidade que você deseja habilitar.

Em **Ação a tomar quando a solicitação não está autenticada**, selecione **Permitir solicitações anônimas (nenhuma ação)**.

Na página de entrada, na barra de navegação, ou em qualquer outro local do aplicativo, adicione um link de entrada a cada um dos provedores que você habilitou (`/.auth/login/<provider>`). Por exemplo:

```html
<a href="/.auth/login/aad">Log in with Azure AD</a>
<a href="/.auth/login/microsoftaccount">Log in with Microsoft Account</a>
<a href="/.auth/login/facebook">Log in with Facebook</a>
<a href="/.auth/login/google">Log in with Google</a>
<a href="/.auth/login/twitter">Log in with Twitter</a>
<a href="/.auth/login/apple">Log in with Apple</a>
```

Quando o usuário clica em um dos links, a respectiva página de entrada é aberta para que ele entre.

Para redirecionar o usuário pós-entada para uma URL personalizada, use o parâmetro de cadeia de caracteres de consulta `post_login_redirect_url` (não deve ser confundido com o URI de redirecionamento na configuração do provedor de identidade). Por exemplo, para orientar o usuário para `/Home/Index` após entrar, use o seguinte código HTML:

```html
<a href="/.auth/login/<provider>?post_login_redirect_url=/Home/Index">Log in</a>
```

## <a name="validate-tokens-from-providers"></a>Validar os tokens de provedores

Em um login direcionado ao cliente, o aplicativo faz login manual do usuário no provedor e, em seguida, envia o token de autenticação para o Serviço de Aplicativo para validação (consulte [Fluxo de Autenticação](overview-authentication-authorization.md#authentication-flow)). Essa validação em si não concede a você acesso aos recursos desejados do aplicativo, mas uma validação bem-sucedida fornecerá um token de sessão que você pode usar para acessar os recursos do aplicativo. 

Para validar o token do provedor, o aplicativo Serviço de Aplicativo deve ser configurado primeiro com o provedor desejado. Em runtime, depois de recuperar o token de autenticação do seu provedor, poste o token em `/.auth/login/<provider>` para validação. Por exemplo: 

```
POST https://<appname>.azurewebsites.net/.auth/login/aad HTTP/1.1
Content-Type: application/json

{"id_token":"<token>","access_token":"<token>"}
```

O formato do token varia ligeiramente de acordo com o provedor. Para obter detalhes, consulte a tabela a seguir:

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

```html
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

Execute o seguinte comando no [Azure cloud Shell](../cloud-shell/quickstart.md):

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

Um código escrito em qualquer linguagem ou estrutura pode obter as informações necessárias desses cabeçalhos. Para aplicativos ASP.NET 4.6, **ClaimsPrincipal** é definido automaticamente com os valores apropriados. O ASP.NET Core, no entanto, não fornece um middleware de autenticação que se integra às declarações de usuário do serviço de aplicativo. Para obter uma solução alternativa, consulte [MaximeRouiller. Azure. AppService. EasyAuth](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth).

Se o [repositório de token](overview-authentication-authorization.md#token-store) estiver habilitado para seu aplicativo, você também poderá obter detalhes adicionais sobre o usuário autenticado chamando `/.auth/me` . Os SDKs do servidor dos Aplicativos Móveis fornecem métodos auxiliares para trabalhar com esses dados. Para saber mais, confira [Como usar o SDK do Node.js dos Aplicativos Móveis do Azure](/previous-versions/azure/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk#howto-tables-getidentity) e [Trabalhar com o SDK do servidor de back-end .NET para Aplicativos Móveis do Azure](/previous-versions/azure/app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk#user-info).

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

Do seu código de cliente (como um aplicativo móvel ou JavaScript no navegador), envie uma solicitação HTTP `GET` para `/.auth/me` (o[repositório de token](overview-authentication-authorization.md#token-store) deve ser habilitado). O JSON retornado tem os tokens específicos do provedor.

> [!NOTE]
> Tokens de acesso são para acessar recursos do provedor, para que eles fiquem presentes somente se você configurar o provedor com um segredo do cliente. Para saber como obter tokens de atualização, confira Tokens de acesso de atualização.

## <a name="refresh-identity-provider-tokens"></a>Atualizar tokens do provedor de identidade

Quando o token de acesso do seu provedor (não o [token de sessão](#extend-session-token-expiration-grace-period)) expirar, você precisará autenticar novamente o usuário antes de usar esse token novamente. Você pode evitar a expiração do token fazendo uma `GET` chamada para o `/.auth/refresh` ponto de extremidade de seu aplicativo. Quando chamado, o serviço de aplicativo atualiza automaticamente os tokens de acesso no [repositório de token](overview-authentication-authorization.md#token-store) para o usuário autenticado. As solicitações subsequentes de tokens do seu código do aplicativo obtêm tokens atualizados. No entanto, para que a atualização do token funcione, o repositório de token deve conter [tokens de atualização](https://auth0.com/learn/refresh-tokens/) para o seu provedor. A forma de obter tokens de atualização é documentada por cada provedor, mas a lista a seguir traz um breve resumo:

- **Google**: anexe um `access_type=offline` parâmetro de cadeia de caracteres para consulta a sua `/.auth/login/google` chamada à API. Se usar o SDK de Aplicativos Móveis, você pode adicionar o parâmetro a uma das `LogicAsync` sobrecargas (consulte [Tokens de atualização do Google](https://developers.google.com/identity/protocols/OpenIDConnect#refresh-tokens)).
- **Facebook**: não fornece tokens de atualização. Tokens de vida útil longa expiram em 60 dias (consulte [Expiração e extensão de tokens de acesso do Facebook](https://developers.facebook.com/docs/facebook-login/access-tokens/expiration-and-extension)).
- **Twitter**: tokens de acesso não expiram (consulte [Perguntas frequentes sobre o OAuth do Twitter](https://developer.twitter.com/en/docs/authentication/faq)).
- **Microsoft Account**: quando [definir configurações de autenticação de conta Microsoft](configure-authentication-provider-microsoft.md), selecione o escopo `wl.offline_access`.
- **Azure Active Directory**: em [https://resources.azure.com](https://resources.azure.com), execute as seguintes etapas:
    1. Na parte superior da página, selecione **Ler/Gravar**.
    2. No navegador esquerdo, navegue até **assinaturas** > * *_\<subscription\_name_** > **resourceGroups** > *_* * * \<resource\_group\_name> _> **provedores**  >  **Microsoft. Web**  >  **sites** > * *_ \<app\_name> _ * * > **config**  >  **authsettings**. 
    3. Clique em **Editar**.
    4. Modifique a propriedade a seguir. Substitua _\<app\_id>_ pela ID de aplicativo Azure Active Directory do serviço que você deseja acessar.

        ```json
        "additionalLoginParams": ["response_type=code id_token", "resource=<app_id>"]
        ```

    5. Clique em **Put**. 

Depois que seu provedor estiver configurado, você poderá [ encontrar o token de atualização e o tempo de expiração do token de acesso ](#retrieve-tokens-in-app-code) na loja do token. 

Para atualizar seu token de acesso a qualquer momento, basta chamar `/.auth/refresh` em qualquer idioma. O snippet a seguir usa o jQuery para atualizar seus tokens de acesso de um cliente JavaScript.

```javascript
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

A Conta da Microsoft e o Microsoft Azure Active Directory permitem a entrada de vários domínios. Por exemplo, a Conta da Microsoft permite contas de _outlook.com_, _live.com_ e _hotmail.com_. O Azure AD permite qualquer número de domínios personalizados para as contas de entrada. No entanto, talvez você queira acelerar seus usuários diretamente para sua própria página de entrada do Azure AD com marca (como `contoso.com` ). Para sugerir o nome de domínio das contas de entrada, siga estas etapas.

No [https://resources.azure.com](https://resources.azure.com) , navegue até **assinaturas** > * *_\<subscription\_name_** > **resourceGroups** > *_* * * \<resource\_group\_name> _> **provedores**  >  **Microsoft. Web**  >  **sites** > * *_ \<app\_name> _ * * > **config**  >  **authsettings**. 

Clique em **Editar**, modifique a propriedade a seguir e, em seguida, clique em **Put**. Certifique-se de substituir _\<domain\_name>_ pelo domínio desejado.

```json
"additionalLoginParams": ["domain_hint=<domain_name>"]
```

Essa configuração acrescenta o `domain_hint` parâmetro de cadeia de caracteres de consulta à URL de redirecionamento de logon. 

> [!IMPORTANT]
> É possível que o cliente remova o `domain_hint` parâmetro depois de receber a URL de redirecionamento e, em seguida, faça logon com um domínio diferente. Portanto, embora essa função seja conveniente, ela não é um recurso de segurança.
>

## <a name="authorize-or-deny-users"></a>Autorizar ou negar usuários

Embora o serviço de aplicativo se encarrega do caso de autorização mais simples (ou seja, rejeitar solicitações não autenticadas), seu aplicativo pode exigir um comportamento de autorização mais refinado, como limitar o acesso a apenas um grupo específico de usuários. Em determinados casos, você precisa escrever código de aplicativo personalizado para permitir ou negar acesso ao usuário conectado. Em outros casos, o serviço de aplicativo ou o provedor de identidade pode ser capaz de ajudar sem a necessidade de alterações de código.

- [Nível de servidor](#server-level-windows-apps-only)
- [Nível do provedor de identidade](#identity-provider-level)
- [Nível do aplicativo](#application-level)

### <a name="server-level-windows-apps-only"></a>Nível do servidor (somente aplicativos do Windows)

Para qualquer aplicativo do Windows, você pode definir o comportamento de autorização do servidor Web do IIS, editando o arquivo de *Web.config* . Os aplicativos do Linux não usam o IIS e não podem ser configurados por meio de *Web.config*.

1. Navegue até `https://<app-name>.scm.azurewebsites.net/DebugConsole`

1. No Gerenciador de navegador de seus arquivos do serviço de aplicativo, navegue até *site/wwwroot*. Se um *Web.config* não existir, crie-o selecionando **+**  >  **novo arquivo**. 

1. Selecione o lápis para *Web.config* para editá-lo. Adicione o código de configuração a seguir e clique em **salvar**. Se *Web.config* já existir, basta adicionar o `<authorization>` elemento a tudo nele. Adicione as contas que você deseja permitir no `<allow>` elemento.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
       <system.web>
          <authorization>
            <allow users="user1@contoso.com,user2@contoso.com"/>
            <deny users="*"/>
          </authorization>
       </system.web>
    </configuration>
    ```

### <a name="identity-provider-level"></a>Nível do provedor de identidade

O provedor de identidade pode fornecer determinada autorização de chave. Por exemplo:

- Para [Azure app serviço](configure-authentication-provider-aad.md), você pode [gerenciar o acesso de nível corporativo](../active-directory/manage-apps/what-is-access-management.md) diretamente no Azure AD. Para obter instruções, consulte [como remover o acesso de um usuário a um aplicativo](../active-directory/manage-apps/methods-for-removing-user-access.md).
- Para o [Google](configure-authentication-provider-google.md), os projetos de API do Google que pertencem a uma [organização](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy#organizations) podem ser configurados para permitir acesso somente aos usuários em sua organização (consulte a [página de suporte do **OAuth 2,0 Configurando** o Google](https://support.google.com/cloud/answer/6158849?hl=en)).

### <a name="application-level"></a>Nível de aplicativo

Se qualquer um dos outros níveis não fornecer a autorização de que você precisa, ou se sua plataforma ou provedor de identidade não tiver suporte, você deverá escrever um código personalizado para autorizar usuários com base nas [declarações do usuário](#access-user-claims).

## <a name="updating-the-configuration-version-preview"></a>Atualizando a versão de configuração (visualização)

Há duas versões da API de gerenciamento para o recurso de autenticação/autorização. A versão de visualização V2 é necessária para a experiência de "autenticação (visualização)" no portal do Azure. Um aplicativo que já usa a API v1 pode atualizar para a versão v2 depois que algumas alterações forem feitas. Especificamente, a configuração secreta deve ser movida para as configurações do aplicativo de slot-adesivo. A configuração do provedor de conta da Microsoft também não tem suporte na V2 no momento.

> [!WARNING]
> A migração para a versão prévia do v2 desabilitará o gerenciamento do recurso de autenticação/autorização do serviço de aplicativo para seu aplicativo por meio de alguns clientes, como sua experiência existente no portal do Azure, CLI do Azure e Azure PowerShell. Isso não pode ser revertido. Durante a versão prévia, a migração de cargas de trabalho de produção não é incentivada nem suportada. Você só deve seguir as etapas nesta seção para aplicativos de teste.

### <a name="moving-secrets-to-application-settings"></a>Movendo segredos para as configurações do aplicativo

1. Obtenha sua configuração existente usando a API V1:

   ```azurecli
   # For Web Apps
   az webapp auth show -g <group_name> -n <site_name>

   # For Azure Functions
   az functionapp auth show -g <group_name> -n <site_name>
   ```

   Na carga JSON resultante, anote o valor secreto usado para cada provedor que você configurou:

   * AAD `clientSecret`
   * Google `googleClientSecret`
   * Facebook `facebookAppSecret`
   * Twitter `twitterConsumerSecret`
   * Conta da Microsoft: `microsoftAccountClientSecret`

   > [!IMPORTANT]
   > Os valores secretos são credenciais de segurança importantes e devem ser tratados com cuidado. Não compartilhe esses valores ou mantenha-os em um computador local.

1. Crie configurações de aplicativo de slot-adesivo para cada valor secreto. Você pode escolher o nome de cada configuração de aplicativo. Seu valor deve corresponder ao que você obteve na etapa anterior ou [fazer referência a um segredo de Key Vault](./app-service-key-vault-references.md?toc=/azure/azure-functions/toc.json) que você criou com esse valor.

   Para criar a configuração, você pode usar o portal do Azure ou executar uma variação do seguinte para cada provedor:

   ```azurecli
   # For Web Apps, Google example    
   az webapp config appsettings set -g <group_name> -n <site_name> --slot-settings GOOGLE_PROVIDER_AUTHENTICATION_SECRET=<value_from_previous_step>

   # For Azure Functions, Twitter example
   az functionapp config appsettings set -g <group_name> -n <site_name> --slot-settings TWITTER_PROVIDER_AUTHENTICATION_SECRET=<value_from_previous_step>
   ```

   > [!NOTE]
   > As configurações de aplicativo para essa configuração devem ser marcadas como slot-adesivo, o que significa que elas não serão movidas entre ambientes durante uma [operação de permuta de slot](./deploy-staging-slots.md). Isso ocorre porque sua configuração de autenticação em si está vinculada ao ambiente. 

1. Crie um novo arquivo JSON chamado `authsettings.json` . Pegue a saída que você recebeu anteriormente e remova cada valor secreto dela. Grave a saída restante no arquivo, certificando-se de que nenhum segredo esteja incluído. Em alguns casos, a configuração pode ter matrizes que contenham cadeias de caracteres vazias. Certifique-se de que `microsoftAccountOAuthScopes` o não e, se tiver, mude esse valor para `null` .

1. Adicione uma propriedade à `authsettings.json` qual aponta para o nome da configuração de aplicativo que você criou anteriormente para cada provedor:
 
   * AAD `clientSecretSettingName`
   * Google `googleClientSecretSettingName`
   * Facebook `facebookAppSecretSettingName`
   * Twitter `twitterConsumerSecretSettingName`
   * Conta da Microsoft: `microsoftAccountClientSecretSettingName`

   Um arquivo de exemplo após essa operação pode ser semelhante ao seguinte, neste caso, somente configurado para o AAD:

   ```json
   {
       "id": "/subscriptions/00d563f8-5b89-4c6a-bcec-c1b9f6d607e0/resourceGroups/myresourcegroup/providers/Microsoft.Web/sites/mywebapp/config/authsettings",
       "name": "authsettings",
       "type": "Microsoft.Web/sites/config",
       "location": "Central US",
       "properties": {
           "enabled": true,
           "runtimeVersion": "~1",
           "unauthenticatedClientAction": "AllowAnonymous",
           "tokenStoreEnabled": true,
           "allowedExternalRedirectUrls": null,
           "defaultProvider": "AzureActiveDirectory",
           "clientId": "3197c8ed-2470-480a-8fae-58c25558ac9b",
           "clientSecret": "",
           "clientSecretSettingName": "MICROSOFT_IDENTITY_AUTHENTICATION_SECRET",
           "clientSecretCertificateThumbprint": null,
           "issuer": "https://sts.windows.net/0b2ef922-672a-4707-9643-9a5726eec524/",
           "allowedAudiences": [
               "https://mywebapp.azurewebsites.net"
           ],
           "additionalLoginParams": null,
           "isAadAutoProvisioned": true,
           "aadClaimsAuthorization": null,
           "googleClientId": null,
           "googleClientSecret": null,
           "googleClientSecretSettingName": null,
           "googleOAuthScopes": null,
           "facebookAppId": null,
           "facebookAppSecret": null,
           "facebookAppSecretSettingName": null,
           "facebookOAuthScopes": null,
           "gitHubClientId": null,
           "gitHubClientSecret": null,
           "gitHubClientSecretSettingName": null,
           "gitHubOAuthScopes": null,
           "twitterConsumerKey": null,
           "twitterConsumerSecret": null,
           "twitterConsumerSecretSettingName": null,
           "microsoftAccountClientId": null,
           "microsoftAccountClientSecret": null,
           "microsoftAccountClientSecretSettingName": null,
           "microsoftAccountOAuthScopes": null,
           "isAuthFromFile": "false"
       }   
   }
   ```

1. Envie este arquivo como a nova configuração de autenticação/autorização para seu aplicativo:

   ```azurecli
   az rest --method PUT --url "/subscriptions/<subscription_id>/resourceGroups/<group_name>/providers/Microsoft.Web/sites/<site_name>/config/authsettings?api-version=2020-06-01" --body @./authsettings.json
   ```

1. Valide se seu aplicativo ainda está operando conforme o esperado após esse gesto.

1. Exclua o arquivo usado nas etapas anteriores.

Agora você migrou o aplicativo para armazenar os segredos do provedor de identidade como configurações do aplicativo.

### <a name="support-for-microsoft-account-registrations"></a>Suporte para registros de conta Microsoft

Atualmente, a API v2 não dá suporte à conta da Microsoft como um provedor distinto. Em vez disso, ele aproveita a plataforma de [identidade da Microsoft](../active-directory/develop/v2-overview.md) convergida para conectar usuários com contas pessoais da Microsoft. Ao alternar para a API v2, a configuração de Azure Active Directory v1 é usada para configurar o provedor de plataforma de identidade da Microsoft.

Se sua configuração existente contiver um provedor de conta da Microsoft e não contiver um provedor de Azure Active Directory, você poderá alternar a configuração para o provedor de Azure Active Directory e, em seguida, executar a migração. Para fazer isso:

1. Vá para [**registros de aplicativo**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) no portal do Azure e localize o registro associado ao seu provedor de conta da Microsoft. Pode estar no cabeçalho "aplicativos de conta pessoal".
1. Navegue até a página "autenticação" para o registro. Em "URIs de redirecionamento", você deverá ver uma entrada terminando em `/.auth/login/microsoftaccount/callback` . Copie esse URI.
1. Adicione um novo URI que corresponda ao que você acabou de copiar, exceto em que ele termina em `/.auth/login/aad/callback` . Isso permitirá que o registro seja usado pela configuração de autenticação/autorização do serviço de aplicativo.
1. Navegue até a configuração de autenticação/autorização do serviço de aplicativo para seu aplicativo.
1. Colete a configuração do provedor de conta da Microsoft.
1. Configure o provedor de Azure Active Directory usando o modo de gerenciamento "avançado", fornecendo a ID do cliente e os valores de segredo do cliente que você coletou na etapa anterior. Para a URL do emissor, use usar e `<authentication-endpoint>/<tenant-id>/v2.0` substitua *\<authentication-endpoint>* pelo ponto de [extremidade de autenticação para seu ambiente de nuvem](../active-directory/develop/authentication-national-cloud.md#azure-ad-authentication-endpoints) (por exemplo, " https://login.microsoftonline.com " para o Azure global), também substituindo *\<tenant-id>* pela sua **ID de diretório (locatário)**.
1. Depois de salvar a configuração, teste o fluxo de logon navegando no navegador para o `/.auth/login/aad` ponto de extremidade no seu site e conclua o fluxo de entrada.
1. Neste ponto, você copiou a configuração com êxito, mas a configuração existente do provedor de conta da Microsoft permanece. Antes de removê-lo, verifique se todas as partes do seu aplicativo referenciam o provedor de Azure Active Directory por meio de links de logon, etc. Verifique se todas as partes do seu aplicativo funcionam conforme o esperado.
1. Depois de validar que as coisas funcionam com o provedor de Azure Active Directory do AAD, você pode remover a configuração do provedor de conta da Microsoft.

Alguns aplicativos podem já ter registros separados para Azure Active Directory e a conta da Microsoft. Esses aplicativos não podem ser migrados no momento. 

> [!WARNING]
> É possível convergir os dois registros modificando os [tipos de conta com suporte](../active-directory/develop/supported-accounts-validation.md) para o registro do aplicativo do AAD. No entanto, isso forçaria um novo prompt de consentimento para os usuários da conta da Microsoft, e as declarações de identidade dos usuários podem ser diferentes na estrutura, `sub` notavelmente alterando os valores, uma vez que uma nova ID do aplicativo está sendo usada. Essa abordagem não é recomendada a menos que seja totalmente compreendida. Em vez disso, você deve aguardar o suporte para os dois registros na superfície da API v2.

### <a name="switching-to-v2"></a>Alternando para v2

Depois que as etapas acima forem executadas, navegue até o aplicativo na portal do Azure. Selecione a seção "autenticação (visualização)". 

Como alternativa, você pode fazer uma solicitação PUT em relação ao `config/authsettingsv2` recurso sob o recurso site. O esquema para a carga é o mesmo que foi capturado na seção [Configurar usando um arquivo](#config-file) .

## <a name="configure-using-a-file-preview"></a><a name="config-file"> </a>Configurar usando um arquivo (visualização)

As configurações de autenticação podem, opcionalmente, ser configuradas por meio de um arquivo fornecido pela sua implantação. Isso pode ser exigido por determinados recursos de visualização da autenticação/autorização do serviço de aplicativo.

> [!IMPORTANT]
> Lembre-se de que o conteúdo do aplicativo e, portanto, esse arquivo pode ser movido entre ambientes, assim como nos [Slots](./deploy-staging-slots.md). É provável que você queira um registro de aplicativo diferente fixado em cada slot e, nesses casos, você deve continuar usando o método de configuração padrão em vez de usar o arquivo de configuração.

### <a name="enabling-file-based-configuration"></a>Habilitando a configuração baseada em arquivo

> [!CAUTION]
> Durante a visualização, habilitar a configuração baseada em arquivo desabilitará o gerenciamento do recurso de autenticação/autorização do serviço de aplicativo para seu aplicativo por meio de alguns clientes, como o portal do Azure, CLI do Azure e Azure PowerShell.

1. Crie um novo arquivo JSON para sua configuração na raiz do seu projeto (implantado no D:\home\site\wwwroot em seu aplicativo Web/de funções). Preencha a configuração desejada de acordo com a [referência de configuração baseada em arquivo](#configuration-file-reference). Se estiver modificando uma configuração de Azure Resource Manager existente, certifique-se de converter as propriedades capturadas na `authsettings` coleção em seu arquivo de configuração.

2. Modifique a configuração existente, que é capturada nas APIs de [Azure Resource Manager](../azure-resource-manager/management/overview.md) em `Microsoft.Web/sites/<siteName>/config/authsettings` . Para modificar isso, você pode usar um [modelo de Azure Resource Manager](../azure-resource-manager/templates/overview.md) ou uma ferramenta como [Azure Resource Explorer](https://resources.azure.com/). Na coleção authsettings, você precisará definir três propriedades (e pode remover outras):

    1.  Definir `enabled` como "true"
    2.  Definir `isAuthFromFile` como "true"
    3.  Defina `authFilePath` como o nome do arquivo (por exemplo, "auth.jsem")

> [!NOTE]
> O formato de `authFilePath` varia entre plataformas. No Windows, há suporte para caminhos relativos e absolutos. Relativo é recomendado. Para o Linux, somente caminhos absolutos têm suporte no momento, portanto, o valor da configuração deve ser "/Home/site/wwwroot/auth.js" ou semelhante.

Depois de fazer essa atualização de configuração, o conteúdo do arquivo será usado para definir o comportamento da autenticação/autorização do serviço de aplicativo para esse site. Se você quiser retornar à configuração de Azure Resource Manager, poderá fazer isso Configurando `isAuthFromFile` de volta para "false".

### <a name="configuration-file-reference"></a>Referência do arquivo de configuração

Todos os segredos que serão referenciados do arquivo de configuração devem ser armazenados como [configurações do aplicativo](./configure-common.md#configure-app-settings). Você pode nomear as configurações de qualquer maneira que desejar. Apenas certifique-se de que as referências do arquivo de configuração usem as mesmas chaves.

Os seguintes esgotamentos possíveis opções de configuração dentro do arquivo:

```json
{
    "platform": {
        "enabled": <true|false>
    },
    "globalValidation": {
        "unauthenticatedClientAction": "RedirectToLoginPage|AllowAnonymous|Return401|Return403",
        "redirectToProvider": "<default provider alias>",
        "excludedPaths": [
            "/path1",
            "/path2"
        ]
    },
    "httpSettings": {
        "requireHttps": <true|false>,
        "routes": {
            "apiPrefix": "<api prefix>"
        },
        "forwardProxy": {
            "convention": "NoProxy|Standard|Custom",
            "customHostHeaderName": "<host header value>",
            "customProtoHeaderName": "<proto header value>"
        }
    },
    "login": {
        "routes": {
            "logoutEndpoint": "<logout endpoint>"
        },
        "tokenStore": {
            "enabled": <true|false>,
            "tokenRefreshExtensionHours": "<double>",
            "fileSystem": {
                "directory": "<directory to store the tokens in if using a file system token store (default)>"
            },
            "azureBlobStorage": {
                "sasUrlSettingName": "<app setting name containing the sas url for the Azure Blob Storage if opting to use that for a token store>"
            }
        },
        "preserveUrlFragmentsForLogins": <true|false>,
        "allowedExternalRedirectUrls": [
            "https://uri1.azurewebsites.net/",
            "https://uri2.azurewebsites.net/",
            "url_scheme_of_your_app://easyauth.callback"
        ],
        "cookieExpiration": {
            "convention": "FixedTime|IdentityDerived",
            "timeToExpiration": "<timespan>"
        },
        "nonce": {
            "validateNonce": <true|false>,
            "nonceExpirationInterval": "<timespan>"
        }
    },
    "identityProviders": {
        "azureActiveDirectory": {
            "enabled": <true|false>,
            "registration": {
                "openIdIssuer": "<issuer url>",
                "clientId": "<app id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_AAD_SECRET",
            },
            "login": {
                "loginParameters": [
                    "paramName1=value1",
                    "paramName2=value2"
                ]
            },
            "validation": {
                "allowedAudiences": [
                    "audience1",
                    "audience2"
                ]
            }
        },
        "facebook": {
            "enabled": <true|false>,
            "registration": {
                "appId": "<app id>",
                "appSecretSettingName": "APP_SETTING_CONTAINING_FACEBOOK_SECRET"
            },
            "graphApiVersion": "v3.3",
            "login": {
                "scopes": [
                    "public_profile",
                    "email"
                ]
            },
        },
        "gitHub": {
            "enabled": <true|false>,
            "registration": {
                "clientId": "<client id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_GITHUB_SECRET"
            },
            "login": {
                "scopes": [
                    "profile",
                    "email"
                ]
            }
        },
        "google": {
            "enabled": true,
            "registration": {
                "clientId": "<client id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_GOOGLE_SECRET"
            },
            "login": {
                "scopes": [
                    "profile",
                    "email"
                ]
            },
            "validation": {
                "allowedAudiences": [
                    "audience1",
                    "audience2"
                ]
            }
        },
        "twitter": {
            "enabled": <true|false>,
            "registration": {
                "consumerKey": "<consumer key>",
                "consumerSecretSettingName": "APP_SETTING_CONTAINING TWITTER_CONSUMER_SECRET"
            }
        },
        "apple": {
            "enabled": <true|false>,
            "registration": {
                "clientId": "<client id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_APPLE_SECRET"
            },
            "login": {
                "scopes": [
                    "profile",
                    "email"
                ]
            }
        },
        "openIdConnectProviders": {
            "<providerName>": {
                "enabled": <true|false>,
                "registration": {
                    "clientId": "<client id>",
                    "clientCredential": {
                        "clientSecretSettingName": "<name of app setting containing client secret>"
                    },
                    "openIdConnectConfiguration": {
                        "authorizationEndpoint": "<url specifying authorization endpoint>",
                        "tokenEndpoint": "<url specifying token endpoint>",
                        "issuer": "<url specifying issuer>",
                        "certificationUri": "<url specifying jwks endpoint>",
                        "wellKnownOpenIdConfiguration": "<url specifying .well-known/open-id-configuration endpoint - if this property is set, the other properties of this object are ignored, and authorizationEndpoint, tokenEndpoint, issuer, and certificationUri are set to the corresponding values listed at this endpoint>"
                    }
                },
                "login": {
                    "nameClaimType": "<name of claim containing name>",
                    "scopes": [
                        "openid",
                        "profile",
                        "email"
                    ],
                    "loginParameterNames": [
                        "paramName1=value1",
                        "paramName2=value2"
                    ],
                }
            },
            //...
        }
    }
}
```

## <a name="pin-your-app-to-a-specific-authentication-runtime-version"></a>Fixar seu aplicativo em uma versão de tempo de execução de autenticação específica

Quando você habilita a autenticação/autorização, o middleware de plataforma é injetado em seu pipeline de solicitação HTTP, conforme descrito na [visão geral do recurso](overview-authentication-authorization.md#how-it-works). Esse middleware de plataforma é atualizado periodicamente com novos recursos e aprimoramentos como parte das atualizações de plataforma rotineiras. Por padrão, seu aplicativo Web ou de funções será executado na versão mais recente deste middleware de plataforma. Essas atualizações automáticas são sempre compatíveis com versões anteriores. No entanto, no caso raro que essa atualização automática apresente um problema em tempo de execução para seu aplicativo Web ou de funções, você pode reverter temporariamente para a versão de middleware anterior. Este artigo explica como fixar temporariamente um aplicativo em uma versão específica do middleware de autenticação.

### <a name="automatic-and-manual-version-updates"></a>Atualizações de versão automática e manual 

Você pode fixar seu aplicativo em uma versão específica do middleware da plataforma definindo uma `runtimeVersion` configuração para o aplicativo. Seu aplicativo sempre é executado na versão mais recente, a menos que você opte por fixá-lo explicitamente de volta a uma versão específica. Haverá algumas versões com suporte por vez. Se você fixar uma versão inválida que não tenha mais suporte, seu aplicativo usará a versão mais recente em vez disso. Para sempre executar a versão mais recente, defina `runtimeVersion` como ~ 1. 

### <a name="view-and-update-the-current-runtime-version"></a>Exibir e atualizar a versão de runtime atual

Você pode alterar a versão de tempo de execução usada pelo seu aplicativo. A nova versão de tempo de execução deve entrar em vigor após a reinicialização do aplicativo. 

#### <a name="view-the-current-runtime-version"></a>Exibir a versão de runtime atual

Você pode exibir a versão atual do middleware de autenticação de plataforma usando o CLI do Azure ou por meio de um dos pontos de extremidade HTTP da versão interna em seu aplicativo.

##### <a name="from-the-azure-cli"></a>Na CLI do Azure

Usando o CLI do Azure, exiba a versão de middleware atual com o comando [AZ webapp auth show](/cli/azure/webapp/auth#az-webapp-auth-show) .

```azurecli-interactive
az webapp auth show --name <my_app_name> \
--resource-group <my_resource_group>
```

Nesse código, substitua `<my_app_name>` pelo nome do seu aplicativo. Substitua também `<my_resource_group>` pelo nome do grupo de recursos para seu aplicativo.

Você verá o `runtimeVersion` campo na saída da CLI. Ele será semelhante ao seguinte exemplo de saída, que foi truncado para fins de clareza: 
```output
{
  "additionalLoginParams": null,
  "allowedAudiences": null,
    ...
  "runtimeVersion": "1.3.2",
    ...
}
```

##### <a name="from-the-version-endpoint"></a>Do ponto de extremidade de versão

Você também pode clicar em ponto de extremidade/.auth/Version em um aplicativo também para exibir a versão de middleware atual em que o aplicativo está sendo executado. Ele será semelhante ao seguinte exemplo de saída:
```output
{
"version": "1.3.2"
}
```

#### <a name="update-the-current-runtime-version"></a>Atualizar a versão de tempo de execução atual

Usando o CLI do Azure, você pode atualizar a `runtimeVersion` configuração no aplicativo com o comando [AZ webapp auth Update](/cli/azure/webapp/auth#az-webapp-auth-update) .

```azurecli-interactive
az webapp auth update --name <my_app_name> \
--resource-group <my_resource_group> \
--runtime-version <version>
```

Substitua `<my_app_name>` pelo nome do seu aplicativo. Substitua também `<my_resource_group>` pelo nome do grupo de recursos para seu aplicativo. Além disso, substitua `<version>` por uma versão válida do tempo de execução 1. x ou da `~1` versão mais recente. Você pode encontrar as notas de versão nas diferentes versões de tempo de execução [aqui] ( https://github.com/Azure/app-service-announcements) para ajudar a determinar a versão a ser fixada.

Execute esse comando a partir do [Azure Cloud Shell](../cloud-shell/overview.md) escolhendo **Experimentar** no exemplo de código anterior. Você também pode usar a [CLI do Azure localmente](/cli/azure/install-azure-cli) para executar esse comando após a execução de [az login](/cli/azure/reference-index#az-login) para entrar.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial: autenticar e autorizar usuários de ponta a ponta](tutorial-auth-aad.md)
