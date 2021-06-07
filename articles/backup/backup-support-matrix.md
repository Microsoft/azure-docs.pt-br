---
title: Matriz de suporte do Backup do Azure
description: Fornece um resumo de configurações compatíveis e limitações do serviço de Backup do Azure.
ms.topic: conceptual
ms.date: 02/17/2019
ms.custom: references_regions
ms.openlocfilehash: 2877b0085f31f39849e04678d5e0ddd65e3888e2
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105564459"
---
# <a name="support-matrix-for-azure-backup"></a>Matriz de suporte Backup do Azure

Use o [Backup do Azure](backup-overview.md) para fazer backup de dados na plataforma de nuvem do Microsoft Azure. Este artigo resume as configurações e limitações de suporte gerais para cenários e implantações do Backup do Azure.

Outras matrizes de suporte estão disponíveis:

- Matriz de suporte para o [backup de máquina virtual (VM) do Azure](backup-support-matrix-iaas.md)
- Matriz de suporte para backup usando [System Center Data Protection Manager (DPM)/Servidor de Backup do Microsoft Azure (MABS)](backup-support-matrix-mabs-dpm.md)
- Matriz de suporte para backup usando [Serviços de Recuperação do Microsoft Azure (MARS)](backup-support-matrix-mars-agent.md)

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="vault-support"></a>Suporte de cofre

O backup do Azure usa cofres dos serviços de recuperação para orquestrar e gerenciar backups para os seguintes tipos de carga de trabalho – VMs do Azure, SQL em VMs do Azure, SAP HANA em VMs do Azure, compartilhamentos de arquivos do Azure e cargas de trabalho locais usando o agente de backup do Azure, Servidor de Backup do Azure e System Center DPM. Ele também usa cofres dos serviços de recuperação para armazenar dados de backup para essas cargas de trabalho.

A tabela a seguir descreve os recursos dos cofres de Serviços de Recuperação:

**Recurso** | **Detalhes**
--- | ---
**Cofres na assinatura** | Até 500 cofres dos Serviços de Recuperação em uma assinatura única.
**Computadores em um cofre** | Até 2000 fontes de origem em todas as cargas de trabalho (como VMs do Azure, SQL Server VM, servidores MABS e assim por diante) podem ser protegidas em um único cofre.<br><br>Até 1.000 VMs do Azure em um único cofre.<br/><br/> Até 50 servidores MABS podem ser registrados em um único cofre.
**Fontes de dados** | O tamanho máximo de uma [fonte de dados](./backup-azure-backup-faq.md#how-is-the-data-source-size-determined) individual é de 54.400 GB. Esse limite não se aplica aos backups de VM do Azure. Nenhum limite se aplica à quantidade total de dados sobre os quais você pode fazer backup para o cofre.
**Backups no cofre** | **VMs do Azure:** Uma vez por dia.<br/><br/>**Computadores protegidos por DPM/MABS:** Duas vezes por dia.<br/><br/> **Backup direto de computadores usando o agente MARS:** Três vezes por dia.
**Backups entre cofres** | O backup é dentro de uma região.<br/><br/> Você precisa de um cofre em cada região do Azure que contenha VMs que você deseja fazer backup. Você não pode fazer backup em uma região diferente.
**Movimentação de cofres** | Você pode [mover cofres](./backup-azure-move-recovery-services-vault.md) entre assinaturas ou entre grupos de recursos na mesma assinatura. No entanto, não há suporte para a movimentação de cofres entre regiões.
**Mover dados entre cofres** | Não há suporte para a movimentação de dados de backup entre cofres.
**Modificar o tipo de armazenamento do cofre** | Você pode modificar o tipo de replicação de armazenamento (armazenamento com redundância geográfica ou armazenamento com redundância local) para um cofre antes que os backups sejam armazenados. Após o início dos backups no cofre, o tipo de replicação não poderá ser modificado.
**ZRS (armazenamento com redundância de zona)** | Disponível nas regiões Sul do Reino Unido (UKS) e Sul Ásia Oriental (SEA).
**Pontos de extremidade privados** | Consulte [esta seção](./private-endpoints.md#before-you-start) para obter os requisitos para criar pontos de extremidade privados para um cofre de serviço de recuperação.  

## <a name="on-premises-backup-support"></a>Suporte de backup local

Veja a seguir os itens compatíveis, caso deseje fazer backup de computadores locais:

**Computador** | **O que é armazenado em backup** | **Localidade** | **Recursos**
--- | --- | --- | ---
**Backup direto de computador Windows com o agente MARS** | Arquivos, pastas, estado do sistema | Fazer backup no cofre dos Serviços de Recuperação. | Backup três vezes por dia<br/><br/> Sem backup com reconhecimento de aplicativo<br/><br/> Restaurar arquivo, pasta e volume
**Backup direto de computador Linux com o agente MARS** | Sem suporte para backup
**Fazer backup no DPM** | Arquivos, pastas, volumes, estado do sistema e dados de aplicativo | Fazer backup em armazenamento local do DPM. Em seguida, o DPM faz backup no cofre. | Instantâneos com reconhecimento de aplicativo<br/><br/> Granularidade completa de backup e recuperação<br/><br/> Com suporte do Linux para VMs (Hyper-V/VMware)<br/><br/> Sem suporte para o Oracle
**Fazer backup para o MABS** | Arquivos, pastas, volumes, estado do sistema e dados de aplicativo | Fazer backup no armazenamento local do MABS. Em seguida, o MABS faz backup no cofre. | Instantâneos com reconhecimento de aplicativo<br/><br/> Granularidade completa de backup e recuperação<br/><br/> Com suporte do Linux para VMs (Hyper-V/VMware)<br/><br/> Sem suporte para o Oracle

## <a name="azure-vm-backup-support"></a>Suporte de backup de VM do Azure

### <a name="azure-vm-limits"></a>Limites de VM do Azure

**Limite** | **Detalhes**
--- | ---
**Discos de dados de VM do Azure** | Veja a [matriz de suporte para backup de VM do Azure](./backup-support-matrix-iaas.md#vm-storage-support).
**Tamanho do disco de dados de VM do Azure** | O tamanho do disco individual pode ter até 32 TB e um máximo de 256 TB combinados para todos os discos em uma VM.

### <a name="azure-vm-backup-options"></a>Opções de backup de VM do Azure

Veja a seguir os itens compatíveis, caso deseje fazer backup de VMs do Azure:

**Computador** | **O que é armazenado em backup** | **Localidade** | **Recursos**
--- | --- | --- | ---
**Backup de VM do Azure usando a extensão de VM** | Toda a VM | Fazer backup para o cofre. | Extensão instalada quando você habilita o backup de uma VM.<br/><br/> Backup uma vez por dia.<br/><br/> Backup com reconhecimento de aplicativo para VMs do Windows; backup consistente com arquivo para VMs do Linux. Configure a consistência com aplicativo para computadores Linux usando scripts personalizados.<br/><br/> Restaure a VM ou disco.<br/><br/>Há suporte para [backup e restauração de Active Directory controladores de domínio](active-directory-backup-restore.md) .<br><br> Não é possível fazer backup de uma VM do Azure em uma localização local.
**Backup de VM do Azure usando o agente MARS** | Arquivos, pastas, estado do sistema | Fazer backup para o cofre. | Backup três vezes por dia.<br/><br/> O agente MARS poderá ser executado juntamente com a extensão de VM se você quiser fazer backup de arquivos/pastas específicos em vez de toda a VM.
**VM do Azure com o DPM** | Arquivos, pastas, volumes, estado do sistema e dados de aplicativo | Fazer backup em armazenamento local da VM do Azure que está executando o DPM. Em seguida, o DPM faz backup no cofre. | Instantâneos com reconhecimento de aplicativo.<br/><br/> Granularidade completa de backup e recuperação.<br/><br/> Com suporte do Linux para VMs (Hyper-V/VMware).<br/><br/> Sem suporte para o Oracle.
**VM do Azure com o MABS** | Arquivos, pastas, volumes, estado do sistema e dados de aplicativo | Fazer backup em armazenamento local da VM do Azure que está executando o MABS. Em seguida, o MABS faz backup no cofre. | Instantâneos com reconhecimento de aplicativo.<br/><br/> Granularidade completa de backup e recuperação.<br/><br/> Com suporte do Linux para VMs (Hyper-V/VMware).<br/><br/> Sem suporte para o Oracle.

## <a name="linux-backup-support"></a>Suporte de backup do Linux

Veja a seguir os itens compatíveis, caso deseje fazer backup de computadores Linux:

**Tipo de backup** | **Linux (endossado pelo Azure)**
--- | ---
**Backup direto de computador local que está executando o Linux** | Sem suporte. O agente do MARS só pode ser instalado em computadores Windows.
**Uso da extensão do agente para fazer backup de VM do Azure que está executando Linux** | Backup consistente com aplicativo usando [scripts personalizados](backup-azure-linux-app-consistent.md).<br/><br/> Recuperação em nível de arquivo.<br/><br/> Faça a restauração com a criação de uma VM de um ponto de recuperação ou um disco.
**Uso do DPM para fazer backup de computadores locais executando Linux** | Backup consistente com o arquivo de VMs de convidado do Linux no Hyper-V e no VMware.<br/><br/> Restauração de VM de VMs do Hyper-V e do VMware Linux convidadas.
**Uso do MABS para fazer backup de computadores locais executando Linux** | Backup consistente com o arquivo de VMs de convidado do Linux no Hyper-V e no VMware.<br/><br/> Restauração de VM de VMs do Hyper-V e do VMware Linux convidadas.
**Uso do MABS ou DPM para fazer backup de VMs do Azure no Linux** | Sem suporte.

## <a name="daylight-saving-time-support"></a>Suporte de horário de verão

O Backup do Azure não dá suporte ao ajuste automático do relógio para alterações de horário de verão para backups de VMs do Azure. Ele não muda a hora do backup para frente ou para trás. Para garantir que o backup seja executado na hora desejada, modifique as políticas de backup manualmente, conforme necessário.

## <a name="disk-deduplication-support"></a>Suporte de eliminação de duplicação de disco

O suporte de eliminação de duplicação de disco ocorre da seguinte maneira:

- Há suporte local para a eliminação de duplicação de disco quando o DPM ou o MABS é usado para fazer backup de VMs do Hyper-V que estão executando o Windows. O Windows Server faz a eliminação de duplicação de dados (em nível do host) em VHDs (discos rígidos virtuais) conectados como armazenamento de backup à VM.
- Não há suporte para a eliminação de duplicação no Azure para qualquer componente de Backup. Quando o DPM e o MABS são implantados no Azure, os discos de armazenamento anexados à VM não podem ser submetidos à eliminação de duplicação.

## <a name="security-and-encryption-support"></a>Suporte à segurança e criptografia

O Backup do Azure dá suporte à criptografia para dados em trânsito e em repouso.

### <a name="network-traffic-to-azure"></a>Tráfego de rede para o Azure

- O tráfego de backup dos servidores para o cofre dos Serviços de Recuperação é criptografado usando a criptografia AES 256.
- Os dados de backup são enviados por um link HTTPS seguro.

### <a name="data-security"></a>Segurança de dados

- Os dados de backup são armazenados no cofre dos Serviços de Recuperação na forma criptografada.
- Quando é feito backup dos dados de servidores locais com o agente MARS, os dados são criptografados com uma senha antes do upload para o backup do Azure e descriptografados somente após serem baixados do backup do Azure.
- É necessário configurar a criptografia *dentro* da máquina virtual para o backup de VMs do Azure.
- O Backup do Azure dá suporte a Azure Disk Encryption, que usa o BitLocker em máquinas virtuais Windows e **dm-crypt** em máquinas virtuais Linux.
- No back-end, o Backup do Azure usa [Criptografia do Serviço de Armazenamento do Azure](../storage/common/storage-service-encryption.md), que protege dados em repouso.

**Computador** | **Em trânsito** | **Em repouso**
--- | --- | ---
**Computadores Windows locais sem o DPM/MABS** | ![Sim][green] | ![Sim][green]
**VMs do Azure** | ![Sim][green] | ![Sim][green]
**Máquinas Windows locais ou VMs do Azure com o DPM** | ![Sim][green] | ![Sim][green]
**Máquinas Windows locais ou VMs do Azure com o MABS** | ![Sim][green] | ![Sim][green]

## <a name="compression-support"></a>Suporte à compactação

O backup dá suporte à compactação do tráfego de backup, conforme resumido na tabela a seguir.

- Para VMs do Azure, a extensão de VM lê os dados diretamente da conta de armazenamento do Azure na rede de armazenamento; portanto, não é necessário compactar esse tráfego.
- Se estiver usando o DPM ou o MABS, economize largura de banda compactando os dados antes deles serem copiados em backup.

**Computador** | **Compactar no DPM/MABS (TCP)** | **Compactar no cofre (HTTPS)**
--- | --- | ---
**Backup direto em computadores locais do Windows** | NA | ![Sim][green]
**Backup de VMs do Azure usando a extensão de VM** | NA | NA
**Backup em computadores locais/Azure usando o MABS/DPM** | ![Sim][green] | ![Sim][green]

## <a name="retention-limits"></a>Limites de retenção

**Configuração** | **Limites**
--- | ---
**Pontos de recuperação máximos por instância protegida (máquina ou carga de trabalho)** | 9\.999
**Tempo máximo de expiração para um ponto de recuperação** | Sem limite
**Frequência máxima de backup para o DPM/MABS** | A cada 15 minutos para o SQL Server<br/><br/> Uma vez por hora para outras cargas de trabalho
**Frequência máxima de backup para o cofre** | **Computadores Windows locais ou VMs do Azure executando MARS:** Três por dia<br/><br/> **DPM/MABS:** Dois por dia<br/><br/> **Backup de VM do Azure:** Um por dia
**Retenção do ponto de recuperação** | Diária, semanal, mensal, anual
**Período de retenção máximo** | Depende da frequência de backup
**Pontos de recuperação no disco do DPM/MABS** | 64 para servidores de arquivos, 448 para servidores de aplicativos <br/><br/>Pontos de recuperação de fita ilimitados para o DPM local

## <a name="cross-region-restore"></a>Restauração Entre Regiões

O backup do Azure adicionou o recurso de restauração entre regiões para reforçar a disponibilidade de dados e a capacidade de resiliência, dando a você controle total para restaurar dados em uma região secundária. Para configurar esse recurso, veja [o artigo Definição de Restauração Entre Regiões.](backup-create-rs-vault.md#set-cross-region-restore). Esse recurso é compatível com os seguintes tipos de gerenciamento:

| Tipo de Gerenciamento de Backup | Com suporte                                                    | Regiões com Suporte |
| ---------------------- | ------------------------------------------------------------ | ----------------- |
| VM do Azure               | Com suporte para VMs do Azure com discos gerenciados e não gerenciados. Não compatível com VMs clássicas. | Disponível em todas as regiões públicas do Azure e regiões soberanas, exceto para a França central, Austrália Central, norte da África do Sul, Norte dos EAU, Norte da Suíça, Centro-oeste da Alemanha, leste da Noruega. <br>Para obter informações sobre como usar nessas regiões, entre em contato com [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) |
| SQL/SAP HANA | Em versão prévia                                                      | Disponível em todas as regiões públicas do Azure e regiões soberanas, exceto para a França central, Austrália Central, norte da África do Sul, Norte dos EAU, Norte da Suíça, Centro-oeste da Alemanha, leste da Noruega. <br>Para obter informações sobre como usar nessas regiões, entre em contato com [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) |
| Agente do MARS/Local  | Não                                                           | N/D               |
| AFS (compartilhamentos de arquivos do Azure)                 | Não                                                           | N/D               |

## <a name="next-steps"></a>Próximas etapas

- [Examinar matriz de suporte](backup-support-matrix-iaas.md) para backup de VM do Azure.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png