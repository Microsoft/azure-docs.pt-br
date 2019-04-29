---
title: Métricas de log durante execuções de treinamento
titleSuffix: Azure Machine Learning service
description: Saiba como adicionar log ao seu script de treinamento, como enviar o experimento, como verificar o progresso de um trabalho em execução e como exibir os resultados de uma execução. Você pode acompanhar suas experiências e monitorar as métricas para aprimorar o processo de criação de modelo.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 59a801fd149a0f647298d94522f9eefc1ea6eb9f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60820906"
---
# <a name="log-metrics-during-training-runs-in-azure-machine-learning"></a>Métricas de log durante o treinamento é executado no Azure Machine Learning

Neste artigo, saiba como adicionar o registro em log para o seu script de treinamento, enviar uma execução de teste, monitorar a execução e exibir os resultados de uma execução no serviço Azure Machine Learning. Aprimore o processo de criação de modelo, controlando seus experimentos e métricas de monitoramento. 

## <a name="list-of-training-metrics"></a>Lista de métricas de treinamento 

As métricas a seguir podem ser adicionadas a uma execução durante o treinamento de um experimento. Para exibir uma lista mais detalhada do que pode ser acompanhado em uma execução, veja a [Documentação de referência de classe de execução](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py).

|Type| Função do Python | Observações|
|----|:----|:----|
|Valores escalares |Função:<br>`run.log(name, value, description='')`<br><br>Exemplo:<br>run.log("accuracy", 0.95) |Registre um valor numérico ou de string para a execução com o nome dado. Registrar uma métrica em log para uma execução faz essa métrica ser armazenada no registro de execução no experimento.  Você pode registrar em log a mesma métrica várias vezes dentro de uma execução, sendo o resultado considerado um vetor dessa métrica.|
|Listas|Função:<br>`run.log_list(name, value, description='')`<br><br>Exemplo:<br>run.log_list("accuracies", [0.6, 0.7, 0.87]) | Faça uma lista de valores para a execução com o nome fornecido.|
|Linha|Função:<br>`run.log_row(name, description=None, **kwargs)`<br>Exemplo:<br>run.log_row("Y over X", x=1, y=0.4) | O uso de *log_row* cria uma métrica com várias colunas, conforme descrito em kwargs. Cada parâmetro nomeado gera uma coluna com o valor especificado.  *log_row* pode ser chamado uma vez para registrar em log uma tupla arbitrária ou várias vezes em loop para gerar uma tabela completa.|
|Tabela|Função:<br>`run.log_table(name, value, description='')`<br><br>Exemplo:<br>run.log_table("Y over X", {"x":[1, 2, 3], "y":[0.6, 0.7, 0.89]}) | Registre um objeto de dicionário na execução com o nome dado. |
|Imagens|Função:<br>`run.log_image(name, path=None, plot=None)`<br><br>Exemplo:<br>`run.log_image("ROC", plt)` | Faça logon uma imagem ao registro de execução. Use log_image para registrar em log um arquivo de imagem ou um gráfico matplotlib para a execução.  Essas imagens serão visíveis e comparáveis no registro de execução.|
|Marcar uma execução|Função:<br>`run.tag(key, value=None)`<br><br>Exemplo:<br>run.tag("selected", "yes") | Marque a execução com uma chave de cadeia de caracteres e um valor de cadeia de caracteres opcional.|
|Carregar arquivo ou diretório|Função:<br>`run.upload_file(name, path_or_stream)`<br> <br> Exemplo:<br>Run.upload_file ("best_model.pkl", ". / pkl") | Carregar um arquivo para o registro de execução. Execuções de capturam automaticamente os arquivos no diretório de saída especificado, cujo padrão é ". /outputs" para a maioria dos tipos de execução.  Use upload_file somente quando arquivos adicionais precisarem ser carregados ou um diretório de saída não for especificado. Sugerimos adicionar `outputs` ao nome para que ele seja carregado para o diretório de saídas. Você pode listar todos os arquivos associados a esse registro de execução pelo `run.get_file_names()` chamado|

