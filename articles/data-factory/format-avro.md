---
title: Formato Avro no Azure Data Factory
description: Este tópico descreve como lidar com o formato Avro no Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: jingwang
ms.openlocfilehash: 4be499e8e304f34b1cab10aed41b5b98a5f24e9b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100392588"
---
# <a name="avro-format-in-azure-data-factory"></a>Formato Avro no Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Siga este artigo quando desejar **analisar os arquivos Avro ou gravar os dados no formato Avro**. 

O formato Avro tem suporte para os seguintes conectores: [Amazon S3](connector-amazon-simple-storage-service.md), [blob do Azure](connector-azure-blob-storage.md), [Azure data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md), [armazenamento de arquivos do Azure](connector-azure-file-storage.md), [sistema de arquivos](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)e [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [Conjuntos de Dados](concepts-datasets-linked-services.md). Esta seção fornece uma lista das propriedades com suporte pelo conjunto de conjuntos Avro.

| Propriedade         | Descrição                                                  | Obrigatório |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | A propriedade Type do conjunto de conjuntos deve ser definida como **Avro**. | Sim      |
| local         | Configurações de local dos arquivos. Cada conector baseado em arquivo tem seu próprio tipo de local e propriedades com suporte em `location` . **Consulte os detalhes no artigo do conector – > seção Propriedades do conjunto de informações**. | Sim      |
| avroCompressionCodec | O codec de compactação a ser usado ao gravar em arquivos Avro. Ao ler de arquivos do Avro, Data Factory determina automaticamente o codec de compactação com base nos metadados do arquivo.<br>Os tipos com suporte são "**None**" (padrão), "**deflate**", "**encaixar**". Observação a atividade de cópia atualmente não dá suporte a instantâneos quando leitura/gravação de arquivos Avro. | Não       |

> [!NOTE]
> Não há suporte para o espaço em branco no nome da coluna para arquivos Avro.

A seguir está um exemplo de conjunto de exemplos de Avro no armazenamento de BLOBs do Azure:

```json
{
    "name": "AvroDataset",
    "properties": {
        "type": "Avro",
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
            "avroCompressionCodec": "snappy"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela fonte e pelo coletor do Avro.

### <a name="avro-as-source"></a>Avro como fonte

As propriedades a seguir têm suporte na seção ***\* origem \**** da atividade de cópia.

| Propriedade      | Descrição                                                  | Obrigatório |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | A propriedade Type da fonte da atividade de cópia deve ser definida como **AvroSource**. | Sim      |
| storeSettings | Um grupo de propriedades sobre como ler dados de um armazenamento de dados. Cada conector baseado em arquivo tem suas próprias configurações de leitura com suporte em `storeSettings` . **Veja os detalhes no artigo do conector – > seção Propriedades da atividade de cópia**. | Não       |

### <a name="avro-as-sink"></a>Avro como coletor

As propriedades a seguir têm suporte na seção ***\* coletor \**** de atividade de cópia.

| Propriedade      | Descrição                                                  | Obrigatório |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | A propriedade Type da fonte da atividade de cópia deve ser definida como **AvroSink**. | Sim      |
| formatSettings          | Um grupo de propriedades. Consulte a tabela de **configurações de gravação do Avro** abaixo.| Não      |
| storeSettings | Um grupo de propriedades sobre como gravar dados em um armazenamento de dados. Cada conector baseado em arquivo tem suas próprias configurações de gravação com suporte em `storeSettings` . **Veja os detalhes no artigo do conector – > seção Propriedades da atividade de cópia**. | Não       |

**Configurações de gravação de Avro** com suporte em `formatSettings` :

| Propriedade      | Descrição                                                  | Obrigatório                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | O tipo de formatSettings deve ser definido como **AvroWriteSettings**. | Sim                                                   |
| maxRowsPerFile | Ao gravar dados em uma pasta, você pode optar por gravar em vários arquivos e especificar o máximo de linhas por arquivo.  | Não |
| fileNamePrefix | Aplicável quando o `maxRowsPerFile` está configurado.<br> Especifique o prefixo do nome do arquivo ao gravar dados em vários arquivos, resultando neste padrão: `<fileNamePrefix>_00000.<fileExtension>` . Se não for especificado, o prefixo de nome de arquivo será gerado automaticamente. Essa propriedade não se aplica quando a origem é um armazenamento de [dados habilitado para opção](copy-activity-performance-features.md)de armazenamento ou de partição baseada em arquivo.  | Não |

## <a name="mapping-data-flow-properties"></a>Propriedades do fluxo de dados de mapeamento

No mapeamento de fluxos de dados, você pode ler e gravar no formato Avro nos seguintes armazenamentos de dados: [armazenamento de BLOBs do Azure](connector-azure-blob-storage.md#mapping-data-flow-properties), [Azure data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)e [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties).

### <a name="source-properties"></a>Propriedades de origem

A tabela abaixo lista as propriedades com suporte por uma fonte Avro. Você pode editar essas propriedades na guia **Opções de origem** .

| Nome | Descrição | Obrigatório | Valores permitidos | Propriedade de script de fluxo de dados |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Caminhos curinga | Todos os arquivos correspondentes ao caminho curinga serão processados. Substitui a pasta e o caminho do arquivo definido no conjunto de um. | não | String[] | wildcardPaths |
| Caminho raiz da partição | Para dados de arquivo particionados, você pode inserir um caminho raiz de partição para ler pastas particionadas como colunas | não | String | partitionRootPath |
| Lista de arquivos | Se sua fonte está apontando para um arquivo de texto que lista os arquivos a serem processados | não | `true` ou `false` | File |
| Coluna para armazenar o nome do arquivo | Criar uma nova coluna com o nome e o caminho do arquivo de origem | não | String | rowUrlColumn |
| Após a conclusão | Exclua ou mova os arquivos após o processamento. O caminho do arquivo inicia a partir da raiz do contêiner | não | Excluir: `true` ou `false` <br> Prosseguir `['<from>', '<to>']` | purgeFiles <br> MoveFile |
| Filtrar por última modificação | Escolher filtrar arquivos com base na última alteração | não | Timestamp | modifiedAfter <br> modifiedBefore |
| Não permitir nenhum arquivo encontrado | Se for true, um erro não será gerado se nenhum arquivo for encontrado | não | `true` ou `false` | ignoreNoFilesFound |

### <a name="sink-properties"></a>Propriedades do coletor

A tabela abaixo lista as propriedades com suporte por um coletor de Avro. Você pode editar essas propriedades na guia **configurações** .

| Nome | Descrição | Obrigatório | Valores permitidos | Propriedade de script de fluxo de dados |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Limpar a pasta | Se a pasta de destino for limpa antes da gravação | não | `true` ou `false` | truncate |
| Opção de nome de arquivo | O formato de nomenclatura dos dados gravados. Por padrão, um arquivo por partição no formato `part-#####-tid-<guid>` | não | Padrão: cadeia de caracteres <br> Por partição: cadeia de caracteres [] <br> Como dados na coluna: String <br> Saída para arquivo único: `['<fileName>']`  | filePattern <br> partitionFileNames <br> rowUrlColumn <br> partitionFileNames |
| Cotar tudo | Incluir todos os valores entre aspas | não | `true` ou `false` | quoteAll |

## <a name="data-type-support"></a>Suporte do tipo de dados

### <a name="copy-activity"></a>Atividade de cópia
[Os tipos de dados complexos](https://avro.apache.org/docs/current/spec.html#schema_complex) do Avro não têm suporte (registros, enumerações, matrizes, mapas, uniões e fixos) na atividade de cópia.

### <a name="data-flows"></a>Fluxos de dados
Ao trabalhar com arquivos Avro em fluxos de dados, você pode ler e gravar tipos de dados complexos, mas certifique-se de limpar primeiro o esquema físico do DataSet. Em fluxos de dados, você pode definir sua projeção lógica e derivar colunas que são estruturas complexas e, em seguida, mapear automaticamente esses campos para um arquivo Avro.

## <a name="next-steps"></a>Próximas etapas

- [Visão geral da atividade de cópia](copy-activity-overview.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)
- [Atividade GetMetadata](control-flow-get-metadata-activity.md)
