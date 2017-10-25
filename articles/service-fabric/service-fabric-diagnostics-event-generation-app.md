---
title: "Monitoramento de nível de aplicativo do Service Fabric do Azure | Microsoft Docs"
description: "Saiba mais sobre eventos e logs de nível de aplicativo e serviço usados para monitorar e diagnosticar clusters do Service Fabric do Azure."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/15/2017
ms.author: dekapur
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: 3c472904641108b7383cd0f1416c47460f8de11a
ms.contentlocale: pt-br
ms.lasthandoff: 05/31/2017

---

# <a name="application-and-service-level-event-and-log-generation"></a>Geração de logs e eventos de nível de aplicativo e serviço

## <a name="instrumenting-the-code-with-custom-events"></a>Instrumentação do código com eventos personalizados

Instrumentar o código é a base para a maioria dos outros aspectos do monitoramento de seus serviços. A instrumentação é a única maneira que você possa saber que algo está errado e para diagnosticar o que precisa ser corrigido. Embora tecnicamente é possível conectar um depurador a um serviço de produção, ele não é uma prática comum. Portanto, é importante ter dados de instrumentação detalhados.

Alguns produtos instrumentam automaticamente seu código. Embora essas soluções podem funcionar bem, quase sempre a instrumentação manual é necessária. No fim das contas, você deve ter informações suficientes para depurar legalmente o aplicativo. Este documento descreve as diferentes abordagens para instrumentar seu código quando for necessário escolher uma abordagem em vez de outra.

