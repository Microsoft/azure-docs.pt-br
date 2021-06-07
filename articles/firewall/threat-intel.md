---
title: Filtragem baseada em inteligência contra ameaças do Firewall do Azure
description: A filtragem contra ameaças baseada em inteligência pode ser habilitada para o seu firewall de forma a alertar e rejeitar o tráfego de/para endereços IP e domínios mal-intencionados.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 05/12/2020
ms.author: victorh
ms.openlocfilehash: 77e36d5ab98f1177b5a0b89ee10b3e6bd22560c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791452"
---
# <a name="azure-firewall-threat-intelligence-based-filtering"></a>Filtragem baseada em inteligência contra ameaças do Firewall do Azure

A filtragem contra ameaças baseada em inteligência pode ser habilitada para o seu firewall de forma a alertar e rejeitar o tráfego de/para endereços IP e domínios mal-intencionados. Os endereços IP e os domínios são originados do feed de inteligência de ameaças da Microsoft. O [Grafo de Segurança Inteligente](https://www.microsoft.com/security/operations/intelligence) potencializa a inteligência contra ameaças da Microsoft e é usado por vários serviços, incluindo a Central de Segurança do Azure.<br>
<br>

:::image type="content" source="media/threat-intel/firewall-threat.png" alt-text="Inteligência contra ameaças do firewall" border="false":::

Se tiver habilitado a filtragem baseada em inteligência contra ameaças, as regras associadas serão processadas antes de qualquer uma das regras NAT, regras de rede ou regras de aplicativo.

Você pode optar por apenas registrar um alerta quando uma regra é disparada ou pode optar pelo modo de alerta e negação.

Por padrão, a filtragem baseada em inteligência contra ameaças está habilitada no modo de alerta. Não é possível desativar esse recurso ou alterar o modo até que a interface do portal esteja disponível em sua região.

:::image type="content" source="media/threat-intel/threat-intel-ui.png" alt-text="Interface do portal de filtragem baseada em inteligência contra ameaças":::

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

- Veja [Exemplos do Log Analytics do Firewall do Azure](./firewall-workbook.md)
- Saiba como [implantar e configurar um Firewall do Azure](tutorial-firewall-deploy-portal.md)
- Revise o [relatório de inteligência de segurança da Microsoft](https://www.microsoft.com/en-us/security/operations/security-intelligence-report)