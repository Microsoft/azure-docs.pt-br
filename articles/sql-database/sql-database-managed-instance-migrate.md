---
title: Migrar banco de dados da instância SQL Server para a instância gerenciada do banco de dados SQL do Azure | Microsoft Docs
description: Saiba como migrar um banco de dados de SQL Server instância para instância gerenciada do banco de dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: douglas, carlrab
manager: craigg
ms.date: 11/07/2019
ms.openlocfilehash: 0fa65454702c67d4b0baeedc7f412ccec402ea46
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302297"
---
# <a name="sql-server-instance-migration-to-azure-sql-database-managed-instance"></a>Migração de uma Instância do SQL Server para uma Instância Gerenciada do Banco de Dados SQL do Azure

Neste artigo, você aprenderá sobre os métodos para migrar uma Instância do SQL Server 2005 ou versão posterior para uma [Instância Gerenciada do Banco de Dados SQL do Azure](sql-database-managed-instance.md). Para obter informações sobre como migrar para um banco de dados individual ou pool elástico, confira [Migrar para um banco de dados individual ou em pool](sql-database-cloud-migrate.md). Para obter informações de migração sobre como migrar de outras plataformas, confira o [Guia de Migração de Banco de Dados do Azure](https://datamigration.microsoft.com/).

> [!NOTE]
> Se você quiser iniciar e experimentar rapidamente Instância Gerenciada, convém ir para o guia de [início rápido](sql-database-managed-instance-quickstart-guide.md) , em vez desta página. 

Em um nível alto, o processo de migração de banco de dados se parece com:

![processo de migração](./media/sql-database-managed-instance-migration/migration-process.png)

- [Avalie a compatibilidade da instância gerenciada](#assess-managed-instance-compatibility) , na qual você deve garantir que não haja problemas de bloqueio que possam impedir suas migrações.
  - Esta etapa também inclui a criação de [linha de base de desempenho](#create-performance-baseline) para determinar o uso de recursos em sua instância de SQL Server de origem. Essa etapa será necessária se você quiser que a implantação seja corretamente dimensionada Instância Gerenciada e verifique se o desempenho após a migração não é afetado.
- [Escolher opções de conectividade do aplicativo](sql-database-managed-instance-connect-app.md)
- [Implante em uma instância gerenciada de tamanho ideal, na](#deploy-to-an-optimally-sized-managed-instance) qual você escolherá as características técnicas (número de vCores, quantidade de memória) e camada de desempenho (comercialmente crítico uso geral) do seu instância gerenciada.
- [Selecione o método de migração e migre](#select-migration-method-and-migrate) o local em que você migra seus bancos de dados usando a migração offline (backup/restauração nativa, importação/exportação de banco de dados) ou migração online (serviço de migração de serviços, replicação transacional).
- [Monitore os aplicativos](#monitor-applications) para garantir que você tem desempenho esperado.

> [!NOTE]
> Para migrar um banco de dados individual para um banco de dados individual ou um pool elástico, confira [Migrar um banco de dados do Microsoft SQL Server para o Banco de Dados SQL do Azure](sql-database-single-database-migrate.md).

## <a name="assess-managed-instance-compatibility"></a>Avaliar a compatibilidade da instância gerenciada

Primeiro, determine se a instância gerenciada é compatível com os requisitos de banco de dados do aplicativo. A opção de implantação de instância gerenciada foi projetada para fornecer migração lift-and-shift fácil para a maioria dos aplicativos existentes que usam o SQL Server local ou em máquinas virtuais. No entanto, às vezes é necessário recursos ou capacidades que ainda não têm suporte e o custo de implementar uma solução alternativa é muito alto.

Utilize o [DMA (Assistente de Migração de Dados)](https://docs.microsoft.com/sql/dma/dma-overview) para detectar possíveis problemas de compatibilidade que afetam a funcionalidade do banco de dados no Banco de Dados SQL do Azure. O AMD ainda não dá suporte à instância gerenciada como destino de migração, mas é recomendável executar a avaliação no Banco de Dados SQL do Azure e examinar atentamente a lista de problemas de compatibilidade e paridade de recursos relatados em relação à documentação do produto. Confira [Recursos do Banco de Dados SQL do Azure](sql-database-features.md) para verificar se há problemas de bloqueio relatados que não bloqueiam a instância gerenciada, porque a maioria dos problemas de bloqueio que impedem uma migração para o Banco de Dados SQL do Azure foi removida com a instância gerenciada. Por exemplo, recursos como consultas entre banco de dados, transações entre banco de dados na mesma instância, servidor vinculado a outras fontes do SQL, CLR, tabelas temporárias globais, exibições em nível de instância, Service Broker e similares estão disponíveis nas instâncias gerenciadas.

Se houver problemas de bloqueio relatados que não foram removidos com a opção de implantação de instância gerenciada, talvez seja preciso considerar uma opção alternativa, como o [SQL Server em Máquinas Virtuais do Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/). Estes são alguns exemplos:

- Se você precisar de acesso direto ao sistema operacional ou ao sistema de arquivos, por exemplo, para instalar agentes personalizados ou de terceiros na mesma máquina virtual com o SQL Server.
- Se você tiver uma dependência estrita de recursos que ainda não têm suporte, como transações entre instâncias, PolyBase e FileStream/FileTable.
- Se for absolutamente necessário permanecer em uma versão específica do SQL Server (2012, por exemplo).
- Se os requisitos de computação forem muito menores do que o oferecido pela instância gerenciada (um vCore, por exemplo) e a fusão de banco de dados não for uma opção aceitável.

Se você tiver resolvido todos os bloqueadores de migração identificados e continuar a migração para Instância Gerenciada, observe que algumas das alterações podem afetar o desempenho da carga de trabalho:
- O modelo de recuperação completa obrigatório e o agendamento de backup automatizado regular podem afetar o desempenho de suas ações de carga de trabalho ou de manutenção/ETL se você tiver usado periodicamente um modelo simples/bulk-logged ou backups interrompidos sob demanda.
- Diferentes configurações de nível de servidor ou banco de dados, como sinalizadores de rastreamento ou níveis de compatibilidade
- Os novos recursos que você está usando como TDE (criptografia de banco de dados transparente) ou grupos de failover automático podem afetar o uso de CPU e e/s.

Instância Gerenciada garantia de 99,99% de disponibilidade mesmo nos cenários críticos, portanto, a sobrecarga causada por esses recursos não pode ser desabilitada. Para obter mais informações, consulte [as causas raiz que podem causar um desempenho diferente em SQL Server e instância gerenciada](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/).

### <a name="create-performance-baseline"></a>Criar linha de base de desempenho

Se você precisar comparar o desempenho de sua carga de trabalho no Instância Gerenciada com a carga de trabalho original em execução no SQL Server, precisará criar uma linha de base de desempenho que será usada para comparação. 

A linha de base de desempenho é um conjunto de parâmetros como o uso médio/máximo da CPU, latência média/máxima de e/s de disco, taxa de transferência, IOPS, média/máxima expectativa de vida da página, tamanho máximo médio de tempdb. Você gostaria de ter parâmetros semelhantes ou ainda melhores após a migração, portanto, é importante medir e registrar os valores de linha de base para esses parâmetros. Além dos parâmetros do sistema, você precisaria selecionar um conjunto de consultas representativas ou as consultas mais importantes em sua carga de trabalho e medir a duração mínima/média/máxima, uso da CPU para as consultas selecionadas. Esses valores permitem que você compare o desempenho da carga de trabalho em execução em Instância Gerenciada aos valores originais em seu SQL Server de origem.

Alguns dos parâmetros que você precisa medir em sua instância de SQL Server são: 
- [Monitore o uso da CPU em sua instância do SQL Server](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Monitor-CPU-usage-on-SQL-Server/ba-p/680777#M131) e registre o uso médio e máximo da CPU.
- [Monitore o uso de memória em sua instância do SQL Server](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-memory-usage) e determine a quantidade de memória usada por diferentes componentes, como pool de buffers, cache de planos, pool de repositório de coluna, [OLTP na memória](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage?view=sql-server-2017), etc. Além disso, você deve encontrar os valores médio e de pico do contador de desempenho de memória expectativa de vida da página.
- Monitore o uso de e/s do disco na instância de SQL Server de origem usando a exibição [Sys. dm _io_virtual_file_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) ou [contadores de desempenho](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-disk-usage).
- Monitore a carga de trabalho e o desempenho de consulta ou sua instância de SQL Server examinando exibições de gerenciamento dinâmico ou Repositório de Consultas se você estiver migrando da versão SQL Server 2016 +. Identifique a duração média e o uso da CPU das consultas mais importantes em sua carga de trabalho para compará-las com as consultas em execução no Instância Gerenciada.

> [!Note]
> Se você notar algum problema com sua carga de trabalho em SQL Server como alto uso da CPU, pressão de memória constante, problemas de tempdb ou parametrização, você deve tentar resolvê-los em sua instância de SQL Server de origem antes de pegar a linha de base e a migração. A migração de problemas conhecidos para qualquer novo sistema migh causa resultados inesperados e invalida qualquer comparação de desempenho.

Como resultado dessa atividade, você deve ter os valores de média e de pico documentados para uso de CPU, memória e e/s no sistema de origem, bem como a duração média e máxima e o uso da CPU das consultas dominante e mais crítica em sua carga de trabalho. Você deve usar esses valores posteriormente para comparar o desempenho de sua carga de trabalho no Instância Gerenciada com o desempenho de linha de base da carga de trabalho no SQL Server de origem.

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>Implantar em uma instância gerenciada de tamanho ideal

A instância gerenciada é adaptada para cargas de trabalho locais cuja movimentação para a nuvem é planejada. Ela introduz um [novo modelo de compra](sql-database-service-tiers-vcore.md) que oferece maior flexibilidade na seleção do nível certo de recursos para as cargas de trabalho. No ambiente local, você provavelmente está acostumado a dimensionar essas cargas de trabalho usando núcleos físicos e largura de banda de E/S. O modelo de compra para a instância gerenciada baseia-se em núcleos virtuais, ou “vCores”, com armazenamento adicional e E/S disponíveis separadamente. O modelo vCore é uma maneira mais simples de compreender os requisitos de computação na nuvem em relação ao que você utiliza no local atualmente. Esse novo modelo permite que você dimensione adequadamente o ambiente de destino na nuvem. Algumas diretrizes gerais que podem ajudá-lo a escolher a camada de serviço e as características certas são descritas aqui:
- Com base no uso de CPU de linha de base, você pode provisionar um Instância Gerenciada que corresponda ao número de núcleos que você está usando em SQL Server, tendo em mente que as características da CPU talvez precisem ser dimensionadas para corresponder às [características da VM em que instância gerenciada está instalado ](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics).
- Com base no uso de memória de linha de base, escolha [a camada de serviço que tem memória correspondente](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics). A quantidade de memória não pode ser escolhida diretamente, portanto, você precisaria selecionar o Instância Gerenciada com a quantidade de vCores que tem memória correspondente (por exemplo, 5,1 GB/vCore em Gen5). 
- Com base na latência de e/s de linha de base do subsistema de arquivos, escolha entre Uso Geral (latência maior que 5 ms) e Comercialmente Crítico camadas de serviço (latência menor que 3 ms).
- Com base na taxa de transferência de linha de base, aloque previamente o tamanho dos arquivos de dados ou de log para obter o desempenho esperado de e/s

Você pode escolher os recursos de computação e armazenamento no momento da implantação e, em seguida, alterá-los posteriormente sem introduzir o tempo de inatividade para seu aplicativo usando o [portal do Azure](sql-database-scale-resources.md):

![dimensionamento da instância gerenciada](./media/sql-database-managed-instance-migration/managed-instance-sizing.png)

Para saber como criar a infraestrutura de VNet e uma instância gerenciada, confira [Criar uma instância gerenciada](sql-database-managed-instance-get-started.md).

> [!IMPORTANT]
> É importante manter a VNet e a sub-rede de destino sempre de acordo com os [requisitos da VNet da instância gerenciada](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Qualquer incompatibilidade pode impedi-lo de criar novas instâncias ou utilizar aquelas que você já criou. Saiba mais sobre a [criação de novas redes](sql-database-managed-instance-create-vnet-subnet.md) e a [configuração de redes existentes](sql-database-managed-instance-configure-vnet-subnet.md).

## <a name="select-migration-method-and-migrate"></a>Selecionar o método de migração e migrar

A opção de implantação de instância gerenciada destina-se a cenários de usuários que exigem a migração de banco de dados em massa de implementações de bancos de dados locais ou IaaS. Essa é a escolha ideal quando for necessário fazer lift-and-shift do back-end dos aplicativos que utilizam regularmente as funcionalidades entre banco de dados e/ou nível de instância. Se este é o seu cenário, você pode mover uma instância inteira para um ambiente correspondente no Azure sem a necessidade de arquitetar novamente seus aplicativos.

Para mover instâncias do SQL, é necessário planejar atentamente:

- A migração de todos os bancos de dados que precisam ser colocados (os que estão executando na mesma instância)
- A migração de objetos de nível de instância que o aplicativo depende, incluindo logons, credenciais, Operadores e Trabalhos SQL Agent e gatilhos de nível de servidor.

A instância gerenciada é um serviço gerenciado que permite delegar algumas das atividades regulares do DBA para a plataforma conforme são inseridas. Portanto, alguns dados de nível de instância não precisam ser migrados, como trabalhos de manutenção para backups regulares ou configuração Always On, pois a [alta disponibilidade](sql-database-high-availability.md) é interna.

A instância gerenciada dá suporte às seguintes opções de migração de banco de dados (atualmente, estes são os únicos métodos de migração compatíveis):

- Serviço de Migração de Banco de Dados do Azure - migração com tempo de inatividade próximo de zero,
- `RESTORE DATABASE FROM URL` nativo - usa backups nativos do SQL Server e requer algum tempo de inatividade.

### <a name="azure-database-migration-service"></a>Serviço de Migração de Banco de Dados do Azure

O [DMS (Serviço de Migração de Banco de Dados do Azure)](../dms/dms-overview.md) é um serviço totalmente gerenciado projetado para permitir migrações contínuas de várias fontes de banco de dados para plataformas de dados do Azure com um tempo de inatividade mínimo. Esse serviço simplifica as tarefas necessárias para mover bancos de dados de terceiros e SQL Server existentes para o Azure. As opções de implantação na versão prévia pública incluem bancos de dados no Banco de Dados SQL do Azure e bancos de dados do SQL Server em uma Máquina Virtual do Azure. O DMS é o método recomendado de migração para as cargas de trabalho empresariais.

Se você usa o SSIS (SQL Server Integration Services) no SQL Server local, o DMS ainda não dá suporte à migração do catálogo do SSIS (SSISDB) que armazena pacotes SSIS. No entanto, você pode provisionar um Azure-SSIS IR (Integration Runtime) no ADF (Azure Data Factory) que criará um SSISDB em uma instância gerenciada e, em seguida, reimplantar os pacotes nele. Confira [Criar um Azure-SSIS IR no ADF](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

Para saber mais sobre esse cenário e as etapas de configuração do DMS, confira [Migrar o banco de dados local para uma instância gerenciada usando o DMS](../dms/tutorial-sql-server-to-managed-instance.md).  

### <a name="native-restore-from-url"></a>RESTAURAÇÃO nativa da URL

A RESTAURAÇÃO de backups nativos (arquivos .bak) obtidos do SQL Server local ou do [SQL Server em Máquinas Virtuais](https://azure.microsoft.com/services/virtual-machines/sql-server/), disponível no [Armazenamento do Azure](https://azure.microsoft.com/services/storage/), é uma das principais funcionalidades da opção de implantação de instância gerenciada que permite uma rápida e fácil migração de banco de dados offline.

O diagrama a seguir fornece uma visão geral de alto nível do processo:

![fluxo de migração](./media/sql-database-managed-instance-migration/migration-flow.png)

A tabela a seguir fornece mais informações sobre os métodos que podem ser utilizados, dependendo da versão do SQL Server de origem em execução:

|Etapa|Mecanismo SQL e versão|Método de backup/restauração|
|---|---|---|
|Coloque o backup no Armazenamento do Microsoft Azure|SQL 2012 SP1 CU2 anterior|Upload do arquivo .bak diretamente para Armazenamento do Microsoft Azure|
||2012 SP1 CU2 - 2016|Backup direto utilizando a sintaxe [WITH CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql)|
||2016 e posterior|Backup direto utilizando [WITH SAS CREDENTIAL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url)|
|Restauração do Armazenamento do Azure para uma instância gerenciada|[RESTORE FROM URL com SAS CREDENTIAL](sql-database-managed-instance-get-started-restore.md)|

> [!IMPORTANT]
> - Ao migrar um banco de dados protegido pela [Transparent Data Encryption](transparent-data-encryption-azure-sql.md) para uma instância gerenciada usando a opção de restauração nativa, o certificado correspondente do SQL Server local ou IaaS precisará ser migrado antes da restauração do banco de dados. Para obter etapas detalhadas, confira [Migrar o certificado TDE para uma instância gerenciada](sql-database-managed-instance-migrate-tde-certificate.md)
> - Não há suporte para restauração de bancos de dados do sistema. Para migrar objetos de nível de instância (armazenados em bancos de dados mestres ou msdb), é recomendável script e executar scripts T-SQL na instância de destino.

Para obter um Início Rápido que mostra como restaurar um backup de banco de dados em uma instância gerenciada usando uma credencial SAS, confira [Restauração do backup para uma instância gerenciada](sql-database-managed-instance-get-started-restore.md).

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]


## <a name="monitor-applications"></a>Monitorar aplicativos

Depois de concluir a migração para Instância Gerenciada, você deve acompanhar o comportamento do aplicativo e o desempenho da carga de trabalho. Esse processo inclui as seguintes atividades:
- [Compare o desempenho da carga de trabalho em execução no instância gerenciada](#compare-performance-with-the-baseline) com a [linha de base de desempenho que você criou no SQL Server de origem](#create-performance-baseline).
- Monitore continuamente [o desempenho da sua carga de trabalho](#monitor-performance) para identificar possíveis problemas e melhorias.

### <a name="compare-performance-with-the-baseline"></a>Comparar o desempenho com a linha de base

A primeira atividade que você precisa tomar imediatamente após a migração bem-sucedida é comparar o desempenho da carga de trabalho com o desempenho da carga de trabalho de linha de base. O objetivo dessa atividade é confirmar se o desempenho da carga de trabalho na sua Instância Gerenciada atende às suas necessidades. 

A migração de banco de dados para Instância Gerenciada mantém as configurações do banco de dados e seu nível de compatibilidade original na maioria dos casos. As configurações originais são preservadas sempre que possível para reduzir o risco de algumas degradações de desempenho em comparação com o SQL Server de origem. Se o nível de compatibilidade de um banco de dados de usuário fosse 100 ou superior antes da migração, ele permaneceria o mesmo após a migração. Se o nível de compatibilidade de um banco de dados de usuário era 90 antes da migração, no banco de dados atualizado, o nível de compatibilidade é definido como 100, que é o nível de compatibilidade mais baixo compatível com a instância gerenciada. O nível de compatibilidade dos bancos de dados do sistema é 140. Como a migração para Instância Gerenciada está na verdade migrando para a versão mais recente do SQL Server Mecanismo de Banco de Dados, você deve estar ciente de que precisa testar novamente o desempenho da sua carga de trabalho para evitar alguns problemas de desempenho surpreendentes.

Como pré-requisito, verifique se você concluiu as seguintes atividades:
- Alinhe suas configurações em Instância Gerenciada com as configurações da instância de SQL Server de origem investigando várias instâncias, banco de dados, configurações de tempdb e configurações. Certifique-se de que você não alterou as configurações como níveis de compatibilidade ou criptografia antes de executar a primeira comparação de desempenho ou aceite o risco de que alguns dos novos recursos habilitados possam afetar algumas consultas. Para reduzir os riscos de migração, altere o nível de compatibilidade do banco de dados somente após o monitoramento do desempenho.
- Implemente [diretrizes de práticas recomendadas de armazenamento para uso geral](https://techcommunity.microsoft.com/t5/DataCAT/Storage-performance-best-practices-and-considerations-for-Azure/ba-p/305525) como alocar previamente o tamanho dos arquivos para obter o melhor desempenho.
- Saiba mais sobre as [principais diferenças de ambiente que podem causar as diferenças de desempenho entre instância gerenciada e SQL Server]( https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) e identificar os riscos que podem afetar o desempenho.
- Certifique-se de manter habilitado Repositório de Consultas e ajuste automático em seu Instância Gerenciada. Esses recursos permitem que você meça o desempenho da carga de trabalho e corrija automaticamente os possíveis problemas de desempenho. Saiba como usar o Repositório de Consultas como uma ferramenta ideal para obter informações sobre o desempenho da carga de trabalho antes e depois da alteração do nível de compatibilidade do banco de dados, conforme explicado em [manter a estabilidade do desempenho durante a atualização para a versão mais recente do SQL Server](https://docs.microsoft.com/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade).
Depois de preparar o ambiente que é comparável o máximo possível com seu ambiente local, você pode começar a executar sua carga de trabalho e medir o desempenho. O processo de medição deve incluir os mesmos parâmetros que você mediu [ao criar o desempenho de linha de base de suas medidas de carga de trabalho no SQL Server de origem](#create-performance-baseline).
Como resultado, você deve comparar os parâmetros de desempenho com a linha de base e identificar as diferenças críticas.

> [!NOTE]
> Em muitos casos, você não poderá obter um desempenho exatamente correspondente em Instância Gerenciada e SQL Server. Instância Gerenciada é um mecanismo de banco de dados SQL Server, mas a infraestrutura e a configuração de alta disponibilidade no Instância Gerenciada podem apresentar alguma diferença. Você pode esperar que algumas consultas sejam mais rápidas, enquanto outras podem ser mais lentas. O objetivo da comparação é verificar se o desempenho da carga de trabalho em Instância Gerenciada corresponde ao desempenho em SQL Server (em média) e identificar se há alguma consulta crítica com o desempenho que não corresponde ao seu desempenho original.

O resultado da comparação de desempenho pode ser:
- O desempenho da carga de trabalho na Instância Gerenciada é alinhado ou melhor que o desempenho da carga de trabalho no SQL Server. Nesse caso, você confirmou com êxito que a migração foi bem-sucedida.
- A maioria dos parâmetros de desempenho e as consultas na carga de trabalho funcionam bem, com algumas exceções com desempenho degradado. Nesse caso, você precisaria identificar as diferenças e sua importância. Se houver algumas consultas importantes com desempenho degradado, você deverá investigar se os planos SQL subjacentes foram alterados ou se as consultas estão atingindo alguns limites de recursos. Nesse caso, a mitigação pode ser aplicar algumas dicas sobre as consultas críticas (por exemplo, nível de compatibilidade alterado, avaliador de cardinalidade herdado) diretamente ou usando guias de plano, recompilar ou criar estatísticas e índices que possam afetar os planos. 
- A maioria das consultas é mais lenta em Instância Gerenciada em comparação com o SQL Server de origem. Nesse caso, tente identificar as causas raiz da diferença, como [atingir um limite de recursos]( sql-database-managed-instance-resource-limits.md#instance-level-resource-limits) , como limites de e/s, limite de memória, limite de taxa de log de instância, etc. Se não houver nenhum limite de recurso que possa causar a diferença, tente alterar o nível de compatibilidade do banco de dados ou alterar as configurações do banco de dados, como estimativa de cardinalidade herdada e reiniciar o teste. Examine as recomendações fornecidas pelas exibições Instância Gerenciada ou Repositório de Consultas para identificar as consultas que retornaram o desempenho.

> [!IMPORTANT]
> O Instância Gerenciada tem o recurso interno de correção de plano automático que está habilitado por padrão. Esse recurso garante que as consultas que funcionaram bem na colagem não sejam prejudicadas no futuro. Verifique se esse recurso está habilitado e se você executou a carga de trabalho por tempo suficiente com as configurações antigas antes de alterar as novas configurações para permitir que Instância Gerenciada saiba mais sobre o desempenho e os planos de linha de base.

Faça a alteração dos parâmetros ou atualize as camadas de serviço para convergir para a configuração ideal até obter o desempenho da carga de trabalho que atenda às suas necessidades.

### <a name="monitor-performance"></a>Monitorar o desempenho

Instância Gerenciada fornece muitas ferramentas avançadas para monitoramento e solução de problemas, e você deve usá-las para monitorar o desempenho em sua instância do. Alguns dos parâmetros que precisam ser monitorados são:
- O uso da CPU na instância para determinar o número de vCores que você provisionou é a correspondência correta para sua carga de trabalho.
- Expectativa de vida da página em seu Instância Gerenciada para determinar [se você precisa de memória adicional](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Do-you-need-more-memory-on-Azure-SQL-Managed-Instance/ba-p/563444).
- Aguarde estatísticas como `INSTANCE_LOG_GOVERNOR` ou `PAGEIOLATCH` que dirá que você tem problemas de e/s de armazenamento, especialmente na camada de uso geral em que talvez seja necessário alocar previamente os arquivos para obter um desempenho de e/s melhor.

## <a name="leverage-advanced-paas-features"></a>Aproveite os recursos avançados de PaaS

Quando você estiver em uma plataforma totalmente gerenciada e tiver verificado que os desempenhos de carga de trabalho são correspondentes a você SQL Server a carga de trabalho, aproveite as vantagens que são fornecidas automaticamente como parte do serviço do banco de dados SQL. 

Mesmo que você não faça algumas alterações na instância gerenciada durante a migração, há alta probabilidade de que você ative alguns dos novos recursos enquanto está operando sua instância para tirar proveito dos aprimoramentos do mecanismo de banco de dados mais recentes. Algumas alterações só serão habilitadas quando o [nível de compatibilidade do banco de dados tiver sido alterado](https://docs.microsoft.com/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database).


Por exemplo, não é necessário criar backups na instância gerenciada – o serviço faz backups para você automaticamente. Não é mais necessário preocupar-se com agendamento, execução e gerenciamento de backups. A instância gerenciada fornece a capacidade de restauração para qualquer momento no período de retenção usando o [PITR (Recuperação Pontual)](sql-database-recovery-using-backups.md#point-in-time-restore). Além disso, não é necessário preocupar-se com a configuração de alta disponibilidade, pois a [alta disponibilidade](sql-database-high-availability.md) é interna.

Para reforçar a segurança, considere o uso de [Azure Active Directory autenticação](sql-database-security-overview.md), [auditoria](sql-database-managed-instance-auditing.md), [detecção de ameaças](sql-database-advanced-data-security.md), [segurança em nível de linha](https://docs.microsoft.com/sql/relational-databases/security/row-level-security)e máscara de [dados dinâmicos](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking) ).

Além dos recursos avançados de gerenciamento e segurança, o Instância Gerenciada fornece um conjunto de ferramentas avançadas que podem ajudá-lo a [monitorar e ajustar sua carga de trabalho](sql-database-monitor-tune-overview.md). A [análise de SQL do Azure](https://docs.microsoft.com/azure/azure-monitor/insights/azure-sql) permite que você monitore um grande conjunto de instâncias gerenciadas e centralize o monitoramento de um grande número de instâncias e bancos de dados. O [ajuste automático](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction) no instância gerenciada monitorar continuamente o desempenho de suas estatísticas de execução do plano SQL e corrigir automaticamente os problemas de desempenho identificados.

## <a name="next-steps"></a>Próximas etapas

- Para obter informações sobre instâncias gerenciadas, confira [O que é uma instância gerenciada?](sql-database-managed-instance.md).
- Para obter um tutorial que inclui uma restauração do backup, confira [Criar uma instância gerenciada](sql-database-managed-instance-get-started.md).
- Para obter um tutorial que mostra a migração usando o DMS, confira [Migrar o banco de dados local para uma instância gerenciada usando o DMS](../dms/tutorial-sql-server-to-managed-instance.md).  
