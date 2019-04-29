---
title: Vários endereços IP para máquinas virtuais do Azure – Portal | Microsoft Docs
description: Saiba como atribuir diversos endereços IP a uma máquina virtual usando o Portal do Azure | Resource Manager.
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: 3a8cae97-3bed-430d-91b3-274696d91e34
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/30/2016
ms.author: annahar
ms.openlocfilehash: b1873b770a6b4280b7098c68ecb75cc1411fe453
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60746959"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-portal"></a>Atribuir vários endereços IP a máquinas virtuais usando o Portal do Azure

> [!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]
> 
> Este artigo explica como criar uma máquina virtual (VM) por meio do Modelo de implantação do Azure Resource Manager usando o Portal do Azure. Múltiplos endereços IP não podem ser atribuídos a recursos criados por meio do modelo de implantação clássico. Para saber mais sobre modelos de implantação do Azure, leia o artigo [Compreender os modelos de implantação](../resource-manager-deployment-model.md).

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name = "create"></a>Criar uma VM com vários endereços IP

Se quiser criar uma VM com vários endereços IP ou um endereço IP privado estático, você deverá criá-la usando o PowerShell ou a CLI do Azure. Para saber como fazer isso, clique nas opções PowerShell ou CLI na parte superior deste artigo. Você pode criar uma VM com um único endereço IP privado dinâmico e (opcionalmente) um único endereço IP público. Use o portal seguindo as etapas nos artigos [Criar uma VM do Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md) ou [Criar uma VM do Linux](../virtual-machines/linux/quick-create-portal.md). Depois de criar a VM, você poderá alterar o tipo de endereço IP de dinâmico para estático e adicionar outros endereços IP usando o portal e executando as etapas na seção [Adicionar endereços IP a uma VM](#add) deste artigo.

## <a name="add"></a>Adicionar endereços IP a uma VM

Você pode adicionar endereços IP públicos e privados a uma interface de rede do Azure executando as etapas a seguir. Os exemplos nas seções a seguir pressupõem que você já tem uma VM com as três configurações de IP descritas no [cenário](#scenario), mas isso não é obrigatório.

### <a name="coreadd"></a>Principais etapas

1. Navegue até o portal do Azure em https://portal.azure.com e entre nele, se for necessário.
2. No portal, clique em **Mais serviços** > digite *máquinas virtuais* na caixa de filtro e clique em **Máquinas virtuais**.
3. No painel **Máquinas virtuais**, clique na VM na qual você deseja adicionar os endereços IP. Clique em **Adaptadores de rede** no painel da máquina virtual que aparece e selecione o adaptador de rede na qual você deseja adicionar os endereços IP. No exemplo mostrado na figura a seguir, o NIC chamado *myNIC* da VM denominada *myVM* está selecionada:

    ![interface de rede](./media/virtual-network-multiple-ip-addresses-portal/figure1.png)

4. No painel que é exibido para a NIC selecionada, clique em **Configurações de IP**.

Conclua as etapas em uma das seções a seguir com base no tipo de endereço IP que você deseja adicionar.

### <a name="add-a-private-ip-address"></a>**Adicionar um endereço IP privado**

Conclua as etapas a seguir para adicionar um novo endereço IP privado:

1. Conclua as etapas na seção [Principais etapas](#coreadd) deste artigo.
2. Clique em **Adicionar**. No painel **Adicionar configuração de IP** que é exibido, crie uma configuração de IP denominada *IPConfig-4* com *10.0.0.7* como um endereço IP privado *Estático* e clique em **OK**.

    > [!NOTE]
    > Ao adicionar um endereço IP estático, você deve especificar um endereço válido, não utilizado, na sub-rede a qual a NIC está conectada. Se o endereço selecionado não estiver disponível, o portal mostrará um X para o endereço IP, e você deverá selecionar outro.

3. Ao clicar em OK, o painel será fechado e você verá a nova configuração de IP listada. Clique em **OK** para fechar o painel **Adicionar configuração de IP**.
4. Clique em **Adicionar** para adicionar outras configurações de IP ou feche todas as folhas abertas para concluir a adição de endereços IP.
5. Adicione os endereços IP privados ao sistema operacional da VM executando as etapas para seu sistema operacional na seção [Adicionar endereços IP ao sistema operacional de uma VM](#os-config) deste artigo.

### <a name="add-a-public-ip-address"></a>Adicionar um endereço IP público

Um endereço IP público é adicionado por meio da associação de um recurso de endereço IP público a uma nova configuração de IP ou a uma configuração de IP existente.

> [!NOTE]
> Endereços IP públicos têm um valor nominal. Para saber mais sobre preços de endereço IP, leia a página [Preços de endereço IP](https://azure.microsoft.com/pricing/details/ip-addresses) . Há um limite para o número de endereços IP públicos que podem ser usados em uma assinatura. Para saber mais sobre os limites, leia o artigo [Limites do Azure](../azure-subscription-service-limits.md#networking-limits).
> 

### <a name="create-public-ip"></a>Criar um recurso de endereço IP público

Um endereço IP público é uma configuração para um recurso de endereço IP público. Se você tiver um recurso de endereço IP público que não está associado no momento a uma configuração de IP, e você deseja associá-lo, ignore as etapas a seguir e conclua as etapas em uma das seções seguintes, de acordo com sua necessidade. Se você não tiver um recurso de endereço IP público disponível, conclua as seguintes etapas para criar um:

1. Navegue até o portal do Azure em https://portal.azure.com e entre nele, se for necessário.
3. No portal, clique em **Criar um recurso** > **Rede** > **Endereço IP público**.
4. No painel **Criar endereço IP público** que é exibido, insira um **Nome**, selecione um tipo de **Atribuição de endereço IP**, uma **Assinatura**, um **Grupo de recursos** e um **Local** e, em seguida, clique em **Criar**, conforme mostrado na imagem a seguir:

    ![Criar um recurso de endereço IP público](./media/virtual-network-multiple-ip-addresses-portal/figure5.png)

5. Conclua as etapas em uma das seções a seguir para associar o recurso de endereço IP público a uma configuração de IP.

#### <a name="associate-the-public-ip-address-resource-to-a-new-ip-configuration"></a>Associar o recurso de endereço IP público a uma nova configuração de IP

1. Conclua as etapas na seção [Principais etapas](#coreadd) deste artigo.
2. Clique em **Adicionar**. No painel **Adicionar configuração de IP** que é exibido, crie uma configuração de IP denominada *IPConfig-4*. Habilite o **Endereço IP público** e selecione um recurso de endereço IP público existente e disponível no painel **Escolher endereço IP público** que é exibido.

    Depois de selecionar o recurso de endereço IP público, clique em **OK** e o painel será fechado. Se você não tiver um endereço IP público existente, crie um usando as etapas na seção [Criar um recurso de endereço IP público](#create-public-ip) deste artigo. 

3. Revise a nova configuração de IP. Apesar de um endereço IP privado não ter sido explicitamente atribuído, um foi atribuído automaticamente para a configuração de IP, pois todas as configurações de IP devem ter um endereço IP privado.
4. Clique em **Adicionar** para adicionar outras configurações de IP ou feche todas as folhas abertas para concluir a adição de endereços IP.
5. Adicione o endereço IP privado ao sistema operacional da VM executando as etapas para seu sistema operacional na seção [Adicionar endereços IP ao sistema operacional de uma VM](#os-config) deste artigo. Não adicione o endereço IP público ao sistema operacional.

#### <a name="associate-the-public-ip-address-resource-to-an-existing-ip-configuration"></a>Associar o recurso de endereço IP público a uma configuração de IP existente

1. Conclua as etapas na seção [Principais etapas](#coreadd) deste artigo.
2. Clique na configuração de IP que quer adicionar ao recurso de endereço IP público.
3. No painel IPConfig que é exibido, clique em **Endereço IP**.
4. No painel **Escolher endereço IP público** que é exibido, selecione um endereço IP público.
5. Clique em **Salvar** e o painel é fechado. Se você não tiver um endereço IP público existente, crie um usando as etapas na seção [Criar um recurso de endereço IP público](#create-public-ip) deste artigo.
3. Revise a nova configuração de IP.
4. Clique em **Adicionar** para adicionar outras configurações de IP ou feche todas as folhas abertas para concluir a adição de endereços IP. Não adicione o endereço IP público ao sistema operacional.


[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
