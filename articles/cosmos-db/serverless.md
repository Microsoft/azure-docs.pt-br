---
title: Oferta sem servidor baseada em consumo no Azure Cosmos DB
description: Saiba mais sobre a oferta sem servidor baseada em consumo do Azure Cosmos DB.
author: ThomasWeiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/08/2021
ms.openlocfilehash: 0c75f9938b3bc4fa8a2e650f77a3708e91180fea
ms.sourcegitcommit: 31cfd3782a448068c0ff1105abe06035ee7b672a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/10/2021
ms.locfileid: "98059212"
---
# <a name="azure-cosmos-db-serverless-preview"></a>Azure Cosmos DB sem servidor (visualização)
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

O Azure Cosmos DB sem servidor permite que você use sua conta do cosmos do Azure em um modo baseado em consumo, no qual você é cobrado apenas pelas unidades de solicitação consumidas pelas operações de banco de dados e pelo armazenamento consumido pelos sua data. Os contêineres sem servidor podem atender a milhares de solicitações por segundo sem nenhum encargo mínimo e nenhum planejamento de capacidade é necessário.

> [!IMPORTANT] 
> Você tem algum comentário sobre o servidor? Queremos ouvir! Sinta-se à vontade para remover uma mensagem para a equipe Azure Cosmos DB sem servidor: [azurecosmosdbserverless@service.microsoft.com](mailto:azurecosmosdbserverless@service.microsoft.com) .

Ao usar Azure Cosmos DB, cada operação de banco de dados tem um custo expresso em [unidades de solicitação](request-units.md). A cobrança por esse custo depende do tipo de conta do Azure cosmos que você está usando:

- No modo de [taxa de transferência provisionado](set-throughput.md) , você precisa se comprometer com uma determinada quantidade de taxa de transferência (expressa em unidades de solicitação por segundo) que é provisionada em seus bancos de dados e contêineres. O custo de suas operações de banco de dados é então deduzido do número de unidades de solicitação disponíveis a cada segundo. No final do período de cobrança, você será cobrado pela quantidade de taxa de transferência provisionada.
- No modo sem servidor, você não precisa provisionar nenhuma taxa de transferência ao criar contêineres em sua conta do Azure Cosmos. No final do período de cobrança, você será cobrado pelo número de unidades de solicitação que foram consumidas pelas operações de banco de dados.

## <a name="use-cases"></a>Casos de uso

Os cenários de melhor Azure Cosmos DB servidor se encaixam em situações em que você espera **tráfego intermitente e imprevisível** com tempos ociosos longos. Como o provisionamento de capacidade nessas situações não é necessário e pode ser proibitivo, Azure Cosmos DB sem servidor deve ser considerado nos seguintes casos de uso:

- Introdução ao Azure Cosmos DB
- Executando aplicativos com
    - intermitência, tráfego intermitente que é difícil de prever ou
    - baixo (<10%) taxa de tráfego média-para-pico
- Desenvolvimento, teste, criação de protótipos e execução em aplicativos novos de produção em que o padrão de tráfego é desconhecido
- Integração com serviços de computação sem servidor como [Azure Functions](../azure-functions/functions-overview.md)

Consulte o artigo [como escolher entre produtividade provisionada e sem servidor](throughput-serverless.md) para obter mais diretrizes sobre como escolher a oferta que melhor se adapta ao seu caso de uso.

## <a name="using-serverless-resources"></a>Usando recursos sem servidor

Sem servidor é um novo tipo de conta do Azure Cosmos, o que significa que você precisa escolher entre a **taxa de transferência provisionada** e sem **servidor** ao criar uma nova conta. Você deve criar uma nova conta sem servidor para começar com o servidor. Durante a versão de visualização, a única maneira com suporte para criar uma nova conta sem servidor é [usando o portal do Azure](create-cosmosdb-resources-portal.md). Atualmente, não há suporte para a migração de contas existentes para/do modo sem servidor.

Qualquer contêiner criado em uma conta sem servidor é um contêiner sem servidor. Os contêineres sem servidor expõem os mesmos recursos que os contêineres criados no modo de taxa de transferência provisionado, de modo que você lê, grava e consulta seus dados exatamente da mesma maneira. No entanto, as contas e os contêineres sem servidor também têm características específicas:

> [!IMPORTANT]
> Algumas dessas limitações podem ser facilitoudas ou removidas quando o servidor não estiver disponível de forma geral e **seus comentários** nos ajudarão a decidir! Alcance e conte-nos mais sobre sua experiência sem servidor: [azurecosmosdbserverless@service.microsoft.com](mailto:azurecosmosdbserverless@service.microsoft.com) .

- Uma conta sem servidor só pode ser executada em uma única região do Azure. Não é possível adicionar outras regiões do Azure a uma conta sem servidor depois de criá-la.
- Não é possível habilitar o recurso de [visualização de link do Synapse](synapse-link.md) em uma conta sem servidor.
- A taxa de transferência de provisionamento não é necessária em contêineres sem servidor, portanto, as seguintes instruções são aplicáveis:
    - Você não pode passar nenhuma taxa de transferência ao criar um contêiner sem servidor e fazer isso retorna um erro.
    - Você não pode ler ou atualizar a taxa de transferência em um contêiner sem servidor e fazer isso retorna um erro.
    - Você não pode criar um banco de dados de produtividade compartilhado em uma conta sem servidor e fazer isso retorna um erro.
- Contêineres sem servidor podem armazenar um máximo de 50 GB de dados e índices.

## <a name="monitoring-your-consumption"></a>Monitorando seu consumo

Se você usou Azure Cosmos DB no modo de taxa de transferência provisionado antes, descobrirá que sem servidor é mais econômico quando o tráfego não justificar a capacidade provisionada. A desvantagem é que seus custos se tornarão menos previsíveis, pois você será cobrado com base no número de solicitações processadas pelo banco de dados. Por isso, é importante ficar atento ao consumo atual.

Ao navegar no painel de **métricas** de sua conta, você encontrará um gráfico chamado **unidades de solicitação consumidas** na guia **visão geral** . Este gráfico mostra quantas unidades de solicitação sua conta consumiu:

:::image type="content" source="./media/serverless/request-units-consumed.png" alt-text="Gráfico mostrando as unidades de solicitação consumidas" border="false":::

Você pode encontrar o mesmo gráfico ao usar Azure Monitor, conforme descrito [aqui](monitor-request-unit-usage.md). Observe que Azure Monitor permite que você configure [alertas](../azure-monitor/platform/alerts-metric-overview.md), que podem ser usados para notificá-lo quando o consumo da unidade de solicitação tiver passado um certo limite.

## <a name="performance"></a><a id="performance"></a>Desempenho

Os recursos sem servidor geram características de desempenho específicas que são diferentes das entregas dos recursos de taxa de transferência provisionados. Depois que a oferta sem servidor ficar disponível para o público geral, a latência de contêineres sem servidor será coberta por um SLO (objetivo de nível de serviço) de 10 milissegundos ou menos para leituras pontuais e 30 milissegundos ou menos para gravações. Uma operação de leitura de ponto consiste em buscar um único item por sua ID e valor de chave de partição.

## <a name="next-steps"></a>Próximas etapas

Comece a usar sem servidor com os seguintes artigos:

- [Unidades de Solicitação no Azure Cosmos DB](request-units.md)
- [Escolher entre a taxa de transferência provisionada e sem servidor](throughput-serverless.md)
- [Modelo de preços no Azure Cosmos DB](how-pricing-works.md)
