---
title: 'Azure ExpressRoute: configurar ExpressRoute direto: CLI'
description: Saiba como usar CLI do Azure para configurar o Azure ExpressRoute Direct para se conectar diretamente à rede global da Microsoft.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/14/2020
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: aea51e56f2d96fa634b1ece2029c9ea5bf3f60fc
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98011298"
---
# <a name="configure-expressroute-direct-by-using-the-azure-cli"></a>Configurar o ExpressRoute direto usando o CLI do Azure

O ExpressRoute Direct oferece a capacidade de se conectar diretamente à rede global da Microsoft por meio de locais de emparelhamento distribuídos estrategicamente em todo o mundo. Para obter mais informações, confira [Sobre a conexão do ExpressRoute Direct](expressroute-erdirect-about.md).

## <a name="before-you-begin"></a>Antes de começar

Antes de usar o ExpressRoute Direct, você deve primeiro registrar sua assinatura. Para se inscrever, envie um e-mail para <ExpressRouteDirect@microsoft.com> com seu ID de assinatura, incluindo os seguintes detalhes:

* Cenários que você pretende para realizar com o **ExpressRoute Direct**
* Preferências de localização. Confira [Localizações de emparelhamento e parceiros do ExpressRoute](expressroute-locations-providers.md) para obter uma lista completa de todas as localizações
* Linha do tempo para implementação
* Tem mais perguntas?

## <a name="create-the-resource"></a><a name="resources"></a>Criar o recurso

1. Entre no Azure e selecione a assinatura que contém o ExpressRoute. O recurso do ExpressRoute Direct e os circuitos do ExpressRoute precisam estar na mesma assinatura. Na CLI do Azure, use os seguintes comandos:

   ```azurecli
   az login
   ```

   Verifique as assinaturas da conta: 

   ```azurecli
   az account list 
   ```

   Selecione a assinatura para a qual você deseja criar um circuito do ExpressRoute:

   ```azurecli
   az account set --subscription "<subscription ID>"
   ```

2. Registre novamente sua assinatura para Microsoft. Network para acessar as APIs expressrouteportslocation e expressrouteport

   ```azurecli
   az provider register --namespace Microsoft.Network
   ```
3. Liste todas os locais onde há suporte para o ExpressRoute Direct:
    
   ```azurecli
   az network express-route port location list
   ```

   **Saída de exemplo**
  
   ```output
   [
   {
    "address": "21715 Filigree Court, DC2, Building F, Ashburn, VA 20147",
    "availableBandwidths": [],
    "contact": "support@equinix.com",
    "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Ashburn-DC2",
    "location": null,
    "name": "Equinix-Ashburn-DC2",
    "provisioningState": "Succeeded",
    "tags": null,
    "type": "Microsoft.Network/expressRoutePortsLocations"
   },
   {
    "address": "1950 N. Stemmons Freeway, Suite 1039A, DA3, Dallas, TX 75207",
    "availableBandwidths": [],
    "contact": "support@equinix.com",
    "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Dallas-DA3",
    "location": null,
    "name": "Equinix-Dallas-DA3",
    "provisioningState": "Succeeded",
    "tags": null,
    "type": "Microsoft.Network/expressRoutePortsLocations"
   },
   {
    "address": "111 8th Avenue, New York, NY 10011",
    "availableBandwidths": [],
    "contact": "support@equinix.com",
    "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-New-York-NY5",
    "location": null,
    "name": "Equinix-New-York-NY5",
    "provisioningState": "Succeeded",
    "tags": null,
    "type": "Microsoft.Network/expressRoutePortsLocations"
   },
   {
    "address": "11 Great Oaks Blvd, SV1, San Jose, CA 95119",
    "availableBandwidths": [],
    "contact": "support@equinix.com",
    "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-San-Jose-SV1",
    "location": null,
    "name": "Equinix-San-Jose-SV1",
    "provisioningState": "Succeeded",
    "tags": null,
    "type": "Microsoft.Network/expressRoutePortsLocations"
   },
   {
    "address": "2001 Sixth Ave., Suite 350, SE2, Seattle, WA 98121",
    "availableBandwidths": [],
    "contact": "support@equinix.com",
    "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Seattle-SE2",
    "location": null,
    "name": "Equinix-Seattle-SE2",
    "provisioningState": "Succeeded",
    "tags": null,
    "type": "Microsoft.Network/expressRoutePortsLocations"
   }
   ]
   ```
