---
title: Usando o Azure Sentinel com o Firewall do aplicativo Web do Azure
description: Este artigo mostra como usar o Azure Sentinel com o WAF (firewall do aplicativo Web) do Azure
services: web-application-firewall
author: TreMansdoerfer
ms.service: web-application-firewall
ms.date: 10/12/2020
ms.author: victorh
ms.topic: how-to
ms.openlocfilehash: 3d905dd1e6acab8f9f6d3885c882dd9c32133cb4
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100596429"
---
# <a name="using-azure-sentinel-with-azure-web-application-firewall"></a>Usando o Azure Sentinel com o Firewall do aplicativo Web do Azure

O WAF (firewall do aplicativo Web) do Azure combinado com o Azure Sentinel pode fornecer gerenciamento de eventos de informações de segurança para recursos do WAF. O Azure Sentinel fornece análise de segurança usando Log Analytics, que permite que você divida e exiba facilmente os dados do WAF. Usando o Azure Sentinel, você pode acessar pastas de trabalho predefinidas e modificá-las para melhor se ajustar às necessidades da sua organização. A pasta de trabalho pode mostrar análises para WAF na CDN (rede de distribuição de conteúdo) do Azure, WAF na porta frontal do Azure e WAF no gateway de aplicativo em várias assinaturas e espaços de trabalho.

## <a name="waf-log-analytics-categories"></a>Categorias do log Analytics do WAF

O WAF log Analytics é dividido nas seguintes categorias:  

- Todas as ações WAF executadas 
- Principais endereços de URI de solicitação bloqueada 40 
- Principais gatilhos de eventos de 50,  
- Mensagens ao longo do tempo 
- Detalhes da mensagem completa 
- Eventos de ataque por mensagens  
- Eventos de ataque ao longo do tempo 
- Filtro de ID de rastreamento 
- Mensagens de ID de rastreamento 
- 10 principais endereços IP de ataque 
- Mensagens de ataque de endereços IP 

## <a name="waf-workbook-examples"></a>Exemplos da pasta de trabalho WAF

Os exemplos de pasta de trabalho WAF a seguir mostram dados de exemplo:

:::image type="content" source="media//waf-sentinel/waf-actions-filter.png" alt-text="Filtro de ações de WAF":::

:::image type="content" source="media//waf-sentinel/top-50-event-trigger.png" alt-text="Principais eventos 50":::

:::image type="content" source="media//waf-sentinel/attack-events.png" alt-text="Eventos de ataque":::

:::image type="content" source="media//waf-sentinel/top-10-attacking-ip-address.png" alt-text="10 principais endereços IP de ataque":::

## <a name="launch-a-waf-workbook"></a>Iniciar uma pasta de trabalho do WAF

A pasta de trabalho WAF funciona para todas as portas de front-end, gateway de aplicativo e CDN do Azure. Antes de conectar os dados desses recursos, o log Analytics deve ser habilitado em seu recurso. 

Para habilitar o log Analytics para cada recurso, vá para o recurso individual do Azure, gateway de aplicativo ou porta de CDN:

1. Selecione **Configurações de diagnóstico**.
2. Selecione **+ Adicionar configuração de diagnóstico**. 
3. Na página configuração de diagnóstico:
   1. Digite um nome. 
   1. Selecione **Enviar para o Log Analytics**. 
   1. Escolha o espaço de trabalho destino de log. 
   1. Selecione os tipos de log que você deseja analisar:
      1. Gateway de aplicativo: ' ApplicationGatewayAccessLog ' e ' ApplicationGatewayFirewallLog '
      1. Porta frontal do Azure: ' FrontDoorAccessLog ' e ' FrontDoorFirewallLog '
      1. CDN: ' AzureCdnAccessLog '
   1. Selecione **Salvar**.

   :::image type="content" source="media//waf-sentinel/diagnostics-setting.png" alt-text="Configuração de diagnóstico":::

4. Na home page do Azure, digite **Azure Sentinel** na barra de pesquisa e selecione o recurso **do Azure Sentinel** . 
2. Selecione um espaço de trabalho já ativo ou crie um novo espaço de trabalho. 
3. No painel do lado esquerdo, em **configuração** , selecione **conectores de dados**.
4. Procure **Firewall do aplicativo Web da Microsoft** e selecione **WAF (firewall do aplicativo Web da Microsoft)**. Selecione a página **abrir conector** no canto inferior direito.

   :::image type="content" source="media//waf-sentinel/data-connectors.png" alt-text="Conectores de dados":::

8. Siga as instruções em **configuração** para cada recurso de WAF para o qual você deseja ter dados analíticos de log se ainda não tiver feito isso anteriormente.
6. Depois de concluir a configuração de recursos de WAF individuais, selecione a guia **próximas etapas** . Selecione uma das pastas de trabalho recomendadas. Esta pasta de trabalho usará todos os dados analíticos de log que foram habilitados anteriormente. Agora, uma pasta de trabalho do WAF funcional deve existir para seus recursos do WAF.

   :::image type="content" source="media//waf-sentinel/waf-workbooks.png" alt-text="Pastas de trabalho WAF":::


## <a name="next-steps"></a>Próximas etapas

- [Saiba mais sobre o Azure Sentinel](../sentinel/overview.md)
- [Saiba mais sobre pastas de trabalho do Azure Monitor](../azure-monitor/visualize/workbooks-overview.md)
