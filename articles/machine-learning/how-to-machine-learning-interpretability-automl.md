---
title: Explicabilidade em ML automatizado (visualização)
titleSuffix: Azure Machine Learning
description: Saiba como obter explicações sobre como seu modelo de ML automatizado determina a importância dos recursos e faz previsões ao usar o SDK do Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, automl, responsible-ml
ms.author: mithigpe
author: minthigpen
ms.date: 07/09/2020
ms.openlocfilehash: 709c85bed4a028c6c168c79cd9fffd6b7b40cb68
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100008036"
---
# <a name="interpretability-model-explanations-in-automated-machine-learning-preview"></a>Interpretação: explicações de modelo no machine learning automatizado (versão preliminar)



Neste artigo, você aprenderá a obter explicações para o aprendizado de máquina automatizado (AutoML) em Azure Machine Learning. O AutoML ajuda a entender a importância dos recursos dos modelos gerados. 

Todas as versões do SDK após 1.0.85 definidas `model_explainability=True` por padrão. No SDK versão 1.0.85 e versões anteriores, os usuários precisam definir `model_explainability=True` no `AutoMLConfig` objeto para usar a interpretação de modelo. 

Neste artigo, você aprenderá como:

- Execute a interpretabilidade durante o treinamento para o melhor modelo ou qualquer modelo.
- Habilite visualizações para ajudá-lo a ver padrões em dados e explicações.
- Implemente a interpretabilidade durante a inferência ou a pontuação.

## <a name="prerequisites"></a>Pré-requisitos

- Recursos de interpretação. Execute `pip install azureml-interpret` para obter o pacote necessário.
- Conhecimento da criação de experimentos de AutoML. Para obter mais informações sobre como usar o SDK do Azure Machine Learning, conclua este [tutorial do modelo de regressão](tutorial-auto-train-models.md) ou veja como [Configurar experimentos do AutoML](how-to-configure-auto-train.md).

## <a name="interpretability-during-training-for-the-best-model"></a>Interpretabilidade durante o treinamento para o melhor modelo

Recupere a explicação do `best_run` , que inclui explicações para recursos brutos e de engenharia.

> [!NOTE]
> A interpretação, a melhor explicação do modelo, não está disponível para experimentos de previsão de ML automáticos que recomendam os seguintes algoritmos como o melhor modelo: 
> * TCNForecaster
> * AutoArima
> * ExponentialSmoothing
> * Prophet
> * Média 
> * Naive
> * Média sazonal 
> * Naive sazonal

### <a name="download-the-engineered-feature-importances-from-the-best-run"></a>Baixe as importâncias de recursos com engenharia da melhor execução

Você pode usar `ExplanationClient` o para baixar as explicações de recursos de engenharia do repositório de artefatos do `best_run` . 

```python
from azureml.interpret import ExplanationClient

client = ExplanationClient.from_run(best_run)
engineered_explanations = client.download_model_explanation(raw=False)
print(engineered_explanations.get_feature_importance_dict())
```

### <a name="download-the-raw-feature-importances-from-the-best-run"></a>Baixar as importâncias do recurso bruto da melhor execução

Você pode usar `ExplanationClient` o para baixar as explicações do recurso bruto do repositório de artefatos do `best_run` .

```python
from azureml.interpret import ExplanationClient

client = ExplanationClient.from_run(best_run)
raw_explanations = client.download_model_explanation(raw=True)
print(raw_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-training-for-any-model"></a>Interpretabilidade durante o treinamento de qualquer modelo 

Ao computar explicações de modelo e visualizá-las, você não está limitado a uma explicação de modelo existente para um modelo AutoML. Você também pode obter uma explicação para seu modelo com dados de teste diferentes. As etapas nesta seção mostram como computar e visualizar a importância de recursos em engenharia com base em seus dados de teste.

### <a name="retrieve-any-other-automl-model-from-training"></a>Recuperar qualquer outro modelo de AutoML do treinamento

```python
automl_run, fitted_model = local_run.get_output(metric='accuracy')
```

### <a name="set-up-the-model-explanations"></a>Configurar as explicações do modelo

Use `automl_setup_model_explanations` para obter as explicações com engenharia e bruta. O `fitted_model` pode gerar os seguintes itens:

- Dados em destaque de amostras treinadas ou de teste
- Listas de nomes de recursos de engenharia
- Classes localizáveis em sua coluna rotulada em cenários de classificação

O `automl_explainer_setup_obj` contém todas as estruturas da lista acima.

```python
from azureml.train.automl.runtime.automl_explain_utilities import automl_setup_model_explanations

automl_explainer_setup_obj = automl_setup_model_explanations(fitted_model, X=X_train, 
                                                             X_test=X_test, y=y_train, 
                                                             task='classification')
