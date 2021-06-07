---
title: 'Lista de verificação: práticas recomendadas de desempenho & diretrizes'
description: Fornece uma lista de verificação rápida para examinar suas práticas recomendadas e diretrizes para otimizar o desempenho do seu SQL Server na VM (máquina virtual) do Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/25/2021
ms.author: mathoma
ms.custom: contperf-fy21q3
ms.reviewer: jroth
ms.openlocfilehash: 0b88884576a47db871c78b874104d4973ee9ba9a
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105572314"
---
# <a name="checklist-performance-best-practices-for-sql-server-on-azure-vms"></a>Lista de verificação: práticas recomendadas de desempenho para SQL Server em VMs do Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este artigo fornece uma lista de verificação rápida como uma série de práticas recomendadas e diretrizes para otimizar o desempenho de seus SQL Server em VMs (máquinas virtuais) do Azure. 

Para obter detalhes abrangentes, consulte os outros artigos desta série: [tamanho da VM](performance-guidelines-best-practices-vm-size.md), [armazenamento](performance-guidelines-best-practices-storage.md), [coletar linha de base](performance-guidelines-best-practices-collect-baseline.md). 


## <a name="overview"></a>Visão geral

Ao executar SQL Server em máquinas virtuais do Azure, continue usando as mesmas opções de ajuste de desempenho do banco de dados que são aplicáveis a SQL Server em ambientes de servidor local. No entanto, o desempenho de um banco de dados relacional em uma nuvem pública depende de muitos fatores, como o tamanho de uma máquina virtual e a configuração dos discos de dados.

Normalmente, há uma compensação entre a otimização para custos e a otimização para desempenho. Essa série de práticas recomendadas de desempenho se concentra em obter o *melhor* desempenho para SQL Server em máquinas virtuais do Azure. Se sua carga de trabalho for menos exigente, talvez você não precise de todas as otimizações recomendadas. Considere suas necessidades de desempenho, custos e padrões de carga de trabalho ao avaliar essas recomendações.

## <a name="vm-size"></a>Tamanho da VM

Veja a seguir uma lista de verificação rápida de práticas recomendadas de tamanho de VM para executar seu SQL Server na VM do Azure: 

