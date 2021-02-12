---
title: Configurar um indexador de BLOB
titleSuffix: Azure Cognitive Search
description: Configure um indexador de blob do Azure para automatizar a indexação de conteúdo de BLOB para operações de pesquisa de texto completo no Azure Pesquisa Cognitiva.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/03/2021
ms.custom: contperf-fy21q3
ms.openlocfilehash: 74813fabec4d5fe43cd158bb4aa359c2a3b0188a
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2021
ms.locfileid: "99988714"
---
# <a name="how-to-configure-blob-indexing-in-cognitive-search"></a>Como configurar a indexação de blob no Pesquisa Cognitiva

Este artigo mostra como configurar um indexador de BLOB para indexação de documentos baseados em texto (como PDFs, Microsoft Office documentos e outros) no Pesquisa Cognitiva do Azure. Se você não estiver familiarizado com os conceitos do indexador, comece com [indexadores no Azure pesquisa cognitiva](search-indexer-overview.md) e [crie um indexador de pesquisa antes de](search-howto-create-indexers.md) mergulhar na indexação de BLOB.

<a name="SupportedFormats"></a>

## <a name="supported-document-formats"></a>Formatos de documento com suporte

O indexador de blob Pesquisa Cognitiva do Azure pode extrair texto dos seguintes formatos de documento:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="data-source-definitions"></a>Definições de fonte de dados

A diferença entre um indexador de BLOB e qualquer outro indexador é a definição da fonte de dados atribuída ao indexador. A fonte de dados encapsula todas as propriedades que especificam o tipo, a conexão e o local do conteúdo a ser indexado.

Uma definição de fonte de dados de blob é semelhante ao exemplo abaixo:

```http
{
    "name" : "my-blob-datasource",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
    "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
}
```

