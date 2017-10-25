---
title: "Como usar o Histórico de Execuções e métricas do Azure Machine Learning Workbench | Microsoft Docs"
description: "Guia para usar os recursos Histórico de Execuções e Métricas de Modelo Azure Machine Learning Workbench"
services: machine-learning
author: jopela
ms.author: jopela
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/07/2017
ms.openlocfilehash: 1a4f2d69907d220a8bbec5087ba431dc243c46d6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-run-history-and-model-metrics-in-azure-machine-learning-workbench"></a>Como usar o Histórico de Execuções e as Métricas do Modelo no Azure Machine Learning Workbench

O Azure Machine Learning Workbench dá suporte à experimentação de ciência de dados por meio de seus recursos de **Histórico de Execuções** e **Modelo Métricas**.
O **Histórico de Execuções** fornece um meio para rastrear as saídas de seus experimentos de aprendizado de máquina e então habilita filtragem e comparação de seus resultados.
**Métricas de Modelo** podem ser registradas em log de qualquer ponto dos seus scripts, acompanhando quaisquer valores que sejam mais importantes em seus experimentos de ciência de dados.
Este artigo descreve como usar de maneira eficiente esses recursos para aumentar a velocidade e a qualidade da sua experimentação de ciência de dados.

## <a name="prerequisites"></a>Pré-requisitos
Para percorrer este guia de instruções, você precisa:
* [Criar e instalar o Azure Machine Learning](quickstart-installation.md)
- [Criar um projeto](quickstart-installation.md)


## <a name="azure-ml-logging-api-overview"></a>Visão geral da API de Registro em Log do Azure ML
A API de Registro em Log do Azure ML está disponível por meio do módulo **azureml.logging** em Python (que é instalado com o Azure ML Workbench). Depois de importar esse módulo, você pode usar o método **get_azureml_logger** para instanciar um objeto de **agente**.
Em seguida, você pode usar o método **log** do agente para armazenar pares de chave/valor produzidos pelos seus scripts do Python.
No momento, há suporte para registro em log de métricas de modelo dos tipos escalar, dicionário, lista e dataframe, conforme mostrado.

```Python
# create a logger instance in already set up environment 
from azureml.logging import get_azureml_logger
logger = get_azureml_logger()

# log scalar (any integer or floating point type is fine)
logger.log("simple value", 7)

# log dictionary
logger.log("range", {"min":5, "max":7})

# log list
logger.log("all values", [5, 6, 7])

# log dataframe
import pandas
df = pandas.DataFrame.from_records([(1,2),(3,4)],columns=["a","b"])
logger.log("dataframe", df)
```
É fácil de usar o agente em seus projetos do Azure ML Workbench e este artigo mostra como fazer isso.

## <a name="create-a-project-in-azure-ml-workbench"></a>Criar um projeto no Azure ML Workbench
Se você ainda não tiver um projeto, poderá criar um em [Criar e instalar Quickstart](quickstart-installation.md). No **Painel Projeto**, você pode abrir o script **iris_sklearn.py** ( conforme mostrado).

![como acessar um script na guia arquivos](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-01b.png)

Você pode usar esse script como um guia para a implementação esperada do registro em log da métrica de modelo no Azure ML.

## <a name="parameterize-and-log-model-metrics-from-script"></a>Parametrizar e registrar em log as métricas do modelo do script
No script **iris_sklearn.py**, o padrão esperado para a importar e construir o agente em Python pode ser reduzido para as linhas de código a seguir.

```Python
from azureml.logging import get_azureml_logger
run_logger = get_azureml_logger()
```

Depois de criado, você pode invocar o método **log** com qualquer par de nome/valor.

Quando o desenvolvimento está concluído, costuma ser útil parametrizar scripts para que os valores possam ser passados por meio da linha de comando.
O exemplo a seguir mostra como aceitar parâmetros de linha de comando (quando houver) usando as bibliotecas padrão do Python.
Esse script pega um único parâmetro para a Taxa de Regularização (*reg*) usada para ajustar um modelo de classificação em um esforço para aumentar a *precisão* sem sobreajuste.
Essas variáveis então são registradas como *Taxa de Regularização* e *Precisão* para que o modelo com resultados ideais possa ser identificado com facilidade.

```Python
# change regularization rate and you will likely get a different accuracy.
reg = 0.01
# load regularization rate from argument if present
if len(sys.argv) > 1:
    reg = float(sys.argv[1])

print("Regularization rate is {}".format(reg))

# log the regularization rate
run_logger.log("Regularization Rate", reg)

# train a logistic regression model on the training set
clf1 = LogisticRegression(C=1/reg).fit(X_train, Y_train)
print (clf1)

# evaluate the test set
accuracy = clf1.score(X_test, Y_test)
print ("Accuracy is {}".format(accuracy))

# log accuracy
run_logger.log("Accuracy", accuracy)
```

