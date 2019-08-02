---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 8861396db6f6b680ddb55ce020e5579dc25b118e
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171875"
---
É importante perceber que há duas maneiras de configurar um ouvinte do grupo de disponibilidade no Azure. Esses métodos são diferentes do tipo de balanceador de carga do Azure que você utiliza ao criar o ouvinte. A tabela a seguir lista as diferenças:

| Tipo de balanceador de carga | Implementação | Use quando: |
| --- | --- | --- |
| **Externo** |Usa o *endereço de IP virtual público* do serviço de nuvem que hospeda as máquinas virtuais (VMs). |Você precisa acessar o ouvinte de fora da rede virtual, inclusive da Internet. |
| **Interna** |Usa um *balanceador de carga interno* com um endereço privado para o ouvinte. |Você pode acessar o ouvinte somente de dentro da mesma rede virtual. Esse acesso inclui VPN site a site em cenários híbridos. |

> [!IMPORTANT]
> Para um ouvinte que usa o VIP público (balanceador de carga externo) do serviço de nuvem, desde que o cliente, o ouvinte e os bancos de dados estejam na mesma região do Azure, você não incorrerá em encargos de saída. Caso contrário, os dados retornados através do ouvinte são considerados dados de saída e serão cobradas as taxas de transferência de dados normais. 
> 
> 

Um ILB pode ser configurado somente em redes virtuais com um escopo regional. Redes virtuais existentes configuradas para um grupo de afinidades não podem usar um ILB. Para obter mais informações, consulte [Visão geral do balanceador de carga interno](../articles/load-balancer/load-balancer-internal-overview.md).

