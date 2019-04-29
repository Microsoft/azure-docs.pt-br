---
title: Resolvedores de entidade em um modelo de aprendiz de conversa - serviços Cognitivos da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba como usar os Solucionadores de Entidade no Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: cca78e2536a922165f40bbcbabcae005021aa70b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60707814"
---
# <a name="entity-resolvers"></a>Solucionadores de entidade

Este tutorial mostra como usar os Solucionadores de Entidade no Conversation Learner

## <a name="video"></a>Vídeo

[![Visualização de Tutorial de Solucionadores de Entidade](https://aka.ms/cl_Tutorial_v3_EntityResolvers_Preview)](https://aka.ms/cl_Tutorial_v3_EntityResolvers)

## <a name="requirements"></a>Requisitos
Este tutorial exige que Bot de tutorial esteja em execução

    npm run tutorial-general

## <a name="details"></a>Detalhes

- O Tipo de Solucionador é uma propriedade opcional de Entidades Personalizadas.
- Solucionadores de Entidade utilizam o poder dos reconhecedores de entidade pré-treinado no LUIS para fornecer detalhes adicionais e maior clareza para sua Entidade Personalizada.

## <a name="steps"></a>Etapas

Inicie na home page na interface de usuário da Web.

### <a name="create-the-model"></a>Criar o modelo

1. Selecione **novo modelo**.
2. Insira **resolvedores de entidade** para **nome**.
3. Selecione **Criar**.

### <a name="create-a-pair-of-entities"></a>Criar um par de Entidades

1. Selecione **entidades** no painel esquerdo, em seguida, **nova entidade**.
2. Insira **partidas** para **nome da entidade**.
3. Selecione **datetimeV2** para **tipo de resolvedor**.
4. Selecione **Criar**. Descartar o pop-up informativo, selecionando **Okey**.
5. Repita as etapas 1 a 4 para criar uma segunda entidade nomeada **retornar** com **datetimeV2** tipo de resolvedor.

![](../media/T09_entities.png)

### <a name="create-a-pair-of-actions"></a>Criar um par de Ações

1. Selecione **ações** no painel esquerdo, em seguida, **nova ação**.
2. Insira **você está saindo na $departure e retornando em $return** para **de resposta do Bot...** .
    - IMPORTANTE - ao digitar $[entityName] necessário pressionar Insira ou clique na entidade na lista suspensa caso contrário Aprendiz de conversa considerará esse seja o texto em vez de uma entidade.
    - Observe que o **necessárias entidades** campo também receberão essas entidades e elas não podem ser removidas. Isso impede que essa ação se torne disponível até que ambas Entidades necessárias estejam presentes.
3. Selecione **Criar**.
4. Selecione **nova ação** para criar uma segunda ação.
5. Insira **quando você estiver planejando viajar?** para **de resposta do Bot...** .
6. Insira **partidas** e **retornar** para **desqualificação entidades**. Estes informam nosso Bot a NÃO executar esta ação se qualquer uma dessas entidades contiver um valor.
7. Selecione **Criar**.

![](../media/T09_actions.png)

### <a name="training"></a>Treinamento

1. Assista a **treinamento: [Status]** no canto superior esquerdo para **concluído**.
    - Se isso levar muito tempo, você pode clicar no link de "Atualização".
    - O treinamento de status "Concluído" é necessário para que funcionem os Solucionadores da Entidade ao treinar o Modelo.

2. No painel esquerdo, clique em "Treinar Diálogos" e, em seguida, clique no botão "Novo Diálogo de Treinamento".
3. Digite na primeira declaração de usuário, "reserve-me um voo" de tipo. 
4. Clique no botão "Ações de Pontuação".
5. Selecione a resposta "Quando você está planejando viajar?".
6. Como usuário, responda, "saindo amanhã e retornando domingo da próxima semana".
    - Observe como o Conversation Learner detectou duas entidades de “Data pré-treinada” nesse turno de usuário.
7. No painel "Detecção de entidade", selecione o texto "amanhã" e rotule-o como "saída"
8. Também rotular o texto "Domingo da próxima semana" como "retorno"
9. Clique no botão "Ações de Pontuação".
    - Observe como o painel "Memória" contém as datas de partida e retorno.
    - Passe o mouse sobre cada um deles e observe como as entidades são objetos de data que capturam claramente a data do Calendário real em vez de "Domingo" ou "amanhã".
10. Selecione "Você está saindo em..." Resposta do Bot.
11. Clique no botão "Salvar".

![](../media/T09_training.png)

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Entradas alternativas](./10-alternative-inputs.md)
