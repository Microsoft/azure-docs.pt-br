---
title: 'Tutorial: adicionar um segmento de rede do NSX-T na Solução VMware no Azure'
description: Saiba como criar um segmento de rede do NSX-T para usar VMs (máquinas virtuais) no vCenter.
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: 9125e552f9641a2d26b9584b66a4447f9c152161
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103462109"
---
# <a name="tutorial-add-a-network-segment-in-azure-vmware-solution"></a>Tutorial: adicionar um segmento de rede na Solução VMware no Azure 

As VMs (máquinas virtuais) criadas no vCenter são colocadas nos segmentos de rede criados no NSX-T e ficam visíveis no vCenter.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Navegar no NSX-T Manager para adicionar segmentos de rede
> * Adicionar um novo segmento de rede
> * Observe o novo segmento de rede no vCenter

## <a name="prerequisites"></a>Pré-requisitos

Ter uma nuvem privada da Solução VMware no Azure com acesso às interfaces do vCenter e do NSX-T Manager. Para obter mais informações, confira o tutorial [Configurar a rede](tutorial-configure-networking.md).

## <a name="add-a-network-segment"></a>Adicionar um segmento de rede

[!INCLUDE [add-network-segment-steps](includes/add-network-segment-steps.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um segmento de rede do NSX-T para usar VMs no vCenter. 

Agora você pode: 

- [Criar e gerenciar o DHCP para a Solução VMware no Azure](manage-dhcp.md)
- [Criar uma Biblioteca de conteúdo para implantar VMs na Solução VMware no Azure](deploy-vm-content-library.md) 
- [Emparelhar ambientes locais com uma nuvem privada](tutorial-expressroute-global-reach-private-cloud.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->
