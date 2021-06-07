---
title: 'Início Rápido: Usar um modelo do Resource Manager para criar uma VM Windows'
description: Neste guia de início rápido, você aprenderá a usar o modelo do Resource Manager para criar uma máquina virtual Windows
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.topic: quickstart
ms.workload: infrastructure
ms.date: 06/04/2020
ms.author: cynthn
ms.custom: subject-armqs
ms.openlocfilehash: b2856c51ccc31b57ea567c5d0940cd260797f770
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102560745"
---
# <a name="quickstart-create-a-windows-virtual-machine-using-an-arm-template"></a>Início Rápido: Criar uma máquina virtual do Windows usando um modelo do Resource Manager

Este guia de início rápido mostra como usar um modelo do Azure Resource Manager para implantar uma VM (máquina virtual) do Windows no Azure.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Se seu ambiente atender aos pré-requisitos e você estiver familiarizado com o uso de modelos ARM, selecione o botão **Implantar no Azure**. O modelo será aberto no portal do Azure.

[![Implantar no Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-vm-simple-windows%2fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="review-the-template"></a>Examinar o modelo

O modelo usado neste início rápido é proveniente dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

:::code language="json" source="~/quickstart-templates/101-vm-simple-windows/azuredeploy.json":::


Vários recursos do Azure estão definidos no modelo:

- [**Microsoft.Network/virtualNetworks/subnets**](/azure/templates/Microsoft.Network/virtualNetworks/subnets): criar uma sub-rede.
- [**Microsoft.Storage/storageAccounts**](/azure/templates/Microsoft.Storage/storageAccounts): criar uma conta de armazenamento.
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/Microsoft.Network/publicIPAddresses): criar um endereço IP público.
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/Microsoft.Network/networkSecurityGroups): criar um grupo de segurança de rede.
- [**Microsoft.Network/virtualNetworks**](/azure/templates/Microsoft.Network/virtualNetworks): criar uma rede virtual.
- [**Microsoft.Network/networkInterfaces**](/azure/templates/Microsoft.Network/networkInterfaces): criar um NIC.
- [**Microsoft.Compute/virtualMachines**](/azure/templates/Microsoft.Compute/virtualMachines): criar uma máquina virtual.



## <a name="deploy-the-template"></a>Implantar o modelo

1. Selecione a imagem a seguir para entrar no Azure e abrir um modelo. O modelo cria um cofre de chaves e um segredo.

    [![Implantar no Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-vm-simple-windows%2fazuredeploy.json)

1. Selecione ou insira os seguintes valores. Use os valores padrão, quando disponíveis.

    - **Assinatura**: selecione uma assinatura do Azure.
    - **Grupo de recursos**: selecione um grupo de recursos existente na lista suspensa ou selecione **Criar**, insira um nome exclusivo para o grupo de recursos e clique em **OK**.
    - **Local**: selecione um local.  Por exemplo, **Centro dos EUA**.
    - **Nome de usuário administrador**: forneça um nome de usuário, como *azureuser*.
    - **Senha do administrador**: forneça uma senha a ser usada para a conta do administrador. A senha deve ter no mínimo 12 caracteres e atender a [requisitos de complexidade definidos](faq.md#what-are-the-password-requirements-when-creating-a-vm).
    - **Prefixo do rótulo DNS**: insira um identificador exclusivo a ser usado como parte do rótulo DNS.
    - **Versão do sistema operacional Windows**: selecione qual versão do Windows você deseja executar na VM.
    - **Tamanho da VM**: selecione o [tamanho](../sizes.md) a ser usado para a VM.
    - **Local**: o padrão será o mesmo local que o grupo de recursos, se ele já existir.
1. Selecione **Examinar + criar**. Após a conclusão da validação, selecione **Criar** para criar e implantar a VM.


O portal do Azure é usado para implantar o modelo. Além do portal do Azure, você também pode usar o Azure PowerShell, a CLI do Azure e a API REST. Para saber mais sobre outros métodos de implantação, confira [Implantar modelos](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Examinar os recursos implantados

Você pode usar o portal do Azure para verificar a VM e outros recursos que foram criados. Após a conclusão da implantação, selecione **Acesse o grupo de recursos** para ver a VM e outros recursos.


## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, exclua o grupo de recursos, que excluirá a VM e todos os recursos no grupo de recursos. 

1. Selecione o **Grupo de recursos**.
1. Na página do grupo de recursos, selecione **Excluir**.
1. Quando solicitado, digite o nome do grupo de recursos e depois selecione **Excluir**.


## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você implantou uma máquina virtual simples usando um modelo do Resource Manager. Para saber mais sobre máquinas virtuais do Azure, continue o tutorial para VMs do Linux.


> [!div class="nextstepaction"]
> [Tutoriais de máquina virtual do Windows Azure](./tutorial-manage-vm.md)