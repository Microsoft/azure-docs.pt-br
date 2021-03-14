---
title: Exibir e configurar alertas de proteção contra DDoS para a proteção contra DDoS do Azure Standard
description: Saiba como exibir e configurar alertas de proteção contra DDoS para a proteção contra DDoS do Azure Standard.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/28/2020
ms.author: yitoh
ms.openlocfilehash: a5639d583d9b98f6527e47bf5db213cb191ebeb7
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100575291"
---
# <a name="view-and-configure-ddos-protection-alerts"></a>Exibir e configurar os alertas da proteção contra DDoS

O padrão de Proteção contra DDoS do Azure fornece informações detalhadas de ataque e visualização com Análise de Ataque de DDoS. Os clientes que protegem suas redes virtuais contra ataques de DDoS têm visibilidade detalhada sobre o tráfego de ataque e as ações tomadas para reduzir o ataque por meio de relatórios de mitigação de ataque e logs de fluxo de mitigação. A telemetria avançada é exposta por meio de Azure Monitor incluindo métricas detalhadas durante a duração de um ataque de DDoS. Alertas podem ser configurados para qualquer métrica do Azure Monitor exposta pela Proteção contra DDoS. O registro em log pode ser integrado com o [Azure Sentinel](../sentinel/connect-azure-ddos-protection.md), Splunk (hubs de eventos do Azure), OMS log Analytics e armazenamento do Azure para análise avançada por meio da interface de diagnóstico de Azure monitor.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configurar alertas por meio do Azure Monitor
> * Configurar alertas por meio do portal
> * Exibir alertas na central de segurança do Azure
> * Validar e testar seus alertas

## <a name="prerequisites"></a>Pré-requisitos

- Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- Antes de concluir as etapas deste tutorial, você deve primeiro criar um plano de [proteção padrão do DDoS do Azure](manage-ddos-protection.md) e a proteção contra DDoS Standard deve ser habilitada em uma rede virtual.
- DDoS monitora os endereços IP públicos atribuídos aos recursos em uma rede virtual. Se você não tiver todos os recursos com endereços IP públicos na rede virtual, você deve primeiro criar um recurso com um endereço IP público. Você pode monitorar o endereço IP público de todos os recursos implantados por meio do Resource Manager (não clássico) listado em [rede virtual para serviços do Azure](../virtual-network/virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) (incluindo balanceadores de carga do Azure onde as máquinas virtuais de back-end estão na rede virtual), exceto para ambientes de serviço Azure app. Para continuar este tutorial, você pode criar rapidamente uma máquina virtual do [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).     

## <a name="configure-alerts-through-azure-monitor"></a>Configurar alertas por meio do Azure Monitor

Com esses modelos, você poderá configurar alertas para todos os endereços IP públicos nos quais habilitou o log de diagnóstico. Portanto, para usar esses modelos de alerta, primeiro você precisará de um espaço de trabalho Log Analytics com as configurações de diagnóstico habilitadas. Consulte [Exibir e configurar o log de diagnóstico de DDoS](diagnostic-logging.md).

### <a name="azure-monitor-alert-rule"></a>Azure Monitor regra de alerta

