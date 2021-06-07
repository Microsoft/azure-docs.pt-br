---
title: O que é o serviço do Banco de Dados SQL do Azure?
description: 'Obtenha uma introdução ao Banco de Dados SQL: detalhes e recursos técnicos do RDBMS da Microsoft na nuvem.'
keywords: introdução ao sql, introdução ao sql, o que é o banco de dados sql
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=3
ms.devlang: ''
ms.topic: overview
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/21/2020
ms.openlocfilehash: 65710ae40a971d214068f0e2686f78fb994a967e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104601373"
---
# <a name="what-is-azure-sql-database"></a>O que é o Banco de Dados SQL do Azure?
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

O Banco de Dados SQL do Azure é um mecanismo de banco de dados de PaaS (plataforma como serviço) totalmente gerenciado que trata da maioria das funções de gerenciamento de banco de dados, como atualização, aplicação de patches, backups e monitoramento, sem nenhum envolvimento do usuário. O Banco de Dados SQL do Azure sempre está em execução na última versão estável do mecanismo de banco de dados do SQL Server e no sistema operacional com patches com 99,99% de disponibilidade. As funcionalidades de PaaS internas do Banco de Dados SQL do Azure permitem que você se concentre nas atividades de administração e otimização de banco de dados específicas do domínio que são essenciais para sua empresa.

Com o Banco de Dados SQL do Azure, você pode criar uma camada de armazenamento de dados altamente disponível e de alto desempenho para aplicativos e soluções no Azure. O Banco de Dados SQL do Azure pode ser a escolha certa para uma série de aplicativos de nuvem modernos porque permite processar dados relacionais e [estruturas não relacionais](../multi-model-features.md), como grafos, JSON, espacial e XML.

Ele se baseia na última versão estável do [mecanismo de banco de dados do Microsoft SQL Server](/sql/sql-server/sql-server-technical-documentation?toc=%2fazure%2fsql-database%2ftoc.json). É possível usar recursos avançados de processamento de consulta, como [tecnologias de alto desempenho na memória](../in-memory-oltp-overview.md) e [processamento inteligente de consulta](/sql/relational-databases/performance/intelligent-query-processing?toc=%2fazure%2fsql-database%2ftoc.json). De fato, as funcionalidades mais recentes do SQL Server são lançadas primeiro para o Banco de Dados SQL e, depois, para o SQL Server. Você recebe as funcionalidades mais recentes do SQL Server sem a sobrecarga de aplicar patches ou atualizar, testados em milhões de bancos de dados. 

O Banco de Dados SQL permite definir e escalar facilmente o desempenho dinamicamente escalonável em dois modelos de compra diferentes: um [modelo de compra baseado em vCore](service-tiers-vcore.md) e um [modelo de compra baseado em DTU](service-tiers-dtu.md). O Banco de Dados SQL é um serviço totalmente gerenciado que tem alta disponibilidade interna, backups e outras operações de manutenção comuns. A Microsoft trata de todas as correções e atualizações do código do sistema operacional e do SQL. Não é preciso gerenciar a infraestrutura subjacente.

Se você não está familiarizado com o Banco de Dados SQL do Azure, confira o vídeo *Visão geral do Banco de Dados SQL do Azure* da nossa [série detalhada de vídeos do SQL do Azure](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners?WT.mc_id=azuresql4beg_azuresql-ch9-niner):
> [!VIDEO https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/Azure-SQL-Database-Overview-7-of-61/player]

