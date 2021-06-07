---
title: Encargos de unidade de solicitação para o Azure Cosmos DB como um repositório de valor de chave
description: Saiba mais sobre os encargos da unidade de solicitação do Azure Cosmos DB para operações simples de gravação e leitura quando ele é usado como um repositório de chaves/valores.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 08/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 9354ae0a22ef2e8ab4ee6a57563d3f3c4c8e4547
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93339288"
---
# <a name="azure-cosmos-db-as-a-key-value-store--cost-overview"></a>Azure Cosmos DB como um repositório de valor de chave – visão geral do custo
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

O Azure Cosmos DB é um serviço de multimodelo de banco de dados distribuído globalmente para a criação fácil de aplicativos altamente disponíveis de grande escala. Por padrão, Azure Cosmos DB indexa de forma automática e eficiente todos os dados ingeridos. Isso permite consultas [SQL](./sql-query-getting-started.md) (e [JavaScript](stored-procedures-triggers-udfs.md)) rápidas e consistentes nos dados. 

Este artigo descreve o custo do Azure Cosmos DB para operações simples de gravação e leitura quando ele é usado como um repositório de chaves/valores. As operações de gravação incluem inserções, substituições, exclusões e upserts de itens de dados. Além de garantir um SLA de disponibilidade de 99,999% para todas as contas de várias regiões, Azure Cosmos DB oferece garantia de <latência de 10 ms para leituras e para as gravações (indexadas), no 99 º percentil. 

## <a name="why-we-use-request-units-rus"></a>Por que usamos RUs (Unidades de Solicitação)

Azure Cosmos DB desempenho é baseado na quantidade de produtividade provisionada expressa em [unidades de solicitação](request-units.md) (ru/s). O provisionamento é de uma granularidade de segundo e é comprado em RU/s ([não deve ser confundido com a cobrança por hora](https://azure.microsoft.com/pricing/details/cosmos-db/)). RUs deve ser considerado uma abstração lógica (uma moeda) que simplifica o provisionamento da taxa de transferência necessária para o aplicativo. Os usuários não precisam considerar a diferenciação da taxa de transferência de leitura e gravação. O modelo de moeda única de RUs cria eficiência no compartilhamento da capacidade provisionada entre leituras e gravações. Esse modelo de capacidade provisionado permite que o serviço forneça uma **taxa de transferência previsível e consistente, baixa latência garantida e alta disponibilidade**. Finalmente, embora o modelo de RU seja usado para representar a taxa de transferência, cada RU provisionado também tem uma quantidade definida de recursos (por exemplo, memória, núcleos/CPU e IOPS).

Como um sistema de banco de dados distribuído globalmente, Cosmos DB é o único serviço do Azure que fornece SLAs abrangentes que abrangem latência, taxa de transferência, consistência e alta disponibilidade. A taxa de transferência que você provisiona é aplicada a cada uma das regiões associadas à sua conta do cosmos. Para leituras, o Cosmos DB oferece vários [níveis de consistência](consistency-levels.md) bem definidos para sua escolha. 

A tabela a seguir mostra o número de RUs necessário para executar operações de leitura e gravação com base em um item de dados de tamanho de 1 KB e 100 KBs com a indexação automática padrão desativada. 

|Tamanho do Item|1 Leitura|1 Gravação|
|-------------|------|-------|
|1 KB|1 RU|5 RUs|
|100 KB|10 RUs|50 RUs|

## <a name="cost-of-reads-and-writes"></a>Custo de leituras e gravações

Se você provisionar 1.000 RU/s, esse valor será de 3,6 milhões RU/hora e custará $0.08 pela hora (nos EUA e na Europa). Para um item de dados de tamanho de 1 KB, isso significa que você pode consumir 3,6 milhões leituras ou 720.000 gravações (3,6 milhões RU/5) usando sua taxa de transferência provisionada. Normalizado para milhões de leituras e gravações, o custo seria de $0.22/milhão de leituras ($0.08/3,6) e $0.111/milhão de gravações ($0.08/0,72). O custo por milhão se torna mínimo, conforme mostra a tabela a seguir.

|Tamanho do Item|Custo de 1 milhão leituras|Custo de gravações de 1 milhão|
|-------------|-------|--------|
|1 KB|$ 0,022|$ 0,111|
|100 KB|$ 0,222|$ 1,111|


A maioria dos repositórios de blob ou de objeto básicos cobra $ 0,40 por um milhão de transações de leitura, e $ 5 por um milhão de transações de gravação. Se usado de forma ideal, Cosmos DB pode ser de até 98% mais barato do que essas outras soluções (para transações de 1 KB).

## <a name="next-steps"></a>Próximas etapas

* Use a [calculadora de ru](https://cosmos.azure.com/capacitycalculator/) para estimar a taxa de transferência para suas cargas de trabalho.