---
title: Adicionar tentativas-LUIS
titleSuffix: Azure Cognitive Services
description: Adicione intenções ao seu aplicativo do LUIS para identificar grupos de perguntas ou comandos que têm as mesmas intenções.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.subservice: language-understanding
ms.topic: article
ms.date: 07/29/2019
ms.author: diberry
ms.service: cognitive-services
ms.openlocfilehash: 84799c2866e7b887a7b509280b073814e7653638
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638211"
---
# <a name="add-intents-to-determine-user-intention-of-utterances"></a>Adicionar intenções para determinar a intenção do usuário de expressões

Adicione [intents](luis-concept-intent.md) ao seu aplicativo LUIS para identificar grupos de perguntas ou comandos que tenham a mesma intenção. 

Os objetivos são gerenciados a partir da seção **Compilação** da barra de navegação superior e, a seguir, das **solicitações** do painel esquerdo. 

## <a name="add-intent"></a>Adicionar intenção

1. Na página **Inteções**, selecione **Criar nova intenção**.

1. Na caixa de diálogo **Criar nova intenção**, insira o nome da intenção, `GetEmployeeInformation`, e clique em **Concluído**.

    ![Adicionar intenção](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

## <a name="add-an-example-utterance"></a>Adicionar uma expressão de exemplo

Enunciados de exemplo são exemplos de texto de comandos ou perguntas do usuário. Para ensinar o Entendimento de Linguagem (LUIS), você precisa adicionar expressões de exemplo a uma intenção.

1. Na página de detalhes da intenção **GetEmployeeInformation**, insira uma expressão relevante que você espera de seus usuários, como `Does John Smith work in Seattle?` na caixa de texto abaixo do nome da intenção, e pressione Enter.
 
    ![Captura de tela da página Detalhes de intenções, com o enunciado realçado](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png) 

    LUIS converte todos os enunciados em minúsculas e adiciona espaços em torno de tokens, como hífens.

<a name="#intent-prediction-discrepancy-errors"></a>

## <a name="intent-prediction-errors"></a>Erros de previsão de intenção 

Um exemplo de expressão em uma intenção pode ter um erro de previsão de intenção entre a intenção em que o exemplo expressão está atualmente em e a intenção de previsão determinada durante o treinamento. 

Para localizar erros de previsão do expressão e corrigi-los, use as opções de **avaliação** da opção de **filtro** de incorreto e não claro combinados com a opção **Exibir** da **exibição detalhada**. 

![Para localizar erros de previsão de expressão e corrigi-los, use a opção de filtro.](./media/luis-how-to-add-intents/find-intent-prediction-errors.png)

Quando os filtros e a exibição são aplicados e há um exemplo de declarações com erros, a lista de expressão de exemplo mostra o declarações e os problemas.

![! [Quando os filtros e a exibição são aplicados e há um exemplo de declarações com erros, a lista de expressão de exemplo mostra o declarações e os problemas.] (./media/luis-how-to-add-intents/find-errors-in-utterances.png)](./media/luis-how-to-add-intents/find-errors-in-utterances.png#lightbox)

Cada linha mostra a pontuação de previsão do treinamento atual para o exemplo expressão, a Pontuação do rival mais próximo, que é a diferença nessas duas pontuações. 

### <a name="fixing-intents"></a>Corrigindo tentativas

Para saber como corrigir erros de previsão de intenção, use o [painel de resumo](luis-how-to-use-dashboard.md). O painel de resumo fornece análise para o último treinamento da versão ativa e oferece as principais sugestões para corrigir seu modelo.  

## <a name="add-a-custom-entity"></a>Adicione uma entidade personalizada

Uma vez que um enunciado é adicionado a um intento, você pode selecionar o texto de dentro do enunciado para criar uma entidade personalizada. Uma entidade personalizada é uma maneira de marcar texto para extração, junto com a intenção correta. 

Consulte [Adicionar entidade ao expressão](luis-how-to-add-example-utterances.md) para saber mais.

## <a name="entity-prediction-discrepancy-errors"></a>Erros de discrepância de previsão de entidade 

A entidade é sublinhada em vermelho para indicar uma [discrepância de predição da entidade](luis-how-to-add-example-utterances.md#entity-status-predictions). Como esta é a primeira ocorrência de uma entidade, não há exemplos suficientes para que o LUIS tenha a confiança de que esse texto está marcado com a entidade correta. Essa discrepância é removida quando o aplicativo é treinado. 

![Captura de tela da página de detalhes do Intents, nome da entidade personalizada realçado em azul](./media/luis-how-to-add-intents/create-custom-entity-name-blue-highlight.png) 

O texto é destacado em azul, indicando uma entidade.  

## <a name="add-a-prebuilt-entity"></a>Adicionar uma entidade predefinida

Para obter informações, consulte [Entidade pré-criada](luis-how-to-add-entities.md#add-a-prebuilt-entity-to-your-app).

## <a name="using-the-contextual-toolbar"></a>Usando a barra de ferramentas contextual

Quando um ou mais exemplos de declarações são selecionados na lista, marcando a caixa à esquerda de um expressão, a barra de ferramentas acima da lista expressão permite que você execute as seguintes ações:

* Reatribuir intenção: mover a (s) declaração (ões) para uma intenção diferente
* Excluir utterance(s)
* Filtros de entidades: exiba apenas declarações contendo entidades filtradas
* Mostrar todos/Apenas erros: mostrar declarações com erros de previsão ou mostrar todos os pronunciamentos
* Visualização Entidades/Tokens: mostra a visualização de entidades com nomes de entidades ou mostra texto bruto de expressão
* Lupa: busca por enunciados contendo texto específico

## <a name="working-with-an-individual-utterance"></a>Trabalhando com uma expressão individual

As seguintes ações podem ser executadas em uma declaração individual a partir do menu de reticências à direita da expressão:

* Editar: alterar o texto da declaração
* Excluir: Remova a expressão a intenção. Se você ainda quer o enunciado, um método melhor é movê-lo para a intenção **Nenhum**. 
* Adicionar um padrão: um padrão permite que você use uma declaração comum e marque texto substituível e texto ignorável, reduzindo assim a necessidade de mais declarações para a intenção. 

A coluna **Labeled intent** permite que você altere a intenção da expressão.

## <a name="train-your-app-after-changing-model-with-intents"></a>Treinar seu aplicativo após alterar o modelo com intenções

Depois de adicionar, editar ou remover intenções, [train](luis-how-to-train.md) e [publique](luis-how-to-publish-app.md) seu aplicativo para que as alterações sejam aplicadas às consultas de ponto de extremidade. 

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como adicionar [declarações de exemplo](luis-how-to-add-example-utterances.md) com entidades. 
