---
title: "Adicionar soluções de gerenciamento do Azure Log Analytics | Microsoft Docs"
description: "As soluções de gerenciamento do Log Analytics / OMS (Operations Management Suite) são uma coleção de lógica, visualização e regras de aquisição de dados que fornecem as métricas que giram em torno de uma área de problema específica."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: f029dd6d-58ae-42c5-ad27-e6cc92352b3b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1ba1bb02c27fa040cc2daef4baf5c9ecc827d323
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="add-azure-log-analytics-management-solutions-to-your-workspace"></a>Adicionar soluções de gerenciamento de Log Analytics do Azure a seu espaço de trabalho

As soluções do Log Analytics são uma coleção de **lógica**, **visualização** e **regras de aquisição de dados** que fornecem as métricas que giram em torno de uma área de problema específica. Este artigo lista as soluções de gerenciamento do Log Analytics com suporte e mostra como adicionar e remover em um espaço de trabalho usando o portal do Azure. Você também pode adicionar soluções no portal do OMS usando a Galeria de Soluções.

As soluções de gerenciamento permitem análises mais profundas em relação ao seguinte:

* Ajudar a investigar e a resolver problemas operacionais com mais rapidez
* Coletar e correlacionar vários tipos de dados de computador
* Ajudar você a ser proativo com as atividades expostas pela solução.

> [!NOTE]
> O Log Analytics inclui a funcionalidade Pesquisa de Log, por isso não é necessário instalar uma solução de gerenciamento para habilitá-la. No entanto, você obtém visualizações de dados, pesquisas sugeridas e análises adicionando soluções de gerenciamento ao espaço de trabalho.

Usando este artigo, você adiciona soluções de gerenciamento a um espaço de trabalho usando o portal do Azure Marketplace. Depois de adicionar uma solução, os dados são coletados dos servidores em sua infraestrutura e enviados para o serviço do OMS. O processamento do serviço do OMS normalmente leva de alguns minutos a uma hora. Depois que o serviço processar os dados, será possível exibi-los no OMS.

Você pode facilmente remover uma solução de gerenciamento quando ela não for mais necessária. Quando você remove uma solução de gerenciamento, seus dados não são enviados para o OMS. Se você estiver no Tipo de preço grátis, a remoção de uma solução pode reduzir a quantidade de dados usados, ajudando você a permanecer na cota diária de dados.

## <a name="view-available-management-solutions"></a>Exiba as soluções de gerenciamento disponíveis

