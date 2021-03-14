---
title: Tipos de armazenamento do Azure para carga de trabalho do SAP
description: Planejando tipos de armazenamento do Azure para cargas de trabalho do SAP
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
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101673741"
---
# <a name="azure-storage-types-for-sap-workload"></a>Tipos de Armazenamento do Azure para carga de trabalho SAP
O Azure tem vários tipos de armazenamento que diferem amplamente em recursos, taxa de transferência, latência e preços. Alguns dos tipos de armazenamento não são, ou do uso limitado para cenários SAP. Enquanto que vários tipos de armazenamento do Azure são adequados ou otimizados para cenários de carga de trabalho SAP específicos. Especialmente para SAP HANA, alguns tipos de armazenamento do Azure receberam certificação para o uso com SAP HANA. Neste documento, vamos percorrer os diferentes tipos de armazenamento e descrever sua capacidade e usabilidade com cargas de trabalho do SAP e componentes SAP.

Comentário sobre as unidades usadas em todo este artigo. Os fornecedores de nuvem pública mudaram para usar GiB ([Gibibyte](https://en.wikipedia.org/wiki/Gibibyte)) ou TIB ([tebibyte](https://en.wikipedia.org/wiki/Tebibyte) como unidades de tamanho, em vez de Gigabyte ou terabyte. Portanto, todas as documentações e premiação do Azure estão usando essas unidades.  Em todo o documento, estamos fazendo referência a essas unidades de tamanho das unidades MiB, GiB e TiB exclusivamente. Talvez seja necessário planejar com MB, GB e TB. Portanto, esteja ciente de algumas pequenas diferenças nos cálculos se você precisar dimensionar para uma taxa de transferência de 400 MiB/s, em vez de uma taxa de transferência de 250 MiB/s.

## <a name="microsoft-azure-storage-resiliency"></a>Resiliência do Armazenamento do Microsoft Azure

Microsoft Azure armazenamento de HDD Standard, SSD Standard, armazenamento Premium do Azure e ultra Disk mantém o VHD base (com o sistema operacional) e os discos de dados anexados à VM ou VHDs em três cópias em três nós de armazenamento diferentes. O failover para outra réplica e a propagação de uma nova réplica no caso de uma falha de nó de armazenamento é transparente. Como resultado dessa redundância, **não** é necessário usar qualquer tipo de camada de redundância de armazenamento em vários discos do Azure. Esse fato é chamado de Armazenamento com Redundância Local (LRS). LRS é o padrão para esses tipos de armazenamento no Azure. [Azure NetApp files](https://azure.microsoft.com/services/netapp/) fornece redundância suficiente para alcançar os mesmos SLAs que o armazenamento do Azure nativo.

Há vários outros métodos de redundância, que são todos descritos no artigo replicação de [armazenamento do Azure](../../../storage/common/storage-redundancy.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) que se aplicam a alguns dos diferentes tipos de armazenamento que o Azure tem a oferecer. 

Lembre-se também de que diferentes tipos de armazenamento do Azure influenciam os SLAs de disponibilidade de VM única lançados em [SLA para máquinas virtuais](https://azure.microsoft.com/support/legal/sla/virtual-machines).

### <a name="azure-managed-disks"></a>Managed disks do Azure

Managed disks são um tipo de recurso no Azure Resource Manager que pode ser usado em vez de VHDs que são armazenados em contas de armazenamento do Azure. Managed Disks alinhar automaticamente com o [conjunto de disponibilidade] [Virtual-Machines-Manage-Availability] da máquina virtual à qual eles estão anexados e, portanto, aumentar a disponibilidade de sua máquina virtual e os serviços que estão em execução na máquina virtual. Para obter mais informações, leia o [artigo de visão geral](../../managed-disks-overview.md).

Relacionado à resiliência, este exemplo demonstra a vantagem dos Managed disks:

- Você está implantando suas duas VMs DBMS para seu sistema SAP em um conjunto de disponibilidade do Azure 
- À medida que o Azure implanta as VMs, o disco com a imagem do sistema operacional será colocado em um cluster de armazenamento diferente. Isso evita que ambas as VMs sejam afetadas por um problema de um único cluster de armazenamento do Azure
- À medida que você cria novos discos gerenciados que você atribui a essas VMs para armazenar os dados e os arquivos de log do seu banco, esses novos discos para as duas VMs também são implantados em clusters de armazenamento separados, portanto, que nenhum dos discos da primeira VM está compartilhando clusters de armazenamento com os discos da segunda VM

Implantando sem discos gerenciados nas contas de armazenamento definidas pelo cliente, a alocação do disco é arbitrária e não tem consciência do fato de que as VMs são implantadas dentro de um AvSet para fins de resiliência.

> [!NOTE]
> Fora desse motivo e de vários outros aprimoramentos que estão disponíveis exclusivamente por meio de discos gerenciados, exigimos que novas implantações de VMs que usam o armazenamento de blocos do Azure para seus discos (todo o armazenamento do Azure, exceto Azure NetApp Files) precisam usar o Azure Managed disks para os discos VHD/OS de base, discos de dados que contêm arquivos de banco de dados SAP. Independentemente de você implantar as VMs por meio do conjunto de disponibilidade, entre Zonas de Disponibilidade ou independentemente dos conjuntos e das zonas. Os discos que são usados com a finalidade de armazenar backups não são necessariamente necessários para serem discos gerenciados.

> [!NOTE]
> O Azure Managed disks fornece apenas LRS (redundância local). 


## <a name="storage-scenarios-with-sap-workloads"></a>Cenários de armazenamento com cargas de trabalho do SAP
O armazenamento persistente é necessário na carga de trabalho do SAP em vários componentes da pilha que você implanta no Azure. Esses cenários listam, no mínimo, como:

- Persistente o VHD de base da sua VM que contém o sistema operacional e outros softwares que você instala nesse disco. Este disco/VHD é a raiz de sua VM. Todas as alterações feitas a ele precisam ser persistidas. Assim, na próxima vez, você parar e reiniciar a VM, todas as alterações feitas antes ainda existem. Especialmente em casos em que a VM está sendo implantada pelo Azure em outro host do que estava em execução originalmente
- Discos de dados persistentes. Esses discos são VHDs anexados para armazenar dados de aplicativo. Esses dados de aplicativos podem ser dados e logs/refazer arquivos de um banco de dados, arquivos de backup ou instalações de software. Significa qualquer disco além do VHD de base que contém o sistema operacional
- Compartilhamentos de arquivos ou discos compartilhados que contêm seu diretório de transporte global para NetWeaver ou S/4HANA. O conteúdo desses compartilhamentos é consumido por software em execução em várias VMs ou é usado para criar cenários de cluster de failover de alta disponibilidade
- O diretório/sapmnt ou compartilhamentos de arquivos comuns para processos EDI ou semelhantes. O conteúdo desses compartilhamentos é consumido por software em execução em várias VMs ou é usado para criar cenários de cluster de failover de alta disponibilidade

Nas próximas seções, os diferentes tipos de armazenamento do Azure e sua usabilidade para a carga de trabalho do SAP são discutidos que se aplicam aos quatro cenários acima. Uma categorização geral de como os diferentes tipos de armazenamento do Azure devem ser usados está documentada no artigo [quais tipos de disco estão disponíveis no Azure?](../../disks-types.md). As recomendações para usar os diferentes tipos de armazenamento do Azure para carga de trabalho do SAP não serão muito diferentes.

Para obter restrições de suporte nos tipos de armazenamento do Azure para o SAP NetWeaver/camada de aplicativo de S/4HANA, leia a [Nota de suporte do sap 2015553](https://launchpad.support.sap.com/#/notes/2015553) para obter SAP Hana os tipos de armazenamento do Azure certificados e com suporte, leia o artigo [SAP Hana configurações de armazenamento de máquina virtual do Azure](./hana-vm-operations-storage.md).

As seções que descrevem os diferentes tipos de armazenamento do Azure fornecerão mais informações sobre as restrições e as possibilidades usando o armazenamento com suporte do SAP. 

## <a name="storage-recommendations-for-sap-storage-scenarios"></a>Recomendações de armazenamento para cenários de armazenamento SAP
Antes de entrar nos detalhes, estamos apresentando o resumo e as recomendações já no início do documento. Enquanto os detalhes para os tipos específicos de armazenamento do Azure estão seguindo esta seção do documento. Resumindo as recomendações de armazenamento para os cenários de armazenamento SAP em uma tabela, ela tem a seguinte aparência:

| Cenário de uso | HDD Standard | SSD Standard | Armazenamento Premium | Disco Ultra | Azure NetApp Files |
| --- | --- | --- | --- | --- | --- |
| Disco do sistema operacional | Não é adequado |  restrito adequado (não Prod) | recomendável | Não é possível | Não é possível |
| Diretório de transporte global | sem suporte | sem suporte | recomendável | recomendável | recomendável |
| /sapmnt | Não é adequado | restrito adequado (não Prod) | recomendável | recomendável | recomendável |
| SAP HANA de volume de dados DBMS famílias de VMs M/Mv2 | sem suporte | sem suporte | recomendável | recomendável | recomendado<sup>2</sup> |
| Volume de log DBMS SAP HANA famílias de VM M/Mv2 | sem suporte | sem suporte | recomendado<sup>1</sup> | recomendável | recomendado<sup>2</sup> | 
| Volume de dados do DBMS SAP HANA famílias de VMs Esv3/Edsv4 | sem suporte | sem suporte | recomendável | recomendável | recomendado<sup>2</sup> |
| Volume de log DBMS SAP HANA famílias de VMs Esv3/Edsv4 | sem suporte | sem suporte | sem suporte | recomendável | recomendado<sup>2</sup> | 
| Volume de dados do DBMS não HANA | sem suporte | restrito adequado (não Prod) | recomendável | recomendável | sem suporte |
| Famílias de VMs não HANA M/Mv2 de volume de log do DBMS | sem suporte | restrito adequado (não Prod) | recomendado<sup>1</sup> | recomendável | sem suporte |
| Famílias de VMs não do HANA de volume de log de DBMS não-M/Mv2 | sem suporte | restrito adequado (não Prod) | adequado para carga de trabalho média | recomendável | sem suporte |


<sup>1</sup> com o uso do [Azure acelerador de gravação](../../how-to-enable-write-accelerator.md) para famílias de VM M/Mv2 para volumes de log/restauração de log <sup>2</sup> usando o seja requer/Hana/data, bem como/Hana/log para estar em seja 

Características que você pode esperar da lista de diferentes tipos de armazenamento, como:

| Cenário de uso | HDD Standard | SSD Standard | Armazenamento Premium | Disco Ultra | Azure NetApp Files |
| --- | --- | --- | --- | --- | --- |
| SLA de taxa de transferência/IOPS | não | não | sim | sim | sim |
| Leituras de latência | high | médio a alto | low | submilissegundo | submilissegundo |
| Gravações de latência | high | médio a alto  | baixo (sub-milissegundo<sup>1</sup>) | submilissegundo | submilissegundo |
| Com suporte do HANA | não | não | Sim<sup>1</sup> | sim | sim |
| Instantâneos de disco possíveis | sim | sim | sim | não | sim |
| Alocação de discos em diferentes clusters de armazenamento ao usar conjuntos de disponibilidade | por meio de discos gerenciados | por meio de discos gerenciados | por meio de discos gerenciados | tipo de disco sem suporte com VMs implantadas por meio de conjuntos de disponibilidade | Não<sup>3</sup> |
| Alinhado com Zonas de Disponibilidade | sim | sim | sim | sim | precisa de envolvimento da Microsoft |
| Redundância zonal | Não para discos gerenciados | Não para discos gerenciados | Não para discos gerenciados | não | não |
| Redundância geográfica | Não para discos gerenciados | Não para discos gerenciados | não | não | não |


<sup>1</sup> com uso de [acelerador de gravação do Azure](../../how-to-enable-write-accelerator.md) para famílias de VM M/Mv2 para volumes de log/restauração de log

<sup>2</sup> os custos dependem de IOPS e taxa de transferência provisionados

<sup>3</sup> a criação de diferentes pools de capacidade seja não garante a implantação de pools de capacidade em unidades de armazenamento diferentes


> [!IMPORTANT]
> Para obter menos de 1 milissegundo de latência de e/s usando Azure NetApp Files (seja), você precisa trabalhar com a Microsoft para organizar o posicionamento correto entre suas VMs e os compartilhamentos NFS com base em seja. Até agora, não há nenhum mecanismo em vigor que forneça uma proximidade automática entre uma VM implantada e os volumes de NFS hospedados em seja. Devido à configuração diferente das diferentes regiões do Azure, a latência de rede adicionada pode enviar por push a latência de e/s além de 1 milissegundo se a VM e o compartilhamento NFS não estiverem alocados na proximidade.


> [!IMPORTANT]
> Nenhum dos discos gerenciados baseados no armazenamento de blocos do Azure atualmente oferecido ou Azure NetApp Files oferece qualquer redundância zonal ou geográfica. Como resultado, você precisa certificar-se de que suas arquiteturas de alta disponibilidade e de recuperação de desastre não dependam de qualquer tipo de replicação de armazenamento nativo do Azure para esses discos gerenciados, os compartilhamentos de NFS ou SMB.


## <a name="azure-premium-storage"></a>Armazenamento Premium do Azure
O armazenamento do SSD Premium do Azure foi introduzido com o objetivo de fornecer:

* Latência de e/s baixa
* SLAs para IOPS e taxa de transferência
* Menos variabilidade na latência de e/s

Esse tipo de armazenamento destina-se a cargas de trabalho do DBMS, tráfego de armazenamento que exige baixa latência de milissegundos de dígito único, e os SLAs em IOPS e taxa de transferência de custo no caso do armazenamento Premium do Azure não são o volume de dados real armazenado em tais discos, mas a categoria de tamanho de um disco, independentemente da quantidade de dados armazenados no disco. Você também pode criar discos no armazenamento Premium que não estão mapeando diretamente para as categorias de tamanho mostradas no artigo [SSD Premium](../../disks-types.md#premium-ssd). As conclusões deste artigo são:

- O armazenamento é organizado em intervalos. Por exemplo, um disco no intervalo de 513 GiB a 1024 GiB capacidade compartilha os mesmos recursos e os mesmos custos mensais
- O IOPS por GiB não está controlando linear nas categorias de tamanho. Discos menores abaixo de 32 GiB têm taxas de IOPS maiores por GiB. Para discos além de 32 GiB a 1024 GiB, a taxa de IOPS por GiB está entre 4-5 IOPS por GiB. Para discos maiores de até 32.767 GiB, a taxa de IOPS por GiB está abaixo de 1
- A taxa de transferência de e/s para esse armazenamento não é linear com o tamanho da categoria de disco. Para discos menores, como a categoria entre 65 GiB e 128 GiB de capacidade, a taxa de transferência está em volta de 780KB/GiB. Enquanto para discos extremos grandes, como um disco de 32.767 GiB, a taxa de transferência está em cerca de 28KB/GiB
- Os SLAs de taxa de transferência e IOPS não podem ser alterados sem alterar a capacidade do disco


A matriz de recursos para a carga de trabalho do SAP é semelhante a:

| Funcionalidade| Comentário| Observações/links | 
| --- | --- | --- | 
| VHD de base do so | apropriados | todos os sistemas |
| Disco de dados | apropriados | todos os sistemas – [especialmente para SAP Hana](../../how-to-enable-write-accelerator.md) |
| Diretório de transporte global do SAP | YES | [Com suporte](https://launchpad.support.sap.com/#/notes/2015553) |
| Sapmnt SAP | apropriados | todos os sistemas |
| Armazenamento de backup | apropriados | para armazenamento de curto prazo de backups |
| Compartilhamentos/disco compartilhado | não disponível | Precisa de arquivos premium do Azure ou terceiros |
| Resiliência | LRS | Nenhum GRS ou ZRS disponível para discos |
| Latency | baixo para médio | - |
| SLA DE IOPS | YES | - |
| IOPS linear para capacidade | semilineares entre colchetes  | [Preço do disco gerenciado](https://azure.microsoft.com/pricing/details/managed-disks/) |
| IOPS máxima por disco | 20.000 [depende do tamanho do disco](https://azure.microsoft.com/pricing/details/managed-disks/) | Considere também [os limites da VM](../../sizes.md) |
| SLA de produtividade | YES | - |
| Taxa de transferência linear para a capacidade | semilineares entre colchetes | [Preço do disco gerenciado](https://azure.microsoft.com/pricing/details/managed-disks/) |
| Certificado pelo HANA | YES | [especialmente para SAP HANA](../../how-to-enable-write-accelerator.md) |
| Instantâneos de disco possíveis | YES | - |
| Instantâneos de VM de backup do Azure possíveis | YES | exceto para [acelerador de gravação](../../how-to-enable-write-accelerator.md) discos armazenados em cache  |
| Custos | MEDIUM | - |

O armazenamento Premium do Azure não atende SAP HANA KPIs de latência de armazenamento com os tipos de cache comuns oferecidos com o armazenamento Premium do Azure. Para atender aos KPIs de latência de armazenamento para SAP HANA gravações de log, você precisa usar o cache de Acelerador de Gravação do Azure, conforme descrito no artigo [habilitar acelerador de gravação](../../how-to-enable-write-accelerator.md). O Azure Acelerador de Gravação beneficia todos os outros sistemas DBMS para suas gravações de log de transações e refazer gravações de log. Portanto, é recomendável usá-lo em todas as implantações do SAP DBMS. Por SAP HANA, o uso do Azure Acelerador de Gravação em conjunto com o armazenamento Premium do Azure é obrigatório.



**Resumo:** O armazenamento Premium do Azure é um dos tipos de armazenamento do Azure recomendados para a carga de trabalho do SAP. Essa recomendação se aplica a sistemas de produção e não de produção. O armazenamento Premium do Azure é adequado para lidar com cargas de trabalho de banco de dados. O uso da Acelerador de Gravação do Azure vai melhorar substancialmente a latência de gravação em discos Premium do Azure. No entanto, para sistemas DBMS com altos IOPS e taxas de taxa de transferência, você precisa provisionar excessivamente a capacidade de armazenamento ou você precisa usar funcionalidades como os espaços de armazenamento do Windows ou gerenciadores de volume lógico no Linux para criar conjuntos de distribuição que oferecem a capacidade desejada em um lado, mas também a IOPS ou taxa de transferência necessária com melhor custo-benefício.


### <a name="azure-burst-functionality-for-premium-storage"></a>Funcionalidade de intermitência do Azure para armazenamento Premium
Para discos de armazenamento Premium do Azure menores ou iguais a 512 GiB em capacidade, a funcionalidade de intermitência é oferecida. A maneira exata de como funciona a intermitência de disco é descrita no artigo [intermitência de disco](../../disk-bursting.md). Ao ler o artigo, você entende o conceito de acumular IOPS e taxa de transferência nos momentos em que a carga de trabalho de e/s está abaixo do IOPS nominal e da taxa de transferência dos discos (para obter detalhes sobre a taxa de transferência nominal, consulte [preço do disco gerenciado](https://azure.microsoft.com/pricing/details/managed-disks/)). Você vai acumular o Delta de IOPS e a taxa de transferência entre o uso atual e os valores nominais do disco. As intermitências são limitadas a um máximo de 30 minutos.

Os casos ideais em que essa funcionalidade de intermitência pode ser planejada provavelmente serão os volumes ou discos que contêm arquivos de dados para o DBMS diferente. A carga de trabalho de e/s esperada nesses volumes, especialmente com sistemas de pequeno a médio porte, deve parecer com a seguinte aparência:

- Carga de trabalho de leitura baixa a moderada, pois os dados são idealmente armazenados em cache na memória, ou como no caso do HANA devem estar completamente na memória
- Picos de gravação disparadas por pontos de verificação de banco de dados ou ponto de salvamento emitidos regularmente
- Carga de trabalho de backup que lê em um fluxo contínuo em casos em que os backups não são executados por meio de instantâneos de armazenamento
- Por SAP HANA, carregar os dados na memória após uma reinicialização da instância

Especialmente em sistemas DBMS menores em que sua carga de trabalho está lidando com apenas algumas centenas de transações por segundo, uma funcionalidade de intermitência também pode fazer sentido para os discos ou volumes que armazenam a transação ou efetuar o log de refazer. A carga de trabalho esperada em um disco ou em volumes é semelhante a:

- Gravações regulares no disco que são dependentes da carga de trabalho e a natureza da carga de trabalho, pois cada confirmação emitida pelo aplicativo provavelmente disparam uma operação de e/s
- Maior carga de trabalho na taxa de transferência para casos de tarefas operacionais, como criar ou recriar índices
- Ler intermitências ao executar o log de transações ou fazer backups de log de restauração


## <a name="azure-ultra-disk"></a>Ultra Disk do Azure
Os discos Ultra do Azure proporcionam uma alta taxa de transferência, alta IOPS e armazenamento em disco de baixa latência e consistente para as VMs de IaaS do Azure. Alguns benefícios adicionais de ultra discos incluem a capacidade de alterar dinamicamente a IOPS e a taxa de transferência do disco, juntamente com suas cargas de trabalho, sem a necessidade de reiniciar suas máquinas virtuais (VM). Ultra discos são adequados para cargas de trabalho com uso intensivo de dados, como carga de trabalho do DBMS SAP. Ultra discos só podem ser usados como discos de dados e não podem ser usados como disco VHD de base que armazena o sistema operacional. Recomendamos o uso do armazenamento Premium do Azure como disco VHD com base.

Ao criar um ultra Disk, você tem três dimensões que pode definir:

- A capacidade do disco. Os intervalos são de 4 GiB a 65.536 GiB
- IOPS provisionado para o disco. Valores máximos diferentes se aplicam à capacidade do disco. Leia o artigo [ultra Disk](../../disks-types.md#ultra-disk) para obter mais detalhes
- Largura de banda de armazenamento provisionada. A largura de banda máxima diferente se aplica de acordo com a capacidade do disco. Leia o artigo [ultra Disk](../../disks-types.md#ultra-disk) para obter mais detalhes

O custo de um único disco é determinado pelas três dimensões que você pode definir para os discos específicos separadamente. 


A matriz de recursos para a carga de trabalho do SAP é semelhante a:

| Funcionalidade| Comentário| Observações/links | 
| --- | --- | --- | 
| VHD de base do so | Não funciona | - |
| Disco de dados | apropriados | todos os sistemas  |
| Diretório de transporte global do SAP | YES | [Com suporte](https://launchpad.support.sap.com/#/notes/2015553) |
| Sapmnt SAP | apropriados | todos os sistemas |
| Armazenamento de backup | apropriados | para armazenamento de curto prazo de backups |
| Compartilhamentos/disco compartilhado | não disponível | Precisa de terceiros |
| Resiliência | LRS | Nenhum GRS ou ZRS disponível para discos |
| Latency | muito baixo | - |
| SLA DE IOPS | YES | - |
| IOPS linear para capacidade | semilineares entre colchetes  | [Preço do disco gerenciado](https://azure.microsoft.com/pricing/details/managed-disks/) |
| IOPS máxima por disco | 1.200 a 160.000 | dependente da capacidade do disco |
| SLA de produtividade | YES | - |
| Taxa de transferência linear para a capacidade | semilineares entre colchetes | [Preço do disco gerenciado](https://azure.microsoft.com/pricing/details/managed-disks/) |
| Certificado pelo HANA | YES | - |
| Instantâneos de disco possíveis | Não | - |
| Instantâneos de VM de backup do Azure possíveis | Não | - |
| Custos | Maior do que o armazenamento Premium | - |



**Resumo:** Os ultra discos do Azure são um armazenamento adequado com baixa latência para todos os tipos de carga de trabalho do SAP. Até agora, o ultra Disk só pode ser usado em combinações com VMs que foram implantadas por meio de Zonas de Disponibilidade (implantação zonal). O ultra Disk não dá suporte a instantâneos de armazenamento neste momento. Ao contrário de todos os outros armazenamentos, o ultra Disk não pode ser usado para o disco VHD de base. Ultra Disk é ideal para casos em que a carga de trabalho de e/s flutua muito e você deseja adaptar a taxa de transferência de armazenamento implantada ou IOPS aos padrões de carga de trabalho de armazenamento em vez de dimensionar para uso máximo de largura de banda e IOPS.


## <a name="azure-netapp-files-anf"></a>Arquivos do Azure NetApp (seja)
[Azure NetApp files](https://azure.microsoft.com/services/netapp/) é o resultado de uma cooperação entre a Microsoft e a NetApp com o objetivo de fornecer alto desempenho do NFS nativo e compartilhamentos SMB. A ênfase é fornecer alta largura de banda e armazenamento de baixa latência que permita cenários de implantação de DBMS e, ao longo do tempo, habilite também a funcionalidade operacional típica do armazenamento do NetApp por meio do Azure. Os compartilhamentos de NFS/SMB são oferecidos em três níveis de serviço diferentes que se diferenciam na taxa de transferência de armazenamento e no preço. Os níveis de serviço são documentados no artigo [níveis de serviço para Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-service-levels.md). Para os diferentes tipos de carga de trabalho do SAP, os seguintes níveis de serviço são altamente recomendados:

- Carga de trabalho do SAP DBMS: desempenho, idealmente ultra
- Compartilhamento SAPMNT: desempenho, idealmente ultra
- Diretório de transporte global: desempenho, idealmente ultra

> [!NOTE]
> O tamanho mínimo de provisionamento é uma unidade de 4 TiB que é chamada de pool de capacidade. Em seguida, você cria volumes fora desse pool de capacidade. Enquanto o menor volume que você pode compilar é 100 GiB. Você pode expandir um pool de capacidade em etapas do TiB. Para obter os preços, consulte o artigo [Azure NetApp files preços](https://azure.microsoft.com/pricing/details/netapp/)

Atualmente, o armazenamento seja tem suporte para vários cenários de carga de trabalho do SAP:

- Fornecendo compartilhamentos SMB ou NFS para o diretório de transporte global da SAP
- O compartilhamento sapmnt em cenários de alta disponibilidade, conforme documentado em:
    - [Alta disponibilidade para SAP NetWeaver em VMs do Azure no Windows com Azure NetApp Files (SMB) para aplicativos SAP](./high-availability-guide-windows-netapp-files-smb.md)
    - [Alta disponibilidade do SAP NetWeaver em VMs do Azure no SUSE Linux Enterprise Server com o Azure NetApp Files para aplicativos SAP](./high-availability-guide-suse-netapp-files.md)
    - [Alta disponibilidade de máquinas virtuais do Azure para SAP NetWeaver em Red Hat Enterprise Linux com Azure NetApp Files para aplicativos SAP](./high-availability-guide-rhel-netapp-files.md)
- SAP HANA implantações usando o NFS v 4.1 compartilhamentos para volumes/Hana/data e/Hana/log e/ou NFS v 4.1 ou NFS v3 para volumes/Hana/Shared, conforme documentado no artigo [SAP Hana configurações de armazenamento de máquina virtual do Azure](./hana-vm-operations-storage.md)

> [!NOTE]
> Nenhuma outra carga de trabalho DBMS tem suporte para compartilhamentos NFS ou SMB baseados em Azure NetApp Files. Atualizações e alterações serão fornecidas se isso for alterado.

Como já no armazenamento Premium do Azure, um tamanho de taxa de transferência fixo ou linear por GB pode ser um problema quando você precisa aderir a alguns números mínimos na taxa de transferência. Como esse é o caso para SAP HANA. Com o seja, esse problema pode se tornar mais pronunciado do que com o disco Premium do Azure. No caso do disco Premium do Azure, você pode pegar vários discos menores com uma taxa de transferência relativamente alta por GiB e distribuí-los para serem econômicos e ter maior taxa de transferência em uma capacidade menor. Esse tipo de distribuição não funciona para compartilhamentos NFS ou SMB hospedados em seja. Essa restrição resultou na implantação de supercapacidade como:

- Para obter, por exemplo, uma taxa de transferência de 250 MiB/s em um volume NFS hospedado em seja, você precisará implantar a capacidade 1,95 TiB do nível de serviço ultra. 
- Para obter 400 MiB/s, você precisará implantar a capacidade de 3,125 TiB. Mas talvez você precise do provisionamento excessivo da capacidade para atingir a taxa de transferência necessária do volume. Esse excesso de provisionamento de capacidade afeta o preço de instâncias menores do HANA. 
- No espaço de uso de NFS sobre seja para o diretório/sapmnt do SAP, normalmente você está indo muito bem com a capacidade mínima de 100 GiB para 150 GiB que é imposta pelo Azure NetApp Files. No entanto, a experiência do cliente mostrou que a taxa de transferência relacionada de 12,8 MiB/s (usando o nível de serviço ultra) pode não ser suficiente e pode ter um impacto negativo na estabilidade do sistema SAP. Nesses casos, os clientes podem evitar problemas aumentando o volume do volume/sapmnt, portanto, essa taxa de transferência é fornecida para esse volume.

A matriz de recursos para a carga de trabalho do SAP é semelhante a:

| Funcionalidade| Comentário| Observações/links | 
| --- | --- | --- | 
| VHD de base do so | Não funciona | - |
| Disco de dados | apropriados | Somente SAP HANA  |
| Diretório de transporte global do SAP | YES | SMB, bem como NFS |
| Sapmnt SAP | apropriados | todos os sistemas SMB (somente Windows) ou NFS (somente Linux) |
| Armazenamento de backup | apropriados | - |
| Compartilhamentos/disco compartilhado | YES | SMB 3,0, NFS v3 e NFS v 4.1 |
| Resiliência | LRS | Nenhum GRS ou ZRS disponível para discos |
| Latency | muito baixo | - |
| SLA DE IOPS | YES | - |
| IOPS linear para capacidade | estritamente linear  | Dependente do [nível de serviço](../../../azure-netapp-files/azure-netapp-files-service-levels.md) |
| SLA de produtividade | YES | - |
| Taxa de transferência linear para a capacidade | semilineares entre colchetes | Dependente do [nível de serviço](../../../azure-netapp-files/azure-netapp-files-service-levels.md) |
| Certificado pelo HANA | YES | - |
| Instantâneos de disco possíveis | YES | - |
| Instantâneos de VM de backup do Azure possíveis | Não | - |
| Custos | Maior do que o armazenamento Premium | - |


Funcionalidade interna adicional do armazenamento seja:

- Capacidade de executar instantâneos do volume
- Clonagem de volumes seja de instantâneos
- Restaurar volumes de instantâneos (snap-REVERT)

**Resumo**: Azure NetApp Files é um armazenamento de baixa latência certificado pelo Hana que permite implantar volumes e compartilhamentos de NFS e SMB. O armazenamento vem com três níveis de serviço diferentes que fornecem taxa de transferência e IOPS diferentes de maneira linear por capacidade de GiB do volume. O armazenamento seja está habilitando para implantar SAP HANA cenários de expansão com um nó em espera. O armazenamento é adequado para fornecer compartilhamentos de arquivos conforme necessário para o/sapmnt ou o diretório de transporte global do SAP. O armazenamento seja é fornecido com a disponibilidade de funcionalidade que está disponível como uma funcionalidade nativa do NetApp.  



## <a name="azure-standard-ssd-storage"></a>Armazenamento SSD standard do Azure
Em comparação com o armazenamento HDD standard do Azure, o armazenamento SSD standard do Azure oferece melhor disponibilidade, consistência, confiabilidade e latência. Ele é otimizado para cargas de trabalho que precisam de desempenho consistente em níveis de IOPS inferiores. Esse armazenamento é o armazenamento mínimo usado para sistemas SAP que não são de produção que têm baixa IOPS e demandas de taxa de transferência. A matriz de recursos para a carga de trabalho do SAP é semelhante a:

| Funcionalidade| Comentário| Observações/links | 
| --- | --- | --- | 
| VHD de base do so | restrito adequado | sistemas de não produção |
| Disco de dados | restrito adequado | alguns sistemas que não são de produção com baixas demandas de IOPS e latência |
| Diretório de transporte global do SAP | Não | [Sem suporte](https://launchpad.support.sap.com/#/notes/2015553) |
| Sapmnt SAP | restrito adequado | sistemas de não produção |
| Armazenamento de backup | apropriados | - |
| Compartilhamentos/disco compartilhado | não disponível | Precisa de terceiros |
| Resiliência | LRS, GRS | Nenhum ZRS disponível para discos |
| Latency | high | muito alto para o diretório de transporte global do SAP ou sistemas de produção |
| SLA DE IOPS | Não | - |
| IOPS máxima por disco | 500 | Independentemente do tamanho do disco |
| SLA de produtividade | Não | - |
| Certificado pelo HANA | Não | - |
| Instantâneos de disco possíveis | YES | - |
| Instantâneos de VM de backup do Azure possíveis | YES | - |
| Custos | LOW | - |



**Resumo:** O armazenamento SSD standard do Azure é a recomendação mínima para VMs que não são de produção para o VHD de base, implantações eventuals de DBMS com insensibilidade de latência relativa e/ou taxas de taxa de transferência e IOPS baixas. Esse tipo de armazenamento do Azure não tem mais suporte para hospedar o diretório de transporte global do SAP. 



## <a name="azure-standard-hdd-storage"></a>Armazenamento HDD standard do Azure
O armazenamento de HDD Standard do Azure era o único tipo de armazenamento quando a infraestrutura do Azure obteve a certificação para a carga de trabalho do SAP NetWeaver no ano de 2014. No ano 2014, as máquinas virtuais do Azure eram pequenas e baixas na taxa de transferência de armazenamento. Portanto, esse tipo de armazenamento foi capaz de apenas acompanhar as demandas. O armazenamento é ideal para cargas de trabalho sem distinção de latência, que você dificilmente experimenta no espaço SAP. Com a crescente taxa de transferência de VMs do Azure e a maior carga de trabalho que essas VMs estão produzindo, esse tipo de armazenamento não é mais considerado para o uso com cenários SAP. A matriz de recursos para a carga de trabalho do SAP é semelhante a:

| Funcionalidade| Comentário| Observações/links | 
| --- | --- | --- | 
| VHD de base do so | Não é adequado | - |
| Disco de dados | Não é adequado | - |
| Diretório de transporte global do SAP | Não | [Sem suporte](https://launchpad.support.sap.com/#/notes/2015553) |
| Sapmnt SAP | Não | Sem suporte |
| Armazenamento de backup | apropriados | - |
| Compartilhamentos/disco compartilhado | não disponível | Precisa de arquivos do Azure ou de terceiros |
| Resiliência | LRS, GRS | Nenhum ZRS disponível para discos |
| Latency | high | muito alto para uso de DBMS, diretório de transporte global do SAP ou sapmnt/saploc |
| SLA DE IOPS | Não | - |
| IOPS máxima por disco | 500 | Independentemente do tamanho do disco |
| SLA de produtividade | Não | - |
| Certificado pelo HANA | Não | - |
| Instantâneos de disco possíveis | YES | - |
| Instantâneos de VM de backup do Azure possíveis | YES | - |
| Custos | LOW | - |



**Resumo:** HDD Standard é um tipo de armazenamento do Azure que só deve ser usado para armazenar backups do SAP. Ele só deve ser usado como um VHD base para sistemas inativos, como sistemas descontinuados usados para pesquisar dados aqui e lá. Mas nenhuma VM de desenvolvimento ativo, QA ou produção deve ser baseada nesse armazenamento. Nem os arquivos de banco de dados estão sendo hospedados no armazenamento


## <a name="azure-vm-limits-in-storage-traffic"></a>Limites de VM do Azure no tráfego de armazenamento
Ao contrário dos cenários locais, o tipo de VM individual que você está selecionando desempenha uma função vital na largura de banda de armazenamento que você pode obter. Para os diferentes tipos de armazenamento, você precisa considerar:

| Tipo de armazenamento| Linux | Windows | Comentários |
| --- | --- | --- | --- |
| HDD Standard | [Tamanhos para VMs do Linux no Azure](../../sizes.md) | [Tamanhos de VMs do Windows no Azure](../../sizes.md) | Provavelmente é difícil tocar nos limites de armazenamento de VMs médias ou grandes |
| SSD Standard | [Tamanhos para VMs do Linux no Azure](../../sizes.md) | [Tamanhos de VMs do Windows no Azure](../../sizes.md) | Provavelmente é difícil tocar nos limites de armazenamento de VMs médias ou grandes |
| Armazenamento Premium | [Tamanhos para VMs do Linux no Azure](../../sizes.md) | [Tamanhos de VMs do Windows no Azure](../../sizes.md) | IOPS fácil de acessar ou limites de VM de taxa de transferência de armazenamento com a configuração de armazenamento |
| Armazenamento de ultra Disk | [Tamanhos para VMs do Linux no Azure](../../sizes.md) | [Tamanhos de VMs do Windows no Azure](../../sizes.md) | IOPS fácil de acessar ou limites de VM de taxa de transferência de armazenamento com a configuração de armazenamento |
| Azure NetApp Files | [Tamanhos para VMs do Linux no Azure](../../sizes.md) | [Tamanhos de VMs do Windows no Azure](../../sizes.md) | O tráfego de armazenamento está usando largura de banda de produtividade de rede e não largura de banda de armazenamento |

Como limitações, você pode observar que:

- Quanto menor a VM, menos discos você pode anexar. Isso não se aplica a seja. Como você monta compartilhamentos NFS ou SMB, não encontra um limite de número de volumes compartilhados a serem anexados
- As VMs têm limites de taxa de transferência de e/s e IOPS que podem ser excedidos facilmente com discos de armazenamento Premium e ultra discos
- Com o seja, o tráfego para os volumes compartilhados está consumindo a largura de banda da rede da VM e não a largura de banda de armazenamento
- Com grandes volumes NFS no espaço de capacidade TiB de dígito duplo, a taxa de transferência que acessa esse volume fora de uma única VM vai limite com base nos limites do Linux para uma única sessão que interage com o volume compartilhado. 

Ao dimensionar as VMs do Azure no ciclo de vida de um sistema SAP, você deve avaliar os limites de taxa de transferência de IOPS e armazenamento do tipo de VM novo e maior. Em alguns casos, também poderia fazer sentido ajustar a configuração de armazenamento para os novos recursos da VM do Azure. 


## <a name="striping-or-not-striping"></a>Distribuição ou não distribuição
A criação de um conjunto de distribuição de vários discos do Azure em um volume maior permite que você acumule a IOPS e a taxa de transferência dos discos individuais em um volume. Ele é usado somente para armazenamento standard do Azure e armazenamento Premium do Azure. O ultra Disk do Azure, no qual você pode configurar a taxa de transferência e IOPS independentes da capacidade de um disco, não requer o uso de conjuntos de distribuição. Volumes compartilhados baseados em NFS ou SMB não podem ser distribuídos. Devido à natureza não linear da taxa de transferência e do IOPS do armazenamento Premium do Azure, você pode provisionar capacidade menor com a mesma IOPS e taxa de transferência do que grandes discos únicos de armazenamento Premium do Azure. Esse é o método para obter uma taxa de transferência maior ou IOPS com custo mais baixo usando o armazenamento Premium do Azure. Por exemplo, a distribuição entre dois discos de armazenamento P15 Premium leva a uma taxa de transferência de: 

- 250 MiB/s. Esse volume terá 512 GiB de capacidade. Se você quiser ter um único disco que fornece 250 de taxa de transferência de MiB por segundo, precisaria escolher um disco P40 com 2 TiB de capacidade. 
- 400 MiB/s por meio da distribuição de quatro discos de armazenamento Premium P10 com uma capacidade geral de 512 GiB por distribuição. Se você quiser ter um único disco com um mínimo de 500 de taxa de transferência MiB por segundo, precisaria escolher um disco de armazenamento Premium P60 com 8 TiB. Como o custo do armazenamento Premium é quase linear com a capacidade, você pode perceber a economia de custos usando a distribuição.

Algumas regras precisam ser seguidas na distribuição:

- Nenhum armazenamento configurado na VM deve ser usado, já que o armazenamento do Azure mantém os dados redundantes
- Os discos aos quais o conjunto de distribuição é aplicado, precisam ter o mesmo tamanho

A distribuição em vários discos menores é a melhor maneira de atingir um bom preço/taxa de desempenho usando o armazenamento Premium do Azure. É entendido que a distribuição tem alguma sobrecarga adicional de gerenciamento e implantação.

Para obter recomendações de tamanho de distribuição específicas, leia a documentação do DBMS diferente, como [SAP Hana configurações de armazenamento de máquina virtual do Azure](./hana-vm-operations-storage.md).




## <a name="next-steps"></a>Próximas etapas
Leia os artigos:

- [Considerações para Implantação do DBMS de Máquinas Virtuais do Azure para carga de trabalho do SAP](./dbms_guide_general.md)
- [Configurações de armazenamento de máquina virtual do SAP HANA no Azure](./hana-vm-operations-storage.md)
