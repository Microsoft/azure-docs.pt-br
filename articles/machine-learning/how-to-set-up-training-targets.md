---
title: Configurar uma execução de treinamento
titleSuffix: Azure Machine Learning
description: Treine seu modelo de aprendizado de máquina em vários ambientes de treinamento (destinos de computação). É possível alternar facilmente os ambientes de treinamento.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperf-fy21q1
ms.openlocfilehash: f38fe7d847754247f8c1510527b3ffe026c20be5
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102518493"
---
# <a name="configure-and-submit-training-runs"></a>Configurar e enviar execuções de treinamento

Neste artigo, você aprenderá a configurar e a enviar Azure Machine Learning execuções para treinar seus modelos. Trechos de código explicam as principais partes da configuração e o envio de um script de treinamento.  Em seguida, use um dos [notebooks de exemplo](#notebooks) para encontrar os exemplos de trabalho completos de ponta a ponta.

Ao treinar, é comum iniciar em seu computador local e, posteriormente, escalar horizontalmente para um cluster baseado em nuvem. Com o Azure Machine Learning, você pode executar o script em vários destinos de computação sem precisar alterar o script de treinamento.

Tudo o que você precisa fazer é definir o ambiente para cada destino de computação em uma **configuração de execução de script**.  Em seguida, quando você quiser executar o teste de treinamento em um destino de computação diferente, especifique a configuração de execução para esse tipo de computação.

## <a name="prerequisites"></a>Pré-requisitos

* Caso não tenha uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree) hoje
* O [SDK do Azure Machine Learning para Python](/python/api/overview/azure/ml/install) (>= 1.13.0)
* Um [espaço de trabalho Azure Machine Learning](how-to-manage-workspace.md), `ws`
* Um destino de computação, `my_compute_target` .  [Criar um destino de computação](how-to-create-attach-compute-studio.md) 

## <a name="whats-a-script-run-configuration"></a><a name="whats-a-run-configuration"></a>O que é uma configuração de execução de script?
Um [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) é usado para configurar as informações necessárias para enviar uma execução de treinamento como parte de um experimento.

Você envia seu teste de treinamento com um objeto ScriptRunConfig.  Esse objeto inclui:

* **source_directory**: o diretório de origem que contém o script de treinamento
* **script**: o script de treinamento a ser executado
* **compute_target**: o destino de computação para executar em
* **ambiente**: o ambiente a ser usado ao executar o script
* e algumas opções adicionais configuráveis (consulte a [documentação de referência](/python/api/azureml-core/azureml.core.scriptrunconfig) para obter mais informações)

## <a name="train-your-model"></a><a id="submit"></a>Treinar seu modelo

O padrão de código para enviar uma execução de treinamento é o mesmo para todos os tipos de destinos de computação:

1. Criar um experimento para executar
1. Criar um ambiente no qual o script será executado
1. Criar um ScriptRunConfig, que especifica o ambiente e o destino de computação
1. Enviar a execução
1. Aguarde a conclusão da execução

Ou você pode:

* Envie uma execução de HyperDrive para [ajustar o hiperparâmetro](how-to-tune-hyperparameters.md).
* Envie um experimento por meio da [extensão do VS Code](tutorial-train-deploy-image-classification-model-vscode.md#train-the-model).

## <a name="create-an-experiment"></a>Criar uma experiência

Crie um experimento em seu espaço de trabalho.

```python
from azureml.core import Experiment

experiment_name = 'my_experiment'
experiment = Experiment(workspace=ws, name=experiment_name)
```

## <a name="select-a-compute-target"></a>Selecionar um destino de computação

Selecione o destino de computação no qual o script de treinamento será executado. Se nenhum destino de computação for especificado no ScriptRunConfig, ou se `compute_target='local'` , o Azure ml executará o script localmente. 

O código de exemplo neste artigo pressupõe que você já criou um destino de computação `my_compute_target` na seção "pré-requisitos".

>[!Note]
>Não há suporte para Azure Databricks como um destino de computação para treinamento de modelo. Você pode usar Azure Databricks para tarefas de preparação e implantação de dados. 

## <a name="create-an-environment"></a>Criar um ambiente
Os [ambientes](concept-environments.md) de Azure Machine Learning são um encapsulamento do ambiente no qual o treinamento do Machine Learning acontece. Eles especificam os pacotes do Python, a imagem do Docker, as variáveis de ambiente e as configurações de software em relação aos seus scripts de treinamento e pontuação. Eles também especificam tempos de execução (Python, Spark ou Docker).

Você pode definir seu próprio ambiente ou usar um ambiente organizado do Azure ML. Os [ambientes organizados](./how-to-use-environments.md#use-a-curated-environment) são ambientes predefinidos que estão disponíveis em seu espaço de trabalho por padrão. Esses ambientes são apoiados por imagens do Docker armazenadas em cache, o que reduz o custo de preparação da execução. Consulte [Azure Machine Learning ambientes organizados](./resource-curated-environments.md) para obter a lista completa de ambientes organizados disponíveis.

Para um destino de computação remota, você pode usar um desses ambientes estruturados populares para começar:

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
myenv = Environment.get(workspace=ws, name="AzureML-Minimal")
```

Para obter mais informações e detalhes sobre ambientes, consulte [criar & usar ambientes de software no Azure Machine Learning](how-to-use-environments.md).
  
### <a name="local-compute-target"></a><a name="local"></a>Destino de computação local

Se o seu destino de computação for seu **computador local**, você será responsável por garantir que todos os pacotes necessários estejam disponíveis no ambiente do Python em que o script é executado.  Use o `python.user_managed_dependencies` para usar seu ambiente atual do Python (ou o Python no caminho que você especificar).

```python
from azureml.core import Environment

myenv = Environment("user-managed-env")
myenv.python.user_managed_dependencies = True

# You can choose a specific Python environment by pointing to a Python path 
# myenv.python.interpreter_path = '/home/johndoe/miniconda3/envs/myenv/bin/python'
```

## <a name="create-the-script-run-configuration"></a>Criar a configuração de execução de script

Agora que você tem um destino de computação ( `my_compute_target` ) e um ambiente ( `myenv` ), crie uma configuração de execução de script que execute seu script de treinamento ( `train.py` ) localizado em seu `project_folder` diretório:

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=project_folder,
                      script='train.py',
                      compute_target=my_compute_target,
                      environment=myenv)

# Set compute target
# Skip this if you are running on your local computer
script_run_config.run_config.target = my_compute_target
```

Se você não especificar um ambiente, um ambiente padrão será criado para você.

Se você tiver argumentos de linha de comando que deseja passar para o script de treinamento, poderá especificá-los por meio do **`arguments`** parâmetro do Construtor ScriptRunConfig, por exemplo, `arguments=['--arg1', arg1_val, '--arg2', arg2_val]` .

Se você quiser substituir o tempo máximo padrão permitido para a execução, poderá fazer isso por meio do **`max_run_duration_seconds`** parâmetro. O sistema tentará cancelar automaticamente a execução se demorar mais do que esse valor.

### <a name="specify-a-distributed-job-configuration"></a>Especificar uma configuração de trabalho distribuído
Se você quiser executar um trabalho de treinamento distribuído, forneça a configuração específica do trabalho distribuída para o **`distributed_job_config`** parâmetro. Os tipos de configuração com suporte incluem [MpiConfiguration](/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration), [TensorflowConfiguration](/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration)e [PyTorchConfiguration](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration). 

Para obter mais informações e exemplos sobre a execução de trabalhos distribuídos Horovod, TensorFlow e PyTorch, consulte:

* [Treinar modelos de TensorFlow](./how-to-train-tensorflow.md#distributed-training)
* [Treinar modelos de PyTorch](./how-to-train-pytorch.md#distributed-training)

## <a name="submit-the-experiment"></a>Enviar o teste

```python
run = experiment.submit(config=src)
run.wait_for_completion(show_output=True)
```

> [!IMPORTANT]
> Ao enviar a execução de treinamento, um instantâneo do diretório contendo os scripts de treinamento será criado e enviado ao destino de computação. Ele também é armazenado como parte do experimento em seu workspace. Se você alterar os arquivos e enviar a execução novamente, apenas os arquivos alterados serão carregados.
>
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
> 
> Para obter mais informações sobre instantâneos, consulte [instantâneos](concept-azure-machine-learning-architecture.md#snapshots).

> [!IMPORTANT]
> **Pastas Especiais** Duas pastas, *produzem* e *logs*, recebem tratamento especial do Aprendizado de Máquina do Azure. Durante o treinamento, ao gravar arquivos em pastas denominadas *gera* e *logs* que são relativos ao diretório raiz (`./outputs` e `./logs`, respectivamente), os arquivos serão automaticamente Carregue o histórico de execução para que você tenha acesso a eles quando sua execução for concluída.
>
> Para criar artefatos durante o treinamento (como arquivos de modelo, os pontos de verificação, arquivos de dados ou imagens plotadas) escreva para a pasta `./outputs`.
>
> Da mesma forma, você pode escrever todos os logs da sua corrida de treinamento para a pasta `./logs`. Para utilizar a integração [TensorBoard](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/tensorboard/export-run-history-to-tensorboard/export-run-history-to-tensorboard.ipynb) do Azure Machine Learning, certifique-se de gravar seus logs TensorBoard nessa pasta. Enquanto a sua corrida estiver em andamento, você poderá lançar o TensorBoard e transmitir esses logs.  Mais tarde, você também poderá restaurar os logs de qualquer uma das execuções anteriores.
>
> Por exemplo, para fazer o download de um arquivo gravado na pasta *de saídas* para sua máquina local após a execução do seu treinamento remoto: `run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

## <a name="git-tracking-and-integration"></a><a id="gitintegration"></a>Acompanhamento e integração do Git

Quando você inicia uma execução de treinamento em que o diretório de origem é um repositório Git local, as informações sobre o repositório são armazenadas no histórico de execuções. Para obter mais informações, confira [Integração do Git com o Azure Machine Learning](concept-train-model-git-integration.md).

## <a name="notebook-examples"></a><a name="notebooks"></a>Exemplos de notebook

Consulte estes blocos de anotações para obter exemplos de como configurar execuções para vários cenários de treinamento:
* [Treinamento em vários destinos de computação](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [Treinamento com estruturas ML](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="troubleshooting"></a>Solução de problemas

* **A execução falha `jwt.exceptions.DecodeError` com**: mensagem de erro exata: `jwt.exceptions.DecodeError: It is required that you pass in a value for the "algorithms" argument when calling decode()` . 
    
    Considere atualizar para a versão mais recente do azureml-Core: `pip install -U azureml-core` .
    
    Se você estiver se esgotando nesse problema para execuções locais, verifique a versão do PyJWT instalada em seu ambiente em que você está iniciando a execução. As versões com suporte do PyJWT são < 2.0.0. Desinstale o PyJWT do ambiente se a versão for >= 2.0.0. Você pode verificar a versão do PyJWT, desinstalar e instalar a versão correta da seguinte maneira:
    1. Inicie um shell de comando, ative o ambiente Conda em que o azureml-Core está instalado.
    2. Insira `pip freeze` e procure `PyJWT` , se encontrado, a versão listada deve ser < 2.0.0
    3. Se a versão listada não for uma versão com suporte, `pip uninstall PyJWT` no Shell de comando e digite y para confirmação.
    4. Instale usando `pip install 'PyJWT<2.0.0'`
    
    Se você estiver enviando um ambiente criado pelo usuário com sua execução, considere usar a versão mais recente do azureml-Core nesse ambiente. As versões >= 1.18.0 do azureml-Core já estão fixadas PyJWT < 2.0.0. Se você precisar usar uma versão do azureml-Core < 1.18.0 no ambiente que enviar, certifique-se de especificar PyJWT < 2.0.0 em suas dependências de Pip.


 * **ModuleErrors (nenhum módulo chamado)**: se você estiver executando o ModuleErrors ao enviar experimentos no Azure ml, o script de treinamento estará esperando que um pacote seja instalado, mas não será adicionado. Depois de fornecer o nome do pacote, o Azure ML instala o pacote no ambiente usado para sua execução de treinamento.

    Se você estiver usando estimadores para enviar experimentos, poderá especificar um nome de pacote por meio do `pip_packages` `conda_packages` parâmetro ou no estimador com base em qual fonte você deseja instalar o pacote. Você também pode especificar um arquivo yml com todas as suas dependências usando `conda_dependencies_file` ou listar todos os seus requisitos de Pip em um arquivo txt usando o `pip_requirements_file` parâmetro. Se você tiver seu próprio objeto de ambiente do Azure ML que deseja substituir a imagem padrão usada pelo estimador, você poderá especificar esse ambiente por meio do `environment` parâmetro do Construtor estimador.
    
    As imagens do Docker mantidas pelo Azure ML e seu conteúdo podem ser vistos em [contêineres do AzureML](https://github.com/Azure/AzureML-Containers).
    As dependências específicas à estrutura são listadas na respectiva documentação da estrutura:
    *  [Chainer](/python/api/azureml-train-core/azureml.train.dnn.chainer#remarks)
    * [PyTorch](/python/api/azureml-train-core/azureml.train.dnn.pytorch#remarks)
    * [TensorFlow](/python/api/azureml-train-core/azureml.train.dnn.tensorflow#remarks)
    *  [SKLearn](/python/api/azureml-train-core/azureml.train.sklearn.sklearn#remarks)
    
    > [!Note]
    > Se você considerar que um pacote específico é comum o suficiente para ser adicionado em ambientes e imagens mantidas do Azure ML, gere um problema do GitHub nos [contêineres do AzureML](https://github.com/Azure/AzureML-Containers). 
 
* **NameError (nome não definido), AttributeError (objeto sem atributo)**: essa exceção deve vir de seus scripts de treinamento. Você pode examinar os arquivos de log de portal do Azure para obter mais informações sobre o nome específico não definido ou erro de atributo. No SDK, você pode usar `run.get_details()` para examinar a mensagem de erro. Isso também listará todos os arquivos de log gerados para sua execução. Certifique-se de dar uma olhada no script de treinamento e corrija o erro antes de reenviar sua execução. 


* **Execução ou teste de exclusão**: os experimentos podem ser arquivados usando o método [experimento. Archive](/python/api/azureml-core/azureml.core.experiment%28class%29#archive--) ou da exibição guia do experimento no cliente Azure Machine Learning Studio por meio do botão "arquivo de teste". Essa ação oculta o experimento de consultas de lista e exibições, mas não a exclui.

    Atualmente, não há suporte para a exclusão permanente de execuções ou experimentos individuais. Para obter mais informações sobre como excluir ativos de espaço de trabalho, consulte [exportar ou excluir seus dados de espaço de trabalho do serviço Machine Learning](how-to-export-delete-data.md).

* O **documento de métrica é muito grande**: Azure Machine Learning tem limites internos sobre o tamanho dos objetos de métrica que podem ser registrados de uma só vez a partir de uma execução de treinamento. Se você encontrar o erro "o Documento de Métrica é muito grande" ao registrar em log uma métrica com valor de lista, tente dividir a lista em partes menores, por exemplo:

    ```python
    run.log_list("my metric name", my_metric[:N])
    run.log_list("my metric name", my_metric[N:])
    ```

    Internamente, o Azure Machine Learning concatena os blocos com o mesmo nome de métrica em uma lista contígua.

* O **destino de computação leva muito tempo para ser iniciado**: as imagens do Docker para destinos de computação são carregadas do ACR (registro de contêiner do Azure). Por padrão, Azure Machine Learning cria um ACR que usa a camada de serviço *básica* . Alterar o ACR para o seu espaço de trabalho para a camada Standard ou Premium pode reduzir o tempo necessário para criar e carregar imagens. Para obter mais informações, confira [Níveis de serviço do Registro de Contêiner do Azure](../container-registry/container-registry-skus.md).

## <a name="next-steps"></a>Próximas etapas

* [Tutorial: Treinar um modelo](tutorial-train-models-with-aml.md) usa um destino de computação gerenciado para treinar um modelo.
* Veja como treinar modelos com estruturas ML específicas, como [Scikit-Learn](how-to-train-scikit-learn.md), [TensorFlow](how-to-train-tensorflow.md)e [PyTorch](how-to-train-pytorch.md).
* Saiba como [ajustar hiperparâmetros de modo eficiente](how-to-tune-hyperparameters.md) para criar modelos melhores.
* Quando você tiver um modelo treinado, aprenda [como e em que local implantar modelos](how-to-deploy-and-where.md).
* Exiba a referência do SDK da [classe ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) .
* [Usar o Azure Machine Learning com Redes Virtuais do Azure](./how-to-network-security-overview.md)
