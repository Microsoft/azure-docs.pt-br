---
title: Mover recursos do Azure para um novo grupo de recursos ou assinatura | Microsoft Docs
description: Use o Azure Resource Manager para mover recursos para um novo grupo de recursos ou uma nova assinatura.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: 01ec8facf2771de9ec01b9470521340a59ee4d0d
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721393"
---
# <a name="move-resources-to-a-new-resource-group-or-subscription"></a>Mover recursos para um novo grupo de recursos ou assinatura

Este artigo mostra como mover recursos do Azure para outra assinatura do Azure ou outro grupo de recursos na mesma assinatura. Você pode usar o portal do Azure, Azure PowerShell, CLI do Azure ou a API REST para mover recursos.

O grupo de origem e o grupo de destino ficam bloqueados durante a operação de movimentação. As operações de gravação e exclusão são bloqueadas nos grupos de recursos até que a migração seja concluída. Esse bloqueio significa que você não pode adicionar, atualizar nem excluir os recursos nos grupos de recursos, mas isso não significa que os recursos estão congelados. Por exemplo, se você mover um SQL Server e seu banco de dados para um novo grupo de recursos, um aplicativo que usa o banco de dados não terá nenhuma inatividade. Ele ainda poderá ler e gravar no banco de dados.

Mover um recurso só o move para um novo grupo de recursos ou assinatura. Ele não altera o local do recurso.

## <a name="checklist-before-moving-resources"></a>Lista de verificação antes de mover recursos

Há algumas etapas importantes a serem executadas antes de mover um recurso. Ao verificar essas condições, é possível evitar erros.

1. Os recursos que você deseja mover devem oferecer suporte a operação de movimentação. Para obter uma lista dos quais recursos de suporte para a movimentação, consulte [suporte para recursos de operação de mover](move-support-resources.md).