4. Determine se um dos locais listados na etapa anterior tem largura de banda disponível:

   ```azurecli
   az network express-route port location show -l "Equinix-Ashburn-DC2"
   ```

   **Saída de exemplo**

   ```output
   {
   "address": "21715 Filigree Court, DC2, Building F, Ashburn, VA 20147",
   "availableBandwidths": [
    {
      "offerName": "100 Gbps",
      "valueInGbps": 100
    }
   ],
   "contact": "support@equinix.com",
   "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Ashburn-DC2",
   "location": null,
   "name": "Equinix-Ashburn-DC2",
   "provisioningState": "Succeeded",
   "tags": null,
   "type": "Microsoft.Network/expressRoutePortsLocations"
   }
   ```
5. Crie um recurso do ExpressRoute Direct com base no local que você escolheu nas etapas anteriores.

   O ExpressRoute Direct dá suporte a encapsulamento QinQ e Dot1Q. Se o QinQ for selecionado, cada circuito do ExpressRoute será atribuído dinamicamente a uma marca S e será exclusivo em todo o recurso ExpressRoute Direct. Cada marca C no circuito precisa ser exclusiva no circuito, mas não no recurso do ExpressRoute Direct.  

   Se você selecionar o encapsulamento Do1Q, você precisará gerenciar a exclusividade da marca C (VLAN) em todo o recurso ExpressRoute Direct.  

   > [!IMPORTANT]
   > O ExpressRoute Direct só pode ser um tipo de encapsulamento. Não será possível alterar o tipo de encapsulamento depois de criar o recurso do ExpressRoute Direct.
   > 
 
   ```azurecli
   az network express-route port create -n $name -g $RGName --bandwidth 100 gbps  --encapsulation QinQ | Dot1Q --peering-location $PeeringLocationName -l $AzureRegion 
   ```

   > [!NOTE]
   > Você também pode definir o atributo **Encapsulamento** para **Dot1Q**. 
   >

   **Saída de exemplo**

   ```output
   {
   "allocationDate": "Wednesday, October 17, 2018",
   "bandwidthInGbps": 100,
   "circuits": null,
   "encapsulation": "Dot1Q",
   "etag": "W/\"<etagnumber>\"",
   "etherType": "0x8100",
   "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct",
   "links": [
    {
      "adminState": "Disabled",
      "connectorType": "LC",
      "etag": "W/\"<etagnumber>\"",
      "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct/links/link1",
      "interfaceName": "HundredGigE2/2/2",
      "name": "link1",
      "patchPanelId": "PPID",
      "provisioningState": "Succeeded",
      "rackId": "RackID",
      "resourceGroup": "Contoso-Direct-rg",
      "routerName": "tst-09xgmr-cis-1",
      "type": "Microsoft.Network/expressRoutePorts/links"
    },
    {
      "adminState": "Disabled",
      "connectorType": "LC",
      "etag": "W/\"<etagnumber>\"",
      "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct/links/link2",
      "interfaceName": "HundredGigE2/2/2",
      "name": "link2",
      "patchPanelId": "PPID",
      "provisioningState": "Succeeded",
      "rackId": "RackID",
      "resourceGroup": "Contoso-Direct-rg",
      "routerName": "tst-09xgmr-cis-2",
      "type": "Microsoft.Network/expressRoutePorts/links"
    }
   ],
   "location": "westus",
   "mtu": "1500",
   "name": "Contoso-Direct",
   "peeringLocation": "Equinix-Ashburn-DC2",
   "provisionedBandwidthInGbps": 0.0,
   "provisioningState": "Succeeded",
   "resourceGroup": "Contoso-Direct-rg",
   "resourceGuid": "02ee21fe-4223-4942-a6bc-8d81daabc94f",
   "tags": null,
   "type": "Microsoft.Network/expressRoutePorts"
   }  
   ```

## <a name="change-adminstate-for-links"></a><a name="state"></a>Alterar AdminState para links

Use esse processo para conduzir um teste de camada 1. Certifique-se de que cada conexão cruzada seja corretamente corrigida em cada roteador nas portas primárias e secundárias.

