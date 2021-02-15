---
title: 'Trabalhando remotamente usando bastiões: bastiões do Azure'
description: Esta página descreve como você pode aproveitar a bastiões do Azure para habilitar o trabalho remotamente devido ao COVID-19 pandemia.
services: bastion
author: mialdrid
ms.service: bastion
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mialdrid
ms.openlocfilehash: 515a6f7175aee23c6f720258b7fdaefe76b3b5d6
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92077942"
---
# <a name="working-remotely-using-azure-bastion"></a>Trabalhando remotamente usando a bastiões do Azure

A bastiões do Azure exerce uma função dinâmica no suporte a cenários de trabalho remoto, permitindo que os usuários com conectividade com a Internet acessem as máquinas virtuais do Azure. Em particular, ele permite que os administradores de ti gerenciem seus aplicativos em execução no Azure a qualquer momento e de qualquer lugar em todo o mundo.

>[!NOTE]
>Este artigo descreve como você pode aproveitar a bastiões do Azure, o Azure, a rede da Microsoft e o ecossistema de parceiros do Azure para trabalhar remotamente e atenuar os problemas de rede que você está enfrentando devido à crise COVID-19.
>

## <a name="securely-access-virtual-machines"></a>Acessar máquinas virtuais com segurança

Especificamente, a bastiões do Azure fornece conectividade RDP/SSH segura e direta para máquinas virtuais na rede virtual do Azure, diretamente no portal do Azure, sem o uso de um endereço IP público. Para obter mais informações sobre a arquitetura de bastiões do Azure e os principais recursos, confira [o que é a bastiões do Azure](bastion-overview.md).

A bastiões do Azure é implantada por rede virtual, o que significa que as empresas podem configurar e gerenciar uma bastiões do Azure para dar suporte rápido ao acesso de usuário remoto a máquinas virtuais em uma rede virtual do Azure. Para obter orientação sobre como criar e gerenciar a bastiões do Azure, consulte [criar um host de bastiões](./tutorial-create-host-portal.md).

## <a name="next-steps"></a>Próximas etapas

* Configure a bastiões do Azure usando o [portal do Azure](./tutorial-create-host-portal.md), o [PowerShell](bastion-create-host-powershell.md)ou o CLI do Azure.

* Leia as [perguntas frequentes sobre bastiões](bastion-faq.md) para obter informações adicionais.