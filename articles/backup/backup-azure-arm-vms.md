---
title: Fazer backup de VMs do Azure | Microsoft Docs
description: "Descubra, registre e faça backup de máquinas virtuais do Azure em um cofre dos Serviços de Recuperação."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "backup de máquinas virtuais; fazer backup de máquina virtual, backup e recuperação de desastres; backup de vm arm"
ms.assetid: 5c68481d-7be3-4e68-b87c-0961c267053e
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 8/15/2017
ms.author: trinadhk;jimpark;markgal;
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 40983a3de104238d09b976b5fcf2419da42c1bba
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="back-up-azure-virtual-machines-to-a-recovery-services-vault"></a>Fazer backup de máquinas virtuais do Azure em um cofre dos Serviços de Recuperação
> [!div class="op_single_selector"]
> * [Fazer backup de VMs no cofre dos Serviços de Recuperação](backup-azure-arm-vms.md)
> * [Fazer backup de VMs no cofre de Backup](backup-azure-vms.md)
>
>

Este artigo detalha como para fazer backup de VMs do Azure (implantadas pelo Resource Manager ou com a implantação clássica) em um cofre dos Serviços de Recuperação. A maior parte do trabalho para fazer backup de VMs é a preparação. Antes de fazer backup ou proteger uma VM, você deverá atender aos [pré-requisitos](backup-azure-arm-vms-prepare.md) para preparar o ambiente e proteger suas VMs. Depois de concluir os pré-requisitos, você pode iniciar a operação de backup para tirar instantâneos da sua VM.


[!INCLUDE [learn about backup deployment models](../../includes/backup-deployment-models.md)]

Para obter mais informações, confira os artigos sobre [planejamento da infraestrutura de backup de VM no Azure](backup-azure-vms-introduction.md) e sobre [máquinas virtuais do Azure](https://azure.microsoft.com/documentation/services/virtual-machines/).

## <a name="triggering-the-backup-job"></a>Disparar o trabalho de backup
A política de backup associada ao cofre dos Serviços de Recuperação define a frequência e quando a operação de backup é executada. Por padrão, o primeiro backup agendado é o backup inicial. Até que o backup inicial ocorra, o Status do Último Backup na folha **Trabalhos de Backup** aparece como **Aviso (backup inicial pendente)**.

![Backup pendente](./media/backup-azure-vms-first-look-arm/initial-backup-not-run.png)

A menos que o backup inicial esteja prestes a começar, é recomendável que você execute **Fazer Backup Agora**. O procedimento a seguir começa no painel do cofre. Esse procedimento serve para executar o trabalho de backup inicial depois de concluir todos os pré-requisitos. Se o trabalho de backup inicial já tiver sido executado, esse procedimento não estará disponível. A política de backup associada determina o próximo trabalho de backup.  

Para executar o trabalho de backup inicial:

1. No painel do cofre, clique no número em **Itens de Backup**, ou clique no bloco **Itens de Backup**. <br/>
  ![Ícone Configurações](./media/backup-azure-vms-first-look-arm/rs-vault-config-vm-back-up-now-1.png)

  A folha **Itens de Backup** será aberta.

  ![Fazer backup de itens](./media/backup-azure-vms-first-look-arm/back-up-items-list.png)

2. Na folha **Itens de Backup**, selecione o item.

  ![Ícone Configurações](./media/backup-azure-vms-first-look-arm/back-up-items-list-selected.png)

  A lista **Itens de Backup** será aberta. <br/>

  ![Trabalho de backup iniciado](./media/backup-azure-vms-first-look-arm/backup-items-not-run.png)

3. Na lista **Itens de Backup**, clique nas reticências **...** para abrir o menu de contexto.

  ![Menu de contexto](./media/backup-azure-vms-first-look-arm/context-menu.png)

  O menu de contexto é exibido.

  ![Menu de contexto](./media/backup-azure-vms-first-look-arm/context-menu-small.png)

4. No menu de contexto, clique em **Fazer backup agora**.

  ![Menu de contexto](./media/backup-azure-vms-first-look-arm/context-menu-small-backup-now.png)

  A folha Fazer Backup Agora é aberta.

  ![mostra a folha Fazer Backup Agora](./media/backup-azure-vms-first-look-arm/backup-now-blade-short.png)

5. Na folha Fazer Backup Agora, clique no ícone de calendário, use o controle de calendário para selecionar o último dia de retenção desse ponto de recuperação e clique em **Fazer Backup**.

  ![definir o último dia em que o ponto de recuperação de Fazer Backup Agora será retido](./media/backup-azure-vms-first-look-arm/backup-now-blade-calendar.png)

  As notificações de implantação informam que o trabalho de backup foi disparado, e que você pode monitorar o andamento do trabalho na página de Trabalhos de backup. Dependendo do tamanho da VM, a criação do backup inicial pode demorar um pouco.

6. Para exibir ou rastrear o status do backup inicial, no painel do cofre, no bloco **Trabalhos de Backup**, clique em **Em andamento**.

  ![Bloco dos Trabalhos de Backup](./media/backup-azure-vms-first-look-arm/open-backup-jobs-1.png)

  A folha Trabalhos de Backup será aberta.

  ![Bloco dos Trabalhos de Backup](./media/backup-azure-vms-first-look-arm/backup-jobs-in-jobs-view-1.png)

  Na folha **Trabalhos de backup** , você pode ver o status de todos os trabalhos. Verifique se o trabalho de backup de sua VM ainda está em andamento ou se já foi concluído. Após a conclusão do trabalho de backup, o status será *Concluído*.

  > [!NOTE]
  > Como parte da operação de backup, o serviço de Backup do Azure emite um comando para a extensão de backup em cada VM para limpar todas as gravações e capturar um instantâneo consistente.
  >
  >

## <a name="troubleshooting-errors"></a>Solucionar erros
Se você enfrentar problemas ao copiar a sua máquina virtual, confira o [artigo de solução de problemas de VM](backup-azure-vms-troubleshoot.md) para obter ajuda.

## <a name="next-steps"></a>Próximas etapas
Agora que você protegeu sua VM, consulte os seguintes artigos para aprender sobre tarefas de gerenciamento de VM e a restauração de VMs.

* [Gerenciar e monitorar suas máquinas virtuais](backup-azure-manage-vms.md)
* [Restaurar máquinas virtuais](backup-azure-arm-restore-vms.md)
