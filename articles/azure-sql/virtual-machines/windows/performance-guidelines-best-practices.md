---
title: Diretrizes de desempenho para o SQL Server no Azure | Microsoft Docs
description: Fornece diretrizes para otimizar o desempenho de SQL Server no Máquinas Virtuais do Microsoft Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.subservice: performance
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/09/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 6ba30436b363353ad183396e07111b33ca912dbf
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98737414"
---
# <a name="performance-guidelines-for-sql-server-on-azure-virtual-machines"></a>Diretrizes de desempenho para o SQL Server em Máquinas Virtuais do Microsoft Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este artigo fornece diretrizes para otimizar o desempenho de SQL Server no Máquinas Virtuais do Microsoft Azure.

## <a name="overview"></a>Visão geral

Ao executar SQL Server em máquinas virtuais do Azure, recomendamos que você continue usando as mesmas opções de ajuste de desempenho do banco de dados que são aplicáveis a SQL Server em ambientes de servidor local. No entanto, o desempenho de um banco de dados relacional em uma nuvem pública depende de muitos fatores como, por exemplo, o tamanho de uma máquina virtual e a configuração dos discos de dados.

[SQL Server imagens provisionadas no portal do Azure](sql-vm-create-portal-quickstart.md) seguir as [práticas recomendadas de configuração](storage-configuration.md)de armazenamento geral. Após o provisionamento, considere aplicar outras otimizações discutidas neste artigo. Baseie suas escolhas na carga de trabalho e verifique por meio de testes.

> [!TIP]
> Normalmente, há uma compensação entre a otimização para custos e a otimização para desempenho. Este artigo se concentra em obter o *melhor* desempenho para SQL Server em máquinas virtuais do Azure. Se sua carga de trabalho for menos exigente, talvez você não precise de todos os aprimoramentos relacionados abaixo. Considere suas necessidades de desempenho, custos e padrões de carga de trabalho ao avaliar essas recomendações.

## <a name="quick-checklist"></a>Lista de verificação rápida

Veja a seguir uma lista de verificação rápida para o desempenho ideal de SQL Server em máquinas virtuais do Azure:

| Área | Otimizações |
| --- | --- |
| [Tamanho da VM](#vm-size-guidance) | -Use tamanhos de VM com 4 ou mais vCPU, como o [Standard_M8-4ms](../../../virtual-machines/m-series.md), o [E4ds_v4](../../../virtual-machines/edv4-edsv4-series.md#edv4-series)ou o [DS12_v2](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) ou superior. <br/><br/> -Use tamanhos de máquina virtual com [otimização de memória](../../../virtual-machines/sizes-memory.md) para obter o melhor desempenho de cargas de trabalho de SQL Server. <br/><br/> -A série [DSv2 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md), [Edsv4](../../../virtual-machines/edv4-edsv4-series.md) , a [M-](../../../virtual-machines/m-series.md)e a série [Mv2](../../../virtual-machines/mv2-series.md) oferecem a proporção ideal de memória para vCore necessária para cargas de trabalho OLTP. Ambas as VMs da série M oferecem a maior taxa de memória para vCore necessária para cargas de trabalho críticas e também é ideal para cargas de trabalho de data warehouse. <br/><br/> -Uma taxa maior de memória para vCore pode ser necessária para cargas de trabalho de missão crítica e data warehouse. <br/><br/> -Aproveite as imagens do Marketplace de máquina virtual do Azure, pois as configurações de SQL Server e as opções de armazenamento estão configuradas para um desempenho de SQL Server ideal. <br/><br/> -Coletar as características de desempenho da carga de trabalho de destino e usá-las para determinar o tamanho apropriado da VM para seu negócio.|
| [Armazenamento](#storage-guidance) | -Para obter testes detalhados de desempenho de SQL Server em máquinas virtuais do Azure com o TPC-E e TPC_C parâmetros de comparação, consulte o blog [otimizar o desempenho do OLTP](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794). <br/><br/> – Use [SSDs Premium](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794) para obter as melhores vantagens de preço/desempenho. Configure o [cache somente leitura](../../../virtual-machines/premium-storage-performance.md#disk-caching) para arquivos de dados e nenhum cache para o arquivo de log. <br/><br/> -Use [ultra discos](../../../virtual-machines/disks-types.md#ultra-disk) se menos de 1-MS latências de armazenamento forem exigidas pela carga de trabalho. Consulte [migrar para o ultra Disk](storage-migrate-to-ultradisk.md) para saber mais. <br/><br/> – Colete os requisitos de latência de armazenamento para dados do SQL Server, arquivos de log e TempDB [monitorando o aplicativo](../../../virtual-machines/premium-storage-performance.md#application-performance-requirements-checklist) antes de escolher o tipo de disco. Se < as latências de armazenamento de 1 MS forem necessárias, use ultra discos, caso contrário, use o SSD Premium. Se latências baixas forem necessárias apenas para o arquivo de log e não para arquivos de dados, [provisione o Disco Ultra](../../../virtual-machines/disks-enable-ultra-ssd.md) nos níveis de taxa de transferência e IOPS necessários somente para o arquivo de log. <br/><br/>  – O Armazenamento Standard é recomendado apenas para fins de desenvolvimento e teste ou para arquivos de backup e não deve ser usado para cargas de trabalho de produção. <br/><br/> - Mantenha a [conta de armazenamento](../../../storage/common/storage-account-create.md) e a VM do SQL Server na mesma região.<br/><br/> – Desabilite o [armazenamento com redundância geográfica](../../../storage/common/storage-redundancy.md) (replicação geográfica) do Azure na conta de armazenamento.  |
| [Discos](#disks-guidance) | – Usar no mínimo dois [discos SSD Premium](../../../virtual-machines/disks-types.md#premium-ssd) (um para arquivo de log e um para arquivos de dados). <br/><br/> -Para cargas de trabalho que exigem < latências de e/s de 1 ms, habilite o acelerador de gravação para a série M e considere usar discos SSD Ultra para a série es e DS. <br/><br/> – Habilite o [cache somente leitura](../../../virtual-machines/premium-storage-performance.md#disk-caching) nos discos que hospedam os arquivos de dados.<br/><br/> -Adicionar uma capacidade adicional de IOPS/taxa de transferência de 20% do que sua carga de trabalho requer ao [Configurar o armazenamento para SQL Server dados, log e arquivos tempdb](storage-configuration.md) <br/><br/> - Evite usar o sistema operacional ou discos temporários para armazenamento de banco de dados ou registro em log.<br/><br/> - Não habilite o cache em discos que hospedam o arquivo de log.  **Importante**: Pare o serviço de SQL Server ao alterar as configurações de cache para um disco de máquinas virtuais do Azure.<br/><br/> – Particione vários discos de dados do Azure para obter maior taxa de transferência de armazenamento.<br/><br/> - Formate com os tamanhos de alocação documentados. <br/><br/> – Coloque o TempDB na unidade `D:\` SSD local para cargas de trabalho do SQL Server críticas (depois de escolher o tamanho correto de VM). Se você criar a VM no portal do Azure ou nos modelos de início rápido do Azure e inserir o banco de informações [temporário no disco local](https://techcommunity.microsoft.com/t5/SQL-Server/Announcing-Performance-Optimized-Storage-Configuration-for-SQL/ba-p/891583), você não precisará de nenhuma ação adicional; para todos os outros casos, siga as etapas no blog para  [usar o SSDs para armazenar o tempdb](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-TempDB-and-buffer-pool-extensions/) para evitar falhas após a reinicialização. Se a capacidade da unidade local não for suficiente para o tamanho do TempDB, coloque o TempDB em um pool de armazenamento [distribuído](../../../virtual-machines/premium-storage-performance.md) em discos SSD Premium com [cache somente leitura](../../../virtual-machines/premium-storage-performance.md#disk-caching). |
| [E/S](#io-guidance) |- Habilite a compactação de página do banco de dados.<br/><br/> - Habilite a inicialização instantânea de arquivos para arquivos de dados.<br/><br/> - Limite o aumento automático do banco de dados.<br/><br/> - Desabilite a redução automática no banco de dados.<br/><br/> - Mova todos os bancos de dados para discos de dados, incluindo bancos de dados do sistema.<br/><br/> - Mova o log de erros do SQL Server e os diretórios de arquivos de rastreamento para discos de dados.<br/><br/> – Configure os locais do arquivo de banco de dados e de backup padrão.<br/><br/> - [Habilitar páginas bloqueadas na memória](/sql/database-engine/configure-windows/enable-the-lock-pages-in-memory-option-windows).<br/><br/> -Avaliar e aplicar as [atualizações cumulativas mais recentes](/sql/database-engine/install-windows/latest-updates-for-microsoft-sql-server) para a versão instalada do SQL Server. |
| [Recursos específicos](#feature-specific-guidance) | -Fazer backup diretamente no armazenamento de BLOBs do Azure.<br/><br/>– Use [backups de instantâneos de arquivo](/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure) para bancos de dados maiores que 12 TB. <br/><br/>– Use vários arquivos TempDB, um arquivo por núcleo, até oito arquivos.<br/><br/>– Defina a memória máxima do servidor para 90% ou até 50 GB restantes para o sistema operacional. <br/><br/>– Habilite o soft NUMA. |


<br/>
Para saber mais sobre *como* e *por que* fazer essas otimizações, examine os detalhes e diretrizes fornecidos nas seções a seguir.
<br/><br/>

## <a name="getting-started"></a>Introdução

Se você estiver criando um novo SQL Server na VM do Azure e não estiver migrando um sistema de origem atual, crie sua nova VM de SQL Server com base em seus requisitos de fornecedor.  Os requisitos do fornecedor para uma VM SQL Server são os mesmos que você implantaria no local. 

Se você estiver criando uma nova VM SQL Server com um novo aplicativo criado para a nuvem, poderá dimensionar facilmente sua VM SQL Server conforme seus requisitos de dados e de uso evoluem.
Inicie os ambientes de desenvolvimento com as séries D-Series, B ou Av2 de nível inferior e aumente seu ambiente ao longo do tempo. 

O mínimo recomendado para um ambiente OLTP de produção é 4 vCore, 32 GB de memória e uma proporção de memória para vCore de 8. Para novos ambientes, comece com 4 máquinas vCores e dimensione para 8, 16, 32 vCores ou mais quando seus requisitos de dados e computação forem alterados. Para a taxa de transferência do OLTP, direcione as VMs de SQL Server que têm 5000 IOPS para cada vCore. 

Use o SQL Server imagens do Marketplace de VM com a configuração de armazenamento no Portal. Isso facilitará a criação apropriada dos pools de armazenamento necessários para obter o tamanho, o IOPS e a taxa de transferência necessários para suas cargas de trabalho. É importante escolher SQL Server VMs que dão suporte ao armazenamento Premium e ao cache de armazenamento Premium. Consulte a seção [armazenamento](#storage-guidance) para saber mais. 

SQL Server data warehouse e ambientes de missão crítica geralmente precisarão ser dimensionados além da proporção de 8 memórias para vCore. Para ambientes médios, talvez você queira escolher uma proporção de 16 núcleos para memória e uma proporção de 32 de núcleo para memória para ambientes maiores de data warehouse. 

SQL Server ambientes de data warehouse geralmente se beneficiam do processamento paralelo de máquinas maiores. Por esse motivo, a série M e a série Mv2 são opções fortes para ambientes maiores de data warehouse.

## <a name="vm-size-guidance"></a>Diretrizes de tamanho de VM

Use o vCPU e a configuração de memória do seu computador de origem como uma linha de base para migrar um banco de dados SQL Server local atual para SQL Server em VMs do Azure. Traga sua principal licença do Azure para aproveitar o [benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) e economize em SQL Server custos de licenciamento.

**A Microsoft recomenda uma proporção de memória para vCore de 8 como um ponto de partida para cargas de trabalho de SQL Server de produção.** As taxas menores são aceitáveis para cargas de trabalho que não são de produção. 

Escolha um tamanho de máquina virtual com [otimização de memória](../../../virtual-machines/sizes-memory.md), [uso geral](../../../virtual-machines/sizes-general.md), [armazenamento otimizado](../../../virtual-machines/sizes-storage.md)ou com [restrição restrita](../../../virtual-machines/constrained-vcpu.md) que seja ideal para SQL Server desempenho com base em sua carga de trabalho (OLTP ou data warehouse). 

### <a name="memory-optimized"></a>Otimizado para memória

Os [tamanhos de máquina virtual com otimização de memória](../../../virtual-machines/sizes-memory.md) são um destino principal para SQL Server VMs e a opção recomendada pela Microsoft. As máquinas virtuais com otimização de memória oferecem taxas mais fortes de memória para CPU e opções de cache de médio a grande porte. 

#### <a name="m-and-mv2-series"></a>Série M e Mv2

A [série M](../../../virtual-machines/m-series.md) oferece contagens de vCore e memória para algumas das maiores cargas de trabalho de SQL Server.  

A [série Mv2](../../../virtual-machines/mv2-series.md) tem as maiores contagens de vCore e memória e é recomendada para cargas de trabalho de missão crítica e data warehouse. As instâncias da série Mv2 são tamanhos de VM com otimização de memória que fornecem desempenho computacional inigualável para dar suporte a grandes bancos de dados na memória e cargas de trabalho com uma alta taxa de memória para CPU que é perfeita para servidores de banco de dados relacionais, caches grandes e análise na memória.

O [Standard_M64ms](../../../virtual-machines/m-series.md) tem uma proporção de memória para vCore de 28, por exemplo.

Alguns dos recursos da série M e Mv2 atraentes para o desempenho de SQL Server incluem [armazenamento Premium](../../../virtual-machines/premium-storage-performance.md) e suporte a [cache de armazenamento Premium](../../../virtual-machines/premium-storage-performance.md#disk-caching) , suporte [a ultra discos](../../../virtual-machines/disks-enable-ultra-ssd.md) e [aceleração de gravação](../../../virtual-machines/how-to-enable-write-accelerator.md).

#### <a name="edsv4-series"></a>Série Edsv4

A [série Edsv4](../../../virtual-machines/edv4-edsv4-series.md) é projetada para aplicativos com uso intensivo de memória. Essas VMs têm uma grande capacidade de SSD de armazenamento local, IOPS de disco local forte, até 504 GiB de RAM e computação aprimorada em comparação com os tamanhos de Ev3/Esv3 anteriores com VMs Gen2. Há uma proporção quase consistente de memória para vCore de 8 em todas essas máquinas virtuais, o que é ideal para cargas de trabalho de SQL Server padrão. 

Essa série de VMs é ideal para aplicativos empresariais com uso intensivo de memória e aplicativos que se beneficiam de armazenamento local de alta velocidade e de baixa latência.

As máquinas virtuais da série Edsv4 dão suporte ao [armazenamento Premium](../../../virtual-machines/premium-storage-performance.md)e ao [cache de armazenamento Premium](../../../virtual-machines/premium-storage-performance.md#disk-caching).

#### <a name="dsv2-series-11-15"></a>Série DSv2 11-15

A [série DSv2 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) tem as mesmas configurações de memória e disco que a série D anterior. Esta série tem uma proporção consistente de memória para CPU de 7 em todas as máquinas virtuais. 

A [série DSv2 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) dá suporte ao [armazenamento Premium](../../../virtual-machines/premium-storage-performance.md) e ao [cache de armazenamento Premium](../../../virtual-machines/premium-storage-performance.md#disk-caching), que é altamente recomendável para um desempenho ideal.

### <a name="general-purpose"></a>Uso Geral

Os [tamanhos de máquina virtual de uso geral](../../../virtual-machines/sizes-general.md) são projetados para fornecer taxas balanceadas de memória para vCore para cargas de trabalho de nível de entrada menores, como desenvolvimento e teste, servidores Web e servidores de banco de dados menores. 

Por causa das taxas menores de memória para vCore com as máquinas virtuais de uso geral, é importante monitorar cuidadosamente os contadores de desempenho baseados em memória para garantir que SQL Server seja capaz de obter a memória cache de buffer de que precisa. Consulte [linha de base de desempenho de memória](#memory) para obter mais informações. 

Como a recomendação inicial para cargas de trabalho de produção é uma proporção de memória para vCore de 8, a configuração mínima recomendada para uma VM de uso geral em execução SQL Server é 4 vCPU e 32 GB de memória. 

#### <a name="ddsv4-series"></a>Série Ddsv4

A [série Ddsv4](../../../virtual-machines/ddv4-ddsv4-series.md) oferece uma combinação justa de vCPU, memória e disco temporário, mas com suporte menor de memória para vCore. 

As VMs Ddsv4 incluem menor latência e armazenamento local de velocidade mais alta.

Esses computadores são ideais para implantações de aplicativo e SQL lado a lado que exigem acesso rápido aos bancos de dados de armazenamento temporário e relacional departamental. Há uma proporção de memória para vCore padrão de 4 em todas as máquinas virtuais nesta série. 

Por esse motivo, é recomendável aproveitar o D8ds_v4 como a máquina virtual inicial nesta série, que tem 8 vCores e 32 GBs de memória. O maior computador é o D64ds_v4, que tem 64 vCores e 256 GB de memória.

As máquinas virtuais da [série Ddsv4](../../../virtual-machines/ddv4-ddsv4-series.md) dão suporte ao [armazenamento Premium](../../../virtual-machines/premium-storage-performance.md) e ao [cache de armazenamento Premium](../../../virtual-machines/premium-storage-performance.md#disk-caching).

> [!NOTE]
> A [série Ddsv4](../../../virtual-machines/ddv4-ddsv4-series.md) não tem a proporção de memória para vCore de 8 que é recomendada para cargas de trabalho de SQL Server. Como tal, considerando o uso dessas máquinas virtuais apenas para cargas de trabalho de desenvolvimento e aplicativos menores.

#### <a name="b-series"></a>Série B

Os tamanhos de máquina virtual da [série B expansível](../../../virtual-machines/sizes-b-series-burstable.md) são ideais para cargas de trabalho que não precisam de desempenho consistente, como prova de conceito e servidores de aplicativos e desenvolvimento muito pequenos. 

A maioria dos tamanhos de máquina virtual da [série B expansível](../../../virtual-machines/sizes-b-series-burstable.md) tem uma proporção de memória para vCore de 4. A maior dessas máquinas é a [Standard_B20ms](../../../virtual-machines/sizes-b-series-burstable.md) com 20 vCores e 80 GB de memória.

Esta série é exclusiva, pois os aplicativos têm a capacidade **de aumentar durante o** horário comercial com créditos intermitentes variados com base no tamanho da máquina. 

Quando os créditos são esgotados, a VM retorna ao desempenho do computador de linha de base.

O benefício da série B é a economia de computação que pode ser obtida em comparação com os outros tamanhos de VM em outras séries, especialmente se você precisar da capacidade de processamento de maneira moderada ao longo do dia.

Esta série dá suporte ao [armazenamento Premium](../../../virtual-machines/premium-storage-performance.md), mas não **dá suporte ao** [cache de armazenamento Premium](../../../virtual-machines/premium-storage-performance.md#disk-caching).

> [!NOTE] 
> A [série B expansível](../../../virtual-machines/sizes-b-series-burstable.md) não tem a proporção de memória para vCore de 8 que é recomendada para cargas de trabalho de SQL Server. Como tal, considere o uso dessas máquinas virtuais para aplicativos menores, servidores Web e cargas de trabalho de desenvolvimento.

#### <a name="av2-series"></a>Série Av2

As VMs da [série Av2](../../../virtual-machines/av2-series.md) são mais adequadas para cargas de trabalho de nível de entrada, como desenvolvimento e teste, servidores Web de tráfego baixo, bancos de dados de aplicativos pequenos a médios e prova de conceito.

Somente o [Standard_A2m_v2](../../../virtual-machines/av2-series.md) (2 VCores e 16GBs de memória), [Standard_A4m_v2](../../../virtual-machines/av2-series.md) (4 vCores e 32 GB de memória) e o [Standard_A8m_v2](../../../virtual-machines/av2-series.md) (8 vCores e 64 GB de memória) têm uma taxa de memória para vCore maior de 8 para essas três principais máquinas virtuais. 

Essas máquinas virtuais são boas opções para desenvolvimento e teste menores SQL Server computadores. 

A 8 vCore [Standard_A8m_v2](../../../virtual-machines/av2-series.md) também pode ser uma boa opção para aplicativos pequenos e servidores Web.

> [!NOTE] 
> A série Av2 não dá suporte ao armazenamento Premium e, como tal, não é recomendada para produção SQL Server cargas de trabalho mesmo com as máquinas virtuais que têm uma proporção de memória para vCore de 8.

### <a name="storage-optimized"></a>Otimizado para armazenamento

Os [tamanhos de VM otimizados para armazenamento](../../../virtual-machines/sizes-storage.md) são para casos de uso específicos. Essas máquinas virtuais são projetadas especificamente com taxa de transferência e e/s de disco otimizadas. Esta série de máquinas virtuais destina-se a cenários de Big Data, data warehouse e grandes bancos de dados transacionais. 

#### <a name="lsv2-series"></a>Série Lsv2

A [série Lsv2](../../../virtual-machines/lsv2-series.md) apresenta alta taxa de transferência, baixa latência e armazenamento de NVMe local. As VMs da série Lsv2 são otimizadas para usar o disco local no nó anexado diretamente à VM em vez de usar discos de dados duráveis. 

Essas máquinas virtuais são opções fortes para Big Data, data warehouse, relatórios e cargas de trabalho de ETL. A alta taxa de transferência e o IOPs do armazenamento de NVMe local são um bom caso de uso para o processamento de arquivos que serão carregados no banco de dados e outros cenários em que a fonte pode ser recriada a partir do sistema de origem ou de outros repositórios, como o armazenamento de BLOBs do Azure ou o Azure Data Lake. [Série Lsv2](../../../virtual-machines/lsv2-series.md) As VMs também podem aumentar seu desempenho de disco por até 30 minutos por vez.

Essas máquinas virtuais têm o tamanho de 8 a 80 vCPU com 8 GiB de memória por vCPU e para cada 8 vCPUs há 1,92 TB de SSD de NVMe. Isso significa que, para a maior VM desta série, o [L80s_v2](../../../virtual-machines/lsv2-series.md), há 80 vCPU e 640 chave de memória com 10 vezes 1.92 TB de armazenamento de NVMe.  Há uma taxa consistente de memória para vCore de 8 em todas essas máquinas virtuais.

O armazenamento NVMe é efêmero, o que significa que os dados serão perdidos nesses discos se você reiniciar a máquina virtual.

A série Lsv2 e ls dá suporte ao [armazenamento Premium](../../../virtual-machines/premium-storage-performance.md), mas não ao armazenamento em cache Premium. Não há suporte para a criação de um cache local para aumentar o IOPs. 

> [!WARNING]
> Armazenar seus arquivos de dados no armazenamento de NVMe efêmero pode resultar em perda de dados quando a VM é desalocada. 

### <a name="constrained-vcores"></a>VCores restrita

O alto desempenho SQL Server cargas de trabalho geralmente precisam de quantidades maiores de memória, e/s e taxa de transferência sem as maiores contagens de vCore. 

A maioria das cargas de trabalho OLTP são bancos de dados de aplicativos controlados por um grande número de transações menores. Com cargas de trabalho OLTP, apenas uma pequena quantidade de dados é lida ou modificada, mas os volumes de transações controlados por contagens de usuários são muito maiores. É importante ter a SQL Server memória disponível para os planos de cache, armazenar dados acessados recentemente para desempenho e garantir que as leituras físicas possam ser lidas na memória rapidamente. 

Esses ambientes OLTP precisam de mais quantidades de memória, armazenamento rápido e largura de banda de e/s necessárias para serem executados de forma ideal. 

Para manter esse nível de desempenho sem os maiores custos de licenciamento SQL Server, o Azure oferece tamanhos de VM com [contagens de vCPU restritas](../../../virtual-machines/constrained-vcpu.md). 

Isso ajuda a controlar os custos de licenciamento, reduzindo o vCores disponível mantendo a mesma memória, armazenamento e largura de banda de e/s da máquina virtual pai.

A contagem de vCPU pode ser restrita a uma metade do tamanho original da VM. A redução do vCores disponível para a máquina virtual alcançará taxas de memória para vCore maiores.

Esses novos tamanhos de VM têm um sufixo que especifica o número de vCPUs ativas para facilitar a identificação. 

Por exemplo, o [m64-32ms Standard](../../../virtual-machines/constrained-vcpu.md) requer somente licenciamento 32 SQL Server vCores com a memória, a e/s e a taxa de transferência de [M64ms](../../../virtual-machines/m-series.md) e [m64-16ms](../../../virtual-machines/constrained-vcpu.md) requer licenciamento somente 16 vCores.  Embora o [m64-16ms](../../../virtual-machines/constrained-vcpu.md) tenha um trimestre do custo de licenciamento SQL Server do M64ms, o custo de computação da máquina virtual será o mesmo.

> [!NOTE] 
> - Cargas de trabalho de média a grande data warehouse ainda podem se beneficiar de [VMs vCore restritas](../../../virtual-machines/constrained-vcpu.md), mas as cargas de trabalho de data warehouse geralmente são caracterizadas por menos usuários e processos que abordam quantidades maiores de dados por meio de planos de consulta executados em paralelo. 
> - O custo de computação, que inclui o licenciamento do sistema operacional, permanecerá o mesmo que a máquina virtual pai. 

## <a name="storage-guidance"></a>Orientação de armazenamento

Para obter testes detalhados de desempenho de SQL Server em máquinas virtuais do Azure com benchmarks TPC-E e TPC-C, consulte o blog [otimizar o desempenho do OLTP](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794). 

O cache do Blob do Azure com SSDs Premium é recomendado para todas as cargas de trabalho de produção. 

> [!WARNING]
> Os SSDs e HDDs Standard têm largura de banda e latências variáveis e só são recomendado para cargas de trabalho de desenvolvimento e teste. As cargas de trabalho de produção devem usar os SSDs Premium.

Além disso, recomendamos a criação de sua conta de armazenamento do Azure no mesmo data center que suas máquinas virtuais do SQL Server a fim de reduzir os atrasos de transferência. Ao criar uma conta de armazenamento, desabilite a replicação geográfica, pois não há garantia para uma ordem de gravação consistente em vários discos. Em vez disso, considere a configuração de uma tecnologia de recuperação de desastres do SQL Server entre dois data centers do Azure. Para saber mais, consulte [Alta disponibilidade e recuperação de desastre para o SQL Server em máquinas virtuais do Azure](business-continuity-high-availability-disaster-recovery-hadr-overview.md).

## <a name="disks-guidance"></a>Diretrizes de discos

Há três tipos de disco principais em máquinas virtuais do Azure:

* **Disco do sistema operacional**: quando você cria uma máquina virtual do Azure, a plataforma anexará pelo menos um disco (rotulado como a unidade **C** ) à VM para o disco do sistema operacional. Cada disco é um VHD armazenado como um blob de páginas no armazenamento.
* **Disco temporário**: Máquinas virtuais do Azure contêm outro disco denominado disco temporário (rotulado como a unidade **D**:). É um disco localizado no nó que pode ser usado como espaço de rascunho.
* **Discos de dados**: Você também pode anexar outros discos à sua máquina virtual como discos de dados, e eles serão armazenados no armazenamento como blobs de página.

As seções a seguir descrevem as recomendações para usar esses diferentes discos.

### <a name="operating-system-disk"></a>Disco do sistema operacional

Um disco do sistema operacional é um VHD que você pode inicializar e montar como uma versão em execução de um sistema operacional e rotulado como a unidade **C** .

A política padrão de caching no disco do sistema operacional é de **Leitura/Gravação**. Para aplicativos sensíveis ao desempenho, recomendamos o uso de discos de dados em vez do disco do sistema operacional. Consulte a seção sobre Discos de Dados abaixo.

### <a name="temporary-disk"></a>Disco temporário

A unidade de armazenamento temporário, rotulada como a unidade **D** , não é persistida no armazenamento de BLOBs do Azure. Não armazene seus arquivos de banco de dados do usuário ou arquivos de log de transações do usuário na unidade **D**:.

– Coloque o TempDB na unidade `D:\` SSD local para cargas de trabalho do SQL Server críticas (depois de escolher o tamanho correto de VM). Se você criar a VM no portal do Azure ou nos modelos de início rápido do Azure e inserir o banco de informações [temporário no disco local](https://techcommunity.microsoft.com/t5/SQL-Server/Announcing-Performance-Optimized-Storage-Configuration-for-SQL/ba-p/891583), você não precisará de nenhuma ação adicional; para todos os outros casos, siga as etapas no blog para  [usar o SSDs para armazenar o tempdb](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-TempDB-and-buffer-pool-extensions/) para evitar falhas após a reinicialização. Se a capacidade da unidade local não for suficiente para o tamanho do TempDB, coloque o TempDB em um pool de armazenamento [distribuído](../../../virtual-machines/premium-storage-performance.md) em discos SSD Premium com [cache somente leitura](../../../virtual-machines/premium-storage-performance.md#disk-caching).

Para VMs que dão suporte a SSDs Premium, você também pode armazenar o TempDB em um disco compatível com SSDs Premium com o cache de leitura habilitado.


### <a name="data-disks"></a>Discos de dados

* **Usar discos SSD Premium para dados e arquivos de log**: Se você não estiver usando divisão de disco, use dois discos SSD Premium em que um disco contém os arquivos de log e o outro contém os dados. Cada SSD Premium, dependendo do próprio tamanho, fornece um número de IOPS e largura de banda (MB/s), conforme descrito no artigo, [Selecionar um tipo de disco](../../../virtual-machines/disks-types.md). Se você estiver usando uma técnica de distribuição de disco, como Espaços de Armazenamento, obterá o desempenho ideal tendo dois pools, um para os arquivos de log e outro para os arquivos de dados. No entanto, se você planeja usar FCIs (instâncias de cluster de failover) do SQL Server, precisa configurar um pool ou usar [compartilhamentos de arquivo Premium](failover-cluster-instance-premium-file-share-manually-configure.md).

   > [!TIP]
   > - Para obter resultados de teste sobre várias configurações de disco e carga de trabalho, consulte a seguinte postagem de blog: [diretrizes de configuração de armazenamento para SQL Server em máquinas virtuais do Azure](/archive/blogs/sqlserverstorageengine/storage-configuration-guidelines-for-sql-server-on-azure-vm).
   > - Para desempenho de missão crítica de SQL Servers que precisem de aproximadamente 50.000 IOPS, considere substituir 10 discos -P30 por um SSD Ultra. Para obter mais informações, consulte a postagem do blog abaixo: [Mission critical performance with Ultra SSD](https://azure.microsoft.com/blog/mission-critical-performance-with-ultra-ssd-for-sql-server-on-azure-vm/) (Desempenho crítico com SSD Ultra).

   > [!NOTE]
   > Quando você provisiona uma VM do SQL Server no portal, tem a opção de editar sua configuração de armazenamento. Dependendo de sua configuração, o Azure configura um ou mais discos. Vários discos são combinados em um único pool de armazenamento com distribuição. Tanto os dados quanto os arquivos de log residem juntos nessa configuração. Para saber mais, confira [Configuração de armazenamento para VMs do SQL Server](storage-configuration.md).

* **Distribuição de disco**: para obter mais taxa de transferência, você pode adicionar mais discos de dados e usar a distribuição de disco. Para determinar o número de discos de dados, você precisa analisar o número de IOPS e a largura de banda necessários para os arquivos de log e para os arquivos de dados e do TempDB. Observe que diferentes tamanhos de VM têm diferentes limites no número de IOPS e na largura de banda com suporte; consulte as tabelas sobre IOPS por [tamanho de VM](../../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Use as seguintes diretrizes:

  * Para Windows 8/Windows Server 2012 ou posterior, use os [Espaços de Armazenamento](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831739(v=ws.11)) com as seguintes diretrizes:

      1. Defina a intercalação (tamanho de faixa) como 64 KB (65.536 bytes) para cargas de trabalho OLTP e 256 KB (262.144 bytes) para cargas de trabalho de data warehouse, a fim de evitar o impacto no desempenho devido ao desalinhamento da partição. Isso deve ser definido com o PowerShell.
      2. Defina a contagem de colunas = número de discos físicos. Use o PowerShell ao configurar mais de 8 discos (não interface do usuário do Gerenciador do Servidor). 

    Por exemplo, o PowerShell a seguir cria um novo pool de armazenamento com o tamanho de intercalação para 64 KB e o número de colunas igual à quantidade de disco físico no pool de armazenamento:

    ```powershell
    $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}
    
    New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" `
        -PhysicalDisks $PhysicalDisks | New- VirtualDisk -FriendlyName "DataFiles" `
        -Interleave 65536 -NumberOfColumns $PhysicalDisks .Count -ResiliencySettingName simple `
        –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter `
        -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" `
        -AllocationUnitSize 65536 -Confirm:$false 
    ```

  * Para o Windows 2008 R2 ou anterior, é possível usar discos dinâmicos (volumes de SO distribuídos) e o tamanho da faixa será sempre 64 KB. Essa opção tornou-se preterida no Windows 8/Windows Server 2012. Para obter informações, veja a declaração de suporte em [O Serviço de Disco Virtual está em transição para a API de Gerenciamento de Armazenamento do Windows](/windows/win32/w8cookbook/vds-is-transitioning-to-wmiv2-based-windows-storage-management-api).

  * Se você estiver usando [Espaços de Armazenamento Diretos (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) com [Instâncias de Cluster de Failover do SQL Server](failover-cluster-instance-storage-spaces-direct-manually-configure.md), será necessário configurar um único pool. Embora diferentes volumes possam ser criados nesse único pool, todos eles compartilharão as mesmas características, como a mesma política de cache.

  * Determine o número de discos associados ao seu pool de armazenamento com base nas suas expectativas de carga. Tenha em mente que tamanhos de VM diferentes permitem quantidades diferentes de discos de dados anexados. Para obter mais informações, consulte [tamanhos das máquinas virtuais](../../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

  * Se você não estiver usando o SSDs Premium (cenários de desenvolvimento/teste), a recomendação é adicionar o número máximo de discos de dados com suporte pelo [tamanho da VM](../../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) e usar a distribuição de disco.

* **Política de cache**: Observe as recomendações a seguir para a política de cache, dependendo da configuração de armazenamento.

  * Se você estiver usando discos separados para dados e arquivos de log, habilite o cache de leitura nos discos de dados que hospedam os arquivos de dados e arquivos de dados do TempDB. Isso pode resultar em um benefício de desempenho significativo. Não habilite o cache no disco que contém o arquivo de log, pois isso causa uma pequena diminuição no desempenho.

  * Se você estiver usando a distribuição de disco em um único pool de armazenamento, a maioria das cargas de trabalho se beneficiará do cache de leitura. Se você tiver pools de armazenamento separados para os arquivos de log e de dados, habilite o cache de leitura apenas no pool de armazenamento para os arquivos de dados. Em determinadas cargas de trabalho de gravação pesada, um melhor desempenho pode ser obtido sem o cache. Isso somente pode ser determinado por meio de testes.

  * As recomendações anteriores aplicam-se aos SSDs Premium. Se você não estiver usando os SSDs Premium, não habilite o caching em discos de dados.

  * Para obter instruções sobre como configurar o cache de disco, consulte os artigos a seguir. Para o modelo de implantação clássico (ASM), confira: [Set-AzureOSDisk](/previous-versions/azure/jj152847(v=azure.100)) e [Set-AzureDataDisk](/previous-versions/azure/jj152851(v=azure.100)). Para o modelo de implantação do Azure Resource Manager, confira: [Set-AzOSDisk](/powershell/module/az.compute/set-azvmosdisk) e [Set-AzVMDataDisk](/powershell/module/az.compute/set-azvmdatadisk).

     > [!WARNING]
     > Pare o serviço de SQL Server ao alterar a configuração de cache de discos de máquinas virtuais do Azure para evitar a possibilidade de qualquer corrupção de banco de dados.

* **Tamanho da unidade de alocação de NTFS**: Ao formatar o disco de dados, recomendamos o uso de um tamanho de unidade de alocação de 64 KB para arquivos de dados e de log e também para o TempDB. Se o TempDB for colocado no disco temporário (D:\ ) o desempenho obtido aproveitando essa unidade supera a necessidade de um tamanho de unidade de alocação de 64 KB. 

* **Práticas recomendadas de gerenciamento de disco**: Ao remover um disco de dados ou alterar seu tipo de cache, interrompa o serviço SQL Server durante a alteração. Quando as configurações do cache são alteradas no disco do SO, o Azure interrompe a VM, altera o tipo de cache e reinicia a VM. Quando as configurações do cache de um disco de dados são alteradas, a VM não é interrompida, mas o disco de dados é desanexado da VM durante a mudança e depois reanexado.

  > [!WARNING]
  > A não interrupção do serviço SQL Server durante essas operações pode causar corrupção no banco de dados.


## <a name="io-guidance"></a>Diretrizes de E/S

* Os melhores resultados com os SSDs Premium são obtidos ao paralelizar seu aplicativo e as solicitações. Os SSDs Premium foram projetados para cenários nos quais a profundidade da fila de E/S é maior do que um. Portanto, você verá pouco ou nenhum ganho de desempenho para solicitações em série de segmento único (mesmo se usarem muito armazenamento). Por exemplo, isso pode afetar os resultados do teste de segmento único de ferramentas de análise de desempenho, por exemplo, o SQLIO.

* Considere o uso da [compactação de página do banco de dados](/sql/relational-databases/data-compression/data-compression) , pois isso pode ajudar a melhorar o desempenho de cargas de trabalho com uso intenso de E/S. No entanto, a compactação de dados pode aumentar o consumo de CPU no servidor de banco de dados.

* Considere a habilitação da inicialização instantânea de arquivo a fim de reduzir o tempo necessário para alocação inicial do arquivo. Para aproveitar a inicialização instantânea de arquivo, conceda a SE_MANAGE_VOLUME_NAME à conta de serviço do SQL Server (MSSQLSERVER) e adicione-a à política de segurança **Executar Tarefas de Manutenção de Volume**. Se você estiver usando uma imagem da plataforma do SQL Server para o Azure, a conta de serviço padrão (NT Service\MSSQLSERVER) não será adicionada à política de segurança **Executar Tarefas de Manutenção de volume**. Em outras palavras, a inicialização instantânea de arquivo não está habilitada em uma imagem de plataforma do SQL Server do Azure. Depois de adicionar a conta de serviço do SQL Server à política de segurança **Executar Tarefas de Manutenção de Volume** , reinicie o serviço do SQL Server. Talvez existam considerações de segurança sobre a utilização desse recurso. Para obter mais informações, consulte [Inicialização de arquivos de bancos de dados](/sql/relational-databases/databases/database-instant-file-initialization).

* Lembre-se de que o **crescimento automático** é considerado meramente uma contingência para um crescimento inesperado. Não gerencie diariamente o crescimento de seus dados e do log com o crescimento automático. Se o crescimento automático for usado, aumente previamente o arquivo usando a opção Tamanho.

* Verifique se a **redução automática** está desabilitada a fim de evitar uma sobrecarga desnecessária que pode afetar negativamente o desempenho.

* Mova todos os bancos de dados para discos de dados, incluindo bancos de dados do sistema. Para obter mais informações, veja [Mover bancos de dados do sistema](/sql/relational-databases/databases/move-system-databases).

* Mova o log de erros do SQL Server e os diretórios de arquivos de rastreamento para discos de dados Isso pode ser feito no SQL Server Configuration Manager clicando duas vezes na Instância do SQL Server e selecionando Propriedades. As configurações do arquivo de rastreamento e do log de erros podem ser alteradas na guia **Parâmetros de Inicialização**. O Diretório de Despejo é especificado na guia **Avançado**. A captura de tela a seguir mostra onde procurar o parâmetro de inicialização do log de erro.

    ![Captura de Tela de Log de Erros do SQL](./media/performance-guidelines-best-practices/sql_server_error_log_location.png)

* Configure os locais de arquivo de banco de dados e backup padrão. Use as recomendações neste artigo e faça as alterações na janela de propriedades do Servidor. Para obter instruções, confira [Exibir ou alterar os locais padrão de arquivos de log e de dados (SQL Server Management Studio)](/sql/database-engine/configure-windows/view-or-change-the-default-locations-for-data-and-log-files). A captura de tela a seguir demonstra onde fazer essas alterações.

    ![Log de Dados de SQL e Arquivos de Backup](./media/performance-guidelines-best-practices/sql_server_default_data_log_backup_locations.png)
* Estabeleça páginas bloqueadas a fim de reduzir a ES e quaisquer atividades de paginação. Para saber mais, confira [Habilitar a opção Bloquear Páginas na Memória (Windows)](/sql/database-engine/configure-windows/enable-the-lock-pages-in-memory-option-windows).

* Se você estiver executando o SQL Server 2012, instale a Atualização Cumulativa 10 do Service Pack 1. Essa atualização contém a correção para o desempenho ruim de E/S ao executar a instrução seleção na tabela temporária no SQL Server 2012. Para obter informações, consulte este [artigo da base de dados de conhecimento](https://support.microsoft.com/kb/2958012).

* Considere a compactação de qualquer arquivo de dados durante a transferência de entrada/saída do Azure.

## <a name="feature-specific-guidance"></a>Diretriz específica do recurso

Algumas implantações podem obter outros benefícios de desempenho usando técnicas mais avançadas de configuração. A lista a seguir destaca alguns recursos do SQL Server que podem ajudá-lo a obter um desempenho maior:

### <a name="back-up-to-azure-storage"></a>Fazer backup no Armazenamento do Azure
Ao executar backups para SQL Server em execução em máquinas virtuais do Azure, você pode usar [SQL Server Backup para URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url). Esse recurso foi disponibilizado a partir do SQL Server 2012 SP1 CU2 e é recomendado para fazer o backup em discos de dados anexados. Quando você faz backup/restauração de/para o armazenamento do Azure, siga as recomendações fornecidas em [SQL Server Backup em URL práticas recomendadas e solução de problemas e restauração de backups armazenados no armazenamento do Azure](/sql/relational-databases/backup-restore/sql-server-backup-to-url-best-practices-and-troubleshooting). Você também pode automatizar esses backups usando o [backup automatizado para SQL Server em máquinas virtuais do Azure](../../../azure-sql/virtual-machines/windows/automated-backup-sql-2014.md).

Antes do SQL Server 2012, use a [Ferramenta de backup do SQL Server para o Azure](https://www.microsoft.com/download/details.aspx?id=40740). Essa ferramenta pode ajudar a aumentar a taxa de transferência do backup utilizando vários alvos de distribuição de backup.

### <a name="sql-server-data-files-in-azure"></a>Arquivos de dados do SQL Server no Azure

Este recurso novo, [Arquivos de dados do SQL Server no Azure](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure), está disponível desde o SQL Server 2014. A execução do SQL Server com os arquivos de dados no Azure demonstra características de desempenho comparáveis as dos discos de dados do Azure.

### <a name="failover-cluster-instance-and-storage-spaces"></a>Espaços de Armazenamento de instância de cluster de failover

Se você estiver usando espaços de armazenamento, ao adicionar nós ao cluster na página de **confirmação** , desmarque a caixa de seleção rotulada **Adicionar todo o armazenamento elegível ao cluster**. 

![Desmarcar o armazenamento qualificado](./media/performance-guidelines-best-practices/uncheck-eligible-cluster-storage.png)

Se você estiver usando Espaços de Armazenamento e não desmarcar **Adicionar todo o armazenamento qualificado ao cluster**, o Windows desconectará os discos virtuais durante o processo de clustering. Como resultado, eles não aparecem no Gerenciador ou Explorador de Discos até que os espaços de armazenamento sejam removidos do cluster e reanexados usando o PowerShell. Espaços de Armazenamento agrupam vários discos em pools de armazenamento. Para obter mais informações, consulte [Espaços de Armazenamento](/windows-server/storage/storage-spaces/overview).

## <a name="multiple-instances"></a>Várias instâncias 

Considere as seguintes práticas recomendadas ao implantar várias instâncias de SQL Server em uma única máquina virtual: 

- Defina a memória máxima do servidor para cada instância de SQL Server, garantindo que haja memória restante para o sistema operacional. Certifique-se de atualizar as restrições de memória para as instâncias de SQL Server se você alterar a quantidade de memória alocada para a máquina virtual. 
- Ter LUNs separados para dados, logs e TempDB, pois todos têm padrões de carga de trabalho diferentes e você não deseja que eles afetem uns aos outros. 
- Teste exaustivamente seu ambiente em cargas de trabalho pesadas de produção para garantir que ele possa lidar com a capacidade de carga de trabalho de pico nos SLAs de seu aplicativo. 

Os sinais de sistemas sobrecarregados podem incluir, mas não se limitam a, esgotamento de thread de trabalho, tempos de resposta lentos e/ou memória do sistema do Dispatcher paralisado. 



## <a name="collect-performance-baseline"></a>Coletar linha de base de desempenho

Para obter uma abordagem mais prescritiva, reúna contadores de desempenho usando PerfMon/LogMan e Capture SQL Server estatísticas de espera para entender melhor as pressões gerais e os gargalos potenciais do ambiente de origem. 

Comece coletando a CPU, a memória, o [IOPS](../../../virtual-machines/premium-storage-performance.md#iops), a [taxa de transferência](../../../virtual-machines/premium-storage-performance.md#throughput)e a [latência](../../../virtual-machines/premium-storage-performance.md#latency) da carga de trabalho de origem em horários de pico após a lista de verificação de [desempenho do aplicativo](../../../virtual-machines/premium-storage-performance.md#application-performance-requirements-checklist). 

Reúna dados durante horários de pico, como cargas de trabalho durante o dia útil normal, mas também outros processos de carga alta, como processamento de fim de dia e cargas de trabalho ETL de fim de semana. Considere a possibilidade de escalar verticalmente seus recursos para cargas de trabalho pesadas do atypically, como o processamento do fim do trimestre, e depois o dimensionamento feito após a conclusão da carga de trabalho. 

Use a análise de desempenho para selecionar o [tamanho da VM](../../../virtual-machines/sizes-memory.md) que pode ser dimensionado para os requisitos de desempenho da carga de trabalho.


### <a name="iops-and-throughput"></a>IOPS e taxa de transferência

SQL Server desempenho depende muito do subsistema de e/s. A menos que seu banco de dados caiba na memória física, SQL Server constantemente leva as páginas do banco de dados para dentro e para fora do pool de buffers. Os arquivos de dados para SQL Server devem ser tratados de forma diferente. O acesso a arquivos de log é seqüencial, exceto quando uma transação precisa ser revertida onde os arquivos de dados, incluindo TempDB, são acessados aleatoriamente. Se você tiver um subsistema de e/s lento, os usuários poderão enfrentar problemas de desempenho, como tempos de resposta lentos e tarefas que não são concluídas devido a tempos limite. 

As máquinas virtuais do Azure Marketplace têm arquivos de log em um disco físico separado dos arquivos de dados por padrão. A contagem e o tamanho dos arquivos de dados do TempDB atendem às práticas recomendadas e são direcionadas para a D:/ Dirigir.. 

Os contadores PerfMon a seguir podem ajudar a validar a taxa de transferência de e/s exigida por seu SQL Server: 
* **Leituras de \LogicalDisk\Disk/s** (IOPS de leitura e gravação)
* **Gravações de \LogicalDisk\Disk/s** (IOPS de leitura e gravação) 
* **\LogicalDisk\Disk bytes/s** (requisitos de taxa de transferência para os arquivos de dados, log e tempdb)

Usando requisitos de IOPS e taxa de transferência em níveis de pico, avalie os tamanhos de VM que correspondem à capacidade de suas medições. 

Se sua carga de trabalho exigir 20 K de IOPS de leitura e IOPS de gravação de 10K, você poderá escolher E16s_v3 (com até 20 k de 32 K armazenados em cache e 25600 IOPS não armazenado em cache) ou M16_s (com até vinte mil em cache e 10K IOPS não armazenados em cache) com 2 discos p30 distribuídos usando espaços de armazenamento. 

Certifique-se de entender os requisitos de taxa de transferência e IOPS da carga de trabalho, pois as VMs têm limites de escala diferentes para IOPS e taxa de transferência.

### <a name="memory"></a>Memória

Acompanhe a memória externa usada pelo sistema operacional, bem como a memória usada internamente pelo SQL Server. A identificação da pressão para qualquer componente ajudará a dimensionar as máquinas virtuais e a identificar as oportunidades de ajuste. 

Os contadores PerfMon a seguir podem ajudar a validar a integridade da memória de uma máquina virtual SQL Server: 
* [\Memória\MBytes disponíveis](/azure/monitoring/infrastructure-health/vmhealth-windows/winserver-memory-availmbytes)
* [\SQLServer: memória do servidor Manager\Target de memória (KB)](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer: memória do servidor Manager\Total de memória (KB)](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer: gravações de Manager\Lazy de buffer/s](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer: expectativa de vida Buffer\leituras do buffer](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)

### <a name="compute--processing"></a>Computação/processamento

A computação no Azure é gerenciada de maneira diferente do local. Os servidores locais são criados nos últimos anos, sem uma atualização devido à sobrecarga de gerenciamento e ao custo de aquisição de novo hardware. A virtualização atenua alguns desses problemas, mas os aplicativos são otimizados para aproveitar ao máximo o hardware subjacente, o que significa que qualquer alteração significativa no consumo de recursos requer o rebalanceamento de todo o ambiente físico. 

Isso não é um desafio no Azure, em que uma nova máquina virtual em uma série diferente de hardware, e até mesmo em uma região diferente, é fácil de alcançar. 

No Azure, você deseja aproveitar o máximo possível dos recursos de máquinas virtuais, portanto, as máquinas virtuais do Azure devem ser configuradas para manter a média de CPU máxima, sem afetar a carga de trabalho. 

Os contadores PerfMon a seguir podem ajudar a validar a integridade de computação de uma máquina virtual SQL Server:
* **Tempo de processador de informações de \Processor (_Total) \%**
* **\Process (sqlservr) \% tempo do processador**

> [!NOTE] 
> Idealmente, tente visar o uso de 80% de sua computação, com picos acima de 90%, mas não atingindo 100% por um período de tempo prolongado. Fundamentalmente, você só deseja provisionar a computação de que o aplicativo precisa e, em seguida, planejar para escalar ou reduzir verticalmente conforme a necessidade dos negócios. 

## <a name="next-steps"></a>Próximas etapas

Para obter as práticas recomendadas de segurança, consulte [considerações de segurança para SQL Server em máquinas virtuais do Azure](security-considerations-best-practices.md).

Revise outros artigos sobre Máquinas Virtuais do SQL Server em [Visão geral do SQL Server nas Máquinas Virtuais do Azure](sql-server-on-azure-vm-iaas-what-is-overview.md). Em caso de dúvidas sobre máquinas virtuais do SQL Server, consulte as [Perguntas frequentes](frequently-asked-questions-faq.md).