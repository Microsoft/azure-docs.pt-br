---
title: Otimizar o desempenho em máquinas de virtuais do Azure da série Lsv2 - armazenamento | Microsoft Docs
description: Aprenda a otimizar o desempenho de sua solução nas máquinas virtuais série Lsv2.
services: virtual-machines-windows
author: laurenhughes
manager: jeconnoc
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/17/2019
ms.author: joelpell
ms.openlocfilehash: f84e81a5a9e9c9cf6f477adefa0869d776f7dd71
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61487334"
---
# <a name="optimize-performance-on-the-lsv2-series-virtual-machines"></a>Otimizar o desempenho em máquinas virtuais série Lsv2

Máquinas virtuais da série Lsv2 suporte a uma variedade de cargas de trabalho que precisam de alta e/s e taxa de transferência de armazenamento local em uma ampla variedade de aplicativos e setores.  A série Lsv2 é ideal para Big Data, SQL, NoSQL bancos de dados, data warehouse e grandes bancos de dados transacionais, incluindo o Cassandra, MongoDB, Cloudera e Redis.

O design das máquinas virtuais (VMs) série Lsv2 maximiza o processador AMD EPYC™ 7551 para fornecer o melhor desempenho entre o processador, memória, dispositivos NVMe e as VMs. Além de maximizar o desempenho de hardware, as VMs da série Lsv2 são projetadas para trabalhar com as necessidades dos sistemas operacionais Windows e Linux para um melhor desempenho com o hardware e software.

Ajuste de software e hardware resultou na versão otimizada [Datacenter do Windows Server 2019](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WindowsServer?tab=Overview), lançado no início de dezembro de 2018 no Azure Marketplace, que dá suporte ao máximo de desempenho nos dispositivos NVMe série Lsv2 VMs.

Este artigo fornece dicas e sugestões para garantir que seus aplicativos e cargas de trabalho atingir o máximo desempenho projetado para as VMs. As informações nesta página serão atualizadas continuamente conforme mais imagens Lsv2 otimizado são adicionadas ao Azure Marketplace.

## <a name="amd-eypc-chipset-architecture"></a>Arquitetura de chipset AMD EYPC™

As VMs da série Lsv2 usam AMD EYPC™ processadores de servidor com base na microarquitetura Zen. AMD desenvolvido Fabric infinito (se) para EYPC™ como escalonável interconexão de seu modelo de NUMA que pode ser usado para comunicações no chip, no pacote e vários pacotes. Em comparação com a QPI (interconexão de caminho rápido) e UPI (Ultra-Path interconexão) usado em processadores modernos de chip monolítico Intel, arquitetura de chip pequeno muitos da AMD pode colocar ambos os benefícios de desempenho, bem como os desafios. O impacto real de restrições de largura de banda e latência de memória pode variar dependendo do tipo de cargas de trabalho em execução.

## <a name="tips-for-maximizing-performance"></a>Dicas para maximizar o desempenho

* O hardware que capacita as VMs da série Lsv2 utiliza dispositivos NVMe com oito s de pares de fila de e/s (QP). Cada fila de e/s do dispositivo NVMe é, na verdade, um par: uma fila de envio e uma fila de conclusão. O driver NVMe está configurado para otimizar a utilização de um desses oito QPs de e/s, distribuindo eu / S em um round robin agendar. Para obter o máximo desempenho, execute trabalhos de oito por dispositivo para corresponder.

* Evite misturar NVMe comandos de administrador (por exemplo, consulta de informações NVMe inteligente, etc.) com os comandos de e/s de NVMe durante cargas de trabalho ativas. Dispositivos NVMe Lsv2 são apoiados por tecnologia Hyper-V NVMe direto, que alterna para o "modo lento" sempre que qualquer comando de administrador NVMe está pendente. Usuários Lsv2 poderiam ver expressivas de desempenho drop no desempenho de e/s de NVMe, se isso acontecer.

* Usuários Lsv2 não devem confiar nas informações de de dispositivo (todos os 0) relatadas de dentro da VM para unidades de dados para decidir a afinidade a para seus aplicativos. A maneira recomendada para melhorar o desempenho é distribuir as cargas de trabalho entre CPUs se possível. 

* A profundidade de máximo da fila com suporte por par de fila de e/s para o dispositivo Lsv2 VM NVMe é 1024 (vs. Amazon i3 QD 32 limite). Os usuários de Lsv2 devem limitar suas cargas de trabalho de parâmetro de comparação (sintéticas) a profundidade de fila 1024 ou para baixo para evitar disparar condições de completo em fila, que podem reduzir o desempenho.

## <a name="utilizing-local-nvme-storage"></a>Utilizando o armazenamento local de NVMe

