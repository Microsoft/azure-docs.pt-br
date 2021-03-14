---
title: Controle de segurança do Azure-inventário e gerenciamento de ativos
description: Inventário de controle de segurança do Azure e gerenciamento de ativos
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: f3079ea475c20e3ae0e78319d6c6b24ee579fd0c
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102521298"
---
# <a name="security-control-inventory-and-asset-management"></a>Controle de segurança: inventário e gerenciamento de ativos

As recomendações de gerenciamento de estoque e de ativos concentram-se em resolver problemas relacionados ao gerenciamento ativo (inventário, controle e correção) de todos os recursos do Azure para que apenas os recursos autorizados recebam acesso, e recursos não-autorizados e não gerenciados sejam identificados e removidos.

## <a name="61-use-automated-asset-discovery-solution"></a>6,1: usar solução de descoberta de ativos automatizada

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 6.1 | 1,1, 1,2, 1,3, 1,4, 9,1, 12,1 | Cliente |

Use o grafo de recursos do Azure para consultar/descobrir todos os recursos (como computação, armazenamento, rede, portas e protocolos, etc.) em suas assinaturas.  Configure permissões apropriadas (leitura) no seu locatário e enumere todas as assinaturas do Azure, bem como os recursos em suas assinaturas.

Embora os recursos clássicos do Azure possam ser descobertos por meio do Resource Graph, é altamente recomendável criar e usar recursos do Azure Resource Manager no futuro.

- [Como criar consultas com o Azure Resource Graph](../../governance/resource-graph/first-query-portal.md)

- [Como exibir suas assinaturas do Azure](/powershell/module/az.accounts/get-azsubscription)

- [Entender o RBAC do Azure](../../role-based-access-control/overview.md)

## <a name="62-maintain-asset-metadata"></a>6.2: manter metadados de ativo

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 6.2 | 1.5 | Cliente |

Aplique marcas aos recursos do Azure, fornecendo metadados para organizá-los logicamente em uma taxonomia.

- [Como criar e usar marcas](../../azure-resource-manager/management/tag-resources.md)

## <a name="63-delete-unauthorized-azure-resources"></a>6.3: excluir recursos do Azure não autorizados

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 6.3 | 1.6 | Cliente |

Use marcação, grupos de gerenciamento e assinaturas separadas, quando apropriado, para organizar e controlar ativos. Reconcilie o inventário regularmente e garanta que os recursos não autorizados sejam excluídos da assinatura em tempo hábil.

- [Como criar assinaturas adicionais do Azure](../../cost-management-billing/manage/create-subscription.md)

- [Como criar Grupos de Gerenciamento](../../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar marcas](../../azure-resource-manager/management/tag-resources.md)

## <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: definir e manter um inventário de recursos aprovados do Azure

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 6.4 | 2.1 | Cliente |

Crie um inventário dos recursos aprovados do Azure e do software aprovado para recursos de computação de acordo com nossas necessidades organizacionais.

## <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorar recursos do Azure não aprovados

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 6.5 | 2.3, 2.4 | Cliente |

Use Azure Policy para colocar restrições no tipo de recursos que podem ser criados em suas assinaturas.

Use o Azure Resource Graph para consultar/descobrir recursos em suas assinaturas.  Verifique se todos os recursos do Azure presentes no ambiente foram aprovados.

