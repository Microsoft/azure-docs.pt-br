---
title: Atividade de obtenção de metadados no Azure Data Factory | Microsoft Docs
description: Saiba como você pode usar a atividade GetMetadata em um pipeline do Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: ''
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: jingwang
ms.openlocfilehash: 78f63b4f46fe5479d4d0fd5849ad80536d8a137c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61346801"
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>A atividade de obtenção de metadados no Azure Data Factory

A atividade GetMetadata pode ser usada para recuperar **metadados** de todos os dados no Azure Data Factory. Essa atividade pode ser utilizada nos seguintes cenários:

- Validar as informações de metadados de todos os dados
- Disparar um pipeline quando os dados estão prontos/disponíveis

A seguinte funcionalidade está disponível no fluxo de controle:

- A saída da atividade GetMetadata pode ser usada em expressões condicionais para executar a validação.
- Um pipeline pode ser disparado quando a condição é atendida por meio do loop Do-Until

## <a name="supported-capabilities"></a>Funcionalidades com suporte

A Atividade GetMetadata coleta um conjunto de dados como uma entrada obrigatória e gera informações de metadados disponíveis como saída da atividade. No momento, há suporte para os seguintes conectores com metadados recuperáveis correspondentes e o tamanho máximo de metadados com suporte é de até **1 MB**.

>[!NOTE]
>Se você executar GetMetadata atividade em um tempo de execução de integração auto-hospedada, a funcionalidade mais recente é suportada na versão 3.6 ou acima. 

### <a name="supported-connectors"></a>Conectores com suporte

**Armazenamento de Arquivos**

| Conector/metadados | itemName<br>(arquivo/pasta) | itemType<br>(arquivo/pasta) | tamanho<br>(arquivo) | criado<br>(arquivo/pasta) | lastModified<br>(arquivo/pasta) |childItems<br>(pasta) |contentMD5<br>(arquivo) | estrutura<br/>(arquivo) | ColumnCount<br>(arquivo) | exists<br>(arquivo/pasta) |
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| Amazon S3 | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| Google Cloud Storage | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| blob do Azure | √/√ | √/√ | √ | x/x | √/√* | √ | √ | √ | √ | √/√ |
| Azure Data Lake Storage Gen1 | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| Azure Data Lake Storage Gen2 | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| Armazenamento de Arquivos do Azure | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| Sistema de Arquivos | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| SFTP | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| FTP | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |

- Para Amazon S3 e o armazenamento de Sloud do Google, o `lastModified` aplica-se ao bucket e chave, mas não virtual pasta; e o `exists` aplica-se ao bucket e chave, mas não para o prefixo ou pasta virtual.
- Para Blob do Azure, `lastModified` aplica-se ao contêiner e ao blob, mas não à pasta virtual.

**Banco de dados relacional:**

| Conector/metadados | estrutura | ColumnCount | exists |
|:--- |:--- |:--- |:--- |
| Banco de Dados SQL do Azure | √ | √ | √ |
| Instância Gerenciada do Banco de Dados SQL do Azure | √ | √ | √ |
| SQL Data Warehouse do Azure | √ | √ | √ |
| SQL Server | √ | √ | √ |

### <a name="metadata-options"></a>Opções de metadados

Os seguintes tipos de metadados podem ser especificados na lista de campos de atividade GetMetadata para recuperar:

| Tipo de Metadados | DESCRIÇÃO |
|:--- |:--- |
| itemName | Nome do arquivo ou pasta. |
| itemType | Tipo do arquivo ou pasta. Valor de saída é `File` ou `Folder`. |
| tamanho | Tamanho do arquivo de ativo em bytes. Aplicável somente para arquivo. |
| criado | O datetime da última modificação do arquivo ou da pasta. |
| lastModified | O datetime da última modificação do arquivo ou da pasta. |
| childItems | Lista de subpastas e arquivos dentro da pasta determinada. Aplicável somente a pasta. Valor de saída é uma lista de nome e tipo de cada item filho. |
| contentMD5 | MD5 do arquivo. Aplicável somente para arquivo. |
| estrutura | Estrutura de dados dentro do arquivo ou tabela de banco de dados relacional. Valor de saída é uma lista de nome de coluna e tipo de coluna. |
| ColumnCount | Número de colunas dentro do arquivo ou tabela relacional. |
| exists| Se um arquivo/pasta/tabela existe ou não. Observe que se "existe" for especificado na lista de campos GetaMetadata, a atividade não falhará mesmo quando o item (arquivo/pasta/tabela) não existe; em vez disso, ele retorna `exists: false` na saída. |

>[!TIP]
>Especifique quando você deseja validar se um arquivo/pasta/tabela existe ou não, `exists` na lista de campos de atividade GetMetadata, em seguida, você pode verificar o `exists: true/false` resultados da saída da atividade. Se `exists` não está configurado na lista de campos, o GetMetadata atividade falhará quando o objeto não foi encontrado.

## <a name="syntax"></a>Sintaxe

**GetMetadata atividade:**

```json
{
    "name": "MyActivity",
    "type": "GetMetadata",
    "typeProperties": {
        "fieldList" : ["size", "lastModified", "structure"],
        "dataset": {
            "referenceName": "MyDataset",
            "type": "DatasetReference"
        }
    }
}
```

**Conjunto de dados**

```json
{
    "name": "MyDataset",
    "properties": {
    "type": "AzureBlob",
        "linkedService": {
            "referenceName": "StorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath":"container/folder",
            "filename": "file.json",
            "format":{
                "type":"JsonFormat"
            }
        }
    }
}
```

## <a name="type-properties"></a>Propriedades de tipo

Atualmente, a atividade GetMetadata pode buscar os seguintes tipos de informações de metadados.

Propriedade | DESCRIÇÃO | Obrigatório
-------- | ----------- | --------
fieldList | Lista os tipos de informações de metadados necessárias. Consulte os detalhes em [Opções de metadados](#metadata-options)  nos metadados  com suportes. | Sim 
dataset | O conjunto de dados de referência cuja atividade de metadados deve ser recuperada pela atividade GetMetadata. Consulte [recursos com suporte para](#supported-capabilities) seção conectores com suporte e consulte o tópico de conector nos detalhes da sintaxe de conjunto de dados. | Sim

## <a name="sample-output"></a>Saída de exemplo

O resultado GetMetadata é mostrado na saída da atividade. A seguir estão dois exemplos com opções de metadados exaustiva selecionadas na lista de campos como referência. Para usar o resultado na atividade subsequente, use o padrão de `@{activity('MyGetMetadataActivity').output.itemName}`.

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
Consulte outras atividades de fluxo de controle com suporte pelo Data Factory: 

- [Atividade de execução de pipeline](control-flow-execute-pipeline-activity.md)
- [Para cada atividade](control-flow-for-each-activity.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)
- [Atividade da Web](control-flow-web-activity.md)
