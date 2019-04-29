---
title: Como usar as ações de espera e não espera com um modelo de Aprendiz de Conversa - Serviços Cognitivos da Microsoft| Microsoft Docs
titleSuffix: Azure
description: Saiba como usar as ações de espera e não espera com o modelo de Aprendiz de Conversa.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 2869e6c59388adf548e5f239d3a831a5a3f060dd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60707607"
---
# <a name="wait-and-non-wait-actions"></a>Ações de espera e não espera

Este tutorial mostra a diferença entre as ações de espera e as ações de não espera no Aprendiz de Conversa.

## <a name="video"></a>Vídeo

[![Versão prévia do tutorial de ação aguardar vs não aguardar](https://aka.ms/cl_Tutorial_v3_WaitnonWait_Preview)](https://aka.ms/cl_Tutorial_v3_WaitnonWait)

## <a name="requirements"></a>Requisitos
Este tutorial exige que o bot de tutorial geral esteja em execução

    npm run tutorial-general

## <a name="details"></a>Detalhes

- Ação Aguardar: Após o sistema executar uma ação "aguardar", ele irá parar a execução de ações e aguardará a entrada do usuário.
- Ação Não Aguardar: Após o sistema executar uma ação "não aguardar", ele escolherá imediatamente outra ação (sem aguardar a entrada do usuário).

## <a name="steps"></a>Etapas

### <a name="create-a-new-model"></a>Criar um novo modelo

1. Na interface do usuário da Web, clique em Novo Modelo
2. No campo "Nome", digite "Esperar Não Aguardar", pressione enter ou clique no botão "Criar".

### <a name="create-the-first-two-wait-actions"></a>Criar as primeiras duas Ações Aguardar

1. No painel esquerdo, clique em "Ações" e, em seguida, no botão "Nova Ação".
2. No campo "Resposta do Bot...", digite "Qual pizza você gostaria?".
    - Essa é uma ação Aguardar, portanto, deixe a caixa "Aguardar a resposta" marcada.
3. Clique no botão "Criar".
4. Repetindo essas etapas, crie outra ação com "Pizza a caminho!" como a resposta do Bot.

### <a name="train-using-those-wait-actions"></a>Treinar usando essas Ações Aguardar

1. No painel esquerdo, clique em "Treinar Diálogos" e, em seguida, no botão "Novo Diálogo de Treinamento".
2. No painel de chat, onde diz "Digite sua mensagem...", digite "Oi". 
    - Isso simula o lado do usuário da conversa.
3. Clique no botão "Pontuar Ações".
4. Selecione a resposta "Qual pizza você gostaria?".
5. Como o usuário, responda com "Marguerita".
6. Clique no botão "Pontuar Ações".
7. Selecione a resposta "Pizza a caminho!".
8. Clique no botão "Salvar".

### <a name="create-a-non-wait-action-while-training"></a>Criar uma Ação Não Aguardar durante o treinamento
Embora você possa criar a Ação Não Aguardar como fez anteriormente, também é possível criá-la a partir de uma sessão de Treinamento.
1. Clique no botão "Novo Diálogo de Treinamento".
2. Como o tipo de usuário, digite "Olá".
3. Clique no botão "Pontuar Ações".
4. Clique no botão "+Ação". 
    - Com esse treinamento, você irá se familiarizar com a caixa de diálogo "Criar uma Ação".
5. Digite a resposta do Bot como "Bem-vindo à Pizza Bot!"
6. Desmarque a caixa de seleção "Aguardar a resposta".
7. Clique no botão "Criar".
    - Observe que o Bot responde imediatamente com "Bem-vindo à Pizza Bot!" e que novamente será solicitado fornecer uma outra resposta do Bot. Isto acontece porque a resposta do Bot foi uma Ação Não Aguardar que acabamos de criar.
9. Selecione a resposta "Qual pizza você gostaria?".
10. Como o usuário, responda com "Marguerita".
11. Clique no botão "Pontuar Ações".
12. Selecione a resposta "Pizza a caminho!".
13. Clique no botão "Salvar".

> [!NOTE]
> A sequência das respostas do bot com relação às ações de espera e de não espera.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Introdução a entidades](./04-introduction-to-entities.md)
