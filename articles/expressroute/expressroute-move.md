---
title: 'ExpressRoute: mover circuitos do clássico para o Azure Resource Manager'
description: Saiba mais sobre o que acontece quando você move um circuito do Azure ExpressRoute do modelo de implantação clássico para o Azure Resource Manager.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/15/2020
ms.author: duau
ms.openlocfilehash: dcba2e9de2b37e8c432f94781b3c4c369ad52395
ms.sourcegitcommit: 02ed9acd4390b86c8432cad29075e2204f6b1bc3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/29/2020
ms.locfileid: "97807934"
---
# <a name="moving-expressroute-circuits-from-the-classic-to-the-resource-manager-deployment-model"></a>Mova os circuitos do ExpressRoute do clássico para o modelo de implantação do Gerenciador de Recursos
Este artigo fornece uma visão geral do que acontece quando você move um circuito do Azure ExpressRoute do modelo de implantação clássico para o Azure Resource Manager.

Você pode usar um único circuito do ExpressRoute para conectar redes virtuais que são implantadas nos modelos de implantação clássico e do Resource Manager.

![Um circuito do ExpressRoute que vincula as redes virtuais nos dois modelos de implantação](./media/expressroute-move/expressroute-move-1.png)

## <a name="expressroute-circuits-that-are-created-in-the-classic-deployment-model"></a>Circuitos do ExpressRoute criados no modelo de implantação clássico
Os circuitos do ExpressRoute criados no modelo de implantação clássico precisam migrar primeiro para o modelo de implantação do Gerenciador de recursos. Só então, pode habilitar a conectividade com os modelos de implantação clássico e do Resource Manager. A conectividade não está em perda ou foi interrompida quando uma conexão está sendo movida. Todos os links de rede de circuito para virtual no modelo de implantação clássico dentro da mesma assinatura e entre assinaturas são preservados.

Depois que a movimentação for concluída com êxito, o circuito do ExpressRoute se comportará exatamente como um circuito do ExpressRoute criado no modelo de implantação do Gerenciador de recursos. Agora, você poderá criar conexões com as redes virtuais no modelo de implantação do Gerenciador de Recursos.

Depois de mover o circuito do ExpressRoute para o modelo de implantação do Gerenciador de recursos, você só poderá gerenciá-lo no modelo de implantação do Gerenciador de recursos. As operações para gerenciar emparelhamentos, atualizar propriedades de circuito e excluir circuitos só estarão disponíveis por meio do modelo de implantação do Resource Manager. Consulte a seção a seguir para obter mais detalhes sobre como você pode gerenciar o acesso a ambos os modelos de implantação.

Você não precisa envolver seu provedor de conectividade para mover seu circuito para o modelo de implantação do Gerenciador de recursos.

## <a name="expressroute-circuits-that-are-created-in-the-resource-manager-deployment-model"></a>Circuitos do ExpressRoute criados no modelo de implantação do Gerenciador de Recursos
Você pode habilitar os circuitos do ExpressRoute criados no modelo de implantação do Gerenciador de Recursos para que sejam acessíveis a partir de ambos os modelos de implantação. Qualquer circuito do ExpressRoute em sua assinatura pode ser configurado para ter acesso de ambos os modelos de implantação.

* Os circuitos do ExpressRoute que foram criados no modelo de implantação do Gerenciador de recursos não têm acesso ao modelo de implantação clássico por padrão.
* Os circuitos do ExpressRoute que foram movidos do modelo de implantação clássico para o modelo de implantação do Gerenciador de recursos podem ser acessados de ambos os modelos de implantação por padrão.
* Um circuito do ExpressRoute sempre tem acesso ao modelo de implantação do Resource Manager, seja ele criado no modelo de implantação do Resource Manager ou clássico. Você pode criar conexões com redes virtuais seguindo instruções sobre [como vincular redes virtuais](expressroute-howto-linkvnet-arm.md).
* O acesso ao modelo de implantação clássico é controlado pelo parâmetro **allowClassicOperations** no circuito do ExpressRoute.

> [!IMPORTANT]
> Todas as cotas documentadas na página de [limites do serviço](../azure-resource-manager/management/azure-subscription-service-limits.md) aplicam-se. Por exemplo, um circuito padrão pode ter no máximo 10 links de rede virtual/conexões nos modelos de implantação clássico e do Gerenciador de Recursos.
> 
> 

## <a name="controlling-access-to-the-classic-deployment-model"></a>Controlando o acesso ao modelo de implantação clássico
Você pode habilitar um circuito do ExpressRoute para vincular a redes virtuais em ambos os modelos de implantação. Para fazer isso, defina o parâmetro **allowClassicOperations** no circuito do ExpressRoute.

A definição de **allowClassicOperations** para TRUE permite vincular as redes virtuais de ambos os modelos de implantação ao circuito do ExpressRoute. 
* Para vincular redes virtuais no modelo de implantação clássico, consulte [como vincular redes virtuais para o modelo de implantação clássico](expressroute-howto-linkvnet-classic.md).
* Para vincular redes virtuais no modelo de implantação do Resource Manager, consulte [como vincular redes virtuais no modelo de implantação do Gerenciador de recursos](expressroute-howto-linkvnet-arm.md).

Definir **allowClassicOperations** para FALSE bloqueia o acesso ao circuito a partir do modelo de implantação clássico. No entanto, todas as redes virtuais vinculadas no modelo de implantação clássico ainda são preservadas. O circuito de ExpressRoute não está visível no modelo de implantação clássico.

