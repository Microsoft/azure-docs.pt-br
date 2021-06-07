---
title: 'Two-Class computador de vetor de suporte: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo máquina de vetor de suporte do Two-Class no Azure Machine Learning para criar um classificador binário.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: 46cfdd319fc89e569d165dc2e11303e67c6dd54e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93420556"
---
# <a name="two-class-support-vector-machine-module"></a>Módulo de máquina de vetor de suporte do Two-Class

Este artigo descreve um módulo no designer de Azure Machine Learning.

Use este módulo para criar um modelo baseado no algoritmo máquina de vetor de suporte. 

As máquinas de vetor de suporte (SVMs) são uma classe bem pesquisada de métodos de aprendizado supervisionados. Essa implementação específica é adequada à previsão de dois resultados possíveis, com base em variáveis contínuas ou categóricas.

Depois de definir os parâmetros do modelo, treine o modelo usando os módulos de treinamento e fornecendo um conjunto de um *DataSet marcado* que inclui um rótulo ou uma coluna de resultado.

## <a name="about-support-vector-machines"></a>Sobre as máquinas de vetor de suporte

O suporte às máquinas de vetor estão entre os mais recentes dos algoritmos de aprendizado da máquina e modelos SVM e tem sido usado em muitos aplicativos, da recuperação de informações até a classificação de texto e imagem. SVMs pode ser usado para tarefas de classificação e regressão.

Esse modelo de SVM é um modelo de aprendizado supervisionado que requer dados rotulados. No processo de treinamento, o algoritmo analisa dados de entrada e reconhece padrões em um espaço de recurso multidimensional chamado de *hiperplano*.  Todos os exemplos de entrada são representados como pontos neste espaço e são mapeados para categorias de saída de tal forma que as categorias são divididas por uma grande e limpam uma lacuna possível.

Para previsão, o algoritmo SVM atribui novos exemplos em uma categoria ou na outra, mapeando-os para esse mesmo espaço. 

## <a name="how-to-configure"></a>Como configurar 

Para esse tipo de modelo, é recomendável que você Normalize o conjunto de os antes de usá-lo para treinar o classificador.
  
1.  Adicione o módulo **máquina de vetor de suporte de duas classes** ao seu pipeline.  
  
2.  Especifique como você deseja que o modelo seja treinado, definindo a opção **criar modo de instrutor** .  
  
    -   **Parâmetro único**: se você souber como deseja configurar o modelo, poderá fornecer um conjunto específico de valores como argumentos.  

    -   **Intervalo de parâmetros**: se você não tiver certeza dos melhores parâmetros, poderá encontrar os parâmetros ideais usando o módulo [ajustar hiperparâmetros de modelo](tune-model-hyperparameters.md) . Você fornece algum intervalo de valores, e o instrutor itera em várias combinações de configurações para determinar a combinação de valores que produz o melhor resultado.

3.  Para **número de iterações**, digite um número que denota o número de iterações usadas durante a criação do modelo.  
  
     Este parâmetro pode ser usado para controlar o compromisso entre a precisão e a velocidade de treinamento.  
  
4.  Para **lambda**, digite um valor a ser usado como o peso para regularização L1.  
  
     Esse coeficiente de regularização pode ser usado para ajustar o modelo. Valores maiores penalizam modelos mais complexos.  
  
5.  Selecione a opção **normalizar recursos**, se desejar normalizar os recursos antes do treinamento.
  
     Se você aplicar a normalização, antes do treinamento, os pontos de dados serão centralizados na média e dimensionados para ter uma unidade de desvio padrão.
  
6.  Selecione a opção, **projeto para a esfera de unidade**, para normalizar coeficientes.
  
     A projeção de valores para o espaço de unidade significa que, antes do treinamento, os pontos de dados são centralizados em 0 e dimensionados para ter uma unidade de desvio padrão.
  
7.  Em **semente de número aleatório**, digite um valor inteiro para usar como semente se você quiser garantir reprodução em execuções.  Caso contrário, um valor de relógio do sistema será usado como uma semente, o que pode resultar em resultados ligeiramente diferentes entre as execuções.
  
9. Conecte um DataSet rotulado e treine o modelo:

    + Se você definir **criar modo de instrutor** como um **único parâmetro**, conecte um conjunto de um DataSet marcado e o módulo [treinar modelo](train-model.md) .  
  
    + Se você definir **criar modo de instrutor** como **intervalo de parâmetros**, conecte um conjunto de um DataSet marcado e treine o modelo usando [ajustar hiperparâmetros de modelo](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Se você passar um intervalo de parâmetros para [treinar o modelo](train-model.md), ele usará apenas o valor padrão na lista de parâmetros únicos.  
    > 
    > Se você passar um único conjunto de valores de parâmetro para o módulo [ajustar hiperparâmetros de modelo](tune-model-hyperparameters.md) , quando ele esperar um intervalo de configurações para cada parâmetro, ele ignorará os valores e usará os valores padrão para o aprendiz.  
    > 
    > Se você selecionar a opção **intervalo de parâmetros** e inserir um único valor para qualquer parâmetro, esse valor único especificado será usado em toda a varredura, mesmo que outros parâmetros sejam alterados em um intervalo de valores.
  
10. Enviar o pipeline.

## <a name="results"></a>Resultados

Após a conclusão do treinamento:

+ Para salvar um instantâneo do modelo treinado, selecione a guia **saídas** no painel direito do módulo modelo de **treinamento** . Selecione o ícone **registrar conjunto de registros** para salvar o modelo como um módulo reutilizável.

+ Para usar o modelo de pontuação, adicione o módulo **modelo de Pontuação** a um pipeline.


## <a name="next-steps"></a>Próximas etapas

Confira o [conjunto de módulos disponíveis](module-reference.md) no Azure Machine Learning. 