## <a name="eventsource"></a>EventSource
Quando você cria uma solução do Service Fabric de um modelo do Visual Studio, uma classe derivada de **EventSource** (**ServiceEventSource** ou **ActorEventSource**) é gerada. Um modelo é criado, no qual você pode adicionar eventos para seu aplicativo ou serviço. O nome **EventSource** **deve** ser exclusivo e deve ser renomeado na cadeia de caracteres do modelo padrão MyCompany-&lt;solution&gt;-&lt;project&gt;. Ter várias definições de **EventSource** que usam o mesmo nome causa um problema no tempo de execução. Cada evento definido deve ter um identificador exclusivo. Se um identificador não for exclusivo, ocorrerá uma falha de tempo de execução. Algumas organizações atribuem antecipadamente intervalos de valores para evitar conflitos entre equipes de desenvolvimento separadas. Para saber mais, veja [blog do Vance](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/) ou a [documentação do MSDN](https://msdn.microsoft.com/library/dn774985(v=pandp.20).aspx).

### <a name="using-structured-eventsource-events"></a>Usando eventos estruturados de EventSource

Cada um dos eventos nos exemplos de código nesta seção são definidos para um caso específico, por exemplo, quando um tipo de serviço é registrado. Quando você definir mensagens pelo caso de uso, dados podem ser empacotados com o texto do erro e mais, você pode facilmente procurar e filtrar com base nos nomes ou valores das propriedades especificadas. Estruturar a saída de instrumentação torna o consumo mais fácil, mas exige mais ideias e mais tempo para definir um novo evento para cada caso de uso. Algumas definições de eventos podem ser compartilhadas em todo o aplicativo. Por exemplo, um evento de início ou de parada de método seria reutilizado em vários serviços em um aplicativo. Um serviço de domínio específico, como um sistema de pedidos, pode ter um evento **CreateOrder**, que terá seu próprio evento exclusivo. Essa abordagem pode gerar muitos eventos e potencialmente requer a coordenação de identificadores entre as equipes de projeto. 

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
```

### <a name="using-eventsource-generically"></a>Usando o EventSource de forma genérica

Como definir eventos específicos pode ser difícil, muitas pessoas definem alguns eventos com um conjunto comum de parâmetros que geralmente emitem suas informações como uma cadeia de caracteres. Grande parte do aspecto estruturado é perdida, tornando mais difícil pesquisar e filtrar os resultados. Alguns eventos, geralmente correspondentes aos níveis de registro de log, são definidos com essa abordagem. O trecho a seguir define uma mensagem de erro e de depuração:

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
```

Usar uma mistura de instrumentação genérica e não estruturados também pode funcionar bem. A instrumentação estruturada é usada para relatar erros e métricas. Eventos genéricos podem ser usados para o log detalhado que é consumido pelos engenheiros para solução de problemas.

## <a name="aspnet-core-logging"></a>Registro em log de ASP.NET Core

É importante planejar cuidadosamente como você instrumentará o seu código. O plano de instrumentação certa pode ajudar a evitar potencialmente desestabilizar sua base de código e, em seguida, precisar reinstrument o código. Para reduzir o risco, você pode escolher uma biblioteca de instrumentação como [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/), que faz parte do Microsoft ASP.NET Core. Núcleo do ASP.NET tem um [ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger) interface que você pode usar com o provedor de sua escolha, minimizando o efeito no código existente. Você pode usar o código ASP.NET Core no Windows e Linux e no .NET Framework completo, então o código de instrumentação é padronizado. Isso é explorado em mais detalhes abaixo:

### <a name="using-microsoftextensionslogging-in-service-fabric"></a>Uso de Microsoft.Extensions.Logging no Service Fabric

1. Adicione o pacote NuGet Microsoft.Extensions.Logging ao projeto que você deseja instrumentar. Além disso, adicione os pacotes de provedor (para um pacote de produtos de terceiros, veja o exemplo a seguir). Para saber mais, veja [Entrar no ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging).
2. Adicione uma diretiva **using** para Microsoft.Extensions.Logging ao seu arquivo de serviço.
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

## <a name="using-other-logging-providers"></a>Usando outros provedores de registro log

Alguns provedores de terceiros usam a abordagem descrita na seção anterior, incluindo [Serilog](https://serilog.net/), [NLog](http://nlog-project.org/) e [Loggr](https://github.com/imobile3/Loggr.Extensions.Logging). Você pode conectar cada uma delas no log do ASP.NET Core, ou pode usá-las separadamente. O Serilog tem um recurso que aprimora todas as mensagens enviadas de um agente de log. Esse recurso pode ser útil para saída do nome do serviço, do tipo e das informações de partição. Para usar esse recurso na infra-estrutura do ASP.NET Core, siga estas etapas:

1. Adicione os pacotes NuGet Serilog, Serilog.Extensions.Logging e Serilog.Sinks.Observable ao projeto. No próximo exemplo, adicione também Serilog.Sinks.Literate. Uma abordagem melhor será mostrada posteriormente neste artigo.
2. No Serilog, crie uma LoggerConfiguration e a instância do agente.

  ```csharp
  Log.Logger = new LoggerConfiguration().WriteTo.LiterateConsole().CreateLogger();
  ```

3. Adicione um argumento SeriLog.ILogger ao construtor do serviço e passe o agente recém-criado.

  ```csharp
  ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();
  ```

4. No construtor de serviço, adicione o código a seguir, que cria os enriquecedores de propriedade para as propriedades **ServiceTypeName**, **ServiceName**, **PartitionId** e **InstanceId** do serviço. Ele também adiciona um aprimorador de propriedade para a fábrica de logs do ASP.NET Core, para que você possa usar Microsoft.Extensions.Logging.ILogger no seu código.

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
  >É recomendável que você não use o Log.Logger estático com o exemplo anterior. O Service Fabric pode hospedar várias instâncias do mesmo tipo de serviço em um único processo. Se você usar o Log.Logger estático, o último gravador de aprimoradores de propriedade mostrará valores para todas as instâncias em execução. É por isso que a variável _logger é uma variável de membro particular da classe de serviço. Além disso, você deve disponibilizar o _logger para código comum, que pode ser usado entre serviços.

## <a name="choosing-a-logging-provider"></a>Escolher um provedor de log

Se o aplicativo depende de alto desempenho, o **EventSource** geralmente é uma boa abordagem. **EventSource** *geralmente* usa menos recursos e é melhor do que o ASP.NET Core log ou qualquer das soluções de terceiros disponíveis.  Isso não é um problema para muitos serviços, mas se o seu serviço for orientado ao desempenho, usar o **EventSource** poderá ser uma opção melhor. No entanto, para obter esses benefícios do registro em log estruturado, o **EventSource** requer um grande investimento da equipe de engenharia. Se possível, crie um protótipo rápido de algumas opções de log e escolha a que melhor atende às suas necessidades.

## <a name="next-steps"></a>Próximas etapas

Após escolher o provedor de log para instrumentar os aplicativos e serviços, os logs e eventos precisam ser agregados para que possam ser enviados a qualquer plataforma de análise. Leia sobre o [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) e o [WAD](service-fabric-diagnostics-event-aggregation-wad.md) para entender melhor algumas das opções recomendadas.