> [!TIP]
> Como podemos aprimorar o SQL do Azure? [Responda à pesquisa](https://microsoft.qualtrics.com/jfe/form/SV_ePOznHhP4gDKfGu?channel=456).

## <a name="deployment-models"></a>Modelos de implantação

O Banco de Dados SQL do Azure fornece as seguintes opções de implantação para um banco de dados:

- O [banco de dados individual](single-database-overview.md) representa um banco de dados isolado e totalmente gerenciado. É possível usar essa opção com aplicativos de nuvem modernos e microsserviços que precisam de uma única fonte de dados confiável. Um banco de dados individual é semelhante a um [banco de dados independente](/sql/relational-databases/databases/contained-databases?toc=%2fazure%2fsql-database%2ftoc.json) no [mecanismo de banco de dados do SQL Server](/sql/sql-server/sql-server-technical-documentation?toc=%2fazure%2fsql-database%2ftoc.json).
- O [pool elástico](elastic-pool-overview.md) é uma coleção de [bancos de dados individuais](single-database-overview.md) com um conjunto compartilhado de recursos, como CPU ou memória. Bancos de dados individuais podem ser movidos para dentro e para fora de um pool elástico.

> [!IMPORTANT]
> Para entender as diferenças de recursos entre o Banco de Dados SQL e o SQL Server, bem como as diferenças entre as opções do Banco de Dados SQL do Azure, confira [Recursos do Banco de Dados SQL](features-comparison.md).

O Banco de Dados SQL do Microsoft Azure fornece desempenho previsível com vários tipos de recursos, camadas de serviço e tamanhos de computação. Ele fornece escalabilidade dinâmica sem tempo de inatividade, otimização inteligente interna e escalabilidade e disponibilidade globais, assim como opções de segurança avançadas. Esses recursos permitem que você se concentre no desenvolvimento rápido de aplicativos e acelere seu tempo de colocação no mercado, em vez de gerenciar máquinas virtuais e infraestrutura. No momento, o Banco de Dados SQL está em 38 datacenters em todo o mundo, para que você possa executar seu banco de dados em um datacenter perto de você.

## <a name="scalable-performance-and-pools"></a>Pools e desempenho dimensionável

É possível definir a quantidade de recursos atribuídos. 
- Com bancos de dados individuais, cada um deles é isolado de outros e é portátil. Cada uma tem sua própria quantidade garantida de recursos de computação, memória e armazenamento. A quantidade de recursos atribuídos ao banco de dados é dedicada a esse banco de dados, e não é compartilhada com outros bancos de dados no Azure. É possível [dimensionar dinamicamente recursos de bancos de dados individuais](single-database-scale.md) para cima e para baixo. A opção de banco de dados individual fornece diferentes recursos de computação, memória e armazenamento para diferentes necessidades. Por exemplo, é possível obter de 1 a 80 vCores ou 32 GB a 4 TB. A [camada de serviço de hiperescala](service-tier-hyperscale.md) para bancos de dados individuais permite que você escale-os para 100 TB, com funcionalidades de backup e restauração rápidos.
- Com os pools elásticos, é possível atribuir recursos que são compartilhados por todos os bancos de dados no pool. É possível criar um novo banco de dados ou movê-los para um pool de recursos para maximizar o uso de recursos e economizar. Essa opção também oferece a possibilidade de [dimensionar dinamicamente os recursos do pool elástico](elastic-pool-scale.md) para cima e para baixo.

É possível criar seu primeiro aplicativo em um banco de dados pequeno e único com um baixo custo por mês na camada de serviço de uso geral. É possível mudar sua camada de serviço manual ou programaticamente a qualquer momento para a camada de serviço comercialmente crítica, para atender às necessidades da sua solução. Você pode ajustar o desempenho sem tempo de inatividade para seu aplicativo ou para seus clientes. A escalabilidade dinâmica permite que o banco de dados responda de forma transparente a mudanças rápidas nos requisitos de recursos. Você paga apenas pelos recursos de que precisa, quando precisa deles.

A *escalabilidade dinâmica* é diferente do *dimensionamento automático*. O dimensionamento automático é quando um serviço pode ser dimensionado automaticamente com base em critérios, enquanto a escalabilidade dinâmico permite o redimensionamento manual sem tempo de inatividade. A opção de banco de dados individual permite escalabilidade dinâmica manual, mas não dimensionamento automático. Para uma experiência mais automática, considere o uso de pools elásticos, que permitem que os bancos de dados compartilhem recursos em um pool com base nas necessidades individuais do banco de dados. Outra opção é usar scripts que podem ajudar a automatizar a escalabilidade de um banco de dados individual. Para ver um exemplo, consulte [Usar o PowerShell para monitorar e dimensionar um banco de dados individual](scripts/monitor-and-scale-database-powershell.md).

### <a name="purchasing-models"></a>Modelos de compra

O Banco de Dados SQL do Azure oferece os seguintes modelos de compra:
- O [modelo de compra baseado em vCore](service-tiers-vcore.md) permite que você escolha o número de vCores, a quantidade ou memória e a quantidade e velocidade de armazenamento. O modelo de compra baseado em vCore também permite que você use o [Benefício Híbrido do Azure para SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) para poupar custos. Para obter mais informações sobre o Benefício Híbrido do Azure, consulte a seção "Perguntas frequentes" mais adiante neste artigo.
- O [modelo de compra baseado em DTU](service-tiers-dtu.md) oferece uma mistura de computação, memória e recursos de E/S em três camadas de serviço para dar suporte a cargas de trabalho leves e pesadas de banco de dados. Os tamanhos da computação dentro de cada camada fornecem uma mistura diferente desses recursos, aos quais você pode adicionar recursos de armazenamento.
- O [modelo sem servidor](serverless-tier-overview.md) dimensiona automaticamente a computação com base na demanda de carga de trabalho e cobra pela quantidade de computação usada por segundo. A camada de computação sem servidor também pausa os bancos de dados automaticamente durante períodos inativos quando apenas o armazenamento é cobrado e retoma automaticamente os bancos de dados quando a atividade retorna.

### <a name="service-tiers"></a>Camadas de serviço

O Banco de Dados SQL do Microsoft Azure oferece três camadas de serviço que são projetadas para diferentes tipos de aplicativos:
- Camada de serviço de [Uso Geral/Standard](service-tier-general-purpose.md) projetada para cargas de trabalho comuns. Oferece opções equilibradas de computação e armazenamento orientadas ao orçamento.
- Camada de serviço [Comercialmente Crítico/Premium](service-tier-business-critical.md) projetada para aplicativos OLTP com alta taxa de transação e E/S de latência mais baixa. Oferece maior resiliência a falhas usando várias réplicas isoladas.
- Camada de serviço de [Hiperescala](service-tier-hyperscale.md) projetada para um banco de dados OLTP muito grande, assim como para fazer o dimensionamento automático e dimensionar a computação de maneira fluida.    

### <a name="elastic-pools-to-maximize-resource-utilization"></a>Pools elásticos para maximizar a utilização de recursos

Para muitas empresas e aplicativos, ser capaz de criar bancos de dados únicos e ajustar o desempenho sob demanda é o suficiente, especialmente se os padrões de uso forem relativamente previsíveis. Padrões de uso imprevisíveis podem dificultar o gerenciamento dos custos e do seu modelo de negócios. [Pools elásticos](elastic-pool-overview.md) são projetados para resolver esse problema. Você aloca recursos de desempenho para um pool, não para um banco de dados individual. Você paga pelos recursos de desempenho coletivo do pool, não pelo desempenho de um banco de dados individual.

   ![Gráfico que mostra pools elásticos nas edições Basic, Standard e Premium](./media/sql-database-paas-overview/sqldb_elastic_pools.png)

Com os pools elásticos, não é preciso se concentrar em expandir ou reduzir o desempenho do banco de dados conforme a demanda de recursos flutua. Os bancos de dados em pool consomem os recursos de desempenho do pool elástico conforme necessário. Os bancos de dados em pool consomem, mas não excedem os limites do pool. Portanto, o custo permanece previsível, mesmo que o uso do banco de dados individual não permaneça.

É possível [adicionar e remover bancos de dados do pool](elastic-pool-overview.md), escalar seu aplicativo por meio de alguns bancos de dados para milhares, tudo dentro de um orçamento que pode ser controlado. Também é possível controlar os recursos mínimos e máximos disponíveis para bancos de dados em pool, para garantir que nenhum deles use todos os recursos do pool e que todos tenham um mínimo de recursos garantido. Para saber mais sobre os padrões de design para aplicativos SaaS usando pools elásticos, confira [Padrões de Design para aplicativos de SaaS multilocatários com o Banco de Dados SQL](saas-tenancy-app-design-patterns.md).

Scripts podem ajudar com o monitoramento e dimensionamento pools elásticos. Para obter um exemplo, confira [Usar o PowerShell para monitorar e escalar um pool elástico no Banco de Dados SQL do Azure](scripts/monitor-and-scale-pool-powershell.md).


### <a name="blend-single-databases-with-pooled-databases"></a>Combinar bancos de dados individuais a bancos de dados em pool

É possível mesclar bancos de dados individuais com pools elásticos e mudar as camadas de serviço de bancos de dados individuais e pools elásticos para se adaptar à situação. Também é possível combinar e corresponder outros serviços do Azure com o Banco de Dados SQL para atender às suas necessidades exclusivas de design de aplicativo, direcionar a eficiência de recursos e custo, bem como descobrir novas oportunidades de negócios.

## <a name="extensive-monitoring-and-alerting-capabilities"></a>Monitoramento abrangente e recursos de alerta

O Banco de Dados SQL do Azure fornece recursos avançados de monitoramento e solução de problemas que ajudam a obter informações mais aprofundadas sobre as características da carga de trabalho. Esses recursos e ferramentas incluem:
 - As funcionalidades de monitoramento internas fornecidas pela última versão do mecanismo de banco de dados do Microsoft SQL Server. Eles permitem que você encontre informações de desempenho em tempo real. 
 - Recursos de monitoramento de PaaS fornecidos pelo Azure que permitem que você monitore e solucione problemas de um grande número de instâncias do banco de dados.

[Repositório de Consultas](/sql/relational-databases/performance/best-practice-with-the-query-store), um recurso interno de monitoramento de SQL Server, registra o desempenho de suas consultas em tempo real e permite que você identifique os possíveis problemas de desempenho e os principais consumidores de recursos. O ajuste automático e as recomendações apresentam conselhos sobre as consultas com o desempenho regressivo e índices ausentes ou duplicados. O ajuste automático no Banco de Dados SQL do Microsoft Azure permite aplicar manualmente os scripts que podem corrigir os problemas ou deixar que o Banco de Dados SQL do Azure aplique a correção. O Banco de Dados SQL do Azure também pode testar e verificar se a correção oferece algum benefício e reter ou reverter a mudança dependendo do resultado. Além dos recursos de Repositório de Consultas e de ajuste automático, é possível usar [DMVs e XEvent](monitoring-with-dmvs.md) para monitorar o desempenho da carga de trabalho.

O Azure fornece ferramentas de [monitoramento de desempenho integrado](performance-guidance.md) e [alertas](alerts-insights-configure-portal.md), combinados com classificações de desempenho, que permitem monitorar o status de milhares de bancos de dados. Usando essas ferramentas, é possível avaliar rapidamente o impacto da expansão ou redução com base nas necessidades de desempenho atuais ou de projeto. Além disso, o Banco de Dados SQL pode [emitir métrica e logs de recursos](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) para facilitar o monitoramento. Você pode configurar o Banco de Dados SQL para armazenar o uso de recursos, trabalhos, sessões e conectividade em um destes recursos do Azure:

- **Armazenamento do Azure**: para o arquivamento de grandes quantidades de telemetria por um preço baixo.
- **Hubs de Eventos do Azure**: Para a integração de telemetria de Banco de Dados SQL com a sua solução de monitoramento personalizada ou pipelines ativos.
- **Logs do Azure Monitor**: Para solução de monitoramento interna com relatórios, alertas e recursos de mitigação.

![Diagrama da arquitetura de monitoramento do Azure](./media/sql-database-paas-overview/architecture.png)

## <a name="availability-capabilities"></a>Recursos de disponibilidade

O Banco de Dados SQL do Microsoft Azure permite que sua empresa continue a operar durante as interrupções. Em um ambiente de SQL Server tradicional, normalmente há pelo menos duas máquinas configuradas localmente. Essas máquinas têm cópias exatas dos dados em sincronia para proteger contra uma falha de um único computador ou componente. Esse ambiente fornece alta disponibilidade, mas não protege contra um desastre natural em seu datacenter.

A recuperação de desastres pressupõe que um evento catastrófico seja geograficamente localizado para ter outro computador ou conjunto de computadores com uma cópia dos seus dados distante. No SQL Server, é possível usar Grupos de Disponibilidade AlwaysOn no modo assíncrono para obter esse recurso. Muitas vezes, o usuário não quer esperar que a replicação ocorra antes de confirmar uma transação, portanto, há potencial para perda de dados quando você faz failovers não planejados.

Os bancos de dados nas camadas de serviço Premium e Comercialmente Crítico já [fazem algo semelhante](high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) à sincronização de um grupo de disponibilidade. Os bancos de dados em camadas de serviço inferiores fornecem redundância por meio do armazenamento usando um [mecanismo diferente, mas equivalente](high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability). A lógica interna ajuda a proteger contra a falha de uma máquina única. O recurso de replicação geográfica ativa permite proteger contra desastres em que uma região inteira é destruída.

O recurso Zonas de Disponibilidade do Azure tenta proteger contra a interrupção de um único datacenter criado dentro de uma única região. Ele ajuda a proteger contra a perda de energia ou rede em um prédio. No Banco de Dados SQL, você coloca as diferentes réplicas em diferentes zonas de disponibilidade (prédios diferentes, na verdade).

Na verdade, o [SLA](https://azure.microsoft.com/support/legal/sla/) (Contrato de Nível de Serviço) do Azure, que é líder do setor e é alimentado por uma rede global de datacenters gerenciados pela Microsoft, ajuda a manter seu aplicativo em execução de maneira ininterrupta. A plataforma do Azure gerencia completamente cada banco de dados e garante alto percentual de disponibilidade de dados sem perda de dados. O Azure controla automaticamente a aplicação de patches, potenciais falhas subjacentes de hardware, software ou rede, a implantação de correções de bug, failovers, upgrades de banco de dados e outras tarefas de manutenção. A disponibilidade padrão é obtida por uma separação de camadas de computação e armazenamento. A disponibilidade Premium é obtida integrando computação e armazenamento em um único nó para desempenho e, em seguida, implementando uma tecnologia semelhante a Grupos de Disponibilidade AlwaysOn. Para conferir uma discussão completa sobre os recursos de alta disponibilidade do Banco de Dados SQL do Azure, veja [Disponibilidade do Banco de Dados SQL](high-availability-sla.md). 

Além disso, o Banco de Dados SQL fornece recursos internos de [continuidade dos negócios e a escalabilidade global](business-continuity-high-availability-disaster-recover-hadr-overview.md). Elas incluem:

- [Backups automáticos](automated-backups-overview.md):

  O Banco de Dados SQL executa automaticamente backups completos, diferenciais e de log de transações de bancos de dados para que você possa restaurá-los em qualquer ponto no tempo. Para bancos de dados individuais e bancos de dados em pool, é possível configurar o Banco de Dados SQL do Azure para armazenar os backups completos de bancos de dados para o Armazenamento do Microsoft Azure para retenção de longo prazo. Para instâncias gerenciadas, você também pode executar backups somente cópia para retenção de backup de longo prazo.

- [Restaurações pontuais](recovery-using-backups.md):

  Todas as opções de implantação do Banco de Dados SQL dão suporte à recuperação em qualquer ponto no tempo dentro do período de retenção de backup automático para qualquer banco de dados.
- [Replicação geográfica ativa](active-geo-replication-overview.md):

  As opções de bancos de dados individuais e bancos de dados em pool permitem que você configure até quatro bancos de dados secundários legíveis nos datacenters do Azure iguais ou distribuídos globalmente. Por exemplo, se você tiver um aplicativo SaaS com um banco de dados de catálogo que tem um alto volume de transações simultâneas somente leitura, use a replicação geográfica ativa para habilitar a escala de leitura global. Isso remove gargalos no primário que são devido a cargas de trabalho de leitura. Para instâncias gerenciadas, use grupos de failover automático.
- [Grupos de failover automático](auto-failover-group-overview.md):

  Todas as opções de implantação do Banco de Dados SQL do Azure permitem que você use grupos de failover para habilitar a alta disponibilidade e o balanceamento de carga em escala global. Isso inclui a replicação geográfica transparente e o failover de grandes conjuntos de bancos de dados, pools elásticos e instâncias gerenciadas. Os grupos de failover permitem a criação de aplicativos SaaS distribuídos globalmente, com sobrecarga de administração mínima. Isso deixa toda a orquestração de monitoramento, roteamento e failover complexos para o Banco de Dados SQL do Azure.
- [Bancos de dados com redundância de zona](high-availability-sla.md):

  O Banco de Dados SQL permite provisionar bancos de dados Premium ou Business Critical ou pools elásticos em várias zonas de disponibilidade. Como esses bancos de dados e pools elásticos têm várias réplicas de redundância para alta disponibilidade, colocar essas réplicas em várias zonas de disponibilidade oferece maior resiliência. Isso inclui a capacidade de recuperar automaticamente de falhas de escala do datacenter sem perda de dados.

## <a name="built-in-intelligence"></a>Inteligência interna

Com o Banco de Dados SQL do Azure, você obtém inteligência integrada que ajuda a reduzir significativamente os custos de execução e gerenciamento de bancos de dados e maximiza o desempenho e a segurança de seu aplicativo. Executando milhões de cargas de trabalho de clientes sem parar, o Banco de Dados SQL coleta e processa uma grande quantidade de dados telemétricos enquanto respeita a privacidade dos clientes. Vários algoritmos estão avaliando os dados telemétricos continuamente para que o serviço possa aprender e se adaptar ao seu aplicativo.

### <a name="automatic-performance-monitoring-and-tuning"></a>Monitoramento e ajuste de desempenho automáticos

O Banco de Dados SQL fornece informações detalhadas sobre as consultas que você precisa monitorar. O Banco de Dados SQL do Azure aprende seus padrões de banco de dados e permite adaptar o esquema deles à carga de trabalho. O Banco de Dados SQL fornece [recomendações de ajuste de desempenho](database-advisor-implement-performance-recommendations.md) em que você pode examinar as ações de ajuste e aplicá-las.

No entanto, o monitoramento constante de um banco de dados é uma tarefa difícil e entediante, principalmente quando você está lidando com muitos bancos de dados. O [Intelligent Insights](intelligent-insights-overview.md) faz esse trabalho automaticamente monitorando o desempenho do Banco de Dados SQL em larga escala. Ele informa você sobre problemas de degradação de desempenho, identifica a causa raiz de cada problema e apresenta recomendações de aprimoramento de desempenho quando possível.

Gerenciar um grande número de bancos de dados pode ser impossível de se fazer com eficiência, mesmo com todas as ferramentas e relatórios disponíveis fornecidos pelo Banco de Dados SQL e pelo Azure. Em vez de monitorar e ajustar o banco de dados manualmente, considere delegar algumas das ações de monitoramento e ajustes ao Banco de Dados SQL do Azure usando o [ajuste automático](automatic-tuning-overview.md). O Banco de Dados SQL do Azure aplica recomendações, testa e verifica cada uma das suas ações de ajustes automaticamente para garantir a melhoria do desempenho. Dessa forma, o Banco de Dados SQL do Azure se adapta à sua carga de trabalho de maneira segura e controlada automaticamente. O ajuste automático significa que o desempenho do banco de dados é monitorado cuidadosamente e comparado antes e depois de cada ação de ajuste. Se o desempenho não melhorar, a ação de ajuste será revertida.

Muitos dos nossos parceiros executando [aplicativos SaaS multilocatários](saas-tenancy-app-design-patterns.md) baseados no Banco de Dados SQL do Azure dependem de ajuste automático de desempenho para garantir que seus aplicativos sempre tenham desempenho estável e previsível. Para eles, esse recurso reduz o risco de ter um incidente de desempenho no meio da noite. Além disso, como parte de sua base de clientes também usa o SQL Server, eles estão usando as mesmas recomendações de indexação fornecidas pelo Banco de Dados SQL para ajudar seus clientes do SQL Server.

Há dois aspectos de ajuste automático [disponíveis no Banco de Dados SQL do Azure](automatic-tuning-overview.md):

- **Gerenciamento automático de índice**: identifica os índices que devem ser adicionados ao seu banco de dados e os que devem ser removidos.
- **Correção automática de plano**: Identifica planos problemáticos e corrige problemas de desempenho do plano SQL.

### <a name="adaptive-query-processing"></a>Processamento de consulta adaptável

Também é possível usar [processamento de consulta adaptável](/sql/relational-databases/performance/intelligent-query-processing), incluindo execução intercalada de funções com valor de tabela com várias instruções, comentários de concessão de memória em modo de lote e junções adaptáveis do modo de lote. Cada um desses recursos de processamento de consulta adaptável aplica técnicas "aprender e adaptar" semelhantes, ajudando a solucionar problemas de otimização de consulta relacionados a problemas de otimização de consultas historicamente problemáticos.

## <a name="advanced-security-and-compliance"></a>Segurança e conformidade avançadas

O Banco de Dados SQL fornece uma variedade de [recursos internos de segurança e conformidade](../../active-directory/identity-protection/concept-identity-protection-security-overview.md) para ajudar seu aplicativo a atender a vários requisitos de conformidade e segurança.

> [!IMPORTANT]
> A Microsoft certificou o Banco de Dados SQL do Azure (todas as opções de implantação) contra vários padrões de conformidade. Para saber mais, confira a [Central de Confiabilidade do Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942), onde é possível encontrar a lista mais atualizada de certificações de conformidade do Banco de Dados SQL do Azure.

### <a name="advance-threat-protection"></a>Proteção avançada contra ameaças

O Azure Defender para SQL é um pacote unificado de funcionalidades avançadas de segurança do SQL. Ele inclui funcionalidades para gerenciar as vulnerabilidades do seu banco de dados e detectar atividades anormais que possam indicar uma ameaça ao banco de dados. Fornece um local único para habilitar e gerenciar essas funcionalidades.

- [Avaliação de vulnerabilidade](sql-vulnerability-assessment.md):

  Esse serviço pode descobrir, controlar e ajudá-lo a corrigir possíveis vulnerabilidades de banco de dados. Fornece visibilidade sobre o estado de segurança e inclui etapas de ação para resolver problemas de segurança e aperfeiçoar as fortificações do banco de dados.
- [Detecção de ameaças](threat-detection-configure.md):

  Esse recurso detecta atividades anômalas que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar seu banco de dados. Monitora continuamente o banco de dados com relação a atividades suspeitas e fornece alertas de segurança imediatos sobre possíveis vulnerabilidades, ataques de injeção de SQL e padrões de acesso anormal do banco de dados. Os alertas da proteção contra ameaças fornecem detalhes de atividades suspeitas e recomendam ações para investigar e atenuar a ameaça.

### <a name="auditing-for-compliance-and-security"></a>Auditoria de segurança e conformidade

A [Auditoria](../../azure-sql/database/auditing-overview.md) acompanha eventos do banco de dados e os grava em um log de auditoria na sua conta de armazenamento do Azure. A auditoria pode ajudar você a manter uma conformidade regulatória, a entender a atividade do banco de dados e a obter informações sobre discrepâncias e anomalias que poderiam indicar preocupações de negócios ou suspeitas de violações de segurança.

### <a name="data-encryption"></a>Criptografia de dados

O Banco de Dados SQL ajuda a proteger seus dados com criptografia. Para dados em movimento, ele usa [segurança da camada de transporte](https://support.microsoft.com/kb/3135244). Para dados em repouso, ele usa [criptografia de dados transparente](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). Para os dados em uso, ele usa o [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine).

### <a name="data-discovery-and-classification"></a>Descoberta e classificação de dados

A [descoberta e a classificação de dados](data-discovery-and-classification-overview.md) oferecem funcionalidades internas no Banco de Dados SQL do Azure para descobrir, classificar, rotular e proteger os dados confidenciais dos seus bancos de dados. Pode ser usado para fornecer visibilidade em seu estado de classificação do banco de dados e para controlar o acesso a dados confidenciais no banco de dados e além de seus limites.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Integração do Azure Active Directory e autenticação multifator

O Banco de Dados SQL permite gerenciar centralmente as identidades de usuário do banco de dados e de outros serviços da Microsoft com a [integração do Azure Active Directory](authentication-aad-overview.md). Esse recurso simplifica o gerenciamento de permissão e aprimora a segurança. O Azure Active Directory é compatível com [autenticação multifator](authentication-mfa-ssms-overview.md) para aumentar a segurança de aplicativos e dados enquanto dá suporte a um processo de logon único.

## <a name="easy-to-use-tools"></a>Ferramentas fáceis de usar

O Banco de Dados SQL torna a compilação e o gerenciamento de aplicativos mais fáceis e produtivos. O Banco de Dados SQL permite a você focar no que há de melhor: compilar aplicativos ótimos. É possível gerenciar e desenvolver no Banco de Dados SQL do Azure usando ferramentas e técnicas já existentes.

|Ferramenta|Descrição|
|:---|:---|
|[O portal do Azure](https://portal.azure.com/)|Um aplicativo baseado na Web para gerenciar todos os serviços do Azure.|
|[Azure Data Studio](/sql/azure-data-studio/)|Uma ferramenta de banco de dados multiplataforma executada no Windows, no macOS e no Linux.|
|[SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)|Um aplicativo cliente gratuito e transferível para gerenciar qualquer infraestrutura SQL, do SQL Server ao Banco de Dados SQL.|
|[SQL Server Data Tools no Visual Studio](/sql/ssdt/download-sql-server-data-tools-ssdt)|Um aplicativo cliente gratuito que pode ser baixado para desenvolver bancos de dados relacionais do SQL Server, bancos de dados do Banco de Dados SQL do Azure, pacotes do Integration Services, modelos de dados do Analysis Services e relatórios do Reporting Services.|
|[Visual Studio Code](https://code.visualstudio.com/docs)|Um editor de código gratuito de software livre que pode ser baixado para Windows, macOS e Linux. Ele dá suporte a extensões, incluindo a [extensão mssql](https://aka.ms/mssql-marketplace) para consultar o Microsoft SQL Server, o Banco de Dados SQL do Azure e o Azure Synapse Analytics.|

O Banco de Dados SQL permite a criação de aplicativos com Python, Java, Node.js, PHP, Ruby e .NET no macOS, Linux e Windows. O Banco de Dados SQL dá suporte às mesmas [bibliotecas de conexão](connect-query-content-reference-guide.md#libraries) do SQL Server.

[!INCLUDE [sql-database-create-manage-portal](../includes/sql-database-create-manage-portal.md)]

## <a name="sql-database-frequently-asked-questions"></a>Perguntas Frequentes do Banco de Dados SQL

### <a name="can-i-control-when-patching-downtime-occurs"></a>É possível controlar quando ocorre tempo de inatividade na aplicação de patches?

Não. O impacto de patch geralmente não será perceptível, se você [empregar lógica de repetição](develop-overview.md#resiliency) no aplicativo. Para mais informações, consulte [Planejando eventos de manutenção do Azure – Banco de Dados SQL do Azure](planned-maintenance.md).



## <a name="engage-with-the-sql-server-engineering-team"></a>Envolver-se com a equipe de engenharia do SQL Server

- [DBA Stack Exchange](https://dba.stackexchange.com/questions/tagged/sql-server): Faça perguntas sobre a administração de banco de dados.
- [Stack Overflow](https://stackoverflow.com/questions/tagged/sql-server): Faça perguntas sobre desenvolvimento.
- [Página de perguntas e respostas da Microsoft](/answers/topics/azure-synapse-analytics.html): Faça perguntas técnicas.
- [Comentários](https://aka.ms/sqlfeedback): Relate bugs e solicite recursos.
- [Reddit](https://www.reddit.com/r/SQLServer/): Discuta o SQL Server.

## <a name="next-steps"></a>Próximas etapas

- Veja a [página de preços](https://azure.microsoft.com/pricing/details/sql-database/) para conferir comparações de custos e calculadoras de bancos de dados individuais e pools elásticos.
- Veja estes guias de início rápido para começar:

  - [Criar um banco de dados no Portal do Azure](single-database-create-quickstart.md)  
  - [Criar um banco de dados com a CLI do Azure](az-cli-script-samples-content-guide.md)
  - [Criar um banco de dados usando o PowerShell](powershell-script-content-guide.md)

- Para ver vários exemplos da CLI do Azure e do PowerShell, consulte:
  - [Exemplos da CLI do Azure para o Banco de Dados SQL do Azure](az-cli-script-samples-content-guide.md)
  - [Exemplos do Azure PowerShell para o Banco de Dados SQL do Azure](powershell-script-content-guide.md)

- Para obter informações sobre novos recursos conforme são anunciados, consulte [Roteiro do Azure para Banco de Dados SQL](https://azure.microsoft.com/roadmap/?category=databases).
- Consulte o [blog do Banco de Dados SQL do Azure](https://azure.microsoft.com/blog/topics/database): um local em que membros da equipe de produto do SQL Server postam sobre notícias e recursos do Banco de Dados SQL.