---
title: Custo total de propriedade (TCO) com Azure Cosmos DB
description: Este artigo compara o custo total de propriedade do Azure Cosmos DB com os bancos de dados locais e IaaS
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.reviewer: sngun
ms.openlocfilehash: eb06d101170685bea5ff231612e9ffbf8df467da
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93079284"
---
# <a name="total-cost-of-ownership-tco-with-azure-cosmos-db"></a>Custo total de propriedade (TCO) com Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

O Azure Cosmos DB foi projetado com a multilocação refinada e o controle de recursos. Esse design permite que o Azure Cosmos DB opere com custos significativamente menores e ajuda os usuários a economizar. Atualmente, o Azure Cosmos DB oferece suporte a mais de 280 cargas de trabalho de cliente em um único computador com aumento contínuo da densidade, além de milhares de cargas de trabalho de cliente dentro de um cluster. Ele balanceia a carga de réplicas das cargas de trabalho de clientes entre computadores diferentes em um cluster e em vários clusters dentro de um data center. Para saber mais, confira [Azure Cosmos DB: enviando a fronteira de bancos de dados distribuídos globalmente](https://azure.microsoft.com/blog/azure-cosmos-db-pushing-the-frontier-of-globally-distributed-databases/). Devido ao controle de recursos, multilocação e integração nativa com o restante da infraestrutura do Azure, o Azure Cosmos DB é em média de 4 a 6 vezes mais barato do que o MongoDB, Cassandra ou outros NoSQL de OSS em execução na IaaS e até 10 vezes mais barato do que mecanismos de banco de dados em execução no local. Consulte o papel sobre [O custo total de (não) propriedade de um serviço de nuvem de banco de dados NoSQL](https://documentdbportalstorage.blob.core.windows.net/papers/11.15.2017/NoSQL%20TCO%20paper.pdf).

As soluções de banco de dados NoSQL de OSS, como Apache Cassandra, MongoDB, HBase, mecanismos foram projetadas para o local. Quando oferecidas como um serviço gerenciado, elas são equivalentes a um modelo do Resource Manager com um banco de dados de locatário para gerenciar os clusters provisionados e monitoramento de suporte. As arquiteturas NoSQL de OSS requerem sobrecarga operacional e as competências podem ser caras e difíceis de encontrar. Por outro lado, o Azure Cosmos DB é um serviço de nuvem totalmente gerenciado que permite aos desenvolvedores concentrarem-se na inovação de negócios em vez de focar no gerenciamento e manutenção da infraestrutura de banco de dados.

Ao contrário de um serviço de banco de dados nativo de nuvem como o Azure Cosmos DB, os mecanismos de banco de dados NoSQL de OSS não foram projetados e criados com controle de recursos ou a multilocação refinada como os princípios fundamentais de arquitetura. Mecanismos de banco de dados NoSQL de OSS, como Cassandra e MongoDB, contam com um pressuposto fundamental de que todos os recursos da máquina virtual no qual eles estão em execução estão disponíveis para uso. Muitos desses mecanismos de banco de dados não funcionam se a quantidade de recursos decair abaixo de um determinado limite. Por exemplo, para pequenas instâncias de VM, sendo que elas estão disponíveis com configurações recomendados pelo fornecedor, normalmente sugerindo VMs em larga escala com custo mais alto. Portanto, não é possível hospedar um NoSQL OSS ou qualquer outro mecanismo de banco de dados local e disponibilizá-lo usando um modelo de carregamento baseado em consumo como solicitações por segundo ou armazenamento consumido.

## <a name="total-cost-of-ownership-of-azure-cosmos-db"></a>Custo total de propriedade do Azure Cosmos DB

O modelo de provisionamento sem servidor do Azure Cosmos DB elimina a necessidade de provisionar excessivamente a infraestrutura de banco de dados. Os recursos do Azure Cosmos DB são fornecidos sem a necessidade de configurações especializadas ou licenciamento. Como resultado, os aplicativos com suporte do Azure Cosmos DB podem executar com até 70% de economia de custo total de propriedade quando comparados aos bancos de dados NoSQL de OSS. Para ver alguns exemplos em tempo real, consulte [Casos de uso de clientes](https://customers.microsoft.com/en-us/search?sq=Cosmos%20DB&ff=&p=0&so=story_publish_date%20desc). Outros benefícios do modelo de preços do Azure Cosmos DB incluem:

* **Valor ótimo para o preço:** Analistas de mercado, clientes e parceiros confirmaram um valor maior de todos os recursos que a Azure Cosmos DB oferece para um preço muito menor em comparação com o que os clientes podem obter ao implementar essas soluções por conta própria ou por meio de outros fornecedores. O banco de dados apresenta uma distribuição global, gravações de várias regiões, modelos de consistência bem definidos e intuitivos, a indexação automática é bastante simplificada com Azure Cosmos DB sem nenhuma complexidade, sobrecarga ou tempo de inatividade.

* **Nenhuma administração de DevOps NoSQL é necessária:** Com Azure Cosmos DB um não precisa empregar DevOps para gerenciar implantações, executar a manutenção, a escala ou o patch. É possível executar as todas as cargas de trabalho que você faria com cluster NoSQL de OSS hospedado localmente ou na infraestrutura de nuvem.

:::image type="content" source="./media/total-cost-ownership/tco.png" alt-text="Custo de propriedade do Azure Cosmos DB" border="false":::

* **Capacidade de dimensionar de forma elástica:** A taxa de transferência de Azure Cosmos DB pode ser ampliada e reduzida, permitindo que você reduza o custo de propriedade fora do horário de pico. Clusters NoSQL de OSS implantados em infraestrutura de nuvem oferecem elasticidade limitada, sendo que implantações locais não são elásticas por definição. No Azure Cosmos DB, se você provisionar mais taxa de transferência, é garantido que essa taxa de transferência será dimensionada linearmente. Essa garantia é amparada pelos SLAs financeiros e no 99º percentil em qualquer escala.

* **Economias de escala:** Um serviço gerenciado como o Azure Cosmos DB opera com um grande número de nós, integrado nativamente com rede, armazenamento e computações. É possível economizar custos devido à padronização de grande escala do Azure Cosmos DB.

* **Otimizado para a nuvem:** O Azure Cosmos DB foi projetado desde o início com multilocação e isolamento de desempenho refinados. Isso permite colocar, executar e balancear idealmente milhares de locatários e suas cargas de trabalho entre todo os clusters e data centers. Por outro lado, a geração atual de bancos de dados NoSQL de OSS opera no local com a máquina virtual inteira, presumidamente executando a carga de trabalho de um único locatário. Esses bancos de dados também não são projetados para aproveitar ao máximo a infra-estrutura e o hardware de um provedor de nuvem. Por exemplo, um mecanismo de banco de dados NoSQL de OSS não está ciente das diferenças entre uma máquina virtual que está inoperante versus uma atualização de imagem de rotina, ou o fato de que esse disco premium já está triplamente replicado. Ele não pode aproveitar esses benefícios e repassar os benefícios e economias para os clientes.

* **Você paga por hora:** Para cargas de trabalho de grande escala que precisam ser dimensionadas a qualquer momento, você só é cobrado por hora. As cargas de trabalho em um aplicativo normalmente variam conforme a época do ano e os dados que são consultados. Com o Azure Cosmos DB, é possível escalar ou reduzir verticalmente conforme a necessidade e pagar apenas pelo que você precisa. Com sistemas locais ou instalados em IaaS, você não pode corresponder a esse modelo, pois não há uma maneira de encerrar o hardware a cada hora. Nesses casos, é possível economizar potencialmente entre 10 a 14 vezes em média com o Azure Cosmos DB.

* **Você obtém vários recursos gratuitamente:** Em Azure Cosmos DB, as cargas de trabalho de gravação são consideravelmente mais baratas em comparação com os serviços de banco de dados alternativos. Além disso, o Azure Cosmos DB oferece recursos como [Indexação Automática](index-policy.md), [Tempo de Vida (TTL)](time-to-live.md), [Feed de Alteração](change-feed.md) e outros sem encargos adicionais, o que normalmente é cobrado por outros serviços de banco de dados.

* **Usa moeda unificada para cargas de trabalho diversas:** Ao contrário das ofertas alternativas, no Azure Cosmos DB, você não precisa segmentar cargas de trabalho, por exemplo, em leituras e gravações. Ou provisionar taxa de transferência com base no tipo de carga de trabalho, ou seja, taxa de transferência de leitura versus taxa de transferência de gravação. No Azure Cosmos DB, a taxa de transferência provisionada é reservada usando uma moeda unificada e normalizada em termos de unidades de solicitação ou RU/seg. O Azure Cosmos DB não força você a atribuir prioridade às suas cargas de trabalho, executar planejamento de capacidade ou pagar por cada tipo de capacidade separadamente. Essa abordagem permite que você simplesmente faça o intercâmbio das mesmas RU/s entre várias operações e tipos de carga de trabalho.

* **Não requer que as VMs de provisionamento sejam dimensionadas:** A maioria dos bancos de dados operacionais exige que você vá para máquinas virtuais grandes para evitar vizinhos com ruído e controle de recursos flexíveis, se desejar a escala. Isso impõe aos clientes o encargo e o compromisso antecipado do custo. Com o Azure Cosmos DB, você pode começar pequeno e progredir para tamanhos de carga de trabalho de grande escala continuamente e sem qualquer tempo de inatividade ou impacto na disponibilidade de dados.

* **Você pode utilizar a taxa de transferência provisionada para um limite máximo:** Em virtude da multiplexação de subnúcleos no Azure Cosmos DB, você pode saturar a taxa de transferência provisionada para uma extensão maior do que as opções hospedadas de IaaS ou ofertas de terceiros. Esse método economiza muito mais do que as soluções alternativas.

* **Integração profunda do Azure Cosmos DB com outros serviços do Azure.** O Azure Cosmos DB tem uma integração nativa com Rede, Computação, Azure Functions (sem servidor), Azure IoT e outros serviços do Azure. Com essa integração, você obtém o melhor desempenho e velocidade da replicação de dados em todo o mundo com garantias robustas. As soluções de terceiros não seriam capazes de igualar ou normalmente cobrariam uma taxa premium para oferecer esses recursos.

* **Você obtém automaticamente alta disponibilidade, com pelo menos 10-20 domínios de falha por padrão:** O Azure Cosmos DB dá suporte à distribuição de cargas de trabalho entre domínios de falha, um recurso essencial para alta disponibilidade. Ele oferece alta disponibilidade de 99,999 para leituras e gravações no 99º percentil em qualquer lugar do mundo. Seria alto o custo para implementação de algo parecido com isso por conta própria ou por meio de uma solução de terceiros.

* **Você obtém automaticamente todos os recursos empresariais, sem nenhum custo adicional.** O Azure Cosmos DB oferece o conjunto mais abrangente de certificações de conformidade, segurança e criptografia em repouso e em movimento sem custo adicional (em comparação com a nossa concorrência). Você obtém automaticamente a disponibilidade regional em qualquer lugar do mundo. É possível ampliar o alcance do seu banco de dados para qualquer número de regiões do Azure e adicionar ou remover regiões em qualquer ponto.

* **Você pode economizar até 65% dos custos com capacidade reservada:** Azure Cosmos DB [capacidade reservada](cosmos-db-reserved-capacity.md) ajuda você a economizar dinheiro por meio do pagamento antecipado de Azure Cosmos DB recursos por um ou três anos. É possível reduzir significativamente seus custos com compromissos antecipados de um ano ou três anos e economizar com descontos de 20% a 65% em comparação com o preço regular. Em suas cargas de trabalho de críticas, você pode obter SLAs melhores em termos de capacidade de provisionamento.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [Como o modelo de preços do Azure Cosmos DB é econômico para os clientes](total-cost-ownership.md)
* Saiba mais sobre [Otimizando para desenvolvimento e teste](optimize-dev-test.md)
* Saiba mais sobre [Otimizando o custo da taxa de transferência](optimize-cost-throughput.md)
* Saiba mais sobre [Otimizando o custo de armazenamento](optimize-cost-storage.md)
* Saiba mais sobre [Otimizando o custo de leituras e gravações](optimize-cost-reads-writes.md)
* Saiba mais sobre [Otimizando o custo de consultas](./optimize-cost-reads-writes.md)
* Saiba mais sobre [Otimizando o custo de contas do Cosmos em várias regiões](optimize-cost-regions.md)
* Saiba mais sobre [O Custo Total de (Não) Propriedade de um Serviço de Nuvem de Banco de Dados NoSQL](https://documentdbportalstorage.blob.core.windows.net/papers/11.15.2017/NoSQL%20TCO%20paper.pdf)
