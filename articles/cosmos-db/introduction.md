---
title: Introdução ao Azure Cosmos DB
description: Saiba mais sobre o Azure Cosmos DB. Este banco de dados multimodelo distribuído globalmente foi criado para alta disponibilidade, escalabilidade elástica e baixa latência e oferece suporte nativo para dados do NoSQL.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: overview
ms.date: 10/23/2020
ms.openlocfilehash: 9df5c63ef175d0cf736ce16036466158ab6565f9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102618297"
---
# <a name="welcome-to-azure-cosmos-db"></a>Bem-vindo ao Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Atualmente, os aplicativos precisam ser altamente responsivos e estar sempre online. Para obter baixa latência e alta disponibilidade, instâncias desses aplicativos precisam ser implantadas em datacenters próximos aos seus usuários. Aplicativos precisam responder em tempo real a grandes alterações no uso no horário de pico, armazenar volumes de dados crescentes e disponibilizar esses dados aos usuários em milissegundos.

O Azure Cosmos DB é um banco de dados NoSQL totalmente gerenciado para um desenvolvimento de aplicativos modernos. Tempos de resposta de milissegundos de dígito único e escalabilidade automática e instantânea garantem a velocidade em qualquer escala. A continuidade dos negócios é garantida com a disponibilidade [com suporte de SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db) e segurança de nível empresarial. O desenvolvimento de aplicativos é mais rápido e produtivo graças à distribuição turnkey de dados de várias regiões em qualquer lugar do mundo, APIs de software livre e SDKs para linguagens populares. Como um serviço totalmente gerenciado, o Azure Cosmos DB tira a administração do banco de dados de suas mãos com o gerenciamento, atualizações e a aplicação de patches automáticos. Ele também lida com o gerenciamento de capacidade com opções econômicas de escala automática e sem servidor que respondem às necessidades do aplicativo para corresponder à capacidade com demanda.

