---
title: Suporte de idioma – QnA Maker
titleSuffix: Azure Cognitive Services
description: O idioma de uma base de dados de conhecimento afeta a capacidade do QnA Maker de extrair automaticamente as perguntas e respostas das fontes, bem como a relevância dos resultados fornecidos pelo QnA Maker em resposta às consultas do usuário. Uma lista de idiomas naturais e cultura com suporte do QnA Maker para sua base de dados de conhecimento. Não misture idiomas na mesma base de dados de conhecimento.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/21/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: a543b17633b99bea63d72f46ba856a8b4593d16a
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67439547"
---
# <a name="language-support-for-qna-maker"></a>Suporte ao idioma para o QnA Maker

O idioma de uma base de dados de conhecimento afeta a capacidade do QnA Maker de extrair automaticamente as perguntas e respostas das [fontes](../Concepts/data-sources-supported.md), bem como a relevância de resultados fornecidos pelo QnA Maker em resposta às consultas do usuário.

## <a name="auto-extraction"></a>Extração automática
O QnA Maker dá suporte à extração de perguntas/respostas em qualquer página de idioma, mas a eficácia da extração é muito maior nos idiomas a seguir, pois o QnA Maker usa palavras-chave para identificar perguntas.

|Idiomas compatíveis| Local|
|-----|----|
|Inglês|en-*|
|Francês|fr-*|
|Italiano|it-*|
|Alemão|de-*|
|Espanhol|es-*|

## <a name="primary-language-detection"></a>Detecção de idioma primária

O idioma principal usado para detecção de é definido para o recurso do QnA Maker e todas as bases de Conhecimento criadas neste recurso, quando o primeiro documento ou URL é adicionado à base de Conhecimento primeiro. O idioma não pode ser alterado. 

Se o usuário planos para oferecer suporte a vários idiomas, eles precisam ter um novo recurso do QnA Maker para cada idioma. Saiba como [criar uma base de dados de Conhecimento de QnA Maker baseada na linguagem](../how-to/language-knowledge-base.md).  

Verifique se o idioma principal com as seguintes etapas:

1. Entre no [Portal do Azure](https://portal.azure.com).  
1. Procure e selecione o recurso de Azure Search criado como parte do seu recurso do QnA Maker. O nome do recurso do Azure Search começará com o mesmo nome que o recurso do QnA Maker e terá o tipo **serviço de pesquisa**. 
1. Dos **visão geral** página do recurso pesquisa, selecione **índices**. 
1. Selecione o índice **testkb**.
1. Selecione o **campos** guia. 
1. Modo de exibição de **Analyzer** coluna para o **perguntas** e **resposta** campos. 


## <a name="query-matching-and-relevance"></a>Correspondência de consulta e relevância
O QnA Maker depende de [analisadores de idioma](https://docs.microsoft.com/rest/api/searchservice/language-support) no Azure Search para fornecer resultados. Os recursos de nova classificação especiais estão disponíveis para idiomas En-* que permitem uma maior relevância.

Enquanto os recursos do Azure Search estão no mesmo nível dos idiomas com suporte, o QnA Maker tem um classificador adicional que fica acima dos resultados de pesquisa do Azure. Nesse modelo classificador, podemos usar alguns recursos especiais de semânticos e baseado no word em en-*, que ainda não estão disponíveis para outros idiomas. Nós não disponibilizar esses recursos, como eles fazem parte do funcionamento interno classificador do QnA Maker. 

O QnA Maker [detecta automaticamente o idioma da base de dados de Conhecimento](#primary-language-detection) durante a criação e define o analisador adequadamente. Você pode criar bases de dados de conhecimento nos idiomas a seguir. 

|Idiomas compatíveis|
|-----|
|Árabe|
|Armênia|
Bengalês|
|Basco|
|Búlgaro|
|Catalão|
|Chinês_Simplificado|
|Chinês_Tradicional|
|Croata|
|Tcheco|
|Dinamarquês|
|Holandês|
|Inglês|
|Estoniano|
|Finlandês|
|Francês|
|Galego|
|Alemão|
|Grego|
|Guzerate|
|Hebraico|
|Híndi|
|Húngaro|
|Islandês|
|Indonésio|
|Irlandês|
|Italiano|
|Japonês|
|Canarim|
|Coreano|
|Letão|
|Lituano|
|Malaiala|
|Malaio|
|Norueguês|
|Polonês|
|Português|
|Panjabi|
|Romeno|
|Russo|
|Cirílico sérvio|
|sérvio Latino|
|Eslovaco|
|Esloveno|
|Espanhol|
|Sueco|
|Tâmil|
|Télugo|
|Tailandês|
|Turco|
|Ucraniano|
|Urdu|
|Vietnamita|
