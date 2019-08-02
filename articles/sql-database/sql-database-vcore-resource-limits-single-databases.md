---
title: Limites de recursos baseados em vCore do Banco de Dados SQL do Azure – banco de dados individual | Microsoft Docs
description: Esta página descreve alguns limites de recursos baseados em vCore comuns para um banco de dados único no Banco de Dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 04/22/2019
ms.openlocfilehash: c89aa3b4ecf0c07cfbb579cdc18fac6e822bc047
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67536221"
---
# <a name="resource-limits-for-single-databases-using-the-vcore-based-purchasing-model"></a>Limites de recursos para bancos de dados individuais usando o modelo de compra baseado em vCore

Este artigo fornece os limites de recursos detalhados para bancos de dados individuais do Banco de Dados SQL do Azure usando o modelo de compra baseado em vCore.

Para limites do modelo de compra baseado em DTU para bancos de dados individuais em um servidor do Banco de Dados SQL, confira [Overview of resource limits on a SQL Database server](sql-database-resource-limits-database-server.md) (Visão geral dos limites de recursos em um servidor do Banco de Dados SQL).

> [!IMPORTANT]
> Em algumas circunstâncias, talvez seja necessário reduzir um banco de dados para recuperar o espaço não utilizado. Para obter mais informações, consulte [gerenciar o espaço de arquivo no banco de dados SQL do Azure](sql-database-file-space-management.md).

