---
title: "Visão geral do Provedor de recursos de rede | Microsoft Docs"
description: Saiba mais sobre o novo Provedor de recursos de rede no Gerenciador de Recursos do Azure
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
ms.assetid: 79bf09da-4809-45cb-8d21-705616ef24dc
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.openlocfilehash: 2428c707ddeed281fddd1e57bc5574603f0b9b1c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="network-resource-provider"></a>Provedor de recursos de rede
Uma necessidade básica para sucesso nos negócios de hoje é a capacidade de compilar e gerenciar aplicativos com reconhecimento de rede de grande escala de uma maneira ágil, flexível, segura e repetível. O Azure Resource Manager permite criar tais aplicativos como uma única coleção de recursos nos grupos de recursos. Esses recursos são gerenciados por meio de vários provedores de recursos no Resource Manager.

O Gerenciador de Recursos do Azure utiliza diferentes provedores de recursos para fornecer acesso a seus recursos. Existem três provedores de recurso principais: Rede, Armazenamento e Computação. Este documento aborda as características e os benefícios do Provedor de Recursos de Rede, incluindo:

* **Metadados** – você pode adicionar informações aos recursos usando marcas. Essas marcas podem ser usadas para controlar a utilização de recursos em grupos de recursos e assinaturas.
* **Maior controle da sua rede** - recursos de rede são flexíveis e você pode controlá-los de forma mais granular. Isso significa que você tem mais flexibilidade no gerenciamento de recursos de rede.
* **Configuração mais rápida** - já que os recursos de rede são agrupados de modo menos rígido, você pode criar e organizar os recursos de rede em paralelo. Isso reduziu drasticamente o tempo de configuração.
* **Controle de acesso com base em função** - o RBAC fornece funções padrão, com escopo de segurança específico, além de permitir a criação de funções personalizadas para gerenciamento seguro.
* **Gerenciamento e implantação simplificados** - é mais fácil implantar e gerenciar aplicativos, já que você pode criar uma pilha com todo o aplicativo como uma única coleção de recursos em um grupo de recursos. E isso agiliza a implantação, pois você pode implantar simplesmente fornecendo uma carga JSON de modelo.
* **Personalização rápida** - você pode usar modelos de estilo declarativo para habilitar a personalização repetível e rápida de implantações.
* **Personalização repetível** - você pode usar modelos de estilo declarativo para habilitar a personalização repetível e rápida de implantações.
* **Interfaces de gerenciamento** - você pode usar qualquer uma das seguintes interfaces para gerenciar seus recursos:
  * API baseada em REST
  * PowerShell
  * SDK .NET
  * SDK do Node.JS
  * Java SDK
  * CLI do Azure
  * Portal de Visualização
  * Linguagem de modelo do Resource Manager

## <a name="network-resources"></a>Recursos de rede
Agora você pode gerenciar recursos de rede de modo independente, em vez de ter todos eles gerenciados por meio de um recurso de computação único (uma máquina virtual). Isso garante um maior grau de flexibilidade e agilidade em redigir uma infraestrutura complexa e em grande escala em um grupo de recursos.

Uma exibição conceitual de uma implantação de exemplo envolvendo um aplicativo de várias camadas é apresentado a seguir. Cada recurso que você vê, como NICs, endereços IP públicos e VMs, pode ser gerenciado independentemente.

![Modelo de recursos de rede](./media/resource-groups-networking/Figure2.png)

Cada recurso contém um conjunto comum de propriedades e seu conjunto de propriedades individuais. As propriedades comuns são:

| Propriedade | Descrição | Valores de exemplo |
| --- | --- | --- |
| **name** |Nome de recurso exclusivo. Cada tipo de recurso tem suas próprias restrições de nomenclatura. |PIP01, VM01, NIC01 |
| **local** |Região do Azure na qual o recurso reside |westus, eastus |
| **ID** |Identificação baseada em URI exclusivo |/subscriptions/<subGUID>/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestPIP |

Você pode verificar as propriedades individuais de recursos nas seções abaixo.

[!INCLUDE [virtual-networks-nrp-pip-include](../../includes/virtual-networks-nrp-pip-include.md)]

[!INCLUDE [virtual-networks-nrp-nic-include](../../includes/virtual-networks-nrp-nic-include.md)]

[!INCLUDE [virtual-networks-nrp-nsg-include](../../includes/virtual-networks-nrp-nsg-include.md)]

