---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/18/2019
ms.author: rogarana
ms.openlocfilehash: ffb07220267a2c192b4aad2405185c80bd9abbc0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94523901"
---
Você pode anexar um número de discos de dados a uma máquina virtual do Azure. Com base nas metas de escalabilidade e de desempenho dos discos de dados de uma VM, você pode determinar o número e o tipo de disco necessários para atender aos seus requisitos de desempenho e capacidade.

> [!IMPORTANT]
> Para obter o desempenho ideal, limite a quantidade de discos altamente utilizados anexados à máquina virtual para evitar possíveis limitações. Se todos os discos anexados não forem altamente utilizados ao mesmo tempo, a máquina virtual poderá dar suporte a um número maior de discos.

**Para discos gerenciados Azure:**

A tabela a seguir ilustra os limites padrão e máximo do número de recursos por região e assinatura. Os limites continuam os mesmos, independentemente dos discos criptografados com chaves de criptografia gerenciadas pela plataforma ou chaves gerenciadas pelo cliente. Não há nenhum limite para o número de discos gerenciados, instantâneos e imagens por grupo de recursos.  

> | Recurso | Limite |
> | --- | --- |
> | Standard Managed Disks | 50.000 |
> | Discos gerenciados SSD Standard | 50.000 |
> | Discos gerenciados Premium | 50.000 |
> | Instantâneos de Standard_LRS | 50.000 |
> | Instantâneos de Standard_ZRS | 50.000 |
> | Imagem gerenciada | 50.000 |

**Para contas de armazenamento Standard:** uma conta de armazenamento Standard tem uma taxa de solicitação total máxima de 20.000 IOPS. A IOPS total de todos os discos da máquina virtual de uma conta de armazenamento Standard não deve exceder esse limite.
  
Basicamente, você calcula o número de discos altamente utilizados compatíveis com uma conta de armazenamento Standard com base no limite da taxa de solicitação. Por exemplo, para uma VM do nível Básico, o número máximo de discos altamente utilizados é de cerca de 66, sendo 20.000/300 IOPS por disco. O número máximo de discos altamente utilizados para uma VM de nível Standard é de cerca de 40, sendo 20.000/500 IOPS por disco. 

**Para contas de armazenamento Premium:** uma conta de armazenamento Premium tem uma taxa de transferência total máxima de 50 Gbps. A taxa de transferência total de todos os discos da VM não deve exceder esse limite.

