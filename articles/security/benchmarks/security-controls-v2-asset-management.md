---
title: Benchmark de segurança do Azure v2 – gerenciamento de ativos
description: Gerenciamento de ativos do benchmark de segurança do Azure v2
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: f0c2fe78c32357798e1f9acb43f5867df9148b38
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/13/2020
ms.locfileid: "97368895"
---
# <a name="security-control-v2-asset-management"></a>Controle de segurança v2: gerenciamento de ativos

O gerenciamento de ativos abrange controles para garantir a visibilidade e a governança da segurança sobre os recursos do Azure. Isso inclui recomendações sobre permissões para a equipe de segurança, acesso de segurança ao inventário de ativos e gerenciamento de aprovações para serviços e recursos (inventário, controle e correção).

## <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: garantir que a equipe de segurança tenha visibilidade dos riscos de ativos

| ID do Azure | Controles do CIS v 7.1 ID (s) | ID (s) do NIST SP 800-53 R4 |
|--|--|--|--|
| AM-1 | 1,1, 1,2 | CM-8, PM-5 |

Verifique se as equipes de segurança recebem permissões de leitor de segurança em seu locatário e assinaturas do Azure para que possam monitorar riscos de segurança usando a central de segurança do Azure. 

Dependendo de como as responsabilidades da equipe de segurança são estruturadas, o monitoramento dos riscos de segurança pode ser a responsabilidade de uma equipe de segurança central ou de uma equipe local. Dito isso, os insights e os riscos de segurança sempre precisam ser agregados de maneira centralizada em uma organização. 

As permissões de Leitor de segurança podem ser aplicadas amplamente a um locatário inteiro (grupo de gerenciamento raiz) ou terem como escopo grupos de gerenciamento ou assinaturas específicas. 

Observação: podem ser necessárias permissões adicionais a fim de obter visibilidade das cargas de trabalho e dos serviços. 

