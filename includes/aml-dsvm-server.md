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
ms.openlocfilehash: 2504245a14eee5308e046148f8073cf34ffa8143
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "79485918"
---
1. [Criar um workspace do Azure Machine Learning](../articles/machine-learning/how-to-manage-workspace.md).

1. Clone [o repositório do GitHub](https://aka.ms/aml-notebooks).

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Adicione um arquivo de configuração do workspace ao diretório clonado usando um destes métodos:

    * No [portal do Azure](https://ms.portal.azure.com), selecione **Baixar config.json** na seção **Visão geral** do seu workspace. 

    ![Baixe o config.json](./media/aml-dsvm-server/download-config.png)

    * Criar um novo workspace usando o código no notebook [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb) em seu diretório clonado.

1. Inicie o servidor de notebook do seu diretório clonado.

    ```bash
    jupyter notebook
    ```