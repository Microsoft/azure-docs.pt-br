---
title: Criar uma imagem personalizada do arquivo VHD usando Azure PowerShell
description: Automatizar a criação de uma imagem personalizada no Azure DevTest Labs de um arquivo VHD usando o PowerShell
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 4b0712fdbec1ce23ad9e09d972e425cb7941107b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87288977"
---
# <a name="create-a-custom-image-from-a-vhd-file-using-powershell"></a>Criar uma imagem personalizada de um arquivo VHD usando o PowerShell

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="step-by-step-instructions"></a>Instruções passo a passo

As seguintes etapas orientam você durante a criação de uma imagem personalizada de um arquivo VHD usando o PowerShell:

1. Em um prompt do PowerShell, faça logon em sua conta do Azure com a seguinte chamada para o cmdlet **Connect-AzAccount** .

    ```powershell
    Connect-AzAccount
    ```

1.  Selecione a assinatura do Azure desejada chamando o cmdlet **Select-AzSubscription** . Substitua o espaço reservado a seguir para a variável **$subscriptionId** por uma ID de assinatura válida do Azure.

    ```powershell
    $subscriptionId = '<Specify your subscription ID here>'
    Select-AzSubscription -SubscriptionId $subscriptionId
    ```

1.  Obtenha o objeto de laboratório chamando o cmdlet **Get-AzResource** . Substitua os espaços reservados a seguir para as variáveis **$labRg** e **$labName** pelos valores apropriados para seu ambiente.

    ```powershell
    $labRg = '<Specify your lab resource group name here>'
    $labName = '<Specify your lab name here>'
    $lab = Get-AzResource -ResourceId ('/subscriptions/' + $subscriptionId + '/resourceGroups/' + $labRg + '/providers/Microsoft.DevTestLab/labs/' + $labName)
    ```

1.  Substitua o espaço reservado a seguir da variável **$vhdUri** pelo URI para seu arquivo VHD carregado. Você pode obter o URI do arquivo VHD na folha de blob da conta de armazenamento no Portal do Azure.

    ```powershell
    $vhdUri = '<Specify the VHD URI here>'
    ```

1.  Crie a imagem personalizada usando o cmdlet **New-AzResourceGroupDeployment** . Substitua os espaços reservados a seguir para as variáveis **$customImageName** e **$customImageDescription** por nomes significativos para seu ambiente.

    ```powershell
    $customImageName = '<Specify the custom image name>'
    $customImageDescription = '<Specify the custom image description>'

    $parameters = @{existingLabName="$($lab.Name)"; existingVhdUri=$vhdUri; imageOsType='windows'; isVhdSysPrepped=$false; imageName=$customImageName; imageDescription=$customImageDescription}

    New-AzResourceGroupDeployment -ResourceGroupName $lab.ResourceGroupName -Name CreateCustomImage -TemplateUri 'https://raw.githubusercontent.com/Azure/azure-devtestlab/master/samples/DevTestLabs/QuickStartTemplates/201-dtl-create-customimage-from-vhd/azuredeploy.json' -TemplateParameterObject $parameters
    ```

## <a name="powershell-script-to-create-a-custom-image-from-a-vhd-file"></a>Script do PowerShell para criar uma imagem personalizada de um arquivo VHD

O script do PowerShell a seguir pode ser usado para criar uma imagem personalizada de um arquivo VHD. Substitua os espaços reservados (que começam e terminam com colchetes angulares) pelos valores apropriados às suas necessidades.

```powershell
# Log in to your Azure account.
Connect-AzAccount

# Select the desired Azure subscription.
$subscriptionId = '<Specify your subscription ID here>'
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab object.
$labRg = '<Specify your lab resource group name here>'
$labName = '<Specify your lab name here>'
$lab = Get-AzResource -ResourceId ('/subscriptions/' + $subscriptionId + '/resourceGroups/' + $labRg + '/providers/Microsoft.DevTestLab/labs/' + $labName)

# Set the URI of the VHD file.
$vhdUri = '<Specify the VHD URI here>'

# Set the custom image name and description values.
$customImageName = '<Specify the custom image name>'
$customImageDescription = '<Specify the custom image description>'

# Set up the parameters object.
$parameters = @{existingLabName="$($lab.Name)"; existingVhdUri=$vhdUri; imageOsType='windows'; isVhdSysPrepped=$false; imageName=$customImageName; imageDescription=$customImageDescription}

# Create the custom image.
New-AzResourceGroupDeployment -ResourceGroupName $lab.ResourceGroupName -Name CreateCustomImage -TemplateUri 'https://raw.githubusercontent.com/Azure/azure-devtestlab/master/samples/DevTestLabs/QuickStartTemplates/201-dtl-create-customimage-from-vhd/azuredeploy.json' -TemplateParameterObject $parameters
```

## <a name="related-blog-posts"></a>Postagens de blogs relacionadas

- [Imagens personalizadas ou fórmulas?](./devtest-lab-faq.md#blog-post)
- [Copiar imagens personalizadas entre Azure DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Próximas etapas

- [Adicionar uma VM ao laboratório](devtest-lab-add-vm.md)
