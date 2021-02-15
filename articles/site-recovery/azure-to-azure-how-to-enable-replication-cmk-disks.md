---
title: Habilitar a replicação de VMs do Azure criptografadas no Azure Site Recovery
description: Este artigo descreve como configurar a replicação para VMs com discos habilitados para CMK (chave gerenciada pelo cliente) de uma região do Azure para outra usando Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 07/10/2020
ms.author: mayg
ms.openlocfilehash: 9f9052f51c5bab0ea738e9fd15d8f62f45ff0c9b
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/01/2020
ms.locfileid: "93146528"
---
# <a name="replicate-machines-with-customer-managed-keys-cmk-enabled-disks"></a>Replicar máquinas com discos habilitados para Chaves Gerenciadas pelo Cliente (CMK)

Este artigo descreve como replicar VMs do Azure com CMK (chaves de Customer-Managed) habilitadas para discos gerenciados, de uma região do Azure para outra.

## <a name="prerequisite"></a>Pré-requisito
Você deve criar os conjuntos de criptografia de disco na região de destino para a assinatura de destino antes de habilitar a replicação para suas máquinas virtuais que têm discos gerenciados habilitados para CMK.

## <a name="enable-replication"></a>Habilitar a replicação

Para este exemplo, a região primária do Azure é Ásia Oriental e a região secundária é Ásia Oriental do Sul.

1. No cofre, selecione **+ replicar** .
2. Observe os campos a seguir.
    - **Fonte** : o ponto de origem das VMs que, neste caso, é o **Azure** .
    - **Local de origem** : a região do Azure onde você deseja proteger suas máquinas virtuais. Para este exemplo, o local de origem é "Ásia Oriental".
    - **Modelo de implantação** : o modelo de implantação do Azure dos computadores de origem.
    - **Assinatura de origem** : a assinatura à qual suas máquinas virtuais de origem pertencem. Pode ser qualquer assinatura que esteja no mesmo locatário Azure Active Directory como seu cofre de serviços de recuperação.
    - **Grupo de recursos** : o grupo de recursos ao qual pertencem as máquinas virtuais de origem. Todas as VMs no grupo de recursos selecionado são listadas para proteção na próxima etapa.

3. Em **máquinas virtuais**  >  **selecione máquinas virtuais** , selecione cada VM que você deseja replicar. Você só pode selecionar computadores para os quais a replicação pode ser habilitada. Depois, selecione **OK** .

4. Em **configurações** , você pode definir as seguintes configurações de site de destino.

    - **Local de destino** : o local onde os dados da máquina virtual de origem serão replicados. Site Recovery fornece uma lista de regiões de destino adequadas com base no local da máquina selecionada. Recomendamos que você use o mesmo local que o local do cofre dos serviços de recuperação.
    - **Assinatura de destino** : a assinatura de destino que é usada para recuperação de desastre. Por padrão, a assinatura de destino será igual à assinatura de origem.
    - **Grupo de recursos de destino** : o grupo de recursos ao qual pertencem todas as máquinas virtuais replicadas. Por padrão, Site Recovery cria um novo grupo de recursos na região de destino. O nome Obtém o `asr` sufixo. Se já existir um grupo de recursos que foi criado por Azure Site Recovery, ele será reutilizado. Você também pode optar por personalizá-lo, conforme mostrado na seção a seguir. O local do grupo de recursos de destino pode ser qualquer região do Azure, exceto a região onde as máquinas virtuais de origem estão hospedadas.
    - **Rede virtual de destino** : por padrão, site Recovery cria uma nova rede virtual na região de destino. O nome Obtém o `asr` sufixo. Ele é mapeado para sua rede de origem e usado para qualquer proteção futura. [Saiba mais](./azure-to-azure-network-mapping.md) sobre o mapeamento de rede.
    - **Contas de armazenamento de destino (se a VM de origem não usa discos gerenciados)** : por padrão, site Recovery cria uma nova conta de armazenamento de destino, imitandondo sua configuração de armazenamento de VM de origem. Se uma conta de armazenamento já existir, ela será reutilizada.
    - **Discos gerenciados de réplica (se a VM de origem usar discos gerenciados)** : Site Recovery cria novos discos gerenciados de réplica na região de destino para espelhar os discos gerenciados da VM de origem do mesmo tipo de armazenamento (Standard ou Premium) que os discos gerenciados da VM de origem.
    - **Contas de armazenamento em cache** : Site Recovery precisa de uma conta de armazenamento extra chamada *armazenamento em cache* na região de origem. Todas as alterações nas VMs de origem são rastreadas e enviadas para a conta de armazenamento de cache. Em seguida, eles são replicados para o local de destino.
    - **Conjunto de disponibilidade** : por padrão, site Recovery cria um novo conjunto de disponibilidade na região de destino. O nome tem o `asr` sufixo. Se um conjunto de disponibilidade criado pelo Site Recovery já existir, ele será reutilizado.
    - **Des (conjuntos de criptografia de disco)** : Site Recovery precisa que os conjuntos de criptografia de disco sejam usados para réplica e discos gerenciados de destino. Você deve criar previamente o DES na assinatura de destino e na região de destino antes de habilitar a replicação. Por padrão, um DES não é selecionado. Você deve clicar em ' Personalizar ' para escolher um DES por disco de origem.
    - **Política de replicação** : define as configurações para o histórico de retenção do ponto de recuperação e a frequência do instantâneo consistente com o aplicativo. Por padrão, Site Recovery cria uma nova política de replicação com configurações padrão de *24 horas* para retenção de ponto de recuperação e *60 minutos* para frequência de instantâneo consistente com o aplicativo.

    ![Habilitar a replicação para computador com discos habilitados para CMK](./media/azure-to-azure-how-to-enable-replication-cmk-disks/cmk-enable-dr.png)

