---
title: "Acompanhar operações personalizadas com o SDK do .NET do Azure Application Insights | Microsoft Docs"
description: "Acompanhar operações personalizadas com o SDK do .NET do Azure Application Insights"
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 06/30/2017
ms.author: sergkanz
ms.openlocfilehash: 6412445f4e7a9b639ae9a38a44ff51038c6fcc00
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="track-custom-operations-with-application-insights-net-sdk"></a>Acompanhar operações personalizadas com o SDK do .NET do Application Insights

SDKs do Azure Application Insights acompanham automaticamente as solicitações HTTP de entrada e chamadas para serviços dependentes, como solicitações HTTP e consultas SQL. O acompanhamento e a correlação de solicitações e dependências fornecem visibilidade sobre a capacidade de resposta e a confiabilidade do aplicativo inteiro em todos os microsserviços que combinados nesse aplicativo. 

Há uma classe de padrões de aplicativo que não pode ter suporte de maneira genérica. O monitoramento adequado de tais padrões requer a instrumentação de código manual. Este artigo aborda alguns padrões que podem exigir a instrumentação manual, tais como processamento de fila personalizada e execução de tarefas em segundo plano de longa execução.

Este documento fornece diretrizes sobre como controlar operações personalizadas com o SDK do Application Insights. Esta documentação é relevante para:

- Application Insights para a .NET (também conhecido como o SDK de Base) versão 2.4+.
- Application Insights para aplicativos Web (executando ASP.NET) versão 2.4+.
- Application Insights para ASP.NET Core versão 2.1+.

## <a name="overview"></a>Visão geral
Uma operação é um trabalho lógico executado por um aplicativo. Ela tem nome, hora de início, duração e resultado, além de um contexto de execução como nome de usuário, propriedades e resultado. Se a operação A tiver sido iniciada pela operação B, então a operação B será definida como pai para A. Uma operação pode ter somente um pai, mas pode ter muitas operações filhas. Para obter mais informações sobre as operações e a correlação de telemetria, consulte [Correlação de telemetria do Azure Application Insights](application-insights-correlation.md).

No SDK do .NET do Application Insights, a operação é descrita pela classe abstrata [OperationTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Core/Managed/Shared/Extensibility/Implementation/OperationTelemetry.cs) e seus descendentes [RequestTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Core/Managed/Shared/DataContracts/RequestTelemetry.cs) e [DependencyTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Core/Managed/Shared/DataContracts/DependencyTelemetry.cs).

## <a name="incoming-operations-tracking"></a>Acompanhamento de operações de entrada 
O SDK da Web do Application Insights coleta automaticamente as solicitações HTTP para aplicativos ASP.NET executados em um pipeline do IIS e para todos os aplicativos do ASP.NET Core. Há soluções com suporte da comunidade para outras plataformas e estruturas. No entanto, se o aplicativo não tiver suporte por nenhuma das soluções padrão ou com suporte pela comunidade, você poderá instrumentá-lo manualmente.

Outro exemplo que requer um acompanhamento personalizado é o trabalho que recebe os itens da fila. Para alguns filas, a chamada para adicionar uma mensagem a essa fila é acompanhada como dependência. No entanto, a operação de alto nível que descreve o processamento de mensagens não é automaticamente coletada.

Vamos ver como podemos acompanhar essas operações.

Em um nível alto, a tarefa é criar `RequestTelemetry` e definir propriedades conhecidas. Depois que a operação for concluída, você poderá acompanhar a telemetria. O exemplo a seguir demonstra essa tarefa.

### <a name="http-request-in-owin-self-hosted-app"></a>Solicitação HTTP no aplicativo autohospedado Owin
Neste exemplo, seguimos o [Protocolo HTTP para correlação](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md). Espere receber cabeçalhos descritos lá.

