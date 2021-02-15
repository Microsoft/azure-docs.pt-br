---
title: 'Tutorial: criar e gerenciar orçamentos do Azure'
description: Este tutorial ajuda a planejar e contabilizar os custos de serviços do Azure que você consome.
author: bandersmsft
ms.author: banders
ms.date: 01/27/2021
ms.topic: tutorial
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.custom: seodec18
ms.openlocfilehash: 5659f3b2d020a97ed2460c55283bb41f2f7606cc
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943731"
---
# <a name="tutorial-create-and-manage-azure-budgets"></a>Tutorial: criar e gerenciar orçamentos do Azure

Orçamentos no Gerenciamento de Custos ajudam você a planejar e promover responsabilidade organizacional. Com orçamentos, você pode considerar os serviços do Azure que consome ou assina durante um período específico. Eles ajudam você a informar outras pessoas sobre seus gastos para gerenciar proativamente os custos e monitorar como os gastos evoluem ao longo do tempo. Quando os limites de orçamento que você criou são excedidos, apenas notificações são disparadas. Nenhum de seus recursos é afetado e seu consumo não é interrompido. Você pode usar os orçamentos para comparar e controlar como analisar os custos de gastos.

Os dados de custo e de uso geralmente ficam disponíveis no prazo de 8 a 24 horas, e os orçamentos são avaliados em relação a esses custos a cada 24 horas. Familiarize-se com os aspectos específicos das [Atualizações de dados de custo e de uso](./understand-cost-mgt-data.md#cost-and-usage-data-updates-and-retention). Quando um limite de orçamento é atingido, as notificações por email são enviadas normalmente em até uma hora da avaliação.

Os orçamentos são redefinidos automaticamente no final de um período (mensal, trimestral ou anual) para o mesmo valor de orçamento quando você seleciona uma data de expiração no futuro. Uma vez que redefinidos com o mesmo valor de orçamento, você precisará criar orçamentos separados quando os valores monetários orçados forem diferentes para períodos futuros. Quando um orçamento expira, ele é excluído automaticamente.

Os exemplos deste tutorial ajudam você a criar e editar um orçamento para uma assinatura do Azure Enterprise Agreement (EA).

Assista ao vídeo [Aplicar orçamentos a assinaturas usando o portal do Azure](https://www.youtube.com/watch?v=UrkHiUx19Po) para ver como você pode criar orçamentos no Azure para monitorar gastos. Para assistir a outros vídeos, visite o [Canal do YouTube do Gerenciamento de Custos](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/UrkHiUx19Po]

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um orçamento no portal do Azure
> * Criar e editar orçamentos com o PowerShell
> * Criar um orçamento com um modelo do Azure Resource Manager

## <a name="prerequisites"></a>Pré-requisitos

Os orçamentos têm compatibilidade com os seguintes tipos de escopos e tipos de conta do Azure:

- Escopos do RBAC do Azure (controle de acesso baseado em função do Azure)
    - Grupos de gerenciamento
    - Subscription
- Escopos do Contrato Enterprise
    - Conta de cobrança
    - department
    - Conta de registro
- Contratos individuais
    - Conta de cobrança
- Escopos do Contrato de Cliente da Microsoft
    - Conta de cobrança
    - Perfil de faturamento
    - Seção da fatura
    - Cliente
- Escopos da AWS
    - Conta externa
    - Assinatura externa


Para exibir os orçamentos, você precisará, pelo menos, de acesso de leitura em sua conta do Azure.

Se você tiver uma nova assinatura, não poderá criar um orçamento imediatamente nem usar outros recursos do Gerenciamento de Custos. Poderá levar até 48 horas para você poder usar todos os recursos do Gerenciamento de Custos.

Para assinatura do Azure EA, você deve ter acesso de leitura para exibir orçamentos. Para criar e gerenciar orçamentos, você deve ter permissão de colaborador.

Há suporte para as permissões ou escopos a seguir do Azure por assinatura para orçamentos por usuário e grupo. Para obter mais informações sobre escopos, consulte [Entender e trabalhar com escopos](understand-work-scopes.md).

- Proprietário – pode criar, modificar ou excluir os orçamentos para uma assinatura.
- Colaborador e contribuidor do Gerenciamento de Custos - pode criar, modificar ou excluir seus próprios orçamentos. Pode modificar o valor do orçamento para orçamentos criados por outras pessoas.
- Leitor e leitor do Gerenciamento de Custos – pode exibir os orçamentos para os quais ele têm permissão.

Para obter mais informações sobre como atribuir permissões aos dados de Gerenciamento de Custos, consulte [Atribuir acesso aos dados de Gerenciamento de Custos](./assign-access-acm-data.md).

## <a name="sign-in-to-azure"></a>Entrar no Azure

- Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-budget-in-the-azure-portal"></a>Criar um orçamento no portal do Azure

Você pode criar um orçamento de assinatura do Azure para um período mensal, trimestral ou anual.

Para criar ou exibir um orçamento, abra o escopo desejado no portal do Azure e selecione **Orçamentos** no menu. Por exemplo, navegue até **Inscrições**, selecione uma assinatura na lista e, em seguida, selecione **Orçamentos** no menu. Use o controle oval **Escopo** para alterar para outro escopo, como um grupo de gerenciamento, em Orçamentos. Para obter mais informações sobre escopos, consulte [Entender e trabalhar com escopos](understand-work-scopes.md).

Depois de criar os orçamentos, eles mostram uma exibição simples de seus gastos atual em relação a eles.

Selecione **Adicionar**.

![Exemplo mostrando uma lista de orçamentos já criados](./media/tutorial-acm-create-budgets/budgets01.png)

Na janela **Criar orçamento**, verifique se o escopo mostrado está correto. Escolha os filtros que deseja adicionar. Os filtros permitem que você crie orçamentos para custos específicos, como grupos de recursos em uma assinatura ou um serviço, como máquinas virtuais. Qualquer filtro que você puder usar na análise de custo também poderá ser aplicado a um orçamento.

Depois de identificar o escopo e os filtros, digite um nome para o orçamento. Em seguida, escolha um período de redefinição de orçamento mensal, trimestral ou anual. Esse período de redefinição determina a janela de tempo que é analisada pelo orçamento. O custo avaliado pelo orçamento começa em zero no início de cada novo período. Quando você cria um orçamento trimestral, ele funciona da mesma maneira que um orçamento mensal. A diferença é que o valor do orçamento para o trimestre é dividido entre os três meses do trimestre. Um valor de orçamento anual é dividido de maneira uniforme entre todos os 12 meses do ano civil.

Se você tiver uma assinatura de Pagamento Conforme o Uso, do MSDN ou do Visual Studio, o período de cobrança da fatura talvez não esteja alinhado ao mês do calendário. Para esses tipos de assinaturas e grupos de recursos, você pode criar um orçamento alinhado ao período da fatura ou aos meses do calendário. Para criar um orçamento alinhado ao seu período de fatura, selecione um período de redefinição de **Mês de cobrança**, **Trimestre de cobrança** ou **Ano de cobrança**. Para criar um orçamento alinhado ao mês do calendário, selecione um período de redefinição **Mensal**, **Trimestral** ou **Anual**.

Em seguida, identifique uma data de validade, em que o orçamento se torna inválido e para de avaliar seus custos.

Com base nos campos escolhidos no orçamento até o momento, é mostrado um grafo para ajudar você a selecionar um limite a ser usado para o orçamento. O orçamento sugerido é baseado no custo previsto mais alto que você pode incorrer em períodos futuros. Você pode alterar o valor do orçamento.

![Exemplo mostrando a criação de um orçamento usando dados de custo mensal ](./media/tutorial-acm-create-budgets/monthly-budget01.png)

Depois de configurar o valor do orçamento, selecione **Avançar** para configurar os alertas do orçamento. Orçamentos exigem pelo menos um limite de custo (% do orçamento) e um endereço de email correspondente. Opcionalmente, você pode incluir até cinco limites e cinco endereços de email em um único orçamento. Quando um limite de orçamento é atingido, as notificações por email são enviadas normalmente em até uma hora da avaliação.

Se você quiser receber emails, adicione azure-noreply@microsoft.com à sua lista de remetentes aprovados para que os emails não entrem na pasta de lixo eletrônico. Para obter mais informações sobre as notificações, confira [Alertas de custo de uso](./cost-mgt-alerts-monitor-usage-spending.md).

No exemplo a seguir, um alerta por email é gerado quando 90% do orçamento é atingido. Se criar um orçamento com a API de Orçamentos, você também poderá atribuir funções para que pessoas recebam os alertas. Não há suporte para a atribuição de funções a pessoas no portal do Azure. Para obter mais informações sobre a API de Orçamentos do Azure, confira [API de Orçamentos](/rest/api/consumption/budgets). Para enviar alertas por email em um idioma diferente, confira [Localidades com suporte para alertas de orçamento por email](manage-automation.md#supported-locales-for-budget-alert-emails).

Os limites de alerta dão suporte a um intervalo de 0,01 a 1000% do limite de orçamento que você forneceu.

![Exemplo mostrando condições de alerta](./media/tutorial-acm-create-budgets/monthly-budget-alert.png)

Depois de criar um orçamento, ele é mostrado na análise de custo. Exibir seu orçamento em relação à sua tendência de gastos é uma das primeiras etapas quando você começa a [analisar seus custos e gastos](./quick-acm-cost-analysis.md).

![Exemplo de orçamento e gastos mostrados na análise de custos](./media/tutorial-acm-create-budgets/cost-analysis.png)

No exemplo anterior, você criou um orçamento para uma assinatura. Você também pode criar um orçamento para um grupo de recursos. Se você quiser criar um orçamento para um grupo de recursos, navegue até **Gerenciamento de Custos + Cobrança**&gt;**Assinaturas** &gt; selecione uma assinatura > **Grupo de recursos** > selecione um grupo de recursos > **Orçamentos** > e, em seguida escolha **Adicionar** um orçamento.

### <a name="create-a-budget-for-combined-azure-and-aws-costs"></a>Criar um orçamento para os custos combinados do Azure e do AWS

Você pode agrupar seus custos do Azure e do AWS atribuindo um grupo de gerenciamento ao conector, juntamente com as contas consolidadas e vinculadas. Atribua suas assinaturas do Azure ao mesmo grupo de gerenciamento. Em seguida, crie um orçamento para os custos combinados.

1. No Gerenciamento de Custos, selecione **Orçamentos**.
1. Selecione **Adicionar**.
1. Selecione **Alterar escopo** e escolha o grupo de gerenciamento.
1. Continue criando o orçamento até concluí-lo.

## <a name="costs-in-budget-evaluations"></a>Avaliações dos custos no orçamento

Agora, as avaliações dos custos no orçamento incluem dados de instâncias reservadas e de compra. Se os encargos se aplicarem a você, você poderá receber alertas conforme os encargos forem incorporados a suas avaliações. Recomendamos que você entre no [portal do Azure](https://portal.azure.com) para verificar se os limites de orçamento estão configurados corretamente para levar em consideração os novos custos. Seus encargos cobrados no Azure não são alterados. Agora, os orçamentos são avaliados em relação a um conjunto mais completo de seus custos. Se os encargos não se aplicarem a você, o comportamento de seu orçamento permanecerá inalterado.

Se você quiser filtrar os novos custos para que os orçamentos sejam avaliados apenas em relação aos encargos de consumo interno do Azure, adicione os seguintes filtros ao seu orçamento:

- Tipo de distribuidor: Azure
- Tipo de preço: Uso

As avaliações de custo do orçamento baseiam-se no custo real. Elas não incluem amortização. Para obter mais informações sobre as opções de filtragem disponíveis nos orçamentos, confira [Como entender opções de agrupamento e filtragem](group-filter.md).

## <a name="trigger-an-action-group"></a>Disparar um grupo de ações

Quando cria ou edita um orçamento para um escopo de grupo de recursos ou de assinatura, você pode configurá-lo para chamar um grupo de ações. O grupo de ações pode executar várias ações quando o limite de orçamento é atingido. No momento, os grupo de ações só têm suporte para os escopos de grupo de recursos e assinatura. Para obter mais informações sobre os Grupo de ações, confira [Criar e gerenciar grupos de ações no portal do Azure](../../azure-monitor/platform/action-groups.md). Para obter mais informações sobre como usar a automação baseada em orçamento com grupos de ações, confira [Gerenciar custos com os orçamentos do Azure](../manage/cost-management-budget-scenario.md).

Para criar ou atualizar grupos de ações, selecione **Gerenciar grupos de ações** quando estiver criando ou editando um orçamento.

![Exemplo de criação de um orçamento para mostrar Gerenciar grupos de ações](./media/tutorial-acm-create-budgets/manage-action-groups01.png)

Em seguida, selecione **Adicionar grupo de ações** e crie o grupo de ações.

![Imagem da caixa Adicionar grupo de ações](./media/tutorial-acm-create-budgets/manage-action-groups02.png)

Depois que o grupo de ações for criado, feche a caixa para retornar ao seu orçamento.

Configure seu orçamento para usar o grupo de ações quando um limite individual for atingido. Há suporte para até cinco limites diferentes.

![Exemplo mostrando a seleção do grupo de ações para uma condição de alerta](./media/tutorial-acm-create-budgets/manage-action-groups03.png)

O exemplo a seguir mostra os limites de orçamento definidos como 50%, 75% e 100%. Cada um deles é configurado para disparar as ações especificadas dentro do grupo de ações designado.

![Exemplo mostrando condições de alerta configuradas com vários grupos de ação e tipos de ações](./media/tutorial-acm-create-budgets/manage-action-groups04.png)

A integração do orçamento com os grupos de ação só funciona para grupos de ação que têm o esquema de alerta comum desabilitado. Para obter mais informações sobre como desabilitar o esquema, confira [Como habilitar o esquema de alerta comum?](../../azure-monitor/platform/alerts-common-schema.md#how-do-i-enable-the-common-alert-schema)

## <a name="create-and-edit-budgets-with-powershell"></a>Criar e editar orçamentos com o PowerShell

Os clientes do EA podem criar e editar orçamentos programaticamente usando o módulo Azure PowerShell.  Para baixar a versão mais recente do Azure PowerShell, execute o seguinte comando:

```azurepowershell-interactive
install-module -name Az
```

Os comandos de exemplo a seguir criam um orçamento.

```azurepowershell-interactive
#Sign into Azure Powershell with your account

Connect-AzAccount

#Select a subscription to to monitor with a budget

select-AzSubscription -Subscription "Your Subscription"

#Create an action group email receiver and corresponding action group

$email1 = New-AzActionGroupReceiver -EmailAddress test@test.com -Name EmailReceiver1
$ActionGroupId = (Set-AzActionGroup -ResourceGroupName YourResourceGroup -Name TestAG -ShortName TestAG -Receiver $email1).Id

#Create a monthly budget that sends an email and triggers an Action Group to send a second email. Make sure the StartDate for your monthly budget is set to the first day of the current month. Note that Action Groups can also be used to trigger automation such as Azure Functions or Webhooks.

New-AzConsumptionBudget -Amount 100 -Name TestPSBudget -Category Cost -StartDate 2020-02-01 -TimeGrain Monthly -EndDate 2022-12-31 -ContactEmail test@test.com -NotificationKey Key1 -NotificationThreshold 0.8 -NotificationEnabled -ContactGroup $ActionGroupId
```

## <a name="create-a-budget-with-an-azure-resource-manager-template"></a>Criar um orçamento com um modelo do Azure Resource Manager

Você pode Criar um orçamento usando um modelo do Azure Resource Manager. Para usar o modelo, confira [Criar um orçamento com um modelo do Azure Resource Manager](quick-create-budget-template.md).

## <a name="clean-up-resources"></a>Limpar os recursos

Se você criou um orçamento e não precisa mais dele, veja os detalhes do orçamento e exclua-o.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Criar um orçamento no portal do Azure
> * Criar e editar orçamentos com o PowerShell
> * Criar um orçamento com um modelo do Azure Resource Manager

Avance para o próximo tutorial para criar uma exportação recorrente para os dados de gerenciamento de custos.

> [!div class="nextstepaction"]
> [Criar e gerenciar dados exportados](tutorial-export-acm-data.md)