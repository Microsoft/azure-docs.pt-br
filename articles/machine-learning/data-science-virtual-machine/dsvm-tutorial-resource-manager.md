---
title: 'Início Rápido: Criar uma VM de Ciência de Dados – Modelo do Resource Manager'
titleSuffix: Azure Data Science Virtual Machine
description: Neste guia de início rápido, você usa um modelo de Azure Resource Manager para implantar rapidamente um Máquina Virtual de Ciência de Dados
services: machine-learning
author: lobrien
ms.author: laobri
ms.date: 06/10/2020
ms.topic: quickstart
ms.service: data-science-vm
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 0683634223a63281ce2b42ebb02f87f9211a589e
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530659"
---
# <a name="quickstart-create-an-ubuntu-data-science-virtual-machine-using-an-arm-template"></a>Início Rápido: Criar uma Máquina Virtual de Ciência de Dados do Ubuntu usando um modelo do Resource Manager

Este guia de início rápido mostra como criar uma Máquina Virtual de Ciência de Dados do Ubuntu 18.04 usando um modelo do Azure Resource Manager. As Máquinas Virtuais de Ciência de Dados são máquinas virtuais baseadas em nuvem pré-carregadas com um conjunto de estruturas e ferramentas de ciência de dados e de machine learning. Quando implantadas em recursos de computação baseados em GPU, todas as ferramentas e bibliotecas são configuradas para usar a GPU.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Se seu ambiente atender aos pré-requisitos e você estiver familiarizado com o uso de modelos ARM, selecione o botão **Implantar no Azure**. O modelo será aberto no portal do Azure.

[![Implantar no Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-ubuntu-DSVM-GPU-or-CPU%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/services/machine-learning/) antes de começar.

* Para usar os comandos da CLI deste documento em seu **ambiente local**, você precisará da [CLI do Azure](/cli/azure/install-azure-cli).

## <a name="review-the-template"></a>Examinar o modelo

O modelo usado neste início rápido é proveniente dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-vm-ubuntu-DSVM-GPU-or-CPU/).

:::code language="json" source="~/quickstart-templates/101-vm-ubuntu-DSVM-GPU-or-CPU/azuredeploy.json":::

Os seguintes recursos do Azure estão definidos no modelo:

* [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
* [Microsoft.Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups)
* [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
* [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
* [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines): Criar uma máquina virtual baseada em nuvem. Neste modelo, a máquina virtual está configurada como uma Máquina Virtual de Ciência de Dados executando o Ubuntu 18.04.

## <a name="deploy-the-template"></a>Implantar o modelo

Para usar o modelo por meio da CLI do Azure, faça logon e escolha sua assinatura (consulte [Entrar na CLI do Azure](/cli/azure/authenticate-azure-cli)). Em seguida, execute:

```azurecli-interactive
read -p "Enter the name of the resource group to create:" resourceGroupName &&
read -p "Enter the Azure location (e.g., centralus):" location &&
read -p "Enter the authentication type (must be 'password' or 'sshPublicKey') :" authenticationType &&
read -p "Enter the login name for the administrator account (may not be 'admin'):" adminUsername &&
read -p "Enter administrator account secure string (value of password or ssh public key):" adminPasswordOrKey &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-ubuntu-DSVM-GPU-or-CPU/azuredeploy.json" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri $templateUri --parameters adminUsername=$adminUsername authenticationType=$authenticationType adminPasswordOrKey=$adminPasswordOrKey &&
echo "Press [ENTER] to continue ..." &&
read
```

Ao executar o comando acima, digite:

1. O nome do grupo de recursos que você deseja criar para conter a DSVM e os recursos associados.
1. A localização do Azure na qual você deseja fazer a implantação.
1. O tipo de autenticação que deseja usar (insira a cadeia de caracteres `password` ou `sshPublicKey`).
1. O nome de logon da conta de administrador (esse valor não pode ser `admin`).
1. O valor da senha ou da chave pública SSH da conta.

## <a name="review-deployed-resources"></a>Examinar os recursos implantados

Para ver sua Máquina Virtual de Ciência de Dados:

1. Vá para o [Portal do Azure](https://portal.azure.com)
1. Entrar.
1. Escolha o grupo de recursos recém-criado.

Você verá as informações do grupo de recursos:

:::image type="content" source="media/dsvm-tutorial-resource-manager/resource-group-home.png" alt-text="Captura de tela de um grupo de recursos básico que contém uma DSVM":::

Clique no recurso da Máquina Virtual para ir para sua página de informações. Aqui, você pode encontrar informações sobre a VM, incluindo detalhes da conexão.

## <a name="clean-up-resources"></a>Limpar os recursos

Se não quiser usar essa máquina virtual, você poderá exclui-la. Como a DSVM está associada a outros recursos, como uma conta de armazenamento, provavelmente será interessante excluir o grupo de recursos inteiro que você criou. Exclua o grupo de recursos por meio do portal clicando no botão **Excluir** e confirmando a ação. Ou, você pode excluir o grupo de recursos por meio da CLI com:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você criou uma Máquina Virtual de Ciência de Dados com base em um modelo do Resource Manager.

> [!div class="nextstepaction"]
> [Exemplos de programas e instruções de ML](dsvm-samples-and-walkthroughs.md)
