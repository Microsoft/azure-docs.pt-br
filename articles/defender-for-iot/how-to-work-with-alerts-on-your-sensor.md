---
title: Trabalhar com alertas em seu sensor
description: Trabalhe com alertas para ajudá-lo a aprimorar a segurança e a operação da sua rede.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 11/30/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 00207ffb8480ae99c2f1aad74183fca9ea45ee17
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/31/2020
ms.locfileid: "97837841"
---
# <a name="work-with-alerts-on-your-sensor"></a>Trabalhar com alertas em seu sensor

Trabalhe com alertas para ajudá-lo a aprimorar a segurança e a operação da sua rede. Os alertas fornecem informações sobre:

- Desvios da atividade de rede autorizada

- Anomalias operacionais e de protocolo

- Tráfego de malware suspeito

:::image type="content" source="media/how-to-work-with-alerts-sensor/address-scan-detected-screen.png" alt-text="Detectar verificação de endereço.":::

As opções de gerenciamento de alertas permitem que os usuários:

- Instrua os sensores para aprender a atividade detectada como tráfego autorizado.

- Confirme a revisão do alerta.

- Instrua sensores a eventos de mudo detectados com dispositivos idênticos e tráfego comparável.

Ferramentas adicionais estão disponíveis para ajudá-lo a aprimorar e agilizar a investigação do alerta. Por exemplo:

  - Adicione comentários instrutivos para revisores de alerta.

  - Crie grupos de alertas para exibição em soluções SOC. 

  - Pesquisar alertas específicos; examinar arquivos PCAP relacionados; exiba os dispositivos detectados e outros dispositivos conectados no mapa do dispositivo ou envie detalhes de alerta para sistemas de parceiros.

  - Encaminhe alertas para fornecedores de parceiros: sistemas SIEM, sistemas MSSP e muito mais.

## <a name="alerts-and-engines"></a>Alertas e mecanismos

Os alertas são disparados quando os mecanismos de sensor detectam alterações no tráfego de rede e no comportamento que precisam de sua atenção. Este artigo descreve o tipo de alerta que cada mecanismo dispara.

| Tipo de alerta | Descrição |
|-|-|
| Alertas de violação de política | Disparado quando o mecanismo de violação de política detecta um desvio do tráfego aprendido anteriormente. Por exemplo: <br /> -Um novo dispositivo é detectado.  <br /> -Uma nova configuração é detectada em um dispositivo. <br /> -Um dispositivo não definido como um dispositivo de programação realiza uma alteração de programação. <br /> -Uma versão de firmware foi alterada. |
| Alertas de violação de protocolo | Disparado quando o mecanismo de violação de protocolo detecta estruturas de pacote ou valores de campo que não estão em conformidade com a especificação de protocolo. | 
| Alertas operacionais | Disparado quando o mecanismo operacional detecta incidentes operacionais de rede ou um dispositivo com mau funcionamento. Por exemplo, um dispositivo de rede foi interrompido por meio de um comando Stop PLC ou uma interface em um sensor interrompeu o tráfego de monitoramento. |
| Alertas de malware | Disparado quando o mecanismo de malware detecta uma atividade de rede mal-intencionada. Por exemplo, o mecanismo detecta um ataque conhecido como Conficker. |
| Alertas de anomalias | Disparado quando o mecanismo de anomalias detecta um desvio. Por exemplo, um dispositivo está executando verificações de rede, mas não está definido como um dispositivo de verificação. |

As ferramentas estão disponíveis para habilitar e desabilitar mecanismos de sensor. Os alertas não são disparados de mecanismos que estão desabilitados. Consulte [controlar qual tráfego é monitorado](how-to-control-what-traffic-is-monitored.md).

## <a name="alerts-and-sensor-reporting"></a>Alertas e relatórios de sensor

A atividade refletida em alertas pode ser calculada quando você está gerando mineração de dados, avaliação de risco e relatórios de vetor de ataque. Quando você gerencia esses eventos, o sensor atualiza os relatórios de acordo.

Por exemplo:

  - A conectividade não autorizada entre um dispositivo em uma sub-rede definida e dispositivos localizados fora da sub-rede (pública) aparecerão no relatório de *atividades da Internet* de mineração de dados e na seção *conexões de Internet* de avaliação de risco. Depois que esses dispositivos são autorizados (aprendidos), eles são calculados para gerar esses relatórios.

  - Os eventos de malware detectados em dispositivos de rede são relatados em relatórios de avaliação de risco. Quando os alertas sobre eventos de malware estiverem *sem áudio*, os dispositivos afetados não serão calculados no relatório de avaliação de risco.

## <a name="see-also"></a>Consulte também

- [Aprendizado e modos inteligentes de aprendizado de ti](how-to-control-what-traffic-is-monitored.md#learning-and-smart-it-learning-modes)
- [Exibir informações fornecidas em alertas](how-to-view-information-provided-in-alerts.md)
- [Gerenciar o evento de alerta](how-to-manage-the-alert-event.md)
- [Acelerar fluxos de trabalho de alerta](how-to-accelerate-alert-incident-response.md)
