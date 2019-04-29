---
title: correlação de telemetria no Azure Application Insights | Microsoft Docs
description: Correlação no Application Insights Telemetry
services: application-insights
documentationcenter: .net
author: lgayhardt
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/14/2019
ms.reviewer: sergkanz
ms.author: lagayhar
ms.openlocfilehash: 565f08f0c69aef393a9296f3cce90570a3f0bc2c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60901112"
---
# <a name="telemetry-correlation-in-application-insights"></a>Correlação de telemetria no Application Insights

No mundo dos microsserviços, cada operação lógica requer que o trabalho seja feito em vários componentes do serviço. Cada um desses componentes pode ser monitorado separadamente pelo [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). O componente do aplicativo Web se comunica com o componente de provedor de autenticação para validar as credenciais do usuário e com o componente de API para obter dados para visualização. O componente de API pode consultar dados de outros serviços e usar componentes do provedor de cache para notificar o componente de cobrança sobre essa chamada. O Application Insights é compatível com a correlação de telemetria distribuída, que você pode usar para detectar qual componente é responsável por falhas ou degradação do desempenho.

Este artigo explica o modelo de dados usado pelo Application Insights para correlacionar a telemetria enviada por vários componentes. Ele aborda protocolos e técnicas de propagação de contexto. Também aborda a implementação de conceitos de correlação em plataformas e idiomas diferentes.

## <a name="data-model-for-telemetry-correlation"></a>Modelo de dados para correlação de telemetria

O Application Insights define um [modelo de dados](../../azure-monitor/app/data-model.md) para correlação de telemetria distribuída. Para associar a telemetria à operação lógica, cada item de telemetria tem o campo de contexto chamado `operation_Id`. Esse identificador é compartilhado por todos os itens de telemetria no rastreamento distribuído. Portanto, mesmo com a perda de telemetria de uma única camada, você ainda pode associar a telemetria relatada por outros componentes.

Uma operação lógica distribuída normalmente consiste de um conjunto de operações menores, que são solicitações processadas por um dos componentes. Essas operações são definidas por [telemetria de solicitação](../../azure-monitor/app/data-model-request-telemetry.md). Toda telemetria de solicitação tem seu próprio `id` que a identifica de modo exclusivo e global. E todos os itens de telemetria (como rastreamentos e exceções) associados a essa solicitação devem definir o `operation_parentId` para o valor de `id` da solicitação.

Toda operação de saída, como uma chamada HTTP para outro componente, é representada por [telemetria de dependência](../../azure-monitor/app/data-model-dependency-telemetry.md). A telemetria de dependência também define sua própria `id`, que é globalmente exclusiva. A telemetria de solicitação, iniciada por essa chamada de dependência, usa este `id` como `operation_parentId`.

Você pode compilar um modo de exibição de operação lógica distribuída usando `operation_Id`, `operation_parentId` e `request.id` com `dependency.id`. Esses campos também definem a ordem de causalidade das chamadas de telemetria.

Em um ambiente de microsserviços, os rastreamentos de componentes podem ir para itens de armazenamento diferentes. Todo componente pode ter sua própria chave de instrumentação no Application Insights. Para obter a telemetria para a operação lógica, você deve consultar os dados de cada item de armazenamento. Quando o número de itens de armazenamento for enorme, você precisará de uma dica sobre onde procurar em seguida. O modelo de dados do Application Insights define dois campos para resolver esse problema: `request.source` e `dependency.target`. O primeiro campo identifica o componente que iniciou a solicitação de dependência, e o segundo identifica qual componente retornou a resposta da chamada de dependência.

## <a name="example"></a>Exemplo

Vejamos um exemplo de um aplicativo chamado Preços de ações, que mostra o preço de mercado atual de uma ação usando uma API externa chamada `Stock`. O aplicativo Preços de ações tem uma página chamada `Stock page` que o navegador da Web do cliente abre usando `GET /Home/Stock`. O aplicativo consulta a API `Stock` usando uma chamada HTTP `GET /api/stock/value`.

Você pode analisar a telemetria resultante executando uma consulta:

