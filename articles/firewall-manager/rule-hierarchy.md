---
title: Usar a política de firewall do Azure para definir uma hierarquia de regra
description: Saiba como usar a política de firewall do Azure para definir uma hierarquia de regra e impor a conformidade.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: how-to
ms.date: 08/26/2020
ms.author: victorh
ms.openlocfilehash: 1ba683e3d616f52854f1055dab9b9fe2d389116a
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92331729"
---
# <a name="use-azure-firewall-policy-to-define-a-rule-hierarchy"></a>Usar a política de firewall do Azure para definir uma hierarquia de regra

Os administradores de segurança precisam gerenciar firewalls e garantir a conformidade entre implantações locais e na nuvem. Um componente fundamental é a capacidade de fornecer às equipes de aplicativos flexibilidade para implementar pipelines de CI/CD para criar regras de firewall de maneira automatizada.

A política de firewall do Azure permite que você defina uma hierarquia de regra e garanta a conformidade:

- Fornece uma estrutura hierárquica para sobrepor uma política base central sobre uma política de equipe de aplicativo filho. A política de base tem uma prioridade mais alta e é executada antes da política filho.
- Use uma definição de função personalizada do Azure para evitar a remoção inadvertida da política base e fornecer acesso seletivo a grupos de coleção de regras em uma assinatura ou grupo de recursos. 

## <a name="solution-overview"></a>Visão geral da solução

As etapas de alto nível para este exemplo são:

1. Crie uma política de firewall base no grupo de recursos da equipe de segurança. 
3. Defina regras específicas de segurança de ti na política de base. Isso adiciona um conjunto comum de regras para permitir/negar tráfego.
4. Crie políticas de equipe de aplicativo que herdam a política de base. 
5. Defina regras específicas da equipe de aplicativo na política. Você também pode migrar regras de firewalls pré-existentes.
6. Crie Azure Active Directory funções personalizadas para fornecer acesso refinado ao grupo de coleta de regras e adicionar funções em um escopo de política de firewall. No exemplo a seguir, os membros da equipe de vendas podem editar grupos de coleção de regras para a política de firewall das equipes de vendas. O mesmo se aplica ao banco de dados e às equipes de engenharia.
7. Associe a política ao firewall correspondente. Um firewall do Azure pode ter apenas uma política atribuída. Isso exige que cada equipe de aplicativo tenha seu próprio firewall.



:::image type="content" source="media/rule-hierarchy/contoso-teams.png" alt-text="Equipes e requisitos" border="false":::

### <a name="create-the-firewall-policies"></a>Criar as políticas de firewall

- Uma política de firewall base.

Crie políticas para cada uma das equipes de aplicativos:

- Uma política de firewall de vendas. A política de firewall de vendas herda a política de base de firewall.
- Uma política de firewall de banco de dados. A política de firewall de banco de dados herda a política de firewall base.
- Uma política de firewall de engenharia. A política de firewall de engenharia também herda a política base de firewall.

:::image type="content" source="media/rule-hierarchy/policy-hierarchy.png" alt-text="Equipes e requisitos" border="false":::

### <a name="create-custom-roles-to-access-the-rule-collection-groups"></a>Criar funções personalizadas para acessar os grupos de coleção de regras 

As funções personalizadas são definidas para cada equipe de aplicativo. A função define as operações e o escopo. As equipes de aplicativos têm permissão para editar grupos de coleções de regras para seus respectivos aplicativos.

Use o seguinte procedimento de alto nível para definir funções personalizadas:

1. Obter a assinatura:

   `Select-AzSubscription -SubscriptionId xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxxx`
2. Execute o seguinte comando:

   `Get-AzProviderOperation "Microsoft.Support/*" | FT Operation, Description -AutoSize`
3. Use o comando Get-AzRoleDefinition para gerar a função leitor no formato JSON. 

   `Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\CustomRoles\ReaderSupportRole.json`
4. Abra o ReaderSupportRole.jsno arquivo em um editor.

   O exemplo a seguir mostra a saída JSON. Para obter informações sobre as diferentes propriedades, consulte [funções personalizadas do Azure](../role-based-access-control/custom-roles.md).

