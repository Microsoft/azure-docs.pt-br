---
title: "O que é o Azure Search | Microsoft Docs"
description: "O Azure Search é um serviço de pesquisa em nuvem hospedado totalmente gerenciado. Saiba mais nesta visão geral do recurso."
services: search
manager: jhubbard
author: ashmaka
documentationcenter: 
ms.assetid: 50bed849-b716-4cc9-bbbc-b5b34e2c6153
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 06/26/2017
ms.author: ashmaka
ms.openlocfilehash: baf73639eb6506b14d0d3a4de1bf55b66e973b95
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-azure-search"></a>O que é o Azure Search?
O Azure Search é uma solução de pesquisa como serviço na nuvem que oferece aos desenvolvedores APIs e ferramentas para adicionar uma experiência de pesquisa avançada aos dados em aplicativos Web, móveis e empresariais.

A funcionalidade é exposta por meio de uma [API REST](/rest/api/searchservice/) ou um [SDK do .NET](search-howto-dotnet-sdk.md) simples que mascara a complexidade inerente da tecnologia de pesquisa. Além das APIs, o portal do Azure fornece suporte de administração e criação de protótipo. A infraestrutura e a disponibilidade são gerenciadas pela Microsoft.

<a name="feature-drilldown"></a>

## <a name="feature-summary"></a>Resumo de recursos

