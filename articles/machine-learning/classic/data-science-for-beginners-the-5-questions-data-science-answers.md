---
title: 'ML Studio (clássico): ciência de dados para iniciantes – Azure'
description: Ciência de dados para iniciantes ensina os conceitos básicos em cinco vídeos curtos, começando com as cinco perguntas que a ciência de dados responde. Do Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.custom: seodec18
ms.date: 03/22/2019
ms.openlocfilehash: e9bbadc95be3479da82d91e9a4a4f712fbc32c57
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100520384"
---
# <a name="data-science-for-beginners-video-1-the-5-questions-data-science-answers"></a>Ciência de dados para iniciantes, vídeo 1: As cinco perguntas que a ciência de dados responde

Obtenha uma breve introdução à ciência de dados com *Ciência de dados para iniciantes* em cinco vídeos curtos de um grande cientista de dados. Esses vídeos são básicos, mas úteis, esteja você interessado em fazer a ciência de dados ou trabalhar com os cientistas de dados.

Este primeiro vídeo é sobre os tipos de perguntas que a ciência de dados pode responder. Para aproveitar ao máximo da série, assista a todos os vídeos. [Acessar a lista de vídeos](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Shows/SupervisionNotRequired/8/player]
>
>

## <a name="other-videos-in-this-series"></a>Outros vídeos nesta série
*Ciência de dados para iniciantes* é uma breve introdução à ciência de dados levando cerca de 25 minutos no total. Confira todos os cinco vídeos:

* Vídeo 1: As cinco perguntas que a ciência de dados responde
* Vídeo 2: [seus dados estão prontos para a ciência de dados?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 min 56 s)*
* Video 3: [Faça uma pergunta que você possa responder com dados](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 min 17 s)*
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

## <a name="question-1-is-this-a-or-b-uses-classification-algorithms"></a>Pergunta 1: Isso é A ou B? usa algoritmos de classificação
Vamos começar com a pergunta: Isso é A ou B?

![Algoritmos de classificação: Isso é A ou B?](./media/data-science-for-beginners-the-5-questions-data-science-answers/classification-algorithms.png)

Essa família de algoritmos é chamada de classificação de duas classes.

Ela é útil para qualquer pergunta que tenha apenas duas respostas possíveis.

Por exemplo:

* Este pneu falhará nos próximos 1000 Km: Sim ou não?
* O que traz mais clientes: um cupom de $5 ou 25% de desconto?

Essa questão também pode ser refeita para incluir mais do que duas opções: Isso é A ou B ou C ou D etc.?  Isso é chamado de classificação multiclasse e é útil quando você tem várias ou vários milhares de respostas possíveis. A classificação multiclasse escolhe a mais provável.

## <a name="question-2-is-this-weird-uses-anomaly-detection-algorithms"></a>Pergunta 2: Isso é estranho? usa algoritmos de detecção de anomalias
A próxima pergunta que a ciência de dados pode responder é: Isso é estranho? Essa pergunta é respondida por uma família de algoritmos chamados de detecção de anomalias.

![Algoritmos de detecção de anomalias: Isso é estranho?](./media/data-science-for-beginners-the-5-questions-data-science-answers/anomaly-detection-algorithms.png)

Se você tiver um cartão de crédito, já terá se beneficiado da detecção de anomalias. Sua empresa de cartão de crédito analisa os padrões de compra, para que possam alertar você sobre uma possíveis fraudes. Os encargos "estranhos" podem ser uma compra em uma loja onde você normalmente não compra, ou uma compra de um item muito caro.

Essa questão pode ser útil de várias formas. Por exemplo:

* Se você tiver um carro com medidores de pressão, talvez queira saber: Esse medidor está lendo normalmente?
* Se você estiver monitorando a Internet, convém saber: esta é a mensagem da Internet típica?

A detecção de anomalias sinaliza comportamentos ou eventos incomuns ou inesperados. Ela dá dicas sobre onde procurar problemas.

## <a name="question-3-how-much-or-how-many-uses-regression-algorithms"></a>Pergunta 3: Quanto custa? ou Quantos? usa algoritmos de regressão
O Machine Learning também pode prever a resposta para Quanto custa? ou Quantos? A família de algoritmo que responde a essa pergunta é chamada de regressão.

![Algoritmos de regressão: Quanto custa? ou Quantos?](./media/data-science-for-beginners-the-5-questions-data-science-answers/regression-algorithms.png)

Algoritmos de regressão fazem previsões numéricas, como:

* Qual será a temperatura na próxima terça-feira?  
* Como serão as vendas em meu quarto trimestre?

Eles ajudam a responder qualquer pergunta cuja resposta seja um número.

## <a name="question-4-how-is-this-organized-uses-clustering-algorithms"></a>Pergunta 4: Como isso é organizado? usa algoritmos de clustering
Agora, as duas últimas perguntas são um pouco mais avançadas.

Às vezes, você deseja compreender a estrutura de um conjunto de dados - Como isso é organizado? Para essa pergunta, você não tem exemplos dos quais já conhece os resultados.

Há muitas maneiras de revelar a estrutura dos dados. Uma abordagem é o clustering. Ele separa os dados em "grupos" naturais, para facilitar a interpretação. Com o clustering, não há uma resposta correta.

![Algoritmos de clustering: Como isso é organizado?](./media/data-science-for-beginners-the-5-questions-data-science-answers/clustering-algorithms.png)

Exemplos comuns de perguntas com clustering são:

* Quais expectadores gostam dos mesmos tipos de filmes?
* Quais modelos de impressora falham da mesma forma?

Ao compreender como os dados são organizado, você pode entender melhor, e prever, comportamentos e eventos.  

## <a name="question-5-what-should-i-do-now-uses-reinforcement-learning-algorithms"></a>Pergunta 5: O que devo fazer agora? usa algoritmos de aprendizado de reforço
A última pergunta – o que devo fazer agora? – usa uma família de algoritmos chamada de aprendizado de reforço.

Aprendizado de reforço foi inspirado pelo modo como o cérebro dos ratos e dos humanos responde ao sofrimento e às recompensas. Esses algoritmos aprendem com os resultados e decidem sobre a próxima ação.

Normalmente, o aprendizado de reforço é uma boa opção para sistemas automatizados que precisam tomar muitas decisões pequenas sem orientação humana.

![Algoritmos de aprendizado de reforço: O que devo fazer agora?](./media/data-science-for-beginners-the-5-questions-data-science-answers/reinforcement-learning-algorithms.png)

As perguntas que eles respondem são sempre sobre qual ação deve ser executada, geralmente por um computador ou um robô. São exemplos:

* Se eu for um sistema de controle de temperatura de uma casa: ajusto a temperatura ou deixo como está?  
* Se eu for um carro automático: no farol amarelo, freio ou acelero?  
* Para um robô aspirador: continuo a aspiração ou volto para a estação de carregamento?

Algoritmos de aprendizado de reforço coletam os dados conforme eles surgem, aprendendo por tentativa e erro.

Então é isso, As cinco perguntas que a ciência de dados pode responder.

## <a name="next-steps"></a>Próximas etapas
* [Experimente um primeiro experimento de ciência de dados com Machine Learning Studio (clássico)](create-experiment.md)
* [Obtenha uma introdução ao Machine Learning no Microsoft Azure](../overview-what-is-azure-ml.md)