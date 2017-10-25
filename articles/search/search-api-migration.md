---
title: "Atualização para a API REST do Serviço Azure Search versão 2016-09-01 | Microsoft Docs"
description: "Atualização para a API de REST do serviço de Pesquisa do Azure versão 2016-09-01"
services: search
documentationcenter: 
author: brjohnstmsft
manager: pablocas
editor: 
ms.assetid: 6183fa6c-48bb-4af7-adae-4be3bc43c3ed
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/27/2016
ms.author: brjohnst
ms.openlocfilehash: f6a189c2e314b91c490583a86d8bacca8ec78a0f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="upgrading-to-the-azure-search-service-rest-api-version-2016-09-01"></a>Atualização para a API de REST do serviço de Pesquisa do Azure versão 2016-09-01
Se você estiver usando a versão 2015-02-28 ou 2015-02-28-Preview da [API REST do serviço Azure Search](https://msdn.microsoft.com/library/azure/dn798935.aspx), este artigo o ajudará a atualizar o aplicativo para usar a próxima versão da API disponível, 2016-09-01.

A versão 2016-09-01 da API REST contém algumas alterações em relação a versões anteriores. Elas são principalmente compatíveis com versões anteriores. Portanto, a alteração do código deve exigir apenas um mínimo de esforço, dependendo da versão que você estava usando antes. Confira [Etapas da atualização](#UpgradeSteps) para obter instruções sobre como alterar o código para usar a nova versão da API.

> [!NOTE]
> Sua instância de serviço do Azure Search dá suporte a várias versões da API REST, incluindo a última. Você pode continuar usando uma versão quando ela não for mais a última, mas aconselhamos a migrar seu código para usar a versão mais recente.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2016-09-01"></a>O que há de novo na versão 2016-09-01
A versão 2016-09-01 é a segunda versão com disponibilidade geral da API REST do Serviço Azure Search. Os novos recursos nesta versão da API incluem:

* [Analisadores personalizados](https://aka.ms/customanalyzers), que permitem assumir o controle do processo de conversão de texto para tokens indexáveis e pesquisáveis.
* Os indexadores de [Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md) e [Armazenamento de Tabelas do Azure](search-howto-indexing-azure-tables.md), que permitem que você importe facilmente dados do armazenamento do Azure para o Azure Search em um agendamento ou sob demanda.
* [Mapeamentos de campo](search-indexer-field-mappings.md), que permitem que você personalize como os indexadores importam dados para o Azure Search.
* ETags, que permitem atualizar as definições de índices, indexadores e fontes de dados com segurança para simultaneidade. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Etapas da atualização
Se estiver atualizando da versão 2015-02-28, você provavelmente não precisará fazer alterações no código, exceto a alteração do número de versão. As únicas situações em que talvez seja necessário alterar o código ocorrem quando:

* O código falha quando propriedades não reconhecidas são retornadas em uma resposta da API. Por padrão, o aplicativo deve ignorar propriedades que não entende.
* O código persiste solicitações de API e tenta reenviá-las à nova versão da API. Por exemplo, isso poderá acontecer se o aplicativo persistir tokens de continuação retornados da API de Pesquisa (para obter mais informações, procure `@search.nextPageParameters` na [Referência de API de Pesquisa](https://msdn.microsoft.com/library/azure/dn798927.aspx#Anchor_1)).

Se alguma dessas situações se aplicar a você, talvez seja necessário alterar o código da maneira adequada. Caso contrário, nenhuma alteração será necessária, a menos que você deseje começar a usar os [novos recursos](#WhatsNew) da versão 2016-09-01.

Se você estiver atualizando da versão 2015-02-28-Preview, os itens acima também serão aplicáveis, mas esteja ciente de que alguns recursos de visualização não estão disponíveis na versão 2016-09-01:

* Suporte de indexador do Armazenamento de Blobs do Azure para arquivos CSV e blobs que contêm matrizes JSON.
* Sinônimos
* Consultas "Mais como esta"

Se o código usar esses recursos, você não poderá atualizar para a versão 2016-09-01 sem parar de usá-los.

> [!IMPORTANT]
> Lembre-se de que as APIs de visualização servem para teste e avaliação, e não devem ser usadas em ambientes de produção.
> 
> 

## <a name="conclusion"></a>Conclusão
Se precisar de mais detalhes sobre como usar a API REST do Serviço Azure Search, confira a [Referência da API](https://msdn.microsoft.com/library/azure/dn798935.aspx) recém-atualizada no MSDN.

Apreciamos seus comentários sobre o Azure Search. Se tiver problemas, fique à vontade para solicitar ajuda no [Fórum do MSDN sobre a Pesquisa do Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch) ou o [StackOverflow](http://stackoverflow.com/). Se você estiver fazendo uma pergunta sobre o Azure Search no StackOverflow, marque-a com `azure-search`.

Obrigado por usar a Pesquisa do Azure!

