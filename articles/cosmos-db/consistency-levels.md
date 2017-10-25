---
title: "Níveis de consistência no Azure Cosmos DB | Microsoft Docs"
description: "O Azure Cosmos DB tem cinco níveis de consistência que ajudam a equilibrar prós e contras de consistência eventual, disponibilidade e latência."
keywords: "consistência eventual, azure cosmos db, Microsoft azure"
services: cosmos-db
author: mimig1
manager: jhubbard
editor: cgronlun
documentationcenter: 
ms.assetid: 3fe51cfa-a889-4a4a-b320-16bf871fe74c
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: mimig
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a1ebec2285982c70aa9dc49950769fe18e2e2d0d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tunable-data-consistency-levels-in-azure-cosmos-db"></a>Níveis ajustáveis de consistência de dados no Azure Cosmos DB
O Azure Cosmos DB é projetado desde o início pensando em distribuição global para cada modelo de dados. Ele se destina a oferecer garantias de baixa latência previsível, um SLA de disponibilidade de 99,99% e vários modelos flexíveis de consistência bem definidos. Atualmente, o Azure Cosmos DB fornece cinco níveis de consistência: forte, desatualização limitada, sessão, prefixo consistente e eventual. 

Além dos modelos de consistência **forte** e **eventual** geralmente oferecidos por bancos de dados distribuídos, o Azure Cosmos DB oferece mais três modelos de consistência cuidadosamente codificados e operacionalizados, e validou sua utilidade em casos de uso do mundo real. Estes são os níveis de consistência: **desatualização limitada**, **sessão** e **prefixo consistente**. Coletivamente, esses cinco níveis de consistência permitem obter um equilíbrio bem pensado entre consistência, disponibilidade e latência. 

## <a name="distributed-databases-and-consistency"></a>Consistência e bancos de dados distribuídos
Os bancos de dados distribuídos comercialmente se enquadram em duas categorias: bancos de dados que não oferecem opções de consistência bem definidas e comprovadas e bancos de dados que oferecem duas opções de programação extrema (consistência forte versus consistência eventual). 

