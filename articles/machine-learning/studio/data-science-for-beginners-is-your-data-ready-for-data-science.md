---
title: Avaliação dos dados
titleSuffix: Azure Machine Learning Studio
description: Quatro critérios que seus dados precisam atender para estarem prontos para ciência de dados. Este vídeo tem exemplos concretos para ajudar na avaliação dos dados básicos.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.custom: seodec18
ms.date: 03/22/2019
ms.openlocfilehash: 1d9475b49b3ef8a2e672e1ce505a7d94d19cb14f
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875316"
---
# <a name="is-your-data-ready-for-data-science"></a>Seus dados estão prontos para a ciência de dados?
## <a name="video-2-data-science-for-beginners-series"></a>Vídeo 2: Ciência de Dados para série de Iniciantes
Aprenda como avaliar seus dados para assegurar que eles atendem aos critérios básicos de preparo para a ciência de dados.

Para aproveitar ao máximo da série, assista a todos os vídeos. [Acesse a lista de vídeos](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Shows/SupervisionNotRequired/9/player]
>
>

## <a name="other-videos-in-this-series"></a>Outros vídeos nesta série
*Ciência de dados para iniciantes* é uma breve introdução à ciência de dados em cinco vídeos curtos.

* Vídeo 1: [As cinco perguntas que a ciência de dados responde](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5min 14s)*
* Vídeo 2: Seus dados estão prontos para a ciência de dados?
* Vídeo 3: [Faça uma pergunta que você poderá responder usando dados](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 min 17 s)*
* Vídeo 4: [Preveja uma resposta com um modelo simples](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 min 42 s)*
* Vídeo 5: [Copie o trabalho de outras pessoas para fazer a ciência de dados](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 s)*

## <a name="transcript-is-your-data-ready-for-data-science"></a>Transcrição: Seus dados estão prontos para a ciência de dados?
Bem-vindo a "Seus dados estão prontos para a ciência de dados?" o segundo vídeo na série *Ciência de dados para iniciantes*.  

Antes da ciência de dados poder fornecer as respostas desejadas, você precisa dar-lhe algum material bruto de alta qualidade com o qual trabalhar. Assim como fazer uma pizza, quanto melhores os ingredientes com os quais você inicia, melhor o produto final. 

## <a name="criteria-for-data"></a>Critérios dos dados
Na ciência de dados, há certos ingredientes que devem ser efetuados pull em conjunto, incluindo:

* Relevantes
* Conectado
* Precisos
* Suficientes para trabalhar

## <a name="is-your-data-relevant"></a>Seus dados são relevantes?
Então, o primeiro ingrediente - são necessários dados relevantes.

![Dados relevantes versus dados irrelevantes - avaliar dados](./media/data-science-for-beginners-is-your-data-ready-for-data-science/relevant-and-irrelevant-data.png)

À esquerda, a tabela apresenta o nível de álcool no sangue de sete pessoas testadas fora de um bar em Boston, a média de rebatidas do Red Sox no último jogo e o preço do leite na loja de conveniência mais próxima.

São todos dados perfeitamente legítimos. O único problema é que não são relevantes. Não há nenhuma relação óbvia entre esses números. Se alguém fornecer o preço atual do leite e a média de rebatidas do Red Sox, não há como estimar o teor de álcool no sangue.

Agora, veja a tabela à direita. Desta vez, a massa corporal de cada pessoa foi medida, assim como a quantidade de bebidas consumida.  Os números em cada linha agora são relevantes entre si. Se eu fornecesse a massa corporal e o número de margaritas bebidas, você poderia fazer uma estimativa do teor de álcool em meu sangue.

## <a name="do-you-have-connected-data"></a>Você tem dados conectados?
O próximo ingrediente serão os dados conectados.

![Dados conectados versus dados desconectados – critérios dos dados, dados prontos](./media/data-science-for-beginners-is-your-data-ready-for-data-science/connected-vs-disconnected-data.png)

Aqui estão alguns dados relevantes sobre a qualidade dos hambúrgueres: temperatura da churrasqueira, peso do hambúrguer e a classificação na revista de alimentos local. Mas observe as lacunas na tabela à esquerda.

A maioria dos conjuntos de dados não tem alguns valores. É comum ter buracos como esses e há maneiras de solucioná-los. Mas se houver muitas falhas, seus dados começarão a parecer um queijo suíço.

Se você vir a tabela à esquerda, há muitos dados ausentes e é difícil propor algum tipo de relação entre o peso do hambúrguer e a temperatura da churrasqueira. Este exemplo mostra dados desconectados.

A tabela à direita, no entanto, está completa - um exemplo de dados conectados.

## <a name="is-your-data-accurate"></a>Seus dados são precisos?
O próximo ingrediente é a precisão. Aqui estão quatro destinos a ocorrer.

![Dados precisos versus dados imprecisos - critérios dos dados](./media/data-science-for-beginners-is-your-data-ready-for-data-science/inaccurate-vs-accurate-data.png)

Veja o destino à direita superior. Há um agrupamento rígido em torno do alvo. Claro, isso é preciso. Estranhamente, na linguagem da ciência de dados, o desempenho no destino logo abaixo também é considerado preciso.

Se você mapeou o centro dessas setas, observou que está muito próximo do alvo. As setas estão distribuídas em torno do destino, portanto, são consideradas imprecisas, mas estão centralizadas em torno do alvo, então, são consideradas precisas.

Agora, veja o destino à esquerda superior. Aqui, as setas estão muito próximas umas das outras, um agrupamento rígido. Elas são exatas, mas são imprecisas porque o centro está longe do alvo. As setas no destino à esquerda inferior são imprecisas e inexatas. O arqueiro precisa de mais prática.

## <a name="do-you-have-enough-data-to-work-with"></a>Você tem dados suficientes com os quais trabalhar?
Finalmente, o ingrediente 4 é um dado suficiente.

![Você tem dados suficientes para a análise? Avaliação dos dados](./media/data-science-for-beginners-is-your-data-ready-for-data-science/barely-enough-data.png)

Pense em cada ponto de dados na tabela como sendo uma pincelada em uma pintura. Se você tiver apenas algumas delas, a pintura poderá ficar confusa - será difícil dizer o que é.

Se você adicionar mais algumas pinceladas, então, a pintura começará a ficar um pouco mais nítida.

Quando você tem apenas pinceladas suficientes, somente verá o suficiente para tomar algumas decisões mais amplas. É algum lugar que eu desejaria visitar? É claro, parece água limpa — sim, é para onde irei em férias.

Conforme você adiciona mais dados, a imagem fica mais clara e você pode tomar decisões mais detalhadas. Agora, é possível ver três hotéis na orla à esquerda. Você pode notar os recursos arquitetônicos do primeiro plano. É possível até escolher permanecer no terceiro andar devido ao modo de exibição.

Com dados relevantes, conectados, precisos e suficientes, você tem todos os ingredientes necessários para fazer uma ciência de dados de alta qualidade.

Certifique-se de assistir a outros vídeos da série *Ciência de dados para iniciantes* no Microsoft Azure Machine Learning Studio.

## <a name="next-steps"></a>Próximas etapas
* [Teste um primeiro experimento da ciência de dados com o Machine Learning Studio](create-experiment.md)
* [Obtenha uma introdução ao Machine Learning no Microsoft Azure](/azure/machine-learning/preview/overview-what-is-azure-ml)
