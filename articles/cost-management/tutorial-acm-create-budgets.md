---
title: Tutorial – Criar e gerenciar orçamentos do Azure | Microsoft Docs
description: Este tutorial ajuda a planejar e contabilizar os custos de serviços do Azure que você consome.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/09/2019
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: seodec18
ms.openlocfilehash: 704aefd68f35ca20f72a2a0c46bf11912c139e65
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61024647"
---
# <a name="tutorial-create-and-manage-azure-budgets"></a>Tutorial: criar e gerenciar orçamentos do Azure

Orçamentos no Gerenciamento de Custos ajudam você a planejar e promover responsabilidade organizacional. Com orçamentos, você pode considerar os serviços do Azure que consome ou assina durante um período específico. Eles ajudam você a informar outras pessoas sobre seus gastos para gerenciar proativamente os custos e monitorar como os gastos evoluem ao longo do tempo. Quando os limites de orçamento que você criou são excedidos, apenas notificações são disparadas. Nenhum de seus recursos é afetado e seu consumo não é interrompido. Você pode usar os orçamentos para comparar e controlar como analisar os custos de gastos.

Orçamentos mensais são avaliados em relação aos gastos de cada quatro horas. No entanto, dados e notificações para os recursos consumidos estão disponíveis dentro de oito horas.  

Os orçamentos são redefinidos automaticamente no final de um período (mensal, trimestral ou anual) para o mesmo valor de orçamento quando você seleciona uma data de expiração no futuro. Uma vez que redefinidos com o mesmo valor de orçamento, você precisará criar orçamentos separados quando os valores monetários orçados forem diferentes para períodos futuros.

Os exemplos deste tutorial ajudam você a criar e editar um orçamento para uma assinatura do Azure Enterprise Agreement (EA).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um orçamento no portal do Azure
> * Editar um orçamento

## <a name="prerequisites"></a>Pré-requisitos

Há suporte para orçamentos de uma variedade de tipos de conta do Azure. Para exibir a lista completa dos tipos de conta compatíveis, confira [Entender os dados do Gerenciamento de Custos](understand-cost-mgt-data.md). Para exibir os orçamentos, você precisará, pelo menos, de acesso de leitura em sua conta do Azure.

 Para assinatura do Azure EA, você deve ter acesso de leitura para exibir orçamentos. Para criar e gerenciar orçamentos, você deve ter permissão de colaborador. Você pode criar orçamentos individuais para grupos de recursos e assinaturas de EA. No entanto, não é possível criar os orçamentos para contas de cobrança de EA.

As seguintes permissões do Azure ou escopos, têm suporte por assinatura para os orçamentos por usuário e grupo. Para obter mais informações sobre escopos, consulte [Entender e trabalhar com escopos](understand-work-scopes.md).

- Proprietário – pode criar, modificar ou excluir os orçamentos para uma assinatura.
- Colaborador e contribuidor do Gerenciamento de Custos - pode criar, modificar ou excluir seus próprios orçamentos. Pode modificar o valor do orçamento para orçamentos criados por outras pessoas.
- Leitor e leitor do Gerenciamento de Custos – pode exibir os orçamentos para os quais ele têm permissão.

Para obter mais informações sobre como atribuir permissões aos dados de Gerenciamento de Custos, consulte [Atribuir acesso aos dados de Gerenciamento de Custos](assign-access-acm-data.md).

## <a name="sign-in-to-azure"></a>Entrar no Azure

- Entre no Portal do Azure em https://portal.azure.com.

## <a name="create-a-budget-in-the-azure-portal"></a>Criar um orçamento no portal do Azure

Você pode criar um orçamento de assinatura do Azure para um período mensal, trimestral ou anual. O conteúdo de navegação no portal do Azure determina se você cria um orçamento para uma assinatura ou para um grupo de gerenciamento.

Para criar ou exibir um orçamento, abra o escopo desejado no portal do Azure e selecione **orçamentos** no menu. Por exemplo, navegue até **inscrições**, selecione uma assinatura na lista e, em seguida, selecione **orçamentos** no menu. Use o **escopo** com conteúdo malicioso para alternar para um escopo diferente, como um grupo de gerenciamento em orçamentos. Para obter mais informações sobre escopos, consulte [Entender e trabalhar com escopos](understand-work-scopes.md).

Depois de criar os orçamentos, eles mostram uma exibição simples de seus gastos atual em relação a eles.

Clique em **Adicionar**.

![Orçamentos de Gerenciamento de Custos mostrados no portal do Azure](./media/tutorial-acm-create-budgets/budgets01.png)

Na janela **Criar orçamento**, insira um nome de orçamento e o valor do orçamento. Em seguida, escolha um período de duração mensal, trimestral ou anual. Em seguida, selecione uma data de término. Orçamentos exigem pelo menos um limite de custo (% do orçamento) e um endereço de email correspondente. Opcionalmente, você pode incluir até cinco limites e cinco endereços de email em um único orçamento. Quando um limite de orçamento é atingido, as notificações por email são recebidas normalmente em menos de oito horas. Para obter mais informações sobre as notificações, confira [Alertas de custo de uso](cost-mgt-alerts-monitor-usage-spending.md).

