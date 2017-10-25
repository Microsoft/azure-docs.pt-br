---
title: "Visão geral dos cofres de Serviços de Recuperação | Microsoft Docs"
description: "Uma visão geral e a comparação entre os cofres de Serviços de Recuperação e os cofres de Backup do Azure."
services: backup
documentationcenter: " "
author: markgalioto
manager: carmonm
ms.assetid: 38d4078b-ebc8-41ff-9bc8-47acf256dc80
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 05/15/2017
ms.author: markgal;arunak
ms.openlocfilehash: 19e2aafe3de106be32f3d90c63c0ea03c626f272
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="recovery-services-vaults-overview"></a>Visão geral dos cofres dos Serviços de Recuperação

Este artigo descreve os recursos de um cofre de Serviços de Recuperação. Um cofre de Serviços de Recuperação é uma entidade de armazenamento no Azure que hospeda dados. Os dados normalmente são cópias de dados ou informações de configuração de VMs (máquinas virtuais), cargas de trabalho, servidores ou estações de trabalho. Um cofre de Serviços de Recuperação é a versão do Resource Manager de um cofre de Backup. A Microsoft incentiva você usar cofres dos Serviços de Recuperação e converter todos os cofres de Backup em cofres de Serviços de Recuperação.

## <a name="what-is-a-recovery-services-vault"></a>O que é um cofre dos Serviços de Recuperação?

Um cofre dos Serviços de Recuperação é uma entidade de armazenamento online no Azure usada para armazenar dados como cópias de backup, pontos de recuperação e políticas de backup. Você pode usar cofres dos Serviços de Recuperação para armazenar dados de backup para vários serviços do Azure, como VMs de IaaS (Windows ou Linux) e bancos de dados SQL do Azure. Os cofres de Serviços de Recuperação dão suporte a System Center DPM, Windows Server, Servidor de Backup do Azure e outros. Os cofres dos Serviços de Recuperação facilitam a organização dos dados de backup, minimizando a sobrecarga de gerenciamento.

Em uma assinatura do Azure, você pode criar quantos cofres dos Serviços de Recuperação desejar.

## <a name="comparing-recovery-services-vaults-and-backup-vaults"></a>Comparação de cofres de Serviços de Recuperação e cofres de Backup

Cofres de Serviços de Recuperação são baseados no modelo do Azure Resource Manager, enquanto os cofres de Backup são baseados no modelo do Azure Service Manager. Quando você atualiza um cofre de Backup para um cofre de Serviços de Recuperação, os dados de backup permanecem intactos durante e após o processo de atualização. Cofres de Serviços de Recuperação fornecem recursos não disponíveis a cofres de Backup, como:

- **Recursos aprimorados para ajudar a proteger dados de backup**: com os cofres de Serviços de Recuperação, o Backup do Azure fornece recursos de segurança para proteger backups em nuvem. Esses recursos de segurança asseguram que você possa proteger seus backups e recuperar com segurança dados de backups em nuvem, mesmo que os servidores de produção e de backup sejam comprometidos. [Saiba mais](backup-azure-security-feature.md)

