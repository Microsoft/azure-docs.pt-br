---
title: Como configurar alertas em eventos de log de diagnóstico do Gateway de VPN do Azure
description: Etapas para configurar alertas em eventos de log de diagnóstico do Gateway de VPN
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptional
ms.date: 04/22/2019
ms.author: alzam
ms.openlocfilehash: 39a6d2e6201dd0635149159cb3727fd3c40f710a
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63769730"
---
# <a name="setting-up-alerts-on-vpn-gateway-diagnostic-log-events"></a>Configurando alertas em eventos de log de diagnóstico do Gateway de VPN

Este artigo ajuda você a configurar alertas com base em eventos de log de diagnóstico do Gateway de VPN.


## <a name="setup"></a>Configurar alertas do Azure Monitor com base em eventos de log de diagnóstico usando o portal

As etapas de exemplo abaixo criará um alerta para um evento de desconexão de túnel VPN site a site



1. Pesquise "Log Analytics" em todos os serviços e escolha "Espaços de trabalho do Log Analytics" ![ponto a site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert0.png "criar")

2. Clique em "Criar" na página do Log Analytics.
![Point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert1.png  "selecione")

3. Verifique o espaço de trabalho "Criar novo" e preencha os detalhes.
![Point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert2.png  "selecione")

4. Localizar o gateway VPN no Monitor de"" > folha "Configurações de diagnóstico" ![ponto a site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert3.png  "selecione")

5. Para ativar o diagnóstico, clique duas vezes no gateway e, em seguida, clique em "Ativar diagnóstico" ![ponto a site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert4.png  "selecione")

6. Preencha os detalhes e certifique-se de que a opção "Enviar para Log Analytics" e "TunnelDiagnosticLog" são verificadas. Escolha o espaço de trabalho de análise de Log que foi criado na etapa 3.
![Point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert5.png  "selecione")

7. Navegue a visão de geral do recurso de gateway da rede virtual e selecione "Alertas" na guia monitoramento, em seguida, criar uma nova regra de alerta ou editar uma regra de alerta existente.
![Point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "selecione")

8. Selecione o espaço de trabalho do Log Analytics e o recurso.
![Point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert7.png  "selecione")

9. Selecione "pesquisa de logs personalizada" como a lógica de sinal em "Adicionar condição" ![ponto a site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert8.png  "selecione")

10. Insira a seguinte consulta na caixa de texto "Consulta de pesquisa", substituindo os valores português (Brasil), conforme apropriado.

    AzureDiagnostics | onde categoria = = "TunnelDiagnosticLog" e ResourceId = = toupper ("<RESOURCEID OF GATEWAY>") e TimeGenerated > ago(5m) e remoteIP_s = = "<REMOTE IP OF TUNNEL>" e status_s = = "Desconectado"

    Defina o valor de limite como 0 e clique em "Concluído"

    ![Point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert9.png  "selecione")

11. Na página "Criar regra", clique em "Criar novo" na seção de grupos de ação. Preencha os detalhes e clique em Okey

![Point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert10.png  "selecione")

12. Na página "Criar regra", preencha os detalhes para "Personalizar a ação" e certifique-se de que o nome do grupo de ação correto é exibida na seção "Nome do grupo de ação". Clique em "Criar regra de alerta" para criar a regra.
![Point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert11.png  "selecione")

## <a name="next-steps"></a>Próximas etapas

Para configurar alertas em métricas de túnel, consulte [como configurar alertas em métricas de Gateway de VPN](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).
