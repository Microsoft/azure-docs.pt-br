---
title: Pontuação de confiança-QnA Maker
titleSuffix: Azure Cognitive Services
description: Quando uma consulta de usuário é comparada com uma base de conhecimento, o QnA Maker retorna respostas relevantes, juntamente com uma pontuação de confiança.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 01/27/2020
ms.custom: seodec18
ms.openlocfilehash: fcfc70f7bfb9e2bb1b1a0edbffdc1282056e5fa7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102214041"
---
# <a name="the-confidence-score-of-an-answer"></a>A pontuação de confiança de uma resposta
Quando uma consulta de usuário é comparada com uma base de conhecimento, o QnA Maker retorna respostas relevantes, juntamente com uma pontuação de confiança. Essa pontuação indica a confiança de que a resposta é a correspondência ideal da consulta do usuário.

A pontuação de confiança é um número entre 0 e 100. Uma pontuação 100 é, provavelmente, uma correspondência exata; no entanto, uma pontuação 0 significa que nenhuma resposta correspondente foi encontrada. Quanto maior a pontuação, maior a confiança na resposta. Pode haver várias respostas retornadas para uma determinada consulta. Nesse caso, as respostas são retornadas em uma ordem de pontuação de confiança decrescente.

No exemplo abaixo, você pode ver uma entidade QnA com duas perguntas.


![Par QnA de exemplo](../media/qnamaker-concepts-confidencescore/ranker-example-qna.png)

No caso do exemplo acima, você pode esperar pontuações como o intervalo de pontuação de exemplo abaixo para diferentes tipos de consultas de usuário:


![Intervalo de pontuação do classificador](../media/qnamaker-concepts-confidencescore/ranker-score-range.png)


A tabela a seguir indica a confiança típica associada a uma determinada pontuação.

|Valor da pontuação|Significado da pontuação|Consulta de exemplo|
|--|--|--|
|90 - 100|Uma correspondência quase exata da consulta de usuário e uma pergunta da KB|"Minhas alterações não são atualizadas na base de conhecimento após a publicação"|
|> 70|Alta confiança – geralmente uma boa resposta que responde por completo à consulta do usuário|"Publiquei minha Base de conhecimento, mas ela não está atualizada"|
|50 - 70|Confiança média – normalmente, uma resposta relativamente boa que deve responder à intenção principal da consulta do usuário|"Devo salvar minhas atualizações antes de publicar minha Base de conhecimento?"|
|30 - 50|Pouca confiança – tipicamente uma resposta relacionada que responde parcialmente à intenção do usuário|"O que salvar e treinar faz?"|
|< 30|Muito pouca confiança – normalmente não responde à consulta do usuário, mas apresenta algumas palavras ou frases correspondentes |"Onde posso adicionar sinônimos à minha Base de conhecimento"|
|0|Nenhuma correspondência, portanto, a resposta não é retornada.|"Quanto custa o serviço"|

## <a name="choose-a-score-threshold"></a>Escolher um limite de pontuação
A tabela acima mostra as pontuações que são esperadas na maioria das Bases de conhecimento. No entanto, como cada KB é diferente e tem tipos diferentes de palavras, intenções e metas-recomendamos que você teste e escolha o limite que melhor funciona para você. Por padrão, o limite é definido como 0, para que todas as respostas possíveis sejam retornadas. O limite recomendado que deve funcionar para a maioria dos KBs é de **50**.

Ao escolher seu limite, tenha em mente o equilíbrio entre Precisão e Cobertura e ajuste seu limite com base em seus requisitos.

- Se **Precisão** for mais importante para seu cenário, aumente seu limite. Dessa forma, sempre que você retornar uma resposta, será muito mais SEGURO e muito mais provável que seja a resposta que os usuários estão procurando. Nesse caso, você pode acabar deixando mais perguntas sem resposta. *Por exemplo:* se seu limite for **70**, é provável que você perca alguns exemplos ambíguos como "o que é salvar e treinar?".

- Se a **Cobertura** (ou o retorno de chamada) for mais importante, e você quiser responder ao maior número de perguntas possível, mesmo que haja apenas uma relação parcial com a pergunta do usuário, então DIMINUA o limite. Isso significa que pode haver mais casos em que a resposta não responda à consulta real do usuário, mas forneça alguma outra resposta relacionada. *Por exemplo:* se você tornar o limite **30**, poderá fornecer respostas para consultas como "onde posso editar meu KB?"

