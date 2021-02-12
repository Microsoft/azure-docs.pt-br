---
title: O que é a camada de serviço de hiperescala?
description: Este artigo descreve a camada de serviço de hiperescala no modelo de compra baseado em vCore no banco de dados SQL do Azure e explica como ele é diferente das camadas de serviço Uso Geral e Comercialmente Crítico.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 1/13/2021
ms.openlocfilehash: 4b5020b6cf7ac2f7aec586d7e6499285c1447b68
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98209756"
---
# <a name="hyperscale-service-tier"></a>Tipo de serviço de Hiperescala

Banco de dados SQL do Azure baseia-se na arquitetura de mecanismo de banco de dados do SQL Server que é ajustada para o ambiente de nuvem para garantir a disponibilidade de 99,99%, até mesmo no caso de falhas de infraestrutura. Há três modelos de arquitetura que são usados no Banco de Dados SQL do Azure:

- Uso Geral/Padrão
- Hiperescala
- Comercialmente Crítico/Premium

A camada de serviço em hiperescala no banco de dados SQL do Azure é a camada de serviço mais recente em que o modelo de compra baseado em vCore. Essa camada de serviço é um armazenamento altamente escalonável e o nível de desempenho de computação que aproveita a arquitetura do Azure para escalar horizontalmente o armazenamento e recursos de computação para um banco de dados SQL do Azure substancialmente além dos limites disponíveis para uso geral e negócios Comercialmente Crítico.

> [!NOTE]
>
> - Para obter detalhes sobre as camadas de serviço Uso Geral e Comercialmente Crítico no modelo de compra baseado em vCore, consulte [uso geral](service-tier-general-purpose.md) e [comercialmente crítico](service-tier-business-critical.md) camadas de serviço. Para obter uma comparação do modelo de compra baseado em vCore com o modelo de compra baseado em DTU, consulte [Modelos e recursos de compra do Banco de Dados SQL do Azure](purchasing-models.md).
> - Atualmente, a camada de serviço de hiperescala está disponível apenas para o banco de dados SQL do Azure e não o Azure SQL Instância Gerenciada.

## <a name="what-are-the-hyperscale-capabilities"></a>Quais são as funcionalidades de Hiperescala

A camada de serviço da Hiperescala no Banco de Dados SQL do Azure fornece os seguintes recursos adicionais:

- Suporte para até 100 TB de tamanho de banco de dados
- Backups de banco de dados quase instantâneos (com base em instantâneos de arquivo armazenados no armazenamento de BLOBs do Azure), independentemente do tamanho, sem nenhum impacto de e/s  
- Rápidas restaurações de banco de dados (com base em instantâneos de arquivo) em minutos, em vez de horas ou dias (não um tamanho de operação de dados)
- Maior desempenho geral devido à maior taxa de transferência de log e tempos mais rápidos de confirmação de transação, independentemente dos volumes de dados
- Rápida expansão - você pode provisionar uma ou mais somente leitura nós para o descarregamento de sua carga de trabalho de leitura e para uso como reserva quente
- Dimensionamento rápido – você pode, em constante tempo, escalar verticalmente seus recursos de computação para acomodar cargas de trabalho pesadas quando necessário e, em seguida, dimensionar os recursos de computação de volta quando não for necessário.

A camada de serviço da Hiperescala elimina muitos dos limites práticos vistos tradicionalmente em bancos de dados de nuvem. Onde mais outros bancos de dados são limitados pelos recursos disponíveis em um único nó, bancos de dados na camada de serviço da Hiperescala não têm esses limites. Com sua arquitetura de armazenamento flexível, o armazenamento aumenta conforme necessário. Na verdade, os bancos de dados de hiperescala não são criados com um tamanho máximo definido. Um banco de dados de hiperescala cresce conforme necessário-e você é cobrado apenas pela capacidade que usar. Para cargas de trabalho de leitura intensa, a camada de serviço da Hiperescala fornece rápida expansão por meio do provisionamento de leitura de réplicas adicionais conforme necessário para o descarregamento de cargas de trabalho leitura.

