---
title: "Implantar um aplicativo em um conjunto de dimensionamento de máquinas virtuais do Azure | Microsoft Docs"
description: "Saiba como implantar um aplicativo de dimensionamento automático simples em um conjunto de dimensionamento de máquinas virtuais usando um modelo do Azure Resource Manager."
services: virtual-machine-scale-sets
documentationcenter: 
author: rwike77
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/24/2017
ms.author: ryanwi
ms.openlocfilehash: 07883a33382cc660b043c99872312a9e77228253
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-an-autoscaling-app-using-a-template"></a>Implantar um aplicativo de dimensionamento automático usando um modelo

Os [modelos do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment) são uma excelente maneira de implantar grupos de recursos relacionados. Esse tutorial se baseia em [Implantar um conjunto de dimensionamento simples](virtual-machine-scale-sets-mvss-start.md) e descreve como implantar um aplicativo de dimensionamento automático simples em um conjunto de dimensionamento usando um modelo do Azure Resource Manager.  Também é possível configurar o dimensionamento automático usando o PowerShell, a CLI ou o portal. Para obter mais informações, consulte [Visão geral do dimensionamento automático](virtual-machine-scale-sets-autoscale-overview.md).

## <a name="two-quickstart-templates"></a>Dois modelos de início rápido
Quando você implanta um conjunto de dimensionamento, você pode instalar um novo software em uma imagem de plataforma usando uma [Extensão de VM](../virtual-machines/virtual-machines-windows-extensions-features.md). Uma extensão de VM do Azure é um pequeno aplicativo que fornece tarefas de configuração e automação pós-implantação nas máquinas virtuais do Azure, tais como implantar um aplicativo. Dois modelos diferentes de exemplo são fornecidos em [Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates), que mostram como implantar um aplicativo de dimensionamento automático em um conjunto de dimensionamento usando extensões de VM.

