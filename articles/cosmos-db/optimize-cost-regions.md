---
title: Otimizar o custo para implantações de várias regiões no Azure Cosmos DB
description: Este artigo explica como gerenciar os custos de implantações de várias regiões no Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: rimman
ms.openlocfilehash: 012eacb172acfdeb0b82343c484c664a3f75310e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60929297"
---
# <a name="optimize-multi-region-cost-in-azure-cosmos-db"></a>Otimizar o custo de várias regiões no Azure Cosmos DB

Você pode adicionar ou remover regiões da sua conta do Azure Cosmos a qualquer momento. A taxa de transferência que você configura para vários bancos de dados e contêineres do Azure Cosmos é reservada em cada região associada à sua conta. Se a taxa de transferência provisionada por hora, que é a soma de RU/s configuradas em todos os bancos de dados e contêineres para sua conta do Azure Cosmos for `T` e o número de regiões do Azure associadas à sua conta de banco de dados for `N`, então, o total de taxa de transferência provisionada para sua conta do Cosmos, para uma determinada hora é igual a:

1. `T x N RU/s` se a sua conta do Azure Cosmos estiver configurada com uma única região de gravação. 

1. `T x (N+1) RU/s` se sua conta do Azure Cosmos for configurada com todas as regiões capazes de processar gravações. 

A taxa de transferência provisionada com região de gravação única custa US$ 0,008 por hora por 100 RU/s e a taxa de transferência provisionada com várias regiões graváveis custa US$ 0,016/hora por 100 RU/s. Para saber mais, confira a [página de Preços](https://azure.microsoft.com/pricing/details/cosmos-db/) do Microsoft Azure Cosmos DB.

## <a name="costs-for-multiple-write-regions"></a>Custos de várias regiões de gravação

Em um sistema de vários mestres, as RUs disponíveis para operações de gravação aumentam `N` vezes em que `N` é o número de regiões de gravação. Ao contrário de gravações de região única, cada região agora é gravável e deve dar suporte à resolução de conflitos. A quantidade de carga de trabalho para gravadores aumentou. Do custo, planejamento de ponto de Vista, para realizar `M` a partir de RU/s das gravações em todo o mundo, você precisará provisionar M `RUs` em um nível de contêiner ou banco de dados. Em seguida, você pode adicionar quantas regiões desejar e usá-las para gravações para executar `M` RU de gravações em todo o mundo. 

### <a name="example"></a>Exemplo

Considere que você tenha um contêiner no Oeste dos EUA provisionado com uma taxa de transferência de 10.000 RU/s e tenha armazenado 1 TB de dados este mês. Suponha que você adicionou três regiões (Leste dos EUA, Europa Setentrional e Ásia Oriental), cada uma com o mesmo armazenamento e taxa de transferência, e quer a capacidade de gravar nos contêineres nas quatro regiões a partir do seu aplicativo globalmente distribuído. O valor total da sua fatura mensal (considerando 31 dias) em um mês é o seguinte:

|**Item**|**Uso (mensalmente)**|**Tarifa**|**Custo mensal**|
|----|----|----|----|
|Cobrança da taxa de transferência para o contêiner no Oeste dos EUA (várias regiões de gravação) |10 mil RU/s * 24 * 31 |US$ 0,016 por 100 RU/s por hora |US$ 1.190,40 |
|Cobrança da taxa de transferência para três regiões adicionais: Leste dos EUA, Europa Setentrional e Ásia Oriental (várias regiões de gravação) |(3 + 1) * 10 mil RU/s * 24 * 31 |US$ 0,016 por 100 RU/s por hora |US$ 4.761,60 |
|Cobrança de armazenamento para o contêiner no Oeste dos EUA |100 GB |US$ 0,25/GB |U$ 25 |
|Cobrança de armazenamento para três regiões adicionais – Leste dos EUA, Europa Setentrional e Ásia Oriental |3 * 1 TB |US$ 0,25/GB |US$ 75 |
|**Total**|||**US$ 6.052** |

## <a name="improve-throughput-utilization-on-a-per-region-basis"></a>Melhorar a utilização de taxa de transferência por região

Se você tiver uma utilização ineficaz, por exemplo, uma ou mais regiões subutilizadas ou superutilizadas, você pode usar as seguintes etapas para melhorar a utilização de taxa de transferência:  

1. Certifique-se de otimizar a taxa de transferência provisionada (RUs) na região de gravação primeiro e, em seguida, fazer o uso máximo das RUs em regiões de leitura usando o feed de alterações da região de leitura, etc. 

2. Leituras e gravações de várias regiões podem ser dimensionadas em todas as regiões associadas à conta do Azure Cosmos. 

3. Monitore a atividade em suas regiões e você pode adicionar e remover regiões sob demanda para dimensionar sua taxa de transferência de leitura e gravação.

## <a name="next-steps"></a>Próximas etapas

A seguir, você poderá saber mais sobre a otimização de custos no Azure Cosmos DB nos seguintes artigos:

* Saiba mais sobre [Otimizando para desenvolvimento e teste](optimize-dev-test.md)
* Saiba mais sobre [Entendendo sua cobrança do Azure Cosmos DB](understand-your-bill.md)
* Saiba mais sobre [Otimizando o custo da taxa de transferência](optimize-cost-throughput.md)
* Saiba mais sobre [Otimizando o custo de armazenamento](optimize-cost-storage.md)
* Saiba mais sobre [Otimizando o custo de leituras e gravações](optimize-cost-reads-writes.md)
* Saiba mais em [Otimizar o custo de consultas](optimize-cost-queries.md)

