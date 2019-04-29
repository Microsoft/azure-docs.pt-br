---
title: Recompilar um índice do Azure Search ou atualizar o conteúdo pesquisável – Azure Search
description: Adicione novos elementos, atualize elementos ou documentos existentes ou exclua documentos obsoletos em uma indexação de recompilação completa ou incremental parcial para atualizar um índice do Azure Search.
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 2595912732389c8a415d1854a84a7b9c182e4dc7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60871141"
---
# <a name="how-to-rebuild-an-azure-search-index"></a>Como recompilar um índice do Azure Search

Este artigo explica como recriar um índice de Azure Search, as circunstâncias sob as quais as recompilações são necessárias e recomendações para atenuar o impacto de recompilações em solicitações de consulta em andamento.

Um *recompilar* refere-se a remover e recriar as estruturas de dados físicos associadas a um índice, incluindo todos os índices invertidos com base no campo. No Azure Search, não é possível remover nem recriar campos individuais. Para recompilar um índice, todo o armazenamento de campo deve ser excluído, recriado com base em um esquema de índice existente ou revisado e, em seguida, novamente preenchido com os dados enviados por push para o índice ou extraídos de fontes externas. É comum recompilar índices durante o desenvolvimento, mas você também precisará recompilar um índice de nível de produção para acomodar alterações estruturais, como a adição de tipos complexos ou de campos a sugestores.

Em contraste com as recompilações que recebem um índice offline, a *atualização de dados* é executada como uma tarefa em segundo plano. Você pode adicionar, remover e substituir documentos com interrupção mínima para cargas de trabalho de consulta, embora as consultas normalmente demoram mais tempo para serem concluídas. Para obter mais informações sobre como atualizar o conteúdo do índice, consulte [Adicionar, atualizar ou excluir documentos](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).

## <a name="rebuild-conditions"></a>Recompilar condições

