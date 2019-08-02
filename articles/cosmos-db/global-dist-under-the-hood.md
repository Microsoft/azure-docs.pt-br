---
title: Distribuição global com o Azure Cosmos DB – nos bastidores
description: Este artigo apresenta detalhes técnicos relacionados à distribuição global do Azure Cosmos DB
author: dharmas-cosmos
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: dharmas
ms.reviewer: sngun
ms.openlocfilehash: 849c3a745de08e7cf8ff7f1b8bb237a6d0f54395
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68384151"
---
# <a name="global-data-distribution-with-azure-cosmos-db---under-the-hood"></a>Distribuição de dados global com o Azure Cosmos DB – nos bastidores

Azure Cosmos DB é um serviço fundamental no Azure, portanto, ele é implantado em todas as regiões do Azure no mundo inteiro, incluindo as nuvens pública, soberanas, departamento de defesa (DoD) e governamental. Em um data center, podemos implantar o Azure Cosmos DB em grandes carimbos de computadores, cada um com armazenamento local dedicado. Em um data center, o Azure Cosmos DB é implantado em vários clusters, cada um executando potencialmente várias gerações de hardware. Os computadores em um cluster normalmente são distribuídos entre 10-20 domínios de falha para alta disponibilidade em uma região. A imagem a seguir mostra a topologia do sistema de distribuição global do Cosmos DB:

![Topologia do sistema](./media/global-dist-under-the-hood/distributed-system-topology.png)

**A distribuição global no Azure Cosmos DB está pronta para uso:** A qualquer momento, com alguns cliques ou programaticamente com uma única chamada à API, você pode adicionar ou remover as regiões geográficas associadas ao banco de dados Cosmos. Um banco de dados Cosmos, por sua vez, consiste em um conjunto de contêineres Cosmos. No Cosmos DB, os contêineres servem como unidades lógicas de distribuição e escalabilidade. As coleções, as tabelas e os gráficos que você cria são (internamente) apenas contêineres do Cosmos. Os contêineres são completamente independentes de esquema e fornecem um escopo para uma consulta. Os dados em um contêiner do Cosmos são indexados automaticamente após a ingestão. A indexação automática permite que os usuários consultem os dados sem os aborrecimentos do gerenciamento de esquema ou índice, especialmente em uma instalação distribuída globalmente.  

- Em uma determinada região, os dados dentro de um contêiner são distribuídos usando uma chave de partição, que você fornece e é gerenciado de forma transparente pelas partições físicas subjacentes (*distribuição local*).  

- Cada partição física também é replicada entre regiões geográficas (*distribuição global*). 

Quando um aplicativo que usa Cosmos DB dimensiona a taxa de transferência de forma elástica em um contêiner Cosmos ou consome mais armazenamento, Cosmos DB manipula de modo transparente as operações de gerenciamento de partição (Split, clone e Delete) em todas as regiões. Independentemente da escala, da distribuição ou de falhas, o Cosmos DB continua a fornecer uma única imagem do sistema dos dados dentro de contêineres, que são distribuídos globalmente em qualquer número de regiões.  

Conforme mostrado na imagem a seguir, os dados dentro de um contêiner são distribuídos juntamente com duas dimensões – dentro de uma região e entre regiões, em todo o mundo:  

![partições físicas](./media/global-dist-under-the-hood/distribution-of-resource-partitions.png)

Uma partição física é implementada por um grupo de réplicas, chamado de *conjunto*de réplicas. Cada computador hospeda centenas de réplicas que correspondem a várias partições físicas em um conjunto fixo de processos, conforme mostrado na imagem acima. As réplicas correspondentes às partições físicas são colocadas dinamicamente e com balanceamento de carga entre os computadores dentro de um cluster e os data centers dentro de uma região.  

