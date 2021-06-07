---
title: 'Início Rápido: Criar um Servidor Flexível do BD do Azure para MySQL – modelo do ARM'
description: Neste guia de início rápido, saiba como criar um Servidor Flexível do Banco de Dados do Azure para MySQL usando um modelo do ARM.
author: mksuni
ms.service: mysql
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: sumuth
ms.date: 10/23/2020
ms.openlocfilehash: def9e4f1b3f1c4e8f88f77dfe6906a8c96a94744
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100389460"
---
# <a name="quickstart-use-an-arm-template-to-create-an-azure-database-for-mysql---flexible-server-preview"></a>Início Rápido: Usar um modelo do ARM para criar um Servidor Flexível do Banco de Dados do Azure para MySQL (versão prévia)

> [!IMPORTANT]
> O Banco de Dados do Azure para MySQL – Servidor Flexível está atualmente na versão prévia pública.

O Servidor Flexível do Banco de Dados do Azure para MySQL (versão prévia) é um serviço gerenciado usado para executar, gerenciar e escalar bancos de dados MySQL altamente disponíveis na nuvem. Você pode usar um modelo do Azure Resource Manager (modelo do ARM) para provisionar um servidor flexível e implantar vários servidores ou vários bancos de dados em um servidor.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Pré-requisitos

Uma conta do Azure com uma assinatura ativa. [Crie um gratuitamente](https://azure.microsoft.com/free/).

## <a name="review-the-template"></a>Examinar o modelo

Um Servidor Flexível do Banco de Dados do Azure para MySQL é o recurso pai de um ou mais bancos de dados em uma região. Ele fornece o escopo para políticas de gerenciamento que se aplicam aos respectivos bancos de dados: logon, firewall, usuários, funções, configurações.

Crie um arquivo _mysql-flexible-server-template.json_ e copie script JSON a seguir nele.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "administratorLogin": {
      "type": "String"
    },
    "administratorLoginPassword": {
      "type": "SecureString"
    },
    "location": {
      "type": "String"
    },
    "serverName": {
      "type": "String"
    },
    "serverEdition": {
      "type": "String"
    },
    "storageSizeMB": {
      "type": "Int"
    },
    "haEnabled": {
      "type": "string",
      "defaultValue": "Disabled"
    },
    "availabilityZone": {
      "type": "String"
    },
    "version": {
      "type": "String"
    },
    "tags": {
      "defaultValue": {},
      "type": "Object"
    },
    "firewallRules": {
      "defaultValue": {},
      "type": "Object"
    },
    "vnetData": {
      "defaultValue": {},
      "type": "Object"
    },
    "backupRetentionDays": {
      "type": "Int"
    }
  },
  "variables": {
    "api": "2020-07-01-preview",
    "firewallRules": "[parameters('firewallRules').rules]",
    "publicNetworkAccess": "[if(empty(parameters('vnetData')), 'Enabled', 'Disabled')]",
    "vnetDataSet": "[if(empty(parameters('vnetData')), json('{ \"subnetArmResourceId\": \"\" }'), parameters('vnetData'))]",
    "finalVnetData": "[json(concat('{ \"subnetArmResourceId\": \"', variables('vnetDataSet').subnetArmResourceId, '\"}'))]"
  },
  "resources": [
    {
      "type": "Microsoft.DBforMySQL/flexibleServers",
      "apiVersion": "[variables('api')]",
      "name": "[parameters('serverName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard_D4ds_v4",
        "tier": "[parameters('serverEdition')]"
      },
      "tags": "[parameters('tags')]",
      "properties": {
        "version": "[parameters('version')]",
        "administratorLogin": "[parameters('administratorLogin')]",
        "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
        "publicNetworkAccess": "[variables('publicNetworkAccess')]",
        "DelegatedSubnetArguments": "[if(empty(parameters('vnetData')), json('null'), variables('finalVnetData'))]",
        "haEnabled": "[parameters('haEnabled')]",
        "storageProfile": {
          "storageMB": "[parameters('storageSizeMB')]",
          "backupRetentionDays": "[parameters('backupRetentionDays')]"
        },
        "availabilityZone": "[parameters('availabilityZone')]"
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-08-01",
      "name": "[concat('firewallRules-', copyIndex())]",
      "dependsOn": [
        "[concat('Microsoft.DBforMySQL/flexibleServers/', parameters('serverName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.DBforMySQL/flexibleServers/firewallRules",
              "name": "[concat(parameters('serverName'),'/',variables('firewallRules')[copyIndex()].name)]",
              "apiVersion": "[variables('api')]",
              "properties": {
                "StartIpAddress": "[variables('firewallRules')[copyIndex()].startIPAddress]",
                "EndIpAddress": "[variables('firewallRules')[copyIndex()].endIPAddress]"
              }
            }
          ]
        }
      },
      "copy": {
        "name": "firewallRulesIterator",
        "count": "[if(greater(length(variables('firewallRules')), 0), length(variables('firewallRules')), 1)]",
        "mode": "Serial"
      },
      "condition": "[greater(length(variables('firewallRules')), 0)]"
    }
  ]
}
```

Esses recursos do Azure estão definidos no modelo:

- Microsoft.DBforMySQL/flexibleServers

## <a name="deploy-the-template"></a>Implantar o modelo

Selecione **Experimentar** no bloco de código do PowerShell a seguir para abrir o [Azure Cloud Shell](../../cloud-shell/overview.md).

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter a name for the new Azure Database for MySQL server"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group where the server will exist"
$location = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"
$adminUser = Read-Host -Prompt "Enter the Azure Database for MySQL server's administrator account name"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString

New-AzResourceGroup -Name $resourceGroupName -Location $location # Use this command when you need to create a new resource group for your deployment
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateFile "D:\Azure\Templates\EngineeringSite.json
    -serverName $serverName `
    -administratorLogin $adminUser `
    -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue ..."
```

