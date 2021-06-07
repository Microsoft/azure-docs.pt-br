---
title: 'Modelo de treinamento: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo **treinar modelo** em Azure Machine Learning para treinar um modelo de classificação ou regressão.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/10/2021
ms.openlocfilehash: 41b327e793c6b35a2b2aeae825c493a484d84fb4
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105562759"
---
# <a name="train-model-module"></a>Módulo Treinar Modelo

Este artigo descreve um módulo no designer de Azure Machine Learning.

Use este módulo para treinar um modelo de classificação ou regressão. O treinamento ocorre depois que você define um modelo e define seus parâmetros e requer dados marcados. Você também pode usar **treinar modelo** para treinar novamente um modelo existente com novos dados. 

## <a name="how-the-training-process-works"></a>Como funciona o processo de treinamento

Em Azure Machine Learning, criar e usar um modelo de aprendizado de máquina normalmente é um processo de três etapas. 

1. Você configura um modelo, escolhendo um tipo específico de algoritmo e definindo seus parâmetros ou hiperparâmetros. Escolha qualquer um dos seguintes tipos de modelo: 

    + Modelos de **classificação** , com base em redes neurais, árvores de decisão e florestas de decisão e outros algoritmos.
    + Modelos de **regressão** , que podem incluir regressão linear padrão ou que usam outros algoritmos, incluindo redes neurais e regressão bayesiana.  

2. Forneça um conjunto de dados que seja rotulado e que tenha um dado compatível com o algoritmo. Conecte os dados e o modelo para **treinar o modelo**.

    O que o treinamento produz é um formato binário específico, o iLearner, que encapsula os padrões estatísticos aprendidos dos dados. Você não pode modificar ou ler este formato diretamente; no entanto, outros módulos podem usar esse modelo treinado. 
    
    Você também pode exibir as propriedades do modelo. Para obter mais informações, consulte a seção resultados.

3. Após a conclusão do treinamento, use o modelo treinado com um dos [módulos de Pontuação](./score-model.md)para fazer previsões sobre novos dados.

## <a name="how-to-use-train-model"></a>Como usar o modelo de treinamento 
    
1. Adicione o módulo **modelo de treinamento** ao pipeline.  Você pode encontrar esse módulo na categoria **Machine Learning** . Expanda **treinar** e arraste o módulo **modelo de treinamento** para o pipeline.
  
1.  Na entrada à esquerda, anexe o modo não treinado. Anexe o conjunto de dados de treinamento à entrada à direita do **modelo de treinamento**.

    O conjunto de os de treinamento deve conter uma coluna de rótulo. Qualquer linha sem rótulos é ignorada.
  
1.  Para **coluna de rótulo**, clique em **Editar coluna** no painel direito do módulo e escolha uma única coluna que contenha resultados que o modelo pode usar para treinamento.
  
    - Para problemas de classificação, a coluna de rótulo deve conter valores **categóricos** ou valores **discretos** . Alguns exemplos podem ser uma classificação Sim/Não, um código ou nome de classificação de doença ou um grupo de renda.  Se você escolher uma coluna não categórica, o módulo retornará um erro durante o treinamento.
  
    -   Para problemas de regressão, a coluna de rótulo deve conter dados **numéricos** que representam a variável de resposta. O ideal é que os dados numéricos representem uma escala contínua. 
    
    Exemplos podem ser uma pontuação de risco de crédito, o tempo projetado para a falha de um disco rígido ou o número previsto de chamadas para um centro de atendimento em um determinado dia ou hora.  Se você não escolher uma coluna numérica, poderá receber um erro.
  
    -   Se você não especificar qual coluna de rótulo usar, o Aprendizado de Máquina do Azure tenta inferir qual é a coluna de rótulo apropriada, usando os metadados do conjunto de dados. Se escolher a coluna errada, use o seletor de coluna para corrigi-la.
  
    > [!TIP] 
    > Se você tiver problemas ao usar o seletor de coluna, consulte o artigo [selecionar colunas no conjunto de linhas](./select-columns-in-dataset.md) para obter dicas. Ele descreve alguns cenários comuns e dicas para usar as opções **with Rules** e **by Name** .
  
1.  Envie o pipeline. Se você tiver muitos dados, pode levar algum tempo.

    > [!IMPORTANT] 
    > Se você tiver uma coluna de ID que é a ID de cada linha, ou uma coluna de texto, que contém muitos valores exclusivos, o **modelo de treinamento** poderá atingir um erro como "número de valores exclusivos na coluna:" {column_name} "é maior que o permitido.
    >
    > Isso ocorre porque a coluna atingiu o limite de valores exclusivos e pode causar memória insuficiente. Você pode usar [Editar metadados](edit-metadata.md) para marcar essa coluna como **recurso claro** e ela não será usada no treinamento ou [extrair recursos de N-Gram do módulo de texto](extract-n-gram-features-from-text.md) para pré-processar coluna de texto. Consulte o [código de erro do designer](././designer-error-codes.md) para obter mais detalhes de erro.

## <a name="model-interpretability"></a>Interpretação de modelo

A interpretação de modelo fornece a possibilidade de compreender o modelo ML e apresentar a base subjacente para tomada de decisões de forma compreensível para os seres humanos.

Atualmente, o módulo **modelo de treinamento** oferece suporte ao [uso do pacote de interpretação para explicar os modelos de ml](../how-to-machine-learning-interpretability-aml.md#generate-feature-importance-values-via-remote-runs). Há suporte para os seguintes algoritmos internos:

- Regressão Linear
- Regressão de Rede Neural
- Regressão Logística de Duas Classes
- Computador de vetor de suporte de duas classes
- Floresta de decisão de várias classes

Para gerar explicações de modelo, você pode selecionar **true** na lista suspensa de **explicações de modelo** no módulo modelo de treinamento. Por padrão, ele é definido como false no módulo **modelo de treinamento** . Observe que a geração de explicações exige custo extra de computação.

![Captura de tela mostrando a caixa de seleção explicação do modelo](./media/module/train-model-explanation-checkbox.png)

Após a conclusão da execução do pipeline, você pode visitar a guia **explicações** no painel direito do módulo **modelo de treinamento** e explorar o desempenho do modelo, o conjunto de recursos e a importância do recurso.

![Captura de tela mostrando gráficos de explicação do modelo](./media/module/train-model-explanations-tab.gif)

Para saber mais sobre como usar explicações de modelo em Azure Machine Learning, consulte o artigo de instruções sobre [interpretar modelos de ml](../how-to-machine-learning-interpretability-aml.md#generate-feature-importance-values-via-remote-runs).

## <a name="results"></a>Resultados

Depois que o modelo for treinado:


+ Para usar o modelo em outros pipelines, selecione o módulo e selecione o ícone **registrar conjunto de registros** na guia **saídas** no painel direito. Você pode acessar modelos salvos na paleta de módulo em **conjuntos** de os.

+ Para usar o modelo na previsão de novos valores, conecte-o ao módulo [modelo de Pontuação](./score-model.md) , junto com os novos dados de entrada.


## <a name="next-steps"></a>Próximas etapas

Confira o [conjunto de módulos disponíveis](module-reference.md) no Azure Machine Learning.