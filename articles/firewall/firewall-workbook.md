---
title: Monitorar logs usando a pasta de trabalho do firewall do Azure
description: As pastas de trabalho do firewall do Azure fornecem uma tela flexível para a análise de dados do firewall do Azure e a criação de relatórios visuais avançados dentro do portal do Azure.
services: firewall
author: gopimsft
ms.service: firewall
ms.topic: how-to
ms.date: 09/22/2020
ms.author: victorh
ms.openlocfilehash: e63336b0e84d303b51eda56f90ca6fb453d8ae0d
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/08/2021
ms.locfileid: "99831834"
---
# <a name="monitor-logs-using-azure-firewall-workbook"></a>Monitorar logs usando a pasta de trabalho do firewall do Azure

A pasta de trabalho do firewall do Azure fornece uma tela flexível para análise de dados do firewall do Azure. Você pode usá-lo para criar relatórios visuais avançados dentro do portal do Azure. Você pode aproveitar vários firewalls implantados no Azure e combiná-los em experiências interativas unificadas.

Você pode obter informações sobre os eventos de firewall do Azure, saber mais sobre suas regras de aplicativo e de rede e ver estatísticas de atividades de firewall em URLs, portas e endereços. A pasta de trabalho do firewall do Azure permite filtrar seus firewalls e grupos de recursos e filtrar dinamicamente por categoria com conjuntos de dados fáceis de ler ao investigar um problema em seus logs. 

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar, você deve [habilitar o log de diagnóstico](firewall-diagnostics.md#enable-diagnostic-logging-through-the-azure-portal) por meio do portal do Azure. Além disso, leia os [logs e as métricas do firewall do Azure](logs-and-metrics.md) para obter uma visão geral dos logs de diagnóstico e das métricas disponíveis para o Firewall do Azure.

## <a name="get-started"></a>Introdução

Para implantar a pasta de trabalho, acesse [Azure monitor pasta de trabalho do firewall do Azure](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20Firewall/Workbook%20-%20Azure%20Firewall%20Monitor%20Workbook) e siga as instruções na página. A pasta de trabalho do firewall do Azure foi projetada para funcionar em vários locatários, várias assinaturas e é filtrável para vários firewalls.

## <a name="overview-page"></a>Página de visão geral

A página Visão geral fornece uma maneira de filtrar entre espaços de trabalho, tempo e firewalls. Ele mostra os eventos por tempo entre firewalls e tipos de log (aplicativo, redes, ameaça Intel, proxy DNS).

:::image type="content" source="./media/firewall-workbook/firewall-workbook-overview.png" alt-text="Visão geral da pasta de trabalho do Azure firewall":::

## <a name="application-rule-log-statistics"></a>Estatísticas de log de regra de aplicativo

Esta página mostra fontes exclusivas de endereço IP ao longo do tempo, uso de contagem de regras de aplicativo, FQDN negado/permitido ao longo do tempo e dados filtrados. Você pode filtrar dados com base no endereço IP.

:::image type="content" source="./media/firewall-workbook/firewall-workbook-application-rule.png" alt-text="Log de regras do aplicativo de pasta de trabalho do firewall":::

## <a name="network-rule-log-statistics"></a>Estatísticas de log de regra de rede

Esta página fornece uma ação de exibição por regra – permitir/negar, porta de destino por IP e DNAT ao longo do tempo. Você também pode filtrar por ação, porta e tipo de destino.

:::image type="content" source="./media/firewall-workbook/firewall-workbook-network-rule.png" alt-text="Log de regra de rede da pasta de trabalho do Azure firewall":::

Você também pode filtrar logs com base na janela de tempo:

:::image type="content" source="./media/firewall-workbook/firewall-workbook-network-rule-time.png" alt-text="Janela de tempo de log da regra de rede do Azure firewall":::

## <a name="investigations"></a>Investigações

Você pode examinar os logs e entender mais sobre o recurso com base no endereço IP de origem. Você pode obter informações como nome da máquina virtual e nome da interface de rede. É simples filtrar para o recurso dos logs.

:::image type="content" source="./media/firewall-workbook/firewall-workbook-investigation.png" alt-text="Investigação da pasta de trabalho do firewall do Azure":::

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [diagnóstico de firewall do Azure](firewall-diagnostics.md)
