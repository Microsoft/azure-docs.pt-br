---
title: Solução de problemas Azure Disk Encryption para VMs Linux
description: Este artigo fornece dicas de solução de problemas para Microsoft Azure a criptografia de disco para VMs do Linux.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: troubleshooting
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 58ea788d6959b039ff5de8a4a889c531b987d826
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102563363"
---
# <a name="azure-disk-encryption-for-linux-vms-troubleshooting-guide"></a>Guia de solução de problemas do Azure Disk Encryption para VMs do Linux

Este guia destina-se a profissionais de TI, analistas de segurança da informação e administradores de nuvem cujas organizações usam o Azure Disk Encryption. Este artigo é para ajudar na solução de problemas relacionados à criptografia de disco.

Antes de executar qualquer uma das etapas abaixo, primeiro verifique se as VMs que você está tentando criptografar estão entre os [tamanhos e sistemas operacionais de VM com suporte](disk-encryption-overview.md#supported-vms-and-operating-systems) e se você atendeu a todos os pré-requisitos:

- [Requisitos adicionais para VMs](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Requisitos de rede](disk-encryption-overview.md#networking-requirements)
- [Requisitos de armazenamento de chave de criptografia](disk-encryption-overview.md#encryption-key-storage-requirements)

 

## <a name="troubleshooting-linux-os-disk-encryption"></a>Solução de problemas de criptografia de disco do SO Linux

A criptografia de disco do SO (sistema operacional) Linux deve desmontar a unidade do SO antes de executá-lo por meio do processo de criptografia de disco completo. Se não for possível desmontar a unidade, é provável que ocorra uma mensagem de erro de "falha ao desmontar após ...".

Esse erro pode ocorrer quando a criptografia de disco do sistema operacional é tentada em uma VM com um ambiente que foi alterado da imagem da Galeria de estoque com suporte. Desvios da imagem compatível podem interferir com a capacidade da extensão de desmontar a unidade do SO. Exemplos de desvios podem incluir os seguintes itens:
- Imagens personalizadas que não correspondem mais a um sistema de arquivos ou esquema de particionamento suportado.
- Não há suporte para aplicativos grandes, como SAP, MongoDB, Apache Cassandra e Docker, quando eles estão instalados e executados no sistema operacional antes da criptografia. A Criptografia de Disco do Azure não consegue encerrar esses processos com segurança, conforme necessário na preparação da unidade do sistema operacional para criptografia de disco. Se ainda houver processos ativos mantendo alças de arquivos abertos na unidade do sistema operacional, a unidade do sistema operacional não poderá ser desmontada, resultando em uma falha na criptografia da unidade do sistema operacional. 
- Scripts personalizados que são executados próximos à hora de encerramento da criptografia que está sendo habilitada ou se qualquer outra alteração estiver sendo feita na VM durante o processo de criptografia. Esse conflito pode acontecer quando um modelo do Azure Resource Manager define múltiplas extensões para executar simultaneamente ou quando uma extensão de script personalizada ou outra ação é executada simultaneamente com a criptografia de disco. Serializar e isolar tais etapas pode resolver o problema.
- O SELinux (Security Enhanced Linux) não foi desabilitado antes da habilitação da criptografia, fazendo com que a etapa de desmontagem falhe. O SELinux pode ser reabilitado após a conclusão da criptografia.
- O disco do SO usa um esquema LVM (Gerenciador de Volume Lógico). Embora o suporte ao disco de dados LVM limitado esteja disponível, um disco de SO LVM, não.
- Os requisitos mínimos de memória não são atendidos (7 GB é sugerido para criptografia de disco do SO).
- As unidades de dados são montadas recursivamente no diretório /mnt/ ou uma na outra (por exemplo, /mnt/data1, /mnt/data2, /data3 + /data3/data4).

## <a name="update-the-default-kernel-for-ubuntu-1404-lts"></a> Atualizar o kernel padrão para o Ubuntu 14.04 LTS

A imagem do Ubuntu 14.04 LTS é fornecida com uma versão padrão do kernel de 4.4. Esta versão do kernel tem um problema conhecido no qual o Encerrador de Memória Insuficiente encerra incorretamente o comando dd durante o processo de criptografia do sistema operacional. Esse bug foi corrigido no mais recente kernel do Linux ajustado para o Azure. Para evitar esse erro, antes de habilitar a criptografia na imagem, atualize para o [kernel ajustado para o Azure 4.15](https://packages.ubuntu.com/trusty/linux-azure) ou posterior usando os comandos a seguir:

```
sudo apt-get update
sudo apt-get install linux-azure
sudo reboot
```

Depois de reiniciar a VM para o novo kernel, a nova versão do kernel pode ser confirmada usando:

```
uname -a
```

## <a name="update-the-azure-virtual-machine-agent-and-extension-versions"></a>Atualizar o agente de máquina virtual do Azure e as versões de extensão

Azure Disk Encryption operações podem falhar em imagens de máquina virtual usando versões sem suporte do agente de máquina virtual do Azure. Imagens do Linux com versões de agente anteriores a 2.2.38 devem ser atualizadas antes de habilitar a criptografia. Para obter mais informações, consulte [como atualizar o agente Linux do Azure em uma VM e o](../extensions/update-linux-agent.md) [suporte de versão mínima para agentes de máquina virtual no Azure](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

A versão correta da extensão de agente convidado Microsoft. Azure. Security. AzureDiskEncryption ou Microsoft. Azure. Security. AzureDiskEncryptionForLinux também é necessária. As versões de extensão são mantidas e atualizadas automaticamente pela plataforma quando os pré-requisitos do agente de máquina virtual do Azure são satisfeitos e uma versão com suporte do agente de máquina virtual é usada.

A extensão Microsoft. OSTCExtensions. AzureDiskEncryptionForLinux foi preterida e não é mais suportada.  

## <a name="unable-to-encrypt-linux-disks"></a>Não é possível criptografar discos do Linux

Em alguns casos, a criptografia de disco do Linux parece estar paralisada na "criptografia de disco do OS iniciada" e o SSH está desabilitado. O processo de criptografia pode levar entre 3 e 16 horas para ser concluído em uma imagem da galeria de estoque. Se discos de dados com diversos terabytes forem adicionados, o processo poderá levar dias.

A sequência de criptografia de disco do SO Linux desmonta a unidade do SO temporariamente. Em seguida, ela realiza a criptografia bloco a bloco de todo o disco do SO, antes de remontá-lo em seu estado criptografado. A criptografia de disco do Linux não permite o uso simultâneo da VM enquanto a criptografia está em andamento. As características de desempenho da VM podem fazer uma diferença significativa no tempo necessário para concluir a criptografia. Essas características incluem o tamanho do disco e se a conta de armazenamento é padrão ou premium (SSD).

Enquanto a unidade do sistema operacional está sendo criptografada, a VM entra em um estado de manutenção e desabilita o SSH para evitar qualquer interrupção no processo em andamento.  Para verificar o status de criptografia, use o comando Azure PowerShell [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) e verifique o campo **ProgressMessage** . O **ProgressMessage** relatará uma série de status, pois os dados e os discos do sistema operacional são criptografados:

```azurepowershell
PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyResourceGroup" -VMName "myVM"

OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings :
ProgressMessage            : Transitioning

PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyResourceGroup" -VMName "myVM"

OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : Encryption succeeded for data volumes

PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyResourceGroup" -VMName "myVM"

OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : Provisioning succeeded

PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyResourceGroup" -VMName "myVM"

OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk encryption started
```

O **ProgressMessage** permanecerá no **sistema operacional criptografia de disco iniciado** para a maior parte do processo de criptografia.  Quando a criptografia for concluída e for bem-sucedida, **ProgressMessage** retornará:

```azurepowershell
PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyResourceGroup" -VMName "myVM"

OsVolumeEncrypted          : Encrypted
DataVolumesEncrypted       : NotMounted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : Encryption succeeded for all volumes
```

Depois que essa mensagem estiver disponível, espera-se que a unidade do SO criptografada esteja pronta para uso e que a VM esteja pronta para ser usada novamente.

Se as informações de inicialização, a mensagem de progresso ou um erro relatar que a criptografia do sistema operacional falhou no meio desse processo, restaure a VM para o instantâneo ou backup feito imediatamente antes da criptografia. Um exemplo de mensagem é o erro "falha ao desmontar", que é descrito neste guia.

Antes de tentar novamente a criptografia, reavalie as características da VM e verifique se todos os pré-requisitos estão satisfeitos.

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>Solucionando problemas do Azure Disk Encryption por trás de um firewall

Consulte [criptografia de disco em uma rede isolada](disk-encryption-isolated-network.md)

## <a name="troubleshooting-encryption-status"></a>Solução de problemas de status de criptografia 

O portal pode exibir um disco como criptografado mesmo após ele ter sido descriptografado na VM.  Isso pode ocorrer quando comandos de baixo nível são usados para descriptografar diretamente o disco de dentro da VM, em vez de usar os comandos de gerenciamento do Azure Disk Encryption de nível superior.  Os comandos de nível superior não apenas descriptografam o disco de dentro da VM, mas fora da VM eles também atualizam configurações importantes de criptografia de nível de plataforma e configurações de extensão associadas à VM.  Se eles não forem mantidos alinhados, a plataforma não poderá relatar o status de criptografia nem provisionar a VM corretamente.

Para desabilitar Azure Disk Encryption com o PowerShell, use [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) seguido por [Remove-AzVMDiskEncryptionExtension](/powershell/module/az.compute/remove-azvmdiskencryptionextension). A execução de Remove-AzVMDiskEncryptionExtension falhará antes de a criptografia ser desabilitada.

Para desabilitar o Azure Disk Encryption com a CLI, use [az vm encryption disable](/cli/azure/vm/encryption).

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu mais sobre alguns problemas comuns no Azure Disk Encryption e como solucioná-los. Para saber mais sobre esse serviço e seus recursos, confira os seguintes artigos:

- [Aplicar a criptografia de disco na Central de Segurança do Azure](../../security-center/asset-inventory.md)
- [Criptografia de dados em repouso do Azure](../../security/fundamentals/encryption-atrest.md)