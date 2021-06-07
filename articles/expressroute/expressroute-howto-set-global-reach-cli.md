---
title: 'Azure ExpressRoute: configurar o ExpressRoute Alcance Global: CLI'
description: Saiba como vincular circuitos do ExpressRoute juntos para fazer uma rede privada entre suas redes locais e habilitar Alcance Global usando o CLI do Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 01/07/2021
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: 55f1e4c030dc0e7c8d0011dcff0cc1c9f8fec06e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99260224"
---
# <a name="configure-expressroute-global-reach-by-using-the-azure-cli"></a>Configurar Alcance Global do ExpressRoute usando o CLI do Azure

Este artigo ajuda você a configurar o Alcance Global do Azure ExpressRoute usando a CLI do Azure. Para obter mais informações, veja [Alcance Global do ExpressRoute](expressroute-global-reach.md).
 
Antes de iniciar a configuração, conclua os requisitos a seguir:

* Instale a versão mais recente da CLI do Azure. Confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli) e [Introdução à CLI do Azure](/cli/azure/get-started-with-azure-cli).
* Entenda os [fluxos de trabalho](expressroute-workflows.md) de provisionamento de circuito do ExpressRoute.
* Certifique-se de que seus circuitos do ExpressRoute estejam no estado Provisionado.
* Certifique-se de que o emparelhamento privado do Azure esteja configurado em seus circuitos do ExpressRoute.  

### <a name="sign-in-to-your-azure-account"></a>Entre na sua conta do Azure

Para iniciar a configuração, entre na sua conta do Azure. O comando a seguir abre o navegador padrão e solicita as credenciais de logon da sua conta do Azure:  

```azurecli
az login
```

Se você tiver várias assinaturas do Azure, verifique as assinaturas para a conta:

```azurecli
az account list
```

Especifique a assinatura que você deseja usar:

```azurecli
az account set --subscription <your subscription ID>
```

### <a name="identify-your-expressroute-circuits-for-configuration"></a>Identifique seus circuitos do ExpressRoute para configuração

Você pode habilitar o ExpressRoute Alcance Global entre dois circuitos do ExpressRoute. Os circuitos precisam estar em países/regiões com suporte e criados em diferentes locais de emparelhamento. Se sua assinatura possuir ambos os circuitos, você poderá selecionar um dos circuitos para executar a configuração. No entanto, se os dois circuitos estiverem em assinaturas diferentes do Azure, você deverá criar uma chave de autorização a partir de um dos circuitos. Usando a chave de autorização gerada a partir do primeiro circuito, você pode habilitar Alcance Global no segundo circuito.

## <a name="enable-connectivity-between-your-on-premises-networks"></a>Habilitar conectividade entre suas redes locais

Ao executar o comando para habilitar a conectividade, observe os seguintes requisitos para valores de parâmetro:

* *peer-circuit* deve ser a ID de recurso completo. Por exemplo:

  > /subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/AzurePrivatePeering

* *address-prefix* deve ser uma sub-rede IPv4 "/29" (por exemplo, "10.0.0.0/29"). Usamos endereços IP nesta sub-rede para estabelecer a conectividade entre os dois circuitos do ExpressRoute. Você não pode usar endereços nessa sub-rede em suas redes virtuais do Azure ou em suas redes locais.

Execute o comando da CLI a seguir para conectar dois circuitos do ExpressRoute:

```azurecli
az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29>
```

A saída da CLI se parece com esta:

```output
{
  "addressPrefix": "<__.__.__.__/29>",
  "authorizationKey": null,
  "circuitConnectionStatus": "Connected",
  "etag": "W/\"48d682f9-c232-4151-a09f-fab7cb56369a\"",
  "expressRouteCircuitPeering": {
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit1Name>/peerings/AzurePrivatePeering",
    "resourceGroup": "<ResourceGroupName>"
  },
  "id": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit1Name>/peerings/AzurePrivatePeering/connections/<ConnectionName>",
  "name": "<ConnectionName>",
  "peerExpressRouteCircuitPeering": {
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/<Circuit2ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit2Name>/peerings/AzurePrivatePeering",
    "resourceGroup": "<Circuit2ResourceGroupName>"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "<ResourceGroupName>",
  "type": "Microsoft.Network/expressRouteCircuits/peerings/connections"
}
```

Quando esta operação for concluída, você terá conectividade entre suas redes locais nos dois lados por meio dos seus circuitos do ExpressRoute.

## <a name="enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions"></a>Habilite a conectividade entre os circuitos do ExpressRoute em diferentes assinaturas do Azure

Se os dois circuitos não estiverem na mesma assinatura do Azure, você precisará de autorização. Na configuração a seguir, você gera autorização na assinatura do circuito 2. Em seguida, você passa a chave de autorização para o circuito 1.

1. Gere uma chave de autorização:

   ```azurecli
   az network express-route auth create --circuit-name <Circuit2Name> -g <Circuit2ResourceGroupName> -n <AuthorizationName>
   ```

   A saída da CLI se parece com esta:

   ```output
   {
     "authorizationKey": "<authorizationKey>",
     "authorizationUseStatus": "Available",
     "etag": "W/\"cfd15a2f-43a1-4361-9403-6a0be00746ed\"",
     "id": "/subscriptions/<SubscriptionID>/resourceGroups/<Circuit2ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit2Name>/authorizations/<AuthorizationName>",
     "name": "<AuthorizationName>",
     "provisioningState": "Succeeded",
     "resourceGroup": "<Circuit2ResourceGroupName>",
     "type": "Microsoft.Network/expressRouteCircuits/authorizations"
   }
   ```

1. Anote a ID do recurso e a chave de autorização do circuito 2.

1. Execute o comando a seguir no circuito de 1, passando a ID do recuso e a chave de autorização do circuito 2:

   ```azurecli
   az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29> --authorization-key <authorizationKey>
   ```

Quando esta operação for concluída, você terá conectividade entre suas redes locais nos dois lados por meio dos seus circuitos do ExpressRoute.

## <a name="get-and-verify-the-configuration"></a>Obter e verificar a configuração

Use o seguinte comando para verificar a configuração no circuito em que a configuração foi realizada (circuito 1 no exemplo anterior):

```azurecli
az network express-route show -n <CircuitName> -g <ResourceGroupName>
```

Na saída de CLI, você verá *CircuitConnectionStatus*. Isso informa se a conectividade entre os dois circuitos foi estabelecida (Conectado) ou não (Desconectado). 

## <a name="disable-connectivity-between-your-on-premises-networks"></a>Desabilitar a conectividade entre suas redes locais

Para desabilitar a conectividade, execute o comando a seguir no circuito em que a configuração foi feita (circuito 1 no exemplo anterior).

```azurecli
az network express-route peering connection delete -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName>
```

Use o comando ```show``` para verificar o status.

Quando esta operação estiver concluída, você deixará de ter conectividade entre suas redes locais por meio de seus circuitos do ExpressRoute.

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre o Alcance Global do ExpressRoute](expressroute-global-reach.md)
* [Verificar a conectividade do ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Vincular um circuito do ExpressRoute a uma rede virtual](expressroute-howto-linkvnet-arm.md)
