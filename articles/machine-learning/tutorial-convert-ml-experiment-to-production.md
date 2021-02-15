---
title: Converter código do notebook em scripts do Python
titleSuffix: Azure Machine Learning
description: Transforme os notebooks experimentais do aprendizado de máquina em código pronto para produção usando o modelo de código MLOpsPython. Em seguida, você pode testar, implantar e automatizar esse código.
author: bjcmit
ms.author: brysmith
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 04/30/2020
ms.custom: devx-track-python
ms.openlocfilehash: cdfeb2fdeefabb0d2d4af2fb63222adda5d023fb
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576018"
---
# <a name="tutorial-convert-ml-experiments-to-production-python-code"></a>Tutorial: Converter experimentos do ML em código Python de produção

Neste tutorial, você aprenderá a converter notebooks Juptyer em scripts Python para tornar o teste e a automação de TI amigáveis usando o modelo de código MLOpsPython e o Azure Machine Learning. Normalmente, esse processo é usado para capturar o código de experimentação/treinamento de um notebook Juptyer e convertê-lo em scripts Python. Esses scripts podem ser usados para teste e automação de CI/CD em seu ambiente de produção. 

Um projeto de aprendizado de máquina exige experimentação, em que as hipóteses são testadas com ferramentas Agile como o Jupyter Notebook usando conjuntos de dados reais. Depois que o modelo estiver pronto para produção, o código do modelo deverá ser colocado em um repositório de códigos de produção. Em alguns casos, o código do modelo precisará ser convertido em scripts Python para serem colocados no repositório de códigos de produção. Este tutorial apresenta uma abordagem recomendada sobre como exportar o código de experimentação para scripts Python.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Limpar o código não essencial
> * Refatorar o código do Jupyter Notebook em funções
> * Criar scripts Python para tarefas relacionadas
> * Criar testes de unidade

## <a name="prerequisites"></a>Pré-requisitos

- Gere o [modelo MLOpsPython](https://github.com/microsoft/MLOpsPython/generate) e use os notebooks `experimentation/Diabetes Ridge Regression Training.ipynb` e `experimentation/Diabetes Ridge Regression Scoring.ipynb`. Esses notebooks são usados como um exemplo de conversão de experimentação para produção. É possível encontrar esses notebooks em [https://github.com/microsoft/MLOpsPython/tree/master/experimentation](https://github.com/microsoft/MLOpsPython/tree/master/experimentation).
- Instale o `nbconvert`. Siga apenas as instruções de instalação da seção __Como instalar o nbconvert__ na página [Instalação](https://nbconvert.readthedocs.io/en/latest/install.html).

## <a name="remove-all-nonessential-code"></a>Remover todo o código não essencial

Um código escrito durante a experimentação é destinado apenas a fins exploratórios. Portanto, a primeira etapa para converter um código experimental em um código de produção é remover esse código não essencial. A remoção do código não essencial também facilitará a manutenção do código. Nesta seção, você removerá o código do notebook `experimentation/Diabetes Ridge Regression Training.ipynb`. As instruções que imprimem a forma de `X` e `y` e a chamada de célula `features.describe` servem apenas para exploração de dados e podem ser removidas. Após a remoção do código não essencial, `experimentation/Diabetes Ridge Regression Training.ipynb` deverá se parecer com o seguinte código sem markdown:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import joblib
import pandas as pd

sample_data = load_diabetes()

df = pd.DataFrame(
    data=sample_data.data,
    columns=sample_data.feature_names)
df['Y'] = sample_data.target

X = df.drop('Y', axis=1).values
y = df['Y'].values

X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=0)
data = {"train": {"X": X_train, "y": y_train},
        "test": {"X": X_test, "y": y_test}}

args = {
    "alpha": 0.5
}

reg_model = Ridge(**args)
reg_model.fit(data["train"]["X"], data["train"]["y"])

preds = reg_model.predict(data["test"]["X"])
mse = mean_squared_error(preds, y_test)
metrics = {"mse": mse}
print(metrics)

model_name = "sklearn_regression_model.pkl"
joblib.dump(value=reg, filename=model_name)
```

## <a name="refactor-code-into-functions"></a>Refatorar o código em funções

Em segundo lugar, o código do Jupyter precisa ser refatorado em funções. A refatoração do código em funções facilita o teste de unidade e torna o código mais sustentável. Nesta seção, você vai refatorar:

- O notebook Treinamento de Regressão de Sulcos Causados por Diabetes (`experimentation/Diabetes Ridge Regression Training.ipynb`)
- O notebook Pontuação de Regressão de Sulcos Causados por Diabetes (`experimentation/Diabetes Ridge Regression Scoring.ipynb`)

### <a name="refactor-diabetes-ridge-regression-training-notebook-into-functions"></a>Refatorar o notebook Treinamento de Regressão de Sulcos Causados por Diabetes em funções

Em `experimentation/Diabetes Ridge Regression Training.ipynb`, conclua as seguintes etapas:

1. Crie uma função chamada `split_data` para dividir o quadro de dados em dados de teste e de treinamento. A função deve usar o `df` de quadros como um parâmetro e retornar um dicionário contendo as chaves `train` e `test`.

    Mova o código no título *Dividir Dados em Conjuntos de Treinamento e Validação* para a função `split_data` e modifique-o para retornar o objeto `data`.

1. Crie uma função chamada `train_model`, que usa os parâmetros `data` e `args` e retorna um modelo treinado.

    Mova o código no título *Modelo de Treinamento no Conjunto de Treinamento* para a função `train_model` e modifique-o para retornar o objeto `reg_model`. Remova o dicionário `args`; os valores serão obtidos do parâmetro `args`.

1. Crie uma função chamada `get_model_metrics`, que usa os parâmetros `reg_model` e `data`, avalia o modelo e, em seguida, retorna um dicionário de métricas para o modelo treinado.

    Mova o código no título *Validar Modelo no Conjunto de Validação* na função `get_model_metrics` e modifique-o para retornar o objeto `metrics`.

As três funções devem ser as seguintes:

```python
# Split the dataframe into test and train data
def split_data(df):
    X = df.drop('Y', axis=1).values
    y = df['Y'].values

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}
    return data


