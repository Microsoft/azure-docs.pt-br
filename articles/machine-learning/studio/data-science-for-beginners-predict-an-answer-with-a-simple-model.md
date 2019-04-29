---
title: Prever respostas com modelos de regressão
titleSuffix: Azure Machine Learning Studio
description: Como criar um modelo simples de regressão para prever um preço no vídeo 4, Ciência de dados para iniciantes. Inclui uma regressão linear com os dados de destino.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.custom: seodec18
ms.date: 03/22/2019
ms.openlocfilehash: 9165e51d07cf97756408c7f73720931abe067bb2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60751511"
---
# <a name="predict-an-answer-with-a-simple-model"></a>Prever uma resposta com um modelo simples
## <a name="video-4-data-science-for-beginners-series"></a>Vídeo 4: Série Ciência de dados para iniciantes
Aprenda como criar um modelo simples de regressão para prever o preço de um diamante no vídeo 4, Ciência de dados para iniciantes. Vamos desenhar um modelo de regressão com dados de destino.

Para aproveitar ao máximo da série, assista a todos os vídeos. [Acesse a lista de vídeos](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/data-science-for-beginners-series-predict-an-answer-with-a-simple-model/player]
>
>

## <a name="other-videos-in-this-series"></a>Outros vídeos nesta série
*Ciência de dados para iniciantes* é uma breve introdução à ciência de dados em cinco vídeos curtos.

* Vídeo 1: [As cinco perguntas que a ciência de dados responde](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5min 14s)*
* Vídeo 2: [Seus dados estão prontos para a ciência de dados?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 min 56 s)*
* Vídeo 3: [Faça uma pergunta que você poderá responder usando dados](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 min 17 s)*
* Vídeo 4: Prever uma resposta com um modelo simples
* Vídeo 5: [Copie o trabalho de outras pessoas para fazer a ciência de dados](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 s)*

## <a name="transcript-predict-an-answer-with-a-simple-model"></a>Transcrição: Prever uma resposta com um modelo simples
Bem-vindos ao quarto vídeo da série "Ciência de dados para iniciantes". Neste vídeo, vamos criar um modelo simples e fazer uma previsão.

Um *modelo* é uma história simplificada sobre nossos dados. Mostrarei o que quero dizer.

## <a name="collect-relevant-accurate-connected-enough-data"></a>Colete dados relevantes, precisos, conectados e suficientes
Vamos supor que eu queira comprar um diamante. Eu tenho um anel que pertencia a minha avó com um espaço para um diamante de 1,35 quilate, e eu quero ter uma ideia de quanto esse diamante custará. Levo um bloco de notas e uma caneta até a loja de joias e anoto o preço e o quilate de todos os diamantes em exposição. Começando com o primeiro diamante: ele tem 1,01 quilate e custa $7.366.

Agora eu faço isso para os outros diamantes na loja.

![Colunas de dados de diamante](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/diamond-data.png)

Observe que a nossa lista tem duas colunas. Cada coluna tem um atributo diferente - peso em quilate e preço - e cada linha é um único ponto de dados que representa um único diamante.

Na verdade, criamos um pequeno conjunto de dados aqui; uma tabela. Observe que isso atende aos nossos critérios de qualidade:

* Os dados são **relevantes** — o peso está definitivamente relacionado ao preço
* Eles são **precisos** — verificamos os preços que anotamos mais de uma vez
* Eles estão **conectados** — não há espaço em branco em qualquer uma dessas colunas
* E, como veremos, eles têm uma quantidade **suficiente** de dados para responder à nossa pergunta

## <a name="ask-a-sharp-question"></a>Faça uma pergunta inteligente
Agora, faremos nosso pergunta de uma forma direta: "Quanto custará para comprar um diamante de 1,35 quilate?"

Nossa lista não tem um diamante de 1,35 quilate, então teremos que usar o restante de dados para obter uma resposta para a pergunta.

## <a name="plot-the-existing-data"></a>Plotar os dados existentes
A primeira coisa que faremos é desenhar uma linha horizontal de números, chamada de eixo, para colocar os pesos no gráfico. A faixa dos pesos vai de 0 a 2, portanto, vamos desenhar uma linha que cubra essa faixa e colocar marcações a cada meio quilate.

Em seguida, desenharemos um eixo vertical para registrar o preço e o conectaremos ao eixo horizontal de peso. Isso será em unidades de dólares. Agora temos um conjunto de eixos coordenados.

![Eixos de peso e preço](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/weight-and-price-axes.png)

Agora, vamos pegar esses dados e transformá-los em um *gráfico de dispersão*. Essa é uma excelente maneira de visualizar conjuntos de dados numéricos.

