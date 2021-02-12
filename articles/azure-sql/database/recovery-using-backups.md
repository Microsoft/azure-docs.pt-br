---
title: Restaurar um banco de dados usando um backup
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Saiba mais sobre a restauração pontual, que permite reverter um banco de dados no banco de dados SQL do Azure ou uma instância no Azure SQL Instância Gerenciada de até 35 dias.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein, danil
ms.date: 11/13/2020
ms.openlocfilehash: 0c3db3b3f22f9f2639012068924708537f9ada77
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98795326"
---
# <a name="recover-using-automated-database-backups---azure-sql-database--sql-managed-instance"></a>Recuperar usando backups automatizados de banco de dados-banco de dados SQL do Azure & SQL Instância Gerenciada
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

As opções a seguir estão disponíveis para recuperação do banco de dados que usam [backups de banco de dados automatizados](automated-backups-overview.md). Você pode:

- Crie um novo banco de dados no mesmo servidor, recuperado para um momento determinado dentro do período de retenção.
- Crie um banco de dados no mesmo servidor, recuperado para a hora de exclusão de um banco de dados excluído.
- Crie um novo banco de dados em qualquer servidor na mesma região, recuperado para o ponto dos backups mais recentes.
- Crie um novo banco de dados em qualquer servidor em qualquer outra região, recuperado para o ponto dos backups replicados mais recentes.

Se você configurou o [backup de retenção de longo prazo](long-term-retention-overview.md), também poderá criar um novo banco de dados de qualquer backup de retenção de longo prazo em qualquer servidor.

> [!IMPORTANT]
> Não é possível substituir um banco de dados existente durante a restauração.

Quando você estiver usando a camada de serviço Standard ou Premium, a restauração do banco de dados poderá incorrer em um custo de armazenamento extra. O custo extra é incorrido quando o tamanho máximo do banco de dados restaurado é maior do que a quantidade de armazenamento incluída na camada de serviço e no nível de desempenho do banco de dados de destino. Para obter detalhes de preço do armazenamento extra, confira a página [Preços do Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/). Se a quantidade real de espaço usado for menor do que a quantidade de armazenamento incluída, esse custo extra poderá ser evitado por meio da configuração do tamanho máximo do banco de dados para a quantidade incluída.

## <a name="recovery-time"></a>Tempo de recuperação

O tempo de recuperação para restaurar um banco de dados ao usar backups de banco de dados automatizado é afetado por vários fatores:

- O tamanho do banco de dados.
- O tamanho da computação do banco de dados.
- O número de logs de transações envolvidos.
- A quantidade de atividade que precisa ser repetida para recuperar até o ponto de restauração.
- A largura de banda de rede se a restauração for para uma região diferente.
- O número de solicitações simultâneas de restauração que estão sendo processadas na região de destino.

Para um banco de dados muito grande ou muito ativo, a restauração pode levar várias horas. Quando há uma interrupção prolongada em uma região, é possível que um número alto de solicitações de restauração geográfica seja iniciado para recuperação de desastre. Quando houver muitas solicitações, o tempo de recuperação dos bancos de dados individuais poderá aumentar. A maioria das restaurações de banco de dados é concluída em menos de 12 horas.

Para uma assinatura única, há limitações no número de solicitações de restauração simultâneas. Essas limitações se aplicam a qualquer combinação de restaurações pontuais, geográficas e do backup de retenção de longo prazo.

| **Opção de implantação** | **Número máximo de solicitações simultâneas que estão sendo processadas** | **Número máximo de solicitações simultâneas que estão sendo enviadas** |
| :--- | --: | --: |
|**Banco de dados único (por assinatura)**|30|100|
|**Pool Elástico (por pool)**|4|2000|


Não há um método interno para restaurar o servidor inteiro. Para obter um exemplo de como realizar essa tarefa, consulte [banco de dados SQL do Azure: recuperação completa do servidor](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666).

