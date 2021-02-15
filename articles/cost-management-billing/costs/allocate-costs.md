---
title: Alocar os custos do Azure
description: Este artigo explica como criar regras de alocação de custos para distribuir os custos de assinaturas, grupos de recursos ou marcas para outras pessoas.
author: bandersmsft
ms.author: banders
ms.date: 08/11/2020
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: benshy
ms.openlocfilehash: 51baa26cf78846bd0a719b8b86056e2ea8176155
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131081"
---
# <a name="create-and-manage-azure-cost-allocation-rules-preview"></a>Criar e gerenciar regras de alocação de custos do Azure (versão prévia)

No geral, as grandes empresas têm recursos ou serviços do Azure que são gerenciados de maneira centralizada, mas são utilizados por diferentes unidades de negócios ou departamentos internos. Normalmente, a equipe de gerenciamento central deseja realocar o custo dos serviços compartilhados para os departamentos internos ou as unidades de negócios organizacionais que estão usando os serviços ativamente. Este artigo ajuda você a entender e usar a alocação de custos no Gerenciamento de Custos do Azure.

Com a alocação de custos, você pode transferir ou distribuir os custos de serviços compartilhados de assinaturas, grupos de recursos ou marcas para outras assinaturas, grupos de recursos ou marcas na sua organização. A alocação de custos desloca os custos dos serviços compartilhados para outra assinatura, grupos de recursos ou marcas pertencentes às unidades de negócios ou aos departamentos internos consumidores. Em outras palavras, a alocação de custos ajuda a gerenciar e mostrar a _responsabilidade de custo_ de um lugar para outro.

A alocação de custos não afeta sua fatura de cobrança. As responsabilidades de cobrança não são alteradas. A principal finalidade da alocação de custos é ajudar você a estornar os custos para outras pessoas. Todos os processos de estorno acontecem na sua organização fora do Azure. A alocação de custos ajuda você a estornar os custos, mostrando-os conforme eles são transferidos ou distribuídos.

Os custos alocados são mostrados na análise de custo. Eles são mostrados como itens adicionais associados às assinaturas, aos grupos de recursos ou às marcas de destino que você especifica ao criar uma regra de alocação de custos.

> [!NOTE]
> Atualmente, o recurso de alocação de custos do Gerenciamento de Custos do Azure está em versão prévia pública. Alguns recursos do Gerenciamento de Custos podem não ter suporte ou podem ter funcionalidades limitadas.

## <a name="prerequisites"></a>Pré-requisitos

- Atualmente, a alocação de custos só dá suporte aos clientes com um [Contrato de Cliente da Microsoft](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) ou um [EA (Contrato Enterprise)](https://azure.microsoft.com/pricing/enterprise-agreement/).
- Para criar ou gerenciar uma regra de alocação de custos, você precisará usar uma conta de Administrador Corporativo para [Contratos Enterprise](../manage/understand-ea-roles.md). Ou então, você precisará ser um proprietário da [Conta de cobrança](../manage/understand-mca-roles.md) para Contratos de Cliente da Microsoft.

## <a name="create-a-cost-allocation-rule"></a>Criar uma regra de alocação de custos

1. Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com/).
2. Acesse **Gerenciamento de Custos + Cobrança** > **Gerenciamento de Custos**.
3. Em **Configurações** > **Configuração**, selecione **Alocação de custos (Versão Prévia)** .
4. Verifique se a conta de cobrança ou o registro do EA correto está selecionado.
5. Selecione **+Adicionar**.
6. Insira um texto descritivo para o nome da regra de alocação de custos.

:::image type="content" source="./media/allocate-costs/rule-name.png" alt-text="Exemplo mostrando a criação de um nome de regra" lightbox="./media/allocate-costs/rule-name.png" :::

A data de início da avaliação da regra é usada para gerar os percentuais de alocação de custos preenchidos previamente.

1. Selecione **Adicionar fontes** e selecione as assinaturas, os grupos de recursos ou as marcas para escolher os custos a serem distribuídos.
2. Selecione **Adicionar destinos** e selecione as assinaturas, os grupos de recursos ou as marcas que receberão os custos alocados.
3. Caso precise criar regras adicionais de alocação de custos, repita esse processo.

## <a name="configure-the-allocation-percentage"></a>Configurar o percentual de alocação

Configure o percentual de alocação para definir como os custos são divididos proporcionalmente entre os destinos especificados. Você pode definir manualmente percentuais de número inteiro para criar uma regra de alocação. Ou então, dividir os custos proporcionalmente com base no uso atual da computação, do armazenamento ou da rede entre os destinos especificados.

