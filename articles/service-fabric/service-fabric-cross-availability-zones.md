---
title: Implantar um cluster entre Zonas de Disponibilidade
description: Saiba como criar um cluster de Service Fabric do Azure em Zonas de Disponibilidade.
author: peterpogorski
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: a49fd6f97a9130fa0369d2a36cdc38e59613afc1
ms.sourcegitcommit: 44edde1ae2ff6c157432eee85829e28740c6950d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/25/2021
ms.locfileid: "105544376"
---
# <a name="deploy-an-azure-service-fabric-cluster-across-availability-zones"></a>Implantar um cluster de Service Fabric do Azure entre Zonas de Disponibilidade
Zonas de Disponibilidade no Azure é uma oferta de alta disponibilidade que protege seus aplicativos e dados de falhas do datacenter. Uma zona de disponibilidade é um local físico exclusivo equipado com energia, resfriamento e rede independentes em uma região do Azure.

O Service Fabric dá suporte a clusters que se estendem por Zonas de Disponibilidade implantando tipos de nós que são fixados em zonas específicas. Isso garantirá a alta disponibilidade de seus aplicativos. Zonas de Disponibilidade do Azure só estão disponíveis em regiões selecionadas. Para obter mais informações, consulte [zonas de disponibilidade do Azure visão geral](../availability-zones/az-overview.md).

