---
title: 'SQL Server para o SQL Instância Gerenciada do Azure: guia de migração'
description: Este guia ensina a migrar seus bancos de dados do SQL Server para o SQL Instância Gerenciada do Azure.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: a1dcb72c30268dd82052e29232e79a485d86f72d
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105025298"
---
# <a name="migration-guide-sql-server-to-azure-sql-managed-instance"></a>Guia de migração: SQL Server para SQL do Azure Instância Gerenciada
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

Este guia ajuda você a migrar sua instância do SQL Server para o SQL Instância Gerenciada do Azure. 

Você pode migrar SQL Server em execução no local ou em: 

- SQL Server em Máquinas Virtuais  
- Amazon Web Services (AWS) EC2 
- Serviço de banco de dados relacional do Amazon (AWS RDS) 
- Mecanismo de computação (Google Cloud Platform-GCP)  
- SQL de nuvem para SQL Server (Google Cloud Platform – GCP) 

Para obter mais informações de migração, consulte [visão geral da migração](sql-server-to-managed-instance-overview.md). Para obter outros guias de migração, confira [Migração de banco de dados](https://docs.microsoft.com/data-migration). 

:::image type="content" source="media/sql-server-to-managed-instance-overview/migration-process-flow-small.png" alt-text="Fluxo do processo de migração":::

## <a name="prerequisites"></a>Pré-requisitos 

Para migrar seu SQL Server para o SQL Instância Gerenciada do Azure, certifique-se de seguir os seguintes pré-requisitos: 

- Escolha um [método de migração](sql-server-to-managed-instance-overview.md#compare-migration-options) e as ferramentas correspondentes necessárias para o método escolhido
- Instale o [Assistente de migração de dados (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) em um computador que possa se conectar ao seu SQL Server de origem
- Conectividade e as permissões adequadas para acessar a origem e o destino. 



## <a name="pre-migration"></a>Pré-migração

Depois de verificar se o seu ambiente de origem tem suporte, comece com o estágio pré-migração. Descubra todas as fontes de dados existentes, avalie a viabilidade da migração e identifique quaisquer problemas de bloqueio que possam impedir sua migração.  

### <a name="discover"></a>Descobrir

Na fase de descoberta, verifique a rede para identificar todas as instâncias de SQL Server e os recursos usados pela sua organização. 

Use as [migrações para Azure](../../../migrate/migrate-services-overview.md) para avaliar a adequação de migração de servidores locais, executar o dimensionamento com base no desempenho e fornecer estimativas de custo para executá-los no Azure. 

Como alternativa, use o [Kit de ferramentas de avaliação e planejamento da Microsoft (o "MAP Toolkit")](https://www.microsoft.com/download/details.aspx?id=7826) para avaliar sua infraestrutura de ti atual. O kit de ferramentas fornece uma poderosa ferramenta de inventário, avaliação e relatórios para simplificar o processo de planejamento de migração. 

Para obter mais informações sobre as ferramentas disponíveis para uso na fase de descoberta, consulte [serviços e ferramentas disponíveis para cenários de migração de dados](../../../dms/dms-tools-matrix.md). 

### <a name="assess"></a>Avaliar 

[!INCLUDE [assess-estate-with-azure-migrate](../../../../includes/azure-migrate-to-assess-sql-data-estate.md)]

Depois que as fontes de dados forem descobertas, avalie qualquer instância de SQL Server local que possa ser migrada para o SQL do Azure Instância Gerenciada para identificar os bloqueios de migração ou os problemas de compatibilidade. 

Você pode usar o Assistente de Migração de Dados (versão 4,1 e posterior) para avaliar os bancos de dados a serem obtidos: 

- [Recomendações de destino do Azure](/sql/dma/dma-assess-sql-data-estate-to-sqldb)
- [Recomendações de SKU do Azure](/sql/dma/dma-sku-recommend-sql-db)

Para avaliar seu ambiente usando a avaliação de migração de banco de dados, siga estas etapas: 

1. Abra o [Assistente de migração de dados (DMA)](https://www.microsoft.com/download/details.aspx?id=53595). 
1. Selecione **arquivo** e, em seguida, escolha **nova avaliação**. 
1. Especifique um nome de projeto, selecione SQL Server como o tipo de servidor de origem e, em seguida, selecione Azure SQL Instância Gerenciada como o tipo de servidor de destino. 
1. Selecione os tipos de relatórios de avaliação que você deseja gerar. Por exemplo, compatibilidade de banco de dados e paridade de recurso. Com base no tipo de avaliação, as permissões necessárias no SQL Server de origem podem ser diferentes.  O DMA destacará as permissões necessárias para o supervisor escolhido antes de executar a avaliação.
    - A categoria de **paridade de recursos** fornece um conjunto abrangente de recomendações, alternativas disponíveis no Azure e etapas de mitigação para ajudá-lo a planejar seu projeto de migração. (permissões de sysadmin necessárias)
    - A categoria de **problemas de compatibilidade** identifica problemas de compatibilidade de recursos com suporte parcial ou sem suporte que podem bloquear a migração, bem como recomendações para solucioná-los ( `CONNECT SQL` , `VIEW SERVER STATE` e `VIEW ANY DEFINITION` permissões necessárias).
1. Especifique os detalhes de conexão de origem para seu SQL Server e conecte-se ao banco de dados de origem.
1. Selecione **Iniciar avaliação**. 
1. Quando o processo for concluído, selecione e examine os relatórios de avaliação para bloqueio de migração e problemas de paridade de recursos. O relatório de avaliação também pode ser exportado para um arquivo que pode ser compartilhado com outras equipes ou pessoal da sua organização. 
1. Determine o nível de compatibilidade do banco de dados que minimiza os esforços após a migração.  
1. Identifique o melhor SKU do Azure SQL Instância Gerenciada para sua carga de trabalho local. 

Para saber mais, consulte [executar uma avaliação de migração SQL Server com assistente de migração de dados](/sql/dma/dma-assesssqlonprem).

Se o SQL Instância Gerenciada não for um destino adequado para sua carga de trabalho, SQL Server em VMs do Azure poderá ser um alvo alternativo viável para sua empresa. 

#### <a name="scaled-assessments-and-analysis"></a>Avaliações e análises dimensionadas

O Assistente de Migração de Dados dá suporte à execução de avaliações dimensionadas e à consolidação dos relatórios de avaliação para análise. Se você tiver vários servidores e bancos de dados que precisam ser avaliados e analisados em escala para fornecer uma visão mais ampla do estado de dado, clique nos links a seguir para saber mais.

- [Executando avaliações dimensionadas usando o PowerShell](/sql/dma/dma-consolidatereports)
- [Analisando relatórios de avaliação usando o Power BI](/sql/dma/dma-consolidatereports#dma-reports)

> [!IMPORTANT]
>A execução de avaliações em escala para vários bancos de dados também pode ser automatizada usando o [Utilitário de linha de comando do DMA](/sql/dma/dma-commandline) , que também permite que os resultados sejam carregados para [migrações para Azure](/sql/dma/dma-assess-sql-data-estate-to-sqldb#view-target-readiness-assessment-results) para análise adicional e preparação de destino.

### <a name="create-a-performance-baseline"></a>Criar uma linha de base de desempenho

Se você precisar comparar o desempenho de sua carga de trabalho em um Instância Gerenciada SQL com sua carga de trabalho original em execução no SQL Server, crie uma linha de base de desempenho a ser usada para comparação. Consulte [linha de base de desempenho](sql-server-to-managed-instance-performance-baseline.md) para saber mais. 

### <a name="create-sql-managed-instance"></a>Criar uma Instância Gerenciada do SQL 

Com base nas informações na fase de descoberta e avaliação, crie um Instância Gerenciada de destino do SQL de tamanho adequado. Você pode fazer isso usando o [portal do Azure](../../managed-instance/instance-create-quickstart.md), o [PowerShell](../../managed-instance/scripts/create-configure-managed-instance-powershell.md)ou um [modelo de Azure Resource Manager (ARM)](../../managed-instance/create-template-quickstart.md). 


## <a name="migrate"></a>Migrações

Depois de concluir as tarefas associadas ao estágio de pré-migração, você estará pronto para executar o esquema e a migração de dados. 

Migre seus dados usando o [método de migração](sql-server-to-managed-instance-overview.md#compare-migration-options)escolhido. 

Este guia descreve as duas opções mais populares – DMS (serviço de migração de banco de dados do Azure) e backup e restauração nativos. 

### <a name="database-migration-service"></a>Serviço de Migração de Banco de Dados

Para executar migrações usando o DMS, siga as etapas abaixo:

1. Registre o provedor de recursos **Microsoft. Datamigration** em sua assinatura se você estiver executando isso pela primeira vez.
1. Crie uma instância do serviço de migração de banco de dados do Azure em um local desejado de sua escolha (preferencialmente na mesma região que o Azure SQL de destino Instância Gerenciada) e selecione uma rede virtual existente ou crie uma nova para hospedar sua instância do DMS.
1. Depois de criar sua instância do DMS, crie um novo projeto de migração e especifique o tipo de servidor de origem como **SQL Server** e o tipo de servidor de destino como **instância gerenciada do banco de dados SQL do Azure**. Escolha o tipo de atividade na folha de criação do projeto-migração de dados online ou offline. 
1.  Especifique os detalhes da SQL Server de origem na página detalhes da **origem de migração** e o SQL do Azure de destino instância gerenciada detalhes na página detalhes do **destino de migração** . Selecione **Avançar**.
1. Escolha o banco de dados que você deseja migrar. 
1. Forneça definições de configuração para especificar o **compartilhamento de rede SMB** que contém os arquivos de backup do banco de dados. Use as credenciais de usuário do Windows com DMS que podem acessar o compartilhamento de rede. Forneça os **detalhes da sua conta de armazenamento do Azure**. 
1. Examine o resumo da migração e escolha **executar migração**. Em seguida, você pode monitorar a atividade de migração e verificar o progresso da migração do banco de dados.
1. Depois que o banco de dados for restaurado, escolha **Iniciar transferência**. O processo de migração copia o backup da parte final do log quando você o disponibiliza no compartilhamento de rede SMB e o restaura no destino. 
1. Interrompa todo o tráfego de entrada para o banco de dados de origem e atualize a cadeia de conexão para o novo banco de dados do Azure SQL Instância Gerenciada. 

Para obter um tutorial passo a passo detalhado dessa opção de migração, consulte [migrar SQL Server para um SQL do Azure instância gerenciada online usando DMS](../../../dms/tutorial-sql-server-managed-instance-online.md). 
   


### <a name="backup-and-restore"></a>Backup e restauração 

Um dos principais recursos do Azure SQL Instância Gerenciada para permitir a migração rápida e fácil de banco de dados é a restauração nativa de arquivos de backup de banco de dados ( `.bak` ) armazenados no [armazenamento do Azure](https://azure.microsoft.com/services/storage/). O backup e a restauração são uma operação assíncrona com base no tamanho do banco de dados. 

O diagrama a seguir fornece uma visão geral de alto nível do processo:

![O diagrama mostra SQL Server com uma seta rotulada BACKUP/upload para URL que flui para o armazenamento do Azure e uma segunda seta rotulada restaurar da URL que flui do armazenamento do Azure para um Instância Gerenciada de SQL.](./media/sql-server-to-managed-instance-overview/migration-restore.png)

> [!NOTE]
> O tempo para fazer o backup, carregá-lo no armazenamento do Azure e executar uma operação de restauração nativa para o Azure SQL Instância Gerenciada baseia-se no tamanho do banco de dados. Fatorar um tempo de inatividade suficiente para acomodar a operação para grandes bancos de dados. 


Para migrar usando backup e restauração, siga estas etapas: 

1. Faça backup do banco de dados no armazenamento de BLOBs do Azure. Por exemplo, use [backup para URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url) em [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms). Use a [ferramenta Microsoft Azure](https://go.microsoft.com/fwlink/?LinkID=324399) para dar suporte a bancos de dados anteriores a SQL Server 2012 SP1 Cu2. 
1. Conecte-se ao seu Instância Gerenciada SQL do Azure usando SQL Server Management Studio. 
1. Crie uma credencial usando uma assinatura de acesso compartilhado para acessar sua conta de armazenamento de BLOBs do Azure com seus backups de banco de dados. Por exemplo:

   ```sql
   CREATE CREDENTIAL [https://mitutorials.blob.core.windows.net/databases]
   WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
   , SECRET = 'sv=2017-11-09&ss=bfqt&srt=sco&sp=rwdlacup&se=2028-09-06T02:52:55Z&st=2018-09-04T18:52:55Z&spr=https&sig=WOTiM%2FS4GVF%2FEEs9DGQR9Im0W%2BwndxW2CQ7%2B5fHd7Is%3D'
   ```
1. Restaure o backup do contêiner de blob do armazenamento do Azure. Por exemplo: 

    ```sql
   RESTORE DATABASE [TargetDatabaseName] FROM URL =
     'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

1. Depois que a restauração for concluída, exiba o banco de dados no Pesquisador de **objetos** no SQL Server Management Studio. 

Para saber mais sobre essa opção de migração, consulte [restaurar um banco de dados no Azure SQL instância gerenciada com o SSMS](../../managed-instance/restore-sample-database-quickstart.md).

> [!NOTE]
> Uma operação de restauração do banco de dados é assíncrona e repetível. Você poderá receber um erro no SQL Server Management Studio se a conexão for interrompida ou o tempo limite expirar. O Banco de Dados SQL do Azure continuará tentando restaurar o banco de dados em segundo plano e você poderá acompanhar o progresso da restauração usando as exibições [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) e [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database).



## <a name="data-sync-and-cutover"></a>Sincronização de dados e transferência

Ao usar opções de migração que replicam/sincronizam alterações de dados de origem para o destino continuamente, os dados de origem e o esquema podem ser alterados e descompassos do destino. Durante a sincronização de dados, certifique-se de que todas as alterações na origem sejam capturadas e aplicadas ao destino durante o processo de migração. 

Depois de verificar se os dados são os mesmos na origem e no destino, você pode fazer a transferência da origem para o ambiente de destino. É importante planejar o processo de transferência com equipes de negócios/aplicativos para garantir que a interrupção mínima durante a transferência não afete a continuidade dos negócios. 

> [!IMPORTANT]
> Para obter detalhes sobre as etapas específicas associadas à execução de uma transferência como parte das migrações usando DMS, consulte [executando a transferência de migração](../../../dms/tutorial-sql-server-managed-instance-online.md#performing-migration-cutover).


## <a name="post-migration"></a>Pós-migração

Depois de concluir com êxito o estágio de migração, passe por uma série de tarefas de pós-atualização para garantir que tudo esteja funcionando de forma tranqüila e eficiente. 

A fase de pós-migração é crucial para reconciliar problemas de precisão de dados e verificar a integridade dos dados, além de resolver problemas de desempenho com a carga de trabalho. 

### <a name="remediate-applications"></a>Corrigir aplicativos 

Depois que os dados são migrados para o ambiente de destino, todos os aplicativos que antes consumiam a origem, precisam começar a consumir o destino. Fazer isso irá, em alguns casos, exigir alterações nos aplicativos.

### <a name="perform-tests"></a>Executar testes

A abordagem de teste para a migração de banco de dados consiste nas seguintes atividades:

1. **Desenvolver testes de validação**: Para testar a migração do banco de dados, você precisa usar consultas SQL. Você deve criar as consultas de validação para executar nos bancos de dados de origem e de destino. Suas consultas de validação devem abranger o escopo que você definiu.
1. **Configurar ambiente de teste**: O ambiente de teste deve conter uma cópia do banco de dados de origem e do banco de dados de destino. Lembre-se de isolar o ambiente de teste.
1. **Executar testes de validação**: Execute os testes de validação na origem e no destino e, em seguida, analise os resultados.
1. **Executar testes de desempenho**: Execute o teste de desempenho na origem e no destino e, em seguida, analise e compare os resultados.


## <a name="leverage-advanced-features"></a>Aproveite os recursos avançados 

Não se esqueça de aproveitar os recursos avançados baseados em nuvem oferecidos pelo SQL Instância Gerenciada, como [alta disponibilidade interna](../../database/high-availability-sla.md), [detecção de ameaças](../../database/azure-defender-for-sql.md)e [monitoramento e ajuste da carga de trabalho](../../database/monitor-tune-overview.md). 

[Análise de SQL do Azure](../../../azure-monitor/insights/azure-sql.md) permite monitorar um grande conjunto de instâncias gerenciadas de maneira centralizada.

Alguns recursos SQL Server só estarão disponíveis quando o [nível de compatibilidade do banco de dados](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) for alterado para o nível de compatibilidade mais recente (150). 


## <a name="next-steps"></a>Próximas etapas

- Para obter uma matriz de serviços e ferramentas da Microsoft e de terceiros que estão disponíveis para ajudá-lo com vários cenários de migração de banco de dados e de aplicativos, bem como tarefas de especialidade, consulte [serviços e ferramentas para a migração de dados](../../../dms/dms-tools-matrix.md).

- Para saber mais sobre o Azure SQL Instância Gerenciada consulte:
   - [Camadas de serviço no Azure SQL Instância Gerenciada](../../managed-instance/sql-managed-instance-paas-overview.md#service-tiers)
   - [Diferenças entre SQL Server e SQL Instância Gerenciada do Azure](../../managed-instance/transact-sql-tsql-differences-sql-server.md)
   - [Calculadora de custo total de Propriedade do Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Para saber mais sobre a estrutura e o ciclo de adoção para migrações na nuvem, consulte
   -  [Cloud Adoption Framework para Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Práticas recomendadas para o custo e o dimensionamento de cargas de trabalho migrar para o Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Para avaliar a camada de acesso do aplicativo, consulte [Kit de ferramentas de migração de acesso a dados (versão prévia)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Para obter detalhes sobre como executar testes de camada A/B de acesso A dados, consulte [Assistente para experimentos de banco de dados](/sql/dea/database-experimentation-assistant-overview).