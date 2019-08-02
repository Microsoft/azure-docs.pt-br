---
title: Criar uma imagem de máquina Virtual e usar uma identidade atribuída pelo usuário gerenciada para acessar arquivos no armazenamento do Azure (visualização)
description: Crie imagem de máquina virtual usando o construtor de imagens do Azure, que pode acessar arquivos armazenados no armazenamento do Azure usando a identidade atribuída pelo usuário gerenciada.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: b6347765f8d2e21c352834dc8d28b65c28f99758
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671442"
---
# <a name="create-an-image-and-use-a-user-assigned-managed-identity-to-access-files-in-azure-storage"></a>Criar uma imagem e usar uma identidade atribuída pelo usuário gerenciada para acessar arquivos no armazenamento do Azure 

Azure Image Builder dá suporte a usando scripts ou copiar arquivos de vários locais, como o GitHub e o Azure armazenamento etc. Para usá-los, eles devem ter sido acessíveis externamente para o construtor de imagens do Azure, mas você pode proteger os blobs de armazenamento do Azure usando Tokens SAS.

Este artigo mostra como criar uma imagem personalizada usando o construtor de imagem de VM do Azure, em que o serviço usará um [identidade atribuída pelo usuário gerenciado](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) para acessar os arquivos no armazenamento do Azure para a personalização de imagem, sem ter que fazer os arquivos acessíveis publicamente ou configurar tokens SAS.

No exemplo a seguir, você criará dois grupos de recursos, um será usado para a imagem personalizada e o outro hospedará uma conta de armazenamento do Azure, que contém um arquivo de script. Isso simula um cenário de vida real, em que você pode ter artefatos de compilação ou arquivos de imagem em diferentes contas de armazenamento, fora do construtor de imagens. Você aprenderá a criar uma identidade atribuída pelo usuário e, em seguida, grant que permissões de leitura no arquivo de script, mas você não irá definir qualquer acesso público para esse arquivo. Você usará o personalizador do Shell para baixar e executar esse script da conta de armazenamento.


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


## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Usaremos algumas partes de informações repetidamente, portanto, vamos criar algumas variáveis para armazenar essas informações.


```azurecli-interactive
# Image resource group name 
imageResourceGroup=aibmdimsi
# storage resource group
strResourceGroup=aibmdimsistor
# Location 
location=WestUS2
# name of the image to be created
imageName=aibCustLinuxImgMsi01
# image distribution metadata reference name
runOutputName=u1804ManImgMsiro
```

Criar uma variável para sua ID de assinatura. Você pode obter isso usando `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Your subscription ID>
```

Crie os grupos de recursos para a imagem e o armazenamento de script.

```azurecli-interactive
# create resource group for image template
az group create -n $imageResourceGroup -l $location
# create resource group for the script storage
az group create -n $strResourceGroup -l $location
```


Criar o armazenamento e copie o script de exemplo para ele do GitHub.

```azurecli-interactive
# script storage account
scriptStorageAcc=aibstorscript$(date +'%s')

# script container
scriptStorageAccContainer=scriptscont$(date +'%s')

# script url
scriptUrl=https://$scriptStorageAcc.blob.core.windows.net/$scriptStorageAccContainer/customizeScript.sh

# create storage account and blob in resource group
az storage account create -n $scriptStorageAcc -g $strResourceGroup -l $location --sku Standard_LRS

az storage container create -n $scriptStorageAccContainer --fail-on-exist --account-name $scriptStorageAcc

# copy in an example script from the GitHub repo 
az storage blob copy start \
    --destination-blob customizeScript.sh \
    --destination-container $scriptStorageAccContainer \
    --account-name $scriptStorageAcc \
    --source-uri https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/customizeScript.sh
```



Conceda permissão do Image Builder para criar recursos no grupo de recursos de imagem. O `--assignee` valor é a ID de registro de aplicativo para o serviço do Image Builder. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```


## <a name="create-user-assigned-managed-identity"></a>Criar identidade atribuída pelo usuário gerenciada

Criar a identidade e atribuir permissões para a conta de armazenamento de script. Para obter mais informações, consulte [identidade de gerenciado atribuída pelo usuário](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity).

```azurecli-interactive
# Create the user assigned identity 
identityName=aibBuiUserId$(date +'%s')
az identity create -g $imageResourceGroup -n $identityName
# assign the identity permissions to the storage account, so it can read the script blob
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $identityName | grep "clientId" | cut -c16- | tr -d '",')
az role assignment create \
    --assignee $imgBuilderCliId \
    --role "Storage Blob Data Reader" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$strResourceGroup/providers/Microsoft.Storage/storageAccounts/$scriptStorageAcc/blobServices/default/containers/$scriptStorageAccContainer 
# create the user identity URI
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$identityName
```


## <a name="modify-the-example"></a>Modificar o exemplo

Baixe o arquivo. JSON de exemplo e configurá-lo com as variáveis que você criou.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage/helloImageTemplateMsi.json -o helloImageTemplateMsi.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateMsi.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateMsi.json
sed -i -e "s/<region>/$location/g" helloImageTemplateMsi.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateMsi.json
sed -i -e "s%<scriptUrl>%$scriptUrl%g" helloImageTemplateMsi.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateMsi.json
sed -i -e "s%<runOutputName>%$runOutputName%g" helloImageTemplateMsi.json
```

## <a name="create-the-image"></a>Criar a imagem

Envie a configuração de imagem para o serviço de construtor de imagens do Azure.

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateMsi.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
```

Inicie o build de imagem.

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateMsi01 \
     --action Run 
```

Aguarde a conclusão do build. Isso pode levar cerca de 15 minutos.

## <a name="create-a-vm"></a>Criar uma máquina virtual

Crie uma VM a partir da imagem. 

```bash
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgVm00 \
  --admin-username aibuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

Depois que a VM tiver sido criada, inicie uma sessão SSH com a VM.

```azurecli-interactive
ssh aibuser@<publicIp>
```

Você deve ver que a imagem foi personalizada com uma mensagem do dia, assim que a conexão SSH é estabelecida!

```console

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up"></a>Limpar

Quando tiver terminado, você pode excluir os recursos se eles não são mais necessários.

```azurecli-interactive
az identity delete --ids $imgBuilderId
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
az group delete -n $imageResourceGroup
az group delete -n $strResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Se você tiver problemas para trabalhar com o construtor de imagens do Azure, consulte [solução de problemas](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md?toc=%2fazure%2fvirtual-machines%context%2ftoc.json).