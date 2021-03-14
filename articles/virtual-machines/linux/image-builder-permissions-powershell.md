---
title: Configurar permissões de serviço do construtor de imagem do Azure usando o PowerShell
description: Configurar requisitos para o serviço do construtor de imagem de VM do Azure, incluindo permissões e privilégios usando o PowerShell
author: danielsollondon
ms.author: danis
ms.date: 03/05/2021
ms.topic: article
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.openlocfilehash: 9f8793b6ea0ba454b66c525c2d53c1de2197d539
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102440200"
---
# <a name="configure-azure-image-builder-service-permissions-using-powershell"></a>Configurar permissões de serviço do construtor de imagem do Azure usando o PowerShell

Quando você se registra no (AIB), o serviço AIB recebe permissão para criar, gerenciar e excluir um grupo de recursos de preparo (IT_*), bem como direitos para adicionar a ele recursos necessários para o build da imagem. Para que isso ocorra, um SPN (nome da entidade de serviço) do AIB é disponibilizado em sua assinatura durante um registro bem-sucedido.

Para permitir que o Construtor de Imagens de VM do Azure distribua imagens para as imagens gerenciadas ou para uma Galeria de Imagens Compartilhadas, você precisará criar uma identidade atribuída pelo usuário do Azure que tenha permissões para ler e gravar imagens. Se você estiver acessando o armazenamento do Azure, isso precisará de permissões para ler contêineres privados ou públicos.

Você deve configurar permissões e privilégios antes de criar uma imagem. As seções a seguir detalham como configurar possíveis cenários usando o PowerShell.

> [!IMPORTANT]
> O Construtor de Imagens do Azure está atualmente em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="register-the-features"></a>Registrar os recursos

Primeiro, você deve se registrar para o serviço do construtor de imagem do Azure. O registro concede a permissão de serviço para criar, gerenciar e excluir um grupo de recursos de preparo. O serviço também tem direitos para adicionar recursos do grupo que são necessários para a compilação da imagem.

```powershell-interactive
Register-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages
```

## <a name="create-an-azure-user-assigned-managed-identity"></a>Criar uma identidade gerenciada atribuída pelo usuário do Azure

O construtor de imagens do Azure exige que você crie uma [identidade gerenciada atribuída pelo usuário do Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md). O construtor de imagens do Azure usa a identidade gerenciada atribuída pelo usuário para ler imagens, gravar imagens e acessar contas de armazenamento do Azure. Você concede a permissão de identidade para realizar ações específicas em sua assinatura.

> [!NOTE]
> Anteriormente, o construtor de imagem do Azure usava o SPN (nome da entidade de serviço) do Azure Image Builder para conceder permissões aos grupos de recursos de imagem. Usar o SPN será preterido. Em vez disso, use uma identidade gerenciada atribuída pelo usuário.

O exemplo a seguir mostra como criar uma identidade gerenciada atribuída pelo usuário do Azure. Substitua as configurações de espaço reservado para definir suas variáveis.

| Configuração | Descrição |
|---------|-------------|
| \<Resource group\> | Grupo de recursos onde criar a identidade gerenciada atribuída pelo usuário. |

```powershell-interactive
## Add AZ PS module to support AzUserAssignedIdentity
Install-Module -Name Az.ManagedServiceIdentity

$parameters = @{
    Name = 'aibIdentity'
    ResourceGroupName = '<Resource group>'
}
# create identity
New-AzUserAssignedIdentity @parameters
```

Para obter mais informações sobre identidades atribuídas ao usuário do Azure, consulte a documentação de [identidade gerenciada atribuída pelo usuário do Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) sobre como criar uma identidade.

## <a name="allow-image-builder-to-distribute-images"></a>Permitir que o construtor de imagens distribua imagens

