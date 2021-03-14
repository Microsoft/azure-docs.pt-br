---
title: Atenuar a injustação em modelos de aprendizado de máquina (versão prévia)
titleSuffix: Azure Machine Learning
description: Saiba mais sobre a imparcialidade em modelos de machine learning e como o pacote Fairlearn para Python pode ajudá-lo a criar modelos mais razoáveis.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: luquinta
author: luisquintanilla
ms.date: 01/26/2021
ms.custom: responsible-ml
ms.openlocfilehash: 276c91ad7fb5b09dbe18d989741f0f54b8b0eb09
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101659674"
---
# <a name="mitigate-unfairness-in-machine-learning-models-preview"></a>Atenuar a injustação em modelos de aprendizado de máquina (versão prévia)

Saiba mais sobre a imparcialidade no aprendizado de máquina e como o pacote Python de software livre [Fairlearn](https://fairlearn.github.io/) pode ajudá-lo a reduzir problemas de incerteza nos modelos de aprendizado de máquina. Se você não estiver tomando um esforço para entender problemas de imparcialidade e para avaliar a imparcialidade ao criar modelos de aprendizado de máquina, poderá criar modelos que produzam resultados injustos.

O seguinte Resumo do [Guia do usuário](https://fairlearn.github.io/main/user_guide/index.html) para o pacote de código-fonte aberto do Fairlearn, descreve como usá-lo para avaliar a imparcialidade dos sistemas de ia que você está criando.  O pacote Fairlearn Open-Source também pode oferecer opções para ajudar a mitigar ou ajudar a reduzir os problemas de imparcialidade que você observar.  Consulte os [blocos de anotações](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness) de [instruções](how-to-machine-learning-fairness-aml.md) e de exemplo para habilitar a avaliação de imparcialidade de sistemas de ia durante o treinamento em Azure Machine Learning.


## <a name="what-is-fairness-in-machine-learning-models"></a>O que é a imparcialidade em modelos de aprendizado de máquina?

>[!NOTE]
> A imparcialidade é um desafio sociotécnico. Muitos aspectos de imparcialidade, como justiça e processo devido, não são capturados em métricas de imparcialidade quantitativas. Além disso, muitas métricas de imparcialidade quantitativas não podem ser atendidas simultaneamente. O objetivo do Fairlearn pacote de código-fonte aberto é permitir que as pessoas avaliem estratégias diferentes de impacto e mitigação. Por fim, cabe aos usuários humanos criar modelos de inteligência artificial e aprendizado de máquina para fazer compensações apropriadas para seu cenário.

Os sistemas de inteligência artificial e de machine learning podem exibir um comportamento injusto. Uma maneira de definir o comportamento injusto é por seus danos ou pelo impacto sobre as pessoas. Há muitos tipos de danos que os sistemas de IA causar. Confira a [palestra do NeurIPS 2017 de Kate Crawford](https://www.youtube.com/watch?v=fMym_BKWQzk) para saber mais.

Dois tipos comuns de danos causados pela IA são:

- Danos de alocação: um sistema de ia estende ou retenções de oportunidades, recursos ou informações para determinados grupos. Os exemplos incluem contratações, admissões em escolas e empréstimos em que um modelo pode ser muito melhor em escolher bons candidatos entre um grupo específico de pessoas do que entre outros grupos.

- Danos de qualidade de serviço: Um sistema de IA não funciona tão bem para um grupo de pessoas como funciona para outro. Por exemplo, um sistema de reconhecimento de voz pode não funcionar tão bem para mulheres como funciona para homens.

Para reduzir o comportamento injusto em sistemas de IA, você precisa avaliar e atenuar esses danos.

## <a name="fairness-assessment-and-mitigation-with-fairlearn"></a>Avaliação e mitigação de imparcialidade com Fairlearn

O Fairlearn é um pacote Python de software livre que permite que os desenvolvedores de sistemas de aprendizado de máquina avaliem a imparcialidade dos seus sistemas e atenuem a injustação.

O pacote Fairlearn Open-Source tem dois componentes:

- Painel de avaliação: Um widget do Jupyter Notebook para avaliar como as previsões de um modelo afetam diferentes grupos. Ele também permite a comparação de vários modelos usando métricas de desempenho e imparcialidade.
- Algoritmos de mitigação: Um conjunto de algoritmos para atenuar a parcialidade na classificação e regressão binárias.

Juntos, esses componentes permitem que os cientistas de dados e os líderes de negócios naveguem por quaisquer compensações entre a imparcialidade e o desempenho e selecionem a estratégia de mitigação que melhor atenda às suas necessidades.

## <a name="assess-fairness-in-machine-learning-models"></a>Avaliar a imparcialidade em modelos de aprendizado de máquina

No pacote de código-fonte aberto do Fairlearn, a imparcialidade é conceituada por meio de uma abordagem conhecida como **imparcialidade de grupo**, que pergunta: quais grupos de indivíduos estão em risco de sofrer danos? Os grupos relevantes, também conhecidos como subpopulações, são definidos por meio de **recursos sensíveis** ou atributos sensíveis. Os recursos confidenciais são passados para um estimador no pacote de código-fonte aberto do Fairlearn como um vetor ou uma matriz chamada  `sensitive_features` . O termo sugere que o designer do sistema deve ser sensível a esses recursos ao avaliar a imparcialidade de grupo. 

Algo a ser cuidadoso é se esses recursos contêm implicações de privacidade devido a dados privados. Mas a palavra “sensível” não significa que esses recursos não devem ser usados para fazer previsões.

>[!NOTE]
> Uma avaliação de imparcialidade não é um exercício puramente técnico.  O pacote Fairlearn Open-Source pode ajudá-lo a avaliar a imparcialidade de um modelo, mas não realizará a avaliação para você.  O pacote Fairlearn Open-Source ajuda a identificar métricas quantitativas para avaliar a imparcialidade, mas os desenvolvedores também devem executar uma análise qualitativa para avaliar a imparcialidade de seus próprios modelos.  Os recursos confidenciais indicados acima são um exemplo desse tipo de análise qualitativa.     

Durante a fase de avaliação, a imparcialidade é quantificada por meio de métricas de disparidade. As **métricas de disparidade** podem avaliar e comparar o comportamento do modelo entre diferentes grupos como proporções ou como diferenças. O pacote Fairlearn Open-Source dá suporte a duas classes de métricas de diparidade:


- Disparidade no desempenho do modelo: Esses conjuntos de métricas calculam a disparidade (diferença) nos valores da métrica de desempenho selecionada em diferentes subgrupos. Alguns exemplos incluem:

  - disparidade na taxa de precisão
  - disparidade na taxa de erro
  - disparidade na precisão
  - disparidade no recall
  - disparidade de MAE
  - muitos outros

- Disparidade na taxa de seleção: Essa métrica contém a diferença na taxa de seleção entre subgrupos diferentes. Um exemplo disso é a disparidade na taxa de aprovação de empréstimo. Taxa de seleção significa a fração de pontos de dados em cada classe classificada como 1 (em classificação binária) ou distribuição de valores de previsão (em regressão).

## <a name="mitigate-unfairness-in-machine-learning-models"></a>Atenuar a injustação em modelos de aprendizado de máquina

### <a name="parity-constraints"></a>Restrições de paridade

O pacote Fairlearn Open-Source inclui uma variedade de algoritmos de mitigação de injustos. Esses algoritmos dão suporte a um conjunto de restrições no comportamento do previsor chamado **restrições de paridade** ou critérios. As restrições de paridade exigem que alguns aspectos do comportamento do previsor sejam comparáveis em todos os grupos que os recursos sensíveis definem (por exemplo, corridas diferentes). Os algoritmos de mitigação no pacote Fairlearn Open-Source usam essas restrições de paridade para atenuar os problemas de imparcialidade observados.

>[!NOTE]
> Mitigar a injustação em um modelo significa reduzir a incerteza, mas essa mitigação técnica não pode eliminar essa incerteza completamente.  Os algoritmos de mitigação de injustação no pacote de código-fonte aberto do Fairlearn podem fornecer estratégias de mitigação sugeridas para ajudar a reduzir a incerteza em um modelo de aprendizado de máquina, mas não são soluções para eliminar completamente a irregularidade.  Pode haver outras restrições de paridade ou critérios que devem ser considerados para cada modelo de aprendizado de máquina de um desenvolvedor específico. Os desenvolvedores que usam Azure Machine Learning devem determinar se a mitigação de maneira suficientemente elimina qualquer injusto no uso pretendido e na implantação de modelos de aprendizado de máquina.  

O pacote Fairlearn Open-Source dá suporte aos seguintes tipos de restrições de paridade: 

|Restrição de paridade  | Finalidade  |Ferramenta de machine learning  |
|---------|---------|---------|
|Paridade demográfica     |  Mitigar danos de alocação | Classificação binária, regressão |
|Chances equalizadas  | Diagnosticar a alocação e os danos de qualidade de serviço | Classificação binária        |
|Oportunidade igual | Diagnosticar a alocação e os danos de qualidade de serviço | Classificação binária        |
|Perda de grupo limitada     |  Mitigar os danos de qualidade de serviço | Regressão |



### <a name="mitigation-algorithms"></a>Algoritmos de mitigação

O pacote Fairlearn Open-Source fornece algoritmos de mitigação de desprocessamento e redução de injustação:

- Redução: esses algoritmos assumem um estimador de aprendizado de máquina de caixa preta padrão (por exemplo, um modelo de LightGBM) e geram um conjunto de modelos retreinados usando uma sequência de conjuntos de documentos de treinamento reponderados. Por exemplo, é possível aumentar o diminuir o peso de candidatos de um determinado gênero para readaptar os modelos e reduzir as disparidades em diferentes grupos de gêneros. Os usuários podem, então, escolher um modelo que forneça a melhor compensação entre precisão (ou outra métrica de desempenho) e disparidade, que geralmente precisaria ser baseada em regras de negócio e cálculos de custo.  
- Pós-processamento: Esses algoritmos usam um classificador existente e o recurso sensível como entrada. Em seguida, eles produzem uma transformação da previsão do classificador para impor as restrições de imparcialidade especificadas. A maior vantagem da otimização do limite é sua simplicidade e flexibilidade, pois não é necessário readaptar o modelo. 

| Algoritmo | Descrição | Ferramenta de machine learning | Recursos sensíveis | Restrições de paridade suportadas | Tipo de algoritmo: |
| --- | --- | --- | --- | --- | --- |
| `ExponentiatedGradient` | Abordagem de caixa preta para classificação justa descrita em [Uma abordagem de reduções para classificação justa](https://arxiv.org/abs/1803.02453) | Classificação binária | Categóricos | [Paridade demográfica](#parity-constraints), [chances equalizadas](#parity-constraints) | Redução |
| `GridSearch` | Abordagem de caixa preta descrita em [Uma abordagem de reduções para classificação justa](https://arxiv.org/abs/1803.02453)| Classificação binária | Binário | [Paridade demográfica](#parity-constraints), [chances equalizadas](#parity-constraints) | Redução |
| `GridSearch` | Abordagem de caixa preta que implementa uma variante de pesquisa de grade da regressão justa com o algoritmo para perda de grupo limitada descrito em [Regressão justa: Definições quantitativas e algoritmos baseados em redução](https://arxiv.org/abs/1905.12843) | Regressão | Binário | [Perda de grupo limitada](#parity-constraints) | Redução |
| `ThresholdOptimizer` | Algoritmo de pré-processamento baseado no documento [Igualdade de oportunidades em aprendizado supervisionado](https://arxiv.org/abs/1610.02413). Essa técnica usa como entrada um classificador existente e o recurso sensível, e produz uma transformação monotônica da previsão do classificador para impor as restrições de paridade especificadas. | Classificação binária | Categóricos | [Paridade demográfica](#parity-constraints), [chances equalizadas](#parity-constraints) | Pós-processamento |

## <a name="next-steps"></a>Próximas etapas

- Saiba como usar os diferentes componentes, conferindo o [GitHub](https://github.com/fairlearn/fairlearn/), guia do [usuário](https://fairlearn.github.io/main/user_guide/index.html), [exemplos](https://fairlearn.github.io/main/auto_examples/index.html)e [notebooks de exemplo](https://github.com/fairlearn/fairlearn/tree/master/notebooks)do Fairlearn.
- Saiba [como habilitar a](how-to-machine-learning-fairness-aml.md) avaliação de imparcialidade de modelos de aprendizado de máquina no Azure Machine Learning.
- Consulte os [blocos de anotações de exemplo](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness) para cenários de avaliação de imparcialidade adicionais no Azure Machine Learning. 