- [Como configurar e gerenciar o Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [Como criar consultas com o Azure Graph](../../governance/resource-graph/first-query-portal.md)

## <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: monitorar aplicativos de software não aprovados nos recursos de computação

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 6.6 | 2.3, 2.4 | Cliente |

Use o inventário de máquina virtual do Azure para automatizar a coleta de informações sobre todos os softwares em máquinas virtuais. O nome do software, a versão, o Publicador e o tempo de atualização estão disponíveis no portal do Azure. Para obter acesso à data de instalação e outras informações, habilite o diagnóstico de nível de convidado e traga os logs de eventos do Windows para um espaço de trabalho Log Analytics.

- [Como habilitar o inventário de máquina virtual do Azure](../../automation/automation-tutorial-installed-software.md)

## <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: remover recursos e aplicativos de software não aprovados do Azure

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 6.7 | 2.5 | Cliente |

Use o Controle de Alterações (monitoramento de integridade de arquivo) e o inventário de máquina virtual da central de segurança do Azure para identificar todos os softwares instalados nas máquinas virtuais. Você pode implementar seu próprio processo para remover software não autorizado. Você também pode usar uma solução de terceiros para identificar softwares não aprovados.

- [Como usar o monitoramento de integridade de arquivo](../../security-center/security-center-file-integrity-monitoring.md)

- [Entender Controle de Alterações do Azure](../../automation/change-tracking/overview.md)

- [Como habilitar o inventário de máquina virtual do Azure](../../automation/automation-tutorial-installed-software.md)

## <a name="68-use-only-approved-applications"></a>6.8: usar somente aplicativos aprovados

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 6,8 | 2.6 | Cliente |

Use os controles de aplicativo adaptáveis da central de segurança do Azure para garantir que apenas o software autorizado seja executado e todos os softwares não autorizados sejam impedidos de serem executados em máquinas virtuais do Azure.

- [Como usar os controles de aplicativo adaptáveis da central de segurança do Azure](../../security-center/security-center-adaptive-application.md)

## <a name="69-use-only-approved-azure-services"></a>6.9: usar somente serviços do Azure aprovados

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 6.9 | 2.6 | Cliente |

Use Azure Policy para restringir quais serviços você pode provisionar em seu ambiente.

- [Como configurar e gerenciar o Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo de recurso específico com o Azure Policy](../../governance/policy/samples/index.md)

## <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: manter um inventário de títulos de software aprovados

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 6.10 | 2.7 | Cliente |

Use os controles de aplicativo adaptáveis da central de segurança do Azure para especificar para quais tipos de arquivo uma regra pode ou não se aplicar.

Implemente uma solução de terceiros se isso não atender ao requisito.

- [Como usar os controles de aplicativo adaptáveis da central de segurança do Azure](../../security-center/security-center-adaptive-application.md)

## <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: limitar a capacidade dos usuários de interagir com Azure Resource Manager

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 6.11 | 2,9 | Cliente |

Use o acesso condicional do Azure para limitar a capacidade dos usuários de interagir com o Gerenciador de recursos do Azure Configurando "bloquear acesso" para o aplicativo de "gerenciamento de Microsoft Azure".

- [Como configurar o acesso condicional para bloquear o acesso ao Gerenciador de recursos do Azure](../../role-based-access-control/conditional-access-azure-management.md)

## <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: limitar a capacidade dos usuários de executar scripts nos recursos de computação

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 6,12 | 2,9 | Cliente |

Dependendo do tipo de scripts, você pode usar configurações específicas do sistema operacional ou recursos de terceiros para limitar a capacidade dos usuários de executar scripts nos recursos de computação do Azure.  Você também pode aproveitar os controles de aplicativo adaptáveis da central de segurança do Azure para garantir que apenas o software autorizado seja executado e todos os softwares não autorizados sejam impedidos de serem executados em máquinas virtuais do Azure.

- [Como controlar a execução de script do PowerShell em ambientes Windows](/powershell/module/microsoft.powershell.security/set-executionpolicy)

- [Como usar os controles de aplicativo adaptáveis da central de segurança do Azure](../../security-center/security-center-adaptive-application.md)

## <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: separar física ou logicamente os aplicativos de alto risco

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 6.13 | 2,9 | Cliente |

Software necessário para operações de negócios, mas pode incorrer em um risco maior para a organização, deve ser isolado em sua própria máquina virtual e/ou rede virtual e suficientemente protegido com um firewall do Azure ou grupo de segurança de rede.

- [Como criar uma rede virtual](../../virtual-network/quick-create-portal.md)

- [Como criar um NSG com uma configuração de segurança](../../virtual-network/tutorial-filter-network-traffic.md)


## <a name="next-steps"></a>Próximas etapas

- Consulte o próximo controle de segurança: [configuração segura](security-control-secure-configuration.md)