- **Monitoramento central para seu ambiente de TI híbrida**: com os cofres de Serviços de Recuperação, você pode monitorar não apenas suas [VMs da IaaS do Azure](backup-azure-manage-vms.md), como também seus [ativos locais](backup-azure-manage-windows-server.md#manage-backup-items) de um portal central. [Saiba mais](http://azure.microsoft.com/blog/alerting-and-monitoring-for-azure-backup)

- **RBAC (Controle de Acesso Baseado em Função)**: o RBAC oferece controle de gerenciamento de acesso detalhado no Azure. [O Azure fornece várias funções internas](../active-directory/role-based-access-built-in-roles.md), e o Backup do Azure tem três [funções internas para gerenciar pontos de recuperação](backup-rbac-rs-vault.md). Cofres de Serviços de Recuperação são compatíveis com RBAC, que restringe o acesso de backup e restauração ao conjunto definido de funções de usuário. [Saiba mais](backup-rbac-rs-vault.md)

- **Proteger todas as configurações de Máquinas Virtuais do Azure**: cofres de Serviços de Recuperação protegem VMs com base no Resource Manager, incluindo Premium Disks, Managed Disks e VMs Criptografadas. Atualizar um cofre de Backup para um cofre de Serviços de Recuperação possibilita atualizar as VMs baseadas no Service Manager para VMs baseadas no Resource Manager. Ao atualizar o cofre, você pode manter os seus pontos de recuperação de VM baseada no Service Manager e configurar a proteção para VMs atualizadas (habilitadas para Resource Manager). [Saiba mais](http://azure.microsoft.com/blog/azure-backup-recovery-services-vault-ga)

- **Restauração instantânea para VMs da IaaS**: usando os cofres de Serviços de Recuperação, você pode restaurar arquivos e pastas em uma VM IaaS sem restaurar toda a VM, o que permite tempos de restauração mais rápidos. Restauração instantânea para VMs da IaaS está disponível para VMs Linux e Windows. [Saiba mais](http://azure.microsoft.com/blog/instant-file-recovery-from-azure-linux-vm-backup-using-azure-backup-preview)

## <a name="managing-your-recovery-services-vaults-in-the-portal"></a>Gerenciando os cofres de Serviços de Recuperação no portal
É fácil criar e gerenciar cofres de Serviços de Recuperação no portal do Azure porque o serviço de Backup está integrado à folha Configurações do Azure. Essa integração significa que você pode criar ou gerenciar um cofre de Serviços de Recuperação *no contexto do serviço de destino*. Por exemplo, para exibir os pontos de recuperação para uma VM, selecione-o e clique em **Backup** na folha Configurações. As informações de backup específicas para VM são exibidas. No exemplo a seguir, **ContosoVM** é o nome da máquina virtual. **ContosoVM-demovault** é o nome do cofre de Serviços de Recuperação. Você não precisa se lembrar do nome do cofre de Serviços de Recuperação que armazena os pontos de recuperação; essas informações estão disponíveis na máquina virtual.  

![O cofre de Serviços de Recuperação detalha a VM](./media/backup-azure-recovery-services-vault-overview/rs-vault-in-context.png)

Se vários servidores forem protegidos usando o mesmo cofre de Serviços de Recuperação, talvez seja mais lógico consultar o cofre de Serviços de Recuperação. Você pode pesquisar em todos os cofres de Serviços de Recuperação na assinatura e escolher um na lista.

As seções a seguir contêm links para artigos que explicam como usar um cofre de Serviços de Recuperação em cada tipo de atividade.

### <a name="back-up-data"></a>Fazer backup de dados
- [Fazer backup de uma VM do Azure](backup-azure-vms-first-look-arm.md)
- [Fazer backup do Windows Server ou da estação de trabalho do Windows](backup-try-azure-backup-in-10-mins.md)
- [Fazer backup de cargas de trabalho do DPM para o Azure](backup-azure-dpm-introduction.md)
- [Preparar-se para fazer backup de cargas de trabalho usando o Servidor de Backup do Azure](backup-azure-microsoft-azure-backup.md)

### <a name="manage-recovery-points"></a>Gerenciar pontos de recuperação
- [Gerenciar backups da VM do Azure](backup-azure-manage-vms.md)
- [Gerenciando arquivos e pastas](backup-azure-manage-windows-server.md)

### <a name="restore-data-from-the-vault"></a>Restaurar dados do cofre
- [Recuperar arquivos individuais de uma VM do Azure](backup-azure-restore-files-from-vm.md)
- [Restaurar uma VM do Azure](backup-azure-arm-restore-vms.md)

### <a name="secure-the-vault"></a>Proteger o cofre
- [Protegendo dados de backup de nuvem em cofres dos Serviços de Recuperação](backup-azure-security-feature.md)



## <a name="next-steps"></a>Próximas etapas
Use o seguinte artigo para:</br>
[Fazer backup de uma VM IaaS](backup-azure-arm-vms-prepare.md)</br>
[Fazer backup de um Servidor de Backup do Azure](backup-azure-microsoft-azure-backup.md)</br>
[Fazer backup de um Windows Server](backup-configure-vault.md)