Executar essas etapas em seus scripts possibilita usar de modo ideal o **Histórico de Execuções**.

## <a name="launch-runs-from-project-dashboard"></a>Inicializar execuções usando o painel de projeto
Retornando para o **Painel do Projeto**, você pode inicializar uma **execução controlada** selecionando o script **iris_sklearn.py** e inserindo o parâmetro **taxa de regularização** na caixa de edição **Argumentos**.

![como inserir parâmetros e iniciar execuções](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-05.png)

Uma vez que inicializar execuções acompanhadas não bloqueia o Azure ML Workbench, várias podem ser inicializadas em paralelo.
O status de cada execução acompanhada está visível no **Painel e Trabalhos** conforme mostrado.

![como acompanhar execuções no painel de trabalhos](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-06.png)

Isso permite a melhor utilização de recursos sem necessidade de executar cada trabalho em série.

## <a name="view-results-in-run-history"></a>Exibir os resultados no Histórico de Execuções
Progresso e os resultados de execuções acompanhadas estão disponíveis para análise no **Histórico de Execuções** do Azure ML Workbench.
O **Histórico de Execuções** oferece três exibições diferentes:
- Painel
- Detalhes
- Comparação

A exibição de **Painel** mostra dados em todas as execuções de um determinado script, renderizados nas formas gráfica e tabular.
A exibição **Detalhes** mostra todos os dados gerados de uma execução específica de um determinado script, incluindo métricas registradas em log e arquivos de saída (como gráficos renderizados). A exibição de **Comparação** permite que os resultados de duas ou três execuções sejam exibidos lado a lado, incluindo também métricas registradas e arquivos de saída.

Em oito execuções rastreadas de **iris_sklearn.py**, valores para o parâmetro **taxa de regularização** e o resultado de **precisão** foram registrados para ilustrar como usar as exibições do Histórico de Execuções.

### <a name="run-history-dashboard"></a>Painel do Histórico de Execuções
Os resultados de todas as oito execuções estão visíveis no **Painel do Histórico de Execuções**.
Como **iris_sklearn.py** logs *taxa de Regularização* e *Precisão*, o **Painel de Histórico de Execuções** exibe gráficos para esses valores por padrão.

![painel do histórico de execuções](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-07.png)

O **Painel Histórico de Execuções** pode ser personalizado para que os valores registrados em log também apareçam na grade.  Clicar no ícone **personalizar** exibe a caixa de diálogo **Personalização da Exibição de Lista** conforme mostrado.

![como personalizar a grade do painel do histórico de execuções](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-08.png)

Quaisquer valores registrados durante execuções acompanhadas estão disponíveis para exibição e selecionar **Taxa de Regularização** e **Precisão** adiciona esses itens à grade.

![valores registrados em log na grade personalizada](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-09.png)

É fácil encontrar execuções interessantes focalizando pontos nos gráficos.  Nesse caso, Executar 7 gerou uma boa precisão e uma baixa duração.

![como localizar uma execução interessante](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-10.png)

Clicar em um ponto associado a Execução 7 em qualquer gráfico ou o link para Execução 7 na grade exibe **Detalhes do Histórico de Execuções**.

### <a name="run-history-details"></a>Detalhes do Histórico de Execuções
Nesta exibição, são exibidos os resultados completos da Execução 7 junto com quaisquer artefatos produzidos pela Execução 7.

![detalhes do histórico de execuções](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-11.png)

A exibição **Detalhes do Histórico de Execuções** também permite a capacidade de **baixar** quaisquer arquivos gravados para a pasta **./outputs** (esses arquivos têm o respaldo do armazenamento em nuvem do Azure ML Workbench para o Histórico de Execuções, que é o assunto de outro artigo).

Por fim, **Detalhes do Histórico de Execuções** oferece uma maneira de restaurar o estado do seu projeto no momento desta execução.
Clicar no botão **Restaurar** exibe uma caixa de diálogo de confirmação, conforme mostrado.

![confirmação de restauração da execução](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-13.png)

Se confirmado, os arquivos poderão ser substituídos ou removidos, portanto, use esse recurso com cuidado.

### <a name="run-history-comparison"></a>Comparação do Histórico de Execuções
Selecionar duas ou três execuções no **Painel de Histórico de Execuções** e clicar em **Comparar** o leva à exibição **Comparação do Histórico de Execuções**.
Como alternativa, clicar em **Comparar** e selecionando uma execução na exibição de **Detalhes do Histórico de Execuções** também abre a exibição **Comparação do Histórico de Execuções**.
Em ambos os casos, a exibição **Comparação do Histórico de Execuções** oferece uma maneira de ver os resultados registrados em log e os artefatos de duas ou três execuções lado a lado.

![comparação do histórico de execuções](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-12.png)

