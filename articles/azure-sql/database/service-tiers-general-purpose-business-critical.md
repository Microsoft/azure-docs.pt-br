---
title: Camadas de serviço de uso geral e crítico para os negócios
titleSuffix: Azure SQL Database & SQL Managed Instance
description: O artigo discute as camadas de serviço de uso geral e crítico para os negócios no modelo de compra baseado em vCore usado pelo banco de dados SQL do Azure e pelo Azure SQL Instância Gerenciada.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake
ms.date: 12/14/2020
ms.openlocfilehash: b5a30846a6e2aaf85ded2e55641aa5fba9507a29
ms.sourcegitcommit: 16887168729120399e6ffb6f53a92fde17889451
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/13/2021
ms.locfileid: "98165766"
---
# <a name="azure-sql-database-and-azure-sql-managed-instance-service-tiers"></a>Banco de dados SQL do Azure e camadas de serviço do Azure SQL Instância Gerenciada
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

O banco de dados SQL do Azure e o Azure SQL Instância Gerenciada são baseados na arquitetura do mecanismo de banco de dados SQL Server ajustada para o ambiente de nuvem para garantir a disponibilidade de 99,99%, mesmo se houver uma falha de infraestrutura. Duas camadas de serviço são usadas pelo banco de dados SQL do Azure e pelo Azure SQL Instância Gerenciada, cada uma com um modelo de arquitetura diferente. Essas camadas de serviço são:

- [Uso geral](service-tier-general-purpose.md), que é projetado para cargas de trabalho orientadas a orçamento.
- [Comercialmente crítico](service-tier-business-critical.md), projetado para cargas de trabalho de baixa latência com alta resiliência a falhas e failovers rápidos.

O banco de dados SQL do Azure tem uma camada de serviço adicional: 

- [Hiperescala](service-tier-hyperscale.md), projetada para a maioria das cargas de trabalho de negócios, fornecendo armazenamento altamente escalonável, expansão de leitura e recursos de restauração rápida de banco de dados.

Este artigo discute as diferenças entre os níveis de serviço, o armazenamento e as considerações de backup para as camadas de serviço de uso geral e crítico para os negócios no modelo de compra baseado em vCore.

## <a name="service-tier-comparison"></a>Comparação da camada de serviço

A tabela a seguir descreve as principais diferenças entre as camadas de serviço para a geração mais recente (Gen5). Observe que as características da camada de serviço podem ser diferentes no banco de dados SQL e no SQL Instância Gerenciada.

