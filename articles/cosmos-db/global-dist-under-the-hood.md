---
title: Distribuição global com o Azure Cosmos DB – nos bastidores
description: Este artigo apresenta detalhes técnicos relacionados à distribuição global do Azure Cosmos DB
author: dharmas-cosmos
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/31/2019
ms.author: dharmas
ms.reviewer: sngun
ms.openlocfilehash: 84ce13ae3bb0a4b66b8167e61b720fe6cecbe95c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60888761"
---
# <a name="global-data-distribution-with-azure-cosmos-db---under-the-hood"></a>Distribuição de dados global com o Azure Cosmos DB – nos bastidores

O Azure Cosmos DB é um serviço básico no Azure, para que ele é implantado em todas as regiões do Azure em todo o mundo incluindo público, soberanas, departamento de defesa (DoD) e nuvens governamentais. Em um data center, podemos implantar o Azure Cosmos DB em grandes carimbos de computadores, cada um com armazenamento local dedicado. Em um data center, o Azure Cosmos DB é implantado em vários clusters, cada um executando potencialmente várias gerações de hardware. Computadores em um cluster são normalmente espalhadas por 10 a 20 domínios de falha para alta disponibilidade dentro de uma região. A imagem a seguir mostra a topologia do sistema de distribuição global do Cosmos DB:

![Topologia do sistema](./media/global-dist-under-the-hood/distributed-system-topology.png)

**Distribuição global no Azure Cosmos DB está pronta para uso:** A qualquer momento, com alguns cliques, ou programaticamente com uma única chamada de API, você pode adicionar ou remover as regiões geográficas associados com seu banco de dados do Cosmos. Um banco de dados Cosmos, por sua vez, consiste em um conjunto de contêineres do Cosmos. No Cosmos DB, os contêineres servem como unidades lógicas de distribuição e escalabilidade. As coleções, as tabelas e os gráficos que você cria são (internamente) apenas contêineres do Cosmos. Os contêineres são completamente independente de esquema e fornecem um escopo para uma consulta. Os dados em um contêiner do Cosmos são indexados automaticamente após a ingestão. Indexação automática permite aos usuários consultar os dados sem os aborrecimentos de gerenciamento de esquema ou índice, especialmente em uma instalação distribuída globalmente.  

- Em uma determinada região, os dados dentro de um contêiner são distribuídos usando uma chave de partição, que fornecem e gerenciados de forma transparente pelas partições físicas subjacentes (*distribuição local*).  

- Cada partição física também é replicada entre regiões geográficas (*distribuição global*). 

Quando um aplicativo usando o Cosmos DB de forma elástica dimensiona a taxa de transferência em um contêiner do Cosmos ou consome mais armazenamento, Cosmos DB transparentemente manipula as operações de gerenciamento de partição (dividir, clonar, excluir) em todas as regiões. Independentemente da escala, da distribuição ou de falhas, o Cosmos DB continua a fornecer uma única imagem do sistema dos dados dentro de contêineres, que são distribuídos globalmente em qualquer número de regiões.  

Conforme mostrado na imagem a seguir, os dados dentro de um contêiner são distribuídos em duas dimensões - o em uma região e entre regiões, em todo o mundo:  

![partições físicas](./media/global-dist-under-the-hood/distribution-of-resource-partitions.png)

Uma partição física é implementada por um grupo de réplicas, chamadas de um *conjunto de réplicas*. Cada computador hospeda centenas de réplicas que correspondem a diversas partições físicas dentro de um conjunto fixo de processos, conforme mostrado na imagem acima. As réplicas correspondentes às partições físicas são colocadas dinamicamente e com balanceamento de carga entre os computadores dentro de um cluster e os data centers dentro de uma região.  

Uma réplica pertence exclusivamente a um locatário do Azure Cosmos DB. Cada réplica hospeda uma instância do [Mecanismo de Banco de Dados](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) do Cosmos DB, que gerencia os recursos, bem como os índices associados. O Mecanismo de Banco de Dados do Cosmos DB opera em um sistema de tipo baseado em ARS (atom-record-sequence). O mecanismo é independente do conceito de um esquema, obscurecendo o limite entre os valores de instância e de estrutura de registros. O Cosmos DB obtém total independência de esquema indexando tudo automaticamente no momento da ingestão de maneira eficiente, o que permite aos usuários consultar seus dados distribuídos globalmente sem precisarem lidar com gerenciamento de esquema ou índice.

O mecanismo de banco de dados Cosmos consiste em componentes, incluindo a implementação de vários primitivos de coordenação, tempos de execução de linguagem, o processador de consultas e o armazenamento e indexação subsistemas responsáveis pelo armazenamento transacional e a indexação de dados, respectivamente. Para fornecer durabilidade e alta disponibilidade, o Mecanismo de Banco de Dados mantém os dados e o índice em SSDs e replica-os entre as instâncias do Mecanismo de Banco de Dados dentro dos conjuntos de réplica, respectivamente. Locatários maiores correspondem a uma escala maior de taxa de transferência e armazenamento e ter maior ou mais réplicas ou ambos. Cada componente do sistema é completamente assíncrono: nenhum thread jamais bloqueia e cada thread faz trabalho de curta duração sem incorrer em nenhuma alternância de thread desnecessária. A limitação de taxa e a contrapressão são inseridas em toda a pilha do controle de admissão para todos os caminhos de E/S. Mecanismo de banco de dados do cosmos foi projetado para explorar a simultaneidade refinada e entregar alta taxa de transferência enquanto estiver operando gastar quantidades de recursos do sistema.

