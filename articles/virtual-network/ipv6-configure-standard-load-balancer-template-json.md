---
title: Implantar um aplicativo IPv6 dual stack na rede virtual do Azure – modelo do Resource Manager
titlesuffix: Azure Virtual Network
description: Este artigo mostra como implantar um aplicativo IPv6 dual stack com o Standard Load Balancer na rede virtual do Azure usando modelos de VM Azure Resource Manager.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: f98ffde9ce9b5c564e9f9c6c0b95e11f76719597
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/22/2020
ms.locfileid: "95244337"
---
# <a name="deploy-an-ipv6-dual-stack-application-in-azure-virtual-network---template"></a>Implantar um aplicativo IPv6 dual stack na rede virtual do Azure-modelo

Este artigo fornece uma lista de tarefas de configuração de IPv6 com a parte do modelo de VM Azure Resource Manager que se aplica ao. Use o modelo descrito neste artigo para implantar um aplicativo de pilha dupla (IPv4 + IPv6) usando Standard Load Balancer no Azure que inclui uma rede virtual de pilha dupla com sub-redes IPv4 e IPv6, um Standard Load Balancer com as configurações de front-end (IPv4 + IPv6) duplas, VMs com NICs que têm uma configuração de IP dupla, grupo de segurança de rede e IPs públicos. 

## <a name="required-configurations"></a>Configurações necessárias

Procure as seções de modelo no modelo para ver onde elas devem ocorrer.

### <a name="ipv6-addressspace-for-the-virtual-network"></a>IPv6 addressSpace para a rede virtual

Seção de modelo a ser adicionada:

```JSON
        "addressSpace": {
          "addressPrefixes": [
            "[variables('vnetv4AddressRange')]",
            "[variables('vnetv6AddressRange')]"    
```

### <a name="ipv6-subnet-within-the-ipv6-virtual-network-addressspace"></a>Sub-rede IPv6 dentro da rede virtual IPv6 addressSpace

Seção de modelo a ser adicionada:
```JSON
          {
            "name": "V6Subnet",
            "properties": {
              "addressPrefix": "[variables('subnetv6AddressRange')]"
            }

```

### <a name="ipv6-configuration-for-the-nic"></a>Configuração de IPv6 para a NIC

Seção de modelo a ser adicionada:
```JSON
          {
            "name": "ipconfig-v6",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
          "privateIPAddressVersion":"IPv6",
              "subnet": {
                "id": "[variables('v6-subnet-id')]"
              },
              "loadBalancerBackendAddressPools": [
                {
                  "id": "[concat(resourceId('Microsoft.Network/loadBalancers','loadBalancer'),'/backendAddressPools/LBBAP-v6')]"
                }
```

### <a name="ipv6-network-security-group-nsg-rules"></a>Regras do NSG (grupo de segurança de rede) IPv6

```JSON
          {
            "name": "default-allow-rdp",
            "properties": {
              "description": "Allow RDP",
              "protocol": "Tcp",
              "sourcePortRange": "33819-33829",
              "destinationPortRange": "5000-6000",
              "sourceAddressPrefix": "fd00:db8:deca:deed::/64",
              "destinationAddressPrefix": "fd00:db8:deca:deed::/64",
              "access": "Allow",
              "priority": 1003,
              "direction": "Inbound"
            }
```

## <a name="conditional-configuration"></a>Configuração condicional

Se você estiver usando uma solução de virtualização de rede, adicione rotas IPv6 na tabela de rotas. Caso contrário, essa configuração é opcional.

```JSON
    {
      "type": "Microsoft.Network/routeTables",
      "name": "v6route",
      "apiVersion": "[variables('ApiVersion')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "routes": [
          {
            "name": "v6route",
            "properties": {
              "addressPrefix": "fd00:db8:deca:deed::/64",
              "nextHopType": "VirtualAppliance",
              "nextHopIpAddress": "fd00:db8:ace:f00d::1"
            }
```

## <a name="optional-configuration"></a>Configuração opcional

### <a name="ipv6-internet-access-for-the-virtual-network"></a>Acesso à Internet IPv6 para a rede virtual

```JSON
{
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-public-ip-addresses"></a>Endereços IP públicos IPv6

```JSON
    {
      "apiVersion": "[variables('ApiVersion')]",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "lbpublicip-v6",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard"
      },
      "properties": {
        "publicIPAllocationMethod": "Static",
        "publicIPAddressVersion": "IPv6"
      }
```

### <a name="ipv6-front-end-for-load-balancer"></a>Front-end IPv6 para Load Balancer

```JSON
          {
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-back-end-address-pool-for-load-balancer"></a>Pool de endereços de back-end IPv6 para Load Balancer

```JSON
              "backendAddressPool": {
                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', 'loadBalancer'), '/backendAddressPools/LBBAP-v6')]"
              },
              "protocol": "Tcp",
              "frontendPort": 8080,
              "backendPort": 8080
            },
            "name": "lbrule-v6"
```

### <a name="ipv6-load-balancer-rules-to-associate-incoming-and-outgoing-ports"></a>Regras de balanceador de carga IPv6 para associar portas de entrada e saída

```JSON
          {
            "name": "ipconfig-v6",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
          "privateIPAddressVersion":"IPv6",
              "subnet": {
                "id": "[variables('v6-subnet-id')]"
              },
              "loadBalancerBackendAddressPools": [
                {
                  "id": "[concat(resourceId('Microsoft.Network/loadBalancers','loadBalancer'),'/backendAddressPools/LBBAP-v6')]"
                }
```

## <a name="sample-vm-template-json"></a>Exemplo de JSON de modelo de VM
Para implantar um aplicativo de pilha dupla IPv6 na rede virtual do Azure usando Azure Resource Manager modelo, exiba o modelo de exemplo [aqui](https://azure.microsoft.com/resources/templates/ipv6-in-vnet-stdlb/).

## <a name="next-steps"></a>Próximas etapas

Você pode encontrar detalhes sobre os preços para [endereços IP públicos](https://azure.microsoft.com/pricing/details/ip-addresses/), [largura de banda de rede](https://azure.microsoft.com/pricing/details/bandwidth/) ou [Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/).
