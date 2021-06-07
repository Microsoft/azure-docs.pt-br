---
title: Criar uma VM zoneada com o portal do Azure
description: Crie uma VM do Windows em uma Região de Disponibilidade com o portal do Azure
documentationcenter: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 3/8/2021
ms.author: mimckitt
ms.reviewer: cynthn
ms.custom: ''
ms.openlocfilehash: 7c7f135d4033a31f855342c172d73f51478931ab
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102501678"
---
# <a name="create-a-virtual-machine-in-an-availability-zone-using-the-azure-portal"></a>Criar uma máquina virtual em uma zona de disponibilidade usando o portal do Azure

Este artigo aborda usando o portal do Azure para criar uma máquina virtual em uma região de disponibilidade do Azure. Uma [zona de disponibilidade](../../availability-zones/az-overview.md) é uma zona fisicamente separada em uma região do Azure. Use zonas de disponibilidade para proteger seus aplicativos e dados de uma improvável falha ou perda de um datacenter inteiro.

Para usar uma zona de disponibilidade, crie a máquina virtual em uma [região do Azure com suporte](../../availability-zones/az-region.md).

## <a name="sign-in-to-azure"></a>Entrar no Azure 

Entre no Portal do Azure em https://portal.azure.com.

## <a name="create-virtual-machine"></a>Criar máquina virtual

1. Clique em **Criar um recurso** no canto superior esquerdo do Portal do Azure.

2. Selecione **Computação** e, em seguida, selecione **Windows Server 2016 Datacenter**. 

3. Insira as informações da máquina virtual. O nome do usuário e a senha inseridos aqui são usados para fazer logon na máquina virtual. A senha deve ter no mínimo 12 caracteres e atender a [requisitos de complexidade definidos](faq.md#what-are-the-password-requirements-when-creating-a-vm). Escolha um local como Leste dos EUA 2 que oferece suporte a zonas de disponibilidade. Quando concluído, clique em **OK**.

    ![Insira as informações básicas sobre sua VM na folha do portal](./media/create-portal-availability-zone/create-windows-vm-portal-basic-blade.png)

4. Escolha um tamanho para a VM. Selecione um tamanho recomendado ou filtro com base nos recursos. Confirme se que o tamanho está disponível na zona que você deseja usar.

    ![Selecione um tamanho de VM](./media/create-portal-availability-zone/create-windows-vm-portal-sizes.png)  

5. Em **configurações**  >  **alta disponibilidade**, selecione uma das zonas numeradas na lista suspensa **zona de disponibilidade** , mantenha os padrões restantes e clique em **OK**.

    ![Selecione uma zona de disponibilidade](./media/create-portal-availability-zone/create-windows-vm-portal-availability-zone.png)

6. Na página de resumo, clique em **Criar** para iniciar a implantação da máquina virtual.

7. A VM será fixada ao painel do portal do Azure. Depois que a implantação for concluída, a folha de resumo da VM abrirá automaticamente.

## <a name="confirm-zone-for-managed-disk-and-ip-address"></a>Confirme a zona do disco gerenciado e endereço IP

Quando a VM é implantada em uma zona de disponibilidade, um disco gerenciado para a VM é criado na mesma zona de disponibilidade. Por padrão, um endereço IP público também é criado nessa região.

Você pode confirmar as configurações de zona para esses recursos no portal.  

1. Clique em **Grupos de recursos** e, em seguida, no nome do grupo de recursos da VM, como *myResourceGroup*.

2. Clique no nome do recurso de disco. A página **Visão geral** inclui detalhes sobre a zona local e de disponibilidade do recurso.

    ![Zona de disponibilidade do disco gerenciado](./media/create-portal-availability-zone/create-windows-vm-portal-disk.png)

3. Clique no nome do recurso de endereço IP público. A página **Visão geral** inclui detalhes sobre a zona local e de disponibilidade do recurso.

    ![Zona de disponibilidade do endereço IP](./media/create-portal-availability-zone/create-windows-vm-portal-ip.png)



## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a criar uma VM em uma zona de disponibilidade. Saiba mais sobre a [disponibilidade](../availability.md) de VMs do Azure.