---
title: Criar/agendar pipelines, atividades em cadeia no Data Factory | Microsoft Docs
description: Aprenda a criar um pipeline de dados no Azure Data Factory para mover e transformar dados. Crie um fluxo de trabalho orientado por dados para produção pronto para usar informações.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 13b137c7-1033-406f-aea7-b66f25b313c0
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 58db6f9903c4dc02c2d76f3784b004972621a000
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836492"
---
# <a name="pipelines-and-activities-in-azure-data-factory"></a>Pipelines e atividades no Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](data-factory-create-pipelines.md)
> * [Versão 2 (versão atual)](../concepts-pipelines-activities.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte [Pipelines na V2 ](../concepts-pipelines-activities.md).

Este artigo o ajuda a compreender pipelines e atividades no Azure Data Factory e a usá-los para construir fluxos de trabalho orientados a dados de ponta a ponta para seus cenários de movimentação e processamento de dados.

> [!NOTE]
> Esse artigo pressupõe que você tenha lido os artigos [Introdução ao Azure Data Factory](data-factory-introduction.md). Se você não tiver experiência prática com a criação de data factories, ler o [tutorial de transformação de dados](data-factory-build-your-first-pipeline.md) e/ou [tutorial de movimentação de dados](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) ajudará a entender melhor este artigo.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Visão geral
Uma fábrica de dados pode ter um ou mais pipelines. Um pipeline é um agrupamento lógico de atividades que juntas executam uma tarefa. As atividades em um pipeline definem ações para executar em seus dados. Por exemplo, você pode usar uma atividade de cópia para copiar dados de um SQL Server local para um Armazenamento de Blobs do Azure. Em seguida, usar uma atividade Hive que executa um script Hive em um cluster HDInsight do Azure a fim de processar/transformar dados do armazenamento de blobs para gerar dados de saída. Por fim, usar uma segunda atividade de cópia para copiar os dados de saída em um SQL Data Warehouse do Azure sobre o qual as soluções de relatório de BI (business intelligence) são criadas.

Uma atividade pode não usar ou usar vários [conjuntos de dados](data-factory-create-datasets.md) de entrada e gerar um ou mais [conjuntos de dados](data-factory-create-datasets.md) de saída. O seguinte diagrama mostra a relação entre pipeline, atividade e conjunto de dados no Data Factory:

![Relação entre pipeline, atividade e conjunto de dados](media/data-factory-create-pipelines/relationship-pipeline-activity-dataset.png)

Um pipeline permite que você gerencie atividades como um conjunto, em vez de cada uma individualmente. Por exemplo, é possível implantar, agendar, suspender e retomar um pipeline, em vez de lidar com atividades no pipeline de modo independente.

O Data Factory dá suporte a dois tipos de atividades: atividades de movimentação de dados e atividades de transformação de dados. Cada atividade pode não ter ou ter vários [conjuntos de dados](data-factory-create-datasets.md) de entrada e gerar um ou mais conjuntos de dados de saída.

Um conjunto de dados de entrada representa a entrada de uma atividade no pipeline e um conjunto de dados de saída representa a saída da atividade. Conjuntos de dados identificam dados em armazenamentos de dados diferentes, como tabelas, arquivos, pastas e documentos. Depois de criar um conjunto de dados, você pode usá-lo com atividades no pipeline. Por exemplo, um conjunto de dados pode ser o conjunto de dados de entrada/saída de uma atividade de Cópia ou uma atividade do HDInsightHive. Para saber mais sobre conjuntos de dados, confira o artigo [Conjuntos de dados no Azure Data Factory](data-factory-create-datasets.md).

### <a name="data-movement-activities"></a>Atividades de movimentação de dados
A Atividade de Cópia no Data Factory copia os dados de um repositório de dados de origem para um repositório de dados de coletor. A Data Factory dá suporte aos repositórios de dados a seguir. Os dados de qualquer origem podem ser gravados em qualquer coletor. Clique em um repositório de dados para saber como copiar dados dentro e fora do repositório.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Os repositórios de dados com * podem estar no local ou no Azure IaaS e exigem a instalação do [Gateway de Gerenciamento de Dados](data-factory-data-management-gateway.md) em um computador Azure IaaS/local.

Para obter mais informações, confira o artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md).

