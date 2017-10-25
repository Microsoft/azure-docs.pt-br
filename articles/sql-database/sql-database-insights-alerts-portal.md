---
title: Usar o portal do Azure para criar alertas de Banco de Dados SQL | Microsoft Docs
description: "Use o portal do Azure para criar alertas do Banco de Dados SQL, que podem disparar notificações ou automação quando as condições especificadas são atendidas."
author: aamalvea
manager: jhubbard
editor: 
services: sql-database
documentationcenter: 
ms.assetid: f7457655-ced6-4102-a9dd-7ddf2265c0e2
ms.service: sql-database
ms.custom: monitor and tune
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2017
ms.author: aamalvea
ms.openlocfilehash: 2b3f9f226039759a68e28b9392d30256f308c8d3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-portal-to-create-alerts-for-azure-sql-database-and-data-warehouse"></a>Usar o Portal do Azure para criar alertas para o Banco de Dados SQL do Azure e para o Data Warehouse

## <a name="overview"></a>Visão geral
Este artigo mostra como configurar alertas do Banco de Dados SQL do Azure e do Data Warehouse usando o Portal do Azure. Este artigo também fornece as práticas recomendadas para definir os períodos de alerta.    

Você pode receber um alerta com base em métricas de monitoramento ou em eventos nos serviços do Azure.

* **Valores da métrica** - o alerta dispara quando o valor de uma métrica especificada ultrapassa um limite que você atribui em qualquer direção. Ou seja, ele dispara quando a condição é atendida pela primeira vez e posteriormente, quando essa condição não está sendo mais atendida.    
* **Eventos do log de atividades** - um alerta pode disparar em *cada* evento ou somente quando ocorre determinado número de eventos.

Você pode configurar um alerta para fazer o seguinte quando ele dispara:

* enviar um email para o administrador de serviços e os coadministradores
* enviar email para outros emails que você especificar.
* chamar um webhook

Você pode configurar e obter informações sobre o uso de regras de alerta

