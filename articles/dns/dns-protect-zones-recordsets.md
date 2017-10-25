---
title: Protegendo Registros e Zonas DNS | Microsoft Docs
description: Como proteger conjuntos de registros e zonas DNS no DNS do Microsoft Azure.
services: dns
documentationcenter: na
author: jtuliani
manager: carmonm
ms.assetid: 190e69eb-e820-4fc8-8e9a-baaf0b3fb74a
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/20/2016
ms.author: jonatul
ms.openlocfilehash: 0b7040d6273b3a6b85cd55850d596807226b87fc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-protect-dns-zones-and-records"></a>Proteger registros e zonas DNS

Registros e zonas DNS são recursos críticos. Excluir uma zona DNS ou até mesmo um único registro DNS pode resultar em uma interrupção total do serviço.  Portanto, é importante que registros e zonas DNS críticos sejam protegidos contra alterações acidentais ou não autorizadas.

Este artigo explica como o DNS do Azure permite que você proteja seus registros e zonas DNS contra essas alterações.  Podemos aplicar dois recursos de segurança avançados fornecidos pelo Azure Resource Manager: [controle de acesso baseado em função](../active-directory/role-based-access-control-what-is.md) e [bloqueios de recurso](../azure-resource-manager/resource-group-lock-resources.md).

## <a name="role-based-access-control"></a>Controle de acesso baseado em função

O RBAC (controle de acesso baseado em função) do Azure permite o gerenciamento de acesso refinado para o usuários, grupos e recursos do Azure. Usando o RBAC, você pode conceder exatamente a quantidade de acesso que os usuários precisam para realizar seus trabalhos. Para obter mais informações sobre como o RBAC ajuda você a gerenciar o acesso, veja [O que é Controle de Acesso Baseado em Função](../active-directory/role-based-access-control-what-is.md).

### <a name="the-dns-zone-contributor-role"></a>A função 'Colaborador de zona DNS'

A função 'Colaborador de zona DNS' é uma função interna fornecida pelo Azure para gerenciamento de recursos DNS.  Atribuir permissões de Colaborador de Zona DNS a um usuário ou grupo permite que esse grupo gerencie recursos DNS, mas não os recursos de qualquer outro tipo.

Por exemplo, suponha que o grupo de recursos 'myzones' contém cinco zonas para a Contoso Corporation. Conceder permissões de 'Colaborador de Zona DNS' para esse grupo de recursos ao administrador de DNS habilita o controle total sobre essas zonas DNS. Isso também evita que permissões desnecessárias sejam concedidas, por exemplo, o administrador de DNS não pode criar ou parar máquinas virtuais.

A maneira mais simples de atribuir permissões de RBAC é [por meio do Portal do Azure](../active-directory/role-based-access-control-configure.md).  Abra a folha de 'Controle de acesso (IAM)' para o grupo de recursos, em seguida, clique em 'Adicionar', selecione a função 'Colaborador de zona DNS' e selecione os usuários ou grupos aos quais é necessário conceder permissões.

![RBAC de nível do grupo de recursos por meio do Portal do Azure](./media/dns-protect-zones-recordsets/rbac1.png)

As permissões também podem ser [concedidas usando o Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md):

```powershell
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group
New-AzureRmRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -ResourceGroupName "<resource group name>"
```

O comando equivalente também está [disponível por meio da CLI do Azure](../active-directory/role-based-access-control-manage-access-azure-cli.md):

```azurecli
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group
azure role assignment create --signInName "<user email address>" --roleName "DNS Zone Contributor" --resourceGroup "<resource group name>"
```

### <a name="zone-level-rbac"></a>RBAC de nível de zona

É possível aplicar regras de RBAC do Azure a uma assinatura, grupo de recursos ou a um recurso individual. No caso do DNS do Azure, esse recurso pode ser uma zona DNS individual ou até mesmo um conjunto de registros individual.