Se você tiver uma assinatura de Pagamento Conforme o Uso, do MSDN ou do Visual Studio, o período de cobrança da fatura talvez não esteja alinhado ao mês do calendário. Para esses tipos de assinaturas e grupos de recursos, você pode criar um orçamento que está alinhado ao período de fatura ou para os meses do calendário. Para criar um orçamento alinhado ao período de nota fiscal, selecione um período de redefinição de mês de cobrança, a cobrança trimestre ou ano de cobrança. Para criar um orçamento alinhado ao mês calendário, selecione um período de redefinição de mensal, trimestral ou anualmente.

Aqui está um exemplo de criação de um orçamento mensal de US$ 4.500. Um alerta por email é gerado quando 90% do orçamento é atingido.

![Informações de exemplo mostradas na caixa Criar orçamento](./media/tutorial-acm-create-budgets/monthly-budget01.png)

Quando você cria um orçamento trimestral, ele funciona da mesma maneira que um orçamento mensal. A diferença é que o valor do orçamento para o trimestre é dividido entre os três meses do trimestre. Como você poderia esperar, um valor do orçamento anual é dividido uniformemente entre todos os 12 meses do ano civil.

Os gastos atuais com relação aos orçamentos são atualizados sempre que o Gerenciamento de Custos recebe dados de cobrança atualizados. Isso costuma ocorrer diariamente.

![Informações de exemplo mostrando gastos atuais em relação ao orçamento](./media/tutorial-acm-create-budgets/budgets-current-spending.png)

Depois de criar um orçamento, ele é mostrado na análise de custo. Exibir seu orçamento em relação à sua tendência de gastos é uma das primeiras etapas quando você começa a [analisar seus custos e gastos](quick-acm-cost-analysis.md).

![Exemplo de orçamento e gastos mostrados na análise de custos](./media/tutorial-acm-create-budgets/cost-analysis.png)

No exemplo anterior, você criou um orçamento para uma assinatura. No entanto, você também pode criar um orçamento para um grupo de recursos. Se você quiser criar um orçamento para um grupo de recursos, navegue até **Gerenciamento de Custos + Cobrança** &gt; **Assinaturas** &gt; selecione uma assinatura > **Grupo de recursos** > selecione um grupo de recursos > **Orçamentos** > e, em seguida escolha **Adicionar** um orçamento.

## <a name="edit-a-budget"></a>Editar um orçamento

Dependendo do nível de acesso que você tem, é possível editar um orçamento para alterar suas propriedades. No exemplo a seguir, algumas das propriedades são somente leitura porque o usuário tem apenas permissão de Contribuinte para a assinatura. No momento, a **Data do término** está desabilitada e não pode ser modificada depois de definida.

![Exemplo de edição de um orçamento para alterar várias propriedades](./media/tutorial-acm-create-budgets/edit-budget.png)

## <a name="trigger-an-action-group"></a>Um grupo de ação de gatilho

Quando você cria ou edita um orçamento para uma assinatura ou o escopo do grupo de recursos, você pode configurá-lo para chamar um grupo de ações. O grupo de ação pode executar uma variedade de ações diferentes quando seu orçamento de limite for atingido. Para obter mais informações sobre grupos de ação, consulte [criar e gerenciar grupos de ações no portal do Azure](../azure-monitor/platform/action-groups.md). Para obter mais informações sobre como usar a automação com base no orçamento com grupos de ação, consulte [gerenciar os custos com orçamentos Azure](../billing/billing-cost-management-budget-scenario.md).

Para criar ou atualizar grupos de ações, clique em **gerenciar grupos de ação** enquanto você está criando ou editando um orçamento.

![Exemplo de criação de um orçamento para mostrar a gerenciar grupos de ação](./media/tutorial-acm-create-budgets/manage-action-groups01.png)

Em seguida, clique em **adicionar grupo de ações** e criar o grupo de ação.


![Imagem da caixa de grupo de ação Add](./media/tutorial-acm-create-budgets/manage-action-groups02.png)

Após a ação de grupo é criado, feche a caixa para retornar ao seu orçamento.

Configure seu orçamento para usar o grupo de ações quando um limite individual é atingido. Até cinco limites diferentes têm suporte.

![Exemplo mostrando a seleção de grupo de ação para uma condição de alerta](./media/tutorial-acm-create-budgets/manage-action-groups03.png)

O exemplo a seguir mostra os limites de orçamento definidos como 50%, 75% e 100%. Cada um é configurada para disparar as ações especificadas dentro do grupo de ação designado.

![Exemplo que mostra as condições de alerta configuradas com vários tipos de ações e grupos de ações](./media/tutorial-acm-create-budgets/manage-action-groups04.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Criar um orçamento no portal do Azure
> * Editar um orçamento

Avance para o próximo tutorial para criar uma exportação recorrente para os dados de gerenciamento de custos.

> [!div class="nextstepaction"]
> [Criar e gerenciar dados exportados](tutorial-export-acm-data.md)