Os modelos de exemplo estão disponíveis: [Service Fabric modelo de zona de disponibilidade cruzada](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="recommended-topology-for-primary-node-type-of-azure-service-fabric-clusters-spanning-across-availability-zones"></a>Topologia recomendada para o tipo de nó primário dos clusters de Service Fabric do Azure que abrangem em Zonas de Disponibilidade
Um Cluster Service Fabric distribuído entre Zonas de Disponibilidade garante a alta disponibilidade do estado do cluster. Para abranger um Cluster Service Fabric entre zonas, você deve criar um tipo de nó primário em cada zona de disponibilidade com suporte na região. Isso distribuirá os nós de semente uniformemente em cada um dos tipos de nó primários.

A topologia recomendada para o tipo de nó primário requer os recursos descritos abaixo:

* O nível de confiabilidade do cluster definido como platina.
* Três tipos de nó marcados como primários.
    * Cada tipo de nó deve ser mapeado para seu próprio conjunto de dimensionamento de máquinas virtuais localizado em diferentes zonas.
    * Cada conjunto de dimensionamento de máquinas virtuais deve ter pelo menos cinco nós (durabilidade prateada).
* Um único recurso de IP público usando SKU padrão.
* Um único recurso de Load Balancer usando SKU padrão.
* Um NSG referenciado pela sub-rede na qual você implanta seus conjuntos de dimensionamento de máquinas virtuais.

>[!NOTE]
> A propriedade de grupo de posicionamento único do conjunto de dimensionamento de máquinas virtuais deve ser definida como true, pois Service Fabric não dá suporte a um único conjunto de dimensionamento de máquinas virtuais que abrange zonas.

Diagrama que mostra o diagrama da arquitetura de zona de disponibilidade do Azure Service Fabric ![ que mostra a arquitetura de zona de disponibilidade do azure Service Fabric.][sf-architecture]

Lista de nós de exemplo que ilustra os formatos FD/UD em um conjunto de dimensionamento de máquinas virtuais que abrange zonas

 ![Lista de nós de exemplo que ilustra os formatos FD/UD em um conjunto de dimensionamento de máquinas virtuais que abrange zonas.][sf-multi-az-nodes]

**Distribuição de réplicas de serviço entre zonas**: quando um serviço é implantado em NodeTypes que são zonas de abrangência, as réplicas são colocadas para garantir que elas se espalhem em zonas separadas. Isso é garantido porque os domínios de falha nos nós presentes em cada um desses nodeTypes são configurados com as informações de zona (ou seja, FD = FD:/na zona 1/1 etc..). Por exemplo: para 5 réplicas ou instâncias de um serviço, a distribuição será 2-2-1 e o tempo de execução tentará garantir a distribuição igual entre AZs.

**Configuração da réplica de serviço do usuário**: os serviços de usuário com estado implantados nos NodeTypes da zona de disponibilidade cruzada devem ser configurados com esta configuração: contagem de réplicas com destino = 9, mín = 5. Essa configuração ajudará o serviço a estar funcionando mesmo quando uma zona ficar inativa, pois 6 réplicas ainda estarão ativas nas outras duas zonas. Uma atualização de aplicativo em tal cenário também passará por ele.

**ReliabilityLevel do cluster**: define o número de nós de semente no cluster e também o tamanho da réplica dos serviços do sistema. Como uma configuração de zona de disponibilidade cruzada tem um número maior de nós, que são distribuídos entre zonas para habilitar a resiliência de zona, um maior valor de confiabilidade garantirá que o nó mais nós de semente e réplicas de serviço do sistema estejam presentes e sejam distribuídos uniformemente entre as zonas, para que, no caso de uma falha de zona, o cluster e os serviços do sistema permaneçam "ReliabilityLevel = platina" garantirá que existam 9 nós de semente distribuídos entre zonas no cluster com 3 sementes em cada zona, portanto, isso é recomendado para a configuração da zona de disponibilidade cruzada.

**Cenário de zona inativo**: quando uma zona ficar inativa, todos os nós nessa zona aparecerão como inativos. As réplicas de serviço nesses nós também estarão inativas. Como há réplicas nas outras zonas, o serviço continua a ser responsivo com réplicas primárias que fazem failover para as zonas que estão funcionando. Os serviços aparecerão no estado de aviso, pois a contagem de réplica de destino ainda não foi obtida e, como a contagem de VM ainda é mais do que o tamanho mínimo da réplica de destino. Subsequentemente, Service Fabric balanceador de carga abrirá réplicas nas zonas de trabalho para corresponder à contagem de réplica de destino configurada. Neste ponto, os serviços aparecerão íntegros. Quando a zona que estava inoperante faz backup, o balanceamento de carga distribui novamente todas as réplicas de serviço uniformemente em todas as zonas.

## <a name="networking-requirements"></a>Requisitos de rede
### <a name="public-ip-and-load-balancer-resource"></a>IP público e recurso de Load Balancer
Para habilitar a propriedade Zones em um recurso de conjunto de dimensionamento de máquinas virtuais, o balanceador de carga e o recurso de IP referenciados por esse conjunto de dimensionamento de máquinas virtuais devem estar usando um SKU *padrão* . Criar um balanceador de carga ou um recurso de IP sem a propriedade SKU criará um SKU básico, que não oferece suporte a Zonas de Disponibilidade. Um balanceador de carga SKU padrão bloqueará todo o tráfego de fora por padrão; para permitir o tráfego externo, um NSG deve ser implantado na sub-rede.

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
```

```json
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


### <a name="enabling-zones-on-a-virtual-machine-scale-set"></a>Habilitando zonas em um conjunto de dimensionamento de máquinas virtuais
Para habilitar uma zona, em um conjunto de dimensionamento de máquinas virtuais, você deve incluir os três valores a seguir no recurso do conjunto de dimensionamento de máquinas virtuais.

* O primeiro valor é a propriedade **Zones** , que especifica em qual zona de disponibilidade o conjunto de dimensionamento de máquinas virtuais será implantado.
* O segundo valor é a propriedade "singlePlacementGroup", que deve ser definida como true.
* O terceiro valor é a propriedade "faultDomainOverride" na extensão do conjunto de dimensionamento de máquinas virtuais Service Fabric. O valor dessa propriedade deve incluir somente a zona na qual esse conjunto de dimensionamento de máquinas virtuais será colocado. Exemplo: "faultDomainOverride": "AZ1" todos os recursos do conjunto de dimensionamento de máquinas virtuais devem ser colocados na mesma região porque os clusters do Azure Service Fabric não têm suporte entre regiões.

```json
{
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType1Name')]",
    "location": "[parameters('computeLocation')]",
    "zones": ["1"],
    "properties": {
        "singlePlacementGroup": "true",
    },
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
            "durabilityLevel": "Silver",
            "certificate": {
                "thumbprint": "[parameters('certificateThumbprint')]",
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
            "systemLogUploadSettings": {
                "Enabled": true
            },
            "faultDomainOverride": "az1"
        },
        "typeHandlerVersion": "1.0"
    }
}
```

### <a name="enabling-multiple-primary-node-types-in-the-service-fabric-cluster-resource"></a>Habilitando vários tipos de nó primários no recurso de Cluster Service Fabric
Para definir um ou mais tipos de nó como primário em um recurso de cluster, defina a propriedade "IsPrimary" como "true". Ao implantar um Cluster Service Fabric em Zonas de Disponibilidade, você deve ter três tipos de nó em zonas distintas.

```json
{
    "reliabilityLevel": "Platinum",
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
        "vmInstanceCount": "[parameters('nt0InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType1Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt1applicationEndPort')]",
            "startPort": "[parameters('nt1applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt1fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt1ephemeralEndPort')]",
            "startPort": "[parameters('nt1ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt1fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt1InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType2Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt2applicationEndPort')]",
            "startPort": "[parameters('nt2applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt2fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt2ephemeralEndPort')]",
            "startPort": "[parameters('nt2ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt2fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt2InstanceCount')]"
    }
    ],
}
```

## <a name="migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip"></a>Migrar para o uso de Zonas de Disponibilidade de um cluster usando um Load Balancer de SKU básico e um IP de SKU básico
Para migrar um cluster, que estava usando um Load Balancer e IP com um SKU básico, primeiro você deve criar um recurso de Load Balancer e IP totalmente novo usando o SKU Standard. Não é possível atualizar esses recursos in-loco.

O novo LB e o IP devem ser referenciados nos novos tipos de nó de zona de disponibilidade cruzada que você gostaria de usar. No exemplo acima, três novos recursos do conjunto de dimensionamento de máquinas virtuais foram adicionados nas zonas 1, 2 e 3. Esses conjuntos de dimensionamento de máquinas virtuais referenciam o LB e o IP recém-criados e são marcados como tipos de nó primários no recurso de Cluster Service Fabric.

Para começar, será necessário adicionar os novos recursos ao modelo do Resource Manager existente. Esses recursos incluem:
* Um recurso de IP público usando SKU padrão.
* Um recurso Load Balancer usando o SKU Standard.
* Um NSG referenciado pela sub-rede na qual você implanta seus conjuntos de dimensionamento de máquinas virtuais.
* Três tipos de nó marcados como primários.
    * Cada tipo de nó deve ser mapeado para seu próprio conjunto de dimensionamento de máquinas virtuais localizado em diferentes zonas.
    * Cada conjunto de dimensionamento de máquinas virtuais deve ter pelo menos cinco nós (durabilidade prateada).

Um exemplo desses recursos pode ser encontrado no modelo de [exemplo](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/10-VM-Ubuntu-2-NodeType-Secure).

```powershell
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $Template `
    -TemplateParameterFile $Parameters
```

Depois que os recursos terminarem de implantar, você poderá começar a desabilitar os nós no tipo de nó primário do cluster original. À medida que os nós estiverem desabilitados, os serviços do sistema serão migrados para o novo tipo de nó primário que foi implantado na etapa anterior.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint $ClusterName `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My 

Write-Host "Connected to cluster"

$nodeNames = @("_nt0_0", "_nt0_1", "_nt0_2", "_nt0_3", "_nt0_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames) {
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}
```

Depois que os nós estiverem todos desabilitados, os serviços do sistema serão executados no tipo de nó primário, que é distribuído entre as zonas. Em seguida, você pode remover os nós desabilitados do cluster. Depois que os nós forem removidos, você poderá remover o IP original, Load Balancer e os recursos do conjunto de dimensionamento de máquinas virtuais.

```powershell
foreach($name in $nodeNames){
    # Remove the node from the cluster
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}

$scaleSetName="nt0"
Remove-AzureRmVmss -ResourceGroupName $groupname -VMScaleSetName $scaleSetName -Force

$lbname="LB-cluster-nt0"
$oldPublicIpName="LBIP-cluster-0"
$newPublicIpName="LBIP-cluster-1"

Remove-AzureRmLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force
Remove-AzureRmPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force
```

Em seguida, você deve remover as referências a esses recursos do modelo do Resource Manager que você implantou.

A etapa final envolverá a atualização do nome DNS e do IP público.

```powershell
$oldprimaryPublicIP = Get-AzureRmPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $groupname
$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel
$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

Remove-AzureRmLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force
Remove-AzureRmPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force

$PublicIP = Get-AzureRmPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $groupname
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzureRmPublicIpAddress -PublicIpAddress $PublicIP

```

## <a name="preview-enable-multiple-availability-zones-in-single-virtual-machine-scale-set"></a>Apresentação Habilitar várias zonas de disponibilidade em um único conjunto de dimensionamento de máquinas virtuais

A solução mencionada anteriormente usa um nodeType por AZ. A solução a seguir permitirá que os usuários implantem 3 AZ no mesmo nodeType.

**Como esse recurso está atualmente em versão prévia, ele não tem suporte para cenários de produção no momento.**

O modelo de exemplo completo está presente [aqui](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/15-VM-Windows-Multiple-AZ-Secure).

![Arquitetura de zona de disponibilidade do Azure Service Fabric][sf-multi-az-arch]

### <a name="configuring-zones-on-a-virtual-machine-scale-set"></a>Configurando zonas em um conjunto de dimensionamento de máquinas virtuais
Para habilitar zonas em um conjunto de dimensionamento de máquinas virtuais, você deve incluir os três valores a seguir no recurso do conjunto de dimensionamento de máquinas virtuais.

* O primeiro valor é a propriedade **Zones** , que especifica o zonas de disponibilidade presente no conjunto de dimensionamento de máquinas virtuais.
* O segundo valor é a propriedade "singlePlacementGroup", que deve ser definida como true. **O conjunto de dimensionamento distribuído entre 3 AZ pode ser dimensionado para até 300 VMs mesmo com "singlePlacementGroup = true".**
* O terceiro valor é "zoneBalance", que garante o balanceamento de zona estrito. Isso deve ser "verdadeiro". Isso garante que as distribuições de VM entre zonas não sejam desbalanceadas, garantindo que, quando uma das zonas ficar inativa, as outras duas zonas tenham VMs suficientes para garantir que o cluster continue sendo executado sem interrupção. Um cluster com uma distribuição de VM desbalanceada pode não sobreviver a um cenário de zona insuficiente, pois essa zona pode ter a maioria das VMs. A distribuição de VM desbalanceada entre zonas também levará a problemas relacionados ao posicionamento do serviço & atualizações de infraestrutura ficarem presas. Leia sobre [zoneBalancing](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md#zone-balancing).
* As substituições FaultDomain e UpgradeDomain não precisam ser configuradas.

```json
{
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType1Name')]",
    "location": "[parameters('computeLocation')]",
    "zones": ["1", "2", "3"],
    "properties": {
        "singlePlacementGroup": "true",
        "zoneBalance": true
    }
}
```

>[!NOTE]
> * **Os clusters Service Fabric devem ter pelo menos um nodeType primário. DurabilityLevel de nodeTypes primários devem ser prata ou superior.**
> * O conjunto de dimensionamento de máquina virtual AZ Spanning deve ser configurado com pelo menos três zonas de disponibilidade, independentemente do durabilityLevel.
> * AZ abrangendo o conjunto de dimensionamento de máquinas virtuais com durabilidade prateada (ou acima), deve ter pelo menos 15 VMs.
> * AZ abrangendo o conjunto de dimensionamento de máquinas virtuais com durabilidade de bronze, deve ter pelo menos 6 VMs.

### <a name="enabling-the-support-for-multiple-zones-in-the-service-fabric-nodetype"></a>Habilitando o suporte para várias zonas no Service Fabric nodeType
O nodeType Service Fabric deve ser habilitado para dar suporte a várias zonas de disponibilidade.

* O primeiro valor é **multipleAvailabilityZones** que deve ser definido como true para o NodeType.
* O segundo valor é **sfZonalUpgradeMode** e é opcional. Essa propriedade não poderá ser modificada se um NodeType com vários AZ já estiver presente no cluster.
  A propriedade controla o agrupamento lógico de VMs em domínios de atualização.
  **Se value for definido como "Parallel":** As VMs sob o NodeType serão agrupadas em UDs ignorando as informações de zona em 5 UDs. Isso fará com que o UD0 em todas as zonas seja atualizado ao mesmo tempo. Esse modo de implantação é mais rápido para atualizações, mas não é recomendado, pois vai em relação às diretrizes SDP, que indica que as atualizações devem ser aplicadas somente uma zona por vez.
  **Se value for omitido ou definido como "Hierarchical":** As VMs serão agrupadas para refletir a distribuição zonal em até 15 UDs. Cada uma das três zonas terá 5 UDs. Isso garante que as atualizações passem a zona, movendo para a próxima zona somente após a conclusão de 5 UDs na primeira zona, lentamente em 15 UDs (3 zonas, 5 UDs), o que é mais seguro da perspectiva do cluster e do aplicativo de usuário.
  Essa propriedade define apenas o comportamento de atualização para atualizações de aplicativo e código do infabric. As atualizações do conjunto de dimensionamento de máquinas virtuais subjacentes ainda serão paralelas em todos os AZ.
  Essa propriedade não terá nenhum impacto na distribuição UD para os tipos de nó que não têm várias zonas habilitadas.
* O terceiro valor é **vmssZonalUpgradeMode = Parallel**. Essa é uma propriedade *obrigatória* a ser configurada no cluster, se um NodeType com vários AZs for adicionado. Essa propriedade define o modo de atualização para as atualizações do conjunto de dimensionamento de máquinas virtuais que ocorrerão em paralelo em todos os AZ de uma só vez.
  Agora essa propriedade só pode ser definida como Parallel.
* O recurso de Cluster Service Fabric apiVersion deve ser "2020-12-01-Preview" ou superior.
* A versão do código do cluster deve ser "7.2.445" ou superior.

```json
{
    "apiVersion": "2020-12-01-preview",
    "type": "Microsoft.ServiceFabric/clusters",
    "name": "[parameters('clusterName')]",
    "location": "[parameters('clusterLocation')]",
    "dependsOn": [
        "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]"
    ],
    "properties": {
        "reliabilityLevel": "Platinum",
        "SFZonalUpgradeMode": "Hierarchical",
        "VMSSZonalUpgradeMode": "Parallel",
        "nodeTypes": [
          {
                "name": "[parameters('vmNodeType0Name')]",
                "multipleAvailabilityZones": true,
          }
        ]
}
```

>[!NOTE]
> * Os recursos de IP público e Load Balancer devem usar o SKU Standard, conforme descrito anteriormente neste artigo.
> * a propriedade "multipleAvailabilityZones" no nodeType só pode ser definida no momento da criação de nodeType e não pode ser modificada posteriormente. Portanto, os nodeTypes existentes não podem ser configurados com essa propriedade.
> * Quando "sfZonalUpgradeMode" for omitido ou definido como "Hierarchical", as implantações de cluster e aplicativo serão mais lentas, pois há mais domínios de atualização no cluster. É importante ajustar corretamente os tempos limite da política de atualização para incorporar a duração do tempo de atualização para 15 domínios de atualização. A política de atualização para o aplicativo e o cluster deve ser atualizada para garantir que a implantação não exceda os tempos limite de implantação do Azure Resource serbice do 12hours. Isso significa que a implantação não deve levar mais do que 12hours para 15UDs, ou seja, Não deve levar mais de 40 min/UD.
> * Defina o cluster **reliabilityLevel = Platinum** para garantir que o cluster sobreviver ao cenário de uma zona inoperante.

>[!NOTE]
> Para uma prática recomendada, recomendamos sfZonalUpgradeMode definido como Hierarchical ou ser omitido. A implantação seguirá a distribuição zonal de VMs que afetam uma quantidade menor de réplicas e/ou instâncias tornando-as mais seguras.
> Use sfZonalUpgradeMode definido como Parallel se a velocidade de implantação for uma prioridade ou apenas uma carga de trabalho sem estado for executada no tipo de nó com várias AZ. Isso fará com que a UD Walk aconteça em paralelo em todos os AZ.

### <a name="migration-to-the-node-type-with-multiple-availability-zones"></a>Migração para o tipo de nó com vários Zonas de Disponibilidade
Para todos os cenários de migração, é necessário adicionar um novo nodeType que terá várias zonas de disponibilidade com suporte. Um nodeType existente não pode ser migrado para dar suporte a várias zonas.
[Este](./service-fabric-scale-up-primary-node-type.md) artigo captura as etapas detalhadas da adição de um novo NodeType e também a adição dos outros recursos necessários para o novo NodeType, como os recursos de IP e lb. O mesmo artigo também descreve agora para desativar o nodeType existente depois que o nodeType com várias zonas de disponibilidade é adicionado ao cluster.

* Migração de um nodeType que está usando os recursos básicos de LB e IP: isso já está descrito [aqui](#migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip) para a solução com um tipo de nó por AZ. 
    Para o novo tipo de nó, a única diferença é que há apenas 1 conjunto de dimensionamento de máquinas virtuais e 1 NodeType para todos os AZ em vez de 1 cada por AZ.
* Migração de um nodeType que está usando o SKU LB padrão e os recursos de IP com NSG: Siga o mesmo procedimento descrito acima com a exceção de que não há necessidade de adicionar novos recursos LB, IP e NSG, e os mesmos recursos podem ser reutilizados no novo nodeType.


[sf-architecture]: ./media/service-fabric-cross-availability-zones/sf-cross-az-topology.png
[sf-multi-az-arch]: ./media/service-fabric-cross-availability-zones/sf-multi-az-topology.png
[sf-multi-az-nodes]: ./media/service-fabric-cross-availability-zones/sf-multi-az-nodes.png