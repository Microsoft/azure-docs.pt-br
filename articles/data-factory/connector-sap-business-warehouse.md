---
title: Copiar dados de SAP BW
description: Saiba como copiar dados do SAP Business Warehouse para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline do Azure Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/04/2019
ms.openlocfilehash: 4a5eb7c8d729fc0c3b95a61a4b8dbc8266d1eec3
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100386655"
---
# <a name="copy-data-from-sap-business-warehouse-using-azure-data-factory"></a>Copiar dados do SAP Business Warehouse usando o Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-sap-business-warehouse-connector.md)
> * [Versão atual](connector-sap-business-warehouse.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados de um banco de dados SAP BW (Business Warehouse). Ele amplia o artigo [Visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

>[!TIP]
>Para aprender o suporte geral do ADF no cenário de integração de dados do SAP, consulte [integração de dados SAP usando Azure data Factory White Paper](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) com introdução detalhada sobre cada conector SAP, análise e diretrizes.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Este conector do SAP Business Warehouse tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/coletor com suporte](copy-activity-overview.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)

Você pode copiar dados do SAP Business Warehouse para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte como origens/coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Especificamente, este conector do SAP Business Warehouse dá suporte:

- Ao SAP Business Warehouse **versão 7.x**.
- À cópia de dados do **InfoCubes e QueryCubes** (incluindo consultas BEx) usando consultas MDX.
- À cópia de dados usando a autenticação Básica.

## <a name="prerequisites"></a>Pré-requisitos

Para usar esse conector do SAP Business Warehouse, você precisa:

- Configurar um Integration Runtime auto-hospedado. Confira o artigo de [Integration Runtime auto-hospedado](create-self-hosted-integration-runtime.md) para obter detalhes.
- Instalar a **biblioteca SAP NetWeaver** no computador do Integration Runtime. Você pode obter a biblioteca do SAP NetWeaver do administrador do SAP ou diretamente do [centro de download de software SAP](https://support.sap.com/swdc). Pesquise pela **Nota SAP Nº 1025361** para obter o local de download da versão mais recente. Verifique se você selecionou a biblioteca do SAP NetWeaver de **64 bits** que corresponde à sua instalação do Integration Runtime. Em seguida, instale todos os arquivos incluídos no SDK RFC do SAP NetWeaver, de acordo com a Nota SAP. A biblioteca do SAP NetWeaver também está incluída na instalação das Ferramentas de Cliente SAP.

>[!TIP]
>Para solucionar o problema de conectividade para SAP BW, certifique-se:
>- Todas as bibliotecas de dependência extraídas do SDK do RFC NetWeaver estão em vigor na pasta %windir%\system32. Normalmente,cudt34.dll, icuin34.dll, icuuc34.dll, libicudecnumber.dll, librfc32.dll, libsapucum.dll, sapcrypto.dll, sapcryto_old.dll, sapnwrfc.dll.
>- As portas necessárias usadas para se conectar ao servidor SAP estão habilitadas no computador do IR auto-hospedado, que geralmente são portas 3300 e 3201.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções que se seguem fornecem detalhes sobre as propriedades que são usadas para definir entidades do Data Factory específicas ao conector do SAP Business Warehouse.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte no serviço vinculado do SAP BW (Business Warehouse):

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como: **SapBw** | Sim |
| Servidor | Nome do servidor no qual reside a instância do SAP BW. | Sim |
| systemNumber | Número de sistema do sistema SAP BW.<br/>Valor permitido: número decimal de dois dígitos representado como uma cadeia de caracteres. | Sim |
| clientId | ID de Cliente do cliente no sistema SAP W.<br/>Valor permitido: número decimal de três dígitos representado como uma cadeia de caracteres. | Sim |
| userName | Nome do usuário que tem acesso ao servidor SAP. | Sim |
| password | Senha do usuário. Marque este campo como uma SecureString para armazená-la com segurança no Data Factory ou [faça referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. É necessário um Integration Runtime auto-hospedado, conforme mencionado nos [Pré-requisitos](#prerequisites). |Sim |

**Exemplo:**

```json
{
    "name": "SapBwLinkedService",
    "properties": {
        "type": "SapBw",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
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

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [conjuntos de dados](concepts-datasets-linked-services.md). Esta seção fornece uma lista das propriedades com suporte pelo conjunto de dados do SAP BW.

Para copiar dados de SAP BW, defina a propriedade Type do conjunto de dado como **SapBwCube**. Enquanto não há propriedades específicas ao tipo com suporte para o conjunto de dados do SAP BW do tipo RelationalTable.

**Exemplo:**

```json
{
    "name": "SAPBWDataset",
    "properties": {
        "type": "SapBwCube",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP BW linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Se você estava usando o conjunto de dados com tipos `RelationalTable`, ele ainda tem suporte como está, mas é recomendável usar o novo de agora em diante.

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela fonte do SAP BW.

### <a name="sap-bw-as-source"></a>SAP BW como fonte

Para copiar dados de SAP BW, as propriedades a seguir têm suporte na seção **origem** da atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade Type da fonte da atividade de cópia deve ser definida como: **SapBwSource** | Sim |
| Consulta | Especifica a consulta MDX para ler dados da instância do SAP BW. | Sim |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromSAPBW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP BW input dataset name>",
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
                "type": "SapBwSource",
                "query": "<MDX query for SAP BW>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Se você estava usando a fonte com tipos `RelationalSource`, ela ainda tem suporte como está, mas é recomendável usar a nova no futuro.

## <a name="data-type-mapping-for-sap-bw"></a>Mapeamento de tipo de dados para SAP BW

Ao copiar dados do SAP BW, os seguintes mapeamentos são usados de tipos de dados do SAP BW para tipos de dados provisórios do Azure Data Factory. Consulte [Mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md) para saber mais sobre como a atividade de cópia mapeia o tipo de dados e esquema de origem para o coletor.

| Tipo de dados do SAP BW | Tipo de dados provisório do Data Factory |
|:--- |:--- |
| ACCP | int |
| CHAR | String |
| CLNT | String |
| CURR | Decimal |
| CUKY | String |
| DEC | Decimal |
| FLTP | Double |
| INT1 | Byte |
| INT2 | Int16 |
| INT4 | int |
| LANG | String |
| LCHR | String |
| LRAW | Byte[] |
| PREC | Int16 |
| QUAN | Decimal |
| RAW | Byte[] |
| RAWSTRING | Byte[] |
| STRING | String |
| UNIDADE | String |
| DATS | String |
| NUMC | String |
| TIMS | String |


## <a name="lookup-activity-properties"></a>Pesquisar propriedades de atividade

Para saber detalhes sobre as propriedades, verifique [Pesquisar atividade](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Azure Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
