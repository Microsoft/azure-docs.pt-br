---
title: 'Início Rápido: Nova atribuição de política com o Python'
description: Neste início rápido, use o Python para criar uma atribuição do Azure Policy para identificar recursos sem conformidade.
ms.date: 03/02/2021
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: 2c9a0d6bb00d82748505304264aeaefa409c4b06
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379373"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-python"></a>Início Rápido: Criar uma atribuição de política para identificar recurso sem conformidade usando Python

A primeira etapa para compreender a conformidade no Azure é identificar o status de seus recursos. Neste guia de início rápido, você cria uma atribuição de política para identificar máquinas virtuais que não estão usando discos gerenciados. Ao concluir, você identificará máquinas virtuais que _não estão em conformidade_.

A biblioteca de Python é usada para gerenciar recursos do Azure da linha de comando ou em scripts. Este guia explica como usar a biblioteca de Python para criar uma atribuição de política.

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-policy-library"></a>Adicionar a biblioteca de Políticas

Para habilitar o Python a trabalhar com o Azure Policy, a biblioteca deve ser adicionada. Essa biblioteca funciona onde quer que o Python possa ser usado, incluindo o [bash no Windows 10](/windows/wsl/install-win10) ou instalado localmente.

1. Verifique se o Python mais recente está instalado (pelo menos a versão **3.8**). Se ele ainda não tiver sido instalado, baixe-o em [Python.org](https://www.python.org/downloads/).

1. Verifique se a CLI do Azure mais recente está instalada (pelo menos a versão **2.5.1**). Se ela ainda não tiver sido instalada, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

   > [!NOTE]
   > A CLI do Azure é necessária para habilitar o Python a usar a **autenticação baseada na CLI** do nos exemplos a seguir. Para obter informações sobre outras opções, confira [Autenticar-se usando as bibliotecas de gerenciamento do Azure para Python](/azure/developer/python/azure-sdk-authenticate).

1. Autentique-se por meio da CLI do Azure.

   ```azurecli
   az login
   ```

1. No ambiente do Python de sua escolha, instale as bibliotecas necessárias para o Azure Policy:

   ```bash
   # Add the Python library for Python
   pip install azure-mgmt-policyinsights

   # Add the Resources library for Python
   pip install azure-mgmt-resource

   # Add the CLI Core library for Python for authentication (development only!)
   pip install azure-cli-core
   ```

   > [!NOTE]
   > Se o Python estiver instalado para todos os usuários, esse comando precisará ser executado em um console com privilégios elevados.

1. Confirme se as bibliotecas foram instaladas. `azure-mgmt-policyinsights` deve ser **0.5.0** ou mais recente, `azure-mgmt-resource` deve ser **9.0.0** ou mais recente e `azure-cli-core` deve ser **2.5.0** ou mais recente.

   ```bash
   # Check each installed library
   pip show azure-mgmt-policyinsights azure-mgmt-resource azure-cli-core
   ```

## <a name="create-a-policy-assignment"></a>Criar uma atribuição de política

Neste guia de início rápido, você cria uma atribuição de política e atribui a definição **Auditar VMs que não usam discos gerenciados** (`06a78e20-9358-41c9-923c-fb736d382a4d`). Esta definição de política identifica recursos que não estão em conformidade com as condições configuradas na definição de política.

Execute o seguinte código para criar uma atribuição de política:

```python
# Import specific methods and models from other libraries
from azure.common.credentials import get_azure_cli_credentials
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.resource.policy import PolicyClient
from azure.mgmt.resource.policy.models import PolicyAssignment

# Get your credentials from Azure CLI (development only!) and get your subscription list
policyClient = get_client_from_cli_profile(PolicyClient)

# Create details for the assignment
policyAssignmentDetails = PolicyAssignment(display_name="Audit VMs without managed disks Assignment", policy_definition_id="/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d", scope="{scope}", description="Shows all virtual machines not using managed disks")

# Create new policy assignment
policyAssignment = policyClient.policy_assignments.create("{scope}", "audit-vm-manageddisks", policyAssignmentDetails)

# Show results
print(policyAssignment)
```

Os comandos anteriores usam as seguintes informações:

Detalhes da atribuição:
- **display_name** – o nome de exibição da atribuição de política. Nesse caso, você está usando _Auditar VMs sem atribuição de discos gerenciados_.
- **policy_definition_id** – o caminho da definição da política, com base no que você está usando para criar a atribuição. Nesse caso, é a ID da definição de política _Auditar VMs que não usam discos gerenciados_. Neste exemplo, a definição de política é interna e o caminho não inclui informações sobre a assinatura nem sobre o grupo de gerenciamento.
- **scope** – um escopo determina em quais recursos ou agrupamento de recursos a atribuição de política é imposta. Ele pode variar de um grupo de gerenciamento a um recurso individual. Substitua `{scope}` por um dos seguintes padrões:
  - Grupo de gerenciamento: `/providers/Microsoft.Management/managementGroups/{managementGroup}`
  - Assinatura: `/subscriptions/{subscriptionId}`
  - Grupo de recursos: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
  - Recurso: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]`
- **description** – uma explicação mais detalhada do que a política faz ou de por que ela está atribuída a esse escopo.

Criação da atribuição:

- Escopo – esse escopo determina onde a atribuição de política é salva. O escopo definido nos detalhes da atribuição deve existir dentro desse escopo.
- Nome - O nome real da atribuição. Neste exemplo, usamos _audit-vm-manageddisks_.
- Atribuição de política – o objeto **PolicyAssignment** do Python criado na etapa anterior.

Agora você está pronto para identificar recursos fora de conformidade para entender o estado de conformidade do ambiente.

## <a name="identify-non-compliant-resources"></a>Identificar recursos fora de conformidade

Use as informações a seguir para identificar recursos que não são compatíveis com a atribuição de política que você criou. Execute o código a seguir:

```python
# Import specific methods and models from other libraries
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.policyinsights._policy_insights_client import PolicyInsightsClient
from azure.mgmt.policyinsights.models import QueryOptions

