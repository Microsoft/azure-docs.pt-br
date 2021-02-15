---
title: Implantar VMs em seu dispositivo Azure Stack Edge pro GPU via script Azure PowerShell
description: Descreve como criar e gerenciar máquinas virtuais (VMs) em um dispositivo Azure Stack Edge pro usando um script Azure PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 12/22/2020
ms.author: alkohli
ms.openlocfilehash: 44ac0b06bb72d722797a20ed44ece14768c80997
ms.sourcegitcommit: 799f0f187f96b45ae561923d002abad40e1eebd6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/24/2020
ms.locfileid: "97763741"
---
# <a name="deploy-vms-on-your-azure-stack-edge-pro-gpu-device-via-azure-powershell-script"></a>Implantar VMs em seu dispositivo Azure Stack Edge pro GPU via script Azure PowerShell

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Este tutorial descreve como criar e gerenciar uma VM em seu dispositivo Azure Stack Edge pro usando um script Azure PowerShell.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar a criar e gerenciar uma VM em seu dispositivo Azure Stack Edge pro usando esse script, você precisará certificar-se de ter concluído os pré-requisitos listados nas etapas a seguir:

### <a name="for-azure-stack-edge-pro-device-via-the-local-web-ui"></a>Para Azure Stack dispositivo pro Edge por meio da interface do usuário da Web local

[!INCLUDE [azure-stack-edge-gateway-deploy-vm-prerequisites](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-prerequisites.md)]

### <a name="for-your-windows-client"></a>Para seu cliente Windows

1. Certifique-se de que você modificou: 

    - Ao arquivo de host no cliente OU
    - À configuração do servidor DNS
    
    > [!IMPORTANT]
    > Recomendamos que você modifique a configuração do servidor DNS para a resolução de nome do ponto de extremidade.

    1. Inicie o **Bloco de notas** como administrador (é necessário ter privilégios de Administrador para salvar o arquivo) e abra o arquivo **hosts** localizado em `C:\Windows\System32\Drivers\etc`.
    
        ![Arquivo hosts do Windows Explorer](media/azure-stack-edge-j-series-connect-resource-manager/hosts-file.png)
    
    2. Adicione as seguintes entradas ao arquivo **hosts** substituindo-as por valores apropriados para seu dispositivo:
    
        ```
        <device IP> login.<appliance name>.<DNS domain>
        <device IP> management.<appliance name>.<DNS domain>
        <device IP> <storage name>.blob.<appliance name>.<DNS domain>
        ```
        Para a conta de armazenamento, você pode fornecer um nome que você deseja que o script use posteriormente para criar uma nova conta de armazenamento. O script não verifica se essa conta de armazenamento é existente.

    3. Use a imagem a seguir para referência. Salve o arquivo **hosts**.

        ![Arquivo hosts no Bloco de notas](media/azure-stack-edge-j-series-deploy-virtual-machine-cli-python/hosts-screenshot-boxed.png)

