---
title: 'Tutorial: Vincular uma rede virtual a um circuito do ExpressRoute – CLI do Azure'
description: Este tutorial mostra como vincular as redes virtuais (VNets) aos circuitos do ExpressRoute usando o modelo de implantação do Resource Manager e a CLI do Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/08/2020
ms.author: duau
ms.openlocfilehash: 90676b134d06af7ef088b2041116b17022da2700
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106110216"
---
# <a name="tutorial-connect-a-virtual-network-to-an-expressroute-circuit-using-cli"></a>Tutorial: Conectar uma rede virtual a um circuito de ExpressRoute usando a CLI

Este tutorial mostra como vincular VNets (redes virtuais) aos circuitos do Azure ExpressRoute usando a CLI do Azure. Para vincular usando a CLI do Azure, as redes virtuais devem ser criadas usando o modelo de implantação do Resource Manager. Elas podem estar na mesma assinatura ou fazer parte de outra assinatura. Se quiser usar um método diferente para conectar sua VNet a um circuito de ExpressRoute, você poderá selecionar um artigo na lista a seguir:

> [!div class="op_single_selector"]
> * [Azure portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [CLI do Azure](howto-linkvnet-cli.md)
> * [Vídeo – Portal do Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-linkvnet-classic.md)
> 

Neste tutorial, você aprenderá a:
> [!div class="checklist"]
> - Conectar uma rede virtual na mesma assinatura a um circuito
> - Conectar uma rede virtual em uma assinatura diferente a um circuito
> - Modificar uma conexão de rede virtual
> - Configurar o ExpressRoute FastPath

## <a name="prerequisites"></a>Pré-requisitos

* Você precisa da versão mais recente da CLI (interface de linha de comando). Para obter mais informações, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli).
* Analise os [pré-requisitos](expressroute-prerequisites.md), os [requisitos de roteamento](expressroute-routing.md) e os [fluxos de trabalho](expressroute-workflows.md) antes de começar a configuração.
* Você deve ter um circuito do ExpressRoute ativo. 
  * Siga as instruções para [criar um circuito do ExpressRoute](howto-circuit-cli.md) e para que o circuito seja habilitado pelo provedor de conectividade. 
  * Verifique se o emparelhamento privado do Azure está configurado para seu circuito. Veja o artigo [Configurar roteamento](howto-routing-cli.md) para obter instruções sobre roteamento. 
  * Verifique se o emparelhamento particular do Azure está configurado. O emparelhamento via protocolo BGP entre sua rede e a Microsoft precisa estar estabelecido para que você possa habilitar a conectividade de ponta a ponta.
  * Verifique se tem uma rede virtual e um gateway de rede virtual criados e totalmente provisionados. Siga as instruções para [Configurar um gateway de rede virtual para ExpressRoute](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md). Certifique-se de usar `--gateway-type ExpressRoute`.
* Você pode vincular até 10 redes virtuais a um circuito de ExpressRoute padrão. Todas as redes virtuais deverão estar na mesma região geopolítica ao usar um circuito de ExpressRoute padrão. 
* Uma única VNet pode ser vinculada a até 16 circuitos de ExpressRoute. Use o processo a seguir para criar um objeto de conexão para cada circuito do ExpressRoute ao qual você está se conectando. Os circuitos de ExpressRoute podem estar na mesma assinatura, assinaturas diferentes ou uma mistura de ambos.
* Se habilitar o complemento premium do ExpressRoute, você poderá vincular redes virtuais fora da região geopolítica do circuito do ExpressRoute. O complemento premium também permitirá que você conecte mais de 10 redes virtuais ao circuito do ExpressRoute, dependendo da largura de banda escolhida. Confira as [perguntas frequentes](expressroute-faqs.md) para obter mais detalhes sobre o complemento premium.

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Conectar uma rede virtual na mesma assinatura a um circuito

Você pode conectar um gateway de rede virtual a um circuito do ExpressRoute usando o exemplo. Verifique se o gateway de rede virtual foi criado e se está pronto para vinculação antes de executar o comando.

