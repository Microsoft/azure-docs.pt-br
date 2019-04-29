---
title: Enviar suas mensagens de segurança à Central de segurança do Azure para a versão prévia do IoT | Microsoft Docs
description: Saiba como enviar suas mensagens de segurança usando a Central de segurança do Azure para IoT.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: c611bb5c-b503-487f-bef4-25d8a243803d
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: a9974fd15ae9c8c420992c3ae1084feebae0f57d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61352036"
---
# <a name="send-security-messages-sdk"></a>Enviar mensagens de segurança do SDK

> [!IMPORTANT]
> A Central de Segurança do Azure para IoT está em versão prévia pública no momento.
> Esta versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este guia de instruções explica a Central de segurança do Azure (ASC) para recursos do serviço de IoT quando você escolhe coletar e enviar mensagens de segurança de seu dispositivo sem usar um ASC para agente do IoT e explica como fazer isso.  

Neste guia, você aprenderá a: 
> [!div class="checklist"]
> * Usar a mensagem de segurança Enviar de API para C#
> * Usar a mensagem de segurança Enviar de API para C

## <a name="asc-for-iot-capabilities"></a>ASC para recursos de IoT

ASC para IoT pode processar e analisar qualquer tipo de dados de mensagem de segurança, desde que os dados enviados está de acordo com o [ASC para IoT esquema](https://aka.ms/iot-security-schemas) e a mensagem é definida como uma mensagem de segurança.

## <a name="security-message"></a>Mensagem de segurança

ASC para IoT define uma mensagem de segurança usando os seguintes critérios:
- Se a mensagem foi enviada com o Azure IoT C /C# SDK
- Se a mensagem está em conformidade com o [esquema de mensagem de segurança](https://aka.ms/iot-security-schemas)
- Se a mensagem tiver sido definida como uma mensagem de segurança antes de enviar

Cada mensagem de segurança inclui os metadados do remetente, como `AgentId`, `AgentVersion`, `MessageSchemaVersion` e uma lista de eventos de segurança.
O esquema define as propriedades válidas e necessárias da mensagem de segurança que inclui os tipos de eventos.

[!NOTE]
> Mensagens enviadas que não são compatíveis com o esquema são ignoradas. Certifique-se de verificar o esquema antes de iniciar o envio dos dados, pois as mensagens ignoradas não estão armazenadas no momento. 
> As mensagens enviadas que não foram definidos como uma mensagem de segurança usando o Azure IoT C /C# SDK não será roteado para o ASC para o pipeline de IoT

## <a name="valid-message-example"></a>Exemplo de mensagem válido

O exemplo a seguir mostra um objeto de mensagem de segurança válido. O exemplo contém os metadados de mensagem e um `ProcessCreate` eventos de segurança.

Uma vez definido como uma mensagem de segurança e enviado, essa mensagem será processada pelo ASC para IoT.

```json
"AgentVersion": "0.0.1",
"AgentId": "e89dc5f5-feac-4c3e-87e2-93c16f010c25",
"MessageSchemaVersion": "1.0",
"Events": [
    {
        "EventType": "Security",
        "Category": "Triggered",
        "Name": "ProcessCreate",
        "IsEmpty": false,
        "PayloadSchemaVersion": "1.0",
        "Id": "21a2db0b-44fe-42e9-9cff-bbb2d8fdf874",
        "TimestampLocal": "2019-01-27 15:48:52Z",
        "TimestampUTC": "2019-01-27 13:48:52Z",
        "Payload":
            [
                {
                    "Executable": "/usr/bin/echo",
                    "ProcessId": 11750,
                    "ParentProcessId": 1593,
                    "UserName": "nginx",
                    "CommandLine": "./backup .htaccess"
                }
            ]
    }
]
```

## <a name="send-security-messages"></a>Enviar mensagens de segurança 

Enviar mensagens de segurança sem usar o ASC para agente do IoT, usando o [IoT do Azure C# SDK do dispositivo](https://github.com/Azure/azure-iot-sdk-csharp/tree/preview) ou [SDK do dispositivo Azure IoT C](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview).

Para enviar os dados dos seus dispositivos ao processamento pela ASC para IoT, use uma das seguintes APIs para marcar mensagens para roteamento correto ao pipeline de processamento de ASC para IoT. As mensagens enviadas dessa maneira serão processadas e exibidas como insights de segurança na ASC para IoT em ambos os Hub IoT ou na Central de Segurança do Azure. 

Todos os dados enviados, mesmo se marcados com o cabeçalho correto, também devem estar em conformidade com o [esquema de mensagens da ASC para IoT](https://aka.ms/iot-security-schemas). 

### <a name="send-security-message-api"></a>Enviar Mensagem de Segurança de API

O **Enviar Mensagens de Segurança** de API está disponível atualmente em C e C#.  

#### <a name="c-api"></a>API do C#

```cs

private static async Task SendSecurityMessageAsync(string messageContent)
{
    ModuleClient client = ModuleClient.CreateFromConnectionString("<connection_string>");
    Message  securityMessage = new Message(Encoding.UTF8.GetBytes(messageContent));
    securityMessage.SetAsSecurityMessage();
    await client.SendEventAsync(securityMessage);
}
```

#### <a name="c-api"></a>API do C

```c
bool SendMessageAsync(IoTHubAdapter* iotHubAdapter, const void* data, size_t dataSize) {
 
    bool success = true;
    IOTHUB_MESSAGE_HANDLE messageHandle = NULL;
 
    messageHandle = IoTHubMessage_CreateFromByteArray(data, dataSize);
 
    if (messageHandle == NULL) {
        success = false;
        goto cleanup;
    }
 
    if (IoTHubMessage_SetAsSecurityMessage(messageHandle) != IOTHUB_MESSAGE_OK) {
        success = false;
        goto cleanup;
    }
 
    if (IoTHubModuleClient_SendEventAsync(iotHubAdapter->moduleHandle, messageHandle, SendConfirmCallback, iotHubAdapter) != IOTHUB_CLIENT_OK) {
        success = false;
        goto cleanup;
    }
 
cleanup:
    if (messageHandle != NULL) {
        IoTHubMessage_Destroy(messageHandle);
    }
 
    return success;
}
 
static void SendConfirmCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback) {
    if (userContextCallback == NULL) {
        //error handling
        return;
    }
 
    if (result != IOTHUB_CLIENT_CONFIRMATION_OK){
        //error handling
    }
}
```

## <a name="next-steps"></a>Próximas etapas
- Leia a [Visão geral](overview.md) do serviço ASC para IoT
- Saiba mais sobre a [Arquitetura](architecture.md) da ASC para IoT
- Habilite o [serviço](quickstart-onboard-iot-hub.md)
- Leia as [Perguntas frequentes](resources-frequently-asked-questions.md)
- Aprenda a acessar [dados brutos de segurança](how-to-security-data-access.md)
- Entenda as [recomendações](concept-recommendations.md)
- Entenda os [alertas](concept-security-alerts.md)
