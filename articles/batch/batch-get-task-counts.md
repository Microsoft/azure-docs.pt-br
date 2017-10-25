---
title: "Monitorar o andamento de um trabalho pela contagem de tarefas por estado – Lote do Azure | Microsoft Docs"
description: "Monitorar o andamento de um trabalho chamando a operação Obter Contagens de Tarefas para contar as tarefas de um trabalho. Você pode obter uma contagem de tarefas ativas, em execução e concluídas e das tarefas que tiveram êxito ou falharam."
services: batch
author: tamram
manager: timlt
ms.service: batch
ms.topic: article
ms.date: 08/02/2017
ms.author: tamram
ms.openlocfilehash: ceff59d7063b60a1344a47489d3d73e0e8ee07df
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="count-tasks-by-state-to-monitor-a-jobs-progress-preview"></a>Contagem de tarefas por estado para monitorar o andamento de um trabalho (versão prévia)

O Lote do Azure fornece uma maneira eficiente para monitorar o progresso de um trabalho conforme ele executa suas tarefas. Você pode chamar a operação [Obter Contagens de Tarefas][rest_get_task_counts] para descobrir quantas tarefas estão em um estado ativo, em execução ou concluído e quantas tiveram êxito ou falharam. Contando o número de tarefas em cada estado, você pode exibir mais facilmente o andamento do trabalho para um usuário ou detectar atrasos inesperados ou falhas que podem afetar o trabalho.

> [!IMPORTANT]
> A operação Obter Contagens de Tarefas está atualmente em versão prévia e ainda não está disponível no Azure Governamental, no Azure China e no Azure Alemanha. 
>
>

## <a name="how-tasks-are-counted"></a>Como as tarefas são contadas

A operação Obter Contagens de Tarefas conta tarefas por estado, da seguinte maneira:

- Uma tarefa é contabilizada como **ativa** quando está na fila e é capaz de executar, mas não está atualmente atribuída a um nó de computação. Uma tarefa também é contada como **ativa** se depende de uma tarefa pai que ainda não foi concluída. Para obter mais informações sobre dependências de tarefas, consulte [Criar dependências de tarefas para executar tarefas que dependem de outras tarefas](batch-task-dependencies.md). 
- Uma tarefa é contabilizada como **executando** quando foi atribuída a um nó de computação, mas ainda não foi concluída. Uma tarefa é contabilizada como **executando** quando seu estado é `preparing` ou `running`, conforme indicado pela operação [Obter informações sobre uma tarefa][rest_get_task].
- Uma tarefa é contabilizada como **concluída** quando não está mais qualificada para executar. Uma tarefa contabilizada como **concluída** normalmente foi concluída com êxito ou foi concluída sem êxito e também esgotou o seu limite de repetições. 

