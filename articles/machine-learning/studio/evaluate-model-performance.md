---
title: Avaliar o desempenho do modelo no Machine Learning | Microsoft Docs
description: Explica como avaliar o desempenho do modelo no Azure Machine Learning.
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 5dc5348a-4488-4536-99eb-ff105be9b160
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: bradsev;garye
ms.openlocfilehash: 48ce4584f7270d78b1d09b848bfdd305d03012b9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-evaluate-model-performance-in-azure-machine-learning"></a>Como avaliar o desempenho do modelo no Azure Machine Learning
Este tópico demonstra como avaliar o desempenho de um modelo no Azure Machine Learning Studio e fornece uma breve explicação sobre as métricas disponíveis para essa tarefa. Três cenários comuns de aprendizado supervisionado são apresentados: 

* regressão
* classificação binária 
* classificação multiclasse

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

Avaliar o desempenho de um modelo é um dos estágios principais do processo de ciência de dados. Indica o êxito da pontuação (previsões) de um conjunto de dados feitas por meio de um modelo treinado. 

O Azure Machine Learning dá suporte à avaliação de modelo por meio de dois dos seus principais módulos de aprendizado de máquina: [Avaliar Modelo][evaluate-model] e [Modelo de Validação Cruzada][cross-validate-model]. Esses módulos permitem que você veja como o seu modelo é executado em termos de um número de métricas que são usados normalmente em estatísticas e aprendizado de máquina.

## <a name="evaluation-vs-cross-validation"></a>Avaliação versus Validação cruzada
Avaliação e Validação cruzada são as formas padrão de medir o desempenho do seu modelo. Ambas geram métricas de avaliação que você pode inspecionar ou comparar com os outros modelos.

[Avaliar Modelo][evaluate-model] espera um conjunto de dados de pontuação como entrada (ou 2 caso você deseje comparar o desempenho de 2 modelos diferentes). Isso significa que você precisa treinar o modelo usando o módulo [Treinar Modelo][train-model] e fazer previsões em um conjunto de dados usando o módulo [Modelo de Pontuação][score-model] para poder avaliar os resultados. A avaliação se baseia nas probabilidades/rótulos pontuados juntamente com os rótulos verdadeiros, que são produzidos pelo módulo [Modelo de Pontuação][score-model].

Como alternativa, você pode usar a validação cruzada para executar várias operações para avaliar-treinar-pontuar (10 partições) automaticamente em diferentes subconjuntos dos dados de entrada. Os dados de entrada são divididos em 10 partes, em que uma está reservada para teste, e as outras 9 para treinamento. Esse processo é repetido 10 vezes e as métricas de avaliação são transformadas em médias. Isso ajuda a determinar como um modelo seria generalizado para novos conjuntos de dados. O módulo [Modelo de Validação Cruzada][cross-validate-model] aproveita um modelo não treinado e alguns conjuntos de dados rotulados e gera os resultados da avaliação de cada uma das 10 partições, além dos resultados médios.

Nas seções a seguir, compilaremos modelos de regressão e classificação simples e avaliaremos o desempenho usando os módulos [Avaliar Modelo][evaluate-model] e [Modelo de Validação Cruzada][cross-validate-model].

## <a name="evaluating-a-regression-model"></a>Avaliar um Modelo de regressão
Suponha que desejamos prever o preço do carro usando alguns recursos, como dimensões, potência, especificações de mecanismo e assim por diante. Este é um problema comum de regressão, em que a variável de destino (*preço*) é um valor numérico contínuo. Conseguimos ajustar um modelo de regressão linear simples que, considerando os valores das características de um determinado carro, pode prever o preço daquele carro. Esse modelo de regressão pode ser usado para a pontuação do mesmo conjunto de dados no qual treinamos. Assim que tivermos os preços previstos para todos os carros, poderemos avaliar o desempenho do modelo observando quanto, em média, as previsões se desviam dos preços reais. Para ilustrar isso, usamos o *conjunto de dados Dados de preço de automóvel (Brutos)* disponível na seção **Conjuntos de Dados Salvos** no Azure Machine Learning Studio.

