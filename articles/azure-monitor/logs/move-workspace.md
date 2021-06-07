---
title: Mover um espaço de trabalho Log Analytics no Azure Monitor | Microsoft Docs
description: Saiba como mover seu espaço de trabalho Log Analytics para outra assinatura ou grupo de recursos.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/12/2020
ms.openlocfilehash: 8f48ed1aa7422d6925c3a7b0ad30b59a479e4614
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102034941"
---
# <a name="move-a-log-analytics-workspace-to-different-subscription-or-resource-group"></a>Mover um espaço de trabalho Log Analytics para uma assinatura ou grupo de recursos diferente

Neste artigo, você aprenderá as etapas para mover Log Analytics espaço de trabalho para outro grupo de recursos ou assinatura na mesma região. Você pode aprender mais sobre como mover recursos do Azure por meio do portal do Azure, do PowerShell, do CLI do Azure ou da API REST. em [mover recursos para um novo grupo de recursos ou assinatura](../../azure-resource-manager/management/move-resource-group-and-subscription.md). 

> [!IMPORTANT]
> Não é possível mover um espaço de trabalho para uma região diferente.

## <a name="verify-active-directory-tenant"></a>Verificar Active Directory locatário
As assinaturas de origem e destino do espaço de trabalho devem existir dentro do mesmo locatário Azure Active Directory. Use Azure PowerShell para verificar se ambas as assinaturas têm a mesma ID de locatário.

``` PowerShell
(Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
(Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
```

## <a name="workspace-move-considerations"></a>Considerações de movimentação do espaço de trabalho
- As soluções gerenciadas que estão instaladas no espaço de trabalho serão movidas com a operação de movimentação do espaço de trabalho Log Analytics. 
- As chaves do espaço de trabalho (primária e secundária) são geradas novamente com a operação de movimentação do espaço de trabalho. Se você mantiver uma cópia de suas chaves de espaço de trabalho no Key Vault, atualize-as com as novas chaves geradas após a movimentação do espaço de trabalho. 
- Os agentes conectados permanecerão conectados e continuarão a enviar dados para o espaço de trabalho após a movimentação. 
- Como a operação de movimentação requer que não haja serviços vinculados do espaço de trabalho, as soluções que dependem desse link devem ser removidas para permitir a movimentação do espaço de trabalho. Soluções que devem ser removidas para que você possa desvincular sua conta de automação:
  - Gerenciamento de atualizações
  - Controle de Alterações
  - Iniciar/Parar VMs durante os horários inativos
  - Central de Segurança do Azure

>[!IMPORTANT]
> **Clientes do Azure Sentinel**
> - Atualmente, após o Azure Sentinel ser implantado em um espaço de trabalho, não há suporte para a movimentação do espaço de trabalho para outro grupo de recursos ou assinatura. 
> - Se você já tiver movido o workspace, desabilite todas as regras ativas em **Análise** e habilite-as novamente após cinco minutos. Essa deve ser uma solução eficaz na maioria dos casos, porém, para reiterar, ela não tem suporte e é executada por sua conta e risco.
> 
> **Recriar alertas**
> - Todos os alertas devem ser recriados após uma movimentação porque as permissões são baseadas na ID de recurso do Azure do espaço de trabalho, que é alterada durante uma movimentação de espaço de trabalho.
>
> **Atualizar caminhos de recurso**
> - Após a movimentação de um espaço de trabalho, qualquer recurso do Azure ou externo que aponte para o espaço de trabalho deve ser revisado e atualizado para apontar para o novo caminho de destino do recurso.
> 
>   *Exemplos:*
>   - [Azure Monitor regras de alerta](../alerts/alerts-resource-move.md)
>   - Aplicativo de terceiros
>   - Script personalizado
>

### <a name="delete-solutions-in-azure-portal"></a>Excluir soluções no portal do Azure
Use o procedimento a seguir para remover as soluções usando o portal do Azure:

1. Abra o menu do grupo de recursos no qual as soluções estão instaladas.
2. Selecione as soluções a serem removidas.
3. Clique em **excluir recursos** e confirme os recursos a serem removidos clicando em **excluir**.

![Excluir soluções](media/move-workspace/delete-solutions.png)

### <a name="delete-using-powershell"></a>Excluir usando o PowerShell

Para remover as soluções usando o PowerShell, use o cmdlet [Remove-AzResource](/powershell/module/az.resources/remove-azresource) , conforme mostrado no exemplo a seguir:

``` PowerShell
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM(<workspace-name>)" -ResourceGroupName <resource-group-name>
```

### <a name="remove-alert-rules-for-startstop-vms-solution"></a>Remover regras de alerta para iniciar/parar a solução de VMs
Para remover a solução **iniciar/parar VMs** , você também precisa remover as regras de alerta criadas pela solução. Use o procedimento a seguir no portal do Azure para remover essas regras.

1. Abra o menu **monitorar** e, em seguida, selecione **alertas**.
2. Clique em **gerenciar regras de alerta**.
3. Selecione as três regras de alerta a seguir e clique em **excluir**.

   - AutoStop_VM_Child
   - ScheduledStartStop_Parent
   - SequencedStartStop_Parent

    ![Excluir regras](media/move-workspace/delete-rules.png)

## <a name="unlink-automation-account"></a>Desvincular conta de automação
Use o procedimento a seguir para desvincular a conta de automação do espaço de trabalho usando o portal do Azure:

1. Abra o menu **contas de automação** e selecione a conta a ser removida.
2. Na seção **recursos relacionados** do menu, selecione espaço de **trabalho vinculado**. 
3. Clique em **desvincular espaço de trabalho** para desvincular o espaço de trabalho da sua conta de automação.

    ![Desvincular o workspace](media/move-workspace/unlink-workspace.png)

## <a name="move-your-workspace"></a>Mover seu espaço de trabalho

### <a name="azure-portal"></a>Portal do Azure
Use o procedimento a seguir para mover seu espaço de trabalho usando o portal do Azure:

1. Abra o menu **log Analytics espaços de trabalho** e selecione seu espaço de trabalho.
2. Na página **visão geral** , clique em **alterar** ao lado de **grupo de recursos** ou **assinatura**.
3. Uma nova página é aberta com uma lista de recursos relacionados ao espaço de trabalho. Selecione os recursos a serem movidos para a mesma assinatura de destino e grupo de recursos que o espaço de trabalho. 
4. Selecione uma **assinatura** de destino e um **grupo de recursos**. Se você estiver movendo o espaço de trabalho para outro grupo de recursos na mesma assinatura, você não verá a opção de **assinatura** .
5. Clique em **OK** para mover o espaço de trabalho e os recursos selecionados.

    ![Captura de tela mostra o painel Visão geral no espaço de trabalho Log Analytics com opções para alterar o grupo de recursos e o nome da assinatura.](media/move-workspace/portal.png)

### <a name="powershell"></a>PowerShell
Para mover seu espaço de trabalho usando o PowerShell, use o [move-AzResource](/powershell/module/AzureRM.Resources/Move-AzureRmResource) como no exemplo a seguir:

``` PowerShell
Move-AzResource -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyResourceGroup01/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace" -DestinationSubscriptionId "00000000-0000-0000-0000-000000000000" -DestinationResourceGroupName "MyResourceGroup02"
```

> [!IMPORTANT]
> Após a operação de movimentação, as soluções removidas e o link da conta de automação devem ser reconfigurados para colocar o espaço de trabalho de volta ao estado anterior.


## <a name="next-steps"></a>Próximas etapas
- Para obter uma lista dos recursos que dão suporte à movimentação, consulte [mover suporte de operação para recursos](../../azure-resource-manager/management/move-support-resources.md).
