---
title: Testes de unidade de Funções Duráveis do Azure
description: Saiba como testar a unidade das Funções Duráveis.
ms.topic: conceptual
ms.date: 11/03/2019
ms.openlocfilehash: 89b6419e95b3971b0d272490e19354f300204e1e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103491037"
---
# <a name="durable-functions-unit-testing"></a>Testes de unidade de Funções Duráveis

Os testes de unidade são uma parte importante de modernas práticas de desenvolvimento de software. Testes de unidade verificam o comportamento de lógica de negócios e protegem contra a introdução de alterações com falhas despercebidas no futuro. As Funções Duráveis podem facilmente aumentar a complexidade, então introduzir testes de unidade ajudará a evitar alterações com falha. As seções a seguir explicam como testar a unidade dos três tipos de função: o cliente de orquestração, o Orchestrator e as funções de atividade.

> [!NOTE]
> Este artigo fornece diretrizes para testes de unidade para Durable Functions aplicativos direcionados Durable Functions 2. x. Para obter mais informações sobre as diferenças entre versões, consulte o artigo [Durable Functions versões](durable-functions-versions.md) .

## <a name="prerequisites"></a>Pré-requisitos

Os exemplos neste artigo requerem conhecimento sobre os conceitos e as estruturas a seguir:

* Teste de unidade

* Funções duráveis

