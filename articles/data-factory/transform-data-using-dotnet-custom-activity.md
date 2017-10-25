---
title: Usar atividades personalizadas em um pipeline do Data Factory do Azure
description: "Saiba como criar atividades personalizadas e usá-las em um pipeline do Azure Data Factory."
services: data-factory
documentationcenter: 
author: shengcmsft
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2017
ms.author: shengc
ms.openlocfilehash: 24f15168fd716cf317087b8a2ad19b66574ce569
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-custom-activities-in-an-azure-data-factory-pipeline"></a>Usar atividades personalizadas em um pipeline do Data Factory do Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 – já disponível](v1/data-factory-use-custom-activities.md)
> * [Versão 2 – Versão prévia](transform-data-using-dotnet-custom-activity.md)

Há dois tipos de atividades que você pode usar em um pipeline do Azure Data Factory.

- [Atividades de movimentação de dados](copy-activity-overview.md) para mover dados entre [os armazenamentos de dados de origem e do coletor com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
- [Atividades de transformação de dados](transform-data.md) para transformar dados usando serviços de computação como Azure HDInsight, Lote do Azure e Azure Machine Learning. 

Para mover dados de/para um armazenamento de dados sem suporte do Data Factory ou para transformar/processar dados de uma forma que não tenha suporte do Data Factory, você pode criar uma **Atividade personalizada** com a sua própria lógica de movimentação ou de transformação de dados e usar essa atividade em um pipeline. A atividade personalizada executa a sua lógica de código personalizada em um pool de máquinas virtuais do **Lote do Azure**.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do serviço Data Factory, que já está disponível, consulte as [Atividades personalizadas na V1](v1/data-factory-use-custom-activities.md).
 

Veja os tópicos a seguir se você for novo no serviço de Lote do Azure:

* [Noções básicas do Lote do Azure](../batch/batch-technical-overview.md) para obter uma visão geral do serviço de Lote do Azure.
* Cmdlet [New-AzureRmBatchAccount](/powershell/module/azurerm.batch/New-AzureRmBatchAccount?view=azurermps-4.3.1) para criar uma conta do Lote do Azure (ou) o [Portal do Azure](../batch/batch-account-create-portal.md) para criar a conta do Lote do Azure usando o Portal do Azure. Consulte o tópico [Usando o PowerShell para gerenciar a conta do Lote do Azure](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) para obter instruções detalhadas sobre como usar este cmdlet.
* [New-AzureBatchPool](/powershell/module/azurerm.batch/New-AzureBatchPool?view=azurermps-4.3.1) para criar um pool do Lote do Azure.

## <a name="azure-batch-linked-service"></a>Serviço vinculado do Lote do Azure 
O JSON a seguir define um serviço linear de exemplo do Lote do Azure. Para obter detalhes, consulte [Compute environments supported by Azure Data Factory](compute-linked-services.md) (Ambientes de computação com suporte do Azure Data Factory)

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
        "type": "AzureBatch",
        "typeProperties": {
            "accountName": "batchaccount",
            "accessKey": {
                "type": "SecureString",
                "value": "access key"
            },
            "batchUri": "https://batchaccount.region.batch.azure.com",
            "poolName": "poolname",
            "linkedServiceName": {
                "referenceName": "StorageLinkedService",
                "type": "LinkedServiceReference"
            }
        }
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

 Para saber mais sobre o serviço vinculado do Lote do Azure, consulte o artigo [Compute linked services](compute-linked-services.md) (Serviços de computação vinculados). 

## <a name="custom-activity"></a>Atividade personalizada

O trecho de código JSON a seguir define um pipeline com uma atividade personalizada simples. A definição da atividade tem uma referência ao serviço vinculado do Lote do Azure. 

```json
{
    "name": "MyCustomActivityPipeline",
    "properties": {
      "description": "Custom activity sample",
      "activities": [{
        "type": "Custom",
        "name": "MyCustomActivity",
        "linkedServiceName": {
          "referenceName": "AzureBatchLinkedService",
          "type": "LinkedServiceReference"
        },
        "typeProperties": {
          "command": "helloworld.exe",
          "folderPath": "customactv2/helloworld",
          "resourceLinkedService": {
            "referenceName": "StorageLinkedService",
            "type": "LinkedServiceReference"
          }
        }
      }]
    }
  }
```

Neste exemplo, helloworld.exe é um aplicativo personalizado armazenado na pasta customactv2/helloworld da conta de Armazenamento do Azure usada no resourceLinkedService. A atividade personalizada envia esse aplicativo personalizado para ser executado no Lote do Azure. Você pode substituir o comando para qualquer aplicativo preferencial que possa ser executado no sistema operacional de destino dos nós do pool do Lote do Azure. 

A tabela a seguir descreve os nomes e as descrições de propriedades que são específicas a esta atividade. 

| Propriedade              | Descrição                              | Obrigatório |
| :-------------------- | :--------------------------------------- | :------- |
| name                  | Nome da atividade no pipeline     | Sim      |
| description           | Texto que descreve o que a atividade faz.  | Não       |
| type                  | Para a atividade personalizada, o tipo de atividade é **Personalizado**. | Sim      |
| linkedServiceName     | Serviço vinculado ao Lote do Azure. Para saber mais sobre esse serviço vinculado, consulte o artigo [Compute linked services](compute-linked-services.md) (Serviços de computação vinculados).  | Sim      |
| command               | Comando do aplicativo personalizado a ser executado. Se o aplicativo já estiver disponível no nó do pool do Lote do Azure, resourceLinkedService e folderPath poderão ser ignorados. Por exemplo, você pode especificar o comando como `cmd /c dir`, que tem suporte nativo no nó do pool do Lote do Windows. | Sim      |
| resourceLinkedService | Serviço de vinculado do Armazenamento do Azure para a conta de armazenamento na qual o aplicativo personalizado é armazenado | Não       |
| folderPath            | Caminho para a pasta do aplicativo personalizado e de todas as suas dependências | Não       |
| referenceObjects      | Uma matriz de serviços vinculados e conjuntos de dados existentes. Os serviços vinculados e os conjuntos de dados referenciados são passados para o aplicativo personalizado no formato JSON para que o seu código personalizado possa referenciar os recursos do Data Factory | Não       |
| extendedProperties    | Propriedades definidas pelo usuário que podem ser passadas para o aplicativo personalizado no formato JSON para que o seu código personalizado possa referenciar propriedades adicionais | Não       |

## <a name="passing-objects-and-properties"></a>Passando objetos e propriedades

Este exemplo mostra como você pode usar referenceObjects e extendedProperties para passar objetos do Data Factory e propriedades definidas pelo usuário para seu aplicativo personalizado. 


```json
{
  "name": "MyCustomActivityPipeline",
  "properties": {
    "description": "Custom activity sample",
    "activities": [{
      "type": "Custom",
      "name": "MyCustomActivity",
      "linkedServiceName": {
        "referenceName": "AzureBatchLinkedService",
        "type": "LinkedServiceReference"
      },
      "typeProperties": {
        "command": "SampleApp.exe",
        "folderPath": "customactv2/SampleApp",
        "resourceLinkedService": {
          "referenceName": "StorageLinkedService",
          "type": "LinkedServiceReference"
        },
        "referenceObjects": {
          "linkedServices": [{
            "referenceName": "AzureBatchLinkedService",
            "type": "LinkedServiceReference"
          }]
        },
        "extendedProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "aSampleSecureString"
            }           
        }
      }
    }]
  }
}
```

Quando a atividade é executada, referenceObjects e extendedProperties são armazenados nos seguintes arquivos que são implantados na mesma pasta de execução do SampleApp.exe: 

- activity.json

  Armazena extendedProperties e as propriedades da atividade personalizada. 

- linkedServices.json

  Armazena uma matriz de serviços vinculados definidos na propriedade referenceObjects. 

- datasets.json

  Armazena uma matriz de conjuntos de dados definidos na propriedade referenceObjects. 

O exemplo de código a seguir demonstra como o SampleApp.exe pode acessar as informações necessárias dos arquivos JSON: 

```C#
using Newtonsoft.Json;
using System;
using System.IO;

namespace SampleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            //From Extend Properties
            dynamic activity = JsonConvert.DeserializeObject(File.ReadAllText("activity.json"));
            Console.WriteLine(activity.typeProperties.extendedProperties.connectionString.value);

            // From LinkedServices
            dynamic linkedServices = JsonConvert.DeserializeObject(File.ReadAllText("linkedServices.json"));
            Console.WriteLine(linkedServices[0].properties.typeProperties.connectionString.value);
        }
    }
}
```

####<a name="retrieve-execution-outputs"></a>Recuperar as saídas de execução

Você pode iniciar uma execução do pipeline de exemplo e monitorar o resultado de execução usando os seguintes comandos do PowerShell: 

```powershell
$runId = Invoke-AzureRmDataFactoryV2Pipeline -dataFactoryName "factoryName" -PipelineName "pipelineName" 
$result = Get-AzureRmDataFactoryV2ActivityRun -dataFactoryName "factoryName" -PipelineRunId $runId -RunStartedAfter "2017-09-06" -RunStartedBefore "2017-12-31"
$result.output -join "`r`n" 
$result.Error -join "`r`n" 
```

O **stdout** e **stderr** do aplicativo personalizado são salvos no contêiner **adfjobs** no serviço vinculado do Armazenamento do Azure que você definiu durante a criação do serviço vinculado do Lote do Azure com um GUID da tarefa. Você pode obter o caminho detalhado da saída da execução da atividade, conforme é mostrado no trecho de código a seguir: 

```shell
"exitcode": 0
"outputs": [
    "https://adfv2storage.blob.core.windows.net/adfjobs/097235ff-2c65-4d50-9770-29c029cbafbb/output/stdout.txt",
    "https://adfv2storage.blob.core.windows.net/adfjobs/097235ff-2c65-4d50-9770-29c029cbafbb/output/stderr.txt"
]
"errorCode": ""
"message": ""
"failureType": ""
"target": "MyCustomActivity"
```

> [!IMPORTANT]
> - O activity.json, o linkedServices.json e o datasets.json são armazenados na pasta de tempo de execução da tarefa Bath. Neste exemplo, o activity.json, linkedServices.json e datasets.json são armazenados no caminho https://adfv2storage.blob.core.windows.net/adfjobs/097235ff-2c65-4d50-9770-29c029cbafbb/runtime/. Caso seja necessário, limpe-o separadamente. 
> - Como os serviços vinculados usam o Integration Runtime (auto-hospedado), as informações confidenciais, como chaves ou senhas, são criptografadas pelo Integration Runtime (auto-hospedado) para garantir que a credencial permaneça no ambiente de rede privada definido pelo cliente. Por esse motivo, alguns campos confidenciais podem ficar faltando na referência do código do aplicativo personalizado. Use SecureString em extendedProperties em vez de usar a referência do serviço vinculado, se necessário. 



## <a name="auto-scaling-of-azure-batch"></a>Dimensionamento automático do Lote do Azure
Você também pode criar um pool de Lotes do Azure com o recurso **autoscale** . Por exemplo, você poderia criar um pool do Lote do Azure sem nenhuma VM dedicada e uma fórmula de escala automática com base no número de tarefas pendentes. 

A fórmula de exemplo alcança o seguinte comportamento: quando o pool é criado pela primeira vez, ele começa com uma VM. A métrica de $PendingTasks define o número de tarefas em execução + estado ativo (em fila).  A fórmula localiza o número médio de tarefas pendentes nos últimos 180 segundos e define TargetDedicated adequadamente. Isso garante que TargetDedicated nunca ultrapasse 25 VMs. Assim, o pool aumenta automaticamente conforme novas tarefas são enviadas e, conforme as tarefas são concluídas, as VMs se liberam uma a uma e são reduzidas pelo dimensionamento automático. startingNumberOfVMs e maxNumberofVMs podem ser ajustados às suas necessidades.

Fórmula de dimensionamento automático:

``` 
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
```

Consulte [Dimensionar automaticamente os nós de computação em um pool de Lotes do Azure](../batch/batch-automatic-scaling.md) para obter detalhes.

Se o pool estiver usando o padrão [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx), o serviço Lote poderá demorar de 15 a 30 minutos para preparar a VM antes de executar a atividade personalizada.  Se o pool estiver usando um autoScaleEvaluationInterval diferente, o serviço de lote pode levar autoScaleEvaluationInterval + 10 minutos.


## <a name="next-steps"></a>Próximas etapas
Consulte os seguintes artigos que explicam como transformar dados de outras maneiras: 

* [U-SQL activity](transform-data-using-data-lake-analytics.md) (Atividade do U-SQL)
* [Hive activity](transform-data-using-hadoop-hive.md) (Atividade do Hive)
* [Pig activity](transform-data-using-hadoop-pig.md) (Atividade do Pig)
* [MapReduce activity](transform-data-using-hadoop-map-reduce.md) (Atividade do MapReduce)
* [Hadoop Streaming activity](transform-data-using-hadoop-streaming.md) (Atividade de streaming do Hadoop)
* [Spark activity](transform-data-using-spark.md) (Atividade do Spark)
* [Machine Learning Batch Execution activity](transform-data-using-machine-learning.md) (Atividade de execução em lotes do Machine Learning)
* [Stored procedure activity](transform-data-using-stored-procedure.md) (Atividade de procedimento armazenado)
