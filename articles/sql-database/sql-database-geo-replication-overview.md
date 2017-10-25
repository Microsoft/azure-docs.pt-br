---
title: "Grupos de failover e replicação geográfica ativa - Banco de Dados SQL do Azure | Microsoft Docs"
description: "Grupos de failover automático com replicação geográfica ativa permitem configurar quatro réplicas do banco de dados em qualquer um dos datacenters do Azure e fazer o failover automaticamente caso ocorra uma interrupção."
services: sql-database
documentationcenter: na
author: anosov1960
manager: jhubbard
editor: monicar
ms.assetid: 2a29f657-82fb-4283-9a83-e14a144bfd93
ms.service: sql-database
ms.custom: business continuity
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: NA
ms.date: 10/11/2017
ms.author: sashan
ms.openlocfilehash: 0725d5747ab343dcf99ad8f2dc0e47d7304c9f1e
ms.sourcegitcommit: 54fd091c82a71fbc663b2220b27bc0b691a39b5b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/12/2017
---
# <a name="overview-failover-groups-and-active-geo-replication"></a>Visão geral: grupos de failover e replicação geográfica ativa
A replicação geográfica ativa permite que você configure até quatro bancos de dados secundários legíveis, na mesma localização de centro de dados ou em localizações (regiões) diferentes. Os bancos de dados secundários estão disponíveis para consulta e failover no caso de uma paralisação do data center ou da incapacidade de conectar ao banco de dados primário. O failover deve ser iniciado manualmente pelo aplicativo do usuário. Após o failover, o novo banco de dados primário terá um ponto de extremidade de conexão diferente. 

> [!NOTE]
> A replicação geográfica ativa agora está disponível para todos os bancos de dados em todas as camadas de serviço e em todas as regiões.
>  

Os grupos de failover automático do Banco de Dados SQL do Azure (em versão prévia) são um recurso de Banco de Dados SQL criado para gerenciar automaticamente a relação de replicação geográfica, a conectividade e o failover em larga escala. Com ele, os clientes têm a capacidade de recuperar automaticamente vários bancos de dados relacionados na região secundária após falhas catastróficas regionais ou outros eventos não planejados que resultam em perda total ou parcial de disponibilidade do serviço de Banco de Dados SQL na região primária. Além disso, eles podem usar os bancos de dados secundários legíveis para descarregar cargas de trabalho de somente leitura.  Como os grupos de failover automático incluem vários bancos de dados, eles devem ser configurados no servidor primário. Os servidores primários e secundários devem estar na mesma assinatura. Os grupos de failover automático oferecem suporte à replicação de todos os bancos de dados no grupo para apenas um servidor secundário em uma região diferente. A replicação geográfica ativa, sem grupos de failover automático, permite a existência de até quatro secundários em qualquer região.

Se você estiver usando replicação geográfica ativa e, por algum motivo, o seu banco de dados primário falhar ou simplesmente precisar ser colocado offline, você poderá iniciar o failover para qualquer um dos seus bancos de dados secundários. Quando o failover é ativado para um dos bancos de dados secundários, todos os outros secundários são vinculados automaticamente ao novo primário. Se você estiver usando grupos de failover automático (em versão prévia) para gerenciar a recuperação dos bancos de dados e alguma interrupção que afete um ou vários bancos de dados no grupo resultar em failover automático. Você pode configurar a política de failover automático que melhor atenda às necessidades do seu aplicativo, ou você pode recusar e usar a ativação manual. Além disso, os grupos de failover automático (em versão prévia) fornecem pontos de extremidade de ouvinte de leitura/gravação e somente leitura que permanecem inalterados durante failovers. Não importa se você usa a ativação de failover manual ou automática, o failover alterna todos os bancos de dados secundários no grupo para primário. Após o failover de banco de dados ser concluído, o registro DNS é atualizado automaticamente para redirecionar os pontos de extremidade para a nova região.

Você pode gerenciar a replicação e o failover de um banco de dados individual ou de um conjunto de bancos de dados em um servidor ou em um pool elástico usando a replicação geográfica ativa. É possível fazer isso usando 

