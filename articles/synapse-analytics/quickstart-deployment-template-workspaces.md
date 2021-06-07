---
title: 'Início Rápido: Criar um workspace do Azure Synapse com um modelo do Azure Resource Manager'
description: Saiba como criar um workspace do Synapse usando um modelo do Azure Resource Manager (Modelo do ARM).
services: azure-resource-manager
author: julieMSFT
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: jrasnick
ms.date: 08/07/2020
ms.openlocfilehash: 7317b7f51c6d0f9d72e3aad81794a569276d2145
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566113"
---
# <a name="quickstart-create-an-azure-synapse-workspace-using-an-arm-template"></a>Início Rápido: Criar um workspace do Azure Synapse usando um modelo do ARM

Este modelo do ARM (Azure Resource Manager) criará um workspace do Azure Synapse com o Data Lake Storage subjacente. O workspace do Azure Synapse é um limite de colaboração protegível para processos de análise no Azure Synapse Analytics.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se seu ambiente atender aos pré-requisitos e você estiver familiarizado com o uso de modelos ARM, selecione o botão **Implantar no Azure**. O modelo será aberto no portal do Azure.

[![Implantar no Azure 1](../media/template-deployments/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FSynapse%2Fmaster%2FManage%2FDeployWorkspace%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="review-the-template"></a>Examinar o modelo

É possível examinar o modelo selecionando o link **Visualizar**. Depois selecione **Editar modelo**.

[![Visualizar](../media/template-deployments/template-visualize-button.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FSynapse%2Fmaster%2FManage%2FDeployWorkspace%2Fazuredeploy.json)

O modelo define dois recursos:

- Conta de armazenamento
- Workspace

## <a name="deploy-the-template"></a>Implantar o modelo

1. Selecione a imagem a seguir para entrar no Azure e abrir o modelo. Este modelo cria um workspace do Synapse.

   [![Implantar no Azure 2](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FSynapse%2Fmaster%2FManage%2FDeployWorkspace%2Fazuredeploy.json)

1. Insira ou atualize os seguintes valores:

   - **Assinatura**: Selecione uma assinatura do Azure.
   - **Grupo de recursos**: Selecione **Criar** e insira um nome exclusivo para o grupo de recursos e selecione **OK**. Um novo grupo de recursos facilitará a limpeza de recursos.
   - **Região**: Selecione uma região.  Por exemplo, **Centro dos EUA**.
   - **Name**: insira um nome para o workspace.
   - **Logon de Administrador**: insira o nome de usuário do administrador do SQL Server.
   - **Senha do administrador do SQL**: insira a senha de administrador para o SQL Server.
   - **Valores das Marcas**: Aceite o padrão.
   - **Examinar e criar**: selecione.
   - **Criar**: selecione.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o Azure Synapse Analytics e o Azure Resource Manager, continue com os artigos abaixo.

- Leia uma [Visão geral do Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)
- Saiba mais sobre o [Azure Resource Manager](../azure-resource-manager/management/overview.md)
- [Criar e implantar seu primeiro modelo do ARM](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
