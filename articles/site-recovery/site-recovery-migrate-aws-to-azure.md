---
title: Migrar VMs do AWS para o Azure | Microsoft Docs
description: "Este artigo descreve como migrar máquinas virtuais em execução no AWS (Amazon Web Services) para o Azure usando o Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: bsiva
manager: jwhit
editor: 
ms.assetid: ddb412fd-32a8-4afa-9e39-738b11b91118
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 05/31/2017
ms.author: bsiva
ms.openlocfilehash: b3c0727a279649f4f7dae30d41027129ce5b04ee
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-virtual-machines-in-amazon-web-services-aws-to-azure-with-azure-site-recovery"></a>Migrar máquinas virtuais no AWS (Amazon Web Services) para Azure com o Azure Site Recovery

Este artigo descreve como migrar instâncias Windows do AWS para máquinas virtuais do Azure com o serviço do [Azure Site Recovery](site-recovery-overview.md).

A migração é efetivamente um failover do AWS para o Azure. Não é possível fazer failback dos computadores para AWS e não há replicação contínua. Este artigo descreve as etapas para migração no Portal do Azure, que se baseiam nas instruções para [replicar um computador físico no Azure](site-recovery-vmware-to-azure.md).

Publique eventuais comentários ou perguntas no final deste artigo ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)

## <a name="supported-operating-systems"></a>Sistemas operacionais com suporte

O Site Recovery pode ser usado para migrar instâncias EC2 que estejam executando qualquer um dos seguintes sistemas operacionais:

- Windows (somente 64 bits)
    - Windows Server 2008 R2 SP1+ (drivers Citrix PV ou somente drivers AWS PV. **Não há suporte para instâncias executando drivers RedHat PV**) Windows Server 2012 Windows Server 2012 R2
- Linux (somente 64 bits)
    - Red Hat Enterprise Linux 6.7 (apenas instâncias de HVM virtualizadas)

## <a name="prerequisites"></a>Pré-requisitos

Aqui está o que você precisa para essa implantação:

* **Servidor de configuração**: uma VM do Amazon EC2 executando o Windows Server 2012 R2 é implantada como o servidor de configuração. Por padrão, os outros componentes do Azure Site Recovery (servidor de processo e servidor de destino mestre) são instalados quando você implanta o servidor de configuração. Este artigo descreve as etapas para migração no Portal do Azure, que se baseiam nas instruções para [Saiba mais](site-recovery-components.md)

* **Instâncias EC2**: as instâncias de máquinas virtuais do Amazon EC2 que você quer migrar.

## <a name="deployment-steps"></a>Etapas de implantação.

1. Crie um cofre dos Serviços de Recuperação.
2. O Grupo de segurança das suas instâncias do EC2 devem ter regras configuradas para permitir a comunicação entre a instância do EC2 que você deseja migrar e a instância na qual você planeja implantar o Servidor de configuração.

3. Na mesma nuvem privada virtual do Amazon que suas instâncias do EC2, implante um servidor de configuração de ASR. Consulte o VMware/Físico para os pré-requisitos do Azure para requisitos de implantação do servidor de configuração.

    ![DeployCS](./media/site-recovery-migrate-aws-to-azure/migration_pic2.png)

4.  Quando o servidor de configuração for implantado em AWS e registrado com o cofre dos Serviços de Recuperação, você deverá ver o servidor de configuração e o servidor de processo sob a infraestrutura do Site Recovery conforme mostrado abaixo:

    ![CSinVault](./media/site-recovery-migrate-aws-to-azure/migration_pic3.png)

5. Depois que você implantou o servidor de configuração (ele pode levar 15 minutos para ser exibido), valide se ele pode se comunicar com a VM que você deseja migrar.

6. [Defina as configurações de replicação](site-recovery-setup-replication-settings-vmware.md).

7. Habilitar replicação: habilite a replicação para as VMs que você deseja migrar. Você pode descobrir as instâncias de EC2 usando os endereços IP privados, que podem ser obtidos no console EC2.

    ![SelectVM](./media/site-recovery-migrate-aws-to-azure/migration_pic4.png)

8. Depois que as instâncias de EC2 tiverem sido protegidas e a replicação para o Azure for concluída, [execute um Failover de teste](site-recovery-test-failover-to-azure.md) para validar o desempenho do aplicativo no Azure.

    ![TFI](./media/site-recovery-migrate-aws-to-azure/migration_pic5.png)

9. Execute um failover do AWS para o Azure para cada VM. Opcionalmente, é possível criar um plano de recuperação e executar um failover para migrar várias máquinas virtuais do AWS para o Azure. [Leia mais](site-recovery-create-recovery-plans.md) sobre planos de recuperação.

10. Para a migração, você não precisa confirmar um failover. Em vez disso, você seleciona a opção Migração Completa para cada computador que deseja migrar. A ação Concluir Migração conclui o processo de migração, remove a replicação do computador e interrompe a cobrança do Site Recovery para o computador.

    ![Migrar](./media/site-recovery-migrate-aws-to-azure/migration_pic6.png)

## <a name="next-steps"></a>Próximas etapas

- [Prepare as máquinas migradas para habilitar a replicação](site-recovery-azure-to-azure-after-migration.md) em outra região para necessidades de recuperação de desastres.
- Inicie a proteção de suas cargas de trabalho ao [replicar máquinas virtuais do Azure.](site-recovery-azure-to-azure.md)
