---
title: Resiliência por meio de monitoramento e análise usando o Azure AD B2C | Microsoft Docs
description: Resiliência por meio de monitoramento e análise usando o Azure AD B2C
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.reviewer: ''
ms.date: 11/30/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 90b2cd4521613a7b449598f0d097a7ec1c2958c6
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98724535"
---
# <a name="resilience-through-monitoring-and-analytics"></a>Resiliência por meio de monitoramento e análise

O monitoramento maximiza a disponibilidade e o desempenho de seus aplicativos e serviços. Ele fornece uma solução abrangente para coletar, analisar e agir na telemetria de sua infraestrutura e aplicativos. Os alertas o notificam proativamente quando são encontrados problemas com seu serviço ou aplicativos. Eles permitem que você identifique e resolva problemas antes que os usuários finais do seu serviço os observem. O [log Analytics do Azure ad](https://azure.microsoft.com/services/monitor/?OCID=AID2100131_SEM_6d16332c03501fc9c1f46c94726d2264:G:s&ef_id=6d16332c03501fc9c1f46c94726d2264:G:s&msclkid=6d16332c03501fc9c1f46c94726d2264#features) ajuda você a analisar, pesquisar logs de auditoria e logs de entrada e criar modos de exibição personalizados.

## <a name="monitor-and-get-notified-through-alerts"></a>Monitorar e ser notificado por meio de alertas

Monitorar o sistema e a infraestrutura é essencial para garantir a integridade geral dos seus serviços. Ele começa com a definição de métricas de negócios, como nova chegada de usuário, taxas de autenticação do usuário final e conversão. Configure esses indicadores para monitorar. Se você estiver planejando um surto futuro devido ao tráfego de promoção ou de feriados, revise suas estimativas especificamente para o evento e o parâmetro de comparação correspondente para as métricas de negócios. Após o evento, volte para o parâmetro de comparação anterior.

Da mesma forma, para detectar falhas ou interrupções de desempenho, a configuração de uma boa linha de base e a definição de alertas é uma prática indispensável para responder a problemas emergentes imediatamente.

![Imagem mostra componentes de monitoramento e análise](media/resilience-with-monitoring-alerting/monitoring-analytics-architecture.png)

### <a name="how-to-implement-monitoring-and-alerting"></a>Como implementar monitoramento e alertas

- **Monitoramento**: use [Azure monitor](../../active-directory-b2c/azure-monitor.md) para monitorar continuamente a integridade em relação aos principais objetivos de nível de serviço (SLO) e obter notificação sempre que ocorrer uma alteração crítica. Comece identificando Azure AD B2C política ou um aplicativo como um componente crítico de sua empresa cuja integridade precisa ser monitorada para manter o SLO. Identifique os principais indicadores que se alinham com seu SLOs.
Por exemplo, acompanhe as métricas a seguir, uma vez que uma queda repentina em ambos resultará em uma perda no negócio.

  - **Total de solicitações**: o número "n" total de solicitações enviadas à política de Azure ad B2C.

  - **Taxa de sucesso (%)**: solicitações bem-sucedidas/número total de solicitações.

  Acesse os [principais indicadores](../../active-directory-b2c/view-audit-logs.md) no [Application insights](../../active-directory-b2c/analytics-with-application-insights.md) , onde Azure ad B2C logs baseados em políticas, [logs de auditoria](../../active-directory-b2c/analytics-with-application-insights.md)e logs de entrada são armazenados.  

   - **Visualizações**: usando painéis de compilação do log Analytics para monitorar visualmente os principais indicadores.

   - **Período atual**: criar gráficos temporais para mostrar alterações no total de solicitações e taxa de êxito (%) no período atual, por exemplo, semana atual.

   - **Período anterior**: criar gráficos temporais para mostrar alterações no total de solicitações e taxa de êxito (%) em um período anterior para fins de referência, por exemplo, última semana.

- **Alerta**: usar o log Analytics define [alertas](../../azure-monitor/platform/alerts-log.md) que são disparados quando há alterações súbitas nos indicadores de chave. Essas alterações podem afetar negativamente o SLOs. Os alertas usam várias formas de métodos de notificação, incluindo email, SMS e WebHooks. Comece definindo um critério que atue como um limite no qual o alerta será disparado. Por exemplo:
  - Alerta contra queda abrupta em total de solicitações: disparar um alerta quando o número total de solicitações for descartado abruptamente. Por exemplo, quando há um descarte de 25% no número total de solicitações comparadas ao período anterior, gere um alerta.  
  - Alertar em relação à taxa de êxito de queda significativa (%): disparar um alerta quando a taxa de êxito da política selecionada for eliminada significativamente.
  - Após receber um alerta, solucione o problema usando [log Analytics](../reports-monitoring/howto-install-use-log-analytics-views.md), [Application insights](../../active-directory-b2c/troubleshoot-with-application-insights.md)e [extensão de vs Code](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c) para Azure ad B2C. Depois de resolver o problema e implantar um aplicativo ou uma política atualizada, ele continua a monitorar os principais indicadores até que eles retornem ao intervalo normal.

- **Alertas de serviço**: Use o [Azure ad B2C alertas de nível de serviço](../../service-health/service-health-overview.md) para ser notificado sobre problemas de serviço, manutenção planejada, consultoria de integridade e consultoria de segurança.

- **Relatórios**: [usando o log Analytics](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md), crie relatórios que ajudam você a obter noções básicas sobre o usuário, desafios técnicos e oportunidades de crescimento.
  - **Painel de integridade**: Crie [painéis personalizados usando o recurso de painel do Azure](../../azure-monitor/learn/tutorial-app-dashboards.md) , que oferece suporte à adição de gráficos usando consultas log Analytics. Por exemplo, identifique o padrão de entradas bem-sucedidas e com falha, os motivos de falha e a telemetria sobre os dispositivos usados para fazer as solicitações.
  - **Abandonar Azure ad B2C jornadas**: Use a [pasta de trabalho](https://github.com/azure-ad-b2c/siem#list-of-abandon-journeys) para acompanhar a lista de percursos Azure ad B2Cdos em que o usuário iniciou a entrada ou a jornada de inscrição, mas nunca a concluiu. Ele fornece detalhes sobre a ID da política e a divisão de etapas que são tomadas pelo usuário antes de abandonar a jornada.
  - **Azure ad B2C pastas de trabalho de monitoramento**: Use as [pastas de trabalho de monitoramento](https://github.com/azure-ad-b2c/siem), que incluem painel de Azure ad B2C, operações de MFA (autenticação multifator), relatório de acesso condicional e logs de pesquisa por CorrelationId, para obter melhores informações sobre a integridade de seu ambiente de Azure ad B2C.
  
## <a name="next-steps"></a>Próximas etapas

- [Recursos de resiliência para desenvolvedores de Azure AD B2C](resilience-b2c.md)
  - [Experiência do usuário final resiliente](resilient-end-user-experience.md)
  - [Interfaces resilientes com processos externos](resilient-external-processes.md)
  - [Resiliência por meio de práticas recomendadas para desenvolvedores](resilience-b2c-developer-best-practices.md)
- [Crie resiliência em sua infraestrutura de autenticação](resilience-in-infrastructure.md)
- [Aumentar a resiliência de autenticação e autorização em seus aplicativos](resilience-app-development-overview.md)