A `"credentials"` propriedade pode ser uma cadeia de conexão, conforme mostrado no exemplo acima, ou uma das abordagens alternativas descritas na próxima seção. A `"container"` propriedade fornece o local de conteúdo no armazenamento do Azure e `"query"` é usada para especificar uma subpasta no contêiner. Para obter mais informações sobre definições de fonte de dados, consulte [criar fonte de dados (REST)](/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>

## <a name="credentials"></a>Credenciais

Você pode fornecer as credenciais para o contêiner de blobs de uma das seguintes maneiras:

**Cadeia de conexão de identidade gerenciada**: `{ "connectionString" : "ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.Storage/storageAccounts/<your storage account name>/;" }`

Essa cadeia de conexão não requer uma chave de conta, mas você deve seguir as instruções para [Configurar uma conexão com uma conta de armazenamento do Azure usando uma identidade gerenciada](search-howto-managed-identities-storage.md).

**Cadeia de conexão da conta de armazenamento de acesso completo**: `{ "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>;" }`

É possível obter a cadeia de conexão no portal do Azure navegando para a folha da conta de armazenamento > Configurações > Chaves (para contas de armazenamento Clássicas) ou Configurações > Chaves de acesso (para contas de armazenamento do Azure Resource Manager).

Cadeia de conexão de SAS ( **assinatura de acesso compartilhado** ) da conta de armazenamento:`{ "connectionString" : "BlobEndpoint=https://<your account>.blob.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=b&sp=rl;" }`

As SAS devem ter as permissões de lista e leitura nos contêineres e objetos (blobs neste caso).

**Assinatura de acesso compartilhado do contêiner**: `{ "connectionString" : "ContainerSharedAccessUri=https://<your storage account>.blob.core.windows.net/<container name>?sv=2016-05-31&sr=c&sig=<the signature>&se=<the validity end time>&sp=rl;" }`

As SAS devem ter a lista e permissões de leitura no contêiner. Para obter mais informações sobre assinaturas de acesso compartilhado de armazenamento, consulte [usando assinaturas de acesso compartilhado](../storage/common/storage-sas-overview.md).

> [!NOTE]
> Se você usar credenciais SAS, você precisará atualizar as credenciais de fonte de dados periodicamente com assinaturas renovadas para impedir sua expiração. Se as credenciais SAS expirarem, o indexador falhará com uma mensagem de erro semelhante a "as credenciais fornecidas na cadeia de conexão são inválidas ou expiraram".  

## <a name="index-definitions"></a>Definições de índice

O índice especifica os campos em um documento, atributos e outras construções que modelam a experiência de pesquisa. O exemplo a seguir cria um índice simples usando [criar índice (API REST)](/rest/api/searchservice/create-index). 

```http
POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
      "name" : "my-target-index",
      "fields": [
        { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
        { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
      ]
}
```

As definições de índice exigem um campo na `"fields"` coleção para atuar como a chave do documento. As definições de índice também devem incluir campos para conteúdo e metadados.

Um **`content`** campo é usado para armazenar o texto extraído dos BLOBs. Sua definição desse campo pode ser semelhante à mostrada acima. Você não precisa usar esse nome, mas permite que você aproveite os mapeamentos de campo implícitos. O indexador de blob pode enviar conteúdo de BLOB para um campo de conteúdo EDM. String no índice, nenhum mapeamento de campo é necessário.

Você também pode adicionar campos para quaisquer metadados de BLOB que desejar no índice. O indexador pode ler propriedades de metadados personalizadas, propriedades de [metadados padrão](#indexing-blob-metadata) e propriedades [de metadados específicas do conteúdo](search-blob-metadata-properties.md) . Para obter mais informações sobre índices, consulte [criar um índice](search-what-is-an-index.md).

<a name="DocumentKeys"></a>

### <a name="defining-document-keys-and-field-mappings"></a>Definir chaves de documento e mapeamentos de campo

Em um índice de pesquisa, a chave do documento identifica exclusivamente cada documento. O campo escolhido deve ser do tipo `Edm.String` . Para conteúdo de BLOB, os melhores candidatos para uma chave de documento são propriedades de metadados no BLOB.

+ **`metadata_storage_name`** -Essa propriedade é um candidato, mas somente se os nomes forem exclusivos em todos os contêineres e pastas que você estiver indexando. Independentemente do local do blob, o resultado final é que a chave do documento (nome) deve ser exclusiva no índice de pesquisa depois que todo o conteúdo tiver sido indexado. 

  Outro problema potencial sobre o nome de armazenamento é que ele pode conter caracteres inválidos para chaves de documento, como traços. Você pode manipular caracteres inválidos usando a `base64Encode` [função de mapeamento de campos](search-indexer-field-mappings.md#base64EncodeFunction). Se você fizer isso, lembre-se também de codificar as chaves do documento ao passá-las em chamadas à API, como o [REST (documento de pesquisa)](/rest/api/searchservice/lookup-document). No .NET, você pode usar o [método UrlTokenEncode](/dotnet/api/system.web.httpserverutility.urltokenencode) para codificar caracteres.

+ **`metadata_storage_path`** -o uso do caminho completo garante a exclusividade, mas o caminho definitivamente contém `/` caracteres que são [inválidos em uma chave de documento](/rest/api/searchservice/naming-rules). Como acima, você pode usar a `base64Encode` [função](search-indexer-field-mappings.md#base64EncodeFunction) para codificar caracteres.

+ Uma terceira opção é adicionar uma propriedade de metadados personalizada aos BLOBs. Essa opção requer que o processo de carregamento de blob adicione essa propriedade de metadados a todos os BLOBs. Como a chave é uma propriedade necessária, todos os blobs que estão faltando um valor não serão indexados.

> [!IMPORTANT]
> Se não houver nenhum mapeamento explícito para o campo de chave no índice, o Azure Pesquisa Cognitiva usará automaticamente `metadata_storage_path` como os valores de chave key e base-64 codificações (a segunda opção acima).
>

#### <a name="example"></a>Exemplo

O exemplo a seguir demonstra `metadata_storage_name` como a chave do documento. Suponha que o índice tenha um campo de chave chamado `key` e outro campo chamado `fileSize` para armazenar o tamanho do documento. Os [mapeamentos de campo](search-indexer-field-mappings.md) na definição do indexador estabelecem associações de campo e `metadata_storage_name` têm a [ `base64Encode` função de mapeamento de campo](search-indexer-field-mappings.md#base64EncodeFunction) para manipular caracteres sem suporte.

```http
PUT https://[service name].search.windows.net/indexers/my-blob-indexer?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  "dataSourceName" : "my-blob-datasource ",
  "targetIndexName" : "my-target-index",
  "schedule" : { "interval" : "PT2H" },
  "fieldMappings" : [
    { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
    { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
  ]
}
```

#### <a name="how-to-make-an-encoded-field-searchable"></a>Como tornar um campo codificado "pesquisável"

Há ocasiões em que você precisa usar uma versão codificada de um campo como `metadata_storage_path` a chave, mas também precisa que esse campo seja pesquisável (sem codificação) no índice de pesquisa. Para dar suporte a ambos os casos de uso, você pode mapear `metadata_storage_path` para dois campos; um para a chave (codificada) e um segundo para um campo de caminho que podemos supor é atribuído como "pesquisável" no esquema de índice. O exemplo a seguir mostra dois mapeamentos de campo para `metadata_storage_path` .

```http
PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  "dataSourceName" : " blob-datasource ",
  "targetIndexName" : "my-target-index",
  "schedule" : { "interval" : "PT2H" },
  "fieldMappings" : [
    { "sourceFieldName" : "metadata_storage_path", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
    { "sourceFieldName" : "metadata_storage_path", "targetFieldName" : "path" }
  ]
}
```

<a name="PartsOfBlobToIndex"></a>

## <a name="index-content-and-metadata"></a>Conteúdo e metadados do índice

Os BLOBs contêm conteúdo e metadados. Você pode controlar quais partes dos blobs são indexadas usando o parâmetro de configuração `dataToExtract`. Ele pode usar os seguintes valores:

+ `contentAndMetadata` – especifica que todos os metadados e conteúdo textual extraídos do blob são indexados. Esse é o valor padrão.

+ `storageMetadata` – especifica que somente [as propriedades de blob padrão e os metadados especificados pelo usuário](../storage/blobs/storage-blob-container-properties-metadata.md) são indexados.

+ `allMetadata` -Especifica que as propriedades de blob padrão e quaisquer [metadados para tipos de conteúdo encontrados](search-blob-metadata-properties.md) são extraídos do conteúdo do blob e indexados.

Por exemplo, para indexar apenas os metadados de armazenamento, use:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "dataToExtract" : "storageMetadata" } }
}
```

<a name="how-azure-search-indexes-blobs"></a>

### <a name="indexing-blob-content"></a>Indexando conteúdo de BLOB

Por padrão, os BLOBs com conteúdo estruturado, como JSON ou CSV, são indexados como uma única parte do texto. Mas se os documentos JSON ou CSV tiverem uma estrutura interna (delimitadores), você poderá atribuir modos de análise para gerar documentos de pesquisa individuais para cada linha ou elemento. Para obter mais informações, consulte [indexando BLOBs JSON](search-howto-index-json-blobs.md) e [indexando BLOBs CSV](search-howto-index-csv-blobs.md).

Um documento composto ou incorporado (como um arquivo ZIP, um documento do Word com emails inseridos do Outlook contendo anexos ou um. O arquivo MSG com anexos) também é indexado como um único documento. Por exemplo, todas as imagens extraídas dos anexos de um. O arquivo MSG será retornado no campo normalized_images.

O conteúdo textual do documento é extraído para um campo de cadeia de caracteres chamado `content`.

  > [!NOTE]
  > O Azure Pesquisa Cognitiva limita a quantidade de texto que ele extrai, dependendo do tipo de preço. Os [limites de serviço](search-limits-quotas-capacity.md#indexer-limits) atuais são 32.000 caracteres para a camada gratuita, 64.000 para básico, 4 milhões para standard, 8 milhões para Standard S2 e 16 milhões para Standard S3. Um aviso é incluído na resposta de status do indexador para documentos truncados.  

<a name="indexing-blob-metadata"></a>

### <a name="indexing-blob-metadata"></a>Indexação dos metadados de blobs

Indexadores também podem indexar metadados de BLOB. Primeiro, todas as propriedades de metadados especificadas pelo usuário podem ser extraídas literalmente. Para receber os valores, você deve definir o campo no índice de pesquisa do tipo `Edm.String` , com o mesmo nome que a chave de metadados do blob. Por exemplo, se um blob tiver uma chave de metadados de `Sensitivity` com valor `High` , você deverá definir um campo chamado `Sensitivity` em seu índice de pesquisa e ele será preenchido com o valor `High` .

Em segundo lugar, as propriedades de metadados de blob padrão podem ser extraídas nos campos listados abaixo. O indexador de blob cria automaticamente mapeamentos de campo internos para essas propriedades de metadados de BLOB. Você ainda precisa adicionar os campos desejados para usar a definição de índice, mas pode omitir a criação de mapeamentos de campo no indexador.

  + **metadata_storage_name** ( `Edm.String` ) – o nome do arquivo do blob. Por exemplo, se você tiver um blob /my-container/my-folder/subfolder/resume.pdf, o valor desse campo será `resume.pdf`.

  + **metadata_storage_path** ( `Edm.String` ) – o URI completo do blob, incluindo a conta de armazenamento. Por exemplo, `https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`

  + **metadata_storage_content_type** ( `Edm.String` )-tipo de conteúdo conforme especificado pelo código usado para carregar o blob. Por exemplo, `application/octet-stream`.

  + **metadata_storage_last_modified** ( `Edm.DateTimeOffset` ) – carimbo de data/hora da última modificação para o blob. O Azure Pesquisa Cognitiva usa esse carimbo de data/hora para identificar BLOBs alterados, para evitar a reindexação de tudo após a indexação inicial.

  + **metadata_storage_size** ( `Edm.Int64` )-tamanho do blob em bytes.

  + **metadata_storage_content_md5** ( `Edm.String` )-hash MD5 do conteúdo do blob, se disponível.

  + **metadata_storage_sas_token** ( `Edm.String` ) – um token SAS temporário que pode ser usado por [habilidades personalizadas](cognitive-search-custom-skill-interface.md) para obter acesso ao blob. Esse token não deve ser armazenado para uso posterior, pois ele pode expirar.

Por fim, todas as propriedades de metadados específicas ao formato de documento dos BLOBs que você está indexando também podem ser representadas no esquema de índice. Para obter mais informações sobre metadados específicos de conteúdo, consulte [Propriedades de metadados de conteúdo](search-blob-metadata-properties.md).

É importante ressaltar que você não precisa definir campos para todas as propriedades acima em seu índice de pesquisa – basta capturar as propriedades necessárias para seu aplicativo.

<a name="WhichBlobsAreIndexed"></a>

## <a name="how-to-control-which-blobs-are-indexed"></a>Como controlar quais BLOBs são indexados

Você pode controlar quais BLOBs são indexados e quais são ignorados, pelo tipo de arquivo do BLOB ou definindo propriedades no próprio BLOB, fazendo com que o indexador pule sobre eles.

### <a name="include-specific-file-extensions"></a>Incluir extensões de arquivo específicas

Use `indexedFileNameExtensions` para fornecer uma lista separada por vírgulas de extensões de arquivo para indexar (com um ponto à esquerda). Por exemplo, para indexar apenas blobs de .PDF e .DOCX, faça o seguinte:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
}
```

### <a name="exclude-specific-file-extensions"></a>Excluir extensões de arquivo específicas

Use `excludedFileNameExtensions` para fornecer uma lista separada por vírgulas de extensões de arquivo a serem ignoradas (novamente, com um ponto à esquerda). Por exemplo, para indexar todos os blobs, exceto aqueles com as extensões .PNG e .JPEG, faça o seguinte:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
}
```

Se ambos `indexedFileNameExtensions` os `excludedFileNameExtensions` parâmetros e estiverem presentes, o indexador primeiro examinará `indexedFileNameExtensions` , em seguida, em `excludedFileNameExtensions` . Se a mesma extensão de arquivo estiver em ambas as listas, ela será excluída da indexação.

### <a name="add-skip-metadata-the-blob"></a>Adicionar metadados "Skip" ao blob

Os parâmetros de configuração do indexador se aplicam a todos os BLOBs no contêiner ou na pasta. Às vezes, você deseja controlar como os *BLOBs individuais* são indexados. Você pode fazer isso adicionando as propriedades e valores de metadados a seguir aos BLOBs no armazenamento de BLOBs. Quando o indexador encontrar essas propriedades, ele ignorará o BLOB ou seu conteúdo na execução da indexação.

| Nome da propriedade | Valor da propriedade | Explicação |
| ------------- | -------------- | ----------- |
| `AzureSearch_Skip` |`"true"` |Instrui o indexador de blobs a ignorar o blob por completo. Não há nenhuma tentativa de extração de metadados nem de conteúdo. Isso é útil quando um blob específico falha repetidamente e interrompe o processo de indexação. |
| `AzureSearch_SkipContent` |`"true"` |Isso é equivalente à configuração `"dataToExtract" : "allMetadata"` descrita [acima](#PartsOfBlobToIndex), no escopo de um blob específico. |

## <a name="index-large-datasets"></a>Indexar conjuntos de grandes volumes

A indexação de blobs pode ser um processo demorado. Nos casos em que você tem milhões de BLOBs para indexar, é possível acelerar a indexação Particionando seus dados e usando vários indexadores para [processar os dados em paralelo](search-howto-large-index.md#parallel-indexing). Veja como você pode configurar isso:

+ Particione seus dados em vários contêineres de blob ou pastas virtuais

+ Configure várias fontes de dados, uma por contêiner ou pasta. Para apontar para uma pasta de blobs, use o parâmetro `query`:

    ```json
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" }
    }
    ```

+ Crie um indexador correspondente para cada fonte de dados. Todos os indexadores devem apontar para o mesmo índice de pesquisa de destino.  

+ Uma unidade de pesquisa em seu serviço pode executar um indexador a qualquer momento. Criar vários indexadores conforme descrito acima será útil somente se eles realmente forem executados em paralelo.

  Para executar vários indexadores em paralelo, escale horizontalmente seu serviço de pesquisa criando um número apropriado de partições e réplicas. Por exemplo, se o serviço de pesquisa tiver seis unidades de pesquisa (por exemplo, 2 partições x 3 réplicas), seis indexadores poderão ser executados simultaneamente, resultando em um aumento de seis vezes a taxa de transferência da indexação. Para saber mais sobre dimensionamento e planejamento de capacidade, confira [ajustar a capacidade de um serviço de pesquisa cognitiva do Azure](search-capacity-planning.md).

<a name="DealingWithErrors"></a>

## <a name="handling-errors"></a>Tratando erros

Os erros que normalmente ocorrem durante a indexação incluem tipos de conteúdo sem suporte, conteúdo ausente ou BLOBs superdimensionados.

Por padrão, o indexador de blobs é interrompido assim que encontra um blob com um tipo de conteúdo sem suporte (por exemplo, uma imagem). Você pode usar o `excludedFileNameExtensions` parâmetro para ignorar determinados tipos de conteúdo. No entanto, talvez você queira indexar para continuar mesmo se ocorrerem erros e, em seguida, depurar documentos individuais mais tarde. Para obter mais informações sobre erros do indexador, consulte [Solucionando problemas comuns do indexador](search-indexer-troubleshooting.md) e [erros e avisos do indexador](cognitive-search-common-errors-warnings.md).

### <a name="respond-to-errors"></a>Responder a erros

Há quatro propriedades indexadoras que controlam a resposta do indexador quando ocorrem erros. Os exemplos a seguir mostram como definir essas propriedades na definição do indexador. Se um indexador já existir, você poderá adicionar essas propriedades editando a definição no Portal.

#### <a name="maxfaileditems-and-maxfaileditemsperbatch"></a>`"maxFailedItems"` e `"maxFailedItemsPerBatch"`

Continue a indexação se ocorrerem erros em qualquer ponto de processamento, durante a análise de BLOBs ou ao adicionar documentos a um índice. Defina essas propriedades com o número de falhas aceitáveis. Um valor de `-1` permite o processamento, independentemente de quantos erros ocorram. Caso contrário, o valor será um inteiro positivo.

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
}
```

