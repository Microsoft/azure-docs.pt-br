---
title: Gerenciamento de Exceções - Ferramenta de Modelagem de Ameaças da Microsoft - Azure | Microsoft Docs
description: Saiba mais sobre o gerenciamento de exceções no Threat Modeling Tool. Veja informações de mitigação e exiba exemplos de código.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.custom: devx-track-csharp
ms.openlocfilehash: 41532e554623c47e9728c6ccab92d99500e42021
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94517420"
---
# <a name="security-frame-exception-management--mitigations"></a>Quadro de segurança: Gerenciamento de exceções| Mitigações 
| Produto/Serviço | Artigo |
| --------------- | ------- |
| **WCF** | <ul><li>[WCF - não inclui o nó serviceDebug no arquivo de configuração](#servicedebug)</li><li>[WCF - não inclui o nó serviceMetadata no arquivo de configuração](#servicemetadata)</li></ul> |
| **API da Web** | <ul><li>[Verificar se o tratamento de exceções adequado é feito na ASP.NET Web API](#exception)</li></ul> |
| **Aplicativo Web** | <ul><li>[Não expor os detalhes da segurança nas mensagens de erro](#messages)</li><li>[Implementar a página de tratamento de erros Padrão](#default)</li><li>[Definir o Método de Implantação para o Varejo no IIS](#deployment)</li><li>[As exceções devem falhar com segurança](#fail)</li></ul> |

## <a name="wcf--do-not-include-servicedebug-node-in-configuration-file"></a><a id="servicedebug"></a>WCF - não inclui o nó serviceDebug no arquivo de configuração

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | Genérico, NET Framework 3 |
| **Atributos**              | N/D  |
| **Referências**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)), [Fortify Kingdom](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_debug_information) |
| **Etapas** | Os serviços do Windows Communication Framework (WCF) podem ser configurados para expor as informações de depuração. As informações de depuração não devem ser usadas nos ambientes de produção. A marcação `<serviceDebug>` define se o recurso das informações de depuração está habilitado para um serviço WCF. Se o atributo includeExceptionDetailInFaults for definido para true, as informações de exceção do aplicativo serão retornadas aos clientes. Os invasores podem aproveitar as informações adicionais que eles obtêm na saída da depuração para montar ataques direcionados na estrutura, banco de dados ou outros recursos usados pelo aplicativo. |

### <a name="example"></a>Exemplo
O arquivo de configuração a seguir inclui a marcação `<serviceDebug>`: 
```
<configuration> 
<system.serviceModel> 
<behaviors> 
<serviceBehaviors> 
<behavior name=""MyServiceBehavior""> 
<serviceDebug includeExceptionDetailInFaults=""True"" httpHelpPageEnabled=""True""/> 
... 
```
Desabilite as informações de depuração no serviço. Isso pode ser feito removendo a marcação `<serviceDebug>` do arquivo de configuração de seu aplicativo. 

## <a name="wcf--do-not-include-servicemetadata-node-in-configuration-file"></a><a id="servicemetadata"></a>WCF - não inclui o nó serviceMetadata no arquivo de configuração

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | Genérico, NET Framework 3 |
| **Referências**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)), [Fortify Kingdom](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_service_enumeration) |
| **Etapas** | Expor publicamente as informações sobre um serviço pode dar aos invasores ideias valiosas sobre como eles podem explorar o serviço. A marcação `<serviceMetadata>` habilita o recurso de publicação de metadados. Os metadados do serviço podem conter informações confidenciais e não devem ser acessíveis publicamente. No mínimo, apenas permita que usuários confiáveis acessem os metadados e verifique se informações desnecessárias não são expostas. Melhor ainda, desabilite totalmente a capacidade de publicar metadados. Uma configuração segura do WCF não conterá a marcação `<serviceMetadata>`. |