# Train the model, return the model
def train_model(data, args):
    reg_model = Ridge(**args)
    reg_model.fit(data["train"]["X"], data["train"]["y"])
    return reg_model


# Evaluate the metrics for the model
def get_model_metrics(reg_model, data):
    preds = reg_model.predict(data["test"]["X"])
    mse = mean_squared_error(preds, data["test"]["y"])
    metrics = {"mse": mse}
    return metrics
```

Ainda em `experimentation/Diabetes Ridge Regression Training.ipynb`, conclua as seguintes etapas:

1. Crie uma função chamada `main`, que não usa nenhum parâmetro e não retorna nada.
1. Mova o código no título "Carregar Dados" para a função `main`.
1. Adicione invocações para as funções recém-gravadas na função `main`:
    ```python
    # Split Data into Training and Validation Sets
    data = split_data(df)
    ```

    ```python
    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)
    ```

    ```python
    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)
    ```
1. Mova o código no título "Salvar Modelo" para a função `main`.

A função `main` deverá ser semelhante ao seguinte código:

```python
def main():
    # Load Data
    sample_data = load_diabetes()

    df = pd.DataFrame(
        data=sample_data.data,
        columns=sample_data.feature_names)
    df['Y'] = sample_data.target

    # Split Data into Training and Validation Sets
    data = split_data(df)

    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)

    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)

    # Save Model
    model_name = "sklearn_regression_model.pkl"

    joblib.dump(value=reg, filename=model_name)
```

Nesta fase, não deverá haver nenhum código restante no notebook que não esteja em uma função, além das instruções de importação da primeira célula.

Adicione uma instrução que chama a função `main`.

```python
main()
```

Após a refatoração, `experimentation/Diabetes Ridge Regression Training.ipynb` deverá ser semelhante ao seguinte código sem markdown:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import pandas as pd
import joblib


# Split the dataframe into test and train data
def split_data(df):
    X = df.drop('Y', axis=1).values
    y = df['Y'].values

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}
    return data


# Train the model, return the model
def train_model(data, args):
    reg_model = Ridge(**args)
    reg_model.fit(data["train"]["X"], data["train"]["y"])
    return reg_model


# Evaluate the metrics for the model
def get_model_metrics(reg_model, data):
    preds = reg_model.predict(data["test"]["X"])
    mse = mean_squared_error(preds, data["test"]["y"])
    metrics = {"mse": mse}
    return metrics


def main():
    # Load Data
    sample_data = load_diabetes()

    df = pd.DataFrame(
        data=sample_data.data,
        columns=sample_data.feature_names)
    df['Y'] = sample_data.target

    # Split Data into Training and Validation Sets
    data = split_data(df)

    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)

    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)

    # Save Model
    model_name = "sklearn_regression_model.pkl"

    joblib.dump(value=reg, filename=model_name)

main()
```

