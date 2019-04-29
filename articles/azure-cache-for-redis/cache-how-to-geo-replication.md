---
title: Como configurar a replicação geográfica para o Cache do Azure para Redis | Microsoft Docs
description: Saiba como replicar as instâncias do Cache do Azure para Redis entre regiões geográficas.
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 375643dc-dbac-4bab-8004-d9ae9570440d
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 03/06/2019
ms.author: yegu
ms.openlocfilehash: 4254175955c3560c7bd0fdd08c6b60c318238b76
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60552247"
---
# <a name="how-to-configure-geo-replication-for-azure-cache-for-redis"></a>Como configurar a replicação geográfica para o Cache do Azure para Redis

A replicação geográfica fornece um mecanismo para vincular duas instâncias do Cache do Azure para Redis de camada Premium. Um cache é escolhido como o cache vinculado primário e o outro como o cache vinculado secundário. O cache vinculado secundário se torna somente leitura e os dados gravados no cache primário são replicados para o cache vinculado secundário. Essa funcionalidade pode ser usada para replicar um cache entre regiões do Azure. Este artigo fornece um guia para configurar a replicação geográfica para as instâncias do Cache do Azure para Redis de camada Premium.

## <a name="geo-replication-prerequisites"></a>Pré-requisitos de replicação geográfica

Para configurar a replicação geográfica entre dois caches, os seguintes pré-requisitos devem ser atendidos:

- Ambos os caches são [camada Premium](cache-premium-tier-intro.md) armazena em cache.
- Ambos os caches estão na mesma assinatura do Azure.
- O cache vinculado secundário é o mesmo tamanho de cache ou um tamanho de cache maior do que o cache vinculado primário.
- Ambos os caches são criados e em um estado de execução.

Alguns recursos não são compatíveis com replicação geográfica:

- Não há suporte para persistência com replicação geográfica.
- Clustering tem suporte se ambos os caches tem o clustering habilitado e tem o mesmo número de fragmentos.
- Há suporte para os caches na mesma rede virtual.
- Os caches em VNETs diferentes têm suporte com advertências. Ver [posso usar a replicação geográfica com Meus caches em uma rede virtual?](#can-i-use-geo-replication-with-my-caches-in-a-vnet) para obter mais informações.

Depois de configurar a replicação geográfica, as seguintes restrições se aplicam para o par de caches vinculados:

- O cache vinculado secundário é somente leitura; você pode ler dele, mas não pode gravar dados nele. 
- Todos os dados que estavam no cache vinculado secundário antes do link ser adicionado são removidos. Se a replicação geográfica for posterior removidos no entanto, os dados replicados permanecem no cache vinculado secundário.
- Não é possível [escala](cache-how-to-scale.md) qualquer cache enquanto os caches estão vinculados.
- Não é possível [alterar o número de fragmentos](cache-how-to-premium-clustering.md) se o cache tiver clustering habilitado.
- Você não pode habilitar a persistência em nenhum dos caches.
- Você pode [exportar](cache-how-to-import-export-data.md#export) de nenhum dos caches.
- Não é possível [importação](cache-how-to-import-export-data.md#import) para o cache vinculado secundário.
- Não é possível excluir o cache vinculado ou o grupo de recursos que os contém, até que você desvincule os caches. Para obter mais informações, consulte [Por que a operação falhou quando tentei excluir meu cache vinculado?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- Se os caches estão em regiões diferentes, os custos de egresso de rede se aplicam aos dados movidos entre regiões. Para obter mais informações, consulte [Quanto custa para replicar meus dados entre regiões do Azure?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- Failover automático não ocorre entre o cache vinculado primário e secundário. Para obter mais informações e obter informações sobre como fazer failover de um aplicativo cliente, consulte [como funciona o failover para o cache vinculado secundário?](#how-does-failing-over-to-the-secondary-linked-cache-work)

## <a name="add-a-geo-replication-link"></a>Adicionar um link de replicação geográfica

1. Para vincular dois caches juntos para replicação geográfica, primeira clique **replicação geográfica** no menu de recursos do cache que você pretende ser primário vinculado cache. Em seguida, clique em **Adicionar link de replicação do cache** da **georeplicação** folha.

    ![Adicionar Link](./media/cache-how-to-geo-replication/cache-geo-location-menu.png)

2. Clique no nome do seu cache secundário desejado do **caches compatíveis** lista. Se o cache secundário não for exibido na lista, verifique se que o [pré-requisitos de replicação geográfica](#geo-replication-prerequisites) para o cache secundário são atendidos. Para filtrar os caches por região, clique na região no mapa para exibir apenas esses caches na **caches compatíveis** lista.

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

    O cache vinculado primário permanece disponível para uso durante o processo de vinculação. O cache vinculado secundário não está disponível até que o processo de vinculação é concluída.

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

- Ao vincular, o cache vinculado primário permanece disponível enquanto o processo de vinculação é concluída.
- Ao vincular, o cache vinculado secundário não está disponível até que o processo de vinculação é concluída.
- Ao desvincular, ambos os caches permanecem disponíveis durante o processo de desvinculação for concluído.

### <a name="can-i-link-more-than-two-caches-together"></a>Posso vincular mais de dois caches juntos?

Não, só é possível vincular dois caches juntos.

### <a name="can-i-link-two-caches-from-different-azure-subscriptions"></a>Posso vincular dois caches de assinaturas do Azure diferentes?

Não, ambos os caches devem estar na mesma assinatura do Azure.

### <a name="can-i-link-two-caches-with-different-sizes"></a>Posso vincular dois caches com tamanhos diferentes?

Sim, contanto que o cache vinculado secundário seja maior do que o cache vinculado primário.

### <a name="can-i-use-geo-replication-with-clustering-enabled"></a>Posso usar a replicação geográfica com o clustering habilitado?

Sim, desde que ambos os caches tenham o mesmo número de fragmentos.

### <a name="can-i-use-geo-replication-with-my-caches-in-a-vnet"></a>Posso usar a replicação geográfica com meus caches em uma VNET?

Sim, a replicação geográfica de caches em redes virtuais é compatível com Advertências:

- Há suporte para a replicação geográfica entre caches na mesma VNET.
- Também há suporte para a replicação geográfica entre caches em VNETs diferentes.
  - Se as redes virtuais estiverem na mesma região, você poderá conectá-los usando [emparelhamento de VNET](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) ou um [conexão de Gateway de VPN de VNET para VNET](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#V2V).
  - Se as redes virtuais estiverem em regiões diferentes, a replicação geográfica usando a rede virtual emparelhamento não é suportado por causa de uma restrição com balanceadores de carga internos básico. Para obter mais informações sobre restrições de emparelhamento de rede virtual, consulte [restrições e requisitos de rede Virtual - emparelhamento -](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#requirements-and-constraints). A solução recomendada é usar uma conexão de Gateway de VPN de VNET para VNET.

Usando o [este modelo do Azure](https://azure.microsoft.com/resources/templates/201-redis-vnet-geo-replication/), você pode implantar rapidamente os dois caches replicados geograficamente em uma rede virtual conectado com uma conexão de Gateway de VPN de VNET para VNET.

### <a name="what-is-the-replication-schedule-for-redis-geo-replication"></a>O que é o cronograma de replicação para replicação geográfica do Redis?

A replicação é contínua e assíncrona e não ocorre em um agendamento específico. Todas as gravações feitas no principal são replicadas de forma assíncrona e instantaneamente no secundário.

### <a name="how-long-does-geo-replication-replication-take"></a>Quanto tempo leva a replicação de replicação geográfica?

A replicação é contínua, incremental e assíncrona e o tempo necessário não é muito diferente da latência entre regiões. Em determinadas circunstâncias, o cache secundário talvez precise fazer uma sincronização completa dos dados do primário. Nesse caso, o tempo de replicação é depende de diversos fatores, como: a carga sobre o cache primário, a largura de banda disponível e a latência entre regiões. Descobrimos que o tempo de replicação para um par de replicação geográfica de 53 GB completo pode estar em qualquer lugar entre 5 a 10 minutos.

### <a name="is-the-replication-recovery-point-guaranteed"></a>O ponto de recuperação de replicação é garantido?

Para os caches em um modo de replicação geográfica, persistência está desabilitada. Se um par de replicação geográfica é desvinculado, como um failover iniciado pelo cliente, o cache vinculado secundário mantém seus dados sincronizados até esse ponto de tempo. Nenhum ponto de recuperação é garantido em tais situações.

Para obter um ponto de recuperação [exportar](cache-how-to-import-export-data.md#export) de nenhum dos caches. Você pode posteriormente [importação](cache-how-to-import-export-data.md#import) para o cache vinculado primário.

### <a name="can-i-use-powershell-or-azure-cli-to-manage-geo-replication"></a>Posso usar o PowerShell ou a CLI do Azure para gerenciar a replicação geográfica?

Sim, a replicação geográfica pode ser gerenciada usando o portal do Azure, PowerShell ou CLI do Azure. Para obter mais informações, consulte o [documentos do PowerShell](https://docs.microsoft.com/powershell/module/az.rediscache/?view=azps-1.4.0#redis_cache) ou [documentos do CLI do Azure](https://docs.microsoft.com/cli/azure/redis/server-link?view=azure-cli-latest).

### <a name="how-much-does-it-cost-to-replicate-my-data-across-azure-regions"></a>Quanto custa para replicar meus dados entre regiões do Azure?

Ao usar a replicação geográfica, dados do cache vinculado primário são replicados para o cache vinculado secundário. Não há nenhum custo para a transferência de dados se os dois caches vinculados estão na mesma região. Se os dois caches vinculados estão em regiões diferentes, o encargo de transferência de dados é o custo de egresso de rede de movimentação em qualquer região de dados. Para obter mais informações, consulte [Detalhes de preço de largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache"></a>Por que a operação falhou quando tentei excluir meu cache vinculado?

Com replicação geográfica de caches e seus grupos de recursos não podem ser excluídos enquanto vinculado até que você remova o link de replicação geográfica. Se você tentar excluir o grupo de recursos que contém um ou ambos os caches vinculados, outros recursos no grupo de recursos serão excluídos, mas o grupo de recursos permanecerá no estado `deleting` e quaisquer caches vinculados no grupo de recursos permanecerão no estado `running`. Para excluir completamente o grupo de recursos e os caches vinculados dentro dele, desvincule os caches, conforme descrito em [remover um link de replicação geográfica](#remove-a-geo-replication-link).

### <a name="what-region-should-i-use-for-my-secondary-linked-cache"></a>Qual região devo usar para meu cache vinculado secundário?

Em geral, recomenda-se para o cache exista na mesma região do Azure como o aplicativo que o acessa. Para aplicativos com regiões primárias e fallback separadas, é recomendável que seus caches primários e secundários existirem nessas mesmas regiões. Para obter mais informações sobre regiões emparelhadas, consulte [Melhores práticas – regiões emparelhadas do Azure](../best-practices-availability-paired-regions.md).

### <a name="how-does-failing-over-to-the-secondary-linked-cache-work"></a>Como funciona o failover para o cache vinculado secundário?

Não há suporte para failover automático entre regiões do Azure para caches replicados geograficamente. Em um cenário de recuperação de desastres, os clientes devem abrir a pilha do aplicativo de maneira coordenada em sua região de backup. Permitindo que decidir componentes individuais do aplicativo quando mudar para seus backups por conta própria pode afetar negativamente o desempenho. Um dos principais benefícios do Redis é que ele é um armazenamento de baixa latência. Se o aplicativo do cliente principal estiver em uma região diferente do seu cache, o tempo de ida e volta adicionado teria um impacto significativo no desempenho. Por esse motivo, podemos evitar fazer failover automaticamente devido a problemas de disponibilidade transitórios.

Para iniciar um failover iniciado pelo cliente, primeiro desvincule os caches. Em seguida, altere seu cliente Redis para usar o ponto de extremidade de conexão do cache secundário (anteriormente vinculado). Quando dois caches estão desvinculados, o cache secundário se torna um cache de leitura-gravação regular novamente e aceite solicitações diretamente de clientes Redis.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a [Camada Premium do Cache do Azure para Redis](cache-premium-tier-intro.md).
