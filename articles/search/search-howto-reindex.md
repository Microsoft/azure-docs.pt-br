---
title: Recriar um índice de pesquisa
titleSuffix: Azure Cognitive Search
description: Adicione novos elementos, atualize elementos ou documentos existentes ou exclua documentos obsoletos em uma recompilação completa ou indexação parcial para atualizar um índice de Pesquisa Cognitiva do Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/18/2020
ms.openlocfilehash: 47e9b80bb25b7ff14695cc67682265fe338ff76f
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98119094"
---
# <a name="how-to-rebuild-an-index-in-azure-cognitive-search"></a>Como recriar um índice no Azure Pesquisa Cognitiva

Este artigo explica como recriar um índice de Pesquisa Cognitiva do Azure, as circunstâncias sob as quais as recompilações são necessárias e recomendações para mitigar o impacto de recompilações em solicitações de consulta em andamento.

Um *recompilar* refere-se a remover e recriar as estruturas de dados físicos associadas a um índice, incluindo todos os índices invertidos com base no campo. No Azure Pesquisa Cognitiva, não é possível descartar e recriar campos individuais. Para recompilar um índice, todo o armazenamento de campo deve ser excluído, recriado com base em um esquema de índice existente ou revisado e, em seguida, novamente preenchido com os dados enviados por push para o índice ou extraídos de fontes externas. 

É comum recriar índices durante o desenvolvimento quando você está Iterando em um design de índice, mas também pode precisar recriar um índice de nível de produção para acomodar alterações estruturais, como adicionar tipos complexos ou adicionar campos a sugestores.

## <a name="rebuild-versus-refresh"></a>"Recompilar" versus "atualizar"

A recompilação não deve ser confundida com a atualização do conteúdo de um índice com documentos novos, modificados ou excluídos. A atualização de um corpus de pesquisa é quase uma determinada em cada aplicativo de pesquisa, com alguns cenários que exigem atualizações de minutos (por exemplo, quando um corpus de pesquisa precisa refletir alterações de inventário em um aplicativo de vendas online).

Contanto que você não esteja alterando a estrutura do índice, você pode atualizar um índice usando as mesmas técnicas que usou para carregar o índice inicialmente:

* Para indexação de modo Push, chame [Adicionar, atualizar ou excluir documentos](/rest/api/searchservice/addupdate-or-delete-documents) para enviar por push as alterações a um índice.

* Para indexadores, você pode [agendar a execução do indexador](search-howto-schedule-indexers.md) e usar o controle de alterações ou os carimbos de data/hora para identificar o Delta. Se as atualizações devem ser refletidas mais rápido do que o que um Agendador pode gerenciar, você pode usar a indexação do modo Push.

## <a name="rebuild-conditions"></a>Recompilar condições

Remova e recrie um índice se qualquer uma das condições a seguir for verdadeira. 

