---
title: Criar um índice de pesquisa no portal do Azure
titleSuffix: Azure Cognitive Search
description: Crie, carregue e consulte o primeiro índice de pesquisa usando o assistente de importação de dados no portal do Azure. Este início rápido usa um conjunto de dados de um hotel fictício para os dados de exemplo.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 03/02/2021
ms.openlocfilehash: 1be165bfe7cca44e8a928933c3c8fe926ad7d4c9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101694828"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-the-azure-portal"></a>Início Rápido: Criar um índice da Pesquisa Cognitiva do Azure no portal do Azure

Crie seu primeiro índice de pesquisa usando o assistente para **Importação de dados** e uma fonte de dados de exemplo interna que consiste em dados fictícios de hotéis. O assistente orientará você durante a criação de um índice de pesquisa (hotels-sample-index) para que você possa escrever consultas interessantes em minutos. 

Embora você não vá usar as opções deste guia de início rápido, o assistente inclui uma página de enriquecimento de IA, de modo que você possa extrair o texto e a estrutura de arquivos de imagem e texto não estruturado. Para obter um passo a passo semelhante que inclui o enriquecimento de IA, confira [Guia de início rápido: Criar um conjunto de habilidades cognitivas](cognitive-search-quickstart-blob.md).

## <a name="prerequisites"></a>Pré-requisitos

+ Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/).

