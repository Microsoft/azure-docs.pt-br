---
title: Matriz de suporte de Backup do Azure para backup de VM do Azure
description: Fornece um resumo de configurações compatíveis e limitações ao fazer backup de VMs do Azure com o serviço de Backup do Azure.
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: raynew
ms.openlocfilehash: 3b979b6bcf2078e83564a8f008d392fd8e0a7c78
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68464898"
---
# <a name="support-matrix-for-azure-vm-backup"></a>Matriz de suporte para backup de VM do Azure
Você pode usar o [serviço de backup do Azure](backup-overview.md) para fazer backup de máquinas locais e cargas de trabalho e VMs (máquinas virtuais) do Azure. Este artigo resume as configurações de suporte e as limitações ao fazer backup de VMs do Azure com o backup do Azure.

Outras matrizes de suporte:

- [Matriz de suporte geral](backup-support-matrix.md) para o backup do Azure
- [Matriz de suporte](backup-support-matrix-mabs-dpm.md) para o servidor de backup do Azure/backup do System Center Data Protection Manager (DPM)
- [Matriz de suporte](backup-support-matrix-mars-agent.md) para backup com o agente de serviços de recuperação do Microsoft Azure (MARS)

## <a name="supported-scenarios"></a>Cenários com suporte

Veja como fazer backup e restaurar as VMs do Azure com o serviço de Backup do Azure.

**Cenário** | **Backup** | **Agente** |**Restore**
--- | --- | --- | ---
Backup direto de VMs do Azure  | Faça backup de toda a VM.  | Nenhum agente é necessário na VM do Azure. O backup do Azure é instalado e usa uma extensão para o [agente de VM do Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) que está em execução na VM. | Restaure da seguinte maneira:<br/><br/> - **Crie uma VM básica**. Isso será útil se a VM não tiver nenhuma configuração especial, como vários endereços IP.<br/><br/> - **Restaure o disco da VM**. Restaure o disco. Em seguida, anexe-o a uma VM existente ou crie uma nova VM do disco usando o PowerShell.<br/><br/> - **Substitua o disco da VM**. Se existir uma VM que usa discos gerenciados (não criptografados), você pode restaurar um disco e usá-lo para substituir um disco existente na VM.<br/><br/> - **Restaure arquivos/pastas específicas**. Você pode restaurar arquivos/pastas de uma VM em vez de uma VM inteira.
Backup direto de VMs do Azure (somente Windows)  | Fazer backup de arquivos/pastas/volumes específicos. | Instale o [agente dos serviços de recuperação do Azure](backup-azure-file-folder-backup-faq.md).<br/><br/> Você pode executar o agente do MARS com a extensão de backup para o agente de VM do Azure para fazer backup da VM no nível de arquivo/pasta. | Restaure arquivos/pastas específicos.
Fazer backup da VM do Azure no servidor de backup  | Fazer backup de arquivos/pastas/volumes; arquivos de estado do sistema/bare-metal; dados de aplicativo para o System Center DPM ou para Backup do Microsoft Azure Server (MABS).<br/><br/> O DPM/MABS faz backup para o cofre de backup. | Instale o agente de proteção do DPM/MABS na VM. O agente de MARS é instalado no DPM/MABS.| Restaure arquivos/pastas/volumes; arquivos de estado do sistema/bare metal; dados de aplicativos.

