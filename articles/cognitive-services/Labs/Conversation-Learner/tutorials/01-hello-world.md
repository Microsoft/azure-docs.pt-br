---
title: Como criar um modelo de Aprendiz de Conversa "Olá, Mundo" - Serviços Cognitivos da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba como criar um modelo de Aprendiz de Conversa "Olá, Mundo".
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: b2c43ad2475ab75d251e57fca6009eb1fa2e1f00
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60707590"
---
# <a name="how-to-create-a-hello-world-model-with-conversation-learner"></a>Como criar um modelo "Olá, Mundo" com o Aprendiz de Conversa

Este tutorial apresenta a introdução ao Conversation Learner, incluindo criação de ações, aprendizagem interativa do Bot e correções de diálogos registrados provenientes de usuários finais.

## <a name="video"></a>Vídeo

[![Versão prévia do tutorial Olá, Mundo](https://aka.ms/cl_Tutorial_v3_HelloWorld_Preview)](https://aka.ms/cl_tutorial_v3_helloworld)


## <a name="requirements"></a>Requisitos
Se você ainda não fez isso, conclua todas as etapas de configuração, incluindo a criação de um arquivo `.env` com sua chave de criação do LUIS.  Consulte [Início Rápido](../quickstart.md) para obter detalhes.

Este tutorial exige que Bot de tutorial esteja em execução

    npm run tutorial-general

## <a name="steps"></a>Etapas

Inicie na home page na interface de usuário da Web.

### <a name="create-the-model"></a>Criar o modelo
1. Clique no botão "Novo Modelo".
2. No campo "Nome", insira "Olá, Mundo".
3. Clique no botão "Criar".

Agora, você deve ver a exibição do modelo criado.

### <a name="create-an-action"></a>Criar uma ação
1. No painel esquerdo, clique em "Ações" e, em seguida, no botão "Nova ação".
    - Uma Ação pode ser uma mensagem de texto retornada pelo Aprendiz de Conversa para o usuário, uma chamada de API ou um cartão.
2. No campo "Resposta do Bot...", digite "Olá".
    - Essa é a resposta que o Bot retornará.
3. Clique no botão "Criar".

Você criou a primeira ação que o Bot pode executar, ou seja, retornar uma resposta de texto.

### <a name="train-dialogs"></a>Caixas de Diálogo de Treinamento
É o treinamento do modelo sobre como responder aos enunciados do usuário.

#### <a name="first-training-dialog"></a>Primeiro Diálogo de Treinamento

1. No painel esquerdo, clique em "Diálogos de Treinamento" e, em seguida, no botão "Novo Diálogo de Treinamento".
2. Digite "Olá", pressione enter.
    - Como um exemplo do que o usuário pode dizer no início de uma conversa.
3. Clique no botão "Ações de Pontuação".
4. Selecione "Olá".
    - Você acabou de concluir um turno completo nesse diálogo de exemplo. 
5. Digite a resposta do usuário, "Até logo".
6. Clique no botão "Ações de Pontuação".
7. Clique no botão "+ Ação".
8. Digite "Até logo!" no campo "Resposta do Bot...", clique no botão "Criar".
    - Observe que o Bot respondeu com a ação que você acabou de criar.
9. Clique no botão "Salvar". 
    - Isso encerrará e salvará esse de Diálogo de Treinamento.

Agora, você tem um Diálogo de Treinamento no Modelo junto com uma Entidade única e duas Ações.

#### <a name="second-training-dialog"></a>Segundo Diálogo de Treinamento
Vamos fazer mais um treinamento e observar como o Bot responde.

1. Clique no botão "Novo Diálogo de Treinamento".
2. Digite, "Oi"
    - Isso é semelhante ao primeiro diálogo e esperamos obter uma boa pontuação do Bot.
3. Clique no botão "Ações de Pontuação".
    - A posição e a pontuação talvez ainda não sejam suficientemente precisas e poderão exigir treinamento adicional.
4. Selecione "Olá".
5. Digite a resposta do usuário, "tchau".
6. Clique no botão "Ações de Pontuação".
7. Selecione "Até logo!"
8. Clique no botão "Salvar".

### <a name="log-dialogs"></a>Registrar Diálogos
Nesse local você Testa, Exibe e Corrige as Conversas que você ou os usuários reais tiveram com o Bot.

#### <a name="test-the-model-as-an-end-user"></a>Testar o Modelo como um Usuário Final
1. No painel esquerdo, clique em "Registrar Diálogos" e, em seguida, no botão "Novo Registro de Diálogo".
2. Digite "Olá".
3. Aguarde alguns segundos, o Bot deverá responder automaticamente com "Olá"
4. Insira 'tchau'
5. Aguarde alguns segundos, novamente o Bot deverá responder automaticamente com "Olá".
6. Clique no botão "Teste Concluído".

#### <a name="view-and-correct-a-user-conversation"></a>Exibir e corrigir uma Conversa do Usuário
Usando Registrar Diálogos você poderá exibir a lista das conversas que os usuários realizaram com o Bot. Além disso, será possível editá-las para corrigir as respostas do Bot e salvar as interações como Diálogos de Treinamento. Para fazer isso:
1. Na grade, clique no registro da conversa.
2. Clique na última ação do Bot, por exemplo, "Olá".
3. Selecione "Até logo!" para corrigir o Bot.
4. Clique no botão "Salvar como Diálogo de Treinamento".

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Introdução ao treinamento](./02-intro-to-training.md)
