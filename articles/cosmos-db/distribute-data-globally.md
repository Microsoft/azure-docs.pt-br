---
title: Distribuir dados globalmente com o Azure Cosmos DB
description: Aprenda sobre a replicação geográfica em escala de planeta, vários mestres, failover e recuperação de dados usando bancos de dados globais do Azure Cosmos DB, um serviço de banco de dados de vários modelos distribuído globalmente.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: 4f17fc7df5aef449c3b0f6dd8d02ae58df959070
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68384895"
---
# <a name="global-data-distribution-with-azure-cosmos-db---overview"></a>Distribuição de dados global com o Azure Cosmos DB – visão geral

As aplicações de hoje precisam ser altamente responsivas e sempre on-line. Para obter baixa latência e alta disponibilidade, instâncias desses aplicativos precisam ser implantadas em datacenters próximos aos seus usuários. Esses aplicativos geralmente são implantados em vários datacenters e são chamados de distribuídos globalmente. Os aplicativos distribuídos globalmente precisam de um banco de dados distribuído globalmente que possa replicar os dados de maneira transparente em qualquer lugar do mundo, a fim de permitir que os aplicativos operem em uma cópia dos dados próxima de seus usuários. 

O Azure Cosmos DB é um serviço de banco de dados distribuído globalmente projetado para fornecer baixa latência, escalabilidade elástica de taxa de transferência, semântica bem definida para consistência de dados e alta disponibilidade. Resumindo, se seu aplicativo precisar de tempo de resposta rápido garantido em qualquer lugar do mundo, se for necessário estar sempre online e precisar de escalabilidade ilimitada e elástica de taxa de transferência e armazenamento, você deverá criar seu aplicativo em Azure Cosmos DB.

É possível configurar os bancos de dados para serem distribuídos globalmente e disponíveis em qualquer uma das regiões do Azure. Para diminuir a latência, coloque os dados próximos de onde os usuários estão. A escolha das regiões requeridas depende do alcance global de sua aplicação e onde seus usuários estão localizados. Cosmos DB replica os dados de forma transparente para todas as regiões associadas à sua conta do cosmos. Ele fornece uma única imagem do sistema do banco de dados do Azure Cosmos globalmente distribuído e dos contêineres que o aplicativo pode ler e gravar localmente. 

Com o Azure Cosmos DB, você poderá adicionar ou remover as regiões associadas à sua conta a qualquer momento. Seu aplicativo não precisa ser pausado ou reimplementado para adicionar ou remover uma região. Ele continua sendo altamente disponível o tempo todo devido aos recursos de hospedagem múltipla que o serviço fornece nativamente.

![Topologia de implantação altamente disponível](./media/distribute-data-globally/deployment-topology.png)

## <a name="key-benefits-of-global-distribution"></a>Principais benefícios da distribuição global

**Compilar aplicativos globais de ativo-ativo.** Com seu próprio protocolo de replicação de vários mestres, cada região dá suporte a gravações e leituras. A funcionalidade de vários mestres também permite:

- Gravação e escalabilidade de leitura elástica ilimitada. 
- 99,999% de leitura e gravação de disponibilidade em todo o mundo.
- Garantia de leituras e gravações atendidas em menos de 10 milissegundos no percentil 99.

Usando o Azure Cosmos DB APIs de hospedagem múltipla, seu aplicativo está ciente da região mais próxima e pode enviar solicitações para essa região. A região mais próxima é identificada sem alterações de configuração. À medida que você adiciona e remove regiões de e para sua conta do Azure Cosmos, seu aplicativo não precisa ser reimplantado ou pausado, ele continua altamente disponível em todos os momentos.

**Crie aplicativos altamente responsivos.** Seu aplicativo pode executar leituras e gravações quase em tempo real em todas as regiões escolhidas para o banco de dados. O Azure Cosmos DB manipula internamente a replicação de dados entre regiões com garantias de nível de consistência do nível que você selecionou.

**Crie aplicativos altamente disponíveis.** A execução de um banco de dados em várias regiões em todo o mundo aumenta a disponibilidade de um banco de dados. Se uma região não estiver disponível, outras regiões manipularão automaticamente as solicitações do aplicativo. O Azure Cosmos DB oferece 99,999% de disponibilidade de leitura e gravação para bancos de dados de várias regiões.

**Manter a continuidade dos negócios durante interrupções regionais.** O Azure Cosmos DB dá suporte ao [failover automático](how-to-manage-database-account.md#automatic-failover) durante uma ou mais interrupções regionais. Durante uma indisponibilidade regional, o Azure Cosmos DB continuará mantendo seus SLAs de latência, disponibilidade, consistência e taxa de transferência. Para ajudar a garantir que todo o seu aplicativo esteja altamente disponível, o Cosmos DB oferece uma API de failover manual para simular uma interrupção regional. Ao usar essa API, você pode executar exercícios regulares de continuidade de negócios.

**Escala de leitura e taxa de transferência globalmente.** Você pode habilitar todas as regiões para serem graváveis e dimensionar de forma elástica as leituras e gravações em todo o mundo. A taxa de transferência que seu aplicativo configura em um banco de dados Cosmos do Azure ou um contêiner é garantida para ser entregue em todas as regiões associadas à sua conta do Azure Cosmos. A taxa de transferência provisionada é garantida por [SLAs com suporte financeiro](https://aka.ms/acdbsla).

**Escolha entre vários modelos de consistência bem definidos.** O protocolo de replicação do Azure Cosmos DB oferece cinco modelos de consistência bem definidos, práticos e intuitivos. Cada modelo tem uma compensação entre consistência e desempenho. Usar esses modelos de consistência permitem que você crie aplicativos distribuídos globalmente com facilidade.

## <a id="Next Steps"></a>Próximas etapas

Leia mais sobre a distribuição global nos seguintes artigos:

* [Distribuição global – nos bastidores](global-dist-under-the-hood.md)
* [Como configurar vários mestres em seus aplicativos](how-to-multi-master.md)
* [Configurar clientes para multihoming](how-to-manage-database-account.md#configure-multiple-write-regions)
* [Como adicionar/remover regiões da conta do Azure Cosmos DB](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Criar uma política de resolução de conflitos personalizada para contas da API do SQL](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)
* [Modelos de consistência programáveis no Cosmos DB](consistency-levels.md)
* [Escolher o nível certo de coerência para o seu aplicativo](consistency-levels-choosing.md)
* [Níveis de coerência entre as APIs do Azure Cosmos DB](consistency-levels-across-apis.md)
* [Equilíbrio entre disponibilidade e desempenho para vários níveis de coerência](consistency-levels-tradeoffs.md)
* [Como implementar a sincronização personalizada para otimizar para maior disponibilidade e desempenho](how-to-custom-synchronization.md)
