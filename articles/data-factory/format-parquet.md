---
title: Formato parquet no Azure Data Factory
description: Este tópico descreve como lidar com o formato parquet no Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/27/2020
ms.author: jingwang
ms.openlocfilehash: a10403b5f26b551458a9e20330bc817512f707de
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100386384"
---
# <a name="parquet-format-in-azure-data-factory"></a>Formato parquet no Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Siga este artigo quando desejar **analisar os arquivos parquet ou gravar os dados no formato parquet**. 

O formato parquet tem suporte para os seguintes conectores: [Amazon S3](connector-amazon-simple-storage-service.md), [blob do Azure](connector-azure-blob-storage.md), [Azure data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md), [armazenamento de arquivos do Azure](connector-azure-file-storage.md), [sistema de arquivos](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)e [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [Conjuntos de Dados](concepts-datasets-linked-services.md). Esta seção fornece uma lista das propriedades com suporte pelo conjunto de parquet.

| Propriedade         | Descrição                                                  | Obrigatório |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | A propriedade Type do conjunto de conjuntos deve ser definida como **parquet**. | Sim      |
| local         | Configurações de local dos arquivos. Cada conector baseado em arquivo tem seu próprio tipo de local e propriedades com suporte em `location` . **Consulte os detalhes no artigo do conector – > seção Propriedades do conjunto de informações**. | Sim      |
| compressionCodec | O codec de compactação a ser usado ao gravar em arquivos parquet. Ao ler de arquivos parquet, as fábricas de dados determinam automaticamente o codec de compactação com base nos metadados do arquivo.<br>Os tipos com suporte são "**None**", "**gzip**", "**encaixado**" (padrão) e "**LZO**". Observação a atividade de cópia atualmente não dá suporte a LZO quando arquivos parquet de leitura/gravação. | Não       |

> [!NOTE]
> Não há suporte para o espaço em branco no nome da coluna para arquivos parquet.

Veja abaixo um exemplo de conjunto de parquet no armazenamento de BLOBs do Azure:

```json
{
    "name": "ParquetDataset",
    "properties": {
        "type": "Parquet",
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
            "compressionCodec": "snappy"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela origem e pelo coletor do parquet.

### <a name="parquet-as-source"></a>Parquet como fonte

As propriedades a seguir têm suporte na seção ***\* origem \**** da atividade de cópia.

| Propriedade      | Descrição                                                  | Obrigatório |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | A propriedade Type da fonte da atividade de cópia deve ser definida como **ParquetSource**. | Sim      |
| storeSettings | Um grupo de propriedades sobre como ler dados de um armazenamento de dados. Cada conector baseado em arquivo tem suas próprias configurações de leitura com suporte em `storeSettings` . **Veja os detalhes no artigo do conector – > seção Propriedades da atividade de cópia**. | Não       |

### <a name="parquet-as-sink"></a>Parquet como coletor

As propriedades a seguir têm suporte na seção ***\* coletor \**** de atividade de cópia.

| Propriedade      | Descrição                                                  | Obrigatório |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | A propriedade Type do coletor da atividade de cópia deve ser definida como **ParquetSink**. | Sim      |
| formatSettings | Um grupo de propriedades. Consulte a tabela **configurações de gravação de parquet** abaixo. |    Não      |
| storeSettings | Um grupo de propriedades sobre como gravar dados em um armazenamento de dados. Cada conector baseado em arquivo tem suas próprias configurações de gravação com suporte em `storeSettings` . **Veja os detalhes no artigo do conector – > seção Propriedades da atividade de cópia**. | Não       |

**Configurações de gravação de parquet** com suporte em `formatSettings` :

| Propriedade      | Descrição                                                  | Obrigatório                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | O tipo de formatSettings deve ser definido como **ParquetWriteSettings**. | Sim                                                   |
| maxRowsPerFile | Ao gravar dados em uma pasta, você pode optar por gravar em vários arquivos e especificar o máximo de linhas por arquivo.  | Não |
| fileNamePrefix | Aplicável quando o `maxRowsPerFile` está configurado.<br> Especifique o prefixo do nome do arquivo ao gravar dados em vários arquivos, resultando neste padrão: `<fileNamePrefix>_00000.<fileExtension>` . Se não for especificado, o prefixo de nome de arquivo será gerado automaticamente. Essa propriedade não se aplica quando a origem é um armazenamento de [dados habilitado para opção](copy-activity-performance-features.md)de armazenamento ou de partição baseada em arquivo.  | Não |

## <a name="mapping-data-flow-properties"></a>Propriedades do fluxo de dados de mapeamento

No mapeamento de fluxos de dados, você pode ler e gravar no formato parquet nos seguintes armazenamentos de dados: [armazenamento de BLOBs do Azure](connector-azure-blob-storage.md#mapping-data-flow-properties), [Azure data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)e [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties).

### <a name="source-properties"></a>Propriedades de origem

A tabela abaixo lista as propriedades com suporte por uma fonte parquet. Você pode editar essas propriedades na guia **Opções de origem** .

| Nome | Descrição | Obrigatório | Valores permitidos | Propriedade de script de fluxo de dados |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Formatar | O formato deve ser `parquet` | sim | `parquet` | format |
| Caminhos curinga | Todos os arquivos correspondentes ao caminho curinga serão processados. Substitui a pasta e o caminho do arquivo definido no conjunto de um. | não | String[] | wildcardPaths |
| Caminho raiz da partição | Para dados de arquivo particionados, você pode inserir um caminho raiz de partição para ler pastas particionadas como colunas | não | String | partitionRootPath |
| Lista de arquivos | Se sua fonte está apontando para um arquivo de texto que lista os arquivos a serem processados | não | `true` ou `false` | File |
| Coluna para armazenar o nome do arquivo | Criar uma nova coluna com o nome e o caminho do arquivo de origem | não | String | rowUrlColumn |
| Após a conclusão | Exclua ou mova os arquivos após o processamento. O caminho do arquivo inicia a partir da raiz do contêiner | não | Excluir: `true` ou `false` <br> Prosseguir `[<from>, <to>]` | purgeFiles <br> MoveFile |
| Filtrar por última modificação | Escolher filtrar arquivos com base na última alteração | não | Timestamp | modifiedAfter <br> modifiedBefore |
| Não permitir nenhum arquivo encontrado | Se for true, um erro não será gerado se nenhum arquivo for encontrado | não | `true` ou `false` | ignoreNoFilesFound |

### <a name="source-example"></a>Exemplo de origem

A imagem abaixo é um exemplo de uma configuração de origem parquet no mapeamento de fluxos de dados.

![Origem do parquet](media/data-flow/parquet-source.png)

O script de fluxo de dados associado é:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    rowUrlColumn: 'fileName',
    format: 'parquet') ~> ParquetSource
```

### <a name="sink-properties"></a>Propriedades do coletor

A tabela abaixo lista as propriedades com suporte de um coletor parquet. Você pode editar essas propriedades na guia **configurações** .

| Nome | Descrição | Obrigatório | Valores permitidos | Propriedade de script de fluxo de dados |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Formatar | O formato deve ser `parquet` | sim | `parquet` | format |
| Limpar a pasta | Se a pasta de destino for limpa antes da gravação | não | `true` ou `false` | truncate |
| Opção de nome de arquivo | O formato de nomenclatura dos dados gravados. Por padrão, um arquivo por partição no formato `part-#####-tid-<guid>` | não | Padrão: cadeia de caracteres <br> Por partição: cadeia de caracteres [] <br> Como dados na coluna: String <br> Saída para arquivo único: `['<fileName>']` | filePattern <br> partitionFileNames <br> rowUrlColumn <br> partitionFileNames |

### <a name="sink-example"></a>Exemplo de coletor

A imagem abaixo é um exemplo de configuração do coletor parquet no mapeamento de fluxos de dados.

![Coletor de parquet](media/data-flow/parquet-sink.png)

O script de fluxo de dados associado é:

```
ParquetSource sink(
    format: 'parquet',
    filePattern:'output[n].parquet',
    truncate: true,
    allowSchemaDrift: true,
    validateSchema: false,
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> ParquetSink
```

## <a name="data-type-support"></a>Suporte do tipo de dados

Os tipos de dados complexos parquet (por exemplo, mapa, lista, STRUCT) atualmente têm suporte apenas em fluxos de dados, não na atividade de cópia. Para usar tipos complexos em fluxos de dados, não importe o esquema de arquivo no conjunto de dados, deixando o esquema em branco no conjunto de dado. Em seguida, na transformação origem, importe a projeção.

## <a name="using-self-hosted-integration-runtime"></a>Usando Integration Runtime auto-hospedados

> [!IMPORTANT]
> Para a cópia habilitada pelo Integration Runtime auto-hospedado, por exemplo, entre armazenamentos de dados locais e na nuvem, se você não estiver copiando arquivos parquet no estado em que se **encontra**, precisará instalar o **pacote redistribuível** do **JRE 8 de 64 bits (Java Runtime Environment) ou OpenJDK** e Microsoft Visual C++ 2010 no seu computador ir. Verifique o parágrafo a seguir com mais detalhes.

Para a cópia em execução no IR auto-hospedado com a serialização/desserialização de arquivo parquet, o ADF localiza o tempo de execução do Java verificando primeiro o registro do *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* jre, se não for encontrado, verificando a variável do sistema em segundo lugar *`JAVA_HOME`* para OpenJDK.

- **Para usar o JRE**: o IR de 64 bits requer o jre de 64 bits. É possível encontrá-lo [aqui](https://go.microsoft.com/fwlink/?LinkId=808605).
- **Para usar OpenJDK**: tem suporte desde a versão de ir 3,13. Empacote o jvm.dll com todos os outros assemblies necessários do OpenJDK no IR auto-hospedado do computador e defina a variável de ambiente JAVA_HOME adequadamente.
- **Para instalar Visual C++ pacote redistribuível 2010**: o pacote redistribuível Visual C++ 2010 não está instalado com instalações de ir de hospedagem interna. É possível encontrá-lo [aqui](https://www.microsoft.com/download/details.aspx?id=14632).

> [!TIP]
> Se você copiar os dados para/do formato Parquet usando o IR auto-hospedado e ocorrências de erro informando que "Ocorreu um erro ao chamar o java, mensagem: **espaço de heap java.lang.OutOfMemoryError:Java**", poderá adicionar uma variável de ambiente `_JAVA_OPTIONS` na máquina que hospeda o IR auto-hospedado para ajustar o tamanho mín/máx do heap para JVM para capacitar a cópia e executar novamente o pipeline.

![Definir o tamanho do heap da JVM no IR auto-hospedado](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Exemplo: defina a variável `_JAVA_OPTIONS` com o valor `-Xms256m -Xmx16g`. O sinalizador `Xms` especifica o pool de alocação de memória inicial para uma JVM (Máquina Virtual Java), enquanto `Xmx` especifica o pool de alocação de memória máxima. Isso significa que a JVM será iniciada com `Xms` quantidade de memória e será capaz de usar um máximo de `Xmx` quantidade de memória. Por padrão, o ADF usa mín. 64 MB e máximo 1G.

## <a name="next-steps"></a>Próximas etapas

- [Visão geral da atividade de cópia](copy-activity-overview.md)
- [Fluxo de dados de mapeamento](concepts-data-flow-overview.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)
- [Atividade GetMetadata](control-flow-get-metadata-activity.md)
