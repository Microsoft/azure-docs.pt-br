---
title: Copiar dados do SAP ECC usando o Azure Data Factory | Microsoft Docs
description: Saiba como copiar dados do SAP ECC para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline do Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: jingwang
ms.openlocfilehash: 7ccd2e7a804c6495f6caf5e264b1f7c2a36cb02e
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827782"
---
# <a name="copy-data-from-sap-ecc-by-using-azure-data-factory"></a>Copiar dados do SAP ECC usando o Azure Data Factory

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados do SAP Enterprise Central ECC (componente). Para obter mais informações, consulte [visão geral da atividade de cópia](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Você pode copiar dados de um SAP ECC para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados que têm suporte como fontes ou coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Especificamente, este conector do SAP ECC dá suporte à:

- Copiar dados do SAP ECC no SAP NetWeaver versão 7.0 e versões posterior.
- Copiando dados de todos os objetos expostos pelos serviços SAP ECC OData, tais como:

  - SAP tabelas ou exibições.
  - Objetos de Interface de programação de aplicativo [BAPI] comerciais.
  - Extratores de dados.
  - Os dados ou documentos intermediários (IDOCs) enviados a integração de processo SAP (PI) que podem ser recebidos como OData por meio de adaptadores relativos.

- Copiar dados usando a autenticação básica.

>[!TIP]
>Para copiar dados do SAP ECC por meio de uma exibição ou tabela SAP, use o [tabela SAP](connector-sap-table.md) conector, que é mais rápido e mais escalonável.

## <a name="prerequisites"></a>Pré-requisitos

Geralmente, o SAP ECC expõe entidades por meio de serviços OData através do Gateway do SAP. Para usar esse conector do SAP ECC, é necessário:

- **Configurar o Gateway do SAP**. Para servidores com o SAP NetWeaver versões mais recentes que 7.4, o Gateway do SAP já está instalado. Para versões anteriores, você deve instalar o Gateway SAP incorporado ou o sistema de hub do SAP Gateway antes de expor dados SAP ECC através de serviços OData. Para configurar o Gateway do SAP, consulte a [guia de instalação](https://help.sap.com/saphelp_gateway20sp12/helpdata/en/c3/424a2657aa4cf58df949578a56ba80/frameset.htm).

- **Ativar e configurar o serviço OData SAP**. Você pode ativar o serviço OData por meio do TCODE SICF em segundos. Você também pode configurar quais objetos precisam ser expostos. Para obter mais informações, consulte o [orientação passo a passo](https://blogs.sap.com/2012/10/26/step-by-step-guide-to-build-an-odata-service-based-on-rfcs-part-1/).

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que são usadas para definir entidades do Data Factory específicas ao conector do SAP ECC.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado do SAP ECC:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| `type` | O `type` propriedade deve ser definida como `SapEcc`. | Sim |
| `url` | A URL do serviço OData do SAP ECC. | Sim |
| `username` | O nome de usuário usado para se conectar ao SAP ECC. | Não |
| `password` | A senha de texto sem formatação usada para se conectar ao SAP ECC. | Não |
| `connectVia` | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar um tempo de execução de integração auto-hospedado ou o tempo de execução de integração do Azure (se o armazenamento de dados está acessível publicamente). Se você não especificar um tempo de execução, `connectVia` usa o tempo de execução de integração do Azure padrão. | Não |

### <a name="example"></a>Exemplo

```json
{
    "name": "SapECCLinkedService",
    "properties": {
        "type": "SapEcc",
        "typeProperties": {
            "url": "<SAP ECC OData URL, e.g., http://eccsvrname:8000/sap/opu/odata/sap/zgw100_dd02l_so_srv/>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        }
    },
    "connectVia": {
        "referenceName": "<name of integration runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de dados, consulte [conjuntos de dados](concepts-datasets-linked-services.md). A seção a seguir fornece uma lista das propriedades com suporte pelo conjunto de dados SAP ECC.

Para copiar dados do SAP ECC, defina as `type` propriedade do conjunto de dados como `SapEccResource`.

Há suporte para as seguintes propriedades:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| `path` | Caminho da entidade OData do SAP ECC. | Sim |

### <a name="example"></a>Exemplo

```json
{
    "name": "SapEccDataset",
    "properties": {
        "type": "SapEccResource",
        "typeProperties": {
            "path": "<entity path, e.g., dd04tentitySet>"
        },
        "linkedServiceName": {
            "referenceName": "<SAP ECC linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, consulte [Pipelines](concepts-pipelines-activities.md). A seção a seguir fornece uma lista das propriedades com suporte pela fonte do SAP ECC.

### <a name="sap-ecc-as-a-source"></a>SAP ECC como origem

Para copiar dados do SAP ECC, defina as `type` propriedade em de `source` seção da atividade de cópia para `SapEccSource`.

As propriedades a seguir têm suporte na atividade de cópia `source` seção:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| `type` | O `type` propriedade da atividade de cópia `source` seção deve ser definida como `SapEccSource`. | Sim |
| `query` | As opções de consulta OData para filtrar os dados. Por exemplo:<br/><br/>`"$select=Name,Description&$top=10"`<br/><br/>O conector do SAP ECC copia dados da URL combinada:<br/><br/>`<URL specified in the linked service>/<path specified in the dataset>?<query specified in the copy activity's source section>`<br/><br/>Para saber mais, confira as [Componentes da URL do OData](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Não |

### <a name="example"></a>Exemplo

```json
"activities":[
    {
        "name": "CopyFromSAPECC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP ECC input dataset name>",
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
                "type": "SapEccSource",
                "query": "$top=10"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mappings-for-sap-ecc"></a>Mapeamentos de tipo de dados para SAP ECC

Quando você estiver copiando dados do SAP ECC, os seguintes mapeamentos são usados de tipos de dados do OData para dados SAP ECC para tipos de dados provisórios do Azure Data Factory. Para saber mais sobre como a atividade de cópia mapeia o tipo de dados e esquema de origem para o coletor, consulte [Mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md).

| Tipo de dados OData | Tipo de dados provisório do Data Factory |
|:--- |:--- |
| `Edm.Binary` | `String` |
| `Edm.Boolean` | `Bool` |
| `Edm.Byte` | `String` |
| `Edm.DateTime` | `DateTime` |
| `Edm.Decimal` | `Decimal` |
| `Edm.Double` | `Double` |
| `Edm.Single` | `Single` |
| `Edm.Guid` | `String` |
| `Edm.Int16` | `Int16` |
| `Edm.Int32` | `Int32` |
| `Edm.Int64` | `Int64` |
| `Edm.SByte` | `Int16` |
| `Edm.String` | `String` |
| `Edm.Time` | `TimeSpan` |
| `Edm.DateTimeOffset` | `DateTimeOffset` |

> [!NOTE]
> Atualmente, não há suporte para tipos de dados complexos.

## <a name="next-steps"></a>Próximas etapas

Para obter uma lista dos armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Azure Data Factory, consulte [armazenamentos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats).
