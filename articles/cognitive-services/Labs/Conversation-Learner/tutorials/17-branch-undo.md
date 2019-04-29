---
title: Como usar operações desfazer e ramificação com um modelo de Aprendiz de Conversa - Serviços Cognitivos da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba como usar operações desfazer e ramificação com um modelo de Aprendiz de Conversa.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: cca7b42c83be3ca428509ea48c387a1c35ddb35a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60640168"
---
# <a name="how-to-use-branching-and-undo-operations"></a>Como usar operações desfazer e ramificação
Neste tutorial, apresentaremos as operações desfazer e ramificação.


## <a name="details"></a>Detalhes
### <a name="undo"></a>Desfazer
Permite que o desenvolvedor "desfaça" a última entrada do usuário ou escolha de ação. Na verdade, "desfazer" realmente cria um novo diálogo e o reproduz até a etapa anterior.  Isso significa que o retorno de chamada de detecção de entidade e as chamadas à API no diálogo serão chamados novamente.

### <a name="branch"></a>Branch
Cria um novo diálogo de treinamento, que começa da mesma forma que um diálogo de treinamento existente – isso economiza o esforço de reintroduzir manualmente os turnos do diálogo. Na realidade, “ramificação” cria um novo diálogo e reproduz o diálogo de treinamento existente até a etapa selecionada.  Isso significa que o retorno de chamada de detecção de entidade e as chamadas à API no diálogo serão chamados novamente.


## <a name="requirements"></a>Requisitos
Este tutorial exige que o bot que recebe pedidos de pizza esteja em execução:

    npm run demo-pizza

### <a name="open-or-import-the-demo"></a>Abrir ou importar a demonstração

Se você já trabalhou com este tutorial de pedidos de pizza, então basta abrir esse modelo da lista na interface do usuário web. Caso contrário, você precisará clicar em "Tutoriais de importação" e selecionar o modelo chamado "Demo-PizzaOrder".

## <a name="undo"></a>Desfazer

Aqui está um exemplo de como verificar o recurso `Undo` em ação:

### <a name="training-dialogs"></a>Diálogos de treinamento
1. No painel esquerdo, clique em "Diálogos de Treinamento" e, em seguida, no botão `New Train Dialog`.
2. Digite “Pedir uma pizza”.
3. Clique no botão `Score Actions`.
4. Clique para selecionar “O que você gostaria em sua pizza?”
5. Digite “qualquer coisa”.
6. Clique no botão `Undo`.
    - A última entrada é removida, deixando a última resposta do Bot de "O que você deseja em seu pizza?"

## <a name="branch"></a>Branch

Para esta demonstração, vamos abrir uma caixa de diálogo de treinamento existente e criar uma nova caixa de diálogo de treinamento pela ramificação.

1. No painel esquerdo, clique em "Diálogos de Treinamento".
2. Observe a grade, você deve ver apenas um treinamento que começa com "novo pedido".
3. Na grade, clique em “novo pedido” para abrir o Diálogo de Treinamento existente.
4. Clique no último “Não” no diálogo.
5. Clique no ícone de "Ramificação", ele está marcado em vermelho nesta imagem:
    - ![](../media/tutorial15_branch.PNG)
    - Todo o Diálogo de Treinamento antes de "não" é copiado em um novo Diálogo de Treinamento.
    - Isso evita que você entre novamente nos turnos anteriores para explorar uma nova "ramificação" de conversa a partir desse ponto.
6. Digite "Sim", pressione inserir.
7. Clique no botão `Score Actions`.
    - Neste ponto o Bot escolhe automaticamente uma resposta, mas não gostamos da resposta. Portanto, vamos alterá-la.
8. Clique na última resposta do Bot.
    - Isso nos permitirá selecionar uma resposta diferente.
9. Selecione "UseLastToppings".
10. Clique no botão `Score Actions`.
    - Novamente o Bot escolhe automaticamente uma resposta. Ele deve dizer, "Você tem linguiça, queijo e cogumelos na sua pizza.". 
    - Desta vez, gostamos da resposta. Portanto, a mantemos.
11. Clique no botão `Score Actions`.
    - Novamente o Bot escolhe automaticamente uma resposta, ela deverá dizer, "Você gostaria de mais alguma coisa?"
12. Digite "não".
13. Clique no botão `Save Branch`.
14. Observe que a grade agora tem dois treinamentos que começa com "novo pedido".
    - Uma delas é a que você usou para a ramificação.
    - E a outra é a versão ramificada que você acabou de salvar.
    - Clique em cada uma delas para verificar essas expectativas.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Controle de versão e marcação](./18-version-tag.md)