|-| Tipo de recurso | Uso Geral |  Hiperescala | Comercialmente Crítico |
|:---:|:---:|:---:|:---:|:---:|
| **Mais adequado para** | |  Oferece opções equilibradas de computação e armazenamento orientadas ao orçamento. | A maioria das cargas de trabalho comerciais. Dimensionamento automático do tamanho de armazenamento de até 100 TB, dimensionamento vertical e horizontal de computação de fluxo, restauração rápida de banco de dados. | Aplicativos OLTP com alta taxa de transação e baixa latência de e/s. Oferece maior resiliência a falhas e failovers rápidos usando várias réplicas atualizadas de forma síncrona.|
|  **Disponível no tipo de recurso:** ||Banco de dados SQL/SQL Instância Gerenciada | Banco de dados SQL do Azure individual | Banco de dados SQL/SQL Instância Gerenciada |
| **Tamanho da computação**| Banco de Dados SQL | 1 a 80 vCores | 1 a 80 vCores | 1 a 80 vCores |
| | Instância Gerenciada de SQL | 4, 8, 16, 24, 32, 40, 64, 80 vCores | N/D | 4, 8, 16, 24, 32, 40, 64, 80 vCores |
| | Pools de Instância Gerenciada do SQL | 2, 4, 8, 16, 24, 32, 40, 64, 80 vCores | N/D | N/D |
| **Tipo de armazenamento** | Tudo | Armazenamento remoto Premium (por instância) | Armazenamento desacoplado com cache SSD local (por instância) | Armazenamento SSD local super rápido (por instância) |
| **Tamanho do banco de dados** | Banco de Dados SQL | 5 GB – 4 TB | Até 100 TB | 5 GB – 4 TB |
| | Instância Gerenciada de SQL  | 32 GB A 8 TB | N/D | 32 GB – 4 TB |
| **Tamanho de armazenamento** | Banco de Dados SQL | 5 GB – 4 TB | Até 100 TB | 5 GB – 4 TB |
| | Instância Gerenciada de SQL  | 32 GB A 8 TB | N/D | 32 GB – 4 TB |
| **Tamanho do TempDB** | Banco de Dados SQL | [32 GB por vCore](resource-limits-vcore-single-databases.md#general-purpose---provisioned-compute---gen4) | [32 GB por vCore](resource-limits-vcore-single-databases.md#hyperscale---provisioned-compute---gen5) | [32 GB por vCore](resource-limits-vcore-single-databases.md#business-critical---provisioned-compute---gen4) |
| | Instância Gerenciada de SQL  | [24 GB por vCore](../managed-instance/resource-limits.md#service-tier-characteristics) | N/D | Até 4 TB- [limitado pelo tamanho do armazenamento](../managed-instance/resource-limits.md#service-tier-characteristics) |
| **Taxa de transferência de gravação de log** | Banco de Dados SQL | [1,875 MB/s por vCore (máximo de 30 MB/s)](resource-limits-vcore-single-databases.md#general-purpose---provisioned-compute---gen4) | 100 MB/s | [6 MB/s por vCore (máx. 96 MB/s)](resource-limits-vcore-single-databases.md#business-critical---provisioned-compute---gen4) |
| | Instância Gerenciada de SQL | [3 MB/s por vCore (máximo de 22 MB/s)](../managed-instance/resource-limits.md#service-tier-characteristics) | N/D | [4 MB/s por VCORE (máx. 48 MB/s)](../managed-instance/resource-limits.md#service-tier-characteristics) |
|**Disponibilidade**|Tudo| 99,99% |  [99,95% com uma réplica secundária, 99,99% com mais réplicas](service-tier-hyperscale-frequently-asked-questions-faq.md#what-slas-are-provided-for-a-hyperscale-database) | 99,99% <br/> [99,995% com Banco de dados individual com redundância de zona](https://azure.microsoft.com/blog/understanding-and-leveraging-azure-sql-database-sla/) |
|**Backups**|Tudo|RA-GRS, 7-35 dias (7 dias por padrão). A retenção máxima para a camada básica é de 7 dias. | RA-GRS, 7 dias, tempo constante de recuperação point-in-time (PITR) | RA-GRS, 7-35 dias (7 dias por padrão) |
|**OLTP na memória** | | N/D | N/D | Disponível |
|**Réplicas somente leitura**| | 0 interno <br> 0-4 usando [a replicação geográfica](active-geo-replication-overview.md) | 0-4 interno | 1 interno, incluído no preço <br> 0-4 usando [a replicação geográfica](active-geo-replication-overview.md) |
|**Preço/cobrança** | Banco de Dados SQL | [vCore, armazenamento reservado e armazenamento de backup](https://azure.microsoft.com/pricing/details/sql-database/single/) são cobrados. <br/>O IOPS não é cobrado. | [vCore para cada réplica e armazenamento usado](https://azure.microsoft.com/pricing/details/sql-database/single/) são cobrados. <br/>IOPS ainda não cobrado. | [vCore, armazenamento reservado e armazenamento de backup](https://azure.microsoft.com/pricing/details/sql-database/single/) são cobrados. <br/>O IOPS não é cobrado. |
|| Instância Gerenciada de SQL | [vCore, armazenamento reservado e armazenamento de backup](https://azure.microsoft.com/pricing/details/sql-database/managed/) é cobrado. <br/>O IOPS não é cobrado| N/D | [vCore, armazenamento reservado e armazenamento de backup](https://azure.microsoft.com/pricing/details/sql-database/managed/) é cobrado. <br/>O IOPS não é cobrado.| 
|**Modelos de desconto**| | [Instâncias reservadas](reserved-capacity-overview.md)<br/>[Benefício híbrido do Azure](../azure-hybrid-benefit.md) (não disponível em assinaturas de desenvolvimento/teste)<br/>Assinaturas de desenvolvimento/teste [Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/) e [pago conforme o uso](https://azure.microsoft.com/offers/ms-azr-0023p/)| [Benefício híbrido do Azure](../azure-hybrid-benefit.md) (não disponível em assinaturas de desenvolvimento/teste)<br/>Assinaturas de desenvolvimento/teste [Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/) e [pago conforme o uso](https://azure.microsoft.com/offers/ms-azr-0023p/)| [Instâncias reservadas](reserved-capacity-overview.md)<br/>[Benefício híbrido do Azure](../azure-hybrid-benefit.md) (não disponível em assinaturas de desenvolvimento/teste)<br/>Assinaturas de desenvolvimento/teste [Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/) e [pago conforme o uso](https://azure.microsoft.com/offers/ms-azr-0023p/)|

Para obter mais informações, consulte as diferenças detalhadas entre as camadas de serviço no [banco de dados SQL do Azure (vCore)](resource-limits-vcore-single-databases.md), [um único DTU (banco de dados SQL do Azure)](resource-limits-dtu-single-databases.md), [DTU (banco de dados SQL) do Azure em pool](resource-limits-dtu-single-databases.md)e páginas [do Azure SQL instância gerenciada](../managed-instance/resource-limits.md) .

> [!NOTE]
> Para obter informações sobre a camada de serviço de hiperescala no modelo de compra baseado em vCore, consulte [camada de serviço de hiperescala](service-tier-hyperscale.md). Para obter uma comparação do modelo de compra baseado em vCore com o modelo de compra baseado em DTU, consulte [comprando modelos e recursos](purchasing-models.md).

## <a name="data-and-log-storage"></a>Armazenamento de dados e de log

Os seguintes fatores afetam a quantidade de armazenamento usada para arquivos de dados e de log e aplicam-se a Uso Geral e Comercialmente Crítico. Para obter detalhes sobre o armazenamento de dados e de log em hiperescala, consulte [camada de serviço de hiperescala](service-tier-hyperscale.md).

- O armazenamento alocado é usado por arquivos de dados (MDF) e arquivos de log (LDF).
- Cada tamanho de computação de banco de dados individual dá suporte a um tamanho máximo de banco de dados, com um tamanho máximo padrão de 32 GB.
- Quando você configura o tamanho necessário do banco de dados individual (o tamanho do arquivo MDF), 30% mais armazenamento adicional é adicionado automaticamente para dar suporte a arquivos LDF.
- Você pode selecionar qualquer tamanho de banco de dados individual entre 10 GB e o máximo com suporte.
  - Para armazenamento nas camadas de serviço padrão ou de uso geral, aumente ou diminua o tamanho em incrementos de 10 GB.
  - Para armazenamento nas camadas de serviço Premium ou comercialmente crítico, aumente ou diminua o tamanho em incrementos de 250 GB.
- Na camada de serviço de uso geral, o `tempdb` usa um SSD anexado e esse custo de armazenamento é incluído no preço vCore.
- Na camada de serviço comercialmente crítico, `tempdb` o compartilha o SSD anexado com os arquivos MDF e ldf, e o `tempdb` custo de armazenamento é incluído no preço vCore.
- Na camada de serviço do DTU Premium, `tempdb` o compartilha o SSD anexado com arquivos MDF e ldf.
- O tamanho do armazenamento para um Instância Gerenciada SQL deve ser especificado em múltiplos de 32 GB.


> [!IMPORTANT]
> Você é cobrado pelo armazenamento total alocado para arquivos MDF e LDF.

Para monitorar o tamanho total atual de seus arquivos MDF e LDF, use [sp_spaceused](/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql). Para monitorar o tamanho atual dos arquivos MDF e LDF individuais, use [sys.database_files](/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql).

> [!IMPORTANT]
> Em algumas circunstâncias, talvez seja necessário reduzir um banco de dados para recuperar o espaço não utilizado. Para obter mais informações, consulte [gerenciar o espaço de arquivo no banco de dados SQL do Azure](file-space-manage.md).

## <a name="backups-and-storage"></a>Backups e armazenamento

O armazenamento de backups de banco de dados é alocado para dar suporte a recursos de PITR (restauração pontual) e [EPD (retenção de longo prazo)](long-term-retention-overview.md) do banco de dados SQL e do SQL instância gerenciada. Esse armazenamento é alocado separadamente para cada banco de dados e faturado como duas cobranças separadas por banco de dados.

- **PITR**: os backups de banco de dados individuais são copiados para o [armazenamento com redundância geográfica com acesso de leitura (ra-grs)](../../storage/common/geo-redundant-design.md) automaticamente. O tamanho do armazenamento aumenta dinamicamente à medida que novos backups são criados. O armazenamento é usado por backups completos semanais, backups diferenciais diários e backups de log de transações, que são copiados a cada 5 minutos. O consumo de armazenamento depende da taxa de alteração do banco de dados e do período de retenção para backups. É possível configurar um período de retenção separado para cada banco de dados entre 7 e 35 dias. Um valor mínimo de armazenamento igual a 100 por cento (1x) do tamanho do banco de dados é fornecido sem custo adicional. Para a maioria dos bancos de dados, esse valor é suficiente para armazenar 7 dias de backups.
- **EPD**: você também tem a opção de configurar a retenção de longo prazo de backups completos por até 10 anos (esse recurso está em [Visualização pública limitada para SQL instância gerenciada](long-term-retention-overview.md#sql-managed-instance-support). Se você configurar uma política EPD, esses backups serão armazenados automaticamente no armazenamento RA-GRS, mas você poderá controlar a frequência com que os backups são copiados. Para atender aos diferentes requisitos de conformidade, você pode selecionar períodos de retenção diferentes para backups semanais, mensais e/ou anuais. A configuração escolhida determina a quantidade de armazenamento que será usada para backups EPD. Para estimar o custo do armazenamento EPD, você pode usar a calculadora de preços EPD. Para obter mais informações, consulte [retenção de longo prazo do banco de dados SQL](long-term-retention-overview.md).

## <a name="next-steps"></a>Próximas etapas

Para obter detalhes sobre os tamanhos de computação e armazenamento específicos disponíveis nas camadas de serviço de uso geral e crítico para os negócios, consulte: 

- [limites de recursos baseados em vCore para o banco de dados SQL do Azure](resource-limits-vcore-single-databases.md).
- [limites de recursos baseados em vCore para bancos de dados em pool no banco de dados SQL do Azure](resource-limits-vcore-elastic-pools.md).
- [limites de recursos baseados em vCore para o Azure SQL instância gerenciada](../managed-instance/resource-limits.md).
