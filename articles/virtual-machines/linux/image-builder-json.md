---
title: Criar um modelo do construtor de imagens do Azure (visualização)
description: Saiba como criar um modelo para usar com o construtor de imagem do Azure.
author: cynthn
ms.author: cynthn
ms.date: 05/10/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: 065962614d0b85c4c50f86bef0b610c9b3577e07
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68248146"
---
# <a name="preview-create-an-azure-image-builder-template"></a>Visualização: Criar um modelo do construtor de imagens do Azure 

O construtor de imagens do Azure usa um arquivo. JSON para passar informações para o serviço do construtor de imagem. Neste artigo, vamos abordar as seções do arquivo JSON, para que você possa criar o seu próprio. Para ver exemplos de arquivos. JSON completos, consulte o [GitHub do Azure Image Builder](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts).

Este é o formato de modelo básico:

```json
 { 
    "type": "Microsoft.VirtualMachineImages/imageTemplates", 
    "apiVersion": "2019-05-01-preview", 
    "location": "<region>", 
    "tags": {
        "<name": "<value>",
        "<name>": "<value>"
             }
    "identity":{},           
    "dependsOn": [], 
    "properties": { 
        "buildTimeoutInMinutes": <minutes>, 
        "build": {}, 
        "customize": {}, 
        "distribute": {} 
      } 
 } 
```



## <a name="type-and-api-version"></a>Tipo e versão da API

O `type` é o tipo de recurso, que deve `"Microsoft.VirtualMachineImages/imageTemplates"`ser. O `apiVersion` será alterado ao longo do tempo conforme a API é alterada, `"2019-05-01-preview"` mas deve ser para visualização.

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2019-05-01-preview",
```

## <a name="location"></a>Location

O local é a região em que a imagem personalizada será criada. Para a visualização do Image Builder, há suporte para as seguintes regiões:

- East US
- Leste dos EUA 2
- Centro-oeste dos EUA
- Oeste dos EUA
- Oeste dos EUA 2


```json
    "location": "<region>",
```
    
## <a name="depends-on-optional"></a>Depende de (opcional)

Essa seção opcional pode ser usada para garantir que as dependências sejam concluídas antes de continuar. 

```json
    "dependsOn": [],
```

Para obter mais informações, consulte [definir dependências de recurso](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies#dependson).

## <a name="identity"></a>Identidade
Por padrão, o Image Builder dá suporte ao uso de scripts ou à cópia de arquivos de vários locais, como o GitHub e o armazenamento do Azure. Para usá-los, eles devem ser acessíveis publicamente.

Você também pode usar uma identidade gerenciada atribuída pelo usuário do Azure, definida por você, para permitir que o construtor de imagens acesse o armazenamento do Azure, desde que a identidade tenha recebido um mínimo de ' leitor de dados de blob de armazenamento ' na conta de armazenamento do Azure. Isso significa que você não precisa tornar os blobs de armazenamento acessíveis externamente ou configurar tokens SAS.


```json
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<imgBuilderId>": {}
        }
        },
```

Para obter um exemplo completo, consulte [usar uma identidade gerenciada atribuída pelo usuário do Azure para acessar arquivos no armazenamento do Azure](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

Suporte ao construtor de imagem para uma identidade atribuída pelo usuário: • dá suporte apenas a uma única identidade • não dá suporte a nomes de domínio personalizados

Para saber mais, confira [o que são identidades gerenciadas para recursos do Azure?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).
Para obter mais informações sobre como implantar esse recurso, consulte [Configurar identidades gerenciadas para recursos do Azure em uma VM do Azure usando CLI do Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity).

## <a name="properties-source"></a>Propriedades: origem

A `source` seção contém informações sobre a imagem de origem que será usada pelo Image Builder.

A API requer um ' SourceType ' que define a origem para a compilação da imagem, atualmente há três tipos:
- ISO – Use quando a origem for um RHEL ISO.
- PlatformImage-indica que a imagem de origem é uma imagem do Marketplace.
- ManagedImage-use isso ao iniciar em uma imagem gerenciada normal.
- SharedImageVersion-isso é usado quando você está usando uma versão de imagem em uma galeria de imagens compartilhada como a origem.

### <a name="iso-source"></a>Origem ISO

O construtor de imagens do Azure dá suporte apenas ao uso publicado Red Hat Enterprise Linux 7. x Binary DVD ISOs, para visualização. O Image Builder dá suporte a:
- RHEL 7.3 
- RHEL 7,4 
- RHEL 7.5 
 
```json
"source": {
       "type": "ISO",
       "sourceURI": "<sourceURI from the download center>",
       "sha256Checksum": "<checksum associated with ISO>"
}
```

Para obter os `sourceURI` valores `sha256Checksum` e, vá para `https://access.redhat.com/downloads` selecionar o produto **Red Hat Enterprise Linux**e uma versão com suporte. 

