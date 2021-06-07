---
title: Criar uma nova versão de imagem de uma versão de imagem existente usando o construtor de imagens do Azure (visualização)
description: Crie uma nova versão de imagem de VM de uma versão de imagem existente usando o construtor de imagem do Azure no Windows.
author: cynthn
ms.author: cynthn
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subervice: image-builder
ms.colletion: windows
ms.openlocfilehash: 0a53e8de8dd832e793ae12034c96ce9fe634ed7a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101694097"
---
# <a name="preview-create-a-new-vm-image-version-from-an-existing-image-version-using-azure-image-builder-in-windows"></a>Visualização: criar uma nova versão de imagem de VM de uma versão de imagem existente usando o construtor de imagem do Azure no Windows

Este artigo mostra como usar uma versão de imagem existente em uma [Galeria de imagens compartilhadas](../shared-image-galleries.md), atualizá-la e publicá-la como uma nova versão de imagem na galeria.

Usaremos um modelo .json de exemplo para configurar a imagem. O arquivo. JSON que estamos usando está aqui: [helloImageTemplateforSIGfromWinSIG.jsem](https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Win_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromWinSIG.json). 

> [!IMPORTANT]
> O Construtor de Imagens do Azure está atualmente em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register-the-features"></a>Registrar os recursos
Para usar o Construtor de Imagens do Azure durante a versão prévia, você precisa registrar o novo recurso.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

Verifique o status do registro do recurso.

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

Verifique seu registro.

```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState
az provider show -n Microsoft.KeyVault | grep registrationState
az provider show -n Microsoft.Compute | grep registrationState
az provider show -n Microsoft.Storage | grep registrationState
az provider show -n Microsoft.Network | grep registrationState
```

Caso o status não seja mostrado como registrado, execute o seguinte:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Compute
az provider register -n Microsoft.KeyVault
az provider register -n Microsoft.Storage
az provider register -n Microsoft.Network
```


## <a name="set-variables-and-permissions"></a>Definir variáveis e permissões

Se você usou [criar uma imagem e distribuir para uma galeria de imagens compartilhadas](image-builder-gallery.md) para criar sua galeria de imagens compartilhadas, você já criou as variáveis que precisamos. Caso contrário, configure algumas variáveis a serem usadas para este exemplo.

Para a Versão Prévia, o Construtor de Imagens só dará suporte à criação de imagens personalizadas no mesmo grupo de recursos da imagem gerenciada de origem. Atualize o nome do grupo de recursos neste exemplo para que seja o mesmo grupo de recursos da imagem gerenciada de origem.

```azurecli-interactive
# Resource group name - we are using ibsigRG in this example
sigResourceGroup=myIBWinRG
# Datacenter location - we are using West US 2 in this example
location=westus
# Additional region to replicate the image to - we are using East US in this example
additionalregion=eastus
# name of the shared image gallery - in this example we are using myGallery
sigName=my22stSIG
# name of the image definition to be created - in this example we are using myImageDef
imageDefName=winSvrimages
# image distribution metadata reference name
runOutputName=w2019SigRo
# User name and password for the VM
username="user name for the VM"
vmpassword="password for the VM"
```

Criar uma variável para a ID da assinatura. Obtenha isso usando `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Subscription ID>
```

Obtenha a versão da imagem que você deseja atualizar.

```azurecli-interactive
sigDefImgVersionId=$(az sig image-version list \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID --query [].'id' -o json | grep 0. | tr -d '"' | tr -d '[:space:]')
```

## <a name="create-a-user-assigned-identity-and-set-permissions-on-the-resource-group"></a>Criar uma identidade atribuída pelo usuário e definir permissões no grupo de recursos
Como você definiu a identidade do usuário no exemplo anterior, só precisa obter a ID do recurso, e então ela será anexada ao modelo.

```azurecli-interactive
#get identity used previously
imgBuilderId=$(az identity list -g $sigResourceGroup --query "[?contains(name, 'aibBuiUserId')].id" -o tsv)
```

Se você já tiver sua própria galeria de imagens compartilhadas e não seguir o exemplo anterior, será necessário atribuir permissões para que o Image Builder acesse o grupo de recursos, para que possa acessar a galeria. Examine as etapas no exemplo [criar uma imagem e distribuir para uma galeria de imagens compartilhadas](image-builder-gallery.md) .


## <a name="modify-helloimage-example"></a>Modificar exemplo de helloImage
Você pode examinar o exemplo que estamos prestes a usar abrindo o arquivo. JSON aqui: [helloImageTemplateforSIGfromSIG.js](https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json) juntamente com a referência de [modelo do Image Builder](../linux/image-builder-json.md). 


Baixe o exemplo. JSON e configure-o com suas variáveis. 

```azurecli-interactive
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Win_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromWinSIG.json -o helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s%<sigDefImgVersionId>%$sigDefImgVersionId%g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateforSIGfromWinSIG.json
```

## <a name="create-the-image"></a>Criar a imagem

Envie a configuração de imagem para o serviço do construtor de imagem de VM.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIGfromWinSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n imageTemplateforSIGfromWinSIG01
```

Inicie o build da imagem.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n imageTemplateforSIGfromWinSIG01 \
     --action Run 
```

Aguarde até que a imagem tenha sido criada e replicação antes de passar para a próxima etapa.


## <a name="create-the-vm"></a>Criar a VM

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name aibImgWinVm002 \
  --admin-username $username \
  --admin-password $vmpassword \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --location $location
```

## <a name="verify-the-customization"></a>Verificar a personalização
Crie uma Conexão de Área de Trabalho Remota com a VM usando o nome de usuário e a senha que você definiu quando criou a VM. Dentro da VM, abra um prompt de cmd e digite:

```console
dir c:\
```

Agora você deve ver dois diretórios:
- `buildActions` que foi criado na primeira versão da imagem.
- `buildActions2` que foi criado como parte acima, atualizando a primeira versão da imagem para criar a segunda versão da imagem.


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os componentes do arquivo. JSON usado neste artigo, consulte referência de [modelo do Image Builder](../linux/image-builder-json.md).
