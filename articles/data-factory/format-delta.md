---
title: Formato Delta no Azure Data Factory
description: Transformar e mover dados de um lago Delta usando o formato Delta
author: djpmsft
ms.service: data-factory
ms.topic: conceptual
ms.date: 12/07/2020
ms.author: daperlov
ms.openlocfilehash: bb5360a678751b37cf36677fca611b39746621f4
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100386485"
---
# <a name="delta-format-in-azure-data-factory"></a>Formato Delta no Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo destaca como copiar dados de e para um data Lake Delta armazenado no [Azure data Lake Store Gen2](connector-azure-data-lake-storage.md) ou no [armazenamento de BLOBs do Azure](connector-azure-blob-storage.md) usando o formato Delta. Esse conector está disponível como um [conjunto de dados embutido](data-flow-source.md#inline-datasets) no mapeamento de fluxos de entrada como uma fonte e um coletor.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4ALTs]

## <a name="mapping-data-flow-properties"></a>Propriedades do fluxo de dados de mapeamento

Esse conector está disponível como um [conjunto de dados embutido](data-flow-source.md#inline-datasets) no mapeamento de fluxos de entrada como uma fonte e um coletor.

### <a name="source-properties"></a>Propriedades de origem

A tabela abaixo lista as propriedades com suporte por uma fonte Delta. Você pode editar essas propriedades na guia **Opções de origem** .

| Nome | Descrição | Necessária | Valores permitidos | Propriedade de script de fluxo de dados |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Formatar | O formato deve ser `delta` | sim | `delta` | format |
| Sistema de arquivos | O sistema de contêiner/arquivos do Delta Lake | sim | String | fileSystem |
| Caminho da pasta | O Direct Lake do Delta | sim | String | folderPath |
| Tipo de compactação | O tipo de compactação da tabela Delta | não | `bzip2`<br>`gzip`<br>`deflate`<br>`ZipDeflate`<br>`snappy`<br>`lz4` | CompressionType |
| Nível de compactação | Escolha se a compactação será concluída o mais rapidamente possível ou se o arquivo resultante deve ser compactado de forma ideal. | obrigatório se `compressedType` for especificado. | `Optimal` ou `Fastest` | compressionLevel |
| Viagem de tempo | Escolha se deseja consultar um instantâneo mais antigo de uma tabela Delta | não | Consultar por carimbo de data/hora: timestamp <br> Consultar por versão: inteiro | timestampAsOf <br> versionAsOf |
| Não permitir nenhum arquivo encontrado | Se for true, um erro não será gerado se nenhum arquivo for encontrado | não | `true` ou `false` | ignoreNoFilesFound |

#### <a name="import-schema"></a>Importar esquema

O Delta só está disponível como um conjunto de uma embutido e, por padrão, não tem um esquema associado. Para obter metadados de coluna, clique no botão **importar esquema** na guia **projeção** . Isso permitirá que você referencie os nomes de coluna e os tipos de dados especificados pelo corpus. Para importar o esquema, uma [sessão de depuração de fluxo de dados](concepts-data-flow-debug-mode.md) deve estar ativa e você deve ter um arquivo de definição de entidade CDM existente para apontar.
 

### <a name="delta-source-script-example"></a>Exemplo de script de origem Delta

```
source(output(movieId as integer,
            title as string,
            releaseDate as date,
            rated as boolean,
            screenedOn as timestamp,
            ticketPrice as decimal(10,2)
            ),
    store: 'local',
    format: 'delta',
    versionAsOf: 0,
    allowSchemaDrift: false,
    folderPath: $tempPath + '/delta'
  ) ~> movies
```

### <a name="sink-properties"></a>Propriedades do coletor

A tabela abaixo lista as propriedades com suporte de um coletor Delta. Você pode editar essas propriedades na guia **configurações** .

| Nome | Descrição | Necessária | Valores permitidos | Propriedade de script de fluxo de dados |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Formatar | O formato deve ser `delta` | sim | `delta` | format |
| Sistema de arquivos | O sistema de contêiner/arquivos do Delta Lake | sim | String | fileSystem |
| Caminho da pasta | O Direct Lake do Delta | sim | String | folderPath |
| Tipo de compactação | O tipo de compactação da tabela Delta | não | `bzip2`<br>`gzip`<br>`deflate`<br>`ZipDeflate`<br>`snappy`<br>`lz4` | CompressionType |
| Nível de compactação | Escolha se a compactação será concluída o mais rapidamente possível ou se o arquivo resultante deve ser compactado de forma ideal. | obrigatório se `compressedType` for especificado. | `Optimal` ou `Fastest` | compressionLevel |
| Vacuum | Especifique o limite de retenção em horas para versões mais antigas da tabela. Um valor de 0 ou menos usa como padrão 30 dias | sim | Integer | vácuo |
| Método Update | Especifique quais operações de atualização são permitidas no Delta Lake. Para métodos que não são inseridos, uma transformação ALTER Row anterior é necessária para marcar linhas. | sim | `true` ou `false` | pode ser excluído <br> Insertable <br> atualizável <br> mesclar |

### <a name="delta-sink-script-example"></a>Exemplo de script de coletor Delta

O script de fluxo de dados associado é:

```
moviesAltered sink(
          input(movieId as integer,
                title as string
            ),
           mapColumn(
                movieId,
                title
            ),
           insertable: true,
           updateable: true,
           deletable: true,
           upsertable: false,
           keys: ['movieId'],
            store: 'local',
           format: 'delta',
           vacuum: 180,
           folderPath: $tempPath + '/delta'
           ) ~> movieDB
```

### <a name="known-limitations"></a>Limitações conhecidas

Ao gravar em um coletor Delta, há uma limitação conhecida em que os números de linhas gravadas não retornarão na saída de monitoramento.

## <a name="next-steps"></a>Próximas etapas

* Crie uma [transformação de origem](data-flow-source.md) no fluxo de dados de mapeamento.
* Crie uma [transformação de coletor](data-flow-sink.md) no fluxo de dados de mapeamento.
* Crie uma [transformação alterar linha](data-flow-alter-row.md) para marcar linhas como inserir, atualizar, Upsert ou excluir.
