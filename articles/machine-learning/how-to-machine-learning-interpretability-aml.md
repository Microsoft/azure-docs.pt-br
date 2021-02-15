---
title: Interpretar & explicar os modelos de ML no Python (versão prévia)
titleSuffix: Azure Machine Learning
description: Saiba como obter explicações sobre como seu modelo de aprendizado de máquina determina a importância do recurso e faz previsões ao usar o SDK do Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: mithigpe
author: minthigpen
ms.reviewer: Luis.Quintanilla
ms.date: 07/09/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, responsible-ml
ms.openlocfilehash: fda1bc2ef0a112a8a32ba7c4caebf29028c8cdd7
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222744"
---
# <a name="use-the-interpretability-package-to-explain-ml-models--predictions-in-python-preview"></a>Usar o pacote de interpretação para explicar os modelos de ML & previsões no Python (versão prévia)



Neste guia de instruções, você aprende a usar o pacote de interpretação do SDK do Azure Machine Learning Python para executar as seguintes tarefas:


* Explique o comportamento do modelo inteiro ou previsões individuais em seu computador pessoal localmente.

* Habilite técnicas de interpretação para recursos de engenharia.

* Explique o comportamento de todo o modelo e previsões individuais no Azure.

* Use um painel de visualização para interagir com as explicações do modelo.

* Implante um explicador de Pontuação junto com seu modelo para observar explicações durante o inferência.



Para obter mais informações sobre as técnicas de interpretação com suporte e os modelos de aprendizado de máquina, consulte [interpretação de modelo em Azure Machine Learning](how-to-machine-learning-interpretability.md) e [notebooks de exemplo](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model).

## <a name="generate-feature-importance-value-on-your-personal-machine"></a>Gerar valor de importância do recurso em seu computador pessoal 
O exemplo a seguir mostra como usar o pacote de interpretação em seu computador pessoal sem contatar os serviços do Azure.

1. Instale o pacote `azureml-interpret`.
    ```bash
    pip install azureml-interpret
    ```

2. Treine um modelo de exemplo em um Jupyter Notebook local.

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

3. Chame o explicador localmente.
   * Para inicializar um objeto de explicador, passe seu modelo e alguns dados de treinamento para o construtor do explicador.
   * Para tornar suas explicações e visualizações mais informativas, você pode optar por passar nomes de recursos e nomes de classe de saída se estiver fazendo a classificação.

   Os blocos de código a seguir mostram como criar uma instância de um objeto de explicador com `TabularExplainer` , `MimicExplainer` e `PFIExplainer` localmente.
   * `TabularExplainer` chama um dos três explicadores SHAP abaixo ( `TreeExplainer` , `DeepExplainer` , ou `KernelExplainer` ).
   * `TabularExplainer` o seleciona automaticamente o mais apropriado para seu caso de uso, mas você pode chamar cada um dos seus três explicadores subjacentes diretamente.

    ```python
    from interpret.ext.blackbox import TabularExplainer

    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=breast_cancer_data.feature_names, 
                                 classes=classes)
    ```

    ou

    ```python

    from interpret.ext.blackbox import MimicExplainer
    
    # you can use one of the following four interpretable models as a global surrogate to the black box model
    
    from interpret.ext.glassbox import LGBMExplainableModel
    from interpret.ext.glassbox import LinearExplainableModel
    from interpret.ext.glassbox import SGDExplainableModel
    from interpret.ext.glassbox import DecisionTreeExplainableModel

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
    from interpret.ext.blackbox import PFIExplainer

    # "features" and "classes" fields are optional
    explainer = PFIExplainer(model,
                             features=breast_cancer_data.feature_names, 
                             classes=classes)
    ```

### <a name="explain-the-entire-model-behavior-global-explanation"></a>Explique o comportamento do modelo inteiro (explicação global) 

Consulte o exemplo a seguir para ajudá-lo a obter os valores de importância do recurso agregado (global).

```python

# you can use the training data or the test data here, but test data would allow you to use Explanation Exploration
global_explanation = explainer.explain_global(x_test)

# if you used the PFIExplainer in the previous step, use the next line of code instead
# global_explanation = explainer.explain_global(x_train, true_labels=y_train)

# sorted feature importance values and feature names
sorted_global_importance_values = global_explanation.get_ranked_global_values()
sorted_global_importance_names = global_explanation.get_ranked_global_names()
dict(zip(sorted_global_importance_names, sorted_global_importance_values))

# alternatively, you can print out a dictionary that holds the top K feature names and values
global_explanation.get_feature_importance_dict()
```

