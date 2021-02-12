---
title: Linha de base de segurança do Azure Policy para o benchmark de segurança do Azure
description: A linha de base de segurança Azure Policy fornece diretrizes de procedimento e recursos para implementar as recomendações de segurança especificadas no benchmark de segurança do Azure.
author: msmbaldwin
ms.service: azure-policy
ms.topic: conceptual
ms.date: 07/02/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: e8915e1c15972341befd176b412925f4e87c94f6
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98201443"
---
# <a name="azure-policy-security-baseline-for-azure-security-benchmark"></a>Linha de base de segurança do Azure Policy para o benchmark de segurança do Azure

Essa linha de base de segurança aplica diretrizes do [benchmark de segurança do Azure](../../../security/benchmarks/overview.md) para Azure Policy. O Azure Security Benchmark fornece recomendações sobre como você pode proteger suas soluções de nuvem no Azure. O conteúdo é agrupado pelos **domínios de conformidade** e pelos **controles de segurança** definidos pelo benchmark de segurança do Azure e pelas diretrizes relacionadas aplicáveis ao Azure Policy. **Controles** não aplicáveis a Azure Policy foram excluídos. Para ver como Azure Policy é completamente mapeado para o benchmark de segurança do Azure, consulte o [arquivo completo de mapeamento de linha de base de segurança Azure Policy](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

Para um mapeamento dos controles de benchmark de segurança do Azure para definições de política internas por meio da iniciativa interna, consulte [conformidade regulatória: benchmark de segurança do Azure](../samples/azure-security-benchmark.md).

Azure Policy usa a _Propriedade_ do termo no local de _responsabilidade_. Para obter detalhes sobre a _Propriedade_, consulte [definições de política de Azure Policy](./definition-structure.md#type) e [responsabilidade compartilhada na nuvem](../../../security/fundamentals/shared-responsibility.md).


## <a name="logging-and-monitoring"></a>Log e monitoramento

*Para saber mais, confira [Controle de segurança: Registro em log e monitoramento](../../../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: habilitar o registro em log de auditoria para recursos do Azure

**Diretrizes**: Azure Policy usa logs de atividade, que são habilitados automaticamente, para incluir origem do evento, data, usuário, carimbo de data/hora, endereços de origem, endereços de destino e outros elementos úteis.

* [Como coletar logs e métricas de plataforma com Azure Monitor](../../../azure-monitor/platform/diagnostic-settings.md)

* [Entender o registro em log e diferentes tipos de log no Azure](../../../azure-monitor/platform/platform-logs-overview.md)


**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para saber mais, confira [Controle de segurança: Identidade e controle de acesso](../../../security/benchmarks/security-control-identity-access-control.md).*

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Usar contas administrativas dedicadas

**Diretriz**: Crie procedimentos operacionais padrão em relação ao uso de contas administrativas dedicadas. Use a identidade e o gerenciamento de acesso da Central de Segurança do Azure para monitorar a quantidade de contas administrativas. 

Você também pode habilitar uma solução de acesso just-in-time/apenas o suficiente usando [Azure ad Privileged Identity Management](../../../active-directory/privileged-identity-management/pim-configure.md) funções privilegiadas ou [Azure Resource Manager](../../../azure-resource-manager/management/overview.md).


**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Usar computadores dedicados (estações de trabalho com acesso privilegiado) para todas as tarefas administrativas

**Diretrizes**: Use PAWs (estações de trabalho com acesso privilegiado) com a MFA configurada para fazer logon e configurar recursos do Azure.

* [Saiba mais sobre Estações de Trabalho com Acesso Privilegiado](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

* [Como habilitar a MFA no Azure](../../../active-directory/authentication/howto-mfa-getstarted.md)


**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="data-protection"></a>Proteção de dados

*Para saber mais, confira [Controle de segurança: Proteção de dados](../../../security/benchmarks/security-control-data-protection.md).*

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: usar o RBAC do Azure para controlar o acesso a recursos

**Orientação**: Use o controle de acesso baseado em função do Azure (RBAC do Azure) para controlar o acesso a Azure Policy.

* [Permissões do Azure RBAC no Azure Policy](../overview.md#azure-rbac-permissions-in-azure-policy)

* [Como configurar o RBAC do Azure](../../../role-based-access-control/role-assignments-portal.md)


**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrar e alertar sobre alterações em recursos críticos do Azure

**Diretrizes**: Use Azure monitor com logs de atividade para criar alertas para quando as alterações ocorrerem no Azure Policy.

* [Como criar alertas para eventos do log de atividades do Azure](../../../azure-monitor/platform/alerts-activity-log.md)


**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para saber mais, confira [Controle de segurança: Inventário e gerenciamento de ativos](../../../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativo

**Diretriz**: Aplique marcas aos recursos do Azure, fornecendo metadados para organizá-los logicamente em uma taxonomia. Use o efeito Azure Policy _Modificar_ para relatar e impor a conformidade e o controle de marca consistente.

* [Tutorial: criar e gerenciar políticas](../tutorials/create-and-manage.md)

* [Tutorial: Gerenciar a governança de marcas](../tutorials/govern-tags.md)


**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: definir e manter um inventário de recursos aprovados do Azure

**Diretrizes**: Crie um inventário de definições de política aprovadas e atribuições de política de acordo com suas necessidades organizacionais.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorar recursos do Azure não aprovados

**Diretrizes**: Use Azure Policy para colocar restrições no tipo de recursos que podem ser criados em suas assinaturas.

* [Como configurar e gerenciar o Azure Policy](../tutorials/create-and-manage.md)


**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="next-steps"></a>Próximas etapas

- Confira o [Azure Security Benchmark](../../../security/benchmarks/overview.md)
- Saiba mais sobre a [Linhas de base de segurança do Azure](../../../security/benchmarks/security-baselines-overview.md)
