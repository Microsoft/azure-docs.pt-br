---
title: Acompanhamento de experimentos e implantação de modelos
titleSuffix: Azure Data Science Virtual Machine
description: Saiba como acompanhar e registrar experimentos do Máquina Virtual de Ciência de Dados com Azure Machine Learning e/ou MLFlow.
services: machine-learning
ms.service: data-science-vm
author: samkemp
ms.author: samkemp
ms.topic: conceptual
ms.date: 07/17/2020
ms.openlocfilehash: 3795d531c5c4c543587ab817c05cd1cfeea6be06
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100518531"
---
# <a name="track-experiments-and-deploy-models-in-azure-machine-learning"></a>Acompanhe experimentos e implante modelos no Azure Machine Learning

Aprimore o processo de criação de modelo acompanhando seus experimentos e monitorando métricas de execução. Neste artigo, saiba como adicionar o código de registro em log ao seu script de treinamento usando a API [MLflow](https://mlflow.org/) e acompanhar o experimento em Azure Machine Learning.

O diagrama a seguir ilustra isso com o Acompanhamento do MLflow, você acompanha as métricas de execução de um experimento e armazena os artefatos de modelo no workspace do Azure Machine Learning.

![acompanhar experimentos](./media/how-to-track-experiments/mlflow-diagram-track.png)

## <a name="prerequisites"></a>Pré-requisitos

* Você precisará [provisionar um Workspace do Azure Machine Learning](../how-to-manage-workspace.md#create-a-workspace)

## <a name="create-a-new-notebook"></a>Criar um novo notebook

O SDK do Azure Machine Learning e do MLFlow são pré-instalados no VM de Ciência de Dados e podem ser acessados no ambiente do **azureml_py36_ \*** Conda. No JupyterLab, clique no iniciador e selecione o seguinte kernel:

![seleção de kernel](./media/how-to-track-experiments/experiment-tracking-1.png)

## <a name="set-up-the-workspace"></a>Configurar o workspace

Vá para a [portal do Azure](https://portal.azure.com) e selecione o espaço de trabalho provisionado como parte dos pré-requisitos. Você verá __baixar config.jsem__ (veja abaixo) – Baixe a configuração e verifique se ela está armazenada em seu diretório de trabalho no DSVM.

![Obter arquivo de configuração](./media/how-to-track-experiments/experiment-tracking-2.png)

A configuração contém informações como o nome do espaço de trabalho, assinatura, etc., e isso significa que você não precisa embutir em código esses parâmetros.

## <a name="track-dsvm-runs"></a>Rastrear execuções de DSVM

Adicione o código a seguir ao seu bloco de anotações (ou script) para definir o objeto de espaço de trabalho do AzureML.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
O URI de acompanhamento é válido até uma hora ou menos. Se você reiniciar o script após algum tempo ocioso, use a API get_mlflow_tracking_uri para obter um novo URI.

### <a name="load-the-data"></a>Carregar os dados

Este exemplo usa o conjunto de dados de diabetes, um pequeno conjunto de dados conhecido fornecido scikit-learn. Esta célula carrega o conjunto de dados e divide-o em conjuntos aleatórios de treinamento e teste.

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
from sklearn.externals import joblib

X, y = load_diabetes(return_X_y = True)
columns = ['age', 'gender', 'bmi', 'bp', 's1', 's2', 's3', 's4', 's5', 's6']
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=0)
data = {
    "train":{"X": X_train, "y": y_train},
    "test":{"X": X_test, "y": y_test}
}

print ("Data contains", len(data['train']['X']), "training samples and",len(data['test']['X']), "test samples")
```

### <a name="add-tracking"></a>Adicionar acompanhamento

Adicione o acompanhamento de experimento usando o SDK do Azure Machine Learning e carregue um modelo persistente para o registro de execução do experimento. O código a seguir adiciona logs e carrega um arquivo de modelo para a execução do experimento. O modelo também é registrado no registro do modelo de Azure Machine Learning.

```python
# Get an experiment object from Azure Machine Learning
from azureml.mlflow import register_model
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    # Log the algorithm parameter alpha to the run
    mlflow.log_param('alpha', 0.03)

    # Create, fit, and test the scikit-learn Ridge regression model
    regression_model = Ridge(alpha=0.03)
    regression_model.fit(data['train']['X'], data['train']['y'])
    preds = regression_model.predict(data['test']['X'])

    # Output the Mean Squared Error to the notebook and to the run
    print('Mean Squared Error is', mean_squared_error(data['test']['y'], preds))
    mlflow.log_metric('mse', mean_squared_error(data['test']['y'], preds))

    # Save the model
    model_file_name = 'model.pkl'
    joblib.dump(value = regression_model, filename = model_file_name)

    # upload the model file explicitly into artifacts
    mlflow.log_artifact(model_file_name)
    # register the model
    register_model(mlflow.active_run(), 'diabetes_model', 'model.pkl', model_framework="ScikitLearn")
```

### <a name="view-runs-in-azure-machine-learning"></a>Exibir execuções em Azure Machine Learning

Você pode exibir a execução do experimento em [Azure Machine Learning Studio](https://ml.azure.com). Clique em __experimentos__ no menu à esquerda e selecione o ' experiment_with_mlflow ' (ou se você decidiu nomear seu experimento de forma diferente no trecho acima, clique no nome usado):

![selecionar experimento](./media/how-to-track-experiments/mlflow-experiments.png)

Você deve ver o MSE (erro ao quadrado da média registrada):

![DESSE](./media/how-to-track-experiments/mlflow-experiments-2.png)

Se você clicar em executar, verá outros detalhes e também o modelo Pickled nas __saídas + logs__

## <a name="deploy-model-in-azure-machine-learning"></a>Implantar modelo no Azure Machine Learning

Nesta seção, descreveremos como implantar modelos treinados em um DSVM para Azure Machine Learning.

### <a name="step-1-create-inference-compute"></a>Etapa 1: criar uma computação de inferência

No menu à esquerda no [AzureML Studio](https://ml.azure.com) , clique em __computação__ e, em seguida, na guia __clusters de inferência__ . Em seguida, clique em __+ novo__ , conforme discutido abaixo:

![Criar a inferência de computação](./media/how-to-track-experiments/mlflow-experiments-6.png)

No painel __novo cluster de inferência__ , preencha os detalhes para:

* Nome da computação
* Serviço kubernetes – selecione criar novo
* Selecione a região
* Selecione o tamanho da VM (para fins deste tutorial, o padrão de Standard_D3_v2 é suficiente)
* Finalidade do cluster – selecionar __desenvolvimento-teste__
* O número de nós deve ser igual a __1__
* Configuração de rede-básica

Em seguida, clique em __criar__.

![detalhes da computação](./media/how-to-track-experiments/mlflow-experiments-7.png)

### <a name="step-2-deploy-no-code-inference-service"></a>Etapa 2: implantar o serviço de inferência de código

Quando registramos o modelo em nosso código usando `register_model` , especificamos a estrutura como sklearn. O Azure Machine Learning não dá suporte a implantações de código para as seguintes estruturas:

* scikit-learn
* Tensorflow SaveModel Format
* Formato de modelo ONNX

Implantação sem código significa que você pode implantar diretamente do artefato do modelo sem precisar especificar nenhum script de Pontuação específico.

Para implantar o modelo diabetes, vá para o menu à esquerda na [Azure Machine Learning Studio](https://ml.azure.com) e selecione __modelos__. Em seguida, clique no diabetes_model registrado:

![Selecionar modelo](./media/how-to-track-experiments/mlflow-experiments-3.png)

Em seguida, clique no botão __implantar__ no painel detalhes do modelo:

![Implantar](./media/how-to-track-experiments/mlflow-experiments-4.png)

Implantaremos o modelo no cluster de inferência (serviço kubernetes do Azure) que criamos na etapa 1. Preencha os detalhes abaixo fornecendo um nome para o serviço e o nome do cluster de computação AKS (criado na etapa 1). Também recomendamos que você aumente a __capacidade de reserva da CPU__ para 1 (de 0,1) e a __capacidade de reserva de memória__ para 1 (de 0,5) – você pode fazer esse aumento clicando em __avançado__ e preenchendo os detalhes. Em seguida, clique em __implantar__.

![detalhes da implantação](./media/how-to-track-experiments/mlflow-experiments-5.png)

### <a name="step-3-consume"></a>Etapa 3: consumir

Quando o modelo tiver sido implantado com êxito, você deverá ver o seguinte (para acessar esta página, clique em pontos de extremidade no menu à esquerda > clique no nome do serviço implantado):

![Consumir modelo](./media/how-to-track-experiments/mlflow-experiments-8.png)

Você deve ver que o estado da implantação vai de __transição__ para __íntegro__. Além disso, esta seção de detalhes fornece o ponto de extremidade REST e as URLs do Swagger que um desenvolvedor de aplicativos pode usar para integrar seu modelo ML em seus aplicativos.

Você pode testar o ponto de extremidade usando o [postmaster](https://www.postman.com/)ou pode usar o SDK do AzureML:

```python
from azureml.core import Webservice
import json

# if you called your service differently then change the name below
service = Webservice(ws, name="diabetes-service")

input_payload = json.dumps({
    'data': X_test[0:2].tolist(),
    'method': 'predict'  # If you have a classification model, you can get probabilities by changing this to 'predict_proba'.
})

output = service.run(input_payload)

print(output)
```

### <a name="step-4-clean-up"></a>Etapa 4: limpar

Exclua a computação de inferência criada na etapa 1 para que você não incorra em encargos de computação em andamento. No menu à esquerda na Azure Machine Learning Studio, clique em clusters de > de inferência de computação > selecione a computação > excluir.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre a [implantação de modelos no AzureML](../how-to-deploy-and-where.md)
