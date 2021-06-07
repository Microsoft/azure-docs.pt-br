---
title: 'Início Rápido: Nova atribuição de política com a API REST'
description: Neste início rápido, use a API REST para criar uma atribuição do Azure Policy para identificar recursos sem conformidade.
ms.date: 01/29/2021
ms.topic: quickstart
ms.openlocfilehash: 438d8004cd50e6e2ef7586c51adc63257f37978b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99219970"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-with-rest-api"></a>Início Rápido: Criar uma atribuição de política para identificar recursos sem conformidade com a API REST

A primeira etapa para compreender a conformidade no Azure é identificar o status de seus recursos.
Este guia de início rápido orienta você no processo de criação de uma atribuição de política para identificar máquinas virtuais que não estão usando discos gerenciados.

No final deste processo, você identificará com êxito quais máquinas virtuais não estão usando discos gerenciados. Eles _não estão em conformidade_ com a atribuição da política.

A API REST é usada para criar e gerenciar recursos do Azure. Este guia usa a API REST para criar uma atribuição de política e para identificar recursos sem conformidade em seu ambiente do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

- Caso ainda não tenha feito isso, instale o [ARMClient](https://github.com/projectkudu/ARMClient). Trata-se de uma ferramenta que envia solicitações HTTP para APIs REST baseadas no Azure Resource Manager. Use também o recurso "Experimentar" na documentação da REST ou em ferramentas como o [Invoke-RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod) do PowerShell ou o [Postman](https://www.postman.com).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-policy-assignment"></a>Criar uma atribuição de política

Neste guia de início rápido, você cria uma atribuição de política e atribui a definição **Auditar VMs que não usam discos gerenciados** (`06a78e20-9358-41c9-923c-fb736d382a4d`). Esta definição de política identifica recursos que não estão em conformidade com as condições configuradas na definição de política.

Execute o comando a seguir para criar uma atribuição de política:

   - URI da API REST

     ```http
     PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/audit-vm-manageddisks?api-version=2019-09-01
     ```

   - Corpo da solicitação

     ```json
     {
       "properties": {
         "displayName": "Audit VMs without managed disks Assignment",
         "description": "Shows all virtual machines not using managed disks",
         "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d",
         "nonComplianceMessages": [
             {
                 "message": "Virtual machines should use a managed disk"
             }
         ]
       }
     }
     ```

O ponto de extremidade e o corpo da solicitação anteriores usam as seguintes informações:

URI da API REST:
- **Escopo**: um escopo determina em quais recursos ou agrupamento de recursos a atribuição de política é imposta. Ele pode variar de um grupo de gerenciamento a um recurso individual. Substitua `{scope}` por um dos seguintes padrões:
  - Grupo de gerenciamento: `/providers/Microsoft.Management/managementGroups/{managementGroup}`
  - Assinatura: `/subscriptions/{subscriptionId}`
  - Grupo de recursos: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
  - Recurso: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]{resourceType}/{resourceName}`
- **Nome** - O nome real da atribuição. Neste exemplo, usamos _audit-vm-manageddisks_.

Corpo da solicitação:
- **DisplayName** - O nome de exibição da atribuição de política. Nesse caso, você está usando _Auditar VMs sem atribuição de discos gerenciados_.
- **Description** – uma explicação mais detalhada do que a política faz ou de por que ela está atribuída a esse escopo.
- **policyDefinitionId** – a ID de definição da política, com base naquela que você está usando para criar a atribuição. Nesse caso, é a ID da definição de política _Auditar VMs que não usam discos gerenciados_.
- **nonComplianceMessages**: define a mensagem vista quando um recurso é negado devido à não conformidade ou avaliado como fora de conformidade. Para obter mais informações, confira [Mensagens de não conformidade de atribuição](./concepts/assignment-structure.md#non-compliance-messages).

## <a name="identify-non-compliant-resources"></a>Identificar recursos fora de conformidade

Para ver os recursos fora de conformidade com essa nova atribuição, execute o seguinte comando para obter as IDs de recurso dos recursos não compatíveis produzidos em um arquivo JSON:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$filter=IsCompliant eq false and PolicyAssignmentId eq 'audit-vm-manageddisks'&$apply=groupby((ResourceId))"
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
            "ResourceId": "/subscriptions/<subscriptionName>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine3Id>"
        }

    ]
}
```

Os resultados são comparáveis aos que você geralmente vê listados em **Recursos não compatível** na exibição do Portal do Azure.

## <a name="clean-up-resources"></a>Limpar os recursos

Use o seguinte comando para remover a atribuição criada:

```http
DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/audit-vm-manageddisks?api-version=2019-09-01
```

Substitua `{scope}` pelo escopo usado ao criar a atribuição de política.

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você atribuiu uma definição de política para identificar recursos sem conformidade em seu ambiente do Azure.

Para saber mais sobre a atribuição de políticas para validar que novos recursos estejam em conformidade, continue com o tutorial para:

> [!div class="nextstepaction"]
> [Criando e gerenciando políticas](./tutorials/create-and-manage.md)
