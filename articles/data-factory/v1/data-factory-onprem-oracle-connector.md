---
title: Copiar dados para dentro e fora do Oracle usando o Data Factory | Microsoft Docs
description: "Aprenda a copiar dados de/para o banco de dados da Oracle que está no local usando o Azure Data Factory."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 3c20aa95-a8a1-4aae-9180-a6a16d64a109
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/04/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: a06f328eba83fda6cf12a7b81f2ab4dfcd95ec3a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="copy-data-tofrom-on-premises-oracle-using-azure-data-factory"></a>Copiar dados para dentro e fora do Oracle local usando o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 – já disponível](data-factory-onprem-oracle-connector.md)
> * [Versão 2 – Versão prévia](../connector-oracle.md)

> [!NOTE]
> Este artigo se aplica à versão 1 do Data Factory, que está com GA (disponibilidade geral). Se você estiver usando a versão 2 do serviço do Data Factory, que está em versão prévia, consulte [Oracle connector in V2](../connector-oracle.md) (Conector do Oracle na V2).


Esse artigo explica como usar a Atividade de Cópia no Azure Data Factory para mover dados para/de um banco de dados do Oracle local. Ele se baseia no artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md), que apresenta uma visão geral da movimentação de dados com a atividade de cópia.

## <a name="supported-scenarios"></a>Cenários com suporte
Você pode copiar dados **de um banco de dados Oracle** para os seguintes armazenamentos de dados:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Você pode copiar dados dos seguintes armazenamentos de dados **para um banco de dados Oracle**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="prerequisites"></a>Pré-requisitos
O Data Factory dá suporte à conexão com fontes Oracle locais usando o Gateway de Gerenciamento de Dados. Veja o artigo [Gateway de gerenciamento de dados](data-factory-data-management-gateway.md) para saber mais sobre o Gateway de Gerenciamento de Dados e o artigo [Mover dados entre fontes locais e a nuvem](data-factory-move-data-between-onprem-and-cloud.md) para obter instruções passo a passo sobre como configurar o gateway de um pipeline de dados para mover dados.

O gateway é requerido mesmo que o banco de dados Oracle esteja hospedado em uma VM IaaS do Azure. Você pode instalar o gateway na mesma VM IaaS do armazenamento de dados ou em uma VM diferente, desde que o gateway possa conectar o banco de dados.

> [!NOTE]
> Consulte [Solucionar problemas de gateway](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) para ver dicas sobre como solucionar os problemas relacionados à conexão/gateway.

## <a name="supported-versions-and-installation"></a>Instalação e versões com suporte
Este conector Oracle dá suporte a duas versões de drivers:

- **Driver da Microsoft para Oracle (recomendado)**: iniciando do gateway de gerenciamento de dados versão 2.7, um driver da Microsoft para Oracle é automaticamente instalado junto com o gateway, de modo que você não precisa manipular adicionalmente o driver para estabelecer a conectividade para o Oracle e você também pode ter melhor desempenho de cópia usando este driver. Há suporte para as versões abaixo de bancos de dados Oracle:
    - Oracle 12c R1 (12.1)
    - Oracle 11g R1, R2 (11.1, 11.2)
    - Oracle 10g R1, R2 (10.1, 10.2)
    - Oracle 9i R1, R2 (9.0.1, 9.2)
    - Oracle 8i R3 (8.1.7)

> [!IMPORTANT]
> Atualmente o driver da Microsoft para Oracle suporta apenas copiando dados do Oracle, mas não gravar no Oracle. E observe que a capacidade de conexão de teste na guia Diagnóstico de Gateway de gerenciamento de dados não oferece suporte a este driver. Como alternativa, você pode usar o Assistente para copiar para validar a conectividade.
>

