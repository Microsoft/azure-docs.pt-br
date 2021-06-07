---
title: Tarefa DevOps do serviço do construtor de imagem do Azure
description: A tarefa do Azure DevOps para injetar artefatos de compilação em uma imagem de VM para que você possa instalar e configurar seu aplicativo e sistema operacional.
author: danielsollondon
ms.author: danis
ms.date: 01/27/2021
ms.topic: article
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.openlocfilehash: d02a5c6bc194009d459647721dab16be0dcade84
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101670475"
---
# <a name="azure-image-builder-service-devops-task"></a>Tarefa DevOps do serviço do construtor de imagem do Azure

Este artigo mostra como usar uma tarefa DevOps do Azure para injetar artefatos de compilação em uma imagem de VM para que você possa instalar e configurar seu aplicativo e sistema operacional.

## <a name="devops-task-versions"></a>Versões da tarefa DevOps
Há duas tarefas de DevOps do AIB (Construtor de imagens de VM) do Azure:

* A [tarefa ' estável ' de AIB](https://marketplace.visualstudio.com/items?itemName=AzureImageBuilder.devOps-task-for-azure-image-builder), esta é a compilação estável mais recente que foi testada e a telemetria não mostra nenhum problema. 


* [Tarefa ' instável ' AIB](https://marketplace.visualstudio.com/items?itemName=AzureImageBuilder.devOps-task-for-azure-image-builder-canary), isso nos permite colocar as atualizações e os recursos mais recentes, permitir que os clientes os testem antes de promovê-lo para a tarefa ' estável '. Se não houver nenhum problema relatado e nossa telemetria não mostrar nenhum problema, aproximadamente 1 semana depois, promoveremos o código da tarefa para "estável". 

## <a name="prerequisites"></a>Pré-requisitos

* Instale a [tarefa DevOps estável de Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=AzureImageBuilder.devOps-task-for-azure-image-builder).
* Você deve ter uma conta do VSTS DevOps e um pipeline de compilação criado
* Registre e habilite os requisitos do recurso do Image Builder na assinatura usada pelos pipelines:
    * [AZ PowerShell](../windows/image-builder-powershell.md#register-features)
    * [AZ CLI](../windows/image-builder.md#register-the-features)
    
* Criar uma conta de armazenamento do Azure padrão no grupo de recursos de imagem de origem, você pode usar outras contas de armazenamento/grupo de recursos. A conta de armazenamento é usada para transferir os artefatos de compilação da tarefa DevOps para a imagem.

    ```powerShell
    # Az PowerShell
    $timeInt=$(get-date -UFormat "%s")
    $storageAccName="aibstorage"+$timeInt
    $location=westus
    # create storage account and blob in resource group
    New-AzStorageAccount -ResourceGroupName $strResourceGroup -Name $storageAccName -Location $location -SkuName Standard_LRS
    ```

    ```bash
    # Az CLI
    location=westus
    scriptStorageAcc=aibstordot$(date +'%s')
    # create storage account and blob in resource group
    az storage account create -n $scriptStorageAcc -g $strResourceGroup -l $location --sku Standard_LRS
    ```

## <a name="add-task-to-release-pipeline"></a>Adicionar tarefa ao pipeline de liberação

Selecione **liberar pipeline**  >  **Editar**

No agente do usuário, selecione *+* para adicionar e Pesquisar por **Image Builder**. Selecione **Adicionar**.

Defina as seguintes propriedades de tarefa:

### <a name="azure-subscription"></a>Assinatura do Azure

Selecione no menu suspenso a assinatura que você deseja que o construtor de imagem execute. Use a mesma assinatura em que as imagens de origem estão localizadas e onde as imagens devem ser distribuídas. Você precisa autorizar o acesso colaborador do Image Builder à assinatura ou ao grupo de recursos.

### <a name="resource-group"></a>Grupo de recursos

Use o grupo de recursos em que o artefato do modelo de imagem temporária será armazenado. Ao criar um artefato de modelo, um grupo de recursos de construtor de imagem temporário adicional `IT_<DestinationResourceGroup>_<TemplateName>_guid` é criado. O grupo de recursos temporários armazena os metadados da imagem, como scripts. No final da tarefa, o artefato do modelo de imagem e o grupo de recursos do construtor de imagem temporário são excluídos.
 
### <a name="location"></a>Location

O local é a região em que o construtor de imagem será executado. Há suporte apenas para um número definido de [regiões](../image-builder-overview.md#regions) . As imagens de origem devem estar presentes neste local. Por exemplo, se você estiver usando a Galeria de imagens compartilhadas, uma réplica deverá existir nessa região.

### <a name="managed-identity-required"></a>Identidade gerenciada (obrigatória)
O Image Builder requer uma identidade gerenciada, que ela usa para ler imagens personalizadas de origem, conectar-se ao armazenamento do Azure e criar imagens personalizadas. Veja [aqui](../image-builder-overview.md#permissions) para obter mais detalhes.

### <a name="vnet-support"></a>Suporte VNET

Atualmente, a tarefa DevOps não dá suporte à especificação de uma sub-rede existente, isso está no roteiro, mas se você quiser utilizar uma VNET existente, poderá usar um modelo de ARM, com um modelo de construtor de imagem aninhado no, consulte os exemplos de modelo do Windows Image Builder sobre como isso é obtido ou, alternativamente, use [AZ AIB PowerShell](../windows/image-builder-powershell.md).

### <a name="source"></a>Fonte

As imagens de origem devem ser do OSs do Image Builder com suporte. Você pode escolher as imagens personalizadas existentes na mesma região em que o Image Builder está em execução:
* Imagem gerenciada-você precisa passar o ResourceId, por exemplo:
    ```json
    /subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/images/<imageName>
    ```
* Galeria de imagens compartilhadas do Azure-você precisa passar o ResourceId da versão da imagem, por exemplo:
    ```json
    /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/<versionNumber>
    ```

    Se precisar obter a versão mais recente da Galeria de imagens compartilhadas, você poderá ter uma tarefa AZ PowerShell ou AZ CLI para obter a versão mais recente e definir uma variável DevOps. Use a variável na tarefa AZ VM Image Builder DevOps. Para obter mais informações, consulte os [exemplos](https://github.com/danielsollondon/azvmimagebuilder/tree/master/solutions/8_Getting_Latest_SIG_Version_ResID#getting-the-latest-image-version-resourceid-from-shared-image-gallery).

* Comunidade Imagem base há uma lista suspensa de imagens populares. elas sempre usarão a versão ' mais recente ' do sistema operacional com suporte. 

    Se a imagem base não estiver na lista, você poderá especificar a imagem exata usando `Publisher:Offer:Sku` .

    Versão da imagem base (opcional)-você pode fornecer a versão da imagem que deseja usar, o padrão é `latest` .

### <a name="customize"></a>Personalizar

#### <a name="provisioner"></a>Provisionador

Inicialmente, há suporte para dois personalizadores- **shell** e **PowerShell**. Somente embutido tem suporte. Se desejar baixar scripts, você poderá passar comandos embutidos para fazer isso.

Para seu sistema operacional, selecione PowerShell ou Shell.

#### <a name="windows-update-task"></a>Windows Update tarefa

Somente para Windows, a tarefa é executada Windows Update no final das personalizações. Ele lida com as reinicializações necessárias.

A seguinte configuração de Windows Update é executada:
```json
    "type": "WindowsUpdate",
    "searchCriteria": "IsInstalled=0",
    "filters": [
        "exclude:$_.Title -like '*Preview*'",
        "include:$true"
```
Ele instala atualizações do Windows importantes e recomendadas que não são Preview.

#### <a name="handling-reboots"></a>Manipulando reinicializações
Atualmente, a tarefa DevOps não tem suporte para a reinicialização de compilações do Windows, se você tentar reinicializar com o código do PowerShell, a compilação falhará. No entanto, você pode usar o código para reinicializar compilações do Linux.

#### <a name="build-path"></a>Caminho de compilação

A tarefa foi projetada para ser capaz de injetar artefatos de versão de compilação DevOps na imagem. Para fazer isso funcionar, você precisa configurar um pipeline de compilação. Na configuração do pipeline de liberação, você deve adicionar o repositório dos artefatos de compilação.

:::image type="content" source="./media/image-builder-devops-task/add-artifact.png" alt-text="Selecionando Adicionar um artefato no pipeline de lançamento.":::

Selecione o botão **Compilar caminho** para escolher a pasta de Build que você deseja que seja colocada na imagem. A tarefa do construtor de imagem copia todos os arquivos e diretórios dentro dele. Quando a imagem está sendo criada, o Image Builder implanta os arquivos e diretórios em caminhos diferentes, dependendo do sistema operacional.

> [!IMPORTANT]
> Ao adicionar um artefato do repositório, você pode achar que o diretório é prefixado com um sublinhado *_*. O sublinhado pode causar problemas com os comandos embutidos. Use as aspas apropriadas nos comandos.
> 

O exemplo a seguir explica como isso funciona:

:::image type="content" source="./media/image-builder-devops-task/build-artifacts.png" alt-text="Uma estrutura de diretório que mostra A hierarquia.":::


* Os arquivos do Windows existem no `C:\` . Um diretório chamado `buildArtifacts` é criado, o que inclui o `webapp` diretório.

* Linux-os arquivos existem no  `/tmp` . O `webapp` diretório é criado, o que inclui todos os arquivos e diretórios. Você deve mover os arquivos deste diretório. Caso contrário, eles serão excluídos, pois estão no diretório temporário.

#### <a name="inline-customization-script"></a>Script de personalização embutido

* Windows – você pode inserir comandos embutidos do PowerShell separados por vírgulas. Se você quiser executar um script em seu diretório de compilação, poderá usar:

    ```PowerShell
    & 'c:\buildArtifacts\webapp\webconfig.ps1'
    ```

   Você pode fazer referência a vários scripts ou adicionar mais comandos, por exemplo:

    ```PowerShell
    & 'c:\buildArtifacts\webapp\webconfig.ps1'
    & 'c:\buildArtifacts\webapp\installAgent.ps1'
    ```
* Linux – em sistemas Linux, os artefatos de compilação são colocados no `/tmp` diretório. No entanto, em muitos sistemas operacionais Linux, em uma reinicialização, o conteúdo do diretório/tmp é excluído. Se você quiser que os artefatos existam na imagem, deverá criar outro diretório e copiá-los.  Por exemplo:

    ```bash
    sudo mkdir /lib/buildArtifacts
    sudo cp -r "/tmp/_ImageBuilding/webapp" /lib/buildArtifacts/.
    ```
    
    Se você estiver ok usando o diretório "/tmp", poderá usar o código abaixo para executar o script.
    
    ```bash
    # grant execute permissions to execute scripts
    sudo chmod +x "/tmp/_ImageBuilding/webapp/coreConfig.sh"
    echo "running script"
    sudo . "/tmp/AppsAndImageBuilderLinux/_WebApp/coreConfig.sh"
    ```
    
#### <a name="what-happens-to-the-build-artifacts-after-the-image-build"></a>O que acontece com os artefatos de compilação após a criação da imagem?

> [!NOTE]
> O Image Builder não remove automaticamente os artefatos de compilação, é altamente recomendável que você sempre tenha código para remover os artefatos de compilação.
> 

* Windows-o construtor de imagem implanta arquivos no `c:\buildArtifacts` diretório. O diretório é persistido, você deve remover o diretório. Você pode removê-lo no script que você executar. Por exemplo:

    ```PowerShell
    # Clean up buildArtifacts directory
    Remove-Item -Path "C:\buildArtifacts\*" -Force -Recurse
    
    # Delete the buildArtifacts directory
    Remove-Item -Path "C:\buildArtifacts" -Force 
    ```
    
* Linux-os artefatos de compilação são colocados no `/tmp` diretório. No entanto, em muitos sistemas operacionais Linux, em uma reinicialização, o `/tmp` conteúdo do diretório é excluído. É recomendável que você tenha código para remover o conteúdo e não confiar no sistema operacional para remover o conteúdo. Por exemplo:

    ```bash
    sudo rm -R "/tmp/AppsAndImageBuilderLinux"
    ```
    
#### <a name="total-length-of-image-build"></a>Comprimento total da compilação da imagem

O comprimento total não pode ser alterado na tarefa de pipeline DevOps ainda. Ele usa o padrão de 240 minutos. Se você quiser aumentar o [buildTimeoutInMinutes](./image-builder-json.md#properties-buildtimeoutinminutes), poderá usar uma tarefa AZ CLI no pipeline de lançamento. Configure a tarefa para copiar um modelo e enviá-lo. Para obter um exemplo, consulte esta [solução](https://github.com/danielsollondon/azvmimagebuilder/tree/master/solutions/4_Using_ENV_Variables#using-environment-variables-and-parameters-with-image-builder)ou use AZ PowerShell.


#### <a name="storage-account"></a>Conta de armazenamento

Selecione a conta de armazenamento que você criou nos pré-requisitos. Se você não o vir na lista, o Image Builder não terá permissões para ele.

Quando a compilação for iniciada, o construtor de imagem criará um contêiner chamado `imagebuilder-vststask` . O contêiner é onde os artefatos de compilação do repositório são armazenados.

> [!NOTE]
> Você precisa excluir manualmente a conta de armazenamento ou o contêiner após cada compilação.
>

### <a name="distribute"></a>Distribuir

Há 3 tipos de distribuição com suporte.

#### <a name="managed-image"></a>Imagem Gerenciada

* Identificação
    ```bash
    /subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/images/<imageName>
    ```

* Locais

#### <a name="azure-shared-image-gallery"></a>Galeria de imagens compartilhadas do Azure

A Galeria de imagens compartilhadas já **deve** existir.

* Identificação 
    ```bash
    /subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<imageDefName>
    ```

* Regiões: lista de regiões, separadas por vírgulas. Por exemplo, westus, orientem, centralus

#### <a name="vhd"></a>VHD 

Você não pode passar nenhum valor para isso, o construtor de imagem emitirá o VHD para o grupo de recursos do construtor de imagem temporário, `IT_<DestinationResourceGroup>_<TemplateName>` no contêiner *VHDs* . Quando você inicia a compilação da versão, o construtor de imagem emite logs. Quando ele for concluído, ele emitirá a URL do VHD.

### <a name="optional-settings"></a>Configurações opcionais

* [Tamanho da VM](image-builder-json.md#vmprofile) -você pode substituir o tamanho da VM, do padrão de *Standard_D1_v2*. Você pode substituir para reduzir o tempo total de personalização ou porque deseja criar as imagens que dependem de determinados tamanhos de VM, como GPU/HPC, etc.

## <a name="how-it-works"></a>Como ele funciona

Quando você cria a versão, a tarefa cria um contêiner na conta de armazenamento, chamado *imagebuilder-vststask*. Ele zips e carrega seus artefatos de compilação e cria um token SAS para o arquivo zip.

A tarefa usa as propriedades passadas para a tarefa para criar o artefato do modelo do Image Builder. A tarefa faz o seguinte:
* Baixa o arquivo zip do artefato de compilação e quaisquer outros scripts associados. Os arquivos são salvos em uma conta de armazenamento no grupo de recursos Temporary Image Builder `IT_<DestinationResourceGroup>_<TemplateName>` .
* Cria um *t_* prefixado de modelo e um inteiro monotônico de 10 dígitos. O modelo é salvo no grupo de recursos selecionado. O modelo existe para a duração da compilação no grupo de recursos. 

Saída de exemplo:

```text
start reading task parameters...
found build at:  /home/vsts/work/r1/a/_ImageBuilding/webapp
end reading parameters
getting storage account details for aibstordot1556933914
created archive /home/vsts/work/_temp/temp_web_package_21475337782320203.zip
Source for image:  { type: 'SharedImageVersion',
  imageVersionId: '/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<imageDefName>/versions/<imgVersionNumber>' }
template name:  t_1556938436xxx
starting put template...
```

Quando o Build da imagem é iniciado, o status de execução é relatado nos logs de liberação:

```text
starting run template...
```

Quando a compilação da imagem for concluída, você verá uma saída semelhante ao seguinte texto:

```text
2019-05-06T12:49:52.0558229Z starting run template...
2019-05-06T13:36:33.8863094Z run template:  Succeeded
2019-05-06T13:36:33.8867768Z getting runOutput for  SharedImage_distribute
2019-05-06T13:36:34.6652541Z ==============================================================================
2019-05-06T13:36:34.6652925Z ## task output variables ##
2019-05-06T13:36:34.6658728Z $(imageUri) =  /subscriptions/<subscriptionID>/resourceGroups/aibwinsig/providers/Microsoft.Compute/galleries/my22stSIG/images/winWAppimages/versions/0.23760.13763
2019-05-06T13:36:34.6659989Z ==============================================================================
2019-05-06T13:36:34.6663500Z deleting template t_1557146959485...
2019-05-06T13:36:34.6673713Z deleting storage blob imagebuilder-vststask\webapp/18-1/webapp_1557146958741.zip
2019-05-06T13:36:34.9786039Z blob imagebuilder-vststask\webapp/18-1/webapp_1557146958741.zip is deleted
2019-05-06T13:38:37.4884068Z delete template:  Succeeded
```

O modelo de imagem e `IT_<DestinationResourceGroup>_<TemplateName>` é excluído.

Você pode usar a variável do VSTS ' $ (imageUri) ' e usá-la na próxima tarefa ou simplesmente usar o valor e compilar uma VM.

## <a name="output-devops-variables"></a>Variáveis DevOps de saída

Pub/offer/SKU/versão da imagem do Marketplace de origem:
* $ (pirPublisher)
* $ (pirOffer)
* $ (pirSku)
* $ (pirVersion)

URI da imagem-o ResourceId da imagem distribuída:
* $ (imageUri)

## <a name="faq"></a>Perguntas frequentes

### <a name="can-i-use-an-existing-image-template-i-have-already-created-outside-of-devops"></a>Posso usar um modelo de imagem existente que já criei, fora do DevOps?

Atualmente, não neste momento.

### <a name="can-i-specify-the-image-template-name"></a>Posso especificar o nome do modelo de imagem?

Não. Um nome de modelo exclusivo é usado e, em seguida, excluído.

### <a name="the-image-builder-failed-how-can-i-troubleshoot"></a>Falha no construtor de imagem. Como posso solucionar problemas?

Se houver uma falha de compilação, a tarefa DevOps não excluirá o grupo de recursos de preparo. Você pode acessar o grupo de recursos de preparo que contém o log de compilação personalizada.

Você verá um erro no log do DevOps para a tarefa do construtor de imagens de VM e verá o local de personalização. log. Por exemplo:

:::image type="content" source="./media/image-builder-devops-task/devops-task-error.png" alt-text="Exemplo de erro de tarefa DevOps que mostra uma falha.":::

Para obter mais informações sobre solução de problemas, consulte [solucionar problemas do serviço do construtor de imagem do Azure](image-builder-troubleshoot.md). 

Depois de investigar a falha, você pode excluir o grupo de recursos de preparo. Primeiro, exclua o artefato de recurso do modelo de imagem. O artefato é prefixado com *t_* e pode ser encontrado no log de compilação da tarefa DevOps:

```text
...
Source for image:  { type: 'SharedImageVersion',
  imageVersionId: '/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<imageDefName>/versions/<imgVersionNumber>' }
...
template name:  t_1556938436xxx
...

```

O artefato de recurso do modelo de imagem está no grupo de recursos especificado inicialmente na tarefa. Quando você terminar de solucionar o problema, exclua o artefato. Se estiver excluindo usando o portal do Azure, dentro do grupo de recursos, selecione **Mostrar tipos ocultos** para exibir o artefato.


## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte [visão geral do construtor de imagens do Azure](../image-builder-overview.md).
