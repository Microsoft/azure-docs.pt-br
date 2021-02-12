---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/01/2020
ms.author: glenga
ms.openlocfilehash: 2ccff72be66a88b9bf0a5e9eb9c29ade8397804b
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96356186"
---
Os erros gerados em um Azure Functions podem vir de qualquer uma das seguintes origens:

- Uso de [gatilhos e associações](..\articles\azure-functions\functions-triggers-bindings.md) de Azure Functions internos
- Chamadas para APIs de serviços subjacentes do Azure
- Chamadas para pontos de extremidade REST
- Chamadas para bibliotecas de cliente, pacotes ou APIs de terceiros

Seguir as boas práticas de tratamento de erro é importante para evitar a perda de dados ou mensagens perdidas. As práticas recomendadas de tratamento de erros incluem as seguintes ações:

- [Habilitar o Application Insights](../articles/azure-functions/functions-monitoring.md)
- [Usar tratamento de erro estruturado](#use-structured-error-handling)
- [Design para Idempotência](../articles/azure-functions/functions-idempotent.md)
- [Implementar políticas de repetição](#retry-policies-preview) (quando apropriado)

### <a name="use-structured-error-handling"></a>Usar tratamento de erro estruturado

A captura e o registro em log de erros são essenciais para o monitoramento da integridade do seu aplicativo. O nível mais alto de qualquer código de função deve incluir um bloco try/catch. No bloco catch, você pode capturar e registrar erros.

## <a name="retry-policies-preview"></a>Políticas de repetição (visualização)

Uma política de repetição pode ser definida em qualquer função para qualquer tipo de gatilho em seu aplicativo de funções.  A política de repetição executa novamente uma função até a execução bem-sucedida ou até que ocorra o número máximo de repetições.  As políticas de repetição podem ser definidas para todas as funções em um aplicativo ou para funções individuais.  Por padrão, um aplicativo de funções não repetirá mensagens (além dos [gatilhos específicos que têm uma política de repetição na origem do gatilho](#using-retry-support-on-top-of-trigger-resilience)).  Uma política de repetição é avaliada sempre que uma execução resulta em uma exceção não capturada.  Como prática recomendada, você deve capturar todas as exceções em seu código e relançar todos os erros que devem resultar em uma nova tentativa.  Os hubs de eventos e os pontos de verificação de Azure Cosmos DB não serão gravados até que a política de repetição para a execução seja concluída, o que significa que o andamento dessa partição será pausado até que o lote atual seja concluído.

### <a name="retry-policy-options"></a>Opções de política de repetição

As opções a seguir estão disponíveis para definir uma política de repetição.

**Contagem máxima de repetições** é o número máximo de vezes que uma execução é repetida antes da falha eventual. Um valor de `-1` significa tentar indefinidamente. A contagem de repetições atual é armazenada na memória da instância. É possível que uma instância tenha uma falha entre as tentativas de repetição.  Quando uma instância falha durante uma política de repetição, a contagem de repetição é perdida.  Quando há falhas de instância, os gatilhos como hubs de eventos, Azure Cosmos DB e armazenamento de filas são capazes de retomar o processamento e repetir o lote em uma nova instância, com a contagem de repetição redefinida para zero.  Outros gatilhos, como HTTP e Timer, não reiniciam em uma nova instância.  Isso significa que a contagem máxima de tentativas é um melhor esforço e, em alguns casos raros, uma execução pode ser repetida mais do que o máximo, ou para que gatilhos como HTTP e temporizador sejam repetidos menos do que o máximo.

A **estratégia de repetição** controla como as tentativas se comportam.  A seguir estão duas opções de repetição com suporte:

| Opção | Descrição|
|---|---|
|**`fixedDelay`**| Um período de tempo especificado pode decorrer entre cada repetição,|
| **`exponentialBackoff`**| A primeira tentativa aguarda o atraso mínimo. Em novas tentativas subsequentes, o tempo é adicionado exponencialmente à duração inicial para cada repetição, até que o atraso máximo seja atingido.  Retirada exponencial adiciona alguma pequena aleatoriedade a atrasos para escalonar repetições em cenários de alta taxa de transferência.|

#### <a name="app-level-configuration"></a>Configuração de nível de aplicativo

Uma política de repetição pode ser definida para todas as funções em um aplicativo [usando o `host.json` arquivo](../articles/azure-functions/functions-host-json.md#retry). 

#### <a name="function-level-configuration"></a>Configuração de nível de função

Uma política de repetição pode ser definida para uma função específica.  A configuração específica da função tem prioridade sobre a configuração no nível do aplicativo.

#### <a name="fixed-delay-retry"></a>Repetição de atraso fixa

# <a name="c"></a>[C#](#tab/csharp)

Novas tentativas exigem o pacote NuGet [Microsoft. Azure. webjobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) >= 3.0.23

```csharp
[FunctionName("EventHubTrigger")]
[FixedDelayRetry(5, "00:00:10")]
public static async Task Run([EventHubTrigger("myHub", Connection = "EventHubConnection")] EventData[] events, ILogger log)
{
// ...
}
```

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

Aqui está a política de repetição na *function.jsno* arquivo:

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```
# <a name="javascript"></a>[JavaScript](#tab/javascript)

Aqui está a política de repetição na *function.jsno* arquivo:


```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```

# <a name="python"></a>[Python](#tab/python)

Aqui está a política de repetição na *function.jsno* arquivo:

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```

# <a name="java"></a>[Java](#tab/java)

Aqui está a política de repetição na *function.jsno* arquivo:


```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Aqui está a política de repetição na *function.jsno* arquivo:


```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```
---

#### <a name="exponential-backoff-retry"></a>Repetição de retirada exponencial

# <a name="c"></a>[C#](#tab/csharp)

Novas tentativas exigem o pacote NuGet [Microsoft. Azure. webjobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) >= 3.0.23

```csharp
[FunctionName("EventHubTrigger")]
[ExponentialBackoffRetry(5, "00:00:04", "00:15:00")]
public static async Task Run([EventHubTrigger("myHub", Connection = "EventHubConnection")] EventData[] events, ILogger log)
{
// ...
}
```

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

Aqui está a política de repetição na *function.jsno* arquivo:

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Aqui está a política de repetição na *function.jsno* arquivo:

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```

# <a name="python"></a>[Python](#tab/python)

Aqui está a política de repetição na *function.jsno* arquivo:

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```

# <a name="java"></a>[Java](#tab/java)

Aqui está a política de repetição na *function.jsno* arquivo:

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Aqui está a política de repetição na *function.jsno* arquivo:

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```
---

|Propriedade function.json  |Propriedade de atributo | Descrição |
|---------|---------|---------| 
|usada|N/D|Obrigatórios. Usar qual estratégia de repetição. Os valores válidos são `fixedDelay` ou `exponentialBackoff`.|
|maxRetryCount|N/D|Obrigatórios. O número máximo de repetições permitidas por execução de função. `-1` significa repetir indefinidamente.|
|delayInterval|N/D|O atraso que será usado entre as repetições ao usar a `fixedDelay` estratégia.|
|minimumInterval|N/D|O atraso mínimo de repetição ao usar a `exponentialBackoff` estratégia.|
|maximumInterval|N/D|O atraso máximo de repetição ao usar a `exponentialBackoff` estratégia.| 

### <a name="retry-limitations-during-preview"></a>Limitações de repetição durante a visualização

- Para projetos .NET, talvez seja necessário extrair manualmente uma versão de [Microsoft. Azure. webjobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) >= 3.0.23.
- No plano de consumo, o aplicativo pode ser reduzido para zero enquanto tenta novamente as mensagens finais em uma fila.
- No plano de consumo, o aplicativo pode ser reduzido enquanto executa repetições.  Para obter melhores resultados, escolha um intervalo de repetição <= 00:01:00 e <= 5 repetições.

## <a name="using-retry-support-on-top-of-trigger-resilience"></a>Usando o suporte de repetição na parte superior da resiliência do gatilho

A política de repetição do aplicativo de funções é independente de novas tentativas ou resiliência que o gatilho fornece.  A política de repetição de função só terá uma camada sobre uma repetição resiliente de gatilho.  Por exemplo, se estiver usando o barramento de serviço do Azure, as filas padrão terão uma contagem de entrega de mensagem de 10.  A contagem de entrega padrão significa que, depois de 10 tentativas de entregas de uma mensagem da fila, o barramento de serviço deixará de responder a mensagem.  Você pode definir uma política de repetição para uma função que tenha um gatilho do barramento de serviço, mas as novas tentativas serão a camada sobre as tentativas de entrega do barramento de serviço.  

Por exemplo, se você usou a contagem de entrega de barramento de serviço padrão de 10 e definiu uma política de repetição de função de 5.  A mensagem primeiro removeria da fila, incrementando a conta de entrega do barramento de serviço para 1.  Se todas as execuções falharem, após cinco tentativas para disparar a mesma mensagem, essa mensagem será marcada como abandonada.  O barramento de serviço recolocaria a mensagem na fila imediatamente, dispararia a função e incrementaria a contagem de entrega para 2.  Finalmente, após 50 tentativas eventuales (10 entregas de barramento de serviço * cinco tentativas de função por entrega), a mensagem seria abandonada e dispararia uma inatividade no barramento de serviço.

> [!WARNING]
> Não é recomendável definir a contagem de entrega para um gatilho como filas do barramento de serviço como 1, o que significa que a mensagem seria mensagens mortas imediatamente após um ciclo de repetição de função única.  Isso ocorre porque os gatilhos fornecem resiliência com repetições, enquanto a política de repetição de função é o melhor esforço e pode resultar em menos do que o número total desejado de tentativas.

### <a name="triggers-with-additional-resiliency-or-retries"></a>Gatilhos com resiliência ou novas tentativas adicionais

Os seguintes gatilhos dão suporte a repetições na origem do gatilho:

* [Armazenamento de Blobs do Azure](../articles/azure-functions/functions-bindings-storage-blob.md)
* [Armazenamento de Filas do Azure](../articles/azure-functions/functions-bindings-storage-queue.md)
* [Barramento de Serviço do Azure (fila/tópico)](../articles/azure-functions/functions-bindings-service-bus.md)

Por padrão, a maioria dos gatilhos tenta novamente solicitações até cinco vezes. Após a quinta repetição, o armazenamento de filas do Azure gravará uma mensagem em uma [fila de suspeitas](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#poison-messages).  A política padrão de fila e tópico do barramento de serviço gravará uma mensagem em uma [fila de mensagens mortas](../articles/service-bus-messaging/service-bus-dead-letter-queues.md) após 10 tentativas.
