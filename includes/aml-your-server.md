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
ms.date: 03/05/2020
ms.openlocfilehash: 0d7622217d192a100fd809c32c9e85970cf61fd7
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102510881"
---
1. Use as instruções em [SDK do Azure Machine Learning](/python/api/overview/azure/ml/install) para instalar o SDK do Azure Machine Learning para Python

1. Criar um [Workspace do Azure Machine Learning](../articles/machine-learning/how-to-manage-workspace.md).

1. Escrever um [arquivo de configuração](../articles/machine-learning/how-to-configure-environment.md#workspace) (**aml_config/config.json**).

1. Clone [o repositório do GitHub](https://aka.ms/aml-notebooks).

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Inicie o servidor de notebook do seu diretório clonado.

    ```bash
    jupyter notebook
    ```