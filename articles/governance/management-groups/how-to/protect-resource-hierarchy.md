---
title: Como proteger sua hierarquia de recursos – Governança do Azure
description: Saiba como proteger sua hierarquia de recursos com configurações de hierarquia que incluem a definição do grupo de gerenciamento padrão.
ms.date: 02/05/2021
ms.topic: conceptual
ms.openlocfilehash: 0f0afb5401fc646d26598a211604790af191f156
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99594579"
---
# <a name="how-to-protect-your-resource-hierarchy"></a>Como proteger a hierarquia de recursos

Seus recursos, grupos de recursos, assinaturas, grupos de gerenciamento e locatários compõem sua hierarquia de recursos. As configurações no grupo de gerenciamento raiz, como funções personalizadas do Azure ou atribuições de política de Azure Policy, podem afetar todos os recursos em sua hierarquia de recursos. É importante proteger a hierarquia de recursos contra alterações que possam afetar negativamente todos os recursos.

Os grupos de gerenciamento agora têm configurações de hierarquia que permitem ao administrador de locatários controlar esses comportamentos. Este artigo aborda cada uma das configurações de hierarquia disponíveis e como defini-las.

## <a name="azure-rbac-permissions-for-hierarchy-settings"></a>Permissões do RBAC do Azure para configurações de hierarquia

A configuração de qualquer uma das configurações de hierarquia requer as duas operações de provedor de recursos a seguir no grupo de gerenciamento raiz:

- `Microsoft.Management/managementgroups/settings/write`
- `Microsoft.Management/managementgroups/settings/read`

Essas operações só permitem que um usuário leia e atualize as configurações de hierarquia. As operações não fornecem nenhum outro acesso à hierarquia do grupo de gerenciamento ou aos recursos na hierarquia. Ambas as operações estão disponíveis no **administrador de configurações de hierarquia** de funções internas do Azure.

## <a name="setting---default-management-group"></a>Configuração – Grupo de gerenciamento padrão

Por padrão, uma nova assinatura adicionada dentro de um locatário é adicionada como um membro do grupo de gerenciamento raiz. Se as atribuições de política, o controle de acesso baseado em função do Azure (RBAC do Azure) e outras construções de governança forem atribuídas ao grupo de gerenciamento raiz, elas afetarão imediatamente essas novas assinaturas. Por esse motivo, muitas organizações não aplicam esses constructos no grupo de gerenciamento raiz, embora esse seja o local recomendado para atribuí-los. Em outros casos, um conjunto mais restritivo de controles é o mais indicado para novas assinaturas, mas não deve ser atribuído a todas as assinaturas. Essa configuração dá suporte a ambos os casos de uso.

Ao permitir que o grupo de gerenciamento padrão para novas assinaturas seja definido, as construções de governança de toda a organização podem ser aplicadas no grupo de gerenciamento raiz e um grupo de gerenciamento separado com atribuições de política ou atribuições de função do Azure mais adequadas para uma nova assinatura pode ser definido.

### <a name="set-default-management-group-in-portal"></a>Definir grupo de gerenciamento padrão no portal

Para definir essa configuração em portal do Azure, siga estas etapas:

1. Use a barra de pesquisa para procurar e selecionar ' grupos de gerenciamento '.

1. No grupo gerenciamento raiz, selecione **detalhes** ao lado do nome do grupo de gerenciamento.

1. Em **configurações**, selecione **configurações da hierarquia**.

1. Selecione o botão **alterar grupo de gerenciamento padrão** .

   > [!NOTE]
   > Se o botão **alterar grupo de gerenciamento padrão** estiver desabilitado, o grupo de gerenciamento que está sendo exibido não será o grupo de gerenciamento raiz ou sua entidade de segurança não terá as permissões necessárias para alterar as configurações de hierarquia.

1. Selecione um grupo de gerenciamento da sua hierarquia e use o botão **selecionar** .

### <a name="set-default-management-group-with-rest-api"></a>Definir grupo de gerenciamento padrão com a API REST

Para definir essa configuração com a API REST, o ponto de extremidade de [configurações de hierarquia](/rest/api/resources/hierarchysettings) é chamado. Para fazer isso, use os seguintes URI da API REST e formato de corpo. Substitua `{rootMgID}` pela ID do grupo de gerenciamento raiz e `{defaultGroupID}` pela ID do grupo de gerenciamento para se tornar o grupo de gerenciamento padrão:

- URI da API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{rootMgID}/settings/default?api-version=2020-02-01
  ```

- Corpo da solicitação

  ```json
  {
      "properties": {
          "defaultManagementGroup": "/providers/Microsoft.Management/managementGroups/{defaultGroupID}"
      }
  }
  ```

Para definir o grupo de gerenciamento padrão de volta para o grupo de gerenciamento raiz, use o mesmo ponto de extremidade e defina **defaultManagementGroup** como um valor de `/providers/Microsoft.Management/managementGroups/{rootMgID}`.

## <a name="setting---require-authorization"></a>Configuração – Requer autorização

Qualquer usuário, por padrão, pode criar novos grupos de gerenciamento dentro de um locatário. Os administradores de um locatário talvez queiram apenas fornecer essas permissões a usuários específicos para manter a consistência e a conformidade na hierarquia do grupo de gerenciamento. Se habilitada, um usuário solicita a operação `Microsoft.Management/managementGroups/write` no grupo de gerenciamento raiz para criar novos grupos de gerenciamento filho.

### <a name="set-require-authorization-in-portal"></a>Definir exigir autorização no portal

Para definir essa configuração em portal do Azure, siga estas etapas:

1. Use a barra de pesquisa para procurar e selecionar ' grupos de gerenciamento '.

1. No grupo gerenciamento raiz, selecione **detalhes** ao lado do nome do grupo de gerenciamento.

1. Em **configurações**, selecione **configurações da hierarquia**.

1. Alterne a opção **exigir permissões para criar novos grupos de gerenciamento.** opção para ativado.

   > [!NOTE]
   > Se o **exigir permissões para criar novos grupos de gerenciamento.** a alternância está desabilitada, ou o grupo de gerenciamento que está sendo exibido não é o grupo de gerenciamento raiz ou sua entidade de segurança não tem as permissões necessárias para alterar as configurações de hierarquia.

### <a name="set-require-authorization-with-rest-api"></a>Definir exigir autorização com a API REST

Para definir essa configuração com a API REST, o ponto de extremidade de [configurações de hierarquia](/rest/api/resources/hierarchysettings) é chamado. Para fazer isso, use os seguintes URI da API REST e formato de corpo. Esse valor é um _booliano_, portanto, forneça **true** ou **false** para o valor. Um valor de **true** habilita esse método de proteção da hierarquia do grupo de gerenciamento:

- URI da API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{rootMgID}/settings/default?api-version=2020-02-01
  ```

- Corpo da solicitação

  ```json
  {
      "properties": {
          "requireAuthorizationForGroupCreation": true
      }
  }
  ```

Para desativar a configuração, use o mesmo ponto de extremidade e defina **requireAuthorizationForGroupCreation** com o valor **false**.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre grupos de gerenciamento, consulte:

- [Criar grupos de gerenciamento para organizar recursos do Azure](../create-management-group-portal.md)
- [Como alterar, excluir ou gerenciar seus grupos de gerenciamento](../manage.md)