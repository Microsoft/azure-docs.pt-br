---
title: Usar o construtor de imagens do Azure para VMs do Linux permitindo o acesso a uma VNET do Azure existente (versão prévia)
description: Criar imagens de VM do Linux com o construtor de imagens do Azure permitindo o acesso a uma VNET do Azure existente
author: danielsollondon
ms.author: danis
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.reviewer: danis
ms.openlocfilehash: 500ddec9b84f9d73db45ddb4b7f5a8486a48d3e5
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102565301"
---
# <a name="use-azure-image-builder-for-linux-vms-allowing-access-to-an-existing-azure-vnet"></a>Usar o construtor de imagens do Azure para VMs Linux permitindo o acesso a uma VNET do Azure existente

Este artigo mostra como você pode usar o construtor de imagens do Azure para criar uma imagem personalizada do Linux básica que tem acesso a recursos existentes em uma VNET. A VM de compilação que você cria é implantada em uma VNET nova ou existente que você especificar em sua assinatura. Quando você usa uma VNET do Azure existente, o serviço do construtor de imagens do Azure não requer conectividade de rede pública.

> [!IMPORTANT]
> O Construtor de Imagens do Azure está atualmente em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="register-the-features"></a>Registrar os recursos

Primeiro, você deve se registrar para o serviço do construtor de imagem do Azure. O registro concede a permissão de serviço para criar, gerenciar e excluir um grupo de recursos de preparo. O serviço também tem direitos para adicionar recursos do grupo que são necessários para a compilação da imagem.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

## <a name="set-variables-and-permissions"></a>Definir variáveis e permissões 

Você usará algumas informações repetidamente. Crie algumas variáveis para armazenar essas informações.

```azurecli-interactive
# set your environment variables here!!!!

# destination image resource group
imageResourceGroup=aibImageRG01

# location (see possible locations in main docs)
location=WestUS2

# your subscription
# get the current subID : 'az account show | grep id'
subscriptionID=$(az account show | grep id | tr -d '",' | cut -c7-)

# name of the image to be created
imageName=aibCustomLinuxImg01

# image distribution metadata reference name
runOutputName=aibCustLinManImg01ro


# VNET properties (update to match your existing VNET, or leave as-is for demo)
# VNET name
vnetName=myexistingvnet01
# subnet name
subnetName=subnet01
# VNET resource group name
# NOTE! The VNET must always be in the same region as the AIB service region.
vnetRgName=existingVnetRG
# Existing Subnet NSG Name or the demo will create it
nsgName=aibdemoNsg
```

Crie o grupo de recursos.

```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="configure-networking"></a>Configurar a rede

Se você não tiver um VNET\Subnet\NSG existente, use o script a seguir para criar um.

```bash

# Create a resource group

az group create -n $vnetRgName -l $location

# Create VNET

az network vnet create \
    --resource-group $vnetRgName \
    --name $vnetName --address-prefix 10.0.0.0/16 \
    --subnet-name $subnetName --subnet-prefix 10.0.0.0/24

# Create base NSG to simulate an existing NSG

az network nsg create -g $vnetRgName -n $nsgName

az network vnet subnet update \
    --resource-group $vnetRgName \
    --vnet-name $vnetName \
    --name $subnetName \
    --network-security-group $nsgName
    
#  NOTE! The VNET must always be in the same region as the Azure Image Builder service region.
```

### <a name="add-network-security-group-rule"></a>Adicionar regra de grupo de segurança de rede

Essa regra permite a conectividade do balanceador de carga do Azure Image Builder com a VM do proxy. A porta 60001 é para sistemas operacionais Linux e a porta 60000 é para sistemas operacionais Windows. A VM proxy conecta-se à VM de compilação usando a porta 22 para Linux OSs ou a porta 5986 para sistemas operacionais Windows.

```azurecli-interactive
az network nsg rule create \
    --resource-group $vnetRgName \
    --nsg-name $nsgName \
    -n AzureImageBuilderNsgRule \
    --priority 400 \
    --source-address-prefixes AzureLoadBalancer \
    --destination-address-prefixes VirtualNetwork \
    --destination-port-ranges 60000-60001 --direction inbound \
    --access Allow --protocol Tcp \
    --description "Allow Image Builder Private Link Access to Proxy VM"
```

### <a name="disable-private-service-policy-on-subnet"></a>Desabilitar política de serviço particular na sub-rede

```azurecli-interactive
az network vnet subnet update \
  --name $subnetName \
  --resource-group $vnetRgName \
  --vnet-name $vnetName \
  --disable-private-link-service-network-policies true 
```

Para obter mais informações sobre a rede do Image Builder, consulte [Opções de rede do serviço do Azure Image Builder](image-builder-networking.md).

## <a name="modify-the-example-template-and-create-role"></a>Modificar o modelo de exemplo e criar função

```bash
# download the example and configure it with your vars

curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/1a_Creating_a_Custom_Linux_Image_on_Existing_VNET/existingVNETLinux.json -o existingVNETLinux.json
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleNetworking.json -o aibRoleNetworking.json
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" existingVNETLinux.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" existingVNETLinux.json
sed -i -e "s/<region>/$location/g" existingVNETLinux.json
sed -i -e "s/<imageName>/$imageName/g" existingVNETLinux.json
sed -i -e "s/<runOutputName>/$runOutputName/g" existingVNETLinux.json