Ao distribuir os custos por custo de computação, custo de armazenamento ou custo de rede, o percentual proporcional é obtido pela avaliação dos custos do destino selecionado. Os custos são associados ao tipo de recurso para o mês de cobrança atual.

Ao distribuir os custos proporcionais ao custo total, o percentual proporcional é alocado pela soma ou pelo custo total dos destinos selecionados para o mês de cobrança atual.

:::image type="content" source="./media/allocate-costs/cost-distribution.png" alt-text="Exemplo mostrando a criação de um nome de regra" lightbox="./media/allocate-costs/cost-distribution.png" :::

Após a definição, os percentuais preenchidos previamente definidos são fixos. Eles são usados para todas as alocações em andamento. Os percentuais são alterados somente quando a regra é atualizada manualmente.

1. Selecione uma das opções a seguir na lista **Preencher previamente o percentual até**.
    - **Distribuir uniformemente**: cada um dos destinos recebe uma proporção de percentual uniforme do custo total.
    - **Custo total**: cria uma taxa proporcional aos destinos com base no custo total. A taxa é usada para distribuir os custos das fontes selecionadas.
    - **Custo de computação**: cria uma taxa proporcional aos destinos com base no custo de computação do Azure (tipos de recursos do namespace [Microsoft.Compute](/azure/templates/microsoft.compute/allversions)). A taxa é usada para distribuir os custos das fontes selecionadas.
    - **Custo de armazenamento**: cria uma taxa proporcional aos destinos com base no custo do armazenamento do Azure (tipos de recursos do namespace [Microsoft.Storage](/azure/templates/microsoft.storage/allversions)). A taxa é usada para distribuir os custos das fontes selecionadas.
    - **Custo de rede**: cria uma taxa proporcional aos destinos com base no custo de rede do Azure (tipos de recursos do namespace [Microsoft.Network](/azure/templates/microsoft.network/allversions)). A taxa é usada para distribuir os custos das fontes selecionadas.
    - **Personalizado**: permite que um percentual de número inteiro seja especificado manualmente. O total especificado precisa ser igual a 100%.
1. Quando a regra for configurada, selecione **Criar**.

A regra de alocação inicia o processamento. Quando a regra está ativa, todos os custos da fonte selecionada são alocados para os destinos especificados.

> [!NOTE] 
> O processamento da nova regra pode levar até duas horas antes de ser concluído e a regra ficar ativa.

## <a name="verify-the-cost-allocation-rule"></a>Confirmar a regra de alocação de custos

Quando a regra de alocação de custos está ativa, os custos das fontes selecionadas são distribuídos para os destinos de alocação especificados. Use as informações a seguir para confirmar se o custo está corretamente alocado para os destinos.

### <a name="view-cost-allocation-for-a-subscription"></a>Exibir a alocação de custos para uma assinatura

