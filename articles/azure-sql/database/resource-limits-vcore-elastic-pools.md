---
title: Limites de recursos vCore do pool elástico
description: Esta página descreve alguns limites comuns de recursos vCore para pools elásticos no banco de dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: reference
author: oslake
ms.author: moslake
ms.reviewer: sstein
ms.date: 01/22/2021
ms.openlocfilehash: 619f4deee81c97f2d7a0b4359e2b999c476b1ae2
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98737550"
---
# <a name="resource-limits-for-elastic-pools-using-the-vcore-purchasing-model"></a>Limites de recursos para pools elásticos usando o modelo de compra vCore
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Este artigo fornece os limites de recursos detalhados para os pools elásticos do banco de dados SQL do Azure e bancos de dados em pool usando o modelo de compra vCore.

Para limites de modelo de compra de DTU, consulte [limites de recursos de DTU do banco de dados SQL – pools elásticos](resource-limits-dtu-elastic-pools.md).

> [!IMPORTANT]
> Em algumas circunstâncias, talvez seja necessário reduzir um banco de dados para recuperar o espaço não utilizado. Para obter mais informações, consulte [gerenciar o espaço de arquivo no banco de dados SQL do Azure](file-space-manage.md).

Você pode definir a camada de serviço, o tamanho da computação (objetivo do serviço) e o valor de armazenamento usando o [portal do Azure](elastic-pool-manage.md#azure-portal), o [PowerShell](elastic-pool-manage.md#powershell), o [CLI do Azure](elastic-pool-manage.md#azure-cli)ou a [API REST](elastic-pool-manage.md#rest-api).

> [!IMPORTANT]
> Para obter diretrizes e considerações sobre o dimensionamento, consulte [dimensionar um pool elástico](elastic-pool-scale.md).

## <a name="general-purpose---provisioned-compute---gen4"></a>Computação provisionada de uso geral-Gen4

> [!IMPORTANT]
> Novos bancos de dados Gen4 não têm mais suporte nas regiões leste da Austrália ou sul do Brasil.

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-1"></a>Camada de serviço de uso geral: plataforma de computação de geração 4 (parte 1)

|Tamanho da computação (objetivo do serviço)|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|Geração de computação|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|1|2|3|4|5|6|
|Memória (GB)|7|14|21|28|35|42|
|Número máximo de bancos de os por pool <sup>1</sup>|100|200|500|500|500|500|
|Suporte de columnstore|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (GB)|512|756|1536|1536|1536|2.048|
|Tamanho máximo do log|154|227|461|461|461|614|
|Tamanho máximo de dados de TempDB (GB)|32|64|96|128|160|192|
|Tipo de armazenamento|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|
|Latência de E/S (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|
|IOPS de dados máximo por pool <sup>2</sup> |400|800|1200|1600|2000|2400|
|Taxa máxima de log por pool (MBps)|6|12|18|24|30|36|
|Máximo de trabalhos simultâneos por pool (solicitações) <sup>3</sup> |210|420|630|840|1.050|1260|
|Máximo de logons simultâneos por pool <sup>3</sup> |210|420|630|840|1.050|1260|
|Máximo de sessões simultâneas|30,000|30,000|30,000|30,000|30,000|30,000|
|Opções mínimas/máximas de vCore do pool elástico por banco de dados|0, 0,25, 0,5, 1|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1…3|0, 0,25, 0,5, 1…4|0, 0,25, 0,5, 1…5|0, 0,25, 0,5, 1…6|
|Número de réplicas|1|1|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|
|Escala de leitura|N/D|N/D|N/D|N/D|N/D|N/D|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|

<sup>1</sup> consulte [Gerenciamento de recursos em pools elásticos densos](elastic-pool-resource-management.md) para obter considerações adicionais.

<sup>2</sup> o valor máximo para tamanhos de e/s variando entre 8 kb e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> para o máximo de trabalhos simultâneos (solicitações) para qualquer banco de dados individual, consulte [limites de recurso de banco de dados único](resource-limits-vcore-single-databases.md). Por exemplo, se o pool elástico estiver usando Gen5 e o vCore máximo por banco de dados for definido como 2, o valor máximo de trabalhos simultâneos será de 200.  Se o vCore máximo por banco de dados for definido como 0,5, o valor máximo de trabalhos simultâneos será 50, pois, em Gen5, há um máximo de 100 trabalhos simultâneos por vCore. Para outras configurações de máximo de vCore por banco de dados que sejam menores que 1 vCore, o número máximo de trabalhos simultâneos é redimensionado de forma semelhante.

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-2"></a>Camada de serviço de uso geral: plataforma de computação de geração 4 (parte 2)

|Tamanho da computação (objetivo do serviço)|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Geração de computação|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|7|8|9|10|16|24|
|Memória (GB)|49|56|63|70|112|159,5|
|Número máximo de bancos de os por pool <sup>1</sup>|500|500|500|500|500|500|
|Suporte de columnstore|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (GB)|2.048|2.048|2.048|2.048|3584|4096|
|Tamanho máximo de log (GB)|614|614|614|614|1075|1229|
|Tamanho máximo de dados de TempDB (GB)|224|256|288|320|512|768|
|Tipo de armazenamento|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|
|Latência de E/S (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|
|IOPS de dados máximo por pool <sup>2</sup>|2800|3200|3600|4000|6400|9600|
|Taxa máxima de log por pool (MBps)|42|48|48|48|48|48|
|Máximo de trabalhos simultâneos por pool (solicitações) <sup>3</sup>|1.470|1680|1.890|2.100|3360|5040|
|Pool de logons simultâneos máximos (solicitações) <sup>3</sup>|1.470|1680|1.890|2.100|3360|5040|
|Máximo de sessões simultâneas|30,000|30,000|30,000|30,000|30,000|30,000|
|Opções mínimas/máximas de vCore do pool elástico por banco de dados|0, 0,25, 0,5, 1…7|0, 0,25, 0,5, 1…8|0, 0,25, 0,5, 1…9|0, 0,25, 0,5, 1…10|0, 0,25, 0,5, 1…10, 16|0, 0,25, 0,5, 1…10, 16, 24|
|Número de réplicas|1|1|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|
|Escala de leitura|N/D|N/D|N/D|N/D|N/D|N/D|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|

<sup>1</sup> consulte [Gerenciamento de recursos em pools elásticos densos](elastic-pool-resource-management.md) para obter considerações adicionais.

<sup>2</sup> o valor máximo para tamanhos de e/s variando entre 8 kb e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](resource-limits-logical-server.md#resource-governance).    

<sup>3</sup> para o máximo de trabalhos simultâneos (solicitações) para qualquer banco de dados individual, consulte [limites de recurso de banco de dados único](resource-limits-vcore-single-databases.md). Por exemplo, se o pool elástico estiver usando Gen5 e o vCore máximo por banco de dados for definido como 2, o valor máximo de trabalhos simultâneos será de 200.  Se o vCore máximo por banco de dados for definido como 0,5, o valor máximo de trabalhos simultâneos será 50, pois, em Gen5, há um máximo de 100 trabalhos simultâneos por vCore. Para outras configurações de máximo de vCore por banco de dados que sejam menores que 1 vCore, o número máximo de trabalhos simultâneos é redimensionado de forma semelhante.

## <a name="general-purpose---provisioned-compute---gen5"></a>Computação provisionada de uso geral-Gen5

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-1"></a>Camada de serviço de uso geral: plataforma de computação de geração 5 (parte 1)

|Tamanho da computação (objetivo do serviço)|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Geração de computação|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|2|4|6|8|10|12|14|
|Memória (GB)|10.4|20,8|31,1|41,5|51,9|62,3|72,7|
|Número máximo de bancos de os por pool <sup>1</sup>|100|200|500|500|500|500|500|
|Suporte de columnstore|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (GB)|512|756|1536|1536|1536|2.048|2.048|
|Tamanho máximo de log (GB)|154|227|461|461|461|614|614|
|Tamanho máximo de dados de TempDB (GB)|64|128|192|256|320|384|448|
|Tipo de armazenamento|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|
|Latência de E/S (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|
|IOPS de dados máximo por pool <sup>2</sup>|800|1600|2400|3200|4000|4800|5600|
|Taxa máxima de log por pool (MBps)|12|24|36|48|48|48|48|
|Máximo de trabalhos simultâneos por pool (solicitações) <sup>3</sup>|210|420|630|840|1.050|1260|1.470|
|Máximo de logons simultâneos por pool (solicitações) <sup>3</sup>|210|420|630|840|1.050|1260|1.470|
|Máximo de sessões simultâneas|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Opções mínimas/máximas de vCore do pool elástico por banco de dados|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1…4|0, 0,25, 0,5, 1…6|0, 0,25, 0,5, 1…8|0, 0,25, 0,5, 1…10|0, 0,25, 0,5, 1…12|0, 0,25, 0,5, 1…14|
|Número de réplicas|1|1|1|1|1|1|1|
|Multi-AZ|[Disponível na visualização](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Disponível na visualização](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Disponível na visualização](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Disponível na visualização](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Disponível na visualização](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Disponível na visualização](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Disponível na visualização](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|
|Escala de leitura|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|

<sup>1</sup> consulte [Gerenciamento de recursos em pools elásticos densos](elastic-pool-resource-management.md) para obter considerações adicionais.

<sup>2</sup> o valor máximo para tamanhos de e/s variando entre 8 kb e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> para o máximo de trabalhos simultâneos (solicitações) para qualquer banco de dados individual, consulte [limites de recurso de banco de dados único](resource-limits-vcore-single-databases.md). Por exemplo, se o pool elástico estiver usando Gen5 e o vCore máximo por banco de dados for definido como 2, o valor máximo de trabalhos simultâneos será de 200.  Se o vCore máximo por banco de dados for definido como 0,5, o valor máximo de trabalhos simultâneos será 50, pois, em Gen5, há um máximo de 100 trabalhos simultâneos por vCore. Para outras configurações de máximo de vCore por banco de dados que sejam menores que 1 vCore, o número máximo de trabalhos simultâneos é redimensionado de forma semelhante.

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-2"></a>Camada de serviço de uso geral: plataforma de computação de geração 5 (parte 2)

|Tamanho da computação (objetivo do serviço)|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Geração de computação|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|16|18|20|24|32|40|80|
|Memória (GB)|83|93,4|103,8|124,6|166,1|207,6|415,2|
|Número máximo de bancos de os por pool <sup>1</sup>|500|500|500|500|500|500|500|
|Suporte de columnstore|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (GB)|2.048|3072|3072|3072|4096|4096|4096|
|Tamanho máximo de log (GB)|614|922|922|922|1229|1229|1229|
|Tamanho máximo de dados de TempDB (GB)|512|576|640|768|1024|1280|2560|
|Tipo de armazenamento|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|
|Latência de E/S (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|
|IOPS de dados máximo por pool <sup>2</sup> |6.400|7.200|8,000|9.600|12.800|16.000|16.000|
|Taxa máxima de log por pool (MBps)|48|48|48|48|48|48|48|
|Máximo de trabalhos simultâneos por pool (solicitações) <sup>3</sup>|1680|1.890|2.100|2520|3360|4200|8400|
|Máximo de logons simultâneos por pool (solicitações) <sup>3</sup>|1680|1.890|2.100|2520|3360|4200|8400|
|Máximo de sessões simultâneas|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Opções mínimas/máximas de vCore do pool elástico por banco de dados|0, 0,25, 0,5, 1…16|0, 0,25, 0,5, 1…18|0, 0,25, 0,5, 1…20|0, 0,25, 0,5, 1…20, 24|0, 0,25, 0,5, 1…20, 24, 32|0, 0,25, 0,5, 1…16, 24, 32, 40|0, 0.25, 0,5, 1…16, 24, 32, 40, 80|
|Número de réplicas|1|1|1|1|1|1|1|
|Multi-AZ|[Disponível na visualização](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Disponível na visualização](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Disponível na visualização](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Disponível na visualização](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Disponível na visualização](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Disponível na visualização](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Disponível na visualização](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|
|Escala de leitura|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|

<sup>1</sup> consulte [Gerenciamento de recursos em pools elásticos densos](elastic-pool-resource-management.md) para obter considerações adicionais.

<sup>2</sup> o valor máximo para tamanhos de e/s variando entre 8 kb e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> para o máximo de trabalhos simultâneos (solicitações) para qualquer banco de dados individual, consulte [limites de recurso de banco de dados único](resource-limits-vcore-single-databases.md). Por exemplo, se o pool elástico estiver usando Gen5 e o vCore máximo por banco de dados for definido como 2, o valor máximo de trabalhos simultâneos será de 200.  Se o vCore máximo por banco de dados for definido como 0,5, o valor máximo de trabalhos simultâneos será 50, pois, em Gen5, há um máximo de 100 trabalhos simultâneos por vCore. Para outras configurações de máximo de vCore por banco de dados que sejam menores que 1 vCore, o número máximo de trabalhos simultâneos é redimensionado de forma semelhante.

## <a name="general-purpose---provisioned-compute---fsv2-series"></a>Computação provisionada de uso geral-série Fsv2

### <a name="fsv2-series-compute-generation-part-1"></a>Geração de computação da série Fsv2 (parte 1)

|Tamanho da computação (objetivo do serviço)|GP_Fsv2_8|GP_Fsv2_10|GP_Fsv2_12|GP_Fsv2_14| GP_Fsv2_16|
|:---| ---:|---:|---:|---:|---:|
|Geração de computação|Série Fsv2|Série Fsv2|Série Fsv2|Série Fsv2|Série Fsv2|
|vCores|8|10|12|14|16|
|Memória (GB)|15.1|18,9|22,7|26,5|30,2|
|Número máximo de bancos de os por pool <sup>1</sup>|500|500|500|500|500|
|Suporte de columnstore|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (GB)|1024|1024|1024|1024|1536|
|Tamanho máximo de log (GB)|336|336|336|336|512|
|Tamanho máximo de dados de TempDB (GB)|333|333|333|333|333|
|Tipo de armazenamento|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|
|Latência de E/S (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|
|IOPS de dados máximo por pool <sup>2</sup>|2560|3200|3840|4480|5120|
|Taxa máxima de log por pool (MBps)|48|48|48|48|48|
|Máximo de trabalhos simultâneos por pool (solicitações) <sup>3</sup>|400|500|600|700|800|
|Máximo de logons simultâneos por pool (solicitações) <sup>3</sup>|800|1000|1200|1.400|1600|
|Máximo de sessões simultâneas|30,000|30,000|30,000|30,000|30,000|
|Opções mínimas/máximas de vCore do pool elástico por banco de dados|0-8|0-10|0-12|0-14|0-16|
|Número de réplicas|1|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|
|Escala de leitura|N/D|N/D|N/D|N/D|N/D|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|


<sup>1</sup> consulte [Gerenciamento de recursos em pools elásticos densos](elastic-pool-resource-management.md) para obter considerações adicionais.

<sup>2</sup> o valor máximo para tamanhos de e/s variando entre 8 kb e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> para o máximo de trabalhos simultâneos (solicitações) para qualquer banco de dados individual, consulte [limites de recurso de banco de dados único](resource-limits-vcore-single-databases.md). Por exemplo, se o pool elástico estiver usando Gen5 e o vCore máximo por banco de dados for definido como 2, o valor máximo de trabalhos simultâneos será de 200.  Se o vCore máximo por banco de dados for definido como 0,5, o valor máximo de trabalhos simultâneos será 50, pois, em Gen5, há um máximo de 100 trabalhos simultâneos por vCore. Para outras configurações de máximo de vCore por banco de dados que sejam menores que 1 vCore, o número máximo de trabalhos simultâneos é redimensionado de forma semelhante.

### <a name="fsv2-series-compute-generation-part-2"></a>Geração de computação da série Fsv2 (parte 2)

|Tamanho da computação (objetivo do serviço)|GP_Fsv2_18|GP_Fsv2_20|GP_Fsv2_24|GP_Fsv2_32| GP_Fsv2_36|GP_Fsv2_72|
|:---| ---:|---:|---:|---:|---:|---:|
|Geração de computação|Série Fsv2|Série Fsv2|Série Fsv2|Série Fsv2|Série Fsv2|Série Fsv2|
|vCores|18|20|24|32|36|72|
|Memória (GB)|34,0|37,8|45,4|60,5|68,0|136,0|
|Número máximo de bancos de os por pool <sup>1</sup>|500|500|500|500|500|
|Suporte de columnstore|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (GB)|1536|1536|1536|3072|3072|4096|
|Tamanho máximo de log (GB)|512|512|512|1024|1024|1024|
|Tamanho máximo de dados de TempDB (GB)|83,25|92,5|111|148|166,5|333|
|Tipo de armazenamento|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|
|Latência de E/S (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|
|IOPS de dados máximo por pool <sup>2</sup>|5760|6400|7680|10240|11520|12800|
|Taxa máxima de log por pool (MBps)|48|48|48|48|48|48|
|Máximo de trabalhos simultâneos por pool (solicitações) <sup>3</sup>|900|1000|1200|1600|1800|3600|
|Máximo de logons simultâneos por pool (solicitações) <sup>3</sup>|1800|2000|2400|3200|3600|7200|
|Máximo de sessões simultâneas|30,000|30,000|30,000|30,000|30,000|30,000|
|Opções mínimas/máximas de vCore do pool elástico por banco de dados|0-18|0-20|0h a 24h|0-32|0-36|0-72|
|Número de réplicas|1|1|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|
|Escala de leitura|N/D|N/D|N/D|N/D|N/D|N/D|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|

<sup>1</sup> consulte [Gerenciamento de recursos em pools elásticos densos](elastic-pool-resource-management.md) para obter considerações adicionais.

<sup>2</sup> o valor máximo para tamanhos de e/s variando entre 8 kb e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> para o máximo de trabalhos simultâneos (solicitações) para qualquer banco de dados individual, consulte [limites de recurso de banco de dados único](resource-limits-vcore-single-databases.md). Por exemplo, se o pool elástico estiver usando Gen5 e o vCore máximo por banco de dados for definido como 2, o valor máximo de trabalhos simultâneos será de 200.  Se o vCore máximo por banco de dados for definido como 0,5, o valor máximo de trabalhos simultâneos será 50, pois, em Gen5, há um máximo de 100 trabalhos simultâneos por vCore. Para outras configurações de máximo de vCore por banco de dados que sejam menores que 1 vCore, o número máximo de trabalhos simultâneos é redimensionado de forma semelhante.


## <a name="general-purpose---provisioned-compute---dc-series"></a>Computação provisionada de uso geral-série DC

|Tamanho da computação (objetivo do serviço)|GP_DC_2|GP_DC_4|GP_DC_6|GP_DC_8|
|:--- | --: |--: |--: |--: |
|Geração de computação|DC|DC|DC|DC|
|vCores|2|4|6|8|
|Memória (GB)|9|18|27|36|
|Número máximo de bancos de os por pool <sup>1</sup>|100|400|400|400|
|Suporte de columnstore|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (GB)|756|1536|2.048|2.048|
|Tamanho máximo de log (GB)|227|461|614|614|
|Tamanho máximo de dados de TempDB (GB)|64|128|192|256|
|Tipo de armazenamento|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|
|Latência de E/S (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|
|IOPS de dados máximo por pool <sup>2</sup>|800|1600|2400|3200|
|Taxa máxima de log por pool (MBps)|9,4|18,8|28,1|32,8|
|Máximo de trabalhos simultâneos por pool (solicitações) <sup>3</sup>|168|336|504|672|
|Máximo de logons simultâneos por pool (solicitações) <sup>3</sup>|168|336|504|672|
|Máximo de sessões simultâneas|30,000|30,000|30,000|30,000|
|Opções mínimas/máximas de vCore do pool elástico por banco de dados|2|2... 4|2... 6|2... 8|
|Número de réplicas|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|
|Escala de leitura|N/D|N/D|N/D|N/D|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|

<sup>1</sup> consulte [Gerenciamento de recursos em pools elásticos densos](elastic-pool-resource-management.md) para obter considerações adicionais.

<sup>2</sup> o valor máximo para tamanhos de e/s variando entre 8 kb e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> para o máximo de trabalhos simultâneos (solicitações) para qualquer banco de dados individual, consulte [limites de recurso de banco de dados único](resource-limits-vcore-single-databases.md). Por exemplo, se o pool elástico estiver usando Gen5 e o vCore máximo por banco de dados for definido como 2, o valor máximo de trabalhos simultâneos será de 200.  Se o vCore máximo por banco de dados for definido como 0,5, o valor máximo de trabalhos simultâneos será 50, pois, em Gen5, há um máximo de 100 trabalhos simultâneos por vCore. Para outras configurações de máximo de vCore por banco de dados que sejam menores que 1 vCore, o número máximo de trabalhos simultâneos é redimensionado de forma semelhante.

## <a name="business-critical---provisioned-compute---gen4"></a>Comercialmente crítico-computação provisionada-Gen4

> [!IMPORTANT]
> Novos bancos de dados Gen4 não têm mais suporte nas regiões leste da Austrália ou sul do Brasil.

### <a name="business-critical-service-tier-generation-4-compute-platform-part-1"></a>Camada de serviço comercialmente crítica: plataforma de computação de geração 4 (parte 1)

|Tamanho da computação (objetivo do serviço)|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|Geração de computação|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|2|3|4|5|6|
|Memória (GB)|14|21|28|35|42|
|Número máximo de bancos de os por pool <sup>1</sup>|50|100|100|100|100|
|Suporte de columnstore|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|2|3|4|5|6|
|Tipo de armazenamento|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|
|Tamanho máximo de dados (GB)|1024|1024|1024|1024|1024|
|Tamanho máximo de log (GB)|307|307|307|307|307|
|Tamanho máximo de dados de TempDB (GB)|64|96|128|160|192|
|Latência de E/S (aproximada)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|
|IOPS de dados máximo por pool <sup>2</sup>|9\.000|13.500|18.000|22.500|27.000|
|Taxa máxima de log por pool (MBps)|20|30|40|50|60|
|Máximo de trabalhos simultâneos por pool (solicitações) <sup>3</sup>|420|630|840|1.050|1260|
|Máximo de logons simultâneos por pool (solicitações) <sup>3</sup>|420|630|840|1.050|1260|
|Máximo de sessões simultâneas|30,000|30,000|30,000|30,000|30,000|
|Opções mínimas/máximas de vCore do pool elástico por banco de dados|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1…3|0, 0,25, 0,5, 1…4|0, 0,25, 0,5, 1…5|0, 0,25, 0,5, 1…6|
|Número de réplicas|4|4|4|4|4|
|Multi-AZ|Sim|Sim|Sim|Sim|Sim|
|Escala de leitura|Sim|Sim|Sim|Sim|Sim|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|

<sup>1</sup> consulte [Gerenciamento de recursos em pools elásticos densos](elastic-pool-resource-management.md) para obter considerações adicionais.

<sup>2</sup> o valor máximo para tamanhos de e/s variando entre 8 kb e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> para o máximo de trabalhos simultâneos (solicitações) para qualquer banco de dados individual, consulte [limites de recurso de banco de dados único](resource-limits-vcore-single-databases.md). Por exemplo, se o pool elástico estiver usando Gen5 e o vCore máximo por banco de dados for definido como 2, o valor máximo de trabalhos simultâneos será de 200.  Se o vCore máximo por banco de dados for definido como 0,5, o valor máximo de trabalhos simultâneos será 50, pois, em Gen5, há um máximo de 100 trabalhos simultâneos por vCore. Para outras configurações de máximo de vCore por banco de dados que sejam menores que 1 vCore, o número máximo de trabalhos simultâneos é redimensionado de forma semelhante.

### <a name="business-critical-service-tier-generation-4-compute-platform-part-2"></a>Camada de serviço comercialmente crítica: plataforma de computação de geração 4 (parte 2)

|Tamanho da computação (objetivo do serviço)|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Geração de computação|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|7|8|9|10|16|24|
|Memória (GB)|49|56|63|70|112|159,5|
|Número máximo de bancos de os por pool <sup>1</sup>|100|100|100|100|100|100|
|Suporte de columnstore|N/D|N/D|N/D|N/D|N/D|N/D|
|Armazenamento OLTP na memória (GB)|7|8|9,5|11|20|36|
|Tipo de armazenamento|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|
|Tamanho máximo de dados (GB)|1024|1024|1024|1024|1024|1024|
|Tamanho máximo de log (GB)|307|307|307|307|307|307|
|Tamanho máximo de dados de TempDB (GB)|224|256|288|320|512|768|
|Latência de E/S (aproximada)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|
|IOPS de dados máximo por pool <sup>2</sup>|31.500|36.000|40.500|45,000|72.000|96.000|
|Taxa máxima de log por pool (MBps)|70|80|80|80|80|80|
|Máximo de trabalhos simultâneos por pool (solicitações) <sup>3</sup>|1.470|1680|1.890|2.100|3360|5040|
|Máximo de logons simultâneos por pool (solicitações) <sup>3</sup>|1.470|1680|1.890|2.100|3360|5040|
|Máximo de sessões simultâneas|30,000|30,000|30,000|30,000|30,000|30,000|
|Opções mínimas/máximas de vCore do pool elástico por banco de dados|0, 0,25, 0,5, 1…7|0, 0,25, 0,5, 1…8|0, 0,25, 0,5, 1…9|0, 0,25, 0,5, 1…10|0, 0,25, 0,5, 1…10, 16|0, 0,25, 0,5, 1…10, 16, 24|
|Número de réplicas|4|4|4|4|4|4|
|Multi-AZ|Sim|Sim|Sim|Sim|Sim|Sim|
|Escala de leitura|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|

<sup>1</sup> consulte [Gerenciamento de recursos em pools elásticos densos](elastic-pool-resource-management.md) para obter considerações adicionais.

<sup>2</sup> o valor máximo para tamanhos de e/s variando entre 8 kb e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> para o máximo de trabalhos simultâneos (solicitações) para qualquer banco de dados individual, consulte [limites de recurso de banco de dados único](resource-limits-vcore-single-databases.md). Por exemplo, se o pool elástico estiver usando Gen5 e o vCore máximo por banco de dados for definido como 2, o valor máximo de trabalhos simultâneos será de 200.  Se o vCore máximo por banco de dados for definido como 0,5, o valor máximo de trabalhos simultâneos será 50, pois, em Gen5, há um máximo de 100 trabalhos simultâneos por vCore. Para outras configurações de máximo de vCore por banco de dados que sejam menores que 1 vCore, o número máximo de trabalhos simultâneos é redimensionado de forma semelhante.

## <a name="business-critical---provisioned-compute---gen5"></a>Comercialmente crítico-computação provisionada-Gen5

### <a name="business-critical-service-tier-generation-5-compute-platform-part-1"></a>Camada de serviço comercialmente crítica: plataforma de computação de geração 5 (parte 1)

|Tamanho da computação (objetivo do serviço)|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Geração de computação|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|4|6|8|10|12|14|
|Memória (GB)|20,8|31,1|41,5|51,9|62,3|72,7|
|Número máximo de bancos de os por pool <sup>1</sup>|50|100|100|100|100|100|
|Suporte de columnstore|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|3.14|4,71|6,28|8,65|11, 2|13,39|
|Tamanho máximo de dados (GB)|1024|1536|1536|1536|3072|3072|
|Tamanho máximo de log (GB)|307|307|461|461|922|922|
|Tamanho máximo de dados de TempDB (GB)|128|192|256|320|384|448|
|Tipo de armazenamento|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|
|Latência de E/S (aproximada)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|
|IOPS de dados máximo por pool <sup>2</sup>|18.000|27.000|36.000|45,000|54.000|63.000|
|Taxa máxima de log por pool (MBps)|60|90|120|120|120|120|
|Máximo de trabalhos simultâneos por pool (solicitações) <sup>3</sup>|420|630|840|1.050|1260|1.470|
|Máximo de logons simultâneos por pool (solicitações) <sup>3</sup>|420|630|840|1.050|1260|1.470|
|Máximo de sessões simultâneas|30,000|30,000|30,000|30,000|30,000|30,000|
|Opções mínimas/máximas de vCore do pool elástico por banco de dados|0, 0,25, 0,5, 1…4|0, 0,25, 0,5, 1…6|0, 0,25, 0,5, 1…8|0, 0,25, 0,5, 1…10|0, 0,25, 0,5, 1…12|0, 0,25, 0,5, 1…14|
|Número de réplicas|4|4|4|4|4|4|
|Multi-AZ|Sim|Sim|Sim|Sim|Sim|Sim|
|Escala de leitura|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|

<sup>1</sup> consulte [Gerenciamento de recursos em pools elásticos densos](elastic-pool-resource-management.md) para obter considerações adicionais.

<sup>2</sup> o valor máximo para tamanhos de e/s variando entre 8 kb e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> para o máximo de trabalhos simultâneos (solicitações) para qualquer banco de dados individual, consulte [limites de recurso de banco de dados único](resource-limits-vcore-single-databases.md). Por exemplo, se o pool elástico estiver usando Gen5 e o vCore máximo por banco de dados for definido como 2, o valor máximo de trabalhos simultâneos será de 200.  Se o vCore máximo por banco de dados for definido como 0,5, o valor máximo de trabalhos simultâneos será 50, pois, em Gen5, há um máximo de 100 trabalhos simultâneos por vCore. Para outras configurações de máximo de vCore por banco de dados que sejam menores que 1 vCore, o número máximo de trabalhos simultâneos é redimensionado de forma semelhante.

### <a name="business-critical-service-tier-generation-5-compute-platform-part-2"></a>Camada de serviço comercialmente crítica: plataforma de computação de geração 5 (parte 2)

|Tamanho da computação (objetivo do serviço)|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Geração de computação|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|16|18|20|24|32|40|80|
|Memória (GB)|83|93,4|103,8|124,6|166,1|207,6|415,2|
|Número máximo de bancos de os por pool <sup>1</sup>|100|100|100|100|100|100|100|
|Suporte de columnstore|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|15,77|18,14|20,51|25,25|37,94|52,23|131,68|
|Tamanho máximo de dados (GB)|3072|3072|3072|4096|4096|4096|4096|
|Tamanho máximo de log (GB)|922|922|922|1229|1229|1229|1229|
|Tamanho máximo de dados de TempDB (GB)|512|576|640|768|1024|1280|2560|
|Tipo de armazenamento|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|
|Latência de E/S (aproximada)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|
|IOPS de dados máximo por pool <sup>2</sup>|72.000|81.000|90.000|108.000|144.000|180,000|256.000|
|Taxa máxima de log por pool (MBps)|120|120|120|120|120|120|120|
|Máximo de trabalhos simultâneos por pool (solicitações) <sup>3</sup>|1680|1.890|2.100|2520|3360|4200|8400|
|Máximo de logons simultâneos por pool (solicitações) <sup>3</sup>|1680|1.890|2.100|2520|3360|4200|8400|
|Máximo de sessões simultâneas|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Opções mínimas/máximas de vCore do pool elástico por banco de dados|0, 0,25, 0,5, 1…16|0, 0,25, 0,5, 1…18|0, 0,25, 0,5, 1…20|0, 0,25, 0,5, 1…20, 24|0, 0,25, 0,5, 1…20, 24, 32|0, 0,25, 0,5, 1…20, 24, 32, 40|0, 0,25, 0,5, 1…20, 24, 32, 40, 80|
|Número de réplicas|4|4|4|4|4|4|4|
|Multi-AZ|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Escala de leitura|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|

<sup>1</sup> consulte [Gerenciamento de recursos em pools elásticos densos](elastic-pool-resource-management.md) para obter considerações adicionais.

<sup>2</sup> o valor máximo para tamanhos de e/s variando entre 8 kb e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> para o máximo de trabalhos simultâneos (solicitações) para qualquer banco de dados individual, consulte [limites de recurso de banco de dados único](resource-limits-vcore-single-databases.md). Por exemplo, se o pool elástico estiver usando Gen5 e o vCore máximo por banco de dados for definido como 2, o valor máximo de trabalhos simultâneos será de 200.  Se o vCore máximo por banco de dados for definido como 0,5, o valor máximo de trabalhos simultâneos será 50, pois, em Gen5, há um máximo de 100 trabalhos simultâneos por vCore. Para outras configurações de máximo de vCore por banco de dados que sejam menores que 1 vCore, o número máximo de trabalhos simultâneos é redimensionado de forma semelhante.

## <a name="business-critical---provisioned-compute---m-series"></a>Computação comercialmente crítica-série M

### <a name="m-series-compute-generation-part-1"></a>Geração de computação da série M (parte 1)

|Tamanho da computação (objetivo do serviço)|BC_M_8|BC_M_10|BC_M_12|BC_M_14|BC_M_16|BC_M_18|
|:---| ---:|---:|---:|---:|---:|---:|
|Geração de computação|Série M|Série M|Série M|Série M|Série M|Série M|
|vCores|8|10|12|14|16|18|
|Memória (GB)|235,4|294,3|353,2|412,0|470,9|529,7|
|Número máximo de bancos de os por pool <sup>1</sup>|100|100|100|100|100|100|
|Suporte de columnstore|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|64|80|96|112|128|150|
|Tamanho máximo de dados (GB)|512|640|768|896|1024|1152|
|Tamanho máximo de log (GB)|171|213|256|299|341|384|
|Tamanho máximo de dados de TempDB (GB)|256|320|384|448|512|576|
|Tipo de armazenamento|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|
|Latência de E/S (aproximada)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|
|IOPS de dados máximo por pool <sup>2</sup>|12.499|15.624|18.748|21.873|24.998|28.123|
|Taxa máxima de log por pool (MBps)|48|60|72|84|96|108|
|Máximo de trabalhos simultâneos por pool (solicitações) <sup>3</sup>|800|1,000|1.200|1.400|1.600|1.800|
|Máximo de logons simultâneos por pool (solicitações) <sup>3</sup>|800|1,000|1.200|1.400|1.600|1.800|
|Máximo de sessões simultâneas|30000|30000|30000|30000|30000|30000|
|Opções mínimas/máximas de vCore do pool elástico por banco de dados|0-8|0-10|0-12|0-14|0-16|0-18|
|Número de réplicas|4|4|4|4|4|4|
|Multi-AZ|Não|Não|Não|Não|Não|Não|
|Escala de leitura|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|

<sup>1</sup> consulte [Gerenciamento de recursos em pools elásticos densos](elastic-pool-resource-management.md) para obter considerações adicionais.

<sup>2</sup> o valor máximo para tamanhos de e/s variando entre 8 kb e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> para o máximo de trabalhos simultâneos (solicitações) para qualquer banco de dados individual, consulte [limites de recurso de banco de dados único](resource-limits-vcore-single-databases.md). Por exemplo, se o pool elástico estiver usando Gen5 e o vCore máximo por banco de dados for definido como 2, o valor máximo de trabalhos simultâneos será de 200.  Se o vCore máximo por banco de dados for definido como 0,5, o valor máximo de trabalhos simultâneos será 50, pois, em Gen5, há um máximo de 100 trabalhos simultâneos por vCore. Para outras configurações de máximo de vCore por banco de dados que sejam menores que 1 vCore, o número máximo de trabalhos simultâneos é redimensionado de forma semelhante.

Se todos os vCores de um pool elástico estiverem ocupados, cada banco de dados no pool receberá uma quantidade igual de recursos de computação para processar as consultas. O banco de dados SQL do Azure fornece integridade de compartilhamento de recursos entre bancos de dados garantindo fatias iguais de tempo de computação. A integridade de compartilhamento de recursos do pool elástico é adicional a qualquer quantidade de recursos garantidos de outra forma a cada banco de dados quando o mínimo de vCores por banco de dados é definido com um valor diferente de zero.

### <a name="m-series-compute-generation-part-2"></a>Geração de computação da série M (parte 2)

|Tamanho da computação (objetivo do serviço)|BC_M_20|BC_M_24|BC_M_32|BC_M_64|BC_M_128|
|:---| ---:|---:|---:|---:|---:|
|Geração de computação|Série M|Série M|Série M|Série M|Série M|
|vCores|20|24|32|64|128|
|Memória (GB)|588,6|706,3|941,8|1883,5|3767,0|
|Número máximo de bancos de os por pool <sup>1</sup>|100|100|100|100|100|100|
|Suporte de columnstore|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|172|216|304|704|1768|
|Tamanho máximo de dados (GB)|1280|1536|2.048|4096|4096|
|Tamanho máximo de log (GB)|427|512|683|1024|1024|
|Tamanho máximo de dados de TempDB (GB)|4096|2.048|1024|768|640|
|Tipo de armazenamento|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|
|Latência de E/S (aproximada)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|
|IOPS de dados máximo por pool <sup>2</sup>|31.248|37.497|49.996|99.993|160.000|
|Taxa máxima de log por pool (MBps)|120|144|192|264|264|
|Máximo de trabalhos simultâneos por pool (solicitações) <sup>3</sup>|2.000|2.400|3\.200|6.400|12.800|
|Máximo de logons simultâneos por pool (solicitações) <sup>3</sup>|2.000|2.400|3\.200|6.400|12.800|
|Máximo de sessões simultâneas|30000|30000|30000|30000|30000|
|Número de réplicas|4|4|4|4|4|
|Multi-AZ|Não|Não|Não|Não|Não|
|Escala de leitura|Sim|Sim|Sim|Sim|Sim|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|

<sup>1</sup> consulte [Gerenciamento de recursos em pools elásticos densos](elastic-pool-resource-management.md) para obter considerações adicionais.

<sup>2</sup> o valor máximo para tamanhos de e/s variando entre 8 kb e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> para o máximo de trabalhos simultâneos (solicitações) para qualquer banco de dados individual, consulte [limites de recurso de banco de dados único](resource-limits-vcore-single-databases.md). Por exemplo, se o pool elástico estiver usando Gen5 e o vCore máximo por banco de dados for definido como 2, o valor máximo de trabalhos simultâneos será de 200.  Se o vCore máximo por banco de dados for definido como 0,5, o valor máximo de trabalhos simultâneos será 50, pois, em Gen5, há um máximo de 100 trabalhos simultâneos por vCore. Para outras configurações de máximo de vCore por banco de dados que sejam menores que 1 vCore, o número máximo de trabalhos simultâneos é redimensionado de forma semelhante.

Se todos os vCores de um pool elástico estiverem ocupados, cada banco de dados no pool receberá uma quantidade igual de recursos de computação para processar as consultas. O banco de dados SQL do Azure fornece integridade de compartilhamento de recursos entre bancos de dados garantindo fatias iguais de tempo de computação. A integridade de compartilhamento de recursos do pool elástico é adicional a qualquer quantidade de recursos garantidos de outra forma a cada banco de dados quando o mínimo de vCores por banco de dados é definido com um valor diferente de zero.

## <a name="business-critical---provisioned-compute---dc-series"></a>Negócio crítico-série de computação provisionada

|Tamanho da computação (objetivo do serviço)|BC_DC_2|BC_DC_4|BC_DC_6|BC_DC_8|
|:--- | --: |--: |--: |--: |
|Geração de computação|DC|DC|DC|DC|
|vCores|2|4|6|8|
|Memória (GB)|9|18|27|36|
|Número máximo de bancos de os por pool <sup>1</sup>|50|100|100|100|
|Suporte de columnstore|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|1.7|3.7|5.9|8.2|
|Tamanho máximo de dados (GB)|768|768|768|768|
|Tamanho máximo de log (GB)|230|230|230|230|
|Tamanho máximo de dados de TempDB (GB)|64|128|192|256|
|Tipo de armazenamento|SSD Local|SSD Local|SSD Local|SSD Local|
|Latência de E/S (aproximada)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|
|IOPS de dados máximo por pool <sup>2</sup>|15750|31500|47250|56000|
|Taxa máxima de log por pool (MBps)|20|60|90|120|
|Máximo de trabalhos simultâneos por pool (solicitações) <sup>3</sup>|168|336|504|672|
|Máximo de logons simultâneos por pool (solicitações) <sup>3</sup>|168|336|504|672|
|Máximo de sessões simultâneas|30,000|30,000|30,000|30,000|
|Opções mínimas/máximas de vCore do pool elástico por banco de dados|2|2... 4|2... 6|2... 8|
|Número de réplicas|4|4|4|4|
|Multi-AZ|Não|Não|Não|Não|
|Escala de leitura|Sim|Sim|Sim|Sim|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|

<sup>1</sup> consulte [Gerenciamento de recursos em pools elásticos densos](elastic-pool-resource-management.md) para obter considerações adicionais.

<sup>2</sup> o valor máximo para tamanhos de e/s variando entre 8 kb e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> para o máximo de trabalhos simultâneos (solicitações) para qualquer banco de dados individual, consulte [limites de recurso de banco de dados único](resource-limits-vcore-single-databases.md). Por exemplo, se o pool elástico estiver usando Gen5 e o vCore máximo por banco de dados for definido como 2, o valor máximo de trabalhos simultâneos será de 200.  Se o vCore máximo por banco de dados for definido como 0,5, o valor máximo de trabalhos simultâneos será 50, pois, em Gen5, há um máximo de 100 trabalhos simultâneos por vCore. Para outras configurações de máximo de vCore por banco de dados que sejam menores que 1 vCore, o número máximo de trabalhos simultâneos é redimensionado de forma semelhante.

## <a name="database-properties-for-pooled-databases"></a>Propriedades do banco de dados para bancos de dados em pool

A tabela a seguir descreve as propriedades dos bancos de dados em pool.

> [!NOTE]
> Os limites de recursos de bancos de dados individuais em pools elásticos geralmente são os mesmos dos bancos de dados únicos fora dos pools que têm o mesmo tamanho de computação (objetivo de serviço). Por exemplo, o máximo de trabalhos simultâneos para um banco de dados GP_Gen4_1 é 200. Assim, o máximo de trabalhos simultâneos para um banco de dados em um pool de GP_Gen4_1 também é 200. Observe que o número total de trabalhos simultâneos no pool de GP_Gen4_1 é 210.

| Propriedade | Descrição |
|:--- |:--- |
| Máximo de vCores por banco de dados |O número máximo de vCores que qualquer banco de dados no pool pode usar, se disponível, com base na utilização por outros bancos de dados no pool. O máximo de vCores por banco de dados não é uma garantia de recursos para um banco de dados. Essa configuração é uma configuração global que se aplica a todos os bancos de dados no pool. Defina um valor para o máximo de vCores por banco de dados que seja alto o suficiente para lidar com picos de utilização do banco de dados. Espera-se um grau de sobrecarga, uma vez que o pool normalmente assume padrões de uso dos bancos de dados com altos e baixos, em que todos os bancos de dados não atingem um pico simultaneamente.|
| Mínimo de VCores por banco de dados |O número mínimo de vCores garantido para qualquer banco de dados no pool. Essa configuração é uma configuração global que se aplica a todos os bancos de dados no pool. O mínimo de vCores por banco de dados pode ser definido como 0 e também é o valor padrão. Essa propriedade é definida entre 0 e a utilização média de vCores por banco de dados. O produto do número de bancos de dados no pool e o mínimo de vCores por banco de dados não pode exceder os vCores por pool.|
| Armazenamento máximo por banco de dados |O tamanho máximo do banco de dados definido pelo usuário para um banco de dados em um pool. Os bancos de dados em pool compartilham armazenamento de pool alocado, para que o tamanho que um banco de dados possa atingir seja limitado ao menor tamanho de banco de dados e armazenamento de pool restante. O tamanho máximo por banco de dados refere-se ao tamanho máximo dos arquivos de dados e não inclui o espaço usado pelos arquivos de log. |
|||

## <a name="next-steps"></a>Próximas etapas

- Para limites de recursos vCore para um único banco de dados, confira [limites de recursos para bancos de dados individuais usando o modelo de compra vCore](resource-limits-vcore-single-databases.md)
- Para os limites de recursos de DTU para um único banco de dados, consulte [limites de recursos para bancos de dados individuais usando o modelo de compra de DTU](resource-limits-dtu-single-databases.md)
- Para os limites de recursos de DTU para pools elásticos, consulte [limites de recursos para pools elásticos usando o modelo de compra de DTU](resource-limits-dtu-elastic-pools.md)
- Para limites de recurso das instâncias gerenciadas, confira os [limites de recurso para instâncias gerenciadas](../managed-instance/resource-limits.md).
- Para saber mais sobre limites gerais do Azure, confira [Assinatura do Azure e limites de serviço, cotas e restrições](../../azure-resource-manager/management/azure-subscription-service-limits.md).
- Para obter informações sobre os limites de recursos em um SQL Server lógico, consulte [visão geral dos limites de recursos em um servidor SQL lógico](resource-limits-logical-server.md) para obter informações sobre os limites nos níveis de servidor e assinatura.
