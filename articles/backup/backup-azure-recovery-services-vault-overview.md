---
title: Visão geral de cofres de Serviços de Recuperação
description: Uma visão geral e a comparação entre os cofres de Serviços de Recuperação e os cofres de Backup do Azure.
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 8/10/2018
ms.author: raynew
ms.openlocfilehash: 21ce9f411bf9c80e44614c6f7a6d123d49634a8f
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465613"
---
# <a name="recovery-services-vaults-overview"></a>Visão geral dos cofres dos Serviços de Recuperação

Este artigo descreve os recursos de um cofre de Serviços de Recuperação. Um cofre de Serviços de Recuperação é uma entidade de armazenamento no Azure que hospeda dados. Os dados normalmente são cópias de dados ou informações de configuração de VMs (máquinas virtuais), cargas de trabalho, servidores ou estações de trabalho. Você pode usar cofres dos Serviços de Recuperação para armazenar dados de backup para vários serviços do Azure, como VMs de IaaS (Windows ou Linux) e bancos de dados SQL do Azure. Os cofres de Serviços de Recuperação dão suporte a System Center DPM, Windows Server, Servidor de Backup do Azure e outros. Os cofres dos Serviços de Recuperação facilitam a organização dos dados de backup, minimizando a sobrecarga de gerenciamento.

Com uma assinatura do Azure, você pode criar até 500 cofres de Serviços de Recuperação por assinatura por região.

## <a name="comparing-recovery-services-vaults-and-backup-vaults"></a>Comparação de cofres de Serviços de Recuperação e cofres de Backup

Se você ainda tem cofres de Backup, eles estão sendo atualizados automaticamente para cofres dos Serviços de Recuperação. Até novembro de 2017, todos os cofres de Backup foram atualizados para cofres dos Serviços de Recuperação.

Cofres de Serviços de Recuperação são baseados no modelo do Azure Resource Manager, enquanto os cofres de Backup eram baseados no modelo do Azure Service Manager. Quando você atualiza um cofre de Backup para um cofre de Serviços de Recuperação, os dados de backup permanecem intactos durante e após o processo de atualização. Cofres de Serviços de Recuperação fornecem recursos não disponíveis a cofres de Backup, como:

- **Recursos aprimorados para ajudar a proteger dados de backup**: Com os cofres de Serviços de Recuperação, o Backup do Azure fornece recursos de segurança para proteger backups em nuvem. Esses recursos de segurança asseguram que você possa proteger seus backups e recuperar dados com segurança, mesmo que os servidores de produção e de backup estejam comprometidos. [Saiba mais](backup-azure-security-feature.md)

- **Monitoramento central para seu ambiente de TI híbrido**: Com os cofres de Serviços de Recuperação, você pode monitorar não apenas suas [VMs da IaaS do Azure](backup-azure-manage-vms.md), como também seus [ativos locais](backup-azure-manage-windows-server.md#manage-backup-items) de um portal central. [Saiba mais](https://azure.microsoft.com/blog/alerting-and-monitoring-for-azure-backup)

- **RBAC (Controle de Acesso Baseado em Função)** : O RBAC oferece controle de gerenciamento de acesso detalhado no Azure. [O Azure fornece várias funções internas](../role-based-access-control/built-in-roles.md), e o Backup do Azure tem três [funções internas para gerenciar pontos de recuperação](backup-rbac-rs-vault.md). Cofres de Serviços de Recuperação são compatíveis com RBAC, que restringe o acesso de backup e restauração ao conjunto definido de funções de usuário. [Saiba mais](backup-rbac-rs-vault.md)

- **Proteger todas as configurações das Máquinas Virtuais do Azure**: Cofres de Serviços de Recuperação protegem VMs com base no Resource Manager, incluindo Premium Disks, Managed Disks e VMs Criptografadas. Atualizar um cofre de Backup para um cofre de Serviços de Recuperação possibilita atualizar as VMs baseadas no Service Manager para VMs baseadas no Resource Manager. Ao atualizar o cofre, você pode manter os seus pontos de recuperação de VM baseada no Service Manager e configurar a proteção para VMs atualizadas (habilitadas para Resource Manager). [Saiba mais](https://azure.microsoft.com/blog/azure-backup-recovery-services-vault-ga)

- **Restauração instantânea para VMs IaaS**: Usando os cofres de Serviços de Recuperação, você pode restaurar arquivos e pastas em uma VM IaaS sem restaurar toda a VM, o que permite tempos de restauração mais rápidos. Restauração instantânea para VMs da IaaS está disponível para VMs Linux e Windows. [Saiba mais](https://azure.microsoft.com/blog/instant-file-recovery-from-azure-linux-vm-backup-using-azure-backup-preview)

## <a name="managing-your-recovery-services-vaults-in-the-portal"></a>Gerenciando os cofres de Serviços de Recuperação no portal
A criação e o gerenciamento de cofres dos Serviços de Recuperação no portal do Azure são fáceis porque o serviço de Backup integra-se a outros serviços do Azure. Essa integração significa que você pode criar ou gerenciar um cofre de Serviços de Recuperação *no contexto do serviço de destino*. Por exemplo, para exibir os pontos de recuperação de uma VM, selecione a VM e clique em **Backup** no menu Operações.

![O cofre de Serviços de Recuperação detalha a VM](./media/backup-azure-recovery-services-vault-overview/rs-vault-in-context-vm.png)

Se a VM não tiver o backup configurado, será solicitado que você configure o backup. Se o backup estiver configurado, você verá informações de backup sobre a VM, incluindo uma lista de pontos de restauração.  

![O cofre de Serviços de Recuperação detalha a VM](./media/backup-azure-recovery-services-vault-overview/vm-recovery-point-list.png)

No exemplo anterior, **ContosoVM** é o nome da máquina virtual. **ContosoVM-demovault** é o nome do cofre de Serviços de Recuperação. Você não precisa se lembrar do nome do cofre de Serviços de Recuperação que armazena os pontos de recuperação; essas informações estão disponíveis na máquina virtual.  

Se um cofre dos Serviços de Recuperação proteger vários servidores, talvez seja mais lógico examinar a área segura dos Serviços de Recuperação. Você pode pesquisar em todos os cofres de Serviços de Recuperação na assinatura e escolher um na lista.

As seções a seguir contêm links para artigos que explicam como usar um cofre de Serviços de Recuperação em cada tipo de atividade.

> [!NOTE]
> O cofre dos Serviços de Recuperação não poderá ser criado com o mesmo nome, caso tenha sido excluído no período de 24 horas. Use um nome de recurso diferente, escolha um grupo de recursos diferente ou tente novamente após 24 horas.

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
Use os artigos a seguir para:</br>
[Fazer backup de uma VM IaaS](backup-azure-arm-vms-prepare.md)</br>
[Fazer backup de um Servidor de Backup do Azure](backup-azure-microsoft-azure-backup.md)</br>
[Fazer backup de um Windows Server](backup-configure-vault.md)
