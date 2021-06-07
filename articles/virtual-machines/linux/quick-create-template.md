---
title: 'Início Rápido: Usar um modelo do Resource Manager para criar uma VM Ubuntu Linux'
description: Neste guia de início rápido, você aprenderá a usar o modelo do Resource Manager para criar uma máquina virtual Linux
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: quickstart
ms.workload: infrastructure
ms.date: 06/04/2020
ms.author: cynthn
ms.custom: subject-armqs
ms.openlocfilehash: 97a9d3632cb0e78b899844b1aaa84a030c6da23e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102549610"
---
# <a name="quickstart-create-an-ubuntu-linux-virtual-machine-using-an-arm-template"></a>Início Rápido: Criar uma máquina virtual do Ubuntu Linux usando um modelo do Resource Manager

Este guia de início rápido mostra como usar um modelo do Azure Resource Manager para implantar uma VM (máquina virtual) do Ubuntu Linux no Azure.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Se seu ambiente atender aos pré-requisitos e você estiver familiarizado com o uso de modelos ARM, selecione o botão **Implantar no Azure**. O modelo será aberto no portal do Azure.

[![Implantar no Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-vm-simple-linux%2fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="review-the-template"></a>Examinar o modelo

O modelo usado neste início rápido é proveniente dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-vm-simple-linux/).

:::code language="json" source="~/quickstart-templates/101-vm-simple-linux/azuredeploy.json":::


Vários recursos do Azure estão definidos no modelo:

- [**Microsoft.Network/virtualNetworks/subnets**](/azure/templates/Microsoft.Network/virtualNetworks/subnets): criar uma sub-rede.
- [**Microsoft.Storage/storageAccounts**](/azure/templates/Microsoft.Storage/storageAccounts): criar uma conta de armazenamento.
- [**Microsoft.Network/networkInterfaces**](/azure/templates/Microsoft.Network/networkInterfaces): criar um NIC.
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/Microsoft.Network/networkSecurityGroups): criar um grupo de segurança de rede.
- [**Microsoft.Network/virtualNetworks**](/azure/templates/Microsoft.Network/virtualNetworks): criar uma rede virtual.
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/Microsoft.Network/publicIPAddresses): criar um endereço IP público.
- [**Microsoft.Compute/virtualMachines**](/azure/templates/Microsoft.Compute/virtualMachines): criar uma máquina virtual.

## <a name="deploy-the-template"></a>Implantar o modelo

1. Selecione a imagem a seguir para entrar no Azure e abrir um modelo. O modelo cria um cofre de chaves e um segredo.

    [![Implantar no Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-vm-simple-linux%2fazuredeploy.json)

1. Selecione ou insira os seguintes valores. Use os valores padrão, quando disponíveis.

    - **Assinatura**: selecione uma assinatura do Azure.
    - **Grupo de recursos**: selecione um grupo de recursos existente na lista suspensa ou selecione **Criar**, insira um nome exclusivo para o grupo de recursos e clique em **OK**.
    - **Local**: selecione um local.  Por exemplo, **Centro dos EUA**.
    - **Nome de usuário administrador**: forneça um nome de usuário, como *azureuser*.
    - **Tipo de autenticação**: Você pode escolher entre usar uma chave SSH ou uma senha.
    - **Senha de administrador ou chave** dependendo do que você escolher para o tipo de autenticação:
        - Se você escolher **password**, a senha deverá ter pelo menos 12 caracteres e atender aos [requisitos de complexidade definidos](faq.md#what-are-the-password-requirements-when-creating-a-vm).
        - Se você escolher **sshPublicKey**, cole o conteúdo da sua chave pública.
    - **Prefixo do rótulo DNS**: insira um identificador exclusivo a ser usado como parte do rótulo DNS.
    - **Versão do sistema operacional Ubuntu**: selecione qual versão do Ubuntu você deseja executar na VM.
    - **Local**: o padrão será o mesmo local que o grupo de recursos, se ele já existir.
    - **Tamanho da VM**: selecione o [tamanho](../sizes.md) a ser usado para a VM.
    - **Nome da Rede Virtual**: nome a ser usado para a vNet.
    - **Nome da sub-rede**: nome da sub-rede que a VM deve usar.
    - **Nome do Grupo de Segurança de Rede**: nome do NSG.
1. Selecione **Examinar + criar**. Após a conclusão da validação, selecione **Criar** para criar e implantar a VM.


O portal do Azure é usado para implantar o modelo. Além do portal do Azure, você também pode usar a CLI do Azure, o Azure PowerShell e a API REST. Para saber mais sobre outros métodos de implantação, confira [Implantar modelos](../../azure-resource-manager/templates/deploy-cli.md).

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
> [Tutoriais de máquina virtual do Linux Azure](./tutorial-manage-vm.md)