Essa [Azure monitor regra de alerta](https://aka.ms/DDOSmitigationstatus) executará uma consulta simples para detectar quando uma mitigação de DDoS ativa está ocorrendo. Isso indica um ataque potencial. Os grupos de ações podem ser usados para invocar ações como resultado do alerta.

[![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%2520DDoS%2520Protection%2FAzure%2520Monitor%2520Alert%2520-%2520DDoS%2520Mitigation%2520Started%2FDDoSMitigationStarted.json)

### <a name="azure-monitor-alert-rule-with-logic-app"></a>Azure Monitor regra de alerta com o aplicativo lógico

Este [modelo](https://aka.ms/ddosalert) implanta os componentes necessários de um alerta de mitigação de DDoS aprimorado: Azure monitor regra de alerta, grupo de ações e aplicativo lógico. O resultado do processo é um alerta de email com detalhes sobre o endereço IP sob ataque, incluindo informações sobre o recurso associado ao IP. O proprietário do recurso é adicionado como um destinatário do email, junto com a equipe de segurança. Um teste de disponibilidade de aplicativo básico também é executado e os resultados são incluídos no alerta de email.

[![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%2520DDoS%2520Protection%2FDDoS%2520Mitigation%2520Alert%2520Enrichment%2FEnrich-DDoSAlert.json)

## <a name="configure-alerts-through-portal"></a>Configurar alertas por meio do portal

Selecione uma das métricas de proteção contra DDoS disponíveis para alertá-lo quando houver uma mitigação ativa durante um ataque usando a configuração de alerta do Azure Monitor. 

1. Entre no [portal do Azure](https://portal.azure.com/) e navegue até o plano de proteção contra DDoS.
2. Em **Monitoramento**, selecione **Métricas**.
3. Na barra de navegação cinza, selecione **nova regra de alerta**. 
4. Insira ou selecione seus próprios valores, ou insira os valores de exemplo a seguir, aceite os padrões restantes e, em seguida, selecione **criar regra de alerta**:

    |Configuração                  |Valor                                                                                               |
    |---------                |---------                                                                                           |
    | Escopo                   | Escolha **Selecionar recurso**. </br> Selecione a **assinatura** que contém o endereço IP público que você deseja registrar, selecione **endereço IP público** para **tipo de recurso** e, em seguida, selecione o endereço IP público específico para o qual você deseja registrar as métricas. </br> Selecione **Concluído**. | 
    | Condição | Selecione **Selecionar condição**. </br> Em nome do sinal, selecione **sob ataque de DDoS ou não**. </br> Em **operador**, selecione **maior ou igual a**. </br> Em **tipo de agregação**, selecione **máximo**. </br> Em **valor do limite**, insira *1*. Para o **ataque sob DDoS ou não** a métrica, **0** significa que você não está sob ataque, enquanto que **1** significa que você está sob ataque. </br> Selecione **Concluído**. | 
    | Ações | Selecione **Adicionar grupos de ações**. </br> Selecione **Criar grupo de ações**. </br> Em **notificações**, em **tipo de notificação**, selecione **email/mensagem SMS/Push/voz**. </br> Em **nome**, insira _MyUnderAttackEmailAlert_. </br> Clique no botão Editar, selecione **email** e como muitas das opções a seguir que você precisa e, em seguida, selecione **OK**. </br> Selecione **Examinar + criar**. | 
    | Detalhes da regra de alerta | Em **nome da regra de alerta**, insira _MyDdosAlert_. |

Em alguns minutos de detecção de ataque, você deve receber um email de Azure Monitor métricas semelhante à imagem a seguir:

![Alerta de ataque](./media/manage-ddos-protection/ddos-alert.png)

Você também pode aprender mais sobre [configuração de webhooks](../azure-monitor/alerts/alerts-webhooks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [aplicativos lógicos](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para criação de alertas.

## <a name="view-alerts-in-azure-security-center"></a>Exibir alertas na central de segurança do Azure

A central de segurança do Azure fornece uma lista de [alertas de segurança](../security-center/security-center-managing-and-responding-alerts.md), com informações para ajudar a investigar e corrigir problemas. Com esse recurso, você obtém uma exibição unificada de alertas, incluindo alertas relacionados a ataques de DDoS e as ações tomadas para mitigar o ataque em tempo quase futuro.
Há dois alertas específicos que você verá para qualquer detecção e mitigação de ataque de DDoS:

- **Ataque de DDoS detectado para IP público**: esse alerta é gerado quando o serviço de proteção contra DDoS detecta que um de seus endereços IP públicos é o destino de um ataque de DDoS.
- **Ataque de DDoS mitigado para IP público**: esse alerta é gerado quando um ataque no endereço IP público é mitigado.
Para exibir os alertas, abra a **central de segurança** no portal do Azure. Em **proteção contra ameaças**, selecione **alertas de segurança**. A captura de tela a seguir mostra um exemplo dos alertas de ataque de DDoS.

![Alerta de DDoS na central de segurança do Azure](./media/manage-ddos-protection/ddos-alert-asc.png)

Os alertas incluem informações gerais sobre o endereço IP público que está sob ataque, informações de inteligência geográfica e contra ameaças e etapas de correção.

## <a name="validate-and-test"></a>Validar e testar

Para simular um ataque de DDoS para validar seus alertas, consulte [validar detecção de DDoS](test-through-simulations.md).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

- Configurar alertas por meio do Azure Monitor
- Configurar alertas por meio do portal
- Exibir alertas na central de segurança do Azure
- Validar e testar seus alertas

Para saber como testar e simular um ataque de DDoS, consulte o guia de testes de simulação:

> [!div class="nextstepaction"]
> [Fazer testes por meio de simulações](test-through-simulations.md)
