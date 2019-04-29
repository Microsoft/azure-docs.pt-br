---
title: Desativação da camada de serviço Premium RS do Banco de Dados SQL do Azure | Microsoft Docs
description: A camada de serviço Premium RS está sendo desativada e o suporte para a camada sendo encerrado - consulte as opções de migração.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
manager: craigg
ms.date: 02/07/2019
ms.openlocfilehash: d7d1bc95a646527ccdf9d2808aefe6b8f46d7e9a
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62111758"
---
# <a name="azure-sql-database-premium-rs-service-tier-preview-is-being-retired---options-for-migration"></a>A camada de serviço Premium RS do Banco de Dados SQL do Azure (versão prévia) está sendo desativada - opções para migração

Em fevereiro de 2018, a Microsoft anunciou que a camada de serviço Premium RS no Banco de Dados SQL do Azure não seria liberada para disponibilidade geral e não teria mais suporte após 31 de janeiro de 2019. O encerramento da data limite do suporte foi estendido para 30 de junho de 2019. Este artigo explica as opções para a migração da camada de serviço Premium RS para outra camada de serviço. Após 30 de junho de 2019, a Microsoft migrará automaticamente os bancos de dados do Premium RS para uma camada de serviço geralmente disponível que melhor corresponda aos requisitos de desempenho do banco de dados Premium RS.

A seguir, estão os destinos de migração e as opções de preços que podem ser adequados para clientes Premium RS:

- Camadas de serviço vCore

  As camadas de serviço **Uso Geral** e **Comercialmente Crítico** no [modelo de compra com base em vCore](sql-database-service-tiers-vcore.md). Essas duas camadas de serviço estão na disponibilidade geral. O modelo de compra baseado em vCore também oferece a camada de serviço **Hiperescala** (visualização pública) que adapta-se sob demanda às necessidades da carga de trabalho com dimensionamento automático de até 100 TB por banco de dados. A camada de serviço Hiperescala fornece desempenho de E/S comparável à camada de serviço Premium no [Modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md) a um preço mais próximo da camada de serviço Premium RS.
