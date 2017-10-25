---
title: "Preparar computadores para configurar a recuperação de desastre entre regiões do Azure após a migração para o Azure usando o Site Recovery | Microsoft Docs"
description: "Este artigo descreve como preparar computadores para configurar a recuperação de desastre entre regiões do Azure após a migração para o Azure usando o Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: ponatara
manager: abhemraj
editor: 
ms.assetid: 9126f5e8-e9ed-4c31-b6b4-bf969c12c184
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2017
ms.author: ponatara
ms.openlocfilehash: 2aee0fb8d1ba1ff1584bee91b4d1cc34b654d97f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="replicate-azure-vms-to-another-region-after-migration-to-azure-by-using-azure-site-recovery"></a>Replicar VMs do Azure para outra região após a migração para o Azure usando o Azure Site Recovery

>[!NOTE]
> A replicação do Azure Site Recovery para as máquinas virtuais (VMs) do Azure está atualmente na versão prévia.

## <a name="overview"></a>Visão geral

Este artigo ajuda você a preparar as máquinas virtuais do Azure para a replicação entre duas regiões do Azure depois que essas máquinas foram migradas de um ambiente local para o Azure usando o Azure Site Recovery.

## <a name="disaster-recovery-and-compliance"></a>Recuperação de desastre e conformidade
Atualmente, mais empresas estão adotando as cargas de trabalho do Azure. Com as empresas movendo as cargas de trabalho de produção locais e essenciais do Azure, configurar a recuperação de desastre para essas cargas de trabalho é obrigatório para a conformidade e segurança contra as interrupções em uma região do Azure.

## <a name="steps-for-preparing-migrated-machines-for-replication"></a>Etapas para preparar máquinas migradas para replicação
Para preparar máquinas migradas para configurar a replicação para outra região do Azure:

1. Migração completa.
2. Instale o agente do Azure, se necessário.
3. Remova o Serviço de mobilidade.  
4. Reinicie a VM.

Essas etapas estão descritas com mais detalhes nas seções a seguir.

### <a name="step-1-migrate-workloads-running-on-hyper-v-vms-vmware-vms-and-physical-servers-to-run-on-azure-vms"></a>Etapa 1: migre cargas de trabalho em execução em VMs do Hyper-V locais, VMs VMware e servidores físicos para execução em VMs do Azure

Para configurar a replicação e migrar suas cargas de trabalho do Hyper-V, VMware e físicas locais para o Azure, siga as etapas no artigo [Migrar máquinas virtuais IaaS do Azure entre regiões do Azure com o Azure Site Recovery](site-recovery-migrate-to-azure.md). 

Depois da migração, você não precisa confirmar excluir um failover. Em vez disso, você seleciona a opção **Migração Completa** para cada computador que deseja migrar:
1. Em **Itens Replicados**, clique com botão direito na VM e clique em **Concluir a Migração**. Clique em **OK** para concluir a etapa. Você pode acompanhar o progresso nas propriedades da VM, monitorando o trabalho de Migração Completa em **trabalhos do Site Recovery**.
2. A ação **Concluir Migração** conclui o processo de migração, remove a replicação da máquina e interrompe a cobrança do Site Recovery para o computador.

   ![completemigration](./media/site-recovery-hyper-v-site-to-azure/migrate.png)

### <a name="step-2-install-the-azure-vm-agent-on-the-virtual-machine"></a>Etapa 2: instale o agente da VM do Azure na máquina virtual
O [agente da VM](../virtual-machines/windows/classic/agents-and-extensions.md#azure-vm-agents-for-windows-and-linux) do Azure deve ser instalado na máquina virtual para a extensão do Site Recovery para trabalhar e ajudar a proteger a VM.

>[!IMPORTANT]
>A partir da versão 9.7.0.0, em máquinas virtuais do Windows, o instalador do Serviço de mobilidade também instala o último agente da VM do Azure disponível. Sobre a migração, a máquina virtual atende aos pré-requisitos de instalação do agente para usar qualquer extensão da VM, incluindo a extensão do Site Recovery. O agente da VM do Azure precisa ser instalado manualmente somente se o Serviço de mobilidade instalado no computador migrado for versão 9.6 ou anterior.

A tabela a seguir fornece informações adicionais sobre como instalar o agente da VM e validar que ele foi instalado:

| **Operação** | **Windows** | **Linux** |
| --- | --- | --- |
| Instalando o agente de VM |Baixe e instale o [agente MSI](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Você precisa de privilégios de administrador para concluir a instalação. |Instale o [agente Linux](../virtual-machines/linux/agent-user-guide.md) mais recente. Você precisa de privilégios de administrador para concluir a instalação. É recomendável instalar o agente do seu repositório de distribuição. *Não é recomendável* instalar o agente de VM Linux diretamente do GitHub.  |
| Validação da instalação do agente de VM |1. Navegue até a pasta :\WindowsAzure\Packages na VM do Azure. Você deve ver o arquivo WaAppAgent.exe. <br>2. Clique com o botão direito do mouse no arquivo, vá para **Propriedades** e selecione a guia **Detalhes**. O campo **Versão do Produto** deve ser 2.6.1198.718 ou mais recente. |N/D |


### <a name="step-3-remove-the-mobility-service-from-the-migrated-virtual-machine"></a>Etapa 3: remova o serviço de Mobilidade da máquina virtual migrada

Se migrou suas máquinas do VMware locais ou servidores físicos no Windows/Linux, você precisa remover/desinstalar manualmente o Serviço de mobilidade da máquina virtual migrada.

>[!IMPORTANT]
>Esta etapa não é necessária para VMs do Hyper-V migradas para o Azure.

#### <a name="uninstall-the-mobility-service-on-a-windows-server-vm"></a>Desinstalar o Serviço de mobilidade de uma VM do Windows Server
Use um dos métodos a seguir para desinstalar o Serviço de mobilidade em um computador Windows Server.

##### <a name="uninstall-by-using-the-windows-ui"></a>Desinstalar usando a interface do usuário do Windows
1. No Painel de Controle, selecione **Programas**.
2. Selecione **Serviço de Mobilidade do Microsoft Azure Site Recovery/servidor de Destino Mestre** e, em seguida, **Desinstalar**.

##### <a name="uninstall-at-a-command-prompt"></a>Desinstalar em um prompt de comando
1. Abra uma janela de Prompt de comando como administrador.
2. Para desinstalar o Serviço de mobilidade, execute o seguinte comando:

   ```
   MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
   ```

#### <a name="uninstall-the-mobility-service-on-a-linux-computer"></a>Desinstalar o Serviço de mobilidade de um computador Linux
1. No servidor Linux, entre como um usuário **raiz**.
2. Em um terminal, acesse /user/local/ASR.
3. Para desinstalar o Serviço de mobilidade, execute o seguinte comando:

   ```
   uninstall.sh -Y
   ```

### <a name="step-4-restart-the-vm"></a>Etapa 4: reinicie a VM

Depois de desinstalar o Serviço de mobilidade, reinicie a VM antes de configurar a replicação para outra região do Azure.


## <a name="next-steps"></a>Próximas etapas
- Inicie a proteção de suas cargas de trabalho ao [replicar máquinas virtuais do Azure](site-recovery-azure-to-azure.md).
- Saiba mais sobre as [Diretrizes de rede para replicar máquinas virtuais do Azure](site-recovery-azure-to-azure-networking-guidance.md).
