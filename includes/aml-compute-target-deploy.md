---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 05/30/2019
ms.openlocfilehash: 05082544830bad543d6743d016a91ef8565767f4
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/22/2019
ms.locfileid: "67331666"
---
| Destino de computação | Uso | Suporte de GPU | Suporte FPGA | DESCRIÇÃO |
| ----- | ----- | ----- | ----- | ----- |
| [Local&nbsp;web&nbsp;service](../articles/machine-learning/service/how-to-deploy-and-where.md#local) | Teste/depuração | Talvez | &nbsp; | BOM para testar e solucionar problemas limitado. Aceleração de hardware depende do uso de bibliotecas no sistema local.
| [AKS (Serviço de Kubernetes do Azure)](../articles/machine-learning/service/how-to-deploy-and-where.md#aks) | Inferência de tipos em tempo real |  [Sim](../articles/machine-learning/service/how-to-deploy-inferencing-gpus.md)  | [Sim](../articles/machine-learning/service/how-to-deploy-fpga-web-service.md)   |Ideal para implantações de produção em grande escala. Fornece o tempo de resposta rápida e o dimensionamento automático do serviço implantado. Não há suporte para dimensionamento automático do cluster por meio do SDK do Azure Machine Learning. Para alterar os nós no cluster AKS, use a interface do usuário para seu cluster AKS no portal do Azure. O AKS é a única opção disponível para a interface visual. |
| [ACI (Instâncias de Contêiner do Azure)](../articles/machine-learning/service/how-to-deploy-and-where.md#aci) | Teste ou desenvolvimento | &nbsp;  | &nbsp; | BOM para escala baixa, com base em CPU cargas de trabalho que exigem < 48 GB de RAM |
| [Computação do Azure Machine Learning](../articles/machine-learning/service/how-to-run-batch-predictions.md) | (Visualização) Lote&nbsp;inferência de tipos | Sim | &nbsp;  | Execute computação sem servidor de pontuação em lote. Dá suporte a VMs normais e de baixa prioridade. |
| [Azure IoT Edge](../articles/machine-learning/service/how-to-deploy-and-where.md#iotedge) | (Visualização) IoT&nbsp;módulo |  &nbsp; | &nbsp; | Implantar e fornecer modelos de ML em dispositivos IoT. |
| [Borda da caixa de dados do Azure](../articles/databox-online/data-box-edge-overview.md)   | via IoT Edge |  &nbsp; | Sim | Implantar e fornecer modelos de ML em dispositivos IoT. |