Essa exibição é especialmente útil para a comparação de gráficos, mas, em geral, quaisquer propriedades de execução podem ser comparadas aqui.

### <a name="command-line-interface"></a>Interface de Linha de Comando
O Azure Machine Learning Workbench também dá acesso ao Histórico de Execuções por meio da sua **Interface de Linha de Comando**.
Para acessar a **Interface de Linha de Comando**, clique no menu **Abrir Prompt de Comando** conforme mostrado.

![abrir prompt de comando](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-14.png)

Os comandos disponíveis para o Histórico de Execuções são acessados por meio de `az ml history`, com a ajuda online disponível adicionando o sinalizador `-h`.
```
$ az ml history -h

Group
    az ml history: View run history of machine learning experiments.

Commands:
    compare : Compare two runs.
    download: Download all the artifacts from a run into the destination path.
    info    : Details about one run.
    last    : Get detail about most recent run.
    list    : List runs.
    promote : Promote Artifacts.
```
Esses comandos oferecem os mesmos recursos e retornam os mesmos dados mostrados nas **Exibições do Histórico de Execuções**.
Por exemplo, os resultados da última execução podem ser exibidos como um objeto JSON.
```
$ az ml history last
{
  "Accuracy": 0.6792452830188679,
  "Regularization Rate": 0.078125,
  "attachments": "control_log, control_log.txt, driver_log, driver_log.txt, pid.txt, dataprep/backgroundProcess.log, dataprep/backgroundProcess_Engine.log, dataprep/backgroundProcess_EngineHost.log, dataprep/backgroundProcess_ProjectProvider.log, dataprep/backgroundProcess_Sampling.log, dataprep/backgroundProcess_Telemetry.log, outputs/cm.png, outputs/model.pkl, outputs/sdk_debug.txt, outputs/roc.png",
  "created_utc": "2017-09-08T00:58:01.611105+00:00",
  "description": null,
  "duration": "0:00:04.892389",
  "end_time_utc": "2017-09-08T00:58:07.951403+00:00",
  "experiment_id": "ce92d0a9-3e2c-4d51-85de-93ef22249ce1",
  "heartbeat_enabled": true,
  "hidden": false,
  "name": null,
  "parent_run_id": null,
  "properties": {
    "CommitId": "e77a5f0df2af1a482bbe39b70bfbb16b62228cb3"
  },
  "run_id": "IrisDemo_1504832281116",
  "run_number": 8,
  "script_name": "iris_sklearn.py",
  "start_time_utc": "2017-09-08T00:58:03.059014+00:00",
  "status": "Completed",
  "target": "local",
  "user_id": "e9fafe06-b0e4-4154-8374-aae34f9977b2"
}
```
Além disso, a lista de todas as execuções pode ser exibida em um formato tabular.
```
$ az ml history list -o table
  Accuracy    Regularization Rate  Duration        Run_id                  Script_name      Start_time_utc                    Status
----------  ---------------------  --------------  ----------------------  ---------------  --------------------------------  ---------
  0.679245               0.078125  0:00:04.892389  IrisDemo_1504832281116  iris_sklearn.py  2017-09-08T00:58:03.059014+00:00  Completed
  0.679245               0.15625   0:00:04.734956  IrisDemo_1504832255198  iris_sklearn.py  2017-09-08T00:57:38.507196+00:00  Completed
  0.660377               0.3125    0:00:04.913762  IrisDemo_1504832234904  iris_sklearn.py  2017-09-08T00:57:16.849881+00:00  Completed
  0.660377               0.625     0:00:06.107764  IrisDemo_1504832210767  iris_sklearn.py  2017-09-08T00:56:54.602813+00:00  Completed
  0.641509               1.25      0:00:04.742727  IrisDemo_1504832171373  iris_sklearn.py  2017-09-08T00:56:13.879280+00:00  Completed
  0.660377               2.5       0:00:04.915401  IrisDemo_1504832148526  iris_sklearn.py  2017-09-08T00:55:52.937083+00:00  Completed
  0.641509               5         0:00:04.730627  IrisDemo_1504832127172  iris_sklearn.py  2017-09-08T00:55:29.612382+00:00  Completed
  0.641509              10         0:00:06.059082  IrisDemo_1504832109906  iris_sklearn.py  2017-09-08T00:55:14.739806+00:00  Completed

```
A **Interface de Linha de Comando** é um caminho alternativo para acessar as funcionalidades do Azure Machine Learning Workbench.

## <a name="next-steps"></a>Próximas etapas
Esses recursos estão disponíveis para ajudá-lo no processo de experimentação de ciência de dados.
Esperamos que você os considere úteis e agradeceríamos muito seus comentários.
Essa é apenas nossa implementação inicial e temos uma grande quantidade de aprimoramentos planejados.
Estamos ansiosos para distribuí-los continuamente ao Azure Machine Learning Workbench. 
