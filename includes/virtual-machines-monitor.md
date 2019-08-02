---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 01/27/2019
ms.author: cynthn
ms.openlocfilehash: ac400c86af8236ff5d67b8b6fbf99f6f4b1d36c9
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171895"
---
Você pode tirar proveito das várias oportunidades de monitorar suas VMs coletando, exibindo e analisando o diagnóstico e os dados de log. Para fazer um simples [monitoramento](../articles/azure-monitor/overview.md) da VM, você pode usar a tela Visão geral da VM no portal do Azure. Você pode usar [extensões](../articles/virtual-machines/windows/extensions-features.md) para configurar o diagnóstico em suas VMs a fim de coletar dados de métrica adicionais. Você também pode usar opções de monitoramentos mais avançadas, como o [Application Insights](../articles/azure-monitor/app/app-insights-overview.md) e o [Log Analytics](../articles/azure-monitor/log-query/log-query-overview.md).

## <a name="diagnostics-and-metrics"></a>Diagnóstico e métrica 

Você pode configurar e monitorar a coleta de [dados de diagnóstico](https://docs.microsoft.com/cli/azure/vm/diagnostics) usando [métricas](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md) no portal do Azure, a CLI do Azure, o Azure PowerShell e programando APIs (Interfaces de Programação de Aplicativos). Por exemplo, você pode:

- **Observar as métricas básicas para a VM.** Na tela Visão geral no portal do Azure, as métricas básicas mostradas incluem uso da CPU, uso de rede, total de bytes de disco e operações de disco por segundo.

- **Habilitar a coleta de diagnóstico de inicialização e exibi-lo usando o portal do Azure.** Ao colocar sua própria imagem no Azure ou iniciar uma das imagens da plataforma, muitas razões podem contribuir para que uma VM fique em um estado não inicializável. Você pode habilitar o diagnóstico de inicialização facilmente quando cria uma máquina virtual clicando em **Habilitado** no Diagnóstico de Inicialização na seção Monitoramento da tela Configurações.

    Durante a inicialização das VMs, o agente de diagnóstico de inicialização captura a saída de inicialização e a armazena no Armazenamento do Azure. Esses dados podem ser usados para solucionar problemas de inicialização da VM. Os diagnósticos de inicialização não são habilitados automaticamente quando você cria uma VM nas ferramentas de linha de comando. Antes de habilitar o diagnóstico de inicialização, é necessário criar uma conta de armazenamento para armazenar os logs de inicialização. Se você habilitar o diagnóstico de inicialização no portal do Azure, uma conta de armazenamento será criada automaticamente.

    Se você não habilitar o diagnóstico de inicialização quando a VM for criada, poderá habilitá-la mais tarde usando a [CLI do Azure](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics), o [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.compute/set-azvmbootdiagnostic) ou um [modelo do Azure Resource Manager](../articles/virtual-machines/windows/extensions-diagnostics-template.md).

- **Habilitar a coleta de dados de diagnóstico do sistema operacional convidado.** Quando você cria uma máquina virtual, tem a oportunidade de habilitar o diagnóstico do sistema operacional convidado na tela de configurações. Quando você habilita a coleta de dados de diagnóstico, a [extensão IaaSDiagnostics para Linux](../articles/virtual-machines/linux/diagnostic-extension.md) ou a [extensão IaSDiagnostics para Windows](../articles/virtual-machines/windows/ps-extensions-diagnostics.md) é adicionada à VM, o que permite que você colete dados de disco, CPU e memória adicionais.

    Usando os dados de diagnóstico coletados, você pode configurar o dimensionamento automático das VMs. Você também pode configurar logs para armazenar os dados e configurar alertas para que avisem quando o desempenho não está funcionando adequadamente.

## <a name="alerts"></a>Alertas

Você pode criar [alertas](../articles/azure-monitor/platform/alerts-overview.md) com base em métricas de desempenho específicas. Exemplos dos problemas que podem ser avisados incluem quando o uso médio da CPU excede determinado limite ou o espaço em disco livre disponível fica abaixo de determinado valor. Os alertas podem ser configurados no [portal do Azure](../articles/azure-monitor/platform/alerts-classic-portal.md)usando o [Azure PowerShell](../articles/azure-monitor/platform/alerts-classic-portal.md#with-powershell) ou a [CLI do Azure](../articles/azure-monitor/platform/alerts-classic-portal.md#with-azure-cli).

## <a name="azure-service-health"></a>Integridade do Serviço do Azure

A [Integridade de Serviço do Azure](../articles/service-health/service-health-overview.md) dá suporte e diretrizes personalizadas em caso de problemas nos serviços do Azure, e ajuda você a se preparar para manutenção futura planejada. A Integridade de Serviço do Azure alerta você e suas equipes usando notificações flexíveis e direcionadas.

## <a name="azure-resource-health"></a>Azure Resource Health

O [Azure Resource Health](../articles/service-health/resource-health-overview.md) ajuda você a diagnosticar e a obter suporte quando um problema com o Azure afeta seus recursos. Ele informa sobre a integridade atual e anterior de seus recursos e ajuda a reduzir os problemas. O Resource Health fornece suporte técnico quando você precisa de ajuda com problemas de serviço do Azure.

## <a name="azure-activity-log"></a>Log de Atividades do Azure

O [Log de Atividades do Azure](../articles/azure-monitor/platform/activity-logs-overview.md) é um log de assinatura que fornece informações sobre eventos no nível da assinatura que ocorreram no Azure. O log inclui um intervalo de dados, de dados operacionais do Azure Resource Manager para atualizações em eventos de Integridade do Serviço. Você pode clicar em Log de Atividades no portal do Azure para exibir o log da sua VM.

Algumas coisas que você pode fazer com o log de atividades incluem:

- Criar um [alerta em um evento do Log de Atividades](../articles/azure-monitor/platform/activity-logs-overview.md).
- [Transmiti-lo para um Hub de Eventos](../articles/azure-monitor/platform/activity-logs-stream-event-hubs.md) para ingestão por um serviço de terceiros ou uma solução de análise personalizada, como o Power BI.
- Analisá-lo no PowerBI usando o [Pacote de conteúdo do PowerBI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).
- [Salvá-lo em uma conta de armazenamento](../articles/azure-monitor/platform/archive-activity-log.md) para inspeção manual ou arquivamento. Você pode especificar o tempo de retenção (em dias) usando o Perfil de Log.

Você também pode acessar os dados de log de atividade usando o [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.insights/), a [CLI do Azure](https://docs.microsoft.com/cli/azure/monitor) ou as [APIs REST do Monitor](https://docs.microsoft.com/rest/api/monitor/).

Os [Logs de diagnóstico do Azure](../articles/azure-monitor/platform/diagnostic-logs-overview.md) são logs emitidos pela sua VM que fornecem dados avançados e frequentes sobre sua operação. Os logs de diagnóstico diferem dos logs de atividade, pois fornecem informações sobre operações que foram executadas dentro da VM.

Algumas coisas que você pode fazer com os logs de diagnóstico incluem:

- [Salve-os em uma conta de armazenamento](../articles/azure-monitor/platform/archive-diagnostic-logs.md) para auditoria ou inspeção manual. Você pode especificar o tempo de retenção (em dias) usando as Configurações de Diagnóstico do Recurso.
- [Transmita-os para os Hubs de Eventos](../articles/azure-monitor/platform/diagnostic-logs-stream-event-hubs.md) para consumo por um serviço de terceiros ou uma solução de análises personalizadas, como o PowerBI.
- Analise-os com o [Log Analytics](../articles/log-analytics/log-analytics-azure-storage.md).

## <a name="advanced-monitoring"></a>Monitoramento avançado

- O [Azure Monitor](../articles/azure-monitor/overview.md) é um serviço que monitora os ambientes locais e na nuvem para manter a disponibilidade e o desempenho deles. Ele fornece uma solução abrangente para coleta, análise e ação com base na telemetria em seus ambientes de nuvem e locais. Ele ajuda a entender o desempenho de seus aplicativos, além de identificar de maneira proativa os problemas que os estão afetando e os recursos dos quais eles dependem. Você pode instalar uma extensão em uma [VM Linux](../articles/virtual-machines/linux/extensions-oms.md) ou [VM Windows](../articles/virtual-machines/windows/extensions-oms.md) que instale o agente do Log Analytics para coletar dados de log e armazenar em um espaço de trabalho do Log Analytics.

    Para VMs Windows e Linux, o método recomendado para coletar logs é a instalação do agente do Log Analytics. A maneira mais fácil de instalar o agente do Log Analytics em uma VM é por meio da [Extensão de VM do Log Analytics](../articles/log-analytics/log-analytics-azure-vm-extension.md). Usar a extensão simplifica o processo de instalação e configura automaticamente o agente para enviar dados para o espaço de trabalho do Log Analytics que você especificar. O agente também será automaticamente atualizado, garantindo que você disponha dos recursos e correções mais recentes.

- O [Observador de Rede](../articles/network-watcher/network-watcher-monitoring-overview.md) permite que você monitore sua VM e seus recursos associados de acordo com o relacionamento deles com a rede em que estão. Você pode instalar a extensão Agente do Observador de rede em uma [VM Linux](../articles/virtual-machines/linux/extensions-nwa.md) ou [VM Windows](../articles/virtual-machines/windows/extensions-nwa.md).

- O [Azure Monitor para VMs](../articles/azure-monitor/insights/vminsights-overview.md) monitora suas VMs (máquina virtuais) do Azure em escala analisando o desempenho e a integridade das VMs Windows e Linux, incluindo seus diferentes processos e dependências interconectados a outros recursos e processos externos. 

## <a name="next-steps"></a>Próximas etapas
- Siga as etapas em [Monitorar uma Máquina Virtual Windows com o Azure PowerShell](../articles/virtual-machines/windows/tutorial-monitoring.md) ou [Monitorar uma Máquina Virtual Linux com a CLI do Azure](../articles/virtual-machines/linux/tutorial-monitoring.md).
- Saiba mais sobre as melhores práticas de [Monitoramento e Diagnóstico](https://docs.microsoft.com/azure/architecture/best-practices/monitoring).
