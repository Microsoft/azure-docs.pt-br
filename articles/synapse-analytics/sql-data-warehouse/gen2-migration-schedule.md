---
title: Migre seu pool SQL dedicado (anteriormente conhecido como SQL DW) para Gen2
description: Instruções para migrar um pool SQL dedicado existente (anteriormente conhecido como SQL DW) para Gen2 e a agenda de migração por região.
services: synapse-analytics
author: mlee3gsd
ms.author: anjangsh
ms.reviewer: jrasnick
manager: craigg
ms.assetid: 04b05dea-c066-44a0-9751-0774eb84c689
ms.service: synapse-analytics
ms.topic: article
ms.subservice: sql-dw
ms.date: 01/21/2020
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 0ce07ff3ca5fbcc9776792129d3bfb4ef54efe7d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98120114"
---
# <a name="upgrade-your-dedicated-sql-pool-formerly-sql-dw-to-gen2"></a>Atualize seu pool SQL dedicado (anteriormente conhecido como SQL DW) para Gen2

A Microsoft está ajudando a reduzir o custo de nível de entrada da execução de um pool SQL dedicado (anteriormente conhecido como SQL DW).  As camadas de computação mais baixas capazes de lidar com consultas exigentes agora estão disponíveis para o pool SQL dedicado (anteriormente conhecido como SQL DW). Leia o suporte de [camada de computação inferior](https://azure.microsoft.com/blog/azure-sql-data-warehouse-gen2-now-supports-lower-compute-tiers/)do anúncio completo para Gen2. A nova oferta está disponível nas regiões indicadas na tabela a seguir. Para regiões com suporte, o pool SQL dedicado Gen1 (anteriormente conhecido como SQL DW) pode ser atualizado para Gen2 por meio de:

- **O processo de atualização automática:** As atualizações automáticas não são iniciadas assim que o serviço está disponível em uma região.  Quando as atualizações automáticas forem iniciadas em uma região específica, as atualizações data warehouse individuais ocorrerão durante o agendamento de manutenção selecionado.
- [**Atualização automática para Gen2:**](#self-upgrade-to-gen2) Você pode controlar quando atualizar fazendo uma autoatualização para Gen2. Se sua região ainda não tiver suporte, você poderá restaurar de um ponto de restauração diretamente para uma instância do Gen2 em uma região com suporte.

## <a name="automated-schedule-and-region-availability-table"></a>Agendamento automatizado e tabela de disponibilidade na região

A tabela a seguir resume por região quando a camada de computação inferior Gen2 estará disponível e quando as atualizações automáticas serão iniciadas. As datas estão sujeitas à alteração. Consulte a tabela com frequência para saber quando a sua região estará disponível.

\* indica que um agendamento específico para a região está indisponível no momento.

| **Região** | **Gen2 inferior disponível** | **Início das atualizações automáticas** |
|:--- |:--- |:--- |
| Leste do Canadá |1º de junho de 2020 |1º de julho de 2020 |
| Leste da China |\* |\* |
| Norte da China |\* |\* |
| Alemanha Central |\* |\* |
| Centro-Oeste da Alemanha |Disponível |1 de maio de 2020 |
| Oeste da Índia |Disponível |1 de maio de 2020  |

## <a name="automatic-upgrade-process"></a>Processo de atualização automática

Com base no gráfico de disponibilidade acima, vamos agendar atualizações automatizadas para suas instâncias do Gen1. Para evitar quaisquer interrupções inesperadas na disponibilidade do pool de SQL dedicado (anteriormente conhecido como SQL DW), as atualizações automatizadas serão agendadas durante seu agendamento de manutenção. A capacidade de criar uma nova instância de Gen1 será desabilitada em regiões que passam por atualização automática para Gen2. O Gen1 será preterido depois que as atualizações automáticas forem concluídas. Para saber mais sobre agendamentos, consulte [Exibir um agendamento de manutenção](maintenance-scheduling.md#view-a-maintenance-schedule)

O processo de atualização envolverá uma breve queda na conectividade (aproximadamente 5 min) à medida que reiniciamos seu pool SQL dedicado (anteriormente conhecido como SQL DW).  Depois que seu pool SQL dedicado (anteriormente conhecido como SQL DW) for reiniciado, ele estará totalmente disponível para uso. No entanto, você pode enfrentar uma degradação no desempenho enquanto o processo de atualização continua a atualizar os arquivos de dados em segundo plano. O tempo total para a degradação do desempenho varia de acordo com o tamanho dos seus arquivos de dados.

Você também pode agilizar o processo de atualização do arquivo de dados executando [Alter Index rebuild](sql-data-warehouse-tables-index.md) em todas as tabelas columnstore primárias usando uma classe maior de SLO e recursos após a reinicialização.

> [!NOTE]
> Alter Index rebuild é uma operação offline e as tabelas não estarão disponíveis até que a recompilação esteja concluída.

## <a name="self-upgrade-to-gen2"></a>Autoatualização para Gen2

Você pode optar por AutoAtualizar seguindo estas etapas em um pool SQL dedicado de Gen1 existente (anteriormente conhecido como SQL DW). Se você optar por atualizar automaticamente, deverá concluí-lo antes que o processo de atualização automática comece na sua região. Isso garante que você evite qualquer risco de atualizações automáticas causando um conflito.

Há duas opções ao realizar uma autoatualização.  Você pode atualizar seu pool SQL dedicado atual (anteriormente conhecido como SQL DW) ou pode restaurar um pool de SQL dedicado Gen1 (anteriormente conhecido como SQL DW) em uma instância Gen2.

- [Atualização in-loco](upgrade-to-latest-generation.md) -essa opção atualizará seu pool SQL dedicado Gen1 (anteriormente conhecido como SQL DW) para Gen2. O processo de atualização envolverá uma breve queda na conectividade (aproximadamente 5 min) à medida que reiniciamos seu pool SQL dedicado (anteriormente conhecido como SQL DW).  Depois de reiniciado, ele estará totalmente disponível para uso. Se você tiver problemas durante a atualização, abra uma [solicitação de suporte](sql-data-warehouse-get-started-create-support-ticket.md) e faça referência a "atualização Gen2" como a possível causa.
- [Atualização do ponto de restauração](sql-data-warehouse-restore-points.md) – crie um ponto de restauração definido pelo usuário no pool SQL dedicado Gen1 atual (anteriormente conhecido como SQL DW) e, em seguida, restaure diretamente para uma instância do Gen2. O pool de SQL dedicado Gen1 existente (anteriormente conhecido como SQL DW) permanecerá em vigor. Depois que a restauração for concluída, seu pool SQL dedicado do Gen2 (anteriormente conhecido como SQL DW) estará totalmente disponível para uso.  Depois de executar todos os processos de teste e validação na instância Gen2 restaurada, a instância Gen1 original pode ser excluída.

  - Etapa 1: no portal do Azure, [crie um ponto de restauração definido pelo usuário](sql-data-warehouse-restore-active-paused-dw.md).
  - Etapa 2: ao restaurar de um ponto de restauração definido pelo usuário, defina o "nível de desempenho" para sua camada de Gen2 preferida.

Talvez você perceba um período de degradação no desempenho enquanto o processo continua atualizando os arquivos de dados em segundo plano. O tempo total para a degradação do desempenho varia de acordo com o tamanho dos seus arquivos de dados.

Para acelerar o processo de migração de dados em segundo plano, você pode forçar imediatamente a movimentação de dados executando [Alter Index rebuild](sql-data-warehouse-tables-index.md) em todas as tabelas de columnstore primárias que você estaria consultando em uma classe de recursos e SLO maior.

> [!NOTE]
> Alter Index rebuild é uma operação offline e as tabelas não estarão disponíveis até que a recompilação esteja concluída.

Se você encontrar problemas com seu pool SQL dedicado (anteriormente conhecido como SQL DW), crie uma [solicitação de suporte](sql-data-warehouse-get-started-create-support-ticket.md) e faça referência a "atualização Gen2" como a possível causa.

Para saber mais, confira [Atualizar para Gen2](upgrade-to-latest-generation.md).

## <a name="migration-frequently-asked-questions"></a>Perguntas frequentes sobre migração

**P: o Gen2 custa o mesmo que Gen1?**

- A: Sim.

**P: como as atualizações afetarão meus scripts de automação?**

- R: qualquer script de automação que faz referência a um objetivo de nível de serviço deve ser alterado para corresponder ao equivalente de Gen2.  Veja os detalhes [aqui](upgrade-to-latest-generation.md#upgrade-in-a-supported-region-using-the-azure-portal).

**P: quanto tempo uma autoatualização normalmente leva?**

- R: você pode atualizar no local ou atualizar de um ponto de restauração.

  - A atualização in-loco fará com que seu pool de SQL dedicado (anteriormente o SQL DW) PAUSE e retome momentaneamente.  Um processo em segundo plano continuará enquanto o pool do SQL dedicado (antigo SQL DW) estiver online.  
  - Será mais demorado se você atualizar usando um ponto de restauração, pois a atualização passará pelo processo completo de restauração.

**P: quanto tempo ocorrerá a atualização automática?**

- R: o tempo de inatividade real para a atualização é a única vez que leva para pausar e retomar o serviço, que está entre 5 e 10 minutos. Após esse breve tempo de inatividade, um processo em segundo plano executará uma migração de armazenamento. O período de tempo para o processo em segundo plano depende do tamanho do seu pool SQL dedicado (anteriormente conhecido como SQL DW).

**P: quando essa atualização automática ocorrerá?**

- R: durante o agendamento de manutenção. Aproveitar o agendamento de manutenção escolhido minimizará a interrupção nos seus negócios.

**P: o que devo fazer se meu processo de atualização em segundo plano parecer estar preso?**

- R: disparar uma reindexação de suas tabelas Columnstore. Observe que a reindexação da tabela será offline durante essa operação.

**P: e se Gen2 não tiver o objetivo de nível de serviço que tenho em Gen1?**

- R: se você estiver executando um DW600 ou DW1200 no Gen1, é aconselhável usar DW500c ou DW1000c respectivamente, já que Gen2 fornece mais memória, recursos e melhor desempenho do que o Gen1.

**P: posso desabilitar o backup geográfico?**

- R: Não. O backup geográfico é um recurso empresarial para preservar a disponibilidade do pool SQL dedicado (antes do SQL DW) no caso de uma região ficar indisponível. Abra uma [solicitação de suporte](sql-data-warehouse-get-started-create-support-ticket.md) em caso de outras dúvidas.

**P: há uma diferença na sintaxe de T-SQL entre Gen1 e Gen2?**

- R: não há nenhuma alteração na sintaxe da linguagem T-SQL de Gen1 para Gen2.

**P: o Gen2 dá suporte a janelas de manutenção?**

- A: Sim.

**P: poderei criar uma nova instância do Gen1 depois que minha região for atualizada?**

- R: Não. Depois que uma região for atualizada, a criação de novas instâncias de Gen1 será desabilitada.

## <a name="next-steps"></a>Próximas etapas

- [Etapas de atualização](upgrade-to-latest-generation.md)
- [Janelas de manutenção](maintenance-scheduling.md)
- [Monitoramento da integridade de recursos](../../service-health/resource-health-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
- [Revise o artigo Antes de começar a migração](upgrade-to-latest-generation.md#before-you-begin)
- [Atualizar in-loco e atualizar de um ponto de restauração](upgrade-to-latest-generation.md)
- [Crie um ponto restauração definido pelo usuário](sql-data-warehouse-restore-points.md)
- [Saiba como restaurar para Gen2](sql-data-warehouse-restore-active-paused-dw.md)
- [Abrir uma solicitação de suporte do Azure Synapse Analytics](./sql-data-warehouse-get-started-create-support-ticket.md)