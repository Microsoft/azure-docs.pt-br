---
title: 'Tutorial: Criar conexões do ExpressRoute usando a WAN Virtual do Azure'
description: Neste tutorial, saiba como usar a WAN Virtual do Azure para criar conexões do ExpressRoute com o Azure e com ambientes locais.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 10/07/2020
ms.author: cherylmc
ms.openlocfilehash: 67df1c822f34f45bb0931848f8015374a346673a
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278432"
---
# <a name="tutorial-create-an-expressroute-association-using-azure-virtual-wan"></a>Tutorial: Criar uma associação do ExpressRoute usando a WAN Virtual do Azure

Este tutorial mostra como usar a WAN Virtual para conectar-se aos seus recursos no Azure com o uso de um circuito do ExpressRoute. Para saber mais sobre WAN Virtual e recursos de WAN Virtual, confira a [Visão geral de WAN Virtual](virtual-wan-about.md).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma WAN virtual
> * Criar um hub e um gateway
> * Conectar uma VNET a um hub
> * Conectar um circuito a um gateway de hub
> * Testar a conectividade
> * Alterar o tamanho do gateway
> * Anunciar uma rota padrão

## <a name="prerequisites"></a>Pré-requisitos

Verifique se você atende aos seguintes critérios antes de iniciar a configuração:

* Você tem uma rede virtual à qual deseja se conectar. Verifique se nenhuma das sub-redes das redes locais se sobrepõe às redes virtuais às quais você deseja se conectar. Para criar uma rede virtual no portal do Azure, consulte o [Início Rápido](../virtual-network/quick-create-portal.md).

* Sua rede virtual não tem gateways de rede virtual. Se sua rede virtual tem um gateway (VPN ou ExpressRoute), remova todos os gateways. Essa configuração requer que as redes virtuais sejam conectadas ao gateway do hub da WAN Virtual.

* Obtenha um intervalo de endereços IP para sua região de hub. O hub é uma rede virtual criada e usada pela WAN Virtual. O intervalo de endereços especificado para o hub não pode se sobrepor a nenhuma das redes virtuais existentes às quais você se conecta. Ele também não pode se sobrepor aos intervalos de endereços aos quais você se conecta localmente. Se não estiver familiarizado com os intervalos de endereços IP da sua configuração de rede local, trabalhe com alguém que possa lhe fornecer esses detalhes.

* O circuito do ExpressRoute precisa ser um circuito Premium ou Standard para se conectar ao gateway do hub.

* Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-virtual-wan"></a><a name="openvwan"></a>Criar uma WAN Virtual

Em um navegador, acesse o [Portal do Azure](https://portal.azure.com) e entre com sua conta do Azure.

1. Navegue até a página da WAN virtual. No portal, clique em **+Criar um recurso**. Digite **WAN Virtual** na caixa de pesquisa e clique em Enter.
2. Selecione **WAN Virtual** nos resultados. Na página WAN Virtual, clique em **Criar** para abrir a página Criar WAN.
3. Na página **Criar WAN**, na guia **Básico**, preencha os seguintes campos:

   ![Criar WAN](./media/virtual-wan-expressroute-portal/createwan.png)

   * **Assinatura**: selecione a assinatura que você quer usar.
   * **Grupo de Recursos**: crie um novo ou use um existente.
   * **Localização do grupo de recursos**: escolha uma localização de recursos na lista suspensa. Uma WAN é um recurso global e não pode residir em uma região específica. No entanto, você deve selecionar uma região a fim de gerenciar e localizar o recurso de WAN criado mais facilmente.
   * **Nome**: digite o nome que você quer dar à sua WAN.
   * **Tipo** – selecione **Standard**. Você não pode criar um gateway do ExpressRoute usando o SKU Básico.
4. Quando terminar de preencher os campos, selecione **Examinar + Criar**.
5. Depois que a validação for aprovada, selecione **Criar** para criar a WAN Virtual.

## <a name="create-a-virtual-hub-and-gateway"></a><a name="hub"></a>Criar um hub virtual e um gateway

Um hub virtual é uma rede virtual criada e usada pela WAN Virtual. Ele pode conter vários gateways, tais como VPN e ExpressRoute. Nesta seção, você criará um gateway do ExpressRoute para o seu hub virtual. Você pode criar o gateway ao [criar um hub virtual](#newhub) ou então pode criar o gateway em um [hub existente](#existinghub), editando-o. 

Os gateways do ExpressRoute são provisionados em unidades de 2 Gbps. 1 unidade de escala = 2 Gbps com suporte para até 10 unidades de escala = 20 Gbps. Leva cerca de 30 minutos para que um gateway e um hub virtual sejam totalmente criados.

### <a name="to-create-a-new-virtual-hub-and-a-gateway"></a><a name="newhub"></a>Para criar um novo hub virtual e um gateway

Crie um novo hub virtual. Depois que o hub é criado, você será cobrado por ele, mesmo se não anexar nenhum site.

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-er-hub-include.md)]

