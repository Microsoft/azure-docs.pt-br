---
title: Limites de recursos vCore de banco de dados individual
description: Esta página descreve alguns limites de recursos vCore comuns para um único banco de dados no banco de dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/23/2021
ms.openlocfilehash: a3cd68b610b0e0db05f60450009bc83a80c3037b
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/25/2021
ms.locfileid: "105107594"
---
# <a name="resource-limits-for-single-databases-using-the-vcore-purchasing-model"></a>Limites de recursos para bancos de dados individuais usando o modelo de compra de vCore
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Este artigo fornece os limites de recursos detalhados para bancos de dados individuais no banco de dados SQL do Azure usando o modelo de compra vCore.

Para os limites de modelo de compra de DTU para bancos de dados individuais em um servidor, consulte [visão geral dos limites de recursos em um servidor](resource-limits-logical-server.md).

Você pode definir a camada de serviço, o tamanho da computação (objetivo do serviço) e a quantidade de armazenamento para um único banco de dados usando o [portal do Azure](single-database-manage.md#the-azure-portal), [Transact-SQL](single-database-manage.md#transact-sql-t-sql), [PowerShell](single-database-manage.md#powershell), o [CLI do Azure](single-database-manage.md#the-azure-cli)ou a [API REST](single-database-manage.md#rest-api).

> [!IMPORTANT]
> Para obter diretrizes e considerações sobre o dimensionamento, consulte [dimensionar um banco de dados individual](single-database-scale.md).

## <a name="general-purpose---serverless-compute---gen5"></a>Computação de uso geral sem servidor-Gen5

A [camada de computação sem servidor](serverless-tier-overview.md) está disponível atualmente somente em hardware Gen5.

### <a name="gen5-compute-generation-part-1"></a>Geração de computação Gen5 (parte 1)

|Tamanho da computação (objetivo do serviço)|GP_S_Gen5_1|GP_S_Gen5_2|GP_S_Gen5_4|GP_S_Gen5_6|GP_S_Gen5_8|
|:--- | --: |--: |--: |--: |--: |
|Geração de computação|Gen5|Gen5|Gen5|Gen5|Gen5|
|VCores mín. máx.|0.5-1|0.5-2|0,5-4|0,75 a 6|1.0-8|
|Memória mín. máx. (GB)|2.02-3|2.05-6|2.10-12|2,25-18|3,00-24|
|Atraso de pausa automática mín. máx. (minutos)|60-10080|60-10080|60-10080|60-10080|60-10080|
|Suporte de columnstore|Sim*|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (GB)|512|1024|1024|1024|1536|
|Tamanho máximo de log (GB)|154|307|307|307|461|
|Tamanho máximo de dados de TempDB (GB)|32|64|128|192|256|
|Tipo de armazenamento|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|
|Latência de E/S (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|
|IOPS de dados máximo \*\*|320|640|1280|1920|2560|
|Taxa máxima de logs (MBps)|4.5|9|18|27|36|
|Máximo de trabalhos simultâneos (solicitações)|75|150|300|450|600|
|Máximo de sessões simultâneas|30,000|30,000|30,000|30,000|30,000|
|Número de réplicas|1|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|
|Escala de leitura|N/D|N/D|N/D|N/D|N/D|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|

\* Os objetivos de serviço com configurações mínimas máximas vcores podem ter memória insuficiente para criar e usar índices de repositório de coluna.  Se encontrar problemas de desempenho com o repositório de coluna, aumente a configuração de VCORE máxima para aumentar a memória máxima disponível.  
\*\* O valor máximo para tamanhos de e/s variando entre 8 KB e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](resource-limits-logical-server.md#resource-governance).

### <a name="gen5-compute-generation-part-2"></a>Geração de computação Gen5 (parte 2)

|Tamanho da computação (objetivo do serviço)|GP_S_Gen5_10|GP_S_Gen5_12|GP_S_Gen5_14|GP_S_Gen5_16|
|:--- | --: |--: |--: |--: |
|Geração de computação|Gen5|Gen5|Gen5|Gen5|
|VCores mín. máx.|1,25-10|1,50-12|1,75-14|2,00-16|
|Memória mín. máx. (GB)|3,75-30|4.50-36|5,25-42|6.00-48|
|Atraso de pausa automática mín. máx. (minutos)|60-10080|60-10080|60-10080|60-10080|
|Suporte de columnstore|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (GB)|1536|3072|3072|3072|
|Tamanho máximo de log (GB)|461|461|461|922|
|Tamanho máximo de dados de TempDB (GB)|320|384|448|512|
|Tipo de armazenamento|SSD remoto|SSD remoto|SSD remoto|SSD remoto|
|Latência de E/S (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|
|IOPS de dados máximo *|3200|3840|4480|5120|
|Taxa máxima de logs (MBps)|45|50|50|50|
|Máximo de trabalhos simultâneos (solicitações)|750|900|1.050|1200|
|Máximo de sessões simultâneas|30,000|30,000|30,000|30,000|
|Número de réplicas|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|
|Escala de leitura|N/D|N/D|N/D|N/D|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|

\* O valor máximo para tamanhos de e/s variando entre 8 KB e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](resource-limits-logical-server.md#resource-governance).

### <a name="gen5-compute-generation-part-3"></a>Geração de computação Gen5 (parte 3)

|Tamanho da computação (objetivo do serviço)|GP_S_Gen5_18|GP_S_Gen5_20|GP_S_Gen5_24|GP_S_Gen5_32|GP_S_Gen5_40|
|:--- | --: |--: |--: |--: |--:|
|Geração de computação|Gen5|Gen5|Gen5|Gen5|Gen5|
|VCores mín. máx.|2,25-18|2,5 a 20|3-24|4-32|5-40|
|Memória mín. máx. (GB)|6.75-54|7.5-60|9-72|12-96|15-120|
|Atraso de pausa automática mín. máx. (minutos)|60-10080|60-10080|60-10080|60-10080|60-10080|
|Suporte de columnstore|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (GB)|3072|3072|4096|4096|4096|
|Tamanho máximo de log (GB)|922|922|1024|1024|1024|
|Tamanho máximo de dados de TempDB (GB)|576|640|768|1024|1280|
|Tipo de armazenamento|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|
|Latência de E/S (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|
|IOPS de dados máximo *|5760|6400|7680|10240|12800|
|Taxa máxima de logs (MBps)|50|50|50|50|50|
|Máximo de trabalhos simultâneos (solicitações)|1350|1500|1800|2400|3000|
|Máximo de sessões simultâneas|30,000|30,000|30,000|30,000|30,000|
|Número de réplicas|1|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|
|Escala de leitura|N/D|N/D|N/D|N/D|N/D|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|

\* O valor máximo para tamanhos de e/s variando entre 8 KB e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](resource-limits-logical-server.md#resource-governance).


## <a name="hyperscale---provisioned-compute---gen4"></a>Hiperescala – computação provisionada-Gen4

### <a name="gen4-compute-generation-part-1"></a>Geração de computação Gen4 (parte 1)

|Tamanho da computação (objetivo do serviço)|HS_Gen4_1|HS_Gen4_2|HS_Gen4_3|HS_Gen4_4|HS_Gen4_5|HS_Gen4_6|
|:--- | --: |--: |--: |---: | --: |--: |
|Geração de computação|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|1|2|3|4|5|6|
|Memória (GB)|7|14|21|28|35|42|
|[RBPEX](service-tier-hyperscale.md#compute) Tamanho|3X de memória|3X de memória|3X de memória|3X de memória|3X de memória|3X de memória|
|Suporte de columnstore|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (TB)|100 |100 |100 |100 |100 |100|
|Tamanho máximo do log (TB)|Ilimitado |Ilimitado |Ilimitado |Ilimitado |Ilimitado |Ilimitado |
|Tamanho máximo de dados de TempDB (GB)|32|64|96|128|160|192|
|Tipo de armazenamento| [Observação 1](#notes) |[Observação 1](#notes)|[Observação 1](#notes) |[Observação 1](#notes) |[Observação 1](#notes) |[Observação 1](#notes) |
|IOPS máximo de SSD local *|4000 |8000 |12000 |16000 |20000 |24.000 |
|Taxa máxima de logs (MBps)|100 |100 |100 |100 |100 |100 |
|Latência de E/S (aproximada)|[Observação 2](#notes)|[Observação 2](#notes)|[Observação 2](#notes)|[Observação 2](#notes)|[Observação 2](#notes)|[Observação 2](#notes)|
|Máximo de trabalhos simultâneos (solicitações)|200|400|600|800|1000|1200|
|Máximo de sessões simultâneas|30,000|30,000|30,000|30,000|30,000|30,000|
|Réplicas secundárias|0-4|0-4|0-4|0-4|0-4|0-4|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|
|Escala de leitura|Sim|Sim|Sim|Sim|Sim|Sim|
|Retenção de armazenamento de backup|7 dias|7 dias|7 dias|7 dias|7 dias|7 dias|
|||

\* Além da e/s SSD local, as cargas de trabalho usarão a es [do servidor de paginação](service-tier-hyperscale.md#page-server) remota. O IOPS efetivo dependerá da carga de trabalho. Para obter detalhes, consulte [governança de e](resource-limits-logical-server.md#resource-governance)/s de dados e [Io de dados nas estatísticas de utilização de recursos](hyperscale-performance-diagnostics.md#data-io-in-resource-utilization-statistics).

### <a name="gen4-compute-generation-part-2"></a>Geração de computação Gen4 (parte 2)

|Tamanho da computação (objetivo do serviço)|HS_Gen4_7|HS_Gen4_8|HS_Gen4_9|HS_Gen4_10|HS_Gen4_16|HS_Gen4_24|
|:--- | ---: |--: |--: | --: |--: |--: |
|Geração de computação|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|7|8|9|10|16|24|
|Memória (GB)|49|56|63|70|112|159,5|
|[RBPEX](service-tier-hyperscale.md#compute) Tamanho|3X de memória|3X de memória|3X de memória|3X de memória|3X de memória|3X de memória|
|Suporte de columnstore|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (TB)|100 |100 |100 |100 |100 |100 |
|Tamanho máximo do log (TB)|Ilimitado |Ilimitado |Ilimitado |Ilimitado |Ilimitado |Ilimitado |
|Tamanho máximo de dados de TempDB (GB)|224|256|288|320|512|768|
|Tipo de armazenamento| [Observação 1](#notes) |[Observação 1](#notes) |[Observação 1](#notes) |[Observação 1](#notes) |[Observação 1](#notes) |[Observação 1](#notes) |
|IOPS máximo de SSD local *|28000 |32000 |36000 |40000 |64000 |76800 |
|Taxa máxima de logs (MBps)|100 |100 |100 |100 |100 |100 |
|Latência de E/S (aproximada)|[Observação 2](#notes)|[Observação 2](#notes)|[Observação 2](#notes)|[Observação 2](#notes)|[Observação 2](#notes)|[Observação 2](#notes)|
|Máximo de trabalhos simultâneos (solicitações)|1.400|1600|1800|2000|3200|4800|
|Máximo de sessões simultâneas|30,000|30,000|30,000|30,000|30,000|30,000|
|Réplicas secundárias|0-4|0-4|0-4|0-4|0-4|0-4|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|
|Escala de leitura|Sim|Sim|Sim|Sim|Sim|Sim|
|Retenção de armazenamento de backup|7 dias|7 dias|7 dias|7 dias|7 dias|7 dias|
|||

\* Além da e/s SSD local, as cargas de trabalho usarão a es [do servidor de paginação](service-tier-hyperscale.md#page-server) remota. O IOPS efetivo dependerá da carga de trabalho. Para obter detalhes, consulte [governança de e](resource-limits-logical-server.md#resource-governance)/s de dados e [Io de dados nas estatísticas de utilização de recursos](hyperscale-performance-diagnostics.md#data-io-in-resource-utilization-statistics).

## <a name="hyperscale---provisioned-compute---gen5"></a>Hiperescala – computação provisionada-Gen5

### <a name="gen5-compute-generation-part-1"></a>Geração de computação Gen5 (parte 1)

|Tamanho da computação (objetivo do serviço)|HS_Gen5_2|HS_Gen5_4|HS_Gen5_6|HS_Gen_8|HS_Gen5_10|HS_Gen5_12|HS_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |
|Geração de computação|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|2|4|6|8|10|12|14|
|Memória (GB)|10.4|20,8|31,1|41,5|51,9|62,3|72,7|
|[RBPEX](service-tier-hyperscale.md#compute) Tamanho|3X de memória|3X de memória|3X de memória|3X de memória|3X de memória|3X de memória|3X de memória|
|Suporte de columnstore|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (TB)|100 |100 |100 |100 |100 |100 |100|
|Tamanho máximo do log (TB)|Ilimitado |Ilimitado |Ilimitado |Ilimitado |Ilimitado |Ilimitado |Ilimitado |
|Tamanho máximo de dados de TempDB (GB)|64|128|192|256|320|384|448|
|Tipo de armazenamento| [Observação 1](#notes) |[Observação 1](#notes)|[Observação 1](#notes) |[Observação 1](#notes) |[Observação 1](#notes) |[Observação 1](#notes) |[Observação 1](#notes) |
|IOPS máximo de SSD local *|8000 |16000 |24.000 |32000 |40000 |48000 |56000 |
|Taxa máxima de logs (MBps)|100 |100 |100 |100 |100 |100 |100 |
|Latência de E/S (aproximada)|[Observação 2](#notes)|[Observação 2](#notes)|[Observação 2](#notes)|[Observação 2](#notes)|[Observação 2](#notes)|[Observação 2](#notes)|[Observação 2](#notes)|
|Máximo de trabalhos simultâneos (solicitações)|200|400|600|800|1000|1200|1.400|
|Máximo de sessões simultâneas|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Réplicas secundárias|0-4|0-4|0-4|0-4|0-4|0-4|0-4|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Escala de leitura|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Retenção de armazenamento de backup|7 dias|7 dias|7 dias|7 dias|7 dias|7 dias|7 dias|
|||

\* Além da e/s SSD local, as cargas de trabalho usarão a es [do servidor de paginação](service-tier-hyperscale.md#page-server) remota. O IOPS efetivo dependerá da carga de trabalho. Para obter detalhes, consulte [governança de e](resource-limits-logical-server.md#resource-governance)/s de dados e [Io de dados nas estatísticas de utilização de recursos](hyperscale-performance-diagnostics.md#data-io-in-resource-utilization-statistics).

### <a name="gen5-compute-generation-part-2"></a>Geração de computação Gen5 (parte 2)

|Tamanho da computação (objetivo do serviço)|HS_Gen5_16|HS_Gen5_18|HS_Gen5_20|HS_Gen_24|HS_Gen5_32|HS_Gen5_40|HS_Gen5_80|
|:--- | --: |--: |--: |--: |---: |--: |--: |
|Geração de computação|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|16|18|20|24|32|40|80|
|Memória (GB)|83|93,4|103,8|124,6|166,1|207,6|415,2|
|[RBPEX](service-tier-hyperscale.md#compute) Tamanho|3X de memória|3X de memória|3X de memória|3X de memória|3X de memória|3X de memória|3X de memória|
|Suporte de columnstore|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (TB)|100 |100 |100 |100 |100 |100 |100 |
|Tamanho máximo do log (TB)|Ilimitado |Ilimitado |Ilimitado |Ilimitado |Ilimitado |Ilimitado |Ilimitado |
|Tamanho máximo de dados de TempDB (GB)|512|576|640|768|1024|1280|2560|
|Tipo de armazenamento| [Observação 1](#notes) |[Observação 1](#notes)|[Observação 1](#notes)|[Observação 1](#notes) |[Observação 1](#notes) |[Observação 1](#notes) |[Observação 1](#notes) |
|IOPS máximo de SSD local *|64000 |72000 |80000 |96000 |128000 |160000 |204800 |
|Taxa máxima de logs (MBps)|100 |100 |100 |100 |100 |100 |100 |
|Latência de E/S (aproximada)|[Observação 2](#notes)|[Observação 2](#notes)|[Observação 2](#notes)|[Observação 2](#notes)|[Observação 2](#notes)|[Observação 2](#notes)|[Observação 2](#notes)|
|Máximo de trabalhos simultâneos (solicitações)|1600|1800|2000|2400|3200|4000|8000|
|Máximo de sessões simultâneas|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Réplicas secundárias|0-4|0-4|0-4|0-4|0-4|0-4|0-4|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Escala de leitura|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Retenção de armazenamento de backup|7 dias|7 dias|7 dias|7 dias|7 dias|7 dias|7 dias|
|||

\* Além da e/s SSD local, as cargas de trabalho usarão a es [do servidor de paginação](service-tier-hyperscale.md#page-server) remota. O IOPS efetivo dependerá da carga de trabalho. Para obter detalhes, consulte [governança de e](resource-limits-logical-server.md#resource-governance)/s de dados e [Io de dados nas estatísticas de utilização de recursos](hyperscale-performance-diagnostics.md#data-io-in-resource-utilization-statistics).

#### <a name="notes"></a>Anotações

**Observação 1**: o hiperescala é uma arquitetura de várias camadas com componentes de armazenamento e computação separados: [arquitetura de camada de serviço de hiperescala](service-tier-hyperscale.md#distributed-functions-architecture)

**Observação 2**: a latência é 1-2 ms para dados no SSD da réplica de computação local, que armazena em cache as páginas de dados mais usadas. Maior latência de dados recuperados de servidores de páginas.

## <a name="hyperscale---provisioned-compute---dc-series"></a>Hiperescala – computação provisionada-série CC

|Tamanho da computação (objetivo do serviço)|HS_DC_2|HS_DC_4|HS_DC_6|HS_DC_8|
|:--- | --: |--: |--: |--: |---: | 
|Geração de computação|Série DC|Série DC|Série DC|Série DC|
|vCores|2|4|6|8|
|Memória (GB)|9|18|27|36|
|[RBPEX](service-tier-hyperscale.md#compute) Tamanho|3X de memória|3X de memória|3X de memória|3X de memória|
|Suporte de columnstore|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (TB)|100 |100 |100 |100 |
|Tamanho máximo do log (TB)|Ilimitado |Ilimitado |Ilimitado |Ilimitado |
|Tamanho máximo de dados de TempDB (GB)|64|128|192|256|
|Tipo de armazenamento| [Observação 1](#notes) |[Observação 1](#notes)|[Observação 1](#notes) |[Observação 1](#notes) |
|IOPS máximo de SSD local *|14000|28000|42000|44800|
|Taxa máxima de logs (MBps)|100 |100 |100 |100 |
|Latência de E/S (aproximada)|[Observação 2](#notes)|[Observação 2](#notes)|[Observação 2](#notes)|[Observação 2](#notes)|
|Máximo de trabalhos simultâneos (solicitações)|160|320|480|640|
|Máximo de sessões simultâneas|30,000|30,000|30,000|30,000|
|Réplicas secundárias|0-4|0-4|0-4|0-4|
|Multi-AZ|N/D|N/D|N/D|N/D|
|Escala de leitura|Sim|Sim|Sim|Sim|
|Retenção de armazenamento de backup|7 dias|7 dias|7 dias|7 dias|
|||

### <a name="notes"></a>Anotações

**Observação 1**: o hiperescala é uma arquitetura de várias camadas com componentes de armazenamento e computação separados: [arquitetura de camada de serviço de hiperescala](service-tier-hyperscale.md#distributed-functions-architecture)

**Observação 2**: a latência é 1-2 ms para dados no SSD da réplica de computação local, que armazena em cache as páginas de dados mais usadas. Maior latência de dados recuperados de servidores de páginas.

## <a name="general-purpose---provisioned-compute---gen4"></a>Computação provisionada de uso geral-Gen4

> [!IMPORTANT]
> Novos bancos de dados Gen4 não têm mais suporte nas regiões leste da Austrália ou sul do Brasil.

### <a name="gen4-compute-generation-part-1"></a>Geração de computação Gen4 (parte 1)

|Tamanho da computação (objetivo do serviço)|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|Geração de computação|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|1|2|3|4|5|6|
|Memória (GB)|7|14|21|28|35|42|
|Suporte de columnstore|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (GB)|1024|1024|1536|1536|1536|3072|
|Tamanho máximo de log (GB)|307|307|461|461|461|922|
|Tamanho máximo de dados de TempDB (GB)|32|64|96|128|160|192|
|Tipo de armazenamento|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|
|Latência de E/S (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|
|IOPS de dados máximo *|320|640|960|1280|1600|1920|
|Taxa máxima de logs (MBps)|4.5|9|13,5|18|22,5|27|
|Máximo de trabalhos simultâneos (solicitações)|200|400|600|800|1000|1200|
|Máximo de sessões simultâneas|30,000|30,000|30,000|30,000|30,000|30,000|
|Número de réplicas|1|1|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|
|Escala de leitura|N/D|N/D|N/D|N/D|N/D|N/D|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|

\* O valor máximo para tamanhos de e/s variando entre 8 KB e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](resource-limits-logical-server.md#resource-governance).

### <a name="gen4-compute-generation-part-2"></a>Geração de computação Gen4 (parte 2)

|Tamanho da computação (objetivo do serviço)|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24
|:--- | --: |--: |--: |--: |--: |--: |
|Geração de computação|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|7|8|9|10|16|24|
|Memória (GB)|49|56|63|70|112|159,5|
|Suporte de columnstore|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (GB)|3072|3072|3072|3072|4096|4096|
|Tamanho máximo de log (GB)|922|922|922|922|1229|1229|
|Tamanho máximo de dados de TempDB (GB)|224|256|288|320|512|768|
|Tipo de armazenamento|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|
|Latência de E/S (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)
|IOPS de dados máximo *|2240|2560|2880|3200|5120|7680|
|Taxa máxima de logs (MBps)|31,5|36|40,5|45|50|50|
|Máximo de trabalhos simultâneos (solicitações)|1.400|1600|1800|2000|3200|4800|
|Máximo de sessões simultâneas|30,000|30,000|30,000|30,000|30,000|30,000|
|Número de réplicas|1|1|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|
|Escala de leitura|N/D|N/D|N/D|N/D|N/D|N/D|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|

\* O valor máximo para tamanhos de e/s variando entre 8 KB e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](resource-limits-logical-server.md#resource-governance).

## <a name="general-purpose---provisioned-compute---gen5"></a>Computação provisionada de uso geral-Gen5

### <a name="gen5-compute-generation-part-1"></a>Geração de computação Gen5 (parte 1)

|Tamanho da computação (objetivo do serviço)|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Geração de computação|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|2|4|6|8|10|12|14|
|Memória (GB)|10.4|20,8|31,1|41,5|51,9|62,3|72,7|
|Suporte de columnstore|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (GB)|1024|1024|1536|1536|1536|3072|3072|
|Tamanho máximo de log (GB)|307|307|461|461|461|922|922|
|Tamanho máximo de dados de TempDB (GB)|64|128|192|256|320|384|384|
|Tipo de armazenamento|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|
|Latência de E/S (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|
|IOPS de dados máximo *|640|1280|1920|2560|3200|3840|4480|
|Taxa máxima de logs (MBps)|9|18|27|36|45|50|50|
|Máximo de trabalhos simultâneos (solicitações)|200|400|600|800|1000|1200|1.400|
|Máximo de sessões simultâneas|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Número de réplicas|1|1|1|1|1|1|1|
|Multi-AZ|[Disponível na visualização](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Disponível na visualização](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Disponível na visualização](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Disponível na visualização](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Disponível na visualização](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Disponível na visualização](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Disponível na visualização](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|
|Escala de leitura|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|

\* O valor máximo para tamanhos de e/s variando entre 8 KB e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](resource-limits-logical-server.md#resource-governance).

### <a name="gen5-compute-generation-part-2"></a>Geração de computação Gen5 (parte 2)

|Tamanho da computação (objetivo do serviço)|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Geração de computação|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|16|18|20|24|32|40|80|
|Memória (GB)|83|93,4|103,8|124,6|166,1|207,6|415,2|
|Suporte de columnstore|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (GB)|3072|3072|3072|4096|4096|4096|4096|
|Tamanho máximo de log (GB)|922|922|922|1024|1024|1024|1024|
|Tamanho máximo de dados de TempDB (GB)|512|576|640|768|1024|1280|2560|
|Tipo de armazenamento|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|
|Latência de E/S (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|
|IOPS de dados máximo *|5120|5760|6400|7680|10240|12800|12800|
|Taxa máxima de logs (MBps)|50|50|50|50|50|50|50|
|Máximo de trabalhos simultâneos (solicitações)|1600|1800|2000|2400|3200|4000|8000|
|Máximo de sessões simultâneas|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Número de réplicas|1|1|1|1|1|1|1|
|Multi-AZ|[Disponível na visualização](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Disponível na visualização](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Disponível na visualização](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Disponível na visualização](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Disponível na visualização](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Disponível na visualização](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Disponível na visualização](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|
|Escala de leitura|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|

\* O valor máximo para tamanhos de e/s variando entre 8 KB e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](resource-limits-logical-server.md#resource-governance).

## <a name="general-purpose---provisioned-compute---fsv2-series"></a>Computação provisionada de uso geral-série Fsv2

### <a name="fsv2-series-compute-generation-part-1"></a>Geração de computação da série Fsv2 (parte 1)

|Tamanho da computação (objetivo do serviço)|GP_Fsv2_8|GP_Fsv2_10|GP_Fsv2_12|GP_Fsv2_14| GP_Fsv2_16|
|:---| ---:|---:|---:|---:|---:|
|Geração de computação|Série Fsv2|Série Fsv2|Série Fsv2|Série Fsv2|Série Fsv2|
|vCores|8|10|12|14|16|
|Memória (GB)|15.1|18,9|22,7|26,5|30,2|
|Suporte de columnstore|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (GB)|1024|1024|1024|1024|1536|
|Tamanho máximo de log (GB)|336|336|336|336|512|
|Tamanho máximo de dados de TempDB (GB)|37|46|56|65|74|
|Tipo de armazenamento|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|
|Latência de E/S (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|
|IOPS de dados máximo *|2560|3200|3840|4480|5120|
|Taxa máxima de logs (MBps)|36|45|50|50|50|
|Máximo de trabalhos simultâneos (solicitações)|400|500|600|700|800|
|Máximo de logons simultâneos|800|1000|1200|1.400|1600|
|Máximo de sessões simultâneas|30,000|30,000|30,000|30,000|30,000|
|Número de réplicas|1|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|
|Escala de leitura|N/D|N/D|N/D|N/D|N/D|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|

\* O valor máximo para tamanhos de e/s variando entre 8 KB e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](resource-limits-logical-server.md#resource-governance).

### <a name="fsv2-series-compute-generation-part-2"></a>Geração de computação da série Fsv2 (parte 2)

|Tamanho da computação (objetivo do serviço)|GP_Fsv2_18|GP_Fsv2_20|GP_Fsv2_24|GP_Fsv2_32| GP_Fsv2_36|GP_Fsv2_72|
|:---| ---:|---:|---:|---:|---:|---:|
|Geração de computação|Série Fsv2|Série Fsv2|Série Fsv2|Série Fsv2|Série Fsv2|Série Fsv2|
|vCores|18|20|24|32|36|72|
|Memória (GB)|34,0|37,8|45,4|60,5|68,0|136,0|
|Suporte de columnstore|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (GB)|1536|1536|1536|3072|3072|4096|
|Tamanho máximo de log (GB)|512|512|512|1024|1024|1024|
|Tamanho máximo de dados de TempDB (GB)|83|93|111|148|167|333|
|Tipo de armazenamento|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|
|Latência de E/S (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|
|IOPS de dados máximo *|5760|6400|7680|10240|11520|12800|
|Taxa máxima de logs (MBps)|50|50|50|50|50|50|
|Máximo de trabalhos simultâneos (solicitações)|900|1000|1200|1600|1800|3600|
|Máximo de logons simultâneos|1800|2000|2400|3200|3600|7200|
|Máximo de sessões simultâneas|30,000|30,000|30,000|30,000|30,000|30,000|
|Número de réplicas|1|1|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|
|Escala de leitura|N/D|N/D|N/D|N/D|N/D|N/D|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|

\* O valor máximo para tamanhos de e/s variando entre 8 KB e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](resource-limits-logical-server.md#resource-governance).

## <a name="general-purpose---provisioned-compute---dc-series"></a>Computação provisionada de uso geral-série DC

|Tamanho da computação (objetivo do serviço)|GP_DC_2|GP_DC_4|GP_DC_6|GP_DC_8| 
|:---| ---:|---:|---:|---:|
|Geração de computação|Série DC|Série DC|Série DC|Série DC|
|vCores|2|4|6|8|
|Memória (GB)|9|18|27|36|
|Suporte de columnstore|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (GB)|1024|1536|3072|3072|
|Tamanho máximo de log (GB)|307|461|922|922|
|Tamanho máximo de dados de TempDB (GB)|64|128|192|256|
|Tipo de armazenamento|SSD remoto|SSD remoto|SSD remoto|SSD remoto|
|Latência de E/S (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|
|IOPS de dados máximo *|640|1280|1920|2560|
|Taxa máxima de logs (MBps)|9|18|27|36|
|Máximo de trabalhos simultâneos (solicitações)|160|320|480|640|
|Máximo de sessões simultâneas|30,000|30,000|30,000|30,000|
|Número de réplicas|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|
|Escala de leitura|N/D|N/D|N/D|N/D|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|


\* O valor máximo para tamanhos de e/s variando entre 8 KB e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](resource-limits-logical-server.md#resource-governance).

## <a name="business-critical---provisioned-compute---gen4"></a>Comercialmente crítico-computação provisionada-Gen4

> [!IMPORTANT]
> Novos bancos de dados Gen4 não têm mais suporte nas regiões leste da Austrália ou sul do Brasil.

### <a name="gen4-compute-generation-part-1"></a>Geração de computação Gen4 (parte 1)

|Tamanho da computação (objetivo do serviço)|BC_Gen4_1|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|Geração de computação|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|1|2|3|4|5|6|
|Memória (GB)|7|14|21|28|35|42|
|Suporte de columnstore|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|1|2|3|4|5|6|
|Tipo de armazenamento|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|
|Tamanho máximo de dados (GB)|1024|1024|1024|1024|1024|1024|
|Tamanho máximo de log (GB)|307|307|307|307|307|307|
|Tamanho máximo de dados de TempDB (GB)|32|64|96|128|160|192|
|Latência de E/S (aproximada)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|
|IOPS de dados máximo *|4.000|8,000|12.000|16.000|20.000|24.000|
|Taxa máxima de logs (MBps)|8|16|24|32|40|48|
|Máximo de trabalhos simultâneos (solicitações)|200|400|600|800|1000|1200|
|Máximo de logons simultâneos|200|400|600|800|1000|1200|
|Máximo de sessões simultâneas|30,000|30,000|30,000|30,000|30,000|30,000|
|Número de réplicas|4|4|4|4|4|4|
|Multi-AZ|Sim|Sim|Sim|Sim|Sim|Sim|
|Escala de leitura|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|

\* O valor máximo para tamanhos de e/s variando entre 8 KB e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](resource-limits-logical-server.md#resource-governance).

### <a name="gen4-compute-generation-part-2"></a>Geração de computação Gen4 (parte 2)

|Tamanho da computação (objetivo do serviço)|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Geração de computação|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|7|8|9|10|16|24|
|Memória (GB)|49|56|63|70|112|159,5|
|Suporte de columnstore|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|7|8|9,5|11|20|36|
|Tipo de armazenamento|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|
|Tamanho máximo de dados (GB)|1024|1024|1024|1024|1024|1024|
|Tamanho máximo de log (GB)|307|307|307|307|307|307|
|Tamanho máximo de dados de TempDB (GB)|224|256|288|320|512|768|
|Latência de E/S (aproximada)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|
|IOPS de dados máximo |28.000|32.000|36.000|40.000|64.000|76.800|
|Taxa máxima de logs (MBps)|56|64|64|64|64|64|
|Máximo de trabalhos simultâneos (solicitações)|1.400|1600|1800|2000|3200|4800|
|Máximo de logons simultâneos (solicitações)|1.400|1600|1800|2000|3200|4800|
|Máximo de sessões simultâneas|30,000|30,000|30,000|30,000|30,000|30,000|
|Número de réplicas|4|4|4|4|4|4|
|Multi-AZ|Sim|Sim|Sim|Sim|Sim|Sim|
|Escala de leitura|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|

\* O valor máximo para tamanhos de e/s variando entre 8 KB e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](resource-limits-logical-server.md#resource-governance).

## <a name="business-critical---provisioned-compute---gen5"></a>Comercialmente crítico-computação provisionada-Gen5

### <a name="gen5-compute-generation-part-1"></a>Geração de computação Gen5 (parte 1)

|Tamanho da computação (objetivo do serviço)|BC_Gen5_2|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Geração de computação|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|2|4|6|8|10|12|14|
|Memória (GB)|10.4|20,8|31,1|41,5|51,9|62,3|72,7|
|Suporte de columnstore|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|1,57|3.14|4,71|6,28|8,65|11, 2|13,39|
|Tamanho máximo de dados (GB)|1024|1024|1536|1536|1536|3072|3072|
|Tamanho máximo de log (GB)|307|307|461|461|461|922|922|
|Tamanho máximo de dados de TempDB (GB)|64|128|192|256|320|384|448|
|Tipo de armazenamento|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|
|Latência de E/S (aproximada)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|
|IOPS de dados máximo *|8000|16.000|24.000|32.000|40.000|48.000|56.000|
|Taxa máxima de logs (MBps)|24|48|72|96|96|96|96|
|Máximo de trabalhos simultâneos (solicitações)|200|400|600|800|1000|1200|1.400|
|Máximo de logons simultâneos|200|400|600|800|1000|1200|1.400|
|Máximo de sessões simultâneas|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Número de réplicas|4|4|4|4|4|4|4|
|Multi-AZ|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Escala de leitura|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|

\* O valor máximo para tamanhos de e/s variando entre 8 KB e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](resource-limits-logical-server.md#resource-governance).

### <a name="gen5-compute-generation-part-2"></a>Geração de computação Gen5 (parte 2)

|Tamanho da computação (objetivo do serviço)|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Geração de computação|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|16|18|20|24|32|40|80|
|Memória (GB)|83|93,4|103,8|124,6|166,1|207,6|415,2|
|Suporte de columnstore|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|15,77|18,14|20,51|25,25|37,94|52,23|131.64|
|Tamanho máximo de dados (GB)|3072|3072|3072|4096|4096|4096|4096|
|Tamanho máximo de log (GB)|922|922|922|1024|1024|1024|1024|
|Tamanho máximo de dados de TempDB (GB)|512|576|640|768|1024|1280|2560|
|Tipo de armazenamento|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|
|Latência de E/S (aproximada)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|
|IOPS de dados máximo *|64.000|72.000|80.000|96.000|128.000|160.000|204.800|
|Taxa máxima de logs (MBps)|96|96|96|96|96|96|96|
|Máximo de trabalhos simultâneos (solicitações)|1600|1800|2000|2400|3200|4000|8000|
|Máximo de logons simultâneos|1600|1800|2000|2400|3200|4000|8000|
|Máximo de sessões simultâneas|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Número de réplicas|4|4|4|4|4|4|4|
|Multi-AZ|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Escala de leitura|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|

\* O valor máximo para tamanhos de e/s variando entre 8 KB e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](resource-limits-logical-server.md#resource-governance).

## <a name="business-critical---provisioned-compute---m-series"></a>Computação comercialmente crítica-série M

### <a name="m-series-compute-generation-part-1"></a>Geração de computação da série M (parte 1)

|Tamanho da computação (objetivo do serviço)|BC_M_8|BC_M_10|BC_M_12|BC_M_14|BC_M_16|BC_M_18|
|:---| ---:|---:|---:|---:|---:|---:|
|Geração de computação|Série M|Série M|Série M|Série M|Série M|Série M|
|vCores|8|10|12|14|16|18|
|Memória (GB)|235,4|294,3|353,2|412,0|470,9|529,7|
|Suporte de columnstore|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|64|80|96|112|128|150|
|Tamanho máximo de dados (GB)|512|640|768|896|1024|1152|
|Tamanho máximo de log (GB)|171|213|256|299|341|384|
|Tamanho máximo de dados de TempDB (GB)|256|320|384|448|512|576|
|Tipo de armazenamento|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|
|Latência de E/S (aproximada)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|
|IOPS de dados máximo *|12.499|15.624|18.748|21.873|24.998|28.123|
|Taxa máxima de logs (MBps)|48|60|72|84|96|108|
|Máximo de trabalhos simultâneos (solicitações)|800|1,000|1.200|1.400|1.600|1.800|
|Máximo de logons simultâneos|800|1,000|1.200|1.400|1.600|1.800|
|Máximo de sessões simultâneas|30000|30000|30000|30000|30000|30000|
|Número de réplicas|4|4|4|4|4|4|
|Multi-AZ|Não|Não|Não|Não|Não|Não|
|Escala de leitura|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|

\* O valor máximo para tamanhos de e/s variando entre 8 KB e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](resource-limits-logical-server.md#resource-governance).

> [!IMPORTANT]
> Em algumas circunstâncias, talvez seja necessário reduzir um banco de dados para recuperar o espaço não utilizado. Para obter mais informações, consulte [gerenciar o espaço de arquivo no banco de dados SQL do Azure](file-space-manage.md).

### <a name="m-series-compute-generation-part-2"></a>Geração de computação da série M (parte 2)

|Tamanho da computação (objetivo do serviço)|BC_M_20|BC_M_24|BC_M_32|BC_M_64|BC_M_128|
|:---| ---:|---:|---:|---:|---:|
|Geração de computação|Série M|Série M|Série M|Série M|Série M|
|vCores|20|24|32|64|128|
|Memória (GB)|588,6|706,3|941,8|1883,5|3767,0|
|Suporte de columnstore|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|172|216|304|704|1768|
|Tamanho máximo de dados (GB)|1280|1536|2.048|4096|4096|
|Tamanho máximo de log (GB)|427|512|683|1024|1024|
|Tamanho máximo de dados de TempDB (GB)|4096|2.048|1024|768|640|
|Tipo de armazenamento|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|
|Latência de E/S (aproximada)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|
|IOPS de dados máximo *|31.248|37.497|49.996|99.993|160.000|
|Taxa máxima de logs (MBps)|120|144|192|264|264|
|Máximo de trabalhos simultâneos (solicitações)|2\.000|2.400|3\.200|6.400|12.800|
|Máximo de logons simultâneos|2\.000|2.400|3\.200|6.400|12.800|
|Máximo de sessões simultâneas|30000|30000|30000|30000|30000|
|Número de réplicas|4|4|4|4|4|
|Multi-AZ|Não|Não|Não|Não|Não|
|Escala de leitura|Sim|Sim|Sim|Sim|Sim|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|

\* O valor máximo para tamanhos de e/s variando entre 8 KB e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](resource-limits-logical-server.md#resource-governance).

> [!IMPORTANT]
> Em algumas circunstâncias, talvez seja necessário reduzir um banco de dados para recuperar o espaço não utilizado. Para obter mais informações, consulte [gerenciar o espaço de arquivo no banco de dados SQL do Azure](file-space-manage.md).

## <a name="business-critical---provisioned-compute---dc-series"></a>Negócio crítico-série de computação provisionada

|Tamanho da computação (objetivo do serviço)|BC_DC_2|BC_DC_4|BC_DC_6|BC_DC_8|
|:--- | --: |--: |--: |--: |
|Geração de computação|Série DC|Série DC|Série DC|Série DC|
|vCores|2|4|6|8|
|Memória (GB)|9|18|27|36|
|Suporte de columnstore|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|1.7|3.7|5.9|8.2|
|Tamanho máximo de dados (GB)|768|768|768|768|
|Tamanho máximo de log (GB)|230|230|230|230|
|Tamanho máximo de dados de TempDB (GB)|64|128|192|256|
|Tipo de armazenamento|SSD Local|SSD Local|SSD Local|SSD Local|
|Latência de E/S (aproximada)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|
|IOPS de dados máximo *|14000|28000|42000|44800|
|Taxa máxima de logs (MBps)|24|48|72|96|
|Máximo de trabalhos simultâneos (solicitações)|200|400|600|800|
|Máximo de logons simultâneos|200|400|600|800|
|Máximo de sessões simultâneas|30,000|30,000|30,000|30,000|
|Número de réplicas|4|4|4|4|
|Multi-AZ|Não|Não|Não|Não|
|Escala de leitura|Não|Não|Não|Não|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|

\* O valor máximo para tamanhos de e/s variando entre 8 KB e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](resource-limits-logical-server.md#resource-governance).


## <a name="next-steps"></a>Próximas etapas

- Para os limites de recursos de DTU para um único banco de dados, consulte [limites de recursos para bancos de dados individuais usando o modelo de compra de DTU](resource-limits-dtu-single-databases.md)
- Para limites de recursos vCore para pools elásticos, consulte [limites de recursos para pools elásticos usando o modelo de compra vCore](resource-limits-vcore-elastic-pools.md)
- Para os limites de recursos de DTU para pools elásticos, consulte [limites de recursos para pools elásticos usando o modelo de compra de DTU](resource-limits-dtu-elastic-pools.md)
- Para os limites de recursos para o SQL Instância Gerenciada, consulte [limites de recursos do sql instância gerenciada](../managed-instance/resource-limits.md).
- Para saber mais sobre limites gerais do Azure, confira [Assinatura do Azure e limites de serviço, cotas e restrições](../../azure-resource-manager/management/azure-subscription-service-limits.md).
- Para obter informações sobre limites de recursos em um servidor, consulte [visão geral dos limites de recursos em um servidor](resource-limits-logical-server.md) para obter informações sobre os limites nos níveis de servidor e assinatura.
