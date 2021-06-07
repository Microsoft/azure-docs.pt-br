---
title: 'Início Rápido: Criar um Servidor Flexível do Banco de Dados do Azure para PostgresSQL – modelo ARM'
description: Neste Início Rápido, saiba como criar um Servidor Flexível do Banco de Dados do Azure para PostgresSQL usando um modelo ARM.
author: mksuni
ms.service: postgresql
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: sumuth
ms.date: 2/11/2021
ms.openlocfilehash: 1f6abb62086bf92be8ae2fe50abbfa5300185fd7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100384258"
---
# <a name="quickstart-use-an-arm-template-to-create-an-azure-database-for-postgresql---flexible-server"></a>Início Rápido: Usar um modelo ARM para criar um Banco de Dados do Azure para PostgreSQL – Servidor Flexível

> [!IMPORTANT]
> O Servidor Flexível do Banco de Dados do Azure para PostgreSQL está em versão prévia

O servidor flexível é um serviço gerenciado usado para executar, gerenciar e dimensionar bancos de dados do PostgreSQL altamente disponíveis na nuvem. Será possível usar um modelo do Azure Resource Manager (modelo do ARM) para provisionar um Servidor Flexível do PostgreSQL com o objetivo de implantar vários servidores ou diversos bancos de dados em um servidor.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

O Azure Resource Manager é p serviço de implantação e gerenciamento do Azure. Ele fornece uma camada de gerenciamento que lhe permite criar, atualizar e excluir recursos em sua conta do Azure. Use recursos de gerenciamento, como controle de acesso, bloqueios e marcas, para proteger e organizar seus recursos após a implantação. Para saber mais sobre os modelos do Azure Resource Manager, confira [Visão geral de implantação de modelo](../../azure-resource-manager/templates/overview.md).

## <a name="prerequisites"></a>Pré-requisitos

Uma conta do Azure com uma assinatura ativa. [Crie um gratuitamente](https://azure.microsoft.com/free/).

## <a name="review-the-template"></a>Examinar o modelo

Um Servidor do Banco de Dados do Azure para PostgresSQL é o recurso pai de um ou mais bancos de dados de uma região. Ele fornece o escopo das políticas de gerenciamento que se aplicam aos respectivos bancos de dados: logon, firewall, usuários, funções, configurações etc.

Crie um arquivo _postgres-flexible-server-template.json_ e copie o script JSON a seguir nele.

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
      "type": "string"
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
    "api": "2020-02-14-privatepreview",
    "firewallRules": "[parameters('firewallRules').rules]",
    "publicNetworkAccess": "[if(empty(parameters('vnetData')), 'Enabled', 'Disabled')]",
    "vnetDataSet": "[if(empty(parameters('vnetData')), json('{ \"subnetArmResourceId\": \"\" }'), parameters('vnetData'))]",
    "finalVnetData": "[json(concat('{ \"subnetArmResourceId\": \"', variables('vnetDataSet').subnetArmResourceId, '\"}'))]"
  },
  "resources": [
    {
      "type": "Microsoft.DBforPostgreSQL/flexibleServers",
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
        "[concat('Microsoft.DBforPostgreSQL/flexibleServers/', parameters('serverName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.DBforPostgreSQL/flexibleServers/firewallRules",
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

- Microsoft.DBforPostgreSQL/flexibleServers

## <a name="deploy-the-template"></a>Implantar o modelo

Selecione **Experimentar** no seguinte bloco de código do PowerShell para abrir o Azure Cloud Shell.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter a name for the new Azure Database for PostgreSQL server"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group where the server will exist"
$location = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"
$adminUser = Read-Host -Prompt "Enter the Azure Database for PostgreSQL server's administrator account name"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString

New-AzResourceGroup -Name $resourceGroupName -Location $location # Use this command when you need to create a new resource group for your deployment
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateFile "D:\Azure\Templates\EngineeringSite.json
    -serverName $serverName `
    -administratorLogin $adminUser `
    -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue ..."
```
---

## <a name="review-deployed-resources"></a>Examinar os recursos implantados

Siga estas etapas para verificar se o servidor foi criado no Azure.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

1. No [portal do Azure](https://portal.azure.com), pesquise por **Servidores Flexíveis do Banco de Dados do Azure para PostgreSQL (Versão Prévia) e selecione-os**.
1. Na lista de banco de dados, selecione o novo servidor para exibir a página **Visão Geral** com o objetivo de gerenciar o servidor.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Será necessário inserir o nome do novo servidor para exibir detalhes sobre o Servidor Flexível do Banco de Dados do Azure para PostgreSQL.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter the name of your Azure Database for PostgreSQL server"
Get-AzResource -ResourceType "Microsoft.DBforPostgreSQL/flexibleServers" -Name $serverName | ft
Write-Host "Press [ENTER] to continue..."
```

# <a name="cli"></a>[CLI](#tab/CLI)

Você precisará inserir o nome e o grupo de recursos do novo servidor para ver os detalhes sobre o Servidor Flexível do Banco de Dados do Azure para PostgreSQL.

```azurecli-interactive
echo "Enter your Azure Database for PostgreSQL Flexible Server name:" &&
read serverName &&
echo "Enter the resource group where the Azure Database for PostgreSQL Flexible Server exists:" &&
read resourcegroupName &&
az resource show --resource-group $resourcegroupName --name $serverName --resource-type "Microsoft.DBforPostgreSQL/flexibleServers"
```

---


## <a name="clean-up-resources"></a>Limpar os recursos

Mantenha esse grupo de recursos, o servidor e o banco de dados individual caso deseje ir para as [Próximas etapas](#next-steps). As próximas etapas mostram como se conectar e consultar seu banco de dados usando diferentes métodos.

Para excluir o grupo de recursos:

# <a name="portal"></a>[Portal](#tab/azure-portal)

No [portal](https://portal.azure.com), selecione o grupo de recursos que você deseja excluir.

1. Selecione **Excluir grupo de recursos**.
1. Para confirmar a exclusão, digite o nome do grupo de recursos

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResourceGroup -Name ExampleResourceGroup
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az group delete --name ExampleResourceGroup
```
----

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Como migrar seu banco de dados usando o despejo e a restauração](../howto-migrate-using-dump-and-restore.md)
