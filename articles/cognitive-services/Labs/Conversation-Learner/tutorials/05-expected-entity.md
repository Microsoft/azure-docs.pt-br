---
title: Como usar a propriedade "Entidade Esperada" das ações do Conversation Learner - Serviços Cognitivos da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba como usar a propriedade "Entidade Esperada" de um modelo do Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 01d991cff9b7f7a66740f86e537833ffe4e862c7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60707882"
---
# <a name="how-to-use-the-expected-entity-property-of-actions"></a>Como usar a propriedade "Entidade Esperada" das ações

Este tutorial demonstra a propriedade "Entidade Esperada" das ações.

## <a name="video"></a>Vídeo

[![Versão prévia do Tutorial de Entidade Esperada](https://aka.ms/cl_Tutorial_v3_ExpectedEntity_Preview)](https://aka.ms/cl_Tutorial_v3_ExpectedEntity)

## <a name="requirements"></a>Requisitos
Este tutorial exige que o bot de tutorial geral esteja em execução

    npm run tutorial-general

## <a name="details"></a>Detalhes
Use a propriedade "Entidade Esperada" de uma ação para salvar a resposta do usuário para essa ação em uma entidade.

Ao adicionar entidades à propriedade "Entidade Esperada" de uma ação, o sistema:

1. Inicia tentando corresponder entidades usando o modelo de extração de entidade baseado em aprendizado de máquina
2. Atribui o enunciado inteiro do usuário para $entidade com base em heurística, se nenhuma entidade for encontrada
3. Chama `EntityDetectionCallback` e prossegue para a seleção da ação.

## <a name="steps"></a>Etapas

### <a name="create-the-model"></a>Criar o modelo

1. Na interface do usuário da Web, clique em "Novo Modelo".
2. No campo "Nome", digite "ExpectedEntities" e pressione enter.
3. Clique no botão "Criar".

### <a name="entity-creation"></a>Criação da entidade

1. No painel esquerdo, clique em "Entidades" e, em seguida, no botão "Nova Entidade".
2. Selecione "Treinada Personalizada" para o "Tipo de Entidade".
3. Digite o "nome" para o "Nome da Entidade".
4. Clique no botão "Criar".

> [!NOTE]
> O tipo de entidade 'Personalizada Treinada' significa que essa entidade pode ser treinada, ao contrário de outros tipos de entidades.

![](../media/tutorial4_entities.PNG)

### <a name="create-the-first-action"></a>Criar a primeira ação

1. No painel esquerdo, clique em "Ações" e, em seguida, no botão "Nova Ação".
2. No campo "Resposta do bot...", digite "Qual é o seu nome?"
3. No campo "Entidades Esperadas", digite "nome".
4. Clique no botão "Criar".

> [!NOTE]
> As entidades detectadas e extraídas da resposta do usuário serão salvas para a entidade "nome" se essa ação for escolhida. Se nenhuma entidade for detectada, a resposta inteira será salva para essa entidade.

### <a name="create-the-second-action"></a>Criar a segunda ação

1. No painel esquerdo, clique em "Ações" e, em seguida, no botão "Nova Ação".
2. No campo "Resposta do bot...", digite "Oi $nome!"
3. Clique no botão "Criar".

> [!NOTE]
> Entidade "nome" foi automaticamente adicionada como "Entidades Necessárias" por referência na resposta.

Agora você tem duas ações.

![](../media/tutorial4_actions.PNG)

### <a name="train-the-model"></a>Treinar o Modelo

1. No painel esquerdo, clique em "Caixas de Diálogo de Treinamento" e, em seguida, no botão "Nova Caixa de Diálogo de Treinamento".
2. No painel de chat, onde está escrito "Digite sua mensagem...", digite "oi".
    - Isso simula o lado do usuário da conversa.
3. Clique no botão "Ações de Pontuação".
4. Selecione a resposta, "Qual é o seu nome?"
    - A resposta "Oi $nome!" não pode ser selecionada, porque agora essa resposta requer que a entidade "nome" seja definida na memória do modelo.
5. No painel de chat, onde está escrito "Digite sua mensagem...", digite "Pedro".
    - "Pedro" é realçado como uma entidade com base na heurística que configuramos anteriormente para salvar a resposta como a entidade.
6. Clique no botão "Ações de Pontuação".
    - A entidade "nome" agora está definida como "Pedro" na memória do modelo, portanto, a ação "Oi $nome" é selecionável como uma ação.
7. Selecione a resposta "Oi $nome!"
8. Clique no botão "Salvar".

Adicionar entradas alternativas treina mais o modelo.

1. No campo "Adicionar entrada alternativa...", digite "Eu sou Jose".
    - O modelo não reconhece o nome como uma entidade, então ele seleciona o bloco de texto inteiro como o valor da entidade
2. Clique na frase "Eu sou Jose" e, em seguida, clique no ícone de lixeira.
3. Clique em "Jose" e, em seguida, clique em "nome" na lista de entidades.
4. Clique em “Score Actions” (Pontuar ações).
5. Selecione a resposta "Oi Pedro!"
6. Clique no botão "Salvar".

![](../media/tutorial4_dialogs.PNG)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Entidades negáveis](./06-negatable-entities.md)
