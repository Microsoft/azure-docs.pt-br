---
title: Usar o Armazenamento Premium do Azure com o SQL Server | Microsoft Docs
description: Este artigo usa recursos criados com o modelo clássico de implantação e fornece orientação sobre como usar o Armazenamento Premium do Azure com o SQL Server em execução em máquinas virtuais do Azure.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 7ccf99d7-7cce-4e3d-bbab-21b751ab0e88
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/01/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 3b3bb206286629a68c14b6444f3f88ffa0af50dd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60582882"
---
# <a name="use-azure-premium-storage-with-sql-server-on-virtual-machines"></a>Usar o Armazenamento Premium do Azure com o SQL Server em máquinas virtuais

## <a name="overview"></a>Visão geral

[SSDs premium do Azure](../disks-types.md) é o armazenamento de última geração que oferece baixa latência e E/S de taxa de transferência alta. Ele funciona melhor para cargas de trabalho de uso intensivo de E/S de chave, como [Máquinas Virtuais](https://azure.microsoft.com/services/virtual-machines/)do SQL Server no IaaS.

> [!IMPORTANT]
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../../../azure-resource-manager/resource-manager-deployment-model.md). Este artigo aborda o uso do modelo de implantação Clássica. A Microsoft recomenda que a maioria das implantações novas use o modelo do Gerenciador de Recursos.

Este artigo fornece informações de planejamento e diretrizes para migração de uma Máquina Virtual executando o SQL Server para usar o Armazenamento Premium. Isso inclui a infraestrutura do Azure (rede, armazenamento) e as etapas de VM do Windows de convidado. O exemplo no [Apêndice](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) mostra uma migração de ponta a ponta abrangente e completa para mover VMs maiores e aproveitar o armazenamento SSD local aprimorado com o PowerShell.

É importante compreender o processo de ponta a ponta utilizando o Armazenamento Premium do Azure com o SQL Server em VMs IAAS. Isso inclui:

* Identificação dos pré-requisitos para usar o Armazenamento Premium.
* Exemplos de implantação do SQL Server no IaaS no Armazenamento Premium para novas implantações.
* Exemplos de migração de implantações existentes, de servidores autônomos e implantações usando grupos de disponibilidade SQL AlwaysOn.
* Abordagens de migração possíveis.
* Exemplo de ponta a ponta completo mostrando as etapas do Azure, do Windows e do SQL Server para a migração de uma implementação AlwaysOn existente.