### <a name="refactor-diabetes-ridge-regression-scoring-notebook-into-functions"></a>Refatorar o notebook Pontuação de Regressão de Sulcos Causados por Diabetes em funções

Em `experimentation/Diabetes Ridge Regression Scoring.ipynb`, conclua as seguintes etapas:

1. Crie uma função chamada `init`, que não usa nenhum parâmetro e não retorna nada.
1. Copie o código no título "Carregar Modelo" na função `init`.

A função `init` deverá ser semelhante ao seguinte código:

```python
def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

Depois que a função `init` for criada, substitua todo o código no título "Carregar Modelo" por uma só chamada para `init` da seguinte maneira:

```python
init()
```

Em `experimentation/Diabetes Ridge Regression Scoring.ipynb`, conclua as seguintes etapas:

1. Crie uma função chamada `run`, que usa `raw_data` e `request_headers` como parâmetros e retorna um dicionário de resultados da seguinte maneira:

    ```python
    {"result": result.tolist()}
    ```

1. Copie o código nos títulos "Preparar Dados" e "Pontuar Dados" na função `run`.

    A função `run` deverá ser semelhante ao seguinte código (lembre-se de remover as instruções que definem as variáveis `raw_data` e `request_headers`, que serão usadas posteriormente quando a função `run` for chamada):

    ```python
    def run(raw_data, request_headers):
        data = json.loads(raw_data)["data"]
        data = numpy.array(data)
        result = model.predict(data)

        return {"result": result.tolist()}
    ```

Depois que a função `run` for criada, substitua todo o código nos cabeçalhos "Preparar Dados" e "Pontuar Dados" pelo seguinte código:

```python
raw_data = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(raw_data, request_header)
print("Test result: ", prediction)
```

O código anterior define as variáveis `raw_data` e `request_header`, chama a função `run` com `raw_data` e `request_header` e imprime as previsões.

Após a refatoração, `experimentation/Diabetes Ridge Regression Scoring.ipynb` deverá ser semelhante ao seguinte código sem markdown:

```python
import json
import numpy
from azureml.core.model import Model
import joblib

def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)

def run(raw_data, request_headers):
    data = json.loads(raw_data)["data"]
    data = numpy.array(data)
    result = model.predict(data)

    return {"result": result.tolist()}

init()
test_row = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(test_row, {})
print("Test result: ", prediction)
```

## <a name="combine-related-functions-in-python-files"></a>Combinar funções relacionadas em arquivos Python

Em terceiro lugar, as funções relacionadas precisam ser mescladas em arquivos Python para ajudar a aprimorar a reutilização de código. Nesta seção, você criará arquivos Python para os seguintes notebooks:

- O notebook Treinamento de Regressão de Sulcos Causados por Diabetes (`experimentation/Diabetes Ridge Regression Training.ipynb`)
- O notebook Pontuação de Regressão de Sulcos Causados por Diabetes (`experimentation/Diabetes Ridge Regression Scoring.ipynb`)

### <a name="create-python-file-for-the-diabetes-ridge-regression-training-notebook"></a>Criar um arquivo Python para o notebook Treinamento de Regressão de Sulcos Causados por Diabetes

Converta o notebook em um script executável executando a seguinte instrução em um prompt de comando, que usa o pacote `nbconvert` e o caminho de `experimentation/Diabetes Ridge Regression Training.ipynb`:

```
jupyter nbconvert "Diabetes Ridge Regression Training.ipynb" --to script --output train
```

Depois que o notebook for convertido em `train.py`, remova os comentários indesejados. Substitua a chamada a `main()` ao final do arquivo por uma invocação condicional como o seguinte código:

```python
if __name__ == '__main__':
    main()
```

O arquivo `train.py` deverá ser semelhante ao seguinte código:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import pandas as pd
import joblib


# Split the dataframe into test and train data
def split_data(df):
    X = df.drop('Y', axis=1).values
    y = df['Y'].values

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}
    return data


# Train the model, return the model
def train_model(data, args):
    reg_model = Ridge(**args)
    reg_model.fit(data["train"]["X"], data["train"]["y"])
    return reg_model


# Evaluate the metrics for the model
def get_model_metrics(reg_model, data):
    preds = reg_model.predict(data["test"]["X"])
    mse = mean_squared_error(preds, data["test"]["y"])
    metrics = {"mse": mse}
    return metrics


def main():
    # Load Data
    sample_data = load_diabetes()

    df = pd.DataFrame(
        data=sample_data.data,
        columns=sample_data.feature_names)
    df['Y'] = sample_data.target

    # Split Data into Training and Validation Sets
    data = split_data(df)

    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)

    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)

    # Save Model
    model_name = "sklearn_regression_model.pkl"

    joblib.dump(value=reg, filename=model_name)

if __name__ == '__main__':
    main()
```

