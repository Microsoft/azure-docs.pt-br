---
title: Conheça os termos da infraestrutura de BareMetal do Azure
description: Conheça os termos da infraestrutura de BareMetal do Azure.
ms.topic: conceptual
ms.date: 1/4/2021
ms.openlocfilehash: b22a6cecb2647df3878cb8fd4ade93d9a7d963fd
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104770879"
---
# <a name="know-the-terms-for-baremetal-infrastructure"></a>Conheça os termos da infraestrutura BareMetal

Neste artigo, abordaremos alguns termos importantes do BareMetal.

- **Revisão**: há uma revisão de carimbo original conhecida como revisão 3 (Rev. 3) e duas revisões de carimbo diferentes para carimbos de instância de BareMetal. Cada carimbo difere na arquitetura e na proximidade com os hosts da máquina virtual do Azure:
    - **Revisão 4** (Rev 4): um design mais recente que fornece proximidade com os hosts da VM (máquina virtual) do Azure e reduz a latência entre as máquinas virtuais do Azure e as unidades da instância BareMetal. 
    - **Revisão 4,2** (Rev 4,2): a mais recente estrutura de BareMetal da marca usando a arquitetura Rev 4 existente. A Rev 4 fornece mais proximidade com os hosts da VM (máquina virtual) do Azure. Ele tem melhorias significativas na latência de rede entre as VMs do Azure e as unidades de instância BareMetal implantadas em carimbos de Rev 4 ou linhas. Você pode acessar e gerenciar suas instâncias do BareMetal por meio do portal do Azure.    

- **Stamp**: define o tamanho da implantação interna da Microsoft de instâncias de BareMetal. Antes que as unidades de instância possam ser implantadas, um carimbo de instância BareMetal consistindo em racks de computação, rede e armazenamento deve ser implantado em um datacenter local. Essa implantação é chamada de carimbo de instância BareMetal ou da revisão 4,2.

- **Locatário**: um cliente implantado no carimbo de instância BareMetal é isolado em um *locatário.* Um locatário é isolado de outros locatários na camada de rede, de armazenamento e de computação. As unidades de armazenamento e de computação atribuídas aos diferentes locatários não podem ver um ao outro ou se comunicar umas com as outras no nível de carimbo de instância BareMetal. Um cliente pode escolher ter implantações em diferentes locatários. Mesmo assim, não há nenhuma comunicação entre os locatários no nível de selo da instância de BareMetal.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre a [infraestrutura do BareMetal](concepts-baremetal-infrastructure-overview.md) ou como [identificar e interagir com as unidades da instância do BareMetal](connect-baremetal-infrastructure.md). 