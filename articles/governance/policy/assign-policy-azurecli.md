---
title: 'Início Rápido: Nova atribuição de política com a CLI do Azure'
description: Neste início rápido, use a CLI do Azure para criar uma atribuição do Azure Policy para identificar recursos sem conformidade.
ms.date: 03/31/2021
ms.topic: quickstart
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2b7dad3eba18966a78fd35211ce40ea48234da30
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106090149"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-with-azure-cli"></a>Início Rápido: Criar uma atribuição de política para identificar recursos sem conformidade na CLI do Azure

A primeira etapa para compreender a conformidade no Azure é identificar o status de seus recursos.
Este guia de início rápido orienta você no processo de criação de uma atribuição de política para identificar máquinas virtuais que não estão usando discos gerenciados.

No final deste processo, você identificará com êxito quais máquinas virtuais não estão usando discos gerenciados. Eles _não estão em conformidade_ com a atribuição da política.

A CLI do Azure é usada para criar e gerenciar recursos do Azure da linha de comando ou em scripts. Este guia usa a CLI do Azure para criar uma atribuição de política e para identificar recursos sem conformidade em seu ambiente do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

- Este guia de início rápido exige que você execute a CLI do Azure versão 2.0.76 ou posterior. Para saber qual é a versão, execute `az --version`. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

- Registre o provedor de recursos do Azure Policy Insights usando a CLI do Azure. O registro do provedor de recursos garante o funcionamento da assinatura com ele. Para registrar um provedor de recursos, você deve ter permissão para registrar a operação do provedor de recursos. Esta operação está incluída nas funções de Colaborador e de Proprietário. Execute o seguinte comando para registrar o provedor de recursos:

  ```azurecli-interactive
  az provider register --namespace 'Microsoft.PolicyInsights'
  ```

  Para saber mais sobre como registrar e exibir provedores de recursos, consulte [Provedores de recursos e tipos](../../azure-resource-manager/management/resource-providers-and-types.md)

- Caso ainda não tenha feito, instale o [ARMClient](https://github.com/projectkudu/ARMClient). É uma ferramenta que envia solicitações HTTP para APIs baseadas no Azure Resource Manager.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-policy-assignment"></a>Criar uma atribuição de política

Neste guia de início rápido, crie uma atribuição de política e atribua a definição **Auditar VMs que não usam discos gerenciados**. Esta definição de política identifica recursos que não estão em conformidade com as condições configuradas na definição de política.

Execute o comando a seguir para criar uma atribuição de política:

```azurecli-interactive
az policy assignment create --name 'audit-vm-manageddisks' --display-name 'Audit VMs without managed disks Assignment' --scope '<scope>' --policy '<policy definition ID>'
```

O comando anterior usa as seguintes informações:

- **Nome** - O nome real da atribuição. Neste exemplo, usamos _audit-vm-manageddisks_.
- **DisplayName** - O nome de exibição da atribuição de política. Nesse caso, você está usando _Auditar VMs sem atribuição de discos gerenciados_.
- **Política**: a ID de definição da política, com base naquela que você está usando para criar a atribuição. Nesse caso, é a ID da definição de política _Auditar VMs que não usam discos gerenciados_. Para obter a ID de definição da política, execute este comando: `az policy definition list --query "[?displayName=='Audit VMs that do not use managed disks']"`
- **Escopo**: um escopo determina em quais recursos ou agrupamento de recursos a atribuição de política é imposta. Pode variar de uma assinatura a grupos de recursos. Substitua o &lt;escopo&gt; pelo nome do seu grupo de recursos.

## <a name="identify-non-compliant-resources"></a>Identificar recursos sem conformidade

Para exibir os recursos que não são compatíveis com essa nova atribuição, obtenha a ID de atribuição de política executando os comandos a seguir:

```azurecli-interactive
az policy assignment list --query "[?displayName=='Audit VMs without managed disks Assignment'].id"
```

Para saber mais sobre as IDs de atribuição de política, confira [az policy assignment](/cli/azure/policy/assignment).

Em seguida, execute o seguinte comando para obter as IDs de recurso dos recursos não compatíveis produzidos em um arquivo JSON:

```console
armclient post "/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$filter=IsCompliant eq false and PolicyAssignmentId eq '<policyAssignmentID>'&$apply=groupby((ResourceId))" > <json file to direct the output with the resource IDs into>
```

Seus resultados devem se parecer com o exemplo a seguir:

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
    "@odata.count": 3,
    "value": [{
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachineId>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine2Id>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionName>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine3ID>"
        }

    ]
}
```

Os resultados são comparáveis aos que você geralmente vê listados em **Recursos não compatível** na exibição do Portal do Azure.

## <a name="clean-up-resources"></a>Limpar os recursos

Use o seguinte comando para remover a atribuição criada:

```azurecli-interactive
az policy assignment delete --name 'audit-vm-manageddisks' --scope '/subscriptions/<subscriptionID>/<resourceGroupName>'
```

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você atribuiu uma definição de política para identificar recursos sem conformidade em seu ambiente do Azure.

Para saber mais sobre a atribuição de políticas para validar que novos recursos estejam em conformidade, continue com o tutorial para:

> [!div class="nextstepaction"]
> [Criando e gerenciando políticas](./tutorials/create-and-manage.md)
