---
title: Monitorar aplicativos em execução no Azure Functions com Application Insights – Azure Monitor | Microsoft Docs
description: O Azure Monitor integra-se perfeitamente ao seu aplicativo em execução no Azure Functions e permite que você monitore o desempenho e identifique os problemas com seus aplicativos rapidamente.
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 06/26/2020
ms.openlocfilehash: 6d790a6f02fdc07609e374639c6e452b9088262e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87024564"
---
# <a name="monitoring-azure-functions-with-azure-monitor-application-insights"></a>Monitoramento do Azure Functions com o Azure Monitor Application Insights

O [Azure Functions](../../azure-functions/functions-overview.md) oferece integração interna com o Azure Application Insights para monitorar funções. 

O Application Insights coleta dados de log, desempenho e erro e detecta automaticamente anomalias de desempenho. O Application Insights inclui ferramentas de análise poderosas para ajudar você a diagnosticar problemas e a entender como suas funções são usadas. Quando você tiver a visibilidade dos dados do aplicativo, poderá melhorar continuamente o desempenho e a usabilidade. Você pode até mesmo usar Application Insights durante o desenvolvimento do projeto de aplicativo de funções local. 

A instrumentação do Application Insights necessária é incorporada ao Azure Functions. A única coisa de que você precisa é de uma chave de instrumentação válida para conectar seu aplicativo de funções a um recurso do Application Insights. A chave de instrumentação deve ser adicionada às configurações do aplicativo quando o recurso do aplicativo de funções é criado no Azure. Se seu aplicativo de funções ainda não tiver essa chave, você poderá defini-la manualmente. Para obter mais informações, leia mais sobre o [monitoramento do Azure Functions](../../azure-functions/functions-monitoring.md?tabs=cmd).

## <a name="distributed-tracing-for-java-applications-on-windows-public-preview"></a>Rastreamento distribuído para aplicativos Java no Windows (visualização pública)

> [!IMPORTANT]
> Este recurso está atualmente em visualização pública para Java Azure Functions no Windows, não há suporte para o rastreamento distribuído para Java Azure Functions no Linux. Para o plano de consumo, ele tem um início frio de 8-9 segundos.

Se seus aplicativos forem escritos em Java, você poderá exibir dados mais ricos de seus aplicativos de funções, incluindo solicitações, dependências, logs e métricas. Os dados adicionais também permitem que você veja e diagnostique transações de ponta a ponta e veja o mapa do aplicativo, que agrega muitas transações para mostrar uma exibição topológica de como os sistemas interagem e quais são as taxas médias de desempenho e de erro.

O diagnóstico de ponta a ponta e o mapa do aplicativo fornecem visibilidade de uma única transação/solicitação. Juntos, esses dois recursos são muito úteis para encontrar a causa raiz de problemas de confiabilidade e gargalos de desempenho de acordo com a solicitação.

### <a name="how-to-enable-distributed-tracing-for-java-function-apps"></a>Como habilitar o rastreamento distribuído para aplicativos de funções Java?

Navegue até a folha Visão Geral do aplicativo de funções, vá para configurações. Em Configurações do Aplicativo, clique em "+ Nova configuração de aplicativo". Adicione as duas configurações de aplicativo a seguir com valores abaixo e clique em salvar no canto superior esquerdo. PRONTO.

```
XDT_MicrosoftApplicationInsights_Java -> 1
ApplicationInsightsAgent_EXTENSION_VERSION -> ~2
```

## <a name="next-steps"></a>Próximas etapas

* Leia mais instruções e informações sobre [Monitoramento do Azure Functions](../../azure-functions/functions-monitoring.md)
* Obtenha uma visão geral de [Rastreamento distribuído](./distributed-tracing.md)
* Veja o que o [Mapa do Aplicativo](./app-map.md?tabs=net) pode fazer por sua empresa
* Leia sobre [solicitações e dependências para aplicativos Java](./java-in-process-agent.md)
* Saiba mais sobre o [Azure Monitor](../overview.md) e o [Application Insights](./app-insights-overview.md)