- O [Portal do Azure](sql-database-geo-replication-portal.md)
- [PowerShell: banco de dados único](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
- [PowerShell: pool elástico](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
- [PowerShell: grupo de failover](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md)
- [Transact-SQL: banco de dados único ou pool elástico](/sql/t-sql/statements/alter-database-azure-sql-database)
- [API REST: banco de dados único](/rest/api/sql/replicationlinks/failover)
- [API REST: grupo de failover](/rest/api/sql/failovergroups/failover). 
 
Após o failover, verifique se os requisitos de autenticação para o servidor e o banco de dados estão configurados no novo primário. Para obter detalhes, consulte [Segurança do Banco de Dados SQL do Azure após a recuperação de desastre](sql-database-geo-replication-security-config.md). Isso se aplica à replicação geográfica ativa e grupos de failover automático (em versão prévia).

A replicação geográfica ativa aproveita a tecnologia [Always On](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) do SQL Server para replicar de forma assíncrona transações confirmadas no banco de dados primário para um banco de dados secundário usando RCSI (isolamento do instantâneo confirmado por leitura). Os grupos de failover automático fornecem a semântica de grupo além da replicação geográfica ativa, mas o mesmo mecanismo de replicação assíncrona é usado. Embora, a qualquer momento, o banco de dados secundário possa estar um pouco atrás do banco de dados primário, os dados secundários têm a garantia de nunca terem transações parciais. A redundância entre regiões permite que os aplicativos se recuperem rapidamente de uma perda permanente de um datacenter inteiro ou de partes de um datacenter, causada por desastres naturais, falhas humanas catastróficas ou crimes. Os dados específicos de RPO podem ser encontrados em [Visão geral da continuidade de negócios](sql-database-business-continuity.md).

A figura a seguir mostra que um exemplo de replicação geográfica ativa configurada com um banco de dados primário na região Centro-Norte dos EUA e um secundário na região Centro-Sul dos EUA.

![relacionamento de replicação geográfica](./media/sql-database-active-geo-replication/geo-replication-relationship.png)

Como os bancos de dados secundários são legíveis, eles podem ser usados para descarregar cargas de trabalho somente leitura, como trabalhos de relatórios. Se você estiver usando replicação geográfica ativa, é possível criar o banco de dados secundário na mesma região que o primário, mas isso não aumenta a resiliência a falhas catastróficas do aplicativo. Se você estiver usando grupos de failover automático (em versão prévia), seus bancos de dados secundários serão criados sempre em uma região diferente.

Além da recuperação de desastres, a replicação geográfica ativa pode ser usada nos seguintes cenários:

* **Migração de banco de dados**: você pode usar a replicação geográfica ativa para migrar um banco de dados de um servidor para outro online com tempo de inatividade mínimo.
* **Atualizações de aplicativos**: você pode criar um secundário extra como uma cópia de failback durante as atualizações de aplicativos.

Para garantir a continuidade de negócios real, a adição de redundância de banco de dados entre datacenters é apenas parte da solução. A recuperação de um aplicativo (serviço) de ponta a ponta após uma falha catastrófica exige a recuperação de todos os componentes que constituem o serviço e quaisquer serviços dependentes. O software cliente (por exemplo, um navegador com um JavaScript personalizado), front-ends da Web, armazenamento e DNS são exemplos desses componentes. É fundamental que todos os componentes sejam resilientes às mesmas falhas e fiquem disponíveis dentro do RTO (objetivo de tempo de recuperação) de seu aplicativo. Portanto, você precisa identificar todos os serviços dependentes e entender as garantias e os recursos que eles fornecem. Em seguida, você deve tomar as medidas necessárias para garantir que seu serviço funcione durante o failover dos serviços dos quais ele depende. Para obter mais informações sobre como criar soluções para a recuperação de desastre, veja [Criando soluções de nuvem para a recuperação de desastre usando a replicação geográfica ativa](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

## <a name="active-geo-replication-capabilities"></a>Recursos da replicação geográfica ativa
O recurso de replicação geográfica ativa fornece os seguintes recursos essenciais:
* **Replicação Assíncrona Automática**: você só pode criar um banco de dados secundário adicionando-o a um banco de dados existente. O banco de dados secundário só pode ser criado em qualquer servidor de Banco de Dados SQL do Azure. Depois de criado, o banco de dados secundário é preenchido com os dados copiados do banco de dados primário. Este processo é conhecido como propagação. Depois que o banco de dados secundário for criado e propagado, as atualizações para o banco de dados primário serão replicadas de forma assíncrona para o banco de dados secundário automaticamente. A replicação assíncrona significa que as transações são confirmadas no banco de dados primário antes de serem replicadas para o banco de dados secundário. 
* **Bancos de dados secundários legíveis**: um aplicativo pode acessar um banco de dados secundário para operações somente leitura usando as mesmas entidades de segurança usadas para acessar o banco de dados primário, ou outras diferentes. Os bancos de dados secundários operam no modo de isolamento de instantâneo para garantir que a replicação das atualizações do primário (repetição de log) não seja atrasada por consultas executadas no secundário.

   > [!NOTE]
   > A repetição de log será atrasada no banco de dados secundário se houver atualizações de esquema que ele esteja recebendo do Primário que exijam um bloqueio de esquema no banco de dados secundário. 
   > 

* **Vários bancos de dados secundários legíveis**: dois ou mais bancos de dados secundários aumentam a redundância e o nível de proteção para o banco de dados primário e o aplicativo. Se existirem vários bancos de dados secundários, o aplicativo permanecerá protegido mesmo se houver uma falha em um dos bancos de dados secundários. Se houver apenas um banco de dados secundário e ele falhar, o aplicativo será exposto a um risco maior até que um novo banco de dados secundário seja criado.

   > [!NOTE]
   > Se você estiver usando replicação geográfica ativa para compilar um aplicativo distribuído globalmente e precisa fornecer acesso somente leitura aos dados em mais de quatro regiões, poderá criar um banco de dados secundário de outro banco de dados secundário (um processo conhecido como encadeamento). Dessa forma, que você pode obter uma escala praticamente ilimitada de replicação de banco de dados. Além disso, o encadeamento reduz a sobrecarga de replicação do banco de dados primário. A desvantagem é uma maior latência de replicação nos bancos de dados secundários filhos. 
   >

* **Suporte para bancos de dados do pool elástico**: a replicação geográfica ativa pode ser configurada para qualquer banco de dados em qualquer pool elástico. O banco de dados secundário pode estar em outro pool elástico. Para bancos de dados regulares, o secundário pode ser um pool elástico e vice-versa, desde que as camadas de serviço sejam as mesmas. 
* **Nível de desempenho configurável do banco de dados secundário**: os bancos de dados primários e secundários são necessários para ter a mesma camada de serviço (Basic, Standard e Premium). Um banco de dados secundário pode ser criado com o nível de desempenho inferior (DTUs) ao primário. Essa opção não é recomendada para aplicativos com atividade de gravação de banco de dados elevada, pois o retardo maior de replicação aumenta o risco de perda de dados substancial após o failover. Além disso, após o failover, o desempenho do aplicativo será afetado até que o novo primário seja atualizado para um nível mais alto de desempenho. O gráfico de percentual de E/S de log no Portal do Azure fornece uma boa maneira de estimar o nível mínimo de desempenho do banco de dados secundário que será necessário para sustentar a carga de replicação. Por exemplo, se o banco de dados Primário for P6 (1000 DTUS) e seu percentual de E/S de log for 50%, o secundário precisará ser pelo menos P4 (500 DTU). Você também pode recuperar os dados de E/S de log usando as exibições de banco de dados [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) ou [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database).  Para obter mais informações sobre os níveis de desempenho do Banco de Dados SQL, confira [Opções de Banco de Dados SQL e desempenho](sql-database-service-tiers.md). 
* **Failover e failback controlados pelo usuário**: um banco de dados secundário pode ser alternado explicitamente para a função primária a qualquer momento pelo aplicativo ou pelo usuário. Durante uma interrupção real a opção "não planejada" deve ser usada, o que promoverá imediatamente um secundário para primário. Quando o primário com falha se recuperar e estiver disponível novamente, o sistema o marcará automaticamente como um secundário e o atualizará de acordo com o novo primário. Devido à natureza assíncrona da replicação, uma pequena quantidade de dados poderá ser perdida durante failovers não planejados se o primário falhar antes de replicar as alterações mais recentes para o secundário. Quando um primário com vários secundários passar por failover, o sistema automaticamente reconfigurará as relações de replicação e vinculará os secundários restantes para o primário recém-promovido, sem a necessidade de intervenção do usuário. Depois que a interrupção que causou o failover for reduzida, poderá ser desejável retornar o aplicativo para a região primária. Para fazer isso, o comando de failover deve ser invocado com a opção "planejada". 
* **Como manter regras de firewall e credenciais em sincronia**: recomendamos o uso de [regras de firewall de banco de dados](sql-database-firewall-configure.md) para bancos de dados com replicação geográfica, de modo que essas regras possam ser replicadas com o banco de dados para garantir que todos os bancos de dados secundários tenham as mesmas regras de firewall que o primário. Essa abordagem elimina a necessidade de os clientes configurarem manualmente e manterem as regras de firewall nos servidores que hospedam os bancos de dados primários e secundários. Da mesma forma, usar [usuários de banco de dados independente](sql-database-manage-logins.md) para o acesso a dados garante que os bancos de dados primários e secundários sempre tenham as mesmas credenciais de usuário para que, durante failovers, não haja interrupções devido à incompatibilidade nos logons e senhas. Com a adição de [Azure Active Directory](../active-directory/active-directory-whatis.md), os clientes podem gerenciar o acesso do usuário aos bancos de dados primários e secundários, eliminando a necessidade de gerenciamento de credenciais em todos os bancos de dados juntos.

## <a name="auto-failover-group-capabilities"></a>Recursos de grupo de failover automático

O recurso grupos de failover automático fornece uma abstração eficaz de replicação geográfica ativa, dando suporte à replicação em nível de grupo e o failover automático. Além disso, ele retira a necessidade de alterar a cadeia de conexão de SQL após o failover, fornecendo os pontos de extremidade do ouvinte adicional. 

* **Grupo de failover**: Um ou mais grupos de failover podem ser criados entre dois servidores em regiões diferentes (servidores primário e secundário). Cada grupo pode conter um ou vários bancos de dados que são recuperados como uma unidade no caso de alguns ou todos os bancos de dados primários ficarem indisponíveis devido a uma interrupção na região primária.  
* **Servidor primário**: um servidor que hospeda os bancos de dados primários no grupo de failover.
* **Servidor secundário**: um servidor que hospeda os bancos de dados secundários no grupo de failover. O servidor secundário não pode estar na mesma região do servidor primário.
* **Adicionando bancos de dados ao grupo de failover**: Você pode colocar vários bancos de dados em um servidor ou em um pool elástico no mesmo grupo de failover. Se você adicionar um banco de dados independente ao grupo, ele cria automaticamente um banco de dados secundário usando a mesma edição e nível de desempenho. Se o banco de dados primário estiver em um pool elástico, o banco de dados secundário é criado automaticamente no pool elástico com o mesmo nome. Se você adicionar um banco de dados que já possui um banco de dados secundário no servidor secundário, essa replicação geográfica é herdada pelo grupo.

   > [!NOTE]
   > Ao adicionar um banco de dados que já possui um banco de dados secundário em um servidor que não faz parte do grupo de failover, um novo banco de dados secundário é criado no servidor secundário. 
   >

* **Ouvinte de leitura/gravação do grupo de failover**: um registro CNAME de DNS formado como **&lt;nome-do-grupo-de-failover&gt;.database.windows.net** que aponta para a URL do servidor primário atual. Ele permite que os aplicativos de SQL de leitura/gravação se reconectem de forma transparente ao banco de dados primário quando o banco de dados primário for alterado após o failover. 
* **Ouvinte somente leitura do grupo de failover**: um registro CNAME de DNS formado como **&lt;nome-do-grupo-de-failover&gt;.secondary.database.windows.net** que aponta para a URL do servidor secundário. Ele permite que os aplicativos SQL de somente leitura se conectem de forma transparente ao banco de dados secundário usando as regras de balanceamento de carga especificadas. Opcionalmente, você pode especificar se deseja que o tráfego de somente leitura seja redirecionado automaticamente para o servidor primário quando o servidor secundário não está disponível.
* **Política de failover automático**: O grupo de failover está configurado, por padrão, com uma política de failover automático. O sistema dispara o failover assim que a falha é detectada. Se você deseja controlar o fluxo de trabalho de failover do aplicativo, pode desligar o failover automático. 
* **Failover manual**: Você pode iniciar o failover manualmente a qualquer momento, independentemente da configuração de failover automático. Se a política de failover automático não for configurada, será necessário fazer o failover manual para recuperar os bancos de dados no grupo de failover. Você pode iniciar um failover forçado ou amigável (com sincronização total de dados). O failover amigável pode ser usado para realocar o servidor ativo para a região primária. Quando o failover estiver concluído os registros DNS são atualizados automaticamente para garantir a conectividade com o servidor correto.
* **Período de carência para a perda de dados**: como os bancos de dados primários e secundários são sincronizados usando a replicação assíncrona, o failover pode resultar em perda de dados. Você pode personalizar a política de failover automático para refletir a tolerância do seu aplicativo à perda de dados. Ao configurar **GracePeriodWithDataLossHours**, você poderá controlar quanto tempo o sistema aguarda antes de iniciar o failover que provavelmente resultará em perda de dados. 

   > [!NOTE]
   > Quando o sistema detecta que os bancos de dados no grupo ainda estão online (por exemplo, a interrupção só afetou o plano de controle de serviço), ele ativa imediatamente o failover com a sincronização total de dados (failover amigável), independentemente do valor definido por **GracePeriodWithDataLossHours**. Esse comportamento faz com que não haja nenhuma perda de dados durante a recuperação. O período de carência tem efeito somente quando não é possível fazer um failover amigável. Se a interrupção for reduzida antes do período de carência expirar, o failover não está ativado.
   >

* **Vários grupos de failover**: Você pode configurar vários grupos de failover para o mesmo par de servidores para controlar a escala de failovers. Cada grupo sofre failover de forma independente. Se seu aplicativo multilocatário usa pools elásticos, você pode usar esse recurso para misturar os bancos de dados primários e secundários em cada pool. Dessa forma, você pode reduzir o impacto de uma interrupção a somente metade dos locatários.

## <a name="best-practices-of-building-highly-available-service"></a>Práticas recomendadas para a criação de serviço altamente disponível

Para criar um serviço altamente disponível que usa o Banco de Dados SQL do Azure, você deve seguir estas diretrizes:

- **Usar grupo de failover**: Um ou mais grupos de failover podem ser criados entre dois servidores em regiões diferentes (servidores primário e secundário). Cada grupo pode conter um ou vários bancos de dados que são recuperados como uma unidade no caso de alguns ou todos os bancos de dados primários ficarem indisponíveis devido a uma interrupção na região primária. O grupo de failover cria um banco de dados geograficamente secundário com o mesmo objetivo de serviço do primário. Se você adicionar uma relação de replicação geográfica existente ao grupo de failover, verifique se o geograficamente secundário está configurado com o mesmo objetivo de nível de serviço do primário.
- **Usar o ouvinte de leitura/gravação para a carga de trabalho OLTP**: ao executar operações de OLTP use **&lt;nome-do-grupo-de-failover&gt;.database.windows.net** como a URL do servidor, e as conexões são automaticamente direcionadas ao primário. Essa URL não é alterada após o failover.  
Observe que o failover envolve a atualização do registro DNS, para que conexões de cliente sejam redirecionadas ao novo primário somente após a atualização do cliente do cache DNS.
- **Usar o ouvinte somente leitura para cargas de trabalho somente leitura**: se você tiver uma carga de trabalho somente leitura logicamente isolada que seja tolerante a determinadas desatualização dos dados, use o banco de dados secundário no aplicativo. Para sessões somente leitura, use **&lt;nome-do-grupo-de-failover&gt;.secondary.database.windows.net** como a URL do servidor, e a conexão é direcionada automaticamente para o secundário. Também recomendamos que você indique na cadeia de conexão a intenção de leitura usando **ApplicationIntent=ReadOnly**. 
- **Prepare-se para a degradação do desempenho**: a decisão de failover do SQL independe dos demais aplicativos ou outros serviços usados. O aplicativo pode estar "misturado", com alguns componentes em uma região e outros em outra. Para evitar a degradação, garanta a implantação do aplicativo redundante na região de DR e siga as diretrizes neste artigo.  
Observe que o aplicativo na área de recuperação de desastres não precisa usar uma cadeia de conexão diferente.  
- **Preparar a perda de dados**: se uma falha for detectada, o SQL disparará o failover de leitura-gravação se não houver perda de dados, até onde nós sabemos. Caso contrário, ele aguardará o período especificado por **GracePeriodWithDataLossHours**. Se você tiver especificado **GracePeriodWithDataLossHours**, esteja preparado para eventual perda de dados. Em geral, durante interrupções, o Azure favorece a disponibilidade. Se você não puder perder dados, defina **GracePeriodWithDataLossHours** com um número grande o suficiente, como 24 horas. 


## <a name="upgrading-or-downgrading-a-primary-database"></a>Atualizar ou fazer downgrade de um banco de dados primário
Você pode atualizar ou fazer downgrade de um banco de dados primário para um nível de desempenho diferente (dentro da mesma camada de serviço) sem desconectar nenhum banco de dados secundário. Ao atualizar, recomendamos que você atualize primeiro o banco de dados secundário e, depois, atualize o primário. Ao fazer downgrade, inverta a ordem: faça primeiro o downgrade do banco de dados primário e, depois, faça do secundário. Quando você atualiza ou faz downgrade do banco de dados para uma camada de serviço diferente essa recomendação é imposta. 

> [!NOTE]
> Se você tiver criado um banco de dados secundário como parte da configuração do grupo de failover não é recomendável fazer o downgrade do banco de dados secundário. Isso é para garantir que sua camada de dados tenha capacidade suficiente para processar sua carga de trabalho normal após o failover ser ativado. 
>

## <a name="preventing-the-loss-of-critical-data"></a>Evitando a perda de dados críticos
Devido à alta latência das redes de longa distância, a cópia contínua usa um mecanismo de replicação assíncrona. A replicação assíncrona tornará a perda de alguns dados inevitável se ocorrer uma falha. No entanto, alguns aplicativos podem exigir nenhuma perda de dados. Para proteger essas atualizações críticas, um desenvolvedor de aplicativo pode chamar o procedimento de sistema [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) imediatamente após a confirmação da transação. Chamar **sp_wait_for_database_copy_sync** bloqueia o thread de chamada até que a última transação confirmada seja transmitida para o banco de dados secundário. Contudo, a chamada não aguarda as transações transmitidas serem reproduzidas e confirmadas no banco de dados secundário. **sp_wait_for_database_copy_sync** é atribuído a um vínculo de cópia contínua específico. Qualquer usuário com os direitos de conexão para o banco de dados primário pode chamar este procedimento.

> [!NOTE]
> **sp_wait_for_database_copy_sync** impede a perda de dados depois de um failover, mas não garante a sincronização completa para acesso de leitura. A demora causada por uma chamada de procedimento **sp_wait_for_database_copy_sync** pode ser significativa e depende do tamanho do log de transações no momento da chamada. 
> 

## <a name="programmatically-managing-failover-groups-and-active-geo-replication"></a>Gerenciar grupos de failover e a replicação geográfica ativa programaticamente
Conforme discutido anteriormente, os grupos de failover automático (em versão prévia) e a replicação geográfica ativa também podem ser gerenciadas programaticamente usando o Azure PowerShell e a API REST. As tabelas a seguir descrevem o conjunto de comandos disponíveis.

**API do Azure Resource Manager e segurança baseada em funções**: a replicação geográfica ativa inclui um conjunto de APIs do Azure Resource Manager para gerenciamento, incluindo a [API REST do banco de dados SQL](https://docs.microsoft.com/rest/api/sql/) e os [cmdlets do Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview). Essas APIs exigem o uso de grupos de recursos e dão suporte a RBAC (segurança baseada em funções). Para obter mais informações sobre como implementar funções de acesso, confira [Controle de Acesso Baseado em Funções do Azure](../active-directory/role-based-access-control-what-is.md).

## <a name="manage-sql-database-failover-using-transact-sql"></a>Gerenciar failover do Banco de Dados SQL usando Transact-SQL

| Command | Descrição |
| --- | --- |
| [ALTER DATABASE (Banco de Dados SQL do Azure)](/sql/t-sql/statements/alter-database-azure-sql-database) |Use o argumento ADD SECONDARY ON SERVER para criar um banco de dados secundário para um banco de dados existente e inicie a replicação de dados |
| [ALTER DATABASE (Banco de Dados SQL do Azure)](/sql/t-sql/statements/alter-database-azure-sql-database) |Usar o FAILOVER ou FORCE_FAILOVER_ALLOW_DATA_LOSS para alternar um banco de dados secundário para primário a fim de iniciar o failover |
| [ALTER DATABASE (Banco de Dados SQL do Azure)](/sql/t-sql/statements/alter-database-azure-sql-database) |Use REMOVE SECONDARY ON SERVER para encerrar uma replicação de dados entre um Banco de Dados SQL e o banco de dados secundário especificado. |
| [sys.geo_replication_links (Banco de Dados SQL do Azure)](/sql/relational-databases/system-dynamic-management-views/sys-geo-replication-links-azure-sql-database) |Retorna informações sobre todos os links de replicação existentes para cada banco de dados no servidor lógico do Banco de Dados SQL. |
| [sys.dm_geo_replication_link_status (Banco de Dados SQL do Azure)](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) |Obtém a hora da última replicação, latência da última replicação e outras informações sobre o link de replicação para um determinado Banco de Dados SQL. |
| [sys.dm_operation_status (Banco de Dados SQL do Azure)](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) |Mostra o status de todas as operações de banco de dados, incluindo o status dos links de replicação. |
| [sp_wait_for_database_copy_sync (Banco de Dados SQL do Azure)](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) |faz com que o aplicativo espere até que todas as transações confirmadas sejam replicadas e reconhecidas pelo banco de dados secundário ativo. |
|  | |

## <a name="manage-sql-database-failover-using-powershell"></a>Gerenciar failover do Banco de Dados SQL usando PowerShell

| Cmdlet | Descrição |
| --- | --- |
| [Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase) |Obtém um ou mais bancos de dados. |
| [New-AzureRmSqlDatabaseSecondary](/powershell/module/azurerm.sql/new-azurermsqldatabasesecondary) |Cria um banco de dados secundário para um banco de dados existente e inicia a replicação de dados. |
| [Set-AzureRmSqlDatabaseSecondary](/powershell/module/azurerm.sql/set-azurermsqldatabasesecondary) |Alterna um banco de dados secundário para primário a fim de iniciar o failover. |
| [Remove-AzureRmSqlDatabaseSecondary](/powershell/module/azurerm.sql/remove-azurermsqldatabasesecondary) |Encerra uma replicação de dados entre um Banco de Dados SQL e o banco de dados secundário especificado. |
| [Get-AzureRmSqlDatabaseReplicationLink](/powershell/module/azurerm.sql/get-azurermsqldatabasereplicationlink) |Obtém os links de replicação geográfica entre um Banco de Dados SQL do Azure e um grupo de recursos ou do SQL Server. |
| [New-AzureRmSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/set-azurermsqldatabasefailovergroup) |   Esse comando cria um grupo de failover e registra-o nos servidores primário e secundário|
| [Remove-AzureRmSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/remove-azurermsqldatabasefailovergroup) | Remove o grupo de failover do servidor e exclui todos os bancos de dados secundários incluídos no grupo |
| [Get-AzureRmSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/get-azurermsqldatabasefailovergroup) | Recupera a configuração do grupo de failover |
| [Set-AzureRmSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/set-azurermsqldatabasefailovergroup) |   Modifica a configuração do grupo de failover |
| [Switch-AzureRMSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/switch-azurermsqldatabasefailovergroup) | Dispara o failover do grupo de failover para o servidor secundário |
|  | |

> [!IMPORTANT]
> Para ver exemplos de script, consulte [Configure and failover a single database using active geo-replication](scripts/sql-database-setup-geodr-and-failover-database-powershell.md) (Configurar e fazer failover de um banco de dados usando a replicação geográfica ativa), [Configure and failover a pooled database using active geo-replication](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md) (Configurar e fazer failover de um banco de dados em pool usando a replicação geográfica ativa) e [Configure and failover a failover group for a single database (preview)](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md) (Configurar e fazer failover de um grupo de failover para um banco de dados único (versão prévia)).
>

## <a name="manage-sql-database-failover-using-the-rest-api"></a>Gerenciar failover do Banco de Dados SQL usando a API REST
| API | Descrição |
| --- | --- |
| [Criar ou atualizar banco de dados (createMode=Restore)](/rest/api/sql/Databases/CreateOrUpdate) |Cria, atualiza ou restaura um banco de dados primário ou secundário. |
| [Obter, Criar ou Atualizar o Status de um Banco de Dados](/rest/api/sql/Databases/CreateOrUpdate) |Retorna o status durante uma operação de criação. |
| [Definir o banco de dados secundário como primário (Failover planejado)](/rest/api/sql/replicationlinks/failover) |Define qual banco de dados de réplica é primário ao realizar failover do banco de dados de réplica primária atual. |
| [Definir o banco de dados secundário como primário r (Failover não planejado)](/rest/api/sql/replicationlinks/failoverallowdataloss) |Define qual banco de dados de réplica é primário ao realizar failover do banco de dados de réplica primária atual. Esta operação pode resultar em perda de dados. |
| [Obter link de replicação](/rest/api/sql/replicationlinks/get) |Obtém um link de replicação específico para um determinado Banco de Dados SQL em uma parceria de replicação geográfica. Recupera as informações visíveis no modo de exibição de catálogo sys.geo_replication_links. |
| [Links de Replicação - Listar pelo Banco de Dados](/rest/api/sql/replicationlinks/listbydatabase) | Obtém todos os links de replicação para um determinado Banco de Dados SQL em uma parceria de replicação geográfica. Recupera as informações visíveis no modo de exibição de catálogo sys.geo_replication_links. |
| [Excluir links de replicação](/rest/api/sql/databases/delete) | Exclui um link de replicação do banco de dados. Não pode ser feito durante o failover. |
| [Criar ou atualizar grupo de failover](/rest/api/sql/failovergroups/createorupdate) | Criar ou atualizar grupo de failover |
| [Excluir grupo de failover](/rest/api/sql/failovergroups/delete) | Remove o grupo de failover do servidor |
| [Failover (planejado)](/rest/api/sql/failovergroups/failover) | Failover do servidor principal atual para este servidor. |
| [O Failover forçado permite a perda de dados](/rest/api/sql/failovergroups/forcefailoverallowdataloss) |Failover do servidor principal atual para este servidor. Esta operação pode resultar em perda de dados. |
| [Obter grupo de failover](/rest/api/sql/failovergroups/get) | Obtém um grupo de failover. |
| [Listar grupos de failover pelo servidor](/rest/api/sql/failovergroups/listbyserver) | Lista grupos de failover em um servidor. |
| [Atualizar grupo de failover](/rest/api/sql/failovergroups/update) | Atualiza um grupo de failover. |
|  | |

## <a name="next-steps"></a>Próximas etapas
* Para exemplos de scripts, consulte:
   - [Configurar e fazer failover de um banco de dados individual usando replicação geográfica ativa](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
   - [Configurar e fazer failover de um banco de dados em pool usando replicação geográfica ativa](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
   - [Configurar e realizar o failover de um grupo de failover para um banco de dados individual (versão prévia)](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md)
* Para obter uma visão geral e os cenários de continuidade dos negócios, confira [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)
* Para saber mais sobre backups automatizados do Banco de Dados SQL do Azure, confira [Backups automatizados do Banco de Dados SQL](sql-database-automated-backups.md).
* Para saber mais sobre como usar backups automatizados de recuperação, confira [Restaurar um banco de dados de backups iniciados pelo serviço](sql-database-recovery-using-backups.md).
* Para saber mais sobre requisitos de autenticação para um novo servidor primário e banco de dados, consulte [Segurança do Banco de Dados SQL após a recuperação de desastres](sql-database-geo-replication-security-config.md).

