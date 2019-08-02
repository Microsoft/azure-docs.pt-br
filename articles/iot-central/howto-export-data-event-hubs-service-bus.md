---
title: Exportar dados para os Hubs de Eventos do Azure e o Barramento de Serviço do Azure | Microsoft Docs
description: Como exportar dados de seu aplicativo do Azure IoT Central para os Hubs de Eventos do Azure e o Barramento de Serviço do Azure
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 07/09/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: c6f10352646350152c5aac795885231697e81fe7
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850204"
---
# <a name="export-your-data-in-azure-iot-central"></a>Exportar dados no Azure IoT Central

*Este tópico aplica-se aos administradores.*

Este artigo descreve como usar o recurso de exportação de dados contínuos no Azure IoT Central para exportar seus dados para seus próprios **hubs de eventos do Azure**e instâncias **do barramento de serviço do Azure** . Exporte **medidas**, **dispositivos** e **modelos de dispositivo** para seu próprio destino para obter insights e análises de caminhos frequentes. Isso inclui disparar regras personalizadas no Azure Stream Analytics, disparar fluxos de trabalho personalizados nos Aplicativos Lógicos do Azure ou transformar os dados e passá-los pelo Azure Functions. 

> [!Note]
> Mais uma vez, ao ativar a exportação contínua de dados, você obtém apenas os dados desse momento em diante. Atualmente, não é possível recuperar dados por um tempo quando a exportação contínua de dados for desativada. Para reter dados mais históricos, ative a exportação contínua de dados no início.


## <a name="prerequisites"></a>Pré-requisitos

- Você precisa ser um administrador do aplicativo IoT Central

## <a name="set-up-export-destination"></a>Configurar o destino de exportação

Se você não tiver um hub de eventos/barramento de serviço existente para exportar para o, siga estas etapas:

## <a name="create-event-hubs-namespace"></a>Criar um namespace dos Hubs de Eventos

