---
title: Exibir e configurar a telemetria de proteção contra DDoS para a proteção contra DDoS do Azure Standard
description: Saiba como exibir e configurar a telemetria de proteção contra DDoS para a proteção contra DDoS do Azure Standard.
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
ms.openlocfilehash: 0a8c30076231aecb17505dd0d7a2fe4e7be485a3
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/15/2021
ms.locfileid: "100522658"
---
# <a name="view-and-configure-ddos-protection-telemetry"></a>Exibir e configurar a telemetria da Proteção contra DDoS

O padrão de Proteção contra DDoS do Azure fornece informações detalhadas de ataque e visualização com Análise de Ataque de DDoS. Os clientes que protegem suas redes virtuais contra ataques de DDoS têm visibilidade detalhada sobre o tráfego de ataque e as ações tomadas para reduzir o ataque por meio de relatórios de mitigação de ataque e logs de fluxo de mitigação. A telemetria avançada é exposta por meio de Azure Monitor incluindo métricas detalhadas durante a duração de um ataque de DDoS. Alertas podem ser configurados para qualquer métrica do Azure Monitor exposta pela Proteção contra DDoS. O registro em log pode ser integrado com o [Azure Sentinel](../sentinel/connect-azure-ddos-protection.md), Splunk (hubs de eventos do Azure), OMS log Analytics e armazenamento do Azure para análise avançada por meio da interface de diagnóstico de Azure monitor.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Exibir telemetria de proteção contra DDoS
> * Exibir políticas de mitigação de DDoS
> * Validar e testar a telemetria de proteção contra DDoS

### <a name="metrics"></a>Métricas

> [!NOTE]
> Enquanto várias opções de **agregação** são exibidas em portal do Azure, somente os tipos de agregação listados na tabela abaixo têm suporte para cada métrica. Pedimos desculpas por essa confusão e estamos trabalhando para resolvê-la.

