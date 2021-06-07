---
title: 'Início Rápido: Criar um BD do Azure para MariaDB – modelo do ARM'
description: Neste Início Rápido, aprenda a criar um servidor do Banco de Dados do Azure para MariaDB usando um modelo do Azure Resource Manager.
author: savjani
ms.author: pariks
ms.date: 05/14/2020
ms.topic: quickstart
ms.service: mariadb
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 6bf7f4d30f2ad4f9e0181aed332e3f6cb9265ca0
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531331"
---
# <a name="quickstart-use-an-arm-template-to-create-an-azure-database-for-mariadb-server"></a>Início Rápido: Usar um modelo do ARM para criar um servidor de Banco de Dados do Azure para MariaDB

O Banco de Dados do Azure para MariaDB é um serviço gerenciado usado para executar, gerenciar e dimensionar bancos de dados MariaDB altamente disponíveis na nuvem. Neste guia de início rápido, você usa um modelo do ARM (modelo do Azure Resource Manager) para criar um servidor do Banco de Dados do Azure para MariaDB no portal do Azure, no PowerShell ou na CLI do Azure.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se seu ambiente atender aos pré-requisitos e você estiver familiarizado com o uso de modelos ARM, selecione o botão **Implantar no Azure**. O modelo será aberto no portal do Azure.