+ Um serviço Pesquisa Cognitiva do Azure (qualquer camada, qualquer região). [Crie um serviço](search-create-service-portal.md) ou [localize um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na assinatura atual. É possível usar um serviço gratuito para este início rápido. 

### <a name="check-for-space"></a>Verificar o espaço

Muitos clientes começam com o serviço gratuito. A camada gratuita é limitada a três índices, três fontes de dados e três indexadores. Verifique se há espaço para itens extras antes de começar. Este tutorial cria uma unidade de cada objeto.

Verifique a página Visão geral do serviço para descobrir quantos índices, indexadores e fontes de dados você já tem. 

:::image type="content" source="media/search-get-started-portal/tiles-indexers-datasources.png" alt-text="Listas de índices, indexadores e fontes de dados":::

## <a name="create-an-index-and-load-data"></a><a name="create-index"></a> Criar um índice e carregar dados

As consultas de pesquisa de iteram em um [*índice*](search-what-is-an-index.md) que contém dados e metadados pesquisáveis e construções adicionais que otimizam certos comportamentos de pesquisa.

Para este tutorial, usamos um conjunto de dados interno de exemplo que pode ser rastreado com um [*indexador*](search-indexer-overview.md) via [assistente **Importar dados**](search-import-data-portal.md). Um indexador é um rastreador específico de fonte que pode ler metadados e conteúdo de fontes de dados do Azure que tenham suporte. Normalmente, os indexadores são usados de forma programática, mas no portal, você pode acessá-los por meio do assistente para **Importação de dados**. 

### <a name="step-1---start-the-import-data-wizard-and-create-a-data-source"></a>Etapa 1 – Iniciar o assistente para Importação de dados e criar uma fonte de dados

1. Entre no [portal do Azure](https://portal.azure.com/) com sua conta do Azure.

1. [Encontre seu serviço de pesquisa](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) e, na página Visão Geral, clique em **Importar dados** na barra de comandos para criar e preencher um índice de pesquisa.

   :::image type="content" source="media/search-get-started-portal/import-data-cmd.png" alt-text="Comando Importar de dados":::

1. No assistente, clique em **Conectar-se aos seus dados** > **Exemplos** > **hotels-sample**. Essa fonte de dados é interna. Se você estiver criando sua própria fonte de dados, precisará especificar um nome, um tipo e as informações de conexão. Depois de criada, ela se torna uma "fonte de dados existente" que pode ser reutilizada em outras operações de importação.

   :::image type="content" source="media/search-get-started-portal/import-datasource-sample.png" alt-text="Selecionar o conjunto de dados de exemplo":::

1. Continue para a próxima página.

### <a name="step-2---skip-the-enrich-content-page"></a>Etapa 2 – Pular a página "Enriquecer conteúdo"

O assistente dá suporte à criação de um [pipeline de enriquecimento de IA](cognitive-search-concept-intro.md) para incorporar os algoritmos de IA dos Serviços Cognitivos na indexação. 

Ignoraremos esta etapa por enquanto e passaremos diretamente para **Personalizar o índice de destino**.

   :::image type="content" source="media/search-get-started-portal/skip-cog-skill-step.png" alt-text="Ignorar etapa de habilidades cognitivas":::

> [!TIP]
> Você pode percorrer um exemplo de indexação de inteligência artificial em um [guia de início rápido](cognitive-search-quickstart-blob.md) ou [tutorial](cognitive-search-tutorial-blob.md).

### <a name="step-3---configure-index"></a>Etapa 3 – Configurar o índice

Para o índice interno de exemplo de hotéis, um esquema de índice padrão foi definido para você. Com exceção de alguns exemplos de filtro avançado, as consultas na documentação e nos exemplos que visam o índice de exemplos de hotel serão executadas nesta definição de índice:

:::image type="content" source="media/search-get-started-portal/hotelsindex.png" alt-text="Índice de hotéis gerado":::

Normalmente, em um exercício baseado em código, a criação de índice é concluída antes do carregamento de dados. O assistente de importação de dados condensa essas etapas gerando um índice básico para qualquer fonte de dados que possa ser rastreada. No mínimo, um índice requer um nome e uma coleção de campos; um dos campos deve estar marcado como a chave do documento para identificar cada documento de forma exclusiva. Além disso, será possível especificar analisadores de linguagem ou sugestores se você desejar obter o preenchimento automático ou consultas sugeridas.

Campos têm atributos e tipos de dados. As caixas de seleção na parte superior são *atributos de índice* que controlam como o campo é usado.

+ **Recuperável** significa que ele aparece na lista de resultados da pesquisa. Você pode marcar campos individuais como fora dos limites para os resultados da pesquisa ao desmarcar essa caixa de seleção, por exemplo, para campos usados somente em expressões de filtro.
+ **Chave** é o identificador exclusivo do documento. É sempre uma cadeia de caracteres e é obrigatório.
+ **Filtrável**, **Classificável** e **Com faceta** determinam se os campos são usados em um filtro, uma classificação ou uma estrutura de faceted navigation.
+ **Pesquisável** significa que um campo é incluído na pesquisa de texto completo. As cadeias de caracteres são pesquisáveis. Campos numéricos e boolianos geralmente são marcados como não pesquisáveis.

Os requisitos de armazenamento não variam como resultado de sua seleção. Por exemplo, se você definir o atributo **Recuperável** em vários campos, os requisitos de armazenamento não aumentarão.

Por padrão, o assistente procura na fonte de dados identificadores exclusivos como base para o campo de chave. As *cadeias de caracteres* são atribuídas como **Recuperáveis** e **Pesquisáveis**. Os *inteiros* são atribuídos como **Recuperáveis**, **Filtráveis**, **Classificáveis** e **Com Faceta**.

1. Aceite os padrões.

   Se você executar o assistente uma segunda vez usando uma fonte de dados de hotéis existente, o índice não será configurado com os atributos padrão. Você precisará selecionar manualmente os atributos em importações futuras. 

1. Continue para a próxima página.

### <a name="step-4---configure-indexer"></a>Etapa 4 – Configurar o indexador

Ainda no assistente **Importar dados**, clique em **Indexador** > **Nome** e digite um nome para o indexador.

Esse objeto define um processo executável. Você poderá colocá-lo em uma agenda recorrente, mas, por enquanto, use a opção padrão para executar o indexador uma vez, imediatamente.

Clique em **Enviar** para criar e executar simultaneamente o indexador.

  :::image type="content" source="media/search-get-started-portal/hotels-indexer.png" alt-text="indexador de hotéis":::

## <a name="monitor-progress"></a>Monitorar o progresso

O assistente levará você para a lista Indexadores, na qual você poderá monitorar o progresso. Para a autonavegação, acesse a página Visão Geral e clique na guia **Indexadores**.

Podem ser necessários alguns minutos para que o portal atualize a página, mas você deverá ver o indexador recém-criado na lista, com o status indicando "em andamento" ou êxito, juntamente com o número de documentos indexados.

   :::image type="content" source="media/search-get-started-portal/indexers-inprogress.png" alt-text="Mensagem de andamento do indexador":::

## <a name="view-the-index"></a>Exibir índice

A página Visão geral do serviço fornece links para os recursos criados no serviço Pesquisa Cognitiva do Azure.  Para exibir o índice que você acabou de criar, clique em **Índices** na lista de links. 

Aguarde a atualização da página do portal. Após alguns minutos, você deverá ver o índice com uma contagem de documentos e tamanho de armazenamento.

   :::image type="content" source="media/search-get-started-portal/indexes-list.png" alt-text="Lista de índices no painel de serviço":::

Nesta lista, você pode clicar no índice *hotels-sample* que acabou de criar e exibir o esquema de índice. e, opcionalmente, adicionar novos campos. 

A guia **Campos** mostra o esquema de índice. Se você estiver gravando consultas e precisar verificar se um campo pode ser filtrado ou classificado, essa guia mostrará os atributos.

Role até a parte inferior da lista para inserir um novo campo. Embora você sempre possa criar um novo campo, na maioria dos casos, não é possível alterar os campos existentes. Os campos existentes têm uma representação física no serviço de pesquisa e, portanto, não podem ser modificados, nem mesmo no código. Para alterar fundamentalmente um campo existente, crie um índice, removendo o original.

   :::image type="content" source="media/search-get-started-portal/sample-index-def.png" alt-text="exemplo de definição de índice":::

Outros constructos, como perfis de pontuação e opções de CORS, podem ser adicionados a qualquer momento.

Para entender claramente o que é possível editar ou não durante o design de índice, reserve um minuto para ver as opções de definição de índice. Opções esmaecidas são um indicador de que um valor não pode ser modificado ou excluído. 

## <a name="query-using-search-explorer"></a><a name="query-index"></a> Consulta usando o Search Explorer

Agora você terá um índice de pesquisa que está pronto para consulta na página de consulta interna [**Gerenciador de pesquisa**](search-explorer.md). Ele fornece uma caixa de pesquisa para que você possa testar cadeias de caracteres de consulta arbitrárias.

O **Gerenciador de pesquisa** só é capaz de lidar com [Solicitações da API REST](/rest/api/searchservice/search-documents), mas aceita a sintaxe de [consulta simples](/rest/api/searchservice/simple-query-syntax-in-azure-search) e [completa do analisador de consulta Lucene](/rest/api/searchservice/lucene-query-syntax-in-azure-search), além de todos os parâmetros de pesquisa disponíveis nas operações da [API REST de pesquisa de documento](/rest/api/searchservice/search-documents#bkmk_examples).

> [!TIP]
> As etapas a seguir são demonstradas no momento 6m08s do [vídeo Visão geral da Pesquisa Cognitiva do Azure](https://channel9.msdn.com/Events/Connect/2016/138).
>

1. Clique em **Gerenciador de pesquisa** na barra de comandos.

   :::image type="content" source="media/search-get-started-portal/search-explorer-cmd.png" alt-text="Comando Search Explorer":::

1. Na lista suspensa **Índice**, escolha *hotels-sample-index*. Clique na lista suspensa **Versão da API** para ver quais APIs REST estão disponíveis. Para as consultas a seguir, use a versão em disponibilidade geral (30/06/2020).

   :::image type="content" source="media/search-get-started-portal/search-explorer-changeindex.png" alt-text="Comandos de índice e API":::

1. Na barra de pesquisa, cole as cadeias de consulta abaixo e clique em **Pesquisar**.

   :::image type="content" source="media/search-get-started-portal/search-explorer-query-string-example.png" alt-text="Botão de pesquisa e de cadeia de consulta":::

## <a name="example-queries"></a>Consultas de exemplo

Você pode inserir termos e frases, semelhante ao que poderá fazer em uma pesquisa do Bing ou do Google, ou expressões de consulta totalmente especificadas. Os resultados são retornados como documentos JSON detalhados.

### <a name="simple-query-with-top-n-results"></a>Consulta simples com os primeiros N resultados

#### <a name="example-string-query-searchspa"></a>Exemplo (consulta de cadeia de caracteres): `search=spa`

+ O parâmetro **search** é usado para inserir uma palavra-chave de pesquisa de texto completo, neste caso, retornando dados de hotéis que contêm *spa* em qualquer campo pesquisável no documento.

+ O **Search Explorer** retorna os resultados em JSON, que é detalhado e difícil de ler quando os documentos têm uma estrutura densa. Isso é intencional; a visibilidade de todo o documento é importante para fins de desenvolvimento, especialmente durante o teste. Para uma melhor experiência de usuário, você precisará escrever código que [trate os resultados](search-pagination-page-layout.md) para destacar elementos importantes.

+ Os documentos contêm todos os campos marcados como recuperáveis no índice. Para exibir atributos de índice no portal, clique em *hotels-sample* na lista **Índices**.

#### <a name="example-parameterized-query-searchspacounttruetop10"></a>Exemplo (consulta parametrizada): `search=spa&$count=true&$top=10`

+ O símbolo **&** é usado para acrescentar os parâmetros de pesquisa, que podem ser especificados em qualquer ordem.

+ O parâmetro **$count = true** retorna a contagem total de todos os documentos retornados. Esse valor aparece próximo ao início dos resultados da pesquisa. Você pode verificar consultas de filtro monitorando alterações relatadas por **$count=true**. Contagens menores indicam que seu filtro está funcionando.

+ O **$top=10** retorna o 10 documentos com maior classificação do total. Por padrão, a Pesquisa Cognitiva do Azure retorna as primeiras 50 melhores correspondências. Você pode aumentar ou diminuir a quantidade via **$top**.

### <a name="filter-the-query"></a><a name="filter-query"></a> Filtrar a consulta

Os filtros são incluídos nas solicitações de pesquisa quando você acrescenta o parâmetro **$filter**. 

#### <a name="example-filtered-searchbeachfilterrating-gt-4"></a>Exemplo (filtrado): `search=beach&$filter=Rating gt 4`

+ O parâmetro **$filter** retorna resultados que correspondem aos critérios fornecidos. Nesse caso, classificações maiores que 4.

+ A sintaxe de filtro é uma construção de OData. Para saber mais, confira [Sintaxe de filtro OData](/rest/api/searchservice/odata-expression-syntax-for-azure-search).

### <a name="facet-the-query"></a><a name="facet-query"></a> Facetar a consulta

Os filtros de faceta estão incluídos em solicitações de pesquisa. Você pode usar o parâmetro facet para retornar uma contagem agregada de documentos que correspondam a um valor de faceta que você fornecer.

#### <a name="example-faceted-with-scope-reduction-searchfacetcategorytop2"></a>Exemplo (facetado com redução de escopo): `search=*&facet=Category&$top=2`

+ **search=** * é uma pesquisa vazia. Pesquisas vazias pesquisam tudo. Um motivo de envio de uma consulta vazia é fazer a filtragem ou faceta no conjunto completo de documentos. Por exemplo, você deseja que uma estrutura de navegação de facetas contenha todos os hotéis no índice.
+ **facet** retorna uma estrutura de navegação que você pode passar para um controle de interface do usuário. Ela retorna categorias e uma contagem. Nesse caso, as categorias são baseadas em um campo convenientemente denominado *Categoria*. Não há nenhuma agregação na Pesquisa Cognitiva do Azure, mas você pode aproximar a agregação com `facet`, que retorna uma contagem de documentos em cada categoria.

+ **$top=2** recupera dois documentos, ilustrando a que você pode usar `top` para reduzir ou aumentar os resultados.

#### <a name="example-facet-on-numeric-values-searchspafacetrating"></a>Exemplo (faceta em valores numéricos): `search=spa&facet=Rating`

+ Essa consulta é a faceta para a classificação em uma pesquisa de texto por *spa*. O termo *Classificação* pode ser especificado como uma faceta porque o campo é marcado como recuperável, filtrável e com faceta no índice e os valores que ele contém (numérico, 1 a 5) são adequados para categorizar as listagens em grupos.

+ Somente campos filtráveis podem ser facetados. Somente os campos recuperáveis podem ser retornados nos resultados.

+ O campo *Classificação* é um ponto flutuante de precisão dupla e o agrupamento será pelo valor preciso. Para obter mais informações sobre o agrupamento por intervalo (por exemplo, "classificações de 3 estrelas," "classificações de 4 estrelas" etc.), confira [Como implementar a navegação facetada na Pesquisa Cognitiva do Azure](./search-faceted-navigation.md#filter-based-on-a-range).

### <a name="highlight-search-results"></a><a name="highlight-query"></a> Realçar resultados da pesquisa

Realce de ocorrências refere-se à formatação de texto correspondentes à palavra-chave, considerando que existam correspondências em um campo específico. Se o termo de pesquisa estiver escondido em uma descrição, você poderá adicionar o realce de ocorrências para facilitar a localização.

#### <a name="example-highlighter-searchbeachhighlightdescription"></a>Exemplo (marca-texto): `search=beach&highlight=Description`

+ Neste exemplo, a palavra formatada *praia* é mais fácil de identificar no campo de descrição.

#### <a name="example-linguistic-analysis-searchbeacheshighlightdescription"></a>Exemplo (análise linguística): `search=beaches&highlight=Description`

+ A pesquisa de texto completo reconhece variações básicas nos formatos de palavra. Nesse caso, os resultados da pesquisa contêm texto destacado para "praia", para hotéis que têm essa palavra em seus campos pesquisáveis, em resposta a uma pesquisa pela palavra-chave "praias". Formas diferentes da mesma palavra podem aparecer nos resultados devido a análise linguística. 

+ A Pesquisa Cognitiva do Azure dá suporte a 56 analisadores da Lucene e da Microsoft. O padrão usado pela Pesquisa Cognitiva do Azure é o analisador Lucene padrão.

### <a name="try-fuzzy-search"></a><a name="fuzzy-search"></a> Experimentar pesquisa difusa

Por padrão, termos de consulta com grafia incorreta, como *seatle* para a cidade de Seattle, não retornam correspondências em uma pesquisa típica. O exemplo a seguir não retorna resultados.

#### <a name="example-misspelled-term-unhandled-searchseatle"></a>Exemplo (termo digitado incorretamente, sem tratamento): `search=seatle`

Para lidar com erros de ortografia, você pode usar a pesquisa difusa. A pesquisa difusa é habilitada quando você usa a sintaxe de consulta Lucene completa, que ocorre quando você faz duas coisas: definir **queryType = full** na consulta e acrescentar o **~** na cadeia de caracteres de pesquisa.

#### <a name="example-misspelled-term-handled-searchseatlequerytypefull"></a>Exemplo (termo digitado incorretamente, com tratamento): `search=seatle~&queryType=full`

Agora, o exemplo retorna documentos que incluem correspondências com "Seattle".

Quando **queryType** for especificado, o analisador de consulta simples padrão será usado. O analisador de consulta simples é mais rápido, mas se você precisar de pesquisa difusa, expressões regulares, pesquisa por proximidade ou outros tipos de consulta avançada, será necessário obter a sintaxe completa.

A pesquisa difusa e a pesquisa curinga têm implicações nos resultados da pesquisa. A análise linguística não é executada nesses formatos de consulta. Antes de usar pesquisas difusa e curinga, reveja [Como a pesquisa de texto completa funciona na Pesquisa Cognitiva do Azure](search-lucene-query-architecture.md#stage-2-lexical-analysis) e procure a seção sobre exceções à análise léxica.

Para saber mais sobre cenários de consulta habilitados pelo analisador de consulta completo, confira [Sintaxe de consulta Lucene na Pesquisa Cognitiva do Azure](/rest/api/searchservice/lucene-query-syntax-in-azure-search).

### <a name="try-geospatial-search"></a><a name="geo-search"></a> Experimentar a pesquisa geoespacial

A pesquisa geográfica tem suporte pelo [tipo de dados edm.GeographyPoint](/rest/api/searchservice/supported-data-types) em um campo que contém coordenadas. A pesquisa geográfica é um tipo de filtro, especificado na [sintaxe do filtro OData](/rest/api/searchservice/odata-expression-syntax-for-azure-search).

#### <a name="example-geo-coordinate-filters-searchcounttruefiltergeodistancelocationgeographypoint-12212-4767-le-5"></a>Exemplo (filtros de coordenadas geográficas): `search=*&$count=true&$filter=geo.distance(Location,geography'POINT(-122.12 47.67)') le 5`

O exemplo de consulta filtra os dados posicionais de todos os resultados e os resultados ficam a menos de 5 km de um determinado ponto (especificado como coordenadas de latitude e longitude). Ao adicionar **$count**, você poderá ver quantos resultados são retornados quando se altera a distância ou as coordenadas.

A pesquisa geográfica é útil se seu aplicativo de pesquisa tem um recurso de “encontrar nas proximidades” ou usa a navegação de mapa. Entretanto, ela não é uma pesquisa de texto completo. Se você tiver requisitos de usuário para pesquisar em uma cidade ou país/região por nome, adicione campos que contêm nomes de cidade ou país/região, além de coordenadas.

## <a name="takeaways"></a>Observações

Este tutorial forneceu uma rápida introdução à Pesquisa Cognitiva do Azure usando o portal do Azure.

Você aprendeu a criar um índice de pesquisa usando o assistente **Importar dados**. Você aprendeu sobre [indexadores](search-indexer-overview.md) e sobre o fluxo de trabalho básico para criar índices, incluindo [modificações com suporte em um índice publicado](/rest/api/searchservice/update-index).

Usando o **explorador do Search** no portal do Azure, você aprendeu algumas sintaxes de consulta básicas por meio de exemplos práticos que demonstraram os recursos principais, como filtros, realce de ocorrência, pesquisa difusa e pesquisa geográfica.

Você também aprendeu a encontrar índices, indexadores e fontes de dados no portal. Considerando novas fonte de dados no futuro, você poderá usar o portal para verificar rapidamente suas definições ou coleções de campos com um mínimo de esforço.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando você está trabalhando em sua própria assinatura, é uma boa ideia identificar, no final de um projeto, se você ainda precisa dos recursos criados. Recursos deixados em execução podem custar dinheiro. Você pode excluir os recursos individualmente ou excluir o grupo de recursos para excluir todo o conjunto de recursos.

Você pode localizar e gerenciar recursos no portal usando o link **Todos os recursos** ou **Grupos de recursos** no painel de navegação à esquerda.

Se você estiver usando um serviço gratuito, estará limitado a três índices, indexadores e fontes de dados. Você pode excluir itens individuais no portal para permanecer abaixo do limite. 

## <a name="next-steps"></a>Próximas etapas

Use um assistente do portal para gerar um aplicativo Web pronto para uso executado em um navegador. Você pode experimentar esse assistente no pequeno índice que acabou de criar ou usar um dos conjuntos de dados de exemplo internos para uma experiência de pesquisa mais avançada.

> [!div class="nextstepaction"]
> [Criar um aplicativo de demonstração no portal](search-create-app-portal.md)