| Categoria | Recursos |
|----------|----------|
|Análise de texto e pesquisa de texto completo | [**Pesquisa de texto completo**](search-lucene-query-architecture.md) é um caso de uso primário para a maioria dos aplicativos baseados em pesquisa. As consultas podem ser formuladas usando uma sintaxe com suporte: <br/><br/>[**Sintaxe de consulta simples**](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search), que oferece operadores lógicos, operadores de pesquisa de frase, operadores de sufixo e operadores de precedência.<br/><br/>A [**sintaxe de consulta Lucene**](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) oferece todo o suporte de consulta simples, além de pesquisa difusa, pesquisa por proximidade, aumento de termo e expressões regulares.| 
| Integração de dados | Os índices do Azure Search aceitam dados de qualquer fonte, desde que eles sejam enviados como uma estrutura de dados JSON. <br/><br/> Opcionalmente, nas fontes de dados com suporte no Azure, use [**indexadores**](search-indexer-overview.md) para rastrear automaticamente o [Banco de Dados SQL do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), o [Azure Cosmos DB](search-howto-index-documentdb.md) ou o [armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md), a fim de sincronizar o conteúdo do índice de pesquisa com o armazenamento de dados primário. Os indexadores de Blob do Azure podem executar a *decodificação de documentos* para [indexar os principais formatos de arquivo](search-howto-indexing-azure-blob-storage.md), incluindo documentos do Microsoft Office, PDF e HTML. |
| Análise do Search | Os [**analisadores léxicos personalizados**](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search) estão disponíveis para consultas de pesquisa complexas usando a correspondência fonética e expressões regulares. |
| Suporte ao idioma | Os [**analisadores de idioma**](https://docs.microsoft.com/rest/api/searchservice/language-support) do Lucene, bem como os processadores de idioma natural da Microsoft, disponíveis em 56 idiomas diferentes para tratar com inteligência a linguística específica a um idioma, incluindo tempos verbais, gênero, substantivos plurais irregulares (por exemplo, “mouse” vs. “mice”), decomposição de palavras, separação de palavras (para idiomas sem espaços) e muito mais. |
| Pesquisa geográfica | O Azure Search com inteligência processa, filtra e exibe as localizações geográficas. Ele permite aos usuários explorar dados com base na proximidade de um resultado da pesquisa a uma localização física. [Assista a este vídeo](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data) ou [examine esta amostra](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) para saber mais. |
| Recursos da experiência do usuário | [**Sugestões de pesquisa**](https://docs.microsoft.com/rest/api/searchservice/suggesters) podem ser habilitadas para consultas de preenchimento automático em uma barra de pesquisa. Documentos reais no índice são sugeridos conforme os usuários inserem a entrada de pesquisa parcial. <br/><br/>A [**navegação facetada**](https://docs.microsoft.com/azure/search/search-faceted-navigation) é habilitada por meio de um único parâmetro de consulta. O Azure Search retorna uma estrutura de navegação mista, que você pode usar como o código por trás de uma lista de categorias para a filtragem autodirigida (por exemplo, para filtrar os itens do catálogo por faixa de preços ou marca). <br/><br/> Os [**filtros**](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) podem ser usados para incorporar a navegação facetada na interface do usuário do aplicativo, melhorar a formulação da consulta e filtrar com base em critérios especificados pelo usuário ou pelo desenvolvedor. Crie filtros usando a sintaxe OData.<br/><br/> O [**realce de ocorrência**](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) aplica um formulário visual a uma palavra-chave correspondente nos resultados da pesquisa. Você pode escolher quais campos retornam os trechos de código destacados.<br/><br/>A [**classificação**](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) é oferecida em vários campos por meio do esquema de índice e, então, alternada no momento da consulta com um único parâmetro de pesquisa.<br/><br/> A [**paginação**](search-pagination-page-layout.md) e limitação dos resultados da pesquisa são simples com o controle bem ajustado que o Azure Search oferece sobre os resultados da pesquisa.  
| Relevância | A [**pontuação simples**](/rest/api/searchservice/add-scoring-profiles-to-a-search-index) é o principal benefício do Azure Search. Os perfis de pontuação são usados para modelar a relevância como uma função de valores nos próprios documentos. Por exemplo, talvez você queira que produtos mais recentes ou com desconto apareçam primeiro nos resultados da pesquisa. Você também pode criar perfis de pontuação usando marcas de pontuação personalizadas com base nas preferências de pesquisa do cliente que você controlou e armazenou separadamente. |
| Monitoramento e emissão de relatórios | A [**análise de tráfego de pesquisa**](search-traffic-analytics.md) é coletada e analisada para revelar novas informações sobre o que os usuários estão digitando na caixa de pesquisa. <br/><br/>As métricas nas consultas por segundo, latência e limitação são capturadas e informadas nas páginas do portal sem nenhuma configuração adicional necessária. Você também pode monitorar facilmente o índice e as contagens de documentos para poder ajustar a capacidade quando necessário. Para obter mais informações, consulte [Administração do serviço](search-manage.md) |
| Ferramentas para criação de protótipos e inspeção | No portal, use o [**Assistente para importação de dados**](search-import-data-portal.md) para configurar indexadores, o designer de índices para criar um índice e o [**Search Explorer**](search-explorer.md) para testar as consultas e refinar os perfis de pontuação. Também é possível abrir qualquer índice para exibir seu esquema. |
| Infraestrutura | A **plataforma altamente disponível** garante uma experiência de serviço de pesquisa extremamente confiável. Quando dimensionada corretamente, o [Azure Search oferece um SLA de 99,9%](https://azure.microsoft.com/support/legal/sla/search/v1_0/).<br/><br/> **Totalmente gerenciado e escalonável** como uma solução de ponta a ponta, o Azure Search não exige nenhum gerenciamento de infraestrutura. Seu serviço pode ser adaptado facilmente às suas necessidades ajustando as duas dimensões para lidar com mais armazenamento de documentos, maiores cargas de consulta ou ambos.

## <a name="how-it-works"></a>Como ele funciona
### <a name="step-1-provision-service"></a>Etapa 1: Provisionar serviço
Você pode criar um serviço do Azure Search no [portal do Azure](https://portal.azure.com/) ou por meio da [API de Gerenciamento de Recursos do Azure](/rest/api/searchmanagement/). É possível escolher o serviço gratuito compartilhado com outros assinantes ou uma [camada paga](https://azure.microsoft.com/pricing/details/search/) que dedica os recursos utilizados apenas pelo seu serviço. Para as camadas pagas, você pode dimensionar um serviço em duas dimensões: 

- Adicionar Réplicas para aumentar a capacidade de manipular cargas de consulta pesadas.   
- Adicionar Partições para aumentar o armazenamento para mais documentos. 

Ao manipular o armazenamento de documentos e a taxa de transferência de consultas separadamente, é possível calibrar a alocação de recursos de acordo com os requisitos de produção.

### <a name="step-2-create-index"></a>Etapa 2: Criar índice
Antes de carregar o conteúdo pesquisável, primeiro é necessário definir um índice do Azure Search. Um índice é como uma tabela de banco de dados que contém os dados e pode aceitar consultas de pesquisa. Você define o esquema de índice a ser mapeado para refletir a estrutura dos documentos que você deseja pesquisar, semelhante a campos em um banco de dados.

Um esquema pode ser criado no portal do Azure ou de forma programática usando o [SDK do .NET](search-howto-dotnet-sdk.md) ou a [API REST](/rest/api/searchservice/).

### <a name="step-3-index-data"></a>Etapa 3: Indexar dados
Depois de definir um índice, você estará pronto para carregar o conteúdo. É possível usar um modelo push ou pull.

O modelo pull recupera dados de fontes de dados externas. Há suporte para ele por meio de *indexadores* que simplificam e automatizam aspectos da ingestão de dados, como se conectar a dados, lê-los ou serializá-los. Os [indexadores](/rest/api/searchservice/Indexer-operations) estão disponíveis para o Azure Cosmos DB, Banco de Dados SQL do Azure, Armazenamento de Blobs do Azure e SQL Server hospedado em uma VM do Azure. É possível configurar um indexador para uma atualização de dados sob demanda ou agendada.

O modelo de push é fornecido por meio do SDK ou APIs REST usados para enviar documentos atualizados para um índice. Você pode enviar dados de praticamente qualquer conjunto de dados usando o formato JSON. Confira [Adicionar, atualizar ou excluir Documentos](/rest/api/searchservice/addupdate-or-delete-documents) ou [Como usar o SDK do .NET)](search-howto-dotnet-sdk.md) para obter orientação sobre como carregar dados.

### <a name="step-4-search"></a>Etapa 4: Pesquisar
Depois de popular um índice, você pode [emitir consultas de pesquisa](/rest/api/searchservice/Search-Documents) para o ponto de extremidade de serviço usando solicitações HTTP simples com a API REST ou o SDK do .NET.

## <a name="how-it-compares"></a>Como ele se compara

Os clientes costumam perguntar sobre as semelhanças da [pesquisa de texto completo do Azure Search](search-lucene-query-architecture.md) com a [pesquisa de texto completo](https://en.wikipedia.org/wiki/Full_text_search) de seus produtos de banco de dados. Nossa resposta é que as funcionalidades de linguagem do Azure Search são mais sofisticadas e mais flexíveis, com suporte para consultas Lucene, analisadores de linguagem Lucene e Microsoft, analisadores personalizados para entradas fonéticas ou outras entradas especializadas e a capacidade de mesclar dados de várias fontes no índice de pesquisa. 

Outro ponto de inflexão é que uma solução de pesquisa aborda toda a experiência de pesquisa. Por exemplo, você poderá desejar ter uma pontuação personalizada de resultados, navegação facetada para a filtragem autodirecionada, realce de ocorrências e sugestões de consulta de digitação antecipada. 

As ferramentas para o monitoramento e entendimento das atividades de consulta também podem influenciar uma decisão de solução de pesquisa. Por exemplo, o Azure Search dá suporte à [análise de tráfego de pesquisa](search-traffic-analytics.md) para métricas na taxa de clickthrough, nas principais pesquisas, nas pesquisas sem cliques e assim por diante. Nos produtos em que a pesquisa é um complemento, é pouco provável que você encontre esses recursos.

Outra consideração é a utilização de recursos. Em geral, a pesquisa em idioma natural é computacionalmente intensiva. Alguns de nossos clientes descarregaram as operações de pesquisa no Azure Search como uma maneira de preservar os recursos do computador para o processamento de transações. Ao externalizar a pesquisa, é possível dimensionar facilmente para encontrar uma correspondência ao volume da consulta.

Depois de decidir usar uma pesquisa dedicada, sua próxima decisão será usar um serviço de nuvem ou um servidor local. Um serviço de nuvem é a escolha certa se você deseja uma [solução completa com sobrecarga e manutenção mínimas e escala ajustável](#cloud-service-advantage).

No paradigma da nuvem, diversos provedores oferecem recursos de linha de base comparáveis, com a pesquisa de texto completo, pesquisa geográfica e a capacidade de lidar com algum nível de ambiguidade nas entradas de pesquisa. Normalmente, é um [recurso especializado](#feature-drilldown) ou a facilidade e simplicidade geral das APIs, ferramentas e gerenciamento que determinam o melhor ajuste.

Entre os provedores de nuvem, o Azure Search é mais forte para cargas de trabalho de pesquisa de texto completo em relação aos repositórios de conteúdo e bancos de dados do Azure, para aplicativos que se baseiam principalmente na pesquisa para recuperação de informações e navegação de conteúdo. As principais vantagens incluem:

+ Integração dos dados do Azure (rastreadores) na camada de indexação
+ Portal do Azure para o gerenciamento central
+ Escala do Azure, confiabilidade e disponibilidade superior
+ Análise linguística e personalizada, com analisadores para a pesquisa sólida de texto completo em 56 idiomas
+ [Principais recursos comuns aos aplicativos centrados em pesquisa](#feature-drilldown): pontuação, facetamento, sugestões, sinônimos, pesquisa geográfica e outros.

> [!Note]
> Para ser claro, as fontes de dados não Azure têm suporte completo, mas se baseiam em uma metodologia de push com uso mais intensivo de código, em vez dos indexadores. Com nossas APIs, você pode direcionar qualquer coleção de documentos JSON para um índice do Azure Search.

Entre nossos clientes, aqueles que conseguem aproveitar a mais ampla gama de recursos do Azure Search incluem catálogos online, programas de linha de negócios e aplicativos de descoberta de documentos.

## <a name="rest-api--net-sdk"></a>API REST | SDK .Net

Embora várias tarefas possam ser executadas no portal, o Azure Search se destina aos desenvolvedores que desejam integrar a funcionalidade de pesquisa nos aplicativos existentes. As seguintes interfaces de programação estão disponíveis.

|Plataforma |Descrição |
|-----|------------|
|[REST](/rest/api/searchservice/) | Comandos HTTP suportados por qualquer plataforma de programação e linguagem, incluindo Xamarin, Java e JavaScript|
|[SDK .NET](search-howto-dotnet-sdk.md) | O wrapper do .NET para a API REST oferece uma codificação eficiente no C# e outras linguagens de código gerenciado destinadas ao .NET Framework |

## <a name="free-trial"></a>Avaliação gratuita
Os assinantes do Azure podem [provisionar um serviço na camada gratuita](search-create-service-portal.md).

Se você não for um assinante, poderá [abrir uma conta do Azure gratuitamente](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F). Você obtém créditos para experimentar os serviços pagos do Azure. Depois que eles forem utilizados, você poderá manter a conta e usar os [serviços gratuitos do Azure](https://azure.microsoft.com/free/). Seu cartão de crédito nunca será cobrado, a menos que você altere explicitamente suas configurações, solicitando esse tipo de cobrança.

Se preferir, você pode [ativar benefícios para assinantes do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): todos os meses, sua assinatura do MSDN concede créditos que podem ser usados para serviços pagos do Azure. 

## <a name="how-to-get-started"></a>Como começar

1. Crie um serviço na [Camada gratuita](search-create-service-portal.md).

2. Execute em etapas um ou mais dos tutoriais a seguir. 

  + [Como usar o SDK do .NET](search-howto-dotnet-sdk.md) demonstra as principais etapas no código gerenciado.  
  + [Introdução à API REST](https://github.com/Azure-Samples/search-rest-api-getting-started) mostra as mesmas etapas usando a API REST.  
  + [Criar seu primeiro índice no portal](search-get-started-portal.md) demonstra os recursos internos de indexação e protótipo do portal.   

Os mecanismos de pesquisa são os drivers comuns de recuperação de informações nos aplicativos móveis, na Web e nos armazenamentos de dados corporativos. O Azure Search fornece ferramentas para criar uma experiência de pesquisa semelhante aos grandes sites comerciais.

Neste vídeo de 9 minutos com o gerente de programa Liam Cavanagh, saiba como a integração de um mecanismo de pesquisa pode trazer vantagens para seu aplicativo. As demonstrações breves abrangem os principais recursos do Azure Search e a aparência de um fluxo de trabalho típico. 

>[!VIDEO https://channel9.msdn.com/Events/Connect/2016/138/player]
 
+ O vídeo de 0-3 minutos aborda os principais recursos e casos de uso.
+ O vídeo de 3-4 minutos aborda o provisionamento de serviços. 
+ O vídeo de 4-6 minutos aborda o Assistente para Importar Dados usado para criar um índice com o conjunto de dados interno de imóveis.
+ O vídeo de 6-9 minutos aborda o Gerenciador de Pesquisa e várias consultas.


