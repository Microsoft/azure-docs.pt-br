---
title: Azure IoT Edge e IoT Central do Azure | Microsoft Docs
description: Entenda como usar Azure IoT Edge com um aplicativo IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 02/19/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom:
- device-developer
- iot-edge
ms.openlocfilehash: e0f3464420c5cb429f780999bf5983b2ab142567
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102608624"
---
# <a name="connect-azure-iot-edge-devices-to-an-azure-iot-central-application"></a>Conectar dispositivos Azure IoT Edge a um aplicativo do Azure IoT Central

*Este artigo se aplica a construtores de solução e desenvolvedores de dispositivos.*

Azure IoT Edge move a análise de nuvem e a lógica de negócios personalizada para dispositivos para que sua organização possa se concentrar nas informações de negócios em vez do gerenciamento de dados. Escale horizontalmente sua solução de IoT empacotando sua lógica de negócios em contêineres padrão, implante esses contêineres em seus dispositivos e monitore-os na nuvem.

Este artigo descreve:

* Como IoT Edge dispositivos se conectam a um aplicativo IoT Central.
* Como usar IoT Central para gerenciar seus dispositivos IoT Edge.

Para saber mais sobre IoT Edge, confira [o que é Azure IOT Edge?](../../iot-edge/about-iot-edge.md)

## <a name="iot-edge"></a>IoT Edge

O IoT Edge é composto por três componentes:

* Os *módulos do IoT Edge* são contêineres que executam serviços do Azure, serviços do parceiro ou o seu próprio código. Os módulos são implantados em dispositivos do IoT Edge e executados localmente nesses dispositivos. Para saber mais, veja [entender Azure IOT Edge módulos](../../iot-edge/iot-edge-modules.md).
* O *tempo de execução do IOT Edge* é executado em cada dispositivo IOT Edge e gerencia os módulos implantados em cada dispositivo. O tempo de execução consiste em dois módulos IoT Edge: *IOT Edge agente* e *IOT Edge Hub*. Para saber mais, confira [entender o tempo de execução de Azure IOT Edge e sua arquitetura](../../iot-edge/iot-edge-runtime.md).
* Uma *interface baseada em nuvem* permite monitorar e gerenciar dispositivos do IoT Edge remotamente. IoT Central é um exemplo de uma interface de nuvem.

Um dispositivo IoT Edge pode ser:

* Um dispositivo autônomo composto por módulos.
* Um *dispositivo de gateway*, com dispositivos downstream se conectando a ele.

## <a name="iot-edge-as-a-gateway"></a>IoT Edge como um gateway

Um dispositivo IoT Edge pode operar como um gateway que fornece uma conexão entre outros dispositivos downstream na rede e seu aplicativo IoT Central.

Há dois padrões de gateway:

* No padrão de *gateway transparente* , o módulo hub de IOT Edge se comporta como IOT central e manipula conexões de dispositivos registrados em IOT central. As mensagens passam de dispositivos downstream para IoT Central como se não houver nenhum gateway entre eles.

* No padrão de *Gateway de tradução* , os dispositivos que não podem se conectar ao IOT central por conta própria, conectem-se a um módulo de IOT Edge personalizado em vez disso. O módulo no dispositivo IoT Edge processa mensagens de dispositivo downstream de entrada e as encaminha para IoT Central.

Os padrões de gateway transparentes e de tradução não são mutuamente exclusivos. Um único dispositivo IoT Edge pode funcionar como um gateway transparente e um gateway de conversão.

Para saber mais sobre os padrões de gateway de IoT Edge, consulte [como um dispositivo de IOT Edge pode ser usado como um gateway](../../iot-edge/iot-edge-as-gateway.md).

### <a name="downstream-device-relationships-with-a-gateway-and-modules"></a>Relações de dispositivo downstream com um gateway e módulos

Os dispositivos downstream podem se conectar a um dispositivo de gateway IoT Edge por meio do módulo de *Hub de IOT Edge*  . Nesse cenário, o dispositivo IoT Edge é um gateway transparente:

:::image type="content" source="media/concepts-iot-edge/gateway-transparent.png" alt-text="Diagrama de gateway transparente" border="false":::

Os dispositivos downstream também podem se conectar a um dispositivo de gateway do IoT Edge por meio de um módulo personalizado. No cenário a seguir, os dispositivos downstream se conectam por meio de um módulo personalizado *Modbus* . Nesse cenário, o dispositivo IoT Edge é um gateway de conversão:

:::image type="content" source="media/concepts-iot-edge/gateway-module.png" alt-text="Diagrama da conexão de módulo personalizado" border="false":::