| Condição | DESCRIÇÃO |
|-----------|-------------|
| Alterar uma definição de campo | A revisão de um nome de campo, de um tipo de dados ou de [atributos de índice](https://docs.microsoft.com/rest/api/searchservice/create-index) específicos (pesquisáveis, filtráveis, classificáveis, com faceta) exige uma recompilação completa. |
| Atribuir um analisador a um campo | Os [analisadores](search-analyzers.md) são definidos em um índice e, em seguida, são atribuídos aos campos. É possível adicionar uma nova definição de analisador a um índice a qualquer momento, mas só é possível *atribuir* um analisador quando o campo é criado. Isso é verdadeiro para as propriedades **analyzer** e **indexAnalyzer**. A propriedade **searchAnalyzer** é uma exceção (é possível atribuir essa propriedade a um campo existente). |
| Atualizar ou excluir uma definição de analisador em um índice | Não é possível excluir nem alterar uma configuração de analisador existente (analisador, gerador de token, filtro de token ou filtro de caracteres) no índice, a menos que você recompile todo o índice. |
| Adicionar um campo a um sugestor | Se um campo já existir e você desejar adicioná-lo a um constructo [Sugestores](index-add-suggesters.md), será necessário recompilar o índice. |
| Excluir um campo | Para remover fisicamente todos os rastreamentos de um campo, você precisa recriar o índice. Quando uma recompilação imediata não é prática, é possível modificar o código do aplicativo para desabilitar o acesso ao campo "excluído". Fisicamente, a definição e o conteúdo do campo permanecem no índice até a próxima recompilação, quando você aplica um esquema que omite o campo em questão. |
| Alternar camadas | Se você precisar de mais capacidade, não há nenhuma atualização in-loco. Um novo serviço será criado no novo ponto de capacidade e os índices precisarão ser compilados desde o início no novo serviço. |

Quaisquer outras modificações podem ser feitas sem afetar as estruturas físicas existentes. Especificamente, as alterações a seguir *não* exigem uma recompilação de índice:

+ Adicionar um novo campo
+ Definir o atributo **recuperável** em um campo existente
+ Definir **searchAnalyzer** em um campo existente
+ Adicionar uma nova definição de analisador em um índice
+ Adicionar, atualizar ou excluir perfis de pontuação
+ Adicionar, atualizar ou excluir configurações de CORS
+ Adicionar, atualizar ou excluir synonymMaps

Quando você adiciona um novo campo, os documentos indexados existentes recebem um valor nulo para o novo campo. Em uma futura atualização de dados, os valores da fonte de dados externa substituirão os nulos adicionados pelo Azure Search. Para obter mais informações sobre como atualizar o conteúdo do índice, consulte [Adicionar, atualizar ou excluir documentos](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).

## <a name="partial-or-incremental-indexing"></a>Indexação parcial ou incremental

No Azure Search, você não pode controlar a indexação em uma base por campo, optando por excluir ou recriar campos específicos. Da mesma forma, não há nenhum mecanismo interno para [indexação de documentos com base em critérios](https://stackoverflow.com/questions/40539019/azure-search-what-is-the-best-way-to-update-a-batch-of-documents). Todos os requisitos para a indexação controlada por critérios precisam ser atendidos por meio de código personalizado.

No entanto, o que você pode fazer facilmente, é *atualizar documentos* em um índice. Para muitas soluções de pesquisa, dados de origem externa são voláteis e a sincronização entre os dados de origem e um índice de pesquisa é uma prática comum. No código, chame a operação [adicionar, atualizar ou excluir documentos](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) ou o [equivalente do .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexesoperationsextensions.createorupdate?view=azure-dotnet) para atualizar o conteúdo do índice ou para adicionar valores para um novo campo.

## <a name="partial-indexing-with-indexers"></a>Indexação parcial com indexadores

Os [Indexadores](search-indexer-overview.md) simplificam a tarefa de atualização de dados. Um indexador pode indexar somente uma tabela ou exibição na fonte de dados externa. Para indexar várias tabelas, a abordagem mais simples é criar uma exibição que une as tabelas e projeta as colunas que você deseja indexar. 

Ao usar indexadores que rastreiam fontes de dados externa, verifique se há uma coluna "marca d'água alta" na fonte de dados. Se houver, você poderá usá-la para detecção de alteração incremental selecionando apenas as linhas que contenham conteúdo novo ou revisado. Para o [Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md#incremental-indexing-and-deletion-detection), um campo `lastModified` é usado. No [Armazenamento de Tabela do Azure](search-howto-indexing-azure-tables.md#incremental-indexing-and-deletion-detection), `timestamp` tem a mesma finalidade. Da mesma forma, tanto o [indexador do Banco de Dados SQL do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows) quanto o [indexador do Azure Cosmos DB](search-howto-index-cosmosdb.md#indexing-changed-documents) têm campos para sinalizar as atualizações de linhas. 

Para obter mais informações sobre indexadores, confira a [Visão geral do indexador](search-indexer-overview.md) e [Redefinir a API REST do Indexador](https://docs.microsoft.com/rest/api/searchservice/reset-indexer).

## <a name="how-to-rebuild-an-index"></a>Como recompilar um índice

Planeje recompilações completas frequentes durante o desenvolvimento ativo, quando o esquema de índice está no estado de fluxo. Para aplicativos já em produção, é recomendável criar um novo índice que é executado lado a lado de um índice existente para evitar a inatividade de consulta.

Permissões de leitura/gravação no nível de serviço são necessárias para atualizações de índice. 

Não é possível recompilar um índice no portal. Programaticamente, é possível chamar a [API REST Atualizar índice](https://docs.microsoft.com/rest/api/searchservice/update-index) ou as [APIs equivalentes do .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexesoperations.createorupdatewithhttpmessagesasync?view=azure-dotnet) para obter uma recompilação completa. Uma solicitação de atualização de índice é idêntica à [API REST Criar índice](https://docs.microsoft.com/rest/api/searchservice/create-index), mas tem um contexto diferente.

O fluxo de trabalho a seguir é tendencioso em relação à API REST, mas aplica-se igualmente ao SDK do .NET.

1. Ao reutilizar um nome de índice, [remova o índice existente](https://docs.microsoft.com/rest/api/searchservice/delete-index). 

   Todas as consultas que direcionam esse índice são descartadas imediatamente. Excluir um índice é irreversível, destruindo o armazenamento físico para a coleção de campos e outros constructos. Certifique-se de que você tem certeza sobre as implicações de excluir um índice antes de descartá-lo. 

2. Formule uma solicitação [Atualizar índice](https://docs.microsoft.com/rest/api/searchservice/update-index) com o ponto de extremidade de serviço, chave de API e uma [chave de administrador](https://docs.microsoft.com/azure/search/search-security-api-keys). Uma chave de administrador é necessária para operações de gravação.

3. No corpo da solicitação, forneça um esquema de índice com as definições de campo alteradas ou modificadas. O corpo da solicitação contém o esquema de índice, bem como os constructos para as opções de CORS, analisadores, sugestores e perfis de pontuação. Os requisitos de esquema estão documentados em [Criar índice](https://docs.microsoft.com/rest/api/searchservice/create-index).

4. Envie uma solicitação [Atualizar índice](https://docs.microsoft.com/rest/api/searchservice/update-index) para recompilar a expressão física do índice no Azure Search. 

5. [Carregue o índice com documentos](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) de uma fonte externa.

Quando você cria o índice, o armazenamento físico é alocado para cada campo no esquema de índice, com um índice invertido criado para cada campo pesquisável. Os campos não pesquisáveis podem ser usados em filtros ou expressões, mas não têm índices invertidos e não são pesquisáveis de texto completo ou de difuso. Em uma recompilação de índice, esses índices invertidos são excluídos e recriados com base no esquema de índice que você fornecer.

Quando você carrega o índice, o índice invertido de cada campo é preenchido com todas as palavras exclusivas, indexadas de cada documento, com um mapa das IDs do documento correspondentes. Por exemplo, durante a indexação de um conjunto de dados de hotéis, um índice invertido criado para um campo de cidade pode conter os termos para Seattle, Portland e assim por diante. Documentos que incluem Seattle ou Portland no campo Cidade teriam sua ID de documento listada juntamente com o termo. Em qualquer operação [Adicionar, atualizar ou excluir](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents), os termos e a lista de IDs de documento são atualizadas de acordo.

> [!NOTE]
> Se você tiver rigorosos requisitos de SLA, você pode considerar o provisionamento de um novo serviço especificamente para esse trabalho, com o desenvolvimento e indexação ocorrendo em isolamento completo de um índice de produção. Um serviço separado é executado em seu próprio hardware, eliminando qualquer possibilidade de contenção de recursos. Quando o desenvolvimento for concluído, você pode deixar o novo índice em vigor, redirecionar consultas para o novo ponto de extremidade e o índice, ou você executa o código concluído para publicar um índice revisado em seu serviço de Azure Search original. Atualmente, não há nenhum mecanismo para mover um índice pronto para uso para outro serviço.

## <a name="view-updates"></a>Exibir atualizações

Você pode começar a consultar um índice, assim que o primeiro documento for carregado. Se você souber a ID de um documento, a [API REST de Procurar documento](https://docs.microsoft.com/rest/api/searchservice/lookup-document) retorna o documento específico. Para testes mais amplos, você deve aguardar até que o índice seja totalmente carregado e, em seguida, usar consultas para verificar o contexto em que você espera ver.

## <a name="see-also"></a>Consulte também

+ [Visão geral do indexador](search-indexer-overview.md)
+ [Indexar grandes conjuntos de dados em escala](search-howto-large-index.md)
+ [Indexando no portal](search-import-data-portal.md)
+ [Indexador do Banco de Dados SQL do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Indexador de Banco de dados do Azure Cosmos](search-howto-index-cosmosdb.md)
+ [Indexador do Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md)
+ [Indexador do Armazenamento de Tabelas do Azure](search-howto-indexing-azure-tables.md)
+ [Segurança no Azure Search](search-security-overview.md)