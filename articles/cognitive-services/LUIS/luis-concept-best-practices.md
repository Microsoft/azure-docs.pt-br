---
title: Práticas recomendadas-LUIS
titleSuffix: Azure Cognitive Services
description: Conheça as melhores práticas do LUIS para obter os melhores resultados do seu modelo de aplicativo do LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 7a2802bd4daa1a009c610688120c9a56583b054f
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639283"
---
# <a name="best-practices-for-building-a-language-understanding-app-with-cognitive-services"></a>Melhores práticas para criação de um aplicativo de reconhecimento vocal com os Serviços Cognitivos
Use o processo de criação de aplicativos para criar seu aplicativo LUIS: 

* Criar modelo de linguagem
* Adicione alguns exemplos de declaração de treinamento (10-15 por intenção)
* Publicar 
* Testar do ponto de extremidade 
* Adicionar recursos

Depois que seu aplicativo for [publicado](luis-how-to-publish-app.md), use o ciclo de criação para adicionar recursos, publicar e testar do ponto de extremidade. Não comece o próximo ciclo de criação adicionando mais declarações de exemplo. Isso não permite que o LUIS aprenda seu modelo com declarações de usuário do mundo real. 

Para o LUIS ser eficiente em seu trabalho de aprendizado, não expanda as declarações até que o conjunto atual de declarações de exemplo e de ponto de extremidade esteja retornando pontuações confiantes e de alta previsão. Melhore as pontuações usando [aprendizado ativo](luis-concept-review-endpoint-utterances.md), [padrões](luis-concept-patterns.md) e [lista de frase](luis-concept-feature.md). 

## <a name="do-and-dont"></a>O que fazer e o que não fazer
A lista a seguir inclui melhores práticas para aplicativos LUIS:

|Certo|Errado|
|--|--|
|[Definir intenções distintas](#do-define-distinct-intents) |[Adicionar muitos exemplos de declaração a intenções](#dont-add-many-example-utterances-to-intents) |
|[Localizar um ponto ideal entre muito genérico e muito específico para cada intenção](#do-find-sweet-spot-for-intents)|[Usar o LUIS como uma plataforma de treinamento](#dont-use-luis-as-a-training-platform)|
|[Criar seu aplicativo iterativamente](#do-build-the-app-iteratively)|[Adicionar muitas declarações de exemplo do mesmo formato, ignorando os outros formatos](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[Adicionar listas de frase e padrões em iterações posteriores](#do-add-phrase-lists-and-patterns-in-later-iterations)|[Misturar a definição de intenções e entidades](#dont-mix-the-definition-of-intents-and-entities)|
|[Equilibrar suas declarações em todas as intenções](#balance-your-utterances-across-all-intents) exceto a intenção Nenhum.<br>[Adicione declarações de exemplo à intenção None](#do-add-example-utterances-to-none-intent)|[Criar listas de frase com todos os valores possíveis](#dont-create-phrase-lists-with-all-the-possible-values)|
|[Aproveitar o recurso de sugestão para aprendizado ativo](#do-leverage-the-suggest-feature-for-active-learning)|[Adicionar muitos padrões](#dont-add-many-patterns)|
|[Monitorar o desempenho do seu aplicativo](#do-monitor-the-performance-of-your-app)|[Treinar e publicar com cada declaração de exemplo única adicionada](#dont-train-and-publish-with-every-single-example-utterance)|
|[Usar versões para cada iteração do aplicativo](#do-use-versions-for-each-app-iteration)||

## <a name="do-define-distinct-intents"></a>Defina intenções distintas
Verifique se o vocabulário para cada intenção é apenas para essa intenção e não está se sobrepondo a uma intenção diferente. Por exemplo, se desejar ter um aplicativo que manipula organizações de viagem como voos por companhia aérea e hotéis, será possível optar por tê-las como áreas de assunto, intenções separadas ou a mesma intenção com entidades para dados específicos dentro da declaração.

Se o vocabulário entre duas intenções for o mesmo, combine a intenção e use entidades. 

Considere os seguintes exemplos de declaração:

|Exemplo de enunciados|
|--|
|Reservar um voo|
|Reservar um hotel|

"Reservar um voo" e "Reservar hotel" usam o mesmo vocabulário de "reservar um ". Esse formato é o mesmo, portanto deve ser a mesma intenção com palavras diferentes de voo e hotel como entidades extraídas. 

Para mais informações:
* Conceito: [Conceitos sobre intenções em seu aplicativo LUIS](luis-concept-intent.md)
* Tutorial: [Criar aplicativo LUIS para determinar as intenções dos usuários](luis-quickstart-intents-only.md)
* Como: [Adicionar intenções para determinar a intenção do usuário de enunciados](luis-how-to-add-intents.md)


## <a name="do-find-sweet-spot-for-intents"></a>Localizar ponto ideal para intenções
Use dados de previsão do LUIS para determinar se suas intenções estiverem se sobrepondo. Intenções sobrepostas confundem o LUIS. O resultado é que a principal intenção de pontuação está muito perto de outra intenção. Como o LUIS não usa o mesmo caminho exato por meio dos dados para treinamento a cada vez, uma intenção sobreposta tem uma chance de ser a primeira ou a segunda no treinamento. Convém que a pontuação da declaração para cada intenção esteja mais distante para que esse flip/flop não aconteça. A boa distinção para intenções deve resultar na principal intenção esperada toda vez. 
 
## <a name="do-build-the-app-iteratively"></a>Criar o aplicativo iterativamente
Mantenha um conjunto separado de declarações que não é usado como [declarações de exemplo](luis-concept-utterance.md) ou declarações de ponto de extremidade. Continue melhorando o aplicativo para seu conjunto de testes. Adapte o conjunto de teste para refletir declarações de usuário real. Use este conjunto de teste para avaliar cada iteração ou versão do aplicativo. 

Os desenvolvedores devem ter três conjuntos de dados. O primeiro é o exemplo de declarações para criar o modelo. O segundo é para testar o modelo no ponto de extremidade. O terceiro são os dados do teste cego usados no [teste de lote](luis-how-to-batch-test.md). Esse último conjunto não é usado no treinamento do aplicativo nem enviado no ponto de extremidade.  

Para mais informações:
* Conceito: [Ciclo de criação de seu aplicativo LUIS](luis-concept-app-iteration.md)

## <a name="do-add-phrase-lists-and-patterns-in-later-iterations"></a>Adicione listas de frase e padrões em iterações posteriores

Recomenda-se não aplicar essas práticas antes de seu aplicativo ser testado. Você deve entender como o aplicativo se comporta antes de adicionar [listas de frases](luis-concept-feature.md) e [padrões](luis-concept-patterns.md) , pois esses recursos são ponderados de forma mais intensa do que o exemplo declarações e distorcerá a confiança. 

Depois de compreender como o seu aplicativo se comporta sem elas, adicione cada um desses recursos que se aplicam ao seu aplicativo. Você não precisa adicionar esses recursos a cada [iteração](luis-concept-app-iteration.md) nem alterá-los a cada versão. 

Não há nenhum problema em adicioná-los no início do design de seu modelo, mas é mais fácil ver como cada recurso altera os resultados depois que o modelo for testado com expressões vocais. 

Uma boa prática é testar por meio do [ponto de extremidade](luis-get-started-create-app.md#query-the-endpoint-with-a-different-utterance) para que você obtenha o benefício adicional do [aprendizado ativo](luis-concept-review-endpoint-utterances.md). O [painel de teste interativo](luis-interactive-test.md) também é uma metodologia de teste válida. 
 

### <a name="phrase-lists"></a>Listas de frase

As [listas de frase](luis-concept-feature.md) permitem que você defina dicionários de palavras relacionadas ao seu domínio de aplicativo. Propague sua lista de frase com poucas palavras e use o recurso de sugestão para que o LUIS conheça mais palavras no vocabulário específico para o seu aplicativo. Uma lista de frases melhora a detecção da intenção e a classificação da entidade, aumentando o sinal associado a palavras ou frases que sejam significativas para seu aplicativo. 

Não adicione toda palavra ao vocabulário, pois a lista de frases não é uma correspondência exata. 

Para mais informações:
* Conceito: [Recurso de lista de frases em seu aplicativo LUIS](luis-concept-feature.md)
* Instruções: [Usar listas de frases para melhorar o sinal da lista de palavras](luis-how-to-add-features.md)

### <a name="patterns"></a>Padrões

Declarações de usuário real do ponto de extremidade, muito semelhantes umas às outras, podem revelar padrões de escolha e de posicionamento de palavras. O recurso [padrão](luis-concept-patterns.md) usa esta escolha e posicionamento de palavras junto com expressões regulares para melhorar a precisão da previsão. Uma expressão regular no padrão possibilita palavras e pontuação que você pretende ignorar enquanto ainda estiver fazendo correspondência com o padrão. 

Use a [sintaxe opcional](luis-concept-patterns.md) do padrão para pontuação para que a pontuação possa ser ignorada. Use a [lista explícita](luis-concept-patterns.md#explicit-lists) para compensar os problemas de sintaxe pattern.any. 

Para mais informações:
* Conceito: [Padrões aumentam a precisão da previsão](luis-concept-patterns.md)
* Instruções: [Como adicionar Padrões para aumentar a precisão da previsão](luis-how-to-model-intent-pattern.md)

## <a name="balance-your-utterances-across-all-intents"></a>Equilibrar suas declarações em todas as intenções

Para que o LUIS tenha previsões precisas, a quantidade de enunciados de exemplo em cada intenção (exceto para a intenção Nenhum), deve ser relativamente semelhante. 

Se você tiver uma intenção com 100 declarações de exemplo e uma intenção com 20 declarações de exemplo, a intenção de expressão 100 terá uma taxa mais alta de previsão.  

## <a name="do-add-example-utterances-to-none-intent"></a>Adicionar declarações de exemplo à intenção None

Essa é a intenção fallback, indicou tudo fora do seu aplicativo. Adicione um exemplo de declaração à intenção None para todos os 10 exemplos de declarações no restante do seu aplicativo LUIS.

Para mais informações:
* Conceito: [Reconheça quais são os enunciados ideais para o aplicativo LUIS](luis-concept-utterance.md)

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>Aproveite o recurso de sugestão para aprendizado ativo

Use **Examinar declarações de ponto de extremidade** do [aprendizado ativo](luis-how-to-review-endpoint-utterances.md) regularmente, em vez de adicionar mais exemplos de declarações a intenções. Como o aplicativo está constantemente recebendo declarações de ponto de extremidade, essa lista está crescendo e mudando.

Para mais informações:
* Conceito: [Conceitos para habilitar o aprendizado ativo revisando enunciados de ponto de extremidade](luis-concept-review-endpoint-utterances.md)
* Tutorial: [Tutorial: Consertar previsões incertas examinando os enunciados de ponto de extremidade](luis-tutorial-review-endpoint-utterances.md)
* Instruções: [Como examinar enunciados de ponto de extremidade no portal do LUIS](luis-how-to-review-endpoint-utterances.md)

## <a name="do-monitor-the-performance-of-your-app"></a>Monitore o desempenho do seu aplicativo

Monitore a precisão da previsão usando um [conjunto de teste em lote](luis-concept-batch-test.md). 

## <a name="dont-add-many-example-utterances-to-intents"></a>Não adicione muitos exemplos de declaração a intenções

Depois que o aplicativo for publicado, adicione apenas declarações do aprendizado ativo no processo iterativo. Se as declarações forem semelhantes demais, adicione um padrão. 

## <a name="dont-use-luis-as-a-training-platform"></a>Não use o LUIS como uma plataforma de treinamento

O LUIS é específico para o domínio de um modelo de linguagem. Ele não é destinado a trabalhar como uma plataforma de treinamento geral de idioma natural. 

## <a name="dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats"></a>Não adicionar muitas declarações de exemplo do mesmo formato, ignorando os outros formatos

O LUIS espera variações nas declarações de uma intenção. As declarações podem variar e ter o mesmo significado geral. As variações podem incluir o comprimento da declaração, a escolha de palavras e o posicionamento de palavras. 

|Não use o mesmo formato|Use o formato variável|
|--|--|
|Comprar uma passagem para Seattle<br>Comprar uma passagem para Paris<br>Comprar uma passagem para Orlando|Comprar 1 passagem para Seattle<br>Reservar duas poltronas no voo para Paris na próxima segunda-feira<br>Eu gostaria de reservar três passagens para Orlando para a semana do saco cheio|

A segunda coluna usa diferentes verbos (comprar e reservar), diferentes quantidades (1, duas, 3) e diferentes organizações de palavras, mas todas têm a mesma intenção de comprar passagens de avião para viagem. 

## <a name="dont-mix-the-definition-of-intents-and-entities"></a>Não misture a definição de intenções e entidades

Criar uma intenção para qualquer ação que seu bot usará. Use entidades como parâmetros que possibilitam a ação. 

Para obter um chatbot que comprará voos de companhias aéreas, crie uma intenção **BookFlight**. Não crie uma intenção para toda companhia aérea ou para todo destino. Use essas partes de dados como [entidades](luis-concept-entity-types.md) e marque-as nos exemplos de declarações. 

## <a name="dont-create-phrase-lists-with-all-the-possible-values"></a>Não crie listas de frases com todos os valores possíveis

Forneça alguns exemplos nas [listas de frases](luis-concept-feature.md), mas não em toda palavra. O LUIS generaliza e leva em conta o contexto. 

## <a name="dont-add-many-patterns"></a>Não adicione muitos padrões

Não adicione [padrões](luis-concept-patterns.md) demais. O LUIS destina-se a aprender rapidamente com menos exemplos. Não sobrecarregue o sistema desnecessariamente.

## <a name="dont-train-and-publish-with-every-single-example-utterance"></a>Não treine nem publique com toda declaração de exemplo única

Adicione 10 ou 15 declarações antes do treinamento e da publicação. Isso permite ver o impacto sobre a precisão da previsão. Adicionar uma única declaração pode não ter um impacto visível sobre a pontuação. 

## <a name="do-use-versions-for-each-app-iteration"></a>Use versões para cada iteração do aplicativo

Cada ciclo de criação deve estar dentro de uma nova [versão](luis-concept-version.md), clonada de uma versão existente. LUIS não tem limite para as versões. Um nome de versão é usado como parte da rota de API, portanto, é importante escolher caracteres permitidos em uma URL, bem como manter dentro a contagem de 10 caracteres para uma versão. Desenvolva uma estratégia de nome de versão para manter suas versões organizadas. 

Para mais informações:
* Conceito: [Reconhecer como e quando usar uma versão do LUIS](luis-concept-version.md)
* Instruções: [Usar versões para editar e testar sem afetar aplicativos de preparo ou produção](luis-how-to-manage-versions.md)


## <a name="next-steps"></a>Próximas etapas

* Saiba como [planejar seu aplicativo](luis-how-plan-your-app.md) no aplicativo LUIS.
