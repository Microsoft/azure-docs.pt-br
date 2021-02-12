---
title: Solução do Azure VMware por CloudSimple-conectar nuvem privada à rede do Azure usando o ExpressRoute
description: Descreve como conectar seu ambiente de nuvem privada do CloudSimple à rede virtual do Azure usando o ExpressRoute
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 98eec6b48d2b02c6eb7f6faea38994a9176bcf87
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97895981"
---
# <a name="connect-your-cloudsimple-private-cloud-environment-to-the-azure-virtual-network-using-expressroute"></a>Conectar seu ambiente de nuvem privada do CloudSimple à rede virtual do Azure usando o ExpressRoute

Sua nuvem privada do CloudSimple pode ser conectada à sua rede virtual do Azure usando o Azure ExpressRoute.  Essa alta largura de banda, a conexão de baixa latência permite que você acesse os serviços em execução na sua assinatura do Azure de seu ambiente de nuvem privada.

A conexão de rede virtual permite que você:

* Use o Azure como um destino de backup para máquinas virtuais em sua nuvem privada.
* Implante servidores KMS em sua assinatura do Azure para criptografar seu repositório de armazenamento vSAN de nuvem privada.
* Use aplicativos híbridos em que a camada da Web do aplicativo é executada na nuvem pública enquanto as camadas do aplicativo e do banco de dados são executadas em sua nuvem privada.

![Conexão do Azure ExpressRoute com a rede virtual](media/cloudsimple-azure-network-connection.png)

## <a name="set-up-a-virtual-network-connection"></a>Configurar uma conexão de rede virtual

Para configurar a conexão de rede virtual para sua nuvem privada, você precisa da sua chave de autorização, do URI do circuito par e do acesso à sua assinatura do Azure. Essas informações estão disponíveis na página conexão de rede virtual no portal do CloudSimple. Para obter instruções, consulte [obter informações de emparelhamento para a rede virtual do Azure para a conexão CloudSimple](virtual-network-connection.md). Se você tiver problemas para obter as informações, envie uma <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">solicitação de suporte</a>.

> [!TIP]
> Se você já tiver uma rede virtual do Azure, uma sub-rede de gateway e um gateway de rede virtual, poderá pular para a etapa 4.

1. Crie uma rede virtual em sua assinatura do Azure e verifique se o espaço de endereço selecionado é diferente do espaço de endereço da sua nuvem privada.  Se você já tiver uma rede virtual do Azure, poderá usar a existente.  Para obter detalhes, consulte [criar uma rede virtual usando o portal do Azure](../virtual-network/quick-create-portal.md).
2. Crie a sub-rede de gateway em sua rede virtual do Azure.  Se você já tiver uma sub-rede de gateway em sua rede virtual do Azure, poderá usar a existente. Para obter detalhes, consulte [criar a sub-rede de gateway](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet).
3. Crie o gateway de rede virtual em sua rede virtual.  Se você tiver um gateway de rede virtual existente, poderá usar o existente. Para obter detalhes, consulte [criar o gateway de rede virtual](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-virtual-network-gateway).
4. Crie a conexão entre sua rede virtual e sua nuvem privada, resgatando a chave de autorização conforme descrito em [conectar uma rede virtual a uma assinatura de circuito diferente](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription).

> [!WARNING]
> Se você estiver usando um gateway de rede virtual existente e ele tiver uma conexão de ExpressRoute para o mesmo local que o circuito de ExpressRoute CloudSimple, a conexão não será estabelecida.  Crie uma nova rede virtual e siga as etapas anteriores.

## <a name="test-the-virtual-network-connection"></a>Testar a conexão de rede virtual

Depois que a conexão é criada, você pode verificar o status da conexão selecionando **Propriedades** em **configurações**.  O estado de status e provisionamento deve mostrar com **êxito**.

![Status da conexão](media/azure-expressroute-connection.png)

Para testar a conexão de rede virtual:

1. Crie uma máquina virtual em sua assinatura do Azure.
2. Localize o endereço IP de sua nuvem privada vCenter (consulte seu email de boas-vindas).
3. Execute ping no vCenter da nuvem da máquina virtual criada em sua rede virtual do Azure.
4. Execute ping na máquina virtual do Azure de uma máquina virtual em execução em sua nuvem privada vCenter.

Se você tiver problemas para estabelecer a conexão, envie uma <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">solicitação de suporte</a>.
