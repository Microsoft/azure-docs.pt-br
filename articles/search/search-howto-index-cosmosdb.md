---
title: Pesquisar Azure Cosmos DB dados
titleSuffix: Azure Cognitive Search
description: Importar dados de Azure Cosmos DB para um índice pesquisável no Pesquisa Cognitiva do Azure. Os indexadores automatizam a ingestão de dados para fontes de dados selecionadas, como o Azure Cosmos DB.
author: mgottein
manager: nitinme
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/11/2020
ms.openlocfilehash: 563edae0292062e1ed7f216c69aeeb84ef0fa7a8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98119468"
---
# <a name="how-to-index-cosmos-db-data-using-an-indexer-in-azure-cognitive-search"></a>Como indexar dados do Cosmos DB usando um indexador no Azure Cognitive Search 

> [!IMPORTANT] 
> A API do SQL está geralmente disponível.
> A API do MongoDB, a API Gremlin e o suporte a API do Cassandra estão atualmente em visualização pública. A funcionalidade de versão prévia é fornecida sem um Contrato de Nível de Serviço e, portanto, não é recomendada para cargas de trabalho de produção. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Você pode solicitar acesso às visualizações preenchendo [este formulário](https://aka.ms/azure-cognitive-search/indexer-preview). 
> [As versões de visualização da API REST](search-api-preview.md) fornecem esses recursos. Atualmente, há suporte limitado ao portal e não há suporte para o SDK do .NET.

> [!WARNING]
> Somente as coleções Cosmos DB com uma [política de indexação](../cosmos-db/index-policy.md) definida como [consistente](../cosmos-db/index-policy.md#indexing-mode) são suportadas pelo Azure pesquisa cognitiva. A indexação de coleções com uma política de indexação lenta não é recomendada e pode resultar em dados ausentes. Não há suporte para coleções com indexação desabilitadas.

Este artigo mostra como configurar um [indexador](search-indexer-overview.md) Azure Cosmos DB para extrair conteúdo e torná-lo pesquisável no Azure pesquisa cognitiva. Este fluxo de trabalho cria um índice de Pesquisa Cognitiva do Azure e o carrega com o texto existente extraído do Azure Cosmos DB. 

Como a terminologia pode ser confusa, vale a pena observar que [Azure Cosmos DB indexação](../cosmos-db/index-overview.md) e a [indexação do Azure pesquisa cognitiva](search-what-is-an-index.md) são operações distintas, exclusivas para cada serviço. Antes de iniciar a indexação de Pesquisa Cognitiva do Azure, seu banco de dados Azure Cosmos DB já deve existir e conter dados.

O indexador Cosmos DB no Azure Pesquisa Cognitiva pode rastrear [itens de Azure Cosmos DB](../cosmos-db/account-databases-containers-items.md#azure-cosmos-items) acessados por meio de protocolos diferentes. 

+ Para a [API do SQL](../cosmos-db/sql-query-getting-started.md), que está em disponibilidade geral, você pode usar o [portal](#cosmos-indexer-portal), a [API REST](/rest/api/searchservice/indexer-operations)ou o [SDK do .net](/dotnet/api/azure.search.documents.indexes.models.searchindexer) para criar a fonte de dados e o indexador.

+ Para a [API do MongoDB (visualização)](../cosmos-db/mongodb-introduction.md), você pode usar o [portal](#cosmos-indexer-portal) ou a [API REST versão 2020-06-30-Preview](search-api-preview.md) para criar a fonte de dados e o indexador.

+ Para [API do Cassandra (visualização)](../cosmos-db/cassandra-introduction.md) e [API Gremlin (versão prévia)](../cosmos-db/graph-introduction.md), você só pode usar a [API REST versão 2020-06-30-Preview](search-api-preview.md) para criar a fonte de dados e o indexador.


> [!Note]
> Você pode transmitir um voto na voz do usuário para o [API de tabela](https://feedback.azure.com/forums/263029-azure-search/suggestions/32759746-azure-search-should-be-able-to-index-cosmos-db-tab) se quiser vê-lo com suporte no Azure pesquisa cognitiva.
>

<a name="cosmos-indexer-portal"></a>

## <a name="use-the-portal"></a>Usar o portal

> [!Note]
> Atualmente, o portal dá suporte à API do SQL e à API do MongoDB (versão prévia).

O método mais fácil para indexação de Azure Cosmos DB itens é usar um assistente na [portal do Azure](https://portal.azure.com/). Por amostragem de dados e leitura de metadados no contêiner, o assistente de [**importação de dados**](search-import-data-portal.md) no Azure pesquisa cognitiva pode criar um índice padrão, mapear campos de origem para campos de índice de destino e carregar o índice em uma única operação. Dependendo do tamanho e complexidade da fonte de dados, é possível ter um índice de pesquisa de texto completo operando em minutos.

É recomendável usar a mesma região ou local para o Azure Pesquisa Cognitiva e Azure Cosmos DB para latência mais baixa e para evitar encargos de largura de banda.

### <a name="1---prepare-source-data"></a>1- Preparar dados de origem

Você deve ter uma conta de Cosmos DB, um banco de dados Azure Cosmos DB mapeado para a API do SQL, API do MongoDB (versão prévia) ou API Gremlin (versão prévia) e conteúdo no banco de dados.

Certifique-se de que seu banco de dados do Cosmos DB contenha dado. O [Assistente de importação de dados](search-import-data-portal.md) lê metadados e executa a amostragem de dados para inferir um esquema de índice, mas também carrega dados de Cosmos DB. Se os dados estiverem ausentes, o assistente parará com esse erro "erro ao detectar o esquema de índice da fonte de dados: não foi possível compilar um índice de protótipo porque a fonte de dados ' emptycollection ' não retornou nenhum dado".

### <a name="2---start-import-data-wizard"></a>2 - Iniciar o Assistente para Importação de Dados 

Você pode [iniciar o assistente](search-import-data-portal.md) na barra de comandos na página do serviço pesquisa cognitiva do Azure ou, se estiver se conectando ao cosmos DB API do SQL, você pode clicar em **Adicionar Azure pesquisa cognitiva** na seção **configurações** do painel de navegação esquerdo da sua conta do cosmos DB.

   ![Comando importar dados no portal](./media/search-import-data-portal/import-data-cmd2.png "Iniciar o assistente Importar dados")

### <a name="3---set-the-data-source"></a>3 - Configurar a fonte de dados

Na página **fonte de dados** , a origem deve ser **Cosmos DB**, com as seguintes especificações:

+ **Nome** é o nome do objeto de fonte de dados. Depois de criado, você pode escolher para outras cargas de trabalho.

+ **Cosmos DB conta** deve estar em um dos seguintes formatos:
    1. A cadeia de conexão primária ou secundária de Cosmos DB com o seguinte formato: `AccountEndpoint=https://<Cosmos DB account name>.documents.azure.com;AccountKey=<Cosmos DB auth key>;` .
        + Para as **coleções do MongoDB** da versão 3,2 e 3,6, use o seguinte formato para a conta de Cosmos DB no portal do Azure: `AccountEndpoint=https://<Cosmos DB account name>.documents.azure.com;AccountKey=<Cosmos DB auth key>;ApiKind=MongoDb`
        + Para **gráficos Gremlin e tabelas Cassandra**, Inscreva-se na [visualização do indexador restrito](https://aka.ms/azure-cognitive-search/indexer-preview) para obter acesso à visualização e informações sobre como formatar as credenciais.
    1.  Uma cadeia de conexão de identidade gerenciada com o seguinte formato que não inclui uma chave de conta: `ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.DocumentDB/databaseAccounts/<your cosmos db account name>/;(ApiKind=[api-kind];)` . Para usar esse formato de cadeia de conexão, siga as instruções para [Configurar uma conexão de indexador com um banco de dados Cosmos DB usando uma identidade gerenciada](search-howto-managed-identities-cosmos-db.md).

+ **Banco** de dados é um banco de dados existente da conta. 

+ A **coleção** é um contêiner de documentos. Os documentos devem existir para que a importação seja realizada com sucesso. 

+ A **consulta** poderá ficar em branco se você quiser todos os documentos, caso contrário, poderá inserir uma consulta que selecione um subconjunto de documentos. A **consulta** está disponível somente para a API do SQL.

   ![Cosmos DB definição de fonte de dados](media/search-howto-index-cosmosdb/cosmosdb-datasource.png "Cosmos DB definição de fonte de dados")

### <a name="4---skip-the-enrich-content-page-in-the-wizard"></a>4-ignorar a página "conteúdo enriquecer" no assistente

Adicionar habilidades cognitivas (ou enriquecimento) não é um requisito de importação. A menos que você tenha uma necessidade específica de [Adicionar o enriquecimento de ia](cognitive-search-concept-intro.md) ao seu pipeline de indexação, ignore esta etapa.

Para ignorar a etapa, clique nos botões azuis na parte inferior da página para "Avançar" e "ignorar".

### <a name="5---set-index-attributes"></a>5- Definir atributos de índice

Na página **índice**, deve haver uma lista de campos com um tipo de dados e uma série de caixas de seleção para definir os atributos de índice. O assistente pode gerar uma lista de campos com base em metadados e por amostragem dos dados de origem. 

Você pode selecionar atributos em massa clicando na caixa de seleção na parte superior de uma coluna de atributo. Escolha **recuperável** e **pesquisável** para cada campo que deve ser retornado a um aplicativo cliente e sujeito ao processamento de pesquisa de texto completo. Você observará que inteiros não são de texto completo ou de pesquisa difusa (os números são avaliados de forma idêntica e geralmente são úteis em filtros).

Examine a descrição de [atributos de índice](/rest/api/searchservice/create-index#bkmk_indexAttrib) e [analisadores de idioma](/rest/api/searchservice/language-support) para obter mais informações. 

Reserve um tempo para revisar suas seleções. Depois de executar o assistente, estruturas de dados físicos são criadas e você não poderá editar esses campos sem descartar e recriar todos os objetos.

   ![Definição de índice de Cosmos DB](media/search-howto-index-cosmosdb/cosmosdb-index-schema.png "Definição de índice de Cosmos DB")

### <a name="6---create-indexer"></a>6 - Criar indexador

Totalmente especificado, o assistente cria três objetos diferentes em seu serviço de pesquisa. Um objeto de fonte de dados e um objeto de índice são salvos como recursos nomeados em seu serviço de Pesquisa Cognitiva do Azure. A última etapa cria um objeto do indexador. Dar um nome ao indexador permite que ele exista como um recurso autônomo, que pode ser agendado e gerenciado independentemente do objeto de fonte de dados e índice criado na mesma sequência do assistente.

Se você não estiver familiarizado com indexadores, um *indexador* é um recurso no Azure pesquisa cognitiva que rastreia uma fonte de dados externa para conteúdo pesquisável. A saída do assistente de **importação de dados** é um indexador que rastreia sua fonte de dados Cosmos DB, extrai conteúdo pesquisável e importa-o para um índice no Azure pesquisa cognitiva.

A captura de tela a seguir mostra a configuração padrão do indexador. Você pode alternar para **uma vez** se quiser executar o indexador uma vez. Clique em **Enviar** para executar o assistente e criar todos os objetos. A indexação começa imediatamente.

   ![Cosmos DB definição do indexador](media/search-howto-index-cosmosdb/cosmosdb-indexer.png "Cosmos DB definição do indexador")

Pode-se monitorar a importação de dados nas páginas do portal. As notificações de progresso indicam o status da indexação e quantos documentos são carregados. 

Quando a indexação estiver concluída, você pode usar o [Search explorer](search-explorer.md) para consultar seu índice.

> [!NOTE]
> Se você não vir os dados esperados, talvez seja necessário definir mais atributos em mais campos. Exclua o índice e o indexador que você acabou de criar e percorra o assistente novamente, modificando suas seleções de atributos de índice na etapa 5. 

<a name="cosmosdb-indexer-rest"></a>

## <a name="use-rest-apis"></a>Usar APIs REST

Você pode usar a API REST para indexar Azure Cosmos DB dados, seguindo um fluxo de trabalho de três partes comum a todos os indexadores no Azure Pesquisa Cognitiva: criar uma fonte de dados, criar um índice, criar um indexador. A extração de dados do Cosmos DB ocorre quando você envia a solicitação criar indexador. Depois que essa solicitação for concluída, você terá um índice passível de consulta. 

> [!NOTE]
> Para indexação de dados de Cosmos DB API Gremlin ou Cosmos DB API do Cassandra você deve primeiro solicitar acesso às visualizações controladas preenchendo [este formulário](https://aka.ms/azure-cognitive-search/indexer-preview). Depois que sua solicitação for processada, você receberá instruções sobre como usar a [API REST versão 2020-06-30-Preview](search-api-preview.md) para criar a fonte de dados.

No início deste artigo, é mencionado que [Azure Cosmos DB indexação](../cosmos-db/index-overview.md) e indexação de [indexação do Azure pesquisa cognitiva](search-what-is-an-index.md) são operações distintas. Para a indexação de Cosmos DB, por padrão, todos os documentos são indexados automaticamente, exceto pelo API do Cassandra. Se você desativar a indexação automática, os documentos poderão ser acessados somente por meio de seus links automáticos ou por consultas usando a ID do documento. A indexação de Pesquisa Cognitiva do Azure requer que Cosmos DB indexação automática seja ativada na coleção que será indexada pelo Azure Pesquisa Cognitiva. Ao se inscrever para a versão prévia do indexador Cosmos DB API do Cassandra, você receberá instruções sobre como configurar a indexação de Cosmos DB.

> [!WARNING]
> O Azure Cosmos DB é a próxima geração do DocumentDB. Anteriormente, com a versão **2017-11-11** da API, você poderia usar a `documentdb` sintaxe. Isso significa que você pode especificar o tipo de fonte de dados como `cosmosdb` ou `documentdb` . A partir da versão de API **2019-05-06** , o portal e as APIs de pesquisa cognitiva do Azure só dão suporte à `cosmosdb` sintaxe conforme instruído neste artigo. Isso significa que o tipo de fonte de dados deve `cosmosdb` se você quiser se conectar a um ponto de extremidade Cosmos DB.

### <a name="1---assemble-inputs-for-the-request"></a>1-montar entradas para a solicitação

Para cada solicitação, você deve fornecer o nome do serviço e a chave de administração para o Azure Pesquisa Cognitiva (no cabeçalho da POSTAgem) e o nome da conta de armazenamento e a chave para o armazenamento de BLOBs. Você pode usar o [postmaster](search-get-started-rest.md) ou o [Visual Studio Code](search-get-started-vs-code.md) para enviar solicitações HTTP para pesquisa cognitiva do Azure.

Copie os quatro valores a seguir no bloco de notas para que você possa colá-los em uma solicitação:

+ Nome do serviço de Pesquisa Cognitiva do Azure
+ Chave de administração de Pesquisa Cognitiva do Azure
+ Cosmos DB cadeia de conexão

Você pode encontrar esses valores no Portal:

1. Nas páginas do portal para Pesquisa Cognitiva do Azure, copie a URL do serviço de pesquisa da página Visão geral.

2. No painel de navegação esquerdo, clique em **chaves** e copie a chave primária ou secundária (elas são equivalentes).

3. Alterne para as páginas do portal para sua conta de armazenamento do cosmos. No painel de navegação à esquerda, em **configurações**, clique em **chaves**. Esta página fornece um URI, dois conjuntos de cadeias de conexão e dois conjuntos de chaves. Copie uma das cadeias de conexão para o bloco de notas.

### <a name="2---create-a-data-source"></a>2-criar uma fonte de dados

A **fonte de dados** especifica os dados no índice, nas credenciais e nas políticas para identificação de alterações nos dados (como documentos modificados ou excluídos em sua coleção). A fonte de dados é definida como um recurso independente para que possa ser usada por vários indexadores.

Para criar uma fonte de dados, formule uma solicitação POST:

```http

    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mycosmosdbdatasource",
        "type": "cosmosdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCollection", "query": null },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        }
    }
```

O corpo da solicitação contém a definição da fonte de dados, que deve incluir os seguintes campos:

| Campo   | Descrição |
|---------|-------------|
| **name** | Obrigatórios. Escolha qualquer nome para representar o objeto da fonte de dados. |
|**tipo**| Obrigatórios. Deve ser `cosmosdb`. |
|**credentials** | Obrigatórios. Deve seguir o formato de cadeia de conexão Cosmos DB ou um formato de cadeia de conexão de identidade gerenciada.<br/><br/>Para **coleções SQL**, cadeias de conexão podem seguir qualquer um dos formatos abaixo: <li>`AccountEndpoint=https://<Cosmos DB account name>.documents.azure.com;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>`<li>Uma cadeia de conexão de identidade gerenciada com o seguinte formato que não inclui uma chave de conta: `ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.DocumentDB/databaseAccounts/<your cosmos db account name>/;` . Para usar esse formato de cadeia de conexão, siga as instruções para [Configurar uma conexão de indexador com um banco de dados Cosmos DB usando uma identidade gerenciada](search-howto-managed-identities-cosmos-db.md).<br/><br/>Para as **coleções do MongoDB** da versão 3,2 e 3,6, use qualquer um dos seguintes formatos para a cadeia de conexão: <li>`AccountEndpoint=https://<Cosmos DB account name>.documents.azure.com;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>;ApiKind=MongoDb`<li>Uma cadeia de conexão de identidade gerenciada com o seguinte formato que não inclui uma chave de conta: `ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.DocumentDB/databaseAccounts/<your cosmos db account name>/;ApiKind=MongoDb;` . Para usar esse formato de cadeia de conexão, siga as instruções para [Configurar uma conexão de indexador com um banco de dados Cosmos DB usando uma identidade gerenciada](search-howto-managed-identities-cosmos-db.md).<br/><br/>Para **gráficos Gremlin e tabelas Cassandra**, Inscreva-se na [visualização do indexador restrito](https://aka.ms/azure-cognitive-search/indexer-preview) para obter acesso à visualização e informações sobre como formatar as credenciais.<br/><br/>Evite números de porta na url do ponto de extremidade. Se você incluir o número da porta, o Azure Pesquisa Cognitiva não poderá indexar o banco de dados Azure Cosmos DB.|
| **contêiner** | Contém os seguintes elementos: <br/>**nome**: Obrigatórios. Especifique a ID da coleção do banco de dados a ser indexada.<br/>**query**: Opcional. Você pode especificar uma consulta para nivelar um documento JSON arbitrário, criando um esquema nivelado que o Azure Cognitive Search possa indexar.<br/>Para a API do MongoDB, API do Gremlin e API do Cassandra, não há suporte para consultas. |
| **dataChangeDetectionPolicy** | Recomendável. Consulte a seção [Indexando documentos alterados](#DataChangeDetectionPolicy).|
|**dataDeletionDetectionPolicy** | Opcional. Consulte a seção [Indexando documentos excluídos](#DataDeletionDetectionPolicy).|

### <a name="using-queries-to-shape-indexed-data"></a>Usando consultas para formatar dados indexados
É possível especificar uma consulta do SQL para mesclar propriedades ou matrizes aninhadas, projetar propriedades JSON e filtrar os dados a serem indexados. 

> [!WARNING]
> Não há suporte para consultas personalizadas para **API do MongoDB**, **api Gremlin** e **API do Cassandra**: o `container.query` parâmetro deve ser definido como nulo ou omitido. Se você precisar usar uma consulta personalizada, informe em [Voz do Usuário](https://feedback.azure.com/forums/263029-azure-search).

Documento de exemplo:

```http
    {
        "userId": 10001,
        "contact": {
            "firstName": "andy",
            "lastName": "hoh"
        },
        "company": "microsoft",
        "tags": ["azure", "cosmosdb", "search"]
    }
```

Filtrar consulta:

```sql
SELECT * FROM c WHERE c.company = "microsoft" and c._ts >= @HighWaterMark ORDER BY c._ts
```

Consulta de mesclagem:

```sql
SELECT c.id, c.userId, c.contact.firstName, c.contact.lastName, c.company, c._ts FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts
```

Consulta de projeção:

```sql
SELECT VALUE { "id":c.id, "Name":c.contact.firstName, "Company":c.company, "_ts":c._ts } FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts
```

Consulta de mesclagem de matriz:

```sql
SELECT c.id, c.userId, tag, c._ts FROM c JOIN tag IN c.tags WHERE c._ts >= @HighWaterMark ORDER BY c._ts
```

### <a name="3---create-a-target-search-index"></a>3-criar um índice de pesquisa de destino 

[Crie um índice de pesquisa cognitiva de destino do Azure](/rest/api/searchservice/create-index) se você ainda não tiver um. O exemplo a seguir cria um índice com um campo de ID e descrição:

```http
    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
    Content-Type: application/json
    api-key: [Search service admin key]

    {
       "name": "mysearchindex",
       "fields": [{
         "name": "id",
         "type": "Edm.String",
         "key": true,
         "searchable": false
       }, {
         "name": "description",
         "type": "Edm.String",
         "filterable": false,
         "sortable": false,
         "facetable": false,
         "suggestions": true
       }]
     }
```

Verifique se o esquema do índice de destino é compatível com o esquema dos documentos JSON de origem ou com a saída da projeção de consulta personalizada.

> [!NOTE]
> Para coleções particionadas, a chave de documento padrão é Azure Cosmos DB `_rid` propriedade, que o Azure pesquisa cognitiva automaticamente renomear como `rid` porque os nomes de campo não podem começar com um caractere de sublinhado. Além disso, Azure Cosmos DB `_rid` valores contêm caracteres que são inválidos nas chaves de pesquisa cognitiva do Azure. Por esse motivo, os valores `_rid` são codificados em Base64.
> 
> Para coleções do MongoDB, o Azure Pesquisa Cognitiva renomeia a `_id` Propriedade automaticamente como `id` .  

### <a name="mapping-between-json-data-types-and-azure-cognitive-search-data-types"></a>Mapeamento entre tipos de dados JSON e tipos de dados do Azure Pesquisa Cognitiva
| Tipo de dados JSON | Tipos de campos de índice de destino compatíveis |
| --- | --- |
| Bool |Edm.Boolean, Edm.String |
| Números que se parecem com inteiros |Edm.Int32, Edm.Int64, Edm.String |
| Números que se parecem com pontos flutuantes |Edm.Double, Edm.String |
| String |Edm.String |
| Matrizes de tipos primitivos, por exemplo, [“a”, “b”, “c”] |Collection(Edm.String) |
| Cadeias de caracteres que se parecem com datas |Edm.DateTimeOffset, Edm.String |
| Objetos GeoJSON, por exemplo, { "type": "Point", "coordinates": [long, lat] } |Edm.GeographyPoint |
| Outros objetos JSON |N/D |

### <a name="4---configure-and-run-the-indexer"></a>4-configurar e executar o indexador

Uma vez que o índice e a fonte de dados forem criados, será possível criar o indexador:

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "mycosmosdbindexer",
      "dataSourceName" : "mycosmosdbdatasource",
      "targetIndexName" : "mysearchindex",
      "schedule" : { "interval" : "PT2H" }
    }
```

Esse indexador é executado a cada duas horas (o intervalo de agendamento é definido como "PT2H"). Para executar um indexador a cada 30 minutos, defina o intervalo para "PT30M". O intervalo mais curto com suporte é de 5 minutos. O agendamento é opcional – se ele for omitido, um indexador será executado apenas uma vez quando for criado. No entanto, você pode executar um indexador sob demanda a qualquer momento.   

Para saber mais sobre a API Criar Indexador, veja [Criar indexador](/rest/api/searchservice/create-indexer).

Para obter mais informações sobre como definir agendas do indexador, consulte [como agendar indexadores para o Azure pesquisa cognitiva](search-howto-schedule-indexers.md).

## <a name="use-net"></a>Usar o .NET

O SDK do .NET disponível para o público geral tem paridade total com a API REST disponível. É recomendável examinar a seção anterior da API REST para aprender os conceitos, fluxo de trabalho e requisitos. Consulte a seguinte documentação de referência de API do .NET para implementar um indexador JSON em código gerenciado.

+ [azure.search.documents. indexes. Models. searchindexerdatasourceconnection](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection)
+ [azure.search.documents. indexes. Models. searchindexerdatasourcetype](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourcetype)
+ [azure.search.documents. indexes. Models. SearchIndex](/dotnet/api/azure.search.documents.indexes.models.searchindex)
+ [azure.search.documents. indexes. Models. SearchIndexer](/dotnet/api/azure.search.documents.indexes.models.searchindexer)

<a name="DataChangeDetectionPolicy"></a>

## <a name="indexing-changed-documents"></a>Indexando documentos alterados

A finalidade de uma política de detecção de alteração de dados é identificar de maneira eficaz dados alterados. Atualmente, a única política com suporte é o [`HighWaterMarkChangeDetectionPolicy`](/dotnet/api/azure.search.documents.indexes.models.highwatermarkchangedetectionpolicy) usando a `_ts` Propriedade (timestamp) fornecida pelo Azure Cosmos DB, que é especificado da seguinte maneira:

```http
    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }
```

O uso dessa política é altamente recomendável para garantir o bom desempenho do indexador. 

Se estiver usando uma consulta personalizada, garanta que a propriedade `_ts` será projetada pela consulta.

<a name="IncrementalProgress"></a>

### <a name="incremental-progress-and-custom-queries"></a>Progresso incremental e consultas personalizadas

O progresso incremental durante a indexação garante que, se a execução do indexador é interrompida por falhas transitórias ou limite de tempo de execução, o indexador pode escolher onde ela parou próxima vez que ele é executado, em vez de toda a coleção de zero de índice novamente. Isso é especialmente importante durante a indexação de coleções grandes. 

Para habilitar o progresso incremental usando uma consulta personalizada, certifique-se de que sua consulta classifica os resultados pelo `_ts` coluna. Isso habilita o ponto de verificação periódico que o Azure Pesquisa Cognitiva usa para fornecer o progresso incremental na presença de falhas.   

Em alguns casos, mesmo que a consulta contenha uma `ORDER BY [collection alias]._ts` cláusula, o Azure pesquisa cognitiva pode não inferir que a consulta é ordenada pelo `_ts` . Você pode informar ao Azure Pesquisa Cognitiva que os resultados são ordenados usando a `assumeOrderByHighWaterMarkColumn` propriedade de configuração. Para especificar essa dica, crie ou atualize o indexador da seguinte maneira: 

```http
    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "assumeOrderByHighWaterMarkColumn" : true } }
    } 
```

<a name="DataDeletionDetectionPolicy"></a>

## <a name="indexing-deleted-documents"></a>Indexando documentos excluídos

Quando as linhas são excluídas da coleção, normalmente, você também deseja excluí-las do índice de pesquisa. A finalidade de uma política de detecção de exclusão de dados é identificar de maneira eficaz dados excluídos. Atualmente, a única política com suporte é a política `Soft Delete` (a exclusão recebe algum tipo de marcador), que é especificada da seguinte forma:

```http
    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }
```

Se estiver usando uma consulta personalizada, garanta que a propriedade referenciada por `softDeleteColumnName` é projetada pela consulta.

O seguinte exemplo cria uma fonte de dados com uma política de exclusão reversível:

```http
    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mycosmosdbdatasource",
        "type": "cosmosdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCosmosDbCollectionId" },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        },
        "dataDeletionDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName": "isDeleted",
            "softDeleteMarkerValue": "true"
        }
    }
```

## <a name="next-steps"></a><a name="NextSteps"></a>Próximas etapas

Parabéns! Você aprendeu a integrar o Azure Cosmos DB com o Azure Pesquisa Cognitiva usando um indexador.

* Para saber mais sobre o Azure Cosmos DB, consulte a [página de serviço do Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).
* Para saber mais sobre Pesquisa Cognitiva do Azure, consulte a [página de serviço Search](https://azure.microsoft.com/services/search/).