---
title: 'Armazenamento: práticas recomendadas de desempenho & diretrizes'
description: Fornece diretrizes e práticas recomendadas de armazenamento para otimizar o desempenho do seu SQL Server na VM (máquina virtual) do Azure.
services: virtual-machines-windows
documentationcenter: na
author: dplessMSFT
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/25/2021
ms.author: dpless
ms.reviewer: jroth
ms.openlocfilehash: 001a9a15c259d0b0d73eec9c9a39ad7c27f26721
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105572333"
---
# <a name="storage-performance-best-practices-for-sql-server-on-azure-vms"></a>Armazenamento: práticas recomendadas de desempenho para SQL Server em VMs do Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este artigo fornece diretrizes e práticas recomendadas de armazenamento para otimizar o desempenho de seus SQL Server em VMs (máquinas virtuais) do Azure.

Normalmente, há uma compensação entre a otimização para custos e a otimização para desempenho. Essa série de práticas recomendadas de desempenho se concentra em obter o *melhor* desempenho para SQL Server em máquinas virtuais do Azure. Se sua carga de trabalho for menos exigente, talvez você não precise de todas as otimizações recomendadas. Considere suas necessidades de desempenho, custos e padrões de carga de trabalho ao avaliar essas recomendações.

Para saber mais, consulte os outros artigos desta série: [lista de verificação de desempenho](performance-guidelines-best-practices-checklist.md), tamanho da [VM](performance-guidelines-best-practices-vm-size.md)e [coletar linha de base](performance-guidelines-best-practices-collect-baseline.md). 

## <a name="checklist"></a>Lista de verificação

Examine a seguinte lista de verificação para obter uma breve visão geral das práticas recomendadas de armazenamento que o restante do artigo aborda em mais detalhes: 

