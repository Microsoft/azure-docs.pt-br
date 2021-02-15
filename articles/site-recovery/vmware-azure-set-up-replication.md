---
title: Configurar políticas de replicação para a recuperação de desastres do VMware com o Azure Site Recovery | Microsoft Docs
description: Descreve como definir configurações de replicação para recuperação de desastre do VMware para o Azure com o Azure Site Recovery.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: sutalasi
ms.openlocfilehash: 45921bdf802a649b7b802f44d2842a543e44f02b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "84699593"
---
# <a name="configure-and-manage-replication-policies-for-vmware-disaster-recovery"></a>Configurar e gerenciar políticas de replicação para recuperação de desastre do VMware

Este artigo descreve como configurar uma política de replicação quando você replicar VMs do VMware no Azure, usando o [Azure Site Recovery](site-recovery-overview.md).

## <a name="create-a-policy"></a>Criar uma política

1. Selecione **gerenciar**  >  **infraestrutura de site Recovery**.
2. Em **Para o VMware e Computadores físicos**, selecione **Políticas de replicação**.
3. Clique em **+Política de replicação** e especifique o nome da política.
4. Em **limite de RPO**, especifique o limite de RPO. Os alertas serão gerados quando a replicação contínua excede esse limite.
5. Em **Retenção do ponto de recuperação**, especifique (em horas) a duração da janela de retenção para cada ponto de recuperação. Os computadores protegidos podem ser recuperados para qualquer ponto em uma janela de retenção. Há suporte para até 24 horas de retenção para computadores replicados no armazenamento premium. Há suporte para até 72 horas no armazenamento standard.
6. Em **frequência de instantâneo consistente com o aplicativo**, escolha na lista suspensa com que frequência (em horas) os pontos de recuperação que contêm instantâneos consistentes com o aplicativo devem ser criados. Se você quiser desativar a geração de pontos de consistência do aplicativo, escolha o valor "off" na lista suspensa.
7. Clique em **OK**. A política deve ser criada em 30 a 60 segundos.

Quando você cria uma política de replicação, uma política de replicação de failback correspondente é criada automaticamente, com o sufixo "failback". Depois de criar a política, você pode editá-la selecionando > **Editar Configurações**.

## <a name="associate-a-configuration-server"></a>Associar um servidor de configuração

Associe a política de replicação ao servidor de configuração local.

1. Clique em **Associar** e selecione o servidor de configuração.

    ![Associar o servidor de configuração](./media/vmware-azure-set-up-replication/associate1.png)
2. Clique em **OK**. O servidor de configuração deve ser associado em um ou dois minutos.

    ![Associação de servidor de configuração](./media/vmware-azure-set-up-replication/associate2.png)

## <a name="edit-a-policy"></a>Editar uma política

Você pode modificar uma política de replicação depois de criá-la.

- As alterações na política são aplicadas a todos os computadores usando a política.
- Se você quiser associar computadores replicados a uma política de replicação diferente, será necessário desabilitar e reabilitar a proteção para os computadores relevantes.

Edite uma política da seguinte maneira:
1. Selecione **gerenciar**  >  **site Recovery**  >  **políticas de replicação**de infraestrutura.
2. Selecione a política de replicação que você deseja modificar.
3. Clique em **Editar configurações**e atualize os campos tempo de retenção do ponto de recuperação/limite de RPO/frequência de instantâneos consistentes com o aplicativo, conforme necessário.
4. Se você quiser desativar a geração de pontos de consistência do aplicativo, escolha o valor "desativado" na lista suspensa do campo **frequência do instantâneo consistente com o aplicativo**.
5. Clique em **Salvar**. A política deve ser atualizada em 30 a 60 segundos.



## <a name="disassociate-or-delete-a-replication-policy"></a>Desassociar ou excluir uma política de replicação

1. Escolha a política de replicação.
    a. Para desassociar a política do servidor de configuração, verifique se nenhum computador replicado está usando a política. Em seguida, clique em **Desassociar**.
    b. Para excluir a política, verifique se ela não está associada a um servidor de configuração. Em seguida, clique em **excluir**. A exclusão deve levar de 30 a 60 segundos.
2. Clique em **OK**.