As [métricas](../azure-monitor/platform/metrics-supported.md#microsoftnetworkpublicipaddresses) a seguir estão disponíveis para a proteção contra DDoS do Azure Standard. Essas métricas também são exportáveis por meio de configurações de diagnóstico (consulte [Exibir e configurar o log de diagnóstico de DDoS](diagnostic-logging.md)).


| Métrica | Nome de exibição da métrica | Unidade | Tipo de agregação | Descrição |
| --- | --- | --- | --- | --- |
| BytesDroppedDDoS | DDoS de bytes de entrada removidos | BytesPerSecond | Máximo | DDoS de bytes de entrada removidos| 
| BytesForwardedDDoS | DDoS de bytes de entrada encaminhados | BytesPerSecond | Máximo | DDoS de bytes de entrada encaminhados |
| BytesInDDoS | DDoS de bytes de entrada | BytesPerSecond | Máximo | DDoS de bytes de entrada |
| DDoSTriggerSYNPackets | Pacotes de entrada SYN para disparar a mitigação de DDoS | CountPerSecond | Máximo | Pacotes de entrada SYN para disparar a mitigação de DDoS |
| DDoSTriggerTCPPackets | Pacotes TCP de entrada a disparar a mitigação de DDoS | CountPerSecond | Máximo | Pacotes TCP de entrada a disparar a mitigação de DDoS |
| DDoSTriggerUDPPackets | Pacotes UDP de entrada a disparar a mitigação de DDoS | CountPerSecond | Máximo | Pacotes UDP de entrada a disparar a mitigação de DDoS |
| IfUnderDDoSAttack | Sob ataque DDoS ou não | Contagem | Máximo | Sob ataque DDoS ou não |
| PacketsDroppedDDoS | DDoS de pacotes de entrada removidos | CountPerSecond | Máximo | DDoS de pacotes de entrada removidos |
| PacketsForwardedDDoS | DDoS de pacotes de entrada encaminhados | CountPerSecond | Máximo | DDoS de pacotes de entrada encaminhados |
| PacketsInDDoS | DDoS de pacotes de entrada | CountPerSecond | Máximo | DDoS de pacotes de entrada |
| TCPBytesDroppedDDoS | DDoS de bytes de TCP de entrada removidos | BytesPerSecond | Máximo | DDoS de bytes de TCP de entrada removidos |
| TCPBytesForwardedDDoS | DDoS de bytes de TCP de entrada encaminhados | BytesPerSecond | Máximo | DDoS de bytes de TCP de entrada encaminhados |
| TCPBytesInDDoS | DDoS de bytes de TCP de entrada | BytesPerSecond | Máximo | DDoS de bytes de TCP de entrada |
| TCPPacketsDroppedDDoS | DDoS de pacotes TCP de entrada removidos | CountPerSecond | Máximo | DDoS de pacotes TCP de entrada removidos |
| TCPPacketsForwardedDDoS | DDoS de pacotes TCP de entrada encaminhados | CountPerSecond | Máximo | DDoS de pacotes TCP de entrada encaminhados |
| TCPPacketsInDDoS | DDoS de pacotes TCP de entrada | CountPerSecond | Máximo | DDoS de pacotes TCP de entrada |
| UDPBytesDroppedDDoS | DDoS de bytes de UDP de entrada removidos | BytesPerSecond | Máximo | DDoS de bytes de UDP de entrada removidos |
| UDPBytesForwardedDDoS | DDoS de bytes de UDP de entrada encaminhados | BytesPerSecond | Máximo | DDoS de bytes de UDP de entrada encaminhados |
| UDPBytesInDDoS | DDoS de bytes de UDP de entrada | BytesPerSecond | Máximo | DDoS de bytes de UDP de entrada |
| UDPPacketsDroppedDDoS | DDoS de pacotes UDP de entrada removidos | CountPerSecond | Máximo | DDoS de pacotes UDP de entrada removidos |
| UDPPacketsForwardedDDoS | DDoS de pacotes UDP de entrada encaminhados | CountPerSecond | Máximo | DDoS de pacotes UDP de entrada encaminhados |
| UDPPacketsInDDoS | DDoS de pacotes UDP de entrada | CountPerSecond | Máximo | DDoS de pacotes UDP de entrada |

## <a name="prerequisites"></a>Pré-requisitos

- Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- Antes de concluir as etapas deste tutorial, você deve primeiro criar um plano de [proteção padrão do DDoS do Azure](manage-ddos-protection.md) e a proteção contra DDoS Standard deve ser habilitada em uma rede virtual.
- DDoS monitora os endereços IP públicos atribuídos aos recursos em uma rede virtual. Se você não tiver todos os recursos com endereços IP públicos na rede virtual, você deve primeiro criar um recurso com um endereço IP público. Você pode monitorar o endereço IP público de todos os recursos implantados por meio do Resource Manager (não clássico) listado em [rede virtual para serviços do Azure](../virtual-network/virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) (incluindo balanceadores de carga do Azure onde as máquinas virtuais de back-end estão na rede virtual), exceto para ambientes de serviço Azure app. Para continuar este tutorial, você pode criar rapidamente uma máquina virtual do [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="view-ddos-protection-telemetry"></a>Exibir telemetria de proteção contra DDoS

A telemetria de um ataque é fornecida por meio do Azure Monitor em tempo real. A telemetria está disponível somente quando um endereço IP público está sob mitigação. 

1. Entre no [portal do Azure](https://portal.azure.com/) e navegue até o plano de proteção contra DDoS.
2. Em **Monitoramento**, selecione **Métricas**.
3. Selecione **Escopo**. Selecione a **assinatura** que contém o endereço IP público que você deseja registrar, selecione **endereço IP público** para **tipo de recurso**, selecione o endereço IP público específico para o qual você deseja registrar métricas e, em seguida, selecione **aplicar**.
4. Selecione o tipo de **agregação** como **máximo**.

Os nomes de métrica apresentam diferentes tipos de pacotes e bytes versus pacotes, com um constructo básico de nomes de marcação em cada métrica, da seguinte maneira:

- **Nome de marcação removido** (por exemplo, **Pacotes de Entrada Removidos por DDoS**): o número de pacotes removidos pelo sistema de proteção contra DDoS.
- **Nome de marcação encaminhado** (por exemplo, **Pacotes de Entrada Encaminhados por DDoS**): o número de pacotes encaminhados pelo sistema de DDoS para o VIP de destino – tráfego que não foi filtrado.
- **Nenhum nome de marca** (por exemplo: **Pacotes de Entrada de DDoS**): o número total de pacotes que entraram no sistema de depuração – que representa a soma dos pacotes ignorados e encaminhados.

## <a name="view-ddos-mitigation-policies"></a>Exibir políticas de mitigação de DDoS

A Proteção contra DDoS padrão aplica TCP SYN, TCP e UDP (três políticas de mitigação ajustadas automaticamente) para cada endereço IP público do recurso protegido, na rede virtual que tem o DDoS habilitado. Você pode exibir os limites da política selecionando os  **pacotes TCP de entrada para disparar a mitigação de DDoS** e **pacotes UDP de entrada para disparar** métricas de mitigação de DDoS com o tipo de **agregação** como ' Max ', conforme mostrado na figura a seguir:

![Exibir políticas de mitigação](./media/manage-ddos-protection/view-mitigation-policies.png)

Os limites da política são configurados automaticamente por meio da nossa criação de perfil de tráfego de rede baseada em aprendizado de máquina do Azure. Somente quando o limite da política for excedido, ocorre a mitigação de DDoS para o endereço IP sob ataque.

## <a name="validate-and-test"></a>Validar e testar

Para simular um ataque de DDoS para validar a telemetria de proteção contra DDoS, consulte [validar detecção de DDoS](test-through-simulations.md).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

- Configurar alertas para métricas de proteção contra DDoS
- Exibir telemetria de proteção contra DDoS
- Exibir políticas de mitigação de DDoS
- Validar e testar a telemetria de proteção contra DDoS

Para saber como configurar relatórios de mitigação de ataque e logs de fluxo, prossiga para o próximo tutorial.

> [!div class="nextstepaction"]
> [Exibir e configurar o log de diagnóstico de DDoS](diagnostic-logging.md)
