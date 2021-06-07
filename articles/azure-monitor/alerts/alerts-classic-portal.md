---
title: Criar e gerenciar alertas de métrica clássicos usando o Azure Monitor
description: Saiba como usar portal do Azure, CLI ou PowerShell para criar, exibir e gerenciar regras de alerta de métricas clássicas.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 02/14/2021
ms.openlocfilehash: 854867bb6f40bdd8f0fac28e69d936a4027760b5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101714367"
---
# <a name="create-view-and-manage-classic-metric-alerts-using-azure-monitor"></a>Criar, exibir e gerenciar alertas de métrica clássicos usando o Azure Monitor

> [!WARNING]
> Este artigo descreve como criar alertas de métrica clássicos mais antigos. O Azure Monitor agora dá suporte [a alertas de métrica quase em tempo real mais recentes e uma nova experiência de alertas](./alerts-overview.md). Os alertas clássicos são [desativados](./monitoring-classic-retirement.md) para usuários de nuvem pública, embora ainda estejam em uso limitado até **31 de maio de 2021**. Os alertas clássicos para a nuvem do Azure governamental e o Azure China 21Vianet serão desativados em **29 de fevereiro de 2024**.
>

Os alertas de métrica clássicos no Azure Monitor fornecem uma maneira para notificação quando uma das suas métricas ultrapassarem um limite. Os alertas de métrica clássicos são uma funcionalidade mais antiga que permite alertas somente em métricas não dimensionais. Há uma funcionalidade mais recente, chamada alertas de métrica, que tem funcionalidade aprimorada sobre alertas de métrica clássicos. Você pode aprender mais sobre a nova funcionalidade de alertas de métrica na [visão geral de alertas de métrica](./alerts-metric-overview.md). Neste artigo, descreveremos como criar, exibir e gerenciar regras de alerta de métricas clássicas por meio de portal do Azure, CLI do Azure e PowerShell.

## <a name="with-azure-portal"></a>Com o portal do Azure

