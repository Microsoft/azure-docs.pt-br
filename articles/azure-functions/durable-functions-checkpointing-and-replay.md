---
title: "Pontos de verificação e reprodução nas Funções Duráveis – Azure"
description: "Saiba como é o funcionamento dos pontos de verificação e da reprodução na extensão de Funções Duráveis do Azure Functions."
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 28c589b6821526fa6b91dc558a08ef2fb68f97df
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="checkpoints-and-replay-in-durable-functions-azure-functions"></a>Pontos de verificação e reprodução nas Funções Duráveis (Azure Functions)

Um dos principais atributos das Funções Duráveis é sua **execução confiável**. Funções de orquestrador e funções de atividade podem estar em execução em diferentes VMs em um data center e essas VMs ou a infraestrutura de rede subjacente não é 100% confiável.

Apesar disso, as Funções Duráveis garantem a execução confiável das orquestrações. Isso é feito usando filas de armazenamento para fazer a invocação da função e verificando periodicamente o histórico de execução em tabelas de armazenamento (usando um padrão de design de nuvem conhecido como [Fornecimento de Eventos](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing)). Esse histórico, em seguida, pode ser reproduzido para recriar automaticamente o estado na memória de uma função de orquestrador.

## <a name="orchestration-history"></a>Histórico de orquestração

Digamos que você tenha a seguinte função de orquestrador.

```csharp
[FunctionName("E1_HelloSequence")]
public static async Task<List<string>> Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    var outputs = new List<string>();

    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Tokyo"));
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Seattle"));
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "London"));

    // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
    return outputs;
}
```

Em cada instrução `await`, o Framework de Tarefa Durável verifica o estado de execução da função no armazenamento de tabela. Esse estado é o que chamamos de *histórico de orquestração*.

## <a name="history-table"></a>Tabela de histórico

De modo geral, o Framework de Tarefa Durável faz o seguinte em cada ponto de verificação:

1. Salva o histórico de execução em tabelas do Armazenamento do Azure.
2. Enfileira mensagens para as funções que o orquestrador quer invocar.
3. Enfileira mensagens para o próprio orquestrador &mdash; por exemplo, mensagens de temporizador durável.

Quando o ponto de verificação for concluído, a função de orquestrador estará livre para ser removida da memória até que tenha mais trabalho a fazer.

> [!NOTE]
> O Armazenamento do Azure não oferece nenhuma garantia transacional entre salvar dados em filas e no armazenamento de tabelas. Para lidar com falhas, o provedor de armazenamento das Funções Duráveis usa padrões de *consistência eventual*. Esses padrões garantem que nenhum dado seja perdido se houver uma falha ou perda de conectividade no meio de um ponto de verificação.

Após a conclusão, o histórico da função mostrado anteriormente se parece com o seguinte no Armazenamento de Tabelas do Azure (abreviado para fins de ilustração):

| PartitionKey (InstanceId)                     | EventType             | Timestamp               | Entrada | Nome             | Result                                                    | Status | 
|----------------------------------|-----------------------|----------|--------------------------|-------|------------------|-----------------------------------------------------------|---------------------| 
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:32.362Z |       |                  |                                                           |                     | 
| eaee885b | ExecutionStarted      | 2017-05-05T18:45:28.852Z | nulo  | E1_HelloSequence |                                                           |                     | 
| eaee885b | TaskScheduled         | 2017-05-05T18:45:32.670Z |       | E1_SayHello      |                                                           |                     | 
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:32.670Z |       |                  |                                                           |                     | 
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.232Z |       |                  |                                                           |                     | 
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.201Z |       |                  | """Hello Tokyo!"""                                        |                     | 
| eaee885b | TaskScheduled         | 2017-05-05T18:45:34.435Z |       | E1_SayHello      |                                                           |                     | 
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:34.435Z |       |                  |                                                           |                     | 
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.857Z |       |                  |                                                           |                     | 
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.763Z |       |                  | """Hello Seattle!"""                                      |                     | 
| eaee885b | TaskScheduled         | 2017-05-05T18:45:34.857Z |       | E1_SayHello      |                                                           |                     | 
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:34.857Z |       |                  |                                                           |                     | 
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:35.032Z |       |                  |                                                           |                     | 
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.919Z |       |                  | """Hello London!"""                                       |                     | 
| eaee885b | ExecutionCompleted    | 2017-05-05T18:45:35.044Z |       |                  | "[""Hello Tokyo!"",""Hello Seattle!"",""Hello London!""]" | Concluído           | 
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:35.044Z |       |                  |                                                           |                     | 

