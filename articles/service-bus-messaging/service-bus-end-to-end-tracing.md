---
title: Diagnóstico e rastreamento end-to-end de Barramento de Serviço do Azure | Microsoft Docs
description: Visão geral do diagnóstico de cliente do barramento de serviço e rastreamento de ponta a ponta (cliente por meio de todos os serviços que estão envolvidos no processamento).
ms.topic: article
ms.date: 02/03/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 19b284aceb83fbbc2bcf662b2b58941e6a5b36f9
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539206"
---
# <a name="distributed-tracing-and-correlation-through-service-bus-messaging"></a>Rastreamento distribuído e correlação por meio de mensagens do Barramento de Serviço

Um dos problemas comuns no desenvolvimento do micro Services é a capacidade de rastrear a operação de um cliente por meio de todos os serviços envolvidos no processamento. É útil para depuração, análise de desempenho, testes A/B e outros cenários típicos de diagnóstico.
Uma parte desse problema é monitorar partes lógicas do trabalho. Inclui o resultado e latência de processamento de mensagem e chamadas de dependência externa. Outra parte é correlação desses eventos de diagnóstico além dos limites do processo.

Quando um produtor envia uma mensagem por meio de uma fila, normalmente acontece no escopo de alguma outra operação lógica, iniciado por algum outro cliente ou serviço. A mesma operação dará continuidade ao consumidor quando ele recebe uma mensagem. O produtor e o consumidor (e outros serviços que processam a operação), presumivelmente emitem eventos de telemetria para rastrear o fluxo e o resultado da operação. Para correlacionar esses eventos e rastrear a operação end-to-end, cada serviço que informa a telemetria deve gravar todos os eventos com um contexto de rastreamento.

Mensagens do Barramento de Serviço do Microsoft Azure definiu propriedades de carga que produtores e consumidores devem usar para passar esse contexto de rastreamento.
O protocolo se baseia o [protocolo HTTP Correlation](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md).

