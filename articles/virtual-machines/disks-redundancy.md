---
title: Opções de redundância para o Azure Managed disks
description: Saiba mais sobre o armazenamento com redundância de zona e o armazenamento com redundância local para Azure Managed disks.
author: roygara
ms.author: rogarana
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: f0f3baf1bf56f958408f789961812c0555f289f1
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102043636"
---
# <a name="redundancy-options-for-managed-disks"></a>Opções de redundância para discos gerenciados

O Azure Managed disks oferece duas opções de redundância de armazenamento, armazenamento com redundância de zona (ZRS) como uma visualização e armazenamento com redundância local. O ZRS fornece maior disponibilidade para discos gerenciados do que o LRS (armazenamento com redundância local). No entanto, a latência de gravação para discos LRS é melhor do que os discos ZRS porque os discos LRS gravam dados de forma síncrona em três cópias em um único data center.

## <a name="locally-redundant-storage-for-managed-disks"></a>Armazenamento com redundância local para discos gerenciados

O armazenamento com redundância local (LRS) Replica seus dados três vezes em um único data center na região selecionada. O LRS protege seus dados contra falhas de unidade e rack do servidor. 

Há algumas maneiras de proteger seu aplicativo usando discos LRS de uma falha de zona inteira que pode ocorrer devido a problemas de hardware ou desastres naturais:
- Use um aplicativo como SQL Server AlwaysOn, que pode gravar dados de forma síncrona em duas zonas e fazer failover automaticamente para outra zona durante um desastre.
- Faça backups frequentes de discos LRS com instantâneos do ZRS.
- Habilite a recuperação de desastre entre zonas para discos LRS por meio de [Azure site Recovery](../site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md). No entanto, a recuperação de desastre entre zonas não fornece nenhum objetivo de ponto de recuperação (RPO).

Se o fluxo de trabalho não oferecer suporte a gravações síncronas em nível de aplicativo entre zonas, ou se seu aplicativo precisar atender a zero RPO, os discos ZRS seriam ideais.

## <a name="zone-redundant-storage-for-managed-disks-preview"></a>Armazenamento com redundância de zona para discos gerenciados (versão prévia)

O ZRS (armazenamento com redundância de zona) Replica o disco gerenciado do Azure de forma síncrona em três zonas de disponibilidade do Azure na região selecionada. Cada zona de disponibilidade é um local físico separado com energia, resfriamento e rede independentes. 

Os discos ZRS permitem que você se recupere de falhas em zonas de disponibilidade. Se uma zona inteira foi desativada, um disco ZRS pode ser anexado a uma VM em uma zona diferente. Você também pode usar discos ZRS como um disco compartilhado para fornecer disponibilidade aprimorada para aplicativos em cluster ou distribuídos, como SQL FCI, SAP ASCS/SCS ou GFS2. Você pode anexar um disco ZRS compartilhado a VMs primárias e secundárias em diferentes zonas para tirar proveito de ZRS e [zonas de disponibilidade](../availability-zones/az-overview.md). Se a zona primária falhar, você poderá fazer failover rapidamente para a VM secundária usando a [reserva persistente de SCSI](disks-shared-enable.md#supported-scsi-pr-commands).

### <a name="limitations"></a>Limitações

Durante a visualização, ZRS para Managed disks tem as seguintes restrições:

- Com suporte apenas com unidades de estado sólido (SSD) Premium e SSDs padrão.
- Atualmente disponível apenas na região EastUS2EUAP.
- Os discos ZRS só podem ser criados com modelos de Azure Resource Manager usando a `2020-12-01` API.

Inscreva-se para a versão prévia [aqui](https://aka.ms/ZRSDisksPreviewSignUp).

### <a name="billing-implications"></a>Implicações de cobrança

Para obter detalhes, consulte a [página de preços do Azure](https://azure.microsoft.com/pricing/details/managed-disks/).

### <a name="comparison-with-other-disk-types"></a>Comparação com outros tipos de disco

Exceto por mais latência de gravação, os discos que usam ZRS são idênticos aos discos usando LRS. Eles têm os mesmos destinos de desempenho.

### <a name="create-zrs-managed-disks"></a>Criar ZRS Managed disks

Use a `2020-12-01` API com seu modelo de Azure Resource Manager para criar um disco ZRS.

#### <a name="create-a-vm-with-zrs-disks"></a>Criar uma VM com discos ZRS

```
$vmName = "yourVMName" 
$adminUsername = "yourAdminUsername"
$adminPassword = ConvertTo-SecureString "yourAdminPassword" -AsPlainText -Force
$osDiskType = "StandardSSD_ZRS"
$dataDiskType = "Premium_ZRS"
$region = "eastus2euap"
$resourceGroupName = "yourResourceGroupName"

New-AzResourceGroup -Name $resourceGroupName -Location $region
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
-TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/ZRSDisks/CreateVMWithZRSDataDisks.json" `
-resourceName $vmName `
-adminUsername $adminUsername `
-adminPassword $adminPassword `
-region $region `
-osDiskType $osDiskType `
-dataDiskType $dataDiskType
```

#### <a name="create-vms-with-a-shared-zrs-disk-attached-to-the-vms-in-different-zones"></a>Criar VMs com um disco ZRS compartilhado anexado às VMs em diferentes zonas

```
$vmNamePrefix = "yourVMNamePrefix"
$adminUsername = "yourAdminUserName"
$adminPassword = ConvertTo-SecureString "yourAdminPassword" -AsPlainText -Force
$osDiskType = "StandardSSD_LRS"
$sharedDataDiskType = "Premium_ZRS"
$region = "eastus2euap"
$resourceGroupName = "zrstesting1"

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
-TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/ZRSDisks/CreateVMsWithASharedDisk.json" `
-vmNamePrefix $vmNamePrefix `
-adminUsername $adminUsername `
-adminPassword $adminPassword `
-region $region `
-osDiskType $osDiskType `
-dataDiskType $sharedDataDiskType
```

#### <a name="create-a-virtual-machine-scale-set-with-zrs-disks"></a>Criar um conjunto de dimensionamento de máquinas virtuais com discos ZRS

```
$vmssName="yourVMSSName"
$adminUsername="yourAdminName"
$adminPassword=ConvertTo-SecureString "yourAdminPassword" -AsPlainText -Force
$region="eastus2euap"
$osDiskType="StandardSSD_LRS"
$dataDiskType="Premium_ZRS"

New-AzResourceGroupDeployment -ResourceGroupName zrstesting `
-TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/ZRSDisks/CreateVMSSWithZRSDisks.json" `
-vmssName "yourVMSSName" `
-adminUsername "yourAdminName" `
-adminPassword $password `
-region "eastus2euap" `
-osDiskType "StandardSSD_LRS" `
-dataDiskType "Premium_ZRS" `
```

## <a name="next-steps"></a>Próximas etapas

- Use estes [modelos de Azure Resource Manager de exemplo para criar uma VM com discos ZRS](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/ZRSDisks).