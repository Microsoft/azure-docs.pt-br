---
title: "Seus dados estão prontos para a ciência de dados? Avaliação de dados – Azure Machine Learning | Microsoft Docs"
description: "Saiba sobre os quatro critérios para os dados estarem prontos para a ciência de dados. O vídeo Ciência de Dados para Iniciantes 2 tem exemplos concretos para ajudar na avaliação dos dados básicos."
keywords: "dados relevantes, avaliar dados, preparar dados, critérios dos dados, dados prontos"
services: machine-learning
documentationcenter: na
author: cjgronlund
manager: jhubbard
editor: cjgronlund
ms.assetid: d502062c-da70-4b21-9054-0bfd9902612e
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/13/2017
ms.author: cgronlun
ms.openlocfilehash: 9b5cf776981af0dff57195d5c7f1923b8d9a3862
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="is-your-data-ready-for-data-science"></a>Seus dados estão prontos para a ciência de dados?
## <a name="video-2-data-science-for-beginners-series"></a>Vídeo 2: Série de ciência de dados para iniciantes
Aprenda como avaliar seus dados para assegurar que eles atendem aos critérios básicos de preparo para a ciência de dados.

Para aproveitar ao máximo da série, assista a todos os vídeos. [Acessar a lista de vídeos](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Shows/SupervisionNotRequired/9/player]
>
>

## <a name="other-videos-in-this-series"></a>Outros vídeos nesta série
*Ciência de dados para iniciantes* é uma breve introdução à ciência de dados em cinco vídeos curtos.

* Vídeo 1: [As cinco perguntas que a ciência de dados responde](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 min e 14 s)*
* Vídeo 2: Seus dados estão prontos para a ciência de dados?
* Video 3: [Faça uma pergunta que você possa responder com dados](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 min 17 s)*
* Vídeo 4: [Preveja uma resposta com um modelo simples](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 min 42 s)*
* Vídeo 5: [Copie o trabalho de outras pessoas para fazer a ciência de dados](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 s)*

## <a name="transcript-is-your-data-ready-for-data-science"></a>Transcrição: Seus dados estão prontos para a ciência de dados?
Bem-vindo a "Seus dados estão prontos para a ciência de dados?" o segundo vídeo na série *Ciência de dados para iniciantes*.  

Antes da ciência de dados poder fornecer as respostas desejadas, você precisa dar-lhe algum material bruto de alta qualidade com o qual trabalhar. Assim como fazer uma pizza, quanto melhores os ingredientes com os quais você inicia, melhor o produto final. 

## <a name="criteria-for-data"></a>Critérios dos dados
Assim, no caso da ciência de dados, existem alguns componentes que precisamos para reunir.

Precisamos de dados que sejam:

* Relevantes
* Conectado
* Precisos
* Suficientes para trabalhar

## <a name="is-your-data-relevant"></a>Seus dados são relevantes?
Então, o primeiro princípio - precisamos de dados relevantes.

![Dados relevantes versus dados irrelevantes - avaliar dados](./media/data-science-for-beginners-is-your-data-ready-for-data-science/relevant-and-irrelevant-data.png)

Veja a tabela à esquerda. Encontramos sete pessoas fora dos bares de Boston, medimos seu nível de álcool no sangue, a média de rebatidas do Red Sox no último jogo e o preço do leite na loja de conveniência mais próxima.

São todos dados perfeitamente legítimos. O único problema é que não são relevantes. Não há nenhuma relação óbvia entre esses números. Se eu fornecesse o preço atual do leite e a média de rebatidas do Red Sox, não seria possível estimar o teor de álcool no meu sangue.

Agora, veja a tabela à direita. Desta vez, medimos a massa corporal de cada pessoa e contamos o número de bebidas que beberam.  Os números em cada linha agora são relevantes entre si. Se eu fornecesse a massa corporal e o número de margaritas bebidas, você poderia fazer uma estimativa do teor de álcool em meu sangue.

