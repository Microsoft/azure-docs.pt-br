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
ms.openlocfilehash: 8ee2dea364253d871d5624242d15d8a81ab6f08f
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67465908"
---
# <a name="smart-detection-in-application-insights"></a>Detecção Inteligente no Application Insights
 Detecção inteligente avisa automaticamente sobre possíveis problemas de desempenho e anomalias de falha em seu aplicativo web. Ele executa uma análise proativa da telemetria que seu aplicativo envia ao [Application Insights](../../azure-monitor/app/app-insights-overview.md). Se houver um aumento repentino nas taxas de falha ou nos padrões anormais de desempenho de cliente ou de servidor, você receberá um alerta. Esse recurso não precisa de nenhuma configuração. Ela funciona se o seu aplicativo envia telemetria suficiente.

Você pode acessar as detecções emitidas por detecção inteligente de emails recebidos e da folha detecção inteligente.

## <a name="review-your-smart-detections"></a>Revise suas detecções inteligentes
É possível descobrir as detecções de duas maneiras:

* **Você recebe um email** do Application Insights. Aqui está um exemplo típico:
  
    ![Alerta de email](./media/proactive-diagnostics/03.png)
  
    Clique no botão grande para abrir mais detalhadamente no portal.
* **Folha detecção inteligente** no Application Insights. Selecione **detecção inteligente** sob o **investigar** menu para ver uma lista das detecções recentes.

![Exibir detecções recentes](./media/proactive-diagnostics/04.png)

Selecione uma detecção para ver seus detalhes.

## <a name="what-problems-are-detected"></a>Quais problemas foram detectados?
Detecção inteligente detecta e notifica sobre uma variedade de problemas, como:

* [Detecção inteligente - anomalias de falha](../../azure-monitor/app/proactive-failure-diagnostics.md). Usamos o aprendizado de máquina para definirmos a taxa esperada de solicitações com falha para seu aplicativo, fazendo a correlação com a carga e com outros fatores. Se a taxa de falha for além do envelope esperado, podemos enviar um alerta.
* [Detecção inteligente - anomalias de desempenho](../../azure-monitor/app/proactive-performance-diagnostics.md). Você recebe notificações se o tempo de resposta de uma duração da operação ou dependência está diminuindo em comparação com a linha de base histórica ou identificamos um padrão anormal no tempo de resposta ou tempo de carregamento de página.   
* Degradações gerais e problemas, como [uma degradação de rastreamento](https://docs.microsoft.com/azure/azure-monitor/app/proactive-trace-severity), [vazamento de memória](https://docs.microsoft.com/azure/azure-monitor/app/proactive-potential-memory-leak), [anormal aumentar volume de exceção](https://docs.microsoft.com/azure/azure-monitor/app/proactive-exception-volume) e [segurança antipadrões](https://docs.microsoft.com/azure/azure-monitor/app/proactive-application-security-detection-pack).

(Os links de Ajuda em cada notificação levam você para os artigos relevantes).

## <a name="smart-detection-email-notifications"></a>Notificações de email de detecção inteligente

Todas as regras de detecção inteligente, exceto para as regras de marcado como _visualização_, são configuradas por padrão para enviar notificações por email quando as detecções são encontradas.

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

