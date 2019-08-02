---
title: Interpretabilidade do modelo
titleSuffix: Azure Machine Learning service
description: Saiba como explicar por que seu modelo faz previsões usando o SDK do Azure Machine Learning. Ele pode ser usado durante o treinamento e a inferência para entender como seu modelo faz previsões.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: larryfr
ms.date: 06/21/2019
ms.openlocfilehash: 1e742c278b9356c7501964541802e0c96dc74b09
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68358652"
---
# <a name="model-interpretability-with-azure-machine-learning-service"></a>Interpretação de modelo com serviço Azure Machine Learning

Neste artigo, você aprenderá a explicar por que seu modelo fez as previsões feitas com os vários pacotes de interpretação da Azure Machine Learning SDK do Python.

Usando as classes e os métodos no SDK, você pode obter:
+ Valores de importância de recursos para recursos brutos e de engenharia
+ Interpretabilidade em conjuntos de valores reais em escala, durante o treinamento e a inferência.
+ Visualizações interativas para ajudá-lo na descoberta de padrões em dados e explicações no tempo de treinamento

Durante a fase de treinamento do ciclo de desenvolvimento, designers de modelo e avaliadores podem usar a saída de interpretação de um modelo para verificar as mesmas e criar confiança com os participantes.  Eles também usam as informações sobre o modelo para depuração, validando o comportamento do modelo corresponde aos seus objetivos e verificando a tendência.

No aprendizado de máquina, os **recursos** são os campos de dados usados para prever um ponto de dados de destino. Por exemplo, para prever o risco de crédito, os campos de dados para idade, tamanho da conta e idade da conta podem ser usados. Nesse caso, a idade, o tamanho da conta e a idade da conta são **recursos**. A importância do recurso informa como cada campo de dados afetou as previsões do modelo. Por exemplo, a idade pode ser muito usada na previsão, enquanto o tamanho da conta e a idade não afetam significativamente a precisão da previsão. Esse processo permite que os cientistas de dados expliquem previsões resultantes, para que os participantes tenham visibilidade sobre quais pontos de dados são mais importantes no modelo.

Usando essas ferramentas, você pode explicar os modelos de aprendizado de máquina **globalmente em todos os dados**ou **localmente em um ponto de dados específico** usando as tecnologias de ponta, de maneira fácil de usar e escalonáveis.

