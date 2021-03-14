---
title: Backups automáticos com redundância geográfica
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: O banco de dados SQL do Azure e o Azure SQL Instância Gerenciada criam automaticamente um backup de banco de dados local a cada poucos minutos e usam o armazenamento com redundância geográfica do acesso de leitura
services: sql-database
ms.service: sql-db-mi
ms.subservice: backup-restore
ms.custom: references_regions
ms.topic: conceptual
author: shkale-msft
ms.author: shkale
ms.reviewer: mathoma, stevestein, danil
ms.date: 03/10/2021
ms.openlocfilehash: 5879c9107a0ab5a2ef150d119e8b5ac8e16ac01d
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102609916"
---
# <a name="automated-backups---azure-sql-database--sql-managed-instance"></a>Backups automatizados – banco de dados SQL do Azure & SQL Instância Gerenciada

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-database-backup"></a>O que é um backup de banco de dados?

Os backups de banco de dados são uma parte essencial de qualquer estratégia de recuperação de desastre e continuidade dos negócios, pois eles protegem seus dados contra corrupção ou exclusão. Esses backups permitem a restauração do banco de dados para um ponto no tempo dentro do período de retenção configurado. Se suas regras de proteção de dados exigirem que os backups estejam disponíveis por um tempo estendido (até 10 anos), você poderá configurar a [retenção de longo prazo](long-term-retention-overview.md) para bancos de dados únicos e em pool.

### <a name="backup-frequency"></a>Frequência de backup

O banco de dados SQL e o SQL Instância Gerenciada usam a tecnologia SQL Server para criar [backups completos](/sql/relational-databases/backup-restore/full-database-backups-sql-server) toda semana, [backups diferenciais](/sql/relational-databases/backup-restore/differential-backups-sql-server) a cada 12-24 horas e [backups de log de transações](/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) a cada 5 a 10 minutos. A frequência dos backups de logs de transações é baseada no tamanho da computação e na quantidade de atividade do banco de dados.

Quando você restaura um banco de dados, o serviço determina quais backups completos, diferenciais e de log de transações precisam ser restaurados.

### <a name="backup-storage-redundancy"></a>Redundância do armazenamento de backup

Por padrão, o banco de dados SQL e o SQL Instância Gerenciada armazenam em [blobs de armazenamento](../../storage/common/storage-redundancy.md) com redundância geográfica que são replicados para uma [região emparelhada](../../best-practices-availability-paired-regions.md). Isso ajuda a proteger contra interrupções que afetam o armazenamento de backup na região primária e permite que você restaure o servidor para uma região diferente em caso de desastre. 

A opção de configurar a redundância de armazenamento de backup fornece a flexibilidade para escolher entre os blobs de armazenamento com redundância local, com redundância de zona ou com redundância geográfica para um SQL Instância Gerenciada ou um banco de dados SQL. Para garantir que seus dados permaneçam na mesma região onde sua instância gerenciada ou banco de dados SQL está implantado, você pode alterar a redundância de armazenamento de backup com redundância geográfica padrão e configurar blobs de armazenamento com redundância de zona ou localmente redundantes para backups. Os mecanismos de redundância de armazenamento armazenam várias cópias de seus dados para que eles sejam protegidos contra eventos planejados e não planejados, incluindo falhas transitórias de hardware, interrupções de rede ou energia ou desastres maciços naturais. A redundância de armazenamento de backup configurada é aplicada a configurações de retenção de backup de curto prazo usadas para PITR (restauração pontual) e backups de retenção de longo prazo usados para EPD (backups de longo prazo). 

Para um banco de dados SQL, a redundância de armazenamento de backup pode ser configurada no momento da criação do banco de dados ou pode ser atualizada para um banco de dados existente; as alterações feitas em um banco de dados existente se aplicam somente a backups futuros. Depois que a redundância de armazenamento de backup de um banco de dados existente é atualizada, pode levar até 48 horas para que as alterações sejam aplicadas. Observe que, a restauração geográfica é desabilitada assim que um banco de dados é atualizado para usar o armazenamento local ou com redundância de zona. 


> [!IMPORTANT]
> Configure a redundância de armazenamento de backup durante o processo de criação de instância gerenciada assim que o recurso for provisionado, não será mais possível alterar a redundância de armazenamento. 

