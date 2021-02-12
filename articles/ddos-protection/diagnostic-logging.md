---
title: Relatórios padrão da proteção contra DDoS do Azure e logs de fluxo
description: Saiba como configurar relatórios e logs de fluxo.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/28/2020
ms.author: yitoh
ms.openlocfilehash: 42d4467a6441e517ef3194f701eb47e7e68505b1
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2021
ms.locfileid: "99806300"
---
# <a name="view-and-configure-ddos-diagnostic-logging"></a>Exibir e configurar o log de diagnóstico de DDoS

O padrão de Proteção contra DDoS do Azure fornece informações detalhadas de ataque e visualização com Análise de Ataque de DDoS. Os clientes que protegem suas redes virtuais contra ataques de DDoS têm visibilidade detalhada sobre o tráfego de ataque e as ações tomadas para reduzir o ataque por meio de relatórios de mitigação de ataque e logs de fluxo de mitigação. A telemetria avançada é exposta por meio de Azure Monitor incluindo métricas detalhadas durante a duração de um ataque de DDoS. Alertas podem ser configurados para qualquer métrica do Azure Monitor exposta pela Proteção contra DDoS. O registro em log pode ser integrado com o [Azure Sentinel](../sentinel/connect-azure-ddos-protection.md), Splunk (hubs de eventos do Azure), OMS log Analytics e armazenamento do Azure para análise avançada por meio da interface de diagnóstico de Azure monitor.

Os seguintes logs de diagnóstico estão disponíveis para a proteção contra DDoS do Azure Standard: 

- **DDoSProtectionNotifications**: as notificações notificarão você sempre que um recurso de IP público estiver sob ataque e quando a mitigação de ataque terminar.
- **DDoSMitigationFlowLogs**: os logs de fluxo de mitigação de ataque permitem que você examine o tráfego descartado, o tráfego encaminhado e outros pontos de extremidade interessantes durante um ataque de DDoS ativo quase em tempo real. Você pode ingerir o fluxo constante desses dados no Azure Sentinel ou em seus sistemas SIEM de terceiros por meio do hub de eventos para monitoramento quase em tempo real, tomar ações em potencial e atender à necessidade de suas operações de defesa.
- **DDoSMitigationReports**: os relatórios de mitigação de ataques usam os dados do protocolo NetFlow que são agregados para fornecer informações detalhadas sobre o ataque em seu recurso. Sempre que um recurso IP público estiver sob ataque, a geração de relatórios começará assim que a atenuação for iniciada. Também será gerado um relatório de incremental a cada 5 minutos e um relatório de pós-atenuação para todo o período de mitigação. Isso é para garantir que, no caso de o ataque de DDoS continuar por mais tempo, você poderá exibir o instantâneo mais recentes do relatório de atenuação a cada 5 minutos e um resumo completo quando a mitigação de ataque estiver concluída. 
- **Biometria: fornece** todas as métricas possíveis disponíveis durante a duração de um ataque de DDoS. 

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configure os logs de diagnóstico de DDoS, incluindo notificações, relatórios de mitigação e logs de fluxo de mitigação. 
> * Habilite o log de diagnóstico em todos os IPs públicos em um escopo definido.
> * Exibir dados de log em pastas de trabalho.

## <a name="prerequisites"></a>Pré-requisitos

- Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- Antes de concluir as etapas deste tutorial, você deve primeiro criar um plano de [proteção padrão do DDoS do Azure](manage-ddos-protection.md) e a proteção contra DDoS Standard deve ser habilitada em uma rede virtual.
- DDoS monitora os endereços IP públicos atribuídos aos recursos em uma rede virtual. Se você não tiver todos os recursos com endereços IP públicos na rede virtual, você deve primeiro criar um recurso com um endereço IP público. Você pode monitorar o endereço IP público de todos os recursos implantados por meio do Resource Manager (não clássico) listado em [rede virtual para serviços do Azure](../virtual-network/virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) (incluindo balanceadores de carga do Azure onde as máquinas virtuais de back-end estão na rede virtual), exceto para ambientes de serviço Azure app. Para continuar este tutorial, você pode criar rapidamente uma máquina virtual do [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).    

## <a name="configure-ddos-diagnostic-logs"></a>Configurar logs de diagnóstico de DDoS

