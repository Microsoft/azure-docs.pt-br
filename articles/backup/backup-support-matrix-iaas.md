---
title: Matriz de suporte para backup de VM do Azure
description: Fornece um resumo de configurações compatíveis e limitações ao fazer backup de VMs do Azure com o serviço de Backup do Azure.
ms.topic: conceptual
ms.date: 09/13/2019
ms.custom: references_regions
ms.openlocfilehash: 82de73944bd833ec5f921e07a29960ac6d175f8d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102609780"
---
# <a name="support-matrix-for-azure-vm-backup"></a>Matriz de suporte para backup de VM do Azure

Você pode usar o [serviço de Backup do Azure](backup-overview.md) para fazer backup de cargas de trabalho e computadores locais e VMs (máquinas virtuais) do Azure. Este artigo resume as configurações de suporte e limitações ao fazer backup de VMs do Azure com o Backup do Azure.

Outras matrizes de suporte:

- [Matriz de suporte geral](backup-support-matrix.md) para o Backup do Azure
- [Matriz de suporte](backup-support-matrix-mabs-dpm.md) para o servidor de backup do Azure/backup do System Center Data Protection Manager (DPM)
- [Matriz de suporte](backup-support-matrix-mars-agent.md) para backup com o agente MARS (Serviços de Recuperação do Microsoft Azure)

## <a name="supported-scenarios"></a>Cenários com suporte

Veja como fazer backup e restaurar as VMs do Azure com o serviço de Backup do Azure.

**Cenário** | **Backup** | **Agente** |**Restaurar**
--- | --- | --- | ---
Backup direto de VMs do Azure  | Faça backup de toda a VM.  | Nenhum agente adicional é necessário na VM do Azure. O backup do Azure é instalado e usa uma extensão para o [agente de VM do Azure](../virtual-machines/extensions/agent-windows.md) que está em execução na VM. | Restaure da seguinte maneira:<br/><br/> - **Crie uma VM básica**. Isso é útil se a VM não tiver nenhuma configuração especial, como vários endereços IP.<br/><br/> - **Restaure o disco da VM**. Restaure o disco. Depois anexe-o a uma VM existente ou crie uma VM desde o disco usando o PowerShell.<br/><br/> - **Substitua o disco da VM**. Se existir uma VM que usa discos gerenciados (não criptografados), você pode restaurar um disco e usá-lo para substituir um disco existente na VM.<br/><br/> - **Restaure arquivos/pastas específicas**. Você pode restaurar os arquivos/pastas de uma VM, em vez da VM inteira.
Backup direto de VMs do Azure (somente no Windows)  | Faça backup de arquivos/pastas/volumes específicos. | Instale o [agente de Serviços de Recuperação do Azure](backup-azure-file-folder-backup-faq.md).<br/><br/> Você pode executar o agente do MARS com a extensão de backup para o agente de VM do Azure para fazer backup da VM no nível de arquivo/pasta. | Restaure arquivos/pastas específicos.
Fazer backup de VM do Azure no servidor de backup  | Faça backup de arquivos/pastas/volumes; arquivos de estado do sistema/bare metal; dados de aplicativos do System Center DPM ou do MABS (Servidor de Backup do Microsoft Azure).<br/><br/> Em seguida, o DPM/MABS faz backup no cofre. | Instale o agente de proteção do DPM/MABS na VM. O agente de MARS é instalado no DPM/MABS.| Restaure arquivos/pastas/volumes; arquivos de estado do sistema/bare metal; dados de aplicativos.

