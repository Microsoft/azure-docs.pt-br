---
title: Formato binário no Azure Data Factory
description: Este tópico descreve como lidar com o formato binário no Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/29/2020
ms.author: jingwang
ms.openlocfilehash: be4338942057cf52bf26b7fd1e15f77e724d49f8
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348893"
---
# <a name="binary-format-in-azure-data-factory"></a>Formato binário no Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

O formato binário tem suporte para os seguintes conectores: [Amazon S3](connector-amazon-simple-storage-service.md), [blob do Azure](connector-azure-blob-storage.md), [Azure data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md), [armazenamento de arquivos do Azure](connector-azure-file-storage.md), [sistema de arquivos](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)e [SFTP](connector-sftp.md).

Você pode usar o conjunto de um DataSet binário na atividade de [cópia](copy-activity-overview.md), na [atividade GetMetadata](control-flow-get-metadata-activity.md)ou na [atividade de exclusão](delete-activity.md). Ao usar o DataSet binário, o ADF não analisa o conteúdo do arquivo, mas o trata como está. 

>[!NOTE]
>Ao usar o DataSet binário na atividade de cópia, você só pode copiar de um conjunto de um binário para um conjunto de um binário.

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [Conjuntos de Dados](concepts-datasets-linked-services.md). Esta seção fornece uma lista das propriedades com suporte pelo conjunto de banco de e binário.

| Propriedade         | Descrição                                                  | Obrigatório |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | A propriedade Type do conjunto de conjuntos deve ser definida como **Binary**. | Yes      |
| local         | Configurações de local dos arquivos. Cada conector baseado em arquivo tem seu próprio tipo de local e propriedades com suporte em `location` . **Consulte os detalhes no artigo do conector – > seção Propriedades do conjunto de informações**. | Yes      |
| compactação | Grupo de propriedades para configurar a compactação de arquivo. Configure esta seção quando desejar fazer compactação/descompactação durante a execução da atividade. | Não |
| type | O codec de compactação usado para ler/gravar arquivos binários. <br>Os valores permitidos são **bzip2**, **gzip**, **deflate**, **ZipDeflate**, **tar** ou **TarGzip**. <br>**Observação** ao usar a atividade de cópia para **ZipDeflate** descompactar / **TarGzip** / arquivo (s) ZipDeflate TarGzip **tar** e gravar no armazenamento de dados de coletor baseado em arquivo, por padrão os arquivos são extraídos para a pasta: `<path specified in dataset>/<folder named as source compressed file>/` , use `preserveZipFileNameAsFolder` / `preserveCompressionFileNameAsFolder` na [origem da atividade de cópia](#binary-as-source) para controlar se deseja preservar o nome dos arquivos compactados como estrutura de pastas.| No       |
| nível | A taxa de compactação. Aplicar quando o conjunto de um for usado no coletor da atividade de cópia.<br>Os valores permitidos são **ideal** ou **mais rápido**.<br>- **Mais rápido:** A operação de compactação deve ser concluída o mais rápido possível, mesmo que o arquivo resultante não seja compactado de maneira ideal.<br>- **Ideal**: a operação de compactação deve ser corretamente compactada, mesmo se a operação levar mais tempo para ser concluída. Para saber mais, veja o tópico [Nível de compactação](/dotnet/api/system.io.compression.compressionlevel) . | No       |

Veja abaixo um exemplo de conjunto de uma Binary DataSet no armazenamento de BLOBs do Azure:

```json
{
    "name": "BinaryDataset",
    "properties": {
        "type": "Binary",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "compression": {
                "type": "ZipDeflate"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela fonte binária e pelo coletor.

>[!NOTE]
>Ao usar o DataSet binário na atividade de cópia, você só pode copiar de um conjunto de um binário para um conjunto de um binário.

### <a name="binary-as-source"></a>Binário como fonte

As propriedades a seguir têm suporte na seção atividade de cópia **_ \_ origem \****.

| Propriedade      | Descrição                                                  | Obrigatório |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | A propriedade Type da fonte da atividade de cópia deve ser definida como **binaryname**. | Yes      |
| formatSettings | Um grupo de propriedades. Consulte a tabela **configurações de leitura binária** abaixo. | No       |
| storeSettings | Um grupo de propriedades sobre como ler dados de um armazenamento de dados. Cada conector baseado em arquivo tem suas próprias configurações de leitura com suporte em `storeSettings` . **Veja os detalhes no artigo do conector – > seção Propriedades da atividade de cópia**. | No       |

**Configurações de leitura binária** com suporte em `formatSettings` :

| Propriedade      | Descrição                                                  | Obrigatório |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | O tipo de formatSettings deve ser definido como **BinaryReadSettings**. | Yes      |
| compactproperties | Um grupo de propriedades sobre como descompactar dados para um determinado codec de compactação. | No       |
| preserveZipFileNameAsFolder<br>(*em `compressionProperties` -> `type` como `ZipDeflateReadSettings`*) | Aplica-se quando o conjunto de dados de entrada é configurado com compactação **ZipDeflate** . Indica se o nome do arquivo zip de origem deve ser preservado como estrutura de pastas durante a cópia.<br>-Quando definido como **true (padrão)**, data Factory grava arquivos descompactados em `<path specified in dataset>/<folder named as source zip file>/` .<br>-Quando definido como **false**, data Factory grava arquivos descompactados diretamente no `<path specified in dataset>` . Verifique se você não tem nomes de arquivo duplicados em arquivos zip de origem diferentes para evitar a corrida ou comportamento inesperado.  | No |
| preserveCompressionFileNameAsFolder<br>(*em `compressionProperties` -> `type` como `TarGZipReadSettings` ou `TarReadSettings`*) | Aplica-se quando o conjunto de **TarGzip** dados de entrada é configurado com / compactação **tar** TarGzip. Indica se o nome do arquivo compactado de origem deve ser preservado como estrutura de pastas durante a cópia.<br>-Quando definido como **true (padrão)**, data Factory grava arquivos descompactados em `<path specified in dataset>/<folder named as source compressed file>/` . <br>-Quando definido como **false**, data Factory grava arquivos descompactados diretamente no `<path specified in dataset>` . Verifique se você não tem nomes de arquivo duplicados em arquivos de origem diferentes para evitar a corrida ou comportamento inesperado. | No |

```json
"activities": [
    {
        "name": "CopyFromBinary",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "BinarySource",
                "storeSettings": {
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true,
                    "deleteFilesAfterCompletion": true
                },
                "formatSettings": {
                    "type": "BinaryReadSettings",
                    "compressionProperties": {
                        "type": "ZipDeflateReadSettings",
                        "preserveZipFileNameAsFolder": false
                    }
                }
            },
            ...
        }
        ...
    }
]
```

### <a name="binary-as-sink"></a>Binário como coletor

As propriedades a seguir têm suporte na seção atividade de cópia **_ \_ Sink \****.

| Propriedade      | Descrição                                                  | Obrigatório |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | A propriedade Type da fonte da atividade de cópia deve ser definida como **BinarySink**. | Yes      |
| storeSettings | Um grupo de propriedades sobre como gravar dados em um armazenamento de dados. Cada conector baseado em arquivo tem suas próprias configurações de gravação com suporte em `storeSettings` . **Veja os detalhes no artigo do conector – > seção Propriedades da atividade de cópia**. | Não       |

## <a name="next-steps"></a>Próximas etapas

- [Visão geral da atividade de cópia](copy-activity-overview.md)
- [Atividade GetMetadata](control-flow-get-metadata-activity.md)
- [Excluir atividade](delete-activity.md)