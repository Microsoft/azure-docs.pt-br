---
title: "Movendo os circuitos da ExpressRoute do clássico para o Gerenciador de Recursos | Microsoft Docs"
description: "Esta página fornece uma visão geral do que você precisa saber ao fazer uma ponte entre os modelos de implantação clássico e do Gerenciador de Recursos."
documentationcenter: na
services: expressroute
author: ganesr
manager: carmonm
editor: 
ms.assetid: bdf01217-1a98-4ec0-a08e-d84fd37f78af
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/02/2017
ms.author: ganesr
ms.openlocfilehash: 7f8386b518ada850fc03e23c5cae3b159b3b213e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="moving-expressroute-circuits-from-the-classic-to-the-resource-manager-deployment-model"></a>Mova os circuitos do ExpressRoute do clássico para o modelo de implantação do Gerenciador de Recursos
Este artigo fornece uma visão geral do que significa mover um circuito do ExpressRoute do clássico para o modelo de implantação do Azure Resource Manager.

Você pode usar um único circuito do ExpressRoute para conectar as redes virtuais implantadas nos modelos clássico e de implantação do Gerenciador de Recursos. Um circuito do ExpressRoute, independentemente de como é criado, agora pode ser vinculado às redes virtuais nos dois modelos de implantação.

![Um circuito do ExpressRoute que vincula as redes virtuais nos dois modelos de implantação](./media/expressroute-move/expressroute-move-1.png)

## <a name="expressroute-circuits-that-are-created-in-the-classic-deployment-model"></a>Circuitos do ExpressRoute criados no modelo de implantação clássico
Os circuitos do ExpressRoute criados no modelo de implantação clássico precisam ser movidos para o modelo de implantação do Gerenciador de Recursos primeiro para habilitar a conectividade com os modelos de implantação clássico e do Gerenciador de Recursos. Não há perda de conectividade nem interrupção quando uma conexão está sendo movida. Todos os links entre a rede virtual e o circuito no modelo de implantação clássico (dentro da mesma assinatura e entre as assinaturas) são preservados.

Depois da migração ser concluída com êxito, o circuito do ExpressRoute será parecido e executado exatamente como um circuito do ExpressRoute criado no modelo de implantação do Gerenciador de Recursos. Agora, você poderá criar conexões com as redes virtuais no modelo de implantação do Gerenciador de Recursos.

Depois de um circuito do ExpressRoute ser movido para o modelo de implantação do Gerenciador de Recursos, você poderá gerenciar o ciclo de vida desse circuito usando apenas o modelo de implantação do Gerenciador de Recursos. Isso significa que você pode realizar operações como adicionar/atualizar/excluir emparelhamentos, atualizar as propriedades do circuito (como largura de banda, SKU e tipo de cobrança) e excluir os circuitos apenas no modelo de implantação do Gerenciador de Recursos. Consulte a seção abaixo sobre os circuitos criados no modelo de implantação do Gerenciador de Recursos para obter mais detalhes sobre como você pode gerenciar o acesso a ambos os modelos de implantação.

Você não precisa envolver seu provedor de conectividade para realizar a migração.

## <a name="expressroute-circuits-that-are-created-in-the-resource-manager-deployment-model"></a>Circuitos do ExpressRoute criados no modelo de implantação do Gerenciador de Recursos
Você pode habilitar os circuitos do ExpressRoute criados no modelo de implantação do Gerenciador de Recursos para que sejam acessíveis a partir de ambos os modelos de implantação. Qualquer circuito do ExpressRoute em sua assinatura pode ser habilitado para ser acessado a partir de ambos os modelos de implantação.

* Os circuitos do ExpressRoute criados no modelo de implantação do Gerenciador de Recursos não têm acesso ao modelo de implantação clássico por padrão.
* Os circuitos do ExpressRoute movidos do modelo de implantação clássico para o modelo de implantação do Gerenciador de Recursos são acessíveis a partir de ambos os modelos de implantação por padrão.
* Um circuito do ExpressRoute sempre tem acesso ao modelo de implantação do Gerenciador de Recursos, independentemente dele ter sido criado no modelo de implantação do Gerenciador de Recursos ou clássico. Isso significa que você pode criar conexões com as redes virtuais criadas no modelo de implantação do Gerenciador de Recursos seguindo as instruções em [como vincular as redes virtuais](expressroute-howto-linkvnet-arm.md).
* O acesso ao modelo de implantação clássico é controlado pelo parâmetro **allowClassicOperations** no circuito do ExpressRoute.

> [!IMPORTANT]
> Todas as cotas documentadas na página de [limites do serviço](../azure-subscription-service-limits.md) aplicam-se. Por exemplo, um circuito padrão pode ter no máximo 10 links de rede virtual/conexões nos modelos de implantação clássico e do Gerenciador de Recursos.
> 
> 

## <a name="controlling-access-to-the-classic-deployment-model"></a>Controlando o acesso ao modelo de implantação clássico
Você pode habilitar um único circuito do ExpressRoute para vincular as redes virtuais em ambos os modelos de implantação definindo o parâmetro **allowClassicOperations** do circuito do ExpressRoute.

A definição de **allowClassicOperations** para TRUE permite vincular as redes virtuais de ambos os modelos de implantação ao circuito do ExpressRoute. Você pode vincular as redes virtuais no modelo de implantação clássico seguindo as diretrizes em [como vincular as redes virtuais no modelo de implantação clássico](expressroute-howto-linkvnet-classic.md). Você pode vincular as redes virtuais no modelo de implantação do Gerenciador de Recursos seguindo as diretrizes em [como vincular as redes virtuais no modelo de implantação do Gerenciador de Recursos](expressroute-howto-linkvnet-arm.md).

