---
title: Configuração de inteligência de ameaças do firewall do Azure
description: Saiba como configurar a filtragem baseada em inteligência contra ameaças para sua política de firewall do Azure para alertar e negar o tráfego de e para domínios e endereços IP mal-intencionados conhecidos.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: article
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 7ede1c917bb44dd31aa59855a0b7c83eb478700a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100651707"
---
# <a name="azure-firewall-threat-intelligence-configuration"></a>Configuração de inteligência de ameaças do firewall do Azure

A filtragem baseada em inteligência contra ameaças pode ser configurada para sua política de firewall do Azure para alertar e negar o tráfego de e para domínios e endereços IP mal-intencionados conhecidos. Os endereços IP e os domínios são originados do feed de inteligência de ameaças da Microsoft. O [Grafo de Segurança Inteligente](https://www.microsoft.com/security/operations/intelligence) potencializa a inteligência contra ameaças da Microsoft e é usado por vários serviços, incluindo a Central de Segurança do Azure.<br>

Se você tiver configurado a filtragem baseada em inteligência contra ameaças, as regras associadas serão processadas antes de qualquer uma das regras de NAT, regras de rede ou regras de aplicativo.

:::image type="content" source="media/threat-intelligence-settings/threat-intelligence-policy.png" alt-text="Política de inteligência contra ameaças":::

## <a name="threat-intelligence-mode"></a>Modo de inteligência contra ameaças

Você pode configurar a inteligência contra ameaças em um dos três modos descritos na tabela a seguir. Por padrão, a filtragem baseada em inteligência contra ameaças está habilitada no modo de alerta.

|Mode |Descrição  |
|---------|---------|
|`Off`     | O recurso de inteligência contra ameaças não está habilitado para o firewall. |
|`Alert only`     | Você receberá alertas de alta confiança para o tráfego que passa pelo firewall para ou de endereços IP e domínios mal-intencionados conhecidos. |
|`Alert and deny`     | O tráfego é bloqueado e você receberá alertas de alta confiança quando o tráfego for detectado, tentando passar pelo firewall para ou de domínios e endereços IP mal-intencionados conhecidos. |

> [!NOTE]
> O modo de inteligência contra ameaças é herdado de políticas pai para políticas filho. Uma política filho deve ser configurada com o mesmo ou um modo mais estrito do que a política pai.

## <a name="allowlist-addresses"></a>Endereços daList de permissão

A inteligência contra ameaças pode disparar falsos positivos e bloquear o tráfego que realmente é válido. Você pode configurar uma lista de endereços IP permitidos para que a inteligência contra ameaças não filtre nenhum dos endereços, intervalos ou sub-redes que você especificar.  

![Endereços daList de permissão](media/threat-intelligence-settings/allow-list.png)

Você pode atualizar alist de permissão com várias entradas de uma vez carregando um arquivo CSV. O arquivo CSV só pode conter endereços IP e intervalos. O arquivo não pode conter títulos.

> [!NOTE]
> Os endereços da lista de permissões de inteligência contra ameaças são herdados de políticas pai para políticas filho. Qualquer endereço IP ou intervalo adicionado a uma política pai também será aplicado a todas as políticas filho.

## <a name="logs"></a>Logs

O trecho de log a seguir mostra uma regra disparada para o tráfego de saída para um site mal-intencionado:

```json
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
