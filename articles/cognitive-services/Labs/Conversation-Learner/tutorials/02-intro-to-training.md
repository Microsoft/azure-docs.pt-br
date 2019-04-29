---
title: Introdução ao treinamento do modelo de Conversation Learner - Serviços Cognitivos da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Aprenda como treinar um modelo, com a ramificação e a edição do treinamento anterior através do Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 1e6dea521207ec00efe948a879c705bd28a75d57
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60707624"
---
# <a name="introduction-to-training"></a>Introdução ao treinamento

Este tutorial mostra as noções básicas de treinamento de um modelo, a ramificação de um novo treinamento com base em um treinamento anterior e a edição de um bot de resposta para alterá-lo.

## <a name="video"></a>Vídeo

[![Introdução à visualização do tutorial de treinamento](https://aka.ms/cl_Tutorial_v3_IntroTraining_Preview)](https://aka.ms/cl_Tutorial_v3_IntroTraining)

## <a name="requirements"></a>Requisitos
Este tutorial exige que o bot de tutorial geral esteja em execução

    npm run tutorial-general

## <a name="details"></a>Detalhes

- Ações: Um bot de resposta para a entrada do usuário.
- Treinar: A maneira como ensinamos um bot a responder à entrada do usuário.
- Branch: A modificação de uma entrada do usuário dentro de uma caixa de diálogo de treinamento salvo com a finalidade de criar uma nova caixa de diálogo de treinamento começa da mesma maneira que o original, mas leva a conversa em outra direção.

## <a name="steps"></a>Etapas

### <a name="create-a-new-model"></a>Criar um novo modelo

1. Na interface do usuário da Web, clique em Novo Modelo
2. Para o “Nome”, digite “Bot inspirar”. Em seguida, clique em Criar.

### <a name="create-an-action"></a>Criar uma ação

1. No painel esquerdo, clique em "Ações" e, em seguida, no botão "Nova ação".
2. No campo "Bot de resposta", digite "Oi!" Você quer se inspirar hoje?".
    - Deixe todos os outros campos e caixas de seleção em sua configuração padrão.
3. Clique em Criar.

### <a name="first-training-and-creating-another-action-while-training"></a>Primeiro treinamento e criação de outra ação durante o treinamento

1. No painel esquerdo, clique em "Caixas de diálogo de treinamento" e, em seguida, no botão "Nova caixa de diálogo de treinamento".
2. No painel de chat, onde está escrito "Digite sua mensagem...", digite "oi". 
    - Isso simula o lado do usuário da conversa.
3. Clique em “Pontuar ações”.
4. Selecione a resposta: "Oi! Você quer se inspirar hoje?".
5. Como o usuário, responda "sim".
6. Clique em “Pontuar ações”.
7. Clique no botão “+ação”. 
    - Você será levado à caixa de diálogo familiar "Criar uma ação".
8. Digite no bot de resposta "Você é incrível!"
9. Clique em Criar.
10. Observe que o bot responde imediatamente.
11. Clique no botão “Salvar”.

### <a name="branch-a-second-training-off-of-the-first-training"></a>Ramificar um segundo treinamento do primeiro treinamento.
1. Clique na linha de grade que resume o treinamento primeiro. 
    - Isso permite a exibição e a edição do treinamento existente.
2. Clique na resposta do usuário "sim". 
    - Isso irá expor os controles de edição.
3. Clique no ícone do branch. 
    - Isso abrirá um prompt para uma entrada do usuário diferente para uma nova conversa.
4. Tipo em "não", aperte enter ou clique no botão "Criar". 
    - Neste ponto, você terá uma nova instância de uma caixa de diálogo de treinamento, a original permanece inalterada.
5. Clique em “Pontuar ações”.
6. Clique na resposta incorreta do bot que acabou de aparecer.
7. Clique no botão “+ ação”. 
    - para que criar uma nova ação para responder com o bot.
8. Digite no bot de resposta "Sem problemas! Tenha um ótimo dia!"
9. Clique em Criar. 
10. Observe que o bot responde imediatamente.
11. Clique no botão “Salvar”.

### <a name="test-the-trainings"></a>Teste dos treinamentos
1. No painel esquerdo, clique em “Registrar diálogos”, depois em “Novo Registro de Diálogo”.
2. Digite a mensagem: "Olá". 
3. Observe que o bot responde automaticamente da maneira que foi treinado.
4. Digite a resposta do usuário: “sim”.
5. Observe a resposta de Bot, ela mostra que o primeiro treinamento está funcionando.
6. Clique no botão de "Tempo limite da sessão". Isso informa ao Conversation Learner para começar novamente, ignorando a conversa que apenas aconteceu.
7. Digite a mensagem: "Olá". 
8. Observe que o bot responde automaticamente da maneira que foi treinado.
9. Digite a resposta do usuário: "não".
10. Observe o bot de resposta, ele mostra que o segundo treinamento está funcionando.
11. Clique no botão "Testes concluídos".

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Ações de espera e não espera](./03-wait-vs-nonwait-actions.md)