### <a name="creating-the-experiment"></a>Criando o experimento
Adicione os seguintes módulos ao seu espaço de trabalho no Machine Learning Studio do Microsoft Azure:

* Dados de preço de automóvel (Brutos)
* [Regressão linear][linear-regression]
* [Modelo de Treinamento][train-model]
* [Modelo de Pontuação][score-model]
* [Avaliar Modelo][evaluate-model]

Conecte as portas, conforme mostrado abaixo na Figura 1 e defina a coluna Rótulo do módulo [Treinar Modelo][train-model] como *preço*.

![Avaliar um Modelo de regressão](./media/evaluate-model-performance/1.png)

Figura 1. Avaliar um Modelo de regressão.

### <a name="inspecting-the-evaluation-results"></a>Inspecionando os Resultados da avaliação
Depois de executar o experimento, você pode clicar na porta de saída do módulo [Avaliar Modelo][evaluate-model] e selecionar *Visualizar* para ver os resultados da avaliação. As métricas de avaliação disponíveis para modelos de regressão são: *Erro Absoluto Médio*, *Erro Absoluto Médio de Raiz*, *Erro Absoluto Relativo*, *Erro Quadrado Relativo* e *Coeficiente de Determinação*.

O termo "erro" aqui representa a diferença entre o valor previsto e o valor verdadeiro. O valor absoluto ou o quadrado dessa diferença geralmente são computados para capturar a magnitude total do erro em todas as instâncias, como a diferença entre o valor previsto e o verdadeiro pode ser negativa em alguns casos. As métricas de erro medem o desempenho de previsão de um modelo de regressão em termos do desvio da média de suas previsões dos valores verdadeiros. Valores mais baixos de erro significam que o modelo é mais preciso para fazer previsões. Uma métrica de erro geral 0 significa que o modelo se ajusta aos dados perfeitamente.

O coeficiente de determinação, que também é conhecido como R ao quadrado, também é uma maneira padrão de medir o quão bem o modelo se ajusta aos dados. Ele pode ser interpretado como a proporção da variação explicada pelo modelo. Uma proporção mais alta é melhor nesse caso, em que 1 indica um ajuste perfeito.

![Métrica de avaliação de regressão linear](./media/evaluate-model-performance/2.png)

Figura 2. Métrica de avaliação de regressão linear.

### <a name="using-cross-validation"></a>Usando Validação Cruzada
Como mencionado anteriormente, você pode executar treinamento, pontuação e avaliações repetidas automaticamente usando o módulo [Modelo de Validação Cruzada][cross-validate-model]. Tudo o que você precisa nesse caso é um conjunto de dados, um modelo não treinado e um módulo [Modelo de Validação Cruzada][cross-validate-model] (veja a figura abaixo). Observe que você precisa definir a coluna de rótulo como *preço* nas propriedades do módulo [Modelo de Validação Cruzada][cross-validate-model].

![Validação cruzada de um modelo de regressão](./media/evaluate-model-performance/3.png)

Figura 3. A validação cruzada em um modelo de regressão.

Depois de executar o experimento, você pode inspecionar os resultados da avaliação clicando na porta de saída à direita do módulo [Modelo de Validação Cruzada][cross-validate-model]. Isso fornecerá uma exibição detalhada das métricas para cada iteração (partição) e os resultados médios de cada uma das métricas (Figura 4).

![Resultados de validação cruzada de um modelo de regressão](./media/evaluate-model-performance/4.png)

Figura 4. Resultados de Validação cruzada de um modelo de regressão.

