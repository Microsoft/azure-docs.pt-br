---
title: O Controle de Acesso Baseado em Função (RBAC) do Azure para controlar os direitos de acesso para criação e gerenciamento de solicitações de suporte | Microsoft Docs
description: O Controle de Acesso Baseado em Função (RBAC) do Azure para controlar os direitos de acesso para criação e gerenciamento de solicitações de suporte
author: ganganarayanan
ms.author: gangan
ms.date: 1/31/2017
ms.topic: article
ms.service: azure
ms.assetid: 58a0ca9d-86d2-469a-9714-3b8320c33cf5
ms.openlocfilehash: d98d0637c6d520193b11f4267c59016772ef063a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60809926"
---
# <a name="azure-role-based-access-control-rbac-to-control-access-rights-to-create-and-manage-support-requests"></a>O Controle de Acesso Baseado em Função (RBAC) do Azure para controlar os direitos de acesso para criação e gerenciamento de solicitações de suporte

O [RBAC (Controle de Acesso Baseado em Função)](https://docs.microsoft.com/azure/role-based-access-control/overview) permite gerenciamento de acesso refinado para o Azure.
A criação de solicitação de suporte no portal do Azure [portal.azure.com](https://portal.azure.com) usa o modelo RBAC do Azure para definir quem pode criar e gerenciar solicitações de suporte.
O acesso é concedido ao atribuir a função RBAC apropriada a usuários, grupos e aplicativos em determinado escopo, que pode ser uma assinatura, um recurso ou um grupo de recursos.

Vejamos um exemplo: Como um proprietário do grupo de recursos com permissões de leitura no escopo da assinatura, você pode gerenciar todos os recursos no grupo de recursos, como sites, máquinas virtuais e sub-redes.
No entanto, se tentar criar uma solicitação de suporte relativa ao recurso de máquina virtual, você vai encontrar o seguinte erro

![Erro de assinatura](./media/create-manage-support-requests-using-access-control/subscription-error.png)

No gerenciamento de solicitações de suporte, você precisa de permissão de gravação ou uma função que tenha a ação de suporte Microsoft.Support/* no escopo de assinatura para poder criar e gerenciar solicitações de suporte.

O seguinte artigo explica como você pode usar o Controle de Acesso Baseado em Função (RBAC) do Azure para criar e gerenciar solicitações de suporte no portal do Azure.

## <a name="getting-started"></a>Introdução

Usando o exemplo acima, será possível criar uma solicitação de suporte para seu recurso se uma função RBAC personalizada na assinatura tiver sido atribuída a você pelo proprietário da assinatura.
É possível criar [funções RBAC personalizadas](https://azure.microsoft.com/documentation/articles/role-based-access-control-custom-roles/) usando o Azure PowerShell, a interface de linha de comando (CLI) do Azure e a API REST.

A propriedade actions de uma função personalizada especifica as operações do Azure às quais a função concede acesso.
Para criar uma função personalizada para o gerenciamento de solicitações de suporte, a função deve ter a ação Microsoft.Support/*

Aqui está um exemplo de uma função personalizada que você pode usar para criar e gerenciar solicitações de suporte.
Chamamos essa função de "Colaborador de Solicitação de Suporte" e é como nos referimos à função personalizada neste artigo.

``` Json
{
    "Name":  "Support Request Contributor",
    "Id":  "1f2aad59-39b0-41da-b052-2fb070bd7942",
    "IsCustom":  true,
    "Description":  "Lets you create and manage support tickets.",
    "Actions":  [
                    "Microsoft.Support/*"
                ],
    "NotActions":  [
                   ],
    "AssignableScopes":  [
                             "/"
                         ]
}
```

Siga as etapas descritas [neste vídeo](https://www.youtube.com/watch?v=-PaBaDmfwKI) para aprender a criar uma função personalizada para sua assinatura.

## <a name="create-and-manage-support-requests-in-the-azure-portal"></a>Criar e gerenciar solicitações de suporte no portal do Azure

Vejamos um exemplo – você é o proprietário da assinatura "Assinatura MSDN do Visual Studio".
Joe, seu colega, é proprietário de recursos em alguns grupos de recursos dessa assinatura e tem permissão de leitura na assinatura.
Você deseja conceder a Joe o acesso necessário à capacidade de criar e gerenciar tíquetes de suporte para os recursos dessa assinatura.

1. A primeira etapa é ir para a assinatura e ver uma lista de usuários em "Configurações". Clique no usuário Joe, que tem acesso de leitura na assinatura, para atribuir uma nova função personalizada para ele.

    ![Adicionar função](./media/create-manage-support-requests-using-access-control/add-role.png)

2. Clique em “Adicionar” na folha “Usuários”. Selecione a função personalizada "Colaborador de Solicitação de Suporte" na lista de funções

    ![Adicione a função colaborador de suporte](./media/create-manage-support-requests-using-access-control/add-support-contributor-role.png)

3. Após selecionar o nome da função, clique em “Adicionar usuários” e insira as credenciais de email de Joe. Clique em “Selecionar”

    ![Adicionar usuários](./media/create-manage-support-requests-using-access-control/add-users.png)

4. Clique em “OK” para continuar

    ![Adicionar acesso](./media/create-manage-support-requests-using-access-control/add-access.png)

5. Agora você verá o usuário com a função personalizada "Colaborador de Solicitação de Suporte" recém-adicionada na Assinatura que tem você como proprietário

    ![Usuário adicionado](./media/create-manage-support-requests-using-access-control/user-added.png)

    Quando Joe fizer logon no portal, ele verá a assinatura à qual foi adicionado.

7. Joe clica em "Nova solicitação de suporte" na folha "Ajuda e suporte" e pode criar solicitações de suporte para "Visual Studio Ultimate com MSDN"

    ![Nova solicitação de suporte](./media/create-manage-support-requests-using-access-control/new-support-request.png)

8. Ao clicar em "Todas as solicitações de suporte", Joe verá a lista de solicitações de suporte criadas para esta Assinatura ![Exibição de detalhes do caso](./media/create-manage-support-requests-using-access-control/case-details-view.png)

## <a name="remove-support-request-access-in-the-azure-portal"></a>Remover o acesso a solicitação de suporte no portal do Azure

Assim como é possível conceder acesso a um usuário para criar e gerenciar solicitações de suporte, também é possível removê-lo.
Para remover a capacidade de criar e gerenciar solicitações de suporte, vá para a assinatura, clique em "Configurações" e clique no usuário (nesse caso, Joe).
Clique com o botão direito no nome da função "Colaborador de Solicitação de Suporte" e clique em "Remover"

![Remover acesso a solicitação de suporte](./media/create-manage-support-requests-using-access-control/remove-support-request-access.png)

Quando Joe faz logon no portal e tenta criar uma solicitação de suporte, ele encontra o seguinte erro

![Erro de assinatura-2](./media/create-manage-support-requests-using-access-control/subscription-error-2.png)

Joe não conseguirá ver as solicitações suporte quando clicar em "Todas as solicitações de suporte"

![exibição de detalhes do caso-2](./media/create-manage-support-requests-using-access-control/case-details-view-2.png)
