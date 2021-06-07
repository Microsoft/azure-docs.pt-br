---
title: Atualizações de plataforma para a solução do Azure VMware
description: Saiba mais sobre as atualizações de plataforma para a solução do Azure VMware.
ms.topic: reference
ms.date: 03/24/2021
ms.openlocfilehash: da6317d49edd3f40e1a8f2518f91fe353bbae285
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105045205"
---
# <a name="platform-updates-for-azure-vmware-solution"></a>Atualizações de plataforma para a solução do Azure VMware

A solução Azure VMware aplicará atualizações importantes a partir de março de 2021. Você receberá uma notificação por meio da integridade do serviço do Azure que inclui a linha do tempo da manutenção. Para obter mais informações, consulte [atualização e atualizações na nuvem privada da solução Azure VMware](concepts-upgrades.md).

## <a name="march-24-2021"></a>24 de março de 2021
Todas as novas nuvens privadas da solução Azure VMware são implantadas com o VMware vCenter versão 6.7 U3l e NSX-T versão 3.1.1. Todas as nuvens privadas existentes serão atualizadas e atualizadas **até junho de 2021** para as versões mencionadas acima.

Você receberá um email com a data e a hora de manutenção planejada. Você pode reagendar uma atualização. O email também fornece detalhes sobre o componente atualizado, seu efeito sobre cargas de trabalho, acesso à nuvem privada e outros serviços do Azure.  Uma hora antes da atualização, você receberá uma notificação e novamente quando ela for concluída.

## <a name="march-15-2021"></a>15 de março de 2021 

- O serviço de solução do Azure VMware fará a manutenção funcionar **até 19 de março de 2021,** para atualizar o servidor do vCenter em sua nuvem privada para vCenter Server versão 6,7 da atualização 3L.

- O VMware vCenter não estará disponível durante esse tempo.  Portanto, você não poderá gerenciar suas VMs (parar, iniciar, criar, excluir) ou dimensionar a nuvem privada (adicionando/removendo servidores e clusters). No entanto, a HA (alta disponibilidade) do VMware continuará a operar para proteger as VMs existentes. 
 
Para obter mais informações sobre esta versão do vCenter, consulte [VMware vCenter Server 6,7 Update 3L Release Notes](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/vsphere-vcenter-server-67u3l-release-notes.html).

## <a name="march-4-2021"></a>4 de março de 2021

- A solução Azure VMware aplicará o [VMware ESXi 6,7, a versão de patch ESXi670-202011002](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/esxi670-202011002.html) a particularidades existentes **até 15 de março de 2021**.

- As soluções alternativas documentadas para a pilha vSphere, de acordo com o [VMSA-2021-0002](https://www.vmware.com/security/advisories/VMSA-2021-0002.html), também serão aplicadas **até 15 de março de 2021**.

>[!NOTE]
>Isso não causa interrupções e não deve afetar os serviços ou cargas de trabalho do Azure VMware. Durante a manutenção, vários alertas do VMware, como a _conectividade de rede perdida em DVPorts_ e a _redundância de uplink perdida em DVPorts_, aparecem no vCenter e são desmarcados automaticamente à medida que a manutenção progride.

## <a name="post-update"></a>Pós-atualização
Após a conclusão, as versões mais recentes dos componentes do VMware serão exibidas. Se você perceber algum problema ou tiver dúvidas, entre em contato com nossa equipe de suporte abrindo um tíquete de suporte.