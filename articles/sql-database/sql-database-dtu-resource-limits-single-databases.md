---
title: Limites de recursos baseados em DTU do Banco de Dados SQL do Azure – banco de dados único | Microsoft Docs
description: Esta página descreve alguns limites de recursos baseados em DTU comuns para o Banco de Dados SQL do Microsoft Azure.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 03/20/2019
ms.openlocfilehash: b3514eca8db5d5b68b3e5784ee95e8583813945c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61075228"
---
# <a name="resource-limits-for-single-databases-using-the-dtu-based-purchasing-model"></a>Limites de recursos para bancos de dados individuais usando o modelo de compra baseado em DTU

Este artigo fornece os limites de recursos detalhados para bancos de dados individuais do Banco de Dados SQL do Azure usando o modelo de compra baseado em DTU.

Para limites de recurso de modelo de compra baseado em DTU para pools elásticos, consulte [Limites de recurso baseado em DTU - bancos de pools elásticos](sql-database-dtu-resource-limits-elastic-pools.md). Para saber os limites de recurso vCore, confira [Limites de recurso baseados em vCore  – bancos de dados individuais](sql-database-vcore-resource-limits-single-databases.md) e [Limites de recurso baseados em vCore do Banco de Dados SQL - pools elásticos](sql-database-vcore-resource-limits-elastic-pools.md). Para obter mais informações sobre os diferentes modelos de compras, consulte [Modelos de compras e camadas de serviço](sql-database-purchase-models.md).

## <a name="single-database-storage-sizes-and-compute-sizes"></a>Banco de dados individual: Tamanhos de armazenamento e tamanhos da computação

As tabelas a seguir mostram os recursos disponíveis para um único banco de dados individual em cada camada de serviço e tamanho da computação. É possível definir a camada de serviço, o tamanho da computação e a quantidade de armazenamento para um banco de dados individual usando o [portal do Azure](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), o [Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-sql-database-servers-and-single-databases), o [PowerShell](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases), a [CLI do Azure](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases) ou a [API REST](sql-database-single-databases-manage.md#rest-api-manage-sql-database-servers-and-single-databases).

> [!IMPORTANT]
> Para dimensionar as diretrizes e considerações, consulte [dimensionar um único banco de dados](sql-database-single-database-scale.md)

### <a name="basic-service-tier"></a>Camada de serviço Básica

| **Tamanho de computação** | **Básico** |
| :--- | --: |
| Número máximo de DTUs | 5 |
| Armazenamento incluído (GB) | 2 |
| Opções de espaço de armazenamento máximo (GB) | 2 |
| Armazenamento máximo OLTP na memória (GB) |N/D |
| Máximo de trabalhos simultâneos (solicitações) | 30 |
| Máximo de sessões simultâneas | 300 |
|||

### <a name="standard-service-tier"></a>Camada de serviço Standard

| **Tamanho de computação** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|
| Número máximo de DTUs | 10 | 20 | 50 | 100 |
| Armazenamento incluído (GB) | 250 | 250 | 250 | 250 |
| Opções de espaço de armazenamento máximo (GB) | 250 | 250 | 250 | 250, 500, 750, 1024 |
| Armazenamento máximo OLTP na memória (GB) | N/D | N/D | N/D | N/D |
| Máximo de trabalhos simultâneos (solicitações)| 60 | 90 | 120 | 200 |
| Máximo de sessões simultâneas |600 | 900 | 1.200 | 2400 |
||||||

### <a name="standard-service-tier-continued"></a>Camada de serviço Standard (continuação)

| **Tamanho de computação** | **S4** | **S6** | **S7** | **S9** | **S12** |
| :--- |---:| ---:|---:|---:|---:|
| Número máximo de DTUs | 200 | 400 | 800 | 1600 | 3000 |
| Armazenamento incluído (GB) | 250 | 250 | 250 | 250 | 250 |
| Opções de espaço de armazenamento máximo (GB) | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 |
| Armazenamento máximo OLTP na memória (GB) | N/D | N/D | N/D | N/D |N/D |
| Máximo de trabalhos simultâneos (solicitações)| 400 | 800 | 1600 | 3200 |6000 |
| Máximo de sessões simultâneas |4800 | 9600 | 19200 | 30000 |30000 |
|||||||

### <a name="premium-service-tier"></a>Camada de serviço Premium

| **Tamanho de computação** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** |
| :--- |---:|---:|---:|---:|---:|---:|
| Número máximo de DTUs | 125 | 250 | 500 | 1000 | 1750 | 4000 |
| Armazenamento incluído (GB) | 500 | 500 | 500 | 500 | 4096* | 4096* |
| Opções de espaço de armazenamento máximo (GB) | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 4096* | 4096* |
| Armazenamento máximo OLTP na memória (GB) | 1 | 2 | 4 | 8 | 14 | 32 |
| Máximo de trabalhos simultâneos (solicitações)| 200 | 400 | 800 | 1600 | 2400 | 6400 |
| Máximo de sessões simultâneas | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||

\* De 1024 GB até 4096 GB em incrementos de 256 GB

> [!IMPORTANT]
> Mais de 1 TB de armazenamento na camada Premium está atualmente disponível em todas as regiões, exceto: Leste da China, Norte da China, Alemanha Central, Nordeste da Alemanha, Centro-oeste dos EUA, regiões US DoD e US Government Central. Nessas regiões, o armazenamento máximo na camada Premium é limitado a 1 TB.  Para obter mais informações, confira [Limitações atuais de P11-P15](sql-database-single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb).  
> [!NOTE]
> Para `tempdb` limites, consulte [limites tempdb](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database?view=sql-server-2017#tempdb-database-in-sql-database).

## <a name="next-steps"></a>Próximas etapas

- Para os limites de recursos do vCore de um único banco de dados, confira os [limites de recursos para bancos de dados individuais usando o modelo de compra baseado no vCore](sql-database-vcore-resource-limits-single-databases.md)
- Para ver os limites de recursos do vCore para os pools elásticos, confira os [limites de recursos para pools elásticos usando o modelo de compras baseado em vCore](sql-database-vcore-resource-limits-elastic-pools.md)
- Para os limites de recursos de DTU dos pools elásticos, confira [limites de recursos para pools elásticos usando o modelo de compra baseado em DTU](sql-database-dtu-resource-limits-elastic-pools.md)
- Para os limites de recursos das instâncias gerenciadas, confira [limites de recursos para instâncias gerenciadas](sql-database-managed-instance-resource-limits.md).
- Para saber mais sobre limites gerais do Azure, confira [Assinatura do Azure e limites de serviço, cotas e restrições](../azure-subscription-service-limits.md).
- Para se informar sobre os limites de recursos em um servidor de banco de dados, confira a [visão geral dos limites de recursos em um servidor do Banco de Dados SQL](sql-database-resource-limits-database-server.md) para conferir os limites nos níveis do servidor e da assinatura.
