---
title: Configurar uma porta de sinal para gravação de vídeo baseada em evento-Azure
description: Este artigo fornece orientação sobre como configurar uma entrada de sinal em um grafo de mídia.
ms.topic: how-to
ms.date: 11/3/2020
ms.openlocfilehash: afcec7c03f1353f08b58311278f5a533e0c911bc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94410786"
---
# <a name="configure-a-signal-gate-for-event-based-video-recording"></a>Configurar uma porta de sinal para gravação de vídeo baseada em evento

Em um grafo de mídia, um [nó de processador da porta de sinal](media-graph-concept.md#signal-gate-processor) permite que você encaminhe a mídia de um nó para outro quando a porta é disparada por um evento. Quando é disparado, o portão é aberto e permite o fluxo de mídia por uma duração especificada. Na ausência de eventos para disparar a porta, o portão fecha e a mídia para de fluir. Você pode usar o processador da porta do sinal para a gravação de vídeo baseada em evento.

Neste artigo, você aprenderá a configurar um processador de porta de sinal.

## <a name="suggested-prereading"></a>Sugestão de leitura sugerida
-   [Grafo de mídia](media-graph-concept.md)
-   [Gravação de vídeo baseada em eventos](event-based-video-recording-concept.md)


## <a name="problem"></a>Problema
Um usuário pode querer iniciar a gravação em um momento específico antes ou depois que a porta é disparada por um evento. O usuário sabe a latência aceitável em seu sistema. Portanto, eles querem especificar a latência do processador da porta do sinal. Eles também desejam especificar a duração mínima e máxima de sua gravação, independentemente de quantos novos eventos forem recebidos.
 
### <a name="use-case-scenario"></a>Cenário do caso de uso
Suponha que você deseja gravar o vídeo sempre que a porta da frente do seu prédio for aberta. Você deseja que a gravação: 

- Inclua os *X* segundos antes da abertura da porta. 
- Por último, no mínimo *Y* segundos, se a porta não estiver aberta novamente. 
- Por último, no máximo *Z* segundos, se a porta for aberta repetidamente. 
 
Você sabe que o sensor de porta tem uma latência de *K* segundos. Para reduzir a chance de eventos serem desconsiderados como entradas em atraso, você deseja permitir pelo menos *K* segundos para que os eventos cheguem.


## <a name="solution"></a>Solução

Para resolver o problema, modifique os parâmetros do processador da porta do sinal.

Para configurar um processador de porta de sinal, use estes quatro parâmetros:
- Janela de avaliação de ativação
- Deslocamento do sinal de ativação
- Janela de ativação mínima
- Janela de ativação máxima

Quando o processador de porta de sinal é disparado, ele permanece aberto para o tempo mínimo de ativação. O evento de ativação começa no carimbo de data/hora do evento mais antigo, além do deslocamento do sinal de ativação. 

Se o processador da porta do sinal for disparado novamente enquanto estiver aberto, o temporizador será redefinido e a porta permanecerá aberta por pelo menos o tempo mínimo de ativação. O processador da porta do sinal nunca permanece aberto além do tempo máximo de ativação. 

Um evento (evento 1) que ocorre antes de outro evento (evento 2), com base em carimbos de data/hora de mídia, poderia ser desconsiderado se o sistema for lento e o evento 1 chegar ao processador da porta do sinal após o evento 2. Se o evento 1 não chegar entre a chegada do evento 2 e a janela de avaliação de ativação, o evento 1 será ignorado. Ele não é passado pelo processador da porta do sinal. 

As IDs de correlação são definidas para cada evento. Essas IDs são definidas a partir do evento inicial. Eles são sequenciais para cada evento a seguir.

> [!IMPORTANT]
> O tempo de mídia é baseado no carimbo de data/hora de mídia de quando um evento ocorre na mídia. A sequência de eventos que chegam na porta de sinal pode não refletir a sequência de eventos que chegam no tempo de mídia.


### <a name="parameters-based-on-the-physical-time-that-events-arrive-at-the-signal-gate"></a>Parâmetros, com base no tempo físico que os eventos chegam na porta do sinal

* **minimumActivationTime (menor duração possível de uma gravação)**: o número mínimo de segundos que o processador da porta de sinal permanece aberto depois que ele é disparado para receber novos eventos, a menos que seja interrompido pelo maximumActivationTime.
* **maximumActivationTime (duração mais longa possível de uma gravação)**: o número máximo de segundos do evento inicial que o processador da porta do sinal permanece aberto depois de ser disparado para receber novos eventos, independentemente de quais eventos são recebidos.
* **activationSignalOffset**: o número de segundos entre a ativação do processador da porta do sinal e o início da gravação do vídeo. Normalmente, esse valor é negativo porque ele inicia a gravação antes do evento de disparo.
* **activationEvaluationWindow**: a partir do evento de disparo inicial, o número de segundos em que um evento ocorrido antes do evento inicial, em tempo de mídia, deve chegar ao processador da porta do sinal antes de ser desconsiderado e considerado uma chegada tardia.

> [!NOTE]
> Uma *chegada tardia* é qualquer evento que chega após a aprovação da janela de avaliação de ativação, mas que chega antes do evento inicial no tempo de mídia.

### <a name="limits-of-parameters"></a>Limites de parâmetros

* **activationEvaluationWindow**: 0 segundos a 10 segundos
* **activationSignalOffset**:-1 minuto a 1 minuto
* **minimumActivationTime**: 1 segundo a 1 hora
* **maximumActivationTime**: 1 segundo a 1 hora


No caso de uso, você definiria os parâmetros da seguinte maneira:

* **activationEvaluationWindow**: *K* segundos
* **activationSignalOffset**: *-X* segundos
* **minimumActivationWindow**: *Y* segundos
* **maximumActivationWindow**: *Z* segundos


Veja um exemplo de como a seção do nó do **processador da porta de sinal** indicaria uma topologia de gráfico de mídia para os seguintes valores de parâmetro:
* **activationEvaluationWindow**: 1 segundo
* **activationSignalOffset**:-5 segundos
* **minimumActivationTime**: 20 segundos
* **maximumActivationTime**: 40 segundos

> [!IMPORTANT]
> O [formato de duração ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations
) é esperado para cada valor de parâmetro. Por exemplo, PT1S = 1 segundo.


```
"processors":              
[
          {
            "@type": "#Microsoft.Media.MediaGraphSignalGateProcessor",
            "name": "signalGateProcessor",
            "inputs": [
              {
                "nodeName": "iotMessageSource"
              },
              {
                "nodeName": "rtspSource"
              }
            ],
            "activationEvaluationWindow": "PT1S",
            "activationSignalOffset": "-PT5S",
            "minimumActivationTime": "PT20S",
            "maximumActivationTime": "PT40S"
          }
]
```


Agora, considere como essa configuração do processador da porta de sinal se comportará em diferentes cenários de gravação.

### <a name="recording-scenarios"></a>Cenários de gravação

**Um evento de uma fonte (*ativação normal*)**

Um processador de porta de sinal que recebe um evento resulta em uma gravação que inicia 5 segundos (sinal de ativação = 5 segundos) antes que o evento chegue à porta. O restante da gravação é de 20 segundos (tempo mínimo de ativação = 20 segundos) porque nenhum outro evento chega antes do término do tempo mínimo de ativação para disparar novamente o portão.

Diagrama de exemplo:
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/normal-activation.svg" alt-text="Diagrama mostrando a ativação normal de um evento de uma fonte.":::

* Duração da gravação =-offset + minimumActivationTime = [E1 + deslocamento, E1 + minimumActivationTime]


**Dois eventos de uma fonte (*ativação reacionada*)**

Um processador de porta de sinal que recebe dois eventos resulta em uma gravação que inicia 5 segundos (deslocamento de sinal de ativação = 5 segundos) antes que o evento chegue à porta. Além disso, o evento 2 chega 5 segundos após o evento 1. Como o evento 2 chega antes do final do tempo mínimo de ativação do evento 1 (20 segundos), o portão é disparado novamente. O restante da gravação é de 20 segundos (tempo mínimo de ativação = 20 segundos) porque nenhum outro evento chega antes do término do tempo mínimo de ativação do evento 2 para disparar novamente o portão.

Diagrama de exemplo:
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/retriggering-activation.svg" alt-text="Diagrama que mostra a ativação reacionada de dois eventos de uma origem.":::

* Duração da gravação =-offset + (chegada do evento 2 – chegada do evento 1) + minimumActivationTime


***N* eventos de uma fonte (*ativação máxima*)**

Um processador de porta de sinal que recebe *N* eventos resulta em uma gravação que inicia 5 segundos (deslocamento de sinal de ativação = 5 segundos) antes que o primeiro evento chegue à porta. À medida que cada evento chega antes do término do tempo mínimo de ativação de 20 segundos do evento anterior, o portão é continuamente disparado. Ele permanece aberto até o tempo máximo de ativação de 40 segundos após o primeiro evento. Em seguida, a porta fecha e não aceita mais nenhum novo evento.

Diagrama de exemplo:
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/maximum-activation.svg" alt-text="Diagrama mostrando a ativação máxima de N eventos de uma fonte.":::
 
* Duração da gravação =-offset + maximumActivationTime

> [!IMPORTANT]
> Os diagramas anteriores pressupõem que todos os eventos cheguem no mesmo instante em tempo físico e em tempo de mídia. Ou seja, eles supõem que não há nenhuma chegada tardia.

## <a name="next-steps"></a>Próximas etapas

Experimente o [tutorial de gravação de vídeo com base em eventos](event-based-video-recording-tutorial.md). Comece editando o [topology.jsem](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json). Modifique os parâmetros para o nó signalgateProcessor e, em seguida, siga o restante do tutorial. Examine as gravações de vídeo para analisar o efeito dos parâmetros.



