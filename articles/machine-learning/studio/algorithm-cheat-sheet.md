---
title: Página de dicas úteis do algoritmo de Machine Learning
titleSuffix: Azure Machine Learning Studio
description: Uma página de dicas úteis de algoritmo de Machine Learning que pode ser impressa o ajuda a escolher o algoritmo certo para o seu modelo de previsão no Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-ms.author=pakalra, previous-author=pakalra
ms.date: 03/04/2019
ms.openlocfilehash: 51a743e7578ea5bbc2acb9094bbf704a09f3cd6a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60752073"
---
# <a name="machine-learning-algorithm-cheat-sheet-for-azure-machine-learning-studio"></a>Folha de referências de algoritmo de aprendizado de máquina para o Azure Machine Learning Studio

A **Folha de Referências de Algoritmo do Azure Machine Learning Studio** ajuda a escolher o algoritmo certo para o modelo de análise preditiva.

O [Azure Machine Learning Studio](https://studio.azureml.net/) tem uma ampla biblioteca de algoritmos das famílias ***regressão***, ***classificação***, ***clustering*** e ***detecção de anomalias***. Cada um foi projetado para atender a um tipo diferente de problema de aprendizado de máquina.

## <a name="download-machine-learning-algorithm-cheat-sheet"></a>Download: Página de dicas úteis do algoritmo de Machine Learning

**Baixe a página de dicas aqui: [Página de dicas úteis do algoritmo de Machine Learning (11x17 pol.)](https://download.microsoft.com/download/A/6/1/A613E11E-8F9C-424A-B99D-65344785C288/microsoft-machine-learning-algorithm-cheat-sheet-v7.pdf)**

![Página de dicas úteis do algoritmo de Machine Learning: Saiba como escolher um algoritmo do Machine Learning.](./media/algorithm-cheat-sheet/machine-learning-algorithm-cheat-sheet-small_v_0_6-01.png)

Baixe e imprima a folha de referências de algoritmo do Machine Learning Studio em tamanho tabloide para mantê-lo à mão e dispor de ajuda para escolher um algoritmo.

> [!NOTE]
> Para obter ajuda com o uso deste roteiro na escolha do algoritmo correto e ver uma discussão mais aprofundada sobre os diferentes tipos de algoritmos para aprendizado de máquina e como eles são usados, consulte [Como escolher algoritmos para o Microsoft Azure Machine Learning Studio](algorithm-choice.md).

## <a name="notes-and-terminology-definitions-for-the-machine-learning-studio-algorithm-cheat-sheet"></a>Anotações e definições terminológicas para a folha de referências de algoritmo do Machine Learning Studio

* As sugestões oferecidas nessa página de dicas úteis de algoritmo são aproximadas às regras de bolso. Algumas podem ser ajustadas e algumas podem ser flagrantemente violadas. Isso serve para sugerir um ponto de partida. Não tenha medo de comparar vários algoritmos em seus dados. Não é simplesmente não há substituto para entender os princípios de cada algoritmo e o sistema que gerou seus dados.

* Cada algoritmo de aprendizado de máquina tem seu próprio estilo ou *tendência indutiva*. Para um problema específico, vários algoritmos podem ser apropriados e um algoritmo pode ser mais adequado do que outros. Mas nem sempre é possível saber com antecedência qual é a melhor opção. Em casos como esse, vários algoritmos estão listados juntos na folha de consulta. Uma estratégia apropriada seria um algoritmo e se os resultados ainda não estiverem satisfatórios, tentar os outros. Confira um exemplo da [Galeria de IA do Azure](https://gallery.azure.ai/) de um experimento que testa vários algoritmos nos mesmos dados e compare os resultados: [Compare os classificadores multiclasse: Reconhecimento de letra](https://gallery.azure.ai/Details/a635502fc98b402a890efe21cec65b92).

* Há três categorias principais de aprendizado de máquina: **aprendizado supervisionado**, **aprendizado sem supervisão** e **aprendizado de reforço**.

  * Em **aprendizado supervisionado**, cada ponto de dados é rotulado ou associado a uma categoria ou um valor de interesse.  Um exemplo de um rótulo categórico é atribuir uma imagem como, por exemplo, um gato ou um cão.  Um exemplo de um rótulo de valor é o preço de venda associado a um carro usado. O objetivo do aprendizado supervisionado é estudar vários exemplos rotulados como esses e, em seguida, conseguir fazer previsões sobre os pontos de dados futuros. Por exemplo, identificar novas fotos com o animal correto ou atribuir preços de vendas precisos a outros carros usados. Este é um tipo popular e útil de aprendizado de máquina. Todos os módulos do Azure Machine Learning Studio são algoritmos de aprendizado supervisionados, exceto [Cluster K-means][k-means-clustering].

  * No **aprendizado não supervisionado**, os pontos de dados não têm rótulos associados a eles. Em vez disso, a meta de um algoritmo de aprendizado sem supervisão é organizar os dados de alguma forma ou descrever sua estrutura. Isso pode significar agrupá-los em clusters, como faz o K-means, ou encontrar diferentes maneiras de consultar dados complexos para que eles pareçam mais simples.

  * No **aprendizado de reforço**, o algoritmo escolhe uma ação em resposta a cada ponto de dados. É uma abordagem comum em robótica, em que o conjunto de leituras do sensor, em um ponto no tempo, é um ponto de dados e o algoritmo deve escolher a próxima ação do robô. Também é um ajuste natural para aplicativos da Internet das Coisas. O algoritmo de aprendizado também recebe um sinal de recompensa pouco tempo depois, indicando se a decisão foi boa. Com base nisso, o algoritmo modifica sua estratégia para alcançar a recompensa mais alta. Atualmente, não há módulos de algoritmo de reforço de aprendizado no Azure Machine Learning Studio.

* **Métodos bayesianos** fazem a previsão de pontos de dados estatisticamente independentes. Isso significa que a variabilidade não modelada em um ponto de dados não está correlacionada com outros, ou seja, não pode ser prevista. Por exemplo, se os dados que estão sendo registrados são o número de minutos até que o próximo metrô chegue, duas medidas feitas com um dia de diferença são estatisticamente independentes. No entanto, duas medidas feitas com um minuto de diferença não são estatisticamente independentes - o valor de uma é uma alta previsão do valor da outra.

* **Regressão de árvore de decisão aumentada** aproveita a sobreposição de recurso ou interação entre os recursos. Isso significa que, em qualquer ponto de dados específico, o valor de um recurso é, de alguma forma, previsão do valor de outro. Por exemplo, em dados diários de alta/baixa temperatura, saber a baixa temperatura do dia permite que você faça uma estimativa razoável da alta temperatura. As informações contidas nos dois recursos são, de alguma forma, redundantes.

* Classificar dados em mais de duas categorias pode ser feito usando um classificador inerentemente multiclasse ou pela combinação de um conjunto de classificadores de duas classes em um **ensemble**. Na abordagem de conjunto, há um classificador de duas classes separado para cada classe; cada uma separa os dados em duas categorias: "esta classe" e "não é esta classe". Em seguida, os classificadores votam na atribuição correta do ponto de dados. Esse é o princípio operacional de [Uma vs todas as multiclasses][one-vs-all-multiclass].

* Vários métodos, incluindo a regressão logística e o computador de ponto de Bayes, assumem **limites de classe linear**. Ou seja, eles supõem que os limites entre classes sejam aproximadamente linhas retas (ou hiperplanos, no caso mais geral). Geralmente, essa é uma característica dos dados que você não conhece até depois de tentar separá-los, mas é algo que normalmente pode ser aprendido visualizando com antecedência. Se os limites de classe parecerem muito irregulares, fique com as árvores de decisão, selvas de decisão, computadores de vetor de suporte ou redes neurais.

* Redes neurais podem ser usadas com variáveis categóricas criando uma **variável fictícia** para cada categoria e configurando-a como 1 nos casos em que a categoria se aplica e 0 naqueles em que ela não se aplica.

## <a name="next-steps"></a>Próximas etapas

* Para obter um infográfico para download que descreve algoritmos e fornece exemplos, confira [Infográfico para download: conceitos básicos do aprendizado de máquina com exemplos de algoritmo](basics-infographic-with-algorithm-examples.md).

* Para obter uma lista por categoria de todos os algoritmos de aprendizado de máquina disponíveis no Machine Learning Studio, consulte [Inicializar Modelo][initialize-model] na Ajuda de Algoritmo e Módulo do Machine Learning Studio.

* Para obter uma lista completa em ordem alfabética dos algoritmos e módulos no Machine Learning Studio, consulte a [Lista de A-Z de módulos do Machine Learning Studio][a-z-list] na Ajuda de módulo e algoritmo do Machine Learning Studio.



<!-- Module References -->
[a-z-list]: /azure/machine-learning/studio-module-reference/a-z-module-list
[initialize-model]: /azure/machine-learning/studio-module-reference/machine-learning-initialize-model
[k-means-clustering]: /azure/machine-learning/studio-module-reference/k-means-clustering
[one-vs-all-multiclass]: /azure/machine-learning/studio-module-reference/one-vs-all-multiclass