* [xUnit](https://github.com/xunit/xunit) - Estrutura de teste

* [moq](https://github.com/moq/moq4) - Estrutura de simulação

## <a name="base-classes-for-mocking"></a>Classes base para a simulação

Há suporte para a simulação por meio da seguinte interface:

* [IDurableOrchestrationClient](/dotnet/api/microsoft.azure.webjobs.IDurableOrchestrationClient), [IDurableEntityClient](/dotnet/api/microsoft.azure.webjobs.IDurableEntityClient) e [IDurableClient](/dotnet/api/microsoft.azure.webjobs.IDurableClient)

* [IDurableOrchestrationContext](/dotnet/api/microsoft.azure.webjobs.IDurableOrchestrationContext)

* [IDurableActivityContext](/dotnet/api/microsoft.azure.webjobs.IDurableActivityContext)
  
* [IDurableEntityContext](/dotnet/api/microsoft.azure.webjobs.IDurableEntityContext)

Essas interfaces podem ser usadas com vários gatilhos e associações com suporte pelo Durable Functions. Ao executar o Azure Functions, o tempo de execução do Functions executará o código de função com uma implementação concreta dessas interfaces. Para testes de unidade, você pode passar uma versão fictícia dessas interfaces para testar sua lógica de negócios.

## <a name="unit-testing-trigger-functions"></a>Testes de unidade das funções de gatilho

Nesta seção, o teste de unidade validará a lógica da seguinte função de gatilho HTTP para iniciar nova orquestrações.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

A tarefa de teste de unidade será verificar o valor do cabeçalho `Retry-After` fornecido na carga de resposta. Portanto, o teste de unidade simulará alguns `IDurableClient` métodos para garantir um comportamento previsível.

Primeiro, usamos uma estrutura fictícia ([MOQ](https://github.com/moq/moq4) , neste caso) para simulação `IDurableClient` :

```csharp
// Mock IDurableClient
var durableClientMock = new Mock<IDurableClient>();
```

> [!NOTE]
> Embora você possa simular interfaces implementando diretamente a interface como uma classe, as estruturas fictícias simplificam o processo de várias maneiras. Por exemplo, se um novo método for adicionado à interface em versões secundárias, o MOQ não exigirá nenhuma alteração de código diferentemente das implementações concretas.

Em seguida, o método `StartNewAsync` é simulado para retornar uma ID de instância conhecida.

```csharp
// Mock StartNewAsync method
durableClientMock.
    Setup(x => x.StartNewAsync(functionName, It.IsAny<object>())).
    ReturnsAsync(instanceId);
```

Em seguida, `CreateCheckStatusResponse` é simulado para sempre retornar uma resposta HTTP 200 vazia.

```csharp
// Mock CreateCheckStatusResponse method
durableClientMock
    // Notice that even though the HttpStart function does not call IDurableClient.CreateCheckStatusResponse() 
    // with the optional parameter returnInternalServerErrorOnFailure, moq requires the method to be set up
    // with each of the optional parameters provided. Simply use It.IsAny<> for each optional parameter
    .Setup(x => x.CreateCheckStatusResponse(It.IsAny<HttpRequestMessage>(), instanceId, returnInternalServerErrorOnFailure: It.IsAny<bool>())
    .Returns(new HttpResponseMessage
    {
        StatusCode = HttpStatusCode.OK,
        Content = new StringContent(string.Empty),
        Headers =
        {
            RetryAfter = new RetryConditionHeaderValue(TimeSpan.FromSeconds(10))
        }
    });
```

`ILogger` também é simulado:

```csharp
// Mock ILogger
var loggerMock = new Mock<ILogger>();
```  

Agora o método `Run` é chamado do teste de unidade:

```csharp
// Call Orchestration trigger function
var result = await HttpStart.Run(
    new HttpRequestMessage()
    {
        Content = new StringContent("{}", Encoding.UTF8, "application/json"),
        RequestUri = new Uri("http://localhost:7071/orchestrators/E1_HelloSequence"),
    },
    durableClientMock.Object,
    functionName,
    loggerMock.Object);
 ```

 A última etapa é comparar o resultado com o valor esperado:

```csharp
// Validate that output is not null
Assert.NotNull(result.Headers.RetryAfter);

// Validate output's Retry-After header value
Assert.Equal(TimeSpan.FromSeconds(10), result.Headers.RetryAfter.Delta);
```

Após a combinação de todas as etapas, o teste de unidade terá o código a seguir:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HttpStartTests.cs)]

## <a name="unit-testing-orchestrator-functions"></a>Testes de unidade das funções de orquestrador

As Funções do Orchestrator são ainda mais interessantes para testes de unidade, uma vez que normalmente têm muito mais lógica de negócios.

Nesta seção, os testes de unidade validarão a saída da função do Orchestrator `E1_HelloSequence`:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

O código de teste de unidade será iniciado com a criação de uma simulação:

```csharp
var durableOrchestrationContextMock = new Mock<IDurableOrchestrationContext>();
```

Em seguida, as chamadas de método de atividade serão simuladas:

```csharp
durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Tokyo")).ReturnsAsync("Hello Tokyo!");
durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Seattle")).ReturnsAsync("Hello Seattle!");
durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "London")).ReturnsAsync("Hello London!");
```

Em seguida, o teste de unidade chamará o método `HelloSequence.Run`:

```csharp
var result = await HelloSequence.Run(durableOrchestrationContextMock.Object);
```

E, finalmente, a saída será validada:

```csharp
Assert.Equal(3, result.Count);
Assert.Equal("Hello Tokyo!", result[0]);
Assert.Equal("Hello Seattle!", result[1]);
Assert.Equal("Hello London!", result[2]);
```

Após a combinação de todas as etapas, o teste de unidade terá o código a seguir:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceOrchestratorTests.cs)]

## <a name="unit-testing-activity-functions"></a>Testes de unidade das funções de atividade

As funções de atividade podem ter unidades testadas da mesma maneira que funções não duráveis.

Nesta seção, o teste de unidade validará o comportamento da função de Atividade `E1_SayHello`:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

E os testes de unidade verificarão o formato da saída. Os testes de unidade podem usar os tipos de parâmetro diretamente ou a `IDurableActivityContext` classe fictícia:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceActivityTests.cs)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre xUnit](https://xunit.net/docs/getting-started/netcore/cmdline)
> 
> [Saiba mais sobre moq](https://github.com/Moq/moq4/wiki/Quickstart)
