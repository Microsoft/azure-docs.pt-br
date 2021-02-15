---
title: Interpretação de modelo na Azure Machine Learning (versão prévia)
titleSuffix: Azure Machine Learning
description: Saiba como entender & explicar como o modelo de aprendizado de máquina faz previsões durante o treinamento & inferência usando o SDK do Azure Machine Learning Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, responsible-ml
ms.author: mithigpe
author: minthigpen
ms.reviewer: Luis.Quintanilla
ms.date: 11/16/2020
ms.openlocfilehash: 6784361dde67d7dcc1423d9edbcc92ec513ff6d4
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222625"
---
# <a name="model-interpretability-in-azure-machine-learning-preview"></a>Interpretação de modelo na Azure Machine Learning (versão prévia)


## <a name="overview-of-model-interpretability"></a>Visão geral da interpretação do modelo

A interpretação é fundamental para cientistas de dados, auditores e tomadores de decisões de negócios para garantir a conformidade com as políticas da empresa, padrões do setor e regulamentos governamentais:

+ Os cientistas de dados precisam da capacidade de explicar seus modelos a executivos e participantes, para que possam entender o valor e a precisão de suas descobertas. Eles também exigem a interpretação para depurar seus modelos e tomar decisões informadas sobre como aprimorá-los. 

+ Auditores legais exigem ferramentas para validar modelos em relação à conformidade regulatória e monitorar como as decisões de modelos estão afetando os seres humanos. 

+ Os tomadores de decisões de negócios precisam de tranqüilidade ao ter a capacidade de fornecer transparência para os usuários finais. Isso permite que eles ganhem e mantenham confiança.


A habilitação da capacidade de explicar um modelo de aprendizado de máquina é importante durante duas fases principais do desenvolvimento de modelos:
+ Durante a fase de treinamento, como designers de modelo e avaliadores podem usar a saída de interpretação de um modelo para verificar as mesmas e criar confiança com os participantes. Eles também usam as informações sobre o modelo para depuração, validando o comportamento do modelo corresponde aos seus objetivos e verificando a infração do modelo ou recursos insignificantes.

+ Durante a fase de inferência, como a transparência de modelos implantados permite que os executivos compreendam "quando implantados" como o modelo está funcionando e como suas decisões estão tratando e afetando as pessoas na vida real. 

## <a name="interpretability-with-azure-machine-learning"></a>Interpretabilidade com Azure Machine Learning

As classes de interpretação são disponibilizadas por meio do seguinte pacote SDK: (saiba como [instalar pacotes SDK para Azure Machine Learning](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py))

* `azureml.interpret`, contém funcionalidades com suporte da Microsoft.

Use `pip install azureml-interpret` para uso geral.

## <a name="how-to-interpret-your-model"></a>Como interpretar seu modelo

Usando as classes e os métodos no SDK, você pode:
+ Explique a previsão do modelo gerando valores de importância do recurso para todo o modelo e/ou pontos de extremidade individuais. 
+ Alcance a interpretação de modelo em conjuntos de valores do mundo real em escala, durante o treinamento e a inferência.
+ Use um painel de visualização interativa para descobrir padrões em dados e explicações no tempo de treinamento


No aprendizado de máquina, os **recursos** são os campos de dados usados para prever um ponto de dados de destino. Por exemplo, para prever o risco de crédito, os campos de dados para idade, tamanho da conta e idade da conta podem ser usados. Nesse caso, a idade, o tamanho da conta e a idade da conta são **recursos**. A importância do recurso informa como cada campo de dados afetou as previsões do modelo. Por exemplo, a idade pode ser usada intensamente na previsão, enquanto o tamanho da conta e a idade não afetam significativamente os valores de previsão. Esse processo permite que os cientistas de dados expliquem previsões resultantes, para que os participantes tenham visibilidade sobre quais recursos são mais importantes no modelo.

Saiba mais sobre técnicas de interpretação com suporte, modelos de aprendizado de máquina com suporte e ambientes de execução com suporte aqui.


