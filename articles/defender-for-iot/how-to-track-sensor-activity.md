---
title: Controlar atividade do sensor
description: A linha do tempo de evento apresenta uma linha do tempo de atividade detectada em sua rede, incluindo alertas e ações de gerenciamento de alertas, eventos de rede e operações de usuário, como entrada de usuário e exclusão de usuários.
ms.date: 12/10/2020
ms.topic: article
ms.openlocfilehash: 195908001fbd247ed2e0fa007bc8dcd5ebf28e60
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104781612"
---
# <a name="track-sensor-activity"></a>Controlar atividade do sensor

## <a name="event-timeline"></a>Linha do tempo do evento

A linha do tempo do evento apresenta uma linha do tempo de atividade que seu sensor detectou. Por exemplo:

  - Alertas e ações de gerenciamento de alertas

  - Eventos de rede

  - Operações de usuário, como entrada de usuário e exclusão de usuário

A linha do tempo do evento fornece uma exibição cronológica dos eventos que ocorreram na rede. A linha do tempo do evento permite compreender e analisar a cadeia de eventos que precederam e seguiu um ataque ou incidente, que ajuda na investigação e na análise forense.

> [!NOTE]
> *Os administradores* e *analistas de segurança* podem executar os procedimentos descritos nesta seção.

Para exibir os logs de eventos:

- No menu lateral, selecione **linha do tempo de evento**.

   :::image type="content" source="media/how-to-track-sensor-activity/event-timeline.png" alt-text="Exiba seus eventos na linha do tempo do evento.":::

Além de exibir os eventos detectados pelo sensor, você pode adicionar eventos manualmente à linha do tempo. Esse processo é útil se o evento ocorreu em um sistema externo, mas tem um impacto na sua rede, e é importante registrar o evento e apresentá-lo como parte da linha do tempo.

Para adicionar eventos manualmente:

- Selecione **criar evento**.

Para exportar informações do log de eventos para um arquivo CSV:

- Selecione **Exportar**.

## <a name="filter-the-event-timeline"></a>Filtrar a linha do tempo do evento

Filtre a linha do tempo para exibir dispositivos e eventos de seu interesse.

Para filtrar a linha do tempo:

1. Selecione **filtros avançados**.

   :::image type="content" source="media/how-to-track-sensor-activity/advance-filters.png" alt-text="Use a janela filtros avançados de eventos para filtrar seus eventos.":::

2. Defina filtros de eventos, da seguinte maneira:

   - **Incluir endereço**: exibe eventos para dispositivos específicos.

   - **Excluir endereço**: Ocultar eventos para dispositivos específicos.

   - **Incluir tipos de evento**: Exibir tipos de eventos específicos.

   - **Excluir tipos de evento**: Ocultar tipos de eventos específicos.

   - **Grupo de dispositivos**: selecione um grupo de dispositivos, como ele foi definido no mapa do dispositivo. Somente os eventos desse grupo são apresentados.

3. Selecione **limpar tudo** para limpar todos os filtros selecionados.

4. Pesquisar por **alertas somente**, **alertas e avisos** ou **todos os eventos**.

5. Selecione **selecionar data** para escolher uma data específica. Escolha um dia, uma hora e um minuto. Os eventos do período de tempo selecionado são mostrados.

6.  Selecione **operações de usuário** para incluir ou excluir eventos de operação do usuário.

7.  Selecione a seta (**V**) para exibir mais informações sobre o evento:

    - Selecione os alertas relacionados (se houver) para exibir uma descrição detalhada do alerta.

    - Selecione o dispositivo para exibir o dispositivo no mapa.

    - Selecione **filtrar eventos por dispositivos relacionados** se você quiser filtrar por dispositivos relacionados.

    - Selecione o **arquivo pcap** para baixar o arquivo pcap (se ele existir) que contém uma captura de pacote de toda a rede em um momento específico. 
    
      O arquivo PCAP contém informações técnicas que podem ajudar os engenheiros de rede a determinar os parâmetros exatos do evento. Você pode analisar o arquivo PCAP com um analisador de protocolo de rede, como o Wireshark, um aplicativo de software livre.

## <a name="see-also"></a>Veja também

[Exibir alertas](how-to-view-alerts.md)