O Azure marketplace contém a lista de [soluções de gerenciamento do Log Analytics](https://azuremarketplace.microsoft.com/marketplace/apps/category/monitoring-management?page=1&subcategories=management-solutions).

Você pode instalar as soluções de gerenciamento do Azure marketplace clicando no link **Obtenha agora** na parte inferior de cada solução.

## <a name="add-a-management-solution"></a>Adicionar uma solução de gerenciamento
1. Se ainda não tiver feito isso, entre no [portal do Azure](https://portal.azure.com) usando a sua assinatura do Azure.
2. Na folha **Novo** em **Marketplace**, selecione **Monitoramento + gerenciamento**.
3. Na folha **Monitoramento + gerenciamento**, clique em **Ver todos**.  
    ![Folha Monitoramento + gerenciamento](./media/log-analytics-add-solutions/monitoring-management-blade.png)  
4. À direita de **Soluções de gerenciamento**, clique em **Mais**.
5. Na folha **Soluções de Gerenciamento**, selecione uma solução de gerenciamento que você deseja adicionar ao espaço de trabalho.  
    ![Folha Monitoramento + gerenciamento](./media/log-analytics-add-solutions/management-solutions.png)  
6. Na folha de solução de gerenciamento, revise as informações sobre a solução de gerenciamento e clique em **Criar**.
7. Na folha *Nome da solução de gerenciamento*, selecione um espaço de trabalho que você deseja associar à solução de gerenciamento.
8. Opcionalmente, altere as configurações do espaço de trabalho em relação à assinatura do Azure, ao grupo de recursos e ao local. Você também pode escolher **Opções de automação**. Clique em **Criar**.  
    ![espaço de trabalho da solução](./media/log-analytics-add-solutions/solution-workspace.png)  
9. Para começar a usar a solução de gerenciamento que você adicionou ao seu espaço de trabalho, navegue até **Log Analytics** > **Assinaturas** > ***nome do espaço de trabalho*** > **Visão geral**. Um novo bloco aparece para a solução de gerenciamento. Clique no bloco para abri-lo e começar a usar a solução depois que os dados forem coletados.

## <a name="remove-a-management-solution"></a>Remover uma solução de gerenciamento

1. No [portal do Azure](https://portal.azure.com), navegue até **Log Analytics** > **Assinaturas** > ***nome do espaço de trabalho*** e, na folha do ***nome do espaço de trabalho***, clique em **Soluções**.
2. Na lista de soluções de gerenciamento, selecione a solução que você deseja remover.
3. Na folha da solução para seu espaço de trabalho, clique em **Excluir**.  
    ![excluir solução](./media/log-analytics-add-solutions/solution-delete.png)  
4. No diálogo de confirmação, clique em **Sim**.

## <a name="offers-and-pricing-tiers"></a>Ofertas e tipos de preços

A tabela a seguir identifica quais soluções de gerenciamento pertencem a cada oferta do Operations Management Suite.
A tabela também identifica o tipo de preço que está disponível para cada solução de gerenciamento.
Todas as soluções na tabela a seguir estão disponíveis no portal do Azure e a galeria de soluções no portal do Log Analytics.

| Solução de gerenciamento                                                                       | Oferta                                                                     | Tipos de preço<sup>1</sup>                                                 | Observações |
| ---                                                                                       | ---                                                                       | ---                                                                                                       | ---   |
| [Log Analytics de Atividade](log-analytics-activity.md)                                                                   | <ul><li>Insight&nbsp;e&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Grátis<br> Standard<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(Autônomo)<br> Por&nbsp;Nó&nbsp;(OMS)   | 90 dias de dados estão disponíveis gratuitamente<br>Dados não sujeitos ao limite da camada gratuita |
| [Avaliação do AD](log-analytics-ad-assessment.md)                                           | <ul><li>Insight&nbsp;e&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Grátis<br> Standard<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(Autônomo)<br> Por&nbsp;Nó&nbsp;(OMS)   | |
| [Status de replicação do AD](log-analytics-ad-replication-status.md)                           | <ul><li>Insight&nbsp;e&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Grátis<br> Standard<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(Autônomo)<br> Por&nbsp;Nó&nbsp;(OMS)   | Não disponível para adicionar do Portal do Azure/Marketplace. |
| [Integridade do Agente](../operations-management-suite/oms-solution-agenthealth.md)                                                                                | <ul><li>Insight&nbsp;e&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Grátis<br> Standard<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(Autônomo)<br> Por&nbsp;Nó&nbsp;(OMS)   | Dados não sujeitos ao limite da camada gratuita<br> Não disponível para adicionar do Portal do Azure/Marketplace. |
| [Gerenciamento de alertas](log-analytics-solution-alert-management.md)                            | <ul><li>Insight&nbsp;e&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Grátis<br> Standard<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(Autônomo)<br> Por&nbsp;Nó&nbsp;(OMS)   | Não disponível para adicionar do Portal do Azure/Marketplace. |
| [Conector do Application Insights (versão prévia)](log-analytics-app-insights-connector.md)                                               | <ul><li>Insight&nbsp;e&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Grátis<br> Standard<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(Autônomo)<br> Por&nbsp;Nó&nbsp;(OMS)   | |
| [Hybrid Worker de Automação](../automation/automation-hybrid-runbook-worker.md)                                                                     | <ul><li>Automação e Controle</li></ul>                                  | Grátis<br> Por&nbsp;Nó&nbsp;(OMS)                                                                         | Requer que seu espaço de trabalho do Log Analytics seja vinculado a uma Conta de automação |
| [Análise de Gateway de Aplicativo do Azure](log-analytics-azure-networking-analytics.md)    | <ul><li>Insight&nbsp;e&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Grátis<br> Standard<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(Autônomo)<br> Por&nbsp;Nó&nbsp;(OMS)   | |
| [Análise de Grupo de Segurança de Rede do Azure](log-analytics-azure-networking-analytics.md)     | <ul><li>Insight&nbsp;e&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Grátis<br> Standard<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(Autônomo)<br> Por&nbsp;Nó&nbsp;(OMS)   | |
| [Azure SQL Analytics (Visualização)](log-analytics-azure-sql.md)                                                       | <ul><li>Insight&nbsp;e&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Grátis<br>Por&nbsp;Nó&nbsp;(OMS)                                                                          | Requer que seu espaço de trabalho do Log Analytics seja vinculado a uma Conta de automação|
| [Análise de Aplicativos Web do Azure](log-analytics-azure-web-apps-analytics.md)     | <ul><li>Insight&nbsp;e&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Grátis<br> Standard<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(Autônomo)<br> Por&nbsp;Nó&nbsp;(OMS)   | |
|[Backup](../backup/backup-introduction-to-azure-backup.md)                                                                                 | <ul><li>Insight and Analytics</li></ul>                                   | Grátis<br> Standard<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(Autônomo)<br> Por&nbsp;Nó&nbsp;(OMS)                                                                       | Requer um cofre de Backup clássico.<br> Não disponível para adicionar do Portal do Azure/Marketplace. |
| [Capacidade e Desempenho (versão prévia)](log-analytics-capacity.md)                                                   | <ul><li>Insight&nbsp;e&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Grátis<br> Standard<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(Autônomo)<br> Por&nbsp;Nó&nbsp;(OMS)   | |
| [Controle de alterações](log-analytics-change-tracking.md)                                       | <ul><li>Automação e Controle</li></ul>                                  | Grátis<br> Por&nbsp;Nó&nbsp;(OMS)                                                                         | Requer que seu espaço de trabalho do Log Analytics seja vinculado a uma Conta de automação |
| [Contêineres](log-analytics-containers.md)                                                 | <ul><li>Insight&nbsp;e&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Grátis<br> Standard<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(Autônomo)<br> Por&nbsp;Nó&nbsp;(OMS)   | |
| [Conector de Gerenciamento do Serviço de TI (versão prévia)](log-analytics-itsmc-overview.md)                                              | <ul><li>Insight&nbsp;e&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Grátis<br> Por&nbsp;Nó&nbsp;(OMS)     | |
| Monitoramento do HDInsight HBase <br>(Visualização)                                                  | <ul><li>Insight&nbsp;e&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Grátis<br> Standard<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(Autônomo)<br> Por&nbsp;Nó&nbsp;(OMS)   | |
| [Análise do Cofre de Chaves](log-analytics-azure-key-vault.md)                   | <ul><li>Insight&nbsp;e&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Grátis<br> Standard<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(Autônomo)<br> Por&nbsp;Nó&nbsp;(OMS)   | |
| [Aplicativos Lógicos B2B](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)                    | <ul><li>Insight&nbsp;e&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Grátis<br> Standard<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(Autônomo)<br> Por&nbsp;Nó&nbsp;(OMS)   | Não disponível para adicionar do Portal do Azure/Marketplace. |
| [Avaliação de malware](log-analytics-malware.md)                                            | <ul><li>Segurança e Conformidade</li></ul>                                 | Grátis<br> Autônomo<br>Por&nbsp;Nó&nbsp;(OMS)                                                                           | Se você adicionar as soluções de Segurança e Conformidade após 19 de junho de 2017, a [cobrança é por nó](https://azure.microsoft.com/pricing/details/security-compliance/) independentemente do tipo de preço do espaço de trabalho. Os primeiros 60 dias são gratuitos.  |
| [Monitor de Desempenho de Rede](log-analytics-network-performance-monitor.md) <br>  | <ul><li>Insight and Analytics</li></ul>                                   | Grátis<br> Por&nbsp;Nó&nbsp;(OMS)                                                                         | |
| [Análise do Office 365 (versão prévia)](../operations-management-suite/oms-solution-office-365.md)                                                       | <ul><li>Insight&nbsp;e&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Grátis<br> Standard<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(Autônomo)<br> Por&nbsp;Nó&nbsp;(OMS)   | |
| [Segurança e Auditoria](../operations-management-suite/oms-security-getting-started.md)      | <ul><li>Segurança&nbsp;e&nbsp;Conformidade</li></ul>                       | Grátis<br> Autônomo<br>Por&nbsp;Nó&nbsp;(OMS)                                                                           | Coletar logs de eventos de segurança requer que esta solução<br>Se você adicionar as soluções de Segurança e Conformidade após 19 de junho de 2017, a [cobrança é por nó](https://azure.microsoft.com/pricing/details/security-compliance/) independentemente do tipo de preço do espaço de trabalho. Os primeiros 60 dias são gratuitos. |
| [Análise do Service Fabric (visualização)](log-analytics-service-fabric.md)                     | <ul><li>Insight&nbsp;e&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Grátis<br> Standard<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(Autônomo)<br> Por&nbsp;Nó&nbsp;(OMS)   | |
| [Mapa do Serviço (versão prévia)](../operations-management-suite/operations-management-suite-service-map.md) | <ul><li>Insight and Analytics</li></ul>                      | Grátis<br> Por&nbsp;Nó&nbsp;(OMS)                                                                         | Disponível no Leste dos EUA, na Europa Ocidental e no EUA Central    |
| [Recuperação de Site](../site-recovery/site-recovery-overview.md)                                                                               | <ul><li>Insight and Analytics</li></ul>                                   | Grátis<br> Standard<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(Autônomo)<br> Por&nbsp;Nó&nbsp;(OMS)                                                                       | Requer um cofre clássico do Site Recovery.<br> Não disponível para adicionar do Portal do Azure/Marketplace. |
| [Avaliação do SQL](log-analytics-sql-assessment.md)                                         | <ul><li>Insight&nbsp;e&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Grátis<br> Standard<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(Autônomo)<br> Por&nbsp;Nó&nbsp;(OMS)   | |
| Iniciar/Parar VMs durante os horários inativos<br>(Visualização)                                              | <ul><li>Insight&nbsp;e&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Grátis<br> Por&nbsp;Nó&nbsp;(OMS)                                                                         | Requer que seu espaço de trabalho do Log Analytics seja vinculado a uma Conta de automação |
| [SurfaceHub](log-analytics-surface-hubs.md)                                               | <ul><li>Insight&nbsp;e&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Grátis<br> Standard<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(Autônomo)<br> Por&nbsp;Nó&nbsp;(OMS)   | Não disponível para adicionar do Portal do Azure/Marketplace. |
| [Avaliação do System Center Operations Manager (versão prévia)](log-analytics-scom-assessment.md)  | <ul><li>Insight and Analytics</li><li>Log Analytics</li></ul>        | Grátis<br> Standard<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(Autônomo)<br> Por&nbsp;Nó&nbsp;(OMS)   | |
| [Gerenciamento de atualizações](../operations-management-suite/oms-solution-update-management.md)                                                                         | <ul><li>Automação e Controle</li></ul>                                  | Grátis<br> Por&nbsp;Nó&nbsp;(OMS)                                                                         | Requer que seu espaço de trabalho do Log Analytics seja vinculado a uma Conta de automação |
| [Conformidade de Atualizações (versão prévia)](https://docs.microsoft.com/windows/deployment/update/update-compliance-get-started)                                                             | <ul><li>Insight&nbsp;e&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Grátis<br> Standard<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(Autônomo)<br> Por&nbsp;Nó&nbsp;(OMS)   | Sem encargos para dados ou nós<br>Dados não sujeitos ao limite da camada gratuita.<br> Não disponível para adicionar do Portal do Azure/Marketplace. |
| [Preparação para atualização](https://docs.microsoft.com/windows/deployment/upgrade/upgrade-readiness-get-started)                                                          | <ul><li>Insight&nbsp;e&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Grátis<br> Standard<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(Autônomo)<br> Por&nbsp;Nó&nbsp;(OMS)   | Sem encargos para dados ou nós<br>Dados não sujeitos ao limite da camada gratuita.<br> Não disponível para adicionar do Portal do Azure/Marketplace. |
| [Monitoramento do VMware (visualização)](log-analytics-vmware.md)                                | <ul><li>Insight&nbsp;e&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Grátis<br> Standard<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(Autônomo)<br> Por&nbsp;Nó&nbsp;(OMS)   | |
| [Wire Data 2.0 (versão prévia)](log-analytics-wire-data.md)                                                                 | <ul><li>Insight and Analytics</li></ul>                                   | Grátis<br> Por&nbsp;Nó&nbsp;(OMS)                                                                         | Disponível no Leste dos EUA, na Europa Ocidental e no EUA Central |

<sup>1</sup> Os tipos de preço *Standard* e *Premium (OMS)* estão disponíveis apenas para os clientes que criaram seu espaço de trabalho do Log Analytics antes de 21 de setembro de 2016.

### <a name="community-provided-management-solutions"></a>Soluções de gerenciamento fornecidas pela comunidade

Soluções fornecidas pela comunidade estão disponíveis na [Galeria de modelos do Azure](https://azure.microsoft.com/resources/templates/?term=Per&nbsp;Node&nbsp;(OMS)) e diretamente de seus autores.

| Solução de gerenciamento               | Oferta                                                                     | Tipos de preço                         | Observações |
| ---                               | ---                                                                       | ---                                   | ---   |
| Todas as soluções fornecidas pela comunidade  | <ul><li>Insight&nbsp;e&nbsp;Analytics</li><li>Log Analytics</li></ul>   | Grátis<br> Por&nbsp;Nó&nbsp;(OMS)     |   Requer que seu espaço de trabalho do Log Analytics seja vinculado a uma Conta de automação |




## <a name="data-collection-details"></a>Detalhes da coleta de dados
As tabelas a seguir mostram os métodos de coleta de dados e outros detalhes sobre como os dados são coletados para fontes de dados e soluções de gerenciamento do Log Analytics. As tabelas são categorizadas por ofertas de solução que correspondem aos [tipos de preço da assinatura](https://go.microsoft.com/fwlink/?linkid=827926). A solução de Log Analytics de Atividade está disponível para todos os tipos de preço gratuitamente.

O agente do Log Analytics do Windows e o agente do System Center Operations Manager são essencialmente os mesmos. O agente do Windows inclui uma funcionalidade adicional para permitir a conexão com o espaço de trabalho do OMS e o encaminhamento por um proxy. Se você usar um agente do Operations Manager, ele deve ser direcionado como um agente do OMS para se comunicar com o OMS. Agentes do Operations Manager nesta tabela são agentes do OMS que estão conectados ao Operations Manager. Confira [Conectar o Operations Manager ao Log Analytics](log-analytics-om-agents.md) para obter informações sobre como conectar seu ambiente existente do Operations Manager ao OMS.

> [!NOTE]
> O tipo de agente que você usa determina como os dados são enviados ao OMS, com as seguintes condições:
> - Você usa o agente do Windows ou um agente do OMS anexado ao Operations Manager.
> - Quando o Operations Manager é necessário, dados do agente do Operations Manager para a solução são sempre enviados para o OMS usando o grupo de gerenciamento do Operations Manager. Além disso, quando o Operations Manager é necessário, apenas o agente do Operations Manager é usado pela solução.
> - Quando o Operations Manager não é necessário e a tabela mostra que os dados do agente do Operations Manager são enviados ao OMS usando o grupo de gerenciamento, os dados do agente do Operations Manager são sempre enviados ao OMS usando grupos de gerenciamento. Agentes do Windows ignoram o grupo de gerenciamento e enviam seus dados diretamente ao OMS.
> - Quando os dados do agente do Operations Manager não são enviados usando um grupo de gerenciamento, os dados são enviados diretamente ao OMS, ignorando o grupo de gerenciamento.

### <a name="insight--analytics--log-analytics"></a>Insight e Analytics / Log Analytics

| Solução de gerenciamento | Plataforma | Agente de monitoramento da Microsoft | Agente do Operations Manager | Armazenamento do Azure | Operations Manager necessário? | Dados de agente do Operations Manager enviados por meio do grupo de gerenciamento | Frequência de coleta |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Log Analytics de Atividade | As tabelas |   |   |   |   |   | após a notificação |
| Avaliação do AD |Windows |&#8226; |&#8226; |  |  |&#8226; |7 dias |
| Status de replicação do AD |Windows |&#8226; |&#8226; |  |  |&#8226; |5 dias |
| Integridade do agente | Windows e Linux | &#8226; | &#8226; |   |   | &#8226; | 1 minuto |
| Gerenciamento de Alertas (Nagios) |Linux |&#8226; |  |  |  |  |na chegada |
| Gerenciamento de Alertas (Zabbix) |Linux |&#8226; |  |  |  |  |1 minuto |
| Gerenciamento de alertas (Operations Manager) |Windows |  |&#8226; |  |&#8226; |&#8226; |3 minutos |
| Conector do Application Insights (visualização) | As tabelas |   |   |   |   |   | após a notificação |
| Análise de Gateway de Aplicativo do Azure | As tabelas |   |   |   |   |   | após a notificação |
| Análise de Grupo de Segurança de Rede do Azure | As tabelas |   |   |   |   |   | após a notificação |
| Azure SQL Analytics (Visualização) |Windows |  |  |  |  |  | 10 minutos |
| Gerenciamento de Capacidade |Windows |&#8226; |&#8226; |  |  |&#8226; |na chegada |
| Contêineres | Windows e Linux | &#8226; | &#8226; |   |   |   | 3 minutos |
| Análise do Cofre de Chaves |Windows |  |  |  |  |  |após a notificação |
| Monitor de Desempenho de Rede | Windows | &#8226; | &#8226; |   |   |   | Handshakes TCP a cada cinco segundos; dados enviados a cada três minutos |
| Análise do Office 365 (visualização) |Windows |  |  |  |  |  |após a notificação |
| Análise do Service Fabric |Windows |  |  |&#8226; |  |  |5 minutos |
| Mapa do Serviço | Windows e Linux | &#8226; | &#8226; |   |   |   | 15 s |
| Avaliação do SQL |Windows |&#8226; |&#8226; |  |  |&#8226; |7 dias |
| SurfaceHub |Windows |&#8226; |  |  |  |  |na chegada |
| System Center Operations Manager Assessment (Visualização) | Windows | &#8226; | &#8226; |   |   | &#8226; | sete dias |
| Análise de atualização (visualização) | Windows | &#8226; |   |   |   |   | 2 dias |
| Monitoramento de VMware (visualização) | Linux | &#8226; |   |   |   |   | 3 minutos |
| Dados durante a transmissão |Windows (2012 R2/8.1 ou posterior) |&#8226; |&#8226; |  |  |  | 1 minuto |


### <a name="automation--control"></a>Automação e Controle

| Solução de gerenciamento | Plataforma | Agente de monitoramento da Microsoft | Agente do Operations Manager | Armazenamento do Azure | Operations Manager necessário? | Dados de agente do Operations Manager enviados por meio do grupo de gerenciamento | Frequência de coleta |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Hybrid Worker de AutomaçãoWorker | Windows | &#8226; | &#8226; |   |   |   | n/d |
| Controle de Alterações |Windows |&#8226; |&#8226; |  |  |&#8226; |por hora |
| Controle de Alterações |Linux |&#8226; |  |  |  |  |por hora |
| Gerenciamento de atualizações | Windows |&#8226; |&#8226; |  |  |&#8226; |pelo menos, 2 vezes por dia e 15 minutos após a instalação de uma atualização |

### <a name="security--compliance"></a>Segurança e conformidade

| Solução de gerenciamento | Plataforma | Agente de monitoramento da Microsoft | Agente do Operations Manager | Armazenamento do Azure | Operations Manager necessário? | Dados de agente do Operations Manager enviados por meio do grupo de gerenciamento | Frequência de coleta |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Avaliação antimalware |Windows |&#8226; |&#8226; |  |  |&#8226; |por hora |
| Segurança e Auditoria<sup>1</sup> | Windows e Linux | parcial | parcial | parcial |   | parcial | vários |

<sup>1</sup> A solução de Segurança e Auditoria pode coletar logs de agentes do Windows, Operations Manager e Linux. Confira [Fontes de dados](#data-sources) para obter informações de coleta de dados sobre:

- syslog
- Logs de eventos de segurança do Windows
- Logs do firewall do Windows
- Logs de eventos do Windows



### <a name="protection--recovery"></a>Proteção e recuperação

| Solução de gerenciamento | Plataforma | Agente de monitoramento da Microsoft | Agente do Operations Manager | Armazenamento do Azure | Operations Manager necessário? | Dados de agente do Operations Manager enviados por meio do grupo de gerenciamento | Frequência de coleta |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Backup | As tabelas |   |   |   |   |   | n/d |
| Azure Site Recovery | As tabelas |   |   |   |   |   | n/d |


### <a name="data-sources"></a>Fontes de dados


| Fonte de dados | Plataforma | Agente de monitoramento da Microsoft | Agente do Operations Manager | Armazenamento do Azure | Operations Manager necessário? | Dados de agente do Operations Manager enviados por meio do grupo de gerenciamento | Frequência de coleta |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Logs de atividade do Azure |Windows |  |  |  |  |  |após a notificação |
| Logs de Diagnóstico do Azure |Windows |  |  |  |  |  |após a notificação |
| Métricas de Diagnóstico do Azure |Windows |  |  |  |  |  |após a notificação |
| ETW |Windows |  |  |&#8226; |  |  |5 minutos |
| Logs IIS |Windows |&#8226; |&#8226; |&#8226; |  |  |5 minutos |
| Contadores de desempenho |Windows |&#8226; |&#8226; |  |  |  |conforme agendado, mínimo de 10 segundos |
| Contadores de desempenho |Linux |&#8226; |  |  |  |  |conforme agendado, mínimo de 10 segundos |
| syslog |Linux |&#8226; |  |  |  |  |do armazenamento do Azure: 10 minutos; do agente: na chegada |
| Logs de eventos de segurança do Windows |Windows |&#8226; |&#8226; |&#8226; |  |  |para o armazenamento do Azure: 10 min; para o agente: na chegada |
| Logs do firewall do Windows |Windows |&#8226; |&#8226; |  |  |  |na chegada |
| Logs de eventos do Windows |Windows |&#8226; |&#8226; |&#8226; |  |&#8226; |para o armazenamento do Azure: 10 min; para o agente: na chegada |



## <a name="preview-management-solutions-and-features"></a>Visualizar soluções e recursos de gerenciamento
Executando um serviço e seguindo as práticas de DevOps, somos capazes de firmar parceria com os clientes para desenvolver recursos e soluções.

Durante uma visualização privada, concedemos a um pequeno grupo de clientes o acesso a uma implementação antecipada do recurso ou da solução para obter comentários e realizar melhorias. Essa implementação antecipada tem recursos e funcionalidades operacionais mínimas.

Nosso objetivo é testar as coisas rapidamente para descobrir o que funciona ou não. Iteramos por esse processo até que os comentários dos clientes de preview particular nos informem que estamos prontos para uma preview pública.

Durante a preview pública, disponibilizamos o recurso ou solução para todos os usuários a fim de obter mais comentários e validar nossa escala e eficiência. Durante essa fase:

* Os recursos de visualização aparecem na guia Configurações e podem ser habilitados por qualquer usuário.
* As soluções de visualização são adicionadas por meio da galeria ou usando um script.

### <a name="what-should-i-know-about-preview-features-and-solutions"></a>O que devo saber sobre Recursos e Soluções em visualização?
Estamos empolgados com os novos recursos e soluções de gerenciamento e adoraríamos trabalhar junto com você para desenvolvê-los.

Soluções e recursos de visualização não são para todas as pessoas. Antes de solicitar para ingressar ema visualização privada ou habilitando uma visualização pública, verifique se está tudo bem trabalhando com algo que está em desenvolvimento.

Ao habilitar um recurso de visualização por meio do portal, você verá um aviso para lembrar que o recurso está em visualização.

#### <a name="for-both-private-and-public-preview"></a>Para previews *particulares* e *públicas*
As seguintes informações são aplicáveis para ambas as visualizações públicas e privadas:

* As coisas podem não funcionar corretamente sempre.
  * Os problemas variam de pequenos incômodos até algo parar totalmente de funcionar.
* Existe a possibilidade de a visualização ter um impacto negativo em seus sistemas/seu ambiente.
  * Tentamos evitar que coisas negativas aconteçam com os sistemas que você está usando com o OMS, mas às vezes pode ocorrer algo inesperado.
* Dados podem ser perdidos/corrompidos.
* Podemos pedir que você colete logs de diagnóstico ou outros dados para ajudar a solucionar problemas.
* O recurso ou a solução podem ser removidos (temporária ou permanentemente).
  * Com base em nossas lições aprendidas durante a visualização, podemos decidir não lançar o recurso ou a solução.
* Previews podem não funcionar ou não ter sido testadas com todas as configurações, e podemos limitar:
  * Os sistemas operacionais que podem ser usados (por exemplo, um recurso pode aplicar-se somente ao Linux enquanto estiver em visualização).
  * O tipo de agente (MMA, Operations Manager) que pode ser usado (por exemplo, um recurso pode não funcionar com o Operations Manager enquanto estiver no modo de visualização).  
* Soluções e recursos em visualização não são cobertos pelo Contrato de Nível de Serviço.
* O uso de recursos de visualização gera encargos de uso.
* Recursos ou funcionalidades que você precisa para o recurso ou a solução ser útil podem estar ausentes ou incompletos.
* Recursos ou soluções podem não estar disponíveis em todas as regiões.
* Recursos ou soluções podem não estar localizados.
* Recursos ou soluções podem ter um limite do número de clientes ou dispositivos que podem usá-los.
* Você pode ter que usar scripts para realizar a configuração e para habilitar a solução/o recurso.
* A IU (interface do usuário) estará incompleta e poderá mudar diariamente.
* As visualizações públicas podem não ser apropriadas para seus sistemas de produção/críticos.

#### <a name="for-private-preview"></a>Para preview *particular*
Além dos itens acima, as seguintes informações são específicas para visualizações particulares:

* Esperamos que você nos forneça comentários sobre sua experiência para que possamos melhorar o recurso/solução.
* Poderemos entrar em contato para solicitar comentários usando pesquisas, chamadas telefônicas ou email.
* As coisas não funcionam corretamente sempre.
* Podemos pode exigir um NDA (acordo de confidencialidade) para a participação ou podemos incluir conteúdo confidencial.
  * Antes de postar em blogs, tuitar ou comunicar-se de outra forma com terceiros, confirme com o Gerente do Programa responsável pela visualização para entender possíveis restrições de divulgação.
* Não execute em sistemas de produção/críticos.

### <a name="how-do-i-get-access-to-private-preview-features-and-solutions"></a>Como obter acesso a recursos e soluções de preview particular?
Nós o convidamos clientes para previews particulares por meio de várias maneiras diferentes dependendo da preview.

* Responder a pesquisa de cliente mensal e conceder permissão para acompanhar você melhoram suas chances de ser convidado para uma preview particular.
* A equipe de contas da Microsoft pode indicar você.
* Você pode se inscrever com base nos detalhes publicados no twitter [msopsmgmt](https://twitter.com/msopsmgmt).
* Inscreva-se com base em eventos da comunidade que compartilham detalhes, encontre-nos em reuniões, conferências e nas comunidades online.

## <a name="next-steps"></a>Próximas etapas
* [Pesquise logs](log-analytics-log-searches.md) para exibir informações detalhadas coletadas pelas soluções de gerenciamento.
