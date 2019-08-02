---
title: 'Destinos de computação: where treinar e implantar modelos'
titleSuffix: Azure Machine Learning service
description: Defina onde você deseja treinar ou implantar seu modelo com o serviço Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 07/10/2019
ms.openlocfilehash: a7944b284a9c1c0424af54874554d05d49ad4b20
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67806036"
---
#  <a name="what-are-compute-targets-in-azure-machine-learning-service"></a>Quais são os destinos de computação no serviço do Azure Machine Learning? 

Um **destino de computação** é um recurso/ambiente de computação designado em que você executa o script de treinamento ou o host de sua implantação do serviço. Esse local pode ser seu computador local ou um recurso de computação em nuvem. Usando destinos tornam mais fácil para você alterar posteriormente o seu ambiente de computação sem a necessidade de alterar seu código de computação.  

Em um ciclo de vida de desenvolvimento do modelo típico, você pode:
1. Comece a desenvolver e testar em uma pequena quantidade de dados. Neste estágio, é recomendável local ambiente (computador local ou VM baseada em nuvem) como seu destino de computação. 
2. Escalar verticalmente para dados maiores, ou distribuído usando uma destas opções de treinamento [destinos de computação de treinamento](#train).  
3. Depois que o modelo estiver pronto, implantá-lo em um ambiente ou o dispositivo IoT com uma destas opções de hospedagem na web [destinos de computação de implantação](#deploy).

Os recursos de computação, você pode usar para seus destinos de computação são anexados a um [espaço de trabalho](concept-workspace.md). Recursos que não seja o computador local são compartilhados por usuários do espaço de trabalho de computação.

## <a name="train"></a> Destinos de computação de treinamento

O serviço do Azure Machine Learning tem suporte variado em todos os recursos de computação diferentes.  Você também pode anexar seus próprios recursos de computação, embora o suporte para vários cenários podem variar.

[!INCLUDE [aml-compute-target-train](../../../includes/aml-compute-target-train.md)]

Saiba mais sobre [como configurar e usar um destino de computação para treinamento de modelo](how-to-set-up-training-targets.md).

## <a name="deploy"></a>Destinos de implantação

Os seguintes recursos de computação podem ser usados para hospedar sua implantação de modelo.

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]

Saiba mais [onde e como implantar o modelo para um destino de computação](how-to-deploy-and-where.md).

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Computação de Machine Learning do Azure (gerenciada)

Um recurso de computação gerenciada é criado e gerenciado pelo serviço do Azure Machine Learning. Essa computação é otimizada para cargas de trabalho de aprendizado de máquina. Computação do Azure Machine Learning é a única computação gerenciada a partir de 30 de maio de 2019. Recursos de computação gerenciados adicionais podem ser adicionados no futuro.

Você pode usar a computação do Azure Machine Learning para treinamento e Inferência de lote (versão prévia).  Com esse recurso de computação, você tem:

* Cluster único ou vários node
* É dimensionado automaticamente sempre que enviar uma execução 
* Gerenciamento de cluster automático e o agendamento de trabalho 
* Suporte para recursos de CPU e GPU

Você pode criar instâncias de computação do Azure Machine Learning no portal do Azure, com o SDK ou com a CLI. Quando criado, ele automaticamente faz parte do seu espaço de trabalho, ao contrário de outros tipos de destinos de computação.

## <a name="unmanaged-compute"></a>Computação não gerenciada

É um destino de computação não gerenciado *não* gerenciado pelo serviço de Azure Machine Learning. Você deve cria esse tipo de destino de computação fora do Azure Machine Learning e anexá-lo ao seu espaço de trabalho. Recursos de computação não gerenciado podem exigir etapas adicionais para você para manter ou melhorar o desempenho para cargas de trabalho de aprendizado de máquina.

## <a name="next-steps"></a>Próximas etapas

Saiba como:
* [Configurar um destino de computação para treinar seu modelo](how-to-set-up-training-targets.md)
* [Implantar o modelo para um destino de computação](how-to-deploy-and-where.md)