- Use tamanhos de VM com 4 ou mais vCPU, como o [Standard_M8-4ms](/../../virtual-machines/m-series), o [E4ds_v4](../../../virtual-machines/edv4-edsv4-series.md#edv4-series)ou o [DS12_v2](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) ou superior. 
- Use tamanhos de máquina virtual com [otimização de memória](../../../virtual-machines/sizes-memory.md) para obter o melhor desempenho de cargas de trabalho de SQL Server. 
- A série [DSv2 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md), [Edsv4](../../../virtual-machines/edv4-edsv4-series.md) , a [M-](../../../virtual-machines/m-series.md)e a série [Mv2](../../../virtual-machines/mv2-series.md) oferecem a proporção ideal de memória para vCore necessária para cargas de trabalho OLTP. Ambas as VMs da série M oferecem a maior taxa de memória para vCore necessária para cargas de trabalho críticas e também são ideais para cargas de trabalho de data warehouse. 
- Considere uma taxa maior de memória para vCore para cargas de trabalho de missão crítica e data warehouse. 
- Use as imagens do Marketplace de máquina virtual do Azure, pois as configurações de SQL Server e as opções de armazenamento estão configuradas para um desempenho de SQL Server ideal. 
- Colete as características de desempenho da carga de trabalho de destino e use-as para determinar o tamanho apropriado da VM para sua empresa.

Para saber mais, Confira as [práticas recomendadas de tamanho de VM](performance-guidelines-best-practices-vm-size.md)abrangente. 

## <a name="storage"></a>Armazenamento

Veja a seguir uma lista de verificação rápida das práticas recomendadas de configuração de armazenamento para executar seu SQL Server na VM do Azure: 

- Monitore o aplicativo e [determine a largura de banda e os requisitos de latência de armazenamento](../../../virtual-machines/premium-storage-performance.md#counters-to-measure-application-performance-requirements) para SQL Server dados, log e arquivos tempdb antes de escolher o tipo de disco. 
- Para otimizar o desempenho do armazenamento, planeje os mais altos IOPS não armazenados em cache disponíveis e use o cache de dados como um recurso de desempenho para leituras de dados, ao mesmo tempo que evita a [máquina virtual e o acelerador/limitação de discos](../../../virtual-machines/premium-storage-performance.md#throttling).
- Coloque dados, log e arquivos tempdb em unidades separadas.
    - Para a unidade de dados, use apenas [discos p30 e P40 Premium](../../../virtual-machines/disks-types.md#premium-ssd) para garantir a disponibilidade do suporte de cache
    - Para o plano de unidade de log para desempenho de capacidade e teste versus custo ao avaliar os [discos Premium p30-P80](../../../virtual-machines/disks-types.md#premium-ssd).
      - Se a latência de armazenamento de submilissegundos for necessária, use os [ultra discos do Azure](../../../virtual-machines/disks-types.md#ultra-disk) para o log de transações. 
      - Para implantações de máquina virtual série M, considere [acelerador de gravação](../../../virtual-machines/how-to-enable-write-accelerator.md) usando os ultra discos do Azure.
    - Coloque o [tempdb](/sql/relational-databases/databases/tempdb-database) na unidade SSD efêmera local `D:\` para a maioria das cargas de trabalho SQL Server depois de escolher o tamanho de VM ideal. 
      - Se a capacidade da unidade local não for suficiente para o tempdb, considere o dimensionamento da VM. Consulte [Data File Caching Policies](performance-guidelines-best-practices-storage.md#data-file-caching-policies) para obter mais informações.
- Distribua vários discos de dados do Azure usando [espaços de armazenamento](/windows-server/storage/storage-spaces/overview) para aumentar a largura de banda de e/s até os limites de IOPS e taxa de transferência da máquina virtual de destino.
- Defina o [cache de host](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) como somente leitura para discos de arquivo de dados.
- Defina o [cache de host](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) como nenhum para discos de arquivo de log.
    - Não habilite o cache de leitura/gravação em discos que contêm SQL Server arquivos. 
    - Sempre pare o serviço de SQL Server antes de alterar as configurações de cache do disco.
- Para cargas de trabalho de desenvolvimento e teste, considere o uso de armazenamento padrão. Não é recomendável usar HDD Standard/SSD para cargas de trabalho de produção.
- [A intermitência de disco baseada em crédito](../../../virtual-machines/disk-bursting.md#credit-based-bursting) (P1-P20) só deve ser considerada para cargas de trabalho de desenvolvimento/teste menores e sistemas departamentais.
- Provisione a conta de armazenamento na mesma região que a VM SQL Server. 
- Desabilite o armazenamento com redundância geográfica do Azure (replicação geográfica) e use LRS (armazenamento com redundância local) na conta de armazenamento.
- Formate seu disco de dados para usar o tamanho da unidade de alocação de 64 KB para todos os arquivos de dados colocados em uma unidade que não seja a `D:\` unidade temporária (que tem um padrão de 4 KB). SQL Server VMs implantadas por meio do Azure Marketplace vêm com discos de dados formatados com tamanho de unidade de alocação e intercalação para o pool de armazenamento definido como 64 KB. 

Para saber mais, Confira as [práticas recomendadas de armazenamento](performance-guidelines-best-practices-storage.md)abrangentes. 


## <a name="azure--sql-feature-specific"></a>Recurso do Azure & SQL específico

Veja a seguir uma lista de verificação rápida de práticas recomendadas para SQL Server e configurações específicas do Azure ao executar o SQL Server na VM do Azure: 

- Registre-se com a [extensão do agente IaaS do SQL](sql-agent-extension-manually-register-single-vm.md) para desbloquear vários [benefícios de recursos](sql-server-iaas-agent-extension-automate-management.md#feature-benefits). 
- Habilite a compactação de página do banco de dados.
- Habilite a inicialização instantânea de arquivos para arquivos de dados.
- Limite o aumento automático do banco de dados.
- Desabilite a redução automática do banco de dados.
- Mova todos os bancos de dados para discos de dados, incluindo bancos de dados do sistema.
- Mova o log de erros do SQL Server e os diretórios de arquivos de rastreamento para discos de dados
- Configurar locais de arquivo de banco de dados e backup padrão.
- [Habilitar páginas bloqueadas na memória](/sql/database-engine/configure-windows/enable-the-lock-pages-in-memory-option-windows).
- Avalie e aplique as [atualizações cumulativas mais recentes](/sql/database-engine/install-windows/latest-updates-for-microsoft-sql-server) para a versão instalada do SQL Server.
- Faça backup diretamente no armazenamento de BLOBs do Azure.
- Use vários arquivos [tempdb](/sql/relational-databases/databases/tempdb-database#optimizing-tempdb-performance-in-sql-server) , 1 arquivo por núcleo, até 8 arquivos.



## <a name="next-steps"></a>Próximas etapas

Para saber mais, consulte os outros artigos desta série:
- [Tamanho da VM](performance-guidelines-best-practices-vm-size.md)
- [Storage](performance-guidelines-best-practices-storage.md)
- [Coletar linha de base](performance-guidelines-best-practices-collect-baseline.md)

Para obter as práticas recomendadas de segurança, consulte [considerações de segurança para SQL Server em máquinas virtuais do Azure](security-considerations-best-practices.md).

Revise outros artigos sobre Máquinas Virtuais do SQL Server em [Visão geral do SQL Server nas Máquinas Virtuais do Azure](sql-server-on-azure-vm-iaas-what-is-overview.md). Em caso de dúvidas sobre máquinas virtuais do SQL Server, consulte as [Perguntas frequentes](frequently-asked-questions-faq.md).