[!INCLUDE [virtual-networks-nrp-udr-include](../../includes/virtual-networks-nrp-udr-include.md)]

[!INCLUDE [virtual-networks-nrp-vnet-include](../../includes/virtual-networks-nrp-vnet-include.md)]

[!INCLUDE [virtual-networks-nrp-dns-include](../../includes/virtual-networks-nrp-dns-include.md)]

[!INCLUDE [virtual-networks-nrp-lb-include](../../includes/virtual-networks-nrp-lb-include.md)]

[!INCLUDE [virtual-networks-nrp-appgw-include](../../includes/virtual-networks-nrp-appgw-include.md)]

[!INCLUDE [virtual-networks-nrp-vpn-include](../../includes/virtual-networks-nrp-vpn-include.md)]

[!INCLUDE [virtual-networks-nrp-tm-include](../../includes/virtual-networks-nrp-tm-include.md)]

## <a name="management-interfaces"></a>Interfaces de gerenciamento
Você pode gerenciar os recursos de rede do Azure usando interfaces diferentes. Neste documento, vamos nos concentrar em duas dessas interfaces: API REST e modelos.

### <a name="rest-api"></a>API REST
Como mencionado anteriormente, os recursos de rede podem ser gerenciados por meio de uma variedade de interfaces, inclusive API REST, o SDK do .NET, SDK do Node.JS, Java SDK, PowerShell, CLI, Portal do Azure e modelos.

A API Rest está de acordo com a especificação do protocolo HTTP 1.1. A estrutura geral do URI da API é apresentada abaixo:

    https://management.azure.com/subscriptions/{subscription-id}/providers/{resource-provider-namespace}/locations/{region-location}/register?api-version={api-version}

E os parâmetros entre chaves representam os elementos a seguir:

* **subscription-id** - a ID de sua assinatura do Azure.
* **resource-provider-namespace** - namespace para o provedor que está sendo usado. O valor para o provedor de recursos de rede é *Microsoft.Network*.
* **region-name** - o nome da região do Azure

Os métodos HTTP a seguir têm suporte ao fazer chamadas para a API REST:

* **PUT** - usado para criar um recurso de um determinado tipo, modificar uma propriedade de recurso ou alterar uma associação entre os recursos.
* **GET** - usado para recuperar informações de um recurso provisionado.
* **DELETE** - usado para excluir um recurso existente.