1. Defina o link para **Enabled**. Repita essa etapa para definir cada link para **Habilitado**.

   Links[0] é a porta primária e Links[1] é a porta secundária.

   ```azurecli
   az network express-route port update -n Contoso-Direct -g Contoso-Direct-rg --set links[0].adminState="Enabled"
   ```
   ```azurecli
   az network express-route port update -n Contoso-Direct -g Contoso-Direct-rg --set links[1].adminState="Enabled"
   ```
   **Saída de exemplo**

   ```output
   {
   "allocationDate": "Wednesday, October 17, 2018",
   "bandwidthInGbps": 100,
   "circuits": null,
   "encapsulation": "Dot1Q",
   "etag": "W/\"<etagnumber>\"",
   "etherType": "0x8100",
   "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct",
   "links": [
    {
      "adminState": "Enabled",
      "connectorType": "LC",
      "etag": "W/\"<etagnumber>\"",
      "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct/links/link1",
      "interfaceName": "HundredGigE2/2/2",
      "name": "link1",
      "patchPanelId": "PPID",
      "provisioningState": "Succeeded",
      "rackId": "RackID",
      "resourceGroup": "Contoso-Direct-rg",
      "routerName": "tst-09xgmr-cis-1",
      "type": "Microsoft.Network/expressRoutePorts/links"
    },
    {
      "adminState": "Enabled",
      "connectorType": "LC",
      "etag": "W/\"<etagnumber>\"",
      "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct/links/link2",
      "interfaceName": "HundredGigE2/2/2",
      "name": "link2",
      "patchPanelId": "PPID",
      "provisioningState": "Succeeded",
      "rackId": "RackID",
      "resourceGroup": "Contoso-Direct-rg",
      "routerName": "tst-09xgmr-cis-2",
      "type": "Microsoft.Network/expressRoutePorts/links"
    }
   ],
   "location": "westus",
   "mtu": "1500",
   "name": "Contoso-Direct",
   "peeringLocation": "Equinix-Ashburn-DC2",
   "provisionedBandwidthInGbps": 0.0,
   "provisioningState": "Succeeded",
   "resourceGroup": "Contoso-Direct-rg",
   "resourceGuid": "<resourceGUID>",
   "tags": null,
   "type": "Microsoft.Network/expressRoutePorts"
   }
   ```

   Use o mesmo procedimento com para desabilitar as portas usando `AdminState = "Disabled"`.

## <a name="create-a-circuit"></a><a name="circuit"></a>Criar um circuito

Por padrão, você pode criar 10 circuitos na assinatura que contém o recurso ExpressRoute Direct. O Suporte da Microsoft pode aumentar o limite padrão. Você é responsável por a largura de banda provisionada e utilizada. Largura de banda provisionada é a soma da largura de banda de todos os circuitos no recurso do ExpressRoute Direct. A largura de banda utilizada é o uso físico das interfaces físicas subjacentes.

Você pode usar as larguras de bandas de circuito adicionais no ExpressRoute Direct apenas para dar suporte aos cenários estabelecidos aqui. As larguras de banda são 40 Gbps e 100 Gbps.

**SkuTier** pode ser local, Standard ou Premium.

**SkuFamily** só pode ser MeteredData. Não há suporte para ilimitado no ExpressRoute Direct.

Crie um circuito no recurso ExpressRoute Direct:

  ```azurecli
  az network express-route create --express-route-port "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct" -n "Contoso-Direct-ckt" -g "Contoso-Direct-rg" --sku-family MeteredData --sku-tier Standard --bandwidth 100 Gbps
  ```

  Outras larguras de banda incluem: 5 Gbps, 10 Gbps e 40 Gbps.

  **Saída de exemplo**

  ```output
  {
  "allowClassicOperations": false,
  "allowGlobalReach": false,
  "authorizations": [],
  "bandwidthInGbps": 100.0,
  "circuitProvisioningState": "Enabled",
  "etag": "W/\"<etagnumber>\"",
  "expressRoutePort": {
    "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct",
    "resourceGroup": "Contoso-Direct-rg"
  },
  "gatewayManagerEtag": "",
  "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRouteCircuits/ERDirect-ckt-cli",
  "location": "westus",
  "name": "ERDirect-ckt-cli",
  "peerings": [],
  "provisioningState": "Succeeded",
  "resourceGroup": "Contoso-Direct-rg",
  "serviceKey": "<serviceKey>",
  "serviceProviderNotes": null,
  "serviceProviderProperties": null,
  "serviceProviderProvisioningState": "Provisioned",
  "sku": {
    "family": "MeteredData",
    "name": "Standard_MeteredData",
    "tier": "Standard"
  },
  "stag": null,
  "tags": null,
  "type": "Microsoft.Network/expressRouteCircuits"
  }  
  ```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o ExpressRoute Direct, confira [Visão geral](expressroute-erdirect-about.md).