Para o primeiro ponto de dados, desenhamos uma linha vertical no quilate 1,01. Em seguida, desenhamos uma linha horizontal em $7.366. No local onde elas se encontram, desenhamos um ponto. Isso representa nosso primeiro diamante.

Agora fazemos isso para cada diamante nesta lista. Quando terminarmos, este será o resultado: um monte de pontos, um para cada diamante.

![Gráfico de dispersão](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/scatter-plot.png)

## <a name="draw-the-model-through-the-data-points"></a>Desenhar o modelo usando os pontos de dados
Agora, se você os pontos e semicerrar os olhos, a coleção parecerá uma linha espessa e difusa. Podemos usar nosso marcador para desenhar uma linha reta através deles.

Desenhando uma linha, criamos um *modelo*. Pense nisso como pegar o mundo real e fazer uma versão simples em desenho dele. Agora, o desenho está incorreto, a linha não passa por todos os pontos de dados. Mas, é uma simplificação útil.

![Linha da regressão linear](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/linear-regression-line.png)

O fato de que todos os pontos não passam exatamente pela linha não tem qualquer problema. Cientistas de dados explicam isso dizendo que há o modelo (essa é a linha) e cada ponto tem algum *ruído* ou *variação* associado a ele. Há a relação perfeita subjacente, e há o mundo real que adiciona ruído e incerteza.

Como estamos tentando responder à pergunta *quanto custa?*, isso é chamado de *regressão*. E, como estamos usando uma linha reta, é uma *regressão linear*.

## <a name="use-the-model-to-find-the-answer"></a>Usar o modelo para encontrar a resposta
Agora temos um modelo e a nossa pergunta é: Quanto custará um diamante de 1,35 quilate?

Para responder à nossa pergunta, nós identificamos visualmente o 1,35 quilate e desenhamos uma linha vertical. Onde ela cruzar a linha do modelo, identificamos visualmente uma linha horizontal no eixo de dólar. Ela atinge diretamente 10.000. Pronto! Essa é a resposta: Um diamante de 1,35 quilate custa aproximadamente $10.000.

![Encontrar a resposta no modelo](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/find-the-answer.png)

## <a name="create-a-confidence-interval"></a>Criar um intervalo de confiança
É natural se preocupar com a precisão dessa previsão. É muito útil saber se o preço do diamante de 1,35 quilate será muito próximo de $10.000, mais barato ou mais caro. Para descobrir isso, vamos desenhar um envelope ao redor da linha de regressão que inclua a maioria dos pontos. Esse envelope é chamado nosso *intervalo de confiança*: Estamos bem confiantes de que os preços se enquadram nesse envelope, pois, no passado, a maioria deles se enquadrou. Podemos desenhar outras duas linhas horizontais a partir das quais a linha de 1,35 quilate cruza a parte superior e inferior do envelope.

![intervalo de confiança](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/confidence-interval.png)

Agora podemos dizer algo sobre o intervalo de confiança:  Podemos dizer com segurança que o preço de um diamante de 1,35 quilate é de aproximadamente $10.000, mas pode ser $8.000 e pode ser $12.000.

## <a name="were-done-with-no-math-or-computers"></a>Pronto, sem matemática ou computadores
Fizemos o que os cientistas de dados são pagos para fazer, e fizemos isso apenas desenhando:

* Fizemos uma pergunta que pudemos responder com dados
* Criamos um *modelo* usando a *regressão linear*
* Fizemos uma *previsão*, completa com um *intervalo de confiança*

E não usamos matemática ou computadores para isso.

Agora, se tivéssemos mais informações, como...

* o design do diamante
* variações de cor (quão próximo do branco o diamante é)
* o número de inclusões no diamante

…teríamos mais colunas. Nesse caso, a matemática se torna útil. Se você tiver mais de duas colunas, é difícil desenhar pontos no papel. A matemática permite o encaixe perfeito dessa linha ou desse plano aos seus dados.

Além disso, se em vez de apenas um punhado de diamantes, tivéssemos dois mil ou dois milhões, esse trabalho seria muito mais otimizado com um computador.

Hoje, falamos sobre como fazer a regressão linear e fizemos uma previsão usando dados.

Confira outros vídeos da série "Ciência de dados para iniciantes" no Microsoft Azure Machine Learning Studio.

## <a name="next-steps"></a>Próximas etapas
* [Teste um primeiro experimento da ciência de dados com o Machine Learning Studio](create-experiment.md)
* [Obtenha uma introdução ao Machine Learning no Microsoft Azure](/azure/machine-learning/preview/overview-what-is-azure-ml)