1. Crie um [namespace dos Hubs de Eventos no portal do Azure](https://ms.portal.azure.com/#create/Microsoft.EventHub). Saiba mais na [documentação dos Hubs de Eventos do Azure](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).
2. Escolha uma assinatura. 

    > [!Note] 
    > Agora você pode exportar dados para outras assinaturas que **não são a mesma** usada para o aplicativo IoT Central pago conforme o uso. Você se conectará usando uma cadeia de conexão, nesse caso.
3. Crie um hub de eventos em seu namespace dos Hubs de Eventos. Acesse o namespace e selecione **+ Hub de Eventos** na parte superior para criar uma instância de hub de eventos.

## <a name="create-service-bus-namespace"></a>Criar um namespace do Barramento de Serviço

1. Crie um [namespace do Barramento de Serviço no portal do Azure](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5). Saiba mais na [documentação do Barramento de Serviço do Azure](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-create-namespace-portal).
2. Escolha uma assinatura. 

    > [!Note] 
    > Agora você pode exportar dados para outras assinaturas que **não são a mesma** usada para o aplicativo IoT Central pago conforme o uso. Você se conectará usando uma cadeia de conexão, nesse caso.

3. Acesse o namespace do Barramento de Serviço e selecione **+ Fila** ou **+ Tópico** na parte superior para criar uma fila ou um tópico para o qual exportar.


## <a name="set-up-continuous-data-export"></a>Configuração da exportação contínua de dados

Agora que você tem um destino de barramento de serviço/hubs de eventos para exportar dados para o, siga estas etapas para configurar a exportação de dados contínuas. 

1. Entre no aplicativo IoT Central.

2. No menu à esquerda, selecione **exportação de dados contínuas**.

    > [!Note]
    > Se a Exportação Contínua de Dados não for exibida no menu à esquerda, isso indicará que você não é um administrador no aplicativo. Contate o administrador para configurar a exportação de dados.

    ![Criar novo Hub de Eventos cde](media/howto-export-data/export_menu1.png)

3. Selecione o botão **+ novo** no canto superior direito. Escolha um dos **hubs de eventos do Azure** ou o **barramento de serviço do Azure** como o destino de sua exportação. 

    > [!NOTE] 
    > O número máximo de exportações por aplicativo é cinco. 

    ![Criar exportação contínua de dados](media/howto-export-data/export_new1.png)

4. Na caixa de listagem suspensa, selecione o namespace do seu namespace de **hubs de eventos/barramento de serviço**. Você também pode escolher a última opção da lista, **Inserir uma cadeia de conexão**. 

    > [!NOTE] 
    > Você verá apenas as Contas de Armazenamento/os namespace dos Hubs de Eventos/namespaces do Barramento de Serviço na **mesma assinatura do aplicativo IoT Central**. Caso deseje exportar para um destino fora dessa assinatura, escolha **Inserir uma cadeia de conexão** e veja a etapa 5.

    > [!NOTE] 
    > Para aplicativos de avaliação de 7 dias, a única maneira de configurar a exportação contínua de dados é por meio de uma cadeia de conexão. Isso ocorre porque os aplicativos de avaliação de 7 dias não têm uma assinatura do Azure associada.

    ![Criar novo Hub de Eventos cde](media/howto-export-data/export_create1.png)

5. (Opcional) Se você escolher **Inserir uma cadeia de conexão**, uma nova caixa será exibida para que você cole a cadeia de conexão. Para obter a cadeia de conexão para:
    - Hubs de eventos ou barramento de serviço, acesse o namespace na portal do Azure.
        - Em **configurações**, selecione **políticas de acesso compartilhado**
        - Escolha a **RootManageSharedAccessKey** padrão ou crie uma
        - Copie a cadeia de conexão primária ou secundária
 
6. Escolha um hub de eventos/fila ou tópico na caixa de listagem suspensa.

7. Em **Dados a serem exportados**, especifique cada tipo de dados a serem exportados, definindo o tipo para **Ativar**.

6. Para ativar a exportação contínua de dados, verifique se a opção **Exportação de dados** está **Ativada**. Clique em **Salvar**.

    ![Configure a exportação contínua de dados](media/howto-export-data/export_list1.png)

7. Após alguns minutos, seus dados serão exibidos no destino escolhido.


## <a name="export-to-azure-event-hubs-and-azure-service-bus"></a>Exportar para os Hubs de Eventos do Azure e o Barramento de Serviço do Azure

Os dados de medidas, dispositivos e modelos de dispositivo são exportados para o hub de eventos ou para uma fila ou tópico do Barramento de Serviço quase em tempo real. Os dados de medidas contêm a totalidade da mensagem enviada pelos dispositivos para o IoT Central, não apenas os valores das medidas propriamente ditos. Os dados de dispositivos exportados contêm alterações em propriedades e configurações de todos os dispositivos. Os modelos de dispositivo exportados contêm as alterações em todos os modelos de dispositivo. Os dados exportados estão na propriedade "body" e no formato JSON.

> [!NOTE]
> Ao escolher um Barramento de Serviço como destino de exportação, as filas e tópicos **não podem ter Sessões ou Detecção de Duplicidades habilitadas**. Se alguma dessas opções estiver habilitada, algumas mensagens não chegarão à sua fila ou tópico.

### <a name="measurements"></a>Medidas

Uma nova mensagem é exportada rapidamente após a IoT Central receber a mensagem de um dispositivo. Cada mensagem exportada nos Hubs de Eventos e no Barramento de Serviço contém a mensagem completa que o dispositivo enviou na propriedade "body" no formato JSON. 

> [!NOTE]
> Os dispositivos que enviaram as medidas são representados por IDs de dispositivo (veja as seções abaixo). Para obter os nomes dos dispositivos, exporte dados do dispositivo e correlacione cada mensagem usando a **connectionDeviceId** que corresponde à **deviceId** da mensagem do dispositivo.

O exemplo a seguir mostra uma mensagem sobre dados de medidas recebida no hub de eventos ou na fila ou tópico do Barramento de Serviço.

```json
{
  "body": {
    "humidity": 29.06963648666288,
    "temp": 8.4503795661685,
    "pressure": 1075.8334910110093,
    "magnetometerX": 408.6966458887116,
    "magnetometerY": -532.8809796603962,
    "magnetometerZ": 174.70653875528205,
    "accelerometerX": 1481.546749013788,
    "accelerometerY": -1139.4316656437406,
    "accelerometerZ": 811.6928695575307,
    "gyroscopeX": 442.19879163299856,
    "gyroscopeY": 123.23710975717177,
    "gyroscopeZ": 708.5397575786151,
    "deviceState": "DANGER"
  },
  "annotations": {
    "iothub-connection-device-id": "<connectionDeviceId>",
    "iothub-connection-auth-method": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "iothub-connection-auth-generation-id": "<generationId>",
    "iothub-enqueuedtime": 1539381029965,
    "iothub-message-source": "Telemetry",
    "x-opt-sequence-number": 25325,
    "x-opt-offset": "<offset>",
    "x-opt-enqueued-time": 1539381030200
  },
  "sequenceNumber": 25325,
  "enqueuedTimeUtc": "2018-10-12T21:50:30.200Z",
  "offset": "<offset>",
  "properties": {
    "content_type": "application/json",
    "content_encoding": "utf-8"
  }
}
```

### <a name="devices"></a>Dispositivos

Mensagens que contêm dados do dispositivo são enviadas ao seu hub de eventos ou fila ou tópico do Barramento de Serviço em intervalos de alguns minutos. Isso significa que a cada intervalo de alguns minutos, um lote de mensagens chegará com os dados sobre:
- Novos dispositivos que foram adicionados
- Dispositivos com valores alterados de propriedades e de configurações

Cada mensagem representa uma ou mais alterações em um dispositivo desde a última mensagem exportada. As informações que serão enviadas em cada mensagem incluem:
- `id` do dispositivo na IoT Central
- `name` do dispositivo
- `deviceId` do [Serviço de Provisionamento de Dispositivos](https://aka.ms/iotcentraldocsdps)
- Informações de modelo de dispositivo
- Valores de propriedade
- Valores de configuração

> [!NOTE]
> Dispositivos excluídos desde o último lote não são exportados. Atualmente, não há indicadores em mensagens exportadas para dispositivos excluídos.
>
> O modelo de dispositivo ao qual cada dispositivo pertence é representado por uma ID de modelo de dispositivo. Para obter o nome do modelo do dispositivo, exporte os dados do modelo de dispositivo também.

O exemplo a seguir mostra uma mensagem sobre dados do dispositivo no hub de eventos ou na fila ou tópico do Barramento de Serviço:


```json
{
  "body": {
    "id": "<id>",
    "name": "<deviceName>",
    "simulated": true,
    "deviceId": "<deviceId>",
    "deviceTemplate": {
      "id": "<templateId>",
      "version": "1.0.0"
    },
    "properties": {
      "cloud": {
        "location": "Seattle"
      },
      "device": {
        "dieNumber": 5445.5862873026645
      }
    },
    "settings": {
      "device": {
        "fanSpeed": 0
      }
    }
  },
  "annotations": {
    "iotcentral-message-source": "devices",
    "x-opt-partition-key": "<partitionKey>",
    "x-opt-sequence-number": 39740,
    "x-opt-offset": "<offset>",
    "x-opt-enqueued-time": 1539274959654
  },
  "partitionKey": "<partitionKey>",
  "sequenceNumber": 39740,
  "enqueuedTimeUtc": "2018-10-11T16:22:39.654Z",
  "offset": "<offset>",
}
```

### <a name="device-templates"></a>Modelos de dispositivo

Mensagens que contêm dados de modelo do dispositivo são enviadas ao seu hub de eventos ou fila ou tópico do Barramento de Serviço em intervalos de alguns minutos. Isso significa que a cada intervalo de alguns minutos, um lote de mensagens chegará com os dados sobre:
- Novos modelos de dispositivo que foram adicionados
- Modelos de dispositivo com definições de medidas, de propriedades e de configurações alteradas

Cada mensagem representa uma ou mais alterações em um modelo de dispositivo desde a última mensagem exportada. As informações que serão enviadas em cada mensagem incluem:
- `id` do modelo de dispositivo
- `name` do modelo de dispositivo
- `version` do modelo de dispositivo
- Tipos de dados de medição e valores mínimos/máximos
- Tipos de dados de propriedade e valores padrão
- Configuração de tipos de dados e valores padrão

> [!NOTE]
> Modelos de dispositivo excluídos desde o último lote não são exportados. Atualmente, não há indicadores em mensagens exportadas para modelos de dispositivos excluídos.

O exemplo a seguir mostra uma mensagem sobre dados de modelo do dispositivo no hub de eventos ou na fila ou tópico do Barramento de Serviço:

```json
{
  "body": {
    "id": "<id>",
    "version": "1.0.0",
    "name": "<templateName>",
    "measurements": {
      "telemetry": {
        "humidity": {
          "dataType": "double",
          "name": "humidity"
        },
        "pressure": {
          "dataType": "double",
          "name": "pressure"
        },
        "temp": {
          "dataType": "double",
          "name": "temperature"
        }
      }
    },
    "properties": {
      "cloud": {
        "location": {
          "dataType": "string",
          "name": "Location"
        }
      },
      "device": {
        "dieNumber": {
          "dataType": "double",
          "name": "Die Number"
        }
      }
    },
    "settings": {
      "device": {
        "fanSpeed": {
          "dataType": "double",
          "name": "Fan Speed",
          "initialValue": 0
        }
      }
    }
  },
  "annotations": {
    "iotcentral-message-source": "deviceTemplates",
    "x-opt-partition-key": "<partitionKey>",
    "x-opt-sequence-number": 25315,
    "x-opt-offset": "<offset>",
    "x-opt-enqueued-time": 1539274985085
  },
  "partitionKey": "<partitionKey>",
  "sequenceNumber": 25315,
  "enqueuedTimeUtc": "2018-10-11T16:23:05.085Z",
  "offset": "<offset>",
}
```

## <a name="next-steps"></a>Próximas etapas

Agora que você sabe como exportar seus dados para os Hubs de Eventos do Azure e o Barramento de Serviço do Azure, continue para a próxima etapa:

> [!div class="nextstepaction"]
> [Como disparar Azure Functions](howto-trigger-azure-functions.md)
