---
title: Configuração de inteligência de ameaças do firewall do Azure
description: Saiba como configurar a filtragem baseada em inteligência contra ameaças para sua política de firewall do Azure para alertar e negar o tráfego de e para domínios e endereços IP mal-intencionados conhecidos.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: article
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: a663c5f3bcf3492c4a9bc74fe93c6ed6a86137ee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90530634"
---
# <a name="azure-firewall-threat-intelligence-configuration"></a>Configuração de inteligência de ameaças do firewall do Azure

A filtragem baseada em inteligência contra ameaças pode ser configurada para sua política de firewall do Azure para alertar e negar o tráfego de e para domínios e endereços IP mal-intencionados conhecidos. Os endereços IP e os domínios são originados do feed de inteligência de ameaças da Microsoft. O [Grafo de Segurança Inteligente](https://www.microsoft.com/security/operations/intelligence) potencializa a inteligência contra ameaças da Microsoft e é usado por vários serviços, incluindo a Central de Segurança do Azure.<br>

Se você tiver configurado a filtragem baseada em inteligência contra ameaças, as regras associadas serão processadas antes de qualquer uma das regras de NAT, regras de rede ou regras de aplicativo.

:::image type="content" source="media/threat-intelligence-settings/threat-intelligence-policy.png" alt-text="Política de inteligência contra ameaças":::

## <a name="threat-intelligence-mode"></a>Modo de inteligência contra ameaças

Você pode optar por registrar apenas um alerta quando uma regra é disparada ou pode escolher o modo de alerta e de negação.

Por padrão, a filtragem baseada em inteligência contra ameaças está habilitada no modo de alerta.

## <a name="allowed-list-addresses"></a>Endereços de lista permitidos

Você pode configurar uma lista de endereços IP permitidos para que a inteligência contra ameaças não filtre nenhum dos endereços, intervalos ou sub-redes que você especificar.



## <a name="logs"></a>Logs

O trecho de log a seguir mostra uma regra disparada:

```
{
    "category": "AzureFirewallNetworkRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallThreatIntelLog",
    "properties": {
         "msg": "HTTP request from 10.0.0.5:54074 to somemaliciousdomain.com:80. Action: Alert. ThreatIntel: Bot Networks"
    }
}
```

## <a name="testing"></a>Testando

- **Teste de saída** – Alertas de tráfego de saída devem ser uma ocorrência rara, pois isso significa que seu ambiente foi comprometido. Para ajudar a testar se os alertas de saída estão funcionando, um FQDN de teste foi criado para disparar um alerta. Use **testmaliciousdomain.eastus.cloudapp.azure.com** para seus testes de saída.

- **Teste de entrada** – É normal ver alertas de tráfego de entrada se regras DNAT estiverem configuradas no firewall. Isso ocorre mesmo se apenas fontes específicas forem permitidas na regra DNAT e o resto do tráfego for negado. O Firewall do Azure não alerta todos os scanners de porta conhecidos; apenas os scanners envolvidos em atividades mal-intencionadas.

## <a name="next-steps"></a>Próximas etapas

- Revise o [relatório de inteligência de segurança da Microsoft](https://www.microsoft.com/en-us/security/operations/security-intelligence-report)