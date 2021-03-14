---
title: Configurar a replicação geográfica para o cache Premium do Azure para instâncias Redis
description: Saiba como replicar seu cache do Azure para instâncias do Redis Premium em regiões do Azure
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: yegu
ms.openlocfilehash: 8701f7bcb2e7ff705e4f1d1b401f4eb3e680f28b
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102501032"
---
# <a name="configure-geo-replication-for-premium-azure-cache-for-redis-instances"></a>Configurar a replicação geográfica para o cache Premium do Azure para instâncias Redis

Neste artigo, você aprenderá a configurar um cache do Azure com replicação geográfica usando o portal do Azure.

A replicação geográfica vincula dois caches do Azure Premium para instâncias Redis e cria um relacionamento de replicação de dados. Essas instâncias de cache geralmente estão localizadas em diferentes regiões do Azure, embora não sejam necessárias. Uma instância age como a primária e a outra como a secundária. O primário lida com solicitações de leitura e gravação e propaga as alterações para o secundário. Esse processo continua até que o link entre as duas instâncias seja removido.

> [!NOTE]
> A replicação geográfica é projetada como uma solução de recuperação de desastre.
>
>

## <a name="geo-replication-prerequisites"></a>Pré-requisitos de replicação geográfica

Para configurar a replicação geográfica entre dois caches, os seguintes pré-requisitos devem ser atendidos:

- Os dois caches são caches da [camada Premium](cache-overview.md#service-tiers) .
- Os dois caches estão na mesma assinatura do Azure.
- O cache vinculado secundário tem o mesmo tamanho de cache ou um tamanho de cache maior do que o cache vinculado primário.
- Ambos os caches são criados e em estado de execução.

Alguns recursos não têm suporte com a replicação geográfica:

- A persistência não tem suporte com a replicação geográfica.
- O clustering terá suporte se ambos os caches tiverem o clustering habilitado e tiverem o mesmo número de fragmentos.
- Há suporte para caches na mesma VNET.
- Os caches em diferentes VNETs têm suporte com advertências. Veja posso [usar a replicação geográfica com meus caches em uma VNET?](#can-i-use-geo-replication-with-my-caches-in-a-vnet) para obter mais informações.

Após a configuração da replicação geográfica, as seguintes restrições se aplicam ao seu par de cache vinculado:

- O cache vinculado secundário é somente leitura; você pode ler dele, mas não pode gravar dados nele. 
- Todos os dados que estavam no cache vinculado secundário antes do link ser adicionado são removidos. No entanto, se a replicação geográfica for removida posteriormente, os dados replicados permanecerão no cache vinculado secundário.
- Não é possível [dimensionar](cache-how-to-scale.md) o cache enquanto os caches estão vinculados.
- Você não poderá [alterar o número de fragmentos](cache-how-to-premium-clustering.md) se o cache tiver o clustering habilitado.
- Você não pode habilitar a persistência em nenhum dos caches.
- Você pode [Exportar](cache-how-to-import-export-data.md#export) de um desses caches.
- Não é possível [importar](cache-how-to-import-export-data.md#import) para o cache vinculado secundário.
- Você não pode excluir o cache vinculado ou o grupo de recursos que os contém, até desvincular os caches. Para obter mais informações, consulte [Por que a operação falhou quando tentei excluir meu cache vinculado?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- Se os caches estiverem em regiões diferentes, os custos de egresso de rede se aplicarão aos dados movidos entre regiões. Para obter mais informações, consulte [Quanto custa para replicar meus dados entre regiões do Azure?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- O failover automático não ocorre entre o cache vinculado primário e o secundário. Para obter mais informações e informações sobre como fazer failover de um aplicativo cliente, confira [como funciona a falha no cache vinculado secundário?](#how-does-failing-over-to-the-secondary-linked-cache-work)

## <a name="add-a-geo-replication-link"></a>Adicionar um link de replicação geográfica

1. Para vincular dois caches para replicação geográfica, clique em **replicação geográfica** no menu de recursos do cache no qual você pretende ser o cache vinculado primário. Em seguida, clique em **Adicionar link de replicação de cache** na folha **replicação geográfica** .

    ![Adicionar Link](./media/cache-how-to-geo-replication/cache-geo-location-menu.png)

2. Clique no nome do seu cache secundário pretendido na lista **caches compatíveis** . Se o seu cache secundário não for exibido na lista, verifique se os [pré-requisitos de replicação geográfica](#geo-replication-prerequisites) para o cache secundário foram atendidos. Para filtrar os caches por região, clique na região no mapa para exibir apenas os caches na lista de **caches compatíveis** .

    ![Caches compatíveis de replicação geográfica](./media/cache-how-to-geo-replication/cache-geo-location-select-link.png)
    
    Você também pode iniciar o processo de vinculação ou exibir detalhes sobre o cache secundário usando o menu de contexto.

    ![Menu de contexto de replicação geográfica](./media/cache-how-to-geo-replication/cache-geo-location-select-link-context-menu.png)

3. Clique em **Vincular** para vincular dois caches e iniciar o processo de replicação.

    ![Vincular caches](./media/cache-how-to-geo-replication/cache-geo-location-confirm-link.png)

4. Você pode exibir o andamento do processo de replicação na folha **Replicação geográfica**.

    ![Status da vinculação](./media/cache-how-to-geo-replication/cache-geo-location-linking.png)

    Você também pode exibir o status de vinculação na folha **Visão geral** para os caches primário e secundário.

    ![Captura de tela que realça como exibir o status de vinculação para os caches primários e secundários.](./media/cache-how-to-geo-replication/cache-geo-location-link-status.png)

    Quando o processo de replicação é concluído, o **Status da vinculação** é alterado para **Êxito**.

    ![Status do cache](./media/cache-how-to-geo-replication/cache-geo-location-link-successful.png)

    O cache vinculado primário permanece disponível para uso durante o processo de vinculação. O cache vinculado secundário não estará disponível até que o processo de vinculação seja concluído.

## <a name="remove-a-geo-replication-link"></a>Remover um link de replicação geográfica

1. Para remover o vínculo entre dois caches e parar a replicação geográfica, clique em **desvincular caches** da folha de **replicação geográfica** .
    
    ![Desvincular caches](./media/cache-how-to-geo-replication/cache-geo-location-unlink.png)

    Quando o processo de desvinculação for concluído, o cache secundário estará disponível para leituras e gravações.

>[!NOTE]
>Quando o link de replicação geográfica é removido, os dados replicados do cache vinculado primário permanecem no cache secundário.
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
- [Posso usar o PowerShell ou o CLI do Azure para gerenciar a replicação geográfica?](#can-i-use-powershell-or-azure-cli-to-manage-geo-replication)
- [Quanto custa para replicar meus dados entre regiões do Azure?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- [Por que a operação falhou quando tentei excluir meu cache vinculado?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- [Qual região devo usar para meu cache vinculado secundário?](#what-region-should-i-use-for-my-secondary-linked-cache)
- [Como funciona o failover para o cache vinculado secundário?](#how-does-failing-over-to-the-secondary-linked-cache-work)
- [Posso configurar o firewall com a replicação geográfica?](#can-i-configure-a-firewall-with-geo-replication)

### <a name="can-i-use-geo-replication-with-a-standard-or-basic-tier-cache"></a>Posso usar a replicação geográfica com um cache de camada Standard ou Basic?

Não, a replicação geográfica só está disponível para caches da camada Premium.

### <a name="is-my-cache-available-for-use-during-the-linking-or-unlinking-process"></a>O cache está disponível para uso durante o processo de vinculação ou desvinculação?

- Ao vincular, o cache vinculado primário permanece disponível enquanto o processo de vinculação é concluído.
- Ao vincular, o cache vinculado secundário não estará disponível até que o processo de vinculação seja concluído.
- Ao desvincular, os dois caches permanecem disponíveis enquanto o processo de desvinculação é concluído.

### <a name="can-i-link-more-than-two-caches-together"></a>Posso vincular mais de dois caches juntos?

Não, você só pode vincular dois caches juntos.

### <a name="can-i-link-two-caches-from-different-azure-subscriptions"></a>Posso vincular dois caches de assinaturas do Azure diferentes?

Não, ambos os caches devem estar na mesma assinatura do Azure.

### <a name="can-i-link-two-caches-with-different-sizes"></a>Posso vincular dois caches com tamanhos diferentes?

Sim, contanto que o cache vinculado secundário seja maior do que o cache vinculado primário.

### <a name="can-i-use-geo-replication-with-clustering-enabled"></a>Posso usar a replicação geográfica com o clustering habilitado?

Sim, desde que ambos os caches tenham o mesmo número de fragmentos.

### <a name="can-i-use-geo-replication-with-my-caches-in-a-vnet"></a>Posso usar a replicação geográfica com meus caches em uma VNET?

Sim, há suporte para a replicação geográfica de caches em VNETs com as advertências:

- Há suporte para a replicação geográfica entre caches na mesma VNET.
- Também há suporte para a replicação geográfica entre caches em diferentes VNETs.
  - Se os VNETs estiverem na mesma região, você poderá conectá-los usando o [emparelhamento vnet](../virtual-network/virtual-network-peering-overview.md) ou uma [conexão vnet a vnet do gateway de VPN](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).
  - Se os VNETs estiverem em regiões diferentes, a replicação geográfica usando o emparelhamento VNET terá suporte, mas uma VM de cliente na VNET 1 (região 1) não poderá acessar o cache na VNET 2 (região 2) por meio de seu nome DNS devido a uma restrição com balanceadores de carga internos básicos. Para obter mais informações sobre restrições de emparelhamento VNET, consulte [rede virtual – emparelhamento-requisitos e restrições](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints). A solução recomendada é usar uma conexão VNET a VNET do gateway de VPN.
  
Usando [este modelo do Azure](https://azure.microsoft.com/resources/templates/201-redis-vnet-geo-replication/), você pode implantar rapidamente dois caches replicados geograficamente em uma VNET conectada a uma conexão VNET a vnet do gateway de VPN.

### <a name="what-is-the-replication-schedule-for-redis-geo-replication"></a>O que é o cronograma de replicação para replicação geográfica do Redis?

A replicação é contínua e assíncrona e não ocorre em um agendamento específico. Todas as gravações feitas no primário são instantâneas e replicadas de forma assíncrona no secundário.

### <a name="how-long-does-geo-replication-replication-take"></a>Quanto tempo leva a replicação de replicação geográfica?

A replicação é incremental, assíncrona e contínua e o tempo gasto não é muito diferente da latência entre regiões. Em determinadas circunstâncias, o cache secundário pode ser necessário para fazer uma sincronização completa dos dados a partir do primário. O tempo de replicação, nesse caso, depende do número de fatores como: carga no cache primário, largura de banda de rede disponível e latência entre regiões. Encontramos o tempo de replicação para um par completo de 53 GB com replicação geográfica pode estar entre 5 e 10 minutos.

### <a name="is-the-replication-recovery-point-guaranteed"></a>O ponto de recuperação de replicação é garantido?

Para caches em um modo replicado geograficamente, a persistência está desabilitada. Se um par com replicação geográfica estiver desvinculado, como um failover iniciado pelo cliente, o cache vinculado secundário manterá seus dados sincronizados até esse ponto de tempo. Nenhum ponto de recuperação é garantido nessas situações.

Para obter um ponto de recuperação, [exporte](cache-how-to-import-export-data.md#export) de um dos caches. Posteriormente, você poderá [importar](cache-how-to-import-export-data.md#import) para o cache vinculado primário.

### <a name="can-i-use-powershell-or-azure-cli-to-manage-geo-replication"></a>Posso usar o PowerShell ou o CLI do Azure para gerenciar a replicação geográfica?

Sim, a replicação geográfica pode ser gerenciada usando o portal do Azure, o PowerShell ou o CLI do Azure. Para obter mais informações, consulte os documentos do [PowerShell](/powershell/module/az.rediscache/#redis_cache) ou [CLI do Azure docs](/cli/azure/redis/server-link).

### <a name="how-much-does-it-cost-to-replicate-my-data-across-azure-regions"></a>Quanto custa para replicar meus dados entre regiões do Azure?

Ao usar a replicação geográfica, os dados do cache vinculado primário são replicados para o cache vinculado secundário. Não haverá cobrança para a transferência de dados se os dois caches vinculados estiverem na mesma região. Se os dois caches vinculados estiverem em regiões diferentes, o encargo de transferência de dados será o custo de saída de rede da movimentação de dados em qualquer região. Para obter mais informações, consulte [detalhes de preços de largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache"></a>Por que a operação falhou quando tentei excluir meu cache vinculado?

Os caches replicados geograficamente e seus grupos de recursos não podem ser excluídos enquanto estiverem vinculados até que você remova o link de replicação geográfica. Se você tentar excluir o grupo de recursos que contém um ou ambos os caches vinculados, outros recursos no grupo de recursos serão excluídos, mas o grupo de recursos permanecerá no estado `deleting` e quaisquer caches vinculados no grupo de recursos permanecerão no estado `running`. Para excluir completamente o grupo de recursos e os caches vinculados dentro dele, desvincule os caches conforme descrito em [remover um link de replicação geográfica](#remove-a-geo-replication-link).

### <a name="what-region-should-i-use-for-my-secondary-linked-cache"></a>Qual região devo usar para meu cache vinculado secundário?

Em geral, é recomendável que o cache exista na mesma região do Azure que o aplicativo que o acessa. Para aplicativos com regiões primárias e de fallback separadas, é recomendável que os seus caches primário e secundário existam nas mesmas regiões. Para obter mais informações sobre regiões emparelhadas, consulte [Melhores práticas – regiões emparelhadas do Azure](../best-practices-availability-paired-regions.md).

### <a name="how-does-failing-over-to-the-secondary-linked-cache-work"></a>Como funciona o failover para o cache vinculado secundário?

O failover automático nas regiões do Azure não tem suporte para caches com replicação geográfica. Em um cenário de recuperação de desastres, os clientes devem abrir toda a pilha de aplicativos de maneira coordenada em sua região de backup. Deixar componentes de aplicativos individuais decidir quando alternar para seus backups por conta própria pode afetar negativamente o desempenho. Um dos principais benefícios do Redis é que ele é um armazenamento de baixa latência. Se o aplicativo principal do cliente estiver em uma região diferente de seu cache, o tempo de viagem de ida e volta terá um impacto perceptível no desempenho. Por esse motivo, evitamos o failover automaticamente devido a problemas de disponibilidade transitórios.

Para iniciar um failover iniciado pelo cliente, primeiro desvincule os caches. Em seguida, altere o cliente Redis para usar o ponto de extremidade de conexão do cache secundário (anteriormente vinculado). Quando os dois caches são desvinculados, o cache secundário se torna um cache de leitura-gravação regular e aceita solicitações diretamente de clientes Redis.

### <a name="can-i-configure-a-firewall-with-geo-replication"></a>Posso configurar um firewall com replicação geográfica?

Sim, você pode configurar um [Firewall](./cache-configure.md#firewall) com replicação geográfica. Para que a replicação geográfica funcione junto com um firewall, verifique se o endereço IP do cache secundário foi adicionado às regras de firewall do cache primário.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o cache do Azure para recursos do Redis.

* [Cache do Azure para camadas de serviço Redis](cache-overview.md#service-tiers)
* [Alta disponibilidade para o cache do Azure para Redis](cache-high-availability.md)
