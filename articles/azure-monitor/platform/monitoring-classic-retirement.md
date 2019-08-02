---
title: O alerta e monitoramento unificados no Monitor do Azure substituem o alerta e monitoramento clássicos
description: Visão geral da desativação de serviços e funcionalidade de monitoramento clássicos, mostrada anteriormente no portal do Azure em Alertas (clássico). O alerta e monitoramento clássico inclui alertas métricos clássicos para recursos do Azure, alertas de métricas clássicas para Application Insights, alertas de webtest clássicos para Application Insights, alertas baseados em métricas personalizadas clássicas para Application Insights e alertas clássicos para o SmartDetection v1 do Application Insights
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 2/7/2019
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 28dba8af059e24040abb254dd4ecd8e336d8221d
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594293"
---
# <a name="unified-alerting--monitoring-in-azure-monitor-replaces-classic-alerting--monitoring"></a>O alerta e monitoramento unificados no Monitor do Azure substituem o alerta e monitoramento clássicos

O Azure Monitor se tornou um serviço unificado de monitoramento de pilha completa, que agora dá suporte a "Uma métrica" e "Um alerta" nos recursos. Para obter mais informações, confira nossa [postagem sobre o novo Azure Monitor](https://azure.microsoft.com/blog/new-full-stack-monitoring-capabilities-in-azure-monitor/). As novas plataformas de monitoramento e alerta do Azure foram desenvolvidas para serem mais rápidas, mais inteligentes e extensíveis – acompanhando a crescente expansão da computação em nuvem e em linha com a filosofia de Nuvem Inteligente da Microsoft. 

Com o novo Azure monitoramento e alertas plataforma em vigor, podemos será desativado "clássico" monitoramento e alertas de plataforma - hospedada dentro *exibir alertas clássicos* seção de alertas do Azure, **será preterido até Agosto de 2019 no público do Azure de nuvens**. [Nuvem do Azure governamental](../../azure-government/documentation-government-welcome.md) e [do Azure na China 21Vianet](https://docs.azure.cn/) não serão afetados.

> [!NOTE]
> Devido a atraso na disponibilização da ferramenta de migração, a data de desativação para a migração de alertas clássicos tiver sido [estendido até 31 de agosto de 2019](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/) da data do dia 30 de junho de 2019 originalmente anunciada.

 ![Alerta clássico no portal do Azure](media/monitoring-classic-retirement/monitor-alert-screen2.png) 

Nós encorajamos você a começar e recriar seus alertas na nova plataforma. Para clientes que têm um grande número de alertas, estamos [sem interrupção em fases](alerts-understand-migration.md#rollout-phases), um [ferramenta de migração voluntária](alerts-using-migration-tool.md) mover alertas clássicos existentes para o novo sistema de alertas sem interrupção ou custos adicionais.

> [!IMPORTANT]
> Regras de alerta clássicas criadas no Log de Atividades não serão preteridas nem migradas. Todas as regras de alerta clássicas criadas no Log de Atividades podem ser acessadas e usadas no estado em que se encontram nos novos Alertas do Azure Monitor. Para obter mais informações, confira [Criar, exibir e gerenciar alertas de log de atividades usando o Azure Monitor](../../azure-monitor/platform/alerts-activity-log.md). De forma semelhante, os Alertas na Integridade do Serviço podem ser acessados e usados no estado em que se encontram na nova seção de Integridade do Serviço. Para obter detalhes, confira [alertas em notificações da Integridade do Serviço](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).

## <a name="unified-metrics-and-alerts-in-application-insights"></a>Métricas unificadas e alertas em insights de aplicativos

Agora, a plataforma de métrica mais recente do Monitor do Azure agora ativará o monitoramento proveniente do Application Insights. Essa movimentação significa que o Application Insights se conectará a Action Groups, permitindo muito mais opções do que apenas as chamadas de email e webhook anteriores. Os alertas agora podem acionar chamadas de voz, funções do Azure, aplicativos lógicos, SMS e ferramentas de ITSM, como o ServiceNow e os Runbooks de automação. Com monitoramento e alertas quase em tempo real, a nova plataforma permite que os usuários do Application Insights aproveitem a mesma tecnologia que potencializa o monitoramento em outros recursos do Azure e sustenta o monitoramento de produtos da Microsoft.

O novo monitoramento e alerta unificados para Application Insights abrangerá:

- **Métricas do Application Insights plataforma** – que fornece métricas predefinidas populares do produto do Application Insights. Para obter mais informações, consulte este artigo sobre como usar [métricas de plataforma para o Application Insights no novo Azure Monitor](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics).
- **Application Insights Disponibilidade e teste na Web** - que fornece a capacidade de avaliar a capacidade de resposta e a disponibilidade de seu aplicativo ou servidor da web. Para obter mais informações, consulte este artigo sobre o uso de [Testes de disponibilidade e alertas para o Application Insights no novo Monitor do Azure](../../azure-monitor/app/monitor-web-app-availability.md).
- **Métricas do aplicativo Insights personalizado** – que permite definir e emitem suas próprias métricas para monitoramento e alertas. Para obter mais informações, consulte este artigo sobre como usar [métrica personalizada para o Application Insights no novo Azure Monitor](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation).
- **Anomalias de falha de Insights do aplicativo (parte da Detecção Inteligente)** - que notifica automaticamente em tempo real se o seu aplicativo da Web sofrer um aumento anormal na taxa de solicitações HTTP com falha ou chamadas de dependência. Para obter mais informações, consulte este artigo sobre como usar [detecção inteligente – anomalias de falha](https://docs.microsoft.com/azure/azure-monitor/app/proactive-failure-diagnostics).

## <a name="unified-metrics-and-alerts-for-other-azure-resources"></a>Métricas e Alertas unificadas para outros recursos do Azure

Desde março de 2018, a próxima geração de monitoramento multidimensional e de alertas para recursos do Azure está em disponibilidade. Agora, a nova plataforma métrica e os alertas são mais rápidos com recursos quase em tempo real. Mais importante ainda, os alertas de plataforma de métrica mais recentes fornecem mais granularidade, pois a plataforma mais nova inclui a opção de dimensões, que permitem dividir e filtrar para combinação, condição ou operação de valor específico. Como todos os alertas no novo Monitor do Azure, os alertas de métrica mais recentes são mais extensíveis com o uso de ActionGroups - permitindo que as notificações se expandam além do e-mail ou webhook para SMS, voz, função do Azure, Runbook de automação e muito mais. Para obter mais informações, confira [Criar, exibir e gerenciar alertas de métrica usando o Azure Monitor](../../azure-monitor/platform/alerts-metric.md).
As métricas mais recentes para recursos do Azure estão disponíveis como:

- **Monitor do Azure Métricas de plataforma padrão** - que fornece métricas populares pré-preenchidas de vários serviços e produtos do Azure. Para obter mais informações, consulte este artigo sobre [métricas com suporte no Azure Monitor](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported) e [dão suporte a alertas de métrica no Azure Monitor](../../azure-monitor/platform/alerts-metric-overview.md#supported-resource-types-for-metric-alerts).
- **Métricas personalizadas do Monitor do Azure** - que fornece métricas de fontes controladas pelo usuário, incluindo o agente do Azure Diagnostics. Para obter mais informações, consulte este artigo sobre [métricas personalizadas no Monitor do Azure](../../azure-monitor/platform/metrics-custom-overview.md). Usando métricas personalizadas, você também pode publicar as métricas coletadas pelo [agente do Windows Azure Diagnostics](../../azure-monitor/platform/collect-custom-metrics-guestos-resource-manager-vm.md) e [InfluxData Telegraf agente](../../azure-monitor/platform/collect-custom-metrics-linux-telegraf.md).

## <a name="retirement-of-classic-monitoring-and-alerting-platform"></a>Desativação do clássico, monitoramento e alertas de plataforma

Conforme mencionado anteriormente, clássico de monitoramento e alerta plataforma atualmente utilizável na [seção de alertas (clássico)](../../azure-monitor/platform/alerts-classic.overview.md) do Azure portal será desativado em provenientes dado meses eles foram substituídos por sistema mais recente.
Clássico mais antigo, monitoramento e alertas será desativado em 31 de agosto de 2019; incluindo o fechamento de APIs relacionadas, interface do portal do Azure e serviços nele. Especificamente, esses recursos serão preteridos:

- Métricas e alertas mais antigos (clássicos) para recursos do Azure como disponíveis atualmente por meio da seção [Alertas (clássico)](../../azure-monitor/platform/alerts-classic.overview.md) do portal do Azure; acessível como recurso de [microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules)
- Plataformas e métricas personalizadas (clássicas) mais antigas para o Application Insights, bem como alertas sobre elas, como estão atualmente disponíveis na seção [Alertas (clássico)](../../azure-monitor/platform/alerts-classic.overview.md) do portal do Azure e acessível como recurso de [microsoft.insights / alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules)
- Mais antiga alerta anomalias de falha (clássica) está disponível como [detecção inteligente no Application Insights](../../azure-monitor/app/proactive-diagnostics.md) no portal do Azure; com alertas configurados mostrado na [alertas (clássica) seção](../../azure-monitor/platform/alerts-classic.overview.md) do Azure Portal

Todos os clássicos de monitoramento e alerta sistemas, incluindo correspondente [API](https://msdn.microsoft.com/library/azure/dn931945.aspx), [PowerShell](../../azure-monitor/platform/alerts-classic-portal.md), [CLI](../../azure-monitor/platform/alerts-classic-portal.md), [página do portal do Azure](../../azure-monitor/platform/alerts-classic-portal.md)e [ Modelo de recurso](../../azure-monitor/platform/alerts-enable-template.md) permanecerá utilizável até o final de agosto de 2019. 

No final de agosto de 2019, no Azure Monitor:

- Serviço de monitoramento e alertas clássico será desativado e não está mais disponível para a criação de novas regras de alerta.
- Quaisquer regras de alerta que continuam existindo em alertas (clássicas) além de 2019 agosto continuar a executar e acionar notificações, mas não estar disponíveis para modificação.
- Iniciar setembro de 2019, regras de alerta no monitoramento e alertas que podem ser migrado, clássico será movido automaticamente pela Microsoft em seu equivalente na nova plataforma do Azure monitor em fases que abrangem algumas semanas. O processo será contínuo sem nenhum tempo de inatividade e os clientes não terão nenhuma perda na cobertura de monitoramento.
- Regras de alerta migradas para a nova plataforma fornecerão cobertura de monitoramento como antes, mas dispararão notificação com novos conteúdos. Qualquer endereço de email, o ponto de extremidade de webhook ou o link do aplicativo lógico associado à regra de alerta clássica será transferido quando migrados, mas não podem se comportar corretamente como conteúdo de alerta será diferente na nova plataforma.
- Alguns [regras de alerta clássicas que não podem ser migradas automaticamente](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated) e exigem uma ação manual dos usuários irá continuar a executar até de 2020 de junho.

> [!IMPORTANT]
> Monitor do Microsoft Azure foi distribuída em fases [ferramenta para migrar voluntariamente](alerts-using-migration-tool.md) regras de alerta clássicas para a nova plataforma em breve. E executá-lo à força para todas as regras de alerta clássicas que ainda existem e podem ser migrados, começando de setembro de 2019. Os clientes precisam garantir que a automação que consome conteúdo de regra de alerta clássica seja adaptada para lidar com o novo conteúdo de [Métricas e Alertas Unificados no Application Insights](#unified-metrics-and-alerts-in-application-insights) ou [Métricas e Alertas Unificados para outros recursos do Azure](#unified-metrics-and-alerts-for-other-azure-resources) após a migração das regras de alerta clássicas. Para obter mais informações, consulte [preparar para a migração de regra de alerta clássico](alerts-prepare-migration.md)

Estamos desenvolvendo uma ferramenta de migração para permitir que você migre voluntariamente seus alertas do [alertas (clássica) seção](../../azure-monitor/platform/alerts-classic.overview.md) do portal do Azure para os novos alertas do Azure. Todas as regras configuradas em Alertas (clássicos) que são migradas para o novo Monitor do Azure permanecerão gratuitas e não serão cobradas. As regras de alerta clássicas migradas também não suportarão qualquer cobrança por notificações push por e-mail, webhook ou LogicApp. No entanto, o uso de novos tipos de notificação ou ação (como SMS, Chamada de voz, integração de ITSM, etc.) será cobrado, se adicionado a um alerta novo ou migrado. Para obter mais informações, consulte [preços do Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

Além disso, a seguir estarão sob o ambit de passíveis de cobrança [preços do Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/):

- Qualquer nova regra de alerta (não migrada) criada além das unidades gratuitas, na nova plataforma do Monitor do Azure
- Qualquer dado ingerido e retido além das unidades gratuitas incluídas pelo Monitor do Azure
- Qualquer teste da web de vários testes executado pelo Application Insights
- Quaisquer métricas personalizadas armazenadas além das unidades gratuitas incluídas no Monitor do Azure

Este artigo será links serão atualizadas continuamente e detalhes sobre o monitoramento do Azure novo & alerta de funcionalidade, bem como a disponibilidade de ferramentas para ajudar os usuários a adotar a nova plataforma do Azure Monitor.


## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre o [novo unificada do Azure Monitor](../../azure-monitor/overview.md).
* Saiba mais sobre os novos [alertas do Azure](../../azure-monitor/platform/alerts-overview.md).
