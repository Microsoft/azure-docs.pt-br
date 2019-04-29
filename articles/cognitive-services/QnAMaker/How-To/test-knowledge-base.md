---
title: Como testar uma base de dados de conhecimento – QnA Maker
titlesuffix: Azure Cognitive Services
description: Testar a base de conhecimento do QnA Maker é uma parte importante de um processo iterativo para melhorar a precisão das respostas que estão sendo retornadas. É possível testar a base de dados de conhecimento por meio de uma interface de chat avançada que também permite fazer edições.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 12/17/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 20ebcb502e03f2d817fe18624d8c790e920c667f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61371794"
---
# <a name="test-your-knowledge-base-interactively-in-qna-maker"></a>Teste sua base de dados de conhecimento interativamente no QnA Maker

Testar a base de conhecimento do QnA Maker é uma parte importante de um processo iterativo para melhorar a precisão das respostas que estão sendo retornadas. É possível testar a base de dados de conhecimento por meio de uma interface de chat avançada que também permite fazer edições.

## <a name="test-answer-matching"></a>Testar correspondência de resposta

1.  Acesse a base de dados de conhecimento selecionando o nome na página  **Minhas bases de dados de conhecimento**.
2.  Para acessar o painel extensível Testar, selecione  **Testar**  no painel superior do seu aplicativo.

    ![Acessar painel de teste](../media/qnamaker-how-to-test-kb/access-test.png)

3.  Insira uma consulta na caixa de texto e selecione Enter.

4.  A resposta da melhor correspondência da base de dados de conhecimento é retornada como a resposta.

## <a name="clear-test-panel"></a>Limpar painel de teste

Para limpar todas as consultas de teste inseridas e seus resultados do console de teste, selecione  **Iniciar de novo**  no canto superior esquerdo do Painel de teste.

## <a name="close-test-panel"></a>Fechar painel de teste

Para fechar o Painel de teste, selecione o botão  **Testar**  novamente. Enquanto o painel de Teste estiver aberto, não será possível editar o conteúdo da Base de Dados de Conhecimento.

## <a name="inspect-score"></a>Inspecionar pontuação

Inspecione detalhes do resultado do teste no painel Inspecionar.

1.  Com o painel deslizante de teste aberto, selecione  **Inspecionar**  para obter mais detalhes sobre essa resposta.

    ![Inspecione as respostas](../media/qnamaker-how-to-test-kb/inspect.png)

2.  O painel de Inspeção é exibido. O painel inclui a principal intenção de pontuação e as entidades identificadas. O painel mostra o resultado da declaração selecionada.

## <a name="correct-the-top-scoring-answer"></a>Corrigir a resposta de pontuação máxima

Se a resposta de pontuação mais alta estiver incorreta, selecione a resposta correta na lista e selecione **Salvar e Treinar**.

![Corrigir a resposta de pontuação máxima](../media/qnamaker-how-to-test-kb/choose-answer.png)

## <a name="add-alternate-questions"></a>Adicionar perguntas alternativas

É possível adicionar formas alternativas de uma pergunta a uma determinada resposta. Digite as respostas alternativas na caixa de texto e clique em entrar para adicioná-las. Selecione **Salvar e Treinar** para armazenar as atualizações.

![Adicionar perguntas alternativas](../media/qnamaker-how-to-test-kb/add-alternate-question.png)

## <a name="add-a-new-answer"></a>Adicionar uma nova resposta

Você poderá adicionar uma nova resposta, se alguma das respostas existentes que foram correspondidas estiver incorreta ou a resposta não existir na base de dados de conhecimento (nenhuma boa correspondência encontrada na base de dados de conhecimento). Insira a nova resposta à pergunta atual na caixa de texto e pressione Enter para adicioná-la. 

Selecione **Salvar e Treinar** para persistir essa resposta. Um novo par de perguntas e respostas foi adicionado à base de dados de conhecimento.

![Adicionar novo par de pergunta e resposta](../media/qnamaker-how-to-test-kb/add-answer.png)

> [!NOTE]
> Todas as edições da base de dados de conhecimento somente serão salvas quando você pressionar o botão **Salvar e Treinar**.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Publicar uma base de dados de conhecimento](./publish-knowledge-base.md)
