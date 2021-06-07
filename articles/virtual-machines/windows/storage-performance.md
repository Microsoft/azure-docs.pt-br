---
title: Otimizar o desempenho nas máquinas virtuais da série Lsv2 do Azure
description: Saiba como otimizar o desempenho da sua solução nas máquinas virtuais da série Lsv2 usando um exemplo do Windows.
author: sasha-melamed
ms.service: virtual-machines
ms.subservice: vm-sizes-storage
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/17/2019
ms.author: joelpell
ms.openlocfilehash: 8d38e3f58de0fc4cc1e963c18b002dc1e16852ec
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102556002"
---
# <a name="optimize-performance-on-the-lsv2-series-windows-virtual-machines"></a>Otimizar o desempenho nas máquinas virtuais do Windows da série Lsv2

As máquinas virtuais da série Lsv2 dão suporte a uma variedade de cargas de trabalho que precisam de alta E/S e taxa de transferência no armazenamento local em uma ampla gama de aplicativos e setores.  A série Lsv2 é ideal para Big Data, SQL, bancos de dados NoSQL, data warehouse e grandes bancos de dados transacionais, incluindo Cassandra, MongoDB, Cloudera e Redis.

O design das VMs (Máquinas Virtuais) da série Lsv2 maximiza o processador AMD EPYC™ 7551 para fornecer o melhor desempenho entre o processador, a memória, os dispositivos NVMe e as VMs. Além de maximizar o desempenho do hardware, as VMs da série Lsv2 são projetadas para funcionar com as necessidades dos sistemas operacionais Windows e Linux para melhorar o desempenho com o hardware e o software.

