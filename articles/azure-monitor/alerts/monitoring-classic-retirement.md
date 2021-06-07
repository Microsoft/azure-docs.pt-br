---
title: Atualização do monitoramento clássico de & de alertas no Azure Monitor
description: Descrição da desativação de serviços e funcionalidades de monitoramento clássicos, mostrada anteriormente em portal do Azure em alertas (clássico).
author: yanivlavi
services: azure-monitor
ms.topic: conceptual
ms.date: 02/14/2021
ms.author: yalavi
ms.openlocfilehash: bfa92a2fc58d479edd328dba9bf02d57ec66c0c9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102041086"
---
# <a name="unified-alerting--monitoring-in-azure-monitor-replaces-classic-alerting--monitoring"></a>O alerta e monitoramento unificados no Monitor do Azure substituem o alerta e monitoramento clássicos

Azure Monitor é uma pilha de monitoramento unificada que dá suporte a ' uma métrica ' e a ' um alerta ' nos recursos do Azure. Veja mais informações nesta [postagem no blog](https://azure.microsoft.com/blog/new-full-stack-monitoring-capabilities-in-azure-monitor/). As novas plataformas de monitoramento e alerta do Azure foram criadas para serem mais rápidas, inteligentes e extensíveis – mantendo o ritmo com os crescentes extensãos de computação em nuvem e em linha com a filosofia de nuvem inteligente da Microsoft.

Com a nova plataforma de monitoramento e alerta do Azure em vigor, os alertas clássicos no Azure Monitor são desativados para usuários de nuvem pública, embora ainda estejam em uso limitado até **31 de maio de 2021**. Os alertas clássicos para a nuvem do Azure governamental e o Azure China 21Vianet serão desativados em **29 de fevereiro de 2024**.

 ![Alerta clássico no portal do Azure](media/monitoring-classic-retirement/monitor-alert-screen2.png) 

Nós encorajamos você a começar e recriar seus alertas na nova plataforma.

> [!IMPORTANT]
> Regras de alerta clássicas criadas no Log de Atividades não serão preteridas nem migradas. Todas as regras de alerta clássicas criadas no Log de Atividades podem ser acessadas e usadas no estado em que se encontram nos novos Alertas do Azure Monitor. Para obter mais informações, confira [Criar, exibir e gerenciar alertas de log de atividades usando o Azure Monitor](./alerts-activity-log.md). De forma semelhante, os Alertas na Integridade do Serviço podem ser acessados e usados no estado em que se encontram na nova seção de Integridade do Serviço. Para obter detalhes, confira [alertas em notificações da Integridade do Serviço](../../service-health/alerts-activity-log-service-notifications-portal.md).

## <a name="unified-metrics-and-alerts-for-azure-resources"></a>Métricas e alertas unificados para recursos do Azure

Em março de 2018, lançamos a próxima geração de alertas para recursos do Azure. A plataforma de métricas e os alertas mais recentes são mais rápidos e fornecem mais granularidade usando dimensões. As dimensões permitem fatiar e filtrar para a combinação, condição ou operação de valor específico. Os alertas de métrica mais recentes usam grupos de ação que permitem mais notificações e ações de automação. Veja mais informações sobre como [Gerenciar alertas de métrica usando Azure monitor](./alerts-metric.md).

As métricas mais recentes para recursos do Azure estão disponíveis como:

- **Monitor do Azure Métricas de plataforma padrão** - que fornece métricas populares pré-preenchidas de vários serviços e produtos do Azure. Para obter mais informações, consulte este artigo sobre [métricas com suporte no Azure Monitor](./alerts-metric-near-real-time.md#metrics-and-dimensions-supported) e [dão suporte a alertas de métrica no Azure Monitor](./alerts-metric-overview.md#supported-resource-types-for-metric-alerts).
- **Métricas personalizadas do Monitor do Azure** - que fornece métricas de fontes controladas pelo usuário, incluindo o agente do Azure Diagnostics. Para obter mais informações, consulte este artigo sobre [métricas personalizadas no Monitor do Azure](../essentials/metrics-custom-overview.md). Usando métricas personalizadas, você também pode publicar as métricas coletadas pelo [agente do Windows Azure Diagnostics](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md) e [InfluxData Telegraf agente](../essentials/collect-custom-metrics-linux-telegraf.md).

## <a name="retirement-of-classic-monitoring-and-alerting-platform"></a>Desativação do clássico, monitoramento e alertas de plataforma

Como mencionado anteriormente, monitoramento e alertas clássicos mais antigos são desativados para usuários de nuvem pública. Ele inclui o fechamento de APIs relacionadas, portal do Azure interface e serviços nela, embora ainda esteja em uso limitado até **31 de maio de 2021**. Os alertas clássicos para a nuvem do Azure governamental e o Azure China 21Vianet serão desativados em **29 de fevereiro de 2024**.

Especificamente, o escopo de aposentadoria é para métricas clássicas disponíveis atualmente na [seção alertas (clássico)](./alerts-classic.overview.md) do portal do Azure e acessíveis como recursos [Microsoft. insights/alertrules](/rest/api/monitor/alertrules) .

Isso significa:

- O serviço de monitoramento e alertas clássicos será desativado e não estará mais disponível para a criação de novas regras de alerta.
- Qualquer regra de alerta que continue a existir em alertas (clássico) continuará a executar e acionar notificações.
- As regras de alerta mais clássicas serão migradas. O processo será contínuo sem nenhum tempo de inatividade e os clientes não terão nenhuma perda na cobertura de monitoramento.
- As notificações disparadas incluirão novas estruturas de cargas. O destino precisará ser adaptado para trabalhar com a nova estrutura.
- Algumas [regras de alerta clássicas que não podem ser migradas automaticamente](alerts-understand-migration.md#manually-migrating-classic-alerts-to-newer-alerts) e exigirão que a ação manual dos usuários continuem a ser executadas.

> [!IMPORTANT]
> A Azure Monitor distribuiu uma [ferramenta para migrar voluntariamente](alerts-using-migration-tool.md) suas regras de alerta clássicas para a nova plataforma. As regras restantes serão migradas automaticamente depois que o serviço for desativado. Os clientes precisarão garantir que a automação que consome a carga da regra de alerta clássica seja adaptada para lidar com a nova carga de [métricas unificadas e alertas para outros recursos do Azure](#unified-metrics-and-alerts-for-azure-resources), após a migração das regras de alerta clássicas. Para obter mais informações, consulte [preparar para a migração da regra de alerta clássica](alerts-prepare-migration.md).

## <a name="pricing-for-migrated-alert-rules"></a>Preços das regras de alerta migradas

Estamos distribuindo uma ferramenta de migração para ajudá-lo a migrar seus [alertas Azure monitor clássicos](./alerts-classic.overview.md) para a nova experiência de alertas. As regras de alerta migradas e os grupos de ação migrados correspondentes (email, webhook ou LogicApp) permanecerão livres de encargos. A funcionalidade que você tinha com alertas clássicos, incluindo a capacidade de editar o limite, o tipo de agregação e a granularidade de agregação, continuará disponível gratuitamente com sua regra de alerta migrada. No entanto, se você editar a regra de alerta migrada para usar qualquer um dos novos recursos, notificações ou tipos de ação da nova plataforma de alerta, uma cobrança correspondente será aplicada. Para obter mais informações sobre os preços das regras e notificações de alerta, consulte [preços de Azure monitor](https://azure.microsoft.com/pricing/details/monitor/).

Veja a seguir exemplos de casos em que você incorrerá em um encargo para sua regra de alerta:

- Qualquer nova regra de alerta (não migrada) criada além das unidades gratuitas, na nova plataforma do Monitor do Azure
- Qualquer dado ingerido e retido além das unidades gratuitas incluídas pelo Monitor do Azure
- Qualquer teste da web de vários testes executado pelo Application Insights
- Quaisquer métricas personalizadas armazenadas além das unidades gratuitas incluídas no Monitor do Azure
- Quaisquer regras de alerta migradas que são editadas para usar recursos de alerta de métrica mais recentes, como frequência, vários recursos/dimensões, [limites dinâmicos](../alerts/alerts-dynamic-thresholds.md), alteração de recurso/sinal e assim por diante.
- Quaisquer grupos de ação migrados que são editados para usar notificações mais recentes ou tipos de ação como SMS, chamada de voz e/ou integração de ITSM.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre o [novo unificada do Azure Monitor](../overview.md).
* Saiba mais sobre os novos [alertas do Azure](./alerts-overview.md).