---
title: Monitorar o uso e os gastos com alertas de custo
description: Este artigo descreve como alertas de custo ajudam você a monitorar uso e os gastos no gerenciamento de custos do Azure.
author: bandersmsft
ms.author: banders
ms.date: 09/03/2020
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: d1546ef92d923b9d72c4aca0ddf61d2b93646656
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98602346"
---
# <a name="use-cost-alerts-to-monitor-usage-and-spending"></a>Usar alertas de custo para monitorar o uso e os gastos

Este artigo ajuda você a entender e a usar alertas de gerenciamento de custos para monitorar o seu uso do Azure e os gastos. Os alertas de custo são gerados automaticamente com base em quando os recursos do Azure são consumidos. Alertas de mostram todos os alertas de cobrança e gerenciamento de custos ativos juntos em um só lugar. Quando seu consumo atinge um determinado limite, são gerados alertas pelo Gerenciamento de Custos. Há três tipos de alertas de custo: alertas de orçamento, alertas de crédito e alertas de cota de gastos do departamento.

## <a name="budget-alerts"></a>Alertas de orçamento

Alertas de orçamento o notificam quando os gastos, com base em uso ou custos, atingem ou excedem o valor definido na [condição de alerta do orçamento](tutorial-acm-create-budgets.md). Orçamentos de gerenciamento de custos são criados usando o portal do Azure ou a [API de consumo do Azure](/rest/api/consumption).

No portal do Azure, os orçamentos são definidos pelo custo. Usando a API de Consumo do Azure, os orçamentos são definidos pelo custo ou pelo uso de consumo. Alertas de orçamento dão suporte a orçamentos com base em custo e em uso. Alertas de orçamento são gerados automaticamente sempre que as condições de alerta de orçamento são atendidas. Você pode exibir todos os alertas de custo no portal do Azure. Sempre que um alerta é gerado, ele é mostrado em alertas de custo. Um email de alerta também é enviado para as pessoas na lista de destinatários de alerta do orçamento.

É possível usar a API de Orçamento para enviar alertas por email em um idioma diferente. Para saber mais, confira [Localidades com suporte para alertas de orçamento por email](manage-automation.md#supported-locales-for-budget-alert-emails).

## <a name="credit-alerts"></a>Alertas de crédito

Os alertas de crédito notificam quando seu Pagamento antecipado do Azure (anteriormente conhecido como compromisso monetário) é consumido. O Pagamento antecipado do Azure é para organizações com Contratos Enterprise. Alertas de crédito são gerados automaticamente em 90% e 100% do seu saldo de crédito do Pagamento antecipado do Azure. Sempre que um alerta é gerado, ele se reflete nos alertas de custo e no email enviado aos proprietários da conta.

## <a name="department-spending-quota-alerts"></a>Alertas de cota de gasto do departamento

Os alertas de cota de gasto do departamento notificam você quando os gastos do departamento atingem um limite fixo da cota. As cotas de gasto são configuradas no Portal do EA. Sempre que um limite é atingido, ele gera um email para proprietários do departamento e é mostrado nos alertas de custo. Por exemplo, 50% ou 75% da cota.

## <a name="supported-alert-features-by-offer-categories"></a>Recursos de alerta compatíveis por categorias de oferta

O suporte para tipos de alertas depende do tipo de conta do Azure que você tem (oferta da Microsoft). A tabela a seguir mostra os recursos de alerta compatíveis com várias ofertas da Microsoft. Você pode exibir a lista completa de ofertas da Microsoft em [Entender os dados de Gerenciamento de Custos](understand-cost-mgt-data.md).

| Tipo de alerta | Contrato Enterprise | Contrato de Cliente da Microsoft | Web direct/pago conforme o uso |
|---|---|---|---|
| Orçamento | ✔ | ✔ | ✔ |
| Crédito | ✔ |✘ | ✘ |
| Cota de gastos do departamento | ✔ | ✘ | ✘ |



## <a name="view-cost-alerts"></a>Exibir alertas de custo

Para exibir alertas de custo, abra o escopo desejado no portal do Azure e selecione **Orçamentos** no menu. Use o item **Escopo** para alterar para outro escopo. Selecione **Alertas de custo** no menu. Para obter mais informações sobre escopos, consulte [Entender e trabalhar com escopos](understand-work-scopes.md).

![Imagem de exemplo de alertas mostrados no Gerenciamento de Custos](./media/cost-mgt-alerts-monitor-usage-spending/budget-alerts-fullscreen.png)

O número total de alertas ativos e ignorados é exibido na página alertas de custo.

Todos os alertas mostram o tipo de alerta. Um alerta de orçamento mostra o motivo pelo qual ele foi gerado e o nome do orçamento ao qual ele se aplica. Cada alerta mostra a data em que ele foi gerado, seu status e o escopo (assinatura ou grupo de gerenciamento) ao qual o alerta se aplica.

Os possíveis status incluem **ativo** e **ignorado**. O status ativo indica que o alerta ainda é relevante. O status ignorado indica que alguém marcou o alerta para defini-lo como não mais relevante.

Selecione um alerta na lista para exibir os detalhes dele. Os detalhes do alerta mostram mais informações sobre o alerta. Os alertas de orçamento incluem um link para o orçamento. Se uma recomendação estiver disponível para um alerta de orçamento, um link para a recomendação também será mostrado. Os alertas de cota de gastos do departamento, crédito e orçamento têm um link para análise na análise de custo, em que você pode explorar os custos para o escopo do alerta. O exemplo a seguir mostra os gastos para um departamento com detalhes do alerta.

![Imagem de exemplo mostrando os gastos para um departamento com detalhes do alerta](./media/cost-mgt-alerts-monitor-usage-spending/dept-spending-selected-with-credits.png)

Quando você exibe os detalhes de um alerta ignorado, pode reativá-lo se a ação manual é necessária. A imagem a seguir mostra um exemplo.

![Imagem de exemplo mostrando as opções de ignorar e reativar](./media/cost-mgt-alerts-monitor-usage-spending/Dismiss-reactivate-options.png)

## <a name="see-also"></a>Confira também

- Se você não tiver criado um orçamento nem definido condições de alerta para um orçamento, conclua o tutorial [Criar e gerenciar orçamentos](tutorial-acm-create-budgets.md).