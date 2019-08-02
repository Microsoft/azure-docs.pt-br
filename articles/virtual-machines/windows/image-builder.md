---
title: Criar uma VM do Windows com o construtor de imagens do Azure (visualização)
description: Crie um VM do Windows com o construtor de imagens do Azure.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-windows
manager: gwallace
ms.openlocfilehash: fec6d83870e20b7622f37c52847803d4f03cbba5
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722683"
---
# <a name="preview-create-a-windows-vm-with-azure-image-builder"></a>Visualização: Criar um VM do Windows com o construtor de imagens do Azure

Este artigo é mostrar como você pode criar uma imagem personalizada do Windows usando o construtor de imagem de VM do Azure. O exemplo neste artigo usa três diferentes [personalizadores](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#properties-customize) para personalizar a imagem:
- PowerShell (ScriptUri) - baixar e executar uma [script do PowerShell](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/testPsScript.ps1).
- Reinicialização do Windows - reinicia a VM.
- PowerShell (embutido) - executar um comando específico. Neste exemplo, ele cria um diretório na VM usando `mkdir c:\\buildActions`.
- Arquivo - copiar um arquivo do GitHub na máquina virtual. Este exemplo copia [index.md](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) para `c:\buildArtifacts\index.html` na VM.

Usaremos um exemplo de modelo. JSON para configurar a imagem. O arquivo. JSON que estamos usando aqui é: [helloImageTemplateWin.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json). 


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
# Resource group name - we are using myImageBuilderRG in this example
imageResourceGroup=myWinImgBuilderRG
# Region location 
location=WestUS2
# Name for the image 
imageName=myWinBuilderImage
# Run output name
runOutputName=aibWindows
# name of the image to be created
imageName=aibWinImage
```

Criar uma variável para sua ID de assinatura. Você pode obter isso usando `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Your subscription ID>
```

Crie o grupo de recursos.

```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

Conceda permissão do Image Builder para criar recursos nesse grupo de recursos. O `--assignee` valor é a ID de registro de aplicativo para o serviço do Image Builder. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```


## <a name="download-the-json-example"></a>Baixe o exemplo a. JSON

Baixe o arquivo. JSON de exemplo e configurá-lo com as variáveis que você criou.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json -o helloImageTemplateWin.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateWin.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateWin.json
sed -i -e "s/<region>/$location/g" helloImageTemplateWin.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateWin.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateWin.json

```

## <a name="create-the-image"></a>Criar a imagem

Enviar a configuração de imagem para o serviço de construtor de imagens de VM

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateWin.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

Inicie o build de imagem.

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateWin01 \
     --action Run 
```

Aguarde até que a compilação estiver concluída. Isso pode levar cerca de 15 minutos.

## <a name="create-the-vm"></a>Criar a VM

Crie a VM usando a imagem criada por você. Substitua *<password>* com sua própria senha para o `aibuser` na VM.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgWinVm00 \
  --admin-username aibuser \
  --admin-password <password> \
  --image $imageName \
  --location $location
```

## <a name="verify-the-customization"></a>Verifique se a personalização

Crie uma conexão de área de trabalho remota à VM usando o nome de usuário e a senha que você definiu quando criou a VM. Dentro da VM, abra um prompt de comando e digite:

```console
dir c:\
```

Você deve ver esses dois diretórios criados durante a personalização de imagem:
- buildActions
- buildArtifacts

## <a name="clean-up"></a>Limpar

Quando você terminar, exclua os recursos.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
az group delete -n $imageResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os componentes do arquivo. JSON usados neste artigo, consulte [referência de modelo do construtor de imagem](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

