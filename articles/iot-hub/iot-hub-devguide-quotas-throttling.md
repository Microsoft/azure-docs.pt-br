---
title: Entender cotas e limitação do Hub IoT do Azure | Microsoft Docs
description: Guia do desenvolvedor ‑ Descrição das cotas que se aplicam ao Hub IoT e o comportamento de limitação esperado.
author: robinsh
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2019
ms.custom:
- 'Role: Cloud Development'
- 'Role: Operations'
- 'Role: Technical Support'
ms.openlocfilehash: 5a5b20efbf804c2ea1097f905da1cfd62727ff15
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94410684"
---
# <a name="reference---iot-hub-quotas-and-throttling"></a>Referência - Cotas e limitação do Hub IoT

Este artigo explica as cotas de um hub IoT e fornece informações para ajudá-lo a entender como a limitação funciona.

## <a name="quotas-and-throttling"></a>Cotas e limitação

Cada assinatura do Azure pode ter no máximo 50 hubs IoT e pelo menos um hub Gratuito.

Cada Hub IoT é provisionado com um determinado número de unidades em uma camada específica. A camada e o número de unidades determinam a cota diária máxima de mensagens que você pode enviar. O tamanho da mensagem usado para calcular a cota diária é de 0,5 KB para um hub de nível gratuito e de 4KB para todos os outros níveis. Para obter mais informações, consulte [preços do Hub IOT do Azure](https://azure.microsoft.com/pricing/details/iot-hub/).

A camada também determina os limites de limitação que o Hub IoT aplicam em todas as operações.

## <a name="iot-plug-and-play"></a>IoT Plug and Play

Os dispositivos IoT Plug and Play enviam pelo menos uma mensagem de telemetria para cada interface, incluindo a raiz, o que pode aumentar o número de mensagens contadas em direção à sua cota de mensagens.

## <a name="operation-throttles"></a>Restrições de operação

As restrições de operação são limitações de taxa que são aplicadas em intervalos de minutos e destinam-se a evitar abusos. Eles também estão sujeitos à [modelagem de tráfego](#traffic-shaping).

A tabela a seguir mostra as limitações impostas. Os valores referem-se a um hub individual.

| Restrição | Gratuito, B1 e S1 | B2 e S2 | B3 e S3 | 
| -------- | ------- | ------- | ------- |
| [Operações de registro de identidade](#identity-registry-operations-throttle) (criar, recuperar, listar, atualizar, excluir) | 1,67/s/unidade (100/min/unidade) | 1,67/s/unidade (100/min/unidade) | 83,33/s/unidade (5000/min/unidade) |
| [Novas conexões de dispositivo](#device-connections-throttle) (esse limite se aplica à taxa de _novas conexões_ , não ao número total de conexões) | Máximo de 100/s ou 12/s/unidade <br/> Por exemplo, duas unidades de S1 são 2\*12 = 24 novas conexões/s, mas você tem pelo menos 100 novas conexões s em suas unidades. Com nove unidades S1 você tem 108/s novas conexões/s (9\*12) em suas uniddes. | 120 novas conexões/s/unidade | 6.000 novas conexões/s/unidade |
| Envios do dispositivo para a nuvem | Mais alto de 100 operações de envio/s ou 12 operações de envio/s/unidade <br/> Por exemplo, duas unidades S1 são 2 \* 12 = 24/s, mas você tem pelo menos 100 operações de envio/s em suas unidades. Com nove unidades S1, você tem 108 operações de envio/s (9 \* 12) em suas unidades. | 120 operações de envio/s/unidade | 6.000 operações de envio/s/unidade |
| Envios de nuvem para dispositivo<sup>1</sup> | 1,67 operações de envio/s/unidade (100 mensagens/min/unidade) | 1,67 operações de envio/s/unidade (100 operações de envio/min/unidade) | 83,33 operações de envio/s/unidade (5.000 operações de envio/min/unidade) |
| Recebimentos de nuvem para dispositivo<sup>1</sup> <br/> (somente quando o dispositivo usar HTTPS)| 16,67 operações de recebimento/s/unidade (1.000 operações de recebimento/min/unidade) | 16,67 operações de recebimento/s/unidade (1.000 operações de recebimento/min/unidade) | 833,33 operações de recebimento/s/unidade (50.000 operações de recebimento/min/unidade) |
| Upload de arquivos | 1,67 inícios de carregamento de arquivo/s/unidade (100/min/unidade) | 1,67 inícios de carregamento de arquivo/s/unidade (100/min/unidade) | 83,33 iniciações de carregamento de arquivo/s/unidade (5000/min/unidade) |
| Métodos diretos<sup>1</sup> | 160KB/seg/unidade<sup>2</sup> | 480KB/seg/unidade<sup>2</sup> | 24MB/seg/unidade<sup>2</sup> | 
| Consultas | 20/min/unidade | 20/min/unidade | 1000/min/unidade |
| Leituras de (dispositivos e módulos) gêmeos <sup>1</sup> | 100/s | Maior de 100/s ou 10/s/unidade | 500/s/unidade |
| Atualizações de (dispositivo e módulo) gêmeos <sup>1</sup> | 50/s | Superior a 50/s ou 5/s/unidade | 250/s/unidade |
| Operações de trabalhos<sup>1</sup> <br/> (criar, atualizar, listar, excluir) | 1,67/s/unidade (100/min/unidade) | 1,67/s/unidade (100/min/unidade) | 83,33/s/unidade (5000/min/unidade) |
| Operações de dispositivo de trabalhos<sup>1</sup> <br/> (atualizar gêmeos, invocar o método direto) | 10/s | Máximo de 10/s ou 1/s/unidade | 50/s/unidade |
| Configurações e implantações de borda <sup>1</sup> <br/> (criar, atualizar, listar, excluir) | 0,33/s/unidade (20/min/unidade) | 0,33/s/unidade (20/min/unidade) | 0,33/s/unidade (20/min/unidade) |
| Taxa de início de fluxo de dispositivo<sup>1</sup> | 5 novos fluxos/s | 5 novos fluxos/s | 5 novos fluxos/s |
| Número máximo de fluxos de dispositivo conectados simultaneamente<sup>1</sup> | 50 | 50 | 50 |
| Transferência máxima de dados de fluxo de dispositivo<sup>1</sup> (volume agregado por dia) | 300 MB | 300 MB | 300 MB |

<sup>1</sup>Esse recurso não está disponível na camada básica do Hub IoT. Para obter mais informações, consulte [Como escolher o Hub IoT correto](iot-hub-scaling.md). <br/><sup>2</sup> O tamanho do medidor de limitação é 4 KB. A limitação é baseada apenas no tamanho da carga de solicitação.

### <a name="throttling-details"></a>Detalhes da limitação

* O tamanho do medidor determina em que incrementos o limite de limitação é consumido. Se o conteúdo da sua chamada direta estiver entre 0 e 4 KB, ele será contado como 4 KB. Você pode fazer até 40 chamadas por segundo por unidade antes de atingir o limite de 160 KB/s/unidade.

   Da mesma forma, se sua carga estiver entre 4 KB e 8 KB, cada chamada contará para 8 KB e você poderá fazer até 20 chamadas por segundo por unidade antes de atingir o limite máximo.

   Por fim, se o tamanho da carga estiver entre 156KB e 160 KB, você poderá fazer apenas uma chamada por segundo por unidade em seu hub antes de atingir o limite de 160 KB/s/unidade.

*  Para *operações de dispositivo de trabalhos (atualização de "atualizar", invocar método direto)* para a camada S3, 50/s/unidade só se aplica a quando você invoca métodos usando trabalhos. Se você invocar métodos diretos diretamente, o limite de limitação original de 24 MB/s/unidade (para S3) se aplicará.

*  **Cota** é o número agregado de mensagens que você pode enviar em seu hub *por dia*. Você pode encontrar o limite de cota do seu hub na coluna **número total de mensagens/dia** na [página de preços do Hub IOT](https://azure.microsoft.com/pricing/details/iot-hub/).

*  Os limites da nuvem para o dispositivo e do dispositivo para a nuvem determinam a *taxa* máxima na qual você pode enviar mensagens – número de mensagens, independentemente das partes de 4 KB. Cada mensagem pode ter até 256 KB, que é o [tamanho máximo da mensagem](iot-hub-devguide-quotas-throttling.md#other-limits).

*  É uma boa prática limitar suas chamadas para que você não pressione/exceda os limites de limitação. Se você atingir o limite, o Hub IoT responderá com o código de erro 429 e o cliente deverá fazer o logoff e tentar novamente. Esses limites são por Hub (ou, em alguns casos por Hub/unidade). Para obter mais informações, consulte [gerenciar conectividade e padrões confiáveis de mensagens/repetição](iot-hub-reliability-features-in-sdks.md#retry-patterns).

### <a name="traffic-shaping"></a>Traffic Shaping

Para acomodar o tráfego de intermitência, o Hub IoT aceita solicitações acima do limite por um período limitado. As primeiras dessas solicitações são processadas imediatamente. No entanto, se o número de solicitações continuar violando a limitação, o Hub IoT começará a colocar as solicitações em uma fila e será processado com a taxa de limite. Esse efeito é chamado de *modelagem de tráfego*. Além disso, o tamanho dessa fila é limitado. Se a violação de limitação continuar, eventualmente a fila será preenchida e o Hub IoT começará a rejeitar solicitações com `429 ThrottlingException` .

Por exemplo, você usa um dispositivo simulado para enviar mensagens de 200 do dispositivo para a nuvem por segundo para o Hub IoT S1 (que tem um limite de 100/s D2C de envios). Para o primeiro minuto ou dois, as mensagens são processadas imediatamente. No entanto, como o dispositivo continua a enviar mais mensagens do que o limite de limitação, o Hub IoT começa a processar apenas 100 mensagens por segundo e coloca o restante em uma fila. Você começa a notar maior latência. Eventualmente, você começa a obter `429 ThrottlingException` à medida que a fila é preenchida e a [métrica "número de erros de limitação" do Hub IOT](monitor-iot-hub-reference.md#device-telemetry-metrics) começa a aumentar. Para saber como criar alertas e gráficos com base em métricas, consulte [monitorar o Hub IOT](monitor-iot-hub.md).

### <a name="identity-registry-operations-throttle"></a>Restrição de operações de registro de identidade

As operações de registro de identidade do dispositivo são destinadas ao uso em tempo de execução em cenários de provisionamento e gerenciamento de dispositivos. Há suporte para leitura ou atualização de grandes números de identidades de dispositivo por meio de [trabalhos de importação e exportação](iot-hub-devguide-identity-registry.md#import-and-export-device-identities).

### <a name="device-connections-throttle"></a>Restrição de conexões de dispositivo

As *conexões de dispositivo* controla a taxa em que novas conexões de dispositivo podem ser estabelecidas com um Hub IoT. A restrição de *conexões de dispositivo* não controla o número máximo de dispositivos conectados simultaneamente. A limitação da taxa de *conexões de dispositivo* depende do número de unidades provisionadas para o hub IoT.

Por exemplo, se você comprar uma única unidade S1, obterá uma restrição de 100 conexões por segundo. Portanto, para conectar dispositivos 100.000, ele leva pelo menos 1.000 segundos (aproximadamente 16 minutos). No entanto, você pode conectar ao mesmo tempo todos os seus dispositivos registrados no registro de identidade.

## <a name="other-limits"></a>Outros limites

IoT Hub impõe outros limites operacionais:

| Operação | Limite |
| --------- | ----- |
| Dispositivos | O número total de dispositivos mais módulos que podem ser registrados em um único Hub IoT é limitado em 1 milhão. A única maneira de aumentar esse limite é entrar em contato com [suporte da Microsoft](https://azure.microsoft.com/support/options/).|
| Carregamentos de arquivos | 10 carregamentos de arquivo simultâneos por dispositivo. |
| Trabalhos<sup>1</sup> | O máximo de trabalhos simultâneos é 1 (para gratuito e S1), 5 (para S2) e 10 (para S3). No entanto, o máximo de trabalhos simultâneos de [importação/exportação de dispositivo](iot-hub-bulk-identity-mgmt.md) é 1 para todas as camadas. <br/>O histórico de trabalhos é mantido em até 30 dias. |
| Pontos de extremidade adicionais | Hubs SKU pagos podem ter 10 pontos de extremidade adicionais. Hubs SKU gratuitos podem ter um ponto de extremidade adicional. |
| Consultas de roteamento de mensagens | Os hubs de SKU pagos podem ter 100 consultas de roteamento. Os hubs SKU gratuitos podem ter cinco consultas de roteamento. |
| Aprimoramentos de mensagem | Os hubs de SKU pagos podem ter até 10 aprimoramentos de mensagens. Os hubs de SKU gratuitos podem ter até 2 aprimoramentos de mensagens.|
| Mensagens do dispositivo para a nuvem | Tamanho máximo da mensagem 256 KB |
| Mensagens de nuvem para dispositivo<sup>1</sup> | Tamanho máximo da mensagem 64 KB. O máximo de mensagens pendentes para entrega é de 50 por dispositivo. |
| Método direto<sup>1</sup> | O tamanho de payload do método direto máximo é 128 KB. |
| Configurações automáticas de dispositivo e módulo<sup>1</sup> | 100 configurações por hub SKU pago. 20 configurações por hub SKU gratuito. |
| IoT Edge implantações automáticas<sup>1</sup> | 50 módulos por implantação. 100 implantações (incluindo implantações em camadas) por Hub SKU pago. 10 implantações por Hub SKU gratuito. |
| Gêmeos<sup>1</sup> | O tamanho máximo das seções desejadas e das propriedades relatadas são 32 KB. O tamanho máximo da seção de marcas é 8 KB. |
| Políticas de acesso compartilhado | O número máximo de políticas de acesso compartilhado é 16. |
| certificados de AC X509 | O número máximo de certificados de AC X509 que podem ser registrados no Hub IoT é 25. |

<sup>1</sup>Esse recurso não está disponível na camada básica do Hub IoT. Para obter mais informações, consulte [Como escolher o Hub IoT correto](iot-hub-scaling.md).

## <a name="increasing-the-quota-or-throttle-limit"></a>Aumentando a cota ou o limite de limitação

A qualquer momento, você pode aumentar as cotas ou os limites de limitação [aumentando o número de unidades provisionadas em um hub IOT](iot-hub-upgrade.md).

## <a name="latency"></a>Latency

O Hub IoT busca oferecer baixa latência para todas as operações. No entanto, devido a condições de rede e outros fatores imprevisíveis, ele não pode garantir uma determinada latência. Ao projetar sua solução, você deve:

* Evitar fazer suposições sobre a latência máxima de qualquer operação de IoT Hub.
* Provisionar o hub IoT na região do Azure mais próxima de seus dispositivos.
* Considere usar o Azure IoT Edge para executar operações sensíveis à latência no dispositivo ou no gateway perto do dispositivo.

Várias unidades de Hub IoT afetam limitação, conforme descrito anteriormente, mas não oferecem nenhum benefício de latência nem garantia adicional.

No caso de aumentos inesperados na latência da operação, entre em contato com o [Suporte da Microsoft](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Próximas etapas

Para uma discussão aprofundada do comportamento de limitação do Hub IoT, veja a postagem do blog [A limitação do Hub IoT e você](https://azure.microsoft.com/blog/iot-hub-throttling-and-you/).

Outros tópicos de referência neste Guia do desenvolvedor do Hub IoT incluem:

* [Pontos de extremidade do Hub IoT](iot-hub-devguide-endpoints.md)
* [Monitorar o Hub IoT](monitor-iot-hub.md)
