---
title: Como usar entradas alternativas com um Aprendiz de conversa - Serviços Cognitivos da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba como usar entradas alternativas com o Aprendiz de Conversa.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 10335f9c74b9033b303c960a77af136cc80d75bb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60708134"
---
# <a name="how-to-use-alternative-inputs"></a>Como usar entradas alternativas

Este tutorial mostra como usar o campo de Entradas Alternativas para o enunciado do usuário na interface de ensino.

## <a name="video"></a>Vídeo

[![Visualização de Tutorial de Entradas Alternativas](https://aka.ms/cl_Tutorial_v3_AlternativeInputs_Preview)](https://aka.ms/cl_Tutorial_v3_AlternativeInputs)

## <a name="requirements"></a>Requisitos
Este tutorial exige que o bot de tutorial geral esteja em execução

    npm run tutorial-general

## <a name="details"></a>Detalhes
As entradas alternativas são enunciados de usuário alternativos que o usuário pode ter dito em um determinado ponto em um diálogo de treinamento. Essas entradas alternativas permitem que você especifique de forma mais compacta as variações dos enunciados, sem ter que endereçar cada variação em um diálogo de treinamento separado.

## <a name="steps"></a>Etapas

### <a name="create-the-model"></a>Criar o modelo

1. Na interface do usuário da Web, clique em “Novo Modelo”.
2. No campo "Nome", digite "AlternativeInputs" e pressione enter.
3. Clique no botão "Criar".

### <a name="entity-creation"></a>Criação da entidade

1. No painel esquerdo, clique em "Entidades" e, em seguida, no botão "Nova Entidade".
2. Selecione "Treinada Personalizada" para o "Tipo de Entidade".
3. Digite "city" para o "Nome da Entidade".
4. Clique no botão "Criar".

![](../media/T10_actions.png)

Agora, crie três ações.

### <a name="create-the-first-action"></a>Criar a primeira ação

1. No painel esquerdo, clique em "Ações" e, em seguida, no botão "Nova ação".
2. No campo "Resposta do bot...", digite "Qual cidade?"
3. No campo "Entidade esperada na resposta do usuário...", digite "cidade".
4. No campo "Entidades Desqualificadas", digite "cidade".
5. Clique no botão "Criar".

![](../media/T10_action_create_1.png)

### <a name="create-the-second-action"></a>Criar a segunda ação

1. No painel esquerdo, clique em "Ações" e, em seguida, no botão "Nova ação".
2. No "do Bot de resposta...", digite "O clima em $city provavelmente está ensolarado".
3. Clique no botão "Criar".

![](../media/T10_action_create_2.png)

### <a name="create-the-third-action"></a>Crie a terceira ação

1. No painel esquerdo, clique em "Ações" e, em seguida, no botão "Nova ação".
2. Em resposta, digite “Tente perguntar sobre o tempo”.
3. No campo "Entidades Desqualificadas", digite "cidade".
4. Clique no botão "Criar".

![](../media/T10_action_create_3.png)

Agora você tem três ações.

![](../media/T10_actions.png)

### <a name="train-the-model"></a>Treinar o modelo

1. No painel esquerdo, clique em "Treinar Diálogos" e, em seguida, no botão "Novo Diálogo de Treinamento".
2. No painel de chat, onde está escrito "Digite sua mensagem...", digite "Como está o tempo?".
3. Clique no botão "Ações de Pontuação".
4. Selecione a resposta, "Qual cidade?"
5. No painel de chat, onde está escrito "Digite sua mensagem...", digite "Denver"
6. Clique no botão "Ações de Pontuação".
7. Selecione a resposta, "O clima em Denver está provavelmente ensolarado".
8. Clique no botão “Enviar alterações”.

![](../media/T10_training_1.png)

Vamos fazer o treinamento do modelo com a criação de outra caixa de diálogo Treinar.

### <a name="second-model-train-dialog"></a>Segunda caixa de diálogo de treinamento de modelo

1. No painel esquerdo, clique em "Treinar Diálogos" e, em seguida, no botão "Novo Diálogo de Treinamento".
2. No painel de chat, onde está escrito "Digite sua mensagem...", digite "O que você pode fazer?"
3. Clique no botão "Ações de Pontuação".
4. Selecione a resposta “Tente perguntar sobre o tempo".
5. No painel de chat, onde está escrito "Digite sua mensagem...", digite "Como está o tempo em Seattle?"
6. Clique em "Seattle", em seguida, clique em "cidade" na lista de entidades.
7. Clique no botão "Ações de Pontuação".
8. Selecione a resposta, "O clima em Seattle está provavelmente ensolarado".
9. Clique no botão “Enviar alterações”.

![](../media/T10_training_2.png)

### <a name="third-model-train-dialog-using-alternative-input"></a>Terceiro modelo de diálogo de treinamento usando alternativos de entrada

1. No painel esquerdo, clique em "Treinar Diálogos" e, em seguida, no botão "Novo Diálogo de Treinamento".
2. No painel de chat, onde está escrito "Digite sua mensagem...", digite "ajuda".
3. Clique no botão "Ações de Pontuação".
    - O Modelo é indefinido como a melhor opção, portanto, ele escolherá o percentual mais alto por padrão.
4. Clique no botão "Ensinando por abandonar" e depois no botão "Confirmar".

![](../media/T10_training_3.png)

Vamos ajustar melhor o sistema usando entradas alternativas. Você pode adicionar entradas alternativas ao ensinar ou depois.

1. No painel esquerdo, clique em "Diálogos de Treinamento" e, em seguida, selecione “O que eu posso fazer?" na lista de caixas de diálogo de treinamento.
1. Clique no enunciado "O que você pode fazer?" no painel de chat.
1. No campo "Adicionar entrada alternativa...", digite "ajuda" e aperte enter.
1. Clique no botão "Salvar alterações".

![](../media/T10_training_4.png)

Vamos adicionar outra entrada alternativa para lidar com Houston.

1. Clique no enunciado "Como está o clima em Seattle?" no painel de chat.
1. No campo "Adicionar entrada alternativa...", digite "tempo em Houston" e aperte enter.
   - As mensagem de erro que destacam as entradas alternativas devem ser semanticamente equivalentes e conter as mesmas entidades que o enunciado original; não apenas os mesmos valores das entidades. A presença das mesmas entidades é necessária.
1. Clique em "Houston" e selecione "cidade" na Lista de Entidades.
1. No campo "Adicionar entrada alternativa...", digite "tempo em Seattle" e aperte enter.
1. Clique em "Seattle" e selecione "city" na Lista de Entidades.
1. Clique no botão "Salvar alterações".
1. Clique no botão “Salvar Edição”.

![](../media/T10_training_5.png)

### <a name="testing-the-model"></a>Testar o modelo

1. No painel esquerdo, clique em "Registrar Diálogos" e, em seguida, no botão "Novo Registro de Diálogo".
2. No painel de chat, onde está escrito "Digite sua mensagem...", digite "ajude-me"
3. No painel de chat, onde está escrito "Digite sua mensagem...", digite "tempo em Denver".

![](../media/T10_logdialog.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Registras diálogos em log](./11-log-dialogs.md)
