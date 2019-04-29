---
title: Como usar entidades multivalor com um modelo do Conversation Learner - Serviços Cognitivos da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba como usar entidades multivalor com um modelo do Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 07550ed9c78aa584e55403d225eef4bcff33b057
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60708036"
---
# <a name="how-to-use-multi-value-entities-with-a-conversation-learner-model"></a>Como usar entidades multivalor com um modelo de Aprendiz de Conversa
Este tutorial mostra a propriedade multivalor de entidades.

## <a name="video"></a>Vídeo

[![Visualização de Tutorial de entidades com vários valores](https://aka.ms/cl_Tutorial_v3_MultiValued_Preview)](https://aka.ms/cl_Tutorial_v3_MultiValued)

## <a name="requirements"></a>Requisitos
Este tutorial exige que Bot de tutorial esteja em execução

    npm run tutorial-general

## <a name="details"></a>Detalhes
Uma entidade multivalor acumula os valores em uma lista em vez de armazenar um único valor.  Essas entidades são úteis quando os usuários podem especificar mais de um valor. Como por exemplo, ingredientes em uma pizza.

As entidades marcadas como multivalores terão cada instância reconhecida da entidade anexada a uma lista na memória do Bot. Acrescenta reconhecimento subsequente para a entidade valor, em vez de substituir.

## <a name="steps"></a>Etapas

Inicie na home page na interface de usuário da Web.

### <a name="create-the-model"></a>Criar o modelo

1. Selecione **novo modelo**.
2. Insira **MultiValueEntities** para **nome**.
3. Selecione **Criar**.

### <a name="entity-creation"></a>Criação da entidade

1. Selecione **entidades** no painel esquerdo, em seguida, **nova entidade**.
2. Selecione **personalizado treinado** para **tipo de entidade**.
3. Insira **ingredientes** para **nome da entidade**.
4. Verifique **múltiplos** para habilitar a entidade de acumular um ou mais valores.
5. Verifique **negável**.
6. Selecione **Criar**.

![](../media/T07_entity_create.png)

### <a name="create-the-first-action"></a>Criar a primeira ação

1. Selecione **ações** no painel esquerdo, em seguida, **nova ação**.
2. Insira **aqui estão suas ingredientes: $toppings** para **de resposta do Bot...** . O sinal de dólar à esquerda indica uma referência de entidade.
3. Selecione **Criar**.

![](../media/T07_action_create_1.png)

### <a name="create-the-second-action"></a>Criar a segunda ação

1. Selecione **ações** no painel esquerdo, em seguida, **nova ação**.
2. Insira **quais ingredientes gostaria?** para **de resposta do Bot...** .
3. Insira **ingredientes** para **desqualificação qualifica**.
4. Selecione **Criar**.

Agora você tem duas ações.

![](../media/T07_action_create_2.png)

### <a name="train-the-model"></a>Treinar o modelo

1. Selecione **Train Dialogs** no painel esquerdo, em seguida, **caixa de diálogo do novo treinamento**.
2. Insira **Olá** para a declaração do usuário no painel à esquerda de bate-papo.
3. Selecione **pontuar ações**.
4. Selecione **quais ingredientes gostaria?** na lista de ações. O percentual é 100%, como a ação só é válida com base em restrições.
5. Insira **Queijo e Cogumelos** para a declaração do usuário no painel à esquerda de bate-papo.
6. Realçar **queijo** , em seguida, selecione **+ ingredientes**.
7. Realçar **Cogumelos** , em seguida, selecione **+ ingredientes**.
8. Selecione **pontuar ações**.
9. Selecione **aqui estão suas ingredientes: $toppings** na lista de ações.
10. Insira **adicionar pepper** para a expressão próxima do usuário no painel à esquerda de bate-papo.
11. Realçar **pepper** , em seguida, selecione **+ ingredientes**.
12. Selecione **pontuar ações**.
13. Selecione **aqui estão suas ingredientes: $toppings** na lista de ações.
14. Insira **remover queijo** para a expressão terceiro do usuário no painel à esquerda de bate-papo.
15. Realçar **queijo** , em seguida, selecione **-ingredientes**.
16. Selecione **pontuar ações**.
17. Selecione **aqui estão suas ingredientes: $toppings** na lista de ações.

![](../media/T07_training.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Entidades previamente treinadas](./08-pre-trained-entities.md)