O impacto da regra de alocação é exibido na análise de custo. No portal do Azure, vá para [Assinaturas](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Selecione uma assinatura na lista que seja o destino de uma regra ativa de alocação de custos. Em seguida, selecione **Análise de custo** no menu. Em Análise de custo, selecione **Agrupar por** e escolha **Alocação de custos**. A exibição resultante mostrará um detalhamento rápido de custos gerado pela assinatura. Os custos alocados à assinatura também são mostrados, como a imagem a seguir.

:::image type="content" source="./media/allocate-costs/cost-breakdown.png" alt-text="Exemplo mostrando a criação de um nome de regra" lightbox="./media/allocate-costs/cost-breakdown.png" :::

### <a name="view-cost-allocation-for-a-resource-group"></a>Exibir a alocação de custos para um grupo de recursos

Use um processo semelhante para ver o impacto de uma regra de alocação de custos em um grupo de recursos. No portal do Azure, acesse [Grupo de recursos](https://portal.azure.com/#blade/HubsExtension/BrowseResourceGroups). Selecione um grupo de recursos na lista que seja o destino de uma regra ativa de alocação de custos. Em seguida, selecione **Análise de custo** no menu. Em Análise de custo, selecione **Agrupar por** e escolha **Alocação de custos**. A exibição mostrará um detalhamento rápido de custos gerado pelo grupo de recursos. O custo alocado ao grupo de recursos também será mostrado.

### <a name="view-cost-allocation-for-tags"></a>Exibir a alocação de custos para marcas

No portal do Azure, acesse **Gerenciamento de Custos + Cobrança** > **Gerenciamento de Custos** > **Análise de custo**. Em Análise de custo, selecione **Adicionar filtro**. Selecione **Marca**, escolha a chave de marca e marque os valores que têm um custo alocado a eles.

:::image type="content" source="./media/allocate-costs/tagged-costs.png" alt-text="Exemplo mostrando a criação de um nome de regra" lightbox="./media/allocate-costs/tagged-costs.png" :::

## <a name="edit-an-existing-cost-allocation-rule"></a>Editar uma regra existente de alocação de custos

Edite uma regra de alocação de custos para alterar a origem ou o destino ou se desejar atualizar o percentual preenchido previamente das opções de computação, armazenamento ou rede. Edite as regras da mesma forma que as cria. A modificação das regras existentes pode levar até duas horas para ser processada novamente.

## <a name="frequently-asked-questions-faq"></a>Perguntas frequentes (FAQ)

As seções a seguir contêm perguntas comuns que as pessoas fazem sobre a alocação de custos.

### <a name="what-are-the-current-limitations-with-cost-allocation-in-public-preview"></a>Quais são as limitações atuais da alocação de custos na versão prévia pública?
<a name="limitations"></a>

Atualmente, há suporte para a alocação de custos no Gerenciamento de Custos nas exibições de previsão, orçamentos e Análise de custo. Os custos alocados também são mostrados na lista de assinaturas e na página Visão geral das assinaturas.

Atualmente, não há suporte para os seguintes itens na versão prévia pública de alocação de custos:

- [Exportações](tutorial-export-acm-data.md) agendadas
- Dados expostos pela API de [Detalhes de Uso](/rest/api/consumption/usagedetails/list)
- Área de assinaturas para cobrança
- [Aplicativo do Power BI de Gerenciamento de Custos](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp)
- [Conector do Power BI Desktop](/power-bi/connect-data/desktop-connect-azure-cost-management)

### <a name="are-costs-factored-into-budgets-and-forecast-views"></a>Os custos são incluídos em exibições de previsão e orçamentos?
<a name="budgets-forecast"></a>

Sim. Os custos alocados são incluídos e são compatíveis com orçamentos e previsões. As exibições de previsão e orçamento mostram os custos alocados a elas, conforme configurado pelas regras de alocação de custos.

### <a name="if-a-cost-allocation-rule-is-deleted-what-happens"></a>Se uma regra de alocação de custos for excluída, o que acontecerá?
<a name="delete-rule"></a>

Quando uma regra de alocação de custos é excluída, todos os custos do mês de cobrança em aberto e atual que estão sendo alocados para os destinos são removidos. Se a regra de alocação de custos existir por vários meses, o histórico dos meses anteriores dos dados de alocação permanecerá como definido originalmente pela regra de alocação.

### <a name="why-is-an-enrollment-admin-or-a-billing-account-admin-needed-to-create-cost-allocation-rules"></a>Por que um administrador de registro ou um administrador de conta de cobrança precisava criar regras de alocação de custos?
<a name="why-admin"></a>

As regras de alocação de custos são criadas no escopo do registro (Contrato Enterprise) ou no escopo da conta de cobrança (Contrato de Cliente da Microsoft). As permissões para fazer alterações nesses escopos exigem privilégios de administrador de cobrança.

### <a name="why-are-sources-and-targets-limited-to-25-per-rule"></a>Por que as origens e os destinos são limitados a 25 por regra?
<a name="source-target-rule-limit"></a>

Essa é uma limitação da versão prévia para garantir um bom desempenho e escalabilidade de alocação de custos. Os limites provavelmente aumentarão ou serão removidos quando a alocação de custos passar para GA (disponibilidade geral).

### <a name="what-can-happen-if-cost-allocation-rules-sourcestargets-overlap"></a>O que pode acontecer se as regras de alocação de custos (origens/destinos) se sobrepõem?
<a name="rule-overlap"></a>

As regras que têm origens ou destinos sobrepostos não são recomendadas. As regras de alocação de custos são aplicadas em ordem pela data de criação e, portanto, se qualquer regra de alocação de custos ficar sobreposta, a regra de alocação com a data de criação mais antiga terá precedência.

## <a name="next-steps"></a>Próximas etapas

- Criar ou atualizar regras de alocação usando a [API REST de Alocação de custos](/rest/api/cost-management/costallocationrules)
- Saiba mais sobre como [otimizar seu investimento na nuvem com o Gerenciamento de Custos do Azure](cost-mgt-best-practices.md)