---
title: Cenários e casos de uso comuns do Azure Cosmos DB
description: 'Saiba mais sobre os cinco principais casos de uso do Azure Cosmos DB: conteúdo gerado pelo usuário, log de eventos, dados de catálogo, dados de preferências do usuário e IoT (Internet das Coisas).'
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: 4aed956fc7052f844080b830aa4cc3b2a07235a1
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104799387"
---
# <a name="common-azure-cosmos-db-use-cases"></a>Casos de uso comuns do Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Este artigo fornece uma visão geral dos vários casos de uso comuns do Azure Cosmos DB.  As recomendações deste artigo servem como ponto de partida ao desenvolver seu aplicativo com o Cosmos DB.

Após ler este artigo, você poderá responder as perguntas a seguir: 

* Quais são os casos de uso comuns para o Azure Cosmos DB?
* Quais são os benefícios do uso do Azure Cosmos DB para aplicativos de varejo?
* Quais são os benefícios do uso do Azure Cosmos DB como um armazenamento de dados para sistemas IoT (Internet das Coisas)?
* Quais são os benefícios do uso do Cosmos DB para aplicativos Web e móveis?

## <a name="introduction"></a>Introdução

[Azure Cosmos DB](../cosmos-db/introduction.md) é o banco de dados NoSQL rápido da Microsoft com APIs abertas para qualquer escala. O serviço foi projetado para permitir aos clientes dimensionar de forma elástica (e independente) a produtividade e o armazenamento em qualquer quantidade de regiões geográficas. O Azure Cosmos DB é o primeiro serviço de banco de dados distribuído globalmente no mercado hoje a oferecer [contratos de nível de serviço](https://azure.microsoft.com/support/legal/sla/cosmos-db/) completos que abrangem produtividade, latência, disponibilidade e consistência.

O Azure Cosmos DB é um multimodelo de banco de dados distribuído globalmente que é usado em uma ampla variedade de aplicativos e casos de uso. É uma boa escolha para qualquer aplicativo [sem servidor](https://azure.com/serverless) que precisa de tempos de resposta baixos na ordem de milissegundos e ser expandido de forma rápida e global. Ele dá suporte a vários modelos de dados (chave-valor, documentos, grafos e colunas) e várias APIs para acesso a dados, incluindo [API do Azure Cosmos DB para MongoDB](mongodb-introduction.md), [API do SQL](./introduction.md), [API do Gremlin](graph-introduction.md) e [API de Tabelas](table-introduction.md) nativamente e de maneira extensível. 

Veja a seguir alguns atributos do Azure Cosmos DB que o tornam adequado para aplicativos de alto desempenho no âmbito global.

* O Azure Cosmos DB particiona os dados nativamente para alta disponibilidade e escalabilidade. O Azure Cosmos DB oferece uma disponibilidade de 99,99% em disponibilidade, débito, latência baixa e consistência para todas as contas de região única e todas as contas de várias regiões com consistência amena e 99,999% de disponibilidade de leitura em todas as contas de banco de dados de várias regiões.
* O Azure Cosmos DB tem armazenamento com suporte de SSD com tempos de resposta de baixa latência na ordem de milissegundos.
* O suporte do Azure Cosmos DB para níveis de consistência (como deterioração restrita, de sessão, de prefixo consistente e eventual) proporciona flexibilidade total e taxas baixas em relação ao custo-desempenho. Nenhum serviço de banco de dados oferece tanta flexibilidade quanto o Azure Cosmos DB na consistência de níveis. 
* O Azure Cosmos DB tem um modelo de preços flexível e amigável aos dados que mede o armazenamento e a produtividade de modo independente.
* O modelo de produtividade reservada do Azure Cosmos DB permite que você pense em termos de número de leituras/gravações em vez de CPU/memória/IOPs do hardware subjacente.
* O design do Azure Cosmos DB permite dimensionar para grandes volumes de solicitações, na ordem de trilhões de solicitações por dia.

Esses atributos são úteis na Web, nos dispositivos móveis, nos jogos e em aplicativos IoT que precisam de tempos de resposta baixos e lidar com grandes volumes de leituras e gravações.

## <a name="iot-and-telematics"></a>IoT e telemático

Casos de uso de IoT normalmente compartilham alguns padrões sobre como consomem, processam e armazenam dados.  Primeiro, esses sistemas precisam consumir picos de dados de sensores de dispositivo em vários locais. Em seguida, esses sistemas processam e analisam dados de streaming para obterem resultados em tempo real. Os dados são arquivados em armazenamento frio para análise em lote. O Microsoft Azure oferece serviços avançados que podem ser aplicados a casos de uso de IoT, incluindo Azure Cosmos DB, hubs de eventos do Azure, Azure Stream Analytics, Hub de notificação do Azure, Azure Machine Learning, Azure HDInsight e Power BI. 

:::image type="content" source="./media/use-cases/iot.png" alt-text="Arquitetura de referência de IoT do Azure Cosmos DB" border="false":::

Picos de dados podem ser processados por Hubs de eventos do Azure que oferecem inclusão de dados de alta taxa de transferência com baixa latência. Dados consumidos que precisem ser processados para obter informações em tempo real poderão ser encaminhados para o Azure Stream Analytics para análise em tempo real. Os dados podem ser carregados no Azure Cosmos DB para consultas ad hoc. Depois que os dados são carregados no Azure Cosmos DB, eles estão prontos para serem consultados. Além disso, os novos dados e as alterações nos dados existentes podem ser lidos no feed de alterações. O feed de alterações é um log persistente, acrescentar somente que armazena alterações em contêineres Cosmos em ordem sequencial. Em seguida, todos os dados ou apenas alterações nos dados no Azure Cosmos DB podem ser usados como dados de referência como parte da análise em tempo real. Além disso, os dados podem ser refinados ainda mais e processados com a conexão dos dados do Azure Cosmos DB com o HDInsight para trabalhos do Pig, do Hive ou trabalhos de Mapear/Reduzir.  Depois, os dados refinados são carregados novamente no Azure Cosmos DB para relatórios.   

Para obter uma solução de IoT de exemplo que usa o Azure Cosmos DB, os Hubs de Eventos e o Storm, veja o [repositório hdinsight-storm-examples no GitHub](https://github.com/hdinsight/hdinsight-storm-examples/).

Para saber mais sobre as ofertas do Azure para IoT, veja [Criar a Internet das Coisas](https://www.microsoft.com/en-us/internet-of-things). 

## <a name="retail-and-marketing"></a>Varejo e marketing
O Azure Cosmos DB é amplamente usado nas próprias plataformas de comércio eletrônico da Microsoft, que executam a Windows Store e o XBox Live. Ele também é usado no setor varejista para armazenar dados de catálogo e para fornecimento de eventos em pipelines de processamento de pedidos.

Os cenários de uso de dados de catálogo envolvem armazenar e consultar um conjunto de atributos para entidades, como pessoas, lugares e produtos. Alguns exemplos de dados de catálogo são contas de usuário, catálogos de produtos, registros de dispositivos IoT e sistemas de listas de materiais. Os atributos desses dados podem variar e podem mudar ao longo do tempo para atender aos requisitos do aplicativo.

Considere um exemplo de um catálogo de produto para um fornecedor de peças automotivas. Todas as partes podem ter seus próprios atributos além dos atributos comuns que todas as partes compartilham. Além disso, atributos de uma parte específica podem mudar no ano seguinte quando um novo modelo é liberado. O Azure Cosmos DB dá suporte a esquemas flexíveis e dados hierárquicos e, portanto, é adequado para armazenar dados de catálogo de produtos.

:::image type="content" source="./media/use-cases/product-catalog.png" alt-text="Arquitetura de referência de catálogo para varejo do Azure Cosmos DB" border="false":::

O Azure Cosmos DB é frequentemente usado para fornecimento de eventos para alimentar arquiteturas orientadas por eventos, usando sua funcionalidade de [feed de alterações](change-feed.md). O feed de alterações fornece a capacidade de ler inserções e atualizações (por exemplo, eventos de pedidos) de forma confiável e incremental feitas em um Azure Cosmos DB, até o nível de microsserviços. Essa funcionalidade pode ser aproveitada para fornecer um repositório persistente de eventos, como um agente de mensagens para eventos de alteração de estado e direcionar o fluxo de trabalho de processamento de pedidos entre muitos microsserviços (que podem ser implementados como [Azure Functions sem servidor](https://azure.com/serverless)).

:::image type="content" source="./media/use-cases/event-sourcing.png" alt-text="Arquitetura de referência de pipeline de ordenação do Azure Cosmos DB" border="false":::

Além disso, os dados armazenados no Azure Cosmos DB podem ser integrados com o HDInsight para análises de Big Data por meio de trabalhos do Apache Spark. Para obter detalhes sobre o Conector do Spark para o Azure Cosmos DB, veja [Executar um trabalho do Spark com o Cosmos DB e o HDInsight](spark-connector.md).

## <a name="gaming"></a>Jogos
A camada de banco de dados é um componente crucial dos aplicativos de jogos. Os jogos modernos executam o processamento gráfico em clientes móveis/console, mas dependem da nuvem para fornecer conteúdo personalizado, como estatísticas de jogos, integração em mídia social e tabelas com as melhores pontuações. Jogos geralmente exigem latências de um milissegundo para leituras e gravações a fim de fornecer uma experiência de jogo envolvente. Um banco de dados de jogo deve ser rápido e ser capaz de lidar com grandes picos de taxas de solicitação durante novos lançamentos de jogos e atualizações de recursos.

O Azure Cosmos DB é usado por jogos como [The Walking Dead: No Man's Land](https://azure.microsoft.com/blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/) da [Next Games](https://www.nextgames.com/) e [Halo 5: Guardians](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/). O Azure Cosmos DB oferece os seguintes benefícios aos desenvolvedores de jogos:

* O Azure Cosmos DB permite que o desempenho seja aumentado ou reduzido de forma elástica. Isso permite que os jogos lidem com a atualização de perfis e estatísticas de dezenas a milhões de jogadores simultâneos fazendo uma única chamada à API.
* O Azure Cosmos DB dá suporte a leituras e gravações em milissegundos para ajudar a evitar retardos durante as partidas.
* A indexação automática do Azure Cosmos DB permite a filtragem em várias propriedades diferentes em tempo real, por exemplo, localização de jogadores por suas IDs internas de jogador ou por suas IDs do GameCenter, do Facebook, do Google, ou ainda a realização de consulta baseada em associação do jogador a uma comunidade. Isso é possível sem a criação de indexação complexa ou infraestrutura de fragmentação.
* Os recursos sociais, incluindo mensagens por bate-papo no jogo, associações a comunidades de jogadores, desafios concluídos, placares com as pontuações mais altas e grafos sociais são mais fáceis de implementar com um esquema flexível.
* O Azure Cosmos DB como uma PaaS (plataforma como serviço) gerenciada exigiu trabalho mínimo de configuração e gerenciamento para permitir a iteração rápida e reduzir o tempo de colocação no mercado.

:::image type="content" source="./media/use-cases/gaming.png" alt-text="Arquitetura de referência de jogos do Azure Cosmos DB" border="false":::

## <a name="web-and-mobile-applications"></a>Aplicativos Web e móveis
O Azure Cosmos DB é normalmente usado em aplicativos Web e móveis e é bastante adequado para modelagem de interações sociais, integração com serviços de terceiros e desenvolvimento de experiências personalizadas avançadas. Os SDKs do Cosmos DB podem ser usados para criar aplicativos avançados do iOS e do Android usando a conhecida [estrutura do Xamarin](mobile-apps-with-xamarin.md).  

### <a name="social-applications"></a>Aplicativos sociais
Um caso de uso comum do Azure Cosmos DB é armazenar e consultar o UGC (conteúdo gerado pelo usuário) de aplicativos Web, móveis e aplicativos de mídia social. Alguns exemplos de UGC são sessões de bate-papo, tweets, postagens em blogs, classificações e comentários. Muitas vezes, o UGC em aplicativos de redes sociais é uma mistura de texto livre, propriedades, rótulos e relações que não se limitam a uma estrutura rígida. Conteúdo como bate-papos, comentários e postagens pode ser armazenado no Cosmos DB sem a necessidade de transformações ou de objetos complexos em camadas de mapeamento relacional.  Propriedades de dados podem ser adicionadas ou modificadas facilmente para atender aos requisitos conforme os desenvolvedores iteram sobre o código do aplicativo, portanto, promovendo um desenvolvimento rápido.  

Aplicativos que se integram a redes sociais de terceiros devem responder às mudanças nos esquemas destas redes. Conforme os dados são indexados automaticamente por padrão no Cosmos DB, eles estão prontos para serem consultados a qualquer momento. Portanto, esses aplicativos têm a flexibilidade para recuperar as projeções de acordo com suas respectivas necessidades.

Muitos dos aplicativos sociais são executados em escala global e podem apresentar os padrões de uso imprevisíveis. Flexibilidade para dimensionar o armazenamento de dados é essencial, uma vez que a camada do aplicativo é dimensionada de acordo com a demanda de uso.  Escale horizontalmente adicionando outras partições de dados em uma conta do Cosmos DB.  Além disso, você também pode criar outras contas do Cosmos DB em várias regiões. Para obter a disponibilidade de regiões do serviço Cosmos DB, consulte [Regiões do Azure](https://azure.microsoft.com/regions/#services).

:::image type="content" source="./media/use-cases/apps-with-global-reach.png" alt-text="Diagrama que mostra a arquitetura de referência do aplicativo Web Azure Cosmos DB." border="false":::

### <a name="personalization"></a>Personalização
Hoje em dia, aplicativos modernos vêm com modos de exibição e experiências mais complexos. Eles são geralmente dinâmicos, atendendo as preferências ou o humor do usuário e as necessidades da marca. Portanto, os aplicativos precisam estar aptos a recuperar configurações personalizadas com eficiência para renderizar experiências e elementos da interface do usuário rapidamente. 

JSON, um formato com suporte no Cosmos DB, é um formato eficiente para representar dados de layout da interface do usuário, já que não é apenas leve, mas também pode ser facilmente interpretado pelo JavaScript. O Cosmos DB oferece níveis de consistência ajustáveis que permitem leituras rápidas com gravações de baixa latência. Portanto, armazenar dados de layout da interface do usuário, incluindo configurações personalizadas, como documentos JSON no Cosmos DB é um meio eficaz de transmitir esses dados.

:::image type="content" source="./media/use-cases/personalization.png" alt-text="Arquitetura de referência de aplicativo Web do Azure Cosmos DB" border="false":::

## <a name="next-steps"></a>Próximas etapas

* Para começar a usar o Azure Cosmos DB, siga nossos [inícios rápidos](create-sql-api-dotnet.md), que mostram um passo a passo para criar uma conta e começar com o Cosmos DB.

* Se você quiser ler mais sobre os clientes usando Azure Cosmos DB, consulte a página [estudos de caso de cliente](https://azure.microsoft.com/case-studies/?service=cosmos-db) .
