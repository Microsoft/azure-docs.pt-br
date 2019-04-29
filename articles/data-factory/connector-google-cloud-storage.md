---
title: Copiar dados do Google Cloud Storage utilizando o Azure Data Factory | Microsoft Docs
description: Saiba mais sobre como copiar dados do Google Cloud Storage para armazenamentos de dados de coletor com suporte por meio do Azure Data Factory.
services: data-factory
author: WenJason
manager: digimobile
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
origin.date: 01/25/2019
ms.date: 04/22/2019
ms.author: v-jay
ms.openlocfilehash: 815ee569f0919f32b38b7b7cdf848be184b7aea8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60808966"
---
# <a name="copy-data-from-google-cloud-storage-using-azure-data-factory"></a>Copiar dados do Google Cloud Storage utilizando o Azure Data Factory

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados do Google Cloud Storage. Ele amplia o artigo [Visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Você pode copiar dados do Google Cloud Storage para qualquer armazenamento de dados de coletor compatível. Para obter uma lista de armazenamentos de dados que têm suporte como fontes ou coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Especificamente, este conector do Google Cloud Storage dá suporte à cópia de arquivos no estado em que se encontram ou à análise de arquivos com os [formatos de arquivo e codecs de compactação com suporte](supported-file-formats-and-compression-codecs.md).

>[!NOTE]
>A cópia de dados do Google Cloud Storage aproveita o [conector do Amazon S3](connector-amazon-simple-storage-service.md) com o ponto de extremidade S3 personalizado correspondente, pois o Google Cloud Storage fornece interoperabilidade compatível com S3.

## <a name="required-permissions"></a>Permissões necessárias

Para copiar dados do Google Cloud Storage, verifique se você recebeu as permissões a seguir:

- **Para a execução de atividade de cópia:**: `s3:GetObject` e `s3:GetObjectVersion` para operações de objeto.
- **Para a criação de GUI do Data Factory**: `s3:ListAllMyBuckets` e `s3:ListBucket`/`s3:GetBucketLocation` para permissões de operações de Bucket são necessárias para operações como testar a conexão e procurar/navegar em caminhos de arquivo. Se você não quiser conceder essa permissão, ignore a conexão de teste na página de criação de serviços vinculados e especifique o caminho diretamente nas configurações do conjunto de dados.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

As seções a seguir fornecem detalhes sobre as propriedades que são usadas para definir entidades do Data Factory específicas ao conector do Google Cloud Storage.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte no serviço vinculado do Google Cloud Storage:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade type deve ser definida como: **AmazonS3**. | Sim |
| accessKeyId | ID da chave de acesso secreta. Para localizar a chave de acesso e o segredo, acesse **Google Cloud Storage** > **Configurações** > **Interoperabilidade**. |Sim |
| secretAccessKey | A chave de acesso do secreta em si. Marque este campo como uma SecureString para armazená-la com segurança no Data Factory ou [faça referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). |Sim |
| serviceUrl | Especifique o ponto de extremidade S3 personalizado como **`https://storage.googleapis.com`**. | Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o Integration Runtime do Azure ou o Integration Runtime auto-hospedado (se o armazenamento de dados estiver localizado em uma rede privada). Se não for especificado, ele usa o Integration Runtime padrão do Azure. |Não  |

Veja um exemplo:

```json
{
    "name": "GoogleCloudStorageLinkedService",
    "properties": {
        "type": "AmazonS3",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": {
                "type": "SecureString",
                "value": "<secret access key>"
            },
            "serviceUrl": "https://storage.googleapis.com"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre conjuntos de dados. Esta seção apresenta uma lista das propriedades com suporte do conjunto de dados do Google Cloud Storage.

Para copiar dados do Google Cloud Storage, defina a propriedade type do conjunto de dados como **AmazonS3Object**. Há suporte para as seguintes propriedades:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type do conjunto de dados deve ser definida como: **AmazonS3Object** |Sim |
| bucketName | O nome do bucket S3. O filtro curinga não é suportado. |Sim para atividade de cópia/pesquisa, não para a atividade GetMetadata |
| chave | O **filtro de nome ou curinga** da chave do objeto S3 sob o bucket especificado. Aplica-se apenas quando a propriedade "prefix" não é especificada. <br/><br/>Há suporte ao filtro curinga para a parte da pasta e para a parte do nome do arquivo. Os curingas permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou caractere único).<br/>– Exemplo 1: `"key": "rootfolder/subfolder/*.csv"`<br/>– Exemplo 2: `"key": "rootfolder/subfolder/???20180427.txt"`<br/>Veja mais exemplos em [Exemplos de filtro de pastas e arquivos](#folder-and-file-filter-examples). Use `^` como escape se o nome real da pasta/arquivo tiver curingas ou esse caractere de escape. |Não  |
| prefixo | Prefixo da chave do objeto S3. Objetos cujas chaves começam com esse prefixo serão selecionados. Aplica-se apenas quando a propriedade "chave" não é especificada. |Não  |
| version | A versão do objeto S3 se o controle de versão do S3 está habilitado. |Não  |
| modifiedDatetimeStart | Filtro de arquivos com base no atributo: Última Modificação. Os arquivos serão selecionados se a hora da última alteração estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. A hora é aplicada ao fuso horário de UTC no formato "2018-12-01T05:00:00Z". <br/><br/> As propriedades podem ser NULL, o que significa que nenhum filtro de atributo de arquivo será aplicado ao conjunto de dados.  Quando `modifiedDatetimeStart` tem o valor de data e hora, mas `modifiedDatetimeEnd` for NULL, isso significa que serão selecionados os arquivos cujo último atributo modificado é maior ou igual ao valor de data e hora.  Quando `modifiedDatetimeEnd` tem o valor de data e hora, mas `modifiedDatetimeStart` for NULL, isso significa que serão selecionados os arquivos cujo último atributo modificado é menor que o valor de data e hora.| Não  |
| modifiedDatetimeEnd | Filtro de arquivos com base no atributo: Última Modificação. Os arquivos serão selecionados se a hora da última alteração estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. A hora é aplicada ao fuso horário de UTC no formato "2018-12-01T05:00:00Z". <br/><br/> As propriedades podem ser NULL, o que significa que nenhum filtro de atributo de arquivo será aplicado ao conjunto de dados.  Quando `modifiedDatetimeStart` tem o valor de data e hora, mas `modifiedDatetimeEnd` for NULL, isso significa que serão selecionados os arquivos cujo último atributo modificado é maior ou igual ao valor de data e hora.  Quando `modifiedDatetimeEnd` tem o valor de data e hora, mas `modifiedDatetimeStart` for NULL, isso significa que serão selecionados os arquivos cujo último atributo modificado é menor que o valor de data e hora.| Não  |
| formato | Se você quiser **copiar arquivos no estado em que se encontram** entre repositórios baseados em arquivo (cópia binária), ignore a seção de formato nas duas definições de conjunto de dados de entrada e de saída.<br/><br/>Se você quer analisar ou gerar arquivos com um formato específico, os seguintes tipos de formato de arquivo são compatíveis: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Defina a propriedade **type** sob formato como um desses valores. Para saber mais, veja as seções [Formato de texto](supported-file-formats-and-compression-codecs.md#text-format), [Formato Json](supported-file-formats-and-compression-codecs.md#json-format), [Formato Avro](supported-file-formats-and-compression-codecs.md#avro-format), [Formato Orc](supported-file-formats-and-compression-codecs.md#orc-format), e [Formato Parquet](supported-file-formats-and-compression-codecs.md#parquet-format). |Não (somente para o cenário de cópia binária) |
| compactação | Especifique o tipo e o nível de compactação para os dados. Para obter mais informações, consulte [Formatos de arquivo e codecs de compactação com suporte](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Tipos compatíveis são: **GZip**, **Deflate**, **BZip2** e **ZipDeflate**.<br/>Níveis compatíveis são: **Ideal** e **Mais Rápido**. |Não  |

>[!TIP]
>Para copiar todos os arquivos em uma pasta, especifique **bucketName** para bucket e **prefixo** para a parte da pasta.<br>Para copiar um único arquivo com um determinado nome, especifique **bucketName** para bucket e **chave** para o nome da parte mais o arquivo de pasta.<br>Para copiar um subconjunto de arquivos em uma pasta, especifique **bucketName** para bucket e **chave** para o nome da parte mais o arquivo de pasta.

**Exemplo: usando o prefixo**

```json
{
    "name": "GoogleCloudStorageDataset",
    "properties": {
        "type": "AmazonS3Object",
        "linkedServiceName": {
            "referenceName": "<linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "prefix": "testFolder/test",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção apresenta uma lista das propriedades com suporte da fonte do Google Cloud Storage.

### <a name="google-cloud-storage-as-source"></a>Google Cloud Storage como fonte

Para copiar dados do Google Cloud Storage, defina o tipo de origem na atividade de cópia como **FileSystemSource**. As propriedades a seguir têm suporte na seção **source** da atividade de cópia:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type da fonte da atividade de cópia deve ser definida como: **FileSystemSource** |Sim |
| recursiva | Indica se os dados são lidos recursivamente a partir das subpastas ou somente da pasta especificada. Observe que quando o recursivo estiver definido como verdadeiro e o coletor for um armazenamento baseado em arquivo, subpasta/pasta vazia não será copiada/criada no coletor.<br/>Os valores permitidos são: **true** (padrão), **false** | Não  |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromGoogleCloudStorage",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
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
                "type": "FileSystemSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Exemplos de filtro de pasta e arquivo

Esta seção descreve o comportamento resultante do caminho da pasta e do nome de arquivo com filtros curinga.

| bucket | chave | recursiva | Estrutura da pasta de origem e resultado do filtro (os arquivos em negrito são recuperados)|
|:--- |:--- |:--- |:--- |
| bucket | `Folder*/*` | falso | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo6.csv |
| bucket | `Folder*/*` | verdadeiro | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo6.csv |
| bucket | `Folder*/*.csv` | falso | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo6.csv |
| bucket | `Folder*/*.csv` | verdadeiro | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo6.csv |

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados que têm suporte como origens e coletores na atividade de cópia no Azure Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md##supported-data-stores-and-formats).
