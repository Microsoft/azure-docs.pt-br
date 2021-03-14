---
title: Criar um indexador
titleSuffix: Azure Cognitive Search
description: Defina as propriedades em um indexador para determinar a origem dos dados e os destinos. Você pode definir parâmetros para modificar os comportamentos de tempo de execução.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/28/2021
ms.openlocfilehash: 596eca0d73ffc4a590fae9b346658a2c31a1d68c
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101676479"
---
# <a name="creating-indexers-in-azure-cognitive-search"></a>Criando indexadores no Azure Pesquisa Cognitiva

Um indexador de pesquisa fornece um fluxo de trabalho automatizado para transferir documentos e conteúdo de uma fonte de dados externa para um índice de pesquisa em seu serviço de pesquisa. Como originalmente criado, ele extrai o texto e os metadados de uma fonte de dados do Azure, serializa os documentos em JSON e transmite os documentos resultantes para um mecanismo de pesquisa para indexação. Desde então, foi estendido dar suporte ao [enriquecimento de ia](cognitive-search-concept-intro.md) para processamento profundo de conteúdo. 

O uso de indexadores reduz significativamente a quantidade e a complexidade do código que você precisa escrever. Este artigo se concentra na mecânica de criação de um indexador como preparação para um trabalho mais avançado com indexadores específicos de origem e [habilidades](cognitive-search-working-with-skillsets.md).

## <a name="whats-an-indexer-definition"></a>O que é uma definição de indexador?

Os indexadores são usados para indexação baseada em texto que obtém o conteúdo alfanumérico de campos de origem em campos de índice ou processamento baseado em ia que analisa o texto não diferenciado para a estrutura ou analisa imagens para texto e informações, além de adicionar esse conteúdo a um índice. As definições de índice a seguir são típicas do que você pode criar para qualquer um dos cenários.

### <a name="indexers-for-text-content"></a>Indexadores para conteúdo de texto

A finalidade original de um indexador era simplificar o processo complexo de carregar um índice fornecendo um mecanismo para conectar e ler texto e conteúdo numérico de campos em uma fonte de dados, serializar esse conteúdo como documentos JSON e entregar esses documentos ao mecanismo de pesquisa para indexação. Esse ainda é um caso de uso primário e, para essa operação, você precisará criar um indexador com as propriedades definidas no exemplo a seguir.

```json
{
  "name": (required) String that uniquely identifies the indexer,
  "dataSourceName": (required) String indicated which existing data source to use,
  "targetIndexName": (required) String,
  "parameters": {
    "batchSize": null,
    "maxFailedItems": null,
    "maxFailedItemsPerBatch": null
  },
  "fieldMappings": [ optional unless there are field discrepancies that need resolution]
}
```

As **`name`** **`dataSourceName`** Propriedades,, e **`targetIndexName`**  são necessárias e, dependendo de como você cria o indexador, a fonte de dados e o índice já devem existir no serviço antes que você possa executar o indexador. 

A **`parameters`** propriedade modifica comportamentos de tempo de execução, como quantos erros aceitar antes de falhar todo o trabalho. Os parâmetros também são como você especifica comportamentos específicos de origem. Por exemplo, se a origem for um armazenamento de BLOBs, você poderá definir um parâmetro que filtra em extensões de arquivo: `"parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }` .

A **`field mappings`** propriedade é usada para mapear explicitamente os campos de origem para destino se esses campos forem diferentes por nome ou tipo. Outras propriedades (não mostradas), são usadas para [especificar uma agenda](search-howto-schedule-indexers.md), criar o indexador em um estado desabilitado ou especificar uma [chave de criptografia](search-security-manage-encryption-keys.md) para a criptografia complementar dos dados em repouso.

### <a name="indexers-for-ai-indexing"></a>Indexadores para indexação de ia

Como os indexadores são o mecanismo pelo qual um serviço de pesquisa faz solicitações de saída, os indexadores foram estendidos para dar suporte a aprimoramentos de ia, adicionando infraestrutura e objetos para implementar esse caso de uso.