É possível definir a camada de serviço, o tamanho da computação e a quantidade de armazenamento para um banco de dados individual usando o [portal do Azure](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), o [Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-sql-database-servers-and-single-databases), o [PowerShell](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases), a [CLI do Azure](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases) ou a [API REST](sql-database-single-databases-manage.md#rest-api-manage-sql-database-servers-and-single-databases).

> [!IMPORTANT]
> Para dimensionar as diretrizes e considerações, consulte [dimensionar um único banco de dados](sql-database-single-database-scale.md).

## <a name="general-purpose-service-tier-storage-sizes-and-compute-sizes"></a>Camada de serviço de Uso Geral: Tamanhos de armazenamento e tamanhos da computação

> [!IMPORTANT]
> Não há suporte para novos bancos de dados Gen4 na região AustraliaEast.

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-1"></a>Camada de serviço de Uso Geral: Plataforma de computação de geração 4 (parte 1)

|Tamanho da computação|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|Geração de hardware|4|4|4|4|4|4|
|vCores|1|2|3|4|5|6|
|Memória (GB)|7|14|21|28|35|42|
|Suporte de columnstore|Sim|sim|sim|sim|sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (GB)|1024|1024|1024|1536|1536|1536|
|Tamanho máximo de log (GB)|307|307|307|461|461|461|
|Tamanho de TempDB (GB)|32|64|96|128|160|192|
|Tipo de armazenamento|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|
|Latência de E/S (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|
|IOPS de destino (64 KB)|500|1000|1500|2000|2500|3000|
|Limites de taxa de log (MBps)|3,75|7.5|11,25|15|18.75|22,5|
|Máximo de trabalhos simultâneos (solicitações)|200|400|600|800|1000|1\.200|
|Máximo permitido de sessões|30000|30000|30000|30000|30000|30000|
|Número de réplicas|1|1|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|
|Escala de leitura|N/D|N/D|N/D|N/D|N/D|N/D|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-2"></a>Camada de serviço de Uso Geral: Plataforma de computação de geração 4 (parte 2)

|Tamanho da computação|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24
|:--- | --: |--: |--: |--: |--: |--: |
|Geração de hardware|4|4|4|4|4|4|
|vCores|7|8|9|10|16|24|
|Memória (GB)|49|56|63|70|112|168|
|Suporte de columnstore|Sim|sim|sim|sim|sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (GB)|1536|3072|3072|3072|4096|4096|
|Tamanho máximo de log (GB)|461|922|922|922|1229|1229|
|Tamanho de TempDB (GB)|224|256|288|320|384|384|
|Tipo de armazenamento|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|
|Latência de E/S (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)
|IOPS de destino (64 KB)|3500|4000|4500|5\.000|7000|7000|
|Limites de taxa de log (MBps)|26.25|30|30|30|30|30|
|Máximo de trabalhos simultâneos (solicitações)|1\.400|1600|1800|2000|3200|4800|
|Máximo permitido de sessões|30000|30000|30000|30000|30000|30000|
|Número de réplicas|1|1|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|
|Escala de leitura|N/D|N/D|N/D|N/D|N/D|N/D|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-1"></a>Camada de serviço de Uso Geral: Plataforma de computação de geração 5 (parte 1)

|Tamanho da computação|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Geração de hardware|5|5|5|5|5|5|5|
|vCores|2|4|6|8|10|12|14|
|Memória (GB)|10.2|20,4|30,6|40,8|51|61,2|71,4|
|Suporte de columnstore|Sim|sim|sim|sim|sim|sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (GB)|1024|1024|1536|1536|1536|3072|3072|
|Tamanho máximo de log (GB)|307|307|307|461|461|461|461|
|Tamanho de TempDB (GB)|64|128|192|256|320|384|384|
|Tipo de armazenamento|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|
|Latência de E/S (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|
|IOPS de destino (64 KB)|1000|2000|3000|4000|5\.000|6000|7000|
|Limites de taxa de log (MBps)|3,75|7.5|11,25|15|18.75|22,5|26.25|
|Máximo de trabalhos simultâneos (solicitações)|200|400|600|800|1000|1\.200|1\.400|
|Máximo permitido de sessões|30000|30000|30000|30000|30000|30000|30000|
|Número de réplicas|1|1|1|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Escala de leitura|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-2"></a>Camada de serviço de Uso Geral: Plataforma de computação de geração 5 (parte 2)

|Tamanho da computação|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Geração de hardware|5|5|5|5|5|5|5|
|vCores|16|18|20|24|32|40|80|
|Memória (GB)|81,6|91,8|102|122,4|163,2|204|408|
|Suporte de columnstore|Sim|sim|sim|sim|sim|sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (GB)|3072|3072|3072|4096|4096|4096|4096|
|Tamanho máximo de log (GB)|922|922|922|1229|1229|1229|1229|
|Tamanho de TempDB (GB)|384|384|384|384|384|384|384|
|Tipo de armazenamento|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|
|Latência de E/S (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|
|IOPS de destino (64 KB)|7000|7000|7000|7000|7000|7000|7000|
|Limites de taxa de log (MBps)|30|30|30|30|30|30|30|
|Máximo de trabalhos simultâneos (solicitações)|1600|1800|2000|2400|3200|4000|8000|
|Máximo permitido de sessões|30000|30000|30000|30000|30000|30000|30000|
|Número de réplicas|1|1|1|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Escala de leitura|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|

### <a name="serverless-compute-tier"></a>Camada de computação sem servidor

O [camada de computação sem servidor](sql-database-serverless.md) está em visualização e é apenas para bancos de dados individuais usando a compra de vCore do modelo.

#### <a name="generation-5-compute-platform"></a>Plataforma de computação de Geração 5

|Tamanho da computação|GP_S_Gen5_1|GP_S_Gen5_2|GP_S_Gen5_4|
|:--- | --: |--: |--: |
|Geração de hardware|5|5|5|
|VCores Mín-Máx|0.5-1|0.5-2|0.5-4|
|Mín-Máx memória (GB)|2.02-3|2.05-6|2.10-12|
|Atraso de pausa automática min (horas)|6|6|6|
|Suporte de columnstore|Sim|sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|
|Tamanho máximo de dados (GB)|512|1024|1024|
|Tamanho máximo de log (GB)|12|24|48|
|Tamanho de TempDB (GB)|32|64|128|
|Tipo de armazenamento|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|
|Latência de E/S (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|
|IOPS de destino (64 KB)|500|1000|2000|
|Limites de taxa de log (MBps)|2.5|5.6|10|
|Máximo de trabalhos simultâneos (solicitações)|75|150|300|
|Máximo permitido de sessões|30000|30000|30000|
|Número de réplicas|1|1|1|
|Multi-AZ|N/D|N/D|N/D|
|Escala de leitura|N/D|N/D|N/D|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|

## <a name="business-critical-service-tier-for-provisioned-compute-tier"></a>Camada de serviço críticos de negócios para a camada de computação provisionada

> [!IMPORTANT]
> Não há suporte para novos bancos de dados Gen4 na região AustraliaEast.

### <a name="business-critical-service-tier-generation-4-compute-platform-part-1"></a>Camada de serviço Comercialmente Crítico: Plataforma de computação de geração 4 (parte 1)

|Tamanho da computação|BC_Gen4_1|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|Geração de hardware|4|4|4|4|4|4|
|vCores|1|2|3|4|5|6|
|Memória (GB)|7|14|21|28|35|42|
|Suporte de columnstore|Sim|sim|sim|sim|sim|Sim|
|Armazenamento OLTP na memória (GB)|1|2|3|4|5|6|
|Tipo de armazenamento|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|
|Tamanho máximo de dados (GB)|650|650|650|650|650|650|
|Tamanho máximo de log (GB)|195|195|195|195|195|195|
|Tamanho de TempDB (GB)|32|64|96|128|160|192|
|Latência de E/S (aproximada)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|
|IOPS de destino (64 KB)|5\.000|10000|15000|20000|25000|30000|
|Limites de taxa de log (MBps)|8|16|24|32|40|48|
|Máximo de trabalhos simultâneos (solicitações)|200|400|600|800|1000|1\.200|
|Máximo de logons simultâneos|200|400|600|800|1000|1\.200|
|Máximo permitido de sessões|30000|30000|30000|30000|30000|30000|
|Número de réplicas|4|4|4|4|4|4|
|Multi-AZ|Sim|sim|sim|sim|sim|Sim|
|Escala de leitura|Sim|sim|sim|sim|sim|Sim|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|

### <a name="business-critical-service-tier-generation-4-compute-platform-part-2"></a>Camada de serviço Comercialmente Crítico: Plataforma de computação de geração 4 (parte 2)

|Tamanho da computação|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Geração de hardware|4|4|4|4|4|4|
|vCores|7|8|9|10|16|24|
|Memória (GB)|49|56|63|70|112|168|
|Suporte de columnstore|Sim|sim|sim|sim|sim|Sim|
|Armazenamento OLTP na memória (GB)|7|8|9,5|11|20|36|
|Tipo de armazenamento|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|
|Tamanho máximo de dados (GB)|650|650|650|650|1024|1024|
|Tamanho máximo de log (GB)|195|195|195|195|307|307|
|Tamanho de TempDB (GB)|224|256|288|320|384|384|
|Latência de E/S (aproximada)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|
|IOPS de destino (64 KB)|35.000|40000|45000|50000|80000|120000|
|Limites de taxa de log (MBps)|56|64|64|64|64|64|
|Máximo de trabalhos simultâneos (solicitações)|1\.400|1600|1800|2000|3200|4800|
|Máximo de logons simultâneos (solicitações)|1\.400|1600|1800|2000|3200|4800|
|Máximo permitido de sessões|30000|30000|30000|30000|30000|30000|
|Número de réplicas|4|4|4|4|4|4|
|Multi-AZ|Sim|sim|sim|sim|sim|Sim|
|Escala de leitura|Sim|sim|sim|sim|sim|Sim|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|

### <a name="business-critical-service-tier-generation-5-compute-platform-part-1"></a>Camada de serviço Comercialmente Crítico: Plataforma de computação de geração 5 (parte 1)

|Tamanho da computação|BC_Gen5_2|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Geração de hardware|5|5|5|5|5|5|5|
|vCores|2|4|6|8|10|12|14|
|Memória (GB)|10.2|20,4|30,6|40,8|51|61,2|71,4|
|Suporte de columnstore|Sim|sim|sim|sim|sim|sim|Sim|
|Armazenamento OLTP na memória (GB)|1. 571|3.142|4,713|6.284|8,655|11,026|13,397|
|Tamanho máximo de dados (GB)|1024|1024|1536|1536|1536|3072|3072|
|Tamanho máximo de log (GB)|307|307|307|461|461|922|922|
|Tamanho de TempDB (GB)|64|128|192|256|320|384|384|
|Tipo de armazenamento|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|
|Latência de E/S (aproximada)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|
|IOPS de destino (64 KB)|8000|16000|24000|32000|40000|48000|56000|
|Limites de taxa de log (MBps)|12|24|36|48|60|72|84|
|Máximo de trabalhos simultâneos (solicitações)|200|400|600|800|1000|1\.200|1\.400|
|Máximo de logons simultâneos|200|400|600|800|1000|1\.200|1\.400|
|Máximo permitido de sessões|30000|30000|30000|30000|30000|30000|30000|
|Número de réplicas|4|4|4|4|4|4|4|
|Multi-AZ|Sim|sim|sim|sim|sim|sim|Sim|
|Escala de leitura|Sim|sim|sim|sim|sim|sim|Sim|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|

### <a name="business-critical-service-tier-generation-5-compute-platform-part-2"></a>Camada de serviço Comercialmente Crítico: Plataforma de computação de geração 5 (parte 2)

|Tamanho da computação|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|GP_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Geração de hardware|5|5|5|5|5|5|5|
|vCores|16|18|20|24|32|40|80|
|Memória (GB)|81,6|91,8|102|122,4|163,2|204|408|
|Suporte de columnstore|Sim|sim|sim|sim|sim|sim|Sim|
|Armazenamento OLTP na memória (GB)|15.768|18,139|20,51|25.252|37.936|52.22|131.64|
|Tamanho máximo de dados (GB)|3072|3072|3072|4096|4096|4096|4096|
|Tamanho máximo de log (GB)|922|922|922|1229|1229|1229|1229|
|Tamanho de TempDB (GB)|384|384|384|384|384|384|384|
|Tipo de armazenamento|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|
|Latência de E/S (aproximada)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|
|IOPS de destino (64 KB)|64000|72000|80000|96000|128000|160000|320000|
|Limites de taxa de log (MBps)|96|96|96|96|96|96|96|
|Máximo de trabalhos simultâneos (solicitações)|1600|1800|2000|2400|3200|4000|8000|
|Máximo de logons simultâneos|1600|1800|2000|2400|3200|4000|8000|
|Máximo permitido de sessões|30000|30000|30000|30000|30000|30000|30000|
|Número de réplicas|4|4|4|4|4|4|4|
|Multi-AZ|Sim|sim|sim|sim|sim|sim|Sim|
|Escala de leitura|Sim|sim|sim|sim|sim|sim|Sim|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|

## <a name="hyperscale-service-tier"></a>Tipo de serviço de Hiperescala

### <a name="generation-5-compute-platform"></a>Plataforma de computação de Geração 5

|Nível de desempenho|HS_Gen5_2|HS_Gen5_4|HS_Gen5_8|HS_Gen5_16|HS_Gen5_24|HS_Gen5_32|HS_Gen5_40|HS_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |
|Geração de hardware|5|5|5|5|5|5|5|5|
|vCores|2|4|8|16|24|32|40|80|
|Memória (GB)|10.2|20,4|40,8|81,6|122,4|163,2|204|408|
|Suporte de columnstore|Sim|sim|sim|sim|sim|sim|sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (TB)|100 |100 |100 |100 |100 |100 |100 |100 |
|Tamanho máximo do log (TB)|1 |1 |1 |1 |1 |1 |1 |1 |
|Tamanho de TempDB (GB)|64|128|256|384|384|384|384|384|
|Tipo de armazenamento|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|
|IOPS de destino (64 KB)| [Observação 1](#note-1) |[Observação 1](#note-1)|[Observação 1](#note-1) |[Observação 1](#note-1) |[Observação 1](#note-1) |[Observação 1](#note-1) |[Observação 1](#note-1) | [Observação 1](#note-1) |
|Latência de E/S (aproximada)|A ser determinado|A ser determinado|A ser determinado|A ser determinado|A ser determinado|A ser determinado|A ser determinado|A ser determinado|
|Máximo de trabalhos simultâneos (solicitações)|200|400|800|1600|2400|3200|4000|8000|
|Máximo permitido de sessões|30000|30000|30000|30000|30000|30000|30000|30000|
|Número de réplicas|2|2|2|2|2|2|2|2|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Escala de leitura|Sim|sim|sim|sim|sim|sim|sim|Sim|
|Armazenamento de backup incluído |7|7|7|7|7|7|7|7|
|||

### <a name="note-1"></a>Observação 1

Hiperescala é uma arquitetura de várias camadas com o cache em vários níveis. IOPS efetiva dependerá da carga de trabalho.

### <a name="next-steps"></a>Próximas etapas

- Para os limites de recursos de DTU de um único banco de dados, confira [limites de recursos para bancos de dados individuais usando o modelo de compra baseado em DTU](sql-database-dtu-resource-limits-single-databases.md)
- Para ver os limites de recursos do vCore para os pools elásticos, confira os [limites de recursos para pools elásticos usando o modelo de compras baseado em vCore](sql-database-vcore-resource-limits-elastic-pools.md)
- Para os limites de recursos de DTU dos pools elásticos, confira [limites de recursos para pools elásticos usando o modelo de compra baseado em DTU](sql-database-dtu-resource-limits-elastic-pools.md)
- Para os limites de recursos das instâncias gerenciadas, confira [limites de recursos para instâncias gerenciadas](sql-database-managed-instance-resource-limits.md).
- Para saber mais sobre limites gerais do Azure, confira [Assinatura do Azure e limites de serviço, cotas e restrições](../azure-subscription-service-limits.md).
- Para se informar sobre os limites de recursos em um servidor de banco de dados, confira a [visão geral dos limites de recursos em um servidor do Banco de Dados SQL](sql-database-resource-limits-database-server.md) para conferir os limites nos níveis do servidor e da assinatura.
