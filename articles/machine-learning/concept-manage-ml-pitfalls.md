---
title: Evitar dados de sobreajuste e desequilibrados com o AutoML
titleSuffix: Azure Machine Learning
description: Identifique e gerencie armadilhas comuns de modelos de ML com as soluções de machine learning automatizado do Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 04/09/2020
ms.openlocfilehash: ba85bda1e322d3efd467527b48bd4cd90eb7ce8c
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96922617"
---
# <a name="prevent-overfitting-and-imbalanced-data-with-automated-machine-learning"></a>Evitar dados de sobreajuste e desequilibrados com o machine learning automatizado

Os dados de sobreajuste e desequilibrados são armadilhas comuns quando você cria modelos de machine learning. Por padrão, o machine learning automatizado do Azure Machine Learning fornece gráficos e métricas para ajudá-lo a identificar esses riscos e implementa práticas recomendadas para ajudar a atenuá-los. 

## <a name="identify-over-fitting"></a>Identificar o sobreajuste

O sobreajuste no aprendizado de máquina ocorre quando um modelo se ajusta bem demais aos dados de treinamento e, como resultado, não consegue prever com precisão os dados de teste não vistos. Em outras palavras, o modelo simplesmente memorizou padrões e ruídos específicos nos dados de treinamento, mas não é suficientemente flexível para fazer previsões sobre dados reais.

Considere os seguintes modelos treinados e suas imprecisões de treinamento e de teste correspondentes.

| Modelo | Precisão de treinamento | Precisão de teste |
|-------|----------------|---------------|
| Um | 99,9% | 95% |
| B | 87% | 87% |
| C | 99,9% | 45% |

Considerando o modelo **A**, há um equívoco comum de que, se a precisão de teste nos dados não vistos for menor do que a precisão de treinamento, o modelo será sobreajustado. No entanto, a precisão de teste deve ser sempre menor que a precisão de treinamento, e a distinção entre sobreajuste e ajuste adequado se resume a *quanto* menos preciso. 

Ao comparar os modelos **A** e **B**, o modelo **A** é um modelo melhor porque tem maior precisão de teste e, embora a precisão de teste seja um pouco menor em 95%, não é uma diferença significativa que sugira que exista sobreposição. Você não escolheria o modelo **B** simplesmente porque as precisões de treinamento e de teste estão mais próximas.

O modelo **C** representa um caso claro de sobreajuste; a precisão de treinamento é muito alta, mas a precisão de teste não é quase tão alta. Essa distinção é subjetiva, mas vem do conhecimento do seu problema e dados e de quais magnitudes de erro são aceitáveis.

## <a name="prevent-over-fitting"></a>Evitar o sobreajuste

Na maioria dos casos flagrantes, um modelo de sobreajuste assumirá que as combinações de valor de recurso vistas durante o treinamento resultarão sempre na mesma saída para o destino.

A melhor maneira de evitar o sobreajuste é seguir as práticas recomendadas do Azure Machine Learning, incluindo:

* Usar mais dados de treinamento e eliminar a tendência estatística
* Evitar o vazamento de destino
* Usar menos recursos
* **Otimização de hiperparâmetro e regularização**
* **Limitações de complexidade do modelo**
* **Validação cruzada**

No contexto de machine learning automatizado, os três primeiros itens acima são **práticas recomendadas que você implementa**. Os últimos três itens em negrito são **as práticas recomendadas que o machine learning automatizado implementa** por padrão para proteção contra sobreajuste. Em configurações diferentes da do machine learning automatizado, vale a pena seguir todas as seis práticas recomendadas para evitar modelos de sobreajuste.

### <a name="best-practices-you-implement"></a>Práticas recomendadas que você implementa

