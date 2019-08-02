---
title: Copiar dados de/para o Oracle usando o Data Factory | Microsoft Docs
description: Saiba como copiar dados de ou para um Oracle Database local usando o Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 3c20aa95-a8a1-4aae-9180-a6a16d64a109
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 51fae63b6db99f28a5b3bed056dadc0c2513ff0f
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839925"
---
# <a name="copy-data-to-or-from-oracle-on-premises-by-using-azure-data-factory"></a>Copiar dados de ou para o Oracle local usando o Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](data-factory-onprem-oracle-connector.md)
> * [Versão 2 (versão atual)](../connector-oracle.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Azure Data Factory. Se você estiver usando a versão atual do serviço do Azure Data Factory, confira [Conector Oracle em V2](../connector-oracle.md).


Esse artigo explica como usar a Atividade de Cópia no Azure Data Factory para mover dados de ou para um Oracle Database local. O artigo é baseado em [Atividades de movimentação de dados](data-factory-data-movement-activities.md), que apresenta uma visão geral da movimentação de dados usando a Atividade de Cópia.

## <a name="supported-scenarios"></a>Cenários com suporte

Você pode copiar dados *de um banco de dados Oracle* para os seguintes armazenamentos de dados:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Você pode copiar dados dos seguintes armazenamentos de dados *para um banco de dados Oracle*:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="prerequisites"></a>Pré-requisitos

O Data Factory dá suporte à conexão com fontes Oracle locais usando o Gateway de Gerenciamento de Dados. Confira o [Gateway de Gerenciamento de Dados](data-factory-data-management-gateway.md) para saber mais sobre o Gateway de Gerenciamento de Dados. Para obter instruções passo a passo de como configurar o gateway em um pipeline de dados para mover dados, confira o artigo [Mover dados de pontos locais para a nuvem](data-factory-move-data-between-onprem-and-cloud.md).

O gateway é necessário mesmo que o Oracle esteja hospedado em uma VM de IaaS (infraestrutura como serviço) do Azure. Você pode instalar o gateway na mesma VM IaaS do armazenamento de dados ou em uma VM diferente, desde que o gateway possa conectar o banco de dados.

> [!NOTE]
> Para obter dicas sobre solução de problemas relacionados à conexão e ao gateway, confira [Solucionar problemas do gateway](data-factory-data-management-gateway.md#troubleshooting-gateway-issues).

## <a name="supported-versions-and-installation"></a>Instalação e versões com suporte

Este conector Oracle dá suporte a duas versões de drivers:

- **Driver da Microsoft para Oracle (recomendado)** : Do Gateway de Gerenciamento de Dados versão 2.7 em diante, um driver da Microsoft para Oracle é instalado automaticamente com o gateway. Você não precisa instalar nem atualizar o driver para estabelecer a conectividade com o Oracle. Você também pode ter um melhor desempenho de cópia usando este driver. Há suporte para estas versões abaixo de bancos de dados Oracle:
  - Oracle 12c R1 (12.1)
  - Oracle 11g R1, R2 (11.1, 11.2)
  - Oracle 10g R1, R2 (10.1, 10.2)
  - Oracle 9i R1, R2 (9.0.1, 9.2)
  - Oracle 8i R3 (8.1.7)

    > [!NOTE]
    > Não há suporte para servidor proxy do Oracle.

    > [!IMPORTANT]
    > Atualmente, o driver da Microsoft para Oracle dá suporte apenas para copiar dados do Oracle. O driver não dá suporte à gravação para o Oracle. A funcionalidade de conexão de teste na guia **Diagnóstico** do Gateway de Gerenciamento de Dados não oferece suporte a este driver. Como alternativa, você pode usar o assistente para Copiar para validar a conectividade.
    >

- **Provedor de Dados Oracle para .NET**: Você também pode usar o Provedor de Dados do Oracle para copiar dados de ou para o Oracle. Esse componente está incluído nos [Componentes de Acesso a Dados do Oracle para Windows](https://www.oracle.com/technetwork/topics/dotnet/downloads/). Instale a versão adequada (32 bits ou 64 bits) no computador em que o gateway foi instalado. [O Provedor de Dados Oracle para .NET 12.1](https://docs.oracle.com/database/121/ODPNT/InstallSystemRequirements.htm#ODPNT149) pode acessar o Oracle Database 10g Versão 2 e versões posteriores.

    Se você selecionar **Instalação de XCopy**, conclua as etapas descritas no arquivo readme.htm. É recomendável selecionar o instalador que tem a interface do usuário (não o instalador de XCopy).

    Depois de instalar o provedor, reinicie o serviço de host do Gateway de Gerenciamento de Dados no seu computador usando o miniaplicativo Serviços ou o Configuration Manager do Gateway de Gerenciamento de Dados.

Se você usar o assistente de Cópia para criar o pipeline de cópia, o tipo de driver será determinado automaticamente. O driver da Microsoft é usado por padrão, a menos que sua versão do gateway seja anterior à versão 2.7 ou você selecione Oracle como o coletor.

## <a name="get-started"></a>Introdução

Você pode criar um pipeline com uma atividade de cópia. O pipeline move dados de ou para um Oracle Database local usando diferentes ferramentas ou APIs.

A maneira mais fácil de criar um pipeline é usar o assistente de Cópia. Consulte [Tutorial: Criar um pipeline usando o assistente de Cópia](data-factory-copy-data-wizard-tutorial.md) para instruções passo a passo rápidas sobre como criar um pipeline usando o assistente Copiar Dados.

Você também pode usar uma das seguintes ferramentas para criar um pipeline: **Visual Studio**, **Azure PowerShell**, uma **modelo do Resource Manager**, o **.NET API**, ou o **API REST**. Consulte o [tutorial Atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo sobre como criar um pipeline que tenha uma atividade de cópia.

Ao usar as ferramentas ou APIs, conclua as seguintes etapas para criar um pipeline que move dados de um armazenamento de dados de origem para um armazenamento de dados de coletor:

1. Criar uma **data factory**. Um data factory pode conter um ou mais pipelines.
2. Criar **serviços vinculados** para vincular repositórios de dados de entrada e saída ao seu data factory. Por exemplo, se você estiver copiando dados de um Oracle Database para um Armazenamento de Blobs do Azure, crie dois serviços vinculados para vincular seu Oracle Database e conta de armazenamento do Azure ao data factory. Para propriedades do serviço vinculado específicas do Oracle, confira [Propriedades do serviço vinculado](#linked-service-properties).
3. Criar **conjuntos de dados** para representar dados de entrada e saída para a operação de cópia. No exemplo mencionado na etapa anterior, você cria um conjunto de dados para especificar a tabela no Oracle Database que contém os dados de entrada. Você cria outro conjunto de dados para especificar o contêiner de blob e a pasta que contém os dados copiados do Oracle Database. Para propriedades de conjunto de dados específicas do Oracle, confira a seção [Propriedades do conjunto de dados](#dataset-properties).
4. Criar um **pipeline** com uma atividade de cópia que usa um conjunto de dados como uma entrada e um conjunto de dados como uma saída. No exemplo anterior, você usa **OracleSource** como fonte e **BlobSink** como coletor para a atividade de cópia. De modo similar, se você estiver copiando do Armazenamento de Blobs do Azure para o Oracle Database, use **BlobSource** e **OracleSink** na atividade de cópia. Para propriedades da Atividade de Cópia específicas do Oracle Database, confira a seção [Propriedades da Atividade de Cópia](#copy-activity-properties). Para obter detalhes sobre como usar um armazenamento de dados como fonte ou coletor, selecione o link para o armazenamento de dados na seção anterior.

Ao usar o assistente, estas definições de JSON para essas entidades do Data Factory são automaticamente criadas para você: serviços vinculados, conjuntos de dados e o pipeline. Ao usar ferramentas ou APIs (exceto pela API .NET), você define essas entidades do Data Factory usando o formato JSON. Para ver amostras com definições de JSON para entidades do Data Factory que você usa para copiar dados para ou do banco de dados Oracle local, confira exemplos de JSON.

As seções que se seguem fornecem detalhes sobre as propriedades JSON que você usa para definir entidades do Data Factory.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

A tabela a seguir descreve elementos JSON que são específicos para o serviço vinculado Oracle:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| type |A propriedade **type** deve ser definida como **OnPremisesOracle**. |Sim |
| driverType | Especifique qual driver a ser usado para copiar dados de ou para um Oracle Database. Os valores permitidos são **Microsoft** ou **ODP** (padrão). Confira [Versão e instalação com suporte](#supported-versions-and-installation) para obter detalhes do driver. | Não |
| connectionString | Especifique as informações necessárias para se conectar à instância do Oracle Database para a propriedade **connectionString**. | Sim |
| gatewayName | O nome do gateway usado para conectar-se ao servidor Oracle local. |Sim |

**Exemplo: Usando o driver da Microsoft**

> [!TIP]
> Se você visualizar um erro informando "ORA-01025: parâmetro UPI fora do intervalo" e o Oracle for versão 8i, adicione `WireProtocolMode=1` à cadeia de conexão e tente novamente:

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString":"Host=<host>;Port=<port>;Sid=<service ID>;User Id=<user name>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Exemplo: Como usar o driver ODP**

Para saber mais sobre os formatos permitidos, confira [Provedor de dados Oracle para .NET ODP](https://www.connectionstrings.com/oracle-data-provider-for-net-odp-net/).

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "connectionString": "Data Source=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=<host name>)(PORT=<port number>))(CONNECT_DATA=(SERVICE_NAME=<service ID>))); User Id=<user name>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira [Criando conjuntos de dados](data-factory-create-datasets.md).

As seções de um arquivo JSON do conjunto de dados, como estrutura, disponibilidade e política, são similares para todos os tipos de conjunto de dados (por exemplo, para Oracle, Armazenamento de Blobs do Azure e armazenamento de Tabelas do Azure).

A seção **typeProperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no armazenamento de dados. A seção **typeProperties** do conjunto de dados do tipo **OracleTable** tem as propriedades a seguir:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| tableName |O nome da tabela no banco de dados Oracle à qual o serviço vinculado se refere. |Não (se **oracleReaderQuery** ou **OracleSource** for especificado) |

## <a name="copy-activity-properties"></a>Propriedades da Atividade de Cópia

Para obter uma lista completa de seções e propriedades disponíveis para definir atividades, consulte [Creating pipelines](data-factory-create-pipelines.md).

Propriedades como nome, descrição, tabelas de entrada e saída e política estão disponíveis para todos os tipos de atividades.

> [!NOTE]
> A Atividade de Cópia usa apenas uma entrada e produz apenas uma saída.

As propriedades que estão disponíveis na seção **typeProperties** da atividade variam de acordo com cada tipo de atividade. As propriedades da Atividade de Cópia variam conforme o tipo de fonte e coletor.

### <a name="oraclesource"></a>OracleSource

Na Atividade de Cópia, quando a fonte é do tipo **OracleSource**, as seguintes propriedades estão disponíveis na seção **typeProperties**:

| Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| oracleReaderQuery |Utiliza a consulta personalizada para ler os dados. |Uma cadeia de caracteres de consulta SQL. Por exemplo, "select \* from **MyTable**". <br/><br/>Se não for especificada, a instrução SQL executada será: "select \* from **MyTable**" |Não<br />(se **tableName** de **dataset** for especificado) |

### <a name="oraclesink"></a>OracleSink

**OracleSink** é compatível com as seguintes propriedades:

| Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| writeBatchTimeout |O tempo de espera para a operação de inserção em lotes a ser concluída antes de atingir o tempo limite. |**timespan**<br/><br/> Exemplo: 00:30:00 (30 minutos) |Não |
| writeBatchSize |Insere dados na tabela SQL quando o tamanho do buffer atinge o valor de **writeBatchSize**. |Inteiro (número de linhas) |Não (padrão: 100) |
| sqlWriterCleanupScript |Especifica uma consulta da Atividade de Cópia a ser executada para que os dados de uma fatia específica sejam removidos. |Uma instrução de consulta. |Não |
| sliceIdentifierColumnName |Especifica o nome da coluna para a atividade de cópia a ser preenchida com um identificador de fatia gerado automaticamente. O valor para **sliceIdentifierColumnName** é usado para limpar os dados de uma fatia específica quando executada novamente. |O nome da coluna de uma coluna que tem o tipo de dados **binary(32)** . |Não |

## <a name="json-examples-for-copying-data-to-and-from-the-oracle-database"></a>Exemplos JSON para copiar de dados de e para o Oracle Database

Os exemplos a seguir fornecem as definições de JSON de exemplo que você pode usar para criar um pipeline usando o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [do Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Os exemplos mostram como copiar dados de ou para um Oracle Database e para ou do Armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados para qualquer um dos coletores listados em [Formatos e armazenamentos de dados com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a Atividade de Cópia no Azure Data Factory.

**Exemplo: Copiar dados do Oracle para o Armazenamento de Blobs do Azure**

O exemplo tem as seguintes entidades do Data Factory:

* Um serviço vinculado do tipo [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties).
* Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties).
* Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Um [pipeline](data-factory-create-pipelines.md) com uma atividade de cópia que usa [OracleSource](data-factory-onprem-oracle-connector.md#copy-activity-properties) como fonte e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) como coletor.

O exemplo copia os dados de uma tabela em um banco de dados Oracle local para um blob por hora. Para obter mais informações sobre várias propriedades usadas no exemplo, confira as seções posteriores aos exemplos.

**Serviço vinculado do Oracle**

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString":"Host=<host>;Port=<port>;Sid=<service ID>;User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Serviço vinculado do armazenamento de Blob do Azure**

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
        }
    }
}
```

**Conjunto de dados de entrada do Oracle**

O exemplo supõe que você criou uma tabela denominada **MyTable** no Oracle. Ele contém uma coluna chamada **timestampcolumn** para dados de série temporal.

Configurar **external**: **true** informa ao serviço Data Factory que o conjunto de dados é externo ao data factory e que o conjunto de dados não é produzido por uma atividade no data factory.

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

**Conjunto de dados de saída de Blob do Azure**

Dados são gravados em um novo blob a cada hora (**frequência**: **horas**, **intervalo**: **1**). O caminho de pasta e o nome de arquivo para o blob são avaliados dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa as partes de ano, mês, dia e hora da hora de início.

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

**Pipeline com uma atividade de cópia**

O pipeline contém uma atividade de cópia configurada para usar os conjuntos de dados de entrada e saída e está agendada para ser executada por hora. Na definição JSON do pipeline, o tipo **source** é definido como **OracleSource** e o tipo **sink** é definido como **BlobSink**. A consulta SQL que você especifica usando a propriedade **oracleReaderQuery** seleciona os dados na última hora a serem copiados.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for a copy activity",
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

**Exemplo: Copiar dados do Armazenamento de Blobs do Azure para o Oracle**

Este exemplo mostra como copiar dados de uma conta de Armazenamento de Blobs do Azure para um Oracle Database local. No entanto, você pode copiar dados *diretamente* de qualquer uma das fontes listadas em [Formatos e armazenamentos de dados com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a Atividade de Cópia no Azure Data Factory.

O exemplo tem as seguintes entidades do Data Factory:

* Um serviço vinculado do tipo [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties).
* Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties).
* Um [pipeline](data-factory-create-pipelines.md) com a Atividade de Cópia que usa [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) como fonte e [OracleSink](data-factory-onprem-oracle-connector.md#copy-activity-properties) como coletor.

O exemplo copia dados de um blob para uma tabela em um banco de dados Oracle local de hora em hora. Para obter mais informações sobre várias propriedades usadas no exemplo, confira as seções posteriores aos exemplos.

**Serviço vinculado do Oracle**

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "connectionString": "Data Source=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=<host name>)(PORT=<port number>))(CONNECT_DATA=(SERVICE_NAME=<service ID>)));
            User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Serviço vinculado do armazenamento de Blob do Azure**

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
        }
    }
}
```

**Conjunto de dados de entrada de Blob do Azure**

Os dados são coletados de um novo blob a cada hora (**frequência**: **hora**, **intervalo**: **1**). O caminho de pasta e o nome de arquivo para o blob são avaliados dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa as partes de o ano, mês e dia da hora de início. O nome de arquivo usa a parte de hora da hora de início. A configuração **external**: **true** informa ao serviço Data Factory que a tabela é externa ao Data Factory e não é produzida por uma atividade no Data Factory.

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

**Conjunto de dados de saída Oracle**

O exemplo supõe que você criou uma tabela denominada **MyTable** no Oracle. Crie a tabela no Oracle com o mesmo número de colunas que você espera que o arquivo CSV de blob contenha. Novas linhas são adicionadas à tabela a cada hora.

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

**Pipeline com uma atividade de cópia**

O pipeline contém uma atividade de cópia configurada para usar os conjuntos de dados de entrada e saída e agendada para ser executada a cada hora. Na definição JSON do pipeline, o tipo **source** está definido como **BlobSource** e o tipo **sink** está definido como **OracleSink**.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-05T19:00:00",
        "description":"pipeline with a copy activity",
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

**Mensagem de erro**

    Copy activity met invalid parameters: 'UnknownParameterName', Detailed message: Unable to find the requested .NET Framework Data Provider. It may not be installed.

**Possíveis causas:**

* O Provedor de Dados .NET Framework para Oracle não foi instalado.
* O Provedor de Dados .NET Framework para Oracle foi instalado no .NET Framework 2.0 e não foi encontrado nas pastas .NET Framework 4.0.

**Resolução**

* Se você não instalou o Provedor do .NET para o Oracle, [instale-o](https://www.oracle.com/technetwork/topics/dotnet/downloads/) e repita o cenário.
* Se você vir a mensagem de erro mesmo depois de instalar o provedor, conclua as seguintes etapas:
    1. Abra o arquivo de configuração de computador para .NET 2.0 na pasta <disco do sistema\>:\Windows\Microsoft.NET\Framework64\v2.0.50727\CONFIG\machine.config.
    2. Pesquise **Provedor de Dados Oracle para .NET**. Você deve ser capaz de encontrar uma entrada conforme mostrado no exemplo a seguir em **system.data** > **DbProviderFactories**: `<add name="Oracle Data Provider for .NET" invariant="Oracle.DataAccess.Client" description="Oracle Data Provider for .NET" type="Oracle.DataAccess.Client.OracleClientFactory, Oracle.DataAccess, Version=2.112.3.0, Culture=neutral, PublicKeyToken=89b483f429c47342" />`
* Copie esta entrada para o arquivo machine.config na seguinte pasta .NET 4.0: <disco do sistema\>: \Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config. Em seguida, altere a versão para 4.xxx.x.x.
* Instale <Caminho de Instalação do ODP.NET\>\11.2.0\client_1\odp.net\bin\4\Oracle.DataAccess.dll no GAC (cache de assembly global) executando **gacutil /i [caminho do provedor]** .

### <a name="problem-2-datetime-formatting"></a>Problema 2: Formatação de data/hora

**Mensagem de erro**

    Message=Operation failed in Oracle Database with the following error: 'ORA-01861: literal does not match format string'.,Source=,''Type=Oracle.DataAccess.Client.OracleException,Message=ORA-01861: literal does not match format string,Source=Oracle Data Provider for .NET,'.

**Resolução**

Talvez você precise ajustar a cadeia de consulta em sua atividade de cópia com base em como as datas são configuradas no Oracle Database. Aqui está um exemplo (usando a função **to_date**):

    "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= to_date(\\'{0:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') AND timestampcolumn < to_date(\\'{1:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') ', WindowStart, WindowEnd)"


## <a name="type-mapping-for-oracle"></a>Mapeamento de tipo para Oracle

Como mencionado no artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md), a Atividade de Cópia executa conversões automáticas de tipos de fonte para tipos de coletor usando a seguinte abordagem de duas etapas:

1. Converter de tipos de origem nativos em um tipo .NET.
2. Converta do tipo .NET no tipo de coletor nativo.

Ao mover dados do Oracle, os seguintes mapeamentos são usados do tipo de dados do Oracle para o tipo .NET e vice-versa:

| Tipo de dados do Oracle | Tipo de dados do .NET Framework |
| --- | --- |
| BFILE |Byte[] |
| BLOB |Byte[]<br/>(só tem suporte no Oracle 10g e versões posteriores quando você usa um driver da Microsoft) |
| CHAR |String |
| CLOB |String |
| DATE |DateTime |
| FLOAT |Decimal, String (se precisão > 28) |
| INTEGER |Decimal, String (se precisão > 28) |
| INTERVAL YEAR TO MONTH |Int32 |
| INTERVAL DAY TO SECOND |timespan |
| LONG |String |
| LONG RAW |Byte[] |
| NCHAR |String |
| NCLOB |String |
| NUMBER |Decimal, String (se precisão > 28) |
| NVARCHAR2 |String |
| RAW |Byte[] |
| ROWID |String |
| TIMESTAMP |DateTime |
| TIMESTAMP WITH LOCAL TIME ZONE |DateTime |
| TIMESTAMP WITH TIME ZONE |DateTime |
| UNSIGNED INTEGER |Number |
| VARCHAR2 |String |
| XML |string |

> [!NOTE]
> Tipos de dados **INTERVAL YEAR TO MONTH** e **INTERVAL DAY TO SECOND** não têm suporte quando você usa um driver da Microsoft.

## <a name="map-source-to-sink-columns"></a>Mapear origem para colunas de coletor

Para saber mais sobre mapeamento de colunas no conjunto de dados de origem para colunas no conjunto de dados de coletor, confira [Mapeamento de colunas de conjunto de dados no Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Leitura repetida de fontes relacionais

Ao copiar dados de um armazenamento de dados relacional, lembre-se da capacidade de repetição para evitar resultados não intencionais. No Azure Data Factory, você pode repetir manualmente a execução de uma fatia. Você também pode configurar uma política de repetição para um conjunto de dados de modo que uma fatia seja executada novamente quando ocorrer uma falha. Quando uma fatia é executada novamente, seja manualmente ou por uma política de repetição, os mesmos dados devem ser lidos, não importa quantas vezes uma fatia é executada. Para saber mais, confira [Leitura repetível de fontes relacionais](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Desempenho e ajuste

Confira o artigo [Guia de desempenho e ajuste da Atividade de Cópia](data-factory-copy-activity-performance.md) para conhecer os principais fatores que afetam o desempenho da movimentação de dados (Atividade de Cópia) no Azure Data Factory. Você também pode aprender sobre as várias maneiras de otimizá-lo.