## <a name="do-you-have-connected-data"></a>Você tem dados conectados?
O próximo ingrediente serão os dados conectados.

![Dados conectados versus dados desconectados – critérios dos dados, dados prontos](./media/data-science-for-beginners-is-your-data-ready-for-data-science/connected-vs-disconnected-data.png)

Aqui estão alguns dados relevantes sobre a qualidade dos hambúrgueres: temperatura da churrasqueira, peso do hambúrguer e a classificação na revista de alimentos local. Mas observe as lacunas na tabela à esquerda.

A maioria dos conjuntos de dados não tem alguns valores. É comum ter buracos como esses e há maneiras de solucioná-los. Mas se houver muitas falhas, seus dados começarão a parecer um queijo suíço.

Se você vir a tabela à esquerda, há muitos dados ausentes e é difícil propor algum tipo de relação entre o peso do hambúrguer e a temperatura da churrasqueira. Este é um exemplo de dados desconectados.

A tabela à direita, no entanto, está completa - um exemplo de dados conectados.

## <a name="is-your-data-accurate"></a>Seus dados são precisos?
O próximo ingrediente que precisamos é a precisão. Aqui estão os quatro destinos que gostaríamos de atingir com setas.

![Dados precisos versus dados imprecisos - critérios dos dados](./media/data-science-for-beginners-is-your-data-ready-for-data-science/inaccurate-vs-accurate-data.png)

Veja o destino à direita superior. Temos um agrupamento rígido em torno do alvo. Claro, isso é preciso. Estranhamente, na linguagem da ciência de dados, nosso desempenho no destino logo abaixo também é considerado preciso.

Se você fosse mapear centro dessas setas, veria que está muito próximo do alvo. As setas estão distribuídas em torno do destino, portanto, são consideradas imprecisas, mas estão centralizadas em torno do alvo, então, são consideradas precisas.

Agora, veja o destino à esquerda superior. Aqui, nossas setas estão muito próximas umas das outras, um agrupamento rígido. Elas são exatas, mas são imprecisas porque o centro está longe do alvo. E, naturalmente, as setas no destino à esquerda inferior são imprecisas e inexatas. O arqueiro precisa de mais prática.

## <a name="do-you-have-enough-data-to-work-with"></a>Você tem dados suficientes com os quais trabalhar?
Por fim, o ingrediente 4 - precisamos ter dados suficientes.

![Você tem dados suficientes para a análise? Avaliação dos dados](./media/data-science-for-beginners-is-your-data-ready-for-data-science/barely-enough-data.png)

Pense em cada ponto de dados na tabela como sendo uma pincelada em uma pintura. Se você tiver apenas algumas, a pintura poderá ser bastante confusa – será difícil dizer o que é.

Se você adicionar mais algumas pinceladas, então, a pintura começará a ficar um pouco mais nítida.

Quando você tem pinceladas insuficientes, pode ver apenas o bastante para tomar algumas decisões amplas. É algum lugar que eu desejaria visitar? É claro, parece água limpa — sim, é para onde irei em férias.

Conforme você adiciona mais dados, a imagem fica mais clara e você pode tomar decisões mais detalhadas. Agora, posso ver três hotéis na orla à esquerda. Você sabe, realmente gosto dos recursos de arquitetura de um no primeiro plano. Ficarei lá, no terceiro andar.

Com os dados que são relevantes, conectados, precisos e suficientes, temos todos os ingredientes necessários para fazer uma ciência de dados com alta qualidade.

Certifique-se de conferir outros vídeos da série *Ciência de dados para iniciantes* no Microsoft Azure Machine Learning.

## <a name="next-steps"></a>Próximas etapas
* [Teste um primeiro experimento da ciência de dados com o Machine Learning Studio](create-experiment.md)
* [Obtenha uma introdução ao Machine Learning no Microsoft Azure](what-is-machine-learning.md)
