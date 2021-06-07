---
title: Tipos de armazenamento do Azure para a carga de trabalho do SAP
description: Planejamento dos tipos de armazenamento do Azure para cargas de trabalho do SAP
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/26/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 329e09221467c2602355e091876c95f305db3578
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101673741"
---
# <a name="azure-storage-types-for-sap-workload"></a>Tipos de Armazenamento do Azure para carga de trabalho SAP
O Azure tem vários tipos de armazenamento que diferem amplamente em recursos, taxa de transferência, latência e preços. Alguns dos tipos de armazenamento não são utilizáveis ou têm uso limitado em cenários do SAP. Enquanto vários tipos de armazenamento do Azure são adequados ou otimizados para cenários de carga de trabalho do SAP específicos. Especialmente para o SAP HANA, alguns tipos de armazenamento do Azure receberam certificação para o uso com o SAP HANA. Neste documento, examinaremos os diferentes tipos de armazenamento e descreveremos a capacidade e usabilidade deles com as cargas de trabalho do SAP e com os componentes do SAP.

Comentário sobre as unidades usadas neste artigo. Os fornecedores de nuvem pública passaram a usar GiB ([gibibyte](https://en.wikipedia.org/wiki/Gibibyte)) ou TiB ([tebibyte](https://en.wikipedia.org/wiki/Tebibyte)) como unidades de tamanho, em vez de gigabyte ou terabyte. Portanto, todas as documentações e preços do Azure estão usando essas unidades.  Em todo o documento fazemos referência apenas às unidades de tamanho MiB, GiB e TiB. Talvez seja necessário planejar com MB, GB e TB. Portanto, esteja ciente de algumas pequenas diferenças nos cálculos se você precisar dimensionar para uma taxa de transferência de 400 MiB/s, em vez de uma taxa de transferência de 250 MiB/s.

## <a name="microsoft-azure-storage-resiliency"></a>Resiliência do Armazenamento do Microsoft Azure

Armazenamento do Microsoft Azure de HDD Standard, SSD Standard, armazenamento Premium do Azure e Disco Ultra mantêm o VHD base (com o sistema operacional) e os discos de dados anexados à VM ou VHDs em três cópias em três nós de armazenamento diferentes. O failover para outra réplica e a propagação de uma nova réplica no caso de uma falha de nó de armazenamento é transparente. Como resultado dessa redundância, **NÃO** é necessário usar nenhum tipo de camada de redundância de armazenamento em vários discos do Azure. Esse fato é chamado de Armazenamento com Redundância Local (LRS). O LRS é o padrão para esses tipos de armazenamento no Azure. [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) fornece redundância suficiente para alcançar os mesmos SLAs que o armazenamento do Azure nativo.

Há muitos outros métodos de redundância, todos eles descritos no artigo [Replicação de Armazenamento do Azure](../../../storage/common/storage-redundancy.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json), que se aplicam a alguns dos diferentes tipos de armazenamento que o Azure tem a oferecer. 

Lembre-se também que diferentes tipos de armazenamento do Azure influenciam os SLAs de disponibilidade de VM única lançados no [SLA para Máquinas Virtuais](https://azure.microsoft.com/support/legal/sla/virtual-machines).

### <a name="azure-managed-disks"></a>Azure Managed Disks

Discos gerenciados são um tipo de recurso no Azure Resource Manager que pode ser usado em vez de VHDs armazenados em Contas de Armazenamento do Azure. Os Managed Disks alinham-se automaticamente ao [conjunto de disponibilidade][virtual-machines-manage-availability] da máquina virtual à qual eles estão conectados e, portanto, aumentam a disponibilidade da sua máquina virtual e dos serviços que estão em execução nessa máquina. Para obter mais informações, leia o [artigo de visão geral](../../managed-disks-overview.md).

Relacionado à resiliência, este exemplo demonstra a vantagem dos discos gerenciados:

- Você está implantando suas duas VMs DBMS para seu sistema SAP em um conjunto de disponibilidade do Azure 
- À medida que o Azure implanta as VMs, o disco com a imagem do sistema operacional será colocado em um cluster de armazenamento diferente. Isso evita que ambas as VMs sejam afetadas por um problema de um cluster de armazenamento do Azure
- À medida que você cria discos gerenciados que você atribui a essas VMs para armazenar os dados e os arquivos de log do seu banco de dados, esses novos discos para as duas VMs também são implantados em clusters de armazenamento separados, portanto, nenhum dos discos da primeira VM compartilha clusters de armazenamento com os discos da segunda VM

Implantando sem discos gerenciados nas contas de armazenamento definidas pelo cliente, a alocação do disco é arbitrária e não tem consciência do fato de que as VMs são implantadas dentro de um AvSet para fins de resiliência.

> [!NOTE]
> Por esse motivo e devido a vários outros aprimoramentos que estão disponíveis exclusivamente por meio de discos gerenciados, exigimos que novas implantações de VMs que usam o armazenamento em bloco do Azure nos discos (todo o armazenamento do Azure, exceto o Azure NetApp Files) precisam usar os Azure Managed Disks nos discos VHD/OS de base, que são discos de dados que contêm arquivos de banco de dados do SAP. Isso é exigido independentemente de você implantar as VMs por meio do conjunto de disponibilidade, entre Zonas de Disponibilidade ou dos conjuntos e das zonas. Os discos que são usados com a finalidade de armazenar backups não são necessariamente precisam ser discos gerenciados.

> [!NOTE]
> Os discos gerenciados do Azure fornecem apenas LRS (redundância local). 


## <a name="storage-scenarios-with-sap-workloads"></a>Cenários de armazenamento com as cargas de trabalho SAP
O armazenamento persistente é necessário na carga de trabalho do SAP em vários componentes da pilha que você implanta no Azure. Esses cenários constam, no mínimo, os seguintes:

- O armazenamento persistente do VHD de base da sua VM que contém o sistema operacional e outro software que você instala nesse disco. Este disco/VHD é a raiz de sua VM. Todas as alterações feitas nele precisam ser persistidas. Assim, na próxima vez que você interromper e reiniciar a VM, todas as alterações feitas anteriormente ainda existirão. Especialmente nos casos em que a VM está sendo implantada pelo Azure em outro host diferente daquele que estava em execução originalmente
- Discos de dados persistentes. Esses discos são VHDs anexados para armazenar dados de aplicativo. Esses dados de aplicativos podem ser arquivos de log/refazer e dados de um banco de dados, arquivos de backup ou instalações de software. Isso significa qualquer disco além do seu VHD de base que contém o sistema operacional
- Compartilhamentos de arquivos ou discos compartilhados que contêm seu diretório de transporte global para NetWeaver ou S/4HANA. O conteúdo desses compartilhamentos é consumido pelo software em execução em várias VMs ou é usado para criar cenários de cluster de failover de alta disponibilidade
- O diretório /sapmnt ou compartilhamentos de arquivos comuns para processos EDI ou semelhantes. O conteúdo desses compartilhamentos é consumido pelo software em execução em várias VMs ou é usado para criar cenários de cluster de failover de alta disponibilidade

Nas próximas seções, serão discutidos os diferentes tipos de armazenamento do Azure que se aplicam aos quatro cenários acima e a usabilidade deles na carga de trabalho do SAP. Uma categorização geral de como os diferentes tipos de armazenamento do Azure devem ser usados está descrita no artigo [Quais tipos de disco estão disponíveis no Azure?](../../disks-types.md). As recomendações para usar os diferentes tipos de armazenamento do Azure para carga de trabalho do SAP não serão muito diferentes.

Para obter as restrições de suporte nos tipos de armazenamento do Azure do SAP NetWeaver/camada de aplicativo de S/4HANA, leia a [Nota de suporte do SAP 2015553](https://launchpad.support.sap.com/#/notes/2015553). Para obter os tipos de armazenamento do Azure com suporte e certificados por SAP HANA, leia o artigo [Configurações de armazenamento de máquina virtual do Azure para SAP HANA](./hana-vm-operations-storage.md).

As seções que descrevem os diferentes tipos de armazenamento do Azure fornecerão mais informações sobre as restrições e as possibilidades usando o armazenamento com suporte ao SAP. 

## <a name="storage-recommendations-for-sap-storage-scenarios"></a>Recomendações de armazenamento para cenários de armazenamento SAP
Antes de entrar em detalhes, apresentaremos o resumo e as recomendações já no início do documento. Enquanto os detalhes dos tipos específicos de armazenamento do Azure serão descritos após esta seção do documento. O resumo das recomendações de armazenamento para os cenários de armazenamento SAP em uma tabela tem a seguinte aparência:

| Cenário de uso | HDD Standard | SSD Standard | Armazenamento Premium | Disco Ultra | Azure NetApp Files |
| --- | --- | --- | --- | --- | --- |
| Disco do sistema operacional | não é adequado |  adequado com restrições (não usar em produção) | recomendável | impossível | impossível |
| Diretório de transporte global | sem suporte | sem suporte | recomendável | recomendável | recomendável |
| /sapmnt | não é adequado | adequado com restrições (não usar em produção) | recomendável | recomendável | recomendável |
| Famílias de VM M/Mv2 do SAP HANA para volume de dados do DBMS | sem suporte | sem suporte | recomendável | recomendável | recomendado<sup>2</sup> |
| Famílias de VM M/Mv2 do SAP HANA para volume do log do DBMS | sem suporte | sem suporte | recomendado<sup>1</sup> | recomendável | recomendado<sup>2</sup> | 
| Famílias de VM Esv3/Edsv4 do SAP HANA para volume de dados do DBMS | sem suporte | sem suporte | recomendável | recomendável | recomendado<sup>2</sup> |
| Famílias de VM Esv3/Edsv4 do SAP HANA para volume do log do DBMS | sem suporte | sem suporte | sem suporte | recomendável | recomendado<sup>2</sup> | 
| Volume de dados do DBMS não HANA | sem suporte | adequado com restrições (não usar em produção) | recomendável | recomendável | sem suporte |
| Famílias de VM M/Mv2 não HANA para volume do log do DBMS | sem suporte | adequado com restrições (não usar em produção) | recomendado<sup>1</sup> | recomendável | sem suporte |
| Famílias de VMs que não são M/Mv2 nem HANA para volume de log do DBMS | sem suporte | adequado com restrições (não usar em produção) | adequado, no máximo, para a carga de trabalho média | recomendável | sem suporte |


<sup>1</sup> Com o uso do [Acelerador de Gravação do Azure](../../how-to-enable-write-accelerator.md) para famílias da VM M/Mv2 para log/volumes de log de restauração <sup>2</sup>. Usar o ANF requer que /hana/data, bem como o /hana/log estejam no ANF 

Características que você pode esperar da lista de diferentes tipos de armazenamento, como:

| Cenário de uso | HDD Standard | SSD Standard | Armazenamento Premium | Disco Ultra | Azure NetApp Files |
| --- | --- | --- | --- | --- | --- |
| SLA de taxa de transferência/IOPS | não | não | sim | sim | sim |
| Leituras de latência | high | médio a alto | low | sub-milissegundo | sub-milissegundo |
| Gravações de latência | high | médio a alto  | baixo (submilissegundo<sup>1</sup>) | sub-milissegundo | sub-milissegundo |
| Com suporte ao HANA | não | não | sim<sup>1</sup> | sim | sim |
| Instantâneos de disco possíveis | sim | sim | sim | não | sim |
| Alocação de discos em diferentes clusters de armazenamento ao usar conjuntos de disponibilidade | por meio de discos gerenciados | por meio de discos gerenciados | por meio de discos gerenciados | tipo de disco sem suporte com VMs implantadas por meio de conjuntos de disponibilidade | não<sup>3</sup> |
| Alinhado com as Zonas de Disponibilidade | sim | sim | sim | sim | precisa de envolvimento da Microsoft |
| Redundância de zona | não para discos gerenciados | não para discos gerenciados | não para discos gerenciados | não | não |
| Redundância geográfica | não para discos gerenciados | não para discos gerenciados | não | não | não |


<sup>1</sup> Com o uso do [Acelerador de Gravação do Azure](../../how-to-enable-write-accelerator.md) nas famílias de VM M/Mv2 para log/volume do log de restauração

<sup>2</sup> Os custos dependem da IOPS e da taxa de transferência provisionadas

<sup>3</sup> A criação de diferentes pools de capacidade do ANF não garante a implantação de pools de capacidade em unidades de armazenamento diferentes


> [!IMPORTANT]
> Para obter menos de um milissegundo de latência de E/S usando o ANF (Azure NetApp Files), você precisa trabalhar com a Microsoft para organizar o posicionamento correto entre as suas VMs e os compartilhamentos NFS com base no ANF. Até agora, não há nenhum mecanismo em vigor que forneça uma proximidade automática entre uma VM implantada e os volumes de NFS hospedados no ANF. Devido a configurações distintas de diferentes regiões do Azure, a latência de rede adicionada poderá demorar mais de um milissegundo para enviar por push a latência de E/S se a VM e o compartilhamento NFS não estiverem alocados próximos.


> [!IMPORTANT]
> O Azure NetApp Files e nenhum dos discos gerenciados baseados no armazenamento em bloco do Azure atualmente oferecidos oferecem redundância de zona ou geográfica. Como resultado, você precisa verificar se as suas arquiteturas de alta disponibilidade e de recuperação de desastre não dependem de nenhum tipo de replicação de armazenamento nativo do Azure para esses compartilhamentos SMB, NFS e de discos gerenciados.


## <a name="azure-premium-storage"></a>Armazenamento Premium do Azure
O armazenamento SSD Premium do Azure foi introduzido com a meta de fornecer:

* Latência de E/S baixa
* SLAs para IOPS e taxa de transferência
* Menos variabilidade na latência de E/S

Esse tipo de armazenamento destina-se a cargas de trabalho do DBMS, a tráfego de armazenamento que exige baixa latência inferior a dez milissegundos e aos SLAs em uma base de Custo de IOPS e de taxa de transferência caso o armazenamento Premium do Azure não seja o volume de dados real armazenado nesses discos e sim a categoria de tamanho do disco, independente da quantidade de dados armazenados nele. Você também pode criar discos no armazenamento premium que não estão sendo mapeados diretamente para as categorias de tamanho mostradas no artigo [SSD Premium](../../disks-types.md#premium-ssd). As conclusões deste artigo são:

- O armazenamento é organizado em intervalos. Por exemplo, um disco no intervalo de capacidade de 513 GiB a 1024 GiB compartilha as mesmas funcionalidades e os mesmos custos mensais
- A IOPS por GiB não está sendo acompanhada linearmente nas categorias de tamanho. Discos menores abaixo de 32 GiB têm taxas de IOPS maiores por GiB. Para discos com mais de 32 GiB a 1024 GiB, a taxa de IOPS por GiB está entre 4 e 5 IOPS por GiB. Para discos maiores de até 32.767 GiB, a taxa de IOPS por GiB está abaixo de 1
- A taxa de transferência de E/S desse armazenamento não é linear com o tamanho da categoria de disco. Para discos menores, assim como na categoria com capacidade entre 65 GiB e 128 GiB, a taxa de transferência é cerca de 780KB/GiB. Enquanto em discos muito grandes, como um disco de 32.767 GiB, a taxa de transferência está em cerca de 28KB/GiB
- Os SLAs de taxa de transferência e de IOPS não podem ser alterados sem alterar a capacidade do disco


A matriz de capacidade da carga de trabalho do SAP é semelhante a:

| Funcionalidade| Comentário| Observações/links | 
| --- | --- | --- | 
| VHD de base do sistema operacional | adequado | todos os sistemas |
| Disco de dados | adequado | todos os sistemas – [especialmente para SAP HANA](../../how-to-enable-write-accelerator.md) |
| Diretório de transporte global do SAP | YES | [Com suporte](https://launchpad.support.sap.com/#/notes/2015553) |
| sapmnt do SAP | adequado | todos os sistemas |
| Armazenamento de backup | adequado | para armazenamento de backups de curto prazo |
| Compartilhamentos/disco compartilhado | não disponível | Precisa de Arquivos Premium do Azure ou de terceiros |
| Resiliência | LRS | Nenhum GRS ou ZRS disponível para discos |
| Latency | baixo a médio | - |
| SLA de IOPS | YES | - |
| IOPS linear com a capacidade | semilinear entre colchetes  | [Preço do Managed Disk](https://azure.microsoft.com/pricing/details/managed-disks/) |
| IOPS máxima por disco | 20.000 [dependendo do tamanho do disco](https://azure.microsoft.com/pricing/details/managed-disks/) | Considere também os [limites da VM](../../sizes.md) |
| SLA de taxa de transferência | YES | - |
| Taxa de transferência linear com a capacidade | semilinear entre colchetes | [Preço do Managed Disk](https://azure.microsoft.com/pricing/details/managed-disks/) |
| Certificado pelo HANA | YES | [especialmente para SAP HANA](../../how-to-enable-write-accelerator.md) |
| Instantâneos de disco possíveis | YES | - |
| Instantâneos de VM do Backup do Azure possíveis | YES | exceto para disco armazenados em cache do [Acelerador de Gravação](../../how-to-enable-write-accelerator.md)  |
| Custos | MEDIUM | - |

O armazenamento Premium do Azure não atende os KPIs de latência de armazenamento do SAP HANA com os tipos de cache comuns oferecidos com o armazenamento Premium do Azure. Para atender aos KPIs de latência de armazenamento para gravações de log do SAP HANA, você precisa usar o cache do Acelerador de Gravação do Azure, conforme descrito no artigo [Habilitar o Acelerador de Gravação](../../how-to-enable-write-accelerator.md). O Acelerador de Gravação do Azure beneficia todos os outros sistemas de DBMS para as gravações de log de transações e gravações de log de restauração. Portanto, é recomendável usá-lo em todas as implantações DBMS do SAP. No SAP HANA, o uso do Acelerador de Gravação do Azure em conjunto com o armazenamento Premium do Azure é obrigatório.



**Resumo**: o armazenamento Premium do Azure é um dos tipos de armazenamento do Azure recomendados para a carga de trabalho do SAP. Essa recomendação se aplica a sistemas de produção e não de produção. O armazenamento Premium do Azure é adequado para lidar com as cargas de trabalho do banco de dados. O uso do Acelerador de Gravação do Azure aprimora substancialmente a latência de gravação em discos premium do Azure. No entanto, em sistemas DBMS com taxas elevadas de IOPS e de transferência, você precisa provisionar excessivamente a capacidade de armazenamento ou usar funcionalidades como os Espaços de Armazenamento do Windows ou gerenciadores de volume lógico no Linux para criar conjuntos de distribuição que oferecem a capacidade desejada por um lado, mas também a IOPS ou a taxa de transferência necessária com o melhor custo-benefício.


### <a name="azure-burst-functionality-for-premium-storage"></a>Funcionalidade de intermitência do Azure para armazenamento premium
É oferecida a funcionalidade de intermitência a discos de armazenamento premium do Azure com capacidade menor ou igual a 512 GiB. A maneira exata que o bursting de disco funciona é descrita no artigo [Bursting de disco](../../disk-bursting.md). Ao ler o artigo, você entenderá o conceito de acumular IOPS e taxa de transferência quando a sua carga de trabalho de E/S estiver abaixo do IOPS nominal e da taxa de transferência dos discos (para obter detalhes sobre a taxa de transferência nominal, confira [Preço do Managed Disk](https://azure.microsoft.com/pricing/details/managed-disks/)). Você acumulará o delta de IOPS e da taxa de transferência sobre o uso atual e os valores nominais do disco. As intermitências são limitadas a, no máximo, 30 minutos.

Os casos ideais em que essa funcionalidade de intermitência pode ser planejada provavelmente serão os volumes ou discos que contêm arquivos de dados para o DBMS diferente. A carga de trabalho de E/S esperada nesses volumes, especialmente com sistemas de pequeno a médio porte, deve ser semelhante a:

- Carga de trabalho de leitura baixa a moderada, pois os dados idealmente são armazenados em cache na memória ou, como no caso do HANA, devem estar completamente na memória
- Intermitências de gravações disparadas por pontos de verificação de banco de dados ou por pontos de salvamento emitidos regularmente
- Carga de trabalho de backup que lê em um fluxo contínuo em casos em que os backups não são executados por meio de instantâneos de armazenamento
- Para o SAP HANA, carregar os dados na memória após uma reinicialização da instância

Especialmente em sistemas DBMS menores em que a carga de trabalho lida apenas com algumas centenas de transações por segundo, uma funcionalidade de intermitência também pode fazer sentido para os discos ou volumes que armazenam a transação ou o log refazer. A carga de trabalho esperada nesse disco ou nesses volumes é semelhante a:

- Gravações regulares no disco que dependem da carga de trabalho e da natureza da carga de trabalho, pois cada confirmação emitida pelo aplicativo provavelmente dispara uma operação de E/S
- Maior carga de trabalho na taxa de transferência para casos de tarefas operacionais, como criar ou recriar índices
- Intermitências de leitura ao executar o log de transações ou backups de log de refazer


## <a name="azure-ultra-disk"></a>Disco Ultra do Azure
Os discos Ultra do Azure proporcionam uma alta taxa de transferência, alta IOPS e armazenamento em disco de baixa latência e consistente para as VMs de IaaS do Azure. Alguns benefícios adicionais dos Discos Ultra incluem a capacidade de alterar dinamicamente a IOPS e a taxa de transferência do disco juntamente com as suas cargas de trabalho, sem a necessidade de reiniciar as VMs (máquinas virtuais). Os Discos Ultra são adequados para cargas de trabalho com uso intensivo de dados, como carga de trabalho do SAP DBMS. Os Discos Ultra só podem ser usados como discos de dados e não podem ser usados como um disco VHD de base que armazena o sistema operacional. É recomendável que o armazenamento Premium do Azure seja usado como um disco VHD de base.

Ao criar um Disco Ultra, você tem três dimensões que podem ser definidas:

- A capacidade do disco. Os intervalos são de 4 GiB a 65.536 GiB
- IOPS provisionada para o disco. Diferentes valores máximos se aplicam à capacidade do disco. Leia o artigo [Disco Ultra](../../disks-types.md#ultra-disk) para obter mais detalhes
- Largura de banda de armazenamento provisionada. Diferentes larguras de banda máximas são aplicáveis de acordo com a capacidade do disco. Leia o artigo [Disco Ultra](../../disks-types.md#ultra-disk) para obter mais detalhes

O custo de um disco é determinado pelas três dimensões que você pode definir para os discos específicos separadamente. 


A matriz de capacidade da carga de trabalho do SAP é semelhante a:

| Funcionalidade| Comentário| Observações/links | 
| --- | --- | --- | 
| VHD de base do sistema operacional | não funciona | - |
| Disco de dados | adequado | todos os sistemas  |
| Diretório de transporte global do SAP | YES | [Com suporte](https://launchpad.support.sap.com/#/notes/2015553) |
| sapmnt do SAP | adequado | todos os sistemas |
| Armazenamento de backup | adequado | para armazenamento de backups de curto prazo |
| Compartilhamentos/disco compartilhado | não disponível | Precisa de terceiros |
| Resiliência | LRS | Nenhum GRS ou ZRS disponível para discos |
| Latency | muito baixo | - |
| SLA de IOPS | YES | - |
| IOPS linear com a capacidade | semilinear entre colchetes  | [Preço do Managed Disk](https://azure.microsoft.com/pricing/details/managed-disks/) |
| IOPS máxima por disco | 1\.200 a 160.000 | dependendo da capacidade do disco |
| SLA de taxa de transferência | YES | - |
| Taxa de transferência linear com a capacidade | semilinear entre colchetes | [Preço do Managed Disk](https://azure.microsoft.com/pricing/details/managed-disks/) |
| Certificado pelo HANA | YES | - |
| Instantâneos de disco possíveis | Não | - |
| Instantâneos de VM do Backup do Azure possíveis | Não | - |
| Custos | Maior do que o armazenamento Premium | - |



**Resumo**: os Discos Ultra do Azure são um armazenamento adequado com baixa latência para todos os tipos de carga de trabalho do SAP. Até agora, o Disco Ultra só pode ser usado em combinações com VMs implantadas por meio de Zonas de Disponibilidade (implantação zonal). O Disco Ultra não dá suporte a instantâneos de armazenamento neste momento. Ao contrário de todos os outros armazenamentos, o Disco Ultra não pode ser usado no disco VHD de base. O Disco Ultra é ideal para casos em que a carga de trabalho de E/S flutua muito e você deseja adaptar a taxa de transferência de armazenamento implantada ou a IOPS aos padrões de carga de trabalho de armazenamento, em vez de dimensionar para uso máximo de largura de banda e IOPS.


## <a name="azure-netapp-files-anf"></a>ANF (Azure NetApp Files)
O [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) é o resultado de uma cooperação entre a Microsoft e a NetApp com a meta de fornecer compartilhamentos SMB e NFS nativos do Azure de alto desempenho. A ênfase é fornecer um armazenamento de baixa latência e de alta largura de banda que possibilite cenários de implantação de DBMS e, ao longo do tempo, permita também a funcionalidade operacional típica do armazenamento do NetApp por meio do Azure. Os compartilhamentos NFS/SMB são oferecidos em três níveis de serviço com diferentes preços e taxas de transferência de armazenamento. Os níveis de serviço são documentados no artigo [Níveis de serviço do Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-service-levels.md). Para diferentes tipos de carga de trabalho do SAP, os seguintes níveis de serviço são altamente recomendados:

- Carga de trabalho do SAP DBMS: desempenho, idealmente Ultra
- Compartilhamento SAPMNT: desempenho, idealmente Ultra
- Diretório de transporte global: desempenho, idealmente Ultra

> [!NOTE]
> O tamanho mínimo de provisionamento é uma unidade de 4 TiB que é chamada de pool de capacidade. Em seguida, crie volumes provenientes desse pool de capacidade. O menor volume que você pode criar é 100 GiB. Entretanto, você pode expandir um pool de capacidade nas etapas do TiB. Para obter os preços, veja o artigo [Preço do Azure NetApp Files](https://azure.microsoft.com/pricing/details/netapp/)

Atualmente, o armazenamento do ANF tem suporte em vários cenários de carga de trabalho do SAP:

- Como fornecer compartilhamentos SMB ou NFS para o diretório de transporte global do SAP
- O compartilhamento sapmnt em cenários de alta disponibilidade, conforme documentado em:
    - [Alta disponibilidade do SAP NetWeaver em VMs do Azure no Windows com o Azure NetApp Files (SMB) para aplicativos SAP](./high-availability-guide-windows-netapp-files-smb.md)
    - [Alta disponibilidade do SAP NetWeaver em VMs do Azure no SUSE Linux Enterprise Server com o Azure NetApp Files para aplicativos SAP](./high-availability-guide-suse-netapp-files.md)
    - [Alta disponibilidade de Máquinas Virtuais do Azure para SAP NetWeaver no Red Hat Enterprise Linux com o Azure NetApp Files para aplicativos SAP](./high-availability-guide-rhel-netapp-files.md)
- As implantações do SAP HANA que usam os compartilhamentos NFS v4.1 para volumes /hana/data e /hana/log e/ou volumes do NFS v4.1 ou do NFS v3 para volumes /hana/shared, conforme documentado no artigo [Configurações de armazenamento de máquina virtual do Azure para SAP HANA](./hana-vm-operations-storage.md)

> [!NOTE]
> Nenhuma outra carga de trabalho do DBMS tem suporte para compartilhamentos NFS ou SMB baseados no Azure NetApp Files. Atualizações e alterações serão fornecidas se isso for alterado.

Como já ocorre no armazenamento Premium do Azure, um tamanho de taxa de transferência fixo ou linear por GB pode ser um problema quando você precisa cumprir alguns valores mínimos na taxa de transferência. Esse é o caso do SAP HANA. Com o ANF, esse problema pode se tornar mais nítido do que com o disco Premium do Azure. No caso do disco Premium do Azure, você pode pegar vários discos menores com uma taxa de transferência relativamente alta por GiB e distribuí-los para que eles sejam econômicos e tenham uma taxa de transferência maior em uma capacidade menor. Esse tipo de distribuição não funciona em compartilhamentos NFS ou SMB hospedados no ANF. Essa restrição resultou na implantação além da capacidade como:

- Para obter, por exemplo, uma taxa de transferência de 250 MiB/s em um volume NFS hospedado no ANF, você precisará implantar a capacidade de 1,95 TiB do nível de serviço Ultra. 
- Para obter 400 MiB/s, você precisará implantar a capacidade de 3.125 TiB. Mas talvez você precise superprovisionar a capacidade para atingir a taxa de transferência necessária do volume. Esse excesso de provisionamento de capacidade afeta o preço das instâncias menores do HANA. 
- Em vez de usar o NFS com o ANF no diretório /sapmnt do SAP, normalmente você está indo muito bem com a capacidade mínima de 100 GiB a 150 GiB que é imposta pelo Azure NetApp Files. No entanto, a experiência do cliente mostrou que a taxa de transferência relacionada de 12,8 MiB/s (usando o nível de serviço Ultra) pode não ser suficiente e ter um impacto negativo na estabilidade do sistema SAP. Nesses casos, os clientes podem evitar problemas aumentando o volume do volume /sapmnt, para que mais taxa de transferência seja fornecida para ele.

A matriz de capacidade da carga de trabalho do SAP é semelhante a:

| Funcionalidade| Comentário| Observações/links | 
| --- | --- | --- | 
| VHD de base do sistema operacional | não funciona | - |
| Disco de dados | adequado | Somente SAP HANA  |
| Diretório de transporte global do SAP | YES | SMB e NFS |
| sapmnt do SAP | adequado | todos os sistemas com SMB (somente Windows) ou NFS (somente Linux) |
| Armazenamento de backup | adequado | - |
| Compartilhamentos/disco compartilhado | YES | SMB 3.0, NFS v3 e NFS v4.1 |
| Resiliência | LRS | Nenhum GRS ou ZRS disponível para discos |
| Latency | muito baixo | - |
| SLA de IOPS | YES | - |
| IOPS linear com a capacidade | estritamente linear  | Depende do [nível de serviço](../../../azure-netapp-files/azure-netapp-files-service-levels.md) |
| SLA de taxa de transferência | YES | - |
| Taxa de transferência linear com a capacidade | semilinear entre colchetes | Depende do [nível de serviço](../../../azure-netapp-files/azure-netapp-files-service-levels.md) |
| Certificado pelo HANA | YES | - |
| Instantâneos de disco possíveis | YES | - |
| Instantâneos de VM do Backup do Azure possíveis | Não | - |
| Custos | Maior do que o armazenamento Premium | - |


Funcionalidade interna adicional do armazenamento do ANF:

- Capacidade de executar instantâneos do volume
- Clonagem de volumes do ANF por meio de instantâneos
- Restauração de volumes de instantâneos (snap-revert)

**Resumo**: o Azure NetApp Files é um armazenamento de baixa latência certificado por HANA que permite implantar volumes ou compartilhamentos NFS e SMB. O armazenamento tem três níveis de serviço diferentes que fornecem taxa de transferência e IOPS distintas de maneira linear por capacidade de GiB do volume. O armazenamento ANF está habilitando a implantação de cenários de expansão do SAP HANA com um nó em espera. O armazenamento é adequado para fornecer compartilhamentos de arquivos conforme necessário para o diretório de transporte global SAP ou /sapmnt. O armazenamento ANF é fornecido com a disponibilidade de funcionalidade que está disponível como uma funcionalidade nativa do NetApp.  



## <a name="azure-standard-ssd-storage"></a>Armazenamento SSD Standard do Azure
Em comparação com o armazenamento HDD Standard do Azure, o armazenamento SSD Standard do Azure oferece melhor disponibilidade, consistência, confiabilidade e latência. Ele é otimizado para cargas de trabalho que precisam de desempenho consistente em níveis de IOPS mais baixos. Esse armazenamento é o armazenamento mínimo usado para sistemas SAP que não são de produção que têm baixas demandas de taxa de transferência e de IOPS. A matriz de capacidade da carga de trabalho do SAP é semelhante a:

| Funcionalidade| Comentário| Observações/links | 
| --- | --- | --- | 
| VHD de base do sistema operacional | adequado, mas restrito | sistemas de não produção |
| Disco de dados | adequado, mas restrito | alguns sistemas que não são de produção com baixas demandas de IOPS e de latência |
| Diretório de transporte global do SAP | Não | [Sem suporte](https://launchpad.support.sap.com/#/notes/2015553) |
| sapmnt do SAP | adequado, mas restrito | sistemas de não produção |
| Armazenamento de backup | adequado | - |
| Compartilhamentos/disco compartilhado | não disponível | Precisa de terceiros |
| Resiliência | LRS, GRS | Nenhum ZRS disponível para discos |
| Latency | high | muito alto para o Diretório de Transporte Global do SAP ou para sistemas de produção |
| SLA de IOPS | Não | - |
| IOPS máxima por disco | 500 | Independente do tamanho do disco |
| SLA de taxa de transferência | Não | - |
| Certificado pelo HANA | Não | - |
| Instantâneos de disco possíveis | YES | - |
| Instantâneos de VM do Backup do Azure possíveis | YES | - |
| Custos | LOW | - |



**Resumo**: o armazenamento SSD Standard do Azure é a recomendação mínima para VMs que não são de produção para o VHD de base, implantações eventuais de DBMS com insensibilidade à latência relativa e/ou baixas taxas de IOPS e de transferência. Esse tipo de armazenamento do Azure não tem mais suporte para hospedar o Diretório de Transporte Global SAP. 



## <a name="azure-standard-hdd-storage"></a>Armazenamento HDD Standard do Azure
O armazenamento HDD Standard do Azure era o único tipo de armazenamento disponível quando a infraestrutura do Azure obteve a certificação para a carga de trabalho do SAP NetWeaver no ano de 2014. Em 2014, as máquinas virtuais do Azure eram pequenas e tinham taxas de transferência de armazenamento baixas. Portanto, esse tipo de armazenamento conseguia apenas acompanhar as demandas. O armazenamento é ideal para cargas de trabalho sem sensibilidade de latência, que você dificilmente experimenta no espaço do SAP. Com maiores taxas de transferência de VMs do Azure e maior carga de trabalho produzida por essas VMs, esse tipo de armazenamento não é mais usado com cenários de SAP. A matriz de capacidade da carga de trabalho do SAP é semelhante a:

| Funcionalidade| Comentário| Observações/links | 
| --- | --- | --- | 
| VHD de base do sistema operacional | não é adequado | - |
| Disco de dados | não é adequado | - |
| Diretório de transporte global do SAP | Não | [Sem suporte](https://launchpad.support.sap.com/#/notes/2015553) |
| sapmnt do SAP | Não | Sem suporte |
| Armazenamento de backup | adequado | - |
| Compartilhamentos/disco compartilhado | não disponível | Precisa de Arquivos do Azure ou de terceiros |
| Resiliência | LRS, GRS | Nenhum ZRS disponível para discos |
| Latency | high | muito alto para uso de DBMS, Diretório de Transporte Global do SAP ou sapmnt/saploc |
| SLA de IOPS | Não | - |
| IOPS máxima por disco | 500 | Independente do tamanho do disco |
| SLA de taxa de transferência | Não | - |
| Certificado pelo HANA | Não | - |
| Instantâneos de disco possíveis | YES | - |
| Instantâneos de VM do Backup do Azure possíveis | YES | - |
| Custos | LOW | - |



**Resumo**: HDD Standard é um tipo de armazenamento do Azure que só deve ser usado para armazenar backups do SAP. Ele só deve ser usado como um VHD base para sistemas inativos, como sistemas descontinuados usados para pesquisar dados aqui e lá. Porém, nenhuma VM de desenvolvimento ativo, garantia de qualidade ou produção deve ser baseada nesse armazenamento. Os arquivos de banco de dados também não devem ser hospedados no armazenamento


## <a name="azure-vm-limits-in-storage-traffic"></a>Limites de VM do Azure no tráfego de armazenamento
Ao contrário dos cenários locais, o tipo de VM individual que você está selecionando desempenha uma função vital na largura de banda de armazenamento que você pode obter. Para os diferentes tipos de armazenamento, você precisa considerar:

| Tipo de armazenamento| Linux | Windows | Comentários |
| --- | --- | --- | --- |
| HDD Standard | [Tamanhos de VMs do Linux no Azure](../../sizes.md) | [Tamanhos de VMs do Windows no Azure](../../sizes.md) | Provavelmente é difícil alterar os limites de armazenamento de VMs médias ou grandes |
| SSD Standard | [Tamanhos de VMs do Linux no Azure](../../sizes.md) | [Tamanhos de VMs do Windows no Azure](../../sizes.md) | Provavelmente é difícil alterar os limites de armazenamento de VMs médias ou grandes |
| Armazenamento Premium | [Tamanhos de VMs do Linux no Azure](../../sizes.md) | [Tamanhos de VMs do Windows no Azure](../../sizes.md) | Limites de VM de taxa de transferência de armazenamento e de IOPS fáceis de alcançar com a configuração de armazenamento |
| Armazenamento de Disco Ultra | [Tamanhos de VMs do Linux no Azure](../../sizes.md) | [Tamanhos de VMs do Windows no Azure](../../sizes.md) | Limites de VM de taxa de transferência de armazenamento e de IOPS fáceis de alcançar com a configuração de armazenamento |
| Azure NetApp Files | [Tamanhos de VMs do Linux no Azure](../../sizes.md) | [Tamanhos de VMs do Windows no Azure](../../sizes.md) | O tráfego de armazenamento está usando largura de banda de taxa de transferência de rede e não a largura de banda de armazenamento. |

Como limitações, você pode observar que:

- Quanto menor a VM, menos discos você pode anexar. Isso não se aplica a ANF. Como você monta compartilhamentos NFS ou SMB, não encontra um limite de número de volumes compartilhados a serem anexados
- As VMs têm limites de taxa de transferência de E/S e IOPS que podem ser excedidos facilmente com discos de armazenamento Premium e Discos Ultra
- Com o seja, o tráfego para os volumes compartilhados está consumindo a largura de banda da rede da VM, e não a largura de banda de armazenamento
- Com grandes volumes NFS no espaço de capacidade TiB de dois dígitos, a taxa de transferência que acessa esse volume fora de uma só VM atingirá um platô com base nos limites do Linux para uma só sessão que interage com o volume compartilhado. 

Ao dimensionar as VMs do Azure no ciclo de vida de um sistema SAP, você deve avaliar os limites de taxa de transferência de IOPS e armazenamento do tipo de VM novo e maior. Em alguns casos, também poderia fazer sentido ajustar a configuração de armazenamento para as novas funcionalidades da VM do Azure. 


## <a name="striping-or-not-striping"></a>Distribuição ou não distribuição
A criação de um conjunto de distribuição de vários discos do Azure em um volume maior permite que você acumule a IOPS e a taxa de transferência dos discos individuais em um volume. Ele é usado somente para Armazenamento Standard do Azure e armazenamento Premium do Azure. O Disco Ultra do Azure, no qual você pode configurar a taxa de transferência e IOPS independentes da capacidade de um disco, não requer o uso de conjuntos de distribuição. Volumes compartilhados baseados em NFS ou SMB não podem ser distribuídos. Devido à natureza não linear da taxa de transferência e da IOPS do armazenamento Premium do Azure, você pode provisionar capacidade menor com a mesma IOPS e taxa de transferência do que grandes discos únicos de armazenamento Premium do Azure. Esse é o método para obter uma taxa de transferência maior ou IOPS com custo mais baixo usando o armazenamento Premium do Azure. Por exemplo, a distribuição entre dois discos de armazenamento P15 Premium faz com que você tenha uma taxa de transferência de: 

- 250 MiB/s. Esse volume terá uma capacidade de 512 GiB. Se você quiser ter um disco que forneça uma taxa de transferência de 250 MiB por segundo, precisará escolher um disco P40 com capacidade de 2 TiB. 
- 400 MiB/s distribuindo quatro discos de armazenamento Premium P10 com uma capacidade geral de 512 GiB por distribuição. Se você quiser ter um disco com uma taxa de transferência mínima de 500 MiB por segundo, precisará escolher um disco de armazenamento Premium P60 com 8 TiB. Como o custo do armazenamento Premium é quase linear com a capacidade, você pode perceber a economia de custos usando a distribuição.

Algumas regras precisam ser seguidas na distribuição:

- Nenhum armazenamento configurado na VM deve ser usado, já que o armazenamento do Azure mantém os dados redundantes
- Os discos aos quais o conjunto de distribuição é aplicado precisam ter o mesmo tamanho

A distribuição em vários discos menores é a melhor maneira de atingir um bom preço/taxa de desempenho usando o armazenamento Premium do Azure. É entendido que a distribuição tem alguma sobrecarga adicional de gerenciamento e implantação.

Para obter recomendações específicas de tamanho de distribuição, leia a documentação do DBMS diferente, como [Configurações de armazenamento de máquina virtual do Azure para SAP HANA](./hana-vm-operations-storage.md).




## <a name="next-steps"></a>Próximas etapas
Leia os artigos:

- [Considerações para Implantação do DBMS de Máquinas Virtuais do Azure para carga de trabalho do SAP](./dbms_guide_general.md)
- [Configurações de armazenamento de máquina virtual do SAP HANA no Azure](./hana-vm-operations-storage.md)
