---
title: Configurar os modos de rede para serviços de contêiner do Azure Service Fabric | Microsoft Docs
description: Saiba como configurar os diferentes modos de rede suportados pelo Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 2dcb678e8350ae0de3317db3682f0e51e27ab6f5
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621928"
---
# <a name="service-fabric-container-networking-modes"></a>Modos de rede de contêiner do Service Fabric

Um cluster do Azure Service Fabric para o contêiner de serviços usa o modo de rede **nat** por padrão. Erros de implantação podem ocorrer quando mais de um serviço de contêiner está escutando na mesma porta e o modo nat está sendo usado. Para dar suporte a vários serviços de contêiner escutando na mesma porta, o Service Fabric oferece o modo de rede **Aberto** (versão 5.7 e posterior). No modo Aberto, cada serviço de contêiner tem um endereço de IP interno e dinamicamente atribuído que suporta vários serviços escutando na mesma porta.  

Se você tiver um serviço de contêiner com um ponto de extremidade estático em seu manifesto de serviço, você pode criar e excluir novos serviços usando o modo Aberto sem erros de implantação. O mesmo arquivo docker-compose.yml também pode ser usado com mapeamentos de porta estática para criar vários serviços.

Quando um serviço de contêiner é reiniciado ou movido para outro nó no cluster, o endereço IP é alterado. Por esse motivo, não é recomendável usar o endereço IP atribuído dinamicamente para descobrir os serviços de contêiner. Use somente o Serviço de Nomenclatura do Service Fabric ou o Serviço DNS para a descoberta de serviço. 