As classes de interpretação são disponibilizadas por meio de vários pacotes do SDK. Saiba como [instalar pacotes do SDK para Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* [`azureml.explain.model`](https://docs.microsoft.com/python/api/azureml-explain-model/?view=azure-ml-py), o pacote principal, contendo funcionalidades com suporte da Microsoft.

* `azureml.contrib.explain.model`, visualização e funcionalidades experimentais que você pode tentar.

* `azureml.train.automl.automlexplainer`pacote para interpretar modelos de aprendizado de máquina automatizados.

> [!IMPORTANT]
> O `contrib` conteúdo no namespace não tem suporte completo. Como as funcionalidades experimentais se tornam maduras, elas serão gradualmente movidas para o namespace principal.

## <a name="how-to-interpret-your-model"></a>Como interpretar seu modelo

Você pode aplicar as classes e métodos de interpretação para entender o comportamento global do modelo ou previsões específicas. O primeiro é chamado de explicação global e o segundo é chamado de explicação local.

Os métodos também podem ser categorizados com base no fato de o método ser independente de modelo ou específico de modelo. Alguns métodos visam certos tipos de modelos. Por exemplo, o explicador de árvore do SHAP só se aplica a modelos baseados em árvore. Alguns métodos tratam o modelo como uma caixa preta, como um explicador de imitação ou de kernel SHAP. O `explain` pacote aproveita essas diferentes abordagens com base em conjuntos de dados, tipos de modelo e casos de uso.

A saída é um conjunto de informações sobre como um determinado modelo faz sua previsão, como:
* Importância de recurso relativo global/local

* Recurso global/local e relação de previsão

### <a name="explainers"></a>Explicadores

Há dois conjuntos de explicadores: Explicadores diretos e meta explicadores no SDK.

Os explicadores diretos vêm de bibliotecas integradas. O SDK encapsula todos os explicadores para que eles exponham um formato de saída e de API comum. Se você se sentir mais confortável diretamente usando esses explicadores, poderá chamá-los diretamente em vez de usar a API comum e o formato de saída. Veja a seguir uma lista dos explicadores diretos disponíveis no SDK:

* **Explicador da árvore de shap**: O explicador de árvore do SHAP, que se concentra no algoritmo de estimativa de valor SHAP tempo polinomial rápido e específico para árvores e conjuntos de árvores.
* **Explicador profundo do shap**: Com base na explicação do SHAP, o profundo explicador "é um algoritmo de aproximação de alta velocidade para valores de SHAP em modelos de aprendizado profundo que se baseiam em uma conexão com o DeepLIFT descrito no artigo SHAP NIPS. Há suporte para modelos TensorFlow e modelos Keras usando o back-end TensorFlow (também há suporte preliminar para PyTorch) ".
* **Explicador de kernel shap**: O explicador do kernel do SHAP usa uma regressão linear local especialmente ponderada para estimar valores de SHAP para qualquer modelo.
* **Explicador**de imitação: O explicador de imitação se baseia na ideia de modelos substitutos globais. Um modelo substituto global é um modelo intrinsecamente interpretável que é treinado para aproximar as previsões de um modelo de caixa preta o mais precisamente possível. O cientista de dados pode interpretar o modelo substituto para desenhar conclusões sobre o modelo de caixa preta. Você pode usar um dos seguintes modelos interpretáveis como seu modelo substituto: LightGBM (LinearExplainableModel), regressão linear (LinearExplainableModel), estocástico Grad descendente modelo explicativo (SGDExplainableModel) e árvore de decisão (DecisionTreeExplainableModel).


* **Explicador de importância do recurso de permuta**: A importância do recurso de permuta é uma técnica usada para explicar os modelos de classificação e regressão inspirados pelo [documento de florestas aleatórias do Breiman](https://www.stat.berkeley.edu/%7Ebreiman/randomforest2001.pdf) (consulte a seção 10). Em um alto nível, a maneira como ele funciona é por meio do embaralhamento de dados um recurso por vez para todo o DataSet e calcular a quantidade de métricas de desempenho que o interesse diminui. Quanto maior a alteração, mais importante é esse recurso.

* **Explicador de verde-limão** (`contrib`): Com base no verde-limão, o explicador de verde-limão usa o algoritmo de alto-limão de modelo interpretável local interpretado para criar modelos substitutos locais. Diferentemente dos modelos substitutos globais, o verde-limão se concentra em treinar modelos substitutos locais para explicar previsões individuais.
* **Explicador de texto Han** (`contrib`): O explicador de texto HAN usa uma rede de atenção hierárquica para obter explicações de modelo de dados de texto para um determinado modelo de texto de caixa preta. Treinamos o modelo substituto de HAN em uma determinada saída prevista do modelo de professor. Depois de treinar globalmente pelo texto corpus, adicionamos uma etapa de ajuste fino para um documento específico a fim de melhorar a precisão das explicações. O HAN usa um RNN bidirecional com duas camadas de atenção, para a atenção da frase e da palavra. Depois que o DNN é treinado no modelo de professor e ajustado em um documento específico, podemos extrair a palavra importância das camadas de atenção. Encontramos o HAN para ser mais preciso do que verde-limão ou SHAP para dados de texto, mas também mais dispendioso em termos de tempo de treinamento. No entanto, fizemos melhorias no tempo de treinamento, dando ao usuário a opção de inicializar a rede com incorporações de palavras diferenciada, embora ainda seja lenta. O tempo de treinamento pode ser melhorado significativamente com a execução de HAN em uma VM de GPU remota do Azure. A implementação de HAN é descrita em "redes de atenção hierárquica para classificação de documentos (Yang et al., 2016)[https://www.cs.cmu.edu/~diyiy/docs/naacl16.pdf](https://www.cs.cmu.edu/~diyiy/docs/naacl16.pdf)" ().


Os __meta explicadores__ selecionam automaticamente um explicador direto adequado e geram as melhores informações de explicação com base no modelo e nos conjuntos de dados fornecidos. Os timeexplicadores aproveitam todas as bibliotecas (SHAP, verde-limão, imitação, etc.) que nós integramos ou desenvolvemos. Estes são os meta explicadores disponíveis no SDK:

* **Explicador tabular**: Usado com conjuntos de tabelas de tabela.
* **Explicador de texto**: Usado com conjuntos de valores de texto.
* **Explicador de imagem**: Usado com conjuntos de imagens de imagem.

Além da meta-seleção dos explicadores diretos, os meta explicadores desenvolvem recursos adicionais sobre as bibliotecas subjacentes e melhoram a velocidade e a escalabilidade nos explicadores diretos.

Atualmente `TabularExplainer` emprega a seguinte lógica para invocar os explicadores diretos do shap:

1. Se for um modelo baseado em árvore, aplique shap `TreeExplainer`, senão
2. Se for um modelo DNN, aplique shap `DeepExplainer`, senão
3. Tratá-lo como um modelo de caixa preta e aplicar SHAP`KernelExplainer`

A inteligência interna `TabularExplainer` se tornará mais sofisticada, pois mais bibliotecas são integradas ao SDK e aprendemos sobre os prós e contras de cada explicador.

`TabularExplainer`também fez melhorias significativas de desempenho e recursos em relação aos explicadores diretos:

* **Resumo do conjunto de inicialização**. Nos casos em que a velocidade da explicação é mais importante, resumimos o conjunto de inicialização e geramos um pequeno conjunto de exemplos representativos, que aceleram a explicação global e local.
* **Amostragem do conjunto de dados de avaliação**. Se o usuário passar em um grande conjunto de amostras de avaliação, mas não precisar realmente de todas elas para ser avaliada, o parâmetro de amostragem poderá ser definido como true para acelerar a explicação global.

O diagrama a seguir mostra a estrutura atual dos explicadores diretos e meta.

[![Arquitetura de interpretação de Machine Learning](./media/machine-learning-interpretability-explainability/interpretability-architecture.png)](./media/machine-learning-interpretability-explainability/interpretability-architecture.png#lightbox)


### <a name="models-supported"></a>Modelos com suporte

Todos os modelos treinados em conjuntos de valores no Python `numpy.array` `iml.datatypes.DenseData`, `pandas.DataFrame`, `explain` ou `scipy.sparse.csr_matrix` no formato são suportados pelo pacote de interpretação do SDK.

As funções de explicação aceitam modelos e pipelines como entrada. Se um modelo for fornecido, o modelo deverá implementar a função `predict` de previsão ou `predict_proba` estar em conformidade com a Convenção Scikit. Se um pipeline (nome do script de pipeline) for fornecido, a função de explicação assumirá que o script de pipeline em execução retorna uma previsão. Damos suporte a modelos treinados por meio de estruturas de aprendizado profundo PyTorch, TensorFlow e Keras.

### <a name="local-and-remote-compute-target"></a>Destino de computação local e remota

O `explain` pacote foi projetado para funcionar com destinos de computação locais e remotos. Se for executado localmente, as funções do SDK não entrarão em contato com nenhum serviço do Azure. Você pode executar a explicação remotamente em Azure Machine Learning computação e registrar em log as informações de explicação em Azure Machine Learning serviços de histórico de execução. Depois que essas informações são registradas, relatórios e visualizações da explicação estão prontamente disponíveis no portal de Workspace do Azure Machine Learning para análise do usuário.

## <a name="interpretability-in-training"></a>Interpretabilidade no treinamento

### <a name="train-and-explain-locally"></a>Treinar e explicar localmente

1. Treine seu modelo em um notebook Jupyter local.

    ```python
    # load breast cancer dataset, a well-known small dataset that comes with scikit-learn
    from sklearn.datasets import load_breast_cancer
    from sklearn import svm
    from sklearn.model_selection import train_test_split
    breast_cancer_data = load_breast_cancer()
    classes = breast_cancer_data.target_names.tolist()
    
    # split data into train and test
    from sklearn.model_selection import train_test_split
    x_train, x_test, y_train, y_test = train_test_split(breast_cancer_data.data,            
                                                        breast_cancer_data.target,  
                                                        test_size=0.2,
                                                        random_state=0)
    clf = svm.SVC(gamma=0.001, C=100., probability=True)
    model = clf.fit(x_train, y_train)
    ```

2. Chame o explicador: Para inicializar um objeto de explicador, você precisa passar seu modelo e alguns dados de treinamento para o construtor do explicador. Você também pode, opcionalmente, passar nomes de recursos e nomes de classe de saída (se estiver fazendo a classificação) que serão usados para tornar as explicações e visualizações mais informativas. Aqui está como criar uma instância de um objeto de explicador usando [TabularExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.tabularexplainer?view=azure-ml-py), [MimicExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.mimic.mimicexplainer?view=azure-ml-py)e [PFIExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.permutation.permutation_importance.pfiexplainer?view=azure-ml-py) localmente. `TabularExplainer`está chamando um dos três explicadores shap abaixo (`TreeExplainer`, `DeepExplainer`ou `KernelExplainer`), e está selecionando automaticamente o mais apropriado para seu caso de uso. No entanto, você pode chamar cada um dos seus três explicativos subjacentes diretamente.

    ```python
    from azureml.explain.model.tabular_explainer import TabularExplainer
    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=breast_cancer_data.feature_names, 
                                 classes=classes)
    ```

    ou

    ```python
    from azureml.explain.model.mimic.mimic_explainer import MimicExplainer
    
    # you can use one of the following four interpretable models as a global surrogate to the black box model
    from azureml.explain.model.mimic.models.lightgbm_model import LGBMExplainableModel
    from azureml.explain.model.mimic.models.linear_model import LinearExplainableModel
    from azureml.explain.model.mimic.models.linear_model import SGDExplainableModel
    from azureml.explain.model.mimic.models.tree_model import DecisionTreeExplainableModel

    # "features" and "classes" fields are optional
    # augment_data is optional and if true, oversamples the initialization examples to improve surrogate model accuracy to fit original model.  Useful for high-dimensional data where the number of rows is less than the number of columns. 
    # max_num_of_augmentations is optional and defines max number of times we can increase the input data size.
    # LGBMExplainableModel can be replaced with LinearExplainableModel, SGDExplainableModel, or DecisionTreeExplainableModel
    explainer = MimicExplainer(model, 
                               x_train, 
                               LGBMExplainableModel, 
                               augment_data=True, 
                               max_num_of_augmentations=10, 
                               features=breast_cancer_data.feature_names, 
                               classes=classes)
    ```
   ou

    ```python
    from azureml.explain.model.permutation.permutation_importance import PFIExplainer 
    
    # "features" and "classes" fields are optional
    explainer = PFIExplainer(model, 
                             features=breast_cancer_data.feature_names, 
                             classes=classes)
    ```

3. Obtenha os valores globais de importância do recurso.

    ```python
    # you can use the training data or the test data here
    global_explanation = explainer.explain_global(x_train)
    
    # if you used the PFIExplainer in the previous step, use the next line of code instead
    # global_explanation = explainer.explain_global(x_train, true_labels=y_test)

    # sorted feature importance values and feature names
    sorted_global_importance_values = global_explanation.get_ranked_global_values()
    sorted_global_importance_names = global_explanation.get_ranked_global_names()
    dict(zip(sorted_global_importance_names, sorted_global_importance_values))

    # alternatively, you can print out a dictionary that holds the top K feature names and values
    global_explanation.get_feature_importance_dict()
    ```

4. Obter os valores de importância do recurso local: Use as seguintes chamadas de função para explicar uma instância individual ou um grupo de instâncias. Observe que o PFIExplainer não oferece suporte a explicações locais.

    ```python
    # explain the first data point in the test set
    local_explanation = explainer.explain_local(x_test[0])

    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```

    ou

    ```python
    # explain the first five data points in the test set
    local_explanation = explainer.explain_local(x_test[0:4])

    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```

### <a name="train-and-explain-remotely"></a>Treinar e explicar remotamente

Embora você possa treinar em vários destinos de computação com suporte pelo serviço Azure Machine Learning, o exemplo nesta seção mostra como fazer isso usando um destino de computação Azure Machine Learning.

1. Crie um script de treinamento em um bloco de anotações Jupyter local (por exemplo, run_explainer. py).

    ```python
    from azureml.contrib.explain.model.explanation.explanation_client import ExplanationClient
    from azureml.core.run import Run

    run = Run.get_context()
    client = ExplanationClient.from_run(run)

    # write code to get and split your data into train and test sets here
    # write code to train your model here 

    # explain predictions on your local machine
    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                x_train, 
                                features=feature_names, 
                                classes=classes)

    # explain overall model predictions (global explanation)
    global_explanation = explainer.explain_global(x_test)
    
    # uploading global model explanation data for storage or visualization in webUX
    # the explanation can then be downloaded on any compute
    # multiple explanations can be uploaded
    client.upload_model_explanation(global_explanation, comment='global explanation: all features')
    # or you can only upload the explanation object with the top k feature info
    #client.upload_model_explanation(global_explanation, top_k=2, comment='global explanation: Only top 2 features')
    ```

2. Siga as instruções em [Configurar destinos de computação para treinamento de modelo](how-to-set-up-training-targets.md#amlcompute) para saber mais sobre como configurar uma computação de Azure Machine Learning como seu destino de computação e enviar sua execução de treinamento.

3. Baixe a explicação em seu notebook Jupyter local.

    ```python
    from azureml.contrib.explain.model.explanation.explanation_client import ExplanationClient
    
    client = ExplanationClient.from_run(run)
    
    # get model explanation data
    explanation = client.download_model_explanation()
    # or only get the top k (e.g., 4) most important features with their importance values
    explanation = client.download_model_explanation(top_k=4)
    
    global_importance_values = explanation.get_ranked_global_values()
    global_importance_names = explanation.get_ranked_global_names()
    print('global importance values: {}'.format(global_importance_values))
    print('global importance names: {}'.format(global_importance_names))
    ```

## <a name="visualizations"></a>Visualizações

Use o painel de visualização para entender e interpretar seu modelo:

### <a name="global-visualizations"></a>Visualizações globais

As seguintes plotagens fornecem uma exibição global do modelo treinado junto com suas previsões e explicações.

|Gráfico|Descrição|
|----|-----------|
|Exploração de dados| Uma visão geral do conjunto de todos os valores de previsão.|
|Importância global|Mostra os principais recursos importantes do K (K configuráveis) globalmente. Esse gráfico é útil para entender o comportamento global do modelo subjacente.|
|Exploração de explicação|Demonstra como um recurso é responsável por fazer uma alteração nos valores de previsão do modelo (ou probabilidade de valores de previsão). |
|Resumo| Usa um recurso local assinado valores de importância em todos os pontos de dados para mostrar a distribuição do impacto que cada recurso tem sobre o valor de previsão.|

[![Painel global de visualização](./media/machine-learning-interpretability-explainability/global-charts.png)](./media/machine-learning-interpretability-explainability/global-charts.png#lightbox)

### <a name="local-visualizations"></a>Visualizações locais

Você pode clicar em qualquer ponto de dados individual a qualquer momento das plotagens anteriores para carregar o gráfico de importância do recurso local para o ponto de dados especificado.

|Gráfico|Descrição|
|----|-----------|
|Importância local|Mostra os principais recursos importantes do K (K configuráveis) globalmente. Esse gráfico é útil para entender o comportamento local do modelo subjacente em um ponto de dados específico.|
|Exploração de muito|Permite alterar os valores de recursos do ponto de dados selecionado e observar como essas alterações afetarão o valor de previsão.|
|Expectativa condicional individual (ICE)| Permite que você altere um valor de recurso de um valor mínimo para um valor máximo para ver como a previsão do ponto de dados é alterada quando um recurso é alterado.|

[![Importância do recurso local do painel de visualização](./media/machine-learning-interpretability-explainability/local-charts.png)](./media/machine-learning-interpretability-explainability/local-charts.png#lightbox)


[![Recurso de painel de visualização muito](./media/machine-learning-interpretability-explainability/perturbation.gif)](./media/machine-learning-interpretability-explainability/perturbation.gif#lightbox)


[![Gráficos de visualização de ICE do painel de visualização](./media/machine-learning-interpretability-explainability/ice-plot.png)](./media/machine-learning-interpretability-explainability/ice-plot.png#lightbox)

Observe que você precisará ter extensões de widget do painel de visualização habilitados antes do início do kernel Jupyter.

* Notebooks Jupyter

    ```shell
    jupyter nbextension install --py --sys-prefix azureml.contrib.explain.model.visualize
    jupyter nbextension enable --py --sys-prefix azureml.contrib.explain.model.visualize
    ```



* Jupyter Labs

    ```shell
    jupyter labextension install @jupyter-widgets/jupyterlab-manager
    jupyter labextension install microsoft-mli-widget
    ```
Para carregar o painel de visualização, use o seguinte código:

```python
from azureml.contrib.explain.model.visualize import ExplanationDashboard

ExplanationDashboard(global_explanation, model, x_test)
```

## <a name="raw-feature-transformations"></a>Transformações de recursos brutos

Opcionalmente, você pode passar seu pipeline de transformação de recursos para o explicador para receber explicações em termos dos recursos brutos antes da transformação (em vez de recursos de engenharia). Se você ignorar isso, o explicador fornecerá explicações em termos de recursos de engenharia.

O formato das transformações com suporte é o mesmo descrito em [sklearn-pandas](https://github.com/scikit-learn-contrib/sklearn-pandas). Em geral, há suporte para todas as transformações, desde que elas operem em uma única coluna e, portanto, sejam claramente uma para muitas. 

Podemos explicar os recursos brutos usando um `sklearn.compose.ColumnTransformer` ou uma lista de tuplas de transformador ajustada. A célula a seguir `sklearn.compose.ColumnTransformer`usa. 

```python
from sklearn.compose import ColumnTransformer

numeric_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='median')),
    ('scaler', StandardScaler())])

categorical_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='constant', fill_value='missing')),
    ('onehot', OneHotEncoder(handle_unknown='ignore'))])

preprocessor = ColumnTransformer(
    transformers=[
        ('num', numeric_transformer, numeric_features),
        ('cat', categorical_transformer, categorical_features)])

# append classifier to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', preprocessor),
                      ('classifier', LogisticRegression(solver='lbfgs'))])


# append classifier to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', preprocessor),
                      ('classifier', LogisticRegression(solver='lbfgs'))])


# clf.steps[-1][1] returns the trained classification model
# pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1],
                                     initialization_examples=x_train,
                                     features=dataset_feature_names,
                                     classes=dataset_classes,
                                     transformations=preprocessor)
```

Caso você queira executar o exemplo com a lista de tuplas de transformador ajustada, use o seguinte código: 
```python
from sklearn.pipeline import Pipeline
from sklearn.impute import SimpleImputer
from sklearn.preprocessing import StandardScaler, OneHotEncoder
from sklearn.linear_model import LogisticRegression
from sklearn_pandas import DataFrameMapper

# assume that we have created two arrays, numerical and categorical, which holds the numerical and categorical feature names

numeric_transformations = [([f], Pipeline(steps=[('imputer', SimpleImputer(
    strategy='median')), ('scaler', StandardScaler())])) for f in numerical]

categorical_transformations = [([f], OneHotEncoder(
    handle_unknown='ignore', sparse=False)) for f in categorical]

transformations = numeric_transformations + categorical_transformations

# append model to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', DataFrameMapper(transformations)),
                      ('classifier', LogisticRegression(solver='lbfgs'))])

# clf.steps[-1][1] returns the trained classification model
# pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1],
                                     initialization_examples=x_train,
                                     features=dataset_feature_names,
                                     classes=dataset_classes,
                                     transformations=transformations)
```

## <a name="interpretability-at-inferencing-time"></a>Interpretabilidade no momento da inferência

O explicador pode ser implantado junto com o modelo original e pode ser usado no momento da pontuação para fornecer as informações de explicação local. Também oferecemos explicadores de pontuação mais leves para tornar a interpretabilidade em tempo de inferência de mais desempenho. O processo de implantação de um explicador de Pontuação de peso mais leve é semelhante à implantação de um modelo e inclui as seguintes etapas:




1. Crie um objeto de explicação (por exemplo, usando TabularExplainer):

   ```python
   from azureml.contrib.explain.model.tabular_explainer import TabularExplainer

   explainer = TabularExplainer(model, 
                                initialization_examples=x_train, 
                                features=dataset_feature_names, 
                                classes=dataset_classes, 
                                transformations=transformations)
   ```

1. Crie um explicador de Pontuação usando o objeto de explicação:

   ```python
   from azureml.contrib.explain.model.scoring.scoring_explainer import KernelScoringExplainer, save

   # create a lightweight explainer at scoring time
   scoring_explainer = KernelScoringExplainer(explainer)

   # pickle scoring explainer
   # pickle scoring explainer locally
   OUTPUT_DIR = 'my_directory'
   save(scoring_explainer, directory=OUTPUT_DIR, exist_ok=True)
   ```

1. Configurar e registrar uma imagem que usa o modelo de explicação de pontuação.

   ```python
   # register explainer model using the path from ScoringExplainer.save - could be done on remote compute
   # scoring_explainer.pkl is the filename on disk, while my_scoring_explainer.pkl will be the filename in cloud storage
   run.upload_file('my_scoring_explainer.pkl', os.path.join(OUTPUT_DIR, 'scoring_explainer.pkl'))
   
   scoring_explainer_model = run.register_model(model_name='my_scoring_explainer', 
                                                model_path='my_scoring_explainer.pkl')
   print(scoring_explainer_model.name, scoring_explainer_model.id, scoring_explainer_model.version, sep = '\t')
   ```

1. Adicional Recuperar o explicador de pontuação da nuvem e testar as explicações

   ```python
   from azureml.contrib.explain.model.scoring.scoring_explainer import load

   # retrieve the scoring explainer model from cloud"
   scoring_explainer_model = Model(ws, 'my_scoring_explainer')
   scoring_explainer_model_path = scoring_explainer_model.download(target_dir=os.getcwd(), exist_ok=True)

   # load scoring explainer from disk
   scoring_explainer = load(scoring_explainer_model_path)

   # test scoring explainer locally
   preds = scoring_explainer.explain(x_test)
   print(preds)
   ```

1. Implantar a imagem em um destino de computação:

   1. Criar um arquivo de Pontuação (antes desta etapa, siga as etapas em [implantar modelos com o serviço de Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where) para registrar seu modelo de previsão original)

        ```python
        %%writefile score.py
        import json
        import numpy as np
        import pandas as pd
        import os
        import pickle
        from sklearn.externals import joblib
        from sklearn.linear_model import LogisticRegression
        from azureml.core.model import Model

        def init():

            global original_model
            global scoring_model

            # retrieve the path to the model file using the model name
            # assume original model is named original_prediction_model
            original_model_path = Model.get_model_path('original_prediction_model')
            scoring_explainer_path = Model.get_model_path('my_scoring_explainer')

            original_model = joblib.load(original_model_path)
            scoring_explainer = joblib.load(scoring_explainer_path)

        def run(raw_data):
            # get predictions and explanations for each data point
            data = pd.read_json(raw_data)
            # make prediction
            predictions = original_model.predict(data)
            # retrieve model explanations
            local_importance_values = scoring_explainer.explain(data)
            # you can return any data type as long as it is JSON-serializable
            return {'predictions': predictions.tolist(), 'local_importance_values': local_importance_values}
        ```

   1. Defina a configuração de implantação (essa configuração depende dos requisitos do seu modelo. O exemplo a seguir define uma configuração que usa um núcleo de CPU e 1 GB de memória)

        ```python
        from azureml.core.webservice import AciWebservice

        aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                                       memory_gb=1,
                                                       tags={"data": "NAME_OF_THE_DATASET",
                                                             "method" : "local_explanation"},
                                                       description='Get local explanations for NAME_OF_THE_PROBLEM')
        ```

   1. Criar um arquivo com dependências de ambiente

        ```python
        from azureml.core.conda_dependencies import CondaDependencies

        # WARNING: to install this, g++ needs to be available on the Docker image and is not by default (look at the next cell)

        azureml_pip_packages = ['azureml-defaults', 'azureml-contrib-explain-model', 'azureml-core', 'azureml-telemetry', 'azureml-explain-model']
 

        # specify CondaDependencies obj
        myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas'],
                                         pip_packages=['sklearn-pandas'] + azureml_pip_packages,
                                         pin_sdk_version=False)


        with open("myenv.yml","w") as f:
            f.write(myenv.serialize_to_string())

        with open("myenv.yml","r") as f:
            print(f.read())
        ```

   1. Criar um dockerfile personalizado com o g + + instalado

        ```python
        %%writefile dockerfile
        RUN apt-get update && apt-get install -y g++
        ```

   1. Implantar a imagem criada (estimativa de tempo: 5 minutos)

        ```python
        from azureml.core.webservice import Webservice
        from azureml.core.image import ContainerImage

        # use the custom scoring, docker, and conda files we created above
        image_config = ContainerImage.image_configuration(execution_script="score.py",
                                                        docker_file="dockerfile",
                                                        runtime="python",
                                                        conda_file="myenv.yml")

        # use configs and models generated above
        service = Webservice.deploy_from_model(workspace=ws,
                                            name='model-scoring-service',
                                            deployment_config=aciconfig,
                                            models=[scoring_explainer_model, original_model],
                                            image_config=image_config)

        service.wait_for_deployment(show_output=True)
        ```

1. Teste a implantação

    ```python
    import requests

    # create data to test service with
    examples = x_list[:4]
    input_data = examples.to_json()

    headers = {'Content-Type':'application/json'}

    # send request to service
    resp = requests.post(service.scoring_uri, input_data, headers=headers)

    print("POST to url", service.scoring_uri)
    # can covert back to Python objects from json string if desired
    print("prediction:", resp.text)
    ```

1. Limpar: Para excluir um serviço Web implantado, use `service.delete()`.




## <a name="next-steps"></a>Próximas etapas

Para ver uma coleção de notebooks Jupyter que demonstram as instruções acima, consulte os notebooks de [exemplo de interpretação de Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model).
