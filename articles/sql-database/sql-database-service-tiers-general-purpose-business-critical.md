---
title: Banco de dados SQL do Azure - uso geral e negócios críticos | Microsoft Docs
description: O artigo discute o uso geral e a camada de serviço críticos de negócios no modelo de compra de vCore.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
manager: craigg
ms.date: 02/23/2019
ms.openlocfilehash: 6a027e6c0afd526139484240f288205fa946d557
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62111792"
---
# <a name="azure-sql-database-service-tiers"></a>Camadas de serviço do Banco de Dados SQL do Azure

Banco de dados SQL do Azure baseia-se na arquitetura de mecanismo de banco de dados do SQL Server que é ajustada para o ambiente de nuvem para garantir a disponibilidade de 99,99%, até mesmo no caso de falhas de infraestrutura. Há três modelos de arquitetura que são usados no Banco de Dados SQL do Azure:

- [Uso geral](sql-database-service-tier-general-purpose.md), projetado para a maioria das cargas de trabalho genéricas.
- [Comercialmente crítico](sql-database-service-tier-business-critical.md), projetado para cargas de trabalho de baixa latência com uma réplica de leitura.
- [Hiperescala](sql-database-service-tier-hyperscale.md), projetado para bancos de dados muito grandes (até 100 TB) com várias réplicas de leitura.

Este artigo discute as considerações de armazenamento e backup para as camadas de serviço General Purpose e Business Critical no modelo de compra baseado em vCore.

> [!NOTE]
> Para obter detalhes sobre a camada de serviço da Hiperescala no modelo de compra baseado no vCore, consulte [Nível de serviço da Hiperescala](sql-database-service-tier-hyperscale.md). Para obter uma comparação do modelo de compra baseado em vCore com o modelo de compra baseado em DTU, consulte [Modelos e recursos de compra do Banco de Dados SQL do Azure](sql-database-purchase-models.md).

## <a name="data-and-log-storage"></a>Armazenamento de dados e de log

Considere o seguinte:

- O armazenamento alocado é usado por arquivos de arquivos de dados (MDF) e arquivos de log (LDF).
- Cada tamanho da computação do banco de dados individual dá suporte a um tamanho máximo de banco de dados, com um tamanho máximo padrão de 32 GB.
- Quando você configura o tamanho desejado do banco de dados individual (tamanho de MDF), 30% do armazenamento adicional é automaticamente adicionado para dar suporte a LDF
- O tamanho do armazenamento na Instância Gerenciada deve ser especificado em múltiplos de 32 GB.
- É possível selecionar qualquer tamanho de banco de dados individual entre 10 GB e o máximo compatível
  - Para armazenamento nas camadas de serviço standard ou de uso geral, aumente ou diminua o tamanho em incrementos de 10 GB
  - Para armazenamento nas camadas de serviço críticas premium ou empresariais, aumente ou diminua o tamanho em incrementos de 250 GB
- Na camada de serviço de Uso Geral, `tempdb` usa um SSD anexado e esse custo de armazenamento é incluído no preço do vCore.
- Na camada de serviço Comercialmente Crítico, `tempdb` compartilha o SSD anexado com os arquivos MDF e LDF e o custo de armazenamento tempDB é incluído no preço vCore.

> [!IMPORTANT]
> Você é cobrado pelo armazenamento total alocado para MDF e LDF.

Para monitorar o tamanho total atual do MDF e do LDF, use [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql). Para monitorar o tamanho atual dos arquivos MDF e LDF individuais, use [sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql).

> [!IMPORTANT]
> Em algumas circunstâncias, talvez seja necessário reduzir um banco de dados para recuperar o espaço não utilizado. Para obter mais informações, consulte [gerenciar o espaço de arquivo no banco de dados SQL do Azure](sql-database-file-space-management.md).

## <a name="backups-and-storage"></a>Backups e armazenamento

O armazenamento para backups de banco de dados é alocado para dar suporte a recursos de PITR (Recuperação Pontual) e [LTR (Retenção de Longo Prazo)](sql-database-long-term-retention.md) do Banco de Dados SQL. Esse armazenamento é alocado separadamente para cada banco de dados e faturado como duas cobranças separadas por banco de dados.

- **PITR**: backups de banco de dados individuais são copiados para o [armazenamento RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md) automaticamente. O tamanho do armazenamento aumenta dinamicamente conforme os novos backups são criados.  O armazenamento é usado por backups completos semanais, backups diferenciais diários e backups de log de transações copiados a cada 5 minutos. O consumo de armazenamento depende da taxa de alteração do banco de dados e do período de retenção. É possível configurar um período de retenção separado para cada banco de dados entre 7 e 35 dias. Uma quantidade de armazenamento mínima igual a 1x de tamanho de dados é fornecido sem nenhum custo adicional. Para a maioria dos bancos de dados, esse valor é suficiente para armazenar 7 dias de backups.
- **LTR**: o Banco de Dados SQL oferece a opção de configurar a retenção de longo prazo de backups completos por até 10 anos. Se a política de LTR estiver habilitada, esses backups serão armazenados no armazenamento RA-GRS automaticamente, mas você poderá controlar com que frequência os backups serão copiados. Para atender a diferentes requisitos de conformidade, é possível selecionar diferentes períodos de retenção para backups semanais, mensais e/ou anuais. Essa configuração definirá quanto armazenamento será usado para os backups de LTR. É possível usar a calculadora de preços de LTR para estimar o custo do armazenamento LTR. Para obter mais informações, consulte [Retenção de longo prazo](sql-database-long-term-retention.md).

## <a name="next-steps"></a>Próximos passos

- Para obter detalhes sobre tamanhos da computação e opções de tamanho de armazenamento específicos disponíveis para um único banco de dados nas camadas General Purpose e Business Critical Service, consulte [Limites de recursos baseados no Banco de Dados SQL vCore para bancos de dados individuais](sql-database-vcore-resource-limits-single-databases.md#general-purpose-service-tier)
- Para obter detalhes sobre específicas de tamanhos da computação e opções de tamanho de armazenamento disponíveis para pools Elásticos nas camadas de serviço críticos de negócios e de uso geral, veja [limites de recurso baseado em vCore do banco de dados SQL para pools Elásticos](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose-service-tier-storage-sizes-and-compute-sizes).
