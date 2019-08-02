---
title: Diagnosticar falhas e exceções em aplicativos Web com o Azure Application Insights | Microsoft Docs
description: Capture exceções de aplicativos do ASP.NET junto com a telemetria de solicitação.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: d1e98390-3ce4-4d04-9351-144314a42aa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/11/2019
ms.author: mbullwin
ms.openlocfilehash: c8d46ddc834cb12aa63720673c83d745ab53ab4d
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68226872"
---
# <a name="diagnose-exceptions-in-your-web-apps-with-application-insights"></a>Diagnosticar exceções em seus aplicativos Web com o Application Insights
Exceções em seu aplicativo Web ao vivo são relatadas pelo [Application Insights](../../azure-monitor/app/app-insights-overview.md). Você pode correlacionar solicitações com falha com exceções e outros eventos no cliente e no servidor, para poder diagnosticar as causas rapidamente.

## <a name="set-up-exception-reporting"></a>Configurar os relatórios de exceção
* Para que as exceções sejam relatadas em seu aplicativo de servidor:
  * Aplicativos Web do Azure: Excluir a extensão do [Application Insights](../../azure-monitor/app/azure-web-apps.md)
  * VM do Azure e conjunto de dimensionamento de máquinas virtuais do Azure aplicativos hospedados pelo IIS: Adicionar a [extensão de monitoramento de aplicativo](../../azure-monitor/app/azure-vm-vmss-apps.md)
  * Instale o[ SDK do Application Insights](../../azure-monitor/app/asp-net.md) no aplicativo ou
  * Servidores web IIS: Execute [Application Insights Agent](../../azure-monitor/app/monitor-performance-live-website-now.md); ou
  * Aplicativos Web Java: Instale o [Agente Java](../../azure-monitor/app/java-agent.md)
