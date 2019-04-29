---
title: Migrar uma Instância do SQL Server para uma Instância Gerenciada do Banco de Dados SQL do Azure | Microsoft Docs
description: Saiba como migrar uma Instância do SQL Server para uma Instância Gerenciada do Banco de Dados SQL do Azure.
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
ms.date: 02/11/2019
ms.openlocfilehash: 1460b595e8887fc932d5be335ae51b07a000b9fb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61315516"
---
# <a name="sql-server-instance-migration-to-azure-sql-database-managed-instance"></a>Migração de uma Instância do SQL Server para uma Instância Gerenciada do Banco de Dados SQL do Azure

Neste artigo, você aprenderá sobre os métodos para migrar uma Instância do SQL Server 2005 ou versão posterior para uma [Instância Gerenciada do Banco de Dados SQL do Azure](sql-database-managed-instance.md). Para obter informações sobre como migrar para um banco de dados individual ou pool elástico, confira [Migrar para um banco de dados individual ou em pool](sql-database-cloud-migrate.md). Para obter informações de migração sobre como migrar de outras plataformas, confira o [Guia de Migração de Banco de Dados do Azure](https://datamigration.microsoft.com/).

Em um nível alto, o processo de migração de banco de dados se parece com:

![processo de migração](./media/sql-database-managed-instance-migration/migration-process.png)

- [Avaliar a compatibilidade da instância gerenciada](#assess-managed-instance-compatibility)
- [Escolher a opção de conectividade do aplicativo](sql-database-managed-instance-connect-app.md)
- [Implantar em uma instância gerenciada de tamanho ideal](#deploy-to-an-optimally-sized-managed-instance)
- [Selecionar o método de migração e migrar](#select-migration-method-and-migrate)
- [Monitorar aplicativos](#monitor-applications)

> [!NOTE]
> Para migrar um banco de dados individual para um banco de dados individual ou um pool elástico, confira [Migrar um banco de dados do Microsoft SQL Server para o Banco de Dados SQL do Azure](sql-database-single-database-migrate.md).

## <a name="assess-managed-instance-compatibility"></a>Avaliar a compatibilidade da instância gerenciada

Primeiro, determine se a instância gerenciada é compatível com os requisitos de banco de dados do aplicativo. A opção de implantação de instância gerenciada foi projetada para fornecer migração lift-and-shift fácil para a maioria dos aplicativos existentes que usam o SQL Server local ou em máquinas virtuais. No entanto, às vezes é necessário recursos ou capacidades que ainda não têm suporte e o custo de implementar uma solução alternativa é muito alto.

Utilize o [DMA (Assistente de Migração de Dados)](https://docs.microsoft.com/sql/dma/dma-overview) para detectar possíveis problemas de compatibilidade que afetam a funcionalidade do banco de dados no Banco de Dados SQL do Azure. O AMD ainda não dá suporte à instância gerenciada como destino de migração, mas é recomendável executar a avaliação no Banco de Dados SQL do Azure e examinar atentamente a lista de problemas de compatibilidade e paridade de recursos relatados em relação à documentação do produto. Confira [Recursos do Banco de Dados SQL do Azure](sql-database-features.md) para verificar se há problemas de bloqueio relatados que não bloqueiam a instância gerenciada, porque a maioria dos problemas de bloqueio que impedem uma migração para o Banco de Dados SQL do Azure foi removida com a instância gerenciada. Por exemplo, recursos como consultas entre banco de dados, transações entre banco de dados na mesma instância, servidor vinculado a outras fontes do SQL, CLR, tabelas temporárias globais, exibições em nível de instância, Service Broker e similares estão disponíveis nas instâncias gerenciadas.

Se houver problemas de bloqueio relatados que não foram removidos com a opção de implantação de instância gerenciada, talvez seja preciso considerar uma opção alternativa, como o [SQL Server em Máquinas Virtuais do Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/). Estes são alguns exemplos:

- Se você precisar de acesso direto ao sistema operacional ou ao sistema de arquivos, por exemplo, para instalar agentes personalizados ou de terceiros na mesma máquina virtual com o SQL Server.
- Se você tiver uma dependência estrita de recursos que ainda não têm suporte, como transações entre instâncias, PolyBase e FileStream/FileTable.
- Se for absolutamente necessário permanecer em uma versão específica do SQL Server (2012, por instância).
- Se os requisitos de computação forem muito menores do que o oferecido pela instância gerenciada (um vCore, por exemplo) e a fusão de banco de dados não for uma opção aceitável.

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>Implantar em uma instância gerenciada de tamanho ideal

A instância gerenciada é adaptada para cargas de trabalho locais cuja movimentação para a nuvem é planejada. Ela introduz um [novo modelo de compra](sql-database-service-tiers-vcore.md) que oferece maior flexibilidade na seleção do nível certo de recursos para as cargas de trabalho. No ambiente local, você provavelmente está acostumado a dimensionar essas cargas de trabalho usando núcleos físicos e largura de banda de E/S. O modelo de compra para a instância gerenciada baseia-se em núcleos virtuais, ou “vCores”, com armazenamento adicional e E/S disponíveis separadamente. O modelo vCore é uma maneira mais simples de compreender os requisitos de computação na nuvem em relação ao que você utiliza no local atualmente. Esse novo modelo permite que você dimensione adequadamente o ambiente de destino na nuvem.

É possível selecionar recursos de armazenamento e computação em tempo de implantação e, depois, alterá-lo posteriormente sem introduzir o tempo de inatividade para o aplicativo usando o [portal do Azure](sql-database-scale-resources.md):

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

Acompanhe o comportamento e desempenho do aplicativo após a migração. Na instância gerenciada, algumas alterações são habilitadas somente quando o [nível de compatibilidade do banco de dados é alterado](https://docs.microsoft.com/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database). A migração de banco de dados para o Banco de Dados SQL do Azure mantém o nível de compatibilidade original na maioria dos casos. Se o nível de compatibilidade de um banco de dados de usuário fosse 100 ou superior antes da migração, ele permaneceria o mesmo após a migração. Se o nível de compatibilidade de um banco de dados de usuário era 90 antes da migração, no banco de dados atualizado, o nível de compatibilidade é definido como 100, que é o nível de compatibilidade mais baixo compatível com a instância gerenciada. O nível de compatibilidade dos bancos de dados do sistema é 140.

Para reduzir os riscos de migração, altere o nível de compatibilidade do banco de dados somente após o monitoramento do desempenho. Utilize o Repositório de Dados de Consultas como ferramenta ideal para obter informações sobre o desempenho da carga de trabalho, antes e após alterar o nível de compatibilidade do banco de dados, conforme explicado em [Manter a estabilidade do desempenho durante a atualização para a versão mais recente do SQL Server](https://docs.microsoft.com/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade).

Quando estiver em uma plataforma totalmente gerenciada, aproveite as vantagens que são fornecidas automaticamente como parte do serviço de Banco de Dados SQL. Por exemplo, não é necessário criar backups na instância gerenciada – o serviço faz backups para você automaticamente. Não é mais necessário preocupar-se com agendamento, execução e gerenciamento de backups. A instância gerenciada fornece a capacidade de restauração para qualquer momento no período de retenção usando o [PITR (Recuperação Pontual)](sql-database-recovery-using-backups.md#point-in-time-restore). Além disso, não é necessário preocupar-se com a configuração de alta disponibilidade, pois a [alta disponibilidade](sql-database-high-availability.md) é interna.

Para fortalecer a segurança, considere utilizar alguns dos recursos disponíveis:

- Autenticação do Azure Active Directory no nível do banco de dados
- Use [recursos de segurança avançados](sql-database-security-overview.md), como [Auditoria](sql-database-managed-instance-auditing.md), [Detecção de Ameaças](sql-database-advanced-data-security.md), [Segurança em Nível de Linha](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) e [Máscara de Dados Dinâmicos](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking), para proteger sua instância.

## <a name="next-steps"></a>Próximas etapas

- Para obter informações sobre instâncias gerenciadas, confira [O que é uma instância gerenciada?](sql-database-managed-instance.md).
- Para obter um tutorial que inclui uma restauração do backup, confira [Criar uma instância gerenciada](sql-database-managed-instance-get-started.md).
- Para obter um tutorial que mostra a migração usando o DMS, confira [Migrar o banco de dados local para uma instância gerenciada usando o DMS](../dms/tutorial-sql-server-to-managed-instance.md).  