Algumas observações sobre os valores das colunas:
* **PartitionKey**: contém a ID de instância da orquestração.
* **EventType**: representa o tipo do evento. Pode ser um dos seguintes tipos:
    * **OrchestrationStarted**: a função de orquestrador, retomada após um período de espera ou executada pela primeira vez. A coluna `Timestamp` é usada para popular o valor determinístico para a API [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime).
    * **ExecutionStarted**: a função de orquestrador que começou a ser executada pela primeira vez. Esse evento também contém a entrada da função na coluna `Input`.
    * **TaskScheduled**: uma função de atividade foi agendada. O nome da função de atividade é capturado na coluna `Name`.
    * **TaskCompleted**: uma função de atividade foi concluída. O resultado da função está na coluna `Result`.
    * **TimerCreated**: um temporizador durável foi criado. A coluna `FireAt` contém a hora, em UTC, agendada para o temporizador expirar.
    * **TimerFired**: um temporizador durável expirou.
    * **EventRaised**: um evento externo foi enviado para a instância de orquestração. A coluna `Name` captura o nome do evento e a coluna `Input` captura sua carga.
    * **OrchestratorCompleted**: a função de orquestrador esperou.
    * **ContinueAsNew**: a função de orquestrador foi concluída e reinicializou a si mesma com estado de nova. A coluna `Result` contém o valor, que é usado como a entrada na instância reiniciada.
    * **ExecutionCompleted**: a função de orquestrador foi executada até a conclusão (ou falhou). As saídas da função ou os detalhes do erro são armazenados na coluna `Result`.
* **Timestamp**: o carimbo de data/hora, em UTC, do evento do histórico.
* **Name**: o nome da função que foi invocada.
* **Input**: a entrada da função formatada em JSON.
* **Output**: a saída da função, ou seja, o valor retornado.

> [!WARNING]
> Embora ela seja útil como uma ferramenta de depuração, não dependa desta tabela. Ela pode mudar à medida que a extensão de Funções Duráveis evoluir.

Toda vez que a função for retomada de um `await`, o Framework de Tarefa Durável executará novamente a função de orquestrador do zero. A cada nova execução, ele consulta o histórico de execução para determinar se a operação assíncrona atual ocorreu.  Se a operação tiver ocorrido, a estrutura repetirá a saída dessa operação imediatamente e passará para o próximo `await`. Esse processo continua até que todo o histórico tenha sido reproduzido. Nesse ponto, todas as variáveis locais na função de orquestrador são restauradas para os valores anteriores.

## <a name="orchestrator-code-constraints"></a>Restrições de código do orquestrador

O comportamento de reprodução cria restrições quanto ao tipo do código que pode ser escrito em uma função de orquestrador:

* O código do orquestrador deve ser **determinístico**. Ele será reproduzido várias vezes e deve produzir o mesmo resultado toda vez. Por exemplo, sem fazer chamadas diretas para obter a data/hora atual, obter números aleatórios, gerar GUIDs aleatórios ou chamar pontos de extremidade remotos.

  Se o código do orquestrador precisar obter a data/hora atual, ele deverá usar a API [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime), que é segura para reprodução.

  Operações não determinísticas devem ser feitas nas funções de atividade. Isso inclui qualquer interação com outras associações de entrada ou saída. Isso garante que qualquer valor não determinístico será gerado uma vez na primeira execução e salvo no histórico de execução. As execuções subsequentes usarão o valor salvo automaticamente.

