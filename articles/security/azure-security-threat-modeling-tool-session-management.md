---
title: Gerenciamento de sessão - Ferramenta de Modelagem de Ameaças da Microsoft - Azure | Microsoft Docs
description: atenuações de ameaças expostas na ferramenta de modelagem de ameaças
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: e8f3cf3889b3f79e930630ff0e768a0c4875eec6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60611697"
---
# <a name="security-frame-session-management"></a>Quadro de segurança: Gerenciamento da sessão
| Produto/Serviço | Artigo |
| --------------- | ------- |
| **Azure AD**    | <ul><li>[Implementar o logoff apropriado usando métodos ADAL ao usar o Azure AD](#logout-adal)</li></ul> |
| Dispositivo IoT | <ul><li>[Usar tempos de vida finitos para tokens SaS gerados](#finite-tokens)</li></ul> |
| **Azure DocumentDB** | <ul><li>[Usar tempos de vida mínimos de tokens para tokens de Recurso gerados](#resource-tokens)</li></ul> |
| **ADFS** | <ul><li>[Implementar o logoff apropriado usando métodos WsFederation ao usar o ADFS](#wsfederation-logout)</li></ul> |
| **Identity Server** | <ul><li>[Implementar o logoff adequado ao usar o Servidor de identidade](#proper-logout)</li></ul> |
| **Aplicativo Web** | <ul><li>[Os aplicativos disponíveis via HTTPS devem usar cookies seguros](#https-secure-cookies)</li><li>[Todo aplicativo baseado em http deve especificar http somente para definição de cookie](#cookie-definition)</li><li>[Atenuar ataques CSRF (solicitação intersite forjada) em páginas Web ASP.NET](#csrf-asp)</li><li>[Configurar sessão para tempo de vida de inatividade](#inactivity-lifetime)</li><li>[Implementar o logoff apropriado do aplicativo](#proper-app-logout)</li></ul> |
| **API da Web** | <ul><li>[Atenuar ataques CSRF (solicitação intersite forjada) em APIs da Web ASP.NET](#csrf-api)</li></ul> |

## <a id="logout-adal"></a>Implementar o logoff apropriado usando métodos ADAL ao usar o Azure AD

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | AD do Azure | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas** | Se o aplicativo depende do token de acesso emitido pelo Azure AD, o manipulador de evento de logoff deverá chamar |

### <a name="example"></a>Exemplo
```csharp
HttpContext.GetOwinContext().Authentication.SignOut(OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType)
```

### <a name="example"></a>Exemplo
Também deve destruir a sessão do usuário chamando o método Session.Abandon(). O método a seguir mostra a implementação segura do logoff do usuário:
```csharp
    [HttpPost]
        [ValidateAntiForgeryToken]
        public void LogOff()
        {
            string userObjectID = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
            AuthenticationContext authContext = new AuthenticationContext(Authority + TenantId, new NaiveSessionCache(userObjectID));
            authContext.TokenCache.Clear();
            Session.Clear();
            Session.Abandon();
            Response.SetCookie(new HttpCookie("ASP.NET_SessionId", string.Empty));
            HttpContext.GetOwinContext().Authentication.SignOut(
                OpenIdConnectAuthenticationDefaults.AuthenticationType,
                CookieAuthenticationDefaults.AuthenticationType);
        } 
```

## <a id="finite-tokens"></a>Usar tempos de vida finitos para tokens SaS gerados

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dispositivo IoT | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas** | Tokens SaS gerados para autenticar no Hub IoT do Azure devem ter um período de expiração finito. Mantenha o tempo de vida do token SaS com um valor mínimo a fim de limitar o tempo que ele pode ser reproduzido em caso de comprometimento do token.|

## <a id="resource-tokens"></a>Usar tempos de vida mínimos de tokens para tokens de Recurso gerados

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Azure Document DB | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas** | Reduza o intervalo de tempo do token de recurso para um valor mínimo necessário. Tokens de recurso têm um intervalo de tempo válido padrão de uma hora.|

## <a id="wsfederation-logout"></a>Implementar o logoff apropriado usando métodos WsFederation ao usar o ADFS

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | ADFS | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas** | Se o aplicativo depender do token de STS emitido pelo ADFS, o manipulador de eventos de logoff deverá chamar o método WSFederationAuthenticationModule.FederatedSignOut() para fazer logoff do usuário. Além disso, a sessão atual deve ser destruída, e o valor do token da sessão deve ser redefinido e tornado nulo.|

### <a name="example"></a>Exemplo
```csharp
        [HttpPost, ValidateAntiForgeryToken]
        [Authorization]
        public ActionResult SignOut(string redirectUrl)
        {
            if (!this.User.Identity.IsAuthenticated)
            {
                return this.View("LogOff", null);
            }

            // Removes the user profile.
            this.Session.Clear();
            this.Session.Abandon();
            HttpContext.Current.Response.Cookies.Add(new System.Web.HttpCookie("ASP.NET_SessionId", string.Empty)
                {
                    Expires = DateTime.Now.AddDays(-1D),
                    Secure = true,
                    HttpOnly = true
                });

            // Signs out at the specified security token service (STS) by using the WS-Federation protocol.
            Uri signOutUrl = new Uri(FederatedAuthentication.WSFederationAuthenticationModule.Issuer);
            Uri replyUrl = new Uri(FederatedAuthentication.WSFederationAuthenticationModule.Realm);
            if (!string.IsNullOrEmpty(redirectUrl))
            {
                replyUrl = new Uri(FederatedAuthentication.WSFederationAuthenticationModule.Realm + redirectUrl);
            }
           //     Signs out of the current session and raises the appropriate events.
            var authModule = FederatedAuthentication.WSFederationAuthenticationModule;
            authModule.SignOut(false);
        //     Signs out at the specified security token service (STS) by using the WS-Federation
        //     protocol.            
            WSFederationAuthenticationModule.FederatedSignOut(signOutUrl, replyUrl);
            return new RedirectResult(redirectUrl);
        }
```

## <a id="proper-logout"></a>Implementar o logoff adequado ao usar o Servidor de identidade

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Servidor de Identidade | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Saída do IdentityServer3-Federated](https://identityserver.github.io/Documentation/docsv2/advanced/federated-signout.html) |
| **Etapas** | O IdentityServer oferece suporte à federação com provedores de identidade externos. Quando um usuário sai de um provedor de identidade upstream, dependendo do protocolo usado, talvez seja possível receber uma notificação quando o usuário se desconectar. Isso permite que o IdentityServer notifique seus clientes para que eles também possam desconectar o usuário. Consulte a documentação na seção de referências para obter detalhes sobre a implementação.|

## <a id="https-secure-cookies"></a>Os aplicativos disponíveis via HTTPS devem usar cookies seguros

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | EnvironmentType - OnPrem |
| **Referências**              | [httpCookies Element (Esquema de configurações ASP.NET)](https://msdn.microsoft.com/library/ms228262(v=vs.100).aspx), [Propriedade HttpCookie.Secure](https://msdn.microsoft.com/library/system.web.httpcookie.secure.aspx) |
| **Etapas** | Normalmente, os cookies só são acessíveis ao domínio para o qual foram definidos. Infelizmente, a definição de "domínio" não inclui o protocolo para que cookies criados por HTTPS sejam acessíveis via HTTP. O atributo "secure" indica para o navegador que o cookie deve ser disponibilizado apenas por HTTPS. Certifique-se de que todos os cookies definidos por HTTPS usem o atributo **secure**. Esse requisito pode ser imposto no arquivo web.config pela configuração do atributo requireSSL como true. Essa é a abordagem preferencial, pois vai impor o atributo **secure** a todos os cookies atuais e futuros, sem a necessidade de fazer qualquer alteração adicional no código.|

### <a name="example"></a>Exemplo
```csharp
<configuration>
  <system.web>
    <httpCookies requireSSL="true"/>
  </system.web>
</configuration>
```
A configuração é aplicada mesmo que HTTP seja usado para acessar o aplicativo. Se HTTP é usado para acessar o aplicativo, a configuração interrompe o aplicativo, pois os cookies estão definidos com o atributo secure e o navegador não os enviará de volta ao aplicativo.

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Web Forms, MVC5 |
| **Atributos**              | EnvironmentType - OnPrem |
| **Referências**              | N/D  |
| **Etapas** | Quando o aplicativo Web for a terceira parte confiável, e o IdP for o servidor ADFS, o atributo secure do token FedAuth poderá ser configurado definindo requireSSL como True na seção `system.identityModel.services` de web.config:|

### <a name="example"></a>Exemplo
```csharp
  <system.identityModel.services>
    <federationConfiguration>
      <!-- Set requireSsl=true; domain=application domain name used by FedAuth cookies (Ex: .gdinfra.com); -->
      <cookieHandler requireSsl="true" persistentSessionLifetime="0.0:20:0" />
    ....  
    </federationConfiguration>
  </system.identityModel.services>
```

## <a id="cookie-definition"></a>Todo aplicativo baseado em http deve especificar http somente para definição de cookie

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Atributo Secure Cookie](https://en.wikipedia.org/wiki/HTTP_cookie#Secure_cookie) |
| **Etapas** | Para atenuar o risco de divulgação de informações com um ataque XSS (script entre sites), um novo atributo - httpOnly - foi introduzido aos cookies e é suportado por todos os principais navegadores. O atributo especifica que um cookie não pode ser acessado por script. Usando cookies HttpOnly, um aplicativo Web reduz a possibilidade de roubo de informações confidenciais contidas no cookie por meio de script e o envio dessas informações ao site de um invasor. |

### <a name="example"></a>Exemplo
Todos os aplicativos baseados em HTTP que usam cookies devem especificar HttpOnly na definição de cookie implementando a seguinte configuração em web.config:
```XML
<system.web>
.
.
   <httpCookies requireSSL="false" httpOnlyCookies="true"/>
.
.
</system.web>
```

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Formulários da Web |
| **Atributos**              | N/D  |
| **Referências**              | [Propriedade FormsAuthentication.RequireSSL](https://msdn.microsoft.com/library/system.web.security.formsauthentication.requiressl.aspx) |
| **Etapas** | O valor da propriedade RequireSSL é definido no arquivo de configuração para um aplicativo ASP.NET usando o atributo requireSSL do elemento de configuração. Especifique no arquivo web.config para seu aplicativo ASP.NET se o protocolo SSL (Secure Sockets Layer) é necessário para retornar o cookie de autenticação de formulários para o servidor configurando o atributo requireSSL.|

### <a name="example"></a>Exemplo 
O exemplo de código a seguir define o atributo requireSSL no arquivo Web.config.
```XML
<authentication mode="Forms">
  <forms loginUrl="member_login.aspx" cookieless="UseCookies" requireSSL="true"/>
</authentication>
```

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | MVC5 |
| **Atributos**              | EnvironmentType - OnPrem |
| **Referências**              | [Configuração do Windows Identity Foundation (WIF) – Part II](https://blogs.msdn.microsoft.com/alikl/2011/02/01/windows-identity-foundation-wif-configuration-part-ii-cookiehandler-chunkedcookiehandler-customcookiehandler/) |
| **Etapas** | Para definir o atributo httpOnly para cookies FedAuth, o valor do atributo hideFromCsript deve ser definido como True. |

### <a name="example"></a>Exemplo
A configuração a seguir mostra a configuração correta:
```XML
<federatedAuthentication>
<cookieHandler mode="Custom"
                       hideFromScript="true"
                       name="FedAuth"
                       path="/"
                       requireSsl="true"
                       persistentSessionLifetime="25">
</cookieHandler>
</federatedAuthentication>
```

## <a id="csrf-asp"></a>Atenuar ataques CSRF (solicitação intersite forjada) em páginas Web ASP.NET

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas** | A CSRF ou XSRF (solicitação intersite forjada) é um tipo de ataque em que um invasor pode realizar ações no contexto de segurança da sessão estabelecida de um usuário diferente em um site da Web. A meta é modificar ou excluir o conteúdo, caso o site de destino dependa exclusivamente de cookies de sessão para autenticar a solicitação recebida. Um invasor pode explorar essa vulnerabilidade fazendo com que um navegador diferente de um usuário carregue uma URL com um comando de um site vulnerável no qual o usuário já esteja conectado. Há muitas maneiras de um invasor fazer isso, como hospedando um site diferente que carrega um recurso do servidor vulnerável ou fazendo o usuário clicar em um link. Esse ataque pode ser evitado se o servidor enviar um token adicional ao cliente, exigir que o cliente inclua esse token em todas as solicitações futuras e verificar se todas as solicitações futuras incluem um token que pertence à sessão atual, por exemplo, usando o ASP.NET AntiForgeryToken ou ViewState. |

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | MVC5, MVC6 |
| **Atributos**              | N/D  |
| **Referências**              | [Prevenção de XSRF/CSRF no ASP.NET MVC e páginas Web](https://www.asp.net/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) |
| **Etapas** | Formulários MVC Anti-CSRF e do ASP.NET – Use o método auxiliar `AntiForgeryToken` em Exibições; coloque um `Html.AntiForgeryToken()` no formulário, por exemplo,|

### <a name="example"></a>Exemplo
```csharp
@using (Html.BeginForm("UserProfile", "SubmitUpdate")) { 
    @Html.ValidationSummary(true) 
    @Html.AntiForgeryToken()
    <fieldset> 
```

### <a name="example"></a>Exemplo
```csharp
<form action="/UserProfile/SubmitUpdate" method="post">
    <input name="__RequestVerificationToken" type="hidden" value="saTFWpkKN0BYazFtN6c4YbZAmsEwG0srqlUqqloi/fVgeV2ciIFVmelvzwRZpArs" />
    <!-- rest of form goes here -->
</form>
```

### <a name="example"></a>Exemplo
Ao mesmo tempo, Html.AntiForgeryToken() fornece ao visitante um cookie chamado __RequestVerificationToken, com o mesmo valor que o valor oculto aleatório mostrado acima. Em seguida, para validar uma postagem de formulário de entrada, adicione o filtro [ValidateAntiForgeryToken] ao método de ação de destino. Por exemplo: 
```
[ValidateAntiForgeryToken]
public ViewResult SubmitUpdate()
{
// ... etc.
}
```
Filtro de autorização que verifica se:
* A solicitação de entrada tem um cookie chamado __RequestVerificationToken
* A solicitação de entrada tem um `Request.Form` chamado __RequestVerificationToken
* O cookie e o valor `Request.Form` são correspondentes. Supondo que tudo esteja certo, a solicitação passará normalmente. Mas, se não estiver, uma falha de autorização com a mensagem "Um token antifalsificação necessário não foi fornecido ou era inválido". 

### <a name="example"></a>Exemplo
Anti-CSRF e AJAX: O token de formulário pode ser um problema para solicitações AJAX, pois uma solicitação AJAX pode enviar dados JSON, não os dados de formulário HTML. Uma solução é enviar os tokens em um cabeçalho HTTP personalizado. O código a seguir usa a sintaxe Razor para gerar os tokens e adiciona os tokens a uma solicitação AJAX. 
```csharp
<script>
    @functions{
        public string TokenHeaderValue()
        {
            string cookieToken, formToken;
            AntiForgery.GetTokens(null, out cookieToken, out formToken);
            return cookieToken + ":" + formToken;                
        }
    }

    $.ajax("api/values", {
        type: "post",
        contentType: "application/json",
        data: {  }, // JSON data goes here
        dataType: "json",
        headers: {
            'RequestVerificationToken': '@TokenHeaderValue()'
        }
    });
</script>
```

### <a name="example"></a>Exemplo
Ao processar a solicitação, extraia os tokens do cabeçalho da solicitação. Em seguida, chame o método AntiForgery.Validate para validar os tokens. O método Validate lança uma exceção se os tokens não forem válidos.
```csharp
void ValidateRequestHeader(HttpRequestMessage request)
{
    string cookieToken = "";
    string formToken = "";

    IEnumerable<string> tokenHeaders;
    if (request.Headers.TryGetValues("RequestVerificationToken", out tokenHeaders))
    {
        string[] tokens = tokenHeaders.First().Split(':');
        if (tokens.Length == 2)
        {
            cookieToken = tokens[0].Trim();
            formToken = tokens[1].Trim();
        }
    }
    AntiForgery.Validate(cookieToken, formToken);
}
```

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Formulários da Web |
| **Atributos**              | N/D  |
| **Referências**              | [Tirar proveito dos recursos internos do ASP.NET para afastar os ataques na Web](https://msdn.microsoft.com/library/ms972969.aspx#securitybarriers_topic2) |
| **Etapas** | Os ataques CSRF em aplicativos baseados em WebForm podem ser minimizados definindo ViewStateUserKey para uma cadeia de caracteres aleatória que varia para cada usuário - ID de usuário ou, melhor ainda, ID de sessão. Por diversos motivos técnicos e sociais, ID de sessão é uma opção bem melhor, pois é imprevisível, atinge um tempo limite e varia de acordo com o usuário.|

### <a name="example"></a>Exemplo
Aqui está o código necessário em todas as suas páginas:
```csharp
void Page_Init (object sender, EventArgs e) {
   ViewStateUserKey = Session.SessionID;
   :
}
```

## <a id="inactivity-lifetime"></a>Configurar sessão para tempo de vida de inatividade

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Propriedade HttpSessionState.Timeout](https://msdn.microsoft.com/library/system.web.sessionstate.httpsessionstate.timeout(v=vs.110).aspx) |
| **Etapas** | Tempo limite da sessão representa o evento que ocorre quando um usuário não realiza qualquer ação em um site da web durante um intervalo (definido pelo servidor web). O evento, no lado do servidor, altera o status da sessão do usuário para "inválida" (por exemplo, "não mais utilizada") e instrui o servidor Web a destruí-la (excluindo todos os dados contidos nela). O exemplo de código a seguir define o atributo de tempo limite de sessão como 15 minutos no arquivo Web.config.|

### <a name="example"></a>Exemplo
```XML 
<configuration>
  <system.web>
    <sessionState mode="InProc" cookieless="true" timeout="15" />
  </system.web>
</configuration>
```

## <a id="threat-detection"></a>Ativar a detecção de ameaças no SQL Azure

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Formulários da Web |
| **Atributos**              | N/D  |
| **Referências**              | [Elemento Forms para autenticação (ASP.NET Settings Schema)](https://msdn.microsoft.com/library/1d3t3c61(v=vs.100).aspx) |
| **Etapas** | Defina o tempo limite de cookie do tíquete de autenticação de formulários como 15 minutos|

### <a name="example"></a>Exemplo
```XML
<forms  name=".ASPXAUTH" loginUrl="login.aspx"  defaultUrl="default.aspx" protection="All" timeout="15" path="/" requireSSL="true" slidingExpiration="true"/>
</forms>
```

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Web Forms, MVC5 |
| **Atributos**              | EnvironmentType - OnPrem |
| **Referências**              | [asdeqa](https://skf.azurewebsites.net/Mitigations/Details/wefr) |
| **Etapas** | Quando o aplicativo da Web for Relying Party e o ADFS for o STS, a vida útil dos cookies de autenticação - tokens FedAuth - pode ser definida pela seguinte configuração no web.config:|

### <a name="example"></a>Exemplo
```XML
  <system.identityModel.services>
    <federationConfiguration>
      <!-- Set requireSsl=true; domain=application domain name used by FedAuth cookies (Ex: .gdinfra.com); -->
      <cookieHandler requireSsl="true" persistentSessionLifetime="0.0:15:0" />
      <!-- Set requireHttps=true; -->
      <wsFederation passiveRedirectEnabled="true" issuer="http://localhost:39529/" realm="https://localhost:44302/" reply="https://localhost:44302/" requireHttps="true"/>
      <!--
      Use the code below to enable encryption-decryption of claims received from ADFS. Thumbprint value varies based on the certificate being used.
      <serviceCertificate>
        <certificateReference findValue="4FBBBA33A1D11A9022A5BF3492FF83320007686A" storeLocation="LocalMachine" storeName="My" x509FindType="FindByThumbprint" />
      </serviceCertificate>
      -->
    </federationConfiguration>
  </system.identityModel.services>
```

### <a name="example"></a>Exemplo
Além disso, o tempo de vida do token da declaração SAML emitida pelo ADFS deve ser definido como 15 minutos, executando o seguinte comando powershell no servidor ADFS:
```csharp
Set-ADFSRelyingPartyTrust -TargetName “<RelyingPartyWebApp>” -ClaimsProviderName @(“Active Directory”) -TokenLifetime 15 -AlwaysRequireAuthentication $true
```

## <a id="proper-app-logout"></a>Implementar o logoff apropriado do aplicativo

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas** | Execute uma Saída adequada do aplicativo, quando o usuário pressionar o botão logoff. Após o logoff, o aplicativo deve destruir a sessão do usuário e também redefinir e anular o valor do cookie de sessão, juntamente com a redefinição e anulação do valor do cookie de autenticação. Além disso, quando várias sessões estiverem vinculadas a uma única identidade de usuário, elas deverão ser coletivamente encerradas no lado do servidor no momento do tempo limite ou no logoff. Por fim, certifique-se de que a funcionalidade de Logoff esteja disponível em cada página. |

## <a id="csrf-api"></a>Atenuar ataques CSRF (solicitação intersite forjada) em APIs da Web ASP.NET

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas** | A CSRF ou XSRF (solicitação intersite forjada) é um tipo de ataque em que um invasor pode realizar ações no contexto de segurança da sessão estabelecida de um usuário diferente em um site da Web. A meta é modificar ou excluir o conteúdo, caso o site de destino dependa exclusivamente de cookies de sessão para autenticar a solicitação recebida. Um invasor pode explorar essa vulnerabilidade fazendo com que um navegador diferente de um usuário carregue uma URL com um comando de um site vulnerável no qual o usuário já esteja conectado. Há muitas maneiras de um invasor fazer isso, como hospedando um site diferente que carrega um recurso do servidor vulnerável ou fazendo o usuário clicar em um link. Esse ataque pode ser evitado se o servidor enviar um token adicional ao cliente, exigir que o cliente inclua esse token em todas as solicitações futuras e verificar se todas as solicitações futuras incluem um token que pertence à sessão atual, por exemplo, usando o ASP.NET AntiForgeryToken ou ViewState. |

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | MVC5, MVC6 |
| **Atributos**              | N/D  |
| **Referências**              | [Impedir ataques CSRF (solicitação intersite forjada) em APIs da Web ASP.NET](https://www.asp.net/web-api/overview/security/preventing-cross-site-request-forgery-csrf-attacks) |
| **Etapas** | Anti-CSRF e AJAX: O token de formulário pode ser um problema para solicitações AJAX, pois uma solicitação AJAX pode enviar dados JSON, não os dados de formulário HTML. Uma solução é enviar os tokens em um cabeçalho HTTP personalizado. O código a seguir usa a sintaxe Razor para gerar os tokens e adiciona os tokens a uma solicitação AJAX. |

### <a name="example"></a>Exemplo
```Javascript
<script>
    @functions{
        public string TokenHeaderValue()
        {
            string cookieToken, formToken;
            AntiForgery.GetTokens(null, out cookieToken, out formToken);
            return cookieToken + ":" + formToken;                
        }
    }
    $.ajax("api/values", {
        type: "post",
        contentType: "application/json",
        data: {  }, // JSON data goes here
        dataType: "json",
        headers: {
            'RequestVerificationToken': '@TokenHeaderValue()'
        }
    });
</script>
```

### <a name="example"></a>Exemplo
Ao processar a solicitação, extraia os tokens do cabeçalho da solicitação. Em seguida, chame o método AntiForgery.Validate para validar os tokens. O método Validate lança uma exceção se os tokens não forem válidos.
```csharp
void ValidateRequestHeader(HttpRequestMessage request)
{
    string cookieToken = "";
    string formToken = "";

    IEnumerable<string> tokenHeaders;
    if (request.Headers.TryGetValues("RequestVerificationToken", out tokenHeaders))
    {
        string[] tokens = tokenHeaders.First().Split(':');
        if (tokens.Length == 2)
        {
            cookieToken = tokens[0].Trim();
            formToken = tokens[1].Trim();
        }
    }
    AntiForgery.Validate(cookieToken, formToken);
}
```

### <a name="example"></a>Exemplo
Formulários MVC Anti-CSRF e do ASP.NET – Use o método auxiliar AntiForgeryToken em Exibições; coloque um Html.AntiForgeryToken() no formulário, por exemplo,
```csharp
@using (Html.BeginForm("UserProfile", "SubmitUpdate")) { 
    @Html.ValidationSummary(true) 
    @Html.AntiForgeryToken()
    <fieldset> 
}
```

### <a name="example"></a>Exemplo
O exemplo acima produzirá algo como o seguinte:
```csharp
<form action="/UserProfile/SubmitUpdate" method="post">
    <input name="__RequestVerificationToken" type="hidden" value="saTFWpkKN0BYazFtN6c4YbZAmsEwG0srqlUqqloi/fVgeV2ciIFVmelvzwRZpArs" />
    <!-- rest of form goes here -->
</form>
```

### <a name="example"></a>Exemplo
Ao mesmo tempo, Html.AntiForgeryToken() fornece ao visitante um cookie chamado __RequestVerificationToken, com o mesmo valor que o valor oculto aleatório mostrado acima. Em seguida, para validar uma postagem de formulário de entrada, adicione o filtro [ValidateAntiForgeryToken] ao método de ação de destino. Por exemplo: 
```
[ValidateAntiForgeryToken]
public ViewResult SubmitUpdate()
{
// ... etc.
}
```
Filtro de autorização que verifica se:
* A solicitação de entrada tem um cookie chamado __RequestVerificationToken
* A solicitação de entrada tem um `Request.Form` chamado __RequestVerificationToken
* O cookie e o valor `Request.Form` são correspondentes. Supondo que tudo esteja certo, a solicitação passará normalmente. Mas, se não estiver, uma falha de autorização com a mensagem "Um token antifalsificação necessário não foi fornecido ou era inválido".

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | MVC5, MVC6 |
| **Atributos**              | Provedor de Identidade - ADFS, Provedor de Identidade - Azure AD |
| **Referências**              | [Proteger uma API Web com contas individuais e logon local na ASP.NET Web API 2.2](https://www.asp.net/web-api/overview/security/individual-accounts-in-web-api) |
| **Etapas** | Se a API Web for protegida usando OAuth 2.0, ela esperará um token de portador no cabeçalho de solicitação de Autorização e a concederá o acesso à solicitação somente se o token for válido. Diferentemente da autenticação baseada em cookie, os navegadores não anexam os tokens de portador às solicitações. O cliente solicitante deve anexar explicitamente o token de portador no cabeçalho da solicitação. Portanto, para APIs Web ASP.NET protegidas usando o OAuth 2.0, os tokens de portador são considerados uma defesa contra ataques de CSRF. Observe que, se a parte MVC do aplicativo usar a autenticação de formulários (ou seja, cookies), será necessário usar tokens antifalsificação pelo aplicativo Web do MVC. |

### <a name="example"></a>Exemplo
A API Web precisa ser informada para confiar SOMENTE em tokens de portador e não em cookies. Isso pode ser feito pela configuração a seguir no `WebApiConfig.Register` método:

```csharp
config.SuppressDefaultHostAuthentication();
config.Filters.Add(new HostAuthenticationFilter(OAuthDefaults.AuthenticationType));
```

O método SuppressDefaultHostAuthentication informa à API da Web para ignorar qualquer autenticação que ocorra antes que a solicitação chegue ao pipeline da API da Web, seja pelo IIS ou pelo middleware OWIN. Dessa forma, podemos restringir a API da Web para autenticar somente usando tokens de portador.
