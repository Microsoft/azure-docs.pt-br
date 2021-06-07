---
title: Avaliar a imparcialidade dos modelos de ML no Python (visualização)
titleSuffix: Azure Machine Learning
description: Saiba como avaliar e mitigar a imparcialidade de seus modelos de aprendizado de máquina usando o Fairlearn e o SDK Azure Machine Learning Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: mesameki
author: mesameki
ms.reviewer: luquinta
ms.date: 11/16/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, responsible-ml
ms.openlocfilehash: 70ad5d6d88dabb51e022a1fc5c011341b06f02fd
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105640684"
---
# <a name="use-azure-machine-learning-with-the-fairlearn-open-source-package-to-assess-the-fairness-of-ml-models-preview"></a>Usar Azure Machine Learning com o pacote Fairlearn Open-Source para avaliar a imparcialidade dos modelos de ML (versão prévia)

Neste guia de instruções, você aprenderá a usar o pacote Python de software livre [Fairlearn](https://fairlearn.github.io/) com Azure Machine Learning para executar as seguintes tarefas:

* Avalie a imparcialidade de suas previsões de modelo. Para saber mais sobre a imparcialidade no aprendizado de máquina, confira a [imparcialidade no artigo Machine Learning](concept-fairness-ml.md).
* Carregue, liste e baixe informações de avaliação de imparcialidade de/para o Azure Machine Learning Studio.
* Consulte um painel de avaliação de imparcialidade no Azure Machine Learning Studio para interagir com os insights de imparcial de seu (s) modelo.

>[!NOTE]
> A avaliação de imparcialidade não é um exercício puramente técnico. **Esse pacote pode ajudá-lo a avaliar a imparcialidade de um modelo de aprendizado de máquina, mas apenas você pode configurar e tomar decisões quanto à forma como o modelo é executado.**  Embora esse pacote ajude a identificar métricas quantitativas para avaliar a imparcialidade, os desenvolvedores de modelos de aprendizado de máquina também devem executar uma análise qualitativa para avaliar a imparcialidade de seus próprios modelos.

## <a name="azure-machine-learning-fairness-sdk"></a>SDK de imparcialidade Azure Machine Learning 

O SDK de imparcialidade Azure Machine Learning, `azureml-contrib-fairness` , integra o pacote Python de software livre, [Fairlearn](http://fairlearn.github.io), em Azure Machine Learning. Para saber mais sobre a integração do Fairlearn no Azure Machine Learning, confira estes [blocos de anotações de exemplo](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness). Para obter mais informações sobre o Fairlearn, consulte o [Guia de exemplo](https://fairlearn.org/v0.6.0/auto_examples/) e os [notebooks de exemplo](https://github.com/fairlearn/fairlearn/tree/master/notebooks). 

Use os seguintes comandos para instalar os `azureml-contrib-fairness` `fairlearn` pacotes e:
```bash
pip install azureml-contrib-fairness
pip install fairlearn==0.4.6
```
As versões posteriores do Fairlearn também devem funcionar no código de exemplo a seguir.



## <a name="upload-fairness-insights-for-a-single-model"></a>Carregar informações de imparcialidade para um único modelo

O exemplo a seguir mostra como usar o pacote de imparcialidade. Vamos carregar informações de imparcialidade de modelo no Azure Machine Learning e ver o painel de avaliação de imparcialidade no Azure Machine Learning Studio.

1. Treine um modelo de exemplo no Jupyter Notebook. 

    Para o conjunto de um, usamos o conjunto de censo adulto conhecido, que buscamos de OpenML. Vamos fingir que temos um problema de decisão de empréstimo com o rótulo indicando se um indivíduo pagou um empréstimo anterior. Treinaremos um modelo para prever se indivíduos anteriormente não vistos indenizarm um empréstimo. Esse modelo pode ser usado para tomar decisões de empréstimo.

    ```python
    import copy
    import numpy as np
    import pandas as pd

    from sklearn.compose import ColumnTransformer
    from sklearn.datasets import fetch_openml
    from sklearn.impute import SimpleImputer
    from sklearn.linear_model import LogisticRegression
    from sklearn.model_selection import train_test_split
    from sklearn.preprocessing import StandardScaler, OneHotEncoder
    from sklearn.compose import make_column_selector as selector
    from sklearn.pipeline import Pipeline
    
    from fairlearn.widget import FairlearnDashboard

    # Load the census dataset
    data = fetch_openml(data_id=1590, as_frame=True)
    X_raw = data.data
    y = (data.target == ">50K") * 1
    
    # (Optional) Separate the "sex" and "race" sensitive features out and drop them from the main data prior to training your model
    X_raw = data.data
    y = (data.target == ">50K") * 1
    A = X_raw[["race", "sex"]]
    X = X_raw.drop(labels=['sex', 'race'],axis = 1)
    
    # Split the data in "train" and "test" sets
    (X_train, X_test, y_train, y_test, A_train, A_test) = train_test_split(
        X_raw, y, A, test_size=0.3, random_state=12345, stratify=y
    )

    # Ensure indices are aligned between X, y and A,
    # after all the slicing and splitting of DataFrames
    # and Series
    X_train = X_train.reset_index(drop=True)
    X_test = X_test.reset_index(drop=True)
    y_train = y_train.reset_index(drop=True)
    y_test = y_test.reset_index(drop=True)
    A_train = A_train.reset_index(drop=True)
    A_test = A_test.reset_index(drop=True)

    # Define a processing pipeline. This happens after the split to avoid data leakage
    numeric_transformer = Pipeline(
        steps=[
            ("impute", SimpleImputer()),
            ("scaler", StandardScaler()),
        ]
    )
    categorical_transformer = Pipeline(
        [
            ("impute", SimpleImputer(strategy="most_frequent")),
            ("ohe", OneHotEncoder(handle_unknown="ignore")),
        ]
    )
    preprocessor = ColumnTransformer(
        transformers=[
            ("num", numeric_transformer, selector(dtype_exclude="category")),
            ("cat", categorical_transformer, selector(dtype_include="category")),
        ]
    )

    # Put an estimator onto the end of the pipeline
    lr_predictor = Pipeline(
        steps=[
            ("preprocessor", copy.deepcopy(preprocessor)),
            (
                "classifier",
                LogisticRegression(solver="liblinear", fit_intercept=True),
            ),
        ]
    )

    # Train the model on the test data
    lr_predictor.fit(X_train, y_train)

    # (Optional) View this model in Fairlearn's fairness dashboard, and see the disparities which appear:
    from fairlearn.widget import FairlearnDashboard
    FairlearnDashboard(sensitive_features=A_test, 
                       sensitive_feature_names=['Race', 'Sex'],
                       y_true=y_test,
                       y_pred={"lr_model": lr_predictor.predict(X_test)})
    ```

2. Faça logon em Azure Machine Learning e Registre seu modelo.
   
    O painel de imparcialidade pode ser integrado a modelos registrados ou não registrados. Registre seu modelo no Azure Machine Learning com as seguintes etapas:
    ```python
    from azureml.core import Workspace, Experiment, Model
    import joblib
    import os
    
    ws = Workspace.from_config()
    ws.get_details()

    os.makedirs('models', exist_ok=True)
    
    # Function to register models into Azure Machine Learning
    def register_model(name, model):
        print("Registering ", name)
        model_path = "models/{0}.pkl".format(name)
        joblib.dump(value=model, filename=model_path)
        registered_model = Model.register(model_path=model_path,
                                        model_name=name,
                                        workspace=ws)
        print("Registered ", registered_model.id)
        return registered_model.id

    # Call the register_model function 
    lr_reg_id = register_model("fairness_logistic_regression", lr_predictor)
    ```

3. Métricas de imparcialidade pré-calcular.

    Crie um dicionário de painel usando o `metrics` pacote do Fairlearn. O `_create_group_metric_set` método tem argumentos semelhantes ao construtor do painel, exceto que os recursos confidenciais são passados como um dicionário (para garantir que os nomes estejam disponíveis). Também devemos especificar o tipo de previsão (classificação binária, neste caso) ao chamar esse método.

    ```python
    #  Create a dictionary of model(s) you want to assess for fairness 
    sf = { 'Race': A_test.race, 'Sex': A_test.sex}
    ys_pred = { lr_reg_id:lr_predictor.predict(X_test) }
    from fairlearn.metrics._group_metric_set import _create_group_metric_set

    dash_dict = _create_group_metric_set(y_true=y_test,
                                        predictions=ys_pred,
                                        sensitive_features=sf,
                                        prediction_type='binary_classification')
    ```
4. Carregue as métricas de integridade em computação.
    
    Agora, importe `azureml.contrib.fairness` o pacote para executar o upload:

    ```python
    from azureml.contrib.fairness import upload_dashboard_dictionary, download_dashboard_by_upload_id
    ```
    Crie um experimento, execute uma execução e carregue o painel:
    ```python
    exp = Experiment(ws, "Test_Fairness_Census_Demo")
    print(exp)

    run = exp.start_logging()

    # Upload the dashboard to Azure Machine Learning
    try:
        dashboard_title = "Fairness insights of Logistic Regression Classifier"
        # Set validate_model_ids parameter of upload_dashboard_dictionary to False if you have not registered your model(s)
        upload_id = upload_dashboard_dictionary(run,
                                                dash_dict,
                                                dashboard_name=dashboard_title)
        print("\nUploaded to id: {0}\n".format(upload_id))
        
        # To test the dashboard, you can download it back and ensure it contains the right information
        downloaded_dict = download_dashboard_by_upload_id(run, upload_id)
    finally:
        run.complete()
    ```
5. Verifique o painel de imparcialidade do Azure Machine Learning Studio

    Se você concluir as etapas anteriores (carregando informações de imparcialidade geradas para Azure Machine Learning), poderá exibir o painel de imparcialidade no [Azure Machine Learning Studio](https://ml.azure.com). Esse painel é o mesmo painel de visualização fornecido no Fairlearn, permitindo que você analise as diferenças entre os subgrupos de seus recursos confidenciais (por exemplo, masculino vs. fêmea).
    Siga um destes caminhos para acessar o painel de visualização no Azure Machine Learning Studio:

    * **Painel de experimentos (versão prévia)**
    1. Selecione **experimentos** no painel esquerdo para ver uma lista de experimentos que você executou em Azure Machine Learning.
    1. Selecione um experimento específico para exibir todas as execuções nesse experimento.
    1. Selecione uma execução e, em seguida, a guia **imparcialidade** para o painel de visualização da explicação.
    1. Após o pouso na guia **Imparciality** , clique em uma **ID de imparcialidade** no menu à direita.
    1. Configure seu painel selecionando seu atributo confidencial, a métrica de desempenho e a métrica de imparcialidade de interesse para chegar à página de avaliação de imparcialidade.
    1. Alterne o tipo de gráfico de um para outro para observar os danos de **alocação** e a **qualidade dos danos de serviço** .



    [![Alocação do painel de imparcialidade](./media/how-to-machine-learning-fairness-aml/dashboard-1.png)](./media/how-to-machine-learning-fairness-aml/dashboard-1.png#lightbox)
    
    [![Qualidade de serviço do painel de imparcialidade](./media/how-to-machine-learning-fairness-aml/dashboard-2.png)](./media/how-to-machine-learning-fairness-aml/dashboard-2.png#lightbox)
    * **Painel de modelos**
    1. Se você registrou seu modelo original seguindo as etapas anteriores, você pode selecionar **modelos** no painel esquerdo para exibi-lo.
    1. Selecione um modelo e, em seguida, a guia **imparcialidade** para exibir o painel de visualização de explicação.

    Para saber mais sobre o painel de visualização e o que ele contém, confira o [Guia do usuário](https://fairlearn.org/v0.6.0/user_guide/assessment.html#fairlearn-dashboard)do Fairlearn.

## <a name="upload-fairness-insights-for-multiple-models"></a>Carregar informações de imparcialidade para vários modelos

Para comparar vários modelos e ver como suas avaliações de imparcialidade diferem, você pode passar mais de um modelo para o painel de visualização e comparar suas compensações de integridade de desempenho.

1. Treine seus modelos:
    
    Agora, criamos um segundo classificador, com base em um avaliador de máquina de vetor de suporte e carregamos um dicionário de painel de imparcialidade usando o pacote do Fairlearn `metrics` . Supomos que o modelo treinado anteriormente ainda está disponível.


    ```python
    # Put an SVM predictor onto the preprocessing pipeline
    from sklearn import svm
    svm_predictor = Pipeline(
        steps=[
            ("preprocessor", copy.deepcopy(preprocessor)),
            (
                "classifier",
                svm.SVC(),
            ),
        ]
    )

    # Train your second classification model
    svm_predictor.fit(X_train, y_train)
    ```

2. Registrar seus modelos

    Em seguida, registre os dois modelos dentro de Azure Machine Learning. Para sua conveniência, armazene os resultados em um dicionário, que mapeia o `id` do modelo registrado (uma cadeia de caracteres no `name:version` formato) para o próprio pregnóstico:

    ```python
    model_dict = {}

    lr_reg_id = register_model("fairness_logistic_regression", lr_predictor)
    model_dict[lr_reg_id] = lr_predictor

    svm_reg_id = register_model("fairness_svm", svm_predictor)
    model_dict[svm_reg_id] = svm_predictor
    ```

3. Carregar o painel do Fairlearn localmente

    Antes de carregar as informações de imparcialidade no Azure Machine Learning, você pode examinar essas previsões em um painel Fairlearn invocado localmente. 



    ```python
    #  Generate models' predictions and load the fairness dashboard locally 
    ys_pred = {}
    for n, p in model_dict.items():
        ys_pred[n] = p.predict(X_test)

    from fairlearn.widget import FairlearnDashboard

    FairlearnDashboard(sensitive_features=A_test, 
                    sensitive_feature_names=['Race', 'Sex'],
                    y_true=y_test.tolist(),
                    y_pred=ys_pred)
    ```

3. Métricas de imparcialidade pré-calcular.

    Crie um dicionário de painel usando o `metrics` pacote do Fairlearn.

    ```python
    sf = { 'Race': A_test.race, 'Sex': A_test.sex }

    from fairlearn.metrics._group_metric_set import _create_group_metric_set

    dash_dict = _create_group_metric_set(y_true=Y_test,
                                        predictions=ys_pred,
                                        sensitive_features=sf,
                                        prediction_type='binary_classification')
    ```
4. Carregue as métricas de integridade em computação.
    
    Agora, importe `azureml.contrib.fairness` o pacote para executar o upload:

    ```python
    from azureml.contrib.fairness import upload_dashboard_dictionary, download_dashboard_by_upload_id
    ```
    Crie um experimento, execute uma execução e carregue o painel:
    ```python
    exp = Experiment(ws, "Compare_Two_Models_Fairness_Census_Demo")
    print(exp)

    run = exp.start_logging()

    # Upload the dashboard to Azure Machine Learning
    try:
        dashboard_title = "Fairness Assessment of Logistic Regression and SVM Classifiers"
        # Set validate_model_ids parameter of upload_dashboard_dictionary to False if you have not registered your model(s)
        upload_id = upload_dashboard_dictionary(run,
                                                dash_dict,
                                                dashboard_name=dashboard_title)
        print("\nUploaded to id: {0}\n".format(upload_id))
        
        # To test the dashboard, you can download it back and ensure it contains the right information
        downloaded_dict = download_dashboard_by_upload_id(run, upload_id)
    finally:
        run.complete()
    ```


    Semelhante à seção anterior, você pode seguir um dos caminhos descritos acima (por meio de **experimentos** ou **modelos**) no Azure Machine Learning Studio para acessar o painel de visualização e comparar os dois modelos em termos de imparcialidade e desempenho.


## <a name="upload-unmitigated-and-mitigated-fairness-insights"></a>Carregar insights de inintegridades não mitigados e mitigados

Você pode usar [algoritmos de mitigação](https://fairlearn.org/v0.6.0/user_guide/mitigation.html)do Fairlearn, comparar seus modelos mitigados gerados com o modelo não mitigado original e navegar pelas compensações de desempenho/imparcialidade entre modelos comparados.

Para ver um exemplo que demonstra o uso do algoritmo de mitigação da [pesquisa de grade](https://fairlearn.org/v0.6.0/user_guide/mitigation.html#grid-search) (que cria uma coleção de modelos mitigados com diferentes interações de desempenho e imparcialidade), confira este bloco de [anotações de exemplo](https://github.com/Azure/MachineLearningNotebooks/blob/master/contrib/fairness/fairlearn-azureml-mitigation.ipynb). 

Carregar informações de imparcialção de vários modelos em uma única execução permite a comparação de modelos com relação à imparcialidade e ao desempenho. Você pode clicar em qualquer um dos modelos exibidos no gráfico de comparação de modelo para ver as informações de imparcialidade detalhadas do modelo específico.


[![Painel Fairlearn de comparação de modelo](./media/how-to-machine-learning-fairness-aml/multi-model-dashboard.png)](./media/how-to-machine-learning-fairness-aml/multi-model-dashboard.png#lightbox)
    

## <a name="next-steps"></a>Próximas etapas

[Saiba mais sobre a imparcialidade do modelo](concept-fairness-ml.md)

[Confira Azure Machine Learning notebooks de exemplo de imparcialidade](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness)
