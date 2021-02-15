---
title: Lógica de processamento de regra do Gerenciador de firewall do Azure
description: Saiba mais sobre a lógica de processamento de regra do Firewall do Azure
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 9184bf7baa85420e067edb4c0aafccb7e6711225
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86512173"
---
# <a name="azure-firewall-rule-processing-logic"></a>Lógica de processamento de regra do Firewall do Azure

O firewall do Azure tem regras NAT, regras de rede e regras de aplicativos. As regras são processadas de acordo com o tipo de regra.

## <a name="network-rules-and-applications-rules"></a>Regras de rede e regras de aplicativos

As regras de rede são aplicadas primeiro e, seguida, são aplicadas as regras de aplicativo. As regras estão sendo encerradas. Portanto, se uma correspondência for encontrada em regras de rede, as regras do aplicativo não serão processadas.  Se nenhuma regra de rede corresponder e se o protocolo de pacote for HTTP/HTTPS, o pacote será então avaliado pelas regras de aplicativo. Se ainda assim nenhuma correspondência for encontrada, o pacote será avaliado em relação à coleção de regras de infraestrutura. Se ainda não houver correspondência, o pacote será negado por padrão.

## <a name="nat-rules"></a>Regras de NAT

A conectividade de entrada pode ser habilitada configurando a DNAT (conversão de endereços de rede de destino) conforme descrito em [Tutorial: Filtrar o tráfego de entrada com DNAT de Firewall do Azure usando o portal do Azure](../firewall/tutorial-firewall-dnat.md). As regras de DNAT são aplicadas primeiro. Se uma correspondência for encontrada, será adicionada uma regra de rede correspondente implícita para permitir o tráfego convertido. Você pode substituir esse comportamento adicionando explicitamente uma coleção de regras de rede com regras de negação que correspondem ao tráfego convertido. Não há regras de aplicativo aplicadas para essas conexões.

## <a name="inherited-rules"></a>Regras herdadas

As coleções de regras de rede herdadas de uma política pai sempre são priorizadas acima das coleções de regras de rede que são definidas como parte de sua nova política. A mesma lógica também se aplica às coleções de regras de aplicativo. No entanto, as coleções de regras de rede são sempre processadas antes de coleções de regras de aplicativo, independentemente da herança.

Por padrão, sua política herda seu modo de inteligência contra ameaças da política pai. Você pode substituir isso definindo seu modo de inteligência contra ameaças para um valor diferente na página de configurações de política. Só é possível substituí-lo por um valor mais estrito. Por exemplo, se a política pai estiver definida como *alerta somente*, você poderá configurar essa política local para *alertar e negar*, mas não poderá desativá-la.

## <a name="next-steps"></a>Próximas etapas

- [Saiba mais sobre o Gerenciador de firewall do Azure](overview.md)