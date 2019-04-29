---
title: Hospedagem de vários sites no Gateway de Aplicativo do Azure
description: Este artigo fornece uma visão geral do suporte a vários sites do Gateway de Aplicativo do Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
origin.date: 01/17/2019
ms.date: 04/16/2019
ms.author: v-junlch
ms.topic: conceptual
ms.openlocfilehash: 335545f86c9c23feefb6ac21ca9cc5c8fcb5e7fb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60715831"
---
# <a name="application-gateway-multiple-site-hosting"></a>Hospedagem de vários sites do Gateway de Aplicativo

A hospedagem de vários sites permite que você configure mais de um aplicativo Web na mesma instância do Application Gateway. Esse recurso permite que você configure a topologia mais eficiente para suas implantações adicionando até 100 sites a um Gateway de Aplicativo. Cada site pode ser direcionado para seu próprio pool de back-end. No exemplo a seguir, o Application Gateway está fornecendo o tráfego para contoso.com e fabrikam.com de dois pools de servidores de back-end chamados ContosoServerPool e FabrikamServerPool.

![imageURLroute](./media/multiple-site-overview/multisite.png)

> [!IMPORTANT]
> As regras são processadas na ordem em que elas são listadas no portal. É altamente recomendável configurar primeiro os ouvintes de vários locais para configurar um ouvinte básico.  Isso irá garantir que o tráfego seja roteado para o back-end correto. Se um ouvinte básico for listado primeiro e corresponder a uma solicitação de entrada, ele é processado por esse ouvinte.

As solicitações de http://contoso.com são encaminhadas para ContosoServerPool, e as de http://fabrikam.com são encaminhadas para FabrikamServerPool.

Da mesma forma, dois subdomínios do mesmo domínio pai podem ser hospedados na mesma implantação do Gateway de Aplicativo. Exemplos de uso de subdomínios podem incluir http://blog.contoso.com e http://app.contoso.com hospedados em uma implantação de gateway de aplicativo único.

## <a name="host-headers-and-server-name-indication-sni"></a>Cabeçalhos de host e SNI (Indicação de Nome de Servidor)

Existem três mecanismos comuns para habilitar a hospedagem de vários sites na mesma infraestrutura.

1. Hospede vários aplicativos Web, cada um em um endereço IP exclusivo.
2. Use o nome de host para hospedar vários aplicativos Web no mesmo endereço IP.
3. Use portas diferentes para hospedar vários aplicativos Web no mesmo endereço IP.

No momento, um Application Gateway obtém um único endereço IP público que escuta o tráfego. Portanto, o suporte a vários aplicativos, cada um com seu próprio endereço IP, não tem suporte no momento. O Gateway de Aplicativo dá suporte à hospedagem de vários aplicativos, cada um ouvindo em portas diferentes, mas esse cenário exige que os aplicativos aceitem o tráfego em portas não padrão e normalmente não é uma configuração desejada. O Gateway de Aplicativo depende de cabeçalhos de host HTTP 1.1 para hospedar mais de um site na mesma porta e endereço IP público. Os sites hospedados no Application Gateway também podem dar suporte ao descarregamento SSL com a extensão TLS de SNI (Indicação de Nome de Servidor). Esse cenário significa que o farm da Web de back-end e o navegador cliente devem dar suporte à extensão TLS e HTTP/1.1 conforme definido no RFC 6066.

## <a name="listener-configuration-element"></a>Elemento de configuração do ouvinte

O elemento de configuração HTTPListener existente é aprimorado para dar suporte aos elementos de indicação de nome de servidor e nome de host que são usados pelo Gateway de Aplicativo para rotear o tráfego para o pool de back-end adequado. O exemplo de código a seguir é o snippet de código do elemento HttpListeners do arquivo de modelo.

```json
"httpListeners": [
    {
        "name": "appGatewayHttpsListener1",
        "properties": {
            "FrontendIPConfiguration": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/DefaultFrontendPublicIP"
            },
            "FrontendPort": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort443'"
            },
            "Protocol": "Https",
            "SslCertificate": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/sslCertificates/appGatewaySslCert1'"
            },
            "HostName": "contoso.com",
            "RequireServerNameIndication": "true"
        }
    },
    {
        "name": "appGatewayHttpListener2",
        "properties": {
            "FrontendIPConfiguration": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/appGatewayFrontendIP'"
            },
            "FrontendPort": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort80'"
            },
            "Protocol": "Http",
            "HostName": "fabrikam.com",
            "RequireServerNameIndication": "false"
        }
    }
],
```

Você pode visitar [Modelo do Resource Manager usando a hospedagem de vários sites](https://github.com/Azure/azure-quickstart-templates/blob/master/201-application-gateway-multihosting) para encontrar uma implantação baseada em modelo de ponta a ponta.

## <a name="routing-rule"></a>Regra de roteamento

Não há nenhuma alteração necessária na regra de roteamento. A regra de roteamento 'Básica' deve continuar a ser escolhido para ligar o ouvinte do site apropriado ao pool de endereços de back-end correspondente.

```json
"requestRoutingRules": [
{
    "name": "<ruleName1>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpsListener1')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/ContosoServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }
    }

},
{
    "name": "<ruleName2>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpListener2')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/FabrikamServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }
    }

}
]
```

## <a name="next-steps"></a>Próximas etapas

Depois de conhecer várias opções de hospedagem de site, vá para [criar um Application Gateway usando a hospedagem de vários sites](tutorial-multiple-sites-powershell.md) para criar um Application Gateway com capacidade de dar suporte a mais de um aplicativo Web.


<!-- Update_Description: update metedata properties -->