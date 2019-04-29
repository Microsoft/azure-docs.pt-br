---
title: Configurar um Gateway para rotear solicitações | Microsoft Docs
description: Saiba como configurar o gateway que manipula o tráfego de entrada para seus aplicativos em execução na Malha do Azure Service Fabric.
services: service-fabric-mesh
documentationcenter: .net
author: dkkapur
manager: chakdan
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/28/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: 2e2502e35b3720ddbfe5950b89e2388de378f2ba
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60583634"
---
# <a name="configure-a-gateway-resource-to-route-requests"></a>Configure um recurso de Gateway para rotear solicitações

Um recurso de Gateway é usado para rotear o tráfego de entrada para a rede que hospeda seu aplicativo. Configure-o para especificar regras por meio do qual as solicitações são direcionadas para serviços específicos ou pontos de extremidade com base na estrutura da solicitação. Veja [Introdução à rede na Malha do Azure Service Fabric](service-fabric-mesh-networks-and-gateways.md) para obter mais informações sobre redes e gateways na malha. 

Recursos do gateway precisam ser declarado como parte de seu modelo de implantação (JSON ou yaml) e são dependentes de um recurso de rede. Este documento descreve as várias propriedades que podem ser definidas para seu gateway e abrange uma configuração de gateway de exemplo.

## <a name="options-for-configuring-your-gateway-resource"></a>Opções para configurar o recurso de Gateway

Uma vez que o recurso de Gateway atua como uma ponte entre seu aplicativo e rede da infraestrutura subjacente (a `open` rede). Você só precisará configurar um (na visualização da Malha, há um limite de um gateway por aplicativo). A declaração para o recurso consiste em duas partes principais: metadados de recursos e as propriedades. 

### <a name="gateway-resource-metadata"></a>Metadados de recurso de gateway

Um gateway é declarado com os seguintes metadados:
* `apiVersion` - deve ser definido para "2018-09-01-preview" (ou posterior, no futuro)
* `name` - um nome da cadeia de caracteres para este gateway
* `type` - "Microsoft.ServiceFabricMesh/gateways"
* `location` -deve ser definido como o local do seu aplicativo / rede; normalmente, será uma referência para o parâmetro de local em sua implantação
* `dependsOn` - a rede para o qual esse gateway servirá como um ponto de entrada para

Aqui está a aparência dele em um modelo de implantação do Azure Resource Manager (JSON): 

```json
{
  "apiVersion": "2018-09-01-preview",
  "name": "myGateway",
  "type": "Microsoft.ServiceFabricMesh/gateways",
  "location": "[parameters('location')]",
  "dependsOn": [
    "Microsoft.ServiceFabricMesh/networks/myNetwork"
  ],
  "properties": {
    [...]
  }
}
```

### <a name="gateway-properties"></a>Propriedades do gateway

A seção de propriedades é usada para definir as redes entre os quais o gateway se encontra e as regras de solicitações de roteamento. 

#### <a name="source-and-destination-network"></a>Rede de origem e destino 

Cada gateway requer uma `sourceNetwork` e `destinationNetwork`. A rede de origem é definida como a rede em que seu aplicativo receberá as solicitações de entrada. Sua propriedade name sempre deve ser definida como "Abrir". A rede de destino é a rede que as solicitações são direcionadas. O valor de nome para este deve ser definido como o nome do recurso de rede do local do seu aplicativo (deve incluir uma referência completa para o recurso). Veja abaixo uma configuração de exemplo da aparência para uma implantação em uma rede chamada "myNetwork".

```json 
"properties": {
  "description": "Service Fabric Mesh Gateway",
  "sourceNetwork": {
    "name": "Open"
  },
  "destinationNetwork": {
    "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'myNetwork')]"
  },
  [...]
}
```

#### <a name="rules"></a>Regras 

Um gateway pode ter várias regras de roteamento que especificam como o tráfego de entrega será tratado. Uma regra de roteamento define a relação entre a porta de escuta e o ponto de extremidade de destino para um determinado aplicativo. Para regras de roteamento de TCP, há um mapeamento 1:1 entre a Porta:Ponto de extremidade. Para regras de roteamento de HTTP, você pode definir regras de roteamentos mais complexas que examinam o caminho da solicitação e, opcionalmente, cabeçalhos, para decidir como a solicitação será encaminhada. 

As regras de roteamento são especificadas em uma base por porta. Cada porta de entrada tem sua própria matriz de regras dentro da seção de propriedades de sua configuração de gateway. 

#### <a name="tcp-routing-rules"></a>Regras de roteamento TCP 

Uma regra de roteamento TCP consiste das seguintes propriedades: 
* `name` - referência à regra que pode ser qualquer cadeia de caracteres de sua escolha 
* `port` - porta na qual escutar solicitações de entrada 
* `destination` - especificação de ponto de extremidade que inclui `applicationName`, `serviceName`, e `endpointName`, para onde as solicitações precisam ser roteadas

Aqui está uma regra de roteamento de TCP de exemplo:

```json
"properties": {
  [...]
  "tcp": [
    {
      "name": "web",
      "port": 80,
      "destination": {
        "applicationName": "myApp",
        "serviceName": "myService",
        "endpointName": "myListener"
      }
    }
  ]
}
```