> [!IMPORTANT]
> Para recuperar usando backups automatizados, você deve ser membro da função colaborador do SQL Server ou da função colaborador do SQL Instância Gerenciada (dependendo do destino de recuperação) na assinatura ou deve ser o proprietário da assinatura. Para obter mais informações, consulte [RBAC do Azure: funções internas](../../role-based-access-control/built-in-roles.md). Você pode usar o portal do Azure, o PowerShell ou a API REST para a recuperação. Não é possível usar o T-SQL.

## <a name="point-in-time-restore"></a>Restauração em um momento determinado

É possível restaurar um banco de dados autônomo, em pool ou de instância para um ponto anterior no tempo usando o portal do Azure, [PowerShell](/powershell/module/az.sql/restore-azsqldatabase) ou a [API REST](/rest/api/sql/databases/createorupdate#creates-a-database-from-pointintimerestore.). A solicitação pode especificar qualquer camada de serviço ou tamanho da computação para o banco de dados restaurado. Você precisa ter recursos suficientes no servidor para o qual você está restaurando o banco de dados. 

Ao concluir, a restauração cria um novo banco de dados no mesmo servidor do banco de dados original. O banco de dados restaurado é cobrado a taxas normais com base no tamanho da computação e na camada de serviço. Você não incorrerá em encargos até que a restauração do banco de dados seja concluída.

Um banco de dados geralmente é restaurado para um ponto anterior para fins de recuperação. Você pode tratar o banco de dados restaurado como uma substituição para o banco de dados original ou usá-lo como uma fonte de dados para atualizar o banco de dado original.

- **Substituição de banco de dados**

  Se você pretende que o banco de dados restaurado substitua o original, especifique o tamanho de computação e a camada de serviço do banco de dados original. Em seguida, você pode renomear o banco de dados original e dar ao banco de dados restaurado o nome original usando o comando [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) no T-SQL.

- **Recuperação de dados**

  Se você planeja recuperar dados do banco de dado restaurado para se recuperar de um erro de usuário ou aplicativo, você precisa gravar e executar um script de recuperação de dados que extrai dados do banco de dado restaurado e aplica-se ao banco de dado original. Embora a operação de restauração possa demorar muito para concluir, o banco de dados em restauração é visível na lista de banco de dados por todo o processo de restauração. Se você excluir o banco de dados durante a restauração, a operação de restauração será cancelada e você não será cobrado pelo banco de dados cuja restauração não foi concluída.
  
### <a name="point-in-time-restore-by-using-azure-portal"></a>Restauração pontual usando o portal do Azure

Você pode recuperar um banco de dados individual ou de instância em um ponto no tempo da folha de visão geral do banco de dados que deseja restaurar no portal do Azure.

#### <a name="sql-database"></a>Banco de Dados SQL

Para recuperar um banco de dados em um ponto no tempo usando o portal do Azure, abra a página Visão geral do banco de dados e selecione **restaurar** na barra de ferramentas. Escolha a fonte de backup e selecione o ponto de backup pontual a partir do qual um novo banco de dados será criado.

  ![Captura de tela das opções de restauração do banco de dados SQL.](./media/recovery-using-backups/pitr-backup-sql-database-annotated.png)

#### <a name="sql-managed-instance"></a>Instância Gerenciada do SQL

Para fazer a recuperação pontual de um banco de dados de instância gerenciada usando o portal do Azure, abra a página de visão geral do banco de dados e selecione **Restaurar** na barra de ferramentas. Escolha o ponto de backup pontual a partir do qual um novo banco de dados será criado.

  ![Captura de tela das opções de restauração do banco de dados para instância gerenciada do SQL.](./media/recovery-using-backups/pitr-backup-managed-instance-annotated.png)

> [!TIP]
> Para restaurar programaticamente um banco de dados de um backup, consulte [recuperação programática usando backups automatizados](recovery-using-backups.md).

## <a name="deleted-database-restore"></a>Restauração de banco de dados excluído

Você pode restaurar um banco de dados excluído para a hora de exclusão ou um ponto anterior no tempo, no mesmo servidor ou na mesma instância gerenciada. Você pode fazer isso pelo portal do Azure, [PowerShell](/powershell/module/az.sql/restore-azsqldatabase) ou [REST (createMode=Restore)](/rest/api/sql/databases/createorupdate). Para restaurar um banco de dados excluído, crie um novo banco de dados do backup.

> [!IMPORTANT]
> Se você excluir um servidor ou instância gerenciada, todos os seus bancos de dados também serão excluídos e não poderão ser recuperados. Não é possível restaurar um servidor excluído ou uma instância gerenciada.

### <a name="deleted-database-restore-by-using-the-azure-portal"></a>Restauração do banco de dados excluído usando o portal do Azure

Você restaura bancos de dados excluídos do portal do Azure do servidor ou do recurso de instância gerenciada.

> [!TIP]
> Pode levar vários minutos para os bancos de dados excluídos recentemente aparecerem na página **bancos de dados excluídos** no portal do Azure ou ao exibir bancos de dados excluídos [programaticamente](#programmatic-recovery-using-automated-backups).

#### <a name="sql-database"></a>Banco de Dados SQL

Para recuperar um banco de dados excluído para a hora de exclusão usando o portal do Azure, abra a página Visão geral do servidor e selecione **bancos de dados excluídos**. Selecione um banco de dados excluído que você quer restaurar e digite o nome do novo banco de dados que será criado com os dados restaurados do backup.

  ![Captura de tela de restaurar banco de dados excluído](./media/recovery-using-backups/restore-deleted-sql-database-annotated.png)

#### <a name="sql-managed-instance"></a>Instância Gerenciada do SQL

Para recuperar um banco de dados gerenciado usando o portal do Azure, abra a página de visão geral da instância gerenciada e selecione **Bancos de dados excluídos**. Selecione um banco de dados excluído que você quer restaurar e digite o nome do novo banco de dados que será criado com os dados restaurados do backup.

  ![Captura de tela de restauração excluída do banco de dados SQL Instância Gerenciada do Azure](./media/recovery-using-backups/restore-deleted-sql-managed-instance-annotated.png)

### <a name="deleted-database-restore-by-using-powershell"></a>Restauração de banco de dados excluído usando o PowerShell

Use os scripts de exemplo a seguir para restaurar um banco de dados excluído para o banco de dados SQL ou o SQL Instância Gerenciada usando o PowerShell.

#### <a name="sql-database"></a>Banco de Dados SQL

Para obter um exemplo de script do PowerShell mostrando como restaurar um banco de dados excluído no banco de dados SQL do Azure, consulte [restaurar um banco de dados usando o PowerShell](scripts/restore-database-powershell.md).

#### <a name="sql-managed-instance"></a>Instância Gerenciada do SQL

Para obter um exemplo de script do PowerShell mostrando como restaurar um banco de dados de instância excluído, consulte [restaurar banco de dados de instância excluído usando o PowerShell](../managed-instance/point-in-time-restore.md#restore-a-deleted-database)

> [!TIP]
> Para restaurar programaticamente um banco de dados excluído, consulte [Programando recuperação programaticamente usando backups automatizados](recovery-using-backups.md).

## <a name="geo-restore"></a>Restauração geográfica

> [!IMPORTANT]
> A restauração geográfica está disponível somente para bancos de dados SQL ou instâncias gerenciadas configuradas com [armazenamento de backup](automated-backups-overview.md#backup-storage-redundancy)com redundância geográfica.

Você pode restaurar um banco de dados em qualquer servidor de banco de dados SQL ou em um banco de dados de instância em qualquer instância gerenciada em qualquer região do Azure dos backups replicados geograficamente mais recentes. A restauração geográfica usa um backup de replicação geográfica como origem. Você pode solicitar restauração geográfica mesmo que o banco de dados ou o datacenter esteja inacessível devido a uma interrupção.

A restauração geográfica é a opção de recuperação padrão quando o banco de dados não estiver disponível devido a um incidente na região de hospedagem. É possível restaurar o banco de dados para um servidor em qualquer outra região. Há um atraso entre o momento em que um backup é realizado e quando ele é replicado geograficamente para um blob do Azure em uma região diferente. Como resultado, o banco de dados restaurado pode ter até uma hora de atraso em relação ao banco de dados original. A ilustração a seguir mostra uma restauração do banco de dados desde o último backup disponível em outra região.

![Gráfico da restauração geográfica](./media/recovery-using-backups/geo-restore-2.png)

### <a name="geo-restore-by-using-the-azure-portal"></a>Restauração geográfica usando o portal do Azure

No portal do Azure, você cria um novo banco de dados de instância única ou gerenciada e seleciona um backup de restauração geográfica disponível. O banco de dados recém-criado contém os dados de backup da restauração geográfica.

#### <a name="sql-database"></a>Banco de Dados SQL

Para restaurar geograficamente um único banco de dados do portal do Azure na região e no servidor de sua escolha, siga estas etapas:

1. No **painel**, selecione **Adicionar** > **Criar Banco de Dados SQL**. Na guia **Noções básicas**, insira as informações necessárias.
2. Selecione **Configurações adicionais**.
3. Para **usar dados existentes**, selecione **Backup**.
4. Para **fazer backup**, selecione um backup na lista de backups de restauração geográfica disponíveis.

    ![Captura de tela das opções para criar Banco de Dados SQL](./media/recovery-using-backups/geo-restore-azure-sql-database-list-annotated.png)

Conclua o processo de criação de um novo banco de dados a partir do backup. Quando você cria um banco de dados no banco de dados SQL do Azure, ele contém o backup de restauração geográfica restaurado.

#### <a name="sql-managed-instance"></a>Instância Gerenciada do SQL

Para restaurar geograficamente um banco de dados de instância gerenciada do portal do Azure para uma instância gerenciada em uma região da sua escolha, selecione uma instância gerenciada na qual você deseja restaurar um banco de dados. Siga estas etapas:

1. Selecione **Novo banco de dados**.
2. Digite um nome de banco de dados desejado.
3. Em **Usar dados existentes**, selecione **Backup**.
4. Selecione um backup na lista de backups de restauração geográfica disponíveis.

    ![Captura de tela das opções de novo banco de dados](./media/recovery-using-backups/geo-restore-sql-managed-instance-list-annotated.png)

Conclua o processo de criação de um novo banco de dados. Ao criar o banco de dados de instância, ele conterá o backup de restauração geográfica restaurado.

### <a name="geo-restore-by-using-powershell"></a>Restauração geográfica usando o PowerShell

#### <a name="sql-database"></a>Banco de Dados SQL

Para um script do PowerShell que mostra como executar a restauração geográfica para um único banco de dados, consulte [usar o PowerShell para restaurar um banco de dados individual para um ponto anterior no tempo](scripts/restore-database-powershell.md).

#### <a name="sql-managed-instance"></a>Instância Gerenciada do SQL

Para um script do PowerShell que mostre como executar a restauração geográfica de um banco de dados de instância gerenciada, consulte [Usar o PowerShell para restaurar um banco de dados de Instância Gerenciada para outra região geográfica](../managed-instance/scripts/restore-geo-backup.md).

### <a name="geo-restore-considerations"></a>Considerações sobre restauração geográfica

Não é possível executar uma restauração pontual em um banco de dados geográfico secundário. Você pode fazer isso somente em um banco de dados primário. Para obter informações detalhadas sobre como usar a restauração geográfica para se recuperar de uma interrupção, consulte [Recuperação de uma interrupção](../../key-vault/general/disaster-recovery-guidance.md).

> [!IMPORTANT]
> A restauração geográfica é a solução de recuperação de desastre mais básica disponível no banco de dados SQL e no SQL Instância Gerenciada. Ele depende de backups replicados geograficamente criados automaticamente com um RPO (objetivo de ponto de recuperação) de até 1 hora e um tempo de recuperação estimado de até 12 horas. Ela não garante que a região de destino terá a capacidade de restaurar seus bancos de dados após uma interrupção regional, devido à possibilidade de um grande aumento de demanda. Se seu aplicativo usa bancos de dados relativamente pequenos e não é essencial para os negócios, a restauração geográfica é uma solução de recuperação de desastre apropriada. 
>
> Para aplicativos comercialmente críticos que exigem grandes bancos de dados e devem garantir a continuidade dos negócios, use [grupos de failover automático](auto-failover-group-overview.md). Eles oferecem RPO e objetivo de tempo de recuperação menores e há sempre a garantia de capacidade. 
>
> Para obter mais informações sobre as opções de continuidade de negócios, consulte [visão geral da continuidade dos negócios](business-continuity-high-availability-disaster-recover-hadr-overview.md).

## <a name="programmatic-recovery-using-automated-backups"></a>Recuperação programática usando backups automatizados

Você também pode usar Azure PowerShell ou a API REST para recuperação. As tabelas a seguir descrevem o conjunto de comandos disponíveis.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell ainda tem suporte do banco de dados SQL e SQL Instância Gerenciada, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. Para esses cmdlets, confira [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo AZ e em Azure Resource Manager módulos têm uma grande extensão idêntica.

#### <a name="sql-database"></a>Banco de Dados SQL

Para restaurar um banco de dados autônomo ou em pool, confira [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase).

  | Cmdlet | Descrição |
  | --- | --- |
  | [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) |Obtém um ou mais bancos de dados. |
  | [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | Obtém um banco de dados excluído que você pode restaurar. |
  | [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) |Obtém um backup com redundância geográfica de um banco de dados. |
  | [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) |Restaura um banco de dados. |

  > [!TIP]
  > Para obter um exemplo de script do PowerShell que mostra como executar uma restauração pontual de um banco de dados, consulte [restaurar um banco de dados usando o PowerShell](scripts/restore-database-powershell.md).

#### <a name="sql-managed-instance"></a>Instância Gerenciada do SQL

Para restaurar um banco de dados de instância gerenciada, consulte [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase).

  | Cmdlet | Descrição |
  | --- | --- |
  | [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance) |Obtém uma ou mais instâncias gerenciadas. |
  | [Get-AzSqlInstanceDatabase](/powershell/module/az.sql/get-azsqlinstancedatabase) | Obtém um banco de dados de instância. |
  | [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase) |Restaura um banco de dados de instância. |

### <a name="rest-api"></a>API REST

Para restaurar um banco de dados usando a API REST:

| API | Descrição |
| --- | --- |
| [REST (createMode=Recovery)](/rest/api/sql/databases) |Restaura um banco de dados. |
| [Obter, Criar ou Atualizar o Status de um Banco de Dados](/rest/api/sql/operations) |Retorna o status durante uma operação de restauração. |

### <a name="azure-cli"></a>CLI do Azure

#### <a name="sql-database"></a>Banco de Dados SQL

Para restaurar um banco de dados usando o CLI do Azure, consulte [AZ SQL DB Restore](/cli/azure/sql/db#az-sql-db-restore).

#### <a name="sql-managed-instance"></a>Instância Gerenciada do SQL

Para restaurar um banco de dados de instância gerenciada usando a CLI do Azure, consulte [az sql midb restore](/cli/azure/sql/midb#az-sql-midb-restore).

## <a name="summary"></a>Resumo

Backups automáticos protegem seus bancos de dados contra erros de usuário e de aplicativo, exclusão acidental do banco de dados e interrupções prolongadas. Essa funcionalidade interna está disponível para todas as camadas de serviço e tamanhos da computação.

## <a name="next-steps"></a>Próximas etapas

- [Visão geral da continuidade dos negócios](business-continuity-high-availability-disaster-recover-hadr-overview.md)
- [Backups automatizados do Banco de Dados SQL](automated-backups-overview.md)
- [Retenção de longo prazo](long-term-retention-overview.md)
- Para saber mais sobre opções de recuperação mais rápidas, confira [Replicação geográfica ativa](active-geo-replication-overview.md) ou [Grupos de failover automático](auto-failover-group-overview.md).
