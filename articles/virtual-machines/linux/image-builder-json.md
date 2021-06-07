---
title: Criar um modelo do Construtor de Imagens do Azure (versão prévia)
description: Saiba como criar um modelo para usar com o Construtor de Imagens do Azure.
author: danielsollondon
ms.author: danis
ms.date: 03/02/2021
ms.topic: reference
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.reviewer: cynthn
ms.openlocfilehash: aaaabe758b036335062907c8e5549ae876c63997
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104594726"
---
# <a name="preview-create-an-azure-image-builder-template"></a>Visualização: Criar um modelo do Construtor de Imagens do Azure 

O Construtor de Imagens do Azure usa um arquivo .json para passar informações para o serviço do Construtor de Imagens. Neste artigo, vamos abordar as seções do arquivo json para que você possa criar o seu próprio. Para ver exemplos completos de arquivos .json, confira o [Construtor de Imagens do Azure no GitHub](https://github.com/Azure/azvmimagebuilder/tree/main/quickquickstarts).

Este é o formato de modelo básico:

```json
 { 
    "type": "Microsoft.VirtualMachineImages/imageTemplates", 
    "apiVersion": "2020-02-14", 
    "location": "<region>", 
    "tags": {
        "<name": "<value>",
        "<name>": "<value>"
     },
    "identity":{},           
    "dependsOn": [], 
    "properties": { 
        "buildTimeoutInMinutes": <minutes>, 
        "vmProfile": 
            {
            "vmSize": "<vmSize>",
            "osDiskSizeGB": <sizeInGB>,
            "vnetConfig": {
                "subnetId": "/subscriptions/<subscriptionID>/resourceGroups/<vnetRgName>/providers/Microsoft.Network/virtualNetworks/<vnetName>/subnets/<subnetName>"
                }
            },
        "source": {}, 
        "customize": {}, 
        "distribute": {} 
      } 
 } 
```



## <a name="type-and-api-version"></a>Tipo e versão da API

O `type` é o tipo de recurso, que deve ser `"Microsoft.VirtualMachineImages/imageTemplates"`. O `apiVersion` será alterado ao longo do tempo conforme a API for alterada, mas deve ser `"2020-02-14"` para a versão prévia.

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2020-02-14",
```

## <a name="location"></a>Location

O local é a região em que a imagem personalizada será criada. Para a versão prévia do Construtor de Imagens, há suporte para as seguintes regiões:

- Leste dos EUA
- Leste dos EUA 2
- Centro-Oeste dos EUA
- Oeste dos EUA
- Oeste dos EUA 2
- Norte da Europa
- Europa Ocidental


```json
    "location": "<region>",
```
## <a name="vmprofile"></a>vmProfile
Por padrão, o Construtor de Imagens usará uma VM de build "Standard_D1_v2". Você pode substituir isso. Por exemplo, se quiser personalizar uma imagem para uma VM GPU, você precisará de um tamanho de VM de GPU. Isso é opcional.

```json
 {
    "vmSize": "Standard_D1_v2"
 },
```

## <a name="osdisksizegb"></a>osDiskSizeGB

Por padrão, o Construtor de Imagens não vai alterar o tamanho da imagem, ele usará o tamanho da imagem de origem. Você **só** pode aumentar o tamanho do disco do sistema operacional (Win e Linux), isso é opcional, e o valor 0 significa deixar o mesmo tamanho da imagem de origem. Não é possível reduzir o tamanho do disco do sistema operacional para menor do que o tamanho da imagem de origem.

```json
 {
    "osDiskSizeGB": 100
 },
```

## <a name="vnetconfig"></a>vnetConfig
Se você não especificar nenhuma propriedade da VNET, o Construtor de Imagens criará a própria VNET, IP público e NSG. O IP público é usado para que o serviço se comunique com a VM de build. No entanto, se você não quiser um IP público ou quiser que o Construtor de Imagens tenha acesso aos recursos da VNET existentes, como servidores de configuração (DSC, Chef, Puppet, Ansible), compartilhamentos de arquivos etc., você poderá especificar uma VNET. Para obter mais informações, confira a [documentação de rede](image-builder-networking.md); isso é opcional.

```json
    "vnetConfig": {
        "subnetId": "/subscriptions/<subscriptionID>/resourceGroups/<vnetRgName>/providers/Microsoft.Network/virtualNetworks/<vnetName>/subnets/<subnetName>"
        }
    }