1. No [portal](https://portal.azure.com/), localize o recurso que você quer monitorar e, em seguida, selecione-o.

2. Na seção **MONITORAMENTO**, selecione **Alertas (Clássico)**. O texto e o ícone podem variar ligeiramente para diferentes recursos. Se você não localizar **Alertas (Clássico)** aqui, poderá encontrá-lo em **Alertas** ou **Regras de Alerta**.

    ![Monitoramento](media/alerts-classic-portal/AlertRulesButton.png)

3. Selecione o comando **Adicionar alerta de métrica (clássico)** e preencha os campos.

    ![Adicionar alerta](media/alerts-classic-portal/AddAlertOnlyParamsPage.png)

4. **Nomeie** a regra de alerta. Em seguida, escolha uma **Descrição**, que também aparece nos emails de notificação.

5. Selecione a **Métrica** que você quer monitorar. Em seguida, escolha um valor de **Condição** e **Limite** para a métrica. Escolha também o **Período** durante o qual a regra de métrica deverá ser atendida antes de o alerta disparar. Por exemplo, se você usar o período "Pelos últimos 5 minutos" e o alerta procura por CPU acima de 80%, o alerta disparará quando a CPU estiver consistentemente acima de 80% por 5 minutos. Depois que o primeiro gatilho ocorrer, ele será disparado novamente quando a CPU ficar abaixo de 80% por 5 minutos. A medição da métrica da CPU acontece a cada minuto.

6. Selecione **Proprietários de email...** se você quiser que administradores e coadministradores recebam notificações por email quando o alerta for acionado.

7. Se você quiser enviar notificações para endereços de email adicionais quando o alerta for acionado, adicione-os no campo **Emails adicionais do administrador**. Separe vários emails com ponto e vírgula, no seguinte formato: *email \@ contoso. com; EMAIL2 \@ contoso.com*

8. Coloque um URI válido no campo **Webhook** se você quiser que ele seja chamado quando o alerta for acionado.

9. Se você usar a Automação do Azure, selecione um runbook a ser executado quando o alerta for acionado.

10. Selecione **OK** para criar o alerta.

Em alguns minutos, o alerta estará ativo e disparará conforme descrito anteriormente.

Após criar um alerta, você poderá selecioná-lo e executar uma das seguintes tarefas:

* Exibir um gráfico mostrando o limite da métrica e os valores reais do dia anterior.
* Editar ou exclui-lo.
* **Desabilitar** ou **Habilitar** se você quiser interromper temporariamente ou continuar recebendo notificações do alerta.

## <a name="with-azure-cli"></a>Com a CLI do Azure

As seções anteriores descreveram como criar, exibir e gerenciar regras de alerta de métrica usando o portal do Azure. Esta seção descreve como fazer o mesmo usando a [CLI do Azure](/cli/azure/get-started-with-azure-cli) de multiplataforma. O modo mais rápido de começar a usar a CLI do Azure é com o [Azure Cloud Shell](../../cloud-shell/overview.md).

### <a name="get-all-classic-metric-alert-rules-in-a-resource-group"></a>Obtenha regras de alertas de métrica clássicos em um grupo de recursos

```azurecli
az monitor alert list --resource-group <group name>
```

### <a name="see-details-of-a-particular-classic-metric-alert-rule"></a>Consulte os detalhes de uma determinada regra de alerta de métrica clássico

```azurecli
az monitor alert show --resource-group <group name> --name <alert name>
```

### <a name="create-a-classic-metric-alert-rule"></a>Crie uma regra de alerta de métrica clássico

```azurecli
az monitor alert create --name <alert name> --resource-group <group name> \
    --action email <email1 email2 ...> \
    --action webhook <URI> \
    --target <target object ID> \
    --condition "<METRIC> {>,>=,<,<=} <THRESHOLD> {avg,min,max,total,last} ##h##m##s"
```

### <a name="delete-a-classic-metric-alert-rule"></a>Exclua uma regra de alerta de métrica clássico

```azurecli
az monitor alert delete --name <alert name> --resource-group <group name>
```

## <a name="with-powershell"></a>Com o PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Esta seção mostra como usar os comandos do PowerShell para criar, exibir e gerenciar alertas de métrica clássicos. Os exemplos neste artigo ilustram como você pode usar os cmdlets do Azure Monitor para alertas de métrica clássicos.

1. Se ainda não tiver feito isso, configure o PowerShell para ser executado no seu computador. Para saber mais, consulte [Como instalar e configurar o PowerShell](/powershell/azure/). Você também pode ver a lista completa de cmdlets do PowerShell do Azure Monitor em [Cmdlets do Azure Monitor (Insights)](/powershell/module/az.applicationinsights).

2. Primeiro, entre em sua assinatura do Azure.

    ```powershell
    Connect-AzAccount
    ```

3. Você verá uma tela de entrada. Quando entrar, sua Conta, sua TenantID e a ID da Assinatura padrão serão exibidas. Todos os cmdlets do Azure funcionam no contexto de sua assinatura padrão. Para exibir a lista de assinaturas a que você tem acesso, use o seguinte comando:

    ```powershell
    Get-AzSubscription
    ```

4. Para alterar o contexto de trabalho para uma assinatura diferente, use o comando a seguir:

    ```powershell
    Set-AzContext -SubscriptionId <subscriptionid>
    ```

5. É possível obter todas as regras de alertas de métrica clássicos em um grupo de recursos:

    ```powershell
    Get-AzAlertRule -ResourceGroup montest
    ```

6. É possível exibir detalhes de uma regra de alerta de métrica clássico

    ```powershell
    Get-AzAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
    ```

7. É possível recuperar todo o conjunto de regras de alerta para um recurso de destino. Por exemplo, todas as regras de alerta definidas em uma VM.

    ```powershell
    Get-AzAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
    ```

8. Regras de alerta clássico não podem ser criadas por meio do PowerShell. Para criar uma regra de alerta, você precisa usar o novo comando [' Add-AzMetricAlertRule '](/powershell/module/az.monitor/add-azmetricalertrule) .

## <a name="next-steps"></a>Próximas etapas

- [Criar um alerta de métrica clássico com um modelo do Resource Manager](./alerts-enable-template.md).
- [Fazer um alerta de métrica clássico notificar um sistema diferente do Azure usando um webhook](./alerts-webhooks.md).