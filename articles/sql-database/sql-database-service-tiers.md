---
title: "Serviço do Banco de Dados SQL do Azure | Microsoft Docs"
description: "Saiba mais sobre as camadas de serviço do Banco de Dados SQL"
keywords: 
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: f5c5c596-cd1e-451f-92a7-b70d4916e974
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 08/20/2017
ms.author: carlrab
ms.openlocfilehash: 0183e9c60da7c216a6268dfec3874eb76270f71d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="what-are-azure-sql-database-service-tiers"></a>Quais são as camadas de serviço do Banco de Dados SQL do Azure

[Banco de Dados SQL do Azure](sql-database-technical-overview.md) oferece as camadas de serviço **Básica**, **Standard**, **Premium**, e **Premium RS** para [bancos de dados individuais](sql-database-single-database-resources.md) e [pools elásticos](sql-database-elastic-pool.md). As camadas de serviço são diferenciadas principalmente por uma gama de níveis de desempenho e opções de tamanho de armazenamento, além do preço.  Todas as camadas de serviço fornecem flexibilidade na alteração do tamanho de armazenamento e no nível de desempenho.  Bancos de dados individuais e pools elásticos são cobrados por hora com base na camada de serviço, nível de desempenho e tamanho de armazenamento.   

## <a name="choosing-a-service-tier"></a>Como escolher uma camada de serviço

Escolher uma camada de serviço depende principalmente da continuidade dos negócios, armazenamento e requisitos de desempenho.
| | **Básico** | **Standard** |**Premium** |**Premium RS** |
| :-- | --: |--:| --:| --:| 
|Carga de trabalho de destino|Desenvolvimento e produção|Desenvolvimento e produção|Desenvolvimento e produção|Carga de trabalho que pode tolerar perda de dados por até 5 minutos devido a falhas de serviço|
|SLA de tempo de atividade|99,99%|99,99%|99,99%|N/d enquanto estiver em versão prévia|
|Retenção de backup|7 dias|35 dias|35 dias|35 dias|
|CPU|Baixo|Baixo, Médio, Alto|Médio, Alto|Média|
|Taxa de transferência de E/S|Baixo  | Média | Ordem de magnitude maior do que o Standard|Mesmo que o Premium|
|Latência de E/S|Maior do que o Premium|Maior do que o Premium|Menor do que o Básico e Standard|Mesmo que o Premium|
|Indexação de ColumnStore e OLTP in-memory|N/D|N/D|Suportado|Suportado|
|||||

## <a name="performance-level-and-storage-size-limits"></a>Limites de tamanho de armazenamento e nível de desempenho

Os níveis de desempenho são expressos em termos de DTUs (Unidades de transação de banco de dados) para bancos de dados individuais, e eDTUs (Unidades de transação de banco de dados elásticos) para pools elásticos. Para saber mais sobre DTUs e eDTUs, confira [O que são DTUs e eDTUs?](sql-database-what-is-a-dtu.md).

### <a name="single-databases"></a>Bancos de dados únicos

|  | **Básico** | **Standard** | **Premium** | **Premium RS**|
| :-- | --: | --: | --: | --: |
| Tamanho máximo de armazenamento* | 2 GB | 1 TB | 4 TB  | 1 TB  |
| Máximo de DTUs | 5 | 3000 | 4000 | 1000 |
||||||

### <a name="elastic-pools"></a>Pools elásticos

| | **Básico** | **Standard** | **Premium** | **Premium RS**|
| :-- | --: | --: | --: | --: |
| Tamanho máximo de armazenamento por banco de dados*  | 2 GB | 1 TB | 1 TB | 1 TB |
| Tamanho máximo de armazenamento por pool* | 156 GB | 4 TB | 4 TB | 1 TB |
| Máximo de eDTUs por banco de dados | 5 | 3000 | 4000 | 1000 |
| Máximo de eDTUs por pool | 1600 | 3000 | 4000 | 1000 |
| Número máximo de bancos de dados por pool | 500  | 500 | 100 | 100 |
||||||

> [!IMPORTANT]
> \* Tamanhos de armazenamento maiores que a quantidade de armazenamento incluída estão em versão prévia e aplicam-se custos extras. Para obter detalhes, confira [Preços de Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/). 
>
> \* Na camada Premium, mais de 1 TB de armazenamento está atualmente disponível nas seguintes regiões: Leste dos EUA 2, Oeste dos EUA, Gov. EUA - Virgínia, Europa Ocidental, Alemanha Central, Sudeste Asiático, Leste do Japão, Leste da Austrália, Canadá Central e Leste do Canadá. Consulte [Limitações atuais de P11-P15](sql-database-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  
> 

Para obter detalhes sobre os níveis de desempenho específicos e opções de tamanho de armazenamento disponíveis, consulte [Limites de recursos do Banco de Dados SQL](sql-database-resource-limits.md).


## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [Recursos de banco de dados individual](sql-database-single-database-resources.md).
- Saiba mais sobre pools elásticos em [Pools elásticos](sql-database-elastic-pool.md).
- Saiba mais sobre a [Assinatura do Azure e limites de serviços, cotas e restrições](../azure-subscription-service-limits.md)
* Saiba mais sobre [DTUs e eDTUs](sql-database-what-is-a-dtu.md).
* Saiba mais sobre como monitorar o uso de DTU em [Monitoramento e ajuste de desempenho](sql-database-troubleshoot-performance.md).

