---
title: Início Rápido – Criar um orçamento com um modelo do Azure Resource Manager
description: Início Rápido mostrando como criar um orçamento com um modelo do Azure Resource Manager.
author: bandersmsft
ms.author: banders
tags: azure-resource-manager
ms.service: cost-management-billing
ms.subservice: cost-management
ms.topic: quickstart
ms.date: 07/28/2020
ms.custom: subject-armqs, devx-track-azurecli
ms.openlocfilehash: 7d93bd757a39247302a6bc09009a1a814425c32f
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92745376"
---
# <a name="quickstart-create-a-budget-with-an-arm-template"></a>Início Rápido: Criar um orçamento com um modelo do ARM

Orçamentos no Gerenciamento de Custos ajudam você a planejar e promover responsabilidade organizacional. Com orçamentos, você pode considerar os serviços do Azure que consome ou assina durante um período específico. Eles ajudam você a informar outras pessoas sobre seus gastos para gerenciar proativamente os custos e monitorar como os gastos evoluem ao longo do tempo. Quando os limites de orçamento que você criou são excedidos, são disparadas notificações. Nenhum de seus recursos é afetado e seu consumo não é interrompido. Você pode usar os orçamentos para comparar e controlar como analisar os custos de gastos. Este início rápido mostra como criar um orçamento usando um modelo do ARM (Azure Resource Manager).

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Se seu ambiente atender aos pré-requisitos e você estiver familiarizado com o uso de modelos ARM, selecione o botão **Implantar no Azure** . O modelo será aberto no portal do Azure.

