---
title: Configurar a preferência de roteamento para uma VM – portal do Azure
description: Saiba como criar uma VM com um endereço IP público com uma opção de preferência de roteamento usando o portal do Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2021
ms.author: mnayak
ms.openlocfilehash: 0559d02ec603d12578fa46d9790d0711fde5e38b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101670907"
---
# <a name="configure-routing-preference-for-a-vm-using-the-azure-portal"></a>Configurar a preferência de roteamento para uma VM usando o portal do Azure

Este artigo mostra como configurar a preferência de roteamento para uma máquina virtual. O tráfego associado à Internet que parte da VM será roteado por meio da rede do ISP quando você escolher **Internet** como opção de preferência de roteamento. O roteamento padrão é feito por meio da rede global da Microsoft.

Este artigo mostra como criar uma máquina virtual com um IP público que está definido para rotear o tráfego pela Internet pública usando o portal do Azure.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [portal do Azure](https://portal.azure.com/).

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

1. Selecione **+ Criar um recurso** localizado no canto superior esquerdo do Portal do Azure.
2. Selecione **Computar** e, em seguida, selecione **VM do Windows Server 2016** ou outro sistema operacional de sua escolha.
3. Insira, ou selecione, as informações a seguir, aceite os padrões para as configurações restantes e, em seguida, selecione **OK**:

    |Configuração|Valor|
    |---|---|
    |Nome|myVM|
    |Nome de usuário| Insira um nome de usuário de sua escolha.|
    |Senha| Insira uma senha de sua escolha. A senha deve ter no mínimo 12 caracteres e atender a [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Subscription| Selecione sua assinatura.|
    |Resource group| Selecione **Usar existente** e, em seguida, **myResourceGroup**.|
    |Location| Selecione **Leste dos EUA**|

4. Selecione um tamanho para a VM e selecione **Selecionar**.
5. Na guia **Rede**, clique em **Criar** em **Endereço IP público**.
6. Insira *myPublicIpAddress*, selecione o SKU como **Standard** e a preferência de roteamento **Internet** e clique em **OK**, como mostra a seguinte figura:

   ![Selecione estático](./media/tutorial-routing-preference-virtual-machine-portal/routing-preference-internet-new.png)

6. Selecione uma porta ou portas não sob **selecione as portas de entrada públicas**. O Portal 3389 está selecionado para permitir o acesso remoto à máquina virtual do Windows Server a partir da Internet. A abertura da porta 3389 da Internet não é recomendada para cargas de trabalho de produção.

   ![Selecione uma porta](./media/tutorial-routing-preference-virtual-machine-portal/pip-ports-new.png)

7. Aceite as configurações padrão restantes e selecione **Ok**.
8. Sobre o **resumo** página, selecione **criar**. A máquina virtual leva alguns minutos para implantar.
9. Depois que a máquina virtual for implantada, insira *myPublicIpAddress* na caixa de pesquisa na parte superior do portal. Quando **myPublicIpAddress** aparece nos resultados da pesquisa, selecione.
10. Você pode visualizar o endereço IP público que é designado e que o endereço é designado à máquina virtual **myVM**, conforme mostrado na figura a seguir:

    ![Captura de tela mostra a NIC pública I P para a interface de rede mynic.](./media/tutorial-routing-preference-virtual-machine-portal/pip-properties-new.png)

11. Selecione **Rede**, clique na NIC **mynic** e selecione o endereço IP público para confirmar se a preferência de roteamento foi atribuída como **Internet**.

    ![Captura de tela mostra o endereço I P e a preferência de roteamento para um endereço público I P.](./media/tutorial-routing-preference-virtual-machine-portal/pip-routing-internet-new.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, exclua o grupo de recursos e todos os recursos que ele contém:

1. Insira *myResourceGroup* na caixa **Pesquisar** na parte superior do portal. Quando aparecer **myResourceGroup** nos resultados da pesquisa, selecione-o.
2. Selecione **Excluir grupo de recursos**.
3. Insira *myResourceGroup* para **DIGITAR O NOME DO GRUPO DE RECURSOS:** e selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre o [IP público com a preferência de roteamento](routing-preference-overview.md).
- Saiba mais sobre os [endereços IP públicos](./public-ip-addresses.md#public-ip-addresses) no Azure.
- Saiba mais sobre todas as [configurações de endereço IP público](virtual-network-public-ip-address.md#create-a-public-ip-address).