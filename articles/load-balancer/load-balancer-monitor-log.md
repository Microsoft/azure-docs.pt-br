---
title: Monitorar operações, eventos e contadores para um balanceador de carga público
titleSuffix: Azure Load Balancer
description: Saiba como habilitar o registro em log para Azure Load Balancer.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/05/2020
ms.author: allensu
ms.openlocfilehash: 59359cdd3e3c035d4cb6789295d41bb3908019bb
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98785813"
---
# <a name="azure-monitor-logs-for-azure-standard-load-balancer"></a>Logs de Azure Monitor do Azure Standard Load Balancer

Você pode usar diferentes tipos de logs de Azure Monitor para gerenciar e solucionar problemas de Standard Load Balancer do Azure. Os logs podem ser transmitidos para um hub de eventos ou um espaço de trabalho Log Analytics. Você pode extrair todos os logs do armazenamento de BLOBs do Azure e exibi-los em ferramentas como Excel e Power BI. 

Os tipos de logs são:

* **Logs de atividade:** Você pode exibir todas as atividades que estão sendo enviadas para suas assinaturas do Azure, junto com seu status. Para obter mais informações, consulte [Exibir logs de atividades para monitorar ações em recursos](../azure-resource-manager/management/view-activity-logs.md). Os logs de atividade são habilitados por padrão e podem ser exibidos no portal do Azure. Esses logs estão disponíveis para o Load Balancer básico do Azure e Standard Load Balancer.
* **Métricas de Standard Load Balancer:** Você pode usar esse log para consultar as métricas exportadas como logs para Standard Load Balancer. Esses logs estão disponíveis apenas para Standard Load Balancer.

