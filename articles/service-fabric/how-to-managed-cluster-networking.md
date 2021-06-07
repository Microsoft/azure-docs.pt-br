---
title: Definir configurações de rede para Service Fabric clusters gerenciados (versão prévia)
description: Saiba como configurar seu Service Fabric cluster gerenciado para regras NSG, acesso à porta RDP, regras de balanceamento de carga e muito mais.
ms.topic: how-to
ms.date: 03/02/2021
ms.openlocfilehash: e17251523c0720665c4c6f5b7811304eebc9923e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101744271"
---
# <a name="configure-network-settings-for-service-fabric-managed-clusters-preview"></a>Definir configurações de rede para Service Fabric clusters gerenciados (versão prévia)

Service Fabric clusters gerenciados são criados com uma configuração de rede padrão. Essa configuração consiste em regras obrigatórias para a funcionalidade de cluster essencial e algumas regras opcionais que se destinam a facilitar a configuração do cliente.

Além da configuração de rede padrão, você pode modificar as regras de rede para atender às necessidades do seu cenário.

## <a name="nsg-rules-guidance"></a>Diretrizes de regras do NSG

Esteja atento a essas considerações ao criar novas regras de NSG para o cluster gerenciado.

* Service Fabric clusters gerenciados reservam o intervalo de prioridade de regra de NSG de 0 a 999 para a funcionalidade essencial. Não é possível criar regras NSG personalizadas com um valor de prioridade inferior a 1000.
* Service Fabric clusters gerenciados reservam o intervalo de prioridade de 3001 a 4000 para criar regras NSG opcionais. Essas regras são adicionadas automaticamente para tornar as configurações rápidas e fáceis. Você pode substituir essas regras adicionando regras NSG personalizadas no intervalo de prioridade de 1000 a 3000.
* As regras de NSG personalizadas devem ter uma prioridade dentro do intervalo de 1000 a 3000.

## <a name="apply-nsg-rules"></a>Aplicar regras NSG

Com clusters de Service Fabric clássicos (não gerenciados), você deve declarar e gerenciar um recurso *Microsoft. Network/networkSecurityGroups* separado para [aplicar as regras de NSG (grupo de segurança de rede) ao cluster](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-nsg-cluster-65-node-3-nodetype). Service Fabric clusters gerenciados permitem atribuir regras NSG diretamente no recurso de cluster do seu modelo de implantação.

