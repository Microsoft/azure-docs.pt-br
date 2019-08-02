---
title: Criar um Azure Load Balancer usando a API REST
titlesuffix: Azure Load Balancer
description: Saiba como criar um Azure Load Balancer usando a API REST.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: load-balancer
ms.date: 06/06/2018
ms.author: allensu
ms.openlocfilehash: ae8fb4494d27d0c145963c9b32757bdb802e0cc7
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68275551"
---
# <a name="create-an-azure-basic-load-balancer-using-rest-api"></a>Criar um Azure Load Balancer básico usando a API REST

Um Azure Load Balancer distribui novos fluxos de entrada que chegam ao front-end do balanceador de carga para instâncias de pool de back-end, de acordo com as regras e as investigações de integridade. O Load Balancer está disponível em dois SKUs: Básico e Standard. Para compreender a diferença entre as duas versões do SKU, confira [Comparações de SKUs do Load Balancer](load-balancer-overview.md#skus).
 
Estas instruções mostram como criar um Azure Load Balancer básico usando a [API REST do Azure](/rest/api/azure/) para ajudar com o balanceamento de carga de solicitações recebidas entre várias VMs em uma rede virtual do Azure. A documentação de referência completa e exemplos adicionais estão disponíveis na [Referência de REST do Azure Load Balancer](/rest/api/load-balancer/).
 
## <a name="build-the-request"></a>Criar a solicitação
Use a seguinte solicitação HTTP PUT para criar um novo Azure Load Balancer Básico.
 ```HTTP
  PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}?api-version=2018-02-01
  ```
### <a name="uri-parameters"></a>Parâmetros de URI

|Nome  |No  |Obrigatório |Tipo |DESCRIÇÃO |
|---------|---------|---------|---------|--------|
|subscriptionId   |  path       |  verdadeiro       |   cadeia de caracteres      |  As credenciais de assinatura que identificam exclusivamente a assinatura do Microsoft Azure. A ID da assinatura faz parte do URI para cada chamada de serviço.      |
|resourceGroupName     |     path    | verdadeiro        |  cadeia de caracteres       |   O nome do grupo de recursos.     |
|loadBalancerName     |  path       |      verdadeiro   |    cadeia de caracteres     |    O nome do balanceador de carga.    |
|api-version    |   query     |  verdadeiro       |     cadeia de caracteres    |  Versão de API do cliente.      |



### <a name="request-body"></a>Corpo da solicitação

O único parâmetro necessário é `location`. Se você não definir a versão do *SKU*, um Load Balancer Básico será criado por padrão.  Use [parâmetros opcionais](https://docs.microsoft.com/rest/api/load-balancer/loadbalancers/createorupdate#request-body) para personalizar o balanceador de carga.

| Nome | Tipo | DESCRIÇÃO |
| :--- | :--- | :---------- |
| location | cadeia de caracteres | Local do recurso. Obtenha uma lista atual de locais usando a operação [Listar locais](https://docs.microsoft.com/rest/api/resources/subscriptions/listlocations). |


## <a name="example-create-and-update-a-basic-load-balancer"></a>Exemplo: criar e atualizar um Load Balancer Básico

Neste exemplo, primeiro você cria um Load Balancer Básico juntamente com seus recursos. Em seguida, você configura os recursos do balanceador de carga que incluem uma configuração de IP de front-end, um pool de endereços de back-end, uma regra de balanceamento de carga, uma investigação de integridade e uma regra NAT de entrada.

Antes de criar um balanceador de carga usando o exemplo a seguir, crie uma rede virtual chamada *vnetlb* com uma sub-rede chamada *subnetlb* em um grupo de recursos chamado *rg1* no local **Leste dos EUA**.

### <a name="step-1-create-a-basic-load-balancer"></a>ETAPA 1. Criar o Load Balancer Básico
Nesta etapa, você cria um Load Balancer Básico chamado *lb* no local **LESTE DOS EUA** no grupo de recursos *rg1*.
#### <a name="sample-request"></a>Solicitação de exemplo

  ```HTTP    
  PUT https://management.azure.com/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb?api-version=2018-02-01
  ```
#### <a name="request-body"></a>Corpo da solicitação

  ```JSON
   {
    "location": "eastus",
   }
  ```
### <a name="step-2-configure-load-balancer-resources"></a>ETAPA 2. Configurar recursos do balanceador de carga
Nesta etapa, você configura os recursos do balanceador de carga *lb* que incluem uma configuração de IP de front-end (*fe-lb*), um pool de endereços de back-end (*be-lb*), uma regra de balanceamento de carga (*rulelb*), uma investigação de integridade (*probe-lb*) e uma regra NAT de entrada (*in-nat-rule*).
#### <a name="sample-request"></a>Solicitação de exemplo

  ```HTTP    
  PUT https://management.azure.com/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb?api-version=2018-02-01
  ```
#### <a name="request-body"></a>Corpo da solicitação

  ```JSON
{
  "properties": {
    "frontendIPConfigurations": [
      {
        "name": "fe-lb",
        "properties": {
          "subnet": {
            "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/virtualNetworks/vnetlb/subnets/subnetlb"
          },
          "loadBalancingRules": [
            {
              "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/loadBalancingRules/rulelb"
            }  ],
          "inboundNatRules": [
            {
              "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/inboundNatRules/in-nat-rule"
            }  ]  }  }  ],
    "backendAddressPools": [
      {
        "name": "be-lb",
        "properties": {
          "loadBalancingRules": [
            {
              "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/loadBalancingRules/rulelb"
            }  ]   }   }   ],
    "loadBalancingRules": [
      {
        "name": "rulelb",
        "properties": {
          "frontendIPConfiguration": {
            "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/frontendIPConfigurations/fe-lb"
          },
          "frontendPort": 80,
          "backendPort": 80,
          "enableFloatingIP": true,
          "idleTimeoutInMinutes": 15,
          "protocol": "Tcp",
          "loadDistribution": "Default",
          "backendAddressPool": {
            "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/backendAddressPools/be-lb"
          },
          "probe": {
            "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/probes/probe-lb"
          }  }  }  ],
    "probes": [
      {
        "name": "probe-lb",
        "properties": {
          "protocol": "Http",
          "port": 80,
          "requestPath": "healthcheck.aspx",
          "intervalInSeconds": 15,
          "numberOfProbes": 2,
          "loadBalancingRules": [
            {
              "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/loadBalancingRules/rulelb"
            }  ]  }  } ],
    "inboundNatRules": [
      {
        "name": "in-nat-rule",
        "properties": {
          "frontendIPConfiguration": {
            "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/frontendIPConfigurations/fe-lb"
          },
          "frontendPort": 3389,
          "backendPort": 3389,
          "enableFloatingIP": true,
          "idleTimeoutInMinutes": 15,
          "protocol": "Tcp"
        } } ],
    "inboundNatPools": [],
    "outboundNatRules": []
  }  }
```
