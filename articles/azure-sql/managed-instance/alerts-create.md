---
title: Alertas e notificações de instalação para Instância Gerenciada (portal do Azure)
description: Use o portal do Azure para criar alertas do SQL Instância Gerenciada, que podem disparar notificações ou automação quando as condições especificadas são atendidas.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 05/04/2020
ms.openlocfilehash: d37465223000399aa42895626c48426ddadc0c72
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100592702"
---
# <a name="create-alerts-for-azure-sql-managed-instance-using-the-azure-portal"></a>Criar uma Instância Gerenciada de SQL do Azure usando o portal do Azure
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Este artigo mostra como configurar alertas para bancos de dados no Azure SQL Instância Gerenciada Database usando o portal do Azure. Os alertas podem enviar um email, chamar um web hook, executar o Azure Function, runbook, chamar um sistema de tíquetes compatível com ITSM externo, ligar para você no telefone ou enviar uma mensagem de texto quando alguma métrica, como o tamanho do armazenamento de instância ou a utilização da CPU, atingir um limite predefinido. Este artigo também fornece as práticas recomendadas para definir os períodos de alerta.


## <a name="overview"></a>Visão geral

Você pode receber um alerta com base em métricas de monitoramento ou em eventos nos serviços do Azure.

* **Valores de métrica** -o alerta é disparado quando o valor de uma métrica especificada cruza um limite que você atribui em qualquer direção. Ou seja, ele dispara quando a condição é atendida pela primeira vez e posteriormente, quando essa condição não está sendo mais atendida.

Você pode configurar um alerta para fazer o seguinte quando ele dispara:

* Enviar notificações por email para o administrador de serviços e coadministradores
* Enviar um email para outros emails que você especificar.
* Chamar um número de telefone com o prompt de voz
* Enviar mensagem de texto para um número de telefone
* Chamar um webhook
* Chamar Azure function
* Chamar o Azure runbook
* Chamar um sistema compatível com ITSM de tíquete externo

Você pode configurar e obter informações sobre regras de alerta usando [o portal do Azure, o PowerShell ou a CLI do Azure](../../azure-monitor/alerts/alerts-classic-portal.md) ou [Azure monitor API REST](/rest/api/monitor/alertrules). 

## <a name="alerting-metrics-available-for-managed-instance"></a>Métricas de alerta disponíveis para a instância gerenciada

