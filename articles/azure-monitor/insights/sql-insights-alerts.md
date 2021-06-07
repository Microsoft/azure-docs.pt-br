---
title: Criar alertas com o SQL insights (versão prévia)
description: Criar alertas com o SQL insights no Azure Monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2021
ms.openlocfilehash: 5fe853ee0f7a113bfb8b0511744d9087f67927c4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609046"
---
# <a name="create-alerts-with-sql-insights-preview"></a>Criar alertas com o SQL insights (versão prévia)
O SQL insights inclui um conjunto de modelos de regra de alerta que você pode usar para criar [regras de alerta no Azure monitor](../alert/../alerts/alerts-overview.md) para problemas comuns de SQL. As regras de alerta no SQL insights são regras de alerta de log baseadas em dados de desempenho armazenados na tabela *InsightsMetrics* em Logs de Azure monitor.  

> [!NOTE]
> Se você tiver solicitações para mais modelos de regra de alerta do SQL insights, envie comentários usando o link na parte inferior desta página ou use o link de comentários do SQL insights na portal do Azure.

## <a name="enable-alert-rules"></a>Habilitar regras de alerta 
Use as etapas a seguir para habilitar os alertas no Azure Monitor do portal do Azure.As regras de alerta criadas serão delimitadas a todos os recursos do SQL monitorados no perfil de monitoramento selecionado.  Quando uma regra de alerta é disparada, ela será disparada na instância ou no banco de dados SQL específico.

> [!NOTE]
> Você também pode criar [regras de alerta de log](../alerts/alerts-log.md) personalizadas executando consultas nos conjuntos de dados na tabela *InsightsMetrics* e salvando essas consultas como uma regra de alerta. 

Selecione **SQL (visualização)** na seção **insights** do menu Azure monitor na portal do Azure. Clique em **alertas**

:::image type="content" source="media/sql-insights-alerts/alerts-button.png" alt-text="Botão alertas":::

O painel **alertas** é aberto no lado direito da página. Por padrão, ele exibirá alertas acionados para recursos do SQL no perfil de monitoramento selecionado com base nas regras de alerta que você já criou. Selecione **modelos de alerta**, que exibirão a lista de modelos disponíveis que você pode usar para criar uma regra de alerta.

:::image type="content" source="media/sql-insights-alerts/alert-templates.png" alt-text="Modelos de alerta":::

Na página **criar regra de alerta** , examine as configurações padrão da regra e edite-as conforme necessário. Você também pode selecionar um [grupo de ações](../alerts/action-groups.md) para criar notificações e ações quando a regra de alerta for disparada. Clique em **Habilitar Regra de alerta** para criar a regra de alerta depois de verificar todas as suas propriedades.


:::image type="content" source="media/sql-insights-alerts/alert-rule.png" alt-text="Página de regras de alerta":::

Para implantar a regra de alerta imediatamente, clique em **implantar regra de alerta**. Clique em **Exibir modelo** se desejar exibir o modelo de regra antes de realmente implantá-lo.

:::image type="content" source="media/sql-insights-alerts/alert-rule-deploy.png" alt-text="Implantar regra de alerta":::

Se você optar por exibir os modelos, selecione **implantar** na página modelo para criar a regra de alerta.

:::image type="content" source="media/sql-insights-alerts/view-template-deploy.png" alt-text="Implantar do modelo de exibição":::


## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [alertas no Azure monitor](../alerts/alerts-overview.md).

