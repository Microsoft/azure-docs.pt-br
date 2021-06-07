---
title: 'Tutorial: Treinar seu primeiro modelo de machine learning – Python'
titleSuffix: Azure Machine Learning
description: A parte 3 da série de introdução do Azure Machine Learning mostra como treinar um modelo de machine learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 02/11/2021
ms.custom: devx-track-python, contperf-fy21q3
ms.openlocfilehash: 7e035406ce220728c316aaa2005e0f4d6089b5e2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105935508"
---
# <a name="tutorial-train-your-first-machine-learning-model-part-3-of-4"></a>Tutorial: Treinar seu primeiro modelo de machine learning (parte 3 de 4)

Este tutorial mostra como treinar um modelo de machine learning no Azure Machine Learning.

Este tutorial é a *parte 3 de uma série de tutoriais de quatro partes* em que você aprenderá os conceitos básicos do Azure Machine Learning e realizará tarefas de machine learning baseadas em trabalhos no Azure. Ele se baseia no trabalho que você concluiu na [Parte 1: Configurar](tutorial-1st-experiment-sdk-setup-local.md) e [Parte 2: Executar um script "Olá, Mundo!"](tutorial-1st-experiment-hello-world.md) da série.

Neste tutorial, você seguirá a próxima etapa enviando um script que treina um modelo de machine learning. Este exemplo ajudará você a entender como o Azure Machine Learning facilita o comportamento consistente entre a depuração local e as execuções remotas.

Neste tutorial, você:

> [!div class="checklist"]
> * Crie um script de treinamento.
> * Usa o Conda para definir um ambiente do Azure Machine Learning.
> * Cria um script de controle.
> * Aprende os conceitos básicos das classes do Azure Machine Learning (`Environment`, `Run`, `Metrics`).
> * Envia e executa seu script de treinamento.
> * Exibe a saída do código na nuvem.
> * Registra em log as métricas para o Azure Machine Learning.
> * Exibe suas métricas na nuvem.

## <a name="prerequisites"></a>Pré-requisitos

