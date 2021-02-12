---
title: "'Início Rápido: Criar um Azure Front Door Service usando um modelo do ARM (Azure Resource Manager)"
description: Este guia de início rápido descreve como criar um Azure Front Door Service usando o modelo do ARM (Azure Resource Manager).
services: front-door
documentationcenter: ''
author: duongau
editor: ''
ms.assetid: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2020
ms.author: duau
ms.custom: subject-armqs
ms.openlocfilehash: 223006193219afe4179f3161d5e60e6439207b22
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92896048"
---
# <a name="quickstart-create-a-front-door-using-an-arm-template"></a>'Início Rápido: Criar um Front Door usando um modelo do ARM

Este guia de início rápido descreve como usar um modelo do ARM (Azure Resource Manager) para criar um Front Door a fim de configurar a alta disponibilidade para um ponto de extremidade da Web.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se seu ambiente atender aos pré-requisitos e você estiver familiarizado com o uso de modelos ARM, selecione o botão **Implantar no Azure**. O modelo será aberto no portal do Azure.

[![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-front-door-create-basic%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

* Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
* IP ou FQDN de um site ou aplicativo Web.

## <a name="review-the-template"></a>Examinar o modelo

O modelo usado neste início rápido é proveniente dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-front-door-create-basic).

Neste guia de início rápido, você criará uma configuração do Front Door com um back-end e um caminho padrão correspondente a `/*`.

:::code language="json" source="~/quickstart-templates/101-front-door-create-basic/azuredeploy.json":::

Um recurso do Azure é definido no modelo:

* [**Microsoft.Network/frontDoors**](/azure/templates/microsoft.network/frontDoors)

## <a name="deploy-the-template"></a>Implantar o modelo

1. Clique em **Experimentar** no bloco de código a seguir para abrir o Azure Cloud Shell e então acompanhe as instruções para entrar no Azure.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-front-door-create-basic/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    Aguarde até ver o prompt do console.

1. Selecione **Copiar** no bloco de códigos anterior para copiar o script do PowerShell.

1. Clique com o botão direito do mouse no painel do console do shell e selecione **Colar**.

1. Insira os valores.

    A implantação de modelo cria um Front Door com um back-end. Neste exemplo, `microsoft.com` é usado como o **backendAddress**.

    O nome do grupo de recursos é o nome do projeto com o acréscimo de **rg**.

    > [!NOTE]
    > **frontDoorName** precisa ser um nome global exclusivo para que o modelo seja implantado com êxito. Se a implantação falhar, comece novamente com a Etapa 1.

    O modelo leva alguns minutos para ser implantado. Quando tiver concluído, a saída deverá ser semelhante a:

    :::image type="content" source="./media/quickstart-create-front-door-template/front-door-template-deployment-powershell-output.png" alt-text="Saída de implantação do PowerShell do modelo do Resource Manager no Front Door":::

O Azure PowerShell é usado para implantar o modelo. Além do Azure PowerShell, você também pode usar o portal do Azure, a CLI do Azure e a API REST. Para saber mais sobre outros métodos de implantação, confira [Implantar modelos](../azure-resource-manager/templates/deploy-portal.md).

## <a name="validate-the-deployment"></a>Validar a implantação

1. Entre no [portal do Azure](https://portal.azure.com).

1. Escolha **Grupos de recursos** no painel esquerdo.

1. Selecione o grupo de recursos criado na seção anterior. O nome do grupo de recursos padrão é o nome do projeto com o acréscimo de **rg**.

1. Selecione o Front Door que você criou anteriormente e clique no link **Host de front-end**. O link abrirá um navegador da Web redirecionando você ao seu FQDN de back-end que você definiu durante a criação.

    :::image type="content" source="./media/quickstart-create-front-door-template/front-door-overview.png" alt-text="Visão geral do portal do Front Door":::

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não precisar mais do serviço de Front Door, exclua o grupo de recursos. Isso remove o Front Door e todos os recursos relacionados.

Para excluir o grupo de recursos, chame o cmdlet `Remove-AzResourceGroup`:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você criou um Front Door.

Para saber como adicionar um domínio personalizado ao Front Door, avance para os tutoriais do Front Door.

> [!div class="nextstepaction"]
> [Tutoriais do Front Door](front-door-custom-domain.md)