[![Implantar no Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fcreate-budget%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Se você tiver uma nova assinatura, não poderá criar um orçamento imediatamente nem usar outros recursos do Gerenciamento de Custos. Poderá levar até 48 horas para você poder usar todos os recursos do Gerenciamento de Custos.

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

Para assinatura do Azure EA, você deve ter acesso de leitura para exibir orçamentos. Para criar e gerenciar orçamentos, você deve ter permissão de colaborador.

Há suporte para as permissões ou escopos a seguir do Azure por assinatura para orçamentos por usuário e grupo. Para obter mais informações sobre escopos, consulte [Entender e trabalhar com escopos](understand-work-scopes.md).

- Proprietário – pode criar, modificar ou excluir os orçamentos para uma assinatura.
- Colaborador e contribuidor do Gerenciamento de Custos - pode criar, modificar ou excluir seus próprios orçamentos. Pode modificar o valor do orçamento para orçamentos criados por outras pessoas.
- Leitor e leitor do Gerenciamento de Custos – pode exibir os orçamentos para os quais ele têm permissão.

Para obter mais informações sobre como atribuir permissões aos dados de Gerenciamento de Custos, consulte [Atribuir acesso aos dados de Gerenciamento de Custos](assign-access-acm-data.md).

## <a name="review-the-template"></a>Examinar o modelo

O modelo usado neste início rápido é proveniente dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/create-budget).

:::code language="json" source="~/quickstart-templates/create-budget/azuredeploy.json" :::

Um recurso do Azure é definido no modelo:

* [Microsoft.Consumption/budgets](/azure/templates/microsoft.consumption/budgets): criar um orçamento do Azure.

## <a name="deploy-the-template"></a>Implantar o modelo

1. Selecione a imagem a seguir para entrar no Azure e abrir um modelo. O modelo cria um orçamento.

   [![Implantar no Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fcreate-budget%2Fazuredeploy.json)

2. Selecione ou insira os seguintes valores.

   :::image type="content" source="./media/quick-create-budget-template/create-budget-using-template-portal.png" alt-text="Modelo do Resource Manager, Criar orçamento, portal de implantação]" lightbox="./media/quick-create-budget-template/create-budget-using-template-portal.png" :::
   
    * **Assinatura** : selecione uma assinatura do Azure.
    * **Grupo de recursos** : se exigido, selecione um grupo de recursos existente ou **Criar** .
    * **Região** : selecione uma região do Azure. Por exemplo, **Centro dos EUA** .
    * **Nome do Orçamento** : insira um nome para o orçamento. Ele deve ser exclusivo em um grupo de recursos. Somente caracteres alfanuméricos, sublinhados e hifens são permitidos.
    * **Quantidade** : insira a quantidade total do custo a ser acompanhada com o orçamento.
    * **Intervalo de agregação** : insira o tempo coberto por um orçamento. Os valores permitidos são mensais, trimestrais ou anuais. O orçamento é redefinido ao final do intervalo de agregação.
    * **Data de Início** : insira a data de início com o primeiro dia do mês no formato AAAA-MM-DD. Uma data de início futura não deve ser a mais de três meses do dia de hoje. Você pode especificar uma data de início anterior com o período de Intervalo de Agregação.
    * **Data de Término** : insira a data de término do orçamento no formato AAAA-MM-DD. 
    * **Primeiro Limite** : insira um valor de limite para a primeira notificação. Uma notificação é enviada quando o custo excede o limite. É sempre percentual e deve estar entre 0 e 1000.
    * **Segundo Limite** : insira um valor de limite para a segunda notificação. Uma notificação é enviada quando o custo excede o limite. É sempre percentual e deve estar entre 0 e 1000.
    * **Funções de Contato** : insira a lista de funções de contato para as quais enviar a notificação de orçamento quando o limite for excedido. Os valores padrão são Proprietário, Colaborador e Leitor. O formato esperado é `["Owner","Contributor","Reader"]`.
    * **Emails de Contato** : insira uma lista de endereços de email para os quais enviar a notificação de orçamento quando o limite for excedido. O formato esperado é `["user1@domain.com","user2@domain.com"]`.
    * **Grupos de Contato** : insira uma lista de IDs de recurso do grupo de ações, como URIs de recurso completo, para os quais enviar a notificação de orçamento quando o limite for excedido. Ela aceita uma matriz de cadeias de caracteres. O formato esperado é `["action group resource ID1","action group resource ID2"]`. Se não desejar usar grupos de ação, insira `[]`.
    * **Valores de Filtro do Grupo de Recursos** : insira uma lista de nomes de grupos de recursos a serem filtrados. O formato esperado é `["Resource Group Name1","Resource Group Name2"]`. Se não desejar aplicar um filtro, insira `[]`. 
    * **Valores de Filtro de Categoria de Medidor** : insira uma lista de categorias de medidor de serviço do Azure. O formato esperado é `["Meter Category1","Meter Category2"]`. Se não desejar aplicar um filtro, insira `[]`.
   
3. Dependendo do tipo de assinatura do Azure, execute uma das seguintes ações:
   - Selecione **Examinar + criar** .
   - Examine os termos e condições e selecione **Eu concordo com os termos e condições declarados acima** e selecione **Comprar** .

4. Se você selecionar **Examinar + criar** , o seu modelo será validado. Selecione **Criar** .  

   ![Modelo do Resource Manager, orçamento, notificação de implantação do portal](./media/quick-create-budget-template/resource-manager-template-portal-deployment-notification.png)

O portal do Azure é usado para implantar o modelo. Além do portal do Azure, você também pode usar o Azure PowerShell, a CLI do Azure e a API REST. Para saber mais sobre outros métodos de implantação, confira [Implantar modelos](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="validate-the-deployment"></a>Validar a implantação

Você pode usar o portal do Azure para verificar se o orçamento foi criado navegando até **Cobrança + Gerenciamento de Custos** > selecione um escopo > **Orçamentos** . Ou use os scripts da CLI do Azure ou do Azure PowerShell a seguir para ver o orçamento.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
az consumption budget list
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
Get-AzConsumptionBudget
```

---

## <a name="clean-up-resources"></a>Limpar os recursos

Quando você não precisar mais de um orçamento, exclua-o usando um dos seguintes métodos:

### <a name="azure-portal"></a>Portal do Azure

Navegue até **Gerenciamento de Custos + Cobrança** > selecione um escopo do orçamento > **Orçamentos** > selecione um orçamento >, em seguida, selecione **Excluir orçamento** .

### <a name="command-line"></a>Linha de comando

Você pode remover o orçamento usando a CLI do Azure ou o Azure PowerShell.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the budget name:" &&
read budgetName &&
az consumption budget delete --budget-name $budgetName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$budgetName = Read-Host -Prompt "Enter the budget name"
Remove-AzConsumptionBudget -Name $budgetName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um orçamento do Azure para a implantação. Para saber mais sobre a Cobrança e o Gerenciamento de Custos do Azure e o Azure Resource Manager, continue lendo os artigos abaixo.

- Leia a visão geral de [Cobrança e Gerenciamento de Custos](../cost-management-billing-overview.md)
- [Crie orçamentos](tutorial-acm-create-budgets.md) no portal do Azure
- Saiba mais sobre o [Azure Resource Manager](../../azure-resource-manager/management/overview.md)