## <a name="review-deployed-resources"></a>Examinar os recursos implantados

Siga estas etapas para verificar se o servidor foi criado no Azure.

### <a name="azure-portal"></a>Portal do Azure

1. No [portal do Azure](https://portal.azure.com), pesquise e selecione **servidores do Banco de Dados do Azure para MySQL**.
1. Na lista banco de dados, selecione o novo servidor. A página **Visão Geral** para o novo servidor do Banco de Dados do Azure para MySQL é exibida.

### <a name="powershell"></a>PowerShell

Será necessário inserir o nome do novo servidor para exibir detalhes sobre o Servidor Flexível do Banco de Dados do Azure para MySQL.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter the name of your Azure Database for MySQL server"
Get-AzResource -ResourceType "Microsoft.DBforMySQL/flexibleServers" -Name $serverName | ft
Write-Host "Press [ENTER] to continue..."
```

### <a name="cli"></a>CLI

Você precisará inserir o nome e o grupo de recursos do novo servidor para ver os detalhes sobre o Servidor Flexível do Banco de Dados do Azure para MySQL.

```azurecli-interactive
echo "Enter your Azure Database for MySQL server name:" &&
read serverName &&
echo "Enter the resource group where the Azure Database for MySQL server exists:" &&
read resourcegroupName &&
az resource show --resource-group $resourcegroupName --name $serverName --resource-type "Microsoft.DbForMySQL/flexibleServers"
```

## <a name="clean-up-resources"></a>Limpar os recursos

Mantenha esse grupo de recursos, o servidor e o banco de dados individual caso deseje ir para as [Próximas etapas](#next-steps). As próximas etapas mostram como se conectar e consultar seu banco de dados usando diferentes métodos.

Para excluir o grupo de recursos:

### <a name="azure-portal"></a>Portal do Azure

1. No [portal do Azure](https://portal.azure.com) pesquise e selecione **Grupos de recursos**.
1. Na lista grupo de recursos, escolha o nome do seu grupo de recursos.
1. Na página **Visão geral** do grupo de recursos, selecione **Excluir grupo de recursos**.
1. Na caixa de diálogo de confirmação, digite o nome do seu grupo de recursos e, em seguida, selecione **Excluir**.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

### <a name="cli"></a>CLI

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
> [Criar e implantar seu primeiro modelo do ARM](../../azure-resource-manager/templates/template-tutorial-create-first-template.md)

Para obter um tutorial passo a passo de como criar um aplicativo com o Serviço de Aplicativo usando o MySQL, confira:

> [!div class="nextstepaction"]
>[Criar um aplicativo Web PHP (Laravel) com o MySQL](tutorial-php-database-app.md)