A primeira sobrecarrega os desenvolvedores de aplicativos com detalhes de seus protocolos de replicação e espera que eles façam compensações difíceis entre consistência, disponibilidade, latência e taxa de transferência. A segunda opção coloca uma pressão para escolher entre os dois extremos. Apesar da abundância de pesquisas e propostas de mais de 50 modelos de consistência, a comunidade de banco de dados distribuído não tem sido capaz de comercializar níveis de consistência diferentes da consistência forte e consistência eventual. O Cosmos DB permite escolher entre cinco modelos de consistência bem-definidos, juntamente com o espectro de consistência: forte, desatualização limitada, [sessão](http://dl.acm.org/citation.cfm?id=383631), prefixo consistente e eventual. 

![O Azure Cosmos DB oferece várias opções de modelos de consistência bem definidos (flexíveis) para sua escolha](./media/consistency-levels/five-consistency-levels.png)

A tabela a seguir ilustra as garantias específicas que cada nível de consistência oferece.
 
**Níveis de consistência e garantias**

| Nível de Consistência | Garantias |
| --- | --- |
| Strong | Transação atômica |
| Bounded staleness | Prefixo consistente. Lê latência por trás de gravações por meio de prefixos k ou intervalos t |
| Session   | Prefixo consistente. Leituras monotônicas, gravações monotônicas, read-your-writes (operações de leitura refletem gravações anteriores), write-follows-reads (gravações são propagadas após as leituras) |
| Prefixo consistente | As atualizações retornadas são algum prefixo de todas as atualizações, sem intervalos |
| Eventual  | Leituras fora de ordem |

Você pode configurar o nível de consistência padrão na sua conta do Cosmos DB (e mais tarde, substituir a consistência em uma solicitação de leitura específica). Internamente, o nível de consistência padrão aplica-se aos dados nos conjuntos de partições que podem abranger regiões. Cerca de 73% dos nossos locatários usam a consistência de sessão e 20% preferem desatualização limitada. Observamos que aproximadamente 3% de nossos clientes experimentam vários níveis de consistência inicialmente antes de escolherem uma opção de consistência específica para seu aplicativo. Também observamos que apenas 2% de nossos locatários substituem os níveis de consistência por solicitação. 

No Cosmos DB, leituras que atuam em consistência de sessão, prefixo consistente e eventual são duas vezes mais econômicas que leituras com consistência forte ou de desatualização limitada. O Cosmos DB tem SLAs abrangente de 99,99% líderes do setor, incluindo garantias de consistência junto com disponibilidade, taxa de transferência e latência. Utilizamos um [verificador de transação atômica](http://dl.acm.org/citation.cfm?id=1806634), que opera continuamente em nossa telemetria do serviço e relata de maneira aberta quaisquer violações de consistência a você. Para desatualização limitada, monitoramos e relatamos quaisquer violações de limites k e t. Para todos os cinco níveis de consistência reduzida, podemos também relatar a [métrica probabilística de desatualização limitada](http://dl.acm.org/citation.cfm?id=2212359) diretamente a você.  

## <a name="scope-of-consistency"></a>Escopo de consistência
A granularidade da consistência engloba uma única solicitação de usuário. Uma solicitação de gravação pode corresponder a uma transação de inserir, substituir, upsert ou excluir. Assim como nas gravações, uma transação de leitura/consulta também tem como escopo uma única solicitação de usuário. O usuário pode ter que paginar um amplo conjunto de resultados, abrangendo várias partições, mas cada transação de leitura engloba uma única página e é servida de dentro de uma única partição.

## <a name="consistency-levels"></a>Níveis de consistência
Você pode configurar um nível de consistência padrão na conta do banco de dados que se aplica a todas as coleções (e os bancos de dados) em sua conta do Cosmos DB. Por padrão, todas as leituras e consultas emitidas para os recursos definidos pelo usuário usarão o nível de consistência padrão especificado na conta do banco de dados. Você pode relaxar o nível de consistência de uma solicitação de leitura/consulta específica usado em cada uma das APIs com suporte. Há suporte para cinco tipos de nível de consistência no protocolo de replicação do Azure Cosmos DB que fornecem uma clara compensação entre garantias de consistência específica e desempenho, conforme descrito abaixo.

**Strong**: 

* A coerência forte oferece uma garantia de [transação atômica](https://aphyr.com/posts/313-strong-consistency-models), assegurando que as leituras retornarão a versão mais recente de um item. 
* a consistência Strong garante que uma gravação fique visível somente depois de confirmada permanentemente pela maioria do quorum de réplicas. Uma gravação é confirmada de modo síncrono e permanente pelo quorum primário e secundário, ou é anulada. Uma leitura sempre é confirmada pela maioria do quorum de leitura. Um cliente nunca pode ver uma gravação não confirmada ou parcial, e sempre há a garantia de leitura da última gravação confirmada. 
* As contas do Azure Cosmos DB que são configuradas para usar coerência forte não podem associar mais de uma região do Azure à respectiva conta do Azure Cosmos DB. 
* O custo de uma operação de leitura (em termos de [unidades de solicitação](request-units.md) consumidas) com coerência forte é maior do que com sessão e eventual, mas igual ao de obsolescência vinculada.

**Bounded staleness**: 

* A consistência desatualização limitada garante que as leituras possam não acompanhar as gravações até, no máximo, os prefixos ou as versões *K* de um item ou intervalo de tempo *t*. 
* Portanto, quando escolher desatualização limitada, a "desatualização" pode ser configurada de duas maneiras: número de versões *K* do item pelo qual as leituras fiquem atrás das gravações e o intervalo de tempo *t* 
* A desatualização limitada oferece ordem global total, exceto na "janela de desatualização". Há garantias de leitura monotônica em uma região tanto dentro quanto fora da "janela de desatualização". 
* A bounded staleness oferece garantia de consistência mais forte do que session ou eventual. Para aplicativos distribuídos globalmente, é recomendável usar bounded staleness para cenários em que deseja ter consistência forte, mas também 99,99% de disponibilidade e baixa latência. 
* As contas do Azure Cosmos DB configuradas com a consistência de desatualização limitada podem associar qualquer número de regiões do Azure à respectiva conta do Azure Cosmos DB. 
* O custo de uma operação de leitura (em termos de RUs consumidas) com obsolescência vinculada é maior do que com sessão e eventual, mas igual ao da coerência forte.

**Session**: 

* Ao contrário dos modelos globais de consistência oferecidos pelos níveis de consistência strong e bounded staleness, a consistência session engloba uma sessão de cliente. 
* A consistência session é ideal para todos os cenários em que há o envolvimento de um dispositivo ou uma sessão de usuário, uma vez que ela garante leituras monotônicas, gravações monotônicas e RYW (leitura de suas próprias gravações). 
* A consistência session oferece consistência previsível para uma sessão, além de taxa de transferência de leitura máxima, ao mesmo tempo que oferece gravações e leituras de latência mais baixa. 
* As contas do Azure Cosmos DB configuradas com a consistência de sessão podem associar qualquer número de regiões do Azure à respectiva conta do Azure Cosmos DB. 
* O custo de uma operação de leitura (em termos de RUs consumidas) com nível de consistência session é menor do que com strong e bounded staleness, mas maior do que com a consistência eventual

<a id="consistent-prefix"></a>
**Prefixo consistente**: 

* O prefixo consistente garante que, na ausência de qualquer gravação adicional, as réplicas no grupo acabem convergindo. 
* O prefixo consistente garante que as leituras nunca vejam gravações fora de ordem. Se as gravações tiverem sido realizadas na ordem `A, B, C`, o cliente verá `A`, `A,B` ou `A,B,C`, mas nunca fora de ordem, como `A,C` ou `B,A,C`.
* As contas do Azure Cosmos DB configuradas com a consistência de prefixo consistente podem associar qualquer número de regiões do Azure à respectiva conta do Azure Cosmos DB. 

**Eventual**: 

* A consistência eventual garante que, na ausência de qualquer gravação adicional, as réplicas no grupo acabem convergindo. 
* A consistência eventual é a forma mais fraca de consistência, em que um cliente pode obter valores que sejam mais antigos do que aqueles que tinha visto antes.
* A consistência Eventual oferece a consistência de leitura mais fraca, mas oferece a menor latência para leituras e gravações.
* As contas do Azure Cosmos DB configuradas com a consistência eventual podem associar qualquer número de regiões do Azure à respectiva conta do Azure Cosmos DB. 
* O custo de uma operação de leitura (em termos de RUs consumidas) com o nível de consistência eventual é o mais baixo de todos os níveis de consistência do Azure Cosmos DB.

## <a name="configuring-the-default-consistency-level"></a>Configurando o nível de consistência padrão
1. No [Portal do Azure](https://portal.azure.com/), na barra esquerda, clique em **Azure Cosmos DB**.
2. Na folha **Azure Cosmos DB**, escolha a conta do banco de dados a ser modificada.
3. Na folha da conta, clique em **Consistência padrão**.
4. Na folha **Consistência Padrão**, selecione o novo nível de consistência e clique em **Salvar**.
   
    ![Captura de tela realçando o ícone Configurações e a entrada Consistência Padrão](./media/consistency-levels/database-consistency-level-1.png)

## <a name="consistency-levels-for-queries"></a>Níveis de consistência para consultas
Por padrão, para recursos definidos pelo usuário, o nível de consistência para consultas é o mesmo nível de consistência para leituras. Por padrão, o índice é atualizado de maneira síncrona em cada inserção, substituição ou exclusão de um item no contêiner do Cosmos DB. Isso permite que as consultas sigam o mesmo nível de consistência que as leituras de ponto. Embora o Azure Cosmos DB seja otimizado para gravação e tenha suporte para volumes constantes de gravações, manutenção síncrona de índice e atendimento a consultas consistentes, você pode configurar determinadas coleções para atualizar o próprio índice sem pressa. A indexação lenta aumenta ainda mais o desempenho de gravação, sendo ideal para cenários de ingestão em massa em que uma carga de trabalho é basicamente de leitura intensa.  

| Modo de indexação | Leituras | Consultas |
| --- | --- | --- |
| Consistente (padrão) |Selecione entre forte, desatualização limitada, sessão, prefixo consistente ou eventual |Escolher entre strong, bounded staleness, session ou eventual |
| Lentidão |Selecione entre forte, desatualização limitada, sessão, prefixo consistente ou eventual |Eventual |
| Nenhum |Selecione entre forte, desatualização limitada, sessão, prefixo consistente ou eventual |Não aplicável |

Assim como nas solicitações de leitura, você pode diminuir o nível de consistência de uma solicitação de consulta específica em cada API.

## <a name="next-steps"></a>Próximas etapas
Se você quiser ler mais sobre níveis de consistência e tradeoffs, recomendamos os seguintes recursos:

* Doug Terry. Replicated Data Consistency explained through baseball (vídeo).   
  [https://www.youtube.com/watch?v=gluIh8zd26I](https://www.youtube.com/watch?v=gluIh8zd26I)
* Doug Terry. Replicated Data Consistency explained through baseball.   
  [http://research.microsoft.com/pubs/157411/ConsistencyAndBaseballReport.pdf](http://research.microsoft.com/pubs/157411/ConsistencyAndBaseballReport.pdf)
* Doug Terry. Session Guarantees for Weakly Consistent Replicated Data.   
  [http://dl.acm.org/citation.cfm?id=383631](http://dl.acm.org/citation.cfm?id=383631)
* Daniel Abadi. Consistency Tradeoffs in Modern Distributed Database Systems Design: CAP is only part of the story”.   
  [http://computer.org/csdl/mags/co/2012/02/mco2012020037-abs.html](http://computer.org/csdl/mags/co/2012/02/mco2012020037-abs.html)
* Peter Bailis, Shivaram Venkataraman, Michael J. Franklin, Joseph M. Hellerstein, Ion Stoica. Probabilistic Bounded Staleness (PBS) para quóruns parciais práticos.   
  [http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
* Werner Vogels. Eventual Consistent - Revisited.    
  [http://allthingsdistributed.com/2008/12/eventually_consistent.html](http://allthingsdistributed.com/2008/12/eventually_consistent.html)
* Moni Naor, Avishai Wool, The Load, Capacity, and Availability of Quorum Systems, SIAM Journal on Computing, v.27 n.2, p.423-447, abril de 1998.
  [http://epubs.siam.org/doi/abs/10.1137/S0097539795281232](http://epubs.siam.org/doi/abs/10.1137/S0097539795281232)
* Sebastian Burckhardt, Chris Dern, Macanal Musuvathi, Roy Tan, Line-up: a complete and automatic linearizability checker, Proceedings of the 2010 ACM SIGPLAN conference on Programming language design and implementation, 05-10 de junho de 2010, Toronto, Ontário, Canadá [doi>10.1145/1806596.1806634] [http://dl.acm.org/citation.cfm?id=1806634](http://dl.acm.org/citation.cfm?id=1806634)
* Peter Bailis, Shivaram Venkataraman, Michael J. Franklin, Joseph M. Hellerstein, Ion Stoica, Probabilistically bounded staleness for practical partial quorums, Proceedings of the VLDB Endowment, v.5 n.8, p.776-787, abril de 2012 [http://dl.acm.org/citation.cfm?id=2212359](http://dl.acm.org/citation.cfm?id=2212359)
