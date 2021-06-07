---
title: Orquestrações Duráveis – Azure Functions
description: Introdução ao recurso de orquestração das Durable Functions do Azure.
author: cgillum
ms.topic: overview
ms.date: 09/08/2019
ms.author: azfuncdf
ms.openlocfilehash: ba314963058389e171601407ff00411049eecd45
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97845427"
---
# <a name="durable-orchestrations"></a>Orquestrações Duráveis

As Durable Functions são uma extensão do [Azure Functions](../functions-overview.md). É possível usar uma *função funções de orquestrador* para orquestrar a execução de outras Durable Functions em um aplicativo de funções. As funções de orquestrador têm as seguintes características:

* As funções de orquestrador definem fluxos de trabalho de função que usam o código do procedimento. Não são necessários esquemas declarativos ou designers.
* As funções de orquestrador podem chamar outras Durable Functions de maneira síncrona e assíncrona. A saída das funções chamadas pode ser salva em variáveis locais com confiança.
* As funções de orquestrador são duráveis e confiáveis. O progresso da execução é marcado automaticamente com um ponto de verificação quando a função “aguarda” ou “gera”. O estado local nunca é perdido quando o processo é reciclado ou a VM é reiniciada.
* As funções de orquestrador podem ser de longa duração. O ciclo de vida total de uma *instância de orquestração* pode ser de segundos, dias, meses ou interminável.

Este artigo oferece uma visão geral das funções de orquestrador e como elas podem ajudar a resolver vários desafios de desenvolvimento do aplicativo. Se você ainda não está familiarizado com os tipos de funções disponíveis em um aplicativo Durable Functions, leia primeiro o artigo sobre [tipos de Funções Duráveis](durable-functions-types-features-overview.md).

## <a name="orchestration-identity"></a>Identidade de orquestração

Cada *instância* de uma orquestração tem um identificador de instância (também conhecido como *ID de uma instância*). Por padrão, cada ID de instância é um GUID gerado automaticamente. No entanto, as IDs de instância também podem ser qualquer valor de cadeia de caracteres gerado pelo usuário. Cada ID de instância de orquestração deve ser exclusiva dentro de um [hub de tarefas](durable-functions-task-hubs.md).

A seguir, há algumas regras sobre IDs de instância:

* As IDs de instância devem ter entre 1 e 256 caracteres.
* As IDs de instância não devem começar com `@`.
* As IDs de instância não devem conter `/`, `\`, `#` ou `?` caracteres.
* As IDs de instância não devem conter caracteres de controle.

> [!NOTE]
> Geralmente, é recomendável usar IDs de instância geradas automaticamente sempre que possível. As IDs de instância geradas pelo usuário são destinadas a cenários em que há um mapeamento de um para um entre uma instância de orquestração e alguma entidade externa específica do aplicativo, como uma ordem de compra ou um documento.