Na lista de **instaladores e imagens do Red Hat Enterprise Linux Server**, você precisa copiar o link para o DVD binário Red Hat Enterprise Linux 7. x e a soma de verificação.

> [!NOTE]
> Os tokens de acesso dos links são atualizados em intervalos frequentes, portanto, sempre que você quiser enviar um modelo, deverá verificar se o endereço do link do RH foi alterado.
 
### <a name="platformimage-source"></a>Origem do PlatformImage 
O construtor de imagens do Azure dá suporte às seguintes imagens do Azure Marketplace:
* Ubuntu 18.04
* Ubuntu 16.04
* RHEL 7,6
* CentOS 7,6
* Windows 2016
* Windows 2019

```json
        "source": {
            "type": "PlatformImage",
                "publisher": "Canonical",
                "offer": "UbuntuServer",
                "sku": "18.04-LTS",
                "version": "18.04.201903060"
        },
```


As propriedades aqui são as mesmas usadas para criar VMs, usando AZ CLI, execute o seguinte para obter as propriedades: 
 
```azurecli-interactive
az vm image list -l westus -f UbuntuServer -p Canonical --output table –-all 
```

> [!NOTE]
> A versão não pode ser ' mais recente ', você deve usar o comando acima para obter um número de versão. 

### <a name="managedimage-source"></a>Origem do ManagedImage

Define a imagem de origem como uma imagem gerenciada existente de um VHD ou VM generalizado. A imagem gerenciada de origem deve ser de um sistema operacional com suporte e estar na mesma região que o seu modelo do Azure Image Builder. 

```json
        "source": { 
            "type": "ManagedImage", 
                "imageId": "/subscriptions/<subscriptionId>/resourceGroups/{destinationResourceGroupName}/providers/Microsoft.Compute/images/<imageName>"
        }
```

O `imageId` deve ser o ResourceId da imagem gerenciada. Use `az image list` para listar imagens disponíveis.


### <a name="sharedimageversion-source"></a>Origem do SharedImageVersion
Define a imagem de origem como uma versão de imagem existente em uma galeria de imagens compartilhada. A versão da imagem deve ser de um sistema operacional com suporte e a imagem deve ser replicada na mesma região que o seu modelo do construtor de imagem do Azure. 

```json
        "source": { 
            "type": "SharedImageVersion", 
            "imageVersionID": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/p  roviders/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageDefinitionName/versions/<imageVersion>" 
   } 
```