1. Alguns serviços têm requisitos ou limitações específicas ao mover recursos. Se você estiver movendo qualquer um dos seguintes serviços, verifique essas diretrizes antes de mover.

   * [Diretrizes de mover os serviços de aplicativo](./move-limitations/app-service-move-limitations.md)
   * [Diretrizes de mover os serviços de DevOps do Azure](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
   * [Diretrizes de movimentação de modelo de implantação clássico](./move-limitations/classic-model-move-limitations.md) -clássico de computação, armazenamento clássico, as redes virtuais clássicas e os serviços de nuvem
   * [Diretrizes de mover os serviços de recuperação](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
   * [Diretrizes de mover máquinas virtuais](./move-limitations/virtual-machines-move-limitations.md)
   * [Diretrizes de mover de redes virtuais](./move-limitations/virtual-network-move-limitations.md)

1. As assinaturas de origem e de destino devem estar ativas. Se você tiver problemas para habilitar uma conta que tenha sido desabilitada [crie uma solicitação de Suporte do Azure](../azure-supportability/how-to-create-azure-support-request.md). Selecione **Subscription Management** para o tipo de problema.

1. As assinaturas de origem e de destino devem existir no mesmo [locatário do Azure Active Directory](../active-directory/develop/quickstart-create-new-tenant.md). Para verificar se as duas assinaturas têm a mesma ID de locatário, use o Azure PowerShell ou a CLI do Azure.

   Para o Azure PowerShell, use:

   ```azurepowershell-interactive
   (Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
   (Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
   ```

   Para a CLI do Azure, use:

   ```azurecli-interactive
   az account show --subscription <your-source-subscription> --query tenantId
   az account show --subscription <your-destination-subscription> --query tenantId
   ```

   Se as IDs de locatário para as assinaturas de origem e de destino não forem iguais, use os métodos a seguir para reconciliá-las:

   * [Transferir a propriedade de uma assinatura do Azure para outra conta](../billing/billing-subscription-transfer.md)
   * [Como associar ou adicionar uma assinatura do Azure ao Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

1. A assinatura de destino deve estar registrada para que o provedor de recursos do recurso seja movido. Se não estiver, você receberá um erro afirmando que a **assinatura não está registrada para um tipo de recurso**. Você pode ver esse erro ao mover um recurso para uma nova assinatura que nunca tenha sido usada com esse tipo de recurso.

   Para o PowerShell, use os seguintes comandos para obter o status do registro:

   ```azurepowershell-interactive
   Set-AzContext -Subscription <destination-subscription-name-or-id>
   Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
   ```

   Para registrar um provedor de recursos, use:

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
   ```

   Para a CLI do Azure, use os seguintes comandos para obter o status do registro:

   ```azurecli-interactive
   az account set -s <destination-subscription-name-or-id>
   az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
   ```

   Para registrar um provedor de recursos, use:

   ```azurecli-interactive
   az provider register --namespace Microsoft.Batch
   ```

1. A conta de movimentação de recursos deve ter pelo menos as seguintes permissões:

   * **Microsoft.Resources/subscriptions/resourceGroups/moveResources/action** no grupo de recursos de origem.
   * **Microsoft.Resources/subscriptions/resourceGroups/write** no grupo de recursos de destino.

1. Antes de mover os recursos, verifique as cotas de assinatura da assinatura para a qual você está movendo os recursos. Se mover os recursos significa que a assinatura excederá seus limites, será necessário verificar se você pode solicitar um aumento na cota. Para obter uma lista de limites e como solicitar um aumento, consulte [Limites, cotas e restrições em assinaturas e serviços do Azure](../azure-subscription-service-limits.md).

## <a name="validate-move"></a>Validar movimentação

A operação [validate move](/rest/api/resources/resources/validatemoveresources) permite que você teste seu cenário de movimentação sem realmente mover os recursos. Use esta operação para verificar se a movimentação será bem-sucedida. Validação é chamada automaticamente quando você envia uma solicitação de movimentação. Use esta operação somente quando você precisa predeterminar os resultados. Para executar essa operação, você precisa de:

* nome do grupo de recursos de origem
* ID do recurso do grupo de recursos de destino
* ID do recurso de cada recurso para mover
* o [token de acesso](/rest/api/azure/#acquire-an-access-token) para sua conta

Envie a seguinte solicitação:

```HTTP
POST https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<source-group>/validateMoveResources?api-version=2019-05-10
Authorization: Bearer <access-token>
Content-type: application/json
```

Com um corpo de solicitação:

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

Se a solicitação estiver formatada corretamente, a operação retornará:

```HTTP
Response Code: 202
cache-control: no-cache
pragma: no-cache
expires: -1
location: https://management.azure.com/subscriptions/<subscription-id>/operationresults/<operation-id>?api-version=2018-02-01
retry-after: 15
...
```

O código de status 202 indica que a solicitação de validação foi aceita, mas ainda não determinou se a operação de movimentação será bem-sucedida. O valor `location` contém um URL que você usa para verificar o status da operação de longa duração.  

Para verificar o status, envie a seguinte solicitação:

```HTTP
GET <location-url>
Authorization: Bearer <access-token>
```

Enquanto a operação ainda está em execução, você continua recebendo o código de status 202. Aguarde o número de segundos indicado no valor `retry-after` antes de tentar novamente. Se a operação de movimentação é validado com êxito, você receberá o código de 204 status. Se a validação da movimentação falhar, você receberá uma mensagem de erro, como:

```json
{"error":{"code":"ResourceMoveProviderValidationFailed","message":"<message>"...}}
```

## <a name="use-the-portal"></a>Usar o portal

Para mover recursos, selecione o grupo de recursos que os contém e, em seguida, selecione o botão **Mover**.

![Mover recursos](./media/resource-group-move-resources/select-move.png)

Selecione se você está movendo os recursos para um novo grupo de recursos ou uma nova assinatura.

Selecione os recursos a serem movidos e o grupo de recursos de destino. Confirme que você precisa atualizar scripts para esses recursos e selecione **OK**. Se tiver selecionado o ícone de edição da assinatura na etapa anterior, você também precisará selecionar a assinatura de destino.

![selecionar destino](./media/resource-group-move-resources/select-destination.png)

Em **Notificações**, você verá que a operação de movimentação está em execução.

![mostrar status da movimentação](./media/resource-group-move-resources/show-status.png)

Quando for concluída, você será notificado sobre o resultado.

![mostrar resultado da movimentação](./media/resource-group-move-resources/show-result.png)

Se você receber um erro, consulte [solucione o problema mover os recursos do Azure para um novo grupo de recursos ou assinatura](troubleshoot-move.md).

## <a name="use-azure-powershell"></a>Usar PowerShell do Azure

Para mover os recursos existentes para outro grupo de recursos ou assinatura, use o comando [Move-AzResource](/powershell/module/az.resources/move-azresource). O exemplo a seguir mostra como mover diversos recursos para um novo grupo de recursos.

```azurepowershell-interactive
$webapp = Get-AzResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

Para mover para uma nova assinatura, inclua um valor para o parâmetro `DestinationSubscriptionId`.

Se você receber um erro, consulte [solucione o problema mover os recursos do Azure para um novo grupo de recursos ou assinatura](troubleshoot-move.md).

## <a name="use-azure-cli"></a>Usar a CLI do Azure

Para mover os recursos existentes para outro grupo de recursos ou assinatura, use o comando [az resource move](/cli/azure/resource?view=azure-cli-latest#az-resource-move). Forneça as IDs dos recursos a mover. O exemplo a seguir mostra como mover diversos recursos para um novo grupo de recursos. No parâmetro `--ids`, forneça uma lista separada por espaços das IDs do recurso que deseja mover.

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

Para mover para uma nova assinatura, forneça o parâmetro `--destination-subscription-id`.

Se você receber um erro, consulte [solucione o problema mover os recursos do Azure para um novo grupo de recursos ou assinatura](troubleshoot-move.md).

## <a name="use-rest-api"></a>Usar a API REST

Para mover recursos existentes para outro grupo de recursos ou assinatura, use o [mover recursos](/rest/api/resources/Resources/MoveResources) operação.

```HTTP
POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version}
```

No corpo da solicitação, especifique o grupo de recursos de destino e os recursos para mover.

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

Se você receber um erro, consulte [solucione o problema mover os recursos do Azure para um novo grupo de recursos ou assinatura](troubleshoot-move.md).

## <a name="next-steps"></a>Próximas etapas

Para obter uma lista dos quais recursos de suporte para a movimentação, consulte [suporte para recursos de operação de mover](move-support-resources.md).
