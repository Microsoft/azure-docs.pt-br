---
title: Exibir alertas
description: Exiba alertas de acordo com várias categorias e use os recursos de pesquisa para ajudá-lo a encontrar alertas de interesse.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/02/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 4803691a82a97cd2be5fa3beafd4419010e7a9c9
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/31/2020
ms.locfileid: "97837868"
---
# <a name="view-alerts"></a>Exibir alertas

Este artigo descreve como exibir alertas disparados por seu sensor e gerenciá-los com as ferramentas de alerta.

Você pode exibir alertas com base em várias categorias, como alertas que foram arquivados ou fixados. Ou você pode procurar alertas de interesse, como alertas com base em um endereço IP ou MAC.  

Você também pode exibir alertas no painel do sensor.

Para exibir alertas:

- Selecione **alertas** no menu lateral. A janela **alertas** exibe os alertas detectados pelo sensor.

  :::image type="content" source="media/how-to-work-with-alerts-sensor/alerts-screen.png" alt-text="Exibição da tela de alertas.":::

## <a name="view-alerts-by-category"></a>Exibir alertas por categoria

Você pode exibir alertas de acordo com várias categorias da exibição principal de **alertas** . Selecione um alerta para examinar os detalhes e gerenciar o evento.

| Classificar por tipo | Descrição |
|--|--|
| **Alertas importantes** | Alertas classificados por importância. |
| **Alertas fixados** | Alertas que o usuário fixa para investigação adicional. Os alertas fixados não são arquivados e são armazenados por 14 dias na pasta fixa. |
| **Alertas recentes** | Alertas classificados por tempo. |
| **Alertas reconhecidos** | Alertas que foram confirmados e sem tratamento, ou que estavam com mudo e mudo. |
| **Alertas arquivados** | Alerta que o sistema arquivou automaticamente. Somente o usuário administrador pode acessá-los. |

## <a name="search-for-alerts-of-interest"></a>Pesquisar por alertas de interesse

A exibição principal de **alertas** fornece vários recursos de pesquisa para ajudá-lo a encontrar alertas de interesse.

:::image type="content" source="media/how-to-work-with-alerts-sensor/main-alerts-view.png" alt-text="Captura de tela de aprendizado de alertas.":::

### <a name="text-search"></a>Pesquisa de texto 

Use a opção **pesquisa gratuita** para procurar alertas por texto, números ou caracteres.

Para Pesquisar:

- Digite o texto necessário no campo **pesquisa livre** e pressione Enter no teclado.

Para limpar a pesquisa:

- Exclua o texto no campo **pesquisa livre** e pressione Enter no teclado.

### <a name="device-group-or-device-ip-address-search"></a>Pesquisa de endereço IP do dispositivo ou do grupo de dispositivos

Procure alertas que referenciem endereços IP ou grupos de dispositivos específicos. Os grupos de dispositivos são criados no mapa do dispositivo.

Para usar filtros avançados:

1. Selecione **filtros avançados** na exibição principal **alertas** .

2. Escolha um grupo de dispositivos ou um dispositivo.

3. Selecione **Confirmar**.

4. Para limpar a pesquisa, selecione **limpar tudo**.

### <a name="security-versus-operational-alert-search"></a>Segurança versus pesquisa de alertas operacionais

Alternar entre a exibição de alertas operacionais e de segurança:

- Os alertas de **segurança** representam o potencial tráfego de malware, anomalias de rede, violações de política e violações de protocolo.

- Os alertas **operacionais** representam anomalias de rede, violações de política e violações de protocolo.

Quando nenhuma das opções é selecionada, todos os alertas são exibidos.

:::image type="content" source="media/how-to-work-with-alerts-sensor/alerts-security.png" alt-text="Segurança na tela alertas.":::

## <a name="alert-window-options"></a>Opções da janela de alerta

As mensagens de alerta fornecem as seguintes ações:

- Selecione :::image type="icon" source="media/how-to-work-with-alerts-sensor/acknowledge-an-alert-icon.png" border="false"::: para confirmar um alerta.

- Selecione :::image type="icon" source="media/how-to-work-with-alerts-sensor/unacknowledge-an-alert-icon.png" border="false"::: para cancelar a confirmação de um alerta.

- Selecione :::image type="icon" source="media/how-to-work-with-alerts-sensor/pin-an-alert-icon.png" border="false"::: para fixar um alerta.

- Selecione :::image type="icon" source="media/how-to-work-with-alerts-sensor/unpin-an-alert-icon.png" border="false"::: para Desafixar um alerta.

- Selecione :::image type="icon" source="media/how-to-work-with-alerts-sensor/acknowledge-all-alerts-icon.png" border="false"::: para confirmar todos os alertas.

- Selecione :::image type="icon" source="media/how-to-work-with-alerts-sensor/learn-and-acknowledge-all-alerts.png" border="false"::: para aprender e reconhecer todos os alertas.

- Selecione :::image type="icon" source="media/how-to-work-with-alerts-sensor/export-to-csv.png" border="false"::: para exportar a lista de alertas para um arquivo CSV e selecione a opção Exportar. Escolha **exportação de alertas** para a opção regular exportar para CSV. Ou escolha **exportação de alerta estendida** para a possibilidade de adicionar linhas separadas para obter informações adicionais sobre um alerta no arquivo CSV.

- Selecione o :::image type="icon" source="media/how-to-work-with-alerts-sensor/export-to-pdf.png" border="false"::: ícone para baixar um relatório de alerta como um arquivo PDF.

- Selecione o :::image type="icon" source="media/how-to-work-with-alerts-sensor/download-pcap.png" border="false"::: ícone para baixar o arquivo pcap. O arquivo é visível com o Wireshark, o analisador de protocolo de rede gratuito.

- Selecione :::image type="icon" source="media/how-to-work-with-alerts-sensor/download-filtered-pcap.png" border="false"::: para baixar um arquivo pcap filtrado que contenha apenas os pacotes de alerta relevantes, reduzindo assim o tamanho do arquivo de saída e permitindo uma análise mais focalizada. Você pode exibir o arquivo usando o Wireshark.

- Selecione o :::image type="icon" source="media/how-to-work-with-alerts-sensor/show-alert-in-timeline.png" border="false"::: ícone para mostrar o alerta na linha do tempo do evento.

- Selecione o :::image type="icon" source="media/how-to-work-with-alerts-sensor/pin-an-alert-icon.png" border="false"::: ícone para fixar o alerta.

- Selecione o :::image type="icon" source="media/how-to-work-with-alerts-sensor/unpin-an-alert-icon.png" border="false"::: ícone para desafixar o alerta.

- Selecione :::image type="icon" source="media/how-to-work-with-alerts-sensor/learn-icon.png" border="false"::: para aprovar o tráfego (somente analistas e administradores de segurança).

- Selecione um dispositivo para exibi-lo no mapa do dispositivo.

## <a name="next-steps"></a>Próximas etapas

[Gerenciar o evento de alerta](how-to-manage-the-alert-event.md)

[Acelerar fluxos de trabalho de alerta](how-to-accelerate-alert-incident-response.md)
