---
title: Selecione uma opção de migração VMware com migração de servidor de migrações para Azure
description: Fornece uma visão geral das opções para migrar VMs VMware para o Azure com migração de servidor de migrações para Azure
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 7446b2050fdd7bbc7704953c053da0629231191c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101715115"
---
# <a name="select-a-vmware-migration-option"></a>Selecione uma opção de migração do VMware

É possível migrar VMs VMware para o Azure usando a ferramenta de Migração de Servidor de Migrações para Azure. Essa ferramenta oferece algumas opções para a migração de VM VMware:

- Migração usando a replicação sem agente. Migre VMs sem precisar instalar nada nelas.
- Migração com um agente para replicação. Instale um agente na VM para replicação.


## <a name="compare-migration-methods"></a>Comparar métodos de migração

Use essas comparações selecionadas para ajudá-lo a decidir qual método usar. Você também pode examinar os requisitos de suporte completo para migração [baseada em](migrate-support-matrix-vmware-migration.md#agent-based-migration) agente e sem [agentes](migrate-support-matrix-vmware-migration.md#agentless-migration) .

**Configuração** | **Sem agente** | **Baseado em agente**
--- | --- | ---
**Permissões do Azure** | Você precisa de permissões para criar um projeto de migrações para Azure e registrar os aplicativos do Azure AD criados ao implantar o dispositivo de migrações para Azure. | Você precisa de permissões de colaborador na assinatura do Azure. 
**Replicação** | Um máximo de 500 VMs pode ser replicado simultaneamente de um vCenter Server. No portal, você pode selecionar até 10 computadores ao mesmo tempo para replicação. Para replicar mais computadores, adicione lotes de 10.| A capacidade de replicação aumenta com a expansão do dispositivo de replicação.
**Implantação do dispositivo** | O [dispositivo migrações para Azure](migrate-appliance.md) é implantado localmente. | O [dispositivo de replicação de migrações para Azure](migrate-replication-appliance.md) é implantado localmente.
**Compatibilidade Site Recovery** | Compatíveis. | Você não poderá replicar com a migração de servidor de migrações para Azure se tiver configurado a replicação para um computador usando Site Recovery.
**Disco de destino** | Discos gerenciados | Discos gerenciados
**Limites de disco** | Disco do sistema operacional: 2 TB<br/><br/> Disco de dados: 32 TB<br/><br/> Máximo de discos: 60 | Disco do sistema operacional: 2 TB<br/><br/> Disco de dados: 32 TB<br/><br/> Máximo de discos: 63
**Discos de passagem** | Sem suporte | Com suporte
**Inicialização UEFI** | Com suporte. | Com suporte.

## <a name="compare-deployment-steps"></a>Comparar etapas de implantação

Depois de revisar as limitações, entender as etapas envolvidas na implantação de cada solução pode ajudá-lo a decidir qual opção escolher.

**Tarefa** | **Detalhes** |**Sem agente** | **Baseado em agente**
--- | --- | --- | ---
**Implantar o dispositivo das Migrações para Azure** | Um dispositivo leve executado em uma VM VMware.<br/><br/> O dispositivo é usado para descobrir e avaliar computadores e migrar computadores usando a migração sem agente. | Obrigatórios.<br/><br/> Se você já configurou o dispositivo para avaliação, poderá usar o mesmo dispositivo para a migração sem agente. | Não necessário.<br/><br/> Se você tiver configurado um dispositivo para avaliação, poderá deixá-lo em vigor ou removê-lo se terminar a avaliação.
**Usar a ferramenta de avaliação do servidor** | Avaliar computadores com a ferramenta migrações para Azure: Server Assessment. | A avaliação é opcional. | A avaliação é opcional.
**Usar a ferramenta de migração de servidor** | Adicione a ferramenta de migração de servidor de migrações para Azure no projeto de migrações para Azure. | Obrigatório | Obrigatório
**Preparar o VMware para migração** | Defina as configurações em servidores VMware e VMs. | Obrigatório | Obrigatório
**Instalar o serviço de mobilidade em VMs** | O serviço de mobilidade é executado em cada VM que você deseja replicar | Não é necessária | Obrigatório
**Implantar o dispositivo de replicação** | O [dispositivo de replicação](migrate-replication-appliance.md) é usado para a migração baseada em agente. Ele se conecta entre o serviço de mobilidade em execução em VMs e a migração de servidor. | Não é necessária | Obrigatório
**Replicar VMs**. Habilite a replicação da VM. | Definir as configurações de replicação e selecionar as VMs a serem replicadas | Obrigatório | Obrigatório
**Execute um teste de migração** | Executar uma migração de teste para verificar se tudo está funcionando conforme o esperado. | Obrigatório | Obrigatório
**Executar uma migração completa** | Migre as VMs. | Obrigatório | Obrigatório



## <a name="next-steps"></a>Próximas etapas

[Migre VMs VMware](tutorial-migrate-vmware.md) com migração sem agente.