Usar **mais dados** é a maneira mais simples e melhor possível de evitar o sobreajuste, e como um bônus adicional, normalmente aumenta a precisão. Quando você usa mais dados, fica mais difícil para o modelo memorizar padrões exatos, e é forçado a alcançar soluções mais flexíveis para acomodar mais condições. Também é importante reconhecer o **desvio estatístico** para garantir que os dados de treinamento não incluam padrões isolados que não existam em dados de previsão dinâmica. Esse cenário pode ser difícil de resolver, pois pode não haver sobreajuste entre seus conjuntos de treinamento e de teste, mas pode haver sobreajuste em comparação com os dados de teste dinâmico.

**O vazamento de destino** é um problema semelhante, no qual você pode não ver o sobreajuste entre os conjuntos de treinamento/de teste, mas ele aparece no tempo de previsão. O vazamento de destino ocorre quando seu modelo "trapaceia" durante o treinamento tendo acesso aos dados que normalmente não deveria ter no tempo de previsão. Por exemplo, se o problema for prever na segunda-feira qual será o preço das commodities na sexta-feira, mas um de seus recursos acidentalmente incluía dados das quintas-feiras, seriam dados que o modelo não terá no tempo de previsão, pois não pode ver o futuro. O vazamento de destino é um erro fácil de ser ignorado, mas costuma ser caracterizado por uma precisão muito alta para o seu problema. Se você estiver tentando prever o preço das ações e tiver treinado um modelo com precisão de 95%, é provável que haja vazamento de destino em algum lugar em seus recursos.

**A remoção de recursos** também pode ajudar no sobreajuste, impedindo que o modelo tenha muitos campos a serem usados para memorizar padrões específicos, fazendo com que ele seja mais flexível. Pode ser difícil medir de forma quantitativa. Contudo, se você puder remover recursos e manter a mesma precisão, você provavelmente tornará o modelo mais flexível e reduzirá o risco de sobreajuste.

### <a name="best-practices-automated-ml-implements"></a>Práticas recomendadas que o machine learning automatizado implementa

A **regularização** é o processo de minimizar uma função de custo para penalizar modelos complexos e sobreajustados. Há diferentes tipos de funções de regularização. Porém, em geral todos eles penalizam a variância, a complexidade e o tamanho do coeficiente do modelo. O machine learning automatizado usa L1 (Laço), L2 (Ressalto) e ElasticNet (L1 e L2 simultaneamente) em combinações diferentes com configurações de hiperparâmetro de modelo diferentes que controlam o sobreajuste. Em termos simples, o machine learning automatizado vai variar o quanto um modelo é regulamentado e escolher o melhor resultado.

O machine learning automatizado também implementa **limitações de complexidade de modelo** explícitas para evitar o sobreajuste. Na maioria dos casos, essa implementação é especificamente para algoritmos de árvore de floresta e árvore de decisão, em que a profundidade máxima da árvore individual é limitada, e o número total de árvores usadas nas técnicas de floresta ou de ensemble é limitado.

A **CV (validação cruzada)** é o processo de pegar muitos subconjuntos de seus dados de treinamento completos e treinar um modelo em cada subconjunto. A ideia é que um modelo poderia ter "sorte" e ter grande precisão com um subconjunto, mas usando muitos subconjuntos o modelo não atingirá essa alta precisão sempre. Ao fazer a CV, você fornece um conjunto de um conjunto de dados de controle de validação, especifica as dobras de CV (número de subconjuntos), e o machine learning automatizado treinará seu modelo e ajustará os hiperparâmetros para minimizar o erro em seu conjunto de validação. Uma dobra de CV pode ser sobreajustada, mas usar muitas delas reduz a probabilidade de que o modelo final seja sobreadequado. A desvantagem é que a CV resulta em tempos de treinamento maiores. Portanto, um custo maior, porque em vez de treinar um modelo uma vez, você o treina uma vez para cada *n* subconjuntos de CV. 

> [!NOTE]
> A validação cruzada não está habilitada por padrão; ela deve ser configurada nas configurações de machine learning automatizado. No entanto, depois que a validação cruzada estiver configurada, e um conjunto de dados de validação tiver sido fornecido, o processo será automatizado para você. Saiba mais sobre a [configuração de validação cruzada no ml automático](how-to-configure-cross-validation-data-splits.md)

