---
title: Como usar o retorno de chamada de detecção de entidade com um modelo de Aprendiz de Conversa - Serviços Cognitivos da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba como usar o retorno de chamada de detecção de entidade com um modelo de Aprendiz de Conversa.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 576dc6bd44360f73c4133907233e59e5f51837b1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60707541"
---
# <a name="how-to-use-entity-detection-callback"></a>Como usar o retorno de chamada de detecção de entidade

Este tutorial mostra o retorno de chamada de detecção de entidade e ilustra um padrão comum para determinar as entidades.

## <a name="video"></a>Vídeo

[![Versão prévia do tutorial de retorno de chamada de detecção de entidade](https://aka.ms/cl_Tutorial_v3_EntityDetection_Preview)](https://aka.ms/cl_Tutorial_v3_EntityDetection)

## <a name="requirements"></a>Requisitos
Este tutorial requer que o bot `tutorialEntityDetectionCallback` esteja em execução.

    npm run tutorial-entity-detection

## <a name="details"></a>Detalhes
Os retornos de chamada de detecção de entidade ativam a modificação do comportamento de reconhecimento de entidade e manipulação de entidade por meio de código. Vamos demonstrar essa funcionalidade examinando um padrão de design de retorno de chamada de detecção de entidade típico. Por exemplo, resolver "big apple" para "Nova Iorque".

## <a name="steps"></a>Etapas

### <a name="create-the-model"></a>Criar o modelo

1. Na interface do usuário da Web, clique em “Novo Modelo”.
2. No campo "Nome", digite "EntityDetectionCallback" e pressione enter.
3. Clique no botão “Criar”.

São necessárias três entidades neste exemplo, então, vamos criar as três.

### <a name="create-the-custom-trained-entity"></a>Criar a entidade treinada personalizada

1. No painel esquerdo, clique em "Entidades" e, em seguida, no botão "Nova Entidade".
2. Selecione "Treinada Personalizada" para o "Tipo de Entidade".
3. Digite "City" para o "Nome da Entidade".
4. Clique no botão “Criar”.

### <a name="create-the-first-programmatic-entity"></a>Criar a primeira entidade programática

1. Clique no botão "Nova entidade".
2. Selecione “Programática” para o “Tipo de Entidade”.
3. Digite "CityUnknown" para o "Nome da Entidade".
4. Clique no botão “Criar”.

### <a name="create-the-first-programmatic-entity"></a>Criar a primeira entidade programática

1. Clique no botão "Nova entidade".
2. Selecione “Programática” para o “Tipo de Entidade”.
3. Digite "CityResolved" para o "Nome da Entidade".
4. Clique no botão “Criar”.

Agora, crie três ações.

### <a name="action-creation"></a>Criação de ação

1. No painel esquerdo, clique em "Ações" e, em seguida, no botão "Nova ação".
2. No campo "Resposta do bot...", digite "Que cidade você deseja?"
3. No campo "Entidade esperada na resposta do usuário...", digite "City".
4. No campo "Entidades desqualificadoras", digite "City".
5. Clique no botão “Criar”.
6. Clique no botão “Nova ação”.
7. No campo "Resposta do bot...", digite "Qual cidade você deseja?"
8. No campo "Entidade esperada na resposta do usuário...", digite "Eu não conheço esta cidade".
9. Clique no botão “Criar”.
10. Clique no botão “Nova ação”.
11. No campo "Resposta de bot...", digite "$CityResolved é muito legal."
12. Clique no botão “Criar”.

Aqui está o código de retorno de chamada:

![](../media/tutorial10_callbackcode.PNG)

### <a name="train-the-model"></a>Treinar o modelo

1. No painel esquerdo, clique em "Treinar Diálogos" e, em seguida, no botão "Novo Diálogo de Treinamento".
2. No painel de chat, onde está escrito "Digite sua mensagem...", digite "oi"
3. Clique no botão "Ações de Pontuação".
4. Selecione a resposta, "Qual cidade você deseja?"
5. No painel de chat, onde está escrito "Digite sua mensagem...", digite "big apple".
6. Clique no botão "Ações de Pontuação".
    - Clicar no botão aciona o retorno de chamada de detecção de entidade
    - O código de retorno de chamada define o valor de entidade CityResolved corretamente para "Nova Iorque"
7. Selecione a resposta "Nova Iorque é muito legal".

Esse padrão é típico de muitos cenários de bot. As expressões de usuário e as entidades extraídas são fornecidas para sua lógica de negócios e essa lógica transforma a expressão em formato canônico, que depois é salvo em entidades programáticas, para turnos de diálogos subsequentes.

![](../media/tutorial10_bigapple.PNG)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Retornos de chamada de sessão](./13-session-callbacks.md)