### <a name="data-transformation-activities"></a>Atividades de transformação de dados
[!INCLUDE [data-factory-transformation-activities](../../../includes/data-factory-transformation-activities.md)]

Para obter mais informações, confira o artigo [Atividades de transformação de dados](data-factory-data-transformation-activities.md).

### <a name="custom-net-activities"></a>Atividades personalizadas do .NET
Se você precisar mover dados de/para um repositório de dados que não tem suporte da Atividade de Cópia ou transformar dados usando sua própria lógica, crie uma **atividade personalizada do .NET**. Para obter detalhes sobre como criar e usar uma atividade personalizada, confira [Usar atividades personalizadas em um pipeline do Azure Data Factory](data-factory-use-custom-activities.md).

## <a name="schedule-pipelines"></a>Agendar pipelines
Um pipeline está ativo somente entre sua hora de **início** e de **término**. Ele não é executado antes da hora de início ou após a hora de término. Se o pipeline for pausado, ele não será executado independentemente da sua hora de início e término. Para um pipeline ser executado, ele não deve estar pausado. Confira [Agendamento e Execução](data-factory-scheduling-and-execution.md) para saber como funciona o agendamento e a execução no Azure Data Factory.

## <a name="pipeline-json"></a>Pipeline de JSON
Vamos dar uma olhada mais próxima em como um pipeline é definido no formato JSON. A estrutura genérica para um pipeline será semelhante ao seguinte:

```json
{
    "name": "PipelineName",
    "properties":
    {
        "description" : "pipeline description",
        "activities":
        [

        ],
        "start": "<start date-time>",
        "end": "<end date-time>",
        "isPaused": true/false,
        "pipelineMode": "scheduled/onetime",
        "expirationTime": "15.00:00:00",
        "datasets":
        [
        ]
    }
}
```

