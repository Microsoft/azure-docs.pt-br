---
title: Pesquisa semântica
titleSuffix: Azure Cognitive Search
description: Saiba como Pesquisa Cognitiva está usando modelos de pesquisa semântica de aprendizado profundo do Bing para tornar os resultados da pesquisa mais intuitivos.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/12/2021
ms.custom: references_regions
ms.openlocfilehash: 634298952d990cd3639aa1c62592fde534b3e8b8
ms.sourcegitcommit: ec39209c5cbef28ade0badfffe59665631611199
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103232657"
---
# <a name="semantic-search-in-azure-cognitive-search"></a>Pesquisa semântica no Azure Pesquisa Cognitiva

> [!IMPORTANT]
> Os recursos de pesquisa semântica estão em visualização pública, disponíveis somente por meio da API REST de visualização. Os recursos de visualização são oferecidos no estado em que se encontram, sob [termos de uso suplementares](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)e não têm a garantia de ter a mesma implementação em disponibilidade geral. Para obter mais informações, consulte [disponibilidade e preços](semantic-search-overview.md#availability-and-pricing).

A pesquisa semântica é uma coleção de recursos relacionados à consulta que dão suporte a uma experiência de consulta mais natural e de alta qualidade. 

Esses recursos incluem uma reclassificação semântica dos resultados da pesquisa, bem como a extração de legenda e de resposta, com o realce semântico de termos e frases relevantes. Os modelos pretremos de ponta são usados para extração e classificação. Para manter o desempenho rápido que os usuários esperam da pesquisa, o resumo semântico e a classificação são aplicados apenas aos primeiros 50 resultados, conforme pontuado pelo [algoritmo de Pontuação de similaridade padrão](index-similarity-and-scoring.md#similarity-ranking-algorithms). Usando esses resultados como o documento corpus, a classificação semântica gera novamente esses resultados com base na intensidade semântica da correspondência.

A tecnologia subjacente é do Bing e do Microsoft Research e integrada à infraestrutura de Pesquisa Cognitiva como um recurso complementar. Para obter mais informações sobre os investimentos de pesquisa e de ia de backup de pesquisa semântica, consulte [como o ia do Bing é a ativação do Azure pesquisa cognitiva (blog do Microsoft Research)](https://www.microsoft.com/research/blog/the-science-behind-semantic-search-how-ai-from-bing-is-powering-azure-cognitive-search/).

O vídeo a seguir fornece uma visão geral dos recursos.

> [!VIDEO https://www.youtube.com/embed/yOf0WfVd_V0]

## <a name="components-and-workflow"></a>Componentes e fluxo de trabalho

A pesquisa semântica melhora a precisão e a RECALL com a adição dos seguintes recursos:

| Recurso | Descrição |
|---------|-------------|
| [Verificação ortográfica](speller-how-to-add.md) | Corrige erros antes que os termos de consulta alcancem o mecanismo de pesquisa. |
| [Classificação semântica](semantic-ranking.md) | Usa o contexto ou o significado semântico para calcular uma nova Pontuação de relevância. |
| [Legendas semânticas e destaques](semantic-how-to-query-request.md) | Frases e frases de um documento que melhor resumem o conteúdo, com destaques sobre passagens de chave para facilitar a varredura. As legendas que resumem um resultado são úteis quando campos de conteúdo individuais são muito densos para a página de resultados. O texto realçado eleva os termos e frases mais relevantes para que os usuários possam determinar rapidamente por que uma correspondência foi considerada relevante. |
| [Respostas semânticas](semantic-answers.md) | Uma subestrutura opcional e adicional retornada de uma consulta semântica. Ele fornece uma resposta direta para uma consulta que se parece com uma pergunta. |

### <a name="order-of-operations"></a>Ordem das operações

Os componentes da pesquisa semântica estendem o pipeline de execução de consulta existente em ambas as direções. Se você habilitar a correção ortográfica, o [Verificador ortográfico](speller-how-to-add.md) corrigirá erros de digitação no início, antes que os termos da consulta atinjam o mecanismo de pesquisa.

:::image type="content" source="media/semantic-search-overview/semantic-workflow.png" alt-text="Componentes semânticos na execução da consulta" border="true":::

A execução da consulta prossegue como de costume, com análise de termo, análise e verificações sobre os índices invertidos. O mecanismo recupera documentos usando correspondência de token e pontua os resultados usando o [algoritmo de Pontuação de similaridade padrão](index-similarity-and-scoring.md#similarity-ranking-algorithms). As pontuações são calculadas com base no grau de similaridade lingüística entre os termos da consulta e os termos correspondentes no índice. Se você os tiver definido, os perfis de Pontuação também serão aplicados neste estágio. Os resultados são passados para o subsistema de pesquisa semântica.

Na etapa de preparação, o documento Corpus retornado do conjunto de resultados inicial é analisado no nível de sentença e de parágrafo para localizar passagens que resumem cada documento. Em contraste com a pesquisa de palavra-chave, essa etapa usa a leitura e a compreensão da máquina para avaliar o conteúdo. Como parte da composição de resultado, uma consulta semântica retorna legendas e respostas. Para Formula-los, a pesquisa semântica usa a representação de linguagem para extrair e realçar as passagens de chave que melhor resumem um resultado. Se a consulta de pesquisa for uma pergunta e as respostas forem solicitadas-a resposta também incluirá uma passagem de texto que melhor responde à pergunta, conforme expresso pela consulta de pesquisa. Para legendas e respostas, o texto existente é usado na formulação. Os modelos semânticos não compõem novas frases ou frases do conteúdo disponível, nem aplicam a lógica para chegar a novas conclusões. Em suma, o sistema nunca retornará conteúdo que ainda não exista.

Os resultados são então pontuados novamente com base na [similaridade conceitual](semantic-ranking.md) dos termos de consulta.

Para usar os recursos semânticos em consultas, você precisará fazer pequenas modificações na [solicitação de pesquisa](semantic-how-to-query-request.md), mas nenhuma configuração extra ou reindexação será necessária.

## <a name="availability-and-pricing"></a>Disponibilidade e preços

Os recursos semânticos estão disponíveis por meio do [registro de inscrição](https://aka.ms/SemanticSearchPreviewSignup), nos serviços de pesquisa criados em uma camada Standard (S1, S2, S3), localizada em uma destas regiões: Norte EUA Central, oeste dos EUA, oeste dos EUA 2, leste dos EUA 2, Europa setentrional Europa Ocidental. 

A correção ortográfica está disponível nas mesmas regiões, mas não tem restrições de camada. Se você tiver um serviço existente que atenda aos critérios de camada e região, somente a inscrição será necessária.

Entre a inicialização da visualização em 2 de março até 1º de abril, a correção ortográfica e a classificação semântica são oferecidas gratuitamente. Após 1º de abril, os custos computacionais da execução dessa funcionalidade se tornarão um evento faturável. O custo esperado é de cerca de USD $500/mês para consultas de 250.000. Você pode encontrar informações detalhadas de custo documentadas na [página de preços pesquisa cognitiva](https://azure.microsoft.com/pricing/details/search/) e em [estimar e gerenciar custos](search-sku-manage-costs.md).

## <a name="next-steps"></a>Próximas etapas

Um novo tipo de consulta permite a classificação de relevância e as estruturas de resposta da pesquisa semântica.

[Crie uma consulta semântica](semantic-how-to-query-request.md) para começar. Ou leia os artigos a seguir para obter informações relacionadas.

+ [Adicionar verificação ortográfica aos termos da consulta](speller-how-to-add.md)
+ [Retornar uma resposta semântica](semantic-answers.md)
+ [Classificação semântica](semantic-ranking.md)