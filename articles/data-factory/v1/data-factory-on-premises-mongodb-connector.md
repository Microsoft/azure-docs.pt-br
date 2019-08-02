---
title: Mover dados do MongoDB usando o Data Factory | Microsoft Docs
description: Saiba mais sobre como mover dados do banco de dados MongoDB usando o Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 10ca7d9a-7715-4446-bf59-2d2876584550
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: e7a84d74e1bda6de8549c79dab1bec8c2515e213
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839072"
---
# <a name="move-data-from-mongodb-using-azure-data-factory"></a>Mover dados do MongoDB usando o Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](data-factory-on-premises-mongodb-connector.md)
> * [Versão 2 (versão atual)](../connector-mongodb.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte [Conector do MongoDB na V2](../connector-mongodb.md).


Esse artigo explica como usar a Atividade de Cópia no Azure Data Factory para mover dados de um banco de dados MongoDB local. Ele se baseia no artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md), que apresenta uma visão geral da movimentação de dados com a atividade de cópia.

Você pode copiar dados de um armazenamento de dados local do MongoDB para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de repositórios de dados com suporte como coletores da atividade de cópia, confira a tabela [Repositórios de dados com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Atualmente, o data factory dá suporte apenas à movimentação de dados de um armazenamento de dados MongoDB para outros repositórios de dados, mas não à movimentação de dados de outros repositórios de dados para um armazenamento de dados MongoDB.

## <a name="prerequisites"></a>Pré-requisitos
Para o serviço Azure Data Factory poder se conectar ao banco de dados MongoDB no local, você deve instalar os seguintes componentes:

- As versões compatíveis do MongoDB são: 2.4, 2.6, 3.0, 3.2, 3.4 e 3.6.
- Gateway de Gerenciamento de Dados na mesma máquina que hospeda o banco de dados ou em uma máquina separada para evitar competência por recursos com o banco de dados. O Gateway de Gerenciamento de Dados é um software que conecta fontes de dados locais a serviços de nuvem de maneira segura e gerenciada. Confira o artigo [Gateway de Gerenciamento de Dados](data-factory-data-management-gateway.md) para obter todos os detalhes sobre o Gateway de Gerenciamento de Dados. Consulte o artigo [Mover dados de pontos locais para a nuvem](data-factory-move-data-between-onprem-and-cloud.md) para obter instruções detalhadas sobre como configurar um pipeline de dados para o gateway a fim de mover dados.

    Quando você instala o gateway, ele instala automaticamente um driver ODBC do Microsoft MongoDB usado para se conectar ao banco de dados MongoDB.

    > [!NOTE]
    > É necessário usar o gateway para se conectar ao MongoDB, mesmo se ele está hospedado em VMs IaaS do Azure. Se estiver tentando se conectar a uma instância do MongoDB hospedada na nuvem, você também pode instalar a instância do gateway na VM do IaaS.

## <a name="getting-started"></a>Introdução
Você pode criar um pipeline com atividade de cópia que mova dados de um armazenamento de dados local MongoDB usando diferentes ferramentas/APIs.

A maneira mais fácil de criar um pipeline é usar o **Assistente de Cópia**. Consulte [Tutorial: criar um pipeline usando o Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md) para ver um breve passo a passo sobre como criar um pipeline usando o Assistente de cópia de dados.

Você também pode usar as ferramentas abaixo para criar um pipeline: **Visual Studio**, **Azure PowerShell**, **modelo do Resource Manager**, **.NET API**, e **API REST**. Confira o [Tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo sobre a criação de um pipeline com uma atividade de cópia.

Ao usar as ferramentas ou APIs, você executa as seguintes etapas para criar um pipeline que move dados de um armazenamento de dados de origem para um armazenamento de dados de coletor:

1. Criar **serviços vinculados** para vincular repositórios de dados de entrada e saída ao seu data factory.
2. Criar **conjuntos de dados** para representar dados de entrada e saída para a operação de cópia.
3. Criar um **pipeline** com uma atividade de cópia que usa um conjunto de dados como uma entrada e um conjunto de dados como uma saída.

Ao usar o assistente, as definições de JSON para essas entidades do Data Factory (serviços vinculados, conjuntos de dados e o pipeline) são automaticamente criadas para você. Ao usar ferramentas/APIs (exceto a API .NET), você define essas entidades do Data Factory usando o formato JSON.  Para obter um exemplo com definições de JSON para entidades do Data Factory que são usadas para copiar dados do armazenamento de dados MongoDB local, confira a seção [Exemplo de JSON: Copiar dados do MongoDB para o Blob do Azure](#json-example-copy-data-from-mongodb-to-azure-blob) neste artigo.

As seções que se seguem fornecem detalhes sobre as propriedades JSON que são usadas para definir entidades do Data Factory específicas a uma origem do MongoDB:

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado
A tabela a seguir fornece a descrição para elementos JSON específicos do serviço vinculado **OnPremisesMongoDB** .

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| type |A propriedade type deve ser definida como: **OnPremisesMongoDb** |Sim |
| server |Endereço IP ou nome do host do servidor MongoDB. |Sim |
| port |A porta TCP usada pelo servidor MongoDB para ouvir conexões de cliente. |Opcional, valor padrão: 27017 |
| authenticationType |Básica ou Anônima. |Sim |
| username |Conta de usuário para acessar o MongoDB. |Sim (se a autenticação básica for usada). |
| password |Senha do usuário. |Sim (se a autenticação básica for usada). |
| authSource |Nome do banco de dados MongoDB que você deseja usar para verificar suas credenciais para autenticação. |Opcional (se a autenticação básica for usada). Padrão: usa a conta de administrador e o banco de dados especificado usando a propriedade databaseName. |
| databaseName |Nome do banco de dados MongoDB que você deseja acessar. |Sim |
| gatewayName |Nome do gateway que acessa o armazenamento de dados. |Sim |
| encryptedCredential |Credencial criptografada pelo gateway. |Opcional |

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de dados, confira o artigo [Criando conjuntos de dados](data-factory-create-datasets.md). As seções como structure, availability e policy de um conjunto de dados JSON são similares para todos os tipos de conjunto de dados (SQL Azure, Blob do Azure, Tabela do Azure etc.).

A seção **typeProperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no armazenamento de dados. A seção typeProperties para o conjunto de dados do tipo **MongoDbCollection** tem as seguintes propriedades:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| collectionName |Nome da coleção no banco de dados MongoDB. |Sim |

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia
Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Criando pipelines](data-factory-create-pipelines.md). As propriedades, como nome, descrição, tabelas de entrada e saída, e política, estão disponíveis para todos os tipos de atividades.

As propriedades disponíveis na seção **typeProperties** da atividade, por outro lado, variam de acordo com cada tipo de atividade. Para a atividade de cópia, elas variam de acordo com os tipos de fonte e coletor.

Quando a fonte é do tipo **MongoDbSource** , as seguintes propriedades estão disponíveis na seção typeProperties:

| Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| query |Utiliza a consulta personalizada para ler os dados. |Cadeia de consulta SQL-92. Por exemplo: select * from MyTable. |Não (se **collectionName** de **dataset** for especificado) |



## <a name="json-example-copy-data-from-mongodb-to-azure-blob"></a>Exemplo JSON: Copiar dados do MongoDB para o Blob do Azure
Este exemplo fornece as definições de JSON de exemplo que você pode usar para criar um pipeline usando o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [do Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ele mostra como copiar dados de um banco de dados MongoDB local para um Armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados para qualquer um dos coletores declarados [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a Atividade de Cópia no Azure Data Factory.

O exemplo tem as seguintes entidades de data factory:

1. Um serviço vinculado do tipo [OnPremisesMongoDb](#linked-service-properties).
2. Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [MongoDbCollection](#dataset-properties).
4. Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Um [pipeline](data-factory-create-pipelines.md) com a Atividade de Cópia que usa [MongoDbSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo copia dados de um resultado de consulta no banco de dados MongoDB para um blob a cada hora. As propriedades JSON usadas nesses exemplos são descritas nas seções após os exemplos.

Como uma primeira etapa, configure o gateway de gerenciamento de dados de acordo com as instruções no artigo [Gateway de Gerenciamento de Dados](data-factory-data-management-gateway.md) .

**Serviço vinculado ao MongoDB:**

```json
{
    "name": "OnPremisesMongoDbLinkedService",
    "properties":
    {
        "type": "OnPremisesMongoDb",
        "typeProperties":
        {
            "authenticationType": "<Basic or Anonymous>",
            "server": "< The IP address or host name of the MongoDB server >",
            "port": "<The number of the TCP port that the MongoDB server uses to listen for client connections.>",
            "username": "<username>",
            "password": "<password>",
            "authSource": "< The database that you want to use to check your credentials for authentication. >",
            "databaseName": "<database name>",
            "gatewayName": "<mygateway>"
        }
    }
}
```

**Serviço vinculado de armazenamento do Azure:**

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

**Conjunto de dados de entrada do MongoDB:** Configurar "external": "true" informa ao serviço Data Factory que a tabela é externa ao Data Factory e não é produzida por uma atividade no Data Factory.

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

**Conjunto de dados de saída de Blob do Azure:**

Os dados são gravados em um novo blob a cada hora (frequência: horas, intervalo: 1). O caminho de pasta para o blob é avaliado dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa as partes ano, mês, dia e horas da hora de início.

```json
{
    "name": "AzureBlobOutputDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/frommongodb/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
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
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Uma atividade de cópia em um pipeline com origem no MongoDB e coletor Blob:**

O pipeline contém uma Atividade de Cópia que está configurada para usar os conjuntos de dados de entrada e saída acima e agendada para ser executada a cada hora. Na definição JSON do pipeline, o tipo **source** está definido como **MongoDbSource** e o tipo **sink** está definido como **BlobSink**. A consulta SQL especificada para a propriedade **query** seleciona os dados na última hora para copiar.

```json
{
    "name": "CopyMongoDBToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "MongoDbSource",
                        "query": "$$Text.Format('select * from MyTable where LastModifiedDate >= {{ts\'{0:yyyy-MM-dd HH:mm:ss}\'}} AND LastModifiedDate < {{ts\'{1:yyyy-MM-dd HH:mm:ss}\'}}', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "MongoDbInputDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutputDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "MongoDBToAzureBlob"
            }
        ],
        "start": "2016-06-01T18:00:00Z",
        "end": "2016-06-01T19:00:00Z"
    }
}
```


## <a name="schema-by-data-factory"></a>Esquema do Data Factory
O serviço Azure Data Factory infere o esquema de uma coleção do MongoDB usando os 100 documentos mais recentes na coleção. Se esses 100 documentos não contiverem o esquema completo, algumas colunas poderão ser ignoradas durante a operação de cópia.

## <a name="type-mapping-for-mongodb"></a>Mapeamento de tipo para o MongoDB
Como mencionado no artigo sobre as [Atividades de movimentação de dados](data-factory-data-movement-activities.md) , a Atividade de Cópia executa conversões automáticas dos tipos de fonte nos tipos de coletor com a seguinte abordagem de duas etapas:

1. Converter de tipos de fonte nativos para o tipo .NET
2. Converter do tipo .NET para o tipo de coletor nativo

Ao mover dados para o MongoDB, os seguintes mapeamentos serão usados dos tipos do MongoDB para os tipos do .NET.

| Tipo do MongoDB | Tipo .NET Framework |
| --- | --- |
| Binary |Byte[] |
| Boolean |Boolean |
| Date |DateTime |
| NumberDouble |Duplo |
| NumberInt |Int32 |
| NumberLong |Int64 |
| ObjectID |String |
| String |String |
| UUID |Guid |
| Objeto |Renormalizado para colunas simples com “_” como separador aninhado |

> [!NOTE]
> Para saber mais sobre o suporte para matrizes usando tabelas virtuais, consulte a seção [Suporte para tipos complexos usando tabelas virtuais](#support-for-complex-types-using-virtual-tables) abaixo.

Atualmente, os seguintes tipos de dados do MongoDB não têm suporte: DBPointer, JavaScript, chave Máx./Mín., Expressão Regular, Símbolo, Carimbo de Data/Hora, Indefinido

## <a name="support-for-complex-types-using-virtual-tables"></a>Suporte para tipos complexos usando tabelas virtuais
O Azure Data Factory usa um driver ODBC interno para se conectar ao banco de dados MongoDB e copiar dados dele. Para tipos complexos, como matrizes ou objetos com tipos diferentes nos documentos, o driver renormaliza os dados em tabelas virtuais correspondentes. Especificamente, se uma tabela contiver tais colunas, o driver vai gerar as seguintes tabelas virtuais:

* Uma **tabela base**, que contém os mesmos dados da tabela real, exceto nas colunas de tipo complexo. A tabela base usa o mesmo nome da tabela real que ela representa.
* Uma **tabela virtual** para cada coluna de tipo complexo, que expande os dados aninhados. As tabelas virtuais ão nomeadas usando o nome da tabela real, um separador “_” e o nome da matriz ou objeto.

As tabelas virtuais se referem aos dados na tabela real, permitindo que o driver acesse dados desordenados. Veja a seção Exemplo abaixo para obter detalhes. Você pode acessar o conteúdo das matrizes do MongoDB consultando e unindo as tabelas virtuais.

Você pode usar o [Assistente de Cópia](data-factory-data-movement-activities.md#create-a-pipeline-with-copy-activity) para exibir intuitivamente a lista de tabelas no banco de dados MongoDB, incluindo as tabelas virtuais, e visualizar os dados internos. Também é possível construir uma consulta no Assistente de Cópia e validar para ver o resultado.

### <a name="example"></a>Exemplo
Por exemplo, "TabelaDeExemplo" abaixo é uma tabela do MongoDB com uma coluna com uma matriz de Objetos em cada célula – Faturas, e uma coluna com uma matriz de tipos escalares – Classificações.

| _id | Nome do Cliente | Faturas | Nível de Serviço | Classificações |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id:”123”, item:”torradeira”, price:”456”, discount:”0,2”}, {invoice_id:”124”, item:”forno”,price: ”1235”,discount: ”0,2”}] |Silver |[5,6] |
| 2222 |XYZ |[{invoice_id:”135”, item:”fridge”, price: ”12543”, discount: ”0.0”}] |Gold |[1,2] |

O driver geraria várias tabelas virtuais para representar essa tabela única. A primeira tabela virtual é a tabela base chamada "ExampleTable", mostra abaixo. A tabela base contém todos os dados da tabela original, mas os dados das matrizes foram omitidos e são expandidos nas tabelas virtuais.

| _id | Nome do Cliente | Nível de Serviço |
| --- | --- | --- |
| 1111 |ABC |Silver |
| 2222 |XYZ |Gold |

As tabelas a seguir mostram as tabelas virtuais que representam as matrizes originais no exemplo. Essas tabelas contém o seguinte:

* Uma referência à coluna de chave primária original correspondente para a linha da matriz original (por meio da coluna _id)
* Uma indica da posição dos dados dentro da matriz original
* Os dados expandidos para cada elemento da matriz

Tabela "TabelaDeExemplo_Faturas":

| _id | TabelaDeExemplo_Faturas_dim1_idx | invoice_id | item | preço | Desconto |
| --- | --- | --- | --- | --- | --- |
| 1111 |0 |123 |torradeira |456 |0,2 |
| 1111 |1 |124 |forno |1235 |0,2 |
| 2222 |0 |135 |geladeira |12543 |0,0 |

Tabela "TabelaDeExemplo_Classificações":

| _id | TabelaDeExemplo_Classificações_dim1_idx | TabelaDeExemplo_Classificações |
| --- | --- | --- |
| 1111 |0 |5 |
| 1111 |1 |6 |
| 2222 |0 |1 |
| 2222 |1 |2 |

## <a name="map-source-to-sink-columns"></a>Mapear origem para colunas de coletor
Para saber mais sobre mapeamento de colunas no conjunto de dados de origem para colunas no conjunto de dados de coletor, confira [Mapping dataset columns in Azure Data Factory](data-factory-map-columns.md) (Mapeamento de colunas de conjunto de dados no Azure Data Factory).

## <a name="repeatable-read-from-relational-sources"></a>Leitura repetida de fontes relacionais
Ao copiar dados de armazenamentos de dados relacionais, lembre-se da capacidade de repetição para evitar resultados não intencionais. No Azure Data Factory, você pode repetir a execução de uma fatia manualmente. Você também pode configurar a política de repetição para um conjunto de dados de modo que uma fatia seja executada novamente quando ocorrer uma falha. Quando uma fatia é executada novamente, seja de que maneira for, você precisa garantir que os mesmos dados sejam lidos não importa quantas vezes uma fatia seja executada. Confira [Leitura repetida de fontes relacionais](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Desempenho e Ajuste
Veja o [Guia de desempenho e ajuste da Atividade de Cópia](data-factory-copy-activity-performance.md) para saber mais sobre os principais fatores que afetam o desempenho da movimentação de dados (Atividade de Cópia) no Azure Data Factory, além de várias maneiras de otimizar esse processo.

## <a name="next-steps"></a>Próximas etapas
Veja o artigo [Mover dados entre locais e na nuvem](data-factory-move-data-between-onprem-and-cloud.md) para obter instruções passo a passo para criar um pipeline de dados que move dados de um armazenamento de dados local para um armazenamento de dados do Azure.
