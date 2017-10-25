---
title: "Padrões de rede do Service Fabric do Azure | Microsoft Docs"
description: "Descreve os padrões de rede comuns do Service Fabric e como criar um cluster usando os recursos de rede do Azure."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/30/2017
ms.author: ryanwi
ms.openlocfilehash: 535ea21a2c08be5f676ee24269b323a415b92607
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="service-fabric-networking-patterns"></a>Padrões de rede do Service Fabric
Você pode integrar seu cluster do Azure Service Fabric a outros recursos de rede do Azure. Neste artigo, mostramos como criar clusters que usam os seguintes recursos:

- [Rede virtual ou sub-rede existente](#existingvnet)
- [Endereço IP público estático](#staticpublicip)
- [Balanceador de carga somente interno](#internallb)
- [Balanceador interno e externo de carga](#internalexternallb)

O Service Fabric é executado em um conjunto de dimensionamento de máquinas virtuais padrão. Qualquer funcionalidade que você pode usar em um conjunto de dimensionamento de máquinas virtuais você pode usar também com um cluster do Service Fabric. As seções de rede dos modelos do Azure Resource Manager para os conjuntos de dimensionamento de máquinas virtuais e o Service Fabric são idênticas. Depois de implantar uma rede virtual existente, é fácil incorporar outros recursos de rede como o Azure ExpressRoute, o Gateway de VPN do Azure, um grupo de segurança de rede e emparelhamento de rede virtual.

O Service Fabric é único entre outros recursos de rede em um aspecto. O [portal do Azure](https://portal.azure.com) usa internamente o provedor de recursos do Service Fabric para chamar um cluster para obter informações sobre nós e aplicativos. O provedor de recursos do Service Fabric exige acesso de entrada acessível publicamente à porta do Gateway HTTP (19080 por padrão) no ponto de extremidade de gerenciamento. O [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) usa o ponto de extremidade de gerenciamento para gerenciar o cluster. Essa porta também é usada pelo provedor de recursos do Service Fabric para consultar informações sobre o cluster para exibição no Portal do Azure. 

Se a porta 19080 não estiver acessível no provedor de recursos do Service Fabric, uma mensagem como *Nós Não Encontrados* será exibida no portal e a lista de nós e aplicativos aparecerá vazia. Se desejar ver o cluster por meio do Portal do Azure, o balanceador de carga deverá expor um endereço IP público e o grupo de segurança de rede deverá permitir o tráfego de entrada pela porta 19080. Se a sua configuração não atender a esses requisitos, o Portal do Azure não exibirá o status atual do cluster.

## <a name="templates"></a>Modelos

Todos os modelos do Service Fabric estão em [um arquivo de download](https://msdnshared.blob.core.windows.net/media/2016/10/SF_Networking_Templates.zip). Você deverá conseguir implantar os modelos no estado em que se encontram usando os comandos do PowerShell a seguir. Se você estiver implantando o modelo de rede Virtual do Azure existente ou o modelo de IP público estático, leia primeiro a seção [Instalação inicial](#initialsetup) deste artigo.

<a id="initialsetup"></a>
## <a name="initial-setup"></a>Configuração inicial

### <a name="existing-virtual-network"></a>Rede virtual existente

No exemplo a seguir, começamos com uma rede virtual existente chamada ExistingRG-vnet no grupo de recursos **ExistingRG**. Essa sub-rede é chamada de default. Esses recursos padrão são criados ao usar o Portal do Azure para criar uma VM (máquina virtual) padrão. Você pode criar a rede virtual e a sub-rede sem criar a VM, mas a meta principal de adicionar um cluster a uma rede virtual existente é fornecer conectividade de rede para outras VMs. Criar a VM fornece um bom exemplo de como uma rede virtual existente é normalmente usada. Se o cluster do Service Fabric usar apenas um balanceador de carga interno sem um endereço IP público, a VM e seu IP público poderão ser usados como uma *jump box*.

### <a name="static-public-ip-address"></a>Endereço IP público estático

Um endereço IP público estático geralmente é um recurso dedicado gerenciado separadamente da VM ou VMs às quais ele é atribuído. Ele é provisionado em um grupo de recursos de rede dedicado (em vez de no grupo de recursos de cluster do Service Fabric em si). Crie um endereço IP público estático com o nome staticIP1 no mesmo grupo de recursos ExistingRG no Portal do Azure ou usando o PowerShell:

```powershell
PS C:\Users\user> New-AzureRmPublicIpAddress -Name staticIP1 -ResourceGroupName ExistingRG -Location westus -AllocationMethod Static -DomainNameLabel sfnetworking

Name                     : staticIP1
ResourceGroupName        : ExistingRG
Location                 : westus
Id                       : /subscriptions/1237f4d2-3dce-1236-ad95-123f764e7123/resourceGroups/ExistingRG/providers/Microsoft.Network/publicIPAddresses/staticIP1
Etag                     : W/"fc8b0c77-1f84-455d-9930-0404ebba1b64"
ResourceGuid             : 77c26c06-c0ae-496c-9231-b1a114e08824
ProvisioningState        : Succeeded
Tags                     :
PublicIpAllocationMethod : Static
IpAddress                : 40.83.182.110
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : null
DnsSettings              : {
                             "DomainNameLabel": "sfnetworking",
                             "Fqdn": "sfnetworking.westus.cloudapp.azure.com"
                           }
```

### <a name="service-fabric-template"></a>Modelo do Service Fabric

Nos exemplos neste artigo, usamos o template.json do Service Fabric. Você pode usar o Assistente do portal padrão para baixar o modelo por meio do Portal antes de criar um cluster. Você também pode usar um dos modelos da [galeria de modelos](https://azure.microsoft.com/en-us/documentation/templates/?term=service+fabric), por exemplo o [cluster de cinco nós do Service Fabric](https://azure.microsoft.com/en-us/resources/templates/service-fabric-secure-cluster-5-node-1-nodetype/).

<a id="existingvnet"></a>
## <a name="existing-virtual-network-or-subnet"></a>Rede virtual ou sub-rede existente

1. Altere o parâmetro da sub-rede para o nome da sub-rede existente e adicione dois novos parâmetros para referenciar a rede virtual existente:

    ```
        "subnet0Name": {
                "type": "string",
                "defaultValue": "default"
            },
            "existingVNetRGName": {
                "type": "string",
                "defaultValue": "ExistingRG"
            },

            "existingVNetName": {
                "type": "string",
                "defaultValue": "ExistingRG-vnet"
            },
            /*
            "subnet0Name": {
                "type": "string",
                "defaultValue": "Subnet-0"
            },
            "subnet0Prefix": {
                "type": "string",
                "defaultValue": "10.0.0.0/24"
            },*/
    ```


2. Altere a variável `vnetID` para que ela aponte para a rede virtual existente:

    ```
            /*old "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",*/
            "vnetID": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingVNetRGName'), '/providers/Microsoft.Network/virtualNetworks/', parameters('existingVNetName'))]",
    ```

3. Remova `Microsoft.Network/virtualNetworks` de seus recursos de modo que o Azure não crie uma nova rede virtual:

    ```
    /*{
    "apiVersion": "[variables('vNetApiVersion')]",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[parameters('virtualNetworkName')]",
    "location": "[parameters('computeLocation')]",
    "properities": {
        "addressSpace": {
            "addressPrefixes": [
                "[parameters('addressPrefix')]"
            ]
        },
        "subnets": [
            {
                "name": "[parameters('subnet0Name')]",
                "properties": {
                    "addressPrefix": "[parameters('subnet0Prefix')]"
                }
            }
        ]
    },
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    }
    },*/
    ```

4. Comente a rede virtual com base no atributo `dependsOn` de `Microsoft.Compute/virtualMachineScaleSets`, de modo que você não dependa da criação de uma nova rede virtual:

    ```
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Computer/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType0Name')]",
    "location": "[parameters('computeLocation')]",
    "dependsOn": [
        /*"[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]",
        */
        "[Concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray0')[0])]",

    ```

5. Implantar o modelo:

    ```powershell
    New-AzureRmResourceGroup -Name sfnetworkingexistingvnet -Location westus
    New-AzureRmResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkingexistingvnet -TemplateFile C:\SFSamples\Final\template\_existingvnet.json
    ```

    Após a implantação, sua rede virtual deverá incluir as VMs do novo conjunto de dimensionamento. O tipo de nó de conjunto de dimensionamento de máquinas virtuais deve mostrar a rede virtual e a sub-rede existentes. Você também pode usar o protocolo RDP para acessar a VM que já está na rede virtual e executar o ping nas VMs do novo conjunto de dimensionamento:

    ```
    C:>\Users\users>ping 10.0.0.5 -n 1
    C:>\Users\users>ping NOde1000000 -n 1
    ```

Para obter outro exemplo, consulte [um que não seja específico para o Service Fabric](https://github.com/gbowerman/azure-myriad/tree/master/existing-vnet).


<a id="staticpublicip"></a>
## <a name="static-public-ip-address"></a>Endereço IP público estático

1. Adicione parâmetros para o grupo de recursos do IP estático existente, nome e FQDN (nome de domínio totalmente qualificado):

    ```
    "existingStaticIPResourceGroup": {
                "type": "string"
            },
            "existingStaticIPName": {
                "type": "string"
            },
            "existingStaticIPDnsFQDN": {
                "type": "string"
    }
    ```

2. Remova o parâmetro `dnsName`. (O endereço IP estático já tem um.)

    ```
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

3. Adicione uma variável para referenciar o endereço IP estático existente:

    ```
    "existingStaticIP": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingStaticIPResourceGroup'), '/providers/Microsoft.Network/publicIPAddresses/', parameters('existingStaticIPName'))]",
    ```

4. Remova `Microsoft.Network/publicIPAddresses` de seus recursos de modo que o Azure não crie um novo endereço IP:

    ```
    /*
    {
        "apiVersion": "[variables('publicIPApiVersion')]",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[concat(parameters('lbIPName'),)'-', '0')]",
        "location": "[parameters('computeLocation')]",
        "properties": {
            "dnsSettings": {
                "domainNameLabel": "[parameters('dnsName')]"
            },
            "publicIPAllocationMethod": "Dynamic"        
        },
        "tags": {
            "resourceType": "Service Fabric",
            "clusterName": "[parameters('clusterName')]"
        }
    }, */
    ```

5. Comente o endereço IP com base no atributo `dependsOn` de `Microsoft.Network/loadBalancers`, de modo que você não dependa da criação de um novo endereço IP:

    ```
    "apiVersion": "[variables('lbIPApiVersion')]",
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB', '-', parameters('clusterName'), '-', parameters('vmNodeType0Name'))]",
    "location": "[parameters('computeLocation')]",
    /*
    "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/', concat(parameters('lbIPName'), '-', '0'))]"
    ], */
    "properties": {
    ```

6. No recurso `Microsoft.Network/loadBalancers`, altere o elemento `publicIPAddress` de `frontendIPConfigurations` para referenciar o endereço IP estático existente em vez de um recém-criado:

    ```
                "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                "publicIPAddress": {
                                    /*"id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"*/
                                    "id": "[variables('existingStaticIP')]"
                                }
                            }
                        }
                    ],
    ```

7. No recurso `Microsoft.ServiceFabric/clusters`, altere `managementEndpoint` para o FQDN do DNS do endereço IP estático. Se estiver usando um cluster seguro, lembre-se de alterar *http://* para *https://*. (Observe que essa etapa se aplica apenas aos clusters do Service Fabric. Se estiver usando um conjunto de dimensionamento de máquinas virtuais, ignore esta etapa.)

    ```
                    "fabricSettings": [],
                    /*"managementEndpoint": "[concat('http://',reference(concat(parameters('lbIPName'),'-','0')).dnsSettings.fqdn,':',parameters('nt0fabricHttpGatewayPort'))]",*/
                    "managementEndpoint": "[concat('http://',parameters('existingStaticIPDnsFQDN'),':',parameters('nt0fabricHttpGatewayPort'))]",
    ```

8. Implantar o modelo:

    ```powershell
    New-AzureRmResourceGroup -Name sfnetworkingstaticip -Location westus

    $staticip = Get-AzureRmPublicIpAddress -Name staticIP1 -ResourceGroupName ExistingRG

    $staticip

    New-AzureRmResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkingstaticip -TemplateFile C:\SFSamples\Final\template\_staticip.json -existingStaticIPResourceGroup $staticip.ResourceGroupName -existingStaticIPName $staticip.Name -existingStaticIPDnsFQDN $staticip.DnsSettings.Fqdn
    ```

Após a implantação, você pode ver que o balanceador de carga está limitado ao endereço IP público estático do outro grupo de recursos. O ponto de extremidade da conexão de cliente do Service Fabric e o ponto de extremidade do [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) apontam para o FQDN do DNS do endereço IP estático.

<a id="internallb"></a>
## <a name="internal-only-load-balancer"></a>Balanceador de carga somente interno

Este cenário substitui o balanceador externo de carga no modelo padrão do Service Fabric por um balanceador de carga somente interno. Para obter implicações para o Portal do Azure e o provedor de recursos do Service Fabric, consulte a seção anterior.

1. Remova o parâmetro `dnsName`. (Ele não é necessário.)

    ```
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

2. Opcionalmente, você poderá adicionar um parâmetro de endereço IP estático se estiver usando o método de alocação estática. Se você usar um método de alocação dinâmica, você não precisará realizar esta etapa.

    ```
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

3. Remova `Microsoft.Network/publicIPAddresses` de seus recursos de modo que o Azure não crie um novo endereço IP:

    ```
    /*
    {
        "apiVersion": "[variables('publicIPApiVersion')]",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[concat(parameters('lbIPName'),)'-', '0')]",
        "location": "[parameters('computeLocation')]",
        "properties": {
            "dnsSettings": {
                "domainNameLabel": "[parameters('dnsName')]"
            },
            "publicIPAllocationMethod": "Dynamic"        
        },
        "tags": {
            "resourceType": "Service Fabric",
            "clusterName": "[parameters('clusterName')]"
        }
    }, */
    ```

4. Remova o atributo `dependsOn` do endereço IP de `Microsoft.Network/loadBalancers`, de modo que você não dependa da criação de um novo endereço IP. Adicione o atributo `dependsOn` de rede virtual porque o balanceador de carga agora depende da sub-rede da rede virtual:

    ```
                "apiVersion": "[variables('lbApiVersion')]",
                "type": "Microsoft.Network/loadBalancers",
                "name": "[concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'))]",
                "location": "[parameters('computeLocation')]",
                "dependsOn": [
                    /*"[concat('Microsoft.Network/publicIPAddresses/',concat(parameters('lbIPName'),'-','0'))]"*/
                    "[concat('Microsoft.Network/virtualNetworks/',parameters('virtualNetworkName'))]"
                ],
    ```

5. Altere a configuração `frontendIPConfigurations` do balanceador de carga, anteriormente configurado para usar `publicIPAddress`, para que passe a usar uma sub-rede e `privateIPAddress`. `privateIPAddress` usa um endereço IP interno estático predefinido. Para usar um endereço IP dinâmico, remova o elemento `privateIPAddress` e altere `privateIPAllocationMethod` para **Dinâmico**.

    ```
                "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                /*
                                "publicIPAddress": {
                                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"
                                } */
                                "subnet" :{
                                    "id": "[variables('subnet0Ref')]"
                                },
                                "privateIPAddress": "[parameters('internalLBAddress')]",
                                "privateIPAllocationMethod": "Static"
                            }
                        }
                    ],
    ```

6. No recurso `Microsoft.ServiceFabric/clusters`, altere `managementEndpoint` para apontar para o endereço do balanceador de carga interno. Se estiver usando um cluster seguro, lembre-se de alterar *http://* para *https://*. (Observe que essa etapa se aplica apenas aos clusters do Service Fabric. Se estiver usando um conjunto de dimensionamento de máquinas virtuais, ignore esta etapa.)

    ```
                    "fabricSettings": [],
                    /*"managementEndpoint": "[concat('http://',reference(concat(parameters('lbIPName'),'-','0')).dnsSettings.fqdn,':',parameters('nt0fabricHttpGatewayPort'))]",*/
                    "managementEndpoint": "[concat('http://',reference(variables('lbID0')).frontEndIPConfigurations[0].properties.privateIPAddress,':',parameters('nt0fabricHttpGatewayPort'))]",
    ```

7. Implantar o modelo:

    ```powershell
    New-AzureRmResourceGroup -Name sfnetworkinginternallb -Location westus

    New-AzureRmResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkinginternallb -TemplateFile C:\SFSamples\Final\template\_internalonlyLB.json
    ```

Após a implantação, o balanceador de carga usa o endereço IP estático privado 10.0.0.250. Se você tiver outro computador na mesma rede virtual, você poderá ir para o ponto de extremidade interno do [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md). Observe que ele se conecta a um dos nós por trás do balanceador de carga.

<a id="internalexternallb"></a>
## <a name="internal-and-external-load-balancer"></a>Balanceador de carga interno e externo

Neste cenário, você começa com o balanceador externo de carga de tipo de nó único existente e adiciona um balanceador de carga interno para o mesmo tipo de nó. Uma porta de back-end anexada ao pool de endereços de back-end pode ser atribuída somente a um único balanceador de carga. Escolha qual balanceador de carga terá as suas portas do aplicativo e o balanceador de carga terá seus pontos de extremidade de gerenciamento (portas 19000 e 19080). Se você colocar os pontos de extremidade de gerenciamento no balanceador de carga interno, lembre-se das restrições do provedor de recursos do Service Fabric discutidas anteriormente neste artigo. No exemplo que usamos, os pontos de extremidade de gerenciamento permanecem no balanceador externo de carga. Você também pode adicionar uma porta 80 do aplicativo e colocá-la no balanceador de carga interno.

Em um cluster com dois tipos de nós, um tipo de nó é no balanceador externo de carga. O outro tipo de nó é no balanceador de carga interno. Para usar um cluster com dois tipos de nós, no modelo com dois tipos de nós criado pelo portal (que vem com dois balanceadores de carga), mude o segundo balanceador de carga para um balanceador de carga interno. Para obter mais informações, consulte a seção [Balanceador de carga somente interno](#internallb).

1. Adicione o parâmetro do endereço IP de balanceador de carga interno estático. (Para obter notas relacionadas ao uso de um endereço IP dinâmico, consulte as seções anteriores deste artigo.)

    ```
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

2. Adicione um parâmetro de porta 80 do aplicativo.

3. Para adicionar versões internas das variáveis de rede existentes, copie e cole-as, adicionando “-Int” ao nome:

    ```
    /* Add internal load balancer networking variables */
            "lbID0-Int": "[resourceId('Microsoft.Network/loadBalancers', concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'), '-Internal'))]",
            "lbIPConfig0-Int": "[concat(variables('lbID0-Int'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
            "lbPoolID0-Int": "[concat(variables('lbID0-Int'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
            "lbProbeID0-Int": "[concat(variables('lbID0-Int'),'/probes/FabricGatewayProbe')]",
            "lbHttpProbeID0-Int": "[concat(variables('lbID0-Int'),'/probes/FabricHttpGatewayProbe')]",
            "lbNatPoolID0-Int": "[concat(variables('lbID0-Int'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]",
            /* Internal load balancer networking variables end */
    ```

4. Se você começar com o modelo gerado pelo portal que usa a porta 80 do aplicativo, o modelo padrão do portal adicionará AppPort1 (porta 80) ao balanceador externo de carga. Nesse caso, remova AppPort1 do balanceador externo de carga `loadBalancingRules` e das investigações, para que seja possível adicioná-la ao balanceador interno de carga:

    ```
    "loadBalancingRules": [
        {
            "name": "LBHttpRule",
            "properties":{
                "backendAddressPool": {
                    "id": "[variables('lbPoolID0')]"
                },
                "backendPort": "[parameters('nt0fabricHttpGatewayPort')]",
                "enableFloatingIP": "false",
                "frontendIPConfiguration": {
                    "id": "[variables('lbIPConfig0')]"            
                },
                "frontendPort": "[parameters('nt0fabricHttpGatewayPort')]",
                "idleTimeoutInMinutes": "5",
                "probe": {
                    "id": "[variables('lbHttpProbeID0')]"
                },
                "protocol": "tcp"
            }
        } /* Remove AppPort1 from the external load balancer.
        {
            "name": "AppPortLBRule1",
            "properties": {
                "backendAddressPool": {
                    "id": "[variables('lbPoolID0')]"
                },
                "backendPort": "[parameters('loadBalancedAppPort1')]",
                "enableFloatingIP": "false",
                "frontendIPConfiguration": {
                    "id": "[variables('lbIPConfig0')]"            
                },
                "frontendPort": "[parameters('loadBalancedAppPort1')]",
                "idleTimeoutInMinutes": "5",
                "probe": {
                    "id": "[concate(variables('lbID0'), '/probes/AppPortProbe1')]"
                },
                "protocol": "tcp"
            }
        }*/

    ],
    "probes": [
        {
            "name": "FabricGatewayProbe",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('nt0fabricTcpGatewayPort')]",
                "protocol": "tcp"
            }
        },
        {
            "name": "FabricHttpGatewayProbe",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('nt0fabricHttpGatewayPort')]",
                "protocol": "tcp"
            }
        } /* Remove AppPort1 from the external load balancer.
        {
            "name": "AppPortProbe1",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('loadBalancedAppPort1')]",
                "protocol": "tcp"
            }
        } */

    ],
    "inboundNatPools": [
    ```

5. Adicione um segundo recurso `Microsoft.Network/loadBalancers`. Ele tem aparência similar à do balanceador de carga interno criado na seção [Balanceador de carga somente interno](#internallb), mas usa as variáveis de balanceador de carga "-Int" e implementa apenas a porta 80 do aplicativo. Isso também remove `inboundNatPools` para manter os pontos de extremidade de RDP no balanceador de carga público. Se você desejar RDP no balanceador interno de carga, mova `inboundNatPools` do balanceador externo de carga para este balanceador interno de carga:

    ```
            /* Add a second load balancer, configured with a static privateIPAddress and the "-Int" load balancer variables. */
            {
                "apiVersion": "[variables('lbApiVersion')]",
                "type": "Microsoft.Network/loadBalancers",
                /* Add "-Internal" to the name. */
                "name": "[concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'), '-Internal')]",
                "location": "[parameters('computeLocation')]",
                "dependsOn": [
                    /* Remove public IP dependsOn, add vnet dependsOn
                    "[concat('Microsoft.Network/publicIPAddresses/',concat(parameters('lbIPName'),'-','0'))]"
                    */
                    "[concat('Microsoft.Network/virtualNetworks/',parameters('virtualNetworkName'))]"
                ],
                "properties": {
                    "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                /* Switch from Public to Private IP address
                                */
                                "publicIPAddress": {
                                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"
                                }
                                */
                                "subnet" :{
                                    "id": "[variables('subnet0Ref')]"
                                },
                                "privateIPAddress": "[parameters('internalLBAddress')]",
                                "privateIPAllocationMethod": "Static"
                            }
                        }
                    ],
                    "backendAddressPools": [
                        {
                            "name": "LoadBalancerBEAddressPool",
                            "properties": {}
                        }
                    ],
                    "loadBalancingRules": [
                        /* Add the AppPort rule. Be sure to reference the "-Int" versions of backendAddressPool, frontendIPConfiguration, and the probe variables. */
                        {
                            "name": "AppPortLBRule1",
                            "properties": {
                                "backendAddressPool": {
                                    "id": "[variables('lbPoolID0-Int')]"
                                },
                                "backendPort": "[parameters('loadBalancedAppPort1')]",
                                "enableFloatingIP": "false",
                                "frontendIPConfiguration": {
                                    "id": "[variables('lbIPConfig0-Int')]"
                                },
                                "frontendPort": "[parameters('loadBalancedAppPort1')]",
                                "idleTimeoutInMinutes": "5",
                                "probe": {
                                    "id": "[concat(variables('lbID0-Int'),'/probes/AppPortProbe1')]"
                                },
                                "protocol": "tcp"
                            }
                        }
                    ],
                    "probes": [
                    /* Add the probe for the app port. */
                    {
                            "name": "AppPortProbe1",
                            "properties": {
                                "intervalInSeconds": 5,
                                "numberOfProbes": 2,
                                "port": "[parameters('loadBalancedAppPort1')]",
                                "protocol": "tcp"
                            }
                        }
                    ],
                    "inboundNatPools": [
                    ]
                },
                "tags": {
                    "resourceType": "Service Fabric",
                    "clusterName": "[parameters('clusterName')]"
                }
            },
    ```

6. Em `networkProfile` para o recurso `Microsoft.Compute/virtualMachineScaleSets`, adicione o pool de endereços de back-end interno:

    ```
    "loadBalancerBackendAddressPools": [
                                                        {
                                                            "id": "[variables('lbPoolID0')]"
                                                        },
                                                        {
                                                            /* Add internal BE pool */
                                                            "id": "[variables('lbPoolID0-Int')]"
                                                        }
    ],
    ```

7. Implantar o modelo:

    ```powershell
    New-AzureRmResourceGroup -Name sfnetworkinginternalexternallb -Location westus

    New-AzureRmResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkinginternalexternallb -TemplateFile C:\SFSamples\Final\template\_internalexternalLB.json
    ```

Após a implantação, você poderá ver dois balanceadores de carga no grupo de recursos. Se você procurar os balanceadores de carga, você poderá ver os endereços IP públicos e pontos de extremidade de gerenciamento (portas 19000 e 19080) atribuídos ao endereço IP público. Você também poderá ver o endereço IP interno estático e o ponto de extremidade do aplicativo (porta 80) atribuído ao balanceador de carga interno. Ambos os balanceadores de carga usam o mesmo pool de back-end de conjunto de dimensionamento de máquinas virtuais.

## <a name="next-steps"></a>Próximas etapas
[Criar um cluster](service-fabric-cluster-creation-via-arm.md)