* [Portal do Azure](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../monitoring-and-diagnostics/insights-alerts-powershell.md)
* [CLI (Interface de linha de comando)](../monitoring-and-diagnostics/insights-alerts-command-line-interface.md)
* [API REST do Monitor do Azure](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Criar uma regra de alerta em uma métrica com o Portal do Azure
1. No [Portal](https://portal.azure.com/), localize o recurso no qual você está interessado em monitor e selecione-o.
2. Esta etapa é diferente para o BD SQL e pools elásticos do que para o SQL DW: 

   - **SOMENTE BD SQL e Pools Elásticos**: selecione **Alertas** ou **Regras de alerta** na seção MONITORAMENTO. O texto e o ícone podem variar um pouco para recursos diferentes.  
   
     ![Monitoramento](../monitoring-and-diagnostics/media/insights-alerts-portal/AlertRulesButton.png)
  
   - **SOMENTE SQL DW**: selecione **Monitoramento** na seção de TAREFAS COMUNS. Clique no gráfico **Uso de DWU**.

     ![TAREFAS COMUNS](../monitoring-and-diagnostics/media/insights-alerts-portal/AlertRulesButtonDW.png)

3. Selecione o comando **Adicionar alerta** e preencha os campos.
   
    ![Adicionar alerta](../monitoring-and-diagnostics/media/insights-alerts-portal/AddDBAlertPage.png)
4. Dê um **Nome** para o alerta de regra e escolha uma **Descrição**, que também mostre os emails de notificação.
5. Selecione a **Métrica** que você deseja monitorar, escolha uma **Condição** e um valor de **Limite** para a métrica. Escolha também o **Período** durante o qual a regra de métrica deverá ser atendida antes de o alerta disparar. Por exemplo, se você usar o período "PT5M" e o alerta procurar CPU acima de 80%, o alerta disparará quando a **média** da CPU estiver consistentemente acima de 80% por 5 minutos. Após a ocorrência do primeiro disparo, ele disparará novamente quando a média da CPU estiver abaixo de 80% durante 5 minutos. A medição da CPU ocorre a cada um minuto. Consulte a tabela abaixo para ver janelas de tempo com suporte e o tipo de agregação que cada alerta usa – nem todos os alertas usam o valor médio.   
6. Verifique **Proprietários de email...** se quiser que os administradores e coadministradores recebem um email quando o alerta disparar.
7. Se você quiser que outros emails recebam uma notificação quando o alerta for disparado, adicione-os ao campo **Email(s) de administrador adicionais** . Vários emails separados com ponto e vírgula – *email@contoso.com;email2@contoso.com*
8. Coloque um URI válido no campo **Webhook** se você quiser chamá-lo quando o alerta for disparado.
9. Selecione **OK** ao concluir a criação do alerta.   

Em alguns minutos, o alerta estará ativo e disparará conforme descrito anteriormente.

## <a name="managing-your-alerts"></a>Gerenciar seus alertas
Depois de criar um alerta, você poderá selecioná-lo e:

* Exibir um gráfico mostrando o limite de métrica e os valores reais do dia anterior.
* Editar ou exclui-lo.
* **Desabilitar** ou **Habilitar** se você quiser interromper temporariamente ou continuar recebendo notificações do alerta.


## <a name="sql-database-alert-values"></a>Valores de alerta do Banco de Dados SQL

| Tipo de recurso | Nome da métrica | Nome amigável | Tipo de agregação | Janela de tempo mínimo de alerta|
| --- | --- | --- | --- | --- |
| Banco de dados SQL | cpu_percent | Percentual de CPU | Média | 5 minutos |
| Banco de dados SQL | physical_data_read_percent | Porcentagem de E/S de dados | Média | 5 minutos |
| Banco de dados SQL | log_write_percent | Porcentagem de E/S de log | Média | 5 minutos |
| Banco de dados SQL | dtu_consumption_percent | Porcentagem de DTU | Média | 5 minutos |
| Banco de dados SQL | storage | Tamanho total do banco de dados | Máximo | 30 minutos |
| Banco de dados SQL | connection_successful | Conexões bem sucedidas | Total | 10 minutos |
| Banco de dados SQL | connection_failed | Conexões com falha | Total | 10 minutos |
| Banco de dados SQL | blocked_by_firewall | Bloqueado pelo firewall | Total | 10 minutos |
| Banco de dados SQL | deadlock | Deadlocks | Total | 10 minutos |
| Banco de dados SQL | storage_percent | Percentual de tamanho do banco de dados | Máximo | 30 minutos |
| Banco de dados SQL | xtp_storage_percent | Percentual (Visualização) de armazenamento do OLTP na memória | Média | 5 minutos |
| Banco de dados SQL | workers_percent | Porcentagem de funcionários | Média | 5 minutos |
| Banco de dados SQL | sessions_percent | Porcentagem de sessões | Média | 5 minutos |
| Banco de dados SQL | dtu_limit | Limite de DTU | Média | 5 minutos |
| Banco de dados SQL | dtu_used | DTU usado | Média | 5 minutos |
||||||
| Pool elástico | cpu_percent | Percentual de CPU | Média | 10 minutos |
| Pool elástico | physical_data_read_percent | Porcentagem de E/S de dados | Média | 10 minutos |
| Pool elástico | log_write_percent | Porcentagem de E/S de log | Média | 10 minutos |
| Pool elástico | dtu_consumption_percent | Porcentagem de DTU | Média | 10 minutos |
| Pool elástico | storage_percent | Porcentagem de armazenamento | Média | 10 minutos |
| Pool elástico | workers_percent | Porcentagem de funcionários | Média | 10 minutos |
| Pool elástico | eDTU_limit | Limite de eDTU | Média | 10 minutos |
| Pool elástico | storage_limit | Limite de armazenamento | Média | 10 minutos |
| Pool elástico | eDTU_used | eDTU usado | Média | 10 minutos |
| Pool elástico | storage_used | Armazenamento usado | Média | 10 minutos |
||||||               
| SQL Data Warehouse | cpu_percent | Percentual de CPU | Média | 10 minutos |
| SQL Data Warehouse | physical_data_read_percent | Porcentagem de E/S de dados | Média | 10 minutos |
| SQL Data Warehouse | storage | Tamanho total do banco de dados | Máximo | 10 minutos |
| SQL Data Warehouse | connection_successful | Conexões bem sucedidas | Total | 10 minutos |
| SQL Data Warehouse | connection_failed | Conexões com falha | Total | 10 minutos |
| SQL Data Warehouse | blocked_by_firewall | Bloqueado pelo firewall | Total | 10 minutos |
| SQL Data Warehouse | service_level_objective | Objetivo de nível de serviço do banco de dados | Total | 10 minutos |
| SQL Data Warehouse | dwu_limit | limite de dwu | Máximo | 10 minutos |
| SQL Data Warehouse | dwu_consumption_percent | Porcentagem de DWU | Média | 10 minutos |
| SQL Data Warehouse | dwu_used | DWU usado | Média | 10 minutos |
||||||


## <a name="next-steps"></a>Próximas etapas
* [Obter uma visão geral do monitoramento do Azure](../monitoring-and-diagnostics/monitoring-overview.md) , incluindo os tipos de informações que você pode coletar e monitorar.
* Saiba mais sobre como [configurar webhooks em alertas](../monitoring-and-diagnostics/insights-webhooks-alerts.md).
* Tenha uma [visão geral dos logs de diagnóstico](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) e colete métricas detalhadas de alta frequência em seu serviço.
* Tenha uma [visão geral da coleção de métricas](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) para verificar se o serviço está disponível e responsivo.
