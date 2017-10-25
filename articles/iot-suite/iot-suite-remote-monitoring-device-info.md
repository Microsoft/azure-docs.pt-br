---
title: "Metadados de informações de dispositivo na solução de monitoramento remoto | Microsoft Docs"
description: "Uma descrição do monitoramento remoto pré-configurado da solução de IoT do Azure e sua arquitetura."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 1b334769-103b-4eb0-a293-184f3d1ba9a3
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: dobett
ms.openlocfilehash: f8fd452806a0a0b98cf8e434c9bd55700083a6c5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="device-information-metadata-in-the-remote-monitoring-preconfigured-solution"></a>Metadados de informações de dispositivo na solução pré-configurada de monitoramento remoto

A solução pré-configurada de monitoramento remoto do Azure IoT Suite demonstra uma abordagem para o gerenciamento de metadados de dispositivo. Este artigo descreve a abordagem utilizada por essa solução para que você possa compreender:

* Quais metadados de dispositivo a solução armazena.
* Como a solução gerencia os metadados de dispositivo.

## <a name="context"></a>Contexto

A solução pré-configurada de monitoramento remoto usa o [Hub IoT do Azure][lnk-iot-hub] para permitir que os dispositivos enviem dados para a nuvem. A solução armazena informações sobre os dispositivos em três locais diferentes:

| Local padrão | Informações armazenadas | Implementação |
| -------- | ------------------ | -------------- |
| Registro de identidade | Id do dispositivo, chaves de autenticação,estado habilitado | Integrado ao Hub IoT |
| Dispositivos gêmeos | Metadados: propriedades relatadas, propriedades desejadas, marcas | Integrado ao Hub IoT |
| Cosmos DB | Histórico de comandos e de métodos | Personalizada para a solução |

O Hub IoT inclui um [registro de identidade do dispositivo][lnk-identity-registry] para gerenciar o acesso a um hub IoT e usa [dispositivos gêmeos][lnk-device-twin] para gerenciar metadados de dispositivo. Também há um *registro do dispositivo* remoto específico da solução de monitoramento que armazena o histórico de comandos e de métodos. A solução de monitoramento remoto usa um banco de dados do [Cosmos DB][lnk-docdb] para implementar um repositório personalizado para o histórico de comandos e de métodos.

> [!NOTE]
> A solução pré-configurada de monitoramento remoto mantém o Registro de identidade do dispositivo em sincronização com as informações do banco de dados do Cosmos DB. Ambos usam a mesma id de dispositivo para identificar exclusivamente cada dispositivo conectado ao seu hub IoT.

## <a name="device-metadata"></a>Metadados de dispositivo

O Hub IoT mantém um [dispositivo gêmeo][lnk-device-twin] para cada dispositivo simulado e físico conectados a uma solução de monitoramento remota. A solução usa dispositivos gêmeos para gerenciar os metadados associados a dispositivos. Um dispositivo gêmeo é um documento JSON mantido pelo Hub IoT, e a solução usa a API do Hub IoT para interagir com os dispositivos gêmeos.

Um dispositivo gêmeo armazena três tipos de metadados:

- As *propriedades relatadas* são enviadas para um hub IoT por um dispositivo. Na solução de monitoramento remota, os dispositivos simulados enviam propriedades relatadas na inicialização e em resposta a comandos e métodos de **Alterar estado do dispositivo**. Você pode exibir as propriedades relatadas na **Lista de dispositivos** e em **Detalhes do dispositivo** no portal de solução. As propriedades relatadas são somente leitura.
- As *propriedades desejadas* são recuperadas do hub IoT pelos dispositivos. É responsabilidade do dispositivo para alterar qualquer configuração necessária no dispositivo. Também é responsabilidade do dispositivo para relatar a alteração para o hub como uma propriedade relatado. Você pode definir um valor de propriedade desejada no portal de solução.
- As *marcas* só existem no dispositivo gêmeo e nunca são sincronizadas com um dispositivo. Você pode definir valores de marca no portal de solução e usá-los quando você filtrar a lista de dispositivos. A solução também usa uma marca para identificar o ícone a ser exibido para um dispositivo no portal de solução.

Exemplo relatado as propriedades dos dispositivos simuladas incluem fabricante, modelo, latitude e longitude. Os dispositivos simulados também retornam a lista de métodos com suporte como uma propriedade relatada.

> [!NOTE]
> O código do dispositivo simulado usa apenas as propriedades **Desired.Config.TemperatureMeanValue** e **Desired.Config.TelemetryInterval** desejadas para atualizar as propriedades relatadas enviadas de volta ao Hub IoT. Todas as outras solicitações de alteração de propriedade desejadas são ignoradas.

Um documento JSON de metadados de informações de dispositivo armazenado no banco de dados do Cosmos DB do Registro do dispositivo tem a seguinte estrutura:

```json
{
  "DeviceProperties": {
    "DeviceID": "deviceid1",
    "HubEnabledState": null,
    "CreatedTime": "2016-04-25T23:54:01.313802Z",
    "DeviceState": "normal",
    "UpdatedTime": null
    },
  "SystemProperties": {
    "ICCID": null
  },
  "Commands": [],
  "CommandHistory": [],
  "IsSimulatedDevice": false,
  "id": "fe81a81c-bcbc-4970-81f4-7f12f2d8bda8"
}
```

