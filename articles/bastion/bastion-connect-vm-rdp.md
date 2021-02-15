---
title: Conectar-se a uma VM do Windows usando a bastiões do Azure
description: Neste artigo, saiba como se conectar a uma máquina virtual do Azure que executa o Windows usando a bastiões do Azure.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 10/21/2020
ms.author: cherylmc
ms.openlocfilehash: 708bd1f61da2f3973333f8e68cabdceee0717bee
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2020
ms.locfileid: "92521520"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion"></a>Conectar-se a uma máquina virtual do Windows usando a bastiões do Azure

Usando a bastiões do Azure, você pode se conectar de forma segura e direta às suas máquinas virtuais por SSL diretamente no portal do Azure. Quando você usa a bastiões do Azure, suas VMs não exigem um cliente, agente ou software adicional. Este artigo mostra como se conectar às suas VMs do Windows. Para obter informações sobre como se conectar a uma VM do Linux, consulte [conectar-se a uma VM do Linux](bastion-connect-vm-ssh.md).

A bastiões do Azure fornece conectividade segura para todas as VMs na rede virtual em que ela é provisionada. O uso do Azure Bastion protege suas máquinas virtuais contra a exposição das portas RDP/SSH ao mundo externo, fornecendo acesso seguro usando o RDP/o SSH. Para obter mais informações, consulte o [que é a bastiões do Azure?](bastion-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se você atende aos seguintes critérios:

* Uma VNet com o host de bastiões já está instalada.

   Verifique se você configurou um host de bastiões do Azure para a rede virtual na qual a VM está localizada. Depois que o serviço de bastiões for provisionado e implantado em sua rede virtual, você poderá usá-lo para se conectar a qualquer VM na rede virtual. Para configurar um host de bastiões do Azure, consulte [criar um host de bastiões](tutorial-create-host-portal.md#createhost).
* Uma máquina virtual do Windows na rede virtual.
* As seguintes funções obrigatórias:
  * A função de leitor na máquina virtual.
  * A função de leitor na placa de interface de rede com endereço IP privado da máquina virtual.
  * Função de leitor no recurso do Azure Bastion.
* Portas: Para se conectar à VM do Windows, você precisará abrir as seguintes portas nela:
  * Portas de entrada: RDP (3389)

## <a name="connect"></a><a name="rdp"></a>Conectar

[!INCLUDE [Connect to a Windows VM](../../includes/bastion-vm-rdp.md)]
 
## <a name="next-steps"></a>Próximas etapas

Leia as [perguntas frequentes do Bastion](bastion-faq.md).