Saiba mais sobre o backup [usando um servidor de backup](backup-architecture.md#architecture-back-up-to-dpmmabs) e sobre [requisitos de suporte](backup-support-matrix-mabs-dpm.md).

## <a name="supported-backup-actions"></a>Ações de backup compatíveis

**Ação** | **Suporte**
--- | ---
Fazer backup de uma VM que esteja desligada/offline | Com suporte.<br/><br/> O instantâneo só é consistente com a falha e não com o aplicativo.
Fazer backup de discos após a migração para discos gerenciados | Com suporte.<br/><br/> O backup continuará a funcionar. Nenhuma ação é necessária.
Backup de discos gerenciados depois de habilitar o bloqueio de grupo de recursos | Sem suporte.<br/><br/> O Backup do Azure não consegue excluir os pontos mais antigos do recurso e os backups começam a falhar ao atingir o limite máximo de pontos de restauração.
Modificar a política de backup de uma VM | Com suporte.<br/><br/> O backup da VM é feito usando as configurações de retenção e agendamento da nova política. Se as configurações de retenção forem estendidas, os pontos de recuperação existentes serão marcados e mantidos. Se forem reduzidas, os pontos de recuperação existentes serão removidos no próximo trabalho de limpeza e eventualmente excluídos.
Cancelar um trabalho de backup| Tem suporte durante o processo de instantâneo.<br/><br/> Não tem suporte quando o instantâneo está em transferência para o cofre.
Backup da VM para uma região ou uma assinatura diferentes |Sem suporte.<br><br>Para fazer backup com êxito, as máquinas virtuais precisam estar na mesma assinatura que a do cofre para backup.
Backups por dia (com a extensão de VM do Azure) | Um backup agendado por dia.<br/><br/>O serviço de backup do Azure dá suporte a até três backups sob demanda por dia e um backup adicional agendado.
Backups por dia (por meio do agente MARS) | Três backups agendados por dia.
Backups por dia (por meio do DPM/MABS) | Dois backups agendados por dia.
Backup mensal/anual| Não tem suporte durante o backup com a extensão de VM do Azure. Só há suporte para diários e semanais.<br/><br/> É possível configurar a política para manter backups diários/semanais por um período de retenção mensal/anual.
Ajuste automático de relógio | Sem suporte.<br/><br/> O Backup do Azure não é ajustado automaticamente para alterações do horário de verão ao fazer backup de uma VM.<br/><br/>  Modifique a política manualmente, se necessário.
[Recursos de segurança para backups híbridos](./backup-azure-security-feature.md) |Não há suporte para desabilitar recursos de segurança.
Fazer backup da VM cuja hora do computador foi alterada | Sem suporte.<br/><br/> Se a hora da máquina for alterada para uma data/hora futura depois de habilitar o backup dessa VM, no entanto, mesmo que a alteração de tempo seja revertida, o backup bem-sucedido não será garantido.

## <a name="operating-system-support-windows"></a>Suporte do sistema operacional (Windows)

A tabela a seguir resume os sistemas operacionais com suporte ao fazer backup de VMs do Microsoft Azure.

**Cenário** | **Suporte SO**
--- | ---
Fazer backup com a extensão do agente de VM do Azure | – Cliente do Windows 10 (somente 64 bits) <br/><br/>– Windows Server 2019 (Datacenter/Data Center Core/Standard) <br/><br/> – Windows Server 2016 (Datacenter/Data Center Core/Standard) <br/><br/> – Windows Server 2012 R2 (Datacenter/Standard) <br/><br/> -Windows Server 2012 (datacenter/padrão) <br/><br/> – Windows Server 2008 R2 (RTM e SP1 Standard)  <br/><br/> – Windows Server 2008 (somente 64 bits)
Backup com o agente MARS | Sistemas operacionais com [suporte](backup-support-matrix-mars-agent.md#supported-operating-systems).
Fazer backup com o DPM/MABS | Sistemas operacionais compatíveis para backup com [MABS](backup-mabs-protection-matrix.md) e [DPM](/system-center/dpm/dpm-protection-matrix).

O Backup do Azure não dá suporte a sistemas operacionais de 32 bits.

## <a name="support-for-linux-backup"></a>Suporte de backup do Linux

Veja a seguir os itens compatíveis, caso deseje fazer backup de computadores Linux.

**Ação** | **Suporte**
--- | ---
Backup de VMs do Azure no Linux com o agente de VM do Azure para Linux | Backup consistente de arquivos.<br/><br/> Backup consistente com aplicativo usando [scripts personalizados](backup-azure-linux-app-consistent.md).<br/><br/> Durante a restauração, você pode criar uma VM, restaurar um disco e usá-lo para criar uma VM ou restaurar um disco e usá-lo para substituir um disco em uma VM existente. Você também pode restaurar arquivos e pastas individuais.
Backup de VMs do Azure no Linux com o agente MARS | Sem suporte.<br/><br/> O agente do MARS só pode ser instalado em computadores Windows.
Backup de VMs do Azure no Linux com DPM/MABS | Sem suporte.
Fazer backup de VMs do Azure do Linux com pontos de montagem do Docker | Atualmente, o backup do Azure não dá suporte à exclusão de pontos de montagem do Docker, pois eles são montados em caminhos diferentes a cada vez.

## <a name="operating-system-support-linux"></a>Suporte do sistema operacional (Linux)

Para backups do Linux na VM do Azure, o Backup do Azure oferece suporte para a [lista de distribuições do Linux endossadas pelo Azure](../virtual-machines/linux/endorsed-distros.md). Observe o seguinte:

- O Backup do Azure não dá suporte ao principal sistema operacional Linux.
- O Backup do Azure não dá suporte a sistemas operacionais de 32 bits.
- Outras distribuições personalizadas do Linux devem funcionar, contanto que o [agente de VM do Azure para Linux](../virtual-machines/extensions/agent-linux.md) esteja disponível na VM com suporte para Python.
- O backup do Azure não oferece suporte a uma VM do Linux configurada por proxy se não tiver a versão 2,7 do Python instalada.
- O backup do Azure não dá suporte ao backup de arquivos NFS montados a partir do armazenamento ou de qualquer outro servidor NFS, para computadores Linux ou Windows. Ele faz backup apenas dos discos que estão anexados localmente à VM.

## <a name="backup-frequency-and-retention"></a>Frequência e retenção de backup

**Configuração** | **Limites**
--- | ---
Número máximo de pontos de recuperação por instância protegida (computador/carga de trabalho) | 9999.
Tempo máximo de expiração para um ponto de recuperação | Sem limite (99 anos).
Frequência máxima de backup para o cofre (extensão da VM do Azure) | Uma vez por dia.
Frequência máxima de backup para o cofre (Agente MARS) | Três backups por dia.
Frequência máxima de backup para o DPM/MABS | A cada 15 minutos para o SQL Server.<br/><br/> Uma vez por hora para outras cargas de trabalho.
Retenção do ponto de recuperação | Diária, semanal, mensal e anual.
Período de retenção máximo | Depende da frequência de backup.
Pontos de recuperação no disco do DPM/MABS | 64 para servidores de arquivos e 448 para servidores de aplicativos.<br/><br/> Os pontos de recuperação de fita são ilimitados para o DPM local.

## <a name="supported-restore-methods"></a>Métodos de restauração compatíveis

**Opção de restauração** | **Detalhes**
--- | ---
**Criar uma nova VM** | Cria e coloca rapidamente uma VM básica em funcionamento a partir de um ponto de restauração.<br/><br/> Você pode especificar um nome para a VM, selecionar o grupo de recursos e a VNet (rede virtual) em que ela será colocada e especificar uma conta de armazenamento para a VM restaurada. A nova VM precisa ser criada na mesma região que o VM de origem.
**Restaurar disco** | Restaura um disco da VM que pode ser usado para criar uma VM.<br/><br/> O Backup do Azure fornece um modelo para ajudá-lo a personalizar e criar uma VM. <br/><br> O trabalho de restauração gera um modelo que você pode baixar e usar para especificar configurações de VM personalizadas e criar uma VM.<br/><br/> Os discos são copiados para o grupo de recursos que você especificar.<br/><br/> Como alternativa, você pode anexar o disco a uma VM existente ou criar uma nova VM usando o PowerShell.<br/><br/> Essa opção é útil se você quiser personalizar a VM, adicionar configurações que não existiam no momento do backup ou adicionar configurações que devem ser definidas usando o modelo ou o PowerShell.
**Substituir existente** | Você pode restaurar um disco e usá-lo para substituir um disco na VM existente.<br/><br/> A VM atual deve existir. Se ela tiver sido excluída, essa opção não poderá ser usada.<br/><br/> O Backup do Azure tira um instantâneo da VM existente antes de substituir o disco e o armazena no local de preparo especificado. Os discos existentes conectados à VM são substituídos pelo ponto de restauração selecionado.<br/><br/> O instantâneo é copiado para o cofre e retido de acordo com a política de retenção. <br/><br/> Após a operação de substituição de disco, o disco original é retido no grupo de recursos. Você pode optar por excluir manualmente os discos originais se eles não forem necessários. <br/><br/>A substituição existente tem suporte para VMs gerenciadas não criptografadas e para VMs [criadas usando imagens personalizadas](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/). Não há suporte para discos e VMs não gerenciados, VMs clássicas e [VMs generalizadas](../virtual-machines/windows/capture-image-resource.md).<br/><br/> Se o ponto de restauração tem mais ou menos discos do que a VM atual, o número de discos no ponto de restauração reflete apenas a configuração da VM.<br><br> Também há suporte para Replace existing para VMs com recursos vinculados, como [a identidade gerenciada pelo usuário e a](../active-directory/managed-identities-azure-resources/overview.md) [Key Vault](../key-vault/general/overview.md).
**Entre regiões (região secundária)** | A restauração entre regiões pode ser usada para restaurar VMs do Azure na região secundária, que é uma [região emparelhada do Azure](../best-practices-availability-paired-regions.md#what-are-paired-regions).<br><br> Você poderá restaurar todas as VMs do Azure para o ponto de recuperação selecionado se o backup for feito na região secundária.<br><br> Esse recurso está disponível para as opções abaixo:<br> <li> [Criar uma máquina virtual](./backup-azure-arm-restore-vms.md#create-a-vm) <br> <li> [Restaurar discos](./backup-azure-arm-restore-vms.md#restore-disks) <br><br> No momento, não há suporte para a opção [Substituir os discos existentes](./backup-azure-arm-restore-vms.md#replace-existing-disks).<br><br> Permissões<br> A operação de restauração na região secundária pode ser executada por administradores de backup e administradores de aplicativos.

## <a name="support-for-file-level-restore"></a>Suporte para restauração no nível de arquivos

**Restaurar** | **Com suporte**
--- | ---
Restaurar arquivos entre sistemas operacionais | Você pode restaurar os arquivos de qualquer máquina que tenha o mesmo sistema operacional (ou compatível) que a VM do backup. Veja a [Tabela de sistemas operacionais compatíveis](backup-azure-restore-files-from-vm.md#step-3-os-requirements-to-successfully-run-the-script).
Restaurar arquivos de VMs criptografadas | Sem suporte.
Restaurar arquivos de contas de armazenamento restritas à rede | Sem suporte.
Restaurar arquivos em máquinas virtuais usando Espaços de Armazenamento do Windows | Não há suporte para restaurar a mesma VM.<br/><br/> Em vez disso, restaure os arquivos em uma VM compatível.
Restaurar arquivos em VM Linux usando matrizes LVM/Raid | Não há suporte para restaurar a mesma VM.<br/><br/> Restaure em uma VM compatível.
Restaurar arquivos com configurações de rede especiais | Não há suporte para restaurar a mesma VM. <br/><br/> Restaure em uma VM compatível.
Restaurar arquivos de disco compartilhado, unidade temporária, disco com eliminação de duplicação, ultra Disk e disco com acelerador de gravação habilitado | Restauração sem suporte, <br/><br/>consulte [suporte ao armazenamento de VM do Azure](#vm-storage-support).

## <a name="support-for-vm-management"></a>Suporte para gerenciamento de VM

A tabela a seguir resume o suporte para backup durante as tarefas de gerenciamento de VM, como adicionar ou substituir os discos da VM.

**Restaurar** | **Com suporte**
--- | ---
Restaure em região/assinatura/zona. | Sem suporte.
Restaurar uma VM existente | Use a opção para substituir disco.
Restaurar o disco com a conta de armazenamento habilitada com a Criptografia do Serviço de Armazenamento do Azure (SSE) | Sem suporte.<br/><br/> Restaure em uma conta que não esteja habilitado com SSE.
Restaurar as contas de armazenamento mistas |Sem suporte.<br/><br/> Com base no tipo de conta de armazenamento, todos os discos restaurados serão premium ou standard e não mistos.
Restaurar VM diretamente em um conjunto de disponibilidade | Para discos gerenciados, você pode restaurar o disco e usar a opção de conjunto de disponibilidade do modelo.<br/><br/> Sem suporte para discos não gerenciados. Para discos não gerenciados, restaure o disco e crie uma VM no conjunto de disponibilidade.
Restaurar o backup de VMs não gerenciadas após a atualização para VM gerenciada| Com suporte.<br/><br/> Você pode restaurar discos e depois criar uma VM gerenciada.
Restaurar uma VM em um ponto de restauração antes de migrar a VM para discos gerenciados | Com suporte.<br/><br/> Restaure os discos não gerenciados (padrão), converta os discos restaurados para discos gerenciados e crie uma VM com discos gerenciados.
Restaurar VM que foi excluída. | Com suporte.<br/><br/> Você pode restaurar a VM usando um ponto de recuperação.
Restaurar uma VM do controlador de domínio  | Com suporte. Para obter detalhes, consulte [restaurar VMs do controlador de domínio](backup-azure-arm-restore-vms.md#restore-domain-controller-vms).
Restaurar a VM em uma rede virtual diferente |Com suporte.<br/><br/> As rede virtual precisa estar na mesma assinatura e região.

## <a name="vm-compute-support"></a>Suporte de VM de computação

**Computação** | **Suporte**
--- | ---
Tamanho da VM |Qualquer tamanho de VM do Azure com, no mínimo, 2 núcleos de CPU e 1 GB de RAM.<br/><br/> [Saiba mais.](../virtual-machines/sizes.md)
Backup de VMs em [conjuntos de disponibilidade](../virtual-machines/availability.md#availability-sets) | Com suporte.<br/><br/> Você não pode restaurar uma VM em um conjunto disponível usando a opção para criar rapidamente uma VM. Em vez disso, ao restaurar a VM, restaure o disco e use-o para implantar uma VM ou restaure um disco e use-o para substituir um disco existente.
Fazer backup de VMs implantadas com [HUB (Benefício de uso híbrido)](../virtual-machines/windows/hybrid-use-benefit-licensing.md) | Com suporte.
Fazer backup de VMs implantadas do [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?filters=virtual-machine-images)<br/><br/> (Publicado pela Microsoft, produtos de terceiros) |Com suporte.<br/><br/> A VM também deve estar em execução em um sistema operacional com suporte.<br/><br/> Ao recuperar arquivos na VM, você pode restaurar apenas em um sistema operacional compatível (não um SO anterior ou posterior). Não restauramos as VMs do Azure Marketplace apoiadas como VMs, pois elas precisam de informações de compra. Eles são restaurados somente como discos.
Fazer backup de VMs implantadas de uma imagem personalizada (terceiros) |Com suporte.<br/><br/> A VM também deve estar em execução em um sistema operacional com suporte.<br/><br/> Ao recuperar arquivos na VM, você pode restaurar apenas em um sistema operacional compatível (não um SO anterior ou posterior).
Fazer backup de VMs migradas para o Azure| Com suporte.<br/><br/> Para fazer backup da VM, o agente da VM deve estar instalado no computador migrado.
Fazer backup de consistência de várias VMs | O backup do Azure não fornece consistência de aplicativos e dados em várias VMs.
Fazer backup com [configuração de diagnóstico](../azure-monitor/essentials/platform-logs-overview.md)  | Sem suporte. <br/><br/> Se a restauração da VM do Azure com configurações de diagnóstico for disparada usando a opção [Criar](backup-azure-arm-restore-vms.md#create-a-vm), a restauração falhará.
Restaurar VMs fixadas por zona | Com suporte (para uma VM submetida a backup depois de Jan 2019 e onde as [zonas de disponibilidade](https://azure.microsoft.com/global-infrastructure/availability-zones/) estão disponíveis).<br/><br/>Atualmente, damos suporte à restauração para a mesma zona fixada em VMs. No entanto, se a zona não estiver disponível devido a uma interrupção, a restauração falhará.
VMs Gen2 | Com suporte <br> O Backup do Azure dá suporte ao backup e à restauração de [VMs Gen2](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/). Quando essas VMs são restauradas do ponto de recuperação, elas são restauradas como [VMs Gen2](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/).
Backup de VMs do Azure com bloqueios | Sem suporte para VMs não gerenciadas. <br><br> Com suporte para VMs gerenciadas.
[VMs do Spot](../virtual-machines/spot-vms.md) | Sem suporte: As restaurações de backup do Azure identificam VMs como VMs regulares do Azure.
[Host Dedicado do Azure](../virtual-machines/dedicated-hosts.md) | Com suporte
Configuração de espaços de armazenamento do Windows de VMs autônomas do Azure | Com suporte

## <a name="vm-storage-support"></a>Suporte ao armazenamento de VM

**Componente** | **Suporte**
--- | ---
Discos de dados de VM do Azure | Suporte para backup de VMs do Azure com até 32 discos.<br><br> O suporte para backup de VMs do Azure com discos não gerenciados ou VMs clássicas é de até 16 discos.
Tamanho do disco de dados | O tamanho do disco individual pode ter até 32 TB e um máximo de 256 TB combinados para todos os discos em uma VM.
Tipo de armazenamento | HDD Standard, SSD Standard e SSD Premium.
Discos gerenciados | Com suporte.
Discos criptografados | Com suporte.<br/><br/> É possível fazer o backup das VMs do Azure habilitadas com o Azure Disk Encryption (com ou sem o aplicativo Azure AD).<br/><br/> As VMs criptografadas não podem ser recuperadas no nível de arquivo/pasta. Você precisa recuperar a VM inteira.<br/><br/> Você pode habilitar a criptografia em VMs que já estão protegidos pelo Backup do Azure.
Discos com o Acelerador de Gravação habilitado | A partir de 23 de novembro de 2020, há suporte apenas nas regiões da Coreia (KRC) e da África do Sul (SAN) para um número limitado de assinaturas. Para as assinaturas com suporte, o backup do Azure fará o backup das máquinas virtuais que têm discos com aceleração de gravação (WA) habilitados durante o backup.<br><br>Para as regiões sem suporte, a conectividade com a Internet é necessária na VM para tirar instantâneos das máquinas virtuais com o WA habilitado.<br><br> **Observação importante**: nessas regiões sem suporte, as máquinas virtuais com discos de WA precisam de conectividade com a Internet para um backup bem-sucedido (embora esses discos sejam excluídos do backup).
Fazer backup e restaurar VMs/discos com eliminação de duplicação | O backup do Azure não dá suporte à eliminação de duplicação. Para obter mais informações, confira este [artigo](./backup-support-matrix.md#disk-deduplication-support) <br/> <br/>  -O backup do Azure não elimina a duplicação entre VMs no cofre dos serviços de recuperação <br/> <br/>  -Se houver VMs no estado de eliminação de duplicação durante a restauração, os arquivos não poderão ser restaurados porque o cofre não entende o formato. No entanto, você pode executar com êxito a restauração completa da VM.
Adicionar o disco à VM protegida | Com suporte.
Redimensionar o disco em uma VM protegida | Com suporte.
Armazenamento compartilhado| Não há suporte para o backup de VMs usando Volume Compartilhado Clusterizado (CSV) ou Scale-Out servidor de arquivos. Os gravadores de CSV provavelmente falharão durante o backup. Na restauração, os discos que contêm volumes CSV podem não aparecer.
[Discos compartilhados](../virtual-machines/disks-shared-enable.md) | Sem suporte.
Discos SSD Ultra | Sem suporte. Para obter mais informações, consulte essas [limitações](selective-disk-backup-restore.md#limitations).
[Discos temporários](../virtual-machines/managed-disks-overview.md#temporary-disk) | Os discos temporários não são submetidos a backup pelo backup do Azure.
Discos NVMe/efêmeras | Sem suporte.

## <a name="vm-network-support"></a>Suporte de rede de VM

**Componente** | **Suporte**
--- | ---
Número de NICs (adaptadores de rede) | Até o número máximo de NICs com suporte em um tamanho específico de VM do Azure.<br/><br/> As NICs são criadas quando a VM é criada durante o processo de restauração.<br/><br/> O número de NICs na VM restaurada espelha o número de NICs na VM quando você habilitou a proteção. A remoção de NICs depois de habilitar a proteção não afeta a contagem.
Balanceador de carga interno/externo |Com suporte. <br/><br/> [Saiba mais](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) sobre a restauração de VMs com configurações de rede especiais.
Múltiplos endereços IP reservados |Com suporte. <br/><br/> [Saiba mais](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) sobre a restauração de VMs com configurações de rede especiais.
VMs com múltiplos adaptadores de rede| Com suporte. <br/><br/> [Saiba mais](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) sobre a restauração de VMs com configurações de rede especiais.
VMs com endereços IP públicos| Com suporte.<br/><br/> Associe um endereço IP público existente à NIC ou crie um endereço e associe-o à NIC após executar a restauração.
NSG (Grupo de Segurança de Rede) na NIC/sub-rede. |Com suporte.
Endereço IP estático | Sem suporte.<br/><br/> Uma nova VM criada de um ponto de restauração recebe um endereço IP dinâmico.<br/><br/> Para VMs clássicas, você não pode fazer backup de uma VM com um endereço IP reservado e sem ponto de extremidade definido.
Endereço IP dinâmico |Com suporte.<br/><br/> Se a NIC na VM de origem usa o endereçamento IP dinâmico, por padrão, a NIC na VM restaurada também usará.
Gerenciador de Tráfego do Azure| Com suporte.<br/><br/>Se a VM de backup estiver no Gerenciador de Tráfego, adicione manualmente a VM restaurada À mesma instância do Gerenciador de Tráfego.
DNS do Azure |Com suporte.
DNS Personalizado |Com suporte.
Conectividade de saída por meio do proxy HTTP | Com suporte.<br/><br/> Não há suporte para proxy autenticado.
Pontos de extremidade de serviço de rede virtual| Com suporte.<br/><br/> As configurações de conta de armazenamento de rede virtual e firewall devem permitir o acesso de todas as redes.

## <a name="vm-security-and-encryption-support"></a>Suporte à segurança e criptografia da VM

O Backup do Azure dá suporte à criptografia para dados em trânsito e em repouso:

Tráfego de rede para o Azure:

- O tráfego de backup dos servidores para o cofre dos Serviços de Recuperação é criptografado usando a criptografia AES 256.
- Os dados de backup são enviados por um link HTTPS seguro.
- Os dados de backup são armazenados no cofre dos Serviços de Recuperação na forma criptografada.
- Somente você tem a chave de criptografia para desbloquear esses dados. A Microsoft não pode descriptografar os dados de backup em momento algum.

  > [!WARNING]
  > Depois de configurar o cofre, só você terá acesso à chave de criptografia. A Microsoft nunca mantém uma cópia e não tem acesso à chave. Se a chave for perdida, a Microsoft não poderá recuperar os dados de backup.

Segurança dos dados:

- Ao fazer backup de VMs do Azure, você precisa configurar a criptografia *dentro* da máquina virtual.
- O Backup do Azure dá suporte a Azure Disk Encryption, que usa o BitLocker em máquinas virtuais do Windows e usa **dm-crypt** em máquinas virtuais do Linux.
- No back-end, o Backup do Azure usa [criptografia do Serviço de Armazenamento do Azure](../storage/common/storage-service-encryption.md), que protege dados em repouso.

**Computador** | **Em trânsito** | **Em repouso**
--- | --- | ---
Computadores Windows locais sem o DPM/MABS | ![Sim][green] | ![Sim][green]
VMs do Azure | ![Sim][green] | ![Sim][green]
Locais/VMs do Azure com o DPM | ![Sim][green] | ![Sim][green]
Locais/VMs do Azure com o MABS | ![Sim][green] | ![Sim][green]

## <a name="vm-compression-support"></a>Suporte à compactação de VM

O backup dá suporte à compactação do tráfego de backup, conforme resumido na tabela a seguir. Observe o seguinte:

- Para VMs do Azure, a extensão de VM lê os dados diretamente da conta de armazenamento do Azure na rede de armazenamento. Não é necessário compactar esse tráfego.
- Se estiver usando o DPM ou o MABS, economize largura de banda compactando os dados antes deles serem copiados em backup no DPM/MABS.

**Computador** | **Compactar no DPM/MABS (TCP)** | **Compactar no cofre (HTTPS)**
--- | --- | ---
Computadores Windows locais sem o DPM/MABS | NA | ![Sim][green]
VMs do Azure | NA | NA
Locais/VMs do Azure com o DPM | ![Sim][green] | ![Sim][green]
Locais/VMs do Azure com o MABS | ![Sim][green] | ![Sim][green]

## <a name="next-steps"></a>Próximas etapas

- [Fazer backup de VMs do Azure](backup-azure-arm-vms-prepare.md).
- [Fazer backup direto de computadores Windows](tutorial-backup-windows-server-to-azure.md), sem um servidor de backup.
- [Configurar o MABS](backup-azure-microsoft-azure-backup.md) para backup no Azure e, em seguida, fazer backup de cargas de trabalho no MABS.
- [Configurar o DPM](backup-azure-dpm-introduction.md) para backup no Azure e, em seguida, fazer backup de cargas de trabalho no DPM.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png