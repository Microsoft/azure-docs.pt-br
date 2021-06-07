---
title: Criar um laboratório usando o Azure DevTest Labs e um modelo do Azure Resource Manager
description: Neste guia de início rápido, você criará um laboratório no Azure DevTest Labs usando um modelo do ARM (Azure Resource Manager). Um administrador de laboratório configura um laboratório, cria VMs no laboratório e configura políticas.
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 06/26/2020
ms.openlocfilehash: 2b825b4d4485f401199556b6faaef0017f583cc1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91461182"
---
# <a name="quickstart-set-up-a-lab-by-using-azure-devtest-labs-arm-template"></a>Início Rápido: Configurar um laboratório usando o Azure DevTest Labs e um modelo do ARM
Neste início rápido, crie um laboratório com uma VM do Datacenter do Windows Server 2019 usando um modelo ARM (modelo do Azure Resource Manager). 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Neste guia de início rápido, você realizará as seguintes ações:

> [!div class="checklist"]
> * Examinar o modelo 
> * Implantar o modelo
> * Verificar o modelo
> * Recursos de limpeza

Se seu ambiente atender aos pré-requisitos e você estiver familiarizado com o uso de modelos ARM, selecione o botão **Implantar no Azure**. O modelo será aberto no portal do Azure.

[![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-dtl-create-lab-windows-vm%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="review-the-template"></a>Examinar o modelo

O modelo usado neste início rápido é proveniente dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-dtl-create-lab-windows-vm/).

:::code language="json" source="~/quickstart-templates/101-dtl-create-lab-windows-vm/azuredeploy.json":::

Os recursos definidos no modelo incluem:

- [**Microsoft.DevTestLab/labs**](/azure/templates/microsoft.devtestlab/labs)
- [**Microsoft.DevTestLab labs/virtualnetworks**](/azure/templates/microsoft.devtestlab/labs/virtualnetworks)
- [**Microsoft.DevTestLab labs/virtualmachines**](/azure/templates/microsoft.devtestlab/labs/virtualmachines)

Para encontrar mais exemplos de modelos, consulte [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Devtestlab).

## <a name="deploy-the-template"></a>Implantar o modelo
Para executar a implantação automaticamente, clique no botão a seguir. 

[![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-dtl-create-lab-windows-vm%2Fazuredeploy.json)

1. Crie um **grupo de recursos** para que seja fácil apagá-lo mais tarde.
1. Selecione uma **localização** para o grupo de recursos. 
1. Insira um **nome para o laboratório**. 
1. Insira um **nome para a VM**. 
1. Insira um **nome de usuário** que possa acessar a VM. 
1. Insira a **senha** do usuário. 
1. Selecione **Concordo com os termos e as condições declarados acima**. 
1. Em seguida, selecione **Comprar**.

    :::image type="content" source="./media/create-lab-windows-vm-template/deploy-template-page.png" alt-text="Página Implantar Modelo":::

## <a name="validate-the-deployment"></a>Validar a implantação
1. Selecione **Notificações** na parte superior para ver o status da implantação e clique no link **Implantação em andamento**.

    :::image type="content" source="./media/create-lab-windows-vm-template/deployment-notification.png" alt-text="Notificação de implantação":::
2. Na página **Implantação – Visão geral**, aguarde até que a implantação seja concluída. Esta operação (especialmente a criação de uma VM) leva algum tempo para ser concluída. Em seguida, selecione **Ir para o grupo de recursos** ou o **nome do grupo de recursos**, conforme mostrado na seguinte imagem: 

    :::image type="content" source="./media/create-lab-windows-vm-template/navigate-resource-group.png" alt-text="Navegar até grupo de recursos":::
3. Na página **Grupo de recursos**, você vê a lista de recursos no grupo de recursos. Confirme que você vê seu laboratório do tipo: `DevTest Lab` nos recursos. Você também verá os recursos dependentes, como a rede virtual e a máquina virtual, no grupo de recursos. 

    :::image type="content" source="./media/create-lab-windows-vm-template/resource-group-home-page.png" alt-text="Home page do grupo de recursos":::
4. Selecione seu laboratório na lista de recursos para ver a home page dele. Confirme que você vê a VM do Datacenter do Windows Server 2019 na lista **Minhas máquinas virtuais**. Na imagem a seguir, a seção **Essentials** está minimizada. 

    :::image type="content" source="./media/create-lab-windows-vm-template/lab-home-page.png" alt-text="Home page do laboratório":::

    > [!IMPORTANT] 
    > Mantenha esta página aberta e siga as instruções na próxima seção para limpar os recursos, a fim de evitar os custos de execução do laboratório e da VM no Azure. Se quiser seguir o próximo tutorial para testar o acesso à VM no laboratório, limpe os recursos depois de fazer isso. 

## <a name="clean-up-resources"></a>Limpar os recursos

1. Primeiro, exclua o laboratório para que seja possível excluir o grupo de recursos. Você não poderá excluir o grupo de recursos se houver um laboratório nele. Para excluir o laboratório, selecione **Excluir** na barra de ferramentas. 

    :::image type="content" source="./media/create-lab-windows-vm-template/delete-lab-button.png" alt-text="Botão Excluir laboratório":::
 2. Na página de confirmação, digite o **nome do laboratório** e selecione **Excluir**. 
 3. Aguarde até que o laboratório seja excluído. Selecione o ícone de **sino** para ver a notificação da operação de exclusão. Esse processo demora um pouco. Confirme a exclusão do laboratório e selecione o **grupo de recursos** no menu de navegação estrutural. 
 
    :::image type="content" source="./media/create-lab-windows-vm-template/confirm-lab-deletion.png" alt-text="Confirmar exclusão de VM nas notificações":::
 1. Na página **Grupo de recursos**, selecione **Excluir grupo de recursos** na barra de ferramentas. Na página de confirmação, digite o **nome do grupo de recursos** e selecione **Excluir**. Verifique as notificações para confirmar se o grupo de recursos foi excluído.
 
    :::image type="content" source="./media/create-lab-windows-vm-template/delete-resource-group-button.png" alt-text="Botão Excluir grupo de recursos":::

## <a name="next-steps"></a>Próximas etapas
Neste guia de início rápido, você criou um laboratório com uma VM. Para saber como acessar o laboratório, vá para o próximo tutorial:

> [!div class="nextstepaction"]
> [Tutorial: Acessar o laboratório](tutorial-use-custom-lab.md)