> [!IMPORTANT]
> Os logs de eventos de alertas de investigação de integridade e Load Balancer não estão funcionais no momento e estão listados nos [problemas conhecidos do Azure Load Balancer](whats-new.md#known-issues). 

> [!IMPORTANT]
> Os logs estão disponíveis apenas para os recursos implantados no modelo de implantação do Azure Resource Manager. Você não pode usar logs para recursos no modelo de implantação clássico. Para saber mais sobre esses modelos de implantação, consulte [Understanding Resource Manager deployment and classic deployment (Noções básicas sobre a implantação do Resource Manager e a implantação clássica)](../azure-resource-manager/management/deployment-models.md).

## <a name="enable-logging"></a>Habilitar o registro em log

O log de atividade é habilitado automaticamente para todos os recursos do Resource Manager. Habilite o log de investigação de integridade e de evento para começar a coletar os dados disponíveis por meio desses logs. Use as seguintes etapas:

1. Entre no [portal do Azure](https://portal.azure.com). Se você ainda não tiver um balanceador de carga, [crie um](./quickstart-load-balancer-standard-public-portal.md) antes de continuar.
1. No portal, selecione **grupos de recursos**.
2. Selecione **\<resource-group-name>** onde está o balanceador de carga.
3. Selecione seu balanceador de carga.
4. Selecione Configurações de diagnóstico do **log de atividades**  >  .
5. No painel **configurações de diagnóstico** , em **configurações de diagnóstico**, selecione **+ Adicionar configuração de diagnóstico**.
6. No painel criação de **configurações de diagnóstico** , insira **MyLBDiagnostics** na caixa **nome** .
7. Você tem três opções para as **configurações de diagnóstico**. Você pode escolher um, dois ou todos os três e configurar cada um para seus requisitos:

   * **Arquivar em uma conta de armazenamento**. Você precisará de uma conta de armazenamento já criada para esse processo. Para criar uma conta de armazenamento, consulte [Criar uma conta de armazenamento](../storage/common/storage-account-create.md?tabs=azure-portal).
     1. Marque a caixa de seleção **arquivar em uma conta de armazenamento** .
     2. Selecione **Configurar** para abrir o painel **selecionar uma conta de armazenamento** .
     3. Na lista suspensa **assinatura** , selecione a assinatura na qual sua conta de armazenamento foi criada.
     4. Na lista suspensa **conta de armazenamento** , selecione o nome da sua conta de armazenamento.
     5. Selecione **OK**.

   * **Transmitir para um hub de eventos**. Você precisará de um hub de eventos já criado para esse processo. Para criar um hub de eventos, consulte [início rápido: criar um hub de eventos usando o portal do Azure](../event-hubs/event-hubs-create.md).
     1. Marque a caixa de seleção **Transmitir para um hub de eventos**.
     2. Selecione **Configurar** para abrir o painel **selecionar Hub de eventos** .
     3. Na lista suspensa **assinatura** , selecione a assinatura na qual o Hub de eventos foi criado.
     4. Na lista suspensa **selecionar namespace do hub de eventos** , selecione o namespace.
     5. Na lista suspensa **selecionar nome da política do hub de eventos** , selecione o nome.
     6. Selecione **OK**.

   * **Enviar para log Analytics**. Você precisará já ter um espaço de trabalho do log Analytics criado e configurado para esse processo. Para criar um espaço de trabalho Log Analytics, consulte [criar um espaço de trabalho log Analytics no portal do Azure](../azure-monitor/learn/quick-create-workspace.md).
     1. Marque a caixa de seleção **Enviar para o Log Analytics**.
     2. Na lista suspensa **assinatura** , selecione a assinatura na qual está o espaço de trabalho do log Analytics.
     3. Na lista suspensa **log Analytics espaço de trabalho** , selecione o espaço de trabalho.

8. Na seção **métrica** do painel **configurações de diagnóstico** , marque a caixa de seleção **biométricas** .

9. Verifique se tudo está correto e, em seguida, selecione **salvar** na parte superior do painel criação de **configurações de diagnóstico** .

## <a name="view-and-analyze-the-activity-log"></a>Exibir e analisar o log de atividades

O log de atividades é gerado por padrão. Você pode configurá-lo para ser exportado em um nível de assinatura seguindo [as instruções neste artigo](../azure-monitor/platform/activity-log.md). Saiba mais sobre esses logs lendo o artigo [Exibir logs de atividades para monitorar ações em recursos](../azure-resource-manager/management/view-activity-logs.md) .

Você pode exibir e analisar os dados do log de atividades usando um dos seguintes métodos:

* **Ferramentas do Azure:** Recupere informações do log de atividades por meio de Azure PowerShell, o CLI do Azure, a API REST do Azure ou o portal do Azure. O artigo [operações de auditoria com o Resource Manager](../azure-resource-manager/management/view-activity-logs.md) fornece instruções passo a passo para cada método.
* **Power bi:** Se você ainda não tiver uma conta de [Power bi](https://powerbi.microsoft.com/pricing) , poderá experimentá-la gratuitamente. Usando a [integração dos logs de auditoria do Azure para Power bi](https://powerbi.microsoft.com/integrations/azure-audit-logs/), você pode analisar seus dados com painéis pré-configurados. Ou você pode personalizar modos de exibição para atender às suas necessidades.

## <a name="view-and-analyze-metrics-as-logs"></a>Exibir e analisar métricas como logs
Usando a funcionalidade de exportação no Azure Monitor, você pode exportar suas métricas de Load Balancer. Essas métricas irão gerar uma entrada de log para cada intervalo de amostragem de um minuto.

A exportação de métricas para logs está habilitada em um nível por recurso. Para habilitar esses logs:

1. Vá para o painel **configurações de diagnóstico** .
1. Filtre por grupo de recursos e selecione a instância de Load Balancer para a qual você deseja habilitar a exportação de métricas. 
1. Quando a página Configurações de diagnóstico para Load Balancer estiver ativa, selecione **biométricas** para exportar métricas qualificadas como logs.

Para limitações de exportação de métrica, consulte a seção [limitações](#limitations) deste artigo.

Depois de habilitar as **biométricas** nas configurações de diagnóstico de Standard Load Balancer, se você estiver usando um hub de eventos ou log Analytics espaço de trabalho, esses logs serão preenchidos na tabela **AzureMonitor** . 

Se você estiver exportando para o armazenamento, conecte-se à sua conta de armazenamento e recupere as entradas de log JSON para logs de investigação de eventos e de integridade. Depois de baixar os arquivos JSON, você pode convertê-los em CSV e exibi-los no Excel, Power BI ou em qualquer outra ferramenta de visualização de dados. 

> [!TIP]
> Se você estiver familiarizado com o Visual Studio e os conceitos básicos de alteração de valores para constantes e variáveis em C#, poderá usar as [ferramentas de conversor de log](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponíveis no github.

## <a name="stream-to-an-event-hub"></a>Transmitir para um hub de eventos
Quando as informações de diagnóstico são transmitidas para um hub de eventos, você pode usá-las para análise de log centralizada em uma ferramenta SIEM de parceiro com integração de Azure Monitor. Para obter mais informações, consulte [transmitir dados de monitoramento do Azure para um hub de eventos](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration).

## <a name="send-to-log-analytics"></a>Enviar para o Log Analytics
Você pode enviar informações de diagnóstico para recursos no Azure diretamente para um espaço de trabalho Log Analytics. Nesse espaço de trabalho, você pode executar consultas complexas em relação às informações para solução de problemas e análise. Para obter mais informações, consulte [coletar logs de recursos do Azure em um espaço de trabalho log Analytics no Azure monitor](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace).

## <a name="limitations"></a>Limitações
O recurso de exportação de métricas para logs para Azure Load Balancer tem as seguintes limitações:
* As métricas são exibidas atualmente por meio de nomes internos quando exportadas como logs. Você pode encontrar o mapeamento na tabela abaixo.
* A dimensionalidade das métricas não é preservada. Por exemplo, com métricas como **DipAvailability** (status de investigação de integridade), você não poderá dividir ou exibir por endereço IP de back-end.
* As métricas para portas de SNAT usadas e portas SNAT alocadas não estão disponíveis no momento para exportação como logs.

## <a name="next-steps"></a>Próximas etapas
* [Examinar as métricas disponíveis para o balanceador de carga](./load-balancer-standard-diagnostics.md)
* [Criar e testar consultas seguindo as instruções de Azure Monitor](../azure-monitor/log-query/log-query-overview.md)