| Marca | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| name |Nome do pipeline. Especifique um nome que represente a ação executada pelo pipeline. <br/><ul><li>Número máximo de caracteres: 260</li><li>Deve começar com uma letra, um número ou um sublinhado (\_)</li><li>Caracteres a seguir não são permitidos: ".", "+","?", "/", "<",">", "\*", "%", "&", ":","\\"</li></ul> |Sim |
| description | Especifique o texto descrevendo para que o pipeline é usado. |Sim |
| activities | A seção **Atividades** pode ter uma ou mais atividades definidas dentro dela. Confira a próxima seção para obter detalhes sobre o elemento das atividades JSON. | Sim |
| start | Data e hora de início para o pipeline. Deve estar no [formato ISO](https://en.wikipedia.org/wiki/ISO_8601). Por exemplo: `2016-10-14T16:32:41Z`. <br/><br/>É possível especificar uma hora local, por exemplo, EST. Veja este exemplo: `2016-02-27T06:00:00-05:00`", que é 6 AM EST.<br/><br/>As propriedades de início e término especificam o período ativo para o pipeline. Fatias de saída são produzidas somente nesse período ativo. |Não<br/><br/>Se você especificar um valor para a propriedade final, será necessário especificar um valor para a propriedade inicial.<br/><br/>Os horários de início e fim podem estar vazios para criar um pipeline. Você deve especificar ambos os valores para definir um período ativo de execução do pipeline. Se você não especificar horários de início e fim ao criar um pipeline, você pode defini-las usando o cmdlet Set-AzDataFactoryPipelineActivePeriod mais tarde. |
| end | Data e hora de término para o pipeline. Se especificado, deve estar no formato ISO. Por exemplo: `2016-10-14T17:32:41Z` <br/><br/>É possível especificar uma hora local, por exemplo, EST. Veja este exemplo: `2016-02-27T06:00:00-05:00`, que é 6 AM EST.<br/><br/>Para executar o pipeline indefinidamente, especifique 9999-09-09 como o valor da propriedade end. <br/><br/> Um pipeline está ativo somente entre sua hora de início e de término. Ele não é executado antes da hora de início ou após a hora de término. Se o pipeline for pausado, ele não será executado independentemente da sua hora de início e término. Para um pipeline ser executado, ele não deve estar pausado. Confira [Agendamento e Execução](data-factory-scheduling-and-execution.md) para saber como funciona o agendamento e a execução no Azure Data Factory. |Não <br/><br/>Se você especificar um valor para a propriedade inicial, será necessário especificar um valor para a propriedade final.<br/><br/>Confira as observações para a propriedade **iniciar** . |
| isPaused | Se definido como true, o pipeline não será executado. Ele está no estado pausado. Valor padrão = falso. Você pode usar essa propriedade para habilitar ou desabilitar o pipeline. |Não |
| pipelineMode | O método de agendamento é executado para o pipeline. Os valores permitidos são: scheduled (padrão), onetime.<br/><br/>'Scheduled' indica que o pipeline será executado em um intervalo de tempo especificado de acordo com seu período ativo (hora de início e término). “Onetime” indica que o pipeline é executado apenas uma vez. Pipelines Onetime não podem ser modificados e atualizados depois de criados atualmente. Confira [Pipeline avulso](#onetime-pipeline) para obter detalhes sobre a configuração única. |Não |
| expirationTime | Após a criação, por quanto tempo o [pipeline avulso](#onetime-pipeline) é válido e deve permanecer provisionado. Se não houver execuções ativas, com falha ou pendentes, o pipeline será excluído automaticamente depois de atingir o tempo de expiração. Valor padrão: `"expirationTime": "3.00:00:00"`|Não |
| datasets |Lista de conjuntos de dados a serem usados por atividades definidas no pipeline. Essa propriedade pode ser usado para definir conjuntos de dados que são específicos para este pipeline e não definido dentro da data factory. Conjuntos de dados definidos dentro este pipeline só podem ser usados por este pipeline e não podem ser compartilhados. Confira [Conjuntos de dados com escopo](data-factory-create-datasets.md#scoped-datasets) para obter detalhes. |Não |

## <a name="activity-json"></a>Atividade JSON
A seção **Atividades** pode ter uma ou mais atividades definidas dentro dela. Cada atividade tem a seguinte estrutura de nível superior:

```json
{
    "name": "ActivityName",
    "description": "description",
    "type": "<ActivityType>",
    "inputs": "[]",
    "outputs": "[]",
    "linkedServiceName": "MyLinkedService",
    "typeProperties":
    {

    },
    "policy":
    {
    },
    "scheduler":
    {
    }
}
```

A seguinte tabela descreve as propriedades na definição de JSON da atividade:

| Marca | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| name | Nome da atividade. Especifique um nome que represente a ação executada pela atividade. <br/><ul><li>Número máximo de caracteres: 260</li><li>Deve começar com uma letra, um número ou um sublinhado (\_)</li><li>Os seguintes caracteres não são permitidos: “.”, “+”, “?”, “/”, “<”,”>”,”*”,”%”,”&”,”:”,”\\”</li></ul> |Sim |
| description | Texto que descreve para que a atividade é usada |Sim |
| type | Tipo da atividade. Confira as seções [Atividades de movimentação de dados](#data-movement-activities) e [Atividades de transformação de dados](#data-transformation-activities) para diferentes tipos de atividade. |Sim |
| inputs |Tabelas de entrada utilizadas pela atividade<br/><br/>`// one input table`<br/>`"inputs":  [ { "name": "inputtable1"  } ],`<br/><br/>`// two input tables` <br/>`"inputs":  [ { "name": "inputtable1"  }, { "name": "inputtable2"  } ],` |Sim |
| outputs |Tabelas de saída utilizadas pela atividade.<br/><br/>`// one output table`<br/>`"outputs":  [ { "name": "outputtable1" } ],`<br/><br/>`//two output tables`<br/>`"outputs":  [ { "name": "outputtable1" }, { "name": "outputtable2" }  ],` |Sim |
| linkedServiceName |Nome do serviço vinculado usado pela atividade. <br/><br/>Uma atividade pode exigir que você especifique o serviço vinculado que é vinculado ao ambiente de computação necessário. |Sim para Atividade do HDInsight e Atividade de Pontuação de Lote do Azure Machine Learning <br/><br/>Não para todas as outros |
| typeProperties |As propriedades na seção **typeProperties** dependem do tipo de atividade. Para ver as propriedades de tipo para uma atividade, clique em links para a atividade na seção anterior. | Não |
| policy |Políticas que afetam o comportamento de tempo de execução da atividade. Se não for especificado, as políticas padrão serão utilizadas. |Não |
| scheduler | A propriedade "scheduler" é usada para definir o agendamento desejado para a atividade. Suas sub-propriedades são aquelas na [propriedade de disponibilidade em um conjunto de dados](data-factory-create-datasets.md#dataset-availability). |Não |

### <a name="policies"></a>Políticas
As políticas afetam o comportamento de tempo de execução de uma atividade, especialmente quando a divisão de uma tabela é processada. A tabela a seguir fornece os detalhes.

| Propriedade | Valores permitidos | Default Value | DESCRIÇÃO |
| --- | --- | --- | --- |
| concurrency |Inteiro <br/><br/>Valor máximo: 10 |1 |Número de execuções simultâneas da atividade.<br/><br/>Determina o número de execuções de atividade paralela que podem ocorrer em divisões diferentes. Por exemplo, se uma atividade precisa passar por um grande conjunto de dados disponíveis, ter um valor de concorrência maior acelera o processamento de dados. |
| executionPriorityOrder |NewestFirst<br/><br/>OldestFirst |OldestFirst |Determina a ordem das divisões de dados que estão sendo processadas.<br/><br/>Por exemplo, se houver duas fatias (uma ocorre às 16h e a outra às 17h),e ambas estiverem com a execução pendente. Se você definir executionPriorityOrder como NewestFirst, a divisão às 17h será processada primeiro. De modo semelhante, se você definir executionPriorityORder como OldestFIrst, a fatia às 16h será processada. |
| retry novamente |Inteiro<br/><br/>O valor máximo pode ser 10 |0 |Número de novas tentativas antes do processamento de dados da divisão ser marcado como Com falha. A execução da atividade para uma divisão de dados é repetida até a contagem de repetição especificada. A nova tentativa é feita logo após a falha. |
| timeout |TimeSpan |00:00:00 |Tempo limite para a atividade. Exemplo: 00:10:00 (pressupõe um tempo de limite de 10 minutos)<br/><br/>Se um valor não for especificado ou for 0, o tempo limite será infinito.<br/><br/>Se o tempo de processamento de dados em uma divisão exceder o valor de tempo limite, ele será cancelado e o sistema tentará repetir o processamento. O número de repetições depende da propriedade de repetição. Quando atingir o tempo limite, o status será TimedOut. |
| delay |TimeSpan |00:00:00 |Especifique o atraso antes do processamento de dados da divisão começar.<br/><br/>A execução da atividade de uma fatia de dados será iniciada após o atraso passar do tempo de execução esperado.<br/><br/>Exemplo: 00:10:00 (pressupõe um atraso de 10 minutos) |
| longRetry |Inteiro<br/><br/>Valor máximo: 10 |1 |O número de tentativas repetidas longas antes que a execução da divisão falhe.<br/><br/>Tentativas de longRetry são espaçadas por longRetryInterval. Portanto, se você precisar especificar um tempo entre tentativas de repetição, use longRetry. Se Retry e longRetry forem especificados, cada tentativa de longRetry incluirá tentativas de Retry, e o número máximo de tentativas será Retry * longRetry.<br/><br/>Por exemplo, se tivermos as seguintes configurações na política de atividade:<br/>Retry: 3<br/>longRetry: 2<br/>longRetryInterval: 01:00:00<br/><br/>Presumindo que haja apenas uma fatia para execução (o status é Aguardando) e a execução da atividade sempre falhe. Inicialmente haveria três tentativas consecutivas de execução. Após cada tentativa, o status de divisão seria Retry. Depois das três primeiras tentativas, o status da divisão seria LongRetry.<br/><br/>Depois de uma hora (ou seja, valor de longRetryInteval), deve haver outro conjunto de três tentativas consecutivas de execução. Depois disso, o status da divisão seria Com falha e não haveria nova tentativa. Portanto, em geral, foram feitas seis tentativas.<br/><br/>Se qualquer execução for bem-sucedida, o status da fatia seria Ready e não haverá mais nenhuma tentativa.<br/><br/>longRetry pode ser usado em situações em que dados dependentes chegam em horários não determinísticos ou o ambiente geral está instável onde o processamento de dados ocorre. Nesses casos, fazer novas tentativas uma após a outra pode não ajudar e fazer isso após um intervalo de tempo resulta na saída desejada.<br/><br/>Advertência: não defina valores altos para longRetry ou longRetryInterval. Normalmente, os valores mais altos implicam outros problemas sistêmicos. |
| longRetryInterval |TimeSpan |00:00:00 |O intervalo entre tentativas de repetição longa |

## <a name="sample-copy-pipeline"></a>Pipeline de cópia de exemplo
No pipeline de exemplo a seguir, há uma atividade do tipo **Cópia** in the **atividades** . Neste exemplo, a [atividade de cópia](data-factory-data-movement-activities.md) copia dados de um Armazenamento de Blobs do Azure para um banco de dados SQL do Azure.

```json
{
  "name": "CopyPipeline",
  "properties": {
    "description": "Copy data from a blob to Azure SQL table",
    "activities": [
      {
        "name": "CopyFromBlobToSQL",
        "type": "Copy",
        "inputs": [
          {
            "name": "InputDataset"
          }
        ],
        "outputs": [
          {
            "name": "OutputDataset"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink",
            "writeBatchSize": 10000,
            "writeBatchTimeout": "60:00:00"
          }
        },
        "Policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ],
    "start": "2016-07-12T00:00:00Z",
    "end": "2016-07-13T00:00:00Z"
  }
}
```

Observe os seguintes pontos:

* Na seção de atividades, há apenas uma atividade cujo **tipo** é definido como **Copy**.
* A entrada da atividade é definida como **InputDataset** e a saída da atividade é definida como **OutputDataset**. Confira o artigo [Conjuntos de dados](data-factory-create-datasets.md) para definir conjuntos de dados em JSON.
* Na seção **typeProperties**, **BlobSource** é especificado como o tipo de origem e **SqlSink** é especificado como o tipo de coletor. Na seção [Atividades de movimentação de dados](#data-movement-activities), clique no repositório de dados que você quer usar como uma fonte ou um coletor para aprender mais sobre como mover dados bidirecionalmente nesse repositório de dados.

Para obter uma explicação completa da criação desse pipeline, confira [Tutorial: Copiar dados do Armazenamento de Blobs para o Banco de Dados SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="sample-transformation-pipeline"></a>Pipeline de transformação de exemplo
No pipeline de exemplo a seguir, há uma atividade do tipo **HDInsightHive** in the **atividades** . Neste exemplo, a [atividade de Hive do HDInsight](data-factory-hive-activity.md) transforma os dados de um Armazenamento de Blobs do Azure executando um arquivo de script do Hive em um cluster Hadoop do HDInsight do Azure.

```json
{
    "name": "TransformPipeline",
    "properties": {
        "description": "My first Azure Data Factory pipeline",
        "activities": [
            {
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                    "scriptLinkedService": "AzureStorageLinkedService",
                    "defines": {
                        "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                        "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
                    }
                },
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "policy": {
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Month",
                    "interval": 1
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
            }
        ],
        "start": "2016-04-01T00:00:00Z",
        "end": "2016-04-02T00:00:00Z",
        "isPaused": false
    }
}
```

Observe os seguintes pontos:

* Na seção de atividades, há apenas uma atividade cujo **tipo** é definido como **HDInsightHive**.
* O arquivo de script do Hive, **partitionweblogs.hql**, é armazenado na conta de armazenamento do Azure (especificada pelo scriptLinkedService chamado **AzureStorageLinkedService**) e na pasta **script** no contêiner **adfgetstarted**.
* A seção `defines` é usada para especificar as configurações de tempo de execução passadas para o script hive como valores de configuração do Hive (por exemplo, `${hiveconf:inputtable}`, `${hiveconf:partitionedtable}`).

A seção **typeProperties** é diferente para cada atividade de transformação. Para saber mais sobre as propriedades de tipo com suporte para uma atividade de transformação, clique na atividade de transformação na tabela [Atividades de transformação de dados](#data-transformation-activities).

Para obter uma explicação completa da criação desse pipeline, confira [Tutorial: Criar seu primeiro pipeline para processar dados usando um cluster Hadoop](data-factory-build-your-first-pipeline.md).

## <a name="multiple-activities-in-a-pipeline"></a>Várias atividades em um pipeline
Os dois pipelines de exemplo anteriores têm apenas uma atividade neles. Você pode ter mais de uma atividade em um pipeline.

Se você tiver várias atividades em um pipeline e se a saída de uma atividade não for uma entrada de outra, as atividades poderão ser executadas em paralelo se as fatias de dados de entrada das atividades estiverem prontas.

É possível encadear duas atividades fazendo com que o conjunto de dados de saída de uma atividade seja o conjunto de dados de entrada da outra atividade. A segunda atividade é executada apenas quando a primeira é concluída com êxito.

![Encadeando atividades no mesmo pipeline](./media/data-factory-create-pipelines/chaining-one-pipeline.png)

Neste exemplo, o pipeline tem duas atividades: Activity1 e Activity2. A Activity1 usa Dataset1 como entrada e gera uma saída Dataset2. A Activity usa Dataset2 como entrada e gera uma saída Dataset3. Uma vez que a saída da Activity1 (Dataset2) é a entrada da Activity2, a Activity2 é executada apenas após a conclusão com êxito da Atividade e gera a fatia Dataset2. Se a Activity1 falhar por algum motivo e não gerar a fatia Dataset2, a Activity2 não será executada para essa fatia (por exemplo: 9h às 10h).

Também é possível encadear atividades que estão em pipelines diferentes.

![Encadeando atividades em dois pipelines](./media/data-factory-create-pipelines/chaining-two-pipelines.png)

Neste exemplo, Pipeline1 tem apenas uma atividade que usa Dataset1 como entrada e gera Dataset2 como saída. O Pipeline2 também tem apenas uma atividade que usa Dataset2 como entrada e Dataset3 como saída.

Para saber mais, confira [agendamento e execução](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline).
## <a name="create-and-monitor-pipelines"></a>Criar e monitorar pipelines
Você pode criar pipelines usando uma destas ferramentas ou SDKs.

- Assistente de Cópia
- Visual Studio
- Azure PowerShell
- Modelo do Azure Resource Manager
- API REST
- API do .NET

Confira os tutoriais a seguir para obter instruções passo a passo para criar pipelines usando uma destas ferramentas ou SDKs.

- [Crie um pipeline com uma atividade de transformação de dados](data-factory-build-your-first-pipeline.md)
- [Crie um pipeline com uma atividade de movimentação de dados](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

Quando um pipeline é criado/implantado, você pode gerenciar e monitorar seus pipelines usando as folhas do portal do Azure ou Monitorar e Gerenciar Aplicativos. Confira os tópicos a seguir para obter instruções passo a passo.

- [Monitorar e gerenciar os pipelines usando as folhas do portal do Azure](data-factory-monitor-manage-pipelines.md).
- [Monitorar e gerenciar pipelines usando Monitorar e Gerenciar Aplicativos](data-factory-monitor-manage-app.md)

## <a name="onetime-pipeline"></a>Pipeline Onetime
Você pode criar e agendar um pipeline para ser executado periodicamente (por exemplo: horário ou diário) dentro dos horários inicial e final que você especificar na definição do pipeline. Confira Agendar atividades para obter detalhes. Você também pode criar um pipeline que executa apenas uma vez. Para fazer isso, defina a propriedade **pipelineMode** na definição do pipeline para **onetime** conforme mostrado no exemplo de JSON a seguir. O valor padrão dessa propriedade é **scheduled**.

```json
{
    "name": "CopyPipeline",
    "properties": {
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource",
                        "recursive": false
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataset"
                    }
                ],
                "name": "CopyActivity-0"
            }
        ],
        "pipelineMode": "OneTime"
    }
}
```

Observe o seguinte:

* As horas de **início** e **término** para o pipeline não são especificadas.
* A **disponibilidade** de conjuntos de dados de entrada e saída é especificada (**frequência** e **intervalo**), mesmo que os valores não sejam usados pelo Data Factory.
* A exibição de diagrama não mostra pipelines únicos. Esse comportamento é padrão.
* Pipelines avulsos não podem ser atualizados. É possível clonar um pipeline único, renomeá-lo, atualizar suas propriedades e implantá-lo para criar outro.

## <a name="next-steps"></a>Próximas etapas
- Para saber mais sobre conjuntos de dados, confira o artigo [Criar conjuntos de dados](data-factory-create-datasets.md).
- Para saber mais sobre como os pipelines são agendados e executados, confira o artigo [Agendamento e execução no Azure Data Factory](data-factory-scheduling-and-execution.md).
