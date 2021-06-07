---
title: Implantar tipos de nó somente sem estado em um Cluster Service Fabric
description: Saiba como criar e implantar tipos de nó sem monitoração de estado no cluster do Azure Service Fabric.
author: peterpogorski
ms.topic: conceptual
ms.date: 09/25/2020
ms.author: pepogors
ms.openlocfilehash: eb19005019a6e4e878f6b0bd6a145048d4a2804c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103563769"
---
# <a name="deploy-an-azure-service-fabric-cluster-with-stateless-only-node-types-preview"></a>Implantar um cluster de Service Fabric do Azure com tipos de nó somente sem estado (versão prévia)
Service Fabric tipos de nó vêm com pressuposição inerente que, em algum momento, os serviços com estado podem ser colocados em nós. Os tipos de nó sem monitoração de estado relaxar essa suposição para um tipo de nó, permitindo que o tipo de nó use outros recursos, como operações de expansão mais rápidas, suporte para atualizações automáticas do so na durabilidade de bronze e dimensionamento para mais de 100 nós em um único conjunto de dimensionamento de máquinas virtuais.

* Os tipos de nó primário não podem ser configurados para serem sem estado
* Os tipos de nó sem monitoração de Estado têm suporte apenas com níveis de durabilidade bronze
* Os tipos de nó sem monitoração de Estado têm suporte apenas no Service Fabric Runtime versão 7.1.409 ou superior.


