---
title: "Como criar uma nova proteção de failover para máquinas virtuais do Azure para a região primária do Azure | Microsoft Docs"
description: "Após o failover das VMs de uma região do Azure para outra, você pode usar o Azure Site Recovery para proteger as máquinas na direção inversa. Aprenda as etapas para criar uma nova proteção antes de realizar um failback novamente."
services: site-recovery
documentationcenter: 
author: ruturaj
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/11/2017
ms.author: ruturajd
ms.openlocfilehash: 32f5d2d142940bc515849dcd0edb1bb1f152aa6d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="reprotect-from-failed-over-azure-region-back-to-primary-region"></a>A nova proteção de failover da região do Azure para a região primária



>[!NOTE]
>
> Atualmente, a replicação do Site Recovery para máquinas virtuais do Azure está em versão prévia.


## <a name="overview"></a>Visão geral
Quando você realiza um [failover](site-recovery-failover.md) nas máquinas virtuais de uma região do Azure para outra, as máquinas virtuais estão em um estado desprotegido. Se você deseja colocá-las de volta na região primária, é necessário primeiro proteger as máquinas virtuais e realizar failover novamente. Não há nenhuma diferença entre como realizar failover em uma direção ou outra. Da mesma forma, após habilitar a proteção das máquinas virtuais, não há nenhuma diferença entre a nova proteção após o failover ou após o failback.
Para explicar os fluxos de trabalho da nova proteção e para evitar confusão, usaremos o local primário dos computadores protegidos como região Ásia Oriental e o local de recuperação dos computadores como região Sudeste Asiático. Durante o failover, você executará o failover das máquinas virtuais para a região Ásia Oriental. Antes de realizar o failback, você precisa proteger novamente as máquinas virtuais do Sudeste Asiático para a Ásia Oriental. Este artigo descreve as etapas sobre como criar uma nova proteção.