> [!NOTE]
> As informações de dispositivo também podem incluir metadados para descrever a telemetria que o dispositivo envia ao Hub IoT. A solução de monitoramento remoto usa esses metadados de telemetria para personalizar a forma como o painel exibe a [telemetria dinâmica][lnk-dynamic-telemetry].

## <a name="lifecycle"></a>Ciclo de vida

Quando você cria um dispositivo no portal da solução pela primeira vez, a solução cria uma entrada no banco de dados Cosmos DB para armazenar o histórico de comandos e de métodos. Neste ponto, a solução também cria uma entrada para o dispositivo no registro de identidade do dispositivo, que gera as chaves que o dispositivo usa para se autenticar no Hub IoT. Ele também cria um dispositivo gêmeo.

Quando um dispositivo se conecta pela primeira vez à solução, ele envia as propriedades relatadas e uma mensagem de informações de dispositivo. Os valores de propriedade relatados são salvos automaticamente no dispositivo gêmeo. As propriedades relatadas incluem o fabricante do dispositivo, número do modelo, número de série e uma lista dos métodos com suporte. A mensagem de informações de dispositivo inclui a lista dos comandos aos quais o dispositivo oferece suporte, incluindo informações sobre quaisquer parâmetros de comando. Quando a solução recebe essa mensagem, ela atualiza as informações do dispositivo no banco de dados do Cosmos DB.

### <a name="view-and-edit-device-information-in-the-solution-portal"></a>Exibir e editar informações de dispositivo no portal da solução

A lista de dispositivos no portal da solução exibe por padrão as seguintes propriedades de dispositivo como colunas: **Status**, **ID do dispositivo**, **Fabricante**, **Número do Modelo**, **Número de Série**, **Firmware**, **Plataforma**, **Processador** e **RAM Instalada**. Você pode personalizar as colunas clicando em **Editor de colunas**. As propriedades do dispositivo **Latitude** e **Longitude** direcionam a localização no Bing Mapas no painel.

![Editor de colunas na lista de dispositivos][img-device-list]

No painel **Detalhes do Dispositivo** no portal de solução, você pode editar as propriedades a as marcas desejadas (as propriedades relatadas são somente leitura).

![Painel de detalhes do dispositivo][img-device-edit]

Você pode usar o portal da solução para remover um dispositivo da sua solução. Quando você remover um dispositivo, a solução remove a entrada do registro de identidade e, em seguida, exclui o dispositivo gêmeo. A solução também remove as informações relacionadas ao dispositivo do banco de dados do Cosmos DB. Antes de remover um dispositivo, você deverá desabilitá-lo.

![Remover o dispositivo][img-device-remove]

## <a name="device-information-message-processing"></a>Processamento de mensagens de informações de dispositivo

As mensagens de informações sobre um dispositivo enviadas por um dispositivo são diferentes das mensagens de telemetria. As mensagens de informações de dispositivo incluem os comandos que um dispositivo pode responder a e qualquer histórico de comandos. O Hub IoT em si não tem conhecimento dos metadados contidos em uma mensagem de informações de dispositivo e processa a mensagem da mesma forma que processa todas as mensagens de dispositivo para a nuvem. Na solução de monitoramento remoto, um trabalho do [Stream Analytics do Azure][lnk-stream-analytics] (ASA) lê as mensagens do Hub IoT. O trabalho do Stream Analytics **DeviceInfo** filtra mensagens que contêm **"ObjectType": "DeviceInfo"** e as encaminha para a instância de host **EventProcessorHost** executada em um trabalho Web. A lógica na instância **EventProcessorHost** usa a ID do dispositivo para localizar o dispositivo específico no registro do Cosmos DB e atualizar o registro.

> [!NOTE]
> Uma mensagem de informações de dispositivo é uma mensagem padrão do dispositivo para a nuvem. A solução faz distinção entre as mensagens de informações de dispositivo e as mensagens de telemetria por meio de consultas ASA.

## <a name="next-steps"></a>Próximas etapas

Agora que você acabou de aprender como personalizar as soluções pré-configuradas, explore outros recursos das soluções pré-configuradas do IoT Suite:

* [Visão geral da solução pré-configurada de manutenção preditiva][lnk-predictive-overview]
* [Perguntas frequentes sobre o IoT Suite][lnk-faq]
* [Segurança IoT desde o início][lnk-security-groundup]

<!-- Images and links -->
[img-device-list]: media/iot-suite-remote-monitoring-device-info/image1.png
[img-device-edit]: media/iot-suite-remote-monitoring-device-info/image2.png
[img-device-remove]: media/iot-suite-remote-monitoring-device-info/image3.png

[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-identity-registry]: ../iot-hub/iot-hub-devguide-identity-registry.md
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-docdb]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-dynamic-telemetry]: iot-suite-dynamic-telemetry.md

[lnk-predictive-overview]: iot-suite-predictive-overview.md
[lnk-faq]: iot-suite-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md