Para o construtor de imagens do Azure distribuir imagens (imagens gerenciadas/Galeria de imagens compartilhadas), o serviço do construtor de imagens do Azure deve ter permissão para injetar as imagens nesses grupos de recursos. Para conceder as permissões necessárias, você precisa criar uma identidade gerenciada atribuída pelo usuário e conceder direitos de ti no grupo de recursos em que a imagem é criada. O construtor de imagens do Azure **não** tem permissão para acessar recursos em outros grupos de recursos na assinatura. Você precisa tomar ações explícitas para permitir o acesso para evitar falhas em suas compilações.

Você não precisa conceder aos direitos de colaborador de identidade gerenciada atribuídos pelo usuário no grupo de recursos para distribuir imagens. No entanto, a identidade gerenciada atribuída pelo usuário precisa das seguintes permissões do Azure `Actions` no grupo de recursos de distribuição:

```Actions
Microsoft.Compute/images/write
Microsoft.Compute/images/read
Microsoft.Compute/images/delete
```

Se estiver distribuindo para uma galeria de imagens compartilhada, você também precisará de:

```Actions
Microsoft.Compute/galleries/read
Microsoft.Compute/galleries/images/read
Microsoft.Compute/galleries/images/versions/read
Microsoft.Compute/galleries/images/versions/write
```

## <a name="permission-to-customize-existing-images"></a>Permissão para personalizar as imagens existentes

Para o construtor de imagens do Azure criar imagens de imagens personalizadas de origem (imagens gerenciadas/Galeria de imagens compartilhadas), o serviço do construtor de imagens do Azure deve ter permissão para ler as imagens nesses grupos de recursos. Para conceder as permissões necessárias, você precisa criar uma identidade gerenciada atribuída pelo usuário e conceder direitos de ti no grupo de recursos onde a imagem está localizada.

Criar com base em uma imagem personalizada existente:

```Actions
Microsoft.Compute/galleries/read
```

Crie a partir de uma versão existente da Galeria de imagens compartilhadas:

```Actions
Microsoft.Compute/galleries/read
Microsoft.Compute/galleries/images/read
Microsoft.Compute/galleries/images/versions/read
```

## <a name="permission-to-customize-images-on-your-vnets"></a>Permissão para personalizar imagens em seu VNETs

O construtor de imagens do Azure tem a capacidade de implantar e usar uma VNET existente em sua assinatura, permitindo assim que as personalizações acessem recursos conectados.

Você não precisa conceder aos direitos de colaborador de identidade gerenciada atribuídos pelo usuário no grupo de recursos para implantar uma VM em uma VNET existente. No entanto, a identidade gerenciada atribuída pelo usuário precisa das seguintes permissões do Azure `Actions` no grupo de recursos de VNET:

```Actions
Microsoft.Network/virtualNetworks/read
Microsoft.Network/virtualNetworks/subnets/join/action
```

## <a name="create-an-azure-role-definition"></a>Criar uma definição de função do Azure

Os exemplos a seguir criam uma definição de função do Azure das ações descritas nas seções anteriores. Os exemplos são aplicados no nível do grupo de recursos. Avalie e teste se os exemplos são granulares o suficiente para suas necessidades. Para seu cenário, talvez seja necessário refiná-lo para uma galeria de imagens compartilhada específica.

As ações de imagem permitem leitura e gravação. Decida o que é apropriado para o seu ambiente. Por exemplo, crie uma função para permitir que o construtor de imagens do Azure Leia imagens do grupo de recursos *example-RG-1* e grave imagens no grupo de recursos *example-RG-2*.

### <a name="custom-image-azure-role-example"></a>Exemplo de função do Azure de imagem personalizada

O exemplo a seguir cria uma função do Azure para usar e distribuir uma imagem personalizada de origem. Em seguida, conceda a função personalizada à identidade gerenciada atribuída pelo usuário para o construtor de imagens do Azure.

Para simplificar a substituição de valores no exemplo, primeiro defina as variáveis a seguir. Substitua as configurações de espaço reservado para definir suas variáveis.

| Configuração | Descrição |
|---------|-------------|
| \<Subscription ID\> | ID da assinatura do Azure |
| \<Resource group\> | Grupo de recursos para imagem personalizada |

