---
title: Implantar VMs em seu dispositivo Azure Stack Edge pro por meio de modelos
description: Descreve como criar e gerenciar máquinas virtuais (VMs) em um dispositivo Azure Stack Edge pro usando modelos.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 01/25/2021
ms.author: alkohli
ms.openlocfilehash: 66d537b79819aecab4ce88a56ed465679363f421
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98805198"
---
# <a name="deploy-vms-on-your-azure-stack-edge-pro-gpu-device-via-templates"></a>Implantar VMs em seu dispositivo do Azure Stack Edge pro GPU por meio de modelos

Este tutorial descreve como criar e gerenciar uma VM em seu dispositivo Azure Stack Edge pro usando modelos. Esses modelos são arquivos JavaScript Object Notation (JSON) que definem a infraestrutura e a configuração da sua VM. Nesses modelos, você especifica os recursos a serem implantados e as propriedades desses recursos.

Os modelos são flexíveis em ambientes diferentes, pois eles podem usar parâmetros como entrada no tempo de execução de um arquivo. A estrutura de nomenclatura padrão é `TemplateName.json` para o modelo e `TemplateName.parameters.json` para o arquivo de parâmetros. Para obter mais informações sobre modelos do ARM, acesse [o que são Azure Resource Manager modelos?](../azure-resource-manager/templates/overview.md).

Neste tutorial, usaremos modelos de exemplo pré-gravados para a criação de recursos. Você não precisará editar o arquivo de modelo e poderá modificar apenas os `.parameters.json` arquivos para personalizar a implantação em seu computador. 

## <a name="vm-deployment-workflow"></a>Fluxo de trabalho de implantação da VM

Para implantar Azure Stack VMs do Edge pro em vários dispositivos, você pode usar um único VHD Sysprep para sua frota completa, o mesmo modelo para implantação e apenas fazer pequenas alterações nos parâmetros para esse modelo para cada local de implantação (essas alterações podem ser feitas manualmente, como estamos fazendo aqui ou programaticamente.) 

O resumo de alto nível do fluxo de trabalho de implantação usando modelos é o seguinte:

