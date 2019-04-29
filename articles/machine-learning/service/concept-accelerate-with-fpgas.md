---
title: O que são FPGAs e o Project Brainwave?
titleSuffix: Azure Machine Learning service
description: Saiba como acelerar modelos e redes neurais profundas com FPGAs no Azure. Este artigo fornece uma introdução à FPGA (matrizes de porta programáveis no campo) e como o Serviço do Azure Machine Learning fornece IA (inteligência artificial) em tempo real quando você implanta seu modelo em uma FPGA do Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: tedway
author: tedway
ms.reviewer: jmartens
ms.date: 1/10/2019
ms.custom: seodec18
ms.openlocfilehash: 716de6d491be753c5c32c0a2774404140010f72c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60821235"
---
# <a name="what-are-fpgas-and-project-brainwave"></a>O que são FPGAs e o Project Brainwave?

Este artigo fornece uma introdução à FPGA (matrizes de porta programáveis no campo) e como o Serviço do Azure Machine Learning fornece IA (inteligência artificial) em tempo real quando você implanta seu modelo em uma FPGA do Azure.

As FPGAs contêm uma matriz de blocos lógicos programáveis e uma hierarquia de interconexões reconfiguráveis. As interconexões permitem que esses blocos sejam configurados de várias maneiras após a fabricação. Comparado a outros chips, as FPGAs fornecem uma combinação de programação e desempenho.

## <a name="fpgas-vs-cpu-gpu-and-asic"></a>FPGAs vs. CPU, GPU e ASIC

O diagrama e a tabela a seguir mostram uma comparação das FPGAs com outros processadores.

![Diagrama de comparação da FPGA do Serviço do Azure Machine Learning](./media/concept-accelerate-with-fpgas/azure-machine-learning-fpga-comparison.png)

|Processador||DESCRIÇÃO|
|---|:-------:|------|
|Circuitos integrados específicos do aplicativo|ASICs|Circuitos personalizados, como as Unidades de Processador (TPU) do TensorFlow da Google, fornecem a maior eficiência. Elas não podem ser reconfiguradas conforme suas necessidades mudam.|
|Matrizes de portas programáveis em campo|FPGAs|As FPGAs, como aquelas disponíveis no Azure, fornecem desempenho aproximado ao dos ASICs. Eles também são flexíveis e reconfiguráveis ao longo do tempo, a fim de implementar a nova lógica.|
|Unidades de processamento gráfico|GPUs|Uma opção popular para cálculos de IA. As GPUs oferecem funcionalidades de processamento paralelo, tornando-as mais rápidas na renderização de imagens comparado às CPUs.|
|Unidades de processamento central|CPUs|Processadores de uso geral cujo desempenho não é ideal para processamento de elementos gráficos e vídeo.|

## <a name="project-brainwave-on-azure"></a>Project Brainwave no Azure

O [Project Brainwave](https://www.microsoft.com/en-us/research/project/project-brainwave/) é uma arquitetura de hardware da Microsoft. Ela se baseia em dispositivos FPGA da Intel, que os cientistas de dados e desenvolvedores usam para acelerar os cálculos de IA em tempo real. Essa arquitetura habilitada para FPGA oferece desempenho, flexibilidade e escala e está disponível no Azure.

As FPGAs possibilitam alcançar baixa latência para solicitações de inferência em tempo real. As solicitações assíncronas (envio em lote) não são necessárias. O envio em lote pode causar latência, porque mais dados precisam ser processados. As implementações do Project Brainwave de unidades de processamento neural não exigem o envio em lote; portanto, a latência pode ser muitas vezes menor, comparado aos processadores CPU e GPU.

### <a name="reconfigurable-power"></a>Potência reconfigurável
Reconfigure as FPGAs para diferentes tipos de modelos de machine learning. Essa flexibilidade torna mais fácil acelerar os aplicativos baseados na precisão numérica mais ideal e o modelo de memória que está sendo usado. Como as FPGAs são reconfiguráveis, você pode ficar atualizado com os requisitos dos algoritmos de IA em rápida mudança.

### <a name="whats-supported-on-azure"></a>O que tem suporte no Azure
O Microsoft Azure é o maior investimento de nuvem do mundo em FPGAs. Você pode executar o Projeto Brainwave na infraestrutura de escala do Azure.

Hoje, o Projeto Brainwave dá suporte a:
+ Cenários de classificação e reconhecimento de imagens
+ Implantação do TensorFlow
+ DNNs: ResNet 50, ResNet 152, VGG-16, SSD-VGG e DenseNet-121
+ Hardware de FPGA da Intel 

Usando essa arquitetura de hardware habilitado para FPGA, as redes neurais treinadas são executadas rapidamente e com uma latência mais baixa. O Projeto Brainwave pode paralelizar DNNs (redes neurais profundas) pré-treinadas entre os FPGAs para expandir o serviço. As DNNs podem ser pré-treinadas, como um transformador de recursos profundo para transferência de aprendizado, ou ajustadas com pesos atualizados.

### <a name="scenarios-and-applications"></a>Cenários e aplicativos

O Projeto Brainwave é integrado ao Azure Machine Learning. A Microsoft usa FPGAs para avaliação de DNN, classificação da Pesquisa do Bing e aceleração de SDN (rede definida por software) para reduzir a latência durante a liberação de CPUs para outras tarefas.

Os cenários a seguir usam a FPGA na arquitetura do Project Brainwave:
+ [Sistema automatizado de inspeção ótica](https://blogs.microsoft.com/ai/build-2018-project-brainwave/)

+ [Mapeamento de cobertura de terra](https://blogs.technet.microsoft.com/machinelearning/2018/05/29/how-to-use-fpgas-for-deep-learning-inference-to-perform-land-cover-mapping-on-terabytes-of-aerial-images/)

## <a name="deploy-to-fpgas-on-azure"></a>Realizar a implantação em FPGAs no Azure

Para criar um serviço de reconhecimento de imagem no Azure, use DNNs compatíveis como um recurso para implantação em FPGAs do Azure:

1. Use o [SDK do Azure Machine Learning para Python](https://aka.ms/aml-sdk) para criar uma definição de serviço. Uma definição de serviço é um arquivo que descreve um pipeline de grafos (entrada, recursos e classificador) com base no TensorFlow. O comando de implantação compacta automaticamente a definição e os grafos em um arquivo zip e carrega o zip no Armazenamento de Blobs do Azure. A DNN já está implantada no Projeto Brainwave para ser executada no FPGA.

1. Registre o modelo usando o SDK com o arquivo zip no Armazenamento de Blobs do Azure.

1. Implante o serviço com o modelo registrado usando o SDK.

Para começar a implantar modelos DNN treinados em FPGAs na nuvem do Azure, confira [Implantar um modelo como um serviço Web em uma FPGA](how-to-deploy-fpga-web-service.md).


## <a name="next-steps"></a>Próximas etapas

Confira estes vídeos e blogs:

+ [Hardware da Hiperescala: ML em escala no Azure + FPGA: Build 2018 (vídeo)](https://www.youtube.com/watch?v=BMgQAHIx2eY)

+ [Por dentro da nuvem configurável baseada em FPGA da Microsoft (vídeo)](https://channel9.msdn.com/Events/Build/2017/B8063)

+ [Projeto Brainwave para IA em tempo real: home page do projeto](https://www.microsoft.com/research/project/project-brainwave/)