Todas as propriedades e parâmetros acima se aplicam a indexadores que executam enriquecimento de ia. As propriedades a seguir são específicas para o enriquecimento de ia: **`skillSets`** , **`outputFieldMappings`** , **`cache`** (somente visualização e REST). 

```json
{
  "name": (required) String that uniquely identifies the indexer,
  "dataSourceName": (required) String, name of an existing data source,
  "targetIndexName": (required) String, name of an existing index,
  "skillsetName" : (required for AI enrichment) String, name of an existing skillset,
  "cache":  {
    "storageConnectionString" : (required for caching AI enriched content) Connection string to a blob container,
    "enableReprocessing": true
    },
  "parameters": {
    "batchSize": null,
    "maxFailedItems": null,
    "maxFailedItemsPerBatch": null
  },
  "fieldMappings": [],
  "outputFieldMappings" : (required for AI enrichment) { ... },
}
```

O enriquecimento do ia está além do escopo deste artigo. Para obter mais informações, comece com estes artigos: [enriquecimento de ia](cognitive-search-concept-intro.md), [habilidades no Azure pesquisa cognitiva](cognitive-search-working-with-skillsets.md)e criar o constart [(REST)](/rest/api/searchservice/create-skillset).

## <a name="choose-an-indexer-client-and-create-the-indexer"></a>Escolher um cliente do indexador e criar o indexador

Quando você estiver pronto para criar um indexador em um serviço de pesquisa remota, será necessário um cliente de pesquisa na forma de uma ferramenta, como portal do Azure ou postmaster, ou código que instancia um cliente do indexador. Recomendamos as APIs portal do Azure ou REST para desenvolvimento antecipado e teste de prova de conceito.

### <a name="permissions"></a>Permissões

Todas as operações relacionadas a indexadores, incluindo solicitações GET para status ou definições, exigem uma [chave de API de administração](search-security-api-keys.md) na solicitação.

### <a name="limits"></a>limites

