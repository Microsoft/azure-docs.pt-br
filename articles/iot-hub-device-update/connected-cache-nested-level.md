---
title: Gateway do Azure IoT Edge aninhado de dois níveis do Cache Conectado da Microsoft com proxy não autenticado de saída | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Tutorial do Gateway do Azure IoT Edge aninhado de dois níveis do Cache Conectado da Microsoft com proxy não autenticado de saída
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 7facb74cd407c576b2a7b119f19427dcd185f04e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105568810"
---
# <a name="microsoft-connected-cache-preview-deployment-scenario-sample-two-level-nested-azure-iot-edge-gateway-with-outbound-unauthenticated-proxy"></a>Exemplo de cenário de implantação de visualização do Cache Conectado da Microsoft: gateway do Azure IoT Edge aninhado de dois níveis com proxy não autenticado de saída

Considerando o diagrama abaixo, neste cenário há um gateway do Azure IoT Edge e um dispositivo downstream do Azure IoT Edge, um gateway pai do Azure IoT Edge para outro gateway do Azure IoT Edge e um servidor proxy na DMZ de TI. Veja abaixo um exemplo das variáveis de ambiente do Cache Conectado da Microsoft que seriam definidas no portal do Azure UX para ambos os módulos MCC implantados nos gateways do Azure IoT Edge. O exemplo fornecido demonstra a configuração de dois níveis de gateways do Azure IoT Edge, mas não há limite para a profundidade de hosts upstream para os quais o Cache Conectado da Microsoft dará suporte. Não há nenhuma diferença nas opções de criação de contêiner do MCC dos exemplos anteriores.

Confira a documentação [Conectar dispositivos IoT Edge downstream – Azure IoT Edge](../iot-edge/how-to-connect-downstream-iot-edge-device.md?preserve-view=true&tabs=azure-portal&view=iotedge-2020-11) para obter mais detalhes sobre como configurar implantações em camadas de gateways do Azure IoT Edge. Além disso, observe que, ao implantar o Azure IoT Edge – o Cache Conectado da Microsoft – e os módulos personalizados, todos os módulos devem residir no mesmo registro de contêiner.

O diagrama a seguir descreve o cenário em que um gateway do Azure IoT Edge de acesso direto aos recursos da CDN está atuando como o pai de outro gateway do Azure IoT Edge que, por sua vez, está atuando como o pai de um dispositivo de folha do Azure IoT, como um Raspberry Pi. Somente o gateway do Azure IoT Edge pai tem conectividade com a Internet para os recursos da CDN, e o Azure IoT Edge filho e o dispositivo IoT do Azure estão isolados da Internet. 

  :::image type="content" source="media/connected-cache-overview/nested-level-proxy.png" alt-text="Cache Conectado da Microsoft aninhado" lightbox="media/connected-cache-overview/nested-level-proxy.png":::

## <a name="parent-gateway-configuration"></a>Configuração do gateway pai

1. Adicione o módulo de Cache Conectado da Microsoft à sua implantação de dispositivo de gateway do Azure IoT Edge no Hub IoT do Azure.
2. Adicione as variáveis de ambiente para a implantação. Veja abaixo um exemplo das variáveis de ambiente.

    **Variáveis de ambiente**

    | Name                 | Valor                                       |
    | ----------------------------- | --------------------------------------------| 
    | CACHE_NODE_ID                 | Confira a descrição da variável de ambiente acima. |
    | CUSTOMER_ID                   | Confira a descrição da variável de ambiente acima. |
    | CUSTOMER_KEY                  | Confira a descrição da variável de ambiente acima. |
    | STORAGE_ *N* _SIZE_GB           | N = 5                                       |
    | CACHEABLE_CUSTOM_1_HOST       | Packagerepo.com:80                          |
    | CACHEABLE_CUSTOM_1_CANONICAL  | Packagerepo.com                             |
    | IS_SUMMARY_ACCESS_UNRESTRICTED| true                                        |
    | UPSTREAM_PROXY                | IP do servidor proxy ou FQDN                     |

3. Adicione as opções de criação de contêiner para a implantação. Não há nenhuma diferença nas opções de criação de contêiner do MCC do exemplo anterior. Veja abaixo um exemplo das opções de criação de contêiner.

### <a name="container-create-options"></a>Opções de criação de contêiner

```markdown
{
    "HostConfig": {
        "Binds": [
            "/MicrosoftConnectedCache1/:/nginx/cache1/"
        ],
        "PortBindings": {
            "8081/tcp": [
                {
                    "HostPort": "80"
                }
            ],
            "5000/tcp": [
                {
                    "HostPort": "5100"
                }
            ]
        }
    }
```

## <a name="child-gateway-configuration"></a>Configuração do gateway filho

>[!Note]
>Caso tenha replicado contêineres usados na configuração em seu próprio registro privado, você precisará modificar as configurações de config.toml e as configurações de tempo de execução na implantação do seu módulo. Para obter mais informações detalhadas, confira o [Tutorial – Criar uma hierarquia de dispositivos IoT Edge — Azure IoT Edge](../iot-edge/tutorial-nested-iot-edge.md?preserve-view=true&tabs=azure-portal&view=iotedge-2020-11#deploy-modules-to-the-lower-layer-device).

1. Modifique o caminho da imagem para o agente do Edge, conforme demonstrado no exemplo abaixo:

```markdown
[agent]
name = "edgeAgent"
type = "docker"
env = {}
[agent.config]
image = "<parent_device_fqdn_or_ip>:8000/iotedge/azureiotedge-agent:1.2.0-rc2"
auth = {}
```
2. Modifique as configurações de execução do Hub do Edge e do agente do Edge na implantação do Azure IoT Edge, conforme demonstrado neste exemplo:
    
    * Em Hub do Edge, no campo de imagem, insira ```$upstream:8000/iotedge/azureiotedge-hub:1.2.0-rc2```
    * Em Agente do Edge, no campo de imagem, insira ```$upstream:8000/iotedge/azureiotedge-agent:1.2.0-rc2```

3. Adicione o módulo de Cache Conectado da Microsoft à sua implantação de dispositivo de gateway do Azure IoT Edge no Hub IoT do Azure.

   * Escolha um nome para seu módulo: ```ConnectedCache```
   * Modifique o URI da imagem: ```$upstream:8000/mcc/linux/iot/mcc-ubuntu-iot-amd64:latest```

4. Adicione as mesmas opções de variáveis de ambiente e de criação de contêiner usadas na implantação pai.

Para a validar se o Cache Conectado da Microsoft está funcionando corretamente, execute o comando a seguir no terminal do dispositivo IoT Edge que hospeda o módulo ou qualquer dispositivo na rede.

```bash
    wget "http://<CHILD Azure IoT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```