O ajuste do software e do hardware resultou na versão otimizada do [Windows Server 2019 Datacenter](https://www.microsoft.com/cloud-platform/windows-server-pricing), lançado no início de dezembro de 2018 para o Azure Marketplace, que dá suporte ao desempenho máximo nos dispositivos NVMe nas VMs da série Lsv2.

Este artigo fornece dicas e sugestões para garantir que suas cargas de trabalho e aplicativos alcancem o desempenho máximo projetado para as VMs. As informações nesta página serão atualizadas continuamente à medida que mais imagens otimizadas da série Lsv2 forem adicionadas ao Azure Marketplace.

## <a name="amd-eypc-chipset-architecture"></a>Arquitetura de chipset AMD EYPC™

As VMs da série Lsv2 usam processadores de servidor AMD EYPC™ com base na microarquitetura do Zen. A AMD desenvolveu a IF (Infinity Fabric) para EYPC™ como interconexão escalonável para seu modelo NUMA que poderia ser usado para comunicações na matriz, no pacote e em vários pacotes. Em comparação com o QPI (Quick-Path Interconnect) e o UPI (Ultra-Path Interconnect) usados em processadores em matriz monolítica modernos da Intel, a arquitetura de matriz pequena de vários NUMA do AMD pode proporcionar benefícios de desempenho, bem como desafios. O impacto real da largura de banda de memória e das restrições de latência pode variar dependendo do tipo de cargas de trabalho em execução.

## <a name="tips-for-maximizing-performance"></a>Dicas para maximizar o desempenho

* O hardware que alimenta as VMs da série Lsv2 utiliza dispositivos NVMe com oito QPs (pares de fila) de E/S. Cada fila de E/S do dispositivo NVMe é, na verdade, um par: uma fila de envio e uma fila de conclusão. O driver NVMe é configurado para otimizar a utilização desses oito QPs de E/S distribuindo a E/S em uma agenda round robin. Para obter o desempenho máximo, execute oito trabalhos por dispositivo para correspondência.

* Evite misturar comandos de administrador do NVMe (por exemplo, consulta de informações SMART do NVMe etc.) com os comandos de E/S do NVMe durante as cargas de trabalho ativas. Os dispositivos NVMe da série Lsv2 são apoiados pela tecnologia Hyper-V NVMe Direct, que muda para o "modo lento" sempre que qualquer comando de administração do NVMe estiver pendente. Os usuários da série Lsv2 poderão ver uma queda significativa no desempenho de E/S do NVMe, se isso acontecer.

* Os usuários da série Lsv2 não devem confiar nas informações NUMA do dispositivo (todas 0) relatadas de dentro da VM para unidades de dados para decidir a afinidade NUMA para seus aplicativos. A maneira recomendada para melhor desempenho é distribuir cargas de trabalho entre CPUs, se possível. 

* A profundidade máxima de fila com suporte por par de filas de E/S para o dispositivo NVMe da VM da série Lsv2 é 1024 (em comparação com o limite de 32 do Amazon i3 QD). Os usuários da série Lsv2 devem limitar suas cargas de trabalho de parâmetro de comparação (sintéticas) para a profundidade de fila de 1.024, ou inferior, para evitar o disparo de condições completas da fila, o que pode reduzir o desempenho.

## <a name="utilizing-local-nvme-storage"></a>Como utilizar o armazenamento de NVMe local

O armazenamento local no disco NVMe de 1,92 TB em todas as VMs da série Lsv2 é efêmero. Durante uma reinicialização padrão bem-sucedida da VM, os dados no disco NVMe local devem persistir. Os dados não serão persistidos no NVMe se a VM for reimplantada, desalocada ou excluída. Os dados não serão persistidos se outro problema fizer a VM, ou o hardware em que ela está sendo executada, se tornar não íntegra. Quando isso acontece, todos os dados no host antigo são apagados com segurança.

Também haverá casos em que a VM precisará ser movida para um computador host diferente, por exemplo, durante uma operação de manutenção planejada. As operações de manutenção planejada e algumas falhas de hardware podem ser antecipadas com os [Eventos Agendados](scheduled-events.md). Os Eventos Agendados devem ser usados para manter-se atualizado das operações previstas de manutenção e recuperação.

No caso de um evento de manutenção planejada exigir que a VM seja recriada em um novo host com discos locais vazios, os dados precisarão ser ressincronizados (novamente, com todos os dados no host antigo sendo apagados com segurança). Isso ocorre porque as VMs da série Lsv2 atualmente não dão suporte à migração ao vivo no disco NVMe local.

Há dois modos de manutenção planejada.

### <a name="standard-vm-customer-controlled-maintenance"></a>Manutenção controlada pelo cliente da VM padrão

- A VM é movida para um host atualizado durante uma janela de 30 dias.
- Os dados de armazenamento local da série Lsv2 podem ser perdidos; portanto, é recomendável fazer backup dos dados antes do evento.

### <a name="automatic-maintenance"></a>Manutenção automática

- Ocorre se o cliente não executa a manutenção controlada por ele ou no caso de procedimentos de emergência, como um evento de dia zero de segurança.
- Destina-se a preservar os dados do cliente, mas há um pequeno risco de um congelamento ou reinicialização da VM.
- Os dados de armazenamento local da série Lsv2 podem ser perdidos; portanto, é recomendável fazer backup dos dados antes do evento.

Para qualquer evento de serviço futuro, use o processo de manutenção controlada para selecionar um horário mais conveniente para a atualização. Antes do evento, você pode fazer backup de seus dados no armazenamento Premium. Após a conclusão do evento de manutenção, você poderá retornar seus dados para o armazenamento de NVMe local das VMs da série Lsv2 atualizadas.

Os cenários que mantêm dados em discos NVMe locais incluem:

- A VM está em execução e é íntegra.
- A VM é reinicializada no local (por você ou pelo Azure).
- A VM está em pausa (parada sem desalocação).
- A maioria das operações de serviço de manutenção planejada.

Os cenários que apagam dados com segurança para proteger o cliente incluem:

- A VM é reimplantada, parada (desalocada) ou excluída (por você).
- A VM se torna não íntegra e tem de reparar o serviço para outro nó devido a um problema de hardware.
- Um pequeno número de operações de serviço de manutenção planejada que exige que a VM seja realocada para outro host para manutenção.

Para saber mais sobre as opções de backup de dados no armazenamento local, consulte [Backup e recuperação de desastre para discos IaaS do Azure](../backup-and-disaster-recovery-for-azure-iaas-disks.md).

## <a name="frequently-asked-questions"></a>Perguntas frequentes

* **Como começo a implantação de VMs da série Lsv2?**  
   Assim como qualquer outra VM, use o [Portal](quick-create-portal.md), a [CLI do Azure](quick-create-cli.md) ou o [PowerShell](quick-create-powershell.md) para criar uma VM.

* **Uma única falha de disco de NVMe fará com que todas as VMs no host falhem?**  
   Se uma falha de disco for detectada no nó de hardware, o hardware estará em um estado de falha. Quando isso ocorre, todas as VMs no nó são automaticamente desalocadas e movidas para um nó íntegro. Para VMs da série Lsv2, isso significa que os dados do cliente no nó com falha também serão apagados com segurança e precisarão ser recriados pelo cliente no novo nó. Conforme observado, antes que a migração ao vivo se torne disponível no Lsv2, os dados no nó com falha serão movidos proativamente com as VMs à medida que forem transferidas para outro nó.

* **É necessário fazer ajustes de sondagem no Windows Server 2012 ou no Windows Server 2016?**  
   A sondagem do NVMe só está disponível no Windows Server 2019 no Azure.  

* **Posso voltar para um modelo de ISR (rotina de serviço de interrupção) tradicional?**  
   As VMs da série Lsv2 são otimizadas para sondagem de NVMe. As atualizações são fornecidas continuamente para melhorar o desempenho de sondagem.

* **Posso ajustar as configurações de sondagem no Windows Server 2019?**  
   As configurações de sondagem não são ajustáveis pelo usuário.
   
## <a name="next-steps"></a>Próximas etapas

* Consulte as especificações para todas as [VMs otimizadas para desempenho de armazenamento](../sizes-storage.md) no Azure
