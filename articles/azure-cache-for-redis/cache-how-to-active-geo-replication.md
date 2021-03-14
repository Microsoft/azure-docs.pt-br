---
title: Configurar a replicação geográfica ativa para o cache do Azure Enterprise para instâncias Redis
description: Saiba como replicar seu cache do Azure para instâncias do Redis Enterprise em regiões do Azure
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: yegu
ms.openlocfilehash: fe777c3aa7b314dc56a42cc64712d18281a6ea7d
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102121160"
---
# <a name="configure-active-geo-replication-for-enterprise-azure-cache-for-redis-instances-preview"></a>Configurar a replicação geográfica ativa para o cache do Azure Enterprise para instâncias Redis (versão prévia)

Neste artigo, você aprenderá a configurar um cache do Azure replicado geograficamente ativo usando o portal do Azure.

A replicação geográfica ativa agrupa dois ou mais cache do Azure corporativo para instâncias Redis em um único cache que abrange em regiões do Azure. Todas as instâncias agem como primárias locais. Um aplicativo decide quais instâncias usar para solicitações de leitura e gravação.

## <a name="create-or-join-an-active-geo-replication-group"></a>Criar ou ingressar em um grupo de replicação geográfica ativa

> [!IMPORTANT]
> A replicação geográfica ativa deve ser habilitada no momento em que um cache do Azure para Redis é criado.
>
>

1. Na nova interface do usuário de criação do **cache Redis** , clique em **Configurar** para configurar a **replicação geográfica ativa** na guia **avançado** .

    ![Configurar a replicação geográfica ativa](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-not-configured.png)

1. Crie um novo grupo de replicação, para uma primeira instância de cache, ou selecione um existente na lista.

    ![Vincular caches](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-new-group.png)

1. Clique em **Configurar** para concluir.

    ![Replicação geográfica ativa configurada](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-configured.png)

1. Repita as etapas acima para cada instância de cache adicional no grupo de replicação geográfica.

## <a name="remove-from-an-active-geo-replication-group"></a>Remover de um grupo de replicação geográfica ativa

Para remover uma instância de cache de um grupo de replicação geográfica ativa, basta excluir a instância. As instâncias restantes serão reconfiguradas automaticamente.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o cache do Azure para recursos do Redis.

* [Cache do Azure para camadas de serviço Redis](cache-overview.md#service-tiers)
* [Alta disponibilidade para o cache do Azure para Redis](cache-high-availability.md)