## <a name="evaluating-a-binary-classification-model"></a>Avaliar um modelo de classificação binária
Em um cenário de classificação binária, a variável de destino tem somente dois resultados possíveis, por exemplo: {0, 1} ou {falso, verdadeiro}, {negativo, positivo}. Suponha que você terá um conjunto de dados de funcionários adultos com algumas variáveis demográficas e de emprego e que você será solicitado a prever o nível de renda, uma variável binária com os valores {“<=50K”, “>50K”}. Em outras palavras, a classe negativa representa os funcionários que recebem um valor menor ou igual a 50 mil por ano, e a classe positiva representa todos os outros funcionários. Como no cenário de regressão, podemos treinar um modelo, pontuar alguns dados e avaliar os resultados. A principal diferença é a opção de métricas que o Azure Machine Learning computa e as saídas. Para ilustrar o cenário de previsão de nível de renda, utilizaremos o conjunto de dados [Adulto](http://archive.ics.uci.edu/ml/datasets/Adult) para criar um experimento de Azure Machine Learning e avaliar o desempenho de um modelo de regressão logística de duas classes, uma classificação binária tipicamente usada.

### <a name="creating-the-experiment"></a>Criando o experimento
Adicione os seguintes módulos ao seu espaço de trabalho no Machine Learning Studio do Microsoft Azure:

* Conjunto de dados de classificação binária de receita no recenseamento adulto
* [Regressão logística de duas classes][two-class-logistic-regression]
* [Modelo de Treinamento][train-model]
* [Modelo de Pontuação][score-model]
* [Avaliar Modelo][evaluate-model]

Conecte as portas, conforme mostrado abaixo na Figura 5 e defina a coluna Rótulo do módulo [Treinar Modelo][train-model] como *renda*.

![Avaliar um modelo de classificação binária](./media/evaluate-model-performance/5.png)

Figura 5. Avaliar um modelo de classificação binária.

### <a name="inspecting-the-evaluation-results"></a>Inspecionando os Resultados da avaliação
Depois de executar o experimento, você pode clicar na porta de saída do módulo [Avaliar Modelo][evaluate-model] e selecionar *Visualizar* para ver os resultados da avaliação (Figura 7). A métrica de avaliação disponível para modelos de classificação binária são: *Exatidão*, *Precisão*, *Recuperação*, *Pontuação F1* e *AUC*. Além disso, o módulo gera uma matriz de confusão, mostrando o número de verdadeiros positivos, de falsos negativos, de falsos positivos e de verdadeiros negativos, bem como as curas *ROC*, *Precisão/Recuperação* e *Elevação*.

A precisão é simplesmente a proporção das instâncias classificadas corretamente. Geralmente, ela é a primeira métrica a observar ao avaliar um classificador. No entanto, quando os dados de teste não estão desbalanceados (em que a maioria das instâncias pertence a uma das classes), ou você está mais interessado no desempenho em qualquer uma das classes, a precisão realmente não captura a eficácia de um classificador. No cenário de classificação de nível de renda, suponha que você esteja testando em alguns dados em que 99% das instâncias representam pessoas que ganham um valor menor ou igual a 50 mil por ano. É possível obter uma precisão de 0,99 prevendo a classe “<=50K” para todas as instâncias. O classificador parece estar fazendo um bom trabalho em geral nesse caso, mas na realidade, ele não consegue classificar qualquer uma das pessoas com um recebimento mais elevado (% 1) corretamente.

Por esse motivo, é útil computar métricas adicionais que capturam aspectos mais específicos da avaliação. Antes de entrar em detalhes sobre essas métricas, é importante compreender a matriz de confusão de uma avaliação de classificação binária. Os rótulos de classe no conjunto de treinamento podem assumir apenas 2 valores possíveis, o que normalmente chamamos como positivo ou negativo. As instâncias positivas e negativas que um classificador prevê corretamente são chamadas verdadeiros positivos (TP) e verdadeiros negativos (TN), respectivamente. Da mesma forma, as instâncias classificadas incorretamente são chamadas de falsos positivos (FP) e falsos negativos (FN). A matriz de confusão é simplesmente uma tabela que mostra o número de instâncias que se enquadram em cada uma dessas 4 categorias. O Azure Machine Learning decide automaticamente qual das duas classes no conjunto de dados é a classe positiva. Se os rótulos de classe forem inteiros ou boolianos, as instâncias de rotuladas como 'true' ou '1' serão atribuídas à classe positiva. Se os rótulos forem cadeias de caracteres, como no caso do conjunto de dados de renda, os rótulos serão classificados em ordem alfabética e o primeiro nível será escolhido para ser a classe negativa, enquanto o segundo nível será a classe positiva.

![Matriz de confusão de classificação binária](./media/evaluate-model-performance/6a.png)

Figura 6. Matriz de confusão de classificação binária.

Voltando ao problema de classificação de renda, queremos fazer várias perguntas de avaliação que nos ajudam a compreender o desempenho do classificador usado. Uma pergunta muito comum é: “Dentre os indivíduos que o modelo prevê estarem ganhando > 50 mil (TP + FP), quantos foram classificados corretamente (TP)?" Essa pergunta pode ser respondida, examinando a **Precisão** do modelo, que é a proporção de positivos que foram classificados corretamente: TP/(TP + FP). Outra pergunta comum é “Dentre todos os funcionários com renda alta > 50 mil (TP + FN), quantos o classificador classifica corretamente (TP)". Na verdade, isso é a **Recuperação** ou a taxa de positivos verdadeiros: TP/(TP + FN) do classificador. Você observará que há uma opção óbvia entre a precisão e a recuperação. Por exemplo, dado um conjunto de dados relativamente equilibrado, uma classificação que prevê principalmente instâncias positivas, teria um lembrete alto, mas uma precisão bastante baixa com muitas instâncias negativas deve ser classificada incorretamente, resultando em um grande número de falsos positivos. Para ver um gráfico de como essas duas métricas variam, você pode clicar na curva ‘PRECISION/RECALL’ na página de saída do resultado de avaliação (parte superior esquerda da Figura 7).

![Resultados da avaliação de classificação binária](./media/evaluate-model-performance/7.png)

Figura 7. Resultados da avaliação de classificação binária.

Outra métrica relacionada usada com frequência é a **Pontuação F1**, que usa a precisão e a recuperação em consideração. Ela é a média harmônica dessas 2 métricas e é calculada como tal: F1 = 2 (precisão x recuperação) / (precisão + recuperação). A pontuação F1 é uma boa maneira de resumir a avaliação de um único número, mas é sempre uma boa prática examinar a precisão e a recuperação para entender melhor como um classificador se comporta.

Além disso, é possível inspecionar a taxa de positivos verdadeiros versus a taxa de falsos positivos na curva **ROC (Característica de Operação do Receptor)** e o valor correspondente de **AUC (Área sob a Curva)**. Quanto mais próxima essa curva estiver do canto superior esquerdo, melhor estará o desempenho do classificador (que é maximizar a taxa de positivos verdadeiros enquanto minimiza os falsos positivos). As curvas que estão próximas à diagonal do gráfico, o resultado dos classificadores que tendem a fazer previsões próximas à previsão aleatória.

### <a name="using-cross-validation"></a>Usando Validação Cruzada
Como no exemplo de regressão, podemos executar validação cruzada para treinar repetidamente, classificar e avaliar os diferentes subconjuntos de dados automaticamente. Da mesma forma, podemos usar o módulo [Modelo de Validação Cruzada][cross-validate-model], um modelo de regressão logística não treinado e um conjunto de dados. A coluna de rótulo deve ser definida como *renda* nas propriedades do módulo [Modelo de Validação Cruzada][cross-validate-model]. Após executar o experimento e clicar na porta de saída à direita do módulo [Modelo de Validação Cruzada][cross-validate-model], podemos ver os valores de métrica de classificação binária para cada partição, além da média e do desvio padrão de cada um. 

![Validação cruzada de um modelo de classificação binária](./media/evaluate-model-performance/8.png)

Figura 8. Validação cruzada em um modelo de classificação binária.

![Resultados de validação cruzada de um classificador binário](./media/evaluate-model-performance/9.png)

Figura 9. Resultados de validação cruzada de um classificador binário.

## <a name="evaluating-a-multiclass-classification-model"></a>Avaliar um modelo de classificação com multiclass
Nesse experimento, usaremos o conjunto de dados popular [Íris](http://archive.ics.uci.edu/ml/datasets/Iris "Íris"), que contém instâncias de 3 diferentes tipos (classes) da planta íris. Há 4 valores de recurso (comprimento/largura da sépala e comprimento/largura da pétala) para cada instância. Nas experiências anteriores, treinamos e testamos os modelos usando os mesmos conjuntos de dados. Aqui, usaremos o módulo de [Dividir Dados][split] para criar 2 subconjuntos de dados, treinar no primeiro e pontuar e avaliar no segundo. O conjunto de dados Íris está disponível publicamente no [Repositório de Machine Learning UCI](http://archive.ics.uci.edu/ml/index.html) e pode ser baixado usando um módulo [Importar Dados][import-data].

### <a name="creating-the-experiment"></a>Criando o experimento
Adicione os seguintes módulos ao seu espaço de trabalho no Machine Learning Studio do Microsoft Azure:

* [Importar Dados][import-data]
* [Floresta de Decisão Multiclasse][multiclass-decision-forest]
* [Dividir Dados][split]
* [Modelo de Treinamento][train-model]
* [Modelo de Pontuação][score-model]
* [Avaliar Modelo][evaluate-model]

Conecte as portas, conforme mostrado abaixo na Figura 10.

Configure o índice da coluna Rótulo do módulo [Treinar Modelo][train-model] como 5. O conjunto de dados não tem nenhuma linha de cabeçalho, mas sabemos que os rótulos de classe estão na quinta coluna.

Clique no módulo [Importar Dados][import-data] e defina a propriedade *Fonte de dados* como *URL da Web via HTTP* e a *URL* como http://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data.

Defina a fração de instâncias a serem usadas para treinamento no módulo [Dividir Dados][split] (0,7, por exemplo).

![Avaliar um classificador Multiclass](./media/evaluate-model-performance/10.png)

Figura 10. Avaliar um classificador Multiclass

### <a name="inspecting-the-evaluation-results"></a>Inspecionando os Resultados da avaliação
Execute o experimento e clique na porta de saída de [Avaliar Modelo][evaluate-model]. Os resultados de avaliação são apresentados na forma de uma matriz de confusão, nesse caso. A matriz mostra o real versus as instâncias previstas para todas as 3 classes.

![Resultados da avaliação de classificação multiclass](./media/evaluate-model-performance/11.png)

Figura 11. Resultados da avaliação de classificação multiclass.

### <a name="using-cross-validation"></a>Usando Validação Cruzada
Como mencionado anteriormente, você pode executar treinamento, pontuação e avaliações repetidas automaticamente usando o módulo [Modelo de Validação Cruzada][cross-validate-model]. Você precisará de um conjunto de dados, um modelo não treinado e um módulo [Modelo de Validação Cruzada][cross-validate-model] (veja a figura abaixo). Novamente, você precisa definir a coluna de rótulo do módulo [Modelo de Validação Cruzada][cross-validate-model] (índice de coluna 5 neste caso). Após executar o experimento e clicar na porta de saída à direita do [Modelo de Validação Cruzada][cross-validate-model], você pode inspecionar os valores de métrica para cada partição, além da média e do desvio padrão. As métricas exibidas aqui são semelhantes àquelas discutidas no caso de classificação binária. No entanto, observe que em classificação multiclass, os verdadeiros positivos/negativos e falsos positivos/negativos de computação são feitos baseados em uma base por classe, pois não há nenhuma classe geral positiva ou negativa. Por exemplo, ao computar a precisão ou o cancelamento da classe ‘Iris-setosa’, supõe-se que essa seja a classe positiva e todas as outras sejam negativas.

![Validação cruzada de um modelo de classificação multiclass](./media/evaluate-model-performance/12.png)

Figura 12. Validação cruzada em um modelo de classificação Multiclass.

![Resultados da validação cruzada de um modelo de classificação multiclass](./media/evaluate-model-performance/13.png)

Figura 13. Resultados da validação cruzada de um modelo de classificação multiclass.

<!-- Module References -->
[cross-validate-model]: https://msdn.microsoft.com/library/azure/75fb875d-6b86-4d46-8bcc-74261ade5826/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[multiclass-decision-forest]: https://msdn.microsoft.com/library/azure/5e70108d-2e44-45d9-86e8-94f37c68fe86/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-logistic-regression]: https://msdn.microsoft.com/library/azure/b0fd7660-eeed-43c5-9487-20d9cc79ed5d/

