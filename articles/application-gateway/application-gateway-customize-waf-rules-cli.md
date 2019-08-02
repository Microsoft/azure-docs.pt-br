---
title: Personalizar regras de firewall de aplicativo web no Gateway de aplicativo do Azure - CLI do Azure
description: Este artigo fornece informações sobre como personalizar regras de firewall de aplicativo Web no Gateway de Aplicativo com a CLI do Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 2/22/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 70377e6414b41669b6d3e991d24136a3cc4270be
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67618649"
---
# <a name="customize-web-application-firewall-rules-through-the-azure-cli"></a>Personalizar as regras de firewall de aplicativo Web por meio da CLI do Azure

O WAF (firewall de aplicativo Web) do Gateway de Aplicativo do Azure fornece proteção para aplicativos Web. Essas proteções são fornecidas pelo CRS (conjunto de regras principais) do OWASP (Open Web Application Security Project). Algumas regras podem causar falsos positivos e bloquear o tráfego real. Por esse motivo, o Gateway de Aplicativo possibilita que a capacidade personalize regras e grupos de regras. Para obter mais informações sobre os grupos de regras e as regras específicas, consulte a [Lista de regras e grupos de regras de CRS do firewall de aplicativo Web](application-gateway-crs-rulegroups-rules.md).

## <a name="view-rule-groups-and-rules"></a>Exibir grupos de regras e regras

O exemplo de código a seguir mostra como exibir regras e grupos de regras que podem ser configurados.

### <a name="view-rule-groups"></a>Exibir grupos de regras

O exemplo abaixo mostra como exibir os grupos de regras:

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --type OWASP
```

A saída a seguir é uma resposta truncada do exemplo anterior:

```json
[
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_3.0",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "REQUEST-910-IP-REPUTATION",
        "rules": null
      },
      ...
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  },
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_2.2.9",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
   "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "crs_20_protocol_violations",
        "rules": null
      },
      ...
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "2.2.9",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  }
]
```

### <a name="view-rules-in-a-rule-group"></a>Exibir regras em um grupo de regras

O exemplo a seguir mostra como exibir as regras em um grupo de regras especificado:

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --group "REQUEST-910-IP-REPUTATION"
```

A saída a seguir é uma resposta truncada do exemplo anterior:

```json
[
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_3.0",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "REQUEST-910-IP-REPUTATION",
        "rules": [
          {
            "description": "Rule 910011",
            "ruleId": 910011
          },
          ...
        ]
      }
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  }
]
```

## <a name="disable-rules"></a>Desabilitar regras

O exemplo a seguir desabilita as regras `910018` e `910017` em um gateway de aplicativo:

```azurecli-interactive
az network application-gateway waf-config set --resource-group AdatumAppGatewayRG --gateway-name AdatumAppGateway --enabled true --rule-set-version 3.0 --disabled-rules 910018 910017
```

## <a name="mandatory-rules"></a>Regras obrigatórias

A lista a seguir contém condições que causam o WAF bloquear a solicitação no modo de prevenção (no modo de detecção são registradas como exceções). Eles não podem ser configurados ou desabilitados:

* Falha ao analisar o corpo da solicitação resulta na solicitação que está sendo bloqueada, a menos que a inspeção de corpo está desativada (XML, JSON, dados de formulário)
* Comprimento de dados do corpo (e não há arquivos) de solicitação é maior do que o limite configurado
* Corpo (incluindo arquivos) é maior que o limite de solicitação
* Ocorreu um erro interno no mecanismo de WAF

Específico do CRS 3. x:

* Limite excedida de pontuação de anomalias de entrada

## <a name="next-steps"></a>Próximas etapas

Depois de configurar as regras desabilitadas, você pode aprender como exibir os logs de WAF. Para obter mais informações, consulte [Diagnósticos do Gateway de Aplicativo](application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
