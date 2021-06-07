---
title: Migrar do DTU para o vCore
description: Migre um banco de dados do Banco de Dados SQL do Azure do modelo de DTU para o modelo de vCore. A migração para o vCore é semelhante ao upgrade ou ao downgrade entre as camadas Standard e Premium.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
ms.custom: sqldbrb=1
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake
ms.date: 02/09/2021
ms.openlocfilehash: 332a2273a377268a425619a0cdaa5f4780b46e73
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100361641"
---
# <a name="migrate-azure-sql-database-from-the-dtu-based-model-to-the-vcore-based-model"></a>Migrar o Banco de Dados SQL do Azure do modelo baseado em DTU para o modelo baseado em vCore
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Este artigo descreve como migrar seu banco de dados do Banco de Dados SQL do Azure do [modelo de compra baseado em DTU](service-tiers-dtu.md) para o [modelo de compra baseado em vCore](service-tiers-vcore.md). 

## <a name="migrate-a-database"></a>Migrar um banco de dados

A migração de um banco de dados do modelo de compra baseado em DTU para o modelo de compra baseado em vCore é semelhante ao dimensionamento entre os objetivos de serviço nas camadas de serviço Básico, Standard e Premium, com [duração](single-database-scale.md#latency) semelhante e um [tempo de inatividade mínimo](scale-resources.md) no final do processo de migração. Um banco de dados migrado para o modelo de compra baseado em vCore pode ser migrado novamente para o modelo de compra baseado em DTU a qualquer momento da mesma forma, com a exceção dos bancos de dados migrados para a camada de serviço [Hiperescala](service-tier-hyperscale.md). 

## <a name="choose-the-vcore-service-tier-and-service-objective"></a>Escolher a camada de serviço de vCore e o objetivo de serviço

Na maioria dos casos de migração de DTU para vCore, os bancos de dados e os pools elásticos nas camadas de serviço Básico e Standard serão mapeados para a camada de serviço [Uso Geral](service-tier-general-purpose.md). Os bancos de dados e os pools elásticos na camada de serviço Premium serão mapeados para a camada de serviço [Comercialmente Crítico](service-tier-business-critical.md). Dependendo do cenário e dos requisitos do aplicativo, a camada de serviço [Hiperescala](service-tier-hyperscale.md) pode ser frequentemente usada como o destino de migração para bancos de dados individuais em todas as camadas de serviço de DTU.

Para escolher o objetivo de serviço ou o tamanho da computação para o banco de dados migrado no modelo de vCore, use uma regra prática simples, mas aproximada: a cada 100 DTUs nas camadas Básico ou Standard, é necessário, *no mínimo*, 1 vCore, e a cada 125 DTUs na camada Premium, é necessário, *no mínimo*, 1 vCore. 

> [!TIP]
> Essa regra é aproximada porque não considera a geração de hardware usada para o pool elástico ou o banco de dados de DTU. 

No modelo de DTU, qualquer [geração de hardware](purchasing-models.md#hardware-generations-in-the-dtu-based-purchasing-model) disponível pode ser usada para o banco de dados ou o pool elástico. Além disso, você tem apenas controle indireto sobre o número de vCores (CPUs lógicas), escolhendo valores de DTU ou de eDTU maiores ou menores. 

Com o modelo de vCore, os clientes precisam fazer uma escolha explícita da geração de hardware e do número de vCores (CPUs lógicas). O modelo de DTU não oferece essas opções, porém, a geração de hardware e o número de CPUs lógicas usadas para cada banco de dados e pool elástico são expostos por meio de exibições de gerenciamento dinâmico. Isso possibilita determinar o objetivo de serviço de vCore correspondente com mais precisão. 

A abordagem a seguir usa essas informações para determinar um objetivo de serviço de vCore com uma alocação semelhante de recursos, para obter um nível de desempenho semelhante após a migração para o modelo de vCore.

### <a name="dtu-to-vcore-mapping"></a>Mapeamento de DTU para vCore

Uma consulta T-SQL abaixo, quando executada no contexto de um banco de dados de DTU a ser migrado, retornará um número correspondente (possivelmente fracionário) de vCores em cada geração de hardware no modelo de vCore. Ao arredondar esse número para o número mais próximo de vCores disponíveis para [bancos de dados](resource-limits-vcore-single-databases.md) e [pools elásticos](resource-limits-vcore-elastic-pools.md) em cada geração de hardware no modelo de vCore, os clientes podem escolher o objetivo de serviço de vCore que seja a correspondência mais próxima com o pool elástico ou o banco de dados de DTU. 

Os cenários de migração de exemplo que usam essa abordagem são descritos na seção [Exemplos](#dtu-to-vcore-migration-examples).

Execute essa consulta no contexto do banco de dados a ser migrado, em vez de no banco de dados `master`. Ao migrar um pool elástico, execute a consulta no contexto de qualquer banco de dados do pool.

```SQL
WITH dtu_vcore_map AS
(
SELECT rg.slo_name,
       DATABASEPROPERTYEX(DB_NAME(), 'Edition') AS dtu_service_tier,
       CASE WHEN rg.slo_name LIKE '%SQLG4%' THEN 'Gen4'
            WHEN rg.slo_name LIKE '%SQLGZ%' THEN 'Gen4'
            WHEN rg.slo_name LIKE '%SQLG5%' THEN 'Gen5'
            WHEN rg.slo_name LIKE '%SQLG6%' THEN 'Gen5'
            WHEN rg.slo_name LIKE '%SQLG7%' THEN 'Gen5'
       END AS dtu_hardware_gen,
       s.scheduler_count * CAST(rg.instance_cap_cpu/100. AS decimal(3,2)) AS dtu_logical_cpus,
       CAST((jo.process_memory_limit_mb / s.scheduler_count) / 1024. AS decimal(4,2)) AS dtu_memory_per_core_gb
FROM sys.dm_user_db_resource_governance AS rg
CROSS JOIN (SELECT COUNT(1) AS scheduler_count FROM sys.dm_os_schedulers WHERE status = 'VISIBLE ONLINE') AS s
CROSS JOIN sys.dm_os_job_object AS jo
WHERE dtu_limit > 0
      AND
      DB_NAME() <> 'master'
      AND
      rg.database_id = DB_ID()
)
SELECT dtu_logical_cpus,
       dtu_hardware_gen,
       dtu_memory_per_core_gb,
       dtu_service_tier,
       CASE WHEN dtu_service_tier = 'Basic' THEN 'General Purpose'
            WHEN dtu_service_tier = 'Standard' THEN 'General Purpose or Hyperscale'
            WHEN dtu_service_tier = 'Premium' THEN 'Business Critical or Hyperscale'
       END AS vcore_service_tier,
       CASE WHEN dtu_hardware_gen = 'Gen4' THEN dtu_logical_cpus
            WHEN dtu_hardware_gen = 'Gen5' THEN dtu_logical_cpus * 0.7
       END AS Gen4_vcores,
       7 AS Gen4_memory_per_core_gb,
       CASE WHEN dtu_hardware_gen = 'Gen4' THEN dtu_logical_cpus * 1.7
            WHEN dtu_hardware_gen = 'Gen5' THEN dtu_logical_cpus
       END AS Gen5_vcores,
       5.05 AS Gen5_memory_per_core_gb,
       CASE WHEN dtu_hardware_gen = 'Gen4' THEN dtu_logical_cpus
            WHEN dtu_hardware_gen = 'Gen5' THEN dtu_logical_cpus * 0.8
       END AS Fsv2_vcores,
       1.89 AS Fsv2_memory_per_core_gb,
       CASE WHEN dtu_hardware_gen = 'Gen4' THEN dtu_logical_cpus * 1.4
            WHEN dtu_hardware_gen = 'Gen5' THEN dtu_logical_cpus * 0.9
       END AS M_vcores,
       29.4 AS M_memory_per_core_gb
FROM dtu_vcore_map;
```

### <a name="additional-factors"></a>Fatores adicionais

Além do número de vCores (CPUs lógicas) e da geração de hardware, vários outros fatores podem influenciar a escolha do objetivo de serviço de vCore:

- A consulta T-SQL de mapeamento faz a correspondência dos objetivos de serviço de DTU e vCore em termos da capacidade de CPU. Portanto, os resultados serão mais precisos para cargas de trabalho associadas à CPU.
- Para a mesma geração de hardware e o mesmo número de vCores, os limites de recursos de taxa de transferência de log de transações e da IOPS para bancos de dados de vCore são geralmente maiores do que para bancos de dados de DTU. Para cargas de trabalho associadas a E/S, talvez seja possível reduzir o número de vCores no modelo de vCore para obter o mesmo nível de desempenho. Os limites de recursos para bancos de dados de DTU e vCore em valores absolutos são expostos na exibição [sys.dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database). A comparação desses valores entre o banco de dados de DTU a ser migrado e um banco de dados de vCore usando um objetivo de serviço com correspondência aproximada ajudará você a escolher o objetivo de serviço de vCore com mais precisão.
- A consulta de mapeamento também retorna a quantidade de memória por núcleo para o pool elástico ou o banco de dados de DTU a ser migrado e para cada geração de hardware no modelo de vCore. É importante garantir uma memória total semelhante ou superior após a migração para vCore para as cargas de trabalho que exigem um cache de dados de memória grande a fim de obter desempenho suficiente ou para cargas de trabalho que exigem grandes concessões de memória para o processamento de consultas. Para essas cargas de trabalho, dependendo do desempenho real, talvez seja necessário aumentar o número de vCores para obter uma memória total suficiente.
- A [utilização de recursos históricos](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) do banco de dados de DTU deve ser considerada na escolha do objetivo de serviço de vCore. Os bancos de dados de DTU com recursos de CPU consistentemente subutilizados podem precisar de menos vCores do que o número retornado pela consulta de mapeamento. Por outro lado, os bancos de dados de DTU em que uma utilização de CPU consistentemente alta causa um desempenho inadequado da carga de trabalho pode exigir mais vCores do que o retornado pela consulta.
- Se estiver migrando bancos de dados com padrões de uso intermitentes ou imprevisíveis, considere o uso da camada de computação [sem servidor](serverless-tier-overview.md). Observe que o número máximo de trabalhos simultâneos (solicitações) na camada sem servidor é de 75% do limite na computação provisionada para o mesmo número máximo de vCores configurado. Além disso, a memória máxima disponível na camada sem servidor é de 3 GB vezes o número máximo de vCores configurado: por exemplo, a memória máxima é de 120 GB quando, no máximo, 40 vCores são configurados.   
- No modelo de vCore, o tamanho máximo de banco de dados com suporte pode ser diferente dependendo da geração de hardware. Para bancos de dados grandes, verifique os tamanhos máximos com suporte no modelo de vCore para [bancos de dados individuais](resource-limits-vcore-single-databases.md) e [pools elásticos](resource-limits-vcore-elastic-pools.md).
- Para pools elásticos, os modelos de [DTU](resource-limits-dtu-elastic-pools.md) e [vCore](resource-limits-vcore-elastic-pools.md) têm diferenças no número máximo de bancos de dados com suporte por pool. Isso deve ser considerado ao migrar pools elásticos com muitos bancos de dados.
- Algumas gerações de hardware podem não estar disponíveis em todas as regiões. Verifique a disponibilidade em [Gerações de hardware](service-tiers-vcore.md#hardware-generations).

> [!IMPORTANT]
> As diretrizes de dimensionamento de DTU para vCore acima são fornecidas para ajudar na estimativa inicial do objetivo de serviço do banco de dados de destino.
>
> A configuração ideal do banco de dados de destino é dependente da carga de trabalho. Assim, para obter a relação ideal de preço/desempenho após a migração, talvez seja necessário aproveitar a flexibilidade do modelo de vCore para ajustar o número de vCores, a [geração de hardware](service-tiers-vcore.md#hardware-generations), as camadas de [serviço](service-tiers-vcore.md#service-tiers) e de [computação](service-tiers-vcore.md#compute-tiers), bem como o ajuste de outros parâmetros de configuração de banco de dados, como o [grau máximo de paralelismo](/sql/relational-databases/query-processing-architecture-guide#parallel-query-processing).
> 

### <a name="dtu-to-vcore-migration-examples"></a>Exemplos de migração de DTU para vCore

> [!NOTE]
> Os valores mostrados nos exemplos abaixo são fornecidos apenas para fins ilustrativos. Os valores reais retornados nos cenários descritos podem ser diferentes.
> 

**Como migrar um banco de dados S9 Standard**

A consulta de mapeamento retorna o seguinte resultado (algumas colunas não são mostradas para fins de brevidade):

|dtu_logical_cpus|dtu_hardware_gen|dtu_memory_per_core_gb|Gen4_vcores|Gen4_memory_per_core_gb|Gen5_vcores|Gen5_memory_per_core_gb|
|----------------|----------------|----------------------|-----------|-----------------------|-----------|-----------------------|
|24,00|Gen5|5,40|16,800|7|24,000|5,05|

Vemos que o banco de dados de DTU tem 24 CPUs lógicas (vCores), com 5,4 GB de memória por vCore, e está usando o hardware Gen5. A correspondência direta com ele é um banco de dados de 24 vCores de Uso Geral com o hardware Gen5, ou seja, o objetivo de serviço de vCore **GP_Gen5_24**.

**Como migrar um banco de dados S0 Standard**

A consulta de mapeamento retorna o seguinte resultado (algumas colunas não são mostradas para fins de brevidade):

|dtu_logical_cpus|dtu_hardware_gen|dtu_memory_per_core_gb|Gen4_vcores|Gen4_memory_per_core_gb|Gen5_vcores|Gen5_memory_per_core_gb|
|----------------|----------------|----------------------|-----------|-----------------------|-----------|-----------------------|
|0,25|Gen4|0,42|0,250|7|0,425|5,05|

Vemos que o banco de dados de DTU tem o equivalente a 0,25 CPUs lógicas (vCores), com 0,42 GB de memória por vCore, e está usando o hardware Gen4. Os menores objetivos de serviço de vCore nas gerações de hardware Gen4 e Gen5, **GP_Gen4_1** e **GP_Gen5_2**, fornecem mais recursos de computação do que o banco de dados S0 Standard. Portanto, uma correspondência direta não é possível. Como o hardware Gen4 está sendo [desativado](https://azure.microsoft.com/updates/gen-4-hardware-on-azure-sql-database-approaching-end-of-life-in-2020/), a opção **GP_Gen5_2** é preferencial. Além disso, se a carga de trabalho for adequada à camada de computação [Sem servidor](serverless-tier-overview.md), **GP_S_Gen5_1** será uma correspondência mais próxima.

**Como migrar um banco de dados P15 Premium**

A consulta de mapeamento retorna o seguinte resultado (algumas colunas não são mostradas para fins de brevidade):

|dtu_logical_cpus|dtu_hardware_gen|dtu_memory_per_core_gb|Gen4_vcores|Gen4_memory_per_core_gb|Gen5_vcores|Gen5_memory_per_core_gb|
|----------------|----------------|----------------------|-----------|-----------------------|-----------|-----------------------|
|42,00|Gen5|4,86|29,400|7|42,000|5,05|

Vemos que o banco de dados de DTU tem 42 CPUs lógicas (vCores), com 4,86 GB de memória por vCore, e está usando o hardware Gen5. Embora não haja um objetivo de serviço de vCore com 42 núcleos, o objetivo de serviço **BC_Gen5_40** é muito próximo em termos de capacidade da CPU e memória e é uma boa correspondência.

**Como migrar um pool elástico Básico de 200 eDTUs**

A consulta de mapeamento retorna o seguinte resultado (algumas colunas não são mostradas para fins de brevidade):

|dtu_logical_cpus|dtu_hardware_gen|dtu_memory_per_core_gb|Gen4_vcores|Gen4_memory_per_core_gb|Gen5_vcores|Gen5_memory_per_core_gb|
|----------------|----------------|----------------------|-----------|-----------------------|-----------|-----------------------|
|4,00|Gen5|5,40|2,800|7|4,000|5,05|

Vemos que o pool elástico de DTU tem 4 CPUs lógicas (vCores), com 5,4 GB de memória por vCore, e está usando o hardware Gen5. A correspondência direta no modelo de vCore é um pool elástico **GP_Gen5_4**. No entanto, esse objetivo de serviço dá suporte a, no máximo, 200 bancos de dados por pool, enquanto o pool elástico Básico de 200 eDTUs dá suporte a até 500 bancos de dados. Se o pool elástico a ser migrado tiver mais de 200 bancos de dados, o objetivo de serviço de vCore correspondente precisará ser **GP_Gen5_6**, que dá suporte a até 500 bancos de dados.

## <a name="migrate-geo-replicated-databases"></a>Migrar bancos de dados com replicação geográfica

A migração do modelo baseado em DTU para o modelo de compra baseado em vCore é semelhante ao upgrade ou ao downgrade das relações de replicação geográfica entre bancos de dados nas camadas de serviço Standard e Premium. Durante a migração, você não precisa parar a replicação geográfica, mas precisa seguir estas regras de sequenciamento:

- Ao atualizar, será necessário primeiro fazer upgrade do banco de dados secundário e, em seguida, upgrade do primário.
- Ao fazer downgrade, inverta a ordem: primeiro, você deverá fazer downgrade do banco de dados primário e, em seguida, fazer downgrade do secundário.

Quando você estiver usando a replicação geográfica entre dois pools elásticos, recomendaremos que você designe um pool como o primário e o outro como o secundário. Nesse caso, quando estiver migrando pools elásticos, use as mesmas diretrizes de sequenciamento. No entanto, se você tiver pools elásticos que contenham bancos de dados primários e secundários, trate o pool com a utilização mais alta como o primário e siga as regras de sequenciamento de acordo.  

A seguinte tabela fornece diretrizes para cenários de migração específicos:

|Camada de serviço atual|Camada de serviço de destino|Tipo de migração|Ações do usuário|
|---|---|---|---|
|Standard|Propósito geral|Lateral|Pode migrar em qualquer ordem, mas precisa garantir o dimensionamento apropriado de vCore, conforme descrito acima|
|Premium|Comercialmente crítico|Lateral|Pode migrar em qualquer ordem, mas precisa garantir o dimensionamento apropriado de vCore, conforme descrito acima|
|Standard|Comercialmente crítico|Atualizar|Deve migrar primeiro o secundário|
|Comercialmente crítico|Standard|Downgrade|Deve migrar primeiro o primário|
|Premium|Propósito geral|Downgrade|Deve migrar primeiro o primário|
|Propósito geral|Premium|Atualizar|Deve migrar primeiro o secundário|
|Comercialmente crítico|Propósito geral|Downgrade|Deve migrar primeiro o primário|
|Propósito geral|Comercialmente crítico|Atualizar|Deve migrar primeiro o secundário|
||||

## <a name="migrate-failover-groups"></a>Migrar grupos de failover

A migração de grupos de failover com vários bancos de dados requer a migração individual dos bancos de dados primário e secundário. Durante esse processo, as mesmas considerações e regras de sequenciamento se aplicam. Depois que os bancos de dados forem convertidos para o modelo de compra baseado em vCore, o grupo de failover permanecerá em vigor com as mesmas configurações de política.

### <a name="create-a-geo-replication-secondary-database"></a>Criar um banco de dados secundário com replicação geográfica

Você só pode criar um banco de dados secundário com replicação geográfica (um secundário geográfico) usando a mesma camada de serviço usada para o banco de dados primário. Para bancos de dados com uma alta taxa de geração de logs, recomendamos criar o secundário geográfico com o mesmo tamanho da computação do primário.

Se estiver criando um secundário geográfico no pool elástico para um banco de dados primário individual, verifique se a `maxVCore` configuração do pool corresponde ao tamanho da computação do banco de dados primário. Se você estiver criando um secundário geográfico no pool elástico para um primário em outro pool elástico, recomendamos que os pools tenham as mesmas configurações de `maxVCore`.

## <a name="use-database-copy-to-migrate-from-dtu-to-vcore"></a>Usar a cópia de banco de dados para a migração de DTU para vCore

É possível copiar qualquer banco de dados com um tamanho da computação baseado em DTU para um banco de dados com um tamanho da computação baseado em vCore sem restrições ou sequenciamento especial, desde que o tamanho da computação de destino dê suporte ao tamanho máximo do banco de dados de origem. A cópia de banco de dados cria um instantâneo dos dados desde a hora de início da operação de cópia e não sincroniza os dados entre a origem e o destino.

## <a name="next-steps"></a>Próximas etapas

- Para obter os tamanhos da computação específicos e as opções de tamanho de armazenamento disponíveis para bancos de dados individuais, confira [Limites de recursos baseados em vCore do Banco de Dados SQL para bancos de dados individuais](resource-limits-vcore-single-databases.md).
- Para obter os tamanhos da computação específicos e as opções de tamanho de armazenamento disponíveis para pools elásticos, confira [Limites de recursos baseados em vCore do Banco de Dados SQL para pools elásticos](resource-limits-vcore-elastic-pools.md).