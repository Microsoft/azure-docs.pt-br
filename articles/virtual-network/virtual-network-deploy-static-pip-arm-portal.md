---
title: Criar uma máquina virtual com um endereço IP público estático - Portal do Azure | Microsoft Docs
description: Saiba como criar uma VM com um endereço IP público estático usando o Portal do Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: e9546bcc-f300-428f-b94a-056c5bd29035
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: kumud
ms.openlocfilehash: 42e035b9dca6168fe77e6982505692cf18bfcb40
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60743102"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-portal"></a>Crie uma máquina virtual com um endereço IP público estático usando o portal do Azure

Você pode criar uma máquina virtual com um endereço IP público estático. Um endereço IP público permite que você se comunique com uma máquina virtual pela Internet. Atribua um endereço IP público estático, em vez de um endereço dinâmico, para garantir que o endereço nunca seja alterado. Saiba mais sobre os [endereços IP públicos estáticos](virtual-network-ip-addresses-overview-arm.md#allocation-method). Para alterar um endereço IP público atribuído a uma máquina virtual existente de dinâmico para estático, ou para trabalhar com endereços IP, consulte [adicionar, alterar ou remover endereços IP](virtual-network-network-interface-addresses.md). Endereços IP públicos têm [carga nominal](https://azure.microsoft.com/pricing/details/ip-addresses), e há um [limite](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) para o número de endereços IP públicos que você pode usar por assinatura.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em https://portal.azure.com.

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

1. Selecione **+ Criar um recurso** localizado no canto superior esquerdo do Portal do Azure.
2. Selecione **Computar** e, em seguida, selecione **VM do Windows Server 2016** ou outro sistema operacional de sua escolha.
3. Insira, ou selecione, as informações a seguir, aceite os padrões para as configurações restantes e, em seguida, selecione **OK**:

    |Configuração|Valor|
    |---|---|
    |NOME|myVM|
    |Nome de usuário| Insira um nome de usuário de sua escolha.|
    |Senha| Insira uma senha de sua escolha. A senha deve ter no mínimo 12 caracteres e atender a [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Assinatura| Selecione sua assinatura.|
    |Grupo de recursos| Selecione **Usar existente** e, em seguida, **myResourceGroup**.|
    |Local padrão| Selecione **Leste dos EUA**|

4. Selecione um tamanho para a VM e selecione **Selecionar**.
5. Sob **as configurações**, selecione **endereço IP público**.
6. Insira *myPublicIpAddress*, selecione **estático**e, em seguida, selecione **Ok**, conforme mostrado na imagem a seguir:

   ![Selecione estático](./media/virtual-network-deploy-static-pip-arm-portal/select-static.png)

   Se o endereço IP público precisar ser um SKU padrão, selecione **Padrão** em **SKU**. Saiba mais sobre [SKUs de endereço IP público](virtual-network-ip-addresses-overview-arm.md#sku). Se a máquina virtual for adicionada ao pool de back-end de um Azure Load Balancer público, o SKU do endereço IP público da máquina virtual deverá corresponder ao SKU do endereço IP público do balanceador de carga. Para obter detalhes, consulte [balanceador de carga do Azure](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#skus).

6. Selecione uma porta ou portas não sob **selecione as portas de entrada públicas**. O Portal 3389 está selecionado para permitir o acesso remoto à máquina virtual do Windows Server a partir da Internet. A abertura da porta 3389 da Internet não é recomendada para cargas de trabalho de produção.

   ![Selecione uma porta](./media/virtual-network-deploy-static-pip-arm-portal/select-port.png)

7. Aceite as configurações padrão restantes e selecione **Ok**.
8. Sobre o **resumo** página, selecione **criar**. A máquina virtual leva alguns minutos para implantar.
9. Depois que a máquina virtual for implantada, insira *myPublicIpAddress* na caixa de pesquisa na parte superior do portal. Quando **myPublicIpAddress** aparece nos resultados da pesquisa, selecione.
10. Você pode visualizar o endereço IP público que é designado e que o endereço é designado à máquina virtual **myVM**, conforme mostrado na figura a seguir:

    ![Endereço IP público de modo de exibição](./media/virtual-network-deploy-static-pip-arm-portal/public-ip-overview.png)

    O Azure atribuiu um endereço IP público dos endereços usados na região na qual você criou a máquina virtual. Você pode baixar a lista de intervalos (prefixos) para as nuvens [pública](https://www.microsoft.com/download/details.aspx?id=56519), do [governo dos EUA](https://www.microsoft.com/download/details.aspx?id=57063), da [China](https://www.microsoft.com/download/details.aspx?id=57062) e da [Alemanha](https://www.microsoft.com/download/details.aspx?id=57064) do Azure.

11. Selecione **Configuração** para confirmar que a atribuição é **estática**.

    ![Endereço IP público de modo de exibição](./media/virtual-network-deploy-static-pip-arm-portal/public-ip-configuration.png)

> [!WARNING]
> Não modifique as configurações do endereço IP no sistema operacional da máquina virtual. O sistema operacional fica ciente dos endereços IP públicos do Azure. Embora você possa adicionar configurações de endereço IP privado ao sistema operacional, recomendamos não fazê-lo, a menos que seja necessário, e somente depois de ler [Adicionar um endereço IP privado a um sistema operacional](virtual-network-network-interface-addresses.md#private).

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, exclua o grupo de recursos e todos os recursos que ele contém:

1. Insira *myResourceGroup* na caixa **Pesquisar** na parte superior do portal. Quando aparecer **myResourceGroup** nos resultados da pesquisa, selecione-o.
2. Selecione **Excluir grupo de recursos**.
3. Insira *myResourceGroup* para **DIGITAR O NOME DO GRUPO DE RECURSOS:** e selecione **Excluir**.

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre [endereços IP públicos](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) no Azure
- Saiba mais sobre todos os [configurações de endereço IP público](virtual-network-public-ip-address.md#create-a-public-ip-address)
- Saiba mais sobre [endereços IP privados](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) e atribuir uma [endereço IP privado estático](virtual-network-network-interface-addresses.md#add-ip-addresses) para uma máquina virtual do Azure
- Saiba mais sobre como criar [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) máquinas virtuais