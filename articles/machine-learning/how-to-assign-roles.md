---
title: Gerenciar funções em seu espaço de trabalho
titleSuffix: Azure Machine Learning
description: Saiba como acessar um espaço de trabalho Azure Machine Learning usando o Azure RBAC (controle de acesso baseado em função do Azure).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: Blackmist
ms.author: nigup
author: nishankgu
ms.date: 03/26/2021
ms.custom: how-to, seodec18, devx-track-azurecli, contperf-fy21q2
ms.openlocfilehash: 4d037bdf266e70a2621b2627bc88abce30af652c
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105612612"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>Gerenciar acesso a um workspace do Azure Machine Learning

Neste artigo, você aprenderá a gerenciar o acesso (autorização) a um espaço de trabalho Azure Machine Learning. O Azure [RBAC (controle de acesso baseado em função)](../role-based-access-control/overview.md) do Azure é usado para gerenciar o acesso aos recursos do Azure, como a capacidade de criar novos recursos ou usar os existentes. Os usuários em seu Azure Active Directory (Azure AD) recebem funções específicas, que concedem acesso aos recursos. O Azure fornece funções internas e a capacidade de criar funções personalizadas.

> [!TIP]
> Embora este artigo se concentre em Azure Machine Learning, os serviços individuais dos quais o Azure ML depende fornecem suas próprias configurações de RBAC. Por exemplo, usando as informações neste artigo, você pode configurar quem pode enviar solicitações de Pontuação para um modelo implantado como um serviço Web no serviço kubernetes do Azure. Mas o serviço kubernetes do Azure fornece seu próprio conjunto de funções do Azure. Para informações de RBAC específicas de serviço que podem ser úteis com Azure Machine Learning, consulte os links a seguir:
>
> * [Controlar o acesso aos recursos do cluster kubernetes do Azure](../aks/azure-ad-rbac.md)
> * [Usar o RBAC do Azure para autorização kubernetes](../aks/manage-azure-rbac.md)
> * [Usar o RBAC do Azure para acessar dados de BLOB](../storage/common/storage-auth-aad-rbac-portal.md)

> [!WARNING]
> A aplicação de algumas funções pode limitar a funcionalidade da interface do usuário no Azure Machine Learning Studio para outros usuários. Por exemplo, se a função de um usuário não tiver a capacidade de criar uma instância de computação, a opção de criar uma instância de computação não estará disponível no estúdio. Esse comportamento é esperado e impede que o usuário tente operações que retornariam um erro de acesso negado.

## <a name="default-roles"></a>Funções padrão

Um workspace do Azure Machine Learning é um recurso do Azure. Assim como outros recursos do Azure, quando um novo workspace do Azure Machine Learning é criado, ele vem com três funções padrão. Você pode adicionar usuários ao espaço de trabalho e atribuí-los a uma dessas funções internas.

| Função | Nível de acesso |
| --- | --- |
| **Leitor** | Ações somente leitura no espaço de trabalho. Os leitores podem listar e exibir ativos, incluindo credenciais de [repositório de armazenamento](how-to-access-data.md) , em um espaço de trabalho. Os leitores não podem criar nem atualizar esses ativos. |
| **Colaborador** | Exiba, crie, edite ou exclua (onde aplicável) ativos em um espaço de trabalho. Por exemplo, os colaboradores podem criar um experimento, criar ou anexar um cluster de computação, enviar uma execução e implantar um serviço Web. |
| **Proprietário** | Acesso completo ao espaço de trabalho, incluindo a capacidade de exibir, criar, editar ou excluir ativos (onde aplicável) em um espaço de trabalho. Além disso, você pode alterar as atribuições de função. |
| **Função personalizada** | Permite que você personalize o acesso a operações de plano de dados ou controle específico em um espaço de trabalho. Por exemplo, o envio de uma execução, a criação de uma computação, a implantação de um modelo ou o registro de um conjunto de uma. |

