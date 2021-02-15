---
title: Recomendações de desempenho, custo e segurança automatizadas para Azure Cosmos DB
description: Saiba como exibir o desempenho, o custo, a segurança e outras recomendações personalizados para Azure Cosmos DB com base em seus padrões de carga de trabalho.
author: ThomasWeiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/28/2020
ms.reviewer: sngun
ms.openlocfilehash: d9eb55030c7ec52f9b2ac79fbab19944f0a3e190
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93087801"
---
# <a name="automated-recommendations-for-azure-cosmos-db"></a>Recomendações automatizadas para Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Todos os serviços de nuvem, incluindo Azure Cosmos DB obter atualizações frequentes com novos recursos, funcionalidades e aprimoramentos. É importante que seu aplicativo acompanhe o desempenho e as atualizações de segurança mais recentes. O portal do Azure oferece recomendações personalizadas que permitem que você maximize o desempenho do seu aplicativo. O mecanismo de consultoria do Azure Cosmos DB analisa continuamente o histórico de uso de seus recursos de Azure Cosmos DB e fornece recomendações com base em seus padrões de carga de trabalho. Essas recomendações correspondem a áreas como particionamento, indexação, rede, segurança, etc. Essas recomendações personalizadas ajudam você a melhorar o desempenho do seu aplicativo.

## <a name="view-recommendations"></a>Exibir recomendações

Você pode exibir recomendações para Azure Cosmos DB das seguintes maneiras:

- Uma maneira de exibir as recomendações está dentro da guia notificações. Se houver novas recomendações, você verá uma barra de mensagens. Entre no seu [portal do Azure](https://portal.azure.com) e navegue até sua conta do Azure Cosmos. Em sua conta do Azure Cosmos, abra o painel **notificações** e, em seguida, selecione a guia **recomendações** . Você pode selecionar a mensagem e exibir as recomendações.  

   :::image type="content" source="./media/automated-recommendations/cosmos-db-pane-recommendations.png" alt-text="Exibir recomendações do painel de Azure Cosmos DB":::

- Você também pode encontrar as recomendações por meio do assistente [do Azure](../advisor/advisor-overview.md) em categorizadas por diferentes buckets, como custo, segurança, confiabilidade, desempenho e excelência operacional. Você pode selecionar assinaturas específicas e filtrar pelo tipo de recurso, que é **Azure Cosmos DB contas** .  Quando você seleciona uma recomendação específica, ela exibe as ações que você pode tomar para beneficiar suas cargas de trabalho.

   :::image type="content" source="./media/automated-recommendations/advisor-pane-recommendations.png" alt-text="Exibir recomendações do painel de Azure Cosmos DB":::

Nem todas as recomendações mostradas no painel de Azure Cosmos DB estão disponíveis no Azure Advisor e vice-versa. Isso ocorre porque, com base no tipo de recomendação que cabe no painel do Azure Advisor, Azure Cosmos DB painel ou ambos.

Atualmente Azure Cosmos DB dá suporte a recomendações nas áreas a seguir. Cada uma dessas recomendações inclui um link para a seção relevante da documentação, portanto, é fácil realizar as próximas etapas.

## <a name="sdk-usage-recommendations"></a>Recomendações de uso do SDK

Nessa categoria, o orientador detecta o uso de uma versão antiga dos SDKs e recomenda que você atualize para uma versão mais recente para aproveitar as correções de bugs e melhorias de desempenho mais recentes. Atualmente, as seguintes recomendações específicas do SDK estão disponíveis:

|Name  |Descrição  |
|---------|---------|
| Conector antigo do Spark | Detecta o uso de versões antigas do conector do Spark e recomenda a atualização. |
| Antigo SDK do .NET | Detecta o uso de versões antigas do SDK do .NET e recomenda a atualização. |
| Antigo SDK do Java | Detecta o uso de versões antigas do conector Java e recomenda a atualização. |

## <a name="indexing-recommendations"></a>Recomendações de indexação

Nessa categoria, o orientador detecta o modo de indexação, a política de indexação, os caminhos indexados e recomenda a alteração se a configuração atual impacta o desempenho da consulta. Atualmente, as seguintes recomendações específicas de indexação estão disponíveis:

|Name  |Descrição  |
|---------|---------|
| Indexação lenta | Detecta o uso do modo de indexação lento e recomenda usar o modo de indexação consistente em vez disso. A finalidade do modo de indexação lento do Azure Cosmos DB é limitada e pode afetar a atualização dos resultados da consulta em algumas situações, para que o modo de indexação consistente seja recomendado. |
| Indexação de composição| Detecta as contas em que as consultas podem se beneficiar de índices compostos e recomenda usá-las. Os índices compostos podem melhorar drasticamente o consumo de desempenho e taxa de transferência de algumas consultas.|
| Política de indexação padrão com muitos caminhos indexados | Detecta contêineres em execução na indexação padrão com muitos caminhos indexados e recomenda personalizar a política de indexação.|
| CLASSIFICAR por consultas com altas cobranças de RU/s| Detecta contêineres que emitem consultas ORDENAdas por com altas cobranças de RU/s e recomenda explorar índices compostos.|
| Contas MongoDB 3,6 sem índice e alto consumo de RU/s| Detecta a API de Azure Cosmos DB para MongoDB com a versão 3,6 de contêineres que emitem consultas com alta carga de RU/s e recomenda a adição de índices.|

## <a name="cost-optimization-recommendations"></a>Recomendações de otimização de custo

Nessa categoria, o supervisor detecta o uso de RU/s e determina que você pode otimizar o preço fazendo algumas alterações em seus recursos ou aproveitando um modelo de preço diferente. Atualmente, as seguintes recomendações específicas de otimização de custo estão disponíveis:

|Name  |Descrição  |
|---------|---------|
| Capacidade reservada | Detecta sua utilização de RU/s e recomenda instâncias reservadas para os usuários que podem se beneficiar dela. |
| Contêineres inativos | Detecta os contêineres que não foram usados por mais de 30 dias e recomenda reduzir a taxa de transferência para esses contêineres ou excluí-los.|
| Novas assinaturas com alta taxa de transferência | Detecta novas assinaturas com contas que gastam de forma incomum alta RU/s por dia e fornece uma notificação. Essa notificação é especificamente para trazer a conscientização para novos clientes que Azure Cosmos DB opera em um modelo baseado em taxa de transferência provisionado e não em um modelo baseado em consumo. |

## <a name="migration-recommendations"></a>Recomendações de migração

Nessa categoria, o supervisor detecta que você está usando recursos herdados recomenda a migração para que você possa aproveitar a escalabilidade maciça do Azure Cosmos DB e outros benefícios. Atualmente, as seguintes recomendações específicas de migração estão disponíveis:

|Name  |Descrição  |
|---------|---------|
| Contêineres não particionados | Detecta contêineres de tamanho fixo que estão se aproximando do limite máximo de armazenamento e recomenda migrá-los para os contêineres particionados.|

## <a name="query-usage-recommendations"></a>Recomendações de uso de consulta

Nessa categoria, o orientador detecta a execução da consulta e identifica que o desempenho da consulta pode ser ajustado com algumas alterações. Atualmente, as seguintes recomendações de uso de consulta estão disponíveis:

|Name  |Descrição  |
|---------|---------|
| Consultas com tamanho de página fixo | Detecta consultas emitidas com um tamanho de página fixo e recomenda o uso de-1 (sem limite no tamanho da página) em vez de definir um valor específico. Essa opção reduz o número de viagens de ida e volta da rede necessárias para recuperar todos os resultados. |

## <a name="next-steps"></a>Próximas etapas

* [Ajustando o desempenho da consulta no Azure Cosmos DB](sql-api-query-metrics.md)
* [Solucionar problemas de consulta](troubleshoot-query-performance.md) ao usar o Azure Cosmos DB
