---
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 11/09/2018
ms.author: genli
ms.openlocfilehash: 3df4108907a4e1e65a444faf1049163966b7accf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "67171844"
---
## <a name="scenario"></a>Cenário
Uma VM com uma única NIC é criada e conectada a uma rede virtual. A VM requer três endereços IP *privados* diferentes e dois endereços IP *públicos*. Os endereços IP são atribuídos às seguintes configurações de IP:

* **IPConfig-1:** atribui um endereço IP privado *estático* e um endereço IP público *estático*.
* **IPConfig-2:** atribui um endereço IP privado *estático* e um endereço IP público *estático*.
* **IPConfig-3:** atribui um endereço IP privado *estático*, mas nenhum endereço IP público.
  
    ![Vários endereços IP](./media/virtual-network-multiple-ip-addresses-scenario/multiple-ipconfigs.png)

As configurações de IP são associadas à NIC quando a NIC é criada e a NIC é conectada à máquina Virtual quando a VM é criada. Os tipos de endereços IP usados para o cenário são para fins de ilustração. Você pode atribuir quaisquer tipos de atribuição e de endereço IP necessários.

> [!NOTE]
> Embora as etapas neste artigo atribuam todas as configurações de IP a uma única NIC, você também pode atribuir várias configurações de IP para qualquer NIC em uma VM. Para saber como criar uma VM com vários NICs, leia o artigo [Criar uma VM com vários NICs](../articles/virtual-machines/windows/multiple-nics.md).