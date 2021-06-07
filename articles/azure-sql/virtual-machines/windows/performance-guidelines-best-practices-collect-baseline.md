---
title: 'Coletar linha de base: práticas recomendadas de desempenho & diretrizes'
description: Fornece etapas para coletar uma linha de base de desempenho como diretrizes para otimizar o desempenho do seu SQL Server na VM (máquina virtual) do Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/25/2021
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: b1909d5fd5e3c02f104c73acb515740cb6ff65f6
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105572313"
---
# <a name="collect-baseline-performance-best-practices-for-sql-server-on-azure-vm"></a>Coletar linha de base: práticas recomendadas de desempenho para SQL Server na VM do Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este artigo fornece informações para coletar uma linha de base de desempenho como uma série de práticas recomendadas e diretrizes para otimizar o desempenho para seu SQL Server em VMs (máquinas virtuais) do Azure.

Normalmente, há uma compensação entre a otimização para custos e a otimização para desempenho. Essa série de práticas recomendadas de desempenho se concentra em obter o *melhor* desempenho para SQL Server em máquinas virtuais do Azure. Se sua carga de trabalho for menos exigente, talvez você não precise de todas as otimizações recomendadas. Considere suas necessidades de desempenho, custos e padrões de carga de trabalho ao avaliar essas recomendações.

## <a name="overview"></a>Visão geral

Para uma abordagem prescritiva, reúna contadores de desempenho usando PerfMon/LogMan e Capture SQL Server estatísticas de espera para entender melhor as pressões gerais e os gargalos potenciais do ambiente de origem. 

