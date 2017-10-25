---
title: Como configurar o firewall do aplicativo Web - Gateway de Aplicativo do Azure | Microsoft Docs
description: Este artigo ensina a usar o firewall do aplicativo Web em um gateway de aplicativo novo ou existente.
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: 670b9732-874b-43e6-843b-d2585c160982
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/20/2017
ms.author: davidmu
ms.openlocfilehash: c9c740a3a1a28a1a9a4f2abf579fe2adb54e4f47
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configure-a-web-application-firewall-on-a-new-or-existing-application-gateway-with-azure-cli"></a>Como configurar o firewall do aplicativo Web em um gateway de aplicativo novo ou existente com a CLI do Azure

> [!div class="op_single_selector"]
> * [Portal do Azure](application-gateway-web-application-firewall-portal.md)
> * [PowerShell](application-gateway-web-application-firewall-powershell.md)
> * [CLI do Azure](application-gateway-web-application-firewall-cli.md)

Saiba como criar um gateway de aplicativo habilitado para firewall do aplicativo Web (WAF). Saiba também como adicionar um WAF a um gateway de aplicativo existente.

O WAF no Gateway de Aplicativo do Azure protege aplicativos Web contra ataques comuns baseados na Web, como injeção de SQL, ataques de scripts entre sites e sequestros de sessão.

 O Gateway de Aplicativo é um balanceador de carga Layer 7. Ele fornece o failover e solicitações HTTP de roteamento de desempenho entre diferentes servidores, estejam eles na nuvem ou no local. O Gateway de Aplicativo fornece muitos recursos do controlador de entrega de aplicativos (ADC):

 * Balanceamento de carga HTTP 
 * Afinidade de sessão baseada em cookie 
 * Descarregamento de protocolo SSL 
 * Investigações de integridade personalizadas 
 * Suporte para a funcionalidade multissite
 
 Para ver uma lista completa dos recursos com suporte, confira [Visão geral do gateway de aplicativo](application-gateway-introduction.md).

