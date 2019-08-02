---
title: Mover dados da Tabela da Web usando o Azure Data Factory | Microsoft Docs
description: Saiba mais sobre como mover dados de uma tabela em uma página da Web usando o Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: f54a26a4-baa4-4255-9791-5a8f935898e2
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/05/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 5b84e49bac35ef8fc9a6c8c7ca90bfd6048dc1c4
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839585"
---
# <a name="move-data-from-a-web-table-source-using-azure-data-factory"></a>Mover dados de uma fonte de tabela da Web usando o Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](data-factory-web-table-connector.md)
> * [Versão 2 (versão atual)](../connector-web-table.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte [Conector de Tabela da Web na V2 ](../connector-web-table.md).

Este artigo descreve como usar a Atividade de Cópia no Azure Data Factory para mover dados de uma tabela em uma página Web para um armazenamento de dados do coletor com suporte. Este artigo se baseia no artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md), que apresenta uma visão geral de movimentação de dados com a atividade de cópia e a lista de armazenamentos de dados com suporte, como fontes/coletores.

Atualmente, o data factory dá suporte apenas para a movimentação de dados de uma tabela da Web para outros armazenamentos de dados, mas não para a movimentação de dados de outros armazenamentos de dados para uma tabela da Web.

> [!IMPORTANT]
> No momento, esse conector da Web dá suporte apenas à extração do conteúdo da tabela de uma página HTML. Para recuperar dados de um ponto de extremidade HTTP/s, use o [conector HTTP](data-factory-http-connector.md) em vez disso.

## <a name="prerequisites"></a>Pré-requisitos

