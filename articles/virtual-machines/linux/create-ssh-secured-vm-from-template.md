---
title: Criar uma VM do Linux no Azure por meio de um modelo
description: Como usar a CLI do Azure para criar uma VM do Linux de um modelo do Resource Manager
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 03/22/2019
ms.author: cynthn
ms.openlocfilehash: 184e856636a74ce5b7ac72b2e8cab3d1f19c4e17
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89074382"
---
# <a name="how-to-create-a-linux-virtual-machine-with-azure-resource-manager-templates"></a>Como criar uma máquina virtual do Linux com os modelos do Azure Resource Manager

Saiba como criar uma VM (máquina virtual) do Linux usando um modelo de Azure Resource Manager e o CLI do Azure do Azure cloud Shell. Para criar uma máquina virtual do Windows, consulte [criar uma máquina virtual do Windows a partir de um modelo do Resource Manager](../windows/ps-template.md).

Uma alternativa é implantar o modelo do portal do Azure. Para abrir o modelo no portal, selecione o botão **implantar no Azure** .

[![Implantar no Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-sshkey%2Fazuredeploy.json)

## <a name="templates-overview"></a>Visão geral de modelos

Os modelos do Azure Resource Manager são arquivos JSON que definem a infraestrutura e a configuração de sua solução do Azure. Usando um modelo, você pode implantar a solução repetidamente em todo seu ciclo de vida e com a confiança de que seus recursos serão implantados em um estado consistente. Para saber mais sobre o formato do modelo e como construí-lo, consulte [início rápido: criar e implantar modelos de Azure Resource Manager usando o portal do Azure](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md). Para exibir a sintaxe JSON para os tipos de recursos, consulte [Definir recursos nos modelos do Azure Resource Manager](/azure/templates/microsoft.compute/allversions).

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

A criação de uma máquina virtual do Azure geralmente inclui duas etapas:

1. Crie um grupos de recursos. Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. Você deve criar um grupo de recursos antes de criar uma máquina virtual.
1. Crie uma máquina virtual.

O exemplo a seguir cria uma VM a partir de um [modelo de início rápido do Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json). Somente a autenticação SSH é permitida para essa implantação. Mediante solicitação, forneça o valor de sua própria chave pública SSH, como o conteúdo de *~/.ssh/id_rsa.pub*. Se você precisar criar um par de chaves SSH, confira [Como criar um par de chaves SSH para VMs Linux no Azure](mac-create-ssh-keys.md). Veja uma cópia do modelo:

[!code-json[create-linux-vm](~/quickstart-templates/101-vm-sshkey/azuredeploy.json)]

Para executar o script da CLI, selecione **Experimente-** o para abrir o Azure cloud Shell. Para colar o script, clique com o botão direito do mouse no Shell e selecione **colar**:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
echo "Enter the project name (used for generating resource names):" &&
read projectName &&
echo "Enter the administrator username:" &&
read username &&
echo "Enter the SSH public key:" &&
read key &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json --parameters projectName=$projectName adminUsername=$username adminPublicKey="$key" &&
az vm show --resource-group $resourceGroupName --name "$projectName-vm" --show-details --query publicIps --output tsv
```

O último comando CLI do Azure mostra o endereço IP público da VM recém-criada. Você precisa do endereço IP público para se conectar à máquina virtual. Consulte a próxima seção deste artigo.

No exemplo anterior, você especificou um modelo armazenado no GitHub. Também é possível baixar ou criar um modelo e especificar o caminho local com o parâmetro `--template-file`.

Estes são alguns recursos adicionais:

- Para saber como desenvolver modelos do Resource Manager, confira a [Documentação do Azure Resource Manager](../../azure-resource-manager/index.yml).
- Para ver os esquemas de máquina virtual do Azure, consulte [referência de modelo do Azure](/azure/templates/microsoft.compute/allversions).
- Para ver mais exemplos de modelo de máquina virtual, consulte [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular).

## <a name="connect-to-virtual-machine"></a>Conectar-se à máquina virtual

Depois, você pode enviar por SSH para sua VM, como de costume. Forneça seu próprio endereço IP público do comando anterior:

```bash
ssh <adminUsername>@<ipAddress>
```

## <a name="next-steps"></a>Próximas etapas

Neste exemplo, você criou uma VM básica do Linux. Para obter mais modelos do Resource Manager que incluem estruturas de aplicativo ou criar ambientes mais complexos, procure os [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular).

Confira a sintaxe e as propriedades do JSON para os tipos de recursos que você implantou para saber mais sobre a criação de modelos:

- [Microsoft.Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups)
- [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
- [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
- [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
- [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)
