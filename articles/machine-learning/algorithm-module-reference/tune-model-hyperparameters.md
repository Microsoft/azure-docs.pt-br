---
title: Ajustar os hiperparâmetros de modelo
titleSuffix: Azure Machine Learning
description: Use o módulo ajustar hiperparâmetros de modelo no designer para executar uma limpeza de parâmetro para ajustar hiperparâmetros.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2020
ms.openlocfilehash: 2bbf75ba5de4ad20e11261bdcfd1204b1a0b0766
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93420624"
---
# <a name="tune-model-hyperparameters"></a>Ajustar os hiperparâmetros de modelo

Este artigo descreve como usar o módulo ajustar hiperparâmetros de modelo no designer de Azure Machine Learning. O objetivo é determinar os hiperparâmetros ideais para um modelo de aprendizado de máquina. O módulo cria e testa vários modelos usando diferentes combinações de configurações. Ele compara métricas em todos os modelos para obter as combinações de configurações. 

O *parâmetro* de termos e *hiperparâmetro* podem ser confusos. Os *parâmetros* do modelo são o que você definiu no painel direito do módulo. Basicamente, esse módulo executa uma *varredura de parâmetro* sobre as configurações de parâmetro especificadas. Ele aprende um conjunto ideal de _hiperparâmetros_ , que podem ser diferentes para cada árvore de decisão, conjunto de informações ou método de regressão específico. O processo de encontrar a configuração ideal às vezes é chamado de *ajuste*. 

O módulo dá suporte ao seguinte método para localizar as configurações ideais para um modelo: *treinamento e ajuste integrados.* Nesse método, você configura um conjunto de parâmetros a ser usado. Em seguida, permita que o módulo Itere em várias combinações. O módulo mede a precisão até encontrar um modelo "melhor". Com a maioria dos módulos do aprendiz, você pode escolher quais parâmetros devem ser alterados durante o processo de treinamento e quais devem permanecer fixos.

Dependendo de quanto tempo você deseja que o processo de ajuste seja executado, você pode decidir testar exaustivamente todas as combinações. Ou você pode reduzir o processo estabelecendo uma grade de combinações de parâmetros e testando um subconjunto aleatório da grade de parâmetros.

Esse método gera um modelo treinado que você pode salvar para reutilização.  

> [!TIP] 
> Você pode fazer uma tarefa relacionada. Antes de começar o ajuste, aplique a seleção de recursos para determinar as colunas ou variáveis que têm o maior valor de informações.

## <a name="how-to-configure-tune-model-hyperparameters"></a>Como configurar hiperparâmetros de modelo de ajuste  

Aprender os hiperparâmetros ideais para um modelo de aprendizado de máquina requer um uso considerável de pipelines.

### <a name="train-a-model-by-using-a-parameter-sweep"></a>Treinar um modelo usando uma varredura de parâmetro  

Esta seção descreve como executar uma varredura de parâmetro básica, que treina um modelo usando o módulo ajustar hiperparâmetros de modelo.

1.  Adicione o módulo ajustar hiperparâmetros de modelo ao seu pipeline no designer.

2.  Conecte um modelo não treinado à entrada mais à esquerda. 

    > [!NOTE] 
    > Os **hiperparâmetros de modelo de ajuste** só podem ser conectados a módulos de algoritmos de aprendizado de máquina internos e não podem dar suporte ao modelo personalizado criado em **criar modelo Python**.


3.  Adicione o conjunto de dados que você deseja usar para treinamento e conecte-o à entrada intermediária de hiperparâmetros de modelo de ajuste.  

    Opcionalmente, se você tiver um conjunto de dados marcado, você poderá conectá-lo à porta de entrada mais à direita (conjunto de informações de **validação opcional** ). Isso permite que você meça a precisão durante o treinamento e o ajuste.

4.  No painel direito de ajustar hiperparâmetros de modelo, escolha um valor para o **modo de limpeza de parâmetro**. Esta opção controla como os parâmetros são selecionados.

    - **Grade inteira** : quando você seleciona essa opção, o módulo executa um loop em uma grade predefinida pelo sistema, para tentar combinações diferentes e identificar o melhor aprendiz. Essa opção é útil quando você não sabe quais são as melhores configurações de parâmetro e deseja tentar todas as combinações de valores possíveis.

    - **Varredura aleatória** : quando você seleciona essa opção, o módulo seleciona os valores de parâmetro aleatoriamente em um intervalo definido pelo sistema. Você deve especificar o número máximo de execuções que deseja que o módulo execute. Essa opção é útil quando você deseja aumentar o desempenho do modelo usando as métricas de sua escolha, mas ainda conservar recursos de computação.    

5.  Para **coluna de rótulo** , abra o seletor de coluna para escolher uma única coluna de rótulo.

6.  Escolha o número de execuções:

    - **Número máximo de execuções em varredura aleatória** : se você escolher uma varredura aleatória, poderá especificar quantas vezes o modelo deve ser treinado, usando uma combinação aleatória de valores de parâmetro.

7.  Para **classificação** , escolha uma única métrica a ser usada para classificar os modelos.

    Quando você executa uma varredura de parâmetro, o módulo calcula todas as métricas aplicáveis para o tipo de modelo e as retorna no relatório de **resultados de varredura** . O módulo usa métricas separadas para modelos de regressão e classificação.

    No entanto, a métrica que você escolhe determina como os modelos são classificados. Somente o modelo superior, conforme classificado pela métrica escolhida, é a saída como um modelo treinado a ser usado para pontuação.