### <a name="to-create-a-gateway-in-an-existing-hub"></a><a name="existinghub"></a>Para criar um gateway em um hub existente

Você também pode criar um gateway em um hub existente editando-o.

1. Navegue até o hub virtual que você deseja editar e selecione-o.
2. Na página **Editar hub virtual**, marque a caixa de seleção **Incluir gateway do ExpressRoute**.
3. Selecione **Confirmar** para confirmar as alterações. Leva cerca de 30 minutos para que o hub e os recursos do hub sejam totalmente criados.

   ![hub existente](./media/virtual-wan-expressroute-portal/edithub.png "editar um hub")

### <a name="to-view-a-gateway"></a>Para exibir um gateway

Depois de criar um gateway do ExpressRoute, você poderá exibir detalhes do gateway. Navegue até o Hub, selecione **ExpressRoute** e exiba o gateway.

![Exibir gateway](./media/virtual-wan-expressroute-portal/viewgw.png "exibir gateway")

## <a name="connect-your-vnet-to-the-hub"></a><a name="connectvnet"></a>Conectar sua VNET ao hub

Nesta seção, você pode criar a conexão de emparelhamento entre uma VNET e seu hub. Repita as etapas para cada VNET que você deseja se conectar.

1. Na página da WAN virtual, clique em **Conexão de rede virtual**.
2. Na página de conexão de rede virtual, clique em **+Adicionar conexão**.
3. Na página **Adicionar conexão**, preencha os seguintes campos:

    * **Nome da Conexão**: nomeie sua conexão.
    * **Hubs**: selecione o hub que você deseja associar a essa conexão.
    * **Assinatura**: verifique a assinatura.
    * **Rede virtual:** selecione a rede virtual que você deseja conectar a esse hub. A rede virtual não pode ter um gateway de rede virtual já existente (nem VPN, nem ExpressRoute).

## <a name="connect-your-circuit-to-the-hub-gateway"></a><a name="connectcircuit"></a>Conectar seu circuito ao gateway do hub

Depois que o gateway é criado, você pode conectar um [circuito de ExpressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) a ele. Circuitos Standard ou Premium do ExpressRoute que estão em locais compatíveis com o Alcance Global do ExpressRoute podem se conectar a um gateway do ExpressRoute de WAN Virtual e usar todas as funcionalidades de trânsito de WAN Virtual (VPN para VPN, VPN e ExpressRoute). Os circuitos Premium ou Standard do ExpressRoute que estão em locais sem Alcance Global podem se conectar aos recursos do Azure, mas não poderão usar os recursos de trânsito da WAN Virtual. Também há suporte para o ExpressRoute Local nos hubs da WAN Virtual do Azure.

### <a name="to-connect-the-circuit-to-the-hub-gateway"></a>Para conectar o circuito ao gateway do hub

