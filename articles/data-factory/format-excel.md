---
title: Formato do Excel no Azure Data Factory
description: Este tópico descreve como lidar com o formato do Excel no Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/08/2020
ms.author: jingwang
ms.openlocfilehash: 8f19ccc90c44ef90cee7bb1ae881086321e863b6
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96902027"
---
# <a name="excel-format-in-azure-data-factory"></a>Formato do Excel no Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Siga este artigo quando desejar **analisar os arquivos do Excel**. Azure Data Factory dá suporte a ". xls" e ". xlsx".

Há suporte para o formato do Excel para os seguintes conectores: [Amazon S3](connector-amazon-simple-storage-service.md), [blob do Azure](connector-azure-blob-storage.md), [Azure data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md), [armazenamento de arquivos do Azure](connector-azure-file-storage.md), [sistema de arquivos](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)e [SFTP](connector-sftp.md). Há suporte como origem, mas não coletor. 

**Observação**: não há suporte para o formato ". xls" ao usar [http](connector-http.md). 

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [Conjuntos de Dados](concepts-datasets-linked-services.md). Esta seção fornece uma lista das propriedades com suporte pelo conjunto de banco de e do Excel.

| Propriedade         | Descrição                                                  | Obrigatório |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | A propriedade Type do conjunto de conjuntos deve ser definida como **Excel**.   | Sim      |
| local         | Configurações de local dos arquivos. Cada conector baseado em arquivo tem seu próprio tipo de local e propriedades com suporte em `location` . | Sim      |
| sheetName        | O nome da planilha do Excel para ler os dados.                       | Especifique `sheetName` ou `sheetIndex` |
| sheetIndex | O índice da planilha do Excel para ler dados, começando em 0. | Especifique `sheetName` ou `sheetIndex` |
| range            | O intervalo de células na planilha fornecida para localizar os dados seletivos, por exemplo:<br>-Não especificado: lê a planilha inteira como uma tabela da primeira linha e coluna não vazias<br>- `A3`: lê uma tabela a partir da célula especificada, detecta dinamicamente todas as linhas abaixo e todas as colunas à direita<br>- `A3:H5`: lê este intervalo fixo como uma tabela<br>- `A3:A3`: lê esta única célula | Não       |
| firstRowAsHeader | Especifica se deve tratar a primeira linha na planilha/intervalo fornecido como uma linha de cabeçalho com nomes de colunas.<br>Os valores permitidos são **true** e **false** (padrão). | Não       |
| nullValue        | Especifica a representação de cadeia de caracteres do valor nulo. <br>O valor padrão é uma **cadeia de caracteres vazia**. | Não       |
| compactação | Grupo de propriedades para configurar a compactação de arquivo. Configure esta seção quando desejar fazer compactação/descompactação durante a execução da atividade. | Não |
| type<br/>(*em `compression`*) | O codec de compactação usado para ler/gravar arquivos JSON. <br>Os valores permitidos são **bzip2**, **gzip**, **deflate**, **ZipDeflate**, **TarGzip**, **tar**, **snapshot** ou **lz4**. O padrão não é compactado.<br>**Observação** a atividade de cópia atualmente não dá suporte a "encaixar" & "lz4" e o fluxo de dados de mapeamento não dá suporte a "ZipDeflate", "TarGzip" e "tar".<br>**Observação** ao usar a atividade de cópia para descompactar arquivo (s) **ZipDeflate** e gravar no armazenamento de dados de coletor baseado em arquivo, os arquivos são extraídos para a pasta: `<path specified in dataset>/<folder named as source zip file>/` . | Não.  |
| nível<br/>(*em `compression`*) | A taxa de compactação. <br>Os valores permitidos são **ideal** ou **mais rápido**.<br>- **Mais rápido:** A operação de compactação deve ser concluída o mais rápido possível, mesmo que o arquivo resultante não seja compactado de maneira ideal.<br>- **Ideal**: a operação de compactação deve ser corretamente compactada, mesmo se a operação levar mais tempo para ser concluída. Para saber mais, veja o tópico [Nível de compactação](/dotnet/api/system.io.compression.compressionlevel) . | Não       |

Veja abaixo um exemplo de conjunto de um banco de uma no armazenamento de BLOBs do Azure:

