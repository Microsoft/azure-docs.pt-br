---
title: Obter atividade de metadados no Azure Data Factory
description: Saiba como usar a atividade obter metadados em um pipeline de Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: jingwang
ms.openlocfilehash: f860225862dcbfb79535acfbd6eeb89a217e7ae9
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100385482"
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>Obter atividade de metadados no Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Você pode usar a atividade obter metadados para recuperar os metadados de quaisquer dados em Azure Data Factory. Você pode usar essa atividade nos seguintes cenários:

- Valide os metadados de quaisquer dados.
- Disparar um pipeline quando os dados estiverem prontos/disponíveis.

A seguinte funcionalidade está disponível no fluxo de controle:

- Você pode usar a saída da atividade obter metadados em expressões condicionais para executar a validação.
- Você pode disparar um pipeline quando uma condição é satisfeita por meio do until loop.

## <a name="capabilities"></a>Funcionalidades

A atividade obter metadados usa um conjunto de dados como uma entrada e retorna informações de metadados como saída. Atualmente, há suporte para os seguintes conectores e metadados recuperáveis correspondentes. O tamanho máximo dos metadados retornados é de cerca de 4 MB.

>[!NOTE]
>Se você executar a atividade obter metadados em um tempo de execução de integração auto-hospedado, os recursos mais recentes terão suporte na versão 3,6 ou posterior.

### <a name="supported-connectors"></a>Conectores com suporte

**Armazenamento de arquivos**

