---
title: Planejar o gerenciamento de custos do Azure
description: Saiba como planejar o gerenciamento de custos do Azure e usar os recursos de acompanhamento e gerenciamento de custos para sua conta do Azure.
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: common
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: banders
ms.custom: contperf-fy21q1
ms.openlocfilehash: 210f7daa9305f2fb343dfae050354264e2fd823e
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98898555"
---
# <a name="plan-to-manage-azure-costs"></a>Planejar o gerenciamento de custos do Azure

Este artigo ajuda você a aprender a planejar o gerenciamento de custos do Azure. Ao se inscrever no Azure, há várias coisas que podem ser feitas para você ter uma ideia melhor de seus gastos:

- Obtenha os custos estimados antes de adicionar serviços usando a [Calculadora de Preços](https://azure.microsoft.com/pricing/calculator/), a tabela de preços do Azure ou durante a adição de serviços no portal do Azure.
- Monitore os custos com [orçamentos](../costs/tutorial-acm-create-budgets.md), [alertas](../costs/cost-mgt-alerts-monitor-usage-spending.md) e [análise de custo](../costs/quick-acm-cost-analysis.md).
- Examine os encargos em sua fatura comparando-os com [arquivos de uso detalhado](../manage/download-azure-invoice-daily-usage-date.md).
- Integre os dados de cobrança e custo ao seu próprio sistema de relatórios usando APIs de [cobrança](/rest/api/billing/) e [consumo](/rest/api/consumption/).
- Use recursos e ferramentas adicionais para clientes do EA (Contrato Enterprise), do CSP (Provedor de Soluções na Nuvem) e do Azure Sponsorship.
- Use [alguns dos serviços mais populares do Azure gratuitamente por 12 meses](../manage/create-free-services.md) disponíveis com a [conta gratuita do Azure](https://azure.microsoft.com/free/). Juntamente com as recomendações listadas abaixo, consulte [Evitar ser cobrado na conta gratuita](../manage/avoid-charges-free-account.md).

Se você precisar cancelar sua assinatura do Azure, confira [Cancelar sua assinatura do Azure](../manage/cancel-azure-subscription.md).

## <a name="get-estimated-costs-before-adding-azure-services"></a>Obter custos estimados antes de adicionar serviços do Azure

Use uma das seguintes ferramentas para estimar o custo do uso de um serviço do Azure:
- Calculadora de preços do Azure
- Tabela de preços do Azure
- Portal do Azure

As imagens nas seções a seguir mostram os preços de exemplo em dólares americanos. Os preços mencionados são apenas para exemplificar. Eles não se destinam a implicar os custos reais. 

### <a name="estimate-cost-online-using-the-pricing-calculator"></a>Estimar custo online usando a calculadora de preços

Confira a [Calculadora de Preços](https://azure.microsoft.com/pricing/calculator/) para obter uma estimativa do custo mensal do serviço que deseja adicionar. Altere a moeda para obter a estimativa em sua moeda local.

![Captura de tela do menu de calculadora de preços](./media/plan-manage-costs/pricing-calc.png)

Exiba o custo estimado de qualquer serviço interno do Azure. Por exemplo, na captura de tela abaixo, o custo de uma VM (Máquina Virtual) do Windows A1 é estimado como US$ 66,96 por mês em horas de computação se você deixá-la em execução o tempo todo:

![Captura de tela da calculadora de preços mostrando um custo estimado de VM Windows A1 por mês](./media/plan-manage-costs/pricing-calc-vm.png)

Os preços mencionados são apenas para exemplificar. Eles não se destinam a implicar os custos reais.

Para obter mais informações sobre preços, confira as [Perguntas frequentes sobre Preços](https://azure.microsoft.com/pricing/faq/). Se desejar conversar com um vendedor do Azure, ligue para o número de telefone mostrado na parte superior da página de Perguntas frequentes.

### <a name="review-prices"></a>Examinar preços

Caso tenha acesso ao Azure por meio de um EA (Contrato Enterprise) ou um MCA (Contrato de Cliente da Microsoft), você poderá exibir e baixar a tabela de preços de sua conta do Azure. A tabela de preços é um arquivo do Excel que contém os preços de todos os serviços do Azure. Para obter mais informações, confira [Exibir e baixar os preços do Azure](../manage/ea-pricing.md).

Para outros tipos de assinatura, você pode obter preços de varejo padrão com a [API de Preços de Varejo do Azure](/rest/api/cost-management/retail-prices/azure-retail-prices).

### <a name="review-estimated-costs-in-the-azure-portal"></a>Examinar os custos estimados no portal do Azure

Exiba o custo estimado por mês durante a adição de um serviço no portal do Azure. Por exemplo, quando você escolhe o tamanho de sua VM do Windows, vê o custo mensal estimado para as horas de computação:

![Exemplo: uma VM Windows A1 mostrando o custo estimado por mês](./media/plan-manage-costs/vm-size-cost.png)

Os preços mencionados são apenas para exemplificar. Eles não se destinam a implicar os custos reais.

## <a name="monitor-costs-when-using-azure-services"></a>Monitorar custos ao usar os serviços do Azure
É possível monitorar custos com as seguintes ferramentas:

- Orçamento e alertas de custo
- Análise de custo

### <a name="track-costs-with-budgets-and-cost-alerts"></a>Acompanhar os custos com orçamentos e alertas de custo

Crie [orçamentos](../costs/tutorial-acm-create-budgets.md) para gerenciar custos e crie [alertas](../costs/cost-mgt-alerts-monitor-usage-spending.md) que notificam você e os stakeholders automaticamente de anomalias de gastos e gastos em excesso.

### <a name="explore-and-analyze-costs-with-cost-analysis"></a><a name="costs"></a> Explorar e analisar os custos com a análise de custos

Depois que os serviços do Azure estiverem em execução, verifique os custos regularmente para acompanhar seus gastos do Azure. Use a análise de custo para entender a procedência dos custos para o uso do Azure.

Visite a [página Gerenciamento de Custos + Cobrança no portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade).

Clique em **Análise de custo** no lado esquerdo da tela para ver o custo atual dividido por vários pivôs, como serviço, localização e assinatura. Depois de adicionar um serviço ou fazer uma compra, aguarde 24 horas para que os dados sejam exibidos. Por padrão, a análise de custo mostra o custo do escopo em que você está. Por exemplo, na captura de tela abaixo, o custo da conta de cobrança da Contoso é exibido. Use o item Escopo para alterar para outro escopo na análise de custo. Para obter mais informações sobre escopos, confira [Entender e trabalhar com escopos](../costs/understand-work-scopes.md#scopes)

![Captura de tela do modo de exibição de análise de custo no portal do Azure](./media/plan-manage-costs/cost-analysis.png)

Filtre por várias propriedades, como marcas, tipo de recurso e período de tempo. Clique em **Adicionar filtro** para adicionar o filtro de uma propriedade e selecione os valores a serem filtrados. Selecione **Exportar** para exportar a exibição para um arquivo de valores separados por vírgula (.csv).

Além disso, clique nos rótulos do gráfico para ver o histórico de gastos diário para esse rótulo. Por exemplo, na captura de tela abaixo, a seleção de uma máquina virtual exibe o custo diário de executar suas VMs.

:::image type="content" source="./media/plan-manage-costs/cost-history.png" alt-text="Captura de tela da exibição do histórico de gastos no portal do Azure" lightbox="./media/plan-manage-costs/cost-history.png" :::

## <a name="optimize-and-reduce-costs"></a>Otimizar e reduzir custos

Se você não estiver familiarizado com os princípios de gerenciamento de custos, leia [Como otimizar seu investimento em nuvem com o Gerenciamento de Custos do Azure](../costs/cost-mgt-best-practices.md).

No portal do Azure, também é possível otimizar e reduzir custos do Azure com desligamento automático para VMs e recomendações do Assistente.

### <a name="consider-cost-cutting-features-like-auto-shutdown-for-vms"></a>Considere recursos de redução de custos, como o desligamento automático para VMs

Dependendo do cenário, você pode configurar o desligamento automático para suas VMs no portal do Azure. Para saber mais, confira [Desligamento automático de máquinas virtuais usando o Azure Resource Manager](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![Captura de tela da opção de desligamento automático no portal](./media/plan-manage-costs/auto-shutdown.png)

O desligamento automático não é igual a desligar a VM com as opções de energia padrão. O desligamento automático interrompe e desaloca suas VMs para parar as cobranças adicionais. Para saber mais, confira os estados de VM nas Perguntas frequentes sobre preços de [VMs Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e [VMs Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/).

Para obter mais recursos de redução de custos para os ambientes de desenvolvimento e teste, confira [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/).

### <a name="turn-on-and-review-azure-advisor-recommendations"></a>Ativar e examinar recomendações do Assistente do Azure

[O Assistente do Azure](../../advisor/advisor-overview.md) ajuda a reduzir os custos identificando recursos com baixa utilização. Pesquise **Assistente** no portal do Azure:

![Captura de tela do botão Azure Advisor no portal do Azure](./media/plan-manage-costs/advisor-button.png)

Selecione **Custo** no lado esquerdo. Você verá recomendações práticas na guia **Custo**:

![Captura de tela de exemplo de recomendação de custo do Advisor](./media/plan-manage-costs/advisor-action.png)

Os preços mencionados são apenas para exemplificar. Eles não se destinam a implicar os custos reais.

Examine o tutorial [Otimizar custos de recomendações](../costs/tutorial-acm-opt-recommendations.md) para obter um tutorial guiado sobre recomendações do Assistente para economia de custos.

## <a name="integrate-with-billing-and-consumption-apis"></a>Integração às APIs de cobrança e consumo

Use as APIs de [cobrança](/rest/api/billing/) e [consumo](/rest/api/consumption/) do Azure para obter dados de cobrança e custo de forma programática. Use a API RateCard junto com a API de Uso para obter seu uso cobrado. Para saber mais, confira [Obtenha informações sobre o consumo de recursos do Microsoft Azure](../manage/consumption-api-overview.md#usage-details-api).

## <a name="additional-resources-and-special-cases"></a><a name="other-offers"></a> Casos especiais e recursos adicionais

### <a name="ea-csp-and-sponsorship-customers"></a>Clientes do EA, CSP e Sponsorship
Fale com seu gerente de conta ou parceiro do Azure para começar.

| Oferta | Recursos |
|-------------------------------|-----------------------------------------------------------------------------------|
| EA (Enterprise Agreement) | [Portal EA](https://ea.azure.com/), [documentos de ajuda](https://ea.azure.com/helpdocs) e [relatório do Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-enterprise/) |
| Programa do CSP (Provedor de Soluções na Nuvem) | Fale com seu provedor |
| Azure Sponsorship | [Portal do Sponsorship](https://www.microsoftazuresponsorships.com/) |

Se estiver gerenciando TI em uma grande organização, recomendamos a leitura de [Azure enterprise scaffold](/azure/architecture/cloud-adoption-guide/subscription-governance) e [enterprise IT white paper](https://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf) (baixe o .pdf, somente em inglês).

### <a name="enterprise-agreement-cost-views-in-the-azure-portal"></a><a name="EA"></a> Visualizações de custo do Contrato Enterprise no portal do Azure

As visualizações de custo Enterprise estão, no momento, em Visualização Pública. Itens a serem observados:

- Os custos de assinatura são baseados no uso e não incluem valores pré-pagos, excedentes, quantidades incluídas, ajustes e impostos. Os encargos reais são calculados no nível de registro.
- Os valores mostrados no portal do Azure podem ser diferentes daqueles no Enterprise Portal. Atualizações no Enterprise Portal podem levar alguns minutos antes de serem mostradas no portal do Azure.
- Se você não está vendo os custos, isso pode ser por um dos seguintes motivos:
    - Você não tem permissões no nível da assinatura. Para ver as exibições de custo empresarial, você precisa ser um Leitor de Cobrança, Leitor, Colaborador ou Proprietário no nível da assinatura.
    - Você é um Proprietário de Conta e seu Administrador de registro desabilitou a configuração “Encargos de exibição do AO”.  Contate o Administrador de registro para obter acesso aos custos.
    - Você é um administrador de departamento e seu administrador de registro desabilitou a configuração **Encargos de exibição do DA**.  Entre em contato com o Administrador de registro para obter acesso.
    - Você comprou o Azure por meio de um parceiro de canal e o parceiro não liberou informações sobre preços.  
- Se você atualizar as configurações relacionadas ao custo acessando o Enterprise Portal, haverá um atraso de alguns minutos antes que as alterações sejam mostradas no portal do Azure.
- Limite de gastos e diretrizes de fatura não se aplicam às assinaturas do Contrato Enterprise.

### <a name="check-your-subscription-and-access"></a>Verificar assinatura e acesso

Para exibir os custos, você precisa ter acesso no nível de conta ou de assinatura às informações de custo ou de cobrança. O acesso varia de acordo com o tipo de conta de cobrança. Para saber mais sobre contas de cobrança e verificar o tipo de sua conta de cobrança, confira [Exibir contas de cobrança no portal do Azure](../manage/view-all-accounts.md).

Caso tenha acesso ao Azure por meio de uma conta de cobrança do MOSP (Programa do Microsoft Online Services), confira [Gerenciar o acesso às informações de cobrança para o Azure](../manage/manage-billing-access.md).

Caso tenha acesso ao Azure por meio de uma conta de cobrança do EA (Contrato Enterprise), confira [Entender as funções administrativas do Contrato Enterprise do Azure no Azure](../manage/understand-ea-roles.md).

Caso tenha acesso ao Azure por meio de uma conta de cobrança do MCA (Contrato de Cliente da Microsoft), confira [Entender as funções administrativas do Contrato de Cliente da Microsoft no Azure](../manage/understand-mca-roles.md).

### <a name="request-a-service-level-agreement-credit-for-a-service-incident"></a>Solicitar um crédito do Contrato de Nível de Serviço para um incidente de serviço

O SLA (Contrato de Nível de Serviço) descreve os compromissos da Microsoft com relação ao tempo de atividade e à conectividade. Um incidente de serviço é relatado quando os serviços do Azure enfrentam um problema que afeta o tempo de atividade ou a conectividade, geralmente denominado *interrupção*. Se não alcançarmos e mantivermos os Níveis de Serviço para cada serviço, conforme descrito no SLA, você poderá ser qualificado para um crédito para uma parte de seus valores de serviço mensais.

Para solicitar um crédito:

Entre no [portal do Azure](https://portal.azure.com/). Se você tiver várias contas, use a que foi afetada pelo tempo de inatividade do Azure. Em seguida, crie uma solicitação de suporte.

Em **Tipo de problema**, selecione **Cobrança** e, em **Tipo de problema**, selecione **Solicitação de Reembolso**.

Adicione detalhes para especificar que você está solicitando um crédito SLA, mencione a data/hora/fuso horário, além dos serviços afetados (VMs, sites etc.)

Por fim, verifique seus detalhes de contato e selecione **Criar** para enviar sua solicitação.

Para alguns serviços, há pré-requisitos para o SLA aplicar. Por exemplo, as máquinas virtuais devem ter duas ou mais instâncias implantadas no mesmo conjunto de disponibilidade.

Para obter mais informações, confira [Contratos de Nível de Serviço](https://azure.microsoft.com/support/legal/sla/) e a documentação [Resumo do SLA para serviços do Azure](https://azure.microsoft.com/support/legal/sla/summary/).

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre como usar [limites de gastos](../manage/spending-limit.md) para impedir o gasto excessivo.
- Comece [analisando os custos do Azure](../costs/quick-acm-cost-analysis.md).