> [!NOTE]
> As versões mais recentes do QnA Maker incluem melhorias na lógica de pontuação e poderão afetar seu limite. Sempre que atualizar o serviço, certifique-se de testar e ajustar o limite, se necessário. Você pode verificar a versão do Serviço QnA [aqui](https://www.qnamaker.ai/UserSettings) e saber como obter as atualizações mais recentes [aqui](../How-To/configure-QnA-Maker-resources.md#get-the-latest-runtime-updates).

## <a name="set-threshold"></a>Definir limite

Defina a pontuação de limite como uma propriedade do [corpo JSON da API GenerateAnswer](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration). Isso significa que você o define para cada chamada para GenerateAnswer.

Na estrutura de bot, defina a Pontuação como parte do objeto de opções com [C#](../how-to/metadata-generateanswer-usage.md?#use-qna-maker-with-a-bot-in-c) ou [Node.js](../how-to/metadata-generateanswer-usage.md?#use-qna-maker-with-a-bot-in-nodejs).

## <a name="improve-confidence-scores"></a>Melhorar as pontuações de confiança
Para melhorar a pontuação de confiança de uma resposta específica a uma consulta de usuário, você poderá adicionar a consulta do usuário à base de dados de conhecimento como uma pergunta alternativa na resposta. Você também pode usar [alterações de palavra](/rest/api/cognitiveservices/qnamaker/alterations/replace) que não diferenciam maiúsculas de minúsculas para adicionar sinônimos a palavras-chave em sua base de conhecimento.


## <a name="similar-confidence-scores"></a>Pontuações de confiança semelhantes
Quando várias respostas tiverem uma pontuação de confiança semelhante, é provável que a consulta tenha sido muito genérica e, portanto, com correspondências com probabilidades iguais com várias respostas. Tente estruturar melhor suas perguntas e respostas para que cada entidade QnA tenha uma intenção distinta.


## <a name="confidence-score-differences-between-test-and-production"></a>Diferenças de Pontuação de confiança entre teste e produção
A pontuação de confiança de resposta pode alterar pouco entre o teste e a versão publicada da base de dados de conhecimento, mesmo se o conteúdo for o mesmo. Isso ocorre porque o conteúdo do teste e a base de dados de conhecimento publicado estão localizados em diferentes índices de Pesquisa Cognitiva do Azure.

O índice de teste contém todos os pares de QnA de suas bases de dados de conhecimento. Ao consultar o índice de teste, a consulta se aplica a todo o índice. em seguida, os resultados são restritos à partição para essa base de dados de conhecimento específica. Se os resultados da consulta de teste estiverem afetando negativamente a sua capacidade de validar a base de dados de conhecimento, você poderá:
* Organize sua base de dados de conhecimento usando uma das seguintes opções:
    * 1 recurso restrito a 1 KB: restrinja seu único recurso QnA (e o índice de teste Pesquisa Cognitiva do Azure resultante) a uma única base de dados de conhecimento.
    * 2 recursos-1 para teste, 1 para produção: tenha dois recursos de QnA Maker, usando um para teste (com seus próprios índices de teste e produção) e outro para o produto (também tendo seus próprios índices de teste e produção)
* e sempre usam os mesmos parâmetros, como **[Top](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)** ao consultar a base de dados de conhecimento de teste e de produção

Ao publicar uma base de dados de conhecimento, o conteúdo de perguntas e respostas base de dados de conhecimento é movido do índice de teste para um índice de produção no Azure Search. Veja como funciona a operação [publicar](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).

Se você tiver uma base de dados de conhecimento em regiões diferentes, cada região usará seu próprio índice de Pesquisa Cognitiva do Azure. Como índices diferentes são usados, as pontuações não serão exatamente as mesmas.


## <a name="no-match-found"></a>Nenhuma correspondência encontrada
Quando nenhuma boa correspondência for encontrada pelo classificador, a pontuação de confiança 0,0 ou "Nenhuma" é retornada e a resposta padrão é "Nenhuma correspondência boa encontrada na KB". Você pode substituir essa [resposta padrão](../How-To/metadata-generateanswer-usage.md) no bot ou no código do aplicativo que chama o ponto de extremidade. Como alternativa, você também pode definir a resposta de substituição no Azure, e isso altera o padrão para todas as bases de dados de conhecimento implantadas em um determinado serviço QnA Maker.

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Práticas recomendadas](./best-practices.md)
