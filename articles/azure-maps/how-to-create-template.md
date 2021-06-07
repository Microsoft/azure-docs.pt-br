---
title: Criar sua conta do Azure Maps usando um modelo do ARM | Mapas do Microsoft Azure
description: Saiba como criar uma conta do Azure Maps usando um modelo de Azure Resource Manager (ARM).
author: philmea
ms.author: philmea
ms.date: 10/20/2020
ms.topic: how-to
ms.service: azure-maps
ms.openlocfilehash: c715c0639e962f76f669515c1d2c826c8cf6cc9e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92525043"
---
# <a name="create-your-azure-maps-account-using-an-arm-template"></a>Criar sua conta do Azure Maps usando um modelo do ARM

Você pode criar sua conta do Azure Maps usando um modelo de Azure Resource Manager (ARM). Depois que você tiver uma conta, poderá implementar as APIs em seu site ou aplicativo móvel.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se seu ambiente atender aos pré-requisitos e você estiver familiarizado com o uso de modelos ARM, selecione o botão **Implantar no Azure**. O modelo será aberto no portal do Azure.

[![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-maps-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, será preciso:

* Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="review-the-template"></a>Examinar o modelo

O modelo usado neste início rápido é proveniente dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-maps-create/).

:::code language="json" source="~/quickstart-templates/101-maps-create/azuredeploy.json":::

O recurso de conta do Azure Maps é definido neste modelo:

* [**Microsoft. Maps/contas**](/azure/templates/microsoft.maps/accounts): criar uma conta do Azure Maps.

## <a name="deploy-the-template"></a>Implantar o modelo

1. Selecione a imagem a seguir para entrar no Azure e abrir um modelo. O modelo cria uma conta do Azure Maps.

    [![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-maps-create%2Fazuredeploy.json)

2. Selecione ou insira os seguintes valores.

    ![Portal de implantação de modelo ARM](./media/how-to-create-template/create-account-using-template-portal.png)

    A menos que seja especificado, use o valor padrão para criar sua conta do Azure Maps.

    * **Assinatura**: selecione uma assinatura do Azure.
    * **Grupo de recursos**: selecione **Criar novo**, insira um nome exclusivo para o grupo de recursos e, em seguida, clique em **OK**.
    * **Local**: selecione um local. Por exemplo, **oeste dos EUA 2**.
    * **Nome da conta**: Insira um nome para sua conta do Azure Maps, que deve ser globalmente exclusivo.
    * **Tipo de preço**: selecione o tipo de preço apropriado, o valor padrão para o modelo é S0.

3. Selecione **Examinar + criar**. 
4. Confirme suas configurações na página revisão e clique em **criar**. Depois que o Azure Maps tiver sido implantado com êxito, você receberá uma notificação:

    ![Notificação do portal de implantação do modelo ARM](./media/how-to-create-template/resource-manager-template-portal-deployment-notification.png)

O portal do Azure é usado para implantar seu modelo. Use também o Azure PowerShell, a CLI do Azure e a API REST. Para saber mais sobre outros métodos de implantação, confira [Implantar modelos](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Examinar os recursos implantados

Você pode usar o portal do Azure para verificar sua conta do Azure Maps e exibir suas chaves. Você também pode usar o script de CLI do Azure a seguir para listar as chaves de sua conta.

```azurecli-interactive
az maps account keys list --name MyMapsAccount --resource-group MyResourceGroup
```

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, exclua o grupo de recursos, que também exclui a conta do Azure Maps. Para excluir o grupo de recursos usando CLI do Azure:

```azurecli-interactive
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre mapas e Azure Resource Manager do Azure, continue nos artigos abaixo.

- Criar um aplicativo de [demonstração](quick-demo-map-app.md) do Azure Maps
- Saiba mais sobre [modelos do ARM](../azure-resource-manager/templates/overview.md)