> [!IMPORTANT]
> O acesso à função pode ser definido para vários níveis no Azure. Por exemplo, alguém com acesso de proprietário a um espaço de trabalho pode não ter acesso de proprietário ao grupo de recursos que contém o espaço de trabalho. Para obter mais informações, consulte [como o RBAC do Azure funciona](../role-based-access-control/overview.md#how-azure-rbac-works).

No momento, não existem funções internas adicionais que sejam específicas para Azure Machine Learning. Para obter mais informações sobre funções internas, consulte [funções internas do Azure](../role-based-access-control/built-in-roles.md).

## <a name="manage-workspace-access"></a>Gerenciar o acesso ao espaço de trabalho

Se você for um proprietário de um espaço de trabalho, poderá adicionar e remover funções para o espaço de trabalho. Você também pode atribuir funções a usuários. Use os links a seguir para descobrir como gerenciar o acesso:
- [Interface do usuário do portal do Azure](../role-based-access-control/role-assignments-portal.md)
- [PowerShell](../role-based-access-control/role-assignments-powershell.md)
- [CLI do Azure](../role-based-access-control/role-assignments-cli.md)
- [REST API](../role-based-access-control/role-assignments-rest.md)
- [Modelos do Gerenciador de Recursos do Azure](../role-based-access-control/role-assignments-template.md)

Se você instalou a [CLI do Azure Machine Learning](reference-azure-machine-learning-cli.md), poderá usar comandos da CLI para atribuir funções a usuários:

```azurecli-interactive 
az ml workspace share -w <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

O `user` campo é o endereço de email de um usuário existente na instância do Azure Active Directory em que a assinatura pai do espaço de trabalho reside. Aqui está um exemplo de como usar este comando:

```azurecli-interactive 
az ml workspace share -w my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

> [!NOTE]
> o comando "AZ ml Workspace share" não funciona para a conta federada por Azure Active Directory B2B. Use o portal da interface do usuário do Azure em vez do comando.

## <a name="create-custom-role"></a>Criar função personalizada

Caso as funções internas sejam insuficientes, você poderá criar funções personalizadas. As funções personalizadas podem ter permissões de recurso de leitura, gravação, exclusão e computação nesse espaço de trabalho. Você pode tornar a função disponível em um nível de espaço de trabalho específico, um nível de grupo de recursos específico ou um nível de assinatura específico.

> [!NOTE]
> Você deve ser um proprietário do recurso nesse nível para criar funções personalizadas dentro desse recurso.

Para criar uma função personalizada, primeiro Construa um arquivo JSON de definição de função que especifique a permissão e o escopo para a função. O exemplo a seguir define uma função personalizada denominada "data cientista Custom" com escopo em um nível de espaço de trabalho específico:

`data_scientist_custom_role.json` :
```json
{
    "Name": "Data Scientist Custom",
    "IsCustom": true,
    "Description": "Can run experiment but can't create or delete compute.",
    "Actions": ["*"],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/*/delete",
        "Microsoft.MachineLearningServices/workspaces/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
        "Microsoft.Authorization/*/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace_name>"
    ]
}
```

> [!TIP]
> Você pode alterar o `AssignableScopes` campo para definir o escopo dessa função personalizada no nível da assinatura, no nível do grupo de recursos ou em um nível de espaço de trabalho específico.
> A função personalizada acima é apenas um exemplo, consulte algumas [funções personalizadas sugeridas para o serviço de Azure Machine Learning](#customroles).

Essa função personalizada pode fazer tudo no espaço de trabalho, exceto pelas seguintes ações:

- Ele não pode criar ou atualizar um recurso de computação.
- Ele não pode excluir um recurso de computação.
- Ele não pode adicionar, excluir ou alterar atribuições de função.
- Ele não pode excluir o espaço de trabalho.

Para implantar essa função personalizada, use o seguinte comando de CLI do Azure:

```azurecli-interactive 
az role definition create --role-definition data_scientist_role.json
```

Após a implantação, essa função fica disponível no espaço de trabalho especificado. Agora você pode adicionar e atribuir essa função no portal do Azure. Ou, você pode atribuir essa função a um usuário usando o `az ml workspace share` comando da CLI:

```azurecli-interactive
az ml workspace share -w my_workspace -g my_resource_group --role "Data Scientist" --user jdoe@contoson.com
```

Para obter mais informações sobre funções personalizadas, consulte [funções personalizadas do Azure](../role-based-access-control/custom-roles.md). 

### <a name="azure-machine-learning-operations"></a>Operações de Azure Machine Learning

Para obter mais informações sobre as operações (ações e não ações) utilizáveis com funções personalizadas, consulte [operações do provedor de recursos](../role-based-access-control/resource-provider-operations.md#microsoftmachinelearningservices). Você também pode usar o seguinte comando CLI do Azure para listar as operações:

```azurecli-interactive
az provider operation show –n Microsoft.MachineLearningServices
```

## <a name="list-custom-roles"></a>Listar funções personalizadas

No CLI do Azure, execute o seguinte comando:

```azurecli-interactive
az role definition list --subscription <sub-id> --custom-role-only true
```

Para exibir a definição de função para uma função personalizada específica, use o comando CLI do Azure a seguir. O `<role-name>` deve estar no mesmo formato retornado pelo comando acima:

```azurecli-interactive
az role definition list -n <role-name> --subscription <sub-id>
```

## <a name="update-a-custom-role"></a>Atualizar uma função personalizada

No CLI do Azure, execute o seguinte comando:

```azurecli-interactive
az role definition update --role-definition update_def.json --subscription <sub-id>
```

Você precisa ter permissões em todo o escopo da nova definição de função. Por exemplo, se essa nova função tiver um escopo entre três assinaturas, você precisará ter permissões em todas as três assinaturas. 

> [!NOTE]
> As atualizações de função podem levar 15 minutos a uma hora para serem aplicadas em todas as atribuições de função nesse escopo.

## <a name="use-azure-resource-manager-templates-for-repeatability"></a>Usar modelos de Azure Resource Manager para repetição

Se você prevê que precisará recriar atribuições de função complexas, um modelo de Azure Resource Manager pode ser uma grande ajuda. O [modelo 201-Machine-Learning-dependências-role-Assignment](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-dependencies-role-assignment) mostra como as atribuições de função podem ser especificadas no código-fonte para reutilização. 

## <a name="common-scenarios"></a>Cenários comuns

A tabela a seguir é um resumo de Azure Machine Learning atividades e as permissões necessárias para realizá-las no menor escopo. Por exemplo, se uma atividade puder ser executada com um escopo de espaço de trabalho (coluna 4), todo o escopo mais alto com essa permissão também funcionará automaticamente:

> [!IMPORTANT]
> Todos os caminhos nesta tabela que começam com `/` são **caminhos relativos** para `Microsoft.MachineLearningServices/` :

| Atividade | Escopo de nível de assinatura | Escopo no nível do grupo de recursos | Escopo no nível do espaço de trabalho |
| ----- | ----- | ----- | ----- |
| Criar novo workspace | Não é necessária | Proprietário ou colaborador | N/A (torna-se proprietário ou herda uma função de escopo maior após a criação) |
| Solicitar cota de Amlcompute de nível de assinatura ou definir cota de nível de espaço de trabalho | Proprietário, ou colaborador, ou função personalizada </br>permitindo que `/locations/updateQuotas/action`</br> no escopo da assinatura | Não autorizado | Não autorizado |
| Criar novo cluster de computação | Não é necessária | Não é necessária | Proprietário, colaborador ou função personalizada, permitindo: `/workspaces/computes/write` |
| Criar nova instância de computação | Não é necessária | Não é necessária | Proprietário, colaborador ou função personalizada, permitindo: `/workspaces/computes/write` |
| Enviando qualquer tipo de execução | Não é necessária | Não é necessária | Proprietário, colaborador ou função personalizada, permitindo: `"/workspaces/*/read", "/workspaces/environments/write", "/workspaces/experiments/runs/write", "/workspaces/metadata/artifacts/write", "/workspaces/metadata/snapshots/write", "/workspaces/environments/build/action", "/workspaces/experiments/runs/submit/action", "/workspaces/environments/readSecrets/action"` |
| Publicando pipelines e pontos de extremidade | Não é necessária | Não é necessária | Proprietário, colaborador ou função personalizada, permitindo: `"/workspaces/endpoints/pipelines/*", "/workspaces/pipelinedrafts/*", "/workspaces/modules/*"` |
| Implantando um modelo registrado em um recurso AKS/ACI | Não é necessária | Não é necessária | Proprietário, colaborador ou função personalizada, permitindo: `"/workspaces/services/aks/write", "/workspaces/services/aci/write"` |
| Pontuação em relação a um ponto de extremidade AKS implantado | Não é necessária | Não é necessária | Proprietário, colaborador ou função personalizada, permitindo: `"/workspaces/services/aks/score/action", "/workspaces/services/aks/listkeys/action"` (quando você não estiver usando Azure Active Directory autenticação) ou `"/workspaces/read"` (quando estiver usando a autenticação de token) |
| Acessando o armazenamento usando blocos de anotações interativos | Não é necessária | Não é necessária | Proprietário, colaborador ou função personalizada, permitindo: `"/workspaces/computes/read", "/workspaces/notebooks/samples/read", "/workspaces/notebooks/storage/*", "/workspaces/listKeys/action"` |
| Criar nova função personalizada | Proprietário, colaborador ou função personalizada que permite `Microsoft.Authorization/roleDefinitions/write` | Não é necessária | Proprietário, colaborador ou função personalizada, permitindo: `/workspaces/computes/write` |

> [!TIP]
> Se você receber uma falha ao tentar criar um espaço de trabalho pela primeira vez, certifique-se de que sua função permite `Microsoft.MachineLearningServices/register/action` . Essa ação permite que você registre o provedor de recursos Azure Machine Learning com sua assinatura do Azure.

### <a name="user-assigned-managed-identity-with-azure-ml-compute-cluster"></a>Identidade gerenciada atribuída pelo usuário com o cluster de computação do Azure ML

Para atribuir uma identidade atribuída ao usuário a um cluster de computação Azure Machine Learning, você precisa de permissões de gravação para criar a computação e a [função de operador de identidade gerenciada](../role-based-access-control/built-in-roles.md#managed-identity-operator). Para obter mais informações sobre o RBAC do Azure com identidades gerenciadas, leia [como gerenciar a identidade atribuída pelo usuário](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

### <a name="mlflow-operations"></a>Operações de MLflow

Para executar operações de MLflow com seu espaço de trabalho Azure Machine Learning, use os seguintes escopos da sua função personalizada:

| Operação MLflow | Escopo |
| --- | --- |
| Listar todos os experimentos no repositório de acompanhamento de espaço de trabalho, obter um experimento por ID, obter um experimento por nome | `Microsoft.MachineLearningServices/workspaces/experiments/read` |
| Criar um experimento com um nome, definir uma marca em um experimento, restaurar um experimento marcado para exclusão| `Microsoft.MachineLearningServices/workspaces/experiments/write` | 
| Excluir um experimento | `Microsoft.MachineLearningServices/workspaces/experiments/delete` |
| Obter uma execução e dados e metadados relacionados, obter uma lista de todos os valores da métrica especificada para uma determinada execução, listar artefatos para uma execução | `Microsoft.MachineLearningServices/workspaces/experiments/runs/read` |
| Criar uma nova execução em um experimento, excluir execuções, restaurar execuções excluídas, métricas de log na execução atual, definir marcas em uma execução, excluir marcas em uma execução, parâmetros de log (par chave-valor) usado para uma execução, registrar em log um lote de métricas, parâmetros e marcas para uma execução, atualizar status da execução | `Microsoft.MachineLearningServices/workspaces/experiments/runs/write` |
| Obter modelo registrado por nome, buscar uma lista de todos os modelos registrados no registro, Pesquisar modelos registrados, modelos de versão mais recentes para cada estágio de solicitações, obter a versão de um modelo registrado, Pesquisar versões de modelo, obter URI onde os artefatos da versão do modelo são armazenados, Pesquisar execuções por IDs de experimento | `Microsoft.MachineLearningServices/workspaces/models/read` |
| Criar um novo modelo registrado, atualizar o nome/descrição de um modelo registrado, renomear o modelo registrado existente, criar uma nova versão do modelo, atualizar a descrição de uma versão do modelo, fazer a transição de um modelo registrado para um dos estágios | `Microsoft.MachineLearningServices/workspaces/models/write` |
| Excluir um modelo registrado junto com todas as suas versões, excluir versões específicas de um modelo registrado | `Microsoft.MachineLearningServices/workspaces/models/delete` |

<a id="customroles"></a>

## <a name="example-custom-roles"></a>Exemplo de funções personalizadas

### <a name="data-scientist"></a>Cientista de dados

Permite que um cientista de dados execute todas as operações dentro de um espaço de trabalho **, exceto**:

* Criação de computação
* Implantando modelos em um cluster AKS de produção
* Implantando um ponto de extremidade de pipeline em produção

`data_scientist_custom_role.json` :
```json
{
    "Name": "Data Scientist Custom",
    "IsCustom": true,
    "Description": "Can run experiment but can't create or delete compute or deploy production endpoints.",
    "Actions": [
        "Microsoft.MachineLearningServices/workspaces/*/read",
        "Microsoft.MachineLearningServices/workspaces/*/action",
        "Microsoft.MachineLearningServices/workspaces/*/delete",
        "Microsoft.MachineLearningServices/workspaces/*/write"
    ],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/delete",
        "Microsoft.MachineLearningServices/workspaces/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
        "Microsoft.Authorization/*",
        "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
        "Microsoft.MachineLearningServices/workspaces/listKeys/action",
        "Microsoft.MachineLearningServices/workspaces/services/aks/write",
        "Microsoft.MachineLearningServices/workspaces/services/aks/delete",
        "Microsoft.MachineLearningServices/workspaces/endpoints/pipelines/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>"
    ]
}
```

### <a name="data-scientist-restricted"></a>Cientista de dados restrito

Uma definição de função mais restrita sem curingas nas ações permitidas. Ele pode executar todas as operações dentro de um espaço de trabalho **, exceto**:

* Criação de computação
* Implantando modelos em um cluster AKS de produção
* Implantando um ponto de extremidade de pipeline em produção

`data_scientist_restricted_custom_role.json` :
```json
{
    "Name": "Data Scientist Restricted Custom",
    "IsCustom": true,
    "Description": "Can run experiment but can't create or delete compute or deploy production endpoints",
    "Actions": [
        "Microsoft.MachineLearningServices/workspaces/*/read",
        "Microsoft.MachineLearningServices/workspaces/computes/start/action",
        "Microsoft.MachineLearningServices/workspaces/computes/stop/action",
        "Microsoft.MachineLearningServices/workspaces/computes/restart/action",
        "Microsoft.MachineLearningServices/workspaces/computes/applicationaccess/action",
        "Microsoft.MachineLearningServices/workspaces/notebooks/storage/read",
        "Microsoft.MachineLearningServices/workspaces/notebooks/storage/write",
        "Microsoft.MachineLearningServices/workspaces/notebooks/storage/delete",
        "Microsoft.MachineLearningServices/workspaces/notebooks/samples/read",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/write",
        "Microsoft.MachineLearningServices/workspaces/experiments/write",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/submit/action",
        "Microsoft.MachineLearningServices/workspaces/pipelinedrafts/write",
        "Microsoft.MachineLearningServices/workspaces/metadata/snapshots/write",
        "Microsoft.MachineLearningServices/workspaces/metadata/artifacts/write",
        "Microsoft.MachineLearningServices/workspaces/environments/write",
        "Microsoft.MachineLearningServices/workspaces/models/write",
        "Microsoft.MachineLearningServices/workspaces/modules/write",
        "Microsoft.MachineLearningServices/workspaces/datasets/registered/write", 
        "Microsoft.MachineLearningServices/workspaces/datasets/registered/delete",
        "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/write",
        "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/delete",
        "Microsoft.MachineLearningServices/workspaces/computes/listNodes/action",
        "Microsoft.MachineLearningServices/workspaces/environments/build/action"
    ],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/computes/write",
        "Microsoft.MachineLearningServices/workspaces/write",
        "Microsoft.MachineLearningServices/workspaces/computes/delete",
        "Microsoft.MachineLearningServices/workspaces/delete",
        "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
        "Microsoft.MachineLearningServices/workspaces/listKeys/action",
        "Microsoft.Authorization/*",
        "Microsoft.MachineLearningServices/workspaces/datasets/registered/profile/read",
        "Microsoft.MachineLearningServices/workspaces/datasets/registered/preview/read",
        "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/profile/read",
        "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/preview/read",
        "Microsoft.MachineLearningServices/workspaces/datasets/registered/schema/read",    
        "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/schema/read",
        "Microsoft.MachineLearningServices/workspaces/datastores/write",
        "Microsoft.MachineLearningServices/workspaces/datastores/delete"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>"
    ]
}
```
     
### <a name="mlflow-data-scientist"></a>Cientista de dados de MLflow

Permite que um cientista de dados execute todas as operações com suporte do MLflow AzureML **, exceto**:

* Criação de computação
* Implantando modelos em um cluster AKS de produção
* Implantando um ponto de extremidade de pipeline em produção

`mlflow_data_scientist_custom_role.json` :
```json
{
    "Name": "MLFlow Data Scientist Custom",
    "IsCustom": true,
    "Description": "Can perform azureml mlflow integrated functionalities that includes mlflow tracking, projects, model registry",
    "Actions": [
        "Microsoft.MachineLearningServices/workspaces/experiments/read",
        "Microsoft.MachineLearningServices/workspaces/experiments/write",
        "Microsoft.MachineLearningServices/workspaces/experiments/delete",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/read",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/write",
        "Microsoft.MachineLearningServices/workspaces/models/read",
        "Microsoft.MachineLearningServices/workspaces/models/write",
        "Microsoft.MachineLearningServices/workspaces/models/delete"
    ],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/delete",
        "Microsoft.MachineLearningServices/workspaces/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
        "Microsoft.Authorization/*",
        "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
        "Microsoft.MachineLearningServices/workspaces/listKeys/action",
        "Microsoft.MachineLearningServices/workspaces/services/aks/write",
        "Microsoft.MachineLearningServices/workspaces/services/aks/delete",
        "Microsoft.MachineLearningServices/workspaces/endpoints/pipelines/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>"
    ]
}
```   

### <a name="mlops"></a>MLOps

Permite atribuir uma função a uma entidade de serviço e usá-la para automatizar os pipelines do MLOps. Por exemplo, para enviar execuções em um pipeline já publicado:

`mlops_custom_role.json` :
```json
{
    "Name": "MLOps Custom",
    "IsCustom": true,
    "Description": "Can run pipelines against a published pipeline endpoint",
    "Actions": [
        "Microsoft.MachineLearningServices/workspaces/read",
        "Microsoft.MachineLearningServices/workspaces/endpoints/pipelines/read",
        "Microsoft.MachineLearningServices/workspaces/metadata/artifacts/read",
        "Microsoft.MachineLearningServices/workspaces/metadata/snapshots/read",
        "Microsoft.MachineLearningServices/workspaces/environments/read",    
        "Microsoft.MachineLearningServices/workspaces/metadata/secrets/read",
        "Microsoft.MachineLearningServices/workspaces/modules/read",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/read",
        "Microsoft.MachineLearningServices/workspaces/datasets/registered/read",
        "Microsoft.MachineLearningServices/workspaces/datastores/read",
        "Microsoft.MachineLearningServices/workspaces/environments/write",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/write",
        "Microsoft.MachineLearningServices/workspaces/metadata/artifacts/write",
        "Microsoft.MachineLearningServices/workspaces/metadata/snapshots/write",
        "Microsoft.MachineLearningServices/workspaces/environments/build/action",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/submit/action"
    ],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/computes/write",
        "Microsoft.MachineLearningServices/workspaces/write",
        "Microsoft.MachineLearningServices/workspaces/computes/delete",
        "Microsoft.MachineLearningServices/workspaces/delete",
        "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
        "Microsoft.MachineLearningServices/workspaces/listKeys/action",
        "Microsoft.Authorization/*"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>"
    ]
}
```

### <a name="workspace-admin"></a>Administrador do espaço de trabalho

Permite que você execute todas as operações dentro do escopo de um espaço de trabalho, **exceto**:

* Criando um novo workspace
* Atribuindo cotas de nível de espaço de trabalho ou de assinatura

O administrador do espaço de trabalho também não pode criar uma nova função. Ele só pode atribuir funções internas ou personalizadas existentes dentro do escopo de seu espaço de trabalho:

`workspace_admin_custom_role.json` :
```json
{
    "Name": "Workspace Admin Custom",
    "IsCustom": true,
    "Description": "Can perform all operations except quota management and upgrades",
    "Actions": [
        "Microsoft.MachineLearningServices/workspaces/*/read",
        "Microsoft.MachineLearningServices/workspaces/*/action",
        "Microsoft.MachineLearningServices/workspaces/*/write",
        "Microsoft.MachineLearningServices/workspaces/*/delete",
        "Microsoft.Authorization/roleAssignments/*"
    ],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>"
    ]
}
```

<a name="labeler"></a>
### <a name="data-labeler"></a>Labeler de dados

Permite que você defina uma função com escopo apenas para rotular dados:

`labeler_custom_role.json` :
```json
{
    "Name": "Labeler Custom",
    "IsCustom": true,
    "Description": "Can label data for Labeling",
    "Actions": [
        "Microsoft.MachineLearningServices/workspaces/read",
        "Microsoft.MachineLearningServices/workspaces/labeling/projects/read",
        "Microsoft.MachineLearningServices/workspaces/labeling/labels/write"
    ],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/labeling/projects/summary/read"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>"
    ]
}
```

## <a name="troubleshooting"></a>Solução de problemas

Aqui estão algumas coisas que você deve conhecer enquanto usa o controle de acesso baseado em função do Azure (RBAC do Azure):

- Quando você cria um recurso no Azure, como um espaço de trabalho, você não é diretamente o proprietário do recurso. Sua função é herdada da função de escopo mais alta na qual você está autorizado nessa assinatura. Como exemplo, se você for um administrador de rede e tiver as permissões para criar um Machine Learning espaço de trabalho, a função de administrador de rede será atribuída a esse espaço de trabalho e não à função de proprietário.

- Para executar operações de cota em um espaço de trabalho, você precisa de permissões de nível de assinatura. Isso significa que a definição de cota de nível de assinatura ou cota de nível de espaço de trabalho para seus recursos de computação gerenciados só poderá ocorrer se você tiver permissões de gravação no escopo da assinatura.

- Quando há duas atribuições de função para o mesmo Azure Active Directory usuário com seções conflitantes de ações/não ações, suas operações listadas em não ações de uma função podem não entrar em vigor se também estiverem listadas como ações em outra função. Para saber mais sobre como o Azure analisa as atribuições de função, leia [como o RBAC do Azure determina se um usuário tem acesso a um recurso](../role-based-access-control/overview.md#how-azure-rbac-determines-if-a-user-has-access-to-a-resource)

- Para implantar seus recursos de computação dentro de uma VNet, você precisa ter permissões explicitamente para as seguintes ações:
    - `Microsoft.Network/virtualNetworks/*/read` nos recursos de VNet.
    - `Microsoft.Network/virtualNetworks/subnet/join/action` no recurso de sub-rede.
    
    Para obter mais informações sobre o RBAC do Azure com rede, consulte [funções internas de rede](../role-based-access-control/built-in-roles.md#networking).

- Às vezes, pode levar até 1 hora para que as novas atribuições de função entrem em vigor em todas as permissões armazenadas em cache em toda a pilha.
- Não há suporte para [acesso condicional](../role-based-access-control/conditional-access-azure-management.md) no momento com Azure Machine Learning.

## <a name="next-steps"></a>Próximas etapas

- [Visão geral de segurança empresarial](concept-enterprise-security.md)
- [Visão geral de isolamento de rede virtual e privacidade](how-to-network-security-overview.md)
- [Tutorial: Treinar modelos](tutorial-train-models-with-aml.md)
- [Operações de provedor de recursos](../role-based-access-control/resource-provider-operations.md#microsoftmachinelearningservices)
