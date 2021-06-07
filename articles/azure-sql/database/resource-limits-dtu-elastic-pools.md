---
title: Limites de recursos de DTU em pools elásticos
description: Esta página descreve alguns limites comuns de recursos de DTU em pools elásticos no Banco de Dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: seo-lt-2019 sqldbrb=1 references_regions
ms.devlang: ''
ms.topic: reference
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: sstein
ms.date: 03/30/2021
ms.openlocfilehash: f3c20774accf9e0b589b898cb903d6da4516e553
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106058894"
---
# <a name="resources-limits-for-elastic-pools-using-the-dtu-purchasing-model"></a>Limites de recursos em pools elásticos usando o modelo de compra baseado em DTU
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Este artigo fornece os limites de recursos detalhados para bancos de dados do Banco de Dados SQL do Azure que estão em um pool elástico usando o modelo de compra baseado em DTU.

* Para obter os limites de recursos do modelo de compra baseado em DTU do Banco de Dados SQL do Azure, confira [Limites de recursos de DTU – Banco de Dados SQL do Azure](resource-limits-dtu-single-databases.md).
* Para obter os limites de recursos de vCore, confira [Limites de recursos de vCore – Banco de Dados SQL do Azure](resource-limits-vcore-single-databases.md) e [Limites de recursos de vCore – pools elásticos](resource-limits-vcore-elastic-pools.md).

## <a name="elastic-pool-storage-sizes-and-compute-sizes"></a>Pool elástico: tamanhos de armazenamento e de computação

Para pools elásticos do Banco de Dados SQL do Azure, as tabelas a seguir mostram os recursos disponíveis em cada tamanho da computação e camada de serviço. Você pode definir a camada de serviço, o tamanho da computação e a quantidade de armazenamento usando:

* [Azure portal](elastic-pool-manage.md#azure-portal)
* [PowerShell](elastic-pool-manage.md#powershell)
* [CLI do Azure](elastic-pool-manage.md#azure-cli)
* [API REST](elastic-pool-manage.md#rest-api).

> [!IMPORTANT]
> Para obter diretrizes e considerações sobre dimensionamento, confira [Escalar um pool elástico](elastic-pool-scale.md)

Os limites de recursos de bancos de dados individuais em pools elásticos geralmente são os mesmos dos bancos de dados individuais fora dos pools com base em DTUs e na camada de serviço. Por exemplo, máximo de trabalhos simultâneos para um banco de dados S2 é 120. Assim, o máximo de trabalhos simultâneos para um banco de dados em um pool padrão também será 120 se o máximo de DTUs por banco de dados no pool for 50 DTUs (o que é equivalente a S2).
 
Para o mesmo número de DTUs, os recursos fornecidos para um pool elástico podem exceder os recursos fornecidos a um banco de dados individual fora de um pool elástico. Isso significa que é possível que a utilização de eDTU de um pool elástico seja menor do que a soma da utilização de DTU entre bancos de dados no pool, dependendo dos padrões de carga de trabalho. Por exemplo, em um caso extremo com apenas um banco de dados em um pool elástico em que a utilização de DTU do banco de dados é de 100%, é possível que a utilização de eDTU do pool seja de 50% para determinados padrões de carga de trabalho. Isso pode ocorrer mesmo que a DTU máxima por banco de dados permaneça no valor máximo com suporte para o tamanho de pool especificado.

> [!NOTE]
> O limite de recursos de armazenamento por pool em cada uma das tabelas a seguir não inclui o tempdb nem o armazenamento de log.

### <a name="basic-elastic-pool-limits"></a>Limites de pool elástico Básico

| eDTUs por pool | **50** | **100** | **200** | **300** | **400** | **800** | **1200** | **1600** |
|:---|---:|---:|---:| ---: | ---: | ---: | ---: | ---: |
| Armazenamento incluído por pool (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Armazenamento máx. por pool (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Armazenamento máximo OLTP na memória por pool (GB) | N/D | N/D | N/D | N/D | N/D | N/D | N/D | N/D |
| Número máximo de BDs por pool <sup>1</sup> | 100 | 200 | 500 | 500 | 500 | 500 | 500 | 500 |
| Número máximo de trabalhos simultâneos (solicitações) por pool <sup>2</sup> | 100 | 200 | 400 | 600 | 800 | 1600 | 2400 | 3200 |
| Número máximo de sessões simultâneas por pool <sup>2</sup> | 30000 | 30000 | 30000 | 30000 |30000 | 30000 | 30000 | 30000 |
| Número mínimo de DTUs por opções de banco de dados | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 |
| Número máximo de DTUs por opções de banco de dados | 5 | 5 | 5 | 5 | 5 | 5 | 5 | 5 |
| Armazenamento máximo por banco de dados (GB) | 2 | 2 | 2 | 2 | 2 | 2 | 2 | 2 |
||||||||

<sup>1</sup> Confira [Gerenciamento de recursos em pools elásticos densos](elastic-pool-resource-management.md) para obter considerações adicionais.

<sup>2</sup> Para obter o valor máximo de trabalhos simultâneos (solicitações) para qualquer banco de dados individual, confira [Limites de recursos de bancos de dados individuais](resource-limits-vcore-single-databases.md). Por exemplo, se o pool elástico estiver usando o Gen5 e o número máximo de vCores por banco de dados for definido como 2, o valor máximo de trabalhos simultâneos será de 200.  Se o número máximo de vCores por banco de dados for definido como 0,5, o valor máximo de trabalhos simultâneos será de 50, pois há, no máximo, 100 trabalhos simultâneos por vCore no Gen5. Para outras configurações de máximo de vCore por banco de dados que sejam menores que 1 vCore, o número máximo de trabalhos simultâneos é redimensionado de forma semelhante.

### <a name="standard-elastic-pool-limits"></a>Limites de pool elástico Standard

| eDTUs por pool | **50** | **100** | **200** | **300** | **400** | **800**|
|:---|---:|---:|---:| ---: | ---: | ---: |
| Armazenamento incluído por pool (GB) <sup>1</sup> | 50 | 100 | 200 | 300 | 400 | 800 |
| Armazenamento máx. por pool (GB) | 500 | 750 | 1024 | 1280 | 1536 | 2.048 |
| Armazenamento máximo OLTP na memória por pool (GB) | N/D | N/D | N/D | N/D | N/D | N/D |
| Número máximo de BDs por pool <sup>2</sup> | 100 | 200 | 500 | 500 | 500 | 500 |
| Número máximo de trabalhos simultâneos (solicitações) por pool <sup>3</sup> | 100 | 200 | 400 | 600 | 800 | 1600 |
| Número máximo de sessões simultâneas por pool <sup>3</sup> | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
| Número mínimo de DTUs por opções de banco de dados | 0, 10, 20, 50 | 0, 10, 20, 50, 100 | 0, 10, 20, 50, 100, 200 | 0, 10, 20, 50, 100, 200, 300 | 0, 10, 20, 50, 100, 200, 300, 400 | 0, 10, 20, 50, 100, 200, 300, 400, 800 |
| Número máximo de DTUs por opções de banco de dados | 10, 20, 50 | 10, 20, 50, 100 | 10, 20, 50, 100, 200 | 10, 20, 50, 100, 200, 300 | 10, 20, 50, 100, 200, 300, 400 | 10, 20, 50, 100, 200, 300, 400, 800 |
| Armazenamento máximo por banco de dados (GB) | 1024 | 1024 | 1024 | 1024 | 1024 | 1024 |
||||||||

<sup>1</sup> Confira [Opções de preços do Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/elastic/) para obter detalhes sobre custos adicionais gerados devido ao armazenamento extra provisionado.

<sup>2</sup> Confira [Gerenciamento de recursos em pools elásticos densos](elastic-pool-resource-management.md) para obter considerações adicionais.

<sup>3</sup> Para obter o valor máximo de trabalhos simultâneos (solicitações) para qualquer banco de dados individual, confira [Limites de recursos de bancos de dados individuais](resource-limits-vcore-single-databases.md). Por exemplo, se o pool elástico estiver usando o Gen5 e o número máximo de vCores por banco de dados for definido como 2, o valor máximo de trabalhos simultâneos será de 200.  Se o número máximo de vCores por banco de dados for definido como 0,5, o valor máximo de trabalhos simultâneos será de 50, pois há, no máximo, 100 trabalhos simultâneos por vCore no Gen5. Para outras configurações de máximo de vCore por banco de dados que sejam menores que 1 vCore, o número máximo de trabalhos simultâneos é redimensionado de forma semelhante.

### <a name="standard-elastic-pool-limits-continued"></a>Limites de pool elástico Standard (continuação)

| eDTUs por pool | **1200** | **1600** | **2000** | **2500** | **3000** |
|:---|---:|---:|---:| ---: | ---: |
| Armazenamento incluído por pool (GB) <sup>1</sup> | 1200 | 1600 | 2000 | 2500 | 3000 |
| Armazenamento máx. por pool (GB) | 2560 | 3072 | 3584 | 4096 | 4096 |
| Armazenamento máximo OLTP na memória por pool (GB) | N/D | N/D | N/D | N/D | N/D |
| Número máximo de BDs por pool <sup>2</sup> | 500 | 500 | 500 | 500 | 500 |
| Número máximo de trabalhos simultâneos (solicitações) por pool <sup>3</sup> | 2400 | 3200 | 4000 | 5.000 | 6000 |
| Número máximo de sessões simultâneas por pool <sup>3</sup> | 30000 | 30000 | 30000 | 30000 | 30000 |
| Número mínimo de DTUs por opções de banco de dados | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 |
| Número máximo de DTUs por opções de banco de dados | 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 |
| Armazenamento máximo por banco de dados (GB) | 1024 | 1536 | 1792 | 2304 | 2816 |
|||||||

<sup>1</sup> Confira [Opções de preços do Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/elastic/) para obter detalhes sobre custos adicionais gerados devido ao armazenamento extra provisionado.

<sup>2</sup> Confira [Gerenciamento de recursos em pools elásticos densos](elastic-pool-resource-management.md) para obter considerações adicionais.

<sup>3</sup> Para obter o valor máximo de trabalhos simultâneos (solicitações) para qualquer banco de dados individual, confira [Limites de recursos de bancos de dados individuais](resource-limits-vcore-single-databases.md). Por exemplo, se o pool elástico estiver usando o Gen5 e o número máximo de vCores por banco de dados for definido como 2, o valor máximo de trabalhos simultâneos será de 200.  Se o número máximo de vCores por banco de dados for definido como 0,5, o valor máximo de trabalhos simultâneos será de 50, pois há, no máximo, 100 trabalhos simultâneos por vCore no Gen5. Para outras configurações de máximo de vCore por banco de dados que sejam menores que 1 vCore, o número máximo de trabalhos simultâneos é redimensionado de forma semelhante.

### <a name="premium-elastic-pool-limits"></a>Limites de pool elástico Premium

| eDTUs por pool | **125** | **250** | **500** | **1000** | **1500**|
|:---|---:|---:|---:| ---: | ---: |
| Armazenamento incluído por pool (GB) <sup>1</sup> | 250 | 500 | 750 | 1024 | 1536 |
| Armazenamento máx. por pool (GB) | 1024 | 1024 | 1024 | 1024 | 1536 |
| Armazenamento máximo OLTP na memória por pool (GB) | 1 | 2 | 4 | 10 | 12 |
| Número máximo de BDs por pool <sup>2</sup> | 50 | 100 | 100 | 100 | 100 |
| Número máximo de trabalhos simultâneos por pool (solicitações) <sup>3</sup> | 200 | 400 | 800 | 1600 | 2400 |
| Número máximo de sessões simultâneas por pool <sup>3</sup> | 30000 | 30000 | 30000 | 30000 | 30000 |
| Mínimo de eDTUs por banco de dados | 0, 25, 50, 75, 125 | 0, 25, 50, 75, 125, 250 | 0, 25, 50, 75, 125, 250, 500 | 0, 25, 50, 75, 125, 250, 500, 1000 | 0, 25, 50, 75, 125, 250, 500, 1000|
| Máximo de eDTUs por banco de dados | 25, 50, 75, 125 | 25, 50, 75, 125, 250 | 25, 50, 75, 125, 250, 500 | 25, 50, 75, 125, 250, 500, 1000 | 25, 50, 75, 125, 250, 500, 1000|
| Armazenamento máximo por banco de dados (GB) | 1024 | 1024 | 1024 | 1024 | 1536 |
|||||||

<sup>1</sup> Confira [Opções de preços do Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/elastic/) para obter detalhes sobre custos adicionais gerados devido ao armazenamento extra provisionado.

<sup>2</sup> Confira [Gerenciamento de recursos em pools elásticos densos](elastic-pool-resource-management.md) para obter considerações adicionais.

<sup>3</sup> Para obter o valor máximo de trabalhos simultâneos (solicitações) para qualquer banco de dados individual, confira [Limites de recursos de bancos de dados individuais](resource-limits-vcore-single-databases.md). Por exemplo, se o pool elástico estiver usando o Gen5 e o número máximo de vCores por banco de dados for definido como 2, o valor máximo de trabalhos simultâneos será de 200.  Se o número máximo de vCores por banco de dados for definido como 0,5, o valor máximo de trabalhos simultâneos será de 50, pois há, no máximo, 100 trabalhos simultâneos por vCore no Gen5. Para outras configurações de máximo de vCore por banco de dados que sejam menores que 1 vCore, o número máximo de trabalhos simultâneos é redimensionado de forma semelhante.

### <a name="premium-elastic-pool-limits-continued"></a>Limites de pool elástico Premium (continuação)

| eDTUs por pool | **2000** | **2500** | **3000** | **3500** | **4000**|
|:---|---:|---:|---:| ---: | ---: |
| Armazenamento incluído por pool (GB) <sup>1</sup> | 2.048 | 2560 | 3072 | 3548 | 4096 |
| Armazenamento máx. por pool (GB) | 2.048 | 2560 | 3072 | 3548 | 4096|
| Armazenamento máximo OLTP na memória por pool (GB) | 16 | 20 | 24 | 28 | 32 |
| Número máximo de BDs por pool <sup>2</sup> | 100 | 100 | 100 | 100 | 100 |
| Número máximo de trabalhos simultâneos (solicitações) por pool <sup>3</sup> | 3200 | 4000 | 4800 | 5600 | 6400 |
| Número máximo de sessões simultâneas por pool <sup>3</sup> | 30000 | 30000 | 30000 | 30000 | 30000 |
| Número mínimo de DTUs por opções de banco de dados | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 |
| Número máximo de DTUs por opções de banco de dados | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 |
| Armazenamento máximo por banco de dados (GB) | 2.048 | 2560 | 3072 | 3584 | 4096 |
|||||||

<sup>1</sup> Confira [Opções de preços do Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/elastic/) para obter detalhes sobre custos adicionais gerados devido ao armazenamento extra provisionado.

<sup>2</sup> Confira [Gerenciamento de recursos em pools elásticos densos](elastic-pool-resource-management.md) para obter considerações adicionais.

<sup>3</sup> Para obter o valor máximo de trabalhos simultâneos (solicitações) para qualquer banco de dados individual, confira [Limites de recursos de bancos de dados individuais](resource-limits-vcore-single-databases.md). Por exemplo, se o pool elástico estiver usando o Gen5 e o número máximo de vCores por banco de dados for definido como 2, o valor máximo de trabalhos simultâneos será de 200.  Se o número máximo de vCores por banco de dados for definido como 0,5, o valor máximo de trabalhos simultâneos será de 50, pois há, no máximo, 100 trabalhos simultâneos por vCore no Gen5. Para outras configurações de máximo de vCore por banco de dados que sejam menores que 1 vCore, o número máximo de trabalhos simultâneos é redimensionado de forma semelhante.

> [!IMPORTANT]
> Atualmente, há mais de 1 TB de armazenamento na camada Premium disponível em todas as regiões, exceto Leste da China, Norte da China, Alemanha Central e Nordeste da Alemanha. Nessas regiões, o armazenamento máximo na camada Premium é limitado a 1 TB.  Para obter mais informações, confira [Limitações atuais de P11-P15](single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb).

Se todas as DTUs de um pool elástico forem usadas, cada banco de dados no pool receberá uma quantidade igual de recursos para processar as consultas. O serviço de Banco de Dados SQL fornece integridade de compartilhamento de recursos entre os bancos de dados ao garantir fatias iguais de tempo de computação. A integridade de compartilhamento de recursos do pool elástico é adicional a qualquer quantidade de recursos garantidos de outra forma a cada banco de dados quando o mínimo de DTUs por banco de dados é definido com um valor diferente de zero.

> [!NOTE]
> Para obter os limites do `tempdb`, confira [Limites do tempdb](/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).

### <a name="database-properties-for-pooled-databases"></a>Propriedades do banco de dados para bancos de dados em pool

A tabela a seguir descreve as propriedades dos bancos de dados em pool.

| Propriedade | Descrição |
|:--- |:--- |
| Máximo de eDTUs por banco de dados |O número máximo de eDTUs que qualquer banco de dados no pool pode usar, se disponível, com base na utilização por outros bancos de dados no pool. O máximo de eDTUs por banco de dados não é uma garantia de recursos para um banco de dados. Essa configuração é uma configuração global que se aplica a todos os bancos de dados no pool. Defina um valor para o máximo de eDTUs por banco de dados que seja alto o suficiente para lidar com picos de utilização do banco de dados. Espera-se um grau de sobrecarga, uma vez que o pool normalmente assume padrões de uso dos bancos de dados com altos e baixos, em que todos os bancos de dados não atingem um pico simultaneamente. Por exemplo, suponha que o pico de utilização por banco de dados seja de 20 eDTUs e apenas 20% dos 100 bancos de dados no pool atinjam o pico simultaneamente. Se o máximo de eDTUs por banco de dados for definido para 20 eDTUs, será razoável sobrecarregar o pool em 5 vezes e definir os eDTUs por pool como 400. |
| Mínimo de eDTUs por banco de dados |O número mínimo de eDTUs garantido para qualquer banco de dados no pool. Essa configuração é uma configuração global que se aplica a todos os bancos de dados no pool. O mínimo de eDTUs por banco de dados pode ser definido como 0 e também é o valor padrão. Essa propriedade é definida entre 0 e a utilização média de eDTUs por banco de dados. O produto do número de bancos de dados no pool e o mínimo de eDTUs por banco de dados não pode exceder os eDTUs por pool. Por exemplo, se um pool tiver 20 bancos de dados e o mínimo de eDTUs por banco de dados for definido como 10 eDTUs, os eDTUs por pool deverão ser de pelo menos 200 eDTUs. |
| Armazenamento máximo por banco de dados |O tamanho máximo do banco de dados definido pelo usuário para um banco de dados em um pool. No entanto, os bancos de dados em pool compartilham o armazenamento de pool alocado. Mesmo se o total de armazenamento máximo *por banco de dados* for definido como maior que o espaço total disponível de armazenamento *do conjunto*, o espaço total realmente usado por todos os bancos de dados não poderá exceder o limite de pool disponível. O tamanho máximo por banco de dados refere-se ao tamanho máximo dos arquivos de dados e não inclui o espaço usado pelos arquivos de log. |
|||

## <a name="next-steps"></a>Próximas etapas

* Para obter os limites de recursos de vCore para um banco de dados individual, confira [Limites de recursos para bancos de dados individuais usando o modelo de compra baseado em vCore](resource-limits-vcore-single-databases.md)
* Para obter os limites de recursos de DTU para um banco de dados individual, confira [Limites de recursos para bancos de dados individuais usando o modelo de compra baseado em DTU](resource-limits-dtu-single-databases.md)
* Para obter os limites de recursos de vCore para pools elásticos, confira [Limites de recursos para pools elásticos usando o modelo de compra baseado em vCore](resource-limits-vcore-elastic-pools.md)
* Para obter os limites de recursos para instâncias gerenciadas na Instância Gerenciada de SQL do Azure, confira [Limites de recursos da Instância Gerenciada de SQL](../managed-instance/resource-limits.md).
* Para saber mais sobre limites gerais do Azure, confira [Assinatura do Azure e limites de serviço, cotas e restrições](../../azure-resource-manager/management/azure-subscription-service-limits.md).
* Para obter informações sobre os limites de recursos em um servidor SQL lógico, confira a [visão geral dos limites de recursos em um servidor SQL lógico](resource-limits-logical-server.md) para ver os limites nos níveis do servidor e da assinatura.