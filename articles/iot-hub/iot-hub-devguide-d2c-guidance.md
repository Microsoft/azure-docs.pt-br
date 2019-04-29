---
title: Opções do dispositivo para a nuvem do Hub IoT do Azure| Microsoft Docs
description: Guia do desenvolvedor ‑ diretrizes sobre quando usar mensagens do dispositivo para a nuvem, as propriedades relatadas ou upload do arquivo para comunicações da nuvem para o dispositivo.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: fffa064b912a96b05feb901d1d2d44533c4681b7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60885509"
---
# <a name="device-to-cloud-communications-guidance"></a>Diretrizes de comunicações do dispositivo para a nuvem

Ao enviar informações do aplicativo do dispositivo para o back-end da solução, o Hub IoT expõe três opções:

* [Mensagens do dispositivo para a nuvem](iot-hub-devguide-messages-d2c.md) para a telemetria de série temporal e alertas.

* [Propriedades relatadas do dispositivo gêmeo](iot-hub-devguide-device-twins.md) para relatar informações de estado do dispositivo, como funcionalidades disponíveis, condições ou o estado de fluxos de trabalho de execução longa. Por exemplo, configuração e atualizações de software.

* [Carregar arquivos](iot-hub-devguide-file-upload.md) para arquivos de mídia e lotes grandes de telemetria carregados por dispositivos conectados de forma intermitente ou compactados para economizar largura de banda.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Aqui está uma comparação detalhada das várias opções de comunicação do dispositivo para a nuvem.

|  | Mensagens do dispositivo para a nuvem | Propriedades relatadas do dispositivo gêmeo | Carregamentos de arquivos |
| ---- | ------- | ---------- | ---- |
| Cenário | Série de tempo de telemetria e alertas. Por exemplo, os lotes de dados de sensor de 256 KB enviados a cada 5 minutos. | Recursos disponíveis e condições. Por exemplo, o modo atual de conectividade do dispositivo como celular ou WiFi. Sincronização dos fluxos de trabalho de longa duração, como atualizações de software e configuração. | Arquivos de mídia. Lotes grandes de telemetria (geralmente compactados). |
| Armazenamento e recuperação | Armazenado temporariamente pelo Hub IoT, até sete dias. Somente leitura sequencial. | Armazenados pelo Hub IoT no dispositivo gêmeo. Recuperáveis usando a [linguagem de consulta do Hub IoT](iot-hub-devguide-query-language.md). | Armazenados na conta de Armazenamento do Azure fornecida pelo usuário. |
| Tamanho | Mensagens de até 256 KB. | O tamanho máximo relatado das propriedades é de 8 KB. | Tamanho máximo de arquivo com suporte pelo Armazenamento de Blobs do Azure. |
| Frequência | Alta. Para obter mais informações, confira [Limites do Hub IoT](iot-hub-devguide-quotas-throttling.md). | Média. Para obter mais informações, confira [Limites do Hub IoT](iot-hub-devguide-quotas-throttling.md). | Baixa. Para obter mais informações, confira [Limites do Hub IoT](iot-hub-devguide-quotas-throttling.md). |
| Protocolo | Disponível em todos os protocolos. | Disponível usando MQTT ou AMQP. | Disponível ao usar qualquer protocolo, mas requer HTTPS no dispositivo. |

Um aplicativo pode precisar enviar informações tanto como um alerta ou uma série temporal de telemetria e torná-lo disponível no dispositivo gêmeo. Nesse cenário, você pode escolher uma das seguintes opções:

* O aplicativo do dispositivo envia uma mensagem de dispositivo para a nuvem e relata uma alteração de propriedade.
* O back-end da solução pode armazenar as informações nas marcações do dispositivo gêmeo quando ele recebe a mensagem.

Como as mensagens de dispositivo para a nuvem permitem uma taxa de transferência muito mais alta do que as atualizações do dispositivo gêmeo, às vezes é desejável evitar a atualização do dispositivo gêmeo para cada mensagem de dispositivo para a nuvem.