```kusto
(requests | union dependencies | union pageViews)
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

Nos resultados, observe que todos os itens de telemetria compartilham a raiz `operation_Id`. Quando uma chamada Ajax é feita da página, a nova ID exclusiva (`qJSXU`) é atribuída à telemetria de dependência e a ID de pageView é usada como `operation_ParentId`. A solicitação do servidor, em seguida, usa a ID do Ajax como `operation_ParentId`.

| itemType   | Nome                      | ID           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| pageView   | Página de ações                |              | STYz               | STYz         |
| dependência | GET /Home/Stock           | qJSXU        | STYz               | STYz         |
| solicitação    | GET Home/Stock            | KqKwlrSt9PA= | qJSXU              | STYz         |
| dependência | GET /api/stock/value      | bBrf2L7mm2g= | KqKwlrSt9PA=       | STYz         |

Quando a chamada `GET /api/stock/value` é feita para um serviço externo, você deseja saber a identidade desse servidor para que possa definir o campo `dependency.target` adequadamente. Quando o serviço externo não oferece suporte a monitoramento, `target` é definido como o nome do host do serviço (por exemplo, `stock-prices-api.com`). No entanto, se o serviço se identifica retornando um cabeçalho HTTP predefinido, `target` contém a identidade do serviço que permite que o Application Insights crie um rastreamento distribuído ao consultar a telemetria desse serviço.

## <a name="correlation-headers"></a>Cabeçalhos de correlação

Estamos trabalhando na proposta de RFC para o [protocolo HTTP de correlação](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md). Essa proposta define dois cabeçalhos:

- `Request-Id`: carrega a ID globalmente exclusiva da chamada.
- `Correlation-Context`: carrega a coleção de pares nome-valor das propriedades de rastreamento distribuído.

O padrão também define dois esquemas para geração de `Request-Id`: simples e hierárquico. Com o esquema simples, uma chave `Id` conhecida é definida para a coleção `Correlation-Context`.

O Application Insights define a [extensão](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) para o protocolo HTTP de correlação. Ele usa pares nome-valor de `Request-Context` para propagar a coleção de propriedades usadas pelo computador chamado ou chamador imediato. O SDK do Application Insights usa esse cabeçalho para definir os campos `dependency.target` e `request.source`.

### <a name="w3c-distributed-tracing"></a>Rastreamento distribuído do W3C

Estamos fazendo a transição para o [formato de rastreamento distribuído do W3C](https://w3c.github.io/trace-context/). Isso define:

- `traceparent`: carrega a ID globalmente exclusiva da operação e o identificador exclusivo da chamada.
- `tracestate`: carrega o contexto específico do sistema de rastreamento.

#### <a name="enable-w3c-distributed-tracing-support-for-classic-aspnet-apps"></a>Habilitar o suporte de rastreamento distribuído do W3C para aplicativos clássicos do ASP.NET

Esse recurso está disponível nos pacotes `Microsoft.ApplicationInsights.Web` e `Microsoft.ApplicationInsights.DependencyCollector` a partir da versão 2.8.0-beta1.
Isso está desabilitado por padrão. Para habilitá-lo, altere `ApplicationInsights.config`:

- Em `RequestTrackingTelemetryModule`, adicione o elemento `EnableW3CHeadersExtraction` com o valor definido para `true`.
- Em `DependencyTrackingTelemetryModule`, adicione o elemento `EnableW3CHeadersInjection` com o valor definido para `true`.

#### <a name="enable-w3c-distributed-tracing-support-for-aspnet-core-apps"></a>Habilitar o suporte de rastreamento distribuído do W3C para aplicativos ASP.NET Core

Esse recurso está no `Microsoft.ApplicationInsights.AspNetCore` versão 2.5.0-beta1 e no `Microsoft.ApplicationInsights.DependencyCollector` versão 2.8.0-beta1.
Isso está desabilitado por padrão. Para habilitar, configure `ApplicationInsightsServiceOptions.RequestCollectionOptions.EnableW3CDistributedTracing` como `true`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry(o => 
        o.RequestCollectionOptions.EnableW3CDistributedTracing = true );
    // ....
}
```

#### <a name="enable-w3c-distributed-tracing-support-for-java-apps"></a>Habilitar suporte a rastreamento distribuído do W3C para aplicativos Java