O `imageVersionId` deve ser o ResourceId da versão da imagem. Use a [lista de versão de imagem AZ SIG](/cli/azure/sig/image-version#az-sig-image-version-list) para listar versões de imagem.

## <a name="properties-customize"></a>Propriedades: personalizar


O Image Builder dá suporte a vários ' customers '. Os personalizadores são funções que são usadas para personalizar a imagem, como a execução de scripts ou a reinicialização de servidores. 

Ao usar `customize`: 
- Você pode usar vários personalizadores, mas eles devem ter um exclusivo `name`.
- Os personalizadores são executados na ordem especificada no modelo.
- Se um personalizador falhar, o componente de personalização inteiro falhará e relatará um erro.
- Considere a quantidade de tempo que a criação de imagem exigirá e ajuste a propriedade ' buildTimeoutInMinutes ' para permitir que o construtor de imagem seja concluído o tempo suficiente.
- É altamente recomendável que você teste o script cuidadosamente antes de usá-lo em um modelo. A depuração do script em sua própria VM será mais fácil.
- Não coloque dados confidenciais nos scripts. 
- Os locais de script precisam ser acessíveis publicamente, a menos que você esteja usando o [MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

```json
        "customize": [
            {
                "type": "Shell",
                "name": "<name>",
                "scriptUri": "<path to script>"
            },
            {
                "type": "Shell",
                "name": "<name>",
                "inline": [
                    "<command to run inline>",
                ]
            }

        ],
```     

 
A seção Personalizar é uma matriz. O construtor de imagens do Azure será executado por meio dos personalizadores em ordem sequencial. Qualquer falha em qualquer personalizador irá falhar o processo de compilação. 
 
 
### <a name="shell-customizer"></a>Personalizador de Shell

O personalizador de shell dá suporte a scripts de Shell em execução, eles devem ser acessíveis publicamente para que o IB possa acessá-los.

```json
    "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "scriptUri": "<link to script>"        
        }, 
    ], 
        "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "inline": "<commands to run>"
        }, 
    ], 
```

Suporte do so: Linux 
 
Personalizar propriedades:

- **tipo** – Shell 
- **nome** -nome para acompanhar a personalização 
- **scriptUri** -URI para o local do arquivo 
- matriz embutida de comandos do Shell, separados por vírgulas.
 
> [!NOTE]
> Ao executar o personalizador de shell com a fonte ISO do RHEL, você precisa garantir que seu primeiro Shell de personalização manipule o registro com um servidor de direitos do Red Hat antes que ocorra qualquer personalização. Depois que a personalização for concluída, o script deverá cancelar o registro com o servidor de direitos.

### <a name="windows-restart-customizer"></a>Personalizador de reinicialização do Windows 
O personalizador de reinicialização permite reiniciar uma VM do Windows e aguardar que ela volte a ficar online, permitindo que você instale o software que exige uma reinicialização.  

```json 
     "customize": [ 
            "type{ ": "WindowsRestart", 
            "restartCommand": "shutdown /r /f /t 0 /c", 
            "restartCheckCommand": "echo Azure-Image-Builder-Restarted-the-VM  > buildArtifacts/azureImageBuilderRestart.txt",
            "restartTimeout": "5m"
         }],
```

Suporte do so: Windows
 
Personalizar propriedades:
- **Tipo**: WindowsRestart
- **restartCommand** -comando para executar a reinicialização (opcional). O padrão é `'shutdown /r /f /t 0 /c \"packer restart\"'`.
- **restartCheckCommand** – comando para verificar se a reinicialização foi bem-sucedida (opcional). 
- **restartTimeout** -tempo limite de reinicialização especificado como uma cadeia de magnitude e unidade. Por exemplo, `5m` (5 minutos) ou `2h` (2 horas). O padrão é: '5m'


### <a name="powershell-customizer"></a>Personalizador do PowerShell 
O personalizador de shell dá suporte à execução de scripts do PowerShell e ao comando embutido, os scripts devem ser publicamente acessíveis para que o IB possa acessá-los.

```json 
     "customize": [
        { 
             "type": "PowerShell",
             "name":   "<name>",  
             "scriptUri": "<path to script>" 
        },  
        { 
             "type": "PowerShell", 
             "name": "<name>", 
             "inline": "<PowerShell syntax to run>", 
             "valid_exit_codes": "<exit code>" 
         } 
    ], 
```

Suporte do so: Windows e Linux

Personalizar propriedades:

- **tipo** – PowerShell.
- **scriptUri** -URI para o local do arquivo de script do PowerShell. 
- **embutido** – comandos embutidos a serem executados, separados por vírgulas.
- **valid_exit_codes** – códigos opcionais válidos que podem ser retornados do comando de script/embutido, isso evitará a falha relatada do comando script/embutido.

### <a name="file-customizer"></a>Personalizador de arquivo

O personalizador de arquivo permite que o construtor de imagens Baixe um arquivo de um GitHub ou armazenamento do Azure. Se você tiver um pipeline de compilação de imagem que se baseia em artefatos de compilação, você pode definir o personalizador de arquivo para baixar do compartilhamento de compilação e mover os artefatos para a imagem.  

```json
     "customize": [ 
         { 
            "type": "File", 
             "name": "<name>", 
             "sourceUri": "<source location>",
             "destination": "<destination>" 
         }
     ]
```

Suporte do so: Linux e Windows 

Propriedades do personalizador de arquivo:

- **SourceUri** – um ponto de extremidade de armazenamento acessível, que pode ser o GitHub ou o armazenamento do Azure. Você só pode baixar um arquivo, não um diretório inteiro. Se você precisar baixar um diretório, use um arquivo compactado e descompacte-o usando os personalizadores do Shell ou do PowerShell. 
- **destino** – este é o caminho de destino completo e o nome do arquivo. Qualquer caminho e subdiretórios referenciados devem existir, use os personalizados do Shell ou do PowerShell para configurá-los com antecedência. Você pode usar os personalizadores de script para criar o caminho. 

Isso é suportado por diretórios do Windows e caminhos do Linux, mas há algumas diferenças: 
- SO Linux – o único criador de imagem de caminho pode gravar em é/tmp.
- Windows – nenhuma restrição de caminho, mas o caminho deve existir.
 
 
Se houver um erro ao tentar baixar o arquivo, ou colocá-lo em um diretório especificado, a etapa de personalização falhará e isso estará no arquivo customization. log.

>> Anotações! O personalizador de arquivos é adequado apenas para downloads de arquivos pequenos, < 20 MB. Para downloads de arquivos maiores, use um script ou comando embutido, o código de uso para baixar arquivos, `wget` como `curl`Linux ou, `Invoke-WebRequest`Windows,.

Os arquivos no Personalizador de arquivo podem ser baixados do armazenamento do Azure usando o [MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

### <a name="generalize"></a>Generalizar 
Por padrão, o construtor de imagens do Azure também executará o código de ' desprovisionamento ' no final de cada fase de personalização de imagem, para ' generalizar ' a imagem. Generalizar é um processo em que a imagem é configurada para que possa ser reutilizada para criar várias VMs. Para VMs do Windows, o construtor de imagem do Azure usa Sysprep. Para o Linux, o Azure Image Builder executa ' waagent-deprovision '. 

Os comandos que os usuários do construtor de imagens para generalizar podem não ser adequados para todas as situações, portanto, o construtor de imagens do Azure permitirá que você personalize esse comando, se necessário. 

Se você estiver migrando a personalização existente e estiver usando diferentes comandos Sysprep/waagent, poderá usar os comandos genéricos do Image Builder e, se a criação da VM falhar, use seus próprios comandos Sysprep ou waagent.

Se o construtor de imagem do Azure criar uma imagem personalizada do Windows com êxito e você criar uma VM a partir dela, descubra que a criação da VM falha ou não é concluída com êxito, você precisará examinar a documentação do Sysprep do Windows Server ou gerar uma solicitação de suporte com o Equipe de suporte do Windows Server Sysprep Customer Services, que pode solucionar problemas e avisar sobre o uso correto do Sysprep.


#### <a name="default-sysprep-command"></a>Comando Sysprep padrão
```powershell
echo '>>> Waiting for GA to start ...'
while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }
while ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running') { Start-Sleep -s 5 }
while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }
echo '>>> Sysprepping VM ...'
if( Test-Path $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml ){ rm $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml -Force} & $Env:SystemRoot\\System32\\Sysprep\\Sysprep.exe /oobe /generalize /quiet /quit
while($true) { $imageState = Get-ItemProperty HKLM:\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Setup\\State | Select ImageState; if($imageState.ImageState -ne 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { Write-Output $imageState.ImageState; Start-Sleep -s 5  } else { break } }
```
#### <a name="default-linux-deprovision-command"></a>Comando padrão de desprovisionamento do Linux

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

#### <a name="overriding-the-commands"></a>Substituindo os comandos
Para substituir os comandos, use os provisionadores do script PowerShell ou Shell para criar os arquivos de comando com o nome exato do arquivo e coloque-os nos diretórios corretos:

* Windows: c:\DeprovisioningScript.ps1
* Linux: /tmp/DeprovisioningScript.sh

O Image Builder lerá esses comandos, eles serão gravados nos logs do AIB, ' customization. log '. Consulte [solução de problemas](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-logs) de como coletar logs.
 
## <a name="properties-distribute"></a>Propriedades: distribuir

O construtor de imagem do Azure dá suporte a três destinos de distribuição: 

- **managedImage** -imagem gerenciada.
- **sharedImage** -Galeria de imagens compartilhada.
- **VHD** -VHD em uma conta de armazenamento.

Você pode distribuir uma imagem para ambos os tipos de destino na mesma configuração, consulte [exemplos](https://github.com/danielsollondon/azvmimagebuilder/blob/7f3d8c01eb3bf960d8b6df20ecd5c244988d13b6/armTemplates/azplatform_image_deploy_sigmdi.json#L80).

Como você pode ter mais de um destino para distribuir, o Image Builder mantém um estado para cada destino de distribuição que pode ser acessado consultando o `runOutputName`.  O `runOutputName` é um objeto que você pode consultar após a distribuição para obter informações sobre essa distribuição. Por exemplo, você pode consultar o local do VHD ou regiões em que a versão da imagem foi replicada. Essa é uma propriedade de cada destino de distribuição. O `runOutputName` deve ser exclusivo para cada destino de distribuição.
 
### <a name="distribute-managedimage"></a>Distribuir: managedImage

A saída da imagem será um recurso de imagem gerenciada.

```json
"distribute": [
        {
"type":"managedImage",
       "imageId": "<resource ID>",
       "location": "<region>",
       "runOutputName": "<name>",
       "artifactTags": {
            "<name": "<value>",
             "<name>": "<value>"
               }
         }]
```
 
Distribuir Propriedades:
- **tipo** – managedImage 
- **imageid** – ID de recurso da imagem de destino, formato esperado:\</subscriptions/SubscriptionId >\</resourceGroups/destinationResourceGroupName >\< /Providers/Microsoft.Compute/images/ imageName >
- **local** -local da imagem gerenciada.  
- **runOutputName** – nome exclusivo para identificar a distribuição.  
- **artifactTags** -marcas de par de valor de chave especificado pelo usuário opcional.
 
 
> [!NOTE]
> O grupo de recursos de destino deve existir.
> Se você quiser que a imagem seja distribuída para uma região diferente, ela aumentará o tempo de implantação. 

### <a name="distribute-sharedimage"></a>Distribute: sharedImage 
A Galeria de imagens compartilhadas do Azure é um novo serviço de gerenciamento de imagens que permite o gerenciamento de replicação de região de imagem, controle de versão e compartilhamento de imagens personalizadas. O construtor de imagens do Azure dá suporte à distribuição com esse serviço, para que você possa distribuir imagens para regiões com suporte pelas galerias de imagens compartilhadas. 
 
Uma galeria de imagens compartilhada é composta de: 
 
- Galeria-contêiner para várias imagens compartilhadas. Uma galeria é implantada em uma região.
- Definições de imagem – um agrupamento conceitual para imagens. 
- Versões de imagem-esse é um tipo de imagem usado para implantar uma VM ou um conjunto de dimensionamento. As versões de imagem podem ser replicadas para outras regiões em que as VMs precisam ser implantadas.
 
Antes de distribuir para a Galeria de imagens, você deve criar uma galeria e uma definição de imagem, ver [imagens](shared-images.md)compartilhadas. 

```json
{
     "type": "sharedImage",
     "galleryImageId": “<resource ID>”,
     "runOutputName": "<name>",
     "artifactTags": {
          "<name": "<value>",
           "<name>": "<value>"
             }
     "replicationRegions": [
        "<region where the gallery is deployed>",
        "<region>"
    ]}
``` 

Distribuir Propriedades para galerias de imagens compartilhadas:

- **tipo** -sharedImage  
- **galleryImageId** – ID da Galeria de imagens compartilhada. O formato é:/subscriptions/\<SubscriptionId >/resourceGroups/\<resourceGroupName >/Providers/Microsoft.Compute/Galleries/\<sharedImageGalleryName >/images/\< > imageGalleryName.
- **runOutputName** – nome exclusivo para identificar a distribuição.  
- **artifactTags** -marcas de par de valor de chave especificado pelo usuário opcional.
- **replicationRegions** -matriz de regiões para replicação. Uma das regiões deve ser a região onde a galeria é implantada.
 
> [!NOTE]
> Você pode usar o construtor de imagem do Azure em uma região diferente para a Galeria, mas o serviço do construtor de imagens do Azure precisará transferir a imagem entre os datacenters e isso levará mais tempo. O Image Builder fará a versão da imagem automaticamente, com base em um inteiro monotônico, você não poderá especificá-la no momento. 

### <a name="distribute-vhd"></a>Fornecer VHD  
Você pode gerar uma saída para um VHD. Em seguida, você pode copiar o VHD e usá-lo para publicar no Azure MarketPlace ou usar com Azure Stack.  

```json
 { 
     "type": "VHD",
     "runOutputName": "<VHD name>",
     "tags": {
          "<name": "<value>",
           "<name>": "<value>"
             }
 }
```
 
Suporte do so: Windows e Linux

Distribuir parâmetros de VHD:

- **tipo** -VHD.
- **runOutputName** – nome exclusivo para identificar a distribuição.  
- **marcas** – marcas de par de valor de chave especificado pelo usuário opcional.
 
O construtor de imagens do Azure não permite que o usuário especifique um local de conta de armazenamento, mas você pode consultar `runOutputs` o status do para obter o local.  

```azurecli-interactive
az resource show \
   --ids "/subscriptions/$subscriptionId/resourcegroups/<imageResourceGroup>/providers/Microsoft.VirtualMachineImages/imageTemplates/<imageTemplateName>/runOutputs/<runOutputName>"  | grep artifactUri 
```

> [!NOTE]
> Depois que o VHD tiver sido criado, copie-o para um local diferente, assim que possível. O VHD é armazenado em uma conta de armazenamento no grupo de recursos temporário criado quando o modelo de imagem é enviado para o serviço do construtor de imagem do Azure. Se você excluir o modelo de imagem, o VHD será perdido. 
 
## <a name="next-steps"></a>Próximas etapas

Há arquivos. JSON de exemplo para diferentes cenários no [GitHub do Azure Image Builder](https://github.com/danielsollondon/azvmimagebuilder).
 
 
 
 
 
 
 
 
 
 