Uma réplica pertence exclusivamente a um locatário do Azure Cosmos DB. Cada réplica hospeda uma instância do [Mecanismo de Banco de Dados](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) do Cosmos DB, que gerencia os recursos, bem como os índices associados. O Mecanismo de Banco de Dados do Cosmos DB opera em um sistema de tipo baseado em ARS (atom-record-sequence). O mecanismo é independente do conceito de um esquema, desfocando o limite entre a estrutura e os valores de instância de registros. O Cosmos DB obtém total independência de esquema indexando tudo automaticamente no momento da ingestão de maneira eficiente, o que permite aos usuários consultar seus dados distribuídos globalmente sem precisarem lidar com gerenciamento de esquema ou índice.

O mecanismo de banco de dados Cosmos consiste em componentes, incluindo a implementação de vários primitivos de coordenação, tempos de execução de linguagem, o processador de consultas e os subsistemas de armazenamento e indexação responsáveis pelo armazenamento transacional e indexação de dados, respectivos. Para fornecer durabilidade e alta disponibilidade, o Mecanismo de Banco de Dados mantém os dados e o índice em SSDs e replica-os entre as instâncias do Mecanismo de Banco de Dados dentro dos conjuntos de réplica, respectivamente. Locatários maiores correspondem a uma escala maior de taxa de transferência e armazenamento e têm maior ou maior réplicas ou ambos. Cada componente do sistema é completamente assíncrono: nenhum thread jamais bloqueia e cada thread faz trabalho de curta duração sem incorrer em nenhuma alternância de thread desnecessária. A limitação de taxa e a contrapressão são inseridas em toda a pilha do controle de admissão para todos os caminhos de E/S. O mecanismo de banco de dados Cosmos foi projetado para explorar a simultaneidade refinada e fornecer alta taxa de transferência enquanto opera em frugal quantidades de recursos do sistema.

A distribuição global de Cosmos DB depende de duas abstrações de chave – *conjuntos* de réplicas e *conjuntos de partições*. Um conjunto de réplicas é um bloco de Lego modular para coordenação, enquanto que um conjunto de partições é uma sobreposição dinâmica de uma ou mais partições físicas distribuídas geograficamente. Para entender como a distribuição global funciona, precisamos entender essas duas abstrações principais. 

## <a name="replica-sets"></a>Conjuntos de réplicas

Uma partição física é materializada como um grupo autogerenciado e com balanceamento de carga dinâmico de réplicas distribuídas entre vários domínios de falha, chamados de conjunto de réplicas. Esse conjunto implementa coletivamente o protocolo de computador de estado replicado para tornar os dados na partição física altamente disponíveis, duráveis e consistentes. A associação de conjunto de réplicas *N* é dinâmica – ela continua flutuando entre *nmín* e *nmáx* com base nas falhas, operações administrativas e no tempo de réplicas com falha para gerar/recuperar novamente. Com base em alterações de associação, a replicação de protocolo também reconfigura o tamanho de leitura e os quóruns de gravação. Para distribuir uniformemente a taxa de transferência atribuída a uma determinada partição física, empregamos duas ideias: 

- Primeiro, o custo do processamento das solicitações de gravação no líder é maior do que o custo da aplicação das atualizações no acompanhamento. Do mesmo modo, o líder tem mais recursos de sistema orçados que os seguidores. 

