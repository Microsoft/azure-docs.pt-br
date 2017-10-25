---
title: "Opções do dispositivo para a nuvem do Hub IoT do Azure| Microsoft Docs"
description: "Guia do desenvolvedor ‑ diretrizes sobre quando usar mensagens do dispositivo para a nuvem, as propriedades relatadas ou upload do arquivo para comunicações da nuvem para o dispositivo."
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: 
ms.assetid: 979136db-c92d-4288-870c-f305e8777bdd
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/09/2017
ms.author: elioda
ms.openlocfilehash: 335928776e1e62caf2855cd5a5684ccaf37f73cd
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="device-to-cloud-communications-guidance"></a>Diretrizes de comunicações do dispositivo para a nuvem
Ao enviar informações do aplicativo do dispositivo para o back-end da solução, o Hub IoT expõe três opções:

* [Mensagens do dispositivo para a nuvem ][lnk-d2c], para telemetria de série de tempo e alertas.
* [Propriedades relatadas do dispositivo gêmeo][lnk-twins] para relatar informações de estado do dispositivo, como recursos disponíveis, condições ou o estado de fluxos de trabalho de longa duração. Por exemplo, configuração e atualizações de software.
* [Carregar arquivos][lnk-fileupload], para arquivos de mídia e lotes grandes de telemetria carregados por dispositivos conectados de forma intermitente ou compactados para economizar largura de banda.

Aqui está uma comparação detalhada das várias opções de comunicação do dispositivo para a nuvem.

|  | Mensagens do dispositivo para a nuvem | Propriedades relatadas do dispositivo gêmeo | Carregamentos de arquivos |
| ---- | ------- | ---------- | ---- |
| Cenário | Série de tempo de telemetria e alertas. Por exemplo, os lotes de dados de sensor de 256 KB enviados a cada 5 minutos. | Recursos disponíveis e condições. Por exemplo, o modo atual de conectividade do dispositivo como celular ou WiFi. Sincronização dos fluxos de trabalho de longa duração, como atualizações de software e configuração. | Arquivos de mídia. Lotes grandes de telemetria (geralmente compactados). |
| Armazenamento e recuperação | Armazenado temporariamente pelo Hub IoT, até sete dias. Somente leitura sequencial. | Armazenados pelo Hub IoT no dispositivo gêmeo. Recuperáveis usando a [linguagem de consulta do Hub IoT][lnk-query]. | Armazenados na conta de Armazenamento do Azure fornecida pelo usuário. |
| Tamanho | Mensagens de até 256 KB. | O tamanho máximo relatado das propriedades é de 8 KB. | Tamanho máximo de arquivo com suporte pelo Armazenamento de Blobs do Azure. |
| Frequência | Alta. Para saber mais, confira [Limites do Hub IoT][lnk-quotas]. | Média. Para saber mais, confira [Limites do Hub IoT][lnk-quotas]. | Baixa. Para saber mais, confira [Limites do Hub IoT][lnk-quotas]. |
| Protocolo | Disponível em todos os protocolos. | Disponível usando MQTT ou AMQP. | Disponível ao usar qualquer protocolo, mas requer HTTPS no dispositivo. |

É possível que um aplicativo exija o envio de informações como uma série de tempo de telemetria ou alerta, e também a disponibilização no dispositivo gêmeo. Nesse cenário, você pode escolher uma das seguintes opções:

* O aplicativo do dispositivo envia uma mensagem de dispositivo para a nuvem e relata uma alteração de propriedade.
* O back-end da solução pode armazenar as informações nas marcações do dispositivo gêmeo quando ele recebe a mensagem.

Como as mensagens de dispositivo para a nuvem permitem uma taxa de transferência muito mais alta do que as atualizações do dispositivo gêmeo, às vezes é desejável evitar a atualização do dispositivo gêmeo para cada mensagem de dispositivo para a nuvem.


[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-fileupload]: iot-hub-devguide-file-upload.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
