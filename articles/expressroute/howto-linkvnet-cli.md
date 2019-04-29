---
title: 'Vincular uma rede virtual a um circuito de ExpressRoute: CLI: Azure| Microsoft Docs'
description: Este artigo mostra como vincular as redes virtuais (VNets) aos circuitos do Microsoft Azure ExpressRoute usando o modelo de implantação do Resource Manager e CLI.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: anzaman,cherylmc
ms.custom: seodec18
ms.openlocfilehash: 5ddcfe14873d13384b043f7a977dc4f069dbe8dd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60883072"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-cli"></a>Conectar uma rede virtual a um circuito de ExpressRoute usando a CLI

Este artigo ajuda a vincular redes virtuais (VNets) aos circuitos de ExpressRoute do Azure usando a CLI. Para vincular usando a CLI do Azure, as redes virtuais devem ser criadas usando o modelo de implantação do Resource Manager. Elas podem estar na mesma assinatura ou fazer parte de outra assinatura. Se quiser usar um método diferente para conectar sua VNet a um circuito de ExpressRoute, você poderá selecionar um artigo na lista a seguir:

> [!div class="op_single_selector"]
> * [Portal do Azure](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [CLI do Azure](howto-linkvnet-cli.md)
> * [Vídeo – Portal do Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-linkvnet-classic.md)
> 

## <a name="configuration-prerequisites"></a>Pré-requisitos de configuração

* Você precisa da versão mais recente da CLI (interface de linha de comando). Para obter mais informações, consulte [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

* Leia os [pré-requisitos](expressroute-prerequisites.md), os [requisitos de roteamento](expressroute-routing.md) e os [fluxos de trabalho](expressroute-workflows.md) antes de começar a configuração.

* Você deve ter um circuito do ExpressRoute ativo. 
  * Siga as instruções para [criar um circuito do ExpressRoute](howto-circuit-cli.md) e para que o circuito seja habilitado pelo provedor de conectividade. 
  * Verifique se o emparelhamento privado do Azure está configurado para seu circuito. Veja o artigo [Configurar roteamento](howto-routing-cli.md) para obter instruções sobre roteamento. 
  * Verifique se o emparelhamento particular do Azure está configurado. O emparelhamento via protocolo BGP entre sua rede e a Microsoft deve estar ativo para que você possa habilitar a conectividade de ponta a ponta.
  * Verifique se tem uma rede virtual e um gateway de rede virtual criados e totalmente provisionados. Siga as instruções para [Configurar um gateway de rede virtual para ExpressRoute](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli). Certifique-se de usar `--gateway-type ExpressRoute`.

* Você pode vincular até 10 redes virtuais a um circuito do ExpressRoute padrão. Todas as redes virtuais deverão estar na mesma região geopolítica ao usar um circuito de ExpressRoute padrão. 

* Uma única rede virtual pode ser vinculada a até quatro circuitos de ExpressRoute. Use as etapas a seguir para criar uma nova conexão para cada circuito de ExpressRoute ao qual você está se conectando. Os circuitos de ExpressRoute podem estar na mesma assinatura, assinaturas diferentes ou uma mistura de ambos.

* Se você tiver habilitado o complemento premium do ExpressRoute, você poderá vincular uma rede virtual fora da região geopolítica do circuito de ExpressRoute ou conectar um grande número de redes virtuais ao circuito de ExpressRoute. Para obter mais informações sobre o complemento premium, consulte as [Perguntas Frequentes](expressroute-faqs.md).

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Conectar uma rede virtual na mesma assinatura a um circuito

Você pode conectar um gateway de rede virtual a um circuito do ExpressRoute usando o exemplo. Verifique se o gateway de rede virtual foi criado e se está pronto para vinculação antes de executar o comando.

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Conectar uma rede virtual em uma assinatura diferente a um circuito

Você pode compartilhar um circuito do ExpressRoute entre várias assinaturas. A figura abaixo mostra um esquema simples de como funciona o compartilhamento de circuitos do ExpressRoute entre várias assinaturas.

Cada uma das nuvens menores dentro da nuvem grande é usada para representar assinaturas pertencentes a diferentes departamentos dentro de uma organização. Cada um dos departamentos dentro da organização pode usar sua própria assinatura para implantar seus serviços, mas pode compartilhar um único circuito do ExpressRoute para se conectar de volta à respectiva rede local. Um único departamento (neste exemplo: IT) pode ser proprietário do circuito do Microsoft Azure ExpressRoute. Outras assinaturas dentro da organização podem usar o circuito de ExpressRoute.

> [!NOTE]
> As cobranças por conectividade e largura de banda do circuito dedicado serão aplicadas ao proprietário do circuito de ExpressRoute. Todas as redes virtuais compartilham a mesma largura de banda.
> 
> 

![Conectividade entre assinaturas](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration---circuit-owners-and-circuit-users"></a>Administração – proprietários e usuários do circuito

O 'proprietário do circuito' é um usuário avançado autorizado do recurso de circuito de ExpressRoute. O proprietário do circuito pode criar autorizações que podem ser resgatadas pelos 'usuários do circuito'. Usuários do circuito são proprietários de gateways de rede virtual que não estão na mesma assinatura que o circuito de ExpressRoute. Usuários do circuito podem resgatar autorizações (uma autorização por rede virtual).

O proprietário do circuito tem a capacidade de modificar e revogar autorizações a qualquer momento. Revogar uma autorização faz com que todas as conexões de links sejam excluídas da assinatura cujo acesso foi revogado.

### <a name="circuit-owner-operations"></a>Operações do proprietário do circuito

**Criar uma autorização**

O proprietário do circuito cria uma autorização, que por sua vez cria uma chave de autorização que pode ser usada por um usuário do circuito para conectar seus gateways de rede virtual ao circuito do ExpressRoute. Uma autorização é válida apenas para uma conexão.

O exemplo a seguir mostra como criar uma autorização:

```azurecli
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization
```

A resposta para isso conterá a chave de autorização e o status:

```azurecli
"authorizationKey": "0a7f3020-541f-4b4b-844a-5fb43472e3d7",
"authorizationUseStatus": "Available",
"etag": "W/\"010353d4-8955-4984-807a-585c21a22ae0\"",
"id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/authorizations/MyAuthorization1",
"name": "MyAuthorization1",
"provisioningState": "Succeeded",
"resourceGroup": "ExpressRouteResourceGroup"
```

**Examinar autorizações**

O proprietário do circuito pode examinar todas as autorizações emitidas em um circuito específico executando o seguinte exemplo:

```azurecli
az network express-route auth list --circuit-name MyCircuit -g ExpressRouteResourceGroup
```

**Adicionar autorizações**

O proprietário do circuito pode adicionar autorizações usando o exemplo a seguir:

```azurecli
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

**Excluir autorizações**

O proprietário do circuito pode revogar/excluir autorizações usando o exemplo a seguir:

```azurecli
az network express-route auth delete --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

### <a name="circuit-user-operations"></a>Operações do usuário do circuito

O usuário do circuito precisa da ID do par e de uma chave de autorização do proprietário do circuito. A chave de autorização é um GUID.

```azurecli
Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
```

**Resgatar uma autorização de conexão**

O usuário de circuito pode executar o seguinte exemplo para resgatar uma autorização de vínculo:

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit --authorization-key "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**Liberar uma autorização de conexão**

É possível liberar uma autorização excluindo a conexão que vincula o circuito do ExpressRoute à rede virtual.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o ExpressRoute, consulte [Perguntas Frequentes sobre ExpressRoute](expressroute-faqs.md).
