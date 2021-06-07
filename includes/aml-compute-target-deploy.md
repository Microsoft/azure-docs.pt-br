---
title: incluir arquivo
description: incluir arquivo
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 12/11/2020
ms.openlocfilehash: 09dd6e9a9d69797c2c33270d1620e861a052efe2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "97505113"
---
O destino de computação que você usa para hospedar seu modelo afetará o custo e a disponibilidade do ponto de extremidade implantado. Use esta tabela para escolher um destino de computação apropriado.

| Destino de computação | Usado para | Suporte de GPU | Suporte de FPGA | Descrição |
| ----- | ----- | ----- | ----- | ----- |
| [Serviço&nbsp;Web&nbsp;local](../articles/machine-learning/how-to-deploy-local-container-notebook-vm.md) | Teste/depuração | &nbsp; | &nbsp; | Use para teste e solução de problemas limitados. A aceleração de hardware depende do uso de bibliotecas no sistema local.
| [AKS (Serviço de Kubernetes do Azure)](../articles/machine-learning/how-to-deploy-azure-kubernetes-service.md) | Inferência em tempo real |  [Sim](../articles/machine-learning/how-to-deploy-inferencing-gpus.md) (implantação do serviço Web) | [Sim](../articles/machine-learning/how-to-deploy-fpga-web-service.md)   |Use para implantações de produção em grande escala. Fornece tempo de resposta rápido e dimensionamento automático do serviço implantado. O dimensionamento automático do cluster não tem suporte por meio do SDK do Azure Machine Learning. Para alterar os nós no cluster do AKS, use a interface do usuário do cluster do AKS no portal do Azure. <br/><br/> Com suporte no designer. |
| [Instâncias de Contêiner do Azure](../articles/machine-learning/how-to-deploy-azure-container-instance.md) | Teste ou desenvolvimento | &nbsp;  | &nbsp; | Use para cargas de trabalho baseadas em CPU de baixa escala que exigem menos de 48 GB de RAM. <br/><br/> Com suporte no designer. |
| [Clusters de cálculo do Azure Machine Learning](../articles/machine-learning/tutorial-pipeline-batch-scoring-classification.md) | Inferência&nbsp;de lote | [Sim](../articles/machine-learning/tutorial-pipeline-batch-scoring-classification.md) (pipeline de machine learning) | &nbsp;  | Executar a pontuação de lote em computação sem servidor. Compatível com VMs normais e de baixa prioridade. Não há suporte para inferência em tempo real.|

> [!NOTE]
> Embora os destinos de computação como local, Computação do Azure Machine Learning e clusters de cálculo do Azure Machine Learning sejam compatíveis com GPU para treinamento e experimentação, o uso de GPU para inferência _quando implantada como um serviço Web_ só é compatível com o AKS.
>
> O uso de uma GPU para inferência _quando a pontuação é feita com um pipeline de machine learning_ só é compatível com a Computação do Azure Machine Learning.
> 
> Ao escolher uma SKU de cluster, primeiro escale verticalmente e, em seguida, escale horizontalmente. Comece com um computador que tenha 150% da RAM de que seu modelo requer, perfile o resultado e localize um computador que tenha o desempenho de que você precisa. Depois de aprender isso, aumente o número de computadores para atender à sua necessidade de inferência simultânea.

> [!NOTE]
> * As instâncias de contêiner são adequadas somente para modelos pequenos com menos de 1 GB de tamanho.
> * Use clusters do AKS de nó único para desenvolvimento/teste de modelos maiores.