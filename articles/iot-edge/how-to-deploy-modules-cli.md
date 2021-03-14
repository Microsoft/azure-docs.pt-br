---
title: Implantar módulos da linha de comando da CLI do Azure – Azure IoT Edge
description: Use a CLI do Azure com a Extensão de IoT do Azure para efetuar push de um módulo do IoT Edge de seu Hub IoT para o dispositivo do IoT Edge, conforme configurado por um manifesto de implantação.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/13/2020
ms.topic: conceptual
ms.service: iot-edge
ms.custom: devx-track-azurecli
services: iot-edge
ms.openlocfilehash: cc4308cf69ecb99fccb09a6668825397675983cd
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103201140"
---
# <a name="deploy-azure-iot-edge-modules-with-azure-cli"></a>Implantar módulos do Azure IoT Edge com CLI do Azure

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Após criar os módulos do IoT Edge com a lógica de negócios, você pretende implantá-los nos dispositivos para operar na borda. Se houver vários módulos trabalhando em conjunto para coletar e processar dados, será possível implantá-los todos de uma vez e declarar as regras de roteamento que conecta-os.

A [CLI do Azure](/cli/azure) é uma ferramenta de linha de comando de plataforma cruzada de software livre para gerenciar recursos do Azure como o IoT Edge. Isso permite que você gerencie instantaneamente recursos, instâncias de serviço de provisionamento de dispositivos e hubs vinculados do Hub IoT. A nova extensão de IoT enriquece a CLI do Azure com recursos como gerenciamento de dispositivos e funcionalidade completa do IoT Edge.

Este artigo mostra como criar um manifesto de implantação JSON e usar esse arquivo para enviar a implantação a um dispositivo do IoT Edge. Para obter informações sobre como criar uma implantação voltada a vários dispositivos com base nas marcações compartilhadas, consulte [Implantar e monitorar módulos do IoT Edge em escala](how-to-deploy-cli-at-scale.md)

## <a name="prerequisites"></a>Pré-requisitos

* Um [Hub IoT](../iot-hub/iot-hub-create-using-cli.md) na assinatura do Azure.
* Um dispositivo IoT Edge

  Se você não tiver um dispositivo IoT Edge configurado, poderá criar um em uma máquina virtual do Azure. Siga as etapas em um dos artigos de início rápido para [criar um dispositivo Linux Virtual](quickstart-linux.md) ou [criar um dispositivo virtual do Windows](quickstart.md).

* [CLI do Azure](/cli/azure/install-azure-cli) em seu ambiente. No mínimo, a versão da CLI do Azure deve ser 2.0.70 ou superior. Use `az --version` para validar. Esta versão dá suporte aos comandos da extensão az e introduz a estrutura de comandos Knack.
* A [extensão de IoT para a CLI do Azure](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Configurar um manifesto de implantação

Um manifesto de implantação é um documento JSON que descreve quais módulos implantar, como os dados fluem entre os módulos e as propriedades desejadas dos módulos gêmeos. Para obter mais informações sobre como os manifestos de implantação funcionam e como criá-los, consulte [Entender como os módulos do IoT Edge podem ser utilizados, configurados e reutilizados](module-composition.md).

Para implantar módulos usando a CLI do Azure, salve o manifesto de implantação localmente como um arquivo .JSON. Você usará o caminho do arquivo na próxima seção quando executar o comando para aplicar a configuração ao dispositivo.

A seguir, é apresentado um manifesto básico de implantação com um módulo como um exemplo:

>[!NOTE]
>Este manifesto de implantação de exemplo usa a versão de esquema 1,1 para o agente de IoT Edge e o Hub. A versão de esquema 1,1 foi lançada junto com IoT Edge versão 1.0.10 e habilita recursos como ordem de inicialização de módulo e priorização de rota.

```json
{
  "content": {
    "modulesContent": {
      "$edgeAgent": {
        "properties.desired": {
          "schemaVersion": "1.1",
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
                "image": "mcr.microsoft.com/azureiotedge-agent:1.1",
                "createOptions": "{}"
              }
            },
            "edgeHub": {
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-hub:1.1",
                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
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
          "schemaVersion": "1.1",
          "routes": {
            "upstream": "FROM /messages/* INTO $upstream"
          },
          "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
          }
        }
      },
      "SimulatedTemperatureSensor": {
        "properties.desired": {
          "SendData": true,
          "SendInterval": 5
        }
      }
    }
  }
}
```

## <a name="deploy-to-your-device"></a>Implantar no seu dispositivo

Você implanta módulos no dispositivo, aplicando o manifesto de implantação configurado com as informações do módulo.

Altere os diretórios para a pasta onde o manifesto de implantação é salvo. Se você tiver usado um dos modelos de IoT Edge do VS Code, use o `deployment.json` arquivo o **config** pasta do seu diretório de solução, e não o arquivo `deployment.template.json`.

Use o comando a seguir para aplicar a configuração a um dispositivo do IoT Edge:

   ```azurecli
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content [file path]
   ```

O parâmetro da ID do dispositivo diferencia maiúsculas de minúsculas. O parâmetro de conteúdo aponta para o arquivo de manifesto de implantação que você salvou.

   ![saída az iot edge set-modules](./media/how-to-deploy-cli/set-modules.png)

## <a name="view-modules-on-your-device"></a>Exibir módulos no dispositivo

Após implantar os módulos no dispositivo, será possível exibir todos eles com o comando a seguir:

Exiba os módulos no seu dispositivo IoT Edge:

   ```azurecli
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

O parâmetro da ID do dispositivo diferencia maiúsculas de minúsculas.

   ![saída da lista de identidade do módulo hub iot az](./media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>Próximas etapas

Saiba como [Implantar e monitorar módulos do IoT Edge em escala](how-to-deploy-at-scale.md)