## <a name="ensure-that-proper-exception-handling-is-done-in-aspnet-web-api"></a><a id="exception"></a>Verificar se o tratamento de exceções adequado é feito na ASP.NET Web API

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | MVC 5, MVC 6 |
| **Atributos**              | N/D  |
| **Referências**              | [Tratamento de Exceções na ASP.NET Web API](https://www.asp.net/web-api/overview/error-handling/exception-handling), [Validação do Modelo na ASP.NET Web API](https://www.asp.net/web-api/overview/formats-and-model-binding/model-validation-in-aspnet-web-api) |
| **Etapas** | Por padrão, a maioria das exceções não identificadas na ASP.NET Web API é convertida em uma resposta HTTP com um código de status`500, Internal Server Error`|

### <a name="example"></a>Exemplo
Para controlar o código de status retornado pela API, `HttpResponseException` pode ser usado como mostrado abaixo: 
```csharp
public Product GetProduct(int id)
{
    Product item = repository.Get(id);
    if (item == null)
    {
        throw new HttpResponseException(HttpStatusCode.NotFound);
    }
    return item;
}
```

### <a name="example"></a>Exemplo
Para ter mais controle sobre a resposta da exceção, a classe `HttpResponseMessage` pode ser usada como mostrado abaixo: 
```csharp
public Product GetProduct(int id)
{
    Product item = repository.Get(id);
    if (item == null)
    {
        var resp = new HttpResponseMessage(HttpStatusCode.NotFound)
        {
            Content = new StringContent(string.Format("No product with ID = {0}", id)),
            ReasonPhrase = "Product ID Not Found"
        }
        throw new HttpResponseException(resp);
    }
    return item;
}
```
Para capturar as exceções sem tratamento que não são do tipo `HttpResponseException`, podem ser usados Filtros de Exceção. Os Filtros de Exceção implementam a interface `System.Web.Http.Filters.IExceptionFilter`. A maneira mais simples de escrever um filtro de exceção é derivar da classe `System.Web.Http.Filters.ExceptionFilterAttribute` e substituir o método OnException. 

### <a name="example"></a>Exemplo
Aqui está um filtro que converte as exceções `NotImplementedException` no código de status HTTP `501, Not Implemented`: 
```csharp
namespace ProductStore.Filters
{
    using System;
    using System.Net;
    using System.Net.Http;
    using System.Web.Http.Filters;

    public class NotImplExceptionFilterAttribute : ExceptionFilterAttribute 
    {
        public override void OnException(HttpActionExecutedContext context)
        {
            if (context.Exception is NotImplementedException)
            {
                context.Response = new HttpResponseMessage(HttpStatusCode.NotImplemented);
            }
        }
    }
}
```

Há várias maneiras de registrar um filtro de exceção da API Web:
- por ação
- pelo controlador
- globalmente

### <a name="example"></a>Exemplo
Para aplicar o filtro em uma ação específica, adicione o filtro como um atributo à ação: 
```csharp
public class ProductsController : ApiController
{
    [NotImplExceptionFilter]
    public Contact GetContact(int id)
    {
        throw new NotImplementedException("This method is not implemented");
    }
}
```
### <a name="example"></a>Exemplo
Para aplicar o filtro em todas as ações em um `controller`, adicione o filtro como um atributo à classe `controller`: 

```csharp
[NotImplExceptionFilter]
public class ProductsController : ApiController
{
    // ...
}
```

### <a name="example"></a>Exemplo
Para aplicar o filtro globalmente em todos os controladores da API Web, adicione uma instância do filtro à coleção `GlobalConfiguration.Configuration.Filters`. Os filtros de exceção nesta coleção aplicam-se a qualquer ação do controlador da API Web. 
```csharp
GlobalConfiguration.Configuration.Filters.Add(
    new ProductStore.NotImplExceptionFilterAttribute());
```

### <a name="example"></a>Exemplo
Para a validação do modelo, o estado do modelo pode ser passado para o método CreateErrorResponse como mostrado abaixo: 
```csharp
public HttpResponseMessage PostProduct(Product item)
{
    if (!ModelState.IsValid)
    {
        return Request.CreateErrorResponse(HttpStatusCode.BadRequest, ModelState);
    }
    // Implementation not shown...
}
```

Verificar os links na seção de referências para obter detalhes adicionais sobre o tratamento de exceção e a validação do modelo na ASP.NET Web API 

## <a name="do-not-expose-security-details-in-error-messages"></a><a id="messages"></a>Não expor os detalhes da segurança nas mensagens de erro

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas** | <p>As mensagens de erro genéricas são fornecidas diretamente para o usuário sem incluir os dados confidenciais do aplicativo. Exemplos de dados confidenciais:</p><ul><li>nomes do servidor</li><li>Cadeias de conexão</li><li>nomes de usuário</li><li>Senhas</li><li>procedimentos SQL</li><li>detalhes das falhas SQL dinâmicas</li><li>rastreamento de pilha e linhas de código</li><li>variáveis armazenadas na memória</li><li>locais da pasta e da unidade</li><li>pontos de instalação do aplicativo</li><li>definições de configuração do host</li><li>outros detalhes internos do aplicativo</li></ul><p>Interceptar todos os erros em um aplicativo e fornecer mensagens de erro genéricas, bem como habilitar os erros personalizados no IIS, ajudará a evitar a divulgação das informações. O banco de dados do SQL Server e o tratamento Exception do .NET, entre outras arquiteturas de tratamento de erros, são especialmente detalhados e extremamente úteis para um usuário mal-intencionado que cria o perfil de seu aplicativo. Não exiba diretamente o conteúdo de uma classe derivada da classe Exception do .NET e verifique se você tem o tratamento adequado das exceções para que uma exceção inesperada não seja gerada sem querer diretamente para o usuário.</p><ul><li>Fornecer mensagens de erro genéricas diretamente para o usuário que abstrai os detalhes específicos encontrados diretamente na mensagem de erro/exceção</li><li>Não exibir o conteúdo de uma classe exception do .NET diretamente para o usuário</li><li>Interceptar todas as mensagens de erro e se apropriado, informar ao usuário por meio de uma mensagem de erro genérica enviada ao cliente do aplicativo</li><li>Não exponha o conteúdo da classe Exception diretamente para o usuário, especialmente o valor de retorno de `.ToString()` ou os valores das propriedades Message ou StackTrace. Registrar essas informações com segurança e exibir uma mensagem mais inofensiva para o usuário</li></ul>|

## <a name="implement-default-error-handling-page"></a><a id="default"></a>Implementar a página de tratamento de erros Padrão

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Caixa de Diálogo Editar Configurações das Páginas de Erro do ASP.NET](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd569096(v=ws.10)) |
| **Etapas** | <p>Quando um aplicativo ASP.NET falhar e fizer com que um Erro Interno do Servidor HTTP/1.x 500 ou uma configuração do recurso (por exemplo, Filtragem da Solicitação) impeça que uma página seja exibida, uma mensagem de erro será gerada. Os administradores podem escolher se o aplicativo deve exibir ou não uma mensagem amigável para o cliente, mensagem de erro detalhada para o cliente ou mensagem de erro detalhada para o localhost apenas. A marcação `<customErrors>` no web.config tem três modos:</p><ul><li>**Ativado:** Especifica que os erros personalizados estão habilitados. Se nenhum atributo defaultRedirect for especificado, os usuários verão um erro genérico. Os erros personalizados são mostrados para os clientes remotos e para o host local</li><li>**Desativado:** Especifica que os erros personalizados estão desabilitados. Os erros detalhados do ASP.NET são mostrados para os clientes remotos e o host local</li><li>**RemoteOnly:** Especifica que os erros personalizados são mostrados apenas para os clientes remotos e que os erros do ASP.NET são mostrados para o host local. Esse é o valor padrão.</li></ul><p>Abra o arquivo `web.config` do aplicativo/site e verifique se a marcação foi `<customErrors mode="RemoteOnly" />` ou `<customErrors mode="On" />` definido.</p>|