#### <a name="http-routing-rules"></a>Regras de roteamento HTTP 

Uma regra de roteamento HTTP consiste das seguintes propriedades: 
* `name` - referência à regra que pode ser qualquer cadeia de caracteres de sua escolha 
* `port` - porta na qual escutar solicitações de entrada 
* `hosts` - uma matriz de políticas que se aplicam a solicitações que chegam em vários "hosts" na porta especificada acima. Hosts são o conjunto de aplicativos e serviços que podem estar em execução na rede e podem atender a solicitações de entrada, ou seja, um aplicativo web. Políticas de host são interpretadas em ordem, portanto, você deve criar em níveis decrescente de especificidade
    * `name` -o nome DNS do host para o qual as seguintes regras de roteamentos são especificadas. Usar "*" aqui seria criar regras de roteamento para todos os hosts.
    * `routes` - uma matriz de políticas para este host específico
        * `match` - especificação da estrutura de solicitação de entrada para essa regra se aplicar, com base em um `path`
            * `path` - contém um `value` (URI de entrada), `rewrite` (como você deseja que a solicitação seja encaminhada) e um `type` (atualmente pode ser apenas "Prefixo")
            * `header` - é uma matriz opcional de valores de cabeçalhos para correspondência no cabeçalho da solicitação, se a solicitação corresponde à especificação de caminho (acima).
              * cada entrada contém `name` (nome de cadeia de caracteres do cabeçalho a corresponder), `value` (valor da cadeia de caracteres do cabeçalho na solicitação) e um `type` (atualmente pode ser apenas "Exato")
        * `destination` - se a solicitação corresponde, ela será roteada para este destino, que é especificado usando um `applicationName`, `serviceName` e `endpointName`

Aqui está uma regra de roteamento de HTTP de exemplo que se aplica a solicitações na porta 80, para todos os hosts atendidos por aplicativos nessa rede. Se a URL da solicitação tem uma estrutura que corresponde à especificação de caminho, ou seja, `<IPAddress>:80/pickme/<requestContent>`, em seguida, será direcionada para o `myListener` ponto de extremidade.  

```json
"properties": {
  [...]
  "http": [
    {
      "name": "web",
      "port": 80,
      "hosts": [
        {
          "name": "*",
          "routes": [
            {
              "match": {
                "path": {
                  "value": "/pickme",
                  "rewrite": "/",
                  "type": "Prefix"
                }
              },
              "destination": {
                "applicationName": "meshApp",
                "serviceName": "myService",
                "endpointName": "myListener"
              }
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="sample-config-for-a-gateway-resource"></a>Configuração de exemplo para um recurso de Gateway 

Aqui está a aparência de uma configuração de recurso completa do Gateway (isso é adaptado do exemplo de entrada disponível no [repositório de amostras da Malha](https://github.com/Azure-Samples/service-fabric-mesh/blob/2018-09-01-preview/templates/ingress/meshingress.linux.json)):

```json
{
  "apiVersion": "2018-09-01-preview",
  "name": "ingressGatewayLinux",
  "type": "Microsoft.ServiceFabricMesh/gateways",
  "location": "[parameters('location')]",
  "dependsOn": [
    "Microsoft.ServiceFabricMesh/networks/meshNetworkLinux"
  ],
  "properties": {
    "description": "Service Fabric Mesh Gateway for Linux mesh samples.",
    "sourceNetwork": {
      "name": "Open"
    },
    "destinationNetwork": {
      "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'meshNetworkLinux')]"
    },
    "http": [
      {
        "name": "web",
        "port": 80,
        "hosts": [
          {
            "name": "*",
            "routes": [
              {
                "match": {
                  "path": {
                    "value": "/hello",
                    "rewrite": "/",
                    "type": "Prefix"
                  }
                },
                "destination": {
                  "applicationName": "meshAppLinux",
                  "serviceName": "helloWorldService",
                  "endpointName": "helloWorldListener"
                }
              },
              {
                "match": {
                  "path": {
                    "value": "/counter",
                    "rewrite": "/",
                    "type": "Prefix"
                  }
                },
                "destination": {
                  "applicationName": "meshAppLinux",
                  "serviceName": "counterService",
                  "endpointName": "counterServiceListener"
                }
              }
            ]
          }
        ]
      }
    ]
  }
}
```

Esse gateway está configurado para um aplicativo do Linux, "meshAppLinux", que consiste em pelo menos dois serviços, "helloWorldService" e "counterService", que escuta na porta 80. Dependendo da estrutura da URL da solicitação de entrada, ele encaminhará a solicitação para um desses serviços. 
* "\<IPAddress >: 80/helloWorld/\<solicitação\>" resultaria em uma solicitação que está sendo direcionada para o "helloWorldListener" o helloWorldService. 
* "\<IPAddress >: 80/contador/\<solicitação\>" resultaria em uma solicitação que está sendo direcionada para o "counterListener" de counterService. 

## <a name="next-steps"></a>Próximas etapas
* Implantar o [Exemplo de entrada](https://github.com/Azure-Samples/service-fabric-mesh/tree/2018-09-01-preview/templates/ingress) para ver os gateways em ação
