---
title: "Consultar um índice (portal - Azure Search) | Microsoft Docs"
description: Emita uma consulta de pesquisa no Gerenciador de Pesquisa do Portal do Azure.
services: search
manager: jhubbard
documentationcenter: 
author: ashmaka
ms.assetid: 8e524188-73a7-44db-9e64-ae8bf66b05d3
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 07/10/2017
ms.author: ashmaka
ms.openlocfilehash: dd68d8ed073bf7b8666ddef35a2f1f84df690b4b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="query-an-azure-search-index-using-search-explorer-in-the-azure-portal"></a>Consultar um índice do Azure Search usando o Search Explorer no portal do Azure
> [!div class="op_single_selector"]
> * [Visão geral](search-query-overview.md)
> * [Portal](search-explorer.md)
> * [.NET](search-query-dotnet.md)
> * [REST](search-query-rest-api.md)
> 
> 

Este artigo mostra como consultar um índice do Azure Search usando o **Search Explorer** no portal do Azure. Você pode usar o Search Explorer para enviar cadeias de caracteres de consulta Lucene simples ou completas para qualquer índice existente em seu serviço.

## <a name="open-the-service-dashboard"></a>Abrir o painel de serviço
1. Clique em **Todos os recursos** no menu à esquerda do [portal do Azure](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).
2. Selecione o serviço Azure Search.

## <a name="select-an-index"></a>Selecionar um índice

Selecione o índice que você deseja pesquisar no bloco **Índices**.

   ![](./media/search-explorer/pick-index.png)

## <a name="open-search-explorer"></a>Abrir o Search Explorer

Clique no bloco do Search Explorer para deslizar e abrir a barra de pesquisa e o painel de resultados.

   ![](./media/search-explorer/search-explorer-tile.png)

## <a name="start-searching"></a>Iniciar a pesquisa

Ao usar o Search Explorer, você pode especificar [parâmetros de consulta](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) para formular a consulta.

1. Em **Cadeia de caracteres de consulta**, digite uma consulta e então pressione **Pesquisar**. 

   A cadeia de caracteres de consulta é analisada automaticamente para a URL de solicitação adequada para enviar uma solicitação HTTP em relação à API REST do Azure Search.   
   
   Você pode usar qualquer sintaxe de consulta Lucene simples ou completa válida para criar a solicitação. O caractere `*` é equivalente a uma pesquisa vazia ou não especificada que retorna todos os documentos em nenhuma ordem específica.

2. Em **Resultados**, os resultados da consulta são apresentados em JSON bruto, idêntico à carga retornada em um Corpo de Resposta HTTP ao emitir solicitações de forma programática.

   ![](./media/search-explorer/search-bar.png)

## <a name="next-steps"></a>Próximas etapas

Os recursos a seguir fornecem exemplos e informações de sintaxe de consulta adicionais.

 + [Sintaxe de consulta simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 
 + [Sintaxe de consulta Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 
 + [Exemplos de sintaxe de consulta Lucene](https://docs.microsoft.com/azure/search/search-query-lucene-examples) 
 + [Sintaxe de expressão do filtro OData](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) 