```

### <a name="initialize-the-mimic-explainer-for-feature-importance"></a>Inicializar o explicador de imitação para a importância do recurso

Para gerar uma explicação para modelos AutoML, use a `MimicWrapper` classe. Você pode inicializar o MimicWrapper com estes parâmetros:

- O objeto de instalação do explicador
- Seu espaço de trabalho
- Um modelo substituto para explicar o `fitted_model` modelo AutoML

O MimicWrapper também usa o `automl_run` objeto no qual as explicações com engenharia serão carregadas.

```python
from azureml.interpret import MimicWrapper

# Initialize the Mimic Explainer
explainer = MimicWrapper(ws, automl_explainer_setup_obj.automl_estimator,
                         explainable_model=automl_explainer_setup_obj.surrogate_model, 
                         init_dataset=automl_explainer_setup_obj.X_transform, run=automl_run,
                         features=automl_explainer_setup_obj.engineered_feature_names, 
                         feature_maps=[automl_explainer_setup_obj.feature_map],
                         classes=automl_explainer_setup_obj.classes,
                         explainer_kwargs=automl_explainer_setup_obj.surrogate_model_params)
```

### <a name="use-mimic-explainer-for-computing-and-visualizing-engineered-feature-importance"></a>Use o explicador de imitação para computação e visualização da importância do recurso com engenharia

Você pode chamar o `explain()` método em MimicWrapper com os exemplos de teste transformados para obter a importância do recurso para os recursos de engenharia gerados. Você também pode entrar no [Azure Machine Learning Studio](https://ml.azure.com/) para exibir a visualização do painel dos valores de importância do recurso dos recursos de engenharia gerados por AutoML featurizers.

```python
engineered_explanations = explainer.explain(['local', 'global'], eval_dataset=automl_explainer_setup_obj.X_test_transform)
print(engineered_explanations.get_feature_importance_dict())
```

### <a name="use-mimic-explainer-for-computing-and-visualizing-raw-feature-importance"></a>Usar o explicador de imitação para computação e visualização da importância bruta do recurso

Você pode chamar o `explain()` método em MimicWrapper com os exemplos de teste transformados para obter a importância do recurso para os recursos brutos. No [Machine Learning Studio](https://ml.azure.com/), você pode exibir a visualização do painel dos valores de importância do recurso dos recursos brutos.

```python
raw_explanations = explainer.explain(['local', 'global'], get_raw=True,
                                     raw_feature_names=automl_explainer_setup_obj.raw_feature_names,
                                     eval_dataset=automl_explainer_setup_obj.X_test_transform,
                                     raw_eval_dataset=automl_explainer_setup_obj.X_test_raw)
