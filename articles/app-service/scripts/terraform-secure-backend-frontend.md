---
title: 'Terraform: implantar o aplicativo Web front-end e o aplicativo Web de back-end conectado com segurança por meio da Integração VNET e do ponto de extremidade privado'
description: Saiba como usar o provedor Terraform no Serviço de Aplicativo para implantar dois aplicativos Web conectados por meio da Integração VNET e do ponto de extremidade privado
author: ericgre
ms.assetid: 3e5d1bbd-5581-40cc-8f65-bc74f1802156
ms.topic: sample
ms.date: 08/10/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: c1de8ebbd9ad381628cfeb19413baa295b42b3db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91739826"
---
# <a name="create-two-web-apps-connected-securely-with-private-endpoint-and-vnet-integration"></a>Criar dois aplicativos Web conectados com segurança por meio da Integração VNET e do ponto de extremidade privado

Este artigo ilustra um exemplo de uso do [ponto de extremidade privado](../networking/private-endpoint.md) e da [Integração VNET](../web-sites-integrate-with-vnet.md) regional para conectar dois aplicativos Web (front-end e back-end) com segurança seguindo estas etapas:
- Implantar uma VNET
- Criar a primeira sub-rede para a integração
- Criar a segunda sub-rede para o ponto de extremidade privado; é necessário definir um parâmetro específico para desabilitar as políticas de rede
- Implantar um Plano do Serviço de Aplicativo do tipo PremiumV2 ou PremiumV3, necessário para o recurso de Ponto de extremidade privado
- Criar o aplicativo Web front-end com algumas configurações de aplicativo específicas para consumir a zona DNS privada; [mais detalhes](../web-sites-integrate-with-vnet.md#azure-dns-private-zones)
- Conectar o aplicativo Web front-end à sub-rede de integração
- Criar o aplicativo Web de back-end
- Criar a zona privada DNS com o nome da zona de link privado para o aplicativo Web privatelink.azurewebsites.net
- Vincular essa zona à VNET
- Criar o ponto de extremidade privado para o aplicativo Web de back-end na sub-rede do ponto de extremidade e registrar nomes DNS (site e SCM) na zona privada DNS criada anteriormente

## <a name="how-to-use-terraform-in-azure"></a>Como usar o Terraform no Azure

Acesse a [documentação do Azure](/azure/developer/terraform/) para saber como usar o Terraform com o Azure.

## <a name="the-complete-terraform-file"></a>O arquivo completo do Terraform

Para usar esse arquivo, é necessário alterar a propriedade de nome para os recursos frontwebapp e backwebapp (o nome do aplicativo Web precisa ser um nome DNS exclusivo em todo o mundo). 

```hcl
provider "azurerm" {
  version = "~>2.0"
  features {}
}

resource "azurerm_resource_group" "rg" {
  name     = "appservice-rg"
  location = "francecentral"
}

resource "azurerm_virtual_network" "vnet" {
  name                = "vnet"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  address_space       = ["10.0.0.0/16"]
}

resource "azurerm_subnet" "integrationsubnet" {
  name                 = "integrationsubnet"
  resource_group_name  = azurerm_resource_group.rg.name
  virtual_network_name = azurerm_virtual_network.vnet.name
  address_prefixes     = ["10.0.1.0/24"]
  delegation {
    name = "delegation"
    service_delegation {
      name = "Microsoft.Web/serverFarms"
    }
  }
}

resource "azurerm_subnet" "endpointsubnet" {
  name                 = "endpointsubnet"
  resource_group_name  = azurerm_resource_group.rg.name
  virtual_network_name = azurerm_virtual_network.vnet.name
  address_prefixes     = ["10.0.2.0/24"]
  enforce_private_link_endpoint_network_policies = true
}

resource "azurerm_app_service_plan" "appserviceplan" {
  name                = "appserviceplan"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name

  sku {
    tier = "Premiumv2"
    size = "P1v2"
  }
}

resource "azurerm_app_service" "frontwebapp" {
  name                = "frontwebapp20200810"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  app_service_plan_id = azurerm_app_service_plan.appserviceplan.id

  app_settings = {
    "WEBSITE_DNS_SERVER": "168.63.129.16",
    "WEBSITE_VNET_ROUTE_ALL": "1"
  }
}

resource "azurerm_app_service_virtual_network_swift_connection" "vnetintegrationconnection" {
  app_service_id  = azurerm_app_service.frontwebapp.id
  subnet_id       = azurerm_subnet.integrationsubnet.id
}

resource "azurerm_app_service" "backwebapp" {
  name                = "backwebapp20200810"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  app_service_plan_id = azurerm_app_service_plan.appserviceplan.id
}

resource "azurerm_private_dns_zone" "dnsprivatezone" {
  name                = "privatelink.azurewebsites.net"
  resource_group_name = azurerm_resource_group.rg.name
}

resource "azurerm_private_dns_zone_virtual_network_link" "dnszonelink" {
  name = "dnszonelink"
  resource_group_name = azurerm_resource_group.rg.name
  private_dns_zone_name = azurerm_private_dns_zone.dnsprivatezone.name
  virtual_network_id = azurerm_virtual_network.vnet.id
}

resource "azurerm_private_endpoint" "privateendpoint" {
  name                = "backwebappprivateendpoint"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  subnet_id           = azurerm_subnet.endpointsubnet.id

  private_dns_zone_group {
    name = "privatednszonegroup"
    private_dns_zone_ids = [azurerm_private_dns_zone.dnsprivatezone.id]
  }

  private_service_connection {
    name = "privateendpointconnection"
    private_connection_resource_id = azurerm_app_service.backwebapp.id
    subresource_names = ["sites"]
    is_manual_connection = false
  }
}
```




## <a name="next-steps"></a>Próximas etapas


> [Saiba mais sobre como usar o Terraform no Azure](/azure/developer/terraform/)