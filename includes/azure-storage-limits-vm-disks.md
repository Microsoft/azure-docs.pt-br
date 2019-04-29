---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/18/2019
ms.author: rogarana
ms.openlocfilehash: 2936fd318f08c74675f7e8b382c861f4a28319fc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60386274"
---
Você pode anexar um número de discos de dados para uma máquina virtual do Azure. Com base nas metas de escalabilidade e desempenho para discos de dados da VM, você pode determinar o número e tipo de disco que você precisa para atender aos seus requisitos de capacidade e desempenho.

> [!IMPORTANT]
> Para obter o desempenho ideal, limite a quantidade de discos altamente utilizados anexados à máquina virtual para evitar possíveis limitações. Se todos os discos conectados não são altamente utilizados ao mesmo tempo, a máquina virtual pode dar suporte a um número maior de discos.

**Discos gerenciados do Azure:**

A tabela a seguir ilustra o padrão e os limites máximo do número de recursos por região e assinatura

> | Resource | Limite padrão  | Limite máximo |
> | --- | --- | --- |
> | Standard Managed Disks | 25.000 | 50.000 |
> | Discos gerenciados SSD Standard | 25.000 | 50.000 |
> | Discos gerenciados Premium | 25.000 | 50.000 |
> | Standard_LRS instantâneos | 25.000 | 50.000 |
> | Standard_ZRS instantâneos | 25.000 | 50.000 |
> | Imagem gerenciada | 25.000 | 50.000 |

* **Para contas de armazenamento padrão:** Uma conta de armazenamento Standard tem uma taxa de solicitação total máxima de 20.000 IOPS. O IOPS total em todos os discos de máquina virtual em uma conta de armazenamento padrão não deve exceder esse limite.
  
    Basicamente, você pode calcular o número de discos altamente utilizados compatíveis com uma conta de armazenamento Standard com base no limite de taxa de solicitação. Por exemplo, para uma VM de camada básica, o número máximo de discos altamente utilizados é aproximadamente 66, que é 20.000/300 IOPS por disco. O número máximo de discos altamente utilizados para uma VM de camada Standard é de aproximadamente 40, que é 20.000/500 IOPS por disco. 

* **Para contas de armazenamento Premium:** Uma conta de armazenamento Premium tem uma taxa de transferência total máxima de 50 Gbps. A taxa de transferência total de todos os discos da VM não deve exceder esse limite.