- **Configuração de entrada**

  - Para aplicativos Java EE, adicione o seguinte à marca `<TelemetryModules>` dentro de ApplicationInsights.xml:

    ```xml
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule>
       <Param name = "W3CEnabled" value ="true"/>
       <Param name ="enableW3CBackCompat" value = "true" />
    </Add>
    ```
  - Para aplicativos Spring Boot, adicione as seguintes propriedades:

    - `azure.application-insights.web.enable-W3C=true`
    - `azure.application-insights.web.enable-W3C-backcompat-mode=true`

- **Configuração de saída**

  Adicione o seguinte ao AI-Agent.xml:

  ```xml
  <Instrumentation>
    <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
    </BuiltIn>
  </Instrumentation>
  ```

  > [!NOTE]
  > o modo de compatibilidade com versões anteriores está habilitado por padrão e o parâmetro `enableW3CBackCompat` é opcional. Use-o somente quando quiser desativar a compatibilidade com versões anteriores.
  >
  > O ideal seria desativá-lo quando todos os seus serviços forem atualizados para versões mais recentes dos SDKs compatíveis com o protocolo W3C. É altamente recomendável que você mude para os SDKs mais novos assim que possível.

> [!IMPORTANT]
> Verifique se ambas as configurações de entrada e saída são exatamente as mesmas.

## <a name="opentracing-and-application-insights"></a>OpenTracing e Application Insights

