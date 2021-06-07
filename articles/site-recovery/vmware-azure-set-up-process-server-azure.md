---
title: Configurar um servidor de processo VMware/failback físico no Azure Site Recovery
description: Este artigo descreve como configurar um servidor em processo no Azure para fazer failback de VMs do Azure para o VMware.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/21/2019
ms.author: ramamill
ms.openlocfilehash: 16214fa07b14c8db2f32fa2c69739b7fa3dc1907
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96008458"
---
# <a name="set-up-a-process-server-in-azure-for-failback"></a>Configurar um servidor em processo no Azure para failback

Depois de fazer failover de servidores físicos ou VMs do VMware no Azure usando o [Site Recovery](site-recovery-overview.md), você pode fazer failover deles no site local quando ele estiver em funcionamento novamente. Para fazer failback, você precisa configurar um servidor em processo temporário no Azure, a fim de lidar com a replicação do Azure local. Você pode excluir essa VM após a conclusão do failback.

## <a name="before-you-start"></a>Antes de começar

Saiba mais sobre o processo de [nova proteção](vmware-azure-reprotect.md) e [failback](vmware-azure-failback.md).

[!INCLUDE [site-recovery-vmware-process-server-prerequ](../../includes/site-recovery-vmware-azure-process-server-prereq.md)]


## <a name="deploy-a-process-server-in-azure"></a>Implantar um servidor em processo no Azure

1. No cofre > **site Recovery infraestrutura** >  **gerenciar**  >  **servidores de configuração**, selecione o servidor de configuração.
2. Na página do servidor, clique em **+ Servidor em processo**
3. Na página **Adicionar servidor em processo**, selecione a opção para implantar o servidor em processo no Azure.
4. Especifique as configurações do Azure, incluindo a assinatura usada para failover, um grupo de recursos, a região do Azure usada para failover e a rede virtual na qual as VMs do Azure estão localizadas. Se você usou várias redes do Azure, você precisa ter um servidor em processo em cada uma.

   ![Adicionar item da galeria do servidor de processo](./media/vmware-azure-set-up-process-server-azure/add-ps-page-1.png)

4. Em **Nome do servidor**, **Nome de usuário** e **Senha**, especifique um nome para o servidor em processo e as credenciais que receberão permissões de Administrador no servidor.
5. Especifique uma conta de armazenamento a ser usada para os discos de VM do servidor, a sub-rede na qual a VM do servidor em processo estará localizada e o endereço IP do servidor que será atribuído quando a VM for iniciada.
6. Clique no botão **OK** para começar a implantar a VM do servidor em processo. O servidor de processo será implantado em Standard_A8_v2 SKU. Verifique se esse SKU de VM está disponível para sua assinatura.

>

## <a name="registering-the-process-server-running-in-azure-to-a-configuration-server-running-on-premises"></a>Registrando o servidor de processo (em execução no Azure) para um servidor de configuração (em execução local)

Depois que a VM do servidor em processo estiver em funcionamento, você precisa registrá-la no servidor de configuração local, da seguinte maneira:

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]


