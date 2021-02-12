---
title: 'Tutorial: Executar um aplicativo "Olá, Mundo!" Script do Python'
titleSuffix: Azure Machine Learning
description: A parte 2 da série de introdução do Azure Machine Learning mostra como enviar um script Python trivial "Olá, Mundo!" para a nuvem.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 09/15/2020
ms.custom: devx-track-python
ms.openlocfilehash: 43a483f49a9e9004a4f487e82195198f2600a919
ms.sourcegitcommit: 3af12dc5b0b3833acb5d591d0d5a398c926919c8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/11/2021
ms.locfileid: "98071146"
---
# <a name="tutorial-run-a-hello-world-python-script-part-2-of-4"></a>Tutorial: Executar um script Python "Olá, Mundo!" (parte 2 de 4)

Neste tutorial, você aprenderá a usar o SDK do Azure Machine Learning para Python para enviar e executar um script "Olá, Mundo!" do Python .

Este tutorial é a *parte 2 de uma série de tutoriais de quatro partes* em que você aprenderá os conceitos básicos do Azure Machine Learning e realizará tarefas de machine learning baseadas em trabalhos no Azure. Este tutorial se baseia no trabalho que você concluiu na [Parte 1: Configurar o computador local para o Azure Machine Learning](tutorial-1st-experiment-sdk-setup-local.md).

Neste tutorial, você irá:

> [!div class="checklist"]
> * Criar e executar um script Python "Olá, Mundo!" localmente.
> * Criar um script de controle Python para enviar "Olá, Mundo!" ao Azure Machine Learning.
> * Entender os conceitos do Azure Machine Learning no script de controle.
> * Enviar e executar o script "Olá, Mundo!" .
> * Exibe a saída do código na nuvem.

## <a name="prerequisites"></a>Pré-requisitos

- Conclusão da [parte 1](tutorial-1st-experiment-sdk-setup-local.md), caso você ainda não tenha um Workspace do Azure Machine Learning.

## <a name="create-and-run-a-python-script-locally"></a>Criar e executar um script do Python localmente

Crie um subdiretório chamado `src` no diretório `tutorial` para armazenar o código que você deseja executar em um cluster de computação do Azure Machine Learning. No subdiretório `src`, crie o script Python `hello.py`:

```python
# src/hello.py
print("Hello world!")
```

A estrutura do diretório do projeto agora terá a seguinte aparência:

```Bash
tutorial
└──.azureml
|  └──config.json
└──src
|  └──hello.py
└──01-create-workspace.py
└──02-create-compute.py
```


### <a name="test-your-script-locally"></a><a name="test"></a>Testar seu script localmente

Execute o código localmente usando seu IDE favorito ou um terminal. A execução local do código traz o benefício da depuração interativa do código.  Na janela que tem o ambiente do Conda *tutorial1* ativado, execute o arquivo do Python:

```bash
cd <path/to/tutorial>
python ./src/hello.py
```

