---
title: Linha de base de segurança do Azure para Azure Policy
description: A linha de base de segurança Azure Policy fornece diretrizes de procedimento e recursos para implementar as recomendações de segurança especificadas no benchmark de segurança do Azure.
author: msmbaldwin
ms.service: azure-policy
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 6e8bb4cf715c6cb8d0729399c1985376de18687b
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105561280"
---
# <a name="azure-security-baseline-for-azure-policy"></a>Linha de base de segurança do Azure para Azure Policy

Essa linha de base de segurança aplica diretrizes do [benchmark de segurança do Azure](../../../security/benchmarks/overview.md) para Azure Policy. O Azure Security Benchmark fornece recomendações sobre como você pode proteger suas soluções de nuvem no Azure. O conteúdo é agrupado pelos **domínios de conformidade** e pelos **controles de segurança** definidos pelo benchmark de segurança do Azure e pelas diretrizes relacionadas aplicáveis ao Azure Policy. **Controles** não aplicáveis a Azure Policy foram excluídos. Para ver como Azure Policy é completamente mapeado para o benchmark de segurança do Azure, consulte o [arquivo completo de mapeamento de linha de base de segurança Azure Policy](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

Para um mapeamento dos controles de benchmark de segurança do Azure para definições de política internas por meio da iniciativa interna, consulte [conformidade regulatória: benchmark de segurança do Azure](../samples/azure-security-benchmark.md).

Azure Policy usa a _Propriedade_ do termo no local de _responsabilidade_. Para obter detalhes sobre a _Propriedade_, consulte [definições de política de Azure Policy](./definition-structure.md#type) e [responsabilidade compartilhada na nuvem](../../../security/fundamentals/shared-responsibility.md).

## <a name="logging-and-monitoring"></a>Registro em log e monitoramento

*Para obter mais informações, consulte o [benchmark de segurança do Azure: registro em log e monitoramento](../../../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: habilitar o registro em log de auditoria para recursos do Azure

**Diretrizes**: Azure Policy usa logs de atividade, que são habilitados automaticamente, para incluir origem do evento, data, usuário, carimbo de data/hora, endereços de origem, endereços de destino e outros elementos úteis.

- [Como coletar logs e métricas de plataforma com Azure Monitor](../../../azure-monitor/essentials/diagnostic-settings.md)

- [Entender o registro em log e diferentes tipos de log no Azure](../../../azure-monitor/essentials/platform-logs-overview.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para obter mais informações, consulte o [benchmark de segurança do Azure: identidade e controle de acesso](../../../security/benchmarks/security-control-identity-access-control.md).*

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Usar contas administrativas dedicadas

**Diretriz**: Crie procedimentos operacionais padrão em relação ao uso de contas administrativas dedicadas. Use a identidade e o gerenciamento de acesso da Central de Segurança do Azure para monitorar a quantidade de contas administrativas. Você também pode habilitar uma solução de acesso just-in-time/apenas o suficiente usando [Azure Active Directory (Azure AD) Privileged Identity Management](../../../active-directory/privileged-identity-management/pim-configure.md) funções privilegiadas ou [Azure Resource Manager](../../../azure-resource-manager/management/overview.md).

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: o [benchmark de segurança do Azure](/azure/governance/policy/samples/azure-security-benchmark) é a iniciativa de política padrão para a central de segurança e é a base para as [recomendações da central de segurança](/azure/security-center/security-center-recommendations). As definições de Azure Policy relacionadas a esse controle são habilitadas automaticamente pela central de segurança. Os alertas relacionados a esse controle podem exigir um plano do [Azure defender](/azure/security-center/azure-defender) para os serviços relacionados.

**Azure Policy definições internas-Microsoft. GuestConfiguration**:

[!INCLUDE [Resource Policy for Microsoft.GuestConfiguration 3.3](../../../../includes/policy/standards/asb/rp-controls/microsoft.guestconfiguration-3-3.md)]

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: usar estações de trabalho seguras e gerenciadas pelo Azure para tarefas administrativas

**Diretrizes**: Use PAWs (estações de trabalho com acesso privilegiado) com a autenticação multifator configurada para fazer logon e configurar os recursos do Azure.

- [Saiba mais sobre Estações de Trabalho com Acesso Privilegiado](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Como habilitar a autenticação multifator no Azure](../../../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, confira o [Azure Security Benchmark: proteção de dados](../../../security/benchmarks/security-control-data-protection.md).*

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: usar o RBAC do Azure para controlar o acesso a recursos 

**Orientação**: Use o controle de acesso baseado em função do Azure (RBAC do Azure) para controlar o acesso a Azure Policy.

- [Permissões do Azure RBAC no Azure Policy](../overview.md#azure-rbac-permissions-in-azure-policy)

- [Como configurar o RBAC do Azure](../../../role-based-access-control/role-assignments-portal.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrar e alertar sobre alterações em recursos críticos do Azure

**Diretrizes**: Use Azure monitor com logs de atividade para criar alertas para quando as alterações ocorrerem no Azure Policy.

- [Como criar alertas para eventos do log de atividades do Azure](../../../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para obter mais informações, consulte o [benchmark de segurança do Azure: inventário e gerenciamento de ativos](../../../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativo

**Diretriz**: Aplique marcas aos recursos do Azure, fornecendo metadados para organizá-los logicamente em uma taxonomia. Use o efeito Azure Policy _Modificar_ para relatar e impor a conformidade e o controle de marca consistente.

- [Tutorial: criar e gerenciar políticas](../tutorials/create-and-manage.md)

- [Tutorial: Gerenciar a governança de marcas](../tutorials/govern-tags.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definir e manter o inventário de recursos aprovados do Azure

**Diretrizes**: Crie um inventário de definições de política aprovadas e atribuições de política de acordo com suas necessidades organizacionais.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorar recursos do Azure não aprovados

**Diretrizes**: Use Azure Policy para colocar restrições no tipo de recursos que podem ser criados em suas assinaturas.

- [Como configurar e gerenciar o Azure Policy](../tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="next-steps"></a>Próximas etapas

- Confira a [Visão geral do Azure Security Benchmark V2](../../../security/benchmarks/overview.md)
- Saiba mais sobre a [Linhas de base de segurança do Azure](../../../security/benchmarks/security-baselines-overview.md)