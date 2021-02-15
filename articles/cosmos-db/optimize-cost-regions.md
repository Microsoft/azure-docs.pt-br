---
title: Otimizar o custo para implantações de várias regiões no Azure Cosmos DB
description: Este artigo explica como gerenciar os custos de implantações de várias regiões no Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2020
ms.openlocfilehash: a559a51feafa310a4645282dc6368f520fc6b972
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96459616"
---
# <a name="optimize-multi-region-cost-in-azure-cosmos-db"></a>Otimizar o custo de várias regiões no Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Você pode adicionar ou remover regiões da sua conta do Azure Cosmos a qualquer momento. A taxa de transferência que você configura para vários bancos de dados e contêineres do Azure Cosmos é reservada em cada região associada à sua conta. Se a taxa de transferência provisionada por hora, que é a soma de RU/s configurada em todos os bancos de dados e contêineres para sua conta do Azure Cosmos for `T` e o número de regiões do Azure associado à sua conta de banco de `N` dado for, a taxa de transferência total provisionada para sua conta do cosmos será igual a uma determinada hora `T x N RU/s` .

A taxa de transferência provisionada com região de gravação única custa US$ 0,008 por hora por 100 RU/s e a taxa de transferência provisionada com várias regiões graváveis custa US$ 0,016/hora por 100 RU/s. Para saber mais, confira a [página de Preços](https://azure.microsoft.com/pricing/details/cosmos-db/) do Microsoft Azure Cosmos DB.

## <a name="costs-for-multiple-write-regions"></a>Custos de várias regiões de gravação

Em um sistema de gravações de várias regiões, o RUs de rede disponível para operações de gravação aumenta `N` os tempos em que `N` é o número de regiões de gravação. Ao contrário das gravações de região única, todas as regiões agora são graváveis e dão suporte à resolução de conflitos. Do ponto de vista do planejamento de custos, para executar `M` ru/s de gravações em todo o mundo, você precisará provisionar M `RUs` em um nível de contêiner ou banco de dados. Em seguida, você pode adicionar quantas regiões desejar e usá-las para gravações para executar `M` RU de gravações em todo o mundo.

### <a name="example"></a>Exemplo

Considere que você tem um contêiner no oeste dos EUA configurado para gravações de região única, provisionado com taxa de transferência de 10K RU/s, armazenando 0,5 TB de dados neste mês. Vamos supor que você adicione uma região, leste dos EUA, com o mesmo armazenamento e taxa de transferência e queira a capacidade de gravar nos contêineres em ambas as regiões do seu aplicativo. A nova conta mensal total (supondo 730 horas em um mês) será a seguinte:

|**Item**|**Uso (mensalmente)**|**Tarifa**|**Custo mensal**|
|----|----|----|----|
|Fatura de taxa de transferência para o contêiner no oeste dos EUA (região de gravação única) |10K RU/s * 730 horas |$0.08 por 100 RU/s por hora |$584 |
|Fatura de taxa de transferência para o contêiner em 2 regiões-oeste dos EUA & leste dos EUA (várias regiões de gravação) |2 * 10K RU/s * 730 horas |US$ 0,016 por 100 RU/s por hora |$2336 |
|Cobrança de armazenamento para o contêiner no Oeste dos EUA |0,5 TB (ou 512 GB) |US$ 0,25/GB |$128 |
|Fatura de armazenamento para o contêiner em 2 regiões-oeste dos EUA & leste dos EUA |2 * 0,5 TB (ou 1.024 GB) |US$ 0,25/GB |$256 |

## <a name="improve-throughput-utilization-on-a-per-region-basis"></a>Melhorar a utilização de taxa de transferência por região

Se você tiver uma utilização ineficiente, por exemplo, uma ou mais regiões de leitura subutilizadas, poderá tomar medidas para fazer o uso máximo do RUs em regiões de leitura usando o feed de alterações da região de leitura ou movê-lo para outro secundário se for superutilizado. Você precisará garantir que Otimize a produtividade provisionada (RUs) primeiro na região de gravação. Grava custos mais do que leituras, a menos que consultas muito grandes, para manter a utilização até mesmo possam ser desafiadoras. Em geral, monitore a taxa de transferência consumida em suas regiões e adicione ou remova regiões sob demanda para dimensionar sua taxa de transferência de leitura e gravação, garantindo entender o impacto da latência para todos os aplicativos implantados na mesma região.

## <a name="next-steps"></a>Próximas etapas

A seguir, você poderá saber mais sobre a otimização de custos no Azure Cosmos DB nos seguintes artigos:

* Saiba mais sobre [Otimizando para desenvolvimento e teste](optimize-dev-test.md)
* Saiba mais sobre [Entender sua cobrança do Azure Cosmos DB](understand-your-bill.md)
* Saiba mais sobre [Otimizando o custo da taxa de transferência](optimize-cost-throughput.md)
* Saiba mais sobre [Otimizando o custo de armazenamento](optimize-cost-storage.md)
* Saiba mais sobre [Otimizando o custo de leituras e gravações](optimize-cost-reads-writes.md)
* Saiba mais sobre [Otimizando o custo de consultas](./optimize-cost-reads-writes.md)
