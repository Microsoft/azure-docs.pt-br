---
title: Resposta precisa usando detecção de span de resposta-QnA Maker
description: Entenda o recurso de resposta preciso disponível em QnA Maker gerenciado.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/09/2020
ms.openlocfilehash: 9c7b265118e395dde2b4b5f3959ba397d75ddac5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103232181"
---
# <a name="precise-answering"></a>Resposta precisa

O recurso de resposta preciso introduzido no QnA Maker gerenciado (versão prévia) permite que você obtenha a resposta curta exata da melhor passagem de resposta de candidato presente na base de dados de conhecimento para qualquer consulta de usuário. Esse recurso usa um modelo de aprendizado profundo que, em tempo de execução, compreende a intenção da consulta do usuário e detecta a resposta curta precisa da passagem de resposta, se houver uma resposta curta presente como fato na passagem da resposta. 

Esse recurso está ativado por padrão no painel de teste, para que você possa testar a funcionalidade específica para seu cenário. Esse recurso é extremamente benéfico para os desenvolvedores de conteúdo, bem como para os usuários finais. Agora, os desenvolvedores de conteúdo não precisam organizar manualmente os pares QnA específicos para cada fato presente na base de dados de conhecimento, e o usuário final não precisa examinar toda a passagem de resposta retornada do serviço para encontrar o fato real que responde à consulta do usuário. Você pode buscar [respostas precisas por meio da API de geração de resposta](How-To/metadata-generateanswer-usage.md#get-precise-answers-with-generateanswer-api).

## <a name="precise-answering-on-qna-maker-portal"></a>Resposta precisa no portal de QnA Maker

No portal de QnA Maker, ao abrir o painel de teste, você verá uma opção para exibir a **resposta curta** na parte superior. Essa opção será selecionada por padrão. Ao inserir uma consulta no painel de teste, você verá uma resposta curta junto com a passagem de resposta, se houver uma resposta curta presente na passagem de resposta.
 
![Painel de teste gerenciado habilitado](../QnAMaker/media/conversational-context/test-pane-with-managed.png)

Você pode desmarcar a opção **Exibir resposta curta** , se desejar ver apenas a passagem de resposta no painel de teste. 

O serviço também retorna a pontuação de confiança da resposta exata como uma **Pontuação de span de resposta** que você pode verificar selecionando a opção de **inspeção** presente logo abaixo da consulta no painel de teste.

![Pontuação de span de resposta gerenciada](../QnAMaker/media/conversational-context/managed-answer-span-score.png)

## <a name="publishing-a-qna-maker-bot"></a>Publicando um bot de QnA Maker

Ao publicar um bot, você obtém a experiência de resposta exata habilitada por padrão em seu aplicativo, em que você verá uma resposta curta junto com a passagem de resposta. Consulte a referência de API para [gerar resposta](/rest/api/cognitiveservices/qnamakerv5.0-preview.1/knowledgebase/generateanswer#answerspan) para ver como usar a resposta exata (chamada AnswerSpan) na resposta. O usuário tem a flexibilidade de escolher outras experiências atualizando o modelo por meio do serviço de aplicativo bot. 

## <a name="language-support"></a>Suporte ao idioma

Atualmente, o recurso de resposta preciso tem suporte apenas para inglês.