> [!div class="nextstepaction"]
> [Executei o script localmente](?success=run-local#control-script) [Encontrei um problema](https://www.research.net/r/7C2NTH7?issue=run-local)

## <a name="create-a-control-script"></a><a name="control-script"></a> Criar um script de controle

Um *script de controle* permite que você execute o script `hello.py` na nuvem. Use o script de controle para controlar como e em que local o código de machine learning é executado.  

No diretório do tutorial, crie um arquivo do Python chamado `03-run-hello.py` e copie e cole o seguinte código nesse arquivo:

```python
# tutorial/03-run-hello.py
from azureml.core import Workspace, Experiment, Environment, ScriptRunConfig

ws = Workspace.from_config()
experiment = Experiment(workspace=ws, name='day1-experiment-hello')

config = ScriptRunConfig(source_directory='./src', script='hello.py', compute_target='cpu-cluster')

run = experiment.submit(config)
aml_url = run.get_portal_url()
print(aml_url)
```

### <a name="understand-the-code"></a>Compreender o código

Aqui está uma descrição de como o script de controle funciona:

:::row:::
   :::column span="":::
      `ws = Workspace.from_config()`
   :::column-end:::
   :::column span="2":::
      O [workspace](/python/api/azureml-core/azureml.core.workspace.workspace?preserve-view=true&view=azure-ml-py) conecta-se ao workspace do Azure Machine Learning, de modo que você possa se comunicar com seus recursos do Azure Machine Learning.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `experiment =  Experiment( ... )`
   :::column-end:::
   :::column span="2":::
      O [experimento](/python/api/azureml-core/azureml.core.experiment.experiment?preserve-view=true&view=azure-ml-py) oferece uma forma simples de organizar várias execuções com um único nome. Mais tarde, você pode ver como os experimentos facilitam a comparação de métricas entre dezenas de execuções.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config = ScriptRunConfig( ... )` 
   :::column-end:::
   :::column span="2":::
      [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py) encapsula seu código `hello.py` e o passa para seu workspace. Como o nome sugere, você pode usar essa classe para _configurar_ como deseja que seu _script_ seja _executado_ no Azure Machine Learning. Ela também especifica em qual destino de computação o script será executado. Nesse código, o destino é o cluster de cálculo criado no [tutorial de configuração](tutorial-1st-experiment-sdk-setup-local.md).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `run = experiment.submit(config)`
   :::column-end:::
   :::column span="2":::
       Envia o seu script. Esse envio é chamado de [execução](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py). Uma execução encapsula uma única execução do seu código. Use uma execução para monitorar o progresso do script, capturar a saída, analisar os resultados, visualizar métricas, entre outros.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `aml_url = run.get_portal_url()` 
   :::column-end:::
   :::column span="2":::
        O objeto `run` fornece um identificador na execução do seu código. Monitore o progresso no Azure Machine Learning Studio com a URL impressa do script Python.  
   :::column-end:::
:::row-end:::

> [!div class="nextstepaction"]
> [Criei o script de controle](?success=create-control-script#submit) [Encontrei um problema](https://www.research.net/r/7C2NTH7?issue=create-control-script)

## <a name="submit-and-run-your-code-in-the-cloud"></a><a name="submit"></a> Enviar e executar seu código na nuvem

Execute o script de controle que, por sua vez, executa `hello.py` no cluster de cálculo criado no [tutorial de configuração](tutorial-1st-experiment-sdk-setup-local.md).


```bash
python 03-run-hello.py
```

> [!TIP]
> Se a execução desse código fornecer um erro informando que você não tem acesso à assinatura, confira [Conectar-se a um workspace](how-to-manage-workspace.md?tab=python#connect-multi-tenant) para obter informações sobre as opções de autenticação.

> [!div class="nextstepaction"]
> [Enviei o código na nuvem](?success=submit-to-cloud#monitor) [Encontrei um problema](https://www.research.net/r/7C2NTH7?issue=submit-to-cloud)

## <a name="monitor-your-code-in-the-cloud-by-using-the-studio"></a><a name="monitor"></a>Monitorar o código na nuvem usando o estúdio

A saída do seu script conterá um link para o estúdio semelhante a este: `https://ml.azure.com/experiments/hello-world/runs/<run-id>?wsid=/subscriptions/<subscription-id>/resourcegroups/<resource-group>/workspaces/<workspace-name>`.

Siga o link.  A princípio, você verá um status de **Preparando**.  A primeira execução levará entre 5 e 10 minutos para ser concluída. Isso ocorrerá devido ao seguinte:

* Uma imagem do Docker será criada na nuvem
* O cluster de cálculo será redimensionado de 0 para 1 nó
* A imagem do Docker será baixada na computação. 

Execuções subsequentes são muito mais rápidas (cerca de 15 segundos) à medida que a imagem do Docker é armazenada em cache no computador. Você pode testar isso reenviando o código abaixo após a conclusão da primeira execução.

Quando o trabalho for concluído, vá para a guia **Saídas + logs**. Nela, você verá um arquivo `70_driver_log.txt` semelhante a este:

```txt
 1: [2020-08-04T22:15:44.407305] Entering context manager injector.
 2: [context_manager_injector.py] Command line Options: Namespace(inject=['ProjectPythonPath:context_managers.ProjectPythonPath', 'RunHistory:context_managers.RunHistory', 'TrackUserError:context_managers.TrackUserError', 'UserExceptions:context_managers.UserExceptions'], invocation=['hello.py'])
 3: Starting the daemon thread to refresh tokens in background for process with pid = 31263
 4: Entering Run History Context Manager.
 5: Preparing to call script [ hello.py ] with arguments: []
 6: After variable expansion, calling script [ hello.py ] with arguments: []
 7:
 8: Hello world!
 9: Starting the daemon thread to refresh tokens in background for process with pid = 31263
10:
11:
12: The experiment completed successfully. Finalizing run...
13: Logging experiment finalizing status in history service.
14: [2020-08-04T22:15:46.541334] TimeoutHandler __init__
15: [2020-08-04T22:15:46.541396] TimeoutHandler __enter__
16: Cleaning up all outstanding Run operations, waiting 300.0 seconds
17: 1 items cleaning up...
18: Cleanup took 0.1812913417816162 seconds
19: [2020-08-04T22:15:47.040203] TimeoutHandler __exit__
```

Na linha 8, você vê "Olá, mundo!" saída.

O arquivo `70_driver_log.txt` contém a saída padrão de uma execução. Esse arquivo poderá ser útil quando você estiver depurando execuções remotas na nuvem.

> [!div class="nextstepaction"]
> [Vi o log no estúdio](?success=monitor-in-studio#next-steps) [Encontrei um problema](https://www.research.net/r/7C2NTH7?issue=monitor-in-studio)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você usou um script "Olá, Mundo!" simples e o executou no Azure. Você viu como se conectar ao seu Workspace do Azure Machine Learning, criar um experimento e enviar o código `hello.py` para a nuvem.

No próximo tutorial, você vai aprimorar esses aprendizados executando algo mais interessante do que `print("Hello world!")`.

> [!div class="nextstepaction"]
> [Tutorial: Treinar um modelo](tutorial-1st-experiment-sdk-train.md)

>[!NOTE] 
> Caso deseje concluir a série de tutoriais aqui e não progredir para a próxima etapa, lembre-se de [limpar seus recursos](tutorial-1st-experiment-bring-data.md#clean-up-resources).