Definir **allowClassicOperations** para FALSE bloqueia o acesso ao circuito a partir do modelo de implantação clássico. Porém, todos os links da rede virtual no modelo de implantação clássico são preservados. Nesse caso, o circuito do ExpressRoute não é visível no modelo de implantação clássico.

## <a name="supported-operations-in-the-classic-deployment-model"></a>Operações com suporte no modelo de implantação clássico
As seguintes operações clássicas têm suporte em um circuito do ExpressRoute quando **allowClassicOperations** é definido para TRUE:

* Obter informações do circuito do ExpressRoute
* Criar/atualizar/get/excluir links da rede virtual para as redes virtuais clássicas
* Criar/atualizar/obter/excluir autorizações de link da rede virtual para a conectividade entre as assinaturas

Não é possível executar as seguintes operações clássicas quando **allowClassicOperations** está definido para TRUE:

* Criar/atualizar/obter/excluir emparelhamentos BGP (Border Gateway Protocol ) para os emparelhamentos dos Azures privado e público, e da Microsoft
* Excluir circuitos do ExpressRoute

## <a name="communication-between-the-classic-and-the-resource-manager-deployment-models"></a>Comunicação entre os modelos de implantação clássico do Gerenciador de Recursos
O circuito do ExpressRoute atua como uma ponte entre os modelos de implantação clássico e do Gerenciador de Recursos. O tráfego entre as máquinas virtuais nas redes virtuais no modelo de implantação clássico e aquelas nas redes virtuais no modelo de implantação do Gerenciador de Recursos fluirá pelo ExpressRoute se ambas as redes virtuais estiverem vinculadas ao mesmo circuito do ExpressRoute.

A taxa de transferência agregada é limitada pela capacidade da taxa de transferência do gateway de rede virtual. O tráfego não entra nas redes do provedor de conectividade nem em suas redes em tais casos. O fluxo do tráfego entre as redes virtuais está totalmente contido na rede da Microsoft.

## <a name="access-to-azure-public-and-microsoft-peering-resources"></a>Acesso aos recursos de emparelhamento do Azure público e da Microsoft
Você pode continuar a acessar os recursos normalmente acessíveis por meio do emparelhamento do Azure público e do emparelhamento da Microsoft sem interrupções.  

## <a name="whats-supported"></a>O que tem suporte
Esta seção descreve o que tem suporte para os circuitos do ExpressRoute:

* Você pode usar um único circuito do ExpressRoute para conectar as redes virtuais implantadas nos modelos de implantação clássico e do Gerenciador de Recursos.
* É possível mover um circuito do ExpressRoute do clássico para o modelo de implantação do Gerenciador de Recursos. Após a migração, o circuito do ExpressRoute é parecido e executado como qualquer outro circuito do ExpressRoute criado no modelo de implantação do Gerenciador de Recursos.
* É possível mover apenas o circuito do ExpressRoute. Os links do circuito, redes virtuais e gateways VPN não podem ser movidos com essa operação.
* Depois de um circuito do ExpressRoute ser movido para o modelo de implantação do Gerenciador de Recursos, você poderá gerenciar o ciclo de vida desse circuito usando apenas o modelo de implantação do Gerenciador de Recursos. Isso significa que você pode realizar operações como adicionar/atualizar/excluir emparelhamentos, atualizar as propriedades do circuito (como largura de banda, SKU e tipo de cobrança) e excluir os circuitos apenas no modelo de implantação do Gerenciador de Recursos.
* O circuito do ExpressRoute atua como uma ponte entre os modelos de implantação clássico e do Gerenciador de Recursos. O tráfego entre as máquinas virtuais nas redes virtuais no modelo de implantação clássico e aquelas nas redes virtuais no modelo de implantação do Gerenciador de Recursos fluirá pelo ExpressRoute se ambas as redes virtuais estiverem vinculadas ao mesmo circuito do ExpressRoute.
* A conectividade entre as assinaturas é suportada nos modelos de implantação clássico e do Gerenciador de Recursos.
* Depois de mover um circuito do ExpressRoute do modelo clássico para o modelo do Azure Resource Manager, você pode [migrar as redes virtuais vinculadas ao circuito do ExpressRoute](expressroute-migration-classic-resource-manager.md).

## <a name="whats-not-supported"></a>O que não tem suporte
Esta seção descreve o que não tem suporte para os circuitos do ExpressRoute:

* Gerenciar o ciclo de vida de um circuito do ExpressRoute a partir do modelo de implantação clássico.
* Suporte RBAC (Controle de Acesso Baseado em Funções) para o modelo de implantação clássico. Não é possível executar os controles RBAC para um circuito no modelo de implantação clássico. Nenhum administrador/coadministrator da assinatura pode vincular ou desvincular as redes virtuais para o circuito.

## <a name="configuration"></a>Configuração
Siga as instruções descritas em [Mover um circuito do ExpressRoute do clássico para o modelo de implantação do Gerenciador de Recursos](expressroute-howto-move-arm.md).

## <a name="next-steps"></a>Próximas etapas
* [Migrar as redes virtuais vinculadas ao circuito do ExpressRoute a partir do modelo clássico para o modelo do Azure Resource Manager](expressroute-migration-classic-resource-manager.md)
* Para obter informações sobre o fluxo de trabalho, consulte [Fluxos de trabalho de provisionamento e estados do circuito do ExpressRoute](expressroute-workflows.md).
* Para configurar sua conexão do ExpressRoute:
  
  * [Criar um circuito do ExpressRoute](expressroute-howto-circuit-arm.md)
  * [Configurar o roteamento](expressroute-howto-routing-arm.md)
  * [Vincular uma rede virtual a um circuito de ExpressRoute](expressroute-howto-linkvnet-arm.md)

