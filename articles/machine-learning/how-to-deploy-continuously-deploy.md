---
title: Implantar Azure Machine Learning modelos continuamente
titleSuffix: Azure Machine Learning
description: Saiba como implantar modelos continuamente com a extensão Azure Machine Learning DevOps. Verificar automaticamente e implantar novas versões de modelo.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 08/03/2020
ms.topic: conceptual
ms.reviewer: larryfr
ms.custom: how-to, tracking-python, deploy
ms.openlocfilehash: 9de971639e22f9656ea75dc64993ac5881efbffb
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102609406"
---
# <a name="continuously-deploy-models"></a>Implantar modelos continuamente

Este artigo mostra como usar a implantação contínua no Azure DevOps para verificar automaticamente se há novas versões de modelos registrados e enviar por push esses novos modelos para produção.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você já tenha registrado um modelo em seu espaço de trabalho Azure Machine Learning. Consulte [este tutorial](how-to-train-scikit-learn.md) para obter um exemplo de treinamento e registro de um modelo scikit-learn.

## <a name="continuously-deploy-models"></a>Implantar modelos continuamente

Você pode implantar modelos continuamente usando a extensão Machine Learning para o [Azure DevOps](https://azure.microsoft.com/services/devops/). Você pode usar a extensão Machine Learning do Azure DevOps para disparar um pipeline de implantação quando um novo modelo de aprendizado de máquina for registrado em um espaço de trabalho do Azure Machine Learning.

1. Inscreva-se para [Azure pipelines](/azure/devops/pipelines/get-started/pipelines-sign-up), que torna a integração contínua e a entrega de seu aplicativo a qualquer plataforma ou nuvem possível. (Observe que Azure Pipelines não é o mesmo que [Machine Learning pipelines](concept-ml-pipelines.md#compare).)

1. [Crie um projeto DevOps do Azure.](/azure/devops/organizations/projects/create-project)

1. Instale a [extensão de Machine Learning para Azure pipelines](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml&targetId=6756afbe-7032-4a36-9cb6-2771710cadc2&utm_source=vstsproduct&utm_medium=ExtHubManageList).

1. Use conexões de serviço para configurar uma conexão de entidade de serviço com seu espaço de trabalho Azure Machine Learning para que você possa acessar seus artefatos. Vá para configurações do projeto, selecione **conexões de serviço** e, em seguida, selecione **Azure Resource Manager**:

    [![Selecionar Azure Resource Manager](media/how-to-deploy-and-where/view-service-connection.png)](media/how-to-deploy-and-where/view-service-connection-expanded.png)

1. Na lista **nível de escopo** , selecione **AzureMLWorkspace** e, em seguida, insira o restante dos valores:

    ![Selecionar AzureMLWorkspace](media/how-to-deploy-and-where/resource-manager-connection.png)

1. Para implantar continuamente o modelo de aprendizado de máquina usando Azure Pipelines, em pipelines, selecione **liberar**. Adicione um novo artefato e, em seguida, selecione o artefato do **modelo do AzureML** e a conexão de serviço que você criou anteriormente. Selecione o modelo e a versão para disparar uma implantação:

    [![Selecionar modelo do AzureML](media/how-to-deploy-and-where/enable-modeltrigger-artifact.png)](media/how-to-deploy-and-where/enable-modeltrigger-artifact-expanded.png)

1. Habilite o gatilho de modelo em seu artefato de modelo. Quando você ativa o gatilho, toda vez que a versão especificada (ou seja, a versão mais recente) desse modelo é registrada em seu espaço de trabalho, um pipeline de versão do Azure DevOps é disparado.

    [![Habilitar o gatilho de modelo](media/how-to-deploy-and-where/set-modeltrigger.png)](media/how-to-deploy-and-where/set-modeltrigger-expanded.png)

## <a name="next-steps"></a>Próximas etapas

Confira os projetos abaixo no GitHub para obter mais exemplos de implantação contínua para modelos de ML.

* [Microsoft/MLOps](https://github.com/Microsoft/MLOps)
* [Microsoft/MLOpsPython](https://github.com/microsoft/MLOpsPython)