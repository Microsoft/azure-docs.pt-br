---
title: Noções básicas sobre as diferenças entre Azure Cosmos DB NoSQL e bancos de dados relacionais
description: Este artigo enumera as diferenças entre o NoSQL e os bancos de dados relacionais
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/16/2019
ms.reviewer: sngun
ms.openlocfilehash: d986106337eb1ede2f6d61303d8a4c487bbed276
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93088464"
---
# <a name="understanding-the-differences-between-nosql-and-relational-databases"></a>Compreendendo as diferenças entre o NoSQL e os bancos de dados relacionais
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Este artigo enumerará alguns dos principais benefícios dos bancos de dados NoSQL em bancos de dados relacionais. Também discutiremos alguns dos desafios em trabalhar com o NoSQL. Para obter uma análise detalhada dos diferentes armazenamentos de dados que existem, confira nosso artigo sobre como [escolher o armazenamento de dados correto](/azure/architecture/guide/technology-choices/data-store-overview).

## <a name="high-throughput"></a>Alta taxa de transferência

Um dos desafios mais óbvios ao manter um sistema de banco de dados relacional é que a maioria dos mecanismos relacionais aplica bloqueios e travas para impor [semântica ACID](https://en.wikipedia.org/wiki/ACID)estrita. Essa abordagem tem benefícios em termos de garantir um estado de dados consistente no banco de dado. No entanto, há compensações pesadas em relação à simultaneidade, latência e disponibilidade. Devido a essas restrições de arquitetura fundamentais, altos volumes transacionais podem resultar na necessidade de fragmentar manualmente os dados. Implementar a fragmentação manual pode ser um exercício demorado e trabalhoso.

Nesses cenários, [os bancos de dados distribuídos](https://en.wikipedia.org/wiki/Distributed_database) podem oferecer uma solução mais escalonável. No entanto, a manutenção ainda pode ser um exercício dispendioso e demorado. Os administradores podem precisar fazer um trabalho extra para garantir que a natureza distribuída do sistema seja transparente. Eles também podem precisar considerar a natureza "desconectada" do banco de dados.

A [Azure Cosmos DB](./introduction.md) simplifica esses desafios, ao ser implantado em todo o mundo em todas as regiões do Azure. Os intervalos de partição podem ser subdivididos dinamicamente para aumentar diretamente o banco de dados em linha com o aplicativo, mantendo simultaneamente a alta disponibilidade. A gestão de recursos nativa de nuvem refinada e rigidamente controlada facilita as [garantias de latência impressionante](./consistency-levels.md#consistency-levels-and-latency) e o desempenho previsível. O particionamento é totalmente gerenciado, de modo que os administradores não precisam escrever código nem gerenciar partições.

Se seus volumes transacionais estiverem atingindo níveis extremos, como muitos milhares de transações por segundo, você deverá considerar um banco de dados NoSQL distribuído. Considere Azure Cosmos DB para obter máxima eficiência, facilidade de manutenção e redução do custo total de propriedade.

:::image type="content" source="./media/relational-or-nosql/backend-scaled.png" alt-text="Back-end" border="false":::

## <a name="hierarchical-data"></a>Dados hierárquicos

Há um número significativo de casos de uso em que as transações no banco de dados podem conter muitas relações pai-filho. Essas relações podem crescer significativamente ao longo do tempo e se comprovam difícil de gerenciar. Formulários de [bancos de dados hierárquicos](https://en.wikipedia.org/wiki/Hierarchical_database_model) surgiram durante os anos 80, mas não eram populares devido à ineficiência no armazenamento. Eles também perderam a força, pois o [modelo relacional de Ted Codd](https://en.wikipedia.org/wiki/Relational_model) se tornou o padrão de fato usado por praticamente todos os sistemas de gerenciamento de banco de dados básicos.

No entanto, hoje a popularidade dos bancos de dados em estilo de documento cresceu significativamente. Esses bancos de dados podem ser considerados uma reinventação do paradigma hierárquico do banco de dados, agora desinibido por preocupações com o custo de armazenamento de dados em disco. Como resultado, a manutenção de muitas relações de entidades pai-filho complexas em um banco de dados relacional agora poderia ser considerada um antipadrão em comparação com as abordagens modernas orientadas a documentos.

O surgimento do [design orientado a objeto](https://en.wikipedia.org/wiki/Object-oriented_design)e a [incompatibilidade de impedância](https://en.wikipedia.org/wiki/Object-relational_impedance_mismatch) que surge ao combiná-lo com modelos relacionais, também realça um antipadrão em bancos de dados relacionais para determinados casos de uso. Os custos de manutenção ocultos, mas geralmente podem surgir como resultado. Embora as [abordagens de ORM](https://en.wikipedia.org/wiki/Object-relational_mapping) tenham evoluído para reduzir parcialmente isso, os bancos de dados orientados a documentos, apesar de serem Unidos muito melhor com abordagens orientadas a objeto. Com essa abordagem, os desenvolvedores não são forçados a serem confirmados em drivers de ORM ou [mecanismos de banco de dados OO](https://en.wikipedia.org/wiki/Object_database)específicos à linguagem do bespoke. Se seus dados contiverem muitas relações pai-filho e níveis mais amplos de hierarquia, convém considerar o uso de um banco de dados de documento NoSQL, como o [Azure Cosmos DB API do SQL](./introduction.md).

:::image type="content" source="./media/relational-or-nosql/order-orderdetails.jpg" alt-text="OrderDetails":::

## <a name="complex-networks-and-relationships"></a>Redes e relacionamentos complexos

Ironicamente, considerando seu nome, os bancos de dados relacionais apresentam uma solução inferior à ideal para modelar relações profundas e complexas. A razão para isso é que as relações entre as entidades não existem de fato em um banco de dados relacional. Eles precisam ser computados em tempo de execução, com relações complexas que exigem junções cartesianas para permitir o mapeamento usando consultas. Como resultado, as operações se tornam exponencialmente mais caras em termos de computação à medida que as relações aumentam. Em alguns casos, um banco de dados relacional tentando gerenciar essas entidades se tornará inutilizável.

Várias formas de bancos de dados de "rede" surgiram durante o tempo de surgimento dos bancos de dados relacionais, mas, como acontece com bancos de dados hierárquicos, esses sistemas lutavam para obter popularidade. A adoção lenta ocorreu devido a uma falta de casos de uso no momento e ineficiências de armazenamento. Hoje, os mecanismos de banco de dados do grafo podem ser considerados um novo surgimento do paradigma do banco de dados de rede. O principal benefício com esses sistemas é que as relações são armazenadas como "cidadãos de primeira classe" no banco de dados. Assim, as relações de atravessamento podem ser feitas em constante tempo, em vez de aumentar a complexidade do tempo com cada nova junção ou produto cruzado.

Se você estiver mantendo uma rede complexa de relações em seu banco de dados, convém considerar um banco de dados de grafo, como a [API do Azure Cosmos DB Gremlin](./graph-introduction.md) para o gerenciamento desse dado.

:::image type="content" source="./media/relational-or-nosql/graph.png" alt-text="Diagrama de banco de dados mostra vários funcionários e departamentos conectados entre si.":::

Azure Cosmos DB é um serviço de banco de dados multimodelo, que oferece uma projeção de API para todos os principais tipos de modelo NoSQL; Coluna-família, documento, gráfico e valor-chave. As camadas de API do documento [Gremlin (grafo)](./gremlin-support.md) e SQL (núcleo) são totalmente interoperáveis. Isso tem benefícios para alternar entre modelos diferentes no nível de programação. Os repositórios de grafo podem ser consultados em termos de atravessamentos de rede complexos, bem como transações modeladas como registros de documento no mesmo armazenamento.

## <a name="fluid-schema"></a>Esquema fluido

Outra característica específica de bancos de dados relacionais é que os esquemas devem ser definidos em tempo de design. Isso tem benefícios em termos de integridade referencial e conformidade dos dados. No entanto, ele também pode ser restritivo à medida que o aplicativo cresce. Responder a alterações no esquema por meio de modelos separados logicamente que compartilham a mesma tabela ou definição de banco de dados pode se tornar complexo ao longo do tempo. Esses casos de uso geralmente se beneficiam do esquema que está sendo devolveddo para o aplicativo gerenciar de acordo com cada registro. Isso exige que o banco de dados seja "independente do esquema" e permita que os registros sejam "autodescritivos" em termos dos dados contidos neles.

Se você estiver gerenciando dados cujas estruturas estão em constante mudança a uma taxa alta, particularmente se as transações puderem ser provenientes de fontes externas em que é difícil impor a conformidade no banco de dados, talvez você queira considerar uma abordagem mais independente de esquema usando um serviço de banco de dados NoSQL gerenciado como Azure Cosmos DB.

## <a name="microservices"></a>Microsserviços

O padrão de [microserviços](https://en.wikipedia.org/wiki/Microservices) cresceu significativamente nos últimos anos. Esse padrão tem suas raízes na [arquitetura orientada a serviços](https://en.wikipedia.org/wiki/Service-oriented_architecture). O padrão de fato para a transmissão de dados nessas arquiteturas modernas de microserviço é o [JSON](https://en.wikipedia.org/wiki/JSON), que também é o meio de armazenamento para a grande maioria dos bancos de dados NoSQL orientados a documentos. Isso faz com que o documento NoSQL armazene um ajuste muito mais uniforme para a persistência e a sincronização (usando [padrões de fornecimento de eventos](https://en.wikipedia.org/wiki/Event-driven_architecture)) em implementações complexas de microatendimento. Bancos de dados relacionais mais tradicionais podem ser muito mais complexos de manter nessas arquiteturas. Isso ocorre devido à maior quantidade de transformação necessária para o estado e a sincronização entre as APIs. Azure Cosmos DB em particular tem vários recursos que o tornam um ajuste ainda mais perfeito para arquiteturas de microserviços baseados em JSON do que muitos bancos de dados NoSQL:

* uma opção de tipos de dados JSON puros
* um mecanismo JavaScript e [API de consulta](./javascript-query-api.md) incorporados ao banco de dados.
* um [feed de alterações](./change-feed.md) de última geração que os clientes podem assinar para ser notificado sobre modificações em um contêiner.

## <a name="some-challenges-with-nosql-databases"></a>Alguns desafios com bancos de dados NoSQL

Embora haja algumas vantagens claras ao implementar bancos de dados NoSQL, também há alguns desafios que você talvez queira levar em consideração. Eles podem não estar presentes no mesmo grau ao trabalhar com o modelo relacional:

* transações com muitas relações apontando para a mesma entidade.
* transações que exigem consistência forte em todo o conjunto de todos.

Observando o primeiro desafio, a regra de Thumb nos bancos de dados NoSQL geralmente é desnormalização, que, como articulada anteriormente, produz leituras mais eficientes em um sistema distribuído. No entanto, há alguns desafios de design que entram em cena com essa abordagem. Vejamos um exemplo de um produto relacionado a uma categoria e a várias marcas:

:::image type="content" source="./media/relational-or-nosql/many-joins.png" alt-text="Junções":::

Uma abordagem de prática recomendada em um banco de dados de documentos NoSQL seria desnormalizar o nome da categoria e os nomes de marca diretamente em um "documento do produto". No entanto, para manter categorias, marcas e produtos em sincronia, as opções de design para facilitar isso têm complexidade de manutenção adicional, pois os dados são duplicados em vários registros no produto, em vez de serem uma atualização simples em uma relação de "um para muitos" e uma junção para recuperar os dados. 

A desvantagem é que as leituras são mais eficientes no registro desnormalizado e tornam-se cada vez mais eficientes à medida que o número de entidades Unidas conceituais aumenta. No entanto, assim como a eficiência de leitura aumenta com o aumento de números de entidades Unidas em um registro de desnormalização, também faz a complexidade da manutenção de manter as entidades em sincronia. Uma maneira de mitigar essa desvantagem é criar um [modelo de dados híbrido](./modeling-data.md#hybrid-data-models).

Embora haja mais flexibilidade disponível em bancos de dados NoSQL para lidar com essas compensações, a maior flexibilidade também pode produzir mais decisões de design. Consulte nosso artigo [como modelar e particionar dados em Azure Cosmos DB usando um exemplo do mundo real](./how-to-model-partition-example.md), que inclui uma abordagem para manter [dados de usuário desnormalizados em sincronia](./how-to-model-partition-example.md#denormalizing-usernames) , onde os usuários não só ficam em partições diferentes, mas em contêineres diferentes.

Em relação à consistência forte, é raro que isso seja necessário em todo o conjunto de dados. No entanto, nos casos em que isso é necessário, pode ser um desafio em bancos de dados distribuídos. Para garantir uma consistência forte, os dados precisam ser sincronizados em todas as réplicas e regiões antes de permitir que os clientes o leiam. Isso pode aumentar a latência de leituras.

Novamente, Azure Cosmos DB oferece mais flexibilidade do que bancos de dados relacionais para as várias compensações que são relevantes aqui, mas para implementações em pequena escala, essa abordagem pode adicionar mais considerações de design. Consulte nosso artigo sobre as [compensações de consistência, disponibilidade e desempenho](./consistency-levels.md) para obter mais detalhes sobre este tópico.

## <a name="next-steps"></a>Próximas etapas

Saiba como gerenciar sua conta do Azure Cosmos e outros conceitos:

* [Como gerenciar sua conta do Azure Cosmos](how-to-manage-database-account.md)
* [Distribuição global](distribute-data-globally.md)
* [Níveis de consistência](consistency-levels.md)
* [Trabalhando com itens e contêineres do Azure Cosmos](account-databases-containers-items.md)
* [Ponto de extremidade de serviço de rede virtual para sua conta do Azure Cosmos](how-to-configure-vnet-service-endpoint.md)
* [Firewall de IP para sua conta do Azure Cosmos](how-to-configure-firewall.md)
* [Como adicionar e remover regiões do Azure para sua conta do Azure Cosmos](how-to-manage-database-account.md)
* [SLAs do Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)