Armazenamento local no disco em todas as VMs Lsv2 NVMe 1,92 TB é efêmero. Durante a reinicialização padrão bem-sucedida da VM, os dados no disco local NVMe serão mantidos. Os dados não persistirão no NVMe, se a VM for reimplantada, desalocada ou excluída. Dados não persistirá se outro problema faz com que a VM ou o hardware que ele está em execução, para se tornar não íntegro. Quando isso acontece, todos os dados no host antigo são apagados com segurança.

Também haverá casos quando a VM precisa ser movido para uma máquina de host diferente, por exemplo, durante uma operação de manutenção planejada. Operações de manutenção planejada e algumas falhas de hardware podem ser previstas com [eventos agendados](scheduled-events.md). Eventos agendados devem ser usados para se manter atualizado em qualquer manutenção prevista e operações de recuperação.

No caso em que um evento de manutenção planejada requer que a VM a ser recriado em um novo host com discos locais vazios, os dados precisam ser ressincronizada (novamente, com todos os dados no host antigo que está sendo apagado com segurança). Isso ocorre porque as VMs da série Lsv2 não damos suporte a migração ao vivo no disco local NVMe.

Há dois modos para manutenção planejada.

### <a name="standard-vm-customer-controlled-maintenance"></a>Manutenção de controladas pelo cliente VM padrão

- A VM for movida para um host atualizado durante uma janela de 30 dias.
- Dados de armazenamento local Lsv2 pode ser perdidos, portanto, é recomendável fazer o backup dados antes do evento.

### <a name="automatic-maintenance"></a>Manutenção automática

- Ocorre se o cliente não executar manutenção controladas pelo cliente ou no caso de procedimentos de emergência, como um evento de dia zero de segurança.
- Se destina a preservar os dados do cliente, mas há um pequeno risco de um congelamento VM ou a reinicialização.
- Dados de armazenamento local Lsv2 pode ser perdidos, portanto, é recomendável fazer o backup dados antes do evento.

Para todos os eventos futuros do serviço, use o processo de manutenção controlada para selecionar um horário mais conveniente para você para a atualização. Antes do evento, você pode fazer backup dos dados no armazenamento premium. Depois que o evento de manutenção for concluída, você pode retornar seus dados para atualizado Lsv2 VMs NVMe armazenamento local.

Os cenários que mantêm os dados em discos de NVMe locais incluem:

- A VM está em execução e íntegro.
- A VM é reinicializada em vigor (por você ou do Azure).
- A VM está em pausa (interrompida sem desalocação).
- A maioria da manutenção planejada, as operações de serviço.

Os cenários que apagar com segurança os dados para proteger o cliente incluem:

- A VM for reimplantada, interrompida (desalocada,) ou excluída (por você).
- A VM se tornará não íntegra e tem-se ao serviço são corrigidas para outro nó devido a um problema de hardware.
- Um número pequeno da manutenção planejada, as operações de serviço que requer a VM ser realocadas para outro host para manutenção.

Para saber mais sobre opções de backup de dados no armazenamento local, consulte [Backup e recuperação de desastre para discos de IaaS do Azure](backup-and-disaster-recovery-for-azure-iaas-disks.md).

## <a name="frequently-asked-questions"></a>Perguntas frequentes

* **Como iniciar a implantação de VMs da série Lsv2?**  
   Bem como qualquer outra VM, use o [Portal](quick-create-portal.md), [CLI do Azure](quick-create-cli.md), ou [PowerShell](quick-create-powershell.md) para criar uma VM.

* **Uma única falha de disco NVMe fará com que todas as máquinas virtuais no host falha?**  
   Se uma falha de disco for detectada no nó de hardware, o hardware está em um estado de falha. Quando isso ocorrer, todas as VMs no nó são desalocadas automaticamente e movidas para um nó íntegro. Para VMs da série Lsv2, isso significa que os dados do cliente no nó com falha são apagados também com segurança e precisarão ser recriada pelo cliente no novo nó. Conforme observado, antes da migração ao vivo se torna disponível em Lsv2, os dados no nó com falha serão ser movidos proativamente com as VMs como eles são transferidos para outro nó.

* **É necessário fazer ajustes de sondagem no Windows no Windows Server 2012 ou Windows Server 2016?**  
   Sondagem de NVMe só está disponível no Windows Server 2019 no Azure.  

* **Posso mudar para um modelo de rotina de serviço de interrupção tradicional (ISR)?**  
   As VMs da série Lsv2 são otimizadas para NVMe de sondagem. As atualizações são fornecidas continuamente para melhorar o desempenho de sondagem.

* **Posso ajustar as configurações de sondagem no Windows Server 2019?**  
   As configurações de sondagem não são ajustáveis do usuário.
   
## <a name="next-steps"></a>Próximas etapas

* Consulte as especificações para todos os [VMs otimizadas para desempenho de armazenamento](sizes-storage.md) no Azure
