---
title: Padrões-adição de precisão-LUIS
titleSuffix: Azure Cognitive Services
description: Adicione modelos de padrão para melhorar a precisão da previsão em aplicativos Reconhecimento vocal (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 7b6c8ba1517de44d01ffbceec812749403465e63
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638082"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>Como adicionar padrões para melhorar a precisão da previsão
Depois que um aplicativo LUIS recebe o ponto de extremidade declarações, use um [padrão](luis-concept-patterns.md) para melhorar a precisão da previsão para declarações que revela um padrão em ordem de palavras e escolha de palavra. Padrões usam [sintaxe](luis-concept-patterns.md#pattern-syntax) específica para indicar o local de: [entidades](luis-concept-entity-types.md), [funções](luis-concept-roles.md)de entidade e texto opcional.

## <a name="add-template-utterance-to-create-pattern"></a>Adicionar declaração modelo para criar o padrão
1. Abra o aplicativo selecionando seu nome na página **Meus Aplicativos** e, em seguida, selecione **Padrões** no painel esquerdo, sob **Melhorar o desempenho do aplicativo**.

    ![Captura de tela da lista de Padrões](./media/luis-how-to-model-intent-pattern/patterns-1.png)

2. Selecione a intenção correta para o padrão. 

    ![Selecionar a intenção](./media/luis-how-to-model-intent-pattern/patterns-2.png)

3. Na caixa de texto do modelo, digite a declaração modelo e selecione Enter. Quando quiser inserir o nome da entidade, use a sintaxe de entidade de padrão correta. Comece a sintaxe de entidade com `{`. A lista de entidades será exibida. Selecione a entidade correta e, depois, Enter. 

    ![Captura de tela de entidade do padrão](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    Se sua entidade incluir uma [função](luis-concept-roles.md), indique a função com um único dois `:`-pontos,, após o `{Location:Origin}`nome da entidade, como. A lista de funções das entidades é exibida em uma lista. Selecione a função e, depois, selecione Enter. 

    ![Captura de tela da entidade com a função](./media/luis-how-to-model-intent-pattern/patterns-4.png)

    Depois de selecionar a entidade correta, termine de inserir o padrão e, em seguida, selecione Enter. Quando você terminar de inserir padrões, [treine](luis-how-to-train.md) seu aplicativo.

    ![Captura de tela do padrão inserido com os dois tipos de entidades](./media/luis-how-to-model-intent-pattern/patterns-5.png)

## <a name="train-your-app-after-changing-model-with-patterns"></a>Treinar seu aplicativo após alterar o modelo com os padrões
Depois de adicionar, editar, remover ou reatribuir um padrão, [treine](luis-how-to-train.md) e [publique](luis-how-to-publish-app.md) seu aplicativo para que as suas alterações afetem as consultas de ponto de extremidade. 

<a name="search-patterns"></a>
<a name="edit-a-pattern"></a>
<a name="reassign-individual-pattern-to-different-intent"></a>
<a name="reassign-several-patterns-to-different-intent"></a>
<a name="delete-a-single-pattern"></a>
<a name="delete-several-patterns"></a>
<a name="filter-pattern-list-by-entity"></a>
<a name="filter-pattern-list-by-intent"></a>
<a name="remove-entity-or-intent-filter"></a>
<a name="add-pattern-from-existing-utterance-on-intent-or-entity-page"></a>

## <a name="use-contextual-toolbar"></a>Usar barra de ferramentas contextual

A barra de ferramentas contextual acima da lista de padrões permite que você:

* Pesquisar padrões
* Editar um padrão
* Reatribuir padrão individual a uma intenção diferente
* Reatribuir vários padrões a uma intenção diferente
* Excluir um padrão único
* Excluir vários padrões
* Filtrar a lista de padrões por entidade
* Filtro-padrão-listar por intenção
* Remover o filtro de entidade ou intenção
* Adicionar padrão de declaração existente na página de intenção ou de entidade

## <a name="next-steps"></a>Próximas etapas

* Saiba como [criar um padrão](luis-tutorial-pattern.md) com um padrão. qualquer função e com um tutorial.
* Saiba como [treinar](luis-how-to-train.md) seu aplicativo.
