---
title: Solucionar problemas no lado do servidor do Cache do Azure para Redis
description: Saiba como resolver problemas comuns do lado do servidor com o cache do Azure para Redis, como pressão de memória, alta utilização de CPU, comandos de longa execução ou limitações de largura de banda.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.openlocfilehash: 12d78846f5892e71388de6e6e76b868f9b14d4de
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "88008909"
---
# <a name="troubleshoot-azure-cache-for-redis-server-side-issues"></a>Solucionar problemas no lado do servidor do Cache do Azure para Redis

Esta seção aborda a solução de problemas que ocorrem devido a uma condição em um cache do Azure para Redis ou às máquinas virtuais que o hospedam.

- [Demanda de memória do servidor Redis](#memory-pressure-on-redis-server)
- [Alto uso da CPU ou carga do servidor](#high-cpu-usage-or-server-load)
- [Comandos de execução longa](#long-running-commands)
- [Limitação de largura de banda do lado do servidor](#server-side-bandwidth-limitation)

> [!NOTE]
> Várias das etapas de solução de problemas neste guia incluem instruções para executar comandos do Redis e monitorar diversas métricas de desempenho. Para obter mais informações, consulte os artigos na seção [Informações adicionais](#additional-information) .
>

## <a name="memory-pressure-on-redis-server"></a>Demanda de memória do servidor Redis

Uma grande demanda da memória do servidor acarreta todo tipo de problemas de desempenho, que podem atrasar o processamento de solicitações. Quando a pressão de memória é alcançada, o sistema pode paginar dados em disco. Esse tipo de _falha de página_ faz com que o sistema fique significativamente mais lento. Há diversas causas possíveis para essa demanda de memória:

- O cache é preenchido com dados próximos à sua capacidade máxima.
- Redis está vendo alta fragmentação de memória. Essa fragmentação geralmente é causada pelo armazenamento de objetos grandes, uma vez que o Redis é otimizado para objetos pequenos.

O Redis expõe duas estatísticas por meio do comando [info](https://redis.io/commands/info) que pode ajudá-lo a identificar esse problema: "used_memory" e "used_memory_rss". Você pode [exibir essas métricas](cache-how-to-monitor.md#view-metrics-with-azure-monitor) usando o Portal.

Há várias alterações que você pode fazer para ajudar a manter a integridade do uso de memória:

- [Configure uma política de memória](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) e defina os tempos de expiração em suas chaves. Essa política poderá não ser suficiente se você tiver fragmentação.
- [Configure um valor de MaxMemory-reservado](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) que seja grande o suficiente para compensar a fragmentação de memória.
- Dividir os objetos grandes armazenados em cache em objetos menores relacionados.
- [Crie alertas](cache-how-to-monitor.md#alerts) em métricas, como memória usada, para ser notificado com antecedência sobre possíveis impactos.
- [Dimensione](cache-how-to-scale.md) para um tamanho de cache maior com mais capacidade de memória.

## <a name="high-cpu-usage-or-server-load"></a>Alto uso da CPU ou carga do servidor

Uma alta carga de servidor ou uso de CPU significa que o servidor não pode processar solicitações em tempo hábil. O servidor pode estar lento para responder e não pode acompanhar as taxas de solicitação.

[Monitore métricas](cache-how-to-monitor.md#view-metrics-with-azure-monitor) como CPU ou carga do servidor. Fique atento a picos de uso de CPU que correspondem aos tempos limite.

Há várias alterações que você pode fazer para reduzir a carga alta do servidor:

- Investigue o que está causando picos de CPU, como [comandos de longa execução](#long-running-commands) indicados abaixo ou falha de página devido à alta pressão de memória.
- [Crie alertas](cache-how-to-monitor.md#alerts) sobre métricas como CPU ou carga do servidor a ser notificado antecipadamente sobre possíveis impactos.
- [Dimensione](cache-how-to-scale.md) para um tamanho de cache maior com mais capacidade de CPU.

## <a name="long-running-commands"></a>Comandos de execução longa

Alguns comandos Redis são mais caros de serem executados do que outros. A [documentação de comandos do Redis](https://redis.io/commands) mostra a complexidade do tempo de cada comando. Como o processamento de comando Redis é de thread único, um comando que leva tempo para ser executado bloqueará todos os outros que vierem depois dele. Você deve examinar os comandos que está emitindo para o servidor Redis para entender seus impactos de desempenho. Por exemplo, o comando [Keys](https://redis.io/commands/keys) geralmente é usado sem saber se é uma operação O (N). Você pode evitar chaves usando a [verificação](https://redis.io/commands/scan) para reduzir os picos de CPU.

Usando o comando [SLOWLOG](https://redis.io/commands/slowlog) , você pode medir os comandos caros que estão sendo executados no servidor.

## <a name="server-side-bandwidth-limitation"></a>Limitação de largura de banda do lado do servidor

Diferentes tamanhos de cache têm capacidades de largura de banda de rede diferentes. Se o servidor exceder a largura de banda disponível, os dados não serão enviados ao cliente o mais rapidamente. As solicitações de clientes podem atingir o tempo limite porque o servidor não pode enviar dados por push para o cliente rápido o suficiente.

As métricas de "leitura de cache" e "gravação de cache" podem ser usadas para ver quanto largura de banda do servidor está sendo usada. Você pode [exibir essas métricas](cache-how-to-monitor.md#view-metrics-with-azure-monitor) no Portal.

Para atenuar situações em que o uso de largura de banda de rede está próximo da capacidade máxima:

- Altere o comportamento da chamada do cliente para reduzir a demanda da rede.
- [Crie alertas](cache-how-to-monitor.md#alerts) sobre métricas como leitura de cache ou gravação de cache para ser notificado antecipadamente sobre possíveis impactos.
- [Dimensione](cache-how-to-scale.md) para um tamanho de cache maior com mais capacidade de largura de banda de rede.

## <a name="additional-information"></a>Informações adicionais

- [Solucionar problemas no lado do cliente do Cache do Azure para Redis](cache-troubleshoot-client.md)
- [Escolher a camada correta](cache-overview.md#choosing-the-right-tier)
- [Como medir e testar o desempenho do meu cache?](cache-management-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
- [Como monitorar o Cache do Azure para Redis](cache-how-to-monitor.md)
- [Como posso executar comandos do Redis?](cache-development-faq.md#how-can-i-run-redis-commands)
