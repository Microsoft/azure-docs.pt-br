---
title: Painel-Reconhecimento vocal-LUIS
titleSuffix: Azure Cognitive Services
description: Corrija as intenções com o painel de análise, uma ferramenta de relatório visualizada.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/22/2019
ms.author: diberry
ms.openlocfilehash: 42dfe4af56149e4305d076b0427f15038a01fadc
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563623"
---
# <a name="how-to-use-the-dashboard-to-improve-your-app"></a>Como usar o painel para melhorar seu aplicativo

Encontre e corrija problemas com as intenções do seu aplicativo treinado ao usar o exemplo declarações. O painel exibe informações gerais do aplicativo, com destaques de tentativas que devem ser corrigidas. 

Examinar a análise do painel é um processo iterativo, repita conforme você altera e aprimora seu modelo.

Esta página não terá uma análise relevante para aplicativos que não tenham nenhum exemplo de declarações em tentativas, conhecidas como aplicativos somente de _padrão_ . 

## <a name="what-issues-can-be-fixed-from-dashboard"></a>Quais problemas podem ser corrigidos do painel?

Os três problemas abordados no painel são:

|Problema|Cor do gráfico|Explicação|
|--|--|--|
|Desequilíbrio de dados|-|Isso ocorre quando a quantidade de declarações de exemplo varia significativamente. Todas as intenções precisam ter _aproximadamente_ o mesmo número de exemplos declarações-exceto a intenção None. Ele deve ter apenas 10%-15% da quantidade total de declarações no aplicativo.<br><br> Se os dados forem desequilibrados, mas a precisão da intenção estiver acima de determinado limite, esse desequilíbrio não será relatado como um problema.<br><br>**Comece com esse problema – pode ser a causa raiz dos outros problemas.**|
|Não limpar previsões|Laranja|Isso ocorre quando a principal intenção e as pontuações da próxima tentativa são próximas o suficiente para que possam se virar no próximo treinamento, devido à [amostragem negativa](luis-how-to-train.md#train-with-all-data) ou mais declarações de exemplo adicionados à intenção. |
|Previsões incorretas|Vermelho|Isso ocorre quando um exemplo de expressão não é previsto para a intenção rotulada (a intenção em que se encontra).|

As previsões corretas são representadas com a cor azul.

O painel mostra esses problemas e informa quais tentativas são afetadas e sugere o que você deve fazer para melhorar o aplicativo. 

## <a name="before-app-is-trained"></a>Antes de o aplicativo ser treinado 

Antes de treinar o aplicativo, o painel não contém sugestões para correções. Treine seu aplicativo para ver essas sugestões.  

## <a name="check-your-publishing-status"></a>Verificar o status de publicação

O cartão de **status de publicação** contém informações sobre a última publicação da versão ativa. 

Verifique se a versão ativa é a versão que você deseja corrigir. 

![O painel mostra os serviços externos do aplicativo, as regiões publicadas e os acertos de ponto de extremidade agregados.](./media/luis-how-to-use-dashboard/analytics-card-1-shows-app-summary-and-endpoint-hits.png)

Isso também mostra quaisquer serviços externos, regiões publicadas e ocorrências de ponto de extremidade agregadas. 

## <a name="review-training-evaluation"></a>Examinar a avaliação do treinamento

O cartão de **avaliação de treinamento** contém o resumo agregado da precisão geral do seu aplicativo por área. A pontuação indica a qualidade da intenção. 

![O cartão de avaliação de treinamento contém a primeira área de informações sobre a precisão geral do aplicativo.](./media/luis-how-to-use-dashboard/analytics-card-2-shows-app-overall-accuracy.png)

O gráfico indica as intenções previstas corretamente e as áreas problemáticas com cores diferentes. À medida que você aprimora o aplicativo com as sugestões, essa Pontuação aumenta. 

As correções sugeridas são separadas por tipo de problema e são as mais significativas para seu aplicativo. Se você preferir revisar e corrigir problemas por tentativa, use o cartão **[tentativas com erros](#intents-with-errors)** na parte inferior da página. 

Cada área com problema tem tentativas que precisam ser corrigidas. Quando você seleciona o nome da intenção, a página **intenção** é aberta com um filtro aplicado ao declarações. Esse filtro permite que você se concentre nos declarações que estão causando o problema.

### <a name="compare-changes-across-versions"></a>Comparar alterações entre versões

Crie uma nova versão antes de fazer alterações no aplicativo. Na nova versão, faça as alterações sugeridas no exemplo de declarações da intenção e, em seguida, treine novamente. No cartão de avaliação de **treinamento** da página do painel, use o **Mostrar alteração de versão treinada** para comparar as alterações. 

![Comparar alterações entre versões](./media/luis-how-to-use-dashboard/compare-improvement-across-versions.png)

### <a name="fix-version-by-adding-or-editing-example-utterances-and-retraining"></a>Corrija a versão adicionando ou editando o exemplo declarações e retreinando

O principal método de corrigir seu aplicativo será adicionar ou editar o exemplo declarações e treinar novamente. O declarações novo ou alterado precisa seguir as diretrizes para [declarações variados](luis-concept-utterance.md).

Adicionar exemplo declarações deve ser feito por alguém que:

* tem um alto grau de compreensão de quais declarações estão em diferentes intenções
* Sabe como declarações em uma única tentativa pode ser confundida com outra intenção
* é capaz de decidir se duas intenções, que são frequentemente confundidas umas com as outras, devem ser recolhidas em uma única intenção e os diferentes dados extraídos com entidades

### <a name="patterns-and-phrase-lists"></a>Listas de padrões e frases

A página de análise não indica quando usar [padrões](luis-concept-patterns.md) ou [listas de frases](luis-concept-feature.md). Se você adicioná-los, ele poderá ajudar com previsões incorretas ou não claras, mas não ajudará com desequilíbrio de dados. 

### <a name="review-data-imbalance"></a>Examinar desequilíbrio de dados

Comece com esse problema – pode ser a causa raiz dos outros problemas.

A lista de intenções de desequilíbrio de **dados** mostra tentativas que precisam de mais declarações para corrigir o desequilíbrio de dados. 

**Para corrigir esse problema**:

* Adicione mais declarações à intenção e, em seguida, treine novamente. 

Não adicione declarações à intenção None, a menos que seja sugerido no painel.

> [!Tip]
> Use a terceira seção da página, **declarações por intenção** , com a configuração de **declarações (número)** , como um guia visual rápido do qual as tentativas precisam de mais declarações.  
    ![Use ' declarações (Number) ' para localizar tentativas com desequilíbrio de dados.](./media/luis-how-to-use-dashboard/predictions-per-intent-number-of-utterances.png)

### <a name="review-incorrect-predictions"></a>Revisar previsões incorretas

A lista de intenção de **previsão incorreta** mostra as intenções que têm declarações, que são usadas como exemplos para uma intenção específica, mas são previstas para diferentes intenções. 

**Para corrigir esse problema**:

* Edite declarações para ser mais específico para a intenção e treine novamente.
* Combine tentativas se declarações estiverem alinhados muito bem e treine novamente.

### <a name="review-unclear-predictions"></a>Revisar previsões não claras

A lista de intenções de **previsão não clara** mostra intenções com declarações com pontuações de previsão que não são muito suficientes de seu rival mais próximo, que a principal intenção do expressão pode mudar no próximo treinamento, devido à [amostragem negativa](luis-how-to-train.md#train-with-all-data).

**Para corrigir esse problema**;

* Edite declarações para ser mais específico para a intenção e treine novamente.
* Combine tentativas se declarações estiverem alinhados muito bem e treine novamente.

## <a name="utterances-per-intent"></a>Declarações por tentativa

Este cartão mostra a integridade geral do aplicativo entre as intenções. Ao corrigir tentativas e readaptação, continue a dar uma olhada no cartão para problemas.

O gráfico a seguir mostra um aplicativo bem equilibrado com quase nenhum problema a ser corrigido.

![O gráfico a seguir mostra um aplicativo bem equilibrado com quase nenhum problema a ser corrigido.](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-balance.png)

O gráfico a seguir mostra um aplicativo com baixo balanceamento com muitos problemas a serem corrigidos.

![O gráfico a seguir mostra um aplicativo bem equilibrado com quase nenhum problema a ser corrigido.](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-imbalance.png)

Passe o mouse sobre a barra de cada uma das intenções para obter informações sobre a intenção. 

![O gráfico a seguir mostra um aplicativo bem equilibrado com quase nenhum problema a ser corrigido.](./media/luis-how-to-use-dashboard/utterances-per-intent-with-details-of-errors.png)

Use o recurso **classificar por** para organizar as tentativas por tipo de problema para que você possa se concentrar nas tentativas mais problemáticas com esse problema. 

## <a name="intents-with-errors"></a>Tentativas com erros

Este cartão permite que você examine problemas para uma intenção específica. A exibição padrão desse cartão é a tentativa mais problemática, para que você saiba onde concentrar seus esforços.

![O cartão tentativas com erros permite que você examine os problemas para uma intenção específica. O cartão é filtrado para as tentativas mais problemáticas, por padrão, para que você saiba onde concentrar seus esforços.](./media/luis-how-to-use-dashboard/most-problematic-intents-with-errors.png)

O gráfico de rosca superior mostra os problemas com a intenção entre os três tipos de problema. Se houver problemas nos três tipos de problema, cada tipo terá seu próprio gráfico abaixo, juntamente com quaisquer tentativas de rival. 

### <a name="filter-intents-by-issue-and-percentage"></a>Filtrar tentativas por problema e percentual

Esta seção do cartão permite que você encontre exemplos de declarações que estão caindo fora do limite de erros. Idealmente, você deseja que as previsões corretas sejam significativas. Essa porcentagem é orientada por clientes e negócios. 

Determine os percentuais de limite com os quais você está familiarizado para sua empresa. 

O filtro permite que você encontre tentativas com um problema específico:

|Filtro|Porcentagem sugerida|Finalidade|
|--|--|--|
|Tentativas mais problemáticas|-|**Comece aqui** -corrigir o declarações nesta tentativa melhorará o aplicativo mais do que outras correções.|
|Corrigir previsões abaixo|60%|Esta é a porcentagem de declarações na intenção selecionada que estão corretas, mas que têm uma pontuação de confiança abaixo do limite. |
|Não limpar previsões acima|15%|Essa é a porcentagem de declarações na intenção selecionada que são confundidas com a intenção de rival mais próxima.|
|Previsões incorretas acima|15%|Este é o percentual de declarações na intenção selecionada que está incorretamente prevista. |

### <a name="correct-prediction-threshold"></a>Limite de previsão correto

O que é uma pontuação de confiança de previsão confiável para você? No início do desenvolvimento de aplicativos, 60% pode ser seu destino. Use as **previsões corretas abaixo** com a porcentagem de 60% para localizar qualquer declarações na intenção selecionada que precisa ser corrigida.

### <a name="unclear-or-incorrect-prediction-threshold"></a>Limite de previsão não claro ou incorreto

Esses dois filtros permitem que você encontre declarações na intenção selecionada além do limite. Você pode considerar esses dois percentuais como porcentagens de erro. Se você estiver familiarizado com uma taxa de erro de 10-15% para previsões, defina o limite de filtro para 15% para localizar todos os declarações acima desse valor. 

## <a name="next-steps"></a>Próximas etapas

* [Gerenciar seus recursos do Azure](luis-how-to-azure-subscription.md)
