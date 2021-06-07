---
title: Configurar endereços IP privados para VMs-portal do Azure
description: Saiba como configurar endereços IP para máquinas virtuais usando o Portal do Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
tags: azure-resource-manager
ms.assetid: 11245645-357d-4358-9a14-dd78e367b494
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/07/2020
ms.author: kumud
ms.openlocfilehash: c8fdba59a8d31c064745c7a1904204359b386a7f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96015905"
---
# <a name="configure-a-private-ip-address-for-a-vm-using-the-azure-portal"></a>Configurar um endereço IP privado para uma VM usando o portal do Azure

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

As etapas de exemplo a seguir esperam que um ambiente simples já seja criado. Se você quiser executar as etapas conforme elas são exibidas neste documento, primeiro [crie uma rede virtual](quick-create-portal.md#create-a-virtual-network). No entanto, na etapa 3, use estes valores:

| Configuração | Valor |
| ------- | ----- |
| Nome | *TestVNet* |
| Espaço de endereço | *192.168.0.0/16* |
| Resource group | **TestRG** (se necessário, selecione **criar novo** para criá-lo) |
| Sub-rede – Nome | *FrontEnd* |
| Sub-rede – Intervalo de endereços | *192.168.1.0/24* |

## <a name="create-a-vm-for-testing-static-private-ip-addresses"></a>Criar uma VM para testar endereços IP privados estáticos
Quando você cria uma VM no modo de implantação do Gerenciador de recursos, não pode definir um endereço IP privado estático usando o portal do Azure. Em vez disso, você cria a VM primeiro. Em seguida, você pode definir seu IP privado como estático.

Para criar uma VM denominada *DNS01* na sub-rede *frontend* de uma rede virtual denominada *TestVNet*, siga estas etapas:

1. No menu do [portal do Azure](https://portal.azure.com), selecione **Criar um recurso**.

    ![Criar um recurso, portal do Azure](./media/virtual-networks-static-ip-arm-pportal/create-a-resource.png)
2. Selecione   >  **máquina virtual** de computação.

    ![Criar VM, portal do Azure](./media/virtual-networks-static-ip-arm-pportal/compute-virtual-machine.png)
3. Em **noções básicas**, especifique valores para itens, conforme descrito na tabela a seguir. Em seguida, selecione **Avançar &nbsp; : &nbsp; discos** e **próximo &nbsp; : &nbsp; rede**.

    | Item | Valor |
    | --- | --- |
    | **Assinatura** | Sua assinatura atual |
    | **Grupo de recursos** | **TestRG** (selecione na lista suspensa) |
    | **Nome da máquina virtual** | *DNS01* |
    | **Região** | **(EUA) Leste dos EUA** |
    | **Imagem** | **Windows Server 2019 Datacenter** |
    | **Tamanho** | **Tamanho da VM** de **B1ls**, **oferta** do **padrão** |
    | **Nome de usuário** | O nome de usuário da sua conta de administrador |
    | **Senha** | A senha do nome de usuário da sua conta de administrador |
    | **Confirmar senha** | A senha novamente |

    ![Guia básico, criar uma máquina virtual, portal do Azure](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-basics.png)
4. Em **rede**, especifique valores para os itens conforme descrito na tabela a seguir e, em seguida, selecione **Avançar**.

    | Item | Valor |
    | --- | --- |
    | **Rede virtual** | **TestVNet** |
    | **Sub-rede** | **FrontEnd** |

    ![Guia rede, criar uma máquina virtual, portal do Azure](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-networking.png)
5. Em **Gerenciamento**, em **conta de armazenamento de diagnóstico**, escolha **vnetstorage**. Se essa conta de armazenamento não aparecer na lista, selecione **criar nova**, especifique um **nome** de *vnetstorage* e selecione **OK**. Por fim, selecione **examinar &nbsp; + &nbsp; criar**.

    ![Guia gerenciamento, criar uma máquina virtual, portal do Azure](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-management.png)
6. Em **revisão + criar**, examine as informações de visão geral e, em seguida, selecione **criar**.

    ![Examine + criar guia, criar uma máquina virtual, portal do Azure](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-review-create.png)

A mensagem a seguir é exibida depois que a VM é criada.

![Mensagem de conclusão de implantação, criar uma máquina virtual, portal do Azure](./media/virtual-networks-static-ip-arm-pportal/deployment-is-complete.png)

## <a name="retrieve-private-ip-address-information-for-a-vm"></a>Recuperar informações de endereço IP privado para uma VM
Para exibir as informações de endereço IP privado para sua nova VM:

1. Vá para a [portal do Azure](https://portal.azure.com) para localizar sua VM. Pesquise por **Máquinas virtuais** e selecione essa opção.

    ![Máquinas virtuais, caixa de pesquisa portal do Azure](./media/virtual-networks-static-ip-arm-pportal/search-box-virtual-machines.png)

2. Selecione o nome da sua nova VM (**DNS01**).

    ![Lista de máquinas virtuais, portal do Azure](./media/virtual-networks-static-ip-arm-pportal/virtual-machine-list.png)

3. Escolha **rede** e selecione a interface de rede exclusiva listada.

    ![Interface de rede, rede, máquina virtual portal do Azure](./media/virtual-networks-static-ip-arm-pportal/networking-network-interface.png)

4. Escolha **configurações de IP** e selecione a configuração de IP listada na tabela.

    ![Configuração de IP, interface de rede, rede, máquina virtual portal do Azure](./media/virtual-networks-static-ip-arm-pportal/network-interface-ip-configurations.png)

5. Em **configurações de endereço IP privado**, na rede/sub-rede virtual **TestVNet/frontend** , observe o valor de **atribuição** (**dinâmico** ou **estático**) e o **endereço IP**.

    ![Atribuição dinâmica ou estática, configurações antigas de endereço IP privado, configuração de IP, interface de rede, rede, máquina virtual, portal do Azure](./media/virtual-networks-static-ip-arm-pportal/private-ip-address-settings-old.png)

## <a name="add-a-static-private-ip-address-to-an-existing-vm"></a>Adicionar um endereço IP privado estático a uma VM existente
Para adicionar um endereço IP privado estático à sua nova VM:

1. Na página configuração de IP, defina a atribuição para seu endereço IP privado como **estático**.
2. Altere seu **endereço IP** privado para *192.168.1.101* e, em seguida, selecione **salvar**.
   
    ![Atribuição dinâmica ou estática, novas configurações de endereço IP privado, configuração de IP, interface de rede, rede, máquina virtual portal do Azure](./media/virtual-networks-static-ip-arm-pportal/private-ip-address-settings-new.png)

> [!NOTE]
> Se você observar que, depois de selecionar **salvar** que a atribuição ainda está definida como **dinâmica**, o endereço IP que você digitou já está em uso. Tente outro endereço IP.

## <a name="remove-a-static-private-ip-address-from-a-vm"></a>Remover o endereço IP privado estático de uma VM
Para remover o endereço IP privado estático da sua VM:

Na página configuração de IP, defina a atribuição para seu endereço IP privado como **dinâmico** e, em seguida, selecione **salvar**.

## <a name="set-ip-addresses-within-the-operating-system"></a>Definir endereços IP no sistema operacional

De dentro do sistema operacional de uma VM, você não deve atribuir estaticamente o IP *privado* atribuído à VM do Azure. Somente faça a atribuição estática de um IP privado quando for necessário, como ao [atribuir muitos endereços IP a VMs](virtual-network-multiple-ip-addresses-portal.md). Se você definir manualmente o endereço IP privado no sistema operacional, verifique se ele corresponde ao endereço IP privado atribuído à [interface de rede](virtual-network-network-interface-addresses.md#change-ip-address-settings)do Azure. Caso contrário, você pode perder a conectividade com a VM. Saiba mais sobre as configurações de [endereço IP privado](virtual-network-network-interface-addresses.md#private).

Além disso, você nunca deve atribuir manualmente o endereço IP *público* atribuído a uma máquina virtual do Azure no sistema operacional da máquina virtual.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as configurações de [endereço IP privado](virtual-network-network-interface-addresses.md).
