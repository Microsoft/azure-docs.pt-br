---
title: Modelo de preços do Azure Cosmos DB
description: Este artigo explica o modelo de preços do Azure Cosmos DB e como ele simplifica o gerenciamento e o planejamento dos custos.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: 573fc4fac413ceed50246bc6fb8df1d9db021c94
ms.sourcegitcommit: 08458f722d77b273fbb6b24a0a7476a5ac8b22e0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/15/2021
ms.locfileid: "98247447"
---
# <a name="pricing-model-in-azure-cosmos-db"></a>Modelo de preços no Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

O modelo de preços do Azure Cosmos DB simplifica o planejamento e o gerenciamento dos custos. Com o Azure Cosmos DB, você paga pelas operações executadas no banco de dados e pelo armazenamento consumido por eles.

- **Operações de banco de dados**: a maneira como você é cobrado pelas operações de banco de dados depende do tipo de conta do Azure cosmos que você está usando.

  - **Taxa de transferência provisionada**: a [taxa de transferência provisionada](set-throughput.md) (também chamada de taxa de transferência reservada) fornece alto desempenho em qualquer escala. Você especifica a taxa de transferência de que precisa em [unidades de solicitação](request-units.md) por segundo (ru/s) e Azure Cosmos DB dedica os recursos necessários para fornecer a taxa de transferência configurada. Você pode [provisionar a taxa de transferência em um banco de dados ou em um contêiner](set-throughput.md). Com base nas suas necessidades de carga de trabalho, você pode dimensionar a taxa de transferência para cima/para baixo a qualquer momento ou usar o [dimensionamento automático](provision-throughput-autoscale.md) (embora haja uma taxa de transferência mínima necessária em um banco de dados ou um contêiner para garantir os SLAs). Você será cobrado por hora pela taxa de transferência máxima provisionada para determinada hora.

   > [!NOTE]
   > Como o modelo de taxa de transferência provisionado dedica recursos ao contêiner ou banco de dados, você será cobrado pela taxa de transferência que você provisionou mesmo se não executar nenhuma carga de trabalho.

  - Sem **servidor**: no modo de [servidor](serverless.md) , você não precisa provisionar nenhuma taxa de transferência ao criar recursos em sua conta do Azure Cosmos. No final do período de cobrança, você será cobrado pela quantidade de unidades de solicitação consumidas pelas operações de banco de dados.

- **Armazenamento**: você é cobrado como uma taxa fixa para a quantidade total de armazenamento consumido por seus dados e índices para uma determinada hora. O armazenamento é cobrado de acordo com o consumo, de modo que você não precisa reservar nenhum armazenamento com antecedência. Você será cobrado somente pelo armazenamento que consumir.

O modelo de preços do Azure Cosmos DB é consistente em todas as APIs. Para obter mais informações, consulte a [página de preços Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/), [noções básicas sobre sua fatura de Azure Cosmos DB](understand-your-bill.md) e [como Azure Cosmos DB modelo de preços é econômico para os clientes](total-cost-ownership.md).

Se você implantar sua conta de Azure Cosmos DB em uma região não governamental nos EUA, haverá um preço mínimo para a taxa de transferência baseada em contêiner e banco de dados no modo de taxa de transferência provisionado. Não há nenhum preço mínimo no modo sem servidor. O preço varia de acordo com a região que você está usando, consulte a [página de preços Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) para obter as informações mais recentes sobre preços.

## <a name="try-azure-cosmos-db-for-free"></a>Experimente o Azure Cosmos DB gratuitamente

O Azure Cosmos DB oferece muitas opções para os desenvolvedores a ele gratuitamente. Estas opções incluem:

* **Azure Cosmos DB camada gratuita**: Azure Cosmos DB camada gratuita facilita a introdução, o desenvolvimento e teste de seus aplicativos ou até mesmo a execução de cargas de trabalho de produção pequenas gratuitamente. Quando a camada gratuita estiver habilitada em uma conta, você obterá os primeiros 400 RU/s e 5 GB de armazenamento na conta gratuitamente, durante o tempo de vida da conta. Você pode ter até uma conta de camada gratuita por assinatura do Azure e deve aceitar ao criar a conta. Para começar, [crie uma nova conta no portal do Azure com a camada gratuita habilitada](create-cosmosdb-resources-portal.md) ou use um [modelo ARM](./manage-with-templates.md#free-tier).

* **Conta gratuita do Azure**: o Azure oferece uma [camada gratuita](https://azure.microsoft.com/free/) que fornece $200 em créditos do Azure pelos primeiros 30 dias e uma quantidade limitada de serviços gratuitos por 12 meses. Para saber mais, confira [Conta gratuita do Azure](../cost-management-billing/manage/avoid-charges-free-account.md). O Azure Cosmos DB é parte da conta gratuita do Azure. Especificamente para Azure Cosmos DB, essa conta gratuita oferece armazenamento de 25 GB e 400 RU/s de taxa de transferência provisionada para o ano inteiro.

* **Experimente o Azure Cosmos DB gratuitamente**: o Azure Cosmos DB oferece uma experiência de tempo limitado usando o try Azure Cosmos DB para contas gratuitas. Você pode criar uma conta do Azure Cosmos DB, criar um banco de dados e coleções e executar um aplicativo de exemplo usando os inícios rápidos e os tutoriais. É possível executar o aplicativo de exemplo sem se inscrever em uma conta do Azure ou usar seu cartão de crédito. [Experimente o Microsoft Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) oferece o Azure Cosmos DB por um mês, com a possibilidade de renovar sua conta várias vezes.

* **Emulador de Azure Cosmos DB**: o emulador de Azure Cosmos DB fornece um ambiente local que emula o serviço de Azure Cosmos DB para fins de desenvolvimento. O emulador é oferecido sem custos e com alta fidelidade para o serviço de nuvem. Com o emulador do Azure Cosmos DB, você pode desenvolver e testar seus aplicativos localmente sem criar uma assinatura Azure ou incorrer em custos. Você pode desenvolver seus aplicativos usando o emulador localmente antes de entrar em produção. Depois que estiver satisfeito com a funcionalidade do aplicativo no emulador, você poderá passar a usar a conta do Azure Cosmos DB na nuvem e economizar custos significativamente. Para obter mais informações sobre o emulador, consulte o artigo [Usando o Azure Cosmos DB para desenvolvimento e teste](local-emulator.md).

## <a name="pricing-with-reserved-capacity"></a>Preços com capacidade reserva

Azure Cosmos DB [capacidade reservada](cosmos-db-reserved-capacity.md) ajuda a economizar dinheiro ao usar o modo de taxa de transferência provisionado por meio do pagamento antecipado de Azure Cosmos DB recursos por um ou três anos. É possível reduzir significativamente seus custos com compromissos antecipados de um ano ou três anos e economizar com descontos de 20% a 65% em comparação com o preço regular. A capacidade reserva do Azure Cosmos DB ajuda a reduzir os custos pelo pagamento antecipado da taxa de transferência provisionada (RU/s) por um período de um ou três anos, e você recebe um desconto na taxa de transferência provisionada. 

A capacidade reserva proporciona um desconto de cobrança e não afeta o estado do runtime dos seus recursos do Azure Cosmos DB. A capacidade reserva está disponível consistentemente em todas as APIs, que incluem MongoDB, Cassandra, SQL, Gremlin e as Tabelas do Azure e todas as regiões em todo o mundo. Você pode aprender mais sobre a capacidade reserva no artigo [Pagar antecipadamente por recursos do Azure Cosmos DB com capacidade reserva](cosmos-db-reserved-capacity.md) e comprar capacidade reserva no [portal do Azure](https://portal.azure.com/).

## <a name="next-steps"></a>Próximas etapas

Você pode aprender mais sobre como otimizar os custos de seus recursos do Azure Cosmos DB nos seguintes artigos:

* Aprenda a [Otimizar para desenvolvimento e teste](optimize-dev-test.md)
* Saiba mais sobre [Entender sua cobrança do Azure Cosmos DB](understand-your-bill.md)
* Saiba mais sobre [Otimizando o custo da taxa de transferência](optimize-cost-throughput.md)
* Saiba mais sobre [Otimizando o custo de armazenamento](optimize-cost-storage.md)
* Saiba mais sobre [Otimizando o custo de leituras e gravações](optimize-cost-reads-writes.md)
* Saiba mais sobre [Otimizando o custo de consultas](./optimize-cost-reads-writes.md)
* Saiba mais sobre [Otimizando o custo de contas do Cosmos em várias regiões](optimize-cost-regions.md)
* Saiba mais sobre [Capacidade reservada do Azure Cosmos DB](cosmos-db-reserved-capacity.md)
* Saiba mais sobre o [Emulador do Microsoft Azure Cosmos DB](local-emulator.md)