---
title: Limites de recursos do Banco de Dados SQL do Azure | Microsoft Docs
description: "Esta página descreve alguns limites de recurso comuns para o Banco de Dados SQL do Azure."
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 884e519f-23bb-4b73-a718-00658629646a
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 10/11/2017
ms.author: carlrab
ms.openlocfilehash: 581948164acef2fe8894e58fb8c8da7772c4cbd9
ms.sourcegitcommit: 54fd091c82a71fbc663b2220b27bc0b691a39b5b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/12/2017
---
# <a name="azure-sql-database-resource-limits"></a>Limites de recursos do Banco de Dados SQL do Azure

## <a name="single-database-storage-sizes-and-performance-levels"></a>Banco de dados individual: níveis de tamanhos e desempenho de armazenamento

Para bancos de dados individuais, as tabelas a seguir mostram os recursos disponíveis para um único banco de dados em cada nível de desempenho e camada de serviço. Você pode definir a camada de serviço, o nível de desempenho e a quantidade de armazenamento para um único banco de dados usando o [Portal do Azure](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal), [Transact-SQL](sql-database-single-database-resources.md#manage-single-database-resources-using-transact-sql), [PowerShell](sql-database-single-database-resources.md#manage-single-database-resources-using-powershell), [CLI do Azure](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-cli) ou a [API REST](sql-database-single-database-resources.md#manage-single-database-resources-using-the-rest-api).

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

## <a name="single-database-change-storage-size"></a>Banco de dados individual: alterar o tamanho do armazenamento

- O preço de DTU para um único banco de dados inclui uma determinada quantidade de armazenamento sem custo adicional. O armazenamento extra além da quantidade incluída pode ser provisionado mediante um custo adicional até o limite máximo de tamanho, em incrementos de 250 GB até 1 TB e, em seguida, em incrementos de 256 GB além de 1 TB. Para conhecer os valores de armazenamento incluídos e os limites máximos de tamanho, confira [Banco de dados individual: tamanhos de armazenamento e níveis de desempenho](#single-database-storage-sizes-and-performance-levels).
- É possível provisionar o armazenamento extra para um único banco de dados aumentando seu tamanho máximo usando o [Portal do Azure](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal), [Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), [CLI do Azure](/cli/azure/sql/db#az_sql_db_update) ou [API REST](/rest/api/sql/databases/update).
- O preço do armazenamento extra para um único banco de dados é a quantidade de armazenamento extra multiplicada pelo preço unitário do armazenamento extra da camada de serviço. Para obter detalhes sobre o preço de armazenamento extra, confira [Preços do Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="single-database-change-dtus"></a>Banco de dados individual: alterar DTUs

Depois de escolher inicialmente uma camada de serviço, nível de desempenho e quantidade de armazenamento, você pode escalar verticalmente de forma dinâmica um banco de dados individual com base na experiência real usando o [Portal do Azure](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal), [Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), a [CLI do Azure](/cli/azure/sql/db#az_sql_db_update) ou a [API REST](/rest/api/sql/databases/update). 

O vídeo a seguir mostra como alterar dinamicamente o nível de desempenho para aumentar os DTUs disponíveis para um único banco de dados.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

A alteração da camada de serviço e/ou nível de desempenho de um banco de dados cria uma réplica do banco de dados original com o novo nível de desempenho e então faz a transição das conexões para réplica. Nenhum dado é perdido durante esse processo, mas durante o breve momento em que realizamos a transição para a réplica, conexões com o banco de dados são desabilitadas, então algumas transações em andamento podem ser revertidas. O período da transição varia, mas fica geralmente abaixo de 4 segundos e menos de 30 segundos 99% do tempo. Se houver muitas transações em andamento no momento de desabilitação das conexões, o período de transição poderá ser maior. 

A duração de todo o processo de expansão depende a camada tamanho e de serviço do banco de dados antes e após a alteração. Por exemplo, um banco de dados de 250 GB que está mudando para, de ou dentro de uma camada de serviço Standard deverá ser concluída dentro de seis horas. Um banco de dados do mesmo tamanho cujos níveis de desempenho estão mudando dentro da camada de serviço Premium deverá concluir a escala vertical dentro de três horas.

> [!TIP]
> Para verificar o status de uma operação de colocação em escala de Banco de Dados SQL em andamento, use a seguinte consulta: ```select * from sys.dm_operation_status```.
>

* Se você estiver atualizando para um nível de serviço ou desempenho mais alto, o tamanho máximo do banco de dados não aumenta, a menos que você especifique explicitamente um tamanho maior (tamanho máximo).
* Para fazer downgrade de um banco de dados, o espaço usado dele deve ter um tamanho menor do que o máximo permitido para a camada de serviço e nível de desempenho de destino. 
* Ao fazer o downgrade da camada **Premium** ou **Premium RS** para a camada **Standard**, um custo de armazenamento extra será aplicado se (1) o tamanho máximo do banco de dados tiver suporte no nível de desempenho de destino e (2) o tamanho máximo ultrapassar a quantidade de armazenamento incluída de nível de desempenho de destino. Por exemplo, se um banco de dados P1 com um tamanho máximo de 500 GB for reduzido para S3, um custo de armazenamento extra será aplicado, pois S3 dá suporte a um tamanho máximo de 500 GB, e a quantidade de armazenamento incluído é somente de 250 GB. Assim, a quantidade de armazenamento extra será 500 GB – 250 GB = 250 GB. Para obter o preço do armazenamento extra, confira [Preços do Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/). Se a quantidade real de espaço usado for menor do que a quantidade de armazenamento incluído, esse custo extra poderá ser evitado por meio da redução do tamanho máximo do banco de dados para a quantidade incluída. 
* Ao atualizar um banco de dados com [replicação geográfica](sql-database-geo-replication-portal.md) habilitada, atualize seus bancos de dados secundários para o nível de desempenho desejado antes de atualizar o banco de dados primário (orientação geral). Ao fazer upgrade para uma diferente, o upgrade do banco de dados secundário será exigido antes.
* Ao atualizar um banco de dados com [replicação geográfica](sql-database-geo-replication-portal.md) habilitada, faça downgrade de seus bancos de dados primários para o nível de desempenho desejado antes de fazer downgrade do banco de dados primário (orientação geral). Ao fazer o downgrade para uma edição diferente, antes será necessário fazer o downgrade do banco de dados primário.
* As ofertas de serviço de restauração são diferentes para as várias camadas de serviço. Se você estiver fazendo downgrade para a camada **Básica**, terá um período de retenção de backup inferior - confira [Backups de Banco de Dados SQL do Azure](sql-database-automated-backups.md).
* As novas propriedades do banco de dados não serão aplicadas até que as alterações sejam concluídas.

## <a name="single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb"></a>Banco de dados individual: limitações de P11 e P15 quando o tamanho máximo for superior a 1 TB

Um tamanho máximo superior a 1 TB para o banco de dados P11 e P15 tem suporte nas seguintes regiões: Leste dos EUA 2, Oeste dos EUA, Gov. EUA - Virgínia, Europa Ocidental, Alemanha Central, Sudeste Asiático, Leste do Japão, Leste da Austrália, Canadá Central e Leste do Canadá. As seguintes considerações e limitações se aplicam aos bancos de dados P11 e P15 com um tamanho máximo superior a 1 TB:

- Se você escolher um tamanho máximo superior a 1 TB durante a criação de um banco de dados (usando um valor de 4 TB ou 4096 GB), o comando create falhará com um erro se o banco de dados for provisionado em uma região sem suporte.
- Para bancos de dados P11 e P15 existentes localizados em uma das regiões com suporte, você pode aumentar o armazenamento máximo para além de 1 TB em incrementos de 256 GB até 4 TB. Para ver se há suporte para um tamanho maior em sua região, use a função [DATABASEPROPERTYEX](/sql/t-sql/functions/databasepropertyex-transact-sql) ou inspecione o tamanho do banco de dados no Portal do Azure. A atualização de um banco de dados existente P11 ou P15 só pode ser executada por um logon principal no nível de servidor ou por membros da função de banco de dados dbmanager. 
- Se uma operação de atualização for executada em uma região com suporte, a configuração será atualizada imediatamente. O banco de dados permanece online durante o processo de atualização. No entanto, não é possível utilizar a quantidade total de armazenamento além de 1 TB de armazenamento até que os arquivos de banco de dados reais sejam atualizados para o novo tamanho máximo. O período de tempo necessário depende do tamanho do banco de dados que está sendo atualizado. 
- Ao criar ou atualizar um banco de dados P11 ou P15, você só pode escolher entre 1 TB e 4 TB de tamanho máximo em incrementos de 256 GB. Ao criar um P11/P15, a opção de armazenamento padrão de 1 TB é pré-selecionada. Para bancos de dados localizados em uma das regiões com suporte, você pode aumentar o número máximo de armazenamento de 4 TB para um banco de dados individual novo ou existente. Para todas as outras regiões, o tamanho máximo não pode ultrapassar 1 TB. O preço não é alterado quando você seleciona 4 TB de armazenamento incluído.
- Se o tamanho máximo de um banco de dados for definido como superior a 1 TB, não poderá ser alterado para 1 TB, mesmo se o armazenamento real usado estiver abaixo de 1 TB. Portanto, não é possível fazer downgrade de um P11 ou P15 com um tamanho máximo superior a 1 TB para um P11 de 1 TB ou P15 de 1 TB ou um nível desempenho inferior, como P1-P6). Esta restrição também se aplica aos cenários de restauração e de cópia, incluindo restauração pontual e geográfica, retenção de backup a longo prazo e cópia de banco de dados. Quando um banco de dados estiver configurado com um tamanho máximo superior a 1 TB, todas as operações de restauração desse banco de dados devem ser executadas em um P11/P15 com um tamanho máximo superior a 1 TB.
- Para cenários com replicação geográfica ativa:
   - Configurar uma relação de replicação geográfica: se o banco de dados primário for P11 ou P15, os secundários também devem ser P11 ou P15; os níveis de desempenho inferiores serão rejeitadas como secundários porque não são capazes de dar suporte a mais de 1 TB.
   - Atualizando o banco de dados primário em uma relação de replicação geográfica: alterar o tamanho máximo para mais de 1 TB em um banco de dados primário disparará a mesma alteração no banco de dados secundário. As duas atualizações devem ser bem-sucedidas para que a alteração no primário entre em vigor. Há limitações de região para a opção superior a 1 TB. Se o secundário estiver em uma região que não oferece suporte a mais de 1 TB, o primário não será atualizado.
- Não há suporte para o uso do serviço de Importação/Exportação para carregar bancos de dados P11/P15 com mais de 1 TB. Use SqlPackage.exe para [importar](sql-database-import.md) e [exportar](sql-database-export.md) dados.

## <a name="elastic-pool-storage-sizes-and-performance-levels"></a>Pool elástico: tamanhos de armazenamento e níveis de desempenho

Para pools elásticos do Bancos de Dados SQL, as tabelas a seguir mostram os recursos disponíveis em cada nível de desempenho e camada de serviço. Você pode definir a camada de serviço, o nível de desempenho e a quantidade de armazenamento usando o [Portal do Azure](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal), [PowerShell](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-powershell), [CLI do Azure](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-cli) ou a [API REST](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-rest-api).

> [!NOTE]
> Os limites de recursos de bancos de dados individuais em pools elásticos geralmente são os mesmos dos bancos de dados individuais fora dos pools com base em DTUs e na camada de serviço. Por exemplo, máximo de trabalhos simultâneos para um banco de dados S2 é 120. Assim, o máximo de trabalhos simultâneos para um banco de dados em um pool padrão também será 120 se o máximo de DTUs por banco de dados no pool for 50 DTUs (o que é equivalente a S2).
>

[!INCLUDE [SQL DB service tiers table for elastic pools](../../includes/sql-database-service-tiers-table-elastic-pools.md)]

Se todas as DTUs de um pool elástico forem usadas, cada banco de dados no pool receberá uma quantidade igual de recursos para processar as consultas. O serviço de Banco de Dados SQL fornece integridade de compartilhamento de recursos entre os bancos de dados ao garantir fatias iguais de tempo de computação. A integridade de compartilhamento de recursos do pool elástico é adicional a qualquer quantidade de recursos garantidos de outra forma a cada banco de dados quando o mínimo de DTUs por banco de dados é definido com um valor diferente de zero.

### <a name="database-properties-for-pooled-databases"></a>Propriedades do banco de dados para bancos de dados em pool

A tabela a seguir descreve as propriedades dos bancos de dados em pool.

| Propriedade | Descrição |
|:--- |:--- |
| Máximo de eDTUs por banco de dados |O número máximo de eDTUs que qualquer banco de dados no pool pode usar, se disponível, com base na utilização por outros bancos de dados no pool. O máximo de eDTUs por banco de dados não é uma garantia de recursos para um banco de dados. Essa configuração é uma configuração global que se aplica a todos os bancos de dados no pool. Defina um valor para o máximo de eDTUs por banco de dados que seja alto o suficiente para lidar com picos de utilização do banco de dados. Espera-se um grau de sobrecarga, uma vez que o pool normalmente assume padrões de uso dos bancos de dados com altos e baixos, em que todos os bancos de dados não atingem um pico simultaneamente. Por exemplo, suponha que o pico de utilização por banco de dados seja de 20 eDTUs e apenas 20% dos 100 bancos de dados no pool atinjam o pico simultaneamente. Se o máximo de eDTUs por banco de dados for definido para 20 eDTUs, será razoável sobrecarregar o pool em 5 vezes e definir os eDTUs por pool como 400. |
| Mínimo de eDTUs por banco de dados |O número mínimo de eDTUs garantido para qualquer banco de dados no pool. Essa configuração é uma configuração global que se aplica a todos os bancos de dados no pool. O mínimo de eDTUs por banco de dados pode ser definido como 0 e também é o valor padrão. Essa propriedade é definida entre 0 e a utilização média de eDTUs por banco de dados. O produto do número de bancos de dados no pool e o mínimo de eDTUs por banco de dados não pode exceder os eDTUs por pool. Por exemplo, se um pool tiver 20 bancos de dados e o mínimo de eDTUs por banco de dados for definido como 10 eDTUs, os eDTUs por pool deverão ser de pelo menos 200 eDTUs. |
| Armazenamento máximo por banco de dados |O armazenamento máximo para um banco de dados em um pool. Os bancos de dados em pool compartilham o armazenamento de pool, de modo que o armazenamento do banco de dados é limitado ao menor entre o armazenamento de pool restante e o armazenamento máximo por banco de dados. O armazenamento máximo por banco de dados refere-se ao tamanho máximo de armazenamento dos arquivos de dados e não inclui o espaço usado pelos arquivos de log. |
|||
 
## <a name="elastic-pool-change-storage-size"></a>Pool elástico: alterar o tamanho do armazenamento

- O preço de eDTU para um pool elástico inclui uma determinada quantidade de armazenamento sem custo adicional. O armazenamento extra além da quantidade incluída pode ser provisionado mediante um custo adicional até o limite máximo de tamanho, em incrementos de 250 GB até 1 TB e, em seguida, em incrementos de 256 GB além de 1 TB. Para conhecer os valores de armazenamento incluídos e os limites de tamanho máximos, confira [Pool elástico: tamanhos de armazenamento e níveis de desempenho](#elastic-pool-storage-sizes-and-performance-levels).
- É possível provisionar o armazenamento extra para um pool elástico aumentando seu tamanho máximo usando o [Portal do Azure](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), a [CLI do Azure](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update) ou a [API REST](/rest/api/sql/elasticpools/update).
- O preço do armazenamento extra para um pool elástico é a quantidade de armazenamento extra multiplicada pelo preço unitário do armazenamento extra da camada de serviço. Para obter detalhes sobre o preço de armazenamento extra, confira [Preços do Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="elastic-pool-change-edtus"></a>Pool elástico: alterar eDTUs

Você pode aumentar ou diminuir os recursos disponíveis para um pool elástico com base nos recurso necessários usando o [Portal do Azure](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), a [CLI do Azure](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update), ou a [API REST](/rest/api/sql/elasticpools/update).

- Ao redimensionar eDTUs de pool, as conexões de banco de dados ficam desativadas por um curto período. Esse é o mesmo comportamento que ocorre ao redimensionar DTUs para um único banco de dados (não em um pool). Para obter detalhes sobre a duração e o impacto da inativação das conexões de um banco de dados durante as operações de redimensionamento, confira [Redimensionar DTUs para um único banco de dados](#single-database-change-storage-size). 
- A duração do redimensionamento de eDTUs do pool pode depender da quantidade total de espaço de armazenamento usado por todos os bancos de dados no pool. Em geral, a latência do redimensionamento tem uma média de 90 minutos ou menos por 100 GB. Por exemplo, se o espaço total usado por todos os bancos de dados no pool for de 200 GB, a latência prevista para redimensionar o pool será de 3 horas ou menos. Em alguns casos, na camada Standard ou Básica, a latência de redimensionamento pode ficar abaixo de cinco minutos, independentemente da quantidade de espaço usado.
- Em geral, a duração da alteração das eDTUs mínimas ou das eDTUs máximas por banco de dados é um processo de cinco minutos ou menos.
- Ao fazer o downgrade de eDTUs de pool, o espaço usado do pool deve ser inferior ao tamanho máximo permitido para a camada de serviço e os eDTUs do pool de destino.
- Ao redimensionar eDTUs de pool, um custo de armazenamento extra será aplicado se (1) o tamanho máximo de armazenamento do pool tiver suporte do pool de destino, e (2) o tamanho máximo do armazenamento ultrapassar a quantidade de armazenamento incluída do pool de destino. Por exemplo, se um pool Standard de 100 eDTU com um tamanho máximo de 100 GB for reduzido para um pool Standard de 50 eDTU, um custo de armazenamento extra será aplicado, pois o pool de destino dá suporte a um tamanho máximo de 100 GB, e a quantidade de armazenamento incluído é somente de 50 GB. Assim, a quantidade de armazenamento extra será 100 GB – 50 GB = 50 GB. Para obter o preço do armazenamento extra, confira [Preços do Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/). Se a quantidade real de espaço usado for menor do que a quantidade de armazenamento incluído, esse custo extra poderá ser evitado por meio da redução do tamanho máximo do banco de dados para a quantidade incluída. 

## <a name="what-happens-when-database-and-elastic-pool-resource-limits-are-reached"></a>O que acontece quando os limites de recursos do banco de dados e do pool elástico forem atingidos?

### <a name="compute-dtus-and-edtus"></a>Computação (DTUs e eDTUs)

Quando a utilização de computação de banco de dados (medida por DTUs e eDTUs) torna-se alta, a latência da consulta aumenta e pode até mesmo atingir o tempo limite. Sob essas condições, as consultas poderão ser colocadas em fila pelo serviço e receberão recursos para execução à medida que os recursos forem liberados.
Ao encontrar uma utilização alta de computação, as opções de atenuação incluem:

- Aumentar o nível de desempenho do banco de dados ou do pool elástico a fim de fornecer ao banco de dados mais DTUs ou eDTUs. Confira [Banco de dados individual: alterar DTUs](#single-database-change-dtus) e [Pool elástico: alterar eDTUs](#elastic-pool-change-edtus).
- Otimizar consultas para reduzir a utilização de recursos de cada consulta. Para saber mais, consulte [Ajuste/Dicas de consulta](sql-database-performance-guidance.md#query-tuning-and-hinting).

### <a name="storage"></a>Armazenamento

Quando o espaço usado do banco de dados atingir o limite de tamanho máximo, inserções e atualizações do banco de dados que aumentam o tamanho dos dados falharão, e os clientes receberão uma [mensagem de erro](sql-database-develop-error-messages.md). Os SELECTS e DELETES do banco de dados continuarão a ter êxito.

Ao encontrar uma utilização alta de espaço, as opções de atenuação incluem:

- Aumentar o tamanho máximo do banco de dados ou do pool elástico ou alterar o nível de desempenho para obter mais armazenamento incluído. Confira [Limites de recursos do Banco de Dados SQL](sql-database-resource-limits.md).
- Se o banco de dados estiver em um pool elástico, o banco de dados poderá ser movido para fora do pool, de modo que seu espaço de armazenamento não seja compartilhado com outros bancos de dados.

### <a name="sessions-and-workers-requests"></a>Sessões e trabalhos (solicitações) 

O número máximo de sessões e trabalhos simultâneos é determinado pelo nível de desempenho e camada de serviço (DTUs e eDTUs).  Novas solicitações serão rejeitadas quando os limites de sessão ou de trabalho forem atingidos, e os clientes receberão uma mensagem de erro. Embora o número de conexões disponíveis possa ser controlado pelo aplicativo, o número de trabalhos simultâneos costuma ser mais difícil de ser estimado e controlado. Isso é especialmente verdadeiro durante os períodos de pico de carga, quando os limites de recurso de banco de dados são atingidos e os trabalhos acumulam devido a consultas de execução mais longas. 

Ao encontrar uma utilização alta de sessão ou trabalho, as opções de atenuação incluem:
- Aumentar a camada de serviço ou o nível de desempenho do banco de dados ou do pool elástico. Consulte [Banco de dados individual: alterar o tamanho do armazenamento](#single-database-change-storage-size), [Banco de dados individual: alterar DTUs](#single-database-change-dtus), [Pool elástico: alterar o tamanho do armazenamento](#elastic-pool-change-storage-size) e [Pool elástico: alterar eDTUs](#elastic-pool-change-edtus).
- Otimizar consultas para reduzir a utilização de recursos de cada consulta se a causa do aumento da utilização de trabalho for devido à contenção de recursos de computação. Para saber mais, consulte [Ajuste/Dicas de consulta](sql-database-performance-guidance.md#query-tuning-and-hinting).

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre as camadas de serviço, confira [Camadas de serviço](sql-database-service-tiers.md).
- Para saber mais sobre bancos de dados individuais, confira [Recursos de banco de dados individual](sql-database-resource-limits.md).
- Para saber mais sobre pools elásticos, confira [Pools elásticos](sql-database-elastic-pool.md).
- Para saber mais sobre limites gerais do Azure, confira [Assinatura do Azure e limites de serviço, cotas e restrições](../azure-subscription-service-limits.md).
- Para saber mais sobre DTUs e eDTUs, confira [DTUs e eDTUs](sql-database-what-is-a-dtu.md).
