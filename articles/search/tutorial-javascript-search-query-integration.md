---
title: 'Tutorial de JavaScript: Destaques da integração de Pesquisa'
titleSuffix: Azure Cognitive Search
description: Noções básicas das consultas de pesquisa do SDK do JavaScript usadas no site habilitado para pesquisa
manager: nitinme
author: diberry
ms.author: diberry
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 03/09/2021
ms.custom: devx-track-js
ms.devlang: javascript
ms.openlocfilehash: cf4e1b1ecf209b587a45ca4c43607bfa95155aee
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "104723429"
---
# <a name="4---search-integration-highlights"></a>4 – Destaques da integração de Pesquisa

Nas lições anteriores, você adicionou a pesquisa a um Aplicativo Web Estático. Esta lição destaca as etapas essenciais que estabelecem a integração. Se você estiver procurando uma folha de referências para integrar a pesquisa ao seu aplicativo JavaScript, este artigo explicará o que você precisa saber.

## <a name="azure-sdk-azuresearch-documents"></a>SDK do Azure @azure/search-documents 

O aplicativo de funções usa o SDK do Azure para Cognitive Search:

* NPM: [@azure/search-documents](https://www.npmjs.com/package/@azure/search-documents)
* Documentação de referência: [Biblioteca de clientes](/javascript/api/overview/azure/search-documents-readme)

O aplicativo de funções se autentica por meio do SDK na API do Cognitive Search baseada em nuvem usando o nome do recurso, a chave de recurso e o nome do índice. Os segredos são armazenados nas configurações do Aplicativo Web Estático e inseridas na Função como variáveis de ambiente. 

## <a name="configure-secrets-in-a-configuration-file"></a>Configurar segredos em um arquivo de configuração

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/api/config.js" highlight="3,4" :::

## <a name="azure-function-search-the-catalog"></a>Função do Azure: pesquisar o catálogo

A [API](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Search/index.js) de `Search` usa um termo de pesquisa e o procura nos documentos do Índice de Pesquisa, retornando uma lista de correspondências. 

O roteamento da API de Pesquisa está contido nas associações de [function.json](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Search/function.json).

A Função do Azure efetua pull das informações de configuração de Pesquisa e preenche a consulta.

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/api/Search/index.js" highlight="4-9, 75" :::

## <a name="client-search-from-the-catalog"></a>Cliente: pesquisar no catálogo

Chame a Função do Azure no cliente do React com o código a seguir. 

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/src/pages/Search/Search.js" highlight="40-51" :::

## <a name="azure-function-suggestions-from-the-catalog"></a>Função do Azure: sugestões do catálogo

A [API](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Suggest/index.js) de `Suggest` usa um termo de pesquisa enquanto um usuário digita um texto e sugere termos de pesquisa como títulos de livros e autores nos documentos do Índice de Pesquisa, retornando uma pequena lista de correspondências. 

O sugestor de pesquisa, `sg`, é definido no [arquivo de esquema](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/bulk-insert/good-books-index.json) usado durante o upload em massa.

O roteamento da API de Sugestão está contido nas associações de [function.json](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Suggest/function.json).

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/api/Suggest/index.js" highlight="4-9, 21" :::

## <a name="client-suggestions-from-the-catalog"></a>Cliente: sugestões do catálogo

A API de função de Sugestão é chamada no aplicativo React em `\src\components\SearchBar\SearchBar.js` como parte da inicialização do componente:

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/src/components/SearchBar/SearchBar.js" highlight="52-60" :::

## <a name="azure-function-get-specific-document"></a>Função do Azure: obter um documento específico 

A [API](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Lookup/index.js) de `Lookup` usa uma ID e retorna o objeto de documento do Índice de Pesquisa. 

O roteamento da API de Pesquisa está contido nas associações de [function.json](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Lookup/function.json).

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/api/Lookup/index.js" highlight="4-9, 17" :::

## <a name="client-get-specific-document"></a>Cliente: obter um documento específico 

Essa API de função é chamada no aplicativo React em `\src\pages\Details\Detail.js` como parte da inicialização do componente:

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/src/pages/Details/Details.js" highlight="19-29" :::

## <a name="next-steps"></a>Próximas etapas

* [Indexar dados do SQL do Azure](search-indexer-tutorial.md)