Distribuição global do cosmos DB se baseia em duas abstrações de chave – *conjuntos de réplicas* e *conjuntos de partições*. Um conjunto de réplicas é um bloco de Lego modular para coordenação, enquanto que um conjunto de partições é uma sobreposição dinâmica de uma ou mais partições físicas distribuídas geograficamente. Para entender como a distribuição global funciona, precisamos entender essas duas abstrações principais. 

## <a name="replica-sets"></a>Conjuntos de réplicas

Uma partição física é materializada como um grupo autogerenciado e com balanceamento de carga dinâmico de réplicas distribuídas entre vários domínios de falha, chamados de conjunto de réplicas. Esse conjunto implementa coletivamente o protocolo de computador de estado replicado para tornar os dados na partição física altamente disponíveis, duráveis e consistentes. A associação de conjunto de réplicas *N* é dinâmico – ela mantém flutuando entre *Nmín* e *Nmáx* com base nas falhas, operações administrativas e o tempo por falha réplicas para regenerar/recuperação. Com base em alterações de associação, a replicação de protocolo também reconfigura o tamanho de leitura e os quóruns de gravação. Para distribuir uniformemente a taxa de transferência que é atribuída a uma determinada partição física, podemos empregar duas ideias: 

- Primeiro, o custo de processamento das solicitações de gravação no líder é maior que o custo da aplicação das atualizações sobre o seguidor. Do mesmo modo, o líder tem mais recursos de sistema orçados que os seguidores. 

