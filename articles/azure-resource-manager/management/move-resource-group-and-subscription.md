---
title: Mover recursos para uma nova assinatura ou grupo de recursos
description: Use o Azure Resource Manager para mover recursos para um novo grupo de recursos ou uma nova assinatura.
ms.topic: conceptual
ms.date: 09/15/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: aca1e5255c89e99a2c996e072e5106da8dc3eef9
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101093630"
---
# <a name="move-resources-to-a-new-resource-group-or-subscription"></a>Mover recursos para um novo grupo de recursos ou uma nova assinatura

Este artigo mostra como mover recursos do Azure para outra assinatura do Azure ou outro grupo de recursos na mesma assinatura. Você pode usar o portal do Azure, Azure PowerShell, CLI do Azure ou a API REST para mover recursos.

O grupo de origem e o grupo de destino ficam bloqueados durante a operação de movimentação. As operações de gravação e exclusão são bloqueadas nos grupos de recursos até que a migração seja concluída. Esse bloqueio significa que você não pode adicionar, atualizar ou excluir recursos nos grupos de recursos. Não significa que os recursos estão congelados. Por exemplo, se você mover um SQL Server e seu banco de dados para um novo grupo de recursos, um aplicativo que usa o banco de dados não terá nenhuma inatividade. Ele ainda poderá ler e gravar no banco de dados. O bloqueio pode durar por um máximo de quatro horas, mas a maioria das mudanças é concluída em muito menos tempo.

Mover um recurso só o move para um novo grupo de recursos ou assinatura. Não altera o local do recurso.

## <a name="checklist-before-moving-resources"></a>Lista de verificação antes de mover recursos

Há algumas etapas importantes a serem executadas antes de mover um recurso. Ao verificar essas condições, é possível evitar erros.

1. Os recursos que você deseja mover devem oferecer suporte à operação de movimentação. Para obter uma lista dos recursos que dão suporte à movimentação, consulte [mover suporte de operação para recursos](move-support-resources.md).

