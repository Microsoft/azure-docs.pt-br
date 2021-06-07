---
title: 'Regressão de rede neural: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo de regressão de rede neural no Azure Machine Learning para criar um modelo de regressão usando um algoritmo de rede neural personalizável.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: 403576454615effeb53651b51679681422b08e9e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "90890358"
---
# <a name="neural-network-regression-module"></a>Módulo de regressão de rede neural

*Cria um modelo de regressão usando um algoritmo de rede neural*  
  
 Categoria: Machine Learning/inicializar modelo/regressão
  
## <a name="module-overview"></a>Visão geral do módulo  

Este artigo descreve um módulo no designer de Azure Machine Learning.

Use este módulo para criar um modelo de regressão usando um algoritmo de rede neural personalizável.
  
 Embora as redes neurais sejam amplamente conhecidas para uso em problemas complexos de aprendizado e modelagem, como reconhecimento de imagem, elas são facilmente adaptadas para problemas de regressão. Qualquer classe de modelos estatísticos pode ser chamada de rede neural se ela usar pesos adaptáveis e puder aproximar funções não lineares de suas entradas. Assim, a regressão de rede neural é adequada para onde um modelo de regressão mais tradicional não tiver uma solução.
  
 A regressão de rede neural é um método de aprendizado supervisionado e, portanto, requer um conjunto de informações *marcado*, que inclui uma coluna de rótulo. Como um modelo de regressão prevê um valor numérico, a coluna de rótulo deve ser um tipo de dado numérico.  
  
 Você pode treinar o modelo fornecendo o modelo e o conjunto de dados marcado como uma entrada para [treinar o modelo](./train-model.md). O modelo treinado pode então ser usado para prever valores para os novos exemplos de entrada.  
  
## <a name="configure-neural-network-regression"></a>Configurar a regressão de rede neural 

As redes neurais podem ser amplamente personalizadas. Esta seção descreve como criar um modelo usando dois métodos:
  
+ [Criar um modelo de rede neural usando a arquitetura padrão](#bkmk_DefaultArchitecture)  
  
    Se você aceitar a arquitetura de rede neural padrão, use o painel **Propriedades** para definir parâmetros que controlam o comportamento da rede neural, como o número de nós na camada oculta, a taxa de aprendizado e a normalização.

    Comece aqui se você for novo nas redes neurais. O módulo dá suporte a muitas personalizações, bem como ao ajuste de modelo, sem conhecimento profundo das redes neurais. 

+ Definir uma arquitetura personalizada para uma rede neural 

    Use esta opção se você quiser adicionar camadas ocultas extras ou personalizar totalmente a arquitetura de rede, suas conexões e funções de ativação.
    
    Essa opção é melhor se você já estiver um pouco familiarizado com redes neurais. Você usa a linguagem net # para definir a arquitetura de rede.  

##  <a name="create-a-neural-network-model-using-the-default-architecture"></a><a name="bkmk_DefaultArchitecture"></a> Criar um modelo de rede neural usando a arquitetura padrão

1.  Adicione o módulo de **regressão de rede neural** ao seu pipeline no designer. Você pode encontrar esse módulo em **Machine Learning**, **inicializar**, na categoria **regressão** . 
  
2. Indique como você deseja que o modelo seja treinado, definindo a opção **criar modo de instrutor** .  
  
    -   **Parâmetro único**: escolha esta opção se você já souber como deseja configurar o modelo.

    -   **Intervalo de parâmetros**: Selecione esta opção se você não tiver certeza dos melhores parâmetros e quiser executar uma limpeza de parâmetro. Selecione um intervalo de valores para iteração e os [hiperparâmetros de modelo de ajuste](tune-model-hyperparameters.md) iteram sobre todas as combinações possíveis das configurações que você forneceu para determinar os hiperparâmetros que produzem os resultados ideais.   

3.  Em **especificação de camada oculta**, selecione **caso totalmente conectado**. Essa opção cria um modelo usando a arquitetura de rede neural padrão, que, para um modelo de regressão de rede neural, tem estes atributos:  
  
    + A rede tem exatamente uma camada oculta.
    + A camada de saída é totalmente conectada à camada oculta e a camada oculta é totalmente conectada à camada de entrada.
    + O número de nós na camada oculta pode ser definido pelo usuário (o valor padrão é 100).  
  
    Como o número de nós na camada de entrada é determinado pelo número de recursos nos dados de treinamento, em um modelo de regressão pode haver apenas um nó na camada de saída.  
  
4. Para **número de nós ocultos**, digite o número de nós ocultos. O padrão é uma camada oculta com nós 100. (Essa opção não estará disponível se você definir uma arquitetura personalizada usando net #.)
  
5.  Para **taxa de aprendizagem**, digite um valor que defina a etapa realizada em cada iteração, antes da correção. Um valor maior para a taxa de aprendizagem pode fazer o modelo convergir mais rapidamente, mas com o risco mínimo de errar o alvo verdadeiro.

6.  Para **número de iterações de aprendizado**, especifique o número máximo de vezes que o algoritmo processa os casos de treinamento.


8.  Para **a dinâmica**, digite um valor a ser aplicado durante o aprendizado como um peso em nós de iterações anteriores.

10. Selecione a opção, **embaralhar exemplos**, para alterar a ordem dos casos entre as iterações. Se você desmarcar essa opção, os casos serão processados exatamente na mesma ordem toda vez que você executar o pipeline.
  
11. Para a **semente de número aleatório**, você pode, opcionalmente, digitar um valor a ser usado como a semente. A especificação de um valor de semente é útil quando você deseja garantir a capacidade de repetição entre execuções do mesmo pipeline.
  
13. Conecte um conjunto de e treine o modelo:

    + Se você definir **criar modo de instrutor** como um **único parâmetro**, conecte um conjunto de um DataSet marcado e o módulo [treinar modelo](train-model.md) .  
  
    + Se você definir **criar modo de instrutor** como **intervalo de parâmetros**, conecte um conjunto de um DataSet marcado e treine o modelo usando [ajustar hiperparâmetros de modelo](tune-model-hyperparameters.md).  
  
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

- Para salvar um instantâneo do modelo treinado, selecione a guia **saídas** no painel direito do módulo modelo de **treinamento** . Selecione o ícone **registrar conjunto de registros** para salvar o modelo como um módulo reutilizável.

## <a name="next-steps"></a>Próximas etapas

Confira o [conjunto de módulos disponíveis](module-reference.md) no Azure Machine Learning. 