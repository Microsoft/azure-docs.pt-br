---
title: Limites de recursos baseados em vCore do Banco de Dados SQL do Azure – pool elásticos | Microsoft Docs
description: Esta página descreve alguns limites de recursos baseados em vCore comuns para pools elásticos no Banco de Dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab
manager: craigg
ms.date: 06/26/2019
ms.openlocfilehash: 4e63e1e477ce82221e5121815b609326cc2c1112
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447179"
---
# <a name="resource-limits-for-elastic-pools-using-the-vcore-based-purchasing-model-limits"></a>Limites de recursos para pools elásticos usando os limites do modelo de compra baseado no vCore

Este artigo fornece os limites de recursos detalhados para pools elásticos do Banco de Dados SQL do Azure e bancos de dados em pool usando o modelo de compra baseado em vCore.

Para limites de modelo de compra baseado em DTU, veja [Limites de recursos baseados em DTU de Banco de Dados SQL – pools elásticos](sql-database-dtu-resource-limits-elastic-pools.md).

> [!IMPORTANT]
> Em algumas circunstâncias, talvez seja necessário reduzir um banco de dados para recuperar o espaço não utilizado. Para obter mais informações, consulte [gerenciar o espaço de arquivo no banco de dados SQL do Azure](sql-database-file-space-management.md).

