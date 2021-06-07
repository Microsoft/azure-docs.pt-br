---
title: 'Azure Resource Manager: Criar um banco de dados individual'
description: Criar um banco de dados individual no Banco de Dados SQL do Azure usando um modelo do Azure Resource Manager.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: subject-armqs sqldbrb=1
ms.devlang: ''
ms.topic: quickstart
author: mumian
ms.author: jgao
ms.reviewer: sstein
ms.date: 06/24/2020
ms.openlocfilehash: b7883144509760c9670decd50062d2595b8dc495
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96187078"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-an-arm-template"></a>Início Rápido: Criar um banco de dados individual no Banco de Dados SQL do Azure usando um modelo do ARM

A criação de um [banco de dados individual](single-database-overview.md) é a opção mais rápida e simples para a criação de um banco de dados no Banco de Dados SQL do Azure. Este início rápido mostra como criar um banco de dados individual usando um modelo do ARM (modelo do Azure Resource Manager).

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Se seu ambiente atender aos pré-requisitos e você estiver familiarizado com o uso de modelos ARM, selecione o botão **Implantar no Azure**. O modelo será aberto no portal do Azure.

[![Implantar no Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-database%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/).

## <a name="review-the-template"></a>Examinar o modelo

Um banco de dados individual tem um conjunto definido de recursos de computação, memória, E/S e armazenamento usando um dos dois [modelos de compra](purchasing-models.md). Quando você cria um banco de dados individual, você também define um [servidor](logical-servers.md) para gerenciá-lo e colocá-lo no [grupo de recursos do Azure](../../active-directory-b2c/overview.md) em uma região especificada.

O modelo usado neste início rápido é proveniente dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-sql-database/).

:::code language="json" source="~/quickstart-templates/101-sql-database/azuredeploy.json":::

Esses recursos do Azure estão definidos no modelo:

- [**Microsoft.Sql/servers**](/azure/templates/microsoft.sql/servers)
- [**Microsoft.Sql/servers/databases**](/azure/templates/microsoft.sql/servers/databases)

Encontre mais amostras de modelo do Banco de Dados SQL do Azure nos [Modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sql&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Implantar o modelo

Selecione **Experimentar** no seguinte bloco de código do PowerShell para abrir o Azure Cloud Shell.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter an Azure location (i.e. centralus)"
$adminUser = Read-Host -Prompt "Enter the SQL server administrator username"
$adminPassword = Read-Host -Prompt "Enter the SQl server administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-sql-database/azuredeploy.json" -administratorLogin $adminUser -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue ..."
```

## <a name="validate-the-deployment"></a>Validar a implantação

Para consultar o banco de dados, confira [Consultar o banco de dados](single-database-create-quickstart.md#query-the-database).

## <a name="clean-up-resources"></a>Limpar os recursos

Mantenha esse grupo de recursos, o servidor e o banco de dados individual caso deseje ir para as [Próximas etapas](#next-steps). As próximas etapas mostram como se conectar e consultar seu banco de dados usando diferentes métodos.

Para excluir o grupo de recursos:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Próximas etapas

- Crie uma regra de firewall no nível do servidor para se conectar ao banco de dados individual por meio de ferramentas locais ou remotas. Para obter mais informações, consulte [Criar uma regra de firewall no nível do servidor](firewall-create-server-level-portal-quickstart.md).
- Depois de criar uma regra de firewall no nível do servidor, [conecte-se e consulte](connect-query-content-reference-guide.md) seu banco de dados usando várias ferramentas e linguagens diferentes.
  - [Conectar e consultar usando o SQL Server Management Studio](connect-query-ssms.md)
  - [Conectar e consultar usando o Azure Data Studio](/sql/azure-data-studio/quickstart-sql-database?toc=%2fazure%2fsql-database%2ftoc.json)
- Para criar um banco de dados individual usando a CLI do Azure, confira [Amostras da CLI do Azure](az-cli-script-samples-content-guide.md).
- Para criar um banco de dados individual usando o Azure PowerShell, confira [Amostras do Azure PowerShell](powershell-script-content-guide.md).
- Para saber como criar modelos do ARM, confira [Criar seu primeiro modelo](../../azure-resource-manager/templates/template-tutorial-create-first-template.md).