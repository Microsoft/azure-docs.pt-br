---
title: Como transferir uma carga entre o dispositivo e o serviço de provisionamento de dispositivos do Azure
description: Este documento descreve como transferir uma carga entre dispositivo e serviço de provisionamento de dispositivos (DPS)
author: menchi
ms.author: menchi
ms.date: 02/11/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: a3ee7f3fca3fff1cd401f26489b01fb9cc4e09c5
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99259512"
---
# <a name="how-to-transfer-payloads-between-devices-and-dps"></a>Como transferir cargas entre dispositivos e o DPS
Às vezes, o DPS precisa de mais dados de dispositivos para provisioná-los corretamente no Hub IoT correto, e esses dados precisam ser fornecidos pelo dispositivo. Vice-versa, o DPS pode retornar dados para o dispositivo para facilitar as lógicas do lado do cliente. 

## <a name="when-to-use-it"></a>Quando usar isso
Esse recurso pode ser usado como um aprimoramento para a [alocação personalizada](./how-to-use-custom-allocation-policies.md). Por exemplo, você deseja alocar seus dispositivos com base no modelo de dispositivo sem intervenção humana. Nesse caso, você usará a [alocação personalizada](./how-to-use-custom-allocation-policies.md). Você pode configurar o dispositivo para relatar as informações do modelo como parte da [chamada do dispositivo de registro](/rest/api/iot-dps/runtimeregistration/registerdevice). O DPS passará a carga do dispositivo para o webhook de alocação personalizada. E sua função pode decidir a qual Hub IoT este dispositivo será usado quando receber informações de modelo do dispositivo. Da mesma forma, se o webhook quiser retornar alguns dados para o dispositivo, ele passará os dados de volta como uma cadeia de caracteres na resposta do webhook.  

## <a name="device-sends-data-payload-to-dps"></a>O dispositivo envia a carga de dados para o DPS
Quando o dispositivo estiver enviando uma [chamada de dispositivo de registro](/rest/api/iot-dps/runtimeregistration/registerdevice) para o DPS, a chamada de registro poderá ser aprimorada para usar outros campos no corpo. O corpo é semelhante ao seguinte: 
   ```
   { 
       “registrationId”: “mydevice”, 
       “tpm”:                
       { 
           “endorsementKey”: “stuff”, 
           “storageRootKey”: “things” 
       }, 
       “payload”: “your additional data goes here. It can be nested JSON.” 
    } 
   ```

## <a name="dps-returns-data-to-the-device"></a>O DPS retorna dados para o dispositivo
Se o webhook da diretiva de alocação personalizada quiser retornar alguns dados para o dispositivo, ele passará os dados de volta como uma cadeia de caracteres na resposta do webhook. A alteração está na seção carga abaixo. 
   ```
   { 
       "iotHubHostName": "sample-iot-hub-1.azure-devices.net", 
       "initialTwin": { 
           "tags": { 
               "tag1": true 
               }, 
               "properties": { 
                   "desired": { 
                       "tag2": true 
                    } 
                } 
            }, 
        "payload": "whatever is returned by the webhook" 
    } 
   ```

## <a name="sdk-support"></a>Suporte a SDK
Esse recurso está disponível em [SDKs de cliente](./index.yml)em C, C#, JAVA e Node.js.  

## <a name="next-steps"></a>Próximas etapas
* Desenvolver usando o [SDK do IoT do Azure]( https://github.com/Azure/azure-iot-sdks) para o Hub IoT do Azure e o Serviço de Provisionamento de Dispositivos Hub IoT do Azure