Este artigo mostra como [adicionar um firewall do aplicativo Web para um gateway de aplicativo existente](#add-web-application-firewall-to-an-existing-application-gateway). Ele também mostra como [Criar um gateway de aplicativo que usa um firewall do aplicativo web](#create-an-application-gateway-with-web-application-firewall).

![Imagem de cenário][scenario]

## <a name="prerequisite-install-the-azure-cli-20"></a>Pré-requisito: instalar a CLI do Azure 2.0

Para executar as etapas deste artigo, será necessário [instalar a interface de linha de comando do Azure (CLI do Azure) para Mac, Linux e Windows](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2).

## <a name="waf-configuration-differences"></a>Diferenças de configuração do WAF

Se você leu o artigo [Como criar um gateway de aplicativo com a CLI do Azure](application-gateway-create-gateway-cli.md), então compreenderá as configurações de SKU a serem definidas durante a criação de um gateway de aplicativo. O WAF fornece configurações adicionais para definir ao configurar a SKU em um Gateway de Aplicativo. Não há nenhuma alteração adicional feita no gateway de aplicativo em si.

| **Configuração** | **Detalhes**
|---|---|
|**SKU** |Um gateway de aplicativo normal sem WAF suporta os tamanhos **Padrão\_Pequeno**, **Padrão\_Médio** e **Padrão\_Grande**. Com a introdução do WAF, há duas SKUs adicionais, **WAF\_Médio** e **WAF\_Grande**. Não há suporte para WAF em gateways de aplicativos pequenos.|
|**Modo** | Essa configuração é o modo do WAF. Os valores permitidos são **Detecção** e **Prevenção**. Quando o WAF está configurado no modo de **Detecção**, todas as ameaças são armazenadas em um arquivo de log. No modo de **Prevenção**, os eventos ainda são registrados, mas o invasor recebe uma resposta: "403 não autorizado" do gateway de aplicativo.|

## <a name="add-a-web-application-firewall-to-an-existing-application-gateway"></a>Como adicionar um firewall do aplicativo Web a um gateway de aplicativo existente

O comando a seguir altera um gateway de aplicativo padrão existente para um gateway de aplicativo com WAF habilitado:

```azurecli-interactive
#!/bin/bash

az network application-gateway waf-config set \
  --enabled true \
  --firewall-mode Prevention \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG"
```

Esse comando atualiza o gateway de aplicativo com um WAF. Confira [Diagnósticos do Gateway de Aplicativo](application-gateway-diagnostics.md) para entender como exibir os logs do seu Gateway de Aplicativo. Devido à natureza de segurança do WAF, examine regularmente os logs para compreender a postura de segurança de seus aplicativos Web.

## <a name="create-an-application-gateway-with-a-web-application-firewall"></a>Como criar um gateway de aplicativo com um firewall do aplicativo Web

O comando a seguir cria um gateway de aplicativo com um WAF:

```azurecli-interactive
#!/bin/bash

az network application-gateway create \
  --name "AdatumAppGateway2" \
  --location "eastus" \
  --resource-group "AdatumAppGatewayRG" \
  --vnet-name "AdatumAppGatewayVNET2" \
  --vnet-address-prefix "10.0.0.0/16" \
  --subnet "Appgatewaysubnet2" \
  --subnet-address-prefix "10.0.0.0/28" \
 --servers "10.0.0.5 10.0.0.4" \
  --capacity 2 
  --sku "WAF_Medium" \
  --http-settings-cookie-based-affinity "Enabled" \
  --http-settings-protocol "Http" \
  --frontend-port "80" \
  --routing-rule-type "Basic" \
  --http-settings-port "80" \
  --public-ip-address "pip2" \
  --public-ip-address-allocation "dynamic" \
  --tags "cli[2] owner[administrator]"
```

> [!NOTE]
> Os gateways de aplicativos criados com a configuração básica do WAF são definidos com o CRS 3.0 para proteções.

## <a name="get-an-application-gateway-dns-name"></a>Como obter um nome DNS do gateway de aplicativo

Depois de criar o gateway, a próxima etapa é configurar o front-end para comunicação. Ao usar um IP público, o gateway de aplicativo requer um nome DNS atribuído dinamicamente, o que não é amigável. Para garantir que os usuários finais possam acessar o gateway de aplicativo, você pode usar um registro CNAME para apontar para o ponto de extremidade público do gateway de aplicativo. Para saber mais, confira [Como configurar um nome de domínio personalizado nos serviços de nuvem do Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). 

Para configurar um registro CNAME, recupere detalhes do gateway de aplicativo e seu nome DNS/IP associado usando o elemento PublicIPAddress anexado ao gateway de aplicativo. Use o nome DNS do gateway de aplicativo para criar um registro CNAME que aponte os dois aplicativos Web para esse nome DNS. Não recomendamos usar registros, pois o VIP pode ser alterado quando o gateway de aplicativo reiniciar.

```azurecli-interactive
#!/bin/bash

az network public-ip show \
  --name pip2 \
  --resource-group "AdatumAppGatewayRG"
```

```
{
  "dnsSettings": {
    "domainNameLabel": null,
    "fqdn": "8c786058-96d4-4f3e-bb41-660860ceae4c.cloudapp.net",
    "reverseFqdn": null
  },
  "etag": "W/\"3b0ac031-01f0-4860-b572-e3c25e0c57ad\"",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/publicIPAddresses/pip2",
  "idleTimeoutInMinutes": 4,
  "ipAddress": "40.121.167.250",
  "ipConfiguration": {
    "etag": null,
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway2/frontendIPConfigurations/appGatewayFrontendIP",
    "name": null,
    "privateIpAddress": null,
    "privateIpAllocationMethod": null,
    "provisioningState": null,
    "publicIpAddress": null,
    "resourceGroup": "AdatumAppGatewayRG",
    "subnet": null
  },
  "location": "eastus",
  "name": "pip2",
  "provisioningState": "Succeeded",
  "publicIpAddressVersion": "IPv4",
  "publicIpAllocationMethod": "Dynamic",
  "resourceGroup": "AdatumAppGatewayRG",
  "resourceGuid": "3c30d310-c543-4e9d-9c72-bbacd7fe9b05",
  "tags": {
    "cli[2] owner[administrator]": ""
  },
  "type": "Microsoft.Network/publicIPAddresses"
}
```

## <a name="next-steps"></a>Próximas etapas

Saiba como personalizar regras de WAF visitando: [Como personalizar as regras de firewall do aplicativo Web por meio da CLI 2.0 do Azure](application-gateway-customize-waf-rules-cli.md).

[scenario]: ./media/application-gateway-web-application-firewall-cli/scenario.png
