---
title: Visão geral das máquinas virtuais
titleSuffix: Azure VMware Solution by CloudSimple
description: Saiba mais sobre as máquinas virtuais CloudSimple e seus benefícios. Você pode gerenciar máquinas virtuais VMware do portal do Azure.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6f9be035978667287b8b88ec1bb64b3882b0f929
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "88141969"
---
# <a name="cloudsimple-virtual-machines-overview"></a>Visão geral das máquinas virtuais CloudSimple

O CloudSimple permite que você gerencie VMs (máquinas virtuais) VMware do portal do Azure.  Um cluster ou um pool de recursos do seu cluster vSphere é gerenciado por meio do Azure, mapeando-o para sua assinatura.

Para criar uma VM CloudSimple do Azure, um modelo de VM deve existir em seu vCenter de nuvem privada.  O modelo é usado para personalizar o sistema operacional e os aplicativos.  A VM de modelo pode ser protegida para atender às políticas de segurança corporativa.  Você pode usar o modelo para criar VMs e, em seguida, consumi-las do portal do Azure usando um modelo de autoatendimento.

## <a name="benefits"></a>Benefícios

As máquinas virtuais CloudSimple da portal do Azure fornecem um mecanismo de autoatendimento para que os usuários criem e gerenciem máquinas virtuais VMware.

* Criar uma VM CloudSimple em sua nuvem privada vCenter
* Gerenciar Propriedades da VM
  * Adicionar/remover discos
  * Adicionar/remover NICs
* Operações de energia de sua VM CloudSimple
  * Ligar e desligar
  * Reiniciar VM
* Excluir VM

## <a name="next-steps"></a>Próximas etapas

* Saiba como [consumir VMs VMware no Azure](quickstart-create-vmware-virtual-machine.md)
* Saiba como [mapear sua assinatura do Azure](azure-subscription-mapping.md)