#### <a name="failonunsupportedcontenttype-and-failonunprocessabledocument"></a>`"failOnUnsupportedContentType"` e `"failOnUnprocessableDocument"` 

Para alguns BLOBs, o Azure Pesquisa Cognitiva não pode determinar o tipo de conteúdo ou não pode processar um documento de um tipo de conteúdo com suporte de outra forma. Para ignorar essas condições de falha, defina parâmetros de configuração como `false` :

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false, "failOnUnprocessableDocument" : false } }
}
```

### <a name="relax-indexer-constraints"></a>Relaxar as restrições do indexador

Você também pode definir [Propriedades de configuração de blob](/rest/api/searchservice/create-indexer#blob-configuration-parameters) que determinam efetivamente se existe uma condição de erro. A propriedade a seguir pode relaxar as restrições, suprimindo erros que, de outra forma, ocorreriam.

+ `"indexStorageMetadataOnlyForOversizedDocuments"` para indexar metadados de armazenamento para o conteúdo do blob que é muito grande para ser processado. Por padrão, os blobs superdimensionados são tratados como erros. Para limites no tamanho do blob, consulte [limites de serviço](search-limits-quotas-capacity.md).

## <a name="see-also"></a>Consulte também

+ [Indexadores na Pesquisa Cognitiva do Azure](search-indexer-overview.md)
+ [Criar um indexador](search-howto-create-indexers.md)
+ [Visão geral da rericação do ia sobre BLOBs](search-blob-ai-integration.md)
+ [Visão geral da pesquisa sobre BLOBs](search-blob-storage-integration.md)