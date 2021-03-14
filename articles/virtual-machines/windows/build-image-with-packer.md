---
title: PowerShell-como criar imagens de VM com o empacotador
description: Saiba como usar o Packr e o PowerShell para criar imagens de máquinas virtuais no Azure
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.collection: windows
ms.topic: how-to
ms.workload: infrastructure
ms.date: 08/05/2020
ms.author: cynthn
ms.openlocfilehash: a2cd6152f9c8e75223fcf1aab77fee7700223979
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102561000"
---
# <a name="powershell-how-to-use-packer-to-create-virtual-machine-images-in-azure"></a>PowerShell: como usar o Packer para criar imagens de máquina virtual no Azure
Cada VM (máquina virtual) no Azure é criada com base em uma imagem que define a distribuição do Windows e a versão do sistema operacional. As imagens podem incluir configurações e aplicativos pré-instalados. O Azure Marketplace fornece várias imagens internas e de terceiros para os ambientes de sistema operacional e de aplicativo mais comuns ou você pode criar suas próprias imagens personalizadas adequadas às suas necessidades. Este artigo fornece detalhes sobre como usar a ferramenta de software livre [Packer](https://www.packer.io/) para definir e compilar imagens personalizadas no Azure.

Este artigo foi testado pela última vez em 8/5/2020 usando a versão 1.6.1 do [Pack](https://www.packer.io/docs/install) .

> [!NOTE]
> Agora, o Azure tem um serviço, o Construtor de Imagens de VM do Azure (versão prévia), para definir e criar suas próprias imagens personalizadas. O Construtor de Imagens de VM do Azure é compilado no Packer, para que você possa até mesmo usar com ele seus scripts de provisionamento de shell do Pack. Para começar a usar o Construtor de Imagens de VM do Azure, consulte [Criar uma VM do Windows com o Construtor de Imagens de VM do Azure](image-builder.md).

## <a name="create-azure-resource-group"></a>Criar um grupo de recursos do Azure
Durante o processo de build, o Packer cria recursos temporários do Azure conforme cria a VM de origem. Para capturar essa VM de origem para uso como uma imagem, você precisa definir um grupo de recursos. A saída do processo de build do Packer é armazenada nesse grupo de recursos.

Crie um grupo de recursos com [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). O exemplo a seguir cria um grupo de recursos chamado *myPackerGroup* no local *eastus* :

```azurepowershell
$rgName = "myPackerGroup"
$location = "East US"
New-AzResourceGroup -Name $rgName -Location $location
```

## <a name="create-azure-credentials"></a>Criar as credenciais do Azure
O Packer se autentica no Azure usando uma entidade de serviço. Uma entidade de serviço do Azure é uma identidade de segurança que você pode usar com aplicativos, serviços e ferramentas de automação como o Packer. Você controla e define as permissões referentes a quais operações a entidade de serviço pode executar no Azure.

Crie uma entidade de serviço com [New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal). O valor de `-DisplayName` precisa ser exclusivo. Substitua-o pelo seu próprio valor, conforme necessário.  

```azurepowershell
$sp = New-AzADServicePrincipal -DisplayName "PackerSP$(Get-Random)"
$BSTR = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($sp.Secret)
$plainPassword = [System.Runtime.InteropServices.Marshal]::PtrToStringAuto($BSTR)
```

Em seguida, gere a senha e a ID do aplicativo.

```powershell
$plainPassword
$sp.ApplicationId
```


Para se autenticar no Azure, você também precisa obter suas IDs de locatário e de assinatura do Azure com [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription):

```powershell
Get-AzSubscription
```


## <a name="define-packer-template"></a>Definir modelo do Packer
Para criar imagens, você cria um modelo como um arquivo JSON. No modelo, você define construtores e provisionadores que executam o processo de build real. O Packer tem um [compilador do Azure](https://www.packer.io/docs/builders/azure.html) que permite definir recursos do Azure, como as credenciais da entidade de serviço criadas na etapa anterior.

Crie um arquivo chamado *windows.json* e cole o conteúdo a seguir. Insira seus próprios valores para o seguinte:

| Parâmetro                           | Onde obter |
|-------------------------------------|----------------------------------------------------|
| *client_id*                         | Exiba a ID da entidade de serviço com `$sp.applicationId` |
| *client_secret*                     | Exiba a senha gerada automaticamente com `$plainPassword` |
| *tenant_id*                         | Saída do comando `$sub.TenantId` |
| *subscription_id*                   | Saída do comando `$sub.SubscriptionId` |
| *managed_image_resource_group_name* | Nome do grupo de recursos que você criou na primeira etapa |
| *managed_image_name*                | Nome da imagem de disco gerenciado que é criada |

```json
{
  "builders": [{
    "type": "azure-arm",

    "client_id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "client_secret": "ppppppp-pppp-pppp-pppp-ppppppppppp",
    "tenant_id": "zzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz",
    "subscription_id": "yyyyyyy-yyyy-yyyy-yyyy-yyyyyyyyyyy",

    "managed_image_resource_group_name": "myPackerGroup",
    "managed_image_name": "myPackerImage",

    "os_type": "Windows",
    "image_publisher": "MicrosoftWindowsServer",
    "image_offer": "WindowsServer",
    "image_sku": "2016-Datacenter",

    "communicator": "winrm",
    "winrm_use_ssl": true,
    "winrm_insecure": true,
    "winrm_timeout": "5m",
    "winrm_username": "packer",

    "azure_tags": {
        "dept": "Engineering",
        "task": "Image deployment"
    },

    "build_resource_group_name": "myPackerGroup",
    "vm_size": "Standard_D2_v2"
  }],
  "provisioners": [{
    "type": "powershell",
    "inline": [
      "Add-WindowsFeature Web-Server",
      "while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }",
      "while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }",
      "& $env:SystemRoot\\System32\\Sysprep\\Sysprep.exe /oobe /generalize /quiet /quit",
      "while($true) { $imageState = Get-ItemProperty HKLM:\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Setup\\State | Select ImageState; if($imageState.ImageState -ne 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { Write-Output $imageState.ImageState; Start-Sleep -s 10  } else { break } }"
    ]
  }]
}
```

Este modelo cria uma VM Windows Server 2016, instala o IIS e generaliza a VM com o Sysprep. A instalação do IIS mostra como você pode usar o provisionador do PowerShell para executar comandos adicionais. A imagem do Packer final, em seguida, inclui a configuração e instalação de software necessárias.

O agente convidado do Windows participa do processo Sysprep. O agente deve ser totalmente instalado antes que a VM possa ser tratado por Sysprep. Para garantir que isso seja verdadeiro, todos os serviços de agente devem estar em execução antes de executar sysprep.exe. O trecho JSON anterior mostra uma maneira de fazer isso no provisionamento do PowerShell. Esse trecho de código será necessário somente se a VM estiver configurada para instalar o agente, que é o padrão.


## <a name="build-packer-image"></a>Criar uma imagem do Packer
Se você ainda não tiver o Packer instalado no computador local, [siga as instruções de instalação do Packer](https://learn.hashicorp.com/packer/getting-started/install).

Crie a imagem abrindo um prompt de comando e especificando o arquivo de modelo do Packer da seguinte maneira:

```
./packer build windows.json
```

Um exemplo da saída dos comandos anteriores é o seguinte:

```bash
azure-arm output will be in this color.

==> azure-arm: Running builder ...
    azure-arm: Creating Azure Resource Manager (ARM) client ...
==> azure-arm: Creating resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> Location          : ‘East US’
==> azure-arm:  -> Tags              :
==> azure-arm:  ->> task : Image deployment
==> azure-arm:  ->> dept : Engineering
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Getting the certificate’s URL ...
==> azure-arm:  -> Key Vault Name        : ‘pkrkvpq0mthtbtt’
==> azure-arm:  -> Key Vault Secret Name : ‘packerKeyVaultSecret’
==> azure-arm:  -> Certificate URL       : ‘https://pkrkvpq0mthtbtt.vault.azure.net/secrets/packerKeyVaultSecret/8c7bd823e4fa44e1abb747636128adbb'
==> azure-arm: Setting the certificate’s URL ...
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Getting the VM’s IP address ...
==> azure-arm:  -> ResourceGroupName   : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> PublicIPAddressName : ‘packerPublicIP’
==> azure-arm:  -> NicName             : ‘packerNic’
==> azure-arm:  -> Network Connection  : ‘PublicEndpoint’
==> azure-arm:  -> IP Address          : ‘40.76.55.35’
==> azure-arm: Waiting for WinRM to become available...
==> azure-arm: Connected to WinRM!
==> azure-arm: Provisioning with Powershell...
==> azure-arm: Provisioning with shell script: /var/folders/h1/ymh5bdx15wgdn5hvgj1wc0zh0000gn/T/packer-powershell-provisioner902510110
    azure-arm: #< CLIXML
    azure-arm:
    azure-arm: Success Restart Needed Exit Code      Feature Result
    azure-arm: ------- -------------- ---------      --------------
    azure-arm: True    No             Success        {Common HTTP Features, Default Document, D...
    azure-arm: <Objs Version=“1.1.0.1” xmlns=“http://schemas.microsoft.com/powershell/2004/04"><Obj S=“progress” RefId=“0"><TN RefId=“0”><T>System.Management.Automation.PSCustomObject</T><T>System.Object</T></TN><MS><I64 N=“SourceId”>1</I64><PR N=“Record”><AV>Preparing modules for first use.</AV><AI>0</AI><Nil /><PI>-1</PI><PC>-1</PC><T>Completed</T><SR>-1</SR><SD> </SD></PR></MS></Obj></Objs>
==> azure-arm: Querying the machine’s properties ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> ComputeName       : ‘pkrvmpq0mthtbtt’
==> azure-arm:  -> Managed OS Disk   : ‘/subscriptions/guid/resourceGroups/packer-Resource-Group-pq0mthtbtt/providers/Microsoft.Compute/disks/osdisk’
==> azure-arm: Powering off machine ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> ComputeName       : ‘pkrvmpq0mthtbtt’
==> azure-arm: Capturing image ...
==> azure-arm:  -> Compute ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> Compute Name              : ‘pkrvmpq0mthtbtt’
==> azure-arm:  -> Compute Location          : ‘East US’
==> azure-arm:  -> Image ResourceGroupName   : ‘myResourceGroup’
==> azure-arm:  -> Image Name                : ‘myPackerImage’
==> azure-arm:  -> Image Location            : ‘eastus’
==> azure-arm: Deleting resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm: Deleting the temporary OS disk ...
==> azure-arm:  -> OS Disk : skipping, managed disk was used...
Build ‘azure-arm’ finished.

==> Builds finished. The artifacts of successful builds are:
--> azure-arm: Azure.ResourceManagement.VMImage:

ManagedImageResourceGroupName: myResourceGroup
ManagedImageName: myPackerImage
ManagedImageLocation: eastus
```

São necessários alguns minutos para que o Packer crie a VM, execute os provisionadores e limpe a implantação.


## <a name="create-a-vm-from-the-packer-image"></a>Criar uma VM a partir da imagem do Packer
Agora você pode criar uma VM com base na Imagem com [New-AzVM](/powershell/module/az.compute/new-azvm). Se ainda não existirem, os recursos de rede de suporte serão criados. Quando solicitado, insira um nome de usuário administrativo e senha a serem criados na VM. O exemplo a seguir cria uma VM nomeada *myVM* com base em *myPackerImage*:

```powershell
New-AzVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80 `
    -Image "myPackerImage"
```

Se você quiser criar VMs em um grupo de recursos diferente ou a região da imagem Packer, especifique a ID da imagem em vez do nome da imagem. Você pode obter a ID de imagem com [Get-AzImage](/powershell/module/az.compute/get-azimage).

A criação da VM a partir da imagem de seu Packer demora alguns minutos.


## <a name="test-vm-and-webserver"></a>Testar a VM e o servidor Web
Obtenha o endereço IP público da VM com [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress). O exemplo a seguir obtém o endereço IP para *myPublicIP* criado anteriormente:

```powershell
Get-AzPublicIPAddress `
    -ResourceGroupName $rgName `
    -Name "myPublicIPAddress" | select "IpAddress"
```

Para ver sua VM, que inclui a instalação do IIS do provisionador do Packer, em ação, insira o endereço IP público em um navegador da Web.

![Site do IIS padrão](./media/build-image-with-packer/iis.png) 


## <a name="next-steps"></a>Próximas etapas
Você também pode usar scripts de provisionamento do Packer com o [Construtor de Imagens de VM do Azure](image-builder.md).
