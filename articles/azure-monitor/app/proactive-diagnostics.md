---
title: Detecção Inteligente no Azure Application Insights | Microsoft Docs
description: O Application Insights executa uma análise automática profunda de telemetria do seu aplicativo e o avisará sobre possíveis problemas de desempenho.
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: 2eeb4a35-c7a1-49f7-9b68-4f4b860938b2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mbullwin
ms.openlocfilehash: edbd7000001ae6927078e2f1bb9e348cc78f9efa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61299608"
---
# <a name="smart-detection-in-application-insights"></a>Detecção Inteligente no Application Insights
 A Detecção Inteligente avisa automaticamente sobre possíveis problemas de desempenho no seu aplicativo Web. Ele executa uma análise proativa da telemetria que seu aplicativo envia ao [Application Insights](../../azure-monitor/app/app-insights-overview.md). Se houver um aumento repentino nas taxas de falha ou nos padrões anormais de desempenho de cliente ou de servidor, você receberá um alerta. Esse recurso não precisa de nenhuma configuração. Ela funciona se o seu aplicativo envia telemetria suficiente.

Você pode acessar os alertas de Detecção Inteligente dos emails recebidos e da folha Detecção Inteligente.

## <a name="review-your-smart-detections"></a>Revise suas detecções inteligentes
É possível descobrir as detecções de duas maneiras:

* **Você recebe um email** do Application Insights. Aqui está um exemplo típico:
  
    ![Alerta de email](./media/proactive-diagnostics/03.png)
  
    Clique no botão grande para abrir mais detalhadamente no portal.
* **O bloco Detecção Proativa** na folha de visão geral do seu aplicativo mostra uma contagem de alertas recentes. Clique no bloco para ver uma lista dos alertas recentes.

![Exibir detecções recentes](./media/proactive-diagnostics/04.png)

Selecione um alerta para ver seus detalhes.

## <a name="what-problems-are-detected"></a>Quais problemas foram detectados?
Há três tipos de detecção:

* [Detecção inteligente - anomalias de falha](../../azure-monitor/app/proactive-failure-diagnostics.md). Usamos o aprendizado de máquina para definirmos a taxa esperada de solicitações com falha para seu aplicativo, fazendo a correlação com a carga e com outros fatores. Se a taxa de falha for além do envelope esperado, podemos enviar um alerta.
* [Detecção inteligente - anomalias de desempenho](../../azure-monitor/app/proactive-performance-diagnostics.md). Você recebe notificações se o tempo de resposta de uma duração da operação ou dependência está diminuindo em comparação com a linha de base histórica ou identificamos um padrão anormal no tempo de resposta ou tempo de carregamento de página.   
* [Detecção inteligente - problemas do Serviço em Nuvem do Azure](https://azure.microsoft.com/blog/proactive-notifications-on-cloud-service-issues-with-azure-diagnostics-and-application-insights/). Você obterá alertas se seu aplicativo estiver hospedado nos Serviços de Nuvem do Azure e se uma instância de função tiver falhas de inicialização, reciclagem frequente ou falhas no tempo de execução.

(Os links de Ajuda em cada notificação levam você para os artigos relevantes).

## <a name="smart-detection-email-notifications"></a>Notificações de email de detecção inteligente

Todas as regras de detecção inteligente, exceto para as regras marcadas como versão prévia, são configuradas por padrão para enviar notificações por email quando as detecções são encontradas.

Configurar notificações por email para uma regra de detecção inteligente específica pode ser feito abrindo a folha **Configurações** de detecção inteligente e selecionando a regra, que abrirá a folha **Editar regra**.

Como alternativa, você pode alterar a configuração usando modelos do Azure Resource Manager. [Confira Gerenciar regras de detecção inteligente do Application Insights usando modelos do Azure Resource Manager](https://docs.microsoft.com/azure/azure-monitor/app/proactive-arm-config) para obter detalhes.

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Próximas etapas
Essas ferramentas de diagnóstico ajudam você a inspecionar a telemetria do seu aplicativo:

* [Metrics explorer](../../azure-monitor/app/metrics-explorer.md)
* [Gerenciador de pesquisa](../../azure-monitor/app/diagnostic-search.md)
* [Analytics - linguagem de consulta poderosa](../../azure-monitor/log-query/get-started-portal.md)

A Detecção Inteligente é totalmente automática. Mas talvez você queira configurar alguns outros alertas?

* [Alertas de métrica configurados manualmente](../../azure-monitor/app/alerts.md)
* [Testes de disponibilidade na Web](../../azure-monitor/app/monitor-web-app-availability.md) 