* O código de orquestrador deve ser **sem bloqueio**. Por exemplo, nada de `Thread.Sleep` ou APIs equivalentes.

  Se o orquestrador precisar de um atraso, ele poderá usar a API [CreateTimer](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CreateTimer_).

* O código do orquestrador **nunca deve iniciar nenhuma operação assíncrona**, exceto usando a API [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html). Por exemplo, nada de `Task.Run`, `Task.Delay` ou `HttpClient.SendAsync`. O Framework de Tarefa Durável executa o código do orquestrador em um único thread e não pode interagir com outros threads que poderiam ser agendados por outras APIs assíncronas.

* **Loops infinitos devem ser evitados** no código do orquestrador. Como o Framework de Tarefa Durável salva o histórico de execução durante o andamento da função de orquestração, um loop infinito pode fazer com que uma instância do orquestrador fique sem memória. Para cenários com loop infinito, use APIs como [ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_) para reiniciar a execução da função e descartar o histórico de execução anterior.

Embora essas restrições possam parecer assustadoras à primeira vista, na prática elas não são difíceis de executar. O Framework de Tarefa Durável tenta detectar violações das regras acima e lança um `NonDeterministicOrchestrationException`. No entanto, esse comportamento de detecção funciona na base do melhor esforço e você não deve confiar nele.

> [!NOTE]
> Todas essas regras se aplicam somente às funções disparadas pela associação `orchestrationTrigger`. Funções de atividade disparadas pela associação `activityTrigger`, bem como funções que usam a associação `orchestrationClient`, não têm essas limitações.

## <a name="durable-tasks"></a>Tarefas duráveis

> [!NOTE]
> Esta seção descreve os detalhes de implementação interna do Framework de Tarefa Durável. Você pode usar as Funções Duráveis sem conhecer essas informações. Elas destinam-se somente a ajudá-lo a entender o comportamento de reprodução.

Ocasionalmente, tarefas que podem ser esperadas com segurança em funções de orquestrador são chamadas de *tarefas duráveis*. Trata-se de tarefas que são criadas e gerenciadas pelo Framework de Tarefa Durável. São exemplos as tarefas retornadas por `CallActivityAsync`, `WaitForExternalEvent` e `CreateTimer`.

Essas *tarefas duráveis* são gerenciadas internamente usando uma lista de objetos `TaskCompletionSource`. Durante a reprodução, essas tarefas são criadas como parte da execução do código do orquestrador e são concluídas conforme o dispatcher enumera os eventos de histórico correspondentes. Tudo isso é feito de forma sincronizada usando um único thread até que todo o histórico tenha sido reproduzido. No caso de tarefas duráveis que não forem concluídas até o término da reprodução do histórico, ações apropriadas serão executadas. Por exemplo, uma mensagem pode ser enfileirada para chamar uma função de atividade.

O comportamento de execução descrito aqui deve ajudar você a entender por que o código de função de orquestrador nunca deve `await` uma tarefa não durável: o thread de dispatcher não pode aguardar sua conclusão e qualquer retorno de chamada por essa tarefa tem o potencial de corromper o estado de acompanhamento da função de orquestrador. Algumas verificações de tempo de execução estão em vigor para tentar evitar isso.

Se quiser obter mais informações sobre como o Framework de Tarefa Durável executa funções de orquestrador, a melhor coisa a fazer é consultar o [Código-fonte da Tarefa Durável no GitHub](https://github.com/Azure/durabletask). Em particular, consulte [TaskOrchestrationExecutor.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationExecutor.cs) e [TaskOrchestrationContext.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationContext.cs)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre o gerenciamento de instâncias](durable-functions-instance-management.md)