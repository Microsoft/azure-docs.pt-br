---
title: 'Excluir um gateway de rede virtual: Portal do Azure: Resource Manager | Microsoft Docs'
description: "Exclua um gateway de rede virtual usando o portal do Azure no modelo de implantação do Resource Manager."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: 
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/20/2017
ms.author: cherylmc
ms.translationtype: HT
ms.sourcegitcommit: 2812039649f7d2fb0705220854e4d8d0a031d31e
ms.openlocfilehash: 1d289c09465cb8d5e4bfa569441dffcbf562b3bf
ms.contentlocale: pt-br
ms.lasthandoff: 07/22/2017

---
# <a name="delete-a-virtual-network-gateway-using-the-portal"></a>Exclua um gateway de rede virtual usando o portal

> [!div class="op_single_selector"]
> * [Portal do Azure](vpn-gateway-delete-vnet-gateway-portal.md)
> * [PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [PowerShell (clássico)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)

Há duas abordagens diferentes que podem ser executadas quando você deseja excluir um gateway de rede virtual de uma configuração de Gateway de VPN.

- Se você desejar excluir tudo e recomeçar, por exemplo, no caso de um ambiente de teste, poderá excluir o grupo de recursos. Quando você exclui um grupo de recursos, isso exclui todos os recursos dentro do grupo. Esse método é recomendado somente se você não deseja manter os recursos no grupo de recursos. Você não pode excluir seletivamente apenas alguns recursos usando essa abordagem.

- Se você quiser manter alguns dos recursos no seu grupo de recursos, a exclusão de um gateway de rede virtual se tornará um pouco mais complicada. Antes de poder excluir o gateway de rede virtual, primeiro você deve excluir todos os recursos que são dependentes do gateway. As etapas a que seguir dependem do tipo de conexões que você criou e os recursos dependentes de cada conexão.

## <a name="delete-a-vpn-gateway"></a>Excluir um gateway de VPN

Para excluir um gateway de rede virtual, exclua cada recurso pertencente ao gateway de rede virtual. Os recursos devem ser excluídos em uma determinada ordem devido a dependências.

[!INCLUDE [delete gateway](../../includes/vpn-gateway-delete-vnet-gateway-portal-include.md)]

Nesse momento, o gateway de rede virtual é excluído. As próximas etapas ajudam a excluir quaisquer recursos que não são mais utilizados.

### <a name="to-delete-the-local-network-gateway"></a>Para excluir o gateway de rede local

1. Em **Todos os recursos**, localize os gateways de rede locais que estavam associados a cada conexão.
2. Na folha **Visão geral** do gateway de rede local, clique em **Excluir**.

### <a name="to-delete-the-public-ip-address-resource-for-the-gateway"></a>Para excluir o recurso de endereço IP público para o gateway

1. Em **Todos os recursos**, localize o recurso de endereço IP público que estava associado ao gateway. Se o gateway de rede virtual estava ativo-ativo, você verá dois endereços IP públicos. 
2. Na página **Visão geral** para o endereço IP público, clique em **Excluir** e, em seguida, em **Sim** para confirmar.

### <a name="to-delete-the-gateway-subnet"></a>Para excluir a sub-rede de gateway

1. Em **Todos os recursos**, localize a rede virtual. 
2. Na folha **Sub-redes**, clique em **GatewaySubnet** e, em seguida, clique em **Excluir**. 
3. Clique em **Sim** para confirmar que deseja excluir a sub-rede do gateway.

## <a name="deleterg"></a>Excluir um Gateway de VPN por meio da exclusão do grupo de recursos

Se você não estiver preocupado em manter nenhum de seus recursos no grupo de recursos e apenas desejar recomeçar, poderá excluir um grupo de recursos inteiro. Essa é uma maneira rápida de remover tudo. As próximas etapas se aplicam somente ao modelo de implantação do Resource Manager.

1. Em **Todos os recursos**, localize o grupo de recursos e clique para abrir a folha.
2. Clique em **Excluir**. Na folha Excluir, exiba os recursos afetados. Certifique-se de que deseja excluir todos esses recursos. Se não, use as etapas em [Excluir um Gateway de VPN](#deletegw) na parte superior deste artigo.
3. Para continuar, digite o nome do grupo de recursos que você deseja excluir e, em seguida, clique em **Excluir**.