```powershell-interactive
$sub_id = "<Subscription ID>"
# Resource group - For Preview, image builder will only support creating custom images in the same Resource Group as the source managed image.
$imageResourceGroup = "<Resource group>"
$identityName = "aibIdentity"

# Use a web request to download the sample JSON description
$sample_uri="https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json"
$role_definition="aibRoleImageCreation.json"

Invoke-WebRequest -Uri $sample_uri -Outfile $role_definition -UseBasicParsing

# Create a unique role name to avoid clashes in the same Azure Active Directory domain
$timeInt=$(get-date -UFormat "%s")
$imageRoleDefName="Azure Image Builder Image Def"+$timeInt

# Update the JSON definition placeholders with variable values
((Get-Content -path $role_definition -Raw) -replace '<subscriptionID>',$sub_id) | Set-Content -Path $role_definition
((Get-Content -path $role_definition -Raw) -replace '<rgName>', $imageResourceGroup) | Set-Content -Path $role_definition
((Get-Content -path $role_definition -Raw) -replace 'Azure Image Builder Service Image Creation Role', $imageRoleDefName) | Set-Content -Path $role_definition

# Create a custom role from the aibRoleImageCreation.json description file. 
New-AzRoleDefinition -InputFile $role_definition

# Get the user-identity properties
$identityNameResourceId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).Id
$identityNamePrincipalId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).PrincipalId

# Grant the custom role to the user-assigned managed identity for Azure Image Builder.
$parameters = @{
    ObjectId = $identityNamePrincipalId
    RoleDefinitionName = $imageRoleDefName
    Scope = '/subscriptions/' + $sub_id + '/resourceGroups/' + $imageResourceGroup
}

New-AzRoleAssignment @parameters
```

### <a name="existing-vnet-azure-role-example"></a>Exemplo de função VNET do Azure existente

O exemplo a seguir cria uma função do Azure para usar e distribuir uma imagem de VNET existente. Em seguida, conceda a função personalizada à identidade gerenciada atribuída pelo usuário para o construtor de imagens do Azure.

Para simplificar a substituição de valores no exemplo, primeiro defina as variáveis a seguir. Substitua as configurações de espaço reservado para definir suas variáveis.

| Configuração | Descrição |
|---------|-------------|
| \<Subscription ID\> | ID da assinatura do Azure |
| \<Resource group\> | Grupo de recursos de VNET |

```powershell-interactive
$sub_id = "<Subscription ID>"
$res_group = "<Resource group>"
$identityName = "aibIdentity"

# Use a web request to download the sample JSON description
$sample_uri="https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleNetworking.json"
$role_definition="aibRoleNetworking.json"

Invoke-WebRequest -Uri $sample_uri -Outfile $role_definition -UseBasicParsing

# Create a unique role name to avoid clashes in the same AAD domain
$timeInt=$(get-date -UFormat "%s")
$networkRoleDefName="Azure Image Builder Network Def"+$timeInt

# Update the JSON definition placeholders with variable values
((Get-Content -path $role_definition -Raw) -replace '<subscriptionID>',$sub_id) | Set-Content -Path $role_definition
((Get-Content -path $role_definition -Raw) -replace '<vnetRgName>', $res_group) | Set-Content -Path $role_definition
((Get-Content -path $role_definition -Raw) -replace 'Azure Image Builder Service Networking Role',$networkRoleDefName) | Set-Content -Path $role_definition

# Create a custom role from the aibRoleNetworking.json description file
New-AzRoleDefinition -InputFile $role_definition

# Get the user-identity properties
$identityNameResourceId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).Id
$identityNamePrincipalId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).PrincipalId

# Assign the custom role to the user-assigned managed identity for Azure Image Builder
$parameters = @{
    ObjectId = $identityNamePrincipalId
    RoleDefinitionName = $networkRoleDefName
    Scope = '/subscriptions/' + $sub_id + '/resourceGroups/' + $res_group
}

New-AzRoleAssignment @parameters
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte [visão geral do construtor de imagens do Azure](../image-builder-overview.md).
