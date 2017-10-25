---
title: Perguntas frequentes do Backup do Azure | Microsoft Docs
description: "Respostas para perguntas comuns sobre: recursos de Backup do Azure incluindo cofres dos Serviços de Recuperação, do que ele pode fazer backup, como ele funciona, criptografia e limites. "
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "backup e recuperação de desastre; serviço de backup"
ms.assetid: 1011bdd6-7a64-434f-abd7-2783436668d7
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/21/2017
ms.author: markgal;arunak;trinadhk;sogup;
ms.openlocfilehash: 94b649d7949b6fbc2ec734afc955dabbfc84fc4d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="questions-about-the-azure-backup-service"></a>Perguntas sobre o serviço de Backup do Azure
Este artigo possui respostas para perguntas comuns para ajudar você a compreender rapidamente os componentes do Backup do Azure. Em algumas das respostas, há links para artigos com informações abrangentes. Você pode fazer perguntas sobre o Backup do Azure clicando em **comentários** (à direita). Os comentários aparecem na parte inferior deste artigo. Uma conta de Livefyre é necessária para o comentário. Você também pode postar perguntas sobre o serviço de Backup do Azure no [fórum de discussão](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

Para verificar rapidamente as seções neste artigo, use os links à direita, em **Neste artigo**.


## <a name="recovery-services-vault"></a>Cofre dos serviços de recuperação

### <a name="is-there-any-limit-on-the-number-of-vaults-that-can-be-created-in-each-azure-subscription-br"></a>Há algum limite para o número de cofres que podem ser criados em cada assinatura do Azure? <br/>
Sim. A partir de setembro de 2016, você pode criar 25 Serviços de Recuperação ou cofres de backup por assinatura. Crie até 25 cofres dos Serviços de Recuperação por região com suporte do Backup do Azure por assinatura. Se você precisar de cofres adicionais, crie outra assinatura.

### <a name="are-there-limits-on-the-number-of-serversmachines-that-can-be-registered-against-each-vault-br"></a>Há limites para o número de servidores/computadores que podem ser registrados em cada cofre? <br/>
Sim, você pode registrar até 50 computadores por cofre. Para máquinas virtuais IaaS do Azure, o limite é 200 VMs por cofre. Se você precisar registrar mais computadores, crie um novo cofre.

### <a name="if-my-organization-has-one-vault-how-can-i-isolate-one-servers-data-from-another-server-when-restoring-databr"></a>Se a minha organização tiver um cofre, como posso isolar dados de um servidor de outro servidor ao restaurar os dados?<br/>
Todos os servidores registrados no mesmo cofre poderão recuperar os dados do backup feito por outros servidores *que usem a mesma senha*. Se houver servidores cujos dados de backup que você deseja isolar de outros servidores em sua organização, use uma senha designada para esses servidores. Por exemplo, os servidores de recursos humanos podem usar uma senha de criptografia, os servidores de contabilidade podem usar outra senha e os outros servidores de armazenamento podem usar uma terceira senha.

### <a name="can-i-migrate-my-backup-data-or-vault-between-subscriptions-br"></a>Posso "migrar" meus dados de backup ou cofre entre assinaturas? <br/>
Não. O cofre é criado no nível da assinatura e não pode ser reatribuído a outra assinatura depois de criado.

### <a name="recovery-services-vaults-are-resource-manager-based-are-backup-vaults-classic-mode-still-supported-br"></a>Os cofres dos Serviços de Recuperação se baseiam no Resource Manager. Os cofres de Backup (modo clássico) ainda têm suporte? <br/>
Todos os cofres de Backup existentes no [Portal Clássico](https://manage.windowsazure.com) continuarão com suporte. No entanto, não é mais possível usar o Portal Clássico para implantar novos cofres de Backup. A Microsoft recomenda o uso de cofres dos Serviços de Recuperação para todas as implantações, pois aperfeiçoamentos futuros só se aplicam aos cofres dos Serviços de Recuperação. Se você tentar criar um cofre de Backup no Portal Clássico, será redirecionado ao [Portal do Azure](https://portal.azure.com).

### <a name="can-i-migrate-a-backup-vault-to-a-recovery-services-vault-br"></a>Pode migrar um cofre de Backup para um cofre dos Serviços de Recuperação? <br/>
Sim, agora você pode atualizar o cofre de Backup para um cofre dos Serviços de Recuperação. Para obter detalhes, consulte o artigo [Atualizar um cofre de Backup para um cofre dos Serviços de Recuperação](backup-azure-upgrade-backup-to-recovery-services.md).

### <a name="i-backed-up-my-classic-vms-in-a-backup-vault-can-i-migrate-my-vms-from-classic-mode-to-resource-manager-mode-and-protect-them-in-a-recovery-services-vault"></a>Fiz backup de minhas VMs clássicas em um cofre de Backup. Posso migrar minhas VMs de modo clássico para modo do Resource Manager e protegê-los em um cofre dos Serviços de Recuperação?
Os pontos de recuperação de VM em um cofre de backup não migrarão automaticamente para o cofre dos serviços de recuperação quando você migrar a VM do modo clássico para o modo do Resource Manager. Siga estas etapas para transferir seus backups de VM:

1. No cofre de Backup, vá para a guia **Itens Protegidos** e selecione a VM. Clique em [Parar Proteção](backup-azure-manage-vms-classic.md#stop-protecting-virtual-machines). Deixe a opção *Excluir dados de backup associados***desmarcada**.
2. Exclua a extensão de backup/instantâneo da VM.
3. Migre a máquina virtual do modo clássico para o modo do Gerenciador de Recursos. As informações de armazenamento e de rede correspondentes à máquina virtual também precisam ser migradas para o modo do Resource Manager.
4. Criar um cofre dos Serviços de Recuperação e configure o backup na máquina virtual migrada usando a ação **Backup** na parte superior do painel do cofre. Para obter informações detalhadas sobre como fazer backup de uma VM em um cofre dos Serviços de Recuperação, veja o artigo [Proteger VMs com um cofre dos Serviços de Recuperação](backup-azure-vms-first-look-arm.md).

## <a name="azure-backup-agent"></a>Agente de Backup do Azure
Há uma lista detalhada de perguntas nas [Perguntas Frequentes sobre o backup da pasta de arquivos do Azure](backup-azure-file-folder-backup-faq.md)

## <a name="azure-vm-backup"></a>Backup da VM do Azure
Há uma lista detalhada de perguntas nas [Perguntas Frequentes sobre o backup da VM do Azure](backup-azure-vm-backup-faq.md)

## <a name="back-up-vmware-servers"></a>Fazer backup dos servidores VMware

### <a name="can-i-back-up-vmware-vcenter-servers-to-azure"></a>Posso fazer backup de servidores do VMware vCenter para o Azure?

Sim. Você pode usar o Servidor de Backup do Azure para fazer backup do VMware vCenter e ESXi para o Azure. Para obter informações sobre a versão do VMware com suporte, confira o artigo [Matriz de proteção do Servidor de Backup do Azure](backup-mabs-protection-matrix.md). Para obter instruções passo a passo, confira [U osar Servidor de Backup do Azure para fazer backup de um servidor VMware](backup-azure-backup-server-vmware.md).


## <a name="azure-backup-server-and-system-center-data-protection-manager"></a>Servidor de Backup do Azure e System Center Data Protection Manager
### <a name="can-i-use-azure-backup-server-to-create-a-bare-metal-recovery-bmr-backup-for-a-physical-server-br"></a>Posso usar o servidor de Backup do Azure para criar um backup BMR (Recuperação Bare-Metal) para um servidor físico? <br/>
Sim.

### <a name="can-i-register-my-dpm-server-to-multiple-vaults-br"></a>Posso registrar meu Servidor DPM para diversos cofres? <br/>
Não. Um servidor DPM ou MABS pode ser registrado para apenas um cofre.

### <a name="which-version-of-system-center-data-protection-manager-is-supported-br"></a>Há suporte para qual versão do System Center Data Protection Manager? <br/>
Recomendamos a instalação do agente de Backup do Azure [mais recente](http://aka.ms/azurebackup_agent) no pacote cumulativo de atualizações (UR) mais recente para o System Center Data Protection Manager (DPM). A partir de agosto de 2016, o Pacote Cumulativo de Atualizações 11 é o mais recente.

### <a name="i-have-installed-azure-backup-agent-to-protect-my-files-and-folders-can-i-now-install-system-center-dpm-to-work-with-azure-backup-agent-to-protect-on-premises-applicationvm-workloads-to-azure-br"></a>Eu instalei o agente do Backup do Azure para proteger meus arquivos e minhas pastas. Agora posso instalar o System Center DPM para trabalhar com o agente do Backup do Azure para proteger as cargas de trabalho do aplicativo/VM local no Azure? <br/>
Para usar o Backup do Azure com o System Center DPM (Data Protection Manager), instale o DPM e, em seguida, instale o agente de Backup do Azure. Instalar os componentes de Backup do Azure nesta ordem garante que o agente de Backup do Azure funcione com o DPM. Instalar o agente de Backup do Azure antes de instalar o DPM não é aconselhável e não há suporte para isso.


## <a name="how-azure-backup-works"></a>Como funciona o Backup do Azure
### <a name="if-i-cancel-a-backup-job-once-it-has-started-is-the-transferred-backup-data-deleted-br"></a>Se eu cancelar um trabalho de backup depois de iniciado, os dados de backup transferidos serão excluídos? <br/>
Não. Todos os dados transferidos para o cofre, antes do cancelamento do trabalho de backup, permanecem no cofre. O Backup do Azure usa um mecanismo de ponto de verificação para, ocasionalmente, adicionar pontos de verificação aos dados de backup durante o backup. Como há pontos de verificação nos dados de backup, o próximo processo de backup pode validar a integridade dos arquivos. O próximo trabalho de backup será incremental para os dados cujo backup foi realizado anteriormente. Os backups incrementais transferem apenas dados novos ou alterados, que equivalem à melhor utilização da largura de banda.

Se você cancelar um trabalho de backup para uma VM do Azure, os dados transferidos serão ignorados. O próximo trabalho de backup transfere dados incrementais do último trabalho de backup bem-sucedido.

### <a name="are-there-limits-on-when-or-how-many-times-a-backup-job-can-be-scheduledbr"></a>Há algum limite de quando ou quantas vezes um backup pode ser agendado?<br/>
Sim. Você pode executar os trabalhos de backup no Windows Server ou em estações de trabalho do Windows até três vezes por dia. Você pode executar trabalhos de backup no System Center DPM até duas vezes por dia. Você pode executar um trabalho de backup para VMs IaaS uma vez por dia. Você pode usar a política de agendamento para o Windows Server ou a estação de trabalho do Windows para especificar programações diárias ou semanais. Usando o System Center DPM, especifique o agendamento diário, semanal, mensal e anual.

### <a name="why-is-the-size-of-the-data-transferred-to-the-recovery-services-vault-smaller-than-the-data-i-backed-upbr"></a>Por que o tamanho dos dados transferidos para o cofre dos Serviços de Recuperação é menor do que os dados submetido a backup?<br/>
 Todos os dados dos quais é feito backup do Azure Backup Agent, SCDPM ou Servidor de Backup do Azure são compactados e criptografados antes de serem transferidos. Depois que a compactação e a criptografia forem aplicadas, os dados no cofre de backup serão de 30 a 40% menores.

## <a name="what-can-i-back-up"></a>Do que eu posso fazer backup
### <a name="which-operating-systems-do-azure-backup-support-br"></a>Quais sistemas operacionais dão suporte ao Backup do Azure? <br/>
O Backup do Azure dá suporte à seguinte lista de sistemas operacionais para backup de arquivos e pastas, além de aplicativos de carga de trabalho protegidos usando o Servidor de Backup do Azure e o System Center Data Protection Manager (DPM).

| Sistema operacional | Plataforma | SKU |
|:--- | --- |:--- |
| Windows 8 e SPs mais recentes |64 bits |Enterprise, Pro |
| Windows 7 e SPs mais recentes |64 bits |Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter |
| Windows 8.1 e SPs mais recentes |64 bits |Enterprise, Pro |
| Windows 10 |64 bits |Enterprise, Pro, Home |
| Windows Server 2016 |64 bits |Standard, Datacenter, Essentials |
| Windows Server 2012 R2 e SPs mais recentes |64 bits |Standard, Datacenter, Foundation |
| Windows Server 2012 e SPs mais recentes |64 bits |Datacenter, Foundation, Standard |
| Windows Storage Server 2016 e SPs mais recentes |64 bits |Standard, Workgroup | 
| Windows Storage Server 2012 R2 e SPs mais recentes |64 bits |Standard, Workgroup |
| Windows Storage Server 2012 e SPs mais recentes |64 bits |Standard, Workgroup |
| Windows Server 2012 R2 e SPs mais recentes |64 bits |Essential |
| Windows Server 2008 R2 SP1 |64 bits |Standard, Enterprise, Datacenter, Foundation |
| Windows Server 2008 SP2 |64 bits |Standard, Enterprise, Datacenter, Foundation |

**Para o backup de VM do Azure:**

* **Linux**: o Backup do Azure dá suporte a [uma lista de distribuições endossadas pelo Azure](../virtual-machines/linux/endorsed-distros.md) exceto o principal sistema operacional Linux.  Outras distribuições personalizadas do Linux também devem funcionar, contanto que o agente de VM esteja disponível na máquina virtual e exista suporte para Python.
* **Windows Server**: não há suporte para versões anteriores ao Windows Server 2008 R2.


### <a name="is-there-a-limit-on-the-size-of-each-data-source-being-backed-up-br"></a>Há um limite para o tamanho de cada fonte de dados submetida a backup? <br/>
Não há nenhum limite para a quantidade de dados cujo backup você pode fazer backup em um cofre. O Backup do Azure restringe o tamanho máximo da fonte de dados, no entanto, esses limites são grandes. A partir de agosto de 2015, o tamanho máximo de fonte de dados para os sistemas operacionais com suporte é:

| S.Não | Sistema operacional | Tamanho máximo da fonte de dados |
|:---:|:--- |:--- |
| 1 |Windows Server 2012 ou posterior |54.400 GB |
| 2 |Windows 8 ou superior |54.400 GB |
| 3 |Windows Server 2008, Windows Server 2008 R2 |1700 GB |
| 4 |Windows 7 |1700 GB |

A tabela a seguir explica como cada tamanho de fonte de dados é determinado.

| Fonte de dados | Detalhes |
|:---:|:--- |
| Volume |A quantidade de dados incluídos no backup do volume único de um servidor ou de uma máquina cliente |
| Máquina virtual do Hyper-V |Soma dos dados de todos os VHDs da máquina virtual da qual está sendo feito o backup |
| Banco de dados do Microsoft SQL Server |O tamanho de um banco de dados SQL único do qual está sendo feito o backup |
| Microsoft SharePoint |A soma dos bancos de dados de conteúdo e de configuração em um farm do SharePoint do qual está sendo feito o backup |
| Microsoft Exchange |Soma de todos os bancos de dados do Exchange em um servidor Exchange do qual está sendo feito o backup |
| Estado do Sistema/BMR |Cada cópia individual do BMR ou do estado do sistema da máquina da qual está sendo feito o backup |

Para backup de VM do Azure, cada VM pode ter até 16 discos de dados com cada disco de dados tendo 1.023 GB ou menos. 

## <a name="retention-policy-and-recovery-points"></a>Política de retenção e pontos de recuperação
### <a name="is-there-a-difference-between-the-retention-policy-for-dpm-and-windows-serverclient-that-is-on-windows-server-without-dpmbr"></a>Há alguma diferença entre a política de retenção do DPM e do Windows Server/cliente Windows (ou seja, no Windows Server sem o DPM)?<br/>
Não, o DPM e o Windows Server/cliente Windows têm políticas de retenção diárias, semanais, mensais e anuais.

### <a name="can-i-configure-my-retention-policies-selectively--ie-configure-weekly-and-daily-but-not-yearly-and-monthlybr"></a>Posso configurar minhas políticas de retenção de forma seletiva – ou seja, configurar semanal e diária, mas não anual e mensal?<br/>
Sim, a estrutura de retenção de Backup do Azure permite que você tenha total flexibilidade na definição da política de retenção de acordo com suas necessidades.

### <a name="can-i-schedule-a-backup-at-6pm-and-specify-retention-policies-at-a-different-timebr"></a>Posso “agendar um backup” às 18h e especificar “políticas de retenção” em um momento diferente?<br/>
Não. As políticas de retenção só podem ser aplicadas em pontos de backup. Na imagem a seguir, a política de retenção é especificada para backups realizados à meia-noite e às 18h. <br/>

![Retenção e agendamento de Backup](./media/backup-azure-backup-faq/Schedule.png)
<br/>

### <a name="if-a-backup-is-retained-for-a-long-duration-does-it-take-more-time-to-recover-an-older-data-point-br"></a>Se um backup for mantido por um longo tempo, levará mais tempo para recuperar um ponto de dados mais antigo? <br/>
Não. O tempo de recuperação do ponto mais antigo ou mais recente é o mesmo. Cada ponto de recuperação se comporta como um ponto completo.

### <a name="if-each-recovery-point-is-like-a-full-point-does-it-impact-the-total-billable-backup-storagebr"></a>Se cada ponto de recuperação é como um ponto completo, isso afeta o armazenamento de backup total cobrável?<br/>
Os produtos típicos de ponto de retenção de longo prazo armazenam dados de backup como pontos completos. Os pontos completos *não oferecem economia* de armazenamento, mas são mais fáceis e rápidos de restaurar. As cópias incrementais *oferecem economia* de armazenamento, mas exigem que você restaure uma cadeia de dados, o que afeta o tempo de recuperação. A arquitetura de armazenamento do Backup do Azure oferece o melhor dos dois recursos, armazenando dados de forma otimizada para restaurações rápidas e incorrendo em baixos custos de armazenamento. Essa abordagem de armazenamento de dados garante que a largura de banda de entrada e saída seja usada com eficiência. A quantidade de armazenamento de dados e o tempo necessário para recuperar os dados são mantidos em um mínimo. Saiba mais sobre como os [backups incrementais](https://azure.microsoft.com/blog/microsoft-azure-backup-save-on-long-term-storage/) são eficientes.

### <a name="is-there-a-limit-on-the-number-of-recovery-points-that-can-be-createdbr"></a>Há um limite para o número de pontos de recuperação que podem ser criados?<br/>
Você pode criar até 9999 pontos de recuperação por instância protegida. Uma instância protegida é um computador, servidor (físico ou virtual) ou carga de trabalho configurado para fazer backup de dados no Azure. Para saber mais, veja as explicações de [Backup e retenção](./backup-introduction-to-azure-backup.md#backup-and-retention) e de [O que é uma instância protegida?](./backup-introduction-to-azure-backup.md#what-is-a-protected-instance)

### <a name="how-many-recoveries-can-i-perform-on-the-data-that-is-backed-up-to-azurebr"></a>Quantas recuperações posso executar nos dados incluídos no backup no Azure?<br/>
Não há limite para o número de recuperações do Backup do Azure.

### <a name="when-restoring-data-do-i-pay-for-the-egress-traffic-from-azure-br"></a>Ao restaurar dados, eu pago pelo tráfego de saída do Azure? <br/>
Não. As suas recuperações são gratuitas e você não é cobrado pelo tráfego de saída.

## <a name="azure-backup-encryption"></a>Criptografia do Backup do Azure
### <a name="is-the-data-sent-to-azure-encrypted-br"></a>Os dados são enviados para o Azure criptografados? <br/>
Sim. Os dados são criptografados localmente no computador cliente/servidor/SCDPM usando AES256 e são enviados por um link HTTPS seguro.

### <a name="is-the-backup-data-on-azure-encrypted-as-wellbr"></a>Os dados de backup também são criptografados no Azure?<br/>
Sim. Os dados enviados para o Azure permanecem criptografados (em repouso). A Microsoft não descriptografa os dados de backup em nenhum momento. Ao fazer backup de uma VM do Azure, o Backup do Azure se baseia em criptografia da máquina virtual. Por exemplo, se sua VM for criptografada usando o Azure Disk Encryption ou alguma outra tecnologia de criptografia, o Backup do Azure usará essa criptografia para proteger seus dados.

### <a name="what-is-the-minimum-length-of-encryption-key-used-to-encrypt-backup-data-br"></a>Qual é o comprimento mínimo da chave de criptografia usada para criptografar os dados de backup? <br/>
A chave de criptografia deve ter pelo menos 16 caracteres, quando você estiver usando o agente de backup do Azure. Para VMs do Azure, não há nenhum limite para o comprimento das chaves usadas pelo KeyVault do Azure. 

### <a name="what-happens-if-i-misplace-the-encryption-key-can-i-recover-the-data-or-can-microsoft-recover-the-data-br"></a>O que acontecerá se eu inserir a chave de criptografia incorretamente? Posso recuperar os dados ou a Microsoft pode recuperar os dados? <br/>
A chave usada para criptografar os dados de backup está presente apenas nas instalações do cliente. A Microsoft não mantém uma cópia no Azure e não tem qualquer acesso à chave. Se o cliente inserir a chave incorretamente, a Microsoft não poderá recuperar os dados de backup.