Os modelos de exemplo estão disponíveis: [Service Fabric modelo de tipos de nó sem monitoração de estado](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="enabling-stateless-node-types-in-service-fabric-cluster"></a>Habilitando tipos de nó sem monitoração de estado no Cluster Service Fabric
Para definir um ou mais tipos de nó como sem estado em um recurso de cluster, defina a propriedade **Isstateless** como "true". Ao implantar um cluster de Service Fabric com tipos de nó sem monitoração de estado, lembre-se de ter pelo menos um tipo de nó primário no recurso de cluster.

* O recurso de Cluster Service Fabric apiVersion deve ser "2020-12-01-Preview" ou superior.

```json
{
    "nodeTypes": [
    {
        "name": "[parameters('vmNodeType0Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt0applicationEndPort')]",
            "startPort": "[parameters('nt0applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt0fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt0ephemeralEndPort')]",
            "startPort": "[parameters('nt0ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt0fabricHttpGatewayPort')]",
        "isPrimary": true,
        "isStateless": false,
        "vmInstanceCount": "[parameters('nt0InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType1Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt1applicationEndPort')]",
            "startPort": "[parameters('nt1applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt1fabricTcpGatewayPort')]",
        "durabilityLevel": "Bronze",
        "ephemeralPorts": {
            "endPort": "[parameters('nt1ephemeralEndPort')]",
            "startPort": "[parameters('nt1ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt1fabricHttpGatewayPort')]",
        "isPrimary": false,
        "isStateless": true,
        "vmInstanceCount": "[parameters('nt1InstanceCount')]"
    }    
    ],
}
```

## <a name="configuring-virtual-machine-scale-set-for-stateless-node-types"></a>Configurando conjunto de dimensionamento de máquinas virtuais para tipos de nó sem estado
Para habilitar tipos de nó sem monitoração de estado, você deve configurar o recurso de conjunto de dimensionamento de máquinas virtuais subjacente da seguinte maneira:

* A propriedade Value  **singlePlacementGroup** , que deve ser definida como **false** se você precisar dimensionar para mais de 100 VMS.
* O **modo** **upgradePolicy** do conjunto de dimensionamento deve ser definido como **sem interrupção**.
* O modo de atualização sem interrupção requer a extensão de integridade do aplicativo ou investigações de integridade configuradas. Configure a investigação de integridade com a configuração padrão para tipos de nó sem monitoração de estado, conforme sugerido abaixo. Depois que os aplicativos são implantados no tipo de nó, as portas de extensão de integridade/investigação de integridade podem ser alteradas para monitorar a integridade do aplicativo.

>[!NOTE]
> É necessário que a contagem de domínios de falha da plataforma seja atualizada para 5 quando um tipo de nó sem estado for apoiado por um conjunto de dimensionamento de máquinas virtuais que esteja abrangendo várias zonas. Consulte este [modelo](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/15-VM-2-NodeTypes-Windows-Stateless-CrossAZ-Secure) para obter mais detalhes.
> 
> **platformFaultDomainCount: 5**
```json
{
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType1Name')]",
    "location": "[parameters('computeLocation')]",
    "properties": {
        "overprovision": "[variables('overProvision')]",
        "upgradePolicy": {
          "mode": "Rolling",
          "automaticOSUpgradePolicy": {
            "enableAutomaticOSUpgrade": true
          }
        }
    }
    "virtualMachineProfile": {
    "extensionProfile": {
    "extensions": [
    {
    "name": "[concat(parameters('vmNodeType1Name'),'_ServiceFabricNode')]",
    "properties": {
        "type": "ServiceFabricNode",
        "autoUpgradeMinorVersion": false,
        "publisher": "Microsoft.Azure.ServiceFabric",
        "settings": {
            "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
            "nodeTypeRef": "[parameters('vmNodeType1Name')]",
            "dataPath": "D:\\\\SvcFab",
            "durabilityLevel": "Bronze",
            "certificate": {
                "thumbprint": "[parameters('certificateThumbprint')]",
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
            "systemLogUploadSettings": {
                "Enabled": true
            },
        },
        "typeHandlerVersion": "1.0"
    }
    },
    {
        "type": "extensions",
        "name": "HealthExtension",
        "properties": {
            "publisher": "Microsoft.ManagedServices",
            "type": "ApplicationHealthWindows",
            "autoUpgradeMinorVersion": true,
            "typeHandlerVersion": "1.0",
            "settings": {
            "protocol": "tcp",
            "port": "19000"
            }
            }
        },
    ]
}
```

## <a name="networking-requirements"></a>Requisitos de rede
### <a name="public-ip-and-load-balancer-resource"></a>IP público e recurso de Load Balancer
Para habilitar o dimensionamento para mais de 100 VMs em um recurso de conjunto de dimensionamento de máquinas virtuais, o balanceador de carga e o recurso de IP referenciado por esse conjunto de dimensionamento de máquinas virtuais devem estar usando um SKU *Standard* . Criar um balanceador de carga ou um recurso de IP sem a propriedade SKU criará um SKU básico, que não oferece suporte a dimensionamento para mais de 100 VMs. Um balanceador de carga SKU padrão bloqueará todo o tráfego de fora por padrão; para permitir o tráfego externo, um NSG deve ser implantado na sub-rede.

```json
{
    "apiVersion": "2018-11-01",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat('LB','-', parameters('clusterName')]",
    "location": "[parameters('computeLocation')]",
    "sku": {
        "name": "Standard"
    }
}
{
    "apiVersion": "2018-11-01",
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB','-', parameters('clusterName')]", 
    "location": "[parameters('computeLocation')]",
    "dependsOn": [
        "[concat('Microsoft.Network/networkSecurityGroups/', concat('nsg', parameters('subnet0Name')))]"
    ],
    "properties": {
        "addressSpace": {
            "addressPrefixes": [
                "[parameters('addressPrefix')]"
            ]
        },
        "subnets": [
        {
            "name": "[parameters('subnet0Name')]",
            "properties": {
                "addressPrefix": "[parameters('subnet0Prefix')]",
                "networkSecurityGroup": {
                "id": "[resourceId('Microsoft.Network/networkSecurityGroups', concat('nsg', parameters('subnet0Name')))]"
              }
            }
          }
        ]
    },
    "sku": {
        "name": "Standard"
    }
}
```

>[!NOTE]
> Não é possível fazer uma alteração in-loco do SKU nos recursos de IP público e do balanceador de carga. Se você estiver migrando de recursos existentes que têm uma SKU básica, consulte a seção migração deste artigo.

### <a name="virtual-machine-scale-set-nat-rules"></a>Regras NAT do conjunto de dimensionamento de máquinas virtuais
As regras NAT de entrada do balanceador de carga devem corresponder aos pools NAT do conjunto de dimensionamento de máquinas virtuais. Cada conjunto de dimensionamento de máquinas virtuais deve ter um pool de NAT de entrada exclusivo.

```json
{
"inboundNatPools": [
    {
        "name": "LoadBalancerBEAddressNatPool0",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "50999",
            "frontendPortRangeStart": "50000",
            "protocol": "tcp"
        }
    },
    {
        "name": "LoadBalancerBEAddressNatPool1",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "51999",
            "frontendPortRangeStart": "51000",
            "protocol": "tcp"
        }
    },
    {
        "name": "LoadBalancerBEAddressNatPool2",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "52999",
            "frontendPortRangeStart": "52000",
            "protocol": "tcp"
        }
    }
    ]
}
```

### <a name="standard-sku-load-balancer-outbound-rules"></a>Standard SKU Load Balancer regras de saída
Standard Load Balancer e IP público Standard introduzem novas capacidades e comportamentos diferentes para a conectividade de saída quando comparados ao uso de SKUs básicos. Se quiser ter conectividade de saída ao trabalhar com SKUs Standard, você precisará defini-la explicitamente com endereços IP públicos Standard ou com o Load Balancer Standard público. Para obter mais informações, consulte [conexões de saída](../load-balancer/load-balancer-outbound-connections.md) e [Standard Load Balancer do Azure](../load-balancer/load-balancer-overview.md).

>[!NOTE]
> O modelo padrão faz referência a um NSG que permite todo o tráfego de saída por padrão. O tráfego de entrada é limitado às portas necessárias para operações de gerenciamento de Service Fabric. As regras de NSG podem ser modificadas para atender às suas necessidades.

>[!NOTE]
> Qualquer Service Fabric cluster que faz uso de um SLB SKU Standard precisa garantir que cada tipo de nó tenha uma regra que permita o tráfego de saída na porta 443. Isso é necessário para concluir a configuração do cluster e qualquer implantação sem essa regra falhará.



### <a name="migrate-to-using-stateless-node-types-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip"></a>Migrar para usar tipos de nó sem monitoração de estado de um cluster usando um Load Balancer de SKU básico e um IP de SKU básico
Para todos os cenários de migração, um novo tipo de nó somente sem estado precisa ser adicionado. O tipo de nó existente não pode ser migrado para ser somente sem estado.

Para migrar um cluster, que estava usando um Load Balancer e IP com um SKU básico, primeiro você deve criar um recurso de Load Balancer e IP totalmente novo usando o SKU Standard. Não é possível atualizar esses recursos in-loco.

O novo LB e o IP devem ser referenciados nos novos tipos de nó sem estado que você gostaria de usar. No exemplo acima, um novo recurso de conjunto de dimensionamento de máquinas virtuais é adicionado para ser usado para tipos de nó sem monitoração de estado. Esses conjuntos de dimensionamento de máquinas virtuais referenciam o LB e o IP recém-criados e são marcados como tipos de nó sem monitoração de estado no recurso de Cluster Service Fabric.

Para começar, será necessário adicionar os novos recursos ao modelo do Resource Manager existente. Esses recursos incluem:
* Um recurso de IP público usando SKU padrão.
* Um recurso Load Balancer usando o SKU Standard.
* Um NSG referenciado pela sub-rede na qual você implanta seus conjuntos de dimensionamento de máquinas virtuais.

Depois que os recursos terminarem de implantar, você poderá começar a desabilitar os nós no tipo de nó que deseja remover do cluster original.

>[!NOTE]
> Ao usar o dimensionamento automático com NodeTypes sem estado com durabilidade de bronze, após a operação reduzir verticalmente, o estado do nó não é limpo automaticamente. Para limpar o Nodestate de nós inferiores durante o dimensionamento automático, é recomendável usar [Service Fabric auxiliar de dimensionamento automático](https://github.com/Azure/service-fabric-autoscale-helper) .

## <a name="next-steps"></a>Próximas etapas 
* [Reliable Services](service-fabric-reliable-services-introduction.md)
* [Tipos de nó e conjuntos de dimensionamento de máquinas virtuais](service-fabric-cluster-nodetypes.md)