``` C#
public class ApplicationInsightsMiddleware : OwinMiddleware
{
    private readonly TelemetryClient telemetryClient = new TelemetryClient(TelemetryConfiguration.Active);
    
    public ApplicationInsightsMiddleware(OwinMiddleware next) : base(next) {}

    public override async Task Invoke(IOwinContext context)
    {
        // Let's create and start RequestTelemetry.
        var requestTelemetry = new RequestTelemetry
        {
            Name = $"{context.Request.Method} {context.Request.Uri.GetLeftPart(UriPartial.Path)}"
        };

        // If there is a Request-Id received from the upstream service, set the telemetry context accordingly.
        if (context.Request.Headers.ContainsKey("Request-Id"))
        {
            var requestId = context.Request.Headers.Get("Request-Id");
            // Get the operation ID from the Request-Id (if you follow the HTTP Protocol for Correlation).
            requestTelemetry.Context.Operation.Id = GetOperationId(requestId);
            requestTelemetry.Context.Operation.ParentId = requestId;
        }

        // StartOperation is a helper method that allows correlation of 
        // current operations with nested operations/telemetry
        // and initializes start time and duration on telemetry items.
        var operation = telemetryClient.StartOperation(requestTelemetry);

        // Process the request.
        try
        {
            await Next.Invoke(context);
        }
        catch (Exception e)
        {
            requestTelemetry.Success = false;
            telemetryClient.TrackException(e);
            throw;
        }
        finally
        {
            // Update status code and success as appropriate.
            if (context.Response != null)
            {
                requestTelemetry.ResponseCode = context.Response.StatusCode.ToString();
                requestTelemetry.Success = context.Response.StatusCode >= 200 && context.Response.StatusCode <= 299;
            }
            else
            {
                requestTelemetry.Success = false;
            }

            // Now it's time to stop the operation (and track telemetry).
            telemetryClient.StopOperation(operation);
        }
    }
    
    public static string GetOperationId(string id)
    {
        // Returns the root ID from the '|' to the first '.' if any.
        int rootEnd = id.IndexOf('.');
        if (rootEnd < 0)
            rootEnd = id.Length;

        int rootStart = id[0] == '|' ? 1 : 0;
        return id.Substring(rootStart, rootEnd - rootStart);
    }
}
```

O protocolo HTTP para correlação também declara o cabeçalho `Correlation-Context`. No entanto, é omitido aqui para manter a simplicidade.

## <a name="queue-instrumentation"></a>Instrumentação de fila
Para a comunicação HTTP, criamos um protocolo para passar os detalhes de correlação. Com alguns protocolos de filas, você pode passar metadados adicionais junto com a mensagem e, com outros, não.

### <a name="service-bus-queue"></a>Fila do Barramento de Serviço
Com a [fila do Barramento de Serviço](../service-bus-messaging/index.md) do Azure, você pode passar um recipiente de propriedades junto com a mensagem. É usada para passar a ID de correlação.

A Fila do Barramento de Serviço usa protocolos baseados em TCP. O Application Insights não acompanha automaticamente as operações de fila, então nós as acompanhamos manualmente. A operação de remover da fila é uma API de estilo push e não é possível acompanhá-la.

#### <a name="enqueue"></a>Enfileirar

```C#
public async Task Enqueue(string payload)
{
    // StartOperation is a helper method that initializes the telemetry item
    // and allows correlation of this operation with its parent and children.
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queueName);
    operation.Telemetry.Type = "Queue";
    operation.Telemetry.Data = "Enqueue " + queueName;

    var message = new BrokeredMessage(payload);
    // Service Bus queue allows the property bag to pass along with the message.
    // We will use them to pass our correlation identifiers (and other context)
    // to the consumer.
    message.Properties.Add("ParentId", operation.Telemetry.Id);
    message.Properties.Add("RootId", operation.Telemetry.Context.Operation.Id);

    try
    {
        await queue.SendAsync(message);
        
        // Set operation.Telemetry Success and ResponseCode here.
        operation.Telemetry.Success = true;
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        // Set operation.Telemetry Success and ResponseCode here.
        operation.Telemetry.Success = false;
        throw;
    }
    finally
    {
        telemetryClient.StopOperation(operation);
    }
}
```

#### <a name="process"></a>Processo
```C#
public async Task Process(BrokeredMessage message)
{
    // After the message is taken from the queue, create RequestTelemetry to track its processing.
    // It might also make sense to get the name from the message.
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "Dequeue " + queueName };

    var rootId = message.Properties["RootId"].ToString();
    var parentId = message.Properties["ParentId"].ToString();
    // Get the operation ID from the Request-Id (if you follow the HTTP Protocol for Correlation).
    requestTelemetry.Context.Operation.Id = rootId;
    requestTelemetry.Context.Operation.ParentId = parentId;

    var operation = telemetryClient.StartOperation(requestTelemetry);

    try
    {
        await ProcessMessage();
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        throw;
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }
}
```

### <a name="azure-storage-queue"></a>Fila de Armazenamento do Azure
O exemplo a seguir mostra como acompanhar operações da [fila de Armazenamento do Azure](../storage/queues/storage-dotnet-how-to-use-queues.md) e correlacionar telemetria entre o produtor, o consumidor e o Armazenamento do Azure. 

