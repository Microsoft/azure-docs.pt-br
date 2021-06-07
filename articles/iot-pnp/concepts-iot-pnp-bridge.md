---
title: Ponte de Plug and Play IoT | Microsoft Docs
description: Entenda a ponte de Plug and Play IoT e como usá-la para conectar dispositivos existentes conectados a um gateway Windows ou Linux como dispositivos de IoT Plug and Play.
author: usivagna
ms.author: ugans
ms.date: 1/20/2021
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: a45efd90043ecb4d457db7ed39651f1a9b5bbd4d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98890600"
---
# <a name="iot-plug-and-play-bridge"></a>Ponte do IoT Plug and Play

A ponte de Plug and Play IoT é um aplicativo de software livre para conectar dispositivos existentes conectados ao gateway do Windows ou Linux como dispositivos de IoT Plug and Play. Depois de instalar e configurar o aplicativo em seu computador Windows ou Linux, você pode usá-lo para conectar dispositivos anexados a um hub IoT. Você pode usar a ponte para mapear interfaces de Plug and Play de IoT para a telemetria os dispositivos anexados estão enviando, trabalhar com propriedades de dispositivo e invocar comandos.

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-high-level.png" alt-text="No lado esquerdo, há alguns sensores existentes anexados (com e sem fio) a um PC Windows ou Linux que contém a ponte de Plug and Play IoT. Em seguida, a ponte de Plug and Play IoT se conecta a um hub IoT no lado direito":::

A ponte IoT Plug and Play pode ser implantada como um executável autônomo em qualquer dispositivo IoT, PC industrial, servidor ou gateway que executa o Windows 10 ou Linux. Ele também pode ser compilado no código do aplicativo. Um arquivo JSON de configuração simples informa ao IoT Plug and Play Bridge quais dispositivos/periféricos anexados devem ser expostos até o Azure.

## <a name="supported-protocols-and-sensors"></a>Protocolos e sensores com suporte

A ponte de Plug and Play IoT dá suporte aos seguintes tipos de periféricos por padrão, com links para a documentação do adaptador:

|Periférico|Windows|Linux|
|---------|---------|---------|
|O [adaptador do sensor Bluetooth](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/bluetooth_sensor_adapter.md) conecta os sensores de ble (Bluetooth de baixa energia) detectados.       |Sim|Não|
|[Adaptador de câmera](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/camera_adapter.md) conecta câmeras em um dispositivo Windows 10.               |Sim|Não|
|O [adaptador Modbus](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/modbus_adapters.md) conecta sensores em um dispositivo Modbus.              |Sim|Sim|
|O [adaptador MQTT](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/mqtt_adapter.md) conecta dispositivos que usam um agente MQTT.                  |Sim|Sim|
|O [adaptador SerialPnP](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/serialpnp/Readme.md) conecta dispositivos que se comunicam por meio de uma conexão serial.               |Sim|Sim|
|Os [periféricos USB do Windows](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/coredevicehealth_adapter.md) usam uma lista de classes de interface de dispositivo com suporte a adaptador para conectar dispositivos que têm uma ID de hardware específica.  |Sim|Não Aplicável|

Para saber como estender a ponte de Plug and Play IoT para dar suporte a protocolos de dispositivo adicionais, consulte [estender a ponte de plug and Play IOT](howto-author-pnp-bridge-adapter.md). Para saber como criar e implantar a ponte de Plug and Play IoT, consulte [criar e implantar a ponte de plug and Play IOT](howto-build-deploy-extend-pnp-bridge.md).

## <a name="iot-plug-and-play-bridge-architecture"></a>Arquitetura de ponte de Plug and Play IoT

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-components.png" alt-text="No lado esquerdo, há várias caixas indicando vários periféricos conectados a um PC com Windows ou Linux que contém a ponte de Plug and Play IoT. Na parte superior, uma caixa rotulada pontos de configuração para a ponte. Em seguida, a ponte se conecta a um hub IoT no lado direito do diagrama.":::

### <a name="iot-plug-and-play-bridge-adapters"></a>Adaptadores de ponte de Plug and Play IoT

A ponte de Plug and Play IoT dá suporte a um conjunto de adaptadores de ponte de Plug and Play IoT para vários tipos de dispositivo. Um *manifesto de adaptador* define estaticamente os adaptadores para uma ponte.

O Gerenciador do adaptador de ponte usa o manifesto para identificar e chamar funções de adaptador. O Gerenciador de adaptador só chama a função CREATE nos adaptadores de ponte que são necessários para os componentes de interface listados no arquivo de configuração. Uma instância de adaptador é criada para cada componente de Plug and Play de IoT.

Um adaptador de ponte cria e adquire um identificador de interface de entrelaçamento digital. O adaptador usa esse identificador para associar a funcionalidade do dispositivo ao teledigital.

