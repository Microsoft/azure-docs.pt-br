---
title: Como solucionar problemas do ParallelRunStep
titleSuffix: Azure Machine Learning
description: Dicas sobre como solucionar problemas ao obter erros usando o ParallelRunStep em pipelines do machine learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: troubleshooting
ms.custom: troubleshooting
ms.reviewer: larryfr, vaidyas, laobri, tracych
ms.author: pansav
author: psavdekar
ms.date: 09/23/2020
ms.openlocfilehash: 619123cc2723fcf8e4bd80410c6b098b113d61c6
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286310"
---
# <a name="troubleshooting-the-parallelrunstep"></a>Como solucionar problemas do ParallelRunStep

Neste artigo, você aprenderá a solucionar problemas ao obter erros, usando a classe [ParallelRunStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallel_run_step.parallelrunstep) do [SDK do Azure Machine Learning](/python/api/overview/azure/ml/intro).

Para obter dicas gerais sobre como solucionar problemas de um pipeline, confira [Como solucionar problemas de pipelines do machine learning](how-to-debug-pipelines.md).

## <a name="testing-scripts-locally"></a>Testar scripts localmente

 O ParallelRunStep é executado como uma etapa em pipelines de ML. Talvez você queira [testar os scripts localmente](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-machine-learning-pipelines) como uma primeira etapa.

##  <a name="script-requirements"></a>Requisitos de script

O script para um `ParallelRunStep` *deve conter* duas funções:
- `init()`: Use essa função para qualquer preparação dispendiosa ou comum para inferência posterior. Por exemplo, use-a para carregar o modelo em um objeto global. Essa função será chamada apenas uma vez no início do processo.
-  `run(mini_batch)`: A função será executada para cada instância de `mini_batch`.
    -  `mini_batch`: `ParallelRunStep` invocará o método run e transmitirá uma lista ou o `DataFrame` Pandas como um argumento para o método. Cada entrada em min_batch será um caminho de arquivo se a entrada for um `FileDataset` ou um `DataFrame` Pandas se a entrada for um `TabularDataset`.
    -  `response`: o método run() deve retornar um `DataFrame` Pandas ou uma matriz. Para append_row output_action, esses elementos retornados são acrescentados ao arquivo de saída comum. Para summary_only, o conteúdo dos elementos é ignorado. Para todas as ações de saída, cada elemento de saída retornado indica uma execução bem-sucedida do elemento de entrada no minilote de entrada. Verifique se dados suficientes foram incluídos no resultado da execução para mapear a entrada para o resultado da saída da execução. A saída de execução será gravada no arquivo de saída e não haverá garantia de que esteja em ordem; você deverá usar uma chave na saída para mapeá-la para a entrada.

```python
%%writefile digit_identification.py
# Snippets from a sample script.
# Refer to the accompanying digit_identification.py
# (https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines/parallel-run)
# for the implementation script.

import os
import numpy as np
import tensorflow as tf
from PIL import Image
from azureml.core import Model


def init():
    global g_tf_sess

    # Pull down the model from the workspace
    model_path = Model.get_model_path("mnist")

    # Construct a graph to execute
    tf.reset_default_graph()
    saver = tf.train.import_meta_graph(os.path.join(model_path, 'mnist-tf.model.meta'))
    g_tf_sess = tf.Session()
    saver.restore(g_tf_sess, os.path.join(model_path, 'mnist-tf.model'))


def run(mini_batch):
    print(f'run method start: {__file__}, run({mini_batch})')
    resultList = []
    in_tensor = g_tf_sess.graph.get_tensor_by_name("network/X:0")
    output = g_tf_sess.graph.get_tensor_by_name("network/output/MatMul:0")

    for image in mini_batch:
        # Prepare each image
        data = Image.open(image)
        np_im = np.array(data).reshape((1, 784))
        # Perform inference
        inference_result = output.eval(feed_dict={in_tensor: np_im}, session=g_tf_sess)
        # Find the best probability, and add it to the result list
        best_result = np.argmax(inference_result)
        resultList.append("{}: {}".format(os.path.basename(image), best_result))

    return resultList
```

Se você tiver outro arquivo ou pasta no mesmo diretório que o script de inferência, poderá consultá-lo localizando o diretório de trabalho atual.

```python
script_dir = os.path.realpath(os.path.join(__file__, '..',))
file_path = os.path.join(script_dir, "<file_name>")
```

### <a name="parameters-for-parallelrunconfig"></a>Parâmetros para ParallelRunConfig

