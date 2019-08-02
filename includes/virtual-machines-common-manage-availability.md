---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: f57c2cacca9bb3e4526ec6261b8aa0ff6c18448a
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171899"
---
## <a name="understand-vm-reboots---maintenance-vs-downtime"></a>Entender as reinicializações de VM - manutenção vs. tempo de inatividade
Há três cenários que podem afetar a máquina virtual no Azure: manutenção de hardware não planejada, tempo de inatividade inesperado e manutenção planejada.

* O **Evento de Manutenção de Hardware Não Planejado** ocorre quando a plataforma do Azure prevê que o hardware ou qualquer componente de plataforma associado a um computador físico está prestes a falhar. Quando a plataforma previr uma falha, ela emitirá um evento de manutenção de hardware não planejada para reduzir o impacto em máquinas virtuais hospedadas no hardware. O Azure usa [migração ao vivo](https://docs.microsoft.com/azure/virtual-machines/linux/maintenance-and-updates) tecnologia para migrar as máquinas virtuais de hardware com falha para uma máquina física íntegra. A Migração ao Vivo é uma operação de preservação de VM que só pausa a Máquina Virtual por um curto período. A memória, os arquivos abertos e as conexões de rede são mantidos, mas o desempenho pode ser reduzido antes e/ou depois do evento. Em casos em que a Migração ao Vivo não puder ser usada, a VM terá um Tempo de Inatividade Inesperado, conforme descrito abaixo.


* **Um Tempo de Inatividade Inesperado** é quando o hardware ou a infraestrutura física para a máquina virtual falha inesperadamente. Isso inclui falhas na rede local, falhas no disco local ou outras falhas no nível de rack. Quando detectada, a plataforma do Azure migra automaticamente (repara) a máquina virtual para um computador físico íntegro no mesmo datacenter. Durante o procedimento de recuperação, as máquinas virtuais ficarão inativas (reinicialização) e, em alguns casos, perderão a unidade temporária. O sistema operacional e os discos de dados anexados são sempre preservados. 

  As máquinas virtuais também podem apresentar tempo de inatividade no caso improvável de uma falha ou desastre que afete um datacenter inteiro, ou até mesmo uma região inteira. Nestas situações, o Azure fornece opções de proteção, incluindo [zonas disponibilidade](../articles/availability-zones/az-overview.md) e [regiões emparelhadas](../articles/best-practices-availability-paired-regions.md#what-are-paired-regions).

* Os **eventos de Manutenção Planejada** são atualizações periódicas feitas pela Microsoft na plataforma subjacente do Azure para melhorara a confiabilidade, o desempenho e a segurança geral da infraestrutura da plataforma executada pela máquina virtual. A maioria dessas atualizações é realizada sem nenhum impacto nas suas Máquinas Virtuais ou nos Serviços de Nuvem (veja [Manutenção de preservação da VM](https://docs.microsoft.com/azure/virtual-machines/windows/preserving-maintenance)). Embora a plataforma do Azure tente usar a Manutenção de Preservação de VM em todas as ocasiões possíveis, há casos raros em que essas atualizações exigem uma reinicialização da máquina virtual para aplicar as atualizações necessárias para a infraestrutura subjacente. Neste caso, você pode executar a Manutenção Planejada do Azure com a operação Manutenção-Reimplantação ao iniciar a manutenção para as VMs na janela de tempo adequada. Para saber mais, veja [Manutenção planejada para máquinas virtuais](https://docs.microsoft.com/azure/virtual-machines/windows/planned-maintenance/).


Para reduzir o impacto do tempo de inatividade devido a um ou mais desses eventos, sugerimos que siga as práticas recomendadas de alta disponibilidade para suas máquinas virtuais:

* [Configurar diversas máquinas virtuais em um conjunto de disponibilidade para redundância]
* [Como usar discos gerenciados para VMs em um conjunto de disponibilidade]
* [Usar eventos agendados para responder de forma proativa a eventos que afetam a VM](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-scheduled-events)
* [Configurar cada camada de aplicativo em conjuntos de disponibilidade separados]
* [Combinar o balanceador de carga com os conjuntos de disponibilidade]
* [Usar as zonas de disponibilidade para se proteger contra falhas no nível do datacenter]

## <a name="configure-multiple-virtual-machines-in-an-availability-set-for-redundancy"></a>Configurar diversas máquinas virtuais em um conjunto de disponibilidade para redundância
Para oferecer redundância para o seu aplicativo, recomendamos que agrupe uma ou mais máquinas virtuais em um conjunto de disponibilidade. Essa configuração em um datacenter garante que durante um evento de manutenção planejada ou não planejada, pelo menos uma máquina virtual estará disponível e atenderá os 99,95% SLA do Azure. Para saber mais, confira [SLA para máquinas virtuais](https://azure.microsoft.com/support/legal/sla/virtual-machines/).

> [!IMPORTANT]
> Evite deixar uma única máquina virtual sozinha em um conjunto de disponibilidade. As máquinas virtuais nesta configuração não se qualificam para uma garantia de SLA e enfrentam tempo de inatividade durante eventos de manutenção planejada do Azure, exceto quando uma única VM estiver usando [SSDs premium do Azure](../articles/virtual-machines/windows/disks-types.md#premium-ssd). Para VMs únicas usando os SSDs premium, o SLA do Azure se aplica.

Cada máquina virtual em seu conjunto de disponibilidade receberá um **domínio de atualização** e um **domínio de falha** da plataforma subjacente do Azure. Para determinado conjunto de disponibilidade, cinco domínios de atualização não configuráveis pelo usuário são atribuídos por padrão (é possível aumentar as implantações do Resource Manager para fornecer até 20 domínios de atualização) para indicar grupos de máquinas virtuais e hardware físico subjacente que pode ser reinicializado ao mesmo tempo. Quando mais do que cinco máquinas virtuais são configuradas com um único conjunto de disponibilidade, a sexta máquina virtual será alocada com o mesmo domínio de atualização da primeira máquina virtual, a sétima com o mesmo domínio de atualização da segunda máquina virtual e assim sucessivamente. A ordem de reinicialização dos domínios de atualização pode não ser sequencial durante a manutenção planejada, mas apenas um domínio de atualização é reinicializado por vez. Um domínio de atualização reinicializado recebe 30 minutos para recuperação antes do início da manutenção em um domínio de atualização diferente.

Os domínios de falha definem o grupo de máquinas virtuais que compartilham uma fonte de energia e chave de rede comum. Por padrão, as máquinas virtuais configuradas em seu conjunto de disponibilidade são separadas entre até três domínios de falha para implantações do Resource Manager (dois domínios de falha para o Clássico). Embora colocar suas máquinas virtuais em um conjunto de disponibilidade não proteja seu aplicativo de falhas de sistema operacional e nem específicas de aplicativo, isso limita o impacto das potencias falhas físicas de hardware, panes de rede ou interrupções de energia.

<!--Image reference-->
   ![Desenho conceitual da configuração de domínio de falha e domínio de atualização](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="use-managed-disks-for-vms-in-an-availability-set"></a>Usar discos gerenciados para VMs no conjunto de disponibilidade
Se você estiver usando atualmente MVs com discos não gerenciados, é altamente recomendável [converter as VMs no Conjunto de Disponibilidade para usar os Managed Disks](../articles/virtual-machines/windows/convert-unmanaged-to-managed-disks.md).

Os [discos gerenciados](../articles/virtual-machines/windows/managed-disks-overview.md) fornecem melhor confiabilidade para os Conjuntos de Disponibilidade, assegurando que os discos das VMs em um Conjunto de Disponibilidade estejam suficientemente isolados entre si para evitar pontos únicos de falha. Ele faz isso automaticamente colocando os discos em domínios de falha de armazenamento diferentes (clusters de armazenamento) e alinhando-os com o domínio de falha da VM. Se um domínio de falha do armazenamento falhar devido a uma falha de hardware ou software, somente a instância de VM com discos no domínio de falha do armazenamento falhará.
![FDs de discos gerenciados](./media/virtual-machines-common-manage-availability/md-fd-updated.png)

> [!IMPORTANT]
> O número de domínios de falha para conjuntos de disponibilidade gerenciados varia por região: dois ou três por região. A tabela a seguir mostra o número por região

[!INCLUDE [managed-disks-common-fault-domain-region-list](managed-disks-common-fault-domain-region-list.md)]

Se você planeja usar VMs com discos não gerenciados, siga abaixo as práticas recomendadas para as contas de Armazenamento nas quais os discos rígidos virtuais (VHDs) das VMs são armazenados como [blobs de página](https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs).

1. **Manter todos os discos (sistema operacional e dados) associados a uma VM na mesma conta de armazenamento**
2. **Examine os [limites](../articles/storage/common/storage-scalability-targets.md) no número de discos não gerenciados em uma Conta de armazenamento** antes de adicionar mais VHDs a uma conta de armazenamento
3. **Use uma conta de armazenamento distinta para cada VM em um conjunto de disponibilidade.** Não compartilhe Contas de armazenamento com várias VMs no mesmo Conjunto de Disponibilidade. É aceitável que as VMs em diferentes Conjuntos de Disponibilidade compartilhem contas de armazenamento, desde que as melhores práticas acima sejam seguidas ![FDs de discos não gerenciados](./media/virtual-machines-common-manage-availability/umd-updated.png)

## <a name="use-scheduled-events-to-proactively-respond-to-vm-impacting-events"></a>Usar eventos agendados para responder de forma proativa a eventos que afetam a VM

Quando você assina [eventos agendados](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-scheduled-events), sua VM é notificada sobre eventos de manutenção futura que podem afetá-la. Quando os eventos agendados são habilitados, sua máquina virtual recebe uma quantidade mínima de tempo antes que a atividade de manutenção seja realizada. Por exemplo, as atualizações do sistema operacional do host que podem afetar a VM são colocadas na fila como eventos que especificam o impacto, bem como uma hora em que a manutenção será realizada, caso nenhuma medida seja tomada. Os eventos de agendamento também são colocados na fila quando o Azure detecta uma falha de hardware iminente que pode afetar a VM, o que permite que você decida quando a recuperação deve ser executada. Os clientes podem usar o evento para executar tarefas antes da manutenção, como salvar o estado, fazer failover para o secundário e assim por diante. Depois de concluir a lógica para manipular normalmente o evento de manutenção, você poderá aprovar o evento agendado pendente para permitir que a plataforma continue com a manutenção.

## <a name="configure-each-application-tier-into-separate-availability-sets"></a>Configurar cada camada de aplicativo em conjuntos de disponibilidade separados
Se as suas máquinas virtuais forem quase idênticas e servirem para o mesmo propósito para o seu aplicativo, recomendamos que configure o seu conjunto de disponibilidade para cada camada de seu aplicativo.  Se você colocar duas camadas diferentes no mesmo conjunto de disponibilidade, todas as máquinas virtuais na mesma camada de aplicativo podem ser reinicializadas ao mesmo tempo. Ao configurar ao menos duas máquinas virtuais no conjunto de disponibilidade de cada camada, você garante que ao menos uma máquina virtual de cada camada estará disponível.

Por exemplo, você pode colocar todas as máquinas virtuais no front-end de seu aplicativo com IIS, Apache, Ngnix execução em um único conjunto de disponibilidade. Certifique-se de que apenas as máquinas virtuais em front-end estão colocadas no mesmo conjunto de disponibilidade. Da mesma forma, certifique-se de que apenas as máquinas virtuais de camadas de dados sejam colocadas no seu próprio conjunto de disponibilidade, como as máquinas virtuais do SQL Server ou suas máquinas virtuais do MySQL.

<!--Image reference-->
   ![Camadas de aplicativo](./media/virtual-machines-common-manage-availability/application-tiers.png)

## <a name="combine-a-load-balancer-with-availability-sets"></a>Combinar o balanceador de carga com os conjuntos de disponibilidade
Combine o [Azure Load Balancer](../articles/load-balancer/load-balancer-overview.md) com o conjunto de disponibilidade para obter a melhor resiliência de aplicativo. O Balanceador de Carga do Azure distribui o tráfego entre as múltiplas máquinas virtuais. Para as nossas máquinas virtuais de camadas padrões, o Balanceador de carga do Azure está incluso. Nem todas as camadas de máquinas virtuais incluem o Azure Load Balancer. Para saber mais sobre o balanceamento de carga de suas máquinas virtuais, confira [Balanceamento de Carga em máquinas virtuais](../articles/virtual-machines/virtual-machines-linux-load-balance.md).

Se o balanceador de carga não estiver configurado para balancear o tráfego entre múltiplas máquinas virtuais, então qualquer evento de manutenção planejada afetará a única máquina virtual atendendo ao tráfego causando uma pane na sua camada de aplicativo. Colocar diversas máquinas virtuais na mesma camada sob o mesmo balanceador de carga e conjunto de disponibilidade habilita o tráfego a ser atendido continuamente pelo menos por uma instância.

## <a name="use-availability-zones-to-protect-from-datacenter-level-failures"></a>Usar as zonas de disponibilidade para se proteger contra falhas no nível do datacenter

[Zonas de disponibilidade](../articles/availability-zones/az-overview.md), uma alternativa para conjuntos de disponibilidade, expandem o nível de controle de que você precisa para manter a disponibilidade dos aplicativos e dos dados em suas VMs. Uma Zona de Disponibilidade é uma zona fisicamente separada em uma região do Azure. Há três Zonas de Disponibilidade por região do Azure com suporte. Cada Zona de Disponibilidade tem fonte de alimentação, rede e resfriamento distintos, e está logicamente separada de outras Zonas de Disponibilidade na região do Azure. Ao arquitetar suas soluções para usar VMs replicadas em zonas, você pode proteger seus aplicativos e seus dados contra a perda de um data center. Se uma zona for comprometida, os aplicativos e os dados replicados ficarão instantaneamente disponíveis em outra zona. 

![Zonas de disponibilidade](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

Saiba mais sobre como implantar uma VM do [Windows](../articles/virtual-machines/windows/create-powershell-availability-zone.md) ou do [Linux](../articles/virtual-machines/linux/create-cli-availability-zone.md) em uma Zona de Disponibilidade.


<!-- Link references -->
[Configurar diversas máquinas virtuais em um conjunto de disponibilidade para redundância]: #configure-multiple-virtual-machines-in-an-availability-set-for-redundancy
[Configurar cada camada de aplicativo em conjuntos de disponibilidade separados]: #configure-each-application-tier-into-separate-availability-sets
[Combinar o balanceador de carga com os conjuntos de disponibilidade]: #combine-a-load-balancer-with-availability-sets
[Avoid single instance virtual machines in availability sets]: #avoid-single-instance-virtual-machines-in-availability-sets
[Como usar discos gerenciados para VMs em um conjunto de disponibilidade]: #use-managed-disks-for-vms-in-an-availability-set
[Usar as zonas de disponibilidade para se proteger contra falhas no nível do datacenter]: #use-availability-zones-to-protect-from-datacenter-level-failures