> [!NOTE]
> Métricas para escalares, listas, linhas e tabelas podem ter o tipo: flutuante, inteiro ou cadeia de caracteres.

## <a name="start-logging-metrics"></a>Iniciar a métrica do registro de log

Se você quiser acompanhar ou monitorar seu experimento, deverá adicionar código para iniciar o log ao enviar a execução. Estas são maneiras de disparar o envio de execução:
* __Run.start_logging__ – adicione funções de registro em log ao seu script de treinamento e inicie uma sessão de registro em log interativa no experimento especificado. **start_logging** cria uma execução interativa para uso em cenários como blocos de anotações. Qualquer métrica registrada em log durante a sessão é adicionada ao registro de execução no experimento.
* __ScriptRunConfig__ – adicione funções de registro em log ao seu script de treinamento e carregue toda a pasta de script com a execução.  **ScriptRunConfig** é uma classe para definir as configurações para execuções do script. Com essa opção, você pode adicionar código de monitoramento para ser notificado da conclusão ou obter um widget visual para monitorar.

## <a name="set-up-the-workspace"></a>Configurar o workspace
Antes de adicionar o registro em log e enviar um experimento, você deve configurar o workspace.

1. Carregar o workspace. Para saber mais sobre como definir a configuração do espaço de trabalho, siga as etapas em [criar um espaço de trabalho do serviço de Azure Machine Learning](setup-create-workspace.md#sdk).

   ```python
   from azureml.core import Experiment, Run, Workspace
   import azureml.core
  
   ws = Workspace(workspace_name = <<workspace_name>>,
               subscription_id = <<subscription_id>>,
               resource_group = <<resource_group>>)
   ```
  
## <a name="option-1-use-startlogging"></a>Opção 1: Usar o start_logging

**start_logging** cria uma execução interativa para uso em cenários como blocos de anotações. Qualquer métrica registrada em log durante a sessão é adicionada ao registro de execução no experimento.

O exemplo a seguir treina um modelo simples de sklearn Ridge localmente em um Jupyter Notebook local. Para saber mais sobre o envio de experimentos em ambientes diferentes, consulte [configurar destinos de computação para treinamento de modelo com o serviço Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/how-to-set-up-training-targets).

1. Crie um script de treinamento em um Jupyter Notebook local. 

   ``` python
   # load diabetes dataset, a well-known small dataset that comes with scikit-learn
   from sklearn.datasets import load_diabetes
   from sklearn.linear_model import Ridge
   from sklearn.metrics import mean_squared_error
   from sklearn.model_selection import train_test_split
   from sklearn.externals import joblib

   X, y = load_diabetes(return_X_y = True)
   columns = ['age', 'gender', 'bmi', 'bp', 's1', 's2', 's3', 's4', 's5', 's6']
   X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.2, random_state = 0)
   data = {
      "train":{"X": X_train, "y": y_train},        
      "test":{"X": X_test, "y": y_test}
   }
   reg = Ridge(alpha = 0.03)
   reg.fit(data['train']['X'], data['train']['y'])
   preds = reg.predict(data['test']['X'])
   print('Mean Squared Error is', mean_squared_error(preds, data['test']['y']))
   joblib.dump(value = reg, filename = 'model.pkl');
   ```

2. Adicione o acompanhamento de experimento usando o SDK do serviço do Azure Machine Learning e carregue um modelo persistente para o registro de execução do experimento. O código a seguir adiciona marcas, logs e carrega um arquivo de modelo para a execução do experimento.

   ```python
   # Get an experiment object from Azure Machine Learning
   experiment = Experiment(workspace = ws, name = "train-within-notebook")
  
   # Create a run object in the experiment
   run = experiment.start_logging()# Log the algorithm parameter alpha to the run
   run.log('alpha', 0.03)

   # Create, fit, and test the scikit-learn Ridge regression model
   regression_model = Ridge(alpha=0.03)
   regression_model.fit(data['train']['X'], data['train']['y'])
   preds = regression_model.predict(data['test']['X'])

   # Output the Mean Squared Error to the notebook and to the run
   print('Mean Squared Error is', mean_squared_error(data['test']['y'], preds))
   run.log('mse', mean_squared_error(data['test']['y'], preds))

   # Save the model to the outputs directory for capture
   joblib.dump(value=regression_model, filename='outputs/model.pkl')

   # Take a snapshot of the directory containing this notebook
   run.take_snapshot('./')

   # Complete the run
   run.complete()
  
   ```

O script termina com ```run.complete()```, que marca a execução como concluída.  Normalmente, essa função é usada em cenários interativos de notebook.

## <a name="option-2-use-scriptrunconfig"></a>Opção 2: Usar ScriptRunConfig

[**ScriptRunConfig** ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) é uma classe para configurar as configurações para o script é executado. Com essa opção, você pode adicionar código de monitoramento para ser notificado da conclusão ou obter um widget visual para monitorar.

Este exemplo expande o modelo básico do sklearn Ridge acima. Ele faz uma limpeza simples de parâmetro dos valores alfa do modelo para capturar métricas e modelos treinados nas execuções sob o experimento. O exemplo é executado localmente em um ambiente gerenciado pelo usuário. 

1. Crie um script de treinamento `train.py`.

   ```python
   # train.py

   import os
   from sklearn.datasets import load_diabetes
   from sklearn.linear_model import Ridge
   from sklearn.metrics import mean_squared_error
   from sklearn.model_selection import train_test_split
   from azureml.core.run import Run
   from sklearn.externals import joblib

   import numpy as np

   #os.makedirs('./outputs', exist_ok = True)

   X, y = load_diabetes(return_X_y = True)

   run = Run.get_context()

   X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.2, random_state = 0)
   data = {"train": {"X": X_train, "y": y_train},
          "test": {"X": X_test, "y": y_test}}

   # list of numbers from 0.0 to 1.0 with a 0.05 interval
   alphas = mylib.get_alphas()

   for alpha in alphas:
      # Use Ridge algorithm to create a regression model
      reg = Ridge(alpha = alpha)
      reg.fit(data["train"]["X"], data["train"]["y"])

      preds = reg.predict(data["test"]["X"])
      mse = mean_squared_error(preds, data["test"]["y"])
      # log the alpha and mse values
      run.log('alpha', alpha)
      run.log('mse', mse)

      model_file_name = 'ridge_{0:.2f}.pkl'.format(alpha)
      # save model in the outputs folder so it automatically get uploaded
      with open(model_file_name, "wb") as file:
          joblib.dump(value = reg, filename = model_file_name)

      # upload the model file explicitly into artifacts 
      run.upload_file(name = model_file_name, path_or_stream = model_file_name)

      # register the model
      #run.register_model(file_name = model_file_name)

      print('alpha is {0:.2f}, and mse is {1:0.2f}'.format(alpha, mse))
  
   ```

2. O script `train.py` referencia `mylib.py`, o que permite que você obtenha a lista de valores alfa para usar no modelo ridge.

   ```python
   # mylib.py
  
   import numpy as np

   def get_alphas():
      # list of numbers from 0.0 to 1.0 with a 0.05 interval
      return np.arange(0.0, 1.0, 0.05)
   ```

3. Configure um ambiente local gerenciado pelo usuário.

   ```python
   from azureml.core.runconfig import RunConfiguration

   # Editing a run configuration property on-fly.
   run_config_user_managed = RunConfiguration()

   run_config_user_managed.environment.python.user_managed_dependencies = True

   # You can choose a specific Python environment by pointing to a Python path 
   #run_config.environment.python.interpreter_path = '/home/user/miniconda3/envs/sdk2/bin/python'
   ```

4. Envie o script ```train.py``` para execução no ambiente gerenciado pelo usuário. Toda essa pasta de script é enviada para treinamento, incluindo o arquivo ```mylib.py```.

   ```python
   from azureml.core import ScriptRunConfig
  
   experiment = Experiment(workspace=ws, name="train-on-local")
   src = ScriptRunConfig(source_directory = './', script = 'train.py', run_config = run_config_user_managed)
   run = experiment.submit(src)
   ```

## <a name="manage-a-run"></a>Gerenciar uma execução

O [Iniciar, monitorar e Cancelar execuções de treinamento](how-to-manage-runs.md) artigo realça os fluxos de trabalho específicos do Azure Machine Learning para saber como gerenciar seus experimentos.

## <a name="view-run-details"></a>Exibir detalhes de execução

### <a name="monitor-run-with-jupyter-notebook-widgets"></a>Monitorar execução com widgets do Jupyter Notebook
Quando você usa o método **ScriptRunConfig** para enviar execuções, pode inspecionar o progresso da execução com um widget do Jupyter Notebook. Como o envio de execução, o widget é assíncrono e fornece atualizações ao vivo a cada 10 a 15 segundos até que o trabalho seja concluído.

1. Exiba o widget do Jupyter enquanto aguarda a execução ser concluída.

   ```python
   from azureml.widgets import RunDetails
   RunDetails(run).show()
   ```

   ![Captura de tela do widget do Jupyter Notebook](./media/how-to-track-experiments/widgets.PNG)

2. **[Para execuções automatizadas do aprendizado de máquina]**  Para acessar os gráficos a partir de uma execução anterior. Substitua `<<experiment_name>>` com o nome do teste apropriado:

   ``` 
   from azureml.widgets import RunDetails
   from azureml.core.run import Run

   experiment = Experiment (workspace, <<experiment_name>>)
   run_id = 'autoML_my_runID' #replace with run_ID
   run = Run(experiment, run_id)
   RunDetails(run).show()
   ```

   ![Widget do notebook Jupyter para Machine Learning automatizado](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)


Para exibir mais detalhes de um pipeline, clique naquele que você gostaria de explorar na tabela, em seguida, os gráficos serão renderizados em um pop-up do portal do Azure.

### <a name="get-log-results-upon-completion"></a>Obter resultados de log após a conclusão

O treinamento e o monitoramento do modelo ocorrem em segundo plano para que você possa executar outras tarefas enquanto aguarda. Você também pode esperar até o modelo ter concluído o treinamento antes de executar mais código. Quando você usa **ScriptRunConfig**, pode usar ```run.wait_for_completion(show_output = True)``` para mostrar quando o treinamento do modelo está concluído. O sinalizador ```show_output``` fornece saída detalhada. 
  
### <a name="query-run-metrics"></a>Métricas de execução de consulta

Você pode exibir as métricas de um modelo treinado usando ```run.get_metrics()```. Agora você pode obter todas as métricas registradas no exemplo anterior para determinar o melhor modelo.

<a name="view-the-experiment-in-the-web-portal"></a>
## <a name="view-the-experiment-in-the-azure-portal"></a>Exibir o experimento no portal do Azure

Quando a execução de um experimento estiver concluída, você poderá navegar até o registro de execução do experimento gravado. Você pode acessar o histórico de dois modos:

* Obter a URL para a execução diretamente ```print(run.get_portal_url())```
* Exiba os detalhes de execução enviando o nome da execução (neste caso, ```run```). Esse modo leva você ao nome, ID, tipo, status, e página de detalhes do experimento, um link para o portal do Azure e um link para a documentação.

O link para a execução leva você diretamente para a página de detalhes de execução no portal do Azure. Aqui, você pode ver quaisquer propriedades, métricas controladas, imagens e gráficos registrados no experimento. Neste caso, registramos em log MSE e os valores alfa.

  ![Detalhes de execução no portal do Azure](./media/how-to-track-experiments/run-details-page-web.PNG)

Você também pode exibir quaisquer saídas ou logs para a execução, ou baixar o instantâneo do experimento que você enviou para poder compartilhar a pasta de teste com outras pessoas.

### <a name="viewing-charts-in-run-details"></a>Exibindo gráficos nos detalhes de execução

Há várias maneiras de usar as APIs para tipos diferentes de registro de métricas de log durante uma execução e exibi-los como gráficos no portal do Azure. 

|Valor conectado|Código de exemplo| Visualizar no portal|
|----|----|----|
|Registrar uma matriz de valores numéricos| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|gráfico de linhas de variável único|
|Registre um único valor numérico com o mesmo nome de métrica usado repetidamente (como em um loop for)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| Gráfico de linhas de variável-único|
|Faça uma linha com colunas numéricas 2 repetidamente|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Gráfico de linhas de duas variáveis|
|Tabela de log com 2 colunas numéricas|`run.log_table(name='Sine Wave', value=sines)`|Gráfico de linhas de duas variáveis|

<a name="auto"></a>
## <a name="understanding-automated-ml-charts"></a>Noções básicas sobre gráficos automatizados de ML

Depois de enviar um trabalho de ML automatizado em um notebook, é possível encontrar um histórico dessas execuções no workspace do serviço de aprendizado de máquina. 

Saiba mais sobre:
+ [Gráficos e curvas para modelos de classificação](#classification)
+ [Quadros e gráficos para modelos de regressão](#regression)
+ [Capacidade de explicar o modelo](#model-explain-ability-and-feature-importance)


### <a name="view-the-run-charts"></a>Exibir os gráficos de execução

1. Vá até seu workspace. 

1. Selecione **Experimentos** no painel mais à esquerda do workspace.

   ![Captura de tela do menu de experimento](./media/how-to-track-experiments/azure-machine-learning-auto-ml-experiment_menu.PNG)

1. Selecione o experimento no qual você está interessado.

   ![Lista de experimentos](./media/how-to-track-experiments/azure-machine-learning-auto-ml-experiment_list.PNG)

1. Na tabela, selecione o Número da Execução.

   ![Execução do experimento](./media/how-to-track-experiments/azure-machine-learning-auto-ml-experiment_run.PNG)

1. Na tabela, selecione o Número da Iteração do modelo que você gostaria de explorar ainda mais.

   ![Modelo do experimento](./media/how-to-track-experiments/azure-machine-learning-auto-ml-experiment_model.PNG)



### <a name="classification"></a>classificação

Para cada modelo de classificação compilado usando os recursos automatizados de aprendizado de máquina do Azure Machine Learning, é possível obter os gráficos a seguir: 
+ [Matriz de confusão](#confusion-matrix)
+ [Gráfico de precisão/recall](#precision-recall-chart)
+ [ROC (características operacionais do destinatário)](#roc)
+ [Curva de comparação de precisão](#lift-curve)
+ [Curva de ganhos](#gains-curve)
+ [Gráfico de calibragem](#calibration-plot)

#### <a name="confusion-matrix"></a>Matriz de confusão

Uma matriz de confusão é usada para descrever o desempenho de um modelo de classificação. Cada linha exibe as instâncias da classe real, e cada coluna representa as instâncias da classe prevista. A matriz de confusão mostra os rótulos classificados de modo correto e incorreto para um determinado modelo.

Para problemas de classificação, o Azure Machine Learning fornece automaticamente uma matriz de confusão para cada modelo compilado. Para cada matriz de confusão, o ML automatizado mostrará em verde e vermelho os rótulos classificados de modo correto e incorreto respectivamente. O tamanho do círculo representa a quantidade de amostras desse compartimento. Além disso, a contagem de frequência de cada rótulo previsto e de cada rótulo real é fornecida em gráficos de barras adjacentes. 

Exemplo 1: Um modelo de classificação com baixa precisão ![Um modelo de classificação com baixa precisão](./media/how-to-track-experiments/azure-machine-learning-auto-ml-confusion_matrix1.PNG)

Exemplo 2: Um modelo de classificação com alta precisão (recomendado) ![Um modelo de classificação com alta precisão](./media/how-to-track-experiments/azure-machine-learning-auto-ml-confusion_matrix2.PNG)


#### <a name="precision-recall-chart"></a>Gráfico de precisão/recall

Com esse gráfico, você pode comparar as curvas de precisão/recall de cada modelo para determinar qual deles tem uma relação aceitável entre a precisão e a recall conforme seu problema comercial específico. Esse gráfico mostra a média macro e micro da precisão/recall, além da precisão/recall associada a todas as classes de um modelo.

O termo “precisão” representa a capacidade de um classificador rotular todas as instâncias corretamente. O termo “recall” representa a capacidade de um classificador localizar todas as instâncias de um rótulo específico. A curva de precisão/recall mostra a relação entre esses dois conceitos. Idealmente, o modelo teria 100% de precisão e exatidão.

Exemplo 1: Um modelo de classificação com baixa precisão e baixo recall ![Um modelo de classificação com baixa precisão e baixo recall](./media/how-to-track-experiments/azure-machine-learning-auto-ml-precision_recall1.PNG)

Exemplo 2: Um modelo de classificação com ~100% de precisão e ~100% de recall (ideal) ![Um modelo de classificação com alta precisão e recall](./media/how-to-track-experiments/azure-machine-learning-auto-ml-precision_recall2.PNG)

#### <a name="roc"></a>ROC

O ROC (característica operacional do receptor) é um gráfico que compara os rótulos classificados de modo correto e incorreto de um modelo específico. A curva ROC pode ser menos informativa ao treinar modelos em conjuntos de dados com alto desvio, pois ela não mostrará os rótulos falso-positivos.

Exemplo 1: Um modelo de classificação com baixa quantidade de rótulos reais e alta quantidade de rótulos falsos ![Modelo de classificação com baixa quantidade de rótulos verdadeiros e alta quantidade de rótulos falsos](./media/how-to-track-experiments/azure-machine-learning-auto-ml-roc1.PNG)

Exemplo 2: Um modelo de classificação com alta quantidade de rótulos verdadeiros e baixa quantidade de rótulos falsos ![Modelo de classificação com alta quantidade de rótulos verdadeiros e baixa quantidade de rótulos falsos](./media/how-to-track-experiments/azure-machine-learning-auto-ml-roc2.PNG)

#### <a name="lift-curve"></a>Curva de comparação de precisão

Você pode comparar a precisão do modelo compilado automaticamente do Azure Machine Learning com a linha de base para exibir o ganho de valor desse determinado modelo.

Gráficos de comparação de precisão são usados para avaliar o desempenho de um modelo de classificação. Eles mostram o quão melhor seu desempenho pode ficar com um modelo em comparação a não ter um modelo. 

Exemplo 1: O modelo tem desempenho pior que um modelo de seleção aleatória ![Um modelo de classificação com desempenho pior do que um modelo de seleção aleatória](./media/how-to-track-experiments/azure-machine-learning-auto-ml-lift_curve1.PNG)

Exemplo 2: O modelo tem desempenho melhor do que um modelo de seleção aleatória ![Um modelo de classificação que tem desempenho melhor](./media/how-to-track-experiments/azure-machine-learning-auto-ml-lift_curve2.PNG)

#### <a name="gains-curve"></a>Curva de ganhos

Um gráfico de ganhos avalia o desempenho de um modelo de classificação em cada porção dos dados. Ele mostra, para cada percentil do conjunto de dados, o quão melhor o desempenho pode ser em comparação a um modelo de seleção aleatória.

Use o gráfico de ganhos cumulativos para ajudar você a escolher o corte de classificação usando uma porcentagem que corresponda a um ganho desejado do modelo. Essas informações fornecem outra maneira de observar os resultados no gráfico de comparação de precisão anexo.

Exemplo 1: Um modelo de classificação com ganho mínimo ![Um modelo de classificação com ganho mínimo](./media/how-to-track-experiments/azure-machine-learning-auto-ml-gains_curve1.PNG)

Exemplo 2: Um modelo de classificação com ganho significativo ![Um modelo de classificação com ganho significativo](./media/how-to-track-experiments/azure-machine-learning-auto-ml-gains_curve2.PNG)

#### <a name="calibration-plot"></a>Gráfico de calibragem

Em todos os problemas de classificação, você pode examinar a linha de calibração para cada média micro, macro e cada classe de um determinado modelo de previsão. 

Um gráfico de calibragem é usado para exibir a confiança de um modelo de previsão. Para isso, ele mostra a relação entre a probabilidade prevista e a probabilidade real, em que “probabilidade” representa as chances de uma determinada instância pertencer a algum rótulo. Um modelo bem calibrado se alinha com a linha y = x, estando razoavelmente confiante em suas previsões. Um modelo com confiança excessiva se nivela à linha y=0, na qual a probabilidade prevista está presente, mas não há nenhuma probabilidade real.

Exemplo 1: Um modelo mais bem calibrado ![Um modelo mais bem calibrado](./media/how-to-track-experiments/azure-machine-learning-auto-ml-calib_curve1.PNG)

Exemplo 2: Um modelo com confiança excessiva ![Um modelo com confiança excessiva](./media/how-to-track-experiments/azure-machine-learning-auto-ml-calib_curve2.PNG)

### <a name="regression"></a>Regressão
Para cada modelo de regressão compilado usando os recursos automatizados de aprendizado de máquina do Azure Machine Learning, é possível obter os gráficos a seguir: 
+ [Previsto vs. Real](#pvt)
+ [Histograma de resíduos](#histo)

<a name="pvt"></a>

#### <a name="predicted-vs-true"></a>Previsto vs. True

Previsto vs. “Real” mostra a relação entre um valor previsto e seu valor real de correlação para um problema de regressão. Esse gráfico pode ser usado para medir o desempenho de um modelo que, quanto mais próximos à linha y=x linha estiverem seus valores previstos, melhor será a precisão de um modelo de previsão.

Após cada execução, você pode ver um gráfico comparando os dados previstos e reais de cada modelo de regressão. Para proteger a privacidade dos dados, os valores ficam no mesmo compartimento, sendo que o tamanho de cada compartimento é mostrado como um gráfico de barras na parte inferior da área do gráfico. Você pode comparar o modelo de previsão, com as áreas de tonalidade mais leves, mostrando as margens de erro comparadas ao valor ideal de onde o modelo deveria estar.

Exemplo 1: Um modelo de regressão com a baixa precisão em previsões ![Um modelo de regressão com baixa precisão em previsões](./media/how-to-track-experiments/azure-machine-learning-auto-ml-regression1.PNG)

Exemplo 2: Um modelo de regressão com a alta precisão em previsões ![Um modelo de regressão com alta precisão em previsões](./media/how-to-track-experiments/azure-machine-learning-auto-ml-regression2.PNG)

<a name="histo"></a>

#### <a name="histogram-of-residuals"></a>Histograma de resíduos

Um resíduo representa um y observado, que é o y previsto. Para mostrar uma margem de erro com baixo desvio, o histograma de resíduos deve ter a forma de uma curva de sino, centralizada em torno do 0. 

Exemplo 1: Um modelo de regressão com desvio em seus erros ![Modelo de regressão de SA com desvio em seus erros](./media/how-to-track-experiments/azure-machine-learning-auto-ml-regression3.PNG)

Exemplo 2: Um modelo de regressão com uma distribuição mais uniforme de erros ![Um modelo de regressão com uma distribuição mais uniforme de erros](./media/how-to-track-experiments/azure-machine-learning-auto-ml-regression4.PNG)

### <a name="model-explain-ability-and-feature-importance"></a>Capacidade de explicar o modelo e a importância do recurso

A importância do recurso fornece uma pontuação que indica o quão valioso era cada recurso na construção de um modelo. É possível analisar a pontuação de importância do recurso do modelo geral, bem como por classe de um modelo de previsão. Também é possível ver, em cada recurso, como a importância se compara a cada classe e no geral.

![Capacidade de explicar o recurso](./media/how-to-track-experiments/azure-machine-learning-auto-ml-feature_explain1.PNG)

## <a name="example-notebooks"></a>Blocos de anotações de exemplo
Os seguintes blocos de anotações demonstram conceitos neste artigo:
* [how-to-use-azureml/training/train-within-notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook)
* [how-to-use-azureml/training/train-on-local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [how-to-use-azureml/training/logging-api/logging-api.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Próximas etapas

Experimente estas próximas etapas para aprender a usar o SDK do Azure Machine Learning para Python:

* Veja um exemplo de como registrar o melhor modelo e implantá-lo no tutorial [Treinar um modelo de classificação de imagem com o Azure Machine Learning](tutorial-train-models-with-aml.md).

* Saiba como [Treinar modelos PyTorch com o Azure Machine Learning](how-to-train-pytorch.md).
