---
title: Copiar dados do Hive usando o Azure Data Factory | Microsoft Docs
description: Saiba como copiar dados do Hive para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline do Azure Data Factory.
services: data-factory
documentationcenter: ''
author: WenJason
manager: digimobile
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
origin.date: 12/07/2018
ms.date: 04/22/2019
ms.author: v-jay
ms.openlocfilehash: b245a80967d91b793fcf360772c0dec758f8f252
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60808896"
---
# <a name="copy-data-from-hive-using-azure-data-factory"></a>Copiar dados do Hive usando o Azure Data Factory 

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados de e para o Hive. Ele amplia o artigo [Visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Você pode copiar dados de um Hive para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte como origens/coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory fornece um driver interno para habilitar a conectividade, portanto, não é necessário instalar manualmente qualquer driver usando esse conector.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades usadas para definir entidades do Data Factory específicas ao conector de Hive.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado de Hive:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como: **Hive** | Sim |
| host | Endereço IP ou nome de host do servidor Hive, separado por ';' para vários hosts (somente quando serviceDiscoveryMode estiver habilitado).  | Sim |
| porta | A porta TCP usada pelo servidor Hive para ouvir conexões de cliente. Se você conectar ao Microsoft Azure HDInsights, especifique a porta como 443. | Sim |
| serverType | O tipo do servidor Hive. <br/>Valores permitidos são: **HiveServer1**, **HiveServer2**, **HiveThriftServer** | Não  |
| thriftTransportProtocol | O protocolo de transporte a ser usado na camada de Thrift. <br/>Valores permitidos são: **Binary**, **SASL**, **HTTP** | Não  |
| authenticationType | O método de autenticação usado para acessar o servidor do Hive. <br/>Valores permitidos são: **Anonymous**, **Username**, **UsernameAndPassword**, **WindowsAzureHDInsightService** | Sim |
| serviceDiscoveryMode | verdadeiro para indicar o uso do serviço ZooKeeper, falso não.  | Não  |
| zooKeeperNameSpace | O namespace no ZooKeeper no qual os2 nós do Hive Server são adicionados.  | Não  |
| useNativeQuery | Especifica se o driver usa consultas HiveQL nativas ou as converte em uma forma equivalente no HiveQL.  | Não  |
| Nome de Usuário | O nome de usuário que você usa para acessar o servidor do Hive.  | Não  |
| Senha | A senha correspondente ao usuário. Marque este campo como uma SecureString para armazená-la com segurança no Data Factory ou [faça referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Não  |
| httpPath | A URL parcial correspondente ao servidor do Hive.  | Não  |
| enableSsl | Especifica se as conexões com o servidor são criptografadas usando SSL. O valor padrão é falso.  | Não  |
| trustedCertPath | O caminho completo do arquivo .pem que contém certificados de autoridade de certificação confiáveis para verificar o servidor ao se conectar via SSL. Essa propriedade só pode ser definida ao usar o SSL em IR auto-hospedado. O valor padrão é o arquivo de cacerts.pem instalado com o IR.  | Não  |
| useSystemTrustStore | Especifica se deve usar um certificado de autoridade de certificação do repositório de confiança de sistema ou de um arquivo PEM especificado. O valor padrão é falso.  | Não  |
| allowHostNameCNMismatch | Especifica se é necessário o nome do certificado SSL emitido pela autoridade de certificação para corresponder ao nome de host do servidor ao se conectar via SSL. O valor padrão é falso.  | Não  |
| allowSelfSignedServerCert | Especifica se deve permitir os certificados autoassinados do servidor. O valor padrão é falso.  | Não  |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o Integration Runtime auto-hospedado ou o Integration Runtime do Azure (se seu armazenamento de dados estiver publicamente acessível). Se não for especificado, ele usa o Integration Runtime padrão do Azure. |Não  |

**Exemplo:**

```json
{
    "name": "HiveLinkedService",
    "properties": {
        "type": "Hive",
        "typeProperties": {
            "host" : "<cluster>.azurehdinsight.net",
            "port" : "<port>",
            "authenticationType" : "WindowsAzureHDInsightService",
            "username" : "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [conjuntos de dados](concepts-datasets-linked-services.md). Esta seção fornece uma lista das propriedades com suporte pelo conjunto de dados de HTTP.

Para copiar dados de Hive, defina a propriedade type do conjunto de dados como **HiveObject**. Há suporte para as seguintes propriedades:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type do conjunto de dados deve ser definida como: **HiveObject** | Sim |
| tableName | Nome da tabela. | Não (se "query" na fonte da atividade for especificada) |

**Exemplo**

```json
{
    "name": "HiveDataset",
    "properties": {
        "type": "HiveObject",
        "linkedServiceName": {
            "referenceName": "<Hive linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela fonte Hive.

### <a name="hivesource-as-source"></a>HiveSource como fonte

Para copiar dados de Hive, defina o tipo de fonte na atividade de cópia como **HiveSource**. As propriedades a seguir têm suporte na seção **source** da atividade de cópia:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type da fonte da atividade de cópia deve ser definida como: **HiveSource** | Sim |
| query | Utiliza a consulta SQL personalizada para ler os dados. Por exemplo: `"SELECT * FROM MyTable"`. | Não (se "tableName" no conjunto de dados for especificado) |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromHive",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Hive input dataset name>",
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
                "type": "HiveSource",
                "query": "SELECT * FROM MyTable"
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
