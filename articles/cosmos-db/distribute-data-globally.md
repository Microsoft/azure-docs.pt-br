---
title: Distribuir dados globalmente com o Azure Cosmos DB
description: Aprenda sobre a replicação geográfica em escala de planeta, vários mestres, failover e recuperação de dados usando bancos de dados globais do Azure Cosmos DB, um serviço de banco de dados de vários modelos distribuído globalmente.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/18/2019
ms.openlocfilehash: 70ead36e20861026e08e864f438071948c526844
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60889030"
---
# <a name="global-data-distribution-with-azure-cosmos-db---overview"></a>Distribuição de dados global com o Azure Cosmos DB – visão geral

As aplicações de hoje precisam ser altamente responsivas e sempre on-line. Para obter baixa latência e alta disponibilidade, instâncias desses aplicativos precisam ser implantadas em datacenters próximos aos seus usuários. Esses aplicativos geralmente são implantados em vários datacenters e são chamados de distribuídos globalmente. Os aplicativos distribuídos globalmente precisam de um banco de dados distribuído globalmente que possa replicar os dados de maneira transparente em qualquer lugar do mundo, a fim de permitir que os aplicativos operem em uma cópia dos dados próxima de seus usuários. 

O Azure Cosmos DB é um serviço de banco de dados distribuído globalmente projetado para fornecer baixa latência, escalabilidade elástica de taxa de transferência, semântica bem definida para consistência de dados e alta disponibilidade. Em resumo, se seu aplicativo precisa de tempo de resposta garantido de rápido em qualquer lugar no mundo, se ela for necessária para estar sempre online e escalabilidade elástica ilimitada de taxa de transferência e armazenamento, você deve criar seu aplicativo no Azure Cosmos DB.

É possível configurar os bancos de dados para serem distribuídos globalmente e disponíveis em qualquer uma das regiões do Azure. Para reduzir a latência, colocar os dados perto de onde os usuários estão. A escolha das regiões requeridas depende do alcance global de sua aplicação e onde seus usuários estão localizados. O cosmos DB replica de forma transparente os dados para todas as regiões associadas à sua conta do Cosmos. Ele fornece uma única imagem do sistema do banco de dados do Azure Cosmos globalmente distribuído e dos contêineres que o aplicativo pode ler e gravar localmente. 

Com o Azure Cosmos DB, você poderá adicionar ou remover as regiões associadas à sua conta a qualquer momento. Seu aplicativo não precisa ser pausado ou reimplementado para adicionar ou remover uma região. Ela continua a ser altamente disponível o tempo todo por causa dos recursos de hospedagem múltipla que o serviço fornece nativamente.

![Topologia de implantação altamente disponível](./media/distribute-data-globally/deployment-topology.png)

## <a name="key-benefits-of-global-distribution"></a>Principais benefícios da distribuição global

**Compilar aplicativos globais de ativo-ativo.** Com seu protocolo de replicação multimestre novel, todas as regiões dá suporte a leituras e gravações. A funcionalidade de vários mestre também permite:

- Elástico ilimitado gravar e ler a escalabilidade. 
- 99,999% de leitura e gravação de disponibilidade em todo o mundo.
- Garantia de leituras e gravações atendidas em menos de 10 milissegundos no percentil 99.

Usando a hospedagem múltipla do BD Cosmos do Azure e APIs, seu aplicativo está ciente de que a região mais próxima e pode enviar solicitações para essa região. A região mais próxima é identificada sem alterações de configuração. Como adicionar e remover regiões de e para sua conta do Azure Cosmos, seu aplicativo não precisa ser reimplantado ou em pausa, ele continuará a ser altamente disponível em todos os momentos.

**Crie aplicativos altamente responsivos.** Seu aplicativo pode executar quase em tempo real leituras e gravações em relação a todas as regiões que você escolheu para seu banco de dados. Internamente, o Azure Cosmos DB manipula a replicação de dados entre regiões com garantias de nível de consistência do nível que você selecionou.

**Crie aplicativos altamente disponíveis.** Executar um banco de dados em várias regiões em todo o mundo aumenta a disponibilidade de um banco de dados. Se uma região não estiver disponível, outras regiões manipularão automaticamente as solicitações do aplicativo. O Azure Cosmos DB oferece 99,999% de disponibilidade de leitura e gravação para bancos de dados de várias regiões.

**Manter a continuidade dos negócios durante interrupções regionais.** O Azure Cosmos DB dá suporte ao [failover automático](how-to-manage-database-account.md#automatic-failover) durante uma ou mais interrupções regionais. Durante uma indisponibilidade regional, o Azure Cosmos DB continuará mantendo seus SLAs de latência, disponibilidade, consistência e taxa de transferência. Para ajudar a garantir que todo o seu aplicativo seja altamente disponível, o Cosmos DB oferece um failover manual de API para simular uma interrupção regional. Ao usar essa API, você pode executar exercícios regulares de continuidade de negócios.

**Escala de leitura e taxa de transferência globalmente.** Você pode habilitar todas as regiões ser gravável e dimensionem de leituras e gravações em todo o mundo. A taxa de transferência que configura o seu aplicativo em um banco de dados Cosmos do Azure ou o contêiner é garantida para ser entregue em todas as regiões associadas a sua conta do Azure Cosmos. A taxa de transferência provisionada é garantida para cima pela [com suporte financeiro SLAs](https://aka.ms/acdbsla).

**Escolha entre vários modelos de consistência bem definidos.** O protocolo de replicação do Azure Cosmos DB oferece cinco modelos de consistência bem definidos, práticos e intuitivos. Cada modelo tem uma compensação entre consistência e desempenho. Usar esses modelos de consistência permitem que você crie aplicativos distribuídos globalmente com facilidade.

## <a id="Next Steps"></a>Próximas etapas

Leia mais sobre a distribuição global nos seguintes artigos:

* [Distribuição global – nos bastidores](global-dist-under-the-hood.md)
* [Como configurar vários mestres em seus aplicativos](how-to-multi-master.md)
* [Configurar clientes para multihoming](how-to-manage-database-account.md#configure-clients-for-multi-homing)
* [Como adicionar/remover regiões da conta do Azure Cosmos DB](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Criar uma política de resolução de conflitos personalizada para contas da API do SQL](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)