---
title: Copiar dados de/para o Armazenamento de Blobs do Azure | Microsoft Docs
description: 'Saiba como copiar dados de blob no Azure Data Factory. Use nossa amostra: como copiar dados bidirecionalmente no Armazenamento de Blobs do Azure e no Banco de Dados SQL do Azure.'
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: bec8160f-5e07-47e4-8ee1-ebb14cfb805d
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/05/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 16d11a707851cdbb3e315c9a6d2fe592a97eca9a
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839580"
---
# <a name="copy-data-to-or-from-azure-blob-storage-using-azure-data-factory"></a>Copie os dados de ou para o Armazenamento de Blobs do Azure usando o Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](data-factory-azure-blob-connector.md)
> * [Versão 2 (versão atual)](../connector-azure-blob-storage.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte [Conector do Armazenamento de Blobs do Azure na V2](../connector-azure-blob-storage.md).


Este artigo explica como usar a Atividade de Cópia no Azure Data Factory para copiar dados de e para o Armazenamento de Blobs do Azure. Ele se baseia no artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md), que apresenta uma visão geral da movimentação de dados com a atividade de cópia.

## <a name="overview"></a>Visão geral
Você pode copiar dados de qualquer armazenamento de dados de origem com suporte para o armazenamento de Blobs do Azure ou do armazenamento de Blobs do Azure para qualquer armazenamento de dados do coletor com suporte. A tabela a seguir fornece uma lista de armazenamentos de dados com suporte como fontes ou coletores na atividade de cópia. Por exemplo, você pode mover dados **de** um banco de dados SQL Server ou um de banco de dados SQL do Azure **para** um armazenamento de blobs do Azure. Além disso, é possível copiar dados **de** um armazenamento de Blobs do Azure **para** um SQL Data Warehouse do Azure ou uma coleção do Azure Cosmos DB.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="supported-scenarios"></a>Cenários com suporte
Você pode copiar dados **do Armazenamento de Blobs do Azure** para os seguintes armazenamentos de dados:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Você pode copiar dados dos seguintes armazenamentos de dados **para um Armazenamento de Blobs do Azure**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!IMPORTANT]
> A Atividade de Cópia dá suporte à cópia de dados de/para contas de Armazenamento do Azure para fins gerais e para o Armazenamento de Blobs Dinâmico/Estático. A atividade dá suporte à **leitura de blobs de bloco, de acréscimo ou de página**, mas dá suporte à **gravação apenas em blobs de blocos**. Não há suporte para armazenamento Premium do Azure como um coletor porque ele é feito por blobs de página.
>
> A atividade de cópia não exclui os dados de origem depois que eles são copiados com êxito para o destino. Se você precisar excluir os dados de origem após uma cópia bem-sucedida, crie uma [atividade personalizada](data-factory-use-custom-activities.md) para excluir os dados e use a atividade no pipeline. Para obter um exemplo, consulte [Excluir exemplo de blob ou pasta no GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/DeleteBlobFileFolderCustomActivity).

## <a name="get-started"></a>Introdução
Você pode criar um pipeline com uma atividade de cópia que mova dados de um armazenamento de Blobs do Azure usando ferramentas/APIs diferentes.

