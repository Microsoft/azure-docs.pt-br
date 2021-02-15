---
title: Definições de políticas internas para a Central de Segurança do Azure
description: Lista as definições de políticas internas do Azure Policy para a Central de Segurança do Azure. Essas definições de políticas internas fornecem abordagens comuns para gerenciar seus recursos do Azure.
ms.date: 02/09/2021
ms.topic: reference
author: memildin
ms.author: memildin
ms.service: security-center
ms.custom: subject-policy-reference
ms.openlocfilehash: ab6556bc391eb3136fa8c4ba40f5a5d71da956e7
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100093023"
---
# <a name="azure-policy-built-in-definitions-for-azure-security-center"></a>Definições internas do Azure Policy para a Central de Segurança do Azure

Esta página é um índice de [Azure Policy](../governance/policy/overview.md) definições de política internas relacionadas à central de segurança do Azure. Os seguintes agrupamentos de definições de política estão disponíveis:

- O grupo de [iniciativas](#azure-security-center-initiatives) lista as definições de iniciativa de Azure Policy na categoria ' Central de segurança '.
- O grupo de [iniciativa padrão](#azure-security-center-initiatives) lista todas as definições de Azure Policy que fazem parte da iniciativa padrão da central de segurança, o [benchmark de segurança do Azure](../security/benchmarks/introduction.md). Esse benchmark criado pela Microsoft e amplamente respeitado se baseia em controles do [CIS (centro de segurança da Internet)](https://www.cisecurity.org/benchmark/azure/) e do [NIST (National Institute of Standards and Technology)](https://www.nist.gov/) , com foco na segurança centrada na nuvem.
- O grupo de [categorias](#azure-security-center-category) lista todas as definições de Azure Policy na categoria ' Central de segurança '.

Para obter mais informações sobre políticas de segurança, confira [Trabalhando com políticas de segurança](./tutorial-security-policy.md). Para obter políticas internas adicionais do Azure Policy para outros serviços, confira [Definições internas do Azure Policy](../governance/policy/samples/built-in-policies.md).

O nome de cada definição de política interna leva à definição da política no portal do Azure. Use o link na coluna **Versão** para ver a origem no [repositório GitHub do Azure Policy](https://github.com/Azure/azure-policy).

## <a name="azure-security-center-initiatives"></a>Iniciativas da central de segurança do Azure

Para saber mais sobre as iniciativas internas que são monitoradas pela central de segurança, consulte a tabela a seguir:

[!INCLUDE [azure-policy-reference-policyset-security-center](../../includes/policy/reference/bycat/policysets-security-center.md)]

## <a name="azure-security-center-default-initiative"></a>Iniciativa padrão da central de segurança do Azure

Para saber mais sobre as políticas internas que são monitoradas pela central de segurança, consulte a tabela a seguir:

[!INCLUDE [azure-policy-reference-init-asc](../../includes/policy/reference/custom/init-asc.md)]

## <a name="azure-security-center-category"></a>Categoria da central de segurança do Azure

[!INCLUDE [azure-policy-reference-category-securitycenter](../../includes/policy/reference/bycat/policies-security-center.md)]

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre as definições de política de segurança Azure Policy na central de segurança. Para saber mais, consulte os artigos a seguir.

- Confira os internos no [repositório Azure Policy GitHub](https://github.com/Azure/azure-policy).
- Revise a [estrutura de definição do Azure Policy](../governance/policy/concepts/definition-structure.md).
- Revisar [Compreendendo os efeitos da política](../governance/policy/concepts/effects.md).
- [Guia de planejamento e operações da central de segurança do Azure](./security-center-planning-and-operations-guide.md): saiba como planejar e entender as considerações de design na central de segurança do Azure.
- [Monitoramento da integridade de segurança na Central de Segurança do Azure](./security-center-monitoring.md): saiba como monitorar a integridade dos recursos do Azure.
- [Gerenciar e responder aos alertas de segurança na Central de Segurança do Azure](./security-center-managing-and-responding-alerts.md): saiba como gerenciar e responder aos alertas de segurança.
- [Monitorar as soluções de parceiros com a Central de Segurança do Azure](./security-center-partner-integration.md): saiba como monitorar o status de integridade de suas soluções de parceiros.
- [Azure Policy](../governance/policy/overview.md): Aprenda a auditar e governar os recursos do Azure.
