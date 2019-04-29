---
title: Configurar alertas e notificações usando o portal do Azure | Microsoft Docs
description: Use o portal do Azure para criar alertas do Banco de Dados SQL, que podem disparar notificações ou automação quando as condições especificadas são atendidas.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 11/02/2018
ms.openlocfilehash: 93337e39a117c1f8d38f24dc416ff8ae95513a34
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61035902"
---
# <a name="create-alerts-for-azure-sql-database-and-data-warehouse-using-azure-portal"></a>Criar alertas para o Banco de Dados SQL do Azure e para o Data Warehouse usando o portal do Azure

## <a name="overview"></a>Visão geral
Este artigo mostra como configurar alertas do Banco de Dados SQL do Azure e do Data Warehouse usando o Portal do Azure. Os alertas podem enviar a você um email ou chamar um webhook quando alguma métrica (por exemplo, tamanho do banco de dados ou uso da CPU) atinge o limite. Este artigo também fornece as práticas recomendadas para definir os períodos de alerta.    

> [!IMPORTANT]
> Esse recurso ainda não está disponível na Instância Gerenciada. Como alternativa, você pode usar o SQL Agent para enviar alertas por email para algumas métricas com base em [Exibições de Gerenciamento Dinâmico](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views).

Você pode receber um alerta com base em métricas de monitoramento ou em eventos nos serviços do Azure.

* **Valores da métrica** - o alerta dispara quando o valor de uma métrica especificada ultrapassa um limite que você atribui em qualquer direção. Ou seja, ele dispara quando a condição é atendida pela primeira vez e posteriormente, quando essa condição não está sendo mais atendida.    
* **Eventos do log de atividades** - um alerta pode disparar em *cada* evento ou somente quando ocorre determinado número de eventos.

Você pode configurar um alerta para fazer o seguinte quando ele dispara:

* enviar um email para o administrador de serviços e os coadministradores
* enviar email para outros emails que você especificar.
* chamar um webhook

Você pode configurar e obter informações sobre o uso de regras de alerta

* [Portal do Azure](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../azure-monitor/platform/alerts-classic-portal.md)
* [CLI (Interface de linha de comando)](../azure-monitor/platform/alerts-classic-portal.md)
* [API REST do Monitor do Azure](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Criar uma regra de alerta em uma métrica com o Portal do Azure
1. No [Portal](https://portal.azure.com/), localize o recurso no qual você está interessado em monitor e selecione-o.
2. Selecione **Alertas (Clássicos)** na seção MONITORAMENTO. O texto e o ícone podem variar um pouco para recursos diferentes.  
   
     ![Monitoramento](media/sql-database-insights-alerts-portal/AlertsClassicButton.JPG)
  
   - **SOMENTE SQL DW**: Clique no grafo **Uso de DWU**. Selecione **exibir alertas clássicos**

3. Selecione o botão **adicionar alerta de métrica (clássico)** preencha os campos.
   
    ![Adicionar alerta](media/sql-database-insights-alerts-portal/AddDBAlertPageClassic.JPG)
4. Dê um **Nome** para o alerta de regra e escolha uma **Descrição**, que também mostre os emails de notificação.
5. Selecione a **Métrica** que você deseja monitorar, escolha uma **Condição** e um valor de **Limite** para a métrica. Escolha também o **Período** durante o qual a regra de métrica deverá ser atendida antes de o alerta disparar. Por exemplo, se você usar o período "PT5M" e o alerta procurar CPU acima de 80%, o alerta disparará quando a **média** da CPU estiver consistentemente acima de 80% por 5 minutos. Após a ocorrência do primeiro disparo, ele disparará novamente quando a média da CPU estiver abaixo de 80% durante 5 minutos. A medição da CPU ocorre a cada um minuto. Consulte a tabela abaixo para ver janelas de tempo com suporte e o tipo de agregação que cada alerta usa – nem todos os alertas usam o valor médio.   
6. Verifique **Proprietários de email...** se quiser que os administradores e coadministradores recebem um email quando o alerta disparar.
7. Se você quiser que outros emails recebam uma notificação quando o alerta for disparado, adicione-os ao campo **Email(s) de administrador adicionais** . Vários emails separados com ponto e vírgula - *e-mail\@contoso.com;email2\@contoso.com*
8. Coloque um URI válido no campo **Webhook** se você quiser chamá-lo quando o alerta for disparado.
9. Selecione **OK** ao concluir a criação do alerta.   

Em alguns minutos, o alerta estará ativo e disparará conforme descrito anteriormente.

## <a name="managing-your-alerts"></a>Gerenciar seus alertas
Depois de criar um alerta, você poderá selecioná-lo e:

* Exibir um grafo mostrando o limite de métrica e os valores reais do dia anterior.
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
| SQL Data Warehouse | connection_successful | Conexões bem sucedidas | Total | 10 minutos |
| SQL Data Warehouse | connection_failed | Conexões com falha | Total | 10 minutos |
| SQL Data Warehouse | blocked_by_firewall | Bloqueado pelo firewall | Total | 10 minutos |
| SQL Data Warehouse | service_level_objective | Camada de serviço do banco de dados | Total | 10 minutos |
| SQL Data Warehouse | dwu_limit | limite de dwu | Máximo | 10 minutos |
| SQL Data Warehouse | dwu_consumption_percent | Porcentagem de DWU | Média | 10 minutos |
| SQL Data Warehouse | dwu_used | DWU usado | Média | 10 minutos |
||||||


## <a name="next-steps"></a>Próximas etapas
* [Obter uma visão geral do monitoramento do Azure](../monitoring-and-diagnostics/monitoring-overview.md) , incluindo os tipos de informações que você pode coletar e monitorar.
* Saiba mais sobre como [configurar webhooks em alertas](../azure-monitor/platform/alerts-webhooks.md).
* Tenha uma [visão geral dos logs de diagnóstico](../azure-monitor/platform/diagnostic-logs-overview.md) e colete métricas detalhadas de alta frequência em seu serviço.
* Tenha uma [visão geral da coleção de métricas](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) para verificar se o serviço está disponível e responsivo.
