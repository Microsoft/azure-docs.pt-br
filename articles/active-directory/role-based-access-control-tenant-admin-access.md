---
title: "Acesso elevado de administrador de locatários — Azure AD | Microsoft Docs"
description: "Este tópico descreve as funções internas para o RBAC (controle de acesso baseado em função)."
services: active-directory
documentationcenter: 
author: andredm7
manager: femila
editor: rqureshi
ms.assetid: b547c5a5-2da2-4372-9938-481cb962d2d6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andredm
ms.openlocfilehash: 22b62be1773c5042ecf6ee078e68a4ffdf791d53
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="elevate-access-as-a-tenant-admin-with-role-based-access-control"></a>Elevar o acesso como um administrador de locatários com Controle de Acesso Baseado em Função

O Controle de Acesso Baseado em Função ajuda os administradores de locatários a obter elevações temporárias no acesso para que eles possam conceder permissões mais elevadas que o normal. Um administrador de locatários pode elevar a si mesmo para a função de Administrador de Acesso do Usuário quando necessário. Essa função fornece ao administrador de locatários permissões para conceder a si mesmo ou a outras funções no escopo "/".

Esse recurso é importante porque permite que o administrador de locatários veja todas as assinaturas que existem em uma organização. Também permite aos aplicativos de automação (como faturamento e auditoria) acessar todas as assinaturas e fornecer uma exibição precisa do estado da organização em termos de gerenciamento de ativos ou cobrança.  

## <a name="how-to-use-elevateaccess-for-tenant-access-with-azure-ad-admin-center"></a>Como usar elevateAccess para acesso de locatário com o centro de administração do Azure AD

No [Centro de administração do Azure Active Directory](https://aad.portal.azure.com), você pode invocar esse recurso de **Propriedades**.
O recurso é chamado de **Administrador global pode gerenciar assinaturas do Azure**. A impressão é que essa é uma Propriedade Global para o Azure Active Directory, no entanto, ela funciona por usuário para o usuário conectado no momento. Quando você tiver direitos de Administrador Global no Azure Active Directory, poderá invocar o recurso elevateAccess para o usuário com o qual você está conectado no momento no Centro de Administração do Azure Active Directory.

Selecionar **Sim** e então **Salvar**: isso **Atribui** a função **Administrador de Acesso do Usuário** na Raiz "/" (Escopo Raiz) ao usuário com o qual você está conectado no momento no Portal.

Selecionar **Não** e então **Salvar**: isso **Remove** a função **Administrador de Acesso do Usuário** na Raiz "/" (Escopo Raiz) ao usuário com o qual você está conectado no momento no Portal.

![Centro de administração do Azure AD – Propriedades – Globaladmin pode gerenciar a Assinatura do Azure – captura de tela](./media/role-based-access-control-tenant-admin-access/aad-azure-portal-global-admin-can-manage-azure-subscriptions.png)

## <a name="how-to-use-elevateaccess-to-give-tenant-access-with-the-rest-api"></a>Como usar elevateAccess para fornecer acesso de locatário com a API REST

O processo básico funciona com as seguintes etapas:

1. Usando REST, chame *elevateAccess*, que concede a você a função Administrador de Acesso do Usuário no escopo "/".

    ```
    POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
    ```

2. Crie uma [atribuição de função](/rest/api/authorization/roleassignments) para atribuir qualquer função em qualquer escopo. O exemplo a seguir mostra as propriedades para atribuir a função Leitor no escopo "/":

    ```
    { "properties":{
    "roleDefinitionId": "providers/Microsoft.Authorization/roleDefinitions/acdd72a7338548efbd42f606fba81ae7",
    "principalId": "cbc5e050-d7cd-4310-813b-4870be8ef5bb",
    "scope": "/"
    },
    "id": "providers/Microsoft.Authorization/roleAssignments/64736CA0-56D7-4A94-A551-973C2FE7888B",
    "type": "Microsoft.Authorization/roleAssignments",
    "name": "64736CA0-56D7-4A94-A551-973C2FE7888B"
    }
    ```

3. Enquanto um Administrador de Acesso do Usuário, você também pode excluir a atribuição de função no escopo "/".

4. Revogue seus privilégios de Administrador de Acesso do Usuário até que sejam necessários novamente.


## <a name="how-to-undo-the-elevateaccess-action-with-the-rest-api"></a>Como desfazer a ação elevateAccess com a API REST

Ao chamar *elevateAccess*, você cria uma atribuição de função para si mesmo, de modo que para revogar esses privilégios é preciso excluir a atribuição.

1.  Chame [GET roleDefinitions](/rest/api/authorization/roledefinitions#RoleDefinitions_Get), em que roleName = Administrador de Acesso do Usuário determina o GUID do nome da função de Administrador de Acesso do Usuário. A resposta deve se parecer com esta:

    ```
    {"value":[{"properties":{
    "roleName":"User Access Administrator",
    "type":"BuiltInRole",
    "description":"Lets you manage user access to Azure resources.",
    "assignableScopes":["/"],
    "permissions":[{"actions":["*/read","Microsoft.Authorization/*","Microsoft.Support/*"],"notActions":[]}],
    "createdOn":"0001-01-01T08:00:00.0000000Z",
    "updatedOn":"2016-05-31T23:14:04.6964687Z",
    "createdBy":null,
    "updatedBy":null},
    "id":"/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
    "type":"Microsoft.Authorization/roleDefinitions",
    "name":"18d7d88d-d35e-4fb5-a5c3-7773c20a72d9"}],
    "nextLink":null}
    ```

    Salve o GUID do parâmetro *name*, neste caso **18d7d88d-d35e-4fb5-a5c3-7773c20a72d9**.

2. Chame [GET roleAssignments](/rest/api/authorization/roleassignments#RoleAssignments_Get), em que principalId = sua própria ObjectId. Isso lista todas as suas atribuições no locatário. Procure aquela em que o escopo é "/" e a RoleDefinitionId termina com o GUID do nome da função que você encontrou na etapa 1. A atribuição de função deve se parecer com esta:

    ```
    {"value":[{"properties":{
    "roleDefinitionId":"/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
    "principalId":"{objectID}",
    "scope":"/",
    "createdOn":"2016-08-17T19:21:16.3422480Z",
    "updatedOn":"2016-08-17T19:21:16.3422480Z",
    "createdBy":"93ce6722-3638-4222-b582-78b75c5c6d65",
    "updatedBy":"93ce6722-3638-4222-b582-78b75c5c6d65"},
    "id":"/providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099",
    "type":"Microsoft.Authorization/roleAssignments",
    "name":"e7dd75bc-06f6-4e71-9014-ee96a929d099"}],
    "nextLink":null}
    ```

    Novamente, salve o GUID do parâmetro *name* parâmetro, neste caso **e7dd75bc-06f6-4e71-9014-ee96a929d099**.

3. Por fim, chame [DELETE roleAssignments](/rest/api/authorization/roleassignments#RoleAssignments_DeleteById), em que roleAssignmentId = o GUID do nome que você encontrou na etapa 2.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [como gerenciar o Controle de Acesso Baseado em Função com REST](role-based-access-control-manage-access-rest.md)

- [Gerenciar atribuições de acesso](role-based-access-control-manage-assignments.md) no Portal do Azure
