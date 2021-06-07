---
title: O que há de novo no Azure Load Balancer
description: Conheça as novidades do Azure Load Balancer, como as últimas notas sobre a versão, problemas conhecidos, correções de bug, funcionalidades preteridas e alterações futuras.
services: load-balancer
author: anavinahar
ms.service: load-balancer
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: anavin
ms.openlocfilehash: a30a42e8a8c4049b53274da512089dd29965e775
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96573146"
---
# <a name="whats-new-in-azure-load-balancer"></a>O que há de novo no Azure Load Balancer?

O Azure Load Balancer é atualizado regularmente. Mantenha-se atualizado com os comunicados mais recentes. Este artigo fornece informações sobre:

- As versões mais recentes
- Problemas conhecidos
- Correções de bug
- Funcionalidade preterida (se aplicável)

Você também pode encontrar as atualizações mais recentes do Azure Load Balancer e assinar o RSS feed [aqui](https://azure.microsoft.com/updates/?category=networking&query=load%20balancer).

## <a name="recent-releases"></a>Versões recentes

| Type |Nome |Descrição  |Data de adição  |
| ------ |---------|---------|---------|
| Recurso | Suporte para movimentações entre grupos de recursos | Suporte para o Standard Load Balancer e IP Público Padrão para [movimentações do grupo de recursos](https://azure.microsoft.com/updates/standard-resource-group-move/). | Outubro de 2020 |
| Recurso | Suporte para gerenciamento de pool de back-end baseado em IP (versão prévia) | O Azure Load Balancer é compatível com a adição e a remoção de recursos de um pool de back-end por meio de endereços IPv4 ou IPv6. Isso permite o gerenciamento fácil de contêineres, máquinas virtuais e conjuntos de dimensionamento de máquinas virtuais associados ao Load Balancer. Também permitirá que os endereços IP sejam reservados como parte de um pool de back-end antes da criação dos recursos associados. Saiba mais [aqui](backend-pool-management.md)|Julho de 2020 |
| Recurso| Insights do Azure Load Balancer usando o Azure Monitor | Criado como parte do Azure Monitor para Redes, os clientes agora têm mapas topológicos para todas as suas configurações do Load Balancer e painéis de integridade para seus Standard Load Balancers pré-configurados com métricas no portal do Azure. [Comece a usar e saiba mais](https://azure.microsoft.com/blog/introducing-azure-load-balancer-insights-using-azure-monitor-for-networks/) | Junho de 2020 |
| Validação | Adição de validação para portas de HA | Uma validação foi adicionada para que as regras de porta de HA e não HA só sejam configuráveis quando o IP flutuante estiver habilitado. Anteriormente, essa configuração passava, mas não funcionava conforme o esperado. Nenhuma alteração na funcionalidade foi feita. Você pode saber mais [aqui](load-balancer-ha-ports-overview.md#limitations)| Junho de 2020 |
| Recurso| Suporte a IPv6 para o Azure Load Balancer (em disponibilidade geral) | Você pode ter endereços IPv6 como front-end para seus Azure Load Balancers. Saiba como [criar um aplicativo de pilha dupla aqui](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md) |Abril de 2020|
| Recurso| Redefinições de TCP no Tempo Limite de Ociosidade (em disponibilidade geral)| Use as redefinições de TCP para criar um comportamento de aplicativo mais previsível. [Saiba mais](load-balancer-tcp-reset.md)| Fevereiro de 2020 |

## <a name="known-issues"></a>Problemas conhecidos

O grupo do produto está trabalhando ativamente em resoluções para os seguintes problemas conhecidos:

|Problema |Descrição  |Atenuação  |
| ---------- |---------|---------|
| Logs de eventos de alerta do Load Balancer e de status da investigação de integridade | O registro em log não funciona para eventos de alerta do Load Balancer para o Standard Load Balancer e o Básico nem para os logs de status da investigação de integridade do Load Balancer Básico  | [Utilize o Azure Monitor para obter métricas multidimensionais de seu Standard Load Balancer](load-balancer-standard-diagnostics.md). O Azure Monitor fornece a visualização de um conjunto avançado de métricas multidimensionais que também podem ser exportadas como logs. Você pode aproveitar o painel de métricas pré-configurado por meio da folha secundária Insights de seu Load Balancer. Se estiver usando o Load Balancer Básico, [atualize para o Standard](upgrade-basic-standard.md) para ter monitoramento das métricas de nível de produção.

  

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure Load Balancer, confira [O que é o Azure Load Balancer?](load-balancer-overview.md) e [perguntas frequentes](load-balancer-faqs.md).