# Get your credentials from Azure CLI (development only!) and get your subscription list
policyInsightsClient = get_client_from_cli_profile(PolicyInsightsClient)

# Set the query options
queryOptions = QueryOptions(filter="IsCompliant eq false and PolicyAssignmentId eq 'audit-vm-manageddisks'",apply="groupby((ResourceId))")

# Fetch 'latest' results for the subscription
results = policyInsightsClient.policy_states.list_query_results_for_subscription(policy_states_resource="latest", subscription_id="{subscriptionId}", query_options=queryOptions)

# Show results
print(results)
```

Substitua `{subscriptionId}` pela assinatura para a qual você deseja ver os resultados de conformidade desta atribuição de política. Para ver uma lista de outros escopos e maneiras de resumir os dados, confira [Métodos de estado da política](/python/api/azure-mgmt-policyinsights/azure.mgmt.policyinsights.operations.policystatesoperations#methods).

Seus resultados devem se parecer com o exemplo a seguir:

```output
{
    'additional_properties': {
        '@odata.nextLink': None
    },
    'odatacontext': 'https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest',
    'odatacount': 12,
    'value': [{data}]
}
```

Os resultados correspondem ao que você vê na **Conformidade do recurso** de uma atribuição de política na exibição do portal do Azure.

## <a name="clean-up-resources"></a>Limpar os recursos

Use o seguinte comando para remover a atribuição criada:

```python
# Import specific methods and models from other libraries
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.resource.policy import PolicyClient

# Get your credentials from Azure CLI (development only!) and get your subscription list
policyClient = get_client_from_cli_profile(PolicyClient)

# Delete the policy assignment
policyAssignment = policyClient.policy_assignments.delete("{scope}", "audit-vm-manageddisks")

# Show results
print(policyAssignment)
```

Substitua `{scope}` pelo mesmo escopo usado para criar a atribuição de política.

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você atribuiu uma definição de política para identificar recursos sem conformidade em seu ambiente do Azure.

Para saber mais sobre definições de atribuição de políticas para validar que novos recursos estejam em conformidade, continue com o tutorial para:

> [!div class="nextstepaction"]
> [Criando e gerenciando políticas](./tutorials/create-and-manage.md)