> [!IMPORTANT]
> Atualmente, o armazenamento com redundância de zona só está disponível em [determinadas regiões](../../storage/common/storage-redundancy.md#zone-redundant-storage). 

> [!NOTE]
> A redundância de armazenamento de backup configurável para o banco de dados SQL do Azure está disponível atualmente na visualização pública no sul do Brasil e geralmente disponível somente na região do sudeste asiático do Azure. Este recurso ainda não está disponível para a camada de hiperescala. 

### <a name="backup-usage"></a>Uso do backup

Use esses backups para:

- **Restauração pontual do banco de dados existente**  -  [Restaure um banco de dados existente para um ponto no tempo no passado](recovery-using-backups.md#point-in-time-restore) dentro do período de retenção usando portal do Azure, Azure PowerShell, CLI do Azure ou API REST. Para o banco de dados SQL, essa operação cria um novo banco de dados no mesmo servidor que o banco de dados original, mas usa um nome diferente para evitar a substituição do banco de dados original. Após a conclusão da restauração, você pode excluir o banco de dados original. Como alternativa, você pode [renomear](/sql/relational-databases/databases/rename-a-database) o banco de dados original e depois renomear o banco de dados restaurado como o nome do banco de dados original. Da mesma forma, para o SQL Instância Gerenciada, essa operação cria uma cópia do banco de dados na mesma instância gerenciada ou diferente na mesma assinatura e na mesma região.
- **Restauração pontual do banco de dados excluído**  -  [Restaure um banco de dados excluído no momento da exclusão](recovery-using-backups.md#deleted-database-restore) ou em qualquer ponto no tempo dentro do período de retenção. O banco de dados excluído só pode ser restaurado no mesmo servidor ou instância gerenciada em que o banco de dados original foi criado. Ao excluir um banco de dados, o serviço usa um backup de log de transações final antes da exclusão, para evitar qualquer perda de dados.
- **Restauração geográfica**  -  [Restaure um banco de dados para outra região geográfica](recovery-using-backups.md#geo-restore). A restauração geográfica permite que você se recupere de um desastre geográfico quando não é possível acessar seu banco de dados ou backups na região primária. Ele cria um novo banco de dados em qualquer servidor ou instância gerenciada existente, em qualquer região do Azure.
   > [!IMPORTANT]
   > A restauração geográfica está disponível somente para bancos de dados SQL ou instâncias gerenciadas configuradas com armazenamento de backup com redundância geográfica.
- **Restaurar do backup**  -  de longo prazo [Restaure um banco de dados de um backup de longo prazo específico](long-term-retention-overview.md) de um banco de dados individual ou de um banco de dados em pool, se o banco de dados tiver sido configurado com uma EPD (política de retenção de longo prazo). A LTR permite que você restaure uma versão antiga do banco de dados usando o [portal do Azure](long-term-backup-retention-configure.md#using-the-azure-portal) ou o [Azure PowerShell](long-term-backup-retention-configure.md#using-powershell) para atender a uma solicitação de conformidade ou para executar uma versão antiga do aplicativo. Para obter mais informações, consulte [Retenção de longo prazo](long-term-retention-overview.md).

Para executar uma restauração, consulte [Restaurar um banco de dados de backups](recovery-using-backups.md).

> [!NOTE]
> No armazenamento do Azure, o termo *replicação* refere-se à cópia de blobs de um local para outro. No SQL, a *replicação de banco de dados* refere-se a várias tecnologias usadas para manter vários bancos de dados secundários sincronizados com um banco de dados primário.

Você pode tentar a configuração de backup e restaurar as operações usando os seguintes exemplos:

| Operação | Portal do Azure | Azure PowerShell |
|---|---|---|
| **Alterar retenção de backup** | [Banco de Dados SQL](automated-backups-overview.md?tabs=single-database#change-the-pitr-backup-retention-period-by-using-the-azure-portal) <br/> [Instância Gerenciada de SQL](automated-backups-overview.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) | [Banco de Dados SQL](automated-backups-overview.md#change-the-pitr-backup-retention-period-by-using-powershell) <br/>[Instância Gerenciada de SQL](/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| **Alterar retenção de backup de longo prazo** | [Banco de Dados SQL](long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>SQL Instância Gerenciada-N/A  | [Banco de Dados SQL](long-term-backup-retention-configure.md)<br/>[Instância Gerenciada de SQL](../managed-instance/long-term-backup-retention-configure.md)  |
| **Restaurar um banco de dados a partir de um momento determinado** | [Banco de Dados SQL](recovery-using-backups.md#point-in-time-restore)<br>[Instância Gerenciada de SQL](../managed-instance/point-in-time-restore.md) | [Banco de Dados SQL](/powershell/module/az.sql/restore-azsqldatabase) <br/> [Instância Gerenciada de SQL](/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| **Restaurar um banco de dados excluído** | [Banco de Dados SQL](recovery-using-backups.md)<br>[Instância Gerenciada de SQL](../managed-instance/point-in-time-restore.md#restore-a-deleted-database) | [Banco de Dados SQL](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [Instância Gerenciada de SQL](/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| **Restaurar um banco de dados do armazenamento de Blobs do Azure** | Banco de dados SQL-N/A <br/>SQL Instância Gerenciada-N/A  | Banco de dados SQL-N/A <br/>[Instância Gerenciada de SQL](../managed-instance/restore-sample-database-quickstart.md) |

## <a name="backup-scheduling"></a>Agendamento de backup

O primeiro backup completo é agendado imediatamente após a criação ou restauração de um novo banco de dados. Geralmente, esse backup é concluído em 30 minutos, mas pode levar mais tempo quando o banco de dados é grande. Por exemplo, o backup inicial pode levar mais tempo em um banco de dados restaurado ou uma cópia de banco de dados, que normalmente seria maior do que um novo banco de dados. Após o primeiro backup completo, todos os backups adicionais são agendados e gerenciados automaticamente. O tempo exato de todos os backups de banco de dados é determinado pelo banco de dados SQL ou pelo serviço de Instância Gerenciada do SQL, pois ele equilibra a carga de trabalho geral do sistema. Você não pode alterar o agendamento de trabalhos de backup ou desabilitá-los.

> [!IMPORTANT]
> Para um banco de dados novo, restaurado ou copiado, o recurso de restauração pontual fica disponível a partir do momento em que o backup de log de transações inicial que segue o backup completo inicial é criado.

## <a name="backup-storage-consumption"></a>Consumo de armazenamento de backup

Com a tecnologia de backup e restauração SQL Server, restaurar um banco de dados para um ponto no tempo requer uma cadeia de backup ininterrupto que consiste em um backup completo, opcionalmente um backup diferencial e um ou mais backups de log de transações. O banco de dados SQL e o agendamento do SQL Instância Gerenciada backup incluem um backup completo toda semana. Portanto, para habilitar o PITR em todo o período de retenção, o sistema deve armazenar backups adicionais completos, diferenciais e de log de transações por até uma semana maior do que o período de retenção configurado. 

Em outras palavras, para qualquer ponto no tempo durante o período de retenção, deve haver um backup completo que seja mais antigo do que o tempo mais antigo do período de retenção, bem como uma cadeia ininterrupta de backups de log de transações e diferenciais desse backup completo até o próximo backup completo.

> [!NOTE]
> Para habilitar o PITR, backups adicionais são armazenados por até uma semana maior que o período de retenção configurado. O armazenamento de backup é cobrado com a mesma taxa para todos os backups. 

Os backups que não são mais necessários para fornecer a funcionalidade PITR são excluídos automaticamente. Como backups diferenciais e backups de log exigem que um backup completo anterior seja restaurável, todos os três tipos de backup são limpos juntos em conjuntos semanais.

Para todos os bancos de dados, incluindo bancos de dados [criptografados TDE](transparent-data-encryption-tde-overview.md) , os backups são compactados para reduzir a compactação e os custos de armazenamento de backup. A taxa de compactação de backup média é de 3-4 vezes, mas pode ser significativamente menor ou maior, dependendo da natureza dos dados e se a compactação de dados é usada no banco de dado.

O banco de dados SQL e o SQL Instância Gerenciada calculam seu armazenamento de backup total usado como um valor cumulativo. A cada hora, esse valor é relatado para o pipeline de cobrança do Azure, que é responsável por agregar esse uso por hora para calcular seu consumo no final de cada mês. Depois que o banco de dados é excluído, o consumo diminui à medida que os backups expiram e são excluídos. Depois que todos os backups forem excluídos e o PITR não for mais possível, a cobrança será interrompida.
   
> [!IMPORTANT]
> Os backups de um banco de dados são mantidos para habilitar o PITR mesmo que o banco de dados tenha sido excluído. Embora a exclusão e recriação de um banco de dados possa economizar custos de armazenamento e computação, isso pode aumentar os custos de armazenamento de backup, pois o serviço retém backups para cada banco de dados excluído, sempre que ele é excluído. 

### <a name="monitor-consumption"></a>Monitorar o consumo

Para bancos de dados vCore, o armazenamento consumido por cada tipo de backup (completo, diferencial e log) é relatado na folha de monitoramento do banco de dados como uma métrica separada. O diagrama a seguir mostra como monitorar o consumo de armazenamento de backup para um banco de dados individual. Este recurso não está disponível no momento para instâncias gerenciadas.

![Monitorar o consumo de backup de banco de dados no portal do Azure](./media/automated-backups-overview/backup-metrics.png)

### <a name="fine-tune-backup-storage-consumption"></a>Ajuste fino do consumo de armazenamento de backup

O consumo de armazenamento de backup até o tamanho máximo de dados de um banco de dado não é cobrado. O consumo de armazenamento de backup excessivo dependerá da carga de trabalho e do tamanho máximo dos bancos de dados individuais. Considere algumas das técnicas de ajuste a seguir para reduzir o consumo de armazenamento de backup:

- Reduza o [período de retenção de backup](#change-the-pitr-backup-retention-period-by-using-the-azure-portal) ao mínimo possível para suas necessidades.
- Evite fazer grandes operações de gravação, como recompilações de índice, com mais frequência do que o necessário.
- Para operações de carregamento de dados grandes, considere usar [índices columnstore clusterizados](/sql/relational-databases/indexes/columnstore-indexes-overview) e seguir as [práticas recomendadas](/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance)relacionadas e/ou reduzir o número de índices não clusterizados.
- Na camada de serviço Uso Geral, o armazenamento de dados provisionado é mais barato do que o preço do armazenamento de backup. Se você estiver sempre com alto excesso de custos de armazenamento de backup, considere aumentar o armazenamento de dados para salvar no armazenamento de backup.
- Use TempDB em vez de tabelas permanentes na lógica do aplicativo para armazenar resultados temporários e/ou dados transitórios.
- Usar o armazenamento de backup com redundância local sempre que possível (por exemplo, ambientes de desenvolvimento/teste)

## <a name="backup-retention"></a>Retenção de backup

Para todos os bancos de dados novos, restaurados e copiados, o Azure SQL Database e o Azure SQL Instância Gerenciada retêm backups suficientes para permitir PITR nos últimos 7 dias por padrão. Com exceção dos bancos de dados de hiperescala e de camada básica, você pode [alterar o período de retenção de backup](#change-the-pitr-backup-retention-period) por cada banco de dados ativo no intervalo de 1-35 dias. Conforme descrito em [consumo de armazenamento de backup](#backup-storage-consumption), os backups armazenados para habilitar PITR podem ser mais antigos do que o período de retenção. Somente para o Azure SQL Instância Gerenciada, é possível definir a taxa de retenção de backup PITR quando um banco de dados tiver sido excluído no intervalo de 0-35 dias. 

Se você excluir um banco de dados, o sistema manterá os backups da mesma forma que faria com um banco de dados online com seu período de retenção específico. Não é possível alterar o período de retenção de backup de um banco de dados excluído.

> [!IMPORTANT]
> Se você excluir um servidor ou uma instância gerenciada, todos os bancos de dados nesse servidor ou instância gerenciada também serão excluídos e não poderão ser recuperados. Não é possível restaurar um servidor excluído ou uma instância gerenciada. Mas se você tiver configurado a EPD (retenção de longo prazo) para um banco de dados ou instância gerenciada, os backups de retenção de longo prazo não serão excluídos e poderão ser usados para restaurar bancos de dados em um servidor ou instância gerenciada diferente na mesma assinatura, até um ponto no tempo em que um backup de retenção de longo prazo foi realizado.

A retenção de backup para fins de PITR nos últimos 1-35 dias é, às vezes, chamada de retenção de backup de curto prazo. Se você precisar manter backups por mais tempo do que o período máximo de retenção de curto prazo de 35 dias, poderá habilitar [a retenção de longo prazo](long-term-retention-overview.md).

### <a name="long-term-retention"></a>Retenção de longo prazo

Para o banco de dados SQL e o SQL Instância Gerenciada, você pode configurar a EPD (retenção de longo prazo) de backup completo por até 10 anos no armazenamento de BLOBs do Azure. Depois que a política EPD é configurada, os backups completos são copiados automaticamente para um contêiner de armazenamento diferente semanalmente. Para atender a vários requisitos de conformidade, você pode selecionar períodos de retenção diferentes para backups completos semanais, mensais e/ou anuais. O consumo de armazenamento depende da frequência selecionada e dos períodos de retenção de backups EPD. Você pode usar a [Calculadora de preços de LTR](https://azure.microsoft.com/pricing/calculator/?service=sql-database) para estimar o custo do armazenamento de LTR.

> [!IMPORTANT]
> Atualizar a redundância de armazenamento de backup para um banco de dados SQL do Azure existente só se aplica aos backups futuros feitos para o banco de dados. Todos os backups EPD existentes para o banco de dados continuarão a residir no blob de armazenamento existente e os novos backups serão armazenados no tipo de blob de armazenamento solicitado. 

Para obter mais informações sobre LTR, confira [Retenção de backup de longo prazo](long-term-retention-overview.md).

## <a name="backup-storage-costs"></a>Custos de armazenamento de backup

O preço do armazenamento de backup varia e depende de seu modelo de compra (DTU ou vCore), da opção de redundância de armazenamento de backup escolhida e também de sua região. O armazenamento de backup é cobrado por GB/mês consumido, para preços, consulte página de [preços do banco de dados SQL do Azure](https://azure.microsoft.com/pricing/details/sql-database/single/) e página de [preços do Azure SQL instância gerenciada](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/) .

> [!NOTE]
> A fatura do Azure mostrará apenas o armazenamento de backup em excesso consumido, não o consumo de armazenamento de backup inteiro. Por exemplo, em um cenário hipotético, se você tiver provisionado 4 TB de armazenamento de dados, receberá 4 TB de espaço livre de armazenamento de backup. Caso você tenha usado o total de 5,8 TB de espaço de armazenamento de backup, a fatura do Azure mostrará apenas 1,8 TB, pois somente o armazenamento de backup excessivo usado será cobrado.

### <a name="dtu-model"></a>Modelo de CPU

No modelo de DTU, não há nenhum custo adicional para o armazenamento de backup para bancos de dados e pools elásticos. O preço do armazenamento de backup é parte do preço do banco de dados ou do pool.

### <a name="vcore-model"></a>Modelo vCore

Para bancos de dados individuais no banco de dados SQL, um valor de armazenamento de backup igual a 100 por cento do tamanho máximo de armazenamento de dados para o banco de dado é fornecido sem custo adicional. Para pools elásticos e instâncias gerenciadas, um valor de armazenamento de backup igual a 100% do armazenamento de dados máximo para o pool ou o tamanho máximo de armazenamento de instância, respectivamente, é fornecido sem nenhum custo adicional. 

Para bancos de dados individuais, esta equação é usada para calcular o uso total de armazenamento de backup cobrável:

`Total billable backup storage size = (size of full backups + size of differential backups + size of log backups) – maximum data storage`

Para bancos de dados em pool, o tamanho total do armazenamento de backup cobrável é agregado no nível do pool e é calculado da seguinte maneira:

`Total billable backup storage size = (total size of all full backups + total size of all differential backups + total size of all log backups) - maximum pool data storage`

Para instâncias gerenciadas, o tamanho total de armazenamento de backup cobrável é agregado no nível de instância e é calculado da seguinte maneira:

`Total billable backup storage size = (total size of full backups + total size of differential backups + total size of log backups) – maximum instance data storage`

O armazenamento de backup cobrável total, se houver, será cobrado em GB/mês, de acordo com a taxa de redundância de armazenamento de backup usada. Esse consumo de armazenamento de backup dependerá da carga de trabalho e do tamanho de bancos de dados individuais, pools elásticos e instâncias gerenciadas. Bancos de dados muito modificados têm backups diferenciais e de log maiores, pois o tamanho desses backups é proporcional à quantidade de alterações de dado. Portanto, esses bancos de dados terão encargos de backup maiores.

O banco de dados SQL e o SQL Instância Gerenciada calcula seu armazenamento de backup cobrável total como um valor cumulativo em todos os arquivos de backup. A cada hora, esse valor é relatado para o pipeline de cobrança do Azure, que agrega esse uso por hora para obter o consumo de armazenamento de backup no final de cada mês. Se um banco de dados for excluído, o consumo de armazenamento de backup diminuirá gradualmente conforme os backups mais antigos expirarem e serão excluídos. Como backups diferenciais e backups de log exigem que um backup completo anterior seja restaurável, todos os três tipos de backup são limpos juntos em conjuntos semanais. Depois que todos os backups forem excluídos, a cobrança será interrompida. 

Como um exemplo simplificado, suponha que um banco de dados tenha acumulado 744 GB de armazenamento de backup e que esse valor permaneça constante durante um mês inteiro, pois o banco de dados está completamente ocioso. Para converter esse consumo de armazenamento cumulativo para o uso por hora, divida-o por 744 (31 dias por mês * 24 horas por dia). O banco de dados SQL reportará ao pipeline de cobrança do Azure que o banco de dados consumiu 1 GB de backup de PITR a cada hora, a uma taxa constante. A cobrança do Azure agregará esse consumo e mostrará um uso de 744 GB para o mês inteiro. O custo será baseado na taxa de quantidade/GB/mês em sua região.

Agora, um exemplo mais complexo. Suponha que o mesmo banco de dados ocioso tenha sua retenção aumentada de 7 dias para 14 dias no meio do mês. Esse aumento resulta na duplicação total do armazenamento de backup para 1.488 GB. O banco de dados SQL relataria 1 GB de uso para as horas 1 a 372 (a primeira metade do mês). Ele relataria o uso como 2 GB para as horas de 373 a 744 (a segunda metade do mês). Esse uso seria agregado a uma fatura final de 1.116 GB/mês.

Os cenários de cobrança de backup reais são mais complexos. Como a taxa de alterações no banco de dados depende da carga de trabalho e é variável ao longo do tempo, o tamanho de cada backup diferencial e de log varia também, fazendo com que o consumo de armazenamento de backup por hora seja flutuante de forma adequada. Além disso, cada backup diferencial contém todas as alterações feitas no banco de dados desde o último backup completo, portanto, o tamanho total de todos os backups diferenciais aumenta gradualmente ao longo de uma semana e, em seguida, é mais nítido quando um conjunto mais antigo de backups completos, diferenciais e de log é desatualizado. Por exemplo, se uma atividade de gravação pesada, como a recompilação de índice, tiver sido executada logo após a conclusão de um backup completo, as modificações feitas pela recompilação de índice serão incluídas nos backups de log de transações feitos durante a recompilação, no próximo backup diferencial e em todos os backups diferenciais feitos até o próximo backup completo ocorrer. Para o último cenário em bancos de dados maiores, uma otimização no serviço cria um backup completo em vez de um backup diferencial se um backup diferencial fosse excessivamente grande, caso contrário. Isso reduz o tamanho de todos os backups diferenciais até o backup completo a seguir.

Você pode monitorar o consumo de armazenamento de backup total para cada tipo de backup (completo, diferencial, log de transações) ao longo do tempo, conforme descrito em [monitorar consumo](#monitor-consumption).

### <a name="backup-storage-redundancy"></a>Redundância do armazenamento de backup

A redundância de armazenamento de backup afeta os custos de backup da seguinte maneira:
- preço com redundância local = x
- preço com redundância de zona = 1,25 x
- preço com redundância geográfica = 2x

Para obter mais detalhes sobre preços de armazenamento de backup, visite a página de [preços do banco de dados SQL do Azure](https://azure.microsoft.com/pricing/details/sql-database/single/) e a [página de preços do SQL instância gerenciada](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/)

> [!IMPORTANT]
> A redundância de armazenamento de backup configurável para a instância gerenciada do SQL está disponível em todas as regiões do Azure e atualmente disponível somente na região do sudeste asiático do Azure para o banco de dados SQL. Por Instância Gerenciada ele só pode ser especificado durante o processo de criação de instância gerenciada. Depois que o recurso for provisionado, você não poderá alterar a opção de redundância de armazenamento de backup.

### <a name="monitor-costs"></a>Monitorar custos

Para entender os custos de armazenamento de backup, vá para **Gerenciamento de custos + Cobrança** no portal do Azure, selecione **Gerenciamento de Custos** e, em seguida,  **Análise de custo**. Selecione a assinatura desejada como **Escopo** e, em seguida, filtre o período de tempo e o serviço nos quais você está interessado.

Adicione um filtro para o **Nome do serviço** e, em seguida, selecione **banco de dados sql** na lista suspensa. Use o filtro **subcategoria de medidor** para escolher o contador de cobrança para seu serviço. Para um único banco de dados ou um pool de banco de dados elástico, selecione **pool único/elástico PITR armazenamento de backup**. Para uma instância gerenciada, selecione **PITR armazenamento de backup de mi**. As subcategorias **Armazenamento** e **Computação** também podem lhe interessar, mas elas não estão associadas aos custos de armazenamento de backup.

![Análise de custo de armazenamento de backup](./media/automated-backups-overview/check-backup-storage-cost-sql-mi.png)

  >[!NOTE]
  > Os medidores só são visíveis para os contadores que estão em uso no momento. Se um contador não estiver disponível, é provável que a categoria não esteja sendo usada no momento. Por exemplo, os contadores de instância gerenciada não estarão presentes para os clientes que não têm uma instância gerenciada implantada. Da mesma forma, os contadores de armazenamento não estarão visíveis para os recursos que não estão consumindo armazenamento. 

## <a name="encrypted-backups"></a>Backups criptografados

Quando o banco de dados é criptografado com TDE, os backups são criptografados automaticamente em repouso, incluindo os backups de LTR. Todos os novos bancos de dados no Azure SQL são configurados com TDE habilitado por padrão. Para obter mais informações sobre o TDE, consulte  [Transparent Data Encryption com o banco de dados sql & sql instância gerenciada](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="backup-integrity"></a>Integridade do backup

Em uma base contínua, a equipe de engenharia do SQL do Azure testa automaticamente a restauração de backups de banco de dados automatizados. (Este teste não está disponível no momento no SQL Instância Gerenciada.) Na restauração pontual, os bancos de dados também recebem verificações de integridade do DBCC CHECKDB.

Os problemas encontrados durante a verificação de integridade resultarão em um alerta para a equipe de engenharia. Para obter mais informações, consulte [integridade de dados no banco de dados SQL](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/).

Todos os backups de banco de dados são obtidos com a opção CHECKSUM para fornecer integridade de backup adicional.

## <a name="compliance"></a>Conformidade

Quando você migra seu banco de dados de uma camada de serviço baseada em DTU para uma camada de serviço baseada em vCore, a retenção de PITR é preservada para garantir que a política de recuperação de dados do aplicativo não seja comprometida. Se a retenção padrão não atender aos seus requisitos de conformidade, você poderá alterar o período de retenção do PITR. Para obter mais informações, consulte [Alterar o período de retenção de backup de PITR](#change-the-pitr-backup-retention-period).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="change-the-pitr-backup-retention-period"></a>Alterar o período de retenção de backup de PITR

Você pode alterar o período de retenção de backup de PITR padrão usando o portal do Azure, o PowerShell ou a API REST. Os exemplos a seguir ilustram como alterar a retenção de PITR para 28 dias.

> [!WARNING]
> Se você reduzir o período de retenção atual, perderá a capacidade de restaurar para pontos no tempo mais antigos do que o novo período de retenção. Os backups que não são mais necessários para fornecer PITR no novo período de retenção são excluídos. Se você aumentar o período de retenção atual, não obterá imediatamente a capacidade de restaurar para os pontos mais antigos no novo período de retenção. Você pode obter essa capacidade ao longo do tempo, à medida que o sistema começa a reter backups por mais tempo.

> [!NOTE]
> Essas APIs afetarão somente o período de retenção de PITR. Se você tiver configurado a LTR para o banco de dados, ela não será afetada. Para mais informações sobre como alterar os períodos de retenção de LTR, consulte [Retenção de longo prazo](long-term-retention-overview.md).

### <a name="change-the-pitr-backup-retention-period-by-using-the-azure-portal"></a>Alterar o período de retenção de backup de PITR usando o portal do Azure

Para alterar o período de retenção de backup PITR para bancos de dados ativos usando o portal do Azure, vá para o servidor ou instância gerenciada com os bancos de dados cujo período de retenção você deseja alterar. Selecione **backups** no painel esquerdo e, em seguida, selecione a guia **políticas de retenção** . Selecione os bancos de dados para os quais você deseja alterar a retenção de backup PITR. Em seguida, selecione **Configurar retenção** na barra de ação.



#### <a name="sql-database"></a>[Banco de Dados SQL](#tab/single-database)

![Alterar a retenção de PITR, nível de servidor](./media/automated-backups-overview/configure-backup-retention-sqldb.png)

#### <a name="sql-managed-instance"></a>[Instância Gerenciada de SQL](#tab/managed-instance)

![Alterar a retenção de PITR, instância gerenciada](./media/automated-backups-overview/configure-backup-retention-sqlmi.png)

---

### <a name="change-the-pitr-backup-retention-period-by-using-powershell"></a>Alterar o período de retenção de backup de PITR usando o PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo AzureRM do PowerShell ainda tem suporte do banco de dados SQL e do SQL Instância Gerenciada, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. Para obter mais informações, consulte [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az são substancialmente idênticos aos dos módulos AzureRm.

#### <a name="sql-database"></a>[Banco de Dados SQL](#tab/single-database)

Para alterar a retenção de backup do PITR para bancos de dados SQL do Azure ativos, use o seguinte exemplo do PowerShell.

```powershell
# SET new PITR backup retention period on an active individual database
# Valid backup retention must be between 1 and 35 days
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

#### <a name="sql-managed-instance"></a>[Instância Gerenciada de SQL](#tab/managed-instance)

Para alterar a retenção de backup do PITR de um banco de dados ativo do SQL Instância Gerenciada **individual** , use o seguinte exemplo do PowerShell.

```powershell
# SET new PITR backup retention period on an active individual database
# Valid backup retention must be between 1 and 35 days
Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -InstanceName testserver -DatabaseName testDatabase -RetentionDays 1
```

Para alterar a retenção de backup do PITR para **todos os** bancos de dados do SQL instância gerenciada ativos, use o seguinte exemplo do PowerShell.

```powershell
# SET new PITR backup retention period for ALL active databases
# Valid backup retention must be between 1 and 35 days
Get-AzSqlInstanceDatabase -ResourceGroupName resourceGroup -InstanceName testserver | Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -RetentionDays 1
```

Para alterar a retenção de backup PITR de um banco de dados SQL Instância Gerenciada **excluído individual** , use o seguinte exemplo do PowerShell.
 
```powershell
# SET new PITR backup retention on an individual deleted database
# Valid backup retention must be between 0 (no retention) and 35 days. Valid retention rate can only be lower than the period of the retention period when database was active, or remaining backup days of a deleted database.
Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName resourceGroup -InstanceName testserver -DatabaseName testDatabase | Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -RetentionDays 0
```

Para alterar a retenção de backup PITR de **todos os** bancos de dados do SQL instância gerenciada excluídos, use o seguinte exemplo do PowerShell.

```powershell
# SET new PITR backup retention for ALL deleted databases
# Valid backup retention must be between 0 (no retention) and 35 days. Valid retention rate can only be lower than the period of the retention period when database was active, or remaining backup days of a deleted database
Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName resourceGroup -InstanceName testserver | Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -RetentionDays 0
```

Zero (0) dias de retenção indicaria que o backup é imediatamente excluído e não é mais mantido para um banco de dados excluído.
Depois que a retenção de backup do PITR foi reduzida para um banco de dados excluído, ele não pode mais ser aumentado.

---

### <a name="change-the-pitr-backup-retention-period-by-using-the-rest-api"></a>Alterar o período de retenção de backup de PITR usando a API REST

#### <a name="sample-request"></a>Solicitação de exemplo

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

#### <a name="sample-response"></a>Resposta de exemplo

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

Para obter mais informações, confira [API REST de retenção de backup](/rest/api/sql/backupshorttermretentionpolicies).

#### <a name="sample-request"></a>Solicitação de exemplo

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

#### <a name="sample-response"></a>Resposta de exemplo

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

Para obter mais informações, confira [API REST de retenção de backup](/rest/api/sql/backupshorttermretentionpolicies).

## <a name="configure-backup-storage-redundancy"></a>Configurar a redundância de armazenamento de backup

> [!NOTE]
> A redundância de armazenamento configurável para backups para SQL Instância Gerenciada só pode ser especificada durante o processo de criação de instância gerenciada. Depois que o recurso for provisionado, você não poderá alterar a opção de redundância de armazenamento de backup. Para o banco de dados SQL, a visualização pública desse recurso está disponível atualmente no sul do Brasil e está disponível na região sudeste asiático do Azure. 

Uma redundância de armazenamento de backup de uma instância gerenciada pode ser definida somente durante a criação da instância. Para um banco de dados SQL, ele pode ser definido ao criar o banco de dados ou pode ser atualizado para um banco de dados existente. O valor padrão é armazenamento com redundância geográfica. Para obter as diferenças de preço entre redundância local, com redundância de zona e armazenamento de backup com redundância geográfica, visite a [página de preços de instância gerenciada](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/).

### <a name="configure-backup-storage-redundancy-by-using-the-azure-portal"></a>Configurar a redundância de armazenamento de backup usando o portal do Azure

#### <a name="sql-database"></a>[Banco de Dados SQL](#tab/single-database)

No portal do Azure, você pode configurar a redundância de armazenamento de backup na folha **criar banco de dados SQL** . A opção está disponível na seção redundância de armazenamento de backup. 
![Abrir folha criar banco de dados SQL](./media/automated-backups-overview/sql-database-backup-storage-redundancy.png)

#### <a name="sql-managed-instance"></a>[Instância Gerenciada de SQL](#tab/managed-instance)

No portal do Azure, a opção de alterar a redundância de armazenamento de backup está localizada na folha **computação + armazenamento** acessível na opção **Configurar instância gerenciada** na guia **noções básicas** ao criar o instância gerenciada SQL.
![Abrir computação + configuração de armazenamento-folha](./media/automated-backups-overview/open-configuration-blade-managedinstance.png)

Localize a opção para selecionar redundância de armazenamento de backup na folha **computação + armazenamento** .
![Configurar a redundância de armazenamento de backup](./media/automated-backups-overview/select-backup-storage-redundancy-managedinstance.png)

---

### <a name="configure-backup-storage-redundancy-by-using-powershell"></a>Configurar a redundância de armazenamento de backup usando o PowerShell

#### <a name="sql-database"></a>[Banco de Dados SQL](#tab/single-database)

Para configurar a redundância de armazenamento de backup ao criar um novo banco de dados, você pode especificar o parâmetro-BackupStoageRedundancy. Os valores possíveis são geo, zona e local. Por padrão, todos os bancos de dados SQL usam o armazenamento com redundância geográfica para backups. A restauração geográfica será desabilitada se um banco de dados for criado com armazenamento de backup local ou com redundância de zona. 

```powershell
# Create a new database with geo-redundant backup storage.  
New-AzSqlDatabase -ResourceGroupName "ResourceGroup01" -ServerName "Server01" -DatabaseName "Database03" -Edition "GeneralPurpose" -Vcore 2 -ComputeGeneration "Gen5" -BackupStorageRedundancy Geo
```

Para obter detalhes [, visite New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase).

Para atualizar a redundância de armazenamento de backup de um banco de dados existente, você pode usar o parâmetro-BackupStorageRedundancy. Os valores possíveis são geo, zona e local.
Observe que, pode levar até 48 horas para que as alterações sejam aplicadas no banco de dados. Alternar do armazenamento de backup com redundância geográfica para o armazenamento local ou com redundância de zona desabilita a restauração geográfica. 

```powershell
# Change the backup storage redundancy for Database01 to zone-redundant. 
Set-AzSqlDatabase -ResourceGroupName "ResourceGroup01" -DatabaseName "Database01" -ServerName "Server01" -BackupStorageRedundancy Zone
```

Para obter detalhes [, visite Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)

> [!NOTE]
> Para usar o parâmetro-BackupStorageRedundancy com restauração de banco de dados, cópia de banco de dados ou criar operações secundárias, use Azure PowerShell versão AZ. SQL 2.11.0. 


#### <a name="sql-managed-instance"></a>[Instância Gerenciada de SQL](#tab/managed-instance)

Para configurar a redundância de armazenamento de backup durante a criação da instância gerenciada, você pode especificar o parâmetro-BackupStoageRedundancy. Os valores possíveis são geo, zona e local.

```powershell
New-AzSqlInstance -Name managedInstance2 -ResourceGroupName ResourceGroup01 -Location westcentralus -AdministratorCredential (Get-Credential) -SubnetId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/resourcegroup01/providers/Microsoft.Network/virtualNetworks/vnet_name/subnets/subnet_name" -LicenseType LicenseIncluded -StorageSizeInGB 1024 -VCore 16 -Edition "GeneralPurpose" -ComputeGeneration Gen4 -BackupStorageRedundancy Geo
```

Para obter mais detalhes [, visite New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance).

---

## <a name="use-azure-policy-to-enforce-backup-storage-redundancy"></a>Usar Azure Policy para impor a redundância de armazenamento de backup

Se você tiver requisitos de residência de dados que exigem que você mantenha todos os seus dados em uma única região do Azure, talvez queira impor backups com redundância de zona ou localmente para o banco de dados SQL ou Instância Gerenciada usando Azure Policy. Azure Policy é um serviço que você pode usar para criar, atribuir e gerenciar políticas que aplicam regras aos recursos do Azure. Azure Policy ajuda a manter esses recursos em conformidade com seus padrões corporativos e contratos de nível de serviço. Para saber mais, confira [Visão geral do Azure Policy](../../governance/policy/overview.md). 

### <a name="built-in-backup-storage-redundancy-policies"></a>Políticas internas de redundância de armazenamento de backup 

As novas políticas internas a seguir são adicionadas, que podem ser atribuídas no nível de assinatura ou grupo de recursos para bloquear a criação de novos bancos de dados ou instâncias com o armazenamento de backup com redundância geográfica. 

[O Banco de Dados SQL deve evitar o uso de redundância de backup de GRS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb219b9cf-f672-4f96-9ab0-f5a3ac5e1c13)

[As Instâncias Gerenciadas do SQL devem evitar o uso de redundância de backup de GRS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa9934fd7-29f2-4e6d-ab3d-607ea38e9079)

Uma lista completa de definições de políticas internas para o banco de dados SQL e Instância Gerenciada pode ser encontrada [aqui](./policy-reference.md).

Para impor os requisitos de residência de dados em um nível organizacional, essas políticas podem ser atribuídas a uma assinatura. Depois que eles são atribuídos em um nível de assinatura, os usuários na assinatura específica não poderão criar um banco de dados ou uma instância gerenciada com armazenamento de backup com redundância geográfica via portal do Azure ou Azure PowerShell. 

> [!IMPORTANT]
> As políticas do Azure não são impostas ao criar um banco de dados via T-SQL. Para impor a residência de dados ao criar um banco de dados usando o T-SQL, [use ' local ' ou ' zona ' como entrada para BACKUP_STORAGE_REDUNDANCY parâmetro na instrução CREATE DATABASE](/sql/t-sql/statements/create-database-transact-sql#create-database-using-zone-redundancy-for-backups).

Saiba como atribuir políticas usando o [portal do Azure](../../governance/policy/assign-policy-portal.md) ou [Azure PowerShell](../../governance/policy/assign-policy-powershell.md)


## <a name="next-steps"></a>Próximas etapas

- Os backups de banco de dados são uma parte essencial de qualquer estratégia de recuperação de desastre e continuidade dos negócios, porque eles protegem seus dados contra exclusão ou corrupção acidentais. Para saber mais sobre as outras soluções de continuidade de negócios do banco de dados SQL, consulte [visão geral da continuidade de negócios](business-continuity-high-availability-disaster-recover-hadr-overview.md)
- Obtenha mais informações sobre como [restaurar um banco de dados para um momento determinado usando o portal do Azure](recovery-using-backups.md).
- Obtenha mais informações sobre como [restaurar um banco de dados para um momento determinado usando o PowerShell](scripts/restore-database-powershell.md).
- Para obter informações sobre como configurar, gerenciar e restaurar a retenção de longo prazo de backups automatizados no armazenamento de Blobs do Azure usando o portal do Azure, consulte [Gerenciar a retenção de backup de longo prazo usando o portal do Azure](long-term-backup-retention-configure.md).
- Para obter informações sobre como configurar, gerenciar e restaurar a retenção de longo prazo de backups automatizados no armazenamento de Blobs do Azure usando o PowerShell, consulte [Gerenciar a retenção de backup de longo prazo usando o PowerShell](long-term-backup-retention-configure.md).
- Para saber tudo sobre o consumo de armazenamento de backup no Azure SQL Instância Gerenciada, confira [consumo de armazenamento de backup em instância gerenciada explicado](https://aka.ms/mi-backup-explained).
- Para saber como ajustar a retenção e os custos de armazenamento de backup para o Azure SQL Instância Gerenciada, consulte ajustar [os custos de armazenamento de backup em instância gerenciada](https://aka.ms/mi-backup-tuning).
