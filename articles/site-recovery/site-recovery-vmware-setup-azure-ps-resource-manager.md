---
title: " Gerenciar um servidor de processo em execução no Azure (Gerenciador de Recursos) | Microsoft Docs"
description: Este artigo descreve como configurar um servidor de processo de failback (Resource Manager) no Azure.
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/29/2017
ms.author: anoopkv
ms.openlocfilehash: 2b9b31abd5d11d02935a74e47d26be9803cdc920
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-a-process-server-running-in-azure-resource-manager"></a>Gerenciar um servidor de processo em execução no Azure (Gerenciador de recursos)
> [!div class="op_single_selector"]
> * [Gerenciador de Recursos](./site-recovery-vmware-setup-azure-ps-resource-manager.md)
> * [Clássico](./site-recovery-vmware-setup-azure-ps-classic.md)

Durante o failback, é recomendável implantar o servidor de processo no Azure se houver alta latência entre a rede Virtual do Azure e sua rede local. Este artigo descreve como você pode definir, configurar e gerenciar os servidores de processo em execução no Azure.

> [!NOTE]
> Use este artigo se você tiver utilizado o **Resource Manager** como o modelo de implantação para as máquinas virtuais durante o failover. Se você usou **Clássico** como o modelo de implantação, siga as etapas em [Como configurar um servidor de processo de Failback (Clássico)](./site-recovery-vmware-setup-azure-ps-classic.md)

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [site-recovery-vmware-process-server-prerequ](../../includes/site-recovery-vmware-azure-process-server-prereq.md)]

## <a name="deploy-a-process-server-on-azure"></a>Implantar um servidor de processos no Azure
1. Em Cofre > **Infraestrutura do Site Recovery** (no cabeçalho “Gerenciar”) > **Servidores de Configuração** (no cabeçalho “Para VMware e Máquinas Físicas”), selecione o servidor de configuração.
2. Na página de detalhes do servidor de configuração que é aberta, clique em "+ servidor de processo"

  ![Adicionar a Galeria do servidor de processo](./media/site-recovery-vmware-setup-azure-ps-arm/add-ps.png)

3.  Sobre o **Adicionar servidor de processo** , selecione os seguintes valores

  ![Adicionar item da galeria do servidor de processo](./media/site-recovery-vmware-setup-azure-ps-arm/add-ps-page-1.png)
|**Nome do Campo**|**Valor**|
|-|-|
|Escolha onde você deseja implantar o servidor de processo|Selecione o valor **implantar um servidor de processo de failback no Azure** |
|Assinatura|Selecione a assinatura do Azure onde você fez failover das máquinas virtuais|
|Grupo de recursos|Você pode criar um grupo de recursos para implantar este servidor de processo ou optar por implantar o servidor de processo em um grupo de recursos existente|
|Local|Selecione o Data Center do Azure no qual as máquinas virtuais onde falhou em|
|Rede do Azure|Selecione o Network(VNet) Virtual do Azure que as máquinas virtuais onde o failover em. Se você fez failover máquinas virtuais em vários VNets do Azure, em seguida, é necessário um servidor de processo implantado por rede virtual|

4. Preencha o restante das propriedades do servidor de processo

  ![Adicionar servidor de processo resumo](./media/site-recovery-vmware-setup-azure-ps-arm/add-ps-page-2.png)
|**Nome do Campo**|**Valor**|
|-|-|
|Nome do Servidor|Nome de exibição < / nome de Host para sua máquina de virtual do servidor de processo|
| Nome de usuário|Um nome de usuário que se torna um administrador na máquina virtual|
|Conta de armazenamento|Nome da conta de armazenamento em que é colocado do disco virtual da máquina virtual|
|Sub-rede|A sub-rede da rede virtual do Azure à qual a máquina virtual está conectada|
| Endereço IP|Endereço IP que você deseja que o servidor de processo uma vez assumi-lo inicializado|
5. Clique no botão Okey para iniciar a máquina virtual do processo servidor de implantação.

> [!NOTE]
> Para poder usar este servidor de processo para failback, você precisa registrá-lo com o servidor de configuração local.

## <a name="registering-the-process-server-running-in-azure-to-a-configuration-server-running-on-premises"></a>Registrando o servidor de processo (em execução no Azure) para um servidor de configuração (em execução local)

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

## <a name="upgrading-the-process-server-to-latest-version"></a>Atualizar o servidor de processo para a versão mais recente.

[!INCLUDE [site-recovery-vmware-upgrade-process-server](../../includes/site-recovery-vmware-upgrade-process-server.md)]

## <a name="unregistering-the-process-server-running-in-azure-from-a-configuration-server-running-on-premises"></a>Cancelando o registro de servidor de processo (em execução no Azure) de um servidor de configuração (em execução local)

[!INCLUDE [site-recovery-vmware-unregister-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]
