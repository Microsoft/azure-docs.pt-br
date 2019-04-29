---
title: Cenários e casos de uso comuns do Azure Cosmos DB
description: 'Saiba mais sobre os cinco principais casos de uso do Azure Cosmos DB: conteúdo gerado pelo usuário, log de eventos, dados de catálogo, dados de preferências do usuário e IoT (Internet das Coisas).'
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 05/07/2017
ms.openlocfilehash: 935c6ff03485c2b8e4d3e2f3df51c740ec1e8371
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60770186"
---
# <a name="common-azure-cosmos-db-use-cases"></a>Casos de uso comuns do Azure Cosmos DB
Este artigo fornece uma visão geral dos vários casos de uso comuns do Azure Cosmos DB.  As recomendações deste artigo servem como ponto de partida ao desenvolver seu aplicativo com o Cosmos DB.   

Após ler este artigo, você poderá responder as perguntas a seguir: 

* Quais são os casos de uso comuns para o Azure Cosmos DB?
* Quais são os benefícios do uso do Azure Cosmos DB para aplicativos de varejo?
* Quais são os benefícios do uso do Azure Cosmos DB como um armazenamento de dados para sistemas IoT (Internet das Coisas)?
* Quais são os benefícios do uso do Cosmos DB para aplicativos Web e móveis?

