---
title: Analisar os padrões de uso da CDN do Azure
description: Este artigo descreve os diferentes tipos de relatórios de análise disponíveis para produtos da CDN do Azure.
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/30/2020
ms.author: allensu
ms.openlocfilehash: 2c593cda7761ce1defa6bdb31e0dbb528b9e5eca
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96483981"
---
# <a name="analyze-azure-cdn-usage-patterns"></a>Analisar os padrões de uso da CDN do Azure

Após habilitar a CDN para seu aplicativo, será possível monitorar o uso da CDN, verificar a integridade da sua entrega e resolver eventuais problemas. A CDN do Azure fornece esses recursos das seguintes maneiras: 

## <a name="raw-logs-for-azure-cdn-from-microsoft"></a>Logs brutos para a CDN do Azure da Microsoft
Com um perfil padrão da Microsoft, você pode habilitar logs brutos e selecionar para transmitir logs para:

* Armazenamento do Azure
* Hubs de Eventos
* Azure Log Analytics

Com o Azure Log Analytics você pode exibir as métricas de monitoramento e configurar alertas. 

Para obter mais informações, consulte [logs brutos de http da CDN do Azure](monitoring-and-access-log.md).


## <a name="core-analytics-via-azure-diagnostic-logs"></a>Análise de núcleo por meio de logs de diagnósticos do Azure

A análise de núcleo está disponível para pontos de extremidade CDN em todos os tipos de preço. Os logs de diagnóstico do Azure permitem que a análise principal seja exportada para o armazenamento do Azure, hubs de eventos ou logs de Azure Monitor. Os logs de Azure Monitor oferecem uma solução com grafos que podem ser configurados pelo usuário e personalizáveis. Para obter mais informações sobre logs de diagnóstico do Azure, consulte [Logs de diagnóstico do Azure](cdn-azure-diagnostic-logs.md).

## <a name="verizon-core-reports"></a>Relatórios de núcleo da Verizon

Os perfis **da CDN standard do Azure da Verizon** ou **do Azure CDN Premium da Verizon** fornecem relatórios principais. Você pode exibir relatórios principais no portal complementar da Verizon. Os relatórios de núcleo da Verizon podem ser acessados por meio da opção **gerenciar** do portal do Azure e oferecem diferentes tipos de grafos e exibições. Para obter mais informações, consulte [Relatórios de núcleo da Verizon](cdn-analyze-usage-patterns.md).

## <a name="verizon-custom-reports"></a>Relatórios personalizados da Verizon

Os perfis **da CDN standard do Azure da Verizon** ou **do Azure CDN Premium da Verizon** fornecem relatórios personalizados. Você pode exibir relatórios personalizados no portal complementar da Verizon. Os relatórios personalizados da Verizon podem ser acessados por meio da opção **gerenciar** do portal do Azure. 

Os relatórios personalizados exibem o número de acertos ou dados transferidos para cada CNAME de borda. Os dados são agrupados por código de resposta HTTP ou status de cache ao longo do tempo. Para obter mais informações, consulte [Relatórios personalizados da Verizon](cdn-verizon-custom-reports.md).

## <a name="azure-cdn-premium-from-verizon-reports"></a>Relatórios de CDN Premium do Azure da Verizon

Com o **Azure CDN Premium da Verizon**, você também pode acessar os seguintes relatórios:
   * [Relatórios avançados de HTTP](cdn-advanced-http-reports.md)
   * [Estatísticas em tempo real](cdn-real-time-stats.md)
   * [Desempenho do nó de borda da CDN do Azure](cdn-edge-performance.md)

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu sobre as diferentes opções para relatórios de análise para a CDN do Azure.

Para obter mais informações sobre a CDN do Azure e os outros serviços do Azure mencionados neste artigo, consulte:

* [O que é a CDN do Azure?](cdn-overview.md)
* [Logs brutos HTTP da CDN do Azure](monitoring-and-access-log.md)
