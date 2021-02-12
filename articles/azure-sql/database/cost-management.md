---
title: Planejar e gerenciar custos para o banco de dados SQL do Azure
description: Saiba como planejar e gerenciar os custos do banco de dados SQL do Azure usando a análise de custo no portal do Azure.
author: stevestein
ms.author: sstein
ms.custom: subject-cost-optimization
ms.service: sql-database
ms.topic: how-to
ms.date: 01/15/2021
ms.openlocfilehash: 56cf30d89460df8ac50d258bd8b29cf4e7236690
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98734623"
---
# <a name="plan-and-manage-costs-for-azure-sql-database"></a>Planejar e gerenciar custos para o banco de dados SQL do Azure

Este artigo descreve como planejar e gerenciar os custos do banco de dados SQL do Azure. Primeiro, você usa a calculadora de preços do Azure para adicionar recursos do Azure e examinar os custos estimados. Depois de começar a usar os recursos do banco de dados SQL do Azure, use os recursos de gerenciamento de custos para definir orçamentos e monitorar custos. Você também pode examinar os custos previstos e identificar as tendências de gastos para identificar as áreas em que talvez queira agir. Os custos do banco de dados SQL do Azure são apenas uma parte dos custos mensais em sua fatura do Azure. Embora este artigo explique como planejar e gerenciar os custos do banco de dados SQL do Azure, você será cobrado por todos os serviços e recursos do Azure usados na sua assinatura do Azure, incluindo qualquer serviço de terceiros.


## <a name="prerequisites"></a>Pré-requisitos

