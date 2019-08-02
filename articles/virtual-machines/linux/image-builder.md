---
title: Criar uma VM do Linux com o construtor de imagens do Azure (visualização)
description: Crie uma VM do Linux com o construtor de imagens do Azure.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: 2966a1803d0664312d71ba992a5ba65f73b27370
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67667520"
---
# <a name="preview-create-a-linux-vm-with-azure-image-builder"></a>Visualização: Criar uma VM do Linux com o construtor de imagens do Azure

Este artigo mostra como você pode criar uma imagem personalizada do Linux usando o construtor de imagens do Azure e a CLI do Azure. O exemplo neste artigo usa três diferentes [personalizadores](image-builder-json.md#properties-customize) para personalizar a imagem:

- Shell (ScriptUri) - downloads e executa um [script de shell](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/customizeScript.sh).
- Shell (embutido) - comandos específicos de execuções. Neste exemplo, os comandos de embutidos incluem a criação de um diretório e atualizar o sistema operacional.
- Arquivo - cópias de um [arquivo do GitHub](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) em um diretório na VM.


Usaremos um exemplo de modelo. JSON para configurar a imagem. O arquivo. JSON que estamos usando aqui é: [helloImageTemplateLinux.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json). 

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

## <a name="setup-example-variables"></a>Configurar variáveis de exemplo

Usaremos algumas partes de informações repetidamente, portanto, vamos criar algumas variáveis para armazenar essas informações.


```azurecli-interactive
# Resource group name - we are using myImageBuilderRG in this example
imageResourceGroup=myImageBuilerRGLinux
# Datacenter location - we are using West US 2 in this example
location=WestUS2
# Name for the image - we are using myBuilderImage in this example
imageName=myBuilderImage
# Run output name
runOutputName=aibLinux
```

Criar uma variável para sua ID de assinatura. Você pode obter isso usando `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Your subscription ID>
```

## <a name="create-the-resource-group"></a>Crie o grupo de recursos.
Isso é usado para armazenar o artefato de modelo de configuração de imagem e a imagem.

```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="set-permissions-on-the-resource-group"></a>Definir permissões em um grupo de recursos
Conceda permissão de 'Colaborador' Image Builder para criar a imagem no grupo de recursos. Sem as permissões adequadas, haverá falha na compilação de imagem. 

O `--assignee` valor é a ID de registro de aplicativo para o serviço do Image Builder. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

## <a name="download-the-template-example"></a>Baixe o exemplo de modelo

Um modelo de configuração de imagem com parâmetros de exemplo foi criado para uso. Baixe o arquivo. JSON de exemplo e configurá-lo com as variáveis que você definiu anteriormente.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json -o helloImageTemplateLinux.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateLinux.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateLinux.json
sed -i -e "s/<region>/$location/g" helloImageTemplateLinux.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateLinux.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateLinux.json
```

Você pode modificar este. JSON de exemplo conforme necessário. Por exemplo, você pode aumentar o valor de `buildTimeoutInMinutes` para permitir a execução de compilações mais tempo. Você pode editar o arquivo no Cloud Shell usando `vi`.

```azurecli-interactive
vi helloImageTemplateLinux.json
```

> [!NOTE]
> Para a imagem de origem, você deve sempre [especificar uma versão](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-version-failure), não é possível usar `latest`.
>
> Se você adicionar ou alterar o grupo de recursos onde a imagem está sendo distribuída, você precisa garantir que o [as permissões são definidas para o grupo de recursos](#set-permissions-on-the-resource-group).


## <a name="submit-the-image-configuration"></a>Enviar a configuração de imagem
Enviar a configuração de imagem para o serviço de construtor de imagens de VM

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateLinux.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

Se ela for concluída com êxito, ele retornará uma mensagem de êxito e, criar um artefato de modelo de configuração de construtor de imagem no $imageResourceGroup. Se você ativar 'Mostrar tipos ocultos', você pode ver o grupo de recursos no portal.

Além disso, em segundo plano, o Image Builder cria um grupo de recursos de preparo em sua assinatura. Construtor de imagens usa o grupo de recursos de preparo para a compilação de imagem. O nome do grupo de recursos será no seguinte formato: `IT_<DestinationResourceGroup>_<TemplateName>`.

> [!IMPORTANT]
> Não exclua o grupo de recursos de preparo diretamente. Se você excluir o artefato de modelo de imagem, ele excluirá automaticamente o grupo de recursos de preparo. Para obter mais informações, consulte o [limpar](#clean-up) seção no final deste artigo.

Se o serviço relata uma falha durante o envio de modelo de configuração de imagem, consulte o [solução de problemas](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#template-submission-errors--troubleshooting) etapas. Você também precisará excluir o modelo antes de tentar novamente enviar a compilação. Para excluir o modelo:

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

## <a name="start-the-image-build"></a>Iniciar o build de imagem

Inicie o build de imagem.


```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Run 
```

Aguarde até que a compilação estiver concluída, para este exemplo, pode levar 10 a 15 minutos.

Se você encontrar algum erro, examine essas [solução de problemas](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-build-errors--troubleshooting) etapas.


## <a name="create-the-vm"></a>Criar a VM

Crie a VM usando a imagem criada por você.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name myVM \
  --admin-username azureuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

Obtenha o endereço IP na saída da criação da VM e usá-lo para SSH na VM.

```azurecli-interactive
ssh azureuser@<pubIp>
```

Você deve ver que a imagem foi personalizada com uma mensagem do dia, assim que a conexão SSH é estabelecida!

```console

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

Tipo `exit` quando você terminar para fechar a conexão SSH.

## <a name="check-the-source"></a>Verifique o código-fonte

O modelo do construtor de imagem, nas 'Propriedades', você verá a imagem de origem, personalização o script é executado, e onde ele é distribuído.

```azurecli-interactive
cat helloImageTemplateLinux.json
```

Para obter mais informações sobre esse arquivo. JSON, consulte [referência de modelo do construtor de imagem](image-builder-json.md)

## <a name="clean-up"></a>Limpar

Quando você terminar, você pode excluir os recursos.

Exclua o modelo do construtor de imagem.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

Exclua o grupo de recursos de imagem.

```bash
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os componentes do arquivo. JSON usados neste artigo, consulte [referência de modelo do Image Builder](image-builder-json.md).