1. Alguns serviços têm limitações ou requisitos específicos ao mover recursos. Se você estiver movendo qualquer um dos serviços a seguir, verifique essas diretrizes antes de mover.

   * Se você estiver usando Azure Stack Hub, não será possível mover recursos entre grupos.
   * [Diretrizes de movimentação de serviços de aplicativos](./move-limitations/app-service-move-limitations.md)
   * [Diretrizes de movimentação de Azure DevOps Services](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
   * [Diretrizes de movimentação do modelo de implantação clássica](./move-limitations/classic-model-move-limitations.md) -computação clássica, armazenamento clássico, redes virtuais clássicas e serviços de nuvem
   * [Diretrizes de movimentação de rede](./move-limitations/networking-move-limitations.md)
   * [Diretrizes de movimentação dos serviços de recuperação](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
   * [Diretrizes de movimentação de máquinas virtuais](./move-limitations/virtual-machines-move-limitations.md)

1. Se você mover um recurso que tenha uma função do Azure atribuída diretamente ao recurso (ou a um recurso filho), a atribuição de função não será movida e se tornará órfã. Após a movimentação, você deve recriar a atribuição de função. Eventualmente, a atribuição de função órfãa será removida automaticamente, mas é uma prática recomendada remover a atribuição de função antes de mover o recurso.

    Para obter informações sobre como gerenciar atribuições de função, consulte [listar atribuições de função do Azure](../../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-at-a-scope) e [atribuir funções do Azure](../../role-based-access-control/role-assignments-portal.md).

1. As assinaturas de origem e de destino devem estar ativas. Se você tiver problemas para habilitar uma conta que tenha sido desabilitada [crie uma solicitação de Suporte do Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Selecione **Subscription Management** para o tipo de problema.

1. As assinaturas de origem e de destino devem existir no mesmo [locatário do Azure Active Directory](../../active-directory/develop/quickstart-create-new-tenant.md). Para verificar se as duas assinaturas têm a mesma ID de locatário, use o Azure PowerShell ou a CLI do Azure.

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

   * [Transferir a propriedade de uma assinatura do Azure para outra conta](../../cost-management-billing/manage/billing-subscription-transfer.md)
   * [Como associar ou adicionar uma assinatura do Azure ao Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

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

   * **Microsoft. Resources/subscriptions/resourceGroups/moveResources/Action** no grupo de recursos de origem.
   * **Microsoft. Resources/subscriptions/resourceGroups/Write** no grupo de recursos de destino.

1. Antes de mover os recursos, verifique as cotas de assinatura da assinatura para a qual você está movendo os recursos. Se mover os recursos significa que a assinatura excederá seus limites, será necessário verificar se você pode solicitar um aumento na cota. Para obter uma lista de limites e como solicitar um aumento, consulte [Limites, cotas e restrições em assinaturas e serviços do Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md).

1. **Para uma movimentação entre assinaturas, o recurso e seus recursos dependentes devem estar localizados no mesmo grupo de recursos e devem ser movidos juntos.** Por exemplo, uma VM com discos gerenciados exigiria que a VM e os discos gerenciados fossem movidos juntos, juntamente com outros recursos dependentes.

   Se você estiver movendo um recurso para uma nova assinatura, verifique se o recurso tem quaisquer recursos dependentes e se eles estão localizados no mesmo grupo de recursos. Se os recursos não estiverem no mesmo grupo de recursos, verifique se os recursos podem ser combinados no mesmo grupo de recursos. Nesse caso, coloque todos esses recursos no mesmo grupo de recursos usando uma operação de movimentação entre grupos de recursos.

   Para obter mais informações, consulte [cenário para mover entre assinaturas](#scenario-for-move-across-subscriptions).

## <a name="scenario-for-move-across-subscriptions"></a>Cenário para mover entre assinaturas

Mover recursos de uma assinatura para outra é um processo de três etapas:

![cenário de movimentação entre assinaturas](./media/move-resource-group-and-subscription/cross-subscription-move-scenario.png)

Para fins de ilustração, temos apenas um recurso dependente.

* Etapa 1: se os recursos dependentes forem distribuídos em grupos de recursos diferentes, primeiro mova-os para um grupo de recursos.
* Etapa 2: Mova o recurso e os recursos dependentes da assinatura de origem para a assinatura de destino.
* Etapa 3: opcionalmente, redistribua os recursos dependentes para grupos de recursos diferentes na assinatura de destino.

## <a name="validate-move"></a>Validar movimentação

A [operação validar movimentação](/rest/api/resources/resources/validatemoveresources) permite testar seu cenário de movimentação sem realmente mover os recursos. Use esta operação para verificar se a movimentação será realizada com sucesso. A validação é chamada automaticamente quando você envia uma solicitação de movimentação. Use essa operação somente quando precisar predeterminar os resultados. Para executar essa operação, você precisa de:

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

Para mover recursos, selecione o grupo de recursos que contém esses recursos.

Quando você exibe o grupo de recursos, a opção mover é desabilitada.

:::image type="content" source="./media/move-resource-group-and-subscription/move-first-view.png" alt-text="opção de movimentação desabilitada":::

Para habilitar a opção mover, selecione os recursos que você deseja mover. Para selecionar todos os recursos, marque a caixa de seleção na parte superior da lista. Ou selecione recursos individualmente. Depois de selecionar os recursos, a opção mover é habilitada.

:::image type="content" source="./media/move-resource-group-and-subscription/select-resources.png" alt-text="selecionar recursos":::

Selecione o botão **mover** .

:::image type="content" source="./media/move-resource-group-and-subscription/move-options.png" alt-text="opções de movimentação":::

Esse botão oferece três opções:

* Mover para um novo grupo de recursos.
* Mover para uma nova assinatura.
* Mover para uma nova região. Para alterar as regiões, consulte [mover recursos entre regiões (do grupo de recursos)](../../resource-mover/move-region-within-resource-group.md?toc=/azure/azure-resource-manager/management/toc.json).

Selecione se você está movendo os recursos para um novo grupo de recursos ou uma nova assinatura.

Selecione o grupo de recursos de destino. Confirme que você precisa atualizar scripts para esses recursos e selecione **OK**. Se você optou por mover para uma nova assinatura, também deverá selecionar a assinatura de destino.

:::image type="content" source="./media/move-resource-group-and-subscription/move-destination.png" alt-text="Selecionar destino":::

Depois de validar que os recursos podem ser movidos, você verá uma notificação informando que a operação de movimentação está em execução.

:::image type="content" source="./media/move-resource-group-and-subscription/move-notification.png" alt-text="Notification":::

Quando for concluída, você será notificado sobre o resultado.

Se você receber um erro, consulte [solucionar problemas de movimentação de recursos do Azure para novo grupo de recursos ou assinatura](troubleshoot-move.md).

## <a name="use-azure-powershell"></a>Usar o Azure PowerShell

Para mover os recursos existentes para outro grupo de recursos ou assinatura, use o comando [Move-AzResource](/powershell/module/az.resources/move-azresource). O exemplo a seguir mostra como mover diversos recursos para um novo grupo de recursos.

```azurepowershell-interactive
$webapp = Get-AzResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

Para mover para uma nova assinatura, inclua um valor para o parâmetro `DestinationSubscriptionId`.

Se você receber um erro, consulte [solucionar problemas de movimentação de recursos do Azure para novo grupo de recursos ou assinatura](troubleshoot-move.md).

## <a name="use-azure-cli"></a>Usar a CLI do Azure

Para mover os recursos existentes para outro grupo de recursos ou assinatura, use o comando [az resource move](/cli/azure/resource#az-resource-move). Forneça as IDs dos recursos a mover. O exemplo a seguir mostra como mover diversos recursos para um novo grupo de recursos. No parâmetro `--ids`, forneça uma lista separada por espaços das IDs do recurso que deseja mover.

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

Para mover para uma nova assinatura, forneça o parâmetro `--destination-subscription-id`.

Se você receber um erro, consulte [solucionar problemas de movimentação de recursos do Azure para novo grupo de recursos ou assinatura](troubleshoot-move.md).

## <a name="use-rest-api"></a>Usar a API REST

Para mover os recursos existentes para outro grupo de recursos ou assinatura, use a operação [mover recursos](/rest/api/resources/Resources/MoveResources) .

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

Se você receber um erro, consulte [solucionar problemas de movimentação de recursos do Azure para novo grupo de recursos ou assinatura](troubleshoot-move.md).

## <a name="frequently-asked-questions"></a>Perguntas frequentes

**Pergunta: minha operação de movimentação de recursos, que geralmente leva alguns minutos, está em execução por quase uma hora. Há algo errado?**

Mover um recurso é uma operação complexa que tem diferentes fases. Ele pode envolver mais do que apenas o provedor de recursos do recurso que você está tentando mover. Devido às dependências entre os provedores de recursos, Azure Resource Manager permite que a operação seja concluída por 4 horas. Esse período de tempo dá aos provedores de recursos uma chance de se recuperar de problemas transitórios. Se sua solicitação de movimentação estiver dentro do período de quatro horas, a operação continuará tentando ser concluída e ainda poderá ser bem sucedido. Os grupos de recursos de origem e de destino são bloqueados durante esse tempo para evitar problemas de consistência.

**Pergunta: por que meu grupo de recursos está bloqueado por quatro horas durante a movimentação de recursos?**

Uma solicitação de movimentação é permitida no máximo quatro horas para ser concluída. Para evitar modificações nos recursos que estão sendo movidos, os grupos de recursos de origem e de destino são bloqueados durante a movimentação do recurso.

Há duas fases em uma solicitação de movimentação. Na primeira fase, o recurso é movido. Na segunda fase, as notificações são enviadas para outros provedores de recursos que dependem do recurso que está sendo movido. Um grupo de recursos pode ser bloqueado para as quatro horas inteiras quando um provedor de recursos falhar em qualquer fase. Durante o tempo permitido, o Gerenciador de recursos repete a etapa com falha.

Se um recurso não puder ser movido dentro de quatro horas, o Resource Manager desbloqueará os dois grupos de recursos. Os recursos que foram movidos com êxito estão no grupo de recursos de destino. Os recursos que não foram movidos são deixados no grupo de recursos de origem.

**Pergunta: quais são as implicações dos grupos de recursos de origem e de destino bloqueados durante a movimentação de recursos?**

O bloqueio impede que você exclua um dos grupos de recursos, criando um novo recurso em qualquer grupo de recursos ou excluindo qualquer um dos recursos envolvidos na movimentação.

A imagem a seguir mostra uma mensagem de erro do portal do Azure quando um usuário tenta excluir um grupo de recursos que faz parte de uma movimentação em andamento.

![Mover mensagem de erro ao tentar excluir](./media/move-resource-group-and-subscription/move-error-delete.png)

**Pergunta: o que significa o código de erro "MissingMoveDependentResources"?**

Ao mover um recurso, seus recursos dependentes devem existir no grupo de recursos de destino ou na assinatura ou ser incluídos na solicitação de movimentação. Você Obtém o código de erro MissingMoveDependentResources quando um recurso dependente não atende a esse requisito. A mensagem de erro tem detalhes sobre o recurso dependente que precisa ser incluído na solicitação de movimentação.

Por exemplo, mover uma máquina virtual pode exigir a movimentação de sete tipos de recursos com três provedores de recursos diferentes. Esses tipos e provedores de recursos são:

* Microsoft.Compute

  * virtualMachines
  * disks
* Microsoft.Network
  * networkInterfaces
  * publicIPAddresses
  * networkSecurityGroups
  * virtualNetworks
* Microsoft.Storage
  * storageAccounts

Outro exemplo comum envolve a movimentação de uma rede virtual. Talvez seja necessário mover vários outros recursos associados a essa rede virtual. A solicitação de movimentação pode exigir a movimentação de endereços IP públicos, tabelas de rotas, gateways de rede virtual, grupos de segurança de rede e outros.

**Pergunta: por que não posso mover alguns recursos no Azure?**

No momento, nem todos os recursos no Azure dão suporte à movimentação. Para obter uma lista de recursos que dão suporte à movimentação, consulte [mover suporte de operação para recursos](move-support-resources.md).

## <a name="next-steps"></a>Próximas etapas

Para obter uma lista dos recursos que dão suporte à movimentação, consulte [mover suporte de operação para recursos](move-support-resources.md).
