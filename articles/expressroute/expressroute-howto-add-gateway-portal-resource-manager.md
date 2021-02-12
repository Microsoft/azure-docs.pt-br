---
title: 'Tutorial: Azure ExpressRoute – adicionar um gateway a uma VNet (portal do Azure)'
description: Este tutorial explica as etapas para adicionar um gateway de rede virtual a uma VNet para o ExpressRoute usando o portal do Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/05/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 843d0b8cfd75e8cbdf45ac535cc9486aa42442d6
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2020
ms.locfileid: "91761760"
---
# <a name="tutorial-configure-a-virtual-network-gateway-for-expressroute-using-the-azure-portal"></a>Tutorial: Configurar um gateway de rede virtual para ExpressRoute usando o portal do Azure
> [!div class="op_single_selector"]
> * [Resource Manager - portal do Azure](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Clássico - PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Vídeo – Portal do Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 

Este tutorial explica as etapas para adicionar um gateway de rede virtual a uma VNet existente. Este artigo explica as etapas para adicionar, redimensionar e remover um gateway de VNet (rede virtual) para uma VNet existente. As etapas para essa configuração destinam-se especificamente a VNets criadas usando o modelo de implantação do Resource Manager que será usado em uma configuração do ExpressRoute. Para obter mais informações sobre gateways de rede virtual e definições de configuração do ExpressRoute, consulte [Sobre gateways de rede virtual para ExpressRoute](expressroute-about-virtual-network-gateways.md). 

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> - Criar uma sub-rede de gateway.
> - Criar gateway de rede virtual.

## <a name="prerequisites"></a>Pré-requisitos

As etapas para essa tarefa usam uma VNet com base nos valores na lista de referência de configuração a seguir. Usamos essa lista em nosso exemplo de etapas. É possível fazer uma cópia da lista para usá-la como referência, substituindo os valores pelos seus próprios.

**Lista de referência de configuração**

* Nome da Rede Virtual = “TestVNet”
* Espaço de endereço da Rede Virtual = 192.168.0.0/16
* Nome da sub-rede = "FrontEnd" 
    * Espaço de endereço da sub-rede = "192.168.1.0/24"
* Grupo de recursos = “TestRG”
* Local = "Leste dos EUA"
* Nome da Sub-rede do Gateway: “GatewaySubnet” Deve-se sempre nomear uma sub-rede do gateway como *GatewaySubnet*.
    * Espaço de endereço da Sub-Rede do Gateway = “192.168.200.0/26”
* Nome do gateway = "ERGW"
* Nome do IP público do gateway = "MyERGWVIP"
* Tipo de gateway = "ExpressRoute". Esse tipo é necessário para uma configuração de ExpressRoute.

Você pode exibir um [Vídeo](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network) dessas etapas antes de iniciar sua configuração.

## <a name="create-the-gateway-subnet"></a>Criar a sub-rede de gateway

1. No [portal](https://portal.azure.com), navegue até a rede virtual do Gerenciador de Recursos para o qual você deseja criar um gateway de rede virtual.
1. Na seção **Configurações** de sua VNet, clique em **Sub-redes** para expandir as Configurações da sub-rede.
1. Nas configurações da **Sub-rede** , selecione **+ Sub-rede de gateway** para adicionar uma sub-rede de gateway. 
   
    :::image type="content" source="./media/expressroute-howto-add-gateway-portal-resource-manager/add-gateway-subnet.png" alt-text="Adicionar a sub-rede de gateway":::

1. O **Nome** da sua sub-rede será automaticamente preenchido com o valor 'GatewaySubnet'. Esse valor é necessário para que o Azure reconheça a sub-rede como a sub-rede de gateway. Ajuste os valores preenchidos automaticamente de **Intervalo de endereços** para corresponder aos seus requisitos de configuração. É recomendável criar uma sub-rede do gateway com um /27 ou maior (/ 26, / 25 e assim por diante). Em seguida, selecione **OK** para salvar os valores e criar a sub-rede de gateway.

    :::image type="content" source="./media/expressroute-howto-add-gateway-portal-resource-manager/add-subnet-gateway.png" alt-text="Adicionar a sub-rede":::

## <a name="create-the-virtual-network-gateway"></a>Criar o gateway de rede virtual

1. No portal, do lado esquerdo, selecione **Criar um recurso** e digite “gateway de rede virtual” na pesquisa. Localize **Gateway de rede virtual** na pesquisa, retorne e selecione a entrada. Na página **Gateway de rede virtual** , selecione **Criar**.
1. Na página **Criar gateway de rede virtual** , insira ou selecione estas configurações:

    | Configuração | Valor |
    | --------| ----- |
    | Subscription | verifique se a assinatura correta foi selecionada. |
    | Grupo de recursos | O grupo de recursos será escolhido automaticamente após você selecionar a rede virtual. | 
    | Nome | Nomeie o seu gateway. Isso não é igual a nomear uma sub-rede de gateway. Trata-se do nome do objeto de gateway que você está criando.|
    | Região | Altere o campo **Região** para apontar para o local onde se encontra sua rede virtual. Se o local não estiver apontando para a região onde está sua rede virtual, a rede virtual não aparecerá na lista suspensa 'Escolher uma rede virtual'. |
    | Tipo de gateway | Selecione **ExpressRoute**|
    | SKU | Selecione o SKU do gateway no menu suspenso. |
    | Rede virtual | Selecione *TestVNet*. |
    | Endereço IP público | Selecione **Criar novo**.|
    | Nome do endereço IP público | Forneça um nome para o endereço IP público. |

1. Selecione **Examinar + criar** e **Criar** para começar a criar o gateway. As configurações são validadas e o gateway é implantado. A criação de um gateway de rede virtual pode levar até 45 minutos para ser concluída.

    :::image type="content" source="./media/expressroute-howto-add-gateway-portal-resource-manager/gateway.png" alt-text="Campos da página Criar gateway de rede virtual":::

## <a name="clean-up-resources"></a>Limpar os recursos

Se não precisar mais do gateway do ExpressRoute, localize-o no grupo de recursos de rede virtual e selecione **Excluir**. Certifique-se de que o gateway não tenha conexões com um circuito.

:::image type="content" source="./media/expressroute-howto-add-gateway-portal-resource-manager/delete-gateway.png" alt-text="Excluir gateway de rede virtual":::

## <a name="next-steps"></a>Próximas etapas
Depois de criar o gateway de VNet, é possível vincular sua VNet a um circuito do ExpressRoute. 

> [!div class="nextstepaction"]
> [Vincular uma rede virtual a um circuito de ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)