O diagrama a seguir mostra as conexões com um dispositivo IoT Edge Gateway por meio de ambos os tipos de módulos. Nesse cenário, o dispositivo IoT Edge é um gateway de conversão e transparente:

:::image type="content" source="media/concepts-iot-edge/gateway-module-transparent.png" alt-text="Diagrama de conexão usando ambos os módulos de conexão" border="false":::

Os dispositivos downstream podem se conectar a um dispositivo de gateway IoT Edge por meio de vários módulos personalizados. O diagrama a seguir mostra os dispositivos downstream que se conectam por meio de um módulo personalizado MODBUS, um módulo personalizado BLE e o módulo *hub IOT Edge*  :

:::image type="content" source="media/concepts-iot-edge/gateway-two-modules-transparent.png" alt-text="Diagrama de conexão usando vários módulos personalizados" border="false":::

## <a name="iot-edge-devices-and-iot-central"></a>Dispositivos IoT Edge e IoT Central

IoT Edge dispositivos podem usar tokens de *assinatura de acesso compartilhado* ou certificados X. 509 para autenticar com IOT central. Você pode registrar manualmente seus dispositivos IoT Edge no IoT Central antes que eles se conectem pela primeira vez ou usar o serviço de provisionamento de dispositivos para lidar com o registro. Para saber mais, confira [Conectar-se ao Azure IoT Central](concepts-get-connected.md).

IoT Central usa [modelos de dispositivo](concepts-device-templates.md) para definir como o IOT central interage com um dispositivo. Por exemplo, um modelo de dispositivo especifica:

* Os tipos de telemetria e propriedades que um dispositivo envia para que IoT Central possa interpretá-los e criar visualizações.
* Os comandos aos quais um dispositivo responde para que IoT Central possa exibir uma interface do usuário para que um operador Use para chamar os comandos.

Um dispositivo IoT Edge pode enviar telemetria, sincronizar valores de propriedade e responder a comandos da mesma maneira que um dispositivo padrão. Portanto, um dispositivo IoT Edge precisa de um modelo de dispositivo no IoT Central.

### <a name="iot-edge-device-templates"></a>Modelos de dispositivo IoT Edge

IoT Central modelos de dispositivo usam modelos para descrever os recursos dos dispositivos. O diagrama a seguir mostra a estrutura do modelo para um dispositivo IoT Edge:

:::image type="content" source="media/concepts-iot-edge/iot-edge-model.png" alt-text="Estrutura do modelo para IoT Edge dispositivo conectado a IoT Central" border="false":::

O IoT Central modela um dispositivo IoT Edge da seguinte maneira:

* Cada modelo de dispositivo IoT Edge tem um modelo de funcionalidade.
* Para cada módulo personalizado listado no manifesto de implantação, um modelo de funcionalidade de módulo é gerado.
* Uma relação é estabelecida entre cada modelo de funcionalidade de módulo e um modelo de dispositivo.
* Um modelo de funcionalidade de módulo implementa uma ou mais interfaces de módulo.
* Cada interface de módulo contém telemetria, propriedades e comandos.

### <a name="iot-edge-deployment-manifests-and-iot-central-device-templates"></a>IoT Edge manifestos de implantação e modelos de dispositivo de IoT Central

No IoT Edge, você implanta e gerencia a lógica de negócios na forma de módulos. Os módulos de IoT Edge são a menor unidade de computação gerenciada pelo IoT Edge e podem conter serviços do Azure, como Azure Stream Analytics, ou seu próprio código específico da solução.

Um *manifesto de implantação* IOT Edge lista os módulos de IOT Edge a serem implantados no dispositivo e como configurá-los. Para saber mais, consulte [saiba como implantar módulos e estabelecer rotas em IOT Edge](../../iot-edge/module-composition.md).

No Azure IoT Central, você importa um manifesto de implantação para criar um modelo de dispositivo para o dispositivo IoT Edge.

