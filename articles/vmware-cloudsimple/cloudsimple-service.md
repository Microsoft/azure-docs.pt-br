---
title: Solução VMware pelo serviço CloudSimple
description: Descreve a visão geral do serviço CloudSimple e conceitos.
author: sharaths-cs
ms.author: b-shsury
ms.date: 04/24/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0bebc68129ee2ff79241bcefec1ce0c3ca0b472d
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595306"
---
# <a name="cloudsimple-service-overview"></a>Visão geral do serviço CloudSimple

Com o serviço CloudSimple, você pode usar a solução CloudSimple do VMware do Azure. Depois de criar o serviço, você pode provisionar nós, reserva de nós e criar nuvens privadas. Você pode criar o serviço CloudSimple em cada região do Azure onde o serviço CloudSimple está disponível. O serviço define a rede de borda da solução CloudSimple do VMware do Azure. A rede de borda dá suporte a serviços que incluem o VPN, ExpressRoute do Azure e conectividade à internet em suas nuvens privadas.

## <a name="gateway-subnet"></a>Gateway de sub-rede

Uma sub-rede de gateway é necessária por serviço CloudSimple e é exclusiva para a região na qual ele é criado. A sub-rede de gateway é usada quando você cria a rede de borda e requer uma de/28 bloco CIDR. O espaço de endereço de sub-rede de gateway deve ser exclusivo. Ele não deve sobrepor qualquer rede que se comunica com o ambiente CloudSimple. As redes que se comunicam com CloudSimple incluem redes locais e redes virtuais do Azure. Uma sub-rede de gateway não pode ser excluída depois que ele é criado. A sub-rede de gateway é removida quando o serviço é excluído.

## <a name="next-steps"></a>Próximas etapas

* Saiba como [criar um serviço CloudSimple no Azure](quickstart-create-cloudsimple-service.md).
