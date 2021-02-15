---
title: Linha de base de segurança do Azure Resource Graph para benchmark de segurança do Azure
description: A linha de base de segurança do grafo de recursos do Azure fornece diretrizes de procedimento e recursos para implementar as recomendações de segurança especificadas no benchmark de segurança do Azure.
author: msmbaldwin
ms.service: resource-graph
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: a85fdf1e1e27b1ce5abb0c7890b717d48ba3bd3f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89230442"
---
# <a name="azure-resource-graph-security-baseline-for-azure-security-benchmark"></a>Linha de base de segurança do Azure Resource Graph para benchmark de segurança do Azure

Essa linha de base de segurança aplica diretrizes do [benchmark de segurança do Azure](../../../security/benchmarks/overview.md) para o grafo de recursos do Azure. O Azure Security Benchmark fornece recomendações sobre como você pode proteger suas soluções de nuvem no Azure. O conteúdo é agrupado pelos **controles de segurança** definidos pelo benchmark de segurança do Azure e pelas diretrizes relacionadas aplicáveis ao grafo de recursos do Azure. Os **controles** não aplicáveis ao grafo de recursos do Azure foram excluídos. Para ver como o grafo de recursos do Azure mapeia completamente para o benchmark de segurança do Azure, consulte o [arquivo de mapeamento de linha de base de segurança de rede virtual do Azure completo](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).



## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para saber mais, confira [Controle de segurança: Identidade e controle de acesso](../../../security/benchmarks/security-control-identity-access-control.md).*

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: revisar e reconciliar regularmente o acesso do usuário

**Diretrizes**: o grafo de recursos do Azure fornece acesso a tipos de recursos e propriedades com base no controle de acesso baseado em função do Azure (RBAC do Azure). Auditar e examinar o acesso concedido a entidades de segurança (usuários, grupos e contas de serviço) regularmente para garantir que as consultas retornem os resultados para os recursos apropriados.

* [Permissões no Azure Resource Graph](../overview.md#permissions-in-azure-resource-graph)

* [Como usar as revisões de acesso de identidade do Azure](../../../active-directory/governance/access-reviews-overview.md)


**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="data-protection"></a>Proteção de dados

*Para saber mais, confira [Controle de segurança: Proteção de dados](../../../security/benchmarks/security-control-data-protection.md).*

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: usar o RBAC do Azure para controlar o acesso a recursos

**Orientação**: Use o controle de acesso baseado em função do Azure (RBAC do Azure) para controlar o acesso a dados e recursos. Para usar o grafo de recursos do Azure, você também deve ter acesso apropriado aos recursos que deseja consultar. Esse acesso deve ser definido como somente leitura e concedido somente à equipe necessária.

* [Permissões no Azure Resource Graph](../overview.md#permissions-in-azure-resource-graph)

* [Como configurar o RBAC do Azure](../../../role-based-access-control/role-assignments-rest.md)


**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para saber mais, confira [Controle de segurança: Inventário e gerenciamento de ativos](../../../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usar solução de descoberta de ativos automatizada

**Orientação**: Use o grafo de recursos do Azure para consultar e descobrir todos os recursos com suporte em suas assinaturas, grupos de gerenciamento e locatários. Verifique se você tem as permissões apropriadas em seu locatário e se é capaz de enumerar todas as assinaturas do Azure, bem como recursos em suas assinaturas.

* [Como criar consultas com o Azure Resource Graph](../first-query-portal.md)

* [Entender o RBAC do Azure](../../../role-based-access-control/overview.md)


**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definir e manter o inventário de recursos aprovados do Azure

**Diretrizes**: Crie um inventário dos recursos aprovados do Azure e do software aprovado para recursos de computação de acordo com suas necessidades organizacionais. Use o grafo de recursos do Azure para consultar os recursos aprovados do Azure e o histórico de alterações (versão prévia) para examinar os instantâneos e ver o que mudou.

* [Consultar recursos do Azure com o grafo de recursos do Azure](../first-query-portal.md)

* [Obter alterações de recurso](../how-to/get-resource-changes.md)


**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorar recursos do Azure não aprovados

**Orientação**: Use o grafo de recursos do Azure para consultar e descobrir recursos em suas assinaturas, grupos de gerenciamento e locatários. Verifique se todos os recursos do Azure no ambiente foram aprovados.

* [Consultar recursos do Azure com o grafo de recursos do Azure](../first-query-portal.md)

* [Exemplos: consultas de início para o grafo de recursos do Azure](../samples/starter.md)


**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="next-steps"></a>Próximas etapas

- Confira o [Azure Security Benchmark](../../../security/benchmarks/overview.md)
- Saiba mais sobre a [Linhas de base de segurança do Azure](../../../security/benchmarks/security-baselines-overview.md)
