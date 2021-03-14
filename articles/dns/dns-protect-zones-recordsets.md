---
title: Protegendo Zonas DNS e registros-DNS do Azure
description: Neste roteiro de aprendizagem, comece a proteger as zonas DNS e os conjuntos de registros em Microsoft Azure DNS.
services: dns
author: asudbring
ms.service: dns
ms.topic: how-to
ms.date: 2/20/2020
ms.author: allensu
ms.openlocfilehash: 85aaf40237b6b6687c54d4b036f280805c98e7b2
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102618960"
---
# <a name="how-to-protect-dns-zones-and-records"></a>Proteger registros e zonas DNS

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Registros e zonas DNS são recursos críticos. Excluir uma zona DNS ou um único registro DNS pode resultar em uma interrupção do serviço. É importante que as zonas e os registros DNS sejam protegidos contra alterações não autorizadas ou acidentais.

Este artigo explica como o DNS do Azure permite que você proteja suas zonas e registros DNS privados contra essas alterações.  Aplicamos dois recursos de valores mobiliários avançados fornecidos pelo Azure Resource Manager: [controle de acesso baseado em função do Azure (RBAC do Azure)](../role-based-access-control/overview.md) e [bloqueios de recursos](../azure-resource-manager/management/lock-resources.md).

## <a name="azure-role-based-access-control"></a>Controle de acesso baseado em função do Azure

O Azure RBAC (controle de acesso baseado em função) permite o gerenciamento de acesso refinado para usuários, grupos e recursos do Azure. Com o RBAC do Azure, você pode conceder o nível de acesso que os usuários precisam. Para obter mais informações sobre como o RBAC do Azure ajuda você a gerenciar o acesso, consulte [o que é o Azure RBAC (controle de acesso baseado em função)](../role-based-access-control/overview.md).

### <a name="the-dns-zone-contributor-role"></a>A função Colaborador de Zona de DNS

A função colaborador de zona DNS é uma função interna para gerenciar recursos de DNS privados. Essa função aplicada a um usuário ou grupo permite que eles gerenciem recursos DNS.

O grupo de recursos *MyResource* Group contém cinco zonas para a Contoso Corporation. A concessão das permissões de Colaborador de Zona DNS do administrador de DNS a esse grupo de recursos permite controle total sobre essas zonas DNS. Ele evita a concessão de permissões desnecessárias. O administrador de DNS não pode criar ou parar máquinas virtuais.

A maneira mais simples de atribuir permissões de RBAC do Azure é [por meio do portal do Azure](../role-based-access-control/role-assignments-portal.md).  

Abra o **controle de acesso (iam)** para o grupo de recursos, selecione **Adicionar** e, em seguida, selecione a função **colaborador de zona DNS** . Selecione os usuários ou grupos necessários para conceder permissões.

![Azure RBAC no nível do grupo de recursos por meio do portal do Azure](./media/dns-protect-zones-recordsets/rbac1.png)

