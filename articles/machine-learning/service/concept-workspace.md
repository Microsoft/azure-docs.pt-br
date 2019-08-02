---
title: O que é um espaço de trabalho
titleSuffix: Azure Machine Learning service
description: O workspace é o recurso de nível superior para o Serviço do Azure Machine Learning. Ele mantém um histórico de todas as execuções de treinamento, incluindo logs, métricas, saída e um instantâneo dos seus scripts. Use essas informações para determinar quais executar treinamento produz o melhor modelo
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 05/21/2019
ms.openlocfilehash: 912c064fb5ca4e7ca311f60ed04a0122809cb0ff
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442366"
---
# <a name="what-is-an-azure-machine-learning-service-workspace"></a>O que é um espaço de trabalho do serviço de Azure Machine Learning?

O espaço de trabalho é o recurso de nível superior para o serviço de Azure Machine Learning, fornecendo um local centralizado para trabalhar com todos os artefatos que você cria quando você usa o serviço Azure Machine Learning.  O espaço de trabalho mantém um histórico de todas as execuções de treinamento, incluindo logs, métricas, saída e um instantâneo dos seus scripts. Essas informações são usadas para determinar quais execuções de treinamento produzem o melhor modelo.  

Quando você tiver um modelo que você deseja, você pode registrá-lo com o espaço de trabalho. Em seguida, em que você usar o modelo registrado e os scripts de pontuação para implantar instâncias de contêiner do Azure, serviço de Kubernetes do Azure, ou uma matriz de portões programáveis em campo (FPGA) como um ponto de extremidade HTTP baseado em REST. Você também pode implantar o modelo em um dispositivo Azure IoT Edge como um módulo.

## <a name="taxonomy"></a>Taxonomia 

O diagrama a seguir é uma taxonomia do workspace:

[![Taxonomia de espaço de trabalho](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png)](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png#lightbox)

O diagrama mostra os seguintes componentes de um espaço de trabalho:

+ Um espaço de trabalho pode conter [Notebook VMs](quickstart-run-cloud-notebook.md), configurados com o ambiente de Python necessário para executar o Azure Machine Learning de recursos de nuvem.
+ [Funções de usuário](how-to-assign-roles.md) permitem que você compartilhe o seu espaço de trabalho com outros usuários, equipes ou projetos.
+ [Destinos de computação](concept-azure-machine-learning-architecture.md#compute-targets) são usados para executar seus experimentos.
+ Quando você cria o espaço de trabalho [recursos associados](#resources) também são criados para você.
+ [Experimentos](concept-azure-machine-learning-architecture.md#experiments) execuções de treinamento, você pode usar para criar seus modelos.  Você pode criar e executar experimentos com
    + O [do Azure Machine Learning do SDK para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
    + O [automatizada experimentos de machine learning (versão prévia)](how-to-create-portal-experiments.md) seção no portal do Azure.
    + O [(visualização) de interface visual](ui-concept-visual-interface.md).
+ [Pipelines](concept-azure-machine-learning-architecture.md#ml-pipelines) são fluxos de trabalho reutilizáveis para treinamento e seu modelo de treinamento.
+ [Conjuntos de dados](concept-azure-machine-learning-architecture.md#datasets-and-datastores) auxílio no gerenciamento dos dados que você pode usar para a criação de pipeline e treinamento do modelo.
+ Depois que você tem um modelo que você deseja implantar, você pode criar um modelo registrado.
+ Use o modelo registrado e um script de pontuação para criar uma [implantação](concept-azure-machine-learning-architecture.md#deployment).

## <a name="tools-for-workspace-interaction"></a>Ferramentas para interação com o espaço de trabalho

Você pode interagir com seu espaço de trabalho das seguintes maneiras:

+ Na web:
    + O [Portal do Azure](https://portal.azure.com)
    + O [interface visual (visualização)](ui-concept-visual-interface.md)
+ No Python usando o Azure Machine Learning [SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
+ Na linha de comando usando o Azure Machine Learning [extensão da CLI](https://docs.microsoft.com/azure/machine-learning/service/reference-azure-machine-learning-cli)

## <a name="machine-learning-with-a-workspace"></a>Aprendizado de máquina com um espaço de trabalho

Tarefas de aprendizado de máquina de leitura e/ou gravar os artefatos em seu espaço de trabalho. 

+ Executar um experimento para treinar um modelo – gravações experimentar resultados da execução no espaço de trabalho.
+ Use automatizados ML para treinar um modelo - grava os resultados de treinamento para o espaço de trabalho.
+ Registre um modelo no espaço de trabalho.
+ Implantar um modelo – usa o modelo registrado para criar uma implantação.
+ Criar e executar fluxos de trabalho reutilizáveis.
+ Artefatos como experimentos, pipelines, modelos, as implantações de aprendizado de máquina de modo de exibição.
+ Modelos de rastreamento e monitoramento.

## <a name="workspace-management"></a>Gerenciamento de espaço de trabalho

Você também pode executar as seguintes tarefas de gerenciamento do espaço de trabalho:

| Tarefa de gerenciamento do espaço de trabalho   | Portal              | .        | CLI        |
|---------------------------|------------------|------------|------------|
| Criar um workspace        | **&check;**     | **&check;** | **&check;** |
| Criar e gerenciar recursos de computação    | **&check;**   | **&check;** |  **&check;**   |
| Gerenciar o acesso ao espaço de trabalho    | **&check;**   | |  **&check;**    |
| Criar uma VM de notebook | **&check;**   | |     |

Introdução ao serviço por [criando um espaço de trabalho](setup-create-workspace.md).

## <a name="resources"></a> Recursos associados

Quando você cria um novo workspace, ele automaticamente cria vários recursos do Azure que são usados pelo workspace:

+ [Registro de Contêiner do Azure](https://azure.microsoft.com/services/container-registry/): Registra os contêineres do docker que a serem usados durante o treinamento e ao implantar um modelo. Para minimizar os custos, é ACR **carregamento lento** até que as imagens de implantação são criadas.
+ [Conta de Armazenamento do Azure](https://azure.microsoft.com/services/storage/): Usado como o repositório de dados padrão para o workspace.  Blocos de anotações do Jupyter que são usados com suas VMs de bloco de anotações são armazenados aqui também.
+ [Azure Application Insights](https://azure.microsoft.com/services/application-insights/): Armazena informações de monitoramento sobre seus modelos.
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): Armazena segredos usados por destinos de computação e outras informações confidenciais necessárias para o workspace.

> [!NOTE]
> Além de criar novas versões, você também pode usar os serviços existentes do Azure.

## <a name="next-steps"></a>Próximas etapas

Para a Introdução ao Serviço do Azure Machine Learning, consulte:

+ [Visão geral de serviço do Azure Machine Learning](overview-what-is-azure-ml.md)
+ [Criar um workspace](setup-create-workspace.md)
+ [Gerenciar um workspace](how-to-manage-workspace.md)
+ [Tutorial: Treinar um modelo](tutorial-train-models-with-aml.md)