Saiba mais sobre [o backup usando um servidor de backup](backup-architecture.md#architecture-back-up-to-dpmmabs) e sobre [os requisitos de suporte](backup-support-matrix-mabs-dpm.md).

## <a name="supported-backup-actions"></a>Ações de backup compatíveis

**Ação** | **Suporte**
--- | ---
Habilitar o backup ao criar uma VM do Windows Azure | Com suporte para: <br/><br/> -Windows Server 2019 (datacenter/Data Center Core/Standard) <br/><br/> -Windows Server 2016 (datacenter/Data Center Core/Standard) <br/><br/> -Windows Server 2012 R2 (datacenter/padrão) <br/><br/> -Windows Server 2008 R2 (RTM e SP1 Standard)
Habilitar backup ao criar uma VM do Linux | Com suporte para:<br/><br/> - Servidor Ubuntu: 18, 4, 17,10, 17, 4, 16, 4 (LTS), 14, 4 (LTS)<br/><br/> - Red Hat: RHEL 6.7, 6.8, 6.9, 7.2, 7.3, 7.4<br/><br/> - SUSE Linux Enterprise Server: 11 SP4, 12 SP2, 12 SP3, 15 <br/><br/> - Debian: 8, 9<br/><br/> - CentOS: 6.9, 7.3<br/><br/> -Oracle Linux: 6.7, 6.8, 6.9, 7.2, 7.3
Fazer backup de uma VM que está desligada/VM offline | Com suporte.<br/><br/> O instantâneo só é consistente com a falha e não com o aplicativo.
Fazer backup de discos após a migração para o Managed disks | Com suporte.<br/><br/> O backup continuará a funcionar. Nenhuma ação é necessária.
Backup de discos gerenciados depois de habilitar o bloqueio de grupo de recursos | Não compatível.<br/><br/> O backup do Azure não pode excluir os pontos de restauração mais antigos, e os backups começarão a falhar quando o limite máximo de pontos de restauração for atingido.
Modificar a política de backup de uma VM | Com suporte.<br/><br/> O backup da VM será feito usando as configurações de agendamento e retenção em nova política. Se as configurações de retenção forem estendidas, os pontos de recuperação existentes serão marcados e mantidos. Se eles forem reduzidos, os pontos de recuperação existentes serão removidos no próximo trabalho de limpeza e eventualmente excluídos.
Cancelar um trabalho de backup | Tem suporte durante o processo de instantâneo.<br/><br/> Não tem suporte quando o instantâneo está em transferência para o cofre.
Backup da VM para uma região ou uma assinatura diferentes |  Não compatível.
Backups por dia (com a extensão de VM do Azure) | Um backup agendado por dia.<br/><br/> Você pode fazer até quatro backups sob demanda por dia.
Backups por dia (por meio do agente MARS) | Três backups agendados por dia.
Backups por dia (por meio do DPM/MABS) | Dois backups agendados por dia.
Backup mensal/anual   | Não tem suporte durante o backup com a extensão de VM do Azure. Só há suporte para diários e semanais.<br/><br/> É possível configurar a política para manter backups diários/semanais por um período de retenção mensal/anual.
Ajuste automático de relógio | Não compatível.<br/><br/> O backup do Azure não é ajustado automaticamente para alterações de horário de verão ao fazer backup de uma VM.<br/><br/>  Modifique a política manualmente, se necessário.
[Recursos de segurança para backups híbridos](https://docs.microsoft.com/azure/backup/backup-azure-security-feature) |  Não há suporte para a desabilitação de recursos de segurança.
Fazer backup da VM cuja hora da máquina foi alterada | Não compatível.<br/><br/> Se a hora da máquina for alterada para uma data/hora futura depois de habilitar o backup para essa VM; No entanto, mesmo se a alteração de tempo for revertida, o backup bem-sucedido não será garantido.  


## <a name="operating-system-support-windows"></a>Suporte do sistema operacional (Windows)

A tabela a seguir resume os sistemas operacionais com suporte ao fazer backup de VMs do Windows Azure.

**Cenário** | **Suporte SO**
--- | ---
Fazer backup com a extensão do agente de VM do Azure | Windows Client: Sem suporte<br/><br/>-Windows Server 2019 (datacenter/Data Center Core/Standard) <br/><br/> -Windows Server 2016 (datacenter/Data Center Core/Standard) <br/><br/> -Windows Server 2012 R2 (datacenter/padrão) <br/><br/> -Windows Server 2008 R2 (RTM e SP1 Standard)
Backup com o agente MARS | Sistemas operacionais com [suporte](backup-support-matrix-mars-agent.md#support-for-direct-backups).
Fazer backup com o DPM/MABS | Sistemas operacionais compatíveis para backup com [MABS](backup-mabs-protection-matrix.md) e [DPM](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807).

## <a name="support-for-linux-backup"></a>Suporte de backup do Linux

Veja a seguir os itens compatíveis, caso deseje fazer backup de computadores Linux.

**Ação** | **Suporte**
--- | ---
Backup de VMs do Azure no Linux com o agente de VM do Azure para Linux | Backup consistente de arquivos.<br/><br/> Backup consistente com aplicativo usando [scripts personalizados](backup-azure-linux-app-consistent.md).<br/><br/> Durante a restauração, você pode criar uma nova VM, restaurar um disco e usá-lo para criar uma VM ou restaurar um disco e usá-lo para substituir um disco em uma VM existente. Você também pode restaurar arquivos e pastas individuais.
Backup de VMs do Azure no Linux com o agente MARS | Não compatível.<br/><br/> O agente do MARS só pode ser instalado em computadores Windows.
Backup de VMs do Azure no Linux com DPM/MABS | Não compatível.

## <a name="operating-system-support-linux"></a>Suporte do sistema operacional (Linux)

Para backups do Linux na VM do Azure, o Backup do Azure oferece suporte para a [lista de distribuições do Linux endossadas pelo Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros). Observe o seguinte:

- O Backup do Azure não dá suporte ao principal sistema operacional Linux.
- O Backup do Azure não dá suporte a sistemas operacionais de 32 bits.
- Outras distribuições do Linux traga seu próprio trabalho podem funcionar contanto que o [agente de VM do Azure para Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) esteja disponível na VM e, desde que o Python tenha suporte.
- O backup do Azure não oferece suporte a uma VM do Linux configurada por proxy se não tiver a versão 2,7 do Python instalada.


## <a name="backup-frequency-and-retention"></a>Frequência e retenção de backup

**Configuração** | **Limites**
--- | ---
Pontos de recuperação máximos por instância protegida (máquina/carga de trabalho) | 9999.
Tempo máximo de expiração para um ponto de recuperação | Sem limite.
Frequência máxima de backup para o cofre (extensão da VM do Azure) | Uma vez por dia.
Frequência máxima de backup para o cofre (Agente MARS) | Três backups por dia.
Frequência máxima de backup para o DPM/MABS | A cada 15 minutos para SQL Server.<br/><br/> Uma vez por hora para outras cargas de trabalho.
Retenção do ponto de recuperação | Diário, semanal, mensal e anual.
Período de retenção máximo | Depende da frequência de backup.
Pontos de recuperação no disco do DPM/MABS | 64 para servidores de arquivos e 448 para servidores de aplicativos.<br/><br/> Os pontos de recuperação de fita são ilimitados para o DPM local.

## <a name="supported-restore-methods"></a>Métodos de restauração compatíveis

**Método de restauração** | **Detalhes**
--- | ---
Criar uma nova VM | Você pode criar uma VM durante o processo de restauração. <br/><br/> Essa opção proporciona uma VM básica em funcionamento. Você pode especificar o nome da VM, grupo de recursos, rede virtual, sub-rede e armazenamento.  
Restaurar um disco | Você pode restaurar um disco e usá-lo para criar uma VM.<br/><br/> Ao selecionar essa opção, o Backup do Azure copia dados do cofre para a conta de armazenamento que você selecionar. O trabalho de restauração gera um modelo. Você pode baixar esse modelo, usá-lo para especificar configurações de VM personalizadas e criar uma VM.<br/><br/> Essa opção permite especificar mais configurações que a opção anterior para criar uma VM.<br/><br/>
Substituir um disco existente | Você pode restaurar um disco e usá-lo para substituir um disco que esteja atualmente em uma máquina virtual.
Restaurar arquivos | É possível recuperar arquivos de um ponto de recuperação selecionado. Baixe um script para montar o disco da VM a partir do ponto de recuperação. Em seguida, navegue pelos volumes de disco para encontrar os arquivos/pastas que você deseja recuperar e desmonte o disco quando terminar.

## <a name="support-for-file-level-restore"></a>Suporte para restauração no nível de arquivos

**Restore** | **Com suporte**
--- | ---
Restaurar arquivos entre sistemas operacionais | Você pode restaurar os arquivos de qualquer máquina que tenha o mesmo sistema operacional (ou compatível) que a VM do backup. Consulte a [tabela de so compatível](backup-azure-restore-files-from-vm.md#system-requirements).
Restaurar arquivos em VMs clássicas | Não compatível.
Restaurar arquivos de VMs criptografadas | Não compatível.
Restaurar arquivos de contas de armazenamento restritas à rede | Não compatível.
Restaurar arquivos em máquinas virtuais usando Espaços de Armazenamento do Windows | Não há suporte para restaurar a mesma VM.<br/><br/> Em vez disso, restaure os arquivos em uma VM compatível.
Restaurar arquivos em VM Linux usando matrizes LVM/Raid | Não há suporte para restaurar a mesma VM.<br/><br/> Restaure em uma VM compatível.
Restaurar arquivos com configurações de rede especiais | Não há suporte para restaurar a mesma VM. <br/><br/> Restaure em uma VM compatível.

## <a name="support-for-vm-management"></a>Suporte para gerenciamento de VM

A tabela a seguir resume o suporte para backup durante as tarefas de gerenciamento de VM, como adicionar ou substituir discos de VM.

**Restore** | **Com suporte**
--- | ---
Restaure em região/assinatura/zona. | Não compatível.
Restaurar uma VM existente | Use a opção para substituir disco.
Restaurar o disco com a conta de armazenamento habilitada com a Criptografia do Serviço de Armazenamento do Azure (SSE) | Não compatível.<br/><br/> Restaure em uma conta que não esteja habilitado com SSE.
Restaurar as contas de armazenamento mistas | Não compatível.<br/><br/> Com base no tipo de conta de armazenamento, todos os discos restaurados serão premium ou standard e não mistos.
Restaurar para a conta de armazenamento usando o ZRS (armazenamento com redundância de zona) | Com suporte (para a VM que é submetida a backup depois de Jan 2019 e onde a [zona de disponibilidade](https://azure.microsoft.com/global-infrastructure/availability-zones/) está disponível)
Restaurar VM diretamente em um conjunto de disponibilidade | Para discos gerenciados, você pode restaurar o disco e usar a opção conjunto de disponibilidade no modelo.<br/><br/> Sem suporte para discos não gerenciados. Para discos não gerenciados, restaure o disco e crie uma VM no conjunto de disponibilidade.
Restaurar o backup de VMs não gerenciadas após a atualização para a VM gerenciada| Com suporte.<br/><br/> Você pode restaurar discos e depois criar uma VM gerenciada.
Restaurar uma VM em um ponto de restauração antes de migrar a VM para discos gerenciados | Com suporte.<br/><br/> Restaure os discos não gerenciados (padrão), converta os discos restaurados para discos gerenciados e crie uma VM com discos gerenciados.
Restaurar VM que foi excluída. | Com suporte.<br/><br/> Você pode restaurar a VM usando um ponto de recuperação.
A restauração de uma VM DC (controladora de domínio) que faça parte de uma configuração com vários DCs por meio do portal | Com suporte se você restaurar o disco e criar uma VM usando o PowerShell.
Restaurar a VM em uma rede virtual diferente |   Com suporte.<br/><br/> A rede virtual deve estar na mesma assinatura e região.

## <a name="vm-compute-support"></a>Suporte de VM de computação

**Computação** | **Suporte**
--- | ---
Tamanho da VM |   Qualquer tamanho de VM do Azure com, no mínimo, 2 núcleos de CPU e 1 GB de RAM.<br/><br/> [Saiba mais.](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)
Backup de VMs em [conjuntos de disponibilidade](https://docs.microsoft.com/azure/virtual-machine-scale-sets/availability#availability-sets) | Com suporte.<br/><br/> Não é possível restaurar uma VM em um conjunto disponível usando a opção para criar rapidamente uma VM. Em vez disso, ao restaurar a VM, restaure o disco e use-o para implantar uma VM ou restaurar um disco e usá-lo para substituir um disco existente.
Fazer backup de VMs implantadas com o [benefício de uso híbrido (Hub)](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) | Com suporte.
Fazer backup de VMs implantadas em um [conjunto](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview) de dimensionamento |  Não compatível.
Fazer backup de VMs implantadas do [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?filters=virtual-machine-images)<br/><br/> (Publicado pela Microsoft, terceiros) |  Com suporte.<br/><br/> A VM também deve estar em execução em um sistema operacional com suporte.<br/><br/> Ao recuperar arquivos na VM, você pode restaurar apenas em um sistema operacional compatível (não um SO anterior ou posterior). Não restauramos as VMs do Azure Marketplace apoiadas como VMs, pois elas precisam comprar informações, mas somente como discos.
Fazer backup de VMs implantadas de uma imagem personalizada (terceiros) |   Com suporte.<br/><br/> A VM também deve estar em execução em um sistema operacional com suporte.<br/><br/> Ao recuperar arquivos na VM, você pode restaurar apenas em um sistema operacional compatível (não um SO anterior ou posterior).
Fazer backup de VMs que são migradas para o Azure  | Com suporte.<br/><br/> Para fazer backup da VM, o agente da VM deve estar instalado no computador migrado.
Fazer backup de consistência de várias VMs | O backup do Azure não fornece consistência de aplicativos e dados em várias VMs.
Backup com [configurações de diagnóstico](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview)  | Sem suporte. <br/><br/> Se a restauração da VM do Azure com configurações de diagnóstico for disparada usando a opção [criar nova](backup-azure-arm-restore-vms.md#create-a-vm) , a restauração falhará.


## <a name="vm-storage-support"></a>Suporte ao armazenamento de VM

**Componente** | **Suporte**
--- | ---
Discos de dados de VM do Azure | Faça o backup de uma VM com 16 discos de dados ou menos. <br/><br/> Suporte a tamanhos de disco de até 4 TB.
Tamanho do disco de dados | O disco individual pode ter até 4.095 GB.<br/><br/> Se seus cofres estiverem executando a versão mais recente do backup do Azure (conhecida como restauração instantânea), haverá suporte para tamanhos de disco de até 4 TB. [Saiba mais](backup-instant-restore-capability.md).  
Tipo de armazenamento | HDD Standard, SSD padrão, SSD Premium. <br/><br/> SSD Standard terá suporte se seus cofres forem atualizados para a versão mais recente do backup de VM do Azure (conhecida como restauração instantânea). [Saiba mais](backup-instant-restore-capability.md).
Discos gerenciados | Com suporte.
Discos criptografados | Com suporte.<br/><br/> As VMs do Azure habilitadas com Azure Disk Encryption podem ser submetidas a backup (com ou sem o aplicativo do Azure AD).<br/><br/> As VMs criptografadas não podem ser recuperadas no nível de arquivo/pasta. Você deve recuperar toda a VM.<br/><br/> Você pode habilitar a criptografia em VMs que já estão protegidos pelo Backup do Azure.
Discos com o Acelerador de Gravação habilitado | Não compatível.<br/><br/> O backup do Azure exclui automaticamente os discos com Acelerador de Gravação habilitado durante o backup. Como não é feito backup, você não poderá restaurar esses discos de pontos de recuperação da VM.
Fazer backup de discos com eliminação de duplicação | Não compatível.
Adicionar o disco à VM protegida | Com suporte.
Redimensionar o disco em uma VM protegida | Com suporte.
Armazenamento compartilhado| O backup de VMs usando Volume Compartilhado Clusterizado (CSV) ou servidor de arquivos de escalabilidade horizontal não é recomendado. Os gravadores de CSV provavelmente falharão durante o backup. Na restauração, os discos que contêm volumes CSV podem não vir.



## <a name="vm-network-support"></a>Suporte de rede de VM

**Componente** | **Suporte**
--- | ---
Número de adaptadores de rede (NICs) | Até o número máximo de NICs com suporte em um tamanho específico de VM do Azure.<br/><br/> As NICs são criadas quando a VM é criada durante o processo de restauração.<br/><br/> O número de NICs na VM restaurada espelha o número de NICs na VM quando você habilitou a proteção. A remoção de NICs depois que você habilita a proteção não afeta a contagem.
Balanceador de carga interno/externo |   Com suporte. <br/><br/> [Saiba mais](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) sobre a restauração de VMs com configurações de rede especiais.
Vários endereços IP reservados |    Com suporte. <br/><br/> [Saiba mais](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) sobre a restauração de VMs com configurações de rede especiais.
VMs com múltiplos adaptadores de rede  | Com suporte. <br/><br/> [Saiba mais](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) sobre a restauração de VMs com configurações de rede especiais.
VMs com endereços IP públicos    | Com suporte.<br/><br/> Associe um endereço IP público existente à NIC ou crie um endereço e associe-o à NIC após a restauração.
NSG (Grupo de Segurança de Rede) na NIC/sub-rede. |   Com suporte.
Endereço IP Reservado (estático) | Não compatível.<br/><br/> Não é possível fazer backup de uma VM com um endereço IP reservado e nenhum ponto de extremidade definido.
Endereço IP dinâmico |    Com suporte.<br/><br/> Se a NIC na VM de origem usar o endereçamento de IP dinâmico, por padrão, a NIC na VM restaurada também a usará.
Gerenciador de Tráfego do Azure   | Com suporte.<br/><br/>Se a VM com backup estiver no Gerenciador de tráfego, adicione manualmente a VM restaurada à mesma instância do Gerenciador de tráfego.
DNS do Azure | Com suporte.
DNS Personalizado |    Com suporte.
Conectividade de saída por meio do proxy HTTP | Com suporte.<br/><br/> Não há suporte para proxy autenticado.
Pontos de extremidade de serviço de rede virtual   | Com suporte.<br/><br/> As configurações de firewall e de conta de armazenamento de rede virtual devem permitir o acesso de todas as redes.



## <a name="vm-security-and-encryption-support"></a>Segurança de VM e suporte de criptografia

O Backup do Azure dá suporte à criptografia para dados em trânsito e em repouso:

Tráfego de rede para o Azure:

- O tráfego de backup de servidores para o cofre dos serviços de recuperação é criptografado usando o criptografia AES 256.
- Os dados de backup são enviados por um link HTTPS seguro.
- Os dados de backup são armazenados no cofre dos Serviços de Recuperação na forma criptografada.
- Só você tem a senha para desbloquear esses dados. A Microsoft não pode descriptografar os dados de backup em momento algum.

  > [!WARNING]
  > Depois de configurar o cofre, somente você terá acesso à chave de criptografia. A Microsoft nunca mantém uma cópia e não tem acesso à chave. Se a chave for perdida, a Microsoft não poderá recuperar os dados de backup.

Segurança dos dados:

- Ao fazer backup de VMs do Azure, você precisa configurar a criptografia *na* máquina virtual.
- O backup do Azure dá suporte a Azure Disk Encryption, que usa o BitLocker em máquinas virtuais do Windows e **DMI-cript** em máquinas virtuais Linux.
- No back-end, o Backup do Azure usa [criptografia do Serviço de Armazenamento do Azure](../storage/common/storage-service-encryption.md), que protege dados em repouso.


**Computador** | **Em trânsito** | **Em repouso**
--- | --- | ---
Computadores Windows locais sem o DPM/MABS | ![Sim][green] | ![Sim][green]
VMs do Azure | ![Sim][green] | ![Sim][green]
Locais/VMs do Azure com o DPM | ![Sim][green] | ![Sim][green]
Locais/VMs do Azure com o MABS | ![Sim][green] | ![Sim][green]



## <a name="vm-compression-support"></a>Suporte à compactação de VM

O backup dá suporte à compactação de tráfego de backup, conforme resumido na tabela a seguir. Observe o seguinte:

- Para VMs do Azure, a extensão de VM lê os dados diretamente da conta de armazenamento do Azure pela rede de armazenamento. Não é necessário compactar esse tráfego.
- Se você estiver usando o DPM ou o MABS, poderá economizar largura de banda compactando os dados antes de fazer backup no DPM/MABS.

**Computador** | **Compactar no DPM/MABS (TCP)** | **Compactar para o cofre (HTTPS)**
--- | --- | ---
Computadores Windows locais sem o DPM/MABS | N/A | ![Sim][green]
VMs do Azure | N/A | N/A
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
