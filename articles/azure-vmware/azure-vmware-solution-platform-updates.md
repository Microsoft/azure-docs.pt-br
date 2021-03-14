---
title: Atualizações de plataforma para a solução do Azure VMware
description: Saiba mais sobre as atualizações de plataforma para a solução do Azure VMware.
ms.topic: reference
ms.date: 03/05/2021
ms.openlocfilehash: 1f1a0c29ffde20b54abb9e4d1d1127fc93a712d9
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102444034"
---
# <a name="platform-updates-for-azure-vmware-solution"></a>Atualizações de plataforma para a solução do Azure VMware


## <a name="march-4-2021"></a>4 de março de 2021

Atualizações importantes para soluções do Azure VMware serão aplicadas a partir de março de 2021. Você receberá uma notificação por meio da integridade do serviço do Azure que inclui a linha do tempo da manutenção. Neste artigo, você aprende o que esperar durante essa operação de manutenção e as alterações em sua nuvem privada.

- As soluções do Azure VMware aplicarão patches ao ESXi em nuvens privadas existentes para [VMware ESXi 6,7, versão de patch ESXi670-202011002](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/esxi670-202011002.html) até 15 de março de 2021.

- As soluções alternativas documentadas para a pilha vSphere, de acordo com o [VMSA-2021-0002](https://www.vmware.com/security/advisories/VMSA-2021-0002.html), também serão aplicadas até 15 de março de 2021.

>[!NOTE]
>Isso não causa interrupções e não deve afetar os serviços ou cargas de trabalho do Azure VMware. Durante a manutenção, vários alertas do VMware, como a _conectividade de rede perdida em DVPorts_ e a _redundância de uplink perdida em DVPorts_, aparecem no vCenter e são desmarcados automaticamente à medida que a manutenção progride.


## <a name="post-update"></a>Pós-atualização
Após a conclusão, as versões mais recentes dos componentes do VMware serão exibidas. Se você perceber algum problema ou tiver dúvidas, entre em contato com nossa equipe de suporte abrindo um tíquete de suporte.