```
## <a name="tags"></a>Marcas

Esses são pares de chave/valor que você pode especificar para a imagem gerada.

## <a name="depends-on-optional"></a>Depende de (opcional)

Essa seção opcional pode ser usada para garantir que as dependências sejam concluídas antes de continuar. 

```json
    "dependsOn": [],
```

Para obter mais informações, confira [Definir dependências de recurso](../../azure-resource-manager/templates/define-resource-dependency.md#dependson).

## <a name="identity"></a>Identidade

Necessário-para o construtor de imagem ter permissões para ler/gravar imagens, leia em scripts do armazenamento do Azure você deve criar uma identidade de User-Assigned do Azure, que tem permissões para os recursos individuais. Para obter detalhes sobre como as permissões do Image Builder funcionam e as etapas relevantes, consulte a [documentação](image-builder-user-assigned-identity.md).


```json
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<imgBuilderId>": {}
        }
        },
```


Suporte ao construtor de imagem para uma identidade de User-Assigned:
* Dá suporte apenas a uma única identidade
* Não dá suporte a nomes de domínio personalizados

Para saber mais, confira [O que são identidades gerenciadas para recursos do Azure?](../../active-directory/managed-identities-azure-resources/overview.md).
Para obter mais informações sobre como implantar esse recurso, confira [Configurar identidades gerenciadas para recursos do Azure em uma VM do Azure usando a CLI do Azure](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity).

## <a name="properties-source"></a>Propriedades: origem

A seção `source` contém informações sobre a imagem de origem que será usada pelo Construtor de Imagens. Atualmente, o Image Builder dá suporte apenas para a criação de imagens de geração do Hyper-V (GEN1) 1 para a Galeria de imagens compartilhada do Azure (SIG) ou a imagem gerenciada. Se você quiser criar imagens Gen2, precisará usar uma imagem Gen2 de origem e distribuir para o VHD. Depois, você precisará criar uma imagem gerenciada do VHD e inseri-la no SIG como uma imagem Gen2.

A API requer um 'SourceType' que define a origem para o build da imagem. No momento, há três tipos:
- PlatformImage – indica que a imagem de origem é uma imagem do Marketplace.
- ManagedImage – use ao iniciar em uma imagem gerenciada normal.
- SharedImageVersion – é usado quando você está usando uma versão de imagem em uma Galeria de Imagens Compartilhadas como a origem.


> [!NOTE]
> Ao usar imagens personalizadas do Windows existentes, você pode executar o comando Sysprep até 8 vezes em uma única imagem do Windows, para obter mais informações, consulte a documentação do [Sysprep](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation#limits-on-how-many-times-you-can-run-sysprep) .

### <a name="platformimage-source"></a>Origem da PlatformImage 
O Construtor de Imagens do Azure dá suporte ao Windows Server e ao cliente e às imagens do Azure Marketplace do Linux, confira [aqui](../image-builder-overview.md#os-support) a lista completa. 

```json
        "source": {
            "type": "PlatformImage",
                "publisher": "Canonical",
                "offer": "UbuntuServer",
                "sku": "18.04-LTS",
                "version": "latest"
        },