Todas as [camadas de serviço limitam](search-limits-quotas-capacity.md#indexer-limits) o número de objetos que você pode criar. Se você estiver experimentando a camada gratuita, só poderá ter três objetos de cada tipo e 2 minutos de processamento do indexador (não incluindo o processamento do Configurador de habilidades).

### <a name="use-azure-portal-to-create-an-indexer"></a>Usar portal do Azure para criar um indexador

O portal fornece duas opções para criar um indexador: [**Assistente de importação de dados**](search-import-data-portal.md) e **novo indexador** que fornece campos para especificar uma definição de indexador. O assistente é exclusivo, pois ele cria todos os elementos necessários. Outras abordagens exigem que você predefina uma fonte de dados e um índice.

A captura de tela a seguir mostra onde você pode encontrar esses recursos no Portal. 

  :::image type="content" source="media/search-howto-create-indexers/portal-indexer-client.png" alt-text="indexador de hotéis" border="true":::

### <a name="use-a-rest-client"></a>Usar um cliente REST

O postmaster e o Visual Studio Code (com uma extensão para o Azure Pesquisa Cognitiva) podem funcionar como um cliente do indexador. Usando qualquer ferramenta, você pode se conectar ao serviço de pesquisa e enviar solicitações [de criação de indexador (REST)](/rest/api/searchservice/create-indexer) . Há vários tutoriais e exemplos que demonstram clientes REST para a criação de objetos. 

Comece com um destes artigos para saber mais sobre cada cliente:

+ [Criar um índice de pesquisa usando REST e o postmaster](search-get-started-rest.md)
+ [Introdução ao Visual Studio Code e ao Azure Cognitive Search](search-get-started-vs-code.md)

Consulte as [operações do indexador (REST)](/rest/api/searchservice/Indexer-operations) para obter ajuda com as solicitações do indexador formular.

### <a name="use-an-sdk"></a>Usar um SDK

Por Pesquisa Cognitiva, os SDKs do Azure implementam recursos geralmente disponíveis. Dessa forma, você pode usar qualquer um dos SDKs para criar objetos relacionados ao indexador. Todos eles fornecem um **SearchIndexerClient** que tem métodos para criar indexadores e objetos relacionados, incluindo habilidades.

| SDK do Azure | Cliente | Exemplos |
|-----------|--------|----------|
| .NET | [SearchIndexerClient](/dotnet/api/azure.search.documents.indexes.searchindexerclient) | [DotNetHowToIndexers](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) |
| Java | [SearchIndexerClient](/java/api/com.azure.search.documents.indexes.searchindexerclient) | [CreateIndexerExample. java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexerExample.java) |
| JavaScript | [SearchIndexerClient](/javascript/api/@azure/search-documents/searchindexerclient) | [Indexadores](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexers) |
| Python | [SearchIndexerClient](/python/api/azure-search-documents/azure.search.documents.indexes.searchindexerclient) | [sample_indexers_operations. py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexers_operations.py) |

## <a name="run-the-indexer"></a>Executar o indexador

Um indexador é executado automaticamente quando você cria o indexador no serviço. Esse é o momento de verdade em que você descobrirá se há erros de conexão de fonte de dados, problemas de mapeamento de campo ou problemas de conjunto de qualificações. 

Há várias maneiras de executar um indexador:

+ Envie uma solicitação HTTP para [criar o indexador](/rest/api/searchservice/create-indexer) ou [atualizar o indexador](/rest/api/searchservice/update-indexer) para adicionar ou alterar a definição e executar o indexador.

+ Envie uma solicitação HTTP para [executar o indexador](/rest/api/searchservice/run-indexer) para executar um indexador sem alterações na definição.

+ Execute um programa que chama os métodos SearchIndexerClient para criar, atualizar ou executar.

> [!NOTE]
> Para evitar executar imediatamente um indexador após a criação, inclua **`disabled=true`** na definição do indexador.

Como alternativa, coloque o indexador [em um agendamento](search-howto-schedule-indexers.md) para invocar o processamento em intervalos regulares. 

O processamento agendado geralmente coincide com a necessidade de indexação incremental do conteúdo alterado. A lógica de detecção de alteração é uma funcionalidade incorporada às plataformas de origem. As alterações em um contêiner de blob são detectadas automaticamente pelo indexador. Para obter orientação sobre como aproveitar a detecção de alterações em outras fontes de dados, consulte os documentos do indexador para fontes de dados específicas:

+ [Banco de Dados SQL do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md)
+ [Armazenamento de Tabelas do Azure](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](search-howto-index-cosmosdb.md)

## <a name="change-detection-and-indexer-state"></a>Alterar estado de detecção e indexador

Os indexadores podem detectar alterações nos dados subjacentes e processar apenas documentos novos ou atualizados em cada execução de indexador. Por exemplo, se o status do indexador indicar que uma execução foi bem-sucedida com `0/0` documentos processados, significa que o indexador não encontrou nenhuma linha ou BLOBs novos ou alterados na fonte de dados subjacente.

Como um indexador dá suporte à detecção de alteração varia de acordo com a fonte de dados:

+ Armazenamento de BLOBs do Azure, armazenamento de tabelas do Azure e Azure Data Lake Storage Gen2 carimbar cada BLOB ou atualização de linha com uma data e hora. Os vários indexadores usam essas informações para determinar quais documentos atualizar no índice. A detecção de alteração interna significa que um indexador pode reconhecer documentos novos e atualizados, sem a necessidade de configuração adicional de sua parte.

+ O Azure SQL e o Cosmos DB fornecem recursos de detecção de alterações em suas plataformas. Você pode especificar a política de detecção de alteração em sua definição de fonte de dados.

Para grandes cargas de indexação, um indexador também controla o último documento processado por meio de uma "marca d' água alta" interna. O marcador nunca é exposto na API, mas, internamente, o indexador controla onde ele parou. Quando a indexação é retomada, por meio de uma execução agendada ou de uma invocação sob demanda, o indexador faz referência à marca d' água alta para que possa continuar de onde parou.

Se você precisar limpar a marca d' água alta para reindexar novamente, você poderá usar [Redefinir indexador](/rest/api/searchservice/reset-indexer). Para fazer uma reindexação mais seletiva, use [Redefinir habilidades](/rest/api/searchservice/preview-api/reset-skills) ou [Redefinir documentos](/rest/api/searchservice/preview-api/reset-documents). Por meio das APIs de redefinição, você pode limpar o estado interno e também liberar o cache se tiver habilitado o [enriquecimento incremental](search-howto-incremental-index.md). Para obter mais informações e a comparação de cada opção de redefinição, consulte [executar ou redefinir indexadores, habilidades e documentos](search-howto-run-reset-indexers.md).

## <a name="know-your-data"></a>Conheça seus dados

Os indexadores esperam um conjunto de linhas tabulares, onde cada linha se torna um documento de pesquisa completo ou parcial no índice. Muitas vezes, há uma correspondência um-para-um entre uma linha e o documento de pesquisa resultante, onde todos os campos na linha são preenchidos completamente em cada documento. Mas você pode usar indexadores para gerar apenas parte de um documento, por exemplo, se você estiver usando vários indexadores ou abordagens para criar o índice. 

Para mesclar dados relacionais em um conjunto de linhas, você deve criar um modo SQL ou criar uma consulta que retorne os registros pai e filho na mesma linha. Por exemplo, o conjunto de dados interno de exemplos de hotéis é um banco de dados SQL que tem registros 50 (um para cada hotel), vinculados a registros de sala em uma tabela relacionada. A consulta que achata os dados coletivos em um conjunto de linhas incorpora todas as informações de sala em documentos JSON em cada registro de Hotel. As informações de sala inseridas são geradas por uma consulta que usa uma cláusula **for JSON auto** . Você pode aprender mais sobre essa técnica em [definir uma consulta que retorna JSON inserido](index-sql-relational-data.md#define-a-query-that-returns-embedded-json). Este é apenas um exemplo; Você pode encontrar outras abordagens que produzirão o mesmo efeito.

Além dos dados simplificados, é importante efetuar pull somente de dados pesquisáveis. Os dados pesquisáveis são alfanuméricos. Pesquisa Cognitiva não pode pesquisar em dados binários em nenhum formato, embora possa extrair e inferir descrições de texto de arquivos de imagem (consulte o [enriquecimento de ai](cognitive-search-concept-intro.md)) para criar conteúdo pesquisável. Da mesma forma, usando o enriquecimento de ia, o texto grande pode ser analisado por modelos de linguagem natural para localizar a estrutura ou informações relevantes, gerando novo conteúdo que você pode adicionar a um documento de pesquisa.

Considerando que os indexadores não corrigem problemas de dados, outras formas de limpeza ou manipulação de dados podem ser necessárias. Para obter mais informações, consulte a documentação do produto do seu [produto de banco de dados do Azure](../index.yml?product=databases).

## <a name="know-your-index"></a>Saber seu índice

Lembre-se de que os indexadores passam os documentos de pesquisa para o mecanismo de pesquisa para indexação. Assim como os indexadores têm propriedades que determinam o comportamento da execução, um esquema de índice tem propriedades que afetam de forma prodeterminada como as cadeias de caracteres são indexadas (somente cadeias de caracteres são analisadas e indexadas). Dependendo das atribuições do analisador, as cadeias de caracteres indexadas podem ser diferentes do que você passou. Você pode avaliar os efeitos de analisadores usando o [texto de análise (REST)](/rest/api/searchservice/test-analyzer). Para obter mais informações sobre analisadores, consulte [analisadores para processamento de texto](search-analyzers.md).

Em termos de como os indexadores interagem com um índice, um indexador verifica apenas os nomes e tipos de campo. Não há nenhuma etapa de validação que garanta que o conteúdo de entrada esteja correto para o campo de pesquisa correspondente no índice. Como uma etapa de verificação, você pode executar consultas no índice preenchido que retornam documentos inteiros ou campos selecionados. Para obter mais informações sobre como consultar o conteúdo de um índice, consulte [criar uma consulta básica](search-query-create.md).

## <a name="next-steps"></a>Próximas etapas

+ [Indexadores do agendamento](search-howto-schedule-indexers.md)
+ [Definir mapeamentos de campo](search-indexer-field-mappings.md)
+ [Monitorar o status do indexador](search-howto-monitor-indexers.md)
+ [Conectar usando identidades gerenciadas](search-howto-managed-identities-data-sources.md)