---
title: Como implantar o módulo OPC Mypara Azure do zero | Microsoft Docs
description: Este artigo descreve como implantar o OPC "s" no zero usando a folha de IoT Edge do portal do Azure e também usando AZ CLI.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
ms.custom: devx-track-azurecli
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 38235f9b01b321e27664ee837763732971f0b85c
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105645996"
---
# <a name="deploy-opc-twin-module-and-dependencies-from-scratch"></a>Implantar o módulo OPC e as dependências do zero

> [!IMPORTANT]
> Enquanto atualizamos este artigo, confira [IoT Industrial do Azure](https://azure.github.io/Industrial-IoT/) para obter o conteúdo mais atualizado.

O módulo OPC myé executado em IoT Edge e fornece vários serviços de borda para o dispositivo OPC e os serviços de registro. 

Há várias opções para implantar módulos em seu gateway de [Azure IOT Edge](https://azure.microsoft.com/services/iot-edge/) , entre eles

- [Implantando da folha IoT Edge do portal do Azure](../iot-edge/how-to-deploy-modules-portal.md)
- [Implantando usando AZ CLI](../iot-edge/how-to-deploy-cli-at-scale.md)

> [!NOTE]
> Para obter mais informações sobre detalhes de implantação e instruções, consulte o [repositório](https://github.com/Azure/azure-iiot-components)github.

## <a name="deployment-manifest"></a>Manifesto de implantação

Todos os módulos são implantados usando um manifesto de implantação.  Um exemplo de manifesto para implantar o [Editor OPC](https://github.com/Azure/iot-edge-opc-publisher) e o [OPC](https://github.com/Azure/azure-iiot-opc-twin-module) myQuery é mostrado abaixo.

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
                "createOptions": "{\"Hostname\":\"publisher\",\"Cmd\":[\"publisher\",\"--pf=./pn.json\",\"--di=60\",\"--tm\",\"--aa\",\"--si=0\",\"--ms=0\"],\"ExposedPorts\":{\"62222/tcp\":{}},\"NetworkingConfig\":{\"EndpointsConfig\":{\"host\":{}}},\"HostConfig\":{\"NetworkMode\":\"host\",\"PortBindings\":{\"62222/tcp\":[{\"HostPort\":\"62222\"}]}}}"
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

## <a name="deploying-from-azure-portal"></a>Implantando do portal do Azure

A maneira mais fácil de implantar os módulos em um dispositivo Azure IoT Edge gateway é por meio do portal do Azure.  

### <a name="prerequisites"></a>Pré-requisitos

1. Implante as [dependências](howto-opc-twin-deploy-dependencies.md) do OPC entrelaçado e obteve o `.env` arquivo resultante. Observe a implantação `hub name` da `PCS_IOTHUBREACT_HUB_NAME` variável no `.env` arquivo resultante.

2. Registre e inicie um gateway de IoT Edge do [Linux](../iot-edge/how-to-install-iot-edge.md) ou [Windows](../iot-edge/how-to-install-iot-edge.md) e anote seu `device id` .

### <a name="deploy-to-an-edge-device"></a>Implantar em um dispositivo de borda

1. Entre no [Portal do Azure](https://portal.azure.com/) e navegue até o Hub IoT.

2. Selecione **IOT Edge** no menu à esquerda.

3. Clique no ID do dispositivo alvo da lista de dispositivos.

4. Selecione **definir módulos**.

5. Na seção **módulos de implantação** da página, selecione **Adicionar** e **IOT Edge módulo.**

6. Na caixa de diálogo **IOT Edge módulo personalizado** `opctwin` , use como nome para o módulo e, em seguida, especifique o *URI da imagem* de contêiner como

   ```bash
   mcr.microsoft.com/iotedge/opc-twin:latest
   ```

   Como *Opções de criação de contêiner*, use o seguinte JSON:

   ```json
   {"NetworkingConfig": {"EndpointsConfig": {"host": {}}}, "HostConfig": {"NetworkMode": "host" }}
   ```

   Preencha os campos opcionais se necessário. Para mais informações sobre opções de criação de contêiner, políticas de reinício, e status desejados consulte [propriedades desejadas do EdgeAgent](../iot-edge/module-edgeagent-edgehub.md#edgeagent-desired-properties). Para mais informações sobre o módulo gêmeo consulte [Defina ou atualize propriedades desejadas](../iot-edge/module-composition.md#define-or-update-desired-properties).

7. Selecione **salvar** e repita a etapa **5**.  

8. Na caixa de diálogo IoT Edge módulo personalizado, use `opcpublisher` como nome para o módulo e o *URI da imagem* de contêiner como 

   ```bash
   mcr.microsoft.com/iotedge/opc-publisher:latest
   ```

   Como *Opções de criação de contêiner*, use o seguinte JSON:

   ```json
   {"Hostname":"publisher","Cmd":["publisher","--pf=./pn.json","--di=60","--tm","--aa","--si=0","--ms=0"],"ExposedPorts":{"62222/tcp":{}},"HostConfig":{"PortBindings":{"62222/tcp":[{"HostPort":"62222"}] }}}
   ```

9. Selecione **salvar** e **Avançar** para continuar na seção rotas.

10. Na guia rotas, Cole o seguinte 

    ```json
    {
      "routes": {
        "opctwinToIoTHub": "FROM /messages/modules/opctwin/* INTO $upstream",
        "opcpublisherToIoTHub": "FROM /messages/modules/opcpublisher/* INTO $upstream"
      }
    }
    ```

    e selecione **Avançar**

11. Examine as informações de implantação e o manifesto.  Ele deve ser semelhante ao manifesto de implantação acima.  Selecione **Enviar**.

12. Após implantar os módulos no dispositivo, será possível exibir todos eles na seção **Dispositivos do Hub IoT**. Esta página mostra o nome de cada módulo implantado, como também informações úteis como o status de implantação e o código de saída.

## <a name="deploying-using-azure-cli"></a>Implantando usando o CLI do Azure

### <a name="prerequisites"></a>Pré-requisitos

1. Instale a versão mais recente da [interface de linha de comando do Azure (AZ)](/cli/azure/) [aqui](/cli/azure/install-azure-cli).

### <a name="quickstart"></a>Guia de Início Rápido

1. Salve o manifesto de implantação acima em um `deployment.json` arquivo.  

2. Use o comando a seguir para aplicar a configuração a um dispositivo do IoT Edge:

   ```azurecli
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content ./deployment.json
   ```

   O `device id` parâmetro diferencia maiúsculas de minúsculas. O parâmetro de conteúdo aponta para o arquivo de manifesto de implantação que você salvou. 
    ![AZ IoT Edge conjunto-saída de módulos](/azure/iot-edge/media/how-to-deploy-cli/set-modules.png)

3. Após implantar os módulos no dispositivo, será possível exibir todos eles com o comando a seguir:

   ```azurecli
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

   O parâmetro da ID do dispositivo diferencia maiúsculas de minúsculas. ![saída da lista de identidade do módulo hub iot az](/azure/iot-edge/media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a implantar o OPC mydo zero, aqui está a próxima etapa sugerida:

> [!div class="nextstepaction"]
> [Implantar o OPC "an" em um projeto existente](howto-opc-twin-deploy-existing.md)