---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 09/17/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: c79b6f854dc78670a7eb8a1275c3e2fc46fcdd99
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172357"
---
### <a name="code-walkthrough"></a>Passo a passo do código

Esta seção descreve algumas das partes principais do código de exemplo e explica como se relacionam com o acelerador de solução de monitoramento remoto.

O trecho de código a seguir mostra como as propriedades relatadas que descrevem os recursos do dispositivo são definidas. Essas propriedades incluem:

- A localização do dispositivo para permitir que o acelerador de solução adicione o dispositivo ao mapa.
- A versão atual do firmware.
- A lista de métodos com suporte no dispositivo.
- O esquema das mensagens de telemetria enviadas pelo dispositivo.

[!code-cpp[Define data structures for Chiller](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=datadefinition "Define data structures for Chiller")]

O exemplo inclui uma função **serializeToJson** que serializa essa estrutura de dados usando a biblioteca Parson.

O exemplo inclui várias funções de retorno de chamada que imprimem informações no console enquanto o cliente interage com o acelerador de solução:

- **connection_status_callback**
- **send_confirm_callback**
- **reported_state_callback**
- **device_method_callback**

O trecho de código a seguir mostra a função **device_method_callback**. Essa função determina a ação a ser tomada quando uma chamada de método é recebida do acelerador de solução. A função recebe uma referência para a estrutura de dados do **Resfriador** no parâmetro **userContextCallback**. O valor de **userContextCallback** é definido quando a função de retorno de chamada é configurada na função **principal**:

[!code-cpp[Device method callback](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=devicemethodcallback "Device method callback")]

Quando o acelerador de solução chama o método de atualização de firmware, o exemplo desserializa a carga JSON e inicia um thread de segundo plano para concluir o processo de atualização. O trecho de código a seguir mostra o **do_firmware_update** que executa no thread:

[!code-cpp[Firmware update thread](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=firmwareupdate "Firmware update thread")]

O trecho de código a seguir mostra como o cliente envia uma mensagem de telemetria ao acelerador de solução. As propriedades da mensagem incluem o esquema de mensagem para ajudar o acelerador de solução a exibir a telemetria no painel:

[!code-cpp[Send telemetry](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=sendmessage "Send telemetry")]

A função **principal** no exemplo:

- Inicializa e desliga o subsistema SDK.
- Inicializa a estrutura de dados do **Resfriador**.
- Envia as propriedades relatadas ao acelerador de solução.
- Configura a função de retorno de chamada do método do dispositivo.
- Envia valores de telemetria simulados ao acelerador de solução.

[!code-cpp[Main](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=main "Main")]
