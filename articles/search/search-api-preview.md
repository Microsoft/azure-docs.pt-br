---
title: API REST de visualização para Azure Search 2019-05-06-Preview-Azure Search
description: Azure Search API REST do serviço versão 2019-05-06-Preview inclui recursos experimentais, como repositório de conhecimento e chaves de criptografia gerenciadas pelo cliente.
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: search
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 29d079c4e21352ced5fdcde44acaee66b79f6af9
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876649"
---
# <a name="azure-search-service-rest-api-version-2019-05-06-preview"></a>API REST do serviço de Azure Search-versão 2019-05-06-Preview
Este artigo descreve a versão `api-version=2019-05-06-Preview` da API REST do serviço Azure Search, oferecendo recursos experimentais ainda não disponíveis.

> [!NOTE]
> Os recursos de versão prévia estão disponíveis para teste e experimentação com a meta de coletar comentários e estão sujeitos a alteração. É altamente recomendável não usar APIs de versão prévia em aplicativos de produção.


## <a name="new-in-2019-05-06-preview"></a>Novo no 2019-05-06-Preview

A [**loja de conhecimento**](knowledge-store-concept-intro.md) é um novo destino de um pipeline de enriquecimento baseado em ia. Além de um índice, agora você pode persistir estruturas de dados preenchidas criadas durante a indexação no armazenamento do Azure. Você controla as estruturas físicas de seus dados por meio de elementos em um configurador de habilidades, incluindo como os dados são formatados, se os dados são armazenados no armazenamento de tabelas ou no armazenamento de BLOBs e se há várias exibições.

[**As chaves de criptografia gerenciadas pelo cliente**](search-security-manage-encryption-keys.md) para criptografia do lado do serviço em repouso também são um novo recurso de visualização. Além da criptografia interna em repouso gerenciada pela Microsoft, você pode aplicar uma camada adicional de criptografia em que você é o único proprietário das chaves.

## <a name="other-preview-features"></a>Outros recursos de versão prévia

Os recursos anunciados em versões prévias anteriores ainda estão em versão prévia pública. Se estiver chamando uma API com uma versão prévia anterior, você poderá continuar usando essa versão ou mudar para a `2019-05-06-Preview` sem alterações no comportamento esperado.

+ [O parâmetro de consulta moreLikeThis](search-more-like-this.md) localiza documentos relevantes para um documento específico. Esse recurso é encontrado em versões prévias anteriores. 
* A indexação de [BLOBs CSV](search-howto-index-csv-blobs.md) cria um documento por linha, em oposição a um documento por blob de texto.
* [Suporte à API do MongoDB para Cosmos DB indexadores](search-howto-index-cosmosdb.md) está em versão prévia.


## <a name="how-to-call-a-preview-api"></a>Como chamar uma API de versão prévia

As versões prévias mais antigas ainda funcionam, mas se tornam obsoletas ao longo do tempo. Se o código chama `api-version=2016-09-01-Preview` ou `api-version=2017-11-11-Preview`, as chamadas ainda são válidas. No entanto, apenas a versão prévia mais recente é atualizada com melhorias. 

A sintaxe de exemplo a seguir ilustra uma chamada para a versão de visualização da API.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

O serviço Azure Search está disponível em várias versões. Para obter mais informações, confira [Versões de API](search-api-versions.md).

## <a name="next-steps"></a>Próximas etapas

Examine a documentação de referência da API REST do serviço de Azure Search. Se você tiver problemas, peça ajuda no [StackOverflow](https://stackoverflow.com/) ou [entre em contato com o suporte](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Referência da API REST do serviço de pesquisa](https://docs.microsoft.com/rest/api/searchservice/)