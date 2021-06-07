---
title: Gerar eventos de log de um aplicativo .NET
description: Saiba mais sobre como adicionar log ao seu aplicativo .NET Service Fabric hospedado em um cluster do Azure ou um cluster de autônomo.
ms.topic: conceptual
ms.date: 03/27/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: dd3afed85dc3eff81cbe60f252cdc16b0d4018c8
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105628111"
---
# <a name="add-logging-to-your-service-fabric-application"></a>Adicione um login ao aplicativo do Service Fabric

Seu aplicativo deve fornecer informações suficientes para depuração quando surgirem problemas. O log é uma das coisas mais importantes que você pode adicionar ao seu aplicativo de Service Fabric. Quando ocorre uma falha, bom log pode fornecer uma maneira para investigar falhas. Ao analisar padrões de log, você pode descobrir maneiras de melhorar o desempenho ou design do seu aplicativo. Este documento demonstra algumas opções de log diferente.

## <a name="eventflow"></a>EventFlow

O conjunto [biblioteca EventFlow](https://github.com/Azure/diagnostics-eventflow) permite que aplicativos definam quais dados de diagnóstico coletar e onde devem ser produzidas. Dados de diagnóstico podem ser qualquer um dos contadores de desempenho para rastreamentos do aplicativo. Ele é executado no mesmo processo que o aplicativo para que a sobrecarga de comunicação seja minimizada. Para obter mais informações sobre EventFlow e o Service Fabric, veja [Agregação de eventos do Azure Service Fabric com EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md).

### <a name="using-structured-eventsource-events"></a>Usando eventos estruturados de EventSource

Definindo mensagem caso de uso de eventos permite que você para dados de pacote sobre o evento, no contexto do evento. Você pode pesquisar mais facilmente e filtrar com base em como os nomes ou valores das propriedades do evento especificado. Estruturar a saída de instrumentação torna a leitura mais fácil, mas exige mais ideias e mais tempo para definir um novo evento para cada caso de uso. 

Algumas definições de eventos podem ser compartilhadas em todo o aplicativo. Por exemplo, um evento de início ou de parada de método seria reutilizado em vários serviços em um aplicativo. Um serviço de domínio específico, como um sistema de pedidos, pode ter um evento **CreateOrder**, que terá seu próprio evento exclusivo. Essa abordagem pode gerar muitos eventos e potencialmente requer a coordenação de identificadores entre as equipes de projeto. 

```csharp
[EventSource(Name = "MyCompany-VotingState-VotingStateService")]
internal sealed class ServiceEventSource : EventSource
{
    public static readonly ServiceEventSource Current = new ServiceEventSource();

    // The instance constructor is private to enforce singleton semantics.
    private ServiceEventSource() : base() { }

    ...

    // The ServiceTypeRegistered event contains a unique identifier, an event attribute that defined the event, and the code implementation of the event.
    private const int ServiceTypeRegisteredEventId = 3;
    [Event(ServiceTypeRegisteredEventId, Level = EventLevel.Informational, Message = "Service host process {0} registered service type {1}", Keywords = Keywords.ServiceInitialization)]
    public void ServiceTypeRegistered(int hostProcessId, string serviceType)
    {
        WriteEvent(ServiceTypeRegisteredEventId, hostProcessId, serviceType);
    }

    // The ServiceHostInitializationFailed event contains a unique identifier, an event attribute that defined the event, and the code implementation of the event.
    private const int ServiceHostInitializationFailedEventId = 4;
    [Event(ServiceHostInitializationFailedEventId, Level = EventLevel.Error, Message = "Service host initialization failed", Keywords = Keywords.ServiceInitialization)]
    public void ServiceHostInitializationFailed(string exception)
    {
        WriteEvent(ServiceHostInitializationFailedEventId, exception);
    }

    ...

```

### <a name="using-eventsource-generically"></a>Usando o EventSource de forma genérica

Como definir eventos específicos pode ser difícil, muitas pessoas definem alguns eventos com um conjunto comum de parâmetros que geralmente emitem suas informações como uma cadeia de caracteres. Grande parte do aspecto estruturado é perdida, tornando mais difícil pesquisar e filtrar os resultados. Alguns eventos, geralmente correspondentes aos níveis de registro de log, são definidos com essa abordagem. O snippet a seguir define uma mensagem de erro e de depuração:

```csharp
[EventSource(Name = "MyCompany-VotingState-VotingStateService")]
internal sealed class ServiceEventSource : EventSource
{
    public static readonly ServiceEventSource Current = new ServiceEventSource();

    // The Instance constructor is private, to enforce singleton semantics.
    private ServiceEventSource() : base() { }

    ...

    private const int DebugEventId = 10;
    [Event(DebugEventId, Level = EventLevel.Verbose, Message = "{0}")]
    public void Debug(string msg)
    {
        WriteEvent(DebugEventId, msg);
    }

    private const int ErrorEventId = 11;
    [Event(ErrorEventId, Level = EventLevel.Error, Message = "Error: {0} - {1}")]
    public void Error(string error, string msg)
    {
        WriteEvent(ErrorEventId, error, msg);
    }

    ...

```

Usar uma mistura de instrumentação genérica e não estruturados também pode funcionar bem. A instrumentação estruturada é usada para relatar erros e métricas. Eventos genéricos podem ser usados para o log detalhado que é consumido pelos engenheiros para solução de problemas.

## <a name="microsoftextensionslogging"></a>Microsoft.Extensions.Logging

O registro do ASP.NET Core ([pacote Microsoft.Extensions.Logging NuGet](https://www.nuget.org/packages/Microsoft.Extensions.Logging)) é uma estrutura de log que fornece uma API de log padrão para seu aplicativo. Suporte para outros back-ends log pode ser conectado ao registro do ASP.NET Core. Isso fornece uma ampla variedade de suporte para log em seu aplicativo é processada, sem precisar alterar a quantidade de código.

1. Adicione o pacote NuGet **Microsoft. Extensions. Logging** ao projeto que você deseja instrumentar. Além disso, adicione todos os pacotes de provedor. Para saber mais, veja [Entrar no ASP.NET Core](/aspnet/core/fundamentals/logging).
2. Adicione uma diretiva **using** para **Microsoft.Extensions.Logging** ao seu arquivo de serviço.
3. Definir uma variável privada em sua classe de serviço.

   ```csharp
   private ILogger _logger = null;
   ```

4. No construtor da sua classe de serviço, adicione este código:

   ```csharp
   _logger = new LoggerFactory().CreateLogger<Stateless>();
   ```

5. Iniciar a instrumentação do código em seus métodos. Veja alguns exemplos:

   ```csharp
   _logger.LogDebug("Debug-level event from Microsoft.Logging");
   _logger.LogInformation("Informational-level event from Microsoft.Logging");

   // In this variant, we're adding structured properties RequestName and Duration, which have values MyRequest and the duration of the request.
   // Later in the article, we discuss why this step is useful.
   _logger.LogInformation("{RequestName} {Duration}", "MyRequest", requestDuration);
   ```

### <a name="using-other-logging-providers"></a>Usando outros provedores de registro log

Alguns provedores de terceiros usam a abordagem descrita na seção anterior, incluindo [Serilog](https://serilog.net/), [NLog](https://nlog-project.org/) e [Loggr](https://github.com/imobile3/Loggr.Extensions.Logging). Você pode conectar cada uma delas no log do ASP.NET Core, ou pode usá-las separadamente. O Serilog tem um recurso que aprimora todas as mensagens enviadas de um agente de log. Esse recurso pode ser útil para saída do nome do serviço, do tipo e das informações de partição. Para usar esse recurso na infra-estrutura do ASP.NET Core, siga estas etapas:

1. Adicione os pacotes NuGet **Serilog**, **Serilog.Extensions.Logging**, **Serilog.Sinks.Literate**, e **Serilog.Sinks.Observable** ao projeto. 
2. Criar um `LoggerConfiguration` e a instância do agente de log.

   ```csharp
   Log.Logger = new LoggerConfiguration().WriteTo.LiterateConsole().CreateLogger();
   ```

3. Adicione um `Serilog.ILogger` argumento ao construtor do serviço e passe o agente recém-criado.

   ```csharp
   ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();
   ```

4. No construtor de serviço, ele cria os enriquecedores de propriedade para **ServiceTypeName**, **ServiceName**, **PartitionId** e **InstanceId** do serviço.

   ```csharp
   public Stateless(StatelessServiceContext context, Serilog.ILogger serilog)
       : base(context)
   {
       PropertyEnricher[] properties = new PropertyEnricher[]
       {
           new PropertyEnricher("ServiceTypeName", context.ServiceTypeName),
           new PropertyEnricher("ServiceName", context.ServiceName),
           new PropertyEnricher("PartitionId", context.PartitionId),
           new PropertyEnricher("InstanceId", context.ReplicaOrInstanceId),
       };

       serilog.ForContext(properties);

       _logger = new LoggerFactory().AddSerilog(serilog.ForContext(properties)).CreateLogger<Stateless>();
   }
   ```

5. Instrumente o código da mesma forma com se você estivesse usando o ASP.NET Core sem SeriLog.

   >[!NOTE]
   >É recomendável que você *não* use o `Log.Logger` estático com o exemplo anterior. O Service Fabric pode hospedar várias instâncias do mesmo tipo de serviço em um único processo. Se você usar o `Log.Logger`, estático, o último gravador de aprimoradores de propriedade mostrará valores para todas as instâncias em execução. É por isso que a variável _logger é uma variável de membro particular da classe de serviço. Além disso, você deve disponibilizar o `_logger` para código comum, que pode ser usado entre serviços.

## <a name="next-steps"></a>Próximas etapas

- Leia mais informações sobre [aplicativo de monitoramento de Service Fabric](service-fabric-diagnostics-event-generation-app.md).
- Leia sobre registro em log com [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) e [Diagnóstico do Windows Azure](service-fabric-diagnostics-event-aggregation-wad.md).
