---
title: Serviço do Banco de Dados SQL do Azure | Microsoft Docs
description: O modelo de compra baseado em vCore permite dimensionar os recursos de computação e armazenamento independentemente, combine o desempenho local e otimizar o preço.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
manager: craigg
ms.date: 06/26/2019
ms.openlocfilehash: e9d1ce3bcd3bf958be0a7837e8416300af03f5a2
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449742"
---
# <a name="choose-among-the-vcore-service-tiers-and-migrate-from-the-dtu-service-tiers"></a>Escolha entre as camadas de serviço vCore e migrar das camadas de serviço DTU

O vCore (núcleo virtual)-modelo de compra baseado em permite dimensionar os recursos de computação e armazenamento independentemente, combine o desempenho local e otimizar o preço. Ele também permite que você escolha a geração de hardware:

- **Gen4**: Até 24 CPUs lógicas com base em Intel E5-2673 v3 (Haswell) 2,4 GHz, vCore = 1 PP (núcleo físico), 7 GB por núcleo, anexado SSD
- **Gen5**: Até 80 CPUs lógicas com base em Intel E5-2673 v4 (Broadwell) 2,3 GHz, vCore = 1 LP (hyper-thread), 5.1 GB por núcleo, SSD de eNVM rápido

Hardware Ger 4 oferece substancialmente mais memória por vCore. No entanto, o hardware Gen5 permite que você amplie recursos de computação muito maiores.

> [!IMPORTANT]
> Não há suporte para novos bancos de dados Gen4 na região AustraliaEast.
> [!NOTE]
> Para obter informações sobre as camadas de serviço baseado em DTU, consulte [camadas para o modelo de compra baseado em DTU de serviço](sql-database-service-tiers-dtu.md). Para obter informações sobre as diferenças entre as camadas de serviço para o baseado em DTU e modelos de compra baseado em vCore, consulte [modelos de compra do Azure SQL Database](sql-database-purchase-models.md).

## <a name="service-tier-characteristics"></a>Características da camada de serviço

O modelo de compra baseado em vCore oferece três camadas de serviço: uso geral, hiperescala e comercialmente crítico. Essas camadas de serviço são diferenciadas por uma variedade de tamanhos de computação, designs de alta disponibilidade, os métodos de isolamento de falhas, tipos e tamanhos de armazenamento e os intervalos de e/s.

Configure separadamente o período necessário de armazenamento e retenção para backups. Para definir o período de retenção de backup, abra o portal do Azure, vá para o servidor (não o banco de dados) e, em seguida, vá para **Gerenciar Backups** > **configurar política**  >   **Ponto no tempo de restauração da configuração** > **7-35 dias**.

A tabela a seguir explica as diferenças entre as três camadas:

||**Propósito geral**|**Comercialmente crítico**|**Em hiperescala**|
|---|---|---|---|
|Mais adequado para|A maioria das cargas de trabalho comerciais. Ofertas orientadas a orçamento, computação balanceada e escalonável e opções de armazenamento.|Aplicativos de negócios com altos requisitos de e/s. Oferece maior resiliência a falhas usando várias réplicas isoladas.|A maioria das cargas de trabalho com requisitos de escala de leitura e de armazenamento altamente escalonável.|
|Computação|**Provisionado computação**:<br/>Gen4: vCores de 1 a 24<br/>Gen5: 2 a 80 vCores<br/>**Computação sem servidor**:<br/>Gen5: 0,5 - 4 vCores|**Provisionado computação**:<br/>Gen4: vCores de 1 a 24<br/>Gen5: 2 a 80 vCores|**Provisionado computação**:<br/>Gen4: vCores de 1 a 24<br/>Gen5: 2 a 80 vCores|
|Memória|**Provisionado computação**:<br/>Gen4: 7 GB por vCore<br/>Gen5: 5.1 GB por vCore<br/>**Computação sem servidor**:<br/>Gen5: 3 GB por vCore|**Provisionado computação**:<br/>Gen4: 7 GB por vCore<br/>Gen5: 5.1 GB por vCore |**Provisionado computação**:<br/>Gen4: 7 GB por vCore<br/>Gen5: 5.1 GB por vCore|
|Armazenamento|Usa o armazenamento remoto.<br/>**Banco de dados único provisionado computação**:<br/>5 GB – 4 TB<br/>**Computação sem servidor de banco de dados único**:<br/>5 GB – 1 TB<br/>**A instância gerenciada**: 32 GB - 8 TB |Usa o armazenamento SSD local.<br/>**Banco de dados único provisionado computação**:<br/>5 GB – 4 TB<br/>**A instância gerenciada**:<br/>32 GB - 4 TB |Aumento automático flexível de armazenamento conforme necessário. Dá suporte a até 100 TB de armazenamento. Usa o armazenamento SSD local para o cache local do pool de buffers e armazenamento de dados local. Usa o armazenamento remoto do Azure como armazenamento de dados de longo prazo final. |
|Taxa de transferência de e/s (aproximada)|**Banco de dados único**: 500 IOPS por vCore com 7000 IOPS máximo.<br/>**A instância gerenciada**: Depende [tamanho do arquivo de](../virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes).|5000 IOPS por núcleo com máximo de 200.000 IOPS|Hiperescala é uma arquitetura de várias camadas com o cache em vários níveis. IOPs efetivas dependerá da carga de trabalho.|
|Disponibilidade|1 réplica, nenhuma réplica de escala de leitura|3 réplicas, 1 [réplica em escala de leitura](sql-database-read-scale-out.md),<br/>com redundância de zona de alta disponibilidade (HA)|1 réplica de leitura / gravação, além de 0 a 4 [réplicas de escala de leitura](sql-database-read-scale-out.md)|
|Backups|[Armazenamento com redundância geográfica de acesso de leitura (RA-GRS)](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7-35 dias (7 dias por padrão)|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7-35 dias (7 dias por padrão)|Backups baseados em instantâneo no armazenamento remoto do Azure. As restaurações usam esses instantâneos para recuperação rápida. Backups são instantâneos e não afetam o desempenho de e/s de computação. As restaurações são rápidas e não são uma operação de tamanho de dados (levando minutos em vez de horas ou dias).|
|Na memória|Sem suporte|Suportado|Sem suporte|
|||

> [!NOTE]
> Você pode obter um banco de dados SQL do Azure gratuito na camada de serviço básico em conjunto com uma conta gratuita do Azure. Para obter mais informações, consulte [criar um banco de dados gerenciado na nuvem com sua conta gratuita do Azure](https://azure.microsoft.com/free/services/sql-database/).

- Para obter mais informações sobre limites de recursos de vCore, consulte [vCore limites de recursos em um único banco de dados](sql-database-vcore-resource-limits-single-databases.md) e [vCore limites de recursos em uma instância gerenciada](sql-database-managed-instance.md#vcore-based-purchasing-model).
- Para obter mais informações sobre o uso geral e as camadas de serviço críticos de negócios, consulte [camadas de serviço de uso geral e comercialmente crítico](sql-database-service-tiers-general-purpose-business-critical.md).
- Para obter mais informações sobre a camada de serviço em hiperescala no modelo de compra baseado em vCore, consulte [camada de serviço em hiperescala](sql-database-service-tier-hyperscale.md).  

## <a name="azure-hybrid-benefit"></a>Benefício Híbrido do Azure

Na camada de computação provisionada do modelo de compra baseado em vCore, é possível trocar suas licenças existentes por tarifas com desconto no banco de dados SQL usando [o benefício híbrido do Azure para SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/). Esse benefício do Azure permite que você economize até 30 por cento no banco de dados SQL usando suas licenças do SQL Server local com o Software Assurance.

![preços](./media/sql-database-service-tiers/pricing.png)

Com o benefício híbrido do Azure, você pode optar por pagar apenas pela infraestrutura subjacente do Azure usando sua licença existente do SQL Server para o mecanismo de banco de dados SQL em si (preço de computação Base) ou você pode pagar para a infraestrutura subjacente e o SQL Server licença (licença inclusa preço).

Você pode escolher ou alterar seu modelo de licenciamento por meio do portal do Azure ou usando uma das seguintes APIs:

- Para definir ou atualizar o tipo de licença por meio do PowerShell:

  - [New-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase)
  - [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)
  - [New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)
  - [Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)

- Para definir ou atualizar o tipo de licença usando a CLI do Azure:

  - [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create)
  - [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update)
  - [az sql mi create](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create)
  - [az sql mi update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)

- Para definir ou atualizar o tipo de licença por meio da API REST:

  - [Banco de Dados – Criar ou Atualizar](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)
  - [Bancos de Dados – Atualizar](https://docs.microsoft.com/rest/api/sql/databases/update)
  - [Managed Instances - Create Or Update](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)
  - [Managed Instances - Update](https://docs.microsoft.com/rest/api/sql/managedinstances/update)

## <a name="migrate-from-the-dtu-based-model-to-the-vcore-based-model"></a>Migrar do modelo baseado em DTU para o modelo baseado em vCore

### <a name="migrate-a-database"></a>Migrar um banco de dados

Migrando um banco de dados do modelo de compra baseado em DTU para o modelo de compra baseado em vCore é semelhante ao upgrade ou downgrade entre as camadas de serviço standard e premium no modelo de compra baseado em DTU.

### <a name="migrate-databases-with-geo-replication-links"></a>Migrar bancos de dados com links de replicação geográfica

Migração do modelo baseado em DTU para o modelo de compra baseado em vCore é semelhante ao upgrade ou downgrade de relacionamentos de replicação geográfica entre bancos de dados nas camadas de serviço standard e premium. Durante a migração, você não precisa parar a replicação geográfica, mas você deve seguir estas regras de sequenciamento:

- Ao atualizar, será necessário primeiro fazer upgrade do banco de dados secundário e, em seguida, upgrade do primário.
- Ao fazer downgrade, inverta a ordem: primeiro, você deverá fazer downgrade do banco de dados primário e, em seguida, fazer downgrade do secundário.

Quando você estiver usando replicação geográfica entre dois pools Elásticos, é recomendável que você designa um pool como primário e o outro como secundário. Nesse caso, quando você estiver migrando pools Elásticos, você deve usar a mesma orientação do sequenciamento. No entanto, se você tiver pools Elásticos que contêm bancos de dados primários e secundários, tratar o pool com maior utilização como primário e siga as regras de sequenciamento adequadamente.  

A tabela a seguir fornece orientação para cenários de migração específicos:

|Camada de serviço atual|Camada de serviço de destino|Tipo de migração|Ações do usuário|
|---|---|---|---|
|Standard|Propósito geral|Lateral|Pode migrar em qualquer ordem, mas precisa garantir o dimensionamento apropriado de vCore*|
|Premium|Comercialmente crítico|Lateral|Pode migrar em qualquer ordem, mas precisa garantir o dimensionamento apropriado de vCore*|
|Standard|Comercialmente crítico|Atualizar|Deve migrar primeiro o secundário|
|Comercialmente crítico|Standard|Downgrade|Deve migrar primeiro o primário|
|Premium|Propósito geral|Downgrade|Deve migrar primeiro o primário|
|Propósito geral|Premium|Atualizar|Deve migrar primeiro o secundário|
|Comercialmente crítico|Propósito geral|Downgrade|Deve migrar primeiro o primário|
|Propósito geral|Comercialmente crítico|Atualizar|Deve migrar primeiro o secundário|
||||

\* Cada 100 DTUs na camada standard requerem pelo menos 1 vCore e cada 125 DTUs na camada premium requerem pelo menos 1 vCore.

### <a name="migrate-failover-groups"></a>Migrar grupos de failover

A migração de grupos de failover com vários bancos de dados requer a migração individual dos bancos de dados primário e secundário. Durante esse processo, as mesmas considerações e regras de sequenciamento se aplicam. Depois que os bancos de dados são convertidos para o modelo de compra baseado em vCore, o grupo de failover permanecerá em vigor com as mesmas configurações de política.

### <a name="create-a-geo-replication-secondary-database"></a>Criar um banco de dados secundário de replicação geográfica

Você pode criar um banco de dados secundário de replicação geográfica (um secundário geográfico) apenas usando a mesma camada de serviço que você usou para o banco de dados primário. Para bancos de dados com uma taxa alta de geração de log, é recomendável criar o geograficamente secundário com o mesmo tamanho de computação que o primário.

Se você estiver criando um secundário geográfico no pool Elástico para um único banco de dados primário, verifique se o `maxVCore` configuração para o pool corresponde ao tamanho de computação do banco de dados primário. Se você estiver criando um secundário geográfico para um primário em outro pool Elástico, é recomendável que os pools têm a mesma `maxVCore` configurações.

### <a name="use-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>Usar a cópia de banco de dados para converter um banco de dados baseado em DTU para um banco de dados baseado em vCore

É possível copiar qualquer banco de dados com um tamanho da computação baseado em DTU para um banco de dados com um tamanho da computação baseado em vCore sem restrições ou sequenciamento especial, desde que o tamanho da computação de destino dê suporte ao tamanho máximo do banco de dados de origem. A cópia de banco de dados cria um instantâneo dos dados a partir da hora de início da operação de cópia e não sincronizar dados entre a origem e destino.

## <a name="next-steps"></a>Próximas etapas

- Para os tamanhos de computação específicos e opções de tamanho de armazenamento disponíveis para bancos de dados individuais, consulte [limites de recurso de baseado em vCore do banco de dados SQL para bancos de dados únicos](sql-database-vcore-resource-limits-single-databases.md).
- Para os tamanhos de computação específicos e opções de tamanho de armazenamento disponíveis para pools Elásticos, consulte [limites de recurso baseado em vCore do banco de dados SQL para pools Elásticos](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose-service-tier-storage-sizes-and-compute-sizes).
