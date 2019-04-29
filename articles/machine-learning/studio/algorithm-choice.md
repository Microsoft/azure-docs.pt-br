---
title: Como escolher algoritmos
titleSuffix: Azure Machine Learning Studio
description: Como escolher algoritmos de Azure Machine Learning Studio para aprendizado supervisionado e não supervisionado nos experimentos de agrupamento, classificação ou regressão.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-ms.author=pakalra, previous-author=pakalra
ms.date: 03/04/2019
ms.openlocfilehash: 3bb88f2f9546ec25433061a0704bd144730bd34c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60752801"
---
# <a name="how-to-choose-algorithms-for-azure-machine-learning-studio"></a>Como escolher algoritmos do Azure Machine Learning Studio

A resposta à pergunta "Qual algoritmo de aprendizado de máquina devo usar?" sempre será "Depende". Depende do tamanho, da qualidade e da natureza dos dados. Depende do que você deseja fazer com a resposta. Depende de como o cálculo do algoritmo foi traduzido em instruções para o computador que você está usando. E depende de quanto tempo você tem. Até mesmo os cientistas de dados mais experientes não podem determinar qual algoritmo terá o melhor desempenho antes de experimentá-lo.

O Machine Learning Studio fornece algoritmos de última geração, como Árvores de Decisão Aumentadas Escalonáveis, sistemas de Recomendação Bayesiana, Redes Neurais Profundas e Selvas de Decisão desenvolvidos na Microsoft Research. Pacotes de aprendizado de máquina escalonáveis de software livre, como Vowpal Wabbit, também estão incluídos. O Machine Learning Studio dá suporte a algoritmos de aprendizado de máquina para classificação binária e de múltiplas classes, de regressão e de clustering. Confira a lista completa de [Módulos do Machine Learning](/azure/machine-learning/studio-module-reference/index).
A documentação fornece algumas informações sobre cada algoritmo e sobre como ajustar os parâmetros para otimizar o algoritmo para seu uso.  


## <a name="the-machine-learning-algorithm-cheat-sheet"></a>Página de dicas úteis do algoritmo de Machine Learning

O **[Microsoft Azure Machine Learning Studio algoritmo Cheat Sheet](algorithm-cheat-sheet.md)** ajuda você a escolher o direito de algoritmo de machine learning para suas soluções de análise preditiva do Azure Machine Learning Biblioteca do Studio de algoritmos.
Este artigo explica como usar essa folha de consulta.

> [!NOTE]
> Para baixar a página de dicas úteis e acompanhar este artigo, vá para [Página de dicas úteis de algoritmo de aprendizado de máquina para o Microsoft Azure Machine Learning Studio](algorithm-cheat-sheet.md).
> 
> 

Essa página de dicas úteis tem uma audiência bem específica em mente: um cientista de dados iniciante com aprendizado de máquina de nível de graduação, tentando escolher um algoritmo para começar a usar o Estúdio de Aprendizado de Máquina do Azure. Isso significa que ela faz algumas generalizações e simplificações excessivas, mas aponta uma direção segura para você. Isso também significa que há muitos algoritmos não listados aqui.

Essas recomendações são comentários e dicas compilados de vários cientistas de dados e especialistas em aprendizado de máquina. Não concordamos em tudo, mas estamos tentei harmonizar nossas opiniões em um consenso aproximado. A maioria das instruções em que houve desentendimentos começa com "Depende..."

### <a name="how-to-use-the-cheat-sheet"></a>Como usar a página de dicas úteis

Leia os rótulos do caminho e do algoritmo no gráfico, como “Para *&lt;rótulo do caminho&gt;*, use *&lt;algoritmo&gt;*”. Por exemplo, “Para *velocidade*, use *regressão logística de duas classes*”. Às vezes, mais de uma ramificação se aplica.
Às vezes, nenhuma delas é a solução perfeita. Elas se destinam a serem recomendações gerais, portanto não se preocupe com a exatidão delas.
Vários cientistas de dados, falamos com disse que a única maneira para encontrar o melhor algoritmo é experimentar todos eles.

