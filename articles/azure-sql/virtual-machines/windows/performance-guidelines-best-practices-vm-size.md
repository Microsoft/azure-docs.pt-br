---
title: 'Tamanho da VM: práticas recomendadas de desempenho & diretrizes'
description: Fornece diretrizes de tamanho de VM e práticas recomendadas para otimizar o desempenho do seu SQL Server na VM (máquina virtual) do Azure.
services: virtual-machines-windows
documentationcenter: na
author: dplessMSFT
editor: ''
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/25/2021
ms.author: dpless
ms.reviewer: jroth
ms.openlocfilehash: 9427ae1b9bd68f63df40d24122cc13b5460fbc27
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105572323"
---
# <a name="vm-size-performance-best-practices-for-sql-server-on-azure-vms"></a>Tamanho da VM: práticas recomendadas de desempenho para SQL Server em VMs do Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este artigo fornece diretrizes de tamanho de VM de uma série de práticas recomendadas e diretrizes para otimizar o desempenho para seu SQL Server em VMs (máquinas virtuais) do Azure.

Normalmente, há uma compensação entre a otimização para custos e a otimização para desempenho. Essa série de práticas recomendadas de desempenho se concentra em obter o *melhor* desempenho para SQL Server em máquinas virtuais do Azure. Se sua carga de trabalho for menos exigente, talvez você não precise de todas as otimizações recomendadas. Considere suas necessidades de desempenho, custos e padrões de carga de trabalho ao avaliar essas recomendações.


## <a name="checklist"></a>Lista de verificação

Examine a seguinte lista de verificação para obter uma breve visão geral das práticas recomendadas de tamanho de VM que o restante do artigo aborda em mais detalhes: 

