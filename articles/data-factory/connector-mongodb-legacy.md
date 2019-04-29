---
title: Copiar dados do MongoDB usando o Azure Data Factory | Microsoft Docs
description: Saiba como copiar dados do MongoDB para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline do Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/20/2018
ms.author: jingwang
ms.openlocfilehash: 86dcd39ad7b9f1e207e9254ec72698db3998bbd6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61400467"
---
# <a name="copy-data-from-mongodb-using-azure-data-factory"></a>Copiar dados do MongoDB usando o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](v1/data-factory-on-premises-mongodb-connector.md)
> * [Versão atual](connector-mongodb.md)

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados de um banco de dados MongoDB. Ele amplia o artigo [Visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

>[!IMPORTANT]
>Versão do ADF um novo conector do MongoDB que fornece melhor MongoDB nativo oferece suporte à comparação para essa implementação baseada em ODBC, consulte o artigo [Conector do MongoDB](connector-mongodb.md) em detalhes. Este conector do MongoDB herdado é mantida com suporte tal como está para compatibilidade com versões anteriores, enquanto que para qualquer nova carga de trabalho, use o novo conector.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Você pode copiar dados de um banco de dados MongoDB para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte como origens/coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Especificamente, este conector do MongoDB dá suporte:

- MongoDB **versões 2.4, 2.6, 3.0, 3.2, 3.4 e 3.6**.
- À cópia de dados usando a autenticação **Básica** ou **Anônima**.

## <a name="prerequisites"></a>Pré-requisitos

Para copiar dados de um banco de dados do MongoDB que não esteja acessível publicamente, você precisará configurar um Integration Runtime auto-hospedado. Consulte o artigo [Integration Runtime auto-hospedado](create-self-hosted-integration-runtime.md) para saber mais detalhes. O Integration Runtime fornece um driver do MongoDB interno, portanto, não será necessário instalar manualmente nenhum driver ao copiar dados do MongoDB.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades usadas para definir entidades do Data Factory específicas ao conector do MongoDB.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado do MongoDB:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type |A propriedade type deve ser definida como: **MongoDb** |Sim |
| Servidor |Endereço IP ou nome do host do servidor MongoDB. |Sim |
| porta |A porta TCP usada pelo servidor MongoDB para ouvir conexões de cliente. |Não (o padrão é 27017) |
| databaseName |Nome do banco de dados MongoDB que você deseja acessar. |Sim |
| authenticationType | Tipo de autenticação usado para se conectar ao banco de dados MongoDB.<br/>Valores permitidos são: **Básico** e **Anônimo**. |Sim |
| Nome de Usuário |Conta de usuário para acessar o MongoDB. |Sim (se a autenticação básica for usada). |
| Senha |Senha do usuário. Marque este campo como uma SecureString para armazená-la com segurança no Data Factory ou [faça referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). |Sim (se a autenticação básica for usada). |
| authSource |Nome do banco de dados MongoDB que você deseja usar para verificar suas credenciais para autenticação. |Não. Para a autenticação Básica, o padrão é usar a conta do administrador e o banco de dados especificado, usando a propriedade databaseName. |
| enableSsl | Especifica se as conexões com o servidor são criptografadas usando SSL. O valor padrão é falso.  | Não  |
| allowSelfSignedServerCert | Especifica se deve permitir os certificados autoassinados do servidor. O valor padrão é falso.  | Não  |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o Integration Runtime auto-hospedado ou o Integration Runtime do Azure (se seu armazenamento de dados estiver publicamente acessível). Se não for especificado, ele usa o Integration Runtime padrão do Azure. |Não  |

**Exemplo:**

```json
{
    "name": "MongoDBLinkedService",
    "properties": {
        "type": "MongoDb",
        "typeProperties": {
            "server": "<server name>",
            "databaseName": "<database name>",
            "authenticationType": "Basic",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa de seções e propriedades disponíveis para definição de conjuntos de dados, consulte [Conjuntos de dados e serviços vinculados](concepts-datasets-linked-services.md). As propriedades a seguir têm suporte para o conjunto de dados do MongoDB:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type do conjunto de dados deve ser definida como: **MongoDbCollection** | Sim |
| collectionName |Nome da coleção no banco de dados MongoDB. |Sim |

**Exemplo:**

```json
{
    "name": "MongoDbDataset",
    "properties": {
        "type": "MongoDbCollection",
        "linkedServiceName": {
            "referenceName": "<MongoDB linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<Collection name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela fonte MongoDB.

### <a name="mongodb-as-source"></a>MongoDB como fonte

As propriedades a seguir têm suporte na seção **source** da atividade de cópia:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type da fonte da atividade de cópia deve ser definida como: **MongoDbSource** | Sim |
| query |Utiliza a consulta SQL-92 personalizada para ler os dados. Por exemplo: select * from MyTable. |Não (se "collectionName" no conjunto de dados for especificada) |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromMongoDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MongoDB input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "MongoDbSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

> [!TIP]
> Ao especificar a consulta SQL, preste atenção ao formato DateTime. Por exemplo: `SELECT * FROM Account WHERE LastModifiedDate >= '2018-06-01' AND LastModifiedDate < '2018-06-02'` ou usar o parâmetro `SELECT * FROM Account WHERE LastModifiedDate >= '@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}' AND LastModifiedDate < '@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'`

## <a name="schema-by-data-factory"></a>Esquema do Data Factory

O serviço Azure Data Factory infere o esquema de uma coleção do MongoDB usando os **100 documentos mais recentes** na coleção. Se esses 100 documentos não contiverem o esquema completo, algumas colunas poderão ser ignoradas durante a operação de cópia.

## <a name="data-type-mapping-for-mongodb"></a>Mapeamento de tipo de dados para o MongoDB

Ao copiar dados do MongoDB, os seguintes mapeamentos são usados de tipos de dados do MongoDB para tipos de dados intermediários do Azure Data Factory. Consulte [Mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md) para saber mais sobre como a atividade de cópia mapeia o tipo de dados e esquema de origem para o coletor.

| Tipo de dados do MongoDB | Tipo de dados provisório do Data Factory |
|:--- |:--- |
| Binário |Byte[] |
| Boolean |Boolean |
| Data |DateTime |
| NumberDouble |Double |
| NumberInt |Int32 |
| NumberLong |Int64 |
| ObjectID |Cadeia de caracteres |
| Cadeia de caracteres |Cadeia de caracteres |
| UUID |Guid |
| Object |Renormalizado para colunas simples com “_" como separador aninhado |

> [!NOTE]
> Para saber mais sobre o suporte para matrizes usando tabelas virtuais, consulte a seção [Suporte para tipos complexos usando tabelas virtuais](#support-for-complex-types-using-virtual-tables).
>
> Atualmente, não há suporte para os seguintes tipos de dados do MongoDB: DBPointer, JavaScript, Max/Min key, Regular Expression, Symbol, Timestamp, Undefined.

## <a name="support-for-complex-types-using-virtual-tables"></a>Suporte para tipos complexos usando tabelas virtuais

O Azure Data Factory usa um driver ODBC interno para se conectar ao banco de dados MongoDB e copiar dados dele. Para tipos complexos, como matrizes ou objetos com tipos diferentes nos documentos, o driver renormaliza os dados em tabelas virtuais correspondentes. Especificamente, se uma tabela contiver tais colunas, o driver vai gerar as seguintes tabelas virtuais:

* Uma **tabela base**, que contém os mesmos dados da tabela real, exceto nas colunas de tipo complexo. A tabela base usa o mesmo nome da tabela real que ela representa.
* Uma **tabela virtual** para cada coluna de tipo complexo, que expande os dados aninhados. As tabelas virtuais são nomeadas usando o nome da tabela real, um separador “_" e o nome da matriz ou do objeto.

As tabelas virtuais se referem aos dados na tabela real, permitindo que o driver acesse dados desordenados. Você pode acessar o conteúdo das matrizes do MongoDB consultando e unindo as tabelas virtuais.

### <a name="example"></a>Exemplo

Por exemplo, a ExampleTable mostrada aqui é uma tabela do MongoDB contendo uma coluna com uma matriz de Objetos em cada célula (Faturas) e uma coluna com uma matriz de tipos Escalares (Classificações).

| _id | Nome do Cliente | Faturas | Nível de Serviço | Classificações |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id:"123", item:"toaster", price:"456", discount:"0.2"}, {invoice_id:"124", item:"oven", price: "1235", desconto: "0.2"}] |Silver |[5,6] |
| 2222 |XYZ |[{invoice_id:"135", item:"fridge", price: "12543", desconto: "0.0"}] |Gold |[1,2] |

O driver geraria várias tabelas virtuais para representar essa tabela única. A primeira tabela virtual é a tabela base chamada "ExampleTable", mostrada no exemplo. A tabela base contém todos os dados da tabela original, mas os dados das matrizes foram omitidos e são expandidos nas tabelas virtuais.

| _id | Nome do Cliente | Nível de Serviço |
| --- | --- | --- |
| 1111 |ABC |Silver |
| 2222 |XYZ |Gold |

As tabelas a seguir mostram as tabelas virtuais que representam as matrizes originais no exemplo. Essas tabelas contém o seguinte:

* Uma referência à coluna de chave primária original correspondente para a linha da matriz original (por meio da coluna _id)
* Uma indica da posição dos dados dentro da matriz original
* Os dados expandidos para cada elemento da matriz

**Tabela “ExampleTable_Invoices":**

| _id | TabelaDeExemplo_Faturas_dim1_idx | invoice_id | item | preço | Desconto |
| --- | --- | --- | --- | --- | --- |
| 1111 |0 |123 |torradeira |456 |0,2 |
| 1111 |1 |124 |forno |1235 |0,2 |
| 2222 |0 |135 |geladeira |12543 |0,0 |

**Tabela “ExampleTable_Ratings":**

| _id | TabelaDeExemplo_Classificações_dim1_idx | TabelaDeExemplo_Classificações |
| --- | --- | --- |
| 1111 |0 |5 |
| 1111 |1 |6 |
| 2222 |0 |1 |
| 2222 |1 |2 |

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Azure Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md##supported-data-stores-and-formats).
