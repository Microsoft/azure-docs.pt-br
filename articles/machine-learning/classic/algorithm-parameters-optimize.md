---
title: 'ML Studio (clássico): otimizar algoritmos – Azure'
description: Explica como escolher o conjunto de parâmetros ideal para um algoritmo no Azure Machine Learning Studio (clássico).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 11/29/2017
ms.openlocfilehash: a216dc7cb17b9e35e412c6bebe34c0cccfb732e4
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100519296"
---
# <a name="choose-parameters-to-optimize-your-algorithms-in-machine-learning-studio-classic"></a>Escolha os parâmetros para otimizar seus algoritmos no Machine Learning Studio (clássico)

**APLICA-SE A:**  ![Aplica-se a.](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (clássico) ![Não se aplica a.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)

Este tópico descreve como escolher o conjunto de hiperparâmetros corretos para um algoritmo no Azure Machine Learning Studio (clássico). A maioria dos algoritmos de Machine Learning tem parâmetros para serem definidos. Ao treinar um modelo, você precisa fornecer valores para esses parâmetros. A eficácia do modelo treinado depende dos parâmetros do modelo que você escolhe. O processo de localizar o melhor conjunto de parâmetros é conhecido como *seleção de modelo*.



Há várias maneiras de realizar a seleção de modelo. No Machine Learning, a validação cruzada é um dos métodos mais amplamente usados para seleção de modelo e é o mecanismo de seleção de modelo padrão no Azure Machine Learning Studio (clássico). Como Azure Machine Learning Studio (clássico) dá suporte a R e Python, você sempre pode implementar seus próprios mecanismos de seleção de modelo usando o R ou o Python.

Há quatro etapas no processo de localizar o melhor conjunto de parâmetros:

1. **Defina o espaço de parâmetro**: para o algoritmo, primeiro decida os valores de parâmetro exatos que você quer considerar.
2. **Definir as configurações de validação cruzada**: decida como escolher dobras de validação cruzada no conjunto de dados.
3. **Definir métrica**: decida qual métrica deve ser usada para determinar o melhor conjunto de parâmetros, como precisão, erro de raiz quadrada média, precisão, recall ou pontuação f.
4. **Treinar, avaliar e comparar**: para cada combinação exclusiva de valores de parâmetros, a validação cruzada é executada por e com base na métrica de erro que você definir. Após a avaliação e a comparação, você pode escolher o modelo com melhor desempenho.

A imagem a seguir ilustra como isso pode ser obtido no Azure Machine Learning Studio (clássico).

![Localizar o melhor conjunto de parâmetros](./media/algorithm-parameters-optimize/fig1.png)

## <a name="define-the-parameter-space"></a>Definir o espaço de parâmetro
Você pode configurar o parâmetro definido na etapa de inicialização de modelo. O painel de parâmetro de todos os algoritmos de aprendizado de máquina tem dois modos de instrutor: *Parâmetro Único* e *Intervalo de Parâmetros*. Escolha o modo de Intervalo de Parâmetros. No modo de intervalo de parâmetros, você pode inserir vários valores para cada parâmetro. Você pode inserir valores separados por vírgula na caixa de texto.

![Árvore de decisão aumentada de duas classes, parâmetro único](./media/algorithm-parameters-optimize/fig2.png)

 Como alternativa, você pode definir os pontos mínimo e máximo da grade e o número total de pontos a serem gerados com **Usar Criador de Intervalo**. Por padrão, os valores de parâmetro são gerados em uma escala linear. Porém, se a opção **Escala Logarítmica** estiver marcada, os valores serão gerados em escala logarítmica (ou seja, a taxa dos pontos adjacentes será constante em vez da diferença). Para parâmetros de inteiros, você pode definir um intervalo usando um hífen. Por exemplo, "1-10" significa que todos os inteiros entre 1 e 10 (ambos inclusivos) formam o conjunto de parâmetros. Um modo misto também tem suporte. Por exemplo, o conjunto de parâmetros "1-10, 20, 50" incluiria inteiros 1-10, 20 e 50.

![Árvore de decisão aumentada de duas classes, intervalo de parâmetros](./media/algorithm-parameters-optimize/fig3.png)

## <a name="define-cross-validation-folds"></a>Definir dobras de validação cruzada
O módulo [Partição e Exemplo][partition-and-sample] pode ser usado para atribuir dobras aos dados aleatoriamente. No exemplo de configuração a seguir para o módulo, definimos cinco dobras e atribuímos aleatoriamente um número de dobras para as instâncias de amostra.

![Partição e exemplo](./media/algorithm-parameters-optimize/fig4.png)

## <a name="define-the-metric"></a>Definir a métrica
O módulo [Ajustar Hiperparâmetros do Modelo][tune-model-hyperparameters] dá suporte à escolha empírica do melhor conjunto de parâmetros para um determinado algoritmo e conjunto de dados. Além de outras informações sobre o treinamento do modelo, o painel **Propriedades** deste módulo inclui a métrica para determinar o melhor conjunto de parâmetros. Ele tem duas caixas de listagem suspensas diferentes para algoritmos de classificação e regressão, respectivamente. Se o algoritmo em questão for de classificação, a métrica de regressão será ignorada e vice-versa. Neste exemplo específico, a métrica é **Precisão**.   

![Parâmetros de limpeza](./media/algorithm-parameters-optimize/fig5.png)

## <a name="train-evaluate-and-compare"></a>Treinar, avaliar e comparar
O mesmo módulo [Ajustar Hiperparâmetros de Modelo][tune-model-hyperparameters] treina todos os modelos que correspondem ao conjunto de parâmetros, avalia diversas métricas e cria o modelo com melhor treinamento com base na métrica que você escolher. Este módulo tem duas entradas obrigatórias:

* O aprendiz não treinado
* O conjunto de dados

O módulo também tem uma entrada de conjunto de dados opcional. Conecte o conjunto de dados com informações de dobra à entrada obrigatória do conjunto de dados. Se o conjunto de dados não for atribuído às informações de dobra, uma validação cruzada de 10 dobras será executada automaticamente por padrão. Se a atribuição de dobra não for feita e um conjunto de dados de validação for indicado na porta opcional do conjunto de dados, um modo de treinamento de teste escolhido e o primeiro conjunto de dados serão usados para treinar o modelo em cada combinação de parâmetros.

![Classificador de árvore de decisão aumentada](./media/algorithm-parameters-optimize/fig6a.png)

Então, o modelo é avaliado no conjunto de dados de validação. A porta de saída à esquerda do módulo mostra métricas diferentes como funções de valores de parâmetro. A porta de saída à direita fornece o modelo treinado que corresponde ao modelo de melhor desempenho, de acordo com a métrica escolhida (**Precisão**, nesse caso).  

![Conjunto de dados de validação](./media/algorithm-parameters-optimize/fig6b.png)

Você pode ver os parâmetros exatos escolhidos visualizando a porta de saída à direita. Esse modelo pode ser usado na pontuação de um conjunto de teste ou em um serviço Web operacionalizado depois de salvá-lo como um modelo treinado.

<!-- Module References -->
[partition-and-sample]: /azure/machine-learning/studio-module-reference/partition-and-sample
[tune-model-hyperparameters]: /azure/machine-learning/studio-module-reference/tune-model-hyperparameters