---
title: 'Início Rápido: Criar um BD do Azure para MySQL – modelo do ARM'
description: Neste Início Rápido, saiba como criar um servidor do Banco de Dados do Azure para MySQL com integração de rede virtual usando um modelo do Azure Resource Manager.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 05/19/2020
ms.openlocfilehash: 3da3b1694a16507203d7f1f1f6cb5df58dd54423
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100366170"
---
# <a name="quickstart-use-an-arm-template-to-create-an-azure-database-for-mysql-server"></a>Início Rápido: Usar um modelo do ARM para criar um servidor de Banco de Dados do Azure para MySQL

O Banco de Dados do Azure para MySQL é um serviço gerenciado usado para executar, gerenciar e dimensionar Bancos de Dados MySQL altamente disponíveis na nuvem. Neste guia de início rápido, você usa um modelo do ARM (modelo do Azure Resource Manager) para criar um servidor do Banco de Dados do Azure para MySQL com integração com rede virtual. Você pode criar o servidor no o portal do Azure, na CLI do Azure ou no Azure PowerShell.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se seu ambiente atender aos pré-requisitos e você estiver familiarizado com o uso de modelos ARM, selecione o botão **Implantar no Azure**. O modelo será aberto no portal do Azure.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Implantar no Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-managed-mysql-with-vnet%2fazuredeploy.json)

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

Crie um Banco de Dados do Azure para o servidor MySQL com um conjunto definido de recursos de computação e armazenamento. Para saber mais, confira [tipos de preço do Banco de Dados do Azure para MySQL](concepts-pricing-tiers.md). Crie o serviço dentro de um [Grupo de recursos do Azure](../azure-resource-manager/management/overview.md).

O modelo usado neste início rápido é proveniente dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-managed-mysql-with-vnet/).

:::code language="json" source="~/quickstart-templates/101-managed-mysql-with-vnet/azuredeploy.json":::

O modelo define cinco recursos do Azure:

* [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
* [**Microsoft.Network/virtualNetworks/subnets**](/azure/templates/microsoft.network/virtualnetworks/subnets)
* [**Microsoft.DBforMySQL/servers**](/azure/templates/microsoft.dbformysql/servers)
* [**Microsoft.DBforMySQL/servers/virtualNetworkRules**](/azure/templates/microsoft.dbformysql/servers/virtualnetworkrules)
* [**Microsoft.DBforMySQL/servers/firewallRules**](/azure/templates/microsoft.dbformysql/servers/firewallrules)

Mais exemplos de modelos do Banco de Dados do Azure para MySQL podem ser encontrados na [galeria de modelos de início rápido](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Dbformysql&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Implantar o modelo

# <a name="portal"></a>[Portal](#tab/azure-portal)

Selecione o seguinte link para implantar o modelo de servidor do Banco de Dados do Azure para MySQL no portal do Azure:

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Implantar no Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-managed-mysql-with-vnet%2fazuredeploy.json)

Na página **Implantar Banco de Dados do Azure para MySQL com VNet**:

1. Para **Grupo de recursos**, selecione **Criar**, insira um nome para o novo grupo de recursos e, em seguida, selecione **OK**.

2. Caso tenha criado um grupo de recursos, selecione uma **Localização** para o grupo de recursos e para o novo servidor.

3. Insira um **Nome do Servidor**, um **Logon de Administrador** e uma **Senha de Logon de Administrador**.

    :::image type="content" source="./media/quickstart-create-mysql-server-database-using-arm-template/deploy-azure-database-for-mysql-with-vnet.png" alt-text="Implantar o Banco de Dados do Azure para MySQL com a janela VNet, modelo de início rápido do Azure, portal do Azure":::

4. Se desejar, altere as outras configurações padrão:

    * **Assinatura**: a assinatura do Azure que você deseja usar para o servidor.
    * **Capacidade de SKU**: a capacidade vCore, que pode ser *2* (o padrão), *4*, *8*, *16*, *32* ou *64*.
    * **Nome do SKU**: o prefixo do nível de SKU, a família de SKU e a capacidade de SKU, unidos por sublinhados, como *B_Gen5_1*, *GP_Gen5_2* (o padrão) ou *MO_Gen5_32*.
    * **Tamanho do SKU em MB**: o tamanho do armazenamento, em megabytes, do servidor do Banco de Dados do Azure para MySQL (padrão *5120*).
    * **Nível do SKU**: o nível de implantação, como *Basic*, *GeneralPurpose* (o padrão) ou *MemoryOptimized*.
    * **Família do SKU**: *Gen4* ou *Gen5* (o padrão), que indica a geração de hardware para a implantação do servidor.
    * **Versão do MySQL**: a versão do servidor MySQL a ser implantada, como *5.6* ou *5.7* (o padrão).
    * **Dias de retenção de backup**: o período desejado para retenção de backup com redundância geográfica, em dias (padrão *7*).
    * **Backup de redundância geográfica**: *Habilitado* ou *Desabilitado* (o padrão), dependendo dos requisitos de Geo-DR (recuperação de desastre geográfico).
    * **Nome da Rede Virtual**: o nome da rede virtual (padrão *azure_mysql_vnet*).
    * **Nome da Sub-Rede**: o nome da sub-rede (padrão *azure_mysql_subnet*).
    * **Nome da Regra da Rede Virtual**: o nome da regra da rede virtual que permite a sub-rede (padrão *AllowSubnet*).
    * **Prefixo de Endereço da VNET**: o prefixo de endereço para a rede virtual (padrão *10.0.0.0/16*).
    * **Prefixo da Sub-Rede**: o prefixo de endereço da sub-rede (padrão *10.0.0.0/16*).

5. Leia os termos e condições e depois selecione **Eu concordo com os termos e condições declarados acima**.

6. Selecione **Comprar**.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Use o código interativo a seguir para criar um servidor de Banco de Dados do Azure para MySQL usando o modelo. O código solicita o novo nome do servidor, o nome e o local de um novo grupo de recursos e um nome de conta e senha de administrador.

Para executar o código em Azure Cloud Shell, selecione **Experimentar** no canto superior de qualquer bloco de código.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter a name for the new Azure Database for MySQL server"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group where the server will exist"
$location = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"
$adminUser = Read-Host -Prompt "Enter the Azure Database for MySQL server's administrator account name"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString

New-AzResourceGroup -Name $resourceGroupName -Location $location # Use this command when you need to create a new resource group for your deployment
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-managed-mysql-with-vnet/azuredeploy.json `
    -serverName $serverName `
    -administratorLogin $adminUser `
    -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue ..."
```

# <a name="cli"></a>[CLI](#tab/CLI)

Use o código interativo a seguir para criar um novo servidor de Banco de Dados do Azure para MySQL usando o modelo. O código solicita o novo nome do servidor, o nome e o local de um novo grupo de recursos e um nome de conta e senha de administrador.

Para executar o código em Azure Cloud Shell, selecione **Experimentar** no canto superior de qualquer bloco de código.

```azurecli-interactive
echo "Enter a name for the new Azure Database for MySQL server:" &&
read serverName &&
echo "Enter a name for the new resource group where the server will exist:" &&
read resourceGroupName &&
echo "Enter an Azure region (for example, centralus) for the resource group:" &&
read location &&
echo "Enter the Azure Database for MySQL server's administrator account name:" &&
read adminUser &&
echo "Enter the administrator password:" &&
read adminPassword &&
params='serverName='$serverName' administratorLogin='$adminUser' administratorLoginPassword='$adminPassword &&
az group create --name $resourceGroupName --location $location &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-managed-mysql-with-vnet/azuredeploy.json &&
echo "Press [ENTER] to continue ..."
```

---

## <a name="review-deployed-resources"></a>Examinar os recursos implantados

# <a name="portal"></a>[Portal](#tab/azure-portal)

Siga estas etapas para ver uma visão geral do seu novo servidor do Banco de Dados do Azure para MySQL:

1. No [portal do Azure](https://portal.azure.com), pesquise e selecione **servidores do Banco de Dados do Azure para MySQL**.

2. Na lista banco de dados, selecione o novo servidor. A página **Visão Geral** para o novo servidor do Banco de Dados do Azure para MySQL é exibida.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Execute o código interativo a seguir para ver detalhes sobre o servidor do Banco de Dados do Azure para MySQL. Você precisará inserir o nome do novo servidor.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter the name of your Azure Database for MySQL server"
Get-AzResource -ResourceType "Microsoft.DBforMySQL/servers" -Name $serverName | ft
Write-Host "Press [ENTER] to continue..."
```

# <a name="cli"></a>[CLI](#tab/CLI)

Execute o código interativo a seguir para exibir detalhes sobre o servidor do Banco de Dados do Azure para MySQL. Você precisará inserir o nome e o grupo de recursos do novo servidor.

```azurecli-interactive
echo "Enter your Azure Database for MySQL server name:" &&
read serverName &&
echo "Enter the resource group where the Azure Database for MySQL server exists:" &&
read resourcegroupName &&
az resource show --resource-group $resourcegroupName --name $serverName --resource-type "Microsoft.DbForMySQL/servers"
```

---

## <a name="exporting-arm-template-from-the-portal"></a>Como exportar um modelo do ARM por meio do portal
Você pode [exportar um modelo do ARM](../azure-resource-manager/templates/export-template-portal.md) pelo portal do Azure. Há duas maneiras de exportar um modelo:

- [Exportação do grupo de recursos ou do recurso](../azure-resource-manager/templates/export-template-portal.md#export-template-from-a-resource). Essa opção gera um novo modelo com base nos recursos existentes. O modelo exportado é um "instantâneo" do estado atual do grupo de recursos. Você pode exportar um grupo de recursos inteiro ou recursos específicos dentro desse grupo de recursos.
- [Exportação antes da implantação ou do histórico](../azure-resource-manager/templates/export-template-portal.md#export-template-before-deployment). Essa opção recupera uma cópia exata de um modelo usado para implantação.

Quando você exportar o modelo, na seção ```"properties":{ }``` do recurso de servidor MySQL, você observará que ```administratorLogin``` e ```administratorLoginPassword``` não serão incluídos por motivos de segurança. Você **PRECISARÁ** adicionar esses parâmetros ao modelo antes de implantá-lo ou ele falhará.

```
"resources": [
    {
      "type": "Microsoft.DBforMySQL/servers",
      "apiVersion": "2017-12-01",
      "name": "[parameters('servers_name')]",
      "location": "southcentralus",
      "sku": {
                "name": "B_Gen5_1",
                "tier": "Basic",
                "family": "Gen5",
                "capacity": 1
            },
      "properties": {
        "administratorLogin": "[parameters('administratorLogin')]",
        "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
```

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
Write-Host "Press [ENTER] to continue..."
```

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

---

## <a name="next-steps"></a>Próximas etapas

Para obter um tutorial passo a passo que orienta você durante o processo de criação de um modelo do ARM, confira:

> [!div class="nextstepaction"]
> [ Tutorial: Criar e implantar seu primeiro modelo do Resource Manager](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
