---
title: Ciência de dados para iniciantes
titleSuffix: Azure Machine Learning Studio
description: Ciência de dados para iniciantes ensina os conceitos básicos em cinco vídeos curtos, começando com as cinco perguntas que a ciência de dados responde. Do Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.custom: seodec18
ms.date: 03/22/2019
ms.openlocfilehash: d89a701f1d4528e1f3dff08daf31873891778f07
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60751312"
---
# <a name="data-science-for-beginners-video-1-the-5-questions-data-science-answers"></a>Ciência de dados para iniciantes, vídeo 1: As cinco perguntas que a ciência de dados responde
Obtenha uma breve introdução à ciência de dados com *Ciência de dados para iniciantes* em cinco vídeos curtos de um grande cientista de dados. Esses vídeos são básicos, mas úteis, esteja você interessado em fazer a ciência de dados ou trabalhar com os cientistas de dados.

Este primeiro vídeo é sobre os tipos de perguntas que a ciência de dados pode responder. Para aproveitar ao máximo da série, assista a todos os vídeos. [Acesse a lista de vídeos](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Shows/SupervisionNotRequired/8/player]
>
>

## <a name="other-videos-in-this-series"></a>Outros vídeos nesta série
*Ciência de dados para iniciantes* é uma breve introdução à ciência de dados levando cerca de 25 minutos no total. Confira todos os cinco vídeos:

* Vídeo 1: As cinco perguntas que a ciência de dados responde
* Vídeo 2: [Seus dados estão prontos para a ciência de dados?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 min 56 s)*
* Vídeo 3: [Faça uma pergunta que você poderá responder usando dados](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 min 17 s)*
* Vídeo 4: [Preveja uma resposta com um modelo simples](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 min 42 s)*
* Vídeo 5: [Copie o trabalho de outras pessoas para fazer a ciência de dados](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 s)*

## <a name="transcript-the-5-questions-data-science-answers"></a>Transcrição: As cinco perguntas que a ciência de dados responde
Olá! Bem-vindo à série de vídeos *Ciência de dados para iniciantes*.

O ciência de dados pode ser intimidadora, portanto, vou apresentar os conceitos básicos sem equações ou jargão de programação de computador.

Neste primeiro vídeo, falaremos sobre "As cinco perguntas que a ciência de dados responde".

A ciência de dados usa números e nomes (também conhecidos como categorias ou rótulos) para prever as respostas às perguntas.

Isso pode surpreender você, mas *há apenas cinco perguntas que a ciência de dados responde*:

* Isso é A ou B?
* Isso é estranho?
* Quanto custa – ou – Quantas?
* Como isso é organizado?
* O que devo fazer agora?

Cada uma dessas perguntas é respondida por uma família separada de métodos de aprendizado de máquina, chamados de algoritmos.

É útil pensar em um algoritmo como uma receita, e seus dados como os ingredientes. Um algoritmo informa como combinar e mesclar os dados para obter uma resposta. Os computadores são como o liquidificador. Eles fazem o trabalho mais pesado do algoritmo, e fazem isso bem rápido.

## <a name="question-1-is-this-a-or-b-uses-classification-algorithms"></a>Pergunta 1: isso é A ou B? usa algoritmos de classificação
Vamos começar com a pergunta: Isso é A ou B?

![Algoritmos de classificação: Isso é A ou B?](./media/data-science-for-beginners-the-5-questions-data-science-answers/classification-algorithms.png)

Essa família de algoritmos é chamada de classificação de duas classes.

Ela é útil para qualquer pergunta que tenha apenas duas respostas possíveis.

Por exemplo: 

* Este pneu falhará nas próximas mil milhas: sim ou não?
* O que traz mais clientes: um cupom de $5 ou 25% de desconto?

Essa pergunta também pode ser refeita para incluir mais de duas opções: Isso é A, B, C ou D, etc.?  Isso é chamado de classificação multiclasse e é útil quando você tem várias ou vários milhares de respostas possíveis. A classificação multiclasse escolhe a mais provável.

## <a name="question-2-is-this-weird-uses-anomaly-detection-algorithms"></a>Pergunta 2: isso é estranho? usa algoritmos de detecção de anomalias
A próxima pergunta que a ciência de dados pode responder é: Isso é estranho? Essa pergunta é respondida por uma família de algoritmos chamados de detecção de anomalias.

