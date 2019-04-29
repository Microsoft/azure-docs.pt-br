---
title: Crie e gerencie os espaços de trabalho
titleSuffix: Azure Machine Learning service
description: Saiba como criar, exibir e excluir espaços de trabalho do Azure Machine Learning no portal do Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: shipatel
author: shivp950
ms.date: 09/24/2018
ms.custom: seodec18
ms.openlocfilehash: e65f739a9641181381205c7255d0472325e8055c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60819600"
---
# <a name="create-and-manage-azure-machine-learning-service-workspaces"></a>Criar e gerenciar espaços de trabalho de serviço do Azure Machine Learning

Neste artigo, você vai criar, exibir e excluir [**Espaços de trabalho do Azure Machine Learning**](concept-azure-machine-learning-architecture.md#workspace) no portal do Azure para o [serviço do Azure Machine Learning](overview-what-is-azure-ml.md).  Também é possível criar e excluir workspaces [usando a CLI](reference-azure-machine-learning-cli.md) ou [com o código Python](https://aka.ms/aml-sdk).

## <a name="create-a-workspace"></a>Criar um workspace 

Para criar um workspace, você precisa de uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Serviço do Azure Machine Learning](https://aka.ms/AMLFree) hoje mesmo.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="view"></a>Exibir um espaço de trabalho

1. No canto superior esquerdo do portal, selecione **Todos os serviços**. 

1. No **todos os serviços** campo de filtro, digite **serviço de aprendizado de máquina**.  

1. Selecione **espaços de trabalho de serviço de Machine Learning**.

   ![pesquisa do espaço de trabalho de serviço de Azure Machine Learning](media/how-to-manage-workspace/all-services.png)

1. Examine a lista de workspaces encontrada. É possível filtrar com base na assinatura, grupos de recursos e locais.  

1. Selecione um espaço de trabalho para exibir suas propriedades.
   ![Propriedades do espaço de trabalho](media/how-to-manage-workspace/allservices_view_workspace_full.PNG)

## <a name="delete-a-workspace"></a>Excluir um workspace

Use o botão Excluir na parte superior do workspace que você deseja excluir.

  ![Botão Excluir](media/how-to-manage-workspace/delete-workspace.png)


## <a name="clean-up-resources"></a>Limpar recursos 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Próximas etapas

Siga o tutorial completo para aprender a usar um workspace para criar, treinar e implantar modelos com o serviço do Azure Machine Learning.

> [!div class="nextstepaction"]
> [Tutorial: Treinar modelos](tutorial-train-models-with-aml.md)