## <a name="set-deployment-method-to-retail-in-iis"></a><a id="deployment"></a>Definir o Método de Implantação para o Varejo no IIS

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Implantação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [implantação Element (Esquema de Configurações do ASP.NET)](/previous-versions/dotnet/netframework-2.0/ms228298(v=vs.80)) |
| **Etapas** | <p>O argumento `<deployment retail>` é para ser usado pelos servidores IIS de produção. Esse argumento é usado para ajudar os aplicativos a serem executados com o melhor desempenho possível e o mínimo de vazamento de informações de segurança desabilitando a capacidade dele de gerar a saída de rastreamento em uma página, desabilitando a capacidade de exibir mensagens de erro detalhadas para os usuários finais e desativando o argumento debug.</p><p>Muitas vezes, os argumentos e as opções que estão voltados para os desenvolvedores, como um rastreamento da solicitação e depuração com falha, são habilitados durante o desenvolvimento ativo. É recomendável que o método de implantação, em qualquer servidor de produção, seja definido para varejo. abra o arquivo machine.config e verifique se `<deployment retail="true" />` permanece definido para true.</p>|

## <a name="exceptions-should-fail-safely"></a><a id="fail"></a>As exceções devem falhar com segurança

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Falhar com segurança](https://owasp.org/www-community/Fail_securely) |
| **Etapas** | O aplicativo deve falhar com segurança. Qualquer método que retorna um valor booliano, com base em qual determinada decisão é tomada, deve ter um bloco de exceção criado cuidadosamente. Há muitos erros lógicos devido a problemas de segurança que passam quando o bloco de exceção é escrito com negligência.|

### <a name="example"></a>Exemplo
```csharp
        public static bool ValidateDomain(string pathToValidate, Uri currentUrl)
        {
            try
            {
                if (!string.IsNullOrWhiteSpace(pathToValidate))
                {
                    var domain = RetrieveDomain(currentUrl);
                    var replyPath = new Uri(pathToValidate);
                    var replyDomain = RetrieveDomain(replyPath);

                    if (string.Compare(domain, replyDomain, StringComparison.OrdinalIgnoreCase) != 0)
                    {
                        //// Adding additional check to enable CMS urls if they are not hosted on same domain.
                        if (!string.IsNullOrWhiteSpace(Utilities.CmsBase))
                        {
                            var cmsDomain = RetrieveDomain(new Uri(Utilities.Base.Trim()));
                            if (string.Compare(cmDomain, replyDomain, StringComparison.OrdinalIgnoreCase) != 0)
                            {
                                return false;
                            }
                            else
                            {
                                return true;
                            }
                        }

                        return false;
                    }
                }

                return true;
            }
            catch (UriFormatException ex)
            {
                LogHelper.LogException("Utilities:ValidateDomain", ex);
                return true;
            }
        }
```
O método acima sempre retornará True se ocorrer uma exceção. Se o usuário final fornecer uma URL malformada, que o navegador respeita, mas o construtor `Uri()` não, isso irá gerar uma exceção e a vítima será levada para uma URL válida, mas malformada.