Para usar este conector de tabela da Web, você precisa configurar um Tempo de Execução de Integração Auto-hospedado (também conhecido como Gateway de Gerenciamento de Dados) e configurar a propriedade `gatewayName` no serviço vinculado do coletor. Por exemplo, para copiar da tabela da Web para o Armazenamento de Blobs do Azure, configure o serviço vinculado do Armazenamento do Azure da seguinte forma:

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>",
      "gatewayName": "<gateway name>"
    }
  }
}
```

## <a name="getting-started"></a>Introdução
Você pode criar um pipeline com atividade de cópia que mova dados de um armazenamento de dados local Cassandra usando diferentes ferramentas/APIs. 

- A maneira mais fácil de criar um pipeline é usar o **Assistente de Cópia**. Consulte [Tutorial: criar um pipeline usando o Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md) para ver um breve passo a passo sobre como criar um pipeline usando o Assistente de cópia de dados. 
- Você também pode usar as ferramentas abaixo para criar um pipeline: **Visual Studio**, **Azure PowerShell**, **modelo do Resource Manager**, **.NET API**, e **API REST**. Confira o [Tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo sobre a criação de um pipeline com uma atividade de cópia. 

Ao usar as ferramentas ou APIs, você executa as seguintes etapas para criar um pipeline que move dados de um armazenamento de dados de origem para um armazenamento de dados de coletor:

1. Criar **serviços vinculados** para vincular repositórios de dados de entrada e saída ao seu data factory.
2. Criar **conjuntos de dados** para representar dados de entrada e saída para a operação de cópia. 
3. Criar um **pipeline** com uma atividade de cópia que usa um conjunto de dados como uma entrada e um conjunto de dados como uma saída. 

Ao usar o assistente, as definições de JSON para essas entidades do Data Factory (serviços vinculados, conjuntos de dados e o pipeline) são automaticamente criadas para você. Ao usar ferramentas/APIs (exceto a API .NET), você define essas entidades do Data Factory usando o formato JSON.  Para obter um exemplo com definições de JSON para entidades do Data Factory que são usadas para copiar dados de uma tabela da Web, consulte a seção [Exemplo de JSON: copiar dados da tabela da Web para o Blob do Azure](#json-example-copy-data-from-web-table-to-azure-blob) neste artigo. 

As seções que se seguem fornecem detalhes sobre as propriedades JSON que são usadas para definir entidades do Data Factory específicas a uma tabela da Web:

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado
A tabela a seguir fornece a descrição para elementos JSON específicos para o serviço vinculado à Web.

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| type |A propriedade type deve ser definida como: **Web** |Sim |
| Url |URL para a origem da Web |Sim |
| authenticationType |Anônima. |Sim |

### <a name="using-anonymous-authentication"></a>Usando a autenticação anônima

```json
{
    "name": "web",
    "properties":
    {
        "type": "Web",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "url" : "https://en.wikipedia.org/wiki/"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de dados, confira o artigo [Criando conjuntos de dados](data-factory-create-datasets.md). As seções como structure, availability e policy de um conjunto de dados JSON são similares para todos os tipos de conjunto de dados (SQL Azure, Blob do Azure, Tabela do Azure etc.).

A seção **typeProperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no armazenamento de dados. A seção typeProperties para o conjunto de dados do tipo **WebTable** tem as propriedades a seguir

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type |tipo do conjunto de dados. Deve ser definido como **WebTable** |Sim |
| path |Uma URL relativa para o recurso que contém a tabela. |Nº Quando o caminho não for especificado, apenas a URL especificada na definição do serviço vinculado será usada. |
| index |O índice da tabela no recurso. Confira a seção [Obter índice de uma tabela em uma página HTML](#get-index-of-a-table-in-an-html-page) a fim de ver as etapas para obter o índice de uma tabela em uma página HTML. |Sim |

**Exemplo:**

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
            "interval":  1
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia
Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Criando pipelines](data-factory-create-pipelines.md). As propriedades, como nome, descrição, tabelas de entrada e saída, e política, estão disponíveis para todos os tipos de atividades.

Por outro lado, as propriedades disponíveis na seção typeProperties da atividade variam de acordo com cada tipo de atividade. Para a atividade de cópia, elas variam de acordo com os tipos de fonte e coletor.

Atualmente, quando a origem na atividade de cópia é do tipo **WebSource**, não há suporte para propriedades adicionais.


## <a name="json-example-copy-data-from-web-table-to-azure-blob"></a>Exemplo JSON: copiar dados de uma tabela da Web para o Blob do Azure
O exemplo a seguir mostra:

1. Um serviço vinculado do tipo [Web](#linked-service-properties).
2. Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [WebTable](#dataset-properties).
4. Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Um [pipeline](data-factory-create-pipelines.md) com a Atividade de Cópia que usa [WebSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo copia dados de uma tabela da Web para um blob do Azure a cada hora. As propriedades JSON usadas nesses exemplos são descritas nas seções após os exemplos.

O exemplo a seguir mostra como copiar dados de uma tabela da Web para um blob do Azure. No entanto, os dados podem ser copiados diretamente para qualquer um dos coletores declarados no artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md) usando a atividade de cópia no Azure Data Factory.

**Serviço vinculado à Web** Este exemplo usa o serviço vinculado à Web com autenticação anônima. Confira a seção [Serviço vinculado à Web](#linked-service-properties) para ver os diferentes tipos de autenticação que você pode usar.

```json
{
    "name": "WebLinkedService",
    "properties":
    {
        "type": "Web",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "url" : "https://en.wikipedia.org/wiki/"
        }
    }
}
```

**Serviço vinculado de armazenamento do Azure**

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>",
      "gatewayName": "<gateway name>"
    }
  }
}
```

**Conjunto de dados de entrada WebTable** Configurar **external** como **true** informa ao serviço Data Factory que o conjunto de dados é externo ao data factory e não é produzido por uma atividade no data factory.

> [!NOTE]
> Confira a seção [Obter índice de uma tabela em uma página HTML](#get-index-of-a-table-in-an-html-page) a fim de ver as etapas para obter o índice de uma tabela em uma página HTML.  
>
>

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
            "interval":  1
        }
    }
}
```


**Conjunto de dados de saída de Blob do Azure**

Os dados são gravados em um novo blob a cada hora (frequência: horas, intervalo: 1).

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties":
        {
            "folderPath": "adfgetstarted/Movies"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```



**Pipeline com Atividade de cópia**

O pipeline contém uma Atividade de Cópia que está configurada para usar os conjuntos de dados de entrada e saída e é agendada para ser executada a cada hora. Na definição JSON do pipeline, o tipo **source** está definido como **WebSource** e o tipo **sink** está definido como **BlobSink**.

Confira Propriedades do tipo WebSource para obter a lista de propriedades com suporte da WebSource.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[  
      {
        "name": "WebTableToAzureBlob",
        "description": "Copy from a Web table to an Azure blob",
        "type": "Copy",
        "inputs": [
          {
            "name": "WebTableInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
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
      }
      ]
   }
}
```

## <a name="get-index-of-a-table-in-an-html-page"></a>Obter índice de uma tabela em uma página HTML
1. Inicie o **Excel 2016** e alterne para a guia **Dados**.  
2. Clique em **Nova Consulta** na barra de ferramentas, aponte para **De Outras Fontes** e clique em **Da Web**.

    ![Menu do Power Query](./media/data-factory-web-table-connector/PowerQuery-Menu.png)
3. Na caixa de diálogo **Da Web**, insira a **URL** que você usaria no JSON de serviço vinculado (por exemplo: https://en.wikipedia.org/wiki/) ) juntamente com o caminho que você especificaria para o conjunto de dados (por exemplo: AFI 27s_100_Years de %... 100_Movies) e clique em **OK**.

    ![Do diálogo da Web](./media/data-factory-web-table-connector/FromWeb-DialogBox.png)

    URL usada neste exemplo: https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies
4. Se você vir a caixa de diálogo **Acessar conteúdo da Web**, selecione a **autenticação** de **URL** correta e clique em **Conectar**.

   ![Acessar caixa de diálogo de conteúdo da Web](./media/data-factory-web-table-connector/AccessWebContentDialog.png)
5. Clique em um item de **tabela** na exibição de árvore para ver o conteúdo da tabela e clique em **Editar** na parte inferior.  

   ![Diálogo de navegador](./media/data-factory-web-table-connector/Navigator-DialogBox.png)
6. Na janela **Editor de Consultas**, clique no botão **Editor Avançado** na barra de ferramentas.

    ![Botão Editor Avançado](./media/data-factory-web-table-connector/QueryEditor-AdvancedEditorButton.png)
7. Na caixa de diálogo Editor Avançado, o número ao lado de "Origem" é o índice.

    ![Editor Avançado – índice](./media/data-factory-web-table-connector/AdvancedEditor-Index.png)

Se você estiver usando o Excel 2013, use o [Microsoft Power Query para Excel](https://www.microsoft.com/download/details.aspx?id=39379) a fim de obter o índice. Confira o artigo [Conectar-se a uma página da Web](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8) para obter detalhes. As etapas são semelhantes se você estiver usando o [Microsoft Power BI para Desktop](https://powerbi.microsoft.com/desktop/).

> [!NOTE]
> Para mapear colunas de conjunto de dados de origem para colunas do conjunto de dados de coletor, confira [Mapeando colunas de conjunto de dados no Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Desempenho e Ajuste
Veja o [Guia de desempenho e ajuste da Atividade de Cópia](data-factory-copy-activity-performance.md) para saber mais sobre os principais fatores que afetam o desempenho da movimentação de dados (Atividade de Cópia) no Azure Data Factory, além de várias maneiras de otimizar esse processo.
