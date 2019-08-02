---
title: Implantar um aplicativo de pilha dupla IPv6 com Load Balancer básica na rede virtual do Azure – modelo do Resource Manager (versão prévia)
titlesuffix: Azure Virtual Network
description: Este artigo mostra como implantar um aplicativo IPv6 dual stack na rede virtual do Azure usando Azure Resource Manager modelos de VM.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 06/26/2019
ms.author: kumud
ms.openlocfilehash: e97a5a478871d94e074b59558a11df1ec752b0f9
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249854"
---
# <a name="deploy-an-ipv6-dual-stack-application-with-basic-load-balancer-in-azure---template-preview"></a>Implantar um aplicativo de pilha dupla IPv6 com Load Balancer básica no Azure – modelo (versão prévia)

Este artigo fornece uma lista de tarefas de configuração de IPv6 com a parte do modelo de VM Azure Resource Manager que se aplica ao. Use o modelo descrito neste artigo para implantar um aplicativo de pilha dupla (IPv4 + IPv6) no Azure que inclui uma rede virtual de pilha dupla com sub-redes IPv4 e IPv6, um balanceador de carga com configurações de front-end (IPv4 + IPv6) duplas, VMs com NICs com um IP duplo configuração, grupo de segurança de rede e IPs públicos. 

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
              "sourceAddressPrefix": "ace:cab:deca:deed::/64",
              "destinationAddressPrefix": "cab:ace:deca:deed::/64",
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
              "addressPrefix": "ace:cab:deca:deed::/64",
              "nextHopType": "VirtualAppliance",
              "nextHopIpAddress": "deca:cab:ace:f00d::1"
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
      "properties": {
        "publicIPAllocationMethod": "Dynamic",
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
Para implantar um aplicativo de pilha dupla IPv6 na rede virtual do Azure usando Azure Resource Manager modelo, exiba o modelo de exemplo [aqui](https://azure.microsoft.com/resources/templates/ipv6-in-vnet/).

## <a name="next-steps"></a>Próximas etapas

Você pode encontrar detalhes sobre os preços de [endereços IP públicos](https://azure.microsoft.com/pricing/details/ip-addresses/), [largura de banda de rede](https://azure.microsoft.com/pricing/details/bandwidth/)ou [Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/).
