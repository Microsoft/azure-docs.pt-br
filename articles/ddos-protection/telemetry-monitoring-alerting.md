---
title: Exibir e configurar a telemetria da Proteção contra DDoS
description: Saiba como exibir e configurar a telemetria de proteção contra DDoS.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 834339a20e369b3835faf05d069f8d4f77385e18
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2020
ms.locfileid: "96744852"
---
# <a name="view-and-configure-ddos-protection-telemetry"></a>Exibir e configurar a telemetria da Proteção contra DDoS

O padrão de Proteção contra DDoS do Azure fornece informações detalhadas de ataque e visualização com Análise de Ataque de DDoS. Os clientes que protegem suas redes virtuais contra ataques de DDoS têm visibilidade detalhada sobre o tráfego de ataque e as ações tomadas para reduzir o ataque por meio de relatórios de mitigação de ataque e logs de fluxo de mitigação. A telemetria avançada é exposta por meio de Azure Monitor incluindo métricas detalhadas durante a duração de um ataque de DDoS. Alertas podem ser configurados para qualquer métrica do Azure Monitor exposta pela Proteção contra DDoS. O registro em log pode ser integrado com o [Azure Sentinel](../sentinel/connect-azure-ddos-protection.md), Splunk (hubs de eventos do Azure), OMS log Analytics e armazenamento do Azure para análise avançada por meio da interface de diagnóstico de Azure monitor.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configurar alertas para métricas de proteção contra DDoS
> * Usar telemetria da proteção contra DDoS
> * Exibir políticas de mitigação de DDoS
> * Exibir alertas de proteção contra DDoS na central de segurança do Azure

## <a name="prerequisites"></a>Pré-requisitos

- Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- Antes de concluir as etapas deste tutorial, você deve primeiro criar um plano de [proteção padrão do DDoS do Azure](manage-ddos-protection.md).

## <a name="configure-alerts-for-ddos-protection-metrics"></a>Configurar alertas para métricas de proteção contra DDoS

Selecione uma das métricas de proteção contra DDoS disponíveis para alertá-lo quando houver uma mitigação ativa durante um ataque usando a configuração de alerta do Azure Monitor. Quando as condições forem atendidas, o endereço especificado receberá um email de alerta:

1. Selecione **Todos os serviços** na parte superior esquerda do portal.
2. Digite *Monitor* na caixa **Filtro**. Selecione **Monitorar** quando aparecer nos resultados.
3. Selecione **Métricas** em **Serviços Compartilhados**.
4. Insira ou selecione seus próprios valores, ou insira os valores de exemplo a seguir, aceite os padrões restantes e, em seguida, selecione **Ok**:

    |Configuração                  |Valor                                                                                               |
    |---------                |---------                                                                                           |
    |Nome                     | Insira _MyDdosAlert_.                                                                                |
    |Subscription             | Selecione a assinatura que contém o endereço IP público para o qual você deseja receber alertas.        |
    |Resource group           | Selecione o grupo de recursos que contém o endereço IP público para o qual você deseja receber alertas.      |
    |Recurso                 | Selecione o endereço IP público que contém o endereço IP público para o qual você deseja receber alertas. DDoS monitora os endereços IP públicos atribuídos aos recursos em uma rede virtual. Se você não tiver todos os recursos com endereços IP públicos na rede virtual, você deve primeiro criar um recurso com um endereço IP público. Você pode monitorar o endereço IP público de todos os recursos implantados por meio do Resource Manager (não clássico) listados em [rede virtual para serviços do Azure](../virtual-network/virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) (incluindo balanceadores de carga do Azure onde as máquinas virtuais de back-end estão na rede virtual), exceto para ambientes de serviço Azure app e gateway de VPN do Azure. Para continuar este tutorial, você pode criar rapidamente uma máquina virtual do [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).                   |
    |Métrica                   | Selecione **sob ataque de DDoS ou não**.                                                                |
    |Limite                | 1 - **1** significa que você está sob ataque. **0** significa que você não está sob ataque.                         |
    |Período                   | Selecione o valor que você escolher.                                                                   |
    |Notificar por email         | Marque a caixa de seleção.                                                                                 |
    |Administrador adicional | Insira seu endereço de email se você não é um proprietário de email, colaborador ou leitor para a assinatura. |

    Em poucos minutos de detecção de ataque, você receberá um email de métricas de Azure Monitor que é semelhante a figura a seguir:

    ![Alerta de ataque](./media/manage-ddos-protection/ddos-alert.png)


Para simular um ataque de DDoS para validar seu alerta, consulte [Validar detecção de DDoS](test-through-simulations.md).

