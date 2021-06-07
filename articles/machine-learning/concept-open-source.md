---
title: Integração de aprendizado de máquina de software livre
titleSuffix: Azure Machine Learning
description: Saiba como usar estruturas de aprendizado de máquina de software livre do Python para treinar, implantar e gerenciar soluções de aprendizado de máquina de ponta a ponta em Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: luisquintanilla
ms.author: luquinta
ms.date: 01/14/2020
ms.openlocfilehash: 983e037376be48f497118b06cce8b23c430b1501
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98223067"
---
# <a name="open-source-integration-with-azure-machine-learning-projects"></a>Integração de código-fonte aberto com projetos Azure Machine Learning

Você pode treinar, implantar e gerenciar o processo de aprendizado de máquina de ponta a ponta em Azure Machine Learning usando bibliotecas e plataformas de aprendizado de máquina de software livre Python.  Use ferramentas de desenvolvimento, como Jupyter notebooks e Visual Studio Code, para aproveitar os seus modelos e scripts existentes no Azure Machine Learning.  

Neste artigo, saiba mais sobre essas bibliotecas e plataformas de software livre.

## <a name="train-open-source-machine-learning-models"></a>Treinar modelos de aprendizado de máquina de software livre

O processo de treinamento do Machine Learning envolve a aplicação de algoritmos aos seus dados a fim de obter uma tarefa ou resolver um problema. Dependendo do problema, você pode escolher algoritmos diferentes que melhor se ajustam à tarefa e aos seus dados. Para obter mais informações sobre as diferentes ramificações do aprendizado de máquina, consulte o [artigo aprendizado profundo versus](./concept-deep-learning-vs-machine-learning.md) o [algoritmo](algorithm-cheat-sheet.md)de aprendizado de máquina.

### <a name="preserve-data-privacy-using-differential-privacy"></a>Preserve a privacidade dos dados usando a privacidade diferencial

Para treinar um modelo de aprendizado de máquina, você precisa de dados. Às vezes, esses dados são confidenciais, e é importante garantir que os dados sejam seguros e privados. A privacidade diferencial é uma técnica de preservar a confidencialidade das informações em um conjunto de dados. Para saber mais, confira o artigo sobre como [preservar a privacidade de dados](concept-differential-privacy.md). 

