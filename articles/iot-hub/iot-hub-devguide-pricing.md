---
title: "Entender o preço do Hub IoT do Azure| Microsoft Docs"
description: "Guia do desenvolvedor ‑ informações sobre como a medição e preço funcionam no Hub IoT, incluindo exemplos funcionais."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 1ac90923-1edf-4134-bbd4-77fee9b68d24
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/29/2017
ms.author: elioda
ms.openlocfilehash: 05006a78cc7d82bc048ec5706465f7140eb40e94
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-iot-hub-pricing-information"></a>Informações sobre preços do Hub IoT do Azure

O [Preço do Hub IoT do Azure][lnk-pricing] fornece as informações gerais sobre SKUs e preços diferentes para o Hub IoT. Este artigo contém detalhes adicionais sobre como as diversas funcionalidades do Hub IoT são medidas como mensagens pelo Hub IoT.

## <a name="charges-per-operation"></a>Encargos por operação

| Operação | Informações de cobrança | 
| --------- | ------------------- |
| Operações de registro de identidade <br/> (criar, recuperar, listar, atualizar e excluir) | Não será cobrado. |
| Mensagens do dispositivo para a nuvem | As mensagens enviadas com êxito são cobradas em partes de 4 KB na entrada no Hub IoT, por exemplo, uma mensagem de 6 KB é cobrada em 2 mensagens. |
| Mensagens da nuvem para o dispositivo | As mensagens enviadas com êxito são cobradas em partes de 4 KB, por exemplo, uma mensagem de 6 KB é cobrada em 2 mensagens. |
| Carregamentos de arquivos | A transferência de arquivos para o Armazenamento do Azure não é limitada pelo Hub IoT. Mensagens de conclusão e inicialização de transferência de arquivo são cobradas como mensagens limitadas com incrementos de 4 KB. Por exemplo, transferir um arquivo de 10 MB são cobradas duas mensagens além do custo de Armazenamento do Azure. |
| Métodos diretos | As solicitações de métodos bem-sucedidas são cobradas em partes de 4 KB, as respostas com corpos não vazios são cobradas em 4 KB como mensagens adicionais. As solicitações para dispositivos desconectados são cobradas como mensagens em partes de 4 KB. Por exemplo, um método com um corpo de 6 KB que resulta em uma resposta sem corpo do dispositivo é cobrado como duas mensagens; um método com um corpo de 6 KB que resulta em uma resposta de 1 KB do dispositivo é cobrado como duas mensagens para a solicitação mais uma outra mensagem para a resposta. |
| Leituras de dispositivo gêmeo | Leituras de dispositivo gêmeo do dispositivo e back-end de solução são cobradas como mensagens em partes de 512 bytes. Por exemplo, a leitura de um dispositivo gêmeo de 6 KB é cobrada como 12 mensagens. |
| Atualizações do dispositivo gêmeo (marcas e propriedades) | As atualizações de dispositivo gêmeo do dispositivo e do back-end da solução são cobradas como mensagens em partes de 512 bytes. Por exemplo, a leitura de um dispositivo gêmeo de 6 KB é cobrada como 12 mensagens. |
| Consultas de dispositivo gêmeo | As consultas são cobradas como mensagens, dependendo do tamanho do resultado em partes de 512 bytes. |
| Operações de trabalhos <br/> (criar, atualizar, listar, excluir) | Não será cobrado. |
| Operações de trabalhos por dispositivo | As operações de trabalhos (como atualizações de dispositivos gêmeos e métodos) são cobradas de forma normal. Por exemplo, um trabalho que resulta em 1000 chamadas de método com solicitações de 1 KB e respostas de corpo vazio é cobrado em 1000 mensagens. |

> [!NOTE]
> Todos os tamanhos são computados considerando-se o tamanho da carga em bytes (o enquadramento de protocolo é ignorado). No caso de mensagens (que têm propriedades e corpo), o tamanho é calculado independentemente do protocolo, conforme descrito no [Guia do desenvolvedor de mensagens do Hub IoT][lnk-message-size].

## <a name="example-1"></a>Exemplo 1

Um dispositivo envia uma mensagem de 1 KB do dispositivo para nuvem por minuto para Hub IoT, que é lida pelo Azure Stream Analytics. O back-end de solução invoca um método (com carga de 512 bytes) no dispositivo a cada dez minutos para disparar uma ação específica. O dispositivo responde ao método com um resultado de 200 bytes.

O dispositivo consome 1 mensagem * 60 minutos * 24 horas = 1440 mensagens por dia para as mensagens do dispositivo para a nuvem e 2 solicitações mais resposta * 6 vezes por hora * 24 horas = 288 mensagens para os métodos, para um total de 1728 Mensagens por dia.

## <a name="example-2"></a>Exemplo 2:

Um dispositivo envia uma mensagem de 100 KB do dispositivo para a nuvem a cada hora. Ele também atualiza seu dispositivo gêmeo com cargas de 1 KB a cada 4 horas. O back-end de solução, uma vez por dia, lê o dispositivo gêmeo de 14 KB e atualiza-o com cargas de 512 bytes para alterar as configurações.

O dispositivo consome 25 mensagens (100 KB/4 KB) * 24 horas para mensagens do dispositivo para a nuvem, mais 2 mensagens (1 KB/0,5 KB) * 6 vezes por dia para atualizações de dispositivo gêmeo, para um total de 612 mensagens por dia.
O back-end da solução consome 28 mensagens (14 KB/0,5 KB) para ler o dispositivo gêmeo, além de 1 mensagem para atualizá-lo, em um total de 29 mensagens.

No total, o dispositivo e o back-end da solução consomem 641 mensagens por dia.


[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[lnk-message-size]: iot-hub-devguide-messages-construct.md