Você pode definir a camada de serviço, o tamanho da computação e a quantidade de armazenamento usando o [portal do Azure](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), o [PowerShell](sql-database-elastic-pool-manage.md#powershell-manage-elastic-pools-and-pooled-databases), a [CLI do Azure](sql-database-elastic-pool-manage.md#azure-cli-manage-elastic-pools-and-pooled-databases) ou a [API REST](sql-database-elastic-pool-manage.md#rest-api-manage-elastic-pools-and-pooled-databases).

> [!IMPORTANT]
> Para dimensionar as diretrizes e considerações, consulte [dimensionar um pool Elástico](sql-database-elastic-pool-scale.md)
> [!NOTE]
> Os limites de recursos de bancos de dados individuais em pools elásticos geralmente são os mesmos dos bancos de dados individuais fora dos pools com o mesmo tamanho da computação. Por exemplo, o máximo de trabalhos simultâneos para um banco de dados GP_Gen4_1 é 200. Assim, o máximo de trabalhos simultâneos para um banco de dados em um pool de GP_Gen4_1 também é 200. Observe que o número total de trabalhos simultâneos no pool de GP_Gen4_1 é 210.

## <a name="general-purpose-service-tier-storage-sizes-and-compute-sizes"></a>Camada de serviço de Uso Geral: Tamanhos de armazenamento e tamanhos da computação

> [!IMPORTANT]
> Não há suporte para novos bancos de dados Gen4 na região AustraliaEast.

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-1"></a>Camada de serviço de Uso Geral: Plataforma de computação de geração 4 (parte 1)

|Tamanho da computação|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|Geração de hardware|4|4|4|4|4|4|
|vCores|1|2|3|4|5|6|
|Memória (GB)|7|14|21|28|35|42|
|Número máximo de BDs por pool|100|200|500|500|500|500|
|Suporte de columnstore|Sim|sim|sim|sim|sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (GB)|512|756|756|1536|1536|1536|
|Tamanho máximo de log|154|227|227|461|461|461|
|Tamanho de TempDB (GB)|32|64|96|128|160|192|
|Tipo de armazenamento|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|
|Latência de E/S (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|
|IOPS de destino (64 KB)|500|1000|1500|2000|2500|3000|
|Limites de taxa de log (MBps)|4.6875|9.375|14.0625|18.75|23.4375|28.125|
|Máximo de trabalhos simultâneos por pool (solicitações)* |210|420|630|840|1\.050|1260|
|Máximo de logons simultâneos por pool * |210|420|630|840|1\.050|1260|
|Máximo permitido de sessões|30000|30000|30000|30000|30000|30000|
|Opções mínimas/máximas de vCore do pool elástico por banco de dados|0, 0,25, 0,5, 1|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1…3|0, 0,25, 0,5, 1…4|0, 0,25, 0,5, 1…5|0, 0,25, 0,5, 1…6|
|Número de réplicas|1|1|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|
|Escala de leitura|N/D|N/D|N/D|N/D|N/D|N/D|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|

\* Para o máximo de trabalhos (solicitações) simultâneos para qualquer banco de dados individual, confira [Limites de recursos do banco de dados individual](sql-database-vcore-resource-limits-single-databases.md). Por exemplo, se o pool elástico estiver usando Gen5 e seu máximo de vCore por banco de dados for 2, então o máximo de trabalhos simultâneos é 200.  Se máximo de vCore por banco de dados for 0,5, o máximo de trabalhos simultâneos é 50, pois há um máximo de 100 trabalhos simultâneos por vcore na Gen5.  Para outras configurações de máximo de vCore por banco de dados que sejam menores que 1 vCore, o número máximo de trabalhos simultâneos é redimensionado de forma semelhante.

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-2"></a>Camada de serviço de Uso Geral: Plataforma de computação de geração 4 (parte 2)

|Tamanho da computação|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Geração de hardware|4|4|4|4|4|4|
|vCores|7|8|9|10|16|24|
|Memória (GB)|49|56|63|70|112|168|
|Número máximo de BDs por pool|500|500|500|500|500|500|
|Suporte de columnstore|Sim|sim|sim|sim|sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (GB)|1536|2\.048|2\.048|2\.048|3584|4096|
|Tamanho máximo de log (GB)|461|614|614|614|1075|1229|
|Tamanho de TempDB (GB)|224|256|288|320|384|384|
|Tipo de armazenamento|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|
|Latência de E/S (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|
|IOPS de destino (64 KB)|3500|4000|4500|5\.000|7000|7000|
|Limites de taxa de log (MBps)|32.8125|37.5|37.5|37.5|37.5|37.5|
|Máximo de trabalhos simultâneos por pool (solicitações)*|1\.470|1680|1\.890|2\.100|3360|5040|
|Máximo do pool de logons simultâneos (solicitações) *|1\.470|1680|1\.890|2\.100|3360|5040|
|Máximo permitido de sessões|30000|30000|30000|30000|30000|30000|
|Opções mínimas/máximas de vCore do pool elástico por banco de dados|0, 0,25, 0,5, 1…7|0, 0,25, 0,5, 1…8|0, 0,25, 0,5, 1…9|0, 0,25, 0,5, 1…10|0, 0,25, 0,5, 1…10, 16|0, 0,25, 0,5, 1…10, 16, 24|
|Número de réplicas|1|1|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|
|Escala de leitura|N/D|N/D|N/D|N/D|N/D|N/D|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|

\* Para o máximo de trabalhos (solicitações) simultâneos para qualquer banco de dados individual, confira [Limites de recursos do banco de dados individual](sql-database-vcore-resource-limits-single-databases.md). Por exemplo, se o pool elástico estiver usando Gen5 e seu máximo de vCore por banco de dados for 2, então o máximo de trabalhos simultâneos é 200.  Se máximo de vCore por banco de dados for 0,5, o máximo de trabalhos simultâneos é 50, pois há um máximo de 100 trabalhos simultâneos por vcore na Gen5.  Para outras configurações de máximo de vCore por banco de dados que sejam menores que 1 vCore, o número máximo de trabalhos simultâneos é redimensionado de forma semelhante.

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-1"></a>Camada de serviço de Uso Geral: Plataforma de computação de geração 5 (parte 1)

|Tamanho da computação|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Geração de hardware|5|5|5|5|5|5|5|
|vCores|2|4|6|8|10|12|14|
|Memória (GB)|10.2|20,4|30,6|40,8|51|61,2|71,4|
|Número máximo de BDs por pool|100|200|500|500|500|500|500|
|Suporte de columnstore|Sim|sim|sim|sim|sim|sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (GB)|512|756|756|1536|1536|1536|
|Tamanho máximo de log (GB)|154|227|227|461|461|461|461|
|Tamanho de TempDB (GB)|64|128|192|256|320|384|384|
|Tipo de armazenamento|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|
|Latência de E/S (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|
|IOPS de destino (64 KB)|1000|2000|3000|4000|5\.000|6000|7000|
|Limites de taxa de log (MBps)|4.6875|9.375|14.0625|18.75|23.4375|28.125|32.8125|
|Máximo de trabalhos simultâneos por pool (solicitações)*|210|420|630|840|1\.050|1260|1\.470|
|Máximo de logons simultâneos por pool (solicitações) *|210|420|630|840|1\.050|1260|1\.470|
|Máximo permitido de sessões|30000|30000|30000|30000|30000|30000|30000|
|Opções mínimas/máximas de vCore do pool elástico por banco de dados|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1…4|0, 0,25, 0,5, 1…6|0, 0,25, 0,5, 1…8|0, 0,25, 0,5, 1…10|0, 0,25, 0,5, 1…12|0, 0,25, 0,5, 1…14|
|Número de réplicas|1|1|1|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Escala de leitura|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|

\* Para o máximo de trabalhos (solicitações) simultâneos para qualquer banco de dados individual, confira [Limites de recursos do banco de dados individual](sql-database-vcore-resource-limits-single-databases.md). Por exemplo, se o pool elástico estiver usando Gen5 e seu máximo de vCore por banco de dados for 2, então o máximo de trabalhos simultâneos é 200.  Se máximo de vCore por banco de dados for 0,5, o máximo de trabalhos simultâneos é 50, pois há um máximo de 100 trabalhos simultâneos por vcore na Gen5.  Para outras configurações de máximo de vCore por banco de dados que sejam menores que 1 vCore, o número máximo de trabalhos simultâneos é redimensionado de forma semelhante.

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-2"></a>Camada de serviço de Uso Geral: Plataforma de computação de geração 5 (parte 2)

|Tamanho da computação|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Geração de hardware|5|5|5|5|5|5|5|
|vCores|16|18|20|24|32|40|80|
|Memória (GB)|81,6|91,8|102|122,4|163,2|204|408|
|Número máximo de BDs por pool|500|500|500|500|500|500|500|
|Suporte de columnstore|Sim|sim|sim|sim|sim|sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (GB)|2\.048|2\.048|3072|3072|4096|4096|4096|
|Tamanho máximo de log (GB)|614|614|922|922|1229|1229|1229|
|Tamanho de TempDB (GB)|384|384|384|384|384|384|384|
|Tipo de armazenamento|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|
|Latência de E/S (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|
|IOPS de destino (64 KB)|7000|7000|7000|7000|7000|7000|7000|
|Limites de taxa de log (MBps)|37.5|37.5|37.5|37.5|37.5|37.5|37.5|
|Máximo de trabalhos simultâneos por pool (solicitações)*|1680|1\.890|2\.100|2520|33.600|4200|8400|
|Máximo de logons simultâneos por pool (solicitações) *|1680|1\.890|2\.100|2520|33.600|4200|8400|
|Opções mínimas/máximas de vCore do pool elástico por banco de dados|0, 0,25, 0,5, 1…16|0, 0,25, 0,5, 1…18|0, 0,25, 0,5, 1…20|0, 0,25, 0,5, 1…20, 24|0, 0,25, 0,5, 1…20, 24, 32|0, 0,25, 0,5, 1…16, 24, 32, 40|0, 0.25, 0,5, 1…16, 24, 32, 40, 80|
|Número de réplicas|1|1|1|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Escala de leitura|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|

\* Para o máximo de trabalhos (solicitações) simultâneos para qualquer banco de dados individual, confira [Limites de recursos do banco de dados individual](sql-database-vcore-resource-limits-single-databases.md). Por exemplo, se o pool elástico estiver usando Gen5 e seu máximo de vCore por banco de dados for 2, então o máximo de trabalhos simultâneos é 200.  Se máximo de vCore por banco de dados for 0,5, o máximo de trabalhos simultâneos é 50, pois há um máximo de 100 trabalhos simultâneos por vcore na Gen5.  Para outras configurações de máximo de vCore por banco de dados que sejam menores que 1 vCore, o número máximo de trabalhos simultâneos é redimensionado de forma semelhante.

## <a name="business-critical-service-tier-storage-sizes-and-compute-sizes"></a>Camada de serviço Comercialmente Crítico: Tamanhos de armazenamento e tamanhos da computação

> [!IMPORTANT]
> Não há suporte para novos bancos de dados Gen4 na região AustraliaEast.

### <a name="business-critical-service-tier-generation-4-compute-platform-part-1"></a>Camada de serviço Comercialmente Crítico: Plataforma de computação de geração 4 (parte 1)

|Tamanho da computação|BC_Gen4_1|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|Geração de hardware|4|4|4|4|4|4|
|vCores|1|2|3|4|5|6|
|Memória (GB)|7|14|21|28|35|42|
|Número máximo de BDs por pool|Somente bancos de dados individuais têm suporte nesse tamanho da computação|50|100|100|100|100|
|Suporte de columnstore|Sim|sim|sim|sim|sim|Sim|
|Armazenamento OLTP na memória (GB)|1|2|3|4|5|6|
|Tipo de armazenamento|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|
|Tamanho máximo de dados (GB)|650|650|650|650|650|650|
|Tamanho máximo de log (GB)|195|195|195|195|195|195|
|Tamanho de TempDB (GB)|32|64|96|128|160|192|
|Latência de E/S (aproximada)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|
|IOPS de destino (64 KB)|5\.000|10000|15000|20000|25000|30000|
|Limites de taxa de log (MBps)|10|20|30|40|50|60|
|Máximo de trabalhos simultâneos por pool (solicitações)*|210|420|630|840|1\.050|1260|
|Máximo de logons simultâneos por pool (solicitações) *|210|420|630|840|1\.050|1260|
|Máximo permitido de sessões|30000|30000|30000|30000|30000|30000|
|Opções mínimas/máximas de vCore do pool elástico por banco de dados|N/D|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1…3|0, 0,25, 0,5, 1…4|0, 0,25, 0,5, 1…5|0, 0,25, 0,5, 1…6|
|Número de réplicas|4|4|4|4|4|4|
|Multi-AZ|Sim|sim|sim|sim|sim|Sim|
|Escala de leitura|Sim|sim|sim|sim|sim|Sim|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|

\* Para o máximo de trabalhos (solicitações) simultâneos para qualquer banco de dados individual, confira [Limites de recursos do banco de dados individual](sql-database-vcore-resource-limits-single-databases.md). Por exemplo, se o pool elástico estiver usando Gen5 e seu máximo de vCore por banco de dados for 2, então o máximo de trabalhos simultâneos é 200.  Se máximo de vCore por banco de dados for 0,5, o máximo de trabalhos simultâneos é 50, pois há um máximo de 100 trabalhos simultâneos por vcore na Gen5.  Para outras configurações de máximo de vCore por banco de dados que sejam menores que 1 vCore, o número máximo de trabalhos simultâneos é redimensionado de forma semelhante.

### <a name="business-critical-service-tier-generation-4-compute-platform-part-2"></a>Camada de serviço Comercialmente Crítico: Plataforma de computação de geração 4 (parte 2)

|Tamanho da computação|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Geração de hardware|4|4|4|4|4|4|
|vCores|7|8|9|10|16|24|
|Memória (GB)|81,6|91,8|102|122,4|163,2|204|
|Número máximo de BDs por pool|100|100|100|100|100|100|
|Suporte de columnstore|N/D|N/D|N/D|N/D|N/D|N/D|
|Armazenamento OLTP na memória (GB)|7|8|9,5|11|20|36|
|Tipo de armazenamento|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|
|Tamanho máximo de dados (GB)|650|650|650|650|1024|1024|
|Tamanho máximo de log (GB)|195|195|195|195|307|307|
|Tamanho de TempDB (GB)|224|256|288|320|384|384|
|Latência de E/S (aproximada)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|
|IOPS de destino (64 KB)|35.000|40000|45000|50000|80000|120000|
|Limites de taxa de log (MBps)|70|80|80|80|80|80|
|Máximo de trabalhos simultâneos por pool (solicitações)*|1\.470|1680|1\.890|2\.100|3360|5040|
|Máximo de logons simultâneos por pool (solicitações) *|1\.470|1680|1\.890|2\.100|3360|5040|
|Máximo permitido de sessões|30000|30000|30000|30000|30000|30000|
|Opções mínimas/máximas de vCore do pool elástico por banco de dados|0, 0,25, 0,5, 1…7|0, 0,25, 0,5, 1…8|0, 0,25, 0,5, 1…9|0, 0,25, 0,5, 1…10|0, 0,25, 0,5, 1…10, 16|0, 0,25, 0,5, 1…10, 16, 24|
|Número de réplicas|4|4|4|4|4|4|
|Multi-AZ|Sim|sim|sim|sim|sim|Sim|
|Escala de leitura|Sim|sim|sim|sim|sim|Sim|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|

\* Para o máximo de trabalhos (solicitações) simultâneos para qualquer banco de dados individual, confira [Limites de recursos do banco de dados individual](sql-database-vcore-resource-limits-single-databases.md). Por exemplo, se o pool elástico estiver usando Gen5 e seu máximo de vCore por banco de dados for 2, então o máximo de trabalhos simultâneos é 200.  Se máximo de vCore por banco de dados for 0,5, o máximo de trabalhos simultâneos é 50, pois há um máximo de 100 trabalhos simultâneos por vcore na Gen5.  Para outras configurações de máximo de vCore por banco de dados que sejam menores que 1 vCore, o número máximo de trabalhos simultâneos é redimensionado de forma semelhante.

#### <a name="business-critical-service-tier-generation-5-compute-platform-part-1"></a>Camada de serviço Comercialmente Crítico: Plataforma de computação de geração 5 (parte 1)

|Tamanho da computação|BC_Gen5_2|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Geração de hardware|5|5|5|5|5|5|5|
|vCores|2|4|6|8|10|12|14|
|Memória (GB)|10.2|20,4|30,6|40,8|51|61,2|71,4|
|Número máximo de BDs por pool|Somente bancos de dados individuais têm suporte nesse tamanho da computação|50|100|100|100|100|100|
|Suporte de columnstore|Sim|sim|sim|sim|sim|sim|Sim|
|Armazenamento OLTP na memória (GB)|1. 571|3.142|4,713|6.284|8,655|11,026|13,397|
|Tamanho máximo de dados (GB)|1024|1024|1536|1536|1536|3072|3072|
|Tamanho máximo de log (GB)|307|307|307|461|461|922|922|
|Tamanho de TempDB (GB)|64|128|192|256|320|384|384|
|Tipo de armazenamento|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|
|Latência de E/S (aproximada)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|
|IOPS de destino (64 KB)|5\.000|10000|15000|20000|25000|30000|35.000|
|Limites de taxa de log (MBps)|15|30|45|60|75|90|105|
|Máximo de trabalhos simultâneos por pool (solicitações)*|210|420|630|840|1\.050|1260|1\.470|
|Máximo de logons simultâneos por pool (solicitações) *|210|420|630|840|1\.050|1260|1\.470|
|Máximo permitido de sessões|30000|30000|30000|30000|30000|30000|30000|
|Opções mínimas/máximas de vCore do pool elástico por banco de dados|N/D|0, 0,25, 0,5, 1…4|0, 0,25, 0,5, 1…6|0, 0,25, 0,5, 1…8|0, 0,25, 0,5, 1…10|0, 0,25, 0,5, 1…12|0, 0,25, 0,5, 1…14|
|Número de réplicas|4|4|4|4|4|4|4|
|Multi-AZ|Sim|sim|sim|sim|sim|Sim|
|Escala de leitura|Sim|sim|sim|sim|sim|sim|Sim|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|

\* Para o máximo de trabalhos (solicitações) simultâneos para qualquer banco de dados individual, confira [Limites de recursos do banco de dados individual](sql-database-vcore-resource-limits-single-databases.md). Por exemplo, se o pool elástico estiver usando Gen5 e seu máximo de vCore por banco de dados for 2, então o máximo de trabalhos simultâneos é 200.  Se máximo de vCore por banco de dados for 0,5, o máximo de trabalhos simultâneos é 50, pois há um máximo de 100 trabalhos simultâneos por vcore na Gen5.  Para outras configurações de máximo de vCore por banco de dados que sejam menores que 1 vCore, o número máximo de trabalhos simultâneos é redimensionado de forma semelhante.

#### <a name="business-critical-service-tier-generation-5-compute-platform-part-2"></a>Camada de serviço Comercialmente Crítico: Plataforma de computação de geração 5 (parte 2)

|Tamanho da computação|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|GP_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Geração de hardware|5|5|5|5|5|5|5|
|vCores|16|18|20|24|32|40|80|
|Memória (GB)|81,6|91,8|102|122,4|163,2|204|408|
|Número máximo de BDs por pool|100|100|100|100|100|100|100|
|Suporte de columnstore|Sim|sim|sim|sim|sim|sim|Sim|
|Armazenamento OLTP na memória (GB)|15.768|18,139|20,51|25.252|37.936|52.22|131.64|
|Tamanho máximo de dados (GB)|3072|3072|3072|4096|4096|4096|4096|
|Tamanho máximo de log (GB)|922|922|922|1229|1229|1229|1229|
|Tamanho de TempDB (GB)|384|384|384|384|384|384|384|
|Tipo de armazenamento|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|
|Latência de E/S (aproximada)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|
|IOPS de destino (64 KB)|40000|45000|50000|60000|80000|100000|200000|
|Limites de taxa de log (MBps)|120|120|120|120|120|120|120|
|Máximo de trabalhos simultâneos por pool (solicitações)*|1680|1\.890|2\.100|2520|3360|4200|8400|
|Máximo de logons simultâneos por pool (solicitações) *|1680|1\.890|2\.100|2520|3360|4200|8400|
|Máximo permitido de sessões|30000|30000|30000|30000|30000|30000|30000|
|Opções mínimas/máximas de vCore do pool elástico por banco de dados|0, 0,25, 0,5, 1…16|0, 0,25, 0,5, 1…18|0, 0,25, 0,5, 1…20|0, 0,25, 0,5, 1…20, 24|0, 0,25, 0,5, 1…20, 24, 32|0, 0,25, 0,5, 1…20, 24, 32, 40|0, 0,25, 0,5, 1…20, 24, 32, 40, 80|
|Número de réplicas|4|4|4|4|4|4|4|
|Multi-AZ|Sim|sim|sim|sim|sim|sim|Sim|
|Escala de leitura|Sim|sim|sim|sim|sim|sim|Sim|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|

\* Para o máximo de trabalhos (solicitações) simultâneos para qualquer banco de dados individual, confira [Limites de recursos do banco de dados individual](sql-database-vcore-resource-limits-single-databases.md). Por exemplo, se o pool elástico estiver usando Gen5 e seu máximo de vCore por banco de dados for 2, então o máximo de trabalhos simultâneos é 200.  Se máximo de vCore por banco de dados for 0,5, o máximo de trabalhos simultâneos é 50, pois há um máximo de 100 trabalhos simultâneos por vcore na Gen5.  Para outras configurações de máximo de vCore por banco de dados que sejam menores que 1 vCore, o número máximo de trabalhos simultâneos é redimensionado de forma semelhante.

Se todos os vCores de um pool elástico estiverem ocupados, cada banco de dados no pool receberá uma quantidade igual de recursos de computação para processar as consultas. O serviço de Banco de Dados SQL fornece integridade de compartilhamento de recursos entre os bancos de dados ao garantir fatias iguais de tempo de computação. A integridade de compartilhamento de recursos do pool elástico é adicional a qualquer quantidade de recursos garantidos de outra forma a cada banco de dados quando o mínimo de vCores por banco de dados é definido com um valor diferente de zero.

## <a name="database-properties-for-pooled-databases"></a>Propriedades do banco de dados para bancos de dados em pool

A tabela a seguir descreve as propriedades dos bancos de dados em pool.

| Propriedade | DESCRIÇÃO |
|:--- |:--- |
| Máximo de vCores por banco de dados |O número máximo de vCores que qualquer banco de dados no pool pode usar, se disponível, com base na utilização por outros bancos de dados no pool. O máximo de vCores por banco de dados não é uma garantia de recursos para um banco de dados. Essa configuração é uma configuração global que se aplica a todos os bancos de dados no pool. Defina um valor para o máximo de vCores por banco de dados que seja alto o suficiente para lidar com picos de utilização do banco de dados. Espera-se um grau de sobrecarga, uma vez que o pool normalmente assume padrões de uso dos bancos de dados com altos e baixos, em que todos os bancos de dados não atingem um pico simultaneamente.|
| Mínimo de VCores por banco de dados |O número mínimo de vCores garantido para qualquer banco de dados no pool. Essa configuração é uma configuração global que se aplica a todos os bancos de dados no pool. O mínimo de vCores por banco de dados pode ser definido como 0 e também é o valor padrão. Essa propriedade é definida entre 0 e a utilização média de vCores por banco de dados. O produto do número de bancos de dados no pool e o mínimo de vCores por banco de dados não pode exceder os vCores por pool.|
| Armazenamento máximo por banco de dados |O tamanho máximo do banco de dados definido pelo usuário para um banco de dados em um pool. Os bancos de dados em pool compartilham armazenamento de pool alocado, para que o tamanho que um banco de dados possa atingir seja limitado ao menor tamanho de banco de dados e armazenamento de pool restante. O tamanho máximo por banco de dados refere-se ao tamanho máximo dos arquivos de dados e não inclui o espaço usado pelos arquivos de log. |
|||

## <a name="next-steps"></a>Próximas etapas

- Para os limites de recursos do vCore de um único banco de dados, confira os [limites de recursos para bancos de dados individuais usando o modelo de compra baseado no vCore](sql-database-vcore-resource-limits-single-databases.md)
- Para os limites de recursos de DTU de um único banco de dados, confira [limites de recursos para bancos de dados individuais usando o modelo de compra baseado em DTU](sql-database-dtu-resource-limits-single-databases.md)
- Para os limites de recursos de DTU dos pools elásticos, confira [limites de recursos para pools elásticos usando o modelo de compra baseado em DTU](sql-database-dtu-resource-limits-elastic-pools.md)
- Para os limites de recursos das instâncias gerenciadas, confira [limites de recursos para instâncias gerenciadas](sql-database-managed-instance-resource-limits.md).
- Para saber mais sobre limites gerais do Azure, confira [Assinatura do Azure e limites de serviço, cotas e restrições](../azure-subscription-service-limits.md).
- Para se informar sobre os limites de recursos em um servidor de banco de dados, confira a [visão geral dos limites de recursos em um servidor do Banco de Dados SQL](sql-database-resource-limits-database-server.md) para conferir os limites nos níveis do servidor e da assinatura.