```json
{
    "name": "ExcelDataset",
    "properties": {
        "type": "Excel",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "sheetName": "MyWorksheet",
            "range": "A3:H5",
            "firstRowAsHeader": true
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela origem do Excel.

### <a name="excel-as-source"></a>Excel como fonte 

As propriedades a seguir têm suporte na seção atividade de cópia **_ \_ origem \****.

| Propriedade      | Descrição                                                  | Obrigatório |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | A propriedade Type da fonte da atividade de cópia deve ser definida como **Excel**. | Sim      |
| storeSettings | Um grupo de propriedades sobre como ler dados de um armazenamento de dados. Cada conector baseado em arquivo tem suas próprias configurações de leitura com suporte em `storeSettings` . | Não       |

```json
"activities": [
    {
        "name": "CopyFromExcel",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "ExcelSource",
                "storeSettings": {
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                }
            },
            ...
        }
        ...
    }
]
```

## <a name="mapping-data-flow-properties"></a>Propriedades do fluxo de dados de mapeamento

No mapeamento de fluxos de dados, você pode ler o formato do Excel nos seguintes armazenamentos de dados: [armazenamento de BLOBs do Azure](connector-azure-blob-storage.md#mapping-data-flow-properties), [Azure data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)e [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties). Você pode apontar para arquivos do Excel usando o conjunto de linhas do Excel ou usando um [conjunto](data-flow-source.md#inline-datasets)de um.

### <a name="source-properties"></a>Propriedades de origem

A tabela abaixo lista as propriedades com suporte por uma origem do Excel. Você pode editar essas propriedades na guia **Opções de origem** . Ao usar o conjunto de linhas embutido, você verá configurações de arquivo adicionais, que são iguais às propriedades descritas na seção [Propriedades do conjunto](#dataset-properties) de cores.

| Nome                      | Descrição                                                  | Obrigatório | Valores permitidos                                            | Propriedade de script de fluxo de dados         |
| ------------------------- | ------------------------------------------------------------ | -------- | --------------------------------------------------------- | --------------------------------- |
| Caminhos curinga           | Todos os arquivos correspondentes ao caminho curinga serão processados. Substitui a pasta e o caminho do arquivo definido no conjunto de um. | no       | String[]                                                  | wildcardPaths                     |
| Caminho raiz da partição       | Para dados de arquivo particionados, você pode inserir um caminho raiz de partição para ler pastas particionadas como colunas | no       | String                                                    | partitionRootPath                 |
| Lista de arquivos             | Se sua fonte está apontando para um arquivo de texto que lista os arquivos a serem processados | no       | `true` ou `false`                                         | File                          |
| Coluna para armazenar o nome do arquivo | Criar uma nova coluna com o nome e o caminho do arquivo de origem       | no       | String                                                    | rowUrlColumn                      |
| Após a conclusão          | Exclua ou mova os arquivos após o processamento. O caminho do arquivo inicia a partir da raiz do contêiner | no       | Excluir: `true` ou `false` <br> Prosseguir `['<from>', '<to>']` | purgeFiles <br> MoveFile         |
| Filtrar por última modificação   | Escolher filtrar arquivos com base na última alteração | no       | Timestamp                                                 | modifiedAfter <br> modifiedBefore |
| Não permitir nenhum arquivo encontrado | Se for true, um erro não será gerado se nenhum arquivo for encontrado | no | `true` ou `false` | ignoreNoFilesFound |

### <a name="source-example"></a>Exemplo de origem

A imagem abaixo é um exemplo de uma configuração de origem do Excel no mapeamento de fluxos de dados usando o modo DataSet.

![Origem do Excel](media/data-flow/excel-source.png)

O script de fluxo de dados associado é:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    wildcardPaths:['*.xls']) ~> ExcelSource
```

Se você usar o conjunto de dados embutido, verá as seguintes opções de origem em mapear fluxo de dados.

![Conjunto de fonte embutido de origem do Excel](media/data-flow/excel-source-inline-dataset.png)

O script de fluxo de dados associado é:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    format: 'excel',
    fileSystem: 'container',
    folderPath: 'path',
    fileName: 'sample.xls',
    sheetName: 'worksheet',
    firstRowAsHeader: true) ~> ExcelSourceInlineDataset
```

## <a name="next-steps"></a>Próximas etapas

- [Visão geral da atividade de cópia](copy-activity-overview.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)
- [Atividade GetMetadata](control-flow-get-metadata-activity.md)