`train.py` agora pode ser invocado em um terminal executando `python train.py`.
As funções de `train.py` também podem ser chamadas em outros arquivos.

O arquivo `train_aml.py` encontrado no diretório `diabetes_regression/training` no repositório MLOpsPython chama as funções definidas em `train.py` no contexto de uma execução de experimento do Azure Machine Learning. As funções também podem ser chamadas em testes de unidade, abordadas mais adiante neste guia.

### <a name="create-python-file-for-the-diabetes-ridge-regression-scoring-notebook"></a>Criar um arquivo Python para o notebook Pontuação de Regressão de Sulcos Causados por Diabetes

Converta o notebook em um script executável executando a seguinte instrução em um prompt de comando, que usa o pacote `nbconvert` e o caminho de `experimentation/Diabetes Ridge Regression Scoring.ipynb`:

```
jupyter nbconvert "Diabetes Ridge Regression Scoring.ipynb" --to script --output score
```

Depois que o notebook for convertido em `score.py`, remova os comentários indesejados. O arquivo `score.py` deverá ser semelhante ao seguinte código:

```python
import json
import numpy
from azureml.core.model import Model
import joblib

def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)

def run(raw_data, request_headers):
    data = json.loads(raw_data)["data"]
    data = numpy.array(data)
    result = model.predict(data)

    return {"result": result.tolist()}

init()
test_row = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(test_row, request_header)
print("Test result: ", prediction)
```

A variável `model` precisa ser global para que fique visível em todo o script. Adicione a seguinte instrução ao início da função `init`:

```python
global model
```

Após a adição da instrução anterior, a função `init` deverá ser semelhante ao seguinte código:

```python
def init():
    global model

    # load the model from file into a global object
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

## <a name="create-unit-tests-for-each-python-file"></a>Criar testes de unidade para cada arquivo Python

Em quarto lugar, crie testes de unidade para as funções Python. Os testes de unidade protegem o código contra regressões funcionais e facilitam a manutenção. Nesta seção, você criará testes de unidade para as funções em `train.py`.

`train.py` contém várias funções, mas criaremos apenas um só teste de unidade para a função `train_model` usando a estrutura Pytest neste tutorial. O Pytest não é a única estrutura de teste de unidade do Python, mas é uma das mais comumente usadas. Para obter mais informações, visite [Pytest](https://pytest.org).

Um teste de unidade geralmente contém três ações principais:

- Organizar objeto: criação e configuração dos objetos necessários
- Executar uma ação em um objeto
- Declarar o que é esperado

O teste de unidade chamará `train_model` com alguns dados e argumentos embutidos em código e validará que `train_model` funcionou conforme o esperado usando o modelo treinado resultante para fazer uma previsão e comparar essa previsão com um valor esperado.

```python
import numpy as np
from code.training.train import train_model


def test_train_model():
    # Arrange
    X_train = np.array([1, 2, 3, 4, 5, 6]).reshape(-1, 1)
    y_train = np.array([10, 9, 8, 8, 6, 5])
    data = {"train": {"X": X_train, "y": y_train}}

    # Act
    reg_model = train_model(data, {"alpha": 1.2})

    # Assert
    preds = reg_model.predict([[1], [2]])
    np.testing.assert_almost_equal(preds, [9.93939393939394, 9.03030303030303])
```

## <a name="next-steps"></a>Próximas etapas

Agora que você entendeu como fazer a conversão de um experimento para um código de produção, confira os seguintes links para obter mais informações e as próximas etapas:

+ [MLOpsPython](https://github.com/microsoft/MLOpsPython/blob/master/docs/custom_model.md): crie um pipeline de CI/CD para treinar, avaliar e implantar um modelo próprio usando o Azure Pipelines e o Azure Machine Learning
+ [Monitorar métricas e execuções de experimento do Azure ML](./how-to-track-experiments.md)
+ [Monitorar e coletar dados de pontos de extremidade de serviço Web do ML](./how-to-enable-app-insights.md)