Por exemplo, suponha que o grupo de recursos 'myzones' contém a zona 'contoso.com' e uma subzona 'customers.contoso.com' na qual os registros CNAME são criados para cada conta de cliente.  A conta usada para gerenciar esses registros CNAME deve ter permissões para criar registros somente na zona 'customers.contoso.com', ele não deve ter acesso às outras zonas.

É possível conceder permissões de nível da zona RBAC por meio do Portal do Azure.  Abra a folha de 'Controle de acesso (IAM)' para a zona, em seguida, clique em 'Adicionar', selecione a função 'Colaborador de zona DNS' e selecione os usuários ou grupos aos quais é necessário conceder permissões.

![RBAC de nível da Zona DNS por meio do Portal do Azure](./media/dns-protect-zones-recordsets/rbac2.png)

As permissões também podem ser [concedidas usando o Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md):

```powershell
# Grant 'DNS Zone Contributor' permissions to a specific zone
New-AzureRmRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -ResourceGroupName "<resource group name>" -ResourceName "<zone name>" -ResourceType Microsoft.Network/DNSZones
```

O comando equivalente também está [disponível por meio da CLI do Azure](../active-directory/role-based-access-control-manage-access-azure-cli.md):

```azurecli
# Grant 'DNS Zone Contributor' permissions to a specific zone
azure role assignment create --signInName <user email address> --roleName "DNS Zone Contributor" --resource-name <zone name> --resource-type Microsoft.Network/DNSZones --resource-group <resource group name>
```

### <a name="record-set-level-rbac"></a>RBAC de nível do conjunto de registros

Podemos ir uma etapa adiante. Considere o administrador do email para a Contoso Corporation, que precisa acessar os registros MX e TXT no ápice da zona 'contoso.com'.  Ela não precisa ter acesso a nenhum outro registro MX, TXT nem de nenhum outro tipo.  O DNS do Azure permite que você atribua permissões no nível do conjunto de registros, exatamente aos registros aos quais o administrador de email precisa ter acesso.  O administrador de email recebe exatamente o controle de que precisa e não pode fazer nenhuma outra alteração.

Permissões de RBAC de nível do conjunto de registros podem ser configuradas por meio do Portal do Azure, usando o botão 'Usuários' na folha do conjunto de registros:

![RBAC de nível do conjunto de registros por meio do Portal do Azure](./media/dns-protect-zones-recordsets/rbac3.png)

As permissões RBAC de nível do conjunto de registros também podem ser [concedidas usando o Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md):

```powershell
# Grant permissions to a specific record set
New-AzureRmRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -Scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"
```

O comando equivalente também está [disponível por meio da CLI do Azure](../active-directory/role-based-access-control-manage-access-azure-cli.md):

```azurecli
# Grant permissions to a specific record set
azure role assignment create --signInName "<user email address>" --roleName "DNS Zone Contributor" --scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"
```

### <a name="custom-roles"></a>Funções personalizadas

A função interna 'Colaborador de Zona DNS' habilita o controle total sobre um recurso DNS. Também é possível criar suas próprias funções de cliente do Azure, para fornecer um controle ainda mais refinado.

Considere novamente o exemplo em que um registro CNAME na zona 'customers.contoso.com' é criado para cada conta de cliente da Contoso Corporation.  A conta usada para gerenciar esses CNAMEs deve ter permissão para gerenciar apenas registros CNAME.  Ela não pode, portanto, modificar os registros de outros tipos (como alterar os registros MX) nem executar operações em nível de zona, como a exclusão de zona.

O exemplo a seguir mostra uma definição de função personalizada para gerenciar apenas registros CNAME:

```json
{
    "Name": "DNS CNAME Contributor",
    "Id": "",
    "IsCustom": true,
    "Description": "Can manage DNS CNAME records only.",
    "Actions": [
        "Microsoft.Network/dnsZones/CNAME/*",
        "Microsoft.Network/dnsZones/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
    ],
    "NotActions": [
    ],
    "AssignableScopes": [
        "/subscriptions/ c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
}
```