> [!NOTE]
> Deseja ajudar a aprimorar os documentos do Azure Cosmos DB participando de um estudo de usuário? Reserve alguns minutos para preencher esta [pesquisa de triagem](https://aka.ms/cosmosdb-documentation-screener-survey) de cinco minutos. Se você estiver qualificado, será redirecionado para um agendador, no qual poderá reservar uma vaga para ingressar em uma sessão de pesquisa interativa. Nenhum dado pessoal é coletado durante esse processo, de acordo com nossa [política de privacidade](https://go.microsoft.com/fwlink/?LinkId=521839).

Você pode [Experimentar o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem uma assinatura do Azure e sem compromissos ou pode usar a [Camada gratuita do Azure Cosmos DB](optimize-dev-test.md#azure-cosmos-db-free-tier) para obter uma conta com as primeiras 400 RU/s e com 5 GB de armazenamento gratuitos.

> [!div class="nextstepaction"]
> [Experimente o Azure Cosmos DB Gratuitamente](https://azure.microsoft.com/try/cosmosdb/)

:::image type="content" source="./media/introduction/azure-cosmos-db.png" alt-text="O Azure Cosmos DB é um banco de dados NoSQL totalmente gerenciado para um desenvolvimento de aplicativos modernos." border="false":::

## <a name="key-benefits"></a>Principais benefícios

### <a name="guaranteed-speed-at-any-scale"></a>Velocidade garantida em qualquer escala

Obtenha taxa de transferência e velocidade [com suporte de SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db) incomparáveis, acesso global rápido e elasticidade instantânea.

- Acesso em tempo real com latências de leitura e gravação rápidas globalmente e taxa de transferência e consistência com suporte de [SLAs](https://azure.microsoft.com/support/legal/sla/cosmos-db)
- Gravações de várias regiões e distribuição de dados em qualquer região do Azure com o clique de um botão.
- Dimensione de maneira independente e elástica o armazenamento e a taxa de transferência em qualquer região do Azure – mesmo durante picos de tráfego imprevisíveis – para ter uma escala ilimitada no mundo todo.

### <a name="simplified-application-development"></a>Desenvolvimento simplificado de aplicativos

Crie rapidamente usando APIs de software livre, vários SDKs, dados sem esquema e análise sem ETL sobre dados operacionais.

- Profundamente integrado aos principais serviços do Azure usados no desenvolvimento de aplicativos modernos (nativos de nuvem), incluindo o Azure Functions, o Hub IoT, o AKS (Serviço de Kubernetes do Azure), o Serviço de Aplicativo e muito mais.
- Escolha entre várias APIs de banco de dados, incluindo a API de Núcleo (SQL) nativa, o API para MongoDB, a API do Cassandra, a API do Gremlin e a API de Tabela.
- Crie aplicativos na API de Núcleo (SQL) usando as linguagens de sua escolha com SDKs para .NET, Java, Node.js e Python. Ou sua escolha de drivers para qualquer uma das outras APIs de banco de dados.
- Execute análises sem ETL nos dados operacionais quase em tempo real armazenados no Azure Cosmos DB com o Azure Synapse Analytics.
- O feed de alterações torna fácil rastrear e gerenciar alterações em contêineres de banco de dados e criar eventos disparados com o Azure Functions.
- O serviço sem esquema do Azure Cosmos DB indexa automaticamente todos os seus dados, independentemente do modelo de dados, para fornecer consultas extremamente rápidas.

### <a name="mission-critical-ready"></a>Pronto para necessidades críticas

Garanta a continuidade dos negócios, a disponibilidade de 99,999% e a segurança de nível empresarial para cada aplicativo.

- O Azure Cosmos DB oferece um conjunto abrangente de [SLAs](https://azure.microsoft.com/support/legal/sla/cosmos-db), incluindo disponibilidade líder do setor em todo o mundo.
- Distribua facilmente os dados para qualquer região do Azure com a replicação automática de dados. Aproveite o tempo de inatividade zero com gravações de várias regiões ou o RPO 0 quando você usa a coerência forte.
- Aproveite a criptografia de nível empresarial em repouso com chaves autogerenciadas.
- O controle de acesso baseado em função do Azure mantém seus dados seguros e oferece controle refinado.

### <a name="fully-managed-and-cost-effective"></a>Totalmente gerenciado e econômico

Gerenciamento de banco de dados de ponta a ponta, com escala automática e sem servidor que corresponde às necessidades do seu aplicativo e do seu TCO

- Serviço de banco de dados totalmente gerenciado. Automático, sem toque, manutenção, aplicação de patches e atualizações, economizando tempo e dinheiro dos desenvolvedores.
- Opções econômicas para cargas de trabalho imprevisíveis ou esporádicas de qualquer tamanho ou escala, permitindo que os desenvolvedores comecem facilmente sem precisar planejar ou gerenciar a capacidade.
- O modelo sem servidor oferece a cargas de trabalho com picos de uso um serviço automático e responsivo para gerenciar o excesso de tráfego sob demanda.
- A taxa de transferência provisionada de dimensionamento automático escala a capacidade de modo automático e instantâneo para cargas de trabalho imprevisíveis, ao mesmo tempo em que mantém os [SLAs](https://azure.microsoft.com/support/legal/sla/cosmos-db).

## <a name="solutions-that-benefit-from-azure-cosmos-db"></a>Soluções que se beneficiam do Azure Cosmos DB

Qualquer aplicativo [Web, móvel, de jogos e IoT](use-cases.md) que precisa lidar com grandes quantidades de dados lê e escreve em uma escala [global](distribute-data-globally.md) com tempos de resposta quase reais para uma variedade de dados, se beneficiarão da alta disponibilidade, alta taxa de transferência, baixa latência e consistência ajustável [garantidas](https://azure.microsoft.com/support/legal/sla/cosmos-db/) do Cosmos DB. Saiba como o Azure Cosmos DB pode ser usado para criar [IoT e telemática](use-cases.md#iot-and-telematics), [varejo e marketing](use-cases.md#retail-and-marketing), [jogos](use-cases.md#gaming), [aplicativos Web e móveis](use-cases.md#web-and-mobile-applications).

## <a name="next-steps"></a>Próximas etapas

Comece no Azure Cosmos DB com um dos nossos guias de início rápido:

- [Introdução à API de SQL do Azure Cosmos DB](create-sql-api-dotnet.md)
- [Introdução à API do MongoDB do Azure Cosmos DB](create-mongodb-nodejs.md)
- [Introdução à API Cassandra do Azure Cosmos DB](create-cassandra-dotnet.md)
- [Introdução à API do Gremlin do Azure Cosmos DB](create-graph-dotnet.md)
- [Introdução à API de Tabela do Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Experimente o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/)
