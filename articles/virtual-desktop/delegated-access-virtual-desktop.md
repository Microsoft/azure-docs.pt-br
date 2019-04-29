---
title: Acesso delegado na visualização de área de trabalho Virtual do Windows - Azure
description: Como delegar capacidades administrativas em uma implantação de visualização de área de trabalho Virtual do Windows, incluindo exemplos.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 250aea52de63a6397ce00e9cadcadf3a8ba39858
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60870499"
---
# <a name="delegated-access-in-windows-virtual-desktop-preview"></a>Acesso delegado na visualização de área de trabalho Virtual do Windows

Visualização de área de trabalho Virtual do Windows tem um modelo de acesso delegado que permite que você defina a quantidade de acesso que um determinado usuário tem permissão para ter atribuindo-lhes uma função. Uma atribuição de função tem três componentes: entidade de segurança, definição de função e escopo. O modelo de acesso delegado de área de trabalho Virtual do Windows é baseado no modelo de RBAC do Azure. Para saber mais sobre as atribuições de função específicos e seus componentes, consulte [visão geral do controle de acesso baseado em função do Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles).

Área de trabalho Virtual do Windows delegada acesso suporta os seguintes valores para cada elemento da atribuição de função:

* Entidade de segurança
    * Usuários
    * Entidades de serviço
* Definição de função
    * Funções internas
* Escopo
    * Grupos de locatários
    * Locatários
    * Pools de host
    * Grupos de aplicativos

## <a name="built-in-roles"></a>Funções internas

Acesso delegado na área de trabalho Virtual do Windows tem várias definições de função interna, que você pode atribuir a usuários e entidades de serviço.

* Um proprietário de RDS pode gerenciar tudo, incluindo o acesso aos recursos.
* Um colaborador de RDS pode gerenciar tudo, exceto o acesso aos recursos.
* Um leitor de RDS podem ver tudo, mas não é possível fazer nenhuma alteração.
* Um operador de RDS pode exibir as atividades de diagnóstico.

## <a name="powershell-cmdlets-for-role-assignments"></a>Cmdlets do PowerShell para atribuições de função

Você pode executar os seguintes cmdlets para criar, exibir e editar atribuições de função:

* **Get-RdsRoleAssignment** exibe uma lista de atribuições de função.
* **Novo RdsRoleAssignment** cria uma nova atribuição de função.
* **Conjunto RdsRoleAssignment** edita as atribuições de função.

### <a name="accepted-parameters"></a>Parâmetros aceitos

Você pode modificar os três cmdlets básicos com os seguintes parâmetros:

* **AadTenantId**: Especifica a ID de locatário do Active Directory do Azure do qual a entidade de serviço é membro.
* **AppGroupName**: nome do grupo de aplicativo de área de trabalho remota.
* **Diagnóstico**: indica o escopo de diagnóstico. (Deve ser combinado com qualquer um de **infra-estrutura** ou **locatário** parâmetros.)
* **HostPoolName**: nome do pool de host de área de trabalho remota.
* **Infraestrutura**: indica o escopo de infraestrutura.
* **RoleDefinitionName**: nome da função de controle de acesso baseado em função dos serviços de área de trabalho remota atribuída para o usuário, grupo ou aplicativo. (Por exemplo, proprietário de serviços de área de trabalho remota, leitor de serviços de área de trabalho remota e assim por diante.)
* **ServerPrincipleName**: nome do aplicativo do Azure Active Directory.
* **SignInName**: endereço de email do usuário ou nome UPN.
* **Nome do locatário**: nome do locatário de área de trabalho remota.

## <a name="next-steps"></a>Próximas etapas

Para obter uma lista mais completa de cada função pode usar cmdlets do PowerShell, consulte o [referência do PowerShell](/powershell/windows-virtual-desktop/overview).

Para obter diretrizes sobre como configurar um ambiente de área de trabalho Virtual do Windows, consulte [ambiente de visualização de área de trabalho Virtual do Windows](environment-setup.md).
