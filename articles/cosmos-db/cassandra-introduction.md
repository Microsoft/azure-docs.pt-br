---
title: Introdução à API do Cassandra do Azure Cosmos DB
description: Saiba como você pode usar o Azure Cosmos DB para fazer lift-and-shift dos aplicativos existentes e criar novos aplicativos usando os drivers do Cassandra e o CQL
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: overview
ms.date: 11/25/2020
ms.openlocfilehash: 2a778c2e9a44bda148fd40112776858943cffe4e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96188608"
---
# <a name="introduction-to-the-azure-cosmos-db-cassandra-api"></a>Introdução à API do Cassandra do Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

A API do Cassandra do Azure Cosmos DB pode ser usada como o armazenamento de dados para aplicativos escritos para o [Apache Cassandra](https://cassandra.apache.org). Isso significa que ao usar os [drivers do Apache](https://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver) em conformidade com CQLv4 existentes, o aplicativo do Cassandra agora pode se comunicar com a API do Cassandra do Azure Cosmos DB. Em muitos casos, é possível alternar entre o uso do Apache Cassandra e da API do Cassandra do Azure Cosmos DB simplesmente alterando uma cadeia de conexão. 

A API do Cassandra permite interagir com os dados armazenados no Azure Cosmos DB usando as ferramentas de CQL (Linguagem de Consulta do Cassandra), baseadas no Cassandra (como cqlsh) e os drivers de cliente do Cassandra com os quais você já está familiarizado.

> [!NOTE]
> O [modo de capacidade sem servidor](serverless.md) já está disponível na API do Cassandra do Azure Cosmos DB.

## <a name="what-is-the-benefit-of-using-apache-cassandra-api-for-azure-cosmos-db"></a>Qual é o benefício de usar a API do Apache Cassandra para o Azure Cosmos DB?

**Sem gerenciamento de operações**: Como um serviço de nuvem totalmente gerenciado, a API do Cassandra do Azure Cosmos DB remove a sobrecarga de gerenciamento e monitoramento de inúmeras configurações em arquivos do sistema operacional, da JVM e yaml e suas interações. O Azure Cosmos DB fornece monitoramento de taxa de transferência, latência, armazenamento e disponibilidade, bem como alertas configuráveis.

**Padrão de software livre**: apesar de ser um serviço totalmente gerenciado, a API do Cassandra ainda dá suporte a uma grande área de superfície do [protocolo de transferência Apache Cassandra](cassandra-support.md) nativo, permitindo que você crie aplicativos em um padrão de software livre amplamente utilizado e independente da nuvem.

**Gerenciamento de desempenho**: O Azure Cosmos DB fornece leituras e gravações de baixa latência no 99º percentil, garantidas pelos SLAs. Os usuários não precisam se preocupar com a sobrecarga operacional para garantir alto desempenho e baixa latência em leituras e gravações. Isso significa que os usuários não precisam lidar com o agendamento de compactação, gerenciamento de marcas de exclusão, configuração filtros de gesto de abrir a mão e as réplicas realizados de forma manual. O Azure Cosmos DB remove a sobrecarga para gerenciar esses problemas e permite que você se concentre na lógica do aplicativo.

**Capacidade de usar o código e as ferramentas existentes**: O Azure Cosmos DB fornece a compatibilidade no nível de protocolo com as ferramentas e os SDKs existentes do Cassandra. Essa compatibilidade garante que você pode usar a base de código existente com a API do Cassandra do Azure Cosmos DB com alterações triviais.

**Elasticidade de armazenamento e taxa de transferência**: O Azure Cosmos DB fornece taxa de transferência em todas as regiões e pode dimensionar a taxa de transferência provisionada com operações no portal do Azure, no PowerShell ou na CLI. É possível [dimensionar elasticamente](manage-scale-cassandra.md) o armazenamento e a taxa de transferência para suas tabelas conforme o necessário e com desempenho previsível.

**Distribuição e disponibilidade globais**: O Azure Cosmos DB oferece a capacidade de distribuir os dados globalmente em todas as regiões do Azure e fornecê-los localmente, garantindo o acesso a dados de baixa latência e alta disponibilidade. O Azure Cosmos DB fornece alta disponibilidade de 99,99% em uma região e disponibilidade de leitura e gravação de 99,999% em várias regiões sem sobrecarga de operações. Saiba mais no artigo [Distribuir dados globalmente](distribute-data-globally.md). 

**Opção de consistência**: O Azure Cosmos DB fornece a opção de cinco níveis de consistência bem-definidos para alcançar a compensação ideal entre consistência e desempenho. Esses níveis de consistência são forte, desatualização limitada, sessão, prefixo consistente e eventual. Esses níveis de consistência bem-definidos, práticos e intuitivos permitem que o desenvolvedor faça compensações precisas entre consistência, disponibilidade e latência. Saiba mais no artigo [Níveis de consistência](consistency-levels.md). 

**Nível empresarial**: O Azure Cosmos DB fornece [certificações de conformidade](https://www.microsoft.com/trustcenter) para garantir que os usuários possam usar a plataforma com segurança. O Azure Cosmos DB também fornece criptografia em repouso e em movimento, firewall de IP e logs de auditoria para atividades do plano de controle.

**Fornecimento de Eventos**: A API do Cassandra fornece acesso a um log de alterações persistente, o [Feed de Alterações](cassandra-change-feed.md), que pode facilitar o fornecimento de eventos diretamente do banco de dados. No Apache Cassandra, o único equivalente é o CDA (captura de dados de alterações), que é meramente um mecanismo para sinalizar tabelas específicas para arquivamento, bem como rejeitar gravações nessas tabelas assim que um tamanho configurável no disco para o log de CDC for atingido (esses recursos são redundantes no Cosmos DB, já que os aspectos relevantes são regidos automaticamente).

## <a name="next-steps"></a>Próximas etapas

* É possível começar rapidamente a compilar os seguintes aplicativos específicos a um idioma para criar e gerenciar dados da API do Cassandra:
  - [Aplicativo Node.js](create-cassandra-nodejs.md)
  - [Aplicativo .NET](create-cassandra-dotnet.md)
  - [Aplicativo Python](create-cassandra-python.md)

* Introdução à [criação de uma conta de API do Cassandra, banco de dados e uma tabela](create-cassandra-api-account-java.md) usando um aplicativo Java.

* [Carregar dados de exemplo na tabela de API do Cassandra](cassandra-api-load-data.md) usando um aplicativo Java.

* [Consultar dados da conta de API do Cassandra](cassandra-api-query-data.md) usando um aplicativo Java.

* Para saber mais sobre os recursos do Apache Cassandra compatíveis com a API do Cassandra do Azure Cosmos DB, confira o artigo [Suporte do Cassandra](cassandra-support.md).

* Leia as [Perguntas frequentes](cassandra-faq.md).