* Instale o [snippet de JavaScript](../../azure-monitor/app/javascript.md) em suas páginas da Web para capturar exceções de navegador.
* Em algumas estruturas de aplicativo ou com algumas configurações, você precisa executar algumas etapas adicionais para capturar mais exceções:
  * [Formulários da Web](#web-forms)
  * [MVC](#mvc)
  * [API Web 1.*](#web-api-1x)
  * [API Web 2.*](#web-api-2x)
  * [WCF](#wcf)

## <a name="diagnosing-exceptions-using-visual-studio"></a>Diagnosticar exceções usando o Visual Studio
Abra a solução do aplicativo no Visual Studio para ajudar com a depuração.

Execute o aplicativo, em seu servidor ou na máquina de desenvolvimento, usando F5.

Abra a janela Pesquisa do Application Insights no Visual Studio e configure-a para exibir eventos de seu aplicativo. Durante a depuração, você pode fazer isso clicando no botão Application Insights.

![Clique com o botão direito no projeto e escolha Application Insights, Abrir.](./media/asp-net-exceptions/34.png)

Observe que você pode filtrar o relatório para mostrar apenas as exceções.

*Nenhuma exceção mostrando? Consulte [Capturar exceções](#exceptions).*

Clique em um relatório de exceções para mostrar o rastreamento de pilha.
Clique em uma referência de linha no rastreamento de pilha para abrir o arquivo de código relevante.

No código, observe que o CodeLens mostra dados sobre as exceções:

![Notificação de exceções do CodeLens.](./media/asp-net-exceptions/35.png)

## <a name="diagnosing-failures-using-the-azure-portal"></a>Como diagnosticar falhas usando o Portal do Azure
O Application Insights vem com uma experiência APM coletada para lhe ajudar a diagnosticar falhas nos aplicativos monitorados. Para iniciar, clique na opção de falhas no menu de recurso do Application Insights localizado na seção Investigar.
Você deve ver uma exibição de tela inteira que mostra as tendências de taxa de falha das solicitações, quantas delas estão falhando e quantos usuários são afetados. À direita, você verá algumas das distribuições mais úteis específicas para a operação com falha selecionada, incluindo os três principais códigos de resposta, os três principais tipos de exceção e os três principais tipos de dependência com falha.

![Exibição de falhas de triagem (guia operações)](./media/asp-net-exceptions/failures0719.png)

Em um único clique, você pode examinar amostras representativas para cada um desses subconjuntos de operações. Em particular, para diagnosticar exceções, você pode clicar na contagem de uma exceção específica a ser apresentada com a guia detalhes da transação de ponta a ponta, como esta:

![Guia detalhes da transação de ponta a ponta](./media/asp-net-exceptions/end-to-end.png)

**Como alternativa,** em vez de examinar as exceções de uma operação com falha específica, você pode iniciar na exibição geral de exceções, alternando para a guia exceções na parte superior. Aqui você pode ver todas as exceções coletadas para o aplicativo monitorado.

*Nenhuma exceção mostrando? Consulte [Capturar exceções](#exceptions).*


## <a name="custom-tracing-and-log-data"></a>Dados personalizados de rastreamento e log
Para obter dados de diagnóstico específicos do aplicativo, você pode inserir código para enviar seus próprios dados de telemetria. Isso é exibido na pesquisa de diagnóstico junto com a solicitação, a exibição de página e outros dados coletados automaticamente.

Você tem várias opções:

* [TrackEvent()](../../azure-monitor/app/api-custom-events-metrics.md#trackevent) normalmente é usado para monitorar padrões de uso, mas os dados que ele envia também aparecem em Eventos Personalizados na pesquisa de diagnóstico. Os eventos são nomeados e podem conter propriedades de cadeia de caracteres e métricas numéricas nas quais é possível [filtrar pesquisas de diagnóstico](../../azure-monitor/app/diagnostic-search.md).
* [TrackTrace()](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) permite que você envie dados mais longos, como informações POST.
* [TrackException()](#exceptions) envia rastreamentos de pilha. [Mais sobre exceções](#exceptions).
* Se você já usa uma estrutura de registros, como Log4Net ou NLog poderá [capturar esses logs](asp-net-trace-logs.md) e vê-los na pesquisa de diagnóstico junto com os dados de solicitação e exceção.

Para ver esses eventos, abra [Pesquisar](../../azure-monitor/app/diagnostic-search.md) no menu à esquerda, selecione os **tipos de evento**de menu suspenso e, em seguida, escolha evento personalizado, rastreamento ou exceção.

![Drill-through](./media/asp-net-exceptions/customevents.png)

> [!NOTE]
> Se o seu aplicativo gerar muita telemetria, o módulo de amostragem adaptável reduzirá automaticamente o volume enviado ao portal, enviando apenas uma fração representativa de eventos. Os eventos que fazem parte da mesma operação serão selecionados ou desmarcados como um grupo, para que você possa navegar entre os eventos relacionados. [Saiba mais sobre amostragem.](../../azure-monitor/app/sampling.md)
>
>

### <a name="how-to-see-request-post-data"></a>Como consultar dados POST de solicitação
Os detalhes da solicitação não incluem os dados enviados ao seu aplicativo em uma chamada POST. Para que esses dados sejam relatados:

* [Instale o SDK](../../azure-monitor/app/asp-net.md) no projeto do seu aplicativo.
* Insira o código no seu aplicativo para chamar [Microsoft.ApplicationInsights.TrackTrace()](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace). Envie os dados de POST no parâmetro de mensagem. Há um limite para o tamanho permitido, portanto você deve tentar enviar somente os dados essenciais.
* Quando você investiga uma solicitação com falha, localize os rastreamentos associados.

## <a name="exceptions"></a> Capturando exceções e dados de diagnóstico relacionados
No início você não verá no portal todas as exceções que causam falhas em seu aplicativo. Você verá quaisquer exceções do navegador (se você estiver usando o [SDK do JavaScript](../../azure-monitor/app/javascript.md) nas páginas da Web). Mas a maioria das exceções de servidor são capturados pelo IIS e é preciso escrever um pouco de código para vê-los.

Você pode:

* **Registrar as exceções explicitamente** inserindo código em manipuladores de exceção para relatar as exceções.
* **Capturar exceções automaticamente** configurando sua estrutura do ASP.NET. As inclusões necessárias são diferentes para diferentes tipos de estrutura.

## <a name="reporting-exceptions-explicitly"></a>Relatar exceções explicitamente
A maneira mais simples é inserir uma chamada a TrackException() em um manipulador de exceção.

```javascript
    try
    { ...
    }
    catch (ex)
    {
      appInsights.trackException(ex, "handler loc",
        {Game: currentGame.Name,
         State: currentGame.State.ToString()});
    }
```

```csharp
    var telemetry = new TelemetryClient();
    ...
    try
    { ...
    }
    catch (Exception ex)
    {
       // Set up some properties:
       var properties = new Dictionary <string, string>
         {{"Game", currentGame.Name}};

       var measurements = new Dictionary <string, double>
         {{"Users", currentGame.Users.Count}};

       // Send the exception telemetry:
       telemetry.TrackException(ex, properties, measurements);
    }
```

```VB
    Dim telemetry = New TelemetryClient
    ...
    Try
      ...
    Catch ex as Exception
      ' Set up some properties:
      Dim properties = New Dictionary (Of String, String)
      properties.Add("Game", currentGame.Name)

      Dim measurements = New Dictionary (Of String, Double)
      measurements.Add("Users", currentGame.Users.Count)

      ' Send the exception telemetry:
      telemetry.TrackException(ex, properties, measurements)
    End Try
```

Os parâmetros de medidas e propriedades são opcionais, mas são úteis para [filtrar e adicionar](../../azure-monitor/app/diagnostic-search.md) informações extras. Por exemplo, se você tiver um aplicativo que pode executar vários jogos, será possível localizar todos os relatórios de exceção relacionados a um jogo específico. Você pode adicionar quantos itens desejar a cada dicionário.

## <a name="browser-exceptions"></a>Exceções de navegador
A maioria das exceções de navegador são relatados.

Se sua página da web inclui arquivos de script de redes de distribuição de conteúdo ou de outros domínios, certifique-se de sua marca de script com o atributo ```crossorigin="anonymous"``` e que o servidor envia [cabeçalhos CORS](https://enable-cors.org/). Isso permitirá que você obtenha um rastreamento de pilha e detalhes de exceções sem tratamento JavaScript desses recursos.

## <a name="reuse-your-telemetry-client"></a>Reutilizar o cliente de telemetria

> [!NOTE]
> O TelemetryClient é recomendado para ser instanciado uma vez e reutilizado durante toda a vida útil de um aplicativo.

Veja abaixo um exemplo que usa TelemetryClient corretamente.

```csharp
public class GoodController : ApiController
{
    // OK
    private static readonly TelemetryClient telemetryClient;

    static GoodController()
    {
        telemetryClient = new TelemetryClient();
    }
}
```


## <a name="web-forms"></a>Formulários da Web
Para formulários da web, o módulo HTTP poderá coletar as exceções quando não houver nenhum redirecionamento configurado com CustomErrors.

Mas se você tiver redirecionamentos ativos, adicione as seguintes linhas para a função Application_Error em Global.asax.cs. (Adicionar um arquivo Global.asax se você ainda não tiver um).

```csharp
    void Application_Error(object sender, EventArgs e)
    {
      if (HttpContext.Current.IsCustomErrorEnabled && Server.GetLastError () != null)
      {
         var ai = new TelemetryClient(); // or re-use an existing instance

         ai.TrackException(Server.GetLastError());
      }
    }
```
## <a name="mvc"></a>MVC
Começar com o SDK do Application Insights Web versão 2.6 (beta3 e posterior), exceções de coleta sem tratamento do Application Insights lançadas automaticamente nos métodos de controladores MVC 5+. Se você já adicionou um manipulador personalizado para controlar essas exceções (conforme descrito nos exemplos a seguir), você pode remover para evitar o controle duplo de exceções.

Há um número de casos que não podem lidar com os filtros de exceção. Por exemplo:

* Exceções geradas por construtores de controlador.
* Exceções geradas por manipuladores de mensagens.
* Exceções geradas durante o roteamento.
* Exceções geradas durante a serialização de conteúdo da resposta.
* Exceção lançada durante a inicialização do aplicativo.
* Exceção lançada em tarefas em segundo plano.

Todas as exceções *tratadas* pelo aplicativo ainda precisam ser controladas manualmente.
Exceções não tratadas provenientes de controladores geralmente resultam em resposta de "Erro interno do servidor" 500. Se a resposta for criada manualmente como resultado de exceção tratada (ou nenhuma exceção), ele é rastreado em telemetria de solicitação correspondente com `ResultCode` 500, porém o SDK do Application Insights não poderá controlar a exceção correspondente.

### <a name="prior-versions-support"></a>Suporte de versões anteriores
Se você usa o MVC 4 (e versões anteriores) do Application Insights Web SDK 2.5 (e versões anteriores), consulte os exemplos a seguir para rastrear exceções.

Se a configuração do [CustomErrors](https://msdn.microsoft.com/library/h0hfz6fc.aspx) é `Off`, as exceções estarão disponíveis para o [módulo HTTP](https://msdn.microsoft.com/library/ms178468.aspx) coletar. No entanto, se for `RemoteOnly` (padrão), ou `On`, a exceção será desmarcada e não está disponível para o Application Insights coletar automaticamente. Você pode corrigir isso substituindo a classe [System.Web.Mvc.HandleErrorAttribute](https://msdn.microsoft.com/library/system.web.mvc.handleerrorattribute.aspx) e aplicando a classe substituída conforme mostrado para as diferentes versões do MVC abaixo ([fonte do GitHub](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions/blob/master/MVC2App/Controllers/AiHandleErrorAttribute.cs)):

```csharp
    using System;
    using System.Web.Mvc;
    using Microsoft.ApplicationInsights;

    namespace MVC2App.Controllers
    {
      [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, Inherited = true, AllowMultiple = true)]
      public class AiHandleErrorAttribute : HandleErrorAttribute
      {
        public override void OnException(ExceptionContext filterContext)
        {
            if (filterContext != null && filterContext.HttpContext != null && filterContext.Exception != null)
            {
                //If customError is Off, then AI HTTPModule will report the exception
                if (filterContext.HttpContext.IsCustomErrorEnabled)
                {   //or reuse instance (recommended!). see note above
                    var ai = new TelemetryClient();
                    ai.TrackException(filterContext.Exception);
                }
            }
            base.OnException(filterContext);
        }
      }
    }
```

#### <a name="mvc-2"></a>MVC 2
Substitua o atributo HandleError pelo novo atributo em seus controladores.

```csharp
    namespace MVC2App.Controllers
    {
        [AiHandleError]
        public class HomeController : Controller
        {
    ...
```

[Amostra](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions)

#### <a name="mvc-3"></a>MVC 3
Registrar `AiHandleErrorAttribute` como um filtro global em Global.asax.cs:

```csharp
    public class MyMvcApplication : System.Web.HttpApplication
    {
      public static void RegisterGlobalFilters(GlobalFilterCollection filters)
      {
         filters.Add(new AiHandleErrorAttribute());
      }
     ...
```

[Amostra](https://github.com/AppInsightsSamples/Mvc3UnhandledExceptionTelemetry)

#### <a name="mvc-4-mvc5"></a>MVC 4, MVC5
Registre AiHandleErrorAttribute como um filtro global em FilterConfig.cs:

```csharp
    public class FilterConfig
    {
      public static void RegisterGlobalFilters(GlobalFilterCollection filters)
      {
        // Default replaced with the override to track unhandled exceptions
        filters.Add(new AiHandleErrorAttribute());
      }
    }
```

[Amostra](https://github.com/AppInsightsSamples/Mvc5UnhandledExceptionTelemetry)

## <a name="web-api"></a>API Web
Começar com o SDK do Application Insights Web versão 2.6 (beta3 e posterior), exceções de coleta sem tratamento do Application Insights lançadas automaticamente nos métodos de controladores para WebAPI 2+. Se você já adicionou um manipulador personalizado para controlar essas exceções (conforme descrito nos exemplos a seguir), você pode remover para evitar o controle duplo de exceções.

Há um número de casos que não podem lidar com os filtros de exceção. Por exemplo:

* Exceções geradas por construtores de controlador.
* Exceções geradas por manipuladores de mensagens.
* Exceções geradas durante o roteamento.
* Exceções geradas durante a serialização de conteúdo da resposta.
* Exceção lançada durante a inicialização do aplicativo.
* Exceção lançada em tarefas em segundo plano.

Todas as exceções *tratadas* pelo aplicativo ainda precisam ser controladas manualmente.
Exceções não tratadas provenientes de controladores geralmente resultam em resposta de "Erro interno do servidor" 500. Se a resposta for criada manualmente como resultado de exceção tratada (ou nenhuma exceção), ele é rastreado em uma telemetria de solicitação correspondente com `ResultCode` 500, porém o SDK do Application Insights não poderá controlar a exceção correspondente.

### <a name="prior-versions-support"></a>Suporte de versões anteriores
Se você usa o WebAPI 1 (e versões anteriores) do Application Insights Web SDK 2.5 (e versões anteriores), consulte os exemplos a seguir para rastrear exceções.

#### <a name="web-api-1x"></a>Web API 1.x
Substitua System.Web.Http.Filters.ExceptionFilterAttribute:

```csharp
    using System.Web.Http.Filters;
    using Microsoft.ApplicationInsights;

    namespace WebAPI.App_Start
    {
      public class AiExceptionFilterAttribute : ExceptionFilterAttribute
      {
        public override void OnException(HttpActionExecutedContext actionExecutedContext)
        {
            if (actionExecutedContext != null && actionExecutedContext.Exception != null)
            {  //or reuse instance (recommended!). see note above
                var ai = new TelemetryClient();
                ai.TrackException(actionExecutedContext.Exception);
            }
            base.OnException(actionExecutedContext);
        }
      }
    }
```

Você pode adicionar esse atributo substituído para controladores específicos ou adicioná-lo na configuração de filtros globais na classe WebApiConfig:

```csharp
    using System.Web.Http;
    using WebApi1.x.App_Start;

    namespace WebApi1.x
    {
      public static class WebApiConfig
      {
        public static void Register(HttpConfiguration config)
        {
            config.Routes.MapHttpRoute(name: "DefaultApi", routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional });
            ...
            config.EnableSystemDiagnosticsTracing();

            // Capture exceptions for Application Insights:
            config.Filters.Add(new AiExceptionFilterAttribute());
        }
      }
    }
```

[Amostra](https://github.com/AppInsightsSamples/WebApi_1.x_UnhandledExceptions)

#### <a name="web-api-2x"></a>Web API 2.x
Adicione uma implementação de IExceptionLogger:

```csharp
    using System.Web.Http.ExceptionHandling;
    using Microsoft.ApplicationInsights;

    namespace ProductsAppPureWebAPI.App_Start
    {
      public class AiExceptionLogger : ExceptionLogger
      {
        public override void Log(ExceptionLoggerContext context)
        {
            if (context !=null && context.Exception != null)
            {//or reuse instance (recommended!). see note above
                var ai = new TelemetryClient();
                ai.TrackException(context.Exception);
            }
            base.Log(context);
        }
      }
    }
```

Adicione isso aos serviços no WebApiConfig:

```csharp
    using System.Web.Http;
    using System.Web.Http.ExceptionHandling;
    using ProductsAppPureWebAPI.App_Start;

    namespace WebApi2WithMVC
    {
      public static class WebApiConfig
      {
        public static void Register(HttpConfiguration config)
        {
            // Web API configuration and services

            // Web API routes
            config.MapHttpAttributeRoutes();

            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );
            config.Services.Add(typeof(IExceptionLogger), new AiExceptionLogger());
        }
      }
     }
```

[Amostra](https://github.com/AppInsightsSamples/WebApi_2.x_UnhandledExceptions)

Como alternativas, você pode:

1. Substituir o ExceptionHandler apenas por uma implementação personalizada de IExceptionHandler. Isso é chamado apenas quando a estrutura ainda é capaz de escolher a mensagem de resposta para enviar (não quando a conexão é anulada, por exemplo)
2. Filtros de Exceção (como descrito na seção controladores acima da API Web 1.x) - não são chamados em todos os casos.

## <a name="wcf"></a>WCF
Adicione uma classe que estende o atributo e implementa IErrorHandler e IServiceBehavior.

```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.ServiceModel.Description;
    using System.ServiceModel.Dispatcher;
    using System.Web;
    using Microsoft.ApplicationInsights;

    namespace WcfService4.ErrorHandling
    {
      public class AiLogExceptionAttribute : Attribute, IErrorHandler, IServiceBehavior
      {
        public void AddBindingParameters(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase,
            System.Collections.ObjectModel.Collection<ServiceEndpoint> endpoints,
            System.ServiceModel.Channels.BindingParameterCollection bindingParameters)
        {
        }

        public void ApplyDispatchBehavior(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase)
        {
            foreach (ChannelDispatcher disp in serviceHostBase.ChannelDispatchers)
            {
                disp.ErrorHandlers.Add(this);
            }
        }

        public void Validate(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase)
        {
        }

        bool IErrorHandler.HandleError(Exception error)
        {//or reuse instance (recommended!). see note above
            var ai = new TelemetryClient();

            ai.TrackException(error);
            return false;
        }

        void IErrorHandler.ProvideFault(Exception error,
            System.ServiceModel.Channels.MessageVersion version,
            ref System.ServiceModel.Channels.Message fault)
        {
        }
      }
    }

Add the attribute to the service implementations:

    namespace WcfService4
    {
        [AiLogException]
        public class Service1 : IService1
        {
         ...
```

[Amostra](https://github.com/AppInsightsSamples/WCFUnhandledExceptions)

## <a name="exception-performance-counters"></a>Contadores de desempenho de exceção
Se você [instalou o Agente do Application Insights](../../azure-monitor/app/monitor-performance-live-website-now.md) no seu servidor, poderá obter um gráfico da taxa de exceções, medida pelo .NET. Isso inclui exceções .NET tradas e sem tratamento.

Abra uma guia do Gerenciador de métricas, adicione um novo gráfico e selecione **taxa de exceção**, listada em contadores de desempenho.

O .NET Framework calcula a taxa contando o número de exceções em um intervalo e dividindo pelo comprimento do intervalo.

Isso é diferente da contagem 'Exceções' calculada pelo portal do Application Insights contando relatórios TrackException. Os intervalos de amostragem são diferentes, e o SDK não envia relatórios TrackException a todas as exceções tratadas e sem tratamento.

## <a name="next-steps"></a>Próximas etapas
* [Monitorar REST, SQL e outras chamadas para dependências](../../azure-monitor/app/asp-net-dependencies.md)
* [Monitorar tempos de carregamento de página, exceções de navegador e chamadas AJAX](../../azure-monitor/app/javascript.md)
* [Monitorar contadores de desempenho](../../azure-monitor/app/performance-counters.md)