No portal, vá para a página **Hub virtual -> Conectividade -> ExpressRoute**. Se tiver acesso em sua assinatura a um circuito do ExpressRoute, você verá o circuito que deseja usar na lista de circuitos. Se você não vir nenhum circuito, mas uma chave de autorização e um URI de circuito par tiverem sido fornecidos a você, será possível resgatar e conectar um circuito. Confira [Para conectar resgatando uma chave de autorização](#authkey).

1. Selecione o circuito.
2. Selecione **Conectar circuitos**.

   ![conectar circuitos](./media/virtual-wan-expressroute-portal/cktconnect.png "conectar circuitos")

### <a name="to-connect-by-redeeming-an-authorization-key"></a><a name="authkey"></a>Para conectar resgatando uma chave de autorização

Use a chave de autorização e o URI de circuito que você forneceu para se conectar.

1. Na página do ExpressRoute, clique em **+Resgatar chave de autorização**

   ![A captura de tela mostra o ExpressRoute para um hub virtual com Resgatar chave de autorização selecionado.](./media/virtual-wan-expressroute-portal/redeem.png "resgatar")
2. Na página Resgatar a chave de autorização, preencha os valores.

   ![resgatar valores de chave](./media/virtual-wan-expressroute-portal/redeemkey2.png "resgatar valores de chave")
3. Selecione **Adicionar** para adicionar a chave.
4. Exibir o circuito. Um circuito resgatado mostra apenas o nome (sem o tipo, o provedor e outras informações) porque ele está em uma assinatura diferente daquela do usuário.

## <a name="to-test-connectivity"></a>Para testar a conectividade

Depois que a conexão de circuito for estabelecida, o status de conexão do hub indicará 'este hub', indicando que a conexão foi estabelecida com o gateway do ExpressRoute do hub. Aguarde aproximadamente cinco minutos antes de testar a conectividade de um cliente atrás do circuito do ExpressRoute, por exemplo, uma VM na VNet que você criou anteriormente.

Se você tiver sites conectados a um gateway de VPN de WAN Virtual no mesmo hub que o gateway do ExpressRoute, você poderá ter conectividade bidirecional entre os pontos de extremidade da VPN e do ExpressRoute. O BGP (roteamento dinâmico) é compatível. O ASN dos gateways no hub é fixo e não pode ser editado no momento.

## <a name="to-change-the-size-of-a-gateway"></a>Para alterar o tamanho de um gateway

Se você quiser alterar o tamanho do gateway do ExpressRoute, localize o gateway do ExpressRoute dentro do hub e selecione as unidades de escala na lista suspensa. Salve sua alteração. Levará aproximadamente 30 minutos para atualizar o gateway do hub.

![alterar tamanho do gateway](./media/virtual-wan-expressroute-portal/changescale.png "alterar tamanho do gateway")

## <a name="to-advertise-default-route-00000-to-endpoints"></a>Para anunciar a rota padrão 0.0.0.0/0 para pontos de extremidade

Se você quiser que o hub virtual do Azure anuncie a rota padrão 0.0.0.0/0 aos pontos de extremidade do ExpressRoute, será necessário habilitar "Propagar a rota padrão".

1. Selecione seu **Circuito->...-> Editar conexão**.

   ![Editar conexão](./media/virtual-wan-expressroute-portal/defaultroute1.png "Editar conexão")

2. Selecione **Habilitar** para propagar a rota padrão.

   ![Propagar rota padrão](./media/virtual-wan-expressroute-portal/defaultroute2.png "Propagar rota padrão")

## <a name="clean-up-resources"></a><a name="cleanup"></a>Limpar recursos

Quando não precisar mais dos recursos que criou, exclua-os. Alguns dos recursos da WAN Virtual precisam ser excluídos em determinada ordem devido às dependências. A exclusão poderá levar cerca de 30 minutos para ser concluída.

[!INCLUDE [Delete resources](../../includes/virtual-wan-resource-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Em seguida, para saber mais sobre a WAN Virtual, confira:

> [!div class="nextstepaction"]
> * [Perguntas frequentes sobre a WAN Virtual](virtual-wan-faq.md)
