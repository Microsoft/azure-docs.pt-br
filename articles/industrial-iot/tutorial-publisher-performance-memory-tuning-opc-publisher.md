---
title: Ajuste de desempenho e memória do Microsoft OPC Publisher
description: Neste tutorial, você aprenderá a ajustar o desempenho e a memória do OPC Publisher.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 89e288d1186efd405019d6474dcbd332e7925d67
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104787188"
---
# <a name="tutorial-tune-the-opc-publisher-performance-and-memory"></a>Tutorial: Ajustar o desempenho e a memória do OPC Publisher

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Ajustar o desempenho
> * Ajustar o fluxo de mensagens aos recursos de memória

Ao executar o OPC Publisher em configurações de produção, os requisitos de desempenho de rede (taxa de transferência e latência) e os recursos de memória devem ser considerados. O OPC Publisher expõe os seguintes parâmetros de linha de comando para ajudar a atender a esses requisitos:

* Capacidade da fila de mensagens (`mq` para a versão 2.5 e anteriores, não disponível na versão 2.6, `om` para a versão 2.7)
* Intervalo de envios do Hub IoT (`si`)
* Tamanho de mensagem do Hub IoT (`ms`)

## <a name="adjusting-iot-hub-send-interval-and-iot-hub-message-size"></a>Ajustando o intervalo de envio e o tamanho de mensagem do Hub IoT

O parâmetro `mq/om` controla o limite superior da capacidade da fila de mensagens interna. Essa fila armazena em buffer todas as mensagens antes que sejam enviadas ao Hub IoT. O tamanho padrão da fila é de até 2 MB para o OPC Publisher versão 2.5 e anteriores e de 4.000 mensagens do Hub IoT para a versão 2.7 (ou seja, se a configuração de tamanho de mensagem do Hub IoT for de 256 KB, o tamanho da fila será de até 1 GB). Se o OPC Publisher não puder enviar mensagens ao Hub IoT com velocidade suficiente, o número de itens nessa fila aumentará. Quando isso ocorre durante execuções de teste, um ou ambos os seguintes procedimentos podem ser feitos para atenuar:

* diminuir o intervalo de envio do Hub IoT (`si`)

* aumentar o tamanho da mensagem do Hub IoT (`ms`, o máximo pode ser definido como 256 KB)

Se a fila continuar crescendo mesmo que os parâmetros `si` e `ms` tenham sido ajustados, em algum momento a capacidade máxima da fila será atingida e as mensagens serão perdidas. Isso ocorre porque os parâmetros `si` e `ms` têm limites físicos e a conexão pela Internet entre o OPC Publisher e o Hub IoT não é rápida o suficiente para o número de mensagens que precisam ser enviadas em um determinado cenário. Nesse caso, apenas a configuração de vários OPC Publishers paralelos ajudará. O parâmetro `mq/om` também tem o maior impacto sobre o consumo de memória pelo OPC Publisher. 

O parâmetro `si` força o Publicador OPC a enviar mensagens ao Hub IoT ao intervalo especificado. Uma mensagem é enviada quando o tamanho máximo da mensagem do Hub IoT de 256 KB de dados está disponível (disparando a redefinição do intervalo de envio) ou quando o intervalo especificado é transcorrido.

O parâmetro `ms` permite o envio em lote de mensagens ao Hub IoT. Na maioria das configurações de rede, a latência de envio de somente uma mensagem ao Hub IoT é alta em comparação com o tempo necessário para transmitir o conteúdo. Isso se deve principalmente aos requisitos de QoS (Qualidade de Serviço), pois as mensagens são confirmadas apenas depois de processadas pelo Hub IoT. Portanto, se um atraso para os dados chegarem ao Hub IoT for aceitável, o OPC Publisher deverá ser configurado para usar o tamanho máximo da mensagem de 256 KB definindo o parâmetro `ms` como 0. Também é a maneira mais econômica de usar o OPC Publisher.

A configuração padrão envia dados ao Hub IoT a cada 10 segundos (`si=10`) ou quando 256 KB de dados de mensagens do Hub IoT estão disponíveis (`ms=0`). Isso adiciona um atraso máximo de 10 segundos, mas tem pouca probabilidade de perder dados devido ao tamanho grande da mensagem. A métrica `monitored item notifications enqueue failure` no OPC Publisher versão 2.5 e anteriores e `messages lost` no OPC Publisher versão 2.7 mostra quantas mensagens foram perdidas.

Quando os parâmetros `si` e `ms` estiverem definidos como 0, o OPC Publisher enviará uma mensagem ao Hub IoT assim que os dados estiverem disponíveis. Isso resulta em um tamanho de mensagem médio do Hub IoT de um pouco mais de 200 bytes. No entanto, a vantagem dessa configuração é que o OPC Publisher envia os dados do ativo conectado sem atraso. O número de mensagens perdidas será alto para casos de uso em que uma grande quantidade de dados precisar ser publicada e, portanto, isso não é recomendado nesses cenários.

Para medir o desempenho do OPC Publisher, o parâmetro `di` pode ser usado para imprimir métricas de desempenho no log de rastreamento no intervalo especificado (em segundos).

## <a name="next-steps"></a>Próximas etapas
Agora que você aprendeu a ajustar o desempenho e a memória do OPC Publisher, pode conferir o repositório do GitHub do OPC Publisher para obter mais recursos:

> [!div class="nextstepaction"]
> [Repositório do GitHub do OPC Publisher](https://github.com/Azure/Industrial-IoT)