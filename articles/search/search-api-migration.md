---
title: Atualizar para a última versão da API REST do serviço Azure Search – Azure Search
description: Examine as diferenças nas versões de API e saiba quais ações são necessárias para migrar o código existente para a versão mais recente da API REST do serviço Azure Search.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: 23003859b9a75fb986fe65f5528004f3dd150f9b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61126984"
---
# <a name="upgrade-to-the-latest-azure-search-service-rest-api-version"></a>Atualizar para a última versão da API REST do serviço Azure Search
Se você estiver usando uma versão anterior da [API REST do serviço Azure Search](https://docs.microsoft.com/rest/api/searchservice/), este artigo o ajudará a atualizar o aplicativo para usar a versão mais recente da API disponível, 2017-11-11.

A versão 2017-11-11 da API REST contém algumas alterações em relação a versões anteriores. Elas são principalmente compatíveis com versões anteriores. Portanto, a alteração do código deve exigir apenas um mínimo de esforço, dependendo da versão que você estava usando antes. Confira [Etapas da atualização](#UpgradeSteps) para obter instruções sobre como alterar o código para usar a nova versão da API.

> [!NOTE]
> Sua instância de serviço do Azure Search dá suporte a várias versões da API REST, incluindo a última. Você pode continuar usando uma versão quando ela não for mais a última, mas aconselhamos a migrar seu código para usar a versão mais recente.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2017-11-11"></a>Novidades na versão 2017-11-11
A versão 2017-11-11 é a versão mais recente com disponibilidade geral da API REST do Serviço Azure Search. Os novos recursos nesta versão da API incluem:

* [Sinônimos](search-synonyms.md). O novo recurso de sinônimos permite definir termos equivalentes e expandir o escopo da consulta.
* [Suporte para indexar com eficiência os blobs de texto](https://docs.microsoft.com/azure/search/search-howto-indexing-azure-blob-storage#IndexingPlainText). O novo modo de análise  `text` para indexadores de Blobs do Azure melhora significativamente o desempenho de indexação.
* [API de Estatísticas do Serviço](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics) Exiba o uso atual e os limites de recursos no Azure Search com essa nova API.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Etapas da atualização
Se estiver atualizando da versão GA 2015-02-28 ou 2016-09-01, você provavelmente não precisará fazer alterações no código, exceto a alteração do número de versão. As únicas situações em que talvez seja necessário alterar o código ocorrem quando:

* O código falha quando propriedades não reconhecidas são retornadas em uma resposta da API. Por padrão, o aplicativo deve ignorar propriedades que não entende.
* O código persiste solicitações de API e tenta reenviá-las à nova versão da API. Por exemplo, isso poderá acontecer se o aplicativo persistir tokens de continuação retornados da API de Pesquisa (para obter mais informações, procure `@search.nextPageParameters` na [Referência de API de Pesquisa](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)).

Se alguma dessas situações se aplicar a você, talvez seja necessário alterar o código da maneira adequada. Caso contrário, nenhuma alteração será necessária, a menos que você deseje começar a usar os [novos recursos](#WhatsNew) da versão 2017-11-11.

Se você estiver atualizando da versão prévia da API, os itens acima também serão aplicáveis, mas esteja ciente de que alguns recursos de visualização não estão disponíveis na versão 2017-11-11:

* Suporte de indexador do Armazenamento de Blobs do Azure para arquivos CSV e blobs que contêm matrizes JSON.
* Consultas "Mais como esta"

Se o código usar esses recursos, você não poderá atualizar para a versão 2017-11-11 sem parar de usá-los.

> [!IMPORTANT]
> Lembre-se de que as APIs de visualização servem para teste e avaliação, e não devem ser usadas em ambientes de produção.
> 
> 

## <a name="conclusion"></a>Conclusão
Se precisar de mais detalhes sobre como usar a API REST do Serviço Azure Search, confira a [Referência da API](https://docs.microsoft.com/rest/api/searchservice/) recém-atualizada no MSDN.

Apreciamos seus comentários sobre o Azure Search. Se tiver problemas, fique à vontade para solicitar ajuda no [Fórum do MSDN sobre o Azure Search](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch) ou o [StackOverflow](https://stackoverflow.com/). Se você estiver fazendo uma pergunta sobre o Azure Search no StackOverflow, marque-a com `azure-search`.

Obrigado por usar o Azure Search!

