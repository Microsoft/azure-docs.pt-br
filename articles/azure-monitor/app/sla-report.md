---
title: Pasta de trabalho de tempo de inatividade, SLA e interrupção – Application Insights
description: Calcule e relate o SLA do teste na Web em um só lugar para todos os recursos do Application Insights e assinaturas do Azure.
ms.topic: conceptual
ms.date: 02/8/2021
ms.openlocfilehash: d225627a27bffd9088956e5aee37ca543e528d4a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101714044"
---
# <a name="downtime-sla-and-outages-workbook"></a>Tempo de inatividade, SLA e pasta de trabalho de interrupções

Apresentamos uma forma simples de calcular e relatar o SLA (Contrato de Nível de Serviço) do teste na Web em um só lugar para todos os recursos do Application Insights e assinaturas do Azure. O relatório Tempo de inatividade e interrupção mostra visualizações de dados e consultas pré-criadas avançadas para você entender melhor a conectividade do cliente, o tempo de resposta comum do aplicativo e o tempo de inatividade.

O modelo da pasta de trabalho de SLA está disponível na galeria de pastas de trabalho do recurso do Application Insights ou na guia de disponibilidade, em **Relatórios de SLA** na parte superior.
:::image type="content" source="./media/sla-report/availability.png" alt-text="Captura de tela da guia de disponibilidade com relatórios de SLA realçados." lightbox="./media/sla-report/availability.png":::

:::image type="content" source="./media/sla-report/workbook-gallery.png" alt-text="Captura de tela da galeria de pastas de trabalho com a pasta de trabalho de tempo de inatividade e interrupções realçada." lightbox ="./media/sla-report/workbook-gallery.png":::

## <a name="parameter-flexibility"></a>Flexibilidade de parâmetro

Os parâmetros definidos na pasta de trabalho influenciam o restante do relatório.

:::image type="content" source="./media/sla-report/outages.png" alt-text=" Captura de tela da guia parâmetros de interrupção/manutenção na pasta de trabalho de tempo de inatividade e interrupções." lightbox ="./media/sla-report/outages.png":::

Os parâmetros `Subscriptions`, `App Insights Resources` e `Web Test` determinam as opções de recursos de alto nível. Esses parâmetros são baseados em consultas do Log Analytics e usados em cada consulta de relatório.

`Failure Threshold` e `Outage Window` permitem que você determine os próprios critérios para uma interrupção do serviço, por exemplo, os critérios para o alerta de disponibilidade do App Insights com base no contador de localização com falha durante um período escolhido. O limite comum é de três locais em uma janela de cinco minutos.

`Maintenance Period` permite selecionar a frequência de manutenção comum e `Maintenance Window` é um seletor de data/hora para um período de manutenção de exemplo. Todos os dados que ocorrerem durante o período identificado serão ignorados nos resultados.

`Availability Target 9s` especifica a meta de "noves", desde dois até cinco "noves".

## <a name="overview-page"></a>Página de visão geral

A página Visão geral contém informações de alto nível sobre o SLA total (excluindo períodos de manutenção, se definido), instâncias de interrupção de ponta a ponta e tempo de inatividade de aplicativo. As instâncias de interrupção vão desde o início da falha do teste até seu sucesso com base nos parâmetros de interrupção. Se um teste começar a falhar às 8h e tiver êxito novamente às 10h, esse período de dados inteiro será considerado a mesma interrupção.

:::image type="content" source="./media/sla-report/overview.gif" alt-text=" GIF da página Visão geral mostrando a tabela de visão geral por teste." lightbox="./media/sla-report/overview.gif":::

Você também pode investigar a interrupção mais longa que ocorreu no período do relatório.

Alguns testes contêm link para o recurso correspondente do Application Insights para uma investigação mais aprofundada, mas isso só é possível no [recurso do Application Insights baseado em workspace](create-workspace-resource.md).

## <a name="downtime-outages-and-failures"></a>Tempo de inatividade, interrupções e falhas

A guia **Interrupções e tempo de inatividade** tem informações sobre o total de instâncias de interrupção e o tempo de inatividade dividido por teste. A guia **Falhas por Localização** tem um mapa geográfico de localizações de teste com falha para identificar áreas de conexão de problema em potencial.

:::image type="content" source="./media/sla-report/outages-failures.gif" alt-text=" GIF da guia Interrupções e tempo de inatividade e da guia Falhas por Localização na pasta de trabalho de tempo de inatividade e interrupções." lightbox="./media/sla-report/outages-failures.gif":::

## <a name="edit-the-report"></a>Editar o relatório

Você pode editar o relatório como qualquer outra [pasta de trabalho do Azure Monitor](../visualize/workbooks-overview.md). Você pode personalizar as consultas ou visualizações com base nas necessidades da sua equipe.

:::image type="content" source="./media/sla-report/edit.gif" alt-text=" GIF mostrando como selecionar o botão Editar para alterar a visualização para um gráfico de pizza." lightbox="./media/sla-report/edit.gif":::

### <a name="log-analytics"></a>Log Analytics

As consultas podem ser executadas no [Log Analytics](../logs/log-analytics-overview.md) e usadas em outros relatórios ou painéis. Remova a restrição de parâmetro e reutilize a consulta principal.

:::image type="content" source="./media/sla-report/logs.gif" alt-text=" GIF da consulta de log." lightbox="./media/sla-report/logs.gif":::

## <a name="access-and-sharing"></a>Acesso e compartilhamento

O relatório pode ser compartilhado com suas equipes ou liderança ou fixado em um painel para uso posterior. O usuário precisa ter permissão de leitura/acesso ao recurso do Application Insights em que a pasta de trabalho real está armazenada.

:::image type="content" source="./media/sla-report/share.png" alt-text=" Captura de tela de compartilhar este modelo." lightbox= "./media/sla-report/share.png":::

## <a name="next-steps"></a>Próximas etapas

- [Dicas de otimização de consulta no Log Analytics](../logs/query-optimization.md).
- Saiba como [criar um gráfico em pastas de trabalho](../visualize/workbooks-chart-visualizations.md).
- Saiba como monitorar seu site com [testes de disponibilidade](monitor-web-app-availability.md).