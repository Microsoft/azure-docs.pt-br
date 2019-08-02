---
title: Backups do Banco de Dados SQL do Azure automáticos e com redundância geográfica | Microsoft Docs
description: O Banco de dados SQL cria automaticamente um backup de banco de dados local a cada poucos minutos e usa o armazenamento com redundância geográfica de acesso de leitura do Azure para redundância geográfica.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 06/27/2019
ms.openlocfilehash: c75b19fff478c14ff47996cf9159e48f3ff69724
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68261191"
---
# <a name="automated-backups"></a>Backups automatizados

O banco de dados SQL cria automaticamente os backups de banco de dados que são mantidos entre 7 e 35 dias e usa o [armazenamento com redundância geográfica com acesso de leitura do Azure (ra-grs)](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) para garantir que eles sejam preservados mesmo que o Data Center não esteja disponível. Esses backups são criados automaticamente. Os backups de banco de dados são uma parte essencial de qualquer estratégia de recuperação de desastre e continuidade dos negócios, porque eles protegem seus dados contra exclusão ou corrupção acidentais. Se suas regras de segurança exigirem que seus backups estejam disponíveis por um longo período de tempo (até 10 anos), você poderá configurar uma [retenção de longo prazo](sql-database-long-term-retention.md) em bancos de dados singleton e pools elásticos.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>O que é um backup de banco de dados SQL

O banco de dados SQL usa a tecnologia SQL Server para criar [backups completos](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server) toda semana, [backups diferenciais](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server) a cada 12 horas e backups de log de [Transações](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) a cada 5-10 minutos. Os backups são armazenados em blobs de [armazenamento ra-grs](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) que são replicados para um [Data Center emparelhado](../best-practices-availability-paired-regions.md) para proteção contra uma interrupção Data Center. Quando você restaura um banco de dados, o serviço descobre quais backups completos, diferenciais e de log de transações precisam ser restaurados.

Use esses backups para:

- **Restaure um banco de dados existente para um ponto no tempo no passado** dentro do período de retenção usando o portal do Azure, Azure PowerShell, CLI do Azure ou a API REST. Em um banco de dados individual e pools elásticos, essa operação criará um novo banco de dados no mesmo servidor que o banco de dados original. No Instância Gerenciada, essa operação pode criar uma cópia do banco de dados ou o mesmo Instância Gerenciada ou diferente na mesma assinatura.
  - **[Altere o período de retenção de backup](#how-to-change-the-pitr-backup-retention-period)** entre 7 a 35 dias para configurar sua política de backup.
  - **Altere a política de retenção de longo prazo até 10 anos** em banco de dados individual e pools elásticos usando [o portal do Azure](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies) ou [Azure PowerShell](sql-database-long-term-backup-retention-configure.md#use-powershell-to-configure-long-term-retention-policies-and-restore-backups).
- **Restaure um banco de dados excluído para a hora em que ele foi excluído** ou a qualquer momento dentro do período de retenção. O banco de dados excluído só pode ser restaurado no mesmo servidor lógico ou Instância Gerenciada em que o banco de dados original foi criado.
- **Restaurar um banco de dados para outra região geográfica**. A restauração geográfica permite a recuperação de um desastre geográfico quando você não consegue acessar o servidor nem o banco de dados. Ele cria um novo banco de dados em qualquer servidor existente do mundo.
- **Restaure um banco de dados de um backup específico de longo prazo** no Banco de Dados Individual ou pool elástico se o banco de dados tiver sido configurado com uma EPD (política de retenção de longo prazo). EPD permite que você restaure uma versão antiga do banco de dados usando [o portal do Azure](sql-database-long-term-backup-retention-configure.md#view-backups-and-restore-from-a-backup-using-azure-portal) ou [Azure PowerShell](sql-database-long-term-backup-retention-configure.md#use-powershell-to-configure-long-term-retention-policies-and-restore-backups) para atender a uma solicitação de conformidade ou para executar uma versão antiga do aplicativo. Para obter mais informações, consulte [Retenção de longo prazo](sql-database-long-term-retention.md).
- Para executar uma restauração, consulte [Restaurar um banco de dados de backups](sql-database-recovery-using-backups.md).

> [!NOTE]
> No armazenamento do Azure, o termo *replicação* refere-se a copiar arquivos de uma localização para outra. *Replicação de banco de dados* do SQL significa manter vários bancos de dados secundários sincronizados com um banco de dados primário.

Você pode experimentar algumas dessas operações usando os seguintes exemplos:

| | O Portal do Azure | Azure PowerShell |
|---|---|---|
| Alterar retenção de backup | [Banco de Dados Individual](sql-database-automated-backups.md#change-pitr-backup-retention-period-using-the-azure-portal) <br/> [Instância Gerenciada](sql-database-automated-backups.md#change-pitr-for-a-managed-instance) | [Banco de Dados Individual](sql-database-automated-backups.md#change-pitr-backup-retention-period-using-powershell) <br/>[Instância Gerenciada](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| Alterar retenção de backup de longo prazo | [Banco de dados individual](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>Instância Gerenciada-N/A  | [Banco de Dados Individual](sql-database-long-term-backup-retention-configure.md#use-powershell-to-configure-long-term-retention-policies-and-restore-backups)<br/>Instância Gerenciada-N/A  |
| Restaurar o banco de dados do ponto no tempo | [Banco de dados individual](sql-database-recovery-using-backups.md#point-in-time-restore) | [Banco de dados individual](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [Instância Gerenciada](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| Restaurar banco de dados excluído | [Banco de dados individual](sql-database-recovery-using-backups.md#deleted-database-restore-using-the-azure-portal) | [Banco de dados individual](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [Instância Gerenciada](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| Restaurar banco de dados do armazenamento de BLOBs do Azure | Banco de dados único-N/A <br/>Instância Gerenciada-N/A  | Banco de dados único-N/A <br/>[Instância Gerenciada](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |

## <a name="how-long-are-backups-kept"></a>Quanto tempo são backups mantidos

Todos os bancos de dados SQL do Azure (bancos de dados de instância única, em pool e gerenciados) têm um período de retenção de backup padrão de **sete** dias. Você pode [alterar o período de retenção de backup de até 35 dias](#how-to-change-the-pitr-backup-retention-period).

Se você excluir um banco de dados, o Banco de Dados SQL manterá os backups da mesma maneira que em um banco de dados online. Por exemplo, se você excluir um banco de dados Básico que tenha um período de retenção de sete dias, um backup de quatro dias será salvo por mais três dias.

Se você precisar manter os backups por mais tempo que o período máximo de retenção, modifique as propriedades de backup para adicionar um ou mais períodos de retenção de longo prazo para o banco de dados. Para obter mais informações, consulte [Retenção de longo prazo](sql-database-long-term-retention.md).

> [!IMPORTANT]
> Se você excluir o SQL Server do Azure que hospeda os bancos de dados SQL, todos os pools elásticos e bancos de dados que pertencem ao servidor também serão excluídos e não poderão ser recuperados. Você não pode restaurar um servidor excluído. Mas, se você tiver configurado a retenção de longo prazo, os backups dos bancos de dados com LTR não serão excluídos e esses bancos de dados poderão ser restaurados.

## <a name="how-often-do-backups-happen"></a>A frequência com que os backups ocorrem

### <a name="backups-for-point-in-time-restore"></a>Backups para a Recuperação Pontual

O Banco de Dados SQL permite o autoatendimento para PITR (Recuperação Pontual) ao criar automaticamente o backup completo, backups diferenciais e backups de log de transações. Os backups completos de banco de dados são criados semanalmente, os backups diferenciais de banco de dados geralmente são criados a cada 12 horas e os backups de log de transações geralmente são criados a cada 5 a 10 minutos, com a frequência baseada no tamanho da computação e na quantidade de atividade do banco de dados. O primeiro backup completo é agendado imediatamente após a criação de um banco de dados. Normalmente ele é concluído em 30 minutos, mas pode levar mais tempo quando o banco de dados tem um tamanho significativo. Por exemplo, o backup inicial pode levar mais tempo para um banco de dados restaurado ou uma cópia do banco de dados. Após o primeiro backup completo, todos os outros backups são agendados automaticamente e gerenciados de forma silenciosa em segundo plano. O tempo exato de todos os backups de banco de dados é determinado pelo serviço do Banco de Dados SQL, pois ele equilibra a carga de trabalho geral do sistema. Você não pode alterar ou desabilitar os trabalhos de backup. 

Os backups de PITR têm redundância geográfica e são protegidos pela [Replicação inter-regional do Armazenamento do Azure](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)

Para obter mais informações, consulte [Restauração pontual](sql-database-recovery-using-backups.md#point-in-time-restore)

### <a name="backups-for-long-term-retention"></a>Backups de retenção de longo prazo

Bancos de dados individuais e em pool oferecem a opção de configurar LTR (retenção de longo prazo) de backups completos por até 10 anos no Armazenamento de Blobs do Azure. Quando a política de LTR está habilitada, os backups completos semanais são copiados automaticamente para um contêiner de armazenamento de RA-GRS diferente. Para atender a diferentes requisitos de conformidade, é possível selecionar diferentes períodos de retenção para backups semanais, mensais e/ou anuais. O consumo do armazenamento depende da frequência selecionada para os backups e dos períodos de retenção. Você pode usar a [Calculadora de preços de LTR](https://azure.microsoft.com/pricing/calculator/?service=sql-database) para estimar o custo do armazenamento de LTR.

Como o PITR, os backups de LTR têm redundância geográfica e são protegidos pela [Replicação inter-regional do Armazenamento do Azure](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage).

Para obter mais informações, confira [Retenção de backup de longo prazo](sql-database-long-term-retention.md).

## <a name="storage-costs"></a>Custos de armazenamento
Sete dias de backups automatizados dos seus bancos de dados são copiados para o armazenamento de blobs RA-GRS Standard por padrão. O armazenamento é usado por backups completos semanais, backups diferenciais diários e backups de log de transações copiados a cada 5 minutos. O tamanho do log de transações depende da taxa de alteração do banco de dados. Uma quantidade de armazenamento mínima igual a 100% do tamanho do banco de dados é fornecida sem encargos extras. O consumo adicional de armazenamento de backup será cobrado em GB/mês.

Para saber mais sobre preços de armazenamento, confira a página de [preços](https://azure.microsoft.com/pricing/details/sql-database/single/). 

## <a name="are-backups-encrypted"></a>Os backups são criptografados

Quando o banco de dados é criptografado com TDE, os backups são criptografados automaticamente em repouso, incluindo os backups de LTR. Quando a TDE está habilitada para um banco de dados SQL do Azure, os backups também são criptografados. Todos os novos bancos de dados SQL do Azure são configurados com TDE habilitada por padrão. Para obter mais informações sobre a TDE, confira [Transparent Data Encryption com o Banco de Dados SQL do Azure](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="how-does-microsoft-ensure-backup-integrity"></a>Como a Microsoft garante a integridade do backup

Em uma base contínua, a equipe de engenharia do banco de dados SQL do Azure testa automaticamente a restauração de backups automatizados de banco de dados de bancos que são colocados em servidores lógicos e pools elásticos (isso não está disponível no Instância Gerenciada). Na restauração pontual, os bancos de dados também recebem verificações de integridade usando DBCC CHECKDB.

Instância gerenciada usa o backup inicial automático `CHECKSUM` com os bancos de dados restaurados usando `RESTORE` o comando nativo ou o serviço de migração de data quando a migração é concluída.

Os problemas encontrados durante a verificação de integridade resultarão em um alerta para a equipe de engenharia. Para obter mais informações sobre a integridade dos dados no Banco de Dados SQL do Azure, confira [Integridade dos dados no Banco de Dados SQL do Azure](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/).

## <a name="how-do-automated-backups-impact-compliance"></a>Como os backups automatizados afetam a conformidade

Quando você migra o banco de dados de uma camada de serviço baseada em DTU, com a retenção de PITR padrão de 35 dias, para uma camada de serviço baseada em vCore, a retenção de PITR é preservada para garantir que a política de recuperação de dados do aplicativo não seja comprometida. Se a retenção padrão não atender aos seus requisitos de conformidade, você poderá alterar o período de retenção de PITR usando o PowerShell ou a API REST. Para obter mais informações, veja [Alterar o período de retenção de backup](#how-to-change-the-pitr-backup-retention-period).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="how-to-change-the-pitr-backup-retention-period"></a>Como alterar o período de retenção de backup de PITR

Você pode alterar o período de retenção de backup PITR padrão usando o portal do Azure, o PowerShell ou a API REST. Os valores com suporte são: 7, 14, 21, 28 ou 35 dias. Os exemplos a seguir ilustram como alterar a retenção de PITR para 28 dias.

> [!WARNING]
> Se você reduzir o período de retenção atual, todos os backups existentes mais antigos que o novo período de retenção não estarão mais disponíveis. Se você aumentar o período de retenção atual, o Banco de Dados SQL manterá os backups existentes até que o período de retenção mais longo seja atingido.

> [!NOTE]
> Essas APIs afetarão somente o período de retenção de PITR. Se você tiver configurado a LTR para o banco de dados, ela não será afetada. Para obter mais informações sobre como alterar o(s) período(s) de retenção de LTR, confira [Retenção de longo prazo](sql-database-long-term-retention.md).

### <a name="change-pitr-backup-retention-period-using-the-azure-portal"></a>Alterar o período de retenção de backup de PITR usando o portal do Azure

Para alterar o período de retenção de backup PITR usando o portal do Azure, navegue até o objeto de servidor cujo período de retenção você deseja alterar no portal e, em seguida, selecione a opção apropriada com base no objeto de servidor que você está modificando.

#### <a name="change-pitr-for-a-sql-database-server"></a>Alteração de PITR para um servidor do Banco de Dados SQL

![Alterar PITR no portal do Azure](./media/sql-database-automated-backup/configure-backup-retention-sqldb.png)

#### <a name="change-pitr-for-a-managed-instance"></a>Alterar PITR para uma Instância Gerenciada

![Alterar PITR no portal do Azure](./media/sql-database-automated-backup/configure-backup-retention-sqlmi.png)

### <a name="change-pitr-backup-retention-period-using-powershell"></a>Alterar o período de retenção de backup de PITR usando o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell ainda tem suporte do banco de dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. Para esses cmdlets, consulte [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo AZ e nos módulos AzureRm são substancialmente idênticos.

```powershell
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

### <a name="change-pitr-retention-period-using-rest-api"></a>Alterar o período de retenção de PITR usando a API REST

#### <a name="sample-request"></a>Solicitação de Exemplo

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2017-10-01-preview
```

#### <a name="request-body"></a>Corpo da solicitação

```json
{
  "properties":{
    "retentionDays":28
  }
}
```

#### <a name="sample-response"></a>Exemplo de Resposta

Código de status: 200

```json
{
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/providers/Microsoft.Sql/resourceGroups/resourceGroup/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default",
  "name": "default",
  "type": "Microsoft.Sql/resourceGroups/servers/databases/backupShortTermRetentionPolicies",
  "properties": {
    "retentionDays": 28
  }
}
```

Para obter mais informações, confira [API REST de retenção de backup](https://docs.microsoft.com/rest/api/sql/backupshorttermretentionpolicies).

## <a name="next-steps"></a>Próximas etapas

- Os backups de banco de dados são uma parte essencial de qualquer estratégia de recuperação de desastre e continuidade dos negócios, porque eles protegem seus dados contra exclusão ou corrupção acidentais. Para saber mais sobre as outras soluções de continuidade dos negócios do Banco de Dados SQL do Azure, consulte [Visão geral da continuidade dos negócios](sql-database-business-continuity.md).
- Para restaurar para um determinado ponto no tempo usando o Portal do Azure, consulte [Restaurar um banco de dados para um ponto no tempo usando o Portal do Azure](sql-database-recovery-using-backups.md).
- Para restaurar para um determinado ponto no tempo usando o PowerShell, consulte [Restaurar um banco de dados para um ponto no tempo usando o PowerShell](scripts/sql-database-restore-database-powershell.md).
- Para configurar, gerenciar e restaurar a retenção de longo prazo de backups automatizados no Armazenamento de Blobs do Azure usando o portal do Azure, consulte [Gerenciar retenção de backup de longo prazo usando o portal do Azure](sql-database-long-term-backup-retention-configure.md).
- Para configurar, gerenciar e restaurar de retenção de longo prazo de backups automatizados no armazenamento de BLOBs do Azure usando o PowerShell, consulte [gerenciar retenção de backup de longo prazo usando o PowerShell](sql-database-long-term-backup-retention-configure.md).
