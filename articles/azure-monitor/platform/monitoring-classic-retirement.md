---
title: Atualização do monitoramento clássico de & de alertas no Azure Monitor
description: Descrição da desativação de serviços e funcionalidades de monitoramento clássicos, mostrada anteriormente em portal do Azure em alertas (clássico).
author: yanivlavi
services: azure-monitor
ms.topic: conceptual
ms.date: 2/7/2019
ms.author: yalavi
ms.subservice: alerts
ms.openlocfilehash: 368ab1bc6a1fc13c3001b437c3c2a8be2bbb9c04
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99525984"
---
# <a name="unified-alerting--monitoring-in-azure-monitor-replaces-classic-alerting--monitoring"></a>O alerta e monitoramento unificados no Monitor do Azure substituem o alerta e monitoramento clássicos

O Azure Monitor se tornou um serviço unificado de monitoramento de pilha completa, que agora dá suporte a "Uma métrica" e "Um alerta" nos recursos. Para obter mais informações, confira nossa [postagem sobre o novo Azure Monitor](https://azure.microsoft.com/blog/new-full-stack-monitoring-capabilities-in-azure-monitor/). As novas plataformas de monitoramento e alerta do Azure foram desenvolvidas para serem mais rápidas, mais inteligentes e extensíveis – acompanhando a crescente expansão da computação em nuvem e em linha com a filosofia de Nuvem Inteligente da Microsoft.

Com a nova plataforma de monitoramento e alerta do Azure em vigor, os alertas clássicos no Azure Monitor são desativados para usuários de nuvem pública, embora ainda estejam em uso limitado para recursos que ainda não dão suporte aos novos alertas. A data de aposentadoria para esses alertas foi ainda mais estendida. Uma nova data será anunciada em breve para a migração de alertas restantes, a [nuvem do Azure governamental](../../azure-government/documentation-government-welcome.md)e o [Azure China 21vianet](https://docs.azure.cn/).

 ![Alerta clássico no portal do Azure](media/monitoring-classic-retirement/monitor-alert-screen2.png) 

Nós encorajamos você a começar e recriar seus alertas na nova plataforma.

> [!IMPORTANT]
> Regras de alerta clássicas criadas no Log de Atividades não serão preteridas nem migradas. Todas as regras de alerta clássicas criadas no Log de Atividades podem ser acessadas e usadas no estado em que se encontram nos novos Alertas do Azure Monitor. Para obter mais informações, confira [Criar, exibir e gerenciar alertas de log de atividades usando o Azure Monitor](./alerts-activity-log.md). De forma semelhante, os Alertas na Integridade do Serviço podem ser acessados e usados no estado em que se encontram na nova seção de Integridade do Serviço. Para obter detalhes, confira [alertas em notificações da Integridade do Serviço](../../service-health/alerts-activity-log-service-notifications-portal.md).

## <a name="unified-metrics-and-alerts-in-application-insights"></a>Métricas unificadas e alertas em insights de aplicativos

Agora, a plataforma de métrica mais recente do Monitor do Azure agora ativará o monitoramento proveniente do Application Insights. Essa movimentação significa que o Application Insights se conectará a Action Groups, permitindo muito mais opções do que apenas as chamadas de email e webhook anteriores. Os alertas agora podem acionar chamadas de voz, funções do Azure, aplicativos lógicos, SMS e ferramentas de ITSM, como o ServiceNow e os Runbooks de automação. Com monitoramento e alertas quase em tempo real, a nova plataforma permite que os usuários do Application Insights aproveitem a mesma tecnologia que potencializa o monitoramento em outros recursos do Azure e sustenta o monitoramento de produtos da Microsoft.

O novo monitoramento e alerta unificados para Application Insights abrangerá:

- **Métricas do Application Insights plataforma** – que fornece métricas predefinidas populares do produto do Application Insights. Para obter mais informações, consulte este artigo sobre como usar [métricas de plataforma para o Application Insights no novo Azure Monitor](../app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics).
- **Application Insights Disponibilidade e teste na Web** - que fornece a capacidade de avaliar a capacidade de resposta e a disponibilidade de seu aplicativo ou servidor da web. Para obter mais informações, consulte este artigo sobre o uso de [Testes de disponibilidade e alertas para o Application Insights no novo Monitor do Azure](../app/monitor-web-app-availability.md).
- **Métricas do aplicativo Insights personalizado** – que permite definir e emitem suas próprias métricas para monitoramento e alertas. Para obter mais informações, consulte este artigo sobre como usar [métrica personalizada para o Application Insights no novo Azure Monitor](../app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation).
- **Anomalias de falha de Insights do aplicativo (parte da Detecção Inteligente)** - que notifica automaticamente em tempo real se o seu aplicativo da Web sofrer um aumento anormal na taxa de solicitações HTTP com falha ou chamadas de dependência. Para obter mais informações, consulte este artigo sobre como usar [a detecção inteligente-anomalias de falha](../app/proactive-failure-diagnostics.md).

## <a name="unified-metrics-and-alerts-for-other-azure-resources"></a>Métricas e Alertas unificadas para outros recursos do Azure

Desde março de 2018, a próxima geração de monitoramento multidimensional e de alertas para recursos do Azure está em disponibilidade. Agora, a nova plataforma métrica e os alertas são mais rápidos com recursos quase em tempo real. Mais importante ainda, os alertas de plataforma de métrica mais recentes fornecem mais granularidade, pois a plataforma mais nova inclui a opção de dimensões, que permitem dividir e filtrar para combinação, condição ou operação de valor específico. Como todos os alertas no novo Monitor do Azure, os alertas de métrica mais recentes são mais extensíveis com o uso de ActionGroups - permitindo que as notificações se expandam além do e-mail ou webhook para SMS, voz, função do Azure, Runbook de automação e muito mais. Para obter mais informações, confira [Criar, exibir e gerenciar alertas de métrica usando o Azure Monitor](./alerts-metric.md).
As métricas mais recentes para recursos do Azure estão disponíveis como:

- **Monitor do Azure Métricas de plataforma padrão** - que fornece métricas populares pré-preenchidas de vários serviços e produtos do Azure. Para obter mais informações, consulte este artigo sobre [métricas com suporte no Azure Monitor](./alerts-metric-near-real-time.md#metrics-and-dimensions-supported) e [dão suporte a alertas de métrica no Azure Monitor](./alerts-metric-overview.md#supported-resource-types-for-metric-alerts).
- **Métricas personalizadas do Monitor do Azure** - que fornece métricas de fontes controladas pelo usuário, incluindo o agente do Azure Diagnostics. Para obter mais informações, consulte este artigo sobre [métricas personalizadas no Monitor do Azure](./metrics-custom-overview.md). Usando métricas personalizadas, você também pode publicar as métricas coletadas pelo [agente do Windows Azure Diagnostics](./collect-custom-metrics-guestos-resource-manager-vm.md) e [InfluxData Telegraf agente](./collect-custom-metrics-linux-telegraf.md).

## <a name="retirement-of-classic-monitoring-and-alerting-platform"></a>Desativação do clássico, monitoramento e alertas de plataforma

Como mencionado anteriormente, monitoramento e alertas clássicos mais antigos são desativados para usuários de nuvem pública; incluindo o fechamento de APIs relacionadas, portal do Azure interface e serviços nela, embora ainda esteja em uso limitado para recursos que ainda não dão suporte aos novos alertas. Especificamente, esses recursos serão preteridos:

- Métricas e alertas mais antigos (clássicos) para recursos do Azure como disponíveis atualmente por meio da seção [Alertas (clássico)](./alerts-classic.overview.md) do portal do Azure; acessível como recurso de [microsoft.insights/alertrules](/rest/api/monitor/alertrules)
- Plataformas e métricas personalizadas (clássicas) mais antigas para o Application Insights, bem como alertas sobre elas, como estão atualmente disponíveis na seção [Alertas (clássico)](./alerts-classic.overview.md) do portal do Azure e acessível como recurso de [microsoft.insights / alertrules](/rest/api/monitor/alertrules)
- Mais antiga alerta anomalias de falha (clássica) está disponível como [detecção inteligente no Application Insights](../app/proactive-diagnostics.md) no portal do Azure; com alertas configurados mostrado na [alertas (clássica) seção](./alerts-classic.overview.md) do Azure Portal

Isso significa:

- O serviço de monitoramento e alertas clássicos será desativado e não estará mais disponível para a criação de novas regras de alerta.
- Qualquer regra de alerta que continue a existir em alertas (clássico) continuará a executar e acionar notificações.
- As regras de alerta no monitoramento clássico & alertas que podem ser migrados, serão automaticamente movidas pela Microsoft para o equivalente na nova plataforma do Azure monitor em fases que abrangem algumas semanas. O processo será contínuo sem nenhum tempo de inatividade e os clientes não terão nenhuma perda na cobertura de monitoramento.
- Regras de alerta migradas para a nova plataforma fornecerão cobertura de monitoramento como antes, mas dispararão notificação com novos conteúdos. Qualquer endereço de email, ponto de extremidade de webhook ou link de aplicativo lógico associado à regra de alerta clássica será postergado quando for migrado, mas poderá não se comportar corretamente, pois a carga do alerta será diferente na nova plataforma.
- Algumas [regras de alerta clássicas que não podem ser migradas automaticamente](alerts-understand-migration.md#manually-migrating-classic-alerts-to-newer-alerts) e exigem que a ação manual dos usuários continuem a ser executadas.

> [!IMPORTANT]
> O monitor de Microsoft Azure foi distribuído na [ferramenta de fases para migrar voluntariamente](alerts-using-migration-tool.md) suas regras de alerta clássicas para a nova plataforma em breve. E executá-lo por força para todas as regras de alerta clássicas que ainda existem e podem ser migradas. Os clientes precisam garantir que a automação que consome conteúdo de regra de alerta clássica seja adaptada para lidar com o novo conteúdo de [Métricas e Alertas Unificados no Application Insights](#unified-metrics-and-alerts-in-application-insights) ou [Métricas e Alertas Unificados para outros recursos do Azure](#unified-metrics-and-alerts-for-other-azure-resources) após a migração das regras de alerta clássicas. Para obter mais informações, consulte [preparar para a migração da regra de alerta clássica](alerts-prepare-migration.md)

Este artigo será links serão atualizadas continuamente e detalhes sobre o monitoramento do Azure novo & alerta de funcionalidade, bem como a disponibilidade de ferramentas para ajudar os usuários a adotar a nova plataforma do Azure Monitor.

## <a name="pricing-for-migrated-alert-rules"></a>Preços das regras de alerta migradas

Estamos implantando uma ferramenta de migração para ajudá-lo a migrar seus [alertas Azure monitor clássicos](./alerts-classic.overview.md) para a nova experiência de alertas. As regras de alerta migradas e os grupos de ação migrados correspondentes (email, webhook ou LogicApp) permanecerão livres de encargos. A funcionalidade que você tinha com alertas clássicos, incluindo a capacidade de editar o limite, o tipo de agregação e a granularidade de agregação, continuará disponível gratuitamente com sua regra de alerta migrada. No entanto, se você editar a regra de alerta migrada para usar qualquer um dos novos recursos, notificações ou tipos de ação da nova plataforma de alerta, uma cobrança correspondente será aplicada. Para obter mais informações sobre os preços das regras e notificações de alerta, consulte [preços de Azure monitor](https://azure.microsoft.com/pricing/details/monitor/).

Veja a seguir exemplos de casos em que você incorrerá em um encargo para sua regra de alerta:

- Qualquer nova regra de alerta (não migrada) criada além das unidades gratuitas, na nova plataforma do Monitor do Azure
- Qualquer dado ingerido e retido além das unidades gratuitas incluídas pelo Monitor do Azure
- Qualquer teste da web de vários testes executado pelo Application Insights
- Quaisquer métricas personalizadas armazenadas além das unidades gratuitas incluídas no Monitor do Azure
- Quaisquer regras de alerta migradas que são editadas para usar recursos de alerta de métrica mais recentes, como frequência, vários recursos/dimensões, [limites dinâmicos](alerts-dynamic-thresholds.md), alteração de recurso/sinal e assim por diante.
- Quaisquer grupos de ação migrados que são editados para usar notificações mais recentes ou tipos de ação como SMS, chamada de voz e/ou integração de ITSM.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre o [novo unificada do Azure Monitor](../overview.md).
* Saiba mais sobre os novos [alertas do Azure](./alerts-overview.md).

