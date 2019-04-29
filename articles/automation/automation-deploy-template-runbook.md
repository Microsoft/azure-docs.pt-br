---
title: Implantar um modelo do Azure Resource Manager em um runbook de Automação do Azure
description: Como implantar um modelo do Azure Resource Manager armazenado no Armazenamento do Azure de um runbook
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
keywords: powershell,  runbook, json, automação do azure
ms.openlocfilehash: 2008ba697665baa0e8cf73564ec31d6267425404
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61073739"
---
# <a name="deploy-an-azure-resource-manager-template-in-an-azure-automation-powershell-runbook"></a>Implantar um modelo do Azure Resource Manager em um runbook do PowerShell de Automação do Azure

Você pode escrever um [runbook do PowerShell de Automação do Azure](automation-first-runbook-textual-powershell.md) que implanta um recurso do Azure usando um [modelo do Azure Resource Management](../azure-resource-manager/resource-manager-create-first-template.md).

Ao fazer isso, você pode automatizar a implantação de recursos do Azure. Você pode manter seus modelos do Gerenciador de Recursos em um local central seguro, como no Armazenamento do Azure.

Neste artigo, criamos um runbook do PowerShell que usa um modelo do Gerenciador de Recursos armazenado no [Armazenamento do Azure](../storage/common/storage-introduction.md) para implantar uma nova conta de Armazenamento do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará dos seguintes itens:

* Assinatura do Azure. Se você ainda não tiver uma, poderá [ativar os benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou [inscrever-se em uma conta gratuita](https://azure.microsoft.com/free/).
* [Conta de automação](automation-sec-configure-azure-runas-account.md) para manter o runbook e se autenticar nos recursos do Azure.  Esta conta deve ter permissão para iniciar e parar a máquina virtual.
* [Conta de Armazenamento do Azure](../storage/common/storage-create-storage-account.md) na qual será armazenado o modelo do Resource Manager
* Azure PowerShell instalado em um computador local. Consulte [Install and configure Azure PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) (Instalar e configurar o Azure PowerShell) para obter informações sobre como obter o Azure PowerShell.

## <a name="create-the-resource-manager-template"></a>Criar o modelo do Gerenciador de Recursos

Para este exemplo, usamos um modelo do Gerenciador de Recursos que implanta uma nova conta de Armazenamento do Azure.

Em um editor de texto, copie o seguinte texto:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "2018-02-01",
      "location": "[parameters('location')]",
      "sku": {
          "name": "[parameters('storageAccountType')]"
      },
      "kind": "Storage", 
      "properties": {
      }
    }
  ],
  "outputs": {
      "storageAccountName": {
          "type": "string",
          "value": "[variables('storageAccountName')]"
      }
  }
}
```

Salve o arquivo localmente como `TemplateTest.json`.

## <a name="save-the-resource-manager-template-in-azure-storage"></a>Salve o modelo do Resource Manager no Armazenamento do Azure

Agora podemos usar o PowerShell para criar um compartilhamento de arquivos do Armazenamento do Azure e carregar o arquivo `TemplateTest.json`.
Para obter instruções sobre como criar um compartilhamento de arquivos e carregar um arquivo no portal do Azure, consulte [Introdução ao armazenamento de arquivos do Azure no Windows](../storage/files/storage-dotnet-how-to-use-files.md).

Inicie o PowerShell no computador local e execute os seguintes comandos para criar um compartilhamento de arquivos e carregar o modelo do Resource Manager para o compartilhamento de arquivos.

```powershell
# Login to Azure
Connect-AzureRmAccount

# Get the access key for your storage account
$key = Get-AzureRmStorageAccountKey -ResourceGroupName 'MyAzureAccount' -Name 'MyStorageAccount'

# Create an Azure Storage context using the first access key
$context = New-AzureStorageContext -StorageAccountName 'MyStorageAccount' -StorageAccountKey $key[0].value

# Create a file share named 'resource-templates' in your Azure Storage account
$fileShare = New-AzureStorageShare -Name 'resource-templates' -Context $context

