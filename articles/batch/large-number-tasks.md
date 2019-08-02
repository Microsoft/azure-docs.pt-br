---
title: Enviar um grande número de tarefas – Lote do Azure | Microsoft Docs
description: Como enviar com eficácia um grande número de tarefas em um único trabalho em Lotes do Azure
services: batch
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 08/24/2018
ms.author: lahugh
ms.custom: ''
ms.openlocfilehash: f91d47e1f57fb74575fbdad0a76386b53fb38b1f
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68322514"
---
# <a name="submit-a-large-number-of-tasks-to-a-batch-job"></a>Enviar muitas tarefas para um trabalho em lotes

Ao executar cargas de trabalho do Lote do Azure em grande escala, pode ser útil enviar dezenas de milhares, centenas de milhares ou números de tarefas ainda maiores para um único trabalho. 

Este artigo fornece diretrizes e alguns exemplos de código para enviar um grande número de tarefas com taxa de transferência significativamente maior para um único trabalho em lotes. Depois que as tarefas forem enviadas, elas entrarão na fila do Lote para processamento no pool que você especificar para o trabalho.

## <a name="use-task-collections"></a>Usar coleções de tarefas

As APIs de Lote fornecem métodos para adicionar tarefas com eficiência a um trabalho como uma *coleção*, além de uma de cada vez. Ao adicionar um grande número de tarefas, utilize os métodos ou sobrecargas apropriados para adicionar tarefas como uma coleção. Em geral, é preciso construir uma coleção de tarefas definindo as tarefas à medida que um conjunto de arquivos de entrada ou parâmetros é iterado para o trabalho.

O tamanho máximo da coleção de tarefas que pode ser adicionado em uma única chamada depende da API de Lote usada:

* As seguintes APIs de Lote limitam a coleção a **100 tarefas**. O limite poderia ser menor dependendo do tamanho das tarefas, por exemplo, se as tarefas tiverem um grande número de arquivos de recursos ou variáveis de ambiente.

    * [API REST](/rest/api/batchservice/task/addcollection)
    * [API Python](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python)
    * [API Node.js](/javascript/api/azure-batch/task?view=azure-node-latest)

  Ao usar essas APIs, é preciso fornecer a lógica para dividir o número de tarefas para satisfazer o limite da coleção e para tratar erros e novas tentativas em caso de falha de adição de tarefas. Se uma coleção de tarefas for muito grande para ser adicionada, a solicitação gerará um erro e precisará ser repetida com menos tarefas.