| Conector/metadados | itemName<br>(arquivo/pasta) | itemType<br>(arquivo/pasta) | tamanho<br>(arquivo) | criado<br>(arquivo/pasta) | lastModified<br>(arquivo/pasta) |childItems<br>(pasta) |contentMD5<br>(arquivo) | estrutura<br/>(arquivo) | ColumnCount<br>(arquivo) | exists<br>(arquivo/pasta) |
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| [Amazon S3](connector-amazon-simple-storage-service.md) | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| [Google Cloud Storage](connector-google-cloud-storage.md) | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| [Armazenamento de Blobs do Azure](connector-azure-blob-storage.md) | √/√ | √/√ | √ | x/x | √/√* | √ | √ | √ | √ | √/√ |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | √/√ | √/√ | √ | x/x | √/√ | √ | √ | √ | √ | √/√ |
| [Arquivos do Azure](connector-azure-file-storage.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [Sistema de arquivos](connector-file-system.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [SFTP](connector-sftp.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [FTP](connector-ftp.md) | √/√ | √/√ | √ | x/x | x/x | √ | x | √ | √ | √/√ |

- Ao usar a atividade obter metadados em uma pasta, verifique se você tem a permissão lista/executar na pasta especificada.
- Para o Amazon S3 e o Google Cloud Storage, o `lastModified` se aplica ao Bucket e à chave, mas não à pasta virtual, e `exists` aplica-se ao Bucket e à chave, mas não ao prefixo ou à pasta virtual.
- Para o armazenamento de BLOBs do Azure, `lastModified` o se aplica ao contêiner e ao blob, mas não à pasta virtual.
- `lastModified` o filtro atualmente se aplica a itens filho de filtro, mas não ao próprio arquivo/pasta especificado.
- O filtro de curingas em pastas/arquivos não tem suporte para a atividade obter metadados.
- `structure` e `columnCount` não têm suporte ao obter metadados de arquivos binários, JSON ou XML.

**Banco de dados relacional**

| Conector/metadados | estrutura | ColumnCount | exists |
|:--- |:--- |:--- |:--- |
| [Banco de Dados SQL do Azure](connector-azure-sql-database.md) | √ | √ | √ |
| [Instância Gerenciada do SQL do Azure](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md) | √ | √ | √ |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) | √ | √ | √ |
| [SQL Server](connector-sql-server.md) | √ | √ | √ |

### <a name="metadata-options"></a>Opções de metadados

Você pode especificar os seguintes tipos de metadados na lista de campos de atividade obter metadados para recuperar as informações correspondentes:

| Tipo de Metadados | Descrição |
|:--- |:--- |
| itemName | Nome do arquivo ou pasta. |
| itemType | Tipo do arquivo ou pasta. O valor retornado é `File` ou `Folder` . |
| tamanho | Tamanho do arquivo, em bytes. Aplicável somente a arquivos. |
| criado | O datetime da última modificação do arquivo ou da pasta. |
| lastModified | O datetime da última modificação do arquivo ou da pasta. |
| childItems | Lista de subpastas e arquivos na pasta especificada. Aplicável somente a pastas. Valor retornado é uma lista do nome e do tipo de cada item filho. |
| contentMD5 | MD5 do arquivo. Aplicável somente a arquivos. |
| estrutura | Estrutura de dados do arquivo ou tabela de banco de dado relacional. Valor retornado é uma lista de nomes de coluna e tipos de coluna. |
| ColumnCount | Número de colunas no arquivo ou na tabela relacional. |
| exists| Se existe um arquivo, uma pasta ou uma tabela. Se `exists` for especificado na lista de campos obter metadados, a atividade não falhará mesmo que o arquivo, a pasta ou a tabela não exista. Em vez disso, `exists: false` é retornado na saída. |

>[!TIP]
>Quando você quiser validar que existe um arquivo, pasta ou tabela, especifique `exists` na lista de campos de atividade obter metadados. Em seguida, você pode verificar o `exists: true/false` resultado na saída da atividade. Se `exists` não for especificado na lista de campos, a atividade obter metadados falhará se o objeto não for encontrado.

>[!NOTE]
>Quando você obtém metadados de repositórios de arquivos e configura `modifiedDatetimeStart` ou `modifiedDatetimeEnd` , o `childItems` na saída incluirá somente os arquivos no caminho fornecido que têm uma hora da última modificação dentro do intervalo especificado. No não incluirá itens em subpastas.

## <a name="syntax"></a>Syntax

**Atividade obter metadados**

```json
{
    "name":"MyActivity",
    "type":"GetMetadata",
    "dependsOn":[

    ],
    "policy":{
        "timeout":"7.00:00:00",
        "retry":0,
        "retryIntervalInSeconds":30,
        "secureOutput":false,
        "secureInput":false
    },
    "userProperties":[

    ],
    "typeProperties":{
        "dataset":{
            "referenceName":"MyDataset",
            "type":"DatasetReference"
        },
        "fieldList":[
            "size",
            "lastModified",
            "structure"
        ],
        "storeSettings":{
            "type":"AzureBlobStorageReadSettings"
        },
        "formatSettings":{
            "type":"JsonReadSettings"
        }
    }
}
```

**Conjunto de dados**

```json
{
    "name":"MyDataset",
    "properties":{
        "linkedServiceName":{
            "referenceName":"AzureStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "annotations":[

        ],
        "type":"Json",
        "typeProperties":{
            "location":{
                "type":"AzureBlobStorageLocation",
                "fileName":"file.json",
                "folderPath":"folder",
                "container":"container"
            }
        }
    }
}
```

## <a name="type-properties"></a>Propriedades de tipo

Atualmente, a atividade obter metadados pode retornar os seguintes tipos de informações de metadados:

Propriedade | Descrição | Necessária
-------- | ----------- | --------
fieldList | Os tipos de informações de metadados necessários. Para obter detalhes sobre os metadados com suporte, consulte a seção [Opções de metadados](#metadata-options) deste artigo. | Sim 
dataset | O conjunto de uma referência cujos metadados serão recuperados pela atividade obter metadados. Consulte a seção de [recursos](#capabilities) para obter informações sobre os conectores com suporte. Consulte os tópicos específicos do conector para obter detalhes sobre a sintaxe do conjunto de informações. | Sim
formatSettings | Aplicar ao usar o tipo de conjunto de banco de forma. | Não
storeSettings | Aplicar ao usar o tipo de conjunto de banco de forma. | Não

## <a name="sample-output"></a>Saída de exemplo

Os resultados de obter metadados são mostrados na saída da atividade. A seguir estão dois exemplos que mostram opções de metadados extensivas. Para usar os resultados em uma atividade subsequente, use este padrão: `@{activity('MyGetMetadataActivity').output.itemName}` .

### <a name="get-a-files-metadata"></a>Obter metadados de um arquivo

```json
{
  "exists": true,
  "itemName": "test.csv",
  "itemType": "File",
  "size": 104857600,
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "contentMD5": "cMauY+Kz5zDm3eWa9VpoyQ==",
  "structure": [
    {
        "name": "id",
        "type": "Int64"
    },
    {
        "name": "name",
        "type": "String"
    }
  ],
  "columnCount": 2
}
```

### <a name="get-a-folders-metadata"></a>Obter metadados de uma pasta

```json
{
  "exists": true,
  "itemName": "testFolder",
  "itemType": "Folder",
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "childItems": [
    {
      "name": "test.avro",
      "type": "File"
    },
    {
      "name": "folder hello",
      "type": "Folder"
    }
  ]
}
```

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre outras atividades de fluxo de controle com suporte pelo Data Factory:

- [Atividade de execução de pipeline](control-flow-execute-pipeline-activity.md)
- [Atividade ForEach](control-flow-for-each-activity.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)
- [Atividade da Web](control-flow-web-activity.md)