- Monitore o aplicativo e [determine a largura de banda e os requisitos de latência de armazenamento](../../../virtual-machines/premium-storage-performance.md#counters-to-measure-application-performance-requirements) para SQL Server dados, log e arquivos tempdb antes de escolher o tipo de disco. 
- Para otimizar o desempenho do armazenamento, planeje os mais altos IOPS não armazenados em cache disponíveis e use o cache de dados como um recurso de desempenho para leituras de dados, ao mesmo tempo que evita a [máquina virtual e os discos com limitação](../../../virtual-machines/premium-storage-performance.md#throttling).
- Coloque dados, log e arquivos tempdb em unidades separadas.
    - Para a unidade de dados, use apenas [discos p30 e P40 Premium](../../../virtual-machines/disks-types.md#premium-ssd) para garantir a disponibilidade do suporte de cache
    - Para o plano de unidade de log para desempenho de capacidade e teste versus custo ao avaliar os [discos Premium p30-P80](../../../virtual-machines/disks-types.md#premium-ssd)
      - Se a latência de armazenamento de submilissegundos for necessária, use os [ultra discos do Azure](../../../virtual-machines/disks-types.md#ultra-disk) para o log de transações. 
      - Para implantações de máquina virtual série M, considere o [acelerador de gravação](../../../virtual-machines/how-to-enable-write-accelerator.md) usando os ultra discos do Azure.
    - Coloque o [tempdb](/sql/relational-databases/databases/tempdb-database) na unidade SSD efêmera local `D:\` para a maioria das cargas de trabalho SQL Server depois de escolher o tamanho de VM ideal. 
      - Se a capacidade da unidade local não for suficiente para o tempdb, considere o dimensionamento da VM. Consulte [Data File Caching Policies](#data-file-caching-policies) para obter mais informações.
- Distribua vários discos de dados do Azure usando [espaços de armazenamento](/windows-server/storage/storage-spaces/overview) para aumentar a largura de banda de e/s até os limites de IOPS e taxa de transferência da máquina virtual de destino.
- Defina o [cache de host](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) como somente leitura para discos de arquivo de dados.
- Defina o [cache de host](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) como nenhum para discos de arquivo de log.
    - Não habilite o cache de leitura/gravação em discos que contêm SQL Server arquivos. 
    - Sempre pare o serviço de SQL Server antes de alterar as configurações de cache do disco.
- Para cargas de trabalho de desenvolvimento e teste e arquivamento de backup de longo prazo, considere o uso de armazenamento padrão. Não é recomendável usar HDD Standard/SSD para cargas de trabalho de produção.
- [A intermitência de disco baseada em crédito](../../../virtual-machines/disk-bursting.md#credit-based-bursting) (P1-P20) só deve ser considerada para cargas de trabalho de desenvolvimento/teste menores e sistemas departamentais.
- Provisione a conta de armazenamento na mesma região que a VM SQL Server. 
- Desabilite o armazenamento com redundância geográfica do Azure (replicação geográfica) e use LRS (armazenamento com redundância local) na conta de armazenamento.
- Formate seu disco de dados para usar o tamanho da unidade de alocação de 64 KB para todos os arquivos de dados colocados em uma unidade que não seja a `D:\` unidade temporária (que tem um padrão de 4 KB). SQL Server VMs implantadas por meio do Azure Marketplace vêm com discos de dados formatados com tamanho de unidade de alocação e intercalação para o pool de armazenamento definido como 64 KB. 

Para comparar a lista de verificação de armazenamento com outras, consulte a [lista de verificação de práticas recomendadas de desempenho](performance-guidelines-best-practices-checklist.md)abrangente. 

## <a name="overview"></a>Visão geral

Para encontrar a configuração mais eficaz para cargas de trabalho de SQL Server em uma VM do Azure, comece [medindo o desempenho de armazenamento do seu aplicativo de negócios](performance-guidelines-best-practices-collect-baseline.md#storage). Quando os requisitos de armazenamento forem conhecidos, selecione uma máquina virtual que dê suporte à taxa de transferência e IOPS necessários com a proporção de memória para vCore apropriada. 

Escolha um tamanho de VM com escalabilidade de armazenamento suficiente para sua carga de trabalho e uma mistura de discos (geralmente em um pool de armazenamento) que atendam aos requisitos de capacidade e desempenho de sua empresa. 

O tipo de disco depende do tipo de arquivo hospedado no disco e de seus requisitos de desempenho de pico.

> [!TIP]
> O provisionamento de uma VM SQL Server por meio do portal do Azure ajuda a orientá-lo pelo processo de configuração de armazenamento e implementa a maioria das práticas recomendadas de armazenamento, como a criação de pools de armazenamento separados para seus arquivos de dados e de log, direcionando o tempdb para a `D:\` unidade e habilitando a política de cache ideal. Para obter mais informações sobre como provisionar e configurar o armazenamento, consulte [configuração de armazenamento da VM do SQL](storage-configuration.md). 

## <a name="vm-disk-types"></a>Tipos de disco da máquina virtual

Você tem uma opção no nível de desempenho para seus discos. Os tipos de discos gerenciados disponíveis como armazenamento subjacente (listados por recursos de desempenho crescentes) são unidades de disco rígido padrão (HDD), SSDs padrão, SSD (unidades de estado sólido) Premium e ultra discos. 

O desempenho do disco aumenta com a capacidade, agrupada por [Rótulos de disco Premium](../../../virtual-machines/disks-types.md#premium-ssd) , como P1 com 4 GiB de espaço e 120 IOPS para P80 com 32 Tib de armazenamento e 20.000 IOPS. O armazenamento Premium dá suporte a um cache de armazenamento que ajuda a melhorar o desempenho de leitura e gravação para algumas cargas de trabalho. Para obter mais informações, consulte [visão geral de Managed disks](../../../virtual-machines/managed-disks-overview.md). 

Também há três tipos de [disco](../../../virtual-machines/managed-disks-overview.md#disk-roles) principais a serem considerados para seu SQL Server na VM do Azure-um disco do sistema operacional, um disco temporário e seus discos de dados. Escolha cuidadosamente o que está armazenado na unidade do sistema operacional `(C:\)` e na unidade temporária efêmera `(D:\)` . 

### <a name="operating-system-disk"></a>Disco do sistema operacional

Um disco do sistema operacional é um VHD que pode ser inicializado e montado como uma versão em execução de um sistema operacional e rotulado como a `C:\` unidade. Quando você cria uma máquina virtual do Azure, a plataforma anexará pelo menos um disco à VM para o disco do sistema operacional. A `C:\` unidade é o local padrão para instalações de aplicativo e configuração de arquivo. 

Para ambientes de SQL Server de produção, não use o disco do sistema operacional para arquivos de dados, arquivos de log, logs de erros. 

### <a name="temporary-disk"></a>Disco temporário

Muitas máquinas virtuais do Azure contêm outro tipo de disco chamado de disco temporário (rotulado como a `D:\` unidade). Dependendo da série da máquina virtual e do tamanho, a capacidade desse disco variará. O disco temporário é efêmero, o que significa que o armazenamento em disco é recriado (como no, ele é desalocado e alocado novamente), quando a máquina virtual é reiniciada ou movida para um host diferente (para [recuperação de serviço](/troubleshoot/azure/virtual-machines/understand-vm-reboot), por exemplo). 

A unidade de armazenamento temporário não é persistida no armazenamento remoto e, portanto, não deve armazenar arquivos de banco de dados do usuário, arquivos de log de transações ou qualquer coisa que deva ser preservada. 

Coloque o tempdb na unidade SSD temporária local `D:\` para cargas de trabalho de SQL Server, a menos que o consumo do cache local seja uma preocupação. Se você estiver usando uma máquina virtual que [não tem um disco temporário](../../../virtual-machines/azure-vms-no-temp-disk.md) , é recomendável colocar o tempdb em seu próprio disco isolado ou pool de armazenamento com Caching definido como somente leitura. Para saber mais, confira [políticas de cache de dados do tempdb](performance-guidelines-best-practices-storage.md#data-file-caching-policies).

### <a name="data-disks"></a>Discos de dados

Os discos de dados são discos de armazenamento remoto que geralmente são criados em [pools de armazenamento](/windows-server/storage/storage-spaces/overview) para exceder a capacidade e o desempenho que qualquer único disco pode oferecer à máquina virtual.

Anexe o número mínimo de discos que satisfazem os requisitos de IOPS, taxa de transferência e capacidade de sua carga de trabalho. Não exceda o número máximo de discos de dados da menor máquina virtual que você planeja redimensionar.

Coloque arquivos de dados e de log em discos de dados provisionados para atender melhor aos requisitos de desempenho. 

Formate seu disco de dados para usar o tamanho da unidade de alocação de 64 KB para todos os arquivos de dados colocados em uma unidade que não seja a `D:\` unidade temporária (que tem um padrão de 4 KB). SQL Server VMs implantadas por meio do Azure Marketplace vêm com discos de dados formatados com tamanho de unidade de alocação e intercalação para o pool de armazenamento definido como 64 KB. 

## <a name="premium-disks"></a>Discos Premium

Use discos SSD Premium para arquivos de dados e de log para cargas de trabalho de SQL Server de produção. SSD Premium IOPS e largura de banda variam de acordo com o [tamanho e o tipo do disco](../../../virtual-machines/disks-types.md). 

Para cargas de trabalho de produção, use os discos p30 e/ou P40 para SQL Server arquivos de dados para garantir o suporte a cache e use o p30 até P80 para SQL Server arquivos de log de transações.  Para obter o melhor custo total de propriedade, comece com P30s (5000 IOPS/200 MBPS) para arquivos de dados e de log e escolha apenas capacidades mais altas quando precisar controlar a contagem de disco de máquina virtual.

Para cargas de trabalho OLTP, corresponda os IOPS de destino por disco (ou pool de armazenamento) com seus requisitos de desempenho usando cargas de trabalho em horários de pico e os `Disk Reads/sec`  +  `Disk Writes/sec` contadores de desempenho. Para cargas de trabalho de data warehouse e relatórios, corresponda a taxa de transferência de destino usando cargas de trabalho em horários de pico e o `Disk Read Bytes/sec`  +  `Disk Write Bytes/sec` . 

Use espaços de armazenamento para obter o desempenho ideal, configure dois pools, um para os arquivos de log e o outro para os arquivos de dados. Se você não estiver usando a distribuição de disco, use dois discos SSD Premium mapeados para unidades separadas, em que uma unidade contém o arquivo de log e a outra contém os dados.

O [IOPS provisionado e a taxa de transferência](../../../virtual-machines/disks-types.md#premium-ssd) por disco que é usada como parte de seu pool de armazenamento. Os recursos combinados de IOPS e taxa de transferência dos discos são a capacidade máxima até os limites de taxa de transferência da máquina virtual.

A prática recomendada é usar o menor número de discos possíveis ao atender aos requisitos mínimos de IOPS (e taxa de transferência) e à capacidade. No entanto, o equilíbrio entre o preço e o desempenho tende a ser melhor com um grande número de discos pequenos, em vez de um pequeno número de discos grandes.

### <a name="scaling-premium-disks"></a>Dimensionamento de discos Premium

Quando um disco gerenciado do Azure é implantado pela primeira vez, o nível de desempenho desse disco é baseado no tamanho do disco provisionado. Designe o nível de desempenho na implantação ou altere-o posteriormente, sem alterar o tamanho do disco. Se a demanda aumentar, você poderá aumentar o nível de desempenho para atender às suas necessidades de negócios. 

A alteração do nível de desempenho permite que os administradores se preparem e atendam à demanda mais alta sem depender da [intermitência de disco](../../../virtual-machines/disk-bursting.md#credit-based-bursting). 

Use o melhor desempenho durante o tempo necessário, onde a cobrança foi projetada para atender à camada de desempenho de armazenamento. Atualize a camada para corresponder aos requisitos de desempenho sem aumentar a capacidade. Retorne à camada original quando o desempenho extra não for mais necessário.

Essa expansão econômica e temporária de desempenho é um caso de uso forte para eventos direcionados, como compras, testes de desempenho, eventos de treinamento e outras janelas curtas, em que um melhor desempenho é necessário apenas por um curto período. 

Para obter mais informações, consulte [camadas de desempenho para discos gerenciados](../../../virtual-machines/disks-change-performance.md). 

## <a name="azure-ultra-disk"></a>Ultra Disk do Azure

Se houver a necessidade de tempos de resposta de submilissegundos com latência reduzida, considere usar o [ultra Disk do Azure](../../../virtual-machines/disks-types.md#ultra-disk) para a unidade de log de SQL Server ou até mesmo a unidade de dados para aplicativos que são extremamente sensíveis à latência de e/s. 

O ultra Disk pode ser configurado onde a capacidade e o IOPS podem ser dimensionados de forma independente. Com os administradores de ultra disco, é possível provisionar um disco com os requisitos de capacidade, IOPS e taxa de transferência com base nas necessidades do aplicativo. 

O ultra Disk não tem suporte em todas as séries de VMs e tem outras limitações, como disponibilidade de região, redundância e suporte para o backup do Azure. Para saber mais, confira [usando os ultra discos do Azure](../../../virtual-machines/disks-enable-ultra-ssd.md) para obter uma lista completa de limitações. 

## <a name="standard-hdds-and-ssds"></a>HDDs e SSDs padrão

Os HDDs e SSDs [padrão](../../../virtual-machines/disks-types.md#standard-hdd) têm latências e larguras de banda variáveis e são recomendados apenas para cargas de trabalho de desenvolvimento/teste. As cargas de trabalho de produção devem usar os SSDs Premium. Se você estiver usando SSD Standard (cenários de desenvolvimento/teste), a recomendação é adicionar o número máximo de discos de dados com suporte pelo [tamanho da VM](../../../virtual-machines/sizes.md?toc=/azure/virtual-machines/windows/toc.json) e usar a distribuição de disco com espaços de armazenamento para obter o melhor desempenho.

## <a name="caching"></a>Cache

As máquinas virtuais que dão suporte ao cache de armazenamento Premium podem aproveitar um recurso adicional chamado BlobCache do Azure ou cache de host para estender os recursos de IOPS e taxa de transferência de uma máquina virtual. As máquinas virtuais habilitadas para armazenamento Premium e cache de armazenamento Premium têm esses dois limites diferentes de largura de banda de armazenamento que podem ser usados em conjunto para melhorar o desempenho do armazenamento.

A taxa de transferência de IOPS e MBps sem o cache conta com os limites de taxa de transferência de disco não armazenados em cache de uma máquina virtual. Os limites máximos armazenados em cache fornecem um buffer adicional para leituras que ajudam a lidar com o crescimento e picos inesperados.

Habilite o cache Premium sempre que a opção tiver suporte para melhorar significativamente o desempenho de leituras na unidade de dados sem custo adicional. 

Leituras e gravações no BlobCache do Azure (IOPS em cache e taxa de transferência) não são contadas em relação aos limites de taxa de transferência e IOPS não armazenados em cache da máquina virtual.

> [!NOTE]
> O cache de disco não tem suporte para discos 4 TiB e maiores (P50 e maior). Se vários discos estiverem anexados à sua VM, cada disco com menos de 4 TiB dará suporte ao cache. Para obter mais informações, consulte [cache de disco](../../../virtual-machines/premium-storage-performance.md#disk-caching). 

### <a name="uncached-throughput"></a>Taxa de transferência não armazenada em cache

O máximo de IOPS e taxa de transferência do disco não armazenado em cache é o limite máximo de armazenamento remoto que a máquina virtual pode manipular. Esse limite é definido na máquina virtual e não é um limite do armazenamento em disco subjacente. Esse limite aplica-se somente à e/s em unidades de dados conectadas remotamente à VM, não à e/s local na unidade temporária ( `D:\` unidade) ou na unidade do sistema operacional.

A quantidade de IOPS e taxa de transferência não armazenados em cache disponível para uma VM pode ser verificada na documentação da sua máquina virtual.

Por exemplo, a documentação da [série M](../../../virtual-machines/m-series.md) mostra que a taxa de transferência máxima não armazenada em cache para a VM Standard_M8ms é de 5000 IOPS e 125 Mbps de taxa de transferência de disco não armazenado em cache. 

![Captura de tela mostrando a documentação de taxa de transferência do disco não armazenado em cache da série M.](./media/performance-guidelines-best-practices/m-series-table.png)

Da mesma forma, você pode ver que o Standard_M32ts dá suporte a 20.000 IOPS de disco não armazenado em cache e 500 MBps de taxa de transferência de disco não armazenado em cache. Esse limite é regido no nível da máquina virtual, independentemente do armazenamento de disco Premium subjacente.

Para obter mais informações, consulte [limites não armazenados em cache e armazenados em cache](../../../virtual-machines/linux/disk-performance-linux.md#virtual-machine-uncached-vs-cached-limits).


### <a name="cached-and-temp-storage-throughput"></a>Taxa de transferência de armazenamento em cache e temporário

O limite máximo de taxa de transferência de armazenamento em cache e temporário é um limite separado do limite de taxa de transferência não armazenado em cache na máquina virtual. O BlobCache do Azure consiste em uma combinação da memória de acesso aleatório do host da máquina virtual e do SSD anexado localmente. A unidade temporária ( `D:\` unidade) na máquina virtual também é hospedada neste SSD local.

O limite máximo de taxa de transferência de armazenamento em cache e temporário controla a e/s em relação à unidade temporária local ( `D:\` unidade) e ao BlobCache do Azure **somente se** o cache de host estiver habilitado. 

Quando o Caching está habilitado no armazenamento Premium, as máquinas virtuais podem ser dimensionadas além das limitações dos limites de taxa de transferência e IOPS de VM não armazenados em cache remoto.  

Somente determinadas máquinas virtuais dão suporte ao armazenamento Premium e ao cache de armazenamento Premium (que precisa ser verificado na documentação da máquina virtual). Por exemplo, a documentação da [série M](../../../virtual-machines/m-series.md) indica que o armazenamento Premium e o cache de armazenamento Premium têm suporte: 

![Captura de tela mostrando o suporte ao armazenamento Premium da série M.](./media/performance-guidelines-best-practices/m-series-table-premium-support.png)

Os limites do cache variam de acordo com o tamanho da máquina virtual. Por exemplo, a VM Standard_M8ms dá suporte a 10000 IOPS de disco em cache e 1000 MBps de taxa de transferência de disco em cache com um tamanho total de cache de 793 GiB. Da mesma forma, a VM Standard_M32ts dá suporte a 40000 IOPS de disco em cache e 400 MBps de taxa de transferência de disco em cache com um tamanho total de cache de 3174 GiB. 

![Captura de tela mostrando a documentação de taxa de transferência de disco em cache da série M.](./media/performance-guidelines-best-practices/m-series-table-cached-temp.png)

Você pode habilitar o cache de host manualmente em uma VM existente. Interrompa todas as cargas de trabalho do aplicativo e os serviços de SQL Server antes que as alterações sejam feitas na política de cache da máquina virtual. A alteração de qualquer uma das configurações de cache da máquina virtual resulta no disco de destino sendo desanexado e reanexado depois que as configurações são aplicadas.

### <a name="data-file-caching-policies"></a>Políticas de cache de arquivo de dados

Sua política de cache de armazenamento varia dependendo do tipo de SQL Server arquivos de dados hospedados na unidade. 

A tabela a seguir fornece um resumo das políticas de cache recomendadas com base no tipo de dados de SQL Server: 

|SQL Server disco |Recomendação |
|---------|---------|
| **Disco de dados** | Habilite `Read-only` o Caching para os discos que hospedam SQL Server arquivos de dados. <br/> As leituras do cache serão mais rápidas do que as leituras não armazenadas em cache do disco de dados. <br/> IOPS e taxa de transferência não armazenados em cache, além de IOPS em cache e taxa de transferência, produzirão o desempenho total possível disponível na máquina virtual dentro dos limites das VMs, mas o desempenho real variará com base na capacidade da carga de trabalho de usar o cache (taxa de acertos do cache). <br/>|
|**Disco de log de transações**|Defina a política de cache para `None` para discos que hospedam o log de transações.  Não há nenhum benefício de desempenho para habilitar o Caching para o disco de log de transações e, na verdade, ter `Read-only` ou o `Read/Write` cache habilitado na unidade de log pode prejudicar o desempenho das gravações na unidade e diminuir a quantidade de cache disponível para leituras na unidade de dados.  |
|**Disco do so operacional** | A política de cache padrão pode ser `Read-only` ou `Read/write` para a unidade do sistema operacional. <br/> Não é recomendável alterar o nível de cache da unidade do sistema operacional.  |
| **tempdb**| Se o tempdb não puder ser colocado na unidade efêmera `D:\` devido a motivos de capacidade, redimensione a máquina virtual para obter uma unidade efêmera maior ou coloque o tempdb em uma unidade de dados separada com `Read-only` Caching configurado. <br/> O cache da máquina virtual e a unidade efêmera usam o SSD local. portanto, tenha isso em mente ao Dimensionar como e/s de tempdb irá contar com os limites de máquina virtual de taxa de transferência e IOPS armazenados em cache quando hospedado na unidade efêmera.| 
| | | 


Para saber mais, confira [cache de disco](../../../virtual-machines/premium-storage-performance.md#disk-caching). 


## <a name="disk-striping"></a>Distribuição de discos

Analise a taxa de transferência e a largura de banda necessárias para seus arquivos de dados SQL para determinar o número de discos de dados, incluindo o arquivo de log e o tempdb. Taxa de transferência e limites de largura de banda variam por tamanho de VM. Para saber mais, consulte [tamanho da VM](../../../virtual-machines/sizes.md)

Adicione discos de dados adicionais e use a distribuição de disco para obter mais produtividade. Por exemplo, um aplicativo que precisa de 12.000 IOPS e 180 MB/s de taxa de transferência pode usar três discos p30 distribuídos para fornecer 15.000 IOPS e 600 MB/s de taxa de transferência. 

Para configurar a distribuição de disco, consulte [distribuição de disco](storage-configuration.md#disk-striping). 

## <a name="disk-capping"></a>Limitação de disco 

Há limites de taxa de transferência no nível do disco e da máquina virtual. Os limites máximos de IOPS por VM e por disco diferem e são independentes um do outro.

Os aplicativos que consomem recursos além desses limites serão limitados (também conhecido como capped). Selecione uma máquina virtual e o tamanho do disco em uma distribuição de disco que atenda aos requisitos do aplicativo e não haverá limitações de limitação. Para solucionar o capping, use Caching ou ajuste o aplicativo para que menos produtividade seja necessária.

Por exemplo, um aplicativo que precisa de 12.000 IOPS e 180 MB/s pode: 
- Use o [Standard_M32ms](../../../virtual-machines/m-series.md) que tem uma taxa de transferência máxima de disco não armazenado em cache de 20.000 IOPS e 500 Mbps.
- Distribua três discos p30 para fornecer taxa de transferência de 15.000 IOPS e 600 MB/s.
- Use um [Standard_M16ms](../../../virtual-machines/m-series.md) máquina virtual e use o cache de host para utilizar o cache local sobre o consumo de taxa de transferência. 

As máquinas virtuais configuradas para escalar verticalmente durante os tempos de alta utilização devem provisionar o armazenamento com IOPS e taxa de transferência suficientes para dar suporte ao tamanho máximo de VM, mantendo o número total de discos menores ou iguais ao número máximo com suporte pelo menor SKU de VM destinado a ser usado.

Para obter mais informações sobre limitações de limitação de disco e como usar o Caching para evitar a limitação, consulte [limitação de e/s de disco](../../../virtual-machines/disks-performance.md).

> [!NOTE] 
> Alguns limitação de disco ainda podem resultar em desempenho satisfatório para os usuários; ajuste e mantenha cargas de trabalho em vez de redimensionar para uma VM maior para balancear o gerenciamento de custo e desempenho para os negócios. 


## <a name="write-acceleration"></a>Aceleração de gravação

A aceleração de gravação é um recurso de disco disponível somente para VMs (máquinas virtuais) da [série M](https://docs.microsoft.com/azure/virtual-machines/m-series) . A finalidade da aceleração de gravação é melhorar a latência de e/s de gravações no armazenamento Premium do Azure quando você precisa de latência de e/s de dígito único devido a cargas de trabalho de alto custo crítico de missão crítica de volume ou ambientes de data warehouse. 

Use a aceleração de gravação para melhorar a latência de gravação na unidade que hospeda os arquivos de log. Não use a aceleração de gravação para SQL Server arquivos de dados. 

Acelerador de Gravação discos compartilham o mesmo limite de IOPS que a máquina virtual. Os discos anexados não podem exceder o limite de IOPS de Acelerador de Gravação para uma VM.  

A tabela a seguir descreve o número de discos de dados e IOPS com suporte por máquina virtual: 

| SKU da VM  | N º de discos Acelerador de Gravação  | Acelerador de Gravação IOPS de disco por VM  |
|---|---|---|
| M416ms_v2, M416s_v2  | 16  | 20000  |
| M128ms, M128s  | 16  | 20000  |
| M208ms_v2, M208s_v2  | 8  | 10000  |
| M64ms, M64ls, M64s  |  8 | 10000 |
| M32ms, M32ls, M32ts, M32s  | 4  | 5.000  |
| M16ms, M16s  | 2 | 2500 |
| M8ms, M8s  | 1 | 1250 |

Há várias restrições ao uso da aceleração de gravação. Para saber mais, confira [restrições ao usar acelerador de gravação](../../../virtual-machines/how-to-enable-write-accelerator.md#restrictions-when-using-write-accelerator).


### <a name="comparing-to-azure-ultra-disk"></a>Comparando com o Azure ultra Disk

A maior diferença entre a aceleração de gravação e os ultra discos do Azure é que a aceleração de gravação é um recurso de máquina virtual disponível somente para a série M e os ultra discos do Azure são uma opção de armazenamento. Aceleração de gravação é um cache otimizado para gravação com suas próprias limitações com base no tamanho da máquina virtual. Os ultra discos do Azure são uma opção de armazenamento de disco de baixa latência para máquinas virtuais do Azure. 

Se possível, use a aceleração de gravação sobre ultra discos para o disco de log de transações. Para máquinas virtuais que não dão suporte à aceleração de gravação, mas exigem baixa latência para o log de transações, use os ultra discos do Azure. 

## <a name="monitor-storage-performance"></a>Monitorar o desempenho do armazenamento

Para avaliar as necessidades de armazenamento e determinar o desempenho do armazenamento, você precisa entender o que medir e o que esses indicadores significam. 

[IOPS (entrada/saída por segundo)](../../../virtual-machines/premium-storage-performance.md#iops) é o número de solicitações que o aplicativo está fazendo para armazenamento por segundo. Meça a IOPS usando contadores do monitor `Disk Reads/sec` de desempenho e `Disk Writes/sec` . Os aplicativos [OLTP (processamento de transações online)](/azure/architecture/data-guide/relational-data/online-transaction-processing) precisam impulsionar IOPS maiores para obter um desempenho ideal. Aplicativos como sistemas de processamento de pagamento, compras online e sistemas de ponto de venda de varejo são exemplos de aplicativos OLTP.

A [taxa de transferência](../../../virtual-machines/premium-storage-performance.md#throughput) é o volume de dados que está sendo enviado para o armazenamento subjacente, geralmente medido por megabytes por segundo. Meça a taxa de transferência com os contadores do monitor de desempenho `Disk Read Bytes/sec` e `Disk Write Bytes/sec` . O [data warehousing](/azure/architecture/data-guide/relational-data/data-warehousing) é otimizado em relação à maximização da taxa de transferência em IOPS. Aplicativos como armazenamentos de dados para análise, relatórios, ETL workstreams e outros destinos de business intelligence são exemplos de aplicativos de data warehouse.

Os tamanhos de unidade de e/s influenciam os recursos de IOPS e taxa de transferência, pois tamanhos menores de e/s produzem IOPS maior e tamanhos de e/s maiores produzem uma taxa de transferência maior. SQL Server escolhe o tamanho de e/s ideal automaticamente. Para obter mais informações sobre o, consulte [otimizar IOPS, taxa de transferência e latência para seus aplicativos](../../../virtual-machines/premium-storage-performance.md#optimize-iops-throughput-and-latency-at-a-glance). 

Há métricas de Azure Monitor específicas que são inestimáveis para descobrir o capping na máquina virtual e no nível de disco, bem como o consumo e a integridade do cache AzureBlob. Para identificar os principais contadores a serem adicionados à sua solução de monitoramento e portal do Azure painel, consulte [métricas de utilização de armazenamento](../../../virtual-machines/disks-metrics.md#storage-io-utilization-metrics). 

> [!NOTE]
> Atualmente, o Azure Monitor não oferece métricas em nível de disco para a unidade temporária efêmera `(D:\)` . A porcentagem consumida de IOPS em cache de VM e a porcentagem consumida de largura de banda em cache de VM refletirá IOPS e taxa de transferência da unidade temporária efêmera `(D:\)` e do cache de host juntos.


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as práticas recomendadas de desempenho, consulte os outros artigos desta série:
- [Lista de verificação rápida](performance-guidelines-best-practices-checklist.md)
- [Tamanho da VM](performance-guidelines-best-practices-vm-size.md)
- [Coletar linha de base](performance-guidelines-best-practices-collect-baseline.md)

Para obter as práticas recomendadas de segurança, consulte [considerações de segurança para SQL Server em máquinas virtuais do Azure](security-considerations-best-practices.md).

– Para obter testes detalhados de desempenho do SQL Server em VMs do Azure com avaliações de desempenho de TPC-E e TPC_C, veja o blog [Otimizar o desempenho de OLTP](https://techcommunity.microsoft.com/t5/sql-server/optimize-oltp-performance-with-sql-server-on-azure-vm/ba-p/916794).

Revise outros artigos sobre Máquinas Virtuais do SQL Server em [Visão geral do SQL Server nas Máquinas Virtuais do Azure](sql-server-on-azure-vm-iaas-what-is-overview.md). Em caso de dúvidas sobre máquinas virtuais do SQL Server, consulte as [Perguntas frequentes](frequently-asked-questions-faq.md).
