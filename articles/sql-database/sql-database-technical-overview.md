---
title: O que é o serviço do Banco de Dados SQL do Azure? | Microsoft Docs
description: 'Obtenha uma introdução ao Banco de Dados SQL: detalhes e recursos técnicos do RDBMS (sistema de gerenciamento de banco de dado relacional) da Microsoft na nuvem.'
keywords: introdução ao sql, introdução ao sql, o que é o banco de dados sql
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
manager: craigg
ms.date: 04/08/2019
ms.openlocfilehash: ecfd0cbc3eaaae64a956568a506252fdbeddcac2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60580642"
---
# <a name="what-is-azure-sql-database-service"></a>O que é o serviço de banco de dados SQL

O Banco de Dados SQL é um serviço gerenciado de banco de dados relacional de uso geral no Microsoft Azure que dá suporte a estruturas como XML, JSON, espacial e dados relacionais. O Banco de Dados SQL oferece desempenho dinamicamente escalonável em dois modelos de compra diferentes: um modelo de compra baseado em vCore e um modelo de compra baseado em DTU. O Banco de Dados SQL também fornece opções como [índices columnstore](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) para análise extrema e relatórios e [OLTP in-memory](sql-database-in-memory.md) para processamento transacional extremo. A Microsoft trata todos os patches e a atualização da base de código SQL sem interrupções e abstrai todo o gerenciamento da infraestrutura subjacente.

> [!NOTE]
> Para obter um glossário de termos no Banco de Dados SQL do Azure, veja [Glossário de termos de banco de dados SQL](sql-database-glossary-terms.md)

O banco de dados SQL do Azure fornece as seguintes opções de implantação para um banco de dados SQL do Azure:

- Como um [banco de dados individual](sql-database-single-database.md) com seu próprio conjunto de recursos gerenciado por um servidor do Banco de Dados SQL. Um banco de dados individual é semelhante a [bancos de dados independentes](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) no SQL Server.
- Uma [pool Elástico](sql-database-elastic-pool.md), que é uma coleção de bancos de dados com um conjunto compartilhado de recursos gerenciados por meio de um servidor do Banco de Dados SQL. Bancos de dados individuais podem ser movidos para dentro e para fora de um pool elástico.
- [A instância gerenciada](sql-database-managed-instance.md), que é uma coleção de bancos de dados do sistema e do usuário com um conjunto compartilhado de recursos. Uma instância gerenciada é semelhante a uma instância do [mecanismo de banco de dados do Microsoft SQL Server](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation).

A ilustração a seguir mostra essas opções de implantação:

![Opções de implantação](./media/sql-database-technical-overview/deployment-options.png)

O Banco de Dados SQL compartilha a sua base de código com o [mecanismo de banco de dados do Microsoft SQL Server](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation). Com a estratégia de prioridade de nuvem da Microsoft, os recursos mais recentes do SQL Server são liberados primeiro no Banco de Dados SQL e, em seguida, no próprio SQL Server. Essa abordagem fornece a você os recursos mais recentes do SQL Server sem sobrecarga para aplicação de patch ou atualização, e com esses novos recursos testados em milhões de bancos de dados. Para obter informações sobre novos recursos conforme vão são anunciados, consulte:

- **[Mapa do Azure para o Banco de Dados SQL](https://azure.microsoft.com/roadmap/?category=databases)**:

  Um lugar para descobrir quais são as novidades e o que está por vir.

- **[Blog do banco de dados SQL do Azure](https://azure.microsoft.com/blog/topics/database)**:

  Um lugar onde os membros da equipe de produto do SQL Server escrevem sobre novidades e recursos do Banco de Dados SQL.

> [!IMPORTANT]
> Para entender as diferenças de recursos entre o Banco de Dados SQL e o SQL Server, bem como as diferenças entre as opções de implantação de Banco de Dados SQL do Azure, veja [recursos do SQL](sql-database-features.md).

O Banco de Dados SQL oferece desempenho previsível com vários tipos de recursos, camadas de serviço e tamanhos de computação, o que fornece escalabilidade dinâmica sem tempo de inatividade, otimização inteligente interna, escalabilidade e disponibilidade globais e opções avançadas de segurança, tudo com quase nenhuma administração. Esses recursos permitem que você se concentre no desenvolvimento rápido de aplicativos e acelere seu tempo de colocação no mercado, em vez de alocar tempo e recursos preciosos para gerenciamento de máquinas virtuais e infraestrutura. O serviço Banco de Dados SQL está atualmente 38 data centers ao redor do mundo, e mais data centers ficam online regularmente, o que permite a execução do banco de dados em um data center próximo.

## <a name="scalable-performance-and-pools"></a>Pools e desempenho dimensionável

- Com bancos de dados individuais, cada banco de dados é isolado do outro e é portátil, cada um com sua própria quantidade garantida de recursos de computação, memória e armazenamento. O Banco de Dados SQL fornece recursos de computação, memória e armazenamento diferentes para diferentes necessidades – e a capacidade de dinamicamente aumentar e reduzir [os recursos de banco de dados individual](sql-database-single-database-scale.md). A [camada de serviço em hiperescala](sql-database-service-tier-hyperscale.md) (versão prévia) para banco de dados individual permite que você dimensione para 100 TB, com funcionalidades de backup rápido e restauração.
- Com os pools Elásticos, você pode criar novos bancos de dados ou mover bancos de dados individuais em um pool de recursos para maximizar o uso de recursos e economizar dinheiro – e a capacidade de aumentar e reduzir dinamicamente [os recursos do pool elástico](sql-database-elastic-pool-scale.md).
- Com instâncias gerenciadas, cada instância gerenciada é isolada de outras instâncias com recursos garantidos. Em uma instância gerenciada, os bancos de dados da instância compartilham um conjunto de recursos – e a capacidade de aumentar e reduzir [recursos de instância gerenciada](sql-database-managed-instance-resource-limits.md).

Você pode criar seu primeiro aplicativo em um banco de dados individual pequeno com um baixo custo mensal na camada de serviço de uso geral e, depois, alterar a camada de serviço manualmente ou de forma programática a qualquer momento para a camada de serviço comercialmente crítico para atender às necessidades da solução. Você pode ajustar o desempenho sem tempo de inatividade para seu aplicativo ou para seus clientes. A escalabilidade dinâmica permite que o banco de dados responda de forma transparente às mudanças rápidas de requisitos de recursos e que você pague apenas pelos recursos de que precisa, quando precisar deles.

A escalabilidade dinâmica é diferente do dimensionamento automático. O dimensionamento automático é quando um serviço pode ser dimensionado automaticamente com base em critérios, enquanto a escalabilidade dinâmico permite o redimensionamento manual sem tempo de inatividade. Um banco de dados individual dá suporte à escalabilidade dinâmica manual, mas não ao dimensionamento automático. Para uma experiência mais *automática*, considere o uso de pools elásticos, que permitem que os bancos de dados compartilhem recursos em um pool com base nas necessidades individuais do banco de dados. No entanto, há scripts que podem ajudar a automatizar a escalabilidade de um banco de dados individual. Para ver um exemplo, consulte [Usar o PowerShell para monitorar e dimensionar um banco de dados individual](scripts/sql-database-monitor-and-scale-database-powershell.md).

### <a name="purchasing-models-service-tiers-compute-sizes-and-storage-amounts"></a>Modelos de compra, camadas de serviço, tamanhos de computação e quantidades de armazenamento

O Banco de Dados SQL oferece dois modelos de compra:

- O [modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md) oferece uma combinação de computação, memória e recursos de E/S em três camadas de serviço para dar suporte a cargas de trabalho leves e pesadas de banco de dados. Os tamanhos da computação dentro de cada camada fornecem uma mistura diferente desses recursos, aos quais você pode adicionar recursos de armazenamento.
- O [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md) permite que você escolha o número de vCores, a quantidade ou memória e a quantidade e velocidade de armazenamento. O modelo de compra baseado em vCore também permite que você use o [Benefício Híbrido do Azure para SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) para poupar custos. Para obter mais informações sobre o Benefício Híbrido do Azure, consulte as [perguntas frequentes](#sql-database-frequently-asked-questions-faq).

  > [!IMPORTANT]
  > A [camada de serviço de hiperescala](sql-database-service-tier-hyperscale.md) para bancos de dados individuais está atualmente em versão prévia pública. Não recomendamos executar nenhuma carga de trabalho de produção em bancos de dados de hiperescala ainda. Você não pode atualizar um banco de dados de hiperescala para outras camadas de serviço. Para fins de teste, é recomendável fazer uma cópia do banco de dados atual e atualize a cópia para a camada de serviço de hiperescala.

### <a name="elastic-pools-to-maximize-resource-utilization"></a>Pools elásticos para maximizar a utilização de recursos

Para muitas empresas e aplicativos, ser capaz de criar bancos de dados únicos e ajustar o desempenho sob demanda é o suficiente, especialmente se os padrões de uso forem relativamente previsíveis. Mas se você tiver os padrões de uso imprevisíveis, pode ser difícil de gerenciar os custos e o seu modelo de negócios. [Pools elásticos](sql-database-elastic-pool.md) são projetados para resolver esse problema. O conceito é simples. Você aloca recursos de desempenho a um pool em vez de a um banco de dados individual e paga pelos recursos de desempenho coletivo do pool, em vez de pelo desempenho de banco de dados único.

   ![pools elásticos](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

Com os pools elásticos, você não precisa se concentrar em expandir ou reduzir o desempenho do banco de dados conforme a demanda de recursos flutua. Os bancos de dados em pool consomem os recursos de desempenho do pool elástico conforme necessário. Os bancos de dados em pool consomem, mas não excedem os limites do pool. Portanto, o custo permanece previsível, mesmo que o uso do banco de dados individual não permaneça. Além disso, é possível [adicionar e remover bancos de dados do pool](sql-database-elastic-pool-manage-portal.md), escalar seu aplicativo por meio de alguns bancos de dados para milhares, tudo dentro de um orçamento que você pode controlar. Você também pode controlar os recursos mínimos e máximos disponíveis para bancos de dados em pool, para garantir que nenhum deles use todos os recursos do pool e que todos tenham um mínimo de recursos garantido. Para saber mais sobre os padrões de design para aplicativos SaaS usando pools Elásticos, confira [Padrões de Design para aplicativos de SaaS multilocatários com o Banco de Dados SQL](sql-database-design-patterns-multi-tenancy-saas-applications.md).

Scripts podem ajudar com o monitoramento e dimensionamento pools elásticos. Para ver um exemplo, consulte [Usar o PowerShell para monitorar e dimensionar um pool elástico do SQL no Banco de Dados SQL do Azure](scripts/sql-database-monitor-and-scale-pool-powershell.md)

> [!IMPORTANT]
> Uma instância gerenciada não dá suporte a pools elásticos. Em vez disso, uma instância gerenciada é uma coleção de bancos de dados de instância que compartilham recursos de instância gerenciada.

### <a name="blend-single-databases-with-pooled-databases"></a>Combinar bancos de dados individuais a bancos de dados em pool

Você pode mesclar bancos de dados individuais com pools elásticos e alterar as camadas de serviço de bancos de dados individuais e pools elásticos de maneira rápida e fácil para se adaptar à sua situação. Com a potência e o alcance do Azure, você pode combinar e corresponder outros serviços do Azure com o Banco de Dados SQL para atender às suas necessidades exclusivas de design de aplicativo, direcionar as eficiências de recursos e custo, bem como descobrir novas oportunidades de negócios.

### <a name="extensive-monitoring-and-alerting-capabilities"></a>Monitoramento abrangente e recursos de alerta

Use as ferramentas de [monitoramento de desempenho interno](sql-database-performance.md) e [alerta](sql-database-insights-alerts-portal.md) em conjunto com as classificações de desempenho baseadas nos vCores. Usando essas ferramentas, você pode avaliar rapidamente o impacto da expansão ou redução com base nas suas necessidades de desempenho atuais ou de projeto. Além disso, o Banco de Dados SQL pode [emitir métrica e logs de diagnóstico](sql-database-metrics-diag-logging.md) para facilitar o monitoramento. Você pode configurar o Banco de Dados SQL para armazenar o uso de recursos, trabalhos, sessões e conectividade em um destes recursos do Azure:

- **Armazenamento do Azure**: para o arquivamento de grandes quantidades de telemetria por um preço baixo
- **Hub de Eventos do Azure**: para a integração de telemetria de Banco de Dados SQL com a sua solução de monitoramento personalizada ou pipelines ativos
- **Logs do Azure Monitor**: para solução de monitoramento interna com relatórios, alertas e recursos de mitigação.

    ![Arquitetura](./media/sql-database-metrics-diag-logging/architecture.png)

## <a name="availability-capabilities"></a>Recursos de disponibilidade

Em um ambiente tradicional do SQL Server, você normalmente teria (pelo menos) 2 máquinas localmente definidas com cópias exatas de (mantidas de forma síncrona) dos dados (usando recursos como grupos de disponibilidade AlwaysOn ou instâncias de Cluster de Failover) para proteger contra um Falha de um único computador/componente.  Isso fornece alta disponibilidade, mas não protege contra um desastre natural destruir seu data center.

Recuperação de desastres pressupõe que um evento catastrófico serão geograficamente localizados suficiente para ter outra máquina/conjunto de máquinas com uma cópia dos seus dados à mão.  No SQL Server, você pode usar grupos de disponibilidade AlwaysOn em execução no modo assíncrono para obter essa funcionalidade.  A velocidade da luz problemas geralmente significa que as pessoas não deseja aguardar a replicação aconteça que longe antes de confirmar uma transação, portanto, há um potencial para perda de dados ao fazer failovers não planejados.

Tipos de bancos de dados no serviço premium e de negócios essencial já [fazer algo muito semelhante](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) para a sincronização de um grupo de disponibilidade. Bancos de dados em camadas de serviço inferiores fornecem redundância por meio de armazenamento usando um [mecanismo diferente mas equivalente](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability). Há lógica que protege contra uma falha de única máquina.  O recurso de replicação geográfica ativa fornece a capacidade de proteger contra desastres em que uma região inteira é destruída.

As zonas de disponibilidade do Azure é uma opção sobre o problema de alta disponibilidade.  Ele tenta proteger contra a interrupção de um único data center compilar dentro de uma única região.  Portanto, ela deseja proteger contra a perda de energia ou de rede para a criação. No SQL Azure, isso funcionará, colocando as diferentes réplicas nas zonas de disponibilidade diferente (prédios, efetivamente) e funcionando como antes.

Na verdade, a disponibilidade de 99,99% líder no mercado do Azure de contrato de nível de serviço [(SLA)](https://azure.microsoft.com/support/legal/sla/), alimentado por uma rede global de datacenters gerenciada pela Microsoft, ajuda a manter seu aplicativo em execução 24 horas, 7. A plataforma do Azure gerencia completamente cada banco de dados e garante alto percentual de disponibilidade de dados sem perda de dados. O Azure controla automaticamente a aplicação de patches, potenciais falhas subjacentes de hardware, software ou rede, a implantação de correções de bug, failovers, upgrades de banco de dados e outras tarefas de manutenção. A disponibilidade padrão é obtida por uma separação de camadas de computação e armazenamento. A disponibilidade premium é obtida integrando computação e armazenamento em um único nó para desempenho e, em seguida, implementando uma tecnologia semelhante a Always On Availability Groups nas capas. Para conferir uma discussão completa sobre os recursos de alta disponibilidade do Banco de Dados SQL do Azure, veja [Disponibilidade do Banco de Dados SQL](sql-database-high-availability.md). Além disso, o Banco de Dados SQL fornece recursos internos de [continuidade dos negócios e a escalabilidade global](sql-database-business-continuity.md), incluindo:

- **[Backups automáticos](sql-database-automated-backups.md)**:

  Banco de dados SQL executa automaticamente completo, diferencial e backups de log de transações de bancos de dados SQL do Azure para que você possa restaurar para qualquer ponto no tempo. Para bancos de dados individuais e bancos de dados em pool, você pode configurar o banco de dados SQL para armazenar os backups de banco de dados completo para o armazenamento do Azure para retenção de longo prazo. Para instâncias gerenciadas, você também pode executar backups somente cópia para retenção de backup de longo prazo.

- **[Restaurações point-in-time](sql-database-recovery-using-backups.md)**:

  Todas as opções de implantação de Banco de Dados SQL dão suporte à recuperação para qualquer ponto no tempo dentro do período de retenção de backup automático para qualquer banco de dados SQL do Azure.
- **[Replicação geográfica ativa](sql-database-active-geo-replication.md)**:

  Bancos de dados individuais e bancos de dados em pool permitem que você configure até quatro bancos de dados secundários legíveis nos datacenters do Azure iguais ou distribuídos globalmente.  Por exemplo, se você tiver um aplicativo SaaS com um banco de dados de catálogo que tem um alto volume de transações simultâneas somente leitura, use a replicação geográfica ativa para habilitar a escala global de leitura e remover gargalos no primário devido a cargas de trabalho de leitura. Para instâncias gerenciadas, use grupos de failover automático.
- **[Grupos de failover automático](sql-database-auto-failover-group.md)**:

  Todas as opções de implantação de Banco de Dados SQL permitem que você use grupos de failover para habilitar a alta disponibilidade e balanceamento de carga em escala global, incluindo replicação geográfica transparente e failover de grandes conjuntos de instâncias gerenciadas, pools elásticos e bancos de dados. Grupos de failover permitem a criação de aplicativos SaaS globalmente distribuídos com sobrecarga de administração mínima, deixando todos o monitoramento complexos, roteamento e coordenação de failover para o Banco de Dados SQL.
- **[Bancos de dados com redundância de zona](sql-database-high-availability.md)**:

  O Banco de Dados SQL permite provisionar bancos de dados premium ou comercialmente críticos ou pools elásticos em várias zonas de disponibilidade. Como esses bancos de dados e pools elásticos têm várias réplicas de redundância para alta disponibilidade, colocar essas réplicas em várias zonas de disponibilidade fornece maior resiliência, incluindo a capacidade de recuperar automaticamente de falhas de escala do datacenter sem perda de dados.

## <a name="built-in-intelligence"></a>Inteligência interna

Com o Banco de Dados SQL, você obtém inteligência interna que ajuda a reduzir significativamente os custos de execução e gerenciamento de bancos de dados e maximiza o desempenho e a segurança de seu aplicativo. Executando milhões de cargas de trabalho de clientes sem parar, o Banco de Dados SQL coleta e processa uma grande quantidade de dados telemétricos enquanto respeita a privacidade dos clientes nos bastidores. Vários algoritmos estão avaliando os dados telemétricos continuamente para que o serviço possa aprender e se adaptar ao seu aplicativo. Com base nesta análise, o serviço surge com recomendações personalizadas de melhoria de desempenho para sua carga de trabalho específica.

### <a name="automatic-performance-monitoring-and-tuning"></a>Monitoramento e ajuste de desempenho automáticos

O Banco de Dados SQL fornece informações detalhadas sobre as consultas que você precisa monitorar. O Banco de Dados SQL aprende seus padrões de banco de dados e permite adaptar seu esquema de banco de dados à carga de trabalho. O Banco de Dados SQL fornece [recomendações de ajuste de desempenho](sql-database-advisor.md) em que você pode examinar as ações de ajuste e aplicá-las.

No entanto, monitorar o banco de dados é uma tarefa difícil e entediante, especialmente ao lidar com muitos bancos de dados. [Insights inteligentes](sql-database-intelligent-insights.md) faz esse trabalho para você automaticamente monitorando o desempenho do banco de dados SQL em grande escala e informa você sobre problemas de degradação de desempenho, identifica a causa raiz do problema e fornece recomendações de melhoria de desempenho quando possível.

Gerenciar um grande número de bancos de dados pode ser impossível de se fazer com eficiência, mesmo com todas as ferramentas e relatórios disponíveis fornecidos pelo Banco de Dados SQL e pelo portal do Azure. Em vez de monitorar e ajustar o banco de dados manualmente, você pode considerar delegar algumas das ações de monitoramento e ajustes ao Banco de Dados SQL usando o [ajuste automático](sql-database-automatic-tuning.md). O Banco de Dados SQL aplica recomendações, testa e verifica cada uma das suas ações de ajustes automaticamente para garantir a melhoria do desempenho. Dessa forma, o Banco de Dados SQL se adapta à sua carga de trabalho de maneira segura e controlada automaticamente. O ajuste automático significa que o desempenho do banco de dados é monitorado cuidadosamente e comparado antes e depois de cada ação de ajuste, e se o desempenho não melhora, a ação de ajuste é revertida.

Hoje, muitos dos nossos parceiros executando [aplicativos SaaS multilocatários](sql-database-design-patterns-multi-tenancy-saas-applications.md) baseados no Banco de Dados SQL dependem de ajuste automático de desempenho para garantir que seus aplicativos sempre tenham desempenho estável e previsível. Para eles, esse recurso reduz o risco de ter um incidente de desempenho no meio da noite. Além disso, como parte de sua base de clientes também usa o SQL Server, eles estão usando as mesmas recomendações de indexação fornecidas pelo Banco de Dados SQL para ajudar seus clientes do SQL Server.

Há dois aspectos de ajuste automático [disponíveis no Banco de Dados SQL](sql-database-automatic-tuning.md):

- **Gerenciamento de índice automático**: identifica os índices que devem ser adicionados ao seu banco de dados e os que devem ser removidos.
- **Correção automática de plano**: identifica planos problemáticos e corrige problemas de desempenho do plano SQL (em breve. Já disponível no SQL Server 2017).

### <a name="adaptive-query-processing"></a>Processamento de consulta adaptável

Também estamos adicionando a família de recursos de [processamento de consulta adaptável](/sql/relational-databases/performance/intelligent-query-processing) de recursos ao Banco de Dados SQL, incluindo execução intercalada de funções com valor de tabela com várias instruções, comentários de concessão de memória de modo em lotes e junções adaptáveis do modo de lote. Cada um desses recursos de processamento de consulta adaptável aplica técnicas "aprender e adaptar" semelhantes, ajudando a solucionar problemas de otimização de consulta relacionados a problemas de otimização de consultas historicamente problemáticos.

## <a name="advanced-security-and-compliance"></a>Segurança e conformidade avançadas

O Banco de Dados SQL fornece uma variedade de [recursos internos de segurança e conformidade](sql-database-security-overview.md) para ajudar seu aplicativo a atender a vários requisitos de conformidade e segurança.

> [!IMPORTANT]
> Azure SQL Database (todas as opções de implantação) foi certificado em relação a vários padrões de conformidade. Para obter mais informações, consulte o [Microsoft Azure Trust Center](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) onde você pode encontrar a lista mais atual das certificações de conformidade do banco de dados SQL.

### <a name="advance-threat-protection"></a>Proteção Avançada contra Ameaças

A segurança de dados avançada é um pacote unificado de funcionalidades avançadas de segurança do SQL. Ela inclui funcionalidades para descobrir e classificar dados confidenciais, gerenciar vulnerabilidades de seu banco de dados e detectar atividades irregulares que possam indicar uma ameaça ao seu banco de dados. Fornece um local único para habilitar e gerenciar esses recursos.

- [Descoberta e classificação de dados](sql-database-data-discovery-and-classification.md):

  Esse recurso (atualmente em pré-visualização) fornece recursos incorporados no Banco de Dados SQL do Azure para descobrir, classificar, rotular e proteger os dados confidenciais em seus bancos de dados. Pode ser usada para fornecer visibilidade em seu estado de classificação do banco de dados e para controlar o acesso a dados confidenciais no banco de dados e, além de suas bordas.
- [Avaliação de vulnerabilidade](sql-vulnerability-assessment.md):

  Esse serviço pode descobrir, controlar e ajudá-lo a corrigir possíveis vulnerabilidades de banco de dados. Fornece visibilidade sobre o estado de segurança e inclui etapas acionáveis para resolver problemas de segurança e aperfeiçoar as fprtificações do banco de dados.
- [Detecção de ameaças](sql-database-threat-detection.md):

  Esse recurso detecta atividades anômalas que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar seu banco de dados. Monitora continuamente o banco de dados com relação a atividades suspeitas e fornece alertas de segurança imediata sobre vulnerabilidades potenciais, ataques de injeção de SQL e padrões de acesso anormal do banco de dados. Os alertas da detecção de ameaças fornecem detalhes de atividades suspeitas e recomendam ação de como investigar e atenuar a ameaça.

### <a name="auditing-for-compliance-and-security"></a>Auditoria de segurança e conformidade

A [Auditoria](sql-database-auditing.md) acompanha eventos do banco de dados e os grava em um log de auditoria na sua conta de armazenamento do Azure. A auditoria pode ajudar você a manter uma conformidade regulatória, a entender a atividade do banco de dados e a obter informações sobre discrepâncias e anomalias que poderiam indicar preocupações de negócios ou suspeitas de violações de segurança.

### <a name="data-encryption"></a>Criptografia de dados

O Banco de Dados SQL protege seus dados fornecendo criptografia para os dados em movimento com o [protocolo TLS](https://support.microsoft.com/kb/3135244), para os dados em repouso com a [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) e para os dados em uso com o [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine).

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Integração do Azure Active Directory e autenticação multifator

O Banco de Dados SQL permite gerenciar centralmente as identidades de usuário do banco de dados e de outros serviços da Microsoft com a [integração do Azure Active Directory](sql-database-aad-authentication.md). Esse recurso simplifica o gerenciamento de permissão e aprimora a segurança. O Azure Active Directory é compatível com [MFA](sql-database-ssms-mfa-authentication.md) (autenticação multifator) para aumentar a segurança de aplicativos e dados enquanto dá suporte a um processo de logon único.

### <a name="compliance-certification"></a>Certificação de conformidade

O Banco de Dados SQL participa de auditorias regulares e foi certificado por vários padrões de conformidade. Para obter mais informações, consulte o [Microsoft Azure Trust Center](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) onde você pode encontrar a lista mais atual das certificações de conformidade do banco de dados SQL.

## <a name="easy-to-use-tools"></a>Ferramentas fáceis de usar

O Banco de Dados SQL torna a compilação e o gerenciamento de aplicativos mais fáceis e produtivos. O Banco de Dados SQL permite a você focar no que há de melhor: compilar aplicativos ótimos. Você pode gerenciar e desenvolver no Banco de Dados SQL usando ferramentas e técnicas já existentes.

- **[O portal do Azure](https://portal.azure.com/)**:

  Um aplicativo baseado na Web para gerenciar todos os serviços do Azure
- **[SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)**:

  Um aplicativo cliente gratuito e transferível para gerenciar qualquer infraestrutura SQL, do SQL Server ao Banco de Dados SQL
- **[SQL Server Data Tools no Visual Studio](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)**:

  Um aplicativo cliente gratuito que pode ser baixado para desenvolver bancos de dados relacionais do SQL Server, bancos de dados SQL do Azure, pacotes do Integration Services, modelos de dados do Analysis Services e relatórios do Reporting Services.
- **[Visual Studio Code](https://code.visualstudio.com/docs)**:

  Um editor de código gratuito, baixável e de código aberto para Windows, macOS e Linux que suporta extensões, incluindo a [extensão mssql](https://aka.ms/mssql-marketplace) para consultar o Microsoft SQL Server, o Banco de Dados SQL do Azure e o SQL Data Warehouse.

O Banco de Dados SQL dá suporte à criação de aplicativos com Python, Java, Node.js, PHP, Ruby e .NET no MacOS, Linux e Windows. O Banco de Dados SQL dá suporte às mesmas [bibliotecas de conexão](sql-database-libraries.md) do SQL Server.

## <a name="sql-database-frequently-asked-questions-faq"></a>Perguntas Frequentes do Banco de Dados SQL (FAQ)

### <a name="what-is-the-current-version-of-sql-database"></a>Qual é a versão atual do banco de dados SQL

A versão atual do banco de dados SQL é V12. Versão V11 foi desativado.

### <a name="can-i-control-when-patching-downtime-occurs"></a>É possível controlar quando ocorre tempo de inatividade de patch

 Não. O impacto de patch geralmente não será perceptível, se você [empregar lógica de repetição](sql-database-develop-overview.md#resiliency) no aplicativo. Para obter mais informações sobre como se preparar para eventos de manutenção planejada em seu banco de dados SQL do Azure, consulte [planejamento de eventos de manutenção do Azure no Banco de Dados SQL do Azure](sql-database-planned-maintenance.md).

### <a name="azure-hybrid-benefit-questions"></a>Perguntas do Benefício Híbrido do Azure

#### <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>Há direitos de uso duplo com o Benefício Híbrido do Azure para o SQL Server

Você tem 180 dias de direitos de uso duplo da licença para garantir que as migrações estejam executando perfeitamente. Após esse período de 180 dias, a licença do SQL Server somente poderá ser utilizada na nuvem no Banco de Dados SQL e não há direitos de uso duplo no local e na nuvem.

#### <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>Como o Benefício Híbrido do Azure para SQL Server difere da mobilidade de licenças?

Atualmente, oferecemos os benefícios de mobilidade de licença para clientes do SQL Server com Software Assurance, o que permite a reatribuição de suas licenças a servidores de terceiros compartilhados. Esse benefício pode ser usado na IaaS do Azure e no AWS EC2.
O Benefício Híbrido do Azure para SQL Server difere da mobilidade de licenças em duas áreas principais:

- Ele fornece benefícios econômicos para mover cargas de trabalho altamente virtualizadas para o Azure. Os clientes do SQL EE podem obter 4 núcleos no Azure na SKU de Uso Geral para cada núcleo que possuem no local para aplicativos altamente virtualizados. A mobilidade de licenças não permite nenhum benefício de custo especial para mover cargas de trabalho virtualizadas para a nuvem.
- Ele fornece para um destino de PaaS no Azure (Instância Gerenciada do Banco de Dados SQL) que é altamente compatível com o SQL Server local

#### <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>Há direitos de uso duplo com o Benefício Híbrido do Azure para o SQL Server

Clientes do Banco de Dados SQL terão os direitos associados ao Benefício Híbrido do Azure para SQL Server a seguir:

|Licença de volume|O que o Benefício Híbrido do Azure para SQL Server oferece?|
|---|---|
|Clientes principais do SQL Server Enterprise Edition com SA|<li>É possível pagar Taxa Base em SKUs de Uso Geral ou Comercialmente Crítico</li><br><li>1 núcleo local = 4 núcleos na SKU de Uso Geral</li><br><li>1 núcleo local = 1 núcleo em SKU para Comercialmente Crítico</li>|
|Clientes principais do SQL Server Standard Edition com SA|<li>É possível pagar somente Taxa Base em SKU de Uso Geral</li><br><li>1 núcleo local = 1 núcleo na SKU de Uso Geral</li>|
|||

## <a name="engage-with-the-sql-server-engineering-team"></a>Envolver-se com a equipe de engenharia do SQL Server

- [DBA Stack Exchange](https://dba.stackexchange.com/questions/tagged/sql-server): faça perguntas de administração de banco de dados
- [Stack Overflow](https://stackoverflow.com/questions/tagged/sql-server): faça perguntas sobre desenvolvimento
- [Fóruns do MSDN](https://social.msdn.microsoft.com/Forums/home?category=sqlserver): faça perguntas técnicas
- [Comentários](https://aka.ms/sqlfeedback): relate bugs e solicite recursos
- [Reddit](https://www.reddit.com/r/SQLServer/): discuta o SQL Server

## <a name="next-steps"></a>Próximas etapas

- Confira a [página de preços](https://azure.microsoft.com/pricing/details/sql-database/) para ver comparações de custos entre o banco de dados único e pools elásticos e calculadoras.
- Veja estes inícios rápidos para começar:

  - [Criar um banco de dados SQL no portal do Azure](sql-database-single-database-get-started.md)  
  - [Criar um banco de dados SQL com a CLI do Azure](sql-database-get-started-cli.md)
  - [Criar um banco de dados SQL usando o PowerShell](sql-database-get-started-powershell.md)

- Para ver vários exemplos da CLI do Azure e do PowerShell, consulte:
  - [Exemplos da CLI do Azure para o Banco de Dados SQL do Azure](sql-database-cli-samples.md)
  - [Exemplos do Azure PowerShell para o Banco de Dados SQL do Azure](sql-database-powershell-samples.md)
