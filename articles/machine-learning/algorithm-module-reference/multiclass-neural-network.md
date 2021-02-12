---
title: 'Rede neural multiclasse: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo rede neural multiclasse no Azure Machine Learning designer para prever um destino que tenha valores de várias classes.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: a4f7f8a7793f31ffbf2099cbfb314fc5097319f5
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421253"
---
# <a name="multiclass-neural-network-module"></a>Módulo de rede neural multiclasse

Este artigo descreve um módulo no designer de Azure Machine Learning.

Use este módulo para criar um modelo de rede neural que possa ser usado para prever um destino que tenha vários valores. 

Por exemplo, redes neurais desse tipo podem ser usadas em tarefas complexas da pesquisa Visual computacional, como reconhecimento de dígitos ou letras, classificação de documentos e reconhecimento de padrões.

A classificação usando redes neurais é um método de aprendizado supervisionado e, portanto, requer um conjunto de informações *marcado* que inclui uma coluna de rótulo.

Você pode treinar o modelo fornecendo o modelo e o conjunto de dados marcado como uma entrada para [treinar o modelo](./train-model.md). O modelo treinado pode então ser usado para prever valores para os novos exemplos de entrada.  

## <a name="about-neural-networks"></a>Sobre redes neurais

Uma rede neural é um conjunto de camadas interconectadas. As entradas são a primeira camada e são conectadas a uma camada de saída por um grafo acíclico composto por bordas e nós ponderados.

Entre as camadas de entrada e saída, você pode inserir várias camadas ocultas. Mais tarefas de previsão podem ser feitas facilmente com apenas uma ou algumas camadas ocultas. No entanto, pesquisas recentes mostraram que as redes neurais profundas (DNN) com muitas camadas podem ser eficazes em tarefas complexas, como reconhecimento de imagem ou de fala. As camadas sucessivas são usadas para modelar níveis crescentes de profundidade semântica.

A relação entre entradas e saídas é aprendida de treinar a rede neural nos dados de entrada. A direção do grafo prossegue das entradas por meio da camada oculta e da camada de saída. Todos os nós em uma camada são conectados pelas bordas ponderadas aos nós na próxima camada.

Para computar a saída da rede para uma entrada específica, um valor é calculado em cada nó nas camadas ocultas e na camada de saída. O valor é definido calculando a soma ponderada dos valores dos nós da camada anterior. Uma função de ativação é então aplicada a essa soma ponderada.

## <a name="configure-multiclass-neural-network"></a>Configurar rede neural multiclasse

1. Adicione o módulo **rede neural multiclasse** ao seu pipeline no designer. Você pode encontrar esse módulo em **Machine Learning** , **inicializar** , na categoria **classificação** .

2. **Criar modo de instrutor** : Use esta opção para especificar como deseja que o modelo seja treinado:

    - **Parâmetro único** : escolha esta opção se você já souber como deseja configurar o modelo.

    - **Intervalo de parâmetros** : Selecione esta opção se você não tiver certeza dos melhores parâmetros e quiser executar uma limpeza de parâmetro. Selecione um intervalo de valores para iteração e os [hiperparâmetros de modelo de ajuste](tune-model-hyperparameters.md) iteram sobre todas as combinações possíveis das configurações que você forneceu para determinar os hiperparâmetros que produzem os resultados ideais.  

3. **Especificação de camada oculta** : selecione o tipo de arquitetura de rede a ser criado.

    - **Caso totalmente conectado** : Selecione esta opção para criar um modelo usando a arquitetura de rede neural padrão. Para modelos de rede neural multiclasse, os padrões são os seguintes:

        - Uma camada oculta
        - A camada de saída é totalmente conectada à camada oculta.
        - A camada oculta é totalmente conectada à camada de entrada.
        - O número de nós na camada de entrada é determinado pelo número de recursos nos dados de treinamento.
        - O número de nós na camada oculta pode ser definido pelo usuário. O padrão é 100.
        - O número de nós na camada de saída depende do número de classes.
  
   

5. **Número de nós ocultos** : essa opção permite que você personalize o número de nós ocultos na arquitetura padrão. Digite o número de nós ocultos. O padrão é uma camada oculta com nós 100.

6. **A taxa de aprendizagem** : defina o tamanho da etapa realizada em cada iteração, antes da correção. Um valor maior para a taxa de aprendizagem pode fazer com que o modelo seja convergido mais rapidamente, mas pode Sobreusar mínimo locais.

7. **Número de iterações de aprendizado** : especifique o número máximo de vezes que o algoritmo deve processar os casos de treinamento.

8. **O diâmetro inicial dos pesos de aprendizado** : Especifique os pesos do nó no início do processo de aprendizado.

9. **O impulso** : especifique um peso a ser aplicado durante o aprendizado a nós de iterações anteriores.
  
11. **Exemplos de ordem aleatória** : Selecione esta opção para embaralhar casos entre iterações.

    Se você desmarcar essa opção, os casos serão processados exatamente na mesma ordem toda vez que você executar o pipeline.

12. **Semente de número aleatório** : digite um valor a ser usado como a semente, se você quiser garantir a capacidade de repetição entre as execuções do mesmo pipeline.

14. Treine o modelo:

    + Se você definir **criar modo de instrutor** como um **único parâmetro** , conecte um conjunto de um DataSet marcado e o módulo [treinar modelo](train-model.md) .  
  
    + Se você definir **criar modo de instrutor** como **intervalo de parâmetros** , conecte um conjunto de um DataSet marcado e treine o modelo usando [ajustar hiperparâmetros de modelo](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Se você passar um intervalo de parâmetros para [treinar o modelo](train-model.md), ele usará apenas o valor padrão na lista de parâmetros únicos.  
    > 
    > Se você passar um único conjunto de valores de parâmetro para o módulo [ajustar hiperparâmetros de modelo](tune-model-hyperparameters.md) , quando ele esperar um intervalo de configurações para cada parâmetro, ele ignorará os valores e usará os valores padrão para o aprendiz.  
    > 
    > Se você selecionar a opção **intervalo de parâmetros** e inserir um único valor para qualquer parâmetro, esse valor único especificado será usado em toda a varredura, mesmo que outros parâmetros sejam alterados em um intervalo de valores.  
  

## <a name="results"></a>Resultados

Após a conclusão do treinamento:

- Para salvar um instantâneo do modelo treinado, selecione a guia **saídas** no painel direito do módulo modelo de **treinamento** . Selecione o ícone **registrar conjunto de registros** para salvar o modelo como um módulo reutilizável.

## <a name="next-steps"></a>Próximas etapas

Confira o [conjunto de módulos disponíveis](module-reference.md) no Azure Machine Learning. 