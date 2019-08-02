---
title: Mover dados do Salesforce usando o Data Factory | Microsoft Docs
description: Saiba mais sobre como mover os dados do Salesforce usando o Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: dbe3bfd6-fa6a-491a-9638-3a9a10d396d1
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: d33172727d4c654614463f69b83f7802cf7fb905
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839604"
---
# <a name="move-data-from-salesforce-by-using-azure-data-factory"></a>Mover dados do Salesforce usando o Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](data-factory-salesforce-connector.md)
> * [Versão 2 (versão atual)](../connector-salesforce.md)

> [!NOTE]
> Este artigo se aplica à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte [Conector do Salesforce na V2](../connector-salesforce.md).

Este artigo descreve como você pode usar a Atividade de Cópia no Azure Data Factory para copiar os dados do Salesforce para qualquer armazenamento de dados listado na coluna Coletores tabela de [fontes de dados e coletores com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . Este artigo se baseia no artigo de [atividades de movimentação de dados](data-factory-data-movement-activities.md) , que apresenta uma visão geral de movimentação de dados com a Atividade de Cópia e combinações de armazenamentos de dados com suporte.

Atualmente, o Azure Data Factory dá suporte apenas para a movimentação dos dados do Salesforce para os [armazenamentos de dados do coletor com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats), mas não dá suporte para a movimentação dos dados de outros armazenamentos de dados para o Salesforce.

## <a name="supported-versions"></a>Versões com suporte
Este conector oferece suporte às seguintes edições do Salesforce: Developer Edition, Professional Edition, Enterprise Edition ou Unlimited Edition. E ele dá suporte à cópia na produção, da área restrita e do domínio personalizado do Salesforce.

## <a name="prerequisites"></a>Pré-requisitos
* A permissão de API deve estar habilitada. Consulte [Como habilito o acesso à API no Salesforce por conjunto de permissões?](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)
* Para copiar os dados do Salesforce para os armazenamentos de dados locais, você deve ter, pelo menos, o Gateway de Gerenciamento de Dados 2.0 instalado no ambiente local.

## <a name="salesforce-request-limits"></a>Limites da solicitação Salesforce
O Salesforce tem limites para o total de solicitações de API e as solicitações simultâneas de API. Observe os seguintes pontos:

- Se o número de solicitações simultâneas exceder o limite, a limitação será atingida e você verá falhas aleatórias.
- Se o número total de solicitações exceder o limite, a conta do Salesforce será bloqueada por 24 horas.

Você também pode receber o erro "REQUEST_LIMIT_EXCEEDED" em ambos os cenários. Veja a seção "Limites de Solicitações da API" no artigo [Salesforce Developer Limits](https://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf) (Limites do Desenvolvedor Salesforce) para obter detalhes.

## <a name="getting-started"></a>Introdução
Você pode criar um pipeline com uma atividade de cópia que mova dados do Salesforce usando diferentes ferramentas/APIs.

A maneira mais fácil de criar um pipeline é usar o **Assistente de Cópia**. Consulte [Tutorial: criar um pipeline usando o Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md) para ver um breve passo a passo sobre como criar um pipeline usando o Assistente de cópia de dados.

Você também pode usar as ferramentas abaixo para criar um pipeline: **Visual Studio**, **Azure PowerShell**, **modelo do Resource Manager**, **.NET API**, e **API REST**. Confira o [Tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo sobre a criação de um pipeline com uma atividade de cópia.

Ao usar as ferramentas ou APIs, você executa as seguintes etapas para criar um pipeline que move dados de um armazenamento de dados de origem para um armazenamento de dados de coletor:

1. Criar **serviços vinculados** para vincular repositórios de dados de entrada e saída ao seu data factory.
2. Criar **conjuntos de dados** para representar dados de entrada e saída para a operação de cópia.
3. Criar um **pipeline** com uma atividade de cópia que usa um conjunto de dados como uma entrada e um conjunto de dados como uma saída.

Ao usar o assistente, as definições de JSON para essas entidades do Data Factory (serviços vinculados, conjuntos de dados e o pipeline) são automaticamente criadas para você. Ao usar ferramentas/APIs (exceto a API .NET), você define essas entidades do Data Factory usando o formato JSON. Para obter um exemplo com definições de JSON para entidades do Data Factory que são usadas para copiar dados do Salesforce, consulte a seção [Exemplo de JSON: copiar dados do Salesforce para o Blob do Azure](#json-example-copy-data-from-salesforce-to-azure-blob) neste artigo.

As seções que se seguem fornecem detalhes sobre as propriedades JSON que são usadas para definir entidades do Data Factory específicas ao Salesforce:

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado
A tabela a seguir fornece descrições dos elementos JSON específicos para o serviço vinculado Salesforce.

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| type |A propriedade type deve ser definida como: **Salesforce**. |Sim |
| environmentUrl | Especifica a URL da instância do Salesforce. <br><br> – O padrão é "https:\//login.salesforce.com". <br> – Para copiar dados da área restrita, especifique “https://test.salesforce.com”. <br> – Para copiar dados do domínio personalizado, especifique, por exemplo, "https://[domínio].my.salesforce.com". |Não |
| username |Especifique um nome de usuário para a conta de usuário. |Sim |
| password |Especifique um senha para a conta de usuário. |Sim |
| securityToken |Especifique um token de segurança para a conta de usuário. Veja [Obter token de segurança](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) para ver instruções sobre como redefinir/obter o token de segurança. Para saber mais sobre os tokens de segurança em geral, veja [Security and the API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm) (Segurança e a API). |Sim |

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, veja o artigo [Criando conjuntos de dados](data-factory-create-datasets.md) . As seções como structure, availability e policy de um conjunto de dados JSON são similares para todos os tipos de conjunto de dados (SQL Azure, Blob do Azure, Tabela do Azure e outros).

A seção **typeProperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no armazenamento de dados. A seção typeProperties para um conjunto de dados do tipo **RelationalTable** tem as propriedades a seguir:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| tableName |Nome da tabela no Salesforce. |Não (se uma **consulta** de **RelationalSource** for especificada) |

> [!IMPORTANT]
> A parte "__c" do Nome da API é necessária para qualquer objeto personalizado.

![Data Factory — conexão Salesforce — nome da API](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia
Para obter uma lista completa das seções e propriedades disponíveis para definir as atividades, veja o artigo [Criando pipelines](data-factory-create-pipelines.md) . As propriedades como o nome, descrição, tabelas de entrada e saída, e várias políticas estão disponíveis para todos os tipos de atividades.

As propriedades que estão disponíveis na seção typeProperties da atividade, por outro lado, variam de acordo com cada tipo de atividade. Para a Atividade de Cópia, elas variam de acordo com os tipos de fonte e coletor.

Em Atividade de Cópia, quando a origem for do tipo **RelationalSource** (que inclui Salesforce), as seguintes propriedades estarão disponíveis na seção typeProperties:

| Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| query |Utiliza a consulta personalizada para ler os dados. |Uma consulta SQL-92 ou uma consulta [SOQL (Salesforce Object Query Language)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) . Por exemplo: `select * from MyTable__c`. |Não (se **tableName** do **conjunto de dados** for especificado) |

> [!IMPORTANT]
> A parte "__c" do Nome da API é necessária para qualquer objeto personalizado.

![Data Factory — conexão Salesforce — nome da API](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)

## <a name="query-tips"></a>Dicas de consulta
### <a name="retrieving-data-using-where-clause-on-datetime-column"></a>Recuperando dados usando a cláusula where na coluna DateTime
Ao especificar a consulta SQL ou SOQL, preste atenção à diferença de formato DateTime. Por exemplo:

* **Exemplo de SOQL**:`$$Text.Format('SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= {0:yyyy-MM-ddTHH:mm:ssZ} AND LastModifiedDate < {1:yyyy-MM-ddTHH:mm:ssZ}', WindowStart, WindowEnd)`
* **Exemplo de SQL**:
    * **Use o assistente de cópia para especificar a consulta:** `$$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts\'{0:yyyy-MM-dd HH:mm:ss}\'}} AND LastModifiedDate < {{ts\'{1:yyyy-MM-dd HH:mm:ss}\'}}', WindowStart, WindowEnd)`
    * **Use a edição de JSON para especificar a consulta (escape char corretamente):** `$$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts\\'{0:yyyy-MM-dd HH:mm:ss}\\'}} AND LastModifiedDate < {{ts\\'{1:yyyy-MM-dd HH:mm:ss}\\'}}', WindowStart, WindowEnd)`

### <a name="retrieving-data-from-salesforce-report"></a>Recuperando dados do relatório do Salesforce
Você pode recuperar dados de relatórios do Salesforce especificando a consulta como `{call "<report name>"}`, por exemplo. `"query": "{call \"TestReport\"}"`.

### <a name="retrieving-deleted-records-from-salesforce-recycle-bin"></a>Recuperar registros excluídos da lixeira do Salesforce
Para consultar os registros excluídos pelo software da lixeira do Salesforce, você poderá especificar **"IsDeleted = 1"** em sua consulta. Por exemplo,

* Para consultar apenas os registros excluídos, especifique "select * from MyTable__c **where IsDeleted= 1**"
* Para consultar todos os registros, incluindo existentes e excluídos, especifique "select * from MyTable__c **m que IsDeleted = 0 ou IsDeleted = 1**"

## <a name="json-example-copy-data-from-salesforce-to-azure-blob"></a>Exemplo JSON: copiar dados do Salesforce para o Blob do Azure
O exemplo a seguir fornece as definições de JSON de exemplo que você pode usar para criar um pipeline usando o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [do Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Eles mostram como copiar dados do Salesforce para o Armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados para qualquer um dos coletores declarados [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a Atividade de Cópia no Azure Data Factory.

Aqui estão os artefatos Data Factory que você precisará criar para implementar o cenário. As seções depois da lista fornecem detalhes sobre essas etapas.

* Um serviço vinculado do tipo [Salesforce](#linked-service-properties)
* Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
* Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [RelationalTable](#dataset-properties)
* Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
* Um [pipeline](data-factory-create-pipelines.md) com Atividade de Cópia que usa [RelationalSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)

**Serviço vinculado Salesforce**

Este exemplo usa o serviço vinculado **Salesforce** . Veja a seção [Serviço vinculado do Salesforce](#linked-service-properties) para ver as propriedades com suporte para esse serviço vinculado. Veja [Obter token de segurança](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) para ver instruções sobre como redefinir/obter o token de segurança.

```json
{
    "name": "SalesforceLinkedService",
    "properties":
    {
        "type": "Salesforce",
        "typeProperties":
        {
            "username": "<user name>",
            "password": "<password>",
            "securityToken": "<security token>"
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
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```
**Conjunto de dados de entrada do Salesforce**

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

Configurar **external** como **true** informa ao serviço Data Factory que o conjunto de dados é externo ao Data Factory e não é produzido por uma atividade no Data Factory.

> [!IMPORTANT]
> A parte "__c" do Nome da API é necessária para qualquer objeto personalizado.

![Data Factory — conexão Salesforce — nome da API](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

**Conjunto de dados de saída do blob do Azure**

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
            "folderPath": "adfgetstarted/alltypes_c"
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

O pipeline contém uma Atividade de Cópia, que está configurada para usar os conjuntos de dados de entrada e saída, e está agendado para ser executado a cada hora. Na definição JSON do pipeline, o tipo **source** está definido como **RelationalSource** e o tipo **sink** está definido como **BlobSink**.

Veja [Propriedades do tipo RelationalSource](#copy-activity-properties) para obter a lista de propriedades com suporte para o RelationalSource.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2016-06-01T18:00:00",
        "end":"2016-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[
        {
            "name": "SalesforceToAzureBlob",
            "description": "Copy from Salesforce to an Azure blob",
            "type": "Copy",
            "inputs": [
            {
                "name": "SalesforceInput"
            }
            ],
            "outputs": [
            {
                "name": "AzureBlobOutput"
            }
            ],
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
        }
        ]
    }
}
```
> [!IMPORTANT]
> A parte "__c" do Nome da API é necessária para qualquer objeto personalizado.

![Data Factory — conexão Salesforce — nome da API](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)


### <a name="type-mapping-for-salesforce"></a>Mapeamento de tipo para Salesforce

| Tipo Salesforce | Tipo baseado no .NET |
| --- | --- |
| Numeração automática |String |
| Checkbox |Boolean |
| Currency |Decimal |
| Date |DateTime |
| Date/Time |DateTime |
| Email |String |
| Id |String |
| Relação de pesquisa |String |
| Lista de seleção múltipla |String |
| Number |Decimal |
| Percent |Decimal |
| Telefone |String |
| Lista de seleção |String |
| Texto |String |
| Área de texto |String |
| Área de texto (longo) |String |
| Área de texto (Rich) |String |
| Texto (criptografado) |String |
| URL |string |

> [!NOTE]
> Para mapear colunas de conjunto de dados de origem para colunas do conjunto de dados de coletor, confira [Mapeando colunas de conjunto de dados no Azure Data Factory](data-factory-map-columns.md).

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>Desempenho e ajuste
Veja o [Guia de desempenho e ajuste da Atividade de Cópia](data-factory-copy-activity-performance.md) para saber mais sobre os principais fatores que afetam o desempenho e a movimentação dos dados (Atividade de Cópia) no Azure Data Factory, além de várias maneiras de otimizar.
