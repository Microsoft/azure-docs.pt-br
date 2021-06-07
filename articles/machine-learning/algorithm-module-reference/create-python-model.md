---
title: 'Criar modelo Python: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo criar modelo Python no Azure Machine Learning para criar um módulo personalizado de modelagem ou processamento de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 06/18/2020
ms.openlocfilehash: 03f80ebeb7b30d8c0fe14060335541d77b7523b0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "90898626"
---
# <a name="create-python-model-module"></a>Criar módulo de modelo do Python

Este artigo descreve um módulo no designer de Azure Machine Learning.

Saiba como usar o módulo criar modelo Python para criar um modelo não treinado a partir de um script Python. Você pode basear o modelo em qualquer aprendiz que esteja incluído em um pacote do Python no ambiente do Azure Machine Learning designer. 

Depois de criar o modelo, você pode usar [treinar modelo](train-model.md) para treinar o modelo em um conjunto de informações, como qualquer outro aprendiz no Azure Machine Learning. O modelo treinado pode ser passado para o [modelo de Pontuação](score-model.md) para fazer previsões. Em seguida, você pode salvar o modelo treinado e publicar o fluxo de trabalho de Pontuação como um serviço Web.

> [!WARNING]
> Atualmente, não é possível conectar esse módulo para ajustar o módulo de **hiperparâmetros de modelo** ou passar os resultados pontuados de um modelo Python para [avaliar o modelo](evaluate-model.md). Se você precisar ajustar os hiperparâmetros ou avaliar um modelo, poderá escrever um script Python personalizado usando o módulo [Executar script Python](execute-python-script.md) .


## <a name="configure-the-module"></a>Configurar o módulo

O uso deste módulo requer conhecimento intermediário ou especializado do Python. O módulo dá suporte ao uso de qualquer aprendiz que esteja incluído nos pacotes do Python já instalados no Azure Machine Learning. Consulte a lista de pacotes do Python pré-instalado em [Executar script Python](execute-python-script.md).

> [!NOTE]
> Tenha cuidado ao escrever seu script e verifique se não há nenhum erro de sintaxe, como usar um objeto não declarado ou um módulo não importado.

> [!NOTE]
> Além disso, preste atenção extra à lista de módulos pré-instalados em [Executar script Python](execute-python-script.md). Importe somente os módulos pré-instalados. Não instale pacotes extras, como "PIP install xgboost" nesse script; caso contrário, erros serão gerados durante a leitura de modelos nos módulos de fluxo inferiores.
  
Este artigo mostra como usar **criar modelo Python** com um pipeline simples. Aqui está um diagrama do pipeline:

![Diagrama de criar modelo Python](./media/module/create-python-model.png)

1. Selecione **criar modelo do Python** e edite o script para implementar o processo de modelagem ou gerenciamento de dados. Você pode basear o modelo em qualquer aprendiz que esteja incluído em um pacote do Python no ambiente de Azure Machine Learning.

> [!NOTE]
> Preste atenção extra aos comentários no código de exemplo do script e verifique se o script segue estritamente o requisito, incluindo o nome da classe, os métodos, bem como a assinatura do método. A violação levará a exceções. 

   O seguinte código de exemplo do classificador Naive Bayes de duas classes usa o popular pacote *sklearn* :

   ```Python

   # The script MUST define a class named AzureMLModel.
   # This class MUST at least define the following three methods:
       # __init__: in which self.model must be assigned,
       # train: which trains self.model, the two input arguments must be pandas DataFrame,
       # predict: which generates prediction result, the input argument and the prediction result MUST be pandas DataFrame.
   # The signatures (method names and argument names) of all these methods MUST be exactly the same as the following example.

   # Please do not install extra packages such as "pip install xgboost" in this script,
   # otherwise errors will be raised when reading models in down-stream modules.
   
   import pandas as pd
   from sklearn.naive_bayes import GaussianNB


   class AzureMLModel:
       def __init__(self):
           self.model = GaussianNB()
           self.feature_column_names = list()

       def train(self, df_train, df_label):
           # self.feature_column_names records the column names used for training.
           # It is recommended to set this attribute before training so that the
           # feature columns used in predict and train methods have the same names.
           self.feature_column_names = df_train.columns.tolist()
           self.model.fit(df_train, df_label)

       def predict(self, df):
           # The feature columns used for prediction MUST have the same names as the ones for training.
           # The name of score column ("Scored Labels" in this case) MUST be different from any other columns in input data.
           return pd.DataFrame(
               {'Scored Labels': self.model.predict(df[self.feature_column_names]), 
                'probabilities': self.model.predict_proba(df[self.feature_column_names])[:, 1]}
           )


   ```

2. Conecte o módulo **criar modelo do Python** que você acabou de criar para **treinar o modelo** e o modelo de **Pontuação**.

3. Se você precisar avaliar o modelo, adicione um módulo [Executar script Python](execute-python-script.md) e edite o script Python.

   O script a seguir é um código de avaliação de exemplo:

   ```Python


   # The script MUST contain a function named azureml_main
   # which is the entry point for this module.

   # imports up here can be used to 
   import pandas as pd

   # The entry point function MUST have two input arguments:
   #   Param<dataframe1>: a pandas.DataFrame
   #   Param<dataframe2>: a pandas.DataFrame
   def azureml_main(dataframe1 = None, dataframe2 = None):
    
       from sklearn.metrics import accuracy_score, precision_score, recall_score, roc_auc_score, roc_curve
       import pandas as pd
       import numpy as np
    
       scores = dataframe1.ix[:, ("income", "Scored Labels", "probabilities")]
       ytrue = np.array([0 if val == '<=50K' else 1 for val in scores["income"]])
       ypred = np.array([0 if val == '<=50K' else 1 for val in scores["Scored Labels"]])    
       probabilities = scores["probabilities"]
    
       accuracy, precision, recall, auc = \
       accuracy_score(ytrue, ypred),\
       precision_score(ytrue, ypred),\
       recall_score(ytrue, ypred),\
       roc_auc_score(ytrue, probabilities)
    
       metrics = pd.DataFrame();
       metrics["Metric"] = ["Accuracy", "Precision", "Recall", "AUC"];
       metrics["Value"] = [accuracy, precision, recall, auc]
    
       return metrics,

   ```

## <a name="next-steps"></a>Próximas etapas

Confira o [conjunto de módulos disponíveis](module-reference.md) no Azure Machine Learning. 