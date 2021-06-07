---
title: Gerenciar eventos de alerta
description: Gerenciar eventos de alerta detectados em sua rede.
ms.date: 12/07/2020
ms.topic: how-to
ms.openlocfilehash: 2995ff0d2246929efb534bc21d888bad3a2cf24a
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104781782"
---
# <a name="manage-alert-events"></a>Gerenciar eventos de alerta

As opções a seguir estão disponíveis para o gerenciamento de eventos de alerta:

 | Ação | Descrição |
 |--|--|
 | **Learn** | Autorize o evento detectado. Para obter mais informações, consulte [sobre como aprender e desaprender eventos](#about-learning-and-unlearning-events). |
 | **Reconhecer** | Oculte o alerta uma vez para o evento detectado. O alerta será disparado novamente se o evento for detectado novamente. Para obter mais informações, consulte [sobre como confirmar e não confirmar eventos](#about-acknowledging-and-unacknowledging-events). |
 | **Mute** | Ignore continuamente a atividade com dispositivos idênticos e tráfego comparável. Para obter mais informações, consulte [about mudo e unmudo de eventos](#about-muting-and-unmuting-events). |
 
Você também pode exportar informações de alerta.
## <a name="about-learning-and-unlearning-events"></a>Sobre aprendizagem e desaprendizado de eventos

Eventos que indicam desvios da rede aprendida podem refletir alterações de rede válidas. Exemplos podem incluir um novo dispositivo autorizado que ingressou na rede ou uma atualização de firmware autorizado.

Quando você seleciona **aprender**, o sensor considera o tráfego, as configurações ou a atividade válida. Isso significa que os alertas não serão mais disparados para o evento. Isso também significa que o evento não será calculado quando o sensor gerar avaliação de risco, vetor de ataque e outros relatórios.

Por exemplo, você recebe um alerta que indica a atividade de verificação de endereço em um dispositivo que um scanner de rede não definiu anteriormente. Se esse dispositivo foi adicionado à rede para fins de verificação, você pode instruir o sensor para aprender o dispositivo como um dispositivo de verificação.

:::image type="content" source="media/how-to-work-with-alerts-sensor/detected.png" alt-text="A janela de verificação endereço detectado.":::

Eventos aprendidos podem não ser aprendidos. Quando o sensor não aprende eventos, ele disparará novamente os alertas relacionados a esse evento.

## <a name="about-acknowledging-and-unacknowledging-events"></a>Sobre a confirmação e a reconfirmação de eventos

Em determinadas situações, talvez você não queira que um sensor Aprenda um evento detectado ou a opção pode não estar disponível. Em vez disso, o incidente pode exigir mitigação. Por exemplo:

- **Mitigar uma configuração de rede ou dispositivo**: você recebe um alerta indicando que um novo dispositivo foi detectado na rede. Ao investigar, você descobre que o dispositivo é um dispositivo de rede não autorizado. Você lida com o incidente desconectando o dispositivo da rede.
- **Atualizar uma configuração de sensor**: você recebe um alerta indicando que um servidor iniciou um número excessivo de conexões remotas. Este alerta foi disparado porque os limites de anomalias do sensor foram definidos para disparar alertas acima de um determinado número de sessões em um minuto. Você lida com o incidente atualizando os limites.

Depois de realizar a mitigação ou investigação, você pode instruir o sensor a ocultar o alerta selecionando **confirmar**. Se o evento for detectado novamente, o alerta será disparado novamente.

Para limpar o alerta:

  - Selecione **reconhecer**.

Para exibir o alerta novamente:

  - Acesse o alerta e selecione não **confirmar**.

Não confirme os alertas se uma investigação adicional for necessária.

## <a name="about-muting-and-unmuting-events"></a>Sobre mudo e desativação de eventos

Em determinadas circunstâncias, talvez você queira instruir seu sensor a ignorar um cenário específico em sua rede. Por exemplo:

  - O mecanismo de **anomalias** dispara um alerta em um pico na largura de banda entre dois dispositivos, mas o pico é válido para esses dispositivos.

  - O mecanismo de **violação de protocolo** dispara um alerta em um desvio de protocolo detectado entre dois dispositivos, mas o desvio é válido entre os dispositivos.

Nessas situações, o aprendizado não está disponível. Quando o aprendizado não puder ser executado e você quiser suprimir o alerta e remover o dispositivo ao calcular os riscos e os vetores de ataque, você poderá ativar mudo do evento de alerta em vez disso.

> [!NOTE] 
> Não é possível ativar mudo de eventos nos quais um dispositivo de Internet é definido como a origem ou o destino.

### <a name="what-alert-activity-is-muted"></a>Qual atividade de alerta está sem áudio?

Um cenário sem áudio inclui os dispositivos de rede e o tráfego detectado para um evento. O título do alerta descreve o tráfego que está sendo mudo.

O dispositivo ou dispositivos com mudo de áudio serão exibidos como uma imagem no alerta. Se dois dispositivos forem mostrados, o tráfego de alerta específico entre eles será mudo.

**Exemplo 1**

Quando um evento está mudo, ele é ignorado sempre que o primário (origem) envia o secundário (destino) um código de função ilegal, conforme definido pelo fornecedor.

:::image type="content" source="media/how-to-work-with-alerts-sensor/secondary-device-connected.png" alt-text="Dispositivo secundário recebido.":::

**Exemplo 2**

Quando um evento está mudo, ele é ignorado sempre que a origem envia um cabeçalho HTTP com conteúdo ilegal, independentemente do destino.

:::image type="content" source="media/how-to-work-with-alerts-sensor/illegal-http-header-content.png" alt-text="Captura de tela de conteúdo de cabeçalho HTTP ilegal.":::

**Após o mudo de um evento:**

- O alerta estará acessível no modo de exibição de alerta **confirmado** até que ele esteja mudo.

- A ação de mudo aparecerá na **linha do tempo do evento**.

  :::image type="content" source="media/how-to-work-with-alerts-sensor/muted-event-notification-screenshot.png" alt-text="Evento detectado e mudo.":::

- O sensor recalculará os dispositivos durante a geração de avaliação de risco, vetor de ataque e outros relatórios. Por exemplo, se você ativou mudo de um alerta que detectou tráfego mal-intencionado em um dispositivo, esse dispositivo não será calculado no relatório de avaliação de risco.

**Para ativar mudo e desativar um alerta:**

- Selecione o ícone **sem som** no alerta.

**Para exibir alertas sem áudio:**

1. Selecione a opção **confirmada** formulário a tela principal de **alertas** .

2. Focalize um alerta para ver se ele está mudo.  

## <a name="export-alert-information"></a>Exportar informações de alerta

Exportar informações de alerta para um arquivo. csv. Você pode exportar informações de todos os alertas detectados ou exportar informações com base na exibição filtrada. As seguintes informações são exportadas:

- Endereço de origem
- Endereço de destino
- Título do alerta
- Severidade do alerta
- Mensagem de alerta
- Informações adicionais
- Status confirmado
- Disponibilidade do PCAP

Para exportar:

1. Selecione alertas no menu lateral.
1. Selecione Exportar.
1. Selecione exportar alertas estendidos para exportar informações de alerta em linhas separadas para cada alerta que abrange vários dispositivos. Quando exportar alertas estendidos estiver selecionado, o arquivo. csv criará uma linha duplicada do evento de alerta com os itens exclusivos em cada linha. Usar essa opção torna mais fácil investigar eventos de alerta exportados.

## <a name="see-also"></a>Veja também

[Controlar qual tráfego é monitorado](how-to-control-what-traffic-is-monitored.md)
