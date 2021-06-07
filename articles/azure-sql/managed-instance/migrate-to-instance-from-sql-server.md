---
title: Migrar do SQL Server para o SQL Instância Gerenciada do Azure
description: Saiba como migrar um banco de dados do SQL Server para o SQL Instância Gerenciada do Azure.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: migration
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: ''
ms.date: 07/11/2019
ms.openlocfilehash: ccc6acfd27a1430a4f6a31886c06322c5c09e224
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105628366"
---
# <a name="sql-server-instance-migration-to-azure-sql-managed-instance"></a>Migração de instância de SQL Server para o SQL do Azure Instância Gerenciada
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Neste artigo, você aprende sobre os métodos para migrar uma instância de versão SQL Server 2005 ou posterior para o [SQL instância gerenciada do Azure](sql-managed-instance-paas-overview.md). Para obter informações sobre como migrar para um banco de dados individual ou pool elástico, consulte [visão geral da migração: SQL Server para o banco de dados SQL](../migration-guides/database/sql-server-to-sql-database-overview.md). Para obter informações de migração sobre como migrar de outras plataformas e diretrizes sobre ferramentas e opções, consulte [migrar para o SQL do Azure](../migration-guides/index.yml).

> [!NOTE]
> Se você quiser iniciar rapidamente e experimentar o Azure SQL Instância Gerenciada, talvez queira ir para o guia de [início rápido](quickstart-content-reference-guide.md) em vez desta página.

Em um nível alto, o processo de migração de banco de dados se parece com:

![Processo de migração](./media/migrate-to-instance-from-sql-server/migration-process.png)

