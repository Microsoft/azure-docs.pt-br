---
title: "Configurar os modos de rede para serviços de contêiner do Azure Service Fabric | Microsoft Docs"
description: Saiba como configurar os diferentes modos de rede com suporte do Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.openlocfilehash: 1ecded3af6396f50e67dc5d2a9ef8337699046ea
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="service-fabric-container-networking-modes"></a>Modos de rede de contêiner do Service Fabric

O modo de rede padrão oferecido no cluster do Service Fabric para serviços de contêiner é o modo de rede `nat`. Com o modo de rede `nat`, ter mais de um serviço de contêiner escutando na mesma porta resulta em erros de implantação. Para executar vários serviços que escutam na mesma porta, o Service Fabric oferece suporte ao modo de rede `open` (versão 5.7 ou superior). Com o modo de rede `open`, cada serviço de contêiner recebe internamente um endereço IP atribuído dinamicamente, permitindo que vários serviços escutem na mesma porta.   

Assim, com um único tipo de serviço com um ponto de extremidade estático definido no manifesto do serviço, novos serviços podem ser criados e excluídos sem erros de implantação usando o modo de rede `open`. Da mesma forma, é possível usar o mesmo arquivo `docker-compose.yml` com mapeamentos de porta estática para a criação de vários serviços.

O uso do IP atribuído dinamicamente para descobrir os serviços não é aconselhável, pois o endereço IP muda quando o serviço reinicia ou muda para outro nó. Use somente o **Serviço de Nomenclatura do Service Fabric** ou o **Serviço DNS** para a descoberta de serviço. 


> [!WARNING]
> Só há permissão para um total de 4096 IPs por rede virtual no Azure. Portanto, a soma do número de nós e o número de instâncias do serviço de contêiner (com a rede `open`) não pode exceder 4096 dentro de uma rede virtual. Para esses cenários de alta densidade, o modo de rede `nat` é recomendado.
>

## <a name="setting-up-open-networking-mode"></a>Configurar o modo de rede aberto

1. Configure o modelo do Azure Resource Manager habilitando o Serviço de DNS e o Provedor de IP em `fabricSettings`. 

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
                    "name":  "Trace/Etw", 
                    "parameters": [
                    {
                            "name": "Level",
                            "value": "5"
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

2. Configure a seção de perfil de rede para permitir a configuração de vários endereços IP em cada nó do cluster. O exemplo a seguir configura cinco endereços IP por nó (portanto, é possível ter cinco instâncias de serviço escutando na porta em cada nó) para um cluster do Service Fabric do Windows/Linux.

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
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 2)]",
                          "properties": {
                            "primary": "false",
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 3)]",
                          "properties": {
                            "primary": "false",
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 4)]",
                          "properties": {
                            "primary": "false",
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 5)]",
                          "properties": {
                            "primary": "false",
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
 

3. Para clusters do Windows somente, configure uma regra NSG para abrir a porta UDP/53 para a rede virtual com os seguintes valores:

   | Prioridade |    Nome    |    Fonte      |  Destino   |   O Barramento de    | Ação |
   |:--------:|:----------:|:--------------:|:--------------:|:------------:|:------:|
   |     2000 | Custom_Dns | VirtualNetwork | VirtualNetwork | DNS (UDP/53) | PERMITIR  |


4. Especifique o modo de rede no manifesto do aplicativo para cada serviço `<NetworkConfig NetworkType="open">`.  O modo `open` resulta no fornecimento de um endereço IP dedicado para o serviço. Se um modo não for especificado, o padrão do modo `nat` básico será usado. Assim, no exemplo de manifesto a seguir, `NodeContainerServicePackage1` e `NodeContainerServicePackage2` podem escutar na mesma porta (os dois serviços estão escutando em `Endpoint1`).

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <ApplicationManifest ApplicationTypeName="NodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
      <Description>Calculator Application</Description>
      <Parameters>
        <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
        <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
      </Parameters>
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeContainerServicePackage1" ServiceManifestVersion="1.0"/>
        <Policies>
          <ContainerHostPolicies CodePackageRef="NodeContainerService1.Code" Isolation="hyperv">
           <NetworkConfig NetworkType="open"/>
           <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
          </ContainerHostPolicies>
        </Policies>
      </ServiceManifestImport>
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeContainerServicePackage2" ServiceManifestVersion="1.0"/>
        <Policies>
          <ContainerHostPolicies CodePackageRef="NodeContainerService2.Code" Isolation="default">
            <NetworkConfig NetworkType="open"/>
            <PortBinding ContainerPort="8910" EndpointRef="Endpoint1"/>
          </ContainerHostPolicies>
        </Policies>
      </ServiceManifestImport>
    </ApplicationManifest>
    ```
Você pode misturar e combinar modos de rede diferentes nos serviços dentro de um aplicativo para um cluster do Windows. Assim, alguns serviços podem ficar no modo `open`, e outros no modo de rede `nat`. Quando um serviço estiver configurado com `nat`, a porta na qual ele está escutando deverá ser exclusiva. Não há suporte para a combinação de modos de rede para serviços diferentes em clusters do Linux. 


## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu sobre modos de rede oferecidos pelo Service Fabric.  

* [Modelo de aplicativo da Malha do Serviço](service-fabric-application-model.md)
* [Recursos do manifesto do serviço Service Fabric](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-service-manifest-resources)
* [Implantar um contêiner do Windows no Service Fabric no Windows Server 2016](service-fabric-get-started-containers.md)
* [Implantar um contêiner do Docker no Service Fabric no Linux](service-fabric-get-started-containers-linux.md)
