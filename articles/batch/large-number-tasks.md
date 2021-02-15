---
title: Enviar muitas tarefas para um trabalho em lotes
description: Saiba como enviar com eficiência um número muito grande de tarefas em um único trabalho do lote do Azure.
ms.topic: how-to
ms.date: 12/30/2020
ms.custom: devx-track-python, devx-track-csharp
ms.openlocfilehash: 08cf92507a4556afbf56c9cb7e2c9c1b3a6c9479
ms.sourcegitcommit: beacda0b2b4b3a415b16ac2f58ddfb03dd1a04cf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/31/2020
ms.locfileid: "97831509"
---
# <a name="submit-a-large-number-of-tasks-to-a-batch-job"></a>Enviar muitas tarefas para um trabalho em lotes

Ao executar cargas de trabalho do Lote do Azure em grande escala, pode ser útil enviar dezenas de milhares, centenas de milhares ou números de tarefas ainda maiores para um único trabalho.

Este artigo mostra como enviar um grande número de tarefas com uma taxa de transferência significativamente maior para um único trabalho em lotes. Depois que as tarefas forem enviadas, elas entrarão na fila do Lote para processamento no pool que você especificar para o trabalho.

## <a name="use-task-collections"></a>Usar coleções de tarefas

Ao adicionar um grande número de tarefas, use os métodos apropriados ou sobrecargas fornecidos pelas APIs do lote para adicionar tarefas como uma *coleção* , em vez de uma de cada vez. Em geral, é preciso construir uma coleção de tarefas definindo as tarefas à medida que um conjunto de arquivos de entrada ou parâmetros é iterado para o trabalho.

O tamanho máximo da coleção de tarefas que você pode adicionar em uma única chamada depende da API do lote que você usa.

### <a name="apis-allowing-collections-of-up-to-100-tasks"></a>APIs que permitem coleções de até 100 tarefas

Essas APIs de lote limitam a coleta a 100 tarefas. O limite pode ser menor dependendo do tamanho das tarefas (por exemplo, se as tarefas tiverem um grande número de arquivos de recursos ou variáveis de ambiente).

- [REST API](/rest/api/batchservice/task/addcollection)
- [API Python](/python/api/azure-batch/azure.batch.operations.TaskOperations)
- [API Node.js](/javascript/api/@azure/batch/task)

Ao usar essas APIs, você precisa fornecer a lógica para dividir o número de tarefas para atender ao limite da coleção e manipular erros e repetições em caso de falhas de adição de tarefas. Se uma coleção de tarefas for muito grande para ser adicionada, a solicitação gerará um erro e precisará ser repetida com menos tarefas.

### <a name="apis-allowing-collections-of-larger-numbers-of-tasks"></a>APIs que permitem coleções de números maiores de tarefas

Outras APIs do lote dão suporte a coleções de tarefas muito maiores, limitadas apenas pela disponibilidade de RAM no cliente remetente. Essas APIs tratam de forma transparente a divisão da coleção de tarefas em "partes" para as APIs de nível inferior e novas tentativas para falhas de adição de tarefas.

