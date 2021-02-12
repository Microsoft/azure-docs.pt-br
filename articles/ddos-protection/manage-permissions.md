---
title: Permissões do plano de proteção contra DDoS do Azure
description: Saiba como gerenciar a permissão em um plano de proteção.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: df53062c7c897493a47d88ea2873f9710b9825bf
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2021
ms.locfileid: "99806249"
---
# <a name="manage-ddos-protection-plans-permissions-and-restrictions"></a>Gerenciar planos de proteção contra DDoS: permissões e restrições

Um plano de proteção contra DDoS funciona em regiões e assinaturas. O mesmo plano pode ser vinculado a redes virtuais de outras assinaturas em regiões diferentes, em seu locatário. A assinatura do plano está associada a gerar a fatura mensal recorrente para o plano, bem como encargos excedentes, caso o número de endereços IP públicos protegidos exceder 100. Para obter mais informações sobre os preços de DDoS, consulte [Detalhes do preço](https://azure.microsoft.com/pricing/details/ddos-protection/).

## <a name="prerequisites"></a>Pré-requisitos

- Antes de concluir as etapas deste tutorial, você deve primeiro criar um plano de [proteção padrão do DDoS do Azure](manage-ddos-protection.md).

## <a name="permissions"></a>Permissões

Para trabalhar com os planos de proteção DDoS, sua conta deve ser atribuída à função de [colaborador da rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma função [personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) à qual são atribuídas as ações apropriadas listadas na tabela a seguir:

| Ação                                            | Nome                                     |
| ---------                                         | -------------                            |
| Microsoft.Network/ddosProtectionPlans/read        | Ler um plano de proteção DDoS              |
| Microsoft.Network/ddosProtectionPlans/write       | Criar ou atualizar um plano de proteção DDoS  |
| Microsoft.Network/ddosProtectionPlans/delete      | Excluir um plano de proteção contra DDoS            |
| Microsoft.Network/ddosProtectionPlans/join/action | Participar de um plano de proteção DDoS              |

Para habilitar a proteção DDoS a uma rede virtual, sua conta deve também ser atribuída a ações [apropriadas para redes virtuais](../virtual-network/manage-virtual-network.md#permissions).

## <a name="azure-policy"></a>Azure Policy

A criação de mais de um plano não é necessária para a maioria das organizações. O plano não pode ser movido entre as assinaturas. Se você quiser alterar a assinatura de um plano, você precisa excluir o plano existente e criar um novo.

Para clientes que têm várias assinaturas e que desejam garantir que um único plano seja implantado em seu locatário para controle de custo, você pode usar Azure Policy para [restringir a criação de planos padrão de proteção contra DDoS do Azure](https://aka.ms/ddosrestrictplan). Essa política bloqueará a criação de qualquer plano de DDoS, a menos que a assinatura tenha sido marcada anteriormente como uma exceção. Essa política também mostrará uma lista de todas as assinaturas que têm um plano de DDoS implantado, mas não devem, marcá-las como fora de conformidade.


## <a name="next-steps"></a>Próximas etapas

Para saber como exibir e configurar a telemetria para seu plano de proteção contra DDoS, prossiga para os tutoriais.

> [!div class="nextstepaction"]
> [Exibir e configurar a telemetria da Proteção contra DDoS](telemetry.md)