Se você quiser habilitar automaticamente o log de diagnóstico em todos os IPs públicos em um ambiente, pule para [habilitar o log de diagnóstico em todos os IPs públicos](#enable-diagnostic-logging-on-all-public-ips).

1. Selecione **Todos os serviços** na parte superior esquerda do portal.
2. Digite *Monitor* na caixa **Filtro**. Selecione **Monitorar** quando aparecer nos resultados.
3. Em **configurações**, selecione **configurações de diagnóstico**.
4. Selecione a **Assinatura** e o **Grupo de recursos** que contêm o endereço IP público que você deseja registrar.
5. Selecione **endereço IP público** para **tipo de recurso** e selecione o endereço IP público específico para o qual você deseja habilitar os logs.
6. Selecione **Adicionar configuração de diagnóstico**. Em **detalhes da categoria**, selecione quantas opções forem necessárias e, em seguida, selecione **salvar**.

    ![Configurações de diagnóstico de DDoS](./media/ddos-attack-telemetry/ddos-diagnostic-settings.png)

7. Em **detalhes de destino**, selecione quantas opções a seguir forem necessárias:

    - **Arquivar em uma conta de armazenamento**: os dados são gravados em uma conta de armazenamento do Azure. Para saber mais sobre essa opção, consulte [arquivar logs de recursos](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage).
    - **Transmitir para um hub de eventos**: permite que um destinatário de log colete os logs usando um Hub de Eventos do Azure. Os Hubs de Eventos habilitam a integração com o Splunk ou outros sistemas SIEM. Para saber mais sobre essa opção, consulte [transmitir logs de recursos para um hub de eventos](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs).
    - **Enviar para log Analytics**: grava logs no serviço Azure monitor. Para saber mais sobre essa opção, consulte [coletar logs para uso em logs de Azure monitor](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-log-analytics-workspace).

### <a name="log-schemas"></a>Esquemas de log

A tabela a seguir lista os nomes e as descrições de campo:

# <a name="ddosprotectionnotifications"></a>[DDoSProtectionNotifications](#tab/DDoSProtectionNotifications)

| Nome do campo | Descrição |
| --- | --- |
| **TimeGenerated** | A data e a hora em UTC em que a notificação foi criada. |
| **ResourceId** | A ID de recurso do seu endereço IP público. |
| **Categoria** | Para notificações, isso será `DDoSProtectionNotifications` .|
| **ResourceGroup** | O grupo de recursos que contém o endereço IP público e a rede virtual. |
| **SubscriptionId** | Sua ID de assinatura do plano de proteção contra DDoS. |
| **Recurso** | O nome do seu endereço IP público. |
| **ResourceType** | Isso sempre será `PUBLICIPADDRESS` . |
| **OperationName** | Para notificações, isso será `DDoSProtectionNotifications` .  |
| **Mensagem** | Detalhes do ataque. |
| **Tipo** | Tipo de notificação. Os valores possíveis incluem `MitigationStarted` . `MitigationStopped`. |
| **PublicIpAddress** | Seu endereço IP público. |

# <a name="ddosmitigationflowlogs"></a>[DDoSMitigationFlowLogs](#tab/DDoSMitigationFlowLogs)

| Nome do campo | Descrição |
| --- | --- |
| **TimeGenerated** | A data e a hora em UTC em que o log de fluxo foi criado. |
| **ResourceId** | A ID de recurso do seu endereço IP público. |
| **Categoria** | Para logs de fluxo, isso será `DDoSMitigationFlowLogs` .|
| **ResourceGroup** | O grupo de recursos que contém o endereço IP público e a rede virtual. |
| **SubscriptionId** | Sua ID de assinatura do plano de proteção contra DDoS. |
| **Recurso** | O nome do seu endereço IP público. |
| **ResourceType** | Isso sempre será `PUBLICIPADDRESS` . |
| **OperationName** | Para logs de fluxo, isso será `DDoSMitigationFlowLogs` . |
| **Mensagem** | Detalhes do ataque. |
| **SourcePublicIpAddress** | O endereço IP público do cliente que gera o tráfego para seu endereço IP público. |
| **SourcePort** | Número da porta que varia de 0 a 65535. |
| **DestPublicIpAddress** | Seu endereço IP público. |
| **DestPort** | Número da porta que varia de 0 a 65535. |
| **Protocolo** | Tipo de protocolo. Os valores possíveis incluem `tcp` , `udp` , `other` .|

# <a name="ddosmitigationreports"></a>[DDoSMitigationReports](#tab/DDoSMitigationReports)

| Nome do campo | Descrição |
| --- | --- |
| **TimeGenerated** | A data e a hora em UTC em que o relatório foi criado. |
| **ResourceId** | A ID de recurso do seu endereço IP público. |
| **Categoria** | Para notificações, isso será `DDoSProtectionNotifications` .|
| **ResourceGroup** | O grupo de recursos que contém o endereço IP público e a rede virtual. |
| **SubscriptionId** | Sua ID de assinatura do plano de proteção contra DDoS. |
| **Recurso** | O nome do seu endereço IP público. |
| **ResourceType** | Isso sempre será `PUBLICIPADDRESS` . |
| **OperationName** | Para relatórios de mitigação, isso será `DDoSMitigationReports` . |
| **ReportType** | Os valores possíveis incluem `Incremental` , `PostMitigation` .|
| **MitigationPeriodStart** | A data e a hora em UTC em que a mitigação foi iniciada.  |
| **MitigationPeriodEnd** | A data e a hora em UTC em que a mitigação terminou. |
| **EndereçoIP** | Seu endereço IP público. |
| **AttackVectors** |  Divisão de tipos de ataque. As chaves incluem `TCP SYN flood` , `TCP flood` ,, `UDP flood` `UDP reflection` , `Other packet flood` .|
| **TrafficOverview** |  Divisão do tráfego de ataque. As chaves incluem,,,,,, `Total packets` `Total packets dropped` `Total TCP packets` `Total TCP packets dropped` `Total UDP packets` `Total UDP packets dropped` `Total Other packets` , `Total Other packets dropped` . |
| **Protocolos** | Divisão de protocolos envolvidos. As chaves incluem `TCP` , `UDP` , `Other` . |
| **DropReasons** | Análise de motivos para pacotes descartados. As chaves incluem,,,,,,,, `Protocol violation invalid TCP syn` `Protocol violation invalid TCP` `Protocol violation invalid UDP` `UDP reflection` `TCP rate limit exceeded` `UDP rate limit exceeded` `Destination limit exceeded` `Other packet flood` `Rate limit exceeded` , `Packet was forwarded to service` . |
| **TopSourceCountries** | Divisão dos 10 principais países de origem do tráfego de entrada. |
| **TopSourceCountriesForDroppedPackets** | Divisão dos 10 principais países de origem do tráfego de ataque que é/foi atenuado. |
| **TopSourceASNs** | Divisão dos 10 principais números do sistema autônomo de origem (ASN) do tráfego de entrada.  |
| **SourceContinents** | Divisão dos continentes de origem do tráfego de entrada. |
***

## <a name="enable-diagnostic-logging-on-all-public-ips"></a>Habilitar o log de diagnóstico em todos os IPs públicos

Este [modelo](https://aka.ms/ddosdiaglogs) cria uma definição de Azure Policy para habilitar automaticamente o log de diagnóstico em todos os logs de IP público em um escopo definido.

[![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%2520DDoS%2520Protection%2FEnable%2520Diagnostic%2520Logging%2FAzure%2520Policy%2FDDoSLogs.json)

## <a name="view-log-data-in-workbooks"></a>Exibir dados de log em pastas de trabalho

### <a name="azure-sentinel-data-connector"></a>Conector de dados do Azure Sentinel

Você pode conectar os logs ao Azure Sentinel, exibir e analisar seus dados em pastas de trabalho, criar alertas personalizados e incorporá-los em processos de investigação. Para se conectar ao Azure Sentinel, confira [conectar-se ao Azure Sentinel](../sentinel/connect-azure-ddos-protection.md). 

![Conector de DDoS do Azure Sentinel](./media/ddos-attack-telemetry/azure-sentinel-ddos.png)

### <a name="azure-ddos-protection-workbook"></a>Pasta de trabalho de proteção contra DDoS do Azure

Você pode usar [este modelo de Azure Resource Manager (ARM)](https://aka.ms/ddosworkbook) para implantar uma pasta de trabalho do Attack Analytics. Esta pasta de trabalho permite que você visualize dados de ataque em vários painéis filtráveis para entender facilmente o que está em jogo. 

[![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%2520DDoS%2520Protection%2FAzure%2520DDoS%2520Protection%2520Workbook%2FAzureDDoSWorkbook_ARM.json)

![Pasta de trabalho proteção contra DDoS](./media/ddos-attack-telemetry/ddos-attack-analytics-workbook.png)

## <a name="validate-and-test"></a>Validar e testar

Para simular um ataque de DDoS para validar seus logs, consulte [validar detecção de DDoS](test-through-simulations.md).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

- Configure os logs de diagnóstico de DDoS, incluindo notificações, relatórios de mitigação e logs de fluxo de mitigação. 
- Habilite o log de diagnóstico em todos os IPs públicos em um escopo definido.
- Exibir dados de log em pastas de trabalho.

Para saber como configurar alertas de ataque, prossiga para o próximo tutorial.

> [!div class="nextstepaction"]
> [Exibir e configurar os alertas da proteção contra DDoS](alerts.md)