A propriedade Actions define as seguintes permissões específicas do DNS:

* `Microsoft.Network/dnsZones/CNAME/*` concede controle total sobre os registros CNAME
* `Microsoft.Network/dnsZones/read` concede permissão para ler as zonas DNS, mas não para modificá-las, permitindo que você veja a zona na qual o CNAME está sendo criado.

As Actions restantes são copiadas da [função interna de Colaborador de Zona DNS](../active-directory/role-based-access-built-in-roles.md#dns-zone-contributor).

> [!NOTE]
> Usando uma função RBAC personalizada para evitar a exclusão de conjuntos de registros permitindo simultaneamente que eles sejam atualizados não é um controle efetivo. Isso impede que os conjuntos de registro sejam excluídos, mas não impede que sejam modificados.  As modificações permitidas incluem adicionar e remover registros do conjunto de registros, incluindo a remoção de todos os registros para deixar um conjunto de registros 'vazio'. Do ponto de vista de resolução de DNS, isso tem o mesmo efeito de excluir o registro.

Definições de função personalizadas não podem ser definidas por meio do Portal do Azure no momento. Uma função personalizada com base nessa definição de função pode ser criada usando o Azure PowerShell:

```powershell
# Create new role definition based on input file
New-AzureRmRoleDefinition -InputFile <file path>
```

Ela também pode ser criada por meio da CLI do Azure:

```azurecli
# Create new role definition based on input file
azure role create -inputfile <file path>
```

A função pode então ser atribuída da mesma forma que as funções internas, conforme descrito anteriormente neste artigo.

Para obter mais informações sobre como criar, gerenciar e atribuir funções personalizadas, veja [Funções personalizadas no RBAC do Azure](../active-directory/role-based-access-control-custom-roles.md).

## <a name="resource-locks"></a>Bloqueios de recurso

Além do RBAC, Azure Resource Manager dá suporte a outro tipo de controle de segurança, que é a capacidade de 'bloquear' recursos. Onde as regras RBAC permitem controlar as ações de usuários e grupos específicos, bloqueios de recurso são aplicados ao recurso e entram em vigor para todos os usuários e funções. Para saber mais, confira [Bloquear recursos com o Gerenciador de Recursos do Azure](../azure-resource-manager/resource-group-lock-resources.md).

Há dois tipos de bloqueio de recurso: **DoNotDelete** e **ReadOnly**. Eles podem ser aplicados a uma zona DNS ou a um conjunto de registros individual.  As seções a seguir descrevem vários cenários comuns e como dar suporte a eles usando bloqueios de recurso.

### <a name="protecting-against-all-changes"></a>Proteção contra todas as alterações

Para impedir que quaisquer alterações sejam feitas, aplique um bloqueio ReadOnly à zona.  Isso impede que novos conjuntos de registros sejam criados e que os existentes sejam modificados ou excluídos.

Bloqueios de recurso em nível de zona podem ser criados via Portal do Azure.  Na folha da zona DNS, clique em 'Bloqueios' e, em seguida, em 'Adicionar':

![Bloqueios de recurso em nível de zona via Portal do Azure](./media/dns-protect-zones-recordsets/locks1.png)

Bloqueios de recurso em nível de zona podem ser criados via Azure PowerShell:

```powershell
# Lock a DNS zone
New-AzureRmResourceLock -LockLevel <lock level> -LockName <lock name> -ResourceName <zone name> -ResourceType Microsoft.Network/DNSZones -ResourceGroupName <resource group name>
```

Não há suporte para configurar os bloqueios de recurso do Azure por meio da CLI do Azure.

### <a name="protecting-individual-records"></a>Protegendo registros individuais

Para impedir que um conjunto de registros DNS existente sofra modificações, aplique um bloqueio ReadOnly ao conjunto de registros.

> [!NOTE]
> Aplicar um bloqueio DoNotDelete a um conjunto de registros não é um controle efetivo. Ele impede que os conjuntos de registro sejam excluídos, mas não impede que sejam modificados.  As modificações permitidas incluem adicionar e remover registros do conjunto de registros, incluindo a remoção de todos os registros para deixar um conjunto de registros 'vazio'. Do ponto de vista de resolução de DNS, isso tem o mesmo efeito de excluir o registro.

Atualmente, os bloqueios de recurso em nível de conjunto de registros só podem ser configurados pelo uso do Azure PowerShell.  Eles não têm suporte no Portal do Azure nem na CLI do Azure.

```powershell
# Lock a DNS record set
New-AzureRmResourceLock -LockLevel <lock level> -LockName "<lock name>" -ResourceName "<zone name>/<record set name>" -ResourceType "Microsoft.Network/DNSZones/<record type>" -ResourceGroupName "<resource group name>"
```

### <a name="protecting-against-zone-deletion"></a>Proteção contra a exclusão de zona

Quando uma zona é excluída no DNS do Azure, todos os conjuntos de registros na zona também são excluídos.  Essa operação não pode ser desfeita.  A exclusão acidental de uma zona crítica tem o potencial para ter um impacto significativo nos negócios.  Portanto, é muito importante proteger-se contra a exclusão acidental de zona.

Aplicar um bloqueio DoNotDelete a uma zona impede que a zona seja excluída.  No entanto, como os bloqueios são herdados pelos recursos filho, ele também impede que eventuais conjuntos de registros na zona sejam excluídos, o que pode ser indesejável.  Além disso, conforme descrito na observação acima, ele é também ineficaz, pois registros ainda podem ser removidos dos conjuntos de registros existentes.

Como alternativa, considere aplicar um bloqueio DoNotDelete em um conjunto de registros na zona, como o conjunto de registros SOA.  Uma vez que a zona não pode ser excluída sem excluir também os conjuntos de registros, isso protege contra a exclusão de zona embora ainda permita que conjuntos de registros dentro da zona sejam modificados livremente. Se ocorrer uma tentativa de excluir a zona, o Azure Resource Manager detectará isso e também excluirá o conjunto de registros SOA, além de bloquear a chamada devido ao estado bloqueado do SOA.  Nenhum conjunto de registros é excluído.

O comando do PowerShell a seguir cria um bloqueio DoNotDelete para o registro SOA da zona fornecida:

```powershell
# Protect against zone delete with DoNotDelete lock on the record set
New-AzureRmResourceLock -LockLevel DoNotDelete -LockName "<lock name>" -ResourceName "<zone name>/@" -ResourceType" Microsoft.Network/DNSZones/SOA" -ResourceGroupName "<resource group name>"
```

Outra maneira de evitar a exclusão acidental de zonas é usando uma função personalizada para garantir que o operador e as contas de serviço usados para gerenciar as zonas não tenham permissões de exclusão. Quando você precisa excluir uma zona, pode impor uma exclusão em duas etapas, primeiro concedendo permissões de exclusão de zonas (no escopo de zona, para evitar excluir a zona errada) e depois para excluir a zona.

Essa segunda abordagem tem a vantagem de funcionar para todas as zonas acessadas por essas contas, sem ter que se lembrar de criar bloqueios. Ela tem a desvantagem de que todas as contas com permissões de exclusão de zonas, como o proprietário da assinatura, ainda podem excluir acidentalmente uma zona crítica.

É possível usar as duas abordagens - bloqueios de recursos e funções personalizadas - ao mesmo tempo, como abordagem de defesa detalhada para a proteção de zonas DNS.

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre como trabalhar com o RBAC, veja [Introdução ao gerenciamento de acesso no Portal do Azure](../active-directory/role-based-access-control-what-is.md).
* Para obter mais informações sobre trabalho com bloqueios de recurso, confira [Bloquear recursos com o Azure Resource Manager](../azure-resource-manager/resource-group-lock-resources.md).

