---
title: Verificar Resource Health eventos que afetam o cluster AKS (versão prévia)
description: Verifique o status de integridade do cluster AKS usando Azure Resource Health.
services: container-service
author: yunjchoi
ms.topic: troubleshooting
ms.date: 08/18/2020
ms.author: yunjchoi
ms.openlocfilehash: a409ac944c51d893fc344f82ae83d97559e055ef
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92070649"
---
# <a name="check-for-resource-health-events-impacting-your-aks-cluster-preview"></a>Verificar Resource Health eventos que afetam o cluster AKS (versão prévia)


Ao executar suas cargas de trabalho de contêiner no AKS, você deseja garantir que possa solucionar problemas assim que eles surgirem para minimizar o impacto sobre a disponibilidade de suas cargas de trabalho. [Azure Resource Health](../service-health/resource-health-overview.md) fornece visibilidade de vários eventos de integridade que podem fazer com que o cluster AKs esteja indisponível.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="open-resource-health"></a>Abrir Resource Health

### <a name="to-access-resource-health-for-your-aks-cluster"></a>Para acessar Resource Health para o cluster AKS:

- Navegue até o cluster AKS no [portal do Azure](https://portal.azure.com).
- Selecione **Resource Health** no painel de navegação esquerdo.

### <a name="to-access-resource-health-for-all-clusters-on-your-subscription"></a>Para acessar Resource Health para todos os clusters em sua assinatura:

- Pesquise a **integridade do serviço** no [portal do Azure](https://portal.azure.com) e navegue até ele.
- Selecione **Resource Health** no painel de navegação esquerdo.
- Selecione sua assinatura e defina o tipo de recurso como AKS (serviço kubernetes do Azure).

![Captura de tela mostra a integridade do recurso para seus clusters de K S.](./media/aks-resource-health/resource-health-check.png)

## <a name="check-the-health-status"></a>Verificar o status de integridade

Azure Resource Health ajuda a diagnosticar e obter suporte para problemas de serviço que afetam os recursos do Azure. Resource Health relatórios sobre a integridade atual e passada de seus recursos e ajuda a determinar se o problema é causado por uma ação iniciada pelo usuário ou um evento de plataforma.

Resource Health recebe sinais para o cluster gerenciado para determinar o estado de integridade do cluster. Ele examina o estado de integridade do cluster AKS e relata ações necessárias para cada sinal de integridade. Esses sinais variam de resolução automática de problemas, atualizações planejadas, eventos de integridade não planejados e indisponibilidade causadas por ações iniciadas pelo usuário. Esses sinais são classificados usando o status de integridade do Azure Resource Health: *disponível, não* *disponível*, *desconhecido*e *degradado*.

- **Disponível**: quando não há nenhum problema conhecido afetando a integridade do cluster, Resource Health relata o cluster como *disponível*.

- **Indisponível**: quando há um evento de plataforma ou não plataforma que afeta a integridade do cluster, Resource Health relata o cluster como *indisponível*.

- **Desconhecido**: quando há uma perda de conexão temporária para as métricas de integridade do cluster, Resource Health relata o cluster como *desconhecido*.

- **Degradado**: quando há um problema de integridade que exige sua ação, Resource Health relata o cluster como *degradado*.

Para obter detalhes adicionais sobre o que cada status de integridade indica, visite [Resource Health visão geral](../service-health/resource-health-overview.md#health-status).

### <a name="view-historical-data"></a>Exibir dados históricos

Você também pode exibir os últimos 30 dias de informações históricas de Resource Health na seção **histórico de integridade** .

## <a name="next-steps"></a>Próximas etapas

Execute verificações no cluster para solucionar problemas de cluster usando o [diagnóstico AKs](./concepts-diagnostics.md).