---
title: Formato XML no Azure Data Factory
description: Este tópico descreve como lidar com o formato XML no Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/29/2020
ms.author: jingwang
ms.openlocfilehash: 1555d8b97f89b567cb1769c0ba10ecf50bca4366
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352585"
---
# <a name="xml-format-in-azure-data-factory"></a>Formato XML no Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Siga este artigo quando desejar **analisar os arquivos XML**. 

Há suporte para o formato XML para os seguintes conectores: [Amazon S3](connector-amazon-simple-storage-service.md), [blob do Azure](connector-azure-blob-storage.md), [Azure data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md), [armazenamento de arquivos do Azure](connector-azure-file-storage.md), [sistema de arquivos](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)e [SFTP](connector-sftp.md). Há suporte como origem, mas não coletor.

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [Conjuntos de Dados](concepts-datasets-linked-services.md). Esta seção fornece uma lista das propriedades com suporte pelo conjunto de XML.

| Propriedade         | Descrição                                                  | Obrigatório |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | A propriedade Type do conjunto de conjuntos deve ser definida como **XML**. | Yes      |
| local         | Configurações de local dos arquivos. Cada conector baseado em arquivo tem seu próprio tipo de local e propriedades com suporte em `location` . **Consulte os detalhes no artigo do conector – > seção Propriedades do conjunto de informações**. | Yes      |
| encodingName     | O tipo de codificação usado para ler/gravar arquivos de teste. <br>Os valores permitidos são os seguintes: "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", "IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "IBM01149", "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13" , "ISO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1251", "WINDOWS-1252", "WINDOWS-1253", "WINDOWS-1254", "WINDOWS-1255", "WINDOWS-1256", "WINDOWS-1257", "WINDOWS-1258".| No       |
| nullValue | Especifica a representação de cadeia de caracteres do valor nulo.<br/>O valor padrão é uma **cadeia de caracteres vazia**. | No |
| compactação | Grupo de propriedades para configurar a compactação de arquivo. Configure esta seção quando desejar fazer compactação/descompactação durante a execução da atividade. | Não |
| type<br>(*em `compression`*) | O codec de compactação usado para ler/gravar arquivos XML. <br>Os valores permitidos são **bzip2**, **gzip**, **deflate**, **ZipDeflate**, **TarGzip**, **tar**, **snapshot** ou **lz4**. O padrão não é compactado.<br>**Observação** a atividade de cópia atualmente não dá suporte a "encaixar" & "lz4" e o fluxo de dados de mapeamento não dá suporte a "ZipDeflate", "TarGzip" e "tar".<br>**Observação** ao usar a atividade de cópia para **ZipDeflate** descompactar / **TarGzip** / arquivo (s) ZipDeflate TarGzip **tar** e gravar no armazenamento de dados de coletor baseado em arquivo, por padrão os arquivos são extraídos para a pasta: `<path specified in dataset>/<folder named as source compressed file>/` , use `preserveZipFileNameAsFolder` / `preserveCompressionFileNameAsFolder` na [origem da atividade de cópia](#xml-as-source) para controlar se deseja preservar o nome dos arquivos compactados como estrutura de pastas. | Não.  |
| nível<br/>(*em `compression`*) | A taxa de compactação. <br>Os valores permitidos são **ideal** ou **mais rápido**.<br>- **Mais rápido:** A operação de compactação deve ser concluída o mais rápido possível, mesmo que o arquivo resultante não seja compactado de maneira ideal.<br>- **Ideal**: a operação de compactação deve ser corretamente compactada, mesmo se a operação levar mais tempo para ser concluída. Para saber mais, veja o tópico [Nível de compactação](/dotnet/api/system.io.compression.compressionlevel) . | No       |

Veja abaixo um exemplo de conjunto de um XML no armazenamento de BLOBs do Azure:

```json
{
    "name": "XMLDataset",
    "properties": {
        "type": "Xml",
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

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela origem XML.

Saiba mais sobre como mapear dados XML e o formato/armazenamento de dados do coletor do [mapeamento de esquema](copy-activity-schema-and-type-mapping.md). Ao visualizar arquivos XML, os dados são mostrados com a hierarquia JSON e você usa o caminho JSON para apontar para os campos.

### <a name="xml-as-source"></a>XML como fonte

As propriedades a seguir têm suporte na seção atividade de cópia **_ \_ origem \****. Saiba mais sobre o [comportamento do conector XML](#xml-connector-behavior).

| Propriedade      | Descrição                                                  | Obrigatório |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | A propriedade Type da fonte da atividade de cópia deve ser definida como **xmlSource**. | Yes      |
| formatSettings | Um grupo de propriedades. Consulte a tabela de **configurações de leitura XML** abaixo. | No       |
| storeSettings | Um grupo de propriedades sobre como ler dados de um armazenamento de dados. Cada conector baseado em arquivo tem suas próprias configurações de leitura com suporte em `storeSettings` . **Veja os detalhes no artigo do conector – > seção Propriedades da atividade de cópia**. | No       |

**Configurações de leitura XML** com suporte em `formatSettings` :

| Propriedade      | Descrição                                                  | Obrigatório |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | O tipo de formatSettings deve ser definido como **XmlReadSettings**. | Yes      |
| validationmode | Especifica se o esquema XML deve ser validado.<br>Os valores permitidos são **nenhum** (padrão, sem validação), **XSD** (validar usando xsd), **DTD** (validar usando DTD). | Não |
| namespaces | Se é para habilitar o namespace ao analisar os arquivos XML. Os valores permitidos são: **true** (padrão), **false**. | Não |
| namespacePrefixes | URI de namespace para mapeamento de prefixo, que é usado para nomear campos ao analisar o arquivo XML.<br/>Se um arquivo XML tiver namespace e namespace estiver habilitado, por padrão, o nome do campo será o mesmo que no documento XML.<br>Se houver um item definido para o URI do namespace neste mapa, o nome do campo será `prefix:fieldName` . | No |
| detectDataType | Se os tipos de dados inteiro, duplo e booliano devem ser detectados. Os valores permitidos são: **true** (padrão), **false**.| Não |
| compactproperties | Um grupo de propriedades sobre como descompactar dados para um determinado codec de compactação. | No       |
| preserveZipFileNameAsFolder<br>(*em `compressionProperties` -> `type` como `ZipDeflateReadSettings`*)  | Aplica-se quando o conjunto de dados de entrada é configurado com compactação **ZipDeflate** . Indica se o nome do arquivo zip de origem deve ser preservado como estrutura de pastas durante a cópia.<br>-Quando definido como **true (padrão)**, data Factory grava arquivos descompactados em `<path specified in dataset>/<folder named as source zip file>/` .<br>-Quando definido como **false**, data Factory grava arquivos descompactados diretamente no `<path specified in dataset>` . Verifique se você não tem nomes de arquivo duplicados em arquivos zip de origem diferentes para evitar a corrida ou comportamento inesperado.  | No |
| preserveCompressionFileNameAsFolder<br>(*em `compressionProperties` -> `type` como `TarGZipReadSettings` ou `TarReadSettings`*) | Aplica-se quando o conjunto de **TarGzip** dados de entrada é configurado com / compactação **tar** TarGzip. Indica se o nome do arquivo compactado de origem deve ser preservado como estrutura de pastas durante a cópia.<br>-Quando definido como **true (padrão)**, data Factory grava arquivos descompactados em `<path specified in dataset>/<folder named as source compressed file>/` . <br>-Quando definido como **false**, data Factory grava arquivos descompactados diretamente no `<path specified in dataset>` . Verifique se você não tem nomes de arquivo duplicados em arquivos de origem diferentes para evitar a corrida ou comportamento inesperado. | No |

## <a name="mapping-data-flow-properties"></a>Propriedades do fluxo de dados de mapeamento

No mapeamento de fluxos de dados, você pode ler e gravar em formato XML nos seguintes repositórios de dados: [armazenamento de BLOBs do Azure](connector-azure-blob-storage.md#mapping-data-flow-properties), [Azure data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)e [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties). Você pode apontar para arquivos XML usando o conjunto de programas XML ou um [conjunto de linhas embutido](data-flow-source.md#inline-datasets).

### <a name="source-properties"></a>Propriedades de origem

A tabela abaixo lista as propriedades com suporte por uma origem XML. Você pode editar essas propriedades na guia **Opções de origem** . Saiba mais sobre o [comportamento do conector XML](#xml-connector-behavior). Ao usar o conjunto de linhas embutido, você verá configurações de arquivo adicionais, que são iguais às propriedades descritas na seção [Propriedades do conjunto](#dataset-properties) de cores. 

| Nome | Descrição | Obrigatório | Valores permitidos | Propriedade de script de fluxo de dados |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Caminhos curinga | Todos os arquivos correspondentes ao caminho curinga serão processados. Substitui a pasta e o caminho do arquivo definido no conjunto de um. | No | String[] | wildcardPaths |
| Caminho raiz da partição | Para dados de arquivo particionados, você pode inserir um caminho raiz de partição para ler pastas particionadas como colunas | Não | String | partitionRootPath |
| Lista de arquivos | Se sua fonte está apontando para um arquivo de texto que lista os arquivos a serem processados | No | `true` ou `false` | File |
| Coluna para armazenar o nome do arquivo | Criar uma nova coluna com o nome e o caminho do arquivo de origem | Não | String | rowUrlColumn |
| Após a conclusão | Exclua ou mova os arquivos após o processamento. O caminho do arquivo inicia a partir da raiz do contêiner | No | Excluir: `true` ou `false` <br> Prosseguir `['<from>', '<to>']` | purgeFiles <br>MoveFile |
| Filtrar por última modificação | Escolher filtrar arquivos com base na última alteração | No | Timestamp | modifiedAfter <br>modifiedBefore |
| Modo de validação | Especifica se o esquema XML deve ser validado. | No | `None` (padrão, sem validação)<br>`xsd` (validar usando XSD)<br>`dtd` (validar usando DTD). | validationmode |
| Namespaces | Se é para habilitar o namespace ao analisar os arquivos XML. | No | `true` (padrão) ou `false` | namespaces |
| Pares de prefixo de namespace | URI de namespace para mapeamento de prefixo, que é usado para nomear campos ao analisar o arquivo XML.<br/>Se um arquivo XML tiver namespace e namespace estiver habilitado, por padrão, o nome do campo será o mesmo que no documento XML.<br>Se houver um item definido para o URI do namespace neste mapa, o nome do campo será `prefix:fieldName` . | No | Matriz com padrão`['URI1'->'prefix1','URI2'->'prefix2']` | namespacePrefixes |
| Não permitir nenhum arquivo encontrado | Se for true, um erro não será gerado se nenhum arquivo for encontrado | no | `true` ou `false` | ignoreNoFilesFound |

### <a name="xml-source-script-example"></a>Exemplo de script de origem XML

O script abaixo é um exemplo de uma configuração de origem XML no mapeamento de fluxos de dados usando o modo DataSet.

```
source(allowSchemaDrift: true,
    validateSchema: false,
    validationMode: 'xsd',
    namespaces: true) ~> XMLSource
```

O script abaixo é um exemplo de uma configuração de origem XML usando o modo de conjunto de linhas embutido.

```
source(allowSchemaDrift: true,
    validateSchema: false,
    format: 'xml',
    fileSystem: 'filesystem',
    folderPath: 'folder',
    validationMode: 'xsd',
    namespaces: true) ~> XMLSource
```

## <a name="xml-connector-behavior"></a>Comportamento do conector XML

Observe o seguinte ao usar XML como fonte.

- Atributos XML:

    - Os atributos de um elemento são analisados como os subcampos do elemento na hierarquia.
    - O nome do campo de atributo segue o padrão `@attributeName` .

- Validação de esquema XML:

    - Você pode optar por não validar o esquema ou validar o esquema usando XSD ou DTD.
    - Ao usar XSD ou DTD para validar arquivos XML, o XSD/DTD deve ser referenciado dentro dos arquivos XML por meio do caminho relativo.

- Manipulação de namespace:

    - O namespace pode ser desabilitado ao usar o fluxo de dados; nesse caso, os atributos que definem o namespace serão analisados como atributos normais.
    - Quando o namespace está habilitado, os nomes do elemento e dos atributos seguem o padrão       `namespaceUri,elementName` e `namespaceUri,@attributeName` , por padrão. Você pode definir o prefixo de namespace para cada URI de namespace na origem; nesse caso, os nomes do elemento e dos atributos seguem o padrão `definedPrefix:elementName` ou `definedPrefix:@attributeName` em vez disso.

- Coluna de valor:

    - Se um elemento XML tiver um valor de texto simples e atributos/elementos filho, o valor de texto simples será analisado como o valor de uma "coluna de valor" com o nome de campo interno `_value_` . E também herda o namespace do elemento, se aplicável.

## <a name="next-steps"></a>Próximas etapas

- [Visão geral da atividade de cópia](copy-activity-overview.md)
- [Fluxo de dados de mapeamento](concepts-data-flow-overview.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)
- [Atividade GetMetadata](control-flow-get-metadata-activity.md)