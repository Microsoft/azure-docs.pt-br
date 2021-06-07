---
title: Como criar um adaptador para a ponte de Plug and Play IoT | Microsoft Docs
description: Identifique os componentes do adaptador de ponte de Plug and Play de IoT. Saiba como estender a ponte escrevendo seu próprio adaptador.
author: usivagna
ms.author: ugans
ms.date: 1/20/2021
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 9a7028dfaeb94e87366de7acfa8cebc4c2f4c767
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98746816"
---
# <a name="extend-the-iot-plug-and-play-bridge"></a>Estender a ponte de Plug and Play IoT
A [ponte de plug and Play IOT](concepts-iot-pnp-bridge.md#iot-plug-and-play-bridge-architecture) permite que você conecte os dispositivos existentes anexados a um gateway ao Hub IOT. Você usa a ponte para mapear as interfaces de Plug and Play de IoT para os dispositivos anexados. Uma interface de Plug and Play IoT define a telemetria que um dispositivo envia, as propriedades sincronizadas entre o dispositivo e a nuvem e os comandos que o dispositivo responde. Você pode instalar e configurar o aplicativo de ponte de software livre em gateways Windows ou Linux. Além disso, a ponte pode ser executada como um módulo Azure IoT Edge Runtime.

Este artigo explica em detalhes como:

- Estenda a ponte de Plug and Play IoT com um adaptador.
- Implemente retornos de chamada comuns para um adaptador de ponte.

Para obter um exemplo simples que mostra como usar a ponte, consulte [como conectar o exemplo de ponte de plug and Play de IOT que é executado no Linux ou Windows para o Hub IOT](howto-use-iot-pnp-bridge.md).

As orientações e exemplos neste artigo pressupõem familiaridade básica com o [Azure digital gêmeos](../digital-twins/overview.md) e o [plug and Play de IOT](overview-iot-plug-and-play.md). Além disso, este artigo pressupõe familiaridade com a [criação e a implantação da ponte de plug and Play IOT](howto-build-deploy-extend-pnp-bridge.md).

## <a name="design-guide-to-extend-the-iot-plug-and-play-bridge-with-an-adapter"></a>Guia de design para estender a ponte de Plug and Play IoT com um adaptador

Para estender os recursos da ponte, você pode criar seus próprios adaptadores de ponte.

A ponte usa adaptadores para:

- Estabeleça uma conexão entre um dispositivo e a nuvem.
- Habilite o fluxo de dados entre um dispositivo e a nuvem.
- Habilite o gerenciamento de dispositivos da nuvem.

Cada adaptador de ponte deve:

- Crie uma interface gêmeos digital.
- Use a interface para associar a funcionalidade do lado do dispositivo a recursos baseados em nuvem, como telemetria, propriedades e comandos.
- Estabeleça a comunicação de controle e de dados com o hardware ou firmware do dispositivo.

Cada adaptador de ponte interage com um tipo específico de dispositivo com base em como o adaptador se conecta e interage com o dispositivo. Mesmo se a comunicação com um dispositivo usar um protocolo de handshake, um adaptador de ponte poderá ter várias maneiras de interpretar os dados do dispositivo. Nesse cenário, o adaptador de ponte usa informações para o adaptador no arquivo de configuração para determinar a *configuração de interface* que o adaptador deve usar para analisar os dados.

Para interagir com o dispositivo, um adaptador de ponte usa um protocolo de comunicação com suporte do dispositivo e as APIs fornecidas pelo sistema operacional subjacente ou pelo fornecedor do dispositivo.

Para interagir com a nuvem, um adaptador de ponte usa as APIs fornecidas pelo SDK do dispositivo IoT do Azure para enviar telemetria, criar interfaces de atualização digitais, enviar atualizações de propriedade e criar funções de retorno de chamada para atualizações de propriedade e comandos.

### <a name="create-a-bridge-adapter"></a>Criar um adaptador de ponte

A ponte espera que um adaptador de ponte implemente as APIs definidas na interface [_PNP_ADAPTER](https://github.com/Azure/iot-plug-and-play-bridge/blob/9964f7f9f77ecbf4db3b60960b69af57fd83a871/pnpbridge/src/pnpbridge/inc/pnpadapter_api.h#L296) :

```c
typedef struct _PNP_ADAPTER {
  // Identity of the IoT Plug and Play adapter that is retrieved from the config
  const char* identity;

  PNPBRIDGE_ADAPTER_CREATE createAdapter;
  PNPBRIDGE_COMPONENT_CREATE createPnpComponent;
  PNPBRIDGE_COMPONENT_START startPnpComponent;
  PNPBRIDGE_COMPONENT_STOP stopPnpComponent;
  PNPBRIDGE_COMPONENT_DESTROY destroyPnpComponent;
  PNPBRIDGE_ADAPTER_DESTOY destroyAdapter;
} PNP_ADAPTER, * PPNP_ADAPTER;
```

Nesta interface:

- `PNPBRIDGE_ADAPTER_CREATE` cria o adaptador e configura os recursos de gerenciamento de interface. Um adaptador também pode depender de parâmetros globais do adaptador para a criação do adaptador. Essa função é chamada uma vez para um único adaptador.
- `PNPBRIDGE_COMPONENT_CREATE` cria as interfaces de cliente de "digital" e associa as funções de retorno de chamada. O adaptador inicia o canal de comunicação com o dispositivo. O adaptador pode configurar os recursos para habilitar o fluxo de telemetria, mas não inicia a telemetria de relatório até que `PNPBRIDGE_COMPONENT_START` seja chamado. Essa função é chamada uma vez para cada componente de interface no arquivo de configuração.
- `PNPBRIDGE_COMPONENT_START` é chamado para permitir que o adaptador de ponte comece a encaminhar a telemetria do dispositivo para o cliente digital. Essa função é chamada uma vez para cada componente de interface no arquivo de configuração.
- `PNPBRIDGE_COMPONENT_STOP` interrompe o fluxo de telemetria.
- `PNPBRIDGE_COMPONENT_DESTROY` destrói o cliente de teledigital e os recursos de interface associados. Essa função é chamada uma vez para cada componente de interface no arquivo de configuração quando a ponte é interrompida ou quando ocorre um erro fatal.
- `PNPBRIDGE_ADAPTER_DESTROY` limpa os recursos do adaptador de ponte.

### <a name="bridge-core-interaction-with-bridge-adapters"></a>Interação de núcleo de ponte com adaptadores de ponte

A lista a seguir descreve o que acontece quando a ponte é iniciada:

1. Quando a ponte é iniciada, o Gerenciador do adaptador de ponte examina cada componente de interface definido no arquivo de configuração e chama `PNPBRIDGE_ADAPTER_CREATE` o adaptador apropriado. O adaptador pode usar parâmetros de configuração de adaptador global para configurar recursos para dar suporte a várias *configurações de interface*.
1. Para cada dispositivo no arquivo de configuração, o Gerenciador de ponte inicia a criação da interface chamando `PNPBRIDGE_COMPONENT_CREATE` no adaptador de ponte apropriado.
1. O adaptador recebe quaisquer definições de configuração de adaptador opcionais para o componente de interface e usa essas informações para configurar conexões com o dispositivo.
1. O adaptador cria as interfaces de cliente de atualização digital e associa as funções de retorno de chamada para atualizações de propriedade e comandos. O estabelecimento de conexões de dispositivo não deve bloquear o retorno dos retornos de chamada após a criação da interface de migração de troca de digital. A conexão de dispositivo ativa é independente do cliente de interface ativa que a ponte cria. Se uma conexão falhar, o adaptador assumirá que o dispositivo está inativo. O adaptador de ponte pode optar por tentar fazer essa conexão novamente.
1. Depois que o Gerenciador de adaptador de ponte cria todos os componentes de interface especificados no arquivo de configuração, ele registra todas as interfaces com o Hub IoT do Azure. O registro é uma chamada assíncrona e de bloqueio. Quando a chamada é concluída, ela dispara um retorno de chamada no adaptador de ponte que pode iniciar a manipulação de retornos de chamada de comando e propriedade da nuvem.
1. Em seguida, o Gerenciador do adaptador de ponte chama `PNPBRIDGE_INTERFACE_START` em cada componente e o adaptador de ponte começa a reportar a telemetria para o cliente digital.

### <a name="design-guidelines"></a>Diretrizes de design

Siga estas diretrizes ao desenvolver um novo adaptador de ponte:

- Determine quais recursos de dispositivo têm suporte e a aparência da definição de interface dos componentes que usam esse adaptador.
- Determine qual interface e parâmetros globais seu adaptador precisa definir no arquivo de configuração.
- Identifique a comunicação de dispositivo de nível baixo necessária para dar suporte às propriedades e aos comandos do componente.
- Determine como o adaptador deve analisar os dados brutos do dispositivo e convertê-los nos tipos de telemetria que a definição de interface de IoT Plug and Play especifica.
- Implemente a interface do adaptador de ponte descrita anteriormente.
- Adicione o novo adaptador ao manifesto do adaptador e Compile a ponte.

### <a name="enable-a-new-bridge-adapter"></a>Habilitar um novo adaptador de ponte

Você habilita adaptadores na ponte adicionando uma referência em [adapter_manifest. c](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/src/adapters/src/shared/adapter_manifest.c):

```c
  extern PNP_ADAPTER MyPnpAdapter;
  PPNP_ADAPTER PNP_ADAPTER_MANIFEST[] = {
    .
    .
    &MyPnpAdapter
  }
```

> [!IMPORTANT]
> Os retornos de chamada do adaptador de ponte são invocados sequencialmente. Um adaptador não deve bloquear um retorno de chamada porque isso impede que o núcleo da ponte faça progressos.

### <a name="sample-camera-adapter"></a>Adaptador de câmera de exemplo

O [Leiame do adaptador de câmera](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/src/adapters/src/Camera/readme.md) descreve um adaptador de câmera de exemplo que você pode habilitar.

## <a name="code-examples-for-common-adapter-scenarioscallbacks"></a>Exemplos de código para os cenários/retornos de chamada do adaptador comum

A seção a seguir fornecerá detalhes sobre como um adaptador para a ponte implementaria retornos de chamada para vários cenários e usos comuns. esta seção aborda os seguintes retornos de chamada:
- [Receber atualização da propriedade (nuvem para dispositivo)](#receive-property-update-cloud-to-device)
- [Relatar uma atualização de propriedade (dispositivo para nuvem)](#report-a-property-update-device-to-cloud)
- [Enviar telemetria (dispositivo para nuvem)](#send-telemetry-device-to-cloud)
- [Receber o retorno de chamada de atualização de comando da nuvem e processá-lo no lado do dispositivo (nuvem para dispositivo)](#receive-command-update-callback-from-the-cloud-and-process-it-on-the-device-side-cloud-to-device)
- [Responder à atualização de comando no lado do dispositivo (dispositivo para nuvem)](#respond-to-command-update-on-the-device-side-device-to-cloud)

Os exemplos a seguir são baseados no [adaptador de exemplo do sensor ambiental](https://github.com/Azure/iot-plug-and-play-bridge/tree/master/pnpbridge/src/adapters/samples/environmental_sensor).

### <a name="receive-property-update-cloud-to-device"></a>Receber atualização da propriedade (nuvem para dispositivo)
A primeira etapa é registrar uma função de retorno de chamada:

```c
PnpComponentHandleSetPropertyUpdateCallback(BridgeComponentHandle, EnvironmentSensor_ProcessPropertyUpdate);
```
A próxima etapa é implementar a função de retorno de chamada para ler a atualização de propriedade no dispositivo:

```c
void EnvironmentSensor_ProcessPropertyUpdate(
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle,
    const char* PropertyName,
    JSON_Value* PropertyValue,
    int version,
    void* userContextCallback
)
{
  // User context for the callback is set to the IoT Hub client handle, and therefore can be type-cast to the client handle type
    SampleEnvironmentalSensor_ProcessPropertyUpdate(userContextCallback, PropertyName, PropertyValue, version, PnpComponentHandle);
}

// SampleEnvironmentalSensor_ProcessPropertyUpdate receives updated properties from the server.  This implementation
// acts as a simple dispatcher to the functions to perform the actual processing.
void SampleEnvironmentalSensor_ProcessPropertyUpdate(
    void * ClientHandle,
    const char* PropertyName,
    JSON_Value* PropertyValue,
    int version,
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle)
{
  if (strcmp(PropertyName, sampleEnvironmentalSensorPropertyBrightness) == 0)
    {
        SampleEnvironmentalSensor_BrightnessCallback(ClientHandle, PropertyName, PropertyValue, version, PnpComponentHandle);
    }
    else
    {
        // If the property is not implemented by this interface, presently we only record a log message but do not have a mechanism to report back to the service
        LogError("Environmental Sensor Adapter:: Property name <%s> is not associated with this interface", PropertyName);
    }
}

// Process a property update for bright level.
static void SampleEnvironmentalSensor_BrightnessCallback(
    void * ClientHandle,
    const char* PropertyName,
    JSON_Value* PropertyValue,
    int version,
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle)
{
    IOTHUB_CLIENT_RESULT iothubClientResult;
    STRING_HANDLE jsonToSend = NULL;
    char targetBrightnessString[32];

    LogInfo("Environmental Sensor Adapter:: Brightness property invoked...");

    PENVIRONMENT_SENSOR EnvironmentalSensor = PnpComponentHandleGetContext(PnpComponentHandle);

    if (json_value_get_type(PropertyValue) != JSONNumber)
    {
        LogError("JSON field %s is not a number", PropertyName);
    }
    else if(EnvironmentalSensor == NULL || EnvironmentalSensor->SensorState == NULL)
    {
        LogError("Environmental sensor device context not initialized correctly.");
    }
    else if (SampleEnvironmentalSensor_ValidateBrightness(json_value_get_number(PropertyValue)))
    {
        EnvironmentalSensor->SensorState->brightness = (int) json_value_get_number(PropertyValue);
        if (snprintf(targetBrightnessString, sizeof(targetBrightnessString), 
            g_environmentalSensorBrightnessResponseFormat, EnvironmentalSensor->SensorState->brightness) < 0)
        {
            LogError("Unable to create target brightness string for reporting result");
        }
        else if ((jsonToSend = PnP_CreateReportedPropertyWithStatus(EnvironmentalSensor->SensorState->componentName,
                    PropertyName, targetBrightnessString, PNP_STATUS_SUCCESS, g_environmentalSensorPropertyResponseDescription,
                    version)) == NULL)
        {
            LogError("Unable to build reported property response");
        }
        else
        {
            const char* jsonToSendStr = STRING_c_str(jsonToSend);
            size_t jsonToSendStrLen = strlen(jsonToSendStr);

            if ((iothubClientResult = SampleEnvironmentalSensor_RouteReportedState(ClientHandle, PnpComponentHandle, (const unsigned char*)jsonToSendStr, jsonToSendStrLen,
                                        SampleEnvironmentalSensor_PropertyCallback,
                                        (void*) &EnvironmentalSensor->SensorState->brightness)) != IOTHUB_CLIENT_OK)
            {
                LogError("Environmental Sensor Adapter:: SampleEnvironmentalSensor_RouteReportedState for brightness failed, error=%d", iothubClientResult);
            }
            else
            {
                LogInfo("Environmental Sensor Adapter:: Successfully queued Property update for Brightness for component=%s", EnvironmentalSensor->SensorState->componentName);
            }

            STRING_delete(jsonToSend);
        }
    }
}

```

### <a name="report-a-property-update-device-to-cloud"></a>Relatar uma atualização de propriedade (dispositivo para nuvem)
A qualquer momento depois que o componente é criado, o dispositivo pode relatar Propriedades para a nuvem com o status: 
```c
// Environmental sensor's read-only property, device state indiciating whether its online or not
//
static const char sampleDeviceStateProperty[] = "state";
static const unsigned char sampleDeviceStateData[] = "true";
static const int sampleDeviceStateDataLen = sizeof(sampleDeviceStateData) - 1;

// Sends a reported property for device state of this simulated device.
IOTHUB_CLIENT_RESULT SampleEnvironmentalSensor_ReportDeviceStateAsync(
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle,
    const char * ComponentName)
{

    IOTHUB_CLIENT_RESULT iothubClientResult = IOTHUB_CLIENT_OK;
    STRING_HANDLE jsonToSend = NULL;

    if ((jsonToSend = PnP_CreateReportedProperty(ComponentName, sampleDeviceStateProperty, (const char*) sampleDeviceStateData)) == NULL)
    {
        LogError("Unable to build reported property response for propertyName=%s, propertyValue=%s", sampleDeviceStateProperty, sampleDeviceStateData);
    }
    else
    {
        const char* jsonToSendStr = STRING_c_str(jsonToSend);
        size_t jsonToSendStrLen = strlen(jsonToSendStr);

        if ((iothubClientResult = SampleEnvironmentalSensor_RouteReportedState(NULL, PnpComponentHandle, (const unsigned char*)jsonToSendStr, jsonToSendStrLen,
            SampleEnvironmentalSensor_PropertyCallback, (void*)sampleDeviceStateProperty)) != IOTHUB_CLIENT_OK)
        {
            LogError("Environmental Sensor Adapter:: Unable to send reported state for property=%s, error=%d",
                                sampleDeviceStateProperty, iothubClientResult);
        }
        else
        {
            LogInfo("Environmental Sensor Adapter:: Sending device information property to IoTHub. propertyName=%s, propertyValue=%s",
                        sampleDeviceStateProperty, sampleDeviceStateData);
        }

        STRING_delete(jsonToSend);
    }

    return iothubClientResult;
}


// Routes the reported property for device or module client. This function can be called either by passing a valid client handle or by passing
// a NULL client handle after components have been started such that the client handle can be extracted from the PnpComponentHandle
IOTHUB_CLIENT_RESULT SampleEnvironmentalSensor_RouteReportedState(
    void * ClientHandle,
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle,
    const unsigned char * ReportedState,
    size_t Size,
    IOTHUB_CLIENT_REPORTED_STATE_CALLBACK ReportedStateCallback,
    void * UserContextCallback)
{
    IOTHUB_CLIENT_RESULT iothubClientResult = IOTHUB_CLIENT_OK;

    PNP_BRIDGE_CLIENT_HANDLE clientHandle = (ClientHandle != NULL) ?
            (PNP_BRIDGE_CLIENT_HANDLE) ClientHandle : PnpComponentHandleGetClientHandle(PnpComponentHandle);

    if ((iothubClientResult = PnpBridgeClient_SendReportedState(clientHandle, ReportedState, Size,
            ReportedStateCallback, UserContextCallback)) != IOTHUB_CLIENT_OK)
    {
        LogError("IoTHub client call to _SendReportedState failed with error code %d", iothubClientResult);
        goto exit;
    }
    else
    {
        LogInfo("IoTHub client call to _SendReportedState succeeded");
    }

exit:
    return iothubClientResult;
}

```

### <a name="send-telemetry-device-to-cloud"></a>Enviar telemetria (dispositivo para nuvem)
```c
//
// SampleEnvironmentalSensor_SendTelemetryMessagesAsync is periodically invoked by the caller to
// send telemetry containing the current temperature and humidity (in both cases random numbers
// so this sample will work on platforms without these sensors).
//
IOTHUB_CLIENT_RESULT SampleEnvironmentalSensor_SendTelemetryMessagesAsync(
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle)
{
    IOTHUB_CLIENT_RESULT result = IOTHUB_CLIENT_OK;
    IOTHUB_MESSAGE_HANDLE messageHandle = NULL;
    PENVIRONMENT_SENSOR device = PnpComponentHandleGetContext(PnpComponentHandle);

    float currentTemperature = 20.0f + ((float)rand() / RAND_MAX) * 15.0f;
    float currentHumidity = 60.0f + ((float)rand() / RAND_MAX) * 20.0f;

    char currentMessage[128];
    sprintf(currentMessage, "{\"%s\":%.3f, \"%s\":%.3f}", SampleEnvironmentalSensor_TemperatureTelemetry, 
            currentTemperature, SampleEnvironmentalSensor_HumidityTelemetry, currentHumidity);


    if ((messageHandle = PnP_CreateTelemetryMessageHandle(device->SensorState->componentName, currentMessage)) == NULL)
    {
        LogError("Environmental Sensor Adapter:: PnP_CreateTelemetryMessageHandle failed.");
    }
    else if ((result = SampleEnvironmentalSensor_RouteSendEventAsync(PnpComponentHandle, messageHandle,
            SampleEnvironmentalSensor_TelemetryCallback, device)) != IOTHUB_CLIENT_OK)
    {
        LogError("Environmental Sensor Adapter:: SampleEnvironmentalSensor_RouteSendEventAsync failed, error=%d", result);
    }

    IoTHubMessage_Destroy(messageHandle);

    return result;
}

// Routes the sending asynchronous events for device or module client
IOTHUB_CLIENT_RESULT SampleEnvironmentalSensor_RouteSendEventAsync(
        PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle,
        IOTHUB_MESSAGE_HANDLE EventMessageHandle,
        IOTHUB_CLIENT_EVENT_CONFIRMATION_CALLBACK EventConfirmationCallback,
        void * UserContextCallback)
{
    IOTHUB_CLIENT_RESULT iothubClientResult = IOTHUB_CLIENT_OK;
    PNP_BRIDGE_CLIENT_HANDLE clientHandle = PnpComponentHandleGetClientHandle(PnpComponentHandle);
    if ((iothubClientResult = PnpBridgeClient_SendEventAsync(clientHandle, EventMessageHandle,
            EventConfirmationCallback, UserContextCallback)) != IOTHUB_CLIENT_OK)
    {
        LogError("IoTHub client call to _SendEventAsync failed with error code %d", iothubClientResult);
        goto exit;
    }
    else
    {
        LogInfo("IoTHub client call to _SendEventAsync succeeded");
    }

exit:
    return iothubClientResult;
}

```
### <a name="receive-command-update-callback-from-the-cloud-and-process-it-on-the-device-side-cloud-to-device"></a>Receber o retorno de chamada de atualização de comando da nuvem e processá-lo no lado do dispositivo (nuvem para dispositivo)
```c
// SampleEnvironmentalSensor_ProcessCommandUpdate receives commands from the server.  This implementation acts as a simple dispatcher
// to the functions to perform the actual processing.
int SampleEnvironmentalSensor_ProcessCommandUpdate(
    PENVIRONMENT_SENSOR EnvironmentalSensor,
    const char* CommandName,
    JSON_Value* CommandValue,
    unsigned char** CommandResponse,
    size_t* CommandResponseSize)
{
    if (strcmp(CommandName, sampleEnvironmentalSensorCommandBlink) == 0)
    {
        return SampleEnvironmentalSensor_BlinkCallback(EnvironmentalSensor, CommandValue, CommandResponse, CommandResponseSize);
    }
    else if (strcmp(CommandName, sampleEnvironmentalSensorCommandTurnOn) == 0)
    {
        return SampleEnvironmentalSensor_TurnOnLightCallback(EnvironmentalSensor, CommandValue, CommandResponse, CommandResponseSize);
    }
    else if (strcmp(CommandName, sampleEnvironmentalSensorCommandTurnOff) == 0)
    {
        return SampleEnvironmentalSensor_TurnOffLightCallback(EnvironmentalSensor, CommandValue, CommandResponse, CommandResponseSize);
    }
    else
    {
        // If the command is not implemented by this interface, by convention we return a 404 error to server.
        LogError("Environmental Sensor Adapter:: Command name <%s> is not associated with this interface", CommandName);
        return SampleEnvironmentalSensor_SetCommandResponse(CommandResponse, CommandResponseSize, sampleEnviromentalSensor_NotImplemented);
    }
}

// Implement the callback to process the command "blink". Information pertaining to the request is
// specified in the CommandValue parameter, and the callback fills out data it wishes to
// return to the caller on the service in CommandResponse.

static int SampleEnvironmentalSensor_BlinkCallback(
    PENVIRONMENT_SENSOR EnvironmentalSensor,
    JSON_Value* CommandValue,
    unsigned char** CommandResponse,
    size_t* CommandResponseSize)
{
    int result = PNP_STATUS_SUCCESS;
    int BlinkInterval = 0;

    LogInfo("Environmental Sensor Adapter:: Blink command invoked. It has been invoked %d times previously", EnvironmentalSensor->SensorState->numTimesBlinkCommandCalled);

    if (json_value_get_type(CommandValue) != JSONNumber)
    {
        LogError("Cannot retrieve blink interval for blink command");
        result = PNP_STATUS_BAD_FORMAT;
    }
    else
    {
        BlinkInterval = (int)json_value_get_number(CommandValue);
        LogInfo("Environmental Sensor Adapter:: Blinking with interval=%d second(s)", BlinkInterval);
        EnvironmentalSensor->SensorState->numTimesBlinkCommandCalled++;
        EnvironmentalSensor->SensorState->blinkInterval = BlinkInterval;

        result = SampleEnvironmentalSensor_SetCommandResponse(CommandResponse, CommandResponseSize, sampleEnviromentalSensor_BlinkResponse);
    }

    return result;
}

```
### <a name="respond-to-command-update-on-the-device-side-device-to-cloud"></a>Responder à atualização de comando no lado do dispositivo (dispositivo para nuvem)

```c
    static int SampleEnvironmentalSensor_BlinkCallback(
        PENVIRONMENT_SENSOR EnvironmentalSensor,
        JSON_Value* CommandValue,
        unsigned char** CommandResponse,
        size_t* CommandResponseSize)
    {
        int result = PNP_STATUS_SUCCESS;
        int BlinkInterval = 0;
    
        LogInfo("Environmental Sensor Adapter:: Blink command invoked. It has been invoked %d times previously", EnvironmentalSensor->SensorState->numTimesBlinkCommandCalled);
    
        if (json_value_get_type(CommandValue) != JSONNumber)
        {
            LogError("Cannot retrieve blink interval for blink command");
            result = PNP_STATUS_BAD_FORMAT;
        }
        else
        {
            BlinkInterval = (int)json_value_get_number(CommandValue);
            LogInfo("Environmental Sensor Adapter:: Blinking with interval=%d second(s)", BlinkInterval);
            EnvironmentalSensor->SensorState->numTimesBlinkCommandCalled++;
            EnvironmentalSensor->SensorState->blinkInterval = BlinkInterval;
    
            result = SampleEnvironmentalSensor_SetCommandResponse(CommandResponse, CommandResponseSize, sampleEnviromentalSensor_BlinkResponse);
        }
    
        return result;
    }
    
    // SampleEnvironmentalSensor_SetCommandResponse is a helper that fills out a command response
    static int SampleEnvironmentalSensor_SetCommandResponse(
        unsigned char** CommandResponse,
        size_t* CommandResponseSize,
        const unsigned char* ResponseData)
    {
        int result = PNP_STATUS_SUCCESS;
        if (ResponseData == NULL)
        {
            LogError("Environmental Sensor Adapter:: Response Data is empty");
            *CommandResponseSize = 0;
            return PNP_STATUS_INTERNAL_ERROR;
        }
    
        *CommandResponseSize = strlen((char*)ResponseData);
        memset(CommandResponse, 0, sizeof(*CommandResponse));
    
        // Allocate a copy of the response data to return to the invoker. Caller will free this.
        if (mallocAndStrcpy_s((char**)CommandResponse, (char*)ResponseData) != 0)
        {
            LogError("Environmental Sensor Adapter:: Unable to allocate response data");
            result = PNP_STATUS_INTERNAL_ERROR;
        }
    
        return result;
}
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a ponte de Plug and Play IoT, visite o repositório GitHub do [iot plug and Play Bridge](https://github.com/Azure/iot-plug-and-play-bridge) .