sed -i -e "s/<vnetName>/$vnetName/g" existingVNETLinux.json
sed -i -e "s/<subnetName>/$subnetName/g" existingVNETLinux.json
sed -i -e "s/<vnetRgName>/$vnetRgName/g" existingVNETLinux.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" aibRoleImageCreation.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleNetworking.json
sed -i -e "s/<vnetRgName>/$vnetRgName/g" aibRoleNetworking.json

```

## <a name="set-permissions-on-the-resource-group"></a>Definir permissões no grupo de recursos

O Construtor de Imagens usará a [identidade do usuário](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity) fornecida para injetar a imagem na SIG (Galeria de Imagens Compartilhadas) do Azure. Neste exemplo, você criará uma definição de função do Azure que tem as ações granulares para executar a distribuição da imagem para o SIG. A definição de função será então atribuída à identidade do usuário.

```bash
# create user assigned identity for image builder
idenityName=aibBuiUserId$(date +'%s')
az identity create -g $imageResourceGroup -n $idenityName

# get identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $idenityName | grep "clientId" | cut -c16- | tr -d '",')

# get the user identity URI, needed for the template
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$idenityName

# update the template
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" existingVNETLinux.json

# make role name unique, to avoid clashes in the same Azure Active Directory domain
imageRoleDefName="Azure Image Builder Image Def"$(date +'%s')
netRoleDefName="Azure Image Builder Network Def"$(date +'%s')

# update the definitions
sed -i -e "s/Azure Image Builder Service Image Creation Role/$imageRoleDefName/g" aibRoleImageCreation.json
sed -i -e "s/Azure Image Builder Service Networking Role/$netRoleDefName/g" aibRoleNetworking.json
```

Em vez de conceder menor granularidade ao construtor de imagem e maior privilégio, você pode criar duas funções. Uma fornece ao Construtor permissões para criar uma imagem, a outra permite que ela Conecte a VM de compilação e o balanceador de carga à sua VNET.

```bash
# create role definitions
az role definition create --role-definition ./aibRoleImageCreation.json
az role definition create --role-definition ./aibRoleNetworking.json

# grant role definition to the user assigned identity
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $imageRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup

az role assignment create \
    --assignee $imgBuilderCliId \
    --role $netRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$vnetRgName
```

Para obter mais informações sobre permissões, consulte [configurar permissões de serviço do construtor de imagem do Azure usando CLI do Azure](image-builder-permissions-cli.md) ou [configurar permissões de serviço do construtor de imagem do Azure usando o PowerShell](image-builder-permissions-powershell.md).

## <a name="create-the-image"></a>Criar a imagem

Envie a configuração de imagem para o serviço Construtor de Imagens do Azure.

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @existingVNETLinux.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n existingVNETLinuxTemplate01

# Wait approximately 1-3 mins (validation, permissions etc.)
```

Inicie o build da imagem.

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n existingVNETLinuxTemplate01 \
     --action Run 

# Wait approximately 15 mins
```

Criar a imagem e replicá-la para ambas as regiões pode levar algum tempo. Aguarde até que essa parte seja concluída antes de passar para a criação de uma VM.


## <a name="create-the-vm"></a>Criar a VM

Crie uma VM com base na versão da imagem criada pelo Construtor de Imagens do Azure.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgVm0001 \
  --admin-username aibuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

Execute o SSH na VM.

```bash
ssh aibuser@<publicIpAddress>
```

Você deve ver que a imagem foi personalizada com uma *Mensagem do Dia* assim que a conexão SSH é estabelecida.

```console
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se você quiser agora repersonalizar a versão da imagem para criar uma nova versão da mesma imagem, ignore as próximas etapas e vá para [usar o construtor de imagem do Azure para criar outra versão de imagem](image-builder-gallery-update-image-version.md).


O seguinte exclui a imagem que foi criada, junto com todos os outros arquivos de recurso. Verifique se você concluiu essa implantação antes de excluir os recursos.

Ao excluir os recursos da galeria de imagens, você precisará excluir todas as versões da imagem para excluir a definição de imagem usada para criá-las. Para excluir uma galeria, primeiro você precisará excluir todas as definições de imagem na galeria.

Exclua o modelo do Construtor de Imagens.

```azurecli
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n existingVNETLinuxTemplate01
```

Excluir atribuições de permissões, funções e identidade
```azurecli-interactive
az role assignment delete \
    --assignee $imgBuilderCliId \
    --role $imageRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup

az role assignment delete \
    --assignee $imgBuilderCliId \
    --role $netRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$vnetRgName


az role definition delete --name "$imageRoleDefName"
az role definition delete --name "$netRoleDefName"

az identity delete --ids $imgBuilderId
```

Exclua o grupo de recursos.

```azurecli-interactive
az group delete -n $imageResourceGroup
```

Se você criou uma VNET para este guia de início rápido, poderá excluir a VNET se ela não estiver mais sendo usada.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as [Galerias de Imagens Compartilhadas do no Azure](../shared-image-galleries.md).
