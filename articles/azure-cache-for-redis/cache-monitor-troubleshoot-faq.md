---
title: Perguntas frequentes sobre monitoramento e solução de problemas de cache do Azure para Redis
description: Conheça as respostas a perguntas comuns que ajudam a monitorar e solucionar problemas do cache do Azure para Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 8e96c73578a9341f67d90cd4482ed75179c6886d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92537517"
---
# <a name="azure-cache-for-redis-monitoring-and-troubleshooting-faqs"></a>Perguntas frequentes sobre monitoramento e solução de problemas de cache do Azure para Redis
Este artigo fornece respostas a perguntas comuns sobre como monitorar e solucionar problemas do cache do Azure para Redis.

## <a name="common-questions-and-answers"></a>Perguntas e respostas comuns
Esta seção aborda as seguintes perguntas frequentes:

* [Como monitorar a integridade e o desempenho do meu cache?](#how-do-i-monitor-the-health-and-performance-of-my-cache)
* [Por que vejo tempos limite?](#why-am-i-seeing-timeouts)
* [Por que meu cliente foi desconectado do cache?](#why-was-my-client-disconnected-from-the-cache)

### <a name="how-do-i-monitor-the-health-and-performance-of-my-cache"></a>Como monitorar a integridade e o desempenho do meu cache?
As instâncias do Cache do Microsoft Azure para Redis podem ser monitoradas no [portal do Azure](https://portal.azure.com). Você pode exibir métricas, fixar gráficos de métricas ao quadro inicial, personalizar o intervalo de data e hora de gráficos de monitoramento, adicionar/remover as métricas de gráficos e definir alertas quando determinadas condições forem atendidas. Para obter mais informações, confira [Como monitorar o Cache do Azure para Redis](cache-how-to-monitor.md).

O **menu Recursos** do Cache do Azure para Redis também contém várias ferramentas para monitorar e solucionar problemas em seus caches.

* **Diagnosticar e solucionar problemas** fornece informações sobre problemas comuns e estratégias para resolvê-los.
* **Resource Health** observa seu recurso e informa se ele está sendo executado conforme o esperado. Para saber mais sobre o serviço Azure Resource Health, confira [Visão geral do Azure Resource Health](../service-health/resource-health-overview.md).
* **Nova solicitação de suporte** fornece opções para abrir uma solicitação de suporte para seu cache.

Essas ferramentas permitem monitorar a integridade de suas instâncias do Cache do Azure para Redis e ajudá-lo a gerenciar seus aplicativos de cache. Para obter mais informações, consulte a seção “Configurações de solução de problemas e suporte” em [Como configurar o Cache do Azure para Redis](cache-configure.md).

### <a name="why-am-i-seeing-timeouts"></a>Por que vejo tempos limite?
Tempos limite ocorrem no cliente que você usa para se comunicar com o Redis. Quando um comando é enviado ao servidor Redis, o comando é colocado na fila e o servidor Redis, eventualmente, seleciona o comando e o executa. No entanto, o cliente pode atingir o tempo limite durante este processo e, se ele faz isso, uma exceção é gerada no lado que realiza a chamada. Para saber mais sobre como solucionar problemas de tempo limite, consulte [Solução de problemas do lado do cliente](cache-troubleshoot-client.md) e [Exceções de tempo limite do StackExchange.Redis](cache-troubleshoot-timeouts.md#stackexchangeredis-timeout-exceptions).

### <a name="why-was-my-client-disconnected-from-the-cache"></a>Por que meu cliente foi desconectado do cache?
A seguir estão alguns motivos comuns para uma desconexão de cache.

* Motivos no lado do cliente
  * O aplicativo cliente foi reimplantado.
  * O aplicativo cliente executou uma operação de dimensionamento.
    * No caso de Serviços de Nuvem ou Aplicativos Web, isso pode ser devido ao dimensionamento automático.
  * A camada de rede no lado do cliente foi alterada.
  * Erros transitórios ocorreram no cliente ou em nós de rede entre o cliente e o servidor.
  * Os limites de largura de banda foram atingidos.
  * Operações vinculadas à CPU demoraram muito para ser concluídas.
* Motivos no lado do servidor
  * Na oferta de cache padrão, o cache do Azure para o serviço Redis iniciou um failover do nó primário para o nó de réplica.
  * O Azure estava aplicando um patch na instância onde o cache foi implantado
    * Isso pode ser para atualizações de servidor do Redis ou manutenção geral de VM.


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como monitorar e solucionar problemas de suas instâncias do Cache do Azure para Redis, confira [Como monitorar o Cache do Azure para Redis](cache-how-to-monitor.md) e os vários guias de solução de problemas.

Saiba mais sobre outros [cache do Azure para perguntas frequentes](cache-faq.md)sobre o Redis.