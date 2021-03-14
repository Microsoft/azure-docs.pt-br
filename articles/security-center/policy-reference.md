---
title: Definições de políticas internas para a Central de Segurança do Azure
description: Lista as definições de políticas internas do Azure Policy para a Central de Segurança do Azure. Essas definições de políticas internas fornecem abordagens comuns para gerenciar seus recursos do Azure.
ms.date: 03/10/2021
ms.topic: reference
author: memildin
ms.author: memildin
ms.service: security-center
ms.custom: subject-policy-reference
ms.openlocfilehash: 4509ea09dc75a366e1360d0eeef481552f500f2f
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102613368"
---
# <a name="azure-policy-built-in-definitions-for-azure-security-center"></a>Definições internas do Azure Policy para a Central de Segurança do Azure

Esta página é um índice de [Azure Policy](../governance/policy/overview.md) definições de política internas relacionadas à central de segurança do Azure. Os seguintes agrupamentos de definições de política estão disponíveis:

- O grupo de [iniciativas](#azure-security-center-initiatives) lista as definições de iniciativa de Azure Policy na categoria "central de segurança".
- O grupo de [iniciativa padrão](#azure-security-center-initiatives) lista todas as definições de Azure Policy que fazem parte da iniciativa padrão da central de segurança, o [benchmark de segurança do Azure](../security/benchmarks/introduction.md). Esse benchmark criado pela Microsoft e amplamente respeitado se baseia em controles do [CIS (centro de segurança da Internet)](https://www.cisecurity.org/benchmark/azure/) e do [NIST (National Institute of Standards and Technology)](https://www.nist.gov/) , com foco na segurança centrada na nuvem.
- O grupo de [categorias](#azure-security-center-category) lista todas as definições de Azure Policy na categoria "central de segurança".

Para obter mais informações sobre políticas de segurança, confira [Trabalhando com políticas de segurança](./tutorial-security-policy.md). Para obter políticas internas adicionais do Azure Policy para outros serviços, confira [Definições internas do Azure Policy](../governance/policy/samples/built-in-policies.md).

O nome de cada definição de política interna leva à definição da política no portal do Azure. Use o link na coluna **Versão** para exibir a origem no [repositório GitHub do Azure Policy](https://github.com/Azure/azure-policy).

## <a name="azure-security-center-initiatives"></a>Iniciativas da central de segurança do Azure

Para saber mais sobre as iniciativas internas que são monitoradas pela central de segurança, consulte a tabela a seguir:

[!INCLUDE [azure-policy-reference-policyset-security-center](../../includes/policy/reference/bycat/policysets-security-center.md)]

## <a name="security-centers-default-initiative-azure-security-benchmark"></a>Iniciativa padrão da central de segurança (benchmark de segurança do Azure)

Para saber mais sobre as políticas internas que são monitoradas pela central de segurança, consulte a tabela a seguir:

[!INCLUDE [azure-policy-reference-init-asc](../../includes/policy/reference/custom/init-asc.md)]

## <a name="azure-security-center-category"></a>Categoria da central de segurança do Azure

[!INCLUDE [azure-policy-reference-category-securitycenter](../../includes/policy/reference/bycat/policies-security-center.md)]

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre as definições de política de segurança Azure Policy na central de segurança. Para saber mais sobre iniciativas, políticas e como elas se relacionam com as recomendações da central de segurança, consulte [o que são políticas de segurança, iniciativas e recomendações?](security-policy-concept.md).
