---
title: Criar uma VM (Clássica) com diversas NICs - Azure PowerShell | Microsoft Docs
description: Saiba como criar uma VM (Clássica) com diversas NICs usando o PowerShell.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: 6e50f39a-2497-4845-a5d4-7332dbc203c5
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 087b52bd603e8aed6078ab340e84c1f6bd0e8082
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60748483"
---
# <a name="create-a-vm-classic-with-multiple-nics-using-powershell"></a>Criar uma VM (Clássica) com diversas NICs usando PowerShell

[!INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

Você pode criar máquinas virtuais (VMs) no Azure e anexar várias interfaces de rede (NICs) para cada uma de suas VMs. Várias NICs permitem a separação dos tipos de tráfego entre NICs. Por exemplo, uma NIC pode se comunicar com a Internet, enquanto outra se comunica apenas com recursos internos que não estão conectados à Internet. A capacidade de separar o tráfego de rede entre as várias NICs é necessária para vários dispositivos de rede virtual, como a entrega de aplicativos e soluções de otimização de WAN.

> [!IMPORTANT]
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos:  [Resource Manager e clássico](../resource-manager-deployment-model.md). Este artigo aborda o uso do modelo de implantação clássica. A Microsoft recomenda que a maioria das implantações novas use o modelo do Gerenciador de Recursos. Saiba como executar essas etapas usando o [modelo de implantação do Resource Manager](../virtual-machines/windows/multiple-nics.md).

[!INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

As etapas a seguir usam um grupo de recursos chamado *IaaSStory* para os serviços Web e o grupo de recursos e *IaaSStory-BackEnd* para os servidores DB.

## <a name="prerequisites"></a>Pré-requisitos

Antes de criar os servidores DB, você precisa criar o grupo de recursos *IaaSStory* com todos os recursos necessários para este cenário. Para criar esses recursos, conclua as etapas a seguir. Crie uma rede virtual seguindo as etapas no artigo [Criar uma rede virtual](virtual-networks-create-vnet-classic-netcfg-ps.md).

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-the-back-end-vms"></a>Criar VMs de back-end
As VMs de back-end dependem da criação dos seguintes recursos:

* **Sub-rede de back-end**. Os servidores de banco de dados serão parte de uma sub-rede separada, para segregar o tráfego. O script a seguir espera que essa sub-rede exista em uma vnet chamada *WTestVnet*.
* **Conta de armazenamento para discos de dados**. Para obter um melhor desempenho, os discos de dados dos servidores de banco de dados usam a tecnologia SDD (unidade de estado sólido), que requer uma conta de Armazenamento Premium. Verifique se o local do Azure no qual você vai implantar é compatível com o Armazenamento Premium.
* **Conjunto de disponibilidade**. Todos os servidores de banco de dados são adicionados a um conjunto de disponibilidade único, para garantir que pelo menos uma das VMs está ativa e em execução durante a manutenção.

### <a name="step-1---start-your-script"></a>Etapa 1 – Iniciar o script
Baixe o script completo do PowerShell usado [aqui](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-ps.ps1). Realize os procedimentos abaixo para alterar o script para funcionar em seu ambiente.

1. Altere os valores das variáveis a seguir com base no grupo de recursos existente implantado acima, no tópico [Pré-requisitos](#prerequisites).

    ```powershell
    $location              = "West US"
    $vnetName              = "WTestVNet"
    $backendSubnetName     = "BackEnd"
    ```

2. Altere os valores das variáveis a seguir de acordo com os valores que deseja usar na implantação do back-end.

    ```powershell
    $backendCSName         = "IaaSStory-Backend"
    $prmStorageAccountName = "iaasstoryprmstorage"
    $avSetName             = "ASDB"
    $vmSize                = "Standard_DS3"
    $diskSize              = 127
    $vmNamePrefix          = "DB"
    $dataDiskSuffix        = "datadisk"
    $ipAddressPrefix       = "192.168.2."
    $numberOfVMs           = 2
    ```

### <a name="step-2---create-necessary-resources-for-your-vms"></a>Etapa 2: criar recursos necessários para as VMs
Você precisa criar um novo serviço de nuvem e conta de armazenamento para os discos de dados para todas as VMs. Você também precisa especificar uma imagem e uma conta de administrador local para as VMs. Para criar esses recursos, siga estes etapas:

1. Crie um novo serviço de nuvem

    ```powershell
    New-AzureService -ServiceName $backendCSName -Location $location
    ```

2. Crie uma nova conta de armazenamento Premium.

    ```powershell
    New-AzureStorageAccount -StorageAccountName $prmStorageAccountName `
    -Location $location -Type Premium_LRS
    ```
3. Defina a conta de armazenamento criada acima como a conta de armazenamento para sua assinatura.

    ```powershell
    $subscription = Get-AzureSubscription | where {$_.IsCurrent -eq $true}  
    Set-AzureSubscription -SubscriptionName $subscription.SubscriptionName `
    -CurrentStorageAccountName $prmStorageAccountName
    ```

4. Selecione uma imagem para a VM.

    ```powershell
    $image = Get-AzureVMImage `
    | where{$_.ImageFamily -eq "SQL Server 2014 RTM Web on Windows Server 2012 R2"} `
    | sort PublishedDate -Descending `
    | select -ExpandProperty ImageName -First 1
    ```

5. Defina as credenciais da conta de administrador local.

    ```powershell
    $cred = Get-Credential -Message "Enter username and password for local admin account"
    ```

### <a name="step-3---create-vms"></a>Etapa 3 - Criar VMs
Você deve usar um loop para criar várias VMs desejadas e para criar as NICs e VMs necessárias dentro do loop. Para criar as NICs e VMs, faça o seguinte:

1. Inicie um loop `for` para repetir os comandos para criar uma VM e duas NICs, quantas vezes forem necessárias, com base no valor da variável `$numberOfVMs`.

    ```powershell
    for ($suffixNumber = 1; $suffixNumber -le $numberOfVMs; $suffixNumber++){
    ```

2. Crie um objeto `VMConfig` que especifica a imagem, o tamanho e o conjunto de disponibilidade da VM.

    ```powershell
    $vmName = $vmNamePrefix + $suffixNumber
    $vmConfig = New-AzureVMConfig -Name $vmName `
        -ImageName $image `
        -InstanceSize $vmSize `
        -AvailabilitySetName $avSetName
    ```

3. Provisione a VM como uma VM do Windows.

    ```powershell
    Add-AzureProvisioningConfig -VM $vmConfig -Windows `
        -AdminUsername $cred.UserName `
        -Password $cred.GetNetworkCredential().Password
    ```

4. Defina a NIC padrão e atribua um endereço IP estático.

    ```powershell
    Set-AzureSubnet         -SubnetNames $backendSubnetName -VM $vmConfig
    Set-AzureStaticVNetIP   -IPAddress ($ipAddressPrefix+$suffixNumber+3) -VM $vmConfig
    ```

5. Adicione uma segunda NIC para cada VM.

    ```powershell
    Add-AzureNetworkInterfaceConfig -Name ("RemoteAccessNIC"+$suffixNumber) `
    -SubnetName $backendSubnetName `
    -StaticVNetIPAddress ($ipAddressPrefix+(53+$suffixNumber)) `
    -VM $vmConfig
    ```
    
6. Crie discos de dados para cada VM.

    ```powershell
    $dataDisk1Name = $vmName + "-" + $dataDiskSuffix + "-1"    
    Add-AzureDataDisk -CreateNew -VM $vmConfig `
    -DiskSizeInGB $diskSize `
    -DiskLabel $dataDisk1Name `
    -LUN 0

    $dataDisk2Name = $vmName + "-" + $dataDiskSuffix + "-2"   
    Add-AzureDataDisk -CreateNew -VM $vmConfig `
    -DiskSizeInGB $diskSize `
    -DiskLabel $dataDisk2Name `
    -LUN 1
    ```

7. Crie cada VM e encerre o loop.

    ```powershell
    New-AzureVM -VM $vmConfig `
    -ServiceName $backendCSName `
    -Location $location `
    -VNetName $vnetName
    }
    ```

### <a name="step-4---run-the-script"></a>Etapa 4: executar o script
Agora que você baixou e alterou o script de acordo com suas necessidades, execute o script para criar VMs do banco de dados de back-end com várias NICs.

1. Salve seu script e execute-o no prompt de comando do **PowerShell** ou **PowerShell ISE**. A saída inicial será exibida, conforme mostrado abaixo:

        OperationDescription    OperationId                          OperationStatus

        New-AzureService        xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        New-AzureStorageAccount xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        
        WARNING: No deployment found in service: 'IaaSStory-Backend'.
2. Preencha as informações necessárias na solicitação de credenciais e clique em **OK**. A seguinte saída será retornada.

        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded

### <a name="step-5---configure-routing-within-the-vms-operating-system"></a>Etapa 5 – Configurar o roteamento dentro do sistema operacional da VM

O protocolo DHCP do Azure atribui um gateway padrão ao primeiro adaptador de rede (primário) anexado à máquina virtual. O Azure não atribui um gateway padrão aos adaptadores de rede adicionais (secundários) anexados à máquina virtual. Portanto, por padrão, não é possível se comunicar com os recursos fora da sub-rede na qual um adaptador de rede secundária se encontra. No entanto, os adaptador de rede secundários podem se comunicar com os recursos fora da sua sub-rede. Para configurar o roteamento de interfaces de rede secundárias, consulte os seguintes artigos:

- [Configurar uma VM do Windows para vários NICs](../virtual-machines/windows/multiple-nics.md#configure-guest-os-for-multiple-nics
)

- [Configurar uma VM do Linux para vários NICs](../virtual-machines/linux/multiple-nics.md#configure-guest-os-for-multiple-nics
)
