---
title: Azure Data Factory - Referência de Script do JSON | Microsoft Docs
description: Oferece esquemas JSON para entidades do Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 279a016d60ecb1bc80baf92a7fa60365145e397d
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836260"
---
# <a name="azure-data-factory---json-scripting-reference"></a>Azure Data Factory - Referência de Script do JSON
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory.


Este artigo fornece esquemas JSON e exemplos para definir entidades do Azure Data Factory (pipeline, atividade, conjunto de dados e serviço vinculado).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="pipeline"></a>Pipeline
A estrutura geral de uma definição de pipeline é a seguinte:

```json
{
  "name": "SamplePipeline",
  "properties": {
    "description": "Describe what pipeline does",
    "activities": [
    ],
    "start": "2016-07-12T00:00:00",
    "end": "2016-07-13T00:00:00"
  }
}
```

A tabela a seguir descreve as propriedades na definição de JSON de pipeline:

| Propriedade | DESCRIÇÃO | Obrigatório
-------- | ----------- | --------
| name | Nome do pipeline. Especifique um nome que representa a ação que a atividade ou o pipeline é configurado para executar<br/><ul><li>Número máximo de caracteres: 260</li><li>Deve começar com uma letra, um número ou um sublinhado (\_)</li><li>Os seguintes caracteres não são permitidos: “.”, “+”, “?”, “/”, “<”,”>”,”*”,”%”,”&”,”:”,”\\”</li></ul> |Sim |
| description |Texto que descreve para que a atividade ou o pipeline é usado | Não |
| activities | Contém uma lista de atividades. | Sim |
| start |Data e hora de início para o pipeline. Deve estar no [formato ISO](https://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2014-10-14T16:32:41. <br/><br/>É possível especificar uma hora local, por exemplo, EST. Aqui está um exemplo: `2016-02-27T06:00:00**-05:00`, que é 6 AM EST.<br/><br/>As propriedades de início e término especificam o período ativo para o pipeline. Fatias de saída são produzidas somente nesse período ativo. |Não<br/><br/>Se você especificar um valor para a propriedade final, será necessário especificar um valor para a propriedade inicial.<br/><br/>Os horários de início e fim podem estar vazios para criar um pipeline. Você deve especificar ambos os valores para definir um período ativo de execução do pipeline. Se você não especificar horários de início e fim ao criar um pipeline, você pode defini-las usando o cmdlet Set-AzDataFactoryPipelineActivePeriod mais tarde. |
| end |Data e hora de término para o pipeline. Se especificado, deve estar no formato ISO. Por exemplo:  2014-10-14T17:32:41 <br/><br/>É possível especificar uma hora local, por exemplo, EST. Veja este exemplo: `2016-02-27T06:00:00**-05:00`, que é 6 AM EST.<br/><br/>Para executar o pipeline indefinidamente, especifique 9999-09-09 como o valor da propriedade end. |Não <br/><br/>Se você especificar um valor para a propriedade inicial, será necessário especificar um valor para a propriedade final.<br/><br/>Confira as observações para a propriedade **iniciar** . |
| isPaused |Se definido como verdadeiro, o pipeline não é executado. Valor padrão = falso. Você pode usar essa propriedade para habilitar ou desabilitar. |Não |
| pipelineMode |O método de agendamento é executado para o pipeline. Os valores permitidos são: scheduled (padrão), onetime.<br/><br/>'Scheduled' indica que o pipeline será executado em um intervalo de tempo especificado de acordo com seu período ativo (hora de início e término). “Onetime” indica que o pipeline é executado apenas uma vez. Pipelines Onetime não podem ser modificados e atualizados depois de criados atualmente. Confira [Pipeline avulso](data-factory-create-pipelines.md#onetime-pipeline) para obter detalhes sobre a configuração única. |Não |
| expirationTime |Duração de tempo após a criação pela qual o pipeline é válido e deve permanecer provisionado. Se não houver execuções ativas, com falha ou pendentes, o pipeline será excluído automaticamente depois de atingir o tempo de expiração. |Não |


## <a name="activity"></a>Atividade
A estrutura de alto nível de uma atividade dentro de uma definição de pipeline (elemento atividades) é a seguinte:

```json
{
    "name": "ActivityName",
    "description": "description",
    "type": "<ActivityType>",
    "inputs":  "[]",
    "outputs":  "[]",
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

A tabela a seguir descreve as propriedades na definição de JSON de atividade:

| Marca | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| name |Nome da atividade. Especifique um nome que representa a ação que a atividade é configurada para executar<br/><ul><li>Número máximo de caracteres: 260</li><li>Deve começar com uma letra, um número ou um sublinhado (\_)</li><li>Os seguintes caracteres não são permitidos: “.”, “+”, “?”, “/”, “<”,”>”,”*”,”%”,”&”,”:”,”\\”</li></ul> |Sim |
| description |Texto que descreve qual a utilidade da atividade. |Não |
| type |Especifica o tipo da atividade. Consulte as seções [ARMAZENAMENTOS DE DADOS](#data-stores) e [ATIVIDADES DE TRANSFORMAÇÃO DE DADOS](#data-transformation-activities) para obter diferentes tipos de atividade. |Sim |
| inputs |Tabelas de entrada utilizadas pela atividade<br/><br/>`// one input table`<br/>`"inputs":  [ { "name": "inputtable1"  } ],`<br/><br/>`// two input tables` <br/>`"inputs":  [ { "name": "inputtable1"  }, { "name": "inputtable2"  } ],` |Não para atividades HDInsightStreaming e SqlServerStoredProcedure <br/> <br/> Sim para todas as outras |
| outputs |Tabelas de saída utilizadas pela atividade.<br/><br/>`// one output table`<br/>`"outputs":  [ { "name": “outputtable1” } ],`<br/><br/>`//two output tables`<br/>`"outputs":  [ { "name": “outputtable1” }, { "name": “outputtable2” }  ],` |Sim |
| linkedServiceName |Nome do serviço vinculado usado pela atividade. <br/><br/>Uma atividade pode exigir que você especifique o serviço vinculado que é vinculado ao ambiente de computação necessário. |Sim para atividades de HDInsight, atividades de Azure Machine Learning e Atividade de Procedimento Armazenado. <br/><br/>Não para todas as outros |
| typeProperties |As propriedades na seção typeProperties dependem do tipo de atividade. |Não |
| policy |Políticas que afetam o comportamento de tempo de execução da atividade. Se não for especificado, as políticas padrão serão utilizadas. |Não |
| scheduler |A propriedade "scheduler" é usada para definir o agendamento desejado para a atividade. Suas sub-propriedades são aquelas na [propriedade de disponibilidade em um conjunto de dados](data-factory-create-datasets.md#dataset-availability). |Não |

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

### <a name="typeproperties-section"></a>Seção typeProperties
A seção typeProperties é diferente para cada tipo de atividade. Atividades de transformação possuem apenas as propriedades de tipo. Consulte [ATIVIDADES DE TRANSFORMAÇÃO DE DADOS](#data-transformation-activities) neste artigo para obter exemplos de JSON que definem atividades de transformação em um pipeline.

A **atividade de cópia** possui duas subseções na seção typeProperties: **fonte** e **coletor**. Consulte a seção [ARMAZENAMENTOS DE DADOS](#data-stores) neste artigo para exemplos de JSON que mostram como usar um armazenamento de dados como uma fonte e/ou coletor.

### <a name="sample-copy-pipeline"></a>Pipeline de cópia de exemplo
No pipeline de exemplo a seguir, há uma atividade do tipo **Cópia** in the **atividades** . Neste exemplo, a [Atividade de cópia](data-factory-data-movement-activities.md) copia dados de um Armazenamento de Blobs do Azure para um banco de dados SQL do Azure.

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
    "start": "2016-07-12T00:00:00",
    "end": "2016-07-13T00:00:00"
  }
}
```

Observe os seguintes pontos:

* Na seção de atividades, há apenas uma atividade cujo **tipo** é definido como **Copy**.
* A entrada da atividade é definida como **InputDataset** e a saída da atividade é definida como **OutputDataset**.
* Na seção **typeProperties**, **BlobSource** é especificado como o tipo de origem e **SqlSink** é especificado como o tipo de coletor.

Consulte a seção [ARMAZENAMENTOS DE DADOS](#data-stores) neste artigo para exemplos de JSON que mostram como usar um armazenamento de dados como uma fonte e/ou coletor.

Para obter uma explicação completa da criação desse pipeline, confira [Tutorial: Copiar dados do Armazenamento de Blobs para o Banco de Dados SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

### <a name="sample-transformation-pipeline"></a>Pipeline de transformação de exemplo
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
        "start": "2016-04-01T00:00:00",
        "end": "2016-04-02T00:00:00",
        "isPaused": false
    }
}
```

Observe os seguintes pontos:

* Na seção de atividades, há apenas uma atividade cujo **tipo** é definido como **HDInsightHive**.
* O arquivo de script do Hive, **partitionweblogs.hql**, é armazenado na conta de armazenamento do Azure (especificada pelo scriptLinkedService chamado **AzureStorageLinkedService**) e na pasta **script** no contêiner **adfgetstarted**.
* A seção **defines`${hiveconf:partitionedtable}` é usada para especificar as configurações de tempo de execução passadas para o script do hive como valores de configuração de Hive (por exemplo,** , `${hiveconf:inputtable}`).

Consulte [ATIVIDADES DE TRANSFORMAÇÃO DE DADOS](#data-transformation-activities) neste artigo para obter exemplos de JSON que definem atividades de transformação em um pipeline.

Para obter uma explicação completa da criação desse pipeline, confira [Tutorial: Criar seu primeiro pipeline para processar dados usando um cluster Hadoop](data-factory-build-your-first-pipeline.md).

## <a name="linked-service"></a>Serviço vinculado
A estrutura de alto nível de uma definição de um serviço vinculado é a seguinte:

```json
{
    "name": "<name of the linked service>",
    "properties": {
        "type": "<type of the linked service>",
        "typeProperties": {
        }
    }
}
```

A tabela a seguir descreve as propriedades na definição de JSON de atividade:

| Propriedade | DESCRIÇÃO | Obrigatório |
| -------- | ----------- | -------- |
| name | Nome do serviço vinculado. | Sim |
| properties - type | Tipo de serviço vinculado. Por exemplo:  Armazenamento do Azure, Banco de Dados SQL do Azure. |
| typeProperties | A seção typeProperties possui elementos que são diferentes para cada armazenamento de dados ou ambiente de computação. Confira a seção armazenamentos de dados para saber sobre todos os serviços vinculados de repositório de dados e [ambientes de computação](#compute-environments) para os serviços vinculados à computação |

## <a name="dataset"></a>Conjunto de dados
Um conjunto de dados no Azure Data Factory é definido da seguinte maneira:

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "external": <boolean flag to indicate external data. only for input datasets>,
        "linkedServiceName": "<Name of the linked service that refers to a data store.>",
        "structure": [
            {
                "name": "<Name of the column>",
                "type": "<Name of the type>"
            }
        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        },
        "availability": {
            "frequency": "<Specifies the time unit for data slice production. Supported frequency: Minute, Hour, Day, Week, Month>",
            "interval": "<Specifies the interval within the defined frequency. For example, frequency set to 'Hour' and interval set to 1 indicates that new data slices should be produced hourly>"
        },
        "policy":
        {
        }
    }
}
```

A tabela a seguir descreve as propriedades no JSON acima:

| Propriedade | DESCRIÇÃO | Obrigatório | Padrão |
| --- | --- | --- | --- |
| name | Nome do conjunto de dados. Confira [Azure Data Factory - Regras de nomenclatura](data-factory-naming-rules.md) para ver as regras de nomenclatura. |Sim |ND |
| type | Tipo de conjunto de dados. Especifique um dos tipos compatíveis com o Azure Data Factory (por exemplo: AzureBlob, AzureSqlTable). Consulte a seção [ARMAZENAMENTOS DE DADOS](#data-stores) para saber sobre todos os tipos de conjunto de dados e armazenamento de dados com suporte da data factory. |
| estrutura | Esquema do conjunto de dados. Ela contém colunas, seus tipos, etc. | Não |ND |
| typeProperties | Propriedades que correspondem ao tipo selecionado. Consulte a seção [ARMAZENAMENTOS DE DADOS](#data-stores) para saber quais são os tipos com suporte e suas propriedades. |Sim |ND |
| external | Sinalizador booliano para especificar se um conjunto de dados é explicitamente produzido por um pipeline de data factory ou não. |Não |false |
| availability | Define a janela de processamento ou o modelo de fatiamento para a produção de conjunto de dados. Para obter detalhes sobre o modelo de divisão do conjunto de dados, consulte o artigo [Agendamento e Execução](data-factory-scheduling-and-execution.md) . |Sim |ND |
| policy |Define os critérios ou a condição que as fatias de conjunto de dados devem atender. <br/><br/>Para obter detalhes, confira a seção Política do Conjunto de Dados. |Não |ND |

Cada coluna na seção **structure** contém as seguintes propriedades:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| name |Nome da coluna. |Sim |
| type |Tipo de dados da coluna.  |Não |
| culture |Cultura baseada em .NET a ser usada quando o tipo é especificado e é o tipo .NET `Datetime` ou `Datetimeoffset`. O padrão é `en-us`. |Não |
| format |O formato de cadeia de caracteres a ser usado quando o tipo é especificado e é o tipo .NET `Datetime` ou `Datetimeoffset`. |Não |

No seguinte exemplo, o conjunto de dados tem três colunas: `slicetimestamp`, `projectname` e `pageviews`, que são do tipo: String, String e Decimal, respectivamente.

```json
structure:
[
    { "name": "slicetimestamp", "type": "String"},
    { "name": "projectname", "type": "String"},
    { "name": "pageviews", "type": "Decimal"}
]
```

A tabela a seguir descreve as propriedades que você pode usar na seção de **availability**:

| Propriedade | DESCRIÇÃO | Obrigatório | Padrão |
| --- | --- | --- | --- |
| frequency |Especifica a unidade de tempo para a produção da fatia de conjunto de dados.<br/><br/><b>Frequência compatível</b>: Minuto, Hora, Dia, Semana, Mês |Sim |ND |
| intervalo |Especifica um multiplicador para frequência<br/><br/>"Intervalo de frequência x" determina a frequência com que a fatia é produzida.<br/><br/>Se você precisa que o conjunto de dados seja dividido por hora, defina <b>Frequência</b> como <b>Hora</b> e <b>intervalo</b> como <b>1</b>.<br/><br/><b>Observação</b>: Caso você especifique a frequência como Minuto, recomendamos que defina o intervalo não sendo inferior a 15 |Sim |ND |
| estilo |Especifica se a fatia deve ser produzida no início/término do intervalo.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>Se a frequência for definida como Mês e o estilo como EndOfInterval, a fatia será produzida no último dia do mês. Se o estilo for definido como StartOfInterval, a fatia será produzida no primeiro dia do mês.<br/><br/>Se a frequência for definida como Dia e o estilo como EndOfInterval, a fatia será produzida na última hora do dia.<br/><br/>Se a Frequência for definida como Hora e o estilo como EndOfInterval, a fatia será produzida ao final da hora. Por exemplo, para uma fatia de período 13h – 14h, a fatia é produzida às 14h. |Não |EndOfInterval |
| anchorDateTime |Define a posição absoluta no tempo usada pelo agendador para computar limites de fatia do conjunto de dados. <br/><br/><b>Observação</b>: Se AnchorDateTime tiver partes de datas mais granulares do que a frequência, as partes mais granulares serão ignoradas. <br/><br/>Por exemplo, se o <b>intervalo</b> for <b>por hora</b> (frequência: hora e intervalo: 1) e o <b>AnchorDateTime</b> contiver <b>minutos e segundos</b>, as partes <b>minutos e segundos</b> do AnchorDateTime serão ignoradas. |Não |01/01/0001 |
| offset |O período de tempo no qual o início e o término de todas as fatias de conjunto de dados são deslocados. <br/><br/><b>Observação</b>: Se anchorDateTime e o deslocamento forem especificados, o resultado será um deslocamento combinado. |Não |ND |

A seção de disponibilidade a seguir especifica que o conjunto de dados de saída é produzido por hora (ou) o conjunto de dados de entrada está disponível por hora:

```json
"availability":
{
    "frequency": "Hour",
    "interval": 1
}
```

A seção **política** na definição do conjunto de dados define os critérios ou a condição que as divisões de conjunto de dados devem atender.

| Nome da política | DESCRIÇÃO | Aplicado a | Obrigatório | Padrão |
| --- | --- | --- | --- | --- |
| minimumSizeMB |Valida que os dados em um **blob do Azure** atendem aos requisitos de tamanho mínimo (em megabytes). |blob do Azure |Não |ND |
| minimumRows |Valida que os dados em um **Banco de Dados SQL do Azure** ou uma **tabela do Azure** contêm o número mínimo de linhas. |<ul><li>Banco de Dados SQL do Azure</li><li>tabela do Azure</li></ul> |Não |ND |

**Exemplo:**

```json
"policy":

{
    "validation":
    {
        "minimumSizeMB": 10.0
    }
}
```

A menos que um conjunto de dados seja produzido pela Azure Data Factory, ele deverá ser marcado como **externo**. Essa configuração geralmente se aplica às entradas da primeira atividade em um pipeline, a menos que uma atividade ou pipeline encadeamento esteja sendo usado.

| name | DESCRIÇÃO | Obrigatório | Default Value |
| --- | --- | --- | --- |
| dataDelay |Tempo para esperar a verificação na disponibilidade dos dados externos de uma determinada divisão. Por exemplo, se os dados estiverem disponíveis por hora, a verificação para ver se os dados externos estão disponíveis e se a fatia correspondente está Pronta pode ser atrasada usando dataDelay.<br/><br/>Aplica-se apenas à hora atual.  Por exemplo, se agora forem 13hs e se esse valor for 10 minutos, a validação começará às 13:10hs.<br/><br/>Essa configuração não afeta fatias no passado (fatias com hora de término da fatia + dataDelay < Agora são processadas sem demora).<br/><br/>Um horário superior a 23:59 horas precisa ser especificado usando o formato `day.hours:minutes:seconds`. Por exemplo, para especificar 24 horas, não use 24:00:00; em vez disso, use 1.00:00:00. Se você usar 24:00:00, isso será tratado como 24 dias (24.00:00:00). Para 1 dia e 4 horas, especifique 1:04:00:00. |Não |0 |
| retryInterval |O tempo de espera entre uma falha e a próxima tentativa de repetição. Se uma tentativa falhar, a próxima tentativa será após retryInterval. <br/><br/>Se agora for 1:00 PM, iniciaremos a primeira tentativa. Se a duração para concluir a primeira verificação de validação for 1 minuto e a operação tiver falhado, a próxima repetição será 1:00 + 1 min (duração) + 1min (intervalo de repetição) = 1:02. <br/><br/>Para fatias no passado, não haverá nenhum atraso. A repetição acontece imediatamente. |Não |00:01:00 (1 minuto) |
| retryTimeout |O tempo limite para cada tentativa de repetição.<br/><br/>Se essa propriedade for definida para 10 minutos, a validação precisará ser concluída em 10 minutos. Se demorar mais de 10 minutos para executar a validação, a repetição atingirá o tempo limite.<br/><br/>Se todas as tentativas para a validação excederem o tempo limite, a fatia será marcada como TimedOut. |Não |00:10:00 (10 minutos) |
| maximumRetry |Número de vezes para verificar a disponibilidade dos dados externos. O valor máximo permitido é 10. |Não |3 |


## <a name="data-stores"></a>ARMAZENAMENTOS DE DADOS
A seção [serviço vinculado](#linked-service) forneceu descrições para elementos JSON que são comuns a todos os tipos de serviços vinculados. Esta seção fornece detalhes sobre os elementos JSON específicos para cada armazenamento de dados.

A seção [Conjunto de dados](#dataset) forneceu descrições para elementos JSON que são comuns a todos os tipos de conjuntos de dados. Esta seção fornece detalhes sobre os elementos JSON específicos para cada armazenamento de dados.

A seção [Atividade](#activity) forneceu descrições para elementos JSON que são comuns a todos os tipos de atividades. Esta seção fornece detalhes sobre os elementos JSON que são específicos para cada armazenamento de dados quando ele é usado como um fonte/coletor em uma atividade de cópia.

Clique no link para o armazenamento no qual você está interessado em ver os esquemas JSON para o serviço vinculado, conjunto de dados e a fonte/coletor para a atividade de cópia.

| Categoria | Armazenamento de dados
|:--- |:--- |
| **As tabelas** |[Armazenamento de Blobs do Azure](#azure-blob-storage) |
| &nbsp; |Repositório Azure Data Lake |
| &nbsp; |[Azure Cosmos DB](#azure-cosmos-db) |
| &nbsp; |[Banco de Dados SQL do Azure](#azure-sql-database) |
| &nbsp; |[SQL Data Warehouse do Azure](#azure-sql-data-warehouse) |
| &nbsp; |[Azure Search](#azure-search) |
| &nbsp; |[Armazenamento de Tabelas do Azure](#azure-table-storage) |
| **Bancos de dados** |[Amazon Redshift](#amazon-redshift) |
| &nbsp; |[IBM DB2](#ibm-db2) |
| &nbsp; |[MySQL](#mysql) |
| &nbsp; |[Oracle](#oracle) |
| &nbsp; |[PostgreSQL](#postgresql) |
| &nbsp; |[SAP Business Warehouse](#sap-business-warehouse) |
| &nbsp; |[SAP HANA](#sap-hana) |
| &nbsp; |[SQL Server](#sql-server) |
| &nbsp; |[Sybase](#sybase) |
| &nbsp; |[Teradata](#teradata) |
| **NoSQL** |[Cassandra](#cassandra) |
| &nbsp; |[MongoDB](#mongodb) |
| **Arquivo** |[Amazon S3](#amazon-s3) |
| &nbsp; |[Sistema de Arquivos](#file-system) |
| &nbsp; |[FTP](#ftp) |
| &nbsp; |[HDFS](#hdfs) |
| &nbsp; |[SFTP](#sftp) |
| **Outros** |[HTTP](#http) |
| &nbsp; |[OData](#odata) |
| &nbsp; |[ODBC](#odbc) |
| &nbsp; |[Salesforce](#salesforce) |
| &nbsp; |Tabela da Web |

## <a name="azure-blob-storage"></a>Armazenamento do Blobs do Azure

### <a name="linked-service"></a>Serviço vinculado
Há dois tipos de serviços vinculados: Serviço vinculado do Armazenamento do Azure e serviço vinculado SAS do Armazenamento do Azure.

#### <a name="azure-storage-linked-service"></a>Serviço vinculado de armazenamento do Azure
Para vincular uma conta de armazenamento do Azure ao data factory usando a **chave de conta**, crie um serviço vinculado do Armazenamento do Azure. Para definir um serviço vinculado do Armazenamento do Azure, defina o **tipo** do serviço vinculado para **AzureStorage**. Em seguida, você pode especificar as seguintes propriedades na seção **typeProperties**:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| connectionString |Especifique as informações necessárias para se conectar ao armazenamento do Azure para a propriedade connectionString. |Sim |

##### <a name="example"></a>Exemplo

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

#### <a name="azure-storage-sas-linked-service"></a>Serviço vinculado de SAS de Armazenamento do Azure
O serviço vinculado de SAS de armazenamento do Azure permite que você vincule uma conta de armazenamento do Azure ao Azure Data Factory usando uma SAS (Assinatura de Acesso Compartilhado). Isso fornece ao data factory acesso restrito/acesso total, com limite de tempo/recursos específicos (blob/contêiner) no armazenamento. Para vincular uma conta de Armazenamento do Azure ao data factory usando a Assinatura de Acesso Compartilhado, crie um serviço vinculado de SAS do Armazenamento do Azure. Para definir um serviço vinculado de SAS do Armazenamento do Azure, defina o **type** do serviço vinculado para **AzureStorageSas**. Em seguida, você pode especificar as seguintes propriedades na seção **typeProperties**:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| sasUri |Especificar o URI de Assinatura de Acesso Compartilhado para os recursos de Armazenamento do Azure, como blob, contêiner ou tabela. |Sim |

##### <a name="example"></a>Exemplo

```json
{
    "name": "StorageSasLinkedService",
    "properties": {
        "type": "AzureStorageSas",
        "typeProperties": {
            "sasUri": "<storageUri>?<sasToken>"
        }
    }
}
```

Para obter mais informações sobre esses serviços vinculados, consulte o artigo [Conector de Armazenamento de Blobs do Azure](data-factory-azure-blob-connector.md#linked-service-properties).

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados de Blob do Azure, defina o **type** do conjunto de dados para **AzureBlob**. Em seguida, especifique as seguintes propriedades específicas do Blob do Azure na seção **typeProperties**:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| folderPath |Caminho para o contêiner e a pasta no armazenamento de blob. Exemplo: myblobcontainer\myblobfolder\ |Sim |
| fileName |O nome do blob. fileName é opcional e diferencia maiúsculas de minúsculas.<br/><br/>Caso você especifique um nome de arquivo, a atividade (incluindo Cópia) funcionará no Blob específico.<br/><br/>Quando fileName não for especificado, a Cópia incluirá todos os Blobs do folderPath para o conjunto de dados de entrada.<br/><br/>Quando fileName não for especificado para um conjunto de dados de saída, o nome do arquivo gerado seria a seguir este formato: `Data.<Guid>.txt` (por exemplo:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Não |
| partitionedBy |partitionedBy é uma propriedade opcional. Você pode usá-lo para especificar um folderPath dinâmico e o nome de arquivo para dados de série temporal. Por exemplo, folderPath pode ser parametrizado para cada hora dos dados. |Não |
| format | Há suporte para os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Defina a propriedade **type** sob formato como um desses valores. Para saber mais, veja as seções [Formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [Formato Json](data-factory-supported-file-and-compression-formats.md#json-format), [Formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format), e [Formato Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format). <br><br> Se você quiser **copiar arquivos no estado em que se encontram** entre repositórios baseados em arquivo (cópia binária), ignore a seção de formato nas duas definições de conjunto de dados de entrada e de saída. |Não |
| compression | Especifique o tipo e o nível de compactação para os dados. Tipos compatíveis são: **GZip**, **Deflate**, **BZip2** e **ZipDeflate**. Níveis compatíveis são: **Ideal** e **Mais Rápido**. Para saber mais, confira [File and compression formats in Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support) (Formatos de arquivo e de compactação no Azure Data Factory). |Não |

#### <a name="example"></a>Exemplo

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "input.log",
            "folderPath": "adfgetstarted/inputdata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
 ```


Para obter mais informações, consulte o artigo [Conector de Blob do Azure](data-factory-azure-blob-connector.md#dataset-properties).

### <a name="blobsource-in-copy-activity"></a>BlobSource na Atividade de Cópia
Se você estiver copiando dados de um Armazenamento de Blobs do Azure, defina o **source type** da atividade de cópia para **BlobSource** e especifique as propriedades a seguir na seção **source**:

| Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| recursive |Indica se os dados são lidos recursivamente por meio de subpastas ou somente da pasta especificada. |True (valor padrão), False |Não |

#### <a name="example-blobsource"></a>Exemplo: **BlobSource**
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureSqlOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "SqlSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
### <a name="blobsink-in-copy-activity"></a>BlobSink na Atividade de Cópia
Se você estiver copiando dados para um Armazenamento de Blobs do Azure, defina o **sink type** da atividade de cópia para **BlobSource** e especifique as propriedades a seguir na seção **source**:

| Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| copyBehavior |Define o comportamento de cópia quando a origem é BlobSource ou FileSystem. |<b>PreserveHierarchy:</b> preserva a hierarquia de arquivos na pasta de destino. O caminho relativo do arquivo de origem para a pasta de origem é idêntico ao caminho relativo do arquivo de destino para a pasta de destino.<br/><br/><b>FlattenHierarchy:</b> todos os arquivos da pasta de origem estão no primeiro nível da pasta de destino. Os arquivos de destino têm o nome gerado automaticamente. <br/><br/><b>MergeFiles (padrão)</b>: mescla todos os arquivos da pasta de origem em um arquivo. Se o nome do arquivo/blob for especificado, o nome do arquivo mesclado será o nome especificado; caso contrário, será o nome de arquivo gerado automaticamente. |Não |

#### <a name="example-blobsink"></a>Exemplo: BlobSink

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSQLInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Para obter mais informações, consulte o artigo [Conector de Blob do Azure](data-factory-azure-blob-connector.md#copy-activity-properties).

## <a name="azure-data-lake-store"></a>Repositório Azure Data Lake

### <a name="linked-service"></a>Serviço vinculado
Para definir um serviço vinculado do Azure Data Lake Store, defina o tipo do serviço vinculado para **AzureDataLakeStore**e especifique as propriedades a seguir na seção **typeProperties**:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como: **AzureDataLakeStore** | Sim |
| dataLakeStoreUri | Especifica informações sobre a conta do Repositório Data Lake do Azure. Ele está no seguinte formato: `https://[accountname].azuredatalakestore.net/webhdfs/v1` ou `adl://[accountname].azuredatalakestore.net/`. | Sim |
| subscriptionId | Id de assinatura do Azure ao qual pertence Data Lake Store. | Obrigatório para coletor |
| resourceGroupName | Nome do grupo de recursos do Azure ao qual pertence Data Lake Store. | Obrigatório para coletor |
| servicePrincipalId | Especifique a ID do cliente do aplicativo. | Sim (para autenticação de entidade de serviço) |
| servicePrincipalKey | Especifique a chave do aplicativo. | Sim (para autenticação de entidade de serviço) |
| tenant | Especifique as informações de locatário (domínio nome ou ID do Locatário) em que o aplicativo reside. É possível recuperá-lo focalizando o canto superior direito do portal do Azure. | Sim (para autenticação de entidade de serviço) |
| authorization | Clique no botão **Autorizar** no **Editor do Data Factory** e insira as suas credenciais, que atribui a URL de autorização gerada automaticamente a essa propriedade. | Sim (para autenticação de credenciais de usuário)|
| sessionId | A ID de sessão OAuth da sessão de autorização OAuth. Cada ID da sessão é exclusiva e pode ser usado somente uma vez. Essa configuração é gerada automaticamente quando você usa o Editor do Data Factory. | Sim (para autenticação de credenciais de usuário) |

#### <a name="example-using-service-principal-authentication"></a>Exemplo: usando a autenticação da entidade de serviço
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info. Example: microsoft.onmicrosoft.com>"
        }
    }
}
```

#### <a name="example-using-user-credential-authentication"></a>Exemplo: usando a autenticação de credenciais de usuário
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<session ID>",
            "authorization": "<authorization URL>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

Para saber mais, consulte o artigo [Conector do Azure Data Lake Store](data-factory-azure-datalake-connector.md#linked-service-properties).

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados do Azure Data Lake Store, defina o **type** do conjunto de dados para **AzureDataLakeStore** e especifique as propriedades a seguir na seção **typeProperties**:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| folderPath |Caminho para o contêiner e a pasta no repositório do Azure Data Lake. |Sim |
| fileName |O nome do arquivo no repositório Azure Data Lake. fileName é opcional e diferencia maiúsculas de minúsculas. <br/><br/>Caso você especifique um nome de arquivo, a atividade (incluindo Cópia) funcionará no arquivo específico.<br/><br/>Quando fileName não for especificado, a Cópia incluirá todos os arquivos do folderPath para o conjunto de dados de entrada.<br/><br/>Quando fileName não for especificado para um conjunto de dados de saída, o nome do arquivo gerado seria a seguir este formato: `Data.<Guid>.txt` (por exemplo:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Não |
| partitionedBy |partitionedBy é uma propriedade opcional. Você pode usá-lo para especificar um folderPath dinâmico e o nome de arquivo para dados de série temporal. Por exemplo, folderPath pode ser parametrizado para cada hora dos dados. |Não |
| format | Há suporte para os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Defina a propriedade **type** sob formato como um desses valores. Para saber mais, veja as seções [Formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [Formato Json](data-factory-supported-file-and-compression-formats.md#json-format), [Formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format), e [Formato Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format). <br><br> Se você quiser **copiar arquivos no estado em que se encontram** entre repositórios baseados em arquivo (cópia binária), ignore a seção de formato nas duas definições de conjunto de dados de entrada e de saída. |Não |
| compression | Especifique o tipo e o nível de compactação para os dados. Tipos compatíveis são: **GZip**, **Deflate**, **BZip2** e **ZipDeflate**. Níveis compatíveis são: **Ideal** e **Mais Rápido**. Para saber mais, confira [File and compression formats in Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support) (Formatos de arquivo e de compactação no Azure Data Factory). |Não |

#### <a name="example"></a>Exemplo
```json
{
    "name": "AzureDataLakeStoreInput",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/input/",
            "fileName": "SearchLog.tsv",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            }
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Para saber mais, consulte o artigo [Conector do Azure Data Lake Store](data-factory-azure-datalake-connector.md#dataset-properties).

### <a name="azure-data-lake-store-source-in-copy-activity"></a>Fonte do Azure Data Lake Store na Atividade de Cópia
Se você estiver copiando dados de um Azure Data Lake Store, defina o **source type** da atividade de cópia para **AzureDataLakeStoreSource** e especifique as propriedades a seguir na seção **source**:

**AzureDataLakeStoreSource** dá suporte à seção **typeProperties** das seguintes propriedades:

| Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| recursive |Indica se os dados são lidos recursivamente por meio de subpastas ou somente da pasta especificada. |True (valor padrão), False |Não |

#### <a name="example-azuredatalakestoresource"></a>Exemplo: AzureDataLakeStoreSource

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureDakeLaketoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureDataLakeStoreInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "AzureDataLakeStoreSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Para saber mais, consulte o artigo [Conector do Azure Data Lake Store](data-factory-azure-datalake-connector.md#copy-activity-properties).

### <a name="azure-data-lake-store-sink-in-copy-activity"></a>Coletor do Azure Data Lake Store na Atividade de Cópia
Se você estiver copiando dados para um Azure Data Lake Store, defina o **sink type** da atividade de cópia para **AzureDataLakeStoreSink** e especifique as propriedades a seguir na seção **sink**:

| Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| copyBehavior |Especifica o comportamento da cópia. |<b>PreserveHierarchy:</b> preserva a hierarquia de arquivos na pasta de destino. O caminho relativo do arquivo de origem para a pasta de origem é idêntico ao caminho relativo do arquivo de destino para a pasta de destino.<br/><br/><b>FlattenHierarchy:</b> todos os arquivos da pasta de origem são criados no primeiro nível da pasta de destino. Os arquivos de destino são criados com o nome gerado automaticamente.<br/><br/><b>MergeFiles:</b> mescla todos os arquivos da pasta de origem em um arquivo. Se o nome do arquivo/blob for especificado, o nome do arquivo mesclado será o nome especificado; caso contrário, será o nome de arquivo gerado automaticamente. |Não |

#### <a name="example-azuredatalakestoresink"></a>Exemplo: AzureDataLakeStoreSink
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoDataLake",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureDataLakeStoreOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "AzureDataLakeStoreSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Para saber mais, consulte o artigo [Conector do Azure Data Lake Store](data-factory-azure-datalake-connector.md#copy-activity-properties).

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="linked-service"></a>Serviço vinculado
Para definir um serviço vinculado do Azure Cosmos DB, defina o **type** do serviço vinculado como **DocumentDb** e especifique as seguintes propriedades na seção **typeProperties**:

| **Property** | **Descrição** | **Necessária** |
| --- | --- | --- |
| connectionString |Especifique as informações necessárias para se conectar ao banco de dados do Azure Cosmos DB. |Sim |

#### <a name="example"></a>Exemplo

```json
{
    "name": "CosmosDBLinkedService",
    "properties": {
        "type": "DocumentDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
        }
    }
}
```
Para obter mais informações, consulte o artigo [Conector do Azure Cosmos DB](data-factory-azure-documentdb-connector.md#linked-service-properties).

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados do Azure Cosmos DB, defina o **type** do conjunto de dados como **DocumentDbCollection** e especifique as seguintes propriedades na seção **typeProperties**:

| **Property** | **Descrição** | **Necessária** |
| --- | --- | --- |
| collectionName |Nome da coleção do Azure Cosmos DB. |Sim |

#### <a name="example"></a>Exemplo

```json
{
    "name": "PersonCosmosDBTable",
    "properties": {
        "type": "DocumentDbCollection",
        "linkedServiceName": "CosmosDBLinkedService",
        "typeProperties": {
            "collectionName": "Person"
        },
        "external": true,
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```
Para obter mais informações, consulte o artigo [Conector do Azure Cosmos DB](data-factory-azure-documentdb-connector.md#dataset-properties).

### <a name="azure-cosmos-db-collection-source-in-copy-activity"></a>Fonte de coleta do Azure Cosmos DB na Atividade de Cópia
Se você estiver copiando dados de um Azure Cosmos DB, defina o **source type** da atividade de cópia como **DocumentDbCollectionSource** e especifique as seguintes propriedades na seção **source**:


| **Property** | **Descrição** | **Valores permitidos** | **Obrigatório** |
| --- | --- | --- | --- |
| query |Especifique a consulta para ler dados. |Cadeia de consulta com suporte no Azure Cosmos DB. <br/><br/>Exemplo: `SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Não <br/><br/>Se não for especificada, a instrução SQL executada será: `select <columns defined in structure> from mycollection` |
| nestingSeparator |Caractere especial para indicar que o documento está aninhado |Qualquer caractere. <br/><br/>O Azure Cosmos DB é um repositório NoSQL para documentos JSON, em que estruturas aninhadas são permitidas. O Azure Data Factory permite que o usuário indique a hierarquia via nestingSeparator, que é "." nos exemplos acima. Com o separador, a atividade de cópia vai gerar o objeto "Name" com três elementos filhos First, Middle e Last, de acordo com "Name.First", "Name.Middle" e "Name.Last" na definição da tabela. |Não |

#### <a name="example"></a>Exemplo

```json
{
    "name": "DocDbToBlobPipeline",
    "properties": {
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "DocumentDbCollectionSource",
                    "query": "SELECT Person.Id, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person",
                    "nestingSeparator": "."
                },
                "sink": {
                    "type": "BlobSink",
                    "blobWriterAddHeader": true,
                    "writeBatchSize": 1000,
                    "writeBatchTimeout": "00:00:59"
                }
            },
            "inputs": [{
                "name": "PersonCosmosDBTable"
            }],
            "outputs": [{
                "name": "PersonBlobTableOut"
            }],
            "policy": {
                "concurrency": 1
            },
            "name": "CopyFromCosmosDbToBlob"
        }],
        "start": "2016-04-01T00:00:00",
        "end": "2016-04-02T00:00:00"
    }
}
```

### <a name="azure-cosmos-db-collection-sink-in-copy-activity"></a>Coletor para coleta do Azure Cosmos DB na Atividade de Cópia
Se você estiver copiando dados para um Azure Cosmos DB, defina o **sink type** da atividade de cópia como **DocumentDbCollectionSink** e especifique as seguintes propriedades na seção **sink**:

| **Property** | **Descrição** | **Valores permitidos** | **Necessária** |
| --- | --- | --- | --- |
| nestingSeparator |Um caractere especial no nome da coluna de fonte para indicar que esse documento aninhado é necessário. <br/><br/>No exemplo acima: `Name.First` na tabela de saída produz a seguinte estrutura JSON no documento do Cosmos DB:<br/><br/>"Name": {<br/>    "First": "John"<br/>}, |Caractere que é usado para separar os níveis de aninhamento.<br/><br/>O valor padrão é `.` (ponto). |Caractere que é usado para separar os níveis de aninhamento. <br/><br/>O valor padrão é `.` (ponto). |
| writeBatchSize |Número de solicitações paralelas ao serviço Azure Cosmos DB para criar documentos.<br/><br/>Você pode ajustar o desempenho ao copiar dados de/para o Azure Cosmos DB usando essa propriedade. Você pode esperar um melhor desempenho ao aumentar writeBatchSize, pois mais solicitações paralelas para o Azure Cosmos DB são enviadas. No entanto, será necessário evitar a limitação que pode gerar a mensagem de erro: "A taxa de solicitação é alta".<br/><br/>A limitação é decida por uma série de fatores, incluindo o tamanho dos documentos, o número de termos incluídos, a política de indexação da coleção de destino, etc. Para operações de cópia, você pode usar uma coleção melhor (por exemplo, S3) para ter mais taxa de transferência disponível (solicitação de 2.500 unidades/segundo). |Inteiro |Não (padrão: 5) |
| writeBatchTimeout |Tempo de espera para a operação ser concluída antes de atingir o tempo limite. |TimeSpan<br/><br/> Exemplo: “00:30:00” (30 minutos). |Não |

#### <a name="example"></a>Exemplo

```json
{
    "name": "BlobToDocDbPipeline",
    "properties": {
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "DocumentDbCollectionSink",
                    "nestingSeparator": ".",
                    "writeBatchSize": 2,
                    "writeBatchTimeout": "00:00:00"
                },
                "translator": {
                    "type": "TabularTranslator",
                    "ColumnMappings": "FirstName: Name.First, MiddleName: Name.Middle, LastName: Name.Last, BusinessEntityID: BusinessEntityID, PersonType: PersonType, NameStyle: NameStyle, Title: Title, Suffix: Suffix"
                }
            },
            "inputs": [{
                "name": "PersonBlobTableIn"
            }],
            "outputs": [{
                "name": "PersonCosmosDbTableOut"
            }],
            "policy": {
                "concurrency": 1
            },
            "name": "CopyFromBlobToCosmosDb"
        }],
        "start": "2016-04-14T00:00:00",
        "end": "2016-04-15T00:00:00"
    }
}
```

Para obter mais informações, consulte o artigo [Conector do Azure Cosmos DB](data-factory-azure-documentdb-connector.md#copy-activity-properties).

## <a name="azure-sql-database"></a>Banco de Dados SQL do Azure

### <a name="linked-service"></a>Serviço vinculado
Para definir um serviço vinculado do Banco de Dados SQL do Azure, defina o **type** do serviço vinculado para **AzureSqlDatabase** e especifique as propriedades a seguir na seção **typeProperties**:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| connectionString |Especifique as informações necessárias para se conectar à instância do Banco de Dados SQL do Azure para a propriedade connectionString. |Sim |

#### <a name="example"></a>Exemplo
```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
    }
}
```

Para obter mais informações, consulte o artigo [Conector de SQL do Azure](data-factory-azure-sql-connector.md#linked-service-properties).

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados do Banco de Dados SQL do Azure, defina o **type** do conjunto de dados para **AzureSqlTable** e especifique as propriedades a seguir na seção **typeProperties**:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| tableName |Nome da tabela ou exibição na instância do Banco de Dados SQL do Azure à qual o serviço vinculado se refere. |Sim |

#### <a name="example"></a>Exemplo

```json
{
    "name": "AzureSqlInput",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
Para obter mais informações, consulte o artigo [Conector de SQL do Azure](data-factory-azure-sql-connector.md#dataset-properties).

### <a name="sql-source-in-copy-activity"></a>Origem do SQL na atividade de cópia
Se você estiver copiando dados de um Banco de Dados SQL do Azure, defina o **source type** da atividade de cópia para **SqlSource** e especifique as propriedades a seguir na seção **source**:


| Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| sqlReaderQuery |Utiliza a consulta personalizada para ler os dados. |Cadeia de caracteres de consulta SQL. Exemplo: `select * from MyTable`. |Não |
| sqlReaderStoredProcedureName |Nome do procedimento armazenado que lê os dados da tabela de origem. |Nome do procedimento armazenado. |Não |
| storedProcedureParameters |Parâmetros para o procedimento armazenado. |Pares de nome/valor. Nomes e uso de maiúsculas e minúsculas de parâmetros devem corresponder aos nomes e o uso de maiúsculas e minúsculas dos parâmetros do procedimento armazenado. |Não |

#### <a name="example"></a>Exemplo

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSQLInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
Para obter mais informações, consulte o artigo [Conector de SQL do Azure](data-factory-azure-sql-connector.md#copy-activity-properties).

### <a name="sql-sink-in-copy-activity"></a>Coletor do SQL na Atividade de Cópia
Se você estiver copiando dados para um Banco de Dados SQL do Azure, defina o **sink type** da atividade de cópia para **SqlSink** e especifique as propriedades a seguir na seção **sink**:

| Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| writeBatchTimeout |Tempo de espera para a operação de inserção em lotes ser concluída antes de atingir o tempo limite. |TimeSpan<br/><br/> Exemplo: “00:30:00” (30 minutos). |Não |
| writeBatchSize |Insere dados na tabela SQL quando o tamanho do buffer atinge writeBatchSize. |Inteiro (número de linhas) |Não (padrão: 10000) |
| sqlWriterCleanupScript |Especifique uma consulta da Atividade de Cópia a executar para que os dados de uma fatia específica sejam removidos. |Uma instrução de consulta. |Não |
| sliceIdentifierColumnName |Especifique um nome de coluna para a Atividade de Cópia a preencher com o identificador de fatias gerado automaticamente, que é usado para limpar os dados de uma fatia específica ao executar novamente. |Nome de uma coluna com tipo de dados de binário (32). |Não |
| sqlWriterStoredProcedureName |Nome do procedimento armazenado que upserts (atualiza/insere) na tabela de destino. |Nome do procedimento armazenado. |Não |
| storedProcedureParameters |Parâmetros para o procedimento armazenado. |Pares de nome/valor. Nomes e uso de maiúsculas e minúsculas de parâmetros devem corresponder aos nomes e o uso de maiúsculas e minúsculas dos parâmetros do procedimento armazenado. |Não |
| sqlWriterTableType |Especifique um nome do tipo de tabela a ser usado no procedimento armazenado. A atividade de cópia disponibiliza aqueles dados sendo movidos em uma tabela temporária com esse tipo de tabela. O código de procedimento armazenado pode mesclar os dados sendo copiados com dados existentes. |Um nome de tipo de tabela. |Não |

#### <a name="example"></a>Exemplo

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureSqlOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource",
                    "blobColumnSeparators": ","
                },
                "sink": {
                    "type": "SqlSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Para obter mais informações, consulte o artigo [Conector de SQL do Azure](data-factory-azure-sql-connector.md#copy-activity-properties).

## <a name="azure-sql-data-warehouse"></a>SQL Data Warehouse do Azure

### <a name="linked-service"></a>Serviço vinculado
Para definir um serviço vinculado do SQL Data Warehouse do Azure, defina o **type** do serviço vinculado para **AzureSqlDW** e especifique as propriedades a seguir na seção **typeProperties**:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| connectionString |Especifique as informações necessárias para se conectar à instância do SQL Data Warehouse do Azure para a propriedade connectionString. |Sim |



#### <a name="example"></a>Exemplo

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
    }
}
```

Para obter mais informações, consulte o artigo [Conector do SQL Data Warehouse do Azure](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties).

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados do SQL Data Warehouse do Azure, defina o **type** do conjunto de dados para **AzureSqlDWTable** e especifique as propriedades a seguir na seção **typeProperties**:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| tableName |Nome da tabela ou exibição no banco de dados SQL Data Warehouse do Azure ao qual o serviço vinculado se refere. |Sim |

#### <a name="example"></a>Exemplo

```json
{
    "name": "AzureSqlDWInput",
    "properties": {
    "type": "AzureSqlDWTable",
        "linkedServiceName": "AzureSqlDWLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Para obter mais informações, consulte o artigo [Conector do SQL Data Warehouse do Azure](data-factory-azure-sql-data-warehouse-connector.md#dataset-properties).

### <a name="sql-dw-source-in-copy-activity"></a>Origem do SQL DW na Atividade de Cópia
Se você estiver copiando dados de um SQL Data Warehouse do Azure, defina o **source type** da atividade de cópia para **SqlDWSource** e especifique as propriedades a seguir na seção **source**:


| Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| sqlReaderQuery |Utiliza a consulta personalizada para ler os dados. |Cadeia de caracteres de consulta SQL. Por exemplo: `select * from MyTable`. |Não |
| sqlReaderStoredProcedureName |Nome do procedimento armazenado que lê os dados da tabela de origem. |Nome do procedimento armazenado. |Não |
| storedProcedureParameters |Parâmetros para o procedimento armazenado. |Pares de nome/valor. Nomes e uso de maiúsculas e minúsculas de parâmetros devem corresponder aos nomes e o uso de maiúsculas e minúsculas dos parâmetros do procedimento armazenado. |Não |

#### <a name="example"></a>Exemplo

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLDWtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSqlDWInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlDWSource",
                    "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Para obter mais informações, consulte o artigo [Conector do SQL Data Warehouse do Azure](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties).

### <a name="sql-dw-sink-in-copy-activity"></a>Coletor do SQL DW na Atividade de Cópia
Se você estiver copiando dados de um SQL Data Warehouse do Azure, defina o **sink type** da atividade de cópia para **SqlDWSink** e especifique as propriedades a seguir na seção **sink**:

| Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| sqlWriterCleanupScript |Especifique uma consulta da Atividade de Cópia a executar para que os dados de uma fatia específica sejam removidos. |Uma instrução de consulta. |Não |
| allowPolyBase |Indica se o PolyBase (quando aplicável) deve ser utilizado em vez do mecanismo BULKINSERT. <br/><br/> **Usar o PolyBase é a maneira recomendada para carregar dados no SQL Data Warehouse.** |verdadeiro <br/>False (padrão) |Não |
| polyBaseSettings |Um grupo de propriedades que pode ser especificado quando a propriedade **allowPolybase** está definida como **true**. |&nbsp; |Não |
| rejectValue |Especifica o número ou o percentual de linhas que podem ser rejeitadas antes de a consulta falhar. <br/><br/>Saiba mais sobre as opções de rejeição do PolyBase na seção **Argumentos** do tópico [CRIAR TABELA EXTERNA (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx) . |0 (padrão), 1, 2, … |Não |
| rejectType |Especifica se a opção rejectValue é especificada como um valor literal ou um percentual. |Valor (padrão), Percentual |Não |
| rejectSampleValue |Determina o número de linhas a serem recuperadas antes de o PolyBase recalcular o percentual de linhas rejeitadas. |1, 2, … |Sim, se **rejectType** for **percentual** |
| useTypeDefault |Especifica como tratar valores ausentes nos arquivos de texto delimitados quando PolyBase recupera dados do arquivo de texto.<br/><br/>Saiba mais sobre essa propriedade na seção Argumentos em [CRIAR FORMATO DE ARQUIVO EXTERNO (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx). |True, False (padrão) |Não |
| writeBatchSize |Insere dados na tabela SQL quando o tamanho do buffer atinge writeBatchSize |Inteiro (número de linhas) |Não (padrão: 10000) |
| writeBatchTimeout |Tempo de espera para a operação de inserção em lotes ser concluída antes de atingir o tempo limite. |TimeSpan<br/><br/> Exemplo: “00:30:00” (30 minutos). |Não |

#### <a name="example"></a>Exemplo

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQLDW",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureSqlDWOutput"
            }],
            "typeProperties": {
                "source": {
                "type": "BlobSource",
                    "blobColumnSeparators": ","
                },
                "sink": {
                    "type": "SqlDWSink",
                    "allowPolyBase": true
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Para obter mais informações, consulte o artigo [Conector do SQL Data Warehouse do Azure](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties).

## <a name="azure-search"></a>Azure Search

### <a name="linked-service"></a>Serviço vinculado
Para definir um serviço vinculado do Azure Search, defina o **type** do serviço vinculado para **AzureSearch**e especifique as propriedades a seguir na seção **typeProperties**:

| Propriedade | DESCRIÇÃO | Obrigatório |
| -------- | ----------- | -------- |
| url | URL para o serviço Azure Search. | Sim |
| key | Chave de administração para o serviço Azure Search. | Sim |

#### <a name="example"></a>Exemplo

```json
{
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": "<AdminKey>"
        }
    }
}
```

Para obter mais informações, consulte o artigo [Conector do Azure Search](data-factory-azure-search-connector.md#linked-service-properties).

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados do Azure Search, defina o **type** do conjunto de dados para **AzureSearchIndex** e especifique as propriedades a seguir na seção **typeProperties**:

| Propriedade | DESCRIÇÃO | Obrigatório |
| -------- | ----------- | -------- |
| type | A propriedade type deve ser definida como: **AzureSearchIndex**.| Sim |
| indexName | Nome do índice do Azure Search. O Data Factory não cria o índice. O índice deve existir no Azure Search. | Sim |

#### <a name="example"></a>Exemplo

```json
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "linkedServiceName": "AzureSearchLinkedService",
        "typeProperties": {
            "indexName": "products"
        },
        "availability": {
            "frequency": "Minute",
            "interval": 15
        }
    }
}
```

Para obter mais informações, consulte o artigo [Conector do Azure Search](data-factory-azure-search-connector.md#dataset-properties).

### <a name="azure-search-index-sink-in-copy-activity"></a>Coletor do Índice do Azure Search na Atividade de Cópia
Se você estiver copiando dados para um índice do Azure Search, defina o **sink type** da atividade de cópia para **AzureSearchIndexSink** e especifique as propriedades a seguir na seção **sink**:

| Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório |
| -------- | ----------- | -------------- | -------- |
| WriteBehavior | Especifica se deve mesclar ou substituir quando já existe um documento no índice. | Merge (padrão)<br/>Carregar| Não |
| WriteBatchSize | Carrega dados para o índice do Azure Search quando o tamanho do buffer atinge writeBatchSize. | 1 a 1.000. O valor padrão é 1000. | Não |

#### <a name="example"></a>Exemplo

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "SqlServertoAzureSearchIndex",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": " SqlServerInput"
            }],
            "outputs": [{
                "name": "AzureSearchIndexDataset"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "AzureSearchIndexSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Para obter mais informações, consulte o artigo [Conector do Azure Search](data-factory-azure-search-connector.md#copy-activity-properties).

## <a name="azure-table-storage"></a>Armazenamento de Tabelas do Azure

### <a name="linked-service"></a>Serviço vinculado
Há dois tipos de serviços vinculados: Serviço vinculado do Armazenamento do Azure e serviço vinculado SAS do Armazenamento do Azure.

#### <a name="azure-storage-linked-service"></a>Serviço vinculado de armazenamento do Azure
Para vincular uma conta de armazenamento do Azure ao data factory usando a **chave de conta**, crie um serviço vinculado do Armazenamento do Azure. Para definir um serviço vinculado do Armazenamento do Azure, defina o **tipo** do serviço vinculado para **AzureStorage**. Em seguida, você pode especificar as seguintes propriedades na seção **typeProperties**:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type |A propriedade type deve ser definida como: **AzureStorage** |Sim |
| connectionString |Especifique as informações necessárias para se conectar ao armazenamento do Azure para a propriedade connectionString. |Sim |

**Exemplo:**

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

#### <a name="azure-storage-sas-linked-service"></a>Serviço vinculado de SAS de Armazenamento do Azure
O serviço vinculado de SAS de armazenamento do Azure permite que você vincule uma conta de armazenamento do Azure ao Azure Data Factory usando uma SAS (Assinatura de Acesso Compartilhado). Isso fornece ao data factory acesso restrito/acesso total, com limite de tempo/recursos específicos (blob/contêiner) no armazenamento. Para vincular uma conta de Armazenamento do Azure ao data factory usando a Assinatura de Acesso Compartilhado, crie um serviço vinculado de SAS do Armazenamento do Azure. Para definir um serviço vinculado de SAS do Armazenamento do Azure, defina o **type** do serviço vinculado para **AzureStorageSas**. Em seguida, você pode especificar as seguintes propriedades na seção **typeProperties**:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type |A propriedade type deve ser definida como: **AzureStorageSas** |Sim |
| sasUri |Especificar o URI de Assinatura de Acesso Compartilhado para os recursos de Armazenamento do Azure, como blob, contêiner ou tabela. |Sim |

**Exemplo:**

```json
{
    "name": "StorageSasLinkedService",
    "properties": {
        "type": "AzureStorageSas",
        "typeProperties": {
            "sasUri": "<storageUri>?<sasToken>"
        }
    }
}
```

Para obter mais informações sobre esses serviços vinculados, consulte o artigo [Conector de Armazenamento de Tabelas do Azure](data-factory-azure-table-connector.md#linked-service-properties).

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados da Tabela do Azure, defina o **type** do conjunto de dados para **AzureTable** e especifique as propriedades a seguir na seção **typeProperties**:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| tableName |Nome da tabela na instância do Banco de Dados da Tabela do Azure à qual o serviço vinculado se refere. |Sim. Quando um nome de tabela é especificado sem uma azureTableSourceQuery, todos os registros da tabela são copiados para o destino. Se uma azureTableSourceQuery também for especificada, os registros da tabela que atende à consulta são copiados para o destino. |

#### <a name="example"></a>Exemplo

```json
{
    "name": "AzureTableInput",
    "properties": {
        "type": "AzureTable",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Para obter mais informações sobre esses serviços vinculados, consulte o artigo [Conector de Armazenamento de Tabelas do Azure](data-factory-azure-table-connector.md#dataset-properties).

### <a name="azure-table-source-in-copy-activity"></a>Origem da Tabela do Azure na Atividade de Cópia
Se você estiver copiando dados de um Armazenamento de Tabelas do Azure, defina o **source type** da atividade de cópia para **AzureTableSource** e especifique as propriedades a seguir na seção **source**:

| Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| azureTableSourceQuery |Utiliza a consulta personalizada para ler os dados. |Cadeia de caracteres de consulta de tabela do Azure. Veja exemplos na próxima seção. |Nº Quando um nome de tabela é especificado sem uma azureTableSourceQuery, todos os registros da tabela são copiados para o destino. Se uma azureTableSourceQuery também for especificada, os registros da tabela que atende à consulta são copiados para o destino. |
| azureTableSourceIgnoreTableNotFound |Indique se assimilar a exceção da tabela não existe. |TRUE<br/>FALSE |Não |

#### <a name="example"></a>Exemplo

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureTabletoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureTableInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "AzureTableSource",
                    "AzureTableSourceQuery": "PartitionKey eq 'DefaultPartitionKey'"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Para obter mais informações sobre esses serviços vinculados, consulte o artigo [Conector de Armazenamento de Tabelas do Azure](data-factory-azure-table-connector.md#copy-activity-properties).

### <a name="azure-table-sink-in-copy-activity"></a>Coletor da Tabela do Azure na Atividade de Cópia
Se você estiver copiando dados para um Armazenamento de Tabelas do Azure, defina o **sink type** da atividade de cópia para **AzureTableSink** e especifique as propriedades a seguir na seção **sink**:

| Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| azureTableDefaultPartitionKeyValue |Valor de chave de partição padrão que pode ser utilizado pelo coletor. |Um valor de cadeia de caracteres. |Não |
| azureTablePartitionKeyName |Especifique o nome da coluna cujos valores são usados como chaves de partição. Se não especificado, AzureTableDefaultPartitionKeyValue será utilizado como a chave da partição. |Um nome de coluna. |Não |
| azureTableRowKeyName |Especifique o nome da coluna cujos valores são usados como chaves de linha. Se não especificado, um GUID é usado para cada linha. |Um nome de coluna. |Não |
| azureTableInsertType |O modo para inserir dados na tabela do Azure.<br/><br/>Essa propriedade controla se linhas existentes na tabela de saída com a partição correspondente e as chaves de linha terão seus valores substituídos ou mesclados. <br/><br/>Para saber mais sobre como essas configurações (mesclagem e substituição) funcionam, consulte os tópicos [Inserir ou Mesclar Entidade](https://msdn.microsoft.com/library/azure/hh452241.aspx) e [Inserir ou Substituir Entidade](https://msdn.microsoft.com/library/azure/hh452242.aspx). <br/><br> Essa configuração se aplica ao nível de linha e não ao nível de tabela e nenhuma das opções excluirá as linhas na tabela de saída que não existirem na entrada. |mesclar (padrão)<br/>substituir |Não |
| writeBatchSize |Insere dados na tabela do Azure quando o writeBatchSize ou writeBatchTimeout for atingido. |Inteiro (número de linhas) |Não (padrão: 10000) |
| writeBatchTimeout |Insere dados na tabela do Azure quando o writeBatchSize ou writeBatchTimeout for atingido |TimeSpan<br/><br/>Exemplo: "00:20:00" (20 minutos) |Não (padrão para 90 seg. de valor de tempo padrão de cliente de armazenamento) |

#### <a name="example"></a>Exemplo

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoTable",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureTableOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "AzureTableSink",
                    "writeBatchSize": 100,
                    "writeBatchTimeout": "01:00:00"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
Para obter mais informações sobre esses serviços vinculados, consulte o artigo [Conector de Armazenamento de Tabelas do Azure](data-factory-azure-table-connector.md#copy-activity-properties).

## <a name="amazon-redshift"></a>Amazon RedShift

### <a name="linked-service"></a>Serviço vinculado
Para definir um serviço vinculado do Amazon Redshift, defina o **type** do serviço vinculado para **AmazonRedshift** e especifique as propriedades a seguir na seção **typeProperties**:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| server |Endereço IP ou nome do host do servidor Amazon Redshift. |Sim |
| port |O número da porta TCP usada pelo servidor Amazon Redshift para ouvir conexões de cliente. |Não, valor padrão: 5439 |
| database |Nome do banco de dados do Amazon Redshift. |Sim |
| Nome de Usuário |Nome de usuário que tem acesso ao banco de dados. |Sim |
| password |Senha para a conta de usuário. |Sim |

#### <a name="example"></a>Exemplo

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties": {
        "type": "AmazonRedshift",
        "typeProperties": {
            "server": "<Amazon Redshift host name or IP address>",
            "port": 5439,
            "database": "<database name>",
            "username": "user",
            "password": "password"
        }
    }
}
```

Para obter mais informações, confira o artigo Conector do Amazon Redshift.

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados do Amazon Redshift, defina o **type** do conjunto de dados para **RelationalTable** e especifique as propriedades a seguir na seção **typeProperties**:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| tableName |Nome da tabela na instância do banco de dados do Amazon Redshift à qual o serviço vinculado se refere. |Não (se **query** de **RelationalSource** for especificado) |


#### <a name="example"></a>Exemplo

```json
{
    "name": "AmazonRedshiftInputDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "AmazonRedshiftLinkedService",
        "typeProperties": {
            "tableName": "<Table name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
Para obter mais informações, confira o artigo Conector do Amazon Redshift.

### <a name="relational-source-in-copy-activity"></a>Origem Relacional na Atividade de Cópia
Se você estiver copiando dados de um Amazon Redshift, defina o **source type** da atividade de cópia para **RelationalSource** e especifique as propriedades a seguir na seção **source**:

| Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| query |Utiliza a consulta personalizada para ler os dados. |Cadeia de caracteres de consulta SQL. Por exemplo: `select * from MyTable`. |Não (se **tableName** de **dataset** for especificado) |

#### <a name="example"></a>Exemplo

```json
{
    "name": "CopyAmazonRedshiftToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "AmazonRedshiftInputDataset"
            }],
            "outputs": [{
                "name": "AzureBlobOutputDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "AmazonRedshiftToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```
Para obter mais informações, confira o artigo Conector do Amazon Redshift.

## <a name="ibm-db2"></a>IBM DB2

### <a name="linked-service"></a>Serviço vinculado
Para definir um serviço vinculado do IBM DB2, defina o **type** do serviço vinculado para **OnPremisesDB2** e especifique as propriedades a seguir na seção **typeProperties**:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| server |Nome do servidor DB2. |Sim |
| database |Nome do banco de dados DB2. |Sim |
| schema |Nome do esquema no banco de dados. O nome do esquema diferencia maiúsculas de minúsculas. |Não |
| authenticationType |Tipo de autenticação usado para se conectar ao banco de dados DB2. Os valores possíveis são: Anônima, Básica e Windows. |Sim |
| Nome de Usuário |Especifique o nome de usuário se você estiver usando a autenticação Basic ou Windows. |Não |
| password |Especifique a senha da conta de usuário que você especificou para o nome de usuário. |Não |
| gatewayName |O nome do gateway que o serviço Data Factory deve usar para se conectar ao banco de dados DB2 local. |Sim |

#### <a name="example"></a>Exemplo
```json
{
    "name": "OnPremDb2LinkedService",
    "properties": {
        "type": "OnPremisesDb2",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```
Para obter mais informações, confira o artigo Conector do IBM DB2.

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados do DB2, defina o **type** do conjunto de dados para **RelationalTable** e especifique as propriedades a seguir na seção **typeProperties**:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| tableName |Nome da tabela na instância do Banco de Dados DB2 à qual o serviço vinculado se refere. O tableName diferencia maiúsculas de minúsculas. |Não (se **query** de **RelationalSource** for especificado)

#### <a name="example"></a>Exemplo
```json
{
    "name": "Db2DataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremDb2LinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Para obter mais informações, confira o artigo Conector do IBM DB2.

### <a name="relational-source-in-copy-activity"></a>Origem Relacional na Atividade de Cópia
Se você estiver copiando dados do IBM DB2, defina o **source type** da atividade de cópia para **RelationalSource** e especifique as propriedades a seguir na seção **source**:


| Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| query |Utiliza a consulta personalizada para ler os dados. |Cadeia de caracteres de consulta SQL. Por exemplo: `"query": "select * from "MySchema"."MyTable""`. |Não (se **tableName** de **dataset** for especificado) |

#### <a name="example"></a>Exemplo
```json
{
    "name": "CopyDb2ToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "select * from \"Orders\""
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "inputs": [{
                "name": "Db2DataSet"
            }],
            "outputs": [{
                "name": "AzureBlobDb2DataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "Db2ToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```
Para obter mais informações, confira o artigo Conector do IBM DB2.

## <a name="mysql"></a>MySQL

### <a name="linked-service"></a>Serviço vinculado
Para definir um serviço vinculado do MySQL, defina o **type** do serviço vinculado para **OnPremisesMySql** e especifique as propriedades a seguir na seção **typeProperties**:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| server |Nome do servidor MySQL. |Sim |
| database |Nome do banco de dados MySQL. |Sim |
| schema |Nome do esquema no banco de dados. |Não |
| authenticationType |Tipo de autenticação usado para se conectar ao banco de dados MySQL. Os valores possíveis são: `Basic`. |Sim |
| userName |Especifique o nome de usuário para se conectar ao banco de dados MySQL. |Sim |
| password |Especifique a senha da conta de usuário que você especificou. |Sim |
| gatewayName |O nome do gateway que o serviço Data Factory deve usar para se conectar ao banco de dados MySQL local. |Sim |

#### <a name="example"></a>Exemplo

```json
{
    "name": "OnPremMySqlLinkedService",
    "properties": {
        "type": "OnPremisesMySql",
        "typeProperties": {
            "server": "<server name>",
            "database": "<database name>",
            "schema": "<schema name>",
            "authenticationType": "<authentication type>",
            "userName": "<user name>",
            "password": "<password>",
            "gatewayName": "<gateway>"
        }
    }
}
```

Para obter mais informações, consulte o artigo [Conector do MySQL](data-factory-onprem-mysql-connector.md#linked-service-properties).

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados do MySQL, defina o **type** do conjunto de dados para **RelationalTable** e especifique as propriedades a seguir na seção **typeProperties**:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| tableName |Nome da tabela na instância do Banco de Dados MySQL à qual o serviço vinculado se refere. |Não (se **query** de **RelationalSource** for especificado) |

#### <a name="example"></a>Exemplo

```json
{
    "name": "MySqlDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremMySqlLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
Para obter mais informações, consulte o artigo [Conector do MySQL](data-factory-onprem-mysql-connector.md#dataset-properties).

### <a name="relational-source-in-copy-activity"></a>Origem Relacional na Atividade de Cópia
Se você estiver copiando dados de um banco de dados do MySQL, defina o **source type** da atividade de cópia para **RelationalSource** e especifique as propriedades a seguir na seção **source**:


| Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| query |Utiliza a consulta personalizada para ler os dados. |Cadeia de caracteres de consulta SQL. Por exemplo: `select * from MyTable`. |Não (se **tableName** de **dataset** for especificado) |


#### <a name="example"></a>Exemplo
```json
{
    "name": "CopyMySqlToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "MySqlDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobMySqlDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "MySqlToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Para obter mais informações, consulte o artigo [Conector do MySQL](data-factory-onprem-mysql-connector.md#copy-activity-properties).

## <a name="oracle"></a>Oracle

### <a name="linked-service"></a>Serviço vinculado
Para definir um serviço vinculado do Oracle, defina o **type** do serviço vinculado para **OnPremisesOracle** e especifique as propriedades a seguir na seção **typeProperties**:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| driverType | Especifique qual driver a ser usado para copiar dados de/para o banco de dados Oracle. Valores permitidos são **Microsoft** ou **ODP** (padrão). Confira a seção Instalação e versão com suporte nos detalhes do driver. | Não |
| connectionString | Especifique as informações necessárias para se conectar à instância do Banco de Dados Oracle para a propriedade connectionString. | Sim |
| gatewayName | Nome do gateway que é usado para conectar o servidor Oracle local |Sim |

#### <a name="example"></a>Exemplo
```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

Para obter mais informações, consulte o artigo [Conector do Oracle](data-factory-onprem-oracle-connector.md#linked-service-properties).

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados do Oracle, defina o **type** do conjunto de dados para **OracleTable** e especifique as propriedades a seguir na seção **typeProperties**:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| tableName |Nome da tabela no Banco de Dados Oracle à qual o serviço vinculado se refere. |Não (se **oracleReaderQuery** de **OracleSource** for especificado) |

#### <a name="example"></a>Exemplo

```json
{
    "name": "OracleInput",
    "properties": {
        "type": "OracleTable",
        "linkedServiceName": "OnPremisesOracleLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "offset": "01:00:00",
            "interval": "1",
            "anchorDateTime": "2016-02-27T12:00:00",
            "frequency": "Hour"
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
Para obter mais informações, consulte o artigo [Conector do Oracle](data-factory-onprem-oracle-connector.md#dataset-properties).

### <a name="oracle-source-in-copy-activity"></a>Origem do Oracle na Atividade de Cópia
Se você estiver copiando dados de um Banco de Dados SQL do Azure, defina o **source type** da atividade de cópia para **OracleSource** e especifique as propriedades a seguir na seção **source**:

| Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| oracleReaderQuery |Utiliza a consulta personalizada para ler os dados. |Cadeia de caracteres de consulta SQL. Por exemplo: `select * from MyTable` <br/><br/>Se não for especificada, a instrução SQL executada será: `select * from MyTable` |Não (se **tableName** de **dataset** for especificado) |

#### <a name="example"></a>Exemplo

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "OracletoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": " OracleInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "OracleSource",
                    "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
            "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Para obter mais informações, consulte o artigo [Conector do Oracle](data-factory-onprem-oracle-connector.md#copy-activity-properties).

### <a name="oracle-sink-in-copy-activity"></a>Coletor do Oracle na Atividade de Cópia
Se você estiver copiando dados para um banco de dados do Oracle, defina o **source type** da atividade de cópia para **OracleSink** e especifique as propriedades a seguir na seção **sink**:

| Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| writeBatchTimeout |Tempo de espera para a operação de inserção em lotes ser concluída antes de atingir o tempo limite. |TimeSpan<br/><br/> Exemplo: 00:30:00 (30 minutos). |Não |
| writeBatchSize |Insere dados na tabela SQL quando o tamanho do buffer atinge writeBatchSize. |Inteiro (número de linhas) |Não (padrão: 100) |
| sqlWriterCleanupScript |Especifique uma consulta da Atividade de Cópia a executar para que os dados de uma fatia específica sejam removidos. |Uma instrução de consulta. |Não |
| sliceIdentifierColumnName |Especifique o nome de coluna para a Atividade de Cópia a ser preenchido com o identificador de fatia gerado automaticamente, que é usado para limpar dados de uma fatia específica quando executado novamente. |Nome de uma coluna com tipo de dados de binário (32). |Não |

#### <a name="example"></a>Exemplo
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-05T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoOracle",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "OracleOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "OracleSink"
                }
            },
            "scheduler": {
                "frequency": "Day",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
Para obter mais informações, consulte o artigo [Conector do Oracle](data-factory-onprem-oracle-connector.md#copy-activity-properties).

## <a name="postgresql"></a>PostgreSQL

### <a name="linked-service"></a>Serviço vinculado
Para definir um serviço vinculado do PostgreSQL, defina o **type** do serviço vinculado para **OnPremisesPostgreSql** e especifique as propriedades a seguir na seção **typeProperties**:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| server |Nome do servidor PostgreSQL. |Sim |
| database |Nome do banco de dados PostgreSQL. |Sim |
| schema |Nome do esquema no banco de dados. O nome do esquema diferencia maiúsculas de minúsculas. |Não |
| authenticationType |Tipo de autenticação usado para se conectar ao banco de dados PostgreSQL. Os valores possíveis são: Anônima, Básica e Windows. |Sim |
| Nome de Usuário |Especifique o nome de usuário se você estiver usando a autenticação Basic ou Windows. |Não |
| password |Especifique a senha da conta de usuário que você especificou para o nome de usuário. |Não |
| gatewayName |O nome do gateway que o serviço Data Factory deve usar para se conectar ao banco de dados PostgreSQL local. |Sim |

#### <a name="example"></a>Exemplo

```json
{
    "name": "OnPremPostgreSqlLinkedService",
    "properties": {
        "type": "OnPremisesPostgreSql",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```
Para obter mais informações, consulte o artigo [Conector do PostgreSQL](data-factory-onprem-postgresql-connector.md#linked-service-properties).

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados do PostgreSQL, defina o **type** do conjunto de dados para **RelationalTable** e especifique as propriedades a seguir na seção **typeProperties**:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| tableName |Nome da tabela na instância do banco de dados PostgreSQL à qual o serviço vinculado se refere. O tableName diferencia maiúsculas de minúsculas. |Não (se **query** de **RelationalSource** for especificado) |

#### <a name="example"></a>Exemplo
```json
{
    "name": "PostgreSqlDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremPostgreSqlLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
Para obter mais informações, consulte o artigo [Conector do PostgreSQL](data-factory-onprem-postgresql-connector.md#dataset-properties).

### <a name="relational-source-in-copy-activity"></a>Origem Relacional na Atividade de Cópia
Se você estiver copiando dados de um banco de dados do PostgreSQL, defina o **source type** da atividade de cópia para **RelationalSource** e especifique as propriedades a seguir na seção **source**:


| Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| query |Utiliza a consulta personalizada para ler os dados. |Cadeia de caracteres de consulta SQL. Por exemplo: "query": "select * from \"MySchema\".\"MyTable\"". |Não (se **tableName** de **dataset** for especificado) |

#### <a name="example"></a>Exemplo

```json
{
    "name": "CopyPostgreSqlToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "select * from \"public\".\"usstates\""
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "inputs": [{
                "name": "PostgreSqlDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobPostgreSqlDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "PostgreSqlToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Para obter mais informações, consulte o artigo [Conector do PostgreSQL](data-factory-onprem-postgresql-connector.md#copy-activity-properties).

## <a name="sap-business-warehouse"></a>SAP Business Warehouse


### <a name="linked-service"></a>Serviço vinculado
Para definir um serviço vinculado do SAP Business Warehouse (BW), defina o **type** do serviço vinculado para **SapBw** e especifique as propriedades a seguir na seção **typeProperties**:

Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório
-------- | ----------- | -------------- | --------
server | Nome do servidor no qual reside a instância do SAP BW. | cadeia de caracteres | Sim
systemNumber | Número de sistema do sistema SAP BW. | Número decimal de dois dígitos representado como uma cadeia de caracteres. | Sim
clientId | ID de Cliente do cliente no sistema SAP W. | Número decimal de três dígitos representado como uma cadeia de caracteres. | Sim
Nome de Usuário | Nome do usuário que tem acesso ao servidor SAP | cadeia de caracteres | Sim
password | Senha do usuário. | cadeia de caracteres | Sim
gatewayName | O nome do gateway que o serviço Data Factory deve usar para se conectar à instância local do SAP BW. | cadeia de caracteres | Sim
encryptedCredential | A cadeia de caracteres de credencial criptografada. | cadeia de caracteres | Não

#### <a name="example"></a>Exemplo

```json
{
    "name": "SapBwLinkedService",
    "properties": {
        "type": "SapBw",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

Para obter mais informações, consulte o artigo [Conector do SAP Business Warehouse](data-factory-sap-business-warehouse-connector.md#linked-service-properties).

### <a name="dataset"></a>Conjunto de dados
Para definir o conjunto de dados do SAP BW defina o **type** do conjunto de dados como **RelationalTable**. Não há propriedades específicas ao tipo com suporte para o conjunto de dados do SAP BW do tipo **RelationalTable**.

#### <a name="example"></a>Exemplo

```json
{
    "name": "SapBwDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapBwLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
Para obter mais informações, consulte o artigo [Conector do SAP Business Warehouse](data-factory-sap-business-warehouse-connector.md#dataset-properties).

### <a name="relational-source-in-copy-activity"></a>Origem Relacional na Atividade de Cópia
Se você estiver copiando dados de um banco de dados do SAP Business Warehouse, defina o **source type** da atividade de cópia para **RelationalSource** e especifique as propriedades a seguir na seção **source**:


| Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| query | Especifica a consulta MDX para ler dados da instância do SAP BW. | Consulta MDX. | Sim |

#### <a name="example"></a>Exemplo

```json
{
    "name": "CopySapBwToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "<MDX query for SAP BW>"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "SapBwDataset"
            }],
            "outputs": [{
                "name": "AzureBlobDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "SapBwToBlob"
        }],
        "start": "2017-03-01T18:00:00",
        "end": "2017-03-01T19:00:00"
    }
}
```

Para obter mais informações, consulte o artigo [Conector do SAP Business Warehouse](data-factory-sap-business-warehouse-connector.md#copy-activity-properties).

## <a name="sap-hana"></a>SAP HANA

### <a name="linked-service"></a>Serviço vinculado
Para definir um serviço vinculado do SAP HANA, defina o **type** do serviço vinculado para **SapHana** e especifique as propriedades a seguir na seção **typeProperties**:

Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório
-------- | ----------- | -------------- | --------
server | Nome do servidor no qual reside a instância do SAP HANA. Se o servidor estiver usando uma porta personalizada, especifique `server:port`. | cadeia de caracteres | Sim
authenticationType | Tipo de autenticação. | cadeia de caracteres. "Básico" ou "Windows" | Sim
Nome de Usuário | Nome do usuário que tem acesso ao servidor SAP | cadeia de caracteres | Sim
password | Senha do usuário. | cadeia de caracteres | Sim
gatewayName | O nome do gateway que o serviço Data Factory deve usar para se conectar à instância local do SAP HANA. | cadeia de caracteres | Sim
encryptedCredential | A cadeia de caracteres de credencial criptografada. | cadeia de caracteres | Não

#### <a name="example"></a>Exemplo

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "server": "<server name>",
            "authenticationType": "<Basic, or Windows>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}

```
Para obter mais informações, consulte o artigo [Conector do SAP HANA](data-factory-sap-hana-connector.md#linked-service-properties).

### <a name="dataset"></a>Conjunto de dados
Para definir o conjunto de dados do SAP HANA defina o **type** do conjunto de dados como **RelationalTable**. Não há propriedades específicas ao tipo com suporte para o conjunto de dados do SAP HANA do tipo **RelationalTable**.

#### <a name="example"></a>Exemplo

```json
{
    "name": "SapHanaDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapHanaLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
Para obter mais informações, consulte o artigo [Conector do SAP HANA](data-factory-sap-hana-connector.md#dataset-properties).

### <a name="relational-source-in-copy-activity"></a>Origem Relacional na Atividade de Cópia
Se você estiver copiando dados de um armazenamento de dados do SAP HANA, defina o **source type** da atividade de cópia para **RelationalSource** e especifique as propriedades a seguir na seção **source**:

| Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| query | Especifica a consulta SQL para ler dados da instância do SAP HANA. | Consulta SQL. | Sim |


#### <a name="example"></a>Exemplo


```json
{
    "name": "CopySapHanaToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "<SQL Query for HANA>"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "SapHanaDataset"
            }],
            "outputs": [{
                "name": "AzureBlobDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "SapHanaToBlob"
        }],
        "start": "2017-03-01T18:00:00",
        "end": "2017-03-01T19:00:00"
    }
}
```

Para obter mais informações, consulte o artigo [Conector do SAP HANA](data-factory-sap-hana-connector.md#copy-activity-properties).


## <a name="sql-server"></a>SQL Server

### <a name="linked-service"></a>Serviço vinculado
Você cria um serviço vinculado do tipo **OnPremisesSqlServer** para vincular um banco de dados do SQL Server local a um data factory. A tabela a seguir fornece a descrição para elementos JSON específicos para o serviço vinculado do SQL Server local.

A tabela a seguir fornece a descrição para elementos JSON específicos para o serviço vinculado do SQL Server.

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| type |A propriedade de tipo deve ser configurada como: **OnPremisesSqlServer**. |Sim |
| connectionString |Especifique as informações de connectionString necessárias para conexão com o banco de dados do SQL Server local usando a autenticação do SQL ou então a autenticação do Windows. |Sim |
| gatewayName |O nome do gateway que o serviço Data Factory deve usar para se conectar ao banco de dados do SQL Server local. |Sim |
| Nome de Usuário |Especifique o nome de usuário se você estiver usando a Autenticação do Windows. Exemplo: **domainname\\username**. |Não |
| password |Especifique a senha da conta de usuário que você especificou para o nome de usuário. |Não |

Você pode criptografar as credenciais usando o **New-AzDataFactoryEncryptValue** cmdlet e usá-los na cadeia de conexão, conforme mostrado no exemplo a seguir (**EncryptedCredential** propriedade):

```json
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```


#### <a name="example-json-for-using-sql-authentication"></a>Exemplo: JSON para usar Autenticação SQL

```json
{
    "name": "MyOnPremisesSQLDB",
    "properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```
#### <a name="example-json-for-using-windows-authentication"></a>Exemplo: JSON para usar Autenticação Windows

Se o nome de usuário e a senha forem especificados, o gateway os usará para representar a conta de usuário especificada para se conectar ao banco de dados SQL Server local. Caso contrário, o gateway se conectará ao SQL Server diretamente com o contexto de segurança do Gateway (a sua conta de inicialização).

```json
{
    "Name": " MyOnPremisesSQLDB",
    "Properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "ConnectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=True;",
            "username": "<domain\\username>",
            "password": "<password>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

Para obter mais informações, consulte o artigo [Conector de SQL Server](data-factory-sqlserver-connector.md#linked-service-properties).

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados do SQL Server, defina o **type** do conjunto de dados para **SqlServerTable** e especifique as propriedades a seguir na seção **typeProperties**:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| tableName |Nome da tabela ou exibição na instância do Banco de Dados SQL Server à qual o serviço vinculado se refere. |Sim |

#### <a name="example"></a>Exemplo
```json
{
    "name": "SqlServerInput",
    "properties": {
        "type": "SqlServerTable",
        "linkedServiceName": "SqlServerLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Para obter mais informações, consulte o artigo [Conector de SQL Server](data-factory-sqlserver-connector.md#dataset-properties).

### <a name="sql-source-in-copy-activity"></a>Origem do SQL na Atividade de Cópia
Se você estiver copiando dados de um banco de dados SQL do Azure, defina o **source type** da atividade de cópia para **SqlSource** e especifique as propriedades a seguir na seção **source**:


| Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| sqlReaderQuery |Utiliza a consulta personalizada para ler os dados. |Cadeia de caracteres de consulta SQL. Por exemplo: `select * from MyTable`. Pode fazer referência a várias tabelas do banco de dados referenciado pelo conjunto de dados de entrada. Se não for especificada, a instrução SQL que é executada é: select from MyTable. |Não |
| sqlReaderStoredProcedureName |Nome do procedimento armazenado que lê os dados da tabela de origem. |Nome do procedimento armazenado. |Não |
| storedProcedureParameters |Parâmetros para o procedimento armazenado. |Pares de nome/valor. Nomes e uso de maiúsculas e minúsculas de parâmetros devem corresponder aos nomes e o uso de maiúsculas e minúsculas dos parâmetros do procedimento armazenado. |Não |

Se **sqlReaderQuery** for especificado para SqlSource, a Atividade de Cópia executará essa consulta na fonte do Banco do SQL Server para obter os dados.

Como alternativa, você pode especificar um procedimento armazenado especificando o **sqlReaderStoredProcedureName** e o **storedProcedureParameters** (se o procedimento armazenado usa parâmetros).

Se você não especificar sqlReaderQuery nem sqlReaderStoredProcedureName, as colunas definidas na seção de estrutura serão usadas para criar uma consulta seleção a ser executada no Banco de Dados SQL Server. Se a definição de conjunto de dados não tem a estrutura, todas as colunas serão selecionadas da tabela.

> [!NOTE]
> Quando você usa **sqlReaderStoredProcedureName**, ainda é necessário especificar um valor para a propriedade **tableName** no JSON do conjunto de dados. Contudo, não há nenhuma validação executada nessa tabela.


#### <a name="example"></a>Exemplo
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "SqlServertoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": " SqlServerInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Neste exemplo, **sqlReaderQuery** é especificada para SqlSource. A Atividade de Cópia executa essa consulta na fonte do Banco de Dados do SQL Server para obter os dados. Como alternativa, você pode especificar um procedimento armazenado especificando o **sqlReaderStoredProcedureName** e o **storedProcedureParameters** (se o procedimento armazenado usa parâmetros). A sqlReaderQuery pode fazer referência a várias tabelas no banco de dados referenciado pelo conjunto de dados de entrada. A propriedade não se limita apenas à tabela definida como typeProperty de tableName do conjunto de dados.

Se você não especificar sqlReaderQuery nem sqlReaderStoredProcedureName, as colunas definidas na seção de estrutura serão usadas para criar uma consulta seleção a ser executada no Banco de Dados SQL Server. Se a definição de conjunto de dados não tem a estrutura, todas as colunas serão selecionadas da tabela.

Para obter mais informações, consulte o artigo [Conector de SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties).

### <a name="sql-sink-in-copy-activity"></a>Coletor do Sql na Atividade de Cópia
Se você estiver copiando dados para um banco de dados SQL Server, defina o **sink type** da atividade de cópia para **SqlSink** e especifique as propriedades a seguir na seção **sink**:

| Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| writeBatchTimeout |Tempo de espera para a operação de inserção em lotes ser concluída antes de atingir o tempo limite. |TimeSpan<br/><br/> Exemplo: “00:30:00” (30 minutos). |Não |
| writeBatchSize |Insere dados na tabela SQL quando o tamanho do buffer atinge writeBatchSize. |Inteiro (número de linhas) |Não (padrão: 10000) |
| sqlWriterCleanupScript |Especifique a consulta para a Atividade de Cópia a ser executada para que os dados de uma fatia especifica sejam removidos. Para saber mais, confira a seção de repetição. |Uma instrução de consulta. |Não |
| sliceIdentifierColumnName |Especifique o nome de coluna para a Atividade de Cópia a ser preenchido com o identificador de fatia gerado automaticamente, que é usado para limpar dados de uma fatia específica quando executado novamente. Para saber mais, confira a seção de repetição. |Nome de uma coluna com tipo de dados de binário (32). |Não |
| sqlWriterStoredProcedureName |Nome do procedimento armazenado que upserts (atualiza/insere) na tabela de destino. |Nome do procedimento armazenado. |Não |
| storedProcedureParameters |Parâmetros para o procedimento armazenado. |Pares de nome/valor. Nomes e uso de maiúsculas e minúsculas de parâmetros devem corresponder aos nomes e o uso de maiúsculas e minúsculas dos parâmetros do procedimento armazenado. |Não |
| sqlWriterTableType |Especifique o nome do tipo de tabela a ser usado no procedimento armazenado. A atividade de cópia disponibiliza aqueles dados sendo movidos em uma tabela temporária com esse tipo de tabela. O código de procedimento armazenado pode mesclar os dados sendo copiados com dados existentes. |Um nome de tipo de tabela. |Não |

#### <a name="example"></a>Exemplo
O pipeline contém uma Atividade de Cópia que está configurada para usar os conjuntos de dados de entrada e saída e agendada para ser executada a cada hora. Na definição de JSON do pipeline, o tipo de **fonte** está definido como **BlobSource** e o tipo de **coletor** está definido como **SqlSink**.

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": " SqlServerOutput "
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource",
                    "blobColumnSeparators": ","
                },
                "sink": {
                    "type": "SqlSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Para obter mais informações, consulte o artigo [Conector de SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties).

## <a name="sybase"></a>Sybase

### <a name="linked-service"></a>Serviço vinculado
Para definir um serviço vinculado do Sybase, defina o **type** do serviço vinculado para **OnPremisesSybase** e especifique as propriedades a seguir na seção **typeProperties**:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| server |Nome do servidor do Sybase. |Sim |
| database |Nome do banco de dados do Sybase. |Sim |
| schema |Nome do esquema no banco de dados. |Não |
| authenticationType |Tipo de autenticação usado para se conectar ao banco de dados Sybase. Os valores possíveis são: Anônima, Básica e Windows. |Sim |
| Nome de Usuário |Especifique o nome de usuário se você estiver usando a autenticação Basic ou Windows. |Não |
| password |Especifique a senha da conta de usuário que você especificou para o nome de usuário. |Não |
| gatewayName |O nome do gateway que o serviço Data Factory deve usar para se conectar ao banco de dados local do Sybase. |Sim |

#### <a name="example"></a>Exemplo
```json
{
    "name": "OnPremSybaseLinkedService",
    "properties": {
        "type": "OnPremisesSybase",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```

Para obter mais informações, consulte o artigo [Conector do Sybase](data-factory-onprem-sybase-connector.md#linked-service-properties).

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados do Sybase, defina o **type** do conjunto de dados para **RelationalTable** e especifique as propriedades a seguir na seção **typeProperties**:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| tableName |Nome da tabela na instância do banco de dados Sybase à qual o serviço vinculado se refere. |Não (se **query** de **RelationalSource** for especificado) |

#### <a name="example"></a>Exemplo

```json
{
    "name": "SybaseDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremSybaseLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Para obter mais informações, consulte o artigo [Conector do Sybase](data-factory-onprem-sybase-connector.md#dataset-properties).

### <a name="relational-source-in-copy-activity"></a>Origem Relacional na Atividade de Cópia
Se você estiver copiando dados de um banco de dados do Sybase, defina o **source type** da atividade de cópia para **RelationalSource** e especifique as propriedades a seguir na seção **source**:


| Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| query |Utiliza a consulta personalizada para ler os dados. |Cadeia de caracteres de consulta SQL. Por exemplo: `select * from MyTable`. |Não (se **tableName** de **dataset** for especificado) |

#### <a name="example"></a>Exemplo

```json
{
    "name": "CopySybaseToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "select * from DBA.Orders"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "inputs": [{
                "name": "SybaseDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobSybaseDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "SybaseToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Para obter mais informações, consulte o artigo [Conector do Sybase](data-factory-onprem-sybase-connector.md#copy-activity-properties).

## <a name="teradata"></a>Teradata

### <a name="linked-service"></a>Serviço vinculado
Para definir um serviço vinculado do Teradata, defina o **type** do serviço vinculado para **OnPremisesTeradata** e especifique as propriedades a seguir na seção **typeProperties**:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| server |Nome do servidor Teradata. |Sim |
| authenticationType |Tipo de autenticação usado para se conectar ao banco de dados Teradata. Os valores possíveis são: Anônima, Básica e Windows. |Sim |
| Nome de Usuário |Especifique o nome de usuário se você estiver usando a autenticação Basic ou Windows. |Não |
| password |Especifique a senha da conta de usuário que você especificou para o nome de usuário. |Não |
| gatewayName |O nome do gateway que o serviço Data Factory deve usar para se conectar ao banco de dados Teradata local. |Sim |

#### <a name="example"></a>Exemplo
```json
{
    "name": "OnPremTeradataLinkedService",
    "properties": {
        "type": "OnPremisesTeradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```

Para obter mais informações, consulte o artigo [Conector do Teradata](data-factory-onprem-teradata-connector.md#linked-service-properties).

### <a name="dataset"></a>Conjunto de dados
Para definir o conjunto de dados do Blobo do Teradata defina o **type** do conjunto de dados como **RelationalTable**. Atualmente, não há nenhuma propriedade do tipo com suporte para o conjunto de dados Teradata.

#### <a name="example"></a>Exemplo
```json
{
    "name": "TeradataDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremTeradataLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Para obter mais informações, consulte o artigo [Conector do Teradata](data-factory-onprem-teradata-connector.md#dataset-properties).

### <a name="relational-source-in-copy-activity"></a>Origem Relacional na Atividade de Cópia
Se você estiver copiando dados de um banco de dados do Teradata, defina o **source type** da atividade de cópia para **RelationalSource** e especifique as propriedades a seguir na seção **source**:

| Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| query |Utiliza a consulta personalizada para ler os dados. |Cadeia de caracteres de consulta SQL. Por exemplo: `select * from MyTable`. |Sim |

#### <a name="example"></a>Exemplo

```json
{
    "name": "CopyTeradataToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', SliceStart, SliceEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "TeradataDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobTeradataDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "TeradataToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "isPaused": false
    }
}
```

Para obter mais informações, consulte o artigo [Conector do Teradata](data-factory-onprem-teradata-connector.md#copy-activity-properties).

## <a name="cassandra"></a>Cassandra


### <a name="linked-service"></a>Serviço vinculado
Para definir um serviço vinculado do Cassandra, defina o **type** do serviço vinculado para **OnPremisesCassandra** e especifique as propriedades a seguir na seção **typeProperties**:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| host |Um ou mais endereços IP ou nomes de host dos servidores Cassandra.<br/><br/>Especifique uma lista separada por vírgulas de endereços IP ou nomes de host para se conectar simultaneamente a todos os servidores. |Sim |
| port |A porta TCP usada pelo servidor Cassandra para ouvir conexões de cliente. |Não, valor padrão: 9042 |
| authenticationType |Básica, ou Anônima |Sim |
| username |Especifique o nome de usuário da conta de usuário. |Sim, se authenticationType for definida como Básica. |
| password |Especifique a senha para a conta de usuário. |Sim, se authenticationType for definida como Básica. |
| gatewayName |O nome do gateway que é usado para se conectar ao servidor Cassandra local. |Sim |
| encryptedCredential |Credencial criptografada pelo gateway. |Não |

#### <a name="example"></a>Exemplo

```json
{
    "name": "CassandraLinkedService",
    "properties": {
        "type": "OnPremisesCassandra",
        "typeProperties": {
            "authenticationType": "Basic",
            "host": "<cassandra server name or IP address>",
            "port": 9042,
            "username": "user",
            "password": "password",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Para obter mais informações, consulte o artigo [Conector do Cassandra](data-factory-onprem-cassandra-connector.md#linked-service-properties).

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados do Cassandra, defina o **type** do conjunto de dados para **CassandraTable** e especifique as propriedades a seguir na seção **typeProperties**:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| keyspace |Nome do keyspace ou do esquema no banco de dados Cassandra. |Sim (se a **consulta** para **CassandraSource** não estiver definida). |
| tableName |Nome da tabela no banco de dados Cassandra. |Sim (se a **consulta** para **CassandraSource** não estiver definida). |

#### <a name="example"></a>Exemplo

```json
{
    "name": "CassandraInput",
    "properties": {
        "linkedServiceName": "CassandraLinkedService",
        "type": "CassandraTable",
        "typeProperties": {
            "tableName": "mytable",
            "keySpace": "<key space>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Para obter mais informações, consulte o artigo [Conector do Cassandra](data-factory-onprem-cassandra-connector.md#dataset-properties).

### <a name="cassandra-source-in-copy-activity"></a>Origem do Cassandra na Atividade de Cópia
Se você estiver copiando dados do Cassandra, defina o **source type** da atividade de cópia para **CassandraSource** e especifique as propriedades a seguir na seção **source**:

| Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| query |Utiliza a consulta personalizada para ler os dados. |Consulta SQL-92 ou consulta CQL. Veja [Referência ao CQL](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>Ao usar a consulta SQL, especifique **keyspace name.table name** para representar a tabela que deseja consultar. |Não (se tableName e keyspace no conjunto de dados estiverem definidos). |
| consistencyLevel |O nível de consistência especifica quantas réplicas devem responder a uma solicitação de leitura antes de retornar dados ao aplicativo cliente. O Cassandra verifica o número especificado de réplicas de dados atender à solicitação de leitura. |ONE, TWO, THREE, QUORUM, ALL, LOCAL_QUORUM, EACH_QUORUM, LOCAL_ONE. Confira [Configuring data consistency (Configurando a consistência de dados)](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) para obter detalhes. |Nº O valor padrão é ONE. |

#### <a name="example"></a>Exemplo

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "CassandraToAzureBlob",
            "description": "Copy from Cassandra to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "CassandraInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "CassandraSource",
                    "query": "select id, firstname, lastname from mykeyspace.mytable"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Para obter mais informações, consulte o artigo [Conector do Cassandra](data-factory-onprem-cassandra-connector.md#copy-activity-properties).

## <a name="mongodb"></a>MongoDB

### <a name="linked-service"></a>Serviço vinculado
Para definir um serviço vinculado do MongoDB, defina o **type** do serviço vinculado para **OnPremisesMongoDB** e especifique as propriedades a seguir na seção **typeProperties**:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| server |Endereço IP ou nome do host do servidor MongoDB. |Sim |
| port |A porta TCP usada pelo servidor MongoDB para ouvir conexões de cliente. |Opcional, valor padrão: 27017 |
| authenticationType |Básica ou Anônima. |Sim |
| username |Conta de usuário para acessar o MongoDB. |Sim (se a autenticação básica for usada). |
| password |Senha do usuário. |Sim (se a autenticação básica for usada). |
| authSource |Nome do banco de dados MongoDB que você deseja usar para verificar suas credenciais para autenticação. |Opcional (se a autenticação básica for usada). Padrão: usa a conta de administrador e o banco de dados especificado usando a propriedade databaseName. |
| databaseName |Nome do banco de dados MongoDB que você deseja acessar. |Sim |
| gatewayName |Nome do gateway que acessa o armazenamento de dados. |Sim |
| encryptedCredential |Credencial criptografada pelo gateway. |Opcional |

#### <a name="example"></a>Exemplo

```json
{
    "name": "OnPremisesMongoDbLinkedService",
    "properties": {
        "type": "OnPremisesMongoDb",
        "typeProperties": {
            "authenticationType": "<Basic or Anonymous>",
            "server": "< The IP address or host name of the MongoDB server >",
            "port": "<The number of the TCP port that the MongoDB server uses to listen for client connections.>",
            "username": "<username>",
            "password": "<password>",
            "authSource": "< The database that you want to use to check your credentials for authentication. >",
            "databaseName": "<database name>",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Para obter mais informações, consulte o artigo [Conector do MongoDB](data-factory-on-premises-mongodb-connector.md#linked-service-properties).

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados do MongoDB, defina o **type** do conjunto de dados para **MongoDbCollection** e especifique as propriedades a seguir na seção **typeProperties**:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| collectionName |Nome da coleção no banco de dados MongoDB. |Sim |

#### <a name="example"></a>Exemplo

```json
{
    "name": "MongoDbInputDataset",
    "properties": {
        "type": "MongoDbCollection",
        "linkedServiceName": "OnPremisesMongoDbLinkedService",
        "typeProperties": {
            "collectionName": "<Collection name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

Para obter mais informações, consulte o artigo [Conector do MongoDB](data-factory-on-premises-mongodb-connector.md#dataset-properties).

#### <a name="mongodb-source-in-copy-activity"></a>Origem do MongoDB na Atividade de Cópia
Se você estiver copiando dados do MongoDB, defina o **source type** da atividade de cópia para **MongoDbSource** e especifique as propriedades a seguir na seção **source**:

| Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| query |Utiliza a consulta personalizada para ler os dados. |Cadeia de consulta SQL-92. Por exemplo: `select * from MyTable`. |Não (se **collectionName** de **dataset** for especificado) |

#### <a name="example"></a>Exemplo

```json
{
    "name": "CopyMongoDBToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "MongoDbSource",
                    "query": "select * from MyTable"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "MongoDbInputDataset"
            }],
            "outputs": [{
                "name": "AzureBlobOutputDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "MongoDBToAzureBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Para obter mais informações, consulte o artigo [Conector do MongoDB](data-factory-on-premises-mongodb-connector.md#copy-activity-properties).

## <a name="amazon-s3"></a>Amazon S3


### <a name="linked-service"></a>Serviço vinculado
Para definir um serviço vinculado do Amazon S3, defina o **type** do serviço vinculado para **AwsAccessKey** e especifique as propriedades a seguir na seção **typeProperties**:

| Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| accessKeyID |ID da chave de acesso secreta. |cadeia de caracteres |Sim |
| secretAccessKey |A chave de acesso do secreta em si. |Cadeia de caracteres secreta criptografada |Sim |

#### <a name="example"></a>Exemplo
```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AwsAccessKey",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": "<secret access key>"
        }
    }
}
```

Para obter mais informações, consulte o artigo [Conector do Amazon S3](data-factory-amazon-simple-storage-service-connector.md#linked-service-properties).

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados do Amazon S3, defina o **type** do conjunto de dados para **AmazonS3** e especifique as propriedades a seguir na seção **typeProperties**:

| Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| bucketName |O nome do bucket S3. |string |Sim |
| key |A chave do objeto S3. |string |Não |
| prefix |Prefixo da chave do objeto S3. Objetos cujas chaves começam com esse prefixo serão selecionados. Aplica-se apenas quando a chave está vazia. |string |Não |
| version |A versão do objeto S3 se o controle de versão do S3 está habilitado. |string |Não |
| format | Há suporte para os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Defina a propriedade **type** sob formato como um desses valores. Para saber mais, veja as seções [Formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [Formato Json](data-factory-supported-file-and-compression-formats.md#json-format), [Formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format), e [Formato Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format). <br><br> Se você quiser **copiar arquivos no estado em que se encontram** entre repositórios baseados em arquivo (cópia binária), ignore a seção de formato nas duas definições de conjunto de dados de entrada e de saída. |Não | |
| compression | Especifique o tipo e o nível de compactação para os dados. Tipos compatíveis são: **GZip**, **Deflate**, **BZip2** e **ZipDeflate**. Os níveis compatíveis são: **Ideal** e **Mais Rápido**. Para saber mais, confira [File and compression formats in Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support) (Formatos de arquivo e de compactação no Azure Data Factory). |Não | |


> [!NOTE]
> bucketName + chave especifica a localização do objeto S3 em que bucket é o contêiner raiz para objetos S3 e a chave é o caminho completo para o objeto S3.

#### <a name="example-sample-dataset-with-prefix"></a>Exemplo: Conjunto de dados de exemplo com o prefixo

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "prefix": "testFolder/test",
            "bucketName": "<S3 bucket name>",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
#### <a name="example-sample-data-set-with-version"></a>Exemplo: Conjunto de dados de exemplo (com a versão)

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "key": "testFolder/test.orc",
            "bucketName": "<S3 bucket name>",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

#### <a name="example-dynamic-paths-for-s3"></a>Exemplo: Caminhos dinâmicos para S3
No exemplo, usamos valores fixos para as propriedades de chave e bucketName no conjunto de dados do Amazon S3.

```json
"key": "testFolder/test.orc",
"bucketName": "<S3 bucket name>",
```

Você pode fazer com que o Data Factory calcule a chave e bucketName dinamicamente em tempo de execução usando variáveis de sistema como SliceStart.

```json
"key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
"bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"
```

Você pode fazer o mesmo para a propriedade de prefixo de um conjunto de dados do Amazon S3. Veja [Funções e variáveis do sistema do Data Factory](data-factory-functions-variables.md) para obter uma lista das funções e variáveis com suporte.

Para obter mais informações, consulte o artigo [Conector do Amazon S3](data-factory-amazon-simple-storage-service-connector.md#dataset-properties).

### <a name="file-system-source-in-copy-activity"></a>Origem do Sistema de Arquivos na Atividade de Cópia
Se você estiver copiando dados do Amazon S3, defina o **source type** da atividade de cópia para **FileSystemSource** e especifique as propriedades a seguir na seção **source**:


| Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| recursive |Especifica se devemos listar recursivamente objetos S3 no diretório. |true/false |Não |


#### <a name="example"></a>Exemplo


```json
{
    "name": "CopyAmazonS3ToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource",
                    "recursive": true
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "AmazonS3InputDataset"
            }],
            "outputs": [{
                "name": "AzureBlobOutputDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "AmazonS3ToBlob"
        }],
        "start": "2016-08-08T18:00:00",
        "end": "2016-08-08T19:00:00"
    }
}
```

Para obter mais informações, consulte o artigo [Conector do Amazon S3](data-factory-amazon-simple-storage-service-connector.md#copy-activity-properties).

## <a name="file-system"></a>Sistema de arquivos


### <a name="linked-service"></a>Serviço vinculado
Você pode vincular um sistema de arquivos local ao Azure Data Factory com o serviço vinculado do **Servidor de Arquivos Local**. A tabela a seguir fornece descrições dos elementos JSON específicos para o serviço vinculado do Servidor de Arquivos Local.

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| type |Verifique se a propriedade de tipo foi definida como **OnPremisesFileServer**. |Sim |
| host |Especifica o caminho raiz da pasta que você deseja copiar. Use o caractere de escape ‘ \ ’ para caracteres especiais na cadeia de caracteres. Confira Definições de conjunto de dados e serviço vinculado de exemplo para obter exemplos. |Sim |
| userid |Especifique a ID do usuário que tem acesso ao servidor. |Não (se você escolher encryptedcredential) |
| password |Especifique a senha para o usuário (userid). |Não (se você escolher encryptedcredential |
| encryptedCredential |Especifique as credenciais criptografadas que você pode obter executando o cmdlet New-AzDataFactoryEncryptValue. |Não (se você optar por especificar userid e password em texto sem formatação) |
| gatewayName |Especifica o nome do gateway que o Data Factory deve usar para se conectar ao servidor de arquivos local. |Sim |

#### <a name="sample-folder-path-definitions"></a>Exemplos de definições de caminho de pasta

| Cenário | Host em definição de serviço vinculado | folderPath em definição de conjunto de dados |
| --- | --- | --- |
| Pasta local no computador do Gateway de Gerenciamento de Dados <br/><br/>Exemplos: D:\\\* ou D:\pasta\subpasta\\* |D:\\\\ (para o Gateway de Gerenciamento de Dados 2.0 e versões posteriores) <br/><br/> localhost (para versões anteriores do Gateway de Gerenciamento de Dados 2.0) |.\\\\ ou pasta\\\\subpasta (para o Gateway de Gerenciamento de Dados 2.0 e versões posteriores) <br/><br/>D:\\\\ ou D:\\\\pasta\\\\subpasta (para a versão de gateway abaixo de 2.0) |
| Pasta compartilhada remota: <br/><br/>Exemplos: \\\\meuservidor\\compartilhar\\\* ou \\\\meuservidor\\compartilhar\\pasta\\subpasta\\* |\\\\\\\\meuservidor\\\\compartilhar |.\\\\ ou pasta\\\\subpasta |


#### <a name="example-using-username-and-password-in-plain-text"></a>Exemplo: Usando username e password em texto sem formatação

```json
{
    "Name": "OnPremisesFileServerLinkedService",
    "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
            "host": "\\\\Contosogame-Asia",
            "userid": "Admin",
            "password": "123456",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-using-encryptedcredential"></a>Exemplo: Usando encryptedcredential

```json
{
    "Name": " OnPremisesFileServerLinkedService ",
    "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
            "host": "D:\\",
            "encryptedCredential": "WFuIGlzIGRpc3Rpbmd1aXNoZWQsIG5vdCBvbmx5IGJ5xxxxxxxxxxxxxxxxx",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Para obter mais informações, consulte o [artigo Conector do Sistema de Arquivos](data-factory-onprem-file-system-connector.md#linked-service-properties).

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados do Sistema de Arquivos, defina o **type** do conjunto de dados para **FileShare** e especifique as propriedades a seguir na seção **typeProperties**:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| folderPath |Especifica o subcaminho para a pasta. Use o caractere de escape ‘\’ para caracteres especiais na cadeia de caracteres. Confira Definições de conjunto de dados e serviço vinculado de exemplo para obter exemplos.<br/><br/>Você pode combinar essa propriedade com **partitionBy** para ter caminhos de pastas com base na fatia de data/hora de início/término. |Sim |
| fileName |Especifique o nome do arquivo no **folderPath** se quiser que a tabela se refira a um arquivo específico na pasta. Se você não especificar algum valor para essa propriedade, a tabela apontará para todos os arquivos na pasta.<br/><br/>Quando o fileName não for especificado para um conjunto de dados de saída, o nome do arquivo gerado será no seguinte formato: <br/><br/>`Data.<Guid>.txt` (Exemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Não |
| fileFilter |Especifique um filtro a ser usado para selecionar um subconjunto de arquivos no folderPath em vez de todos os arquivos. <br/><br/>Os valores permitidos são: `*` (vários caracteres) e `?` (um único caractere).<br/><br/>Exemplo 1: "fileFilter": "*.log"<br/>Exemplo 2: "fileFilter": 2016-1-?.txt"<br/><br/>Observe que fileFilter é aplicável a um conjunto de dados FileShare de entrada. |Não |
| partitionedBy |Você pode usar partitionedBy para especificar um folderPath/fileName dinâmico para dados de série temporal. Um exemplo é folderPath parametrizado para cada hora dos dados. |Não |
| format | Há suporte para os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Defina a propriedade **type** sob formato como um desses valores. Para saber mais, veja as seções [Formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [Formato Json](data-factory-supported-file-and-compression-formats.md#json-format), [Formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format), e [Formato Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format). <br><br> Se você quiser **copiar arquivos no estado em que se encontram** entre repositórios baseados em arquivo (cópia binária), ignore a seção de formato nas duas definições de conjunto de dados de entrada e de saída. |Não |
| compression | Especifique o tipo e o nível de compactação para os dados. Tipos compatíveis são: **GZip**, **Deflate**, **BZip2** e **ZipDeflate**; e os níveis com suporte são: **Ideal** e **Mais Rápido**. confira [Formatos de arquivo e de compactação no Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |

> [!NOTE]
> Você não pode usar fileName e fileFilter simultaneamente.

#### <a name="example"></a>Exemplo

```json
{
    "name": "OnpremisesFileSystemInput",
    "properties": {
        "type": " FileShare",
        "linkedServiceName": " OnPremisesFileServerLinkedService ",
        "typeProperties": {
            "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
            "fileName": "{Hour}.csv",
            "partitionedBy": [{
                "name": "Year",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                        "format": "yyyy"
                }
            }, {
                "name": "Month",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "MM"
                }
            }, {
                "name": "Day",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "dd"
                }
            }, {
                "name": "Hour",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "HH"
                }
            }]
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Para obter mais informações, consulte o [artigo Conector do Sistema de Arquivos](data-factory-onprem-file-system-connector.md#dataset-properties).

### <a name="file-system-source-in-copy-activity"></a>Origem do Sistema de Arquivos na Atividade de Cópia
Se você estiver copiando dados do Sistema de Arquivos, defina o **source type** da atividade de cópia para **FileSystemSource** e especifique as propriedades a seguir na seção **source**:

| Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| recursive |Indica se os dados são lidos recursivamente das subpastas ou somente da pasta especificada. |True, False (padrão) |Não |

#### <a name="example"></a>Exemplo

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2015-06-01T18:00:00",
        "end": "2015-06-01T19:00:00",
        "description": "Pipeline for copy activity",
        "activities": [{
            "name": "OnpremisesFileSystemtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "OnpremisesFileSystemInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
            "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
Para obter mais informações, consulte o [artigo Conector do Sistema de Arquivos](data-factory-onprem-file-system-connector.md#copy-activity-properties).

### <a name="file-system-sink-in-copy-activity"></a>Coletor do Sistema de Arquivos na Atividade de Cópia
Se você estiver copiando dados para um Sistema de Arquivos, defina o **sink type** da atividade de cópia para **FileSystemSink** e especifique as propriedades a seguir na seção **sink**:

| Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| copyBehavior |Define o comportamento de cópia quando a origem é BlobSource ou FileSystem. |**PreserveHierarchy:** Preserva a hierarquia de arquivos na pasta de destino. Ou seja, o caminho relativo do arquivo de origem para a pasta de origem é o mesmo que o caminho relativo do arquivo de destino para a pasta de destino.<br/><br/>**FlattenHierarchy:** Todos os arquivos da pasta de origem são criados no primeiro nível da pasta de destino. Os arquivos de destino são criados com um nome gerado automaticamente.<br/><br/>**MergeFiles:** Mescla todos os arquivos da pasta de origem em um arquivo. Se o nome do arquivo/nome do blob for especificado, o nome do arquivo mesclado será o nome especificado. Caso contrário, ele será um nome de arquivo gerado automaticamente. |Não |

auto-

#### <a name="example"></a>Exemplo

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2015-06-01T18:00:00",
        "end": "2015-06-01T20:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLtoOnPremisesFile",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSQLInput"
            }],
            "outputs": [{
                "name": "OnpremisesFileSystemOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "FileSystemSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 3,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Para obter mais informações, consulte o [artigo Conector do Sistema de Arquivos](data-factory-onprem-file-system-connector.md#copy-activity-properties).

## <a name="ftp"></a>FTP

### <a name="linked-service"></a>Serviço vinculado
Para definir um serviço vinculado do FTP, defina o **type** do serviço vinculado para **FtpServer** e especifique as propriedades a seguir na seção **typeProperties**:

| Propriedade | DESCRIÇÃO | Obrigatório | Padrão |
| --- | --- | --- | --- |
| host |Nome ou endereço IP do servidor FTP |Sim |&nbsp; |
| authenticationType |Especificar tipo de autenticação |Sim |Básica, Anônima |
| Nome de Usuário |Usuário que tem acesso ao servidor FTP |Não |&nbsp; |
| password |Senha do usuário (nome de usuário) |Não |&nbsp; |
| encryptedCredential |Credencial criptografada para acessar o servidor FTP |Não |&nbsp; |
| gatewayName |Nome do Gateway de Gerenciamento de Dados para conectar um servidor FTP local |Não |&nbsp; |
| porta |Porta na qual o servidor FTP está escutando |Não |21 |
| enableSsl |Especifique se deseja usar o canal FTP sobre SSL/TLS |Não |true |
| enableServerCertificateValidation |Especifique se deseja habilitar a validação do certificado SSL do servidor ao usar o canal FTP sobre SSL/TLS |Não |true |

#### <a name="example-using-anonymous-authentication"></a>Exemplo: Usando a autenticação anônima

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
            "typeProperties": {
            "authenticationType": "Anonymous",
            "host": "myftpserver.com"
        }
    }
}
```

#### <a name="example-using-username-and-password-in-plain-text-for-basic-authentication"></a>Exemplo: Usar nome de usuário e senha em texto sem formatação para autenticação básica

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
        }
    }
}
```

#### <a name="example-using-port-enablessl-enableservercertificatevalidation"></a>Exemplo: Usar a porta, enableSsl, enableServerCertificateValidation

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456",
            "port": "21",
            "enableSsl": true,
            "enableServerCertificateValidation": true
        }
    }
}
```

#### <a name="example-using-encryptedcredential-for-authentication-and-gateway"></a>Exemplo: Usar encryptedCredential para autenticação e gateway

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "gatewayName": "<onpremgateway>"
        }
      }
}
```

Para obter mais informações, consulte o artigo [Conector do FTP](data-factory-ftp-connector.md#linked-service-properties).

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados do FTP, defina o **type** do conjunto de dados para **FileShare** e especifique as propriedades a seguir na seção **typeProperties**:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| folderPath |Subcaminho para a pasta. Use o caractere de escape ' \ ' para caracteres especiais na cadeia de caracteres. Para obter exemplos, confira Definições de conjunto de dados e serviço vinculado de exemplo.<br/><br/>Você pode combinar essa propriedade com **partitionBy** para ter caminhos de pastas com base na fatia de data/hora de início/término. |Sim
| fileName |Especifique o nome do arquivo no **folderPath** se quiser que a tabela se refira a um arquivo específico na pasta. Se você não especificar algum valor para essa propriedade, a tabela apontará para todos os arquivos na pasta.<br/><br/>Quando o fileName não for especificado para um conjunto de dados de saída, o nome do arquivo gerado será no seguinte formato: <br/><br/>`Data.<Guid>.txt` (Exemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Não |
| fileFilter |Especifique um filtro a ser usado para selecionar um subconjunto de arquivos no folderPath em vez de todos os arquivos.<br/><br/>Os valores permitidos são: `*` (vários caracteres) e `?` (um único caractere).<br/><br/>Exemplo 1: `"fileFilter": "*.log"`<br/>Exemplo 2: `"fileFilter": 2016-1-?.txt"`<br/><br/> fileFilter é aplicável a um conjunto de dados FileShare de entrada. Essa propriedade não tem suporte com HDFS. |Não |
| partitionedBy |partitionedBy pode usado para especificar um filename, folderPath dinâmico para dados de série temporal. Por exemplo, folderPath parametrizado para cada hora dos dados. |Não |
| format | Há suporte para os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Defina a propriedade **type** sob formato como um desses valores. Para saber mais, veja as seções [Formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [Formato Json](data-factory-supported-file-and-compression-formats.md#json-format), [Formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format), e [Formato Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format). <br><br> Se você quiser **copiar arquivos no estado em que se encontram** entre repositórios baseados em arquivo (cópia binária), ignore a seção de formato nas duas definições de conjunto de dados de entrada e de saída. |Não |
| compression | Especifique o tipo e o nível de compactação para os dados. Tipos compatíveis são: **GZip**, **Deflate**, **BZip2** e **ZipDeflate**; e os níveis com suporte são: **Ideal** e **Mais Rápido**. Para saber mais, confira [File and compression formats in Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support) (Formatos de arquivo e de compactação no Azure Data Factory). |Não |
| useBinaryTransfer |Especifique se deve usar o modo de transferência Binário. True para o modo binário e ASCII false. Valor padrão: True. Essa propriedade só pode ser usada quando o tipo de serviço vinculado associado é do tipo: FtpServer. |Não |

> [!NOTE]
> filename e fileFilter não podem ser usados simultaneamente.

#### <a name="example"></a>Exemplo

```json
{
    "name": "FTPFileInput",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "FTPLinkedService",
        "typeProperties": {
            "folderPath": "<path to shared folder>",
            "fileName": "test.csv",
            "useBinaryTransfer": true
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Para obter mais informações, consulte o artigo [Conector do FTP](data-factory-ftp-connector.md#dataset-properties).

### <a name="file-system-source-in-copy-activity"></a>Origem do Sistema de Arquivos na Atividade de Cópia
Se você estiver copiando dados do FTP, defina o **source type** da atividade de cópia para **FileSystemSource** e especifique as propriedades a seguir na seção **source**:

| Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| recursive |Indica se os dados são lidos recursivamente a partir das subpastas ou somente da pasta especificada. |True, False (padrão) |Não |

#### <a name="example"></a>Exemplo

```json
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "FTPToBlobCopy",
            "inputs": [{
                "name": "FtpFileInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2016-08-24T18:00:00",
        "end": "2016-08-24T19:00:00"
    }
}
```

Para obter mais informações, consulte o artigo [Conector do FTP](data-factory-ftp-connector.md#copy-activity-properties).


## <a name="hdfs"></a>HDFS

### <a name="linked-service"></a>Serviço vinculado
Para definir um serviço vinculado do HDFS, defina o **type** do serviço vinculado para **Hdfs** e especifique as propriedades a seguir na seção **typeProperties**:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| type |A propriedade type deve ser definida como: **Hdfs** |Sim |
| url |URL para o HDFS |Sim |
| authenticationType |Anônimo ou Windows. <br><br> Para usar **autenticação Kerberos** com o conector HDFS, veja esta seção para configurar seu ambiente local adequadamente. |Sim |
| userName |Nome de usuário para a autenticação do Windows. |Sim (para a Autenticação do Windows) |
| password |Senha para a autenticação do Windows. |Sim (para a Autenticação do Windows) |
| gatewayName |O nome do gateway que o serviço Data Factory deve usar para se conectar ao HDFS. |Sim |
| encryptedCredential |[Novo AzDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) saída da credencial de acesso. |Não |

#### <a name="example-using-anonymous-authentication"></a>Exemplo: Usando a autenticação anônima

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "authenticationType": "Anonymous",
            "userName": "hadoop",
            "url": "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-using-windows-authentication"></a>Exemplo: Usando a autenticação do Windows

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "authenticationType": "Windows",
            "userName": "Administrator",
            "password": "password",
            "url": "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Para obter mais informações, confira o artigo Conector do HDFS.

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados do HDFS, defina o **type** do conjunto de dados para **FileShare** e especifique as propriedades a seguir na seção **typeProperties**:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| folderPath |Caminho para a pasta. Exemplo: `myfolder`<br/><br/>Use o caractere de escape ' \ ' para caracteres especiais na cadeia de caracteres. Por exemplo: para pasta\subpasta, especifique a pasta\\\\subpasta e para d:\pastadeexemplo, especifique d:\\\\pastadeexemplo.<br/><br/>Você pode combinar essa propriedade com **partitionBy** para ter caminhos de pastas com base na fatia de data/hora de início/término. |Sim |
| fileName |Especifique o nome do arquivo no **folderPath** se quiser que a tabela se refira a um arquivo específico na pasta. Se você não especificar algum valor para essa propriedade, a tabela apontará para todos os arquivos na pasta.<br/><br/>Quando o fileName não for especificado para um conjunto de dados de saída, o nome do arquivo gerado será no seguinte formato: <br/><br/>`Data.<Guid>.txt` (por exemplo:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Não |
| partitionedBy |partitionedBy pode usado para especificar um filename, folderPath dinâmico para dados de série temporal. Exemplo: folderPath parametrizado para cada hora dos dados. |Não |
| format | Há suporte para os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Defina a propriedade **type** sob formato como um desses valores. Para saber mais, veja as seções [Formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [Formato Json](data-factory-supported-file-and-compression-formats.md#json-format), [Formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format), e [Formato Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format). <br><br> Se você quiser **copiar arquivos no estado em que se encontram** entre repositórios baseados em arquivo (cópia binária), ignore a seção de formato nas duas definições de conjunto de dados de entrada e de saída. |Não |
| compression | Especifique o tipo e o nível de compactação para os dados. Tipos compatíveis são: **GZip**, **Deflate**, **BZip2** e **ZipDeflate**. Níveis compatíveis são: **Ideal** e **Mais Rápido**. Para saber mais, confira [File and compression formats in Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support) (Formatos de arquivo e de compactação no Azure Data Factory). |Não |

> [!NOTE]
> filename e fileFilter não podem ser usados simultaneamente.

#### <a name="example"></a>Exemplo

```json
{
    "name": "InputDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "HDFSLinkedService",
        "typeProperties": {
            "folderPath": "DataTransfer/UnitTest/"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Para obter mais informações, confira o artigo Conector do HDFS.

### <a name="file-system-source-in-copy-activity"></a>Origem do Sistema de Arquivos na Atividade de Cópia
Se você estiver copiando dados do HDFS, defina o **source type** da atividade de cópia para **FileSystemSource** e especifique as propriedades a seguir na seção **source**:

**FileSystemSource** suporta as seguintes propriedades:

| Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| recursive |Indica se os dados são lidos recursivamente a partir das subpastas ou somente da pasta especificada. |True, False (padrão) |Não |

#### <a name="example"></a>Exemplo

```json
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "HdfsToBlobCopy",
            "inputs": [{
                "name": "InputDataset"
            }],
            "outputs": [{
                "name": "OutputDataset"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Para obter mais informações, confira o artigo Conector do HDFS.

## <a name="sftp"></a>SFTP


### <a name="linked-service"></a>Serviço vinculado
Para definir um serviço vinculado do SFTP, defina o **type** do serviço vinculado para **Sftp** e especifique as propriedades a seguir na seção **typeProperties**:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| host | Nome ou endereço IP do servidor SFTP. |Sim |
| port |Porta na qual o servidor SFTP está escutando. O valor padrão é: 21 |Não |
| authenticationType |Especifique o tipo de autenticação. Valores permitidos: **Básico**, **SshPublicKey**. <br><br> Consulte as seções [Usando a autenticação Básica](#using-ssh-public-key-authentication) e Usando autenticação de chave pública SSH para ver mais propriedades e exemplos de JSON, respectivamente. |Sim |
| skipHostKeyValidation | Especifique se deseja ignorar a validação da chave de host. | Nº O valor padrão: false |
| hostKeyFingerprint | Especifique a impressão digital da chave de host. | Sim se o `skipHostKeyValidation` estiver definido como false.  |
| gatewayName |Nome do Gateway de Gerenciamento de Dados para se conectar a um servidor SFTP local. | Sim se estiver copiando dados de um servidor SFTP local. |
| encryptedCredential | Credencial criptografada para acessar o servidor SFTP. Gerado automaticamente quando você especifica a autenticação Básica (nome de usuário + senha) ou autenticação SshPublicKey (nome de usuário + conteúdo ou caminho da chave privada) no assistente de cópia ou na caixa de diálogo de pop-up do ClickOnce. | Nº Aplique somente quando estiver copiando dados de um servidor SFTP local. |

#### <a name="example-using-basic-authentication"></a>Exemplo: Usando a autenticação Básica

Para usar a autenticação Básica, defina `authenticationType` como `Basic` e especifique as propriedades a seguir, além das genéricas do conector SFTP apresentadas na última seção:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| Nome de Usuário | Usuário que tem acesso ao servidor SFTP. |Sim |
| password | Senha do usuário (nome de usuário). | Sim |

```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<SFTP server name or IP address>",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "password": "xxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-basic-authentication-with-encrypted-credential"></a>Exemplo: **Autenticação básica com credenciais criptografadas**

```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<FTP server name or IP address>",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="using-ssh-public-key-authentication"></a>**Usando a autenticação de chave pública SSH:**

Para usar a autenticação Básica, defina `authenticationType` como `SshPublicKey` e especifique as propriedades a seguir, além das genéricas do conector SFTP apresentadas na última seção:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| Nome de Usuário |Usuário que tem acesso ao servidor SFTP |Sim |
| privateKeyPath | Especifique, para o arquivo de chave privada, um caminho absoluto que esse gateway possa acessar. | Especifique `privateKeyPath` ou `privateKeyContent`. <br><br> Aplique somente quando estiver copiando dados de um servidor SFTP local. |
| privateKeyContent | Uma cadeia de caracteres serializada do conteúdo da chave privada. O Assistente de Cópia pode ler o arquivo de chave privada e extrair o conteúdo da chave privada automaticamente. Se você estiver usando qualquer outra ferramenta/SDK, use a propriedade privateKeyPath em seu lugar. | Especifique `privateKeyPath` ou `privateKeyContent`. |
| passPhrase | Especifique a senha/frase secreta para descriptografar a chave particular se o arquivo de chave for protegido por uma frase secreta. | Sim, se o arquivo de chave privada for protegido por uma frase secreta. |

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyPath",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<FTP server name or IP address>",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyPath": "D:\\privatekey_openssh",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true,
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-sshpublickey-authentication-using-private-key-content"></a>Exemplo: **Autenticação SshPublicKey usando o conteúdo da chave privada**

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyContent",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver.westus.cloudapp.azure.com",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyContent": "<base64 string of the private key content>",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true
        }
    }
}
```

Para obter mais informações, consulte o artigo [Conector do SFTP](data-factory-sftp-connector.md#linked-service-properties).

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados do SFTP, defina o **type** do conjunto de dados para **FileShare** e especifique as propriedades a seguir na seção **typeProperties**:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| folderPath |Subcaminho para a pasta. Use o caractere de escape ' \ ' para caracteres especiais na cadeia de caracteres. Para obter exemplos, confira Definições de conjunto de dados e serviço vinculado de exemplo.<br/><br/>Você pode combinar essa propriedade com **partitionBy** para ter caminhos de pastas com base na fatia de data/hora de início/término. |Sim |
| fileName |Especifique o nome do arquivo no **folderPath** se quiser que a tabela se refira a um arquivo específico na pasta. Se você não especificar algum valor para essa propriedade, a tabela apontará para todos os arquivos na pasta.<br/><br/>Quando o fileName não for especificado para um conjunto de dados de saída, o nome do arquivo gerado será no seguinte formato: <br/><br/>`Data.<Guid>.txt` (Exemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Não |
| fileFilter |Especifique um filtro a ser usado para selecionar um subconjunto de arquivos no folderPath em vez de todos os arquivos.<br/><br/>Os valores permitidos são: `*` (vários caracteres) e `?` (um único caractere).<br/><br/>Exemplo 1: `"fileFilter": "*.log"`<br/>Exemplo 2: `"fileFilter": 2016-1-?.txt"`<br/><br/> fileFilter é aplicável a um conjunto de dados FileShare de entrada. Essa propriedade não tem suporte com HDFS. |Não |
| partitionedBy |partitionedBy pode usado para especificar um filename, folderPath dinâmico para dados de série temporal. Por exemplo, folderPath parametrizado para cada hora dos dados. |Não |
| format | Há suporte para os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Defina a propriedade **type** sob formato como um desses valores. Para saber mais, veja as seções [Formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [Formato Json](data-factory-supported-file-and-compression-formats.md#json-format), [Formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format), e [Formato Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format). <br><br> Se você quiser **copiar arquivos no estado em que se encontram** entre repositórios baseados em arquivo (cópia binária), ignore a seção de formato nas duas definições de conjunto de dados de entrada e de saída. |Não |
| compression | Especifique o tipo e o nível de compactação para os dados. Tipos compatíveis são: **GZip**, **Deflate**, **BZip2** e **ZipDeflate**. Níveis compatíveis são: **Ideal** e **Mais Rápido**. Para saber mais, confira [File and compression formats in Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support) (Formatos de arquivo e de compactação no Azure Data Factory). |Não |
| useBinaryTransfer |Especifique se deve usar o modo de transferência Binário. True para o modo binário e ASCII false. Valor padrão: True. Essa propriedade só pode ser usada quando o tipo de serviço vinculado associado é do tipo: FtpServer. |Não |

> [!NOTE]
> filename e fileFilter não podem ser usados simultaneamente.

#### <a name="example"></a>Exemplo

```json
{
    "name": "SFTPFileInput",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "SftpLinkedService",
        "typeProperties": {
            "folderPath": "<path to shared folder>",
            "fileName": "test.csv"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Para obter mais informações, consulte o artigo [Conector do SFTP](data-factory-sftp-connector.md#dataset-properties).

### <a name="file-system-source-in-copy-activity"></a>Origem do Sistema de Arquivos na Atividade de Cópia
Se você estiver copiando dados de uma origem do SFTP, defina o **source type** da atividade de cópia para **FileSystemSource** e especifique as propriedades a seguir na seção **source**:

| Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| recursive |Indica se os dados são lidos recursivamente a partir das subpastas ou somente da pasta especificada. |True, False (padrão) |Não |



#### <a name="example"></a>Exemplo

```json
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "SFTPToBlobCopy",
            "inputs": [{
                "name": "SFTPFileInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2017-02-20T18:00:00",
        "end": "2017-02-20T19:00:00"
    }
}
```

Para obter mais informações, consulte o artigo [Conector do SFTP](data-factory-sftp-connector.md#copy-activity-properties).


## <a name="http"></a>HTTP

### <a name="linked-service"></a>Serviço vinculado
Para definir um serviço vinculado do HTTP, defina o **type** do serviço vinculado para **Http** e especifique as propriedades a seguir na seção **typeProperties**:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| url | URL base para o Servidor Web | Sim |
| authenticationType | Especifica o tipo de autenticação. Valores permitidos são: **Anônima**, **Básica**, **Digest**, **Windows**, **ClientCertificate**. <br><br> Consulte as seções abaixo desta tabela para mais propriedades e amostras JSON para esses tipos de autenticação, respectivamente. | Sim |
| enableServerCertificateValidation | Especifique se deseja habilitar a validação do certificado SSL do servidor se a origem for um servidor Web HTTPS | Não, o padrão é true |
| gatewayName | Nome do Gateway de Gerenciamento de Dados para se conectar a uma origem HTTP local. | Sim se estiver copiando dados de uma origem HTTP local. |
| encryptedCredential | Credencial criptografada para acessar o ponto de extremidade HTTP. Gerado automaticamente quando você configura as informações de autenticação no assistente de cópia ou a caixa de diálogo pop-up do ClickOnce. | Nº Aplique somente quando estiver copiando dados de um servidor HTTP local. |

#### <a name="example-using-basic-digest-or-windows-authentication"></a>Exemplo: Usando a autenticação Básica, Digest ou Windows
Defina `authenticationType` como `Basic`, `Digest` ou `Windows` e especifique as propriedades a seguir, além das genéricas do conector HTTP apresentadas acima:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| Nome de Usuário | Nome de usuário para acessar o ponto de extremidade HTTP. | Sim |
| password | Senha do usuário (nome de usuário). | Sim |

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "Http",
        "typeProperties": {
            "authenticationType": "basic",
            "url": "https://en.wikipedia.org/wiki/",
            "userName": "user name",
            "password": "password"
        }
    }
}
```

#### <a name="example-using-clientcertificate-authentication"></a>Exemplo: Usando a autenticação ClientCertificate

Para usar a autenticação Básica, defina `authenticationType` como `ClientCertificate` e especifique as propriedades a seguir, além das genéricas do conector HTTP apresentadas acima:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| embeddedCertData | O conteúdo codificado na Base64 de dados binários do arquivo Personal Information Exchange (PFX). | Especifique `embeddedCertData` ou `certThumbprint`. |
| certThumbprint | A impressão digital do certificado que foi instalado no repositório de certificados do computador do gateway. Aplique somente ao copiar dados de uma origem HTTP local. | Especifique `embeddedCertData` ou `certThumbprint`. |
| password | Senha associada ao certificado. | Não |

Se você usar `certThumbprint` para autenticação e o certificado estiver instalado no armazenamento pessoal do computador local, você precisará conceder a permissão de leitura para o serviço de gateway:

1. Iniciar o MMC (Console de Gerenciamento Microsoft). Adicionar o snap-in **Certificados**, que tem como destino o **Computador Local**.
2. Expanda **Certificados**, **Pessoal** e clique em **Certificados**.
3. Clique com o botão direito do mouse no certificado do repositório pessoal e selecione **Todas as Tarefas**->**Gerenciar Chaves Particulares...**
3. Na guia **Segurança**, adicione a conta de usuário sob a qual o serviço de Host de Gateway de Gerenciamento de Dados está em execução com o acesso de leitura para o certificado.

**Exemplo: usando o certificado do cliente:** Esse serviço vinculado vincula seu data factory a um servidor Web HTTP local. Ele usa um certificado do cliente instalado no computador com o Gateway de Gerenciamento de Dados instalado.

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "Http",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "certThumbprint": "thumbprint of certificate",
            "gatewayName": "gateway name"
        }
    }
}
```

#### <a name="example-using-client-certificate-in-a-file"></a>Exemplo: usando o certificado do cliente em um arquivo
Esse serviço vinculado vincula seu data factory a um servidor Web HTTP local. Ele usa um certificado do cliente no computador com o Gateway de Gerenciamento de Dados instalado.

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "Http",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "embeddedCertData": "base64 encoded cert data",
            "password": "password of cert"
        }
    }
}
```

Para obter mais informações, consulte o artigo [Conector do HTTP](data-factory-http-connector.md#linked-service-properties).

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados do HDFS, defina o **type** do conjunto de dados para **Http** e especifique as propriedades a seguir na seção **typeProperties**:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| relativeUrl | Uma URL relativa para o recurso que contém os dados. Quando o caminho não for especificado, apenas a URL especificada na definição do serviço vinculado será usada. <br><br> Para construir um URL dinâmico, você pode usar [funções de Data Factory e variáveis de sistema](data-factory-functions-variables.md), Exemplo: `"relativeUrl": "$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)"`. | Não |
| requestMethod | Método Http. Os valores permitidos são **GET** ou **POST**. | Nº O padrão é `GET`. |
| additionalHeaders | Cabeçalhos de solicitação HTTP adicionais. | Não |
| requestBody | O corpo da solicitação HTTP. | Não |
| format | Se você quiser simplesmente **recuperar os dados do ponto de extremidade HTTP como estão** sem analisá-los, ignore estas configurações de formato. <br><br> Se você quer analisar o conteúdo da resposta HTTP durante a cópia, os seguintes tipos de formato têm suporte: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Para saber mais, veja as seções [Formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [Formato Json](data-factory-supported-file-and-compression-formats.md#json-format), [Formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format), e [Formato Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format). |Não |
| compression | Especifique o tipo e o nível de compactação para os dados. Tipos compatíveis são: **GZip**, **Deflate**, **BZip2** e **ZipDeflate**. Níveis compatíveis são: **Ideal** e **Mais Rápido**. Para saber mais, confira [File and compression formats in Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support) (Formatos de arquivo e de compactação no Azure Data Factory). |Não |

#### <a name="example-using-the-get-default-method"></a>Exemplo: usando o método GET (padrão)

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "XXX/test.xml",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

#### <a name="example-using-the-post-method"></a>Exemplo: usando o método POST

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "/XXX/test.xml",
            "requestMethod": "Post",
            "requestBody": "body for POST HTTP request"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```
Para obter mais informações, consulte o artigo [Conector do HTTP](data-factory-http-connector.md#dataset-properties).

### <a name="http-source-in-copy-activity"></a>Origem do HTTP na Atividade de Cópia
Se você estiver copiando dados de uma origem do HTTP, defina o **source type** da atividade de cópia para **HttpSource** e especifique as propriedades a seguir na seção **source**:

| Propriedade | DESCRIÇÃO | Obrigatório |
| -------- | ----------- | -------- |
| httpRequestTimeout | O tempo limite (TimeSpan) para a solicitação HTTP obter uma resposta. É o tempo limite para obter uma resposta e não o tempo limite para ler dados de resposta. | Nº Valor padrão: 00:01:40 |


#### <a name="example"></a>Exemplo

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "HttpSourceToAzureBlob",
            "description": "Copy from an HTTP source to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "HttpSourceDataInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "HttpSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Para obter mais informações, consulte o artigo [Conector do HTTP](data-factory-http-connector.md#copy-activity-properties).

## <a name="odata"></a>OData

### <a name="linked-service"></a>Serviço vinculado
Para definir um serviço vinculado do OData, defina o **type** do serviço vinculado para **OData** e especifique as propriedades a seguir na seção **typeProperties**:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| url |URL do serviço OData. |Sim |
| authenticationType |Tipo de autenticação usada para se conectar ao armazenamento de dados OData. <br/><br/> Para o OData de nuvem, os valores possíveis são Anônimo, Básico e OAuth (observe que o Azure Data Factory dá suporte no momento apenas a OAuth baseado no Azure Active Directory). <br/><br/> Para OData local, os valores possíveis são Anonymous, Basic e Windows. |Sim |
| Nome de Usuário |Especifique o nome de usuário se você estiver usando a autenticação Básica. |Sim (apenas se você estiver usando a autenticação Básica) |
| password |Especifique a senha da conta de usuário que você especificou para o nome de usuário. |Sim (apenas se você estiver usando a autenticação Básica) |
| authorizedCredential |Se você estiver usando OAuth, clique no botão **Autorizar** no Editor ou Assistente de Cópia do Data Factory e digite suas credenciais. O valor dessa propriedade será gerado automaticamente. |Sim (apenas se você estiver usando a autenticação OAuth) |
| gatewayName |O nome do gateway que o serviço Data Factory deve usar para se conectar ao serviço OData local. Especifique apenas se você estiver copiando dados de origem de OData local. |Não |

#### <a name="example---using-basic-authentication"></a>Exemplo - Usando a autenticação básica
```json
{
    "name": "inputLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Basic",
            "username": "username",
            "password": "password"
        }
    }
}
```

#### <a name="example---using-anonymous-authentication"></a>Exemplo - Usando a autenticação anônima

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        }
    }
}
```

#### <a name="example---using-windows-authentication-accessing-on-premises-odata-source"></a>Exemplo - Usando a autenticação do Windows para acessar uma origem de OData local

```json
{
    "name": "inputLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of on-premises OData source, for example, Dynamics CRM>",
            "authenticationType": "Windows",
            "username": "domain\\user",
            "password": "password",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example---using-oauth-authentication-accessing-cloud-odata-source"></a>Exemplo - Usando autenticação OAuth para acessar a origem de OData da nuvem
```json
{
    "name": "inputLinkedService",
    "properties":
    {
        "type": "OData",
            "typeProperties":
        {
            "url": "<endpoint of cloud OData source, for example, https://<tenant>.crm.dynamics.com/XRMServices/2011/OrganizationData.svc>",
            "authenticationType": "OAuth",
            "authorizedCredential": "<auto generated by clicking the Authorize button on UI>"
        }
    }
}
```

Para obter mais informações, consulte o artigo [Conector do OData](data-factory-odata-connector.md#linked-service-properties).

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados do OData, defina o **type** do conjunto de dados para **ODataResource** e especifique as propriedades a seguir na seção **typeProperties**:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| path |Caminho para o recurso OData |Não |

#### <a name="example"></a>Exemplo

```json
{
    "name": "ODataDataset",
    "properties": {
        "type": "ODataResource",
        "typeProperties": {
            "path": "Products"
        },
        "linkedServiceName": "ODataLinkedService",
        "structure": [],
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "retryInterval": "00:01:00",
            "retryTimeout": "00:10:00",
            "maximumRetry": 3
        }
    }
}
```

Para obter mais informações, consulte o artigo [Conector do OData](data-factory-odata-connector.md#dataset-properties).

### <a name="relational-source-in-copy-activity"></a>Origem Relacional na Atividade de Cópia
Se você estiver copiando dados de uma origem de OData, defina o **source type** da atividade de cópia para **RelationalSource** e especifique as propriedades a seguir na seção **source**:

| Propriedade | DESCRIÇÃO | Exemplo | Obrigatório |
| --- | --- | --- | --- |
| query |Utiliza a consulta personalizada para ler os dados. |"?$select=Name, Description&$top=5" |Não |

#### <a name="example"></a>Exemplo

```json
{
    "name": "CopyODataToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "?$select=Name, Description&$top=5"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "ODataDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobODataDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "ODataToBlob"
        }],
        "start": "2017-02-01T18:00:00",
        "end": "2017-02-03T19:00:00"
    }
}
```

Para obter mais informações, consulte o artigo [Conector do OData](data-factory-odata-connector.md#copy-activity-properties).


## <a name="odbc"></a>ODBC


### <a name="linked-service"></a>Serviço vinculado
Para definir um serviço vinculado do ODBC, defina o **type** do serviço vinculado para **OnPremisesOdbc** e especifique as propriedades a seguir na seção **typeProperties**:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| connectionString |A parte da credencial que não está relacionada ao acesso da cadeia de conexão e uma credencial criptografada opcional. Veja os exemplos nas seções a seguir. |Sim |
| credential |A parte da credencial de acesso da cadeia de conexão especificada no formato propriedade-valor específico do driver. Exemplo: `“Uid=<user ID>;Pwd=<password>;RefreshToken=<secret refresh token>;”.` |Não |
| authenticationType |Tipo de autenticação usado para se conectar ao armazenamento de dados ODBC. Os valores possíveis são: Anônimo e Básico. |Sim |
| Nome de Usuário |Especifique o nome de usuário se você estiver usando a autenticação Básica. |Não |
| password |Especifique a senha da conta de usuário que você especificou para o nome de usuário. |Não |
| gatewayName |O nome do gateway que o serviço Data Factory deve usar para se conectar ao armazenamento de dados ODBC. |Sim |

#### <a name="example---using-basic-authentication"></a>Exemplo - Usando a autenticação básica

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "OnPremisesOdbc",
        "typeProperties": {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
            "userName": "username",
            "password": "password",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```
#### <a name="example---using-basic-authentication-with-encrypted-credentials"></a>Exemplo - Usando a autenticação básica com credenciais criptografadas
Você pode criptografar as credenciais usando o [New-AzDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) cmdlet.

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "OnPremisesOdbc",
        "typeProperties": {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=myserver.database.windows.net; Database=TestDatabase;;EncryptedCredential=eyJDb25uZWN0...........................",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-using-anonymous-authentication"></a>Exemplo: Usando a autenticação anônima

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "OnPremisesOdbc",
        "typeProperties": {
            "authenticationType": "Anonymous",
            "connectionString": "Driver={SQL Server};Server={servername}.database.windows.net; Database=TestDatabase;",
            "credential": "UID={uid};PWD={pwd}",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Para obter mais informações, consulte o artigo [Conector do ODBC](data-factory-odbc-connector.md#linked-service-properties).

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados do ODBC, defina o **type** do conjunto de dados para **RelationalTable** e especifique as propriedades a seguir na seção **typeProperties**:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| tableName |Nome da tabela no repositório de dados ODBC. |Sim |


#### <a name="example"></a>Exemplo

```json
{
    "name": "ODBCDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "ODBCLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Para obter mais informações, consulte o artigo [Conector do ODBC](data-factory-odbc-connector.md#dataset-properties).

### <a name="relational-source-in-copy-activity"></a>Origem Relacional na Atividade de Cópia
Se você estiver copiando dados de um armazenamento de dados do ODBC, defina o **source type** da atividade de cópia para **RelationalSource** e especifique as propriedades a seguir na seção **source**:

| Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| query |Utiliza a consulta personalizada para ler os dados. |Cadeia de caracteres de consulta SQL. Por exemplo: `select * from MyTable`. |Sim |

#### <a name="example"></a>Exemplo

```json
{
    "name": "CopyODBCToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "OdbcDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobOdbcDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "OdbcToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Para obter mais informações, consulte o artigo [Conector do ODBC](data-factory-odbc-connector.md#copy-activity-properties).

## <a name="salesforce"></a>Salesforce


### <a name="linked-service"></a>Serviço vinculado
Para definir um serviço vinculado da Salesforce, defina o **type** do serviço vinculado para **Salesforce** e especifique as propriedades a seguir na seção **typeProperties**:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| environmentUrl | Especifica a URL da instância do Salesforce. <br><br> – O padrão é "https:\//login.salesforce.com". <br> – Para copiar dados da área restrita, especifique “https://test.salesforce.com”. <br> – Para copiar dados do domínio personalizado, especifique, por exemplo, "https://[domínio].my.salesforce.com". |Não |
| username |Especifique um nome de usuário para a conta de usuário. |Sim |
| password |Especifique um senha para a conta de usuário. |Sim |
| securityToken |Especifique um token de segurança para a conta de usuário. Veja [Obter token de segurança](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) para ver instruções sobre como redefinir/obter o token de segurança. Para saber mais sobre os tokens de segurança em geral, veja [Security and the API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm) (Segurança e a API). |Sim |

#### <a name="example"></a>Exemplo

```json
{
    "name": "SalesforceLinkedService",
    "properties": {
        "type": "Salesforce",
        "typeProperties": {
            "username": "<user name>",
            "password": "<password>",
            "securityToken": "<security token>"
        }
    }
}
```

Para obter mais informações, consulte o artigo [Conector da Salesforce](data-factory-salesforce-connector.md#linked-service-properties).

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados da Salesforce, defina o **type** do conjunto de dados para **RelationalTable** e especifique as propriedades a seguir na seção **typeProperties**:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| tableName |Nome da tabela no Salesforce. |Não (se uma **consulta** de **RelationalSource** for especificada) |

#### <a name="example"></a>Exemplo

```json
{
    "name": "SalesforceInput",
    "properties": {
        "linkedServiceName": "SalesforceLinkedService",
        "type": "RelationalTable",
        "typeProperties": {
            "tableName": "AllDataType__c"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Para obter mais informações, consulte o artigo [Conector da Salesforce](data-factory-salesforce-connector.md#dataset-properties).

### <a name="relational-source-in-copy-activity"></a>Origem Relacional na Atividade de Cópia
Se você estiver copiando dados da Salesforce, defina o **source type** da atividade de cópia para **RelationalSource** e especifique as propriedades a seguir na seção **source**:

| Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| query |Utiliza a consulta personalizada para ler os dados. |Uma consulta SQL-92 ou uma consulta [SOQL (Salesforce Object Query Language)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm). Por exemplo: `select * from MyTable__c`. |Não (se **tableName** do **conjunto de dados** for especificado) |

#### <a name="example"></a>Exemplo



```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "SalesforceToAzureBlob",
            "description": "Copy from Salesforce to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "SalesforceInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "SELECT Id, Col_AutoNumber__c, Col_Checkbox__c, Col_Currency__c, Col_Date__c, Col_DateTime__c, Col_Email__c, Col_Number__c, Col_Percent__c, Col_Phone__c, Col_Picklist__c, Col_Picklist_MultiSelect__c, Col_Text__c, Col_Text_Area__c, Col_Text_AreaLong__c, Col_Text_AreaRich__c, Col_URL__c, Col_Text_Encrypt__c, Col_Lookup__c FROM AllDataType__c"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

> [!IMPORTANT]
> A parte "__c" do Nome da API é necessária para qualquer objeto personalizado.

Para obter mais informações, consulte o artigo [Conector da Salesforce](data-factory-salesforce-connector.md#copy-activity-properties).

## <a name="web-data"></a>Dados da Web

### <a name="linked-service"></a>Serviço vinculado
Para definir um serviço vinculado da Web, defina o **type** do serviço vinculado para **Web** e especifique as propriedades a seguir na seção **typeProperties**:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| Url |URL para a origem da Web |Sim |
| authenticationType |Anônima. |Sim |


#### <a name="example"></a>Exemplo


```json
{
    "name": "web",
    "properties": {
        "type": "Web",
        "typeProperties": {
            "authenticationType": "Anonymous",
            "url": "https://en.wikipedia.org/wiki/"
        }
    }
}
```

Para obter mais informações, consulte o artigo [Conector da Tabela da Web](data-factory-web-table-connector.md#linked-service-properties).

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados da Web, defina o **type** do conjunto de dados para **WebTable** e especifique as propriedades a seguir na seção **typeProperties**:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type |tipo do conjunto de dados. Deve ser definido como **WebTable** |Sim |
| path |Uma URL relativa para o recurso que contém a tabela. |Nº Quando o caminho não for especificado, apenas a URL especificada na definição do serviço vinculado será usada. |
| index |O índice da tabela no recurso. Confira a seção Obter índice de uma tabela em uma página HTML a fim de ver as etapas para obter o índice de uma tabela em uma página HTML. |Sim |

#### <a name="example"></a>Exemplo

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "linkedServiceName": "WebLinkedService",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Para obter mais informações, consulte o artigo [Conector da Tabela da Web](data-factory-web-table-connector.md#dataset-properties).

### <a name="web-source-in-copy-activity"></a>Origem da Web na Atividade de Cópia
Se você estiver copiando dados de uma tabela da web, defina o **source type** de atividade de cópia para **WebSource**. Atualmente, quando a origem na atividade de cópia é do tipo **WebSource**, não há suporte para propriedades adicionais.

#### <a name="example"></a>Exemplo

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "WebTableToAzureBlob",
            "description": "Copy from a Web table to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "WebTableInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "WebSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Para obter mais informações, consulte o artigo [Conector da Tabela da Web](data-factory-web-table-connector.md#copy-activity-properties).

## <a name="compute-environments"></a>AMBIENTES DE COMPUTAÇÃO
A tabela a seguir lista os ambientes de computação com suporte do Data Factory e as atividades de transformação que podem ser executadas neles. Clique no link para a computação a qual você está interessado em ver os esquemas JSON para o serviço vinculado para vinculá-lo a um data factory.

| Ambiente de computação | Atividades |
| --- | --- |
| [Cluster HDInsight sob demanda](#on-demand-azure-hdinsight-cluster) ou [seu próprio cluster HDInsight](#existing-azure-hdinsight-cluster) |[Atividade personalizada do .NET](#net-custom-activity), [Atividade Hive](#hdinsight-hive-activity), [Atividade Pig](#hdinsight-pig-activity), [Atividade MapReduce](#hdinsight-mapreduce-activity), Atividade de streaming do Hadoop, [Atividade do Spark](#hdinsight-spark-activity) |
| [Lote do Azure](#azure-batch) |[Atividade personalizada do .NET](#net-custom-activity) |
| [Azure Machine Learning](#azure-machine-learning) | [Atividade de Execução de Lote de Machine Learning](#machine-learning-batch-execution-activity), [Atividade de Atualização de Recursos de Machine Learning](#machine-learning-update-resource-activity) |
| [Análise Azure Data Lake](#azure-data-lake-analytics) |[U-SQL da Análise Data Lake](#data-lake-analytics-u-sql-activity) |
| [Banco de Dados SQL do Azure](#azure-sql-database-1), [SQL Data Warehouse do Azure](#azure-sql-data-warehouse-1), [SQL Server](#sql-server-1) |[Procedimento armazenado](#stored-procedure-activity) |

## <a name="on-demand-azure-hdinsight-cluster"></a>Cluster HDInsight do Azure sob demanda
O serviço Azure Data Factory pode criar automaticamente um cluster HDInsight sob demanda baseado em Windows/Linux para processar dados. O cluster é criado na mesma região que a conta de armazenamento (propriedade linkedServiceName em JSON) associada ao cluster. É possível executar as seguintes atividades de transformação nesse serviço vinculado: [Atividade personalizada do .NET](#net-custom-activity), [Atividade Hive](#hdinsight-hive-activity), [Atividade Pig](#hdinsight-pig-activity), [Atividade MapReduce](#hdinsight-mapreduce-activity), Atividade de streaming do Hadoop, [Atividade do Spark](#hdinsight-spark-activity).

### <a name="linked-service"></a>Serviço vinculado
A tabela a seguir fornece as descrições das propriedades usadas na definição de JSON do Azure de um serviço vinculado do HDInsight sob demanda.

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| type |A propriedade de tipo deve ser configurada como **HDInsightOnDemand**. |Sim |
| clusterSize |Número de nós de trabalho/dados no cluster. O cluster HDInsight é criado com 2 nós principais juntamente com o número de nós de trabalho que você especifica para esta propriedade. Os nós são do tamanho Standard_D3 que tem 4 núcleos; portanto, um cluster de 4 nós de trabalho usa 24 núcleos (4\*4 = 16 núcleos para nós de trabalho + 2\*4 = 8 núcleos para nós de cabeçalho). Veja [Criar clusters do Hadoop baseados em Linux no HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) para obter detalhes sobre a camada Standard_D3. |Sim |
| timeToLive |O tempo ocioso permitido para o cluster HDInsight sob demanda. Especifica quanto tempo o cluster HDInsight sob demanda permanece ativo após a conclusão de uma atividade executada se não há nenhum outro trabalho ativo no cluster.<br/><br/>Por exemplo, se uma execução de atividade demora 6 minutos e o timetolive é definido como 5 minutos, o cluster fica ativo durante 5 minutos após a execução de 6 minutos de execução da atividade. Se outra atividade é executada com a janela de 6 minutos, ela é processada pelo mesmo cluster.<br/><br/>A criação de um cluster HDInsight sob demanda é uma operação cara (pode demorar um pouco) e, portanto, use essa configuração conforme o necessário para melhorar o desempenho de um data factory com a reutilização de um cluster HDInsight sob demanda.<br/><br/>Se você definir o valor de timetolive como 0, o cluster é excluído assim que a atividade executada é processada. Por outro lado, se você definir um valor alto, o cluster pode permanecer ocioso desnecessariamente resultando em altos custos. Portanto, é importante que você defina o valor apropriado com base em suas necessidades.<br/><br/>Vários pipelines podem compartilhar a mesma instância do cluster do HDInsight sob demanda se o valor da propriedade timetolive estiver definido corretamente |Sim |
| version |Versão do cluster HDInsight Para obter detalhes, confira [Versões com suporte do HDInsight no Azure Data Factory](data-factory-compute-linked-services.md#supported-hdinsight-versions-in-azure-data-factory). |Não |
| linkedServiceName |Serviço vinculado do Armazenamento do Azure a ser usado pelo cluster sob demanda para armazenar e processar dados. <p>Atualmente, não é possível criar um cluster HDInsight sob demanda que use um Azure Data Lake Store como o armazenamento. Se você quiser armazenar os dados resultantes do processamento do HDInsight em um Azure Data Lake Store, use uma Atividade de Cópia para copiar os dados do Armazenamento de Blobs do Azure para o Azure Data Lake Store.</p>  | Sim |
| additionalLinkedServiceNames |Especifica as contas de armazenamento adicionais para o serviço vinculado do HDInsight para que o serviço do Data Factory possa registrá-los em seu nome. |Não |
| osType |Tipo do sistema operacional. Valores permitidos são: Windows (padrão) e Linux |Não |
| hcatalogLinkedServiceName |O nome do serviço vinculado do SQL Azure que aponta para o banco de dados HCatalog. O cluster HDInsight sob demanda é criado usando o banco de dados SQL do Azure como o metastore. |Não |

### <a name="json-example"></a>Exemplo de JSON
O JSON a seguir define um serviço vinculado HDInsight sob demanda baseado em Linux. O serviço Data Factory cria automaticamente um cluster HDInsight **baseado em Linux** ao processar uma fatia de dados.

```json
{
    "name": "HDInsightOnDemandLinkedService",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "linkedServiceName": "StorageLinkedService"
        }
    }
}
```

Para obter mais informações, consulte o artigo [Serviços vinculados de computação](data-factory-compute-linked-services.md).

## <a name="existing-azure-hdinsight-cluster"></a>Cluster Azure HDInsight existente
Você pode criar um serviço vinculado Azure HDInsight para registrar seu próprio cluster HDInsight com o Data Factory. É possível executar as seguintes atividades de transformação de dados neste serviço vinculado: [Atividade personalizada do .NET](#net-custom-activity), [Atividade Hive](#hdinsight-hive-activity), [Atividade Pig](#hdinsight-pig-activity), [Atividade MapReduce](#hdinsight-mapreduce-activity), Atividade de streaming do Hadoop, [Atividade do Spark](#hdinsight-spark-activity).

### <a name="linked-service"></a>Serviço vinculado
A tabela a seguir fornece as descrições das propriedades usadas na definição de JSON do Azure de um serviço vinculado do Azure HDInsight.

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| type |A propriedade de tipo deve ser configurada como **HDInsight**. |Sim |
| clusterUri |A URI do cluster HDInsight. |Sim |
| Nome de Usuário |Especifique o nome do usuário a ser usado para se conectar a um cluster HDInsight existente. |Sim |
| password |Especifique a senha para a conta de usuário. |Sim |
| linkedServiceName | Nome do serviço vinculado do Armazenamento do Azure que faz referência ao Armazenamento de Blobs usado pelo cluster HDInsight. <p>No momento, você não pode especificar um serviço vinculado do Azure Data Lake Store para essa propriedade. Você pode acessar dados no Azure Data Lake Store usando scripts Hive/Pig se o cluster HDInsight tiver acesso ao Data Lake Store. </p>  |Sim |

Para conferir as versões de clusters HDInsight com suporte, veja [Versões do HDInsight com suporte](data-factory-compute-linked-services.md#supported-hdinsight-versions-in-azure-data-factory).

#### <a name="json-example"></a>Exemplo de JSON

```json
{
    "name": "HDInsightLinkedService",
    "properties": {
        "type": "HDInsight",
        "typeProperties": {
            "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
            "userName": "admin",
            "password": "<password>",
            "linkedServiceName": "MyHDInsightStoragelinkedService"
        }
    }
}
```

## <a name="azure-batch"></a>Lote do Azure
Você pode criar um serviço vinculado de Lote do Azure para registrar um pool de lote de máquinas virtuais (VMs) com uma fábrica de dados. Você pode executar atividades personalizadas do .NET usando o Lote do Azure ou o Azure HDInsight. Você pode executar uma [atividade personalizada do .NET](#net-custom-activity) neste serviço vinculado.

### <a name="linked-service"></a>Serviço vinculado
A tabela a seguir fornece as descrições das propriedades usadas na definição de JSON do Azure de um serviço vinculado do Lote do Azure.

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| type |A propriedade de tipo deve ser definida como **AzureBatch**. |Sim |
| accountName |Nome da conta do Lote do Azure. |Sim |
| accessKey |Tecla de acesso para a conta do Lote do Azure. |Sim |
| poolName |Nome do pool de máquinas virtuais. |Sim |
| linkedServiceName |Nome do serviço vinculado do Armazenamento do Azure associado ao serviço vinculado de Lote do Azure. Esse serviço vinculado é usado para arquivos de teste necessários para executar a atividade e armazenar os logs de execução da atividade. |Sim |


#### <a name="json-example"></a>Exemplo de JSON

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
        "type": "AzureBatch",
        "typeProperties": {
            "accountName": "<Azure Batch account name>",
            "accessKey": "<Azure Batch account key>",
            "poolName": "<Azure Batch pool name>",
            "linkedServiceName": "<Specify associated storage linked service reference here>"
        }
    }
}
```

## <a name="azure-machine-learning"></a>Azure Machine Learning
Criar um serviço vinculado do Azure Machine Learning para registrar um ponto de extremidade de pontuação do lote de Machine Learning com um data factory. Duas atividades de transformação de dados que podem ser executados nesse serviço vinculado: [Atividade de Execução de Lote de Machine Learning](#machine-learning-batch-execution-activity), [Atividade de Atualização de Recursos de Machine Learning](#machine-learning-update-resource-activity).

### <a name="linked-service"></a>Serviço vinculado
A tabela a seguir fornece as descrições das propriedades usadas na definição de JSON do Azure de um serviço vinculado do Azure Machine Learning.

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| Tipo |A propriedade de tipo deve ser configurada como: **AzureML**. |Sim |
| mlEndpoint |A URL de pontuação do lote. |Sim |
| apiKey |A API do modelo de workspace publicada. |Sim |

#### <a name="json-example"></a>Exemplo de JSON

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://[batch scoring endpoint]/jobs",
            "apiKey": "<apikey>"
        }
    }
}
```

## <a name="azure-data-lake-analytics"></a>Análise Azure Data Lake
Você cria um serviço vinculado da **Análise Azure Data Lake** para vincular um serviço de computação da Análise do Azure Data Lake a um Azure Data Factory antes de usar a atividade do [U-SQL da Análise Data Lake](data-factory-usql-activity.md) em um pipeline.

### <a name="linked-service"></a>Serviço vinculado

A tabela a seguir fornece as descrições das propriedades usadas na definição de JSON de um serviço vinculado do Azure Data Lake Analytics.

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| Tipo |A propriedade de tipo deve ser configurada como: **AzureDataLakeAnalytics**. |Sim |
| accountName |Nome da conta da Análise Azure Data Lake. |Sim |
| dataLakeAnalyticsUri |URI da Análise Azure Data Lake. |Não |
| autorização |O código de autorização é recuperado automaticamente depois de clicar no botão **Autorizar** no Editor do Data Factory e concluir o logon OAuth. |Sim |
| subscriptionId |Id de assinatura do Azure |Não (se não for especificado, a assinatura do Data Factory é usada). |
| resourceGroupName |Nome do grupo de recursos do Azure |Não (se não for especificado, o grupo de recursos do Data Factory é usado). |
| sessionId |ID da sessão de autorização OAuth. Cada ID da sessão é exclusiva e pode ser usado somente uma vez. A ID é gerada automaticamente quando o Editor do Data Factory é usado. |Sim |


#### <a name="json-example"></a>Exemplo de JSON
O exemplo a seguir fornece uma definição de JSON para um serviço vinculado da Análise Azure Data Lake.

```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "<account name>",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "authorization": "<authcode>",
            "sessionId": "<session ID>",
            "subscriptionId": "<subscription id>",
            "resourceGroupName": "<resource group name>"
        }
    }
}
```

## <a name="azure-sql-database"></a>Banco de Dados SQL do Azure
Você pode criar um serviço vinculado SQL do Azure e usá-lo com a [Atividade de Procedimento Armazenado](#stored-procedure-activity) para invocar um procedimento armazenado de um pipeline do Data Factory.

### <a name="linked-service"></a>Serviço vinculado
Para definir um serviço vinculado do Banco de Dados SQL do Azure, defina o **type** do serviço vinculado para **AzureSqlDatabase** e especifique as propriedades a seguir na seção **typeProperties**:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| connectionString |Especifique as informações necessárias para se conectar à instância do Banco de Dados SQL do Azure para a propriedade connectionString. |Sim |

#### <a name="json-example"></a>Exemplo de JSON

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
    }
}
```

Confira o artigo [Conector SQL do Azure](data-factory-azure-sql-connector.md#linked-service-properties) para saber mais sobre esse serviço vinculado.

## <a name="azure-sql-data-warehouse"></a>SQL Data Warehouse do Azure
Você pode criar um serviço vinculado do SQL Data Warehouse do Azure e usá-lo com a [Atividade de Procedimento Armazenado](data-factory-stored-proc-activity.md) para invocar um procedimento armazenado de um pipeline do Data Factory.

### <a name="linked-service"></a>Serviço vinculado
Para definir um serviço vinculado do SQL Data Warehouse do Azure, defina o **type** do serviço vinculado para **AzureSqlDW** e especifique as propriedades a seguir na seção **typeProperties**:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| connectionString |Especifique as informações necessárias para se conectar à instância do SQL Data Warehouse do Azure para a propriedade connectionString. |Sim |

#### <a name="json-example"></a>Exemplo de JSON

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
    }
}
```

Para obter mais informações, consulte o artigo [Conector do SQL Data Warehouse do Azure](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties).

## <a name="sql-server"></a>SQL Server
Você pode criar um serviço vinculado do SQL Server e usá-lo com a [Atividade de Procedimento Armazenado](data-factory-stored-proc-activity.md) para invocar um procedimento armazenado de um pipeline do Data Factory.

### <a name="linked-service"></a>Serviço vinculado
Você cria um serviço vinculado do tipo **OnPremisesSqlServer** para vincular um banco de dados do SQL Server local a um data factory. A tabela a seguir fornece a descrição para elementos JSON específicos para o serviço vinculado do SQL Server local.

A tabela a seguir fornece a descrição para elementos JSON específicos para o serviço vinculado do SQL Server.

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| type |A propriedade de tipo deve ser configurada como: **OnPremisesSqlServer**. |Sim |
| connectionString |Especifique as informações de connectionString necessárias para conexão com o banco de dados do SQL Server local usando a autenticação do SQL ou então a autenticação do Windows. |Sim |
| gatewayName |O nome do gateway que o serviço Data Factory deve usar para se conectar ao banco de dados do SQL Server local. |Sim |
| Nome de Usuário |Especifique o nome de usuário se você estiver usando a Autenticação do Windows. Exemplo: **domainname\\username**. |Não |
| password |Especifique a senha da conta de usuário que você especificou para o nome de usuário. |Não |

Você pode criptografar as credenciais usando o **New-AzDataFactoryEncryptValue** cmdlet e usá-los na cadeia de conexão, conforme mostrado no exemplo a seguir (**EncryptedCredential** propriedade):

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```


#### <a name="example-json-for-using-sql-authentication"></a>Exemplo: JSON para usar Autenticação SQL

```json
{
    "name": "MyOnPremisesSQLDB",
    "properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```
#### <a name="example-json-for-using-windows-authentication"></a>Exemplo: JSON para usar Autenticação Windows

Se o nome de usuário e a senha forem especificados, o gateway os usará para representar a conta de usuário especificada para se conectar ao banco de dados SQL Server local. Caso contrário, o gateway se conectará ao SQL Server diretamente com o contexto de segurança do Gateway (a sua conta de inicialização).

```json
{
    "Name": " MyOnPremisesSQLDB",
    "Properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "ConnectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=True;",
            "username": "<domain\\username>",
            "password": "<password>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

Para obter mais informações, consulte o artigo [Conector de SQL Server](data-factory-sqlserver-connector.md#linked-service-properties).

## <a name="data-transformation-activities"></a>ATIVIDADES DE TRANSFORMAÇÃO DE DADOS

Atividade | DESCRIÇÃO
-------- | -----------
[Atividade de Hive do HDInsight](#hdinsight-hive-activity) | A atividade de Hive do HDInsight em um pipeline de Data Factory executa consultas de Hive em seu próprio cluster ou no cluster sob demanda do HDInsight baseado em Windows/Linux.
[Atividade de Pig do HDInsight](#hdinsight-pig-activity) | A atividade de Pig do HDInsight em um pipeline de Data Factory executa consultas de Pig em seu próprio cluster ou no cluster sob demanda do HDInsight baseado em Windows/Linux.
[Atividade de MapReduce do HDInsight](#hdinsight-mapreduce-activity) | A atividade de MapReduce do HDInsight em um pipeline do Data Factory executa programas MapReduce no seu próprio cluster HDInsight baseado em Windows/Linux, ou em um sob demanda.
[Atividade de Streaming do HDInsight](#hdinsight-streaming-activity) | A Atividade de Streaming do HDInsight em um pipeline do Data Factory executa programas de Transmissão do Hadoop em seu próprio ou cluster HDInsight sob demanda baseado no Windows/Linux.
[Atividade do HDInsight Spark](#hdinsight-spark-activity) | A atividade do HDInsight Spark em um pipeline do Data Factory executa programas do Spark em seu próprio cluster HDInsight.
[Atividade de Execução em Lote do Machine Learning](#machine-learning-batch-execution-activity) | O Azure Data Factory permite que você crie facilmente pipelines que usam o serviço Web do Azure Machine Learning publicado para a análise preditiva. Usando a Atividade de Execução em Lote em um pipeline do Azure Data Factory, você pode invocar um serviço Web do Machine Learning para fazer previsões sobre dados em lote.
[Atividade do Recurso de Atualização do Machine Learning](#machine-learning-update-resource-activity) | Ao longo do tempo, os modelos de previsão nos testes de pontuação do Machine Learning precisam ser readaptados usando novos conjuntos de dados de entrada. Depois de concluir o novo treinamento, você deseja atualizar o serviço Web de pontuação com o modelo do Machine Learning readaptado. Você pode usar a Atividade de Recurso de Atualização para atualizar o serviço Web com o modelo recém-adaptado.
[Atividade de Procedimento Armazenado](#stored-procedure-activity) | Use a atividade Procedimento Armazenado em um pipeline do Data Factory para invocar um procedimento armazenado em um dos seguintes armazenamentos de dados: Banco de Dados SQL do Azure, SQL Data Warehouse do Azure, Banco de Dados do SQL Server em sua empresa ou uma VM do Azure.
[Atividade de U-SQL do Data Lake Analytics](#data-lake-analytics-u-sql-activity) | A atividade de U-SQL do Data Lake Analytics executa um script U-SQL em um cluster do Azure Data Lake Analytics.
[Atividade personalizada do .NET](#net-custom-activity) | Se precisar transformar dados de uma maneira que não tenha suporte do Data Factory, você poderá criar uma atividade personalizada com sua própria lógica de processamento de dados e usar a atividade no pipeline. Você pode configurar a atividade personalizada do .NET para que seja executada usando um serviço de Lote do Azure ou um cluster do Azure HDInsight.


## <a name="hdinsight-hive-activity"></a>Atividade de Hive do HDInsight
Você pode especificar as seguintes propriedades em uma definição de JSON de atividade do Hive. A propriedade de tipo para a atividade deve ser: **HDInsightHive**. Você deve primeiro criar um serviço vinculado do HDInsight e especificar o nome dele como um valor para a propriedade **linkedServiceName**. As propriedades a seguir possuem suporte na seção **typeProperties** quando você define o tipo de atividade para HDInsightHive:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| script |Especifique o script de Hive embutido |Não |
| scriptPath |Armazene o script de Hive em um armazenamento de blob do Azure e forneça o caminho para o arquivo. Use a propriedade 'script' ou 'scriptPath'. As duas não podem ser usadas juntas. O nome do arquivo diferencia maiúsculas de minúsculas. |Não |
| defines |Especifique parâmetros como pares chave/valor para referenciar dentro do script de Hive usando 'hiveconf' |Não |

Essas propriedades de tipo são específicas para a atividade de Hive. Outras propriedades (fora da seção typeProperties) possuem suporte para todas as atividades.

### <a name="json-example"></a>Exemplo de JSON
O JSON a seguir define uma atividade de Hive do HDInsight em um pipeline.

```json
{
    "name": "Hive Activity",
    "description": "description",
    "type": "HDInsightHive",
    "inputs": [
      {
        "name": "input tables"
      }
    ],
    "outputs": [
      {
        "name": "output tables"
      }
    ],
    "linkedServiceName": "MyHDInsightLinkedService",
    "typeProperties": {
      "script": "Hive script",
      "scriptPath": "<pathtotheHivescriptfileinAzureblobstorage>",
      "defines": {
        "param1": "param1Value"
      }
    },
   "scheduler": {
      "frequency": "Day",
      "interval": 1
    }
}
```

Para saber mais, consulte o artigo [Atividade de Hive](data-factory-hive-activity.md).

## <a name="hdinsight-pig-activity"></a>Atividade de Pig do HDInsight
Você pode especificar as seguintes propriedades em uma definição de JSON de atividade de Pig. A propriedade de tipo para a atividade deve ser: **HDInsightPig**. Você deve primeiro criar um serviço vinculado do HDInsight e especificar o nome dele como um valor para a propriedade **linkedServiceName**. As propriedades a seguir possuem suporte na seção **typeProperties** quando você define o tipo de atividade para HDInsightPig:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| script |Especificar o script de Pig embutido |Não |
| scriptPath |Armazenar o script de Pig em um armazenamento de blob do Azure e fornecer o caminho para o arquivo. Use a propriedade 'script' ou 'scriptPath'. As duas não podem ser usadas juntas. O nome do arquivo diferencia maiúsculas de minúsculas. |Não |
| defines |Especificar parâmetros como pares chave/valor para referenciar dentro do script de Pig |Não |

Essas propriedades de tipo são específicas para a atividade de Pig. Outras propriedades (fora da seção typeProperties) possuem suporte para todas as atividades.

### <a name="json-example"></a>Exemplo de JSON

```json
{
    "name": "HiveActivitySamplePipeline",
      "properties": {
    "activities": [
        {
            "name": "Pig Activity",
            "description": "description",
            "type": "HDInsightPig",
            "inputs": [
                  {
                    "name": "input tables"
                  }
            ],
            "outputs": [
                  {
                    "name": "output tables"
                  }
            ],
            "linkedServiceName": "MyHDInsightLinkedService",
            "typeProperties": {
                  "script": "Pig script",
                  "scriptPath": "<pathtothePigscriptfileinAzureblobstorage>",
                  "defines": {
                    "param1": "param1Value"
                  }
            },
               "scheduler": {
                  "frequency": "Day",
                  "interval": 1
            }
          }
    ]
  }
}
```

Para saber mais, confira o artigo Atividade de Pig.

## <a name="hdinsight-mapreduce-activity"></a>Atividade de MapReduce do HDInsight
Você pode especificar as seguintes propriedades em uma definição de JSON de Atividade MapReduce. A propriedade de tipo para a atividade deve ser: **HDInsightMapReduce**. Você deve primeiro criar um serviço vinculado do HDInsight e especificar o nome dele como um valor para a propriedade **linkedServiceName**. As propriedades a seguir possuem suporte na seção **typeProperties** quando você define o tipo de atividade para HDInsightMapReduce:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| jarLinkedService | Nome do serviço vinculado do Armazenamento do Azure que contém o arquivo JAR. | Sim |
| jarFilePath | Caminho para o arquivo JAR no Armazenamento do Azure. | Sim |
| className | Nome da classe principal no arquivo JAR. | Sim |
| argumentos | Uma lista de argumentos separados por vírgulas para o programa MapReduce. Em tempo de execução, você verá alguns argumentos extras (por exemplo: mapreduce.job.tags) da estrutura MapReduce. Para diferenciar seus argumentos com os argumentos MapReduce, considere usar opção e valor como argumentos, conforme mostrado no exemplo a seguir (- s, --input - output etc... são opções seguidas imediatamente por seus valores) | Não |

### <a name="json-example"></a>Exemplo de JSON

```json
{
    "name": "MahoutMapReduceSamplePipeline",
    "properties": {
        "description": "Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calculates an Item Similarity Matrix to determine the similarity between two items",
        "activities": [
            {
                "type": "HDInsightMapReduce",
                "typeProperties": {
                    "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
                    "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
                    "jarLinkedService": "StorageLinkedService",
                    "arguments": ["-s", "SIMILARITY_LOGLIKELIHOOD", "--input", "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/input", "--output", "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/output/", "--maxSimilaritiesPerItem", "500", "--tempDir", "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/temp/mahout"]
                },
                "inputs": [
                    {
                        "name": "MahoutInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "MahoutOutput"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "MahoutActivity",
                "description": "Custom Map Reduce to generate Mahout result",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-01-03T00:00:00",
        "end": "2017-01-04T00:00:00"
    }
}
```

Para saber mais, consulte o artigo [Atividade MapReduce](data-factory-map-reduce.md).

## <a name="hdinsight-streaming-activity"></a>Atividade de Streaming do HDInsight
Você pode especificar as seguintes propriedades em uma definição de JSON de Atividade de Transmissão do Hadoop. A propriedade de tipo para a atividade deve ser: **HDInsightStreaming**. Você deve primeiro criar um serviço vinculado do HDInsight e especificar o nome dele como um valor para a propriedade **linkedServiceName**. As propriedades a seguir possuem suporte na seção **typeProperties** quando você define o tipo de atividade para HDInsightStreaming:

| Propriedade | DESCRIÇÃO |
| --- | --- |
| mapper | Nome do executável mapeador. No exemplo, cat.exe é o executável do mapeador.|
| reducer | Nome do executável redutor. No exemplo, wc.exe é o executável do redutor. |
| input | Arquivo de entrada (incluindo a localização) do mapeador. No exemplo: `"wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt"`: adfsample é o contêiner de blob, example/data/Gutenberg é a pasta e davinci.txt é o blob. |
| output | Arquivo de saída (incluindo a localização) do redutor. A saída do trabalho de Transmissão do Hadoop é gravada no local especificado para essa propriedade. |
| filePaths | Caminhos para os executáveis do mapeador e do redutor. No exemplo: "adfsample/example/apps/wc.exe", adfsample é o contêiner de blob, example/apps é a pasta e wc.exe é o executável. |
| fileLinkedService | Serviço vinculado do Armazenamento do Azure que representa o armazenamento do Azure que contém os arquivos especificados na seção filePaths. |
| arguments | Uma lista de argumentos separados por vírgulas para o programa MapReduce. Em tempo de execução, você verá alguns argumentos extras (por exemplo: mapreduce.job.tags) da estrutura MapReduce. Para diferenciar seus argumentos com os argumentos MapReduce, considere usar opção e valor como argumentos, conforme mostrado no exemplo a seguir (- s, --input - output etc... são opções seguidas imediatamente por seus valores) |
| getDebugInfo | Um elemento opcional. Quando ela é definida como Falha, os logs são baixados somente em caso de falha de execução. Quando ela é definida como Todos, os logs sempre são baixados, não importa o status de execução. |

> [!NOTE]
> Você deve especificar um conjunto de dados de saída da Atividade de Transmissão do Hadoop para a propriedade **outputs** . Esse conjunto de dados é apenas um conjunto fictício exigido para direcionar o agendamento de pipeline (a cada hora, diariamente, etc.). Se a atividade não aceita uma entrada, você pode ignorar a especificação de um conjunto de dados de entrada para a atividade para a propriedade **inputs**.

## <a name="json-example"></a>Exemplo de JSON

```json
{
    "name": "HadoopStreamingPipeline",
    "properties": {
        "description": "Hadoop Streaming Demo",
        "activities": [
            {
                "type": "HDInsightStreaming",
                "typeProperties": {
                    "mapper": "cat.exe",
                    "reducer": "wc.exe",
                    "input": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                    "output": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                    "filePaths": ["<nameofthecluster>/example/apps/wc.exe","<nameofthecluster>/example/apps/cat.exe"],
                    "fileLinkedService": "StorageLinkedService",
                    "getDebugInfo": "Failure"
                },
                "outputs": [
                    {
                        "name": "StreamingOutputDataset"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "RunHadoopStreamingJob",
                "description": "Run a Hadoop streaming job",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2014-01-04T00:00:00",
        "end": "2014-01-05T00:00:00"
    }
}
```

Para saber mais, consulte o artigo [Atividade de Transmissão do Hadoop](data-factory-hadoop-streaming-activity.md).

## <a name="hdinsight-spark-activity"></a>Atividade do HDInsight Spark
Você pode especificar as seguintes propriedades em uma definição de JSON de Atividade de Spark. A propriedade de tipo para a atividade deve ser: **HDInsightSpark**. Você deve primeiro criar um serviço vinculado do HDInsight e especificar o nome dele como um valor para a propriedade **linkedServiceName**. As propriedades a seguir possuem suporte na seção **typeProperties** quando você define o tipo de atividade para HDInsightSpark:

| Propriedade | DESCRIÇÃO | Obrigatório |
| -------- | ----------- | -------- |
| rootPath | O contêiner de Blob do Azure e a pasta que contém o arquivo Spark. O nome do arquivo diferencia maiúsculas de minúsculas. | Sim |
| entryFilePath | Caminho relativo à pasta raiz do código/pacote Spark. | Sim |
| className | Classe principal de Java/Spark do aplicativo | Não |
| arguments | Uma lista de argumentos de linha de comando para o programa Spark. | Não |
| proxyUser | A conta de usuário a ser representada para execução do programa Spark | Não |
| sparkConfig | Propriedades de configuração do Spark. | Não |
| getDebugInfo | Especifica quando os arquivos de log do Spark são copiados no armazenamento do Azure usado pelo cluster HDInsight (ou) especificado por sparkJobLinkedService. Valores permitidos: Nenhum, Sempre ou Falha. Valor padrão: nenhuma. | Não |
| sparkJobLinkedService | O serviço vinculado ao Armazenamento do Azure que contém o arquivo de trabalho, dependências e os logs do Spark.  Se você não especificar um valor para essa propriedade, o armazenamento associado ao cluster HDInsight será usado. | Não |

### <a name="json-example"></a>Exemplo de JSON

```json
{
    "name": "SparkPipeline",
    "properties": {
        "activities": [
            {
                "type": "HDInsightSpark",
                "typeProperties": {
                    "rootPath": "adfspark\\pyFiles",
                    "entryFilePath": "test.py",
                    "getDebugInfo": "Always"
                },
                "outputs": [
                    {
                        "name": "OutputDataset"
                    }
                ],
                "name": "MySparkActivity",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-02-05T00:00:00",
        "end": "2017-02-06T00:00:00"
    }
}
```
Observe os seguintes pontos:

- A propriedade **type** é definida como **HDInsightSpark**.
- O **rootPath** é definido como **adfspark\\pyFiles**, onde adfspark é o contêiner de Blob do Azure e pyFiles é a pasta de arquivos nesse contêiner. Neste exemplo, o Armazenamento de Blobs do Azure é aquele que está associado ao cluster Spark. Você pode carregar o arquivo em um Armazenamento do Azure diferente. Se você fizer isso, crie um serviço vinculado do Armazenamento do Azure para vincular essa conta de armazenamento ao data factory. Em seguida, especifique o nome do serviço vinculado como um valor para a propriedade **sparkJobLinkedService**. Confira Propriedades de Atividade Spark para obter detalhes sobre essa propriedade e outras propriedades às quais a atividade Spark oferece suporte.
- O **entryFilePath** é definido como **test.py**, que é o arquivo Python.
- A propriedade **getDebugInfo** é definida como **Always**, o que significa que os arquivos de log são gerados sempre (sucesso ou falha).

    > [!IMPORTANT]
    > É recomendável que você não defina essa propriedade como Always em um ambiente de produção a menos que você esteja solucionando um problema.
- A seção **outputs** possui um conjunto de dados de saída. Você deve especificar um conjunto de dados de saída mesmo que o programa spark não produza nenhuma saída. O conjunto de dados de saída orienta o agendamento para o pipeline (por hora, diariamente, etc.).

Para obter mais informações sobre a atividade, consulte o artigo [Atividade Spark](data-factory-spark.md).

## <a name="machine-learning-batch-execution-activity"></a>Atividade de Execução em Lote de Machine Learning
Você pode especificar as propriedades a seguir em uma definição de JSON de atividade de execução de lote do Azure Machine Learning studio. A propriedade de tipo para a atividade deve ser: **AzureMLBatchExecution**. Você deve criar uma máquina do Azure pela primeira vez o serviço vinculado de aprendizado e especifique o nome dele como um valor para o **linkedServiceName** propriedade. As propriedades a seguir possuem suporte na seção **typeProperties** quando você define o tipo de atividade para AzureMLBatchExecution:

Propriedade | DESCRIÇÃO | Obrigatório
-------- | ----------- | --------
webServiceInput | O conjunto de dados a ser aprovado como uma entrada para o serviço Web do Azure Machine Learning Studio. Esse conjunto de dados também deve ser incluído nas entradas para a atividade. |Use webServiceInput ou webServiceInputs. |
webServiceInputs | Especifica conjuntos de dados a serem aprovados como entradas para o serviço Web do Azure Machine Learning Studio. Se o serviço Web receber várias entradas, use a propriedade webServiceInputs em vez de usar a propriedade webServiceInput. Os conjuntos de dados referenciados por **webServiceInputs** também devem ser incluídos nas **entradas** da Atividade. | Use webServiceInput ou webServiceInputs. |
webServiceOutputs | Os conjuntos de dados atribuídos como saídas para o serviço Web do Azure Machine Learning Studio. O serviço Web retorna dados de saída neste conjunto de dados. | Sim |
globalParameters | Especifica valores para parâmetros de serviço Web nesta seção. | Não |

### <a name="json-example"></a>Exemplo de JSON
Neste exemplo, a atividade possui o conjunto de dados **MLSqlInput** como entrada e **MLSqlOutput** como a saída. O **MLSqlInput** é passado como uma entrada para o serviço Web usando a propriedade JSON **webServiceInput**. O **MLSqlOutput** é passado como uma entrada para o serviço Web usando a propriedade JSON **webServiceOutputs**.

```json
{
   "name": "MLWithSqlReaderSqlWriter",
   "properties": {
      "description": "Azure ML model with sql azure reader/writer",
      "activities": [{
         "name": "MLSqlReaderSqlWriterActivity",
         "type": "AzureMLBatchExecution",
         "description": "test",
         "inputs": [ { "name": "MLSqlInput" }],
         "outputs": [ { "name": "MLSqlOutput" } ],
         "linkedServiceName": "MLSqlReaderSqlWriterDecisionTreeModel",
         "typeProperties":
         {
            "webServiceInput": "MLSqlInput",
            "webServiceOutputs": {
               "output1": "MLSqlOutput"
            },
            "globalParameters": {
               "Database server name": "<myserver>.database.windows.net",
               "Database name": "<database>",
               "Server user account name": "<user name>",
               "Server user account password": "<password>"
            }
         },
         "policy": {
            "concurrency": 1,
            "executionPriorityOrder": "NewestFirst",
            "retry": 1,
            "timeout": "02:00:00"
         }
      }],
      "start": "2016-02-13T00:00:00",
       "end": "2016-02-14T00:00:00"
   }
}
```

No exemplo do JSON, o serviço Web do Machine Learning implantado usa um módulo leitor e gravador para ler/gravar dados de/para um Banco de Dados SQL do Azure. Este serviço Web expõe os quatro parâmetros a seguir:  Nome do servidor de banco de dados, Nome do banco de dados, Nome de conta de usuário do servidor e Senha de conta de usuário do servidor.

> [!NOTE]
> Apenas as entradas e saídas da atividade AzureMLBatchExecution podem ser passadas como parâmetros para o serviço Web. Por exemplo, no snippet JSON acima, MLSqlInput é uma entrada para a atividade de AzureMLBatchExecution, que é passada como entrada para o serviço Web através do parâmetro webServiceInput.

## <a name="machine-learning-update-resource-activity"></a>Atividade de Atualização de Recursos do Machine Learning
Você pode especificar as propriedades a seguir em uma definição de JSON de atividade do recurso de atualização do Azure Machine Learning studio. A propriedade de tipo para a atividade deve ser: **AzureMLUpdateResource**. Você deve criar uma máquina do Azure pela primeira vez o serviço vinculado de aprendizado e especifique o nome dele como um valor para o **linkedServiceName** propriedade. As propriedades a seguir possuem suporte na seção **typeProperties** quando você define o tipo de atividade para AzureMLUpdateResource:

Propriedade | DESCRIÇÃO | Obrigatório
-------- | ----------- | --------
trainedModelName | Nome do modelo treinado novamente. | Sim |
trainedModelDatasetName | O conjunto de dados apontando para o arquivo iLearner retornado pela operação de novos treinamentos. | Sim |

### <a name="json-example"></a>Exemplo de JSON
O pipeline tem duas atividades: **AzureMLBatchExecution** e **AzureMLUpdateResource**. A Atividade de Execução em Lote do Azure Machine Learning Studio assume os dados de treinamento como entrada e produz um arquivo iLearner como uma saída. A atividade invoca o serviço Web de treinamento (experimento de treinamento exposto como um serviço Web) com os dados de treinamento de entrada e recebe o arquivo ilearner do serviço Web. O placeholderBlob é apenas um conjunto de dados de saída fictício necessário ao serviço Azure Data Factory para executar o pipeline.


```json
{
    "name": "pipeline",
    "properties": {
        "activities": [
            {
                "name": "retraining",
                "type": "AzureMLBatchExecution",
                "inputs": [
                    {
                        "name": "trainingData"
                    }
                ],
                "outputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "typeProperties": {
                    "webServiceInput": "trainingData",
                    "webServiceOutputs": {
                        "output1": "trainedModelBlob"
                    }
                 },
                "linkedServiceName": "trainingEndpoint",
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "02:00:00"
                }
            },
            {
                "type": "AzureMLUpdateResource",
                "typeProperties": {
                    "trainedModelName": "trained model",
                    "trainedModelDatasetName" :  "trainedModelBlob"
                },
                "inputs": [{ "name": "trainedModelBlob" }],
                "outputs": [{ "name": "placeholderBlob" }],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "AzureML Update Resource",
                "linkedServiceName": "updatableScoringEndpoint2"
            }
        ],
        "start": "2016-02-13T00:00:00",
        "end": "2016-02-14T00:00:00"
    }
}
```

## <a name="data-lake-analytics-u-sql-activity"></a>Atividade do U-SQL da Análise Data Lake
Você pode especificar as seguintes propriedades em uma definição de JSON de atividade de U-SQL. A propriedade de tipo para a atividade deve ser: **DataLakeAnalyticsU-SQL**. Você deve primeiro criar um serviço vinculado do Azure Data Lake Analytics e especificar o nome dele como um valor para a propriedade **linkedServiceName**. As propriedades a seguir possuem suporte na seção **typeProperties** quando você define o tipo de atividade para DataLakeAnalyticsU-SQL:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| scriptPath |Caminho para a pasta que contém o script U-SQL. O nome do arquivo diferencia maiúsculas de minúsculas. |Não (se você usar o script) |
| scriptLinkedService |Serviço vinculado que vincula o armazenamento que contém o script para a fábrica de dados |Não (se você usar o script) |
| script |Especificar script embutido em vez de especificar scriptPath e scriptLinkedService. Por exemplo: "script": "CREATE DATABASE test". |Não (se você usar scriptPath e scriptLinkedService) |
| degreeOfParallelism |O número máximo de nós usados simultaneamente para executar o trabalho. |Não |
| prioridade |Determina quais trabalhos de todos os que estão na fila devem ser selecionados para serem executados primeiro. Quanto menor o número, maior a prioridade. |Não |
| parameters |Parâmetros do script U-SQL |Não |

### <a name="json-example"></a>Exemplo de JSON

```json
{
    "name": "ComputeEventsByRegionPipeline",
    "properties": {
        "description": "This pipeline computes events for en-gb locale and date less than Feb 19, 2012.",
        "activities":
        [
            {
                "type": "DataLakeAnalyticsU-SQL",
                "typeProperties": {
                    "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
                    "scriptLinkedService": "StorageLinkedService",
                    "degreeOfParallelism": 3,
                    "priority": 100,
                    "parameters": {
                        "in": "/datalake/input/SearchLog.tsv",
                        "out": "/datalake/output/Result.tsv"
                    }
                },
                "inputs": [
                    {
                        "name": "DataLakeTable"
                    }
                ],
                "outputs":
                [
                    {
                        "name": "EventsByRegionTable"
                    }
                ],
                "policy": {
                    "timeout": "06:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "EventsByRegion",
                "linkedServiceName": "AzureDataLakeAnalyticsLinkedService"
            }
        ],
        "start": "2015-08-08T00:00:00",
        "end": "2015-08-08T01:00:00",
        "isPaused": false
    }
}
```

Para saber mais, consulte [Atividade de U-SQL no Data Lake Analytics](data-factory-usql-activity.md).

## <a name="stored-procedure-activity"></a>Atividade de Procedimento Armazenado
Você pode especificar as seguintes propriedades em uma definição de JSON de Atividade de Procedimento de Armazenado. A propriedade de tipo para a atividade deve ser: **SqlServerStoredProcedure**. Você deve primeiro criar um dos serviços vinculados a seguir e especificar o nome do serviço vinculado como um valor para a propriedade **linkedServiceName**.

- SQL Server
- Banco de Dados SQL do Azure
- SQL Data Warehouse do Azure

As propriedades a seguir possuem suporte na seção **typeProperties** quando você define o tipo de atividade para SqlServerStoredProcedure:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| storedProcedureName |Especifique o nome do procedimento armazenado no banco de dados SQL do Azure ou SQL Data Warehouse do Azure que é representado pelo serviço vinculado utilizado pela tabela de saída. |Sim |
| storedProcedureParameters |Especifique valores para parâmetros de procedimento armazenado. Se você precisar passar null para um parâmetro, use a sintaxe: "param1": null (todas as letras minúsculas). Veja o exemplo a seguir para saber mais sobre como usar essa propriedade. |Não |

Se você especificar um conjunto de dados de entrada, ele deverá estar disponível (no status 'Pronto') para a atividade de procedimento armazenado a ser executada. O conjunto de dados de entrada não pode ser consumido no procedimento armazenado como um parâmetro. Ele só é usado para verificar a dependência antes de iniciar a atividade de procedimento armazenado. Você deve especificar um conjunto de dados de saída para uma atividade de procedimento armazenado.

O conjunto de dados de saída especifica a **agenda** da atividade de procedimento armazenado (por hora, semana, mês, etc.). O conjunto de dados de saída deve usar um **serviço vinculado** que se refere a um Banco de Dados SQL do Azure, ou SQL Data Warehouse do Azure, ou um Banco de Dados SQL Server no qual você quer que o procedimento armazenado seja executado. O conjunto de dados de saída pode servir como uma maneira de passar o resultado do procedimento armazenado para processamento posterior de outra atividade ([atividades de encadeamento](data-factory-scheduling-and-execution.md##multiple-activities-in-a-pipeline)) no pipeline. No entanto, o Data Factory não trava automaticamente a saída de um procedimento armazenado para esse conjunto de dados. É o procedimento armazenado que grava em uma tabela SQL para a qual o conjunto de dados de saída aponta. Em alguns casos, o conjunto de dados de saída pode ser um **conjunto de dados fictício**, que é usado apenas para especificar o agendamento para execução da atividade de procedimento armazenado.

### <a name="json-example"></a>Exemplo de JSON

```json
{
    "name": "SprocActivitySamplePipeline",
    "properties": {
        "activities": [
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "usp_sample",
                    "storedProcedureParameters": {
                        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
                    }
                },
                "outputs": [{ "name": "sprocsampleout" }],
                "name": "SprocActivitySample"
            }
        ],
         "start": "2016-08-02T00:00:00",
         "end": "2016-08-02T05:00:00",
        "isPaused": false
    }
}
```

Para saber mais, consulte o artigo [Atividade de Procedimento Armazenado](data-factory-stored-proc-activity.md).

## <a name="net-custom-activity"></a>Atividade personalizada do .NET
Você pode especificar as seguintes propriedades em uma definição de JSON de atividade personalizada de .NET. A propriedade de tipo para a atividade deve ser: **DotNetActivity**. Você deve primeiro criar um serviço vinculado do Azure HDInsight ou um serviço vinculado do Lote do Azure e especificar o nome do serviço vinculado como um valor para a propriedade **linkedServiceName**. As propriedades a seguir possuem suporte na seção **typeProperties** quando você define o tipo de atividade para DotNetActivity:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| AssemblyName | Nome do assembly. No exemplo, é: **MyDotnetActivity.dll**. | Sim |
| EntryPoint |Nome da classe que implementa a interface IDotNetActivity. No exemplo, é: **MyDotNetActivityNS.MyDotNetActivity** em que MyDotNetActivityNS é o namespace e MyDotNetActivity é a classe.  | Sim |
| PackageLinkedService | Nome do serviço vinculado do Armazenamento do Azure que aponta para o armazenamento de blobs que contém o arquivo zip da atividade personalizada. No exemplo, é: **AzureStorageLinkedService**.| Sim |
| PackageFile | Nome do arquivo zip. No exemplo, é: **customactivitycontainer/MyDotNetActivity.zip**. | Sim |
| extendedProperties | Propriedades estendidas que você pode definir e passar para o código .NET. Neste exemplo, a variável **SliceStart** é definida para um valor baseado na variável de sistema SliceStart. | Não |

### <a name="json-example"></a>Exemplo de JSON

```json
{
  "name": "ADFTutorialPipelineCustom",
  "properties": {
    "description": "Use custom activity",
    "activities": [
      {
        "Name": "MyDotNetActivity",
        "Type": "DotNetActivity",
        "Inputs": [
          {
            "Name": "InputDataset"
          }
        ],
        "Outputs": [
          {
            "Name": "OutputDataset"
          }
        ],
        "LinkedServiceName": "AzureBatchLinkedService",
        "typeProperties": {
          "AssemblyName": "MyDotNetActivity.dll",
          "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
          "PackageLinkedService": "AzureStorageLinkedService",
          "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
          "extendedProperties": {
            "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
          }
        },
        "Policy": {
          "Concurrency": 2,
          "ExecutionPriorityOrder": "OldestFirst",
          "Retry": 3,
          "Timeout": "00:30:00",
          "Delay": "00:00:00"
        }
      }
    ],
    "start": "2016-11-16T00:00:00",
    "end": "2016-11-16T05:00:00",
    "isPaused": false
  }
}
```

Para saber informações detalhadas, consulte o artigo [Usar atividades personalizadas no Data Factory](data-factory-use-custom-activities.md).

## <a name="next-steps"></a>Próximas etapas
Consulte os seguintes tutoriais:

- [Tutorial: Criar um pipeline com uma atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Tutorial: Criar um pipeline com uma atividade de hive](data-factory-build-your-first-pipeline.md)
