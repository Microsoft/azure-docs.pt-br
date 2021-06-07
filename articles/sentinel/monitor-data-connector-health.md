---
title: Monitorar a integridade de seus conectores de dados com esta pasta de trabalho do Azure Sentinel | Microsoft Docs
description: Use a pasta de trabalho de monitoramento de integridade para controlar a conectividade e o desempenho dos conectores de dados.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2020
ms.author: yelevin
ms.openlocfilehash: 7d52b76601a617f62ae5b10fa38841ef2608bf49
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94656983"
---
# <a name="monitor-the-health-of-your-data-connectors-with-this-azure-sentinel-workbook"></a>Monitorar a integridade de seus conectores de dados com esta pasta de trabalho do Azure Sentinel

A **pasta de trabalho monitoramento de integridade dos conectores de dados** permite que você controle a integridade, a conectividade e o desempenho dos conectores de dados de dentro do Azure Sentinel. A pasta de trabalho fornece monitores adicionais, detecta anomalias e fornece informações sobre o status de ingestão de dados do espaço de trabalho. Você pode usar a lógica da pasta de trabalho para monitorar a integridade geral dos dados ingeridos e para criar exibições personalizadas e alertas baseados em regras.

## <a name="use-the-health-monitoring-workbook"></a>Usar a pasta de trabalho de monitoramento de integridade

1. No portal do Azure Sentinel, selecione **pastas de trabalho** no menu **Gerenciamento de ameaças** .

1. Na Galeria de **pastas de trabalho** , insira *integridade* na barra de pesquisa e selecione **monitoramento de integridade da coleta de dados** entre os resultados.

1. Selecione o **modelo de exibição** para usar a pasta de trabalho como está ou selecione **salvar** para criar uma cópia editável da pasta de trabalho. Quando a cópia for criada, selecione **Exibir pasta de trabalho salva**.

1. Uma vez na pasta de trabalho, primeiro selecione a **assinatura** e o **espaço de trabalho** que você deseja exibir e defina o **intervalo** de tempo para filtrar os dados de acordo com suas necessidades. Use a alternância **Mostrar ajuda** para exibir a explicação in-loco da pasta de trabalho.

    :::image type="content" source="media/monitor-data-connector-health/data-health-workbook-1.png" alt-text="Página inicial da pasta de trabalho de monitoramento de integridade do conector de dados" lightbox="media/monitor-data-connector-health/data-health-workbook-1.png":::

Há três seções com guias nesta pasta de trabalho:

1. A guia **visão geral** mostra o status geral da ingestão de dados no espaço de trabalho selecionado: medidas de volume, taxas EPS e tempo do último log recebido.

1. A guia **anomalias de coleta de dados** ajudará você a detectar anomalias no processo de coleta de dados, por tabela e fonte de dados. Cada guia apresenta anomalias para uma determinada tabela (a guia **geral** inclui uma coleção de tabelas). As anomalias são calculadas usando a função **series_decompose_anomalies ()** que retorna uma **Pontuação de anomalias**. [Saiba mais sobre essa função](/azure/data-explorer/kusto/query/series-decompose-anomaliesfunction?WT.mc_id=Portal-fx). Defina os seguintes parâmetros para a função a ser avaliada:

    - **AnomaliesTimeRange**: este seletor de tempo aplica-se somente à exibição de anomalias de coleta de dados.
    - **SampleInterval**: o intervalo de tempo no qual os dados são amostrados no intervalo de tempo determinado. A pontuação de anomalias é calculada somente nos dados do último intervalo.
    - **PositiveAlertThreshold**: este valor define o limite positivo da pontuação de anomalias. Ele aceita valores decimais.
    - **NegativeAlertThreshold**: este valor define o limite negativo da pontuação de anomalias. Ele aceita valores decimais.

        :::image type="content" source="media/monitor-data-connector-health/data-health-workbook-2.png" alt-text="página anomalias da pasta de trabalho de monitoramento de integridade do conector de dados" lightbox="media/monitor-data-connector-health/data-health-workbook-2.png":::

1. A guia **informações do agente** mostra informações sobre a integridade dos agentes de log Analytics instalados em seus vários computadores, se a VM do Azure, outra VM de nuvem, VM local ou física. Você pode monitorar o seguinte:

   - Local do sistema

   - Latência e status da pulsação

   - Memória disponível e espaço em disco

   - Operações do agente

    Nesta seção, você deve selecionar a guia que descreve o ambiente de suas máquinas: escolha a guia **computadores gerenciados pelo Azure** se desejar exibir apenas os computadores gerenciados por arco do Azure; escolha a guia **todos os computadores** para exibir computadores gerenciados e não Azure com o agente de log Analytics instalado.

    :::image type="content" source="media/monitor-data-connector-health/data-health-workbook-3.png" alt-text="página informações do agente de pasta de trabalho monitoramento de integridade do conector de dados" lightbox="media/monitor-data-connector-health/data-health-workbook-3.png":::

## <a name="next-steps"></a>Próximas etapas
Saiba como integrar [seus dados ao Azure Sentinel](quickstart-onboard.md), [conectar fontes de dados](connect-data-sources.md)e [obter visibilidade de seus dados e ameaças em potencial](quickstart-get-visibility.md).