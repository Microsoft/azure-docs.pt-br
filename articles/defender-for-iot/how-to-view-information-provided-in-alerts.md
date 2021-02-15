---
title: Exibir informações em alertas
description: Selecione um alerta na janela alertas para examinar os detalhes.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/03/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 5877db6b3bc7366f28e679882a2c784e6828b1c1
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/15/2021
ms.locfileid: "100523576"
---
# <a name="view-information-in-alerts"></a>Exibir informações em alertas

Selecione um alerta na janela **alertas** para examinar os detalhes do alerta. Os detalhes incluem as seguintes informações:

- Metadados de alerta

- Informações sobre o tráfego, dispositivos e o evento

- Links para dispositivos conectados no mapa do dispositivo

- Comentários definidos por analistas e administradores de segurança

- Recomendações para investigar o evento

## <a name="alert-metadata"></a>Metadados de alerta

Os detalhes do alerta incluem os seguintes metadados de alerta:

  - ID do Alerta

  - Mecanismo de política que disparou o alerta

  - Data e hora em que o alerta foi disparado

:::image type="content" source="media/how-to-work-with-alerts-sensor/internet-connectivity-detection-unauthorized.png" alt-text="Conectividade de Internet não autorizada detectada.":::

## <a name="information-about-devices-traffic-and-the-event"></a>Informações sobre dispositivos, tráfego e o evento

A mensagem de alerta fornece informações sobre:

  - Os dispositivos detectados.

  - O tráfego detectado entre os dispositivos, como protocolos e códigos de função.

  - Informações sobre as implicações do evento.

Você pode usar essas informações ao decidir como gerenciar o evento de alerta.

## <a name="links-to-connected-devices-in-the-device-map"></a>Links para dispositivos conectados no mapa do dispositivo

Para saber mais sobre os dispositivos conectados aos dispositivos detectados, você pode selecionar uma imagem de dispositivo no alerta e exibir dispositivos conectados no mapa.

:::image type="content" source="media/how-to-work-with-alerts-sensor/rcp-operation-failed.png" alt-text="Falha na operação de RCP.":::

:::image type="content" source="media/how-to-work-with-alerts-sensor/devices-screen-populated.png" alt-text="Captura de tela de dispositivos.":::

O mapa filtra para o dispositivo que você selecionou e outros dispositivos conectados a ele. O mapa também exibe a caixa de diálogo **Propriedades rápidas** para os dispositivos detectados nos alertas.

## <a name="comments-defined-by-security-analysts-and-administrators"></a>Comentários definidos por analistas e administradores de segurança 

Os alertas podem incluir uma lista de comentários predefinidos. Por exemplo, os comentários podem ser instruções para que as ações de mitigação tenham ou nomes de indivíduos entrem em contato com o evento.

Quando você estiver gerenciando um evento de alerta, poderá escolher o comentário ou os comentários que melhor refletem o status do evento ou as etapas que você executou para investigar o alerta.

Os comentários selecionados são salvos na mensagem de alerta. Trabalhar com comentários aprimora a comunicação entre indivíduos e equipes durante a investigação de um evento de alerta. Como resultado, os comentários podem acelerar o tempo de resposta a incidentes.

Um administrador ou analista de segurança predefine comentários. Os comentários selecionados não são encaminhados aos sistemas de parceiros definidos nas regras de encaminhamento.

Depois de examinar as informações em um alerta, você pode realizar várias etapas forenses para orientá-lo no gerenciamento do evento de alerta. Por exemplo:

- Analisar atividade recente do dispositivo (relatório de mineração de dados). 

- Analise outros eventos que ocorreram ao mesmo tempo (linha do tempo do evento). 

- Analise o tráfego de eventos abrangente (arquivo PCAP).

## <a name="pcap-files"></a>Arquivos PCAP

Em alguns casos, você pode acessar um arquivo PCAP da mensagem de alerta. Isso pode ser útil se você quiser informações mais detalhadas sobre o tráfego de rede associado.

Para baixar um arquivo PCAP, selecione :::image type="content" source="media/how-to-work-with-alerts-sensor/download-pcap.png" alt-text="ícone de download."::: no canto superior direito da caixa de diálogo **detalhes do alerta** .

## <a name="recommendations-for-investigating-an-event"></a>Recomendações para investigar um evento 

A área de **recomendação** de um alerta exibe informações que podem ajudá-lo a entender melhor um evento. Examine essas informações antes de gerenciar o evento de alerta ou executar uma ação no dispositivo ou na rede.

## <a name="next-steps"></a>Próximas etapas

[Acelerar fluxos de trabalho de alerta](how-to-accelerate-alert-incident-response.md)

[Gerenciar o evento de alertas](how-to-manage-the-alert-event.md)
