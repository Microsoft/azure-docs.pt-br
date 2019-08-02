---
title: Mover dados para e do SQL Server | Microsoft Docs
description: Saiba mais sobre como mover dados de/para o banco de dados do SQL Server local ou em uma VM do Azure usando o Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 864ece28-93b5-4309-9873-b095bbe6fedd
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 1e70611d438678ded1260dd00a04960c798fdde5
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836219"
---
# <a name="move-data-to-and-from-sql-server-on-premises-or-on-iaas-azure-vm-using-azure-data-factory"></a>Mover dados para e do SQL Server local ou em IaaS (VM do Azure) usando o Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](data-factory-sqlserver-connector.md)
> * [Versão 2 (versão atual)](../connector-sql-server.md)

> [!NOTE]
> Este artigo se aplica à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte [Conector do SQL Server na V2](../connector-sql-server.md).

Esse artigo explica como usar a Atividade de Cópia no Azure Data Factory para mover dados para/de um banco de dados do SQL Server local. Ele se baseia no artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md), que apresenta uma visão geral da movimentação de dados com a atividade de cópia.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="supported-scenarios"></a>Cenários com suporte
Você pode copiar dados **de um banco de dados do SQL Server** para os seguintes armazenamentos de dados:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Você pode copiar dados dos armazenamentos de dados a seguir **para um banco de dados do SQL Server**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="supported-sql-server-versions"></a>Versões do SQL Server com suporte
Este conector do SQL Server dá suporte à cópia de dados de e para as seguintes versões inferiores da instância hospedada localmente ou no IaaS do Azure usando a autenticação do SQL e do Windows: SQL Server 2016, SQL Server 2014, SQL Server 2012, SQL Server 2008 R2, SQL Server 2008, SQL Server 2005

## <a name="enabling-connectivity"></a>Habilitando a conectividade
Os conceitos e as etapas necessários para conectar-se com o SQL Server hospedado localmente ou em máquinas virtuais do Azure IaaS (infraestrutura como serviço) são os mesmos. Em ambos os casos, você precisa usar o Gateway de Gerenciamento de Dados para ter conectividade.

Consulte o artigo [movendo dados entre pontos locais e na nuvem](data-factory-move-data-between-onprem-and-cloud.md) para saber mais sobre o Gateway de gerenciamento de dados e obter instruções passo a passo de como configurar o gateway. Configurar uma instância de gateway é um pré-requisito para a conexão com o SQL Server.

Embora você possa instalar o gateway no mesmo computador local ou instância de VM na nuvem que o SQL Server, para obter melhore desempenho, é recomendável instalá-los em computadores separados. Ter o gateway e o SQL Server em computadores distintos reduz a contenção de recursos.

## <a name="getting-started"></a>Introdução
Você pode criar um pipeline com atividade de cópia que move dados de/para um banco de dados SQL Server local por meio de ferramentas/APIs diferentes.

A maneira mais fácil de criar um pipeline é usar o **Assistente de Cópia**. Consulte [Tutorial: criar um pipeline usando o Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md) para ver um breve passo a passo sobre como criar um pipeline usando o Assistente de cópia de dados.

