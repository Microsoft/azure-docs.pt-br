---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/24/2020
ms.openlocfilehash: 2eff30333362d461f196972fbaedbeac8f2ae7c9
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033812"
---
## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas neste artigo, você precisa do seguinte:

* Um aplicativo do Azure IoT Central criado usando o modelo de **Aplicativo personalizado**. Para obter mais informações, consulte o [Criar um início rápido de aplicativo](../articles/iot-central/core/quick-deploy-iot-central.md). O aplicativo precisa ter sido criado em 14 de julho de 2020 ou após essa data.
* Um computador de desenvolvimento com o [Python](https://www.python.org/) versão 3.7 ou posterior instalado. É possível executar `python --version` na linha de comando para verificar a versão. O Python está disponível para uma ampla variedade de sistemas operacionais. As instruções deste tutorial pressupõem que você esteja executando o comando **python** no prompt de comando do Windows.
* Uma cópia local do repositório GitHub do [SDK do Microsoft Azure IoT para Python](https://github.com/Azure/azure-iot-sdk-python) que contém o código de exemplo. Use este link para baixar uma cópia do repositório: [Baixar ZIP](https://github.com/Azure/azure-iot-sdk-python/archive/master.zip). Em seguida, descompacte o arquivo em uma localização adequada no computador local.

## <a name="review-the-code"></a>Examine o código

Na cópia do SDK do Microsoft Azure IoT para Python baixado anteriormente, abra o arquivo *azure-iot-sdk-python/azure-iot-device/samples/pnp/simple_thermostat.py* em um editor de texto.

Quando você executa o exemplo para se conectar ao IoT Central, ele usa o DPS (Serviço de Provisionamento de Dispositivos) para registrar o dispositivo e gerar uma cadeia de conexão. O exemplo recupera as informações de conexão do DPS necessárias por meio do ambiente de linha de comando.

A função `main`:

* Usa o DPS para provisionar o dispositivo. As informações de provisionamento incluem a ID do modelo. O IoT Central usa a ID do modelo para identificar ou gerar o modelo de dispositivo para este dispositivo. Para saber mais, confira [Associar um dispositivo a um modelo de dispositivo automaticamente](../articles/iot-central/core/concepts-get-connected.md#associate-a-device-with-a-device-template).
* Cria um objeto `Device_client` e define a ID do modelo `dtmi:com:example:Thermostat;1` antes de abrir a conexão.
* Envia a propriedade `maxTempSinceLastReboot` ao IoT Central.
* Cria um ouvinte para o comando `getMaxMinReport`.
* Cria o ouvinte de propriedade para escutar as atualizações de propriedade gravável.
* Inicia um loop para enviar a telemetria de temperatura a cada 10 segundos.

```python
async def main():
    switch = os.getenv("IOTHUB_DEVICE_SECURITY_TYPE")
    if switch == "DPS":
        provisioning_host = (
            os.getenv("IOTHUB_DEVICE_DPS_ENDPOINT")
            if os.getenv("IOTHUB_DEVICE_DPS_ENDPOINT")
            else "global.azure-devices-provisioning.net"
        )
        id_scope = os.getenv("IOTHUB_DEVICE_DPS_ID_SCOPE")
        registration_id = os.getenv("IOTHUB_DEVICE_DPS_DEVICE_ID")
        symmetric_key = os.getenv("IOTHUB_DEVICE_DPS_DEVICE_KEY")

        registration_result = await provision_device(
            provisioning_host, id_scope, registration_id, symmetric_key, model_id
        )

        if registration_result.status == "assigned":

            device_client = IoTHubDeviceClient.create_from_symmetric_key(
                symmetric_key=symmetric_key,
                hostname=registration_result.registration_state.assigned_hub,
                device_id=registration_result.registration_state.device_id,
                product_info=model_id,
            )
        else:
            raise RuntimeError(
                "Could not provision device. Aborting Plug and Play device connection."
            )

    elif switch == "connectionString":

        # ...

    # Connect the client.
    await device_client.connect()

    max_temp = 10.96  # Initial Max Temp otherwise will not pass certification
    await device_client.patch_twin_reported_properties({"maxTempSinceLastReboot": max_temp})

    listeners = asyncio.gather(
        execute_command_listener(
            device_client,
            method_name="getMaxMinReport",
            user_command_handler=max_min_handler,
            create_user_response_handler=create_max_min_report_response,
        ),
        execute_property_listener(device_client),
    )

    async def send_telemetry():
        global max_temp
        global min_temp
        current_avg_idx = 0

        while True:
            current_temp = random.randrange(10, 50)
            if not max_temp:
                max_temp = current_temp
            elif current_temp > max_temp:
                max_temp = current_temp

            if not min_temp:
                min_temp = current_temp
            elif current_temp < min_temp:
                min_temp = current_temp

            avg_temp_list[current_avg_idx] = current_temp
            current_avg_idx = (current_avg_idx + 1) % moving_window_size

            temperature_msg1 = {"temperature": current_temp}
            await send_telemetry_from_thermostat(device_client, temperature_msg1)
            await asyncio.sleep(8)

    send_telemetry_task = asyncio.create_task(send_telemetry())

    # ...
```

A função `provision_device` usa o DPS para provisionar o dispositivo e registrá-lo no IoT Central. A função inclui a ID do modelo do dispositivo, que o IoT Central usa para [associar um dispositivo a um modelo de dispositivo](../articles/iot-central/core/concepts-get-connected.md#associate-a-device-with-a-device-template), no conteúdo de provisionamento:

```python
async def provision_device(provisioning_host, id_scope, registration_id, symmetric_key, model_id):
    provisioning_device_client = ProvisioningDeviceClient.create_from_symmetric_key(
        provisioning_host=provisioning_host,
        registration_id=registration_id,
        id_scope=id_scope,
        symmetric_key=symmetric_key,
    )
    provisioning_device_client.provisioning_payload = {"modelId": model_id}
    return await provisioning_device_client.register()
```

A função `execute_command_listener` processa as solicitações de comando, executa a função `max_min_handler` quando o dispositivo recebe o comando `getMaxMinReport` e executa a função `create_max_min_report_response` para gerar a resposta:

```python
async def execute_command_listener(
    device_client, method_name, user_command_handler, create_user_response_handler
):
    while True:
        if method_name:
            command_name = method_name
        else:
            command_name = None

        command_request = await device_client.receive_method_request(command_name)
        print("Command request received with payload")
        print(command_request.payload)

        values = {}
        if not command_request.payload:
            print("Payload was empty.")
        else:
            values = command_request.payload

        await user_command_handler(values)

        response_status = 200
        response_payload = create_user_response_handler(values)

        command_response = MethodResponse.create_from_method_request(
            command_request, response_status, response_payload
        )

        try:
            await device_client.send_method_response(command_response)
        except Exception:
            print("responding to the {command} command failed".format(command=method_name))
```

A função `async def execute_property_listener` processa as atualizações de propriedade gravável, como `targetTemperature`, e gera a resposta JSON:

```python
async def execute_property_listener(device_client):
    ignore_keys = ["__t", "$version"]
    while True:
        patch = await device_client.receive_twin_desired_properties_patch()  # blocking call

        print("the data in the desired properties patch was: {}".format(patch))

        version = patch["$version"]
        prop_dict = {}

        for prop_name, prop_value in patch.items():
            if prop_name in ignore_keys:
                continue
            else:
                prop_dict[prop_name] = {
                    "ac": 200,
                    "ad": "Successfully executed patch",
                    "av": version,
                    "value": prop_value,
                }

        await device_client.patch_twin_reported_properties(prop_dict)
```

A função `send_telemetry_from_thermostat` envia as mensagens de telemetria ao IoT Central:

```python
async def send_telemetry_from_thermostat(device_client, telemetry_msg):
    msg = Message(json.dumps(telemetry_msg))
    msg.content_encoding = "utf-8"
    msg.content_type = "application/json"
    print("Sent message")
    await device_client.send_message(msg)
```

## <a name="get-connection-information"></a>Obter informações de conexão

[!INCLUDE [iot-central-connection-configuration](iot-central-connection-configuration.md)]

## <a name="run-the-code"></a>Executar o código

Para executar o aplicativo de exemplo, abra um ambiente de linha de comando e acesse a pasta *azure-iot-sdk-python/azure-iot-device/samples/pnp* que contém o arquivo de exemplo *simple_thermostat.py*.

[!INCLUDE [iot-central-connection-environment](iot-central-connection-environment.md)]

Instale os pacotes necessários:

```cmd/sh
pip install azure-iot-device
```

Execute o exemplo:

```cmd/sh
python simple_thermostat.py
```

A saída a seguir mostra o dispositivo se registrando e se conectando ao IoT Central. O exemplo envia a propriedade `maxTempSinceLastReboot` antes de começar a enviar a telemetria:

```cmd/sh
Device was assigned
iotc-.......azure-devices.net
sample-device-01
Listening for command requests and property updates
Press Q to quit
Sending telemetry for temperature
Sent message
Sent message
Sent message
```

[!INCLUDE [iot-central-monitor-thermostat](iot-central-monitor-thermostat.md)]

Você pode ver como o dispositivo responde a comandos e a atualizações de propriedade:

```cmd/sh
Sent message
the data in the desired properties patch was: {'targetTemperature': {'value': 86.3}, '$version': 2}
Sent message

...

Sent message
Command request received with payload
2020-10-14T08:00:00.000Z
Will return the max, min and average temperature from the specified time 2020-10-14T08:00:00.000Z to the current time
Done generating
{"avgTemp": 31.5, "endTime": "2020-10-16T10:07:41.580722", "maxTemp": 49, "minTemp": 12, "startTime": "2020-10-16T10:06:21.580632"}
```
