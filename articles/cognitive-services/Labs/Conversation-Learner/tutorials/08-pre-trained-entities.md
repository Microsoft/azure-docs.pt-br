---
title: Como adicionar entidades previamente treinadas a um modelo de Conversation Learner – Serviços Cognitivos da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba como adicionar entidades previamente treinadas a um modelo de Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: dade442cb04f79ef75e65d6eb29128b105e72b40
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60707679"
---
# <a name="how-to-add-pre-trained-entities"></a>Como adicionar entidades previamente treinadas
Este tutorial mostra como adicionar entidades previamente treinadas ao modelo de Conversation Learner.

## <a name="video"></a>Vídeo

[![Visualização de Tutorial entidades previamente treinadas](https://aka.ms/cl_Tutorial_v3_PreTrainedEntities_Preview)](https://aka.ms/cl_Tutorial_v3_PreTrainedEntities)

## <a name="requirements"></a>Requisitos
Este tutorial exige que Bot de tutorial esteja em execução

    npm run tutorial-general

## <a name="details"></a>Detalhes

Entidades previamente treinadas reconhecem tipos comuns de entidades, como números, datas, valores monetários e outros.  Elas funcionam "out-of-the-box," não exigem qualquer treinamento e seu comportamento não pode ser alterado ao contrário de entidades personalizadas.  Por padrão, entidades previamente treinadas têm vários valores, acumulando todas as instâncias da entidade identificadas.

## <a name="steps"></a>Etapas

Inicie na home page na interface de usuário da Web.

### <a name="create-the-model"></a>Criar o modelo

1. Selecione **novo modelo**.
2. Insira **PretrainedEntities** para **nome**.
3. Selecione **Criar**.

### <a name="entity-creation"></a>Criação da entidade

1. Selecione **entidades** no painel esquerdo, em seguida, **nova entidade**.
2. Selecione **Trained/datetimeV2** para **tipo de entidade**.
3. Verifique **múltiplos** para habilitar a entidade de acumular um ou mais valores. Observe que entidades Pre-Trained não pode ser negável.
4. Selecione **Criar**.

![](../media/T08_entity_create.png)

1. Selecione **ações** no painel esquerdo, em seguida, **nova ação**.
2. Insira **a data é $builtin-datetimev2** para **de resposta do Bot...** .
3. Selecione **Criar**.

![](../media/T08_action_create_1.png)

### <a name="create-the-second-action"></a>Criar a segunda ação

1. Selecione **ações** no painel esquerdo, em seguida, **nova ação**.
2. Insira **qual é a data?** para **de resposta do Bot...** . Entidades previamente treinadas não podem ser **necessárias entidades** conforme eles são reconhecidos por padrão para todas as declarações.
3. Insira **datetimev2 builtin** para **desqualificação entidades**.
4. Selecione **Criar**.

![](../media/T08_action_create_2.png)

### <a name="train-the-model"></a>Treinar o modelo

1. Selecione **Train Dialogs** no painel esquerdo, em seguida, **caixa de diálogo do novo treinamento**.
2. Insira **hello** para a declaração do usuário no painel à esquerda de bate-papo.
3. Selecione **pontuar ações**.
4. Selecione **qual é a data?** na lista de ações
5. Insira **hoje mesmo** para a declaração do usuário no painel à esquerda de bate-papo.
    - O **hoje mesmo** expressão é automaticamente reconhecida por modelos previamente treinados no LUIS.
    - Passar o mouse sobre os valores de entidades previamente treinadas mostra dados adicionais fornecidos pelo LUIS.

![](../media/T08_training.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Resolvedores de entidade](./09-entity-resolvers.md)