A [especificação do modelo de dados do OpenTracing](https://opentracing.io/) e os modelos de dados do Application Insights são mapeados da seguinte maneira:

| Application Insights                  | OpenTracing                                       |
|------------------------------------   |-------------------------------------------------  |
| `Request`, `PageView`                 | `Span` com `span.kind = server`                  |
| `Dependency`                          | `Span` com `span.kind = client`                  |
| `Id` de `Request` e `Dependency`    | `SpanId`                                          |
| `Operation_Id`                        | `TraceId`                                         |
| `Operation_ParentId`                  | `Reference` do tipo `ChildOf` (o intervalo pai)   |

Para saber mais, confira o [Modelo de dados de telemetria do Application Insights](../../azure-monitor/app/data-model.md). 

Para obter definições de conceitos do OpenTracing, confira [specification](https://github.com/opentracing/specification/blob/master/specification.md) e [semantic_conventions](https://github.com/opentracing/specification/blob/master/semantic_conventions.md) do OpenTracing.

## <a name="telemetry-correlation-in-net"></a>Correlação de telemetria em .NET

Ao longo do tempo, o .NET definiu várias maneiras de correlacionar os logs de diagnóstico e de telemetria:

- O `System.Diagnostics.CorrelationManager` permite controlar [LogicalOperationStack e ActivityId](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx). 
- O `System.Diagnostics.Tracing.EventSource` e o ETW (Rastreamento de Eventos para Windows) definem o método [SetCurrentThreadActivityId](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx).
- `ILogger` usa [Escopos de log](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes). 
- O WCF (Windows Communication Foundation) e o HTTP conectam a propagação de contexto "atual".

No entanto, esses métodos não habilitam o suporte a rastreamento distribuído automático. `DiagnosticSource` é uma maneira de dar suporte automático à correlação entre computadores. Bibliotecas .NET são compatíveis com 'DiagnosticSource' e permitem propagação automática entre computadores do contexto de correlação por meio de transporte, como o HTTP.

O [Guia para Atividades](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) em `DiagnosticSource` explica as noções básicas do acompanhamento de atividades.

O ASP.NET Core 2.0 é compatível com a extração de cabeçalhos HTTP e inicia a nova atividade.

O `System.Net.HttpClient`, a partir da versão 4.1.0, é compatível com a injeção automática de cabeçalhos HTTP de correlação e com o acompanhamento da chamada HTTP como uma atividade.

Há um novo módulo HTTP, [Microsoft.AspNet.TelemetryCorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/), para o ASP.NET clássico. Este módulo implementa a correlação de telemetria usando `DiagnosticSource`. Ele inicia uma atividade com base nos cabeçalhos de solicitação de entrada. Também correlaciona a telemetria dos diferentes estágios de processamento de solicitação, até mesmo nos casos em que todos os estágios do processamento de Serviços de Informações da Internet (IIS) são executados em um thread gerenciado diferente.

O SDK do Application Insights, a partir da versão 2.4.0-beta1, usa `DiagnosticSource` e `Activity` para coletar telemetria e associá-la à atividade atual.

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-the-java-sdk"></a>Correlação de telemetria no SDK do Java

O [SDK do Application Insights para Java](../../azure-monitor/app/java-get-started.md) oferece suporte para correlação automática de telemetria a partir da versão 2.0.0. Ele preenche automaticamente `operation_id` para toda a telemetria (como rastreamentos, exceções e eventos personalizados) emitida dentro do escopo de uma solicitação. Também cuida da propagação dos cabeçalhos de correlação (descritos anteriormente) para as chamadas de serviço a serviço via HTTP se o [Agendo do SDK do Java](../../azure-monitor/app/java-agent.md) estiver configurado.

> [!NOTE]
> Somente chamadas feitas via Apache HTTPClient têm suporte para o recurso de correlação. Se você estiver usando o Spring RestTemplate ou o Feign, ambos podem ser usados com o Apache HTTPClient.

Atualmente, a propagação automática de contexto entre tecnologias de mensagens (como Kafka, RabbitMQ ou Barramento de Serviço do Microsoft Azure) não tem suporte. No entanto, é possível codificar esses cenários manualmente usando as APIs `trackDependency` e `trackRequest`. Nessas APIs, uma telemetria de dependência representa uma mensagem que está sendo enfileirada por um produtor e a solicitação representa uma mensagem que está sendo processada por um consumidor. Neste caso, tanto `operation_id` como `operation_parentId` devem ser propagados nas propriedades da mensagem.

### <a name="telemetry-correlation-in-asynchronous-java-application"></a>Correlação de telemetria no aplicativo de Java assíncrono

Para correlacionar a telemetria no aplicativo Spring Boot assíncrona, siga [isso](https://github.com/Microsoft/ApplicationInsights-Java/wiki/Distributed-Tracing-in-Asynchronous-Java-Applications) artigo detalhado. Ele fornece orientações para a instrumentação do Spring [ThreadPoolTaskExecutor](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskExecutor.html) , bem como [ThreadPoolTaskScheduler](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskScheduler.html). 


<a name="java-role-name"></a>
## <a name="role-name"></a>Nome da função

Às vezes, convém personalizar a maneira como os nomes de componentes são exibidos no [Mapa do Aplicativo](../../azure-monitor/app/app-map.md). Para fazer isso, você pode definir manualmente `cloud_RoleName`seguindo um destes procedimentos:

- Se você usar o Spring Boot com o iniciador do Spring Boot do Application Insights, a única alteração necessária é definir seu nome personalizado para o aplicativo no arquivo application.properties.

  `spring.application.name=<name-of-app>`

  O iniciador do Spring Boot atribui automaticamente `cloudRoleName` ao valor inserido para a propriedade `spring.application.name`.

- Se você está usando `WebRequestTrackingFilter`, `WebAppNameContextInitializer` define o nome do aplicativo automaticamente. Adicione o seguinte ao arquivo de configuração (ApplicationInsights.xml):

  ```XML
  <ContextInitializers>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebAppNameContextInitializer" />
  </ContextInitializers>
  ```

- Se você usar a classe de contexto de nuvem:

  ```Java
  telemetryClient.getContext().getCloud().setRole("My Component Name");
  ```

## <a name="next-steps"></a>Próximas etapas

- Gravar a [telemetria personalizada](../../azure-monitor/app/api-custom-events-metrics.md).
- Saiba mais sobre a [configuração cloud_RoleName](../../azure-monitor/app/app-map.md#set-cloud-role-name) para outros SDKs.
- Integrar todos os componentes do seu microsserviço ao Application Insights. Consultar as [plataformas compatíveis](../../azure-monitor/app/platforms.md).
- Consultar o [modelo de dados](../../azure-monitor/app/data-model.md) para os tipos do Application Insights.
- Saiba como [estender e filtrar a telemetria](../../azure-monitor/app/api-filtering-sampling.md).
- Revisar a [Referência de configuração do Application Insights](configuration-with-applicationinsights-config.md).