![Algoritmos de detecção de anomalias: Isso é estranho?](./media/data-science-for-beginners-the-5-questions-data-science-answers/anomaly-detection-algorithms.png)

Se você tiver um cartão de crédito, já terá se beneficiado da detecção de anomalias. Sua empresa de cartão de crédito analisa os padrões de compra, para que possam alertar você sobre uma possíveis fraudes. Os encargos "estranhos" podem ser uma compra em uma loja onde você normalmente não compra, ou uma compra de um item muito caro.

Essa questão pode ser útil de várias formas. Por exemplo:

* Se você tem um carro com medidores de pressão, talvez queira saber: esse medidor de pressão está lendo normalmente?
* Se você está monitorando a Internet, deseja saber: esta mensagem da Internet é típica?

A detecção de anomalias sinaliza comportamentos ou eventos incomuns ou inesperados. Ela dá dicas sobre onde procurar problemas.

## <a name="question-3-how-much-or-how-many-uses-regression-algorithms"></a>Pergunta 3: quanto? ou quantos? usa algoritmos de regressão
O Machine Learning também pode prever a resposta para Quanto custa? ou Quantos? A família de algoritmo que responde a essa pergunta é chamada de regressão.

![Algoritmos de regressão: quanto? ou quantos?](./media/data-science-for-beginners-the-5-questions-data-science-answers/regression-algorithms.png)

Algoritmos de regressão fazem previsões numéricas, como:

* Qual será a temperatura na próxima terça-feira?  
* Como serão as vendas em meu quarto trimestre?

Eles ajudam a responder qualquer pergunta cuja resposta seja um número.

## <a name="question-4-how-is-this-organized-uses-clustering-algorithms"></a>Pergunta 4: como isso é organizado? usa algoritmos de clustering
Agora, as duas últimas perguntas são um pouco mais avançadas.

Às vezes, você deseja compreender a estrutura de um conjunto de dados - Como isso é organizado? Para essa pergunta, não há exemplos para os quais você já conhece os resultados.

Há muitas maneiras de revelar a estrutura dos dados. Uma abordagem é o clustering. Ele separa os dados em "grupos" naturais, para facilitar a interpretação. Com o clustering, não há uma resposta correta.

![Algoritmos de clustering: Como isso é organizado?](./media/data-science-for-beginners-the-5-questions-data-science-answers/clustering-algorithms.png)

Exemplos comuns de perguntas com clustering são:

* Quais expectadores gostam dos mesmos tipos de filmes?
* Quais modelos de impressora falham da mesma forma?

Ao compreender como os dados são organizado, você pode entender melhor, e prever, comportamentos e eventos.  

## <a name="question-5-what-should-i-do-now-uses-reinforcement-learning-algorithms"></a>Pergunta 5: o que devo fazer agora? usa algoritmos de aprendizado de reforço
A última pergunta – o que devo fazer agora? – usa uma família de algoritmos chamada de aprendizado de reforço.

Aprendizado de reforço foi inspirado pelo modo como o cérebro dos ratos e dos humanos responde ao sofrimento e às recompensas. Esses algoritmos aprendem com os resultados e decidem sobre a próxima ação.

Normalmente, o aprendizado de reforço é uma boa opção para sistemas automatizados que precisam tomar muitas decisões pequenas sem orientação humana.

![Algoritmos de aprendizado de reforço: O que devo fazer agora?](./media/data-science-for-beginners-the-5-questions-data-science-answers/reinforcement-learning-algorithms.png)

As perguntas que eles respondem são sempre sobre qual ação deve ser executada, geralmente por um computador ou um robô. Os exemplos abrangem:

* Se eu fosse um sistema de controle de temperatura de uma casa: ajustar a temperatura ou deixá-la como está?  
* Se eu fosse um carro autônomo: no farol amarelo, frear ou acelerar?  
* Para um robô aspirador de pó: continuar a aspiração ou voltar para a estação de carregamento?

Algoritmos de aprendizado de reforço coletam os dados conforme eles surgem, aprendendo por tentativa e erro.

Então é isso, As cinco perguntas que a ciência de dados pode responder.

## <a name="next-steps"></a>Próximas etapas
* [Teste um primeiro experimento da ciência de dados com o Machine Learning Studio](create-experiment.md)
* [Obtenha uma introdução ao Machine Learning no Microsoft Azure](/azure/machine-learning/preview/overview-what-is-azure-ml)
