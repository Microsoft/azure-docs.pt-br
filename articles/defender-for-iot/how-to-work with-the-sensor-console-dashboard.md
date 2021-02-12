---
title: Trabalhar com o painel do console do sensor
description: O painel permite exibir rapidamente o status de segurança da rede. Ele fornece uma visão geral de alto nível das ameaças ao seu sistema inteiro em uma linha do tempo, juntamente com informações sobre dispositivos relacionados.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 11/03/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: c9afc22cd123a782c9ee0247952c78c72ee916c0
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509060"
---
# <a name="the-dashboard"></a>O painel

O painel permite exibir rapidamente o status de segurança da rede. Ele fornece uma visão geral de alto nível das ameaças ao seu sistema inteiro em uma linha do tempo, juntamente com informações sobre dispositivos relacionados, incluindo:

- Alertas em diferentes níveis de severidade:

- Crítico

- Principal

- Secundária

- Warnings

- Os dois indicadores no centro da página mostram os pacotes por segundo (PPS) e os alertas não confirmados (UA). **PPS** é o número de pacotes confirmados pelo sistema por segundo. **UA** é o número de alertas que ainda não foram confirmados.

- Lista de alertas não confirmados com sua descrição.

- Linha do tempo com a descrição do alerta.

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/dashboard-alert-screen-v2.png" alt-text="Painel":::

## <a name="viewing-the-latest-alerts"></a>Exibindo os alertas mais recentes

O medidor de alertas não confirmados (UA) no centro da página indica o número de tais alertas. Para exibir uma lista de alertas, selecione **mais alertas** na parte inferior da página painel ou selecione **alertas** no menu lateral.

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/unhandled-alerts-list.png" alt-text="Alertas não confirmados":::

## <a name="status-boxes"></a>Caixas de status

Cada caixa de status é descrita nesta seção.

| Caixa de status e medidores | Descrição |
| -------------- | -------------- |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/critical-alert-status-box-v2.png" alt-text="Alertas críticos"::: | **Alertas críticos** – a caixa na parte superior da página indica o número de alertas críticos. Selecione esta caixa para exibir descrições dos alertas na linha do tempo e na lista sob os medidores, se houver.                              |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/major-alert-status-box-v2.png" alt-text="Principais alertas"::: | **Alertas principais** – a caixa na parte superior direita da página indica o número de alertas principais. Selecione esta caixa para exibir descrições dos alertas na linha do tempo e na lista sob os medidores, se houver.                                     |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/minor-alert-status-box-v2.png" alt-text="Alertas secundários"::: | **Alertas secundários** – a caixa na parte inferior esquerda da página indica o número de alertas secundários. Selecione esta caixa para exibir descrições dos alertas na linha do tempo e na lista sob os medidores, se houver.                                   |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/warnings-alert-status-box-v2.png" alt-text="Alertas de aviso"::: | **Alertas de aviso** -a caixa no meio inferior da página indica o número de alertas de aviso. Selecione esta caixa para exibir descrições dos alertas na linha do tempo e na lista sob os medidores, se houver.                             |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/all-alert-status-box-v2.png" alt-text="Todos os Alertas"::: | **Todos os alertas** – a caixa na parte inferior direita da página indica o número total de alertas críticos, principais, secundários e de aviso. Selecione esta caixa para exibir descrições dos alertas na linha do tempo e na lista sob os medidores, se houver. |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/packets-per-second-gauge-v2.png" alt-text="Pacotes por segundo"::: | **Pacotes por segundo (PPS)** – a métrica do PPS é um indicador do desempenho da rede. |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/unacknowledged-events-gauge-v2.png" alt-text="Eventos não confirmados (UA)"::: | **Eventos não confirmados** – essa métrica indica o número de eventos não confirmados.

## <a name="using-the-timeline"></a>Usando a linha do tempo

Os alertas são exibidos ao longo de uma linha do tempo vertical que inclui informações de data e hora.

A linha do tempo exibe graficamente:

- Alertas críticos

- Principais alertas

- Alertas secundários

- Alertas de aviso

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/timeline-of-events.png" alt-text="Gráfico de linha do tempo":::

## <a name="viewing-alerts"></a>Exibindo alertas

Selecione a seta para baixo **V** na parte inferior de uma caixa de alerta para exibir as informações de entrada e dispositivos de alerta.

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/extended-alert-screen.png" alt-text="Informações de entrada e dispositivos de alerta":::

- Selecione o dispositivo para exibir o mapa do modo físico. Os dispositivos sujeitos ao sujeito são realçados.

- Clique em qualquer lugar na caixa de alerta para exibir detalhes adicionais sobre o alerta. Um pop-up será exibido semelhante ao mostrado abaixo

- Selecione :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/excel-icon.png" alt-text="Excel"::: para exportar um arquivo CSV sobre o alerta.

- Somente administradores e analistas de segurança — selecione :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/approve-all-icon.png" alt-text="reconhecer tudo"::: para **confirmar todos os** alertas associados.

- Selecione :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/pdf-icon.png" alt-text="PDF":::para baixar um relatório de alerta como um arquivo PDF.

- Selecione :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/pin-icon.png" alt-text="fixar":::para fixar ou desafixar o alerta. A seleção do PIN o adicionará à janela **alertas fixados** na tela **alertas** .

- Selecione :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/download-icon.png" alt-text="baixar"::: para investigar o alerta baixando o arquivo pcap relacionado que contém uma análise de protocolo de rede.

- Selecione :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/cloud-download-icon.png" alt-text="nuvem"::: para baixar um arquivo pcap filtrado relacionado que contenha apenas os pacotes relevantes de alerta, reduzindo assim o tamanho do arquivo de saída e permitindo uma análise mais focalizada. Você pode exibi-lo usando o [Wireshark](https://www.wireshark.org/).

- Selecione :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/navigate-icon.png" alt-text="navegação"::: para navegar até a linha do tempo do evento no momento do alerta solicitado. Isso permite que você avalie outros eventos que podem estar ocorrendo em um alerta específico.

- Somente administradores e analistas de segurança – altere o status do alerta de não confirmado para confirmado. Selecione aprender para aprovar a atividade detectada.

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/unauthorized-internet-connectivity-detection-v3.png" alt-text="Conectividade de Internet não autorizada detectada":::

## <a name="see-also"></a>Consulte também

[Trabalhar com alertas em seu sensor](how-to-work-with-alerts-on-your-sensor.md)
