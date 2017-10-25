---
title: Armazenamento Premium de alto desempenho e discos gerenciados do Azure para VMs | Microsoft Docs
description: "Saiba mais sobre o Armazenamento Premium de alto desempenho e discos gerenciados para VMs do Azure. As VMs das séries DS, DSv2, GS e Fs do Azure são compatíveis com o Armazenamento Premium."
services: storage
documentationcenter: 
author: ramankumarlive
manager: aungoo-msft
editor: tysonn
ms.assetid: e2a20625-6224-4187-8401-abadc8f1de91
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: ramankum
ms.openlocfilehash: 559753d7ff3da7e8b5dc8f976368a1d35dd6258b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="high-performance-premium-storage-and-managed-disks-for-vms"></a>Armazenamento Premium de alto desempenho e discos gerenciados para VMs
O Armazenamento Premium do Azure dá suporte de disco de alto desempenho e baixa latência para máquinas virtuais (VMs) com cargas de trabalho com uso intensivo de E/S (entrada/saída). Os discos de VM que usam Armazenamento Premium armazenam dados em SSDs (unidades de estado sólido). Para tirar proveito da velocidade e do desempenho dos discos de armazenamento premium, você pode migrar os discos de VM existentes para o Armazenamento Premium.

No Azure, você pode anexar vários discos de armazenamento premium para uma VM. Usar vários discos dá aos seus aplicativos até 256 TB de armazenamento por VM. Com o Armazenamento Premium, seus aplicativos podem obter 80.000 operações de E/S por segundo (IOPS) por VM e uma taxa de transferência de disco de até 2.000 megabytes por segundo (MB/s) por VM. As operações de leitura oferecem muito menos latências.

Com o armazenamento Premium, o Azure oferece a capacidade de realmente deslocar os exigentes aplicativos empresariais, como farms Dynamics AX, Dynamics CRM, Exchange Server, SAP Business Suite e SharePoint para a nuvem. Você pode executar cargas de trabalho de banco de dados de alto desempenho em aplicativos como o SQL Server, Oracle, MongoDB, MySQL e Redis, que exigem alto desempenho consistente e baixa latência.

> [!NOTE]
> Para obter o melhor desempenho para o seu aplicativo, é recomendável que você migre qualquer disco VM que requer alta IOPS para o Armazenamento Premium. Se o disco não requer IOPS alta, você pode ajudar a limitar os custos mantendo o armazenamento padrão do Azure. No armazenamento padrão, dados de disco VM são armazenados em unidades de disco rígido (HDD) em vez de SSDs.
> 

O Azure oferece duas maneiras de criar discos de armazenamento premium para VMs:

* **Discos não gerenciados**

    O método original é usar discos não gerenciados. Em um disco não gerenciado, você gerencia contas de armazenamento que você pode usar para armazenar os arquivos de disco rígido virtual (VHD) que correspondem aos seus discos de VM. Os arquivos VHD são armazenados como blobs de páginas nas contas de armazenamento do Azure. 

* **Discos gerenciados**

    Quando você escolhe [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md), o Azure gerencia contas de armazenamento que você pode usar para os discos de VM. Especifique o tipo de disco (Premium ou Standard) e o tamanho do disco de que você precisa. O Azure cria e gerencia o disco para você. Você não precisa se preocupar sobre onde colocar os discos em várias contas de armazenamento para garantir que esteja dentro dos limites de escalabilidade para suas contas de armazenamento. O Azure trata disso para você.

É recomendável que você escolha discos gerenciados, para tirar proveito de seus muitos recursos.

