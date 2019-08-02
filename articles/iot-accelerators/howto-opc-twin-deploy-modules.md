---
title: Como implantar o módulo gêmeo de OPC para Azure do zero | Microsoft Docs
description: Como implantar o gêmeo do OPC do zero.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 798f087c260b6b0a1efc366b864fe2bb7bce732e
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603697"
---
# <a name="deploy-opc-twin-module-and-dependencies-from-scratch"></a>Implantar o módulo gêmeo de OPC e as dependências do zero

O módulo gêmeo de OPC é executado no IoT Edge e fornece vários serviços de borda para o dispositivo gêmeo OPC e os serviços de registro. 

Há várias opções para implantar módulos para seus [do Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) Gateway, entre eles

- [Implantando de folha de IoT Edge do portal do Azure](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-portal)
- [Implantar usando a CLI AZ](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor-cli)

> [!NOTE]
> Para obter mais informações sobre detalhes de implantação e instruções, consulte o GitHub [repositório](https://github.com/Azure/azure-iiot-components).

## <a name="deployment-manifest"></a>Manifesto de implantação

Todos os módulos são implantados usando um manifesto de implantação.  Um manifesto de exemplo para implantar ambas [publicador OPC](https://github.com/Azure/iot-edge-opc-publisher) e [OPC gêmeo](https://github.com/Azure/azure-iiot-opc-twin-module) é mostrado abaixo.

```json
{
  "content": {
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
                "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                "createOptions": ""
              }
            },
            "edgeHub": {
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}], \"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
              }
            }
          },
          "modules": {
            "opctwin": {
              "version": "1.0",
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/iotedge/opc-twin:latest",
                "createOptions": "{\"NetworkingConfig\": {\"EndpointsConfig\": {\"host\": {}}}, \"HostConfig\": {\"NetworkMode\": \"host\" }}"
              }
            },
            "opcpublisher": {
              "version": "2.0",
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/iotedge/opc-publisher:latest",
                "createOptions": "{\"Hostname\":\"publisher\",\"Cmd\":[\"publisher\",\"--pf=./pn.json\",\"--di=60\",\"--to\",\"--aa\",\"--si=0\",\"--ms=0\"],\"ExposedPorts\":{\"62222/tcp\":{}},\"NetworkingConfig\":{\"EndpointsConfig\":{\"host\":{}}},\"HostConfig\":{\"NetworkMode\":\"host\",\"PortBindings\":{\"62222/tcp\":[{\"HostPort\":\"62222\"}]}}}"
              }
            }
          }
        }
      },
      "$edgeHub": {
        "properties.desired": {
          "schemaVersion": "1.0",
          "routes": {
            "opctwinToIoTHub": "FROM /messages/modules/opctwin/* INTO $upstream",
            "opcpublisherToIoTHub": "FROM /messages/modules/opcpublisher/* INTO $upstream"
          },
          "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
          }
        }
      }
    }
  }
}
```

## <a name="deploying-from-azure-portal"></a>Implantação do portal do Azure

É a maneira mais fácil de implantar os módulos em um dispositivo de gateway do Azure IoT Edge por meio do portal do Azure.  

### <a name="prerequisites"></a>Pré-requisitos

1. Implantar o gêmeo de OPC [dependências](howto-opc-twin-deploy-dependencies.md) e obtido resultante `.env` arquivo. Observe o implantado `hub name` do `PCS_IOTHUBREACT_HUB_NAME` variável resultante `.env` arquivo.

2. Registre e inicie uma [Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) ou [Windows](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-windows) gateway do IoT Edge e anote seu `device id`.

### <a name="deploy-to-an-edge-device"></a>Implantar em um dispositivo de borda

1. Entre no [Portal do Azure](https://portal.azure.com/) e navegue até o Hub IoT.

2. Selecione **do IoT Edge** no menu à esquerda.

3. Clique no ID do dispositivo alvo da lista de dispositivos.

4. Selecione **Definir Módulos**.

5. No **módulos de implantação** seção da página, selecione **Add** e **módulo do IoT Edge.**

6. No **módulo personalizado IoT Edge** uso da caixa de diálogo `opctwin` como nome do módulo, em seguida, especifique o contêiner *URI da imagem* como

   ```bash
   mcr.microsoft.com/iotedge/opc-twin:latest
   ```

   Como *opções de criação de* usar o JSON a seguir:

   ```json
   {"NetworkingConfig": {"EndpointsConfig": {"host": {}}}, "HostConfig": {"NetworkMode": "host" }}
   ```

   Preencha os campos opcionais se necessário. Para mais informações sobre opções de criação de contêiner, políticas de reinício, e status desejados consulte [propriedades desejadas do EdgeAgent](https://docs.microsoft.com/azure/iot-edge/module-edgeagent-edgehub#edgeagent-desired-properties). Para mais informações sobre o módulo gêmeo consulte [Defina ou atualize propriedades desejadas](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties).

7. Selecione **salve** e repita a etapa **5**.  

8. Na caixa de diálogo de módulo personalizado IoT Edge, use `opcpublisher` como nome do módulo e o contêiner *URI da imagem* como 

   ```bash
   mcr.microsoft.com/iotedge/opc-publisher:latest
   ```

   Como *opções de criação de* usar o JSON a seguir:

   ```json
   {"Hostname":"publisher","Cmd":["publisher","--pf=./pn.json","--di=60","--to","--aa","--si=0","--ms=0"],"ExposedPorts":{"62222/tcp":{}},"HostConfig":{"PortBindings":{"62222/tcp":[{"HostPort":"62222"}] }}}
   ```

9. Selecione **salve** e, em seguida **próxima** para continuar para a seção de rotas.

10. Na guia de rotas, cole o seguinte 

    ```json
    {
      "routes": {
        "opctwinToIoTHub": "FROM /messages/modules/opctwin/outputs/* INTO $upstream",
        "opcpublisherToIoTHub": "FROM /messages/modules/opcpublisher/outputs/* INTO $upstream"
      }
    }
    ```

    e selecione **Avançar**

11. Examine as informações de implantação e de manifesto.  Deve se parecer com o manifesto de implantação acima.  Selecione **Enviar**.

12. Após implantar os módulos no dispositivo, será possível exibir todos eles na seção **Dispositivos do Hub IoT**. Esta página mostra o nome de cada módulo implantado, como também informações úteis como o status de implantação e o código de saída.

## <a name="deploying-using-azure-cli"></a>Implantar usando a CLI do Azure

### <a name="prerequisites"></a>Pré-requisitos

1. Instale a versão mais recente do [interface de linha de comando do Azure (AZ)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) partir [aqui](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

### <a name="quickstart"></a>Início rápido

1. Salve o manifesto de implantação acima em um `deployment.json` arquivo.  

2. Use o comando a seguir para aplicar a configuração a um dispositivo do IoT Edge:

   ```bash
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content ./deployment.json
   ```

   O `device id` parâmetro diferencia maiusculas de minúsculas. O parâmetro de conteúdo aponta para o arquivo de manifesto de implantação que você salvou. 
    ![az IoT Edge set-modules output](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/set-modules.png)

3. Após implantar os módulos no dispositivo, será possível exibir todos eles com o comando a seguir:

   ```bash
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

   O parâmetro de ID de dispositivo diferencia maiusculas de minúsculas. ![saída da lista de identidade do módulo hub iot az](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como implantar o gêmeo do OPC do zero, aqui está a próxima etapa sugerida:

> [!div class="nextstepaction"]
> [Implantar o gêmeo de OPC para um projeto existente](howto-opc-twin-deploy-existing.md)
