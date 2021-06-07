---
title: Formato de texto delimitado no Azure Data Factory
description: Este tópico descreve como lidar com o formato de texto delimitado no Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/23/2021
ms.author: jingwang
ms.openlocfilehash: daf3691b48f7bf12e9ef51de7d4253dad9dbd2b1
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105026863"
---
# <a name="delimited-text-format-in-azure-data-factory"></a>Formato de texto delimitado no Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Siga este artigo quando desejar **analisar os arquivos de texto delimitados ou gravar os dados em um formato de texto delimitado**. 

O formato de texto delimitado tem suporte para os seguintes conectores: [Amazon S3](connector-amazon-simple-storage-service.md), [blob do Azure](connector-azure-blob-storage.md), [Azure data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md), [armazenamento de arquivos do Azure](connector-azure-file-storage.md), [sistema de arquivos](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)e [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [Conjuntos de Dados](concepts-datasets-linked-services.md). Esta seção fornece uma lista das propriedades com suporte pelo conjunto de acordo de texto delimitado.

| Propriedade         | Descrição                                                  | Obrigatório |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | A propriedade Type do conjunto de conjuntos deve ser definida como **DelimitedText**. | Yes      |
| local         | Configurações de local dos arquivos. Cada conector baseado em arquivo tem seu próprio tipo de local e propriedades com suporte em `location` .  | Yes      |
| columnDelimiter  | Os caracteres usados para separar colunas em um arquivo. <br>O valor padrão é **vírgula `,`**. Quando o delimitador de coluna é definido como uma cadeia de caracteres vazia, o que significa que não há delimitador, a linha inteira é executada como uma única coluna.<br>Atualmente, o delimitador de coluna como cadeia de caracteres vazia ou multichar tem suporte apenas para mapear fluxo de dados, mas não para a atividade de cópia.  | No       |
| rowDelimiter     | O caractere único ou "\r\n" usado para separar linhas em um arquivo. <br>O valor padrão é qualquer um dos seguintes valores **na leitura: ["\r\n", "\r", "\n"]** e **"\n" ou "\r\n" na gravação** mapeando o fluxo de dados e a atividade de cópia, respectivamente. <br>Quando o delimitador de linha é definido como nenhum delimitador (cadeia de caracteres vazia), o delimitador de coluna deve ser definido como nenhum delimitador (cadeia de caracteres vazia) também, o que significa tratar todo o conteúdo como um único valor.<br>Atualmente, o delimitador de linha como uma cadeia de caracteres vazia só tem suporte para mapear o fluxo de dados, mas não para a atividade | No       |
| quoteChar        | O caractere único para citar valores de coluna se ele contiver delimitador de coluna. <br>O valor padrão é **aspas duplas** `"` . <br>Quando `quoteChar` é definido como uma cadeia de caracteres vazia, isso significa que não há nenhum caractere de cotação e o valor da coluna não está entre aspas e `escapeChar` é usado para escapar o delimitador de coluna e ele mesmo. | No       |
| escapeChar       | O caractere único para escapar as aspas dentro de um valor entre aspas.<br>O valor padrão é **barra invertida `\`**. <br>Quando `escapeChar` é definido como uma cadeia de caracteres vazia, o `quoteChar` também deve ser definido como uma cadeia de caracteres vazia; nesse caso, verifique se todos os valores de coluna não contêm delimitadores. | No       |
| firstRowAsHeader | Especifica se deve tratar/transformar a primeira linha como uma linha de cabeçalho com nomes de colunas.<br>Os valores permitidos são **true** e **false** (padrão).<br>Quando a primeira linha como cabeçalho é falsa, observe que a visualização de dados da interface do usuário e a saída da atividade de pesquisa geram automaticamente nomes de coluna como Prop_ {n} (a partir de 0), a atividade de cópia requer [mapeamento explícito](copy-activity-schema-and-type-mapping.md#explicit-mapping) da origem para o coletor e localiza colunas por ordinal (começando de 1) e mapeando listas de fluxo de dados e localiza colunas com Column_ o nome  | No       |
| nullValue        | Especifica a representação de cadeia de caracteres do valor nulo. <br>O valor padrão é uma **cadeia de caracteres vazia**. | No       |
| encodingName     | O tipo de codificação usado para ler/gravar arquivos de teste. <br>Os valores permitidos são os seguintes: "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", "IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "IBM01149", "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13" , "ISO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1251", "WINDOWS-1252", "WINDOWS-1253", "WINDOWS-1254", "WINDOWS-1255", "WINDOWS-1256", "WINDOWS-1257", "WINDOWS-1258".<br>Observação o fluxo de dados de mapeamento não dá suporte à codificação UTF-7. | No       |
| compressionCodec | O codec de compactação usado para ler/gravar arquivos de texto. <br>Os valores permitidos são **bzip2**, **gzip**, **deflate**, **ZipDeflate**, **TarGzip**, **tar**, **snapshot** ou **lz4**. O padrão não é compactado. <br>**Observação** a atividade de cópia atualmente não dá suporte a "encaixar" & "lz4" e o fluxo de dados de mapeamento não dá suporte a "ZipDeflate", "TarGzip" e "tar". <br>**Observação** ao usar a atividade de cópia para descompactar /  / arquivo (s) ZipDeflate TarGzip **tar** e gravar no armazenamento de dados de coletor baseado em arquivo, por padrão os arquivos são extraídos para a pasta: `<path specified in dataset>/<folder named as source compressed file>/` , use `preserveZipFileNameAsFolder` / `preserveCompressionFileNameAsFolder` na [origem da atividade de cópia](#delimited-text-as-source) para controlar se deseja preservar o nome dos arquivos compactados como estrutura de pastas. | No       |
| compressionLevel | A taxa de compactação. <br>Os valores permitidos são **ideal** ou **mais rápido**.<br>- **Mais rápido:** A operação de compactação deve ser concluída o mais rápido possível, mesmo que o arquivo resultante não seja compactado de maneira ideal.<br>- **Ideal**: a operação de compactação deve ser corretamente compactada, mesmo se a operação levar mais tempo para ser concluída. Para saber mais, veja o tópico [Nível de compactação](/dotnet/api/system.io.compression.compressionlevel) . | No       |

Abaixo está um exemplo de conjunto de texto delimitado no armazenamento de BLOBs do Azure:

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
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
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "escapeChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela fonte de texto delimitada e pelo coletor.

### <a name="delimited-text-as-source"></a>Texto delimitado como fonte 

As propriedades a seguir têm suporte na seção ***\* origem \**** da atividade de cópia.

| Propriedade       | Descrição                                                  | Obrigatório |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | A propriedade Type da fonte da atividade de cópia deve ser definida como **DelimitedTextSource**. | Yes      |
| formatSettings | Um grupo de propriedades. Consulte a tabela de **configurações de leitura de texto delimitada** abaixo. |  No       |
| storeSettings  | Um grupo de propriedades sobre como ler dados de um armazenamento de dados. Cada conector baseado em arquivo tem suas próprias configurações de leitura com suporte em `storeSettings` . | No       |

**Configurações de leitura de texto delimitado** com suporte em `formatSettings` :

| Propriedade      | Descrição                                                  | Obrigatório |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | O tipo de formatSettings deve ser definido como **DelimitedTextReadSettings**. | Yes      |
| skipLineCount | Indica o número de linhas **não vazias** a serem ignoradas ao ler dados de arquivos de entrada. <br>Se skipLineCount e firstRowAsHeader forem especificados, primeiro as linhas serão ignoradas e, em seguida, as informações de cabeçalho serão lidas no arquivo de entrada. | No       |
| compactproperties | Um grupo de propriedades sobre como descompactar dados para um determinado codec de compactação. | No       |
| preserveZipFileNameAsFolder<br>(*em `compressionProperties` -> `type` como `ZipDeflateReadSettings`*) |  Aplica-se quando o conjunto de dados de entrada é configurado com compactação **ZipDeflate** . Indica se o nome do arquivo zip de origem deve ser preservado como estrutura de pastas durante a cópia.<br>-Quando definido como **true (padrão)**, data Factory grava arquivos descompactados em `<path specified in dataset>/<folder named as source zip file>/` .<br>-Quando definido como **false**, data Factory grava arquivos descompactados diretamente no `<path specified in dataset>` . Verifique se você não tem nomes de arquivo duplicados em arquivos zip de origem diferentes para evitar a corrida ou comportamento inesperado.  | No |
| preserveCompressionFileNameAsFolder<br>(*em `compressionProperties` -> `type` como `TarGZipReadSettings` ou `TarReadSettings`*)  | Aplica-se quando o conjunto de dados de entrada é configurado com / compactação **tar** TarGzip. Indica se o nome do arquivo compactado de origem deve ser preservado como estrutura de pastas durante a cópia.<br>-Quando definido como **true (padrão)**, data Factory grava arquivos descompactados em `<path specified in dataset>/<folder named as source compressed file>/` . <br>-Quando definido como **false**, data Factory grava arquivos descompactados diretamente no `<path specified in dataset>` . Verifique se você não tem nomes de arquivo duplicados em arquivos de origem diferentes para evitar a corrida ou comportamento inesperado. | No |

```json
"activities": [
    {
        "name": "CopyFromDelimitedText",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "DelimitedTextSource",
                "storeSettings": {
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                },
                "formatSettings": {
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 3,
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

### <a name="delimited-text-as-sink"></a>Texto delimitado como coletor

As propriedades a seguir têm suporte na seção ***\* coletor \**** de atividade de cópia.

| Propriedade       | Descrição                                                  | Obrigatório |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | A propriedade Type da fonte da atividade de cópia deve ser definida como **DelimitedTextSink**. | Yes      |
| formatSettings | Um grupo de propriedades. Consulte a tabela de **configurações de gravação de texto delimitado** abaixo. |    No      |
| storeSettings  | Um grupo de propriedades sobre como gravar dados em um armazenamento de dados. Cada conector baseado em arquivo tem suas próprias configurações de gravação com suporte em `storeSettings` .  | No       |

**Configurações de gravação de texto delimitado** com suporte em `formatSettings` :

| Propriedade      | Descrição                                                  | Obrigatório                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | O tipo de formatSettings deve ser definido como **DelimitedTextWriteSettings**. | Yes                                                   |
| fileExtension | A extensão de arquivo usada para nomear os arquivos de saída, por exemplo, `.csv` , `.txt` . Ele deve ser especificado quando o `fileName` não for especificado no conjunto de DelimitedText de saída. Quando o nome do arquivo for configurado no conjunto de resultados de saída, ele será usado como o nome do arquivo do coletor e a configuração da extensão do arquivo será ignorada.  | Sim quando o nome do arquivo não é especificado no conjunto de resultados de saída |
| maxRowsPerFile | Ao gravar dados em uma pasta, você pode optar por gravar em vários arquivos e especificar o máximo de linhas por arquivo.  | No |
| fileNamePrefix | Aplicável quando o `maxRowsPerFile` está configurado.<br> Especifique o prefixo do nome do arquivo ao gravar dados em vários arquivos, resultando neste padrão: `<fileNamePrefix>_00000.<fileExtension>` . Se não for especificado, o prefixo de nome de arquivo será gerado automaticamente. Essa propriedade não se aplica quando a origem é um armazenamento de [dados habilitado para opção](copy-activity-performance-features.md)de armazenamento ou de partição baseada em arquivo.  | No |

## <a name="mapping-data-flow-properties"></a>Propriedades do fluxo de dados de mapeamento

No mapeamento de fluxos de dados, você pode ler e gravar em formato de texto delimitado nos seguintes repositórios de dados: [armazenamento de BLOBs do Azure](connector-azure-blob-storage.md#mapping-data-flow-properties), [Azure data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)e [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties).

### <a name="source-properties"></a>Propriedades de origem

A tabela abaixo lista as propriedades com suporte por uma fonte de texto delimitada. Você pode editar essas propriedades na guia **Opções de origem** .

| Name | Descrição | Obrigatório | Valores permitidos | Propriedade de script de fluxo de dados |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Caminhos curinga | Todos os arquivos correspondentes ao caminho curinga serão processados. Substitui a pasta e o caminho do arquivo definido no conjunto de um. | não | String[] | wildcardPaths |
| Caminho raiz da partição | Para dados de arquivo particionados, você pode inserir um caminho raiz de partição para ler pastas particionadas como colunas | não | String | partitionRootPath |
| Lista de arquivos | Se sua fonte está apontando para um arquivo de texto que lista os arquivos a serem processados | não | `true` ou `false` | File |
| Linhas multilinha | O arquivo de origem contém linhas que abrangem várias linhas. Os valores de várias linhas devem estar entre aspas. | Não `true` ou `false` | multiLineRow |
| Coluna para armazenar o nome do arquivo | Criar uma nova coluna com o nome e o caminho do arquivo de origem | não | String | rowUrlColumn |
| Após a conclusão | Exclua ou mova os arquivos após o processamento. O caminho do arquivo inicia a partir da raiz do contêiner | não | Excluir: `true` ou `false` <br> Prosseguir `['<from>', '<to>']` | purgeFiles <br> MoveFile |
| Filtrar por última modificação | Escolher filtrar arquivos com base na última alteração | não | Timestamp | modifiedAfter <br> modifiedBefore |
| Não permitir nenhum arquivo encontrado | Se for true, um erro não será gerado se nenhum arquivo for encontrado | não | `true` ou `false` | ignoreNoFilesFound |

### <a name="source-example"></a>Exemplo de origem

A imagem abaixo é um exemplo de uma configuração de fonte de texto delimitada no mapeamento de fluxos de dados.

![Origem do DelimitedText](media/data-flow/delimited-text-source.png)

O script de fluxo de dados associado é:

```
source(
    allowSchemaDrift: true,
    validateSchema: false,
    multiLineRow: true,
    wildcardPaths:['*.csv']) ~> CSVSource
```

> [!NOTE]
> As fontes de fluxo de dados dão suporte a um conjunto limitado de mascaramento do Linux que é compatível com sistemas de arquivos do Hadoop

### <a name="sink-properties"></a>Propriedades do coletor

A tabela abaixo lista as propriedades com suporte por um coletor de texto delimitado. Você pode editar essas propriedades na guia **configurações** .

| Name | Descrição | Obrigatório | Valores permitidos | Propriedade de script de fluxo de dados |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Limpar a pasta | Se a pasta de destino for limpa antes da gravação | não | `true` ou `false` | truncate |
| Opção de nome de arquivo | O formato de nomenclatura dos dados gravados. Por padrão, um arquivo por partição no formato `part-#####-tid-<guid>` | não | Padrão: cadeia de caracteres <br> Por partição: cadeia de caracteres [] <br> Nomear arquivo como dados da coluna: cadeia de caracteres <br> Saída para arquivo único: `['<fileName>']` <br> Nomear pasta como dados da coluna: cadeia de caracteres | filePattern <br> partitionFileNames <br> rowUrlColumn <br> partitionFileNames <br> rowFolderUrlColumn |
| Cotar tudo | Incluir todos os valores entre aspas | não | `true` ou `false` | quoteAll |

rowFolderUrlColumn:

### <a name="sink-example"></a>Exemplo de coletor

A imagem abaixo é um exemplo de configuração de coletor de texto delimitado no mapeamento de fluxos de dados.

![Coletor de DelimitedText](media/data-flow/delimited-text-sink.png)

O script de fluxo de dados associado é:

```
CSVSource sink(allowSchemaDrift: true,
    validateSchema: false,
    truncate: true,
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> CSVSink
```

## <a name="next-steps"></a>Próximas etapas

- [Visão geral da atividade de cópia](copy-activity-overview.md)
- [Fluxo de dados de mapeamento](concepts-data-flow-overview.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)
- [Atividade GetMetadata](control-flow-get-metadata-activity.md)