A ID da instância de uma orquestração é um parâmetro obrigatório para a maioria das [operações de gerenciamento de instância](durable-functions-instance-management.md). Elas também são importantes para diagnóstico, como [pesquisa por meio de dados de acompanhamento de orquestração](durable-functions-diagnostics.md#application-insights) no Application Insights para fins de solução de problemas ou de análise. Por esse motivo, é recomendável salvar as IDs de instância geradas em alguma localização externa (por exemplo, um banco de dados ou em logs de aplicativo), em que elas podem ser facilmente consultadas mais tarde.

## <a name="reliability"></a>Confiabilidade

As funções de orquestrador mantêm com confiança seu estado de execução usando o padrão de design de [fornecimento de eventos](/azure/architecture/patterns/event-sourcing). Em vez de armazenar diretamente o estado atual de uma orquestração, a Estrutura de Tarefas Duráveis usa um armazenamento somente de acréscimo para registrar a série completa de ações executadas pela orquestração de função. Um armazenamento somente de acréscimo tem muitos benefícios em comparação com o "despejo" do estado de runtime completo. Os benefícios incluem maior desempenho, escalabilidade e capacidade de resposta. Você também obtém consistência eventual para dados transacionais e trilhas e histórico de auditoria completos. As trilhas de auditoria dão suporte a ações de compensação confiáveis.

As Durable Functions usam o fornecimento de eventos de forma transparente. Nos bastidores, o operador `await` (C#) ou `yield` (JavaScript/Python) de uma função de orquestrador leva o controle do thread do orquestrador de volta para o dispatcher da Estrutura de Tarefas Duráveis. O dispatcher, em seguida, confirma novas ações agendadas pela função orquestradora (como chamar uma ou mais funções filho ou agendar um temporizador durável) no armazenamento. A ação de confirmação transparente é acrescentada ao histórico de execução da instância de orquestração. O histórico é armazenado na tabela de armazenamento. A ação de confirmação, em seguida, adiciona mensagens a uma fila para agendar o trabalho de fato. Neste ponto, a função orquestradora pode ser descarregada da memória.

Quando uma função de orquestração recebe mais trabalho a fazer (por exemplo, uma mensagem de resposta é recebida ou um temporizador durável expira), o orquestrador é ativado e executa mais uma vez a função inteira, desde o início, para recompilar o estado local. Durante a reprodução, se o código tentar chamar uma função (ou executar qualquer outro trabalho assíncrono), a Estrutura de Tarefas Duráveis consultará o histórico de execução da orquestração atual. Se ele detectar que a [função de atividade](durable-functions-types-features-overview.md#activity-functions) já foi executada e gerou um resultado, ele reproduzirá o resultado da função e o código do orquestrador continuará em execução. A reprodução continua até que o código da função seja concluído ou até que tenha agendado um novo trabalho assíncrono.

> [!NOTE]
> Para que o padrão de reprodução funcione corretamente e de forma confiável, o código de função funções de orquestrador deve ser *determinístico*. Para obter mais informações sobre restrições de código para funções de orquestrador, confira o tópico [restrições de código de função funções de orquestrador](durable-functions-code-constraints.md).

> [!NOTE]
> Se uma função de orquestrador emitir mensagens de log, o comportamento da reprodução poderá causar mensagens de erro duplicadas a serem emitidas. Confira o tópico [Log](durable-functions-diagnostics.md#app-logging) para saber mais sobre por que esse comportamento ocorre e como resolvê-lo.

## <a name="orchestration-history"></a>Histórico de orquestração

O comportamento de fornecimento de eventos da Estrutura de Tarefas Duráveis está intimamente acoplado ao código da função funções de orquestrador que você escreve. Suponha que você tenha uma função de orquestrador de encadeamento de atividades, como a seguinte função de orquestrador:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("E1_HelloSequence")]
public static async Task<List<string>> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var outputs = new List<string>();

    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Tokyo"));
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Seattle"));
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "London"));

    // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
    return outputs;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const output = [];
    output.push(yield context.df.callActivity("E1_SayHello", "Tokyo"));
    output.push(yield context.df.callActivity("E1_SayHello", "Seattle"));
    output.push(yield context.df.callActivity("E1_SayHello", "London"));

    // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
    return output;
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    result1 = yield context.call_activity('SayHello', "Tokyo")
    result2 = yield context.call_activity('SayHello', "Seattle")
    result3 = yield context.call_activity('SayHello', "London")
    return [result1, result2, result3]

main = df.Orchestrator.create(orchestrator_function)
```
---

Em cada instrução `await` (C#) ou `yield` (JavaScript/Python), a Estrutura de Tarefas Duráveis verifica o estado da execução da função em algum back-end de armazenamento durável (normalmente, o armazenamento de Tabela do Azure). Esse estado é o que chamamos de *histórico de orquestração*.

### <a name="history-table"></a>Tabela de histórico

De modo geral, o Framework de Tarefa Durável faz o seguinte em cada ponto de verificação:

1. Salva o histórico de execução em tabelas do Armazenamento do Azure.
2. Enfileira mensagens para as funções que o orquestrador quer invocar.
3. Enfileira mensagens para o próprio orquestrador &mdash; por exemplo, mensagens de temporizador durável.

Quando o ponto de verificação for concluído, a função de orquestrador estará livre para ser removida da memória até que tenha mais trabalho a fazer.

> [!NOTE]
> O Armazenamento do Azure não oferece nenhuma garantia transacional entre salvar dados em filas e no armazenamento de tabelas. Para lidar com falhas, o provedor de armazenamento das Funções Duráveis usa padrões de *consistência eventual*. Esses padrões garantem que nenhum dado seja perdido se houver uma falha ou perda de conectividade no meio de um ponto de verificação.

Após a conclusão, o histórico da função mostrado anteriormente se parece com a seguinte tabela no Armazenamento de Tabelas do Azure (abreviado para fins de ilustração):

| PartitionKey (InstanceId)                     | EventType             | Timestamp               | Entrada | Nome             | Result                                                    | Status |
|----------------------------------|-----------------------|----------|--------------------------|-------|------------------|-----------------------------------------------------------|
| eaee885b | ExecutionStarted      | 2017-05-05T18:45:28.852Z | nulo  | E1_HelloSequence |                                                           |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:32.362Z |       |                  |                                                           |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:32.670Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:32.670Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.201Z |       |                  | """Hello Tokyo!"""                                        |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.232Z |       |                  |                                                           |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:34.435Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:34.435Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.763Z |       |                  | """Hello Seattle!"""                                      |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.857Z |       |                  |                                                           |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:34.857Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:34.857Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.919Z |       |                  | """Hello London!"""                                       |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:35.032Z |       |                  |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:35.044Z |       |                  |                                                           |                     |
| eaee885b | ExecutionCompleted    | 2017-05-05T18:45:35.044Z |       |                  | "[""Hello Tokyo!"",""Hello Seattle!"",""Hello London!""]" | Concluído           |

Algumas observações sobre os valores das colunas:

* **PartitionKey**: Contém a ID de instância da orquestração.
* **EventType**: Representa o tipo do evento. Pode ser um dos seguintes tipos:
  * **OrchestrationStarted**: A função de orquestrador retomada após um período de espera ou executada pela primeira vez. A coluna `Timestamp` é usada para preencher o valor determinístico das APIs `CurrentUtcDateTime` (.NET), `currentUtcDateTime` (JavaScript) e `current_utc_datetime` (Python).
  * **ExecutionStarted**: A função de orquestrador que começou a ser executada pela primeira vez. Esse evento também contém a entrada da função na coluna `Input`.
  * **TaskScheduled**: Uma função de atividade foi agendada. O nome da função de atividade é capturado na coluna `Name`.
  * **TaskCompleted**: Uma função de atividade foi concluída. O resultado da função está na coluna `Result`.
  * **TimerCreated**: Um temporizador durável foi criado. A coluna `FireAt` contém a hora, em UTC, agendada para o temporizador expirar.
  * **TimerFired**: Um temporizador durável foi disparado.
  * **EventRaised**: Um evento externo foi enviado para a instância de orquestração. A coluna `Name` captura o nome do evento e a coluna `Input` captura sua carga.
  * **OrchestratorCompleted**: Uma função de orquestrador é aguardada.
  * **ContinueAsNew**: A função de orquestrador foi concluída e reinicializou-se com novo estado. A coluna `Result` contém o valor, que é usado como a entrada na instância reiniciada.
  * **ExecutionCompleted**: A função de orquestrador foi executada até a conclusão (ou falhou). As saídas da função ou os detalhes do erro são armazenados na coluna `Result`.
* **Timestamp**: O carimbo de data/hora, em UTC, do evento do histórico.
* **Name**: O nome da função que foi invocada.
* **Entrada**: A entrada da função formatada em JSON.
* **Result**: A saída da função, ou seja, o valor retornado.

> [!WARNING]
> Embora ela seja útil como uma ferramenta de depuração, não dependa desta tabela. Ela pode mudar à medida que a extensão de Funções Duráveis evoluir.

Toda vez que a função for retomada de um `await` (C#) ou `yield` (JavaScript/Python), a Estrutura de Tarefas Duráveis executará novamente a função de orquestrador desde o início. A cada nova execução, ele consulta o histórico de execução para determinar se a operação assíncrona atual ocorreu.  Se a operação tiver ocorrido, a estrutura repetirá a saída da operação imediatamente e passará para o próximo `await` (C#) ou `yield` (JavaScript/Python). Esse processo continua até que todo o histórico tenha sido reproduzido. Depois que o histórico atual tiver sido reproduzido, as variáveis locais terão sido restauradas para seus valores anteriores.

## <a name="features-and-patterns"></a>Recursos e padrões

As seções a seguir descrevem os recursos e padrões de funções de orquestrador.

### <a name="sub-orchestrations"></a>Subtipo de orquestrações

As funções de orquestrador podem chamar funções de atividade, mas também outras funções de orquestrador. Por exemplo, você pode criar uma orquestração maior usando uma biblioteca de funções de orquestrador. Ou pode executar várias instâncias de uma função de orquestrador em paralelo.

Para obter mais informações e exemplos, confira o artigo [Suborquestrações](durable-functions-sub-orchestrations.md).

### <a name="durable-timers"></a>Temporizadores duráveis

As orquestrações podem agendar *temporizadores duráveis* para implementar atrasos ou configurar a manipulação de tempo limite em ações assíncronas. Use temporizadores duráveis em funções de orquestrador em vez de `Thread.Sleep` e `Task.Delay` (C#) ou `setTimeout()` e `setInterval()` (JavaScript) ou `time.sleep()` (Python).

Para obter mais informações e exemplos, confira o artigo [Temporizadores duráveis](durable-functions-timers.md).

### <a name="external-events"></a>Eventos externos

As funções de orquestrador podem aguardar os eventos externos atualizarem uma instância de orquestração. O recurso Durable Functions geralmente é útil para manipular uma interação humana ou outros retornos de chamada externos.

Para obter mais informações e exemplos, confira o artigo [Eventos externos](durable-functions-external-events.md).

### <a name="error-handling"></a>Tratamento de erros

As funções de orquestrador podem usar os recursos de tratamento de erro da linguagem de programação. No código de orquestração, há suporte para padrões existentes, como `try`/`catch`.

As funções de orquestrador também podem adicionar políticas de repetição à atividade ou às funções de suborquestrador que elas chamam. Se uma atividade ou função de suborquestrador falhar com uma exceção, a política de repetição especificada poderá atrasar automaticamente e repetir a execução até um número especificado de vezes.

> [!NOTE]
> Se houver uma exceção sem tratamento em uma função funções de orquestrador, a instância de orquestração será concluída em um estado `Failed`. Uma instância de orquestração não poderá ser repetida após ter falhado.

Para obter mais informações e exemplos, confira o artigo [Tratamento de erro](durable-functions-error-handling.md).

### <a name="critical-sections-durable-functions-2x-currently-net-only"></a>Seções críticas (Durable Functions 2.x, atualmente somente no .NET)

As instâncias de orquestração têm thread único para que não seja preciso se preocupar com condições de corrida *dentro* de uma orquestração. No entanto, as condição de corrida são possíveis quando as orquestrações interagem com sistemas externos. Para atenuar as condições de corrida ao interagir com sistemas externos, as funções de orquestrador podem definir *seções críticas* usando um método `LockAsync` no .NET.

O exemplo de código a seguir mostra uma função funções de orquestrador que define uma seção crítica. Ele entra na seção crítica usando o método `LockAsync`. Esse método requer a passagem de uma ou mais referências a uma [Entidade Durável](durable-functions-entities.md), que gerencia de maneira durável o estado de bloqueio. Somente uma única instância dessa orquestração pode executar o código na seção crítica por vez.

```csharp
[FunctionName("Synchronize")]
public static async Task Synchronize(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var lockId = new EntityId("LockEntity", "MyLockIdentifier");
    using (await context.LockAsync(lockId))
    {
        // critical section - only one orchestration can enter at a time
    }
}
```

O `LockAsync` adquire os bloqueios duráveis e retorna um `IDisposable` que encerra a seção crítica quando descartado. Esse resultado `IDisposable` pode ser usado junto com um bloco `using` para obter uma representação sintática da seção crítica. Quando uma função funções de orquestrador entra em uma seção crítica, apenas uma instância pode executar esse bloco de código. Outras instâncias que tentarem entrar na seção crítica serão bloqueadas até que a instância anterior saia da seção crítica.

O recurso de seção crítica também é útil para coordenar alterações em entidades duráveis. Para obter informações sobre seções críticas, confira o tópico [“Coordenação de entidade” de entidades duráveis](durable-functions-entities.md#entity-coordination).

> [!NOTE]
> As seções críticas estão disponíveis nas Durable Functions 2.0 e superiores. No momento, apenas orquestrações do .NET implementam esse recurso.

### <a name="calling-http-endpoints-durable-functions-2x"></a>Chamar pontos de extremidade HTTP (Durable Functions 2.x)

As funções de orquestrador não têm permissão para realizar E/S conforme descrito nas [restrições de código da função funções de orquestrador](durable-functions-code-constraints.md). A alternativa típica para essa limitação é encapsular o código que precisa realizar E/S em uma função de atividade. As orquestrações que interagem com sistemas externos frequentemente usam funções de atividade para realizar chamadas HTTP e retornar o resultado à orquestração.

# <a name="c"></a>[C#](#tab/csharp)

Para simplificar esse padrão comum, as funções de orquestrador podem usar o método `CallHttpAsync` para invocar APIs HTTP diretamente.

```csharp
[FunctionName("CheckSiteAvailable")]
public static async Task CheckSiteAvailable(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    Uri url = context.GetInput<Uri>();

    // Makes an HTTP GET request to the specified endpoint
    DurableHttpResponse response = 
        await context.CallHttpAsync(HttpMethod.Get, url);

    if (response.StatusCode >= 400)
    {
        // handling of error codes goes here
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const url = context.df.getInput();
    var res = yield context.df.callHttp("GET", url);
    if (res.statusCode >= 400) {
        // handling of error codes goes here
    }
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    url = context.get_input()
    res = yield context.call_http('GET', url)
    if res.status_code >= 400:
        # handing of error code goes here
```
---

Além de dar suporte a padrões básicos de solicitação/resposta, o método dá suporte ao tratamento automático de padrões de sondagem HTTP 202 assíncronos comuns e à autenticação com serviços externos usando [identidades gerenciadas](../../active-directory/managed-identities-azure-resources/overview.md).

Para obter mais informações e exemplos detalhados, confira o artigo [Recursos HTTP](durable-functions-http-features.md).

> [!NOTE]
> Chamar pontos de extremidade HTTP diretamente de funções de orquestrador está disponível nas Durable Functions 2.0 e superiores.

### <a name="passing-multiple-parameters"></a>Passando vários parâmetros

Não é possível passar vários parâmetros para uma função de atividade diretamente. A recomendação é transmitir uma matriz de objetos ou objetos de composição.

# <a name="c"></a>[C#](#tab/csharp)

No .NET, use também objetos [ValueTuple](/dotnet/csharp/tuples). O seguinte exemplo usa os novos recursos de [ValueTuple](/dotnet/csharp/tuples) adicionados com [C# 7](/dotnet/csharp/whats-new/csharp-7#tuples):

```csharp
[FunctionName("GetCourseRecommendations")]
public static async Task<object> RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string major = "ComputerScience";
    int universityYear = context.GetInput<int>();

    object courseRecommendations = await context.CallActivityAsync<object>(
        "CourseRecommendations",
        (major, universityYear));
    return courseRecommendations;
}

[FunctionName("CourseRecommendations")]
public static async Task<object> Mapper([ActivityTrigger] IDurableActivityContext inputs)
{
    // parse input for student's major and year in university
    (string Major, int UniversityYear) studentInfo = inputs.GetInput<(string, int)>();

    // retrieve and return course recommendations by major and university year
    return new
    {
        major = studentInfo.Major,
        universityYear = studentInfo.UniversityYear,
        recommendedCourses = new []
        {
            "Introduction to .NET Programming",
            "Introduction to Linux",
            "Becoming an Entrepreneur"
        }
    };
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

#### <a name="orchestrator"></a>Orchestrator

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const location = {
        city: "Seattle",
        state: "WA"
    };
    const weather = yield context.df.callActivity("GetWeather", location);

    // ...
};
```

#### <a name="getweather-activity"></a>`GetWeather` Atividade

```javascript
module.exports = async function (context, location) {
    const {city, state} = location; // destructure properties into variables

    // ...
};
```

# <a name="python"></a>[Python](#tab/python)

#### <a name="orchestrator"></a>Orchestrator

```python
from collections import namedtuple
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    Location = namedtuple('Location', ['city', 'state'])
    location = Location(city='Seattle', state= 'WA')

    weather = yield context.call_activity("GetWeather", location)

    # ...

```
#### <a name="getweather-activity"></a>`GetWeather` Atividade

```python
from collections import namedtuple

Location = namedtuple('Location', ['city', 'state'])

def main(location: Location) -> str:
    city, state = location
    return f"Hello {city}, {state}!"
```

---

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Restrições de código do orquestrador](durable-functions-code-constraints.md)