A maneira mais fácil de criar um pipeline é usar o **Assistente de Cópia**. Este artigo tem um [passo a passo](#walkthrough-use-copy-wizard-to-copy-data-tofrom-blob-storage) para criar um pipeline para copiar dados de um local Armazenamento de Blobs do Azure para outro. Para ver um tutorial sobre a criação de um pipeline para copiar dados de um Armazenamento de Blobs do Azure para o Banco de Dados SQL do Azure, consulte [Tutorial: Criar um pipeline usando Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md).

Você também pode usar as ferramentas abaixo para criar um pipeline: **Visual Studio**, **Azure PowerShell**, **modelo do Resource Manager**, **.NET API**, e **API REST**. Confira o [Tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo sobre a criação de um pipeline com uma atividade de cópia.

Ao usar as ferramentas ou APIs, você executa as seguintes etapas para criar um pipeline que move dados de um armazenamento de dados de origem para um armazenamento de dados de coletor:

1. Criar uma **data factory**. Um data factory pode conter um ou mais pipelines.
2. Criar **serviços vinculados** para vincular repositórios de dados de entrada e saída ao seu data factory. Por exemplo, se você está copiando dados de um Armazenamento de Blobs do Azure para um Banco de Dados SQL do Azure, crie dois serviços vinculados para vincular sua Conta de Armazenamento do Azure e o Banco de Dados SQL do Azure ao data factory. Para propriedades do serviço vinculado que são específicas do Armazenamento de Blobs do Azure, consulte a seção de [propriedades do serviço vinculado](#linked-service-properties).
2. Criar **conjuntos de dados** para representar dados de entrada e saída para a operação de cópia. No exemplo mencionado na última etapa, você cria um conjunto de dados para especificar o contêiner de blobs e a pasta que contém os dados de entrada. Em seguida, você cria outro conjunto de dados para especificar a tabela SQL no Banco de Dados SQL do Azure que contém os dados copiados do Armazenamento de Blobs. Para propriedades do conjunto de dados que são específicas do Armazenamento de Blobs do Azure, consulte a seção de [propriedades do conjunto de dados](#dataset-properties).
3. Criar um **pipeline** com uma atividade de cópia que usa um conjunto de dados como uma entrada e um conjunto de dados como uma saída. No exemplo mencionado anteriormente, você usa BlobSource como fonte e SqlSink como coletor para a atividade de cópia. De modo similar, se estiver copiando do Banco de Dados SQL do Azure para o Armazenamento de Blobs do Azure, você usará SqlSource e BlobSink na atividade de cópia. Para propriedades da atividade de cópia que são específicas do Armazenamento de Blobs do Azure, consulte a seção de [propriedades da atividade de cópia](#copy-activity-properties). Para obter detalhes sobre como usar um armazenamento de dados como uma origem ou um coletor, clique no link na seção anterior para o armazenamento de dados.

Ao usar o assistente, as definições de JSON para essas entidades do Data Factory (serviços vinculados, conjuntos de dados e o pipeline) são automaticamente criadas para você. Ao usar ferramentas/APIs (exceto a API .NET), você define essas entidades do Data Factory usando o formato JSON.  Para obter exemplos com definições de JSON das entidades do Data Factory usadas para copiar dados bidirecionalmente em um Armazenamento de Blobs do Azure, confira a seção [Exemplos de JSON](#json-examples-for-copying-data-to-and-from-blob-storage  ) neste artigo.

As seções a seguir fornecem detalhes sobre as propriedades JSON que são usadas para definir entidades do Data Factory específicas para o Armazenamento de Blobs do Azure.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado
Existem dois tipos de serviço vinculado que você pode usar para vincular um Armazenamento do Azure a um Azure Data Factory. Elas são: o serviços vinculado **AzureStorage** e **AzureStorageSas**. O serviço vinculado do Armazenamento do Azure fornece o data factory com acesso global ao Armazenamento do Azure. Já o serviço vinculado SAS (Assinatura de Acesso Compartilhado) do Armazenamento do Azure fornece o data factory com acesso restrito/associado ao tempo ao Armazenamento do Azure. Não há outras diferenças entre esses dois serviços vinculados. Escolha o serviço vinculado que atenda às suas necessidades. As seções a seguir fornecem mais detalhes sobre esses dois serviços vinculados.

[!INCLUDE [data-factory-azure-storage-linked-services](../../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para especificar um conjunto de dados de modo a representar dados de entrada ou saída em um Armazenamento de Blobs do Azure, defina a propriedade de tipo do conjunto de dados como: **AzureBlob**. Defina a propriedade **linkedServiceName** do conjunto de dados para o nome do Armazenamento do Azure ou do serviço vinculado de SAS do Armazenamento do Azure.  As propriedades de tipo do conjunto de dados especificam o **contêiner de blob** e a **pasta** no Armazenamento de Blobs.

Para obter uma lista completa das seções e propriedades JSON disponíveis para definir conjuntos de dados, consulte o artigo [Criando conjuntos de dados](data-factory-create-datasets.md). As seções como structure, availability e policy de um conjunto de dados JSON são similares para todos os tipos de conjunto de dados (SQL Azure, Blob do Azure, Tabela do Azure etc.).

O Data Factory dá suporte aos seguintes valores de tipo baseados em .NET compatíveis com CLS, de modo a fornecer informações de tipo em "estrutura" para esquema de fontes de dados de leitura, como blob do Azure: Int16, Int32, Int64, Single, Double, Decimal, Byte[], Bool, String, Guid, Datetime, Datetimeoffset, Timespan. O Data Factory executa conversões de tipo automaticamente ao mover dados de um armazenamento de dados de origem para um armazenamento de dados do coletor.

A seção **typeProperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização, o formato etc. dos dados no armazenamento de dados. A seção typeProperties para o conjunto de dados do tipo **AzureBlob** tem as seguintes propriedades:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| folderPath |Caminho para o contêiner e a pasta no armazenamento de blob. Exemplo: myblobcontainer\myblobfolder\ |Sim |
| fileName |O nome do blob. fileName é opcional e diferencia maiúsculas de minúsculas.<br/><br/>Caso você especifique um nome de arquivo, a atividade (incluindo Cópia) funcionará no Blob específico.<br/><br/>Quando fileName não for especificado, a Cópia incluirá todos os Blobs do folderPath para o conjunto de dados de entrada.<br/><br/>Quando **fileName** não for especificado para um conjunto de dados de saída e **preserveHierarchy** não for especificado em um coletor de atividade, o nome do arquivo gerado seria a seguir este formato: `Data.<Guid>.txt` (para exemplo:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Não |
| partitionedBy |partitionedBy é uma propriedade opcional. Você pode usá-lo para especificar um folderPath dinâmico e o nome de arquivo para dados de série temporal. Por exemplo, folderPath pode ser parametrizado para cada hora dos dados. Confira a seção [Usando a propriedade partitionedBy](#using-partitionedby-property) para obter detalhes e exemplos. |Não |
| format | Há suporte para os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Defina a propriedade **type** sob formato como um desses valores. Para saber mais, veja as seções [Formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [Formato Json](data-factory-supported-file-and-compression-formats.md#json-format), [Formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format), e [Formato Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format). <br><br> Se você quiser **copiar arquivos no estado em que se encontram** entre repositórios baseados em arquivo (cópia binária), ignore a seção de formato nas duas definições de conjunto de dados de entrada e de saída. |Não |
| compression | Especifique o tipo e o nível de compactação para os dados. Tipos compatíveis são: **GZip**, **Deflate**, **BZip2** e **ZipDeflate**. Níveis compatíveis são: **Ideal** e **Mais Rápido**. Para saber mais, confira [File and compression formats in Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support) (Formatos de arquivo e de compactação no Azure Data Factory). |Não |

### <a name="using-partitionedby-property"></a>Usando a propriedade partitionedBy
Conforme mencionado na seção anterior, você pode especificar um nome de arquivo e um folderPath dinâmico para dados de série temporal com a propriedade **partitionedBy**, [funções do Data Factory e as variáveis do sistema](data-factory-functions-variables.md).

Para saber mais sobre conjuntos de dados de série temporal, agendamento e fatias, veja os artigos [Criando conjuntos de dados](data-factory-create-datasets.md) e [Agendamento e execução](data-factory-scheduling-and-execution.md).

#### <a name="sample-1"></a>Exemplo 1

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

Nesse exemplo, {Slice} é substituído pelo valor da variável de sistema SliceStart do Data Factory no formato (AAAAMMDDHH) especificado. O SliceStart refere-se à hora de início da fatia. O folderPath é diferente para cada fatia. Por exemplo: wikidatagateway/wikisampledataout/2014100103 ou wikidatagateway/wikisampledataout/2014100104

#### <a name="sample-2"></a>Exemplo 2

```json
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
[
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```

Neste exemplo, ano, mês, dia e hora do SliceStart são extraídos em variáveis separadas que são usadas pelas propriedades folderPath e fileName.

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia
Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Criando pipelines](data-factory-create-pipelines.md). As propriedades, como nome, descrição, conjuntos de dados de entrada e saída, e políticas, estão disponíveis para todos os tipos de atividade. Por outro lado, as propriedades disponíveis na seção **typeProperties** da atividade variam de acordo com cada tipo de atividade. Para a atividade de cópia, elas variam de acordo com os tipos de fonte e coletor. Se você estiver movendo dados de um armazenamento de Blobs do Azure, defina o tipo de origem na atividade de cópia como **BlobSource**. Da mesma forma, se você estiver movendo dados para um armazenamento de Blobs do Azure, defina o tipo de coletor na atividade de cópia como **BlobSink**. Esta seção fornece uma lista das propriedades compatíveis com BlobSource e BlobSink.

O **BlobSource** dá suporte às seguintes propriedades na seção **typeProperties**:

| Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| recursive |Indica se os dados são lidos recursivamente por meio de subpastas ou somente da pasta especificada. |True (valor padrão), False |Não |

O **BlobSink** dá suporte às seguintes propriedades na seção **typeProperties**:

| Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| copyBehavior |Define o comportamento de cópia quando a origem é BlobSource ou FileSystem. |<b>PreserveHierarchy:</b> preserva a hierarquia de arquivos na pasta de destino. O caminho relativo do arquivo de origem para a pasta de origem é idêntico ao caminho relativo do arquivo de destino para a pasta de destino.<br/><br/><b>FlattenHierarchy:</b> todos os arquivos da pasta de origem estão no primeiro nível da pasta de destino. Os arquivos de destino têm o nome gerado automaticamente. <br/><br/><b>MergeFiles:</b> mescla todos os arquivos da pasta de origem em um arquivo. Se o nome do arquivo/blob for especificado, o nome do arquivo mesclado será o nome especificado; caso contrário, será o nome de arquivo gerado automaticamente. |Não |

**BlobSource** também oferece suporte a essas duas propriedades para compatibilidade com versões anteriores.

* **treatEmptyAsNull**: especifica se a cadeia de caracteres nula ou vazia deve ser tratada como nula.
* **skipHeaderLineCount** - Especifica quantas linhas precisam ser ignoradas. É aplicável somente quando o conjunto de dados de entrada usa TextFormat.

Da mesma forma, **BlobSink** oferece suporte à seguinte propriedade para compatibilidade com versões anteriores.

* **blobWriterAddHeader**: especifica se um cabeçalho de definições de coluna deve ser adicionado durante a gravação em um conjunto de dados de saída.

Agora, os conjuntos de dados dão suporte às seguintes propriedades que implementam a mesma funcionalidade: **treatEmptyAsNull**, **skipLineCount**, **firstRowAsHeader**.

A tabela a seguir fornece orientação sobre como usar as novas propriedades do conjunto de dados em vez de propriedades de fonte/coletor de blob.

| Propriedade de Atividade de Cópia | Propriedade de Conjunto de Dados |
|:--- |:--- |
| skipHeaderLineCount em BlobSource |skipLineCount e firstRowAsHeader. As linhas são ignoradas primeiro e, em seguida, a primeira linha é lida como um cabeçalho. |
| treatEmptyAsNull em BlobSource |treatEmptyAsNull no conjunto de dados de entrada |
| blobWriterAddHeader em BlobSink |firstRowAsHeader no conjunto de dados de saída |

Confira a seção [Especificar TextFormat](data-factory-supported-file-and-compression-formats.md#text-format) para obter informações detalhadas sobre essas propriedades.

### <a name="recursive-and-copybehavior-examples"></a>exemplos de recursive e copyBehavior
Esta seção descreve o comportamento resultante da operação de cópia para diferentes combinações de valores recursive e copyBehavior.

| recursive | copyBehavior | Comportamento resultante |
| --- | --- | --- |
| true |preserveHierarchy |Para uma pasta de origem Pasta1 com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5<br/><br/>a pasta de destino Pasta1 é criada com a mesma estrutura da origem<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5. |
| true |flattenHierarchy |Para uma pasta de origem Pasta1 com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5<br/><br/>a Pasta1 de destino é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome gerado automaticamente para o Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo5 |
| true |mergeFiles |Para uma pasta de origem Pasta1 com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5<br/><br/>a Pasta1 de destino é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Os conteúdos dos Arquivo1 + Arquivo2 + Arquivo3 + Arquivo4 + Arquivo5 são mesclados em um único arquivo com um nome de arquivo gerado automaticamente |
| false |preserveHierarchy |Para uma pasta de origem Pasta1 com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5<br/><br/>a pasta de destino Pasta1 é criada com a seguinte estrutura<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/><br/><br/>Subpasta1 com Arquivo3, Arquivo4 e Arquivo5 não são selecionados. |
| false |flattenHierarchy |Para uma pasta de origem Pasta1 com a seguinte estrutura:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5<br/><br/>a pasta de destino Pasta1 é criada com a seguinte estrutura<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome gerado automaticamente para o Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo2<br/><br/><br/>Subpasta1 com Arquivo3, Arquivo4 e Arquivo5 não são selecionados. |
| false |mergeFiles |Para uma pasta de origem Pasta1 com a seguinte estrutura:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5<br/><br/>a pasta de destino Pasta1 é criada com a seguinte estrutura<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Os conteúdos dos Arquivo1 + Arquivo2 são mesclados em um único arquivo com um nome de arquivo gerado automaticamente. Nome gerado automaticamente para o Arquivo1<br/><br/>Subpasta1 com Arquivo3, Arquivo4 e Arquivo5 não são selecionados. |

## <a name="walkthrough-use-copy-wizard-to-copy-data-tofrom-blob-storage"></a>Passo a passo: Usar o Assistente de Cópia para copiar dados bidirecionalmente no Armazenamento de Blobs
Vamos examinar como copiar dados rapidamente de e para um armazenamento de blobs do Azure. Neste passo a passo, os armazenamentos de dados de origem e de destino do tipo: Armazenamento de Blobs do Azure. O pipeline neste passo a passo copia os dados de uma pasta para outra no mesmo contêiner de blobs. Este passo a passo é intencionalmente simples para mostrar configurações ou propriedades ao usar o Armazenamento de Blobs como uma fonte ou um coletor.

### <a name="prerequisites"></a>Pré-requisitos
1. Crie uma **Conta de Armazenamento do Azure** de uso geral caso não tenha uma. Neste passo a passo, o armazenamento de blobs é usado como um armazenamento de dados de **origem** e de **destino**. Se você não tiver uma conta de armazenamento do Azure, veja o artigo [Criar uma conta de armazenamento](../../storage/common/storage-quickstart-create-account.md) para conhecer as etapas para criar um.
2. Crie um contêiner de blobs chamado **adfblobconnector** na conta de armazenamento.
4. Crie uma pasta chamada **input** no contêiner **adfblobconnector**.
5. Crie um arquivo chamado **emp.txt** com o seguinte conteúdo e carregue-o para a pasta **input** usando ferramentas como o [Gerenciador de Armazenamento do Azure](https://azurestorageexplorer.codeplex.com/)
    ```json
    John, Doe
    Jane, Doe
    ```

### <a name="create-the-data-factory"></a>Criar o data factory
1. Entre no [Portal do Azure](https://portal.azure.com).
2. Clique em **Criar um recurso** no canto superior esquerdo, clique em **Inteligência + análise** e clique em **Data Factory**.
3. No painel **Novo data factory**:  
    1. Insira **ADFBlobConnectorDF** para o **nome**. O nome da data factory do Azure deve ser globalmente exclusivo. Se você receber o erro `*Data factory name “ADFBlobConnectorDF” is not available`, altere o nome do data factory (por exemplo, yournameADFBlobConnectorDF) e tente criá-lo novamente. Veja o tópico [Data Factory - regras de nomenclatura](data-factory-naming-rules.md) para ver as regras de nomenclatura para artefatos do Data Factory.
    2. Selecione sua **assinatura**do Azure.
    3. Para Grupo de Recursos, selecione **Usar existente** para selecionar um grupo de recursos existente (ou) selecione **Criar novo** para inserir um nome para um grupo de recursos.
    4. Selecione um **local** para o data factory.
    5. Marque a caixa de seleção **Fixar no painel** na parte inferior da folha.
    6. Clique em **Criar**.
3. Depois que a criação for concluída, você verá a folha **Data Factory**, conforme mostrado na seguinte imagem:  ![Página inicial do Data Factory](./media/data-factory-azure-blob-connector/data-factory-home-page.png)

### <a name="copy-wizard"></a>Assistente de Cópia
1. Na home page do Data Factory, clique no bloco **Copiar dados** para iniciar o **Assistente de Cópia de Dados** em uma guia separada.  
    
    > [!NOTE]
    > Se você vir que o navegador da Web está bloqueado em "Autorizando...", desabilite/desmarque a configuração **Bloquear cookies de terceiros e dados de site** (ou) mantenha-a habilitada, crie uma exceção para **login.microsoftonline.com** e tente iniciar o assistente novamente.
2. Na página **Propriedades** :
    1. Insira **CopyPipeline** para **Nome da tarefa**. O nome da tarefa é o nome do pipeline no data factory.
    2. Insira uma **descrição** para a tarefa (opcional).
    3. Para **Cadência de tarefa ou Agendamento de tarefa**, mantenha a opção **Executar regularmente de acordo com o agendamento**. Se você desejar executar essa tarefa somente uma vez, em vez de executá-la repetidamente de acordo com um agendamento, selecione **Executar uma vez agora**. Se você selecionar a opção **Executar uma vez agora**, um [pipeline avulso](data-factory-create-pipelines.md#onetime-pipeline) será criado.
    4. Mantenha as configurações de **Padrão recorrente**. Essa tarefa é executada diariamente entre as horas de início e término especificadas na próxima etapa.
    5. Altere a **Data/hora de início** para **21/04/2017**.
    6. Altere a **Data/hora de término** para **25/04/2017**. Talvez você queira digitar a data em vez de procurá-la no calendário.
    8. Clique em **Avançar**.
        ![Ferramenta de Cópia – Página Propriedades](./media/data-factory-azure-blob-connector/copy-tool-properties-page.png)
3. Na página **Repositório de dados de origem**, clique no bloco **Armazenamento de Blobs do Azure**. Use essa página para especificar o repositório de dados de origem para a tarefa de cópia. Você pode usar um serviço de repositório de dados vinculado existente ou especificar um novo repositório de dados. Para usar um serviço vinculado existente, selecione **DE SERVIÇOS VINCULADOS EXISTENTES** e selecione o serviço vinculado correto.
    ![Ferramenta de Cópia – Página do armazenamento de dados de origem](./media/data-factory-azure-blob-connector/copy-tool-source-data-store-page.png)
4. Na página **Especificar a conta de armazenamento de Blobs do Azure** :
    1. Mantenha o nome gerado automaticamente para o **Nome da conexão**. O nome da conexão é o nome do serviço vinculado do tipo: Armazenamento do Microsoft Azure.
    2. Confirme se a opção **De assinaturas do Azure** foi selecionada em **Método de seleção de conta**.
    3. Selecione sua assinatura do Azure ou mantenha **Selecionar tudo** para **Assinatura do Azure**.
    4. Selecione uma **Conta de armazenamento do Azure** na lista de contas de armazenamento do Azure disponíveis na assinatura selecionada. Você também pode optar por inserir as configurações da conta de armazenamento manualmente selecionando a opção **Inserir manualmente** para o **Método de seleção de conta**.
    5. Clique em **Avançar**.  
        ![Ferramenta de Cópia – Especificar a conta do armazenamento de Blobs do Azure](./media/data-factory-azure-blob-connector/copy-tool-specify-azure-blob-storage-account.png)
5. Na página **Escolher o arquivo de entrada ou a pasta** :
    1. Clique duas vezes em **adfblobcontainer**.
    2. Selecione **input** e clique em **Escolher**. Neste passo a passo, você seleciona a pasta input. Você também pode selecionar o arquivo emp.txt na pasta.
        ![Ferramenta de Cópia – Escolher a pasta ou o arquivo de entrada](./media/data-factory-azure-blob-connector/copy-tool-choose-input-file-or-folder.png)
6. Na página **Escolher o arquivo ou a pasta de entrada**:
    1. Confirme se **a pasta ou o arquivo** está definido como **adfblobconnector/input**. Se os arquivos estiverem em subpastas, por exemplo, 01/04/2017, 02/04/2017 e assim por diante, insira adfblobconnector/input/{year}/{month}/{day} para o arquivo ou a pasta. Ao pressionar TAB fora da caixa de texto, você verá três listas suspensas para selecionar formatos de ano (yyyy), mês (MM) e dia (dd).
    2. Não defina a opção **Copiar arquivo recursivamente**. Selecione essa opção para percorrer as pastas recursivamente para que os arquivos sejam copiados para o destino.
    3. Não defina a opção **Cópia binária**. Selecione essa opção para executar uma cópia binária do arquivo de origem para o destino. Não selecione essa opção neste passo a passo, de forma que você possa ver mais opções na próxima página.
    4. Confirme se o **Tipo de compactação** está definido como **Nenhum**. Selecione um valor para essa opção se os arquivos de origem estiverem compactados em um dos formatos com suporte.
    5. Clique em **Avançar**.
    ![Ferramenta de Cópia – Escolher a pasta ou o arquivo de entrada](./media/data-factory-azure-blob-connector/chose-input-file-folder.png)
7. Na página **Configurações de formato de arquivo**, você vê os delimitadores e o esquema é detectado automaticamente pelo assistente na análise do arquivo.
    1. Confirme as opções a seguir:  
        a. O **formato de arquivo** está definido como **Formato de texto**. É possível ver todos os formatos com suporte na lista suspensa. Por exemplo:  JSON, Avro, ORC, Parquet.
       b. O **delimitador de coluna** está definido como `Comma (,)`. É possível ver os outros delimitadores de coluna com suporte no Data Factory na lista suspensa. Você também pode especificar um delimitador personalizado.
       c. O **delimitador de coluna** está definido como `Carriage Return + Line feed (\r\n)`. É possível ver os outros delimitadores de linha com suporte no Data Factory na lista suspensa. Você também pode especificar um delimitador personalizado.
       d. A opção **ignorar contagem de linhas** está definida como **0**. Se você desejar que algumas linhas sejam ignoradas na parte superior do arquivo, insira o número aqui.
       e. A opção **primeira linha de dados contém nomes de coluna** não está definida. Se os arquivos de origem contiverem nomes de coluna na primeira linha, selecione essa opção.
       f. A opção **tratar o valor da coluna vazia como nulo** está definida.
    2. Expanda **Configurações avançadas** para ver a opção avançada disponível.
    3. Na parte inferior da página, consulte a **visualização** de dados do arquivo emp.txt.
    4. Clique na guia **ESQUEMA** na parte inferior para ver o esquema inferido pelo assistente de cópia observando os dados no arquivo de origem.
    5. Clique em **Avançar** depois de revisar os delimitadores e visualizar os dados.
    ![Ferramenta de Cópia – Configurações de formato de arquivo](./media/data-factory-azure-blob-connector/copy-tool-file-format-settings.png)
8. Na **página Armazenamento de dados de destino**, selecione **Armazenamento de Blobs do Azure** e clique em **Avançar**. Você está usando o Armazenamento de Blobs do Azure como os armazenamentos de dados de origem e de destino neste passo a passo.  
    ![Ferramenta de Cópia – selecionar o armazenamento de dados de destino](media/data-factory-azure-blob-connector/select-destination-data-store.png)
9. Na página **Especificar a conta de armazenamento de Blobs do Azure**:  
    1. Insira **AzureStorageLinkedService** no campo **Nome da conexão**.
    2. Confirme se a opção **De assinaturas do Azure** foi selecionada em **Método de seleção de conta**.
    3. Selecione sua **assinatura**do Azure.
    4. Selecione sua conta de armazenamento do Azure.
    5. Clique em **Avançar**.
10. Na página **Escolher o arquivo ou a pasta de saída**:  
    1. especifique **Caminho da pasta** como **adfblobconnector/output/{year}/{month}/{day}** . Insira **GUIA**.
    1. No **ano**, selecione **yyyy**.
    1. No **mês**, confirme se ele está definido como **MM**.
    1. No **dia**, confirme se ele está definido como **dd**.
    1. Confirme se o **tipo de compactação** está definido como **Nenhum**.
    1. Confirme se o **comportamento de cópia** está definido como **Mesclar arquivos**. Se o arquivo de saída com o mesmo nome já existir, o novo conteúdo será adicionado ao mesmo arquivo no final.
    1. Clique em **Avançar**.
       ![Ferramenta de Cópia – Escolher o arquivo ou a pasta de saída](media/data-factory-azure-blob-connector/choose-the-output-file-or-folder.png)
11. Na página **Configurações de formato de arquivo**, examine as configurações e clique em **Avançar**. Uma das opções adicionais aqui é adicionar um cabeçalho ao arquivo de saída. Se você selecionar essa opção, uma linha de cabeçalho será adicionada com nomes das colunas do esquema da fonte. Você pode renomear os nomes de coluna padrão ao exibir o esquema da fonte. Por exemplo, você poderá alterar a primeira coluna para Nome e a segunda coluna para Sobrenome. Em seguida, o arquivo de saída será gerado com um cabeçalho com esses nomes como nomes de coluna.
    ![Ferramenta de Cópia – Configurações de formato de arquivo para o destino](media/data-factory-azure-blob-connector/file-format-destination.png)
12. Na página **Configurações de desempenho**, confirme se **unidades de nuvem** e **cópias paralelas** estão definidas como **Automático** e clique em Avançar. Para obter detalhes sobre essas configurações, consulte [Guia de desempenho e ajuste da atividade de cópia](data-factory-copy-activity-performance.md#parallel-copy).
    ![Ferramenta de Cópia – Configurações de desempenho](media/data-factory-azure-blob-connector/copy-performance-settings.png)
14. Na página **Resumo**, examine todas as configurações (propriedades da tarefa, configurações da origem e do destino e configurações de cópia) e clique em **Avançar**.
    ![Ferramenta de Cópia – Página Resumo](media/data-factory-azure-blob-connector/copy-tool-summary-page.png)
15. Examine as informações na página **Resumo** e clique em **Concluir**. Esse assistente cria dois serviços vinculados, dois conjuntos de dados (entrada e saída) e um pipeline no data factory (de onde você iniciou o Assistente de Cópia).
    ![Ferramenta de Cópia – Página Implantação](media/data-factory-azure-blob-connector/copy-tool-deployment-page.png)

### <a name="monitor-the-pipeline-copy-task"></a>Monitorar o pipeline (tarefa de cópia)

1. Clique no link `Click here to monitor copy pipeline` na página **Implantação**.
2. Você deverá ver o **aplicativo Monitorar e Gerenciar** em uma guia separada.  ![Aplicativo Monitorar e Gerenciar](media/data-factory-azure-blob-connector/monitor-manage-app.png)
3. Altere a hora de **início** na parte superior para `04/19/2017` e a hora de **término** para `04/27/2017` e, em seguida, clique em **Aplicar**.
4. Você deverá ver cinco janelas de atividades na lista **JANELAS DE ATIVIDADES**. As horas de **WindowStart** devem abranger todos os dias desde a hora de início até a hora de término do pipeline.
5. Clique no botão **Atualizar** da lista **JANELAS DE ATIVIDADES** algumas vezes até ver que o status de todas as janelas de atividades está definido como Pronto.
6. Agora, verifique se os arquivos de saída são gerados na pasta de saída do contêiner adfblobconnector. Você deverá ver a seguinte estrutura de pastas na pasta de saída:
    ```
    2017/04/21
    2017/04/22
    2017/04/23
    2017/04/24
    2017/04/25
    ```
   Para obter informações detalhadas sobre como monitorar e gerenciar data factories, consulte o artigo [Monitorar e gerenciar o pipeline do Data Factory](data-factory-monitor-manage-app.md).

### <a name="data-factory-entities"></a>Entidades de Data Factory
Agora, volte para a guia com a home page do Data Factory. Observe que agora há dois serviços vinculados, dois conjuntos de dados e um pipeline no data factory.

![Home page do Data Factory com entidades](media/data-factory-azure-blob-connector/data-factory-home-page-with-numbers.png)

Clique em **Criar e implantar** para iniciar o Editor do Data Factory.

![Editor Data Factory](media/data-factory-azure-blob-connector/data-factory-editor.png)

Você deverá ver as seguintes entidades do Data Factory no data factory:

- Dois serviços vinculados. Um para a origem e o outro para o destino. Ambos os serviços vinculados consultam a mesma conta de Armazenamento do Azure neste passo a passo.
- Dois conjuntos de dados. Um conjunto de dados de entrada e um conjunto de dados de saída. Neste passo a passo, ambos usam o mesmo contêiner de blobs, mas consultam pastas diferentes (entrada e saída).
- Um pipeline. O pipeline contém uma atividade de cópia que usa uma fonte de blob e um coletor de blob para copiar dados de uma localização de blob do Azure para outra.

As próximas seções fornecem mais informações sobre essas entidades.

#### <a name="linked-services"></a>Serviços vinculados
Você deverá ver dois serviços vinculados. Um para a origem e o outro para o destino. Neste passo a passo, ambas as definições têm a mesma aparência, exceto os nomes. O **tipo** do serviço vinculado está definido como **AzureStorage**. A propriedade mais importante da definição de serviço vinculado é a **connectionString**, que é usada pelo Data Factory para se conectar à sua conta de Armazenamento do Azure em tempo de execução. Ignore a propriedade hubName na definição.

##### <a name="source-blob-storage-linked-service"></a>Serviço vinculado do armazenamento de blobs do Azure
```json
{
    "name": "Source-BlobStorage-z4y",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=mystorageaccount;AccountKey=**********"
        }
    }
}
```

##### <a name="destination-blob-storage-linked-service"></a>Serviço vinculado do armazenamento de blobs de destino

```json
{
    "name": "Destination-BlobStorage-z4y",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=mystorageaccount;AccountKey=**********"
        }
    }
}
```

Para obter mais informações sobre o serviço vinculado do Armazenamento do Azure, consulte a seção [Propriedades do serviço vinculado](#linked-service-properties).

#### <a name="datasets"></a>Conjunto de dados
Há dois conjuntos de dados: um conjunto de dados de entrada e um conjunto de dados de saída. O tipo do conjunto de dados está definido como **AzureBlob** para ambos.

O conjunto de dados de entrada aponta para a pasta **input** do contêiner de blobs **adfblobconnector**. A propriedade **external** está definida como **true** para este conjunto de dados, pois os dados não são gerados pelo pipeline com a atividade de cópia que usa esse conjunto de dados como entrada.

O conjunto de dados de saída aponta para a pasta **output** do mesmo contêiner de blobs. O conjunto de dados de saída também usa o ano, mês e dia da variável de sistema **SliceStart** para avaliar dinamicamente o caminho do arquivo de saída. Para obter uma lista de funções e variáveis de sistema com suporte no Data Factory, consulte [Funções e variáveis de sistema do Data Factory](data-factory-functions-variables.md). A propriedade **external** está definida como **false** (valor padrão), pois este conjunto de dados é gerado pelo pipeline.

Para obter mais informações sobre as propriedades com suporte no conjunto de dados de Blobs do Azure, consulte a seção [Propriedades do conjunto de dados](#dataset-properties).

##### <a name="input-dataset"></a>Conjunto de dados de entrada

```json
{
    "name": "InputDataset-z4y",
    "properties": {
        "structure": [
            { "name": "Prop_0", "type": "String" },
            { "name": "Prop_1", "type": "String" }
        ],
        "type": "AzureBlob",
        "linkedServiceName": "Source-BlobStorage-z4y",
        "typeProperties": {
            "folderPath": "adfblobconnector/input/",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```

##### <a name="output-dataset"></a>Conjunto de dados de saída

```json
{
    "name": "OutputDataset-z4y",
    "properties": {
        "structure": [
            { "name": "Prop_0", "type": "String" },
            { "name": "Prop_1", "type": "String" }
        ],
        "type": "AzureBlob",
        "linkedServiceName": "Destination-BlobStorage-z4y",
        "typeProperties": {
            "folderPath": "adfblobconnector/output/{year}/{month}/{day}",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            },
            "partitionedBy": [
                { "name": "year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
                { "name": "month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
                { "name": "day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }
            ]
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        },
        "external": false,
        "policy": {}
    }
}
```

#### <a name="pipeline"></a>Pipeline
O pipeline tem apenas uma atividade. O **tipo** da atividade está definido como **Cópia**. Nas propriedades de tipo da atividade, há duas seções, uma para a fonte e a outra para o coletor. O tipo de origem está definido como **BlobSource**, pois a atividade está copiando dados de um armazenamento de blobs. O tipo de coletor está definido como **BlobSink**, pois a atividade está copiando dados para um armazenamento de blobs. A atividade de cópia usa InputDataset-z4y como a entrada e OutputDataset-z4y como a saída.

Para obter mais informações sobre as propriedades com suporte na BlobSource e no BlobSink, consulte a seção [Propriedades da atividade de cópia](#copy-activity-properties).

```json
{
    "name": "CopyPipeline",
    "properties": {
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource",
                        "recursive": false
                    },
                    "sink": {
                        "type": "BlobSink",
                        "copyBehavior": "MergeFiles",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataset-z4y"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataset-z4y"
                    }
                ],
                "policy": {
                    "timeout": "1.00:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "style": "StartOfInterval",
                    "retry": 3,
                    "longRetry": 0,
                    "longRetryInterval": "00:00:00"
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "Activity-0-Blob path_ adfblobconnector_input_->OutputDataset-z4y"
            }
        ],
        "start": "2017-04-21T22:34:00Z",
        "end": "2017-04-25T05:00:00Z",
        "isPaused": false,
        "pipelineMode": "Scheduled"
    }
}
```

## <a name="json-examples-for-copying-data-to-and-from-blob-storage"></a>Exemplos JSON para cópia de dados de e para o Armazenamento de Blobs
Os exemplos a seguir fornecem as definições de JSON de exemplo que você pode usar para criar um pipeline usando o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [do Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Tais exemplos mostram como copiar dados de/para o Armazenamento de Blobs do Azure e o Banco de Dados SQL do Azure. No entanto, os dados podem ser copiados **diretamente** de qualquer uma das fontes para qualquer um dos coletores declarados [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a Atividade de Cópia no Azure Data Factory.

### <a name="json-example-copy-data-from-blob-storage-to-sql-database"></a>Exemplo de JSON: Copiar dados do Armazenamento de Blobs para o Banco de Dados SQL
O exemplo a seguir mostra:

1. Um serviço vinculado do tipo [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties).
2. Um serviço vinculado do tipo [AzureStorage](#linked-service-properties).
3. Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [AzureBlob](#dataset-properties).
4. Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
5. Um [pipeline](data-factory-create-pipelines.md) com Atividade de cópia que usa [BlobSource](#copy-activity-properties) e [SqlSink](data-factory-azure-sql-connector.md#copy-activity-properties).

O exemplo copia os dados de série temporal de um blob do Azure para uma tabela SQL do Azure a cada hora. As propriedades JSON usadas nesses exemplos são descritas nas seções após os exemplos.

**Serviço vinculado do SQL Azure:**

```json
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Serviço vinculado de armazenamento do Azure:**

```json
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
O Azure Data Factory oferece suporte a dois tipos de serviços vinculados de Armazenamento do Azure: **AzureStorage** e **AzureStorageSas**. Para o primeiro, você especifica a cadeia de conexão que inclui a chave de conta, e para o mais recente, você especifica o URI de SAS (Assinatura de Acesso Compartilhado). Confira a seção [Serviços vinculados](#linked-service-properties) para obter detalhes.

**Conjunto de dados de entrada de Blob do Azure:**

Os dados são coletados de um novo blob a cada hora (frequência: horas, intervalo: 1). O caminho de pasta e nome de arquivo para o blob são avaliados dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa parte da hora de início do dia, mês e ano e o nome de arquivo usa a parte da hora de início. A configuração "external": "true" informa o Data Factory que a tabela é externa ao Data Factory e não é produzida por uma atividade no Data Factory.

```json
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "HH" } }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": "\n"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```
**Conjunto de dados de saída do SQL Azure:**

Os dados de cópia do exemplo em uma tabela chamada "MyTable" em um banco de dados SQL do Azure. Crie a tabela no banco de dados SQL do Azure com o mesmo número de colunas que você espera que o arquivo CSV de Blob contenha. Novas linhas são adicionadas à tabela a cada hora.

```json
{
  "name": "AzureSqlOutput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**Uma atividade de cópia em um pipeline com origem de Blob e coletor SQL:**

O pipeline contém uma Atividade de Cópia que está configurada para usar os conjuntos de dados de entrada e saída e é agendada para ser executada a cada hora. Na definição de JSON do pipeline, o tipo de **fonte** está definido como **BlobSource** e o tipo de **coletor** está definido como **SqlSink**.

```json
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[
      {
        "name": "AzureBlobtoSQL",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink"
          }
        },
        "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```
### <a name="json-example-copy-data-from-azure-sql-to-azure-blob"></a>Exemplo de JSON: Copiar dados do SQL do Azure para o Blob do Azure
O exemplo a seguir mostra:

1. Um serviço vinculado do tipo [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties).
2. Um serviço vinculado do tipo [AzureStorage](#linked-service-properties).
3. Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
4. Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](#dataset-properties).
5. Um [pipeline](data-factory-create-pipelines.md) com Atividade de Cópia que usa [SqlSource](data-factory-azure-sql-connector.md#copy-activity-properties) e [BlobSink](#copy-activity-properties).

O exemplo copia os dados de série temporal de uma tabela SQL do Azure para um blob do Azure a cada hora. As propriedades JSON usadas nesses exemplos são descritas nas seções após os exemplos.

**Serviço vinculado do SQL Azure:**

```json
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Serviço vinculado de armazenamento do Azure:**

```json
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
O Azure Data Factory oferece suporte a dois tipos de serviços vinculados de Armazenamento do Azure: **AzureStorage** e **AzureStorageSas**. Para o primeiro, você especifica a cadeia de conexão que inclui a chave de conta, e para o mais recente, você especifica o URI de SAS (Assinatura de Acesso Compartilhado). Confira a seção [Serviços vinculados](#linked-service-properties) para obter detalhes.

**Conjunto de dados de entrada do SQL Azure:**

O exemplo supõe que você criou uma tabela "MyTable" no SQL Azure e que ela contém uma coluna chamada "timestampcolumn" para dados de série temporal.

Configurar "external": true informa ao serviço Data Factory que a tabela é externa ao Data Factory e não é produzida por uma atividade no Data Factory.

```json
{
  "name": "AzureSqlInput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Conjunto de dados de saída de Blob do Azure:**

Os dados são gravados em um novo blob a cada hora (frequência: horas, intervalo: 1). O caminho de pasta para o blob é avaliado dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa as partes ano, mês, dia e horas da hora de início.

```json
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}/",
      "partitionedBy": [
        {
          "name": "Year",
          "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "HH" } }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Uma atividade de cópia em um pipeline com origem SQL e coletor de Blob:**

O pipeline contém uma Atividade de Cópia que está configurada para usar os conjuntos de dados de entrada e saída e é agendada para ser executada a cada hora. Na definição de JSON do pipeline, o tipo de **fonte** está definido como **SqlSource** e o tipo de **coletor** está definido como **BlobSink**. A consulta SQL especificada para a propriedade **SqlReaderQuery** seleciona os dados na última hora a serem copiados.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[
            {
                "name": "AzureSQLtoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureSQLInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "SqlSource",
                        "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            }
        ]
    }
}
```

> [!NOTE]
> Para mapear colunas de conjunto de dados de origem para colunas do conjunto de dados de coletor, confira [Mapeando colunas de conjunto de dados no Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Desempenho e Ajuste
Veja o [Guia de desempenho e ajuste da Atividade de Cópia](data-factory-copy-activity-performance.md) para saber mais sobre os principais fatores que afetam o desempenho da movimentação de dados (Atividade de Cópia) no Azure Data Factory, além de várias maneiras de otimizar esse processo.
