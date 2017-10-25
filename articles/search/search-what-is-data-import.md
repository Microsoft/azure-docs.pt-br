---
title: Upload de dados no Azure Search | Microsoft Docs
description: "Aprenda a carregar dados em um índice na Pesquisa do Azure."
services: search
documentationcenter: 
author: ashmaka
manager: jhubbard
editor: 
tags: 
ms.assetid: aa8d47c1-4ae6-4209-a8ce-48d5a9474707
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 05/01/2017
ms.author: ashmaka
ms.openlocfilehash: 5a601b75ec67824e72d8736bc3c45f8e1231ca86
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="upload-data-to-azure-search"></a>Carregar dados para a Pesquisa do Azure
> [!div class="op_single_selector"]
> * [Visão geral](search-what-is-data-import.md)
> * [.NET](search-import-data-dotnet.md)
> * [REST](search-import-data-rest-api.md)
> 
> 

Há duas maneiras de popular um índice com seus dados. A primeira opção é enviar manualmente os dados para o índice usando a [API REST](search-import-data-rest-api.md) ou o [SDK do .NET](search-import-data-dotnet.md) do Azure Search. A segunda opção é [apontar uma fonte de dados com suporte](search-indexer-overview.md) para o índice e permitir que o Azure Search e obtenha automaticamente os dados.

## <a name="push-data-to-an-index"></a>Enviar por push dados para um índice
Essa abordagem se refere ao envio programático de dados para a Pesquisa do Azure para torná-los disponíveis para a pesquisa. Para os aplicativos com requisitos de latência muito baixos (por exemplo, se você precisar que as operações de pesquisa estejam sincronizadas com os bancos de dados de inventário), um modelo de push será sua única opção.

Você pode usar a [API REST](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents) ou o [SDK do .NET](search-import-data-dotnet.md) para enviar dados por push para um índice. Atualmente, não há nenhum suporte de ferramenta para enviar por push dados por meio do portal.

Essa abordagem é mais flexível do que um modelo de pull, pois você pode carregar os documentos individualmente ou em lotes (até 1.000 por lote ou 16 MB, o limite que vier em primeiro lugar). O modelo de push também permite que você carregue documentos para a Pesquisa do Azure, independentemente de onde os dados estão.

O formato de dados compreendido pelo Azure Search é JSON, e todos os documentos no conjunto de dados devem ter campos que são mapeados para os campos definidos no esquema de índice. 

## <a name="pull-data-into-an-index"></a>Extrair dados para um índice
O modelo de pull rastreia uma fonte de dados com suporte e carrega automaticamente os dados no índice. No Azure Search, esse recurso é implementado por meio de *indexadores*, disponíveis no momento para o [Armazenamento de blobs](search-howto-indexing-azure-blob-storage.md), [Armazenamento de tabelas](search-howto-indexing-azure-tables.md), [Azure Cosmos DB](http://aka.ms/documentdb-search-indexer), [banco de dados SQL do Azure e SQL Server nas VMs do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md). 

Indexadores conectam um índice a uma fonte de dados (geralmente uma tabela, exibição ou estrutura equivalente) e mapeiam campos de origem para os campos equivalentes no índice. Durante a execução, o conjunto de linhas é automaticamente transformado em JSON e carregado no índice especificado. Todos os indexadores dão suporte à programação. Assim, você pode especificar com que frequência os dados devem ser atualizados. A maioria dos indexadores fornece controle de alterações se a fonte de dados oferecer suporte a isso. Rastreando as alterações e as exclusões para os documentos existentes, além de reconhecer novos documentos, os indexadores dispensam a necessidade de gerenciar ativamente os dados no índice. 

A funcionalidade de indexador é exposta no [Portal do Azure](search-import-data-portal.md), na [API REST](/rest/api/searchservice/Indexer-operations) e no [SDK do .NET](/dotnet/api/microsoft.azure.search.indexersoperations). 

Uma vantagem de usar o portal é que o Azure Search normalmente pode gerar um esquema de índice padrão para você, lendo os metadados do conjunto de dados de origem. Você pode modificar o índice gerado até que ele seja processado. Depois disso, as únicas edições de esquema permitidas são aquelas que não exigem reindexação. Se as alterações que você deseja fazer afetarem o esquema diretamente, será necessário recompilar o índice. 

Depois que o índice é populado, você pode usar o **Gerenciador de Pesquisa** na barra de comandos do portal como uma etapa de verificação.

## <a name="query-an-index-using-search-explorer"></a>Consultar um índice usando o Search Explorer

Uma maneira rápida de executar uma verificação preliminar sobre o carregamento do documento é usar o **Gerenciador de Pesquisa** no portal. O explorador permite consultar um índice sem precisar escrever código. A experiência de pesquisa se baseia nas configurações padrão, como a [sintaxe simples](/rest/api/searchservice/simple-query-syntax-in-azure-search) e padrão [searchMode query parameter](/rest/api/searchservice/search-documents). Os resultados são retornados em JSON para que você possa inspecionar o documento inteiro.

> [!TIP]
> Vários [exemplos de código de Azure Search](https://github.com/Azure-Samples/?utf8=%E2%9C%93&query=search) incluem conjuntos de dados incorporados ou prontamente disponíveis, oferecendo uma maneira fácil de começar a trabalhar. O portal também fornece um indexador de exemplo e uma fonte de dados que consiste em um conjunto de dados de imóveis pequeno (denominado "realestate-us-sample"). Quando você executa o indexador pré-configurado na fonte de dados de exemplo, um índice é criado e carregado com documentos que podem ser consultados no Explorador de Pesquisa ou pelo código que você escreve.