Para obter informações gerais sobre o SQL Server nas Máquinas Virtuais do Azure, consulte [SQL Server nas Máquinas Virtuais do Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

**Autor:** Daniel Sol **Revisores técnicos:** Luis Carlos Vargas Herring, Sanjay Mishra, Pravin Mital, Juergen Thomas, Gonzalo Ruiz.

## <a name="prerequisites-for-premium-storage"></a>Pré-requisitos para o Armazenamento Premium

Existem vários pré-requisitos para o uso do Armazenamento Premium.

### <a name="machine-size"></a>Tamanho do computador

Para usar o Armazenamento Premium, você precisará usar VMs (máquinas virtuais) da série DS. Se você não usou máquinas da série DS no seu serviço de nuvem antes, exclua a VM existente, mantenha os discos anexados e, em seguida, crie um novo serviço de nuvem antes de recriar a VM conforme o tamanho da função DS *. Para saber mais sobre os tamanhos das Máquinas Virtuais, consulte [Tamanhos da Máquina Virtual e do Serviço de Nuvem do Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="cloud-services"></a>Serviços de Nuvem

Você só poderá usar VMs DS* com Armazenamento Premium quando elas forem criadas em um novo serviço de nuvem. Se você estiver usando o SQL Server AlwaysOn no Azure, o Ouvinte AlwaysOn fará referência ao endereço IP do Balanceador de Externo ou Interno de Carga do Azure que estiver associado a um serviço de nuvem. O foco deste artigo é explicar como migrar ao mesmo tempo em que a disponibilidade é mantida nesse cenário.

> [!NOTE]
> Uma série DS* deve ser a primeira VM implantada no novo serviço de nuvem.
>
>

### <a name="regional-vnets"></a>VNETS regionais

Para VMs DS*, você deve configurar a VNET (Rede Virtual) que hospeda suas VMs como regional. Essa "ampliação" da VNET tem a finalidade de permitir que VMs maiores sejam provisionadas em outros clusters e permitir a comunicação entre elas. Na captura de tela a seguir, o local realçado mostra VNETs regionais, enquanto o primeiro resultado mostra uma VNET "estreita".

![RegionalVNET][1]

Você pode gerar um tíquete de suporte da Microsoft para migrar para uma VNET regional. A Microsoft fará uma alteração. Para concluir a migração para VNETs regionais, altere a propriedade AffinityGroup na configuração de rede. Primeiro, exporte a Configuração da Rede no PowerShell, em seguida, substitua a propriedade **AffinityGroup** no elemento **VirtualNetworkSite** por uma propriedade **Location**. Especifique `Location = XXXX` onde `XXXX` é uma região do Azure. Em seguida, importe a nova configuração.

Por exemplo, considerando a seguinte configuração de VNET:

```xml
<VirtualNetworkSite name="danAzureSQLnet" AffinityGroup="AzureSQLNetwork">
<AddressSpace>
  <AddressPrefix>10.0.0.0/8</AddressPrefix>
  <AddressPrefix>172.16.0.0/12</AddressPrefix>
</AddressSpace>
<Subnets>
...
</VirtualNetworkSite>
```

Para movê-la para uma VNET regional na Europa Ocidental, altere a configuração para o seguinte:
```xml
<VirtualNetworkSite name="danAzureSQLnet" Location="West Europe">
<AddressSpace>
  <AddressPrefix>10.0.0.0/8</AddressPrefix>
  <AddressPrefix>172.16.0.0/12</AddressPrefix>
</AddressSpace>
<Subnets>
...
</VirtualNetworkSite>
```

### <a name="storage-accounts"></a>Contas de armazenamento

Você precisa criar uma nova conta de armazenamento que seja configurada para Armazenamento Premium. Observe que o uso do Armazenamento Premium é definido na conta de armazenamento, não em VHDs individuais, no entanto, ao usar uma VM série DS*, você pode anexar VHDs de contas de Armazenamento Padrão e Premium. Você poderá considerar isso se não quiser colocar o VHD do sistema operacional na conta de Armazenamento Premium.

O seguinte comando **New-AzureStorageAccountPowerShell** com o Tipo **"Premium_LRS"** cria uma Conta de Armazenamento Premium:

```powershell
$newstorageaccountname = "danpremstor"
New-AzureStorageAccount -StorageAccountName $newstorageaccountname -Location "West Europe" -Type "Premium_LRS"   
```

### <a name="vhds-cache-settings"></a>Configurações de Cache de VHDs

A principal diferença entre a criação de discos que fazem parte de uma conta de Armazenamento Premium é a configuração de cache de disco. Para discos do volume SQL Server Data, recomendamos que você use ‘**Caching de Leitura**’. Para volumes de log de transações, a configuração de cache de disco deve ser definida como ‘**Nenhum**’. Isso é diferente das recomendações para contas de Armazenamento Padrão.

Depois que os VHDs forem anexados, a configuração de cache não poderá ser alterada. Você precisaria desanexar e anexar novamente o VHD com uma configuração de cache atualizada.

### <a name="windows-storage-spaces"></a>Espaços de armazenamento do Windows

Você pode usar os [Espaços de Armazenamento do Windows](https://technet.microsoft.com/library/hh831739.aspx) como fez com o Armazenamento Standard anterior; isso permite que você migre uma VM que já utiliza os Espaços de Armazenamento. O exemplo no [Apêndice](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) (etapa 9 em diante) demonstra o código do Powershell para extrair e importar uma VM com vários VHDs anexados.

Pools de Armazenamento foram usados com a conta de armazenamento do Azure Padrão para melhorar a taxa de transferência e reduzir a latência. Talvez você ache interessante testar Pools de Armazenamento com o Armazenamento Premium para novas implantações, mas isso agrega uma complexidade adicional com a configuração do armazenamento.

#### <a name="how-to-find-which-azure-virtual-disks-map-to-storage-pools"></a>Como descobrir quais discos virtuais do Azure são mapeados para os pools de armazenamento

Como há recomendações de configuração de cache diferentes para VHDs anexados, você pode optar por copiar os VHDs em uma conta de Armazenamento Premium. No entanto, quando você anexá-los outra vez à nova VM da série DS, talvez seja necessário alterar as configurações de cache. É mais simples aplicar as configurações de cache recomendadas do Armazenamento Premium quando há VHDs separados para arquivos do SQL Data e arquivos de log (em vez de um único VHD que contém ambos).

> [!NOTE]
> Se você tiver arquivos de log e de dados do SQL Server no mesmo volume, a opção de cache que escolher dependerá dos padrões de acesso de E/S para as cargas de trabalho de banco de dados. Apenas o teste pode demonstrar qual opção de cache é ideal para esse cenário.
>
>

No entanto, se estiver usando Espaços de Armazenamento do Windows compostos de vários VHDs, você precisará examinar seus scripts originais para identificar quais VHDs anexados estão em qual pool específico, assim poderá definir as configurações de cache de acordo com cada disco.

Se você não tiver o script original disponível para mostrar quais VHDs são mapeados para o pool de armazenamento, sia as próximas etapas para determinar o mapeamento de pool de disco/armazenamento.

Para cada disco, siga estas etapas:

1. Obtenha a lista de discos anexados à VM com o comando **Get-AzureVM** :

```powershell
Get-AzureVM -ServiceName <servicename> -Name <vmname> | Get-AzureDataDisk
```

1. Anote o DiskName e o LUN.

    ![DisknameAndLUN][2]
1. Área de trabalho remota na VM. Em seguida, vá para **Gerenciamento do Computador** | **Gerenciador de Dispositivos** | **Unidades de Disco**. Examine as propriedades de cada um dos 'Discos Virtuais da Microsoft'

    ![VirtualDiskProperties][3]
1. O número de LUNs aqui é uma referência para o número de LUNs que você especificar ao anexar o VHD à VM.
1. Para o 'Disco Virtual Microsoft', vá para a guia **Detalhes**, em seguida, na lista **Propriedade**, vá para **Chave do Driver**. Em **Valor**, observe o **Deslocamento**, que é 0002 na captura de tela a seguir. O 0002 indica o PhysicalDisk2 que o pool de armazenamento referencia.

    ![VirtualDiskPropertyDetails][4]
1. Para cada pool de armazenamento, despeje os discos associados:

```powershell
Get-StoragePool -FriendlyName AMS1pooldata | Get-PhysicalDisk
```

![GetStoragePool][5]

Agora você pode usar essas informações para associar VHDs anexados a discos físicos em pools de armazenamento.

Após o mapeamento de VHDs para discos físicos nos pools de armazenamento, você poderá desanexá-los e copiá-los em uma conta de Armazenamento Premium e depois anexá-los com a configuração de cache correta. Veja o exemplo no [Apêndice](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage), etapas 8 a 12. Essas etapas mostram como extrair uma configuração de disco VHD anexado à VM para um arquivo CSV, copiar os VHDs, alterar as configurações de cache de configuração do disco e finalmente reimplantar a VM como uma VM da série DS com todos os discos anexados.

### <a name="vm-storage-bandwidth-and-vhd-storage-throughput"></a>Largura de banda de armazenamento de VM e taxa de transferência de armazenamento de VHD

O desempenho de armazenamento depende do tamanho da VM DS* especificado e dos tamanhos de VHD. As VMs têm concessões diferentes para o número de VHDs que podem ser anexados e a largura de banda máxima a que eles dão suporte (MB/s). Para obter os números específicos da largura de banda, consulte [Tamanhos da Máquina Virtual e do Serviço de Nuvem do Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Mais IOPS são obtidos com tamanhos de disco maiores. Considere isso quando você pensar em seu caminho de migração. Para obter detalhes, [consulte a tabela de IOPS e Tipos de Disco](../disks-types.md#premium-ssd).

Por fim, considere que as VMs têm larguras de banda máxima de disco diferentes com suporte para todos os discos anexados. Em cargas elevadas, você poderia saturar a largura de banda máxima de disco disponível para esse tamanho de função de VM. Por exemplo, um Standard_DS14 dá suporte a até 512 MB/s e, portanto, com três discos P30 você poderia saturar a largura de banda do disco da VM. Porém, neste exemplo, o limite de taxa de transferência poderia ser excedido dependendo da combinação de E/Ss de leitura e gravação.

## <a name="new-deployments"></a>Novas implantações

As próximas duas seções demonstram como você pode implantar VMs do SQL Server no Armazenamento Premium. Como mencionado antes, você não precisa necessariamente colocar o disco do sistema operacional no Armazenamento Premium. Você pode optar por fazer isso caso tenha a intenção de colocar cargas de trabalho intensivas de E/S no VHD do sistema operacional.

O primeiro exemplo demonstra o uso de imagens da Galeria do Azure existente. O segundo exemplo mostra como usar uma imagem de VM personalizada em uma conta de armazenamento Padrão existente.

> [!NOTE]
> Esses exemplos pressupõem que você já tenha criou um VNET regional.

### <a name="create-a-new-vm-with-premium-storage-with-gallery-image"></a>Criar uma nova VM com Armazenamento Premium com Imagem da Galeria

O exemplo a seguir mostra como colocar o VHD do sistema operacional no armazenamento premium e anexar VHDs de Armazenamento Premium. No entanto, você também pode colocar o disco do sistema operacional em uma conta de Armazenamento Padrão e, em seguida, anexar VHDs que residem em uma conta de Armazenamento Premium. Ambos os cenários são demonstrados.

```powershell
$mysubscription = "DansSubscription"
$location = "West Europe"

#Set up subscription
Set-AzureSubscription -SubscriptionName $mysubscription
Select-AzureSubscription -SubscriptionName $mysubscription -Current  
```

#### <a name="step-1-create-a-premium-storage-account"></a>Etapa 1: Criar uma conta de Armazenamento Premium

```powershell
#Create Premium Storage account, note Type
$newxiostorageaccountname = "danspremsams"
New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  
```

#### <a name="step-2-create-a-new-cloud-service"></a>Etapa 2: Criar um novo serviço de nuvem

```powershell
$destcloudsvc = "danNewSvcAms"
New-AzureService $destcloudsvc -Location $location
```

#### <a name="step-3-reserve-a-cloud-service-vip-optional"></a>Etapa 3: Reservar um VIP de serviço de nuvem (opcional)

```powershell
#check exisitng reserved VIP
Get-AzureReservedIP

$reservedVIPName = “sqlcloudVIP”
New-AzureReservedIP –ReservedIPName $reservedVIPName –Label $reservedVIPName –Location $location
```

#### <a name="step-4-create-a-vm-container"></a>Etapa 4: Criar um contêiner de VM

```powershell
#Generate storage keys for later
$xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

##Generate storage acc contexts
$xioContext = New-AzureStorageContext –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary   

#Create container
$containerName = 'vhds'
New-AzureStorageContainer -Name $containerName -Context $xioContext
```

#### <a name="step-5-placing-os-vhd-on-standard-or-premium-storage"></a>Etapa 5: Colocar o VHD do sistema operacional no Armazenamento Standard ou Premium

```powershell
#NOTE: Set up subscription and default storage account which is used to place the OS VHD in

#If you want to place the OS VHD Premium Storage Account
Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $newxiostorageaccountname  

#If you wanted to place the OS VHD Standard Storage Account but attach Premium Storage VHDs then you would run this instead:
$standardstorageaccountname = "danstdams"

Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $standardstorageaccountname
```

#### <a name="step-6-create-vm"></a>Etapa 6: Criar VM

```powershell
#Get list of available SQL Server Images from the Azure Image Gallery.
$galleryImage = Get-AzureVMImage | where-object {$_.ImageName -like "*SQL*2014*Enterprise*"}
$image = $galleryImage.ImageName

#Set up Machine Specific Information
$vmName = "dsDan1"
$vnet = "dansvnetwesteur"
$subnet = "SQL"
$ipaddr = "192.168.0.8"

#Remember to change to DS series VM
$newInstanceSize = "Standard_DS1"

#create new Availability Set
$availabilitySet = "cloudmigAVAMS"

#Machine User Credentials
$userName = "myadmin"
$pass = "mycomplexpwd4*"

#Create VM Config
$vmConfigsl = New-AzureVMConfig -Name $vmName -InstanceSize $newInstanceSize -ImageName $image  -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` -AdminUserName $userName -Password $pass | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

#Add Data and Log Disks to VM Config
#Note the size specified ‘-DiskSizeInGB 1023’, this attaches 2 x P30 Premium Storage Disk Type
#Utilising the Premium Storage enabled Storage account

$vmConfigsl | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 0 -HostCaching "ReadOnly"  -DiskLabel "DataDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-data1.vhd"
$vmConfigsl | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 1 -HostCaching "None"  -DiskLabel "logDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-log1.vhd"

#Create VM
$vmConfigsl  | New-AzureVM –ServiceName $destcloudsvc -VNetName $vnet ## Optional (-ReservedIPName $reservedVIPName)  

#Add RDP Endpoint
$EndpointNameRDPInt = "3389"
Get-AzureVM -ServiceName $destcloudsvc -Name $vmName | Add-AzureEndpoint -Name "EndpointNameRDP" -Protocol "TCP" -PublicPort "53385" -LocalPort $EndpointNameRDPInt  | Update-AzureVM

#Check VHD storage account, these should be in $newxiostorageaccountname
Get-AzureVM -ServiceName $destcloudsvc -Name $vmName | Get-AzureDataDisk
Get-AzureVM -ServiceName $destcloudsvc -Name $vmName |Get-AzureOSDisk
```

### <a name="create-a-new-vm-to-use-premium-storage-with-a-custom-image"></a>Criar uma nova VM para usar o Armazenamento Premium com uma imagem personalizada

Este cenário demonstra onde você tem imagens personalizadas existentes que residem em uma conta de Armazenamento Padrão. Como mencionado, se quiser colocar o VHD do sistema operacional no Armazenamento Premium, você precisará copiar a imagem que existe na conta de Armazenamento Standard e transferi-la para um Armazenamento Premium antes que possa ser usada. Se você tiver uma imagem local, também poderá usar esse método para copiá-la diretamente para a conta de Armazenamento Premium.

#### <a name="step-1-create-storage-account"></a>Etapa 1: Criar conta de armazenamento

```powershell
$mysubscription = "DansSubscription"
$location = "West Europe"

#Create Premium Storage account
$newxiostorageaccountname = "danspremsams"
New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  

#Standard Storage account
$origstorageaccountname = "danstdams"
```

#### <a name="step-2-create-cloud-service"></a>Etapa 2: criar serviço de nuvem

```powershell
$destcloudsvc = "danNewSvcAms"
New-AzureService $destcloudsvc -Location $location
```

#### <a name="step-3-use-existing-image"></a>Etapa 3: Usar a imagem existente

Você pode usar uma imagem existente. Ou você também pode [capturar uma imagem de uma máquina existente](../classic/capture-image-classic.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Observe que o computador cuja imagem é criada não precisa ser um computador DS*. Assim que tiver a imagem, as próximas etapas mostrarão como copiá-la para a conta de Armazenamento Premium com o commandlet **Start-AzureStorageBlobCopy** do PowerShell.

```powershell
#Get storage account keys:
#Standard Storage account
$originalstorage =  Get-AzureStorageKey -StorageAccountName $origstorageaccountname
#Premium Storage account
$xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

#Set up contexts for the storage accounts:
$origContext = New-AzureStorageContext  –StorageAccountName $origstorageaccountname -StorageAccountKey $originalstorage.Primary
$destContext = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary  
```

#### <a name="step-4-copy-blob-between-storage-accounts"></a>Etapa 4: Copiar o Blob entre contas de armazenamento

```powershell
#Get Image VHD
$myImageVHD = "dansoldonorsql2k14-os-2015-04-15.vhd"
$containerName = 'vhds'

#Copy Blob between accounts
$blob = Start-AzureStorageBlobCopy -SrcBlob $myImageVHD -SrcContainer $containerName `
-DestContainer vhds -Destblob "prem-$myImageVHD" `
-Context $origContext -DestContext $destContext  
```

#### <a name="step-5-regularly-check-copy-status"></a>Etapa 5: Verificar regularmente o status da cópia:

```powershell
$blob | Get-AzureStorageBlobCopyState
```

#### <a name="step-6-add-image-disk-to-azure-disk-repository-in-subscription"></a>Etapa 6: Adicionar o disco de imagem ao repositório de disco do Azure na assinatura

```powershell
$imageMediaLocation = $destContext.BlobEndPoint+"/"+$myImageVHD
$newimageName = "prem"+"dansoldonorsql2k14"

Add-AzureVMImage -ImageName $newimageName -MediaLocation $imageMediaLocation
```

> [!NOTE]
> Pode ser que mesmo com os relatórios de status mostrando sucesso, você ainda receba um erro de concessão de disco. Nesse caso, espere cerca de 10 minutos.

#### <a name="step-7--build-the-vm"></a>Etapa 7:  Compilar a VM

Aqui, você está criando a VM com base na sua imagem e anexando dois VHDs de Armazenamento Premium:

```powershell
$newimageName = "prem"+"dansoldonorsql2k14"
#Set up Machine Specific Information
$vmName = "dansolchild"
$vnet = "westeur"
$subnet = "Clients"
$ipaddr = "192.168.0.41"

#This needs to be a new cloud service
$destcloudsvc = "danregsvcamsxio2"

#Use to DS Series VM
$newInstanceSize = "Standard_DS1"

#create new Availability Set
$availabilitySet = "cloudmigAVAMS3"

#Machine User Credentials
$userName = "myadmin"
$pass = "theM)stC0mplexP@ssw0rd!”


#Create VM Config
$vmConfigsl2 = New-AzureVMConfig -Name $vmName -InstanceSize $newInstanceSize -ImageName $newimageName  -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` -AdminUserName $userName -Password $pass | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

$vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 0 -HostCaching "ReadOnly"  -DiskLabel "DataDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-Datadisk-1.vhd"
$vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 1 -HostCaching "None"  -DiskLabel "LogDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-logdisk-1.vhd"



$vmConfigsl2 | New-AzureVM –ServiceName $destcloudsvc -VNetName $vnet
```

## <a name="existing-deployments-that-do-not-use-always-on-availability-groups"></a>Implantações existentes que não usam grupos de disponibilidade AlwaysOn

> [!NOTE]
> Para implantações existentes, primeiro consulte a seção [Pré-requisitos](#prerequisites-for-premium-storage) deste artigo.

Há considerações diferentes para implantações do SQL Server que não usam grupos de disponibilidade AlwaysOn e aquelas que usam. Se não estiver usando AlwaysOn e tiver um SQL Server autônomo existente, você poderá atualizar para o Armazenamento Premium usando um novo serviço de nuvem e conta de armazenamento. Considere as seguintes opções:

* **Criar uma nova VM do SQL Server**. Você pode criar uma nova VM do SQL Server que usa uma conta de Armazenamento Premium, conforme documentado em Novas implantações. Em seguida, faça uma operação de backup e restauração de seus bancos de dados de configuração e usuário do SQL Server. É necessário atualizar o aplicativo para referenciar o novo SQL Server se ele está sendo acessado interna ou externamente. Você precisa copiar todos os objetos 'fora do banco de dados' como se estivesse fazendo uma migração SxS (Lado a Lado) do SQL Server. Isso inclui objetos como logons, certificados e servidores vinculados.
* **Migrar uma VM existente do SQL Server**. Isso exige colocar a VM do SQL Server offline, depois transferi-la para um novo serviço de nuvem, o que inclui copiar todos os seus VHDs anexados para a conta de Armazenamento Premium. Quando a VM é colocada online, o aplicativo faz referência ao nome de host do servidor como antes. Lembre-se de que o tamanho do disco existente afeta as características de desempenho. Por exemplo, um disco de 400 GB é arredondado para um P20. Se você souber que não vai precisar do desempenho desse disco, poderá recriar a VM coo uma VM da série DS, e anexar VHDs de Armazenamento Premium com a especificação de tamanho/desempenho que quiser. Em seguida, você poderá desanexar e anexar novamente os arquivos de banco de dados SQL.

> [!NOTE]
> Ao copiar os discos VHD, não se esqueça de considerar o tamanho, pois o tipo de Disco de Armazenamento Premium em que eles vão se enquadrar depende do tamanho e isso determina a especificação de desempenho de disco. O Azure arredonda para o tamanho de disco mais próximo e, portanto, se você tiver um disco de 400 GB, ele será arredondado para um P20. Dependendo dos requisitos de E/S existentes do VHD do sistema operacional, talvez não seja necessário migrá-lo para uma conta de Armazenamento Premium.

Se o SQL Server for acessado externamente, o VIP de serviço de nuvem será alterado. Você também tem que atualizar pontos de extremidade, ACLs e configurações de DNS.

## <a name="existing-deployments-that-use-always-on-availability-groups"></a>Implantações existentes que usam grupos de disponibilidade AlwaysOn

> [!NOTE]
> Para implantações existentes, primeiro consulte a seção [Pré-requisitos](#prerequisites-for-premium-storage) deste artigo.

No início desta seção, vamos examinar como o AlwaysOn interage com a Rede do Azure. Em seguida, vamos dividir as migrações em dois cenários: migrações em que algum tempo de inatividade pode ser tolerado e migrações em que você deve obter o mínimo de tempo de inatividade.

Os grupos de disponibilidade AlwaysOn locais do SQL Server usam um Ouvinte local que registra um nome DNS virtual junto com um endereço IP que é compartilhado entre um ou mais SQL Servers. Quando se conectam, os clientes são roteadas por meio do IP do ouvinte para o SQL Server Primário. Esse é o servidor que tem o recurso IP AlwaysOn no momento.

![DeploymentsUseAlways On][6]

No Microsoft Azure, você pode ter apenas um endereço IP atribuído a uma NIC na máquina virtual, portanto, para atingir a mesma camada de abstração do local, o Azure utiliza o endereço IP que é atribuído aos balanceadores de carga internos ou externos (ILB/ELB). O recurso IP que é compartilhado entre os servidores é definido como o mesmo IP de ILB/ELB. Ele é publicado no DNS e o tráfego do cliente é transmitido por meio de ILB/ELB para a réplica do SQL Server Primário. O ILB/ELB sabe qual SQL Server é o primário, pois ele usa testes para investigar o recurso IP AlwaysOn. No exemplo anterior, ele investiga cada nó com um ponto de extremidade referenciado pelo ELB/ILB, aquele que responder será o SQL Server Primário.

> [!NOTE]
> O ILB e o ELB são atribuídos a um serviço de nuvem do Azure específico, portanto, se ocorrer alguma migração de nuvem no Azure provavelmente isso significa que o IP do balanceador de carga será alterado.
>
>

### <a name="migrating-always-on-deployments-that-can-allow-some-downtime"></a>Migrando implantações AlwaysOn que podem permitir algum tempo de inatividade

Existem duas estratégias para migrar as implantações AlwaysOn que permitem algum tempo de inatividade:

1. **Adicionar mais réplicas secundárias a um cluster AlwaysOn existente**
2. **Migrar para um novo cluster AlwaysOn**

#### <a name="1-add-more-secondary-replicas-to-an-existing-always-on-cluster"></a>1. Adicionar mais réplicas secundárias a um cluster AlwaysOn existente

Uma estratégia é adicionar mais réplicas secundárias ao grupo de disponibilidade AlwaysOn. Você precisa adicioná-las em um novo serviço de nuvem e atualizar o ouvinte com o novo IP do balanceador de carga.

##### <a name="points-of-downtime"></a>Pontos de tempo de inatividade:

* Validação de cluster.
* Testando failovers AlwaysOn para secundárias novas.

Se você estiver usando Pools de Armazenamento do Windows na VM para obter uma taxa de transferência de E/S mais alta, eles serão colocados offline durante uma Validação de Cluster Completa. O teste de validação é necessário quando você adiciona nós ao cluster. O tempo necessário para executar o teste pode variar, de modo que você deve testar isso em seu ambiente de teste representativo para obter um tempo aproximado para essa operação.

Você deve provisionar o tempo em que é possível executar o failover manual e testes de caos nos nós adicionados recentemente para assegurar funções de Alta Disponibilidade AlwaysOn conforme o esperado.

![DeploymentUseAlways On2][7]

> [!NOTE]
> Você deve interromper todas as instâncias do SQL Server em que os Pools de Armazenamento forem usados antes de executar a Validação.
>
> ##### <a name="high-level-steps"></a>Etapas de alto nível
>

1. Crie dois novos SQL Servers no novo serviço de nuvem com Armazenamento Premium anexado.
2. Copie sobre backups e restauração completos com **NORECOVERY**.
3. Copie sobre objetos dependentes de ‘banco de dados fora do usuário’, como logons etc.
4. Crie um novo ILB (balanceador de carga interno) ou use um ELB (balanceador de carga externo) e configure pontos de extremidade balanceados de carga em ambos os nós novos.

   > [!NOTE]
   > Verifique se todos os nós têm a configuração do ponto de extremidade correta antes de continuar
   >
   >
5. Interrompa o acesso de usuário/aplicativo ao SQL Server (se você estiver usando pools de armazenamento).
6. Interrompa serviços de mecanismo do SQL Server em todos os nós (se você estiver usando pools de armazenamento).
7. Adicione novos nós ao cluster e execute a validação completa.
8. Depois que a validação for bem-sucedida, inicie todos os Serviços do SQL Server.
9. Faça backup dos logs de transações e restaure os bancos de dados do usuário.
10. Adicione novos nós ao Grupo de Disponibilidade AlwaysOn e coloque a replicação em **Síncrono**.
11. Adicione o recurso de endereço IP do novo ILB/ELB do serviço de nuvem por meio do PowerShell para AlwaysOn com base no exemplo de vários sites apresentado no [Apêndice](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage). No cluster do Windows, defina os **Possíveis proprietários** do recurso **Endereço IP** para os novos nós. Consulte a seção "Adicionando o recurso de endereço IP na mesma sub-rede" do [Apêndice](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).
12. Execute o failover para um dos novos nós.
13. Transforme os novos nós em Parceiros de Failover Automático e teste os failovers.
14. Remova os nós originais do grupo de disponibilidade.

##### <a name="advantages"></a>Vantagens

* Novos SQL Servers podem ser testados (SQL Server e aplicativo) antes de serem adicionados ao AlwaysOn.
* Você pode alterar o tamanho da VM e personalizar o armazenamento de acordo com seus requisitos exatos. No entanto, seria vantajoso manter todos os caminhos de arquivo SQL iguais.
* Você pode controlar quando a transferência dos backups de banco de dados para as réplicas secundárias é iniciada. Isso é diferente de usar o commandlet do Azure **Start-AzureStorageBlobCopy** para copiar VHDs, pois essa é uma cópia assíncrona.

##### <a name="disadvantages"></a>Desvantagens

* Durante o uso de Pools de Armazenamento do Windows, há um tempo de inatividade de cluster na Validação de Cluster Completa para os novos nós adicionais.
* Dependendo da versão do SQL Server e do número existente de réplicas secundárias, você não poderá adicionar mais réplicas secundárias sem remover as existentes.
* O tempo de transferência de dados SQL pode ser muito longo durante a configuração de réplicas secundárias.
* Há custos adicionais durante a migração quando há novas máquinas em execução em paralelo.

#### <a name="2-migrate-to-a-new-always-on-cluster"></a>2. Migrar para um novo cluster AlwaysOn

Outra estratégia é criar um novo cluster AlwaysOn com nós totalmente novos no novo serviço de nuvem e redirecionar os clientes para usá-lo.

##### <a name="points-of-downtime"></a>Pontos de tempo de inatividade

Há um tempo de inatividade quando você transfere aplicativos e usuários para o novo ouvinte AlwaysOn. O tempo de inatividade depende do seguinte:

* Tempo necessário para restaurar backups de log de transações final para bancos de dados em novos servidores.
* Tempo necessário para atualizar os aplicativos cliente para usar o novo ouvinte AlwaysOn.

##### <a name="advantages"></a>Vantagens

* Você pode testar o ambiente de produção real, o SQL Server e as alterações de compilação do sistema operacional.
* Você tem a opção de personalizar o armazenamento e reduzir o tamanho da VM. Isso pode resultar na redução de custos.
* Você pode atualizar sua compilação ou versão do SQL Server durante esse processo. Você também pode atualizar o sistema operacional.
* O cluster AlwaysOn anterior pode atuar como um destino de reversão sólido.

##### <a name="disadvantages"></a>Desvantagens

* Você precisará alterar o nome DNS do ouvinte se quiser que os dois clusters AlwaysOn sejam executados simultaneamente. Isso adiciona sobrecarga administrativa durante a migração, pois as cadeias de caracteres de aplicativo cliente devem refletir o novo nome do ouvinte.
* Você deve implementar um mecanismo de sincronização entre os dois ambientes para mantê-los o mais próximo possível para minimizar os requisitos de sincronização final antes da migração.
* Há um custo adicional durante a migração com o novo ambiente em execução.

### <a name="migrating-always-on-deployments-for-minimal-downtime"></a>Migrando implantações AlwaysOn para tempo de inatividade mínimo

Existem duas estratégias para migrar implantações AlwaysOn para o tempo de inatividade mínimo:

1. **Utilizar uma réplica secundária existente: Site único**
2. **Utilizar réplicas secundárias existentes: Vários sites**

#### <a name="1-utilize-an-existing-secondary-single-site"></a>1. Utilizar uma réplica secundária existente: Site único

Uma estratégia para tempo de inatividade mínimo é usar uma réplica secundária de nuvem existente e removê-la do serviço de nuvem atual. Em seguida, copie os VHDs para a nova conta de Armazenamento Premium e crie a VM no novo serviço de nuvem. Em seguida, atualize o ouvinte no clustering e failover.

##### <a name="points-of-downtime"></a>Pontos de tempo de inatividade

* Há um tempo de inatividade quando você atualiza o nó final com o ponto de extremidade de carga balanceada.
* A reconexão do cliente pode ser atrasada dependendo da configuração do cliente/DNS.
* Haverá um tempo de inatividade adicional se você optar por colocar o grupo de cluster AlwaysOn offline para trocar os endereços IP. Você pode evitar isso usando uma dependência OR e possíveis proprietários para o recurso de endereço IP adicionado. Consulte a seção "Adicionando o recurso de endereço IP na mesma sub-rede" do [Apêndice](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).

> [!NOTE]
> Quando quiser que o nó adicional participe como um parceiro de failover AlwaysOn, você precisará adicionar um ponto de extremidade do Azure com uma referência a um conjunto de balanceamento de carga. Quando você executa o comando **Add-AzureEndpoint** para fazer isso, as conexões atuais permanecem abertas, mas as novas conexões com o ouvinte não poderão ser estabelecidas até o balanceador de carga ser atualizado. Em testes, observou-se que isso durava de 90 a 120 segundos, mas é necessário conferir.

##### <a name="advantages"></a>Vantagens

* Sem custo extra incorrido durante a migração.
* Uma migração um-para-um.
* Redução da complexidade.
* Permite maior IOPS de SKUs de Armazenamento Premium. Quando os discos são desanexados da VM e copiados para o novo serviço de nuvem, uma ferramenta de terceiros pode ser usada para aumentar o tamanho do VHD, que oferece taxas de transferência mais altas. Para tamanhos de VHD maiores, consulte este [fórum de discussão](https://social.msdn.microsoft.com/Forums/azure/4a9bcc9e-e5bf-4125-9994-7c154c9b0d52/resizing-azure-data-disk?forum=WAVirtualMachinesforWindows).

##### <a name="disadvantages"></a>Desvantagens

* Há uma perda temporária de alta disponibilidade e recuperação de desastre durante a migração.
* Como essa é uma migração 1:1, você tem que usar um tamanho mínimo de VM que dá suporte a seu número de VHDs e, portanto, não poderá diminuir suas VMs.
* Este cenário usa o commandlet Azure **Start-AzureStorageBlobCopy** , que é assíncrono. Não há nenhum SLA na conclusão da cópia. O tempo das cópias varia, pois embora isso dependa da espera na fila, também depende da quantidade de dados a ser transferida. O tempo de cópia aumentará se a transferência for para outro data center do Azure que com suporte ao Armazenamento Premium em outra região. Se você tiver apenas dois nós, considere uma possível redução, caso a cópia demore mais do que no teste. Isso pode incluir as ideias a seguir.
  * Adicione um terceiro nó temporário do SQL Server para alta disponibilidade antes da migração com tempo de inatividade estabelecido.
  * Execute a migração fora da manutenção programada do Azure.
  * Verifique se você configurou corretamente o quorum do cluster.  

##### <a name="high-level-steps"></a>Etapas de alto nível

Este documento não demonstra um exemplo completo de ponta a ponta, no entanto, o [Apêndice](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) fornece detalhes que podem ser usados para isso.

![MinimalDowntime][8]

* Obtenha a configuração de disco e remova o nó (não exclua VHDs anexados).
* Crie uma conta de Armazenamento Premium e copie VHDs da conta de Armazenamento Padrão
* Crie um novo serviço de nuvem e reimplante a VM SQL2 no serviço de nuvem. Crie a VM usando o VHD do sistema operacional original copiado e anexe VHDs copiados.
* Configure o ILB / ELB e adicione pontos de extremidade.
* Siga um destes procedimentos para atualizar o ouvinte:
  * Coloque o grupo AlwaysOn offline e atualize o ouvinte AlwaysOn com o novo endereço IP ILB/ELB.
  * Ou adicione o recurso de endereço IP do novo ILB/ELB do serviço de nuvem por meio do PowerShell para clustering do Windows. Em seguida, defina os possíveis proprietários do recurso de endereço IP para o nó migrado, o SQL2 e defina isso como dependência OR no nome da rede. Consulte a seção "Adicionando o recurso de endereço IP na mesma sub-rede" do [Apêndice](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).
* Verifique a configuração/propagação DNS para os clientes.
* Migre a VM do SQL1 e siga as etapas 2 a 4.
* Se você seguir as etapas 5ii, adicione SQL1 como um possível proprietário para o recurso de endereço IP adicionado
* Failovers de teste.

#### <a name="2-utilize-existing-secondary-replicas-multi-site"></a>2. Utilizar réplicas secundárias existentes: Multissite

Se tiver nós em mais de um DC (data center) do Azure ou se tiver um ambiente híbrido, você poderá usar uma configuração AlwaysOn nesse ambiente para minimizar o tempo de inatividade.

A abordagem é alterar a sincronização AlwaysOn para Síncrona para o DC do Azure local ou secundário e executar o failover para esse SQL Server. Em seguida, copie os VHDs em uma conta de Armazenamento Premium e reimplante a máquina em um novo serviço de nuvem. Atualize o ouvinte e, em seguida, execute o failback.

##### <a name="points-of-downtime"></a>Pontos de tempo de inatividade

O tempo de inatividade consiste no tempo para executar o failover para o controlador de domínio alternativo e voltar. Também depende da sua configuração cliente/DNS e a reconexão do cliente poder ser atrasada.
Considere o seguinte exemplo de uma configuração AlwaysOn híbrida:

![MultiSite1][9]

##### <a name="advantages"></a>Vantagens

* Você pode utilizar a infraestrutura existente.
* Você tem a opção de atualizar previamente o armazenamento do Azure no controlador de domínio do Azure DR pela primeira vez.
* O armazenamento do controlador de domínio do Azure DR pode ser reconfigurado.
* Há um mínimo de dois failovers durante a migração, excluindo failovers de teste.
* Você não precisa mover dados do SQL Server com backup e restauração.

##### <a name="disadvantages"></a>Desvantagens

* Dependendo do acesso de cliente para o SQL Server, poderá haver latência aumentada quando o SQL Server estiver em execução em um controlador de domínio alternativo para o aplicativo.
* O tempo de cópia de VHDs de armazenamento Premium pode ser longo. Isso poderá afetar sua decisão de manter o nó no grupo de disponibilidade. Considere isso para ocasiões em que a execução de cargas de trabalho com uso intensivo de log durante a migração for necessária, pois o nó primário tem que manter as transações não replicadas em seu log de transações. Portanto, isso pode crescer significativamente.
* Este cenário usa o commandlet Azure **Start-AzureStorageBlobCopy** , que é assíncrono. Não há nenhum SLA a concluir. O tempo das cópias varia, pois embora isso dependa da espera na fila, também depende da quantidade de dados a ser transferida. Portanto, você tem apenas um nó no segundo data center. Adote medidas de redução, caso a cópia demore mais do que no teste. Essas etapas de mitigação incluem as seguintes ideias:
  * Adicione um segundo nó temporário do SQL para alta disponibilidade antes da migração com tempo de inatividade estabelecido.
  * Execute a migração fora da manutenção programada do Azure.
  * Verifique se você configurou corretamente o quorum do cluster.

Esse cenário pressupõe que você documentou sua instalação e sabe como o armazenamento é mapeado para fazer alterações para as configurações de cache de disco ideais.

##### <a name="high-level-steps"></a>Etapas de alto nível

![Multisite2][10]

* Torne o controlador de domínio do Azure local / alternativo o SQL Server Primário e torne-o o outro AFP (Parceiro de Failover Automático).
* Obtenha as informações de configuração de disco do SQL2 e remova o nó (não exclua VHDs anexados).
* Crie uma conta de Armazenamento Premium e copie VHDs da conta de Armazenamento Padrão.
* Crie um novo serviço de nuvem e crie a VM SQL2 com seus discos de Armazenamento Premium anexados.
* Configure o ILB / ELB e adicione pontos de extremidade.
* Atualize o ouvinte AlwaysOn com novos endereços IP ILB/ELB e failover de teste.
* Verifique a configuração DNS.
* Altere o AFP para o SQL2, migre o SQL1 e execute as etapas 2 a 5.
* Failovers de teste.
* Alternar o AFP de volta para SQL1 e SQL2

## <a name="appendix-migrating-a-multisite-always-on-cluster-to-premium-storage"></a>Apêndice: Migrando um cluster Always On de vários sites para o Armazenamento Premium

O restante deste artigo fornece um exemplo detalhado de conversão de um cluster AlwaysOn de vários sites para o Armazenamento Premium. Ele também converte o Ouvinte de usar um ELB (balanceador de carga externo) em um ILB (balanceador de carga interno).

### <a name="environment"></a>Ambiente

* Windows 2k12 / SQL 2k12
* 1 arquivo de banco de dados no SP
* 2 x pools de armazenamento por nó

![Appendix1][11]

### <a name="vm"></a>VM:

Neste exemplo, vamos demonstrar a movimentação de um ELB para um ILB. O ELB estava disponível antes do ILB e, portanto, esse procedimento mostra como alternar para o ILB durante a migração.

![Appendix2][12]

### <a name="pre-steps-connect-to-subscription"></a>Pré-etapas: Conectar-se à assinatura

```powershell
Add-AzureAccount

#Set up subscription
Get-AzureSubscription
```

#### <a name="step-1-create-new-storage-account-and-cloud-service"></a>Etapa 1: Criar a nova conta de armazenamento e o serviço de nuvem

```powershell
$mysubscription = "DansSubscription"
$location = "West Europe"

#Storage accounts
#current storage account where the vm to migrate resides
$origstorageaccountname = "danstdams"

#Create Premium Storage account
$newxiostorageaccountname = "danspremsams"
New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  

#Generate storage keys for later
$originalstorage =  Get-AzureStorageKey -StorageAccountName $origstorageaccountname
$xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

#Generate storage acc contexts
$origContext = New-AzureStorageContext  –StorageAccountName $origstorageaccountname -StorageAccountKey $originalstorage.Primary
$xioContext = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary  

#Set up subscription and default storage account
Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $origstorageaccountname
Select-AzureSubscription -SubscriptionName $mysubscription -Current

#CREATE NEW CLOUD SVC
$vnet = "dansvnetwesteur"

##Existing cloud service
$sourceSvc="dansolSrcAms"

##Create new cloud service
$destcloudsvc = "danNewSvcAms"
New-AzureService $destcloudsvc -Location $location
```

#### <a name="step-2-increase-the-permitted-failures-on-resources-optional"></a>Etapa 2: Aumentar as falhas permitidas nos recursos \<opcional >

Em determinados recursos que pertencem ao seu grupo de disponibilidade AlwaysOn há limites no número de falhas que podem ocorrer em um período, no qual o serviço de cluster tenta reiniciar o grupo de recursos. É recomendável aumentar isso, durante a execução deste procedimento, pois se não você não executar manualmente o failover e disparar failovers desligando máquinas, poderá chegar perto desse limite.

Aconselhamos a dobrar a concessão de falha. Para fazer isso no Gerenciador de Cluster de Failover, acesse as Propriedades do grupo de recursos AlwaysOn:

![Appendix3][13]

Altere o Máximo de Falhas para 6.

#### <a name="step-3-addition-ip-address-resource-for-cluster-group-optional"></a>Etapa 3: Recurso de endereço IP de adição para grupo de clusters \<opcional >

Se você tiver apenas um endereço IP para o grupo de clusters e ele estiver alinhado à sub-rede de nuvem, tome cuidado: se você acidentalmente colocar offline todos os nós de cluster na nuvem nessa rede, o recurso de IP de cluster e o nome de rede do cluster não poderão ficar online. Nessa situação, ela impede que as atualizações para outros recursos de cluster.

#### <a name="step-4-dns-configuration"></a>Etapa 4: Configuração de DNS

A implementação de uma transição suave depende de como o DNS está sendo utilizado e atualizado.
Quando o AlwaysOn está instalado, ele cria um grupo de recursos de cluster do Windows. Se você abrir o Gerenciador de Cluster de Failover, verá que, no mínimo, ele tem três recursos; os dois aos que o documento se refere são:

* VNN (Nome da Rede Virtual): o nome DNS ao qual o cliente se conecta quando quer se conectar aos SQL Servers via AlwaysOn.
* Recurso de endereço IP – o endereço IP associado à VNN. Você pode ter mais de um e, em uma configuração de vários sites, tem um endereço IP por site/sub-rede.

Ao se conectar ao SQL Server, o driver SQL Server Client recupera os registros DNS associados ao ouvinte e tenta se conectar a cada endereço IP associado ao Always On. Em seguida, discutiremos alguns fatores que podem influenciar a isso.

O número de registros DNS simultâneos associados ao nome do ouvinte depende não só do número de endereços IP associados, mas da configuração ‘RegisterAllIpProviders' no Clustering de Failover para o recurso de VNN AlwaysON.

Quando você implanta o AlwaysOn no Azure, há diferentes etapas para criar os Endereços IP e o Ouvinte. É necessário configurar manualmente ‘RegisterAllIpProviders’ como 1 e isso é diferente para uma implantação local AlwaysOn em que já está definido como 1.

Se 'RegisterAllIpProviders' for 0, você verá somente um registro DNS no DNS associado com o ouvinte:

![Appendix4][14]

Se 'RegisterAllIpProviders' for 1:

![Appendix5][15]

O código a seguir descarta as configurações de VNN e as configura para você. Para que a alteração entre em vigor, você precisa colocar o VNN offline e online novamente. Isso coloca o Ouvinte offline, causando interrupção de conectividade do cliente.

```powershell
##Always On Listener Name
$ListenerName = "Mylistener"
##Get AlwaysOn Network Name Settings
Get-ClusterResource $ListenerName| Get-ClusterParameter
##Set RegisterAllProvidersIP
Get-ClusterResource $ListenerName| Set-ClusterParameter RegisterAllProvidersIP  1
```

Em uma etapa de migração posterior, você precisa atualizar o ouvinte Always On com um endereço IP atualizado que faz referência a um balanceador de carga. Isso envolve uma remoção e uma adição do recurso de endereço IP. Após a atualização IP, você precisa garantir que o novo endereço IP tenha sido atualizado na zona DNS e que os clientes estejam atualizando seu cache DNS local.

Se os clientes residirem em um segmento de rede diferente e referenciarem um servidor DNS diferente, você terá que considerar o que acontece com a transferência de zona DNS durante a migração, pois o tempo de reconexão de aplicativo fica restrito por pelo menos o tempo de transferência de zona de novos endereços IP para o ouvinte. Se tiver pouco tempo aqui, você deverá discutir e testar forçar uma transferência de zona incremental com as equipes do Windows e definir o registro de host DNS para uma TTL (tempo de vida) menor, para atualização dos clientes. Para obter mais informações, consulte [Transferências de Zona Incrementais](https://technet.microsoft.com/library/cc958973.aspx) e [Start-DnsServerZoneTransfer](https://docs.microsoft.com/powershell/module/dnsserver/start-dnsserverzonetransfer).

Por padrão, a TTL do registro DNS que está associada ao ouvinte no AlwaysOn no Azure é de 1200 segundos. Talvez você queira reduzi-lo se houver restrições de tempo durante sua migração para assegurar que os clientes atualizem seu DNS com o endereço IP atualizado do ouvinte. Você pode ver e modificar a configuração despejando a configuração de VNN:

```powershell
$AGName = "myProductionAG"
$ListenerName = "Mylistener"
#Look at HostRecordTTL
Get-ClusterResource $ListenerName| Get-ClusterParameter

#Set HostRecordTTL Examples
Get-ClusterResource $ListenerName| Set-ClusterParameter -Name "HostRecordTTL" 120
```

> [!NOTE]
> Observe que, quanto menor for 'HostRecordTTL', maior será a quantidade de tráfego DNS.

##### <a name="client-application-settings"></a>Configurações de aplicativo cliente

Se seu aplicativo de cliente SQL dá suporte a .NET 4.5 SQLClient, você pode usar ' MULTISUBNETFAILOVER = TRUE' palavra-chave. Essa palavra-chave deve ser aplicada porque permite uma conexão mais rápida para o grupo de disponibilidade SQL Always On durante o failover. Isso enumera todos os endereços IP associados ao ouvinte AlwaysOn em paralelo e executa uma velocidade de repetição de conexão TCP mais agressiva durante um failover.

Para saber mais sobre as configurações anteriores, confira [Palavra-chave MultiSubnetFailover e recursos associados](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover). Consulte também [Suporte do SqlClient para a Alta Disponibilidade e a Recuperação de Desastre](https://msdn.microsoft.com/library/hh205662\(v=vs.110\).aspx).

#### <a name="step-5-cluster-quorum-settings"></a>Etapa 5: Configurações de quórum do cluster

Como vai retirar pelo menos um SQL Server de cada vez, você deverá modificar a configuração de quórum de cluster. Se estiver usando FSW (File Share Witness) com dois nós, você deverá definir o quorum para permitir a maioria dos nós e utilizar votação dinâmica, permitindo que um único nó fique de pé.

```powershell
Set-ClusterQuorum -NodeMajority  
```

Para saber mais sobre como gerenciar e configurar o quórum do cluster, confira [Configurar e gerenciar o quorum em um cluster de failover do Windows Server 2012](https://technet.microsoft.com/library/jj612870.aspx).

#### <a name="step-6-extract-existing-endpoints-and-acls"></a>Etapa 6: Extrair ACLs e pontos de extremidade existentes

```powershell
#GET Endpoint info
Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureEndpoint
#GET ACL Rules for Each EP, this example is for the Always On Endpoint
Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureAclConfig -EndpointName "myAOEndPoint-LB"  
```

Salve esse texto em um arquivo.

#### <a name="step-7-change-failover-partners-and-replication-modes"></a>Etapa 7: Alterar parceiros de failover e modos de replicação

Se tiver mais de dois SQL Servers, você deverá alterar o failover de outra secundário em outro DC ou local para 'Síncrono' e torná-lo um AFP (Parceiro de Failover Automático), de modo a manter a alta disponibilidade durante as alterações. Você pode fazer isso por meio do TSQL ou modificar por SSMS:

![Appendix6][16]

#### <a name="step-8-remove-secondary-vm-from-cloud-service"></a>Etapa 8: Remover a VM secundária do serviço de nuvem

Você deve planejar a migração de um nó secundário da nuvem primeiro. Se o nó for atualmente primário, inicie um failover manual.

```powershell
$vmNameToMigrate="dansqlams2"

#Check machine status
Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

#Shutdown secondary VM
Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | stop-AzureVM


#Extract disk configuration

##Building Existing Data Disk Configuration
$file = "C:\Azure Storage Testing\mydiskconfig_$vmNameToMigrate.csv"
$datadisks = @(Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureDataDisk )
Add-Content $file “lun, vhdname, hostcaching, disklabel, diskName”
foreach ($disk in $datadisks)
{
    $vhdname = $disk.MediaLink.AbsolutePath -creplace  "/vhds/"
    $disk.Lun, , $disk.HostCaching, $vhdname, $disk.DiskLabel,$disks.DiskName
    # Write-Host "copying disk $disk"
    $adddisk = "{0},{1},{2},{3},{4}" -f $disk.Lun,$vhdname, $disk.HostCaching, $disk.DiskLabel, $disk.DiskName
    $adddisk | add-content -path $file
}

#Get OS Disk
$osdisks = Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureOSDisk ## | select -ExpandProperty MediaLink
$osvhdname = $osdisks.MediaLink.AbsolutePath -creplace  "/vhds/"
$osdisks.OS, $osdisks.HostCaching, $osvhdname, $osdisks.DiskLabel, $osdisks.DiskName
$addosdisk = "{0},{1},{2},{3},{4}" -f $osdisks.OS,$osvhdname, $osdisks.HostCaching, $osdisks.Disklabel , $osdisks.DiskName
$addosdisk | add-content -path $file

#Import disk config
$diskobjects  = Import-CSV $file

#Check disk config, make sure below returns the disks associated with the VM
$diskobjects

#Identify OS Disk
$osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
$osdiskforbuild = $osdiskimport.diskName

#Check machibe is off
Get-AzureVM -ServiceName $sourceSvc -Name  $vmNameToMigrate

#Drop machine and rebuild to new cls
Remove-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate
```

#### <a name="step-9-change-disk-caching-settings-in-csv-file-and-save"></a>Etapa 9: Alterar as configurações de caching de disco no arquivo CSV e salvar

Para volumes de dados, isso deve ser definido como READONLY.

Para volumes TLOG, isso deve ser definido como NONE.

![Appendix7][17]

#### <a name="step-10-copy-vhds"></a>Etapa 10: Copiar VHDs

```powershell
#Ensure you have created the container for these:
$containerName = 'vhds'

#Create container
New-AzureStorageContainer -Name $containerName -Context $xioContext

####DISK COPYING####
#Get disks from csv, get settings for each VHDs and copy to Premium Storage accoun
ForEach ($disk in $diskobjects)
{
   $lun = $disk.Lun
   $vhdname = $disk.vhdname
   $cacheoption = $disk.HostCaching
   $disklabel = $disk.DiskLabel
   $diskName = $disk.DiskName
   Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname has cache setting : $cacheoption"

   #Start async copy
   Start-AzureStorageBlobCopy -srcUri "https://$origstorageaccountname.blob.core.windows.net/vhds/$vhdname" `
    -SrcContext $origContext `
    -DestContainer $containerName `
    -DestBlob $vhdname `
    -DestContext $xioContext
}
```


Você pode verificar o status da cópia dos VHDs à conta de Armazenamento Premium:

```powershell
ForEach ($disk in $diskobjects)
{
   $lun = $disk.Lun
   $vhdname = $disk.vhdname
   $cacheoption = $disk.HostCaching
   $disklabel = $disk.DiskLabel
   $diskName = $disk.DiskName

   $copystate = Get-AzureStorageBlobCopyState -Blob $vhdname -Container $containerName -Context $xioContext
   Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname, STATUS = " $copystate.Status
}
```

![Appendix8][18]

Aguarde até que todos esses itens sejam registrados como êxito.

Para obter informações para blobs individuais:

```powershell
Get-AzureStorageBlobCopyState -Blob "blobname.vhd" -Container $containerName -Context $xioContext
```

#### <a name="step-11-register-os-disk"></a>Etapa 11: Registrar o disco do sistema operacional

```powershell
#Change storage account
Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountname
Select-AzureSubscription -SubscriptionName $mysubscription -Current

#Register OS disk
$osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
$osvhd = $osdiskimport.vhdname
$osdiskforbuild = $osdiskimport.diskName

#Registering OS disk, but as XIO disk
$xioDiskName = $osdiskforbuild + "xio"
Add-AzureDisk -DiskName $xioDiskName -MediaLocation  "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$osvhd"  -Label "BootDisk" -OS "Windows"
```

#### <a name="step-12-import-secondary-into-new-cloud-service"></a>Etapa 12: Importar a réplica secundária para o novo serviço de nuvem

O código a seguir também usa a opção adicional em que é possível importar a máquina e usar o VIP preservável.

```powershell
#Build VM Config
$ipaddr = "192.168.0.5"
#Remember to change to XIO
$newInstanceSize = "Standard_DS13"
$subnet = "SQL"

#Create new Availability Set
$availabilitySet = "cloudmigAVAMS"

#build machine config into object
$vmConfig = New-AzureVMConfig -Name $vmNameToMigrate -InstanceSize $newInstanceSize -DiskName $xioDiskName -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

#Reload disk config
$diskobjects  = Import-CSV $file
$datadiskimport = $diskobjects | where {$_.lun -ne "Windows"}

ForEach ( $attachdatadisk in $datadiskimport)
{
    $label = $attachdatadisk.disklabel
    $lunNo = $attachdatadisk.lun
    $hostcach = $attachdatadisk.hostcaching
    $datadiskforbuild = $attachdatadisk.diskName
    $vhdname = $attachdatadisk.vhdname

    ###Attaching disks to a VM during a deploy to a new cloud service and new storage account is different from just attaching VHDs to just a redeploy in a new cloud service
    $vmConfig | Add-AzureDataDisk -ImportFrom -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vhdname" -LUN $lunNo -HostCaching $hostcach -DiskLabel $label

}

#Create VM
$vmConfig  | New-AzureVM –ServiceName $destcloudsvc –Location $location -VNetName $vnet ## Optional (-ReservedIPName $reservedVIPName)
```

#### <a name="step-13-create-ilb-on-new-cloud-svc-add-load-balanced-endpoints-and-acls"></a>Etapa 13: Criar ILB no novo Svc de nuvem, adicionar pontos de extremidade de carga balanceada e ACLs

```powershell
#Check for existing ILB
GET-AzureInternalLoadBalancer -ServiceName $destcloudsvc

$ilb="sqlIntIlbDest"
$subnet = "SQL"
$IP="192.168.0.25"
Add-AzureInternalLoadBalancer -ServiceName $destcloudsvc -InternalLoadBalancerName $ilb –SubnetName $subnet –StaticVNetIPAddress $IP

#Endpoints
$epname="sqlIntEP"
$prot="tcp"
$locport=1433
$pubport=1433
Get-AzureVM –ServiceName $destcloudsvc –Name $vmNameToMigrate  | Add-AzureEndpoint -Name $epname -Protocol $prot -LocalPort $locport -PublicPort $pubport -ProbePort 59999 -ProbeIntervalInSeconds 5 -ProbeTimeoutInSeconds 11  -ProbeProtocol "TCP" -InternalLoadBalancerName $ilb -LBSetName $ilb -DirectServerReturn $true | Update-AzureVM

#SET Azure ACLs or Network Security Groups & Windows FWs

#https://msdn.microsoft.com/library/azure/dn495192.aspx

####WAIT FOR FULL AlwaysOn RESYNCRONISATION!!!!!!!!!#####
```

#### <a name="step-14-update-always-on"></a>Etapa 14: Atualizar Always On

```powershell
#Code to be executed on a Cluster Node
$ClusterNetworkNameAmsterdam = "Cluster Network 2" # the azure cluster subnet network name
$newCloudServiceIPAmsterdam = "192.168.0.25" # IP address of your cloud service

$AGName = "myProductionAG"
$ListenerName = "Mylistener"


Add-ClusterResource "IP Address $newCloudServiceIPAmsterdam" -ResourceType "IP Address" -Group $AGName -ErrorAction Stop |  Set-ClusterParameter -Multiple @{"Address"="$newCloudServiceIPAmsterdam";"ProbePort"="59999";SubnetMask="255.255.255.255";"Network"=$ClusterNetworkNameAmsterdam;"OverrideAddressMatch"=1;"EnableDhcp"=0} -ErrorAction Stop

#set dependency and NETBIOS, then remove old IP address

#set NETBIOS, then remove old IP address
Get-ClusterGroup $AGName | Get-ClusterResource -Name "IP Address $newCloudServiceIPAmsterdam" | Set-ClusterParameter -Name EnableNetBIOS -Value 0

#set dependency to Listener (OR Dependency) and delete previous IP Address resource that references:

#Make sure no static records in DNS
```

![Appendix9][19]

Agora, remova o endereço IP do serviço de nuvem antigo.

![Appendix10][20]

#### <a name="step-15-dns-update-check"></a>Etapa 15: Verificar a atualização do DNS

Agora você deve verificar os servidores DNS em suas redes cliente do SQL Server e assegurar que o clustering adicionou o registro do host extra ao endereço IP adicionado. Se os servidores DNS não foram atualizados, considere forçar uma transferência de zona DNS e garanta que os clientes nessa sub-rede podem ser resolvidos para os dois endereços IP AlwaysOn. Isso ocorre para que você não precise aguardar a replicação automática de DNS.

#### <a name="step-16-reconfigure-always-on"></a>Etapa 16: Reconfigurar o Always On

Aqui, você aguarda o secundário para o qual o nó foi migrado ressincronizar-se totalmente com o nó local e mudar para o nó de replicação síncrona e torná-lo o AFP.  

#### <a name="step-17-migrate-second-node"></a>Etapa 17: Migrar o segundo nó

```powershell
$vmNameToMigrate="dansqlams1"

Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

#Get endpoint information
$endpoint = Get-AzureVM -ServiceName $sourceSvc  -Name $vmNameToMigrate | Get-AzureEndpoint

#Shutdown VM
Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | stop-AzureVM

#Get disk config

#Building Existing Data Disk Configuration
$file = "C:\Azure Storage Testing\mydiskconfig_$vmNameToMigrate.csv"
$datadisks = @(Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureDataDisk )
Add-Content $file “lun, vhdname, hostcaching, disklabel, diskName”
foreach ($disk in $datadisks)
{
    $vhdname = $disk.MediaLink.AbsolutePath -creplace  "/vhds/"
    $disk.Lun, , $disk.HostCaching, $vhdname, $disk.DiskLabel,$disks.DiskName
    # Write-Host "copying disk $disk"
    $adddisk = "{0},{1},{2},{3},{4}" -f $disk.Lun,$vhdname, $disk.HostCaching, $disk.DiskLabel, $disk.DiskName
    $adddisk | add-content -path $file
}

#Get OS Disk
$osdisks = Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureOSDisk ## | select -ExpandProperty MediaLink
$osvhdname = $osdisks.MediaLink.AbsolutePath -creplace  "/vhds/"
$osdisks.OS, $osdisks.HostCaching, $osvhdname, $osdisks.DiskLabel, $osdisks.DiskName
$addosdisk = "{0},{1},{2},{3},{4}" -f $osdisks.OS,$osvhdname, $osdisks.HostCaching, $osdisks.Disklabel , $osdisks.DiskName
$addosdisk | add-content -path $file

#Import disk config
$diskobjects  = Import-CSV $file

#Check disk configuration
$diskobjects

#Identify OS Disk
$osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
$osdiskforbuild = $osdiskimport.diskName

#Check machine is off
Get-AzureVM -ServiceName $sourceSvc -Name  $vmNameToMigrate

#Drop machine and rebuild to new cls
Remove-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate
```

#### <a name="step-18-change-disk-caching-settings-in-csv-file-and-save"></a>Etapa 18: Alterar as configurações de caching de disco no arquivo CSV e salvar

Para volumes de dados, as configurações de cache devem ser definidas como READONLY.

Para volumes TLOG, as configurações de cache devem ser definidas como NONE.

![Appendix11][21]

#### <a name="step-19-create-new-independent-storage-account-for-secondary-node"></a>Etapa 19: Criar a nova conta de armazenamento independente para o nó secundário

```powershell
$newxiostorageaccountnamenode2 = "danspremsams2"
New-AzureStorageAccount -StorageAccountName $newxiostorageaccountnamenode2 -Location $location -Type "Premium_LRS"  

#Reset the storage account src if node 1 in a different storage account
$origstorageaccountname2nd = "danstdams2"

#Generate storage keys for later
$xiostoragenode2 = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountnamenode2

#Generate storage acc contexts
$xioContextnode2 = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountnamenode2 -StorageAccountKey $xiostoragenode2.Primary  

#Set up subscription and default storage account
Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountnamenode2
Select-AzureSubscription -SubscriptionName $mysubscription -Current
```

#### <a name="step-20-copy-vhds"></a>Etapa 20: Copiar VHDs

```powershell
#Ensure you have created the container for these:
$containerName = 'vhds'

#Create container
New-AzureStorageContainer -Name $containerName -Context $xioContextnode2  

####DISK COPYING####
##get disks from csv, get settings for each VHDs and copy to Premium Storage accoun
ForEach ($disk in $diskobjects)
{
   $lun = $disk.Lun
   $vhdname = $disk.vhdname
   $cacheoption = $disk.HostCaching
   $disklabel = $disk.DiskLabel
   $diskName = $disk.DiskName
   Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname has cache setting : $cacheoption"

   #Start async copy
   Start-AzureStorageBlobCopy -srcUri "https://$origstorageaccountname2nd.blob.core.windows.net/vhds/$vhdname" `
    -SrcContext $origContext `
    -DestContainer $containerName `
    -DestBlob $vhdname `
    -DestContext $xioContextnode2
}

#Check for copy progress

#check individual blob status
Get-AzureStorageBlobCopyState -Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd" -Container $containerName -Context $xioContext
```

Você pode verificar o status da cópia VHD para todos os VHDs:

```powershell
ForEach ($disk in $diskobjects)
{
    $lun = $disk.Lun
    $vhdname = $disk.vhdname
    $cacheoption = $disk.HostCaching
    $disklabel = $disk.DiskLabel
    $diskName = $disk.DiskName

    $copystate = Get-AzureStorageBlobCopyState -Blob $vhdname -Container $containerName -Context $xioContextnode2
    Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname, STATUS = " $copystate.Status
}
```

![Appendix12][22]

Aguarde até que todos esses itens sejam registrados como êxito.

Para obter informações para blobs individuais:

```powershell
#Check individual blob status
Get-AzureStorageBlobCopyState -Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd" -Container $containerName -Context $xioContextnode2
```

#### <a name="step-21-register-os-disk"></a>Etapa 21: Registrar o disco do sistema operacional

```powershell
#change storage account to the new XIO storage account
Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountnamenode2
Select-AzureSubscription -SubscriptionName $mysubscription -Current

#Register OS disk
$osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
$osvhd = $osdiskimport.vhdname
$osdiskforbuild = $osdiskimport.diskName

#Registering OS disk, but as XIO disk
$xioDiskName = $osdiskforbuild + "xio"
Add-AzureDisk -DiskName $xioDiskName -MediaLocation  "https://$newxiostorageaccountnamenode2.blob.core.windows.net/vhds/$osvhd"  -Label "BootDisk" -OS "Windows"

#Build VM Config
$ipaddr = "192.168.0.4"
$newInstanceSize = "Standard_DS13"

#Join to existing Availability Set

#Build machine config into object
$vmConfig = New-AzureVMConfig -Name $vmNameToMigrate -InstanceSize $newInstanceSize -DiskName $xioDiskName -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

#Reload disk config
$diskobjects  = Import-CSV $file
$datadiskimport = $diskobjects | where {$_.lun -ne "Windows"}

ForEach ( $attachdatadisk in $datadiskimport)
{
    $label = $attachdatadisk.disklabel
    $lunNo = $attachdatadisk.lun
    $hostcach = $attachdatadisk.hostcaching
    $datadiskforbuild = $attachdatadisk.diskName
    $vhdname = $attachdatadisk.vhdname

    ###This is different to just a straight cloud service change
    #note if you do not have a disk label the command below will fail, populate as required.
    $vmConfig | Add-AzureDataDisk -ImportFrom -MediaLocation "https://$newxiostorageaccountnamenode2.blob.core.windows.net/vhds/$vhdname" -LUN $lunNo -HostCaching $hostcach -DiskLabel $label

}

#Create VM
$vmConfig  | New-AzureVM –ServiceName $destcloudsvc –Location $location -VNetName $vnet -Verbose
```

#### <a name="step-22-add-load-balanced-endpoints-and-acls"></a>Etapa 22: Adicionar pontos de extremidade de carga balanceada e ACLs

```powershell
#Endpoints
$epname="sqlIntEP"
$prot="tcp"
$locport=1433
$pubport=1433
Get-AzureVM –ServiceName $destcloudsvc –Name $vmNameToMigrate  | Add-AzureEndpoint -Name $epname -Protocol $prot -LocalPort $locport -PublicPort $pubport -ProbePort 59999 -ProbeIntervalInSeconds 5 -ProbeTimeoutInSeconds 11  -ProbeProtocol "TCP" -InternalLoadBalancerName $ilb -LBSetName $ilb -DirectServerReturn $true | Update-AzureVM


#STOP!!! CHECK in the Azure portal or Machine Endpoints through PowerShell that these Endpoints are created!

#SET ACLs or Azure Network Security Groups & Windows FWs

#https://msdn.microsoft.com/library/azure/dn495192.aspx
```

#### <a name="step-23-test-failover"></a>Etapa 23: Failover de Teste

Aguarde até o nó migrado sincronizar com o nó Always On local. Coloque-o no modo de replicação síncrona e aguarde até que ele seja sincronizado. Em seguida, failover de local para o primeiro nó migrado, que é o AFP. Depois que isso funcionar, altere o último nó migrado para o AFP.

Você deve testar failovers entre todos os nós e executar testes de caos para garantir que os failovers funcionem como esperado e de modo oportuno.

#### <a name="step-24-put-back-cluster-quorum-settings--dns-ttl--failover-pntrs--sync-settings"></a>Etapa 24: Colocar de volta configurações de quorum de cluster / TTL DNS / Failover de impressoras / Configurações de sincronização

##### <a name="adding-ip-address-resource-on-same-subnet"></a>Adicionando recurso de endereço IP na mesma sub-rede

Se você tiver apenas dois SQL Servers e quiser migrá-los para um novo serviço de nuvem, mas quiser mantê-los na mesma sub-rede, poderá evitar colocar o ouvinte offline para excluir o endereço IP AlwaysOn original e adicionar o novo endereço IP. Se você está migrando as VMs para outra sub-rede, não é necessário fazer isso, pois há uma rede de cluster adicional que faz referência a essa sub-rede.

Depois que você ativar a réplica secundária migrada e adicionar o novo recurso de endereço IP ao novo serviço de nuvem antes de executar o failover da réplica primária existente, deverá executar essas etapas no Gerenciador de Cluster de Failover:

Para adicionar o endereço IP, confira o Apêndice, etapa 14.

1. Para obter o recurso de endereço IP atual, altere o possível proprietário para ‘SQL Server Primário Existente’, no exemplo, ‘dansqlams4’:

    ![Appendix13][23]
2. Para obter o novo recurso de endereço IP, altere o possível proprietário para ‘SQL Server secundário migrado’, no exemplo, ‘dansqlams5’:

    ![Appendix14][24]
3. Depois que isso for definido, você poderá executar o failover e, quando o último nó for migrado, os Possíveis Proprietários deverão ser editados para que esse nó seja adicionado como um Proprietário Possível:

    ![Appendix15][25]

## <a name="additional-resources"></a>Recursos adicionais

* [Armazenamento Premium do Azure](../disks-types.md)
* [Máquinas virtuais](https://azure.microsoft.com/services/virtual-machines/)
* [SQL Server nas Máquinas Virtuais do Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md)

<!-- IMAGES -->
[1]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/1_VNET_Portal.png
[2]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/2_Diskname_Lun.png
[3]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/3_Virtual_Disk_Properties.png
[4]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/4_Virtual_Disk_Properties_Details.png
[5]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/5_Get_Storage_Pool.png
[6]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/6_Deployments_Always_On.png
[7]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/7_Add_More_Secondaries.png
[8]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/8_Use_Existing_Secondary_Single_Site.png
[9]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/9_Use_Existing_Secondary_Multi_Site.png
[10]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/9_Use_Existing_Secondary_Multi_Site_b.png
[11]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_01.png
[12]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_02.png
[13]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_03.png
[14]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_04.png
[15]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_05.png
[16]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_06.png
[17]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_07.png
[18]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_08.png
[19]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_09.png
[20]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_10.png
[21]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_11.png
[22]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_12.png
[23]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_13.png
[24]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_14.png
[25]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_15.png
