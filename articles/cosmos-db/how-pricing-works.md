---
title: Modelo de preços do Azure Cosmos DB
description: Este artigo explica o modelo de preços do Azure Cosmos DB e como ele simplifica o gerenciamento e o planejamento dos custos.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: rimman
ms.openlocfilehash: 997a80ed1a8089c5255292f23bc5dacf8a6cb0e7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61059666"
---
# <a name="pricing-model-in-azure-cosmos-db"></a>Modelo de preços no Azure Cosmos DB 

O modelo de preços do Azure Cosmos DB simplifica o planejamento e o gerenciamento dos custos. Com o Azure Cosmos DB, você paga pela taxa de transferência provisionada e pelo armazenamento que consumir.

* **Taxa de transferência provisionada**: a taxa de transferência provisionada (também chamada de taxa de transferência reservada) garante um alto desempenho em qualquer escala. Especifique a taxa de transferência (RU/s) de que você precisa e o Azure Cosmos DB dedicará os recursos necessários para garantir a taxa de transferência configurada. Você será cobrado por hora pela taxa de transferência máxima provisionada para determinada hora.

   > [!NOTE]
   > Como o modelo de taxa de transferência provisionada dedica recursos para seu contêiner ou banco de dados, você será cobrado pela taxa de transferência provisionada mesmo se não executar cargas de trabalho.

* **Armazenamento consumido**: será cobrada uma taxa fixa para a quantidade total de armazenamento (GBs) consumida em dados e para os índices de determinada hora.

A taxa de transferência provisionada, especificada como [RU/s](request-units.md) (unidades de solicitação por segundo), permite que você leia ou grave dados em contêineres ou bancos de dados. Você pode [provisionar a taxa de transferência em um banco de dados ou em um contêiner](set-throughput.md). Com base nas suas necessidades de carga de trabalho, você pode aumentar ou reduzir a taxa de transferência a qualquer hora. Os preços do Azure Cosmos DB são flexíveis proporcionais à taxa de transferência que você definiu em um banco de dados ou em um contêiner. Os valores mínimos de taxa de transferência e armazenamento, e os incrementos de escala, fornecem uma gama completa de preços e flexibilidade para todos os segmentos de clientes, de contêineres de pequeno porte para contêineres de grande porte. Cada banco de dados ou contêiner é cobrado por hora pela taxa de transferência provisionada em unidades de 100 RU/s, com o mínimo de 400 RU/s, e pelo armazenamento consumido em GBs. Ao contrário da taxa de transferência provisionada, o armazenamento é cobrado com base no consumo. Ou seja, você não precisa reservar armazenamento de antemão. Você será cobrado somente pelo armazenamento que consumir.

Para obter mais informações, confira a [página de preços do Microsoft Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) e [Entendendo sua fatura do Microsoft Azure Cosmos DB](understand-your-bill.md).

O modelo de preços do Azure Cosmos DB é consistente em todas as APIs. Saiba mais em [Como o modelo de preços do Microsoft Azure Cosmos DB é econômico para os clientes](total-cost-ownership.md). Há uma taxa de transferência mínima exigida em um banco de dados ou contêiner para garantir os SLAs, e você pode aumentar ou diminuir a taxa de transferência provisionada por US$ 6 para cada 100 RU/s.