> [!WARNING]
> Se você tiver [concluído a migração](site-recovery-migrate-to-azure.md#what-do-we-mean-by-migration), movido a máquina virtual para outro grupo de recursos ou excluídos a máquina virtual do Azure, não será possível aplicar failback depois disso.

Depois que a nova proteção for concluída e a replicação de máquinas virtuais protegidas, você pode iniciar um failback nas máquinas virtuais para trazê-las de volta para a Ásia Oriental.

Publique comentários ou perguntas no final deste artigo ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="prerequisites"></a>Pré-requisitos
1. As máquinas virtuais devem ter sido confirmadas.
2. O local de destino, nesse caso, a região do Azure Ásia Oriental, deve estar disponível e ser capaz de acessar/criar novos recursos nessa região.

## <a name="steps-to-reprotect"></a>Etapas para proteger novamente

As etapas a seguir são para a nova proteção de uma máquina virtual usando os padrões.

1. Em **Vault** > **Itens replicados**, clique com o botão direito do mouse na máquina virtual em que foi executado o failover e, em seguida, selecione **Proteger Novamente**. Também é possível clicar no computador e selecionar **Proteger Novamente** nos botões de comando.

![Clique com o botão direito do mouse para proteger novamente](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotect.png)

2. Na folha, observe que a direção da proteção **Sudeste Asiático para Ásia Oriental** já está marcada.

![Folha da nova proteção](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotectblade.png)

3. Examine as informações de **Grupo de recursos, rede, conjuntos de armazenamento e a disponibilidade** e clique em OK. Se houver quaisquer recursos marcados (novo), eles serão criados como parte da nova a proteção.

Isso será um trabalho de gatilho da nova proteção que fará a propagação primeiro do local de destino (SEA neste caso) com os dados mais recentes e depois que for concluída, replicará os deltas antes do failover voltar para o Sudeste Asiático.

### <a name="reprotect-customization"></a>Personalização da nova proteção
Se quiser escolher a conta de armazenamento de extração ou a rede durante a nova proteção, você pode fazer isso usando a opção personalizar fornecida na folha da nova proteção.

![Opção de personalização](./media/site-recovery-how-to-reprotect-azure-to-azure/customize.png)

Você pode personalizar as propriedades a seguir da máquina virtual de destino durante a nova proteção.

![Folha de personalização](./media/site-recovery-how-to-reprotect-azure-to-azure/customizeblade.png)

|Propriedade |Observações  |
|---------|---------|
|Grupo de recursos de destino     | Você pode optar por alterar o grupo de recursos de destino no qual a máquina virtual será criada. Como parte da nova proteção, a máquina virtual de destino será excluída, portanto, você pode escolher um novo grupo de recursos no qual você pode criar a VM depois do failover         |
|Rede virtual de destino     | A rede não pode ser alterada durante a nova proteção. Para alterar a rede, refaça o mapeamento de rede.         |
|Armazenamento de destino     | Você pode alterar a conta de armazenamento à qual a máquina virtual será criada depois do failover.         |
|Armazenamento de cache     | Você pode especificar uma conta de armazenamento de cache que será usada durante a replicação. Se você usar os padrões, uma nova conta de armazenamento de cache será criada, se ela ainda não existir.         |
|Conjunto de disponibilidade     |Se a máquina virtual na Ásia Oriental fizer parte de um conjunto de disponibilidade, você pode escolher um conjunto de disponibilidade para a máquina virtual de destino do Sudeste Asiático. Os padrões encontrarão o conjunto de disponibilidade SEA existente e tentarão usá-lo. Durante a personalização, você pode especificar um conjunto AV totalmente novo.         |


### <a name="what-happens-during-reprotect"></a>O que acontece durante a nova proteção?

Após habilitar a primeira proteção, a seguir estão os artefatos que são gerados se você usar os padrões.
1. Uma conta de armazenamento de cache é criada na região Ásia Oriental.
2. Se a conta de armazenamento de destino (a conta de armazenamento original da VM do Sudeste Asiático) não existir, uma nova será criada. O nome é a conta de armazenamento da máquina virtual da Ásia Oriental com o sufixo "asr".
3. Se o conjunto de destino AV não existir e os padrões detectarem que precisa criar um novo conjunto AV, então ele será criado como parte do trabalho da nova proteção. Se você personalizou a nova proteção, o conjunto AV selecionado será usado.
4.

A seguir estão a lista de etapas que ocorrem quando você disparar um trabalho da nova proteção. Este é o caso no qual a máquina virtual lateral de destino existe.

1. Os artefatos necessários são criados como parte da nova proteção. Se já existirem, eles serão reutilizados.
2. A máquina virtual lateral (Sudeste Asiático) de destino é primeiro desativada, se ela estiver em execução.
3. O disco da máquina virtual lateral de destino é copiado pelo Azure Site Recovery em um contêiner como um blob de semeadura.
4. A máquina virtual lateral de destino é excluída.
5. O blob de semeadura é usado pela máquina virtual lateral (Ásia Oriental) de origem atual para replicar. Isso garante que apenas deltas sejam replicadas.
6. As principais alterações entre o disco de origem e o blob de semeadura são sincronizadas. Elas podem levar algum tempo para ser concluída.
7. Depois de concluído o trabalho da nova proteção, a replicação delta começa a criar um ponto de recuperação de acordo com a política.

> [!NOTE]
> Não é possível proteger em um nível do plano de recuperação. Você só pode proteger novamente em um nível de VM.

Depois que o Proteja Novamente for bem-sucedido, a máquina virtual entrará no estado protegido.

## <a name="next-steps"></a>Próximas etapas

Depois que a máquina virtual entrou em um estado protegido, você poderá iniciar um failover. O failover desligará a máquina virtual na região do Azure Ásia Oriental e, em seguida, criará e inicializará a máquina virtual da região Sudeste Asiático. Portanto, há um pequeno tempo de inatividade do aplicativo. Sendo assim, faça o failover quando seu aplicativo puder passar por um tempo de inatividade. É recomendável que você teste primeiro o failover da máquina virtual para certificar-se de que ele está chegando corretamente, antes de iniciar um failover.

-   [Etapas para iniciar o failover de teste da máquina virtual](site-recovery-test-failover-to-azure.md)

-   [Etapas para iniciar o failover da máquina virtual](site-recovery-failover.md)
