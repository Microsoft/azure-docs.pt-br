---
title: Entender a atualização do dispositivo para o arquivo de configuração do Hub IoT do Azure | Microsoft Docs
description: Entender a atualização do dispositivo para o arquivo de configuração do Hub IoT do Azure.
author: ValOlson
ms.author: valls
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 39ecd9d6d0deec942d5c8cce9357c779a4b328d3
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101661737"
---
# <a name="device-update-for-iot-hub-configuration-file"></a>Atualização de dispositivo para o arquivo de configuração do Hub IoT

O "adu-conf.txt" é um arquivo opcional que pode ser criado para gerenciar as configurações a seguir.

* AzureDeviceUpdateCore: 4. ClientMetadata: 4. deviceproperties ["fabricante"]
* AzureDeviceUpdateCore: 4. ClientMetadata: 4. deviceproperties ["modelo"]
* DeviceInformation. manufacturer
* DeviceInformation. Model
* Cadeia de conexão do dispositivo (se não for conhecida pelo agente de atualização de dispositivo).

## <a name="purpose"></a>Finalidade
Primeiro, o agente de atualização de dispositivo tentará obter os `manufacturer` `model` valores e do dispositivo para usar na [camada de interface](device-update-agent-overview.md#the-interface-layer). Se isso falhar, o agente de atualização de dispositivo será exibido na próxima vez para o arquivo "adu-conf.txt" e usará os valores de lá. Se ambas as tentativas não forem bem-sucedidas, o agente de atualização de dispositivo usará valores [padrão](https://github.com/Azure/iot-hub-device-update/blob/main/CMakeLists.txt) .

Saiba mais sobre a interface do [Adu Core](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/adu_core_interface) e a [interface de informações do dispositivo](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/device_info_interface).

## <a name="file-location"></a>Local do arquivo

No sistema Linux, na partição ou no disco chamado `adu` , crie um arquivo de texto chamado "adu-conf.txt" com os campos a seguir.

## <a name="list-of-fields"></a>Lista de campos

|Name|Descrição|
|-----------|--------------------|
|connection_string|Cadeia de conexão pré-provisionada que o dispositivo pode usar para se conectar ao Hub IoT. Observação: não será necessário se você estiver Provisionando o agente de atualização de dispositivo por meio do [serviço de identidade do Azure IOT](https://azure.github.io/iot-identity-service/)|
|aduc_manufacturer|Relatado pela `AzureDeviceUpdateCore:4.ClientMetadata:4` interface para classificar o dispositivo para direcionar a implantação de atualização.|
|aduc_model|Relatado pela `AzureDeviceUpdateCore:4.ClientMetadata:4` interface para classificar o dispositivo para direcionar a implantação de atualização.|
|fabricante|Relatado pelo agente de atualização de dispositivo como parte da `DeviceInformation` interface.|
|modelo|Relatado pelo agente de atualização de dispositivo como parte da `DeviceInformation` interface.|

## <a name="example-adu-conftxt-file-contents"></a>Conteúdo do arquivo de exemplo "adu-conf.txt"

```markdown

connection_string = `HostName=<yourIoTHubName>;DeviceId=<yourDeviceId>;SharedAccessKey=<yourSharedAccessKey>`
aduc_manufacturer = <value to send through `AzureDeviceUpdateCore:4.ClientMetadata:4.deviceProperties["manufacturer"]`
aduc_model = <value to send through `AzureDeviceUpdateCore:4.ClientMetadata:4.deviceProperties["model"]`
manufacturer = <value to send through `DeviceInformation.manufacturer`
model = <value to send through `DeviceInformation.manufacturer`
```