> [!IMPORTANT]
> As métricas de alerta estão disponíveis somente para instância gerenciada. As métricas de alerta para bancos de dados individuais na instância gerenciada não estão disponíveis. A telemetria de diagnóstico de banco de dados está por outro lado disponível na forma de [logs de diagnóstico](../database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md#diagnostic-telemetry-for-export). Alertas em logs de diagnóstico podem ser configurados no produto [SQL Analytics](../../azure-monitor/insights/azure-sql.md) usando [scripts de alerta de log](../../azure-monitor/insights/azure-sql.md#creating-alerts-for-sql-managed-instance) para instância gerenciada.

As seguintes métricas de instância gerenciada estão disponíveis para a configuração de alertas:

| Métrica | Descrição | Unidade de medida \ valores possíveis |
| :--------- | --------------------- | ----------- |
| Percentual médio de CPU | Percentual médio de utilização da CPU no período de tempo selecionado. | 0-100 (porcentagem) |
| Bytes de E/S lidos | Bytes de e/s lidos no período de tempo selecionado. | Bytes |
| Bytes de E/S gravados | Bytes de e/s gravados no período de tempo selecionado. | Bytes |
| Contagem de solicitações de E/S | Contagem de solicitações de e/s no período de tempo selecionado. | Numérico |
| Espaço de armazenamento reservado | Espaço de armazenamento máximo atual reservado para a instância gerenciada. Alterações com a operação de dimensionamento de recursos. | MB (megabytes) |
| Espaço de armazenamento usado | Espaço de armazenamento usado no período selecionado. Alterações com consumo de armazenamento por bancos de dados e a instância. | MB (megabytes) |
| Contagem de núcleos virtuais | vCores provisionado para a instância gerenciada. Alterações com a operação de dimensionamento de recursos. | 4-80 (vCores) |

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Criar uma regra de alerta em uma métrica com o Portal do Azure

1. No [portal](https://portal.azure.com/)do Azure, localize a instância gerenciada que você está interessado em monitorar e selecione-a.

2. Selecione o item de menu **métricas** na seção monitoramento.

   ![Monitoramento](./media/alerts-create/mi-alerting-menu-annotated.png)
  
3. No menu suspenso, selecione uma das métricas nas quais você deseja configurar o alerta (o espaço de armazenamento usado é mostrado no exemplo).

4. Selecionar período de agregação-média, mínimo ou máximo atingido no período de tempo determinado (Méd, min ou Max). 

5. Selecionar **nova regra de alerta**

6. No painel criar regra de alerta, clique no **nome da condição** (o espaço de armazenamento usado é mostrado no exemplo)

   ![Definir condição](./media/alerts-create/mi-create-metrics-alert-smaller-annotated.png)

7. No painel configurar lógica de sinal, defina o operador, o tipo de agregação e o valor de limite

   * As opções de tipo de operador são maiores que, iguais e menores que (o valor de limite)
   * As opções de tipo de agregação são mín., máx. ou média (no período de granularidade de agregação)
   * O valor de limite é o valor de alerta que será avaliado com base no operador e nos critérios de agregação
   
   ![Configure_signal_logic](./media/alerts-create/mi-configure-signal-logic-annotated.png)
   
   No exemplo mostrado na captura de tela, o valor de 1840876 MB é usado representando um valor limite de 1,8 TB. Como o operador no exemplo é definido como maior que, o alerta será criado se o consumo de espaço de armazenamento na instância gerenciada passar por 1,8 TB. Observe que o valor do limite para métricas de espaço de armazenamento deve ser expresso em MB.

8. Defina o período de avaliação-granularidade de agregação em minutos e frequência de avaliação. A frequência de avaliação indicará tempo que o sistema de alerta verificará periodicamente se a condição de limite foi atendida.

9. Selecione Grupo de ações. O painel grupo de ações será exibido por meio do qual você poderá selecionar um existente ou criar uma nova ação. Essa ação define que acontecerá ao disparar um alerta (por exemplo, enviar email, ligar para você no telefone, executar um webhook, função do Azure ou um runbook, por exemplo).

   ![Select_action_group](./media/alerts-create/mi-select-action-group-smaller-annotated.png)

   * Para criar um novo grupo de ação, selecione **+ Criar grupo de ação**

      ![Create_action_group_alerts](./media/alerts-create/mi-create-alert-action-group-smaller-annotated.png)
   
   * Defina como deseja ser alertado: Insira o nome do grupo de ações, nome curto, nome da ação e tipo de ação selecionar. O tipo de ação define se você será notificado por email, mensagem de texto, chamada de voz ou, se talvez webhook, função do Azure, runbook será executado ou o tíquete de ITSM será criado em seu sistema compatível.

      ![Define_how_to_be_alerted](./media/alerts-create/mi-add-alerts-action-group-annotated.png)

10. Preencha os detalhes da regra de alerta para seus registros, selecione o tipo de severidade.

      ![Rule_description](./media/alerts-create/mi-rule-details-complete-smaller-annotated.png)

   * Conclua a criação da regra de alerta clicando no botão **criar regra de alerta** .

A nova regra de alerta ficará ativa em alguns minutos e será disparada com base em suas configurações.

## <a name="verifying-alerts"></a>Verificando alertas

> [!NOTE]
> Para suprimir alertas com ruído, consulte [supressão de alertas usando regras de ação](../../azure-monitor/alerts/alerts-action-rules.md#suppression-of-alerts).

Ao configurar uma regra de alerta, verifique se você está satisfeito com o gatilho de alerta e sua frequência. Para o exemplo mostrado nesta página para configurar um alerta no espaço de armazenamento usado, se a sua opção de alerta fosse email, você pode receber um email como o mostrado abaixo.

   ![alert_example](./media/alerts-create/mi-email-alert-example-smaller-annotated.png)

O email mostra o nome do alerta, os detalhes do limite e o motivo pelo qual o alerta foi disparado ajudando você a verificar e solucionar o problema do seu alerta. Você pode usar o botão **ver no portal do Azure** para exibir o alerta recebido por email no portal do Azure. 

## <a name="view-suspend-activate-modify-and-delete-existing-alert-rules"></a>Exibir, suspender, ativar, modificar e excluir regras de alerta existentes

> [!NOTE]
> Os alertas existentes precisam ser gerenciados no menu alertas do painel portal do Azure. Os alertas existentes não podem ser modificados na folha Instância Gerenciada recurso.

Para exibir, suspender, ativar, modificar e excluir alertas existentes:

1. Procure alertas usando a pesquisa portal do Azure. Clique em alertas.

   ![find_alerts](./media/alerts-create/mi-manage-alerts-browse-smaller-annotated.png)

   Como alternativa, você também pode clicar em alertas na barra de navegação do Azure, se você o tiver configurado.

2. No painel alertas, selecione Gerenciar regras de alerta.

   ![modify_alerts](./media/alerts-create/mi-manage-alert-rules-smaller-annotated.png)

   A lista de alertas existentes será exibida. Selecione uma regra de alerta individual existente para gerenciá-la. As regras ativas existentes podem ser modificadas e ajustadas à sua preferência. As regras ativas também podem ser suspensas sem serem excluídas. 

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre Azure Monitor sistema de alertas, confira [visão geral dos alertas no Microsoft Azure](../../azure-monitor/alerts/alerts-overview.md)
* Saiba mais sobre os alertas de métricas, veja [como os alertas de métrica funcionam no Azure monitor](../../azure-monitor/alerts/alerts-metric-overview.md)
* Saiba mais sobre como configurar um webhook em alertas, consulte [chamar um webhook com um alerta de métrica clássico](../../azure-monitor/alerts/alerts-webhooks.md)
* Saiba mais sobre como configurar e gerenciar alertas usando o PowerShell, consulte [regras de ação](/powershell/module/az.monitor/add-azmetricalertrulev2)
* Saiba mais sobre como configurar e gerenciar alertas usando a API, consulte [Azure monitor referência da API REST](/rest/api/monitor/)