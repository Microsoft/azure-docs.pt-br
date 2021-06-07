---
title: 'Tutorial: Vincular uma VNet a um circuito do ExpressRoute – portal do Azure'
description: Este tutorial mostra como criar uma conexão para vincular uma rede virtual a um circuito do Azure ExpressRoute usando o portal do Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/15/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: a9bfb4beea2c2bec1b819d228215cfff65e37fe4
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106110250"
---
# <a name="tutorial-connect-a-virtual-network-to-an-expressroute-circuit-using-the-portal"></a>Tutorial: Conectar uma rede virtual a um circuito ExpressRoute usando o portal

> [!div class="op_single_selector"]
> * [Azure portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [CLI do Azure](howto-linkvnet-cli.md)
> * [Vídeo – Portal do Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-linkvnet-classic.md)
> 

Este tutorial ajuda você a criar uma conexão para vincular uma rede virtual a um circuito do Azure ExpressRoute usando o portal do Azure. As redes virtuais conectadas ao circuito do Azure ExpressRoute podem estar na mesma assinatura ou fazer parte de outra assinatura.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> - Conectar uma rede virtual a um circuito na mesma assinatura.
> - Conectar uma rede virtual a um circuito em uma assinatura diferente.
> - Excluir o link entre a rede virtual e o circuito do ExpressRoute.

## <a name="prerequisites"></a>Pré-requisitos

* Analise os [pré-requisitos](expressroute-prerequisites.md), os [requisitos de roteamento](expressroute-routing.md) e os [fluxos de trabalho](expressroute-workflows.md) antes de começar a configuração.

* Você deve ter um circuito do ExpressRoute ativo.
  * Siga as instruções para [criar um circuito do ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) e para que o circuito seja habilitado pelo provedor de conectividade.
  * Verifique se o emparelhamento privado do Azure está configurado para seu circuito. Confira o artigo [Criar e modificar o emparelhamento para um circuito do ExpressRoute](expressroute-howto-routing-portal-resource-manager.md) a fim de obter instruções sobre emparelhamento e roteamento.
  * Verifique se o emparelhamento privado do Azure está configurado e estabelece o emparelhamento via protocolo BGP entre a rede e a Microsoft a fim de proporcionar conectividade de ponta a ponta.
  * Verifique se tem uma rede virtual e um gateway de rede virtual criados e totalmente provisionados. Siga as instruções para [criar um gateway de rede virtual para ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). Um gateway de rede virtual para ExpressRoute usa o GatewayType “ExpressRoute”, não VPN.

* Você pode vincular até 10 redes virtuais a um circuito de ExpressRoute padrão. Todas as redes virtuais deverão estar na mesma região geopolítica ao usar um circuito de ExpressRoute padrão.

* Uma única VNet pode ser vinculada a até 16 circuitos de ExpressRoute. Use o processo a seguir para criar um objeto de conexão para cada circuito do ExpressRoute ao qual você está se conectando. Os circuitos de ExpressRoute podem estar na mesma assinatura, assinaturas diferentes ou uma mistura de ambos.

* Se habilitar o complemento premium do ExpressRoute, você poderá vincular redes virtuais fora da região geopolítica do circuito do ExpressRoute. O complemento premium também permitirá que você conecte mais de 10 redes virtuais ao circuito do ExpressRoute, dependendo da largura de banda escolhida. Confira as [perguntas frequentes](expressroute-faqs.md) para obter mais detalhes sobre o complemento premium.

* Você pode [exibir um vídeo](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit) antes de começar para entender melhor as etapas.

## <a name="connect-a-vnet-to-a-circuit---same-subscription"></a>Conectar uma VNet a um circuito - mesma assinatura

> [!NOTE]
> As informações de configuração do BGP não aparecerão se o provedor da camada 3 tiver configurado os emparelhamentos. Se o circuito estiver no estado de provisionamento, você poderá criar conexões.
>

### <a name="to-create-a-connection"></a>Para criar uma conexão

1. Certifique-se de que o circuito de ExpressRoute e emparelhamento privado do Azure foram configurados com êxito. Siga as instruções contidas em [Criar um circuito do ExpressRoute](expressroute-howto-circuit-arm.md) e [Criar e modificar o emparelhamento para um circuito do ExpressRoute](expressroute-howto-routing-arm.md). O circuito do ExpressRoute deve se parecer com a imagem a seguir:

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/express-route-circuit.png" alt-text="Captura de tela de circuito do ExpressRoute":::

1. Agora, você pode começar a provisionar uma conexão para vincular seu gateway de rede virtual ao circuito de ExpressRoute. Selecione **Conexão** > **Adicionar** para abrir a página **Adicionar conexão**.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/add-connection.png" alt-text="Adicionar captura de tela de conexão":::

1. Insira um nome para a conexão e selecione **Avançar: Configurações >** .

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/create-connection-basic.png" alt-text="Página básica Criar conexão":::

1. Selecione o gateway que pertence à rede virtual que você deseja vincular ao circuito e selecione **Examinar + criar**. Em seguida, selecione **Criar** depois de concluir a validação.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/create-connection-settings.png" alt-text="Página Criar configurações de conexão":::

1. Depois que sua conexão foi configurada com êxito, seu objeto de conexão mostrará as informações para a conexão.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/connection-object.png" alt-text="Captura de tela de objeto de conexão":::

## <a name="connect-a-vnet-to-a-circuit---different-subscription"></a>Conectar uma VNet a um circuito - assinatura diferente

Você pode compartilhar um circuito do ExpressRoute entre várias assinaturas. A figura a seguir mostra um esquema simples de como funciona o compartilhamento de circuitos do ExpressRoute entre várias assinaturas.

:::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/cross-subscription.png" alt-text="Conectividade entre assinaturas":::

Cada uma das nuvens menores dentro da nuvem grande é usada para representar assinaturas pertencentes a diferentes departamentos dentro de uma organização. Cada um dos departamentos dentro da organização usa a própria assinatura para implantar os serviços deles, mas podem compartilhar um circuito do ExpressRoute para se conectar de volta à sua rede local. Um único departamento (neste exemplo: TI) pode ter o circuito do ExpressRoute. Outras assinaturas dentro da organização podem usar o circuito do ExpressRoute.

  > [!NOTE]
  > As cobranças por conectividade e largura de banda do circuito dedicado serão aplicadas ao proprietário do circuito do ExpressRoute. Todas as redes virtuais compartilham a mesma largura de banda.
  >

### <a name="administration---about-circuit-owners-and-circuit-users"></a>Administração – sobre proprietários do circuito e usuários do circuito

O “proprietário do circuito” é um usuário avançado autorizado do recurso de circuito do ExpressRoute. O proprietário do circuito pode criar autorizações que podem ser resgatadas pelos ‘usuários do circuito’. Os usuários do circuito são proprietários de gateways de rede virtual que não estão na mesma assinatura que o circuito do ExpressRoute. Usuários do circuito podem resgatar autorizações (uma autorização por rede virtual).

O proprietário do circuito tem a capacidade de modificar e revogar autorizações a qualquer momento. Revogar uma autorização faz com que todas as conexões de links sejam excluídas da assinatura cujo acesso foi revogado.

### <a name="circuit-owner-operations"></a>Operações do proprietário do circuito

**Criar uma autorização de conexão**

O proprietário do circuito cria uma autorização, que, por sua vez, cria uma chave de autorização que pode ser usada por um usuário do circuito para conectar os respectivos gateways de rede virtual ao circuito do ExpressRoute. Uma autorização é válida apenas para uma conexão.

> [!NOTE]
> Cada conexão requer uma autorização separada.
>

1. Na página ExpressRoute, selecione **Autorizações** e digite um **nome** para a autorização e selecione **Salvar**.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/authorization.png" alt-text="Autorizações":::

2. Após a gravação da configuração, copie a **ID do Recurso** e a **Chave de Autorização**.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/authorization-key.png" alt-text="Chave de autorização":::

**Excluir uma autorização de conexão**

Você pode excluir uma conexão selecionando o ícone **Excluir** para a chave de autorização da conexão.

:::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/delete-authorization-key.png" alt-text="Excluir chave de autorização":::

Se você quiser excluir a conexão, mas reter a chave de autorização, poderá excluir a conexão da página de conexão do circuito.

:::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/delete-connection-owning-circuit.png" alt-text="Excluir circuito de propriedade de conexão":::

### <a name="circuit-user-operations"></a>Operações do usuário do circuito

O usuário do circuito precisa da ID do recurso e de uma chave de autorização do proprietário do circuito.

**Resgatar uma autorização de conexão**

1. Selecione o botão **+ Criar um recurso**. Pesquise **Conexão** e selecione **Criar**.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/create-new-resources.png" alt-text="Criar novos recursos":::

1. Verifique se o *Tipo de conexão* está definido como **ExpressRoute**. Selecione o *Grupo de recursos* e a *Localização*. Em seguida, selecione **OK** na página Noções básicas.

    > [!NOTE]
    > A localização *precisa* corresponder ao local do gateway de rede virtual para a qual você está criando a conexão.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/connection-basics.png" alt-text="Página de noções básicas":::

1. Na página **Configurações**, selecione *Gateway de rede Virtual* e marque a caixa de seleção **Resgatar autorização**. Insira a *Chave de autorização* e o *URI do circuito par* e nomeie a conexão. Selecione **OK**. 
 
    > [!NOTE]
    > O *URI do circuito do par* é a ID do Recurso do circuito do ExpressRoute (que pode ser encontrado no painel de Configuração de Propriedades do circuito do ExpressRoute).

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/connection-settings.png" alt-text="Página Configurações":::

1. Examine as informações da página **Resumo** e selecione **OK**.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/connection-summary.png" alt-text="Página Resumo":::

## <a name="clean-up-resources"></a>Limpar os recursos

É possível excluir uma conexão e desvincular a VNet de um circuito ExpressRoute, selecionando o ícone **Excluir** na página da sua conexão.

:::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/delete-connection.png" alt-text="Excluir conexão":::

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a conectar uma rede virtual a um circuito na mesma assinatura e em uma assinatura diferente. Para obter mais informações sobre o gateway do ExpressRoute, confira: 

> [!div class="nextstepaction"]
> [Sobre os gateways de rede virtual do ExpressRoute](expressroute-about-virtual-network-gateways.md)
