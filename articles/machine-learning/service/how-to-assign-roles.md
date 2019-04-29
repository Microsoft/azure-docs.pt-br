---
title: Gerenciar funções em um espaço de trabalho do Azure Machine Learning
titleSuffix: Azure Machine Learning service
description: Saiba como acessar um espaço de trabalho de serviço de Azure Machine Learning usando o controle de acesso baseado em função (RBAC).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: larryfr
author: Blackmist
ms.date: 2/20/2019
ms.custom: seodec18
ms.openlocfilehash: 56813d9a075e1c327fb5612c50ea72f067ec505d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60820060"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>Gerenciar o acesso a um espaço de trabalho do Azure Machine Learning

Neste artigo, você aprenderá a gerenciar o acesso a um espaço de trabalho do Azure Machine Learning. [Controle de acesso baseado em função (RBAC)](/azure/role-based-access-control/overview) é usado para gerenciar o acesso aos recursos do Azure. Os usuários no Active Directory do Azure são atribuídos a funções específicas, que concedem acesso aos recursos. O Azure fornece funções internas e a capacidade de criar funções personalizadas.

## <a name="default-roles"></a>Funções padrão

Um espaço de trabalho do Azure Machine Learning é um recurso do Azure. Assim como outros recursos do Azure, quando um novo espaço de trabalho do Azure Machine Learning é criado, ele vem com três funções padrão. Você pode adicionar usuários ao espaço de trabalho e atribuí-los a uma dessas funções internas.

| Função | Nível de acesso |
| --- | --- |
| **Leitor** | Ações de somente leitura no espaço de trabalho. Leitores podem listar e exibir ativos no espaço de trabalho, mas não é possível criar ou atualizar esses ativos. |
| **Colaborador** | Exibir, criar, editar ou excluir (quando aplicável) ativos em um espaço de trabalho. Por exemplo, colaboradores podem criar um teste, criar ou anexar a um cluster de computação, enviar uma execução e implantar um serviço web. |
| **Proprietário** | Acesso completo ao espaço de trabalho, incluindo a capacidade de exibir, criar, editar ou excluir (quando aplicável) ativos em um espaço de trabalho. Além disso, você pode alterar as atribuições de função. |

> [!IMPORTANT]
> Acesso de função pode ser definido para vários níveis no Azure. Por exemplo, alguém com acesso de proprietário a um espaço de trabalho não pode ter acesso de proprietário para o grupo de recursos que contém o espaço de trabalho. Para obter mais informações, consulte [funciona como o RBAC](/azure/role-based-access-control/overview#how-rbac-works).

Para obter mais informações sobre funções internas específicas, consulte [funções internas para o Azure](/azure/role-based-access-control/built-in-roles).

## <a name="manage-workspace-access"></a>Gerenciar o acesso ao espaço de trabalho

Se você for um proprietário de um espaço de trabalho, você pode adicionar e remover funções para o espaço de trabalho. Você também pode atribuir funções aos usuários. Use os links a seguir para descobrir como gerenciar o acesso:
- [Interface do usuário do portal do Azure](/azure/role-based-access-control/role-assignments-portal)
- [PowerShell](/azure/role-based-access-control/role-assignments-powershell)
- [CLI do Azure](/azure/role-based-access-control/role-assignments-cli)
- [API REST](/azure/role-based-access-control/role-assignments-rest)
- [Modelos do Gerenciador de Recursos do Azure](/azure/role-based-access-control/role-assignments-template)

Se você tiver instalado o [CLI do Azure Machine Learning](reference-azure-machine-learning-cli.md), você também pode usar um comando da CLI para atribuir funções a usuários.

```azurecli-interactive 
az ml workspace share -n <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

O `user` campo é o endereço de email de um usuário existente na instância do Active Directory do Azure na qual reside a assinatura do espaço de trabalho pai. Aqui está um exemplo de como usar este comando:

```azurecli-interactive 
az ml workspace share -n my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

## <a name="create-custom-role"></a>Criar uma função personalizada

Se as funções internas não forem suficientes, você pode criar funções personalizadas. Funções personalizadas podem ter ler, gravar, excluir e permissões de recurso no espaço de trabalho de computação. Você pode disponibilizar a função em um nível de espaço de trabalho específico, um nível de grupo de recursos específico ou um nível de assinatura específica.

> [!NOTE]
> Você deve ser um proprietário do recurso nesse nível para criar funções personalizadas dentro desse recurso.

Para criar uma função personalizada, primeiro criar um arquivo de JSON de definição de função que especifica a permissão e o escopo da função. O exemplo a seguir define uma função personalizada denominada "Cientista de dados" no escopo em um nível de espaço de trabalho específico:

`data_scientist_role.json` :
```json
{
    "Name": "Data Scientist",
    "IsCustom": true,
    "Description": "Can run experiment but can't create or delete compute.",
    "Actions": ["*"],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/*/delete",
        "Microsoft.MachineLearningServices/workspaces/computes/*/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
        "Microsoft.Authorization/*/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace_name>"
    ]
}
```

Você pode alterar o `AssignableScopes` campo para definir o escopo dessa função personalizada no nível da assinatura, o nível do grupo de recursos ou um nível de espaço de trabalho específico.

Essa função personalizada pode fazer tudo no espaço de trabalho, exceto para as seguintes ações:

- Ele não é possível criar ou atualizar um recurso de computação.
- Ele não é possível excluir um recurso de computação.
- Não é possível adicionar, excluir ou alterar as atribuições de função.
- Ele não é possível excluir o espaço de trabalho.

Para implantar essa função personalizada, use o seguinte comando da CLI do Azure:

```azurecli-interactive 
az role definition create --role-definition data_scientist_role.json
```

Após a implantação, essa função se torna disponível no espaço de trabalho especificado. Agora você pode adicionar e atribuir essa função no portal do Azure. Ou, você pode atribuir essa função a um usuário usando o `az ml workspace share` comando da CLI:

```azurecli-interactive
az ml workspace share -n my_workspace -g my_resource_group --role "Data Scientist" --user jdoe@contoson.com
```


Para obter mais informações, consulte [funções personalizadas para recursos do Azure](/azure/role-based-access-control/custom-roles).

## <a name="next-steps"></a>Próximas etapas

- [Visão geral de segurança empresarial](concept-enterprise-security.md)
- [Executar com segurança inferência dentro de uma rede virtual e experimentos](how-to-enable-virtual-network.md)
- [Tutorial: Treinar modelos](tutorial-train-models-with-aml.md)