```json
   { 
     "Name": "Reader", 
     "Id": "acdd72a7-3385-48ef-bd42-f606fba81ae7", 
     "IsCustom": false, 
     "Description": "Lets you view everything, but not make any changes.", 
     "Actions": [ 
      "*/read" 
     ], 
     "NotActions": [], 
     "DataActions": [], 
     "NotDataActions": [], 
     "AssignableScopes": [ 
       "/" 
     ] 
   } 
```
5. Edite o arquivo JSON para adicionar o 

   `*/read", "Microsoft.Network/*/read", "Microsoft.Network/firewallPolicies/ruleCollectionGroups/write` 

   operação para a propriedade **Actions**   . Inclua uma vírgula após a operação de leitura. Essa ação permite que o usuário crie e atualize grupos de coleção de regras.
6. No **AssignableScopes**, adicione sua ID de assinatura com o seguinte formato: 

   `/subscriptions/xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxxx`

   Você deve adicionar IDs de assinatura explícitas; caso contrário, não será possível importar a função para a assinatura.
7. Exclua a linha de propriedade **ID**   e altere a propriedade **IsCustom**   para true.
8. Altere as **Name**   Propriedades nome e **Descrição**   para o grupo de coleção de *regras AZFM autor* e *os usuários nessa função podem editar grupos de coleta de regras de política de firewall*

O arquivo JSON deve ser semelhante ao exemplo a seguir:

```
{ 

    "Name":  "AZFM Rule Collection Group Author", 
    "IsCustom":  true, 
    "Description":  "Users in this role can edit Firewall Policy rule collection groups", 
    "Actions":  [ 
                    "*/read", 
                    "Microsoft.Network/*/read", 
                     "Microsoft.Network/firewallPolicies/ruleCollectionGroups/write" 
                ], 
    "NotActions":  [ 
                   ], 
    "DataActions":  [ 
                    ], 
    "NotDataActions":  [ 
                       ], 
    "AssignableScopes":  [ 
                             "/subscriptions/xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxxx"] 
} 
```
9. Para criar a nova função personalizada, use o comando New-AzRoleDefinition e especifique o arquivo de definição de função JSON. 

   `New-AzRoleDefinition -InputFile "C:\CustomRoles\RuleCollectionGroupRole.json`

### <a name="list-custom-roles"></a>Listar funções personalizadas

Para listar todas as funções personalizadas, você pode usar o comando Get-AzRoleDefinition:

   `Get-AzRoleDefinition | ? {$_.IsCustom -eq $true} | FT Name, IsCustom`

Você também pode ver as funções personalizadas na portal do Azure. Acesse sua assinatura, selecione **controle de acesso (iam)**, **funções**.

:::image type="content" source="media/rule-hierarchy/sales-app-policy.png" alt-text="Equipes e requisitos":::

:::image type="content" source="media/rule-hierarchy/sales-app-policy-read.png" alt-text="Equipes e requisitos":::

Para obter mais informações, consulte [tutorial: criar uma função personalizada do Azure usando Azure PowerShell](../role-based-access-control/tutorial-custom-role-powershell.md).

### <a name="add-users-to-the-custom-role"></a>Adicionar usuários à função personalizada

No portal, você pode adicionar usuários ao grupo de coleção de regras AZFM função autores e fornecer acesso às políticas de firewall.

1. No portal, selecione a política de firewall da equipe de aplicativos (por exemplo, SalesAppPolicy).
2. Selecione **controle de acesso**.
3. Selecione **Adicionar atribuição de função**.
4. Adicione usuários/grupos de usuários (por exemplo, a equipe de vendas) à função.

Repita esse procedimento para as outras políticas de firewall.

### <a name="summary"></a>Resumo

A política de firewall com funções personalizadas agora fornece acesso seletivo a grupos de coleção de regras de política de firewall.

Os usuários não têm permissões para:
- Exclua a política de firewall ou firewall do Azure.
- Atualize a hierarquia de política de firewall ou as configurações de DNS ou a inteligência contra ameaças.
- Atualize a política de firewall em que elas não são membros do grupo de autor do grupo de coleta de regras AZFM.

Os administradores de segurança podem usar a política base para impor guardrails e bloquear determinados tipos de tráfego (por exemplo, ICMP), conforme exigido pela empresa. 

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a [política de firewall do Azure](policy-overview.md).

