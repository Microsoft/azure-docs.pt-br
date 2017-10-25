---
title: "Configurar endereços IP para máquinas virtuais (Clássicas) - Portal do Azure | Microsoft Docs"
description: "Saiba como configurar endereços IP para máquinas virtuais (Clássico) usando o Portal do Azure."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: b8ef8367-58b2-42df-9f26-3269980950b8
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bde6de3495c2909b63b1f85e420a4ff5e7ac2c1a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-the-azure-portal"></a>Configurar endereços IP particulares para uma máquina virtual (Clássica) usando o Portal do Azure

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Este artigo aborda o modelo de implantação clássico. Você também pode [gerenciar um endereço IP privado estático no modelo de implantação do Gerenciador de Recursos](virtual-networks-static-private-ip-arm-pportal.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

As etapas de exemplo abaixo esperam um ambiente simples já criado. Se você quiser executar as etapas da forma como elas aparecem neste documento, primeiro crie o ambiente de teste descrito em [criar uma vnet](virtual-networks-create-vnet-classic-pportal.md).

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Como especificar um endereço IP privado estático ao criar uma VM
Para criar uma VM denominada *DNS01* na sub-rede *FrontEnd* de uma VNet chamada *TestVNet* com o endereço IP privado estático *192.168.1.101*, execute as etapas abaixo:

1. Em um navegador, navegue até http://portal.azure.com e, se necessário, entre com sua conta do Azure.
2. Clique em **NOVO** > **Computação** > **Windows Server 2012 R2 Datacenter**, observe que a lista **Selecionar um modelo de implantação** já mostra **Clássico** e, em seguida, clique em **Criar**.
   
    ![Criar VM no portal do Azure](./media/virtual-networks-static-ip-classic-pportal/figure01.png)
3. Na folha **Criar VM** , insira o nome da VM a ser criada (*DNS01* em nosso cenário), a conta de administrador local e a senha.
   
    ![Criar VM no portal do Azure](./media/virtual-networks-static-ip-classic-pportal/figure02.png)
4. Clique em **Configuração opcional** > **Rede** > **Rede Virtual**, e, em seguida, clique em **TestVNet**. Se **TestVNet** não estiver disponível, verifique se você está usando o *Centro dos EUA* como local e que você criou o ambiente de teste descrito no início deste artigo.
   
    ![Criar VM no portal do Azure](./media/virtual-networks-static-ip-classic-pportal/figure03.png)
5. Na folha **Rede**, certifique-se de que a sub-rede selecionada atualmente é *FrontEnd*, em seguida, clique em **Endereços IP**, em **Atribuição de endereço IP** clique em **Estático**, e, em seguida, digite *192.168.1.101* para **Endereço IP** conforme mostrado abaixo.
   
    ![Criar VM no portal do Azure](./media/virtual-networks-static-ip-classic-pportal/figure04.png)    
6. Clique em **OK** na folha **Endereços IP**, em seguida, clique em **OK** na folha **Rede** e clique em **OK** na folha **Configuração opcional**.
7. Na folha **Criar VM**, clique em **Criar**. Observe o bloco abaixo exibido no painel.
   
    ![Criar VM no portal do Azure](./media/virtual-networks-static-ip-classic-pportal/figure05.png)

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Como recuperar informações do endereço IP privado estático de uma VM
Para exibir as informações de endereço IP privado estático para a VM criada com as etapas acima, execute as etapas abaixo.

1. No portal do Azure, clique em **PROCURAR TUDO** > **Máquinas virtuais (clássico)** > **DNS01** > **Todas as configurações** > **Endereços IP** e observe o endereço IP e a atribuição de endereço IP, conforme mostrado abaixo.
   
    ![Criar VM no portal do Azure](./media/virtual-networks-static-ip-classic-pportal/figure06.png)

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Como remover o endereço IP privado estático de uma VM
Para remover o endereço IP privado estático da VM criada acima, siga as etapas abaixo.

1. Na folha **Endereços IP** mostrada acima, clique em **Dinâmico** à direita da **Atribuição de endereço IP**, em seguida, clique em **Salvar**, e, em seguida, clique em **Sim**.
   
    ![Criar VM no portal do Azure](./media/virtual-networks-static-ip-classic-pportal/figure07.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Como adicionar um endereço IP privado estático a uma VM existente
Para adicionar um IP privado estático à VM criada usando as etapas acima, execute as etapas abaixo:

1. Na folha **Endereços IP** mostrada acima, clique em **Estático** à direita de **Atribuição de endereço IP**.
2. Digite *192.168.1.101* para **Endereço IP**, clique em **Salvar** e, em seguida, clique em **Sim**.

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre endereços [IP públicos reservados](virtual-networks-reserved-public-ip.md) .
* Saiba mais sobre endereços [ILPIP (IP público em nível de instância)](virtual-networks-instance-level-public-ip.md) .
* Consulte as [APIs REST de IP reservado](https://msdn.microsoft.com/library/azure/dn722420.aspx).

