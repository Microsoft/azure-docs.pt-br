---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 16ccd89fe6eaad3fd6c2704b2f324f486eee45e1
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171900"
---
# <a name="common-errors-during-classic-to-azure-resource-manager-migration"></a>Erros comuns durante a migração do Clássico para o Azure Resource Manager
Este artigo cataloga os erros e mitigações mais comuns durante a migração de recursos de IaaS do modelo de implantação clássico do Azure para a pilha do Azure Resource Manager.

[!INCLUDE [updated-for-az](./updated-for-az.md)]

## <a name="list-of-errors"></a>Lista de erros

| Cadeia de caracteres de erro | Redução |
| --- | --- |
| Erro interno do servidor |Em alguns casos, isso é um erro transitório desaparece com uma nova tentativa. Se ele persistir, [entre em contato com o suporte do Azure](../articles/azure-supportability/how-to-create-azure-support-request.md) pois ele precisará de investigação dos logs da plataforma. <br><br> **OBSERVAÇÃO:** não tente realizar nenhuma automitigação depois que o incidente for controlado pela equipe de suporte, pois isso poderá ter consequências indesejadas em seu ambiente. |
| Não há suporte para migração para implantação {nome_da_implantação} em {nome_do_serviço_hospedado} HostedService porque é uma implantação de PaaS (Web/Trabalho). |Isso ocorre quando uma implantação contém uma função de trabalho/Web. Uma vez que a migração tem suporte apenas para máquinas virtuais, remova a função Web/de trabalho da implantação e tente novamente a migração. |
| Falha na implantação do modelo {nome_do_ modelo}. CorrelationId={guid} |No back-end do serviço de migração, usamos modelos do Azure Resource Manager para criar recursos na pilha do Azure Resource Manager. Já que os modelos são idempotentes, geralmente você pode realizar com segurança novas tentativas da operação de migração para passar por esse erro. Se esse erro persistir, faça [entre em contato com o suporte do Azure](../articles/azure-supportability/how-to-create-azure-support-request.md) e dê a eles a CorrelationId. <br><br> **OBSERVAÇÃO:** não tente realizar nenhuma automitigação depois que o incidente for controlado pela equipe de suporte, pois isso poderá ter consequências indesejadas em seu ambiente. |
| A rede virtual {nome_da_ rede_virtual} não existe. |Isso poderá acontecer se você tiver criado a rede virtual no novo Portal do Azure. O nome de rede Virtual real segue o padrão "grupo * \<nome da rede virtual >" |
| A VM {nome_da_VM} no HostedService {nome_do_serviço_hospedado} contém a Extensão {nome_da_extensão} para a qual não há suporte no Azure Resource Manager. É recomendável desinstalá-la da VM antes de continuar com a migração. |Extensões XML como o BGInfo 1. \* não têm suporte no Azure Resource Manager. Portanto, essas extensões não podem ser migradas. Se essas extensões forem deixadas instaladas na máquina virtual, elas serão automaticamente desinstaladas antes da conclusão da migração. |
| A VM {nome_da_VM} no serviço hospedado {nome_do_serviço_hospedado} contém a Extensão VMSnapshot/VMSnapshotLinux, que atualmente não tem suporte para Migração. Desinstale-a da VM e adicione-a usando o Azure Resource Manager após a conclusão da migração |Esse é o cenário em que a máquina virtual está configurada para o Backup do Azure. Como atualmente este é um cenário sem suporte, siga a solução https://aka.ms/vmbackupmigration |
| A VM {nome_da_VM} no serviço hospedado {nome_do_serviço_hospedado} contém a Extensão {nome_da_extensão}, cujo status não está sendo relatado da VM. Portanto, esta VM não pode ser migrada. Certifique-se de que o status da Extensão está sendo relatado ou desinstale a extensão da VM e tente novamente realizar a migração. <br><br> A VM {nome_da_VM} no serviço hospedado {nome_do_serviço_hospedado} contém a Extensão {nome_da_extensão} relatando o status do manipulador: {status_do_manipulador}. Portanto, a VM não pode ser migrada. Certifique-se de que o status do manipulador de Extensões sendo relatado é {status_do_manipulador} ou desinstale-o da VM e tente novamente realizar a migração. <br><br> Agente de VM para a VM {nome_da_VM} no serviço hospedado {nome_do_serviço_hospedado} está relatando o status geral do agente como Não Pronto. Portanto, se a VM tiver uma extensão migrável, ela não poderá ser migrada. Certifique-se de que o agente de VM esteja relatando o status do agente geral como Pronto. Consulte https://aka.ms/classiciaasmigrationfaqs. |O agente convidado do Azure e extensões de VM precisam de acesso de saída à Internet para que seus status sejam populados pela conta de armazenamento da VM. Causas comuns de falha de status incluem <li> um Grupo de Segurança de Rede que bloqueia o acesso de saída à Internet <li> Se a rede virtual tem no local de servidores DNS e conectividade DNS for perdida <br><br> Se você continuar a ver um status sem suporte, você poderá desinstalar as extensões para ignorar essa verificação e prosseguir com a migração. |
| Não há suporte para migração para implantação {nome_da_implantação} no serviço hospedado {nome_do_serviço_hospedado} porque ele tem vários conjuntos de disponibilidade. |Atualmente, apenas os serviços hospedados com um ou nenhum conjunto de disponibilidade podem ser migrados. Para contornar esse problema, mova os conjuntos de disponibilidade e as máquinas virtuais adicionais nesses conjuntos de disponibilidade para um serviço hospedado diferente. |
| Não há suporte para migração para a implantação {nome_da_implantação} no serviço hospedado {nome_do_serviço_hospedado} porque ela tem VMs que não são parte do conjunto de disponibilidade, embora o serviço hospedado contenha uma. |A solução para esse cenário é para mover todas as máquinas virtuais para um único conjunto de disponibilidade ou remover todas as máquinas virtuais do conjunto de disponibilidade no serviço hospedado. |
| A conta de armazenamento/serviço hospedado/rede virtual {nome_da_rede_virtual} está no processo de migração e, portanto, não pode ser alterada |Esse erro ocorre quando a operação de migração "Prepare" foi concluída no recurso e uma operação que faça uma alteração no recurso é disparada. Por causa do bloqueio no plano de gerenciamento após a operação de "Prepare", as alterações ao recurso são bloqueadas. Para desbloquear o plano de gerenciamento, você pode executar a operação de migração de "Commit" para concluir a migração ou a operação de migração "Abort" para reverter a operação "Prepare". |
| A migração não é permitida para o HostedService {hosted-service-name} porque ele tem a VM {vm-name} no estado: RoleStateUnknown. A migração é permitida somente quando a VM está em um dos seguintes estados – Em execução, Parada, Interrompida, Desalocada. |A VM pode estar passando por uma transição de estado que geralmente acontece durante uma operação de atualização no serviço hospedado como uma reinicialização, instalação de extensão, etc. É recomendável que a operação de atualização seja concluída no HostedService antes de tentar a migração. |
| A implantação {deployment-name} em HostedService {hosted-service-name} contém uma VM {vm-name} com Disco de Dados {data-disk-name} cujo tamanho de blob físico de {size-of-the-vhd-blob-backing-the-data-disk} bytes não corresponde ao tamanho lógico do Disco de Dados da VM de {size-of-the-data-disk-specified-in-the-vm-api} bytes. A migração continuará sem especificar um tamanho para o disco de dados para a VM do Azure Resource Manager. | Esse erro ocorre se você redimensionar o blob VHD sem atualizar o tamanho do modelo da API da VM. As etapas de atenuação detalhadas estão descritas [abaixo](#vm-with-data-disk-whose-physical-blob-size-bytes-does-not-match-the-vm-data-disk-logical-size-bytes).|
| Ocorreu uma exceção de armazenamento ao validar o disco de dados {nome do disco de dados} com link de mídia {Uri do disco de dados} para VM {nome da VM} no Serviço de Nuvem {nome do Serviço de Nuvem}. Assegure-se de que o link de mídia VHD possa ser acessado para esta máquina virtual | Esse erro poderá ocorrer se os discos da VM tiverem sido excluídos ou não estiverem mais acessíveis. Verifique se os discos para a VM existem.|
| A VM {vm-name} em HostedService {cloud-service-name} contém o Disco com MediaLink {vhd-uri} que tem o nome do blob {vhd-blob-name}, que não é suportado no Azure Resource Manager. | Esse erro ocorre quando o nome do blob tem um "/" dentro dele que não tem suporte no Provedor de Recursos de Computação no momento. |
| A migração não é permitida para a Implantação {nome-implantação} no HostedService {nome-serviço-nuvem}, pois não está no escopo regional. Consulte para https:\//aka.ms/regionalscope para mover essa implantação para o escopo regional. | Em 2014, o Azure anunciou que os recursos de rede seriam movidos de um escopo no nível do cluster para um escopo regional. Ver [ https://aka.ms/regionalscope ](https://aka.ms/regionalscope) para obter mais detalhes. Esse erro ocorre quando a implantação sendo migrada não teve uma operação de atualização que a move automaticamente para um escopo regional. A melhor alternativa é adicionar um ponto de extremidade a uma VM ou um disco de dados à VM, em seguida, repetir a migração. <br> Consulte [Como configurar os pontos de extremidade em uma máquina virtual clássica do Windows no Azure](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#create-an-endpoint) ou [Anexar um disco de dados a uma máquina virtual do Windows criada com o modelo de implantação clássico](../articles/virtual-machines/windows/classic/attach-disk.md)|
| A migração não é compatível com a Rede Virtual {vnet-name}  porque ela contém implantações e PaaS de não gateway. | Esse erro ocorre quando você tem implantações PaaS de não gateway como os serviços de gerenciamento de API ou Gateway do aplicativo que estão conectados à Rede Virtual.|


## <a name="detailed-mitigations"></a>Atenuação detalhada

### <a name="vm-with-data-disk-whose-physical-blob-size-bytes-does-not-match-the-vm-data-disk-logical-size-bytes"></a>A VM com o Disco de Dados cujo tamanho de blob físico é de bytes não corresponde ao tamanho lógico do Disco de Dados da VM de bytes.

Isso acontece quando o tamanho lógico do Disco de Dados perde a sincronia com o tamanho real do blob VHD. Isso pode ser facilmente verificado usando os seguintes comandos:

#### <a name="verifying-the-issue"></a>Verificando o problema

```powershell
# Store the VM details in the VM object
$vm = Get-AzureVM -ServiceName $servicename -Name $vmname

# Display the data disk properties
# NOTE the data disk LogicalDiskSizeInGB below which is 11GB. Also note the MediaLink Uri of the VHD blob as we'll use this in the next step
$vm.VM.DataVirtualHardDisks


HostCaching         : None
DiskLabel           : 
DiskName            : coreosvm-coreosvm-0-201611230636240687
Lun                 : 0
LogicalDiskSizeInGB : 11
MediaLink           : https://contosostorage.blob.core.windows.net/vhds/coreosvm-dd1.vhd
SourceMediaLink     : 
IOType              : Standard
ExtensionData       : 

# Now get the properties of the blob backing the data disk above
# NOTE the size of the blob is about 15 GB which is different from LogicalDiskSizeInGB above
$blob = Get-AzStorageblob -Blob "coreosvm-dd1.vhd" -Container vhds 

$blob

ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudPageBlob
BlobType          : PageBlob
Length            : 16106127872
ContentType       : application/octet-stream
LastModified      : 11/23/2016 7:16:22 AM +00:00
SnapshotTime      : 
ContinuationToken : 
Context           : Microsoft.WindowsAzure.Commands.Common.Storage.AzureStorageContext
Name              : coreosvm-dd1.vhd
```

#### <a name="mitigating-the-issue"></a>Atenuando o problema

```powershell
# Convert the blob size in bytes to GB into a variable which we'll use later
$newSize = [int]($blob.Length / 1GB)

# See the calculated size in GB
$newSize

15

# Store the disk name of the data disk as we'll use this to identify the disk to be updated
$diskName = $vm.VM.DataVirtualHardDisks[0].DiskName

# Identify the LUN of the data disk to remove
$lunToRemove = $vm.VM.DataVirtualHardDisks[0].Lun

# Now remove the data disk from the VM so that the disk isn't leased by the VM and it's size can be updated
Remove-AzureDataDisk -LUN $lunToRemove -VM $vm | Update-AzureVm -Name $vmname -ServiceName $servicename

OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
Update-AzureVM       213xx1-b44b-1v6n-23gg-591f2a13cd16   Succeeded  

# Verify we have the right disk that's going to be updated
Get-AzureDisk -DiskName $diskName

AffinityGroup        : 
AttachedTo           : 
IsCorrupted          : False
Label                : 
Location             : East US
DiskSizeInGB         : 11
MediaLink            : https://contosostorage.blob.core.windows.net/vhds/coreosvm-dd1.vhd
DiskName             : coreosvm-coreosvm-0-201611230636240687
SourceImageName      : 
OS                   : 
IOType               : Standard
OperationDescription : Get-AzureDisk
OperationId          : 0c56a2b7-a325-123b-7043-74c27d5a61fd
OperationStatus      : Succeeded

# Now update the disk to the new size
Update-AzureDisk -DiskName $diskName -ResizedSizeInGB $newSize -Label $diskName

OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
Update-AzureDisk     cv134b65-1b6n-8908-abuo-ce9e395ac3e7 Succeeded 

# Now verify that the "DiskSizeInGB" property of the disk matches the size of the blob 
Get-AzureDisk -DiskName $diskName


AffinityGroup        : 
AttachedTo           : 
IsCorrupted          : False
Label                : coreosvm-coreosvm-0-201611230636240687
Location             : East US
DiskSizeInGB         : 15
MediaLink            : https://contosostorage.blob.core.windows.net/vhds/coreosvm-dd1.vhd
DiskName             : coreosvm-coreosvm-0-201611230636240687
SourceImageName      : 
OS                   : 
IOType               : Standard
OperationDescription : Get-AzureDisk
OperationId          : 1v53bde5-cv56-5621-9078-16b9c8a0bad2
OperationStatus      : Succeeded

# Now we'll add the disk back to the VM as a data disk. First we need to get an updated VM object
$vm = Get-AzureVM -ServiceName $servicename -Name $vmname

Add-AzureDataDisk -Import -DiskName $diskName -LUN 0 -VM $vm -HostCaching ReadWrite | Update-AzureVm -Name $vmname -ServiceName $servicename

OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
Update-AzureVM       b0ad3d4c-4v68-45vb-xxc1-134fd010d0f8 Succeeded      
```

### <a name="moving-a-vm-to-a-different-subscription-after-completing-migration"></a>Como mover uma VM para uma assinatura diferente após a conclusão da migração

Depois de concluir o processo de migração, talvez você queira mover a VM para outra assinatura. No entanto, se você tiver um segredo/certificado na VM que faça referência a um recurso do Key Vault, a movimentação atualmente não tem suporte. As instruções abaixo permitirão que você contorne o problema. 

#### <a name="powershell"></a>PowerShell
```powershell
$vm = Get-AzVM -ResourceGroupName "MyRG" -Name "MyVM"
Remove-AzVMSecret -VM $vm
Update-AzVM -ResourceGroupName "MyRG" -VM $vm
```
#### <a name="azure-cli"></a>CLI do Azure

```bash
az vm update -g "myrg" -n "myvm" --set osProfile.Secrets=[]
```