## <a name="introduction"></a>Introdução
O [Azure Cosmos DB](../cosmos-db/introduction.md) é um serviço de banco de dados distribuído globalmente da Microsoft. O serviço foi projetado para permitir aos clientes dimensionar de forma elástica (e independente) a produtividade e o armazenamento em qualquer quantidade de regiões geográficas. O Azure Cosmos DB é o primeiro serviço de banco de dados distribuído globalmente no mercado hoje a oferecer [contratos de nível de serviço](https://azure.microsoft.com/support/legal/sla/cosmos-db/) completos que abrangem produtividade, latência, disponibilidade e consistência. 

O Azure Cosmos DB é um multimodelo de banco de dados distribuído globalmente que é usado em uma ampla variedade de aplicativos e casos de uso. É uma boa escolha para qualquer aplicativo [sem servidor](https://azure.com/serverless) que precisa de tempos de resposta baixos na ordem de milissegundos e ser expandido de forma rápida e global. Ele dá suporte a vários modelos de dados (chave-valor, documentos, grafos e colunas) e várias APIs para acesso a dados, incluindo [API do Azure Cosmos DB para MongoDB](mongodb-introduction.md), [API do SQL](documentdb-introduction.md), [API do Gremlin](graph-introduction.md) e [API de Tabelas](table-introduction.md) nativamente e de maneira extensível. 

Veja a seguir alguns atributos do Azure Cosmos DB que o tornam adequado para aplicativos de alto desempenho no âmbito global.

* O Azure Cosmos DB particiona os dados nativamente para alta disponibilidade e escalabilidade. O Azure Cosmos DB oferece uma disponibilidade de 99,99% em disponibilidade, débito, latência baixa e consistência para todas as contas de região única e todas as contas de várias regiões com consistência amena e 99,999% de disponibilidade de leitura em todas as contas de banco de dados de várias regiões.
* O Azure Cosmos DB tem armazenamento com suporte de SSD com tempos de resposta de baixa latência na ordem de milissegundos.
* O suporte do Azure Cosmos DB para níveis de consistência (como deterioração restrita, de sessão, de prefixo consistente e eventual) proporciona flexibilidade total e taxas baixas em relação ao custo-desempenho. Nenhum serviço de banco de dados oferece tanta flexibilidade quanto o Azure Cosmos DB na consistência de níveis. 
* O Azure Cosmos DB tem um modelo de preços flexível e amigável aos dados que mede o armazenamento e a produtividade de modo independente.
* O modelo de produtividade reservada do Azure Cosmos DB permite que você pense em termos de número de leituras/gravações em vez de CPU/memória/IOPs do hardware subjacente.
* O design do Azure Cosmos DB permite dimensionar para grandes volumes de solicitações, na ordem de trilhões de solicitações por dia.

Esses atributos são úteis na Web, nos dispositivos móveis, nos jogos e em aplicativos IoT que precisam de tempos de resposta baixos e lidar com grandes volumes de leituras e gravações.

## <a name="iot-and-telematics"></a>IoT e telemático
Casos de uso de IoT normalmente compartilham alguns padrões sobre como consomem, processam e armazenam dados.  Primeiro, esses sistemas precisam consumir picos de dados de sensores de dispositivo em vários locais. Em seguida, esses sistemas processam e analisam dados de streaming para obterem resultados em tempo real. Os dados são arquivados em armazenamento frio para análise em lote. O Microsoft Azure oferece serviços avançados que podem ser aplicados a casos de uso de IoT, incluindo Azure Cosmos DB, Hubs de Eventos do Azure, Stream Analytics do Azure, Hub de Notificação do Azure, Azure Machine Learning, Azure HDInsight e Power BI. 

![Arquitetura de referência de IoT do Azure Cosmos DB](./media/use-cases/iot.png)

Picos de dados podem ser processados por Hubs de eventos do Azure que oferecem inclusão de dados de alta taxa de transferência com baixa latência. Dados consumidos que precisem ser processados para obter informações em tempo real poderão ser encaminhados para o Azure Stream Analytics para análise em tempo real. Os dados podem ser carregados no Azure Cosmos DB para consultas ad hoc. Depois que os dados são carregados no Azure Cosmos DB, eles estão prontos para serem consultados. Além disso, os novos dados e as alterações nos dados existentes podem ser lidos no feed de alterações. O feed de alterações é persistente, acrescente apenas logs que armazenam alterações nos contêineres do Cosmos DB em ordem sequencial. Todos os dados ou somente as alterações nos dados no Azure Cosmos DB podem ser usados como dados de referência como parte da análise em tempo real. Além disso, os dados podem ser refinados ainda mais e processados com a conexão dos dados do Azure Cosmos DB com o HDInsight para trabalhos do Pig, do Hive ou trabalhos de Mapear/Reduzir.  Depois, os dados refinados são carregados novamente no Azure Cosmos DB para relatórios.   

Para obter uma solução de IoT de exemplo que usa o Azure Cosmos DB, os Hubs de Eventos e o Storm, veja o [repositório hdinsight-storm-examples no GitHub](https://github.com/hdinsight/hdinsight-storm-examples/).

Para saber mais sobre as ofertas do Azure para IoT, veja [Criar a Internet das Coisas](https://www.microsoft.com/en-us/internet-of-things). 

## <a name="retail-and-marketing"></a>Varejo e marketing
O Azure Cosmos DB é amplamente usado nas próprias plataformas de comércio eletrônico da Microsoft, que executam a Windows Store e o XBox Live. Ele também é usado no setor varejista para armazenar dados de catálogo e para fornecimento de eventos em pipelines de processamento de pedidos.

Os cenários de uso de dados de catálogo envolvem armazenar e consultar um conjunto de atributos para entidades, como pessoas, lugares e produtos. Alguns exemplos de dados de catálogo são contas de usuário, catálogos de produtos, registros de dispositivos IoT e sistemas de listas de materiais. Os atributos desses dados podem variar e podem mudar ao longo do tempo para atender aos requisitos do aplicativo.

Considere um exemplo de um catálogo de produto para um fornecedor de peças automotivas. Todas as partes podem ter seus próprios atributos além dos atributos comuns que todas as partes compartilham. Além disso, atributos de uma parte específica podem mudar no ano seguinte quando um novo modelo é liberado. O Azure Cosmos DB dá suporte a esquemas flexíveis e dados hierárquicos e, portanto, é adequado para armazenar dados de catálogo de produtos.

![Arquitetura de referência de catálogo para varejo do Azure Cosmos DB](./media/use-cases/product-catalog.png)

O Azure Cosmos DB é frequentemente usado para fornecimento de eventos para alimentar arquiteturas orientadas por eventos, usando sua funcionalidade de [feed de alterações](change-feed.md). O feed de alterações fornece a capacidade de ler inserções e atualizações (por exemplo, eventos de pedidos) de forma confiável e incremental feitas em um Azure Cosmos DB, até o nível de microsserviços. Essa funcionalidade pode ser aproveitada para fornecer um repositório persistente de eventos, como um agente de mensagens para eventos de alteração de estado e direcionar o fluxo de trabalho de processamento de pedidos entre muitos microsserviços (que podem ser implementados como [Azure Functions sem servidor](https://azure.com/serverless)).

![Arquitetura de referência de pipeline de ordenação do Azure Cosmos DB](./media/use-cases/event-sourcing.png)

Além disso, os dados armazenados no Azure Cosmos DB podem ser integrados com o HDInsight para análises de Big Data por meio de trabalhos do Apache Spark. Para obter detalhes sobre o Conector do Spark para o Azure Cosmos DB, veja [Executar um trabalho do Spark com o Cosmos DB e o HDInsight](spark-connector.md).

## <a name="gaming"></a>Jogos
A camada de banco de dados é um componente essencial dos aplicativos de jogos. Os jogos modernos executam o processamento gráfico em clientes móveis/console, mas dependem da nuvem para fornecer conteúdo personalizado, como estatísticas de jogos, integração em mídia social e tabelas com as melhores pontuações. Jogos geralmente exigem latências de um milissegundo para leituras e gravações a fim de fornecer uma experiência de jogo envolvente. Um banco de dados de jogo deve ser rápido e ser capaz de lidar com grandes picos de taxas de solicitação durante novos lançamentos de jogos e atualizações de recursos.

O Azure Cosmos DB é usado por jogos como [The Walking Dead: No Man's Land](https://azure.microsoft.com/blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/) da [Next Games](https://www.nextgames.com/) e [Halo 5: Guardians](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/). O Azure Cosmos DB oferece os seguintes benefícios aos desenvolvedores de jogos:

* O Azure Cosmos DB permite que o desempenho seja aumentado ou reduzido de forma elástica. Isso permite que os jogos lidem com a atualização de perfis e estatísticas de dezenas a milhões de jogadores simultâneos fazendo uma única chamada à API.
* O Azure Cosmos DB dá suporte a leituras e gravações em milissegundos para ajudar a evitar retardos durante as partidas.
* A indexação automática do Azure Cosmos DB permite a filtragem em várias propriedades diferentes em tempo real, por exemplo, localização de jogadores por suas IDs internas de jogador ou por suas IDs do GameCenter, do Facebook, do Google, ou ainda a realização de consulta baseada em associação do jogador a uma comunidade. Isso é possível sem a criação de indexação complexa ou infraestrutura de fragmentação.
* Os recursos sociais, incluindo mensagens por bate-papo no jogo, associações a comunidades de jogadores, desafios concluídos, placares com as pontuações mais altas e grafos sociais são mais fáceis de implementar com um esquema flexível.
* O Azure Cosmos DB como uma PaaS (plataforma como serviço) gerenciada exigiu trabalho mínimo de configuração e gerenciamento para permitir a iteração rápida e reduzir o tempo de colocação no mercado.

![Arquitetura de referência de jogos do Azure Cosmos DB](./media/use-cases/gaming.png)

## <a name="web-and-mobile-applications"></a>Aplicativos Web e móveis
O Azure Cosmos DB é normalmente usado em aplicativos Web e móveis e é bastante adequado para modelagem de interações sociais, integração com serviços de terceiros e desenvolvimento de experiências personalizadas avançadas. Os SDKs do Cosmos DB podem ser usados para criar aplicativos avançados do iOS e do Android usando a conhecida [estrutura do Xamarin](mobile-apps-with-xamarin.md).  

### <a name="social-applications"></a>Aplicativos sociais
Um caso de uso comum do Azure Cosmos DB é armazenar e consultar o UGC (conteúdo gerado pelo usuário) de aplicativos Web, móveis e aplicativos de mídia social. Alguns exemplos de UGC são sessões de bate-papo, tweets, postagens em blogs, classificações e comentários. Muitas vezes, o UGC em aplicativos de redes sociais é uma mistura de texto livre, propriedades, rótulos e relações que não se limitam a uma estrutura rígida. Conteúdo como bate-papos, comentários e postagens pode ser armazenado no Cosmos DB sem a necessidade de transformações ou de objetos complexos em camadas de mapeamento relacional.  Propriedades de dados podem ser adicionadas ou modificadas facilmente para atender aos requisitos conforme os desenvolvedores iteram sobre o código do aplicativo, portanto, promovendo um desenvolvimento rápido.  

Aplicativos que se integram a redes sociais de terceiros devem responder às mudanças nos esquemas destas redes. Conforme os dados são indexados automaticamente por padrão no Cosmos DB, eles estão prontos para serem consultados a qualquer momento. Portanto, esses aplicativos têm a flexibilidade para recuperar as projeções de acordo com suas respectivas necessidades.

Muitos dos aplicativos sociais são executados em escala global e podem apresentar os padrões de uso imprevisíveis. Flexibilidade para dimensionar o armazenamento de dados é essencial, uma vez que a camada do aplicativo é dimensionada de acordo com a demanda de uso.  Escale horizontalmente adicionando outras partições de dados em uma conta do Cosmos DB.  Além disso, você também pode criar outras contas do Cosmos DB em várias regiões. Para obter a disponibilidade de regiões do serviço Cosmos DB, consulte [Regiões do Azure](https://azure.microsoft.com/regions/#services).

![Arquitetura de referência de aplicativo Web do Azure Cosmos DB](./media/use-cases/apps-with-global-reach.png)

### <a name="personalization"></a>Personalização
Hoje em dia, aplicativos modernos vêm com modos de exibição e experiências mais complexos. Eles são geralmente dinâmicos, atendendo as preferências ou o humor do usuário e as necessidades da marca. Portanto, os aplicativos precisam estar aptos a recuperar configurações personalizadas com eficiência para renderizar experiências e elementos da interface do usuário rapidamente. 

JSON, um formato com suporte no Cosmos DB, é um formato eficiente para representar dados de layout da interface do usuário, já que não é apenas leve, mas também pode ser facilmente interpretado pelo JavaScript. O Cosmos DB oferece níveis de consistência ajustáveis que permitem leituras rápidas com gravações de baixa latência. Portanto, armazenar dados de layout da interface do usuário, incluindo configurações personalizadas, como documentos JSON no Cosmos DB é um meio eficaz de transmitir esses dados.

![Arquitetura de referência de aplicativo Web do Azure Cosmos DB](./media/use-cases/personalization.png)

## <a name="next-steps"></a>Próximas etapas
Para começar a usar o Azure Cosmos DB, siga nossos [inícios rápidos](create-sql-api-dotnet.md), que mostram um passo a passo para criar uma conta e começar com o Cosmos DB. 

Ou se desejar ler mais sobre os clientes que usam o Cosmos DB, os seguintes relatos de clientes estão disponíveis:

* [Jet.com](https://jet.com). Rivais de comércio eletrônico de olho no sucesso executam o Microsoft Cloud e utilizam o Cosmos DB em uma escala global.
* [Asos.com](https://www.asos.com/). Asos.com é uma loja online de produtos de beleza e moda do Reino Unido. Voltada principalmente para jovens adultos, a Asos vende mais de 850 marcas, bem como sua própria linha de roupas e acessórios.
* [Toyota](https://www.toyota.com/). A Toyota Motor Corporation é um fabricante automotivo japonês. A Toyota utilizou o Cosmos DB para um aplicativo de IoT global.
* [Citrix](https://customers.microsoft.com/story/citrix). A Citrix desenvolve uma solução de logon único usando o Azure Service Fabric e o Azure Cosmos DB
* [TEXA](https://customers.microsoft.com/story/texaspa) A solução de IoT revolucionária da TEXA para proprietários de veículos ajuda a economizar tempo, dinheiro e combustível – e possivelmente, vidas.
* [Domino's Pizza](https://www.dominos.com). A Domino's Pizza Inc. é uma cadeia de restaurantes especializados em pizza dos EUA.
* [Johnson Controls](https://www.johnsoncontrols.com). A Johnson Controls é líder global em vários setores e tecnologias diversificadas que atende a uma ampla variedade de clientes em mais de 150 países.
* [Microsoft Windows, Universal Store, Hub IoT do Azure, Xbox Live e outros serviços de escala da Internet](https://azure.microsoft.com/blog/how-azure-documentdb-planet-scale-nosql-helps-run-microsoft-s-own-businesses/). Como a Microsoft cria serviços altamente escalonáveis usando o Azure Cosmos DB.
* [Equipe de Dados e Análise da Microsoft](https://customers.microsoft.com/story/microsoftdataandanalytics). A equipe de Dados e Análise da Microsoft obtém uma coleção de Big Data de escala mundial com o Azure Cosmos DB
* [Sulekha.com](https://customers.microsoft.com/story/sulekha-uses-azure-documentdb-to-connect-customers-and-businesses-across-india). A Sulekha usa o Azure Cosmos DB para conectar clientes e empresas em toda a Índia.
* [NewOrbit](https://customers.microsoft.com/story/neworbit-takes-flight-with-azure-documentdb). A NewOrbit decola com o Azure Cosmos DB.
* [Affinio](https://customers.microsoft.com/doclink/affinio-switches-from-aws-to-azure-documentdb-to-harness-social-data-at-scale). A Affinio alterna entre o AWS e o Azure Cosmos DB para aproveitar dados sociais em grande escala.
* [Next Games](https://azure.microsoft.com//blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/). The Walking Dead: No Man's Land alcança o primeiro lugar com o apoio do Azure Cosmos DB.
* [Halo](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/). Como a Halo 5 implementou partidas sociais usando o Azure Cosmos DB.
* [Cortana Analytics Gallery](https://azure.microsoft.com/blog/cortana-analytics-gallery-a-scalable-community-site-built-on-azure-documentdb/). Galeria do Cortana Analytics – um site da comunidade escalonável criado com base no Azure Cosmos DB.
* [Breeze](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18602). Integrador líder oferece a empresas multinacionais informações globais em minutos com as tecnologias de nuvem flexíveis.
* [News Republic](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18639). Adicionando inteligência às notícias para fornecer informações com a finalidade de engajar cidadãos. 
* [SGS International](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18653). Para obter cores consistentes em todo o mundo, grandes marcas se voltam para a SGS. E a SGS se volta para o Azure.
* [Telenor](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18608). O líder global Telenor usa a nuvem para se mover com a velocidade de uma start-up. 
* [XOMNI](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18667). A loja do futuro utiliza pesquisa rápida e um fluxo de dados fácil.
* [Nucleo](https://customers.microsoft.com/story/azure-based-software-platform-breaks-down-barriers-bet). Plataforma de software com base no Azure quebra as barreiras entre as empresas e os clientes
* [Weka](https://customers.microsoft.com/story/weka-smart-fridge-improves-vaccine-management-so-more-people-can-be-protected-against-diseases). O Refrigerador Inteligente da Weka aprimora o gerenciamento de vacinas para que mais pessoas podem ser protegidas contra doenças
* [Orange Tribes](https://customers.microsoft.com/story/theres-more-to-that-food-app-than-meets-the-eye-or-the-mouth). Esse aplicativo de comida reserva muito mais do os que olhos, ou a boca, podem sentir.
* [Real Madrid](https://customers.microsoft.com/story/real-madrid-brings-the-stadium-closer-to-450-million-f). Com o Microsoft Cloud, é como se o Real Madrid levasse 450 milhões de fãs do mundo inteiro para seu estádio.
* [Tuku](https://customers.microsoft.com/story/tuku-makes-car-buying-fun-with-help-from-azure-services). TUKU torna a compra de carros mais divertida com a Ajuda dos serviços do Azure
