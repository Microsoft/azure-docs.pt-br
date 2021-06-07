---
title: Formato do Common Data Model
description: Transformar dados usando o sistema de metadados do Common Data Service
author: kromerm
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: makromer
ms.openlocfilehash: 45f5334ebee3365c17bfa52c8d47ed75b82bdfa1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100387692"
---
# <a name="common-data-model-format-in-azure-data-factory"></a>Formato de modelo de dados comuns no Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

O sistema de metadados do CDM (Common Data Service) possibilita que os dados e seu significado sejam facilmente compartilhados entre aplicativos e processos de negócios. Para saber mais, consulte a visão geral do [Common Data](/common-data-model/) Service.

No Azure Data Factory, os usuários podem transformar dados de entidades CDM no formulário model.jse no manifesto armazenado em [Azure data Lake Store Gen2](connector-azure-data-lake-storage.md) (ADLS Gen2) usando fluxos de dados de mapeamento. Você também pode coletar dados no formato CDM usando referências de entidade CDM que vão parar seus dados no formato CSV ou parquet em pastas particionadas. 

## <a name="mapping-data-flow-properties"></a>Propriedades do fluxo de dados de mapeamento

O Common Data Service está disponível como um [conjunto de dados embutido](data-flow-source.md#inline-datasets) no mapeamento de fluxos de dados como uma fonte e um coletor.

> [!NOTE]
> Ao escrever entidades CDM, você deve ter uma definição de entidade CDM (esquema de metadados) existente já definida para usar como referência. O coletor de fluxo de dados do ADF lerá esse arquivo de entidade CDM e importará o esquema em seu coletor para o mapeamento de campos.

### <a name="source-properties"></a>Propriedades de origem

A tabela abaixo lista as propriedades com suporte por uma fonte CDM. Você pode editar essas propriedades na guia **Opções de origem** .

| Nome | Descrição | Obrigatório | Valores permitidos | Propriedade de script de fluxo de dados |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Formatar | O formato deve ser `cdm` | sim | `cdm` | format |
| Formato de metadados | Onde a referência de entidade para os dados está localizada. Se estiver usando o CDM versão 1,0, escolha manifesto. Se estiver usando uma versão do CDM anterior a 1,0, escolha model.jsem. | Sim | `'manifest'` ou `'model'` | manifestatype |
| Local raiz: contêiner | Nome do contêiner da pasta CDM | sim | String | fileSystem |
| Local raiz: caminho da pasta | Local da pasta raiz da pasta CDM | sim | String | folderPath |
| Arquivo de manifesto: caminho da entidade | Caminho da pasta da entidade na pasta raiz | não | String | entityPath |
| Arquivo de manifesto: nome do manifesto | Nome do arquivo de manifesto. O valor padrão é ' default '  | Não | String | manifestName |
| Filtrar por última modificação | Escolher filtrar arquivos com base na última alteração | não | Timestamp | modifiedAfter <br> modifiedBefore | 
| Serviço vinculado de esquema | O serviço vinculado em que o corpus está localizado | Sim, se estiver usando o manifesto | `'adlsgen2'` ou `'github'` | corpusStore | 
| Contêiner de referência de entidade | O contêiner Corpus está em | Sim, se estiver usando Manifest e corpus no ADLS Gen2 | String | adlsgen2_fileSystem |
| Repositório de referência de entidade | Nome do repositório GitHub | Sim, se estiver usando o manifesto e o corpus no GitHub | String | github_repository |
| Ramificação de referência de entidade | Branch do repositório do GitHub | Sim, se estiver usando o manifesto e o corpus no GitHub | String |  github_branch |
| Pasta Corpus | o local raiz do corpus | Sim, se estiver usando o manifesto | String | corpusPath |
| Entidade Corpus | Caminho para referência de entidade | sim | String | entidade |
| Não permitir nenhum arquivo encontrado | Se for true, um erro não será gerado se nenhum arquivo for encontrado | não | `true` ou `false` | ignoreNoFilesFound |

Ao selecionar "referência de entidade" nas transformações de origem e de coletor, você pode selecionar dentre essas três opções para o local da referência de sua entidade:

* Local usa a entidade definida no arquivo de manifesto que já está sendo usada pelo ADF
* Personalizado pedirá que você aponte para um arquivo de manifesto de entidade diferente do arquivo de manifesto que o ADF está usando
* Standard usará uma referência de entidade da biblioteca padrão de entidades CDM mantidas no ```Github``` .

### <a name="sink-settings"></a>Configurações do coletor

* Aponte para o arquivo de referência de entidade CDM que contém a definição da entidade que você deseja escrever.

![configurações de entidade](media/data-flow/common-data-model-111.png "Referência de entidade")

* Defina o caminho da partição e o formato dos arquivos de saída que você deseja que o ADF use para escrever suas entidades.

![formato de entidade](media/data-flow/common-data-model-222.png "Formato de entidade")

* Defina o local do arquivo de saída e o local e o nome do arquivo de manifesto.

![local CDM](media/data-flow/common-data-model-333.png "Local CDM")


#### <a name="import-schema"></a>Importar esquema

CDM só está disponível como um conjunto de uma embutido e, por padrão, não tem um esquema associado. Para obter metadados de coluna, clique no botão **importar esquema** na guia **projeção** . Isso permitirá que você referencie os nomes de coluna e os tipos de dados especificados pelo corpus. Para importar o esquema, uma [sessão de depuração de fluxo de dados](concepts-data-flow-debug-mode.md) deve estar ativa e você deve ter um arquivo de definição de entidade CDM existente para apontar.

Ao mapear colunas de fluxo de dados para propriedades de entidade na transformação do coletor, clique na guia "mapeamento" e selecione "importar esquema". O ADF lerá a referência de entidade que você apontou em suas opções de coletor, permitindo que você mapeie para o esquema CDM de destino.

![Configurações do coletor CDM](media/data-flow/common-data-model-444.png "Mapeamento de CDM")

> [!NOTE]
>  Ao usar model.jsno tipo de origem que se origina dos fluxos de dataPower BI ou da plataforma de energia, você poderá encontrar erros "caminho corpus é nulo ou vazio" da transformação de origem. Isso provavelmente ocorre devido à formatação de problemas do caminho do local da partição na model.jsno arquivo. Para corrigir isso, siga estas etapas: 

1. Abrir o model.jsno arquivo em um editor de texto
2. Localize as partições. Propriedade Location 
3. Alterar "blob.core.windows.net" para "dfs.core.windows.net"
4. Corrigir qualquer codificação "% 2F" na URL para "/"
5. Se você estiver usando fluxos de dados do ADF, os caracteres especiais no caminho do arquivo de partição deverão ser substituídos por valores alfanuméricos ou mudar para fluxos de dados Synapse

### <a name="cdm-source-data-flow-script-example"></a>Exemplo de script de fluxo de dados de origem CDM

```
source(output(
        ProductSizeId as integer,
        ProductColor as integer,
        CustomerId as string,
        Note as string,
        LastModifiedDate as timestamp
    ),
    allowSchemaDrift: true,
    validateSchema: false,
    entity: 'Product.cdm.json/Product',
    format: 'cdm',
    manifestType: 'manifest',
    manifestName: 'ProductManifest',
    entityPath: 'Product',
    corpusPath: 'Products',
    corpusStore: 'adlsgen2',
    adlsgen2_fileSystem: 'models',
    folderPath: 'ProductData',
    fileSystem: 'data') ~> CDMSource
```

### <a name="sink-properties"></a>Propriedades do coletor

A tabela abaixo lista as propriedades com suporte de um coletor CDM. Você pode editar essas propriedades na guia **configurações** .

| Nome | Descrição | Obrigatório | Valores permitidos | Propriedade de script de fluxo de dados |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Formatar | O formato deve ser `cdm` | sim | `cdm` | format |
| Local raiz: contêiner | Nome do contêiner da pasta CDM | sim | String | fileSystem |
| Local raiz: caminho da pasta | Local da pasta raiz da pasta CDM | sim | String | folderPath |
| Arquivo de manifesto: caminho da entidade | Caminho da pasta da entidade na pasta raiz | não | String | entityPath |
| Arquivo de manifesto: nome do manifesto | Nome do arquivo de manifesto. O valor padrão é ' default ' | Não | String | manifestName |
| Serviço vinculado de esquema | O serviço vinculado em que o corpus está localizado | sim | `'adlsgen2'` ou `'github'` | corpusStore | 
| Contêiner de referência de entidade | O contêiner Corpus está em | Sim, se corpus em ADLS Gen2 | String | adlsgen2_fileSystem |
| Repositório de referência de entidade | Nome do repositório GitHub | Sim, se corpus no GitHub | String | github_repository |
| Ramificação de referência de entidade | Branch do repositório do GitHub | Sim, se corpus no GitHub | String |  github_branch |
| Pasta Corpus | o local raiz do corpus | sim | String | corpusPath |
| Entidade Corpus | Caminho para referência de entidade | sim | String | entidade |
| Caminho da partição | Local onde a partição será gravada | não | String | partitionPath |
| Limpar a pasta | Se a pasta de destino for limpa antes da gravação | não | `true` ou `false` | truncate |
| Tipo de formato | Escolha para especificar o formato parquet | não | `parquet` Se especificado | subformat |
| Delimitador de coluna | Se estiver gravando em DelimitedText, como delimitar colunas | Sim, se estiver gravando em DelimitedText | String | columnDelimiter |
| Primeira linha como cabeçalho | Se estiver usando DelimitedText, se os nomes de coluna são adicionados como um cabeçalho | não | `true` ou `false` | columnNamesAsHeader |

### <a name="cdm-sink-data-flow-script-example"></a>Exemplo de script de fluxo de dados do coletor CDM

O script de fluxo de dados associado é:

```
CDMSource sink(allowSchemaDrift: true,
    validateSchema: false,
    entity: 'Product.cdm.json/Product',
    format: 'cdm',
    entityPath: 'ProductSize',
    manifestName: 'ProductSizeManifest',
    corpusPath: 'Products',
    partitionPath: 'adf',
    folderPath: 'ProductSizeData',
    fileSystem: 'cdm',
    subformat: 'parquet',
    corpusStore: 'adlsgen2',
    adlsgen2_fileSystem: 'models',
    truncate: true,
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> CDMSink

```

## <a name="next-steps"></a>Próximas etapas

Crie uma [transformação de origem](data-flow-source.md) no fluxo de dados de mapeamento.