- Use tamanhos de VM com 4 ou mais vCPU, como o [Standard_M8-4ms](/../../virtual-machines/m-series), o [E4ds_v4](../../../virtual-machines/edv4-edsv4-series.md#edv4-series)ou o [DS12_v2](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) ou superior. 
- Use tamanhos de máquina virtual com [otimização de memória](../../../virtual-machines/sizes-memory.md) para obter o melhor desempenho de cargas de trabalho de SQL Server. 
- A série [DSv2 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md), [Edsv4](../../../virtual-machines/edv4-edsv4-series.md) , a [M-](../../../virtual-machines/m-series.md)e a série [Mv2](../../../virtual-machines/mv2-series.md) oferecem a proporção ideal de memória para vCore necessária para cargas de trabalho OLTP. Ambas as VMs da série M oferecem a maior taxa de memória para vCore necessária para cargas de trabalho críticas e também são ideais para cargas de trabalho de data warehouse. 
- Considere uma taxa maior de memória para vCore para cargas de trabalho de missão crítica e data warehouse. 
- Aproveite as imagens do Marketplace de máquina virtual do Azure, pois as configurações de SQL Server e as opções de armazenamento estão configuradas para um desempenho de SQL Server ideal. 
- Colete as características de desempenho da carga de trabalho de destino e use-as para determinar o tamanho apropriado da VM para sua empresa.

Para comparar a lista de verificação de tamanho da VM com as outras, consulte a [lista de verificação de práticas recomendadas de desempenho](performance-guidelines-best-practices-checklist.md)abrangente. 

## <a name="overview"></a>Visão geral

Ao criar um SQL Server na VM do Azure, considere cuidadosamente o tipo de carga de trabalho necessário. Se você estiver migrando um ambiente existente, [colete uma linha de base de desempenho](performance-guidelines-best-practices-collect-baseline.md) para determinar sua SQL Server nos requisitos de VM do Azure. Se essa for uma nova VM, crie sua nova VM SQL Server com base em seus requisitos de fornecedor. 

Se você estiver criando uma nova VM SQL Server com um novo aplicativo criado para a nuvem, poderá dimensionar facilmente sua VM SQL Server conforme seus requisitos de dados e de uso evoluem.
Inicie os ambientes de desenvolvimento com as séries D-Series, B ou Av2 de nível inferior e aumente seu ambiente ao longo do tempo. 

Use o SQL Server imagens do Marketplace de VM com a configuração de armazenamento no Portal. Isso facilitará a criação apropriada dos pools de armazenamento necessários para obter o tamanho, o IOPS e a taxa de transferência necessários para suas cargas de trabalho. É importante escolher SQL Server VMs que dão suporte ao armazenamento Premium e ao cache de armazenamento Premium. Consulte o artigo de [armazenamento](performance-guidelines-best-practices-storage.md) para saber mais. 

O mínimo recomendado para um ambiente OLTP de produção é 4 vCore, 32 GB de memória e uma proporção de memória para vCore de 8. Para novos ambientes, comece com 4 máquinas vCores e dimensione para 8, 16, 32 vCores ou mais quando seus requisitos de dados e computação forem alterados. Para a taxa de transferência do OLTP, direcione as VMs de SQL Server que têm 5000 IOPS para cada vCore. 

SQL Server data warehouse e ambientes de missão crítica geralmente precisarão ser dimensionados além da proporção de 8 memórias para vCore. Para ambientes médios, convém escolher uma proporção de 16 memória para vCore e uma proporção de memória para vCore de 32 para ambientes maiores de data warehouse. 

SQL Server ambientes de data warehouse geralmente se beneficiam do processamento paralelo de máquinas maiores. Por esse motivo, a série M e a série Mv2 são opções fortes para ambientes maiores de data warehouse.

Use o vCPU e a configuração de memória do seu computador de origem como uma linha de base para migrar um banco de dados SQL Server local atual para SQL Server em VMs do Azure. Traga sua principal licença do Azure para aproveitar o [benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) e economize em SQL Server custos de licenciamento.

## <a name="memory-optimized"></a>Otimizado para memória

Os [tamanhos de máquina virtual com otimização de memória](../../../virtual-machines/sizes-memory.md) são um destino principal para SQL Server VMs e a opção recomendada pela Microsoft. As máquinas virtuais com otimização de memória oferecem taxas mais fortes de memória para CPU e opções de cache de médio a grande porte. 

### <a name="m-mv2-and-mdsv2-series"></a>Série M, Mv2 e Mdsv2

A [série M](../../../virtual-machines/m-series.md) oferece contagens de vCore e memória para algumas das maiores cargas de trabalho de SQL Server.  

A [série Mv2](../../../virtual-machines/mv2-series.md) tem as maiores contagens de vCore e memória e é recomendada para cargas de trabalho de missão crítica e data warehouse. As instâncias da série Mv2 são tamanhos de VM com otimização de memória que fornecem desempenho computacional inigualável para dar suporte a grandes bancos de dados na memória e cargas de trabalho com uma alta taxa de memória para CPU que é perfeita para servidores de banco de dados relacionais, caches grandes e análise na memória.

O [Standard_M64ms](../../../virtual-machines/m-series.md) tem uma proporção de memória para vCore de 28, por exemplo.

A [série de memória média Mdsv2](../../..//virtual-machines/msv2-mdsv2-series.md) é uma nova série M que está atualmente em versão [prévia](https://aka.ms/Mv2MedMemoryPreview) que oferece uma variedade de máquinas virtuais do Azure de nível série m com uma oferta de memória midtier. Essas máquinas são adequadas para cargas de trabalho de SQL Server com um mínimo de 10 suporte de memória para vCore de até 30.

Alguns dos recursos da série M e Mv2 atraentes para o desempenho de SQL Server incluem [armazenamento Premium](../../../virtual-machines/premium-storage-performance.md) e suporte a [cache de armazenamento Premium](../../../virtual-machines/premium-storage-performance.md#disk-caching) , suporte [a ultra discos](../../../virtual-machines/disks-enable-ultra-ssd.md) e [aceleração de gravação](../../../virtual-machines/how-to-enable-write-accelerator.md).

### <a name="edsv4-series"></a>Série Edsv4

A [série Edsv4](../../../virtual-machines/edv4-edsv4-series.md) é projetada para aplicativos com uso intensivo de memória. Essas VMs têm uma grande capacidade de SSD de armazenamento local, IOPS de disco local forte, até 504 GiB de RAM. Há 8 GiB de memória quase consistentes por vCore na maioria dessas máquinas virtuais, o que é ideal para cargas de trabalho de SQL Server padrão. 

Há uma nova máquina virtual neste grupo com o [Standard_E80ids_v4](../../../virtual-machines/edv4-edsv4-series.md) que oferece 80 vCores, 504 GB de memória, com uma proporção de memória para vCore de 6. Essa máquina virtual é notável porque é [isolada](../../../virtual-machines/isolation.md) , o que significa que é garantido que seja a única máquina virtual em execução no host e, portanto, seja isolada de outras cargas de trabalho do cliente. Isso tem uma proporção de memória para vCore menor do que o recomendado para SQL Server, portanto, ele só deve ser usado se o isolamento for necessário.

As máquinas virtuais da série Edsv4 dão suporte ao [armazenamento Premium](../../../virtual-machines/premium-storage-performance.md)e ao [cache de armazenamento Premium](../../../virtual-machines/premium-storage-performance.md#disk-caching).

### <a name="dsv2-series-11-15"></a>Série DSv2 11-15

A [série DSv2 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) tem as mesmas configurações de memória e disco que a série D anterior. Esta série tem uma proporção consistente de memória para CPU de 7 em todas as máquinas virtuais. Este é o menor da série com otimização de memória e é uma boa opção de baixo custo para cargas de trabalho de SQL Server de nível de entrada.

A [série DSv2 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) dá suporte ao [armazenamento Premium](../../../virtual-machines/premium-storage-performance.md) e ao [cache de armazenamento Premium](../../../virtual-machines/premium-storage-performance.md#disk-caching), que é altamente recomendável para um desempenho ideal.

## <a name="general-purpose"></a>Propósito geral

Os [tamanhos de máquina virtual de uso geral](../../../virtual-machines/sizes-general.md) são projetados para fornecer taxas balanceadas de memória para vCore para cargas de trabalho de nível de entrada menores, como desenvolvimento e teste, servidores Web e servidores de banco de dados menores. 

Por causa das taxas menores de memória para vCore com as máquinas virtuais de uso geral, é importante monitorar cuidadosamente os contadores de desempenho baseados em memória para garantir que SQL Server seja capaz de obter a memória cache de buffer de que precisa. Consulte [linha de base de desempenho de memória](performance-guidelines-best-practices-collect-baseline.md#memory) para obter mais informações. 

Como a recomendação inicial para cargas de trabalho de produção é uma proporção de memória para vCore de 8, a configuração mínima recomendada para uma VM de uso geral em execução SQL Server é 4 vCPU e 32 GB de memória. 

### <a name="ddsv4-series"></a>Série Ddsv4

A [série Ddsv4](../../../virtual-machines/ddv4-ddsv4-series.md) oferece uma combinação justa de vCPU, memória e disco temporário, mas com suporte menor de memória para vCore. 

As VMs Ddsv4 incluem menor latência e armazenamento local de velocidade mais alta.

Esses computadores são ideais para implantações de aplicativo e SQL lado a lado que exigem acesso rápido aos bancos de dados de armazenamento temporário e relacional departamental. Há uma proporção de memória para vCore padrão de 4 em todas as máquinas virtuais nesta série. 

Por esse motivo, é recomendável aproveitar o D8ds_v4 como a máquina virtual inicial nesta série, que tem 8 vCores e 32 GBs de memória. O maior computador é o D64ds_v4, que tem 64 vCores e 256 GB de memória.

As máquinas virtuais da [série Ddsv4](../../../virtual-machines/ddv4-ddsv4-series.md) dão suporte ao [armazenamento Premium](../../../virtual-machines/premium-storage-performance.md) e ao [cache de armazenamento Premium](../../../virtual-machines/premium-storage-performance.md#disk-caching).

> [!NOTE]
> A [série Ddsv4](../../../virtual-machines/ddv4-ddsv4-series.md) não tem a proporção de memória para vCore de 8 que é recomendada para cargas de trabalho de SQL Server. Como tal, considerando o uso dessas máquinas virtuais apenas para cargas de trabalho de desenvolvimento e aplicativos menores.

### <a name="b-series"></a>Série B

Os tamanhos de máquina virtual da [série B expansível](../../../virtual-machines/sizes-b-series-burstable.md) são ideais para cargas de trabalho que não precisam de desempenho consistente, como prova de conceito e servidores de aplicativos e desenvolvimento muito pequenos. 

A maioria dos tamanhos de máquina virtual da [série B expansível](../../../virtual-machines/sizes-b-series-burstable.md) tem uma proporção de memória para vCore de 4. A maior dessas máquinas é a [Standard_B20ms](../../../virtual-machines/sizes-b-series-burstable.md) com 20 vCores e 80 GB de memória.

Esta série é exclusiva, pois os aplicativos têm a capacidade **de aumentar durante o** horário comercial com créditos intermitentes variados com base no tamanho da máquina. 

Quando os créditos são esgotados, a VM retorna ao desempenho do computador de linha de base.

O benefício da série B é a economia de computação que pode ser obtida em comparação com os outros tamanhos de VM em outras séries, especialmente se você precisar da capacidade de processamento de maneira moderada ao longo do dia.

Esta série dá suporte ao [armazenamento Premium](../../../virtual-machines/premium-storage-performance.md), mas não **dá suporte ao** [cache de armazenamento Premium](../../../virtual-machines/premium-storage-performance.md#disk-caching).

> [!NOTE] 
> A [série B expansível](../../../virtual-machines/sizes-b-series-burstable.md) não tem a proporção de memória para vCore de 8 que é recomendada para cargas de trabalho de SQL Server. Como tal, considere o uso dessas máquinas virtuais para aplicativos menores, servidores Web e cargas de trabalho de desenvolvimento.

### <a name="av2-series"></a>Série Av2

As VMs da [série Av2](../../../virtual-machines/av2-series.md) são mais adequadas para cargas de trabalho de nível de entrada, como desenvolvimento e teste, servidores Web de tráfego baixo, bancos de dados de aplicativos pequenos a médios e prova de conceito.

Somente o [Standard_A2m_v2](../../../virtual-machines/av2-series.md) (2 VCores e 16GBs de memória), [Standard_A4m_v2](../../../virtual-machines/av2-series.md) (4 vCores e 32 GB de memória) e o [Standard_A8m_v2](../../../virtual-machines/av2-series.md) (8 vCores e 64 GB de memória) têm uma taxa de memória para vCore maior de 8 para essas três principais máquinas virtuais. 

Essas máquinas virtuais são boas opções para desenvolvimento e teste menores SQL Server computadores. 

A 8 vCore [Standard_A8m_v2](../../../virtual-machines/av2-series.md) também pode ser uma boa opção para aplicativos pequenos e servidores Web.

> [!NOTE] 
> A série Av2 não dá suporte ao armazenamento Premium e, como tal, não é recomendada para produção SQL Server cargas de trabalho mesmo com as máquinas virtuais que têm uma proporção de memória para vCore de 8.

## <a name="storage-optimized"></a>Otimizado para armazenamento

Os [tamanhos de VM otimizados para armazenamento](../../../virtual-machines/sizes-storage.md) são para casos de uso específicos. Essas máquinas virtuais são projetadas especificamente com taxa de transferência e e/s de disco otimizadas. 

### <a name="lsv2-series"></a>Série Lsv2

A [série Lsv2](../../../virtual-machines/lsv2-series.md) apresenta alta taxa de transferência, baixa latência e armazenamento de NVMe local. As VMs da série Lsv2 são otimizadas para usar o disco local no nó anexado diretamente à VM em vez de usar discos de dados duráveis. 

Essas máquinas virtuais são opções fortes para Big Data, data warehouse, relatórios e cargas de trabalho de ETL. A alta taxa de transferência e o IOPS do armazenamento de NVMe local são um bom caso de uso para o processamento de arquivos que serão carregados no banco de dados e outros cenários em que eles podem ser recriados a partir do sistema de origem ou de outros repositórios, como o armazenamento de BLOBs do Azure ou o Azure Data Lake. [Série Lsv2](../../../virtual-machines/lsv2-series.md) As VMs também podem aumentar seu desempenho de disco por até 30 minutos por vez.

Essas máquinas virtuais têm o tamanho de 8 a 80 vCPU com 8 GiB de memória por vCPU e para cada 8 vCPUs há 1,92 TB de SSD de NVMe. Isso significa que, para a maior VM desta série, o [L80s_v2](../../../virtual-machines/lsv2-series.md), há 80 vCPU e 640 chave de memória com 10 vezes 1.92 TB de armazenamento de NVMe.  Há uma taxa consistente de memória para vCore de 8 em todas essas máquinas virtuais.

O armazenamento NVMe é efêmero, o que significa que os dados serão perdidos nesses discos se você desalocar sua máquina virtual ou se for movido para um host diferente para a recuperação de serviço.

A série Lsv2 e ls dá suporte ao [armazenamento Premium](../../../virtual-machines/premium-storage-performance.md), mas não ao armazenamento em cache Premium. Não há suporte para a criação de um cache local para aumentar o IOPs. 

> [!WARNING]
> Armazenar seus arquivos de dados no armazenamento de NVMe efêmero pode resultar em perda de dados quando a VM é desalocada. 

## <a name="constrained-vcores"></a>VCores restrita

O alto desempenho SQL Server cargas de trabalho geralmente precisam de quantidades maiores de memória, e/s e taxa de transferência sem as maiores contagens de vCore. 

A maioria das cargas de trabalho OLTP são bancos de dados de aplicativos controlados por um grande número de transações menores. Com cargas de trabalho OLTP, apenas uma pequena quantidade de dados é lida ou modificada, mas os volumes de transações controlados por contagens de usuários são muito maiores. É importante ter a SQL Server memória disponível para os planos de cache, armazenar dados acessados recentemente para desempenho e garantir que as leituras físicas possam ser lidas na memória rapidamente. 

Esses ambientes OLTP precisam de mais quantidades de memória, armazenamento rápido e largura de banda de e/s necessárias para serem executados de forma ideal. 

Para manter esse nível de desempenho sem os maiores custos de licenciamento SQL Server, o Azure oferece tamanhos de VM com [contagens de vCPU restritas](../../../virtual-machines/constrained-vcpu.md). 

Isso ajuda a controlar os custos de licenciamento, reduzindo o vCores disponível mantendo a mesma memória, armazenamento e largura de banda de e/s da máquina virtual pai.

A contagem de vCPU pode ser restrita a uma metade do tamanho original da VM. A redução dos vCores disponíveis para a máquina virtual atingirá taxas de memória para vCore maiores, mas o custo de computação permanecerá o mesmo.

Esses novos tamanhos de VM têm um sufixo que especifica o número de vCPUs ativas para facilitar a identificação. 

Por exemplo, o [m64-32ms Standard](../../../virtual-machines/constrained-vcpu.md) requer somente licenciamento 32 SQL Server vCores com memória, e/s e taxa de transferência do [M64ms](../../../virtual-machines/m-series.md) e [m64-16ms](../../../virtual-machines/constrained-vcpu.md) requer licenciamento somente 16 vCores.  Embora o [m64-16ms](../../../virtual-machines/constrained-vcpu.md) tenha um trimestre do custo de licenciamento SQL Server do M64ms, o custo de computação da máquina virtual será o mesmo.

> [!NOTE] 
> - Cargas de trabalho de média a grande data warehouse ainda podem se beneficiar de [VMs vCore restritas](../../../virtual-machines/constrained-vcpu.md), mas as cargas de trabalho de data warehouse geralmente são caracterizadas por menos usuários e processos que abordam quantidades maiores de dados por meio de planos de consulta executados em paralelo. 
> - O custo de computação, que inclui o licenciamento do sistema operacional, permanecerá o mesmo que a máquina virtual pai. 



## <a name="next-steps"></a>Próximas etapas

Para saber mais, consulte os outros artigos desta série:
- [Lista de verificação rápida](performance-guidelines-best-practices-checklist.md)
- [Storage](performance-guidelines-best-practices-storage.md)
- [Coletar linha de base](performance-guidelines-best-practices-collect-baseline.md)

Para obter as práticas recomendadas de segurança, consulte [considerações de segurança para SQL Server em máquinas virtuais do Azure](security-considerations-best-practices.md).

Revise outros artigos sobre Máquinas Virtuais do SQL Server em [Visão geral do SQL Server nas Máquinas Virtuais do Azure](sql-server-on-azure-vm-iaas-what-is-overview.md). Em caso de dúvidas sobre máquinas virtuais do SQL Server, consulte as [Perguntas frequentes](frequently-asked-questions-faq.md).