Comece coletando a CPU, a memória, o [IOPS](../../../virtual-machines/premium-storage-performance.md#iops), a [taxa de transferência](../../../virtual-machines/premium-storage-performance.md#throughput)e a [latência](../../../virtual-machines/premium-storage-performance.md#latency) da carga de trabalho de origem em horários de pico após a lista de verificação de [desempenho do aplicativo](../../../virtual-machines/premium-storage-performance.md#application-performance-requirements-checklist). 

Reúna dados durante horários de pico, como cargas de trabalho durante o dia útil normal, mas também outros processos de carga alta, como processamento de fim de dia e cargas de trabalho ETL de fim de semana. Considere a possibilidade de escalar verticalmente seus recursos para cargas de trabalho pesadas do atypically, como o processamento do fim do trimestre, e depois o dimensionamento feito após a conclusão da carga de trabalho. 

Use a análise de desempenho para selecionar o [tamanho da VM](../../../virtual-machines/sizes-memory.md) que pode ser dimensionado para os requisitos de desempenho da carga de trabalho.


## <a name="storage"></a>Armazenamento

SQL Server desempenho depende muito do subsistema de e/s e o desempenho do armazenamento é medido por IOPS e taxa de transferência. A menos que seu banco de dados caiba na memória física, SQL Server constantemente leva as páginas do banco de dados para dentro e para fora do pool de buffers. Os arquivos de dados para SQL Server devem ser tratados de forma diferente. O acesso a arquivos de log é seqüencial, exceto quando uma transação precisa ser revertida onde os arquivos de dados, incluindo tempdb, são acessados aleatoriamente. Se você tiver um subsistema de e/s lento, os usuários poderão enfrentar problemas de desempenho, como tempos de resposta lentos e tarefas que não são concluídas devido a tempos limite. 

As máquinas virtuais do Azure Marketplace têm arquivos de log em um disco físico separado dos arquivos de dados por padrão. A contagem e o tamanho dos arquivos de dados do tempdb atendem às práticas recomendadas e são direcionadas para a `D:\` unidade efêmera. 

Os contadores PerfMon a seguir podem ajudar a validar a taxa de transferência de e/s exigida por seu SQL Server: 
* **\LogicalDisk\Disk leituras de páginas/s** (IOPS de leitura)
* **Gravações de \LogicalDisk\Disk/s** (IOPS de gravação) 
* **Bytes de leitura de \LogicalDisk\Disk/s** (requisitos de taxa de transferência de leitura para os arquivos de dados, log e tempdb)
* **Bytes de gravação de \LogicalDisk\Disk/s** (requisitos de taxa de transferência de gravação para os arquivos de dados, log e tempdb)

Usando requisitos de IOPS e taxa de transferência em níveis de pico, avalie os tamanhos de VM que correspondem à capacidade de suas medições. 

Se sua carga de trabalho exigir IOPS de leitura de 20 mil e IOPS de gravação de 10K, você poderá escolher E16s_v3 (com até 32 k armazenados em cache e 25600 IOPS sem cache) ou M16_s (com até 20 mil de cache e 10.000 IOPS não armazenados em cache) com 2 discos p30 distribuídos usando espaços de armazenamento. 

Certifique-se de entender os requisitos de taxa de transferência e IOPS da carga de trabalho, pois as VMs têm limites de escala diferentes para IOPS e taxa de transferência.

## <a name="memory"></a>Memória

Acompanhe a memória externa usada pelo sistema operacional, bem como a memória usada internamente pelo SQL Server. A identificação da pressão para qualquer componente ajudará a dimensionar as máquinas virtuais e a identificar as oportunidades de ajuste. 

Os contadores PerfMon a seguir podem ajudar a validar a integridade da memória de uma máquina virtual SQL Server: 
* [\Memória\MBytes disponíveis](/azure/monitoring/infrastructure-health/vmhealth-windows/winserver-memory-availmbytes)
* [\SQLServer: memória do servidor Manager\Target de memória (KB)](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer: memória do servidor Manager\Total de memória (KB)](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer: gravações de Manager\Lazy de buffer/s](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer: expectativa de vida Buffer\leituras do buffer](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)

## <a name="compute"></a>Computação

A computação no Azure é gerenciada de maneira diferente do local. Os servidores locais são criados nos últimos anos, sem uma atualização devido à sobrecarga de gerenciamento e ao custo de aquisição de novo hardware. A virtualização atenua alguns desses problemas, mas os aplicativos são otimizados para aproveitar ao máximo o hardware subjacente, o que significa que qualquer alteração significativa no consumo de recursos requer o rebalanceamento de todo o ambiente físico. 

Isso não é um desafio no Azure, em que uma nova máquina virtual em uma série diferente de hardware, e até mesmo em uma região diferente, é fácil de alcançar. 

No Azure, você deseja aproveitar o máximo possível dos recursos de máquinas virtuais, portanto, as máquinas virtuais do Azure devem ser configuradas para manter a média de CPU máxima, sem afetar a carga de trabalho. 

Os contadores PerfMon a seguir podem ajudar a validar a integridade de computação de uma máquina virtual SQL Server:
* **Tempo de processador de informações de \Processor (_Total) \%**
* **\Process (sqlservr) \% tempo do processador**

> [!NOTE] 
> Idealmente, tente visar o uso de 80% de sua computação, com picos acima de 90%, mas não atingindo 100% por um período de tempo prolongado. Fundamentalmente, você só deseja provisionar a computação de que o aplicativo precisa e, em seguida, planejar para escalar ou reduzir verticalmente conforme a necessidade dos negócios. 


## <a name="next-steps"></a>Próximas etapas

Para saber mais, consulte os outros artigos desta série:
- [Lista de verificação rápida](performance-guidelines-best-practices-checklist.md)
- [Tamanho da VM](performance-guidelines-best-practices-vm-size.md)
- [Storage](performance-guidelines-best-practices-storage.md)


Para obter as práticas recomendadas de segurança, consulte [considerações de segurança para SQL Server em máquinas virtuais do Azure](security-considerations-best-practices.md).

Revise outros artigos sobre Máquinas Virtuais do SQL Server em [Visão geral do SQL Server nas Máquinas Virtuais do Azure](sql-server-on-azure-vm-iaas-what-is-overview.md). Em caso de dúvidas sobre máquinas virtuais do SQL Server, consulte as [Perguntas frequentes](frequently-asked-questions-faq.md).
