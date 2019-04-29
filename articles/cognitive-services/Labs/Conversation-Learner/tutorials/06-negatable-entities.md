---
title: Como usar Entidades Negáveis com um Modelo do Conversation Learner - Serviços Cognitivos da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba como usar Entidades Negáveis com um Modelo do Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 78dc759632c4fc3116a59ea1e5bc0b93200bca45
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60707951"
---
# <a name="how-to-use-negatable-entities-with-a-conversation-learner-model"></a>Como usar Entidades Negáveis com um Modelo do Conversation Learner

Este tutorial demonstra a propriedade "Negável" das Entidades.

## <a name="video"></a>Vídeo

[![Versão prévia de tutorial de Entidades Negáveis](https://aka.ms/cl_Tutorial_v3_NegatableEntities_Preview)](https://aka.ms/cl_Tutorial_v3_NegatableEntities)

## <a name="requirements"></a>Requisitos
Este tutorial exige que Bot de tutorial esteja em execução

    npm run tutorial-general

## <a name="details"></a>Detalhes
A propriedade "Negatable" de uma entidade permite rotular os dois normal (positivo) e números negativos são instâncias da entidade, ensinar com base em modelos positivos e negativos e limpar o valor de uma entidade existente. Entidades com o conjunto de propriedades "Negáveis" são chamadas de Entidades Negáveis no Conversation Leaner.

## <a name="steps"></a>Etapas

Inicie na home page na interface de usuário da Web.

### <a name="create-the-model"></a>Criar o modelo

1. Selecione **novo modelo**.
2. Insira **NegatableEntity** para **nome**.
3. Selecione **Criar**.

### <a name="entity-creation"></a>Criação da entidade

1. Selecione **entidades** no painel esquerdo, em seguida, **nova entidade**.
2. Selecione **personalizado treinado** para **tipo de entidade**.
3. Insira **nome** para **nome da entidade**.
4. Verifique **Negatable** permitir que os usuários fornecer um valor de entidade ou dizer algo *não* valor de uma entidade, assim, excluindo o valor de entidade correspondente.
5. Selecione **Criar**.

![](../media/T06_entity_create.png)

### <a name="create-the-first-action"></a>Criar a primeira ação

1. Selecione **ações** no painel esquerdo, em seguida, **nova ação**.
2. Insira **não sei seu nome.** para **de resposta do Bot...** .
3. Insira **nome** para **desqualificação qualifica**.
4. Selecione **Criar**.

![](../media/T06_action_create_1.png)

### <a name="create-the-second-action"></a>Criar a segunda ação

1. Selecione **ações** no painel esquerdo, em seguida, **nova ação**.
2. Insira **sei seu nome. Ele é $name.** para **de resposta do Bot...** .
3. Selecione **Criar**.

> [!NOTE]
> O **nome** entidade foi adicionada automaticamente como um **necessárias entidades** por referência na declaração de resposta.

Agora você tem duas ações.

![](../media/T06_action_create_2.png)

### <a name="train-the-model"></a>Treinar o modelo

1. Selecione **Train Dialogs** no painel esquerdo, em seguida, **caixa de diálogo do novo treinamento**.
2. Insira **hello** para a declaração do usuário no painel à esquerda de bate-papo.
3. Selecione **pontuar ações**.
4. Selecione **não sei seu nome.** na lista de ações. O percentual é 100%, como a ação só é válida com base em restrições.
5. Insira **meu nome é o Frank** para a declaração do usuário no painel à esquerda de bate-papo.
6. Realçar **Frank** , em seguida, selecione **+ nome**. Entidades negável tem duas instâncias: (+) e adiciona ou substitui o valor; (-) menos remove o valor.
7. Selecione **pontuar ações**. O **nome** entidade agora está definida como **Frank** na memória do modelo, portanto, o **sei seu nome. Ele é $name** ação está disponível.
8. Selecione **sei seu nome. Ele é $name.** na lista de ações.
9. Insira **meu nome não é Frank.** para a declaração do usuário no painel à esquerda de bate-papo.
10. Realçar **Frank** , em seguida, selecione **-nome** para limpar o valor da **nome** entidade.
11. Selecione **pontuar ações**.
12. Selecione **não sei seu nome.** na lista de ações.
13. Insira **Susan, meu nome é.** para a expressão de terceiro do usuário no painel à esquerda de bate-papo.
14. Realçar **Susan** , em seguida, **+ nome** 

![](../media/T06_training.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Entidades com vários valores](./07-multi-value-entities.md)