Ambas a solicitação e a resposta têm conformidade com um formato de carga JSON. Para obter mais detalhes, consulte [APIs de Gerenciamento de Recursos do Azure](https://msdn.microsoft.com/library/azure/dn948464.aspx).

### <a name="resource-manager-template-language"></a>Linguagem de modelo do Resource Manager
Além de gerenciar recursos de modo imperativo (por APIs ou SDK), você também pode usar um estilo de programação declarativo para criar e gerenciar recursos de rede usando a Linguagem de Modelo do Resource Manager.

Uma representação de um modelo de exemplo é fornecida abaixo -

    {
      "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
      "contentVersion": "<version-number-of-template>",
      "parameters": { <parameter-definitions-of-template> },
      "variables": { <variable-definitions-of-template> },
      "resources": [ { <definition-of-resource-to-deploy> } ],
      "outputs": { <output-of-template> }    
    }

O modelo é basicamente uma descrição de JSON os recursos e os valores de instância injetados por meio de parâmetros. O exemplo a seguir pode ser usado para criar uma rede virtual com 2 sub-redes.

    {
        "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/VNET.json",
        "contentVersion": "1.0.0.0",
        "parameters" : {
          "location": {
            "type": "String",
            "allowedValues": ["East US", "West US", "West Europe", "East Asia", "South East Asia"],
            "metadata" : {
              "Description" : "Deployment location"
            }
          },
          "virtualNetworkName":{
            "type" : "string",
            "defaultValue":"myVNET",
            "metadata" : {
              "Description" : "VNET name"
            }
          },
          "addressPrefix":{
            "type" : "string",
            "defaultValue" : "10.0.0.0/16",
            "metadata" : {
              "Description" : "Address prefix"
            }

          },
          "subnet1Name": {
            "type" : "string",
            "defaultValue" : "Subnet-1",
            "metadata" : {
              "Description" : "Subnet 1 Name"
            }
          },
          "subnet2Name": {
            "type" : "string",
            "defaultValue" : "Subnet-2",
            "metadata" : {
              "Description" : "Subnet 2 name"
            }
          },
          "subnet1Prefix" : {
            "type" : "string",
            "defaultValue" : "10.0.0.0/24",
            "metadata" : {
              "Description" : "Subnet 1 Prefix"
            }
          },
          "subnet2Prefix" : {
            "type" : "string",
            "defaultValue" : "10.0.1.0/24",
            "metadata" : {
              "Description" : "Subnet 2 Prefix"
            }
          }
        },
        "resources": [
        {
          "apiVersion": "2015-05-01-preview",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "[parameters('virtualNetworkName')]",
          "location": "[parameters('location')]",
          "properties": {
            "addressSpace": {
              "addressPrefixes": [
                "[parameters('addressPrefix')]"
              ]
            },
            "subnets": [
              {
                "name": "[parameters('subnet1Name')]",
                "properties" : {
                  "addressPrefix": "[parameters('subnet1Prefix')]"
                }
              },
              {
                "name": "[parameters('subnet2Name')]",
                "properties" : {
                  "addressPrefix": "[parameters('subnet2Prefix')]"
                }
              }
            ]
          }
        }
        ]
    }

Você tem a opção de fornecer os valores de parâmetro manualmente ao usar um modelo, ou então pode usar um arquivo de parâmetro. O exemplo a seguir mostra um conjunto possível de valores de parâmetro a ser usado com o modelo acima:

    {
      "location": {
          "value": "East US"
      },
      "virtualNetworkName": {
          "value": "VNET1"
      },
      "subnet1Name": {
          "value": "Subnet1"
      },
      "subnet2Name": {
          "value": "Subnet2"
      },
      "addressPrefix": {
          "value": "192.168.0.0/16"
      },
      "subnet1Prefix": {
          "value": "192.168.1.0/24"
      },
      "subnet2Prefix": {
          "value": "192.168.2.0/24"
      }
    }


As principais vantagens de usar modelos são:

* Você pode criar uma infraestrutura complexa em um grupo de recursos em um estilo declarativo. A orquestração de criação de recursos, incluindo gerenciamento de dependências, é manipulada pelo Resource Manager.
* A infraestrutura pode ser criada de forma repetida por várias regiões e dentro de uma região, simplesmente alterando os parâmetros.
* O estilo declarativo resulta em menor tempo de avanço na criação de modelos e implantação da infraestrutura.

Para exemplos de modelo, consulte [Modelos de início rápido do Azure](https://github.com/Azure/azure-quickstart-templates).

Para obter mais informações sobre a Linguagem de Modelo do Resource Manager, confira [Linguagem de Modelo do Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

O modelo de exemplo acima usa a rede virtual e recursos de sub-rede. Há outros recursos de rede, que você pode usar, conforme listado abaixo:

### <a name="using-a-template"></a>Criação de um modelo
Você pode implantar serviços no Azure de um modelo usando o PowerShell, AzureCLI ou apenas clicando para implantar no GitHub. Para implantar os serviços de um modelo no GitHub, execute as etapas a seguir:

1. Abra o arquivo template3 no GitHub. Por exemplo, abra a rede Virtual do [com duas sub-redes](https://github.com/Azure/azure-quickstart-templates/tree/master/101-virtual-network).
2. Clique em **implantar no Azure**e entre no portal do Azure com suas credenciais.
3. Verifique o modelo e, em seguida, clique em **Salvar**.
4. Clique em **Editar parâmetros** e selecione um local, como *Oeste dos EUA*, para o vnet e sub-redes.
5. Se necessário, altere os parâmetros **ADDRESSPREFIX** e **SUBNETPREFIX** e clique em **OK**.
6. Clique em **Selecionar um grupo de recursos** e, em seguida, clique no grupo de recursos ao qual você deseja adicionar o vnet e as sub-redes. Como alternativa, você pode criar um novo grupo de recursos clicando **Ou criar novos**.
7. Clique em **Criar**. Observe a exibição lado a lado **Implantação de modelo de provisionamento**. Quando a implantação estiver concluída, você verá uma tela semelhante à tela abaixo.

![Implantação do modelo de exemplo](./media/resource-groups-networking/Figure6.png)

## <a name="next-steps"></a>Próximas etapas
[Idioma de modelo do Gerenciador de Recursos do Azure](../azure-resource-manager/resource-group-authoring-templates.md)

[Rede do Azure - modelos usados frequentemente](https://github.com/Azure/azure-quickstart-templates)

[Azure Resource Manager vs implantação clássica](../azure-resource-manager/resource-manager-deployment-model.md)

[Visão geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)