A operação Obter Contagens de Tarefas também informa quantas tarefas tiveram êxito ou falharam. O Lote determina se uma tarefa teve êxito ou falhou ao verificar a propriedade **result** da propriedade [executionInfo] [https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task#executionInfo]:

    - Uma tarefa será contabilizada como **bem-sucedida** se o resultado da execução da tarefa for `success`.
    - Uma tarefa será contabilizada como **com falha** se o resultado da execução da tarefa for `failure`.

Para obter mais informações sobre estados de tarefa, consulte [Obter informações sobre uma tarefa][rest_get_task].

O exemplo de código .NET a seguir mostra como recuperar contagens de tarefas por estado: 

```csharp
var taskCounts = await batchClient.JobOperations.GetTaskCountsAsync("job-1");

Console.WriteLine("Task count in active state: {0}", taskCounts.Active);
Console.WriteLine("Task count in preparing or running state: {0}", taskCounts.Running);
Console.WriteLine("Task count in completed state: {0}", taskCounts.Completed);
Console.WriteLine("Succeeded task count: {0}", taskCounts.Succeeded);
Console.WriteLine("Failed task count: {0}", taskCounts.Failed);
Console.WriteLine("ValidationStatus: {0}", taskCounts.ValidationStatus);
```

> [!NOTE]
> Você pode usar um padrão semelhante para REST e outras linguagens com suporte para obter as contagens de tarefa para um trabalho. 
> 
> 

## <a name="consistency-checking-for-task-counts"></a>Verificação de consistência para contagens de tarefas

O serviço de Lote agrega as contagens de tarefa ao coletar dados de várias partes de um sistema distribuído assíncrono. Para garantir que as contagens de tarefa estão corretas, o Lote fornece validação adicional para contagens de estado executando verificações de consistência em vários componentes do sistema. O Lote executa essas verificações de consistência, como há menos de 200.000 tarefas no trabalho. No evento improvável de a verificação de consistência encontrar erros, o Lote corrige o resultado da operação de obter contagens de tarefas com base nos resultados da verificação de consistência. A verificação de consistência é uma medida extra para garantir que os clientes que utilizam a operação Obter Contagens de Tarefas obtenham as informações corretas de que precisam para a solução deles.

A propriedade **validationStatus** na resposta indica se o Lote executou a verificação de consistência. Se o lote não é capaz de verificar contagens de estado contra os estados reais mantidos no sistema, a propriedade **validationStatus** é definida como `unvalidated`. Por motivos de desempenho, o Lote não executará a verificação de consistência se o trabalho inclui mais de 200.000 tarefas; portanto, a propriedade **validationStatus** pode ser definida como `unvalidated` nesse caso. No entanto, a contagem de tarefas não é necessariamente errada nesse caso, pois até mesmo uma perda de dados muito limitada é muito improvável. 

Quando uma tarefa muda de estado, o pipeline de agregação processa a alteração dentro de alguns segundos. A operação Obter Contagens de Tarefas reflete as contagens de tarefas atualizadas dentro desse período. No entanto, se o pipeline de agregação perder uma alteração em um estado de tarefa, essa alteração não será registrada até a próxima passagem de validação. Durante esse tempo, contagens de tarefa podem ser ligeiramente imprecisas devido ao evento perdido, mas elas serão corrigidas na próxima passagem de validação.

## <a name="best-practices-for-counting-a-jobs-tasks"></a>Melhores práticas para contagem de tarefas de um trabalho

Chamar a operação Obter Contagens de Tarefas é a maneira mais eficiente para retornar uma contagem básica das tarefas de um trabalho por estado. Se você estiver usando a versão 2017-06-01.5.1 do serviço de Lote, é recomendável escrever ou atualizar o código para usar Obter Contagens de Tarefas.

A operação obter contagens de tarefa não está disponível em versões do serviço de Lote anteriores a 2017-06-01.5.1. Se você estiver usando uma versão mais antiga do serviço, use uma consulta de lista para contar tarefas em um trabalho em vez disso. Para obter mais informações, consulte [Criar consultas para listar recursos do Lote com eficiência](batch-efficient-list-queries.md).

## <a name="next-steps"></a>Próximas etapas

* Veja a [visão geral dos recursos do Lote](batch-api-basics.md) para saber mais sobre os conceitos e os recursos do serviço do Lote. O artigo aborda os recursos principais do Lote, como pools, nós de computação, trabalhos e tarefas, e fornece uma visão geral dos recursos do serviço.
* Obtenha as noções básicas sobre o desenvolvimento de um aplicativo habilitado para o Lote usando a [biblioteca de cliente .NET do Lote](batch-dotnet-get-started.md) ou do [Python](batch-python-tutorial.md). O artigo introdutório orienta você por meio de um aplicativo de trabalho que usa o serviço em Lotes para executar uma carga de trabalho em vários nós de computação.


[rest_get_task_counts]: https://docs.microsoft.com/rest/api/batchservice/get-the-task-counts-for-a-job
[rest_get_task]: https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task
[rest_list_tasks]: https://docs.microsoft.com/rest/api/batchservice/list-the-tasks-associated-with-a-job
