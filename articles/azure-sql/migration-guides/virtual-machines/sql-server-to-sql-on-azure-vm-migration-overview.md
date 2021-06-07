---
title: SQL Server para SQL Server na VM do Azure (visão geral da migração)
description: Saiba mais sobre as diferentes estratégias de migração quando você deseja migrar seus SQL Server para SQL Server em VMs do Azure.
ms.custom: ''
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.devlang: ''
ms.topic: how-to
author: markjones-msft
ms.author: markjon
ms.reviewer: mathoma
ms.date: 11/06/2020
ms.openlocfilehash: 2702451aa3645b2702f8f38c37574a6601249dac
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105641410"
---
# <a name="migration-overview-sql-server-to-sql-server-on-azure-vms"></a>Visão geral da migração: SQL Server para SQL Server em VMs do Azure
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlvm.md)]

Saiba mais sobre as diferentes estratégias de migração para migrar seus SQL Server para SQL Server em VMs (máquinas virtuais) do Azure. 

Você pode migrar SQL Server em execução no local ou em:

- SQL Server em Máquinas Virtuais  
- Amazon Web Services (AWS) EC2 
- Serviço de banco de dados relacional do Amazon (AWS RDS) 
- Mecanismo de computação (Google Cloud Platform-GCP)

Para obter outros guias de migração, confira [Migração de banco de dados](https://docs.microsoft.com/data-migration). 

## <a name="overview"></a>Visão geral

Migre para [SQL Server em VMS (máquinas virtuais) do Azure](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) quando desejar usar o ambiente de SQL Server familiar com o controle do sistema operacional e desejar aproveitar os recursos fornecidos pela nuvem, como a alta disponibilidade interna da VM, [backups automatizados](../../virtual-machines/windows/automated-backup.md)e [aplicação de patch automatizada](../../virtual-machines/windows/automated-patching.md). 

Economize em custos ao trazer sua própria licença com o [modelo de licenciamento benefício híbrido do Azure](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md) ou estenda o suporte para SQL Server 2008 e SQL Server 2008 R2 obtendo [atualizações de segurança gratuitas](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md). 


## <a name="choose-appropriate-target"></a>Escolha o destino apropriado

As máquinas virtuais do Azure são executadas em muitas regiões diferentes do Azure e também oferecem uma variedade de [tamanhos de máquina](../../../virtual-machines/sizes.md) e [Opções de armazenamento](../../../virtual-machines/disks-types.md). Ao determinar o tamanho correto da VM e do armazenamento para sua carga de trabalho SQL Server, consulte as [diretrizes de desempenho para SQL Server em máquinas virtuais do Azure.](../../virtual-machines/windows/performance-guidelines-best-practices.md#vm-size-guidance) Para determinar o tamanho da VM e os requisitos de armazenamento para sua carga de trabalho. é recomendável que eles sejam dimensionados por meio de uma [avaliação de migração Performance-Based Azure](../../../migrate/concepts-assessment-calculation.md#types-of-assessments). Se essa não for uma opção disponível, consulte o seguinte artigo sobre como criar sua própria [linha de base para desempenho](https://azure.microsoft.com/services/virtual-machines/sql-server/).

A consideração também deve ser feita na instalação e configuração corretas de SQL Server em uma VM. É recomendável usar a [Galeria de imagens de máquina virtual do SQL do Azure](../../virtual-machines/windows/create-sql-vm-portal.md) , pois isso permite que você crie uma VM SQL Server com a versão, a edição e o sistema operacional corretos. Isso também registrará a VM do Azure com o [provedor de recursos](../../virtual-machines/windows/create-sql-vm-portal.md) SQL Server automaticamente, habilitando recursos como backups automatizados e aplicação de patch automatizada.

## <a name="migration-strategies"></a>Estratégias de migração

Há duas estratégias de migração para migrar seus bancos de dados de usuário para uma instância do SQL Server em VMs do Azure: **migrar** e **mover e deslocar**. 

A abordagem apropriada para sua empresa normalmente depende dos seguintes fatores: 

- Tamanho e escala da migração
- Velocidade de migração
- Suporte a aplicativos para alteração de código
- É necessário alterar SQL Server versão, sistema operacional ou ambos.
- Ciclo de vida de suporte de seus produtos existentes
- Janela para tempo de inatividade do aplicativo durante a migração

:::image type="content" source="media/sql-server-to-sql-on-azure-vm-individual-databases-guide/virtual-machine-migration-downtime.png" alt-text="tempo de inatividade de migração de máquina virtual":::

A tabela a seguir descreve as diferenças nas duas estratégias de migração:
<br />

| **Estratégia de migração** | **Descrição** | **Quando usar** |
| --- | --- | --- |
| **Levantar & Shift** | Use a estratégia de migração de comparação e de deslocamento para mover todo o SQL Server físico ou virtual de seu local atual para uma instância do SQL Server na VM do Azure sem nenhuma alteração no sistema operacional ou na versão SQL Server. Para concluir uma migração de comparação de precisão e de deslocamento, consulte [migrações para Azure](../../../migrate/migrate-services-overview.md). <br /><br /> O servidor de origem permanece online e solicita solicitações de serviços enquanto o servidor de origem e de destino sincroniza dados, permitindo uma migração quase direta. | Use para migrações de grande escala, até mesmo aplicáveis a cenários como data center sair. <br /><br /> Mínimo de nenhuma alteração de código necessária para bancos de dados ou aplicativos SQL do usuário, permitindo migrações gerais mais rápidas. <br /><br />Nenhuma etapa adicional é necessária para migrar os serviços de Business Intelligence, como  [SSIS](/sql/integration-services/sql-server-integration-services), [SSRS](/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports)e [SSAS](/analysis-services/analysis-services-overview). |
|**Migrar** | Use uma estratégia de migração quando desejar atualizar o SQL Server de destino e/ou a versão do sistema operacional. <br /> <br /> Selecione uma VM do Azure no Azure Marketplace ou uma imagem de SQL Server preparada que corresponda à versão do SQL Server de origem. | Use quando houver um requisito ou desejo de usar recursos disponíveis em versões mais recentes do SQL Server, ou se houver um requisito para atualizar SQL Server herdadas e/ou versões do sistema operacional que não têm mais suporte.  <br /> <br /> Pode exigir alterações de aplicativo ou de banco de dados de usuário para dar suporte à atualização de SQL Server. <br /><br />Pode haver considerações adicionais para migrar os serviços de [Business Intelligence](#business-intelligence) se estiver no escopo da migração. |


## <a name="lift-and-shift"></a>Lift-and-shift  

A tabela a seguir detalha o método disponível para a estratégia de migração de comparação **e de deslocamento** para migrar seu banco de dados SQL Server para SQL Server em VMs do Azure:
<br />

|**Método** | **Versão de origem mínima** | **Versão de destino mínima** | **Restrição de tamanho do backup de origem** |  **Observações** |
| --- | --- | --- | --- | --- |
| [Migrações para Azure](../../../migrate/index.yml) | SQL Server 2008 SP4| SQL Server 2008 SP4| [Limite de armazenamento da VM do Azure](../../../index.yml) |  SQL Server existente a ser movido no estado em que se encontra em uma instância de SQL Server em uma VM do Azure. Pode dimensionar as cargas de trabalho de migração de até 35.000 VMs. <br /><br /> Os servidores de origem permanecem online e atendem às solicitações durante a sincronização de dados do servidor, minimizando o tempo de inatividade. <br /><br /> **Script de & de automação**: [scripts de Azure site Recovery](../../../migrate/how-to-migrate-at-scale.md) e [exemplo de migração e planejamento em escala para o Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)|

## <a name="migrate"></a>Migrar  

Devido à facilidade de instalação, a abordagem de migração recomendada é pegar um [backup](/sql/t-sql/statements/backup-transact-sql) de SQL Server nativo localmente e, em seguida, copiar o arquivo para o Azure. Esse método dá suporte a bancos de dados maiores (>1 TB) para todas as versões do SQL Server a partir de 2008 e backups de bancos de dados maiores (>1 TB). No entanto, para bancos de dados que começam no SQL Server 2014, que são menores que 1 TB e que têm boa conectividade com o Azure, [SQL Server Backup para URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url) é a melhor abordagem. 

Ao migrar SQL Server bancos de dados para uma instância do SQL Server em VMs do Azure, é importante escolher uma abordagem que se adapte quando você precisa fazer a transição para o servidor de destino, pois isso afeta a janela de tempo de inatividade do aplicativo.

A tabela a seguir fornece detalhes sobre todos os métodos disponíveis para migrar seu banco de dados SQL Server para SQL Server em VMs do Azure:
<br />

|**Método** | **Versão de origem mínima** | **Versão de destino mínima** | **Restrição de tamanho do backup de origem** | **Observações** |
| --- | --- | --- | --- | --- |
| **[Backup em um arquivo](sql-server-to-sql-on-azure-vm-individual-databases-guide.md#migrate)** | SQL Server 2008 SP4 | SQL Server 2008 SP4| [Limite de armazenamento da VM do Azure](../../../index.yml) |  Essa é uma técnica simples e bem testada para mover bancos de dados entre computadores. Use a compactação para minimizar o tamanho do backup para transferência. <br /><br /> **Automação & scripting**: [Transact-SQL (T-SQL)](/sql/t-sql/statements/backup-transact-sql) e [AzCopy para o armazenamento de BLOBs](../../../storage/common/storage-use-azcopy-v10.md)  |
| **[Backup para URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url)** | SQL Server 2012 SP1 CU2 | SQL Server 2012 SP1 CU2| 12,8 TB para SQL Server 2016, caso contrário, 1 TB | Uma maneira alternativa de mover o arquivo de backup para a VM usando o armazenamento do Azure. Use a compactação para minimizar o tamanho do backup para transferência. <br /><br /> **Script de & de automação**:  [T-SQL ou plano de manutenção](/sql/relational-databases/backup-restore/sql-server-backup-to-url) |
| **[DMA (Assistente de Migração de Banco de Dados)](/sql/dma/dma-overview)** | SQL Server 2005| SQL Server 2008 SP4| [Limite de armazenamento da VM do Azure](../../../index.yml) |  O [DMA](/sql/dma/dma-overview) avalia SQL Server local e, em seguida, atualiza diretamente para versões posteriores do SQL Server ou migra para SQL Server em VMs do Azure, banco de dados SQL do Azure ou azure SQL instância gerenciada. <br /><br /> Não deve ser usado em bancos de dados de usuário habilitados para FileStream.<br /><br /> O DMA também inclui a capacidade de migrar [logons do SQL e do Windows](/sql/dma/dma-migrateserverlogins) e avaliar os [pacotes do SSIS](/sql/dma/dma-assess-ssis). <br /><br /> **Script de & de automação**: [interface de linha de comando](/sql/dma/dma-commandline) |
| **[Desanexar e anexar](../../virtual-machines/windows/migrate-to-vm-from-sql-server.md#detach-and-attach-from-a-url)** | SQL Server 2008 SP4 | SQL Server 2014 | [Limite de armazenamento da VM do Azure](../../../index.yml) | Use esse método ao planejar [armazenar esses arquivos usando o serviço de armazenamento de BLOBs do Azure](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure) e anexá-los a uma instância do SQL Server em uma VM do Azure, particularmente útil com bancos de dados muito grandes ou quando o tempo de backup e restauração for muito longo. <br /><br /> **Automação & scripting**:  [T-SQL](/sql/relational-databases/databases/detach-a-database#TsqlProcedure) e [AzCopy para o armazenamento de BLOBs](../../../storage/common/storage-use-azcopy-v10.md)|
|**[Envio de logs](sql-server-to-sql-on-azure-vm-individual-databases-guide.md#migrate)** | SQL Server 2008 SP4 (somente Windows) | SQL Server 2008 SP4 (somente Windows) | [Limite de armazenamento da VM do Azure](../../../index.yml) | O envio de logs Replica arquivos de log transacionais do local para uma instância do SQL Server em uma VM do Azure. <br /><br /> Isso fornece tempo de inatividade mínimo durante o failover e tem menos sobrecarga de configuração do que configurar um grupo de disponibilidade Always On. <br /><br /> **Script de & de automação**: [T-SQL](/sql/database-engine/log-shipping/log-shipping-tables-and-stored-procedures)  |
| **[Grupo de disponibilidade distribuída](../../virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md#hybrid-it-disaster-recovery-solutions)** | SQL Server 2016| SQL Server 2016 | [Limite de armazenamento da VM do Azure](../../../index.yml) |  Um [grupo de disponibilidade distribuído](/sql/database-engine/availability-groups/windows/distributed-availability-groups) é um tipo especial de grupo de disponibilidade que abrange dois grupos de disponibilidade separados. Os grupos de disponibilidade que participam de um grupo de disponibilidade distribuído não precisam estar no mesmo local e incluir suporte entre domínios. <br /><br /> Esse método minimiza o tempo de inatividade, use quando você tiver um grupo de disponibilidade configurado localmente. <br /><br /> **Script de & de automação**: [T-SQL](/sql/t-sql/statements/alter-availability-group-transact-sql)  |
| | | | | |

&nbsp; &nbsp; &nbsp;&nbsp; &nbsp; &nbsp;
 
> [!TIP]
> Para transferências de dados grandes com opções limitadas a nenhuma rede, consulte [transferências de dados grandes com conectividade limitada](../../../storage/common/storage-solution-large-dataset-low-network.md).
> 

### <a name="considerations"></a>Considerações

Veja a seguir uma lista dos principais pontos a serem considerados ao revisar os métodos de migração:

- Para obter o melhor desempenho de transferência de dados, migre os bancos e os arquivos para uma instância do SQL Server na VM do Azure usando um arquivo de backup compactado. Para bancos de dados maiores, além da compactação, [divida o arquivo de backup em arquivos menores](/sql/relational-databases/backup-restore/back-up-files-and-filegroups-sql-server) para aumentar o desempenho durante o backup e a transferência. 
- Se estiver migrando do SQL Server 2014 ou superior, considere [criptografar os backups](/sql/relational-databases/backup-restore/backup-encryption) para proteger os dados durante a transferência de rede.
- Para minimizar o tempo de inatividade durante a migração do banco de dados, use a opção Always On grupo de disponibilidade. 
- Para minimizar o tempo de inatividade sem a sobrecarga de configurar um grupo de disponibilidade, use a opção de envio de logs. 
- Para limitar a nenhuma opção de rede, use métodos de migração offline, como backup e restauração, ou [serviços de transferência de disco](../../../storage/common/storage-solution-large-dataset-low-network.md) disponíveis no Azure.
- Para alterar também a versão do SQL Server em um SQL Server na VM do Azure, consulte [change SQL Server Edition](../../virtual-machines/windows/change-sql-server-edition.md).

## <a name="business-intelligence"></a>Business Intelligence 

Pode haver considerações adicionais ao migrar SQL Server serviços de Business Intelligence fora do escopo das migrações de banco de dados do usuário. 

Esses serviços incluem:

- [**SSIS (SQL Server Integration Service)**](/sql/integration-services/install-windows/upgrade-integration-services)
- [**SSRS (SQL Server Reporting Services)**](/sql/reporting-services/install-windows/upgrade-and-migrate-reporting-services)
- [**SSAS (SQL Server Analysis Services)**](/sql/database-engine/install-windows/upgrade-analysis-services)

## <a name="supported-versions"></a>Versões com suporte

Ao se preparar para migrar SQL Server bancos de dados para SQL Server em VMs do Azure, certifique-se de considerar as versões do SQL Server que têm suporte. Para obter uma lista de versões de SQL Server com suporte atuais em VMs do Azure, consulte [SQL Server em VMs do Azure](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md#get-started-with-sql-server-vms).

## <a name="migration-assets"></a>Ativos de migração 

Para obter assistência adicional, consulte os recursos a seguir que foram desenvolvidos para projetos de migração do mundo real.

|Ativo  |Descrição  |
|---------|---------|
|[Modelo e ferramenta de avaliação de carga de trabalho de dados](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Data%20Workload%20Assessment%20Model%20and%20Tool)| Essa ferramenta dá sugestão das plataformas de destino de "melhor ajuste", da preparação para a nuvem e do nível de correção de aplicativo/banco de dados para uma determinada carga de trabalho. Ela oferece um cálculo simples, com um único clique, e oferece a geração de relatórios que ajudam a acelerar avaliações de grandes volumes fornecendo um processo de decisão de plataforma de destino uniforme e automatizado.|
|[Automação de coleta de dados de Perfmon usando logman](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Perfmon%20Data%20Collection%20Automation%20Using%20Logman)|Uma ferramenta que coleta dados de execução para entender o desempenho de linha de base que auxilia na recomendação de destino de migração. Essa ferramenta que usa logman.exe para criar o comando que irá criar, iniciar, parar e excluir contadores de desempenho definidos em um SQL Server remoto.|
|[Implantação de SQL Server no Azure](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/SQL%20Server%20Deployment%20in%20Azure%20.pdf)|Este white paper de diretrizes ajuda a revisar várias opções para mover suas cargas de trabalho de SQL Server para o Azure, incluindo comparação de recursos, alta disponibilidade e considerações de backup/armazenamento. |
|[SQL Server local para a máquina virtual do Azure](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/OnPremise%20SQL%20Server%20to%20Azure%20VM.pdf)|Este white paper descreve as etapas para fazer backup e restaurar bancos de dados do SQL Server local para SQL Server na máquina virtual do Azure usando scripts de exemplo.|
|[Multiple-SQL-VM-VNet-ILB](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/ARM%20Templates/Multiple-SQL-VM-VNet-ILB)|Este white paper descreve as etapas para configurar várias máquinas virtuais do Azure em um SQL Server Always On configuração de grupo de disponibilidade.|
|[Máquinas virtuais do Azure com suporte a SSD Ultra por região](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Find%20Azure%20VMs%20supporting%20Ultra%20SSD)|Esses scripts do PowerShell fornecem uma opção programática para recuperar a lista de regiões que dão suporte a máquinas virtuais do Azure com suporte a ultra SSDs.|

A equipe de engenharia de SQL de dados desenvolveu esses recursos. O principal compromisso desta equipe é desbloquear e acelerar a modernização complexa para projetos de migração da plataforma de dados para a plataforma de dados do Azure da Microsoft.

## <a name="next-steps"></a>Próximas etapas

Para começar a migrar seus bancos de dados SQL Server para SQL Server em VMs do Azure, consulte o [Guia de migração de banco de dados individual](sql-server-to-sql-on-azure-vm-individual-databases-guide.md). 

- Para obter uma matriz de serviços e ferramentas da Microsoft e de terceiros que estão disponíveis para ajudá-lo com vários cenários de migração de banco de dados e de aplicativos, bem como tarefas de especialidade, consulte o artigo [serviço e ferramentas para a migração de dados.](../../../dms/dms-tools-matrix.md)

- Para saber mais sobre o SQL Azure, consulte:
   - [Opções de implantação](../../azure-sql-iaas-vs-paas-what-is-overview.md)
   - [SQL Server em VMs do Azure](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
   - [Calculadora de custo total de Propriedade do Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Para saber mais sobre a estrutura e o ciclo de adoção para migrações na nuvem, consulte
   -  [Cloud Adoption Framework para Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Práticas recomendadas para o custo e o dimensionamento de cargas de trabalho migrar para o Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Para obter informações sobre licenciamento, consulte
   - [Traga sua própria licença com o Benefício Híbrido do Azure](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md)
   - [Obtenha suporte estendido gratuito para SQL Server 2008 e SQL Server 2008 R2](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)


- Para avaliar a camada de acesso do aplicativo, consulte [Kit de ferramentas de migração de acesso a dados (versão prévia)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Para obter detalhes sobre como executar testes de camada A/B de acesso A dados, consulte [Assistente para experimentos de banco de dados](/sql/dea/database-experimentation-assistant-overview).