print(raw_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-inference"></a>Interpretabilidade durante a inferência

Nesta seção, você aprenderá a colocar em operação um modelo AutoML com o explicador que foi usado para calcular as explicações na seção anterior.

### <a name="register-the-model-and-the-scoring-explainer"></a>Registrar o modelo e o explicador de Pontuação

Use o `TreeScoringExplainer` para criar o explicador de pontuação que calculará os valores de importância de recursos com engenharia no tempo de inferência. Você inicializa o explicador de pontuação com o `feature_map` que foi calculado anteriormente. 

Salve o explicador de Pontuação e registre o modelo e o explicador de pontuação com o Serviço Gerenciamento de Modelos. Execute o código a seguir:

```python
from azureml.interpret.scoring.scoring_explainer import TreeScoringExplainer, save

# Initialize the ScoringExplainer
scoring_explainer = TreeScoringExplainer(explainer.explainer, feature_maps=[automl_explainer_setup_obj.feature_map])

# Pickle scoring explainer locally
save(scoring_explainer, exist_ok=True)

# Register trained automl model present in the 'outputs' folder in the artifacts
original_model = automl_run.register_model(model_name='automl_model', 
                                           model_path='outputs/model.pkl')

# Register scoring explainer
automl_run.upload_file('scoring_explainer.pkl', 'scoring_explainer.pkl')
scoring_explainer_model = automl_run.register_model(model_name='scoring_explainer', model_path='scoring_explainer.pkl')
```

### <a name="create-the-conda-dependencies-for-setting-up-the-service"></a>Criar as dependências de Conda para configurar o serviço

Em seguida, crie as dependências de ambiente necessárias no contêiner para o modelo implantado. Observe que o azureml – padrões com a versão >= 1.0.45 deve ser listado como uma dependência Pip, pois ele contém a funcionalidade necessária para hospedar o modelo como um serviço Web.

```python
from azureml.core.conda_dependencies import CondaDependencies

azureml_pip_packages = [
    'azureml-interpret', 'azureml-train-automl', 'azureml-defaults'
]

myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas', 'numpy', 'py-xgboost<=0.80'],
                                 pip_packages=azureml_pip_packages,
                                 pin_sdk_version=True)

with open("myenv.yml","w") as f:
    f.write(myenv.serialize_to_string())

with open("myenv.yml","r") as f:
    print(f.read())

```

### <a name="create-the-scoring-script"></a>Criar o script de Pontuação

Escreva um script que carregue seu modelo e produza previsões e explicações com base em um novo lote de dados.

```python
%%writefile score.py
import joblib
import pandas as pd
from azureml.core.model import Model
from azureml.train.automl.runtime.automl_explain_utilities import automl_setup_model_explanations


def init():
    global automl_model
    global scoring_explainer

    # Retrieve the path to the model file using the model name
    # Assume original model is named automl_model
    automl_model_path = Model.get_model_path('automl_model')
    scoring_explainer_path = Model.get_model_path('scoring_explainer')

    automl_model = joblib.load(automl_model_path)
    scoring_explainer = joblib.load(scoring_explainer_path)


def run(raw_data):
    data = pd.read_json(raw_data, orient='records')
    # Make prediction
    predictions = automl_model.predict(data)
    # Setup for inferencing explanations
    automl_explainer_setup_obj = automl_setup_model_explanations(automl_model,
                                                                 X_test=data, task='classification')
    # Retrieve model explanations for engineered explanations
    engineered_local_importance_values = scoring_explainer.explain(automl_explainer_setup_obj.X_test_transform)
    # Retrieve model explanations for raw explanations
    raw_local_importance_values = scoring_explainer.explain(automl_explainer_setup_obj.X_test_transform, get_raw=True)
    # You can return any data type as long as it is JSON-serializable
    return {'predictions': predictions.tolist(),
            'engineered_local_importance_values': engineered_local_importance_values,
            'raw_local_importance_values': raw_local_importance_values}
```

### <a name="deploy-the-service"></a>Implantar o serviço

Implante o serviço usando o arquivo Conda e o arquivo de Pontuação das etapas anteriores.

```python
from azureml.core.webservice import Webservice
from azureml.core.webservice import AciWebservice
from azureml.core.model import Model, InferenceConfig
from azureml.core.environment import Environment

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                               memory_gb=1,
                                               tags={"data": "Bank Marketing",  
                                                     "method" : "local_explanation"},
                                               description='Get local explanations for Bank marketing test data')
myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
inference_config = InferenceConfig(entry_script="score_local_explain.py", environment=myenv)

# Use configs and models generated above
service = Model.deploy(ws,
                       'model-scoring',
                       [scoring_explainer_model, original_model],
                       inference_config,
                       aciconfig)
service.wait_for_deployment(show_output=True)
```

### <a name="inference-with-test-data"></a>Inferência com dados de teste

A inferência com alguns dados de teste para ver o valor previsto do modelo AutoML, atualmente tem suporte apenas no SDK Azure Machine Learning. Exiba as importâncias do recurso que contribuem para um valor previsto. 

```python
if service.state == 'Healthy':
    # Serialize the first row of the test data into json
    X_test_json = X_test[:1].to_json(orient='records')
    print(X_test_json)
    # Call the service to get the predictions and the engineered explanations
    output = service.run(X_test_json)
    # Print the predicted value
    print(output['predictions'])
    # Print the engineered feature importances for the predicted value
    print(output['engineered_local_importance_values'])
    # Print the raw feature importances for the predicted value
    print('raw_local_importance_values:\n{}\n'.format(output['raw_local_importance_values']))
```

### <a name="visualize-to-discover-patterns-in-data-and-explanations-at-training-time"></a>Visualizar para descobrir padrões em dados e explicações no tempo de treinamento

Você pode visualizar o gráfico de importância do recurso em seu espaço de trabalho em [Machine Learning Studio](https://ml.azure.com). Depois que a execução do AutoML for concluída, selecione **Exibir detalhes do modelo** para exibir uma execução específica. Selecione a guia **explicações** para ver o painel de visualização de explicação.

[![Arquitetura de interpretação de Machine Learning](./media/how-to-machine-learning-interpretability-automl/automl-explanation.png)](./media/how-to-machine-learning-interpretability-automl/automl-explanation.png#lightbox)

Para obter mais informações sobre as visualizações do painel de explicação e plotagens específicas, consulte o [documento de instruções sobre interpretação](how-to-machine-learning-interpretability-aml.md).

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como você pode habilitar as explicações do modelo e a importância dos recursos em áreas do SDK do Azure Machine Learning diferentes do Machine Learning automatizado, consulte o [artigo conceito sobre interpretação](how-to-machine-learning-interpretability.md).