* As seguintes APIs são compatíveis com coleções de tarefas muito maiores, limitadas apenas pela disponibilidade de RAM no cliente de envio. Essas APIs tratam de forma transparente a divisão da coleção de tarefas em “partes” para as APIs de nível inferior e novas tentativas se a adição de tarefas falhar.

    * [API do .NET](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync?view=azure-dotnet)
    * [API Java](/java/api/com.microsoft.azure.batch.protocol.tasks.addcollectionasync?view=azure-java-stable)
    * [Extensão da CLI do Lote do Azure](batch-cli-templates.md) com modelos da CLI do Lote
    * [Extensão do SDK do Python](https://pypi.org/project/azure-batch-extensions/)

## <a name="increase-throughput-of-task-submission"></a>Aumentar a taxa de transferência do envio de tarefa

Pode levar algum tempo para adicionar uma grande coleção de tarefas a um trabalho – por exemplo, até um minuto para adicionar 20.000 tarefas por meio da API do .NET. Dependendo da sua carga de trabalho e da API de Lote, você pode melhorar a taxa de transferência de tarefa modificando um ou mais dos seguintes itens:

* **Tamanho da tarefa** – Adicionar tarefas grandes demora mais do que adicionar tarefas menores. Para reduzir o tamanho de cada tarefa em uma coleção, simplifique a linha de comando da tarefa, reduza o número de variáveis de ambiente ou trate os requisitos de execução de tarefas com mais eficiência. Por exemplo, em vez de usar um grande número de arquivos de recurso, instale as dependências de tarefa usando uma [tarefa inicial](batch-api-basics.md#start-task) no pool ou use um [pacote de aplicativo](batch-application-packages.md) ou [contêiner do Docker](batch-docker-container-workloads.md).

* **Número de operações paralelas** – Dependendo da API de Lote, eleve a taxa de transferência aumentando o número máximo de operações simultâneas realizadas pelo cliente do Lote. Defina esta configuração usando a propriedade [BatchClientParallelOptions.MaxDegreeOfParallelism](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) na API do .NET ou o parâmetro `threads` de métodos como [TaskOperations.add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python) na extensão do SDK de Python do Lote. (Essa propriedade não está disponível no SDK do Python de Lote nativo.) Por padrão, essa propriedade é definida como 1; defina-a para um valor mais alto para melhorar a taxa de transferência das operações. Você troca uma taxa de transferência maior pelo consumo da largura de banda da rede e de certo desempenho da CPU. A taxa de transferência de tarefa aumenta em até 100 vezes o `MaxDegreeOfParallelism` ou `threads`. Na prática, é preciso definir as operações simultâneas para um número abaixo de 100. 
 
  A extensão da CLI do Lote do Azure com modelos do Lote aumenta o número de operações simultâneas automaticamente com base no número de núcleos disponíveis, mas essa propriedade não pode ser definida na CLI. 

* **Limites de conexão HTTP** – O número de conexões HTTP simultâneas pode limitar o desempenho do cliente do Lote quando ele está adicionando um grande número de tarefas. O número de conexões HTTP é limitado com determinadas APIs. Ao desenvolver com a API do .NET, por exemplo, a propriedade [ServicePointManager.DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit) é definida como 2 por padrão. É recomendável aumentar o valor para um número próximo ou maior que o número de operações paralelas.

## <a name="example-batch-net"></a>Exemplo: .NET no Lote

Os snippets de código C# a seguir mostram as configurações a serem definidas ao adicionar um grande número de tarefas usando a API .NET do Lote.

Para aumentar a taxa de transferência de tarefa, aumente o valor da propriedade [MaxDegreeOfParallelism](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) do [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient?view=azure-dotnet). Por exemplo:

```csharp
BatchClientParallelOptions parallelOptions = new BatchClientParallelOptions()
  {
    MaxDegreeOfParallelism = 15
  };
...
```
Adicione uma coleção de tarefas ao trabalho usando a sobrecarga apropriada do método [AddTaskAsync](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync?view=azure-dotnet) ou [AddTask](/dotnet/api/microsoft.azure.batch.cloudjob.addtask?view=azure-dotnet
). Por exemplo:

```csharp
// Add a list of tasks as a collection
List<CloudTask> tasksToAdd = new List<CloudTask>(); // Populate with your tasks
...
await batchClient.JobOperations.AddTaskAsync(jobId, tasksToAdd, parallelOptions);
```


## <a name="example-batch-cli-extension"></a>Exemplo: extensão da CLI do Lote

Usando as extensões da CLI do Lote do Azure com [modelos da CLI do Lote](batch-cli-templates.md), crie um arquivo JSON de modelo de trabalho que inclua uma [fábrica de tarefas](https://github.com/Azure/azure-batch-cli-extensions/blob/master/doc/taskFactories.md). A fábrica de tarefas define uma coleção de tarefas relacionadas para um trabalho de uma única definição de tarefa.  

Veja a seguir um modelo de trabalho de exemplo para um trabalho de limpeza paramétrico unidimensional com um grande número de tarefas, nesse caso, 250.000. A linha de comando da tarefa é um comando `echo` simples.

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

Adicione a coleção de tarefas usando [task.add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python). Defina o parâmetro `threads` para aumentar o número de operações simultâneas:

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

* Saiba mais sobre como usar a extensão da CLI do Lote do Azure com [modelos da CLI do Lote](batch-cli-templates.md).
* Saiba mais sobre a [extensão do SDK do Python do Lote](https://pypi.org/project/azure-batch-extensions/).
