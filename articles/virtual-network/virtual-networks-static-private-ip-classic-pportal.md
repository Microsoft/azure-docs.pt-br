---
title: Configurar endereços IP para máquinas virtuais (Clássicas) - Portal do Azure | Microsoft Docs
description: Saiba como configurar endereços IP para máquinas virtuais (Clássico) usando o Portal do Azure.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
tags: azure-service-management
ms.assetid: b8ef8367-58b2-42df-9f26-3269980950b8
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6986f6f16cbd32d44223bba4f4be4577fa11258c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98222897"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-the-azure-portal"></a>Configurar endereços IP particulares para uma máquina virtual (Clássica) usando o Portal do Azure

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Este artigo aborda o modelo de implantação clássico. Você também pode [gerenciar um endereço IP privado estático no modelo de implantação do Gerenciador de Recursos](virtual-networks-static-private-ip-arm-pportal.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

As etapas de exemplo a seguir esperam um ambiente simples já criado. Se você quiser executar as etapas da forma como elas aparecem neste documento, primeiro crie o ambiente de teste descrito em [criar uma vnet](/previous-versions/azure/virtual-network/virtual-networks-create-vnet-classic-pportal).

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Como especificar um endereço IP privado estático ao criar uma VM
Para criar uma VM denominada *DNS01* na sub-rede *FrontEnd* de uma VNet chamada *TestVNet* com o endereço IP privado estático *192.168.1.101*, conclua as seguintes etapas:

1. Em um navegador, navegue até https://portal.azure.com e, se necessário, entre com sua conta do Azure.
2. Selecione **novo**  >  **computação**  >  **Windows Server 2012 R2 Datacenter**, observe que a lista **selecionar um modelo de implantação** já mostra o **clássico** e, em seguida, selecione **criar**.
   
    ![Captura de tela que mostra a portal do Azure com o novo > computação > bloco do datacenter do Windows Server 2012 R2 realçado.](./media/virtual-networks-static-ip-classic-pportal/figure01.png)
3. Em **Criar VM**, insira o nome da VM a ser criada (*DNS01* no cenário), a conta de administrador local e a senha.
   
    ![Captura de tela que mostra como criar uma VM inserindo o nome da VM, o nome de usuário do administrador local e a senha.](./media/virtual-networks-static-ip-classic-pportal/figure02.png)
4. Selecione **configuração opcional**  >  **rede rede**  >  **virtual** e, em seguida, selecione **TestVNet**. Se **TestVNet** não estiver disponível, verifique se você está usando o *Centro dos EUA* como local e que você criou o ambiente de teste descrito no início deste artigo.
   
    ![Captura de tela que mostra a configuração opcional > rede > rede virtual > opção TestVNet realçada.](./media/virtual-networks-static-ip-classic-pportal/figure03.png)
5. Em **Rede**, certifique-se de que a sub-rede selecionada atualmente é *FrontEnd*, em seguida, selecione **Endereços IP**, em **Atribuição de endereço IP** selecione **Estático**, e, em seguida, digite *192.168.1.101* para **Endereço IP** conforme mostrado abaixo.
   
    ![Captura de tela que realça o campo IP endereços em que você digita o endereço IP estático.](./media/virtual-networks-static-ip-classic-pportal/figure04.png)    
6. Selecione **OK** em **Endereços IP**, selecione **OK** em **Rede** e, em seguida, selecione **OK** em **Configuração opcional**.
7. Em **Criar VM**, selecione **Criar**. Observe o bloco abaixo exibido no painel:
   
    ![Captura de tela que mostra a criação do bloco do Windows Server 2012 R2 Datacenter.](./media/virtual-networks-static-ip-classic-pportal/figure05.png)

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Como recuperar informações do endereço IP privado estático de uma VM
Para exibir as informações de endereço IP privado estático para a VM criada com as etapas acima, execute as etapas abaixo.

1. Na portal do Azure, selecione **procurar todas as**  >  **máquinas virtuais (clássicas)**  >  **DNS01**  >  **todas as configurações**  >  **endereços IP** e observe a atribuição de endereço IP e o endereço IP, conforme mostrado abaixo.
   
    ![Criar VM no portal do Azure](./media/virtual-networks-static-ip-classic-pportal/figure06.png)

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Como remover um endereço IP privado estático de uma VM

Em **Endereços IP**, selecione **Dinâmico** à direita da **Atribuição de endereço IP**, selecione **Salvar** e, em seguida, selecione **Sim**, conforme mostrado na figura a seguir:
   
![Captura de tela que mostra como remover o endereço IP privado estático de uma VM selecionando dinâmico à direita do rótulo de atribuição de endereço IP.](./media/virtual-networks-static-ip-classic-pportal/figure07.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Como adicionar um endereço IP privado estático a uma VM existente

1. Em **Endereços IP**, mostrado anteriormente, selecione **Estático** à direita de **Atribuição de endereço IP**.
2. Tipo *192.168.1.101* para **Endereço IP**, selecione **Salvar** e, em seguida, selecione **Sim**.

## <a name="set-ip-addresses-within-the-operating-system"></a>Definir endereços IP no sistema operacional

É recomendável que você não atribua estaticamente o IP privado atribuído à máquina virtual do Azure no sistema operacional de uma VM, a menos que seja necessário. Se você definir manualmente o endereço IP privado no sistema operacional, verifique se é o mesmo endereço que o endereço IP privado atribuído à VM do Azure ou se é possível perder a conectividade com a máquina virtual. Nunca atribua manualmente o endereço IP público atribuído a uma máquina virtual do Azure no sistema operacional da máquina virtual.

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre endereços [IP públicos reservados](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) .
* Saiba mais sobre endereços [ILPIP (IP público em nível de instância)](/previous-versions/azure/virtual-network/virtual-networks-instance-level-public-ip) .
* Consulte as [APIs REST de IP reservado](/previous-versions/azure/reference/dn722420(v=azure.100)).