O trecho de código a seguir mostra um exemplo IoT Edge manifesto de implantação:

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
          "type": "docker",
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {}
          }
        },
        "systemModules": {
          "edgeAgent": {
            "type": "docker",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0.9",
              "createOptions": "{}"
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0.9",
              "createOptions": "{}"
            }
          }
        },
        "modules": {
          "SimulatedTemperatureSensor": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
              "createOptions": "{}"
            }
          }
        }
      }
    },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {
            "route": "FROM /* INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 7200
        }
      }
    },
    "SimulatedTemperatureSensor": {
      "properties.desired": {
           "SendData": true,
           "SendInterval": 10
      }
    }
  }
}
```

No trecho anterior, você pode ver:

* Há três módulos. O *agente de IOT Edge* e os módulos de sistema de *IOT Edge Hub* que estão presentes em cada manifesto de implantação. O módulo **SimulatedTemperatureSensor** personalizado.
* As imagens do módulo público são extraídas de um repositório do registro de contêiner do Azure que não requer nenhuma credencial para se conectar. Para imagens de módulo particular, defina as credenciais de registro de contêiner a serem usadas na `registryCredentials` configuração para o módulo *IOT Edge Agent* .
* O módulo **SimulatedTemperatureSensor** personalizado tem duas propriedades `"SendData": true` e `"SendInterval": 10` .

Quando você importa esse manifesto de implantação para um aplicativo IoT Central, ele gera o seguinte modelo de dispositivo:

:::image type="content" source="media/concepts-iot-edge/device-template.png" alt-text="Captura de tela mostrando o modelo de dispositivo criado no manifesto de implantação.":::

Na captura de tela anterior, você pode ver:

* Um módulo chamado **SimulatedTemperatureSensor**. O *agente de IOT Edge* e os módulos de sistema de *IOT Edge Hub* não aparecem no modelo.
* Uma interface chamada **Gerenciamento** que inclui duas propriedades graváveis chamadas **SendData** e **SendInterval**.

O manifesto de implantação não inclui informações sobre a telemetria que o módulo **SimulatedTemperatureSensor** envia ou os comandos aos quais ele responde. Adicione essas definições ao modelo de dispositivo manualmente antes de publicá-lo.

Para saber mais, consulte [tutorial: adicionar um dispositivo de Azure IOT Edge ao aplicativo de IOT central do Azure](tutorial-add-edge-as-leaf-device.md).

### <a name="update-a-deployment-manifest"></a>Atualizar um manifesto de implantação

Se você criar uma nova [versão](howto-version-device-template.md) do modelo de dispositivo, poderá substituir o manifesto de implantação por uma nova versão:

Quando você substitui o manifesto de implantação, todos os dispositivos IoT Edge conectados baixam o novo manifesto e atualizam seus módulos. No entanto, IoT Central não atualiza as interfaces no modelo de dispositivo com quaisquer alterações na configuração do módulo. Por exemplo, se você substituir o manifesto mostrado no trecho anterior pelo manifesto a seguir, você não verá automaticamente a propriedade **SendUnits** na interface de **Gerenciamento** no modelo de dispositivo. Adicione manualmente a nova propriedade à interface de **Gerenciamento** para IOT central para reconhecê-la:

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
          "type": "docker",
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {}
          }
        },
        "systemModules": {
          "edgeAgent": {
            "type": "docker",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0.9",
              "createOptions": "{}"
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0.9",
              "createOptions": "{}"
            }
          }
        },
        "modules": {
          "SimulatedTemperatureSensor": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
              "createOptions": "{}"
            }
          }
        }
      }
    },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {
            "route": "FROM /* INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 7200
        }
      }
    },
    "SimulatedTemperatureSensor": {
      "properties.desired": {
           "SendData": true,
           "SendInterval": 10,
           "SendUnits": "Celsius"
      }
    }
  }
}
```

## <a name="deploy-the-iot-edge-runtime"></a>Implantar o tempo de execução de IoT Edge

Para saber onde você pode executar o IoT Edge Runtime, consulte [Azure IOT Edge sistemas com suporte](../../iot-edge/support.md).

Você também pode instalar o IoT Edge Runtime nos seguintes ambientes:

* [Instalar ou desinstalar o Azure IoT Edge para Linux](../../iot-edge/how-to-install-iot-edge.md)
* [Instalar e provisionar o Azure IoT Edge para Linux em um dispositivo Windows (versão prévia)](../../iot-edge/how-to-install-iot-edge-on-windows.md)
* [Executar Azure IoT Edge em máquinas virtuais Ubuntu no Azure](../../iot-edge/how-to-install-iot-edge-ubuntuvm.md)

## <a name="iot-edge-gateway-devices"></a>Dispositivos IoT Edge gateway

Se você selecionou um dispositivo de IoT Edge para ser um dispositivo de gateway, poderá adicionar relações de downstream a modelos de dispositivo para dispositivos que você deseja conectar ao dispositivo de gateway.

Para saber mais, consulte [como conectar dispositivos por meio de um gateway IOT Edge transparente](how-to-connect-iot-edge-transparent-gateway.md).

## <a name="next-steps"></a>Próximas etapas

Se você for um desenvolvedor de dispositivos, uma próxima etapa sugerida será aprender a [desenvolver seus próprios módulos de IOT Edge](../../iot-edge/module-development.md).