- [Avalie a compatibilidade do SQL instância gerenciada](#assess-sql-managed-instance-compatibility) em que você deve garantir que não haja problemas de bloqueio que possam impedir suas migrações.
  
  Essa etapa também inclui a criação de uma [linha de base de desempenho](#create-a-performance-baseline) para determinar o uso de recursos em sua instância de SQL Server de origem. Essa etapa será necessária se você quiser implantar uma instância gerenciada corretamente dimensionada e verificar se o desempenho após a migração não é afetado.
- [Escolha opções de conectividade de aplicativo](connect-application-instance.md).
- [Implante em uma instância gerenciada de tamanho ideal, na](#deploy-to-an-optimally-sized-managed-instance) qual você escolherá as características técnicas (número de vCores, quantidade de memória) e camada de desempenho (comercialmente crítico uso geral) da instância gerenciada.
- [Selecione o método de migração e migre](#select-a-migration-method-and-migrate) o local em que você migra seus bancos de dados usando a migração offline (backup/restauração nativa, importação/exportação de banco de dados) ou migração online (serviço de migração do Azure Data Migration, replicação transacional).
- [Monitore os aplicativos](#monitor-applications) para garantir que você tem desempenho esperado.

> [!NOTE]
> Para migrar um banco de dados individual para um único banco de dados ou um pool elástico, consulte [migrar um SQL Server banco de dados para o banco de dados SQL do Azure](../database/migrate-to-database-from-sql-server.md).

## <a name="assess-sql-managed-instance-compatibility"></a>Avaliar a compatibilidade do SQL Instância Gerenciada

Primeiro, determine se o SQL Instância Gerenciada é compatível com os requisitos de banco de dados do seu aplicativo. O SQL Instância Gerenciada foi projetado para fornecer migração de deslocamento rápido e fácil para a maioria dos aplicativos existentes que usam SQL Server. No entanto, às vezes você pode exigir recursos ou funcionalidades que ainda não têm suporte e o custo da implementação de uma solução alternativa é muito alto.

Use [Assistente de migração de dados](/sql/dma/dma-overview) para detectar possíveis problemas de compatibilidade impactando a funcionalidade do banco de dados no banco de dados SQL do Azure. Se houver alguns problemas de bloqueio relatados, talvez seja necessário considerar uma opção alternativa, como [SQL Server na VM do Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/). Estes são alguns exemplos:

- Se você precisar de acesso direto ao sistema operacional ou sistema de arquivos, por exemplo, para instalar agentes de terceiros ou personalizados na mesma máquina virtual com SQL Server.
- Se você tiver uma dependência estrita dos recursos que ainda não têm suporte, como FileStream/Filetable, polybase e transações de instância cruzada.
- Se você realmente precisa permanecer em uma versão específica do SQL Server (2012, por exemplo).
- Se os requisitos de computação forem muito menores do que as ofertas de instância gerenciada (um vCore, por exemplo) e a consolidação de banco de dados não for uma opção aceitável.

Se você tiver resolvido todos os bloqueadores de migração identificados e estiver continuando a migração para o SQL Instância Gerenciada, observe que algumas das alterações podem afetar o desempenho da carga de trabalho:

- O modelo de recuperação completa obrigatório e o agendamento de backup automatizado regular podem afetar o desempenho de suas ações de carga de trabalho ou de manutenção/ETL se você tiver usado periodicamente um modelo simples/bulk-logged ou backups interrompidos sob demanda.
- Configurações de nível de servidor ou de banco de dados diferentes, como sinalizadores de rastreamento ou níveis de compatibilidade.
- Os novos recursos que você está usando como TDE (criptografia de banco de dados transparente) ou grupos de failover automático podem afetar o uso de CPU e e/s.

O SQL Instância Gerenciada garante 99,99% de disponibilidade mesmo em cenários críticos, portanto, a sobrecarga causada por esses recursos não pode ser desabilitada. Para obter mais informações, consulte [as causas raiz que podem causar um desempenho diferente no SQL Server e no instância gerenciada do SQL do Azure](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/).

#### <a name="in-memory-oltp-memory-optimized-tables"></a>In-Memory OLTP (tabelas com otimização de memória)

O SQL Server fornece In-Memory recurso OLTP que permite o uso de tabelas com otimização de memória, tipos de tabela com otimização de memória e módulos do SQL compilados nativamente para executar cargas de trabalho que têm requisitos de processamento transacional de alta taxa de transferência e baixa latência. 

> [!IMPORTANT]
> In-Memory OLTP só tem suporte na camada de Comercialmente Crítico no Azure SQL Instância Gerenciada (e sem suporte na camada Uso Geral).

Se você tiver tabelas com otimização de memória ou tipos de tabela com otimização de memória em sua SQL Server local e estiver procurando migrar para o SQL do Azure Instância Gerenciada, você deve:

- Escolha Comercialmente Crítico camada para o Instância Gerenciada de destino do Azure SQL que dá suporte ao OLTP In-Memory ou
- Se você quiser migrar para Uso Geral camada no Instância Gerenciada SQL do Azure, remova tabelas com otimização de memória, tipos de tabela com otimização de memória e módulos SQL compilados nativamente que interagem com objetos com otimização de memória antes de migrar seus bancos de dados. A seguinte consulta T-SQL pode ser usada para identificar todos os objetos que precisam ser removidos antes da migração para Uso Geral camada:

```tsql
SELECT * FROM sys.tables WHERE is_memory_optimized=1
SELECT * FROM sys.table_types WHERE is_memory_optimized=1
SELECT * FROM sys.sql_modules WHERE uses_native_compilation=1
```

Para saber mais sobre as tecnologias na memória, consulte [otimizar o desempenho usando tecnologias na memória no banco de dados SQL do Azure e Azure sql instância gerenciada](../in-memory-oltp-overview.md)

### <a name="create-a-performance-baseline"></a>Criar uma linha de base de desempenho

Se você precisar comparar o desempenho de sua carga de trabalho em uma instância gerenciada com sua carga de trabalho original em execução no SQL Server, será necessário criar uma linha de base de desempenho que será usada para comparação.

A linha de base de desempenho é um conjunto de parâmetros como o uso médio/máximo da CPU, latência média/máxima de e/s do disco, taxa de transferência, IOPS, expectativa médio/máximo de vida da página e tamanho máximo médio de tempdb. Você gostaria de ter parâmetros semelhantes ou ainda melhores após a migração, portanto, é importante medir e registrar os valores de linha de base para esses parâmetros. Além dos parâmetros do sistema, você precisaria selecionar um conjunto de consultas representativas ou as consultas mais importantes em sua carga de trabalho e medir a duração mínima/média/máxima e o uso da CPU para as consultas selecionadas. Esses valores permitem que você compare o desempenho da carga de trabalho em execução na instância gerenciada com os valores originais em sua instância de SQL Server de origem.

Alguns dos parâmetros que você precisa medir em sua instância de SQL Server são:

- [Monitore o uso da CPU em sua instância do SQL Server](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Monitor-CPU-usage-on-SQL-Server/ba-p/680777#M131) e registre o uso médio e máximo da CPU.
- [Monitore o uso de memória em sua instância do SQL Server](/sql/relational-databases/performance-monitor/monitor-memory-usage) e determine a quantidade de memória usada por diferentes componentes, como pool de buffers, cache de planos, pool de repositório de coluna, [OLTP na memória](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage), etc. Além disso, você deve encontrar os valores médio e de pico do contador de desempenho de memória expectativa de vida da página.
- Monitore o uso de e/s de disco na instância de SQL Server de origem usando [Sys.dm_io_virtual_file_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) exibição ou [contadores de desempenho](/sql/relational-databases/performance-monitor/monitor-disk-usage).
- Monitore a carga de trabalho e o desempenho de consulta ou sua instância de SQL Server examinando exibições de gerenciamento dinâmico ou Repositório de Consultas se você estiver migrando de uma versão do SQL Server 2016 +. Identifique a duração média e o uso da CPU das consultas mais importantes em sua carga de trabalho para compará-las com as consultas em execução na instância gerenciada.

> [!Note]
> Se você notar algum problema com sua carga de trabalho em SQL Server como alto uso de CPU, pressão de memória constante ou problemas de tempdb ou de parametrização, tente resolvê-los em sua instância de SQL Server de origem antes de pegar a linha de base e a migração. A migração de problemas conhecidos para qualquer novo sistema pode causar resultados inesperados e invalidar qualquer comparação de desempenho.

Como resultado dessa atividade, você deve ter os valores de média e de pico documentados para uso de CPU, memória e e/s no sistema de origem, bem como a duração média e máxima e o uso da CPU das consultas dominante e mais crítica em sua carga de trabalho. Você deve usar esses valores posteriormente para comparar o desempenho de sua carga de trabalho em uma instância gerenciada com o desempenho de linha de base da carga de trabalho na instância de SQL Server de origem.

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>Implantar em uma instância gerenciada de tamanho ideal

O SQL Instância Gerenciada é adaptado para cargas de trabalho locais que estão planejando migrar para a nuvem. Ele apresenta um [novo modelo de compra](../database/service-tiers-vcore.md) que fornece maior flexibilidade na seleção do nível certo de recursos para suas cargas de trabalho. No ambiente local, você provavelmente está acostumado a dimensionar essas cargas de trabalho usando núcleos físicos e largura de banda de E/S. O modelo de compra para a instância gerenciada é baseado em núcleos virtuais, ou "vCores", com armazenamento adicional e e/s disponíveis separadamente. O modelo vCore é uma maneira mais simples de compreender os requisitos de computação na nuvem em relação ao que você utiliza no local atualmente. Esse novo modelo permite que você dimensione adequadamente o ambiente de destino na nuvem. Algumas diretrizes gerais que podem ajudá-lo a escolher a camada de serviço e as características certas são descritas aqui:

- Com base no uso da CPU de linha de base, você pode provisionar uma instância gerenciada que corresponda ao número de núcleos que você está usando em SQL Server, tendo em mente que as características da CPU talvez precisem ser dimensionadas para corresponder às [características da VM em que a instância gerenciada está instalada](resource-limits.md#hardware-generation-characteristics).
- Com base no uso de memória de linha de base, escolha [a camada de serviço que tem memória correspondente](resource-limits.md#hardware-generation-characteristics). A quantidade de memória não pode ser escolhida diretamente, portanto, você precisaria selecionar a instância gerenciada com a quantidade de vCores que tem memória correspondente (por exemplo, 5,1 GB/vCore em Gen5).
- Com base na latência de e/s de linha de base do subsistema de arquivos, escolha entre as camadas de serviço Uso Geral (latência maior que 5 ms) e Comercialmente Crítico (latência menor que 3 ms).
- Com base na taxa de transferência de linha de base, aloque previamente o tamanho dos arquivos de dados ou de log para obter o desempenho esperado de e/s.

Você pode escolher os recursos de computação e armazenamento no momento da implantação e, em seguida, alterá-los posteriormente sem introduzir o tempo de inatividade para seu aplicativo usando o [portal do Azure](../database/scale-resources.md):

![Dimensionamento da instância gerenciada](./media/migrate-to-instance-from-sql-server/managed-instance-sizing.png)

Para saber como criar a infraestrutura de VNet e uma instância gerenciada, confira [Criar uma instância gerenciada](instance-create-quickstart.md).

> [!IMPORTANT]
> É importante manter sua VNet de destino e sub-rede de acordo com [os requisitos de vnet de instância gerenciada](connectivity-architecture-overview.md#network-requirements). Qualquer incompatibilidade pode impedi-lo de criar novas instâncias ou utilizar aquelas que você já criou. Saiba mais sobre a [criação de novas redes](virtual-network-subnet-create-arm-template.md) e a [configuração de redes existentes](vnet-existing-add-subnet.md).

## <a name="select-a-migration-method-and-migrate"></a>Selecionar um método de migração e migrar

O SQL Instância Gerenciada direciona cenários de usuário que exigem migração de banco de dados em massa de implementações locais ou de banco de dados de VM do Azure. Eles são a opção ideal quando você precisa migrar e deslocar o back-end dos aplicativos que usam regularmente funcionalidades de nível de instância e/ou de banco de dados. Se este é o seu cenário, você pode mover uma instância inteira para um ambiente correspondente no Azure sem a necessidade de arquitetar novamente seus aplicativos.

Para mover instâncias do SQL, é necessário planejar atentamente:

- A migração de todos os bancos de dados que precisam ser colocados (aqueles em execução na mesma instância).
- A migração de objetos de nível de instância dos quais seu aplicativo depende, incluindo logons, credenciais, trabalhos e operadores do SQL Agent e gatilhos de nível de servidor.

O SQL Instância Gerenciada é um serviço gerenciado que permite delegar algumas atividades regulares do DBA para a plataforma à medida que elas são criadas. Portanto, alguns dados em nível de instância não precisam ser migrados, como trabalhos de manutenção para backups regulares ou configuração de Always On, já que a [alta disponibilidade](../database/high-availability-sla.md) é interna.

O SQL Instância Gerenciada dá suporte às seguintes opções de migração de banco de dados (atualmente, estes são os únicos métodos de migração com suporte):

- Serviço de migração de banco de dados do Azure-migração com tempo de inatividade quase zero.
- `RESTORE DATABASE FROM URL` nativo - usa backups nativos do SQL Server e requer algum tempo de inatividade.

### <a name="azure-database-migration-service"></a>Serviço de Migração de Banco de Dados do Azure

O [serviço de migração de banco de dados do Azure](../../dms/dms-overview.md) é um serviço totalmente gerenciado projetado para permitir migrações diretas de várias fontes de banco de dados para plataformas de data do Azure com tempo de inatividade Esse serviço simplifica as tarefas necessárias para mover bancos de dados existentes de terceiros e do SQL Server para o Azure. As opções de implantação no modo de visualização pública incluem bancos de dados no banco de dados SQL do Azure e SQL Server bancos de dados em uma máquina virtual do Azure. O serviço de migração de banco de dados é o método recomendado de migração para suas cargas de trabalho corporativas.

Se você usar SQL Server Integration Services (SSIS) no SQL Server local, o serviço de migração de banco de dados ainda não oferecerá suporte à migração do catálogo do SSIS (SSISDB) que armazena os pacotes do SSIS, mas você pode provisionar Azure-SSIS Integration Runtime (IR) no Azure Data Factory, que criará um novo SSISDB em uma instância gerenciada para que você possa reimplantar os pacotes nele. Consulte [criar Azure-SSIS ir em Azure data Factory](../../data-factory/create-azure-ssis-integration-runtime.md).

Para saber mais sobre esse cenário e as etapas de configuração do serviço de migração de banco de dados, consulte [migrar seu banco de dados local para a instância gerenciada usando o serviço de migração de banco de dados](../../dms/tutorial-sql-server-to-managed-instance.md).  

### <a name="native-restore-from-url"></a>RESTAURAÇÃO nativa da URL

A restauração de backups nativos (arquivos. bak) obtidas de uma instância SQL Server, disponível no [armazenamento do Azure](https://azure.microsoft.com/services/storage/), é um dos principais recursos do SQL instância gerenciada que permite uma migração de banco de dados offline rápida e fácil.

O diagrama a seguir fornece uma visão geral de alto nível do processo:

![O diagrama mostra SQL Server com uma seta rotulada BACKUP/upload para URL que flui para o armazenamento do Azure e uma segunda seta rotulada restaurar da URL que flui do armazenamento do Azure para um Instância Gerenciada de SQL.](./media/migrate-to-instance-from-sql-server/migration-flow.png)

A tabela a seguir fornece mais informações sobre os métodos que podem ser utilizados, dependendo da versão do SQL Server de origem em execução:

|Etapa|Mecanismo SQL e versão|Método de backup/restauração|
|---|---|---|
|Coloque o backup no Armazenamento do Microsoft Azure|Antes do 2012 SP1 CU2|Carregar o arquivo. bak diretamente no armazenamento do Azure|
||2012 SP1 CU2 - 2016|Backup direto utilizando a sintaxe [WITH CREDENTIAL](/sql/t-sql/statements/restore-statements-transact-sql)|
||2016 e posterior|Backup direto utilizando [WITH SAS CREDENTIAL](/sql/relational-databases/backup-restore/sql-server-backup-to-url)|
|Restaurar do armazenamento do Azure para uma instância gerenciada|[RESTORE FROM URL com SAS CREDENTIAL](restore-sample-database-quickstart.md)|

> [!IMPORTANT]
>
> - Quando você estiver migrando um banco de dados protegido por [Transparent Data Encryption](../database/transparent-data-encryption-tde-overview.md) para uma instância gerenciada usando a opção de restauração nativa, o certificado correspondente da VM local ou do Azure SQL Server precisa ser migrado antes da restauração do banco de dados. Para obter as etapas detalhadas, confira [Migrar um certificado TDE para uma instância gerenciada](tde-certificate-migrate.md).
> - Não há suporte para restauração de bancos de dados do sistema. Para migrar objetos de nível de instância (armazenados em bancos de dados mestre ou msdb), é recomendável criar scripts para eles e executar scripts T-SQL na instância de destino.

Para obter um Início Rápido que mostra como restaurar um backup de banco de dados em uma instância gerenciada usando uma credencial SAS, confira [Restauração do backup para uma instância gerenciada](restore-sample-database-quickstart.md).

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]

## <a name="monitor-applications"></a>Monitorar aplicativos

Depois de concluir a migração para uma instância gerenciada, você deve acompanhar o comportamento e o desempenho do aplicativo de sua carga de trabalho. Esse processo inclui as seguintes atividades:

- [Compare o desempenho da carga de trabalho em execução na instância gerenciada](#compare-performance-with-the-baseline) com a [linha de base de desempenho que você criou na instância de SQL Server de origem](#create-a-performance-baseline).
- [Monitore continuamente o desempenho da sua carga de trabalho](#monitor-performance) para identificar possíveis problemas e melhorias.

### <a name="compare-performance-with-the-baseline"></a>Comparar o desempenho com a linha de base

A primeira atividade que você precisa tomar imediatamente após a migração bem-sucedida é comparar o desempenho da carga de trabalho com o desempenho da carga de trabalho de linha de base. O objetivo dessa atividade é confirmar se o desempenho da carga de trabalho em sua instância gerenciada atende às suas necessidades.

A migração de banco de dados para uma instância gerenciada mantém as configurações do banco de dados e seu nível de compatibilidade original na maioria dos casos. As configurações originais são preservadas sempre que possível para reduzir o risco de algumas degradações de desempenho em comparação com a instância de SQL Server de origem. Se o nível de compatibilidade de um banco de dados de usuário fosse 100 ou superior antes da migração, ele permaneceria o mesmo após a migração. Se o nível de compatibilidade de um banco de dados de usuário era 90 antes da migração, no banco de dados atualizado, o nível de compatibilidade é definido como 100, que é o nível de compatibilidade mais baixo com suporte em uma instância gerenciada. O nível de compatibilidade dos bancos de dados do sistema é 140. Como a migração para uma instância gerenciada está na verdade migrando para a versão mais recente do mecanismo de banco de dados SQL Server, você deve estar ciente de que precisa testar novamente o desempenho da sua carga de trabalho para evitar alguns problemas de desempenho surpreendentes.

Como pré-requisito, verifique se você concluiu as seguintes atividades:

- Alinhe suas configurações na instância gerenciada com as configurações da instância de SQL Server de origem, investigando várias instâncias de instância, banco de dados, tempdb e configurações. Certifique-se de que você não alterou as configurações como níveis de compatibilidade ou criptografia antes de executar a primeira comparação de desempenho ou aceite o risco de que alguns dos novos recursos habilitados possam afetar algumas consultas. Para reduzir os riscos de migração, altere o nível de compatibilidade do banco de dados somente após o monitoramento do desempenho.
- Implemente [diretrizes de práticas recomendadas de armazenamento para uso geral](https://techcommunity.microsoft.com), como alocar previamente o tamanho dos arquivos para obter um melhor desempenho.
- Saiba mais sobre as [principais diferenças de ambiente que podem causar as diferenças de desempenho entre uma instância gerenciada e SQL Server](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/)e identificar os riscos que podem afetar o desempenho.
- Certifique-se de manter habilitado Repositório de Consultas e ajuste automático em sua instância gerenciada. Esses recursos permitem que você meça o desempenho da carga de trabalho e corrija automaticamente os possíveis problemas de desempenho. Saiba como usar o Repositório de Consultas como uma ferramenta ideal para obter informações sobre o desempenho da carga de trabalho antes e depois da alteração do nível de compatibilidade do banco de dados, conforme explicado em [manter a estabilidade do desempenho durante a atualização para uma versão mais recente do SQL Server](/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade).
Depois de preparar o ambiente que é comparável o máximo possível com seu ambiente local, você pode começar a executar sua carga de trabalho e medir o desempenho. O processo de medição deve incluir os mesmos parâmetros que você mediu [enquanto criou o desempenho de linha de base de suas medidas de carga de trabalho na instância de SQL Server de origem](#create-a-performance-baseline).
Como resultado, você deve comparar os parâmetros de desempenho com a linha de base e identificar as diferenças críticas.

> [!NOTE]
> Em muitos casos, você não poderá obter exatamente o desempenho correspondente na instância gerenciada e SQL Server. O Azure SQL Instância Gerenciada é um mecanismo de banco de dados SQL Server, mas a infraestrutura e a configuração de alta disponibilidade em uma instância gerenciada podem apresentar algumas diferenças. Você pode esperar que algumas consultas sejam mais rápidas enquanto outras podem ser mais lentas. O objetivo da comparação é verificar se o desempenho da carga de trabalho na instância gerenciada corresponde ao desempenho em SQL Server (em média) e identifica quaisquer consultas críticas com o desempenho que não correspondam ao seu desempenho original.

O resultado da comparação de desempenho pode ser:

- O desempenho da carga de trabalho na instância gerenciada é alinhado ou melhor do que o desempenho da carga de trabalho no SQL Server. Nesse caso, você confirmou com êxito que a migração foi bem-sucedida.
- A maioria dos parâmetros de desempenho e das consultas na carga de trabalho funciona bem, com algumas exceções com desempenho degradado. Nesse caso, você precisaria identificar as diferenças e sua importância. Se houver algumas consultas importantes com desempenho degradado, você deverá investigar se os planos SQL subjacentes foram alterados ou se as consultas estão atingindo alguns limites de recursos. A mitigação, nesse caso, pode ser aplicar algumas dicas sobre as consultas críticas (por exemplo, nível de compatibilidade alterado, avaliador de cardinalidade herdado) diretamente ou usando guias de plano, recriar ou criar estatísticas e índices que possam afetar os planos.
- A maioria das consultas é mais lenta em uma instância gerenciada em comparação com a instância de SQL Server de origem. Nesse caso, tente identificar as causas raiz da diferença, como [atingir um limite de recursos](resource-limits.md#service-tier-characteristics) , como limites de e/s, limite de memória, limite de taxa de log de instância, etc. Se não houver nenhum limite de recurso que possa causar a diferença, tente alterar o nível de compatibilidade do banco de dados ou alterar as configurações do banco de dados, como estimativa de cardinalidade herdada e reiniciar o teste. Examine as recomendações fornecidas pela instância gerenciada ou Repositório de Consultas exibições para identificar as consultas que retornaram o desempenho.

> [!IMPORTANT]
> O Azure SQL Instância Gerenciada tem um recurso interno de correção de plano automático que está habilitado por padrão. Esse recurso garante que as consultas que funcionaram bem na colagem não sejam prejudicadas no futuro. Verifique se esse recurso está habilitado e se você executou a carga de trabalho por tempo suficiente com as configurações antigas antes de alterar as novas configurações para permitir que a instância gerenciada saiba mais sobre o desempenho e os planos de linha de base.

Faça a alteração dos parâmetros ou atualize as camadas de serviço para convergir para a configuração ideal até obter o desempenho da carga de trabalho que atenda às suas necessidades.

### <a name="monitor-performance"></a>Monitorar desempenho

O SQL Instância Gerenciada fornece muitas ferramentas avançadas para monitoramento e solução de problemas, e você deve usá-las para monitorar o desempenho em sua instância. Alguns dos parâmetros que você precisa monitorar são:

- Uso da CPU na instância para determinar se o número de vCores que você provisionou é a correspondência correta para sua carga de trabalho.
- Expectativa de vida da página em sua instância gerenciada para determinar [se você precisa de memória adicional](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Do-you-need-more-memory-on-Azure-SQL-Managed-Instance/ba-p/563444).
- Estatísticas como `INSTANCE_LOG_GOVERNOR` ou `PAGEIOLATCH` que determinarão se você tem problemas de e/s de armazenamento, especialmente na camada de uso geral, em que talvez seja necessário alocar previamente os arquivos para obter um desempenho de e/s melhor.

## <a name="leverage-advanced-paas-features"></a>Aproveite os recursos avançados de PaaS

Quando você estiver em uma plataforma totalmente gerenciada e tiver verificado que os desempenhos de carga de trabalho correspondem à sua carga de trabalho de SQL Server, use as vantagens que são fornecidas automaticamente como parte do serviço.

Mesmo que você não faça algumas alterações na instância gerenciada durante a migração, há alta probabilidade de que você ative alguns dos novos recursos enquanto está operando sua instância para aproveitar os aprimoramentos do mecanismo de banco de dados mais recentes. Algumas alterações só serão habilitadas quando o [nível de compatibilidade do banco de dados tiver sido alterado](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database).

Por exemplo, você não precisa criar backups na instância gerenciada – o serviço executa backups para você automaticamente. Não é mais necessário preocupar-se com agendamento, execução e gerenciamento de backups. O SQL Instância Gerenciada fornece a capacidade de restaurar para qualquer ponto no tempo dentro desse período de retenção usando a [PITR (recuperação pontual)](../database/recovery-using-backups.md#point-in-time-restore). Além disso, você não precisa se preocupar com a configuração de alta disponibilidade, pois a [alta disponibilidade](../database/high-availability-sla.md) é interna.

Para reforçar a segurança, considere o uso de [Azure Active Directory autenticação](../database/security-overview.md), [auditoria](auditing-configure.md), [detecção de ameaças](../database/azure-defender-for-sql.md), [segurança em nível de linha](/sql/relational-databases/security/row-level-security)e máscara de [dados dinâmicos](/sql/relational-databases/security/dynamic-data-masking).

Além dos recursos avançados de gerenciamento e segurança, uma instância gerenciada fornece um conjunto de ferramentas avançadas que podem ajudá-lo a [monitorar e ajustar sua carga de trabalho](../database/monitor-tune-overview.md). [Análise de SQL do Azure](../../azure-monitor/insights/azure-sql.md) permite monitorar um grande conjunto de instâncias gerenciadas e centralizar o monitoramento de um grande número de instâncias e bancos de dados. O [ajuste automático](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction) em instâncias gerenciadas monitora continuamente o desempenho de suas estatísticas de execução do plano SQL e corrige automaticamente os problemas de desempenho identificados.

## <a name="next-steps"></a>Próximas etapas

- Para obter informações sobre o Azure SQL Instância Gerenciada, consulte [o que é o instância gerenciada do Azure SQL?](sql-managed-instance-paas-overview.md).
- Para obter um tutorial que inclui uma restauração do backup, consulte [criar uma instância gerenciada](instance-create-quickstart.md).
- Para tutorial mostrando migração usando o serviço de migração de banco de dados, consulte [migrar seu banco de dados local para o Azure SQL instância gerenciada usando o serviço de migração de banco de dados](../../dms/tutorial-sql-server-to-managed-instance.md).