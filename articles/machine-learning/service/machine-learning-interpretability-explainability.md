---
title: Interpretabilidade do modelo
titleSuffix: Azure Machine Learning service
description: Saiba como explicar por que o seu modelo faz previsões usando o SDK do Azure Machine Learning possibilidade de interpretação. Ele pode ser usado durante a inferência e treinamento para entender como seu modelo faz previsões.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: larryfr
ms.date: 04/09/2019
ms.openlocfilehash: 668551603dfa2a9c42f4538fd9a66ee646e1feb7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60817264"
---
# <a name="model-interpretability-with-azure-machine-learning-service"></a>Possibilidade de interpretação de modelo com o serviço Azure Machine Learning

Neste artigo, você aprenderá a explicar por que o seu modelo de feitas as previsões ele feitas usando o SDK de possibilidade de interpretação do Azure Machine Learning. É importante ser capaz de explicar seu modelo pelos seguintes motivos:

* Clientes e partes interessadas querem saber **torna o seu modelo se eles podem confiar que as previsões**.
* Como um cientista de dados, você quiser entender **como consultar o modelo para encontrar insights**. Você também precisa de ferramentas para tomar decisões informadas sobre **como melhorar seu modelo**.
* Como uma empresa, você precisa entender **o comportamento do modelo com diferentes distribuições de entrada** e **como o modelo se comportará durante a análise de entrada específica**.

Possibilidade de interpretação de aprendizado de máquina é importante em duas fases do ciclo de desenvolvimento do aprendizado de máquina: 

* Durante **treinamento**: Avaliadores e designers de modelo exigem ferramentas de possibilidade de interpretação para explicar a saída de um modelo aos participantes para criar a relação de confiança. Eles também precisam percepções sobre o modelo para que eles podem depurar o modelo e tomar decisões sobre se o comportamento corresponde ao seus objetivos. Por fim, eles precisam garantir que o modelo não é mais adequado.

* Durante **inferência**: Previsões precisam ser explicadas para as pessoas que usam seu modelo. Por exemplo, por que o modelo negar um empréstimo de hipoteca ou prever que um portfólio de investimento traz um risco mais alto?

O SDK do Azure Machine Learning possibilidade de interpretação incorpora tecnologias desenvolvidas pela Microsoft e comprovadas bibliotecas de terceiros (por exemplo, da forma e verde-LIMÃO). O SDK cria uma API comum entre as bibliotecas integradas e integra os serviços do Azure Machine Learning. Usando esse SDK, você pode explicar os modelos de aprendizado de máquina **globalmente em todos os dados**, ou **localmente em um ponto de dados específicos** usando as tecnologias de última geração de uma maneira fácil de usar e escalonável.

## <a name="how-does-it-work"></a>Como ele funciona?

Possibilidade de interpretação de aprendizado de máquina do Azure podem ser aplicada para entender o modelo global comportamento ou previsões específicos. O primeiro é chamado de explicação global e o último é chamado de explicação local.

Métodos de possibilidade de interpretação de aprendizado de máquina do Azure podem ser categorizados também com base em se o método é independente de modelo ou um modelo específico. Alguns métodos de determinado tipo de modelos de destino. Por exemplo, explicador de árvore da forma só se aplica a modelos baseados em árvore. Alguns métodos de tratam o modelo como uma caixa preta, como imitar explicador ou explicador do kernel da forma. SDK de possibilidade de interpretação de aprendizado de máquina do Azure utiliza essas abordagens diferentes com base em conjuntos de dados, tipos de modelo e casos de uso.

Possibilidade de interpretação de aprendizado de máquina do Azure retorna um conjunto de informações sobre como um modelo torna sua previsão. As informações incluem itens como:

* Importância do recurso relativo do local/global
* Relação de recurso e previsão global/local

## <a name="architecture"></a>Arquitetura

SDK do Azure Machine Learning possibilidade de interpretação é estruturado em dois pacotes do Python:

* [azureml.Explain.Model](https://docs.microsoft.com/python/api/azureml-explain-model/?view=azure-ml-py) -o pacote principal, que contém as funcionalidades que são suportadas pela Microsoft.
* `azureml.contrib.explain.model` -Visualizar e funcionalidades experimentais que você pode experimentar.

    > [!IMPORTANT]
    > As coisas em contrib não são totalmente compatíveis. Como as funcionalidades experimentais ficam maduras, eles serão movidos gradualmente para o pacote principal.

### <a name="explainers"></a>Explainers

O SDK de possibilidade de interpretação do Azure Machine Learning apresenta dois conjuntos de explainers: Explainers diretas e Explainers Meta.

__Direcionar explainers__ vêm de bibliotecas integradas. O SDK encapsula todos os explainers para que eles expõem uma API comum e o formato de saída. A seguir é uma lista das explainers diretas disponíveis no SDK:

> [!TIP]
> Se você estiver mais familiarizado com o uso diretamente esses explainers, você pode invocá-los diretamente em vez de usar a API comum e o formato de saída.

* **Árvore explicador**: Explicador de árvore da forma, que se concentra no tempo polinomial rápido da forma valor estimativa específico de algoritmo de árvores e conjuntos de árvores de.
* **Explicador profunda**: Com base na explicação da forma, explicador profunda "é um algoritmo de aproximação de alta velocidade de valores da forma de modelos de aprendizado profundo que se baseia em uma conexão com DeepLIFT descrita no artigo NIPS da forma. Há suporte para modelos de TensorFlow e modelos de Keras usando o back-end TensorFlow (também há suporte preliminar para PyTorch) ".
* **Explicador kernel**: Explicador de Kernel da forma usa uma regressão linear de local especialmente ponderada para estimar os valores da forma para qualquer modelo.
* **Imitar explicador**: Explicador imitar baseia-se na ideia de modelos globais substituto. Um modelo de substitutos global é um modelo que é interpretado intrinsecamente que é treinado para aproximar as previsões de um modelo de caixa preta de forma mais precisa possível. Cientista de dados pode interpretar o modelo de substitutos para tirar conclusões sobre o modelo de caixa preta.
* **Verde-LIMÃO explicador**: Verde-LIMÃO explicador com base em verde-LIMÃO, usa o algoritmo de geração de Local que é interpretado independente de modelo explicações (verde-LIMÃO) para criar modelos de local alternativo. Ao contrário dos modelos de substitutos global, verde-LIMÃO concentra-se no treinamento de modelos de local alternativo para explicar as previsões individuais.
* **HENRIQUE texto explicador**: HENRIQUE texto explicador usa uma rede de atenção hierárquica para obter explicações sobre o modelo de dados de texto para um modelo de texto determinada caixa-preta. Treinar o modelo de substitutos HENRIQUE nas saídas de um modelo professor determinado previsto. Após o treinamento globalmente em corpus de texto, adicionamos uma etapa de ajuste fino de um documento específico para melhorar a precisão das explicações. HENRIQUE usa um bidirecional RNN com duas camadas de atenção, por atenção sentença e palavra. Depois que a DNN é treinada em um modelo de professor e ajustada em um documento específico, podemos pode extrair os importances word das camadas de atenção. Descobrimos HENRIQUE para ser mais precisos do que verde-LIMÃO ou da forma para dados de texto, mas mais caros em termos de tempo também de treinamento. No entanto, fizemos melhorias para o tempo de treinamento por dar ao usuário a opção para inicializar a rede com luva incorporações de palavras, embora ele ainda está lento. O tempo de treinamento pode ser melhorado significativamente executando HENRIQUE em uma VM de GPU remota do Azure. A implementação de HENRIQUE é descrita em 'Redes de atenção hierárquica para classificação de documento (Yang farão, 2016)' ([https://www.cs.cmu.edu/~diyiy/docs/naacl16.pdf](https://www.cs.cmu.edu/~diyiy/docs/naacl16.pdf)).

__Explainers meta__ automaticamente selecionar um explicador direto adequado e gerar as informações de explicação melhor com base no modelo especificado e conjuntos de dados. Os explainers meta aproveitam todas as bibliotecas (da forma, verde-LIMÃO, imitam, etc.) que temos integrado ou desenvolvidos. Estes são os explainers meta disponíveis no SDK:

* **Explicador tabular**: Usado com conjuntos de dados tabulares.
* **Texto explicador**: Usado com conjuntos de dados de texto.

Além ao meta-selecionando dos explainers diretos, explainers meta desenvolvem recursos adicionais sobre as bibliotecas subjacentes e melhorar a velocidade e escalabilidade nos explainers diretos.

No momento `TabularExplainer` emprega a lógica a seguir para invocar o Explainers direto:

1. Se for um modelo baseado em árvore, aplicar `TreeExplainer`, else
2. Se for um modelo DNN, aplicar `DeepExplainer`, else
3. Tratá-lo como um modelo de caixa preta e aplicar `KernelExplainer`

A inteligência incorporada `TabularExplainer` se tornará mais sofisticadas conforme mais bibliotecas estão integradas no SDK e podemos aprender sobre os prós e contras de cada explicador.

`TabularExplainer` também fez aprimoramentos significativos de desempenho e de recursos sobre o Explainers direto:

* **Resumo do conjunto de dados de inicialização**. Em casos onde a velocidade de explicação é mais importante, vamos resumir o conjunto de dados de inicialização e gerar um pequeno conjunto de exemplos representativos, que acelera a explicação global e local.
* **O conjunto de dados de avaliação de amostragem**. Se o usuário passa em um grande conjunto de exemplos de avaliação, mas, na verdade, não precisa de todos eles a ser avaliada, o parâmetro de amostragem pode ser definido como true para acelerar a explicação global.

O diagrama a seguir mostra a relação entre os dois conjuntos de direct e explainers meta.

[![Arquitetura de possibilidade de interpretação de aprendizado de máquina](./media/machine-learning-interpretability-explainability/interpretability-architecture.png)](./media/machine-learning-interpretability-explainability/interpretability-architecture.png#lightbox)

### <a name="models-supported"></a>Modelos com suporte

Os modelos são treinados em conjuntos de dados em Python `numpy.array`, `pandas.DataFrame`, `iml.datatypes.DenseData`, ou `scipy.sparse.csr_matrix` formato são compatíveis com o SDK de possibilidade de interpretação de aprendizado de máquina.

As funções de explicação aceitam modelos e pipelines como entrada. Se um modelo for fornecido, o modelo deve implementar a função de previsão `predict` ou `predict_proba` que esteja de acordo com a convenção de Scikit. Se um pipeline (nome do script de pipeline) for fornecido, a função de explicação pressupõe que o script em execução do pipeline retorna uma previsão.

### <a name="local-and-remote-compute-target"></a>Destino de computação local e remota

O SDK de possibilidade de interpretação de aprendizado de máquina é projetado para trabalhar com ambos os destinos de computação local e remota. Se executar localmente, as funções do SDK não contatar todos os serviços do Azure. Você pode executar explicação remotamente na computação do Azure Machine Learning e registrar as informações na explicação em serviços de histórico de execução do Azure Machine Learning. Depois que essas informações são registradas, relatórios e visualizações da explicação estão disponíveis no Portal de espaço de trabalho do Azure Machine Learning para análise de usuário.

## <a name="train-and-explain-locally"></a>Treinar e explique localmente

1. Treine seu modelo em um notebook Jupyter local. 

    ``` python
    # load breast cancer dataset, a well-known small dataset that comes with scikit-learn
    from sklearn.datasets import load_breast_cancer
    from sklearn import svm
    from sklearn.model_selection import train_test_split
    breast_cancer_data = load_breast_cancer()
    classes = breast_cancer_data.target_names.tolist()
    # Split data into train and test
    from sklearn.model_selection import train_test_split
    x_train, x_test, y_train, y_test = train_test_split(breast_cancer_data.data, breast_cancer_data.target, test_size=0.2, random_state=0)
    clf = svm.SVC(gamma=0.001, C=100., probability=True)
    model = clf.fit(x_train, y_train)
    ```

2. Chame o explicador: Para iniciar um objeto explicador, você precisará passar o modelo, dados de treinamento, recursos de interesse (opcional) e nomes de classe de saída (se classificação) para o explicador. Aqui está como criar uma instância de um objeto explicador usando [TabularExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.tabularexplainer?view=azure-ml-py), [MimicExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.mimic.mimicexplainer?view=azure-ml-py), e `LimeExplainer` localmente. `TabularExplainer` é chamar um dos três explainers abaixo (`TreeExplainer`, `DeepExplainer`, ou `KernelExplainer`) e é automaticamente selecionando o mais apropriado para seu caso de uso. No entanto, você pode chamar cada um dos seus três explainers subjacentes diretamente.

    ```python
    from azureml.explain.model.tabular_explainer import TabularExplainer
    explainer = TabularExplainer(model, x_train, features=breast_cancer_data.feature_names, classes=classes)
    ```
    ou o
    ```python
    from azureml.explain.model.mimic.mimic_explainer import MimicExplainer
    from azureml.explain.model.mimic.models.lightgbm_model import LGBMExplainableModel
    explainer = MimicExplainer(model, x_train, LGBMExplainableModel, features=breast_cancer_data.feature_names, classes=classes)
    ```

3. Obter o recurso global de valores de importância.

    ```python
    # You can use the training data or the test data here. 
    global_explanation = explainer.explain_global(x_train)
    # Sorted feature importance values and feature names
    sorted_global_importance_values = global_explanation.get_ranked_global_values()
    sorted_global_importance_names = global_explanation.get_ranked_global_names()
    dict(zip(sorted_global_importance_names, sorted_global_importance_values))
    ```

4. Os valores de importância do recurso local: use as seguintes chamadas de função para explicar a uma instância individual ou um grupo de instâncias.

    ```python
    # explain the first data point in the test set
    local_explanation = explainer.explain_local(x_test[0,:])
    
    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```
    ou o
    ```python
    # explain the first five data points in the test set
    local_explanation = explainer.explain_local(x_test[0:4,:])
    
    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```

## <a name="train-and-explain-remotely"></a>Treinar e explique remotamente

Enquanto você pode treinar sobre os vários destinos de computação com suporte pelo serviço Azure Machine Learning, o exemplo nesta seção mostra como fazer isso usando AMLCompute.

1. Crie um script de treinamento em um bloco de anotações do Jupyter (por exemplo, run_explainer.py) local.

    ``` python  
    run = Run.get_context()
    client = ExplanationClient.from_run(run)
    
    # Train your model here

    # explain predictions on your local machine    
    explainer = TabularExplainer(model, x_train, features=breast_cancer_data.feature_names, classes=classes)
    # explain overall model predictions (global explanation)
    global_explanation = explainer.explain_global(x_test)
    # explain local data points (individual instances)
    local_explanation = explainer.explain_local(x_test[0,:])
    # upload global and local explanation objects to Run History
    upload_model_explanation(run, local_explanation, top_k=2, comment='local explanation: top 2 features')
    # Uploading global model explanation data for storage or visualization in webUX
    # The explanation can then be downloaded on any compute
    # Multiple explanations can be uploaded
    client.upload_model_explanation(global_explanation, comment='global explanation: all features')
    # Or you can only upload the explanation object with the top k feature info
    #client.upload_model_explanation(global_explanation, top_k=2, comment='global explanation: Only top 2 features')
    ```

2. Siga as instruções em [configurar destinos de computação para treinamento de modelo](how-to-set-up-training-targets.md#amlcompute) para saber mais sobre como configurar uma computação do Azure Machine Learning como seu destino de computação e enviar sua execução de treinamento.

3. Baixe a explicação do bloco de anotações Jupyter local. 
    > [!IMPORTANT]
    > As coisas em contrib não são totalmente compatíveis. Como as funcionalidades experimentais ficam maduras, eles serão movidos gradualmente para o pacote principal.

    ``` python
    from azureml.contrib.explain.model.explanation.explanation_client import ExplanationClient
    # Get model explanation data
    client = ExplanationClient.from_run(run)
    explanation = client.download_model_explanation()
    local_importance_values = explanation.local_importance_values
    expected_values = explanation.expected_values
    # Or you can use the saved run.id to retrive the feature importance values
    client = ExplanationClient.from_run_id(ws, experiment_name, run.id)
    explanation = client.download_model_explanation()
    local_importance_values = explanation.local_importance_values
    expected_values = explanation.expected_values
    # Get the top k (e.g., 4) most important features with their importance values
    explanation = client.download_model_explanation(top_k=4)
    global_importance_values = explanation.get_ranked_global_values()
    global_importance_names = explanation.get_ranked_global_names()
    print('global importance values: {}'.format(global_importance_values))
    print('global importance names: {}'.format(global_importance_names))
    ```

## <a name="next-steps"></a>Próximas etapas

Para ver uma coleção de blocos de anotações do Jupyter que demonstram as instruções acima, consulte a [notebooks de amostra da possibilidade de interpretação do Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model).
