---
title: Copiar dados do Dynamics AX usando o Azure Data Factory (visualização)|Microsoft Docs
description: Saiba como copiar dados do Dynamics AX para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline do Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: jingwang
ms.openlocfilehash: 05bd4fdd220b47b11dfed9857dbc8dbe25b236df
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61347772"
---
# <a name="copy-data-from-dynamics-ax-by-using-azure-data-factory-preview"></a>Copiar dados do Dynamics AX usando o Azure Data Factory (visualização)

Este artigo descreve como usar a Atividade de Cópia no Azure Data Factory para copiar dados de uma fonte Dynamics AX. O artigo baseia-se em [Atividade de Cópia no Azure Data Factory](copy-activity-overview.md), que apresenta uma visão geral da Atividade de Cópia.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Você pode copiar dados do Dynamics AX para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados que o Copy Activity suporta como fontes e coletores, consulte [Armazenamentos de dados e formatos compatíveis](copy-activity-overview.md#supported-data-stores-and-formats).

Especificamente, este conector do Dynamics AX é compatível com à cópia de dados do Dynamics AX usando o **protocolo OData** com **Autenticação de Entidade de serviço**.

>[!TIP]
>Você também pode usar esse conector para copiar dados do **Dynamics 365 Finance and Operations**. Consulte o [suporte OData](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/data-entities/odata) e o [método de autenticação](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/data-entities/services-home-page#authentication) do Dynamic 365.

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre propriedades que você pode usar para definir entidades do Data Factory específicas do conector Dynamics AX.

## <a name="prerequisites"></a>Pré-requisitos

Para usar a autenticação de entidade de serviço, siga estas etapas:

1. Registre uma entidade de aplicativo no Azure Active Directory (Azure AD) seguindo [Registre o aplicativo com um locatário do Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Anote os seguintes valores, que são usados para definir o serviço vinculado:

    - ID do aplicativo
    - Chave do aplicativo
    - ID do locatário

2. Vá para o Dynamics AX e conceda essa permissão adequada de entidade de serviço para acessar o Dynamics AX.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte no serviço vinculado do Dynamics AX:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade **tipo** deve ser definida como: **DynamicsAX**. |Sim |
| url | Ponto de extremidade OData da instância do Dynamics AX (ou Dynamics 365 Finance and Operations). |Sim |
| servicePrincipalId | Especifique a ID do cliente do aplicativo. | Sim |
| servicePrincipalKey | Especifique a chave do aplicativo. Marque esse campo como **SecureString** para armazená-lo com segurança no Data Factory ou [referencie um segredo armazenado no Cofre de Chaves do Azure](store-credentials-in-key-vault.md). | Sim |
| tenant | Especifique as informações de locatário (domínio nome ou ID do Locatário) em que o aplicativo reside. Para recuperá-lo, passe o mouse no canto superior direito do portal do Azure. | Sim |
| aadResourceId | Especifique o recurso do AAD ao qual você está solicitando autorização. Por exemplo, se a URL do Dynamics é `https://sampledynamics.sandbox.operations.dynamics.com/data/`, o recurso do AAD correspondente é geralmente `https://sampledynamics.sandbox.operations.dynamics.com`. | Sim |
| connectVia | O [Tempo de Integração](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode escolher o Azure Integration Runtime ou o Integration Runtime auto-hospedado (se o armazenamento de dados estiver localizado em uma rede privada). Se não especificado, o Tempo de Execução de Integração do Azure padrão será usado. |Não  |

**Exemplo**

```json
{
    "name": "DynamicsAXLinkedService",
    "properties": {
        "type": "DynamicsAX",
        "typeProperties": {
            "url": "<Dynamics AX instance OData endpoint>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource, e.g. https://sampledynamics.sandbox.operations.dynamics.com>"
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}

```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Esta seção fornece uma lista de propriedades compatíveis com o conjunto de dados Dynamics AX.

Para obter uma lista completa de seções e propriedades disponíveis para definição de conjuntos de dados, consulte [Conjuntos de dados e serviços vinculados](concepts-datasets-linked-services.md). 

Para copiar dados do Dynamics AX, defina a propriedade **tipo** do conjunto de dados como **DynamicsAXResource**. Há suporte para as seguintes propriedades:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade **tipo** do conjunto de dados deve ser definida como **DynamicsAXResource**. | Sim |
| caminho | Caminho para a entidade OData do Dynamics AX. | Sim |

**Exemplo**

```json
{
    "name": "DynamicsAXResourceDataset",
    "properties": {
        "type": "DynamicsAXResource",
        "typeProperties": {
            "path": "<entity path e.g. dd04tentitySet>"
        },
        "linkedServiceName": {
            "referenceName": "<Dynamics AX linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da Atividade de Cópia

Esta seção fornece uma lista de propriedades compatíveis com a fonte Dynamics AX.

Para obter uma lista completa de seções e propriedades que estão disponíveis para definir atividades, consulte [Pipelines](concepts-pipelines-activities.md). 

### <a name="dynamics-ax-as-source"></a>Dynamics AX como fonte

Para copiar dados do Dynamics AX, defina o **tipo** de fonte em Copiar atividade para **DynamicsAXSource**. As seguintes propriedades são suportadas na seção **source** da atividade de cópia:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade **tipo** da fonte Copiar atividade deve ser definida como **DynamicsAXSource**. | Sim |
| query | Opções de consulta OData para filtrar dados. Exemplo: `"?$select=Name,Description&$top=5"`.<br/><br/>**Observação**: O conector copia os dados da URL combinada: `[URL specified in linked service]/[path specified in dataset][query specified in copy activity source]`. Para saber mais, confira as [Componentes da URL do OData](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Não  |

**Exemplo**

```json
"activities":[
    {
        "name": "CopyFromDynamicsAX",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Dynamics AX input dataset name>",
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
                "type": "DynamicsAXSource",
                "query": "$top=10"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Próximas etapas

Para obter uma lista de armazenamentos de dados que o Copy Activity suporta como fontes e coletores no Azure Data Factory, consulte [Armazenamentos e formatos de dados compatíveis](copy-activity-overview.md##supported-data-stores-and-formats).
