---
title: Definições de políticas internas para a Central de Segurança do Azure
description: Lista as definições de políticas internas do Azure Policy para a Central de Segurança do Azure. Essas definições de políticas internas fornecem abordagens comuns para gerenciar seus recursos do Azure.
ms.date: 04/14/2021
ms.topic: reference
author: memildin
ms.author: memildin
ms.service: security-center
ms.custom: subject-policy-reference
ms.openlocfilehash: 8023dcc29fe8ea3af853237e911524549e4d2bcb
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739630"
---
# <a name="azure-policy-built-in-definitions-for-azure-security-center"></a>Definições internas do Azure Policy para a Central de Segurança do Azure

Esta página é um índice de definições de políticas internas do [Azure Policy](../governance/policy/overview.md) relacionadas à Central de Segurança do Azure. Os seguintes agrupamentos de definições de política estão disponíveis:

- O grupo de [iniciativas](#azure-security-center-initiatives) lista as definições de iniciativa do Azure Policy na categoria "Central de Segurança".
- O grupo de [iniciativa padrão](#azure-security-center-initiatives) lista todas as definições do Azure Policy que fazem parte da iniciativa padrão da Central de Segurança, o [Azure Security Benchmark](https://docs.microsoft.com/security/benchmark/azure/introduction). Esse parâmetro de comparação amplamente respeitado e de autoria da Microsoft se baseia nos controles do [CIS (Center for Internet Security)](https://www.cisecurity.org/benchmark/azure/) e do [NIST (National Institute of Standards and Technology)](https://www.nist.gov/) com foco na segurança centrada na nuvem.
- O grupo de [categoria](#azure-security-center-category) lista todas as definições do Azure Policy na categoria "Central de Segurança".

Para obter mais informações sobre políticas de segurança, confira [Trabalhando com políticas de segurança](./tutorial-security-policy.md). Para obter políticas internas adicionais do Azure Policy para outros serviços, confira [Definições internas do Azure Policy](../governance/policy/samples/built-in-policies.md).

O nome de cada definição de política interna leva à definição da política no portal do Azure. Use o link na coluna **Versão** para exibir a origem no [repositório GitHub do Azure Policy](https://github.com/Azure/azure-policy).

## <a name="azure-security-center-initiatives"></a>Iniciativas da Central de Segurança do Azure

Para saber mais sobre as iniciativas internas monitoradas pela Central de Segurança, confira a seguinte tabela:

[!INCLUDE [azure-policy-reference-policyset-security-center](../../includes/policy/reference/bycat/policysets-security-center.md)]

## <a name="security-centers-default-initiative-azure-security-benchmark"></a>Iniciativa padrão da Central de Segurança (Azure Security Benchmark)

Para saber mais sobre as políticas internas monitoradas pela Central de Segurança, confira a seguinte tabela:

[!INCLUDE [azure-policy-reference-init-asc](../../includes/policy/reference/custom/init-asc.md)]

## <a name="azure-security-center-category"></a>Categoria Central de Segurança do Azure

[!INCLUDE [azure-policy-reference-category-securitycenter](../../includes/policy/reference/bycat/policies-security-center.md)]

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre as definições de política de segurança do Azure Policy na Central de Segurança. Para saber mais sobre iniciativas, políticas e como elas se relacionam com as recomendações da Central de Segurança, confira [O que são políticas, iniciativas e recomendações de segurança?](security-policy-concept.md).
