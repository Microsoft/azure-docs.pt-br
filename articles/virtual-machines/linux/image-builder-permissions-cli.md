---
title: Configurar permissões de serviço do construtor de imagem do Azure usando CLI do Azure
description: Configurar requisitos para o serviço do construtor de imagem de VM do Azure, incluindo permissões e privilégios usando CLI do Azure
author: cynthn
ms.author: danis
ms.date: 04/02/2021
ms.topic: article
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.openlocfilehash: 4b6154a18cf4e08bf59dad91350160a1f83c49ed
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102201474"
---
# <a name="configure-azure-image-builder-service-permissions-using-azure-cli"></a>Configurar permissões de serviço do construtor de imagem do Azure usando CLI do Azure

Quando você se registra no (AIB), o serviço AIB recebe permissão para criar, gerenciar e excluir um grupo de recursos de preparo (IT_*), bem como direitos para adicionar a ele recursos necessários para o build da imagem. Para que isso ocorra, um SPN (nome da entidade de serviço) do AIB é disponibilizado em sua assinatura durante um registro bem-sucedido.

Para permitir que o Construtor de Imagens de VM do Azure distribua imagens para as imagens gerenciadas ou para uma Galeria de Imagens Compartilhadas, você precisará criar uma identidade atribuída pelo usuário do Azure que tenha permissões para ler e gravar imagens. Se você estiver acessando o armazenamento do Azure, isso precisará de permissões para ler contêineres privados ou públicos.

Você deve configurar permissões e privilégios antes de criar uma imagem. As seções a seguir detalham como configurar possíveis cenários usando CLI do Azure.

> [!IMPORTANT]
> O Construtor de Imagens do Azure está atualmente em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="register-the-features"></a>Registrar os recursos

Primeiro, você deve se registrar para o serviço do construtor de imagem do Azure. O registro concede a permissão de serviço para criar, gerenciar e excluir um grupo de recursos de preparo. O serviço também tem direitos para adicionar recursos do grupo que são necessários para a compilação da imagem.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

## <a name="create-an-azure-user-assigned-managed-identity"></a>Criar uma identidade gerenciada atribuída pelo usuário do Azure

O construtor de imagens do Azure exige que você crie uma [identidade gerenciada atribuída pelo usuário do Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md). O construtor de imagens do Azure usa a identidade gerenciada atribuída pelo usuário para ler imagens, gravar imagens e acessar contas de armazenamento do Azure. Você concede a permissão de identidade para realizar ações específicas em sua assinatura.

> [!NOTE]
> Anteriormente, o construtor de imagem do Azure usava o SPN (nome da entidade de serviço) do Azure Image Builder para conceder permissões aos grupos de recursos de imagem. Usar o SPN será preterido. Em vez disso, use uma identidade gerenciada atribuída pelo usuário.

O exemplo a seguir mostra como criar uma identidade gerenciada atribuída pelo usuário do Azure. Substitua as configurações de espaço reservado para definir suas variáveis.

| Configuração | Descrição |
|---------|-------------|
| \<Resource group\> | Grupo de recursos onde criar a identidade gerenciada atribuída pelo usuário. |

```azurecli-interactive
identityName="aibIdentity"
imageResourceGroup=<Resource group>

az identity create \
    --resource-group $imageResourceGroup \
    --name $identityName
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

```azurecli-interactive
# Subscription ID - You can get this using `az account show | grep id` or from the Azure portal.
subscriptionID=<Subscription ID>
# Resource group - For Preview, image builder will only support creating custom images in the same Resource Group as the source managed image.
imageResourceGroup=<Resource group>
identityName="aibIdentity"

# Use *cURL* to download the a sample JSON description 
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

# Create a unique role name to avoid clashes in the same Azure Active Directory domain
imageRoleDefName="Azure Image Builder Image Def"$(date +'%s')

# Update the JSON definition using stream editor
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" aibRoleImageCreation.json
sed -i -e "s/Azure Image Builder Service Image Creation Role/$imageRoleDefName/g" aibRoleImageCreation.json