`ParallelRunConfig` é a principal configuração para a instância `ParallelRunStep` no pipeline do Azure Machine Learning. Use-a para encapsular o script e configurar os parâmetros necessários, incluindo todas as seguintes entradas:
- `entry_script`: Um script de usuário como um caminho de arquivo local que será executado em paralelo em vários nós. Se `source_directory` estiver presente, use um caminho relativo. Caso contrário, use qualquer caminho que seja acessível no computador.
- `mini_batch_size`: O tamanho do minilote passado para uma única chamada de `run()`. (opcional; o valor padrão são arquivos `10` para `FileDataset` e `1MB` para `TabularDataset`.)
    - Para `FileDataset`, é o número de arquivos com um valor mínimo de `1`. Você pode combinar vários arquivos em um minilote.
    - Para `TabularDataset`, é o tamanho dos dados. Os valores de exemplo são `1024`, `1024KB`, `10MB` e `1GB`. O valor recomendado é `1MB`. O minilote de `TabularDataset` nunca ultrapassará os limites do arquivo. Por exemplo, se você tiver arquivos .csv com vários tamanhos, o menor arquivo será de 100 KB, e o maior será de 10 MB. Se você definir `mini_batch_size = 1MB`, os arquivos com um tamanho menor que 1 MB serão tratados como um minilote. Arquivos com um tamanho maior que 1 MB serão divididos em vários minilotes.
        > [!NOTE]
        > TabularDatasets com o respaldo de SQL não pode ser particionado. 

- `error_threshold`: O número de falhas de registro para `TabularDataset` e falhas de arquivo para `FileDataset` que devem ser ignorados durante o processamento. Se a contagem de erros de toda a entrada ficar acima desse valor, o trabalho será anulado. O limite de erro é para toda a entrada, não para um minilote individual enviado ao método `run()`. O intervalo é `[-1, int.max]`. A parte `-1` indica que é para ignorar todas as falhas durante o processamento.
- `output_action`: Um dos seguintes valores indica como a saída será organizada:
    - `summary_only`: O script de usuário armazenará a saída. `ParallelRunStep` usará a saída somente para o cálculo do limite de erro.
    - `append_row`: para todas as entradas, somente um arquivo será criado na pasta de saída para acrescentar todas as saídas separadas por linha.
- `append_row_file_name`: para personalizar o nome do arquivo de saída para append_row output_action (opcional; o valor padrão é `parallel_run_step.txt`).
- `source_directory`: Caminhos para pastas que contêm todos os arquivos a serem executados no destino de computação (opcional).
- `compute_target`: Apenas `AmlCompute` tem suporte.
- `node_count`: O número de nós de computação a serem usados para executar o script do usuário.
- `process_count_per_node`: O número de processos por nó. A melhor prática é definir como o número de GPU ou CPU que um nó tem (opcional; o valor padrão é `1`).
- `environment`: A definição de ambiente Python. Você pode configurá-lo para usar um ambiente Python existente ou para configurar um ambiente temporário. A definição também é responsável por configurar as dependências de aplicativo necessárias (opcional).
- `logging_level`: Detalhamento do log. Os valores no detalhamento crescente são: `WARNING`, `INFO` e `DEBUG`. (opcional; o valor padrão é `INFO`)
- `run_invocation_timeout`: O tempo limite de invocação do método `run()` em segundos. (opcional; o valor padrão é `60`)
- `run_max_try`: contagem máxima de tentativas de `run()` para um minilote. Um `run()` falhará se uma exceção for gerada ou nada será retornado quando `run_invocation_timeout` for atingido (opcional; o valor padrão é `3`). 

Especifique `mini_batch_size`, `node_count`, `process_count_per_node`, `logging_level`, `run_invocation_timeout` e `run_max_try` como `PipelineParameter`, de modo que, ao reenviar uma execução de pipeline, você possa ajustar os valores de parâmetro. Neste exemplo, você usa `PipelineParameter` para `mini_batch_size` e `Process_count_per_node` e alterará esses valores ao reenviar outra execução. 

### <a name="parameters-for-creating-the-parallelrunstep"></a>Parâmetros para a criação do ParallelRunStep

