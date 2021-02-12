---
title: 'Two-Class rede neural: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo de rede neural Two-Class no Azure Machine Learning para criar um classificador binário.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: 9131a2439facef00cae818bffef38e536a40a2fd
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421151"
---
# <a name="two-class-neural-network-module"></a>Two-Class módulo de rede neural

Este artigo descreve um módulo no designer de Azure Machine Learning.

Use este módulo para criar um modelo de rede neural que possa ser usado para prever um destino que tenha somente dois valores.

A classificação usando redes neurais é um método de aprendizado supervisionado e, portanto, requer um conjunto de informações *marcado* , que inclui uma coluna de rótulo. Por exemplo, você pode usar esse modelo de rede neural para prever resultados binários, como se um paciente tem ou não uma determinada doença ou se um computador provavelmente falhará dentro de uma janela de tempo especificada.  

Depois de definir o modelo, treine-o fornecendo um conjunto de dados marcado e o modelo como uma entrada para o [modelo de treinamento](./train-model.md). O modelo treinado pode então ser usado para prever valores para novas entradas.

### <a name="more-about-neural-networks"></a>Mais sobre redes neurais

Uma rede neural é um conjunto de camadas interconectadas. As entradas são a primeira camada e são conectadas a uma camada de saída por um grafo acíclico composto por bordas e nós ponderados.

Entre as camadas de entrada e saída, você pode inserir várias camadas ocultas. Mais tarefas de previsão podem ser feitas facilmente com apenas uma ou algumas camadas ocultas. No entanto, pesquisas recentes mostraram que as redes neurais profundas (DNN) com muitas camadas podem ser eficazes em tarefas complexas, como reconhecimento de imagem ou de fala. As camadas sucessivas são usadas para modelar níveis crescentes de profundidade semântica.

A relação entre entradas e saídas é aprendida de treinar a rede neural nos dados de entrada. A direção do grafo prossegue das entradas por meio da camada oculta e da camada de saída. Todos os nós em uma camada são conectados pelas bordas ponderadas aos nós na próxima camada.

Para computar a saída da rede para uma entrada específica, um valor é calculado em cada nó nas camadas ocultas e na camada de saída. O valor é definido calculando a soma ponderada dos valores dos nós da camada anterior. Uma função de ativação é então aplicada a essa soma ponderada.
  
## <a name="how-to-configure"></a>Como configurar

1.  Adicione o módulo **rede neural de duas classes** ao seu pipeline. Você pode encontrar esse módulo em **Machine Learning** , **inicializar** , na categoria **classificação** .  
  
2.  Especifique como você deseja que o modelo seja treinado, definindo a opção **criar modo de instrutor** .  
  
    -   **Parâmetro único** : escolha esta opção se você já souber como deseja configurar o modelo.

    -   **Intervalo de parâmetros** : se você não tiver certeza dos melhores parâmetros, poderá encontrar os parâmetros ideais usando o módulo [ajustar hiperparâmetros de modelo](tune-model-hyperparameters.md) . Você fornece algum intervalo de valores, e o instrutor itera em várias combinações de configurações para determinar a combinação de valores que produz o melhor resultado.  

3.  Para **especificação de camada oculta** , selecione o tipo de arquitetura de rede a ser criado.  
  
    -   **Caso totalmente conectado** : usa a arquitetura de rede neural padrão, definida para redes neurais de duas classes, da seguinte maneira:
  
        -   Tem uma camada oculta.
  
        -   A camada de saída é totalmente conectada à camada oculta e a camada oculta é totalmente conectada à camada de entrada.
  
        -   O número de nós na camada de entrada é igual ao número de recursos nos dados de treinamento.
  
        -   O número de nós na camada oculta é definido pelo usuário. O valor padrão é 100.
  
        -   O número de nós é igual ao número de classes. Para uma rede neural de duas classes, isso significa que todas as entradas devem ser mapeadas para um dos dois nós na camada de saída.

5.  Para **taxa de aprendizagem** , defina o tamanho da etapa realizada em cada iteração, antes da correção. Um valor maior para a taxa de aprendizagem pode fazer o modelo convergir mais rapidamente, mas com o risco mínimo de errar o alvo verdadeiro.

6.  Para **número de iterações de aprendizado** , especifique o número máximo de vezes que o algoritmo deve processar os casos de treinamento.

7.  Para **o diâmetro inicial dos pesos de aprendizado** , especifique os pesos do nó no início do processo de aprendizado.

8.  Para **a dinâmica** , especifique um peso a ser aplicado durante o aprendizado a nós de iterações anteriores  

10. Selecione a opção **exemplos de ordem aleatória** para embaralhar casos entre iterações. Se você desmarcar essa opção, os casos serão processados exatamente na mesma ordem toda vez que você executar o pipeline.
  
11. Para **semente de número aleatório** , digite um valor a ser usado como a semente.
  
     A especificação de um valor de semente é útil quando você deseja garantir a capacidade de repetição entre execuções do mesmo pipeline.  Caso contrário, um valor de relógio do sistema será usado como a semente, o que pode causar resultados ligeiramente diferentes cada vez que você executar o pipeline.
  
13. Adicione um DataSet rotulado ao pipeline e treine o modelo:

    + Se você definir **criar modo de instrutor** como um **único parâmetro** , conecte um conjunto de um DataSet marcado e o módulo [treinar modelo](train-model.md) .  
  
    + Se você definir **criar modo de instrutor** como **intervalo de parâmetros** , conecte um conjunto de um DataSet marcado e treine o modelo usando [ajustar hiperparâmetros de modelo](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Se você passar um intervalo de parâmetros para [treinar o modelo](train-model.md), ele usará apenas o valor padrão na lista de parâmetros únicos.  
    > 
    > Se você passar um único conjunto de valores de parâmetro para o módulo [ajustar hiperparâmetros de modelo](tune-model-hyperparameters.md) , quando ele esperar um intervalo de configurações para cada parâmetro, ele ignorará os valores e usará os valores padrão para o aprendiz.  
    > 
    > Se você selecionar a opção **intervalo de parâmetros** e inserir um único valor para qualquer parâmetro, esse valor único especificado será usado em toda a varredura, mesmo que outros parâmetros sejam alterados em um intervalo de valores.  
  
14. Enviar o pipeline.

## <a name="results"></a>Resultados

Após a conclusão do treinamento:

+ Para salvar um instantâneo do modelo treinado, selecione a guia **saídas** no painel direito do módulo modelo de **treinamento** . Selecione o ícone **registrar conjunto de registros** para salvar o modelo como um módulo reutilizável.

+ Para usar o modelo de pontuação, adicione o módulo **modelo de Pontuação** a um pipeline.


## <a name="next-steps"></a>Próximas etapas

Confira o [conjunto de módulos disponíveis](module-reference.md) no Azure Machine Learning. 