Você também pode usar as ferramentas abaixo para criar um pipeline: **Visual Studio**, **Azure PowerShell**, **modelo do Resource Manager**, **.NET API**, e **API REST**. Confira o [Tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo sobre a criação de um pipeline com uma atividade de cópia.

Ao usar as ferramentas ou APIs, você executa as seguintes etapas para criar um pipeline que move dados de um armazenamento de dados de origem para um armazenamento de dados de coletor:

1. Criar uma **data factory**. Um data factory pode conter um ou mais pipelines.
2. Criar **serviços vinculados** para vincular repositórios de dados de entrada e saída ao seu data factory. Por exemplo, se você estiver copiando dados de um banco de dados do SQL Server para um Armazenamento de Blobs do Azure, crie dois serviços vinculados para vincular seu banco de dados do SQL Server e conta de Armazenamento do Azure ao data factory. Para propriedades do serviço vinculado que são específicas do banco de dados do SQL Server, consulte a seção [propriedades do serviço vinculado](#linked-service-properties).
3. Criar **conjuntos de dados** para representar dados de entrada e saída para a operação de cópia. No exemplo mencionado na última etapa, você cria um conjunto de dados para especificar a tabela SQL no banco de dados do SQL Server que contém os dados de entrada. Em seguida, você cria outro conjunto de dados para especificar o contêiner de blob e a pasta que contém os dados copiados do banco de dados do SQL Server. Para propriedades do conjunto de dados que são específicas do banco de dados do SQL Server, consulte a seção [propriedades do conjunto de dados](#dataset-properties).
4. Criar um **pipeline** com uma atividade de cópia que usa um conjunto de dados como uma entrada e um conjunto de dados como uma saída. No exemplo mencionado anteriormente, você usa SqlSource como fonte e BlobSink como coletor para a atividade de cópia. De modo similar, se você estiver copiando do Armazenamento de Blobs do Azure para o banco de dados do SQL Server, você usará BlobSource e SqlSink na atividade de cópia. Para propriedades da atividade de cópia que são específicas do banco de dados do SQL Server, consulte a seção [propriedades da atividade de cópia](#copy-activity-properties). Para obter detalhes sobre como usar um armazenamento de dados como uma origem ou um coletor, clique no link na seção anterior para o armazenamento de dados.

Ao usar o assistente, as definições de JSON para essas entidades do Data Factory (serviços vinculados, conjuntos de dados e o pipeline) são automaticamente criadas para você. Ao usar ferramentas/APIs (exceto a API .NET), você define essas entidades do Data Factory usando o formato JSON. Para obter exemplos com definições de JSON das entidades do Data Factory usadas para copiar dados bidirecionalmente em um banco de dados do SQL Server local, confira a seção [Exemplos de JSON](#json-examples-for-copying-data-from-and-to-sql-server) neste artigo.

As seções que se seguem fornecem detalhes sobre as propriedades JSON que são usadas para definir entidades do Data Factory específicas ao SQL Server:

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado
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

### <a name="samples"></a>Exemplos
**JSON para usar Autenticação SQL**

```json
{
    "name": "MyOnPremisesSQLDB",
    "properties":
    {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```
**JSON para usar Autenticação Windows**

O Gateway de Gerenciamento de Dados representará a conta de usuário especificada para se conectar ao banco de dados local do SQL Server.

```json
{
    "Name": " MyOnPremisesSQLDB",
    "Properties":
    {
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

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Nos exemplos, você usou um conjunto de dados do tipo **SqlServerTable** para representar uma tabela em um banco de dados SQL Server.

Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de dados, confira o artigo [Criando conjuntos de dados](data-factory-create-datasets.md). Seções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (SQL Server, blob do Azure, tabela do Azure, etc.).

A seção typeProperties é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no armazenamento de dados. A seção **typeProperties** do conjunto de dados do tipo **SqlServerTable** tem as seguintes propriedades:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| tableName |Nome da tabela ou exibição na instância do Banco de Dados SQL Server à qual o serviço vinculado se refere. |Sim |

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia
Se você estiver movendo dados de um banco de dados SQL Server, defina o tipo de origem na atividade de cópia como **SqlSource**. Da mesma forma você estiver movendo dados para um banco de dados SQL Server, defina o tipo de coletor na atividade de cópia como **SqlSink**. Esta seção fornece uma lista das propriedades com suporte de SqlSource e SqlSink.

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Criando pipelines](data-factory-create-pipelines.md). As propriedades, como nome, descrição, tabelas de entrada e saída, e políticas, estão disponíveis para todos os tipos de atividade.

> [!NOTE]
> A Atividade de cópia usa apenas uma entrada e produz apenas uma saída.

Por outro lado, as propriedades disponíveis na seção typeProperties da atividade variam de acordo com cada tipo de atividade. Para a atividade de cópia, elas variam de acordo com os tipos de fonte e coletor.

### <a name="sqlsource"></a>SqlSource
Quando a fonte em uma atividade de cópia for do tipo **SqlSource**, as seguintes propriedades estarão disponíveis na seção **typeProperties**:

| Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| sqlReaderQuery |Utiliza a consulta personalizada para ler os dados. |Cadeia de caracteres de consulta SQL. Por exemplo: select * from MyTable. Pode fazer referência a várias tabelas do banco de dados referenciado pelo conjunto de dados de entrada. Se não for especificada, a instrução SQL que é executada é: select from MyTable. |Não |
| sqlReaderStoredProcedureName |Nome do procedimento armazenado que lê os dados da tabela de origem. |Nome do procedimento armazenado. A última instrução SQL deve ser uma instrução SELECT no procedimento armazenado. |Não |
| storedProcedureParameters |Parâmetros para o procedimento armazenado. |Pares de nome/valor. Nomes e uso de maiúsculas e minúsculas de parâmetros devem corresponder aos nomes e o uso de maiúsculas e minúsculas dos parâmetros do procedimento armazenado. |Não |

Se **sqlReaderQuery** for especificado para SqlSource, a Atividade de Cópia executará essa consulta na fonte do Banco do SQL Server para obter os dados.

Como alternativa, você pode especificar um procedimento armazenado especificando o **sqlReaderStoredProcedureName** e o **storedProcedureParameters** (se o procedimento armazenado usa parâmetros).

Se você não especificar sqlReaderQuery nem sqlReaderStoredProcedureName, as colunas definidas na seção de estrutura serão usadas para criar uma consulta seleção a ser executada no Banco de Dados SQL Server. Se a definição de conjunto de dados não tem a estrutura, todas as colunas serão selecionadas da tabela.

> [!NOTE]
> Quando você usa **sqlReaderStoredProcedureName**, ainda é necessário especificar um valor para a propriedade **tableName** no JSON do conjunto de dados. Contudo, não há nenhuma validação executada nessa tabela.

### <a name="sqlsink"></a>SqlSink
**SqlSink** dá suporte às seguintes propriedades:

| Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| writeBatchTimeout |Tempo de espera para a operação de inserção em lotes ser concluída antes de atingir o tempo limite. |TimeSpan<br/><br/> Exemplo: “00:30:00” (30 minutos). |Não |
| writeBatchSize |Insere dados na tabela SQL quando o tamanho do buffer atinge writeBatchSize. |Inteiro (número de linhas) |Não (padrão: 10000) |
| sqlWriterCleanupScript |Especifique a consulta para a Atividade de Cópia a ser executada para que os dados de uma fatia especifica sejam removidos. Para obter mais informações, consulte a seção [Cópia repetida](#repeatable-copy). |Uma instrução de consulta. |Não |
| sliceIdentifierColumnName |Especifique o nome de coluna para a Atividade de Cópia a ser preenchido com o identificador de fatia gerado automaticamente, que é usado para limpar dados de uma fatia específica quando executado novamente. Para obter mais informações, consulte a seção [Cópia repetida](#repeatable-copy). |Nome de uma coluna com tipo de dados de binário (32). |Não |
| sqlWriterStoredProcedureName |Nome do procedimento armazenado que define como aplicar os dados de origem à tabela de destino, por exemplo, para fazer upserts ou transformações usando sua própria lógica de negócios. <br/><br/>Observe que esse procedimento armazenado será **invocado por lote**. Se você deseja executar uma operação que é executada apenas uma vez e que não tem nenhuma relação com os dados de origem, por exemplo, excluir/truncar, use a propriedade `sqlWriterCleanupScript`. |Nome do procedimento armazenado. |Não |
| storedProcedureParameters |Parâmetros para o procedimento armazenado. |Pares de nome/valor. Nomes e uso de maiúsculas e minúsculas de parâmetros devem corresponder aos nomes e o uso de maiúsculas e minúsculas dos parâmetros do procedimento armazenado. |Não |
| sqlWriterTableType |Especifique o nome do tipo de tabela a ser usado no procedimento armazenado. A atividade de cópia disponibiliza aqueles dados sendo movidos em uma tabela temporária com esse tipo de tabela. O código de procedimento armazenado pode mesclar os dados sendo copiados com dados existentes. |Um nome de tipo de tabela. |Não |


## <a name="json-examples-for-copying-data-from-and-to-sql-server"></a>Exemplos JSON para copiar dados de e para o SQL Server
Os exemplos a seguir fornecem as definições de JSON de exemplo que você pode usar para criar um pipeline usando o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [do Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Os exemplos a seguir mostram como copiar dados entre o SQL Server e o Armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados **diretamente** de qualquer uma das fontes para qualquer um dos coletores declarados [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a Atividade de Cópia no Azure Data Factory.

## <a name="example-copy-data-from-sql-server-to-azure-blob"></a>Exemplo: Copiar dados do SQL Server para Blob do Azure
O exemplo a seguir mostra:

1. Um serviço vinculado do tipo [OnPremisesSqlServer](#linked-service-properties).
2. Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [SqlServerTable](#dataset-properties).
4. Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. O [pipeline](data-factory-create-pipelines.md) com a Atividade de Cópia que usa [SqlSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo copia dados de série temporal de uma tabela do SQL Server para um blob do Azure a cada hora. As propriedades JSON usadas nesses exemplos são descritas nas seções após os exemplos.

Como uma primeira etapa, configure o gateway de gerenciamento de dados. As instruções estão no artigo [Mover dados entre fontes locais e a nuvem](data-factory-move-data-between-onprem-and-cloud.md) .

**Serviço vinculado do SQL Server**
```json
{
  "Name": "SqlServerLinkedService",
  "properties": {
    "type": "OnPremisesSqlServer",
    "typeProperties": {
      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
      "gatewayName": "<gatewayname>"
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
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Conjunto de dados de entrada do SQL Server**

O exemplo supõe que você criou uma tabela "MyTable" no SQL Server e que ela contém uma coluna chamada "timestampcolumn" para dados de série temporal. Você pode consultar várias tabelas no mesmo banco de dados usando um único conjunto de dados, mas uma única tabela deve ser usada para a typeProperty de tableName do conjunto de dados.

Configurar "external": "true" informa ao serviço Data Factory que o conjunto de dados é externo ao Data Factory e não é produzido por uma atividade no Data Factory.

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
**Conjunto de dados de saída de Blob do Azure**

Os dados são gravados em um novo blob a cada hora (frequência: horas, intervalo: 1). O caminho de pasta para o blob é avaliado dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa as partes ano, mês, dia e horas da hora de início.

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
**Pipeline com Atividade de cópia**

O pipeline contém uma Atividade de Cópia que está configurada para usar os conjuntos de dados de entrada e saída e agendada para ser executada a cada hora. Na definição de JSON do pipeline, o tipo de **fonte** está definido como **SqlSource** e o tipo de **coletor** está definido como **BlobSink**. A consulta SQL especificada para a propriedade **SqlReaderQuery** seleciona os dados na última hora a serem copiados.

```json
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2016-06-01T18:00:00",
    "end":"2016-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "SqlServertoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": " SqlServerInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
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
      }
    ]
  }
}
```
Neste exemplo, **sqlReaderQuery** é especificada para SqlSource. A Atividade de Cópia executa essa consulta na fonte do Banco de Dados do SQL Server para obter os dados. Como alternativa, você pode especificar um procedimento armazenado especificando o **sqlReaderStoredProcedureName** e o **storedProcedureParameters** (se o procedimento armazenado usa parâmetros). A sqlReaderQuery pode fazer referência a várias tabelas no banco de dados referenciado pelo conjunto de dados de entrada. A propriedade não se limita apenas à tabela definida como typeProperty de tableName do conjunto de dados.

Se você não especificar sqlReaderQuery nem sqlReaderStoredProcedureName, as colunas definidas na seção de estrutura serão usadas para criar uma consulta seleção a ser executada no Banco de Dados SQL Server. Se a definição de conjunto de dados não tem a estrutura, todas as colunas serão selecionadas da tabela.

Consulte a seção [Sql Source](#sqlsource) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) para obter a lista de propriedades com suporte em SqlSource e BlobSink.

## <a name="example-copy-data-from-azure-blob-to-sql-server"></a>Exemplo: Copiar dados do Blob do Azure para o SQL Server
O exemplo a seguir mostra:

1. O serviço vinculado do tipo [OnPremisesSqlServer](#linked-service-properties).
2. O serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [SqlServerTable](data-factory-sqlserver-connector.md#dataset-properties).
5. O [pipeline](data-factory-create-pipelines.md) com a atividade de Cópia que usa [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) e SqlSink.

O exemplo copia dados de série temporal de um Blob do Azure para uma tabela do SQL Server a cada hora. As propriedades JSON usadas nesses exemplos são descritas nas seções após os exemplos.

**Serviço vinculado do SQL Server**

```json
{
  "Name": "SqlServerLinkedService",
  "properties": {
    "type": "OnPremisesSqlServer",
    "typeProperties": {
      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
      "gatewayName": "<gatewayname>"
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
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Conjunto de dados de entrada de Blob do Azure**

Os dados são coletados de um novo blob a cada hora (frequência: hora, intervalo: 1). O caminho de pasta e nome de arquivo para o blob são avaliados dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa a parte do ano, mês e dia da hora de início e o nome de arquivo usa a parte da hora de início. A configuração "external": "true" informa o serviço Data Factory que o conjunto de dados é externo ao Data Factory e não é produzido por uma atividade no Data Factory.

```json
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
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
        "columnDelimiter": ",",
        "rowDelimiter": "\n"
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
**Conjunto de dados de saída do SQL Server**

O exemplo copia dados para uma tabela chamada "MyTable" no SQL Server. Crie a tabela no SQL Server com o mesmo número de colunas que você espera que o arquivo CSV de Blob contenha. Novas linhas são adicionadas à tabela a cada hora.

```json
{
  "name": "SqlServerOutput",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlServerLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**Pipeline com Atividade de cópia**

O pipeline contém uma Atividade de Cópia que está configurada para usar os conjuntos de dados de entrada e saída e agendada para ser executada a cada hora. Na definição de JSON do pipeline, o tipo de **fonte** está definido como **BlobSource** e o tipo de **coletor** está definido como **SqlSink**.

```json
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[
      {
        "name": "AzureBlobtoSQL",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": " SqlServerOutput "
          }
        ],
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
      }
    ]
  }
}
```

## <a name="troubleshooting-connection-issues"></a>Solucionar problemas de conexão
1. Configure seu SQL Server para aceitar conexões remotas. Inicie o **SQL Server Management Studio**, clique com o botão direito do mouse em **servidor** e clique em **Propriedades**. Selecione **Conexões** na lista e marque a opção **Permitir conexões remotas com o servidor**.

    ![Habilitar conexões remotas](./media/data-factory-sqlserver-connector/AllowRemoteConnections.png)

    Veja [Configurar a Opção de Configuração do Servidor de acesso remoto](https://msdn.microsoft.com/library/ms191464.aspx) para obter as etapas detalhadas.
2. Inicie o **SQL Server Configuration Manager**(Gerenciador de Configuração do SQL Server). Expanda **Configuração de Rede do SQL Server** para a instância desejada e selecione **Protocolos para MSSQLSERVER**. Você deve ver os protocolos no painel à direita. Habilite o TCP/IP clicando com o botão direito do mouse em **TCP/IP** e clicando em **Habilitar**.

    ![Habilitar TCP/IP](./media/data-factory-sqlserver-connector/EnableTCPProptocol.png)

    Veja [Habilitar ou Desabilitar um Protocolo de Rede do Servidor](https://msdn.microsoft.com/library/ms191294.aspx) para ver detalhes e formas alternativas de habilitar um protocolo TCP/IP.
3. Na mesma janela, clique duas vezes em **TCP/IP** para iniciar a janela **Propriedades de TCP/IP**.
4. Alterne para a guia **Endereços IP** . Role para baixo para ver a seção **IPAll** . Anote o **a porta TCP**(o padrão é **1433**).
5. Crie uma **regra para o Firewall do Windows** no computador para permitir a entrada de tráfego por essa porta.
6. **Verificar a conexão**: para se conectar ao SQL Server usando um nome totalmente qualificado, use o SQL Server Management Studio de um computador diferente. Por exemplo: "\<máquina\>.\<domínio\>.corp.\<empresa\>.com,1433”. "

   > [!IMPORTANT]
   > 
   > Consulte [Mover dados entre fontes locais e a nuvem com o Gateway de Gerenciamento de Dados](data-factory-move-data-between-onprem-and-cloud.md) para obter informações detalhadas.
   > 
   > Consulte [Solucionar problemas de gateway](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) para ver dicas sobre como solucionar os problemas relacionados à conexão/gateway.


## <a name="identity-columns-in-the-target-database"></a>Colunas de identidade no banco de dados de destino
Esta seção fornece um exemplo que copia dados de uma tabela de origem sem uma coluna de identidade para uma tabela de destino com uma coluna de identidade.

**Tabela de origem:**

```sql
create table dbo.SourceTbl
(
    name varchar(100),
    age int
)
```
**Tabela de destino:**

```sql
create table dbo.TargetTbl
(
    identifier int identity(1,1),
    name varchar(100),
    age int
)
```

Observe que a tabela de destino tem uma coluna de identidade.

**Definição de JSON do conjunto de dados de origem**

```json
{
    "name": "SampleSource",
    "properties": {
        "published": false,
        "type": " SqlServerTable",
        "linkedServiceName": "TestIdentitySQL",
        "typeProperties": {
            "tableName": "SourceTbl"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```
**Definição de JSON do conjunto de dados de destino**

```json
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "published": false,
        "type": "AzureSqlTable",
        "linkedServiceName": "TestIdentitySQLSource",
        "typeProperties": {
            "tableName": "TargetTbl"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": false,
        "policy": {}
    }
}
```

Observe que sua tabela de origem e de destino têm um esquema diferente (a de destino tem uma coluna adicional com identidade). Nesse cenário, você precisa especificar a propriedade **structure** na definição de conjunto de dados de destino, que não inclui a coluna de identidade.

## <a name="invoke-stored-procedure-from-sql-sink"></a>Invocar o procedimento armazenado do coletor SQL
Para obter um exemplo de como invocar um procedimento armazenado do coletor SQL em uma atividade de cópia de um pipeline, confira o artigo [Invoke stored procedure for SQL sink in copy activity](data-factory-invoke-stored-procedure-from-copy-activity.md) (Invocar procedimento armazenado para coletor SQL na atividade de cópia).

## <a name="type-mapping-for-sql-server"></a>Mapeamento de tipo para o SQL Server
Conforme mencionado no artigo sobre [atividades de movimentação de dados](data-factory-data-movement-activities.md) , a Atividade de Cópia executa conversões automáticas de tipos de fonte em tipos de coletor com a abordagem de duas etapas:

1. Converter de tipos de fonte nativos para o tipo .NET
2. Converter do tipo .NET para o tipo de coletor nativo

Ao mover dados bidirecionalmente no SQL Server, os mapeamentos a seguir são usados do tipo SQL para o tipo .NET e vice-versa.

O mapeamento é o mesmo que o mapeamento de tipo de dados do SQL Server para o ADO.NET.

| Tipo de mecanismo do Banco de Dados do SQL Server | Tipo .NET Framework |
| --- | --- |
| bigint |Int64 |
| binary |Byte[] |
| bit |Boolean |
| char |String, Char[] |
| date |DateTime |
| DateTime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Float |Duplo |
| imagem |Byte[] |
| int |Int32 |
| money |Decimal |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |Decimal |
| nvarchar |String, Char[] |
| real |Simples |
| rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Object * |
| text |String, Char[] |
| time |TimeSpan |
| timestamp |Byte[] |
| tinyint |Byte |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |xml |

## <a name="mapping-source-to-sink-columns"></a>Mapear origem para colunas de coletor
Para mapear colunas de conjunto de dados de origem para colunas do conjunto de dados de coletor, confira [Mapeando colunas de conjunto de dados no Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-copy"></a>Cópia repetida
Ao copiar dados para o Banco de Dados do SQL Server, a atividade de cópia acrescenta dados à tabela de coletor por padrão. Para substituir isso pela execução de UPSERT, confira o artigo [Repeatable write to SqlSink](data-factory-repeatable-copy.md#repeatable-write-to-sqlsink) (Gravação repetida no SqlSink).

Ao copiar dados de armazenamentos de dados relacionais, lembre-se da capacidade de repetição para evitar resultados não intencionais. No Azure Data Factory, você pode repetir a execução de uma fatia manualmente. Você também pode configurar a política de repetição para um conjunto de dados de modo que uma fatia seja executada novamente quando ocorrer uma falha. Quando uma fatia é executada novamente, seja de que maneira for, você precisa garantir que os mesmos dados sejam lidos não importa quantas vezes uma fatia seja executada. Confira [Leitura repetida de fontes relacionais](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Desempenho e Ajuste
Veja o [Guia de desempenho e ajuste da Atividade de Cópia](data-factory-copy-activity-performance.md) para saber mais sobre os principais fatores que afetam o desempenho da movimentação de dados (Atividade de Cópia) no Azure Data Factory, além de várias maneiras de otimizar esse processo.