# Create a custom role from the sample aibRoleImageCreation.json description file.
az role definition create --role-definition ./aibRoleImageCreation.json

# Get the user-assigned managed identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $identityName | grep "clientId" | cut -c16- | tr -d '",')

# Grant the custom role to the user-assigned managed identity for Azure Image Builder.
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $imageRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

### <a name="existing-vnet-azure-role-example"></a>Exemplo de função VNET do Azure existente

O exemplo a seguir cria uma função do Azure para usar e distribuir uma imagem de VNET existente. Em seguida, conceda a função personalizada à identidade gerenciada atribuída pelo usuário para o construtor de imagens do Azure.

Para simplificar a substituição de valores no exemplo, primeiro defina as variáveis a seguir. Substitua as configurações de espaço reservado para definir suas variáveis.

| Configuração | Descrição |
|---------|-------------|
| \<Subscription ID\> | ID da assinatura do Azure |
| \<Resource group\> | Grupo de recursos de VNET |

```azurecli-interactive
# Subscription ID - You can get this using `az account show | grep id` or from the Azure portal.
subscriptionID=<Subscription ID>
VnetResourceGroup=<Resource group>
identityName="aibIdentity"

# Use *cURL* to download the a sample JSON description 
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleNetworking.json -o aibRoleNetworking.json

# Create a unique role name to avoid clashes in the same domain
netRoleDefName="Azure Image Builder Network Def"$(date +'%s')

# Update the JSON definition using stream editor
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleNetworking.json
sed -i -e "s/<vnetRgName>/$vnetRgName/g" aibRoleNetworking.json
sed -i -e "s/Azure Image Builder Service Networking Role/$netRoleDefName/g" aibRoleNetworking.json

# Create a custom role from the aibRoleNetworking.json description file.
az role definition create --role-definition ./aibRoleNetworking.json

# Get the user-assigned managed identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $identityName | grep "clientId" | cut -c16- | tr -d '",')

# Grant the custom role to the user-assigned managed identity for Azure Image Builder.
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $netRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$VnetResourceGroup
```

## <a name="using-managed-identity-for-azure-storage-access"></a>Usando a identidade gerenciada para acesso de armazenamento do Azure

Se você quiser Conecte autenticar com o armazenamento do Azure e usar contêineres privados, o Azure Image Builder precisará de uma identidade gerenciada atribuída pelo usuário. O construtor de imagens do Azure usa a identidade para autenticar com o armazenamento do Azure.

> [!NOTE]
> O construtor de imagens do Azure usa apenas a identidade no momento de envio do modelo de imagem. A VM de compilação não tem acesso à identidade durante a compilação da imagem.

Use CLI do Azure para criar a identidade gerenciada atribuída pelo usuário.

```azurecli
az role assignment create \
    --assignee <Image Builder client ID> \
    --role "Storage Blob Data Reader" \
    --scope /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Storage/storageAccounts/$scriptStorageAcc/blobServices/default/containers/<Storage account container>
```

No modelo do Image Builder, você precisa fornecer a identidade gerenciada atribuída pelo usuário.

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2019-05-01-preview",
    "location": "<Region>",
    ..
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<Image Builder ID>": {}     
        }
```

Substitua as seguintes configurações de espaço reservado:

| Configuração | Descrição |
|---------|-------------|
| \<Region\> | Região do modelo |
| \<Resource group\> | Resource group |
| \<Storage account container\> | Nome do contêiner da conta de armazenamento |
| \<Subscription ID\> | Assinatura do Azure |

Para obter mais informações sobre como usar uma identidade gerenciada atribuída pelo usuário, consulte [criar uma imagem personalizada que usará uma identidade gerenciada do azure User-Assigned para arquivos do Conecte Access do armazenamento do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-user-assigned-identity). O guia de início rápido explica como criar e configurar a identidade gerenciada atribuída pelo usuário para acessar uma conta de armazenamento.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte [visão geral do construtor de imagens do Azure](../image-builder-overview.md).
