---
title: Tempo de inatividade, SLA e a pasta de trabalho de interrupção-Application Insights
description: Calcule e relate o SLA para o teste na Web por meio de um único painel de vidro em seus recursos de Application Insights e assinaturas do Azure.
ms.topic: conceptual
ms.date: 02/8/2021
ms.openlocfilehash: d225627a27bffd9088956e5aee37ca543e528d4a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101714044"
---
# <a name="downtime-sla-and-outages-workbook"></a>Tempo de inatividade, SLA e a pasta de trabalho de interrupções

Introdução de uma maneira simples de calcular e relatar SLA (contrato de nível de serviço) para testes da Web por meio de um único painel de vidro em seus recursos de Application Insights e assinaturas do Azure. O relatório de tempo de inatividade e interrupção fornece visualizações de dados e consultas pré-criados avançadas para aprimorar sua compreensão da conectividade do cliente, do tempo de resposta do aplicativo típico e do tempo de inatividade.

O modelo de pasta de trabalho SLA pode ser acessado por meio da Galeria de pastas de trabalho no recurso Application Insights ou pela guia disponibilidade, selecionando **relatórios SLA** na parte superior.
:::image type="content" source="./media/sla-report/availability.png" alt-text="Captura de tela da guia disponibilidade com relatórios SLA realçados." lightbox="./media/sla-report/availability.png":::

:::image type="content" source="./media/sla-report/workbook-gallery.png" alt-text="Captura de tela da Galeria de pastas de trabalho com tempo de inatividade e interrupções realçada." lightbox ="./media/sla-report/workbook-gallery.png":::

## <a name="parameter-flexibility"></a>Flexibilidade de parâmetro

Os parâmetros definidos na pasta de trabalho influenciam o restante do relatório.

:::image type="content" source="./media/sla-report/outages.png" alt-text=" Captura de tela da guia parâmetros de interrupção/manutenção na pasta de trabalho tempo de inatividade e interrupções." lightbox ="./media/sla-report/outages.png":::

`Subscriptions``App Insights Resources` `Web Test` os parâmetros, e determinam suas opções de recursos de alto nível. Esses parâmetros são baseados em consultas do log Analytics e usados em cada consulta de relatório.

`Failure Threshold` e `Outage Window` permitem que você determine seus próprios critérios para uma interrupção do serviço, por exemplo, os critérios para o alerta de disponibilidade do App insights com base no contador de localização com falha durante um período escolhido. O limite típico é de três locais em uma janela de cinco minutos.

`Maintenance Period` permite que você selecione a frequência de manutenção típica e `Maintenance Window` é um seletor de data/hora para um período de manutenção de exemplo. Todos os dados que ocorrerem durante o período identificado serão ignorados nos resultados.

`Availability Target 9s` Especifica o objetivo de seus nove-alvos de dois a cinco noves.

## <a name="overview-page"></a>Página de visão geral

A página Visão geral contém informações de alto nível sobre o SLA total (excluindo períodos de manutenção, se definido), instâncias de interrupção de ponta a ponta e tempo de inatividade do aplicativo. As instâncias de interrupção são definidas por quando um teste começa a falhar até que seja bem-sucedido com base nos parâmetros de interrupção. Se um teste começar a falhar às 8:00 e tiver êxito novamente às 10:00 am, esse período de dados inteiro será considerado a mesma interrupção.

:::image type="content" source="./media/sla-report/overview.gif" alt-text=" GIF da página Visão geral mostrando a tabela de visão geral por teste." lightbox="./media/sla-report/overview.gif":::

Você também pode investigar sua interrupção mais longa que ocorreu em seu período de relatório.

Alguns testes podem ser vinculados ao recurso de Application Insights para uma investigação mais aprofundada, mas isso só é possível no [recurso de Application insights baseado em espaço de trabalho](create-workspace-resource.md).

## <a name="downtime-outages-and-failures"></a>Tempo de inatividade, interrupções e falhas

A guia **interrupções e tempo de inatividade** tem informações sobre as instâncias de interrupção total e a hora de redução total dividida por teste. A guia **falhas por local** tem um mapa geográfico de locais de teste com falha para ajudar a identificar áreas de conexão de problema em potencial.

:::image type="content" source="./media/sla-report/outages-failures.gif" alt-text=" GIF de interrupções, guia tempo de inatividade e falha pela guia local na pasta de trabalho tempo de inatividade e interrupções." lightbox="./media/sla-report/outages-failures.gif":::

## <a name="edit-the-report"></a>Editar o relatório

Você pode editar o relatório como qualquer outra [pasta de trabalho Azure monitor](../visualize/workbooks-overview.md). Você pode personalizar as consultas ou visualizações com base nas necessidades da sua equipe.

:::image type="content" source="./media/sla-report/edit.gif" alt-text=" GIF de selecionar o botão Editar para alterar a visualização para um gráfico de pizza." lightbox="./media/sla-report/edit.gif":::

### <a name="log-analytics"></a>Log Analytics

As consultas podem ser executadas em [log Analytics](../logs/log-analytics-overview.md) e usadas em outros relatórios ou painéis. Remova a restrição de parâmetro e reutilize a consulta principal.

:::image type="content" source="./media/sla-report/logs.gif" alt-text=" GIF de consulta de log." lightbox="./media/sla-report/logs.gif":::

## <a name="access-and-sharing"></a>Acesso e compartilhamento

O relatório pode ser compartilhado com suas equipes, liderança ou fixado em um painel para uso posterior. O usuário precisa ter permissão de leitura/acesso ao recurso do Application insights em que a pasta de trabalho real está armazenada.

:::image type="content" source="./media/sla-report/share.png" alt-text=" Captura de tela de compartilhar este modelo." lightbox= "./media/sla-report/share.png":::

## <a name="next-steps"></a>Próximas etapas

- [Log Analytics dicas de otimização de consulta](../logs/query-optimization.md).
- Saiba como [criar um gráfico em pastas de trabalho](../visualize/workbooks-chart-visualizations.md).
- Saiba como monitorar seu site com [testes de disponibilidade](monitor-web-app-availability.md).