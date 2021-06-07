---
title: Criar VM do SQL Server usando um modelo do ARM
description: Saiba como criar um SQL Server na VM (máquina virtual) do Azure usando um modelo do ARM (Azure Resource Manager).
author: MashaMSFT
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: mathoma
ms.date: 06/29/2020
ms.service: virtual-machines-sql
ms.subservice: deployment
ms.openlocfilehash: a20cb27ac91a0b01ed9cc3a5ac4c5c57f90ceda1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359635"
---
# <a name="quickstart-create-sql-server-vm-using-an-arm-template"></a>Início Rápido: Criar VM do SQL Server usando um modelo do ARM

Use este modelo do ARM (Azure Resource Manager) para implantar um SQL Server na VM (máquina virtual) do Azure. 

[!INCLUDE [About Azure Resource Manager](../../../../includes/resource-manager-quickstart-introduction.md)]

Se seu ambiente atender aos pré-requisitos e você estiver familiarizado com o uso de modelos ARM, selecione o botão **Implantar no Azure**. O modelo será aberto no portal do Azure.

[![Implantar no Azure](../../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-sql-vm-new-storage%2fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

O modelo do ARM da VM do SQL Server requer o seguinte:

- A versão mais recente da [CLI do Azure](/cli/azure/install-azure-cli) e/ou do [PowerShell](/powershell/scripting/install/installing-powershell). 
- Um [grupo de recursos](../../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) pré-configurado com uma [rede virtual](../../../virtual-network/quick-create-portal.md) preparada e uma [sub-rede](../../../virtual-network/virtual-network-manage-subnet.md#add-a-subnet).
- Uma assinatura do Azure. Se você não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.


## <a name="review-the-template"></a>Examinar o modelo

O modelo usado neste início rápido é proveniente dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-sql-vm-new-storage/).

:::code language="json" source="~/quickstart-templates/101-sql-vm-new-storage/azuredeploy.json":::

Cinco recursos do Azure são definidos no modelo: 

- [Microsoft.Network/publicIpAddresses](/azure/templates/microsoft.network/publicipaddresses): Cria um endereço IP público. 
- [Microsoft.Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups): Cria um grupo de segurança de rede. 
- [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces): Configura o adaptador de rede. 
- [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines): Cria uma máquina virtual no Azure. 
- [Microsoft.SqlVirtualMachine/SqlVirtualMachines](/azure/templates/microsoft.sqlvirtualmachine/sqlvirtualmachines): registra a máquina virtual a extensão do Agente de IaaS do SQL. 

Mais modelos de SQL Server na VM do Azure podem ser encontrados na [galeria de modelos de início rápido](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sqlvirtualmachine&pageNumber=1&sort=Popular).


## <a name="deploy-the-template"></a>Implantar o modelo

1. Selecione a imagem a seguir para entrar no Azure e abrir um modelo. O modelo cria uma máquina virtual com a versão pretendida do SQL Server instalada e registrada na extensão do Agente de IaaS do SQL. 

   [![Implantar no Azure](../../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-sql-vm-new-storage%2fazuredeploy.json)

2. Selecione ou insira os seguintes valores.

    * **Assinatura**: Selecione uma assinatura do Azure.
    * **Grupo de recursos**: O grupo de recursos preparado para sua VM do SQL Server. 
    * **Região**: Selecione uma região.  Por exemplo, **Centro dos EUA**.
    * **Nome da máquina virtual**: Insira um nome para a máquina virtual do SQL Server. 
    * **Tamanho da Máquina Virtual**: escolha o tamanho apropriado para sua máquina virtual na lista suspensa.
    * **Nome da Rede Virtual Existente**: insira o nome da rede virtual preparada para sua VM do SQL Server. 
    * **Grupo de Recursos da Vnet Existente**: insira o grupo de recursos em que sua rede virtual foi preparada. 
    * **Nome da Sub-Rede Existente**: o nome da sua sub-rede preparada. 
    * **Oferta de Imagem**: escolha o SQL Server e a imagem do Windows Server que melhor atendem às suas necessidades empresariais. 
    * **SKU do SQL**: escolha a edição da SKU do SQL Server que melhor atenda às suas necessidades empresariais. 
    * **Nome de Usuário do Administrador**: o nome de usuário para o administrador da máquina virtual. 
    * **Senha do Administrador**: a senha usada pela conta de administrador da VM. 
    * **Tipo de Carga de Trabalho de Armazenamento**:  o tipo de armazenamento para a carga de trabalho que melhor corresponde à sua empresa. 
    * **Contagem de Discos de Dados SQL**:  o número de discos que o SQL Server usa para arquivos de dados.  
    * **Caminho de Dados**:  o caminho para os arquivos de dados do SQL Server. 
    * **Contagem de Discos de Log do SQL**:  o número de discos que o SQL Server usa para arquivos de log. 
    * **Caminho do Log**:  o caminho para os arquivos de log do SQL Server. 
    * **Localização**:  A localização de todos os recursos, esse valor deve permanecer o padrão de `[resourceGroup().location]`. 

3. Selecione **Examinar + criar**. Depois que a VM do SQL Server for implantada com êxito, você receberá uma notificação.

O portal do Azure é usado para implantar o modelo. Além do portal do Azure, você também pode usar o Azure PowerShell, a CLI do Azure e a API REST. Para saber mais sobre outros métodos de implantação, confira [Implantar modelos](../../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Examinar os recursos implantados

Você pode usar a CLI do Azure para verificar os recursos implantados. 


```azurecli-interactive
echo "Enter the resource group where your SQL Server VM exists:" &&
read resourcegroupName &&
az resource list --resource-group $resourcegroupName 
```

## <a name="clean-up-resources"></a>Limpar os recursos

Quando você não precisar mais dele, exclua o grupo de recursos usando a CLI do Azure ou o Azure PowerShell:

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Próximas etapas

Para obter um tutorial passo a passo que orienta você durante o processo de criação de um modelo, confira:

> [!div class="nextstepaction"]
> [ Tutorial: Criar e implantar seu primeiro modelo do Resource Manager](../../../azure-resource-manager/templates/template-tutorial-create-first-template.md)

Para outras maneiras de implantar uma VM do SQL Server, confira: 
- [Azure portal](create-sql-vm-portal.md)
- [PowerShell](create-sql-vm-powershell.md)

Para saber mais, confira [uma visão geral do SQL Server em VMs do Azure](sql-server-on-azure-vm-iaas-what-is-overview.md).