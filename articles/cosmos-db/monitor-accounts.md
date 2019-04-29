---
title: Monitorar as métricas de desempenho e armazenamento no Azure Cosmos DB
description: Saiba como monitorar sua conta do Azure Cosmos DB para verificar métricas de desempenho, como solicitações e erros de servidor, além de métricas de uso, como consumo de armazenamento.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: a12e0f567747b67c67f7bc825df1079d852e2f16
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61330908"
---
# <a name="monitor-performance-and-storage-metrics-in-azure-cosmos-db"></a>Monitorar as métricas de desempenho e armazenamento no Azure Cosmos DB

Monitore suas contas do Azure Cosmos DB no [portal do Azure](https://portal.azure.com/). Para cada conta do Azure Cosmos DB, um conjunto completo de métricas está disponível para monitorar a taxa de transferência, o armazenamento, a disponibilidade, a latência e a consistência.

As métricas podem ser examinadas na página da Conta, na nova página de Métricas ou no Azure Monitor.

## <a name="view-performance-metrics-on-the-metrics-page"></a>Visualizar métricas de desempenho na página de Métricas
1. No [Portal do Azure](https://portal.azure.com/), clique em **Todos os serviços**, role até **Bancos de Dados**, clique em **Azure Cosmos DB** e, em seguida, clique no nome da conta do Azure Cosmos DB cujas métricas de desempenho você deseja exibir.
2. Quando a nova página carregar, no menu de recursos, em **Monitoramento**, clique em **Métricas**.
3. Quando a página de Métricas abrir, selecione a coleção que será analisada no menu suspenso **Coleções**.

   O portal do Azure exibe o conjunto de métricas de coleção disponíveis. Observe que as métricas de taxa de transferência, armazenamento, disponibilidade, latência e consistência são fornecidas em guias separadas. Para obter detalhes adicionais sobre as métricas fornecidas, clique na seta dupla na parte superior direita de cada painel de métricas.

   ![Captura de tela da Lente de monitoramento que mostra o conjunto de métricas](./media/monitor-accounts/metrics-suite.png)

## <a name="view-performance-metrics-by-using-azure-monitoring"></a>Exibir métricas de desempenho usando o Azure Monitor
1. No [Portal do Azure](https://portal.azure.com/), clique em **Monitorar** na barra esquerda.
2. No menu de recursos, clique em **Métricas**.
3. Na janela **Monitorar – Métricas**, no menu suspenso **Grupo de recursos**, selecione o grupo de recursos associado à conta do Azure Cosmos DB que você deseja monitorar. 
4. No menu suspenso **Recursos**, selecione a conta de banco de dados para monitorar.
5. Na lista de **Métricas disponíveis**, selecione as métricas para exibir. Use o botão CTRL para selecionar várias métricas. 

## <a name="view-performance-metrics-on-the-account-page"></a>Visualizar métricas de desempenho na página de contas
1. No [Portal do Azure](https://portal.azure.com/), clique em **Todos os serviços**, role até **Bancos de Dados**, clique em **Azure Cosmos DB** e, em seguida, clique no nome da conta do Azure Cosmos DB cujas métricas de desempenho você deseja exibir.
2. A lente de **Monitoramento** exibe os seguintes blocos por padrão:
   
   * Solicitações totais para o dia de hoje.
   * Armazenamento usado.
   
   ![Captura de tela da Lente de monitoramento que mostra as solicitações e o uso do armazenamento](./media/monitor-accounts/documentdb-total-requests-and-usage.png)
3. Clicar na seta dupla na parte superior direita do bloco **Solicitações** abre uma página **Métrica** detalhada.
4. A página **Métrica** mostra detalhes sobre o total de solicitações. 

## <a name="set-up-alerts-in-the-portal"></a>Configurar alertas no Portal
1. No [Portal do Azure](https://portal.azure.com/), clique em **Todos os serviços**, clique em **Azure Cosmos DB** e, em seguida, no nome da conta do Azure Cosmos DB para a qual você deseja configurar alertas de métricas de desempenho.
2. No menu de recursos, clique em **Regras de Alerta** para abrir a página Regras de alerta.  
   ![Captura de tela da parte de regras de alerta selecionado](./media/monitor-accounts/madocdb10.5.png)
3. Na página **Regras de alerta**, clique em **Adicionar Alerta**.  
   ![Captura de tela da página Regras de Alerta, com o botão Adicionar alerta realçado](./media/monitor-accounts/madocdb11.png)
4. Na página **Adicionar uma regra de alerta** , especifique:
   
   * O nome da regra de alerta que você está configurando.
   * Uma descrição da nova regra de alerta.
   * A métrica para a regra de alerta.
   * A condição, o limite e o período que determinam quando o alerta é ativado. Por exemplo, uma contagem de erros de servidor superior a cinco nos últimos 15 minutos.
   * Se o administrador de serviços e seus coadministradores recebem ou não emails quando o alerta é enviado.
   * Endereços de email adicionais para notificações de alerta.  
     ![Captura de tela de adicionar uma página de regra de alerta](./media/monitor-accounts/madocdb12.png)

## <a name="monitor-azure-cosmos-db-programmatically"></a>Monitorar o Azure Cosmos DB de forma programática
As métricas no nível da conta disponíveis no portal, como o uso de armazenamento da conta e o total de solicitações, não estão disponíveis por meio das APIs do SQL. No entanto, você pode recuperar os dados de uso no nível da coleção usando as APIs do SQL. Para recuperar os dados do nível de coleção, faça o seguinte:

* Para usar a API REST, [execute um GET na coleção](https://msdn.microsoft.com/library/mt489073.aspx). As informações de cota e de uso da coleção são retornadas nos cabeçalhos x-ms-resource-quota e x-ms-resource-usage na resposta.
* Para usar o SDK do .NET, use o método [DocumentClient.ReadDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync.aspx), que retorna uma [ResourceResponse](https://msdn.microsoft.com/library/dn799209.aspx) que contém diversas propriedades de uso como **CollectionSizeUsage**, **DatabaseUsage**, **DocumentUsage** e muito mais.

Para acessar outras métricas, use o [SDK do Azure Monitor](https://www.nuget.org/packages/Microsoft.Azure.Insights). As definições de métricas disponíveis podem ser recuperadas chamando:

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metricDefinitions?api-version=2015-04-08

As consultas para recuperar métricas individuais usam o seguinte formato:

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metrics?api-version=2015-04-08&$filter=%28name.value%20eq%20%27Total%20Requests%27%29%20and%20timeGrain%20eq%20duration%27PT5M%27%20and%20startTime%20eq%202016-06-03T03%3A26%3A00.0000000Z%20and%20endTime%20eq%202016-06-10T03%3A26%3A00.0000000Z

Para saber mais, confira [Retrieving Resource Metrics via the Azure Monitor REST API (Recuperação de métricas de recursos por meio da API REST do Azure Monitor)](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/02/23/retrieving-resource-metrics-via-the-azure-insights-api/). Observe que "Azure Insights" foi renomeado para "Azure Monitor".  Esta entrada de blog refere-se ao nome mais antigo.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o planejamento de capacidade do Azure Cosmos DB, consulte a [calculadora do planejador de capacidade do Azure Cosmos DB](https://www.documentdb.com/capacityplanner).

