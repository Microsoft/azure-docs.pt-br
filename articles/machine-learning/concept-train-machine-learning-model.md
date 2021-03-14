---
title: Criar e treinar modelos
titleSuffix: Azure Machine Learning
description: Saiba como treinar modelos com Azure Machine Learning. Explore os diferentes métodos de treinamento e escolha o correto para seu projeto.
services: machine-learning
ms.service: machine-learning
author: Blackmist
ms.author: larryfr
ms.subservice: core
ms.topic: conceptual
ms.date: 05/13/2020
ms.custom: devx-track-python
ms.openlocfilehash: de3d9aa60322cc3e6e189f6f16c35d6f42c0cf61
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102500403"
---
# <a name="train-models-with-azure-machine-learning"></a>Treinar modelos com Azure Machine Learning

O Azure Machine Learning fornece várias maneiras de treinar seus modelos, desde soluções Code-First usando o SDK para soluções de baixo código, como o Machine Learning automatizado e o Visual Designer. Use a seguinte lista para determinar qual método de treinamento é o ideal para você:

+ [SDK do Azure Machine Learning para Python](#python-sdk): o SDK do Python fornece várias maneiras de treinar modelos, cada um com funcionalidades diferentes.

    | Método de treinamento | Descrição |
    | ----- | ----- |
    | [Configuração de execução](#run-configuration) | Uma **maneira comum de treinar modelos** é usar um script de treinamento e executar a configuração. A configuração de execução fornece as informações necessárias para configurar o ambiente de treinamento usado para treinar seu modelo. Você pode especificar seu script de treinamento, o destino de computação e o ambiente do Azure ML na sua configuração de execução e executar um trabalho de treinamento. |
    | [Machine learning automatizado](#automated-machine-learning) | O machine learning automatizado permite **treinar modelos sem uma ampla experiência em ciência de dados ou conhecimento de programação**. Para pessoas com conhecimento de ciência de dados e programação, ele fornece uma forma de economizar tempo e recursos automatizando a seleção de algoritmos e o ajuste de hiperparâmetros. Você não precisa se preocupar em definir uma configuração de execução ao usar o machine learning automatizado. |
    | [Pipeline de machine learning](#machine-learning-pipeline) | Pipelines não são um método de treinamento diferente, mas uma **forma de definir um fluxo de trabalho usando etapas modulares e reutilizáveis**, que podem incluir treinamento como parte do fluxo de trabalho. Os pipelines do Machine Learning dão suporte ao uso do Machine Learning automatizado e à execução da configuração para treinar modelos. Como os pipelines não estão voltados especificamente para o treinamento, os motivos para usar um pipeline são mais variados do que os outros métodos de treinamento. Geralmente, você pode usar um pipeline quando:<br>* Deseja **agendar processos autônomos** como preparação de dados ou trabalhos de treinamento de execução prolongada.<br>* Usa **várias etapas** coordenadas em recursos de computação heterogêneos e locais de armazenamento.<br>* Use o pipeline como um **modelo reutilizável** para cenários específicos, como retreinamento ou pontuação de lote.<br>* **Fontes de dados de acompanhamento e versão, entradas e saídas** para seu fluxo de trabalho.<br>* O fluxo de trabalho é **implementado por equipes diferentes que trabalham em etapas específicas independentemente**. As etapas podem ser unidas em um pipeline para implementar o fluxo de trabalho. |

+ [Azure Machine Learning SDK para r (visualização)](#r-sdk-preview): o SDK para r usa o pacote reticulate para associar ao SDK do Python Azure Machine Learning. Isso permite que você acesse os principais objetos e métodos implementados no SDK do Python de qualquer ambiente do R.

+ **Designer**: o designer de Azure Machine Learning fornece um ponto de entrada fácil para o aprendizado de máquina para a criação de conceitos de prova ou para usuários com pouca experiência de codificação. Ele permite treinar modelos usando uma interface do usuário baseada na Web do tipo “arrastar e soltar”. Você pode usar o código do Python como parte do design ou treinar modelos sem escrever nenhum código.

+ **CLI**: a CLI de machine learning fornece comandos para tarefas comuns com o Azure Machine Learning e é geralmente usada para **scripts e automatização de tarefas**. Por exemplo, quando você tiver criado um pipeline ou script de treinamento, poderá usar a CLI para iniciar a execução de treinamento em uma agenda ou quando os arquivos de dados usados para treinamento forem atualizados. Para modelos de treinamento, ela fornece comandos que enviam trabalhos de treinamento. Ela pode enviar trabalhos usando pipelines ou configurações de execução.

Cada um desses métodos de treinamento pode usar diferentes tipos de recursos de computação para treinamento. Coletivamente, esses recursos são chamados de [__destinos de computação__](concept-azure-machine-learning-architecture.md#compute-targets). Um destino de computação pode ser um computador local ou um recurso de nuvem, como Computação do Azure Machine Learning, Azure HDInsight ou uma máquina virtual remota.

## <a name="python-sdk"></a>SDK do Python

O SDK do Azure Machine Learning para Python permite criar e executar fluxos de trabalho de machine learning com o Azure Machine Learning. Você pode interagir com o serviço em uma sessão interativa do Python, Jupyter Notebooks, Visual Studio Code ou outro IDE.

* [O que é o SDK do Azure Machine Learning para Python](/python/api/overview/azure/ml/intro)
* [Instalar/atualizar o SDK](/python/api/overview/azure/ml/install)
* [Configurar um ambiente de desenvolvimento para Azure Machine Learning](how-to-configure-environment.md)

### <a name="run-configuration"></a>Configuração de execução

Um trabalho de treinamento genérico com Azure Machine Learning pode ser definido usando o [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig). A configuração de execução de script é usada, juntamente com seus scripts de treinamento para treinar um modelo em um destino de computação.

Você pode começar com uma configuração de execução para seu computador local e, em seguida, alternar para uma configuração de um destino de computação baseado em nuvem, conforme necessário. Ao alterar o destino de computação, você pode alterar a configuração de execução usada. Uma execução também registra informações sobre o trabalho de treinamento, como entradas, saídas e logs.

* [O que é uma configuração de execução?](concept-azure-machine-learning-architecture.md#run-configurations)
* [Tutorial: Treinar seu primeiro modelo de ML](tutorial-1st-experiment-sdk-train.md)
* [Exemplos: exemplos de Jupyter Notebook e Python de modelos de treinamento](https://github.com/Azure/azureml-examples)
* [Como: configurar uma execução de treinamento](how-to-set-up-training-targets.md)

### <a name="automated-machine-learning"></a>Automatizado de Machine Learning

Defina as iterações, configurações de hiperparâmetro, personalização e outras configurações. Durante o treinamento, o Azure Machine Learning tenta algoritmos e parâmetros diferentes em paralelo. O treinamento para quando atinge os critérios de saída que você definiu.

> [!TIP]
> Além do SDK do Python, você também pode usar o ML automatizado por meio do [Azure Machine Learning Studio](https://ml.azure.com).

* [O que é o machine learning automatizado?](concept-automated-ml.md)
* [Tutorial: Crie seu primeiro modelo de classificação com o machine learning automatizado](tutorial-first-experiment-automated-ml.md)
* [Tutorial: Usar machine learning automatizado para prever tarifas de táxi](tutorial-auto-train-models.md)
* [Exemplos: exemplos do Jupyter Notebook para machine learning automatizado](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)
* [Como: Configurar experimentos de ML automatizado no Python](how-to-configure-auto-train.md)
* [Como: Treinar automaticamente um modelo de previsão de série temporal](how-to-auto-train-forecast.md)
* [Como: Criar, explorar e implantar experimentos de machine learning automatizado com o estúdio do Azure Machine Learning](how-to-use-automated-ml-for-ml-models.md)

### <a name="machine-learning-pipeline"></a>Pipeline de machine learning

Os pipelines do Machine Learning podem usar os métodos de treinamento mencionados anteriormente. Pipelines se tratam mais de criar um fluxo de trabalho, assim abrangem mais do que apenas o treinamento de modelos. Em um pipeline, você pode treinar um modelo usando o aprendizado de máquina automatizado ou executar configurações.

* [O que são pipelines de ML no Azure Machine Learning?](concept-ml-pipelines.md)
* [Crie e execute pipelines de machine learning com o SDK do Azure Machine Learning](./how-to-create-machine-learning-pipelines.md)
* [Tutorial: Usar Pipelines do Azure Machine Learning para pontuação do lote](tutorial-pipeline-batch-scoring-classification.md)
* [Exemplos: exemplos do Jupyter Notebook para pipelines de machine learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines)
* [Exemplos: Pipeline com machine learning automatizado](https://aka.ms/pl-automl)

### <a name="understand-what-happens-when-you-submit-a-training-job"></a>Entender o que acontece quando você envia um trabalho de treinamento

O ciclo de vida de treinamento do Azure consiste em:

1. Compactando os arquivos na pasta do projeto, ignorando aqueles especificados em _. amlignore_ ou _. gitignore_
1. Escalar verticalmente seu cluster de cálculo 
1. Criando ou baixando o dockerfile para o nó de computação 
    1. O sistema calcula um hash de: 
        - A imagem base 
        - Etapas personalizadas do Docker (consulte [implantar um modelo usando uma imagem de base do Docker personalizada](./how-to-deploy-custom-docker-image.md))
        - A definição de Conda YAML (consulte [criar & usar ambientes de software em Azure Machine Learning](./how-to-use-environments.md))
    1. O sistema usa esse hash como a chave em uma pesquisa do ACR (registro de contêiner do Azure) do espaço de trabalho
    1. Se não for encontrado, ele procurará uma correspondência no ACR global
    1. Se não for encontrado, o sistema criará uma nova imagem (que será armazenada em cache e registrada com o ACR do espaço de trabalho)
1. Baixando seu arquivo de projeto compactado para armazenamento temporário no nó de computação
1. Descompactando o arquivo de projeto
1. O nó de computação em execução `python <entry script> <arguments>`
1. Salvando logs, arquivos de modelo e outros arquivos gravados na `./outputs` conta de armazenamento associada ao espaço de trabalho
1. Ampliando a computação, incluindo a remoção de armazenamento temporário 

Se você optar por treinar em seu computador local ("configurar como execução local"), não será necessário usar o Docker. Você pode usar o Docker localmente se escolher (consulte a seção [Configurar pipeline de ml](./how-to-debug-pipelines.md) para obter um exemplo).

## <a name="r-sdk-preview"></a>SDK do R (visualização)

O SDK do R permite que você use a linguagem R com o Azure Machine Learning. o SDK usa o pacote reticulate para se associar ao SDK do Python do Azure Machine Learning. Isso lhe dá acesso aos principais objetos e métodos implementados no SDK do Python de qualquer ambiente do R.

Para obter mais informações, consulte os seguintes artigos:

* [Referência do SDK do Azure Machine Learning para R](https://azure.github.io/azureml-sdk-for-r/index.html)

## <a name="azure-machine-learning-designer"></a>Azure Machine Learning Designer

O designer permite treinar modelos usando uma interface de arrastar e soltar em seu navegador da Web.

+ [O que é o designer?](concept-designer.md)
+ [Tutorial: prever o preço do automóvel](tutorial-designer-automobile-price-train-score.md)

## <a name="many-models-solution-accelerator"></a>Acelerador de solução de Muitos modelos

O [Acelerador de Solução de Muitos Modelos](https://aka.ms/many-models) (versão prévia) é baseado no Azure Machine Learning e permite treinar, operar e gerenciar centenas ou até milhares de modelos de machine learning.

Por exemplo, criar um modelo __para cada instância ou indivíduo__ nos cenários a seguir pode levar a resultados aprimorados:

* Como prever as vendas para cada repositório individual
* Manutenção preditiva para centenas de poços de petróleo
* Como personalizando uma experiência para usuários individuais.

Para obter mais informações, confira [Acelerador de solução de Muitos modelos](https://aka.ms/many-models) no GitHub.

## <a name="cli"></a>CLI

A CLI de machine learning é uma extensão para a CLI do Azure. Ela fornece comandos de CLI multiplataforma para trabalhar com o Azure Machine Learning. Normalmente, você usa a CLI para automatizar tarefas, como treinar um modelo de machine learning.

* [Usar a extensão da CLI do Azure Machine Learning](reference-azure-machine-learning-cli.md)
* [MLOps no Azure](https://github.com/microsoft/MLOps)

## <a name="vs-code"></a>Código VS

Você pode usar a extensão VS Code para executar e gerenciar seus trabalhos de treinamento. Consulte o [Guia de instruções de gerenciamento de recursos vs Code](how-to-manage-resources-vscode.md#experiments) para saber mais.

## <a name="next-steps"></a>Próximas etapas

Saiba como [Configurar uma execução de treinamento](how-to-set-up-training-targets.md).