8.  Para **semente aleatória** , insira um número a ser usado para iniciar a limpeza de parâmetro. 

9. Envie o pipeline.

## <a name="results-of-hyperparameter-tuning"></a>Resultados do ajuste de hiperparâmetro

Quando o treinamento for concluído:

+ Para exibir os resultados da varredura, você pode clicar com o botão direito do mouse no módulo e selecionar **Visualizar** ou clicar com o botão direito do mouse na porta de saída à esquerda do módulo a ser visualizado.

    Os **resultados da varredura** incluem todas as métricas de limpeza e precisão do parâmetro que se aplicam ao tipo de modelo, e a métrica que você selecionou para classificação determina qual modelo é considerado "melhor".

+ Para salvar um instantâneo do modelo treinado, selecione a guia **saídas + logs** no painel direito do módulo modelo de **treinamento** . Selecione o ícone **registrar conjunto de registros** para salvar o modelo como um módulo reutilizável.


## <a name="technical-notes"></a>Observações técnicas

Esta seção contém detalhes e dicas de implementação.

### <a name="how-a-parameter-sweep-works"></a>Como funciona uma varredura de parâmetro

Ao configurar uma varredura de parâmetro, você define o escopo da pesquisa. A pesquisa pode usar um número finito de parâmetros selecionados aleatoriamente. Ou pode ser uma pesquisa exaustiva em um espaço de parâmetro que você definir.

+ **Varredura aleatória** : essa opção treina um modelo usando um número definido de iterações. 

  Você especifica um intervalo de valores para iteração e o módulo usa um subconjunto escolhido aleatoriamente desses valores. Os valores são escolhidos com substituição, o que significa que números anteriormente escolhidos em Random não são removidos do pool de números disponíveis. Portanto, a possibilidade de qualquer valor selecionado permanece a mesma em todos os passos.  

+ **Grade inteira** : a opção de usar a grade inteira significa que cada combinação é testada. Essa opção é a mais completa, mas requer mais tempo. 

### <a name="controlling-the-length-and-complexity-of-training"></a>Controlando o tamanho e a complexidade do treinamento

A iteração em várias combinações de configurações pode ser demorada, portanto, o módulo fornece várias maneiras de restringir o processo:

+ Limite o número de iterações usadas para testar um modelo.
+ Limite o espaço de parâmetro.
+ Limite o número de iterações e o espaço de parâmetro.

É recomendável que você pipeline com as configurações para determinar o método mais eficiente de treinamento em um determinado conjunto de dado e modelo.

### <a name="choosing-an-evaluation-metric"></a>Escolhendo uma métrica de avaliação

No final do teste, o modelo apresenta um relatório que contém a precisão de cada modelo para que você possa examinar os resultados da métrica:

- Um conjunto uniforme de métricas é usado para todos os modelos de classificação binária.
- A precisão é usada para todos os modelos de classificação de várias classes.
- Um conjunto diferente de métricas é usado para modelos de regressão. 

No entanto, durante o treinamento, você deve escolher uma *única* métrica a ser usada na classificação dos modelos gerados durante o processo de ajuste. Você pode achar que a melhor métrica varia, dependendo do problema de sua empresa e do custo de falsos positivos e falsos negativos.

#### <a name="metrics-used-for-binary-classification"></a>Métricas usadas para classificação binária

-   A **precisão** é a proporção de resultados verdadeiros em casos totais.  

-   **Precisão** é a proporção de resultados verdadeiros para resultados positivos.  

-   **Recall** é a fração de todos os resultados corretos em todos os resultados.  

-   A **Pontuação F** é uma medida que equilibra precisão e RECALL.  

-   **Auc** é um valor que representa a área sob a curva quando falsos positivos são plotados no eixo x e verdadeiros positivos são plotados no eixo y.  

-   A **perda de log média** é a diferença entre duas distribuições de probabilidade: a verdadeira e a do modelo.  

#### <a name="metrics-used-for-regression"></a>Métricas usadas para regressão

-   Média de erros **absolutos** calcula todos os erros no modelo, em que o *erro* significa a distância do valor previsto do valor verdadeiro. Geralmente é abreviado como *Mae*.  

-   A **raiz do erro de quadrado médio** mede a média dos quadrados dos erros e, em seguida, usa a raiz desse valor. Geralmente é abreviado como *RMSE*.  

-   **Erro absoluto relativo** representa o erro como uma porcentagem do valor verdadeiro.  

-   **Erro quadrado relativo** normaliza o erro quadrado total dividindo pelo erro quadrado total dos valores previstos.  

-   O **coeficiente de determinação** é um único número que indica quão bem os dados se ajustam a um modelo. Um valor de um significa que o modelo corresponde exatamente aos dados. Um valor de zero significa que os dados são aleatórios ou não podem ser ajustados para o modelo. Ele é frequentemente chamado de *r <sup>2</sup>* , *r <sup>2</sup>* ou *r-quadrado*.  

### <a name="modules-that-dont-support-a-parameter-sweep"></a>Módulos que não dão suporte a uma varredura de parâmetro

Quase todos os aprendizes no Azure Machine Learning dão suporte à validação cruzada com uma varredura de parâmetro integrada, que permite escolher os parâmetros com os quais o pipeline será escolhido. Se o aprendiz não der suporte à definição de um intervalo de valores, você ainda poderá usá-lo em validação cruzada. Nesse caso, um intervalo de valores permitidos é selecionado para a varredura. 


## <a name="next-steps"></a>Próximas etapas

Confira o [conjunto de módulos disponíveis](module-reference.md) no Azure Machine Learning. 

