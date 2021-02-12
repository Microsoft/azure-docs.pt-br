---
title: 'Regressão linear: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo regressão linear no Azure Machine Learning para criar um modelo de regressão linear para uso em um pipeline.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: 809b1be4f9f12e1963ff5caeaacd109c84db154f
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93323648"
---
# <a name="linear-regression-module"></a>Módulo de regressão linear
Este artigo descreve um módulo no designer de Azure Machine Learning.

Use este módulo para criar um modelo de regressão linear para uso em um pipeline.  A regressão linear tenta estabelecer uma relação linear entre uma ou mais variáveis independentes e um resultado numérico, ou variável dependente. 

Você usa esse módulo para definir um método de regressão linear e, em seguida, treinar um modelo usando um DataSet rotulado. O modelo treinado pode então ser usado para fazer previsões.

## <a name="about-linear-regression"></a>Sobre a regressão linear

A regressão linear é um método estatístico comum, que foi adotado no aprendizado de máquina e aprimorado com muitos métodos novos para ajustar o erro de linha e medição. Em síntese, regressão refere-se à previsão de um destino numérico. A regressão linear ainda é uma boa opção quando você deseja um modelo simples para uma tarefa de previsão básica. A regressão linear também tende a funcionar bem em conjuntos de dados esparsos e altamente dimensionais sem complexidade.

O Azure Machine Learning dá suporte a uma variedade de modelos de regressão, além da regressão linear. No entanto, o termo "regressão" pode ser interpretado livremente e alguns tipos de regressão fornecidos em outras ferramentas não têm suporte.

+ O problema de regressão clássica envolve uma única variável independente e uma variável dependente. Isso é chamado de *regressão simples*.  Este módulo dá suporte à regressão simples.

+ A *regressão linear múltipla* envolve duas ou mais variáveis independentes que contribuem para uma única variável dependente. Problemas nos quais várias entradas são usadas para prever um único resultado numérico também são chamados de *regressão linear multivariada*.

    O módulo **regressão linear** pode resolver esses problemas, como a maioria dos outros módulos de regressão.

+ A *regressão de vários rótulos* é a tarefa de prever várias variáveis dependentes em um único modelo. Por exemplo, na regressão logística de multi-rótulo, um exemplo pode ser atribuído para vários rótulos diferentes. (Isso é diferente da tarefa de prever vários níveis dentro de uma única variável de classe.)

    Não há suporte para esse tipo de regressão no Azure Machine Learning. Para prever várias variáveis, crie um aprendiz separado para cada saída que você deseja prever.

Há anos, estatísticos desenvolver métodos cada vez mais avançados para regressão. Isso é verdadeiro mesmo para regressão linear. Esse módulo dá suporte a dois métodos para medir o erro e se ajustar à linha de regressão: método de quadrados mínimos comum e descendente de gradiente.

- **Gradiente descendente** é um método que minimiza a quantidade de erros em cada etapa do processo de treinamento do modelo. Há muitas variações do espaço descendente do gradiente e a otimização para vários problemas de aprendizado tem sido muito estudada. Se você escolher essa opção para o **método de solução** , poderá definir uma variedade de parâmetros para controlar o tamanho da etapa, a taxa de aprendizagem e assim por diante. Essa opção também dá suporte ao uso de uma varredura de parâmetro integrada.

- Os **quadrados mínimos comuns** são uma das técnicas mais usadas na regressão linear. Por exemplo, os quadrados mínimos são o método usado nas ferramentas de análise para o Microsoft Excel.

    Os quadrados mínimos comuns se referem à função de perda, que calcula o erro como a soma do quadrado da distância entre o valor real e a linha prevista e se ajusta ao modelo, minimizando o erro ao quadrado. Esse método assume uma relação linear forte entre as entradas e a variável dependente.

## <a name="configure-linear-regression"></a>Configurar regressão linear

Esse módulo dá suporte a dois métodos para ajustar um modelo de regressão, com opções diferentes:

+ [Ajustar um modelo de regressão usando quadrados mínimos comuns](#create-a-regression-model-using-ordinary-least-squares)

    Para conjuntos de valores pequenos, é melhor selecionar quadrados mínimos comuns. Isso deve fornecer resultados semelhantes para o Excel.
    
+ [Criar um modelo de regressão usando o gradiente online descendente](#create-a-regression-model-using-online-gradient-descent)

    O espaço descendente do gradiente é uma função de perda melhor para modelos que são mais complexos, ou que têm muito poucos dados de treinamento, dado o número de variáveis.

### <a name="create-a-regression-model-using-ordinary-least-squares"></a>Criar um modelo de regressão usando quadrados mínimos comuns

1. Adicione o módulo **modelo de regressão linear** ao seu pipeline no designer.

    Você pode encontrar esse módulo na categoria **Machine Learning** . Expanda **inicializar modelo** , expanda **regressão** e arraste o módulo **modelo de regressão linear** para seu pipeline.

2. No painel **Propriedades** , na lista suspensa **método de solução** , selecione **quadrados mínimos comuns**. Esta opção especifica o método de computação usado para localizar a linha de regressão.

3. Em **peso de regularização L2** , digite o valor a ser usado como o peso para regularização L2. Recomendamos que você use um valor diferente de zero para evitar o superajuste.

     Para saber mais sobre como a regularização afeta o ajuste de modelo, consulte este artigo: [regularização de L1 e L2 para Machine Learning](/archive/msdn-magazine/2015/february/test-run-l1-and-l2-regularization-for-machine-learning)

4. Selecione a opção **incluir termo de intercepção** , se você quiser exibir o termo para a interceptação.

    Desmarque esta opção se você não precisar revisar a fórmula de regressão.

5. Para a **semente de número aleatório** , você pode, opcionalmente, digitar um valor para propagar o gerador de número aleatório usado pelo modelo.

    Usar um valor de semente será útil se você quiser manter os mesmos resultados entre diferentes execuções do mesmo pipeline. Caso contrário, o padrão é usar um valor do relógio do sistema.


7. Adicione o módulo [modelo de treinamento](./train-model.md) ao seu pipeline e conecte um DataSet rotulado.

8. Envie o pipeline.

### <a name="results-for-ordinary-least-squares-model"></a>Resultados para o modelo de quadrados mínimos comum

Após a conclusão do treinamento:


+ Para fazer previsões, conecte o modelo treinado ao módulo [modelo de Pontuação](./score-model.md) , juntamente com um conjunto de valores novos. 


### <a name="create-a-regression-model-using-online-gradient-descent"></a>Criar um modelo de regressão usando o gradiente online descendente

1. Adicione o módulo **modelo de regressão linear** ao seu pipeline no designer.

    Você pode encontrar esse módulo na categoria **Machine Learning** . Expanda o **modelo de inicialização** , expanda **regressão** e arraste o módulo **modelo de regressão linear** para seu pipeline

2. No painel **Propriedades** , na lista suspensa **método de solução** , escolha **descendente de gradiente online** como o método de computação usado para localizar a linha de regressão.

3. Para **criar modo de instrutor** , indique se você deseja treinar o modelo com um conjunto predefinido de parâmetros ou se deseja otimizar o modelo usando uma varredura de parâmetro.

    + **Parâmetro único** : se você souber como deseja configurar a rede de regressão linear, poderá fornecer um conjunto específico de valores como argumentos.
    
    + **Intervalo de parâmetros** : Selecione esta opção se você não tiver certeza dos melhores parâmetros e quiser executar uma limpeza de parâmetro. Selecione um intervalo de valores para iteração e os [hiperparâmetros de modelo de ajuste](tune-model-hyperparameters.md) iteram sobre todas as combinações possíveis das configurações que você forneceu para determinar os hiperparâmetros que produzem os resultados ideais.  

   
4. Para **taxa de aprendizagem** , especifique a taxa de aprendizado inicial para o otimizador de descendente de gradiente estocástico.

5. Para **número de épocas de treinamento** , digite um valor que indique quantas vezes o algoritmo deve iterar por meio de exemplos. Para conjuntos de dados com um pequeno número de exemplos, esse número deverá ser grande para alcançar convergência.

6. **Normalizar recursos** : se você já tiver normalizado os dados numéricos usados para treinar o modelo, você poderá desmarcar essa opção. Por padrão, o módulo normaliza todas as entradas numéricas para um intervalo entre 0 e 1.

    > [!NOTE]
    > 
    > Lembre-se de aplicar o mesmo método de normalização aos novos dados usados para pontuação.

7. Em **peso de regularização L2** , digite o valor a ser usado como o peso para regularização L2. Recomendamos que você use um valor diferente de zero para evitar o superajuste.

    Para saber mais sobre como a regularização afeta o ajuste de modelo, consulte este artigo: [regularização de L1 e L2 para Machine Learning](/archive/msdn-magazine/2015/february/test-run-l1-and-l2-regularization-for-machine-learning)


9. Selecione a opção **diminuir taxa de aprendizado** se quiser que a taxa de aprendizagem diminua conforme as iterações progredirem.  

10. Para a **semente de número aleatório** , você pode, opcionalmente, digitar um valor para propagar o gerador de número aleatório usado pelo modelo. Usar um valor de semente será útil se você quiser manter os mesmos resultados entre diferentes execuções do mesmo pipeline.


12. Treine o modelo:

    + Se você definir **criar modo de instrutor** como um **único parâmetro** , conecte um conjunto de um DataSet marcado e o módulo [treinar modelo](train-model.md) .  
  
    + Se você definir **criar modo de instrutor** como **intervalo de parâmetros** , conecte um conjunto de um DataSet marcado e treine o modelo usando [ajustar hiperparâmetros de modelo](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Se você passar um intervalo de parâmetros para [treinar o modelo](train-model.md), ele usará apenas o valor padrão na lista de parâmetros únicos.  
    > 
    > Se você passar um único conjunto de valores de parâmetro para o módulo [ajustar hiperparâmetros de modelo](tune-model-hyperparameters.md) , quando ele esperar um intervalo de configurações para cada parâmetro, ele ignorará os valores e usará os valores padrão para o aprendiz.  
    > 
    > Se você selecionar a opção **intervalo de parâmetros** e inserir um único valor para qualquer parâmetro, esse valor único especificado será usado em toda a varredura, mesmo que outros parâmetros sejam alterados em um intervalo de valores.

13. Envie o pipeline.

### <a name="results-for-online-gradient-descent"></a>Resultados para descendente de gradiente online

Após a conclusão do treinamento:

+ Para fazer previsões, conecte o modelo treinado ao módulo [modelo de Pontuação](./score-model.md) , junto com os novos dados de entrada.


## <a name="next-steps"></a>Próximas etapas

Confira o [conjunto de módulos disponíveis](module-reference.md) no Azure Machine Learning.