# <a name="azuremessagingservicebus-sdk-latest"></a>[SDK do Azure. Messaging. ServiceBus (mais recente)](#tab/net-standard-sdk-2)
| Nome da propriedade        | Descrição                                                 |
|----------------------|-------------------------------------------------------------|
|  ID de diagnóstico       | Identificador exclusivo de uma chamada externa do produtor para a fila. Consulte [Request-Id no protocolo HTTP](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#request-id) para a lógica, considerações e formato |

## <a name="service-bus-net-client-autotracing"></a>Autorastreamento de cliente .NET do barramento de serviço
A `ServiceBusProcessor` classe do [cliente do barramento de serviço de mensagens do Azure para .net](/dotnet/api/azure.messaging.servicebus.servicebusprocessor) fornece pontos de instrumentação de rastreamento que podem ser conectados por sistemas de rastreamento ou parte do código do cliente. A instrumentação permite controlar todas as chamadas para o serviço de mensagens do Barramento de Serviço do lado do cliente. Se o processamento de mensagens for feito usando o (padrão [ `ProcessMessageAsync` de manipulador de `ServiceBusProcessor` ](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.processmessageasync) mensagens), o processamento de mensagens também será instrumentado.

### <a name="tracking-with-azure-application-insights"></a>Rastreando o Application Insights do Azure

O [Microsoft Application Insights](https://azure.microsoft.com/services/application-insights/) fornece recursos de monitoramento de desempenho avançado, incluindo solicitações automáticas e rastreamento de dependências.

Dependendo do tipo do seu projeto, instale o SDK do Application Insights:
- [ASP.NET](../azure-monitor/app/asp-net.md) - instalar a versão 2.5-beta2 ou superior
- [ASP.NET Core](../azure-monitor/app/asp-net-core.md) - instalar a versão 2.2.0-beta2 ou superior.
Esses links fornecem detalhes sobre como instalar o SDK, criando recursos e configurando o SDK (se necessário). Para aplicativos diferentes do ASP.NET, consulte o artigo [Application Insights do Azure para aplicativos de Console](../azure-monitor/app/console.md).

Se você usar o (padrão [ `ProcessMessageAsync` de manipulador de `ServiceBusProcessor` ](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.processmessageasync) mensagens) para processar mensagens, o processamento de mensagens também será instrumentado. Todas as chamadas do barramento de serviço feitas pelo seu serviço são automaticamente rastreadas e correlacionadas com outros itens de telemetria. Caso contrário, consulte o exemplo a seguir para o controle de processamento de mensagem manual.

#### <a name="trace-message-processing"></a>Processamento de mensagem de rastreamento

```csharp
async Task ProcessAsync(ProcessMessageEventArgs args)
{
    ServiceBusReceivedMessage message = args.Message;
    if (message.ApplicationProperties.TryGetValue("Diagnostic-Id", out var objectId) && objectId is string diagnosticId)
    {
        var activity = new Activity("ServiceBusProcessor.ProcessMessage");
        activity.SetParentId(diagnosticId);
        // If you're using Microsoft.ApplicationInsights package version 2.6-beta or higher, you should call StartOperation<RequestTelemetry>(activity) instead
        using (var operation = telemetryClient.StartOperation<RequestTelemetry>("Process", activity.RootId, activity.ParentId))
        {
            telemetryClient.TrackTrace("Received message");
            try 
            {
            // process message
            }
            catch (Exception ex)
            {
                telemetryClient.TrackException(ex);
                operation.Telemetry.Success = false;
                throw;
            }

            telemetryClient.TrackTrace("Done");
        }
    }
}
```

Neste exemplo, a telemetria de solicitação é relatada para cada mensagem processada, tendo um carimbo de data/hora, duração e resultado (êxito). A telemetria também tem um conjunto de propriedades de correlação. Rastreamentos aninhados e exceções relatadas durante o processamento da mensagem também são marcadas com propriedades de correlação que os representam como filhos do `RequestTelemetry`.

Caso você faça chamadas para componentes externos com suporte durante o processamento de mensagens, elas também são automaticamente rastreadas e correlacionadas. Consulte [Acompanhar operações personalizadas com o SDK do .NET do Application Insights](../azure-monitor/app/custom-operations-tracking.md) para rastreamento manual e correlação.

Se você estiver executando qualquer código externo Além do SDK do Application Insights, espere ver a **duração** mais longa ao exibir os logs de Application insights. 

![Duração maior no log de Application Insights](./media/service-bus-end-to-end-tracing/longer-duration.png)

Isso não significa que houve um atraso ao receber a mensagem. Nesse cenário, a mensagem já foi recebida, pois a mensagem é passada como um parâmetro para o código do SDK. E, a marca de **nome** nos logs do App insights (**processo**) indica que a mensagem está sendo processada pelo código de processamento de evento externo. Esse problema não é relacionado ao Azure. Em vez disso, essas métricas referem-se à eficiência do seu código externo, dado que a mensagem já foi recebida do barramento de serviço. 

### <a name="tracking-without-tracing-system"></a>Controle sem o sistema de rastreamento
Caso seu sistema de rastreamento não dê suporte ao acompanhamento automático de chamadas do barramento de serviço, você pode estar procurando adicionar esse suporte a um sistema de rastreamento ou ao seu aplicativo. Esta seção descreve eventos de diagnóstico enviados pelo cliente .NET do Barramento de Serviço.  

Cliente de .NET do Barramento de Serviço é instrumentado usando primitivos de rastreamento .NET [System.Diagnostics.Activity](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) e [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md).

`Activity` serve como um contexto de rastreamento enquanto `DiagnosticSource` é um mecanismo de notificação. 

Se não houver nenhum ouvinte para os eventos de diagnóstico, a instrumentação estará desativada, mantendo os custos de instrumentação zero. O DiagnosticSource fornece todo o controle para o ouvinte:
- o ouvinte controla quais fontes e eventos escutar
- o ouvinte controla a amostragem e taxa do evento
- os eventos são enviados com uma carga que fornece o contexto completo para que você possa acessar e modificar o objeto de mensagem durante o evento

Familiarize-se com [Guia do Usuário DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) antes de continuar com a implementação.

Vamos criar um ouvinte de eventos do Barramento de Serviço no aplicativo do ASP.NET Core que grava logs com o Microsoft.Extension.Logger.
Ele usa a biblioteca [System.Reactive.Core](https://www.nuget.org/packages/System.Reactive.Core) para assinar DiagnosticSource (também é fácil assinar DiagnosticSource sem ela)

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory factory, IApplicationLifetime applicationLifetime)
{
    // configuration...

    var serviceBusLogger = factory.CreateLogger("Azure.Messaging.ServiceBus");

    IDisposable innerSubscription = null;
    IDisposable outerSubscription = DiagnosticListener.AllListeners.Subscribe(delegate (DiagnosticListener listener)
    {
        // subscribe to the Service Bus DiagnosticSource
        if (listener.Name == "Azure.Messaging.ServiceBus")
        {
            // receive event from Service Bus DiagnosticSource
            innerSubscription = listener.Subscribe(delegate (KeyValuePair<string, object> evnt)
            {
                // Log operation details once it's done
                if (evnt.Key.EndsWith("Stop"))
                {
                    Activity currentActivity = Activity.Current;
                    serviceBusLogger.LogInformation($"Operation {currentActivity.OperationName} is finished, Duration={currentActivity.Duration}, Id={currentActivity.Id}, StartTime={currentActivity.StartTimeUtc}");
                }
            });
        }
    });

    applicationLifetime.ApplicationStopping.Register(() =>
    {
        outerSubscription?.Dispose();
        innerSubscription?.Dispose();
    });
}
```

Neste exemplo, o ouvinte registra duração, resultado, identificador exclusivo e a hora de início para cada operação de Barramento de Serviço.

### <a name="events"></a>Eventos
Todos os eventos terão as seguintes propriedades que estão em conformidade com a especificação de telemetria aberta: https://github.com/open-telemetry/opentelemetry-specification/blob/master/specification/trace/api.md .

- `message_bus.destination` – caminho da fila/tópico/assinatura
- `peer.address` – namespace totalmente qualificado
- `kind` – produtor, consumidor ou cliente. O produtor é usado ao enviar mensagens, consumidor ao receber e cliente ao liquidar.
- `component` – `servicebus`

Todos os eventos também têm as propriedades ' Entity ' e ' Endpoint ', que são omitidos na tabela abaixo
  * `string Entity` --Nome da entidade (fila, tópico e assim por diante).
  * `Uri Endpoint` - URL do endpoint do Barramento de Serviço

### <a name="instrumented-operations"></a>Operações instrumentadas
Aqui está a lista completa de operações instrumentadas:

| Nome de operação | API controlada |
| -------------- | ----------- | 
| ServiceBusSender. Send | ServiceBusSender.SendMessageAsync<br/>ServiceBusSender.SendMessagesAsync |
| ServiceBusSender. Schedule | ServiceBusSender. ScheduleMessageAsync<br/>ServiceBusSender.ScheduleMessagesAsync | 
| ServiceBusSender. Cancel | ServiceBusSender.CancelScheduledMessageAsync<br/>ServiceBusSender.CancelScheduledMessagesAsync |
| ServiceBusReceiver. Receive | ServiceBusReceiver.ReceiveMessageAsync<br/>ServiceBusReceiver.ReceiveMessagesAsync |
| ServiceBusReceiver.ReceiveDeferred | ServiceBusReceiver.ReceiveDeferredMessagesAsync |
| ServiceBusReceiver. Peek | ServiceBusReceiver.PeekMessageAsync<br/>ServiceBusReceiver.PeekMessagesAsync |
| ServiceBusReceiver. Abandon | ServiceBusReceiver.AbandonMessagesAsync |
| ServiceBusReceiver. Complete | ServiceBusReceiver.CompleteMessagesAsync |
| ServiceBusReceiver. mensagens mortas | ServiceBusReceiver.DeadLetterMessagesAsync |
| ServiceBusReceiver. Defer |  ServiceBusReceiver.DeferMessagesAsync |
| ServiceBusReceiver.RenewMessageLock | ServiceBusReceiver.RenewMessageLockAsync |
| ServiceBusSessionReceiver.RenewSessionLock | ServiceBusSessionReceiver.RenewSessionLockAsync |
| ServiceBusSessionReceiver. getsessionstate | ServiceBusSessionReceiver.GetSessionStateAsync |
| ServiceBusSessionReceiver. setsessionstate | ServiceBusSessionReceiver.SetSessionStateAsync |
| ServiceBusProcessor. ProcessMessage | Retorno de chamada do processador definido em ServiceBusProcessor. Propriedade ProcessMessageAsync |
| ServiceBusSessionProcessor.ProcessSessionMessage | Retorno de chamada do processador definido em ServiceBusSessionProcessor. Propriedade ProcessMessageAsync |

### <a name="filtering-and-sampling"></a>Filtragem e amostragem

Em alguns casos, é desejável para fazer logon apenas uma parte dos eventos para reduzir o consumo de armazenamento ou sobrecarga de desempenho. Você pode fazer somente eventos de 'Stop' (como no exemplo anterior) ou a porcentagem de exemplo dos eventos. 
`DiagnosticSource` fornecem a maneira de conseguir isso com `IsEnabled` predicado. Para obter mais informações, consulte [Filtragem com base em contexto em DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#context-based-filtering).

`IsEnabled` pode ser chamado várias vezes para uma única operação para minimizar o impacto de desempenho.

`IsEnabled` é chamado na sequência a seguir:

1. `IsEnabled(<OperationName>, string entity, null)` por exemplo, `IsEnabled("ServiceBusSender.Send", "MyQueue1")`. Observe que não há ' Start ' ou ' Stop ' no final. Usá-lo para filtrar as operações específicas ou filas. Se o método de retorno de chamada retornar `false` , os eventos para a operação não serão enviados.

   * Para as operações de 'Processo' e 'ProcessSession', você também receberá `IsEnabled(<OperationName>, string entity, Activity activity)` retorno de chamada. Use-o para filtrar eventos com base em `activity.Id` ou propriedades de marcas.
  
2. `IsEnabled(<OperationName>.Start)` por exemplo, `IsEnabled("ServiceBusSender.Send.Start")`. Verifica se o evento 'Start' deva ser acionado. O resultado afeta apenas o evento ' Start ', mas a instrumentação adicional não depende dele.

Não há `IsEnabled` para o evento ' Stop '.

Se algum resultado da operação for a exceção, `IsEnabled("ServiceBusSender.Send.Exception")` é chamado. Você só pode assinar eventos de 'Exceções' e impedir o restante da instrumentação. Nesse caso, você ainda precisa tratar essas exceções. Como outra instrumentação está desabilitada, você não deve esperar que o contexto de rastreamento flua com as mensagens do consumidor para o produtor.

Você pode usar `IsEnabled` também implementar estratégias de amostragem. A amostragem com base no `Activity.Id` ou `Activity.RootId` garante a amostragem consistente em todos os aros (desde que ele seja propagado pelo sistema de rastreamento ou pelo seu próprio código).

Em presença de vários `DiagnosticSource` ouvintes para a mesma fonte, é suficiente que apenas um ouvinte aceite o evento, portanto, não há garantia que `IsEnabled` seja chamada.



# <a name="microsoftazureservicebus-sdk"></a>[SDK do Microsoft. Azure. ServiceBus](#tab/net-standard-sdk)

| Nome da propriedade        | Descrição                                                 |
|----------------------|-------------------------------------------------------------|
|  ID de diagnóstico       | Identificador exclusivo de uma chamada externa do produtor para a fila. Consulte [Request-Id no protocolo HTTP](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#request-id) para a lógica, considerações e formato |
|  Correlation-Context | Contexto de operação, é propagado por todos os serviços envolvidos no processamento da operação. Para obter mais informações, consulte [contexto de correlação no protocolo HTTP](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#correlation-context) |

## <a name="service-bus-net-client-autotracing"></a>Autorastreamento de cliente .NET do barramento de serviço

Começando com a versão 3.0.0 o [Cliente de Barramento de Serviço do Microsoft Azure para .NET](/dotnet/api/microsoft.azure.servicebus.queueclient) fornece pontos de instrumentação de rastreamento que podem ser conectados por sistemas de rastreamento ou parte do código de cliente.
A instrumentação permite controlar todas as chamadas para o serviço de mensagens do Barramento de Serviço do lado do cliente. Se o processamento de mensagens é feito com o [padrão de manipulador de mensagens](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler), o processamento de mensagem também é instrumentado

### <a name="tracking-with-azure-application-insights"></a>Rastreando o Application Insights do Azure

O [Microsoft Application Insights](https://azure.microsoft.com/services/application-insights/) fornece recursos de monitoramento de desempenho avançado, incluindo solicitações automáticas e rastreamento de dependências.

Dependendo do tipo do seu projeto, instale o SDK do Application Insights:
- [ASP.NET](../azure-monitor/app/asp-net.md) - instalar a versão 2.5-beta2 ou superior
- [ASP.NET Core](../azure-monitor/app/asp-net-core.md) - instalar a versão 2.2.0-beta2 ou superior.
Esses links fornecem detalhes sobre como instalar o SDK, criando recursos e configurando o SDK (se necessário). Para aplicativos diferentes do ASP.NET, consulte o artigo [Application Insights do Azure para aplicativos de Console](../azure-monitor/app/console.md).

Se você usar o [padrão de manipulador de mensagens](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler) para processar mensagens, você concluiu: todas as chamadas do barramento de serviço feitas pelo serviço são automaticamente rastreadas e correlacionadas com outros itens de telemetria. Caso contrário, consulte o exemplo a seguir para o controle de processamento de mensagem manual.

#### <a name="trace-message-processing"></a>Processamento de mensagem de rastreamento

```csharp
private readonly TelemetryClient telemetryClient;

async Task ProcessAsync(Message message)
{
    var activity = message.ExtractActivity();
    
    // If you're using Microsoft.ApplicationInsights package version 2.6-beta or higher, you should call StartOperation<RequestTelemetry>(activity) instead
    using (var operation = telemetryClient.StartOperation<RequestTelemetry>("Process", activity.RootId, activity.ParentId))
    {
        telemetryClient.TrackTrace("Received message");
        try 
        {
           // process message
        }
        catch (Exception ex)
        {
             telemetryClient.TrackException(ex);
             operation.Telemetry.Success = false;
             throw;
        }

        telemetryClient.TrackTrace("Done");
   }
}
```

Neste exemplo, `RequestTelemetry` é relatado para cada mensagem processada, com um carimbo de hora, a duração e o resultado (êxito). A telemetria também tem um conjunto de propriedades de correlação.
Rastreamentos aninhados e exceções relatadas durante o processamento da mensagem também são marcadas com propriedades de correlação que os representam como filhos do `RequestTelemetry`.

Caso você faça chamadas para componentes externos com suporte durante o processamento de mensagens, elas também são automaticamente rastreadas e correlacionadas. Consulte [Acompanhar operações personalizadas com o SDK do .NET do Application Insights](../azure-monitor/app/custom-operations-tracking.md) para rastreamento manual e correlação.

Se você estiver executando qualquer código externo Além do SDK do Application Insights, espere ver a **duração** mais longa ao exibir os logs de Application insights. 

![Duração maior no log de Application Insights](./media/service-bus-end-to-end-tracing/longer-duration.png)

Isso não significa que houve um atraso ao receber a mensagem. Nesse cenário, a mensagem já foi recebida, pois a mensagem é passada como um parâmetro para o código do SDK. E, a marca de **nome** nos logs do App insights (**processo**) indica que a mensagem está sendo processada pelo código de processamento de evento externo. Esse problema não é relacionado ao Azure. Em vez disso, essas métricas referem-se à eficiência do seu código externo, dado que a mensagem já foi recebida do barramento de serviço. Consulte [este arquivo no GitHub](https://github.com/Azure/azure-sdk-for-net/blob/4bab05144ce647cc9e704d46d3763de5f9681ee0/sdk/servicebus/Microsoft.Azure.ServiceBus/src/ServiceBusDiagnosticsSource.cs) para ver onde a marca de **processo** é gerada e atribuída depois que a mensagem tiver sido recebida do barramento de serviço. 

### <a name="tracking-without-tracing-system"></a>Controle sem o sistema de rastreamento
Caso seu sistema de rastreamento não dê suporte ao acompanhamento automático de chamadas do barramento de serviço, você pode estar procurando adicionar esse suporte a um sistema de rastreamento ou ao seu aplicativo. Esta seção descreve eventos de diagnóstico enviados pelo cliente .NET do Barramento de Serviço.  

Cliente de .NET do Barramento de Serviço é instrumentado usando primitivos de rastreamento .NET [System.Diagnostics.Activity](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) e [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md).

`Activity` serve como um contexto de rastreamento enquanto `DiagnosticSource` é um mecanismo de notificação. 

Se não houver nenhum ouvinte para os eventos de diagnóstico, a instrumentação estará desativada, mantendo os custos de instrumentação zero. O DiagnosticSource fornece todo o controle para o ouvinte:
- o ouvinte controla quais fontes e eventos escutar
- o ouvinte controla a amostragem e taxa do evento
- os eventos são enviados com uma carga que fornece o contexto completo para que você possa acessar e modificar o objeto de mensagem durante o evento

Familiarize-se com [Guia do Usuário DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) antes de continuar com a implementação.

Vamos criar um ouvinte de eventos do Barramento de Serviço no aplicativo do ASP.NET Core que grava logs com o Microsoft.Extension.Logger.
Ele usa a biblioteca [System.Reactive.Core](https://www.nuget.org/packages/System.Reactive.Core) para assinar DiagnosticSource (também é fácil assinar DiagnosticSource sem ela)

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory factory, IApplicationLifetime applicationLifetime)
{
    // configuration...

    var serviceBusLogger = factory.CreateLogger("Microsoft.Azure.ServiceBus");

    IDisposable innerSubscription = null;
    IDisposable outerSubscription = DiagnosticListener.AllListeners.Subscribe(delegate (DiagnosticListener listener)
    {
        // subscribe to the Service Bus DiagnosticSource
        if (listener.Name == "Microsoft.Azure.ServiceBus")
        {
            // receive event from Service Bus DiagnosticSource
            innerSubscription = listener.Subscribe(delegate (KeyValuePair<string, object> evnt)
            {
                // Log operation details once it's done
                if (evnt.Key.EndsWith("Stop"))
                {
                    Activity currentActivity = Activity.Current;
                    TaskStatus status = (TaskStatus)evnt.Value.GetProperty("Status");
                    serviceBusLogger.LogInformation($"Operation {currentActivity.OperationName} is finished, Duration={currentActivity.Duration}, Status={status}, Id={currentActivity.Id}, StartTime={currentActivity.StartTimeUtc}");
                }
            });
        }
    });

    applicationLifetime.ApplicationStopping.Register(() =>
    {
        outerSubscription?.Dispose();
        innerSubscription?.Dispose();
    });
}
```

Neste exemplo, o ouvinte registra duração, resultado, identificador exclusivo e a hora de início para cada operação de Barramento de Serviço.

#### <a name="events"></a>Eventos

Para cada operação, dois eventos são enviados: 'Start' e 'Stop'. Provavelmente, você está interessado apenas em eventos ' Stop '. Eles fornecem o resultado da operação e a hora de início e a duração como propriedades da atividade.

A carga do evento fornece um ouvinte com o contexto da operação, ele replica os parâmetros de entrada de API e valor de retorno. A carga do evento de 'Stop' tem todas as propriedades da carga do evento de 'Start', portanto você pode ignorar o evento de 'Start' completamente.

Todos os eventos também têm as propriedades ' Entity ' e ' Endpoint ', que são omitidos na tabela abaixo
  * `string Entity` - - Nome da entidade (fila, tópico, etc.)
  * `Uri Endpoint` - URL do endpoint do Barramento de Serviço

Cada evento de 'Stop' tem `Status` propriedade com `TaskStatus` operação assíncrona foi concluída, o que também é omitido na tabela a seguir para simplificar.

Aqui está a lista completa de operações instrumentadas:

| Nome de operação | API controlada | Propriedades específicas de carga|
|----------------|-------------|---------|
| Microsoft.Azure.ServiceBus.Send | [MessageSender.SendAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.sendasync) | `IList<Message> Messages` -Lista de mensagens sendo enviadas |
| Microsoft.Azure.ServiceBus.ScheduleMessage | [MessageSender.ScheduleMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.schedulemessageasync) | `Message Message` -Mensagem sendo processada<br/>`DateTimeOffset ScheduleEnqueueTimeUtc` -Deslocamento de mensagem agendado<br/>`long SequenceNumber` -Número de sequência da mensagem agendada (carga do evento 'Stop') |
| Microsoft.Azure.ServiceBus.Cancel | [MessageSender.CancelScheduledMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.cancelscheduledmessageasync) | `long SequenceNumber` -Número de sequência da mensagem a ser cancelada | 
| Microsoft.Azure.ServiceBus.Receive | [MessageReceiver.ReceiveAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receiveasync) | `int RequestedMessageCount` - O número máximo de mensagens que podem ser recebidas.<br/>`IList<Message> Messages` - Lista de mensagens recebidas (carga útil do evento 'Parar') |
| Microsoft.Azure.ServiceBus.Peek | [MessageReceiver.PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync) | `int FromSequenceNumber` - O ponto de partida a partir do qual procurar um lote de mensagens.<br/>`int RequestedMessageCount` - O número de mensagens a recuperar.<br/>`IList<Message> Messages` - Lista de mensagens recebidas (carga útil do evento 'Parar') |
| Microsoft.Azure.ServiceBus.ReceiveDeferred | [MessageReceiver.ReceiveDeferredMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receivedeferredmessageasync) | `IEnumerable<long> SequenceNumbers` - A lista que contém os números de sequência a serem recebidos.<br/>`IList<Message> Messages` - Lista de mensagens recebidas (carga útil do evento 'Parar') |
| Microsoft.Azure.ServiceBus.Complete | [MessageReceiver.CompleteAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.completeasync) | `IList<string> LockTokens` - A lista que contém os tokens de bloqueio das mensagens correspondentes para concluir.|
| Microsoft.Azure.ServiceBus.Abandon | [MessageReceiver.AbandonAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.abandonasync) | `string LockToken` - O token de bloqueio da mensagem correspondente é abandonado. |
| Microsoft.Azure.ServiceBus.Defer | [MessageReceiver.DeferAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deferasync) | `string LockToken` - O token de bloqueio da mensagem correspondente para adiar. | 
| Microsoft.Azure.ServiceBus.DeadLetter | [MessageReceiver.DeadLetterAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deadletterasync) | `string LockToken` - O símbolo de bloqueio da mensagem correspondente a letra morta. | 
| Microsoft.Azure.ServiceBus.RenewLock | [MessageReceiver.RenewLockAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync) | `string LockToken` - O token de bloqueio da mensagem correspondente para renovar o bloqueio.<br/>`DateTime LockedUntilUtc` - Nova data e hora de expiração do token de bloqueio no formato UTC. (carga do evento de 'Stop')|
| Microsoft.Azure.ServiceBus.Process | Função lambda manipulador de mensagens fornecida no [IReceiverClient.RegisterMessageHandler](/dotnet/api/microsoft.azure.servicebus.core.ireceiverclient.registermessagehandler) | `Message Message` - Mensagem sendo processada. |
| Microsoft.Azure.ServiceBus.ProcessSession | Função de lambda de manipulador de sessão de mensagem fornecida no [IQueueClient.RegisterSessionHandler](/dotnet/api/microsoft.azure.servicebus.iqueueclient.registersessionhandler) | `Message Message` - Mensagem sendo processada.<br/>`IMessageSession Session` -Sessão que está sendo processado |
| Microsoft.Azure.ServiceBus.AddRule | [SubscriptionClient.AddRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.addruleasync) | `RuleDescription Rule` - A descrição da regra que fornece a regra para adicionar. |
| Microsoft.Azure.ServiceBus.RemoveRule | [SubscriptionClient.RemoveRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.removeruleasync) | `string RuleName` - Nome da regra a ser removida. |
| Microsoft.Azure.ServiceBus.GetRules | [SubscriptionClient.GetRulesAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.getrulesasync) | `IEnumerable<RuleDescription> Rules` - Todas as regras associadas à assinatura. (Somente a carga 'Stop') |
| Microsoft.Azure.ServiceBus.AcceptMessageSession | [ISessionClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.azure.servicebus.isessionclient.acceptmessagesessionasync) | `string SessionId` - O sessionId presente nas mensagens. |
| Microsoft.Azure.ServiceBus.GetSessionState | [IMessageSession.GetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.getstateasync) | `string SessionId` - O sessionId presente nas mensagens.<br/>`byte [] State` - Estado da sessão (carga útil do evento 'Parar') |
| Microsoft.Azure.ServiceBus.SetSessionState | [IMessageSession.SetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.setstateasync) | `string SessionId` - O sessionId presente nas mensagens.<br/>`byte [] State` -Estado de sessão |
| Microsoft.Azure.ServiceBus.RenewSessionLock | [IMessageSession.RenewSessionLockAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.renewsessionlockasync) | `string SessionId` - O sessionId presente nas mensagens. |
| Microsoft.Azure.ServiceBus.Exception | qualquer API instrumentado| `Exception Exception` -Instância de exceção |

Em todos os eventos, você pode acessar `Activity.Current` que contém o contexto da operação atual.

#### <a name="logging-more-properties"></a>Registrando mais propriedades

`Activity.Current` fornece contexto detalhado da operação atual e seus pais. Para obter mais informações, consulte a [documentação da atividade](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md).
A instrumentação do barramento de serviço fornece mais informações no `Activity.Current.Tags` -elas mantêm `MessageId` e sempre que estão `SessionId` disponíveis.

Atividades que acompanham evento 'Receber', 'Inspeção' e 'ReceiveDeferred' também pode ter `RelatedTo` marca. Ele contém uma lista distinta de `Diagnostic-Id`(s) de mensagens que foram recebidas como resultado.
Essa operação pode resultar em várias mensagens relacionadas a ser recebida. Além disso, o `Diagnostic-Id` não é conhecido quando a operação é iniciada, portanto, as operações ' Receive ' podem ser correlacionadas às operações ' Process ' usando somente essa marca. É útil ao analisar problemas de desempenho para verificar quanto tempo demorou para receber a mensagem.

É uma maneira eficiente para as marcas de log iterar sobre eles, portanto adicionando marcas à forma do exemplo anterior 

```csharp
Activity currentActivity = Activity.Current;
TaskStatus status = (TaskStatus)evnt.Value.GetProperty("Status");

var tagsList = new StringBuilder();
foreach (var tags in currentActivity.Tags)
{
    tagsList.Append($", {tags.Key}={tags.Value}");
}

serviceBusLogger.LogInformation($"{currentActivity.OperationName} is finished, Duration={currentActivity.Duration}, Status={status}, Id={currentActivity.Id}, StartTime={currentActivity.StartTimeUtc}{tagsList}");
```

#### <a name="filtering-and-sampling"></a>Filtragem e amostragem

Em alguns casos, é desejável para fazer logon apenas uma parte dos eventos para reduzir o consumo de armazenamento ou sobrecarga de desempenho. Você pode fazer somente eventos de 'Stop' (como no exemplo anterior) ou a porcentagem de exemplo dos eventos. 
`DiagnosticSource` fornecem a maneira de conseguir isso com `IsEnabled` predicado. Para obter mais informações, consulte [Filtragem com base em contexto em DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#context-based-filtering).

`IsEnabled` pode ser chamado várias vezes para uma única operação para minimizar o impacto de desempenho.

`IsEnabled` é chamado na sequência a seguir:

1. `IsEnabled(<OperationName>, string entity, null)` por exemplo, `IsEnabled("Microsoft.Azure.ServiceBus.Send", "MyQueue1")`. Observe que não há ' Start ' ou ' Stop ' no final. Usá-lo para filtrar as operações específicas ou filas. Se o método de retorno de chamada retornar `false` , os eventos para a operação não serão enviados

   * Para as operações de 'Processo' e 'ProcessSession', você também receberá `IsEnabled(<OperationName>, string entity, Activity activity)` retorno de chamada. Use-o para filtrar eventos com base em `activity.Id` ou propriedades de marcas.
  
2. `IsEnabled(<OperationName>.Start)` por exemplo, `IsEnabled("Microsoft.Azure.ServiceBus.Send.Start")`. Verifica se o evento 'Start' deva ser acionado. O resultado afeta apenas o evento ' Start ', mas a instrumentação adicional não depende dele.

Não há `IsEnabled` para o evento ' Stop '.

Se algum resultado da operação for a exceção, `IsEnabled("Microsoft.Azure.ServiceBus.Exception")` é chamado. Você só pode assinar eventos de 'Exceções' e impedir o restante da instrumentação. Nesse caso, você ainda precisa tratar essas exceções. Como outra instrumentação está desabilitada, você não deve esperar que o contexto de rastreamento flua com as mensagens do consumidor para o produtor.

Você pode usar `IsEnabled` também implementar estratégias de amostragem. A amostragem com base no `Activity.Id` ou `Activity.RootId` garante a amostragem consistente em todos os aros (desde que ele seja propagado pelo sistema de rastreamento ou pelo seu próprio código).

Em presença de vários `DiagnosticSource` ouvintes para a mesma fonte, é suficiente que apenas um ouvinte aceite o evento, portanto, não há garantia que `IsEnabled` seja chamada.

---

## <a name="next-steps"></a>Próximas etapas

* [Correlação de Application Insights](../azure-monitor/app/correlation.md)
* [Monitore Dependências do Application Insights](../azure-monitor/app/asp-net-dependencies.md) para ver se REST, SQL ou outros recursos externos estão causando lentidão.
* [Acompanhar operações personalizadas com o SDK do .NET do Application Insights](../azure-monitor/app/custom-operations-tracking.md)