Usando as informações no arquivo de configuração, o adaptador de ponte usa as seguintes técnicas para habilitar o dispositivo completo para comunicação de alta do digital através da ponte:

- Estabelece um canal de comunicação diretamente.
- Cria um observador de dispositivo para aguardar que um canal de comunicação fique disponível.

### <a name="configuration-file"></a>Arquivo de configuração

A ponte de Plug and Play IoT usa um arquivo de configuração baseado em JSON que especifica:

- Como se conectar a um aplicativo de Hub IoT ou IoT Central: as opções incluem cadeias de conexão, parâmetros de autenticação ou DPS (serviço de provisionamento de dispositivos).
- O local dos modelos de recurso de Plug and Play de IoT que a ponte usa. O modelo define os recursos de um dispositivo de Plug and Play IoT e é estático e imutável.
- Uma lista de componentes de interface de Plug and Play IoT e as seguintes informações para cada componente:
- A ID da interface e o nome do componente.
- O adaptador de ponte necessário para interagir com o componente.
- Informações de dispositivo que o adaptador de ponte precisa para estabelecer comunicação com o dispositivo. Por exemplo, ID de hardware ou informações específicas para um adaptador, interface ou protocolo.
- Um subtipo de adaptador de ponte opcional ou configuração de interface se o adaptador oferecer suporte a vários tipos de comunicação com dispositivos semelhantes. O exemplo mostra como um componente de sensor Bluetooth pode ser configurado:

    ```json
    {
      "_comment": "Component BLE sensor",
      "pnp_bridge_component_name": "blesensor1",
      "pnp_bridge_adapter_id": "bluetooth-sensor-pnp-adapter",
      "pnp_bridge_adapter_config": {
        "bluetooth_address": "267541100483311",
        "blesensor_identity" : "Blesensor1"
      }
    }
    ```

- Uma lista opcional de parâmetros do adaptador de ponte global. Por exemplo, o adaptador de ponte do sensor Bluetooth tem um dicionário de configurações com suporte. Um componente de interface que requer o adaptador do sensor Bluetooth pode escolher uma dessas configurações como a seguinte `blesensor_identity` :

    ```json
    {
      "pnp_bridge_adapter_global_configs": {
        "bluetooth-sensor-pnp-adapter": {
          "Blesensor1" : {
            "company_id": "0x499",
            "endianness": "big",
            "telemetry_descriptor": [
              {
                "telemetry_name": "humidity",
                "data_parse_type": "uint8",
                "data_offset": 1,
                "conversion_bias": 0,
                "conversion_coefficient": 0.5
              },
              {
                "telemetry_name": "temperature",
                "data_parse_type": "int8",
                "data_offset": 2,
                "conversion_bias": 0,
                "conversion_coefficient": 1.0
              },
              {
                "telemetry_name": "pressure",
                "data_parse_type": "int16",
                "data_offset": 4,
                "conversion_bias": 0,
                "conversion_coefficient": 1.0
              },
              {
                "telemetry_name": "acceleration_x",
                "data_parse_type": "int16",
                "data_offset": 6,
                "conversion_bias": 0,
                "conversion_coefficient": 0.00980665
              },
              {
                "telemetry_name": "acceleration_y",
                "data_parse_type": "int16",
                "data_offset": 8,
                "conversion_bias": 0,
                "conversion_coefficient": 0.00980665
              },
              {
                "telemetry_name": "acceleration_z",
                "data_parse_type": "int16",
                "data_offset": 10,
                "conversion_bias": 0,
                "conversion_coefficient": 0.00980665
              }
            ]
          }
        }
      }
    }
    ```

## <a name="download-iot-plug-and-play-bridge"></a>Baixar ponte de Plug and Play IoT

Você pode baixar uma versão pré-criada da ponte com adaptadores com suporte nas [versões do IoT plug and Play Bridge](https://github.com/Azure/iot-plug-and-play-bridge/releases) e expandir a lista de ativos para a versão mais recente. Baixe a versão mais recente do aplicativo para seu sistema operacional.

Você também pode baixar e exibir o código-fonte do [IoT plug and Play Bridge no GitHub](https://github.com/Azure/iot-plug-and-play-bridge).

## <a name="next-steps"></a>Próximas etapas

Agora que você tem uma visão geral da arquitetura do IoT Plug and Play Bridge, as próximas etapas são para saber mais sobre:

- [Como conectar um exemplo de ponte IoT Plug and Play em execução no Linux ou Windows para o Hub IoT](./howto-use-iot-pnp-bridge.md)
- [Criar e implantar a ponte do IoT Plug and Play](howto-build-deploy-extend-pnp-bridge.md)
- [Estender a ponte do IoT Plug and Play](howto-author-pnp-bridge-adapter.md)
- [Ponte de Plug and Play IoT no GitHub](https://github.com/Azure/iot-plug-and-play-bridge)