## <a name="supported-operations-in-the-classic-deployment-model"></a>Operações com suporte no modelo de implantação clássico
As seguintes operações clássicas têm suporte em um circuito do ExpressRoute quando **allowClassicOperations** é definido para TRUE:

* Obter informações do circuito do ExpressRoute
* Criar/atualizar/get/excluir links da rede virtual para as redes virtuais clássicas
* Criar/atualizar/obter/excluir autorizações de link da rede virtual para a conectividade entre as assinaturas

No entanto, quando **allowClassicOperations** é definido como true, você não pode executar as seguintes operações clássicas:

* Criar/atualizar/obter/excluir emparelhamentos BGP (Border Gateway Protocol ) para os emparelhamentos dos Azures privado e público, e da Microsoft
* Excluir circuitos do ExpressRoute

## <a name="communication-between-the-classic-and-the-resource-manager-deployment-models"></a>Comunicação entre os modelos de implantação clássico do Gerenciador de Recursos
O circuito do ExpressRoute atua como uma ponte entre os modelos de implantação clássico e do Gerenciador de Recursos. O tráfego entre as redes virtuais para ambos os modelos de implantação pode passar pelo circuito do ExpressRoute se ambas as redes virtuais estiverem vinculadas ao mesmo circuito.

A taxa de transferência agregada é limitada pela capacidade da taxa de transferência do gateway de rede virtual. O tráfego não insere as redes do provedor de conectividade ou suas redes nesses casos. O fluxo do tráfego entre as redes virtuais está totalmente contido na rede da Microsoft.

## <a name="access-to-azure-public-and-microsoft-peering-resources"></a>Acesso aos recursos de emparelhamento do Azure público e da Microsoft
Você pode continuar a acessar os recursos normalmente acessíveis por meio do emparelhamento do Azure público e do emparelhamento da Microsoft sem interrupções.  

## <a name="whats-supported"></a>O que tem suporte
Esta seção descreve o que tem suporte para os circuitos do ExpressRoute:

* Você pode usar um único circuito do ExpressRoute para conectar as redes virtuais implantadas nos modelos de implantação clássico e do Gerenciador de Recursos.
* É possível mover um circuito do ExpressRoute do clássico para o modelo de implantação do Gerenciador de Recursos. Depois de movido, o circuito do ExpressRoute continuará a operar como qualquer outro circuito do ExpressRoute criado no modelo de implantação do Gerenciador de recursos.
* É possível mover apenas o circuito do ExpressRoute. Os links do circuito, redes virtuais e gateways VPN não podem ser movidos com essa operação.
* Depois de um circuito do ExpressRoute ser movido para o modelo de implantação do Gerenciador de Recursos, você poderá gerenciar o ciclo de vida desse circuito usando apenas o modelo de implantação do Gerenciador de Recursos. Isso significa que você pode executar operações como adicionar/atualizar/excluir emparelhamentos, atualizar propriedades de circuito (como largura de banda, SKU e tipo de cobrança) e excluir circuitos somente no modelo de implantação do Gerenciador de recursos.
* O circuito do ExpressRoute atua como uma ponte entre os modelos de implantação clássico e do Gerenciador de Recursos. O tráfego entre máquinas virtuais em redes virtuais clássicas e máquinas virtuais nas redes virtuais do Resource Manager pode se comunicar por meio do ExpressRoute se ambas as redes virtuais estiverem vinculadas ao mesmo circuito do ExpressRoute.
* A conectividade entre as assinaturas é suportada nos modelos de implantação clássico e do Gerenciador de Recursos.
* Depois de mover um circuito do ExpressRoute do modelo clássico para o modelo do Azure Resource Manager, você pode [migrar as redes virtuais vinculadas ao circuito do ExpressRoute](expressroute-migration-classic-resource-manager.md).

## <a name="whats-not-supported"></a>O que não tem suporte
Esta seção descreve o que não tem suporte para os circuitos do ExpressRoute:

* Gerenciar o ciclo de vida de um circuito do ExpressRoute a partir do modelo de implantação clássico.
* Suporte do Azure RBAC (controle de acesso baseado em função) para o modelo de implantação clássico. Você não pode executar controles RBAC do Azure em um circuito no modelo de implantação clássico. Nenhum administrador/coadministrator da assinatura pode vincular ou desvincular as redes virtuais para o circuito.

## <a name="configuration"></a>Configuração
Siga as instruções descritas em [Mover um circuito do ExpressRoute do clássico para o modelo de implantação do Gerenciador de Recursos](expressroute-howto-move-arm.md).

## <a name="next-steps"></a>Próximas etapas
* [Migrar as redes virtuais vinculadas ao circuito do ExpressRoute a partir do modelo clássico para o modelo do Azure Resource Manager](expressroute-migration-classic-resource-manager.md)
* Para obter informações sobre o fluxo de trabalho, consulte [Fluxos de trabalho de provisionamento e estados do circuito do ExpressRoute](expressroute-workflows.md).
* Para configurar sua conexão do ExpressRoute:
  
  * [Criar um circuito do ExpressRoute](expressroute-howto-circuit-arm.md)
  * [Configurar o roteamento](expressroute-howto-routing-arm.md)
  * [Vincular uma rede virtual a um circuito do ExpressRoute](expressroute-howto-linkvnet-arm.md)