Você também pode aprender mais sobre [configuração de webhooks](../azure-monitor/platform/alerts-webhooks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [aplicativos lógicos](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para criação de alertas.

## <a name="use-ddos-protection-telemetry"></a>Usar telemetria da proteção contra DDoS

A telemetria de um ataque é fornecida por meio do Azure Monitor em tempo real. A telemetria está disponível somente durante o tempo pelo qual um endereço IP público está sob mitigação. Você não vê a telemetria antes nem depois de um ataque ser mitigado.

1. Selecione **Todos os serviços** na parte superior esquerda do portal.
2. Digite *Monitor* na caixa **Filtro**. Selecione **Monitorar** quando aparecer nos resultados.
3. Selecione **métricas**, em **serviços compartilhados**.
4. Selecione a **Assinatura** e o **Grupo de recursos** que contêm o endereço IP público para o qual você deseja a telemetria.
5. Selecione **Endereço IP público** para **Tipo de recurso**, em seguida, selecione o endereço IP público específico para o qual você deseja a telemetria.
6. Uma série de **Métricas Disponíveis** aparecem no lado esquerdo da tela. Quando selecionadas, essas métricas são mostradas no **Gráfico de Métricas do Azure Monitor** na tela de visão geral.
7. Selecione o tipo de **agregação** como **máximo**

Os nomes de métrica apresentam diferentes tipos de pacotes e bytes versus pacotes, com um constructo básico de nomes de marcação em cada métrica, da seguinte maneira:

- **Nome de marcação removido** (por exemplo, **Pacotes de Entrada Removidos por DDoS**): o número de pacotes removidos pelo sistema de proteção contra DDoS.
- **Nome de marcação encaminhado** (por exemplo, **Pacotes de Entrada Encaminhados por DDoS**): o número de pacotes encaminhados pelo sistema de DDoS para o VIP de destino – tráfego que não foi filtrado.
- **Nenhum nome de marca** (por exemplo: **Pacotes de Entrada de DDoS**): o número total de pacotes que entraram no sistema de depuração – que representa a soma dos pacotes ignorados e encaminhados.

Essa [Azure monitor regra de alerta](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/Azure%20Monitor%20Alert%20-%20DDoS%20Mitigation%20Started) executará uma consulta simples para detectar quando uma mitigação de DDoS ativa está ocorrendo. Para simular um ataque de DDoS para validar a telemetria, consulte [Validar detecção de DDoS](test-through-simulations.md). 

## <a name="view-ddos-mitigation-policies"></a>Exibir políticas de mitigação de DDoS

A Proteção contra DDoS padrão aplica TCP SYN, TCP e UDP (três políticas de mitigação ajustadas automaticamente) para cada endereço IP público do recurso protegido, na rede virtual que tem o DDoS habilitado. Você pode exibir os limites da política selecionando os  **pacotes TCP de entrada para disparar a mitigação de DDoS** e **pacotes UDP de entrada para disparar** métricas de mitigação de DDoS com o tipo de **agregação** como ' Max ', conforme mostrado na figura a seguir:

![Exibir políticas de mitigação](./media/manage-ddos-protection/view-mitigation-policies.png)

Os limites da política são configurados automaticamente por meio da nossa criação de perfil de tráfego de rede baseada em aprendizado de máquina do Azure. Somente quando o limite da política for excedido, ocorre a mitigação de DDoS para o endereço IP sob ataque.

## <a name="view-ddos-protection-alerts-in-azure-security-center"></a>Exibir alertas de proteção contra DDoS na central de segurança do Azure

A central de segurança do Azure fornece uma lista de [alertas de segurança](../security-center/security-center-managing-and-responding-alerts.md), com informações para ajudar a investigar e corrigir problemas. Com esse recurso, você obtém uma exibição unificada de alertas, incluindo alertas relacionados a ataques de DDoS e as ações tomadas para mitigar o ataque em tempo quase futuro.
Há dois alertas específicos que você verá para qualquer detecção e mitigação de ataque de DDoS:

- **Ataque de DDoS detectado para IP público**: esse alerta é gerado quando o serviço de proteção contra DDoS detecta que um de seus endereços IP públicos é o destino de um ataque de DDoS.
- **Ataque de DDoS mitigado para IP público**: esse alerta é gerado quando um ataque no endereço IP público é mitigado.
Para exibir os alertas, abra a **central de segurança** no portal do Azure. Em **proteção contra ameaças**, selecione **alertas de segurança**. A captura de tela a seguir mostra um exemplo dos alertas de ataque de DDoS.

![Alerta de DDoS na central de segurança do Azure](./media/manage-ddos-protection/ddos-alert-asc.png)

Os alertas incluem informações gerais sobre o endereço IP público que está sob ataque, informações de inteligência geográfica e contra ameaças e etapas de correção.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

- Configurar alertas para métricas de proteção contra DDoS
- Usar telemetria da proteção contra DDoS
- Exibir políticas de mitigação de DDoS
- Exibir alertas de proteção contra DDoS na central de segurança do Azure

Para saber como configurar relatórios de mitigação de ataque e logs de fluxo, prossiga para o próximo tutorial.

> [!div class="nextstepaction"]
> [Configurar relatórios de mitigação de ataque de DDoS e logs de fluxo](reports-and-flow-logs.md)