- **Provedor de dados Oracle para .NET:** também é possível usar o provedor de dados Oracle para copiar dados de/para Oracle. Esse componente está incluído nos [Componentes de Acesso a Dados do Oracle para Windows](http://www.oracle.com/technetwork/topics/dotnet/downloads/). Instale a versão adequada (32/64 bits) no computador em que o gateway está instalado. [O Oracle Data Provider .NET 12.1](http://docs.oracle.com/database/121/ODPNT/InstallSystemRequirements.htm#ODPNT149) pode acessar o Oracle Database 10g Release 2 ou posterior.

    Se você escolher "Instalação de XCopy", siga as etapas no readme.htm. É recomendável escolher o instalador com IU (não XCopy).

    Depois de instalar o provedor, **reinicie** o serviço de Host do Gateway de Gerenciamento de Dados no seu computador usando o miniaplicativo Serviços (ou) o Gerenciador de Configuração do Gateway de Gerenciamento de Dados.  

Se você usar o assistente de cópia para criar o pipeline de cópia, o tipo de driver será determinado automaticamente. O driver da Microsoft será usado por padrão, a menos que sua versão do gateway seja menor do que 2.7 ou você escolher o Oracle como um coletor.

## <a name="getting-started"></a>Introdução
Você pode criar um pipeline com atividade de cópia que move dados de/para um banco de dados Oracle local por meio de ferramentas/APIs diferentes.

A maneira mais fácil de criar um pipeline é usar o **Assistente de Cópia**. Confira [Tutorial: Criar um pipeline usando o Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md) para ver um breve passo a passo sobre como criar um pipeline usando o Assistente de cópia de dados.

Você também pode usar as seguintes ferramentas para criar um pipeline: **Portal do Azure**, **Visual Studio**, **Azure PowerShell**, **modelo do Azure Resource Manager**, **API .NET** e **API REST**. Confira o [Tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo sobre a criação de um pipeline com uma atividade de cópia.

Ao usar as ferramentas ou APIs, você executa as seguintes etapas para criar um pipeline que move dados de um armazenamento de dados de origem para um armazenamento de dados de coletor:

1. Criar uma **data factory**. Um data factory pode conter um ou mais pipelines. 
2. Criar **serviços vinculados** para vincular repositórios de dados de entrada e saída ao seu data factory. Por exemplo, se você estiver copiando dados de um banco de dados Oracle para um armazenamento de blobs do Azure, crie dois serviços vinculados para vincular seu banco de dados Oracle e conta de armazenamento do Azure ao data factory. Para propriedades do serviço vinculado específicas do Oracle, consulte a seção [propriedades do serviço vinculado](#linked-service-properties).
3. Criar **conjuntos de dados** para representar dados de entrada e saída para a operação de cópia. No exemplo mencionado na última etapa, você cria um conjunto de dados para especificar a tabela no banco de dados Oracle que contém os dados de entrada. Em seguida, você cria outro conjunto de dados para especificar o contêiner de blob e a pasta que contém os dados copiados do banco de dados Oracle. Para propriedades de conjunto de dados específicas do Oracle, consulte a seção [propriedades do conjunto de dados](#dataset-properties).
4. Criar um **pipeline** com uma atividade de cópia que usa um conjunto de dados como uma entrada e um conjunto de dados como uma saída. No exemplo mencionado anteriormente, você usa OracleSource como fonte e BlobSink como coletor para a atividade de cópia. De modo similar, se você estiver copiando do Armazenamento de Blobs do Azure para o banco de dados Oracle, use BlobSource e OracleSink na atividade de cópia. Para propriedades da atividade de cópia específicas do banco de dados Oracle, consulte a seção [propriedades da atividade de cópia](#copy-activity-properties). Para obter detalhes sobre como usar um armazenamento de dados como uma origem ou um coletor, clique no link na seção anterior para o seu armazenamento de dados. 

Ao usar o assistente, as definições de JSON para essas entidades do Data Factory (serviços vinculados, conjuntos de dados e o pipeline) são automaticamente criadas para você. Ao usar ferramentas/APIs (exceto a API .NET), você define essas entidades do Data Factory usando o formato JSON.  Para obter exemplos com definições de JSON das entidades do Data Factory usadas para copiar dados bidirecionalmente em um banco de dados do Oracle local, confira a seção [Exemplos de JSON](#json-examples-for-copying-data-to-and-from-oracle-database) neste artigo.

As seções que se seguem fornecem detalhes sobre as propriedades JSON que são usadas para definir entidades do Data Factory:

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado
A tabela a seguir fornece a descrição para elementos JSON específicos para o serviço vinculado do Oracle.

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| type |A propriedade do tipo deve ser definida como: **OnPremisesOracle** |Sim |
| driverType | Especifique qual driver a ser usado para copiar dados de/para o banco de dados Oracle. Valores permitidos são **Microsoft** ou **ODP** (padrão). Consulte [suporte para instalação e da versão](#supported-versions-and-installation) seção detalhes do driver. | Não |
| connectionString | Especifique as informações necessárias para se conectar à instância do Banco de Dados Oracle para a propriedade connectionString. | Sim |
| gatewayName | Nome do gateway usado para conectar o servidor Oracle local |Sim |

**Exemplo: usando o driver da Microsoft:**
```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString":"Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Exemplo: usando o driver ODP**

Consulte [este site](https://www.connectionstrings.com/oracle-data-provider-for-net-odp-net/) para conferir os formatos permitidos.

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "connectionString": "Data Source=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=<hostname>)(PORT=<port number>))(CONNECT_DATA=(SERVICE_NAME=<SID>)));
User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de dados, confira o artigo [Criando conjuntos de dados](data-factory-create-datasets.md). As seções, como estrutura, disponibilidade e política de um JSON do conjunto de dados, são parecidas para todos os tipos de conjunto de dados (Oracle, blob do Azure, tabela do Azure etc.).

A seção typeProperties é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no armazenamento de dados. A seção typeProperties do conjunto de dados do tipo OracleTable tem as propriedades a seguir:

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| tableName |Nome da tabela no Banco de Dados Oracle à qual o serviço vinculado se refere. |Não (se **oracleReaderQuery** de **OracleSource** for especificado) |

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia
Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Criando pipelines](data-factory-create-pipelines.md). As propriedades, como nome, descrição, tabelas de entrada e saída, e política, estão disponíveis para todos os tipos de atividades.

> [!NOTE]
> A Atividade de cópia usa apenas uma entrada e produz apenas uma saída.

Por outro lado, as propriedades disponíveis na seção typeProperties da atividade variam de acordo com cada tipo de atividade. Para a atividade de cópia, elas variam de acordo com os tipos de fonte e coletor.

### <a name="oraclesource"></a>OracleSource
Na Atividade de Cópia, quando a fonte é do tipo **OracleSource**, as seguintes propriedades estão disponíveis na seção **typeProperties**:

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| oracleReaderQuery |Utiliza a consulta personalizada para ler os dados. |Cadeia de caracteres de consulta SQL. Por exemplo: select * from MyTable <br/><br/>Se não for especificada, a instrução SQL que é executada é: select * from MyTable |Não (se **tableName** de **dataset** for especificado) |

### <a name="oraclesink"></a>OracleSink
**OracleSink** é compatível com as seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| writeBatchTimeout |Tempo de espera para a operação de inserção em lotes ser concluída antes de atingir o tempo limite. |timespan<br/><br/> Exemplo: "00:30:00" (30 minutos). |Não |
| writeBatchSize |Insere dados na tabela SQL quando o tamanho do buffer atinge writeBatchSize. |Inteiro (número de linhas) |Não (padrão: 100) |
| sqlWriterCleanupScript |Especifique uma consulta da Atividade de Cópia a executar para que os dados de uma fatia específica sejam removidos. |Uma instrução de consulta. |Não |
| sliceIdentifierColumnName |Especifique o nome de coluna para a Atividade de Cópia a ser preenchido com o identificador de fatia gerado automaticamente, que é usado para limpar dados de uma fatia específica quando executado novamente. |Nome de uma coluna com tipo de dados de binário (32). |Não |

## <a name="json-examples-for-copying-data-to-and-from-oracle-database"></a>Exemplos JSON para copiar de dados de e para o banco de dados Oracle
O exemplo a seguir fornece as definições de JSON de exemplo que você pode usar para criar um pipeline usando o [Portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Eles mostram como copiar dados de/para um banco de dados Oracle de/para o Armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados para qualquer um dos coletores declarados [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a Atividade de Cópia no Azure Data Factory.   

## <a name="example-copy-data-from-oracle-to-azure-blob"></a>Exemplo: copiar dados do Oracle para o Blob do Azure

O exemplo tem as seguintes entidades de data factory:

1. Um serviço vinculado do tipo [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties).
2. Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties).
4. Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Um [pipeline](data-factory-create-pipelines.md) com a Atividade de Cópia que usa [OracleSource](data-factory-onprem-oracle-connector.md#copy-activity-properties) como fonte e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) como coletor.

O exemplo copia os dados de uma tabela em um banco de dados Oracle local para um blob por hora. Para obter mais informações sobre as várias propriedades usadas no exemplo, consulte a documentação nas seções após os exemplos.

**Serviço vinculado do Oracle:**

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString":"Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Serviço vinculado do armazenamento de Blob do Azure:**

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
        }
    }
}
```

**Conjunto de dados de entrada do Oracle:**

O exemplo supõe que você criou uma tabela "MyTable" no Oracle e que ela contém uma coluna chamada "timestampcolumn" para dados de série temporal.

Configurar “external”: “true” informa ao serviço Data Factory que o conjunto de dados é externo ao Data Factory e não é produzido por uma atividade no Data Factory.

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
            "anchorDateTime": "2014-02-27T12:00:00",
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

**Conjunto de dados de saída de Blob do Azure:**

Os dados são gravados em um novo blob a cada hora (frequência: hora, intervalo: 1). O caminho de pasta e nome de arquivo para o blob são avaliados dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa as partes ano, mês, dia e horas da hora de início.

```json
{
    "name": "AzureBlobOutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ],
            "format": {
                "type": "TextFormat",
                "columnDelimiter": "\t",
                "rowDelimiter": "\n"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Pipeline com Atividade de cópia:**

O pipeline contém uma Atividade de Cópia configurada para usar os conjuntos de dados de entrada e saída, e está agendada para ser executada por hora. Na definição JSON do pipeline, o tipo **source** está definido como **OracleSource** e o tipo **sink** está definido como **BlobSink**.  A consulta SQL especificada com a propriedade **oracleReaderQuery** seleciona os dados na última hora para copiar.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[  
            {
                "name": "OracletoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": " OracleInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
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
            }
        ]
    }
}
```

## <a name="example-copy-data-from-azure-blob-to-oracle"></a>Exemplo: Copiar dados do Blob do Azure para o Oracle
Este exemplo mostra como copiar dados de um Armazenamento de Blobs do Azure para um banco de dados Oracle local. No entanto, os dados podem ser copiados **diretamente** de qualquer uma das fontes indicadas [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a Atividade de Cópia no Azure Data Factory.  

O exemplo tem as seguintes entidades de data factory:

1. Um serviço vinculado do tipo [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties).
2. Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties).
5. Um [pipeline](data-factory-create-pipelines.md) com a Atividade de Cópia que usa [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) como fonte e [OracleSink](data-factory-onprem-oracle-connector.md#copy-activity-properties) como coletor.

O exemplo copia dados de um blob para uma tabela em um banco de dados Oracle local de hora em hora. Para obter mais informações sobre as várias propriedades usadas no exemplo, consulte a documentação nas seções após os exemplos.

**Serviço vinculado do Oracle:**
```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "connectionString": "Data Source=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=<hostname>)(PORT=<port number>))(CONNECT_DATA=(SERVICE_NAME=<SID>)));
            User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Serviço vinculado do armazenamento de Blob do Azure:**
```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
        }
    }
}
```

**Conjunto de dados de entrada de Blob do Azure**

Os dados são coletados de um novo blob a cada hora (frequência: hora, intervalo: 1). O caminho de pasta e nome de arquivo para o blob são avaliados dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa a parte do ano, mês e dia da hora de início e o nome de arquivo usa a parte da hora de início. A configuração “external”: ”true” informa ao serviço Data Factory que essa é uma tabela externa do data factory e não é produzida por uma atividade no data factory.

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                }
            ],
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            }
        },
        "external": true,
        "availability": {
            "frequency": "Day",
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

**Conjunto de dados de saída Oracle:**

O exemplo pressupõe que você tenha criado uma tabela "MyTable" no Oracle. Crie a tabela no Oracle com o mesmo número de colunas que você espera que o arquivo CSV de Blob contenha. Novas linhas são adicionadas à tabela a cada hora.

```json
{
    "name": "OracleOutput",
    "properties": {
        "type": "OracleTable",
        "linkedServiceName": "OnPremisesOracleLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Day",
            "interval": "1"
        }
    }
}
```

**Pipeline com Atividade de cópia:**

O pipeline contém uma Atividade de Cópia que está configurada para usar os conjuntos de dados de entrada e saída e é agendada para ser executada a cada hora. Na definição JSON do pipeline, o tipo **source** está definido como **BlobSource** e o tipo **sink** está definido como **OracleSink**.  

```json
{  
    "name":"SamplePipeline",
    "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-05T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[  
            {
                "name": "AzureBlobtoOracle",
                "description": "Copy Activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "OracleOutput"
                    }
                ],
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
            }
        ]
    }
}
```


## <a name="troubleshooting-tips"></a>Dicas de solução de problemas
### <a name="problem-1-net-framework-data-provider"></a>Problema 1: Provedor de dados do .NET Framework

Se você vir a seguinte **mensagem de erro**:

    Copy activity met invalid parameters: 'UnknownParameterName', Detailed message: Unable to find the requested .Net Framework Data Provider. It may not be installed”.  

**Possíveis causas:**

1. O provedor de dados .NET Framework para Oracle não foi instalado.
2. O provedor de dados .NET Framework para Oracle foi instalado no .NET Framework 2.0 e não foi encontrado nas pastas .NET Framework 4.0.

**Resolução/Solução Alternativa:**

1. Se você não instalou o Provedor do .NET para o Oracle, [instale-o](http://www.oracle.com/technetwork/topics/dotnet/downloads/) e repita o cenário.
2. Se você receber a mensagem de erro mesmo depois de instalar o provedor, faça o seguinte:
   1. Abra a configuração do computador do .NET 2.0 na pasta: <system disk>: \Windows\Microsoft.NET\Framework64\v2.0.50727\CONFIG\machine.config.
   2. Procure **Provedor de Dados Oracle para .NET**, e você poderá encontrar uma entrada, conforme mostrado no exemplo a seguir em **system.data** -> **DbProviderFactories**: "<add name="Oracle Data Provider for .NET" invariant="Oracle.DataAccess.Client" description="Provedor de Dados Oracle para .NET" type="Oracle.DataAccess.Client.OracleClientFactory, Oracle.DataAccess, Version=2.112.3.0, Culture=neutral, PublicKeyToken=89b483f429c47342" />”
3. Copie esta entrada no arquivo machine.config na seguinte pasta v4.0: <system disk>: \Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config e altere a versão para 4.xxx.x.x.
4. Instale “<Caminho Instalado do ODP.NET> \11.2.0\client_1\odp.net\bin\4\Oracle.DataAccess.dll” no GAC (cache de assembly global), executando `gacutil /i [provider path]`.## Dicas de solução de problemas

### <a name="problem-2-datetime-formatting"></a>Problema 2: formatação de datetime

Se você vir a seguinte **mensagem de erro**:

    Message=Operation failed in Oracle Database with the following error: 'ORA-01861: literal does not match format string'.,Source=,''Type=Oracle.DataAccess.Client.OracleException,Message=ORA-01861: literal does not match format string,Source=Oracle Data Provider for .NET,'.

**Resolução/Solução Alternativa:**

Talvez seja necessário ajustar a cadeia de caracteres de consulta em sua atividade de cópia com base em como as datas são configuradas no banco de dados Oracle, conforme mostrado no exemplo a seguir (usando a função to_date):

    "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= to_date(\\'{0:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\')  AND timestampcolumn < to_date(\\'{1:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') ', WindowStart, WindowEnd)"


## <a name="type-mapping-for-oracle"></a>Mapeamento de tipo para Oracle
Como mencionado no artigo sobre as [atividades da movimentação de dados](data-factory-data-movement-activities.md) , a atividade de Cópia executa conversões automáticas dos tipos de fonte nos tipos de coletor com a seguinte abordagem de duas etapas:

1. Converter de tipos de fonte nativos para o tipo .NET
2. Converter do tipo .NET para o tipo de coletor nativo

Ao mover dados do Oracle, os seguintes mapeamentos são usados do tipo de dados do Oracle para o tipo .NET e vice-versa.

| Tipo de dados do Oracle | Tipo de dados do .NET Framework |
| --- | --- |
| BFILE |Byte[] |
| BLOB |Byte[] |
| CHAR |Cadeia de caracteres |
| CLOB |Cadeia de caracteres |
| DATE |DateTime |
| FLOAT |Decimal, cadeia de caracteres (se precisão > 28) |
| INTEGER |Decimal, cadeia de caracteres (se precisão > 28) |
| INTERVAL YEAR TO MONTH |Int32 |
| INTERVAL DAY TO SECOND |timespan |
| LONG |Cadeia de caracteres |
| LONG RAW |Byte[] |
| NCHAR |Cadeia de caracteres |
| NCLOB |Cadeia de caracteres |
| NUMBER |Decimal, cadeia de caracteres (se precisão > 28) |
| NVARCHAR2 |Cadeia de caracteres |
| RAW |Byte[] |
| ROWID |Cadeia de caracteres |
| TIMESTAMP |DateTime |
| TIMESTAMP WITH LOCAL TIME ZONE |DateTime |
| TIMESTAMP WITH TIME ZONE |DateTime |
| UNSIGNED INTEGER |NUMBER |
| VARCHAR2 |Cadeia de caracteres |
| XML |Cadeia de caracteres |

> [!NOTE]
> Tipo de dados **intervalo ano para mês** e **intervalo dia para segundo** não têm suporte ao usar o driver da Microsoft.

## <a name="map-source-to-sink-columns"></a>Mapear origem para colunas de coletor
Para saber mais sobre mapeamento de colunas no conjunto de dados de origem para colunas no conjunto de dados de coletor, confira [Mapping dataset columns in Azure Data Factory](data-factory-map-columns.md) (Mapeamento de colunas de conjunto de dados no Azure Data Factory).

## <a name="repeatable-read-from-relational-sources"></a>Leitura repetida de fontes relacionais
Ao copiar dados de armazenamentos de dados relacionais, lembre-se da capacidade de repetição para evitar resultados não intencionais. No Azure Data Factory, você pode repetir a execução de uma fatia manualmente. Você também pode configurar a política de repetição para um conjunto de dados de modo que uma fatia seja executada novamente quando ocorrer uma falha. Quando uma fatia é executada novamente, seja de que maneira for, você precisa garantir que os mesmos dados sejam lidos não importa quantas vezes uma fatia seja executada. Confira [Leitura repetida de fontes relacionais](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Desempenho e Ajuste
Veja o [Guia de desempenho e ajuste da Atividade de Cópia](data-factory-copy-activity-performance.md) para saber mais sobre os principais fatores que afetam o desempenho da movimentação de dados (Atividade de Cópia) no Azure Data Factory, além de várias maneiras de otimizar esse processo.
