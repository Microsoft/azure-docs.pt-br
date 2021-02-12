---
title: Serviços vinculados no Azure Data Factory
description: Saiba mais sobre os serviços vinculados no Data Factory. Os serviços vinculados vinculam computação/armazenamentos de dados a um data factory.
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: anandsub
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/21/2020
ms.openlocfilehash: 172694363b482edf6d463aa74b85e81c91fa6a9c
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500097"
---
# <a name="linked-services-in-azure-data-factory"></a>Serviços vinculados no Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-create-datasets.md)
> * [Versão atual](concepts-linked-services.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve o que são serviços vinculados, como eles são definidos no formato JSON e como são usados em pipelines do Azure Data Factory.

Se estiver conhecendo o Azure Data Factory agora, consulte [Introdução ao Azure Data Factory](introduction.md) para obter uma visão geral.

## <a name="overview"></a>Visão geral

Uma fábrica de dados pode ter um ou mais pipelines. Um **pipeline** é um agrupamento lógico de **atividades** que juntas executam uma tarefa. As atividades em um pipeline definem ações para executar em seus dados. Por exemplo, você pode usar uma atividade de cópia para copiar dados de SQL Server para o armazenamento de BLOBs do Azure. Em seguida, poderá usar uma atividade do Hive que executa um script Hive em um cluster HDInsight do Azure a fim de processar dados do armazenamento de Blobs para gerar dados de saída. Por fim, você pode usar uma segunda atividade de cópia para copiar os dados de saída para o Azure Synapse Analytics, na parte superior das soluções de relatório de business intelligence (BI) criadas. Para obter mais informações sobre pipelines e atividades, consulte [Pipelines e atividades](concepts-pipelines-activities.md) no Azure Data Factory.

Por outro lado, um **conjunto de dados** é uma exibição nomeada de dados que simplesmente aponta ou faz referência aos dados que você deseja usar em suas **atividades** como entradas e saídas.

Antes de criar um conjunto de dados, você deve criar um **serviço vinculado** para vincular seu armazenamento de dados com o data factory. Serviços vinculados são como cadeias de conexão, que definem as informações de conexão necessárias para o Data Factory para se conectar a recursos externos. Pense dessa maneira: o conjunto de dados representa a estrutura dos dados nos armazenamentos de dados vinculados e o serviço vinculado define a conexão à fonte de dados. Por exemplo, um serviço vinculado do Armazenamento do Azure vincula uma conta de armazenamento ao data factory. Um conjunto de dados de Blob do Azure representa o contêiner de blob e a pasta na conta de armazenamento do Azure que contém os blobs de entrada a serem processados.

Veja abaixo um cenário de exemplo. Para copiar dados do armazenamento de Blob em um Banco de Dados SQL, crie dois serviços vinculados: Armazenamento do Azure e Banco de Dados SQL do Azure. Em seguida, crie dois conjuntos de dados: O conjunto de dados de Blob do Azure (que se refere ao serviço vinculado do Armazenamento do Azure) e o conjunto de dados de Tabela do SQL do Azure (que se refere ao serviço vinculado do Banco de Dados SQL do Azure). Os serviços vinculados do Armazenamento do Azure e do Banco de Dados SQL do Azure contêm cadeias de conexão que o Data Factory usa em runtime para se conectar ao Armazenamento do Azure e ao Banco de Dados SQL do Azure, respectivamente. O conjunto de dados de Blob do Azure especifica o contêiner de blobs e a pasta de blobs que contém os blobs de entrada no armazenamento de Blobs. O conjunto de dados de Tabela do SQL do Azure especifica a tabela do SQL no banco de dados SQL no qual os dados serão copiados.

O seguinte diagrama mostra a relação entre pipeline, atividade, conjunto de dados e serviço vinculado no Data Factory:

![Relação entre pipeline, atividade e conjunto de dados, serviços vinculados](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)

## <a name="linked-service-json"></a>JOSN de serviço vinculado

Um serviço vinculado no Data Factory é definido no formato JSON da seguinte maneira:

```json
{
    "name": "<Name of the linked service>",
    "properties": {
        "type": "<Type of the linked service>",
        "typeProperties": {
              "<data store or compute-specific type properties>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

A tabela a seguir descreve as propriedades no JSON acima:

Propriedade | Descrição | Obrigatório |
-------- | ----------- | -------- |
name | Nome do serviço vinculado. Consulte [Azure Data Factory – Regras de nomenclatura](naming-rules.md). |  Sim |
type | Tipo de serviço vinculado. Por exemplo: AzureBlobStorage (armazenamento de dados) ou AzureBatch (computação). Consulte a descrição de typeProperties. | Sim |
typeProperties | As propriedades de tipo são diferentes para cada armazenamento de dados ou de computação. <br/><br/> Para os tipos de armazenamento de dados com suporte e suas propriedades de tipo, consulte o artigo [visão geral do conector](copy-activity-overview.md#supported-data-stores-and-formats) . Navegue até o artigo de conector do armazenamento de dados para saber mais sobre as propriedades de tipo específicas para um armazenamento de dados. <br/><br/> Para os tipos de computação suportados e suas propriedades de tipo, consulte [serviços vinculados de computação](compute-linked-services.md). | Sim |
connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o Integration Runtime do Azure ou o Integration Runtime auto-hospedado (se o armazenamento de dados estiver localizado em uma rede privada). Se não for especificado, ele usa o Integration Runtime padrão do Azure. | Não

## <a name="linked-service-example"></a>Exemplo de serviço vinculado

O serviço vinculado a seguir é um serviço vinculado do armazenamento de BLOBs do Azure. Observe que o tipo está definido como armazenamento de BLOBs do Azure. As propriedades de tipo para o serviço vinculado do armazenamento de BLOBs do Azure incluem uma cadeia de conexão. O serviço de Data Factory usa essa cadeia de conexão para se conectar ao armazenamento de dados em runtime.

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="create-linked-services"></a>Criar serviços vinculados

Os serviços vinculados podem ser criados no Azure Data Factory UX por meio do [Hub de gerenciamento](author-management-hub.md) e de quaisquer atividades, conjuntos de dados ou fluxos de dado que façam referência a eles.

Você pode criar serviços vinculados usando uma destas ferramentas ou SDKs: [API .net](quickstart-create-data-factory-dot-net.md), [PowerShell](quickstart-create-data-factory-powershell.md), [api REST](quickstart-create-data-factory-rest-api.md), modelo de Azure Resource Manager e portal do Azure.


## <a name="data-store-linked-services"></a>Serviços vinculados do armazenamento de dados

Você pode encontrar a lista de armazenamentos de dados compatíveis com o Data Factory do artigo [Visão geral do conector](copy-activity-overview.md#supported-data-stores-and-formats). Clique em um armazenamento de dados para saber mais sobre as propriedades de conexão compatíveis.

## <a name="compute-linked-services"></a>Serviços vinculados de computação

Consulte os [ambientes de computação compatíveis](compute-linked-services.md) para obter detalhes sobre diferentes ambientes de computação aos quais você pode se conectar de seu data factory, bem como as diferentes configurações.

## <a name="next-steps"></a>Próximas etapas

Consulte os seguintes tutoriais para obter instruções passo a passo para criar pipelines e conjuntos de dados usando uma destas ferramentas ou SDKs.

- [Início rápido: criar um data factory usando o .NET](quickstart-create-data-factory-dot-net.md)
- [Início rápido: criar um data factory usando o PowerShell](quickstart-create-data-factory-powershell.md)
- [Início rápido: criar um data factory usando a API REST](quickstart-create-data-factory-rest-api.md)
- [Início rápido: criar um data factory usando o portal do Azure](quickstart-create-data-factory-portal.md)
