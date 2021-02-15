---
title: Como monitorar a latência do lado do servidor para operações no Azure Cosmos DB
description: Saiba como monitorar a latência do servidor para operações em Azure Cosmos DB conta ou um contêiner. Os proprietários de uma conta de Azure Cosmos DB podem entender os problemas de latência do lado do servidor com suas contas do Azure Cosmos.
ms.service: cosmos-db
ms.topic: how-to
author: kanshiG
ms.author: govindk
ms.date: 04/07/2020
ms.openlocfilehash: ec6a9db63504958640137fcd0fcfc904eb01afa5
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93074722"
---
# <a name="how-to-monitor-the-server-side-latency-for-operations-in-an-azure-cosmos-db-container-or-account"></a>Como monitorar a latência do lado do servidor para operações em um Azure Cosmos DB contêiner ou uma conta
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

O Azure Monitor para Azure Cosmos DB fornece uma exibição de métricas para monitorar sua conta e criar painéis. As métricas do Azure Cosmos DB são coletadas por padrão, e esse recurso não exige que você habilite nem configure nada explicitamente. A métrica de latência do lado do servidor é usada para exibir a latência do lado do servidor de uma operação. Azure Cosmos DB fornece SLA de menos de 10 ms para operações de leitura/gravação de ponto com conectividade direta. Para operações de leitura e gravação de ponto, os SLAs são calculados como detalhados no [documento SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/).

Você pode monitorar a latência do lado do servidor se você vir uma latência excepcionalmente alta para operação de ponto, como:

* Uma operação GET ou uma SET com chave de partição e ID no modo de conectividade direta
* Uma operação de leitura ou gravação ou
* Uma consulta

Você pode pesquisar o log de diagnóstico para ver o tamanho dos dados retornados. Se você vir uma alta latência sustentada para operações de consulta, deverá procurar o log de diagnóstico para obter uma taxa de transferência mais alta [ou ru/s](cosmosdb-monitor-resource-logs.md#diagnostic-queries) usado. A latência do lado do servidor mostra a quantidade de tempo gasto na infraestrutura de back-end antes que os dados sejam retornados ao cliente. É importante observar essa métrica para eliminar quaisquer problemas de latência de back-end.

## <a name="view-the-server-side-latency-metric"></a>Exibir a métrica de latência do lado do servidor

1. Entre no [portal do Azure](https://portal.azure.com/).

1. Selecione **Monitor** na barra de menus de navegação à esquerda e selecione **Métricas** .

   :::image type="content" source="./media/monitor-server-side-latency/monitor-metrics-blade.png" alt-text="Painel Métricas no Azure Monitor":::

1. No painel **Métricas** > **Selecionar um recurso** > escolha a **assinatura** e o **grupo de recursos** necessários. Para o **Tipo de recurso** , selecione **Contas do Azure Cosmos DB** , escolha uma das contas existentes do Azure Cosmos e selecione **Aplicar** .
   
   :::image type="content" source="./media/monitor-server-side-latency/select-cosmos-db-account.png" alt-text="Painel Métricas no Azure Monitor":::

1. Em seguida, selecione a métrica de **latência do lado do servidor**  na lista de métricas disponíveis. Para saber mais detalhadamente sobre todas as métricas disponíveis nesta lista, consulte o artigo [Métricas por categoria](monitor-cosmos-db-reference.md). Neste exemplo, vamos selecionar latência do **lado do servidor** e **Méd** como o valor de agregação. Além desses detalhes, você também pode selecionar o **Intervalo de tempo** e a **Granularidade de tempo** das métricas. Você pode exibir as métricas de, no máximo, os últimos 30 dias.  Depois que você aplicar o filtro, um gráfico será exibido com base no seu filtro. Você pode ver a latência do lado do servidor por minuto para o período selecionado.  

   :::image type="content" source="./media/monitor-server-side-latency/server-side-latency-metric.png" alt-text="Painel Métricas no Azure Monitor":::

## <a name="filters-for-server-side-latency"></a>Filtros para latência do lado do servidor

Você também pode filtrar as métricas e obter os gráficos exibidos por um **CollectionName** , **ConnectionMode** , **DatabaseName** , **OperationType** , **região** e **PublicAPIType** específicos. 

Para filtrar as métricas, selecione **Adicionar filtro** e escolha a propriedade necessária, como **PublicAPIType** , e selecione o valor **SQL** . Adicione outro filtro para **OperationType** . Em seguida, o grafo exibe a latência do lado do servidor para diferentes operações durante o período selecionado. As operações executadas por meio do procedimento armazenado não são registradas em log, de modo que não estão disponíveis na métrica OperationType.

As métricas de **latência do lado do servidor** para cada operação são exibidas conforme mostrado na imagem a seguir:

:::image type="content" source="./media/monitor-server-side-latency/server-side-latency-filters.png" alt-text="Painel Métricas no Azure Monitor":::

Você também pode agrupar as métricas usando a opção **aplicar divisão** .  

## <a name="next-steps"></a>Próximas etapas

* Monitore dados do Azure Cosmos DB usando [configurações de diagnóstico](cosmosdb-monitor-resource-logs.md) no Azure.
* [Auditar operações do painel de controle do Azure Cosmos DB](audit-control-plane-logs.md)