Os kits de ajuda de privacidade diferencial de software livre, como o [SmartNoise](https://github.com/opendifferentialprivacy/smartnoise-core-python) , ajudam você [a preservar a privacidade dos dados](how-to-differential-privacy.md) em soluções Azure Machine Learning.

### <a name="classical-machine-learning-scikit-learn"></a>Aprendizado de máquina clássico: scikit-Learn

Para tarefas de treinamento que envolvem tarefas clássicas de algoritmos de aprendizado de máquina, como classificação, clustering e regressão, você pode usar algo como Scikit-learn. Para saber como treinar um modelo de classificação flor, consulte o [artigo como treinar com Scikit-Learn](how-to-train-scikit-learn.md).

### <a name="neural-networks-pytorch-tensorflow-keras"></a>Redes neurais: PyTorch, TensorFlow, Keras

Os algoritmos de aprendizado de máquina de software livre conhecidos como redes neurais, um subconjunto de aprendizado de máquina, são úteis para treinar modelos de aprendizado profundo no Azure Machine Learning.

As estruturas de aprendizado profundo de software livre e os guias de instruções incluem:

 *  [PyTorch](https://github.com/pytorch/pytorch): [treinar um modelo de classificação de imagem de aprendizado profundo usando o aprendizado de transferência](how-to-train-pytorch.md) 
 *  [TensorFlow](https://github.com/tensorflow/tensorflow): [reconhecer dígitos manuscritos usando TensorFlow](how-to-train-tensorflow.md)
 *  [Keras](https://github.com/keras-team/keras): [criar uma rede neural para analisar imagens usando o Keras](how-to-train-keras.md)

Treinar um modelo de aprendizado profundo do zero geralmente requer grandes quantidades de tempo, dados e recursos de computação. Você pode usar um atalho para o processo de treinamento usando o aprendizado de transferência. O aprendizado de transferência é uma técnica que aplica o conhecimento obtido da solução de um problema para um problema diferente, mas relacionado. Isso significa que você pode fazer com que um modelo existente o redefina. Consulte o [artigo aprendizado profundo vs. Machine Learning](concept-deep-learning-vs-machine-learning.md#what-is-transfer-learning) para saber mais sobre o aprendizado de transferência.

### <a name="reinforcement-learning-ray-rllib"></a>Aprendizado de reforço: Ray RLLib

O reforço Learning é uma técnica de inteligência artificial que treina os modelos usando ações, Estados e recompensas: os agentes de aprendizado do reforço aprendem a obter um conjunto de ações predefinidas que maximizam as recompensas especificadas com base no estado atual de seu ambiente. 

O projeto [Ray RLLib](https://github.com/ray-project/ray) tem um conjunto de recursos que permitem alta escalabilidade em todo o processo de treinamento. O processo iterativo é de tempo e de uso intensivo de recursos, pois os agentes de aprendizado de reforço tentam aprender a maneira ideal de obter uma tarefa.  Ray RLLib também dá suporte nativo a estruturas de aprendizado profundo como TensorFlow e PyTorch.  

Para saber como usar o Ray RLLib com Azure Machine Learning, consulte [como treinar um modelo de aprendizado do reforço](how-to-use-reinforcement-learning.md).

### <a name="monitor-model-performance-tensorboard"></a>Monitorar o desempenho do modelo: TensorBoard

Treinar um único ou vários modelos requer a visualização e a inspeção das métricas desejadas para garantir que o modelo seja executado conforme o esperado. Você pode [usar o TensorBoard no Azure Machine Learning para rastrear e visualizar as métricas de experimento](./how-to-monitor-tensorboard.md)

### <a name="frameworks-for-interpretable-and-fair-models"></a>Estruturas para modelos interpretáveis e justas

Os sistemas de Machine Learning são usados em diferentes áreas da sociedade, como o setor bancário, educação e saúde. Assim, é importante que esses sistemas sejam contados para as previsões e recomendações que eles fazem para evitar consequências indesejadas.

Estruturas de código-fonte aberto como [InterpretML](https://github.com/interpretml/interpret/) e Fairlearn ( https://github.com/fairlearn/fairlearn) trabalhe com Azure Machine Learning para criar modelos de aprendizado de máquina mais transparentes e justa.

Para obter mais informações sobre como criar modelos razoáveis e interpretáveis, consulte os seguintes artigos:

- [Interpretabilidade de modelo no Azure Machine Learning](how-to-machine-learning-interpretability.md)
- [Interpretar e explicar modelos de aprendizado de máquina](how-to-machine-learning-interpretability-aml.md)
- [Explicar os modelos de AutoML](how-to-machine-learning-interpretability-automl.md)
- [Mitigar a imparcialidade em modelos de aprendizado de máquina](concept-fairness-ml.md)
- [Usar Azure Machine Learning à imparcialidade do modelo de ativos](how-to-machine-learning-fairness-aml.md)

## <a name="model-deployment"></a>Implantação de modelo

Depois que os modelos são treinados e estão prontos para produção, você precisa escolher como implantá-los. O Azure Machine Learning fornece vários destinos de implantação. Para obter mais informações, consulte o [artigo onde e como implantar](./how-to-deploy-and-where.md).

### <a name="standardize-model-formats-with-onnx"></a>Padronizar formatos de modelo com ONNX

Após o treinamento, o conteúdo do modelo, como os parâmetros aprendidos, é serializado e salvo em um arquivo. Cada estrutura tem seu próprio formato de serialização. Ao trabalhar com diferentes estruturas e ferramentas, isso significa que você precisa implantar modelos de acordo com os requisitos da estrutura. Para padronizar esse processo, você pode usar o formato de troca de rede neural aberto (ONNX). ONNX é um formato de código aberto para modelos de inteligência artificial. O ONNX é compatível com a interoperabilidade entre estruturas. Isso significa que você pode treinar um modelo em uma das muitas estruturas de aprendizado de máquina populares, como PyTorch, convertê-la em formato ONNX e consumir o modelo de ONNX em uma estrutura diferente, como ML.NET.

Para obter mais informações sobre ONNX e como consumir modelos de ONNX, consulte os seguintes artigos:

- [Criar e acelerar modelos de ML com ONNX](concept-onnx.md)
- [Usar modelos ONNX em aplicativos .NET](how-to-use-automl-onnx-model-dotnet.md)

### <a name="package-and-deploy-models-as-containers"></a>Empacotar e implantar modelos como contêineres

As tecnologias de contêiner, como o Docker, são uma maneira de implantar modelos como serviços Web. Os contêineres fornecem uma forma independente de plataforma e recurso para criar e orquestrar ambientes de software reproduzíveis. Com essas tecnologias principais, você pode usar [ambientes pré-configurados](./how-to-use-environments.md), imagens de [contêiner pré-configuradas](./how-to-deploy-custom-docker-image.md) ou personalizadas para implantar seus modelos de aprendizado de máquina como [clusters kubernetes](./how-to-deploy-azure-kubernetes-service.md?tabs=python). Para fluxos de trabalho com uso intensivo de GPU, você pode usar ferramentas como o servidor de inferência NVIDIA Triton para [fazer previsões usando GPUs](how-to-deploy-with-triton.md?tabs=python).

### <a name="secure-deployments-with-homomorphic-encryption"></a>Implantações seguras com criptografia homomórficas

A proteção de implantações é uma parte importante do processo de implantação. Para [implantar os serviços inferências criptografados](how-to-homomorphic-encryption-seal.md), use a Biblioteca Python de software livre `encrypted-inference` . O `encrypted inferencing` pacote fornece associações baseadas no [selo da Microsoft](https://github.com/Microsoft/SEAL), uma biblioteca de criptografia homomórficas.

## <a name="machine-learning-operations-mlops"></a>Operações de Machine Learning (MLOps)

As operações de Machine Learning (MLOps), normalmente pensadas como DevOps para aprendizado de máquina, permitem que você crie fluxos de trabalho de aprendizado de máquina mais transparentes, resilientes e reproduzíveis. Consulte o [artigo o que é o MLOps](./concept-model-management-and-deployment.md) para saber mais sobre o MLOps. 

Usando práticas DevOpss como integração contínua (CI) e implantação contínua (CD), você pode automatizar o ciclo de vida de aprendizado de máquina de ponta a ponta e capturar dados de governança em relação a ele. Você pode definir o [pipeline de CI/CD do Machine Learning em ações do GitHub](./how-to-github-actions-machine-learning.md) para executar Azure Machine Learning tarefas de treinamento e implantação. 

A captura de dependências de software, métricas, metadados, dados e controle de versão de modelo são uma parte importante do processo MLOps para criar pipelines transparentes, reproduzíveis e auditáveis. Para essa tarefa, você pode [usar MLFlow em Azure Machine Learning](how-to-use-mlflow.md) , bem como ao [treinar modelos de aprendizado de máquina no Azure Databricks](./how-to-use-mlflow-azure-databricks.md). Você também pode [implantar modelos MLflow como um serviço Web do Azure](how-to-deploy-mlflow-models.md). 
