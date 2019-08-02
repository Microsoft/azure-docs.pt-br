---
title: Gerenciar funções em um espaço de trabalho Azure Machine Learning
titleSuffix: Azure Machine Learning service
description: Saiba como acessar um espaço de trabalho de serviço do Azure Machine Learning usando o RBAC (controle de acesso baseado em função).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: larryfr
author: Blackmist
ms.date: 07/10/2019
ms.custom: seodec18
ms.openlocfilehash: 0f28397717d5c89e5a5bcd5e7bdc17b4feb49577
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467968"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>Gerenciar o acesso a um espaço de trabalho do Azure Machine Learning

Neste artigo, você aprenderá a gerenciar o acesso a um espaço de trabalho do Azure Machine Learning. O [RBAC (controle de acesso baseado em função)](/azure/role-based-access-control/overview) é usado para gerenciar o acesso aos recursos do Azure. Os usuários em seu Azure Active Directory recebem funções específicas, que concedem acesso aos recursos. O Azure fornece funções internas e a capacidade de criar funções personalizadas.

## <a name="default-roles"></a>Funções padrão

Um espaço de trabalho Azure Machine Learning é um recurso do Azure. Assim como outros recursos do Azure, quando um novo espaço de trabalho Azure Machine Learning é criado, ele vem com três funções padrão. Você pode adicionar usuários ao espaço de trabalho e atribuí-los a uma dessas funções internas.

| Role | Nível de acesso |
| --- | --- |
| **Leitor** | Ações somente leitura no espaço de trabalho. Os leitores podem listar e exibir ativos em um espaço de trabalho, mas não podem criar ou atualizar esses ativos. |
| **Colaborador** | Exiba, crie, edite ou exclua (onde aplicável) ativos em um espaço de trabalho. Por exemplo, os colaboradores podem criar um experimento, criar ou anexar um cluster de computação, enviar uma execução e implantar um serviço Web. |
| **Proprietário** | Acesso completo ao espaço de trabalho, incluindo a capacidade de exibir, criar, editar ou excluir ativos (onde aplicável) em um espaço de trabalho. Além disso, você pode alterar as atribuições de função. |

> [!IMPORTANT]
> O acesso à função pode ser definido para vários níveis no Azure. Por exemplo, alguém com acesso de proprietário a um espaço de trabalho pode não ter acesso de proprietário ao grupo de recursos que contém o espaço de trabalho. Para obter mais informações, consulte [como o RBAC funciona](/azure/role-based-access-control/overview#how-rbac-works).

Para obter mais informações sobre funções internas específicas, consulte [funções internas do Azure](/azure/role-based-access-control/built-in-roles).

## <a name="manage-workspace-access"></a>Gerenciar o acesso ao espaço de trabalho

Se você for um proprietário de um espaço de trabalho, poderá adicionar e remover funções para o espaço de trabalho. Você também pode atribuir funções a usuários. Use os links a seguir para descobrir como gerenciar o acesso:
- [Interface do usuário do portal do Azure](/azure/role-based-access-control/role-assignments-portal)
- [PowerShell](/azure/role-based-access-control/role-assignments-powershell)
- [CLI do Azure](/azure/role-based-access-control/role-assignments-cli)
- [API REST](/azure/role-based-access-control/role-assignments-rest)
- [Modelos do Gerenciador de Recursos do Azure](/azure/role-based-access-control/role-assignments-template)

Se você instalou a [CLI do Azure Machine Learning](reference-azure-machine-learning-cli.md), também poderá usar um comando da CLI para atribuir funções aos usuários.

```azurecli-interactive 
az ml workspace share -w <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

O `user` campo é o endereço de email de um usuário existente na instância do Azure Active Directory em que a assinatura pai do espaço de trabalho reside. Aqui está um exemplo de como usar este comando:

```azurecli-interactive 
az ml workspace share -w my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

## <a name="create-custom-role"></a>Criar uma função personalizada

Se as funções internas forem insuficientes, você poderá criar funções personalizadas. As funções personalizadas podem ter permissões de recurso de leitura, gravação, exclusão e computação nesse espaço de trabalho. Você pode tornar a função disponível em um nível de espaço de trabalho específico, um nível de grupo de recursos específico ou um nível de assinatura específico.

> [!NOTE]
> Você deve ser um proprietário do recurso nesse nível para criar funções personalizadas dentro desse recurso.

Para criar uma função personalizada, primeiro Construa um arquivo JSON de definição de função que especifique a permissão e o escopo para a função. O exemplo a seguir define uma função personalizada denominada "cientista de dados" com escopo em um nível de espaço de trabalho específico:

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

Você pode alterar o `AssignableScopes` campo para definir o escopo dessa função personalizada no nível da assinatura, no nível do grupo de recursos ou em um nível de espaço de trabalho específico.

Essa função personalizada pode fazer tudo no espaço de trabalho, exceto pelas seguintes ações:

- Ele não pode criar ou atualizar um recurso de computação.
- Ele não pode excluir um recurso de computação.
- Ele não pode adicionar, excluir ou alterar atribuições de função.
- Ele não pode excluir o espaço de trabalho.

Para implantar essa função personalizada, use o seguinte comando de CLI do Azure:

```azurecli-interactive 
az role definition create --role-definition data_scientist_role.json
```

Após a implantação, essa função fica disponível no espaço de trabalho especificado. Agora você pode adicionar e atribuir essa função no portal do Azure. Ou, você pode atribuir essa função a um usuário usando o comando `az ml workspace share` da CLI:

```azurecli-interactive
az ml workspace share -w my_workspace -g my_resource_group --role "Data Scientist" --user jdoe@contoson.com
```


Para obter mais informações, consulte [funções personalizadas para recursos do Azure](/azure/role-based-access-control/custom-roles).

## <a name="next-steps"></a>Próximas etapas

- [Visão geral de segurança corporativa](concept-enterprise-security.md)
- [Executar experimentos e inferência/Pontuação com segurança dentro de uma rede virtual](how-to-enable-virtual-network.md)
- [Tutorial: Treinar modelos](tutorial-train-models-with-aml.md)
