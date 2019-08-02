---
title: Criar conjuntos de dados no Azure Data Factory | Microsoft Docs
description: Saiba como criar conjuntos de dados no Azure Data Factory, com exemplos que usam propriedades como offset e anchorDateTime.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 0614cd24-2ff0-49d3-9301-06052fd4f92a
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: f88d83a851ad878ac9ee9b0195816d2ca35e4c13
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839368"
---
# <a name="datasets-in-azure-data-factory"></a>Conjuntos de dados no Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](data-factory-create-datasets.md)
> * [Versão 2 (versão atual)](../concepts-datasets-linked-services.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte [Conjuntos de dados na V2 ](../concepts-datasets-linked-services.md).

Este artigo descreve o que são conjuntos de dados, como eles são definidos no formato JSON e como são usados em pipelines do Azure Data Factory. Ele fornece detalhes sobre cada seção (por exemplo, estrutura, disponibilidade e política) na definição de JSON do conjunto de dados. O artigo também fornece exemplos de como usar as propriedades **offset**, **anchorDateTime** e **style** em uma definição de JSON do conjunto de dados.

> [!NOTE]
> Se estiver conhecendo o Azure Data Factory agora, consulte [Introdução ao Azure Data Factory](data-factory-introduction.md) para obter uma visão geral. Caso não tenha experiência prática com a criação de data factories, obtenha um melhor entendimento lendo o [tutorial de transformação de dados](data-factory-build-your-first-pipeline.md) e o [tutorial de movimentação de dados](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="overview"></a>Visão geral
Uma fábrica de dados pode ter um ou mais pipelines. Um **pipeline** é um agrupamento lógico de **atividades** que juntas executam uma tarefa. As atividades em um pipeline definem ações para executar em seus dados. Por exemplo, você poderá usar uma atividade de cópia para copiar os dados de um SQL Server local para um armazenamento de Blobs do Azure. Em seguida, poderá usar uma atividade do Hive que executa um script Hive em um cluster HDInsight do Azure a fim de processar dados do armazenamento de Blobs para gerar dados de saída. Por fim, poderá usar uma segunda atividade de cópia para copiar os dados de saída para o SQL Data Warehouse do Azure, no qual as soluções de relatório de BI (business intelligence) são criadas. Para obter mais informações sobre pipelines e atividades, consulte [Pipelines e atividades no Azure Data Factory](data-factory-create-pipelines.md).

Uma atividade pode usar zero ou mais **conjuntos de dados** de entrada e gerar um ou mais conjuntos de dados de saída. Um conjunto de dados de entrada representa a entrada de uma atividade no pipeline e um conjunto de dados de saída representa a saída da atividade. Conjuntos de dados identificam dados em armazenamentos de dados diferentes, como tabelas, arquivos, pastas e documentos. Por exemplo, um conjunto de dados de Blob do Azure especifica o contêiner de blobs e a pasta no armazenamento de Blobs dos quais o pipeline deve ler os dados.

Antes de criar um conjunto de dados, crie um **serviço vinculado** para vincular o armazenamento de dados ao data factory. Serviços vinculados são como cadeias de conexão, que definem as informações de conexão necessárias para o Data Factory para se conectar a recursos externos. Conjuntos de dados identificam dados em armazenamentos de dados vinculados, como tabelas SQL, arquivos, pastas e documentos. Por exemplo, um serviço vinculado do Armazenamento do Azure vincula uma conta de armazenamento ao data factory. Um conjunto de dados de Blob do Azure representa o contêiner de blob e a pasta que contém os blobs de entrada a ser processado.

Veja abaixo um cenário de exemplo. Para copiar dados do armazenamento de Blobs para um banco de dados SQL, crie dois serviços vinculados: Armazenamento do Azure e Banco de Dados SQL do Azure. Em seguida, crie dois conjuntos de dados: O conjunto de dados de Blob do Azure (que se refere ao serviço vinculado do Armazenamento do Azure) e o conjunto de dados de Tabela do SQL do Azure (que se refere ao serviço vinculado do Banco de Dados SQL do Azure). Os serviços vinculados do Armazenamento do Azure e do Banco de Dados SQL do Azure contêm cadeias de conexão que o Data Factory usa em tempo de execução para se conectar ao Armazenamento do Azure e ao Banco de Dados SQL do Azure, respectivamente. O conjunto de dados de Blob do Azure especifica o contêiner de blobs e a pasta de blobs que contém os blobs de entrada no armazenamento de Blobs. O conjunto de dados de Tabela do SQL do Azure especifica a tabela do SQL no banco de dados SQL para o qual os dados serão copiados.

O seguinte diagrama mostra a relação entre pipeline, atividade, conjunto de dados e serviço vinculado no Data Factory:

![Relação entre pipeline, atividade e conjunto de dados, serviços vinculados](media/data-factory-create-datasets/relationship-between-data-factory-entities.png)

## <a name="dataset-json"></a>Conjunto de dados do JSON
Um conjunto de dados no Data Factory é definido no formato JSON da seguinte maneira:

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "external": "<boolean flag to indicate external data. only for input datasets>",
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
| name |Nome do conjunto de dados. Confira [Azure Data Factory - Regras de nomenclatura](data-factory-naming-rules.md) para ver as regras de nomenclatura. |Sim |ND |
| type |Tipo de conjunto de dados. Especifique um dos tipos compatíveis com o Data Factory (por exemplo: AzureBlob, AzureSqlTable). <br/><br/>Para obter detalhes, consulte [Tipo de conjunto de dados](#Type). |Sim |ND |
| structure |Esquema do conjunto de dados.<br/><br/>Para obter detalhes, consulte [Estrutura de conjunto de dados](#Structure). |Não |ND |
| typeProperties | As propriedades de tipo são diferentes para cada tipo (por exemplo: Blob do Azure, tabela do SQL Azure). Para obter detalhes sobre os tipos com suporte e suas propriedades, consulte [Tipo de conjunto de dados](#Type). |Sim |ND |
| external | Sinalizador booliano para especificar se um conjunto de dados é explicitamente produzido por um pipeline de data factory ou não. Se o conjunto de dados de entrada para uma atividade não é produzido pelo pipeline atual, defina esse sinalizador como true. Defina esse sinalizador como true para o conjunto de dados de entrada da primeira atividade no pipeline.  |Não |false |
| availability | Define a janela de processamento (por exemplo, por hora ou diária) ou o modelo de divisão para a produção de conjunto de dados. Cada unidade de dados consumida e produzida por uma execução de atividade é chamada de uma fatia de dados. Se a disponibilidade de um conjunto de dados de saída é definida como diária (frequência - Dia, intervalo - 1), uma fatia é produzida diariamente. <br/><br/>Para obter detalhes, confira Disponibilidade do conjunto de dados. <br/><br/>Para obter detalhes sobre o modelo de divisão do conjunto de dados, consulte o artigo [Agendamento e execução](data-factory-scheduling-and-execution.md). |Sim |ND |
| policy |Define os critérios ou a condição que as fatias de conjunto de dados devem atender. <br/><br/>Para obter detalhes, consulte a seção [Política do conjunto de dados](#Policy). |Não |ND |

## <a name="dataset-example"></a>Exemplo de conjunto de dados
No exemplo a seguir, o conjunto de dados representa uma tabela chamada **MyTable** em um banco de dados SQL.

```json
{
    "name": "DatasetSample",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties":
        {
            "tableName": "MyTable"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

Observe os seguintes pontos:

* **type** foi definido como AzureSqlTable.
* A propriedade de tipo **tableName** (específica ao tipo AzureSqlTable) foi definida como MyTable.
* **linkedServiceName** se refere a um serviço vinculado do tipo AzureSqlDatabase, que é definido no próximo snippet de JSON.
* **Frequência de disponibilidade** é definida como Dia e **intervalo** como 1. Isso significa que a fatia do conjunto de dados é gerada diariamente.

**AzureSqlLinkedService** é definido da seguinte maneira:

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>@<servername>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30"
        }
    }
}
```

No snippet de JSON anterior:

* **type** foi definido como AzureSqlDatabase.
* A propriedade de tipo **connectionString** especifica informações para se conectar a um banco de dados SQL.

Como você pode ver, o serviço vinculado define como se conectar a um banco de dados SQL. O conjunto de dados define qual tabela é usada como uma entrada e saída para a atividade em um pipeline.

> [!IMPORTANT]
> A menos que um conjunto de dados seja produzido pela pipeline, ele deverá ser marcado como **externo**. Essa configuração geralmente se aplica às entradas da primeira atividade em um pipeline.

## <a name="Type"></a> Tipo de conjunto de dados
O tipo do conjunto de dados depende do armazenamento de dados que você usa. Consulte a tabela a seguir para obter uma lista de armazenamentos de dados com suporte pelo Data Factory. Clique em um armazenamento de dados para saber como criar um serviço vinculado e um conjunto de dados para esse armazenamento de dados.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Armazenamentos de dados com * podem ser locais ou IaaS (infraestrutura como serviço) do Azure. Esses armazenamentos de dados exigem a instalação do [Gateway de Gerenciamento de Dados](data-factory-data-management-gateway.md).

No exemplo na seção anterior, o tipo do conjunto de dados é definido como **AzureSqlTable**. Da mesma forma, para um conjunto de dados de Blob do Azure, o tipo do conjunto de dados é definido como **AzureBlob**, conforme mostrado no seguinte JSON:

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

## <a name="Structure"></a>Estrutura do conjunto de dados
A seção **estrutura** é opcional. Ela define o esquema do conjunto de dados contendo uma coleção de nomes e tipos de dados de colunas. Use a seção de estrutura para fornecer informações de tipo que são usadas para converter tipos e mapear colunas da origem para o destino. No seguinte exemplo, o conjunto de dados tem três colunas: `slicetimestamp`, `projectname` e `pageviews`. Elas são do tipo String, String e Decimal, respectivamente.

```json
structure:
[
    { "name": "slicetimestamp", "type": "String"},
    { "name": "projectname", "type": "String"},
    { "name": "pageviews", "type": "Decimal"}
]
```

Cada coluna da seção Estrutura contém as seguintes propriedades:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| name |Nome da coluna. |Sim |
| type |Tipo de dados da coluna.  |Não |
| culture |Cultura baseada em .NET a ser usada quando o tipo é um tipo .NET `Datetime` ou `Datetimeoffset`. O padrão é `en-us`. |Não |
| format |O formato de cadeia de caracteres a ser usado quando o tipo é um tipo .NET `Datetime` ou `Datetimeoffset`. |Não |

As diretrizes a seguir ajudam você a determinar quando incluir informações de estrutura e o que incluir na seção **estrutura**.

* **Para fontes de dados estruturadas**, especifique a seção de estrutura somente se desejar mapear colunas de origem para colunas do coletor; além disso, seus nomes não são os mesmos. Esse tipo de fonte de dados estruturada armazena informações de esquema de dados e de tipo juntamente com os próprios dados. Exemplos de fontes de dados estruturadas incluem SQL Server, Oracle e tabela do Azure.
  
    Como as informações de tipo já estão disponíveis para fontes de dados estruturadas, não inclua informações de tipo quando incluir a seção de estrutura.
* **Para o esquema em fontes de dados de leitura (especificamente o armazenamento de Blobs)** , você pode optar por armazenar os dados sem armazenar nenhuma informação de tipo ou de esquema juntamente com os dados. Para esses tipos de fontes de dados, inclua a estrutura quando desejar mapear as colunas de origem para as colunas do coletor. Também inclua a estrutura quando o conjunto de dados for uma entrada para uma atividade de cópia e os tipos de dados do conjunto de dados de origem precisarem ser convertidos em tipos nativos para o coletor.
    
    O Data Factory dá suporte aos seguintes valores para fornecer informações de tipo na estrutura: **Int16, Int32, Int64, Single, Double, Decimal, Byte[], Boolean, String, Guid, Datetime, Datetimeoffset e Timespan**. Esses valores são valores de tipo baseados em NET e em conformidade com CLS (Common Language Specification).

O Data Factory executa conversões de tipo automaticamente ao mover dados de um armazenamento de dados de origem para um armazenamento de dados do coletor.

## <a name="dataset-availability"></a>Disponibilidade do conjunto de dados
A seção **disponibilidade** em um conjunto de dados define a janela de processamento (por exemplo, por hora, diária ou semanal) do conjunto de dados. Para obter mais informações sobre janelas de atividades, consulte [Agendamento e execução](data-factory-scheduling-and-execution.md).

A seguinte seção de disponibilidade especifica que o conjunto de dados de saída é gerado a cada hora ou o conjunto de dados de entrada está disponível a cada hora:

```json
"availability":
{
    "frequency": "Hour",
    "interval": 1
}
```

Se o pipeline tem as seguintes horas de início e término:

```json
    "start": "2016-08-25T00:00:00Z",
    "end": "2016-08-25T05:00:00Z",
```

O conjunto de dados de saída é gerado de hora em hora, dentro dos horários de início e término do pipeline. Portanto, cinco fatias de conjunto de dados são geradas por esse pipeline, uma para cada janela de atividades (00h à 1h, 1h às 2h, 2h às 3h, 3h às 4h e 4h às 5h).

A tabela a seguir descreve as propriedades que você pode usar na seção de disponibilidade:

| Propriedade | DESCRIÇÃO | Obrigatório | Padrão |
| --- | --- | --- | --- |
| frequency |Especifica a unidade de tempo para a produção da fatia de conjunto de dados.<br/><br/><b>Frequência compatível</b>: Minuto, Hora, Dia, Semana, Mês |Sim |ND |
| interval |Especifica um multiplicador para a frequência.<br/><br/>“Frequência x intervalo” determina a frequência com que a fatia é gerada. Por exemplo, se você precisa que o conjunto de dados seja dividido por hora, defina <b>frequência</b> como <b>Hora</b> e <b>intervalo</b> como <b>1</b>.<br/><br/>Observe que, caso você especifique a **frequência** como **Minuto**, deverá definir o intervalo como não inferior a 15. |Sim |ND |
| style |Especifica se a fatia deve ser gerada no início ou término do intervalo.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul>Se a **frequência** for definida como **Mês** e o **estilo** como **EndOfInterval**, a fatia será gerada no último dia do mês. Se o **estilo** for definido como **StartOfInterval**, a fatia será gerada no primeiro dia do mês.<br/><br/>Se a **frequência** for definida como **Dia** e o **estilo** como **EndOfInterval**, a fatia será gerada na última hora do dia.<br/><br/>Se a **frequência** for definida como **Hora** e o **estilo** como **EndOfInterval**, a fatia será gerada ao final da hora. Por exemplo, para uma fatia do período 13h às 14h, a fatia é gerada às 14h. |Não |EndOfInterval |
| anchorDateTime |Define a posição absoluta no tempo usada pelo agendador para computar limites de fatia do conjunto de dados. <br/><br/>Observe que, se essa propriedade tiver partes de datas que são mais granulares do que a frequência especificada, as partes mais granulares são ignoradas. Por exemplo, se o **intervalo** for **por hora** (frequência: hora e intervalo: 1) e o **AnchorDateTime** contiver **minutos e segundos**, as partes de minutos e segundos de **anchorDateTime** serão ignoradas. |Não |01/01/0001 |
| offset |O período de tempo no qual o início e o término de todas as fatias de conjunto de dados são deslocados. <br/><br/>Observe que, se **anchorDateTime** e **offset** forem especificados, o resultado será um deslocamento combinado. |Não |ND |

### <a name="offset-example"></a>exemplo de deslocamento
Por padrão, as fatias diárias (`"frequency": "Day", "interval": 1`) começam às 00h (meia-noite) em UTC (Tempo Universal Coordenado). Se desejar que a hora de início para hora UTC de 6 horas em vez disso, define o deslocamento, conforme mostrado no snippet a seguir:

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
### <a name="anchordatetime-example"></a>Exemplo de anchorDateTime
No exemplo a seguir, o conjunto de dados é gerado uma vez a cada 23 horas. A primeira fatia começa na hora especificada pela **anchorDateTime**, que é definida como `2017-04-19T08:00:00` (UTC).

```json
"availability":
{
    "frequency": "Hour",
    "interval": 23,
    "anchorDateTime":"2017-04-19T08:00:00"
}
```

### <a name="offsetstyle-example"></a>exemplo de deslocamento/estilo
O seguinte conjunto de dados é mensal e é gerado no terceiro de cada mês às 8h (`3.08:00:00`):

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "offset": "3.08:00:00", 
    "style": "StartOfInterval"
}
```

## <a name="Policy"></a>Política de conjunto de dados
A seção **política** na definição do conjunto de dados define os critérios ou a condição que as divisões de conjunto de dados devem atender.

### <a name="validation-policies"></a>Políticas de validação
| Nome da política | DESCRIÇÃO | Aplicado a | Obrigatório | Padrão |
| --- | --- | --- | --- | --- |
| minimumSizeMB |Valida se os dados em um **armazenamento de Blobs do Azure** atendem aos requisitos de tamanho mínimo (em megabytes). |Armazenamento de Blobs do Azure |Não |ND |
| minimumRows |Valida que os dados em um **Banco de Dados SQL do Azure** ou uma **tabela do Azure** contêm o número mínimo de linhas. |<ul><li>Banco de Dados SQL do Azure</li><li>Tabela do Azure</li></ul> |Não |ND |

#### <a name="examples"></a>Exemplos
**minimumSizeMB:**

```json
"policy":

{
    "validation":
    {
        "minimumSizeMB": 10.0
    }
}
```

**minimumRows:**

```json
"policy":
{
    "validation":
    {
        "minimumRows": 100
    }
}
```

### <a name="external-datasets"></a>Conjuntos de dados externos
Conjuntos de dados externos são aqueles que não são produzidos por um pipeline em execução na data factory. Se o conjunto de dados estiver marcado como **external**, a política **ExternalData** poderá ser definida para influenciar o comportamento da disponibilidade da divisão do conjunto de dados.

A menos que um conjunto de dados seja gerado pelo Data Factory, ele deverá ser marcado como **externo**. Essa configuração geralmente se aplica às entradas da primeira atividade em um pipeline, a menos que um encadeamento de atividade ou de pipeline seja usado.

| NOME | DESCRIÇÃO | Obrigatório | Valor padrão |
| --- | --- | --- | --- |
| dataDelay |O tempo de atraso da verificação da disponibilidade dos dados externos da divisão especificada. Por exemplo, é possível atrasar uma verificação por hora usando essa configuração.<br/><br/>A configuração se aplica somente à hora atual. Por exemplo, se agora forem 13hs e se esse valor for 10 minutos, a validação começará às 13:10hs.<br/><br/>Observe que essa configuração não afeta as fatias do passado. Fatias com **Hora de Término da Fatia** + **dataDelay** < **Agora** são processadas sem atrasos.<br/><br/>Horas maiores que 23h59 horas devem ser especificadas com o formato `day.hours:minutes:seconds`. Por exemplo, para especificar 24 horas, não use 24:00:00. Em vez disso, use 1.00:00:00. Se você usar 24:00:00, isso será tratado como 24 dias (24.00:00:00). Para 1 dia e 4 horas, especifique 1:04:00:00. |Não |0 |
| retryInterval |O tempo de espera entre uma falha e a próxima tentativa. Essa configuração se aplica à hora atual. Se a tentativa anterior falhou, a próxima tentativa ocorrerá após o período de **retryInterval**. <br/><br/>Se agora for 1:00 PM, iniciaremos a primeira tentativa. Se a duração para concluir a primeira verificação de validação for 1 minuto e a operação tiver falhado, a próxima repetição será às 13h + 1min (duração) + 1min (intervalo de repetição) = 13h02. <br/><br/>Para fatias no passado, não haverá nenhum atraso. A repetição acontece imediatamente. |Não |00:01:00 (1 minuto) |
| retryTimeout |O tempo limite para cada tentativa de repetição.<br/><br/>Se essa propriedade for definida como 10 minutos, a validação deverá ser concluída em 10 minutos. Se demorar mais de 10 minutos para executar a validação, a repetição atingirá o tempo limite.<br/><br/>Se todas as tentativas para a validação atingirem o tempo limite, a fatia será marcada como **TimedOut**. |Não |00:10:00 (10 minutos) |
| maximumRetry |O número de vezes para verificar a disponibilidade dos dados externos. O valor máximo permitido é 10. |Não |3 |


## <a name="create-datasets"></a>Criar conjuntos de dados
Você pode criar conjuntos de dados usando uma destas ferramentas ou SDKs:

- Assistente de Cópia
- Visual Studio
- PowerShell
- Modelo do Azure Resource Manager
- API REST
- API do .NET

Consulte os seguintes tutoriais para obter instruções passo a passo para criar pipelines e conjuntos de dados usando uma destas ferramentas ou SDKs:

- [Crie um pipeline com uma atividade de transformação de dados](data-factory-build-your-first-pipeline.md)
- [Crie um pipeline com uma atividade de movimentação de dados](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

Depois que um pipeline é criado e implantado, você pode gerenciar e monitorar seus pipelines usando as folhas do portal do Azure ou o aplicativo de Monitoramento e Gerenciamento. Consulte os seguintes tópicos para obter instruções passo a passo:

- [Monitorar e gerenciar pipelines usando as folhas do portal do Azure](data-factory-monitor-manage-pipelines.md)
- [Monitorar e gerenciar pipelines usando o aplicativo de Monitoramento e Gerenciamento](data-factory-monitor-manage-app.md)

## <a name="scoped-datasets"></a>Conjuntos de dados com escopo
Você pode criar conjuntos de dados que estão no escopo de um pipeline usando a propriedade **datasets** . Esses conjuntos de dados só podem ser usados por atividades dentro deste pipeline, não por atividades em outros pipelines. O exemplo a seguir define um pipeline com dois conjuntos de dados (InputDataset-rdc e OutputDataset-rdc) a serem usados no pipeline.

> [!IMPORTANT]
> Há suporte apenas para conjuntos de dados com escopo com pipelines avulsos (em que **pipelineMode** é definido como **OneTime**). Confira [Pipeline avulso](data-factory-create-pipelines.md#onetime-pipeline) para obter detalhes.
>
>

```json
{
    "name": "CopyPipeline-rdc",
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
                        "name": "InputDataset-rdc"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataset-rdc"
                    }
                ],
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1,
                    "style": "StartOfInterval"
                },
                "name": "CopyActivity-0"
            }
        ],
        "start": "2016-02-28T00:00:00Z",
        "end": "2016-02-28T00:00:00Z",
        "isPaused": false,
        "pipelineMode": "OneTime",
        "expirationTime": "15.00:00:00",
        "datasets": [
            {
                "name": "InputDataset-rdc",
                "properties": {
                    "type": "AzureBlob",
                    "linkedServiceName": "InputLinkedService-rdc",
                    "typeProperties": {
                        "fileName": "emp.txt",
                        "folderPath": "adftutorial/input",
                        "format": {
                            "type": "TextFormat",
                            "rowDelimiter": "\n",
                            "columnDelimiter": ","
                        }
                    },
                    "availability": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "external": true,
                    "policy": {}
                }
            },
            {
                "name": "OutputDataset-rdc",
                "properties": {
                    "type": "AzureBlob",
                    "linkedServiceName": "OutputLinkedService-rdc",
                    "typeProperties": {
                        "fileName": "emp.txt",
                        "folderPath": "adftutorial/output",
                        "format": {
                            "type": "TextFormat",
                            "rowDelimiter": "\n",
                            "columnDelimiter": ","
                        }
                    },
                    "availability": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "external": false,
                    "policy": {}
                }
            }
        ]
    }
}
```

## <a name="next-steps"></a>Próximas etapas
- Para obter mais informações sobre pipelines, consulte [Criar pipelines](data-factory-create-pipelines.md).
- Para obter mais informações sobre como os pipelines são agendados e executados, consulte [Agendamento e execução no Azure Data Factory](data-factory-scheduling-and-execution.md).