- [API do .NET](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync)
- [API Java](/java/api/com.microsoft.azure.batch.protocol.tasks.addcollectionasync)
- [Extensão da CLI do Lote do Azure](batch-cli-templates.md) com modelos da CLI do Lote
- [Extensão do SDK do Python](https://pypi.org/project/azure-batch-extensions/)

## <a name="increase-throughput-of-task-submission"></a>Aumentar a taxa de transferência do envio de tarefa

Pode levar algum tempo para adicionar uma grande coleção de tarefas a um trabalho. Por exemplo, a adição de 20.000 tarefas por meio da API do .NET pode levar até um minuto. Dependendo da API do lote e da carga de trabalho, você pode melhorar a taxa de transferência da tarefa modificando um ou mais dos itens a seguir.

### <a name="task-size"></a>Tamanho da tarefa

Adicionar tarefas grandes demora mais do que adicionar menores. Para reduzir o tamanho de cada tarefa em uma coleção, simplifique a linha de comando da tarefa, reduza o número de variáveis de ambiente ou trate os requisitos de execução de tarefas com mais eficiência.

Por exemplo, em vez de usar um grande número de arquivos de recursos, instale dependências de tarefas usando uma [tarefa inicial](jobs-and-tasks.md#start-task) no pool ou use um [pacote de aplicativos](batch-application-packages.md) ou um [contêiner do Docker](batch-docker-container-workloads.md).

### <a name="number-of-parallel-operations"></a>Número de operações paralelas

Dependendo da API do lote, você pode aumentar a taxa de transferência aumentando o número máximo de operações simultâneas pelo cliente do lote. Defina esta configuração usando a propriedade [BatchClientParallelOptions.MaxDegreeOfParallelism](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) na API do .NET ou o parâmetro `threads` de métodos como [TaskOperations.add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations) na extensão do SDK de Python do Lote. (Essa propriedade não está disponível no SDK do Python de Lote nativo.)

Por padrão, essa propriedade é definida como 1, mas você pode defini-la como maior para melhorar a taxa de transferência de operações. Você troca uma taxa de transferência maior pelo consumo da largura de banda da rede e de certo desempenho da CPU. A taxa de transferência de tarefa aumenta em até 100 vezes o `MaxDegreeOfParallelism` ou `threads`. Na prática, você deve definir o número de operações simultâneas para abaixo de 100.

 A extensão da CLI do Lote do Azure com modelos do Lote aumenta o número de operações simultâneas automaticamente com base no número de núcleos disponíveis, mas essa propriedade não pode ser definida na CLI.

### <a name="http-connection-limits"></a>Limites de conexão HTTP

Ter muitas conexões HTTP simultâneas pode limitar o desempenho do cliente de lote quando ele está adicionando um grande número de tarefas. Algumas APIs limitam o número de conexões HTTP. Ao desenvolver com a API do .NET, por exemplo, a propriedade [ServicePointManager.DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit) é definida como 2 por padrão. É recomendável aumentar o valor para um número próximo ou maior que o número de operações paralelas.

## <a name="example-batch-net"></a>Exemplo: .NET no Lote

Os snippets de código C# a seguir mostram as configurações a serem definidas ao adicionar um grande número de tarefas usando a API .NET do Lote.

Para aumentar a taxa de transferência de tarefa, aumente o valor da propriedade [MaxDegreeOfParallelism](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) do [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient). Por exemplo:

```csharp
BatchClientParallelOptions parallelOptions = new BatchClientParallelOptions()
  {
    MaxDegreeOfParallelism = 15
  };
...
```

Adicione uma coleção de tarefas ao trabalho usando a sobrecarga apropriada do método [AddTaskAsync](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync) ou [AddTask](/dotnet/api/microsoft.azure.batch.cloudjob.addtask
). Por exemplo:

```csharp
// Add a list of tasks as a collection
List<CloudTask> tasksToAdd = new List<CloudTask>(); // Populate with your tasks
...
await batchClient.JobOperations.AddTaskAsync(jobId, tasksToAdd, parallelOptions);
```

## <a name="example-batch-cli-extension"></a>Exemplo: extensão da CLI do Lote

Usando as extensões da CLI do Lote do Azure com [modelos da CLI do Lote](batch-cli-templates.md), crie um arquivo JSON de modelo de trabalho que inclua uma [fábrica de tarefas](https://github.com/Azure/azure-batch-cli-extensions/blob/master/doc/taskFactories.md). A fábrica de tarefas define uma coleção de tarefas relacionadas para um trabalho de uma única definição de tarefa.  

Veja a seguir um modelo de trabalho de exemplo para um trabalho de limpeza paramétrica unidimensional com um grande número de tarefas (nesse caso, 250.000). A linha de comando da tarefa é um comando `echo` simples.

```json
{
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "myjob",
            "constraints": {
                "maxWallClockTime": "PT5H",
                "maxTaskRetryCount": 1
            },
            "poolInfo": {
                "poolId": "mypool"
            },
            "taskFactory": {
                "type": "parametricSweep",
                "parameterSets": [
                    {
                        "start": 1,
                        "end": 250000,
                        "step": 1
                    }
                ],
                "repeatTask": {
                    "commandLine": "/bin/bash -c 'echo Hello world from task {0}'",
                    "constraints": {
                        "retentionTime":"PT1H"
                    }
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
```

Para executar um trabalho com o modelo, consulte [Usar modelos da CLI do Lote do Azure e transferência de arquivos](batch-cli-templates.md).

## <a name="example-batch-python-sdk-extension"></a>Exemplo: extensão do SDK de Python do Lote

Para usar a extensão do SDK do Python do Lote do Azure, primeiro instale o SDK do Python e a extensão:

```
pip install azure-batch
pip install azure-batch-extensions
```

Configure um `BatchExtensionsClient` que usa a extensão do SDK:

```python

client = batch.BatchExtensionsClient(
    base_url=BATCH_ACCOUNT_URL, resource_group=RESOURCE_GROUP_NAME, batch_account=BATCH_ACCOUNT_NAME)
...
```

Crie uma coleção de tarefas que serão adicionadas a um trabalho. Por exemplo:

```python
tasks = list()
# Populate the list with your tasks
...
```

Adicione a coleção de tarefas usando [task.add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations). Defina o parâmetro `threads` para aumentar o número de operações simultâneas:

```python
try:
    client.task.add_collection(job_id, threads=100)
except Exception as e:
    raise e
```

A extensão do SDK de Python do Lote também é compatível com a adição de parâmetros de tarefa ao trabalho usando uma especificação JSON para uma fábrica de tarefas. Por exemplo, configure parâmetros do trabalho para uma limpeza paramétrica semelhante àquela mostrada no exemplo de modelo da CLI do Lote anterior:

```python
parameter_sweep = {
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "myjob",
            "poolInfo": {
                "poolId": "mypool"
            },
            "taskFactory": {
                "type": "parametricSweep",
                "parameterSets": [
                    {
                        "start": 1,
                        "end": 250000,
                        "step": 1
                    }
                ],
                "repeatTask": {
                    "commandLine": "/bin/bash -c 'echo Hello world from task {0}'",
                    "constraints": {
                        "retentionTime": "PT1H"
                    }
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
...
job_json = client.job.expand_template(parameter_sweep)
job_parameter = client.job.jobparameter_from_json(job_json)
```

Adicione os parâmetros de trabalho ao trabalho. Defina o parâmetro `threads` para aumentar o número de operações simultâneas:

```python
try:
    client.job.add(job_parameter, threads=50)
except Exception as e:
    raise e
```

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre como usar a extensão da CLI do Lote do Azure com [modelos da CLI do Lote](batch-cli-templates.md).
- Saiba mais sobre a [extensão do SDK do Python do Lote](https://pypi.org/project/azure-batch-extensions/).
- Leia sobre [as práticas recomendadas para o lote do Azure](best-practices.md).
