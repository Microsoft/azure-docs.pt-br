---
title: Controles de Conformidade Regulatória do Azure Policy para o Azure Cognitive Search
description: Lista os controles de Conformidade Regulatória do Azure Policy Disponíveis para o Azure Cognitive Search. Estas definições de políticas internas fornecem abordagens comuns para gerenciar a conformidade dos seus recursos do Azure.
ms.date: 01/25/2021
ms.topic: sample
author: HeidiSteen
ms.author: heidist
ms.service: search
ms.custom: subject-policy-compliancecontrols
ms.openlocfilehash: 1f06865fc0b4f982c2e2653e471c75d2a1f88c9e
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98795419"
---
# <a name="azure-policy-regulatory-compliance-controls-for-azure-cognitive-search"></a>Controles de Conformidade Regulatória do Azure Policy para o Azure Cognitive Search

Se está usando o [Azure Policy](../governance/policy/overview.md) para impor as recomendações do [Azure Security Benchmark](../security/benchmarks/introduction.md), provavelmente você já sabe que pode criar políticas para identificar e corrigir serviços fora de conformidade. Essas políticas podem ser personalizadas ou podem ser baseadas em definições internas que fornecem critérios de conformidade e as soluções apropriadas para as melhores práticas mais conhecidas.

Para o Azure Cognitive Search, atualmente há uma definição interna, listada abaixo, que você pode usar em uma atribuição de política. Essa definição interna tem finalidade de registro em log e monitoramento. Usando essa definição interna em uma [política criada por você](../governance/policy/assign-policy-portal.md), o sistema verificará se há serviços de pesquisa que não têm o [log de diagnósticos](search-monitor-logs.md) e o habilitará adequadamente.

[A Conformidade Regulatória no Azure Policy](../governance/policy/concepts/regulatory-compliance.md) fornece definições de iniciativas criadas e gerenciadas pela Microsoft, conhecidas como _definições internas_, para os **domínios de conformidade** e **controles de segurança** relacionados a diferentes padrões de conformidade. Esta página lista os **domínios de conformidade** e os **controles de segurança** para o Azure Cognitive Search. Você pode atribuir os itens internos a um **controle de segurança** individualmente a fim de ajudar a manter seus recursos do Azure em conformidade com o padrão específico.

[!INCLUDE [azure-policy-compliancecontrols-introwarning](../../includes/policy/standards/intro-warning.md)]

[!INCLUDE [azure-policy-compliancecontrols-search](../../includes/policy/standards/byrp/microsoft.search.md)]

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a [Conformidade Regulatória do Azure Policy](../governance/policy/concepts/regulatory-compliance.md).
- Confira os internos no [repositório Azure Policy GitHub](https://github.com/Azure/azure-policy).
