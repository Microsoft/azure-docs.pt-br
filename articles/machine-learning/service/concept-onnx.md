---
title: Inferência de plataforma cruzada de alto desempenho com ONNX
titleSuffix: Azure Machine Learning service
description: Saiba mais sobre o ONNX e o tempo de execução do ONNX para acelerar modelos
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: prasantp
author: prasanthpul
ms.date: 04/24/2019
ms.custom: seodec18
ms.openlocfilehash: 10c47c5dbae0ffb204a78ff510e457cce9a25ff0
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68361092"
---
# <a name="onnx-and-azure-machine-learning-create-and-accelerate-ml-models"></a>ONNX e Azure Machine Learning: Criar e acelerar modelos de ML

Saiba como usar a [troca de rede neural aberta](https://onnx.ai) (ONNX) pode ajudar a otimizar a inferência do seu modelo de aprendizado de máquina. A inferência, ou a Pontuação do modelo, é a fase em que o modelo implantado é usado para previsão, mais comumente em dados de produção. 

É difícil otimizar os modelos de aprendizado de máquina para inferência (ou pontuação de modelo), pois você precisa ajustar o modelo e a biblioteca de inferência para aproveitar ao máximo os recursos de hardware. O problema se torna extremamente difícil se você quiser obter um desempenho ideal em diferentes tipos de plataformas (nuvem/borda, CPU/GPU, etc.), já que cada uma tem funcionalidades e características diferentes. A complexidade aumenta se você tiver modelos de uma variedade de estruturas que precisam ser executadas em uma variedade de plataformas. É muito demorado otimizar todas as diferentes combinações de hardware e estruturas. Uma solução para treinar uma vez em sua estrutura preferida e executar em qualquer lugar na nuvem ou na borda é necessária. É aí que entra o ONNX.

A Microsoft e uma comunidade de parceiros criaram ONNX como um padrão aberto para representar modelos de aprendizado de máquina. Modelos de [muitas estruturas](https://onnx.ai/supported-tools) , incluindo TensorFlow, PyTorch, SciKit-learn, Keras, encadeador, MXNET e Matlab, podem ser exportados ou convertidos para o formato ONNX padrão. Depois que os modelos estiverem no formato ONNX, eles poderão ser executados em uma variedade de plataformas e dispositivos.

O [tempo de execução do ONNX](https://github.com/Microsoft/onnxruntime) é um mecanismo de inferência de alto desempenho para a implantação de modelos do ONNX para produção. Ele é otimizado para a nuvem e o Edge e funciona no Linux, no Windows e no Mac. Escrito em C++, ele também tem C, Python e C# APIs. O tempo de execução do ONNX fornece suporte para toda a especificação ONNX-ML e também se integra com aceleradores em hardware diferente, como TensorRT em GPUs NVidia.

O tempo de execução do ONNX é usado em serviços de alta escala da Microsoft, como Bing, Office e serviços cognitivas. Os ganhos de desempenho dependem de vários fatores, mas esses serviços da Microsoft perceberam um __ganho de desempenho médio de 2x na CPU__. O tempo de execução do ONNX também é usado como parte do Windows ML em centenas de milhões de dispositivos. Você pode usar o tempo de execução com serviços de Azure Machine Learning. Usando o tempo de execução do ONNX, você pode se beneficiar das grandes otimizações de nível de produção, testes e melhorias contínuas.

[![Diagrama de fluxo ONNX mostrando treinamento, conversores e implantação](media/concept-onnx/onnx.png)](./media/concept-onnx/onnx.png#lightbox)

## <a name="get-onnx-models"></a>Obter modelos ONNX

Você pode obter modelos ONNX de várias maneiras:
+ Treinar um novo modelo ONNX no serviço Azure Machine Learning (consulte os exemplos na parte inferior deste artigo)
+ Converter o modelo existente de outro formato em ONNX (consulte os [tutoriais](https://github.com/onnx/tutorials)) 
+ Obtenha um modelo de ONNX pré-treinado do [modelo de ONNX Zoo](https://github.com/onnx/models) (consulte os exemplos na parte inferior deste artigo)
+ Gerar um modelo ONNX personalizado do [Serviço de Visão Personalizada do Azure](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/) 

Muitos modelos, incluindo classificação de imagem, detecção de objetos e processamento de texto, podem ser representados como modelos de ONNX. No entanto, alguns modelos talvez não possam ser convertidos com êxito. Se você encontrar essa situação, registre um problema no GitHub do respectivo conversor que você usou. Você pode continuar usando o modelo de formato existente até que o problema seja resolvido.

## <a name="deploy-onnx-models-in-azure"></a>Implantar modelos do ONNX no Azure

Com o serviço do Azure Machine Learning, é possível implantar, gerenciar e monitorar os modelos do ONNX. Usando o [fluxo de trabalho de implantação](concept-model-management-and-deployment.md) padrão e o ONNX Runtime, é possível criar um ponto de extremidade REST hospedado na nuvem. Consulte o exemplo de notebooks Jupyter no final deste artigo para experimentá-lo por conta própria. 

### <a name="install-and-use-onnx-runtime-with-python"></a>Instalar e usar o ONNX Runtime com Python

Os pacotes do Python para o tempo de execução do ONNX estão disponíveis em [PyPi.org](https://pypi.org) ([CPU](https://pypi.org/project/onnxruntime), [GPU](https://pypi.org/project/onnxruntime-gpu)). Leia [os requisitos do sistema](https://github.com/Microsoft/onnxruntime#system-requirements) antes da instalação. 

 Para instalar o ONNX Runtime para Python, use um dos seguintes comandos: 
```python   
pip install onnxruntime       # CPU build
pip install onnxruntime-gpu   # GPU build
```

Para chamar o ONNX Runtime no script do Python, use:    
```python
import onnxruntime
session = onnxruntime.InferenceSession("path to model")
```

A documentação que acompanha o modelo geralmente informa as entradas e saídas para usar o modelo. Também é possível usar uma ferramenta de visualização como o [Netron](https://github.com/lutzroeder/Netron) para exibir o modelo. O Tempo de Execução do ONNX também permite consultar os metadados do modelo, as entradas e as saídas:    
```python
session.get_modelmeta()
first_input_name = session.get_inputs()[0].name
first_output_name = session.get_outputs()[0].name
```

Para fazer inferências no modelo, use `run` e passe a lista de saídas que deseja receber (deixe em branco se quiser todas) e um mapa dos valores de entrada. O resultado é uma lista das saídas.  
```python
results = session.run(["output1", "output2"], {
                      "input1": indata1, "input2": indata2})
results = session.run([], {"input1": indata1, "input2": indata2})
```

Para obter a referência completa da API do Python, consulte os [documentos de referência do ONNX Runtime](https://aka.ms/onnxruntime-python).    

## <a name="examples"></a>Exemplos

Consulte [how-to-use-azureml/deployment/onnx](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx), por exemplo, notebooks que criam e implantam modelos ONNX.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="more-info"></a>Mais informações

Saiba mais sobre ONNX ou colabore com o projeto:
+ [Site do projeto ONNX](https://onnx.ai)
+ [Código do ONNX no GitHub](https://github.com/onnx/onnx)

Saiba mais sobre o ONNX Runtime ou contribua para o projeto:
+ [Repositório GitHub do ONNX Runtime](https://github.com/Microsoft/onnxruntime)


