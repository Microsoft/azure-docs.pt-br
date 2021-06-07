---
title: Detecção Inteligente no Azure Application Insights | Microsoft Docs
description: O Application Insights executa uma análise automática profunda de telemetria do seu aplicativo e o avisará sobre possíveis problemas de desempenho.
ms.topic: conceptual
ms.date: 02/07/2019
ms.openlocfilehash: 974795e853416ff7a4c051530b050fc663c9423d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100585649"
---
# <a name="smart-detection-in-application-insights"></a>Detecção Inteligente no Application Insights
 A Detecção Inteligente avisa automaticamente sobre possíveis problemas de desempenho no seu aplicativo Web. Ele executa uma análise proativa da telemetria que seu aplicativo envia ao [Application Insights](./app-insights-overview.md). Se houver um aumento repentino nas taxas de falha ou nos padrões anormais de desempenho de cliente ou de servidor, você receberá um alerta. Esse recurso não precisa de nenhuma configuração. Ela funciona se o seu aplicativo envia telemetria suficiente.

Você pode acessar as detecções emitidas pela detecção inteligente usando os emails recebidos e da folha Detecção Inteligente.

## <a name="review-your-smart-detections"></a>Revise suas detecções inteligentes
É possível descobrir as detecções de duas maneiras:

* **Você recebe um email** do Application Insights. Aqui está um exemplo típico:
  
    ![Alerta de email](./media/proactive-diagnostics/03.png)
  
    Clique no botão grande para abrir mais detalhadamente no portal.
* A **folha detecção inteligente** no Application Insights. Selecione **Detecção Inteligente** no menu **Investigar** para ver uma lista de detecções recentes.

![Exibir detecções recentes](./media/proactive-diagnostics/04.png)

Selecione uma detecção para ver seus detalhes.

## <a name="what-problems-are-detected"></a>Quais problemas foram detectados?
A detecção inteligente detecta e notifica sobre uma variedade de problemas, como:

* [Detecção inteligente - anomalias de falha](./proactive-failure-diagnostics.md). Usamos o aprendizado de máquina para definirmos a taxa esperada de solicitações com falha para seu aplicativo, fazendo a correlação com a carga e com outros fatores. Se a taxa de falha for além do envelope esperado, podemos enviar um alerta.
* [Detecção inteligente - anomalias de desempenho](./proactive-performance-diagnostics.md). Você recebe notificações se o tempo de resposta de uma duração da operação ou dependência está diminuindo em comparação com a linha de base histórica ou identificamos um padrão anormal no tempo de resposta ou tempo de carregamento de página.   
* Problemas e degradações gerais, como [Degradação de rastreamento](./proactive-trace-severity.md), [Vazamento de memória](./proactive-potential-memory-leak.md), [Aumento anormal no volume de exceção](./proactive-exception-volume.md) e [Antipadrões de segurança](./proactive-application-security-detection-pack.md).

(Os links de Ajuda em cada notificação levam você para os artigos relevantes).

## <a name="smart-detection-email-notifications"></a>Notificações de email de detecção inteligente

Todas as regras de detecção inteligente, exceto para as regras marcadas como _versão prévia_, são configuradas por padrão para enviar notificações por email quando as detecções são encontradas.

Configurar notificações por email para uma regra de detecção inteligente específica pode ser feito abrindo a folha **Configurações** de detecção inteligente e selecionando a regra, que abrirá a folha **Editar regra**.

Como alternativa, você pode alterar a configuração usando modelos do Azure Resource Manager. [Confira Gerenciar regras de detecção inteligente do Application Insights usando modelos do Azure Resource Manager](./proactive-arm-config.md) para obter detalhes.

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Próximas etapas
Essas ferramentas de diagnóstico ajudam você a inspecionar a telemetria do seu aplicativo:

* [Metrics explorer](../essentials/metrics-charts.md)
* [Gerenciador de pesquisa](./diagnostic-search.md)
* [Analytics - linguagem de consulta poderosa](../logs/log-analytics-tutorial.md)

A Detecção Inteligente é totalmente automática. Mas talvez você queira configurar alguns outros alertas?

* [Alertas de métrica configurados manualmente](../alerts/alerts-log.md)
* [Testes de disponibilidade na Web](./monitor-web-app-availability.md)