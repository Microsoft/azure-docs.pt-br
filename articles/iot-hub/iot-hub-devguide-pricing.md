---
title: Entender o preço do Hub IoT do Azure| Microsoft Docs
description: Guia do desenvolvedor ‑ informações sobre como a medição e preço funcionam no Hub IoT, incluindo exemplos funcionais.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.openlocfilehash: 9b6db1b7171652ea5ace4db370b72dc22b6bdc90
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60626223"
---
# <a name="azure-iot-hub-pricing-information"></a>Informações sobre preços do Hub IoT do Azure

O [Preço do Hub IoT do Azure](https://azure.microsoft.com/pricing/details/iot-hub) fornece as informações gerais sobre SKUs e preços diferentes para o Hub IoT. Este artigo contém detalhes adicionais sobre como as diversas funcionalidades do Hub IoT são medidas como mensagens pelo Hub IoT.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="charges-per-operation"></a>Encargos por operação

| Operação | Informações de cobrança | 
| --------- | ------------------- |
| Operações de registro de identidade <br/> (criar, recuperar, listar, atualizar e excluir) | Não será cobrado. |
| Mensagens do dispositivo para a nuvem | As mensagens enviadas com êxito são cobradas em partes de 4 KB na entrada no Hub IoT. Por exemplo, uma mensagem de 6 KB é cobrada 2 mensagens. |
| Mensagens da nuvem para o dispositivo | As mensagens enviadas com êxito são cobradas em partes de 4 KB, por exemplo, uma mensagem de 6 KB é cobrada em 2 mensagens. |
| Carregamentos de arquivos | A transferência de arquivos para o Armazenamento do Azure não é limitada pelo Hub IoT. Mensagens de conclusão e inicialização de transferência de arquivo são cobradas como mensagens limitadas com incrementos de 4 KB. Por exemplo, a transferência de um arquivo de 10 MB é cobrada como duas mensagens além do custo do armazenamento do Azure. |
| Métodos diretos | Solicitações de métodos bem-sucedidas são cobradas em partes de 4 KB e respostas são cobradas em blocos de 4 KB como mensagens adicionais. As solicitações para dispositivos desconectados são cobradas como mensagens em partes de 4 KB. Por exemplo, um método com um corpo de 4 KB que resulta em uma resposta sem corpo do dispositivo é cobrado como duas mensagens. Um método com um corpo de 6 KB que resulta em uma resposta de 1 KB do dispositivo é cobrado como duas mensagens para a solicitação mais outra mensagem para a resposta. |
| Leituras de dispositivos e módulos gêmeos | Leituras de dispositivos ou módulos gêmeos e de back-end de solução são cobradas como mensagens em partes de 512 bytes. Por exemplo, a leitura de um gêmeo de 6 KB é cobrada como 12 mensagens. |
| Atualizações do dispositivo e módulo gêmeo (marcas e propriedades) | Atualizações de dispositivos ou módulos gêmeos e de back-end de solução são cobradas como mensagens em partes de 512 bytes. Por exemplo, a leitura de um gêmeo de 6 KB é cobrada como 12 mensagens. |
| Consultas de dispositivos e módulos gêmeos | As consultas são cobradas como mensagens, dependendo do tamanho do resultado em partes de 512 bytes. |
| Operações de trabalhos <br/> (criar, atualizar, listar, excluir) | Não será cobrado. |
| Operações de trabalhos por dispositivo | As operações de trabalhos (como atualizações de gêmeos e métodos) são cobradas de forma normal. Por exemplo, um trabalho que resulta em 1000 chamadas de método com solicitações de 1 KB e respostas de corpo vazio é cobrado em 1000 mensagens. |
| Mensagens keep alive | Ao usar protocolos AMQP ou MQTT, as mensagens trocadas para estabelecer a conexão e durante a negociação não são cobradas. |

> [!NOTE]
> Todos os tamanhos são computados considerando-se o tamanho da carga em bytes (o enquadramento de protocolo é ignorado). Para mensagens que têm propriedades e o corpo, o tamanho é calculado de maneira independente de protocolo. Para obter mais informações, consulte [formato de mensagem do Hub IoT](iot-hub-devguide-messages-construct.md).

## <a name="example-1"></a>Exemplo 1

Um dispositivo envia uma mensagem de 1 KB do dispositivo para nuvem por minuto para Hub IoT, que é lida pelo Azure Stream Analytics. O back-end de solução invoca um método (com uma carga de 512 bytes) no dispositivo a cada 10 minutos para disparar uma ação específica. O dispositivo responde ao método com um resultado de 200 bytes.

O dispositivo consume:

* Uma mensagem * 60 minutos * 24 horas = 1440 mensagens por dia para as mensagens de dispositivo para a nuvem.
* Duas solicitações e resposta * 6 vezes por hora * 24 horas = 288 mensagens para os métodos.

Esse cálculo oferece um total de 1728 mensagens por dia.

## <a name="example-2"></a>Exemplo 2:

Um dispositivo envia uma mensagem de 100 KB do dispositivo para a nuvem a cada hora. Ele também atualiza seu dispositivo gêmeo com cargas de 1 KB a cada quatro horas. O back-end de solução, uma vez por dia, lê o dispositivo gêmeo de 14 KB e atualiza-o com cargas de 512 bytes para alterar as configurações.

O dispositivo consume:

* 25 mensagens (100 KB/4 KB) * 24 horas para mensagens de dispositivo para nuvem.
* Duas mensagens (1 KB/0,5 KB) * seis vezes por dia para as atualizações do dispositivos gêmeos.

Esse cálculo oferece um total de 612 mensagens por dia.

O back-end da solução consome 28 mensagens (14 KB/0,5 KB) para ler o dispositivo gêmeo, além de uma mensagem para atualizá-lo, em um total de 29 mensagens.

No total, o dispositivo e o back-end da solução consomem 641 mensagens por dia.
