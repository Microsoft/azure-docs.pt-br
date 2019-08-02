---
title: Configurar replicação para VMs do Azure no Azure Site Recovery | Microsoft Docs
description: Este artigo descreve como configurar a replicação para VMs do Azure, de uma região do Azure para outra, usando o Site Recovery.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/29/2018
ms.author: asgang
ms.openlocfilehash: 19a1e13815f1d83f13392892dd9c87b84a40a1c1
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67551688"
---
# <a name="replicate-azure-vms-to-another-azure-region"></a>Replicar VMs do Azure para outra região do Azure


Este artigo descreve como habilitar a replicação de VMs do Azure, de uma região do Azure para outra.

## <a name="before-you-start"></a>Antes de começar

Este artigo pressupõe que você preparou para a implantação da recuperação de Site, conforme descrito na [tutorial de recuperação de desastre do Azure para](azure-to-azure-tutorial-enable-replication.md).

Pré-requisitos devem estar em vigor, e você deverá ter criado um cofre dos serviços de recuperação.


## <a name="enable-replication"></a>Habilitar a replicação

Habilite a replicação. Este procedimento pressupõe que a região do Azure primária seja Leste da Ásia e a região secundária seja Sudeste Asiático.

1. No cofre, clique em **+Replicar**.
2. Observe os seguintes campos:
   - **Fonte**: O ponto de origem das VMs que, neste caso, é o **Azure**.
   - **Local de origem**: A região do Azure de onde você deseja proteger suas VMs. Para esta ilustração, a localização da fonte é 'Ásia Oriental'
   - **Modelo de implantação**: O modelo de implantação do Azure para os computadores de origem.
   - **Assinatura do Azure**: A assinatura à qual pertencem suas VMs de origem. Pode ser qualquer assinatura dentro do mesmo locatário do Azure Active Directory na qual existe o cofre dos serviços de recuperação.
   - **Grupo de Recursos**: O grupo de recursos ao qual suas máquinas virtuais de origem pertencem. Todas as máquinas virtuais do grupo de recursos selecionado são listadas para a proteção na próxima etapa.

     ![Habilitar a replicação](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

3. Em **Máquinas Virtuais > Selecionar máquinas virtuais**, clique e selecione cada VM que você deseja replicar. Você só pode selecionar computadores para os quais a replicação pode ser habilitada. Em seguida, clique em **OK**.
    ![Habilitar a replicação](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)

4. Em **Configurações**, você também pode definir configurações do site de destino:

   - **Local de Destino**: O local no qual seus dados de máquina virtual de origem serão replicados. Dependendo de seu local de máquinas selecionadas, a recuperação de Site fornece a lista de regiões de destino adequado. É recomendável que você mantenha o local de destino o mesmo do local do cofre dos Serviços de Recuperação.
   - **Assinatura de destino**: A assinatura de destino usada na recuperação de desastres. Por padrão, a assinatura de destino será o mesma que a assinatura de origem.
   - **Grupo de recursos de destino**: O grupo de recursos ao qual pertencem todas as máquinas virtuais replicadas.
       - Por padrão o Site Recovery cria um novo grupo de recursos na região de destino com um sufixo "asr" no nome.
       - Se o grupo de recursos criado pelo Site Recovery já existir, ele será reutilizado.
       - Você pode personalizar as configurações de grupo de recursos.
       - O local do grupo de recursos de destino pode ser qualquer região do Azure, exceto a região na qual as VMs de origem são hospedadas.
   - **Rede virtual de destino**: Por padrão, o Site Recovery cria uma nova rede virtual na região de destino com um sufixo "asr" no nome. Isso é mapeado para sua rede de origem e usado para qualquer proteção futura. [Saiba mais](site-recovery-network-mapping-azure-to-azure.md) sobre o mapeamento de rede.
   - **Contas de armazenamento (a VM de origem não usa discos gerenciados) de destino**: Por padrão, o Site Recovery cria uma nova conta de armazenamento de destino imitando suas configurações de armazenamento da VM de origem. Caso a conta de armazenamento já exista, ela é reutilizada.
   - **Discos gerenciados de réplica (a VM de origem usa discos gerenciados)** : Site Recovery cria novos discos gerenciados de réplica na região de destino para espelhar os discos gerenciados da VM de origem com o mesmo tipo de armazenamento (Standard ou premium) a VM do disco gerenciado de origem.
   - **Contas de armazenamento em cache**: O Site Recovery precisa de uma conta de armazenamento adicional, chamada de armazenamento em cache na região de origem. Todas as alterações ocorrendo nas máquinas virtuais de origem são controladas e enviadas para a conta de armazenamento do cache antes de replicar para o local de destino. Essa conta de armazenamento deve ser o padrão.
   - **Conjuntos de disponibilidade de destino**: Por padrão, o Site Recovery cria uma novo conjunto de disponibilidade na região de destino com o sufixo "asr" no nome, para VMs que fazem parte de uma conjunto de disponibilidade na região de origem. Se o conjunto de disponibilidade criado pelo Site Recovery já existir, ele será reutilizado.
   - **Zonas de disponibilidade de destino**: Por padrão, o Site Recovery atribui o mesmo número da zona que a região de origem na região de destino se a região de destino oferecer suporte a zonas de disponibilidade.

     Se a região de destino não oferecer suporte a zonas de disponibilidade, as VMs de destino são configuradas como instâncias isoladas por padrão. Se necessário, você pode configurar essas VMs como parte dos conjuntos de disponibilidade na região de destino, clicando em “Personalizar”.

     >[!NOTE]
     >Depois de habilitar a replicação, não é possível alterar o tipo de disponibilidade — única instância, zona de disponibilidade ou conjunto de disponibilidade. É preciso desabilitar e habilitar a replicação para alterar o tipo de disponibilidade.
     >
    
   - **Política de replicação**: Define as configurações para o histórico de retenção do ponto de recuperação e frequência de instantâneo consistente do aplicativo. Por padrão, o Azure Site Recovery cria uma nova política de replicação com configurações padrão de ' 24 horas para retenção de ponto de recuperação e frequência do instantâneo '4 horas' para consistente com o aplicativo.

     ![Habilitar a replicação](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)

### <a name="enable-replication-for-added-disks"></a>Habilitar a replicação para os discos adicionados

Se você adicionar discos a uma VM do Azure para o qual a replicação é habilitada, ocorre o seguinte:
-   Integridade da replicação da VM mostra um aviso e informa a uma observação informando que um ou mais discos estão disponíveis para proteção.
-   Se você habilitar a proteção para os discos adicionados, o aviso desaparecerá após a replicação inicial do disco.
-   Se você optar por não habilitar a replicação para o disco, você pode selecionar para ignorar o aviso.

    
    ![Novo disco adicionado](./media/azure-to-azure-how-to-enable-replication/newdisk.png)

Para habilitar a replicação para o disco adicionado, faça o seguinte:

1.  No cofre > **itens replicados**, clique na VM à qual você adicionou o disco.
2.  Clique em **discos**e, em seguida, selecione o disco de dados para o qual você deseja habilitar a replicação (esses discos têm um **não protegido** status).
3.  Na **detalhes do disco**, clique em **habilitar a replicação**.

    ![Habilitar a replicação para o disco adicionado](./media/azure-to-azure-how-to-enable-replication/enabled-added.png)

Após o trabalho de replicação de habilitação é executado e a replicação inicial terminar, o aviso de integridade de replicação para o problema de disco é removido.


  
## <a name="customize-target-resources"></a>Personalizar os recursos de destino

Você pode modificar as configurações de destino padrão usadas pelo Site Recovery.

1. Clique em **Personalizar:** ao lado de "Assinatura de destino" para modificar a assinatura de destino padrão. Selecione a assinatura na lista de todas as assinaturas disponíveis no mesmo locatário do AAD (Azure Active Directory).

2. Clique em **Personalizar:** para modificar as configurações padrão:
    - Em **Grupo de recursos de destino**, selecione o grupo de recursos da lista de todos os grupos de recursos no local de destino da assinatura.
    - Em **Rede virtual de destino**, selecione a rede em uma lista de todas as redes virtuais no local de destino.
    - Em **Conjunto de disponibilidade**, você pode adicionar configurações do conjunto de disponibilidade à VM, caso elas façam parte de um conjunto de disponibilidade na região de origem.
    - Em **Contas de armazenamento de destino**, selecione a conta que você deseja usar.

        ![Habilitar a replicação](./media/site-recovery-replicate-azure-to-azure/customize.PNG)
3. Clique em **Personalizar** para modificar as configurações de replicação.
4. Na **consistência de várias VMs**, selecione as VMs que você deseja replicar juntos.
    - Todos os computadores de um grupo de replicação terão pontos de recuperação consistentes com o aplicativo e consistentes com falhas quando passarem por failover.
    - Habilitar a consistência de várias VMs pode afetar o desempenho da carga de trabalho (como ele é intensivo de CPU). Ele deve ser habilitado apenas se os computadores estão executando a mesma carga de trabalho, e você precisar de consistência entre várias máquinas.
    - Por exemplo, se um aplicativo tiver 2 máquinas de virtuais do SQL Server e dois servidores web, você deve adicionar apenas as VMs do SQL Server para um grupo de replicação.
    - Você pode optar por ter um máximo de 16 VMs em um grupo de replicação.
    - Se você habilitar a consistência de várias VMs, as máquinas virtuais no grupo de replicação se comunicarão entre si pela porta 20004.
    - Certifique-se de que não há nenhum dispositivo de firewall bloqueando a comunicação interna entre as VMs pela porta 20004.
    - Se você quiser fazer parte de um grupo de replicação de VMs do Linux, verifique se que o tráfego de saída na porta 20004 manualmente é aberto de acordo com a orientação para a versão específica do Linux.
![Habilitar a replicação](./media/site-recovery-replicate-azure-to-azure/multivmsettings.PNG)
    
5. Clique em **Criar o recurso de destino** > **Habilitar Replicação**.
6. Depois que as VMs forem habilitadas para replicação, você poderá verificar o status da integridade da VM em **Itens replicados**

>[!NOTE]
>Durante a replicação inicial, o status pode levar algum tempo para atualizar, sem andamento. Clique no botão **Atualizar** para obter o status mais recente.
>

# <a name="next-steps"></a>Próximas etapas

[Saiba mais](site-recovery-test-failover-to-azure.md) sobre a execução de failovers de teste.