- Em segundo lugar, sempre que possível, o quórum de leitura para um nível de coerência específico é composto exclusivamente pelas réplicas de seguidores. Evitamos entrar em contato com o líder para atender às leituras, a menos que necessário. Podemos empregar um número de ideias de pesquisa feita na relação de [capacidade e carga](https://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) nos sistemas com base em quorum para o [cinco modelos de consistência](consistency-levels.md) que dá suporte ao Cosmos DB.  

## <a name="partition-sets"></a>Conjuntos de partições

Um grupo de partições físicas, uma de cada um configurado com as regiões de banco de dados Cosmos, é composto para gerenciar o mesmo conjunto de chaves replicados em todas as regiões configuradas. Este primitivo coordenação mais alto é chamado um *conjunto de partições* -uma sobreposição dinâmica distribuída geograficamente de gerenciamento de um determinado conjunto de chaves de partições físicas. Enquanto o escopo de uma determinada partição física (uma réplica-set) dentro de um cluster, um conjunto de partições pode abranger clusters, data centers e regiões geográficas, conforme mostrado na imagem abaixo:  

![Conjuntos de partição](./media/global-dist-under-the-hood/dynamic-overlay-of-resource-partitions.png)

Você pode pensar em um conjunto de partições como um "superconjunto de réplicas" geograficamente disperso composto por vários conjuntos de réplicas com o mesmo conjunto de chaves. Semelhante a um conjunto de réplicas, da associação do conjunto partição também é dinâmica – ele varia com base em operações de gerenciamento de partição física implícita para adicionar ou remover novas partições/para um determinado conjunto de partições (por exemplo, quando você expandir a taxa de transferência em um contêiner, adicionar ou remover uma região para seu banco de dados Cosmos, ou quando ocorrem falhas). Em virtude de cada uma das partições (de um conjunto de partições) tendo a gerenciar a associação de conjunto de partições em seu próprio conjunto de réplicas, a associação é totalmente descentralizado e altamente disponível. Durante a reconfiguração de um conjunto de partições, a topologia da sobreposição entre as partições físicas também é estabelecida. A topologia dinamicamente é selecionada com base no nível de consistência, distância geográfica e largura de banda de rede disponível entre a origem e as partições físicas de destino.  

O serviço permite que você configure seus bancos de dados do Cosmos com uma ou várias regiões de gravação e, dependendo da escolha, conjuntos de partições são configurados para aceitar as gravações em exatamente uma ou em todas as regiões. O sistema usa um protocolo de consenso aninhado de dois níveis – um nível opera dentro das réplicas de um conjunto de réplicas de uma partição física que aceita as gravações e o outro opera no nível de um conjunto de partições para fornecer garantias completas de ordenação para todas as gravações confirmadas no conjunto de partições. Esse consenso aninhado em várias camadas é crítico para a implementação de nossos contratos rigorosos de alta disponibilidade, bem como a implementação dos modelos de coerência, que o Cosmos DB oferece a seus clientes.  

## <a name="conflict-resolution"></a>Resolução de conflitos

Nosso design para propagação da atualização, resolução de conflitos e acompanhamento de causalidade é inspirado no trabalho anterior sobre [algoritmos epidêmicos](https://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) e o sistema [Bayou](https://zoo.cs.yale.edu/classes/cs422/2013/bib/terry95managing.pdf). Embora kernels de ideias tenham sobrevivido e apresentem um quadro de referência conveniente para comunicação com o design do sistema do Cosmos DB, eles também foram submetidos a transformações significativas ao serem aplicados ao sistema do Cosmos DB. Isso era necessária, porque os sistemas anteriores foram projetados nem com a governança de recursos com a escala no qual o Cosmos DB precisa para operar, nem para fornecer os recursos (por exemplo, a consistência de desatualização limitada) e a rigorosos e SLAs abrangentes que o Cosmos DB fornece a seus clientes.  

Lembre-se de que um conjunto de partições é distribuído em várias regiões e segue o protocolo de replicação do Cosmos DB (vários mestres) para replicar os dados entre as partições físicas que compõem um determinado conjunto de partições. Cada partição física (de um conjunto de partições) aceita gravações e prepara leituras tipicamente para os clientes que são locais dessa região. As gravações aceitas por uma partição física em uma região são confirmadas permanentemente e altamente disponibilizadas na partição física, antes de serem reconhecidas para o cliente. São gravações provisórias e propagadas para outras partições físicas dentro do conjunto de partições usando um canal antientropia. Os clientes podem solicitar gravações provisórias ou confirmadas passando um cabeçalho de solicitação. A propagação antientropia (incluindo a frequência da propagação) é dinâmica, baseada na topologia do conjunto de partições, na proximidade regional entre as partições físicas e no nível de coerência configurado. Em um conjunto de partições, o Cosmos DB segue um esquema de confirmação primário com uma partição de arbitrador selecionada dinamicamente. A seleção do arbitrador é dinâmica e é parte integrante da reconfiguração do conjunto de partições com base na topologia da sobreposição. As gravações confirmadas (incluindo atualizações multilinha/em lote) têm garantia de serem pedidas. 

Podemos empregar os relógios de vetor codificados (contendo a ID da região e relógios lógicos correspondendo a cada nível de consenso no conjunto de réplicas e no conjunto de partições, respectivamente) para vetores de versão e acompanhamento de causalidade detectarem e resolverem conflitos de atualização. A topologia e o algoritmo de seleção de par são projetados para garantir os armazenamentos fixo e mínimo e a sobrecarga mínima de rede dos vetores de versão. O algoritmo garante a propriedade de convergência estrita.  

Para os bancos de dados do Cosmos configurados com várias regiões de gravação, o sistema oferece diversas políticas de resolução automática de conflitos para os desenvolvedores escolherem, incluindo: 

- **Última de gravação de Wins (LWW)**, que, por padrão, usa uma propriedade de carimbo de hora definida pelo sistema (que é baseada no protocolo de sincronização de hora de relógio). O Cosmos DB também permite que você especifique qualquer outra propriedade numérica personalizada a ser usada para resolução de conflitos.  
- **Política de resolução em conflito definido pelo aplicativo (personalizada)** (expresso por meio de procedimentos de mesclagem), que é projetado para a reconciliação de semântica definido pelo aplicativo de conflitos. Esses procedimentos são invocados após a detecção de conflitos de entre gravações sob responsabilidade de uma transação de banco de dados no lado do servidor. O sistema fornece exatamente garante uma vez para a execução de um procedimento de mesclagem como parte do protocolo de compromisso. Há [vários exemplos de resolução de conflito](how-to-manage-conflicts.md) disponíveis para você experimentar.  

## <a name="consistency-models"></a>Modelos de coerência

Se você configurar seu banco de dados do Cosmos com uma única ou várias regiões de gravação, você pode escolher entre cinco modelos de consistência bem definidos. Com várias regiões de gravação, estes são alguns aspectos importantes dos níveis de consistência:  

A consistência de desatualização limitada garante que todas as leituras serão dentro *K* prefixos ou *T* segundos desde a gravação mais recente em qualquer uma das regiões. Além disso, leituras com consistência de desatualização são garantidamente monotônica e garantia de prefixo consistente. O protocolo antientropia opera de maneira limitada por taxa e garante que os prefixos não se acumulam e a contrapressão sobre as gravações não precisem ser aplicadas. Garantias de consistência de sessão monotônico de leitura, gravação monotônica, suas próprias gravações de leitura, gravação segue garantias de prefixo de leitura e consistente em todo o mundo. Para bancos de dados configurados com consistência forte, os benefícios (gravação de baixa latência, disponibilidade de gravação alta) de várias regiões de gravação não se aplica, por causa de replicação síncrona entre regiões.

A semântica dos modelos de cinco consistência no Cosmos DB é descrita [aqui](consistency-levels.md)e matematicamente descritas usando um alto nível TLA + especificações [aqui](https://github.com/Azure/azure-cosmos-tla).

## <a name="next-steps"></a>Próximas etapas

Em seguida, saiba como configurar a distribuição global usando os seguintes artigos:

* [Adicionar ou remover regiões de sua conta de banco de dados](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Como configurar clientes para multihoming](how-to-manage-database-account.md#configure-clients-for-multi-homing)
* [Como criar uma política de resolução de conflitos personalizado](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)
