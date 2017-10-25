---
title: "Como configurar a replicação geográfica para o Cache Redis do Azure | Microsoft Docs"
description: "Saiba como replicar suas instâncias de Cache Redis do Azure entre regiões geográficas."
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 375643dc-dbac-4bab-8004-d9ae9570440d
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: sdanie
ms.openlocfilehash: 332326ce4188385aa6e569c812e16c3daa68bd5d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-geo-replication-for-azure-redis-cache"></a>Como configurar a replicação geográfica para o Cache Redis do Azure

A Replicação geográfica fornece um mecanismo para vincular duas instâncias de Cache Redis do Azure de camada Premium. Um cache é designado como o cache vinculado primário e o outro como o cache vinculado secundário. O cache vinculado secundário se torna somente leitura e os dados gravados no cache primário são replicados para o cache vinculado secundário. Essa funcionalidade pode ser usada para replicar um cache entre regiões do Azure. Este artigo fornece um guia para configurar a replicação geográfica para suas instâncias de Cache Redis do Azure de camada Premium.

## <a name="geo-replication-prerequisites"></a>Pré-requisitos de replicação geográfica

Para configurar a replicação geográfica entre dois caches, os seguintes pré-requisitos devem ser atendidos:

- Ambos os caches devem ser de [camada Premium](cache-premium-tier-intro.md).
- Ambos os caches devem estar na mesma assinatura do Azure.
- O cache vinculado secundário deve estar no mesmo tipo de preço ou em um tipo de preço maior do que o cache vinculado primário.
- Se o cache vinculado primário tiver clustering habilitado, o cache vinculado secundário deverá ter clustering habilitado com o mesmo número de fragmentos que o cache vinculado primário.
- Ambos os caches devem ser criados em um estado de execução.
- A persistência não deve ser habilitada em nenhum dos caches.
- Há suporte para a replicação geográfica entre caches na mesma VNET. Também há suporte para replicação geográfica entre caches em VNETs diferentes, desde que as duas VNETs sejam configuradas de forma que recursos nas VNETs sejam capazes de alcançar uns aos outros por meio de conexões TCP.

Depois de configurar a replicação geográfica, as seguintes restrições se aplicam para o par de caches vinculados:

- O cache vinculado secundário é somente leitura; você pode ler dele, mas não pode gravar dados nele. 
- Todos os dados que estavam no cache vinculado secundário antes do link ser adicionado são removidos. Se a replicação geográfica for subsequentemente removida, no entanto, os dados replicados permanecerão no cache vinculado secundário.
- Não é possível iniciar uma [operação de dimensionamento](cache-how-to-scale.md) em um cache nem [alterar o número de fragmentos](cache-how-to-premium-clustering.md) se o cache tem o clustering habilitado.
- Você não pode habilitar a persistência em nenhum dos caches.
- Você pode usar [Exportar](cache-how-to-import-export-data.md#export) com qualquer um dos caches, mas você só pode [Importar](cache-how-to-import-export-data.md#import) para o cache vinculado primário.
- Até que você remova o link de replicação geográfica, você não pode excluir nenhum dos caches vinculados nem o grupo de recursos que os contém. Para obter mais informações, consulte [Por que a operação falhou quando tentei excluir meu cache vinculado?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- Se dois caches estão em regiões diferentes, os custos de egresso de rede serão aplicados aos dados replicados entre as regiões para o cache vinculado secundário. Para obter mais informações, consulte [Quanto custa para replicar meus dados entre regiões do Azure?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- Não há nenhum failover automático para o cache vinculado secundário se o cache primário (e sua réplica) ficam inativos. Em ordem para fazer failover de aplicativos cliente, você precisa remover manualmente o link de replicação geográfica e apontar os aplicativos cliente para o cache que anteriormente era o cache vinculado secundário. Para obter mais informações, consulte [Como funciona o failover para o cache vinculado secundário?](#how-does-failing-over-to-the-secondary-linked-cache-work)

## <a name="add-a-geo-replication-link"></a>Adicionar um link de replicação geográfica

1. Para vincular dois caches Premium juntos para replicação geográfica, clique em **Replicação geográfica** no menu de Recursos do cache destinado a ser o cache vinculado primário e, em seguida, clique em **Adicionar link de replicação de cache** da folha **Replicação geográfica**.

    ![Adicionar Link](./media/cache-how-to-geo-replication/cache-geo-location-menu.png)

2. Clique no nome do cache secundário desejado da lista **Caches compatíveis**. Se o cache desejado não está exibido na lista, verifique se os [pré-requisitos de replicação geográfica](#geo-replication-prerequisites) para o cache secundário desejado estão atendidos. Para filtrar os caches por região, clique na região desejada no mapa para exibir apenas esses caches na lista **Caches compatíveis**.

    ![Caches compatíveis de replicação geográfica](./media/cache-how-to-geo-replication/cache-geo-location-select-link.png)
    
    Você também pode iniciar o processo de vinculação ou exibir detalhes sobre o cache secundário por meio do menu de contexto.

    ![Menu de contexto de replicação geográfica](./media/cache-how-to-geo-replication/cache-geo-location-select-link-context-menu.png)

3. Clique em **Vincular** para vincular dois caches e iniciar o processo de replicação.

    ![Vincular caches](./media/cache-how-to-geo-replication/cache-geo-location-confirm-link.png)

4. Você pode exibir o andamento do processo de replicação na folha **Replicação geográfica**.

    ![Status da vinculação](./media/cache-how-to-geo-replication/cache-geo-location-linking.png)

    Você também pode exibir o status de vinculação na folha **Visão geral** para os caches primário e secundário.

    ![Status do cache](./media/cache-how-to-geo-replication/cache-geo-location-link-status.png)

    Quando o processo de replicação é concluído, o **Status da vinculação** é alterado para **Êxito**.

    ![Status do cache](./media/cache-how-to-geo-replication/cache-geo-location-link-successful.png)

    Durante o processo de vinculação, o cache vinculado primário permanece disponível para uso, mas o cache vinculado secundário não está disponível até a conclusão do processo de vinculação.

## <a name="remove-a-geo-replication-link"></a>Remover um vínculo de replicação geográfica

1. Para remover o vínculo entre dois caches e parar a replicação geográfica, clique em **Desvincular caches** na folha **Replicação geográfica**.
    
    ![Desvincular caches](./media/cache-how-to-geo-replication/cache-geo-location-unlink.png)

    Quando o processo de desvinculação for concluído, o cache secundário estará disponível para leituras e gravações.

>[!NOTE]
>Quando o vínculo de replicação geográfica for removido, os dados replicados do cache vinculado primário permanecerão no cache secundário.
>
>

## <a name="geo-replication-faq"></a>Perguntas frequentes de replicação geográfica

- [Posso usar a replicação geográfica com um cache de camada Standard ou Basic?](#can-i-use-geo-replication-with-a-standard-or-basic-tier-cache)
- [O cache está disponível para uso durante o processo de vinculação ou desvinculação?](#is-my-cache-available-for-use-during-the-linking-or-unlinking-process)
- [Posso vincular mais de dois caches juntos?](#can-i-link-more-than-two-caches-together)
- [Posso vincular dois caches de assinaturas do Azure diferentes?](#can-i-link-two-caches-from-different-azure-subscriptions)
- [Posso vincular dois caches com tamanhos diferentes?](#can-i-link-two-caches-with-different-sizes)
- [Posso usar a replicação geográfica com o clustering habilitado?](#can-i-use-geo-replication-with-clustering-enabled)
- [Posso usar a replicação geográfica com meus caches em uma VNET?](#can-i-use-geo-replication-with-my-caches-in-a-vnet)
- [O que é o cronograma de replicação para replicação geográfica do Redis?](#what-is-the-replication-schedule-for-redis-geo-replication)
- [Quanto tempo leva a replicação de replicação geográfica?](#how-long-does-geo-replication-replication-take)
- [O ponto de recuperação de replicação é garantido?](#is-the-replication-recovery-point-guaranteed)
- [Posso usar o PowerShell ou a CLI do Azure para gerenciar a replicação geográfica?](#can-i-use-powershell-or-azure-cli-to-manage-geo-replication)
- [Quanto custa para replicar meus dados entre regiões do Azure?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- [Por que a operação falhou quando tentei excluir meu cache vinculado?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- [Qual região devo usar para meu cache vinculado secundário?](#what-region-should-i-use-for-my-secondary-linked-cache)
- [Como funciona o failover para o cache vinculado secundário?](#how-does-failing-over-to-the-secondary-linked-cache-work)

### <a name="can-i-use-geo-replication-with-a-standard-or-basic-tier-cache"></a>Posso usar a replicação geográfica com um cache de camada Standard ou Basic?

Não, a replicação geográfica está disponível somente para caches de camada Premium.

### <a name="is-my-cache-available-for-use-during-the-linking-or-unlinking-process"></a>O cache está disponível para uso durante o processo de vinculação ou desvinculação?

- Ao vincular dois caches para replicação geográfica, o cache vinculado primário permanece disponível para uso, mas o cache vinculado secundário não está disponível até a conclusão do processo de vinculação.
- Ao remover o link de replicação geográfica entre dois caches, ambos os caches permanecem disponíveis para uso.

### <a name="can-i-link-more-than-two-caches-together"></a>Posso vincular mais de dois caches juntos?

Não. Ao usar a replicação geográfica, só é possível vincular dois caches juntos.

### <a name="can-i-link-two-caches-from-different-azure-subscriptions"></a>Posso vincular dois caches de assinaturas do Azure diferentes?

Não, ambos os caches devem estar na mesma assinatura do Azure.

### <a name="can-i-link-two-caches-with-different-sizes"></a>Posso vincular dois caches com tamanhos diferentes?

Sim, contanto que o cache vinculado secundário seja maior do que o cache vinculado primário.

### <a name="can-i-use-geo-replication-with-clustering-enabled"></a>Posso usar a replicação geográfica com o clustering habilitado?

Sim, desde que ambos os caches tenham o mesmo número de fragmentos.

### <a name="can-i-use-geo-replication-with-my-caches-in-a-vnet"></a>Posso usar a replicação geográfica com meus caches em uma VNET?

Sim, há suporte para replicação geográfica de caches em redes virtuais. 

- Há suporte para a replicação geográfica entre caches na mesma VNET.
- Também há suporte para replicação geográfica entre caches em VNETs diferentes, desde que as duas VNETs sejam configuradas de forma que recursos nas VNETs sejam capazes de alcançar uns aos outros por meio de conexões TCP.

### <a name="what-is-the-replication-schedule-for-redis-geo-replication"></a>O que é o cronograma de replicação para replicação geográfica do Redis?

A replicação não ocorre em um cronograma específico, ela é contínua e assíncrona, isto é, todas as gravações feitas no principal são instantânea e assincronamente replicadas no secundário.

### <a name="how-long-does-geo-replication-replication-take"></a>Quanto tempo leva a replicação de replicação geográfica?

A replicação é contínua, incremental e assíncrona e o tempo gasto geralmente não é muito diferente da latência entre regiões. Em determinadas circunstâncias, às vezes, o secundário pode precisar fazer uma sincronização completa dos dados do primário. O tempo de replicação nesse caso depende de diversos fatores como a carga no cache primário, a largura de banda disponível no computador do cache, a latência entre regiões etc. Por exemplo, com base em alguns testes encontramos que o tempo de replicação para um par com replicação geográfica de 53 GB completo nas regiões do Leste dos EUA e do Oeste dos EUA pode ser algo entre cinco e dez minutos.

### <a name="is-the-replication-recovery-point-guaranteed"></a>O ponto de recuperação de replicação é garantido?

No momento, para caches em um modo de replicação geográfica, a funcionalidade de importação/exportação e persistência está desabilitada. Portanto, no caso de um failover iniciado pelo cliente ou em casos em que o link de replicação foi interrompido entre o par de replicação geográfica, o secundário reterá os dados na memória que ele sincronizou do primário até aquele momento. Não há nenhuma garantia de ponto de recuperação fornecida em tais situações.

### <a name="can-i-use-powershell-or-azure-cli-to-manage-geo-replication"></a>Posso usar o PowerShell ou a CLI do Azure para gerenciar a replicação geográfica?

Neste momento, você só pode gerenciar a replicação geográfica usando o portal do Azure.

### <a name="how-much-does-it-cost-to-replicate-my-data-across-azure-regions"></a>Quanto custa para replicar meus dados entre regiões do Azure?

Ao usar a replicação geográfica, dados do cache vinculado primário são replicados para o cache vinculado secundário. Se os dois caches vinculados estão na mesma região do Azure, não há nenhum encargo para a transferência de dados. Se os dois caches vinculados estão em diferentes regiões do Azure, o encargo pela transferência de dados de replicação geográfica é o custo de largura de banda de replicação de dados para a outra região do Azure. Para obter mais informações, consulte [Detalhes de preço de largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache"></a>Por que a operação falhou quando tentei excluir meu cache vinculado?

Quando dois caches estão vinculados, você não pode excluir nenhum dos caches vinculados nem o grupo de recursos que os contém até que você remova o link de replicação geográfica. Se você tentar excluir o grupo de recursos que contém um ou ambos os caches vinculados, outros recursos no grupo de recursos serão excluídos, mas o grupo de recursos permanecerá no estado `deleting` e quaisquer caches vinculados no grupo de recursos permanecerão no estado `running`. Para concluir a exclusão do grupo de recursos e os caches vinculados dentro dele, interrompa o vínculo de replicação geográfica conforme descrito em [Remover um link de replicação geográfica](#remove-a-geo-replication-link).

### <a name="what-region-should-i-use-for-my-secondary-linked-cache"></a>Qual região devo usar para meu cache vinculado secundário?

Em geral, é recomendável que o cache exista na mesma região do Azure que o aplicativo que o acessa. Se seu aplicativo tiver uma região primária e uma de fallback, seus caches primário e secundário devem existir nessas mesmas regiões. Para obter mais informações sobre regiões emparelhadas, consulte [Melhores práticas – regiões emparelhadas do Azure](../best-practices-availability-paired-regions.md).

### <a name="how-does-failing-over-to-the-secondary-linked-cache-work"></a>Como funciona o failover para o cache vinculado secundário?

Na versão inicial da replicação geográfica, o Cache Redis do Azure não dá suporte a failover automático entre regiões do Azure. A replicação geográfica é usada principalmente em um cenário de recuperação de desastre. Em um cenário de recuperação de desastre, clientes devem criar toda a pilha de aplicativos em uma região de backup de uma maneira coordenada, em vez de permitir que os componentes de aplicativos individuais decidam por conta própria quando mudar para seus backups. Isso é especialmente relevante para o Redis. Um dos principais benefícios do Redis é ser um repositório de muito baixa latência. Se o Redis usado por um aplicativo faz failover para uma região do Azure diferente mas a camada de computação não faz, o tempo da viagem de ida e volta adicionado teria um impacto perceptível no desempenho. Por esse motivo, gostaríamos de evitar que o Redis faça failover automaticamente devido a problemas de disponibilidade transitórios.

No momento, para iniciar o failover, você precisa remover o link de replicação geográfica no portal do Azure e, em seguida, alterar o ponto de extremidade de conexão no cliente Redis do cache vinculado primário para o cache secundário (anteriormente vinculado). Quando dois caches são desassociados, a réplica se torna um cache de leitura-gravação regular novamente e aceita as solicitações diretamente dos clientes Redis.


## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a [Camada Premium do Cache Redis do Azure](cache-premium-tier-intro.md).