### <a name="explain-an-individual-prediction-local-explanation"></a>Explicar uma previsão individual (explicação local)
Obtenha os valores de importância de um recurso individual de diferentes pontos de extremidade chamando explicações para uma instância individual ou um grupo de instâncias.
> [!NOTE]
> `PFIExplainer` não oferece suporte a explicações locais.

```python
# get explanation for the first data point in the test set
local_explanation = explainer.explain_local(x_test[0:5])

# sorted feature importance values and feature names
sorted_local_importance_names = local_explanation.get_ranked_local_names()
sorted_local_importance_values = local_explanation.get_ranked_local_values()
```

### <a name="raw-feature-transformations"></a>Transformações de recursos brutos

Você pode optar por obter explicações em termos de recursos brutos e não transformados, em vez de recursos de engenharia. Para essa opção, você passa seu pipeline de transformação de recursos para o explicador no `train_explain.py` . Caso contrário, o explicador fornece explicações em termos de recursos de engenharia.

O formato das transformações com suporte é o mesmo descrito em [sklearn-pandas](https://github.com/scikit-learn-contrib/sklearn-pandas). Em geral, há suporte para todas as transformações, desde que elas operem em uma única coluna, para que fique claro que elas são um-para-muitos.

Obtenha uma explicação para recursos brutos usando um `sklearn.compose.ColumnTransformer` ou com uma lista de tuplas de transformador ajustadas. O exemplo a seguir usa `sklearn.compose.ColumnTransformer` .

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

## <a name="generate-feature-importance-values-via-remote-runs"></a>Gerar valores de importância de recurso por meio de execuções remotas

O exemplo a seguir mostra como você pode usar a `ExplanationClient` classe para habilitar a interpretação de modelo para execuções remotas. Ele é conceitualmente semelhante ao processo local, exceto que você:

* Use o `ExplanationClient` na execução remota para carregar o contexto de interpretação.
* Baixe o contexto posteriormente em um ambiente local.

1. Instale o pacote `azureml-interpret`.
    ```bash
    pip install azureml-interpret
    ```
1. Crie um script de treinamento em um Jupyter Notebook local. Por exemplo, `train_explain.py`.

    ```python
    from azureml.interpret import ExplanationClient
    from azureml.core.run import Run
    from interpret.ext.blackbox import TabularExplainer

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

1. Configure um Azure Machine Learning computação como seu destino de computação e envie sua execução de treinamento. Consulte [criar e gerenciar Azure Machine Learning clusters de computação](how-to-create-attach-compute-cluster.md) para obter instruções. Você também pode encontrar os [blocos de anotações de exemplo](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model/azure-integration/remote-explanation) úteis.

1. Baixe a explicação em seu Jupyter Notebook local.

    ```python
    from azureml.interpret import ExplanationClient
    
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

Depois de baixar as explicações no Jupyter Notebook local, você pode usar o painel de visualização para entender e interpretar seu modelo. Para carregar o widget Painel de visualização em seu Jupyter Notebook, use o seguinte código:

```python
from interpret_community.widget import ExplanationDashboard

ExplanationDashboard(global_explanation, model, datasetX=x_test)
```

A visualização dá suporte a explicações em recursos com engenharia e bruta. As explicações brutas baseiam-se nos recursos do conjunto de base original e explicações criadas com base nos recursos do conjunto de um com a engenharia de recursos aplicada.

Ao tentar interpretar um modelo em relação ao conjunto de recursos original, é recomendável usar explicações brutas, uma vez que cada importância de recurso corresponderá a uma coluna do conjunto de recursos original. Um cenário em que explicações projetadas pode ser útil é ao examinar o impacto de categorias individuais de um recurso categórico. Se uma codificação One-Hot for aplicada a um recurso categórico, as explicações convertidas resultantes incluirão um valor de importância diferente por categoria, um por recurso de engenharia única. Isso pode ser útil ao restringir a qual parte do conjunto de DataSet é mais informativa para o modelo.

> [!NOTE]
> As explicações de engenharia e bruta são calculadas sequencialmente. Primeiro, uma explicação com engenharia é criada com base no modelo e no pipeline personalização. Em seguida, a explicação bruta é criada com base nessa explicação projetada agregando a importância dos recursos de engenharia que vieram do mesmo recurso bruto.

### <a name="create-edit-and-view-dataset-cohorts"></a>Criar, editar e exibir conjuntos de coortes de conjunto de um

A faixa de faixas superior mostra as estatísticas gerais em seu modelo e dados. Você pode dividir os dados em uma fatia e nos subgrupos coortes, para investigar ou comparar o desempenho e as explicações do modelo entre esses subgrupos definidos. Comparando as estatísticas e explicações do conjunto de acordo com esses subgrupos, você pode ter uma noção de por que possíveis erros estão ocorrendo em um grupo em vez de outro.

[![Criando, editando e exibindo DataSet coortes](./media/how-to-machine-learning-interpretability-aml/dataset-cohorts.gif)](./media/how-to-machine-learning-interpretability-aml/dataset-cohorts.gif#lightbox)

### <a name="understand-entire-model-behavior-global-explanation"></a>Entender todo o comportamento do modelo (explicação global) 

As primeiras três guias do painel explicação fornecem uma análise geral do modelo treinado junto com suas previsões e explicações.

#### <a name="model-performance"></a>Desempenho do modelo
Avalie o desempenho de seu modelo explorando a distribuição de seus valores de previsão e os valores de suas métricas de desempenho de modelo. Você pode investigar melhor seu modelo examinando uma análise comparativa de seu desempenho em diferentes coortes ou subgrupos de seu conjunto de informações. Selecione Filtros ao longo de valor y e x-Value para cortar entre diferentes dimensões. Exibir métricas como precisão, precisão, recall, para (taxa de falsos positivos) e FNR (taxa negativa de falsos).

[![Guia desempenho do modelo na visualização explicação](./media/how-to-machine-learning-interpretability-aml/model-performance.gif)](./media/how-to-machine-learning-interpretability-aml/model-performance.gif#lightbox)

#### <a name="dataset-explorer"></a>Gerenciador de conjuntos de conjunto
Explore as estatísticas do conjunto de dados selecionando filtros diferentes nos eixos X, Y e cor para dividir os data junto com dimensões diferentes. Crie DataSet coortes acima para analisar estatísticas de conjunto de resultados com filtros como resultado previsto, recursos de conjunto de e grupos de erros. Use o ícone de engrenagem no canto superior direito do grafo para alterar os tipos de grafo.

[![Guia DataSet Explorer na visualização de explicação](./media/how-to-machine-learning-interpretability-aml/dataset-explorer.gif)](./media/how-to-machine-learning-interpretability-aml/dataset-explorer.gif#lightbox)

#### <a name="aggregate-feature-importance"></a>Importância agregada do recurso
Explore os principais recursos importantes que afetam as previsões gerais do modelo (também conhecida como explicação global). Use o controle deslizante para mostrar valores de importância de recurso decrescente. Selecione até três coortes para ver os valores de importância do recurso lado a lado. Clique em qualquer uma das barras de recursos no grafo para ver como os valores do recurso selecionado afetam a previsão do modelo no gráfico de dependência abaixo.

[![Guia importância de agregação de recurso na visualização explicação](./media/how-to-machine-learning-interpretability-aml/aggregate-feature-importance.gif)](./media/how-to-machine-learning-interpretability-aml/aggregate-feature-importance.gif#lightbox)

### <a name="understand-individual-predictions-local-explanation"></a>Entender previsões individuais (explicação local) 

A quarta guia da guia explicação permite que você faça drill-through de um DataPoint individual e suas importâncias de recursos individuais. Você pode carregar o gráfico de importância de recurso individual para qualquer ponto de dados clicando em qualquer um dos pontos de dados individuais no gráfico de dispersão principal ou selecionando um DataPoint específico no assistente de painel à direita.

|Plotar|Description|
|----|-----------|
|Importância de recurso individual|Mostra os principais recursos importantes para uma previsão individual. Ajuda a ilustrar o comportamento local do modelo subjacente em um ponto de dados específico.|
|Análise de What-If|Permite alterações nos valores de recursos do ponto de dados real selecionado e observa as alterações resultantes no valor de previsão, gerando um DataPoint hipotético com os novos valores de recurso.|
|Expectativa condicional individual (ICE)|Permite alterações de valor de recurso de um valor mínimo para um valor máximo. Ajuda a ilustrar como a previsão do ponto de dados é alterada quando um recurso é alterado.|

[![Importância de recurso individual e guia What-If no painel de explicação](./media/how-to-machine-learning-interpretability-aml/individual-tab.gif)](./media/how-to-machine-learning-interpretability-aml/individual-tab.gif#lightbox)

> [!NOTE]
> Essas são explicações baseadas em muitas aproximações e não são a "causa" de previsões. Sem a solidez matemática estrita da inferência de causal, não aconselhamos os usuários a tomar decisões reais com base no recurso perturbations da ferramenta de What-If. Essa ferramenta é principalmente para entender o modelo e a depuração.

### <a name="visualization-in-azure-machine-learning-studio"></a>Visualização no Azure Machine Learning Studio

Se você concluir as etapas de [interpretação remota](how-to-machine-learning-interpretability-aml.md#generate-feature-importance-values-via-remote-runs) (carregando uma explicação gerada para Azure Machine Learning histórico de execução), poderá exibir o painel de visualização no [Azure Machine Learning Studio](https://ml.azure.com). Este painel é uma versão mais simples do painel de visualização explicado acima. What-If as plotagens de geração de DataPoint e ICE estão desabilitadas, pois não há computação ativa no Azure Machine Learning Studio que possa executar cálculos em tempo real.

Se as explicações de conjunto de dados, global e local estiverem disponíveis, os dados preencherão todas as guias. Se apenas uma explicação global estiver disponível, a guia importância do recurso individual será desabilitada.

Siga um destes caminhos para acessar o painel de visualização no Azure Machine Learning Studio:

* Painel de **experimentos** (versão prévia)
  1. Selecione **experimentos** no painel esquerdo para ver uma lista de experimentos que você executou em Azure Machine Learning.
  1. Selecione um experimento específico para exibir todas as execuções nesse experimento.
  1. Selecione uma execução e, em seguida, a guia **explicações** para o painel de visualização da explicação.

   [![Painel de visualização com importância de recurso agregado no AzureML Studio em experimentos](./media/how-to-machine-learning-interpretability-aml/model-explanation-dashboard-aml-studio.png)](./media/how-to-machine-learning-interpretability-aml/model-explanation-dashboard-aml-studio.png#lightbox)

* Painel de **modelos**
  1. Se você registrou seu modelo original seguindo as etapas em [implantar modelos com Azure Machine Learning](./how-to-deploy-and-where.md), você pode selecionar **modelos** no painel esquerdo para exibi-lo.
  1. Selecione um modelo e, em seguida, a guia **explicações** para exibir o painel de visualização da explicação.

## <a name="interpretability-at-inference-time"></a>Interpretabilidade no tempo de inferência

Você pode implantar o explicador junto com o modelo original e usá-lo no momento da inferência para fornecer os valores de importância do recurso individual (explicação local) para qualquer novo DataPoint. Também oferecemos explicadores de pontuação mais leves para melhorar o desempenho de interpretação no momento da inferência, que atualmente tem suporte apenas no SDK Azure Machine Learning. O processo de implantação de um explicador de Pontuação de peso mais leve é semelhante à implantação de um modelo e inclui as seguintes etapas:

1. Crie um objeto de explicação. Por exemplo, você pode usar `TabularExplainer` :

   ```python
    from interpret.ext.blackbox import TabularExplainer


   explainer = TabularExplainer(model, 
                                initialization_examples=x_train, 
                                features=dataset_feature_names, 
                                classes=dataset_classes, 
                                transformations=transformations)
   ```

1. Crie um explicador de pontuação com o objeto de explicação.

   ```python
   from azureml.interpret.scoring.scoring_explainer import KernelScoringExplainer, save

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

1. Como uma etapa opcional, você pode recuperar o explicador de pontuação da nuvem e testar as explicações.

   ```python
   from azureml.interpret.scoring.scoring_explainer import load

   # retrieve the scoring explainer model from cloud"
   scoring_explainer_model = Model(ws, 'my_scoring_explainer')
   scoring_explainer_model_path = scoring_explainer_model.download(target_dir=os.getcwd(), exist_ok=True)

   # load scoring explainer from disk
   scoring_explainer = load(scoring_explainer_model_path)

   # test scoring explainer locally
   preds = scoring_explainer.explain(x_test)
   print(preds)
   ```

1. Implante a imagem em um destino de computação, seguindo estas etapas:

   1. Se necessário, Registre seu modelo de previsão original seguindo as etapas em [implantar modelos com Azure Machine Learning](./how-to-deploy-and-where.md).

   1. Crie um arquivo de pontuação.

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
   1. Defina a configuração de implantação.

         Essa configuração depende dos requisitos do seu modelo. O exemplo a seguir define uma configuração que usa um núcleo de CPU e um GB de memória.

         ```python
         from azureml.core.webservice import AciWebservice

          aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                                    memory_gb=1,
                                                    tags={"data": "NAME_OF_THE_DATASET",
                                                          "method" : "local_explanation"},
                                                    description='Get local explanations for NAME_OF_THE_PROBLEM')
         ```

   1. Crie um arquivo com dependências de ambiente.

         ```python
         from azureml.core.conda_dependencies import CondaDependencies

         # WARNING: to install this, g++ needs to be available on the Docker image and is not by default (look at the next cell)

         azureml_pip_packages = ['azureml-defaults', 'azureml-core', 'azureml-telemetry', 'azureml-interpret']
 

         # specify CondaDependencies obj
         myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas'],
                                          pip_packages=['sklearn-pandas'] + azureml_pip_packages,
                                          pin_sdk_version=False)


         with open("myenv.yml","w") as f:
            f.write(myenv.serialize_to_string())

         with open("myenv.yml","r") as f:
            print(f.read())
         ```

   1. Crie um dockerfile personalizado com o g + + instalado.

         ```python
         %%writefile dockerfile
         RUN apt-get update && apt-get install -y g++
         ```

   1. Implante a imagem criada.
   
         Esse processo leva aproximadamente cinco minutos.

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

1. Teste a implantação.

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

1. Limpar.

   Para excluir um serviço Web implantado, use `service.delete()`.

## <a name="troubleshooting"></a>Solução de problemas

* **Dados esparsos sem suporte**: o modelo explica a explicação do painel/diminui substancialmente com um grande número de recursos, portanto, no momento, não há suporte para o formato de dados esparsos. Além disso, problemas gerais de memória surgirão com grandes conjuntos de altos e um grande número de recursos. 

* **Modelos de previsão sem suporte com explicações de modelo**: interpretação, melhor explicação de modelo, não está disponível para experimentos de previsão AutoML que recomendam os seguintes algoritmos como o melhor modelo: TCNForecaster, AutoArima, Prophet, ExponentialSmoothing, Average, Naive, média sazonal e Naive sazonal. A previsão de AutoML tem modelos de regressão que dão suporte a explicações. No entanto, no painel explicação, a guia "importância de recurso individual" não tem suporte apenas para previsão devido à complexidade em seus pipelines de dados.

* **Explicação local para o índice de dados**: o painel de explicação não oferece suporte à relação de valores de importância local para um identificador de linha do DataSet de validação original se esse conjunto de dados for maior do que 5000 pontos de dados como o Dashboard downsamples aleatoriamente. No entanto, o painel mostra os valores de recurso de conjunto de forma bruto para cada DataPoint passado para o painel na guia importância de recurso individual. Os usuários podem mapear as importâncias locais de volta para o conjunto de recursos original, por meio da correspondência dos valores de recurso de DataSet bruto. Se o tamanho do conjunto de recursos de validação for inferior a 5000 amostras, o `index` recurso no AzureML Studio corresponderá ao índice no conjunto de recursos de validação.

* **Não há suporte para plotagens What-If/Ice no Studio**: What-If e em gráficos de expectativa condicional (ICE) individuais no Azure Machine Learning Studio na guia explicações, uma vez que a explicação carregada precisa de uma computação ativa para recalcular previsões e probabilidades de recursos de perturbed. No momento, ele tem suporte em notebooks Jupyter quando executado como um widget usando o SDK.


## <a name="next-steps"></a>Próximas etapas

[Saiba mais sobre a interpretação de modelo](how-to-machine-learning-interpretability.md)

[Confira Azure Machine Learning notebooks de exemplo de interpretação](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)
