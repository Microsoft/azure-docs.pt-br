---
title: Copiar dados do Marketo usando Azure Data Factory (versão prévia) | Microsoft Docs
description: Saiba como copiar dados do Marketo para armazenamentos de dados de coletor compatível usando uma atividade de cópia em um pipeline do Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: jingwang
ms.openlocfilehash: d6d6517a85997265021573b2f9d481c81283c216
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61400450"
---
# <a name="copy-data-from-marketo-using-azure-data-factory-preview"></a>Copiar dados do Marketo usando Azure Data Factory (versão prévia)

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados de e para o Marketo. Ele amplia o artigo [Visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

> [!IMPORTANT]
> Atualmente, esse conector está em versão prévia. Você pode experimentá-lo e oferecer comentários. Se você quiser uma dependência de conectores em versão prévia em sua solução, entre em contato com [suporte do Azure](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Você pode copiar dados de um Marketo para qualquer armazenamento de dados de coletor compatível. Para obter uma lista de armazenamentos de dados com suporte como origens/coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory fornece um driver interno para habilitar a conectividade, portanto, não é necessário instalar manualmente qualquer driver usando esse conector.

>[!NOTE]
>Esse conector do Marketo é criado sobre a API REST do Marketo. Lembre-se de que o Marketo tem [limite de solicitações simultâneas](https://developers.marketo.com/rest-api/) no lado do serviço. Se você encontrar erros informando "Erro ao tentar usar a API REST: Máximo de taxa limite '100' excedido com '20' segundos (606) "ou" Erro ao tentar usar a API REST: Limite de acesso simultâneo '10' atingido (615) ", considere reduzir as execuções de atividade de cópia simultânea para reduzir o número de solicitações para o serviço.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades usadas para definir entidades do Data Factory específicas ao conector do Marketo.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir são compatíveis com o serviço vinculado do Marketo:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como: **Marketo** | Sim |
| endpoint | O ponto de extremidade do servidor do Marketo. (ou seja, 123-ABC-321.mktorest.com)  | Sim |
| clientId | A ID do cliente do serviço Marketo.  | Sim |
| clientSecret | O segredo do cliente do serviço Marketo. Marque este campo como uma SecureString para armazená-la com segurança no Data Factory ou [faça referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Sim |
| useEncryptedEndpoints | Especifica se os endpoints de fonte de dados são criptografados usando HTTPS. O valor padrão é true.  | Não  |
| useHostVerification | Especifica se é necessário o nome do host no certificado do servidor para corresponder ao nome de host do servidor ao se conectar via SSL. O valor padrão é true.  | Não  |
| usePeerVerification | Especifica se deve verificar a identidade do servidor quando se conecta por meio de SSL. O valor padrão é true.  | Não  |

**Exemplo:**

```json
{
    "name": "MarketoLinkedService",
    "properties": {
        "type": "Marketo",
        "typeProperties": {
            "endpoint" : "123-ABC-321.mktorest.com",
            "clientId" : "<clientId>",
            "clientSecret": {
                "type": "SecureString",
                "value": "<clientSecret>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [conjuntos de dados](concepts-datasets-linked-services.md). Esta seção fornece uma lista das propriedades compatíveis com o conjunto de dados do Marketo.

Para copiar dados do Marketo, defina a propriedade type do conjunto de dados como **MarketoObject**. Há suporte para as seguintes propriedades:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type do conjunto de dados deve ser definida como: **MarketoObject** | Sim |
| tableName | Nome da tabela. | Não (se "query" na fonte da atividade for especificada) |

**Exemplo**

```json
{
    "name": "MarketoDataset",
    "properties": {
        "type": "MarketoObject",
        "linkedServiceName": {
            "referenceName": "<Marketo linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades compatíveis com a origem do Marketo.

### <a name="marketo-as-source"></a>Marketo como fonte

Para copiar dados do Marketo, defina o tipo de origem na atividade de cópia como **MarketoSource**. As propriedades a seguir têm suporte na seção **source** da atividade de cópia:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type da fonte da atividade de cópia deve ser definida como: **MarketoSource** | Sim |
| query | Utiliza a consulta SQL personalizada para ler os dados. Por exemplo: `"SELECT * FROM Activitiy_Types"`. | Não (se "tableName" no conjunto de dados for especificado) |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromMarketo",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Marketo input dataset name>",
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
                "type": "MarketoSource",
                "query": "SELECT top 1000 * FROM Activitiy_Types"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Azure Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