1. **Configurar os pré-requisitos** – há três tipos de pré-requisitos: dispositivo, cliente e para a VM.

    1. **Pré-requisitos do dispositivo**

        1. Conecte-se a Azure Resource Manager no dispositivo.
        2. Habilite a computação por meio da interface do usuário local.

    2. **Pré-requisitos do cliente**

        1. Baixe os modelos de VM e os arquivos associados no cliente.
        1. Opcionalmente, configure o TLS 1,2 no cliente.
        1. [Baixe e instale Gerenciador de armazenamento do Microsoft Azure](https://go.microsoft.com/fwlink/?LinkId=708343&clcid=0x409) em seu cliente.

    3. **Pré-requisitos de VM**

        1. Crie um grupo de recursos no local do dispositivo que conterá todos os recursos da VM.
        1. Crie uma conta de armazenamento para carregar o VHD usado para criar a imagem de VM.
        1. Adicione o URI da conta de armazenamento local ao arquivo DNS ou hosts no cliente que está acessando seu dispositivo.
        1. Instale o certificado de armazenamento de BLOBs no dispositivo e no cliente local que está acessando seu dispositivo. Opcionalmente, instale o certificado de armazenamento de BLOBs no Gerenciador de Armazenamento.
        1. Crie e carregue um VHD para a conta de armazenamento criada anteriormente.

2. **Criar VM com base em modelos**

    1. Crie uma imagem de VM usando o `CreateImage.parameters.json` modelo de arquivo e implantação de parâmetros `CreateImage.json` .
    1. Crie uma VM com recursos criados anteriormente usando o `CreateVM.parameters.json` modelo de arquivo e implantação de parâmetros  `CreateVM.json` .

## <a name="device-prerequisites"></a>Pré-requisitos do dispositivo

Configure esses pré-requisitos em seu dispositivo Azure Stack Edge pro.

[!INCLUDE [azure-stack-edge-gateway-deploy-virtual-machine-prerequisites](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-prerequisites.md)]

## <a name="client-prerequisites"></a>Pré-requisitos do cliente

Configure esses pré-requisitos no cliente que serão usados para acessar o dispositivo Azure Stack Edge pro.

1. [Baixe Gerenciador de armazenamento](https://azure.microsoft.com/features/storage-explorer/) se você estiver usando-o para carregar um VHD. Como alternativa, você pode baixar o AzCopy para carregar um VHD. Talvez seja necessário configurar o TLS 1,2 no computador cliente se estiver executando versões mais antigas do AzCopy. 
1. [Baixe os modelos de VM e os arquivos de parâmetros](https://aka.ms/ase-vm-templates) em seu computador cliente. Descompacte-o em um diretório que você usará como um diretório de trabalho.


## <a name="vm-prerequisites"></a>Pré-requisitos de VM

Configure esses pré-requisitos para criar os recursos necessários para a criação da VM. 

    
### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos do Azure com [New-AzureRmResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Um grupo de recursos é um contêiner lógico no qual os recursos do Azure, como a conta de armazenamento, disco, disco gerenciado são implantados e gerenciados.

> [!IMPORTANT]
> Todos os recursos são criados no mesmo local que o do dispositivo e o local é definido como **DBELocal**.

```powershell
New-AzureRmResourceGroup -Name <Resource group name> -Location DBELocal
```

Um exemplo de saída é mostrado abaixo.

```powershell
PS C:\windows\system32> New-AzureRmResourceGroup -Name myasegpurgvm -Location DBELocal

ResourceGroupName : myasegpurgvm
Location          : dbelocal
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/DDF9FC44-E990-42F8-9A91-5A6A5CC472DB/resourceGroups/myasegpurgvm

PS C:\windows\system32>
```

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Crie uma nova conta de armazenamento usando o grupo de recursos criado na etapa anterior. Essa conta é uma **conta de armazenamento local** que será usada para carregar a imagem do disco virtual para a VM.

```powershell
New-AzureRmStorageAccount -Name <Storage account name> -ResourceGroupName <Resource group name> -Location DBELocal -SkuName Standard_LRS
```

> [!NOTE]
> Somente as contas de armazenamento local, como o armazenamento com redundância local (Standard_LRS ou Premium_LRS), podem ser criadas via Azure Resource Manager. Para criar contas de armazenamento em camadas, consulte as etapas em [Adicionar, conectar-se a contas de armazenamento em seu Azure Stack Edge pro](azure-stack-edge-j-series-deploy-add-storage-accounts.md).

Um exemplo de saída é mostrado abaixo.

```powershell
PS C:\windows\system32> New-AzureRmStorageAccount -Name myasegpusavm -ResourceGroupName myasegpurgvm -Location DBELocal -SkuName Standard_LRS

StorageAccountName ResourceGroupName Location SkuName     Kind    AccessTier CreationTime
------------------ ----------------- -------- -------     ----    ---------- ------------
myasegpusavm       myasegpurgvm      DBELocal StandardLRS Storage            7/29/2020 10:11:16 PM

PS C:\windows\system32>
```

Para obter a chave da conta de armazenamento, execute o `Get-AzureRmStorageAccountKey` comando. Uma saída de exemplo desse comando é mostrada aqui.

```powershell
PS C:\windows\system32> Get-AzureRmStorageAccountKey

cmdlet Get-AzureRmStorageAccountKey at command pipeline position 1
Supply values for the following parameters:
(Type !? for Help.)
ResourceGroupName: myasegpurgvm
Name: myasegpusavm

KeyName    Value                                                  Permissions   
-------     -----                                                   --
key1 GsCm7QriXurqfqx211oKdfQ1C9Hyu5ZutP6Xl0dqlNNhxLxDesDej591M8y7ykSPN4fY9vmVpgc4ftkwAO7KQ== 11 
key2 7vnVMJUwJXlxkXXOyVO4NfqbW5e/5hZ+VOs+C/h/ReeoszeV+qoyuBitgnWjiDPNdH4+lSm1/ZjvoBWsQ1klqQ== ll
```

### <a name="add-blob-uri-to-hosts-file"></a>Adicionar o URI do blob ao arquivo hosts

Certifique-se de que você já adicionou o URI de blob no arquivo de hosts para o cliente que você está usando para se conectar ao armazenamento de BLOBs. **Execute o bloco de notas como administrador** e adicione a seguinte entrada para o URI de blob no `C:\windows\system32\drivers\etc\hosts` :

`<Device IP> <storage account name>.blob.<Device name>.<DNS domain>`

Em um ambiente típico, você teria o DNS configurado para que todas as contas de armazenamento apontem para o dispositivo pro Azure Stack Edge com uma `*.blob.devicename.domainname.com` entrada.

### <a name="optional-install-certificates"></a>Adicional Instalar certificados

Ignore esta etapa se você for se conectar via Gerenciador de Armazenamento usando *http*. Se você estiver usando *https*, precisará instalar os certificados apropriados no Gerenciador de armazenamento. Nesse caso, instale o certificado de ponto de extremidade de BLOB. Para obter mais informações, consulte como criar e carregar certificados em [gerenciar certificados](azure-stack-edge-j-series-manage-certificates.md). 

### <a name="create-and-upload-a-vhd"></a>Criar e carregar um VHD

Verifique se você tem uma imagem de disco virtual que você pode usar para carregar na etapa posterior. Siga as etapas em [criar uma imagem de VM](azure-stack-edge-gpu-create-virtual-machine-image.md). 

Copie as imagens de disco a serem usadas em blobs de páginas na conta de armazenamento local que você criou nas etapas anteriores. Você pode usar uma ferramenta como [Gerenciador de armazenamento](https://azure.microsoft.com/features/storage-explorer/) ou [AzCopy para carregar o VHD na conta de armazenamento](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#upload-a-vhd) que você criou nas etapas anteriores. 

### <a name="use-storage-explorer-for-upload"></a>Usar Gerenciador de Armazenamento para carregar

1. Abra o Gerenciador de Armazenamento. Vá para **Editar** e verifique se o aplicativo está definido como **destino Azure Stack APIs**.

    ![Definir destino para Azure Stack APIs](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/set-target-apis-1.png)

2. Instale o certificado do cliente no formato PEM. Vá para **editar > certificados SSL > importar certificados**. Aponte para o certificado do cliente.

    ![Importar certificado de ponto de extremidade de armazenamento de BLOB](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/import-blob-storage-endpoint-certificate-1.png)

    - Se você estiver usando certificados gerados pelo dispositivo, baixe e converta o certificado do ponto de extremidade do armazenamento de BLOBs `.cer` em um `.pem` formato. Execute o comando a seguir. 
    
        ```powershell
        PS C:\windows\system32> Certutil -encode 'C:\myasegpu1_Blob storage (1).cer' .\blobstoragecert.pem
        Input Length = 1380
        Output Length = 1954
        CertUtil: -encode command completed successfully.
        ```
    - Se você estiver trazendo seu próprio certificado, use o certificado raiz da cadeia de assinatura no `.pem` formato.

3. Depois de importar o certificado, reinicie Gerenciador de Armazenamento para que as alterações entrem em vigor.

    ![Reiniciar o Gerenciador de Armazenamento](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/restart-storage-explorer-1.png)

4. No painel esquerdo, clique com o botão direito do mouse em **contas de armazenamento** e selecione **conectar ao armazenamento do Azure**. 

    ![Conectar-se ao armazenamento do Azure 1](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/connect-azure-storage-1.png)

5. Selecione **Usar um nome e uma chave da conta de armazenamento**. Selecione **Avançar**.

    ![Conectar-se ao armazenamento do Azure 2](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/connect-azure-storage-2.png)

6. Em **conectar com nome e chave**, forneça o **nome de exibição**, o nome da conta de **armazenamento**, a chave da **conta** de armazenamento do Azure. Selecione **outro** domínio de armazenamento e forneça a `<device name>.<DNS domain>` cadeia de conexão. Se você não instalou um certificado no Gerenciador de Armazenamento, marque a opção **usar http** . Selecione **Avançar**.

    ![Conectar-se com o nome e a chave](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/connect-name-key-1.png)

7. Examine o **Resumo da conexão** e selecione **conectar**.

8. A conta de armazenamento aparece no painel esquerdo. Selecione e expanda a conta de armazenamento. Selecione **contêineres de blob**, clique com o botão direito do mouse e selecione **criar contêiner de blob**. Forneça um nome para o contêiner de BLOB.

9. Selecione o contêiner que você acabou de criar e, no painel direito, selecione **carregar > carregar arquivos**. 

    ![Carregar arquivo VHD 1](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/upload-vhd-file-1.png)

10. Procure e aponte para o VHD que você deseja carregar nos **arquivos selecionados**. Selecione o **tipo de blob** como blob de **páginas** e selecione **carregar**.

    ![Carregar arquivo VHD 2](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/upload-vhd-file-2.png)

11. Depois que o VHD for carregado no contêiner de BLOB, selecione o VHD, clique com o botão direito do mouse e selecione **Propriedades**. 

    ![Carregar arquivo VHD 3](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/upload-vhd-file-3.png)

12. Copie e salve o **URI**, que será usado em etapas posteriores.

    ![Copiar URI](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/copy-uri-1.png)


## <a name="create-image-for-your-vm"></a>Criar imagem para sua VM

Para criar uma imagem para sua VM, edite o `CreateImage.parameters.json` arquivo de parâmetros e, em seguida, implante o modelo `CreateImage.json` que usa esse arquivo de parâmetro.


### <a name="edit-parameters-file"></a>Editar arquivo de parâmetros

O arquivo `CreateImage.parameters.json` usa os seguintes parâmetros: 

```json
"parameters": {
        "osType": {
              "value": "<Operating system corresponding to the VHD you upload can be Windows or Linux>"
        },
        "imageName": {
            "value": "<Name for the VM image>"
        },
        "imageUri": {
              "value": "<Path to the VHD that you uploaded in the Storage account>"
        },
    }
```

Edite o arquivo `CreateImage.parameters.json` para incluir os seguintes valores para seu dispositivo Azure Stack Edge pro:

1. Forneça o tipo de sistema operacional correspondente ao VHD que será carregado. O tipo de sistema operacional pode ser Windows ou Linux.

    ```json
    "parameters": {
            "osType": {
              "value": "Windows"
            },
    ```

2. Altere o URI da imagem para o URI da imagem que você carregou na etapa anterior:

   ```json
   "imageUri": {
       "value": "https://myasegpusavm.blob.myasegpu1.wdshcsso.com/windows/WindowsServer2016Datacenter.vhd"
       },
   ```

   Se você estiver usando *http* com Gerenciador de armazenamento, altere o URI para um URI *http* .

3. Forneça um nome de imagem exclusivo. Essa imagem é usada para criar a VM nas etapas posteriores. 

   Aqui está um exemplo de JSON que é usado neste artigo.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
      "parameters": {
        "osType": {
          "value": "Linux"
        },
        "imageName": {
          "value": "myaselinuximg"
        },
        "imageUri": {
          "value": "https://sa2.blob.myasegpuvm.wdshcsso.com/con1/ubuntu18.04waagent.vhd"
        }
      }
    }
    ```

5. Salve o arquivo de parâmetros.


### <a name="deploy-template"></a>Implantar modelo 

Implante o modelo `CreateImage.json` . Este modelo implanta os recursos de imagem que serão usados para criar VMs na etapa posterior.

> [!NOTE]
> Ao implantar o modelo, se você receber um erro de autenticação, suas credenciais do Azure para esta sessão poderão ter expirado. Execute `login-AzureRM` novamente o comando para se conectar ao Azure Resource Manager no dispositivo do Azure Stack Edge Pro novamente.

1. Execute o comando a seguir: 
    
    ```powershell
    $templateFile = "Path to CreateImage.json"
    $templateParameterFile = "Path to CreateImage.parameters.json"
    $RGName = "<Name of your resource group>"
    New-AzureRmResourceGroupDeployment `
        -ResourceGroupName $RGName `
        -TemplateFile $templateFile `
        -TemplateParameterFile $templateParameterFile `
        -Name "<Name for your deployment>"
    ```
    Este comando implanta um recurso de imagem. Para consultar o recurso, execute o seguinte comando:

    ```powershell
    Get-AzureRmImage -ResourceGroupName <Resource Group Name> -name <Image Name>
    ``` 
    Aqui está um exemplo de saída de uma imagem criada com êxito.
    
    ```powershell
    PS C:\WINDOWS\system32> login-AzureRMAccount -EnvironmentName aztest -TenantId c0257de7-538f-415c-993a-1b87a031879d
    
    Account               SubscriptionName              TenantId                             Environment
    -------               ----------------              --------                             -----------
    EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d aztest
    
   PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\CreateImage\CreateImage.json"
    PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\CreateImage\CreateImage.parameters.json"
    PS C:\WINDOWS\system32> $RGName = "rg2"
    PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment4"
        
    DeploymentName          : deployment4
    ResourceGroupName       : rg2
    ProvisioningState       : Succeeded
    Timestamp               : 12/10/2020 7:06:57 PM
    Mode                    : Incremental
    TemplateLink            :
    Parameters              :
                              Name             Type                       Value
                              ===============  =========================  ==========
                              osType           String                     Linux
                              imageName        String                     myaselinuximg
                              imageUri         String
                              https://sa2.blob.myasegpuvm.wdshcsso.com/con1/ubuntu18.04waagent.vhd
    
    Outputs                 :
    DeploymentDebugLogLevel :    
    PS C:\WINDOWS\system32>
    ```
    
## <a name="create-vm"></a>Criar VM

### <a name="edit-parameters-file-to-create-vm"></a>Editar arquivo de parâmetros para criar a VM
 
Para criar uma VM, use o arquivo de parâmetro `CreateVM.parameters.json`. Ele usa os seguintes parâmetros.
    
```json
"vmName": {
            "value": "<Name for your VM>"
        },
        "adminUsername": {
            "value": "<Username to log into the VM>"
        },
        "Password": {
            "value": "<Password to log into the VM>"
        },
        "imageName": {
            "value": "<Name for your image>"
        },
        "vmSize": {
            "value": "<A supported size for your VM>"
        },
        "vnetName": {
            "value": "<Name for the virtual network, use ASEVNET>"
        },
        "subnetName": {
            "value": "<Name for the subnet, use ASEVNETsubNet>"
        },
        "vnetRG": {
            "value": "<Resource group for Vnet, use ASERG>"
        },
        "nicName": {
            "value": "<Name for the network interface>"
        },
        "privateIPAddress": {
            "value": "<Private IP address, enter a static IP in the subnet created earlier or leave empty to assign DHCP>"
        },
        "IPConfigName": {
            "value": "<Name for the ipconfig associated with the network interface>"
        }
```    

Atribua parâmetros apropriados no `CreateVM.parameters.json` para seu dispositivo Azure Stack Edge pro.

1. Forneça um nome exclusivo, o nome da interface de rede e o nome do ipconfig. 
1. Insira um nome de usuário, uma senha e um tamanho de VM com suporte.
1. Quando você habilitou a interface de rede para computação, um comutador virtual e uma rede virtual foram criados automaticamente nessa interface de rede. Você pode consultar a rede virtual existente para obter o nome da vnet, o nome da sub-rede e o nome do grupo de recursos de vnet.

    Execute o comando a seguir:

    ```powershell
    Get-AzureRmVirtualNetwork
    ```
    Veja o exemplo de saída:
    
    ```powershell
    
    PS C:\WINDOWS\system32> Get-AzureRmVirtualNetwork
    
    Name                   : ASEVNET
    ResourceGroupName      : ASERG
    Location               : dbelocal
    Id                     : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/ASERG/providers/Microsoft
                             .Network/virtualNetworks/ASEVNET
    Etag                   : W/"990b306d-18b6-41ea-a456-b275efe21105"
    ResourceGuid           : f8309d81-19e9-42fc-b4ed-d573f00e61ed
    ProvisioningState      : Succeeded
    Tags                   :
    AddressSpace           : {
                               "AddressPrefixes": [
                                 "10.57.48.0/21"
                               ]
                             }
    DhcpOptions            : null
    Subnets                : [
                               {
                                 "Name": "ASEVNETsubNet",
                                 "Etag": "W/\"990b306d-18b6-41ea-a456-b275efe21105\"",
                                 "Id": "/subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/ASERG/provider
                             s/Microsoft.Network/virtualNetworks/ASEVNET/subnets/ASEVNETsubNet",
                                 "AddressPrefix": "10.57.48.0/21",
                                 "IpConfigurations": [],
                                 "ResourceNavigationLinks": [],
                                 "ServiceEndpoints": [],
                                 "ProvisioningState": "Succeeded"
                               }
                             ]
    VirtualNetworkPeerings : []
    EnableDDoSProtection   : false
    EnableVmProtection     : false
    
    PS C:\WINDOWS\system32>
    ```

    Use ASEVNET para nome da vnet, ASEVNETsubNet para nome da sub-rede e ASERG para nome do grupo de recursos de vnet.
    
1. Agora você precisará de um endereço IP estático para atribuir à VM que está na rede de sub-rede definida acima. Substitua **PrivateIPAddress** por esse endereço no arquivo de parâmetro. Para que a VM obtenha um endereço IP do servidor DCHP local, deixe o `privateIPAddress` valor em branco.  
    
    ```json
    "privateIPAddress": {
                "value": "5.5.153.200"
            },
    ```
    
4. Salve o arquivo de parâmetros.

    Aqui está um exemplo de JSON que é usado neste artigo.
    
    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
          "vmName": {
              "value": "VM1"
          },
          "adminUsername": {
              "value": "Administrator"
          },
          "Password": {
              "value": "Password1"
          },
        "imageName": {
          "value": "myaselinuximg"
        },
        "vmSize": {
          "value": "Standard_NC4as_T4_v3"
        },
        "vnetName": {
          "value": "ASEVNET"
        },
        "subnetName": {
          "value": "ASEVNETsubNet"
        },
        "vnetRG": {
          "value": "aserg"
        },
        "nicName": {
          "value": "nic5"
        },
        "privateIPAddress": {
          "value": ""
        },
        "IPConfigName": {
          "value": "ipconfig5"
        }
      }
    }
    ```      

### <a name="deploy-template-to-create-vm"></a>Implantar modelo para criar VM

Implante o modelo de criação de VM `CreateVM.json` . Este modelo cria uma interface de rede a partir da VNet existente e cria a VM a partir da imagem implantada.

1. Execute o comando a seguir: 
    
    ```powershell
    Command:
        
        $templateFile = "<Path to CreateVM.json>"
        $templateParameterFile = "<Path to CreateVM.parameters.json>"
        $RGName = "<Resource group name>"
             
        New-AzureRmResourceGroupDeployment `
            -ResourceGroupName $RGName `
            -TemplateFile $templateFile `
            -TemplateParameterFile $templateParameterFile `
            -Name "<DeploymentName>"
    ```   

    A criação da VM levará 15-20 minutos. Aqui está um exemplo de saída de uma VM criada com êxito.
    
    ```powershell
    PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\CreateVM\CreateVM.json"
    PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\CreateVM\CreateVM.parameters.json"
    PS C:\WINDOWS\system32> $RGName = "rg2"
    PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "Deployment6"
       
    DeploymentName          : Deployment6
    ResourceGroupName       : rg2
    ProvisioningState       : Succeeded
    Timestamp               : 12/10/2020 7:51:28 PM
    Mode                    : Incremental
    TemplateLink            :
    Parameters              :
                              Name             Type                       Value
                              ===============  =========================  ==========
                              vmName           String                     VM1
                              adminUsername    String                     Administrator
                              password         String                     Password1
                              imageName        String                     myaselinuximg
                              vmSize           String                     Standard_NC4as_T4_v3
                              vnetName         String                     ASEVNET
                              vnetRG           String                     aserg
                              subnetName       String                     ASEVNETsubNet
                              nicName          String                     nic5
                              ipConfigName     String                     ipconfig5
                              privateIPAddress  String
    
    Outputs                 :
    DeploymentDebugLogLevel :
    
    PS C:\WINDOWS\system32
    ```   

    Você também pode executar o `New-AzureRmResourceGroupDeployment` comando de forma assíncrona com o `–AsJob` parâmetro. Aqui está um exemplo de saída quando o cmdlet é executado em segundo plano. Em seguida, você pode consultar o status do trabalho criado usando o `Get-Job` cmdlet.

    ```powershell   
    PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment `
    >>     -ResourceGroupName $RGName `
    >>     -TemplateFile $templateFile `
    >>     -TemplateParameterFile $templateParameterFile `
    >>     -Name "Deployment2" `
    >>     -AsJob
     
    Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
    --     ----            -------------   -----         -----------     --------             -------
    2      Long Running... AzureLongRun... Running       True            localhost            New-AzureRmResourceGro...
     
    PS C:\WINDOWS\system32> Get-Job -Id 2
     
    Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
    --     ----            -------------   -----         -----------     --------             -------
    ```

7. Verifique se a VM foi provisionada com êxito. Execute o comando a seguir:

    `Get-AzureRmVm`


## <a name="connect-to-a-vm"></a>Como conectar-se a uma VM

Dependendo se você criou uma VM do Windows ou do Linux, as etapas para conectar podem ser diferentes.

### <a name="connect-to-windows-vm"></a>Conectar-se à VM do Windows

Siga estas etapas para se conectar a uma VM do Windows.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-windows.md)]

### <a name="connect-to-linux-vm"></a>Conectar-se à VM do Linux

Siga estas etapas para se conectar a uma VM do Linux.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-linux.md)]



## <a name="next-steps"></a>Próximas etapas

[Azure Resource Manager cmdlets](/powershell/module/azurerm.resources/?view=azurermps-6.13.0&preserve-view=true)