Veja um exemplo da [Galeria de IA do Azure](https://gallery.azure.ai/) de um experimento que testa vários algoritmos nos mesmos dados e compare os resultados: [Compare os classificadores multiclasse: Reconhecimento de letra](https://gallery.azure.ai/Details/a635502fc98b402a890efe21cec65b92).

> [!TIP]
> Para baixar uma visão geral infográfica e fácil de entender dos conceitos básicos do aprendizado de máquina para saber mais sobre os algoritmos populares usados para responder a perguntas comuns desse aprendizado, confira [Conceitos básicos do aprendizado de máquina com exemplos de algoritmo](basics-infographic-with-algorithm-examples.md).

## <a name="flavors-of-machine-learning"></a>Tipos de aprendizado de máquina

### <a name="supervised"></a>Supervisionado

Os algoritmos de aprendizado supervisionados fazem previsões com base em um conjunto de exemplos. Por exemplo, as cotações históricas podem ser usadas para fazer suposições sobre preços futuros. Cada exemplo usado para treinamento é rotulado com o valor de seu interesse — neste caso, o preço da ação. Um algoritmo de aprendizado supervisionado procura por padrões nesses rótulos de valor. Ele pode usar qualquer informação que possa ser relevante – o dia da semana, a temporada, os dados financeiros da empresa, o tipo de setor, a presença de eventos geopolíticos perturbadores – e cada algoritmo procura tipos diferentes de padrões. Depois que o algoritmo tiver encontrado o melhor padrão possível, usará esse padrão para fazer previsões para dados de testes sem rótulos — os preços de amanhã.

O aprendizado supervisionado é um tipo popular e útil de aprendizado de máquina. Com uma exceção, todos os módulos do Azure Machine Learning Studio são algoritmos de aprendizado supervisionados. Há vários tipos específicos de aprendizado supervisionado representados no Azure Machine Learning Studio: classificação, regressão e detecção de anomalias.

* **Classificação**. Quando os dados estiverem sendo usados para prever uma categoria, o aprendizado supervisionado também será chamado de classificação. Esse é o caso ao atribuir uma imagem como uma foto de um ‘gato’ ou de um ‘cachorro’. Quando há apenas duas opções, isso é chamado de **classificação binomial** ou de **duas classes**. Quando houver mais categorias, como na previsão do vencedor do torneio NCAA March Madness, esse problema é conhecido como **classificação multiclasse**.
* **Regressão**. Quando um valor estiver sendo previsto, assim como acontece com preços de cotações, o aprendizado supervisionado será chamado de regressão.
* **Detecção de anomalias**. Às vezes, o objetivo é identificar os pontos de dados que são simplesmente incomuns. Na detecção de fraudes, por exemplo, quaisquer padrões incomuns de gasto em cartão de crédito são suspeitos. As variações possíveis são tão numerosas e os exemplos de treinamento tão poucos, que não é viável aprender como seria uma atividade fraudulenta. A abordagem que usa detecção de anomalias é simplesmente saber quais atividade normal é semelhante (usando um histórico de transações não fraudulentas históricas) e identificar tudo que é significativamente diferente.

### <a name="unsupervised"></a>Não supervisionado

No aprendizado não supervisionado, os pontos de dados não têm rótulos associados a eles. Em vez disso, a meta de um algoritmo de aprendizado sem supervisão é organizar os dados de alguma forma ou descrever sua estrutura. Isso pode significar agrupá-los em clusters ou encontrar diferentes maneiras de consultar dados complexos para que eles pareçam mais simples ou mais organizados.

### <a name="reinforcement-learning"></a>Aprendizado de reforço

No aprendizado de reforço, o algoritmo escolhe uma ação em resposta a cada ponto de dados. O algoritmo de aprendizado também recebe um sinal de recompensa pouco tempo depois, indicando se a decisão foi boa.
Com base nisso, o algoritmo modifica sua estratégia para alcançar a recompensa mais alta. Atualmente, não há módulos de algoritmo de reforço de aprendizado no Azure Machine Learning Studio. O aprendizado de reforço é comum em robótica, em que o conjunto de leituras do sensor, em um ponto no tempo, é um ponto de dados e o algoritmo deve escolher a próxima ação do robô. Também é um ajuste natural para aplicativos da Internet das Coisas.

## <a name="considerations-when-choosing-an-algorithm"></a>Considerações ao escolher um algoritmo

### <a name="accuracy"></a>Precisão

Obter a resposta mais precisa possível nem sempre será necessário.
Às vezes uma aproximação será adequada, dependendo do uso que você quiser dar a ela. Se esse for o caso, talvez seja possível reduzir o tempo de processamento drasticamente usando métodos mais aproximados. Outra vantagem dos métodos mais aproximados é que eles naturalmente tendem a evitar o superajuste.

### <a name="training-time"></a>Tempo de treinamento

O número de minutos ou de horas necessários para treinar um modelo varia muito entre algoritmos. Em geral, o tempo de treinamento está intimamente vinculado à precisão — um normalmente acompanha o outro. Além disso, alguns algoritmos são mais sensíveis ao número de pontos de dados do que outros.
Quando o tempo for limitado, ele poderá orientar a escolha do algoritmo, especialmente quando o conjunto de dados for grande.

### <a name="linearity"></a>Linearidade

Muitos algoritmos de aprendizado de máquina usam a linearidade. Os algoritmos de classificação linear supõem que as classes podem ser separadas por uma linha reta (ou seu análogo em dimensões maiores). Isso inclui a regressão logística e as máquinas de vetor de suporte (como implementado no Azure Machine Learning Studio).
Os algoritmos de regressão linear supõem que as tendências de dados seguem uma linha reta. Essas suposições não são ruins para alguns problemas, mas em outros elas podem reduzir a precisão.

![Limite de classe não linear](./media/algorithm-choice/image1.png)

***Limite de classe não linear*** *- contar com um algoritmo de classificação linear resultaria em baixa precisão*

![Dados com uma tendência não linear](./media/algorithm-choice/image2.png)

***Dados com uma tendência não linear*** *- usar um método de regressão linear geraria erros muito maiores do que o necessário*

Apesar de seus riscos, os algoritmos lineares são muito populares como uma primeira linha de ataque. Eles tendem a ser algoritmicamente simples e rápidos de treinar.

### <a name="number-of-parameters"></a>Número de parâmetros

Os parâmetros são os botões que o cientista de dados precisa girar ao configurar um algoritmo. Eles são números que afetam o comportamento do algoritmo, como tolerância a erros ou o número de iterações, ou opções entre variantes de como o algoritmo se comporta. O tempo de treinamento e a precisão do algoritmo às vezes podem ser muito importantes para obter apenas as configurações corretas. Normalmente, os algoritmos com um grande número de parâmetros exigem mais tentativa e erro para encontrar uma boa combinação.

Como alternativa, há um bloco de módulo de [limpeza de parâmetro](algorithm-parameters-optimize.md) no Azure Machine Learning Studio que experimenta automaticamente todas as combinações de parâmetro em qualquer granularidade que você escolher. Embora essa seja uma excelente maneira de certificar-se de que você tenha estendido o espaço de parâmetro, o tempo necessário para treinar o modelo aumenta exponencialmente com o número de parâmetros.

A vantagem é que ter muitos parâmetros geralmente indica que um algoritmo tem mais flexibilidade. Geralmente, ela pode atingir uma precisão muito boa, desde que você pode encontrar a combinação certa de configurações de parâmetro.

### <a name="number-of-features"></a>Número de recursos

Para determinados tipos de dados, o número de recursos pode ser muito grande em comparação ao número de pontos de dados. Geralmente, isso acontece com dados de genética ou de texto. O grande número de recursos pode reduzir alguns algoritmos de aprendizado, fazendo com que o tempo de treinamento seja impraticável. As Máquinas de Vetor de Suporte são particularmente adequadas para esse caso (veja abaixo).

### <a name="special-cases"></a>Casos especiais

Alguns algoritmos de aprendizado fazem suposições específicas sobre a estrutura de dados ou os resultados desejados. Se você conseguir encontrar um que atenda às suas necessidades, ele oferecerá resultados mais úteis, previsões mais exatas ou tempos de treinamento menores.

| **Algoritmo** | **Precisão** | **Tempo de treinamento** | **Linearidade** | **Parâmetros** | **Observações** |
| --- |:---:|:---:|:---:|:---:| --- |
| **Classificação de duas classes** | | | | | |
| [regressão logística](/azure/machine-learning/studio-module-reference/two-class-logistic-regression) | |● |● |5 | |
| [floresta de decisão](/azure/machine-learning/studio-module-reference/two-class-decision-forest) |● |○ | |6 | |
| [selva de decisão](/azure/machine-learning/studio-module-reference/two-class-decision-jungle) |● |○ | |6 |Volume de memória insuficiente |
| [árvore de decisão aumentada](/azure/machine-learning/studio-module-reference/two-class-boosted-decision-tree) |● |○ | |6 |Grande volume de memória |
| [rede neural](/azure/machine-learning/studio-module-reference/two-class-neural-network) |● | | |9 |[A personalização adicional é possível](azure-ml-netsharp-reference-guide.md) |
| [perceptron médio](/azure/machine-learning/studio-module-reference/two-class-averaged-perceptron) |○ |○ |● |4 | |
| [máquina de vetor de suporte](/azure/machine-learning/studio-module-reference/two-class-support-vector-machine) | |○ |● |5 |Bom para conjuntos de recursos grandes |
| [máquina de vetor de suporte localmente profunda](/azure/machine-learning/studio-module-reference/two-class-locally-deep-support-vector-machine) |○ | | |8 |Bom para conjuntos de recursos grandes |
| [computador do ponto de Bayes](/azure/machine-learning/studio-module-reference/two-class-bayes-point-machine) | |○ |● |3 | |
| **Classificação multiclasse** | | | | | |
| [regressão logística](/azure/machine-learning/studio-module-reference/multiclass-logistic-regression) | |● |● |5 | |
| [floresta de decisão](/azure/machine-learning/studio-module-reference/multiclass-decision-forest) |● |○ | |6 | |
| [selva de decisão](/azure/machine-learning/studio-module-reference/multiclass-decision-jungle) |● |○ | |6 |Volume de memória insuficiente |
| [rede neural](/azure/machine-learning/studio-module-reference/multiclass-neural-network) |● | | |9 |[A personalização adicional é possível](azure-ml-netsharp-reference-guide.md) |
| [one-v-all](/azure/machine-learning/studio-module-reference/one-vs-all-multiclass) |- |- |- |- |Consulte as propriedades do método de duas classes selecionado |
| **Regressão** | | | | | |
| [linear](/azure/machine-learning/studio-module-reference/linear-regression) | |● |● |4 | |
| [Linear Bayesiana](/azure/machine-learning/studio-module-reference/bayesian-linear-regression) | |○ |● |2 | |
| [floresta de decisão](/azure/machine-learning/studio-module-reference/decision-forest-regression) |● |○ | |6 | |
| [árvore de decisão aumentada](/azure/machine-learning/studio-module-reference/boosted-decision-tree-regression) |● |○ | |5 |Grande volume de memória |
| [Quantil de floresta rápida](/azure/machine-learning/studio-module-reference/fast-forest-quantile-regression) |● |○ | |9 |Distribuições em vez de previsões de ponto |
| [rede neural](/azure/machine-learning/studio-module-reference/neural-network-regression) |● | | |9 |[A personalização adicional é possível](azure-ml-netsharp-reference-guide.md) |
| [Poisson](/azure/machine-learning/studio-module-reference/poisson-regression) | | |● |5 |Tecnicamente linear de log. Para prever contagens |
| [ordinal](/azure/machine-learning/studio-module-reference/ordinal-regression) | | | |0 |Para prever a ordem de classificação |
| **Detecção de anomalias** | | | | | |
| [máquina de vetor de suporte](/azure/machine-learning/studio-module-reference/one-class-support-vector-machine) |○ |○ | |2 |Especialmente bom para conjuntos de recursos grandes |
| [Detecção de anomalias baseada em PCA](/azure/machine-learning/studio-module-reference/pca-based-anomaly-detection) | |○ |● |3 | |
| [K-means](/azure/machine-learning/studio-module-reference/k-means-clustering) | |○ |● |4 |Um algoritmo de clustering |

**Propriedades do algoritmo:**

**●** - mostra excelente precisão, tempos de treinamento pequenos e o uso de linearidade

**○** - mostra boa precisão e tempo de treinamento moderado

## <a name="algorithm-notes"></a>Anotações sobre algoritmo

### <a name="linear-regression"></a>Regressão linear

Como mencionado anteriormente, a [regressão linear](/azure/machine-learning/studio-module-reference/linear-regression) ajusta uma linha (ou plano ou hiperplano) ao conjunto de dados. Ela é uma força de trabalho, simples e rápida, mas pode ser muito simplista para alguns problemas.

![Dados com uma tendência linear](./media/algorithm-choice/image3.png)

***Dados com uma tendência linear***

### <a name="logistic-regression"></a>Regressão logística

Embora ele inclua 'regressão' no nome, a regressão logística é, na verdade, uma ferramenta poderosa para [duas classes](/azure/machine-learning/studio-module-reference/two-class-logistic-regression) e [multiclasse](/azure/machine-learning/studio-module-reference/multiclass-logistic-regression) classificação. É rápida e simples. O fato de que ela usa uma curva em forma de ‘S’ em vez de uma linha reta faz com que ela seja uma opção natural para a divisão de dados em grupos. A regressão logística oferece limites de classe lineares e, portanto, quando for usá-la, verifique se uma aproximação linear serve para você.

![Regressão logística para dados de duas classes com apenas um recurso](./media/algorithm-choice/image4.png)

***Uma regressão logística para dados de duas classes com apenas um recurso*** *- o limite de classe é o ponto no qual a curva logística é mais próxima de ambas as classes*

### <a name="trees-forests-and-jungles"></a>Árvores, florestas e selvas

Florestas de decisão ([regressão](/azure/machine-learning/studio-module-reference/decision-forest-regression), [duas classes](/azure/machine-learning/studio-module-reference/two-class-decision-forest) e [multiclasse](/azure/machine-learning/studio-module-reference/multiclass-decision-forest)), selvas de decisão ([duas classes](/azure/machine-learning/studio-module-reference/two-class-decision-jungle) e [multiclasse](/azure/machine-learning/studio-module-reference/multiclass-decision-jungle)) e árvores de decisão aumentadas ([regressão](/azure/machine-learning/studio-module-reference/boosted-decision-tree-regression) e [duas classes](/azure/machine-learning/studio-module-reference/two-class-boosted-decision-tree)) são todos baseados em árvores de decisão, um conceito fundamental de aprendizado de máquina. Há muitas variantes de árvores de decisão, mas todas fazem a mesma coisa — subdividir o espaço de recursos em regiões com basicamente o mesmo rótulo. Elas podem ser regiões de categoria consistente ou de valor constante, dependendo se você estiver fazendo classificação ou regressão.

![A árvore de decisão subdivide um espaço de recurso](./media/algorithm-choice/image5.png)

***Uma árvore de decisão subdivide um espaço de recursos em regiões de valores aproximadamente uniformes***

Como um espaço de recurso pode ser subdividido em pequenas regiões de forma arbitrária, fica fácil imaginar a divisão minuciosa o suficiente para ter um ponto de dados por região. Esse é um exemplo extremo de superajuste. Para evitar isso, um grande conjunto de árvores são construídos com cuidado matemático especial para garantir que as árvores não estejam correlacionadas. A média dessa "floresta de decisão" é uma árvore que evita o superajuste. As florestas de decisão podem usar muita memória. As selvas de decisão são uma variante que consome menos memória às custas de um tempo de treinamento um pouco mais longo.

As árvores de decisão aumentadas evitam o superajuste ao limitarem o número de vezes que podem se subdividir e como poucos pontos de dados são permitidos em cada região. O algoritmo constrói uma sequência de árvores, e cada uma delas aprende a compensar o erro deixado pela árvore anterior. O resultado é um aprendiz muito preciso que tende a usar muita memória. Para obter a descrição técnica completa, confira o [documento original de Friedman](https://www-stat.stanford.edu/~jhf/ftp/trebst.pdf).

[Regressão rápida de quantil de floresta](/azure/machine-learning/studio-module-reference/fast-forest-quantile-regression) é uma variação de árvores de decisão para casos especiais onde você deseja conhecer não apenas o valor típico (médio) dos dados em uma região, mas também sua distribuição na forma de quantis.

### <a name="neural-networks-and-perceptrons"></a>Redes neurais e perceptrons

As redes neurais são algoritmos de aprendizado inspirados no cérebro que cobrem problemas [multiclasse](/azure/machine-learning/studio-module-reference/multiclass-neural-network), de [duas classes](/azure/machine-learning/studio-module-reference/two-class-neural-network) e de [regressão](/azure/machine-learning/studio-module-reference/neural-network-regression). Elas vêm em uma variedade infinita, mas as redes neurais do Azure Machine Learning Studio estão todas na forma de grafos direcionados acíclicos. Isso significa que os recursos de entrada são passados para a frente (nunca para trás) por meio de uma sequência de camadas transformadas em saídas. Em cada camada, as entradas são ponderadas em várias combinações, somadas e passadas para a próxima camada. Essa combinação de cálculos simples resulta na capacidade de aprender limites de classe e tendências de dados sofisticados, aparentemente mágicos. As redes de várias camadas desse tipo executam o “aprendizado profundo” que alimenta tantos relatórios técnicos e a ficção científica.

No entanto, esse alto desempenho tem um preço. As redes neurais podem ter um treinamento muito longo, particularmente para grandes conjuntos de dados com muitos recursos. Elas também têm mais parâmetros do que a maioria dos algoritmos, o que significa que a limpeza de parâmetros aumenta muito o tempo de treinamento.
E para as pessoas brilhantes que desejam [especificar sua própria estrutura de rede](azure-ml-netsharp-reference-guide.md), as possibilidades são inesgotáveis.

![Limites aprendidos por redes neurais](./media/algorithm-choice/image6.png)

***Os limites aprendidos por redes neurais podem ser complexos e irregulares***

O [perceptron médio de duas classes](/azure/machine-learning/studio-module-reference/two-class-averaged-perceptron) é a resposta das redes neurais para os tempos de treinamento estratosféricos. Ele usa uma estrutura de rede que fornece os limites de classe linear. É quase primitivo pelos padrões de hoje, mas tem uma longa história de trabalho robusto e é pequeno o suficiente para aprender rapidamente.

### <a name="svms"></a>SVMs

As máquinas de vetor de suporte (SVMs) encontram o limite que separa as classes pela maior margem possível. Quando as duas classes não puderem ser claramente separadas, os algoritmos encontrarão o melhor limite possível. Como escrito no Azure Machine Learning Studio, o [SVM de duas classes](/azure/machine-learning/studio-module-reference/two-class-support-vector-machine) faz isso com apenas uma linha reta (no jargão da SVM, ele usa um kernel linear).
Como ela faz essa aproximação linear, é capaz de ser executada de maneira consideravelmente rápida. Em que ele realmente se destaca é com os dados de uso intenso de recursos, como texto ou dados de genoma. Nesses casos, as SVMs são capazes de separar classes mais rapidamente e com menos superajuste do que a maioria dos outros algoritmos, além de exigir apenas uma pequena quantidade de memória.

![Limite de classe de computador de vetor de suporte](./media/algorithm-choice/image7.png)

***Um limite de classe de computador de vetor de suporte típico maximiza a margem que separa duas classes***

Outro produto da Microsoft Research, a [SVM localmente profunda de duas classes](/azure/machine-learning/studio-module-reference/two-class-locally-deep-support-vector-machine) é uma variante não linear de SVM que mantém a maior parte da eficiência de velocidade e de memória da versão linear. Ela é ideal para casos em que a abordagem linear não oferece respostas exatas o suficiente. Os desenvolvedores a mantiveram rápida ao dividir o problema em um número de pequenos problemas lineares da SVM. Leia a [descrição completa](http://proceedings.mlr.press/v28/jose13.html) para obter detalhes sobre como eles realizaram esse truque.

Usando uma extensão inteligente de SVMs não lineares, a [SVM de uma classe](/azure/machine-learning/studio-module-reference/one-class-support-vector-machine) cria um limite que descreve totalmente o conjunto de dados inteiro. Ela é útil para a detecção de anomalias. Qualquer ponto de dados que ficar de fora desse limite será incomum o suficiente para ser notado.

### <a name="bayesian-methods"></a>Métodos Bayesianos

Os métodos Bayesianos possuem uma qualidade altamente desejável: eles evitam o superajuste. Eles fazem isso fazendo algumas suposições com antecedência sobre a distribuição provável da resposta. Outro subproduto dessa abordagem é que eles têm muito poucos parâmetros. O Azure Machine Learning Studio tem algoritmos Bayesianos para classificação ([computador do ponto de Bayes de duas classes](/azure/machine-learning/studio-module-reference/two-class-bayes-point-machine)) e regressão ([regressão linear Bayesiana](/azure/machine-learning/studio-module-reference/bayesian-linear-regression)).
Observe que eles supõem que os dados podem ser divididos ou ajustados com uma linha reta.

Em uma nota histórica, os computadores de ponto de Bayes foram desenvolvidos no Microsoft Research. Eles têm algum trabalho teórico excepcionalmente belo por trás deles. O aluno interessado será direcionado para o [artigo original no JMLR](http://jmlr.org/papers/volume1/herbrich01a/herbrich01a.pdf) e para um [blog perspicaz de Chris Bishop](https://blogs.technet.com/b/machinelearning/archive/2014/10/30/embracing-uncertainty-probabilistic-inference.aspx).

### <a name="specialized-algorithms"></a>Algoritmos especializados
Se você tiver um objetivo muito específico, este talvez seja o seu dia de sorte. Na coleção do Azure Machine Learning Studio, há algoritmos especializados em:

- previsão de classificação ([regressão ordinal](/azure/machine-learning/studio-module-reference/ordinal-regression));
- previsão de contagem ([regressão Poisson](/azure/machine-learning/studio-module-reference/poisson-regression));
- detecção de anomalias (uma baseada na [análise de componentes principais](/azure/machine-learning/studio-module-reference/pca-based-anomaly-detection) e outra baseada em [máquinas de vetor de suporte](/azure/machine-learning/studio-module-reference/one-class-support-vector-machine))
- clustering ([K-means](/azure/machine-learning/studio-module-reference/k-means-clustering))

![Detecção de anomalias baseada em PCA](./media/algorithm-choice/image8.png)

***Detecção de anomalias baseada em PCA*** *- a grande maioria dos dados se encaixa em uma distribuição estereotipada; os pontos que desviarem drasticamente dessa distribuição são suspeitos*

![Conjunto de dados agrupados usando K-means](./media/algorithm-choice/image9.png)

***Um conjunto de dados é agrupado em cinco clusters usando K-means***

Há também um conjunto [classificador multiclasse um contra todos](/azure/machine-learning/studio-module-reference/one-vs-all-multiclass), que divide o problema de classificação de classe N em dois problemas de classificação de classe N-1. A precisão, o tempo de treinamento e as propriedades de linearidade são determinados pelos classificadores de duas classes usados.

![Classificadores de duas classes combinados para formar um classificador de três classes](./media/algorithm-choice/image10.png)

***Um par de classificadores de duas classes é combinado para formar um classificador de três classes***

O Azure Machine Learning Studio também inclui acesso a uma poderosa estrutura de aprendizado de máquina sob o título de [Vowpal Wabbit](/azure/machine-learning/studio-module-reference/train-vowpal-wabbit-version-7-4-model).
O VW desafia a categorização aqui, já que pode aprender problemas de classificação e de regressão e pode até aprender de dados parcialmente sem rótulo. Você pode configurá-lo para usar qualquer um de vários algoritmos, funções de perda e algoritmos de otimização de aprendizado. Ele foi projetado desde o início para ser eficiente, paralelo e extremamente rápido. Ele lida com conjuntos de recursos absurdamente grandes com pouco esforço aparente.
Iniciado e liderado pelo próprio John Langford, da Microsoft Research, o VW é uma entrada de Fórmula 1 em um campo de algoritmos de stock cars. Nem todo problema se ajustará ao VW, mas se ele se ajustar, poderá valer a pena seguir a curva de aprendizado em sua interface. Ele também está disponível como [código-fonte aberto autônomo](https://github.com/JohnLangford/vowpal_wabbit) em várias linguagens.

## <a name="next-steps"></a>Próximas etapas

* Para baixar uma visão geral infográfica e fácil de entender dos conceitos básicos do aprendizado de máquina para saber mais sobre os algoritmos populares usados para responder a perguntas comuns desse aprendizado, confira [Conceitos básicos do aprendizado de máquina com exemplos de algoritmo](basics-infographic-with-algorithm-examples.md).

* Para obter uma lista por categoria de todos os algoritmos de aprendizado de máquina disponíveis no Machine Learning Studio, consulte [Inicializar Modelo](/azure/machine-learning/studio-module-reference/machine-learning-initialize-model) na Ajuda de Algoritmo e Módulo do Machine Learning Studio.

* Para obter uma lista completa em ordem alfabética dos algoritmos e módulos no Machine Learning Studio, consulte a [Lista de A-Z de módulos do Machine Learning Studio](/azure/machine-learning/studio-module-reference/a-z-module-list) na Ajuda de módulo e algoritmo do Machine Learning Studio.