### <a name="python-http-server-on-linux"></a>Servidor HTTP do Python em Linux
O modelo de exemplo do [servidor HTTP do Python em Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale) implanta um aplicativo de dimensionamento automático simples em execução em um conjunto de dimensionamento do Linux.  O [Bottle](http://bottlepy.org/docs/dev/), uma estrutura da Web Python e um servidor HTTP simples são implantados em cada VM no conjunto de dimensionamento usando uma extensão de VM de script personalizado. O conjunto de dimensionamento escala verticalmente quando a utilização média da CPU em todas as VMs é maior que 60% e reduz verticalmente quando a utilização média da CPU é menor que 30%.

Além do recurso de conjunto de dimensionamento, o modelo de exemplo *azuredeploy.json* também declara endereço IP público, rede virtual, balanceador de carga e recursos de configurações de dimensionamento automático.  Para obter mais informações sobre como criar esses recursos em um modelo, consulte [Conjunto de dimensionamento Linux com dimensionamento automático](virtual-machine-scale-sets-linux-autoscale.md).

No modelo *azuredeploy.json*, a propriedade `extensionProfile` do recurso `Microsoft.Compute/virtualMachineScaleSets` especifica uma extensão de script personalizado. `fileUris` especifica o local dos scripts. Nesse caso, são dois arquivos: *workserver.py*, que define um servidor HTTP simples e *installserver.sh*, que instala o Bottle e inicia o servidor HTTP. `commandToExecute` especifica o comando a ser executado depois que o conjunto de dimensionamento tiver sido implantado.

```json
          "extensionProfile": {
            "extensions": [
              {
                "name": "lapextension",
                "properties": {
                  "publisher": "Microsoft.Azure.Extensions",
                  "type": "CustomScript",
                  "typeHandlerVersion": "2.0",
                  "autoUpgradeMinorVersion": true,
                  "settings": {
                    "fileUris": [
                      "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/installserver.sh",
                      "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/workserver.py"
                    ],
                    "commandToExecute": "bash installserver.sh"
                  }
                }
              }
            ]
          }
```

### <a name="aspnet-mvc-application-on-windows"></a>Aplicativo ASP.NET MVC no Windows
O modelo de exemplo do [aplicativo ASP.NET MVC no Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale) implanta um aplicativo simples do ASP.NET MVC em execução no IIS no conjunto de dimensionamento do Windows.  O IIS e o aplicativo MVC são implantados usando a extensão de VM de [DSC (configuração de estado desejado) do PowerShell](virtual-machine-scale-sets-dsc.md).  O conjunto de dimensionamento escala verticalmente (uma instância VM por vez) quando a utilização de CPU é maior que 50% por 5 minutos. 

Além do recurso de conjunto de dimensionamento, o modelo de exemplo *azuredeploy.json* também declara endereço IP público, rede virtual, balanceador de carga e recursos de configurações de dimensionamento automático. Esse modelo também demonstra a atualização de aplicativo.  Para obter mais informações sobre como criar esses recursos em um modelo, consulte [Conjunto de dimensionamento Windows com dimensionamento automático](virtual-machine-scale-sets-windows-autoscale.md).

No modelo *azuredeploy.json*, a propriedade `extensionProfile` do recurso `Microsoft.Compute/virtualMachineScaleSets` especifica uma extensão de [DSC (configuração de estado desejado)](virtual-machine-scale-sets-dsc.md) que instala o IIS e um aplicativo Web padrão de um pacote WebDeploy.  O script *IISInstall.ps1* instala o IIS na máquina virtual e é encontrado na pasta *DSC*.  O aplicativo Web MVC é encontrado na pasta *WebDeploy*.  Os caminhos para o script de instalação e o aplicativo Web são definidos nos parâmetros `powershelldscZip` e `webDeployPackage` do arquivo *azuredeploy.parameters.json*. 

```json
          "extensionProfile": {
            "extensions": [
              {
                "name": "Microsoft.Powershell.DSC",
                "properties": {
                  "publisher": "Microsoft.Powershell",
                  "type": "DSC",
                  "typeHandlerVersion": "2.9",
                  "autoUpgradeMinorVersion": true,
                  "forceUpdateTag": "[parameters('powershelldscUpdateTagVersion')]",
                  "settings": {
                    "configuration": {
                      "url": "[variables('powershelldscZipFullPath')]",
                      "script": "IISInstall.ps1",
                      "function": "InstallIIS"
                    },
                    "configurationArguments": {
                      "nodeName": "localhost",
                      "WebDeployPackagePath": "[variables('webDeployPackageFullPath')]"
                    }
                  }
                }
              }
            ]
          }
```

## <a name="deploy-the-template"></a>Implantar o modelo
A maneira mais simples de implantar o modelo [Servidor HTTP do Python em Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale) ou [Aplicativo ASP.NET MVC no Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale) é usar o botão **Implantar no Azure** encontrado na nos arquivos Leiame no GitHub.  Você também pode usar o PowerShell ou a CLI do Azure para implantar os modelos de exemplo.

### <a name="powershell"></a>PowerShell
Copie os arquivos do [Servidor HTTP do Python em Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale) ou [Aplicativo ASP.NET MVC no Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale) do repositório do GitHub para uma pasta no computador local.  Abra o arquivo *azuredeploy.parameters.json* e atualize os valores padrão dos parâmetros `vmssName`, `adminUsername` e `adminPassword`. Salve o script do PowerShell a seguir em *deploy. ps1* na mesma pasta do modelo *azuredeploy.json*. Para implantar o modelo de exemplo, execute o script *deploy.ps1* de uma janela de comando do PowerShell.

```powershell
param(
 [Parameter(Mandatory=$True)]
 [string]
 $subscriptionId,

 [Parameter(Mandatory=$True)]
 [string]
 $resourceGroupName,

 [string]
 $resourceGroupLocation,

 [Parameter(Mandatory=$True)]
 [string]
 $deploymentName,

 [string]
 $templateFilePath = "template.json",

 [string]
 $parametersFilePath = "parameters.json"
)

<#
.SYNOPSIS
    Registers RPs
#>
Function RegisterRP {
    Param(
        [string]$ResourceProviderNamespace
    )

    Write-Host "Registering resource provider '$ResourceProviderNamespace'";
    Register-AzureRmResourceProvider -ProviderNamespace $ResourceProviderNamespace;
}

#******************************************************************************
# Script body
# Execution begins here
#******************************************************************************
$ErrorActionPreference = "Stop"

# sign in
Write-Host "Logging in...";
Login-AzureRmAccount;

# select subscription
Write-Host "Selecting subscription '$subscriptionId'";
Select-AzureRmSubscription -SubscriptionID $subscriptionId;

# Register RPs
$resourceProviders = @("microsoft.compute","microsoft.insights","microsoft.network");
if($resourceProviders.length) {
    Write-Host "Registering resource providers"
    foreach($resourceProvider in $resourceProviders) {
        RegisterRP($resourceProvider);
    }
}

#Create or check for existing resource group
$resourceGroup = Get-AzureRmResourceGroup -Name $resourceGroupName -ErrorAction SilentlyContinue
if(!$resourceGroup)
{
    Write-Host "Resource group '$resourceGroupName' does not exist. To create a new resource group, please enter a location.";
    if(!$resourceGroupLocation) {
        $resourceGroupLocation = Read-Host "resourceGroupLocation";
    }
    Write-Host "Creating resource group '$resourceGroupName' in location '$resourceGroupLocation'";
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
}
else{
    Write-Host "Using existing resource group '$resourceGroupName'";
}

# Start the deployment
Write-Host "Starting deployment...";
if(Test-Path $parametersFilePath) {
    New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath -TemplateParameterFile $parametersFilePath;
} else {
    New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath;
}
```

### <a name="azure-cli"></a>CLI do Azure
```azurecli
#!/bin/bash
set -euo pipefail
IFS=$'\n\t'

# -e: immediately exit if any command has a non-zero exit status
# -o: prevents errors in a pipeline from being masked
# IFS new value is less likely to cause confusing bugs when looping arrays or arguments (e.g. $@)

usage() { echo "Usage: $0 -i <subscriptionId> -g <resourceGroupName> -n <deploymentName> -l <resourceGroupLocation>" 1>&2; exit 1; }

declare subscriptionId=""
declare resourceGroupName=""
declare deploymentName=""
declare resourceGroupLocation=""

# Initialize parameters specified from command line
while getopts ":i:g:n:l:" arg; do
    case "${arg}" in
        i)
            subscriptionId=${OPTARG}
            ;;
        g)
            resourceGroupName=${OPTARG}
            ;;
        n)
            deploymentName=${OPTARG}
            ;;
        l)
            resourceGroupLocation=${OPTARG}
            ;;
        esac
done
shift $((OPTIND-1))

#Prompt for parameters is some required parameters are missing
if [[ -z "$subscriptionId" ]]; then
    echo "Subscription Id:"
    read subscriptionId
    [[ "${subscriptionId:?}" ]]
fi

if [[ -z "$resourceGroupName" ]]; then
    echo "ResourceGroupName:"
    read resourceGroupName
    [[ "${resourceGroupName:?}" ]]
fi

if [[ -z "$deploymentName" ]]; then
    echo "DeploymentName:"
    read deploymentName
fi

if [[ -z "$resourceGroupLocation" ]]; then
    echo "Enter a location below to create a new resource group else skip this"
    echo "ResourceGroupLocation:"
    read resourceGroupLocation
fi

#templateFile Path - template file to be used
templateFilePath="template.json"

if [ ! -f "$templateFilePath" ]; then
    echo "$templateFilePath not found"
    exit 1
fi

#parameter file path
parametersFilePath="parameters.json"

if [ ! -f "$parametersFilePath" ]; then
    echo "$parametersFilePath not found"
    exit 1
fi

if [ -z "$subscriptionId" ] || [ -z "$resourceGroupName" ] || [ -z "$deploymentName" ]; then
    echo "Either one of subscriptionId, resourceGroupName, deploymentName is empty"
    usage
fi

#login to azure using your credentials
az account show 1> /dev/null

if [ $? != 0 ];
then
    az login
fi

#set the default subscription id
az account set --name $subscriptionId

set +e

#Check for existing RG
az group show $resourceGroupName 1> /dev/null

if [ $? != 0 ]; then
    echo "Resource group with name" $resourceGroupName "could not be found. Creating new resource group.."
    set -e
    (
        set -x
        az group create --name $resourceGroupName --location $resourceGroupLocation 1> /dev/null
    )
    else
    echo "Using existing resource group..."
fi

#Start deployment
echo "Starting deployment..."
(
    set -x
    az group deployment create --name $deploymentName --resource-group $resourceGroupName --template-file $templateFilePath --parameters $parametersFilePath
)

if [ $?  == 0 ];
 then
    echo "Template has been successfully deployed"
fi
```

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