```


As propriedades aqui são as mesmas usadas para criar VMs. Usando a CLI do AZ, execute o seguinte para obter as propriedades: 
 
```azurecli-interactive
az vm image list -l westus -f UbuntuServer -p Canonical --output table –-all 
```

Você pode usar 'Latest' na versão. A versão é avaliada quando o build da imagem ocorre, não quando o modelo é enviado. Se você usar essa funcionalidade com o destino da Galeria de Imagens Compartilhadas, poderá evitar reenviar o modelo e executar novamente o build da imagem a intervalos para que suas imagens sejam recriadas com base em imagens mais recentes.

#### <a name="support-for-market-place-plan-information"></a>Suporte para informações do plano do Market Place
Você também pode especificar informações de plano, por exemplo:
```json
    "source": {
        "type": "PlatformImage",
        "publisher": "RedHat",
        "offer": "rhel-byos",
        "sku": "rhel-lvm75",
        "version": "latest",
        "planInfo": {
            "planName": "rhel-lvm75",
            "planProduct": "rhel-byos",
            "planPublisher": "redhat"
       }
```
### <a name="managedimage-source"></a>Origem da ManagedImage

Define a imagem de origem como uma imagem gerenciada existente de um VHD ou VM generalizado.

> [!NOTE]
> A imagem gerenciada de origem deve ser de um sistema operacional com suporte e a imagem deve estar na mesma região que o seu modelo do Azure Image Builder. 

```json
        "source": { 
            "type": "ManagedImage", 
                "imageId": "/subscriptions/<subscriptionId>/resourceGroups/{destinationResourceGroupName}/providers/Microsoft.Compute/images/<imageName>"
        }
```

O `imageId` deve ser a ResourceId da imagem gerenciada. Use `az image list` para listar as imagens disponíveis.


### <a name="sharedimageversion-source"></a>Origem da SharedImageVersion
Define a imagem de origem como uma versão de imagem existente em uma Galeria de Imagens Compartilhadas.

> [!NOTE]
> A imagem gerenciada de origem deve ser de um sistema operacional com suporte e a imagem deve estar na mesma região que o seu modelo do Azure Image Builder, caso contrário, replique a versão da imagem para a região de modelo do Image Builder.


```json
        "source": { 
            "type": "SharedImageVersion", 
            "imageVersionID": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/p  roviders/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageDefinitionName/versions/<imageVersion>" 
   } 
```

O `imageVersionId` deve ser a ResourceId da versão da imagem. Use [az sig image-version list](/cli/azure/sig/image-version#az-sig-image-version-list) para listar as versões da imagem.


## <a name="properties-buildtimeoutinminutes"></a>Propriedades: buildTimeoutInMinutes

Por padrão, o Construtor de Imagens será executado por 240 minutos. Depois disso, ele atingirá o tempo limite e será interrompido, independentemente de o build da imagem ter sido concluído ou não. Se o tempo limite for atingido, você verá um erro semelhante a este:

```text
[ERROR] Failed while waiting for packerizer: Timeout waiting for microservice to
[ERROR] complete: 'context deadline exceeded'
```

Se você não especificar um valor de buildTimeoutInMinutes ou defini-lo como 0, será usado o valor padrão. Você pode aumentar ou diminuir o valor até o máximo de 960 minutos (16 horas). Para o Windows, não recomendamos definir esse valor como inferior a 60 minutos. Se você acreditar que está atingindo o tempo limite, examine os [logs](image-builder-troubleshoot.md#customization-log) para ver se a etapa de personalização está aguardando algo como entrada do usuário. 

Se você acreditar que precisa de mais tempo para que as personalizações sejam concluídas, defina isso para o que você acredita ser necessário, com uma pequena sobrecarga. No entanto, não defina-o muito alto, pois talvez seja necessário aguardar até que ele atinja o tempo limite antes de ver um erro. 


## <a name="properties-customize"></a>Propriedades: personalizar

O Construtor de Imagens dá suporte a vários "personalizadores". Os personalizadores são funções usadas para personalizar a imagem, como a execução de scripts ou a reinicialização de servidores. 

Ao usar `customize`: 
- Você pode usar vários personalizadores, mas eles devem ter um `name` exclusivo.
- Os personalizadores são executados na ordem especificada no modelo.
- Se um personalizador falhar, o componente de personalização inteiro falhará e relatará um erro.
- É altamente recomendável testar o script cuidadosamente antes de usá-lo em um modelo. A depuração do script em sua própria VM será mais fácil.
- Não coloque dados confidenciais nos scripts. 
- Os locais de script precisam ser acessíveis publicamente, a menos que você esteja usando [MSI](./image-builder-user-assigned-identity.md).

```json
        "customize": [
            {
                "type": "Shell",
                "name": "<name>",
                "scriptUri": "<path to script>",
                "sha256Checksum": "<sha256 checksum>"
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

 
A seção de personalizar é uma matriz. O Construtor de Imagens do Azure percorrerá os personalizadores em ordem sequencial. Qualquer falha em qualquer personalizador causará a falha do processo de build. 

> [!NOTE]
> Comandos embutidos podem ser exibidos na definição do modelo de imagem e por Suporte da Microsoft ao ajudar com um caso de suporte. Se você tiver informações confidenciais, elas deverão ser movidas para scripts no armazenamento do Azure, onde o acesso requer autenticação.
 
### <a name="shell-customizer"></a>Personalizador de Shell

O personalizador de shell dá suporte a scripts Shell em execução. Eles devem estar acessíveis publicamente para que o IB possa acessá-los.

```json
    "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "scriptUri": "<link to script>",
            "sha256Checksum": "<sha256 checksum>"       
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

Suporte a SO: Linux 
 
Personalizar propriedades:

- **type** – shell 
- **name** – nome para acompanhar a personalização 
- **scriptUri** – URI para o local do arquivo 
- **inline** – matriz de comandos de shell separados por vírgulas.
- **sha256Checksum** – valor da soma de verificação SHA256 do arquivo. Você o gera localmente e, em seguida, o Construtor de Imagens faz a soma de verificação e a validação.
    * Para gerar a sha256Checksum, usando um terminal no Mac/Linux, execute: `sha256sum <fileName>`

> [!NOTE]
> Comandos embutidos são armazenados como parte da definição do modelo de imagem. você pode vê-los ao despejar a definição de imagem, e eles também são visíveis para Suporte da Microsoft no caso de um caso de suporte para fins de solução de problemas. Se você tiver comandos ou valores confidenciais, é altamente recomendável que eles sejam movidos para scripts e usem uma identidade de usuário para autenticar no armazenamento do Azure.

#### <a name="super-user-privileges"></a>Privilégios de superusuário
Para que os comandos sejam executados com privilégios de superusuário, eles devem ser prefixados com o `sudo` , você pode adicioná-los em scripts ou usá-los como comandos embutidos, por exemplo:
```json
                "type": "Shell",
                "name": "setupBuildPath",
                "inline": [
                    "sudo mkdir /buildArtifacts",
                    "sudo cp /tmp/index.html /buildArtifacts/index.html"
```
Exemplo de um script usando sudo que você pode referenciar usando scriptUri:
```bash
#!/bin/bash -e

echo "Telemetry: creating files"
mkdir /myfiles

echo "Telemetry: running sudo 'as-is' in a script"
sudo touch /myfiles/somethingElevated.txt
```

### <a name="windows-restart-customizer"></a>Personalizador de reinicialização do Windows 
O personalizador de reinicialização permite reiniciar uma VM do Windows e aguardar que ela volte a ficar online, permitindo que você instale software que requer uma reinicialização.  

```json 
     "customize": [ 

            {
                "type": "WindowsRestart",
                "restartCommand": "shutdown /r /f /t 0", 
                "restartCheckCommand": "echo Azure-Image-Builder-Restarted-the-VM  > c:\\buildArtifacts\\azureImageBuilderRestart.txt",
                "restartTimeout": "5m"
            }
  
        ],
```

Suporte a SO: Windows
 
Personalizar propriedades:
- **Digitar**: WindowsRestart
- **restartCommand** – comando para executar a reinicialização (opcional). O padrão é `'shutdown /r /f /t 0 /c \"packer restart\"'`.
- **restartCheckCommand** – comando para verificar se a reinicialização foi bem-sucedida (opcional). 
- **restartTimeout** – tempo limite de reinicialização especificado como uma cadeia de caracteres de magnitude e unidade. Por exemplo, `5m` (5 minutos) ou `2h` (2 horas). O padrão é: '5m'

### <a name="linux-restart"></a>Reinicialização do Linux  
Não há nenhum personalizador de reinicialização do Linux, no entanto, se você estiver instalando drivers ou componentes que exigem uma reinicialização, você poderá instalá-los e invocar uma reinicialização usando o personalizador de shell. Há um tempo limite de SSH de 20 minutos para a VM de build.

### <a name="powershell-customizer"></a>Personalizador do PowerShell 
O personalizador de shell dá suporte à execução de scripts do PowerShell e ao comando embutido. Os scripts devem estar publicamente acessíveis para que o IB possa acessá-los.

```json 
     "customize": [
        { 
             "type": "PowerShell",
             "name":   "<name>",  
             "scriptUri": "<path to script>",
             "runElevated": "<true false>",
             "sha256Checksum": "<sha256 checksum>" 
        },  
        { 
             "type": "PowerShell", 
             "name": "<name>", 
             "inline": "<PowerShell syntax to run>", 
             "validExitCodes": "<exit code>",
             "runElevated": "<true or false>" 
         } 
    ], 
```

Suporte a SO: Windows e Linux

Personalizar propriedades:

- **type** – PowerShell.
- **scriptUri** – URI para o local do arquivo de script do PowerShell. 
- **inline** – comandos embutidos a serem executados, separados por vírgulas.
- **validExitCodes** – códigos opcionais que podem ser retornados do comando de script/embutido. Isso evitará a falha relatada do comando de script/embutido.
- **runElevated** – opcional, booliano, suporte para a execução de comandos e scripts com permissões elevadas.
- **sha256Checksum** – valor da soma de verificação SHA256 do arquivo. Você o gera localmente e, em seguida, o Construtor de Imagens faz a soma de verificação e a validação.
    * Para gerar o sha256Checksum, usando um PowerShell no Windows [Get-hash](/powershell/module/microsoft.powershell.utility/get-filehash)


### <a name="file-customizer"></a>Personalizador de arquivo

O personalizador de arquivo permite que o construtor de imagens baixe um arquivo de um GitHub ou armazenamento do Azure. Se você tiver um pipeline de build de imagem que se baseia em artefatos de build, poderá definir o personalizador de arquivo para baixar do compartilhamento de build e mover os artefatos para a imagem.  

```json
     "customize": [ 
         { 
            "type": "File", 
             "name": "<name>", 
             "sourceUri": "<source location>",
             "destination": "<destination>",
             "sha256Checksum": "<sha256 checksum>"
         }
     ]
```

Suporte a SO: Linux e Windows 

Propriedades do personalizador de arquivo:

- **sourceUri** – um ponto de extremidade de armazenamento acessível. Pode ser o GitHub ou o armazenamento do Azure. Você só pode baixar um arquivo, não um diretório inteiro. Se você precisar baixar um diretório, use um arquivo compactado e descompacte-o usando os personalizadores Shell ou PowerShell. 

> [!NOTE]
> Se o sourceUri for uma conta de armazenamento do Azure, independentemente de o blob ser marcado como público, você concederá permissões de identidade de usuário gerenciadas para acesso de leitura no BLOB. Consulte este [exemplo](./image-builder-user-assigned-identity.md#create-a-resource-group) para definir as permissões de armazenamento.

- **destination** – é o caminho de destino completo e o nome do arquivo. Qualquer caminho e subdiretórios referenciados devem existir. Use os personalizadores Shell ou PowerShell para configurá-los com antecedência. Você pode usar os personalizadores de script para criar o caminho. 

Há suporte para isso em diretórios do Windows e caminhos do Linux, mas há algumas diferenças: 
- SO Linux – o único caminho em que o construtor de imagens pode gravar em é /tmp.
- Windows – nenhuma restrição de caminho, mas o caminho deve existir.
 
 
Se houver um erro ao tentar baixar o arquivo, ou colocá-lo em um diretório especificado, a etapa de personalização falhará e isso estará no arquivo customization.log.

> [!NOTE]
> O personalizador de arquivos é adequado apenas para downloads de arquivos pequenos <20 MB. Para downloads de arquivos maiores, use um script ou comando embutido, então use o código para baixar arquivos, como o `wget` ou o `curl` do Linux e o `Invoke-WebRequest` do Windows.

### <a name="windows-update-customizer"></a>Personalizador do Windows Update
Este personalizador foi criado com base no [Provisionador de do Windows Update da comunidade](https://packer.io/docs/provisioners/community-supported.html) para o Packer, que é um projeto de software livre mantido pela comunidade do Packer. A Microsoft testa e valida o provisionamento com o serviço do Construtor de Imagens e dará suporte à investigação de problemas com ele, trabalhando para resolvê-los. No entanto, o projeto de software livre não tem suporte oficial da Microsoft. Para obter a documentação detalhada e ajuda com o Provisionador do Windows Update, confira o repositório do projeto.

```json
     "customize": [
            {
                "type": "WindowsUpdate",
                "searchCriteria": "IsInstalled=0",
                "filters": [
                    "exclude:$_.Title -like '*Preview*'",
                    "include:$true"
                            ],
                "updateLimit": 20
            }
               ], 
OS support: Windows
```

Personalizar propriedades:
- **type** – WindowsUpdate.
- **searchCriteria** – opcional, define que tipo de atualizações são instaladas (recomendado, importante etc.), BrowseOnly = 0 e IsInstalled = 0 (recomendado) é o padrão.
- **filters** – opcional, permite que você especifique um filtro para incluir ou excluir atualizações.
- **updateLimit** – opcional, define quantas atualizações podem ser instaladas. O padrão é 1.000.
 
> [!NOTE]
> O personalizador de Windows Update poderá falhar se houver reinicializações pendentes do Windows ou se as instalações do aplicativo ainda estiverem em execução, normalmente você poderá ver esse erro no customization. log, `System.Runtime.InteropServices.COMException (0x80240016): Exception from HRESULT: 0x80240016` . Aconselhamos que você considere a adição de uma reinicialização do Windows e/ou permitindo que os aplicativos tenham tempo suficiente para concluir suas instalações usando comandos de [suspensão](/powershell/module/microsoft.powershell.utility/start-sleep) ou espera nos comandos embutidos ou scripts antes de executar Windows Update.

### <a name="generalize"></a>Generalizar 
Por padrão, o Construtor de Imagens do Azure também executará o código de "desprovisionamento" no final de cada fase de personalização de imagem para "generalizar" a imagem. Generalizar é um processo em que a imagem é configurada para que possa ser reutilizada para criar várias VMs. Para VMs do Windows, o Construtor de Imagens do Azure usa Sysprep. Para o Linux, o Construtor de Imagens do Azure executa 'waagent-deprovision'. 

Os comandos que o Construtor de Imagens usa para generalizar podem não ser adequados para todas as situações, portanto, o Construtor de Imagens do Azure permitirá que você os personalize, se necessário. 

Se você estiver migrando a personalização existente e estiver usando diferentes comandos Sysprep/waagent, poderá usar os comandos genéricos do Construtor de Imagens. Se a criação da VM falhar, use seus próprios comandos Sysprep ou waagent.

Se o Construtor de Imagens do Azure criar uma imagem personalizada do Windows com êxito e você criar uma VM com base nela, então descobrir que a criação da VM falhou ou não é concluída com êxito, será preciso examinar a documentação do Sysprep do Windows Server ou gerar uma solicitação de suporte com a equipe de suporte do Windows Server Sysprep Customer Services, que pode solucionar problemas e recomendar o uso correto do Sysprep.


#### <a name="default-sysprep-command"></a>Comando Sysprep padrão
```powershell
Write-Output '>>> Waiting for GA Service (RdAgent) to start ...'
while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }
Write-Output '>>> Waiting for GA Service (WindowsAzureTelemetryService) to start ...'
while ((Get-Service WindowsAzureTelemetryService) -and ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running')) { Start-Sleep -s 5 }
Write-Output '>>> Waiting for GA Service (WindowsAzureGuestAgent) to start ...'
while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }
Write-Output '>>> Sysprepping VM ...'
if( Test-Path $Env:SystemRoot\system32\Sysprep\unattend.xml ) {
  Remove-Item $Env:SystemRoot\system32\Sysprep\unattend.xml -Force
}
& $Env:SystemRoot\System32\Sysprep\Sysprep.exe /oobe /generalize /quiet /quit
while($true) {
  $imageState = (Get-ItemProperty HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\State).ImageState
  Write-Output $imageState
  if ($imageState -eq 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { break }
  Start-Sleep -s 5
}
Write-Output '>>> Sysprep complete ...'
```
#### <a name="default-linux-deprovision-command"></a>Comando padrão de desprovisionamento do Linux

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

#### <a name="overriding-the-commands"></a>Como substituir os comandos
Para substituir os comandos, use os provisionadores de script PowerShell ou Shell para criar os arquivos de comando com o nome exato do arquivo e coloque-os nos diretórios corretos:

* Windows: c:\DeprovisioningScript.ps1
* Linux: /tmp/DeprovisioningScript.sh

O Construtor de Imagens lerá esses comandos, que serão gravados nos logs do AIB, 'customization.log'. Confira [solução de problemas](image-builder-troubleshoot.md#customization-log) sobre como coletar logs.
 
## <a name="properties-distribute"></a>Propriedades: distribuição

O Construtor de Imagens do Azure dá suporte a três destinos de distribuição: 

- **managedImage** – imagem gerenciada.
- **sharedImage** – Galeria de Imagens Compartilhadas.
- **VHD** – VHD em uma conta de armazenamento.

Você pode distribuir uma imagem para ambos os tipos de destino na mesma configuração.

> [!NOTE]
> O comando AIB Sysprep padrão não inclui "/Mode: VM", mas isso talvez seja necessário ao criar imagens que terão a função HyperV instalada. Se você precisar adicionar esse argumento de comando, deverá substituir o comando Sysprep.

Como você pode ter mais de um destino para distribuir, o Construtor de Imagens mantém um estado para cada destino de distribuição que pode ser acessado consultando o `runOutputName`.  O `runOutputName` é um objeto que você pode consultar após a distribuição para obter informações sobre essa distribuição. Por exemplo, você pode consultar o local do VHD ou as regiões nas quais a versão da imagem foi replicada ou a versão de imagem SIG criada. Essa é uma propriedade de cada destino de distribuição. O `runOutputName` deve ser exclusivo para cada destino de distribuição. Aqui está um exemplo, consultando uma distribuição da Galeria de Imagens Compartilhadas:

```bash
subscriptionID=<subcriptionID>
imageResourceGroup=<resourceGroup of image template>
runOutputName=<runOutputName>

az resource show \
        --ids "/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.VirtualMachineImages/imageTemplates/ImageTemplateLinuxRHEL77/runOutputs/$runOutputName"  \
        --api-version=2020-02-14
```

Saída:
```json
{
  "id": "/subscriptions/xxxxxx/resourcegroups/rheltest/providers/Microsoft.VirtualMachineImages/imageTemplates/ImageTemplateLinuxRHEL77/runOutputs/rhel77",
  "identity": null,
  "kind": null,
  "location": null,
  "managedBy": null,
  "name": "rhel77",
  "plan": null,
  "properties": {
    "artifactId": "/subscriptions/xxxxxx/resourceGroups/aibDevOpsImg/providers/Microsoft.Compute/galleries/devOpsSIG/images/rhel/versions/0.24105.52755",
    "provisioningState": "Succeeded"
  },
  "resourceGroup": "rheltest",
  "sku": null,
  "tags": null,
  "type": "Microsoft.VirtualMachineImages/imageTemplates/runOutputs"
}
```

### <a name="distribute-managedimage"></a>Distribuição: managedImage

A saída da imagem será um recurso de imagem gerenciada.

```json
{
       "type":"managedImage",
       "imageId": "<resource ID>",
       "location": "<region>",
       "runOutputName": "<name>",
       "artifactTags": {
            "<name": "<value>",
            "<name>": "<value>"
        }
}
```
 
Propriedades de distribuição:
- **type** – managedImage 
- **imageid** – ID de recurso da imagem de destino, formato esperado:/subscriptions/ \<subscriptionId> /resourceGroups/ \<destinationResourceGroupName> /Providers/Microsoft.Compute/images/\<imageName>
- **local** – local da imagem gerenciada.  
- **runOutputName** – nome exclusivo para identificar a distribuição.  
- **artifactTags** – marcas opcionais de par de chave-valor especificadas pelo usuário.
 
 
> [!NOTE]
> O grupo de recursos de destino deve existir.
> Se você quiser que a imagem seja distribuída para uma região diferente, isso aumentará o tempo de implantação. 

### <a name="distribute-sharedimage"></a>Distribuição: sharedImage 
A Galeria de Imagens Compartilhadas do Azure é um novo serviço de Gerenciamento de Imagens que permite o gerenciamento de replicação de região de imagem, o controle de versão e o compartilhamento de imagens personalizadas. O Construtor de Imagens do Azure dá suporte à distribuição com esse serviço, assim, você pode distribuir imagens para regiões com suporte nas Galerias de Imagens Compartilhadas. 
 
Uma Galeria de Imagens Compartilhadas é composta por: 
 
- Galeria – contêiner para várias imagens compartilhadas. Uma galeria é implantada em uma região.
- Definições de imagem – um agrupamento conceitual para imagens. 
- Versões de imagem – esse é um tipo de imagem usado para implantar uma VM ou um conjunto de dimensionamento. As versões de imagem podem ser replicadas para outras regiões em que as VMs precisam ser implantadas.
 
Antes de distribuir para a Galeria de Imagens, você deve criar uma galeria e uma definição de imagem. Confira [Imagens compartilhadas](../shared-images-cli.md). 

```json
{
    "type": "SharedImage",
    "galleryImageId": "<resource ID>",
    "runOutputName": "<name>",
    "artifactTags": {
        "<name>": "<value>",
        "<name>": "<value>"
    },
    "replicationRegions": [
        "<region where the gallery is deployed>",
        "<region>"
    ]
}
``` 

Distribuir propriedades para galerias de imagens compartilhadas:

- **type** – sharedImage  
- **galleryImageId** – ID da Galeria de imagens compartilhadas, isso pode ser especificado em dois formatos:
    * O controle de versão automático-o Image Builder gerará um número de versão monotônico para você, isso é útil quando você deseja manter a recriação de imagens do mesmo modelo: o formato é: `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageGalleryName>` .
    * Controle de versão explícito-você pode passar o número de versão que deseja que o construtor de imagem use. O formato é: `/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<sharedImageGalName>/images/<imageDefName>/versions/<version e.g. 1.1.1>`

- **runOutputName** – nome exclusivo para identificar a distribuição.  
- **artifactTags** – marcas opcionais de par de chave-valor especificadas pelo usuário.
- **replicationRegions** – matriz de regiões para replicação. Uma das regiões deve ser aquela em que a galeria é implantada. A adição de regiões significa um aumento do tempo de compilação, pois a compilação não será concluída até que a replicação seja concluída.
- **excludeFromLatest** (opcional) isso permite marcar a versão da imagem que você cria não ser usada como a versão mais recente na definição SIG, o padrão é ' false '.
- **storageAccountType** (opcional) o AIB dá suporte à especificação desses tipos de armazenamento para a versão de imagem a ser criada:
    * "Standard_LRS"
    * "Standard_ZRS"


> [!NOTE]
> Se o modelo de imagem e o referenciado `image definition` não estiverem no mesmo local, você verá um tempo adicional para criar imagens. Atualmente, o Image Builder não tem um `location` parâmetro para o recurso de versão da imagem, nós o pegamos de seu pai `image definition` . Por exemplo, se uma definição de imagem estiver em westus e você quiser que a versão da imagem seja replicada para o lesteus, um blob será copiado para o westus, a partir desse, um recurso de versão da imagem em westus será criado e, em seguida, replicado para o eastus. Para evitar o tempo de replicação adicional, verifique se o `image definition` modelo de imagem e está no mesmo local.


### <a name="distribute-vhd"></a>Distribuir: VHD  
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
 
Suporte a SO: Windows e Linux

Distribuir parâmetros de VHD:

- **type** – VHD.
- **runOutputName** – nome exclusivo para identificar a distribuição.  
- **tags** – marcas opcionais de par de chave-valor especificadas pelo usuário.
 
O Construtor de Imagens do Azure não permite que o usuário especifique um local de conta de armazenamento, mas você pode consultar o status de `runOutputs` para obter o local.  

```azurecli-interactive
az resource show \
   --ids "/subscriptions/$subscriptionId/resourcegroups/<imageResourceGroup>/providers/Microsoft.VirtualMachineImages/imageTemplates/<imageTemplateName>/runOutputs/<runOutputName>"  | grep artifactUri 
```

> [!NOTE]
> Depois que o VHD tiver sido criado, copie-o para um local diferente assim que possível. O VHD é armazenado em uma conta de armazenamento no grupo de recursos temporário criado quando o modelo de imagem é enviado para o serviço do Construtor de Imagens do Azure. Se você excluir o modelo de imagem, o VHD será perdido. 

## <a name="image-template-operations"></a>Operações de modelo de imagem

### <a name="starting-an-image-build"></a>Iniciando uma compilação de imagem
Para iniciar uma compilação, você precisa invocar ' Run ' no recurso de modelo de imagem, exemplos de `run` comandos:

```PowerShell
Invoke-AzResourceAction -ResourceName $imageTemplateName -ResourceGroupName $imageResourceGroup -ResourceType Microsoft.VirtualMachineImages/imageTemplates -ApiVersion "2020-02-14" -Action Run -Force
```


```bash
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Run 
```

### <a name="cancelling-an-image-build"></a>Cancelando uma compilação de imagem
Se você estiver executando uma compilação de imagem que acredite que está incorreta, aguardando a entrada do usuário ou se sentir que nunca será concluída com êxito, poderá cancelar a compilação.

A compilação pode ser cancelada a qualquer momento. Se a fase de distribuição foi iniciada, ainda será possível cancelar, mas você precisará limpar todas as imagens que podem não ser concluídas. O comando cancelar não aguarda o cancelamento da conclusão, monitore o `lastrunstatus.runstate` andamento do cancelamento, usando esses [comandos](image-builder-troubleshoot.md#customization-log)de status.


Exemplos de `cancel` comandos:

```powerShell
Invoke-AzResourceAction -ResourceName $imageTemplateName -ResourceGroupName $imageResourceGroup -ResourceType Microsoft.VirtualMachineImages/imageTemplates -ApiVersion "2020-02-14" -Action Cancel -Force
```

```bash
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Cancel 
```

## <a name="next-steps"></a>Próximas etapas

Há arquivos .json de exemplo para diferentes cenários no [Construtor de Imagens do Azure no GitHub](https://github.com/azure/azvmimagebuilder).