---
title: Monitorar o uso da taxa de transferência de uma operação no Azure Cosmos DB
description: Saiba como monitorar a taxa de transferência ou o uso da unidade de solicitação de uma operação no Azure Cosmos DB. Os proprietários de uma conta do Azure Cosmos DB podem entender quais operações estão usando mais unidades de solicitação.
ms.service: cosmos-db
ms.topic: how-to
author: kanshiG
ms.author: govindk
ms.date: 04/09/2020
ms.openlocfilehash: d6f0f30d7437b6f8ecc1d915eb3d3195f2504fec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93098239"
---
# <a name="how-to-monitor-throughput-or-request-unit-usage-of-an-operation-in-azure-cosmos-db"></a>Como monitorar a taxa de transferência ou o uso da unidade de solicitação de uma operação no Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

O Azure Monitor para Azure Cosmos DB fornece uma exibição de métricas para monitorar sua conta e criar painéis. As métricas do Azure Cosmos DB são coletadas por padrão, e esse recurso não exige que você habilite nem configure nada explicitamente. A métrica **Total de Unidades de Solicitação** é usada para obter o uso de unidades de solicitação para diferentes tipos de operações. Posteriormente, você pode analisar quais operações usaram a maior parte da taxa de transferência. Por padrão, os dados de taxa de transferência são agregados em um intervalo de um minuto. No entanto, você pode alterar a unidade de agregação alterando a opção de granularidade do tempo.

Há duas maneiras de analisar os dados de uso da unidade de solicitação:

* Quais operações estão usando mais unidades de solicitação dentro do intervalo de tempo determinado.
* Quais operações em geral dominam sua carga de trabalho consumindo mais unidades de solicitação.
Essa análise permite que você se concentre em operações como insert e upsert e examine a indexação delas. Você pode descobrir se você está sub/superindexando campos específicos e modificar a [política de indexação](index-policy.md#include-exclude-paths) para incluir ou excluir os caminhos.

Se você observar que determinadas consultas estão usando mais unidades de solicitação, poderá executar ações como:

* Reconsiderar se você está solicitando a quantidade certa de dados.
* Modificar a consulta para usar o índice com a cláusula de filtro.
* Executar chamadas de função UDF menos onerosas.
* Defina chaves de partição para minimizar a dispersão da consulta em partições diferentes.
* Você também pode usar as métricas de consulta retornadas na resposta da chamada, os detalhes do log de diagnóstico e conferir o artigo [ajuste de desempenho de consulta](sql-api-query-metrics.md) para saber mais sobre a execução da consulta.
* Você pode começar da soma e, em seguida, examinar a utilização média usando a dimensão correta.

## <a name="view-the-total-request-unit-usage-metric"></a>Exibir a métrica de uso total da unidade de solicitação

1. Entre no [portal do Azure](https://portal.azure.com/).

1. Selecione **Monitor** na barra de menus de navegação à esquerda e selecione **Métricas**.

   :::image type="content" source="./media/monitor-request-unit-usage/monitor-metrics-blade.png" alt-text="Painel Métricas no Azure Monitor":::

1. No painel **Métricas** > **Selecionar um recurso** > escolha a **assinatura** e o **grupo de recursos** necessários. Para o **Tipo de recurso**, selecione **Contas do Azure Cosmos DB**, escolha uma das contas existentes do Azure Cosmos e selecione **Aplicar**.

   :::image type="content" source="./media/monitor-request-unit-usage/select-cosmos-db-account.png" alt-text="Escolha a conta do Azure Cosmos DB para exibir as métricas":::

1. Em seguida, selecione a métrica **Total de Unidades de Solicitação** na lista de métricas disponíveis. Para saber mais detalhadamente sobre todas as métricas disponíveis nesta lista, consulte o artigo [Métricas por categoria](monitor-cosmos-db-reference.md). Neste exemplo, vamos selecionar **Unidades de Solicitação Totais** e **Média** como o valor de agregação. Além desses detalhes, você também pode selecionar o **Intervalo de tempo** e a **Granularidade de tempo** das métricas. Você pode exibir as métricas de, no máximo, os últimos 30 dias.  Depois que você aplicar o filtro, um gráfico será exibido com base no seu filtro. Você pode ver o número médio de unidades de solicitação consumidas por minuto pelo período selecionado.  

   :::image type="content" source="./media/monitor-request-unit-usage/request-unit-usage-metric.png" alt-text="Escolha uma métrica no portal do Azure":::

## <a name="filters-for-request-unit-usage"></a>Filtros para o uso da unidade de solicitação

Você também pode filtrar as métricas e obter os gráficos exibidos por um **CollectionName**, **DatabaseName**, **OperationType**, **Region**, **Status** e **StatusCode** específicos. As opções **Adicionar filtro** e **Aplicar divisão** permitem filtrar o uso da unidade de solicitação e agrupar as métricas.

Para obter o uso da unidade de solicitação de cada operação por total (soma) ou média, selecione **Aplicar divisão** e escolha **Tipo de operação** e o valor do filtro, conforme mostrado na seguinte imagem:

   :::image type="content" source="./media/monitor-request-unit-usage/request-unit-usage-operations.png" alt-text="Unidades de solicitação do Cosmos DB para operações no Azure Monitor":::

Se você quiser ver o uso da unidade de solicitação por coleção, selecione **Aplicar divisão** e escolha o nome da coleção como um filtro. Você verá um chat semelhante ao mostrado a seguir, com uma opção de coleções no painel. Em seguida, você pode selecionar um nome de coleção específico para exibir mais detalhes:

   :::image type="content" source="./media/monitor-request-unit-usage/request-unit-usage-collection.png" alt-text="As unidades de solicitação do Cosmos DB para todas as operações pela coleção no Azure Monitor":::

## <a name="next-steps"></a>Próximas etapas

* Monitore dados do Azure Cosmos DB usando [configurações de diagnóstico](cosmosdb-monitor-resource-logs.md) no Azure.
* [Auditar operações do painel de controle do Azure Cosmos DB](audit-control-plane-logs.md)
