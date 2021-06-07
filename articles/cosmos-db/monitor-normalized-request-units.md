---
title: Monitorar RU/s normalizado para um contêiner Cosmos do Azure ou uma conta
description: Saiba como monitorar o uso da unidade de solicitação normalizada de uma operação no Azure Cosmos DB. Os proprietários de uma conta de Azure Cosmos DB podem entender quais operações estão consumindo mais unidades de solicitação.
ms.service: cosmos-db
ms.topic: how-to
author: kanshiG
ms.author: govindk
ms.date: 01/07/2021
ms.openlocfilehash: ec82532b54e7834b62fcc03d3ee7de1345a0f546
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98027759"
---
# <a name="how-to-monitor-normalized-rus-for-an-azure-cosmos-container-or-an-account"></a>Como monitorar RU/s normalizados para um contêiner Cosmos do Azure ou uma conta
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

O Azure Monitor para Azure Cosmos DB fornece uma exibição de métricas para monitorar sua conta e criar painéis. As métricas do Azure Cosmos DB são coletadas por padrão, e esse recurso não exige que você habilite nem configure nada explicitamente.

A métrica de **consumo de ru normalizada** é usada para ver como os intervalos de chave de partição são bem saturados em relação ao tráfego. Azure Cosmos DB distribui a taxa de transferência igualmente entre todos os intervalos de chaves de partição. Essa métrica fornece uma exibição por segundo da utilização máxima da taxa de transferência para o intervalo de chaves de partição. Use essa métrica para calcular o uso de RU/s no intervalo de chaves de partição para determinado contêiner. Usando essa métrica, se você vir uma alta porcentagem de utilização de unidades de solicitação em todos os intervalos de chaves de partição no Azure monitor, você deve aumentar a taxa de transferência para atender às necessidades de sua carga de trabalho. Exemplo-a utilização normalizada é definida como o máximo da utilização de RU/s em todos os intervalos de chaves de partição. Por exemplo, suponha que a taxa de transferência máxima seja de 20.000 RU/s e que você tenha dois intervalos de chave de partição, P_1 e P_2, cada um com capacidade de dimensionar para 10.000 RU/s. Em determinado segundo, se P_1 tiver usado 6.000 RUs e P_2 8.000 RUs, a utilização normalizada será MAX(6.000 RUs / 10.000 RUs, 8.000 RUs/ 10.000 RUs) = 0,8.

## <a name="what-to-expect-and-do-when-normalized-rus-is-higher"></a>O que esperar e fazer quando a RU/s normalizada é maior

Quando o consumo de RU/s normalizado atingir 100% para determinado intervalo de chaves de partição, e se um cliente ainda fizer solicitações nesse período de 1 segundo para esse intervalo de chaves de partição específico, ele receberá um erro de taxa limitada. O cliente deve respeitar o tempo de espera sugerido e repetir a solicitação. O SDK facilita a manipulação dessa situação, repetindo os tempos pré-configurados ao aguardar adequadamente.  Não é necessário que você veja o erro de limitação da taxa de RU apenas porque o RU normalizado atingiu 100%. Isso ocorre porque o RU normalizado é um único valor que representa o uso máximo em todos os intervalos de chave de partição, um intervalo de chaves de partição pode estar ocupado, mas os outros intervalos de chave de partição podem atender às solicitações sem problemas. Por exemplo, uma única operação, como um procedimento armazenado que consome todos os RU/s em um intervalo de chaves de partição, levará a um pequeno pico no consumo normalizado de RU/s. Nesses casos, não haverá erro de limitação de taxa imediata, se a taxa de solicitação for baixa ou se forem feitas solicitações a outras partições em diferentes intervalos de chaves de partição. 

As métricas de Azure Monitor ajudam a localizar as operações por código de status para a API do SQL usando a métrica **total de solicitações** . Posteriormente, você pode filtrar essas solicitações pelo código de status 429 e dividi-las por **tipo de operação**.  

Para localizar as solicitações, que são limitadas por taxa, a maneira recomendada é obter essas informações por meio dos logs de diagnóstico.

Se houver um pico contínuo de 100% de consumo normalizado de RU/s ou perto de 100% em vários intervalos de chaves de partição, é recomendável aumentar a taxa de transferência. Você pode descobrir quais operações são pesadas e seu uso de pico utilizando as métricas do Azure monitor e os logs de diagnóstico do Azure monitor.