[![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-managed-mariadb-with-vnet%2fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

# <a name="portal"></a>[Portal](#tab/azure-portal)

Uma conta do Azure com uma assinatura ativa. [Crie um gratuitamente](https://azure.microsoft.com/free/).

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

* Uma conta do Azure com uma assinatura ativa. [Crie um gratuitamente](https://azure.microsoft.com/free/).
* Se você quiser executar o código localmente, [Azure PowerShell](/powershell/azure/).

# <a name="cli"></a>[CLI](#tab/CLI)

* Uma conta do Azure com uma assinatura ativa. [Crie um gratuitamente](https://azure.microsoft.com/free/).
* Se você quiser executar o código localmente, [CLI do Azure](/cli/azure/).

---

## <a name="review-the-template"></a>Examinar o modelo

Crie um servidor do Banco de Dados do Azure para MariaDB com um conjunto definido de recursos de computação e armazenamento. Para saber mais, confira [tipos de preço do Banco de Dados do Azure para MariaDB](concepts-pricing-tiers.md). Crie o serviço dentro de um [Grupo de recursos do Azure](../azure-resource-manager/management/overview.md).

O modelo usado neste início rápido é proveniente dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-managed-mariadb-with-vnet/).

:::code language="json" source="~/quickstart-templates/101-managed-mariadb-with-vnet/azuredeploy.json":::

O modelo define cinco recursos do Azure:

* [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
* [**Microsoft.Network/virtualNetworks/subnets**](/azure/templates/microsoft.network/virtualnetworks/subnets)
* [**Microsoft.DBforMariaDB/servers**](/azure/templates/microsoft.dbformariadb/servers)
* [**Microsoft.DBforMariaDB/servers/virtualNetworkRules**](/azure/templates/microsoft.dbformariadb/servers/virtualnetworkrules)
* [**Microsoft.DBforMariaDB/servers/firewallRules**](/azure/templates/microsoft.dbformariadb/servers/firewallrules)

Mais exemplos do modelo do Banco de Dados do Azure para MariaDB podem ser encontrados nos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Dbformariadb&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Implantar o modelo

# <a name="portal"></a>[Portal](#tab/azure-portal)

Selecione o seguinte link para implantar o modelo de servidor do Banco de Dados do Azure para MariaDB no portal do Azure:

[![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-managed-mariadb-with-vnet%2fazuredeploy.json)

Na página **Implantar Banco de Dados do Azure para MariaDB com VNet**:

1. Para **Grupo de recursos**, selecione **Criar**, insira um nome para o novo grupo de recursos e, em seguida, selecione **OK**.

2. Caso tenha criado um grupo de recursos, selecione uma **Localização** para o grupo de recursos e para o novo servidor.

3. Insira um **Nome do Servidor**, um **Logon de Administrador** e uma **Senha de Logon de Administrador**.

    ![Implantar o Banco de Dados do Azure para MariaDB com a janela VNet, modelo de início rápido do Azure, portal do Azure](./media/quickstart-create-mariadb-server-database-arm-template/deploy-azure-database-mariadb-vnet.png)

4. Se desejar, altere as outras configurações padrão:

    * **Assinatura**: a assinatura do Azure que você deseja usar para o servidor.
    * **Capacidade de SKU**: a capacidade vCore, que pode ser *2* (o padrão), *4*, *8*, *16*, *32* ou *64*.
    * **Nome do SKU**: o prefixo do nível de SKU, a família de SKU e a capacidade de SKU, unidos por sublinhados, como *B_Gen5_1*, *GP_Gen5_2* (o padrão) ou *MO_Gen5_32*.
    * **Tamanho do SKU em MB**: o tamanho do armazenamento, em megabytes, do servidor do Banco de Dados do Azure para MariaDB (padrão *51200*).
    * **Nível do SKU**: o nível de implantação, como *Basic*, *GeneralPurpose* (o padrão) ou *MemoryOptimized*.
    * **Família do SKU**: *Gen4* ou *Gen5* (o padrão), que indica a geração de hardware para a implantação do servidor.
    * **Versão do MariaDB**: a versão do servidor MariaDB a ser implantada, como *10.2* ou *10.3* (o padrão).
    * **Dias de retenção de backup**: o período desejado para retenção de backup com redundância geográfica, em dias (padrão *7*).
    * **Backup de redundância geográfica**: *Habilitado* ou *Desabilitado* (o padrão), dependendo dos requisitos de Geo-DR (recuperação de desastre geográfico).
    * **Nome da Rede Virtual**: o nome da rede virtual (padrão *azure_mariadb_vnet*).
    * **Nome da Sub-Rede**: o nome da sub-rede (padrão *azure_mariadb_subnet*).
    * **Nome da Regra da Rede Virtual**: o nome da regra da rede virtual que permite a sub-rede (padrão *AllowSubnet*).
    * **Prefixo de Endereço da VNET**: o prefixo de endereço para a rede virtual (padrão *10.0.0.0/16*).
    * **Prefixo da Sub-Rede**: o prefixo de endereço da sub-rede (padrão *10.0.0.0/16*).

5. Leia os termos e condições e depois selecione **Eu concordo com os termos e condições declarados acima**.

6. Selecione **Comprar**.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Use o código interativo a seguir para criar um servidor de Banco de Dados do Azure para MariaDB usando o modelo. O código solicita o novo nome do servidor, o nome e o local de um novo grupo de recursos e um nome de conta e senha de administrador.

Para executar o código em Azure Cloud Shell, selecione **Experimentar** no canto superior de qualquer bloco de código.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter a name for the new Azure Database for MariaDB server"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group where the server will exist"
$location = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"
$adminUser = Read-Host -Prompt "Enter the Azure Database for MariaDB server's administrator account name"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString

New-AzResourceGroup -Name $resourceGroupName -Location $location # Use this command when you need to create a new resource group for your deployment
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-managed-mariadb-with-vnet/azuredeploy.json `
    -serverName $serverName `
    -administratorLogin $adminUser `
    -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue: "
```

# <a name="cli"></a>[CLI](#tab/CLI)

Use o código interativo a seguir para criar um servidor de Banco de Dados do Azure para MariaDB usando o modelo. O código solicita o novo nome do servidor, o nome e o local de um novo grupo de recursos e um nome de conta e senha de administrador.

Para executar o código em Azure Cloud Shell, selecione **Experimentar** no canto superior de qualquer bloco de código.

```azurecli-interactive
read -p "Enter a name for the new Azure Database for MariaDB server:" serverName &&
read -p "Enter a name for the new resource group where the server will exist:" resourceGroupName &&
read -p "Enter an Azure region (for example, centralus) for the resource group:" location &&
read -p "Enter the Azure Database for MariaDB server's administrator account name:" adminUser &&
read -p "Enter the administrator password:" adminPassword &&
params='serverName='$serverName' administratorLogin='$adminUser' administratorLoginPassword='$adminPassword &&
az group create --name $resourceGroupName --location $location &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-managed-mariadb-with-vnet/azuredeploy.json &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="review-deployed-resources"></a>Examinar os recursos implantados

# <a name="portal"></a>[Portal](#tab/azure-portal)

Siga estas etapas e tenha uma visão geral do seu novo servidor do Banco de Dados do Azure para MariaDB:

1. No [portal do Azure](https://portal.azure.com), pesquise e selecione **servidores do Banco de Dados do Azure para MariaDB**.

2. Na lista banco de dados, selecione o novo servidor. A página **Visão Geral** para o novo servidor do Banco de Dados do Azure para MariaDB é exibida.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Execute o código interativo a seguir para exibir detalhes sobre o servidor do Banco de Dados do Azure para MariaDB. Você precisará inserir o nome do novo servidor.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter the name of your Azure Database for MariaDB server"
Get-AzResource -ResourceType "Microsoft.DbForMariaDB/servers" -Name $serverName | ft
Read-Host -Prompt "Press [ENTER] to continue: "
```

# <a name="cli"></a>[CLI](#tab/CLI)

Execute o código interativo a seguir para exibir detalhes sobre o servidor do Banco de Dados do Azure para MariaDB. Você precisará inserir o nome e o grupo de recursos do novo servidor.

```azurecli-interactive
read -p "Enter your Azure Database for MariaDB server name: " serverName &&
read -p "Enter the resource group where the Azure Database for MariaDB server exists: " resourcegroupName &&
az resource show --resource-group $resourcegroupName --name $serverName --resource-type "Microsoft.DbForMariaDB/servers" &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, exclua o grupo de recursos, que excluirá os recursos no grupo de recursos.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. No [portal do Azure](https://portal.azure.com) pesquise e selecione **Grupos de recursos**.

2. Na lista grupo de recursos, escolha o nome do seu grupo de recursos.

3. Na página **Visão geral** do grupo de recursos, selecione **Excluir grupo de recursos**.

4. Na caixa de diálogo de confirmação, digite o nome do seu grupo de recursos e, em seguida, selecione **Excluir**.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Read-Host -Prompt "Press [ENTER] to continue: "
```

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
read -p "Enter the Resource Group name: " resourceGroupName &&
az group delete --name $resourceGroupName &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="next-steps"></a>Próximas etapas

Para obter um tutorial passo a passo que orienta você durante o processo de criação de um modelo do ARM, confira:

> [!div class="nextstepaction"]
> [ Tutorial: Criar e implantar seu primeiro modelo do Resource Manager](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