- [Anaconda](https://www.anaconda.com/download/) ou [Miniconda](https://www.anaconda.com/download/) para gerenciar os ambientes virtuais Python e instalar pacotes.
- Conclusão da [parte 1](tutorial-1st-experiment-sdk-setup-local.md) e da [parte 2](tutorial-1st-experiment-hello-world.md) da série.

## <a name="create-training-scripts"></a>Criar scripts de treinamento

Primeiro, você define a arquitetura de rede neural em um arquivo `model.py`. Todo o seu código de treinamento entrará no subdiretório `src`, incluindo `model.py`.

O código a seguir é obtido [deste exemplo introdutório](https://pytorch.org/tutorials/beginner/blitz/cifar10_tutorial.html) do PyTorch. Observe que os conceitos do Azure Machine Learning aplicam-se a qualquer código de machine learning, não apenas ao PyTorch.

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/IDE-users/src/model.py":::

Em seguida, você define o script de treinamento. Esse script baixa o conjunto de dados CIFAR10 usando as APIs `torchvision.dataset` do PyTorch, configura a rede definida em `model.py` e a treina por duas épocas usando SGD padrão e perda de entropia cruzada.

Crie um script de `train.py` no subdiretório `src`:

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/IDE-users/src/train.py":::

Agora você tem a seguinte estrutura de diretório:

:::image type="content" source="media/tutorial-1st-experiment-sdk-train/directory-structure.png" alt-text="A estrutura de diretório mostra train.py no subdiretório src":::


> [!div class="nextstepaction"]
> [Criei os scripts de treinamento](?success=create-scripts#environment) [Encontrei um problema](https://www.research.net/r/7CTJQQN?issue=create-scripts)

## <a name="create-a-new-python-environment"></a><a name="environment"></a> Criar um ambiente do Python

Crie um arquivo chamado `pytorch-env.yml` no diretório `.azureml` oculto:

:::code language="yml" source="~/MachineLearningNotebooks/tutorials/get-started-day1/IDE-users/environments/pytorch-env.yml":::

Esse ambiente tem todas as dependências que o seu modelo e o script de treinamento exigem. Observe que não há nenhuma dependência do SDK do Azure Machine Learning para Python.

> [!div class="nextstepaction"]
> [Criei o arquivo de ambiente](?success=create-env-file#test-local) [Encontrei um problema](https://www.research.net/r/7CTJQQN?issue=create-env-file)

## <a name="test-locally"></a><a name="test-local"></a> Testar localmente

Em uma janela de terminal ou Prompt do Anaconda, use o código a seguir para testar seu script localmente no novo ambiente.  

```bash
conda deactivate                                # If you are still using the tutorial environment, exit it
conda env create -f .azureml/pytorch-env.yml    # create the new Conda environment
conda activate pytorch-env                      # activate new Conda environment
python src/train.py                             # train model
```

Depois de executar esse script, você verá os dados baixados em um diretório chamado `tutorial/data`.

> [!div class="nextstepaction"]
> [Executei o código localmente](?success=test-local#create-local) [Encontrei um problema](https://www.research.net/r/7CTJQQN?issue=test-local)

## <a name="create-the-control-script"></a><a name="create-local"></a> Criar o script de controle

A diferença entre o script de controle a seguir e aquele que você usou para enviar o script "Olá, Mundo!" é que você adiciona algumas linhas extras para definir o ambiente.

Crie um arquivo Python no diretório `tutorial` chamado `04-run-pytorch.py`:

```python
# 04-run-pytorch.py
from azureml.core import Workspace
from azureml.core import Experiment
from azureml.core import Environment
from azureml.core import ScriptRunConfig

if __name__ == "__main__":
    ws = Workspace.from_config()
    experiment = Experiment(workspace=ws, name='day1-experiment-train')
    config = ScriptRunConfig(source_directory='./src',
                             script='train.py',
                             compute_target='cpu-cluster')

    # set up pytorch environment
    env = Environment.from_conda_specification(
        name='pytorch-env',
        file_path='./.azureml/pytorch-env.yml'
    )
    config.run_config.environment = env

    run = experiment.submit(config)

    aml_url = run.get_portal_url()
    print(aml_url)
```    
    
### <a name="understand-the-code-changes"></a>Entender as alterações de código

:::row:::
   :::column span="":::
      `env = ...`
   :::column-end:::
   :::column span="2":::
      O Azure Machine Learning fornece o conceito de [ambiente](/python/api/azureml-core/azureml.core.environment.environment) para representar um ambiente reproduzível com controle de versão do Python para a execução de experimentos. É fácil criar um ambiente de um ambiente Conda ou pip local.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config.run_config.environment = env`
   :::column-end:::
   :::column span="2":::
      Adiciona o ambiente a [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig).
   :::column-end:::
:::row-end:::

> [!div class="nextstepaction"]
> [Criei o script de controle](?success=control-script#submit) [Encontrei um problema](https://www.research.net/r/7CTJQQN?issue=control-script)


## <a name="submit-the-run-to-azure-machine-learning"></a><a name="submit"></a> Enviar a execução para o Azure Machine Learning

Volte para o ambiente *tutorial* que tem o SDK do Azure Machine Learning para Python instalado. Como o código de treinamento não está em execução no seu computador, você não precisa ter o PyTorch instalado.  Porém, você precisa do `azureml-sdk`, que está no ambiente *tutorial*.

```bash
conda deactivate
conda activate tutorial
python 04-run-pytorch.py
```

>[!NOTE] 
> Na primeira vez que você executar esse script, o Azure Machine Learning criará uma imagem do Docker por meio do ambiente PyTorch. A execução inteira pode levar de 5 a 10 minutos para ser concluída. 
>
> Veja os logs de build do Docker no Azure Machine Learning Studio. Siga o link para o estúdio, selecione a guia **Saídas + logs** e escolha `20_image_build_log.txt`.
>
> Essa imagem será reutilizada em execuções futuras para tornar a execução muito mais rápida.

Depois que a imagem for criada, selecione `70_driver_log.txt` para ver a saída do script de treinamento.

```txt
Downloading https://www.cs.toronto.edu/~kriz/cifar-10-python.tar.gz to ./data/cifar-10-python.tar.gz
...
Files already downloaded and verified
epoch=1, batch= 2000: loss 2.19
epoch=1, batch= 4000: loss 1.82
epoch=1, batch= 6000: loss 1.66
epoch=1, batch= 8000: loss 1.58
epoch=1, batch=10000: loss 1.52
epoch=1, batch=12000: loss 1.47
epoch=2, batch= 2000: loss 1.39
epoch=2, batch= 4000: loss 1.38
epoch=2, batch= 6000: loss 1.37
epoch=2, batch= 8000: loss 1.33
epoch=2, batch=10000: loss 1.31
epoch=2, batch=12000: loss 1.27
Finished Training
```

> [!WARNING]
> Se um erro `Your total snapshot size exceeds the limit` for exibido, isso indicará que o diretório `data` estará localizado no valor `source_directory` usado em `ScriptRunConfig`.
>
> Mova `data` para fora de `src`.

Os ambientes podem ser registrados em um workspace com `env.register(ws)`. Em seguida, eles podem ser facilmente compartilhados, reutilizados e ter o controle de versão. Os ambientes facilitam a reprodução de resultados anteriores e a colaboração com a equipe.

O Azure Machine Learning também mantém uma coleção de ambientes organizados. Esses ambientes abrangem cenários comuns de machine learning e são apoiados por imagens do Docker armazenadas em cache. As imagens do Docker armazenadas em cache aceleram a primeira execução remota.

Em suma, o uso de ambientes registrados pode poupar tempo! Leia [Como usar ambientes](./how-to-use-environments.md) para obter mais informações.

> [!div class="nextstepaction"]
> [Enviei a execução](?success=test-w-environment#log) [Encontrei um problema](https://www.research.net/r/7CTJQQN?issue=test-w-environment)

## <a name="log-training-metrics"></a><a name="log"></a> Métricas de treinamento de log

Agora que você tem um treinamento de modelo no Azure Machine Learning, comece a controlar algumas métricas de desempenho.

O script de treinamento atual imprime as métricas para o terminal. O Azure Machine Learning fornece um mecanismo para registrar métricas em log com mais funcionalidade. Ao adicionar algumas linhas de código, você pode visualizar as métricas no estúdio e compará-las entre várias execuções.

### <a name="modify-trainpy-to-include-logging"></a>Modifique `train.py` para incluir o registro em log

Modifique o script `train.py` para incluir mais duas linhas de código:

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/code/pytorch-cifar10-train-with-logging/train.py":::


#### <a name="understand-the-additional-two-lines-of-code"></a>Entender as duas linhas de código adicionais

Em `train.py`, você acessa o objeto de execução _dentro_ do próprio script de treinamento usando o método `Run.get_context()` e o usa para registrar métricas:

```python
# in train.py
run = Run.get_context()

...

run.log('loss', loss)
```

As métricas no Azure Machine Learning são:

- Organizadas por experimento e execução, sendo, portanto, fácil controlar e comparar métricas.
- Equipado com uma interface do usuário para que você possa visualizar o desempenho de treinamento no estúdio.
- Projetado para ajuste de escala, assim, você aproveita esses benefícios mesmo quando executa centenas de experimentos.

> [!div class="nextstepaction"]
> [Modifiquei train.py ](?success=modify-train#log) [Encontrei um problema](https://www.research.net/r/7CTJQQN?issue=modify-train)

### <a name="update-the-conda-environment-file"></a>Atualizar o arquivo do ambiente Conda

O script `train.py` acabou de receber uma nova dependência de `azureml.core`. Atualize `pytorch-env.yml` para refletir essa alteração:

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/configuration/pytorch-aml-env.yml":::

> [!div class="nextstepaction"]
> [Atualizei o arquivo de ambiente](?success=update-environment#submit-again) [Encontrei um problema](https://www.research.net/r/7CTJQQN?issue=update-environment)

### <a name="submit-the-run-to-azure-machine-learning"></a><a name="submit-again"></a> Enviar a execução para o Azure Machine Learning
Envie este script mais uma vez:

```bash
python 04-run-pytorch.py
```

Desta vez, quando você visitar o estúdio, acesse a guia **Métricas**, na qual agora poderá ver atualizações dinâmicas sobre a perda de treinamento do modelo.

:::image type="content" source="media/tutorial-1st-experiment-sdk-train/logging-metrics.png" alt-text="Grafo de perda de treinamento na guia Métricas.":::

> [!div class="nextstepaction"]
> [Reenviei a execução](?success=resubmit-with-logging#next-steps) [Encontrei um problema](https://www.research.net/r/7CTJQQN?issue=resubmit-with-logging)

## <a name="next-steps"></a>Próximas etapas

Nesta sessão, você atualizou de um script "Olá, Mundo!" básico para um script de treinamento mais realista que exigia a execução de um ambiente específico do Python. Você viu como levar um ambiente Conda local para a nuvem com ambientes do Azure Machine Learning. Por fim, em algumas linhas de código, você viu como pode registrar métricas em log para o Azure Machine Learning.

Há outras maneiras de criar ambientes do Azure Machine Learning, incluindo [por meio de um arquivo requirements.txt do Pip](/python/api/azureml-core/azureml.core.environment.environment#from-pip-requirements-name--file-path-) ou [de um ambiente Conda local existente](/python/api/azureml-core/azureml.core.environment.environment#from-existing-conda-environment-name--conda-environment-name-).

Na próxima sessão, você verá como trabalhar com os dados no Azure Machine Learning carregando o conjunto de dados CIFAR10 para o Azure.

> [!div class="nextstepaction"]
> [Tutorial: Traga seus próprios dados](tutorial-1st-experiment-bring-data.md)

>[!NOTE] 
> Caso deseje concluir a série de tutoriais aqui e não progredir para a próxima etapa, lembre-se de [limpar seus recursos](tutorial-1st-experiment-bring-data.md#clean-up-resources).
