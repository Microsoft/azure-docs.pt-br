---
title: Usar o construtor de imagens do Azure com uma galeria de imagens para máquinas virtuais do Linux (visualização)
description: Crie imagens do Linux com o construtor de imagens do Azure e Galeria de imagens compartilhadas.
author: cynthn
ms.author: cynthn
ms.date: 04/20/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: a47c7fd60ec9ddd3fd5e5accae8849bd62bf894c
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671489"
---
# <a name="preview-create-a-linux-image-and-distribute-it-to-a-shared-image-gallery"></a>Visualização: Criar uma imagem do Linux e distribuí-lo para uma galeria de imagens compartilhadas 

Este artigo mostra como você pode usar o construtor de imagens do Azure para criar uma versão da imagem em um [Galeria de imagens compartilhadas](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries), em seguida, distribuir a imagem globalmente.


Usaremos um exemplo de modelo. JSON para configurar a imagem. O arquivo. JSON que estamos usando aqui é: [helloImageTemplateforSIG.json](https://github.com/danielsollondon/azvmimagebuilder/blob/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json). 

Para distribuir a imagem para uma galeria de imagens compartilhadas, o modelo usa [sharedImage](image-builder-json.md#distribute-sharedimage) como o valor para o `distribute` seção do modelo.

> [!IMPORTANT]
> Construtor de imagens do Azure está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register-the-features"></a>Registrar os recursos
Para usar o construtor de imagens do Azure durante a visualização, você precisa registrar o novo recurso.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

Verifique o status do registro do recurso.

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

Verificar o seu registro.

```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState

az provider show -n Microsoft.Storage | grep registrationState
```

Se não dizem registrado, execute o seguinte:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages

az provider register -n Microsoft.Storage
```

## <a name="set-variables-and-permissions"></a>Definir variáveis e permissões 

Usaremos algumas partes de informações repetidamente, portanto, vamos criar algumas variáveis para armazenar essas informações.

Para a visualização, construtor de imagens dará suporte somente a criação de imagens personalizadas no mesmo grupo de recursos como a imagem do código-fonte gerenciado. Atualize o nome do grupo de recursos neste exemplo para ser o mesmo grupo de recursos como sua imagem gerenciada de origem.

```azurecli-interactive
# Resource group name - we are using ibLinuxGalleryRG in this example
sigResourceGroup=ibLinuxGalleryRG
# Datacenter location - we are using West US 2 in this example
location=westus2
# Additional region to replicate the image to - we are using East US in this example
additionalregion=eastus
# name of the shared image gallery - in this example we are using myGallery
sigName=myIbGallery
# name of the image definition to be created - in this example we are using myImageDef
imageDefName=myIbImageDef
# image distribution metadata reference name
runOutputName=aibLinuxSIG
```

Criar uma variável para sua ID de assinatura. Você pode obter isso usando `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Subscription ID>
```

Crie o grupo de recursos.

```azurecli-interactive
az group create -n $sigResourceGroup -l $location
```


Conceda permissão de construtor de imagens do Azure para criar recursos nesse grupo de recursos. O `--assignee` valor é a ID de registro de aplicativo para o serviço do Image Builder. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```





## <a name="create-an-image-definition-and-gallery"></a>Criar uma definição de imagem e a Galeria

Crie uma galeria de imagens. 

```azurecli-interactive
az sig create \
    -g $sigResourceGroup \
    --gallery-name $sigName
```

Crie uma definição de imagem.

```azurecli-interactive
az sig image-definition create \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --publisher myIbPublisher \
   --offer myOffer \
   --sku 18.04-LTS \
   --os-type Linux
```


## <a name="download-and-configure-the-json"></a>Baixar e configurar o. JSON

Baixe o modelo. JSON e configurá-lo com suas variáveis.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json -o helloImageTemplateforSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIG.json
```

## <a name="create-the-image-version"></a>Criar a versão da imagem

A seguinte parte criará a versão da imagem na Galeria. 

Envie a configuração de imagem para o serviço de construtor de imagens do Azure.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

Inicie o build de imagem.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforSIG01 \
     --action Run 
```

Criando a imagem e replicá-lo para ambas as regiões podem levar algum tempo. Aguarde até que essa parte seja concluída antes de passar para a criação de uma VM.


## <a name="create-the-vm"></a>Criar a VM

Crie uma VM usando a versão da imagem que foi criada pelo construtor de imagens do Azure.

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name myAibGalleryVM \
  --admin-username aibuser \
  --location $location \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --generate-ssh-keys
```

SSH para a VM.

```azurecli-interactive
ssh aibuser@<publicIpAddress>
```

Você deve ver a imagem foi personalizada com um *mensagem do dia* assim que a conexão SSH é estabelecida!

```console
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up-resources"></a>Limpar recursos

Se você quiser experimentar agora personalizando novamente a versão da imagem para criar uma nova versão da mesma imagem, ignore as próximas etapas e vá para [uso do Azure Image Builder para criar outra versão da imagem](image-builder-gallery-update-image-version.md).


Isso excluirá a imagem que foi criada, junto com todos os outros arquivos de recurso. Verifique se que você tiver terminado com essa implantação antes de excluir os recursos.

Ao excluir os recursos da Galeria de imagem, você precisa excluir todas as versões de imagem antes de excluir a definição da imagem usada para criá-los. Para excluir uma galeria, primeiro você precisa ter excluído todas as definições de imagem na Galeria.

Exclua o modelo do construtor de imagem.

```azurecli-interactive
az resource delete \
    --resource-group $sigResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

Obter a versão da imagem criada pelo construtor de imagem, isso sempre começa com `0.`e, em seguida, exclua a versão da imagem

```azurecli-interactive
sigDefImgVersion=$(az sig image-version list \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID --query [].'name' -o json | grep 0. | tr -d '"')
az sig image-version delete \
   -g $sigResourceGroup \
   --gallery-image-version $sigDefImgVersion \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID
```   


Exclua a definição da imagem.

```azurecli-interactive
az sig image-definition delete \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID
```

Exclua Galeria.

```azurecli-interactive
az sig delete -r $sigName -g $sigResourceGroup
```

Exclua o grupo de recursos.

```azurecli-interactive
az group delete -n $sigResourceGroup -y
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [galerias de imagens do Azure compartilhado](shared-image-galleries.md).