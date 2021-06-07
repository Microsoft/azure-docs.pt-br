---
title: Solução do Azure VMware por CloudSimple-Service
description: Saiba mais sobre o serviço CloudSimple com sua visão geral. A criação do serviço permite que você compre nós, Reserve nós e crie nuvens privadas.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8f32197eda4fc7632e883fd21dd6e1caa0dbd24b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97898735"
---
# <a name="cloudsimple-service-overview"></a>Visão geral do serviço CloudSimple

O serviço CloudSimple permite que você consuma a solução do Azure VMware pelo CloudSimple.  A criação do serviço permite que você compre nós, Reserve nós e crie nuvens privadas.  Você cria o serviço CloudSimple em cada região do Azure onde o serviço CloudSimple está disponível. O serviço define a rede de borda da solução do Azure VMware por CloudSimple. A rede de borda dá suporte a serviços que incluem conectividade VPN, ExpressRoute e Internet para suas nuvens privadas.

## <a name="gateway-subnet"></a>Sub-rede do gateway

Uma sub-rede de gateway é necessária por serviço CloudSimple e é exclusiva para a região na qual ela é criada. A sub-rede de gateway é usada ao criar a rede de borda e requer um bloco CIDR/28.  O espaço de endereço de sub-rede de gateway deve ser exclusivo. Ele não deve se sobrepor a nenhuma rede que se comunica com o ambiente CloudSimple. As redes que se comunicam com o CloudSimple incluem redes locais e rede virtual do Azure.  Uma sub-rede de gateway não pode ser excluída depois de ser criada.  A sub-rede de gateway é removida quando o serviço é excluído.

## <a name="next-steps"></a>Próximas etapas

* Saiba como [criar um serviço CloudSimple no Azure](quickstart-create-cloudsimple-service.md).
