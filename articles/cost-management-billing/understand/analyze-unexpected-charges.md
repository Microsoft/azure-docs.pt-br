---
title: Analisar encargos do Azure inesperados
description: Saiba como analisar encargos inesperados para sua assinatura do Azure.
author: bandersmsft
ms.reviewer: adwise
ms.service: cost-management-billing
ms.subservice: cost-management
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: banders
ms.custom: contperf-fy21q1
ms.openlocfilehash: 6d5198f47b6089730ff345eb12ab522fc5ce58a1
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100575378"
---
# <a name="analyze-unexpected-charges"></a>Analisar encargos inesperados

A infraestrutura de recursos de nuvem que você criou para sua organização é provavelmente complexa. Muitos tipos de recursos do Azure podem ter diferentes tipos de encargos. Os recursos do Azure podem ser da propriedade de diferentes equipes em sua organização e ter tipos diferentes de modelo de cobrança que se aplicam a vários recursos. Para obter uma melhor compreensão dos encargos, comece sua análise usando uma ou mais estratégias nas seções a seguir.

## <a name="review-invoice-for-resource-responsible-for-charge"></a>Examinar a fatura para o recurso responsável pelo encargo

A maneira como você compra os serviços do Azure ajuda a determinar a metodologia e as ferramentas disponíveis ao identificar o recurso associado a um encargo. Para determinar qual metodologia se aplica a você, primeiro [determine o tipo de oferta do Azure](../costs/understand-cost-mgt-data.md#determine-your-offer-type). Em seguida, identifique sua categoria de cliente na lista de [ofertas do Azure com suporte](../costs/understand-cost-mgt-data.md#supported-microsoft-azure-offers).

Os artigos a seguir fornecem etapas detalhadas que explicam como você deve examinar sua fatura com base no seu tipo de cliente. Em cada artigo, há instruções sobre como baixar um arquivo CSV que contém detalhes de uso e de custo para um determinado período de cobrança.

- [Processo de análise de fatura de pagamento conforme o uso](review-individual-bill.md#charges)
- [Processo de análise de fatura do Contrato Enterprise](review-enterprise-agreement-bill.md)
- [Processo de análise do Contrato de Cliente da Microsoft](review-customer-agreement-bill.md#analyze-your-azure-usage-charges)
- [Processo de análise do Contrato de Parceiro da Microsoft](review-partner-agreement-bill.md#analyze-your-azure-usage-charges)

Sua fatura do Azure agrega encargos do mês por _medidor_. Medidores são usados para acompanhar o uso de um recurso ao longo do tempo e para calcular sua fatura. Quando você cria um único recurso do Azure, como uma máquina virtual, uma ou mais instâncias de medidor são criadas para o recurso.

Filtre o arquivo CSV de uso com base no _MeterName_, conforme mostrado na fatura que você deseja analisar, para ver todos os itens de linha aplicáveis ao medidor. A _InstanceID_ do item de linha corresponde ao recurso real do Azure que gerou o encargo.

Quando você tiver identificado o recurso em questão, poderá usar a Análise de custo no Gerenciamento de Custos do Azure para analisar ainda mais os custos relacionados ao recurso. Para saber mais sobre como usar a análise de custo, confira [Começar a analisar os custos](../costs/quick-acm-cost-analysis.md).

## <a name="review-invoiced-charges-in-cost-analysis"></a>Examinar encargos faturados na Análise de custo

Para ver os detalhes de sua fatura no portal do Azure, navegue até a Análise de custo do escopo associado à fatura que você está analisado. Selecione a exibição **Detalhes da fatura**. Os detalhes da fatura mostram os encargos como foi visto na fatura.

[![Exemplo mostrando a seleção de detalhes da fatura](./media/analyze-unexpected-charges/invoice-details.png)](./media/analyze-unexpected-charges/invoice-details.png#lightbox)

Vendo os detalhes da fatura, é possível identificar o serviço que tem custos inesperados e determinar quais recursos estão diretamente associados ao recurso na Análise de custo. Por exemplo, se desejar analisar encargos do serviço de Máquinas Virtuais, navegue até a exibição **Custo acumulado**. Em seguida, defina a granularidade como **Diária** e filtre os encargos **Nome do serviço: máquinas virtuais** e agrupe-os por **Recurso**.

[![Exemplo mostrando os custos acumulados para máquinas virtuais](./media/analyze-unexpected-charges/virtual-machines.png)](./media/analyze-unexpected-charges/virtual-machines.png#lightbox)

## <a name="identify-spikes-in-cost-over-time"></a>Identificar picos de custo ao longo do tempo

Às vezes, talvez você não saiba quais custos recentes resultaram em alterações nos encargos cobrados. Para entender o que mudou, você pode usar a Análise de custo para [ver um detalhamento diário ou mensal dos custos ao longo do tempo](../costs/cost-analysis-common-uses.md#view-costs-per-day-or-by-month). Depois de criar a exibição, agrupe seus encargos por **Serviço** ou **Recurso** de para identificar as alterações. Você também pode alterar sua exibição para um gráfico de **Linha** para visualizar melhor os dados.

![Exemplo mostrando os custos ao longo do tempo na análise de custo](./media/analyze-unexpected-charges/costs-over-time.png)

## <a name="determine-resource-pricing-and-billing-model"></a>Determinar o preço do recurso e o modelo de cobrança

Um único recurso pode acumular encargos entre vários produtos e serviços do Azure. Veja a página [Preço do Azure por produto](https://azure.microsoft.com/pricing/#product-pricing) para saber mais sobre o preço de cada serviço do Azure. Por exemplo, uma única VM (máquina virtual) criada no Azure pode ter os medidores a seguir criados para rastrear o uso. Cada um pode ter um preço diferente.

- Calcular horas
- Horas de Endereço IP
- Transferência de Dados de Entrada
- Transferência de Dados de Saída
- Disco Gerenciado Standard
- Operações do Disco Gerenciado Standard
- Disco de E/S Padrão
- E/S Padrão – Leitura de Blob de Blocos
- E/S Padrão – Gravação de Blob de Blocos
- Bloqueio de blobs padrão IO-Block

Quando a VM é criada, cada medidor começa a emitir registros de uso. O uso e o preço do medidor são rastreados no sistema de medição do Azure. Você pode ver os medidores que foram usados para calcular sua fatura no arquivo CSV de uso.

## <a name="find-people-responsible-for-the-resource-and-engage"></a>Encontrar pessoas responsáveis pelo recurso e envolvê-las

Geralmente, a equipe responsável por um determinado recurso conhecerá as alterações que foram feitas em um recurso. O envolvimento dessa equipe é útil porque você identifica por que os encargos podem aparecer. Por exemplo, a equipe proprietária pode ter criado recentemente o recurso, atualizado o SKU dele (alterando a taxa do recurso) ou aumentado a carga no recurso devido às alterações de código. Continue lendo as seções a seguir para obter mais técnicas para determinar quem tem a propriedade de um recurso.

### <a name="analyze-the-audit-logs-for-the-resource"></a>Analisar os logs de auditoria do recurso

Se você tiver permissões para exibir um recurso, deverá ser capaz de acessar os logs de auditoria dele. Examine os logs para encontrar o usuário responsável pelas alterações mais recentes em um recurso. Para saber mais, confira [Exibir e recuperar eventos do Log de atividades do Azure](../../azure-monitor/essentials/activity-log.md#view-the-activity-log).

### <a name="analyze-user-permissions-to-the-resources-parent-scope"></a>Analisar as permissões do usuário para o escopo pai do recurso

As pessoas que têm acesso de gravação a uma assinatura ou grupo de recursos normalmente têm informações sobre os recursos que foram criados. Elas devem ter a capacidade de explicar a finalidade de um recurso ou indicar você à pessoa que saiba. Para identificar as pessoas que têm permissões para um escopo de Assinatura, confira [Verificar o acesso de um usuário a recursos do Azure](../../role-based-access-control/check-access.md). Você pode usar um processo semelhante para grupos de recursos.

## <a name="get-help-to-identify-charges"></a>Obter ajuda para identificar encargos

Se você tiver usado as estratégias anteriores e ainda não entendeu por que você recebeu um encargo ou se precisar de outra ajuda com problemas de cobrança, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas

- Saiba como [Otimizar seu investimento na nuvem com o Gerenciamento de Custos do Azure](../costs/cost-mgt-best-practices.md).