Além disso, o tempo necessário para criar backups de banco de dados ou para aumentar ou diminuir a escala não está mais vinculado ao volume de dados no banco de dados. Bancos de dados de Hiperescala podem ser armazenados virtualmente instantaneamente. Você também pode dimensionar um banco de dados em dezenas de terabytes para cima ou para baixo em minutos. Esse recurso libera você das preocupações sobre ser encaixotado pelas opções iniciais de configuração.

Para obter mais informações sobre os tamanhos da computação para a camada de serviço em Hiperescala, confira [Características da camada de serviço](service-tiers-vcore.md#service-tiers).

## <a name="who-should-consider-the-hyperscale-service-tier"></a>Quem deve considerar a camada de serviço da Hiperescala

A camada de serviço de hiperescala destina-se à maioria das cargas de trabalho de negócios, pois fornece excelente flexibilidade e alto desempenho com recursos de computação e armazenamento escalonáveis de maneira independente. Com a capacidade de armazenamento de dimensionamento automático de até 100 TB, é uma ótima opção para clientes que:

- Ter grandes bancos de dados locais e desejar modernizar seus aplicativos ao migrar para a nuvem
- Já estão na nuvem e são limitados pelas restrições de tamanho máximo do banco de dados de outras camadas de serviço (1-4 TB)
- Ter bancos de dados menores, mas exigir dimensionamento rápido de computação vertical e horizontal, alto desempenho, backup instantâneo e restauração rápida de banco de dados.

A camada de serviço de hiperescala dá suporte a uma ampla variedade de cargas de trabalho de SQL Server, desde OLTP puro até análise pura, mas é essencialmente otimizada para cargas de trabalho OLTP e HTAP (processamento analítico e de transações híbridas).

> [!IMPORTANT]
> Pools Elásticos não dão suporte a camada de serviço da Hiperescala.

## <a name="hyperscale-pricing-model"></a>Modelo de preços de Hiperescala

A camada de serviço em Hiperescala só está disponível no [modelo vCore](service-tiers-vcore.md). Para alinhar-se com a nova arquitetura, o modelo de preços é ligeiramente diferente de camadas de serviço de Uso Geral ou Comercialmente Críticas:

- **Computação**:

  O preço de unidade de computação em Hiperescala é por réplica. O preço do [Benefício Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) é aplicado para réplicas em escala de leitura automaticamente. Criamos uma réplica primária e uma réplica somente leitura por banco de dados de hiperescala por padrão.  Os usuários podem ajustar o número total de réplicas, incluindo a primária de 1-5.

- **Armazenamento**:

  Você não precisa especificar o tamanho máximo de dados ao configurar um banco de dados da Hiperescala. Na camada de hiperescala, você será cobrado pelo armazenamento de seu banco de dados com base na alocação real. O armazenamento é alocado automaticamente entre 40 GB e 100 TB, em incrementos de 10 GB. Vários arquivos de dados podem crescer ao mesmo tempo, se necessário. Um banco de dados de hiperescala é criado com um tamanho inicial de 10 GB e começa crescendo 10 GB a cada 10 minutos, até atingir o tamanho de 40 GB.

Para obter mais informações sobre os preços da Hiperescala, confira [Preços do Banco de Dados SQL do Azure](https://azure.microsoft.com/pricing/details/sql-database/single/)

## <a name="distributed-functions-architecture"></a>Arquitetura de funções distribuídas

Ao contrário de mecanismos de banco de dados tradicional que centralizou a todas as funções de gerenciamento de dados em um local/processo (até mesmo então chamados bancos de dados distribuídos em produção hoje têm várias cópias de um mecanismo de dados monolítico), um banco de dados da Hiperescala separa o mecanismo de processamento de consulta, em que a semântica de diversos mecanismos de dados divergem dos componentes que fornecem armazenamento de longo prazo e a durabilidade dos dados. Dessa forma, a capacidade de armazenamento pode ser perfeitamente expandida até onde conforme necessário (o destino inicial é 100 TB). Réplicas somente leitura compartilham os mesmos componentes de armazenamento, portanto, nenhuma cópia de dados é necessária para criar uma nova réplica legível.

O diagrama a seguir ilustra os diferentes tipos de nós em um banco de dados de Hiperescala:

![Arquitetura](./media/service-tier-hyperscale/hyperscale-architecture.png)

Um banco de dados de hiperescala contém os seguintes tipos diferentes de componentes:

### <a name="compute"></a>Computação

O nó de computação é onde reside o mecanismo relacional. É aí que ocorre o processamento de idioma, consulta e transação. Todas as interações do usuário com um banco de dados da Hiperescala ocorrem por meio de nós de computação. Nós têm caches baseado em SSD (rotulado como RBPEX - extensão do Pool de buffers resiliente no diagrama anterior) para minimizar o número da rede de computação viagens de ida e necessárias para buscar uma página de dados. Há um nó de computação principal em que todas as transações e cargas de trabalho de leitura / gravação são processadas. Há um ou mais nós de computação secundária que atuam como nós em espera ativos para fins de failover, bem como para atuam como nós de computação de somente leitura para o descarregamento de cargas de trabalho de leitura (se essa funcionalidade é desejada).

O mecanismo de banco de dados em execução em nós de computação de hiperescala é o mesmo que em outras camadas de serviço do banco de dados SQL do Azure. Quando os usuários interagem com o mecanismo de banco de dados em nós de computação de hiperescala, a área de superfície e o comportamento do mecanismo com suporte são os mesmos que em outras camadas de serviço, com exceção das [limitações conhecidas](#known-limitations).

### <a name="page-server"></a>Servidor de página

Servidores de página são sistemas que representam um mecanismo de armazenamento dimensionado.  Cada servidor de páginas é responsável por um subconjunto das páginas no banco de dados.  De forma nominal, cada servidor de página controla até 128 GB ou até 1 TB de dados. Nenhum dado é compartilhado em mais de um servidor de páginas (fora das réplicas de servidor de página que são mantidas para redundância e disponibilidade). O trabalho de um servidor de páginas é servir páginas de banco de dados para os nós de computação sob demanda e manter as páginas atualizadas à medida que as transações atualizam dados. Os servidores de página são mantidos atualizados com a execução de registros de log do serviço de log. Os servidores de página também mantêm a cobertura de caches baseados em SSD para melhorar o desempenho. Armazenamento de longo prazo de páginas de dados é mantido no armazenamento do Azure para confiabilidade adicional.

### <a name="log-service"></a>Serviço de log

O serviço de log aceita registros de log da réplica de computação primária, os mantém em um cache durável e encaminha os registros de log para o restante das réplicas de computação (para que eles possam atualizar seus caches), bem como os servidores de páginas relevantes, para que os dados possam ser atualizados lá. Dessa forma, todas as alterações de dados da réplica de computação primária são propagadas por meio do serviço de log para todas as réplicas de computação e servidores de página secundários. Por fim, os registros de log são enviados por push para o armazenamento de longo prazo no armazenamento do Azure, que é um repositório de armazenamento virtualmente infinito. Esse mecanismo elimina a necessidade de truncamento de log frequente. O serviço de log também tem memória local e caches SSD para acelerar o acesso aos registros de log.

### <a name="azure-storage"></a>Armazenamento do Azure

O armazenamento do Azure contém todos os arquivos de dados em um banco de dado. Os servidores de páginas mantêm os arquivos de dados no armazenamento do Azure atualizados. Esse armazenamento é usado para fins de backup, bem como para replicação entre regiões do Azure. Os backups são implementados usando instantâneos de armazenamento de arquivos de dados. As operações de restauração usando instantâneos são rápidas, independentemente do tamanho dos dados. Os dados podem ser restaurados para qualquer ponto no tempo dentro do período de retenção de backup do banco de dados.

## <a name="backup-and-restore"></a>Backup e restauração

Os backups são baseados em instantâneo de arquivo e, portanto, são quase instantâneos. O armazenamento e a separação de computação permitem o envio por push da operação de backup/restauração para a camada de armazenamento para reduzir a carga de processamento na réplica de computação primária. Como resultado, o backup do banco de dados não afeta o desempenho do nó de computação primário. Da mesma forma, a PITR (recuperação pontual) é feita revertendo para os instantâneos de arquivo e, como tal, não é um tamanho de operação de dados. A restauração de um banco de dados de hiperescala na mesma região do Azure é uma operação em tempo constante, e até mesmo bancos de dados com vários terabytes podem ser restaurados em minutos, em vez de horas ou dias. A criação de novos bancos de dados por meio da restauração de um backup existente também aproveita esse recurso: a criação de cópias para fins de desenvolvimento ou teste, mesmo de bancos de dados de vários terabytes, é factível em minutos.

Para a restauração geográfica de bancos de dados de hiperescala, confira [restaurando um gráfico de hiperescala para uma região diferente](#restoring-a-hyperscale-database-to-a-different-region).

## <a name="scale-and-performance-advantages"></a>Vantagens de desempenho e escala

Com a capacidade de criar rapidamente nós de computação adicionais de somente leitura para cima/para baixo, a arquitetura permite significativa ler recursos de escala e Hiperescala também pode liberar o nó de computação principal para atender às solicitações de gravação mais. Além disso, os nós de computação podem ser dimensionados para cima/para baixo rapidamente devido à arquitetura de armazenamento compartilhado da arquitetura da Hiperescala.

## <a name="create-a-hyperscale-database"></a>Criar um banco de dados de hiperescala

Um banco de dados de hiperescala pode ser criado usando o [portal do Azure](https://portal.azure.com), [T-SQL](/sql/t-sql/statements/create-database-transact-sql), [PowerShell](/powershell/module/azurerm.sql/new-azurermsqldatabase)ou [CLI](/cli/azure/sql/db#az-sql-db-create). Os bancos de dados de hiperescala estão disponíveis apenas usando o [modelo de compra baseado em vCore](service-tiers-vcore.md).

O comando T-SQL a seguir cria um banco de dados em Hiperescala. Você deve especificar tanto o objetivo do serviço quanto a edição na instrução `CREATE DATABASE`. Consulte os [limites de recurso](./resource-limits-vcore-single-databases.md#hyperscale---provisioned-compute---gen4) para obter uma lista de objetivos de serviço válidos.

```sql
-- Create a Hyperscale Database
CREATE DATABASE [HyperscaleDB1] (EDITION = 'Hyperscale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

Isso criará um banco de dados de hiperescala no hardware Gen5 com quatro núcleos.

## <a name="upgrade-existing-database-to-hyperscale"></a>Atualizar banco de dados existente para hiperescala

Você pode mover seus bancos de dados do Azure para o hiperescala usando o [portal do Azure](https://portal.azure.com), o [T-SQL](/sql/t-sql/statements/alter-database-transact-sql), o [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase)ou a [CLI](/cli/azure/sql/db#az-sql-db-update). Neste momento, essa é uma migração unidirecional. Não é possível mover bancos de dados de um subdimensionamento para outra camada de serviço, a não ser por meio da exportação e da importação. Para provas de conceito (POCs), é recomendável fazer uma cópia dos bancos de dados de produção e migrar a cópia para o hiperescala. A migração de um banco de dados existente no banco de dados SQL do Azure para a camada de hiperescala é um tamanho de operação de dado.

O comando T-SQL a seguir move um banco de dados para a camada de serviço em Hiperescala. Você deve especificar tanto o objetivo do serviço quanto a edição na instrução `ALTER DATABASE`.

```sql
-- Alter a database to make it a Hyperscale Database
ALTER DATABASE [DB2] MODIFY (EDITION = 'Hyperscale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

## <a name="connect-to-a-read-scale-replica-of-a-hyperscale-database"></a>Conectar-se a uma réplica em escala de leitura de um banco de dados em Hiperescala

Em bancos de dados de hiperescala, o `ApplicationIntent` argumento na cadeia de conexão fornecido pelo cliente determina se a conexão é roteada para a réplica de gravação ou para uma réplica secundária somente leitura. Se o `ApplicationIntent` conjunto para `READONLY` e o banco de dados não tiver uma réplica secundária, a conexão será roteada para a réplica primária e o padrão é o `ReadWrite` comportamento.

```cmd
-- Connection string with application intent
Server=tcp:<myserver>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

As réplicas secundárias de hiperescala são idênticas, usando o mesmo objetivo de nível de serviço que a réplica primária. Se mais de uma réplica secundária estiver presente, a carga de trabalho será distribuída entre todos os secundários disponíveis. Cada réplica secundária é atualizada de forma independente. Assim, réplicas diferentes podem ter uma latência de dados diferente em relação à réplica primária.

## <a name="database-high-availability-in-hyperscale"></a>Alta disponibilidade do banco de dados em hiperescala

Como em todas as outras camadas de serviço, a hiperescala garante a durabilidade dos dados para transações confirmadas, independentemente da disponibilidade da réplica de computação. A extensão do tempo de inatividade devido à réplica primária se tornar indisponível depende do tipo de failover (planejado versus não planejado) e da presença de pelo menos uma réplica secundária. Em um failover planejado (ou seja, um evento de manutenção), o sistema cria a nova réplica primária antes de iniciar um failover ou usa uma réplica secundária existente como o destino de failover. Em um failover não planejado (ou seja, uma falha de hardware na réplica primária), o sistema usará uma réplica secundária como um destino de failover, se houver, ou criará uma nova réplica primária do pool de capacidade de computação disponível. No último caso, a duração do tempo de inatividade é mais longa devido a etapas adicionais necessárias para criar a nova réplica primária.

Para SLA de hiperescala, consulte [SLA para o banco de dados SQL do Azure](https://azure.microsoft.com/support/legal/sla/sql-database/).

## <a name="disaster-recovery-for-hyperscale-databases"></a>Recuperação de desastre para bancos de dados de hiperescala

### <a name="restoring-a-hyperscale-database-to-a-different-region"></a>Restaurando um banco de dados de hiperescala para uma região diferente

Se você precisar restaurar um banco de dados de hiperescala no banco de dados SQL do Azure para uma região que não seja a que está hospedada no momento, como parte de uma operação de recuperação de desastres ou de análise, realocação ou qualquer outro motivo, o método principal é fazer uma restauração geográfica do banco de dados. Isso envolve exatamente as mesmas etapas que você usaria para restaurar qualquer outro banco de dados no banco de dados SQL para uma região diferente:

1. Crie um [servidor](logical-servers.md) na região de destino se você ainda não tiver um servidor apropriado.  Esse servidor deve pertencer à mesma assinatura que o servidor original (origem).
2. Siga as instruções no tópico de [restauração geográfica](./recovery-using-backups.md#geo-restore) da página sobre como restaurar um banco de dados no banco de dados SQL do Azure de backups automáticos.

> [!NOTE]
> Como a origem e o destino estão em regiões separadas, o banco de dados não pode compartilhar o armazenamento de instantâneos com o banco de dados de origem como em restaurações não geográficas, que são concluídas rapidamente, independentemente do tamanho do banco de dados. No caso de uma restauração geográfica de um banco de dados de hiperescala, ela será uma operação de tamanho de dado, mesmo que o destino esteja na região emparelhada do armazenamento replicado geograficamente. Portanto, uma restauração geográfica levará tempo proporcional ao tamanho do banco de dados que está sendo restaurado. Se o destino estiver na região emparelhada, a transferência de dados estará dentro de uma região, que será significativamente mais rápida do que uma transferência de dados entre regiões, mas ela ainda será uma operação de tamanho de dados.

## <a name="available-regions"></a><a name=regions></a>Regiões disponíveis

A camada de hiperescala do banco de dados SQL do Azure está disponível em todas as regiões, mas habilitada por padrão nas seguintes regiões listadas abaixo. Se desejar criar um banco de dados de hiperescala em uma região em que o hiperescala não esteja habilitado por padrão, você poderá enviar uma solicitação de integração via portal do Azure. Para obter instruções, consulte [aumentos de cota de solicitação para o banco de dados SQL do Azure](quota-increase-request.md) para obter instruções. Ao enviar sua solicitação, use as seguintes diretrizes:

- Use o tipo de cota do banco de dados SQL de [acesso à região](quota-increase-request.md#region) .
- Na descrição, adicione os núcleos de SKU/total de computação, incluindo réplicas legíveis, e indique que você está solicitando capacidade de hiperescala.
- Especifique também uma projeção do tamanho total de todos os bancos de dados ao longo do tempo em TB.

Regiões habilitadas:
- Leste da Austrália
- Sudeste da Austrália
- Austrália Central
- Sul do Brasil
- Canadá Central
- Centro dos EUA
- Leste da China 2
- Norte da China 2
- Leste da Ásia
- Leste dos EUA
- Leste dos EUA 2
- França Central
- Centro-Oeste da Alemanha
- Leste do Japão
- Oeste do Japão
- Coreia Central
- Sul da Coreia
- Centro-Norte dos EUA
- Norte da Europa
- Leste da Noruega
- Oeste da Noruega
- Norte da África do Sul
- Centro-Sul dos Estados Unidos
- Sudeste Asiático
- Oeste da Suíça
- Sul do Reino Unido
- Oeste do Reino Unido
- DoD Central dos EUA
- DoD do Leste dos EUA
- Govt Arizona americano
- US govt Texas
- Centro-Oeste dos EUA
- Europa Ocidental
- Oeste dos EUA
- Oeste dos EUA 2

## <a name="known-limitations"></a>Limitações conhecidas

Essas são as limitações atuais da camada de serviço de hiperescala a partir do GA.  Estamos trabalhando ativamente para remover o máximo possível de limitações.

| Problema | Descrição |
| :---- | :--------- |
| O painel gerenciar backups de um servidor não mostra bancos de dados de hiperescala. Eles serão filtrados da exibição.  | O hiperscale tem um método separado para gerenciar backups, de modo que a retenção de Long-Term e as configurações de retenção de backup pontual não se aplicam. De acordo, os bancos de dados de hiperescala não aparecem no painel gerenciar backup.<br><br>Para bancos de dados migrados para o subdimensionamento de outras camadas de serviço do Azure SQL Database, os backups de pré-migração são mantidos durante o período de [retenção de backup](automated-backups-overview.md#backup-retention) do banco de dados de origem. Esses backups podem ser usados para [restaurar](recovery-using-backups.md#programmatic-recovery-using-automated-backups) o banco de dados de origem para um ponto no tempo antes da migração.|
| Restauração em um momento determinado | Um banco de dados não hiperescala não pode ser restaurado como um banco de dados de hiperescala, e um banco de dados de hiperescala não pode ser restaurado como um banco de dados que não seja de hiperescala. Para um banco de dados não hiperescala que foi migrado para o subdimensionamento alterando sua camada de serviço, a restauração para um ponto no tempo antes da migração e dentro do período de retenção de backup do banco de dados é suportada [programaticamente](recovery-using-backups.md#programmatic-recovery-using-automated-backups). O banco de dados restaurado não será hiperescala. |
| Ao alterar a camada de serviço do banco de dados SQL do Azure para o subdimensionamento, a operação falhará se o banco de dados tiver arquivos com mais de 1 TB | Em alguns casos, pode ser possível contornar esse problema [reduzindo](file-space-manage.md#shrinking-data-files) os arquivos grandes para menos de 1 TB antes de tentar alterar a camada de serviço para hiperescala. Use a consulta a seguir para determinar o tamanho atual dos arquivos de banco de dados. `SELECT file_id, name AS file_name, size * 8. / 1024 / 1024 AS file_size_GB FROM sys.database_files WHERE type_desc = 'ROWS'`;|
| Instância Gerenciada de SQL | Atualmente, o Azure SQL Instância Gerenciada não tem suporte com bancos de dados de hiperescala. |
| Pools elásticos |  Atualmente, não há suporte para pools elásticos com o hiperscale.|
| Migração para Hiperescala é, no momento, uma operação unidirecional | Depois que um banco de dados é migrado para o subdimensionamento, ele não pode ser migrado diretamente para uma camada de serviço não hiperescala. No momento, a única maneira de migrar um banco de dados de hiperescala para não hiperescala é exportar/importar usando um arquivo bacpac ou outras tecnologias de movimentação de dados (cópia em massa, Azure Data Factory, Azure Databricks, SSIS etc.) Exportação/importação de Bacpac do portal do Azure, do PowerShell usando [New-AzSqlDatabaseExport](/powershell/module/az.sql/new-azsqldatabaseexport) ou [New-AzSqlDatabaseImport](/powershell/module/az.sql/new-azsqldatabaseimport), de CLI do Azure usando [AZ SQL DB Export](/cli/azure/sql/db#az-sql-db-export) e [AZ SQL DB Import](/cli/azure/sql/db#az-sql-db-import)e da [API REST](/rest/api/sql/databases%20-%20import%20export) não tem suporte. A importação/exportação de Bacpac para bancos de dados de hiperescala menores (até 200 GB) é suportada usando o SSMS e [SqlPackage](/sql/tools/sqlpackage) versão 18,4 e posterior. Para bancos de dados maiores, a exportação/importação de bacpac pode levar muito tempo e pode falhar por vários motivos.|
| Migração de bancos de dados com In-Memory objetos OLTP | O hiperscale dá suporte a um subconjunto de objetos OLTP In-Memory, incluindo tipos de tabela com otimização de memória, variáveis de tabela e módulos compilados nativamente. No entanto, quando qualquer tipo de In-Memory objetos OLTP estão presentes no banco de dados que está sendo migrado, não há suporte para a migração de camadas de serviço Premium e Comercialmente Crítico para hiperescala. Para migrar esse banco de dados para um subdimensionamento, todos os objetos OLTP In-Memory e suas dependências devem ser descartados. Depois que o banco de dados é migrado, esses objetos podem ser recriados. Tabelas duráveis e não duráveis com otimização de memória não têm suporte no momento em hiperescala e devem ser alteradas para tabelas de disco.|
| Replicação geográfica  | Você ainda não pode configurar a replicação geográfica para a hiperescala do banco de dados SQL do Azure. |
| Cópia de banco de dados | A cópia do banco de dados em hiperescala agora está em visualização pública. |
| Recursos de banco de dados inteligente | Com exceção da opção "forçar plano", todas as outras opções de ajuste automático ainda não têm suporte em hiperescala: as opções podem parecer estar habilitadas, mas não haverá recomendações ou ações feitas. |
| Insights de Desempenho de Consulta | No momento, não há suporte para informações de desempenho de consulta para bancos de dados de hiperescala. |
| Reduzir Banco de Dados | Atualmente, não há suporte para DBCC SHRINKDATABASE ou DBCC SHRINKFILE para bancos de dados de hiperescala. |
| Verificação de integridade do banco de dados | Atualmente, não há suporte para DBCC CHECKDB em bancos de dados de hiperescala. DBCC CHECKFILEGROUP e DBCC CHECKtable podem ser usados como uma solução alternativa. Confira [integridade de dados no banco](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/) de dados SQL do Azure para obter detalhes sobre o gerenciamento de integridade de dados no Azure SQL Database. |

## <a name="next-steps"></a>Próximas etapas

- Para perguntas frequentes sobre Hiperescala, confira [Perguntas frequentes sobre Hiperescala](service-tier-hyperscale-frequently-asked-questions-faq.md).
- Para obter informações sobre as camadas de serviço, consulte [camadas de serviço](purchasing-models.md)
- Consulte [visão geral dos limites de recursos em um servidor](resource-limits-logical-server.md) para obter informações sobre os limites nos níveis de servidor e assinatura.
- Para comprar os limites de modelo para um banco de dados individual, confira [Limites de modelo de compra baseado em vCore do Banco de Dados SQL do Azure para um banco de dados individual](resource-limits-vcore-single-databases.md).
- Para obter uma lista de recursos e de comparação, consulte [Recursos comuns do SQL](features-comparison.md).
