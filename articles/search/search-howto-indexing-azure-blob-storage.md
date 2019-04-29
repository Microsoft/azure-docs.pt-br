---
title: Indexar o conteúdo do Armazenamento de Blobs do Azure para pesquisa de texto completo – Azure Search
description: Saiba como indexar o Armazenamento de Blobs do Azure e extrair texto de documentos com o Azure Search.
ms.date: 03/01/2019
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 87dc1dab0670f69ff8c418be476986baec2821fb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60871325"
---
# <a name="indexing-documents-in-azure-blob-storage-with-azure-search"></a>Indexação de documentos no Armazenamento de Blobs do Azure com o Azure Search
Este artigo mostra como usar o Azure Search para indexar documentos (como PDFs, documentos do Microsoft Office e vários outros formatos comuns) armazenados no armazenamento de blobs do Azure. Primeiro, ele explica as noções básicas de configuração de um indexador de blob. Em seguida, ele explora mais profundamente os comportamentos e cenários que você pode encontrar.

## <a name="supported-document-formats"></a>Formatos de documento com suporte
O indexador de blob pode extrair o texto dos seguintes formatos de documento:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="setting-up-blob-indexing"></a>Configuração da indexação de blob
Você pode configurar um indexador do Armazenamento de Blobs do Azure usando:

* [Portal do Azure](https://ms.portal.azure.com)
* [API REST](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations) do Azure Search
* [SDK do .NET](https://aka.ms/search-sdk) do Azure Search

> [!NOTE]
> Alguns recursos (por exemplo, mapeamentos de campo) ainda não estão disponíveis no portal e precisam ser usados por meio de programação.
>

Aqui, demonstraremos o fluxo usando a API REST.

### <a name="step-1-create-a-data-source"></a>Etapa 1: Criar uma fonte de dados
Uma fonte de dados especifica quais dados indexar, as credenciais necessárias para acessar os dados e as políticas que identificam com eficiência as alterações nos dados (linhas novas, modificadas ou excluídas). Uma fonte de dados pode ser usada por vários indexadores no mesmo serviço de pesquisa.

Para a indexação de blobs, a fonte de dados deve ter as seguintes propriedades requeridas:

* **name** é o nome exclusivo da fonte de dados dentro de seu serviço de pesquisa.
* **type** deve ser `azureblob`.
* **credentials** fornece a cadeia de conexão da conta de armazenamento como o parâmetro `credentials.connectionString`. Consulte [Como especificar credenciais](#Credentials) abaixo para obter detalhes.
* **container** especifica um contêiner em sua conta de armazenamento. Por padrão, todos os blobs no contêiner são recuperáveis. Se você quiser apenas blobs de índice em um diretório virtual específico, especifique o diretório usando o parâmetro opcional **query**.

Para criar uma fonte de dados:

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
    }   

Para obter mais informações sobre Criar a API da Fonte de Dados, consulte [Criar Fonte de Dados](https://docs.microsoft.com/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>
#### <a name="how-to-specify-credentials"></a>Como especificar credenciais ####

Você pode fornecer as credenciais para o contêiner de blobs de uma das seguintes maneiras:

- **Cadeia de conexão da conta de armazenamento com acesso completo**: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>` Você pode obter a cadeia de conexão do portal do Azure, navegando até a folha da conta de armazenamento > Configurações > chaves (para contas de armazenamento clássicas) ou configurações > chaves de acesso (para contas de armazenamento do Azure Resource Manager).
- Cadeia de conexão da SAS **(assinatura de acesso compartilhado) de conta de armazenamento**: `BlobEndpoint=https://<your account>.blob.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=b&sp=rl` As SAS devem ter as permissões de lista e leitura nos contêineres e objetos (blobs neste caso).
-  **Assinatura de Acesso Compartilhado do contêiner**: `ContainerSharedAccessUri=https://<your storage account>.blob.core.windows.net/<container name>?sv=2016-05-31&sr=c&sig=<the signature>&se=<the validity end time>&sp=rl` As SAS devem ter a lista e permissões de leitura no contêiner.

Para saber mais sobre assinaturas de acesso compartilhado de armazenamento, veja [Uso de Assinaturas de Acesso Compartilhado](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> Se você usar credenciais SAS, você precisará atualizar as credenciais de fonte de dados periodicamente com assinaturas renovadas para impedir sua expiração. Se as credenciais SAS expirarem, o indexador irá falhar com uma mensagem de erro semelhante à `Credentials provided in the connection string are invalid or have expired.`.  

### <a name="step-2-create-an-index"></a>Etapa 2: Crie um índice
O índice especifica os campos em um documento, atributos e outras construções que modelam a experiência de pesquisa.

Veja como criar um índice com um campo `content` pesquisável para armazenar o texto extraído dos blobs:   

    POST https://[service name].search.windows.net/indexes?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }

Para obter mais informações sobre a criação de índices, consulte [Criar Índice](https://docs.microsoft.com/rest/api/searchservice/create-index)

### <a name="step-3-create-an-indexer"></a>Etapa 3: Criar um indexador
Um indexador conecta uma fonte de dados a um índice de pesquisa de destino e fornece um agendamento para automatizar a atualização de dados.

Uma vez que o índice e a fonte de dados forem criados, será possível criar o indexador:

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "blob-indexer",
      "dataSourceName" : "blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Esse indexador será executado a cada duas horas (o intervalo de agendamento é definido como "PT2H"). Para executar um indexador a cada 30 minutos, defina o intervalo para "PT30M". O intervalo mais curto com suporte é de 5 minutos. O agendamento é opcional – se ele for omitido, um indexador será executado apenas uma vez quando for criado. No entanto, você pode executar um indexador sob demanda a qualquer momento.   

Para saber mais sobre a API Criar Indexador, veja [Criar indexador](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

## <a name="how-azure-search-indexes-blobs"></a>Como o Azure Search indexa os blobs

Dependendo da [configuração do indexador](#PartsOfBlobToIndex), o indexador de blobs pode indexar somente metadados de armazenamento (algo útil quando você está preocupado apenas com os metadados e não precisa indexar o conteúdo dos blobs), metadados de armazenamento e conteúdo ou conteúdo textual e de metadados. Por padrão, o indexador extrai os metadados e o conteúdo.

> [!NOTE]
> Por padrão, os blobs com conteúdo estruturado, como JSON, CSV e indexados como uma única parte de texto. Se você desejar indexar blobs JSON e CSV de uma maneira estruturada, consulte as versões prévias dos recursos [Indexando blobs JSON](search-howto-index-json-blobs.md) e [Indexando blobs CSV](search-howto-index-csv-blobs.md).
>
> Um documento composto ou incorporado (como um arquivo ZIP ou um documento do Word com email do Outlook incorporado contendo anexos) também é indexado como um único documento.

* O conteúdo textual do documento é extraído para um campo de cadeia de caracteres chamado `content`.

> [!NOTE]
> O Azure Search limita quanto texto é extraído dependendo do tipo de preço: 32 mil caracteres na camada gratuita, 64 mil na Básica e 4 milhões nas camadas Standard, Standard S2 e Standard S3. Um aviso é incluído na resposta de status do indexador para documentos truncados.  

* As propriedades de metadados especificadas pelo usuário e presentes no blob, se houver alguma, são extraídas literalmente.
* As propriedades de metadados de blob padrão são extraídas para os seguintes campos:

  * **metadata\_storage\_name** (Edm.String): o nome do arquivo do blob. Por exemplo, se você tiver um blob /my-container/my-folder/subfolder/resume.pdf, o valor desse campo será `resume.pdf`.
  * **metadata\_storage\_path** (Edm.String): o URI completo do blob, incluindo a conta de armazenamento. Por exemplo, `https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`
  * **metadata\_storage\_content\_type** (Edm.String): o tipo de conteúdo, conforme especificado pelo código usado para carregar o blob. Por exemplo: `application/octet-stream`.
  * **metadata\_storage\_last\_modified** (Edm.DateTimeOffset): último carimbo de data/hora modificado para o blob. O Azure Search usa esse carimbo de data/hora para identificar os blobs alterados, a fim de evitar a reindexação total após a indexação inicial.
  * **metadata\_storage\_size** (Edm.Int64): tamanho do blob em bytes.
  * **metadata\_storage\_content\_md5** (Edm.String): hash MD5 do conteúdo do blob, se estiver disponível.
* As propriedades de metadados específicas a cada formato de documento são extraídas nos campos listados [aqui](#ContentSpecificMetadata).

Não é necessário definir os campos para todas as propriedades acima em seu índice de pesquisa, basta capturar as propriedades necessárias para seu aplicativo.

> [!NOTE]
> Geralmente, os nomes de campo no índice existente serão diferentes dos nomes de campo gerados durante a extração do documento. Você pode usar os **mapeamentos de campo** para mapear os nomes de propriedade fornecidos pelo Azure Search para os nomes de campo em seu índice de pesquisa. Você verá um exemplo de uso de mapeamento de campo abaixo.
>
>

<a name="DocumentKeys"></a>
### <a name="defining-document-keys-and-field-mappings"></a>Definir chaves de documento e mapeamentos de campo
No Azure Search, a chave do documento identifica exclusivamente um documento. Cada índice de pesquisa deve ter exatamente um campo de chave do tipo Edm.String. O campo de chave é necessário para cada documento adicionado ao índice (é, na verdade, o único campo obrigatório).  

Você deve considerar cuidadosamente qual campo extraído deve ser mapeado para o campo de chave de seu índice. Os candidatos são:

* **metadata\_storage\_name**: este pode ser um candidato conveniente, mas observe que 1) talvez os nomes não sejam exclusivos, pois você pode ter blobs com o mesmo nome em pastas diferentes, e 2) o nome pode conter caracteres inválidos em chaves de documento, por exemplo, traços. Você pode lidar com caracteres inválidos usando a `base64Encode` [função de mapeamento de campo](search-indexer-field-mappings.md#base64EncodeFunction). Se fizer isso, lembre-se de codificar as chaves de documento ao transmiti-las em chamadas à API, como Pesquisa. (Por exemplo, em .NET você pode usar o método [UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) para essa finalidade).
* **metadata\_storage\_path**: o uso do caminho completo garante a exclusividade, mas o caminho contém definitivamente caracteres `/` que são [inválidos em uma chave de documento](https://docs.microsoft.com/rest/api/searchservice/naming-rules).  Como foi mencionado acima, você tem a opção de codificar as chaves usando a [função](search-indexer-field-mappings.md#base64EncodeFunction) `base64Encode`.
* Se nenhuma das opções acima funcionar para você, adicione uma propriedade de metadados personalizada aos blobs. No entanto, essa opção exige que seu processo de carregamento de blob adicione essa propriedade de metadados a todos os blobs. Como a chave é uma propriedade obrigatória, todos os blobs que não tiverem essa propriedade apresentarão falha na indexação.

> [!IMPORTANT]
> Se não houver um mapeamento explícito para o campo de chave no índice, o Azure Search usará automaticamente `metadata_storage_path` como chave e valores da chave de codificação com base-64 (a segunda opção acima).
>
>

Para este exemplo, vamos escolher o campo `metadata_storage_name` como a chave do documento. Vamos supor também que o índice tenha um campo de chave chamado `key` e um campo `fileSize` para armazenamento do tamanho do documento. Para conectar as coisas conforme o desejado, especifique os seguintes mapeamentos de campo ao criar ou atualizar o indexador:

    "fieldMappings" : [
      { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
      { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
    ]

Para unir tudo isso, veja como você pode adicionar mapeamentos de campo e habilitar a codificação das chaves em base-64 para um indexador existente:

    PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "dataSourceName" : " blob-datasource ",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "fieldMappings" : [
        { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
        { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
      ]
    }

> [!NOTE]
> Para saber mais sobre mapeamentos de campo, consulte [este artigo](search-indexer-field-mappings.md).
>
>

<a name="WhichBlobsAreIndexed"></a>
## <a name="controlling-which-blobs-are-indexed"></a>Controlando quais blobs serão indexados
É possível controlar quais blobs são indexados e quais são ignorados.

### <a name="index-only-the-blobs-with-specific-file-extensions"></a>Indexe apenas os blobs com extensões de arquivo específicas
Você pode indexar apenas os blobs com as extensões do nome de arquivo especificadas usando o parâmetro de configuração do indexador `indexedFileNameExtensions`. O valor é uma cadeia de caracteres que contém uma lista separada por vírgulas das extensões de arquivo (com um ponto à esquerda). Por exemplo, para indexar apenas blobs de .PDF e .DOCX, faça o seguinte:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
    }

### <a name="exclude-blobs-with-specific-file-extensions"></a>Excluir blobs com extensões de arquivo específicas
Você pode excluir os blobs com extensões do nome de arquivo específicas da indexação usando o parâmetro de configuração `excludedFileNameExtensions`. O valor é uma cadeia de caracteres que contém uma lista separada por vírgulas das extensões de arquivo (com um ponto à esquerda). Por exemplo, para indexar todos os blobs, exceto aqueles com as extensões .PNG e .JPEG, faça o seguinte:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
    }

Se os parâmetros `indexedFileNameExtensions` e `excludedFileNameExtensions` estiverem presentes, o Azure Search primeiro examinará `indexedFileNameExtensions`, em seguida, `excludedFileNameExtensions`. Isso significa que, se a mesma extensão de arquivo estiver presente nas duas listas, ela será excluída da indexação.

<a name="PartsOfBlobToIndex"></a>
## <a name="controlling-which-parts-of-the-blob-are-indexed"></a>Controlando quais partes do blob são indexadas

Você pode controlar quais partes dos blobs são indexadas usando o parâmetro de configuração `dataToExtract`. Ele pode usar os seguintes valores:

* `storageMetadata` – especifica que somente [as propriedades de blob padrão e os metadados especificados pelo usuário](../storage/blobs/storage-properties-metadata.md) são indexados.
* `allMetadata` – especifica que os metadados de armazenamento e os [metadados específicos do tipo de conteúdo](#ContentSpecificMetadata) extraídos do conteúdo do blob são indexados.
* `contentAndMetadata` – especifica que todos os metadados e conteúdo textual extraídos do blob são indexados. Esse é o valor padrão.

Por exemplo, para indexar apenas os metadados de armazenamento, use:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "dataToExtract" : "storageMetadata" } }
    }

### <a name="using-blob-metadata-to-control-how-blobs-are-indexed"></a>Usando metadados de blob para controlar como os blobs são indexados

Os parâmetros de configuração descritos acima se aplicam a todos os blobs. Às vezes, você pode desejar controlar como *blobs individuais* são indexados. Faça isso adicionando as seguintes propriedades de metadados de blob e valores:

| Nome da propriedade | Valor da propriedade | Explicação |
| --- | --- | --- |
| AzureSearch_Skip |"true" |Instrui o indexador de blobs a ignorar o blob por completo. Não há nenhuma tentativa de extração de metadados nem de conteúdo. Isso é útil quando um blob específico falha repetidamente e interrompe o processo de indexação. |
| AzureSearch_SkipContent |"true" |Isso é equivalente à configuração `"dataToExtract" : "allMetadata"` descrita [acima](#PartsOfBlobToIndex), no escopo de um blob específico. |

<a name="DealingWithErrors"></a>
## <a name="dealing-with-errors"></a>Lidando com problemas

Por padrão, o indexador de blobs é interrompido assim que encontra um blob com um tipo de conteúdo sem suporte (por exemplo, uma imagem). Evidentemente, você pode usar o parâmetro `excludedFileNameExtensions` para ignorar alguns tipos de conteúdo. No entanto, talvez você precise indexar blobs sem conhecer todos os possíveis tipos de conteúdo com antecedência. Para continuar a indexação quando um tipo de conteúdo sem suporte é encontrado, defina o parâmetro de configuração `failOnUnsupportedContentType` como `false`:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false } }
    }

Para alguns blobs, o Azure Search não é capaz de determinar o tipo de conteúdo ou não é capaz de processar um documento com tipo de conteúdo compatível. Para ignorar este modo de falha, defina o parâmetro de configuração `failOnUnprocessableDocument` como false:

      "parameters" : { "configuration" : { "failOnUnprocessableDocument" : false } }

O Azure Search limita o tamanho de blobs que são indexados. Esses limites são documentados em [Limites de serviço no Azure Search](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity). Por padrão, os blobs superdimensionados são tratados como erros. No entanto, você ainda pode indexar os metadados de armazenamento de blobs superdimensionados se você definir o parâmetro de configuração `indexStorageMetadataOnlyForOversizedDocuments` como true: 

    "parameters" : { "configuration" : { "indexStorageMetadataOnlyForOversizedDocuments" : true } }

Você também poderá continuar a indexação se ocorrem erros a qualquer momento do processamento, ao analisar blobs ou ao adicionar documentos a um índice. Para ignorar um número específico de erros, defina os parâmetros de configuração `maxFailedItems` e `maxFailedItemsPerBatch` como os valores desejados. Por exemplo: 

    {
      ... other parts of indexer definition
      "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
    }

## <a name="incremental-indexing-and-deletion-detection"></a>Indexação incremental e detecção de exclusão 
Quando você configura um indexador de blob para execução em um cronograma, ele reindexa apenas os blobs alterados, conforme determinado pelo carimbo de data/hora `LastModified` do blob.

> [!NOTE]
> Não é necessário especificar uma política de detecção de alteração, a indexação incremental é habilitada automaticamente para você.

Para dar suporte à exclusão de documentos, use uma abordagem de "exclusão reversível". Se você excluir os blobs imediatamente, documentos correspondentes não serão removidos do índice de pesquisa. Em vez disso, execute as seguintes etapas:  

1. Adicione uma propriedade de metadados personalizados ao blob para indicar ao Azure Search que ela é logicamente excluída
2. Configure uma política de detecção de exclusão reversível na fonte de dados
3. Depois que o indexador processar o blob (conforme mostrado pela API do status do indexador), você pode excluir fisicamente o blob

Por exemplo, a política a seguir considerará que um blob foi excluído se tiver uma propriedade de metadados `IsDeleted` com o valor `true`:

    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",     
            "softDeleteColumnName" : "IsDeleted",
            "softDeleteMarkerValue" : "true"
        }
    }   

## <a name="indexing-large-datasets"></a>Indexando grandes conjuntos de dados

A indexação de blobs pode ser um processo demorado. Nos casos em que você tem milhões de blobs para indexar, é possível acelerar a indexação particionando seus dados e usando vários indexadores para processar os dados em paralelo. Veja como você pode configurar isso:

- Particione seus dados em vários contêineres de blob ou pastas virtuais
- Configure várias fontes de dados do Azure Search, um por contêiner ou pasta. Para apontar para uma pasta de blobs, use o parâmetro `query`:

    ```
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" }
    }
    ```

- Crie um indexador correspondente para cada fonte de dados. Todos os indexadores podem apontar para o mesmo índice de pesquisa de destino.  

- Uma unidade de pesquisa em seu serviço pode executar um indexador a qualquer momento. Criar vários indexadores conforme descrito acima será útil somente se eles realmente forem executados em paralelo. Para executar vários indexadores em paralelo, escale horizontalmente seu serviço de pesquisa criando um número apropriado de partições e réplicas. Por exemplo, se o serviço de pesquisa tiver seis unidades de pesquisa (por exemplo, 2 partições x 3 réplicas), seis indexadores poderão ser executados simultaneamente, resultando em um aumento de seis vezes a taxa de transferência da indexação. Para saber mais sobre o planejamento de capacidade e colocação em escala, consulte [Escalar níveis de recursos para cargas de trabalho de consulta e indexação no Azure Search](search-capacity-planning.md).

## <a name="indexing-documents-along-with-related-data"></a>Indexação de documentos junto com os dados relacionados

Talvez você queira "montar" documentos de várias fontes em seu índice. Por exemplo, convém mesclar texto de blobs com outros metadados armazenados no Cosmos DB. Você pode até usar a API de indexação por push junto a vários indexadores para criar documentos de pesquisa de várias partes. 

Para que isso funcione, todos os indexadores e outros componentes precisam concordar com a chave de documento. Para obter um passo a passo detalhado, confira este artigo externo: [Combinar documentos com outros dados no Azure Search](https://blog.lytzen.name/2017/01/combine-documents-with-other-data-in.html).

<a name="IndexingPlainText"></a>
## <a name="indexing-plain-text"></a>Indexação de texto sem formatação 

Se todos os seus blobs contiverem texto sem formatação na mesma codificação, você poderá melhorar significativamente o desempenho de indexação usando o **modo de análise de texto**. Para usar o modo de análise de texto, defina a propriedade de configuração `parsingMode` como `text`:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text" } }
    }

Por padrão, a codificação `UTF-8` será assumida. Para especificar uma codificação diferente, use a propriedade de configuração `encoding`: 

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text", "encoding" : "windows-1252" } }
    }


<a name="ContentSpecificMetadata"></a>
## <a name="content-type-specific-metadata-properties"></a>Propriedades de metadados específicas ao tipo de conteúdo
A tabela a seguir resume o processo executado para cada formato de documento, e descreve as propriedades dos metadados extraídos pelo Azure Search.

| Formato de documento/tipo de conteúdo | Propriedades de metadados específicas do tipo de conteúdo | Detalhes do processamento |
| --- | --- | --- |
| HTML (`text/html`) |`metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` |Remoção da marcação HTML e extração do texto |
| PDF (`application/pdf`) |`metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title` |Extração do texto, incluindo documentos incorporados (excluindo imagens) |
| DOCX (application/vnd.openxmlformats-officedocument.wordprocessingml.document) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extração de texto, incluindo documentos incorporados |
| DOC (application/msword) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extração de texto, incluindo documentos incorporados |
| XLSX (application/vnd.openxmlformats-officedocument.spreadsheetml.sheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extração de texto, incluindo documentos incorporados |
| XLS (application/vnd.ms-excel) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extração de texto, incluindo documentos incorporados |
| PPTX (application/vnd.openxmlformats-officedocument.presentationml.presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Extração de texto, incluindo documentos incorporados |
| PPT (application/vnd.ms-powerpoint) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Extração de texto, incluindo documentos incorporados |
| MSG (application/vnd.ms-outlook) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_message_bcc`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` |Extração do texto, incluindo anexos |
| ZIP (application/zip) |`metadata_content_type` |Extração do texto de todos os documentos no arquivo |
| XML (application/xml) |`metadata_content_type`</br>`metadata_content_encoding`</br> |Remoção da marcação XML e extração do texto |
| JSON (application/json) |`metadata_content_type`</br>`metadata_content_encoding` |Extrair texto<br/>OBSERVAÇÃO:  se precisar extrair vários campos de documento de um blob JSON, confira [Como indexar blobs JSON](search-howto-index-json-blobs.md) para obter detalhes |
| EML (message/rfc822) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_creation_date`<br/>`metadata_subject` |Extração do texto, incluindo anexos |
| RTF (aplicativo/rtf) |`metadata_content_type`</br>`metadata_author`</br>`metadata_character_count`</br>`metadata_creation_date`</br>`metadata_page_count`</br>`metadata_word_count`</br> | Extrair texto|
| Texto sem formatação (text/plain) |`metadata_content_type`</br>`metadata_content_encoding`</br> | Extrair texto|


## <a name="help-us-make-azure-search-better"></a>Ajude-nos a aprimorar o Azure Search
Caso você tenha solicitações de recursos ou ideias para melhorias, entre em contato conosco pelo [site UserVoice](https://feedback.azure.com/forums/263029-azure-search/).