Atualmente, o preço mínimo para a taxa de transferência baseada em contêiner e em banco de dados é de US$ 24/mês (confira a [página de preços do Microsoft Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) para informações mais recentes. Se sua carga de trabalho usa vários contêineres, ela ser otimizada em termos de custo usando taxa de transferência no nível do banco de dados, pois a taxa de transferência no nível do banco de dados permite que você tenha qualquer quantidade de contêineres em um banco de dados compartilhando a taxa de transferência entre eles. A tabela a seguir resume a taxa de transferência provisionada e os custos para entidades diferentes:

|**Entidade**  | **Taxa de transferência mínima e custo** |**Incrementos de escala e custo** |**Escopo de provisionamento** |
|---------|---------|---------|-------|
|Banco de dados    | 400 RU/s (US$ 24/mês)    | 100 RU/s (US$ 6/mês)   |A taxa de transferência é reservada para o banco de dados e compartilhada por contêineres dentro do banco de dados |
|Contêiner     | 400 RU/s (US$ 24/mês)    | 100 RU/s (US$ 6/mês)  |A taxa de transferência é reservada para um contêiner específico |

Conforme mostrado na tabela anterior, o valor da taxa de transferência mínima no Azure Cosmos DB começa em US$ 24/mês. Se você começar com a taxa de transferência mínima e a escalar verticalmente ao longo do tempo para dar suporte às cargas de trabalho de produção, os custos aumentarão suavemente em incrementos de US$ 6/mês. O modelo de preços do Azure Cosmos DB é flexível e há um aumento ou diminuição suave no preço durante o aumento ou redução.

## <a name="try-azure-cosmos-db-for-free"></a>Experimente o Azure Cosmos DB gratuitamente 

O Azure Cosmos DB oferece várias opções para os desenvolvedores gratuitamente. Estas opções incluem:

* **Conta gratuita do Azure**: o Azure oferece uma [camada gratuita](https://azure.microsoft.com/free/) que dá a você US$ 200 em créditos do Azure para gastar nos primeiros 30 dias e uma quantidade limitada de serviços gratuitos por 12 meses. Para saber mais, confira [Conta gratuita do Azure](../billing/billing-avoid-charges-free-account.md). O Azure Cosmos DB é parte da conta gratuita do Azure. Especificamente para o Azure Cosmos DB, a conta gratuita oferece armazenamento de 5 GB e 400 RUs de taxa de transferência provisionada para o ano todo. 

* **Experimente o Azure Cosmos DB gratuitamente**: O Azure Cosmos DB oferece uma experiência por tempo limitado nas contas Experimente o Azure Cosmos DB gratuitamente. Você pode criar uma conta do Azure Cosmos DB, criar um banco de dados e coleções e executar um aplicativo de exemplo usando os inícios rápidos e os tutoriais. É possível executar o aplicativo de exemplo sem se inscrever em uma conta do Azure ou usar seu cartão de crédito. [Experimente o Microsoft Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) oferece o Azure Cosmos DB por um mês, com a possibilidade de renovar sua conta várias vezes.

* **Emulador do Azure Cosmos DB**: o emulador do Azure Cosmos DB fornece um ambiente local que emula o serviço do Azure Cosmos DB para fins de desenvolvimento. O emulador é oferecido sem custos e com alta fidelidade para o serviço de nuvem. Com o emulador do Azure Cosmos DB, você pode desenvolver e testar seus aplicativos localmente sem criar uma assinatura Azure ou incorrer em custos. Você pode desenvolver seus aplicativos usando o emulador localmente antes de entrar em produção. Depois que estiver satisfeito com a funcionalidade do aplicativo no emulador, você poderá passar a usar a conta do Azure Cosmos DB na nuvem e economizar custos significativamente. Para obter mais informações sobre o emulador, consulte o artigo [Usando o Azure Cosmos DB para desenvolvimento e teste](local-emulator.md).

## <a name="pricing-with-reserved-capacity"></a>Preços com capacidade reserva

A [capacidade reserva](cosmos-db-reserved-capacity.md) do Azure Cosmos DB ajuda você a economizar dinheiro ao pagar antecipadamente pelos recursos do Azure Cosmos DB por um período de um a três anos. É possível reduzir significativamente seus custos com compromissos antecipados de um ano ou três anos e economizar com descontos de 20% a 65% em comparação com o preço regular. A capacidade reserva do Azure Cosmos DB ajuda a reduzir os custos pelo pagamento antecipado da taxa de transferência provisionada (RU/s) por um período de um ou três anos, e você recebe um desconto na taxa de transferência provisionada. 

A capacidade reserva proporciona um desconto de cobrança e não afeta o estado do tempo de execução dos seus recursos do Azure Cosmos DB. A capacidade reserva está disponível consistentemente em todas as APIs, que incluem MongoDB, Cassandra, SQL, Gremlin e as Tabelas do Azure e todas as regiões em todo o mundo. Você pode aprender mais sobre a capacidade reserva no artigo [Pagar antecipadamente por recursos do Azure Cosmos DB com capacidade reserva](cosmos-db-reserved-capacity.md) e comprar capacidade reserva no [portal do Azure](https://portal.azure.com/).

## <a name="next-steps"></a>Próximas etapas

Você pode aprender mais sobre como otimizar os custos de seus recursos do Azure Cosmos DB nos seguintes artigos:

* Aprenda a [Otimizar para desenvolvimento e teste](optimize-dev-test.md)
* Saiba mais sobre [Entender sua cobrança do Azure Cosmos DB](understand-your-bill.md)
* Saiba mais sobre [Otimizando o custo da taxa de transferência](optimize-cost-throughput.md)
* Saiba mais sobre [Otimizando o custo de armazenamento](optimize-cost-storage.md)
* Saiba mais sobre [Otimizando o custo de leituras e gravações](optimize-cost-reads-writes.md)
* Saiba mais sobre [Otimizando o custo de consultas](optimize-cost-queries.md)
* Saiba mais sobre [Otimizando o custo de contas do Cosmos em várias regiões](optimize-cost-regions.md)
* Saiba mais sobre [Capacidade reservada do Azure Cosmos DB](cosmos-db-reserved-capacity.md)
* Saiba mais sobre o [Emulador do Microsoft Azure Cosmos DB](local-emulator.md)