A análise de custo dá suporte à maioria dos tipos de conta do Azure, mas não a todos. Para exibir a lista completa dos tipos de contas compatíveis, confira [Entender os dados do Gerenciamento de Custos](../../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Para exibir dados de custo, você precisa de, pelo menos, acesso de leitura para uma conta do Azure. 

Para obter informações sobre como atribuir o acesso aos dados do Gerenciamento de Custos do Azure, confira [Atribuir acesso aos dados](../../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).


## <a name="sql-database-initial-cost-considerations"></a>Considerações de custo inicial do banco de dados SQL

Ao trabalhar com o banco de dados SQL do Azure, há vários recursos de economia de custos a serem considerados:


### <a name="vcore-or-dtu-purchasing-models"></a>modelos de compra vCore ou DTU 

O banco de dados SQL do Azure dá suporte a dois modelos de compra: vCore e DTU. A maneira como você se cobra varia de acordo com os modelos de compra, portanto, é importante entender o modelo que funciona melhor para sua carga de trabalho ao planejar e considerar os custos. Para obter informações sobre modelos de compra vCore e DTU, consulte [escolher entre os modelos de compra VCORE e DTU](purchasing-models.md).


### <a name="provisioned-or-serverless"></a>Provisionado ou sem servidor

No modelo de compra vCore, o banco de dados SQL do Azure também dá suporte a dois tipos de camadas de computação: taxa de transferência provisionada e sem servidor. A maneira como você é cobrado por cada camada de computação varia, portanto, é importante entender o que funciona melhor para sua carga de trabalho ao planejar e considerar os custos. Para obter detalhes, consulte [visão geral do modelo de vCore – camadas de computação](service-tiers-vcore.md#compute-tiers).

Na camada de computação provisionada do modelo de compra baseado em vCore, você pode trocar suas licenças existentes por tarifas com desconto. Para obter detalhes, consulte [benefício híbrido do Azure (AHB)](../azure-hybrid-benefit.md).

### <a name="elastic-pools"></a>Pools elásticos

Para ambientes com vários bancos de dados que têm demandas de uso variáveis e imprevisíveis, os pools elásticos podem fornecer economias de custo em comparação com o provisionamento da mesma quantidade de bancos de dados individuais. Para obter detalhes, consulte [pools elásticos](elastic-pool-overview.md).

## <a name="estimate-azure-sql-database-costs"></a>Estimar os custos do banco de dados SQL do Azure

Use a [calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/) para estimar os custos para diferentes configurações do banco de dados SQL do Azure. As informações e os preços na imagem a seguir são apenas para fins de exemplo:

:::image type="content" source="media/cost-management/pricing-calc.png" alt-text="Exemplo de calculadora de preços do banco de dados SQL do Azure":::

Você também pode estimar como as diferentes opções de política de retenção afetam o custo. As informações e os preços na imagem a seguir são apenas para fins de exemplo:

:::image type="content" source="media/cost-management/backup-storage.png" alt-text="Exemplo de calculadora de preços do banco de dados SQL do Azure para armazenamento":::


## <a name="understand-the-full-billing-model-for-azure-sql-database"></a>Entender o modelo de cobrança completo do banco de dados SQL do Azure

O banco de dados SQL do Azure é executado na infraestrutura do Azure que acumula custos junto com o banco de dados SQL do Azure quando você implanta o novo recurso. É importante entender que a infraestrutura adicional pode acumular o custo. Você precisa gerenciar esse custo ao fazer alterações nos recursos implantados. 


O banco de dados SQL do Azure (com exceção de sem servidor) é cobrado em uma taxa previsível e por hora. Se o banco de dados SQL estiver ativo por menos de uma hora, você será cobrado por cada hora em que o banco de dados existe usando a camada de serviço mais alta selecionada, o armazenamento provisionado e a e/s que foram aplicados durante essa hora, independentemente do uso ou se o banco de dados esteve ativo por menos de uma hora.


### <a name="using-monetary-credit-with-azure-sql-database"></a>Usando crédito monetário com o banco de dados SQL do Azure

Você pode pagar por cobranças do banco de dados SQL do Azure com o crédito antecipado do Azure (anteriormente chamado de compromisso monetário). No entanto, você não pode usar o crédito de pagamento antecipado do Azure para pagar por cobranças de produtos e serviços de terceiros, incluindo aqueles do Azure Marketplace.

## <a name="review-estimated-costs-in-the-azure-portal"></a>Examinar os custos estimados no portal do Azure

Ao percorrer o processo de criação de um banco de dados SQL do Azure, você pode ver os custos estimados durante a configuração da camada de computação. 

Para acessar essa tela, selecione **configurar banco de dados** na guia **noções básicas** da página **criar banco de dados SQL** . As informações e os preços na imagem a seguir são apenas para fins de exemplo:

  :::image type="content" source="media/cost-management/cost-estimate.png" alt-text="Exemplo mostrando estimativa de custo no portal do Azure":::



Se sua assinatura do Azure tiver um limite de gastos, o Azure impedirá que você gaste em seu valor de crédito. Conforme você cria e usa os recursos do Azure, seus créditos são usados. Quando você atingir seu limite de crédito, os recursos implantados serão desabilitados para o restante desse período de cobrança. Você não pode alterar seu limite de crédito, mas pode removê-lo. Para obter mais informações sobre limites de gastos, consulte [limite de gastos do Azure](../../cost-management-billing/manage/spending-limit.md).

## <a name="monitor-costs"></a>Monitorar custos

Ao começar a usar o banco de dados SQL do Azure, você pode ver os custos estimados no Portal. Use as seguintes etapas para examinar a estimativa de custo:

1. Entre no portal do Azure e navegue até o grupo de recursos do banco de dados SQL do Azure. Você pode localizar o grupo de recursos navegando até o banco de dados e selecionando **grupo de recursos** na seção **visão geral** .
1. No menu, selecione **análise de custo**.
1. Exiba os **custos acumulados** e defina o gráfico na parte inferior como **nome do serviço**. Este gráfico mostra uma estimativa dos custos atuais do banco de dados SQL. Para restringir os custos da página inteira para o banco de dados SQL do Azure, selecione **Adicionar filtro** e, em seguida, selecione **banco de dados SQL do Azure**. As informações e os preços na imagem a seguir são apenas para fins de exemplo:

   :::image type="content" source="media/cost-management/cost-analysis.png" alt-text="Exemplo mostrando custos acumulados no portal do Azure":::

A partir daqui, você pode explorar os custos por conta própria. Para obter mais informações sobre as diferentes configurações de análise de custo, consulte [iniciar análise de custos](../../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="create-budgets"></a>Criar orçamentos

<!-- Note to Azure service writer: Modify the following as needed for your service. -->

Você pode criar [orçamentos](../../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) para gerenciar custos e criar [alertas](../../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) que notifiquem automaticamente as partes interessadas de despesas de gastos e que estejam gastando riscos. Os alertas são baseados nos gastos comparados com os limites de orçamento e de custo. Orçamentos e alertas são criados para assinaturas e grupos de recursos do Azure, para que eles sejam úteis como parte de uma estratégia de monitoramento de custo geral. 

Os orçamentos podem ser criados com filtros para recursos ou serviços específicos no Azure se você quiser mais granularidade presente no monitoramento. Os filtros ajudam a garantir que você não crie acidentalmente novos recursos que custam dinheiro adicional. Para obter mais informações sobre as opções de filtro ao criar um orçamento, consulte [Opções de grupo e filtro](../../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Exportar dados de custo

Você também pode [exportar seus dados de custo](../../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) para uma conta de armazenamento. Isso é útil quando você precisa ou de outras pessoas para realizar análise de dados adicional para custos. Por exemplo, uma equipe de Finanças pode analisar os dados usando o Excel ou Power BI. Você pode exportar seus custos em uma agenda diária, semanal ou mensal e definir um intervalo de datas personalizado. A exportação de dados de custo é a maneira recomendada para recuperar conjuntos de dado de custo.


## <a name="other-ways-to-manage-and-reduce-costs-for-azure-sql-database"></a>Outras maneiras de gerenciar e reduzir custos para o banco de dados SQL do Azure

O banco de dados SQL do Azure também permite dimensionar os recursos para cima ou para baixo para controlar os custos com base nas necessidades do seu aplicativo. Para obter detalhes, consulte [dimensionar dinamicamente os recursos do banco de dados](scale-resources.md).

Economize dinheiro confirmando uma reserva de recursos de computação por um ou três anos. Para obter detalhes, consulte [economizar custos para recursos com capacidade reservada](reserved-capacity-overview.md).


## <a name="next-steps"></a>Próximas etapas

- Saiba [como otimizar seu investimento em nuvem com o gerenciamento de custos do Azure](../../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Saiba mais sobre como gerenciar custos com [análise de custo](../../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Saiba mais sobre como [evitar custos inesperados](../../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Faça o curso de aprendizado guiado de [Gerenciamento de custos](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) .