# Add the TemplateTest.json file to the new file share
# "TemplatePath" is the path where you saved the TemplateTest.json file
$templateFile = 'C:\TemplatePath'
Set-AzureStorageFileContent -ShareName $fileShare.Name -Context $context -Source $templateFile
```

## <a name="create-the-powershell-runbook-script"></a>Criar o script de runbook do PowerShell

Agora podemos criar um script do PowerShell que obtém o arquivo `TemplateTest.json` do Armazenamento do Azure e implanta o modelo para criar uma nova conta de Armazenamento do Azure.

Em um editor de texto, cole o seguinte texto:

```powershell
param (
    [Parameter(Mandatory=$true)]
    [string]
    $ResourceGroupName,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageAccountName,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageAccountKey,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageFileName
)



# Authenticate to Azure if running from Azure Automation
$ServicePrincipalConnection = Get-AutomationConnection -Name "AzureRunAsConnection"
Connect-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint | Write-Verbose

#Set the parameter values for the Resource Manager template
$Parameters = @{
    "storageAccountType"="Standard_LRS"
    }

# Create a new context
$Context = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

Get-AzureStorageFileContent -ShareName 'resource-templates' -Context $Context -path 'TemplateTest.json' -Destination 'C:\Temp'

$TemplateFile = Join-Path -Path 'C:\Temp' -ChildPath $StorageFileName

# Deploy the storage account
New-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile $TemplateFile -TemplateParameterObject $Parameters 
``` 

Salve o arquivo localmente como `DeployTemplate.ps1`.

## <a name="import-and-publish-the-runbook-into-your-azure-automation-account"></a>Importar e publicar o runbook em sua conta de Automação do Azure

Agora podemos usar o PowerShell para importar o runbook para sua conta de Automação do Azure e, em seguida, publicar o runbook.
Para obter informações sobre como importar e publicar um runbook no portal do Azure, confira [Gerenciar runbooks na Automação do Azure](manage-runbooks.md).

Para importar `DeployTemplate.ps1` para sua conta de Automação como runbook do PowerShell, execute os seguintes comandos do PowerShell:

```powershell
# MyPath is the path where you saved DeployTemplate.ps1
# MyResourceGroup is the name of the Azure ResourceGroup that contains your Azure Automation account
# MyAutomationAccount is the name of your Automation account
$importParams = @{
    Path = 'C:\MyPath\DeployTemplate.ps1'
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Type = 'PowerShell'
}
Import-AzureRmAutomationRunbook @importParams

# Publish the runbook
$publishParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
}
Publish-AzureRmAutomationRunbook @publishParams
```

## <a name="start-the-runbook"></a>Iniciar o runbook

Agora vamos iniciar o runbook chamando o cmdlet [AzureRmAutomationRunbook início](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook).

Para obter informações sobre como iniciar um runbook no portal do Azure, consulte [Iniciar um runbook na Automação do Azure](automation-starting-a-runbook.md).

Execute os seguintes comandos no console do PowerShell:

```powershell
# Set up the parameters for the runbook
$runbookParams = @{
    ResourceGroupName = 'MyResourceGroup'
    StorageAccountName = 'MyStorageAccount'
    StorageAccountKey = $key[0].Value # We got this key earlier
    StorageFileName = 'TemplateTest.json' 
}

# Set up parameters for the Start-AzureRmAutomationRunbook cmdlet
$startParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
    Parameters = $runbookParams
}

# Start the runbook
$job = Start-AzureRmAutomationRunbook @startParams
```

O runbook é executado e você pode verificar o status executando `$job.Status`.

O runbook obtém o modelo do Resource Manager e o utiliza para implantar uma nova conta de Armazenamento do Azure.
Você pode ver que a nova conta de armazenamento foi criada executando o seguinte comando:
```powershell
Get-AzureRmStorageAccount
```

## <a name="summary"></a>Resumo

É isso! Agora você pode usar modelos de Resource Manager, Automação do Azure e Armazenamento do Azure para implantar todos os seus recursos do Azure.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre os modelos do Resource Manager, confira [Visão geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)
* Para começar a usar o Armazenamento do Azure, consulte [Introdução ao Armazenamento do Azure](../storage/common/storage-introduction.md).
* Para localizar outros runbooks úteis de Automação do Azure, consulte [Galerias de runbooks e módulos para Automação do Azure](automation-runbook-gallery.md).
* Para encontrar outros modelos úteis do Resource Manager: consulte [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/)