>[!WARNING]
>O Azure permite um total de 65,356 IPs por rede virtual. A soma do número de nós e o número de instâncias de serviço de contêiner (que usam o modo aberto) não pode exceder 65,356 IPs dentro de uma rede virtual. Para cenários de alta densidade, recomendamos o modo de rede nat. Além disso, outras dependências, como o balanceador de carga terá outra [limitações](https://docs.microsoft.com/azure/azure-subscription-service-limits) a considerar. Atualmente, até 50 IPs por nó foram testadas e comprovadas estável. 
>

## <a name="set-up-open-networking-mode"></a>Configurar o modo de rede Aberto

1. Configurar o modelo do Azure Resource Manager. Na seção **fabricSettings** do recurso de cluster, habilite o serviço DNS e o provedor de IP: 

    ```json
    "fabricSettings": [
                {
                    "name": "DnsService",
                    "parameters": [
                       {
                            "name": "IsEnabled",
                            "value": "true"
                      }
                    ]
                },
                {
                    "name": "Hosting",
                    "parameters": [
                      { 
                            "name": "IPProviderEnabled",
                            "value": "true"
                      }
                    ]
                },
                {
                    "name": "Setup",
                    "parameters": [
                    {
                            "name": "ContainerNetworkSetup",
                            "value": "true"
                    }
                    ]
                }
            ],
    ```
    
2. Configure a seção de perfil de rede do recurso de conjunto de dimensionamento de máquinas virtuais. Isso permite a configuração de vários endereços IP em cada nó do cluster. O exemplo a seguir define cinco endereços de IP por nó para um cluster do Windows/Linux Service Fabric. Você pode ter cinco instâncias de serviço escutando na porta em cada nó. Para que os cinco IPs sejam acessíveis no Azure Load Balancer, inscreva os cinco IPs no Pool de Endereços de Back-End do Azure Load Balancer, conforme mostrado abaixo.  Você também precisará adicionar as variáveis na parte superior do seu modelo, na seção de variáveis.

    Adicione essa seção a Variáveis:

    ```json
    "variables": {
        "nicName": "NIC",
        "vmName": "vm",
        "virtualNetworkName": "VNet",
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
        "vmNodeType0Name": "[toLower(concat('NT1', variables('vmName')))]",
        "subnet0Name": "Subnet-0",
        "subnet0Prefix": "10.0.0.0/24",
        "subnet0Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet0Name'))]",
        "lbID0": "[resourceId('Microsoft.Network/loadBalancers',concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType0Name')))]",
        "lbIPConfig0": "[concat(variables('lbID0'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
        "lbPoolID0": "[concat(variables('lbID0'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
        "lbProbeID0": "[concat(variables('lbID0'),'/probes/FabricGatewayProbe')]",
        "lbHttpProbeID0": "[concat(variables('lbID0'),'/probes/FabricHttpGatewayProbe')]",
        "lbNatPoolID0": "[concat(variables('lbID0'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]"
    }
    ```
    
    Adicione esta seção ao recurso de conjunto de dimensionamento de máquinas virtuais:

    ```json   
    "networkProfile": {
                "networkInterfaceConfigurations": [
                  {
                    "name": "[concat(parameters('nicName'), '-0')]",
                    "properties": {
                      "ipConfigurations": [
                        {
                          "name": "[concat(parameters('nicName'),'-',0)]",
                          "properties": {
                            "primary": "true",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "loadBalancerInboundNatPools": [
                              {
                                "id": "[variables('lbNatPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 1)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 2)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 3)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 4)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 5)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        }
                      ],
                      "primary": true
                    }
                  }
                ]
              }
   ```
 
3. Apenas para clusters do Windows, configure uma regra de grupo de segurança de rede (NSG) do Azure que abre a porta UDP/53 para a rede virtual com os seguintes valores:

   |Configuração |Valor | |
   | --- | --- | --- |
   |Prioridade |2000 | |
   |Nome |Custom_Dns  | |
   |Origem |VirtualNetwork | |
   |Destino | VirtualNetwork | |
   |Serviço | DNS (UDP/53) | |
   |Action | Allow  | |
   | | |

4. Especifique o modo de rede no manifesto do aplicativo para cada serviço: `<NetworkConfig NetworkType="Open">`. O modo de rede **Aberto** resulta no fornecimento de um endereço IP dedicado para o serviço. Se um modo não for especificado, o serviço torna **nat** o modo padrão. Assim, no exemplo de manifesto a seguir, os serviços `NodeContainerServicePackage1` e `NodeContainerServicePackage2` podem escutar na mesma porta (os dois serviços estão escutando em `Endpoint1`). Quando o modo de rede Aberto é especificado, `PortBinding` as configurações não podem ser especificadas.

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <ApplicationManifest ApplicationTypeName="NodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
      <Description>Calculator Application</Description>
      <Parameters>
        <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
        <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
      </Parameters>
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeContainerServicePackage1" ServiceManifestVersion="1.0"/>
        <Policies>
          <ContainerHostPolicies CodePackageRef="NodeContainerService1.Code" Isolation="hyperv">
           <NetworkConfig NetworkType="Open"/>
          </ContainerHostPolicies>
        </Policies>
      </ServiceManifestImport>
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeContainerServicePackage2" ServiceManifestVersion="1.0"/>
        <Policies>
          <ContainerHostPolicies CodePackageRef="NodeContainerService2.Code" Isolation="default">
            <NetworkConfig NetworkType="Open"/>
          </ContainerHostPolicies>
        </Policies>
      </ServiceManifestImport>
    </ApplicationManifest>
    ```

    Você pode misturar e combinar modos de rede diferentes nos serviços dentro de um aplicativo para um cluster do Windows. Alguns serviços usam o modo Aberto enquanto outros usam o modo nat. Quando um serviço estiver configurado para usar o modo nat, a porta que o serviço está escutando deve ser exclusiva.

    >[!NOTE]
    >Em clusters do Linux, não há suporte para a combinação de modos de rede para serviços diferentes. 
    >

5. Quando o modo **Abrir** é selecionado, a definição do **ponto de extremidade** no manifesto do serviço deve apontar explicitamente para o pacote de código correspondente ao ponto de extremidade, mesmo se o pacote de serviço tiver somente um pacote de código nele. 
   
   ```xml
   <Resources>
     <Endpoints>
       <Endpoint Name="ServiceEndpoint" Protocol="http" Port="80" CodePackageRef="Code"/>
     </Endpoints>
   </Resources>
   ```
   
6. Para Windows, um reinício da VM fará com que a rede aberta seja recriada. Isso é para atenuar um problema subjacente na pilha de rede. O comportamento padrão é recriar a rede. Se esse comportamento precisar ser desativado, a configuração a seguir poderá ser usada seguida por uma atualização de configuração.

```json
"fabricSettings": [
                {
                    "name": "Setup",
                    "parameters": [
                    {
                            "name": "SkipContainerNetworkResetOnReboot",
                            "value": "true"
                    }
                    ]
                }
            ],          
 ``` 
 
## <a name="next-steps"></a>Próximas etapas
* [Entender o modelo de aplicativo do Service Fabric](service-fabric-application-model.md)
* [Saiba mais sobre recursos do manifesto do serviço Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-manifest-resources)
* [Implantar um contêiner do Windows no Service Fabric no Windows Server 2016](service-fabric-get-started-containers.md)
* [Implantar um contêiner do Docker no Service Fabric no Linux](service-fabric-get-started-containers-linux.md)