## <a name="supported-interpretability-techniques"></a>Técnicas de interpretação com suporte

 `azureml-interpret` usa as técnicas de interpretação desenvolvidas na [interpretação da Comunidade](https://github.com/interpretml/interpret-community/), um pacote Python de software livre para treinar modelos interpretáveis e ajudar a explicar sistemas de ia Blackbox. A [interpretação-Community](https://github.com/interpretml/interpret-community/) serve como o host para os explicadores com suporte do SDK e atualmente oferece suporte às seguintes técnicas de interpretação:

|Técnica de interpretação|Descrição|Type|
|--|--|--------------------|
|Explicador de árvore SHAP| O explicador de árvore do [shap](https://github.com/slundberg/shap), que se concentra no algoritmo de estimativa de valor shap tempo polinomial rápido e específico para **árvores e conjuntos de árvores**.|Específico do modelo|
|Explicador profundo do SHAP| Com base na explicação do SHAP, o profundo explicador "é um algoritmo de aproximação de alta velocidade para valores de SHAP em modelos de aprendizado profundo que se baseiam em uma conexão com o DeepLIFT descrito no [artigo shap Nips](https://papers.nips.cc/paper/7062-a-unified-approach-to-interpreting-model-predictions). Há suporte para modelos **TensorFlow** e modelos **Keras** usando o back-end TensorFlow (também há suporte preliminar para PyTorch) ".|Específico do modelo|
|Explicador linear SHAP| O explicador linear de SHAP computa valores de SHAP para um **modelo linear**, opcionalmente, para correlações entre recursos.|Específico do modelo|
|Explicador de kernel SHAP| O explicador do kernel do SHAP usa uma regressão linear local especialmente ponderada para estimar valores de SHAP para **qualquer modelo**.|Independente de modelo|
|Explicador de imitação (substituto global)| O explicador de imitação se baseia na ideia de treinar [modelos substitutos globais](https://christophm.github.io/interpretable-ml-book/global.html) para imitar modelos Blackbox. Um modelo substituto global é um modelo intrinsecamente interpretável que é treinado para aproximar as previsões de **qualquer modelo de caixa preta** o mais precisamente possível. Os cientistas de dados podem interpretar o modelo substituto para desenhar conclusões sobre o modelo de caixa preta. Você pode usar um dos seguintes modelos interpretáveis como seu modelo substituto: LightGBM (LGBMExplainableModel), regressão linear (LinearExplainableModel), estocástico gradiente descendente de modelo explicativo (SGDExplainableModel) e árvore de decisão (DecisionTreeExplainableModel).|Independente de modelo|
|Explicador de importância do recurso de permuta (PFI)| A importância do recurso de permuta é uma técnica usada para explicar os modelos de classificação e regressão inspirados pelo [documento de florestas aleatórias do Breiman](https://www.stat.berkeley.edu/~breiman/randomforest2001.pdf) (consulte a seção 10). Em um alto nível, a maneira como ele funciona é por meio do embaralhamento de dados um recurso por vez para todo o DataSet e calcular quanto a métrica de desempenho de interesse muda. Quanto maior a alteração, mais importante é esse recurso. PFI pode explicar o comportamento geral de **qualquer modelo subjacente** , mas não explica previsões individuais. |Independente de modelo|




Além das técnicas de interpretação descritas acima, damos suporte a outro explicador baseado em SHAP, chamado `TabularExplainer` . Dependendo do modelo, `TabularExplainer` o usa um dos explicadores de shap com suporte:

* TreeExplainer para todos os modelos baseados em árvore
* DeepExplainer para modelos de DNN
* LinearExplainer para modelos lineares
* KernelExplainer para todos os outros modelos

`TabularExplainer` também fez melhorias significativas de desempenho e recursos em relação aos explicadores de SHAP diretos:

* **Resumo do conjunto de inicialização**. Nos casos em que a velocidade da explicação é mais importante, resumimos o conjunto de recursos de inicialização e geramos um pequeno conjunto de exemplos representativos, que aceleram a geração de valores de importância de recursos gerais e individuais.
* **Amostragem do conjunto de dados de avaliação**. Se o usuário passar em um grande conjunto de amostras de avaliação, mas não precisar realmente de todas elas para ser avaliada, o parâmetro de amostragem poderá ser definido como true para acelerar o cálculo das explicações gerais do modelo.

O diagrama a seguir mostra a estrutura atual dos explicadores com suporte.

[![Arquitetura de interpretação de Machine Learning](./media/how-to-machine-learning-interpretability/interpretability-architecture.png)](./media/how-to-machine-learning-interpretability/interpretability-architecture.png#lightbox)


## <a name="supported-machine-learning-models"></a>Modelos de aprendizado de máquina com suporte

O `azureml.interpret` pacote do SDK dá suporte a modelos treinados com os seguintes formatos de conjunto de os:
- `numpy.array`
- `pandas.DataFrame`
- `iml.datatypes.DenseData`
- `scipy.sparse.csr_matrix`

As funções de explicação aceitam modelos e pipelines como entrada. Se um modelo for fornecido, o modelo deverá implementar a função de previsão `predict` ou estar `predict_proba` em conformidade com a Convenção Scikit. Se o modelo não oferecer suporte a isso, você poderá encapsular o modelo em uma função que gera o mesmo resultado que `predict` ou `predict_proba` em Scikit e usar essa função de wrapper com o explicador selecionado. Se um pipeline for fornecido, a função de explicação assumirá que o script de pipeline em execução retorna uma previsão. Usando essa técnica de encapsulamento, o `azureml.interpret` pode dar suporte a modelos treinados por meio de estruturas de aprendizado profundo PyTorch, TensorFlow e Keras, bem como modelos de aprendizado de máquina clássicos.

## <a name="local-and-remote-compute-target"></a>Destino de computação local e remota

O `azureml.interpret` pacote foi projetado para funcionar com destinos de computação locais e remotos. Se for executado localmente, as funções do SDK não entrarão em contato com nenhum serviço do Azure. 

Você pode executar a explicação remotamente em Azure Machine Learning computação e registrar em log as informações de explicação no serviço de histórico de execução de Azure Machine Learning. Depois que essas informações são registradas, relatórios e visualizações da explicação estão prontamente disponíveis no Azure Machine Learning Studio para análise do usuário.


## <a name="next-steps"></a>Próximas etapas

- Veja [como](how-to-machine-learning-interpretability-aml.md) habilitar a interpretabilidade para treinamento de modelos tanto localmente quanto em Azure Machine Learning recursos de computação remota. 
- Consulte os [blocos de anotações de exemplo](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) para cenários adicionais. 
- Se você estiver interessado em interpretar a interpretação de cenários de texto, consulte [interpretar o texto](https://github.com/interpretml/interpret-text), um repositório de código aberto relacionado para [interpretar a Comunidade](https://github.com/interpretml/interpret-community/), para obter técnicas de interpretação para NLP. `azureml.interpret` no momento, o pacote não dá suporte a essas técnicas, mas você pode começar com um [bloco de anotações de exemplo na classificação de texto](https://github.com/interpretml/interpret-text/blob/master/notebooks/text_classification/text_classification_classical_text_explainer.ipynb).