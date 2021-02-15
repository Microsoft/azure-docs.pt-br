---
title: 'ONNX: alta inferência de plataforma cruzada, de alto desempenho'
titleSuffix: Azure Machine Learning
description: Saiba como usar a troca de rede neural aberta (ONNX) pode ajudar a otimizar a inferência do seu modelo de aprendizado de máquina.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: prasantp
author: prasanthpul
ms.date: 06/18/2020
ms.custom: seodec18
ms.openlocfilehash: 5a1ed6c43e4d3a96e0e19d7be50e1e16bcab780e
ms.sourcegitcommit: 3af12dc5b0b3833acb5d591d0d5a398c926919c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/11/2021
ms.locfileid: "98071231"
---
# <a name="onnx-and-azure-machine-learning-create-and-accelerate-ml-models"></a>ONNX e Azure Machine Learning: criar e acelerar modelos de ML

Saiba como usar a [troca de rede neural aberta](https://onnx.ai) (ONNX) pode ajudar a otimizar a inferência do seu modelo de aprendizado de máquina. A inferência, ou a Pontuação do modelo, é a fase em que o modelo implantado é usado para previsão, mais comumente em dados de produção. 

É difícil otimizar os modelos de aprendizado de máquina para inferência (ou pontuação de modelo), pois você precisa ajustar o modelo e a biblioteca de inferência para aproveitar ao máximo os recursos de hardware. O problema se torna extremamente difícil se você quiser obter um desempenho ideal em diferentes tipos de plataformas (nuvem/borda, CPU/GPU, etc.), já que cada uma tem funcionalidades e características diferentes. A complexidade aumenta se você tiver modelos de uma variedade de estruturas que precisam ser executadas em uma variedade de plataformas. É muito demorado otimizar todas as diferentes combinações de hardware e estruturas. Uma solução para treinar uma vez em sua estrutura preferida e executar em qualquer lugar na nuvem ou na borda é necessária. É aí que entra o ONNX.

A Microsoft e uma comunidade de parceiros criaram ONNX como um padrão aberto para representar modelos de aprendizado de máquina. Os modelos de [várias estruturas](https://onnx.ai/supported-tools) , incluindo TensorFlow, PyTorch, SciKit-learn, Keras, encadeador, MXNET, MATLAB e SparkML, podem ser exportados ou convertidos para o formato ONNX padrão. Depois que os modelos estiverem no formato ONNX, eles poderão ser executados em vários serviços e plataformas.

O [tempo de execução do ONNX](https://onnxruntime.ai) é um mecanismo de inferência de alto desempenho para a implantação de modelos do ONNX para produção. Ele é otimizado para a nuvem e o Edge e funciona no Linux, no Windows e no Mac. Escrito em C++, ele também tem APIs C, Python, C#, Java e JavaScript (Node.js) para uso em uma variedade de ambientes. O tempo de execução do ONNX dá suporte a modelos de ML DNN e tradicionais e integra-se com aceleradores em hardware diferente, como TensorRT em GPUs NVidia, OpenVINO em processadores Intel, DirectML no Windows e muito mais. Usando o tempo de execução do ONNX, você pode se beneficiar das grandes otimizações de nível de produção, testes e melhorias contínuas.

O tempo de execução do ONNX é usado em serviços da Microsoft de grande escala, como Bing, Office e serviços cognitivas do Azure. Os ganhos de desempenho dependem de vários fatores, mas esses serviços da Microsoft perceberam um __ganho de desempenho médio de 2x na CPU__. Além dos serviços Azure Machine Learnings, o tempo de execução ONNX também é executado em outros produtos que dão suporte a cargas de trabalho de Machine Learning, incluindo:
+ Windows: o tempo de execução é integrado ao Windows como parte do [windows Machine Learning](/windows/ai/windows-ml/) e é executado em centenas de milhões de dispositivos. 
+ Família de produtos SQL do Azure: executar Pontuação nativa nos dados no [Azure SQL Edge](../azure-sql-edge/onnx-overview.md) e no [Azure SQL instância gerenciada](../azure-sql/managed-instance/machine-learning-services-overview.md).
+ ML.NET: [executar modelos de ONNX no ml.net](/dotnet/machine-learning/tutorials/object-detection-onnx).


[![Diagrama de fluxo ONNX mostrando treinamento, conversores e implantação](./media/concept-onnx/onnx.png)](././media/concept-onnx/onnx.png#lightbox)

## <a name="get-onnx-models"></a>Obter modelos ONNX

Você pode obter modelos ONNX de várias maneiras:
+ Treinar um novo modelo de ONNX no Azure Machine Learning (consulte os exemplos na parte inferior deste artigo) ou usando [recursos de Machine Learning automatizados](concept-automated-ml.md#automl--onnx)
+ Converter o modelo existente de outro formato em ONNX (consulte os [tutoriais](https://github.com/onnx/tutorials)) 
+ Obter um modelo ONNX pré-treinado do [Modelo Zoo do ONNX](https://github.com/onnx/models)
+ Gerar um modelo ONNX personalizado do [Serviço de Visão Personalizada do Azure](../cognitive-services/custom-vision-service/index.yml) 

Muitos modelos, incluindo classificação de imagem, detecção de objetos e processamento de texto, podem ser representados como modelos de ONNX. Se você encontrar um problema com um modelo que não pode ser convertido com êxito, registre um problema no GitHub do respectivo conversor que você usou. Você pode continuar usando o modelo de formato existente até que o problema seja resolvido.

## <a name="deploy-onnx-models-in-azure"></a>Implantar modelos do ONNX no Azure

Com o Azure Machine Learning, você pode implantar, gerenciar e monitorar seus modelos do ONNX. Usando o [fluxo de trabalho de implantação](concept-model-management-and-deployment.md) padrão e o ONNX Runtime, é possível criar um ponto de extremidade REST hospedado na nuvem. Consulte o exemplo de notebooks Jupyter no final deste artigo para experimentá-lo por conta própria. 

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

A documentação que acompanha o modelo geralmente informa as entradas e saídas para usar o modelo. Também é possível usar uma ferramenta de visualização como o [Netron](https://github.com/lutzroeder/Netron) para exibir o modelo. O Runtime do ONNX também permite consultar os metadados do modelo, as entradas e as saídas:    
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
Consulte [como usar-azureml/Deployment/onnx](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx) para ver os notebooks Python de exemplo que criam e implantam modelos de onnx.

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

Exemplos de uso em outros idiomas podem ser encontrados no [GitHub do ONNX Runtime](https://github.com/microsoft/onnxruntime/tree/master/samples).

## <a name="more-info"></a>Mais informações

Saiba mais sobre o **ONNX** ou o Contribute para o projeto:
+ [Site do projeto ONNX](https://onnx.ai)
+ [Código do ONNX no GitHub](https://github.com/onnx/onnx)

Saiba mais sobre o **tempo de execução do ONNX** ou contribua para o projeto:
+ [Site do projeto do ONNX Runtime](https://onnxruntime.ai)
+ [Repositório GitHub do ONNX Runtime](https://github.com/Microsoft/onnxruntime)