As permissões também podem ser [concedidas usando o Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group

$usr = "<user email address>"
$rol = "DNS Zone Contributor"
$rsg = "<resource group name>"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg
```

O comando equivalente também está [disponível por meio da CLI do Azure](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group

az role assignment create \
--assignee "<user email address>" \
--role "DNS Zone Contributor" \
--resource-group "<resource group name>"
```

### <a name="zone-level-azure-rbac"></a>RBAC do Azure no nível da zona

É possível aplicar regras de RBAC do Azure a uma assinatura, grupo de recursos ou a um recurso individual. Esse recurso pode ser uma zona DNS individual ou um conjunto de registros individual.

Por exemplo, o grupo de recursos *MyResource* Group contém a zona *contoso.com* e uma subzona *Customers.contoso.com*. Os registros CNAME são criados para cada conta de cliente. A conta de administrador usada para gerenciar registros CNAME recebe permissões para criar registros na zona *Customers.contoso.com* . A conta pode gerenciar somente *Customers.contoso.com* .

As permissões do RBAC do Azure no nível de zona podem ser concedidas por meio do portal do Azure.  Abra o **controle de acesso (iam)** para a zona, selecione **Adicionar**, depois selecione a função **colaborador de zona DNS** e selecione os usuários ou grupos necessários para conceder permissões.

![Nível de zona DNS RBAC do Azure por meio do portal do Azure](./media/dns-protect-zones-recordsets/rbac2.png)

As permissões também podem ser [concedidas usando o Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell
# Grant 'DNS Zone Contributor' permissions to a specific zone

$usr = "<user email address>"
$rol = "DNS Zone Contributor"
$rsg = "<resource group name>"
$zon = "<zone name>"
$typ = "Microsoft.Network/DNSZones"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg -ResourceName $zon -ResourceType $typ
```

O comando equivalente também está [disponível por meio da CLI do Azure](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant 'DNS Zone Contributor' permissions to a specific zone

az role assignment create \
--assignee <user email address> \
--role "DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/DnsZones/<zone name>/"
```

### <a name="record-set-level-azure-rbac"></a>Azure RBAC de nível de conjunto de registros

As permissões são aplicadas no nível do conjunto de registros.  O usuário recebe o controle das entradas necessárias e não consegue fazer nenhuma outra alteração.

Nível de conjunto de registros as permissões RBAC do Azure podem ser configuradas por meio do portal do Azure, usando o botão **controle de acesso (iam)** na página conjunto de registros:

![Nível do conjunto de registros Azure RBAC via portal do Azure](./media/dns-protect-zones-recordsets/rbac3.png)

Nível de conjunto de registros as permissões RBAC do Azure também podem ser [concedidas usando Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell
# Grant permissions to a specific record set

$usr = "<user email address>"
$rol = "DNS Zone Contributor"
$sco = 
"/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -Scope $sco
```

O comando equivalente também está [disponível por meio da CLI do Azure](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant permissions to a specific record set

az role assignment create \
--assignee "<user email address>" \
--role "DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"
```

### <a name="custom-roles"></a>Funções personalizadas

A função Colaborador de Zona de DNS interna permite controle total sobre um recurso DNS. É possível criar suas próprias funções personalizadas do Azure para fornecer um controle mais refinado.

A conta usada para gerenciar CNAMEs recebe permissão para gerenciar somente registros CNAME. A conta não pode modificar os registros de outros tipos. A conta não pode fazer operações em nível de zona, como exclusão de zona.

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
        "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
}
```

A propriedade Actions define as seguintes permissões específicas do DNS:

* `Microsoft.Network/dnsZones/CNAME/*` concede controle total sobre os registros CNAME
* `Microsoft.Network/dnsZones/read` concede permissão para ler as zonas DNS, mas não para modificá-las, permitindo que você veja a zona na qual o CNAME está sendo criado.

As Actions restantes são copiadas da [função interna de Colaborador de Zona DNS](../role-based-access-control/built-in-roles.md#dns-zone-contributor).

> [!NOTE]
> Usar uma função personalizada do Azure para impedir a exclusão de conjuntos de registros enquanto ainda permite que eles sejam atualizados não é um controle efetivo. Isso impede que os conjuntos de registro sejam excluídos, mas não impede que sejam modificados.  As modificações permitidas incluem adicionar e remover registros do conjunto de registros, incluindo a remoção de todos os registros para deixar um conjunto de registros vazio. Do ponto de vista de resolução de DNS, isso tem o mesmo efeito de excluir o registro.

Definições de função personalizadas não podem ser definidas no momento por meio do portal do Azure. Uma função personalizada com base nessa definição de função pode ser criada usando o Azure PowerShell:

```azurepowershell
# Create new role definition based on input file
New-AzRoleDefinition -InputFile <file path>
```

Ela também pode ser criada por meio da CLI do Azure:

```azurecli
# Create new role definition based on input file
az role create -inputfile <file path>
```

A função pode então ser atribuída da mesma forma que as funções internas, conforme descrito anteriormente neste artigo.

Para obter mais informações sobre como criar, gerenciar e atribuir funções personalizadas, consulte [funções personalizadas do Azure](../role-based-access-control/custom-roles.md).

## <a name="resource-locks"></a>Bloqueios de recurso

Azure Resource Manager dá suporte a outro tipo de controle de segurança, a capacidade de bloquear recursos. Os bloqueios de recurso são aplicados ao recurso e são efetivos entre todos os usuários e funções. Para obter mais informações, consulte [Bloquear recursos com Azure Resource Manager](../azure-resource-manager/management/lock-resources.md).

Há dois tipos de bloqueio de recurso: **CanNotDelete** e **ReadOnly**. Esses tipos de bloqueio podem ser aplicados a uma zona DNS privado ou a um conjunto de registros individual. As seções a seguir descrevem vários cenários comuns e como dar suporte a eles usando bloqueios de recurso.

### <a name="protecting-against-all-changes"></a>Proteção contra todas as alterações

Para evitar que as alterações sejam feitas, aplique um bloqueio ReadOnly à zona. Esse bloqueio impede que novos conjuntos de registros sejam criados, e conjuntos de registros existentes sejam modificados ou excluídos.

Bloqueios de recurso em nível de zona podem ser criados via Portal do Azure.  Na página da zona DNS, selecione **Bloqueios** e, em seguida, selecione **+Adicionar**:

![Bloqueios de recurso em nível de zona via Portal do Azure](./media/dns-protect-zones-recordsets/locks1.png)

Bloqueios de recursos de nível de zona também podem ser criados por meio de [Azure PowerShell](/powershell/module/az.resources/new-azresourcelock):

```azurepowershell
# Lock a DNS zone

$lvl = "<lock level>"
$lnm = "<lock name>"
$rsc = "<zone name>"
$rty = "Microsoft.Network/DNSZones"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rsc -ResourceType $rty -ResourceGroupName $rsg
```

O comando equivalente também está [disponível por meio da CLI do Azure](/cli/azure/lock#az-lock-create):

```azurecli
# Lock a DNS zone

az lock create \
--lock-type "<lock level>" \
--name "<lock name>" \
--resource-name "<zone name>" \
--namespace "Microsoft.Network" \
--resource-type "DnsZones" \
--resource-group "<resource group name>"
```

### <a name="protecting-individual-records"></a>Protegendo registros individuais

Para impedir que um conjunto de registros DNS existente sofra modificações, aplique um bloqueio ReadOnly ao conjunto de registros.

> [!NOTE]
> Aplicar um bloqueio CanNotDelete a um conjunto de registros não é um controle efetivo. Ele impede que os conjuntos de registro sejam excluídos, mas não impede que sejam modificados.  As modificações permitidas incluem adicionar e remover registros do conjunto de registros, incluindo a remoção de todos os registros para deixar um conjunto de registros vazio. Do ponto de vista de resolução de DNS, isso tem o mesmo efeito de excluir o registro.

Atualmente, os bloqueios de recurso em nível de conjunto de registros só podem ser configurados pelo uso do Azure PowerShell.  Eles não têm suporte no portal do Azure ou CLI do Azure.

```azurepowershell
# Lock a DNS record set

$lvl = "<lock level>"
$lnm = "<lock name>"
$rsc = "<zone name>/<record set name>"
$rty = "Microsoft.Network/DNSZones/<record type>"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rsc -ResourceType $rty -ResourceGroupName $rsg
```

### <a name="protecting-against-zone-deletion"></a>Proteção contra a exclusão de zona

Quando uma zona é excluída no DNS do Azure, todos os conjuntos de registros na zona são excluídos.  Esta operação não pode ser desfeita. A exclusão acidental de uma zona crítica tem o potencial para ter um impacto significativo nos negócios.  É importante proteger contra a exclusão acidental de zona.

Aplicar um bloqueio CanNotDelete a uma zona impede que a zona seja excluída. Os bloqueios são herdados por recursos filho. Um bloqueio impede que qualquer conjunto de registros na zona seja excluído. Conforme descrito na observação acima, ele é ineficaz, pois os registros ainda podem ser removidos dos conjuntos de registros existentes.

Como alternativa, aplique um bloqueio CanNotDelete a um conjunto de registros na zona, como o conjunto de registros SOA. A zona não é excluída sem excluir também os conjuntos de registros. Esse bloqueio protege contra a exclusão de zona e, ao mesmo tempo, permite que os conjuntos de registros dentro da zona sejam modificados livremente. Se for feita uma tentativa de excluir a zona, Azure Resource Manager detectará essa remoção. A remoção também excluiria o conjunto de registros SOA, Azure Resource Manager bloquear a chamada porque a SOA está bloqueada.  Nenhum conjunto de registros é excluído.

O seguinte comando do PowerShell cria um bloqueio CanNotDelete no registro SOA da zona especificada:

```azurepowershell
# Protect against zone delete with CanNotDelete lock on the record set

$lvl = "CanNotDelete"
$lnm = "<lock name>"
$rsc = "<zone name>/@"
$rty = "Microsoft.Network/DNSZones/SOA"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rsc -ResourceType $rty -ResourceGroupName $rsg
```

Outra opção para impedir a exclusão acidental de zona é usar uma função personalizada. Essa função garante que as contas usadas para gerenciar suas zonas não tenham permissões de exclusão de zona. 

Quando você precisa excluir uma zona, é possível impor uma exclusão de duas etapas:

 - Primeiro, conceder permissões de exclusão de zona
 - Em segundo lugar, conceda permissões para excluir a zona.

A função personalizada funciona para todas as zonas acessadas por essas contas. Contas com permissões de exclusão de zona, como o proprietário da assinatura, ainda podem excluir uma zona acidentalmente.

É possível usar as duas abordagens – bloqueios de recursos e funções personalizadas – ao mesmo tempo, como uma abordagem de defesa aprofundada para a proteção de zona DNS.

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre como trabalhar com o RBAC do Azure, consulte [o que é o Azure RBAC (controle de acesso baseado em função do Azure)](../role-based-access-control/overview.md).
* Para obter mais informações sobre trabalho com bloqueios de recurso, confira [Bloquear recursos com o Azure Resource Manager](../azure-resource-manager/management/lock-resources.md).
