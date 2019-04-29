---
title: Perguntas frequentes – Azure Search
description: Obtenha respostas para perguntas comuns sobre o Serviço Microsoft Azure Search, um serviço de pesquisa hospedado na nuvem do Microsoft Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 08/03/2017
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 9ea4e4ec78e5613758bd9e5ff7a4fbd3273208c6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61290441"
---
# <a name="azure-search---frequently-asked-questions-faq"></a>Azure Search - FAQ (perguntas frequentes)

 Encontre respostas para perguntas frequentes sobre conceitos, código e cenários relacionados ao Azure Search.

## <a name="platform"></a>Plataforma

### <a name="how-is-azure-search-different-from-full-text-search-in-my-dbms"></a>Como o Azure Search é diferente da pesquisa de texto completo no meu DBMS?

O Azure Search dá suporte a várias fontes de dados, a [análise linguística para vários idiomas](https://docs.microsoft.com/rest/api/searchservice/language-support), a [análises personalizadas para entradas de dados interessantes e incomuns](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search), a controles de classificação por meio de pesquisa [perfis de pontuação](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)e a recursos de experiência do usuário, como digitação antecipada, realce de ocorrências e navegação facetada. Ele também inclui outros recursos, como sinônimos e sintaxe de consulta avançada, mas esses geralmente não são recursos diferenciados.

### <a name="what-is-the-difference-between-azure-search-and-elasticsearch"></a>Qual é a diferença entre o Azure Search e o Elasticsearch?

Ao comparar as tecnologias de pesquisa, os clientes frequentemente solicitam informações específicas sobre como o Azure Search compara com o Elasticsearch. Os clientes que optam pelo Azure Search em vez do Elasticsearch para a pesquisa de seus projetos de aplicativo normalmente o fazem porque facilitamos uma tarefa essencial ou porque eles precisam de integração interna com outras tecnologias da Microsoft:

+ O Azure Search é um serviço de nuvem totalmente gerenciado com SLA (contratos de nível de serviço) de 99,9%, quando provisionados com redundância suficiente (2 réplicas para acesso de leitura, 3 réplicas para leitura e gravação).
+ Os [Processadores de linguagem natural](https://docs.microsoft.com/rest/api/searchservice/language-support) da Microsoft oferecem análise linguística de ponta.  
+ Os [Indexadores do Azure Search](search-indexer-overview.md) podem rastrear uma variedade de fontes de dados do Azure para indexação inicial e incremental.
+ Se você precisa de uma resposta rápida para flutuações na consulta ou nos volumes de indexação, pode usar [controles deslizantes](search-manage.md#scale-up-or-down) no portal do Azure ou executar um [script do PowerShell](search-manage-powershell.md), ignorando o gerenciamento de fragmento diretamente.  
+ Os [Recursos de pontuação e ajuste](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) fornecem os meios para influenciar a pontuação de classificações de pesquisa além do que o mecanismo de pesquisa sozinho pode fornecer.

### <a name="can-i-pause-azure-search-service-and-stop-billing"></a>Posso pausar o serviço Azure Search e interromper a cobrança?

Não é possível pausar o serviço. Recursos de computação e armazenamento são alocados para seu uso exclusivo quando o serviço é criado. Não é possível liberar e recuperar os recursos sob demanda.

## <a name="indexing-operations"></a>Operações de indexação

### <a name="backup-and-restore-or-download-and-move-indexes-or-index-snapshots"></a>Índices de backup e restauração (ou download e mover) ou instantâneos de índice?

Embora você possa [obter uma definição de índice](https://docs.microsoft.com/rest/api/searchservice/get-index) a qualquer momento, não há nenhum recurso de extração de índice, instantâneo ou restauração de backup para baixar um índice *preenchido* em execução na nuvem para um sistema local ou movê-lo para outro serviço Azure Search.

Os índices são criados e preenchidos a partir de código escrito e executado apenas no Azure Search na nuvem. Normalmente, os clientes que desejam mover um índice para outro serviço o fazem editando o código para usar um novo ponto de extremidade e executando novamente a indexação. Se você deseja ter a capacidade de criar um instantâneo ou fazer backup de um índice, vote no [User Voice](https://feedback.azure.com/forums/263029-azure-search/suggestions/8021610-backup-snapshot-of-index).

### <a name="can-i-restore-my-index-or-service-once-it-is-deleted"></a>Posso restaurar meu índice ou serviço depois que ele for excluído?

Não, você não pode restaurar índices ou serviços. Se você excluir um índice do Azure Search, a operação será final e o índice não poderá ser recuperado. Quando você exclui um serviço de Pesquisa do Azure, todos os índices no serviço são excluídos permanentemente. Além disso, se você excluir um grupo de recursos do Azure que contenha um ou mais serviços de pesquisa do Azure, todos os serviços serão excluídos permanentemente.  

Restaurar recursos, como índices, indexadores, fontes de dados e qualificações, exige que você os recrie a partir do código. No caso de índices, você deve reindexar dados de fontes externas. Por esse motivo, é altamente recomendável manter uma cópia mestra ou backup dos dados originais em outro repositório de dados, como o Banco de Dados SQL do Azure ou o Banco de Dados Cosmos.

### <a name="can-i-index-from-sql-database-replicas-applies-to-azure-sql-database-indexershttpsdocsmicrosoftcomazuresearchsearch-howto-connecting-azure-sql-database-to-azure-search-using-indexers"></a>Posso indexar a partir das réplicas de banco de dados SQL? (Aplica-se a [indexadores de Banco de Dados SQL do Azure](https://docs.microsoft.com/azure/search/search-howto-connecting-azure-sql-database-to-azure-search-using-indexers).)

Não há nenhuma restrição quanto ao uso de réplicas primárias ou secundárias como fonte de dados na criação de um índice do zero. No entanto, a atualização de um índice com atualizações incrementais (com base em registros modificados) exige a réplica primária. Esse requisito vem do Banco de Dados SQL, que garante o controle de alterações somente em réplicas primárias. Se você tentar usar réplicas secundárias para uma carga de trabalho de atualização do índice, não há nenhuma garantia de que obterá todos os dados.

## <a name="search-operations"></a>Operações de pesquisa

### <a name="can-i-search-across-multiple-indexes"></a>Posso pesquisar em vários índices?

Não, essa operação não tem suporte. A pesquisa sempre está no escopo para um único índice.

### <a name="can-i-restrict-search-corpus-access-by-user-identity"></a>Posso restringir o acesso à pesquisa pela identidade do usuário?

Você pode implementar [filtros de segurança](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search) com filtro `search.in()`. O filtro integra-se bem com [serviços de gerenciamento de identidade como o Microsoft Azure Active Directory (AAD)](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search-with-aad) para selecionar resultados de pesquisa com base em associação de grupos de usuários definidas.

### <a name="why-are-there-zero-matches-on-terms-i-know-to-be-valid"></a>Por que não aparece correspondência em termos que sei que são válidos?

O mais comum é não saber que cada tipo de consulta dá suporte a níveis de análise linguística e a comportamentos de pesquisa diferentes. A pesquisa de texto completo, que é a carga de trabalho predominante, inclui uma fase de análise de linguagem que divide os termos de acordo com a raiz. Esse aspecto da análise de consulta amplia as possíveis correspondências, pois o termo indexado corresponde a um número maior de variantes.

Consultas difusas, de regex e com caractere curinga, no entanto, não são analisadas como consultas de termos ou frases comuns e podem levar a uma recuperação ruim se a consulta não coincidir com o formulário analisado da palavra no índice de pesquisa. Para obter mais informações sobre verificação e análise de consultas, consulte [arquitetura de consulta](https://docs.microsoft.com/azure/search/search-lucene-query-architecture).

### <a name="my-wildcard-searches-are-slow"></a>Minhas pesquisas com caractere curinga estão lentas.

A maioria das consultas de pesquisa com caractere curinga, como prefixo, difusa e regex, são reconfiguradas internamente com termos correspondentes no índice de pesquisa. Esse processamento adicional de exame do índice de pesquisa aumenta a latência. Além disso, consultas de pesquisa amplas, como `a*`, por exemplo, que provavelmente serão reconfiguradas com muitos termos, podem ser muito lentas. Para pesquisas com muitos caracteres curingas, considere definir um [analisador personalizado](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search).

### <a name="why-is-the-search-rank-a-constant-or-equal-score-of-10-for-every-hit"></a>Por que a classificação é uma pontuação igual ou constante de 1,0 para cada ocorrência?

Por padrão, os resultados da pesquisa são pontuados com base nas [propriedades estatísticas dos termos de correspondência](search-lucene-query-architecture.md#stage-4-scoring) e classificados do mais alto para o mais baixo do conjunto de resultados. No entanto, alguns tipos de consulta (caractere curinga, prefixo, regex) sempre contribuem com uma pontuação constante para a pontuação total do documento. Este comportamento ocorre por design. O Azure Search impõe uma pontuação constante para permitir que as correspondências encontradas pela expansão de consulta sejam incluídas nos resultados sem afetar a classificação.

Por exemplo, suponha que uma entrada de "turnê*" em uma pesquisa com curinga produz correspondências em "turim", "turrão" e "turmalina". Dada a natureza desses resultados, não é possível inferir de forma razoável quais termos são mais valiosos do que outros. Por esse motivo, podemos ignorar as frequências dos termos ao pontuar resultados em consultas dos tipos caractere curinga, prefixo e regex. Os resultados da pesquisa com base em uma entrada parcial recebem uma pontuação constante para evitar a tendência de correspondências possivelmente inesperadas.

## <a name="design-patterns"></a>Padrões de design

### <a name="what-is-the-best-approach-for-implementing-localized-search"></a>Qual é a melhor abordagem para implementar pesquisa localizada?

A maioria dos clientes escolhe campos dedicados em vez de uma coleção quando se trata de dar suporte a idiomas diferentes no mesmo índice. Os campos específicos de idioma possibilitam a atribuição de um analisador adequado. Por exemplo, a atribuição do Analisador de Francês da Microsoft a um campo que contém cadeias de caracteres em francês. Isso também simplifica a filtragem. Se você souber que uma consulta é iniciada em uma página com fr-fr na URL, poderá limitar os resultados da pesquisa para esse campo. Ou criar um [perfil de pontuação](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) para dar ao campo mais peso relativo. O Azure Search oferece suporte a mais de [50 analisadores de idioma](https://docs.microsoft.com/azure/search/search-language-support) à sua escolha.

## <a name="next-steps"></a>Próximas etapas

A sua pergunta é sobre a falta de um recurso ou funcionalidade? Solicite o recurso no [site do User Voice](https://feedback.azure.com/forums/263029-azure-search).

## <a name="see-also"></a>Consulte também

 [Stack Overflow: Azure Search](https://stackoverflow.com/questions/tagged/azure-search)   
 [Como funciona a pesquisa de texto completo no Azure Search](search-lucene-query-architecture.md)  
 [O que é o Azure Search?](search-what-is-azure-search.md)