2. [Baixe o script do PowerShell](https://aka.ms/ase-vm-powershell) usado neste procedimento.

3. Verifique se o seu cliente Windows está executando o PowerShell 5,0 ou posterior.

4. Certifique-se de que o `Azure.Storage Module version 4.5.0` esteja instalado em seu sistema. Você pode obter esse módulo do [Galeria do PowerShell](https://www.powershellgallery.com/packages/Azure.Storage/4.5.0). Para instalar este módulo, digite:

    `Install-Module -Name Azure.Storage -RequiredVersion 4.5.0`

    Para verificar a versão do módulo instalado, digite:

    `Get-InstalledModule -name Azure.Storage`

    Para desinstalar qualquer outro módulo de versão, digite:

    `Uninstall-Module -Name Azure.Storage`

5. [Baixe o AzCopy 10](../storage/common/storage-use-azcopy-v10.md#download-azcopy) para o cliente do Windows. Anote esse local, pois você irá passá-lo como um parâmetro durante a execução do script.

6. Verifique se o seu cliente Windows está executando o TLS 1,2 ou posterior.


## <a name="create-a-vm"></a>Criar uma máquina virtual

1. Execute o PowerShell como administrador.
1. Vá para a pasta em que você baixou o script em seu cliente.
1. Antes de executar o script, verifique se você ainda está conectado ao Azure Resource Manager local do dispositivo e se a conexão não expirou.

    ```powershell
    PS C:\windows\system32> login-AzureRMAccount -EnvironmentName aztest1 -TenantId c0257de7-538f-415c-993a-1b87a031879d

    Account               SubscriptionName              TenantId                             Environment
    -------               ----------------              --------                             -----------
    EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d aztest1

    PS C:\windows\system32> cd C:\Users\v2
    PS C:\Users\v2>
    ```  
1. Use o comando a seguir para executar o script:
 
    `.\ArmPowershellClient.ps1 -NicPrivateIp <Private IP> -VHDPath <Path> -VHDFile <VHD File, with extension> -StorageAccountName <Name> -OS <Windows/Linux> -VMSize <Supported VM Size> -VMUserName <Username to be used to sign in to VM> -VMPassword <Password for the VM> --AzCopy10Path <Absolute Path>`

    Se você quiser que o IP seja alocado dinamicamente para a VM, omita o `-NicPrivateIp` parâmetro.

    Aqui estão os exemplos em que o script é executado para criar uma VM do Windows e uma VM do Linux.

    **Para uma VM do Windows:**

    Aqui está um exemplo de saída para uma VM do Windows que foi criada.

    ```powershell
    PS C:\Users\v2> .\ArmPowershellClient.ps1 -VHDPath \\asefs\Logs\vmvhd -VHDFile WindowsServer2016Datacenter.vhd -StorageAccountName myasesatest -OS Windows -VMSize Standard_D1_v2 -VMUserName Administrator -VMPassword Password1 -AzCopy10Path C:\Users\AzCopy10\AzCopy.exe
    New-AzureRmResourceGroup -Name rg201221071831 -Location DBELocal -Force
    Successfully created Resource Group:rg201221071831
    Successfully created Resource Group:StorAccRG
    Get-AzureRmStorageAccount -Name myasesatest -ResourceGroupName StorAccRG -ErrorAction SilentlyContinue
    New-AzureRmStorageAccount -Name myasesatest  -ResourceGroupName StorAccRG -SkuName Standard_LRS -Location DBELocal
    
    Created New Storage Account
    Get-AzureRmStorageAccount -name myasesatest -resourcegroupname
    StorageAccountName ResourceGroupName Location SkuName     Kind    AccessTier CreationTime          ProvisioningState EnableHttpsTrafficOnly
    ------------------ ----------------- -------- -------     ----    ---------- ------------          ----------------- ----------------------
    myasesatest        StorAccRG         DBELocal StandardLRS Storage            12/22/2020 3:18:38 AM Succeeded         False
    myasesatest        StorAccRG         DBELocal StandardLRS Storage            12/22/2020 3:18:38 AM Succeeded         False
    
     Uploading Vhd to Storage Account
    
    New-AzureStorageContext -StorageAccountName myasesatest -StorageAccountKey hyibjhbVlOROgTlU1nQJIlxrg94eGDhF+RIQ71Z7UVZIxoOPMlHP274NUhZtA1hMxGBcpk2BVApiFasFPEhY/A== -Endpoint https://myasesatest.blob.myasegpuvm.wdshcsso.com/
    
    New-AzureStorageAccountSASToken -Service Blob,File,Queue,Table -ResourceType Container,Service,Object -Permission
    
    SAS Token : ?sv=2017-07-29&sig=TXaGbjum9tFFaJnu3SFmDuslJuqNiNQwvuHfpPJMYN0%3D&spr=https&se=2020-12-22T04%3A18%3A43Z&srt=sco&ss=bfqt&sp=racwdl
    
     C:\Users\AzCopy10\AzCopy.exe make https://myasesatest.blob.myasegpuvm.wdshcsso.com/vmimages?sv=2017-07-29&sig=TXaGbjum9tFFaJnu3SFmDuslJuqNiNQwvuHfpPJMYN0%3D&spr=https&se=2020-12-22T04%3A18%3A43Z&srt=sco&ss=bfqt&sp=racwdl
    
    Successfully created the resource.
    
     AzCopy cp \\asefs\Logs\vmvhd\WindowsServer2016Datacenter.vhd https://myasesatest.blob.myasegpuvm.wdshcsso.com/vmimages?sv=2017-07-29&sig=TXaGbjum9tFFaJnu3SFmDuslJuqNiNQwvuHfpPJMYN0%3D&spr=https&se=2020-12-22T04%3A18%3A43Z&srt=sco&ss=bfqt&sp=racwdl
    
    INFO: Scanning...
    
    Job b6f54665-93c4-2f47-4770-5f3b7b0de2dc has started
    Log file is located at: C:\Users\Administrator\.azcopy\b6f54665-93c4-2f47-4770-5f3b7b0de2dc.log
    
    INFO: AzCopy.exe: A newer version 10.8.0 is available to download
    
    99.9 %, 0 Done, 0 Failed, 1 Pending, 0 Skipped, 1 Total,  (Disk may be limiting speed)
    
    Job b6f54665-93c4-2f47-4770-5f3b7b0de2dc summary
    Elapsed Time (Minutes): 12.7717
    Total Number Of Transfers: 1
    Number of Transfers Completed: 1
    Number of Transfers Failed: 0
    Number of Transfers Skipped: 0
    TotalBytesTransferred: 13958644224
    Final Job Status: Completed
    
    VHD Upload Done
    
     Creating a new managed disk
    
      = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import -SourceUri
    
     Microsoft.Azure.Commands.Compute.Automation.Models.PSDisk
    
     New-AzureRmDisk -ResourceGroupName rg201221071831 -DiskName ld201221071831 -Disk
    
    ResourceGroupName  : rg201221071831
    ManagedBy          :
    Sku                : Microsoft.Azure.Management.Compute.Models.DiskSku
    Zones              :
    TimeCreated        : 12/21/2020 7:31:35 PM
    OsType             :
    CreationData       : Microsoft.Azure.Management.Compute.Models.CreationData
    DiskSizeGB         : 13
    EncryptionSettings :
    ProvisioningState  : Succeeded
    Id                 : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/rg201221071831/providers/Microsoft.Compute/disks/ld201221071831
    Name               : ld201221071831
    Type               : Microsoft.Compute/disks
    Location           : DBELocal
    Tags               : {}
    
     Created a new managed disk
    
     Creating a new Image out of managed disk
    
    ResourceGroupName    :
    SourceVirtualMachine :
    StorageProfile       : Microsoft.Azure.Management.Compute.Models.ImageStorageProfile
    ProvisioningState    :
    Id                   :
    Name                 :
    Type                 :
    Location             : DBELocal
    Tags                 :
    
     New-AzureRmImage -Image Microsoft.Azure.Commands.Compute.Automation.Models.PSImage -ImageName ig201221071831  -ResourceGroupName rg201221071831
    
    ResourceGroupName    : rg201221071831
    SourceVirtualMachine :
    StorageProfile       : Microsoft.Azure.Management.Compute.Models.ImageStorageProfile
    ProvisioningState    : Succeeded
    Id                   : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/rg201221071831/providers/Microsoft.Compute/images/ig201221071831
    Name                 : ig201221071831
    Type                 : Microsoft.Compute/images
    Location             : dbelocal
    Tags                 : {}
    
     Created a new Image
    
     Using Vnet /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/ASERG/providers/Microsoft.Network/virtualNetworks/ASEVNET
    
     Creating a new Newtork Interface
    WARNING: The output object type of this cmdlet will be modified in a future release.
    
    VirtualMachine              :
    IpConfigurations            : {ip201221071831}
    DnsSettings                 : Microsoft.Azure.Commands.Network.Models.PSNetworkInterfaceDnsSettings
    MacAddress                  : 001DD87D7216
    Primary                     :
    EnableAcceleratedNetworking : False
    EnableIPForwarding          : False
    NetworkSecurityGroup        :
    ProvisioningState           : Succeeded
    VirtualMachineText          : null
    IpConfigurationsText        : [
                                    {
                                      "Name": "ip201221071831",
                                      "Etag": "W/\"27785dd5-d12a-4d73-9495-ffad7847261a\"",
                                      "Id": "/subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/rg201221071831/providers/Microsoft.Network/networkInterfaces/nic201221071831/ipConfigurations/ip201221071831",
                                      "PrivateIpAddress": "10.57.51.61",
                                      "PrivateIpAllocationMethod": "Dynamic",
                                      "Subnet": {
                                        "Id": "/subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/ASERG/providers/Microsoft.Network/virtualNetworks/ASEVNET/subnets/ASEVNETsubNet",
                                        "ResourceNavigationLinks": [],
                                        "ServiceEndpoints": []
                                      },
                                      "ProvisioningState": "Succeeded",
                                      "PrivateIpAddressVersion": "IPv4",
                                      "LoadBalancerBackendAddressPools": [],
                                      "LoadBalancerInboundNatRules": [],
                                      "Primary": true,
                                      "ApplicationGatewayBackendAddressPools": [],
                                      "ApplicationSecurityGroups": []
                                    }
                                  ]
    DnsSettingsText             : {
                                    "DnsServers": [],
                                    "AppliedDnsServers": [],
                                    "InternalDomainNameSuffix": "qgotb4hjdh4efnhn0vz5adtb3f.a--x.internal.cloudapp.net"
                                  }
    NetworkSecurityGroupText    : null
    ResourceGroupName           : rg201221071831
    Location                    : dbelocal
    ResourceGuid                : e6327ab9-0855-4f04-9b36-17bbf31b5bd8
    Type                        : Microsoft.Network/networkInterfaces
    Tag                         :
    TagsTable                   :
    Name                        : nic201221071831
    Etag                        : W/"27785dd5-d12a-4d73-9495-ffad7847261a"
    Id                          : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/rg201221071831/providers/Microsoft.Network/networkInterfaces/nic201221071831
    
     Created Network Interface
    
     Creating a new VM
    
     New-AzureRmVMConfig -VMName VM201221071831 -VMSize Standard_D1_v2
    
     Set-AzureRmVMOperatingSystem -VM Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine -Windows -ComputerName COM201221071831 -Credential System.Management.Automation.PSCredential
    
     Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine = Set-AzureRmVMOSDisk -VM Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine -Name osld201221071831 -Caching ReadWrite -CreateOption FromImage -Windows -StorageAccountType StandardLRS
    
     Add-AzureRmVMNetworkInterface -VM Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine -Id /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/rg201221071831/providers/Microsoft.Network/networkInterfaces/nic201221071831.Id
    
     Set-AzureRmVMSourceImage -VM Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine -Id /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/rg201221071831/providers/Microsoft.Compute/images/ig201221071831
    
     New-AzureRmVM -ResourceGroupName rg201221071831 -Location DBELocal -VM Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine -Verbose
    WARNING: Since the VM is created using premium storage or managed disk, existing standard storage account, myasesa1, is used for boot
    diagnostics.
    VERBOSE: Performing the operation "New" on target "VM201221071831".
    
    Ticks             : 1533424841
    Days              : 0
    Hours             : 0
    Milliseconds      : 342
    Minutes           : 2
    Seconds           : 33
    TotalDays         : 0.00177479726967593
    TotalHours        : 0.0425951344722222
    TotalMilliseconds : 153342.4841
    TotalMinutes      : 2.55570806833333
    TotalSeconds      : 153.3424841
    
    RequestId           :
    IsSuccessStatusCode : True
    StatusCode          : OK
    ReasonPhrase        : OK

    PS C:\Users\v2>
    ```

    **Para uma VM do Linux:**

    Aqui está o exemplo do comando que foi usado para criar uma VM do Linux.

    ```powershell
    .\ArmPowershellClient.ps1 -VHDPath \\asefs\Logs\vmvhd -VHDFile ubuntu13.vhd -StorageAccountName myasesatest -OS Linux -VMSize Standard_D1_v2 -VMUserName Administrator -VMPassword Password1 -AzCopy10Path C:\Users\AzCopy10\AzCopy.exe
    New-AzureRmResourceGroup -Name rg201221075546 -Location DBELocal -Force    
    ```

  
1. Depois de criar as VMs com êxito, essas VMs devem aparecer na lista de máquinas virtuais no portal do Azure. Para exibir as VMs, no Azure Stack recurso do Edge para seu dispositivo no portal do Azure, acesse **serviços de borda > máquinas virtuais**. 

    ![Exibir lista de máquinas virtuais](media/azure-stack-edge-gpu-deploy-virtual-machine-powershell-script/list-virtual-machine-1.png)

    Para exibir os detalhes de uma VM, selecione o nome da VM. Observe a alocação dinâmica de IP para essa VM.

    ![Exibir detalhes da VM](media/azure-stack-edge-gpu-deploy-virtual-machine-powershell-script/view-virtual-machine-details-1.png)

1. Para limpar os recursos criados pelo script, use os seguintes comandos:
    
    ```powershell
    Get-AzureRmVM | Remove-AzureRmVM -Force
    Get-AzureRmNetworkInterface | Remove-AzureRmNetworkInterface -Force
    Get-AzureRmImage | Remove-AzureRmImage -Force
    Get-AzureRmDisk | Remove-AzureRmDisk -Force
    Get-AzureRmStorageAccount | Remove-AzureRmStorageAccount -Force
    ```


## <a name="next-steps"></a>Próximas etapas

[Implantar VMs usando cmdlets Azure PowerShell](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)