---
title: Monitorar uso e os gastos com alertas do custo | Microsoft Docs
description: Este artigo descreve como alertas de custo ajudam você a monitorar uso e os gastos no gerenciamento de custos do Azure.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/13/2019
ms.topic: conceptual
ms.service: cost-management
manager: alavital
ms.custom: ''
ms.openlocfilehash: feb7fcdd9005ef131acadfc63defbe4caeaca014
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60576522"
---
# <a name="use-cost-alerts-to-monitor-usage-and-spending"></a>Usar alertas de custo para monitorar o uso e os gastos

Este artigo ajuda você a entender e a usar alertas de gerenciamento de custos para monitorar o seu uso do Azure e os gastos. Os alertas de custo são gerados automaticamente com base em quando os recursos do Azure são consumidos. Alertas de mostram todos os alertas de cobrança e gerenciamento de custos ativos juntos em um só lugar. Quando seu consumo atinge um determinado limite, são gerados alertas pelo Gerenciamento de Custos. Há três tipos de alertas de custo: alertas de orçamento, alertas de crédito e alertas de cota de gastos do departamento.

## <a name="budget-alerts"></a>Alertas de orçamento

Alertas de orçamento o notificam quando os gastos, com base em uso ou custos, atingem ou excedem o valor definido na [condição de alerta do orçamento](tutorial-acm-create-budgets.md). Orçamentos de gerenciamento de custos são criados usando o portal do Azure ou a [API de consumo do Azure](https://docs.microsoft.com/rest/api/consumption).

No portal do Azure, os orçamentos são definidos pelo custo. Usando a API de Consumo do Azure, os orçamentos são definidos pelo custo ou pelo uso de consumo. Alertas de orçamento dão suporte a orçamentos com base em custo e em uso. Alertas de orçamento são gerados automaticamente sempre que as condições de alerta de orçamento são atendidas. Você pode exibir todos os alertas de custo no portal do Azure. Sempre que um alerta é gerado, ele é mostrado em alertas de custo. Um email de alerta também é enviado para as pessoas na lista de destinatários de alerta do orçamento.

## <a name="credit-alerts"></a>Alertas de crédito

Alertas de crédito o notificam quando seus compromissos monetários do crédito do Azure são consumidos. Compromissos monetários são para organizações com Contratos Enterprise. Alertas de crédito são gerados automaticamente em 90% e 100% do seu saldo de crédito do Azure. Sempre que um alerta é gerado, ele é refletido nos alertas de custo e no email enviado para os proprietários da conta.

## <a name="department-spending-quota-alerts"></a>Alertas de cota de gastos do departamento

Alertas de cota de gastos de departamento notificam quando o departamento de gastos atinge um limite fixo da cota. As cotas de gastos são configuradas no portal do EA. Sempre que um limite é atingido, ele gera um email aos proprietários do departamento e é mostrado em alertas de custo. Por exemplo, 50% ou 75% da cota.

## <a name="supported-alert-features-by-offer-categories"></a>Suporte para os recursos de alerta por categorias de oferta

Suporte para tipos de alerta depende do tipo de conta do Azure que você tenha (Microsoft) oferecem. A tabela a seguir mostra os recursos de alerta que têm suporte por várias ofertas de Microsoft. Você pode exibir a lista completa de ofertas da Microsoft em [dados de compreender o gerenciamento de custos](understand-cost-mgt-data.md).

| Tipo de alerta | Contrato Enterprise | Contrato de Cliente da Microsoft | Web direct/pago conforme-You-prosseguir |
|---|---|---|---|
| Orçamento | ✔ | ✔ | ✔ |
| Crédito | ✔ |✘ | ✘ |
| Cota de gastos do departamento | ✔ | ✘ | ✘ |



## <a name="view-cost-alerts"></a>Exibir alertas de custo

Para exibir alertas de custo, abra o escopo desejado no portal do Azure e selecione **orçamentos** no menu. Use o **escopo** com conteúdo malicioso para alternar para um escopo diferente. Selecione **custo alertas** no menu. Para obter mais informações sobre escopos, consulte [Entender e trabalhar com escopos](understand-work-scopes.md).

![Imagem de exemplo de alertas mostrados no Gerenciamento de Custos](./media/cost-mgt-alerts-monitor-usage-spending/budget-alerts-fullscreen.png)

O número total de alertas ativos e ignorados aparece na página de alertas de custo.

Todos os alertas mostram o tipo de alerta. Um alerta de orçamento mostra o motivo pelo qual ele foi gerado e o nome do orçamento ao qual ele se aplica. Cada alerta mostra a data em que ele foi gerado, seu status e o escopo (assinatura ou grupo de gerenciamento) ao qual o alerta se aplica.

Possíveis status incluem **ativo** e **descartado**. O status ativo indica que o alerta ainda é relevante. O status ignorado indica que alguém marcou o alerta para configurá-lo como não há mais relevantes.

Selecione um alerta na lista para exibir os detalhes. Os detalhes do alerta mostram mais informações sobre o alerta. Alertas de orçamento incluem um link para o orçamento. Se uma recomendação está disponível para um alerta de orçamento, um link para a recomendação também é mostrado. Alertas de cota de gastos de departamento, crédito e orçamento têm um link para analisar de análise de custo no qual você pode explorar os custos para o escopo do alerta. O exemplo a seguir mostra os gastos para um departamento com detalhes do alerta.

![Imagem de exemplo mostrando os gastos para um departamento com detalhes do alerta](./media/cost-mgt-alerts-monitor-usage-spending/dept-spending-selected-with-credits.png)

Quando você exibe os detalhes de um alerta ignorado, pode reativá-lo se a ação manual é necessária. A imagem a seguir mostra um exemplo.

![Imagem de exemplo mostrando as opções de ignorar e reativar](./media/cost-mgt-alerts-monitor-usage-spending/Dismiss-reactivate-options.png)

## <a name="see-also"></a>Consulte também

- Se você não tiver criado um orçamento nem definido condições de alerta para um orçamento, conclua o tutorial [Criar e gerenciar orçamentos](tutorial-acm-create-budgets.md).
