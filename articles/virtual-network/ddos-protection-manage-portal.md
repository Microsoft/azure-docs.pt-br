---
title: "Gerenciar a Proteção contra DDoS do Azure Standard usando o Portal do Azure | Microsoft Docs"
description: "Saiba como usar a telemetria da Proteção contra DDoS do Azure Standard no Azure Monitor para mitigar um ataque."
services: virtual-network
documentationcenter: na
author: kumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/22/2017
ms.author: kumud
ms.openlocfilehash: 5c599b4cc867dbc9a081af3a081195b998f63954
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-azure-ddos-protection-standard-using-the-azure-portal"></a>Gerenciar a Proteção contra DDoS do Azure Standard usando o Portal do Azure

>[!IMPORTANT]
>A Proteção contra DDoS do Azure Standard (Proteção contra DDoS) está atualmente em versão prévia. O suporte à Proteção contra DDoS só é dado por um número limitado de recursos do Azure e em um número seleto de regiões. Você precisa [registrar-se para o serviço](http://aka.ms/ddosprotection) durante a versão prévia limitada para habilitar a Proteção contra DDoS para a sua assinatura. Você é contatado pela equipe de DDoS do Azure após o registro para lhe orientar durante o processo de habilitação. A Proteção contra DDoS está disponível nas regiões Leste dos EUA, Oeste dos EUA e Centro-oeste dos EUA. Durante a versão prévia, você não será cobrado pelo uso do serviço.

Este artigo lhe mostra como usar o Portal do Azure para habilitar a Proteção contra DDoS, como desabilitá-la e como usar a telemetria para mitigar um ataque. 

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Faça logon no Portal do Azure em http://portal.azure.com.

## <a name="enable-ddos-protection"></a>Habilitar Proteção contra DDoS

Habilite a Proteção contra DDoS em uma rede virtual nova ou existente.

### <a name="create-a-new-virtual-network-and-enable-ddos-protection"></a>Criar uma nova rede virtual e habilitar a Proteção contra DDoS

1. Clique no botão **Novo** no canto superior esquerdo do Portal do Azure.
2. Selecione **Rede** e, em seguida, selecione **Rede Virtual**.
3. Insira as informações da rede virtual. Em *Proteção contra DDoS*, clique em **Habilitado** e, em seguida, clique em **Criar**.

    ![Criar rede virtual](./media/ddos-protection-manage-portal/ddos-create-vnet.png)   

Um aviso informa que a habilitação da Proteção contra DDoS incorre em encargos. Nenhum encargo para a Proteção contra DDoS é gerado durante a versão prévia. As cobranças são geradas na GA (disponibilidade geral) e os clientes receberão um aviso 30 dias antes do início das cobranças e da GA.

### <a name="enable-ddos-protection-on-an-existing-virtual-network"></a>Habilitar a Proteção contra DDoS em uma rede virtual existente 

1. Clique em **Redes virtuais** no menu do Portal do Azure e, em seguida, selecione a rede virtual.
2. Clique em **Proteção contra DDoS**, clique em **Habilitada** na tela *Proteção contra DDoS* e, em seguida, clique em **Salvar**. 

Um aviso informa que a habilitação da Proteção contra DDoS incorre em encargos. Nenhum encargo para a Proteção contra DDoS é gerado durante a versão prévia. As cobranças são geradas na GA (disponibilidade geral) e os clientes receberão um aviso 30 dias antes do início das cobranças e da GA.

## <a name="disable-ddos-protection"></a>Desabilitar Proteção contra DDoS

1. Clique em **Redes virtuais** no menu do Portal do Azure e, em seguida, selecione a rede virtual.
2. Clique em **Proteção contra DDoS**, clique em **Desabilitada** na tela *Proteção contra DDoS* e, em seguida, clique em **Salvar**.

## <a name="configure-alerts-on-ddos-protection-metrics"></a>Configurar alertas em métricas de Proteção contra DDoS

Aproveitando a configuração de alerta do Azure Monitor, você pode selecionar qualquer uma das métricas de Proteção contra DDoS disponíveis para alertar quando há uma mitigação ativa durante um ataque. Quando as condições forem atendidas, você receberá um email de alerta no endereço especificado.

1. Clique em **Monitor** e, em seguida, clique em **Métricas**.
2. Na tela *Métricas*, selecione o grupo de recursos, o tipo de recurso do **Endereço IP Público**e o IP público do Azure.
3. Para configurar um alerta de email para uma métrica, clique em **Clique para adicionar um alerta**. Um alerta de email pode ser criado em qualquer métrica, mas a métrica mais óbvia é **Sob ataque de DDoS ou não**. Esse é um valor booliano 1 ou 0. Um **1** significa que você está sob ataque. Um **0** significa que você não está sob ataque.
4. Para receber um email quando estiver sob ataque, defina a métrica para **Sob ataque de DDoS ou não** e a **Condição para Maior que zero (0) pelos últimos 5 minutos**. Alertas semelhantes podem ser configurados para outras métricas.

    ![Configurar métricas](./media/ddos-protection-manage-portal/ddos-metrics.png)

    Em poucos minutos de detecção de ataque, você será notificado pelo uso de métricas no Azure Monitor.

    ![Alerta de ataque](./media/ddos-protection-manage-portal/ddos-alert.png) 

Você também pode aprender mais sobre [configuração de webhooks](../monitoring-and-diagnostics/insights-webhooks-alerts.md) e [aplicativos lógicos](../logic-apps/logic-apps-what-are-logic-apps.md) para criação de alertas.

## <a name="configure-logging-on-ddos-protection-metrics"></a>Configurar o registro em log para métricas de Proteção contra DDoS

1. Clique em **Monitor** e, em seguida, clique em **Configurações de Diagnóstico**.
2. Na tela *Métricas*, selecione o grupo de recursos, o tipo de recurso do **Endereço IP Público**e o IP público do Azure.
3. Clique em **Ativar diagnóstico para coletar os dados a seguir**.

Há três opções disponíveis para registro em log:

- **Arquivar em uma conta de armazenamento** – grava os logs em uma conta de armazenamento.
- **Fluxo para um hub de eventos** – permite que um destinatário de log colete os logs usando um hub de eventos. Isso habilita a integração com o Splunk ou outros sistemas SIEM.
- **Enviar para o Log Analytics** – grava logs para o serviço Azure Log Analytics do OMS.

## <a name="use-ddos-protection-telemetry"></a>Usar a telemetria da Proteção contra DDoS

A telemetria de um ataque é fornecida por meio do Azure Monitor em tempo real. A telemetria está disponível somente durante o tempo pelo qual um endereço IP público está sob mitigação. Você não verá telemetria antes nem depois de um ataque ser mitigado.

1. Clique em **Monitor** e, em seguida, clique em **Métricas**. 
2. Na tela *Métricas*, selecione o grupo de recursos, o tipo de recurso do **Endereço IP Público**e o IP público do Azure. Uma série de Métricas Disponíveis aparece no lado esquerdo da tela. Quando selecionadas, essas métricas são mostradas no Gráfico de Métricas do Azure Monitor na tela de visão geral. 

Os nomes de métrica apresentam diferentes tipos de pacotes e bytes versus pacotes, com um constructo básico de nomes de marca em cada métrica da seguinte maneira:

- **Nome da marca Ignorado (por exemplo, Pacotes de Entrada Ignorados por DDoS):** o número de pacotes ignorados/removidos pelo sistema de Proteção contra DDoS.
- **Nome da marca Encaminhado (por exemplo, Pacotes de Entrada Encaminhados por DDoS):** o número de pacotes encaminhados pelo sistema DDoS para o VIP de destino – tráfego que não foi filtrado.
- **Nenhum nome de marca (por exemplo: Pacotes de Entrada de DDoS):** o número total de pacotes que entraram no sistema de depuração – que representa a soma dos pacotes ignorados e encaminhados.

## <a name="next-steps"></a>Próximas etapas

- [Saiba mais sobre os Logs de Diagnóstico do Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)
- [Analisar logs do Armazenamento do Azure com o Log Analytics](../log-analytics/log-analytics-azure-storage.md)
- [Introdução aos Hubs de Evento](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)