Introdução ao Armazenamento Premium, [Criar sua conta gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 

Para obter informações sobre como migrar suas VMs existentes para o Armazenamento Premium, confira [Converter uma VM do Windows de discos não gerenciados para discos gerenciados](../../virtual-machines/windows/convert-unmanaged-to-managed-disks.md) ou [Converter uma VM do Linux de discos não gerenciados para discos gerenciados](../../virtual-machines/linux/convert-unmanaged-to-managed-disks.md).

> [!NOTE]
> O Armazenamento Premium está disponível na maioria das regiões. Para obter a lista de regiões disponíveis, em [Serviços do Azure por região](https://azure.microsoft.com/regions/#services), examine as regiões em que há suporte para as VMs da série de tamanho Premium (VMs da série DS, série DSV2, série GS e série Fs).
> 

## <a name="features"></a>Recursos

Aqui estão alguns dos recursos de Armazenamento Premium:

* **Discos de Armazenamento Premium**

    O Armazenamento Premium dá suporte a discos VM que podem ser anexados a VMs da série de tamanho específico. O Armazenamento Premium dá suporte às VMs das séries DS, DSv2, GS, Ls e Fs. Você tem sete opções de tamanho de disco: P4 (32 GB), P6 (64 GB), P10 (128 GB), P20 (512 GB), P30 (1024 GB), P40 (2048 GB), P50 (4095 GB). Tamanhos de disco P4 e P6 no momento têm suporte somente para o Managed Disks. Cada tamanho de disco tem suas próprias especificações de desempenho. Dependendo dos requisitos do aplicativo, você pode anexar um ou mais discos à VM. Descrevemos as especificações em mais detalhes em [Metas de desempenho e escalabilidade do Armazenamento Premium](#scalability-and-performance-targets).

* **Blobs de página Premium**

    O Armazenamento Premium dá suporte a blobs de página. Use blobs de página para armazenar discos persistentes não gerenciados para máquinas virtuais no Armazenamento Premium. Ao contrário do Armazenamento do Azure padrão, o Armazenamento Premium não oferece suporte a blobs de bloco, blobs de acréscimo, arquivos, tabelas ou filas. Os blobs da página Premium dá suporte a seis tamanhos, de P10 a P50 e P60 (8191GiB). O blob de páginas Premium P60 não tem suporte para ser anexado como disco de VM. 

    Qualquer objeto colocado em uma conta de armazenamento premium será um blob de páginas. O blob de páginas se encaixa em um dos tamanhos provisionados com suporte. É por isso que uma conta de armazenamento premium não se destina a ser usada para armazenar blobs pequenos.

* **Conta de Armazenamento Premium**

    Para começar a usar o Armazenamento Premium, crie uma conta de armazenamento premium para discos não gerenciados. No [portal do Azure](https://portal.azure.com), para criar uma conta de armazenamento premium, escolha o nível de desempenho **Premium**. Selecione a opção de replicação **Armazenamento localmente redundante (LRS)**. Você também pode criar uma conta de armazenamento premium, definindo o tipo como **Premium_LRS** em um dos seguintes locais:
    * [API REST de armazenamento](https://docs.microsoft.com/rest/api/storageservices/Azure-Storage-Services-REST-API-Reference) (versão 2014-02-14 ou posterior)
    * [API REST do Gerenciamento de Serviço](http://msdn.microsoft.com/library/azure/ee460799.aspx) (versão 2014-10-01 ou posterior; para implantações de clássicas do Azure)
    * [API REST do Azure do provedor de recursos de armazenamento](https://docs.microsoft.com/rest/api/storagerp) (para implantações do Azure Resource Manager)
    * [Azure PowerShell](/powershell/azureps-cmdlets-docs.md) (versão 0.8.10 ou posterior)

    Para obter informações sobre limites de conta de armazenamento premium, confira [Metas de desempenho e escalabilidade do Armazenamento Premium](#premium-storage-scalability-and-performance-targets).

* **Armazenamento com redundância local Premium**

    Uma conta de armazenamento premium dá suporte a armazenamento com redundância local somente como a opção de replicação. O armazenamento com redundância local mantém três cópias dos dados em uma única região. Para recuperação de desastres regionais, você deve fazer backup os discos VM em uma região diferente usando o [Backup do Azure](../../backup/backup-introduction-to-azure-backup.md). Você também deve usar uma conta de armazenamento com redundância geográfica (GRS) como o cofre de backup. 

    O Azure usa a conta de armazenamento como contêiner para seus discos não gerenciados. Quando você cria uma VM do Azure da série DS, série DSv2, série GS, ou série Fs com discos não gerenciados e seleciona uma conta de armazenamento premium, o sistema operacional e discos de dados são armazenados na conta de armazenamento.

## <a name="supported-vms"></a>VMs com suporte
O Armazenamento Premium dá suporte às VMs das séries DS, DSv2, GS, Ls e Fs. Você pode usar os discos do Armazenamento Standard e Premium com esses tipos de VM. Não é possível usar discos de Armazenamento Premium com séries de VM que não são compatíveis com o Armazenamento Premium.

Para obter informações sobre tipos de VM e tamanhos no Azure para Windows, confira [Tamanhos de VM do Windows](../../virtual-machines/virtual-machines-windows-sizes-storage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Para obter informações sobre tipos de VM e tamanhos no Azure para Linux, confira [Tamanhos de VM Linux](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Estes são alguns dos recursos das VMs das séries DS, DSv2, GS, Ls e Fs:

* **Serviço de nuvem**

    Você pode adicionar VMs da série DS para um serviço de nuvem que tenha apenas VMs da série DS. Não adicione VMs da série DS a um serviço de nuvem existente que tenha qualquer tipo diferente de VMs da série DS. Você pode migrar seus VHDs existentes para um novo serviço de nuvem que executa apenas VMs da série DS. Se quiser usar o mesmo endereço IP virtual para o novo serviço de nuvem que hospeda as VMs da série DS, use [endereços IP reservados](../../virtual-network/virtual-networks-instance-level-public-ip.md). As VMs da série GS podem ser adicionadas a um serviço de nuvem existente que tem apenas VMs da série Gs.

* **Disco do sistema operacional**

    Você pode configurar sua VM de Armazenamento Premium para usar um disco de sistema operacional padrão ou premium. Para obter a melhor experiência, é recomendável usar um disco de sistema operacional baseado no Armazenamento Premium.

* **Discos de dados**

    Você pode usar discos standard e premium na mesma VM de Armazenamento Premium. Com o Armazenamento Premium, você pode provisionar uma VM e anexar vários discos de dados persistentes a ela. Se necessário, para aumentar a capacidade e o desempenho do volume, você pode distribuir entre os discos.

    > [!NOTE]
    > Se você distribuir discos de dados de armazenamento premium usando [Espaços de Armazenamento](http://technet.microsoft.com/library/hh831739.aspx), configure espaços de armazenamento com uma coluna para cada disco que será usado. Caso contrário, o desempenho geral do volume distribuído pode ser menor que o esperado devido a uma distribuição irregular de tráfego entre os discos. Por padrão, no Gerenciador de Servidores, você pode configurar colunas para até oito discos. Se você tiver mais de oito discos, use o PowerShell para criar o volume. Especifique manualmente o número de colunas. Caso contrário, a UI do Gerenciador de servidores continuará a usar 8 colunas, mesmo que haja mais discos. Por exemplo, se você tiver 32 discos em um conjunto único de distribuição, especifique 32 colunas. Para especificar o número de colunas que o disco virtual usa, no cmdlet [New-VirtualDisk](http://technet.microsoft.com/library/hh848643.aspx) do PowerShell, use o parâmetro *NumberOfColumns*. Para obter mais informações, confira [Visão geral dos espaços de armazenamento](http://technet.microsoft.com/library/hh831739.aspx) e [Perguntas frequentes de espaços de armazenamento](http://social.technet.microsoft.com/wiki/contents/articles/11382.storage-spaces-frequently-asked-questions-faq.aspx).
    >
    > 

* **Cache**

    VMs da série de tamanho que dão suporte a Armazenamento Premium têm um único recurso de cache para altos níveis de taxa de transferência e latência. A capacidade de cache excede o desempenho de disco de armazenamento premium subjacente. Você pode definir a política de cache de disco em discos de armazenamento premium para **ReadOnly**, **ReadWrite** ou **Nenhum**. A política de cache de disco de padrão é **ReadOnly** para todos os discos de dados premium e **ReadWrite** para discos do sistema operacional. Para otimizar o desempenho do seu aplicativo, use a configuração de cache correta. Por exemplo, para discos de dados com necessidade de prontidão intensa ou somente leitura, como arquivos de dados do SQL Server, defina a política de cache de disco como **ReadOnly**. Para discos de dados com gravação intensa ou somente gravação, como arquivos de log do SQL Server, defina a política de cache de disco como **None**. Para saber mais sobre como otimizar seu design com o Armazenamento Premium, confira [Design para desempenho com o Armazenamento Premium](storage-premium-storage-performance.md).

* **Analytics**

    Para analisar o desempenho da máquina virtual usando discos no Armazenamento Premium, ative o diagnóstico de VM no [portal do Azure](https://portal.azure.com). Para obter mais informações, confira [Monitoramento de VM do Azure com extensão de diagnóstico do Azure](https://azure.microsoft.com/blog/2014/09/02/windows-azure-virtual-machine-monitoring-with-wad-extension/). 

    Para ver o desempenho do disco, use ferramentas baseadas no sistema operacional como o [Windows Performance Monitor](https://technet.microsoft.com/library/cc749249.aspx) para VMs do Windows e o comando [iostat](http://linux.die.net/man/1/iostat) para VMs do Linux.

* **Desempenho e limites de escala VM**

    Cada tamanho de VM com suporte do Armazenamento Premium tem limites de escala e especificações de desempenho para IOPS, largura de banda e número de discos que podem ser anexados por VM. Quando você usar discos de armazenamento premium com máquinas virtuais, certifique-se de que há IOPS e largura de banda suficientes em sua VM para direcionar o tráfego de disco.

    Por exemplo, uma VM STANDARD_DS1 tem uma largura de banda dedicada de 32 MB/s para o tráfego de discos do Armazenamento Premium. Um disco de Armazenamento Premium P10 pode fornecer uma largura de banda de 100 MB/s. Se um disco P10 do armazenamento premium estiver anexado a essa VM, poderá chegar apenas a 32 MB/s. Não pode usar o máximo 100 MB/s que o disco P10 pode fornecer.

    Atualmente, a maior VM da série DS é o Standard_DS15_v2. O Standard_DS15_v2 pode fornecer até 960 MB/s em todos os discos. A maior VM da série GS é Standard_GS5. O Standard_GS5 pode fornecer até 2.000 MB/s em todos os discos.

    Observe que esses limites são apenas para o tráfego de disco. Esses limites não incluem acertos de cache e tráfego de rede. Uma largura de banda separada está disponível para o tráfego de rede de VM. A largura de banda para o tráfego de rede é diferente da largura de banda dedicada usada pelos discos de armazenamento premium.

    Para obter informações mais atualizadas sobre IOPS e produtividade (largura de banda) máximos para as VMs com suporte do Armazenamento Premium, confira [Tamanhos de VMs Windows](/azure/virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ou [Tamanhos de VMs Linux](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

    Para obter mais informações sobre discos de armazenamento premium e seus limites IOPS e taxa de transferência, confira a tabela na próxima seção.

## <a name="scalability-and-performance-targets"></a>Escalabilidade e metas de desempenho
Nesta seção, descreveremos as metas de escalabilidade e desempenho a serem consideradas ao usar o Armazenamento Premium.

As contas de Armazenamento Premium têm as seguintes metas de escalabilidade:

| Capacidade total da conta | Largura de banda total para uma conta de armazenamento com redundância local |
| --- | --- | 
| Capacidade do disco: 35 TB <br>Capacidade do instantâneo: 10 TB | Até 50 gigabits para entrada<sup>1</sup> + saída<sup>2</sup> |

<sup>1</sup> Todos os dados (solicitações) que são enviados para uma conta de armazenamento

<sup>2</sup> Todos os dados (respostas) que são recebidos de uma conta de armazenamento

Para saber mais, confira [Metas de desempenho e escalabilidade do Armazenamento do Azure](storage-scalability-targets.md).

Se você estiver usando contas de Armazenamento Premium para discos não gerenciados, e seu aplicativo ultrapassar as metas de escalabilidade de uma única conta de armazenamento, convém migrar para discos gerenciados. Se você não deseja migrar discos gerenciados, crie seu aplicativo para usar várias contas de armazenamento. Em seguida, particione os dados nessas contas de armazenamento. Por exemplo, se você quiser anexar discos de 51 TB em várias VMs, separe-as entre duas contas de armazenamento. O limite para uma conta de armazenamento único premium é de 35 TB. Certifique-se de que uma única conta de Armazenamento Premium nunca tenha mais do que 35 TB de discos provisionados.

### <a name="premium-storage-disk-limits"></a>Limites do disco de Armazenamento Premium
Quando você provisiona um disco de Armazenamento Premium, o tamanho do disco determina os valores máximos de IOPS e taxa de transferência (largura de banda). O Azure oferece sete tipos de disco de armazenamento Premium: P4 (somente Managed Disks), P6 (somente Managed Disks), P10, P20, P30, P40 e P50. Cada tipo de disco de armazenamento premium tem limites específicos de IOPS e taxa de transferência. Os limites para os tipos de disco são descritos na tabela a seguir:

| Tipo de discos premium  | P4    | P6    | P10   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| Tamanho do disco           | 32 GB| 64 GB| 128 GB| 512 GB            | 1024 GB (1 TB)    | 2048 GB (2 TB)    | 4095 GB (4 TB)    | 
| IOPS por disco       | 120   | 240   | 500   | 2.300              | 5.000              | 7500              | 7500              | 
| Taxa de transferência por disco | 25 MB por segundo  | 50 MB por segundo  | 100 MB por segundo | 150 MB por segundo | 200 MB por segundo | 250 MB por segundo | 250 MB por segundo | 

> [!NOTE]
> Verifique se largura de banda suficiente está disponível na sua VM para direcionar o tráfego de disco, conforme descrito em [Suporte para Armazenamento Premium VMs](#premium-storage-supported-vms). Caso contrário, sua taxa de transferência do disco e o IOPS é restrita a valores mais baixos. A taxa de transferência máxima e IOPS se baseiam nos limites de VM, não nos limites de disco descritos na tabela anterior.  
> 
> 

Aqui estão algumas coisas importantes a saber sobre metas de desempenho e escalabilidade do Armazenamento Premium:

* **Desempenho e capacidade provisionada**

    Quando você provisiona um disco de armazenamento premium, ao contrário do armazenamento padrão, a capacidade, IOPS e taxa de transferência de disco são garantidos. Por exemplo, se você criar um disco P50, o Azure provisionará uma capacidade de armazenamento de 4.095 GB, 7.500 IOPS e uma taxa de transferência de 250 MB/s para o disco. O aplicativo pode usar a capacidade e o desempenho no todo ou em parte.

* **Tamanho do disco**

    O Azure mapeia o tamanho do disco (arredondado) para a opção mais próxima do Disco de Armazenamento Premium, conforme especificado na tabela na seção anterior. Por exemplo, um tamanho de disco de 100 GB é classificado como uma opção P10. Pode executar até 500 IOPS, com taxa de transferência de até 100 MB/s. Da mesma forma, um disco de tamanho de 400 GB é classificado como P20. Pode executar até 2.300 IOPS, com taxa de transferência de 150 MB/s.
    
    > [!NOTE]
    > Você pode aumentar com facilidade o tamanho dos discos existentes. Por exemplo, talvez você queira aumentar o tamanho de um disco de 30 GB para 128 GB ou até mesmo 1 TB. Ou, pode querer converter seu disco P20 para P30 porque precisa de mais capacidade ou mais IOPS e taxa de transferência. 
    > 
 
* **Tamanho de E/S**

    O tamanho de uma E/S é 256 KB. Se os dados transferidos forem menores que 256 KB, serão considerados uma unidade de E/S. Tamanhos maiores de e/s são contados como várias entradas e saídas de tamanho de 256 KB. Por exemplo, 1.100 KB E/S é contado como cinco unidades de E/S.

* **Taxa de transferência**

    O limite de taxa de transferência inclui gravações no disco e inclui operações de leitura de disco que não são atendidas do cache. Por exemplo, um disco P10 tem taxa de transferência de 100 MB/s por disco. Alguns exemplos de transferência válidos para um disco P10 são mostrados na tabela a seguir:

    | Taxa de transferência máxima por disco P10 | Leituras não armazenadas em cache do disco | Gravações não armazenadas em cache do disco |
    | --- | --- | --- |
    | 100 MB/s | 100 MB/s | 0 |
    | 100 MB/s | 0 | 100 MB/s |
    | 100 MB/s | 60 MB/s | 40 MB/s |

* **Acertos do cache**

    Os acertos de cache não são limitados pelo IOPS ou Taxa de transferência alocada do disco. Por exemplo, quando você usa um disco de dados com a configuração de cache **ReadOnly** em uma VM com suporte do Armazenamento Premium, as Leituras realizadas do cache não estão sujeitas aos limites de taxa de transferência e IOPS do disco. Se a carga de trabalho de um disco for predominantemente de leitura, você poderá obter uma taxa de transferência muito alta. O cache está sujeito a limites separados de IOPS e taxa de transferência no nível da VM, com base no tamanho da VM. VMs da série DS têm aproximadamente 4.000 IOPS e taxa de transferência de 33 MB/s por núcleo para cache e E/S de SSD local. As VMs da série GS têm um limite de 5.000 IOPS e taxa de transferência de 50 MB/s por núcleo para cache e E/S de SSD local. 

## <a name="throttling"></a>Limitação
Uma limitação poderá ocorrer se seu aplicativo IOPS ou a taxa de transferência excederem os limites alocados para um disco de armazenamento premium. A limitação também poderá ocorrer se o tráfego total em todos os discos na VM exceder o limite de largura de banda em disco disponível para a máquina virtual. Para evitar a limitação, é recomendável que você limite o número de solicitações pendentes de E/S do disco. Use um limite com base nas metas de desempenho e escalabilidade para o disco que você forneceu e a largura de banda de disco disponível para a VM.  

Seu aplicativo pode obter a latência menor quando ele é projetado para evitar a limitação. No entanto, se o número de solicitações de e/s pendentes para o disco for muito pequeno, seu aplicativo não poderá tirar proveito dos níveis máximos de IOPS e taxa de transferência que estão disponíveis no disco.

Os exemplos a seguir demonstram como calcular os níveis de limitação. Todos os cálculos baseiam-se no tamanho da unidade de E/S de 256 KB.

### <a name="example-1"></a>Exemplo 1
O aplicativo processou 495 unidades de E/S de tamanho de 16 KB em um segundo em um disco P10. As unidades de E/S são contadas como 495 IOPS. Se você tentar uma e/s de 2 MB no mesmo segundo, o total de unidades de e/s é igual a 495 + 8 IOPS. Isso ocorre porque o E/S de 2 MB = unidades de E/S 2.048 KB / 256 KB = 8, quando o tamanho da unidade de E/S é 256 KB. Como a soma de 495 + 8 excede o limite de 500 IOPS para o disco, a limitação ocorre.

### <a name="example-2"></a>Exemplo 2
Seu aplicativo processou 400 unidades de E/S de tamanho de 256 KB em um disco P10. A largura de banda total consumida é (400 × 256) / 1024 KB = 100 MB/s. Um disco P10 tem um limite de taxa de transferência de 100 MB/s. Se o seu aplicativo tentar realizar mais operações de E/S nesse segundo, ele é limitado porque excede o limite alocado.

### <a name="example-3"></a>Exemplo 3
Você tem uma VM DS4 com dois discos P30 conectados. Cada disco P30 é capaz de ter uma taxa de transferência de 200 MB/s. No entanto, uma VM DS4 tem uma capacidade de largura de banda total do disco de 256 MB/s. Você não pode aplicar a taxa de transferência máxima aos os discos conectados nesta VM DS4 ao mesmo tempo. Para resolver esse problema, você pode sustentar o tráfego de 200 MB/s em um disco e 56 MB/s em outro disco. Se a soma do tráfego disco ultrapassar 256 MB/s, o tráfego de disco será limitado.

> [!NOTE]
> Se o tráfego de disco consistir principalmente de tamanhos de E/S pequenos, seu aplicativo provavelmente atingirá o limite IOPS antes do limite de taxa de transferência. No entanto, se o tráfego de disco consistir principalmente de tamanhos grandes de E/S, seu aplicativo provavelmente atingirá o limite de taxa de transferência primeiro, em vez do limite de IOPS. Você pode maximizar o IOPS do aplicativo e a capacidade de taxa de transferência usando tamanhos de E/S ideais. Além disso, você pode limitar o número de solicitações de E/S para um disco pendentes.
> 

Para saber mais sobre a criação de alto desempenho usando o Armazenamento Premium, confira [Design para desempenho com o Armazenamento Premium](storage-premium-storage-performance.md).

## <a name="snapshots-and-copy-blob"></a>Instantâneos e cópia de Blob

Para o serviço de Armazenamento, o arquivo VHD é um blob de páginas. Você pode tirar instantâneos dos blobs de páginas e copiá-los para outro local, como uma conta de armazenamento diferente.

### <a name="unmanaged-disks"></a>Discos não gerenciados

Crie [instantâneos incrementais](../../virtual-machines/windows/incremental-snapshots.md) para discos não gerenciados premium da mesma maneira que usa os instantâneos com o Armazenamento Standard. O Armazenamento Premium dá suporte ao armazenamento com redundância local somente como a opção de replicação. É recomendável que você crie instantâneos e, em seguida, copie os instantâneos para uma conta de armazenamento com redundância geográfica padrão. Para saber mais, confira [Opções de redundância do Armazenamento do Azure](storage-redundancy.md).

Se um disco estiver anexado a uma VM, algumas operações de API no disco não serão permitidas. Por exemplo, você não pode executar uma operação de [Cópia de Blob](/rest/api/storageservices/Copy-Blob) nesse blob se o disco estiver anexado a uma VM. Em vez disso, primeiro crie um instantâneo desse blob usando a API REST [Blob de Instantâneo](/rest/api/storageservices/Snapshot-Blob). Em seguida, execute [Copiar Blob](/rest/api/storageservices/Copy-Blob) do instantâneo para copiar o disco anexado. Como alternativa, você pode desanexar o disco e, em seguida, executar as operações necessárias.

Os seguintes limites a se aplicam a instantâneos de blob de Armazenamento Premium:

| Limite de armazenamento Premium | Valor |
| --- | --- |
| Número máximo de instantâneos por blob | 100 |
| Capacidade de conta de armazenamento de instantâneos<br>(Inclui dados em apenas instantâneos. Não inclui dados no blob de base.) | 10 TB |
| Tempo mínimo entre instantâneos consecutivos | 10 minutos |

Para manter cópias com redundância geográfica de seus instantâneos, você pode copiar instantâneos de uma conta de armazenamento premium para uma conta de armazenamento com redundância geográfica padrão usando AzCopy ou Copiar Blob. Para obter mais informações, confira [Transferir dados com o Utilitário de Linha de Comando AzCopy](storage-use-azcopy.md) e [Cópia de Blob](/rest/api/storageservices/Copy-Blob).

Para obter informações detalhadas sobre como executar operações REST em blobs de página nas contas de Armazenamento Premium, confira [Operações do serviço Blob com o Armazenamento Premium do Azure](http://go.microsoft.com/fwlink/?LinkId=521969).

### <a name="managed-disks"></a>Discos gerenciados

Um instantâneo de um disco gerenciado é uma cópia somente leitura do disco gerenciado. O instantâneo é armazenado como um disco gerenciado padrão. Atualmente, [instantâneos incrementais](../../virtual-machines/windows/incremental-snapshots.md) não têm suporte para discos gerenciados. Para aprender a tirar um instantâneo de um disco gerenciado, confira [Criar uma cópia de um VHD armazenado como um Managed Disk do Azure usando instantâneos gerenciado no Windows](../../virtual-machines/windows/snapshot-copy-managed-disk.md) ou [Criar uma cópia de um VHD armazenado como um Managed Disk do Azure usando instantâneos gerenciados no Linux](../../virtual-machines/linux/snapshot-copy-managed-disk.md)

Se um disco gerenciado estiver anexado a uma VM, algumas operações de API no disco não serão permitidas. Por exemplo, você não pode gerar uma assinatura de acesso compartilhado (SAS) para executar uma operação de cópia enquanto o disco estiver anexado a uma VM. Em vez disso, primeiro crie um instantâneo do disco e, em seguida, execute a cópia do instantâneo. Como alternativa, você pode desanexar o disco e, em seguida, gerar uma SAS para executar a operação de cópia.


## <a name="premium-storage-for-linux-vms"></a>Armazenamento Premium para VMs do Linux
Você pode usar as seguintes informações para ajudá-lo a configurar as VMs do Linux no Armazenamento Premium:

Para atingir as metas de escalabilidade no Armazenamento Premium, para todos os discos de armazenamento premium com cache definido como **ReadOnly** ou **nenhum**, você deve desabilitar "barreiras" ao montar o sistema de arquivos. Você não precisa de barreiras para esse cenário, pois as gravações em discos de Armazenamento Premium são duráveis para essas configurações de cache. Quando a solicitação de gravação for concluída, os dados terão sido gravados no armazenamento persistente. Para desabilitar "barreiras", use um dos seguintes métodos. Escolha o seguinte para o sistema de arquivos:
  
* Para **reiserFS**, para desabilitar as barreiras, use a opção de montagem `barrier=none`. (Para habilitar as barreiras, use `barrier=flush`.)
* Para **ext3/ext4**, para desabilitar as barreiras, use a opção de montagem `barrier=0`. (Para habilitar as barreiras, use `barrier=1`.)
* Para **XFS**, para desabilitar as barreiras, use a opção de montagem `nobarrier`. (Para habilitar as barreiras, use `barrier`.)
* Para discos de armazenamento premium com cache definido como **ReadWrite**, habilite as barreiras para durabilidade de gravação.
* Para que rótulos de volume persistam depois de reiniciar a máquina virtual, você deve atualizar /etc/fstab com as referências do identificador universalmente exclusivo (UUID) nos discos. Para obter mais informações, confira [Adicionar um disco gerenciado a uma VM Linux](../../virtual-machines/linux/add-disk.md).

As seguintes distribuições Linux foram validadas para o armazenamento do Azure Premium. Para obter o melhor desempenho e estabilidade com o Armazenamento Premium, recomendamos que você atualize suas VMs para uma dessas versões, no mínimo (ou uma versão posterior). Algumas das versões exigem um LIS (Serviços de Integração do Linux) v4.0 para Azure mais recente. Para baixar e instalar uma distribuição, siga o link listado na tabela a seguir. Podemos adicionar imagens à lista à medida que concluímos a validação. Observe que nossas validações mostram que o desempenho varia para cada imagem. O desempenho depende da carga de trabalho e das configurações de imagem. Imagens diferentes são ajustadas para tipos diferentes de carga de trabalho.

| Distribuição | Versão | Kernel com suporte | Detalhes |
| --- | --- | --- | --- |
| Ubuntu | 12.04 | 3.2.0-75.110+ | Ubuntu-12_04_5-LTS-amd64-server-20150119-en-us-30GB |
| Ubuntu | 14.04 | 3.13.0-44.73+ | Ubuntu-14_04_1-LTS-amd64-server-20150123-en-us-30GB |
| Debian | 7.x, 8.x | 3.16.7-ckt4-1+ | &nbsp; |
| SUSE | SLES 12| 3.12.36-38.1+| suse-sles-12-priority-v20150213 <br> suse-sles-12-v20150213 |
| SUSE | SLES 11 SP4 | 3.0.101-0.63.1+ | &nbsp; |
| CoreOS | 584.0.0+| 3.18.4+ | CoreOS 584.0.0 |
| CentOS | 6.5, 6.6, 6.7, 7.0 | &nbsp; | [LIS4 obrigatório](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) <br> *Confira a observação na próxima seção* |
| CentOS | 7.1+ | 3.10.0-229.1.2.el7+ | [LIS4 recomendado](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) <br> *Confira a observação na próxima seção* |
| Red Hat Enterprise Linux (RHEL) | 6.8+, 7.2+ | &nbsp; | &nbsp; |
| Oracle | 6.0+, 7.2+ | &nbsp; | UEK4 ou RHCK |
| Oracle | 7.0-7.1 | &nbsp; | UEK4 ou RHCK c/[LIS 4.1+](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) |
| Oracle | 6.4-6.7 | &nbsp; | UEK4 ou RHCK c/[LIS 4.1+](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) |


### <a name="lis-drivers-for-openlogic-centos"></a>Drivers LIS para Openlogic CentOS

Se você executa VMs com OpenLogic CentOS, execute o comando a seguir para instalar os drivers mais recentes:

```
sudo rpm -e hypervkvpd  ## (Might return an error if not installed. That's OK.)
sudo yum install microsoft-hyper-v
```

Para ativar os novos drivers, reinicie o computador.

## <a name="pricing-and-billing"></a>Preços e cobrança

Ao usar o Armazenamento Premium, as seguintes considerações de cobrança se aplicam:

* **Tamanho de disco e blob de Armazenamento Premium**

    A cobrança para um disco de armazenamento premium ou blob depende do tamanho do disco ou blob provisionado. O Azure mapeia o tamanho provisionado (arredondado) para a opção de disco de armazenamento premium mais próxima. Para obter detalhes, confira a tabela [Metas de desempenho e escalabilidade do Armazenamento Premium](#premium-storage-scalability-and-performance-targets). Cada disco é mapeado para um tamanho de disco provisionado com suporte e é cobrado de acordo. A cobrança por qualquer disco provisionado é rateada por hora usando o preço mensal para a oferta de Armazenamento Premium. Por exemplo, se você provisionou um disco P10 e ele foi excluído após 20 horas, você será cobrado pela a oferta P10 rateada em 20 horas. Isto é independente da quantidade de dados reais gravados no disco ou do IOPS e taxa de transferência usados.

* **Instantâneos de discos não gerenciados Premium**

    Instantâneos de discos premium não gerenciados são cobrados pela capacidade adicional usada pelos instantâneos. Para obter mais informações sobre instantâneos, confira [Criar um instantâneo de um blob](/rest/api/storageservices/Snapshot-Blob).

* **Instantâneos de discos gerenciados Premium**

    Um instantâneo de um disco gerenciado é uma cópia somente leitura do disco. O disco é armazenado como um disco gerenciado padrão. Um instantâneo custa o mesmo que um disco gerenciado padrão. Por exemplo, se você tirar um instantâneo de um disco gerenciado premium de 128 GB, o custo de um instantâneo será equivalente ao de um disco gerenciado padrão de 128 GB.  

* **Transferências de dados de saída**

    [Transferências de dados de saída](https://azure.microsoft.com/pricing/details/data-transfers/) (dados saindo dos data centers do Azure) acarretam a cobrança por uso de largura de banda.

Para obter informações detalhadas sobre os preços para Armazenamento Premium, o suporte para Armazenamento Premium VMs e discos gerenciados, confira estes artigos:

* [Preços do Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/)
* [Preços de Máquinas Virtuais](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Preços de discos gerenciados](https://azure.microsoft.com/pricing/details/managed-disks/)

## <a name="azure-backup-support"></a>Suporte de Backup do Azure 

Para a recuperação de desastre regional, é necessário fazer backup dos discos da VM em outra região usando o [Backup do Azure](../../backup/backup-introduction-to-azure-backup.md) e uma conta de armazenamento GRS como o cofre de backup.

Para criar um trabalho de backup com backups baseados em tempo, fácil restauração de VM e políticas de retenção de backup, use o Backup do Azure. Você pode usar os Backup com discos gerenciados e não gerenciados. Para obter mais informações, confira [Backup do Azure para máquinas virtuais com discos não gerenciados](../../backup/backup-azure-vms-first-look-arm.md) e [Backup do Azure para máquinas virtuais com discos gerenciados](../../backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup). 

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre o Armazenamento Premium, confira os artigos a seguir.

### <a name="design-and-implement-with-premium-storage"></a>Criar e implementar com o Armazenamento Premium
* [Design para desempenho com o Armazenamento Premium](storage-premium-storage-performance.md)
* [Operações de armazenamento de blobs com o Armazenamento Premium](http://go.microsoft.com/fwlink/?LinkId=521969)

### <a name="operational-guidance"></a>Diretrizes operacionais
* [Migrar para o Armazenamento Premium do Azure](../common/storage-migration-to-premium-storage.md)

### <a name="blog-posts"></a>Postagens no blog
* [Armazenamento Premium do Azure com disponibilidade geral](https://azure.microsoft.com/blog/azure-premium-storage-now-generally-available-2/)
* [Anúncio da série GS: como adicionar suporte ao Armazenamento Premium para as maiores VMs na nuvem pública](https://azure.microsoft.com/blog/azure-has-the-most-powerful-vms-in-the-public-cloud/)
