---
title: Ciclo de vida da base de dados de conhecimento – QnA Maker
titleSuffix: Azure Cognitive Services
description: O QnA Maker aprende melhor em um ciclo iterativo de alterações de modelo, exemplos de expressão, publicação e coleta de dados de consultas de ponto de extremidade.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 04/16/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 3f78b8a2566137d596f4ab3f083e1d14289365c3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61379208"
---
# <a name="knowledge-base-lifecycle-in-qna-maker"></a>Ciclo de vida da base de dados de conhecimento no QnA Maker
O QnA Maker aprende melhor em um ciclo iterativo de alterações de modelo, exemplos de expressão, publicação e coleta de dados de consultas de ponto de extremidade. 

![Ciclo de criação](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>Criar uma base de dados de conhecimento do QnA Maker
O ponto de extremidade de KB (base de dados de conhecimento) do QnA Maker fornece uma resposta de melhor correspondência a uma consulta do usuário com base no conteúdo da base de dados de conhecimento. Criar uma base de dados de conhecimento é uma ação única para configurar um repositório de conteúdo de perguntas, respostas e metadados associados. Uma base de dados de conhecimento pode ser criada, rastreando conteúdo pré-existente como páginas de perguntas frequentes, manuais de produtos ou pares P-R estruturados. Saiba como [criar uma base de dados de conhecimento](../How-To/create-knowledge-base.md).

## <a name="testing-and-updating-the-knowledge-base"></a>Testar e atualizar a base de dados de conhecimento

A base de dados de conhecimento está pronta para testes, uma vez que é preenchida com conteúdo, seja editorialmente ou através de extração automática. Teste interativo pode ser feito no portal do QnA Maker por meio de **teste** painel inserindo consultas comuns de usuário e verificar que as respostas retornem com a resposta correta e a pontuação de confiança suficiente. 

* **Para corrigir as pontuações de confiança baixa**: Adicionar perguntas alternativas. 
* **Quando uma consulta retorna incorretamente a [resposta padrão](confidence-score.md#change-default-answer)**: adicionar novas respostas para a pergunta correta. 

Este loop estreito de atualização de teste continuará até que você esteja satisfeito com os resultados. Saiba como [testar a base de dados de conhecimento](../How-To/test-knowledge-base.md).

Para grandes KBs, use testes automatizados com o [generateAnswer API](../how-to/metadata-generateanswer-usage.md#get-answer-predictions-with-the-generateanswer-api) e o `isTest` quais consultas de propriedade body o `test` base de dados de Conhecimento, em vez de base de Conhecimento publicada. 

```json
{
  "question": "example question",
  "top": 3,
  "userId": "Default",
  "isTest": true
}
```

## <a name="publish-the-knowledge-base"></a>Publicar a base de dados de conhecimento
Quando terminar de testar a base de dados de conhecimento, você poderá publicá-la. Publicar efetua push da última versão da base de dados de conhecimento testada para um índice do Azure Search dedicado que representa a base de dados de conhecimento **publicada**. Isso também cria um ponto de extremidade que pode ser chamado no aplicativo ou chat bot.

Dessa forma, quaisquer alterações feitas na versão de teste da base de dados de conhecimento não afetam a versão publicada que pode estar ativa em um aplicativo de produção.

Cada uma dessas bases de dados de conhecimento pode ser direcionada para testes separadamente. Usando as APIs, você pode direcionar a versão de teste da base de dados de conhecimento com `isTest` propriedade na chamada generateAnswer body.

Saiba como [publicar a base de dados de conhecimento](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).

## <a name="monitor-usage"></a>Monitorar o uso
Para registrar os logs de chat do seu serviço, é necessário habilitar o Application Insights ao [criar o serviço do QnA Maker](../How-To/set-up-qnamaker-service-azure.md).

É possível obter várias análises do uso do serviço. Saiba mais sobre como usar o Application Insights para obter [análises do serviço do QnA Maker](../How-To/get-analytics-knowledge-base.md).

De acordo com o que aprende-se com as análises, faça as [atualizações da base de dados de conhecimento](../How-To/edit-knowledge-base.md) apropriadas.

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Pontuação de confiança](./confidence-score.md)

## <a name="see-also"></a>Consulte também 

[Base de Conhecimento](./knowledge-base.md)
[Visão geral do QnA Maker](../Overview/overview.md)