```azurecli-interactive
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Conectar uma rede virtual em uma assinatura diferente a um circuito

Você pode compartilhar um circuito do ExpressRoute entre várias assinaturas. A figura a seguir mostra um esquema simples de como funciona o compartilhamento de circuitos do ExpressRoute entre várias assinaturas.

Cada uma das nuvens menores dentro da nuvem grande é usada para representar assinaturas pertencentes a diferentes departamentos dentro de uma organização. Cada um dos departamentos dentro da organização usa a própria assinatura para implantar os serviços deles, mas podem compartilhar um circuito do ExpressRoute para se conectar de volta à sua rede local. Um único departamento (neste exemplo: TI) pode ter o circuito do ExpressRoute. Outras assinaturas dentro da organização podem usar o circuito do ExpressRoute.

> [!NOTE]
> As cobranças por conectividade e largura de banda do circuito dedicado serão aplicadas ao proprietário do circuito de ExpressRoute. Todas as redes virtuais compartilham a mesma largura de banda.
> 
> 

![Conectividade entre assinaturas](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration---circuit-owners-and-circuit-users"></a>Administração – proprietários e usuários do circuito

O 'proprietário do circuito' é um usuário avançado autorizado do recurso de circuito de ExpressRoute. O proprietário do circuito pode criar autorizações que podem ser resgatadas pelos 'usuários do circuito'. Os Usuários do Circuito são proprietários de gateways de rede virtual que não estão na mesma assinatura que o circuito do ExpressRoute. Usuários do circuito podem resgatar autorizações (uma autorização por rede virtual).

O proprietário do circuito tem a capacidade de modificar e revogar autorizações a qualquer momento. Revogar uma autorização faz com que todas as conexões de links sejam excluídas da assinatura cujo acesso foi revogado.

### <a name="circuit-owner-operations"></a>Operações do proprietário do circuito

**Criar uma autorização**

O proprietário do circuito cria uma autorização, que, por sua vez, cria uma chave de autorização que pode ser usada por um usuário do circuito para conectar os respectivos gateways de rede virtual ao circuito do ExpressRoute. Uma autorização é válida apenas para uma conexão.

O exemplo a seguir mostra como criar uma autorização:

```azurecli-interactive
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization
```

A resposta para isso conterá a chave de autorização e o status:

```output
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

```azurecli-interactive
az network express-route auth list --circuit-name MyCircuit -g ExpressRouteResourceGroup
```

**Adicionar autorizações**

O proprietário do circuito pode adicionar autorizações usando o exemplo a seguir:

```azurecli-interactive
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

**Excluir autorizações**

O proprietário do circuito pode revogar/excluir autorizações usando o exemplo a seguir:

```azurecli-interactive
az network express-route auth delete --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

### <a name="circuit-user-operations"></a>Operações do usuário do circuito

O usuário do circuito precisa da ID do par e de uma chave de autorização do proprietário do circuito. A chave de autorização é um GUID.

```azurecli-interactive
az network express-route show -n MyCircuit -g ExpressRouteResourceGroup
```

**Resgatar uma autorização de conexão**

O usuário de circuito pode executar o seguinte exemplo para resgatar uma autorização de vínculo:

```azurecli-interactive
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit --authorization-key "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**Liberar uma autorização de conexão**

É possível liberar uma autorização excluindo a conexão que vincula o circuito do ExpressRoute à rede virtual.

## <a name="modify-a-virtual-network-connection"></a>Modificar uma conexão de rede virtual
Você pode atualizar determinadas propriedades de uma conexão de rede virtual. 

**Atualizar o peso da conexão**

Sua rede virtual pode ser conectada a vários circuitos do ExpressRoute. Você pode receber o mesmo prefixo de mais de um circuito do ExpressRoute. Para escolher à qual conexão enviar o tráfego destinado a esse prefixo, você pode alterar *RoutingWeight* de uma conexão. O tráfego será enviado na conexão com o *RoutingWeight* mais alto.

```azurecli-interactive
az network vpn-connection update --name ERConnection --resource-group ExpressRouteResourceGroup --routing-weight 100
```

O intervalo de *RoutingWeight* é de 0 a 32.000. O valor padrão é 0.

## <a name="configure-expressroute-fastpath"></a>Configurar o ExpressRoute FastPath 
Você poderá habilitar o [ExpressRoute FastPath](expressroute-about-virtual-network-gateways.md) se o gateway de rede virtual for de Ultradesempenho ou ErGw3AZ. O FastPath aprimora o desempenho do caminho de dados, como pacotes por segundo e conexões por segundo entre sua rede local e sua rede virtual. 

**Configurar o FastPath em uma nova conexão**

```azurecli-interactive
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --express-route-gateway-bypass true --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```

**Como atualizar uma conexão existente para habilitar o FastPath**

```azurecli-interactive
az network vpn-connection update --name ERConnection --resource-group ExpressRouteResourceGroup --express-route-gateway-bypass true
```
## <a name="clean-up-resources"></a>Limpar recursos

Se você não precisar mais da conexão do ExpressRoute, na assinatura em que o gateway está localizado, use o comando `az network vpn-connection delete` para remover o vínculo entre o gateway e o circuito.

```azurecli-interactive
az network vpn-connection delete --name ERConnection --resource-group ExpressRouteResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a conectar uma rede virtual a um circuito na mesma assinatura e em uma assinatura diferente. Para obter mais informações sobre o gateway do ExpressRoute, confira: 

> [!div class="nextstepaction"]
> [Sobre os gateways de rede virtual do ExpressRoute](expressroute-about-virtual-network-gateways.md)
