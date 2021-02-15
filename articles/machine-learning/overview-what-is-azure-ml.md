---
title: O que é o Azure Machine Learning
description: O Azure Machine Learning é uma solução integrada de ciência de dados para que os cientistas de dados e o MLops modelem e implantem aplicativos ML em escala de nuvem.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
ms.author: larryfr
author: BlackMist
ms.date: 11/04/2020
ms.custom: devx-track-python
ms.openlocfilehash: a9d471e81c348bfaa92c5d15774517f0d9e0e43f
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98683350"
---
# <a name="what-is-azure-machine-learning"></a>O que é o Azure Machine Learning?

Neste artigo, você aprenderá sobre o Azure Machine Learning, um ambiente baseado em nuvem que pode ser usado para treinar, implantar, automatizar, gerenciar e rastrear modelos de ML. 

O Azure Machine Learning pode ser usado para qualquer tipo de aprendizado de máquina, desde ML clássico até aprendizado profundo, aprendizado supervisionado e não supervisionado. Se você preferir escrever o código Python ou R com o SDK ou trabalhar com as opções sem código/com pouco código no [estúdio](#build-ml-models-in-the-studio), poderá criar, treinar e acompanhar modelos de machine learning e aprendizado profundo em um Workspace do Azure Machine Learning. 

Inicie o treinamento do seu computador local e escale horizontalmente para a nuvem. 

O serviço também interopera com ferramentas populares de software livre de aprendizado profundo e reforço como o PyTorch, TensorFlow, scikit-learn e Ray RLlib. 

> [!Tip]
> **Avaliação gratuita!**  Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente hoje mesmo a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree). Você recebe créditos para gastar em serviços do Azure. Depois que eles forem utilizados, você poderá manter a conta e usar os [serviços gratuitos do Azure](https://azure.microsoft.com/free/). Seu cartão de crédito nunca será cobrado, a menos que você altere explicitamente suas configurações, solicitando esse tipo de cobrança.


## <a name="what-is-machine-learning"></a>O que aprendizado da máquina?

O Machine Learning é uma técnica da ciência de dados que permite que os computadores usem os dados existentes para prever tendências, resultados e comportamentos futuros. Usando o aprendizado de máquina, os computadores aprendem sem serem explicitamente programados.

As estimativas ou previsões de aprendizado de máquina podem tornar aplicativos e dispositivos mais inteligentes. Por exemplo, quando você faz compras online, o aprendizado de máquina ajuda a recomendar outros produtos que podem lhe agradar com base no que você já comprou. Ou, ao passar seu cartão de crédito, o aprendizado de máquina compara a transação com um banco de dados de transações e ajuda a detectar fraudes. E, quando o aspirador de pó robô aspira uma sala, o aprendizado de máquina ajuda a decidir se o trabalho está concluído.

## <a name="machine-learning-tools-to-fit-each-task"></a>Ferramentas de Machine Learning ideais para cada tarefa 

O Azure Machine Learning fornece todas as ferramentas que desenvolvedores de ferramentas e cientistas de dados precisam para seus fluxos de trabalho de aprendizado de máquina, incluindo:
+ O [Azure Machine Learning Designer](tutorial-designer-automobile-price-train-score.md): arraste e solte módulos para compilar seus experimentos e, depois, implante pipelines.

+ Notebooks Jupyter: use nossos [notebooks de exemplo](https://github.com/Azure/MachineLearningNotebooks) ou crie seus próprios notebooks para aproveitar nossas amostras de <a href="/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">SDK para Python</a> em seu aprendizado de máquina. 

+ Scripts R ou notebooks nos quais você usa o <a href="https://azure.github.io/azureml-sdk-for-r/reference/index.html" target="_blank">SDK para R</a> para escrever seu próprio código ou usar os módulos do R no designer.

+ O [Acelerador de Solução de Muitos Modelos](https://aka.ms/many-models) (versão prévia) é baseado no Azure Machine Learning e permite treinar, operar e gerenciar centenas ou até milhares de modelos de machine learning.

+ [Extensão de machine learning para usuários do Visual Studio Code](tutorial-setup-vscode-extension.md)

+ [CLI de aprendizado de máquina](reference-azure-machine-learning-cli.md)

+ Estruturas open-source como PyTorch, TensorFlow e scikit-learn, entre muitas outras

+ [Aprendizado de reforço](how-to-use-reinforcement-learning.md) com o Ray Llib

Você pode até mesmo usar [MLflow para controlar as métricas e implantar modelos](how-to-use-mlflow.md) ou Kubeflow para [compilar pipelines de fluxo de trabalho de ponta a ponta](https://www.kubeflow.org/docs/azure/).

## <a name="build-ml-models-in-python-or-r"></a>Criar modelos de ML em Python ou R

Inicie o treinamento em seu computador local usando o <a href="/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">SDK de Python</a> ou o <a href="https://azure.github.io/azureml-sdk-for-r/reference/index.html" target="_blank">SDK de R</a> do Azure Machine Learning. Em seguida, você pode escalar horizontalmente para a nuvem. 

Com muitos [destinos de computação](how-to-create-attach-compute-studio.md) disponíveis, como a Computação do Azure Machine Learning e o [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks), além de [serviços avançados de ajuste de hiperparâmetro](how-to-tune-hyperparameters.md), você pode criar modelos melhores com mais rapidez usando o poder da nuvem.

Também é possível [automatizar o treinamento e o ajuste do modelo](tutorial-auto-train-models.md) usando o SDK.

## <a name="build-ml-models-in-the-studio"></a>Criar modelos de ML no estúdio

O [estúdio do Azure Machine Learning](https://studio.azureml.net) é um portal da Web no Azure Machine Learning para opções sem código e com pouco código para treinamento de modelo, implantação e gerenciamento de ativos. O estúdio integra-se ao SDK do Azure Machine Learning para proporcionar uma experiência perfeita. Para obter mais informações, confira [O que é o estúdio do Azure Machine Learning](overview-what-is-machine-learning-studio.md).

+ **Designer do Azure Machine Learning**

  Use [o designer](concept-designer.md) para treinar e implantar modelos de machine learning sem nenhuma codificação. Experimente o [tutorial do designer](tutorial-designer-automobile-price-train-score.md) para começar a usá-lo. 

  ![GIF animado da interface do tipo "arrastar e soltar" do designer do Azure Machine Learning](media/concept-designer/designer-drag-and-drop.gif)

+ **Acompanhar experimentos**

  Saiba como [acompanhar e visualizar experimentos de ciência de dados](tutorial-first-experiment-automated-ml.md) no estúdio. 

    ![Detalhes da execução no estúdio do Azure Machine Learning](media/how-to-track-experiments/experimentation-tab.gif)


+ **E muito mais...**

  Acesse o estúdio do Azure Machine Learning em [ml.azure.com](https://studio.azureml.net). 


## <a name="mlops-deploy--lifecycle-management"></a>MLOps: Gerenciamento de implantação e ciclo de vida
Quando você tem o modelo certo, é possível usá-lo facilmente em um serviço Web, em um dispositivo IoT ou do Power BI. Para obter mais informações, confira o artigo [Como e onde implantar](how-to-deploy-and-where.md).

Em seguida, você pode gerenciar seus modelos implantados usando o [SDK do Azure Machine Learning para Python](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py), o [Azure Machine Learning Studio](https://ml.azure.com) ou a [CLI de aprendizado de máquina](reference-azure-machine-learning-cli.md).

Esses modelos podem ser consumidos e retornar previsões em [tempo real](how-to-consume-web-service.md) ou [assincronamente](./tutorial-pipeline-batch-scoring-classification.md) em grandes quantidades de dados.

E com [pipelines de aprendizado de máquina](concept-ml-pipelines.md) avançados, é possível colaborar em cada etapa desde a preparação de dados, do treinamento do modelo e da avaliação, por meio da implantação. Os pipelines permitem:

* Automatizar o processo de aprendizado de máquina de ponta a ponta na nuvem
* Reutilizar os componentes e apenas executar as etapas novamente quando necessário
* Usar diferentes recursos de computação em cada etapa
* Executar tarefas de pontuação em lote

Se você quiser usar scripts para automatizar seu fluxo de trabalho de aprendizado de máquina, a [interface de linha de comando de aprendizado de máquina](reference-azure-machine-learning-cli.md) fornecerá ferramentas de linha de comando que executam tarefas comuns, como enviar uma execução de treinamento ou implantar um modelo.

Para começar a usar o Azure Machine Learning, confira [Próximas etapas](#next-steps).

## <a name="integration-with-other-services"></a>Integração com outros serviços

O Azure Machine Learning trabalha com outros serviços na plataforma do Azure e também se integra a ferramentas de software livre, como Git e MLFlow.

+ Destinos de computação, como __Serviço de Kubernetes do Azure__, __Instâncias de Contêiner do Azure__, __Azure Databricks__, __Azure Data Lake Analytics__ e __Azure HDInsight__. Para obter mais informações sobre destinos de computação, confira [O que são destinos de computação?](concept-compute-target.md).
+ __Grade de Eventos do Azure__. Para obter mais informações, confira [Consumir eventos do Azure Machine Learning](./how-to-use-event-grid.md).
+ __Azure Monitor__. Para obter mais informações, confira [Monitoramento do Azure Machine Learning](monitor-azure-machine-learning.md).
+ Os armazenamentos de dados, como __contas de Armazenamento do Azure__, __Azure Data Lake Storage__, __Banco de Dados SQL do Azure__, __Banco de Dados do Azure para PostgreSQL__ e __Azure Open Datasets__. Para obter mais informações, confira [Acessar dados nos serviços de armazenamento do Azure](how-to-access-data.md) e [Criar conjuntos de dados com os Azure Open Datasets](how-to-create-register-datasets.md).
+ __Redes Virtuais do Azure__. Para obter mais informações, confira [Visão geral de isolamento de rede virtual e privacidade](how-to-network-security-overview.md).
+ __Azure Pipelines__. Para obter mais informações, confira [Treinar e implantar modelos de machine learning](/azure/devops/pipelines/targets/azure-machine-learning).
+ __Logs do repositório Git__. Para obter mais informações, confira [Integração do Git](concept-train-model-git-integration.md).
+ __MLFlow__. Para obter mais informações, confira [MLFlow para acompanhar métricas](how-to-use-mlflow.md) e [Implantar modelos como um serviço Web](how-to-deploy-mlflow-models.md) 
+ __Kubeflow__. Para obter mais informações, confira [Criar pipelines de fluxo de trabalho de ponta a ponta](https://www.kubeflow.org/docs/azure/).

### <a name="secure-communications"></a>Comunicações seguras

Sua conta de Armazenamento do Azure, os destinos de computação e outros recursos podem ser usados com segurança dentro de uma rede virtual para treinar modelos e realizar a inferência. Para obter mais informações, confira [Visão geral de isolamento de rede virtual e privacidade](how-to-network-security-overview.md).

## <a name="next-steps"></a>Próximas etapas

- Crie seu primeiro experimento com seu método preferencial:
- + [Comece a usar seu próprio ambiente de desenvolvimento](tutorial-1st-experiment-sdk-setup-local.md)
  + [Use blocos de anotações do Jupyter em uma instância de computação para treinar e implantar modelos de ML](tutorial-1st-experiment-sdk-setup.md)
  + [Usar machine learning automatizado para treinar e implantar modelos de Machine Learning](tutorial-first-experiment-automated-ml.md) 
  + [Usar os recursos de arrastar e soltar do designer para treinar e implantar](tutorial-designer-automobile-price-train-score.md) 
  + [Usar a interface de linha de comando de aprendizado de máquina para treinar e implantar um modelo](tutorial-train-deploy-model-cli.md)

- Saiba mais sobre [pipelines de aprendizado de máquina](concept-ml-pipelines.md) para compilar, otimizar e gerenciar cenários de aprendizado de máquina.

- Leia o artigo detalhado [Arquitetura e conceitos do Azure Machine Learning](concept-azure-machine-learning-architecture.md).