A fila de Armazenamento tem uma API HTTP. Todas as chamadas à fila são rastreadas pelo coletor de dependência do Application Insights para solicitações HTTP.
Certifique-se de que `Microsoft.ApplicationInsights.DependencyCollector.HttpDependenciesParsingTelemetryInitializer` esteja em `applicationInsights.config`. Se você não tem, adicione-o programaticamente, conforme descrito em [Filtragem e pré-processamento no SDK do Azure Application Insights](app-insights-api-filtering-sampling.md).

Se você configurar o Application Insights manualmente, certifique-se de criar e inicializar `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule` de maneira similar a:
 
``` C#
DependencyTrackingTelemetryModule module = new DependencyTrackingTelemetryModule();

// You can prevent correlation header injection to some domains by adding it to the excluded list.
// Make sure you add a Storage endpoint. Otherwise, you might experience request signature validation issues on the Storage service side.
module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
module.Initialize(TelemetryConfiguration.Active);

// Do not forget to dispose of the module during application shutdown.
```

Também convém correlacionar a ID da operação do Application Insights à ID de solicitação de Armazenamento. Para obter informações sobre como definir e obter um cliente de solicitação de Armazenamento e uma ID de solicitação do servidor, consulte [Monitorar, diagnosticar e solucionar problemas do Armazenamento do Azure](../storage/common/storage-monitoring-diagnosing-troubleshooting.md#end-to-end-tracing).

#### <a name="enqueue"></a>Enfileirar
Como as filas de Armazenamento do Azure dão suporte a API HTTP, todas as operações com a fila automaticamente são acompanhadas pelo Application Insights. Em muitos casos, essa instrumentação deve ser suficiente. No entanto, para correlacionar rastreamentos no lado do consumidor com rastreamentos de produtor, você deve passar algum contexto de correlação de forma similar a como fazemos em Protocolo HTTP para Correlação. 

Neste exemplo, podemos acompanhar a operação opcional `Enqueue`. Você pode:

 - **Correlacionar novas tentativas (se houver)**: todas têm um pai comum que é a operação `Enqueue`. Caso contrário, elas são acompanhadas como filhos da solicitação de entrada. Se houver várias solicitações lógicas para a fila, pode ser difícil descobrir qual chamada resultou em novas tentativas.
 - **Correlacione os logs do Armazenamento (se e quando necessário)**: são correlacionados à telemetria do Application Insights.

A operação `Enqueue` é filho de uma operação pai (por exemplo, uma solicitação de HTTP entrada). A chamada de dependência de HTTP é o filho da operação `Enqueue` e o neto da solicitação de entrada:

```C#
public async Task Enqueue(CloudQueue queue, string message)
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queue.Name);
    operation.Telemetry.Type = "Queue";
    operation.Telemetry.Data = "Enqueue " + queue.Name;

    // MessagePayload represents your custom message and also serializes correlation identifiers into payload.
    // For example, if you choose to pass payload serialized to JSON, it might look like
    // {'RootId' : 'some-id', 'ParentId' : '|some-id.1.2.3.', 'message' : 'your message to process'}
    var jsonPayload = JsonConvert.SerializeObject(new MessagePayload
    {
        RootId = operation.Telemetry.Context.Operation.Id,
        ParentId = operation.Telemetry.Id,
        Payload = message
    });
    
    CloudQueueMessage queueMessage = new CloudQueueMessage(jsonPayload);

    // Add operation.Telemetry.Id to the OperationContext to correlate Storage logs and Application Insights telemetry.
    OperationContext context = new OperationContext { ClientRequestID = operation.Telemetry.Id};

    try
    {
        await queue.AddMessageAsync(queueMessage, null, null, new QueueRequestOptions(), context);
    }
    catch (StorageException e)
    {
        operation.Telemetry.Properties.Add("AzureServiceRequestID", e.RequestInformation.ServiceRequestID);
        operation.Telemetry.Success = false;
        operation.Telemetry.ResultCode = e.RequestInformation.HttpStatusCode.ToString();
        telemetryClient.TrackException(e);
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }
}  
```

Para reduzir a quantidade de telemetria que o seu aplicativo relata ou se você não quiser acompanhar a operação `Enqueue` por outros motivos, use a API `Activity` diretamente:

- Crie (e inicie) um novo `Activity` em vez de iniciar a operação do Application Insights. Você *não* precisa atribuir nenhuma propriedade a ele, exceto o nome da operação.
- Serializar `yourActivity.Id` para o conteúdo da mensagem, em vez de `operation.Telemetry.Id`. Você também pode usar `Activity.Current.Id`.


#### <a name="dequeue"></a>Remover da fila
Da mesma forma que `Enqueue`, a solicitação HTTP real para a fila de Armazenamento é acompanhada automaticamente pelo Application Insights. No entanto, a operação `Enqueue` supostamente ocorre no contexto de pai, como um contexto de solicitação de entrada. Os SDKs do Application Insights correlacionam automaticamente tal operação (e a parte HTTP dela) com a solicitação pai e outra telemetria relatada no mesmo escopo.

A operação `Dequeue` é complicada. O SDK do Application Insights acompanha automaticamente as solicitações HTTP. No entanto, ele não sabe o contexto de correlação até que a mensagem seja analisada. Não é possível correlacionar a solicitação HTTP para obter a mensagem com o restante da telemetria.

Em muitos casos, pode ser útil correlacionar a solicitação HTTP à fila em outros rastreamentos também. O exemplo a seguir demonstra como fazer isso:

``` C#
public async Task<MessagePayload> Dequeue(CloudQueue queue)
{
    var telemetry = new DependencyTelemetry
    {
        Type = "Queue",
        Name = "Dequeue " + queue.Name
    };

    telemetry.Start();

    try
    {
        var message = await queue.GetMessageAsync();

        if (message != null)
        {
            var payload = JsonConvert.DeserializeObject<MessagePayload>(message.AsString);

            // If there is a message, we want to correlate the Dequeue operation with processing.
            // However, we will only know what correlation ID to use after we get it from the message,
            // so we will report telemetry after we know the IDs.
            telemetry.Context.Operation.Id = payload.RootId;
            telemetry.Context.Operation.ParentId = payload.ParentId;

            // Delete the message.
            return payload;
        }
    }
    catch (StorageException e)
    {
        telemetry.Properties.Add("AzureServiceRequestID", e.RequestInformation.ServiceRequestID);
        telemetry.Success = false;
        telemetry.ResultCode = e.RequestInformation.HttpStatusCode.ToString();
        telemetryClient.TrackException(e);
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetry.Stop();
        telemetryClient.Track(telemetry);
    }

    return null;
}
```

#### <a name="process"></a>Processo

No exemplo a seguir, acompanharemos uma mensagem de entrada da mesma forma como podemos acompanhar uma solicitação HTTP de entrada:

```C#
public async Task Process(MessagePayload message)
{
    // After the message is dequeued from the queue, create RequestTelemetry to track its processing.
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "Dequeue " + queueName };
    // It might also make sense to get the name from the message.
    requestTelemetry.Context.Operation.Id = message.RootId;
    requestTelemetry.Context.Operation.ParentId = message.ParentId;

    var operation = telemetryClient.StartOperation(requestTelemetry);

    try
    {
        await ProcessMessage();
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        throw;
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }
}
```

Da mesma forma, outras operações de fila podem ser instrumentadas. Uma operação de espiar deve ser instrumentada da mesma maneira que uma operação de remoção da fila. A instrumentação de operações de gerenciamento de fila não é necessária. O Application Insights acompanha operações como HTTP e, na maioria dos casos, isso é suficiente.

Ao instrumentar a exclusão de mensagem, verifique se você definiu os identificadores da operação (correlação). Como alternativa, você pode usar a API `Activity`. Assim, você não precisa definir identificadores de operação nos itens de telemetria porque o Application Insights faz isso para você:

- Crie um novo `Activity` depois que tiver obtido um item da fila.
- Use `Activity.SetParentId(message.ParentId)` para correlacionar os logs de produtor e consumidor.
- Inicie o `Activity`.
- Acompanhe as operações de remoção da fila, processamento e exclusão usando auxiliares `Start/StopOperation`. Faça isso do mesmo fluxo de controle assíncrono (contexto de execução). Dessa forma, elas são correlacionadas corretamente.
- Pare o `Activity`.
- Use `Start/StopOperation` ou chame a telemetria `Track` manualmente.

### <a name="batch-processing"></a>Processamento em lotes
Com algumas filas, você pode remover da fila várias mensagens com uma solicitação. O processamento dessas mensagens é supostamente independente e pertence a diferentes operações lógicas. Nesse caso, não é possível correlacionar a operação `Dequeue` a um processamento de mensagem específico.

Cada mensagem deve ser processada no seu próprio fluxo de controle assíncrono. Para obter mais informações, consulte a seção [Acompanhamento de dependências de saída](#outgoing-dependencies-tracking).

## <a name="long-running-background-tasks"></a>Tarefas em segundo plano de execução longa
Alguns aplicativos iniciam operações de longa execução que podem ser causadas por solicitações de usuário. Da perspectiva do rastreamento/instrumentação, isso não é diferente da instrumentação de solicitação ou de dependência: 

``` C#
async Task BackgroundTask()
{
    var operation = telemetryClient.StartOperation<RequestTelemetry>(taskName);
    operation.Telemetry.Type = "Background";
    try
    {
        int progress = 0;
        while (progress < 100)
        {
            // Process the task.
            telemetryClient.TrackTrace($"done {progress++}%");
        }
        // Update status code and success as appropriate.
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        // Update status code and success as appropriate.
        throw;
    }
    finally
    {
        telemetryClient.StopOperation(operation);
    }
}
```

Neste exemplo, usamos `telemetryClient.StartOperation` para criar `RequestTelemetry` e preencher o contexto de correlação. Digamos que você tem uma operação pai criada por solicitações de entrada que agendaram a operação. Desde que `BackgroundTask` inicie no mesmo fluxo de controle assíncrono que uma solicitação de entrada, ela será correlacionada com essa operação pai. `BackgroundTask` e todos os itens de telemetria aninhados são automaticamente correlacionados com a solicitação a causou, mesmo após o término da solicitação.

Quando a tarefa inicia do thread em segundo plano que não tem nenhuma operação (`Activity`) associada a ele, `BackgroundTask` não tem nenhum pai. No entanto, ela pode ter operações aninhadas. Todos os itens de telemetria relatados da tarefa estão correlacionados à `RequestTelemetry` criada na `BackgroundTask`.

## <a name="outgoing-dependencies-tracking"></a>Acompanhamento de dependências de saída
Você pode controlar sua própria variante de dependência ou uma operação sem suporte pelo Application Insights.

O método `Enqueue` na fila do Barramento de Serviço ou fila de Armazenamento pode servir como exemplos de tal acompanhamento personalizado.

A abordagem geral ao acompanhamento de dependência personalizado é:

- Chamar o método `TelemetryClient.StartOperation` (extensão) que preencha as propriedades `DependencyTelemetry` necessárias para correlação e algumas outras propriedades (carimbo de data/hora de início, duração).
- Definir outras propriedades personalizadas no `DependencyTelemetry`: tais como nome e qualquer outro contexto necessário.
- Fazer uma chamada de dependência e esperar por ela.
- Interromper a operação com `StopOperation` quando concluída.
- Tratar exceções.

`StopOperation` somente interrompe a operação que foi iniciada. Se a operação de execução atual não corresponder à que você deseja interromper, `StopOperation` não fará nada. Essa situação acontecer se você iniciar várias operações em paralelo no mesmo contexto de execução:

```C#
var firstOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 1");
var firstOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 1");
var firstTask = RunMyTaskAsync();

var secondOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 2");
var secondTask = RunMyTaskAsync();

await firstTask;

// This will do nothing and will not report telemetry for the first operation
// as currently secondOperation is active.
telemetryClient.StopOperation(firstOperation); 

await secondTask;
```

Certifique-se de sempre chamar `StartOperation` e executar a tarefa em seu próprio contexto:
```C#
public async Task RunMyTaskAsync()
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("task 1");
    try 
    {
        var myTask = await StartMyTaskAsync();
        // Update status code and success as appropriate.
    }
    catch(...) 
    {
        // Update status code and success as appropriate.
    }
    finally 
    {
        telemetryClient.StopOperation(operation);
    }
}
```

## <a name="next-steps"></a>Próximas etapas

- Aprenda noções básicas de [correlação de telemetria](application-insights-correlation.md) no Application Insights.
- Consulte o [modelo de dados](application-insights-data-model.md) para modelo de dados e tipos do Application Insights.
- Relate [eventos e métricas](app-insights-api-custom-events-metrics.md) personalizados para o Application Insights.
- Confira a [configuração](app-insights-configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet) padrão para a coleção de propriedades de contexto.
- Confira o [Guia do Usuário de System.Diagnostics.Activity](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) para ver como correlacionar telemetria.