Use a propriedade [networkSecurityRules](/azure/templates/microsoft.servicefabric/managedclusters#managedclusterproperties-object) do seu recurso *Microsoft. perfabric/managedclusters* (versão `2021-01-01-preview` ou posterior) para atribuir regras de NSG. Por exemplo:

```json
            "apiVersion": "2021-01-01-preview",
            "type": "Microsoft.ServiceFabric/managedclusters",
            ...
            "properties": {
                ...
                "networkSecurityRules" : [
                    {
                        "name": "AllowCustomers",
                        "protocol": "*",
                        "sourcePortRange": "*",
                        "sourceAddressPrefix": "Internet",
                        "destinationAddressPrefix": "*",
                        "destinationPortRange": "33000-33499",
                        "access": "Allow",
                        "priority": 2001,
                        "direction": "Inbound" 
                    },
                    {
                        "name": "AllowARM",
                        "protocol": "*",
                        "sourcePortRange": "*",
                        "sourceAddressPrefix": "AzureResourceManager",
                        "destinationAddressPrefix": "*",
                        "destinationPortRange": "33500-33699",
                        "access": "Allow",
                        "priority": 2002,
                        "direction": "Inbound" 
                    },
                    {
                        "name": "DenyCustomers",
                        "protocol": "*",
                        "sourcePortRange": "*",
                        "sourceAddressPrefix": "Internet",
                        "destinationAddressPrefix": "*",
                        "destinationPortRange": "33700-33799",
                        "access": "Deny",
                        "priority": 2003,
                        "direction": "Outbound"
                    },
                    {
                        "name": "DenyRDP",
                        "protocol": "*",
                        "sourcePortRange": "*",
                        "sourceAddressPrefix": "*",
                        "destinationAddressPrefix": "VirtualNetwork",
                        "destinationPortRange": "3389",
                        "access": "Deny",
                        "priority": 2004,
                        "direction": "Inbound",
                        "description": "Override for optional SFMC_AllowRdpPort rule. This is required in tests to avoid Sev2 incident for security policy violation."
                    }
                ],
                "fabricSettings": [
                ...
                ]
            }
```

## <a name="rdp-ports"></a>Portas RDP

Service Fabric clusters gerenciados não permitem acesso às portas RDP por padrão. Você pode abrir portas RDP na Internet definindo a seguinte propriedade em um Service Fabric recurso de cluster gerenciado.

```json
"allowRDPAccess": true 
```

Quando a propriedade allowRDPAccess for definida como true, a seguinte regra de NSG será adicionada à implantação do cluster.  

```json
{
    "name": "SFMC_AllowRdpPort", 
    "type": "Microsoft.Network/networkSecurityGroups/securityRules",
    "properties": {
        "description": "Optional rule to open RDP ports.",
        "protocol": "tcp",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Allow",
        "priority": 3002,
        "direction": "Inbound",
        "sourcePortRanges": [],
        "destinationPortRange": "3389"
    }
}
```

## <a name="clientconnection-and-httpgatewayconnection-ports"></a>Portas ClientConnection e HttpGatewayConnection

### <a name="nsg-rule-sfmc_allowservicefabricgatewaytosfrp"></a>Regra NSG: SFMC_AllowServiceFabricGatewayToSFRP

Uma regra NSG padrão é adicionada para permitir que o provedor de recursos de Service Fabric acesse o clientConnectionPort e o httpGatewayConnectionPort do cluster. Essa regra permite o acesso às portas por meio da marca de serviço ' Service Fabric '.

>[!NOTE]
>Essa regra é sempre adicionada e não pode ser substituída.

```json
{ 
    "name": "SFMC_AllowServiceFabricGatewayToSFRP", 
    "type": "Microsoft.Network/networkSecurityGroups/securityRules", 
    "properties": { 
        "description": "This is required rule to allow SFRP to connect to the cluster. This rule cannot be overridden.", 
        "protocol": "TCP", 
        "sourcePortRange": "*", 
        "sourceAddressPrefix": "ServiceFabric", 
        "destinationAddressPrefix": "VirtualNetwork", 
        "access": "Allow", 
        "priority": 500, 
        "direction": "Inbound", 
        "sourcePortRanges": [], 
        "destinationPortRanges": [ 
            "19000", 
            "19080" 
        ] 
    } 
}
```

### <a name="nsg-rule-sfmc_allowservicefabricgatewayports"></a>Regra NSG: SFMC_AllowServiceFabricGatewayPorts

Essa é uma regra opcional de NSG para permitir o acesso ao clientConnectionPort e httpGatewayPort da Internet. Essa regra permite que os clientes acessem o SFX, conectem-se ao cluster usando o PowerShell e usem Service Fabric pontos de extremidade de API de cluster de fora do. 

>[!NOTE]
>Essa regra não será adicionada se houver uma regra personalizada com o mesmo acesso, direção e valores de protocolo para a mesma porta. Você pode substituir essa regra por regras NSG personalizadas. 

```json
{ 
    "name": "SFMC_AllowServiceFabricGatewayPorts", 
    "type": "Microsoft.Network/networkSecurityGroups/securityRules", 
    "properties": { 
        "description": "Optional rule to open SF cluster gateway ports. To override add a custom NSG rule for gateway ports in priority range 1000-3000.", 
        "protocol": "tcp", 
        "sourcePortRange": "*", 
        "sourceAddressPrefix": "*", 
        "destinationAddressPrefix": "VirtualNetwork", 
        "access": "Allow", 
        "priority": 3001, 
        "direction": "Inbound", 
        "sourcePortRanges": [], 
        "destinationPortRanges": [ 
            "19000", 
            "19080" 
        ] 
    } 
}
```

## <a name="load-balancer-ports"></a>Portas do balanceador de carga

Service Fabric clusters gerenciados, crie uma regra NSG no intervalo de prioridade padrão para todas as portas do balanceador de carga (LB) configuradas na seção "loadBalancingRules" em propriedades *ManagedCluster* . Essa regra abre portas LB para tráfego de entrada da Internet.  

>[!NOTE]
>Essa regra é adicionada no intervalo de prioridade opcional e pode ser substituída pela adição de regras NSG personalizadas.

```json
{
    "name": "SFMC_AllowLoadBalancedPorts",
    "type": "Microsoft.Network/networkSecurityGroups/securityRules",
    "properties": {
        "description": "Optional rule to open LB ports",
        "protocol": "*", 
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Allow",
        "priority": 3003,
        "direction": "Inbound",
        "sourcePortRanges": [],
        "destinationPortRanges": [
        "80", "8080", "4343"
        ]
    }
}
```

## <a name="load-balancer-probes"></a>Investigações do balanceador de carga

Service Fabric clusters gerenciados criam automaticamente investigações do balanceador de carga para portas do gateway de malha, bem como todas as portas configuradas na seção "loadBalancingRules" das propriedades do cluster gerenciado.

```json
{ 
  "value": [ 
    { 
        "name": "FabricTcpGateway", 
        "properties": { 
            "provisioningState": "Succeeded", 
            "protocol": "Tcp", 
            "port": 19000, 
            "intervalInSeconds": 5, 
            "numberOfProbes": 2, 
            "loadBalancingRules": [ 
                { 
                    "id": "<>"
                } 
            ] 
        }, 
        "type": "Microsoft.Network/loadBalancers/probes" 
    }, 
    { 
        "name": "FabricHttpGateway", 
        "properties": { 
            "provisioningState": "Succeeded", 
            "protocol": "Tcp", 
            "port": 19080, 
            "intervalInSeconds": 5, 
            "numberOfProbes": 2, 
            "loadBalancingRules": [ 
                { 
                    "id": "<>" 
                } 
            ]
        },
        "type": "Microsoft.Network/loadBalancers/probes" 
    },
    {
        "name": "probe1_tcp_8080", 
        "properties": { 
            "provisioningState": "Succeeded", 
            "protocol": "Tcp", 
            "port": 8080, 
            "intervalInSeconds": 5, 
            "numberOfProbes": 2, 
            "loadBalancingRules": [ 
            { 
                "id": "<>" 
            } 
        ] 
      }, 
      "type": "Microsoft.Network/loadBalancers/probes" 
    } 
  ] 
} 
```

## <a name="next-steps"></a>Próximas etapas

[Service Fabric opções de configuração de cluster gerenciado](how-to-managed-cluster-configuration.md)

[Visão geral de clusters gerenciados Service Fabric](overview-managed-cluster.md)
