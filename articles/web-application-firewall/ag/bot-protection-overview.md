---
title: Visão geral da proteção contra bots do WAF no Gateway de Aplicativo do Azure
titleSuffix: Azure Web Application Firewall
description: Este artigo oferece uma visão geral na proteção contra bots do firewall do aplicativo Web (WAF) no Gateway de Aplicativo
services: web-application-firewall
author: winthrop28
ms.service: web-application-firewall
ms.date: 05/20/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: e01f9ac8966223e11ad218af7bf6fbb2462f28f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "83714892"
---
# <a name="azure-web-application-firewall-on-azure-application-gateway-bot-protection-overview"></a>Visão geral da proteção contra bots do firewall do aplicativo Web no Gateway de Aplicativo do Azure

Aproximadamente 20% de todo o tráfego da internet provém de bots ruins. Eles extraem, examinam e procuram por vulnerabilidades no seu aplicativo Web. Quando esses bots são bloqueados pelo firewall do aplicativo Web (WAF), eles não podem atacar você. Eles também não podem usar seus recursos e serviços, como seus back-ends e outra infraestrutura subjacente.

Você pode habilitar um conjunto de regras gerenciado de proteção contra bots para seu WAF bloquear ou registrar solicitações de endereços IP mal-intencionados conhecidos. Os endereços IP e os domínios são originados do feed de inteligência de ameaças da Microsoft. O Gráfico de Segurança Inteligente potencializa a inteligência contra ameaças da Microsoft e é usado por vários serviços, incluindo a Central de Segurança do Azure.

> [!IMPORTANT]
> O conjunto de regras de proteção contra bots está atualmente em versão prévia pública e é fornecido com um contrato de nível de serviço de versão prévia pública. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Veja os  [Termos de Uso Adicionais para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter detalhes.

## <a name="use-with-owasp-rulesets"></a>Usar com conjuntos de regras OWASP

Você pode usar o conjunto de regras de proteção contra bots juntamente com qualquer conjunto de regras OWASP (2.2.9, 3,0 e 3,1). Somente um conjunto de regras OWASP pode ser usado por vez. O conjunto de regras de proteção contra bots contém uma regra adicional que aparece no seu próprio conjunto de regras. Ele é intitulado **Microsoft_BotManagerRuleSet_0.1**, e você pode habilitá-lo ou desabilitá-lo como as outras regras OWASP.

![Conjunto de regras de bot](../media/bot-protection-overview/bot-ruleset.png)

## <a name="ruleset-update"></a>Atualização do conjunto de regras

A lista do conjunto de regras de mitigação de bots de endereços IP reconhecidamente ruins é atualizada várias vezes por dia pelo feed de Inteligência contra ameaças da Microsoft para acompanhar os bots. Os aplicativos Web são protegidos continuamente, mesmo que os vetores de ataque de bot sejam alterados.

## <a name="log-example"></a>Exemplo de log

Veja um exemplo de entrada de log para proteção contra bots:

```
{
        "timeStamp": "0000-00-00T00:00:00+00:00",
            "resourceId": "appgw",
            "operationName": "ApplicationGatewayFirewall",
            "category": "ApplicationGatewayFirewallLog",
            "properties": {
            "instanceId": "vm1",
                "clientIp": "1.2.3.4",
                "requestUri": "/hello.php?arg1=aaaaaaabccc",
                "ruleSetType": "MicrosoftBotProtection",
                "message": "IPReputationTriggered",
                "action": "Blocked",
                "hostname": "example.com",
                "transactionId": "abc",
                "policyId": "waf policy 1",
                "policyScope": "Global",
                "policyScopeName": "Default Policy",
                "engine": "Azwaf"
        }
    }
```

## <a name="next-steps"></a>Próximas etapas

- [Configurar a proteção contra bots para Firewall do aplicativo Web no Gateway de Aplicativo do Azure (versão prévia)](bot-protection.md)