Crie o ParallelRunStep usando o script, a configuração do ambiente e os parâmetros. Especifique o destino de computação que você já anexou ao seu workspace como o destino de execução do seu script de inferência. Use `ParallelRunStep` para criar a etapa do pipeline de inferência de lote, que usa todos os seguintes parâmetros:
- `name`: O nome da etapa, com as seguintes restrições de nomenclatura: unique, 3-32 characters e regex ^\[a-z\]([-a-z0-9]*[a-z0-9])?$.
- `parallel_run_config`: Um objeto `ParallelRunConfig`, conforme definido anteriormente.
- `inputs`: um ou mais conjuntos de dados do Azure Machine Learning de tipo único a serem particionados para processamento paralelo.
- `side_inputs`: um ou mais dados de referência ou conjuntos de dados usados como entradas laterais sem a necessidade de partição.
- `output`: um objeto `OutputFileDatasetConfig` que representa o caminho do diretório no qual os dados de saída serão armazenados.
- `arguments`: uma lista de argumentos passados para o script do usuário. Use unknown_args para recuperá-los em seu script de entrada (opcional).
- `allow_reuse`: Se a etapa deve reutilizar os resultados anteriores quando executada com as mesmas configurações/entradas. Se esse parâmetro for `False`, uma nova execução sempre será gerada para essa etapa durante a execução do pipeline. (opcional; o valor padrão é `True`.)

```python
from azureml.pipeline.steps import ParallelRunStep

parallelrun_step = ParallelRunStep(
    name="predict-digits-mnist",
    parallel_run_config=parallel_run_config,
    inputs=[input_mnist_ds_consumption],
    output=output_dir,
    allow_reuse=True
)
```

## <a name="debugging-scripts-from-remote-context"></a>Depurar scripts do contexto remoto

A transição da depuração de um script de pontuação localmente para depurar um script de pontuação em um pipeline real pode ser um passo difícil. Para obter informações sobre como localizar os logs no portal, confira a [seção pipelines do machine learning em scripts de depuração com base em um contexto remoto](how-to-debug-pipelines.md). As informações contidas nessa seção também se aplicam a um ParallelRunStep.

Por exemplo, o arquivo de log `70_driver_log.txt` contém informações do controlador que inicia o código ParallelRunStep.

Devido à natureza distribuída dos trabalhos do ParallelRunStep, há logs de várias fontes diferentes. No entanto, são criados dois arquivos consolidados que fornecem informações de alto nível:

- `~/logs/job_progress_overview.txt`: Esse arquivo fornece informações de alto nível sobre o número de mini-lotes (também conhecidos como tarefas) criados até o momento e o número de mini-lotes processados até o momento. Nesse final, ele mostra o resultado do trabalho. Se o trabalho tiver falhado, ele mostrará a mensagem de erro e onde iniciar a solução de problemas.

- `~/logs/sys/master_role.txt`: esse arquivo fornece a exibição do nó principal (também conhecido como orquestrador) do trabalho em execução. Inclui a criação de tarefas, o monitoramento de progresso, o resultado da execução.

Os logs gerados do script de entrada usando o auxiliar EntryScript e as instruções PRINT serão encontrados nos seguintes arquivos:

- `~/logs/user/entry_script_log/<ip_address>/<process_name>.log.txt`: esses arquivos são os logs gravados de entry_script usando o auxiliar EntryScript.

- `~/logs/user/stdout/<ip_address>/<process_name>.stdout.txt`: esses arquivos são os logs de stdout (por exemplo, a instrução PRINT) de entry_script.

- `~/logs/user/stderr/<ip_address>/<process_name>.stderr.txt`: esses arquivos são os logs de stderr de entry_script.

Para uma compreensão concisa dos erros em seu script, há:

- `~/logs/user/error.txt`: Este arquivo tentará resumir os erros em seu script.

Para obter mais informações sobre erros no seu script, há:

- `~/logs/user/error/`: contém rastreamentos de pilha completos de exceções geradas durante o carregamento e a execução do script de entrada.

Quando você precisar de um entendimento completo de como cada nó executou o script de pontuação, examine os logs de processo individuais para cada nó. Os logs de processo podem ser encontrados na pasta `sys/node`, agrupados por nós de trabalho:

- `~/logs/sys/node/<ip_address>/<process_name>.txt`: esse arquivo fornece informações detalhadas sobre cada minilote à medida que ele é selecionado ou concluído por um trabalho. Para cada mini-lote, esse arquivo inclui:

    - O endereço IP e o PID do processo de trabalho. 
    - O número total de itens, contagem de itens processados com êxito e contagem de itens com falha.
    - A hora de início, a duração, o tempo de processamento e o tempo do método de execução.

Você também pode ver os resultados de verificações periódicas do uso de recursos para cada nó. Os arquivos de log e de instalação estão nesta pasta:

- `~/logs/perf`: defina `--resource_monitor_interval` para alterar o intervalo de verificação em segundos. O intervalo padrão é `600`, que é de aproximadamente 10 minutos. Para interromper o monitoramento, defina o valor como `0`. Cada pasta `<ip_address>` inclui:

    - `os/`: informações sobre todos os processos em execução no nó. Uma verificação executa um comando do sistema operacional e salva o resultado em um arquivo. No Linux, o comando é `ps`. No Windows, use `tasklist`.
        - `%Y%m%d%H`: o nome da subpasta é o horário.
            - `processes_%M`: o arquivo termina com o minuto do horário de verificação.
    - `node_disk_usage.csv`: uso detalhado do disco do nó.
    - `node_resource_usage.csv`: visão geral do uso de recursos do nó.
    - `processes_resource_usage.csv`: visão geral do uso de recursos de cada processo.

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>Como fazer log do meu script de usuário a partir de um contexto remoto?

O ParallelRunStep pode executar vários processos em um nó com base em process_count_per_node. Para organizar os logs de cada processo no nó e combinar a instrução PRINT e LOG, recomendamos o uso do agente do ParallelRunStep, conforme mostrado abaixo. Você obtém um agente do EntryScript e faz com que os logs apareçam na pasta **logs/usuário** no portal.

**Um script de entrada de exemplo usando o agente:**
```python
from azureml_user.parallel_run import EntryScript

def init():
    """ Initialize the node."""
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug("This will show up in files under logs/user on the Azure portal.")


def run(mini_batch):
    """ Accept and return the list back."""
    # This class is in singleton pattern and will return same instance as the one in init()
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug(f"{__file__}: {mini_batch}.")
    ...

    return mini_batch
```

### <a name="how-could-i-pass-a-side-input-such-as-a-file-or-files-containing-a-lookup-table-to-all-my-workers"></a>Como posso passar uma entrada no lado, como um arquivo ou arquivo(s) contendo uma tabela de pesquisa, para todos os meus trabalhos?

O usuário pode passar dados de referência para o script usando o parâmetro side_inputs do ParalleRunStep. Todos os conjuntos de dados fornecidos como side_inputs serão montados em cada nó de trabalho. O usuário pode obter a localização da montagem passando um argumento.

Construa um [Conjunto de dados](/python/api/azureml-core/azureml.core.dataset.dataset) contendo os dados de referência, especifique um caminho de montagem local e registre-o no espaço de trabalho. Passe-o para o parâmetro `side_inputs` do seu `ParallelRunStep`. Além disso, você pode adicionar o caminho na seção `arguments` para acessar facilmente o caminho montado:

```python
local_path = "/tmp/{}".format(str(uuid.uuid4()))
label_config = label_ds.as_named_input("labels_input").as_mount(local_path)
batch_score_step = ParallelRunStep(
    name=parallel_step_name,
    inputs=[input_images.as_named_input("input_images")],
    output=output_dir,
    arguments=["--labels_dir", label_config],
    side_inputs=[label_config],
    parallel_run_config=parallel_run_config,
)
```

Depois disso, você pode acessá-lo em seu script de inferência (por exemplo, em seu método init()) da seguinte maneira:

```python
parser = argparse.ArgumentParser()
parser.add_argument('--labels_dir', dest="labels_dir", required=True)
args, _ = parser.parse_known_args()

labels_path = args.labels_dir
```

### <a name="how-to-use-input-datasets-with-service-principal-authentication"></a>Como usar conjuntos de dados de entrada com autenticação de entidade de serviço?

O usuário pode passar conjuntos de dados de entrada com a autenticação de entidade de serviço usada no espaço de trabalho. O uso desse conjunto de dados do ParallelRunStep requer que esse conjunto de dados seja registrado para construir a configuração do ParallelRunStep.

```python
service_principal = ServicePrincipalAuthentication(
    tenant_id="***",
    service_principal_id="***",
    service_principal_password="***")
 
ws = Workspace(
    subscription_id="***",
    resource_group="***",
    workspace_name="***",
    auth=service_principal
    )
 
default_blob_store = ws.get_default_datastore() # or Datastore(ws, '***datastore-name***') 
ds = Dataset.File.from_files(default_blob_store, '**path***')
registered_ds = ds.register(ws, '***dataset-name***', create_new_version=True)
```

## <a name="next-steps"></a>Próximas etapas

* Confira esses [Jupyter notebooks que demonstram pipelines do Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines)

* Confira a referência do SDK para obter ajuda com o pacote [azureml-pipeline-steps](/python/api/azureml-pipeline-steps/azureml.pipeline.steps). Veja a [documentação](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunstep) de referência para a classe ParallelRunStep.

* Siga o [tutorial avançado](tutorial-pipeline-batch-scoring-classification.md) sobre como usar pipelines com o ParallelRunStep. O tutorial mostra como passar outro arquivo como uma entrada lateral.