- [Visão geral da função Leitor de segurança](../../role-based-access-control/built-in-roles.md#security-reader)

- [Visão geral dos grupos de gerenciamento do Azure](../../governance/management-groups/overview.md)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente** ([saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Segurança de infraestrutura e ponto de extremidade](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Gerenciamento de conformidade de segurança](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: verificar se a equipe de segurança tem acesso ao inventário de ativos e metadados

| ID do Azure | Controles do CIS v 7.1 ID (s) | ID (s) do NIST SP 800-53 R4 |
|--|--|--|--|
| AM-2 | 1,1, 1,2, 1,4, 1,5, 9,1, 12,1 | CM-8, PM-5 |

Garanta que as equipes de segurança tenham acesso a um inventário de ativos continuamente atualizado no Azure. As equipes de segurança geralmente precisam desse inventário para avaliar a potencial exposição da organização delas a riscos emergentes e como uma entrada para aprimoramentos de segurança contínuos. 

O recurso de inventário da central de segurança do Azure e o grafo de recursos do Azure podem consultar e descobrir todos os recursos em suas assinaturas, incluindo serviços do Azure, aplicativos e recursos de rede.  

Organize logicamente os ativos de acordo com a taxonomia de sua organização usando marcas, bem como outros metadados no Azure (nome, descrição e categoria).  

- [Como criar consultas com o Azure Resource Graph Explorer](../../governance/resource-graph/first-query-portal.md)

- [Gerenciamento de inventário de ativos da central de segurança do Azure](../../security-center/asset-inventory.md)

- [Para obter mais informações sobre como marcar ativos, consulte o guia de decisão de nomenclatura e marcação de recursos](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente** ([saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Segurança de infraestrutura e ponto de extremidade](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Gerenciamento de conformidade de segurança](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="am-3-use-only-approved-azure-services"></a>AM-3: usar somente serviços do Azure aprovados

| ID do Azure | Controles do CIS v 7.1 ID (s) | ID (s) do NIST SP 800-53 R4 |
|--|--|--|--|
| AM-3 | 2.3, 2.4 | CM-7, CM-8 |

Use o Azure Policy para auditar e restringir quais serviços os usuários podem provisionar em seu ambiente. Use o Azure Resource Graph para consultar e descobrir recursos dentro das assinaturas deles.  Você também poderá usar o Azure Monitor para criar regras para disparar alertas quando um serviço não aprovado for detectado.

- [Configurar e gerenciar Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo de recurso específico com o Azure Policy](../../governance/policy/samples/index.md)

- [Como criar consultas com o Azure Resource Graph Explorer](../../governance/resource-graph/first-query-portal.md)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente** ([saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Gerenciamento de conformidade de segurança](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Gerenciamento de postura](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)  

## <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: garantir a segurança do gerenciamento do ciclo de vida dos ativos

| ID do Azure | Controles do CIS v 7.1 ID (s) | ID (s) do NIST SP 800-53 R4 |
|--|--|--|--|
| AM-4 | 2,3, 2,4, 2,5 | CM-7, CM-8, CM-10, CM-11 |

Estabeleça ou atualize políticas de segurança que atendam aos processos de gerenciamento do ciclo de vida de ativos para modificações potencialmente de impacto alto. Essas modificações incluem alterações em: provedores de identidade e acesso, confidencialidade de dados, configuração de rede e atribuição de privilégio administrativo.

Remova os recursos do Azure quando eles não forem mais necessários.

- [Excluir o grupo de recursos e o recurso do Azure](../../azure-resource-manager/management/delete-resource-group.md)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente** ([saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Segurança de infraestrutura e ponto de extremidade](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Gerenciamento de postura](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)  

- [Gerenciamento de conformidade de segurança](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: limitar a capacidade dos usuários de interagir com Azure Resource Manager

| ID do Azure | Controles do CIS v 7.1 ID (s) | ID (s) do NIST SP 800-53 R4 |
|--|--|--|--|
| -5 | 2,9 | AC-3 |

Use o acesso condicional do Azure AD para limitar a capacidade dos usuários de interagir com Azure Resource Manager Configurando "bloquear acesso" para o aplicativo de "gerenciamento de Microsoft Azure".

- [Como configurar o acesso condicional para bloquear o acesso ao Gerenciador de recursos do Azure](../../role-based-access-control/conditional-access-azure-management.md)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente** ([saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Gerenciamento de postura](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)  

- [Segurança de infraestrutura e ponto de extremidade](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="am-6-use-only-approved-applications-in-compute-resources"></a>AM-6: usar somente aplicativos aprovados em recursos de computação

| ID do Azure | Controles do CIS v 7.1 ID (s) | ID (s) do NIST SP 800-53 R4 |
|--|--|--|--|
| -6 | 2,6, 2,7 | AC-3, CM-7, CM-8, CM-10, CM-11 |

Certifique-se de que apenas o software autorizado seja executado e todos os softwares não autorizados sejam impedidos de serem executados em máquinas virtuais do Azure.

Use os controles de aplicativo adaptável da central de segurança do Azure (ASC) para descobrir e gerar uma lista de permissões de aplicativo. Você também pode usar os controles de aplicativo adaptáveis ASC para garantir que apenas o software autorizado seja executado e todos os softwares não autorizados sejam impedidos de serem executados em máquinas virtuais do Azure.

Use a Controle de Alterações de automação do Azure e o inventário para automatizar a coleta de informações de inventário de suas VMs Windows e Linux. O nome do software, a versão, o Publicador e o tempo de atualização estão disponíveis no portal do Azure. Para obter a data de instalação do software e outras informações, habilite o diagnóstico em nível de convidado e direcione os logs de eventos do Windows para Log Analytics espaço de trabalho.

Dependendo do tipo de scripts, você pode usar configurações específicas do sistema operacional ou recursos de terceiros para limitar a capacidade dos usuários de executar scripts nos recursos de computação do Azure. 

Você também pode usar uma solução de terceiros para descobrir e identificar softwares não aprovados.

- [Como usar os controles de aplicativo adaptáveis da central de segurança do Azure](../../security-center/security-center-adaptive-application.md)

- [Entender o inventário e Controle de Alterações de automação do Azure](../../automation/change-tracking/overview.md)

- [Como controlar a execução de script do PowerShell em ambientes Windows](/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente** ([saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Segurança de infraestrutura e ponto de extremidade](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Gerenciamento de postura](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)  

- [Gerenciamento de conformidade de segurança](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)