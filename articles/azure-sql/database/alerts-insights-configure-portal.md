---
title: Alertas e notificações de instalação no portal do Azure
description: Use o portal do Azure para criar alertas, que podem disparar notificações ou automação quando as condições especificadas são atendidas.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: aamalvea
ms.author: aamalvea
ms.reviewer: wiassaf, sstein
ms.date: 05/04/2020
ms.openlocfilehash: 86ac0f64a6a797b1bc702597ed30e6417f2efe14
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500964"
---
# <a name="create-alerts-for-azure-sql-database-and-azure-synapse-analytics-using-the-azure-portal"></a>Criar alertas para o banco de dados SQL do Azure e a análise de Synapse do Azure usando o portal do Azure
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]


## <a name="overview"></a>Visão geral

Este artigo mostra como configurar alertas para bancos de dados no banco de dados SQL do Azure e a análise de Synapse do Azure usando o portal do Azure. Os alertas podem enviar a você um email ou chamar um webhook quando alguma métrica (por exemplo, tamanho do banco de dados ou uso da CPU) atinge o limite.

> [!NOTE]
> Para o Azure SQL Instância Gerenciada instruções específicas, consulte [criar alertas para o sql instância gerenciada do Azure](../managed-instance/alerts-create.md).

Você pode receber um alerta com base em métricas de monitoramento ou em eventos nos serviços do Azure.

* **Valores de métrica** -o alerta é disparado quando o valor de uma métrica especificada cruza um limite que você atribui em qualquer direção. Ou seja, ele dispara quando a condição é atendida pela primeira vez e posteriormente, quando essa condição não está sendo mais atendida.
* **Eventos do log de atividades** – um alerta pode disparar em *cada* evento ou somente quando ocorre um determinado número de eventos.

Você pode configurar um alerta para fazer o seguinte quando ele dispara:

* Enviar notificações por email para o administrador e coadministradores de serviços
* Enviar um email para outros emails que você especificar.
* Chamar um webhook

Você pode configurar e obter informações sobre o uso de regras de alerta

* [O portal do Azure](../../azure-monitor/platform/alerts-classic-portal.md)
* [PowerShell](../../azure-monitor/platform/alerts-classic-portal.md)
* [Uma CLI (interface de linha de comando)](../../azure-monitor/platform/alerts-classic-portal.md)
* [API REST do Azure Monitor](/rest/api/monitor/alertrules)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Criar uma regra de alerta em uma métrica com o Portal do Azure

1. No [Portal](https://portal.azure.com/), localize o recurso no qual você está interessado em monitor e selecione-o.
2. Selecione **alertas** na seção monitoramento. O texto e o ícone podem variar um pouco para recursos diferentes.  

   ![Monitoramento](./media/alerts-insights-configure-portal/Alerts.png)
  
3. Selecione o botão **nova regra de alerta** para abrir a página **criar regra** .
  ![Criar regra](./media/alerts-insights-configure-portal/create-rule.png)

4. Na seção **condição** , clique em **Adicionar**.
  ![Definir condição](./media/alerts-insights-configure-portal/create-rule.png)
5. Na página **Configurar lógica de sinal** , selecione um sinal.
  ![Selecionar sinal](./media/alerts-insights-configure-portal/select-signal.png)
6. Depois de selecionar um sinal, como **percentual de CPU**, a página **Configurar lógica de sinal** será exibida.
  ![Configurar sinal lógico](./media/alerts-insights-configure-portal/configure-signal-logic.png)
7. Nessa página, configure esse tipo de limite, operador, tipo de agregação, valor de limite, granularidade de agregação e frequência de avaliação. Em seguida, clique em **Concluído**.
8. Na **regra criar**, selecione um grupo de **ações** existente ou crie um novo grupo. Um grupo de ações permite que você defina a ação a ser executada quando ocorrer uma condição de alerta.
  ![Definir grupo de ação](./media/alerts-insights-configure-portal/action-group.png)

9. Defina um nome para a regra, forneça uma descrição opcional, escolha um nível de severidade para a regra, escolha se deseja habilitar a regra na criação de regra e clique em **criar alerta de regra** para criar o alerta de regra de métrica.

Em 10 minutos, o alerta está ativo e é disparado conforme descrito anteriormente.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre como [configurar webhooks em alertas](../../azure-monitor/platform/alerts-webhooks.md).