## <a name="customize-target-resources"></a>Personalizar os recursos de destino

Siga estas etapas para modificar as Site Recovery configurações de destino padrão.

1. Selecione **Personalizar** ao lado de "assinatura de destino" para modificar a assinatura de destino padrão. Selecione a assinatura na lista de assinaturas que estão disponíveis no locatário do Azure AD.

2. Selecione **Personalizar** ao lado de "grupo de recursos, rede, armazenamento e conjuntos de disponibilidade" para modificar as seguintes configurações padrão:
    - Para **grupo de recursos de destino** , selecione o grupo de recursos na lista de grupos de recursos no local de destino da assinatura.
    - Para **rede virtual de destino** , selecione a rede em uma lista de redes virtuais no local de destino.
    - Para o **conjunto de disponibilidade** , você pode adicionar configurações de conjunto de disponibilidade à VM, se elas fizerem parte de um conjunto de disponibilidade na região de origem.
    - Para **contas de armazenamento de destino** , selecione a conta a ser usada.

3. Selecione **Personalizar** ao lado de "configurações de criptografia de armazenamento" para selecionar o des de destino para cada disco gerenciado de origem gerenciado por CMK (chave gerenciada pelo cliente). No momento da seleção, você também poderá ver a qual cofre de chaves de destino o DES está associado.

4. Selecione **criar recurso de destino**  >  **habilitar replicação** .
5. Depois que as VMs estiverem habilitadas para replicação, você poderá verificar o status de integridade das VMs em **itens replicados** .

![Captura de tela que mostra onde verificar o status de integridade das VMs.](./media/azure-to-azure-how-to-enable-replication-cmk-disks/cmk-customize-target-disk-properties.png)

>[!NOTE]
>Durante a replicação inicial, o status pode levar algum tempo para ser atualizado, sem um progresso aparente. Clique em **Atualizar**  para obter o status mais recente.

## <a name="faqs"></a>Perguntas frequentes

* Eu habilitei o CMK em um item replicado existente, como posso garantir que o CMK também seja aplicado na região de destino?

    Você pode descobrir o nome do disco gerenciado de réplica (criado por Azure Site Recovery na região de destino) e anexar o DES a esse disco de réplica. No entanto, você não poderá ver os detalhes do DES na folha discos depois de anexá-lo. Como alternativa, você pode optar por desabilitar a replicação da VM e habilitá-la novamente. Ele garantirá que você veja os detalhes de DES e do cofre de chaves na folha discos do item replicado.

* Adicionei um novo disco habilitado para CMK ao item replicado. Como replicar este disco com Azure Site Recovery?

    Não há suporte para a adição de um novo disco habilitado para CMK a um item replicado existente. Desabilite a replicação e habilite a replicação novamente para a máquina virtual.

* Habilitei a plataforma e as chaves gerenciadas pelo cliente, como posso proteger meus discos?

    Habilitar a criptografia dupla com a plataforma e as chaves gerenciadas pelo cliente é suppprted por Site Recovery. Siga as instruções neste artigo para proteger seu computador. Você precisa criar um DES com criptografia dupla habilitada na região de destino com antecedência. No momento da habilitação da replicação para essa VM, você pode fornecer esse DES para Site Recovery.

