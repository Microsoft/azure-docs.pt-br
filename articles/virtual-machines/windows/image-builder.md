---
title: Criar uma VM do Windows com o construtor de imagens do Azure (visualização)
description: Crie uma VM do Windows com o construtor de imagens do Azure.
author: cynthn
ms.author: cynthn
ms.date: 05/05/2020
ms.topic: how-to
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.openlocfilehash: ca9f9ad0fb724884e865469ba118af4c7e6cea2f
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98202837"
---
# <a name="preview-create-a-windows-vm-with-azure-image-builder"></a>Versão prévia: criar uma VM do Windows com o construtor de imagens do Azure

Este artigo mostra como você pode criar uma imagem personalizada do Windows usando o construtor de imagem de VM do Azure. O exemplo neste artigo usa os [personalizadores](../linux/image-builder-json.md#properties-customize) para personalizar a imagem:
- PowerShell (ScriptUri) – Baixe e execute um [script do PowerShell](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/testPsScript.ps1).
- Reinicialização do Windows – reinicia a VM.
- PowerShell (embutido) – executar um comando específico. Neste exemplo, ele cria um diretório na VM usando `mkdir c:\\buildActions` .
- Arquivo – Copie um arquivo do GitHub para a VM. Este exemplo copia [index.MD](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) para `c:\buildArtifacts\index.html` na VM.
- buildTimeoutInMinutes-aumentar um tempo de compilação para permitir compilações mais longas, o padrão é de 240 minutos e você pode aumentar um tempo de compilação para permitir compilações mais longas.
- vmProfile-especificando uma vmSize e propriedades de rede
- osDiskSizeGB-você pode aumentar o tamanho da imagem
- identidade-fornecendo uma identidade para o construtor de imagens do Azure usar durante a compilação


Você também pode especificar um `buildTimeoutInMinutes` . O padrão é de 240 minutos, e você pode aumentar um tempo de compilação para permitir compilações mais longas.

Usaremos um modelo .json de exemplo para configurar a imagem. O arquivo. JSON que estamos usando está aqui: [helloImageTemplateWin.jsem](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json). 


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
```

Caso o status não seja mostrado como registrado, execute o seguinte:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Compute
az provider register -n Microsoft.KeyVault
az provider register -n Microsoft.Storage
```


## <a name="set-variables"></a>Definir variáveis

Usaremos algumas informações repetidamente, portanto, criaremos algumas variáveis para armazená-las.


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

Criar uma variável para a ID da assinatura. Obtenha isso usando `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Your subscription ID>
```
## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Esse grupo de recursos é usado para armazenar o artefato do modelo de configuração de imagem e a imagem.


```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="create-a-user-assigned-identity-and-set-permissions-on-the-resource-group"></a>Criar uma identidade atribuída pelo usuário e definir permissões no grupo de recursos
O Image Builder usará a [identidade do usuário](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity) fornecida para injetar a imagem no grupo de recursos. Neste exemplo, você criará uma definição de função do Azure que tem as ações granulares para executar a distribuição da imagem. A definição de função será então atribuída à identidade do usuário.

## <a name="create-user-assigned-managed-identity-and-grant-permissions"></a>Criar identidade gerenciada atribuída pelo usuário e conceder permissões 
```bash
# create user assigned identity for image builder to access the storage account where the script is located
idenityName=aibBuiUserId$(date +'%s')
az identity create -g $imageResourceGroup -n $idenityName

# get identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $idenityName | grep "clientId" | cut -c16- | tr -d '",')

# get the user identity URI, needed for the template
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$idenityName

# download preconfigured role definition example
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

imageRoleDefName="Azure Image Builder Image Def"$(date +'%s')

# update the definition
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" aibRoleImageCreation.json
sed -i -e "s/Azure Image Builder Service Image Creation Role/$imageRoleDefName/g" aibRoleImageCreation.json

# create role definitions
az role definition create --role-definition ./aibRoleImageCreation.json

# grant role definition to the user assigned identity
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $imageRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```



## <a name="download-the-image-configuration-template-example"></a>Baixar o exemplo de modelo de configuração de imagem

Um modelo de configuração de imagem com parâmetros foi criado para você tentar. Baixe o arquivo example. JSON e configure-o com as variáveis que você definiu anteriormente.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json -o helloImageTemplateWin.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateWin.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateWin.json
sed -i -e "s/<region>/$location/g" helloImageTemplateWin.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateWin.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateWin.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateWin.json

```

Você pode modificar este exemplo, no terminal usando um editor de texto como `vi` .

```azurecli-interactive
vi helloImageTemplateWin.json
```

> [!NOTE]
> Para a imagem de origem, você sempre deve [especificar uma versão](../linux/image-builder-troubleshoot.md#build--step-failed-for-image-version), não pode usar `latest` .
> Se você adicionar ou alterar o grupo de recursos para o qual a imagem é distribuída, deverá fazer com que as [permissões sejam definidas](#create-a-user-assigned-identity-and-set-permissions-on-the-resource-group) no grupo de recursos.
 
## <a name="create-the-image"></a>Criar a imagem

Enviar a configuração de imagem para o serviço do construtor de imagens de VM

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateWin.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

Ao concluir, isso retornará uma mensagem de êxito de volta ao console e criará um `Image Builder Configuration Template` no `$imageResourceGroup` . Você pode ver esse recurso no grupo de recursos na portal do Azure, se você habilitar ' Mostrar tipos ocultos '.

Em segundo plano, o Image Builder também criará um grupo de recursos de preparo em sua assinatura. Esse grupo de recursos é usado para a compilação da imagem. Ele estará neste formato: `IT_<DestinationResourceGroup>_<TemplateName>`

> [!Note]
> Você não deve excluir o grupo de recursos de preparo diretamente. Primeiro, exclua o artefato do modelo de imagem. isso fará com que o grupo de recursos de preparo seja excluído.

Se o serviço relatar uma falha durante o envio do modelo de configuração de imagem:
-  Examine essas etapas de [solução de problemas](../linux/image-builder-troubleshoot.md#troubleshoot-image-template-submission-errors) . 
- Você precisará excluir o modelo, usando o trecho a seguir, antes de tentar enviar novamente.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

## <a name="start-the-image-build"></a>Iniciar a compilação da imagem
Inicie o processo de criação de imagem usando [AZ Resource Invoke-Action](/cli/azure/resource#az-resource-invoke-action).

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateWin01 \
     --action Run 
```

Aguarde até que a compilação seja concluída. Isso pode levar cerca de 15 minutos.

Se você encontrar algum erro, leia essas etapas de [solução de problemas](../linux/image-builder-troubleshoot.md#troubleshoot-common-build-errors) .


## <a name="create-the-vm"></a>Criar a VM

Crie a VM usando a imagem que você criou. Substitua *\<password>* pela sua própria senha para o `aibuser` na VM.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgWinVm00 \
  --admin-username aibuser \
  --admin-password <password> \
  --image $imageName \
  --location $location
```

## <a name="verify-the-customization"></a>Verificar a personalização

Crie uma Conexão de Área de Trabalho Remota com a VM usando o nome de usuário e a senha que você definiu quando criou a VM. Dentro da VM, abra um prompt de cmd e digite:

```console
dir c:\
```

Você deve ver esses dois diretórios criados durante a personalização da imagem:
- buildActions
- buildArtifacts

## <a name="clean-up"></a>Limpeza

Quando terminar, exclua os recursos.

### <a name="delete-the-image-builder-template"></a>Excluir o modelo do construtor de imagem

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

### <a name="delete-the-role-assignment-role-definition-and-user-identity"></a>Exclua a atribuição de função, a definição de função e a identidade do usuário.
```azurecli-interactive
az role assignment delete \
    --assignee $imgBuilderCliId \
    --role "$imageRoleDefName" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup

az role definition delete --name "$imageRoleDefName"

az identity delete --ids $imgBuilderId
```

### <a name="delete-the-image-resource-group"></a>Excluir o grupo de recursos de imagem

```azurecli-interactive
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os componentes do arquivo. JSON usado neste artigo, consulte referência de [modelo do Image Builder](../linux/image-builder-json.md).