<a name="imbalance"></a>

## <a name="identify-models-with-imbalanced-data"></a>Identificar modelos com dados desequilibrados

Normalmente, os dados desequilibrados são encontrados em dados para cenários de classificação de aprendizado de máquina e referem-se a dados que contêm uma proporção desproporcional de observações em cada classe. Esse desequilíbrio pode causar um efeito de falso positivo de acordo com a precisão de um modelo porque os dados de entrada têm desvio, o que resulta no modelo treinado para imitar esse desvio. 

Além disso, as execuções de machine learning automatizado geram automaticamente os gráficos a seguir, o que pode ajudá-lo a entender a exatidão das classificações do modelo, além de identificar os modelos potencialmente afetados por dados desequilibrados.

Gráfico| Descrição
---|---
[Matriz de confusão](how-to-understand-automated-ml.md#confusion-matrix)| Avalia os rótulos classificados corretamente em relação aos rótulos reais dos dados. 
[Precisão/recall](how-to-understand-automated-ml.md#precision-recall-curve)| Avalia a proporção de rótulos corretos em relação à taxa de instâncias de rótulo encontradas dos dados 
[Curvas ROC](how-to-understand-automated-ml.md#roc-curve)| Avalia a proporção de rótulos corretos em relação à taxa de rótulos falsos positivos.

## <a name="handle-imbalanced-data"></a>Tratamento dos dados desequilibrados 

Como parte de seu objetivo de simplificar o fluxo de trabalho do Machine Learning, o **ml automatizado tem recursos internos** para ajudar a lidar com dados desequilibrados, como 

- Uma **coluna de peso**: o ml automatizado dá suporte a uma coluna de pesos como entrada, fazendo com que as linhas nos dados sejam ponderadas ou reduzidas, o que pode ser usado para tornar uma classe mais ou menos "importante".

- Os algoritmos usados pelo desequilíbrio de detecção de ML automatizado quando o número de amostras na classe minoritária são iguais ou menores que 20% do número de amostras na classe principal, em que a classe minoritária refere-se a um com menos amostras e classe de maioria refere-se a uma com a maioria dos exemplos. Posteriormente, o AutoML executará um experimento com dados de subamostrados para verificar se o uso de pesos de classe corrigiria esse problema e melhorará o desempenho. Se ele garantir um desempenho melhor por meio desse experimento, essa solução será aplicada.

- Use uma métrica de desempenho que lide melhor com dados desequilibrados. Por exemplo, a AUC_weighted é uma métrica primária que calcula a contribuição de cada classe com base no número relativo de amostras que representam essa classe, portanto, é mais robusta em relação ao desequilíbrio.

As técnicas a seguir são opções adicionais para lidar com dados desequilibrados **fora do ml automatizado**. 

- A reamostragem até mesmo do desequilíbrio de classe, seja por upsampling das classes menores ou downsampling das classes maiores. Esses métodos exigem experiência para processar e analisar.

- Examine as métricas de desempenho para dados desequilibrados. Por exemplo, a pontuação F1 é a média harmônica de precisão e RECALL. A precisão mede a exatidão de um classificador, onde a precisão mais alta indica menos falsos positivos, enquanto a RECALL mede a integridade de um classificador, onde a RECALL superior indica menos falsos negativos.

## <a name="next-steps"></a>Próximas etapas

Veja exemplos e saiba como criar modelos usando o machine learning automatizado:

+ Siga o [tutorial: Treinar automaticamente um modelo de regressão com o Azure Machine Learning](tutorial-auto-train-models.md)

+ Defina as configurações para o teste de treinamento automático:
  + No Azure Machine Learning Studio, [use estas etapas](how-to-use-automated-ml-for-ml-models.md).
  + Com o SDK do Python, [use estas etapas](how-to-configure-auto-train.md).