Em resumo, a métrica de **consumo de ru normalizada** é usada para ver qual intervalo de chave de partição está mais quente em termos de uso. Portanto, ele oferece a distorção da taxa de transferência em direção a um intervalo de chaves de partição. Posteriormente, você pode acompanhar para ver o log do **PartitionKeyRUConsumption** em logs de Azure monitor para obter informações sobre quais chaves de partição lógica estão quentes em termos de uso. Isso apontará para alteração na opção de chave de partição ou na alteração da lógica do aplicativo. Para resolver a limitação de taxa, distribua a carga de dados por meio de várias partições ou apenas aumente a taxa de transferência conforme necessário. 

## <a name="view-the-normalized-request-unit-consumption-metric"></a>Exibir a métrica de consumo de unidade de solicitação normalizada

1. Entre no [portal do Azure](https://portal.azure.com/).

2. Selecione **Monitor** na barra de menus de navegação à esquerda e selecione **Métricas**.

   :::image type="content" source="./media/monitor-normalized-request-units/monitor-metrics-blade.png" alt-text="Painel Métricas no Azure Monitor":::

3. No painel **Métricas** > **Selecionar um recurso** > escolha a **assinatura** e o **grupo de recursos** necessários. Para o **Tipo de recurso**, selecione **Contas do Azure Cosmos DB**, escolha uma das contas existentes do Azure Cosmos e selecione **Aplicar**.

   :::image type="content" source="./media/monitor-normalized-request-units/select-cosmos-db-account.png" alt-text="Escolha uma conta do Azure Cosmos para exibir as métricas":::

4. Em seguida, você pode selecionar uma métrica na lista de métricas disponíveis. Você pode selecionar métricas específicas para unidades de solicitação, armazenamento, latência, disponibilidade, Cassandra e outros. Para saber mais detalhadamente sobre todas as métricas disponíveis nesta lista, consulte o artigo [Métricas por categoria](monitor-cosmos-db-reference.md). Neste exemplo, vamos selecionar métrica de **consumo de ru normalizada** e **Max** como o valor de agregação.

   Além desses detalhes, você também pode selecionar o **Intervalo de tempo** e a **Granularidade de tempo** das métricas. Você pode exibir as métricas de, no máximo, os últimos 30 dias.  Depois que você aplicar o filtro, um gráfico será exibido com base no seu filtro.

   :::image type="content" source="./media/monitor-normalized-request-units/normalized-request-unit-usage-metric.png" alt-text="Escolha uma métrica no portal do Azure":::

### <a name="filters-for-normalized-request-unit-consumption"></a>Filtros para consumo de unidade de solicitação normalizado

Você também pode filtrar as métricas e o gráfico exibidos por um **CollectionName**, **DatabaseName**, **PartitionKeyRangeID** e **região** específicos. Para filtrar as métricas, selecione **Adicionar filtro** e escolha a propriedade necessária, como **CollectionName** e o valor correspondente no qual você está interessado. Em seguida, o grafo exibe as unidades de consumo de RU normalizadas consumidas para o contêiner para o período selecionado.  

Você pode agrupar as métricas usando a opção **Aplicar divisão**. Para bancos de dados de taxa de transferência compartilhada, a métrica de RU normalizada mostra os dados somente na granularidade dos bancos de dados; ela não mostra nenhum dado por coleção. Portanto, para o banco de dados de produtividade compartilhado, você não verá nenhum dado quando aplicar a divisão pelo nome da coleção.

A métrica de consumo de unidade de solicitação normalizada para cada contêiner é exibida conforme mostrado na imagem a seguir:

:::image type="content" source="./media/monitor-normalized-request-units/normalized-request-unit-usage-filters.png" alt-text="Aplicar filtros à métrica de consumo de unidade de solicitação normalizada":::

## <a name="next-steps"></a>Próximas etapas

* Monitore dados do Azure Cosmos DB usando [configurações de diagnóstico](cosmosdb-monitor-resource-logs.md) no Azure.
* [Auditar operações do painel de controle do Azure Cosmos DB](audit-control-plane-logs.md)
