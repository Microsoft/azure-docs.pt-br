---
title: 'Vincular uma rede virtual a um circuito ExpressRoute: portal do Azure | Microsoft Docs'
description: "Este documento apresenta uma visão geral de como vincular redes virtuais (VNets) a circuitos do ExpressRoute."
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f5cb5441-2fba-46d9-99a5-d1d586e7bda4
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/12/2017
ms.author: cherylmc
ms.openlocfilehash: 34caed9323ae3067d1dc17ab9c62ebf7a9be855b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit"></a>Conectar uma rede virtual a um circuito do ExpressRoute
> [!div class="op_single_selector"]
> * [Portal do Azure](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [CLI do Azure](howto-linkvnet-cli.md)
> * [Vídeo – Portal do Azure](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-linkvnet-classic.md)
> 

Este artigo ajuda você a vincular as redes virtuais (VNets) aos circuitos do Azure ExpressRoute usando o modelo de implantação do Resource Manager e o Portal do Azure. As redes virtuais podem estar na mesma assinatura ou fazer parte de outra assinatura.

## <a name="before-you-begin"></a>Antes de começar
* Analise os [pré-requisitos](expressroute-prerequisites.md), os [requisitos de roteamento](expressroute-routing.md) e os [fluxos de trabalho](expressroute-workflows.md) antes de começar a configuração.
* Você deve ter um circuito do ExpressRoute ativo.
  
  * Siga as instruções para [criar um circuito do ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) e para que o circuito seja habilitado pelo provedor de conectividade.
  * Verifique se o emparelhamento privado do Azure está configurado para seu circuito. Veja o artigo [Configurar roteamento](expressroute-howto-routing-portal-resource-manager.md) para obter instruções sobre roteamento.
  * Verifique se o emparelhamento privado do Azure está configurado e se o emparelhamento BGP entre sua rede e a Microsoft está ativo para que você possa habilitar a conectividade de ponta a ponta.
  * Verifique se tem uma rede virtual e um gateway de rede virtual criados e totalmente provisionados. Siga as instruções para [criar um gateway de rede virtual para ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). Um gateway de rede virtual para ExpressRoute usa o GatewayType “ExpressRoute”, não VPN.

* Você pode vincular até 10 redes virtuais a um circuito de ExpressRoute padrão. Todas as redes virtuais deverão estar na mesma região geopolítica ao usar um circuito de ExpressRoute padrão. 
* Você poderá vincular uma rede virtual fora da região geopolítica do circuito do ExpressRoute ou conectar um grande número de redes virtuais ao circuito do ExpressRoute, se tiver habilitado o complemento premium do ExpressRoute. Confira as [perguntas frequentes](expressroute-faqs.md) para obter mais detalhes sobre o complemento premium.
* Você pode [exibir um vídeo](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit) antes de começar para entender melhor as etapas.

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Conectar uma rede virtual na mesma assinatura a um circuito

### <a name="to-create-a-connection"></a>Para criar uma conexão

> [!NOTE]
> As informações de configuração do BGP não aparecerão se o provedor de camada 3 tiver configurado seus emparelhamentos. Se o circuito estiver no estado de provisionamento, você poderá criar conexões.
>

1. Certifique-se de que o circuito de ExpressRoute e emparelhamento privado do Azure foram configurados com êxito. Siga as instruções nos artigos [Criar um circuito do ExpressRoute](expressroute-howto-circuit-arm.md) e [Configurar o roteamento](expressroute-howto-routing-arm.md). O circuito do ExpressRoute deve se parecer com a imagem a seguir:

    ![Captura de tela de circuito do ExpressRoute](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1.png)
   
2. Agora, você pode começar a provisionar uma conexão para vincular seu gateway de rede virtual ao circuito de ExpressRoute. Clique em **Conexão** > **Adicionar** para abrir a folha **Adicionar conexão** e, em seguida, configure os valores.

    ![Adicionar a captura de tela de conexão](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1.png)  

3. Depois que sua conexão foi configurada com êxito, seu objeto de conexão mostrará as informações para a conexão.

     ![Captura de tela de objeto de conexão](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub2.png)

### <a name="to-delete-a-connection"></a>Para excluir uma conexão
Você pode excluir uma conexão selecionando o ícone **Excluir** na folha de sua conexão.

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Conectar uma rede virtual em uma assinatura diferente a um circuito
Você pode compartilhar um circuito do ExpressRoute entre várias assinaturas. A figura abaixo mostra um esquema simples de como funciona o compartilhamento de circuitos do ExpressRoute entre várias assinaturas.

![Conectividade entre assinaturas](./media/expressroute-howto-linkvnet-portal-resource-manager/cross-subscription.png)

- Cada uma das nuvens menores dentro da nuvem grande é usada para representar assinaturas pertencentes a diferentes departamentos dentro de uma organização.
- Cada um dos departamentos dentro da organização pode usar sua própria assinatura para implantar seus serviços, mas pode compartilhar um único circuito do ExpressRoute para se conectar de volta à respectiva rede local.
- Um único departamento (neste exemplo: TI) pode ter o circuito do ExpressRoute. Outras assinaturas na organização podem usar o circuito do ExpressRoute e as autorizações associadas ao circuito, incluindo as assinaturas vinculadas a outros locatários do Azure Active Directory e os registros do Contrato Enterprise. 

    > [!NOTE]
    > As cobranças por conectividade e largura de banda do circuito dedicado serão aplicadas ao proprietário do circuito do ExpressRoute. Todas as redes virtuais compartilham a mesma largura de banda.
    > 
    >

### <a name="administration---circuit-owners-and-circuit-users"></a>Administração – proprietários e usuários do circuito

O “proprietário do circuito” é um usuário avançado autorizado do recurso de circuito do ExpressRoute. O proprietário do circuito pode criar autorizações que podem ser resgatadas pelos ‘usuários do circuito’. Usuários do circuito são proprietários de gateways de rede virtual que não estão na mesma assinatura que o circuito do ExpressRoute. Usuários do circuito podem resgatar autorizações (uma autorização por rede virtual).

O proprietário do circuito tem a capacidade de modificar e revogar autorizações a qualquer momento. Revogar uma autorização faz com que todas as conexões de links sejam excluídas da assinatura cujo acesso foi revogado.

### <a name="circuit-owner-operations"></a>Operações do proprietário do circuito

**Criar uma autorização de conexão**

O proprietário do circuito cria uma autorização. Isso resulta na criação de uma chave de autorização que pode ser usada por um usuário do circuito para conectar seus gateways de rede virtual ao circuito do ExpressRoute. Uma autorização é válida apenas para uma conexão.

1. Na folha ExpressRoute, clique em **Autorizações** e, em seguida, digite um **nome** para a autorização e clique em **Salvar**.

    ![Autorizações](./media/expressroute-howto-linkvnet-portal-resource-manager/authorization.png)

2. Após a gravação da configuração, copie a **ID do Recurso** e a **Chave de Autorização**.

    ![Chave de autorização](./media/expressroute-howto-linkvnet-portal-resource-manager/authkey.png)

**Excluir uma autorização de conexão**

Você pode excluir uma conexão selecionando o ícone **Excluir** na folha de sua conexão.

### <a name="circuit-user-operations"></a>Operações do usuário do circuito

O usuário do circuito precisa da ID do recurso e de uma chave de autorização do proprietário do circuito. 

**Resgatar uma autorização de conexão**

1.  Clique no botão **+Novo**.

    ![Clique em Novo](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection1.png)

2.  Pesquise **“Conexão”** no Marketplace, selecione-a e clique em **Criar**.

    ![Pesquisar conexão](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection2.png)

3.  Verifique se o **Tipo de conexão** está definido como "ExpressRoute".


4.  Preencha os detalhes e clique em **OK** na folha Conceitos básicos.

    ![Folha de Noções básicas](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection3.png)

5.  Na folha **Configurações**, selecione **Gateway de rede virtual** e marque a caixa de seleção **Resgatar autorização**.

6.  Insira a **Chave de autorização** e o **URI do circuito par** e nomeie a conexão. Clique em **OK**.

    ![Folha de configurações](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection4.png)

7. Examine as informações na folha **Resumo** e clique em **OK**.


**Liberar uma autorização de conexão**

É possível liberar uma autorização excluindo a conexão que vincula o circuito do ExpressRoute à rede virtual.

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre o ExpressRoute, consulte [Perguntas Frequentes sobre ExpressRoute](expressroute-faqs.md).
