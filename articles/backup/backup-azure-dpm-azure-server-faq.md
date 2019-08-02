---
title: Perguntas frequentes sobre o Servidor de Backup do Azure e o DPM
description: 'Respostas a perguntas comuns sobre: O Servidor de Backup do Azure e o DPM.'
author: srinathvasireddy
manager: sivan
ms.service: backup
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: srinathv
ms.openlocfilehash: 54727daa158172ae44379b847c70602ca998c65d
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68466406"
---
# <a name="azure-backup-server-and-dpm---faq"></a>Servidor de Backup do Azure e o DPM-perguntas frequentes
Este artigo responde a perguntas frequentes sobre o Servidor de Backup do Azure e o DPM.

### <a name="can-i-use-azure-backup-server-to-create-a-bare-metal-recovery-bmr-backup-for-a-physical-server-br"></a>Posso usar o servidor de Backup do Azure para criar um backup BMR (Recuperação Bare-Metal) para um servidor físico? <br/>
Sim.

### <a name="can-i-register-the-server-to-multiple-vaults"></a>Posso registrar o servidor em vários cofres?
Nº Um servidor do DPM ou do Backup do Azure pode ser registrado em apenas um cofre.


### <a name="can-i-use-dpm-to-back-up-apps-in-azure-stack"></a>Pode usar o DPM para fazer backup de aplicativos na pilha do Azure?
Nº Você pode usar o Backup do Azure para proteger o Azure Stack, o Backup do Azure não oferece suporte ao uso do DPM para fazer backup de aplicativos no Azure Stack.

### <a name="if-ive-installed-azure-backup-agent-to-protect-my-files-and-folders-can-i-install-system-center-dpm-to-back-up-on-premises-workloads-to-azure"></a>Se eu instalei o agente do Backup do Azure para proteger meus arquivos e pastas, posso instalar o System Center DPM para fazer backup de cargas de trabalho locais no Azure?
Sim. Mas você deve configurar o DPM primeiro e depois instalar o agente de Backup do Azure.  Instalar os componentes nesta ordem garante que o agente de Backup do Azure funcione com o DPM. Instalar o agente antes de instalar o DPM não é aconselhável e não há suporte para isso.

### <a name="why-cant-i-add-an-external-dpm-server-after-installing-ur7-and-latest-azure-backup-agent"></a>Por que não consigo adicionar um servidor DPM externo após instalar o UR7 e o agente de Backup do Azure mais recente?
Para os servidores DPM com fontes de dados protegidas na nuvem (usando um pacote cumulativo de atualizações anterior ao Pacote de Cumulativo de Atualizações 7), é necessário aguardar pelo menos um dia após instalar o UR7 e o agente de Backup do Azure mais recente para começar a **Adicionar servidor DPM externo**. O período de um dia é necessário para carregar os metadados dos grupos de proteção do DPM no Azure. Os metadados do grupo de proteção são carregados na primeira vez por meio de um trabalho noturno.

## <a name="vmware-and-hyper-v-backup"></a>Backup do VMware e Hyper-V

### <a name="can-i-back-up-vmware-vcenter-servers-to-azure"></a>Posso fazer backup de servidores do VMware vCenter para o Azure?
Sim. Você pode usar o Servidor de Backup do Azure para fazer backup dos hosts VMware vCenter Server e ESXi no Azure.

- [Saiba mais](backup-mabs-protection-matrix.md) sobre as versões compatíveis.
- [Siga estas etapas](backup-azure-backup-server-vmware.md) para fazer backup de um servidor VMware.

### <a name="do-i-need-a-separate-license-to-recover-a-full-on-premises-vmwarehyper-v-cluster"></a>Preciso de uma licença separada para recuperar um cluster VMware/Hyper-V local completo?
Você não precisa separar licenciamento para a proteção de VMware/Hyper-V.

- Se você for um cliente do System Center, use o System Center Data Protection Manager (DPM) para proteger as VMs do VMware.
- Se você não for um cliente do System Center, você pode usar o Servidor de Backup do Azure (pré-pago) para proteger as VMs VMware.


## <a name="sharepoint"></a>SharePoint

### <a name="can-i-recover-a-sharepoint-item-to-the-original-location-if-sharepoint-is-configured-by-using-sql-alwayson-with-protection-on-disk"></a>Poderei recuperar um item do SharePoint para a localização original se o SharePoint tiver sido configurado usando o SQL AlwaysOn (com proteção em disco)?
Sim, o item pode ser recuperado para o site do SharePoint original.

### <a name="can-i-recover-a-sharepoint-database-to-the-original-location-if-sharepoint-is-configured-by-using-sql-alwayson"></a>Poderei recuperar um banco de dados do SharePoint na localização original se o SharePoint estiver configurado usando o AlwaysOn do SQL?
Como os bancos de dados do SharePoint são configurados no SQL AlwaysOn, eles não podem ser modificados a menos que o grupo de disponibilidade seja removido. Por isso, o DPM não pode restaurar o banco de dados para o local original. Não é possível recuperar um banco de dados do SQL Server para outra instância do SQL Server.

## <a name="next-steps"></a>Próximas etapas

Leia as outras perguntas frequentes:

- [Saiba mais](backup-support-matrix-mabs-dpm.md) sobre a matriz de suporte do servidor de backup do Azure e do DPM.
- [Saiba mais](backup-azure-mabs-troubleshoot.md) sobre as diretrizes de solução de problemas do servidor de backup do Azure e do DPM.