| Condição | Descrição |
|-----------|-------------|
| Alterar uma definição de campo | A revisão de um nome de campo, de um tipo de dados ou de [atributos de índice](/rest/api/searchservice/create-index) específicos (pesquisáveis, filtráveis, classificáveis, com faceta) exige uma recompilação completa. |
| Atribuir um analisador a um campo | Os [analisadores](search-analyzers.md) são definidos em um índice e, em seguida, são atribuídos aos campos. É possível adicionar uma nova definição de analisador a um índice a qualquer momento, mas só é possível *atribuir* um analisador quando o campo é criado. Isso é verdadeiro para as propriedades **analyzer** e **indexAnalyzer**. A propriedade **searchAnalyzer** é uma exceção (é possível atribuir essa propriedade a um campo existente). |
| Atualizar ou excluir uma definição de analisador em um índice | Não é possível excluir nem alterar uma configuração de analisador existente (analisador, gerador de token, filtro de token ou filtro de caracteres) no índice, a menos que você recompile todo o índice. |
| Adicionar um campo a um sugestor | Se um campo já existir e você desejar adicioná-lo a um constructo [Sugestores](index-add-suggesters.md), será necessário recompilar o índice. |
| Excluir um campo | Para remover fisicamente todos os rastreamentos de um campo, você precisa recriar o índice. Quando uma recompilação imediata não é prática, você pode modificar o código do aplicativo para desabilitar o acesso ao campo "excluído" ou usar o [parâmetro de consulta $SELECT](search-query-odata-select.md) para escolher quais campos são representados no conjunto de resultados. Fisicamente, a definição e o conteúdo do campo permanecem no índice até a próxima recompilação, quando você aplica um esquema que omite o campo em questão. |
| Alternar camadas | Se você precisar de mais capacidade, não haverá nenhuma atualização in-loco no portal do Azure. Um novo serviço deve ser criado e os índices devem ser criados a partir do zero no novo serviço. Para ajudar a automatizar esse processo, você pode usar o código de exemplo **index-backup-restore** neste [repositório de exemplo do Azure pesquisa cognitiva .net](https://github.com/Azure-Samples/azure-search-dotnet-samples). Esse aplicativo fará o backup do índice em uma série de arquivos JSON e, em seguida, recriará o índice em um serviço de pesquisa que você especificar.|

## <a name="update-conditions"></a>Condições de atualização

Muitas outras modificações podem ser feitas sem afetar as estruturas físicas existentes. Especificamente, as alterações a seguir *não* exigem uma recompilação de índice. Para essas alterações, você pode [atualizar uma definição de índice](/rest/api/searchservice/update-index) com suas alterações.

+ Adicionar um novo campo
+ Definir o atributo **recuperável** em um campo existente
+ Definir **searchAnalyzer** em um campo existente
+ Adicionar uma nova definição de analisador em um índice
+ Adicionar, atualizar ou excluir perfis de pontuação
+ Adicionar, atualizar ou excluir configurações de CORS
+ Adicionar, atualizar ou excluir synonymMaps

Quando você adiciona um novo campo, os documentos indexados existentes recebem um valor nulo para o novo campo. Em uma atualização futura de dados, os valores de dados de origem externos substituem os nulos adicionados pelo Azure Pesquisa Cognitiva. Para obter mais informações sobre como atualizar o conteúdo do índice, consulte [Adicionar, atualizar ou excluir documentos](/rest/api/searchservice/addupdate-or-delete-documents).

## <a name="how-to-rebuild-an-index"></a>Como recompilar um índice

Durante o desenvolvimento, o esquema de índice é alterado com frequência. Você pode planejar isso criando índices que podem ser excluídos, recriados e recarregados rapidamente com um pequeno conjunto de dados representativos.

Para aplicativos já em produção, é recomendável criar um novo índice que é executado lado a lado de um índice existente para evitar a inatividade de consulta. O código do aplicativo fornece redirecionamento para o novo índice.

A indexação não é executada em segundo plano e o serviço balanceia a indexação adicional em relação a consultas contínuas. Durante a indexação, você pode [monitorar solicitações de consulta](search-monitor-queries.md) no portal para garantir que as consultas sejam concluídas oportunamente.

1. Determine se uma recompilação é necessária. Se você estiver apenas adicionando campos ou alterando alguma parte do índice que não esteja relacionado a campos, talvez seja possível simplesmente [atualizar a definição](/rest/api/searchservice/update-index) sem excluir, recriar e recarregá-la completamente.

1. [Obtenha uma definição de índice](/rest/api/searchservice/get-index) caso você precise dela para referência futura.

1. [Descarte o índice existente](/rest/api/searchservice/delete-index), supondo que você não esteja executando índices novos e antigos lado a lado. 

   Todas as consultas que direcionam esse índice são descartadas imediatamente. Lembre-se de que a exclusão de um índice é irreversível, destruindo o armazenamento físico para a coleção Fields e outras construções. Pause para pensar nas implicações antes de descartá-la. 

1. [Crie um índice revisado](/rest/api/searchservice/create-index), em que o corpo da solicitação inclui definições de campo alteradas ou modificadas.

1. [Carregue o índice com documentos](/rest/api/searchservice/addupdate-or-delete-documents) de uma fonte externa.

Quando você cria o índice, o armazenamento físico é alocado para cada campo no esquema de índice, com um índice invertido criado para cada campo pesquisável. Os campos não pesquisáveis podem ser usados em filtros ou expressões, mas não têm índices invertidos e não são pesquisáveis de texto completo ou de difuso. Em uma recompilação de índice, esses índices invertidos são excluídos e recriados com base no esquema de índice que você fornecer.

Quando você carrega o índice, o índice invertido de cada campo é preenchido com todas as palavras exclusivas, indexadas de cada documento, com um mapa das IDs do documento correspondentes. Por exemplo, durante a indexação de um conjunto de dados de hotéis, um índice invertido criado para um campo de cidade pode conter os termos para Seattle, Portland e assim por diante. Documentos que incluem Seattle ou Portland no campo Cidade teriam sua ID de documento listada juntamente com o termo. Em qualquer operação [Adicionar, atualizar ou excluir](/rest/api/searchservice/addupdate-or-delete-documents), os termos e a lista de IDs de documento são atualizadas de acordo.

> [!NOTE]
> Se você tiver rigorosos requisitos de SLA, você pode considerar o provisionamento de um novo serviço especificamente para esse trabalho, com o desenvolvimento e indexação ocorrendo em isolamento completo de um índice de produção. Um serviço separado é executado em seu próprio hardware, eliminando qualquer possibilidade de contenção de recursos. Quando o desenvolvimento for concluído, você deixaria o novo índice em vigor, redirecionando as consultas para o novo ponto de extremidade e índice, ou executaria o código concluído para publicar um índice revisado em seu serviço de Pesquisa Cognitiva do Azure original. Atualmente, não há nenhum mecanismo para mover um índice pronto para uso para outro serviço.

## <a name="check-for-updates"></a>Verificar atualizações

Você pode começar a consultar um índice, assim que o primeiro documento for carregado. Se você souber a ID de um documento, a [API REST de Procurar documento](/rest/api/searchservice/lookup-document) retorna o documento específico. Para testes mais amplos, você deve aguardar até que o índice seja totalmente carregado e, em seguida, usar consultas para verificar o contexto em que você espera ver.

Você pode usar o [Search Explorer](search-explorer.md) ou uma ferramenta de teste na Web, como o [postmaster](search-get-started-rest.md) ou [Visual Studio Code](search-get-started-vs-code.md) para verificar o conteúdo atualizado.

Se você adicionou ou renomeou um campo, use [$Select](search-query-odata-select.md) para retornar esse campo: `search=*&$select=document-id,my-new-field,some-old-field&$count=true`

## <a name="see-also"></a>Consulte também

+ [Visão geral do indexador](search-indexer-overview.md)
+ [Indexar grandes conjuntos de dados em escala](search-howto-large-index.md)
+ [Indexando no portal](search-import-data-portal.md)
+ [Indexador do Banco de Dados SQL do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Indexador de Banco de dados do Azure Cosmos](search-howto-index-cosmosdb.md)
+ [Indexador de armazenamento de BLOBs do Azure](search-howto-indexing-azure-blob-storage.md)
+ [Indexador de armazenamento de tabela do Azure](search-howto-indexing-azure-tables.md)
+ [Segurança no Azure Pesquisa Cognitiva](search-security-overview.md)