- Em segundo lugar, sempre que possível, o quórum de leitura para um nível de coerência específico é composto exclusivamente pelas réplicas de seguidores. Evitamos entrar em contato com o líder para atender às leituras, a menos que necessário. Empregamos várias ideias da pesquisa feita na relação de [carga e capacidade](https://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) nos sistemas baseados em quorum para os [cinco modelos de consistência](consistency-levels.md) aos quais Cosmos DB dá suporte.  

## <a name="partition-sets"></a>Conjuntos de partições

Um grupo de partições físicas, um de cada um dos configurados com as regiões de banco de dados Cosmos, é composto para gerenciar o mesmo conjunto de chaves replicadas em todas as regiões configuradas. Esse primitivo de coordenação mais alto é chamado de *conjunto* de partições, uma sobreposição dinâmica geograficamente distribuída de partições físicas que gerenciam um determinado conjunto de chaves. Embora uma determinada partição física (um conjunto de réplicas) esteja no escopo de um cluster, um conjunto de partições pode abranger clusters, data centers e regiões geográficas, conforme mostrado na imagem abaixo:  

![Conjuntos de partição](./media/global-dist-under-the-hood/dynamic-overlay-of-resource-partitions.png)

Você pode pensar em um conjunto de partições como um "superconjunto de réplicas" geograficamente disperso composto por vários conjuntos de réplicas com o mesmo conjunto de chaves. Semelhante a um conjunto de réplicas, a associação de um conjunto de partições também é dinâmica – ela flutua com base nas operações de gerenciamento de partição física implícita para adicionar/remover novas partições de/para um determinado conjunto de partições (por exemplo, ao escalar horizontalmente a taxa de transferência em um contêiner, adicionar/remover uma região para o banco de dados Cosmos ou quando ocorreram falhas). Em virtude de ter cada uma das partições (de um conjunto de partição) gerenciar a associação de conjunto de partições em seu próprio conjunto de réplicas, a associação é totalmente descentralizada e altamente disponível. Durante a reconfiguração de um conjunto de partições, a topologia da sobreposição entre as partições físicas também é estabelecida. A topologia é selecionada dinamicamente com base no nível de consistência, distância geográfica e largura de banda de rede disponível entre as partições físicas de origem e de destino.  

O serviço permite que você configure seus bancos de dados do Cosmos com uma ou várias regiões de gravação e, dependendo da escolha, conjuntos de partições são configurados para aceitar as gravações em exatamente uma ou em todas as regiões. O sistema usa um protocolo de consenso aninhado de dois níveis – um nível opera dentro das réplicas de um conjunto de réplicas de uma partição física que aceita as gravações e o outro opera no nível de um conjunto de partições para fornecer garantias completas de ordenação para todas as gravações confirmadas no conjunto de partições. Esse consenso aninhado em várias camadas é crítico para a implementação de nossos contratos rigorosos de alta disponibilidade, bem como a implementação dos modelos de coerência, que o Cosmos DB oferece a seus clientes.  

## <a name="conflict-resolution"></a>Resolução de conflitos

Nosso design para propagação da atualização, resolução de conflitos e acompanhamento de causalidade é inspirado no trabalho anterior sobre [algoritmos epidêmicos](https://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) e o sistema [Bayou](https://zoo.cs.yale.edu/classes/cs422/2013/bib/terry95managing.pdf). Embora kernels de ideias tenham sobrevivido e apresentem um quadro de referência conveniente para comunicação com o design do sistema do Cosmos DB, eles também foram submetidos a transformações significativas ao serem aplicados ao sistema do Cosmos DB. Isso era necessário, porque os sistemas anteriores não foram projetados nem com a governança de recursos nem com a escala na qual Cosmos DB precisa operar, nem para fornecer os recursos (por exemplo, consistência de desatualização limitada) e os rigorosos e SLAs abrangentes que Cosmos DB fornece aos seus clientes.  

Lembre-se de que um conjunto de partições é distribuído em várias regiões e segue o protocolo de replicação do Cosmos DB (vários mestres) para replicar os dados entre as partições físicas que compõem um determinado conjunto de partições. Cada partição física (de um conjunto de partições) aceita gravações e prepara leituras tipicamente para os clientes que são locais dessa região. As gravações aceitas por uma partição física em uma região são confirmadas permanentemente e altamente disponibilizadas na partição física, antes de serem reconhecidas para o cliente. São gravações provisórias e propagadas para outras partições físicas dentro do conjunto de partições usando um canal antientropia. Os clientes podem solicitar gravações provisórias ou confirmadas passando um cabeçalho de solicitação. A propagação antientropia (incluindo a frequência da propagação) é dinâmica, baseada na topologia do conjunto de partições, na proximidade regional entre as partições físicas e no nível de coerência configurado. Em um conjunto de partições, o Cosmos DB segue um esquema de confirmação primário com uma partição de arbitrador selecionada dinamicamente. A seleção do arbitrador é dinâmica e é parte integrante da reconfiguração do conjunto de partições com base na topologia da sobreposição. As gravações confirmadas (incluindo atualizações multilinha/em lote) têm garantia de serem pedidas. 

Podemos empregar os relógios de vetor codificados (contendo a ID da região e relógios lógicos correspondendo a cada nível de consenso no conjunto de réplicas e no conjunto de partições, respectivamente) para vetores de versão e acompanhamento de causalidade detectarem e resolverem conflitos de atualização. A topologia e o algoritmo de seleção de par são projetados para garantir os armazenamentos fixo e mínimo e a sobrecarga mínima de rede dos vetores de versão. O algoritmo garante a propriedade de convergência estrita.  

Para os bancos de dados do Cosmos configurados com várias regiões de gravação, o sistema oferece diversas políticas de resolução automática de conflitos para os desenvolvedores escolherem, incluindo: 

- **Last-Write-WINS (LWW)** , que, por padrão, usa uma propriedade Timestamp definida pelo sistema (que é baseada no protocolo de relógio de sincronização de tempo). O Cosmos DB também permite que você especifique qualquer outra propriedade numérica personalizada a ser usada para resolução de conflitos.  
- **Política de resolução de conflitos definida pelo aplicativo (personalizada)** (expresso por meio de procedimentos de mesclagem), que é projetado para a reconciliação de semânticas definida pelo aplicativo de conflitos. Esses procedimentos são invocados após a detecção de conflitos de entre gravações sob responsabilidade de uma transação de banco de dados no lado do servidor. O sistema fornece exatamente uma vez a garantia para a execução de um procedimento de mesclagem como parte do protocolo de compromisso. Há [várias amostras de resolução de conflitos](how-to-manage-conflicts.md) disponíveis para você brincar com.  

## <a name="consistency-models"></a>Modelos de coerência

Se você configurar o banco de dados cosmos com uma única ou várias regiões de gravação, poderá escolher entre os cinco modelos de consistência bem definidos. Com várias regiões de gravação, os seguintes são alguns aspectos notáveis dos níveis de consistência:  

A consistência de desatualização limitada garante que todas as leituras estarão dentro dos prefixos *K* ou *T* segundos da gravação mais recente em qualquer uma das regiões. Além disso, as leituras com consistência de desatualização limitada são garantidas como monotônico e com garantias de prefixo consistentes. O protocolo antientropia opera de maneira limitada por taxa e garante que os prefixos não se acumulam e a contrapressão sobre as gravações não precisem ser aplicadas. A consistência da sessão garante leitura monotônico, gravação monotônico, leitura de suas próprias gravações, gravação de leitura e garantias de prefixo consistentes, em todo o mundo. Para os bancos de dados configurados com consistência forte, os benefícios (baixa latência de gravação, alta disponibilidade de gravação) de várias regiões de gravação não se aplicam, devido à replicação síncrona entre regiões.

A semântica dos cinco modelos de consistência no Cosmos DB é descrita [aqui](consistency-levels.md)e é descrita matematicamente usando uma especificação TLA + de alto nível [aqui](https://github.com/Azure/azure-cosmos-tla).

## <a name="next-steps"></a>Próximas etapas

Em seguida, saiba como configurar a distribuição global usando os seguintes artigos:

* [Adicionar ou remover regiões de sua conta de banco de dados](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Como configurar clientes para multihoming](how-to-manage-database-account.md#configure-multiple-write-regions)
* [Como criar uma política de resolução de conflito personalizada](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)