- Preço de Desenvolvimento/Teste

  O [Preço de desenvolvimento/teste](https://azure.microsoft.com/pricing/dev-test/) proporciona economia de até 55% em relação às taxas incluídas na licença com a assinatura do Visual Studio.
- Benefício Híbrido do Azure e preço de capacidade reserva

  O [Benefício Híbrido do Azure e preço de capacidade reserva](https://azure.microsoft.com/pricing/details/sql-database/) proporcionam economia de até 80% em relação às taxas incluídas na licença. Para obter mais informações sobre essas opções, consulte [Benefício Híbrido do Azure para SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) e [Capacidade reserva do Banco de Dados SQL do Azure](sql-database-reserved-capacity.md).

## <a name="act-now-to-migrate-your-premium-rs-databases-to-alternative-sql-database-service-tiers"></a>Faça agora a migração dos seus bancos de dados Premium RS para camadas de serviço alternativas do Banco de Dados SQL

Revise as diretrizes contidas neste artigo em conjunto com nossos preços e a documentação para determinar os destinos de migração corretos para as cargas de trabalho do Premium RS.

## <a name="migrate-compute-intensive-workloads-and-save"></a>Migrar cargas de trabalho de computação intensiva e salvar

Para as cargas de trabalho Premium RS de computação intensiva, é recomendável migrar para a camada de serviço Uso Geral com base vCore e economizar mais em comparação às taxas incluídas na licença usando o Benefício Híbrido do Azure para SQL Server e ofertas de capacidade reserva. Se você preferir permanecer em uma opção de compra com base em DTU, poderá migrar seus bancos de dados Premium RS de computação intensiva para uma camada de serviço Padrão e ainda economizar em comparação com o preço de disponibilidade geral do Premium RS (se estivesse em disponibilidade geral).

> [!WARNING]
> A migração das cargas de trabalho Premium RS para camadas de serviço Premium com base em DTU poderá aumentar os custos mensais em comparação com os preços atuais do Premium RS. É recomendável considerar as camadas Hiperescala ou Comercialmente Crítico com Benefício Híbrido do Azure e preço de capacidade reserva para manter custos semelhantes ou inferiores ao do Premium RS.

### <a name="premium-rs-databases"></a>Banco de dados Premium RS

|**Se atualmente você estiver em...**|**Migrar para comparável com base em vCore...**|**Migrar para comparável com base em DTU…**|
|---|---|---|
|Premium RS 1|1 vCore de Uso Geral (Gen4)|Padrão 3|
|Premium RS 2|2 vCores de Uso Geral (Gen4)|Padrão 4|
|Premium RS 4|Uso Geral 4 vCores (Gen4)|Padrão 6|
|Premium RS 6|Uso Geral 6 vCores (Gen4)|Padrão 7|

### <a name="premium-rs-pools"></a>Pools Premium RS

|**Se atualmente você estiver em...**|**Migrar para comparável com base em vCore...**|**Migrar para comparável com base em DTU…**|
|---|---|---|
|Pool Premium RS 125 DTU|1 vCore de Uso Geral (Gen4)|Pool Padrão 100 eDTUs|
|Pool Premium RS 250 DTU|2 vCores de Uso Geral (Gen4)|Pool Padrão 250 eDTUs|
|Pool Premium RS 500 DTU|Uso Geral 4 vCores (Gen4)|Pool Padrão 500 eDTUs|
|Pool Premium RS 1000 DTU|Uso Geral 8 vCores (Gen4)|Pool Padrão 1000 eDTUs|

## <a name="optimize-savings-and-performance-for-your-io-intensive-workloads"></a>Otimizar economia e desempenho para as cargas de trabalho com uso intensivo de E/S

É recomendável migrar os bancos de dados individuais com uso intensivo de E/S para nossa camada Hiperescala com base em vCore, atualmente em versão prévia, e seus pools de bancos de dados com uso intensivo de E/S para nossa camada Comercialmente Crítico geralmente disponível, para a combinação ideal de desempenho e custo.  As seguintes opções com base em vCore manterão ou melhorarão o desempenho atual e poderão economizar seu dinheiro quando combinadas com Benefício Híbrido do Azure e preço de capacidade reserva.

|**Se atualmente você estiver em...**|**Migrar para comparável com base em vCore...**|**Migrar para comparável com base em DTU…**|
|---|---|---|
|Premium RS 1|(Versão prévia) 1 vCore de Hiperescala (Gen4) ou 1 vCore Comercialmente Crítico (Gen4)|Premium 1|
|Premium RS 2|(Versão prévia) 2 vCores de Hiperescala (Gen4) ou 2 vCores Comercialmente Críticos (Gen4)|Premium 2|
|Premium RS 4|(Versão prévia) 4 vCores de Hiperescala (Gen4) ou 4 vCores Comercialmente Críticos (Gen4)|Premium 4
|Premium RS 6|(Versão prévia) 6 vCores de Hiperescala (Gen4) ou 6 vCores Comercialmente Críticos (Gen4)|Premium 6|

|**Se atualmente você estiver em...**|**Migrar para comparável com base em vCore...**|**Migrar para comparável com base em DTU…**|
|---|---|---|
|Pool Premium RS 125 DTU|Comercialmente Crítico 2 vCores (Gen4)|Pool Premium 125 eDTUs|
|Pool Premium RS 250 DTU|Comercialmente Crítico 2 vCores (Gen4)|Pool Premium 250 eDTUs|
|Pool Premium RS 500 DTU|Comercialmente Crítico 4 vCores (Gen4)|Pool Premium 500 eDTUs|
|Pool Premium RS 1000 DTU|Comercialmente Crítico 8 vCores (Gen4)|Pool Premium 1000 eDTUs|

## <a name="take-advantage-of-our-new-offers"></a>Aproveite as nossas novas ofertas

Nossas camadas de serviço no modelo de compra baseado em vCore são elegíveis para ofertas especiais que podem economizar até 80% em comparação aos preços incluídos na licença. Use suas licenças do SQL Server Standard ou Enterprise Edition com Software Assurance ativo para economizar até 55% em comparação com os preços incluídos na licença com o [Benefício Híbrido do Azure para SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/). Você pode combinar o benefício híbrido com o preço [de capacidade reserva do Banco de Dados SQL do Azure](sql-database-reserved-capacity.md) e economizar até 80% ao comprometer-se antecipadamente com um prazo de um ou três anos.  Ative hoje mesmo os dois benefícios no portal do Azure.

Se você tiver dúvidas ou preocupações em relação a essa alteração, ou se precisar de assistência para a migração, contate a [Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

## <a name="migration-from-a-premium-rs-service-tier-to-a-service-tier-in-either-the-dtu-or-the-vcore-model"></a>Migração de uma camada de serviço Premium RS para uma camada de serviço no modelo vCore ou DTU

### <a name="migration-of-a-database"></a>Migração de um banco de dados

A migração de um banco de dados de uma camada de serviço Premium RS para uma camada de serviço no modelo vCore ou DTU é semelhante ao upgrade e downgrade entre camadas de serviço na camada de serviço Premium RS.

### <a name="using-database-copy-to-convert-a-premium-rs-database-to-a-dtu-based-or-vcore-based-database"></a>Usar cópia do banco de dados para converter um banco de dados Premium RS em um banco de dados com base em DTU ou vCore

É possível copiar qualquer banco de dados com um tamanho da computação Premium RS para um banco de dados com um tamanho da computação com base em DTU ou vCore sem restrições ou sequenciamento especial, desde que o tamanho da computação de destino dê suporte para o tamanho máximo do banco de dados de origem. O banco de dados cria um instantâneo de dados a partir do horário de início da operação de cópia e não executa a sincronização de dados entre a origem e o destino.

## <a name="next-steps"></a>Próximas etapas

- Para obter detalhes sobre os tamanhos da computação específicos e as opções de tamanho de armazenamento disponíveis para bancos de dados individuais, confira [Limites de recursos baseados em vCore do Banco de Dados SQL para bancos de dados individuais](sql-database-vcore-resource-limits-single-databases.md#general-purpose-service-tier)
- Para obter detalhes sobre tamanhos da computação específicos e opções de tamanho de armazenamento disponíveis para pools elásticos, veja [Limites de recursos baseados em vCore do Banco de Dados SQL para pools elásticos](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose-service-tier-storage-sizes-and-compute-sizes).
